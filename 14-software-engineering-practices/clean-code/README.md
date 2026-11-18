# Clean Code

Clean code can be read and enhanced by a developer other than its original author. At AmaliTech, writing clean code is not optional — it is a core professional skill. This document covers the rules from the Amalitech Coding Standards that apply to your day-to-day writing.

---

## 1. Meaningful Names — Amalitech §3

Names are the primary communication tool in code. A good name eliminates the need for a comment.

### Choose descriptive, unambiguous names (§3.1)

```java
// BAD — what does d mean? What is 1800000?
String d = "Monday";
long t = 1_800_000;

// GOOD — the name explains itself
String firstDayOfWeek = "Monday";
Duration sessionTimeout = Duration.ofMinutes(30);
```

### Make meaningful distinctions (§3.2)

```java
// BAD — userList suggests it's a List; userList2 is worse
List<User> userList = new ArrayList<>();
Set<User> userList2 = new HashSet<>();

// GOOD — plural suffices; names are distinct
List<User> users = new ArrayList<>();
Set<User> activeUsers = new HashSet<>();
```

### Use pronounceable names (§3.3)

```java
// BAD — you cannot say this in a code review
LocalDate genymdhms = LocalDate.now();

// GOOD
LocalDate generationDate = LocalDate.now();
```

### Replace magic numbers with named constants (§3.4)

```java
// BAD — what is 1800000?
boolean hasExpired(long idleMillis) {
    return idleMillis > 1_800_000;
}

// GOOD — searchable, readable, single place to change
private static final Duration SESSION_TIMEOUT = Duration.ofMinutes(30);

boolean hasExpired(Duration idleTime) {
    return idleTime.compareTo(SESSION_TIMEOUT) > 0;
}
```

### Avoid encodings and noise words (§3.5)

```java
// BAD — Hungarian notation, noise word "Info", m_ prefix
class UserInfo {
    private String m_strName;
    private int m_iAge;
}

// GOOD
class User {
    private String name;
    private int age;
}
```

Noise words to avoid: `Info`, `Data`, `Object`, `Manager`, `The` prefix.

### Identifier naming rules (§3.5 table)

| Identifier | Convention | Example |
|-----------|-----------|---------|
| Package | `all.lower.case` | `com.amalitech.booking` |
| Class / Interface | `UpperCamelCase` noun | `class Invoice`, `interface PaymentGateway` |
| Method | `lowerCamelCase` verb | `calculateTotal()`, `isAvailable()` |
| Variable / Parameter | `lowerCamelCase` | `int seatCount` |
| Constant (`static final`) | `UPPER_SNAKE_CASE` | `MAX_PARTICIPANTS` |
| Type parameter | Single capital letter | `T`, `E`, `K`, `V` |

---

## 2. Functions Do One Thing — Amalitech §4.2

A function should do one thing, and its name should say what that thing is.

```java
// BAD — this method does three things: looks up subscriber, checks active, emails
class NewsletterService {
    void emailSubscribers(List<Subscriber> subscribers) {
        for (Subscriber subscriber : subscribers) {
            SubscriberRecord subscriberRecord = database.lookup(subscriber);
            if (subscriberRecord.isActive()) {
                mailer.send(subscriber);
            }
        }
    }
}

// GOOD — emailActiveSubscribers does one thing; isActive is a separate responsibility
class NewsletterService {
    void emailActiveSubscribers(List<Subscriber> subscribers) {
        subscribers.stream()
                .filter(this::isActive)
                .forEach(mailer::send);
    }

    private boolean isActive(Subscriber subscriber) {
        return database.lookup(subscriber).isActive();
    }
}
```

---

## 3. Functions Should Be Small — Amalitech §4.1

Functions should rarely exceed 20 lines. If a function is long, it is doing too many things. Extract the parts into private methods.

```java
// BAD — 30+ line method doing validation, admin path, user path
class PasswordService {
    void changePassword(String email, String oldPassword, String newPassword) {
        User user = users.findByEmail(email);
        if (user == null || !hasher.matches(oldPassword, user.passwordHash())) {
            throw new InvalidCredentialsException(email);
        }
        if (hasher.matches(newPassword, user.passwordHash())) {
            throw new PasswordReusedException(email);
        }
        if (user.isAdmin()) {
            user.setPasswordHash(hasher.hash(newPassword));
            users.save(user);
            sessions.endAllFor(user);
            String token = tokens.createRevertToken(user);
            for (String address : user.emailAddresses()) {
                mailer.send(address, "Your password was changed", token);
            }
        } else {
            if (!user.isEnabled()) {
                throw new AccountDisabledException(email);
            }
            user.setPasswordHash(hasher.hash(newPassword));
            users.save(user);
            sessions.endAllFor(user);
        }
    }
}

// GOOD — the top-level method is a readable outline; details are in small private methods
class PasswordService {
    void changePassword(String email, String oldPassword, String newPassword) {
        User user = users.findByEmail(email);
        checkPasswordCanChange(user, email, oldPassword, newPassword);
        if (user.isAdmin()) {
            changeAdminPassword(user, newPassword);
        } else {
            changeUserPassword(user, newPassword);
        }
    }

    private void checkPasswordCanChange(User user, String email,
                                        String oldPassword, String newPassword) {
        if (user == null || !hasher.matches(oldPassword, user.passwordHash())) {
            throw new InvalidCredentialsException(email);
        }
        if (hasher.matches(newPassword, user.passwordHash())) {
            throw new PasswordReusedException(email);
        }
    }

    private void changeUserPassword(User user, String newPassword) {
        if (!user.isEnabled()) {
            throw new AccountDisabledException(user.email());
        }
        storeNewPassword(user, newPassword);
    }

    private void changeAdminPassword(User user, String newPassword) {
        storeNewPassword(user, newPassword);
        sendPasswordChangedEmails(user);
    }

    private void storeNewPassword(User user, String newPassword) {
        user.setPasswordHash(hasher.hash(newPassword));
        users.save(user);
        sessions.endAllFor(user);
    }

    private void sendPasswordChangedEmails(User user) {
        String token = tokens.createRevertToken(user);
        for (String address : user.emailAddresses()) {
            mailer.send(address, "Your password was changed", token);
        }
    }
}
```

