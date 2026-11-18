# Phase 04 — OOP Exercises

Practice exercises covering classes, objects, constructors, encapsulation, inheritance, polymorphism, abstraction, interfaces, and composition.

---

## Exercise 1 — Student Class
**Difficulty:** Beginner
**Concepts:** class, fields, methods, ArrayList, toString

### Problem
Create a `Student` class with:
- Private fields: `name` (String), `id` (String), `grades` (List of doubles)
- Constructor: `Student(String name, String id)`
- Methods: `addGrade(double grade)`, `getAverage()` (returns double), `getLetterGrade()` (A/B/C/D/F based on average), `toString()`

### Expected Output / Sample
```
Student: Alice (ID: S001)
Grades: [88.0, 92.0, 76.0]
Average: 85.3
Letter Grade: B
```

### Hints
- Use `ArrayList<Double>` for grades.
- `getAverage()` sums all grades and divides by count. Handle the empty-list case (return 0.0).

### Bonus
Add a `getHighest()` and `getLowest()` method.

---

## Exercise 2 — BankAccount with Validation
**Difficulty:** Beginner
**Concepts:** encapsulation, private fields, exceptions

### Problem
Create a `BankAccount` class with:
- Private fields: `owner` (String), `balance` (double)
- Constructor validates that balance is not negative
- `deposit(double amount)` — throws `IllegalArgumentException` if amount ≤ 0
- `withdraw(double amount)` — throws `IllegalArgumentException` if amount ≤ 0, throws `IllegalStateException` if insufficient funds
- `getBalance()` and `toString()`

### Expected Output / Sample
```
Alice: $1000.00
After deposit(500): Alice: $1500.00
After withdraw(200): Alice: $1300.00
withdraw(5000): IllegalStateException — Insufficient funds
deposit(-10): IllegalArgumentException — Must be positive
```

### Hints
- Use `throw new IllegalArgumentException("message")` for validation.

### Bonus
Add a `transfer(BankAccount target, double amount)` method that withdraws from this account and deposits into the target.

---

## Exercise 3 — Person and Student Inheritance
**Difficulty:** Intermediate
**Concepts:** inheritance, extends, super, @Override

### Problem
Create a `Person` class with private fields `name` and `age`. Add a constructor, getters, and `toString()`.

Create a `Student` class that extends `Person`. Add fields `studentId` and `gpa`. Override `toString()` using `super.toString()`. Add a method `isHonorRoll()` that returns true if gpa >= 3.5.

### Expected Output / Sample
```
Person: Bob, age 20
Student: Bob, age 20 | ID: S002 | GPA: 3.8 | Honor Roll: Yes
```

### Hints
- `Student` constructor calls `super(name, age)` for the Person fields.
- `toString()` calls `super.toString()` for the Person part.

### Bonus
Create a `GraduateStudent` that extends `Student` and adds a `thesis` field.

---

## Exercise 4 — Shape Hierarchy with Abstraction
**Difficulty:** Intermediate
**Concepts:** abstract class, abstract methods, polymorphism

### Problem
Create an abstract `Shape` class with:
- Field: `colour` (String)
- Abstract methods: `area()` and `perimeter()`
- Concrete method: `describe()` that prints the colour, area, and perimeter

Implement three concrete shapes:
- `Circle(String colour, double radius)`
- `Rectangle(String colour, double width, double height)`
- `Triangle(String colour, double a, double b, double c)` — use Heron's formula for area

Create one of each in a `List<Shape>` and call `describe()` on each.

### Expected Output / Sample
```
Red circle: area = 78.54, perimeter = 31.42
Blue rectangle: area = 24.00, perimeter = 20.00
Green triangle: area = 6.00, perimeter = 12.00
```

### Hints
- Heron's formula: `s = (a+b+c)/2`, `area = Math.sqrt(s*(s-a)*(s-b)*(s-c))`

### Bonus
Add a `compareTo(Shape other)` method that returns whether this shape is larger or smaller by area.

---

## Exercise 5 — Payable Interface
**Difficulty:** Intermediate
**Concepts:** interface, implements, polymorphism

### Problem
Create a `Payable` interface with method `double calculatePay()`. Implement it in:
- `FullTimeEmployee(String name, double monthlySalary)` — `calculatePay()` returns monthly salary
- `PartTimeEmployee(String name, double hourlyRate, int hoursWorked)` — `calculatePay()` returns `hourlyRate * hoursWorked`
- `Contractor(String name, double projectFee, double taxRate)` — `calculatePay()` returns `projectFee * (1 - taxRate)`

