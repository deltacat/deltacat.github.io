title: "在 Amazon AWS 搭建及部署网站：（三）开发及部署环境"
date: 2013-09-01 17:40:15
id: 169
tags: [建站, aws]
categories: 建站心得
---

服务器已经搭建好，网站也开始运行了。那么如何方便地部署代码呢？

最基本的方式，就是使用 SFTP 向网站目录直接部署。这种方法的缺点是版本控制不便，在上传时也无法方便的比较代码变化。

用SVN来部署是一个很好的选择。
SVN 本身是一个服务，我的选择是使用 Apache 的 SVN 模块来访问，不必单独启用SVN服务。

<!--more-->

**第一步：确保 Apache 正确安装和配置**

详情参见前文，这里假定 Apache 服务已正确安装，运行无误

**第二步：安装 subversion**
> // 安装 subversion
> 
> yum install subversion
> 
> // 安装 apache subversion 模块
> 
> yum install mod_dav_svn
> 
> // 检查 svn 安装
> 
> svn --version
**第三步： 配置 apache 上的 subversion**

Apache 中 subversion 配置文件: /etc/httpd/conf.d/subversion.conf
用 vi 打开该文件，找到如下被注释掉的部分，打开
> &lt;Location /repos&gt;
> 
> DAV svn
> 
> SVNParentPath /var/www/svn/repos
> 
> AuthType Basic
> 
> AuthName "Authorizion realm"
> 
> AuthUserFile /etc/svn-auth-conf
> 
> Require valid-user
> 
> &lt;/Location&gt;
这一行：
SVNParentPath /var/www/svn
改成：
SVNPath /var/www/svn/repos
“/var/www/svn/repos”是你SVN库的存放位置，可根据情况修改。

&lt;Location /repos&gt;表示在url地址是 http://&lt;hostname&gt;/repos 时访问的就是库 /var/www/svn/repos 。
AuthUserFile /etc/svn-auth-conf指明了svn帐户文件是/etc/svn-auth-conf 。

注意：在默认的 subversion.conf 文件中，以上代码段是包含在如下标签中的。这个标签表示，仅对“写”操作进行权限控制。这里的SVN服务是用于内部部署代码而不是用于开源项目，显然，应该对“读”操作做同样的限制。因此，这个标签行的注释开关应保持关闭。
> &lt;LimitExcept GET PROPFIND OPTIONS REPORT&gt;
> 
> &lt;/LimitExcept&gt;
**第四步：建立subversion帐户<!--more-->**

subversion自己对帐户进行管理，所以要客户端要访问它必须使用subversion帐户进行登录。
> // 创建第一个帐户
> 
> htpasswd -cm /etc/svn-auth-conf &lt;username1&gt;
> 
> // 创建更多其它帐户（注意参数）
> 
> htpasswd -m /etc/svn-auth-conf &lt;username2&gt;
**第五步：建立subversion仓库**
> cd /var/www/svn
> 
> svnadmin create repos
> 
> chown -R apache.apache repos
**第六步：测试Subversion工作**
> // 重启 apache:
> 
> service httpd restart
任何时候，改动过配置文件，均需重启服务使改变生效。
在浏览器中输入 http://&lt;hostname&gt;/repos 应该可以看到（会提示身份验证）：
> repos - Revision 0: /
> 
> --------------------------------------------------------------
> 
> Powered by Apache Subversion version 1.7.10 (r1485443).
打开本地SVN客户端（我这里用的是tortoiseSVN），输入地址，提示用户名密码时按之前的设定输入，一切无误的话应该可以列出代码库。至此Subversion服务配置完毕

**第七步：配置SVN自动部署最新代码至网站目录**

Subversion 的钩子脚本是一种事件触发机制，当SVN系统执行到某些预定义事件时，触发一些预定义动作。其中，post-commit 事件就可用于自动部署。简单来说，在 Web 目录 checkout 特定代码库，然后设定当 svn 提交完成时，自动更新 web 目录。我们可以这样来设置 svn 库：首先建立工作分支和发布分支，工作分支稳定后，合并最新代码至发布分支（提交前核对更改），代码提交完成后，server端的post-commit脚本从发布分支自动更新web目录。

具体来说，比如我们的网站目录在 /var/www/html/MyWeb, 首先，用如下命令 checkout 代码库：
> svn checkout &lt;repos url&gt; /var/www/html/MyWeb --username &lt;username&gt; --password &lt;password&gt;
然后，创建脚本
> cd /var/www/svn/repos/hooks
> 
> cp post-commit.tmpl post-commit
> 
> vi post-commit
将 post-commit 的内容改为：
> export LANG=en_US.UTF-8
> 
> SVN=/usr/bin/svn
> 
> WEB=/var/www/html/MyWeb
> 
> ${SVN} update ${WEB} --username &lt;username&gt; --password &lt;password&gt;
第一行 export … 是为了避免编码错误，可根据服务器设置调整，保持和服务器编码一致即可。
第二行是svn命令行位置
第三行是checkout的代码库位置，需要用chown将该目录权限授予apache用户
第四行是实际执行的命令。
保存post-commit, chmod a+x 使其可执行。然后手工执行进行测试。如果出现保存密码的提示，则需要修改设置避免该提示出现，否则客户端提交代码会失败（因为客户端没有当提示出现选择yes/no的机会）。首先将配置文件拷贝至apache的home目录，然后修改：
> cp -r /root/.subversion/ /var/www/
> 
> vi /var/www/.subversion/servers
修改如下字段即可：
> store-plaintext-passwords = no
> 
> store-passwords = no
现在一切就绪，使用客户端工具提交代码测试，检查Web目录是否同步了更改。
至此，配置工作完毕。

**参考资料：**

1\. [http://www.worldhello.net/doc/svn_hooks/svn_hooks.mm.htm](http://www.worldhello.net/doc/svn_hooks/svn_hooks.mm.htm "http://www.worldhello.net/doc/svn_hooks/svn_hooks.mm.htm")
2\. [http://blog.csdn.net/niu_gao/article/details/7502326](http://blog.csdn.net/niu_gao/article/details/7502326 "http://blog.csdn.net/niu_gao/article/details/7502326")
3\. [http://www.ibm.com/developerworks/cn/java/j-lo-apache-subversion/index.html](http://www.ibm.com/developerworks/cn/java/j-lo-apache-subversion/index.html "http://www.ibm.com/developerworks/cn/java/j-lo-apache-subversion/index.html")