title: 一行代码在wordpress任意页面实现重定向
categories: 建站心得
tags: [建站,wordpress,php]
s: wordpress-shortcode-redirect
date: 2016-06-15 09:28:42

---

出于某些原因，需要将Wordpress站点的某篇文章或者某个页面临时重定向到另一个地址。
有现成插件可以使用，太重；当然也可以给需要转向的页面加JS代码实现，这种方法又不够方便也不够优雅。

于是用简码（shortcode）来实现。用一小段代码给站点增加一个新的用于转向的简码，那么每次在需要转向的页面或文章中，只要加入这么一句话，即可轻松转向：

```
[my_redirect url="http://www.example.com"]
```

## 实现

先贴代码

<!--more-->

```php
// 1. 重定向简码实现
function my_redirect_shortcode($params) {
    // 提取参数
    extract(shortcode_atts(array(
	      'url' => '/',
	    ), $params));

	 // 功能实现
    header('Location: '.$url);
}

// 2. 简码注册函数
function my_add_shortcodes() {
    add_shortcode( 'my-redirect', 'my_redirect_shortcode' );
}

// 3. 在初始化时注册简码
add_action( 'init', 'my_add_shortcodes' );
```

将以上代码加入主题的functions.php即可。当然，我的做法是使用子主题，然后在子主题的functions.php中加入自定义代码以避免主题升级时丢失修改。

## Wordpress 简码介绍

Wordpress 从2.5版开始引入简码功能。该功能简单说就是给一段PHP代码加上名字，然后在页面通过这个名字触发服务端代码执行。调用时的用法与BBCode非常相似。

Wordpress [内置了很多有用的简码][link-wp-builtin]，同时提供了[自定义简码的API][link-api]。

上面功能的实现就是基于自定义简码API，稍作解释如下。

生成一个简码三个步骤：

1. 实现简码功能的代码。如上面的 `my_redirect_shortcode`。这是核心。
2. 注册简码的代码。如上面的函数 `function my_add_shortcodes()`。这个函数调用WP函数 `add_shortcode()`，给一个简码函数设定一个名称
3. WP初始化时加载注册简码。

这里面主要是简码函数的实现。该函数接受两个参数，第一个参数是简码参数，在我们这里就是url。函数实现部分调用WP函数extract来提取参数并赋缺省值。

我这里实现的只使用了一个参数，实际上简码接受第二个参数`$content`，这个参数的用法如下

PHP部分

```php
function my_shortcode($params, $content='defaut content') {
    // 提取参数
    extract(shortcode_atts(array(
	      'url' => '/',
	    ), $params));
	 
	 // 用params干点啥
	 // ...
	 
	 // 用content干点啥
	 // ...
}
```

页面调用

```html
[my_code param="param"]my_content[/my_code]
```

## 简码插件推荐

这里推荐两个很好用的插件: 

- [Magee Shortcodes][link-magee]
- [Shortcodes Ultimate][link-ultimate]

前一个很多主题都依赖，基于Bootstrap提供了一组HTML5/CSS3排版的简码
后一个提供很多非常有用的简码，同时它还有一个强大的功能就是“自定义简码”。嗯，有它就不需要去收工编辑functions.php了。XD

## 参考链接

- [Wordpress Shortcode](https://codex.wordpress.org/Category:Shortcodes)


[link-magee]: https://wordpress.org/plugins/magee-shortcodes/
[link-ultimate]: https://wordpress.org/plugins/shortcodes-ultimate/
[link-wp-builtin]: https://codex.wordpress.org/Shortcode
[link-api]: https://codex.wordpress.org/Shortcode_API