Create a `List<Payable>` with one of each. Print each name and pay, then print the total payroll.

### Expected Output / Sample
```
Alice (FullTime): $5000.00
Bob (PartTime): $960.00
Carol (Contractor): $8500.00
Total payroll: $14460.00
```

### Hints
- Use an interface reference in the list: `List<Payable>`.
- Use `instanceof` or a `getName()` method from a parent class to print the name.

### Bonus
Sort the list by pay amount using `Comparator.comparingDouble(Payable::calculatePay)`.

---

## Exercise 6 — Polymorphism with Animal Sounds
**Difficulty:** Beginner
**Concepts:** polymorphism, @Override, arrays of parent type

### Problem
Create an `Animal` class with a method `makeSound()` that returns `"..."`. Create subclasses: `Dog` ("Woof!"), `Cat` ("Meow!"), `Cow` ("Moo!"), `Duck` ("Quack!"). Create an array of 8 animals (mix of types) and loop through it, printing each animal's sound. Each animal should also have a `name` field.

### Expected Output / Sample
```
Rex (Dog): Woof!
Whiskers (Cat): Meow!
Bessie (Cow): Moo!
Donald (Duck): Quack!
...
```

### Bonus
Add a method `animalType()` to each subclass that returns the type name. Use it in the output.

---

## Exercise 7 — Interface with Default Method
**Difficulty:** Intermediate
**Concepts:** interface, default method, multiple interfaces

### Problem
Create a `Describable` interface with:
- Abstract method `String getDescription()`
- Default method `void printDescription()` that prints the result of `getDescription()`

Implement it in `Book(title, author, year)` and `Movie(title, director, rating)`. Each provides its own `getDescription()`. Call `printDescription()` on both without overriding it.

### Expected Output / Sample
```
"The Pragmatic Programmer" by Andrew Hunt (1999)
"Inception" directed by Christopher Nolan — Rating: 8.8/10
```

### Bonus
Add a second interface `Ratable` with `double getRating()`. Implement it in `Movie` only. Write a method `printRating(Ratable r)` that works for any `Ratable`.

---

## Exercise 8 — Composition: Order with Products
**Difficulty:** Intermediate
**Concepts:** composition, ArrayList, delegation

### Problem
Create a `Product` class with `name` (String) and `price` (double).

Create an `Order` class that uses composition:
- Has a `List<Product>` internally (private)
- `addProduct(Product p)` — adds a product
- `removeProduct(String name)` — removes by name
- `getTotal()` — sum of all product prices
- `getItemCount()` — number of products
- `printReceipt()` — prints all items and the total

### Expected Output / Sample
```
=== Order Receipt ===
Laptop      $999.99
Mouse        $29.99
Keyboard     $79.99
-------------------
Total      $1109.97
3 item(s)
```

### Hints
- Use `removeIf()` or loop with an iterator to remove by name.

### Bonus
Add a `discount(double percent)` method that applies a percentage discount to the total.

---

## Exercise 9 — Encapsulation: Temperature Converter
**Difficulty:** Beginner
**Concepts:** encapsulation, getters, computed properties

### Problem
Create a `Temperature` class that stores a value internally as Celsius (private double field). Provide:
- `Temperature(double celsius)` constructor with validation (above absolute zero: -273.15°C)
- `getCelsius()`, `getFahrenheit()`, `getKelvin()` — all computed from the internal field
- `setCelsius(double)` setter with the same validation
- `toString()` that prints all three values

### Expected Output / Sample
```
100.0°C = 212.0°F = 373.15K
-273.15°C = -459.67°F = 0.0K
```

### Bonus
Add a static factory method `Temperature.fromFahrenheit(double f)` that creates a Temperature from a Fahrenheit value.

---

## Exercise 10 — Interface: Comparable Shapes
**Difficulty:** Intermediate
**Concepts:** Comparable interface (from Java), generics, sorting

### Problem
Make `Circle` and `Rectangle` from Exercise 4 implement `Comparable<Shape>` (compare by area). Create a `List<Shape>` with five shapes of mixed types and sort them using `Collections.sort()`. Print them in order from smallest to largest area.

