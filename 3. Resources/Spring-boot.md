---
para: resource
tags: [java, backend]
---
Build on top of [[Spring]]. Mainly use [[Java]] but also support [[Kotlin]], [[Groovy]].
This is the most mature web-framework for [[Java]] ecosystem (beat [[vertx]] in my opinion).
With [[Thymeleaf]] it can be come a [[Full-stack web framework]] with [[Server-side rendering]].
See also: [[Spring Security]], [[Java testing]], [[HikariCP]]

# Standard project structure

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
  --group-id=com.example \
  --artifact-id=demo \
  --name=project-1 \
  --package-name=com.example.project1 \
  --dependencies=web,security,lombok,liquibase,testcontainers,actuator,devtools,restdocs,validation,data-jpa,h2,mysql \
  --extract \
  project-1
```

# App starting life-cycle

bootstrap (load properties & env) -> context creation -> bean definition -> dependency injection -> initialization -> ready -> shutdown

To inspect the **Application Startup Lifecycle**
1. Implement the `ApplicationListener` interface
2. Use the `@EventListener` annotation.

## The Multi-Event Listener

This class captures the events that fire after the context exists (Started, Ready).
EnvironmentPrepared, ContextInitialized and ApplicationPrepared fire before the context exists, so a `@Component` can't receive them; register them in `main()` instead (next section).

```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.context.event.*;
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Slf4j
@Component
public class StartupCheckListener {

    // Application Started (Ready, but Runners haven't run)
    @EventListener
    public void handleApplicationStarted(ApplicationStartedEvent event) {
        log.info("--- 5. Application Started");
    }

    // Application Ready (Everything finished)
    @EventListener
    public void handleApplicationReady(ApplicationReadyEvent event) {
        log.info("--- 6. Application Ready! Time to serve traffic.");
    }
}
```

## Capturing the 1st event

Notes:
- Start-up event occur before even bean creation so it is best to capture at main.

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.context.event.ApplicationContextInitializedEvent;
import org.springframework.boot.context.event.ApplicationEnvironmentPreparedEvent;
import org.springframework.boot.context.event.ApplicationPreparedEvent;
import org.springframework.boot.context.event.ApplicationStartingEvent;
import org.springframework.context.ApplicationListener;

@SpringBootApplication
public class LifecycleApplication {

    public static void main(String[] args) {
        SpringApplication app = new SpringApplication(LifecycleApplication.class);

        // Manually adding a listener for the earliest possible event
        app.addListeners((ApplicationListener<ApplicationStartingEvent>) event -> {
            System.out.println("--- 1. Application Starting (Manual Listener Triggered)");
        });

        // 2. Environment Ready (Properties/Profiles loaded)
        app.addListeners((ApplicationListener<ApplicationEnvironmentPreparedEvent>) event -> {
            System.out.println("--- 2. Environment Prepared: Profiles: "
                + java.util.Arrays.toString(event.getEnvironment().getActiveProfiles()));
        });

        // 3. Context Initialized (Context created, no bean definitions yet)
        app.addListeners((ApplicationListener<ApplicationContextInitializedEvent>) event -> {
            System.out.println("--- 3. Context Initialized");
        });

        // 4. Application Prepared (Definitions loaded, not instantiated)
        app.addListeners((ApplicationListener<ApplicationPreparedEvent>) event -> {
            System.out.println("--- 4. Application Prepared (Beans loaded)");
        });

        app.run(args);
    }
}
```
# Bean

Article: https://medium.com/@bolot.89/understanding-the-spring-boot-bean-lifecycle-a-deep-dive-132d543efeaa

```java title="sample.java"
@Component  
public class MyService {  
    // ...  
}
```

## How to create

1. @Service | @Repository | @Controller
2. @Component
3. @Configuration & @Bean
4. XML config (not recommended since too verbose)

## Life-cycle

Instantiation -> DI -> Aware Interfaces -> `BeanPostProcessor` (before) -> Init Methods -> `BeanPostProcessor` (after) -> In use -> Destroy Methods

### Sample

