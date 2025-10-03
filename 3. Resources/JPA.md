# Generate Entity
[Article](https://stackoverflow.com/questions/14956665/generate-jpa2-entities-from-existing-database-using-maven)

## jpa-entity-generator
[Repo](https://github.com/smartnews/jpa-entity-generator)
Issues:
- Cannot remove default `@Data` annotation
- For some reason, the latest update is note released and it **NEEDED** to support `jakarta` package (rename from `javax`)

Notes:
- Good enhancement can be found from this [repo](https://github.com/pierrickrouxel/jpa-entity-generator/tree/main)

### Maven dependancies
```xml
<plugin>
  <groupId>com.smartnews</groupId>
  <artifactId>maven-jpa-entity-generator-plugin</artifactId>
  <version>0.99.8</version>
  <dependencies>
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.46</version>
    </dependency>
  </dependencies>
</plugin>
```

### Config & Run
1. Create the `src/main/resources/entityGenConfig.yml` ([sample](https://raw.githubusercontent.com/smartnews/jpa-entity-generator/6cca92a226d5225b2d8316bc205b77941f86281e/src/test/resources/entityGenConfig4.yml))
	```yaml title="src/main/resources/entityGenConfig.yml"
	jdbcSettings:
		url: "jdbc:h2:file:./db/blog;MODE=MySQL"
		username: "user"
		password: "pass"
		driverClassName: "org.h2.Driver"
	packageName: "com.example.entity"
	useJakarta: true
    ```
2. Run command: `mvn jpa-entity-generator:generateAll`

## hibernate-tools-maven-plugin
Official plugin from [[Hibernate]]
[Guide](https://web.archive.org/web/20201013105933/https://jonamlabs.com/how-to-use-hibernate-tools-maven-plugin-to-generate-jpa-entities-from-an-existing-database/)

### [[Maven]] plugin
```xml
<plugin>  
    <groupId>org.hibernate.tool</groupId>  
    <artifactId>hibernate-tools-maven</artifactId>  
    <version>${hibernate-tools.version}</version>  
    <configuration>  
        <propertyFile>${project.basedir}/hibernate.properties</propertyFile>  
        <revengFile>${project.basedir}/hibernate.reveng.xml</revengFile>  
    </configuration>  
    <dependencies>  
        <!-- DB Driver of your database -->  
        <dependency>  
            <groupId>com.mysql</groupId>  
            <artifactId>mysql-connector-j</artifactId>  
            <version>${mysql.version}</version>  
        </dependency>  
    </dependencies>  
</plugin>
```

### Properties file
```properties
hibernate.dialect=org.hibernate.dialect.MySQLDialect  
hibernate.connection.url=jdbc:mysql://localhost:3306/spring_1  
hibernate.connection.driver_class=com.mysql.cj.jdbc.Driver  
hibernate.connection.username=user1  
hibernate.connection.password=pass1234
```

### Reverse engineer file
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<!DOCTYPE hibernate-reverse-engineering SYSTEM "http://hibernate.org/dtd/hibernate-reverse-engineering-3.0.dtd" >  
  
<hibernate-reverse-engineering>  
    <table-filter match-name="DATABASECHANGELOG" exclude="true"/>  
    <table-filter match-name="DATABASECHANGELOGLOCK" exclude="true"/>  
</hibernate-reverse-engineering>
```

### Commands
```shell
# Generate
mvn hibernate-tools:hbm2java
```
## [[Lombok]] `@Data` annotation should be avoided
It is not recommended to add `@Data` annotation to Entity since it can break the [[Lazy evaluation]]