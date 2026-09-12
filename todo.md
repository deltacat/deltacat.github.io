# TODO

> 约定：`[ ]` 未完成，`[x]` 已完成，完成一项勾一项。
> 依据：2026-09-12 hexo 6.3→8.1.2 / butterfly 4.5.1→5.7.0 全量升级会话。

## 需要做（升级收尾，按顺序执行）

- [x] 提交升级改动：`package.json`、`yarn.lock`、`.nvmrc`、`_config.yml`、`_config.butterfly.yml`、`AGENTS.md`、`README.md`、`todo.md`，并删除 `wordpress.2015-03-30.xml`（不含 `_drafts/` 个人草稿）
- [ ] 部署前临时扩 GitHub PAT 的 Contents 写权限（https 推送凭据由 gh credential helper 注入）
- [ ] 发布模式已切换为 Cloudflare Pages（生产分支 main）：推送 `c777b67` 与后续提交即触发构建上线（站点外观将由 NexT → butterfly 5.7）
- [ ] 部署后线上核对：首页 banner/副标题打字机、文章页目录与代码块、归档、404 页、评论已关闭、sitemap.xml
- [ ] 部署后在用户的仓库管理项目日志中补记本次发布

## 建议做（可选，按需领取）

- [ ] 处理根级样例文 `source/_posts/hello-world.md`（Hexo init 遗留，正被当普通文章收录进 sitemap）：删除或加 `hidden: true` 留档
- [ ] 恢复评论系统（Valine 已随 LeanCloud 停服移除）：giscus（零后端，数据存 GitHub Discussions）/ Waline、Twikoo（需自建后端，可尝试导入旧 Valine 数据）
- [ ] GitHub Actions 自动构建发布（push main 云端生成 gh-pages，本地免 Node 环境）
- [ ] 新文章命名习惯：英文 slug 或无标点中文标题（`hexo new` 用干净名，中文标题写进 front-matter），避免 URL 百分号编码
- [ ] 视觉微调（如与旧站观感比对后在意）：关闭页脚主题版本号 `footer: {copyright: {version: false}}`；分享站点 twitter→x 为 5.x 默认
- [ ] 个人草稿 `source/_drafts/gitea-security-incident-report-20260912.md` 完稿后自行提交（当前处于暂存状态，勿混入升级提交）
