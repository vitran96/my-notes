Static analysis tool to check for common code mistake, bug pattern.
Can only be used as part of compiler.

# Alternative

[[Java-Error prone]]  doesn't work well with [[Java#Java 17]]+.
So alternatives are:
- [[Java-Checkstyle]] & [[Java-PMD]]
- [[ArchUnit]]
- [[SpotBugs]]
- [[Sonarlint]]

[[Mend.io]] focus is on Security and compliance. It can not be 100% alternative for [[Java-Error prone]].

# [[Maven]] config

```xml
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.11.0</version>
        <configuration>
          <source>17</source>
          <target>8</target>
          <encoding>UTF-8</encoding>
          <compilerArgs>
            <arg>-XDcompilePolicy=simple</arg>
            <arg>--should-stop=ifError=FLOW</arg>
            <arg>-Xplugin:ErrorProne</arg>
          </compilerArgs>
          <annotationProcessorPaths>
            <path>
              <groupId>com.google.errorprone</groupId>
              <artifactId>error_prone_core</artifactId>
              <version>${error-prone.version}</version>
            </path>
            <!-- Other annotation processors go here.

            If 'annotationProcessorPaths' is set, processors will no longer be
            discovered on the regular -classpath; see also 'Using Error Prone
            together with other annotation processors' below. -->
          </annotationProcessorPaths>
        </configuration>
      </plugin>
    </plugins>
  </build>
```

