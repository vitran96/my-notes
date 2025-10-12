[[Java]] testing library. Can be used together with [[JUnit]]. Used to mock / stub data / function. Usually used in [[Unit Test]].
Mockito also works well with [[Spring-boot]].

[Test configuration](https://medium.com/simform-engineering/testing-spring-boot-applications-best-practices-and-frameworks-6294e1068516)

Sample
```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void shouldReturnUserNameWhenUserExists() {
        when(userRepository.findById(1L)).thenReturn(Optional.of(new User(1L, "Alice")));

        String result = userService.getUserName(1L);

        assertEquals("Alice", result);
    }
}
```

# Prepare mocking
`@InjectMock` to choose which class to inject mock to.
`@Mock` to mock a class and inject into `@InjectMock`
`@Spy` can be used to track the change.

# Mocking
`when` -> `then`