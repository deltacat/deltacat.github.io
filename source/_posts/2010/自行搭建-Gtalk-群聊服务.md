title: "自行搭建 Gtalk 群聊服务"
date: 2010-11-25 16:38:26
tags: [建站]
id: 110
categories: 建站心得
---

GTalk 的群聊一直是一个软肋，虽然在线版（GMail内置）和Lab版支持，但是毕竟不方便，而且有很多的限制。于是就有了很多群聊服务的出现。常见的服务有 [Chatterous](http://www.chatterous.com), [Partychapp](http://partychapp.appspot.com/) 等。使用都很方便，注册帐号就行了。

如果想更多得控制，或者担心聊天信息泄漏给第三方，还可以自行搭建聊天群组机器人。最常见的就是 Conference Bot 这个机器人了。网上也有很多资料。

出于种种原因，ConfBot 我这里使用一直有问题。最近Chatterous总是抽风，于是重新兴起自建机器人的念头。经过搜索，发现一个方案，使用 GTalkGroups + GAE. GAE 是全天候服务的，解决了服务器问题。GTalkGroups 本身也比 Confernce Bot 简单。

下面是我的步骤。

<!--more-->

1\. 下载工具及程序：[GAE SDK](http://code.google.com/appengine/downloads.html)，[Python](http://www.python.org/), [GTalkGroups](http://gtalkgroups.googlecode.com/)

2\. 安装 Python, 安装 GAE。这一步参考第一步中 GAE SDK 下载资源中的文档。

3\. 创建 GAE Application。参考 GAE 文档。记下这一步创建的 App ID 备用。

4\. 修改 GTalkGroups 脚本中的必要字段：
> app.yaml 中，第一行的APPID修改为自己的。
> 
> 
> app.yaml 中，url:/chatbridge 值改为随机字串。比如 url:/chatbridge123456
> 
> 
> m2ggg_config 中，root_gmail 的值修改为你自己的邮箱，这个邮箱将成为管理员
> 
> 
> m2ggg_config 中，protect_key1,protect_key2\. 改成两个随机数字串.
5\. 上传应用，进入后台检查服务状态。如果你所在的网络是内部网，比如公司，学校，那么需要设置环境变量，将你使用的代理设置给 GAE SDK。具体到 Windows 环境就是如下两个命令：
> C:\&gt;set HTTP_PROXY=http://xxx.xxx.xx.xxx:&lt;port&gt;
> 
> 
> C:\&gt;set HTTPS_PROXY=https://xxx.xxx.xx.xxx:&lt;port&gt;
6\. 上传完成后，进入GAE后台，进入Datastore Indexes 一栏，检查服务状态，全部为 Serving 就可以使用了。我看到的文档说可能要等数小时，实际上我这边几乎实时完成。

7\. 需要用户在GTalk中添加你的机器人的ID为聊天好友： &lt;appid&gt;@appspot.com 。然后管理员使用 //add 或 //allok 命令通过申请。到此位置就可以开始使用了。作为管理员，你需要第一个加入，输入 //add, //admin 来加入自己作为第一个用户并提升为管理员。 <!--more-->

**附：GTalkGroups 机器人命令：**
> UserCmd (普通用户指令)
> 
> //online
> 
> 用途：显示在线人员名单
> 
> //nick
> 
> 用途：设置昵称
> 
> 示例：//nick 你好
> 
> 说明：如果群里有其他人使用相同昵称则该昵称不能设置
> 
> //setid
> 
> 用途：设置FID
> 
> 示例：//setid hello
> 
> 说明：如果群里有其他人使用相同FID则该昵称不能设置。FID只能设置一次，重设无效。
> 
> //block
> 
> 用途：屏蔽某人的消息
> 
> 示例：//block hello
> 
> 说明：后面所跟的参数为FID。上面示例所的执行结果是屏蔽了FID为hello的用户的消息。
> 
> //unblock
> 
> 用途：取消屏蔽某人的消息
> 
> 示例：//unblock hello
> 
> 说明：后面所跟的参数为FID。上面示例所的执行结果是取消屏蔽FID为hello的用户的消息。
> 
> //m
> 
> 用途：给某人发送私密消息
> 
> 示例：//m hello helloworld
> 
> 说明：后面所跟的参数为FID和内容。上面示例所的执行结果是把私密消息helloworld发送到FID为hello的这个用户。发送成功会显示OK的。目前不能发送消息给形如@***||*** 这样的外连接用户。
> 
> //showinfo
> 
> 用途：显示某人的基本信息
> 
> 示例：//showinfo hello
> 
> 说明：后面所跟的参数为FID。上面示例所的执行结果是输出FID为hello的用户的基本资料。
> 
> //stop
> 
> 用途：马上停止接收群内消息，直到若干秒后恢复
> 
> 示例：//stop 3600
> 
> 说明：后面所跟的参数为秒数。上面示例所的执行结果是在一小时（3600秒）内不接收群内消息。如果重复输入此命令，时间不会叠加，以最后的一次执行为准。如果执行//stop -1，则取消屏蔽，马上显示消息。
> 
> //continue
> 
> 用途：停止屏蔽群内消息，作用等同于//stop -1
> 
> //help
> 
> 用途：输出帮助内容——指令的简单说明。
> 
> 
> AdminCmd（管理员指令）
> 
> //whois
> 
> 用途：查看某人消息
> 
> 示例：//whois hello
> 
> 说明：参数必须为FID。使用whois可以获取用户的电邮，以供后面的其他指令作为参数用。
> 
> //blacklist
> 
> 用途：将某人加入群黑名单
> 
> 示例：//blacklist somebody.you.want.to.disable@gmail.com
> 
> 说明：参数为用户email
> 
> //pass
> 
> 用途：把某人从黑名单上移除
> 
> 示例：//pass remove.from.blacklist@gmail.com
> 
> 说明：参数为用户email
> 
> //admin
> 
> 用途：设置某人为管理员
> 
> 示例：//admin admin.of.group@gmail.com
> 
> 说明：参数为用户email
> 
> //unadmin
> 
> 用途：解除某人的管理员身份
> 
> 示例：//unadmin not.a.admin.of.group@gmail.com
> 
> 说明：参数为用户email
> 
> //add
> 
> 用途：增加一个成员
> 
> 示例：//add new.member@gmail.com
> 
> 说明：填写email，若email为已有成员，则执行指令后用户资料会被重新初始化。
> 
> //addrouterecv
> 
> 用途：创建HTTP消息接收通道。
> 
> 示例：//addrouterecv mychannel2 hello.mypassword2 mychannel1
> 
> 说明：后跟3个参数，分别是名字、密码和回路禁止。示例的执行结果是：创建一个外挂消息接收点，名字叫mychannel2，密码是hello.mypassword2，收到的消息不用重新转发给mychannel1
> 
> //addroutesend
> 
> 用途：创建HTTP消息发送通道
> 
> 示例：//addroutesend mychannel1 hello.mypassword1 http://some.of.mychannel1.some.appspot.program
> 
> 说明：后跟3个参数，分别是名字、密码和接收消息的地址。示例的执行结果是：建一个外挂消息发送器，名字叫mychannel1，密码是 hello.mypassword1，消息发送到http://some.of.mychannel1.some.appspot.program上。若在服务器http://some.of.mychannel1.some.appspot.program创建同样的名字和密码（mychannel1和 hello.mypassword1）的接收点，并禁止回路mychannel2，则对方会收到此聊天群发出的消息，并且不回送。
> 
> //allok
> 
> 用途：通过所有没有进入群的用户
> 
> //listuser
> 
> 保留，原意是提供用户列表，但是GAE后台能显示，所以没有加上
**参考资料：**

1\. [Gtalk群的使用](http://hi.baidu.com/democratleo/blog/item/fd594623451dcbfed7cae24a.html)

2\. [命令列表](http://io43.com/io43.GtalkGroup/cmds/)

3\. [Google Application Engine Resource.](http://code.google.com/appengine/downloads.html)