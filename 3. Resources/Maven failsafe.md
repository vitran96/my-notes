---
para: resource
site: https://maven.apache.org/surefire/maven-failsafe-plugin/
tags: [java, testing]
---
[[Maven]] plugin to run test.
It is suggested for [[Integration Test]] only.
It will run at `verify` stage with `mvn verify`
Goals: `integration-test` (runs tests) + `verify` (fails the build), so run `mvn verify`, not `mvn integration-test`.

# File lookup pattern

- `IT*.java`
- `*IT.java`
- `*ITCase.java`

# Report

`target/failsafe-reports/failsafe-summary.xml` and `TEST-*.xml`

# Skip

This can be skip with
- `-DskipTests=True`. This one is a bit dangerous as it will skip [[Maven surefire]] too.
- `-DskipITs=True`

# Choose specific test

- `-Dit.test=<Class#method>,...` (not `-Dtest`, which is surefire's)

See also: [[Jacoco]], [[Test Containers]]
