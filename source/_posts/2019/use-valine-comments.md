title: 启用valine评论系统
s: use-valine-comments
date: 2019-03-23 12:00:00
categories:
- 建站心得
tags: [hexo,建站]

---

以前用过的一些评论系统基本都挂了。

最近想重新启用。研究了几个典型的比如 [gitalk][GiTalk]，[来必力][LiveRe]，都有些不满意之处。

gitalk 主要担心安全性，毕竟 github OAuth App 给的权限过大。
LiveRe 是韩国公司，不怎么放心

最终选择了 [Valine][Valine]，原因如下：

1. 基于 [LeanCloud][LeanCloud]，成熟 SaaS 服务，一时半会儿不大会关；
2. 可为评论创建独立 LeanCloud 应用。数据库完全由自己控制并与其他数据隔离；
3. 免登陆无后端，配置简单，用户体验较好；
4. 无缝集成 [Hexo NexT 主题][NexT]。

<!-- more -->

快速开始：

1. 登录/注册 [LeanCloud][LeanCloud] 账号，创建应用
2. 获取 AppID，AppKey，填入 NexT 主题第三方服务 Valine 相应位置

	```
	valine:
	  enable: true
	  appid: xxxxxxxxxxxx # your leancloud application appid
	  appkey: xxxxxxxxxxxx # your leancloud application appkey
	  notify: false 
	  verify: false 
	  placeholder: Just go go
	  avatar: mm 
	  guest_info: nick,mail,link 
	  pageSize: 10 
	  visitor: true 
	  comment_count: false 
	```
3. 重新编译 hexo 文件，上传发布
4. LeanCloud 后台调整安全性设置比如安全域名

欢迎试用留言！


[LeanCloud]: https://leancloud.cn
[Valine]: https://valine.js.org "快速、简洁且高效的无后端评论系统"
[NexT]: https://theme-next.org "Theme for Hexo"
[GiTalk]: https://gitalk.github.io
[LiveRe]: https://www.livere.com

