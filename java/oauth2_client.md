# oauth2下不同服务的client是否相同？

首先oauth2的client完全是存储在`oauth_client_details`表中的。如果想注册新的服务到Oauth2的认证中心，那么必须将该服务的client添加到这张表中。具体字段含义可见Oauth2章节。
```yml
security:
  oauth2:
    client:
      client-id: orderclient
      client-secret: secret
      access-token-uri: http://localhost:8080/oauth/token
      user-authorization-uri: http://localhost:8080/oauth/authorize
    resource:
      token-info-uri: http://localhost:8080/oauth/check_token 
```
如上述，添加了订单服务，它的 client_id 和 client_secret 必须和数据库中的对应。如不对应，则生成的token无法验证。

假设又添加了用户服务，它的 client_id 和 client_secret 如下：
```yml
security:
  oauth2:
    client:
      client-id: userclient
      client-secret: secret
      access-token-uri: http://localhost:8080/oauth/token
      user-authorization-uri: http://localhost:8080/oauth/authorize
    resource:
      token-info-uri: http://localhost:8080/oauth/check_token 
```

现在`oauth_client_details`中有两条记录。我们用用户服务的client和secret去认证中心请求token：
```json
{
    "access_token": "4d993a02-3556-42ee-9dca-94e625f429e1",
    "token_type": "bearer",
    "expires_in": 43199,
    "scope": "read write"
}
```

然后用该token去请求订单服务，结果成功返回数据。

因此，得出结论，只要微服务中配置的client和secret和数据库中的相对应，那么就可以通过token的检查。所以说，服务下的client_id是不同的，随便用哪一个服务去获取token，它在全局都是可用的。
