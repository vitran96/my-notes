# Change set with [[SQL]]
[SqlFile](https://docs.liquibase.com/reference-guide/change-types/sqlfile)

```xml
<databaseChangeLog
  xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:ext="http://www.liquibase.org/xml/ns/dbchangelog-ext"
  xmlns:pro="http://www.liquibase.org/xml/ns/pro"
    xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-latest.xsd
        http://www.liquibase.org/xml/ns/dbchangelog-ext
        http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-ext.xsd
        http://www.liquibase.org/xml/ns/pro
        http://www.liquibase.org/xml/ns/pro/liquibase-pro-latest.xsd">
  <changeSet author="liquibase-docs" id="sqlFile-example" context="sqlFile-context" labels="sqlFile-label">
    <sqlFile dbms="!h2, oracle, mysql"
            encoding="UTF-8"
            endDelimiter="\nGO"
            path="my/path/file.sql"
            relativeToChangelogFile="true"
            splitStatements="true"
            stripComments="true"/>
    <rollback>
      <sqlFile path="my/path/rollback.sql"/>
    </rollback>
  </changeSet>
</databaseChangeLog>
```

# [[Maven]] plugin
```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.liquibase</groupId>
			<artifactId>liquibase-maven-plugin</artifactId>
			<version>4.5.0</version>
			<configuration>
				<propertyFile>
					src/main/resources/liquibase.yml
				</propertyFile>
			</configuration>
		</plugin>
	</plugins>
</build>
```

# Properties file sample
[Guide](https://docs.liquibase.com/pro/user-guide-4-33/what-is-the-liquibase-properties-file)
```properties
changelogFile: ../path/to/file/dbchangelog.xml
driver: oracle.jdbc.OracleDriver
url: jdbc:oracle:thin:@192.168.0.22:1521/orcl
referenceDriver: oracle.jdbc.OracleDriver
referenceUrl: jdbc:oracle:thin:@192.168.0.22:1521/orcl
licenseKey: aeioufakekey32aeioufakekey785463214
classpath: ../path/to/file/ojdbc6-11.2.0.3.0.jar
```

```yaml
changelogFile: ../path/to/file/dbchangelog.xml
driver: oracle.jdbc.OracleDriver
url: jdbc:oracle:thin:@192.168.0.22:1521/orcl
referenceDriver: oracle.jdbc.OracleDriver
referenceUrl: jdbc:oracle:thin:@192.168.0.22:1521/orcl
licenseKey: aeioufakekey32aeioufakekey785463214
classpath: ../path/to/file/ojdbc6-11.2.0.3.0.jar
```

# Changelog sample
```yaml
spring:
	enable: aaa
```

# Generate changelog
[[JPA Buddy]] is a good alternative but might require licensing.

## Generate via [[Maven]] plugins
[Guide](https://www.baeldung.com/liquibase-refactor-schema-of-java-app#2-generate-a-changelog-from-an-existing-database)
```
mvn liquibase:generateChangeLog
```

## Generate via comparing 2 DB
[Guide](https://www.baeldung.com/liquibase-refactor-schema-of-java-app#3-generate-a-changelog-from-diffs-between-two-databases)
```shell
mvn liquibase:diff
```

Reference config must be set
```properties
changeLogFile=src/main/resources/config/liquibase/master.xml
url=jdbc:h2:file:./target/h2db/db/carapp 
username=carapp 
password= 
driver=com.mysql.jdbc.Driver 
referenceUrl=jdbc:h2:file:./target/h2db/db/carapp2 
referenceDriver=org.h2.Driver 
referenceUsername=tutorialuser2 
referencePassword=tutorialmy5ql2
```

## Generate base on [[JPA]] & DB context variable.
[Guide](https://www.baeldung.com/liquibase-refactor-schema-of-java-app#hibernate)
### Config
```xml
<plugin>
   <groupId>org.liquibase</groupId>
   <artifactId>liquibase-maven-plugin</artifactId>
   <version>4.27.0</version>
   <dependencies>
      ...
      <dependency>
         <groupId>org.liquibase.ext</groupId>
         <artifactId>liquibase-hibernate5</artifactId>
         <version>3.6</version>
      </dependency>
     ...
   </dependencies>
   ...
</plugin>
```

## Liquibase [[Maven]] configuration sample
```xml
<configuration>
   <changeLogFile>src/main/resources/config/liquibase/master.xml</changeLogFile>
   <diffChangeLogFile>src/main/resources/config/liquibase/changelog/${maven.build.timestamp}_changelog.xml</diffChangeLogFile>
   <driver>org.h2.Driver</driver>
   <url>jdbc:h2:file:./target/h2db/db/carapp</url>
   <defaultSchemaName />
   <username>carapp</username>
   <password />
   <referenceUrl>hibernate:spring:com.car.app.domain?dialect=org.hibernate.dialect.H2Dialect
    &hibernate.physical_naming_strategy=org.springframework.boot.orm.jpa.hibernate.SpringPhysicalNamingStrategy
    &hibernate.implicit_naming_strategy=org.springframework.boot.orm.jpa.hibernate.SpringImplicitNamingStrategy
   </referenceUrl>
   <verbose>true</verbose>
   <logging>debug</logging>
</configuration>
```