---
layout: post
title: "Using JWT in the context of Spring Cloud" 
description: ""
category: 
tags: [architecture, security]
---

Note that Spring Cloud Security is exclusively for OAuth2 scenarios. Treat it as a complete different thing from Spring Security! Also, Spring Security has a very specific, pre-defined authentication flow, from where you can find names of many auto-wired/injected classes

Dependency:
	
	a. spring-security-web
	
	b. spring-security-config

On the authentication service side, generate JWT and add it as "Autherization: Bearer" token.  May consider writing it in an authentication filter that extends RequestHeaderAuthenticationFilter 

On the resource service side, to parse the incoming JWT, we have two implementation options 

Option 1
-----------

1. add an AUTHORIZATION filter that extends BasicAuthenticationFilter. 

2. This filter will parse the token text into a UsernamePasswordAuthenticationToken 

3. this authentication token will be placed in the context via `SecurityContextHolder.getContext().setAuthentication(authentication)`

4. We also need to extend WebSecurityConfigurerAdapter. Inside `protected void configure(HttpSecurity http) throws Exception`, we configure authoriazation for each path. Note that we need to explicitly set CORS config here to enable traffic from all source. This class also sets what AuthenticationManager to init/pass-in/config. 


Option 2 (less common)
------------

1. Add an AUTHENTICATION filter that extends RequestHeaderAuthenticationFilter. This filter will have a reference to the AuthenticationManager, which ,by the design of Spring Security, passes requests through a list of AuthenticationProviders

2. This also means we need to extend AuthenticationProvider to parse token and authenticate

3. Don't forget to inject our filter and provider into the context
