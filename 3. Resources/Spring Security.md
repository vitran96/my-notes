---
para: resource
tags: [java, security, backend]
---

## Notes

- Default will redirect like how [[SSO]] work
	- Can be disabled with config
- Spring security does support [[LDAP]], [[OAuth]] integration
- [[CSRF]] is supported by default
- Request cache is supported by default

## Sample config

```java
package com.example.spring_1;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.http.HttpStatus;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.method.configuration.EnableMethodSecurity;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configurers.AbstractHttpConfigurer;
import org.springframework.security.config.annotation.web.configurers.RequestCacheConfigurer;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.HttpStatusEntryPoint;
import org.springframework.web.cors.*;

import java.util.List;

@EnableWebSecurity
@EnableMethodSecurity
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain apiSecurityChain(HttpSecurity http) throws Exception {
        http
                // NOTE: disable to simplify this sample
                .csrf(AbstractHttpConfigurer::disable)
                // NOTE: why does this affect redirect?`
                .requestCache(RequestCacheConfigurer::disable)
                .cors(Customizer.withDefaults())
                .authorizeHttpRequests(auth -> auth
                        // NOTE: this allow create internal only API.
                        .requestMatchers(
                                "/v3/api-docs/**",
                                "/scalar/**",
                                "/actuator/health"
                        ).permitAll()
                        .anyRequest().authenticated()
                )
                // NOTE: basic auth is now (simple), no "formLogin()" -> no redirect
                .httpBasic(Customizer.withDefaults())
                .exceptionHandling(ex -> ex
                        // NOTE: return 401
                        .authenticationEntryPoint(new HttpStatusEntryPoint(HttpStatus.UNAUTHORIZED))
                        .accessDeniedHandler((request, response, accessDeniedException) -> {
                            // TODO: why do it this way??
                            response.setStatus(HttpStatus.FORBIDDEN.value());
                            response.setContentType("application/json");
                            response.getWriter().write("{\"error\":\"forbidden\"}");
                        })
                );
        return http.build();
    }

    // TODO: replace this with service to interact with User table in DB
    @Bean
    UserDetailsService users(PasswordEncoder encoder) {
        UserDetails admin = User.withUsername("admin")
                .password(encoder.encode("Admin@123"))
                .roles("ADMIN")
                .build();

        UserDetails user = User.withUsername("user")
                .password(encoder.encode("User@123"))
                .roles("USER")
                .build();

        return new InMemoryUserDetailsManager(admin, user);
    }

    @Bean
    PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    // TODO: this should come from application.yml & ENV
    @Bean
    CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration cfg = new CorsConfiguration();
        cfg.setAllowedOrigins(List.of("http://localhost:5173")); // React dev origin
        cfg.setAllowedMethods(List.of("GET", "POST", "PUT", "DELETE", "OPTIONS"));
        cfg.setAllowedHeaders(List.of("*"));
        cfg.setAllowCredentials(true);

        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", cfg);
        return source;
    }
}
```

## Custom JWT filter

~~Since our user data source is in DB, we need to create custom `UserDetailsService`.~~
~~There is an existing implementation, [JdbcUserDetailsManager](https://docs.spring.io/spring-security/reference/servlet/authentication/passwords/jdbc.html). However, it has different schema so we are not going to use it.~~
Above is not relevant anymore. Spring security doesn't have built-in structure to authenticate as [[REST API]].
So we will have to create our own filter and our own process / flow.

Note: 
- Consider using [[OAuth]] module

```java title="SecurityConfig.java"
	private final AccessTokenFilter accessTokenFilter;  
	private final CustomAuthenticationEntryPoint customAuthenticationEntryPoint;  
	private final CustomAccessDeniedHandler customAccessDeniedHandler;

	@Bean
    public SecurityFilterChain apiSecurityChain(HttpSecurity http) throws Exception {
        http
                .authorizeHttpRequests(auth -> auth
                        // NOTE: this way, I can guarantee route like SPA route won't be blocked
                        .requestMatchers(
                               "/api/v1/auth/login",
                               "/api/v1/dummies/say-hi"
                        ).permitAll()
                        .requestMatchers("/api/**").authenticated()
                        .anyRequest().permitAll()
                )
                // TODO: split and re-enable this
                .csrf(AbstractHttpConfigurer::disable)
                // NOTE: why does this affect redirect?
                .requestCache(RequestCacheConfigurer::disable)
                // TODO: better split this config
                .cors(Customizer.withDefaults())
                // NOTE: basic auth is now (simple), no "formLogin()" -> no redirect
                .httpBasic(AbstractHttpConfigurer::disable)
                // Override Spring Security error handling for custom ErrorDetail
                .exceptionHandling(ex -> ex
                        .authenticationEntryPoint(customAuthenticationEntryPoint)
                        .accessDeniedHandler(customAccessDeniedHandler)
                )
                .addFilterBefore(accessTokenFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
```

```java title="JwtProfider.java"
import com.auth0.jwt.JWT;  
import com.auth0.jwt.JWTCreator;  
import com.auth0.jwt.JWTVerifier;  
import com.auth0.jwt.algorithms.Algorithm;  
import com.auth0.jwt.interfaces.DecodedJWT;  
import org.springframework.beans.factory.annotation.Value;  
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;  
import org.springframework.security.core.Authentication;  
import org.springframework.security.core.userdetails.User;  
import org.springframework.security.core.userdetails.UserDetails;  
import org.springframework.stereotype.Service;  
  
import java.time.Instant;  
import java.util.Objects;  
  
@Service  
public class JwtProvider {  
    private final String secret;  
    private final String issuer;  
    private final int accessTokenExpiry;  
    private final int refreshTokenExpiry;  
  
    public JwtProvider(  
            @Value("${app.jwt.secret}") String secret,  
            @Value("${spring.application.name}") String issuer,  
            @Value("${app.jwt.access.expiry}") int accessTokenExpiry,  
            @Value("${app.jwt.refresh.expiry}") int refreshTokenExpiry  
    ) {  
        this.secret = secret;  
        this.issuer = issuer;  
        this.accessTokenExpiry = accessTokenExpiry;  
        this.refreshTokenExpiry = refreshTokenExpiry;  
    }  
  
    public Authentication getAuthentication(String token) {  
        DecodedJWT decodedJWT = decodeJwt(token);  
        verifyOrThrow(decodedJWT);  
        String subject = decodedJWT.getSubject();  
  
        UserDetails principal = User.builder()  
                .username(subject)  
                // Password cannot be null  
                .password("")  
                .build();  
        return UsernamePasswordAuthenticationToken.authenticated(  
                principal, 
                "", 
                null
        );  
    }  
  
    public void verifyOrThrow(DecodedJWT decodedJWT) {  
        Algorithm algorithm = Algorithm.HMAC512(secret);  
        JWTVerifier jwtVerifier = JWT.require(algorithm)  
                .withIssuer(issuer)  
                .build();  
  
        jwtVerifier.verify(decodedJWT);  
    }  
  
    public DecodedJWT decodeJwt(String token) {  
        return JWT.decode(token);  
    }  
  
    private String generateToken(int expiry, String subject) {  
        Instant now = Instant.now();  
        Algorithm algorithm = Algorithm.HMAC512(secret);  
        JWTCreator.Builder builder = JWT.create()  
                .withIssuer(issuer)  
                .withIssuedAt(now)  
                .withExpiresAt(now.plusMillis(expiry));  
  
        if (Objects.nonNull(subject)) {  
            builder = builder  
                    .withSubject(subject);  
        }  
  
        return builder.sign(algorithm);  
    }  
  
    private String generateToken(int expiry) {  
        String str = null;  
        return generateToken(expiry, str);  
    }  
  
    public String generateAccessToken(String idStr) {  
        return generateToken(accessTokenExpiry, idStr);  
    }  
  
    public String generateRefreshToken() {  
        return generateToken(refreshTokenExpiry);  
    }  
}
```

```java title="AuthenticationServiceImpl.java"
import com.auth0.jwt.exceptions.JWTVerificationException;  
import com.auth0.jwt.interfaces.DecodedJWT;  
import lombok.RequiredArgsConstructor;  
import org.springframework.security.crypto.password.PasswordEncoder;  
import org.springframework.stereotype.Service;  
import com.example.spring_1.exception.NotAuthenticatedException;  
import com.example.spring_1.exception.NotFoundException;  
import com.example.spring_1.exception.UserNotAuthenticatedException;  
import com.example.spring_1.user.User;  
import com.example.spring_1.user.UserRepository;  
import com.example.spring_1.user.UserSummary;  
  
@Service  
@RequiredArgsConstructor  
public class AuthenticationServiceImpl implements AuthenticationService {  
    private final UserRepository userRepository;  
    private final JwtProvider jwtProvider;  
    private final PasswordEncoder passwordEncoder;  
  
    @Override  
    public LoginResponse authenticate(LoginRequest loginRequest) {  
        User user = userRepository.findOneByEmail(loginRequest.username())  
                .orElseThrow(() -> new NotFoundException("User " + loginRequest.username() + " not found!"));  
  
        if (!passwordEncoder.matches(loginRequest.password(), user.getHashedPassword())) {  
            throw new UserNotAuthenticatedException("User username or password is not correct!");  
        }  
  
        String refreshToken = jwtProvider.generateRefreshToken();  
        String accessToken = jwtProvider.generateAccessToken(user.getId().toString());  
  
        return LoginResponse.builder()  
                .refreshToken(refreshToken)  
                .accessToken(accessToken)  
                .build();  
    }  
  
    @Override  
    public RefreshDto refreshToken(String refreshToken, String accessToken) {  
        if (!canRefresh(refreshToken)) {  
            throw new NotAuthenticatedException("Refresh token");  
        }  
  
        String subject = jwtProvider.decodeJwt(accessToken).getSubject();  
        String newAccessToken = jwtProvider.generateAccessToken(subject);  
        return RefreshDto.builder()  
                .accessToken(newAccessToken)  
                .build();  
    }  
  
    private boolean canRefresh(String token) {  
        try {  
            DecodedJWT decodedJWT = jwtProvider.decodeJwt(token);  
            jwtProvider.verifyOrThrow(decodedJWT);  
            return true;  
        } catch (JWTVerificationException e) {  
            return false;  
        }  
    }  
}
```

```java title="AccessTokenFilter.java"
import com.auth0.jwt.exceptions.JWTVerificationException;
import com.auth0.jwt.exceptions.TokenExpiredException;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;
import org.apache.commons.lang3.StringUtils;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import java.io.IOException;
import java.rmi.RemoteException;

@Component
@RequiredArgsConstructor
public class AccessTokenFilter extends OncePerRequestFilter {

    private static final String AUTH_PREFIX = "Bearer ";
    public static final String AUTH_EXCEPTION_ATTRIBUTE = "authException";

    private final JwtProvider jwtProvider;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {
        String accessToken = extractToken(request.getHeader("Authorization"));
        if (StringUtils.isNotEmpty(accessToken)) {
            validateAccessToken(request, accessToken);
        }

        filterChain.doFilter(request, response);
    }

    private void validateAccessToken(HttpServletRequest request, String accessToken) {
        try {
            Authentication authentication = jwtProvider.getAuthentication(accessToken);
            SecurityContextHolder.getContext().setAuthentication(authentication);
        } catch (JWTVerificationException e) {
            request.setAttribute(AUTH_EXCEPTION_ATTRIBUTE, e);
        }
    }

    private String extractToken(String fullBearerToken) {
        if (StringUtils.startsWith(fullBearerToken, AUTH_PREFIX)) {
            return StringUtils.removeStart(fullBearerToken, AUTH_PREFIX);
        }

        return null;
    }
}

```

```java title="CustomAccessDeniedHandler.java"
import com.fasterxml.jackson.databind.ObjectMapper;  
import jakarta.servlet.ServletException;  
import jakarta.servlet.http.HttpServletRequest;  
import jakarta.servlet.http.HttpServletResponse;  
import lombok.RequiredArgsConstructor;  
import org.springframework.http.HttpStatus;  
import org.springframework.security.access.AccessDeniedException;  
import org.springframework.security.web.access.AccessDeniedHandler;  
import org.springframework.stereotype.Component;  
import com.example.spring_1.HttpMediaType;  
import com.example.spring_1.exception.ErrorDetail;  
  
import java.io.IOException;  
import java.time.LocalDateTime;  
  
@Component  
@RequiredArgsConstructor  
public class CustomAccessDeniedHandler implements AccessDeniedHandler {  
    private final ObjectMapper objectMapper;  
  
    @Override  
    public void handle(HttpServletRequest request,  
                       HttpServletResponse response,  
                       AccessDeniedException accessDeniedException) throws IOException, ServletException {  
        var errorDetail = new ErrorDetail(  
                LocalDateTime.now(),  
                1,  
                accessDeniedException.getMessage(),  
                request.getServletPath(),  
                null  
        );  
  
        response.setStatus(HttpStatus.FORBIDDEN.value());  
        response.setContentType(HttpMediaType.APP_JSON.getValue());  
        response.getWriter().write(objectMapper.writeValueAsString(errorDetail));  
    }  
}
```

```java title="CustomAuthenticationEntryPoint.java"
import com.auth0.jwt.exceptions.JWTVerificationException;  
import com.fasterxml.jackson.databind.ObjectMapper;  
import jakarta.servlet.ServletException;  
import jakarta.servlet.http.HttpServletRequest;  
import jakarta.servlet.http.HttpServletResponse;  
import lombok.RequiredArgsConstructor;  
import org.springframework.http.HttpStatus;  
import org.springframework.security.core.AuthenticationException;  
import org.springframework.security.web.AuthenticationEntryPoint;  
import org.springframework.stereotype.Component;  
import com.example.spring_1.HttpMediaType;  
import com.example.spring_1.exception.ErrorDetail;  
  
import java.io.IOException;  
import java.time.LocalDateTime;  
import java.util.Objects;  
  
@Component  
@RequiredArgsConstructor  
public class CustomAuthenticationEntryPoint implements AuthenticationEntryPoint {  
    private final ObjectMapper objectMapper;  
  
    @Override  
    public void commence(HttpServletRequest request,  
                         HttpServletResponse response,  
                         AuthenticationException authException) throws IOException, ServletException {  
        var message = authException.getMessage();  
        var customAuthException = request.getAttribute(AccessTokenFilter.AUTH_EXCEPTION_ATTRIBUTE);  
        if (Objects.nonNull(customAuthException) && customAuthException instanceof JWTVerificationException jwtVerificationException) {  
            message = jwtVerificationException.getMessage();  
        }  
  
        var errorDetail = new ErrorDetail(  
                LocalDateTime.now(),  
                0,  
                message,  
                request.getServletPath(),  
                null  
        );  
  
        response.setStatus(HttpStatus.UNAUTHORIZED.value());  
        response.setContentType(HttpMediaType.APP_JSON.getValue());  
        response.getWriter().write(objectMapper.writeValueAsString(errorDetail));  
    }  
}
```

**Known flaw:** refreshToken() takes the subject from an unverified access token and the refresh token carries no subject, so anyone with a valid refresh token can mint tokens for any user. Put the subject in the refresh token and verify it.

## Request matchers

A simple way to fine-grain request matcher.
This is important since it mean we only need to define the path 1 time.
And it work well with [[SPA]] too if we host it together with BE.

## Role & Permission

Article: https://medium.com/@victoronu/implementing-role-and-permission-based-authorization-in-spring-boot-with-jwt-359901206b6a

%% TODO: %%

## OAuth

%% TODO: %%

## SPNEGO

%% TODO: extract sample from a past project %%

See [[Kerberos#SPNEGO|SPNEGO]].
