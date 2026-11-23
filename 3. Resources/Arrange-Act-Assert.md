---
para: resource
tags: [testing]
---
Test structure pattern for [[Unit Test]], [[Integration Test]] and [[E2E]] tests: three blocks per test.

## Key points

- Arrange: build inputs, mocks, state.
- Act: call the one behaviour under test.
- Assert: check the outcome. Same idea as [[Gherkin]] Given/When/Then. One Act per test.

## Examples

See the sample in [[Mockito]]; used with [[JUnit]].
