---
para: resource
tags: [tooling]
---
OpenAPI Specification (formerly Swagger): YAML/JSON description of a REST API. openapi-generator generates clients/servers from it (site: https://openapi-generator.tech/docs/installation/)

# CLI

%% TODO: get sample command from a past project %%
Check out generator list to see what options you have: https://openapi-generator.tech/docs/generators

Notes:
- The tool support relative path.
- `npx @openapitools/openapi-generator-cli` needs a Java runtime (it downloads the jar).

```shell
npx @openapitools/openapi-generator-cli generate -i petstore.yaml -g ruby -o /tmp/test/
openapi-generator-cli generate -i petstore.yaml -g ruby -o /tmp/test/
```

# [[Maven]] plugin

https://openapi-generator.tech/docs/plugins

If you just need to generate code, there is a plugin to generate the client.
This approach is not as useful as generate the whole source code to use as an independent package.

For a TypeScript client with [[Redux]], see [[Redux#RTK Query]].

# Springdoc (Spring Boot)

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

### [[WebFlux]]

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

## Set base url to root

%% TODO: %%

## Inject example to all route

%% TODO: %%
