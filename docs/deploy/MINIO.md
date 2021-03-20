# 用MINIO自建存储服务

> 对象存储服务可以用来存储各类文件，mall项目中的图片存储采用的是OSS，今天我们来讲下如何自己搭建一个对象存储服务来存储图片。

## MINIO 简介

MinIO 是一款基于Go语言的高性能对象存储服务，在Github上已有19K+Star。它采用了Apache License v2.0开源协议，非常适合于存储大容量非结构化的数据，例如图片、视频、日志文件、备份数据和容器/虚拟机镜像等。 本文将使用 MinIO 来自建一个对象存储服务用于存储图片。

## 安装及部署

> MinIO的安装方式有很多，这里我们使用它在Docker环境下的安装方式。

* 下载MinIO的Docker镜像：
* 在Docker容器中运行MinIO，这里我们将MiniIO的数据和配置文件夹挂在到宿主机上
* 运行成功后，访问该地址来登录并使用MinIO，默认Access Key和Secret都是`minioadmin`
```bash
docker run -p 9000:9000 --name minio \
-d --restart=always \
-e "MINIO_ACCESS_KEY=account" \
-e "MINIO_SECRET_KEY=password" \
-v /usr/minio_data:/data \
-v /usr/minio_config:/root/.minio \
minio/minio server /data
```
http://127.0.0.1:9000