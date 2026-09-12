---
title: "Gitea 安全事件排查与处置报告"
date: 2026-09-12 12:00:00
categories: 建站心得
tags: [安全, Gitea, 事件响应, Docker]
---

# Gitea 安全事件排查与处置报告

**主机**:某云厂商轻量实例(2C/1.9G,Ubuntu 22.04,384 天未重启)
**服务**:自建 Gitea(域名已脱敏)→ nginx(443)→ 127.0.0.1:3000 → docker 容器 `gitea-server-1`
**排查日期**:2026-09-12
**定性**:已确认的真实入侵(容器内 RCE),宿主机未发现失陷证据

---

## 一、事件概述

gitea 服务长期无法访问(HTTP 超时),表面原因是系统负载高达 1924(正常 <2),由约 1911 个自 7 月 31 日起卡死在 R 状态的 `sh` 僵死进程造成。深挖后确认这是一次真实入侵的附带损伤:

**2026-07-31 23:18(北京时间),攻击者利用 Gitea CVE-2026-60004(diffpatch API 路径穿越可植入 git hook)在容器内获得任意命令执行,下载并运行提权工具 RootHawk 尝试容器逃逸(未成功),运行过程 fork 出的 1911 个 shell 全部挂死,拖垮整机 6 周。**

## 二、攻击链还原(证据齐全)

1. **暴露面**:gitea 1.24.5(2025-08 版本),开放注册、无验证码,API 匿名可探测。
2. **漏洞**:CVE-2026-60004 —— Gitea < 1.27.1 的 `POST /api/v1/repos/{owner}/{repo}/diffpatch` 接口可通过补丁路径穿越向裸仓库的 `hooks/` 目录写入可执行文件,后续 git 操作触发 hook 即执行任意命令。
3. **利用过程**(容器日志留痕,攻击者 IP `64.176.51.87`,DigitalOcean):
   - `POST /user/sign_up` 批量注册账号(2026-07-31 起,累计 772 个 `poc*`/`testpoc*`/随机名账号,邮箱均为 `@test.local`/`@poc.local` 假邮箱);
   - 注册 `gitea_exp` 账号(id 9),名下创建 847 个仓库(多为例行测试的空仓库);
   - 对仓库 `gitea_exp/up76cf845b` 调用 diffpatch 接口植入恶意 hook,提交信息 "apply";
   - 2026-07-31 23:18–23:21,`/tmp/RootHawk-v2.0.0-amd64` 落地容器并执行。
4. **恶意 hook 内容**(已在仓库提交记录中提取):执行 `echo "sh /tmp/deploy.sh" | /tmp/RootHawk-v2.0.0-amd64 -any`,并将命令输出经 `git hash-object` 写回仓库 `outec5e1b24` 分支 —— **以 git 仓库本身作为命令执行结果的回传通道**。
5. **提权/逃逸尝试**:RootHawk(Go 编写的本地提权验证工具包)以 git 用户(uid 1000)运行 `-run-internal CVE-2026-31431`(Linux 内核 crypto 子系统 algif_aead 本地提权洞,2026-04 披露),另内置 PwnKit(CVE-2021-4034)、CVE-2021-3560、Dirty Pipe(CVE-2022-0847)及多个 2026 年内核 LPE 模块。容器内无 pkexec、Docker 默认 seccomp 拦截,逃逸未成功。
6. **附带损伤**:RootHawk 运行时 fork 的 1911 个 shell 挂死 → 负载 1924 → gitea 与整机瘫痪 42 天,期间服务不可用。
7. **后续**:7/31–9/12 该扫描平台每天继续自动化注册账号、建仓库、创建 access token(共 9 个),持续将本实例作为漏洞测试靶场。
8. **二次植入与持久化(9 月 10 日)**:攻击者(IP `20.212.233.41`,Azure)再次利用 RCE,向 git 全局配置(`data/home/.gitconfig`)注入 `[uploadpack] packObjectsHook = sh <脚本>`,使**每次 git clone/fetch 都执行攻击者脚本**(探针脚本:touch 标记文件 + `id > 输出`,用于验证 RCE 存活)。该持久化导致 9/10 之后所有 git 克隆必然失败(脚本不产出 pack 流,客户端报 `early EOF / fetch-pack: invalid index-pack output`),此问题最初被误判为 gitea 版本 bug,后定位为攻击持久化,已于 9/12 清除(脚本已隔离至 `/root/quarantine-attacker-scripts-20260912/`)。

