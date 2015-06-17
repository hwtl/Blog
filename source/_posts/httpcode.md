title: Spring MVC HttpCode
---
最近从JAVA 后台转到android 开发，开始频繁接触由各个后台服务器提供的api 接口，在对接当中
android 采用的是google提供的okhttp，很多时候会遇到后台提供的各种httpcode 不一致导致的
一些不必要的沟通，最后通过和各个业务team 商讨下来，决定顶下一套简易的方案出来，以下为后台
借用spring mvc 框架提供的一些常用操作的demo

## demo 示例
hello world 就不用在提供了吧，相信大家都已经很熟悉了
### 配置applicationContext.xml

``` bash
	<!--包扫描注解-->
	<context:component-scan base-package="com.jerry.*" />
	<!--设置mvc 的注解驱动-->
	<mvc:annotation-driven >
	    <mvc:message-converters  register-defaults="false">
	        <!-- http 请求编码 -->
	        <bean class="org.springframework.http.converter.StringHttpMessageConverter">
	        </bean>
	        <!-- json 请求编码 -->
	        <bean  class="com.alibaba.fastjson.support.spring.FastJsonHttpMessageConverter">
	        </bean>
	    </mvc:message-converters>
	</mvc:annotation-driven>
```

More info: [项目示例](https://git.oschina.net/Jerry.hu/prjHttpStatusCode)

### Controller 编写

``` java
package com.jerry.code.controller;

import com.jerry.code.model.Message;
import com.jerry.code.model.User;
import com.jerry.code.service.UserService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.util.Assert;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import java.util.Objects;

/**
 * Created with IntelliJ IDEA.
 * PackageName:com.jerry.code.controller
 * Author: Jerry.hu
 * Create: Jerry.hu (2015-06-16 上午11:26)
 * Description:
 * To change this template use File | Settings | File Templates.
 */
@Controller
@RequestMapping(value = "/user")

public class CodeController extends BaseController{
	@Autowired
	private UserService userService;

	@RequestMapping(value = "/{userCode}",method = RequestMethod.GET)
	protected ResponseEntity<?> show(@PathVariable int userCode,
	                                 @RequestHeader(value = "X-Token") String token) {
		Assert.isTrue(userCode > 80000, "用户工号不正确");

		User user = userService.getBean(userCode);

		if (user == null) {
			Message message = new Message("404", "用户信息不存在");
			return new ResponseEntity<>(message, HttpStatus.NOT_FOUND);
		}


		if (Objects.equals(token, "666666")) {
			Message message = new Message("403", "用户无访问权限");
			return new ResponseEntity<>(message, HttpStatus.FORBIDDEN);
		}

		if (Objects.equals(token, "888888")) {
			Message message = new Message("401", "用户未授权");
			return new ResponseEntity<>(message, HttpStatus.UNAUTHORIZED);
		}

		return ResponseEntity.ok(user);
	}
}


```

More info: [Server](http://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](http://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](http://hexo.io/docs/deployment.html)