---

## 4. Avoid Flag Arguments — Amalitech §4.5

A boolean parameter that selects between two behaviors means the function does two things. Split it.

```java
// BAD — flag argument: does two different things
class ReportWriter {
    Path createFile(String name, boolean temporary) throws IOException {
        if (temporary) {
            return Files.createFile(Path.of("temp", name));
        }
        return Files.createFile(Path.of(name));
    }
}

// GOOD — two separate methods with clear names
class ReportWriter {
    Path createFile(String name) throws IOException {
        return Files.createFile(Path.of(name));
    }

    Path createTemporaryFile(String name) throws IOException {
        return createFile(Path.of("temp", name).toString());
    }
}
```

---

## 5. No Side Effects — Amalitech §4.4

A function with a side effect does something in addition to what its name says. This makes code surprising and hard to test.

```java
// BAD — checkPassword also initializes a session (a hidden side effect)
boolean checkPassword(String userName, String password) {
    User user = userGateway.findByName(userName);
    if (user != null) {
        String phrase = cryptographer.decrypt(user.encodedPhrase(), password);
        if ("Valid Password".equals(phrase)) {
            Session.initialize(); // SIDE EFFECT — name gives no hint of this
            return true;
        }
    }
    return false;
}

// GOOD — checking and session init are separate; caller decides
boolean checkPassword(String userName, String password) {
    User user = userGateway.findByName(userName);
    if (user == null) return false;
    String phrase = cryptographer.decrypt(user.encodedPhrase(), password);
    return "Valid Password".equals(phrase);
}

void loginUser(String userName, String password) {
    if (checkPassword(userName, password)) {
        Session.initialize();
    }
}
```

---

## 6. DRY, KISS, YAGNI — Amalitech §2.2

**DRY — Don't Repeat Yourself**  
Every piece of knowledge must have a single, authoritative representation. If you find yourself copying code, extract it into a shared method or class.

```java
// BAD — validation logic duplicated in two places
class RegistrationController {
    void register(String email) {
        if (email == null || !email.contains("@")) {
            throw new InvalidEmailException(email);
        }
        // ...
    }
}

class ProfileController {
    void updateEmail(String email) {
        if (email == null || !email.contains("@")) {  // duplicated!
            throw new InvalidEmailException(email);
        }
        // ...
    }
}

// GOOD — single place
class EmailValidator {
    static void validate(String email) {
        if (email == null || !email.contains("@")) {
            throw new InvalidEmailException(email);
        }
    }
}
```

**KISS — Keep It Simple**  
When you can solve a problem with a simple approach, use the simple approach. Complexity should be introduced only when necessary.

**YAGNI — You Aren't Gonna Need It**  
Do not add functionality until it is needed. Speculative generality is a code smell. If you are not sure you need a feature, do not build it.

---

## 7. Code Smells

Code smells are surface indicators of deeper problems. They do not mean the code is broken — they mean it is harder to maintain than it should be.

### Long Method
A method longer than 20–30 lines almost certainly does more than one thing.  
**Fix:** Extract method — pull logical chunks into named private methods.

### Large Class
A class with many fields and methods likely has more than one responsibility.  
**Fix:** Identify the separate responsibilities and extract each into its own class.

### Feature Envy
A method that uses data from another class more than its own class.  
**Fix:** Move the method to the class whose data it uses.

```java
// BAD — DiscountCalculator is envying Order's data
class DiscountCalculator {
    double calculate(Order order) {
        return order.getTotal() * order.getDiscountRate() * order.getCustomerMultiplier();
    }
}

// GOOD — the logic belongs to Order
class Order {
    double calculateDiscount() {
        return total * discountRate * customerMultiplier;
    }
}
```

### Data Clumps
Groups of variables that always travel together (e.g., `firstName`, `lastName`, `email`) indicate a missing class.  
**Fix:** Introduce a class or record to hold them.

