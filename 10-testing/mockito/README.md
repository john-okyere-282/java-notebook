# Mockito

## What Is Mocking?

A unit test should test one class in isolation. The problem is that most classes depend on others — a `UserService` might depend on a `UserRepository` that queries a database. Calling the real database in a unit test makes the test slow, fragile, and hard to control.

**Mocking** replaces a real dependency with a fake object (a mock) that you control. You tell the mock what to return for specific inputs, and you can verify afterward that it was called correctly.

---

## Maven Dependency

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.8.0</version>
    <scope>test</scope>
</dependency>

<!-- Required for JUnit 5 integration -->
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.8.0</version>
    <scope>test</scope>
</dependency>
```

---

## Setting Up Mockito with JUnit 5

Use `@ExtendWith(MockitoExtension.class)` to activate Mockito's JUnit 5 integration.

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    UserRepository userRepository;   // Mockito creates a fake UserRepository

    @InjectMocks
    UserService userService;         // Mockito creates UserService and injects the mock into it

}
```

| Annotation | Description |
|------------|-------------|
| `@Mock` | Creates a mock (fake) of the given class or interface |
| `@InjectMocks` | Creates an instance of the class under test and injects all `@Mock` fields into it |
| `@ExtendWith(MockitoExtension.class)` | Activates Mockito annotations |

---

## when().thenReturn() — Stubbing

Tell the mock what to return when a specific method is called.

```java
@Test
void should_returnUser_when_userExists() {
    // Arrange
    User alice = new User(1L, "Alice", "alice@example.com");
    when(userRepository.findById(1L)).thenReturn(Optional.of(alice));

    // Act
    User result = userService.getUserById(1L);

    // Assert
    assertEquals("Alice", result.getName());
}
```

If the method is called with an argument that was not stubbed, it returns the default for the type (`null` for objects, `0` for numbers, `false` for booleans, empty collections for lists).

### Argument matchers

```java
// Match any Long argument
when(userRepository.findById(anyLong())).thenReturn(Optional.of(alice));

// Match any String
when(userRepository.findByEmail(anyString())).thenReturn(Optional.empty());

// Match exact value
when(userRepository.findByEmail("alice@example.com")).thenReturn(Optional.of(alice));
```

---

## when().thenThrow() — Simulating Failures

```java
@Test
void should_throwException_when_userNotFound() {
    when(userRepository.findById(99L)).thenReturn(Optional.empty());

    assertThrows(
        UserNotFoundException.class,
        () -> userService.getUserById(99L)
    );
}

@Test
void should_propagateException_when_databaseFails() {
    when(userRepository.findById(anyLong()))
        .thenThrow(new RuntimeException("Database connection lost"));

    assertThrows(RuntimeException.class, () -> userService.getUserById(1L));
}
```

---

## verify() — Assert the Mock Was Called

After the action, verify that the mock was called the expected number of times with the expected arguments.

```java
@Test
void should_saveUser_when_registering() {
    // Act
    userService.register(new CreateUserRequest("Bob", "bob@example.com"));

    // Assert — verify the repository's save() was called exactly once
    verify(userRepository, times(1)).save(any(User.class));

    // verify() with no times() defaults to times(1)
    verify(userRepository).save(any(User.class));
}

@Test
void should_notSaveUser_when_emailAlreadyExists() {
    when(userRepository.existsByEmail("bob@example.com")).thenReturn(true);

    assertThrows(
        EmailAlreadyExistsException.class,
        () -> userService.register(new CreateUserRequest("Bob", "bob@example.com"))
    );

    verify(userRepository, never()).save(any(User.class));
}
```

| Verification mode | Meaning |
|-------------------|---------|
| `times(n)` | Called exactly n times |
| `never()` | Never called |
| `atLeastOnce()` | Called one or more times |
| `atMost(n)` | Called at most n times |

---

## ArgumentCaptor — Inspect What Was Passed

