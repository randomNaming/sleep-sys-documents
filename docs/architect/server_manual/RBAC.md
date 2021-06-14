# 权限控制

> 本系统权限控制采用 `RBAC` 思想  
> 简单地说，一个用户拥有若干角色，每一个角色拥有若干个菜单，菜单中存在菜单权限与按钮权限  
> 这样，就构造成“用户-角色-菜单” 的授权模型  
> 在这种模型中，用户与角色、角色与菜单之间构成了多对多的关系，如下图

![RBAC表结构图](http://81.69.99.37:9000/docs/server_manual/RBAC.jpg)

## 安全框架

本系统安全框架使用的是 Spring Cloud Security + Spring Cloud Oauth2， 访问后端接口需在请求头中携带token进行访问，请求头格式如下：
```
# Authorization: Bearer 登录时返回的token
Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbiIsImV4cCI6MTU1ODk2NzY0OSwiaWF0IjoxNTU4OTQ2MDQ5fQ.jsJvqHa1tKbJazG0p9kq5J2tT7zAk5B6N_CspdOAQLWgEICStkMmvLE-qapFTtWnnDUPAjqmsmtPFSWYaH5LtA
```

## 数据交互
## 接口放行

## 自定义异常

在使用`Spring Security Oauth2`登录和鉴权失败时，默认返回的异常信息如下

```json
{
  "error": "unauthorized",
  "error_description": "Full authentication is required to access this resource"
}
```

##### 新增CustomOauthException

添加自定义异常类，指定`json`序列化方式

```java
@JsonSerialize(using = CustomOauthExceptionSerializer.class)
public class OauthException extends OAuth2Exception {

    public OauthException(String msg) {
        super(msg);
    }
}
```



 {docsify-updated}