## 三、影响评估

| 范围 | 结论 | 依据 |
|---|---|---|
| gitea 容器 | **已失陷**(RCE 达成) | 恶意 hook、RootHawk 二进制、命令回传分支均留痕 |
| 宿主机 | 未发现失陷证据 | 7/30–8/3 无任何 SSH 成功登录;无 crontab/ld.so.preload/可疑账号/未知 authorized_keys 等持久化;wtmp 无 7/31 登录记录 |
| 数据 | 攻击者仓库均为自建,未发现访问他人私有仓库痕迹 | 容器 HTTP 日志仅见 `gitea_exp/*` 路径;如需彻底确认可继续审计 |
| 横向风险 | 宿主机 22 端口 7/31 02:13–02:21 被 `49.233.165.15`(腾讯云段)探测 481 次,全部失败 | journalctl 留痕,之后该 IP 未再出现 |

### 容器化隔离收益评估

**生效的隔离层(本次事件有实证)**:

| 隔离机制 | 事件中的实际表现 |
|---|---|
| 容器边界 | 两次 RCE(7/31、9/10)全部止步于容器内;宿主机无失陷证据(无成功 SSH 登录、无宿主持久化、无宿主文件落地) |
| 非 root 运行 | gitea rootless 镜像以 git 用户(uid 1000)运行,攻击载荷全程无容器内 root 权限 |
| Docker 默认 seccomp | RootHawk 的全部提权/逃逸模块(PwnKit、Dirty Pipe、CVE-2026-31431 内核利用等)均未成功;容器镜像内亦无 pkexec 可供利用 |
| 窄挂载 | 仅数据/配置目录挂载(时区文件只读),未挂载 docker.sock,宿主文件系统其余部分对攻击者不可见 |

**暴露的缺口与闭环**:

- 唯一穿透隔离的损害路径是**资源耗尽**:RootHawk 运行时 fork 出的 1911 个僵死进程将宿主机(2 核/1.9G)负载拖至 1924,服务瘫痪 6 周——容器边界当时未设资源上限,拦不住此类破坏;
- 已闭环:容器已增加 `mem_limit: 1g`、`pids_limit: 200`、`cpus: 1.5`,同类 fork 风暴将在容器内被进程数上限截断,不再波及宿主机。该案例说明容器隔离的收益需要配套资源限制才完整。

---

## 四、IOC(入侵指标)

- **攻击 IP**:`64.176.51.87`(主要利用源,DigitalOcean)、`20.212.233.41`(9/10 二次植入持久化,Azure);爆破源见第五节
- **载荷 SHA256**:`f8f912c8ca4a048fe2a9ee122a25ccb90dabca11cb4c7eb4ca35db23a949cf55`(`/tmp/RootHawk-v2.0.0-amd64`,2.8MB Go 二进制)
- **恶意账号**:`gitea_exp`(id 9)及 870 个 2026-07-31 之后注册的自动化账号(已全部删除)
- **可疑探测**:`49.233.165.15`(7/31 凌晨 SSH key 探测)
- **证据留存**:全量数据备份 `/home/<user>/gitea-backup-20260912.tar.gz`(70MB,含处置前所有数据);宿主机日志 `/var/log/auth.log*`、journald;容器日志(docker logs,已随重建重置,关键证据已摘录至本报告)

## 五、2222 端口爆破情况

- 近 7 天 **约 3.6 万次** 失败 SSH 认证(近 24 小时 4238 次),全部为公钥认证探测,无一成功:
  - `5.231.242.53`:9206 次(已封禁)
  - `172.105.83.82`(Linode):8428 次
  - `5.231.242.185` / `5.231.242.181`(同网段):4783 / 4767 次
  - `161.35.169.21`(DigitalOcean):4428 次;`43.110.38.5`:2048 次;`43.165.170.19`:1324 次;长尾若干
