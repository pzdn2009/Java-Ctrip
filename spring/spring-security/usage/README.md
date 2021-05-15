# Usage

## Authentication

* Sms Code
* Email Code
* Usernamepassword
  * JDBC
* WeChat
* 用户名+密码、邮箱+密码、手机号+验证码、微信登录
* Barear Token
* 手动



springSecurity     每种认证方式都要写一大推类

1.要写Token封装认证信息

2.要写UserDetailsService的实现获取用户信息

3.要写provider调用UserDetailsService并且告诉AuthenticationManager他能认证哪种token

4.要写filter去拦截用户请求,获取用户提交的表单数据,交给AuthenticationManager选择一个provider去认证

5.把filter与provider注入一些必要属性交给总配置

```text
UsernamePasswordAuthenticationToken authenticationToken =
        new UsernamePasswordAuthenticationToken(authUser.getUsername(), password);

Authentication authentication = authenticationManagerBuilder.getObject().authenticate(authenticationToken);
SecurityContextHolder.getContext().setAuthentication(authentication);

https://www.cnblogs.com/minglie/p/9525931.html
```

成功处理器



Login LogOut UserInfo   


## Authorization





