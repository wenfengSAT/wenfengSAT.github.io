---
layout:     post
title:      基于Spring的Github第三方登录
subtitle:   基于Spring的Github第三方登录
date:       2018-10-17
author:     wenfengSAT
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Github
---


> 基于Spring的Github第三方登录

+ 准备工作

```

在做第三方登陆之前，首先我们当然需要有一个授权服务器承认的第三方应用身份，因此，我们首先前往授权服务器进行申请，
由于国内的所有应用都需要进行审核，比较麻烦，这里我们以Github为例，首先申请一个第三方应用的资格。

首先登陆Github账号，进入【Settings】->选择【Developer settings】，这里我们可以看到当前账户所建的回调应用

```

![Github第三方登录](https://raw.githubusercontent.com/wenfengSAT/data/master/images/blog/4.jpg)

```

点击右上角的【new Auth APP】，按要求填写信息就可以申请一个第三方应用的身份。由于我们是本地调试，
我们按照本地的测试地址填写相关信息即可：

```

![Github第三方登录](https://raw.githubusercontent.com/wenfengSAT/data/master/images/blog/5.jpg)

```

完成之后点进去拿到Client Id和Client Secret，这两个信息是用来标识第三方应用身份的相关信息，
特别注意Client Secret，Client Secret是用来和授权服务器交换验证凭证（Access Token）的，千万不能暴露出去。

这样，我们就拥有了第三方应用的身份，可以喝Github交互进行OAuth2的授权了。

```
    
+ 功能分析

```

我们的第三方登陆功能实际上就是将OAuth2的授权流程跑通，最后将拿到的用户信息存储在数据库中，
并将其与本地数据的用户信息对应起来，以便于下次登陆时直接拿到本地的用户信息。

总结一下OAuth的基本流程，实际上主要涉及到下列URL：

引导用户进行授权的授权地址（authorizationUrl）
用户授权后传递用户凭证（code）的redirectURL
使用用户凭证（code）交换验证凭证（Access Token）的地址
获取用户信息的地址（可能不止一个）
通过确认以上地址，我们可以搭建一个通用化的OAuth授权以及验证功能，不同的授权服务器只需要提供不同的地址，
其他流程完全可以用相同的代码来解决，这就是我们今天需要实现的通用化第三方登陆功能的基础。

以Github为例，其相应的API地址分别为：

https://github.com/login/oauth/authorize?client_id={client_id}&redirect_uri={redirect_uri}&state={state}
http://localhost:8080/oauth/github/callback?code={code}&state={state}
https://github.com/login/oauth/access_token
https://api.github.com/user?access_token={access_token}
现在，我们需要做下列事情：

在首页显示Github的授权链接，使用户能够访问authorizationUrl
编写一个Controller，处理http://localhost:8080/oauth/github/callback?code={code}&state={state}的请求，主要是拿到code
用code访问http://localhost:8080/oauth/github/callback?code={code}&state={state}access token
然后利用access token访问https://api.github.com/user?access_token={access_token}，拿到用户信息
根据用户信息判断该用户是否已经绑定，如果已经绑定，直接登录，如果未绑定则导入到绑定页面，引导用户进入绑定页面
在绑定页面添加新的用户

```


+ 示例代码

```java

package com.security.controller;

import java.io.IOException;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Random;
import java.util.Set;
import java.util.concurrent.ExecutionException;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.TypeReference;
import com.github.scribejava.apis.GitHubApi;
import com.github.scribejava.core.builder.ServiceBuilder;
import com.github.scribejava.core.exceptions.OAuthException;
import com.github.scribejava.core.model.OAuth2AccessToken;
import com.github.scribejava.core.model.OAuthRequest;
import com.github.scribejava.core.model.Verb;
import com.github.scribejava.core.oauth.OAuth20Service;
import com.security.entity.GithubUser;
import com.security.entity.ResourceEntity;
import com.security.entity.RoleEntity;
import com.security.entity.UserEntity;
import com.security.repository.RoleRepository;
import com.security.repository.UserRepository;
import com.security.util.ResourceUtil;
import com.github.scribejava.core.model.Response;
import org.springframework.security.core.authority.SimpleGrantedAuthority;

@Controller
@RequestMapping("/OAuth")
public class OAuthController {

	/**
	 * 1.访问用户登录的验证接口
	 * https://github.com/login/oauth/authorize?client_id=xxxxxxxxxxxxxxxxxx&scope=user,public_repo
	 * 2.访问上面接口后会github会让其跳转到你预定的url(Authorization callback URL)，并且带上code参数,例如
	 * http://localhost:8080/callback?code=****************
	 * 3.然后，开发者可以通过code,client_id以及client_secret这三个参数获取用户的access_token即用户身份标识，请求如下
	 * https://github.com/login/oauth/access_token?client_id=xxxxxxxxxxxxxxxxxxx&client_secret=xxxxxxxxxxxxxxxxx&code=xxxxxxxxxxxxxxxxxxx
	 * 这样就会返回access_token,如下
	 * access_token=xxxxxxxxxxxxxxxxxxxxxxxxx&scope=public_repo%2
	 * Cuser&token_type=bearer 
	 * 4. 这样我们就可以用这个access_token来获取用户的信息
	 * https://api.github.com/user?access_token=xxxxxxxxxxxxxxxxxxxxxxxxx
	 * 
	 */

	@Autowired
	private UserRepository userRepository;
	@Autowired
	private RoleRepository roleRepository;

	@Autowired
	protected AuthenticationManager authenticationManager;

	private static final String PROTECTED_RESOURCE_URL = "https://api.github.com/user";
	@Value("${github.appId}")  
	private String appId;
	@Value("${github.appSecret}")  
	private String appSecret; 
	@Value("${github.callbackUrl}")  
	private String callbackUrl; 
	@Value("${github.redrictUrl}")  
	private String redrictUrl; 

	@RequestMapping(value = "/authLogin", method = RequestMethod.GET)
	public void authLogin(HttpServletRequest request, HttpServletResponse response) {
		try {
			response.sendRedirect(redrictUrl);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}

	@RequestMapping(value = "/callback/getOAuth", method = RequestMethod.GET)
	public String getOAuth(@RequestParam(value = "code", required = true) String code, Model model,
			HttpServletRequest request, HttpServletResponse response) {
		String secretState = "secret" + new Random().nextInt(999_999);
		OAuth20Service service = new ServiceBuilder(appId)
				.apiSecret(appSecret).state(secretState)
				.callback(callbackUrl).build(GitHubApi.instance());
		OAuth2AccessToken accessToken = null;
		GithubUser githubUser = null;
		try {
			accessToken = service.getAccessToken(code);
			final OAuthRequest oAuthRequest = new OAuthRequest(Verb.GET, PROTECTED_RESOURCE_URL);
			service.signRequest(accessToken, oAuthRequest);
			final Response oAuthresponse = service.execute(oAuthRequest);
			githubUser = JSON.parseObject(oAuthresponse.getBody(), new TypeReference<GithubUser>() {});
			model.addAttribute("user", oAuthresponse.getBody());
			System.out.println(oAuthresponse.getBody());
		} catch (IOException e) {
			model.addAttribute("error", "github登录失败！");
			return "login";
		} catch (InterruptedException e) {
			model.addAttribute("error", "github登录失败！");
			return "login";
		} catch (ExecutionException e) {
			model.addAttribute("error", "github登录失败！");
			return "login";
		} catch (OAuthException e) {
			model.addAttribute("error", "github登录失败！");
			return "login";
		}
		// TODO
		// 1、判断是不是第一次授权登录,新增用户写进数据库，给一个默认角色
		/*
		 * if(第一次授权登录){ 
		 * 新增用户写进数据库，给一个默认角色，user表新增字段第三方登录方式及第三方用户id
		 * 新增userDetail表来存储用户详细信息 
		 * }else{ 
		 * 	根据第三方用户id获取以前存进库里的信息登录 
		 * }
		 */
		List<RoleEntity> roles = new ArrayList<RoleEntity>();
		roles.add(roleRepository.findOne((long) 2));//普通用户
		UserEntity userEntity = new UserEntity();
		userEntity.setUsername(githubUser.getLogin());
		userEntity.setPassword("123456");
		userEntity.setId((long) 3);
		userEntity.setRoles(roles);
		userEntity = userRepository.saveAndFlush(userEntity);
		List<GrantedAuthority> authorities = new ArrayList<>();
		authorities.add(new SimpleGrantedAuthority("ROLE_USER"));
		Authentication authentication = new UsernamePasswordAuthenticationToken(userEntity.getUsername(), userEntity.getPassword(), authorities);
		//List<GrantedAuthority> authorities = new ArrayList<>();
		//authorities.add(new SimpleGrantedAuthority("ROLE_USER"));
		//authorities.add(new SimpleGrantedAuthority("ROLE_ADMIN"));
		//Authentication authentication = new UsernamePasswordAuthenticationToken("admin", "123456", authorities);
		// 将token传递给Authentication进行验证
		Authentication result = authenticationManager.authenticate(authentication);
		SecurityContextHolder.getContext().setAuthentication(result);
		UserEntity user = (UserEntity) SecurityContextHolder.getContext().getAuthentication().getPrincipal();
		List<RoleEntity> roleList = user.getRoles();
		Set<ResourceEntity> resourceList = new HashSet<ResourceEntity>();
		String roles1 = "";
		for (RoleEntity role : roleList) {
			roles1 += role.getName() + ",";
			resourceList.addAll(role.getResources());
		}
		roles1 = roles1.substring(0, roles1.length() - 1);
		request.getSession().setAttribute("roles", roles1);
		System.out.println("====================="+roles1);
		for(ResourceEntity r: resourceList) {
			System.out.println(r.getName());
		}
		System.out.println(ResourceUtil.format(new ArrayList<>(resourceList)));
		model.addAttribute("resourceList", ResourceUtil.format(new ArrayList<>(resourceList)));
		return "github_success";
	}
}


```

+ 全部代码

```

http://www.zuidaima.com/share/4111455298538496.htm

```












