title: "迁移到Hexo，双线镜像"
date: 2015-03-31 11:40:17
tags: [建站, hexo, github, blog]
categories: 建站心得

---

一直以来都是用 wordpress。随着 WP 日渐臃肿，以及在喜欢上 Markdown 写作之后，就一直想找个能对 Markdown 支持完美的，轻量级的，并易于版本管理的博客系统。

在这种需求下，Jekyll 落入了视线。

Jekyll 是 GitHub 和 GitCafe 内置支持的静态网页生成引擎，可定制化程度也高。看上去对我们程序猴子这种不折腾会死的物种非常合适，但是经过一段时间的观察和分析后。感觉可能它并不是最佳选择。

然后我看到了 cnfeat 这篇 [简明Github Pages与Hexo教程][link-cnfeat-hexo]。瞬间就被它吸引了。

![骗点击][img-girls]

# 选哪个

两个引擎都支持 markdown，都可以利用 git 库进行管理。最终发布的都是静态页面。这一点没什么大的差别。相对而言，Jekyll 发展较早，相对比较成熟，Hexo 是后起之秀，但是发展迅速。

Jelly 是 github 的默认推荐。它的工作方式是把源文件上传至 github 的特定分支，然后 github 在发现有文件更新时，在服务端重新编译生成静态网页自动部署至 master 分支。

Jekyll 的优势是发展比较成熟，因为是官方推荐，支持也比较好。看上去对程序员来说也更加优雅便于折腾。缺点是配置比较复杂，需要遵循一定的规范比如一定要放在 gh-pages 分支。因为是提交源文件在服务端生成，所以在内容发生变化到前端呈现会有一定延迟。

Hexo 的原作者是一名台湾的学生。推出的目的就是为了提供更大的便利，更容易入手，更方便配置。

实际上也是如此，Hexo 从安装、配置、发布都非常简洁。常用的命令记住 `n g s d` 四个字母就搞定。

Hexo 还有一个决定性的好处，下文说。

总之最后我选择了 Hexo，在代码库中建立一个独立的 hexo 分支用于存放源文件。

<!-- more -->

# 双线部署


hexo 可以同步推送到多个git库，比如，在配置文件里这样写：

```
deploy:
  type: git
  message: "hexo deploy generated pages"
  repo: 
    github: <github repo url>
    gitcafe: <gitcafe repo url> 
```

然后执行部署命令，就可以同步部署到 gitcafe 和 github。

如果用 dnspod 来做实现双线解析，瞬间就可以实现境内境外双线解析镜像。这点非常爽。不过这里有个坑，参见下一节。


# 坑

这里是一些大坑。估计如果看到Hexo的介绍后，想自己动手试试看的都会遇到。

- 首先，部署到GitHub时，应该直接部署到master分支。部署到 gitcafe 则应部署至 gitcafe-pages 分支。
	
	gitcafe-pages, gh-pages 这样的分支是用来给Jekyll在服务端生成静态网站用的。
	
	用hexo生成网站是在本地生成后部署静态页面，这时直接部署到master即可。但是 gitcafe 的 master 分支貌似不会生成网站，必须部署至 gitcafe-pages 分支。
	
- 然后，Hexo 现在最新的版本是3.0 
	
	config的设置项有所更改，包结构也有变化。
	
	最坑的是，如果多点部署的话，会报错。看了下应该是 3.0 更新后的bug，在 github Hexo 项目上，已经有人提了[issue][link-hexo-issue-repo]，看什么时候能解决吧。
	
	3.0 详细的变化可以参考 [github 上的文档][link-hexo-v3].

	**更新**: 昨天 git deployer 更新到 0.0.4，修正了该问题。参见[作者的说明][link-hexo-isse-repo-comment].

	更新方法：编辑 package.json，把 hexo-deployer-git 的版本升级到 0.0.4，然后 `npm update`.
	
- 迁移自 wordpress

	用插件 hexo-migrator-wordpress 即可一键完成。比较郁闷的是，如果原文的标题是中文，那么这里所有的文件名都会被解析成形如 ab-cd-ef-gh-de-ty-ww-rt-as-cd-fg.md 这种形式。原因可以理解，它首先将中文进行编码，然后替换空格最终形成“slug”形式的permalink。但是看着实在很蛋疼。

- 目录（categories）

	hexo 支持多目录。但是……

	如果你指定了多个目录，比如 `[建站心得，流水账]`，那么最后呈现出的目录列表可能是这个样子的：

	```
	建站心得5
	流水账5
	流水账1
	流水账1
	流水账1
	...
	```

	泪奔。所以还是老老实实用单category，结合标签云好啦。
	
还碰过其他坑，暂时记起来这么多。后面想起来或遇到新坑再更新。

	
# 写作工具

当然。你可以直接在简书写，跨平台，实时保存。XD 然后复制到 Hexo 源文件目录。

Mac 平台的话，强烈推荐 “Mou”。
	
# 最后

详细的 Hexo 安装设置指南就不多说了。 cnfeat 的文章讲得非常详细，即使小白也能按照所列的步骤一步步搭建起自己的一个静态博客。

只是有一点，弄这些玩意儿，翻墙技能还是要有一点的。否则一个 npm 命令都会让你抓狂。

[国际惯例，成品展示在这里。][link-my-site]

Enjoy it!

(题图来自 [Hexo 作者的个人主页][link-zespia]。据说可以提高点击率。XD)
	

<!-- 链接区 -->
[link-cnfeat-hexo]: http://cnfeat.com/2014/05/10/2014-05-11-how-to-build-a-blog/
[link-ibruce-hexo]: http://ibruce.info/2013/11/22/hexo-your-blog/
[link-hexo-issue-repo]: https://github.com/hexojs/hexo/issues/1108
[link-hexo-v3]: https://github.com/hexojs/hexo/wiki/Breaking-Changes-in-Hexo-3.0
[link-my-site]: http://www.catxn.net
[link-zespia]: http://zespia.tw
[img-girls]: http://static.catxn.cn/images/acg/zespia001.jpg-o
[link-hexo-isse-repo-comment]: https://github.com/hexojs/hexo/issues/1108#issuecomment-87942286

