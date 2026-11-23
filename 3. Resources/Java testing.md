---
para: resource
tags: [java, testing]
---
Overview of the Java test stack; pick per layer.

## Stack

- Unit: [[JUnit]] + [[Mockito]], structured as [[Arrange-Act-Assert]]; run by [[Maven surefire]] (`*Test`)
- Integration: [[Test Containers]]; run by [[Maven failsafe]] (`*IT`)
- BDD / acceptance: [[Cucumber]] + [[Gherkin]]
- Coverage: [[Jacoco]]
- Hooks: [[Lefthook]]

## Spring Boot tests

Caveat: `@SpringBootTest` loads the full context, so DB migrations run; use `@WebMvcTest` for a web-only slice.

### Unit Test

%% TODO: %%

Don't need `@SpringBootTest` if not load Spring app context.
Mocking with [[Mockito]] and run test with [[JUnit]].

### Integration test

%% TODO: %%

#### Mock external service

%% TODO: %%

#### [[Test Containers]] usage

Articles:
- https://blog.jetbrains.com/idea/2024/12/testing-spring-boot-applications-using-testcontainers/

##### Create container as bean

```java title="MySqlTestConfiguration.java"
package com.example.spring_1;  
  
import org.springframework.boot.test.context.TestConfiguration;  
import org.springframework.context.annotation.Bean;  
import org.testcontainers.containers.MySQLContainer;  
import org.testcontainers.utility.DockerImageName;  
  
@TestConfiguration(proxyBeanMethods = false)  
public class MySqlTestConfiguration {  
  
    // NOTE: cannot contain "repo name" in image name (eg: "docker.io/")
//    private static final String MYSQL_IMAGE = "docker.io/mysql:9.4.0-oraclelinux9";  
    private static final String MYSQL_IMAGE = "mysql:9.4.0-oraclelinux9";  
  
    @Bean  
    @ServiceConnection
    MySQLContainer<?> mySQLContainer() {  
        return new MySQLContainer<>(DockerImageName.parse(MYSQL_IMAGE));  
    }  
}
```

```java title="AuthControllerIntegrationTest"
package com.example.spring_1.authentication;  
  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.boot.testcontainers.service.connection.ServiceConnection;  
import org.springframework.context.annotation.Import;  
import org.testcontainers.containers.MySQLContainer;  
import org.testcontainers.junit.jupiter.Container;  
import org.testcontainers.junit.jupiter.Testcontainers;  
import org.testcontainers.utility.DockerImageName;  
import com.example.spring_1.MySqlTestConfiguration;  

// NOTE: use @Testcontainers + @Contiainerto auto start / stop container
@SpringBootTest  
@Import(MySqlTestConfiguration.class) // Import your configuration
public class AuthControllerIntegrationTest {  
  
    // NOTE: use @ServiceConnection to auto update datasource properties
    //   @DynamicPropertySource can be used for manual override
    @Autowired
    private MySQLContainer<?> mySQLContainer;
}
```

##### Create container managed by [[JUnit]]

```java title="AuthControllerIntegrationTest"
package com.example.spring_1.authentication;  
  
import org.springframework.beans.factory.annotation.Autowired;  
import org.springframework.boot.test.context.SpringBootTest;  
import org.springframework.boot.testcontainers.service.connection.ServiceConnection;  
import org.springframework.context.annotation.Import;  
import org.testcontainers.containers.MySQLContainer;  
import org.testcontainers.junit.jupiter.Container;  
import org.testcontainers.junit.jupiter.Testcontainers;  
import org.testcontainers.utility.DockerImageName;  
import com.example.spring_1.MySqlTestConfiguration;  

// NOTE: use @Testcontainers + @Contiainerto auto start / stop container
@SpringBootTest  
@Testcontainers  
public class AuthControllerIntegrationTest {  
  
    // NOTE: use @ServiceConnection to auto update datasource properties
    //   @DynamicPropertySource can be used for manual override
    @Container  
    @ServiceConnection
    private static MySQLContainer<?> mySQLContainer = new MySQLContainer<>(DockerImageName.parse("mysql:9.4.0-oraclelinux9"));
}
```

### MockMVC

MockMVC can be used as a [[Integration Test]] to test security.
However, it might fail if we have a strict [[Flyway]]/[[Liquibase]] setup.

```java
@SpringBootTest
@AutoConfigureMockMvc
class SecurityPolicyTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void shouldRejectAnonymousUser() throws Exception {
        mockMvc.perform(get("/api/admin/dashboard"))
               .andExpect(status().isUnauthorized());
    }

    @Test
    @WithMockUser(roles = "USER")
    void shouldForbiddenUserFromAdminArea() throws Exception {
        mockMvc.perform(get("/api/admin/dashboard"))
               .andExpect(status().isForbidden());
    }

    @Test
    @WithMockUser(roles = "ADMIN")
    void shouldAllowAdminToAccessDashboard() throws Exception {
        mockMvc.perform(get("/api/admin/dashboard"))
               .andExpect(status().isOk());
    }
}
```

### TestRestTemplate vs MockMvc

Article:
- https://docs.spring.io/spring-framework/reference/testing/mockmvc/vs-end-to-end-integration-tests.html

Not recommended TestRestTemplate for everything since it is slow? Why slow?
- Spring boot will have to set up server and open port
- TestRestTemplate will have to use network to test

### RestTestClient vs TestRestTemplate

Introduction: https://www.baeldung.com/spring-resttestclient-guide
