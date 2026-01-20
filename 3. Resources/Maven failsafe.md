---
site: https://maven.apache.org/surefire/maven-failsafe-plugin/
---

[[Maven]] plugin to run test.
It is suggested for [[Integration Test]] only.
It will run at `verify` stage with `mvn verify`

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

- `-Dtest=<class#method>,...`