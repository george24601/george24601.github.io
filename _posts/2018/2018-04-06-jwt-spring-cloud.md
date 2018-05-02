---
layout: post
title: "Using JWT in the context of Spring Cloud" 
description: ""
category: 
tags: [architecture, security]
---

JWT
----------
Three parts: header, payload and signature.This means microservices need to know the public key to verify the signature.

Normally sent with the authorization header as the bearer token,i.e., use the bearer schema

API gateway will forward the request with JWT to the service, which will in turn will decide if it will grant the resource or not. 

We may choose to let user use a reference token, and API gateway will translate the reference token to the jwt token. Note the reference token can come from an OAuth server. This also suggests that JWT stays within the private network, and only reference token is on user's browser session

Note that Spring Cloud Security is exclusively for OAuth2 scenarios. Treat it as a complete different thing from Spring Security! Also, Spring Security has a very specific, pre-defined authentication flow, from where you can find names of many auto-wired/injected classes

Common Choices
---------

1. Spring cloud security

2. keycloak by JBoss

3. OpenID-Connect-Java-Spring-Server

4. Apereo CAS

Workflow
----------
1. Client logs in to acquire access token

2. Client sends access token to the API gateway

3. Gateway uses auth server to validate token and convert to JWT

4. Gateway sends jwt token along with requests to backend microservices

5. Every microservice has JWT client to decrypt user context inside the jwt

Dependency
-----------

1. spring-security-web
	
2. spring-security-config

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
