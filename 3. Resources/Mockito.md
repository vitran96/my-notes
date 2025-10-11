[[Java]] testing library. Can be used together with [[JUnit]]. Used to mock / stub data / function. Usually used in [[Unit Test]].
Mockito also works well with [[Spring-boot]].

# Prepare mocking
`@InjectMock` to choose which class to inject mock to.
`@Mock` to mock a class and inject into `@InjectMock`
`@Spy` can be used to track the change.