### Expected Output / Sample
```
Shapes sorted by area:
1. Red circle: area = 3.14
2. Blue rectangle: area = 6.00
3. Green circle: area = 12.57
...
```

### Hints
- `compareTo()` returns negative if `this` is smaller, positive if larger, 0 if equal.
- `Double.compare(this.area(), other.area())` does this correctly.

### Bonus
Also sort in reverse order using `Collections.sort(list, Collections.reverseOrder())`.

---

## Exercise 11 — Abstract Class: Vehicle Hierarchy
**Difficulty:** Intermediate
**Concepts:** abstract class, concrete and abstract methods

### Problem
Create an abstract `Vehicle` class with fields `brand` and `year`, abstract methods `fuelType()` and `maxSpeed()`, and a concrete `describe()` method. Implement:
- `ElectricCar` — fuelType = "Electric", maxSpeed = 250
- `PetrolCar` — fuelType = "Petrol", maxSpeed = 200
- `Bicycle` — fuelType = "None (human-powered)", maxSpeed = 40

Add all three to a `List<Vehicle>` and print their descriptions.

### Bonus
Add an `isEcoFriendly()` method in the abstract class that returns `true` if `fuelType()` does not contain "Petrol" or "Diesel". Override it in relevant subclasses.

---

## Exercise 12 — Strategy Pattern (Composition)
**Difficulty:** Advanced
**Concepts:** composition, interface, strategy pattern

### Problem
Create a `SortStrategy` interface with `void sort(int[] array)`. Implement `BubbleSort` and `InsertionSort`. Create a `Sorter` class that holds a `SortStrategy` and can swap it. Test sorting the same array with both strategies and print results.

### Hints
- `Sorter` holds a `SortStrategy` field.
- `Sorter.sort(int[] array)` delegates to `strategy.sort(array)`.

### Bonus
Add a `TimedSort` that wraps any other `SortStrategy` and prints the time taken.

---

## Exercise 13 — instanceof and Downcasting
**Difficulty:** Intermediate
**Concepts:** polymorphism, instanceof, downcasting

### Problem
Create a `List<Animal>` containing a mix of `Dog`s and `Cat`s. Loop through it. For each `Dog`, call `fetch(String item)`. For each `Cat`, call `purr()`. Use Java 16 pattern matching (`instanceof Dog dog`) if available, otherwise use traditional cast.

### Expected Output / Sample
```
Buddy fetches the ball!
Whiskers is purring...
Rex fetches the stick!
Luna is purring...
```

### Bonus
Add a third type `Bird` with `sing()`. Show that adding a new type only requires adding a new branch — no existing code changes.

---

## Exercise 14 — Full OOP Design: Library System
**Difficulty:** Advanced
**Concepts:** classes, inheritance, encapsulation, interfaces, composition

### Problem
Design a mini library system with:
- `LibraryItem` abstract class: `title`, `id`, abstract `describe()`, concrete `toString()`
- `Book extends LibraryItem`: adds `author` and `pages`
- `DVD extends LibraryItem`: adds `director` and `durationMinutes`
- `Borrowable` interface: `checkout(String memberName)`, `returnItem()`, `isAvailable()`
- Both `Book` and `DVD` implement `Borrowable`
- `Library` class (composition): holds a `List<LibraryItem>`, methods `addItem()`, `findById(String id)`, `listAvailable()`

Test: add 3 books and 2 DVDs. Checkout some. List available items.

### Hints
- `Borrowable` can store `checkedOutTo` as a String field (null = available).

### Bonus
Add a `Member` class that keeps a list of borrowed items and has a `borrowingHistory()` method.

---

## Exercise 15 — Immutable Class
**Difficulty:** Intermediate
**Concepts:** encapsulation, final fields, no setters, defensive copy

### Problem
Create an immutable `Money` class with private final fields `amount` (double) and `currency` (String). Provide only getters, no setters. Add methods `add(Money other)` (throws if currencies differ) and `multiply(double factor)` — both return **new** `Money` objects, they don't modify the original. Demonstrate immutability.

### Expected Output / Sample
```
Price: 10.00 USD
After add(5.00 USD): 15.00 USD  (original unchanged: 10.00 USD)
After multiply(1.1): 16.50 USD
```

### Bonus
Implement `equals()` and `hashCode()` so two `Money` objects with the same amount and currency are equal.

---

## Exercise 16 — Interface Segregation
**Difficulty:** Intermediate
**Concepts:** Interface Segregation Principle (ISP), multiple small interfaces

