# 分页处理

> 在实际工作中，很进行列表查询的场景，我们往往都需要做两个步骤：  
> 1. 查询所需页数对应数据；  
> 2. 统计符合条件的数据总数；
> 而这，又会导致我们必然至少要写2个sql进行操作。这无形中增加了我们的工作量，
> 另外，当发生需要变动时，我们又需要同时改动这两个sql，否则必然导致结果的不一致

PageHelper 是一个简单易用的分页工具

### 安装

在 pom.xml 中添加如下依赖：
```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.3.0</version>
	<!-- 包含mybatis version 2.1.3 持久层框架-->
	<!-- pagehelper封装的springboot启动依赖默认加入了mybatis的springboot启动依赖，
		 如果项目中用到了mybatis启动依赖，可以选择将原有的依赖删除，或者在pagehelper
		 依赖中排除mybatis的启动依赖 -->
</dependency>
```

最新版本号可以从[官方文档](#office ':target=_self')查看

### 使用

阅读前请注意看[重要提示](#重要提示)

支持以下几种调用方式：
```java
//第一种，RowBounds方式的调用
List<User> list = sqlSession.selectList("x.y.selectIf", null, new RowBounds(0, 10));

//第二种，Mapper接口方式的调用，推荐这种使用方式。
PageHelper.startPage(1, 10);
List<User> list = userMapper.selectIf(1);

//第三种，Mapper接口方式的调用，推荐这种使用方式。
PageHelper.offsetPage(1, 10);
List<User> list = userMapper.selectIf(1);

//第四种，参数方法调用
//存在以下 Mapper 接口方法，你不需要在 xml 处理后两个参数
public interface CountryMapper {
    List<User> selectByPageNumSize(
            @Param("user") User user,
            @Param("pageNum") int pageNum, 
            @Param("pageSize") int pageSize);
}
//配置supportMethodsArguments=true
//在代码中直接调用：
List<User> list = userMapper.selectByPageNumSize(user, 1, 10);

//第五种，参数对象
//如果 pageNum 和 pageSize 存在于 User 对象中，只要参数有值，也会被分页
//有如下 User 对象
public class User {
    //其他fields
    //下面两个参数名和 params 配置的名字一致
    private Integer pageNum;
    private Integer pageSize;
}
//存在以下 Mapper 接口方法，你不需要在 xml 处理后两个参数
public interface CountryMapper {
    List<User> selectByPageNumSize(User user);
}
//当 user 中的 pageNum!= null && pageSize!= null 时，会自动分页
List<User> list = userMapper.selectByPageNumSize(user);

//第六种，ISelect 接口方式
//jdk6,7用法，创建接口
Page<User> page = PageHelper.startPage(1, 10).doSelectPage(new ISelect() {
    @Override
    public void doSelect() {
        userMapper.selectGroupBy();
    }
});
//jdk8 lambda用法
Page<User> page = PageHelper.startPage(1, 10).doSelectPage(()-> userMapper.selectGroupBy());

//也可以直接返回PageInfo，注意doSelectPageInfo方法和doSelectPage
pageInfo = PageHelper.startPage(1, 10).doSelectPageInfo(new ISelect() {
    @Override
    public void doSelect() {
        userMapper.selectGroupBy();
    }
});
//对应的lambda用法
pageInfo = PageHelper.startPage(1, 10).doSelectPageInfo(() -> userMapper.selectGroupBy());

//count查询，返回一个查询语句的count数
long total = PageHelper.count(new ISelect() {
    @Override
    public void doSelect() {
        userMapper.selectLike(user);
    }
});
//lambda
total = PageHelper.count(()->userMapper.selectLike(user));
```

在某种程度上而言,上述写法的确是符合PageHelper的使用规范 

> “ 在集合查询前使用PageHelper.startPage(pageNum,pageSize),并且中间不能穿插执行其他SQL”  

但是作为Developer的我们,往往只有在追求完美和极致的道路上才能够寻得突破和机遇; 以下是合理且规范的基本使用:

```java
public PageInfo<ResponseEntityDto> page(RequestParamDto param) {
 return PageHelper.startPage(param.getPageNum(), param.getPageSize())
     .doSelectPageInfo(() -> list(param))
} 

public List<ResponseEntityDto> list(RequestParamDto param) {
 return mapper.selectManySelective(param);
}
```

#### FAQ
---
1. 为什么要重新声明一个list函数?  

> 答：  
> 往往在很多实际业务应用场景中, 分页查询是基于大数据量的表格展示需求来进行的.
> 然而很多时候,譬如: 内部服务的互相调用,OpenAPI的提供. 甚至在某些前后端分离
> 联调的业务场景中,是同样需要一个非分页集合查询接口来提供服务的. 另外,暂时以
> 上因素抛开不谈,我们可以根据上述写法来定义和规范某些东西  
> 
> 譬如: 分页和集合查询的分离和解耦(解耦详情请看进阶使用), 分页请求的请求和响
> 应与实际业务参数的分离(详情请看进阶使用)等等…

2. doSelectPageInfo是什么?  

> 答：  
> doSelectPageInfo是PageHelper.startPage()函数返回的默认Page实例内置的函数  
> 该函数可以用以Lambda的形式通过额外的Function来进行查询而不需要再进行多余的
> PageInfo与List转换,而doSelectPageInfo的参数则是PageHelper内置的Function(ISelect)接口用以达到转换PageInfo的目的

3. 这种写法的代码量看起来不少反多?

> 答：  
> 正如同①中所描述的,就代码量而言,确实没有更进一步的简化,但是再某些业务场景中,在已具有list函数接口的情况下,是一种更直观的优化

### 重要提示

#### `PageHelper.startPage`方法重要提示

只有紧跟在`PageHelper.startPage`方法后的**第一个**Mybatis的**查询（Select）**方法会被分页。

#### 请不要配置多个分页插件
请不要在系统中配置多个分页插件(使用Spring时,`mybatis-config.xml`和`Spring<bean>`配置方式，请选择其中一种，不要同时配置多个分页插件)！

#### 分页插件不支持带有`for update`语句的分页
对于带有`for update`的sql，会抛出运行时异常，对于这样的sql建议手动分页，毕竟这样的sql需要重视。

#### 分页插件不支持嵌套结果映射
由于嵌套结果方式会导致结果集被折叠，因此分页查询的结果在折叠后总数会减少，所以无法保证分页结果数量正确

## 相关资料
<span id="office">官方文档</span>：[https://github.com/pagehelper/pagehelper-spring-boot](https://github.com/pagehelper/pagehelper-spring-boot)  
PageHelper 规范进阶：[https://zhuanlan.zhihu.com/p/268312718](https://zhuanlan.zhihu.com/p/268312718)