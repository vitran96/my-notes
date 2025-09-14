Build on top of [[Spring]]. Mainly use [[Java]] but also support [[Kotlin]], [[Groovy]].
This is the most mature web-framework for [[Java]] ecosystem (beat [[vertx]] in my opinion)

# Init command

```shell
spring init `
  --boot-version=3.5.5 `
  --java-version=21 `
  --build=maven `
  --language=java `
  --group-id=com.example `
  --artifact-id=demo `
  --name=demo `
  --package-name=com.example.demo `
  --dependencies=web,validation,data-jpa,h2,security,actuator,devtools,lombok,testcontainers,mysql,restdocs `
  --extract `
  demo

```

# Spring-doc dependencies

```xml
<!-- pom.xml -->
<dependency>
  <groupId>org.springdoc</groupId>
  <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
  <version>2.x</version> <!-- use latest 2.x -->
</dependency>
```