```java
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.BeansException;
import org.springframework.beans.factory.BeanNameAware;
import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

@Slf4j
@Component
public class LifecycleInspectorBean implements 
        BeanNameAware, 
        ApplicationContextAware, 
        InitializingBean, 
        DisposableBean {

    private String beanName;

    // 1. Instantiation (Constructor)
    //   Dependencies injection can also occur here
    public LifecycleInspectorBean() {
        log.info("--- 1. INSTANTIATION: Bean object created via constructor.");
    }

    // 2. Populate Properties (@Value or @Autowired)
    @Value("${spring.application.name:DefaultApp}")
    public void setAppName(String name) {
        log.info("--- 2. POPULATE PROPERTIES: Injected property 'name': {}", name);
    }

    // 3. Aware Interfaces
    @Override
    public void setBeanName(String name) {
        this.beanName = name;
        log.info("--- 3. BEAN NAME AWARE: My name in the context is: {}", name);
    }

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        log.info("--- 3. CONTEXT AWARE: Received reference to the ApplicationContext.");
    }

    // 4. Initialization - @PostConstruct
    @PostConstruct
    public void postConstruct() {
        log.info("--- 4. @POSTCONSTRUCT: Custom init logic after dependency injection.");
    }

    // 4. Initialization - InitializingBean interface
    @Override
    public void afterPropertiesSet() throws Exception {
        log.info("--- 4. AFTER PROPERTIES SET: InitializingBean hook called.");
    }

    // 5. Ready for Use
    public void executeBusinessLogic() {
        log.info("--- 5. READY: Bean is performing its job.");
    }

    // 6. Destruction - @PreDestroy
    @PreDestroy
    public void preDestroy() {
        log.info("--- 6. @PREDESTROY: Cleaning up before Spring kills the bean.");
    }

    // 6. Destruction - DisposableBean interface
    @Override
    public void destroy() throws Exception {
        log.info("--- 6. DISPOSABLE BEAN: Final cleanup logic executed.");
    }
}
```

## Aware & Factory

Implement interface `BeanNameAware` to inject name.
Implement `FactoryBean<T>` to control how a bean is created; `BeanFactoryAware` to receive the `BeanFactory`.

When do we need to implement aware interface?
-> When we need to interact / access been related information like the BeanFactory or bean's name.

## Condition flag

```java
import org.springframework.boot.autoconfigure.condition.ConditionalOnProperty;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class NotificationConfiguration {

    @Bean
    @ConditionalOnProperty(
        prefix = "feature.notifications",
        name = "enabled",
        havingValue = "true",
        matchIfMissing = false // The bean will NOT be created if the property is absent
    )
    public NotificationService emailNotificationService() {
        return new EmailNotificationService();
    }
}
```

## Matching a bean

What to do if we have multiple bean implementing the same interface?

1. Create the bean in configuration bean and set unqiue function name -> match the name by that string
2. Use `@Qualifier`
3. `@Primary`

Create bean:
```java
public interface PaymentProcessor {
    void process(double amount);
}

@Component("paypalProcessor")
public class PaypalProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("Processing $" + amount + " via PayPal.");
    }
}

@Component("stripeProcessor")
public class StripeProcessor implements PaymentProcessor {
    @Override
    public void process(double amount) {
        System.out.println("Processing $" + amount + " via Stripe.");
    }
}
```

Matching bean:
```java
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.stereotype.Service;

@Service
public class CheckoutService {

    private final PaymentProcessor paymentProcessor;

    // Use @Qualifier in the constructor to pick the specific implementation
    public CheckoutService(@Qualifier("stripeProcessor") PaymentProcessor paymentProcessor) {
        this.paymentProcessor = paymentProcessor;
    }

    public void completeCheckout(double total) {
        paymentProcessor.process(total);
    }
}
```


## Why is it important?

- For Spring component scan & dependencies injection.

## Why Constructor injection is better than `@Autowired` 

1. Enforcing dependencies for creation
2. Clear schema contract
3. fields can be final (immutable)
4. testable with plain new, no Spring
5. Ensure compile-time safety

