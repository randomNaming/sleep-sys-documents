# RESETful API 设计规范

> 为了避免歧义，对以下 **助动词** 进行说明:
> * `必须` : 绝对，严格遵循，请照做，无条件遵守；
> * `一定不可` : 禁令，严令禁止；
> * `应该` : 强烈建议这样做，但是不强求；
> * `不该` : 强烈不建议这样做，但是不强求；
> * `可以` 和 `可选` : 选择性高一点，在这个文档内，此词语使用较少；

### 协议

在通过 `API` 于后端服务通信的过程中,`应该` 使用 `HTTPS` 协议，部署 `SSL` 证书,这样接口传递的数据的安全性才能获得一定的保障

### API ROOT URL

`API` 的根入口点应尽可能保持足够简单：
* api.example.com/*
* example.com/api/*

> 如果你的应用很庞大或者你预计它将会变的很庞大，那 `应该` 将 `API` 放到子域下。这种做法可以保持某些规模化上的灵活性

### 返回的报文 Response

所有的 `API` 响应，`必须` 遵守 `HTTP` 设计规范，`必须` 选择合适的 `HTTP` 状态码。`一定不可` 所有接口都返回状态码为 `200` 的 `HTTP` 响应，如：

```
HTTP/1.1 200 ok
Content-Type: application/json
Server: example.com

{
    "code": 0,
    "msg": "success",
    "data": {
        "username": "username"
    }
}
```

或

```
HTTP/1.1 200 ok
Content-Type: application/json
Server: example.com

{
    "code": -1,
    "msg": "该活动不存在",
}
```

#### 200 OK

正确示例：

额外的媒体信息
```
{
    "data": [
        {
            "id": 1,
            "avatar": "https://lorempixel.com/640/480/?32556",
            "nickname": "fwest",
            "last_logined_time": "2018-05-29 04:56:43",
            "has_registed": true
        },
        {
            "id": 2,
            "avatar": "https://lorempixel.com/640/480/?86144",
            "nickname": "zschowalter",
            "last_logined_time": "2018-06-16 15:18:34",
            "has_registed": true
        }
    ],
    "meta": {
        "pagination": {
            "total": 101,
            "count": 2,
            "per_page": 2,
            "current_page": 1,
            "total_pages": 51,
            "links": {
                "next": "http://api.example.com?page=2"
            }
        }
    }
}
```

> 其中，分页和其他额外的媒体信息，必须放到 `meta` 字段中。

### 版本

所有的 `API` 必须保持向后兼容，你 `必须` 在引入新版本 `API` 的同时确保旧版本 `API` 仍然可用。 `应该` 为其提供版本支持



## 参考资料

* REST API风格案例：[https://leancloud.cn/docs/rest_api.html](https://leancloud.cn/docs/rest_api.html)