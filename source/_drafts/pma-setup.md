title: phpMyAdmin 安全设置
s: pma-setup
categories: 
- 软件开发
tags: 
- 软件开发
- nodejs
- macOS
date: 2016-07-25 12:00:00

---

## 安全调整主要有几个方面

- 配置访问授权方式
- 默认访问路径修改
- 配置安全连接

目前我们采用的是 nginx + apache 的服务，下文以此为例。

## 访问授权方式

## 默认访问路径

### 首先，禁用服务器直接 IP 地址访问。

在 nginx 配置文件中增加 

```
server {
	# 监听默认端口默认主机
	listen 80 default; 
	# 拒绝访问
	return 403;
	# 或者重定向至主页
	# rewrite ^(.*) http://www.xxoo.com permanent; 
}
```

### 修改apache 配置文件 phpMyAdmin.conf

默认的 phpMyAdmin.conf 设置 phpmyadmin 通过apache任意虚拟主机的虚拟目录（别名）进入。这样很容易被扫描式攻击探测到。

简单的解决方法是给 pma 设置一个独立的主机，非默认端口，并改变默认别名。比如只允许通过这种形式访问：

```
abc.mydomian.com:1234/here
```

## 安全连接启用