### Problem
You have a `SmartDevice` interface with 8 methods: `turnOn()`, `turnOff()`, `connectToWifi()`, `playMusic()`, `makeCall()`, `takePhoto()`, `setAlarm()`, `navigateTo()`. Split it into focused interfaces (`Switchable`, `NetworkCapable`, `MediaPlayer`, `Phone`, `Camera`, `Clock`, `Navigator`). Create three devices: `BasicSpeaker` (Switchable + NetworkCapable + MediaPlayer), `Smartphone` (all), `SmartWatch` (Switchable + Clock + Phone). Show each only implements what it needs.

### Bonus
Write a method `playAllMedia(List<MediaPlayer>)` that calls `playMusic()` on each. Show that `SmartWatch` cannot be passed in because it's not a `MediaPlayer`.

---

## Exercise 17 — toString and equals
**Difficulty:** Intermediate
**Concepts:** Object methods, @Override, equals contract

### Problem
Create a `Point` class with `x` and `y` (doubles). Override:
- `toString()` → `"Point(3.0, 4.0)"`
- `equals(Object obj)` → true if same type and same x and y
- `hashCode()` → consistent with equals

Demonstrate: two `Point` objects with the same coordinates are `equal`. Add them to a `HashSet` and show only one entry is stored (because they're considered equal).

### Hints
- Use `Objects.equals()` and `Objects.hash()` from `java.util.Objects`.

### Bonus
Implement `Comparable<Point>` using distance from origin: `Math.sqrt(x*x + y*y)`.

---

## Exercise 18 — Polymorphic Payment System
**Difficulty:** Advanced
**Concepts:** interface, polymorphism, composition

### Problem
Design a payment processing system:
- `Payment` interface: `boolean process()`, `String getDescription()`, `double getAmount()`
- `CreditCardPayment(double amount, String cardNumber)`: masks the card (`****1234`)
- `PayPalPayment(double amount, String email)`: uses email
- `BankTransfer(double amount, String accountNumber)`: uses account number
- `PaymentProcessor` class: takes a `List<Payment>` and processes all, prints successes/failures, prints total processed

Simulate one random failure (e.g. if amount > 10000, return false from `process()`).

### Bonus
Add a `PaymentLogger` that wraps any `Payment` using composition and logs before and after processing.

---

## Exercise 19 — Abstract Factory Pattern (Stretch)
**Difficulty:** Advanced
**Concepts:** abstract class, interface, factory, polymorphism

### Problem
Create a notification system:
- Abstract class `Notification`: `recipient`, `message`, abstract `send()`
- `EmailNotification extends Notification`
- `SMSNotification extends Notification`
- `PushNotification extends Notification`
- `NotificationFactory` class with static methods `createEmail(...)`, `createSMS(...)`, `createPush(...)`

Build a list of notifications using the factory and send all of them.

### Bonus
Add a `NotificationService` that takes a list and sends all, logging the type of each.

---

## Exercise 20 — Full Design: Zoo Management
**Difficulty:** Advanced
**Concepts:** all OOP pillars

### Problem
Design a simplified zoo management system:
- `Animal` abstract class: `name`, `species`, `age`; abstract `makeSound()`, `eat()`; concrete `describe()`
- `Mammal extends Animal`: adds `furColour`
- `Bird extends Animal`: adds `canFly` (boolean)
- `Reptile extends Animal`: adds `isVenomous` (boolean)
- Concrete animals: `Lion extends Mammal`, `Parrot extends Bird`, `Cobra extends Reptile`
- `Enclosure` class (composition): holds a `List<Animal>`, `enclosureName`; methods `addAnimal()`, `feedAllAnimals()`, `morningRollCall()`
- `Zoo` class (composition): holds a `List<Enclosure>`, `zooName`; `addEnclosure()`, `openDay()` (calls `morningRollCall()` on each enclosure)

### Expected Output / Sample
```
=== SUNRISE ZOO — Morning Roll Call ===
[Savanna Enclosure]
  Simba (Lion): ROAR!
  Nala (Lion): ROAR!
[Aviary]
  Polly (Parrot): Squawk!
[Reptile House]
  Kaa (Cobra): Hisssss...
```

### Bonus
Add a `Visitor` interface with `visit(Animal animal)`. Create a `ZooVisitor` class that counts how many animals of each type it visits.
