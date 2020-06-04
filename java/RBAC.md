# 基于RBAC的权限设计

![RBAC](https://blog-gitbook.oss-cn-beijing.aliyuncs.com/java/rbac.jpg)

关于RBAC的权限设计一般设计为以上7张表。如果只做后端api的权限，则不需要 menu 表。

后端权限在表中存储但是如何对应到代码的权限中？

spring security 配置为
```java
/**
 * 客户端配置
 */
@Configuration
@EnableResourceServer
public class ResourceServerConfiguration extends ResourceServerConfigurerAdapter{
	
	@Autowired
	private SysPermissionService sysPermissionService;

    @Override
    public void configure(HttpSecurity http) throws Exception{
    	//基础配置    	
        http
        .csrf().disable()
        .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);// 调整为让 Spring Security 不创建和使用 session
    	
        //从数据库中读取配置的api路径，动态拦截。
    	ExpressionUrlAuthorizationConfigurer<HttpSecurity>.ExpressionInterceptUrlRegistry authorizeRequests = http.authorizeRequests();
    	List<SysPermission> list = sysPermissionService.list();
    	//这里是配置所有拦截，拦截的路径在sys_permission表中
    	for (SysPermission sysPermission : list) {
    		authorizeRequests.antMatchers(sysPermission.getUrl()).hasAnyAuthority(sysPermission.getCode());
		}
    	authorizeRequests.anyRequest().permitAll()//其他没有限定的请求，允许随意访问
        .and().anonymous();//对于没有配置权限的其他请求允许匿名访问
    }

}
```
动态生成拦截。

在登录时，会赋予用户权限，只有匹配到权限的url才能被该用户访问。

当前后端杂合在一起时，前端的跳转也是经过后端来控制，所以只需要5张表来进行基础的控制即可。

当前后端分离时，当我们使用vue时，根据获取到的menu，来进行动态生成路由。做法是登录成功后，查询用户的所有menu，生成路由表。