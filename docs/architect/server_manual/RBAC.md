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

#### 自定义**登录失败**异常信息
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

##### 新增CustomOauthExceptionSerializer

```java
public class CustomOauthExceptionSerializer extends StdSerializer<CustomOauthException> {
    public CustomOauthExceptionSerializer() {
        super(CustomOauthException.class);
    }

    @Override
    public void serialize(CustomOauthException value, JsonGenerator gen, SerializerProvider provider) throws IOException {
        HttpServletRequest request = ((ServletRequestAttributes) RequestContextHolder.getRequestAttributes()).getRequest();

        gen.writeStartObject();
		gen.writeStringField("error_code", String.valueOf(e.getHttpErrorCode()));
		gen.writeStringField("message", e.getMessage());
		gen.writeStringField("timestamp", DateUtil.formatDateTime(new Date()));
		if (e.getAdditionalInformation()!=null) {
			for (Map.Entry<String, String> entry : e.getAdditionalInformation().entrySet()) {
				String key = entry.getKey();
				String add = entry.getValue();
				gen.writeStringField(key, add);
			}
		}
		gen.writeEndObject();
    }
}
```

##### 添加OauthWebResponseException

添加`OauthWebResponseException`，登录发生异常时指定`exceptionTranslator`
```java
import org.springframework.http.ResponseEntity;
import org.springframework.security.oauth2.common.exceptions.OAuth2Exception;
import org.springframework.security.oauth2.provider.error.DefaultWebResponseExceptionTranslator;
import org.springframework.stereotype.Component;

@Component("oauthWebResponseException")
public class OauthWebResponseException extends DefaultWebResponseExceptionTranslator {

    @Override
    public ResponseEntity<OAuth2Exception> translate(Exception e) throws Exception {
        OAuth2Exception oAuth2Exception = (OAuth2Exception) e;
        return ResponseEntity
                .status(oAuth2Exception.getHttpErrorCode())
                .body(new OauthException(oAuth2Exception.getMessage()));
    }
}
```

##### 修改授权服务器配置

```java
@Configuration
@EnableAuthorizationServer
@RequiredArgsConstructor
public class AuthorizationServerConfig extends AuthorizationServerConfigurerAdapter {

    private final AuthenticationManager authenticationManager;
    private final UserDetailServiceImpl userDetailService;
    private final OauthWebResponseException oauthWebResponseException;
    @Qualifier("jwtTokenStore")
    private final TokenStore tokenStore;
    private final JwtAccessTokenConverter jwtAccessTokenConverter;

    @Override
    public void configure(AuthorizationServerEndpointsConfigurer endpoints) {
        endpoints.authenticationManager(authenticationManager)
                .userDetailsService(userDetailService)
                // 自定义令牌存储策略 - accessToken转成JWT token
                .tokenStore(tokenStore)
				/** 加入自定义异常处理 */
                .exceptionTranslator(oauthWebResponseException)
                .accessTokenConverter(jwtAccessTokenConverter);
    }
	
	// ...其他配置
}
```


 {docsify-updated}