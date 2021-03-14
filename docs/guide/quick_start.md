# 快速开始

使用该项目前，你需要检查你本地的开发环境，避免出现问题!

----------

## 极速启动
### 所需环境
这里列出项目所需的环境与相关安装教程
```
docker: 19.0+
安装教程: https://docs.docker.com/get-docker/

# 最新版的docker包含docker-compose，可通过命令确认是否已经安装：
docker-compose version
# 如未安装，务必通过官方获取对应的版本应用
```

!> 无需额外安装jdk,mysql等基础环境配置.  
Docker容器已经包含了～

```bash
# 命令行工具到达项目根目录文件下
cd /Users/garrett/Documents/project/sleep-monitoring-platform
# 阿里云容器镜像服务账号登录
docker login --username=[阿里云容器服务账号] registry.cn-beijing.aliyuncs.com
# 确认该目录下存在docker-compose.yml文件
docker-compose up
```

!> 由于国外服务器存在访问慢的现象,建议修改docker镜像为阿里云   
具体操作可跳转[官方文档](https://help.aliyun.com/document_detail/60750.html)阅读

------

## 开发环境启动
### 所需环境
> 集成开发工具建议使用IntelliJ IDEA（简称idea）

```
1、JDK：1.8
安装教程：https://www.runoob.com/java/java-environment-setup.html

2、Maven 3.0+  
安装教程：https://www.runoob.com/maven/maven-setup.html
# idea已集成，可跳过安装
```

## 开发准备
> #### TIP
>
> 在使用该系统前，你还需要做如下准备

1. 给 [idea](https://blog.csdn.net/wochunyang/article/details/81736354)
或者 [eclipse](https://blog.csdn.net/magi1201/article/details/85995987)安装 lombok 插件，
我们用它可以省略get，set 方法，可以使代码更简洁，具体查看 [lombok教程](https://yuanrengu.com/2020/baec5dff.html)
2. 你需要有 Spring boot 的基础，推荐教程 [Spring Boot 2.0 学习](https://github.com/ityouknow/spring-boot-examples)
project_from_git

## 运行项目
> #### TIP
> 
> 本项目是前后端分离项目,建议前后端都启动好,以便有良好的使用体验

### 后端

打开IDEA,直接导入Git项目

![IDEA导入Git项目](http://sleepclound.ltd:9000/docs/quick_start/perject_from_git.png)

输入项目地址

```
https://gitee.com/garrettxia/sleep-monitoring-platform.git
```
![IDEA的VSC](http://sleepclound.ltd:9000/docs/quick_start/idea_vsc.jpg)

修改配置，如果你的数据库账号或密码不是`sleep_account`和`sleep_account`，那么需要在 `application-dev.yml` 配置文件中做相应的修改
```yaml
# ...其他配置

spring:
  datasource:
    username: sleep_account
	# 明文密码
    password: sleep_account # 这里修改成你本地数据的密码
    driver-class-name: com.mysql.cj.jdbc.Driver # mysql8.0+ 以上版本需要加 cj,否则 com.mysql.jdbc.Driver即可
    url: jdbc:mysql://localhost:3306/sleep_sys_schema?characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC
    # 数据源其他配置 如果使用的是明文密码，以下这一段务必注释掉
    # druid:
    #   filter:
    #     config:
    #       enabled: true
    #   connection-properties: config.decrypt=true;config.decrypt.key=${public-key};druid.stat.mergeSql=true;druid.stat.slowSqlMillis=5000
```

修改项目环境变量(application.yml)
```yaml
# ....其他配置

spring:
  profiles:
    active: dev # dev-本地数据库 test-测试数据库
    
# ....其他配置
```

打开数据库，创建一个新库 `sleep_sys_schema`

!> 本项目已经配置`Flyway`数据库版本管理工具,如无定制化需求,可 **跳过** 下一步sql脚本手动导入

#### sql脚本手动导入
导入database文件夹中的初始化脚本库脚本 `sleep_sys_schema-mysql-8.0.2.sql`

![Navicat的sql文件执行](http://sleepclound.ltd:9000/docs/quick_start/navicat_sql.png)

待 Maven **导入完依赖** 点击`运行`即可

![IDEA 运行](http://sleepclound.ltd:9000/docs/quick_start/idea_run.png)  

### 后端运行常见问题

> #### 问题描述  
> 代码中使用注解@Slf4j的java类中，log变量统统报红

报错原因：   
IDEA2021 已内置 Lombok插件,或许是您的集成工具版本过低，为安装插件导致

解决方法：
1. File → settings → Plugins, 然后搜索“Lombok”

![IDEA Lombok 安装](http://sleepclound.ltd:9000/docs/quick_start/idea_lombok_install.png)

2. 重启IDEA即可
3. 运行项目