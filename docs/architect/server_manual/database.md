# 数据库版本控制

> ### 摘要
> 当我们的应用升级时往往会伴随着数据库表结构的升级，此时就需要迁移数据库的表结构。  
> 一般我们会使用工具或者脚本来实现，手动操作毕竟有一定风险，我们期望应用启动时自动升级数据库表结构  
> **Flyway**正是这么一款工具，通过Flyway和SpringBoot结合使用，在应用启动时就可以自动升级数据库表结构

## Flyway简介
-----
Flyway是一款数据库迁移工具，它让数据库迁移变得更加简单。它能像Git一样对数据库进行版本控制，支持命令行工具、Maven插件、第三方工具（比如SpringBoot）等多种使用方式。

Flyway具有如下特点：

* 简单：使用和学习简单，通过不同版本的SQL脚本实现数据库迁移
* 功能强大：支持多种数据库，拥有大量的第三方工具，支持CI/DI

## 概要
### 工作原理

##### 初始化
当你最开始将FlyWay指向一个空数据库时。

![工作原理_空数据库](http://sleepclound.ltd:9000/docs/server_manual/flyway_work1.png)

它会试着去查找schema历史表，如果此时数据库是空的，则FlyWAY会自己创建一张历史表 。
你现在就有了个仅包含空表`flyway_schema_history`（默认）的数据库

![工作原理_增加跟踪表](http://sleepclound.ltd:9000/docs/server_manual/flyway_work2.png)

这张表将会追踪数据库的状态，随后flyway会立即扫描应用的文件系统或类路径(classpath)以用于迁移，Sql或Java文件都可以支持迁移。

迁移会按照版本号进行排序并依次执行：

![工作原理_迁移](http://sleepclound.ltd:9000/docs/server_manual/flyway_work3.png)

随着每次迁移被执行，`schema_history`历史表会依此更新记录

##### 增量迁移

Flyway会再次扫描应用的文件系统和类路径，迁移依据历史表检查，如果版本号低于或等于当前版本号，则忽略迁移操作。

剩下的迁移是增量迁移(pending migrations)，准备就绪，但未执行

![工作原理_迁移](http://sleepclound.ltd:9000/docs/server_manual/flyway_work4.png)

它们会按照版本号排序并依次执行：

![工作原理_迁移](http://sleepclound.ltd:9000/docs/server_manual/flyway_work5.png)

`schema_history`历史表会依此更新记录

### 脚本命名规范
在创建Flyway的SQL脚本时，有些命名规范需要遵守，这些命名规范决定了Flyway执行脚本的顺序和方式

![命名规范](http://sleepclound.ltd:9000/docs/server_manual/flyway_model.png)

为了能被Flyway正确执行，SQL迁移脚本需要遵循如下规范：
> * Prefix（前缀）：V表示有版本号的数据库迁移，U表示一些数据库版本的回滚，R表示可重复执行的数据库迁移；
> * Version（版本号）：Flyway会按照版本号的大小顺序来执行数据库迁移脚本；
> * Separator（分隔符）：命名时使用双下划线分隔符；
> * Description（描述）：用于描述该迁移脚本的具体操作说明；
> * Suffix（后缀）：表示.sql文件
### 相关命令
* migrate：数据库迁移命令，会根据设置好的SQL脚本直接将数据库表升级至最新版本。
* clean：删除数据库中所有的表，千万别在生产环境上使用。
* info：打印所有关于数据库迁移的详细信息和状态信息。
* validate：验证数据库迁移是否可用。
* undo：对数据库迁移进行回滚操作。
* baseline：以现有数据库为基准，创建flyway_schema_history表，大于基准版本的数据库迁移才会被应用。
* repair：修复flyway_schema_history表

## 结合SpringBoot使用

* 首先在pom.xml中添加Flyway相关依赖
```xml
<!--Flyway相关依赖-->
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>
```

* 修改对应的配置文件`application-*.yml`,对数据源和Flyway进行配置
```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/sleep_sys_schema?characterEncoding=UTF-8&useSSL=false&serverTimezone=UTC
    username: sleep_account # 你的数据库账号
    password: sleep_account # 你的数据库密码
  flyway:
    # 启用Flyway功能，默认开启，可不写
    enabled: true
    # 禁用Flyway的clean命令，使用clean命令会删除schema下的所有表
    clean-disabled: true
    # 设置Flyway的SQL脚本路径
    locations: classpath:db/migration
    # 设置版本信息控制表名称，默认flyway_schema_history
    table: flyway_schema_history
    # 在执行migrate命令时需要有flyway_schema_history表，通过baseline命令可以生成该表
    baseline-on-migrate: true
    # 指定baseline版本号，低于该版本的SQL脚本在migrate是不会执行
    baseline-version: 1
    # 设置字符编码
    encoding: UTF-8
    # 不允许不按顺序迁移
    out-of-order: false
    # 设置Flyway管控的schema，不设置的话为datasourcel.url中指定的schema
    schemas: flyway
    # 执行migrate时开启校验
    validate-on-migrate: true
```
* 最后直接运行SpringBoot应用，即可自动创建好对应的数据库，控制台会输出如下信息
```
......
2021-03-14 14:18:21.118  INFO 3748 --- [           main] o.s.s.concurrent.ThreadPoolTaskExecutor  : Initializing ExecutorService 'applicationTaskExecutor'
2021-03-14 14:18:21.296  INFO 3748 --- [           main] o.f.c.internal.license.VersionPrinter    : Flyway Community Edition 7.1.1 by Redgate
2021-03-14 14:18:21.544  INFO 3748 --- [           main] o.f.c.i.database.base.DatabaseType       : Database: jdbc:mysql://localhost:3306/sleep_sys_schema (MySQL 8.0)
2021-03-14 14:18:21.678  INFO 3748 --- [           main] o.f.core.internal.command.DbValidate     : Successfully validated 1 migration (execution time 00:00.040s)
2021-03-14 14:18:21.692  INFO 3748 --- [           main] o.f.c.i.s.JdbcTableSchemaHistory         : Creating Schema History table `sleep_sys_schema`.`flyway_schema_history` ...
2021-03-14 14:18:21.793  INFO 3748 --- [           main] o.f.core.internal.command.DbMigrate      : Current version of schema `sleep_sys_schema`: << Empty Schema >>
2021-03-14 14:18:21.814  INFO 3748 --- [           main] o.f.core.internal.command.DbMigrate      : Migrating schema `sleep_sys_schema` to version "1 - Initial Setup"
2021-03-14 14:18:22.469  INFO 3748 --- [           main] o.f.core.internal.command.DbMigrate      : Successfully applied 1 migration to schema `sleep_sys_schema` (execution time 00:00.688s)
......
```
## 总结
对比手动升级数据库表结构，使用Flyway自动升级更有优势。
使用Flyway可以在我们升级应用时同时升级数据库，也可以应用于数据库初始化
由于社区版本目前不支持数据库回滚，升级前做好备份是很有必要的