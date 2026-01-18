[[Maven]] plugin to run test.
It is suggested for [[Unit Test]] only.
It run at `test` stage with `mvn test`

# File lookup pattern

- `Test*.java`
- `*Test.java`
- `*Tests.java`
- `*TestCase.java`

# Report

`target/surefire-reports/TEST-*.xml`

# Skip

Test can be skip with 
- `-DskipTests=true`. This one is however a bit dangerous as it might stop [[Maven failsafe]] too.

# Choose test to run

- `-Dtest=<class#method>,...`