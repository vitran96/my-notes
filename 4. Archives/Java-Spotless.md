We can use [[Maven]] plugin: `spotless` for [[Code-linting]] and [[Code-formatting]].
[Guide](https://www.baeldung.com/java-maven-spotless-plugin)
```xml
<plugin>
    <groupId>com.diffplug.spotless</groupId>
    <artifactId>spotless-maven-plugin</artifactId>
    <version>2.43.0</version>
    <configuration>
        <java>
            <googleJavaFormat/>
        </java>
    </configuration>
</plugin>
```

Command:
```shell
# Linting
mvn spotless:check

# Foramtting
mvn spotless:apply
```