[[Java]] [[Unit Test]] framework for [[Software testing]].
Can be used for [[Integration Test]] too.
Can even cover [[E2E]] but not recommended.

# Naming convention

By default, build tool like `maven` expect suffix `*Test`.

# Extensions

## Built-in

Document: https://docs.junit.org/6.1.0/writing-tests/built-in-extensions.html

### @TempDir

From [[JUnit]] 5+, there is a built-in `@TempDir` to manage temporary directory for testing.
