title: "Chrome 扩展 “Butterfly Sharing” 更新并开源"
date: 2015-02-25 11:58
tags: [软件开发,chrome,javascript]
categories: 软件开发
s: butterfly-sharing-opensource
---

五年前为了自己用着方便，写了个小小的Chrome扩展：[Butterfly Sharing](https://chrome.google.com/webstore/detail/pofanjellkmepliaagnpopjndkkdlolg)

名字很土，完全是顺手摸了个图标加个名字就放上去，然后就懒得改了。

简单介绍下它的功能：

* 缩短网址。比如你正在浏览 `http://www.jianshu.com/p/2f737f8fe75f` ，点一下扩展按钮，就可以变成这样： `http://t.cn/RwNrNHA`。用来记录和分享就容易一些。
* 分享到SNS。比如刚刚那个网址，可以直接变成 `初出茅庐的IT人员，怎样才能做好项目经理？ http://t.cn/RwNrNHA`，你可以点一下按钮就直接把这段文字分享到微博、开心网、facebook什么的，或者拷贝到邮件/聊天工具中发给朋友，因为网址简短，它不会破坏你的版面。
* 短网址引擎可选择，现在支持 goo.gl，t.cn，等等
* 支持二维码。这样你在电脑上看到一个很好的网页，想发到手机就很方便了。

<!-- more -->

# 项目开源

* 扩展发布于 [Chrome 应用商店](https://chrome.google.com/webstore/detail/pofanjellkmepliaagnpopjndkkdlolg)，兼容猎豹浏览器。
* 代码开源，托管于 [GitHub](https://github.com/deltacat/butterfly-sharing)。

这个扩展最初是修改自 goo.gl shortener，因为 goo.gl 需要翻墙，用起来并不总是很方便（尤其是对你要分享的朋友来说），并且当时它对国内的 SNS 支持并不完全。所以参考这个扩展自己弄了一个。

时过境迁，国内外 SNS 站点都发生了很多变化，比如很好用的Google Reader彻底挂了。比如现在分享的主力从PC端转移到了移动端，所以这次加上了二维码。

感觉这个小小的扩展，还有一些用武之地，于是干脆把它开源。虽然代码是门外汉水平，对初学chrome扩展开发的同学应该还是有所帮助。同时，如果有熟悉 JavaScript、CSS 的同学愿意同我一起来完善这个插件将是非常令人开心的事情。有意协作请发邮件或留言。

# Chrome 扩展开发主要变化

这个扩展自从提交到应用商店就没怎么动过。最近有时想把在电脑上浏览的网页直接转发到朋友圈，发现并没有现成的好用的扩展，于是想给之前这个扩展加上二维码的功能。本以为几句代码改改的事情，没想到一动手才发现麻烦大了：Google 在两年前强制要求所有新提交的扩展一律将manifest版本升级到2，随之而来的是一堆编码规则的变化。

春节前用业余时间清理了一下代码重新发布，过完年总结下这些坑，如果有遇到同样问题的同学，可以参考下。

## Manifest 更新

详细的描述参考 [Google 官方文档](https://developer.chrome.com/extensions/manifestVersion)
主要变化如下：

* manifest_version 升级到2

这个倒是简单，只要在manifest.json 文件中加一行：

	"manifest_version": 2

* background 更新

“background_page” 失效。我们不需要一个单独的background.html来包含后台脚本。现在只需要简单地在manifest中编辑属性 "background" 即可。例如：

	"background": {
		"scripts": [
					"scripts/consts.js", 
					"scripts/preferences.js", 
					"scripts/background.js",
					"scripts/services.js"
					],
		"persistent": false
	  }

这个方法其实比以前省事。

* 弹出页的定义

默认的弹出页定义方式改为：

	"browser_action": {
		"default_icon": "icons/butterfly-48.png",
		"default_popup": "popup.html"
	}


## 代码规则更新

*  新的[内容安全策略 (CSP)](https://developer.chrome.com/extensions/contentSecurityPolicy) 

Manifest 的更新很简单。这个 CSP 才是大 BOSS。简单来说，就是不再允许 HTML 内嵌脚本，比如 onclick, onload 这些统统不能用了。所有的脚本一律要放到 JS 文件中去。

所以，以前直接写在 HTML 文件中的事件处理，只能在 JS 文件中通过 DOM 操作进行。好在用 jQuery 来弄这个很方便，不过要从以前埋的坑里爬出来，只能手工一条条去改。

	sendMessage / onMessage

所有的 sendRequest / onRequest 一律改为 sendMessage / onMessage。用法倒是没变。

嗯，差不多就这么些东西了。