# Spring scope

| Scope | Description |
| --- | --- |
| Singleton | (Default) Scopes a single bean definition to a single object instance per Spring IoC container. |
| Prototype | Scopes a single bean definition to any number of object instances. A new instance is created every time it is requested. |
| Request | Scopes a single bean definition to the lifecycle of a single HTTP request (Web-aware). |
| Session | Scopes a single bean definition to the lifecycle of an HTTP Session (Web-aware). |

## Prototype scope in Singleton

The prototype is injected once when the singleton is created, so the singleton keeps the same instance forever (not per call or thread).
To fix the issue, we can do 1 of the below approach.

### @Lookup

```java
@Component
public class MySingleton {

    public void process() {
        // This call will return a NEW instance every time
        MyPrototype instance = getPrototype();
        instance.doWork();
    }

    @Lookup
    public MyPrototype getPrototype() {
        return null; // Spring overrides this at runtime
    }
}
```

### Inject factory

```java
@Component
public class MySingleton {
    private final ObjectFactory<MyPrototype> prototypeFactory;

    public MySingleton(ObjectFactory<MyPrototype> prototypeFactory) {
        this.prototypeFactory = prototypeFactory;
    }

    public void process() {
        // Each call to getObject() returns a new Prototype
        MyPrototype instance = prototypeFactory.getObject();
        instance.doWork();
    }
}
```

### Scoped proxy

```java
@Component
@Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyPrototype { 
    // ...
}
```

# [[Liquibase]] support

