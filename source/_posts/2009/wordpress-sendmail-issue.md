title: "发现一个问题，WordPress 邮件发送"
date: 2009-12-31 18:13:51
tags: [流水账, 建站]
id: 73
categories: 建站心得
hidden: true

---

Google 了下，没有找到解答。

现象如下：

本博目前使用的是 WordPress 2.9，建立于 cPanel 主机。没有使用主机的邮件系统来接收邮件。也就是说，发送到catxn.cn域的所有邮件，理论上应由我自定义的邮局接收。

我的自定义邮局在其他地方工作正常。但是，如果从 Wordpress 发送邮件至 catxn.cn 的邮件帐户，无论如何都无法接收到。

怀疑是 WordPress 把发送到所有 catxn.xn 的邮件，都发送到本地邮件系统了。这个问题很讨厌，不知道怎么解决。

=== ===

搞定。

需要在 cPanel 面板的邮件系统中做简单设置。将对应域名的 MX 记录指向邮局的域名。 :P