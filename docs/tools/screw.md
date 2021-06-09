# 数据库文档生成

## 简介

在企业级开发中，我们经常会有编写数据库表结构文档的时间付出，`screw`开源工具的出现解决了这一需求空缺，可以高效生成数据库文档

> Screw 官网地址
> https://toscode.gitee.com/leshalv/screw

![screw工具生成效果](http://sleepclound.ltd:9000/docs/tools/screw_demo.png)

### 数据库支持
* MySQL
* MariaDB
* TIDB
* Oracle
* SqlServer
* PostgreSQL
* Cache DB

## 快速开始

### 配置

#### 引入依赖

```xml
 <dependency>
    <groupId>cn.smallbun.screw</groupId>
    <artifactId>screw-core</artifactId>
    <version>${lastVersion}</version>
 </dependency>
 
 <!-- ...其他依赖 -->
 <!-- 数据库 -->
 <!-- 连接池管理等 -->
 
```

#### 启动方式
`screw`有两种执行方式，一种是`代码`执行，另一种是`pom`文件配置插件的形式执行。

* 编写代码

```java
@DisplayName("开源自动生成工具")
public class ScrewApplicationTests {

	@Test
    @DisplayName("生成数据库文档")
    void contextLoads() {
        // 1.0.5版本以上内置数据源
        HikariConfig hikariConfig = new HikariConfig();
		
		/* 
		 * 常规数据库链接配置
		 */
		
		// 数据库类型
        hikariConfig.setDriverClassName("com.mysql.cj.jdbc.Driver");
		// 数据库连接
        hikariConfig.setJdbcUrl("jdbc:mysql://localhost:3306/数据库名称?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=true&serverTimezone=GMT%2B8&allowMultiQueries=true&useInformationSchema=true");

        hikariConfig.setUsername("数据库用户名");
        hikariConfig.setPassword("数据库密码");
		
        //设置能够获取tables remarks信息
        hikariConfig.addDataSourceProperty("useInformationSchema", "true");
        hikariConfig.setMinimumIdle(2);
        hikariConfig.setMaximumPoolSize(5);
		
        DataSource dataSource = new HikariDataSource(hikariConfig);

        // 生成文件配置
        EngineConfig engineConfig = EngineConfig.builder()
                // 生成文件路径，自己mac本地的地址，这里需要自己更换下路径
                .fileOutputDir("/Users")
                // 打开目录
                .openOutputDir(true)
				// 生成的文件名
                .fileName("数据库_说明文档")
                // 文件类型
                .fileType(EngineFileType.WORD)
                // 生成模板实现
                .produceType(EngineTemplateType.freemarker).build();
        // 生成文档配置（包含以下自定义版本号、描述等配置连接）
        Configuration config = Configuration.builder()
                .version("1.0.3")
                .description("生成文档信息描述")
                .dataSource(dataSource)
                .engineConfig(engineConfig)
                .produceConfig(getProcessConfig())
                .build();
        // 执行生成
        new DocumentationExecute(config).execute();
    }

    /**
     * 配置想要生成的表+ 配置想要忽略的表
     *
     * @return 生成表配置
     */
    public static ProcessConfig getProcessConfig() {
        // 忽略表名
        List<String> ignoreTableName = Arrays.asList("a", "test_group");
        // 忽略表前缀，如忽略a开头的数据库表
        List<String> ignorePrefix = Arrays.asList("a", "t");
        // 忽略表后缀
        List<String> ignoreSuffix = Arrays.asList("_test", "czb_");
        return ProcessConfig.builder()
                //根据名称指定表生成
                .designatedTableName(new ArrayList<>())
                //根据表前缀生成
                .designatedTablePrefix(new ArrayList<>())
                //根据表后缀生成
                .designatedTableSuffix(new ArrayList<>())
                //忽略表名
                .ignoreTableName(ignoreTableName)
                //忽略表前缀
                .ignoreTablePrefix(ignorePrefix)
                //忽略表后缀
                .ignoreTableSuffix(ignoreSuffix)
                .build();
    }
}
```

* maven插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>cn.smallbun.screw</groupId>
            <artifactId>screw-maven-plugin</artifactId>
            <version>${lastVersion}</version>
            <dependencies>
                <!-- HikariCP -->
                <dependency>
                    <groupId>com.zaxxer</groupId>
                    <artifactId>HikariCP</artifactId>
                    <version>3.4.5</version>
                </dependency>
                <!--mysql driver-->
                <dependency>
                    <groupId>mysql</groupId>
                    <artifactId>mysql-connector-java</artifactId>
                    <version>8.0.20</version>
                </dependency>
            </dependencies>
            <configuration>
                <!--username-->
                <username>root</username>
                <!--password-->
                <password>password</password>
                <!--driver-->
                <driverClassName>com.mysql.cj.jdbc.Driver</driverClassName>
                <!--jdbc url-->
                <jdbcUrl>jdbc:mysql://127.0.0.1:3306/xxxx</jdbcUrl>
                <!--生成文件类型-->
                <fileType>HTML</fileType>
                <!--打开文件输出目录-->
                <openOutputDir>false</openOutputDir>
                <!--生成模板-->
                <produceType>freemarker</produceType>
                <!--文档名称 为空时:将采用[数据库名称-描述-版本号]作为文档名称-->
                <fileName>测试文档名称</fileName>
                <!--描述-->
                <description>数据库文档生成</description>
                <!--版本-->
                <version>${project.version}</version>
                <!--标题-->
                <title>数据库文档</title>
            </configuration>
            <executions>
                <execution>
                    <phase>compile</phase>
                    <goals>
                        <goal>run</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```


配置完以后在 `maven project`->`screw` 双击执行ok。

![maven插件](https://img-blog.csdnimg.cn/20200802211843999.png)