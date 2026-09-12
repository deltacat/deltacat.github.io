# Changelog

本文件记录博客仓库自身的变化。每次部署的详细记录见 Cloudflare Pages 控制台的部署历史。

## [0.2.0] - 2026-09-12

### Added
- `AGENTS.md`：AI 代理协作规则（六条铁律：部署即上线 / 文件名即 URL / 草稿区不动 / gh-pages 产物规则 / 凭据最小权限 / 公开仓隐私红线）
- `todo.md`：任务清单（需要做 / 建议做，checkbox 勾选制）
- `CHANGELOG.md`：本文件

### Changed
- Hexo 6.3.0 → **8.1.2**；butterfly 4.5.1 → **5.7.0**，`_config.butterfly.yml` 由全量配置重写为最小覆盖文件（12 组定制项按新结构映射）
- Node 16.19 → **24**（`.nvmrc`）；**Yarn 1 → 4.18**（corepack 接管 + `packageManager` 字段钉定，`.yarnrc.yml` 采用 node-modules 兼容模式）
- 全部 hexo 插件升至最新（feed 4 / index 4 / marked 7 / stylus 3 / hide-posts 0.4 / deployer-git 4）
- **发布模式切换**：本地 `hexo deploy` → **Cloudflare Pages**（生产分支 main，推送即构建发布）；deploy 配置收敛为单目标 GitHub（https）；gh-pages 分支与 GitHub Pages 退役
- 主页副标题打字机速度参数改经 `typed_option` 透传；图片查看统一为 `lightbox: fancybox`

### Removed
- Valine 评论系统（LeanCloud 停服后端失效；备选 giscus / Waline / Twikoo 待议）
- `wordpress.2015-03-30.xml`（2015 年迁移存档，已过时）
- coding.net 部署目标与 baidusitemap 组件及配置（baidu-sitemap 插件 2023-07 起停更；migrator-wordpress 使命完成）

### Security
- 清除文档中的本地环境信息（路径 / 工具链 / 私有仓名）；新增铁律：提交前对拟提交内容做隐私与敏感信息严格扫描

## 前置历史（择要）

- 2023-01-09：源码切换 butterfly 主题（当时未及部署，直至 2026-09 本次升级一并完成）
- 2023-01-05：NexT 版本最后一次部署上线（gh-pages 分支保留为最后一代部署存档）
- 2017-03 → 2019-12：NexT 主题时期（启用、多次更新、ICP 备案）
- 2015-03：自 WordPress 迁移至 Hexo（jacman 主题；原始导出存档已于 2026-09 移除）
