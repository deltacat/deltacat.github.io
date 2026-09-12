# AGENTS.md — 博客仓库代理规则

本仓库是用户个人博客「夜猫阿罗哈」的 Hexo 源码仓，线上地址 https://www.catxn.net（GitHub Pages：`main` 分支存源码，构建产物由 hexo deploy 推送至 `gh-pages` 分支发布）。

技术栈：Hexo 8.1.2 + butterfly 主题 5.7.0（npm 包，非 themes/ 目录）+ Node 24（版本锁定见 `.nvmrc`）+ yarn。AI Agent 参与维护时遵循本文件。

## 铁律（优先级最高，违反即停）

1. **部署即上线**：`yarn deploy`（hexo deploy）会把构建产物推到 `gh-pages`，立刻改变线上站点。**未经用户明确指示不得执行**；日常改动只做到 `yarn build` / `yarn serve` 验证为止。
2. **文件名即 URL**：禁止重命名或移动 `source/_posts/` 下的已有文章（含目录间挪动）。permalink 为 `posts/:year/:title/`，`:title` 取自文件名；且年份目录之外的主题目录名（如 `读史/`）会作为前缀泄漏进 URL——任何挪动都会断外链。
3. **`source/_drafts/` 是用户个人草稿区**：不渲染、不代为整理，未经用户指示不提交、不修改。
4. **`gh-pages` 分支是构建产物**：禁止手工编辑或直接提交，一切变更经 `hexo deploy`。
5. **凭据最小权限**：GitHub fine-grained PAT 平时保持最小权限；`deploy` 前由用户临时扩 Contents 写权限，用完收回。推送走 https，凭据由 gh credential helper 自动注入，不要配置 SSH。
6. **公开仓隐私红线**：本仓库是公开仓库，任何待提交内容不得包含隐私或敏感信息——本地路径与机器信息、凭据/密钥/Token/AppID、真实个人信息、内网与服务器地址等。**每次提交前对拟提交内容做严格扫描**（敏感关键词检索 + 变更文件逐个过目），发现即停，报告用户处置。

## 操作准则

- **不逐次提交**：工作告一段落后列出改动清单，经用户确认再提交；`git add` 只指定相关文件，个人草稿与未跟踪文件绝不捎带。
- **构建验证是提交前置**：改动后 `yarn clean && yarn build` 须零报错，界面相关改动加 `yarn serve` 人工抽查。
- **`_config.butterfly.yml` 是最小覆盖文件**：只写与主题默认值不同的定制项；升级 butterfly 大版本时对照新版默认配置重新映射（2026-09-12 的 4.5.1→5.7.0 迁移映射：`highlight_*`→`code_blocks`、`fancybox`→`lightbox`、`sharejs`→`share.use`、副标题速度参数→`typed_option`、`mathjax/katex`→`math`）。
- **隐藏文章**：front-matter 加 `hidden: true`（hexo-hide-posts），页面仍生成直链但不出现在首页/归档/sitemap。
- **评论系统当前关闭**（原 Valine 因 LeanCloud 停服失效，2026-09-12 移除）；恢复评论属新决策（备选 giscus/Waline/Twikoo），先问用户再做。
- **新文章命名**：干净的英文 slug 或无标点中文标题，中文完整标题写进 front-matter（避免 URL 百分号编码）；`hexo new` 自动落当年年份目录，写系列文放主题目录前知悉第 2 条铁律。
- **todo.md 是任务清单**：做完勾选 `[x]`，新待办按「需要做 / 建议做」分类写入。
- **文档不含本地环境信息**（铁律 6 在文档维度的具体化）：任何提交进本仓的文档不得出现本地文件系统路径、本机工具链细节或私有仓库名称。

## 常用命令

```bash
yarn install               # 安装依赖（改 package.json 后必跑）
yarn clean && yarn build   # 清缓存 + 全量构建（public/，约 1~2s）
yarn serve                 # 本地预览 http://localhost:4000
yarn deploy                # ⚠️ 发布上线（铁律 1，需用户指示 + PAT 扩权）
npx hexo new "slug"        # 新文章（落在 source/_posts/<当前年份>/）
```

## 目录结构

```
├── AGENTS.md / README.md / todo.md
├── _config.yml              # Hexo 主配置（站点信息/永久链接/deploy）
├── _config.butterfly.yml    # 主题最小覆盖配置（只存定制项）
├── package.json / .nvmrc    # 依赖与 Node 版本锁定
├── source/
│   ├── _posts/              # 文章：年份目录（2009–2019）+ 主题目录（读史/管理）
│   ├── _drafts/             # 个人草稿（不渲染、不代管）
│   ├── images/              # 全站图片（中心式）
│   └── about/ 404.md CNAME  # 关于页 / 404（头像背景）/ 自定义域名 www.catxn.net
├── scaffolds/
└── public/                  # 构建产物（gitignore）
```
