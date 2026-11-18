# Phase 10 Exercises — Testing

---

## Exercise 1 — Write Tests for StringUtils

Write a `StringUtils` class with these methods, then write full JUnit 5 tests for each one:

- `reverse(String s)` — returns the string reversed
- `countVowels(String s)` — returns the count of vowels (a, e, i, o, u)
- `isPalindrome(String s)` — returns true if s reads the same forwards and backwards (case-insensitive)

Requirements: cover the happy path, the edge cases (null input, empty string, single character), and error paths.

---

## Exercise 2 — Test a Calculator Fully

Write a `Calculator` class with `add`, `subtract`, `multiply`, `divide`, and `power` methods. Write a complete test class using `@Nested` to group tests by method. Every code path must be covered (including division by zero). Use `@DisplayName` for all test methods.

---

## Exercise 3 — TDD: Build a Stack

Using TDD (write tests first, then implement), build a `Stack<T>` class with:
- `push(T item)` — adds item to top
- `pop()` — removes and returns top item; throws `EmptyStackException` if empty
- `peek()` — returns top item without removing; throws `EmptyStackException` if empty
- `isEmpty()` — returns true if stack has no items
- `size()` — returns number of items

Commit (or document in comments) each Red-Green-Refactor cycle.

---

## Exercise 4 — TDD: Build a Password Validator

Using TDD, build a `PasswordValidator` class. The rules are:
- Minimum 8 characters
- Must contain at least one uppercase letter
- Must contain at least one digit
- Must contain at least one special character (!@#$%^&*)

The `validate(String password)` method should return a `ValidationResult` object (you define this record/class) with a `boolean valid` field and a `List<String> errors` field. Write one test per rule, then implement.

---

## Exercise 5 — Test a BankAccount

Write a `BankAccount` class with:
- Constructor taking an `accountNumber` and initial `balance`
- `deposit(double amount)` — throws `IllegalArgumentException` for non-positive amounts
- `withdraw(double amount)` — throws `InsufficientFundsException` for overdrafts
- `getBalance()` — returns current balance

Write a complete test class. Use `assertAll` where multiple properties need checking in one test.

---

## Exercise 6 — Mockito: Service with Repository

You have an `InventoryService` that depends on an `InventoryRepository` interface:

```java
interface InventoryRepository {
    Optional<Product> findBySku(String sku);
    Product save(Product product);
    void delete(String sku);
}
```

Write a `restockProduct(String sku, int quantity)` method in `InventoryService`. It should:
- Throw `ProductNotFoundException` if the SKU does not exist
- Throw `IllegalArgumentException` if quantity is not positive
- Update the product's stock and save it

Write tests using `@Mock` and `@InjectMocks`. Verify correct calls to the repository with `verify()`.

---

## Exercise 7 — ArgumentCaptor

Using the `InventoryService` from Exercise 6, add an `addProduct(String sku, String name, double price)` method. Use `ArgumentCaptor<Product>` in a test to capture the `Product` object passed to `repository.save()` and assert that all fields were set correctly.

---

## Exercise 8 — Test Exception Messages

Write tests for a `DateParser.parse(String dateString)` method that:
- Returns a `LocalDate` for valid ISO dates (`"2024-01-15"`)
- Throws `IllegalArgumentException("Invalid date format: <input>")` for invalid input
- Throws `NullPointerException("Date string cannot be null")` for null input

Assert both the exception type **and** the message in each test.

---

## Exercise 9 — @BeforeEach and State Reset

Write a `ShoppingCart` class (see TDD walkthrough). Then write tests demonstrating that each test gets a fresh `ShoppingCart` via `@BeforeEach`. Show what would go wrong if tests shared state (write one test that intentionally breaks when state is shared, then fix it).

---

## Exercise 10 — Parameterized Tests

Use `@ParameterizedTest` and `@CsvSource` (look up the JUnit 5 docs) to write a single test method that runs the palindrome check from Exercise 1 against multiple inputs:

| Input | Expected |
|-------|---------|
| `"racecar"` | `true` |
| `"hello"` | `false` |
| `"A man a plan a canal Panama"` (remove spaces, ignore case) | `true` |
| `""` | `true` |
| `"a"` | `true` |

---

## Exercise 11 — TDD: Roman Numeral Converter

Using TDD, implement `RomanNumeralConverter.toRoman(int number)`:
- Input range: 1–3999
- Throws `IllegalArgumentException` for out-of-range values

Write your tests first, covering: 1 → `"I"`, 4 → `"IV"`, 9 → `"IX"`, 14 → `"XIV"`, 1994 → `"MCMXCIV"`, 3999 → `"MMMCMXCIX"`.

---

## Exercise 12 — Integration-Style Test (No Mocking)

Write a `CsvStudentLoader` class that reads a CSV file and returns a list of `Student` objects. Test it by:
1. Creating a temporary file in `@BeforeEach` using `Files.createTempFile()`.
2. Writing test CSV data to it.
3. Calling `CsvStudentLoader.load(path)` and asserting the result.
4. Deleting the file in `@AfterEach`.

No mocking — this is a small integration test of real file I/O.

---

## Exercise 13 — Test-Driving an Email Validator

Using TDD, write an `EmailValidator.isValid(String email)` method. Write tests first for:
- `"user@example.com"` → valid
- `"user.name+tag@domain.co.uk"` → valid
- `"missingat.com"` → invalid
- `"@nodomain"` → invalid
- `"spaces in@email.com"` → invalid
- `null` → throws `NullPointerException`

---

## Exercise 14 — Mutation Testing Review

Take the `Calculator` tests from Exercise 2. Run PIT (or manually reason through it):
- What happens if you change `+` to `-` in the `add` method? Does a test catch it?
- What happens if you change `>` to `>=` in the divide-by-zero check? Does a test catch it?
- For any surviving mutation you find, write a new test that kills it.

---

## Exercise 15 — Full Test Suite for an Order System

Build and fully test a small order system following TDD:

Classes to build:
- `Product(String name, double price)` — record
- `OrderItem(Product product, int quantity)` — has a `subtotal()` method
- `Order` — holds a list of `OrderItem`; has `addItem()`, `getTotal()`, `applyDiscount(double percent)`
- `OrderService` — depends on `OrderRepository`; has `placeOrder()`, `cancelOrder()`, `getOrderById()`

Requirements:
- Full JUnit 5 tests for `Order` and `OrderItem` (no mocks needed — pure logic)
- Mockito tests for `OrderService` (mock the repository)
- Use `@Nested` to organise by method
- Use `@DisplayName` on all tests
- Cover all error paths
