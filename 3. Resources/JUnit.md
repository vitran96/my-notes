---
para: resource
tags: [java, testing]
---
[[Java]] [[Unit Test]] framework for [[Software testing]].
Can be used for [[Integration Test]] too.
Can even cover [[E2E]] but not recommended.

# Naming convention

Maven Surefire picks up `Test*.java`, `*Test.java`, `*Tests.java`, `*TestCase.java` by default (see [[Maven surefire]]); integration tests `*IT.java` run via [[Maven failsafe]].

# Extensions

## Built-in

Document: https://docs.junit.org/6.1.0/writing-tests/built-in-extensions.html

### @TempDir

From [[JUnit]] 5.4+, there is a built-in `@TempDir` to manage temporary directory for testing.

See also: [[Mockito]], [[Arrange-Act-Assert]], [[Test Containers]], [[Java testing]]
