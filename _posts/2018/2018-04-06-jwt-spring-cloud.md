---
layout: post
title: "Using JWT in the context of Spring Cloud" 
description: ""
category: 
tags: [architecture, security]
---

### Authorization and Role Management in Microservices

The discussions I found give VERY diverted opinions on how to implement authorization in a microservice environment. I will just list my findings and conclusions here

*  Most authorization rules I found in a microservice environments are role-based instead of ACL-based. May consider a specific user role/identity service to handle the user-role mapping. However, must be careful because this user-role mapping service can quickly become the kitchen skin of contexts from different domains
* Often, a service has a very domain specifc authorization rules and roles. In this case, such logic and roles, should remain within the service, but the trade off means we need to replicate main user database, or at least access a centralized user-role service
*  Most likely you will have some global default roles, e.g., admin, billing admin. These roles should stay with the general global auth service, i.e., when we generate the JWT, we should attach the applicable global roles to the JWT. Moreover, these admin roles should be the only roles that can bootstrap service-specific roles and assign them to the users.
* JWT should have only global role info, because: 
  * JWT is normally part of header, which is limited to 8KB. 
  * We don't want to leak other service's context.
* This setup suggests that role popluation happens twice, once at the auth service, once at the actual request processing service, and the actual role/permission check is decentralized instead of centralized.  
* Favor coarse-grained roles, modelled on how organization works


### JWT

* Three parts: header, payload and signature.This means microservices need to know the public key to verify the signature.
* Normally sent with the authorization header as the bearer token,i.e., use the bearer schema
* API gateway will forward the request with JWT to the service, which will in turn will decide if it will grant the resource or not. 
* We may choose to let user use a reference token, and API gateway will translate the reference token to the jwt token. Note the reference token can come from an OAuth server. This also suggests that JWT stays within the private network, and only reference token is on user's browser session

Note that Spring Cloud Security is exclusively for OAuth2 scenarios. Treat it as a complete different thing from Spring Security! Also, Spring Security has a very specific, pre-defined authentication flow, from where you can find names of many auto-wired/injected classes

### Common Choices

* Spring cloud security
* keycloak by JBoss
* OpenID-Connect-Java-Spring-Server
* Apereo CAS

### Workflow

* Client logs in to acquire access token
* Client sends access token to the API gateway
* Gateway uses auth server to validate token and convert to JWT
* Gateway sends jwt token along with requests to backend microservices
* Every microservice has JWT client to decrypt user context inside the jwt

On the authentication service side, generate JWT and add it as "Autherization: Bearer" token.  May consider writing it in an authentication filter that extends RequestHeaderAuthenticationFilter 

On the resource service side, to parse the incoming JWT, we have two implementation options 

#### Option 1

1. add an AUTHORIZATION filter that extends BasicAuthenticationFilter. 
2. This filter will parse the token text into a UsernamePasswordAuthenticationToken 
3. this authentication token will be placed in the context via `SecurityContextHolder.getContext().setAuthentication(authentication)`
4. We also need to extend WebSecurityConfigurerAdapter. Inside `protected void configure(HttpSecurity http) throws Exception`, we configure authoriazation for each path. Note that we need to explicitly set CORS config here to enable traffic from all source. This class also sets what AuthenticationManager to init/pass-in/config. 


#### Option 2 (less common)

1. Add an AUTHENTICATION filter that extends RequestHeaderAuthenticationFilter. This filter will have a reference to the AuthenticationManager, which ,by the design of Spring Security, passes requests through a list of AuthenticationProviders
2. This also means we need to extend AuthenticationProvider to parse token and authenticate
3. Don't forget to inject our filter and provider into the context