- 宿主机 22 端口同期约 3.6 万次失败(当前 auth.log 周期内)。
- gitea 内置 SSH 仅公钥认证,爆破无法奏效,风险为噪音与日志骚扰,但建议保持封禁。

## 六、已执行的处置(2026-09-12)

1. **恢复服务**:重启容器清除 1911 个僵死进程,负载由 1924 恢复正常,web 验证 HTTP 200。
2. **全量备份**:`/home/<user>/gitea-backup-20260912.tar.gz`(处置前数据,可整体回滚)。
3. **升级修复**:gitea 1.24.5 → **1.27.3**(2026-08-29 发布,包含 CVE-2026-60004 修复),DB 迁移 321→333+ 全部成功,容器随镜像重建(容器内 RootHawk 载荷随之消失)。
4. **关闭注册**:app.ini `[service] DISABLE_REGISTRATION = true`,注册页与 API 注册接口均已验证关闭。
5. **清理失陷痕迹**:删除 871 个攻击者账号(含 gitea_exp,API `purge=true` 级联删除其 1327 个仓库及 9 个 token),0 失败;核实仅存 5 个正常账号(3 个用户 + 2 个组织,用户名已脱敏)、2 个正常仓库、7 个正常用户 key;全库扫描 575→现存仓库 hooks 目录,无其他恶意 hook 植入;临时管理员 token 已全部吊销(现存 0)。
6. **加固**:
   - 安装并启用 fail2ban:sshd jail(22 端口,5 次/10 分钟封 1 小时)+ 自定义 gitea-ssh jail(读容器 json 日志,经 DOCKER-USER 链封禁 2222,24 小时);**最大爆破源 5.231.242.53 已被自动封禁**;
   - 容器日志加轮转限制(json-file max-size 50m × 3),防止日志撑爆磁盘。
7. **清除持久化(同日追加)**:删除 git 全局配置中被注入的 `packObjectsHook`(保留 gitea 正常配置项),攻击探针脚本移出至 `/root/quarantine-attacker-scripts-20260912/` 留证;全卷扫描(配置、hooks、脚本、app.ini)确认无其他残留;修复后 git-over-HTTP 克隆验证恢复正常(两仓库分别 39M/113M 完整克隆成功),gitea 稳定运行于 1.27.3。
8. **补齐容器资源隔离(同日追加)**:容器增加 `mem_limit: 1g`、`pids_limit: 200`、`cpus: 1.5`,封堵本次唯一穿透隔离边界的资源耗尽路径(详见"容器化隔离收益评估")。

## 七、遗留建议

1. **云厂商控制台 → 服务器防火墙(安全组)**:仅放行必要端口;22/2222 如无公网运维需求可改成源 IP 白名单,一劳永逸(比 fail2ban 更可靠,不受重启影响)。
2. 备份文件建议转存异地(如 COS),并定期演练恢复;本机 50G 盘仅剩 26G。
3. 机器已 384 天未重启、内核未打补丁(CVE-2026-31431 等 2026 年内核 LPE 均未修复),建议安排停机窗口升级内核并重启——当前 docker 隔离是仅有的缓解层。
4. 可选深审:导出全部容器 HTTP 日志,核对 `64.176.51.87` 是否访问过 `gitea_exp` 之外的私有仓库。
5. 恢复对外使用前,建议 3 位正常用户更新密码并检查个人 SSH key 是否有泄露可能(此次攻击不涉及密码爆破,公钥体系未被攻破)。

## 八、参考资料

- [NVD — CVE-2026-60004:Gitea diffpatch API RCE(1.27.1 修复)](https://nvd.nist.gov/vuln/detail/cve-2026-60004)
- [runZero — Gitea diffpatch RCE 分析与资产排查](https://www.runzero.com/blog/gitea/)
- [Tenable — CVE-2026-31431(Linux kernel algif_aead 本地提权)](https://www.tenable.com/cve/CVE-2026-31431)
- [Wind River — CVE-2026-31431 / 43284 / 43500 安全响应](https://www.windriver.com/security/vulnerability-responses/CVE-2026-31431)
- [Gitea 官方发布(1.27.3,2026-08-29)](https://github.com/go-gitea/gitea/releases)