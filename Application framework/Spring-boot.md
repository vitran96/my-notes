Build on top of [[Spring]]. Mainly use [[Java]] but also support [[Kotlin]], [[Groovy]].
This is the most mature web-framework for [[Java]] ecosystem (beat [[vertx]] in my opinion)

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
URL can be used from datasource
```yaml
spring:
  liquibase:
    change-log: classpath:/db/migrations/changelog.xml
    url: jdbc:mysql://localhost:3306/mydatabase
	user: myuser
	password: mypassword
    enabled: false
```

# Config DB & JPA
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