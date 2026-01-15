[[Java]] [[Code coverage]] tool

[Guide](https://www.baeldung.com/jacoco)

# Plugin

## Merged report

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.11</version>
    <executions>
        <execution>
            <id>prepare-agent</id>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
            <configuration>
                <destFile>${project.build.directory}/jacoco-all.exec</destFile>
            </configuration>
        </execution>
        <execution>
            <id>prepare-agent-it</id>
            <goals>
                <goal>prepare-agent-integration</goal>
            </goals>
            <configuration>
                <destFile>${project.build.directory}/jacoco-all.exec</destFile>
                <append>true</append>
            </configuration>
        </execution>
        <execution>
            <id>report</id>
            <phase>verify</phase> <goals>
                <goal>report</goal>
            </goals>
            <configuration>
                <dataFile>${project.build.directory}/jacoco-all.exec</dataFile>
                <outputDirectory>${project.reporting.outputDirectory}/jacoco-merged</outputDirectory>
            </configuration>
        </execution>
    </executions>
</plugin>
```

## Separated report for [[Unit Test]] and [[Integration Test]]

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.7.7.201606060606</version>
    <executions>
        <execution>
            <id>pre-unit-test</id>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>

        <execution>
            <id>pre-integration-test</id>
            <goals>
                <goal>prepare-agent-integration</goal>
            </goals>
        </execution>

        <execution>
            <id>post-unit-test</id>
            <phase>test</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
        <execution>
            <id>post-integration-test</id>
            <phase>verify</phase>
            <goals>
                <goal>report-integration</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

# Report

`target/site/jacoco/index.html`