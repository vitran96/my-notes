# Change set with SQL
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

# Maven plugin
```xml
```
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
```