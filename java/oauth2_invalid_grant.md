# Oauth2请求token报错invalid_grant

使用 Oauth2 使用密码模式请求 `/oauth/token` 时，返回结果为:
```json
{
    "error": "invalid_grant",
    "error_description": "Bad credentials"
}
```
查找原因是否是数据库中`oauth_client_details`表中的`client_secret`没加密，又尝试了重新使用`BCryptPasswordEncoder`加密了下，结果还是报错。

结果是密码输错了。。密码是123456，我输入了123。。。
