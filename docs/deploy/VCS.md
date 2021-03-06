# 自建版本控制系统

> 一款极易搭建的自助Git服务，特别轻量级，安装方便

## Gogs简介
Gogs是一款极易搭建的自助Git服务，使用Go语言开发，只要Go语言支持的平台它都支持，包括Linux、Mac OS X、Windows以及ARM平台。Gogs对系统硬件要求极低，你甚至可以在树莓派上搭建它。

项目地址：[https://github.com/gogs/gogs](https://github.com/gogs/gogs)

## 安装
> Gogs在Docker环境下的安装非常简单，只需要两个命令即可，推荐使用该方式来进行安装。

* 首先我们需要先下载Gogs的Docker镜像:

```bash
docker pull gogs/gogs
```

* 下载完成后使用`docker run`命令即可运行服务

```bash
docker run -p 10022:22 -p 10080:3000 --name=gogs \
-v /mydata/gogs:/data  \
-d gogs/gogs
```

!> 注意  
**10022**对应的是Gogs的`SSH服务端口`  
**10080**对应的使用Gogs的`HTTP服务端口`  
容器的`数据目录`挂载在宿主机的/mydata/gogs目录下  
这样能够有效避免容器数据的丢失

## 配置

* 安装完成后，我们第一次访问Gogs服务会显示一个设置页面,访问地址：[http://127.0.0.1:10080](http://127.0.0.1:10080)
* 数据库设置，这里我们直接使用内置的`SQLite3`数据库即可，使用其他的需要自行搭建数据库；

## 使用
### 注册

* 配置好以后会直接跳转到登录界面，首先注册一个帐户；
* 注册完成后，登录即可进入控制面板页面
* 剩下的一些操作和gitee或github基本无差别

## Gogs VS GitLab

> 下面对比下Gogs和Gitlab在安装使用过程中的优缺点，仅代表个人观点。

| 比较方面 |	Gogs |	Gitlab |
|--------|-------|---------|
|Docker镜像大小	|44MB	|836MB|
|启动速度	很快，|几秒	|很慢，机器配置不好要10分钟|
|配置要求	|很低，树莓派都可以 |	很高，吃内存，吃CPU|
|访问速度	|够快|	机器配置好也还可以|
|功能	|功能较少	|功能很丰富|