When you want to assert the exact object passed to a mock method:

```java
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;

@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    UserRepository userRepository;

    @InjectMocks
    UserService userService;

    @Captor
    ArgumentCaptor<User> userCaptor;

    @Test
    void should_saveUserWithCorrectEmail_when_registering() {
        userService.register(new CreateUserRequest("Alice", "alice@example.com"));

        verify(userRepository).save(userCaptor.capture());
        User savedUser = userCaptor.getValue();

        assertEquals("alice@example.com", savedUser.getEmail());
        assertNotNull(savedUser.getCreatedAt());
    }
}
```

---

## @Spy — Partial Mocking

A spy wraps a real object. Methods not stubbed call through to the real implementation.

```java
import org.mockito.Spy;

@ExtendWith(MockitoExtension.class)
class EmailServiceTest {

    @Spy
    EmailService emailService = new EmailService();  // real object wrapped

    @Test
    void should_callSendOnce() {
        // Only stub the method that makes an external call
        doNothing().when(emailService).sendEmail(anyString(), anyString());

        emailService.notifyUser("alice@example.com", "Welcome!");

        verify(emailService, times(1)).sendEmail("alice@example.com", anyString());
    }
}
```

Use `@Spy` sparingly — if you need to spy on many methods, the class probably has too many responsibilities.

---

## Complete Example — Mocking a Repository in a Service Test

**UserRepository.java** (interface):

```java
package com.amalitech.user;

import java.util.Optional;

public interface UserRepository {
    Optional<User> findById(Long id);
    boolean existsByEmail(String email);
    User save(User user);
}
```

**UserService.java**:

```java
package com.amalitech.user;

public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException("User not found: " + id));
    }

    public User register(String name, String email) {
        if (userRepository.existsByEmail(email)) {
            throw new IllegalArgumentException("Email already registered: " + email);
        }
        return userRepository.save(new User(null, name, email));
    }
}
```

**UserServiceTest.java**:

```java
package com.amalitech.user;

import org.junit.jupiter.api.*;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.*;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Optional;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
@DisplayName("UserService")
class UserServiceTest {

    @Mock
    UserRepository userRepository;

    @InjectMocks
    UserService userService;

    @Captor
    ArgumentCaptor<User> userCaptor;

    @Nested
    @DisplayName("getUserById()")
    class GetUserById {

        @Test
        @DisplayName("returns user when found")
        void should_returnUser_when_exists() {
            User alice = new User(1L, "Alice", "alice@example.com");
            when(userRepository.findById(1L)).thenReturn(Optional.of(alice));

            User result = userService.getUserById(1L);

            assertEquals("Alice", result.getName());
        }

        @Test
        @DisplayName("throws UserNotFoundException when user does not exist")
        void should_throwException_when_notFound() {
            when(userRepository.findById(99L)).thenReturn(Optional.empty());

            assertThrows(UserNotFoundException.class, () -> userService.getUserById(99L));
        }
    }

    @Nested
    @DisplayName("register()")
    class Register {

        @Test
        @DisplayName("saves user and returns saved instance")
        void should_saveUser_when_emailIsNew() {
            when(userRepository.existsByEmail("bob@example.com")).thenReturn(false);
            when(userRepository.save(any(User.class)))
                .thenAnswer(inv -> inv.getArgument(0));

            userService.register("Bob", "bob@example.com");

            verify(userRepository).save(userCaptor.capture());
            assertEquals("bob@example.com", userCaptor.getValue().getEmail());
        }

        @Test
        @DisplayName("throws IllegalArgumentException when email is already registered")
        void should_throwException_when_emailExists() {
            when(userRepository.existsByEmail("bob@example.com")).thenReturn(true);

            assertThrows(
                IllegalArgumentException.class,
                () -> userService.register("Bob", "bob@example.com")
            );

            verify(userRepository, never()).save(any(User.class));
        }
    }
}
```
