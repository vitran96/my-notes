Build on top of [[Spring]]. Mainly use [[Java]] but also support [[Kotlin]], [[Groovy]].
This is the most mature web-framework for [[Java]] ecosystem (beat [[vertx]] in my opinion).
With [[Thymeleaf]] it can be come a [[Full-stack web framework]] with [[Server-side rendering]].

# Standard project struture
```
spring-boot-app/
├── src/
│   ├── main/
│   │   ├── java/com/example/springbootapp/
│   │   │   ├── SpringBootAppApplication.java       # Main entry point
│   │   │   ├── controller/                         # REST Controllers
│   │   │   ├── service/                            # Business logic
│   │   │   ├── repository/                         # Data access layer
│   │   │   ├── model/                              # JPA Entities
│   │   │   ├── dto/                                # Data Transfer Objects (Request/Response)
│   │   │   ├── mapper/                             # MapStruct / manual mappers Entity <-> DTO
│   │   │   ├── config/                             # Security, Swagger, etc.
│   │   │   └── exception/                          # Exception classes & handlers
│   │   └── resources/
│   │       ├── application.yml
│   │       ├── static/
│   │       ├── templates/
│   │       └── db/                                 # Liquibase/Flyway migrations
│   └── test/java/com/example/springbootapp/
│       └── ...                                     # Unit & integration tests
├── pom.xml
└── README.md

```

# Init command
```shell
spring init \
  --boot-version=3.5.5 \
  --java-version=21 \
  --build=maven \
  --language=java \
  --group-id=tech.kingoyster \
  --artifact-id=demo \
  --name=project-1 \
  --package-name=tech.kingoyster.project-1 \
  --dependencies=web,security,lombok,liquibase,testcontainers,actuator,devtools,restdocs,validation,data-jpa,h2,mysql \
  --extract \
  project-1
```

# Lifecycle
%% TODO: %%

# [[Liquibase]] support
Spring-boot can auto recognize Liquibase dependancies in the class-path so you don't need additional configuration.
Please take a look at [[Liquibase]] page for configuration.

## Config
URL can be used from data-source
```yaml
spring:
  liquibase:
    change-log: classpath:/db/migrations/changelog.xml
    url: jdbc:mysql://localhost:3306/mydatabase
	user: myuser
	password: mypassword
    enabled: false
```

## Run with Bean