See [[Liquibase#Spring Boot integration]]

# [[Flyway]] support

See [[Flyway#Spring Boot integration]]

# [[JPA]]

Notes:
- [[JPA]] is the specification, [[Hibernate]] is the implementation, and [[HikariCP]] is the connection provider.

See [[JPA#Spring Boot config]]

# Spring [[HikariCP]]

See [[HikariCP]]

# [[Mongo DB]]

%% TODO: %%

# REST Controller

`@RestController` = `@Controller` + `@ResponseBody`: return values are written to the body by `HttpMessageConverters` (JSON when Jackson is present)

# Spring test

See [[Java testing]]

# Spring validation

%% TODO: %%

[[Maven]] dependency:
```xml
<!-- Maven -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## Sample validation

```java
class User {
	@Email
	private String email;
}
```

## Lifecycle

%% TODO: %%

# Spring [[REST API|REST]] Docs

[Document](https://docs.spring.io/spring-restdocs/docs/current/reference/htmlsingle)

Notes:
- We can [[Unit Test]] the document since this module also support testing.
- This is more like handwriting document

# [[Springdoc]] [[OpenAPI]] generation

See [[OpenAPI#Springdoc (Spring Boot)]]

# Spring actuator

Check out [[Spring-boot#Config file]] for actuator properties.

## Actuator metric

### Request

`http.server.requests` is statistic about all incoming request.
Good for monitoring with system like [[Prometheus]] / [[Grafana]]

## How to know all existing actuator ID?

Enable ALL actuator then view what is available with `/actuator`
```yaml
management:
	web:
		exposure:
			include: "*"
```

## Custom actuator

```java
@Component
@Endpoint(id = "features")
public class FeaturesEndpoint {

    private Map<String, Feature> features = new ConcurrentHashMap<>();

    @ReadOperation
    public Map<String, Feature> features() {
        return features;
    }

    @ReadOperation
    public Feature feature(@Selector String name) {
        return features.get(name);
    }

    @WriteOperation
    public void configureFeature(@Selector String name, Feature feature) {
        features.put(name, feature);
    }

    @DeleteOperation
    public void deleteFeature(@Selector String name) {
        features.remove(name);
    }

    public static class Feature {
        private Boolean enabled;

        // [...] getters and setters 
    }

}
```

## Extending existing actuator

Notes:
- This might cause failure if `info` actuator excluded / not included. Should you `@ConditionOnProperty` or some kind of flag to make this Bean not cause failure.

```java
@Component
@EndpointWebExtension(endpoint = InfoEndpoint.class)
public class InfoWebEndpointExtension {

    private InfoEndpoint delegate;

    // standard constructor

    @ReadOperation
    public WebEndpointResponse<Map> info() {
        Map<String, Object> info = this.delegate.info();
        Integer status = getStatus(info);
        return new WebEndpointResponse<>(info, status);
    }

    private Integer getStatus(Map<String, Object> info) {
        // return 5xx if this is a snapshot
        return 200;
    }
}
```

# Spring security

See [[Spring Security]]

# DTO Mapper pattern

Notes:
- DTO set at controller
- Mapping should be done at Service layer
- Mapping can be done manually or with `MapStruct` / `ModelMapper`

# Custom config param

## Use `@Value`

```java
@Value("${app.env1}")
private String env1;
```

## Use `@Configuration`

```java
@Configuration  
@ConfigurationProperties(prefix = "app.admin")  
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

## Configuration properties

Instead of `@Value("${...}")` to get env data, we can create a bean to save and access globally.
Also, with additional dependencies, we can even document and generate artifact to support properties file auto-complete for custom properties.

```properties
# Sample App Configuration
app.display-name=My Coding Project
app.version=1.0.2
app.max-connections=50
app.timeout-seconds=30
```

```java
import lombok.Data;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;

@Configuration
@ConfigurationProperties(prefix = "app")
@Data
public class AppConfig {
    private String displayName;
    private String version;
    private int maxConnections;
    private int timeoutSeconds;
}
```

# Startup task

## Task to run on app ready

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
            // Do stuff 
        } catch (Exception e) {  
            // TODO: add logger  
            System.out.println("Failed to create admin account: " + e.getMessage());  
        }  
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

Common properties can be found here: https://docs.spring.io/spring-boot/appendix/application-properties/index.html

## YML vs properties

- [[Yaml|YML]]:
	- Better structure
	- Easier to make mistake due to indentation
	- Might be harder for resolve [[Git]] conflict compare to [[properties]] since the config is on multiline
- [[properties]]:
	- Easier to resolve conflict

Note:
- In my opinion, we choose by preference. And I prefer [[Yaml]] format. We should rarely make change to config let alone create a conflict.

# Controller input type

%% TODO: %%

# Exception control

## Global exception handler

The `@ControllerAdvice` only catches exceptions thrown in the **DispatcherServlet** layer. If an exception happens in a **Filter** (e.g., during JWT authentication), your global handler won't see it.

```java title="ExceptionAdviceHandler.java"
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.context.request.WebRequest;
import java.time.LocalDateTime;

@ControllerAdvice
public class ExceptionAdviceHandler {

    // Handle specific exceptions
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorDetails> handleResourceNotFoundException(
            ResourceNotFoundException ex, WebRequest request) {
        
        ErrorDetails errorDetails = new ErrorDetails(
            LocalDateTime.now(), 
            ex.getMessage(), 
            request.getDescription(false)
        );
        
        return new ResponseEntity<>(errorDetails, HttpStatus.NOT_FOUND);
    }

    // Handle global exceptions (Fallback)
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorDetails> handleGlobalException(
            Exception ex, WebRequest request) {
        
        ErrorDetails errorDetails = new ErrorDetails(
            LocalDateTime.now(), 
            "An unexpected error occurred", 
            request.getDescription(false)
        );
        
        return new ResponseEntity<>(errorDetails, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

## Controller-level exception handler

```java
@RestController
public class OrderController {

    @GetMapping("/orders/{id}")
    public Order getOrder(@PathVariable String id) {
        // ... logic
        throw new OrderNotFoundException("Order missing");
    }

    // This only catches exceptions thrown within OrderController
    @ExceptionHandler(OrderNotFoundException.class)
    public ResponseEntity<String> handleOrderError(OrderNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```

## Filter-level exception handler

```java
@Component
public class JwtFilter extends OncePerRequestFilter {
    @Autowired
    @Qualifier("handlerExceptionResolver")
    private HandlerExceptionResolver resolver;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) {
        try {
            filterChain.doFilter(request, response);
        } catch (Exception e) {
            // This sends the error to your @ControllerAdvice
            resolver.resolveException(request, response, null, e);
        }
    }
}
```

# Spring middleware

## Filter as "Servlet Middleware"

Filters are part of the [[Servlet]] Container ([[Tomcat]]). They are the "outermost" layer of protection. They execute **before** the request even reaches Spring's `DispatcherServlet`.
- Best for: Tasks that don't need to know which controller method is being called.
- Examples: Logging raw HTTP headers, CORS, GZIP compression, and security (Spring Security is essentially a chain of filters).
- Key Interface: `jakarta.servlet.Filter`

```java
@Component
public class MyFilter implements Filter {
    @Override
    public void doFilter(ServletRequest req, ServletResponse res, FilterChain chain) 
            throws IOException, ServletException {
        // 1. BEFORE (Pre-processing)
        chain.doFilter(req, res); // 2. PASS TO NEXT
        // 3. AFTER (Post-processing)
    }
}
```

Preferred / modern approach:
```java
import org.springframework.web.filter.OncePerRequestFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@Component
public class TransactionFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        
        String requestId = request.getHeader("X-Request-ID");
        System.out.println("Processing request ID: " + requestId);
        
        filterChain.doFilter(request, response);
    }
}
```
## Interceptor as "Spring MVC Middleware"

Interceptors are a Spring-specific concept. They execute after the request is accepted by the `DispatcherServlet` but before it hits your `@RestController`.
- Best for: Tasks that need to know about the "Handler" (the specific method being called) or need access to Spring's Model and View.
- Examples: Checking custom annotations on a controller method, measuring execution time of a specific API, or modifying the ModelAndView.
- Key Interface: `HandlerInterceptor`

```java
@Component
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        // Runs before the controller
        return true; // Continue execution
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {
        // Runs after the controller, before view rendering
    }
}
```

# Spring [[Aspect oriented programming|AOP]]

Spring package for [[Aspect oriented programming|AOP]].

Pattern `execution(modifiers-pattern? ret-type-pattern declaring-type-pattern? name-pattern(param-pattern) throws-pattern?)`.
- `*`: Matches any return type or name.
- `..`: Matches any number of arguments.

Notes:
- Spring [[Aspect oriented programming|AOP]] work via proxy so it won't work if invoked from within the same class.

```java title="LoggingAspect.java"
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {

    // Pointcut expression: Matches any method in UserService
    @Before("execution(* com.example.service.UserService.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("AOP Before: Calling method: " + joinPoint.getSignature().getName());
    }

    @AfterReturning(pointcut = "execution(* com.example.service.UserService.*(..))", returning = "result")
    public void logAfter(JoinPoint joinPoint, Object result) {
        System.out.println("AOP After: Method " + joinPoint.getSignature().getName() + " returned: " + result);
    }
    
    @Around("execution(* com.example.service.UserService.*(..))")
	public Object measureExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
	    long start = System.currentTimeMillis();
	
	    // This line actually executes the business method
	    Object result = joinPoint.proceed(); 
	
	    long executionTime = System.currentTimeMillis() - start;
	    System.out.println(joinPoint.getSignature() + " executed in " + executionTime + "ms");
	    
	    return result;
	}
}
```

## Spring Proxy

%% TODO %%

# Caching

%% TODO: %%

# Serve SPA site

```properties
# Define where Spring should look for static files
spring.web.resources.static-locations=classpath:/my-react-build/,file:/var/www/static/

# All static files will now be served under /dashboard/
spring.mvc.static-path-pattern=/dashboard/**

# Enable caching for 1 year for static resources
spring.web.resources.cache.period=31536000
# Ensure that if the file changes, the cache is busted (uses fingerprinting)
spring.web.resources.chain.strategy.content.enabled=true
spring.web.resources.chain.strategy.content.paths=/**

# Map the default error path to index.html
server.error.path=/index.html
```

%% verify; usual approach is a forward controller for non-file routes %%

# [[Hibernate]] nested [[SQL]] transaction

%% TODO: %%

# [[Spnego]] integration

See [[Spring Security]]

# Mock Rest Service Server

%% TODO: MockRestServiceServer %%

# Spring Rest client

Spring's Rest client is a new [[HTTP]] client, replacement for the old Rest Template

Example:
Config:
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.client.RestClient;

@Configuration
public class ClientConfig {

    @Bean
    public RestClient restClient() {
        return RestClient.builder()
                .baseUrl("https://api.example.com")
                // Adding our middleware here
                .requestInterceptor(new CustomClientMiddleware())
                .defaultHeader("Content-Type", "application/json")
                .build();
    }
}
```

Middleware:
```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpRequest;
import org.springframework.http.client.ClientHttpRequestExecution;
import org.springframework.http.client.ClientHttpRequestInterceptor;
import org.springframework.http.client.ClientHttpResponse;
import java.io.IOException;
import java.util.UUID;

@Slf4j
public class CustomClientMiddleware implements ClientHttpRequestInterceptor {

    @Override
    public ClientHttpResponse intercept(HttpRequest request, byte[] body, ClientHttpRequestExecution execution) 
            throws IOException {
        
        // 1. Pre-processing: Add a header
        request.getHeaders().add("X-Correlation-ID", UUID.randomUUID().toString());
        log.info("Sending {} request to {}", request.getMethod(), request.getURI());

        // 2. Execute the request
        ClientHttpResponse response = execution.execute(request, body);

        // 3. Post-processing: Log the status
        log.info("Received response with status: {}", response.getStatusCode());
        
        return response;
    }
}
```


## Config [[SSL]]

%% TODO: %%

## Config timeout

%% TODO: %%

# Create File API

Article: https://spring.io/guides/gs/uploading-files
File [[Blob]] API.

%% TODO: %%

```java
package com.example.spring_1.file;  
  
import lombok.RequiredArgsConstructor;  
import org.springframework.core.io.Resource;  
import org.springframework.http.HttpStatus;  
import org.springframework.http.MediaType;  
import org.springframework.http.ResponseEntity;  
import org.springframework.web.bind.annotation.*;  
import org.springframework.web.multipart.MultipartFile;  
  
import java.io.IOException;  
import java.util.List;  
import java.util.Map;  
  
@RestController  
@RequestMapping("/api/v1/files")  
@RequiredArgsConstructor  
public class FileController {  
  
    private final StorageService storageService;  
  
    @GetMapping  
    public List<String> listFile() {  
        return null;  
    }  
  
    @GetMapping(value = "/{filename:.+}", produces = MediaType.APPLICATION_OCTET_STREAM_VALUE)  
    public ResponseEntity<Resource> downloadFile(@PathVariable String filename) {  
        return null;  
    }  
  
//    @PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)  
//    @ResponseStatus(HttpStatus.NO_CONTENT)  
//    public void uploadFile(@RequestParam("file")MultipartFile file) throws IOException {  
//        var filename = file.getOriginalFilename();  
//        var data = file.getBytes();  
//  
//        // TODO:  
//    }  
  
    // NOTE: @RequestPart allow use to send both BLOB data & JSON
    @PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)  
    @ResponseStatus(HttpStatus.NO_CONTENT)  
    public void uploadFile(  
            @RequestPart("file") MultipartFile file,  
            @RequestPart("metadata") Map<String, String> additionalData) throws IOException {  
        var filename = file.getOriginalFilename();  
        var data = file.getBytes();  
  
        // TODO:  
    }  
}
```

## Upload

### Old

### New

## Download

### Download [[Byte]]

### Download [[IO Stream]]

# Configurable log

Spring Boot uses Logback by default (Log4j2 via `spring-boot-starter-log4j2`); set `logging.level.<pkg>=DEBUG`. See [[Spring-boot#Config file]].

%% TODO: %%

# Path validation

Below is an example how we can do sample validation for route params with [[Regex]]

`{filename:.+}` restricts the path variable by regex; `.+` keeps the extension in older Spring where suffix matching truncated it.
