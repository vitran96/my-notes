Build on top of [[Spring]]. Mainly use [[Java]] but also support [[Kotlin]], [[Groovy]].
This is the most mature web-framework for [[Java]] ecosystem (beat [[vertx]] in my opinion)

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

Sample validation:
```java
class User {
	@Email
	private String email;
}
```

## Maven
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

# Spring security
Notes:
- Default will redirect like how SSO work
	- Can be disabled with config
- Spring security does support [[LDAP]], [[OAuth]] integration

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
                // NOTE: why disable for REST?
                .csrf(AbstractHttpConfigurer::disable)
                // NOTE: why does this affect redirect?
                .requestCache(RequestCacheConfigurer::disable)
                .cors(Customizer.withDefaults())
                .authorizeHttpRequests(auth -> auth
                        // NOTE: this allow create internal only API.
                        // TODO: But how about internal between service?
                        .requestMatchers("/internal/**").denyAll()
                        .requestMatchers(
                                "/v3/api-docs/**",
                                // NOTE: what is scalar?
                                "/scalar/**",
                                "/actuator/health"
//                                "/h2-console/**"
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

        // NOTE: this is for H2 console frame
//        http.headers(
//                headers -> headers.frameOptions(HeadersConfigurer.FrameOptionsConfig::sameOrigin)
//        );

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