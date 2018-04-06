---
layout: post
title: "Using JWT in the context of Spring Cloud" 
description: ""
category: 
tags: [architecture, security]
---

1. On the authentication service side, generate JWT token and add it as "Autherization: Bearer" token. May consider writing it in an authentication filter 

```java

@Override
    protected void authenticate(HttpServletRequest req,
                                            HttpServletResponse res,
                                            FilterChain chain,
                                            Authentication auth) throws IOException, ServletException {

	String token = Jwts.builder()
                .setSubject(((User) auth.getPrincipal()).getUsername())
                .setExpiration(new Date(System.currentTimeMillis() + EXPIRATION_TIME))
                .signWith(SignatureAlgorithm.HS512, SECRET.getBytes())
                .compact();
        String token = JWT_TOKEN_TXT_HERE
        res.addHeader("Authorization", "Bearer " + token);
    }
```

2. On the resource service side, add an authorization filter 

```java

public class JWTAuthorizationFilter extends BasicAuthenticationFilter {

    public JWTAuthorizationFilter(AuthenticationManager authManager) {
        super(authManager);
    }

private UsernamePasswordAuthenticationToken getAuthentication(HttpServletRequest request) {
        String token = request.getHeader("Authorization");
        if (token != null) {
            // parse the token.
            String user = Jwts.parser()
                    .setSigningKey(SECRET.getBytes())
                    .parseClaimsJws(token.replace("Bearer ", ""))
                    .getBody()
                    .getSubject();

            if (user != null) {
		//note we have only user here
                return new UsernamePasswordAuthenticationToken(user, null, new ArrayList<>());
            }
            return null;
        }
        return null;
    }

@Override
    protected void doFilterInternal(HttpServletRequest req,
                                    HttpServletResponse res,
                                    FilterChain chain) throws IOException, ServletException {
        String header = req.getHeader("Authorization");

        UsernamePasswordAuthenticationToken authentication = getAuthentication(req);

//we set the user in the SecurityContext and allow the request to move on.
        SecurityContextHolder.getContext().setAuthentication(authentication);
        chain.doFilter(req, res);
    }

}

3. Add the authorization filter to the Spring Security filter change

```java

@EnableWebSecurity
public class WebSecurity extends WebSecurityConfigurerAdapter {

    public WebSecurity() {
    }

//define which resources are public and which are secured.
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.cors().and().csrf().disable().authorizeRequests()
                .antMatchers(HttpMethod.POST, SIGN_UP_URL).permitAll()
                .anyRequest().authenticated()
                .and()
                .addFilter(new JWTAuthorizationFilter(authenticationManager()))
                // this disables session creation on Spring Security
                .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS);
    }


//we left it wide open by permitting requests from any source (/**)
  @Bean
  CorsConfigurationSource corsConfigurationSource() {
    final UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", new CorsConfiguration().applyPermitDefaultValues());
    return source;
  }
}

```

4. use `@PreAuthorize("hasAuthority('')")` in front of the controller to control access