[Guide](https://www.baeldung.com/liquibase-refactor-schema-of-java-app#config)

```java
@Bean
public SpringLiquibase liquibase() {
    SpringLiquibase liquibase = new SpringLiquibase();
    liquibase.setChangeLog("classpath:config/liquibase/master.xml");
    liquibase.setDataSource(dataSource());
    return liquibase;
}
```

or

```java
@Bean
public SpringLiquibase liquibase(@Qualifier("taskExecutor") TaskExecutor taskExecutor,
        DataSource dataSource, LiquibaseProperties liquibaseProperties) {

    // Use liquibase.integration.spring.SpringLiquibase if you don't want Liquibase to start asynchronously
    SpringLiquibase liquibase = new AsyncSpringLiquibase(taskExecutor, env);
    liquibase.setDataSource(dataSource);
    liquibase.setChangeLog("classpath:config/liquibase/master.xml");
    liquibase.setContexts(liquibaseProperties.getContexts());
    liquibase.setDefaultSchema(liquibaseProperties.getDefaultSchema());
    liquibase.setDropFirst(liquibaseProperties.isDropFirst());
    if (env.acceptsProfiles(JHipsterConstants.SPRING_PROFILE_NO_LIQUIBASE)) {
        liquibase.setShouldRun(false);
    } else {
        liquibase.setShouldRun(liquibaseProperties.isEnabled());
        log.debug("Configuring Liquibase");
    }
    return liquibase;
}
```

# DB & [[JPA]]

## Config
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/demo_liquibase
    username: postgres
    password: changemeinproduction
    driver-class-name: org.postgresql.Driver
  jpa:
    hibernate:
      ddl-auto: none
```

# REST Controller
REST controller is a combination of `@Controller` and `@ResponseBody` (basically add `application/json` as response)

# Spring's Bean
Why is it important?
- For Spring component scan & dependencies injection.

## `@Autowired` vs constructor injection

# Spring test

## Unit Test
Don't need `@SpringBootTest` if not load Spring app context.

# Spring validation

## Sample validation
```java
class User {
	@Email
	private String email;
}
```

## Lifecycle
%% TODO: %%

## [[Maven]]
```xml
<!-- Maven -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

# Spring [[REST API|REST]] Docs
[Document](https://docs.spring.io/spring-restdocs/docs/current/reference/htmlsingle)

Notes:
- We can [[Unit Test]] the document since this module also support testing.
- This is more like handwriting document

# Springdoc [[OpenAPI]] generation
[Guide](https://www.baeldung.com/spring-rest-openapi-documentation)

API URL: `http://localhost:8080/v3/api-docs`
Swagger UI: `http://localhost:8080/swagger-ui/index.html`

## [[Maven]] dependency
### WebMVC
```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.8.13</version>
</dependency>
```

### WebFlux
```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webflux-ui</artifactId>
    <version>2.8.5</version>
</dependency>
```

### Support plugin for [[Kotlin]]
```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-common</artifactId>
    <version>2.8.5</version>
</dependency>
```

## Properties
[Properties](https://springdoc.org/properties.html)
```properties
springdoc.api-docs.enabled=false
springdoc.api-docs.path=<url path>
springdoc.swagger-ui.path=<url path>.html
springdoc.swagger-ui.operationsSorter=<sort with method|etc...>
springdoc.swagger-ui.tagsSorter=alpha
springdoc.writer-with-order-by-keys=true
```

## Plugin to generate docs
```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <version>3.4.3</version>
    <executions>
        <execution>
            <id>pre-integration-test</id>
            <goals>
                <goal>start</goal>
            </goals>
        </execution>
        <execution>
            <id>post-integration-test</id>
            <goals>
                <goal>stop</goal>
            </goals>
        </execution>
    </executions>
</plugin>
<plugin>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-maven-plugin</artifactId>
    <version>1.4</version>
    <executions>
        <execution>
            <phase>integration-test</phase>
            <goals>
                <goal>generate</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## Show actuator routes
Actuator is not show by default
```properties
springdoc.show-actuator=true
```

## Show Spring-security routes
Spring's security not show by default
```properties
# not work
springdoc.show-login-endpoint=true
```

# Spring security
Notes:
- Default will redirect like how [[SSO]] work
	- Can be disabled with config
- Spring security does support [[LDAP]], [[OAuth]] integration
- [[CSRF]] is supported by default
- Request cache is supported by default

## Sample config
```java
package tech.kingoyster.spring_1;

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



## Lifecycle
%% TODO: %%

## Custom implement
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
import tech.kingoyster.spring_1.exception.NotAuthenticatedException;  
import tech.kingoyster.spring_1.exception.NotFoundException;  
import tech.kingoyster.spring_1.exception.UserNotAuthenticatedException;  
import tech.kingoyster.spring_1.user.User;  
import tech.kingoyster.spring_1.user.UserRepository;  
import tech.kingoyster.spring_1.user.UserSummary;  
  
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
import tech.kingoyster.spring_1.HttpMediaType;  
import tech.kingoyster.spring_1.exception.ErrorDetail;  
  
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
import tech.kingoyster.spring_1.HttpMediaType;  
import tech.kingoyster.spring_1.exception.ErrorDetail;  
  
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

## Require authenticate on all API exclude some
A simple way to fine-grain request matcher.
This is important since it mean we only need to define the path 1 time.
And it work well with [[SPA]] too if we host it together with BE.

```java title="SecurityConfig.java"
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
		...
  
    return http.build();  
}
```

# DTO Mapper pattern
Notes:
- DTO set at controller
- Mapping should be done at Service layer
- Mapping can be done manually or with `MapStruct` / `ModelMapper`

# Startup task
```java title="InitAdmin.java"
package example;  
  
import lombok.RequiredArgsConstructor;  
import org.springframework.boot.context.event.ApplicationReadyEvent;  
import org.springframework.context.event.EventListener;  
import org.springframework.security.crypto.password.PasswordEncoder;  
import org.springframework.stereotype.Component;  
  
import java.util.Optional;  
  
@Component  
@RequiredArgsConstructor  
public class InitAdmin {  
    public final UserRepository userRepository;  
    public final PasswordEncoder passwordEncoder;  
    public final AdminConfiguration adminConfiguration;  
  
    @EventListener(ApplicationReadyEvent.class)  
    void initAdmin() {  
        try {  
            Optional<UserSummary> admin = userRepository.findOneProjectedByEmail(adminConfiguration.getEmail());  
            if (admin.isPresent()) {  
                return;  
            }  
  
            userRepository.save(  
                    User.builder()  
                            .email(adminConfiguration.getEmail())  
                            .fullName(adminConfiguration.getFullName())  
                            .hashedPassword(  
                                    passwordEncoder.encode(adminConfiguration.getPassword())  
                            )  
                            .build()  
            );  
        } catch (Exception e) {  
            // TODO: add logger  
            System.out.println("Failed to create admin account: " + e.getMessage());  
        }  
    }  
}
```

# Custom config param

## Use `@Value`
```java
@Value("app.env1")
private String env1;
```

## Use `@Configuration`
```java
@Configuration  
@ConfigurationProperties(prefix = "app.admin")  
@RequiredArgsConstructor  
@NoArgsConstructor  
@Getter  
@Setter  
public class AdminConfiguration {
	// app.admin.fullName
    private String fullName;  
	// app.admin.email
    private String email;  
	// app.admin.password
    private String password;  
}
```

# Startup task

## Task to run on app ready
```java
@Component  
@RequiredArgsConstructor  
public class InitAdmin {  
    @EventListener(ApplicationReadyEvent.class)  
    void taskToRun() {  
        // Find if any user with email existed.  
    }  
}
```

# Profile
Best practice:
- Use multiple profile
- Use profile by deployment environment.

## Deployment profile
Should set things like debugger level for each deployment so that we don't have to override on deploy and only need to choose profile.
```shell
java -jar app.jar --spring.profiles.active=dev
```

# Config file

## YML vs properties
- [[Yaml|YML]]:
	- Better structure
	- Easier to make mistake due to indentation
	- Might be harder for resolve [[Git]] conflict compare to [[properties]] since the config is on multiline
- [[properties]]:
	- Easier to resolve conflict

Note:
- In my opinion, we choose by preference. And I prefer [[Yaml]] format. We should rarely make change to config let alone create a conflict.

# Caching
%% TODO: %%

# Serve SPA site