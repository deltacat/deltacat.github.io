title: "在 Amazon AWS 搭建及部署网站：序"
date: 2013-08-30 13:36:45
tags:
id: 166
categories:
  - 建站心得
---

最近玩了把 AWS，实现了服务器的创建、PHP+MySql运行环境、代码部署等。一方面，后面的项目会反复重复这个流程，需要一份手册，另一方面，也给自己一个记录。于是把整个过程和要点整理一下，发到自己的博客。

主要用到的服务、技术、术语先列个清单：

**亚马逊服务：**

1\. EC2: 亚马逊弹性云计算（Elastic Compute Cloud）。其实就是个虚拟主机。有多种操作系统可选。本文基于Linux系统
2\. RDS：Relational Database Service，数据库
3\. CloudFront：类似CDN的东西，网站前端加速。

**后台环境：**

1\. Apache: Web 服务器。可通过 yum 安装
2\. PHP: PHP, PHP-MySQL 扩展, phpMyAdmin。均可通过 yum 安装
3\. MySQL: mysql, mysql-server, mcrypt 扩展, mbstring 扩展，均可通过 yum 安装
4\. SSH：EC2内置，可通过yum安装
5\. Subversion：subversion, mod_dav_svn 可通过 yum 安装
6\. Wordpress：这个是用于测试后台、数据库连接是否正常以及测试各地连接速度用的。对于国内个人博客来说，用亚马逊主机搭建成本太高。

**前端工具：**

1\. 主机终端工具：Bitvise SSH Client （Tunnelier ），或 Putty
2\. SVN 客户端工具：TortoiseSVN
3\. 代码编辑工具：Notepad ++, PHP 插件，SVN 插件，……

**系列目录：**

1\. AWS EC2 主机的创建
2\. 配置基本运行环境（Apache, PHP, MySql, RDS）
3\. 配置代码部署环境（Subversion）
4\. 支持SSL连接

**参考资料：**

1\. [如何把Wordpress安装到Amazon EC2](http://www.alibuybuy.com/posts/59628.html)
2\. [Linux下Apache+PHP+MySQL+Subversion开发测试环境配置过程](http://www.linuxdiyf.com/viewarticle.php?id=61323)
3. [Wordpress 安装向导](http://codex.wordpress.org.cn/Installing_WordPress)