title: "WordPress"
date: 2009-12-25 12:38:36
id: 14
tags: [建站, wordpress, 流水账]
categories: 建站心得
hidden: true

---

在 Wopus IDC 申请了空间，开始我的 WordPress 之旅。

首先是几个必要的 WordPress 设置。

然后选择主题。这里我使用了 MG12 制作的 iNove 主题。

<!--more-->

**安装必备插件：**

1.  Google Analyticator2.  Syntax Highlighter and Code Prettifier Plugin for WordPress3.  SEO Title Tag  

**进行一些特殊修改：**

1.  iNove 主题本身不支持调整 Title 的显示。安装 SEO Title Tag 后，需要使用 FTP 下载 header.php （位于 themes/inove），然后将 &lt;Title&gt;…&lt;/Title&gt; 标签替换成如下内容：      <pre class="brush: php; auto-links: true; collapse: false; first-line: 1; gutter: true; highlight: [10,11]; html-script: false; light: false; ruler: false; smart-tabs: true; tab-size: 4; toolbar: true;">&lt;title&gt;&lt;?php if (function_exists('seo_title_tag')) { seo_title_tag(); }	else { bloginfo('name'); ?&gt;&lt;?php wp_title(); } ?&gt;&lt;/title&gt;</pre>
2.  根目录重定向。为了保持根目录的整洁，我将 WordPress 安装在 wordpress 目录下。同时希望，以 [http://www.catxn.cn](http://www.catxn.cn) 的形式可以直接访问博客。需要调整设置，设置-&gt;常规选项-&gt;博客地址(URL)，这里改为博客的地址。并将 wordpress 下的 index.php 文件拷贝至根目录，并编辑 require(…) 为
        <pre class="brush: php; auto-links: true; collapse: false; first-line: 1; gutter: true; html-script: false; light: false; ruler: false; smart-tabs: true; tab-size: 4; toolbar: true;">require('./wordpress/wp-blog-header.php'); </pre>

**几个备用链接：**

- [启用iNove主题，并做几个小修改](http://www.quhao.cn/post/wordpress-theme-inove.html/comment-page-1)

- [.htaccess 教程](http://hi.baidu.com/seobb/blog/item/97f8cf228b1bfef0d7cae2a0.html)

- [新手建站攻略](http://www.ifunkey.com/topics/%E7%8B%AC%E7%AB%8B%E4%B9%8B%E9%81%93/%E6%96%B0%E6%89%8B%E5%BB%BA%E7%AB%99%E6%94%BB%E7%95%A5)

- ![双片金鱼](http://farm3.static.flickr.com/2510/4007319845_6e57aafdde.jpg)