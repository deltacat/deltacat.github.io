title: "在 Amazon AWS 搭建及部署网站：（二）安装、配置软件，启动网站"
date: 2013-09-01 16:18:39
id: 209
tags: [建站, aws]
categories: 建站心得
---

现在，我们已经有了一台EC2主机，具备了基本的硬件环境。下面，开始软件环境的配置。

<!--more-->


**第一步：连接服务器**

后面所有的一切，都需要在SSH终端窗口操作。首先，我们需要一个SSH客户端。PuTTY是很常用的工具。这里我们选用 Bitvise SSH Client。这个软件之前叫做“Tunnelier”。软件可以在 [这里下载](http://www.bitvise.com/ssh-client-download)。

下载好后安装，在SSH这一栏填入必须的信息。主机名按实际地址填写，username填ec2-user(EC2默认用户名)，在 User Keypaire manager 这里导入之前申请EC2过程中保存的Key Pair 文件（.pem格式）。这里Bitvise Client 的好处就体现出来了，PuTTy无法直接使用AWS格式的KeyPair文件，需要做一次转换，而Bitvise可以直接导入。导入后，选择 Initial Meth中导入的key的栏位，一般是 Slot 1，根据实际情况填写即可：[![image](http://www.catxn.net/wordpress/wp-content/uploads/2013/09/image_thumb4.png "image")](http://www.catxn.net/wordpress/wp-content/uploads/2013/09/image4.png)

填写好后，点击Login，第一次回弹出一个确认窗，是否接受认证信息，点击确认，一切正常的话登陆成功后左侧导航栏会出现 “New terminal console”, “New SFTP Window”。点击 new terminal console，熟悉的Linux终端提示符就出现了。后面所有的命令行操作均在这里进行。

[这篇文章](http://calebogden.com/wordpress-on-linux-in-the-amazon-cloud-with-mac)中提到了如何直接以root身份登录。实际上，所有的操作均可用sudo命令来执行。如果不需要用SFTP向系统文件中上传文件，没有理由直接root登录。后面会介绍用SVN部署代码的方法，除了一些特殊情况，SFTP基本上不怎么需要了。

**第二步：安装基本服务：Apache、PHP、MySQL**
<!--more-->

作为一个基本Web网站，我们需要至少这三样东西：Apache，PHP以及 MySQL。实际上数据库我们将来会使用 RDS提供的MySQL实例。不过这里还是从安装本机版本逐步开始。

依次输入下列指令，安装Apache，MySql，PHP 以及必备扩展库，启动服务，设置 MySQL主密码。
  > // 安装 apache      
> yum install http       
> // 安装 PHP 及必备支持库       
> yum install php libmcrypt libmcrypt-devel php-mcrypt php-mbstring       
> // 安装 php-mysql 扩展       
> yum install php-mysql       
> // 安装 mysql       
> yum install mysql       
> yum install mysql-server       
> // 设置 MySQL 主密码       
> mysqladmin -u root password '&lt;the password&gt;'       
> // 启动服务       
> service httpd start       
> service mysqld start  

按照提示完成安装后，在浏览器输入主机地址，这时应该会看到Apache的默认欢迎页，表示我们的基本Web服务器开始运行了。

**第三步：安装phpMyAdmin**

phpMyAdmin 用来管理后台MySQL数据库。首先，我们从官方网站下载，然后解压缩。移动至Web根目录并重命名为 “phpMyAdmin”。因为刚刚下载的文件夹解压后是保护版本号等信息的。
  > wget http://sourceforge.net/projects/phpmyadmin/files/latest/download      
> unzip download       
> mv &lt;unzipped folder name&gt; /var/www/html/phpMyAdmin       
> rm download  

解压之后，删除下载的文件。    
现在，赋予phpMyAdmin目录正确的权限。首先坚持Web服务器的用户
  > egrep 'User|Group' /etc/httpd/conf/httpd.conf  

这里会列出如下信息，默认应该是 apache，如果不是，以列出的为准。

User apache    
Group apache

执行如下命令设置权限：
  > useradd phpmydmin      
> passwd phpmyadmin       
> cd /var/www/html       
> chown –R phpmyadmin.apache phpMyAdmin/  

然后，创建配置目录并设置权限
  > cd phpMyAdmin      
> mkdir config       
> cp config.sample.inc.php config/config.inc.php       
> chown –R phpmyadmin.apache config/       
> chmod –R o+rw config/  

完成后重启服务。
  > service httpd restart  

然后用浏览器打开 http://&lt;host&gt;/phpmyadmin/setup，管理界面就显示出来了。点击“新建主机”，输入主机名，主机地址（默认localhost），点击”认证“，选择”config“认证方式，输入数据库用户名和密码。点击保存。[![image](http://www.catxn.net/wordpress/wp-content/uploads/2013/09/image_thumb5.png "image")](http://www.catxn.net/wordpress/wp-content/uploads/2013/09/image5.png)完成后的的关键一步是，回到setup概要页后，注意要保存配置文件：[![image](http://www.catxn.net/wordpress/wp-content/uploads/2013/09/image_thumb6.png "image")](http://www.catxn.net/wordpress/wp-content/uploads/2013/09/image6.png)在这里保存后，所有的设置在真正回写到配置文件中。现在，在浏览器打开 http://&lt;host&gt;/phpmyadmin，登录进入数据库管理后台。如果成功的话，出于安全考虑，我们要删除配置文件和setup目录。
  > cd /var/www/html/phpMyAdmin      
> mv config/config.inc.php ./       
> rm –rf config/       
> rm –rf setup/  

至此，phpMyAdmin配置完成。另外，为了快速开始，我们在认证方式中选择config方式。建议采用cookie方式连接。这里暂时不多做介绍。

**第四步：开始第一个 Wordpress 网站**

此类教程网上铺天盖地，这里只做简单描述。

首先，下载，解压，设置权限。
  > cd /var/www/html      
> wget http://wordpress.org/latest.zip       
> unzip latest.zip       
> chown –R apache.apache wordpress/       
> chmod –R 755 wordpress  

然后，在浏览器中打开 http://&lt;host&gt;/wordpress 。熟悉的 wordpress 初始化页面出现了。按照之前设置的数据库用户名密码填写，根据向导完成后，一个 wordpress 页面就出现在我们眼前。

**第五步：虚拟主机，域名绑定，以及一点收尾工作**

对于Apache的设置，有两件事需要做。第一，出于安全性考虑，应禁用目录浏览。第二，为便于使用（或其他目的），我们需要在同一主机上host多个网站。这些工作都可以通过编辑 Apache 配置文件完成。

首先，用 vi 打开 /etc/httpd/conf/httpd.conf ，找到如下段落：
  > &lt;Directory &quot;/var/www/html&quot;&gt;     
> &#160;&#160;&#160; Options **Indexes** FollowSymLinks      
> &lt;/Directory&gt;  

删除”Indexes“即可，就禁用了 Web 主文档目录及所有子目录的目录浏览操作。也可对特定目录做此操作。

编辑虚拟主机。首先，需要找到这一行（通常在靠近结尾处）

NameVirtualHost *:80

解开注释。然后在末尾按需求添加段落，比如我们有一个域名 my.name.com，让它指向我们刚刚创建的wordpress站点：
  > &lt;VirtualHost *:80&gt;     
> &#160;&#160;&#160; ServerName my.name.com      
> &#160;&#160;&#160; DocumentRoot /var/www/html/wordpress      
> &lt;/VirtualHost&gt;  

现在，保存，退出，重启 apache。在浏览器输入 my.name.com   
Enjoy it!

**参考资料：**

1\. [http://codex.wordpress.org.cn/Installing_WordPress](http://codex.wordpress.org.cn/Installing_WordPress "http://codex.wordpress.org.cn/Installing_WordPress")    
2\. [http://httpd.apache.org/docs/2.2/vhosts/name-based.html](http://httpd.apache.org/docs/2.2/vhosts/name-based.html "http://httpd.apache.org/docs/2.2/vhosts/name-based.html")    
3\. [http://www.cnblogs.com/88999660/articles/1581524.html](http://www.cnblogs.com/88999660/articles/1581524.html "http://www.cnblogs.com/88999660/articles/1581524.html")