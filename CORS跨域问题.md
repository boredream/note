## 跨域问题

是什么？

浏览器的一个策略，默认情况下web端无法访问不通域名或不同端口号的服务端接口。



为什么？

浏览器引入同源策略主要是为了保护用户隐私和安全。如果浏览器没有同源策略，那么一个网页就可以访问另外一个网页中的任何资源，包括 Cookie、Session 等用户隐私数据。这样就会导致安全问题，例如恶意网站可以通过脚本攻击其他网站，窃取用户隐私数据。

在现在的前后端分离项目中，前端和后端往往是分别部署在不同的服务器上的，这样就容易出现跨域问题。解决方法一般是在前端服务器上配置代理，将前端请求转发到后端服务器上，使得前端请求和后端响应的域名相同，避免了跨域问题。



怎么做？

服务端添加

```java
@Configuration
public class CorsConfig {
    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        corsConfiguration.setAllowedOrigins(Collections.singletonList("*"));
        corsConfiguration.setAllowedMethods(Collections.singletonList("*"));
        corsConfiguration.setAllowedHeaders(Collections.singletonList("*"));
        corsConfiguration.setAllowCredentials(false);
 				corsConfiguration.setMaxAge(3600L);
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", corsConfiguration);
        return new CorsFilter(source);
    }
}
```



注：

vue中不能使用<form>标签，否则会默认走非ajax全局刷新的模式，无法解决跨域问题。

如果使用了 SpringSecurity，可能会对option预检请求也进行token验证从而报错，因此需要添加

```java
// SecurityConfig.java
 @Override
    protected void configure(HttpSecurity http) throws Exception {
        // ... 略
        // 跳过预检请求
        http.requestMatchers(CorsUtils::isPreFlightRequest).permitAll()
    }
```







## 组件库

默认前端样式很基础，一般需要elementUI等组件库。除了优化基础组件如button样式外，还提供一些常用的较复杂功能的组件。

原生里官方直接封装好组件了，本身有一定样式。elementUI可以类似于官方的MaterialDesign组件，但C端大部分组件特殊，通常需要一个应用or一个公司自定义自己的组件库。