# 夜猫阿罗哈 — 个人博客

猫老师的个人网站：https://www.catxn.net （GitHub Pages，自定义域名已绑定）

基于 [Hexo](https://hexo.io/zh-cn/) 8.1.2 + [butterfly](https://butterfly.js.org/) 主题 5.7.0 构建；`main` 分支存源码，构建产物由 `hexo deploy` 推送至 `gh-pages` 分支发布。

## 快速上手

```bash
yarn install               # 安装依赖（Node 24，版本锁定见 .nvmrc）
yarn serve                 # 本地预览 http://localhost:4000
yarn clean && yarn build   # 清缓存 + 全量构建
yarn deploy                # 发布上线（需用户指示 + PAT 临时扩 Contents 写权限）
```

## 目录导览

- `source/_posts/` — 文章（年份目录 + 读史/管理 主题目录）；`_drafts/` — 草稿（不渲染）
- `_config.yml` — 站点配置；`_config.butterfly.yml` — 主题定制（最小覆盖式，只存与默认不同的项）
- `source/images/` — 全站图片；`source/CNAME` — 域名 www.catxn.net
- `todo.md` — 任务清单（需要做 / 建议做）

## 代理协作

AI Agent 参与维护前先读 **[AGENTS.md](AGENTS.md)**：部署即上线、文件名即 URL、草稿区不动、PAT 最小权限等规则都在那里。

## 资料

- [Hexo 文档](https://hexo.io/zh-cn/docs/) · [butterfly 文档](https://butterfly.js.org/)
- 主题沿革：jacman（2015–2017）→ NexT（2017–2023，线上最后版本）→ butterfly（2023-01 源码切换，2026-09 升级 5.7 并部署）