```java
// BAD — these three always go together
void sendEmail(String host, int port, String protocol) { ... }

// GOOD — group them into a record
record MailServerConfig(String host, int port, String protocol) {}
void sendEmail(MailServerConfig config) { ... }
```

### Primitive Obsession
Using primitive types (`String`, `int`, `double`) where a domain object would be more expressive.

```java
// BAD — what is this double? Dollars? Cents? Euros?
double price = 99.99;

// GOOD
record Money(BigDecimal amount, Currency currency) {}
Money price = new Money(new BigDecimal("99.99"), Currency.getInstance("USD"));
```

---

## 8. Refactoring Techniques

Refactoring improves code structure without changing its behavior.

### Extract Method
Pull a chunk of code from a large method into a named private method.

### Rename
Rename variables, methods, or classes to better reflect their purpose. Modern IDEs do this safely.

### Introduce Parameter Object
When a method takes several related parameters, group them into a class or record (addresses Data Clumps).

```java
// Before
void register(String firstName, String lastName, String email, String phone) { ... }

// After
record RegistrationRequest(String firstName, String lastName, String email, String phone) {}
void register(RegistrationRequest request) { ... }
```

---

## 9. Formatting — Amalitech §7

Formatting communicates structure. Consistently formatted code is easier to read than technically correct but inconsistent code.

**Vertical openness between concepts (§7.2):** Separate different concepts with blank lines.

**Vertically dense related lines (§7.3):** Lines that belong together should sit together.

**Instance variables at the top (§7.5):** Declare fields at the top of the class.

**Caller above callee (§7.6):** Keep a method above the methods it calls, so the file reads top-to-bottom.

**Column limit (§11.1):** 100 characters per line.

**Braces always (§11.1):** Use `{}` for every `if`, `for`, `while`, even one-liners.

```java
// BAD — no braces, cramped, no blank lines between concepts
class Invoice {
private final String id;
private final double amount;
Invoice(String id, double amount) { this.id = id; this.amount = amount; }
double taxAmount() { return amount * 0.2; }
double total() { return amount + taxAmount(); }
}

// GOOD
class Invoice {

    private final String id;
    private final double amount;

    Invoice(String id, double amount) {
        this.id = id;
        this.amount = amount;
    }

    double total() {
        return amount + taxAmount();
    }

    private double taxAmount() {
        return amount * 0.2;
    }
}
```

---

## 10. Comments — Amalitech §10

**Comment the why, not the what (§10.1).** The code says what it does. A comment should explain a business rule, a non-obvious decision, or a workaround.

```java
// BAD — the comment restates the code
// loop through every character
for (int i = 0; i < data.length(); i++) {
    char c = data.charAt(i);     // get the character
    hash = (hash << 5) - hash + c; // make the hash
}

// GOOD — the comment explains the why
for (int i = 0; i < data.length(); i++) {
    // same as hash * 31 + c, matching the partner system's checksum
    hash = (hash << 5) - hash + data.charAt(i);
}
```

**No journal comments or commented-out code (§10.2).** Use `git log` for history. Delete dead code.

**No positional markers (§10.3).**

```java
// BAD
////////////////////////////////////////////////////////////
// Model
////////////////////////////////////////////////////////////
private final Map<String, String> model = new HashMap<>();
```

---

## 11. Objects and Data Structures — Amalitech §8

- **Hide internal structure.** Keep fields `private`. Expose behavior, not data.
- **Use records for plain data.** A class that only holds values and has no behavior should be a `record`.
- **Avoid hybrids.** A class is either an object (behavior) or a data structure (data). A class with public fields and also behavior methods confuses the purpose.

```java
// BAD — public fields expose internal structure; logic in a separate static class
class Membership {
    public LocalDate startDate;
    public int months;
    public boolean frozen;
}

class MembershipChecker {
    static boolean isActive(Membership membership, LocalDate today) {
        return !membership.frozen
                && !today.isAfter(membership.startDate.plusMonths(membership.months));
    }
}

// GOOD — the object knows its own behavior; fields are private
final class Membership {
    private final LocalDate startDate;
    private final Period length;
    private boolean frozen;

    Membership(LocalDate startDate, Period length) {
        this.startDate = startDate;
        this.length = length;
    }

    boolean isActiveOn(LocalDate date) {
        return !frozen && !date.isAfter(startDate.plus(length));
    }

    void freeze() {
        frozen = true;
    }
}
```

---

## Summary

| Rule | Section | Key point |
|------|---------|-----------|
| Descriptive names | §3 | Names replace comments |
| Small functions | §4.1 | Rarely more than 20 lines |
| One responsibility | §4.2 | Function name says the one thing it does |
| No flag args | §4.5 | Split into two methods |
| No side effects | §4.4 | Function does what its name says, nothing else |
| DRY / KISS / YAGNI | §2.2 | Don't repeat; keep it simple; build what's needed |
| Formatting | §7, §11.1 | Blank lines between concepts, braces always, 100-char limit |
| Comments | §10 | Comment the why; no dead code; no markers |
| Objects and data | §8 | Private fields; expose behavior |
