# Building REST APIs with Spring Boot

A REST API is how modern backend services communicate with clients — browsers, mobile apps, other services. This document covers HTTP fundamentals, REST principles, and how to build a complete REST API in Spring Boot.

---

## 1. HTTP Fundamentals

HTTP (Hypertext Transfer Protocol) is the protocol used to transfer data between clients and servers on the web. Every REST API communicates over HTTP.

### HTTP Methods

| Method | Purpose | Idempotent? | Has Body? |
|--------|---------|------------|---------|
| `GET` | Retrieve a resource | Yes | No |
| `POST` | Create a new resource | No | Yes |
| `PUT` | Replace a resource entirely | Yes | Yes |
| `PATCH` | Partially update a resource | No | Yes |
| `DELETE` | Remove a resource | Yes | No |

**Idempotent** means making the same request multiple times produces the same result. `DELETE /tasks/5` is idempotent — after the first call it succeeds; after the second, it still results in the task being absent.

### HTTP Status Codes

| Code | Meaning | When to Use |
|------|---------|------------|
| `200 OK` | Success | GET, PUT, PATCH succeeded |
| `201 Created` | Resource created | POST succeeded |
| `204 No Content` | Success, no body | DELETE succeeded |
| `400 Bad Request` | Invalid input | Validation failed |
| `401 Unauthorized` | Not authenticated | Missing/invalid token |
| `403 Forbidden` | Not authorized | Authenticated but no permission |
| `404 Not Found` | Resource not found | ID does not exist |
| `409 Conflict` | State conflict | Duplicate email, etc. |
| `422 Unprocessable Entity` | Semantic validation failed | Valid JSON, but business rule violated |
| `500 Internal Server Error` | Unexpected server error | Unhandled exception |

### HTTP Request Structure

```
POST /api/tasks HTTP/1.1
Host: localhost:8080
Content-Type: application/json
Authorization: Bearer <token>

{
    "title": "Write documentation",
    "description": "Cover all public APIs",
    "dueDate": "2025-12-31"
}
```

### HTTP Response Structure

```
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/tasks/42

{
    "id": 42,
    "title": "Write documentation",
    "status": "PENDING",
    "createdAt": "2025-01-15T10:30:00"
}
```

---

## 2. REST Principles

REST (Representational State Transfer) is an architectural style with six constraints. The ones you will encounter daily:

**Stateless:** Each request contains all information the server needs to process it. The server does not store session state between requests. Authentication tokens must be sent with every request.

**Resource-based:** Everything is a resource, identified by a URL. Resources are nouns, not verbs.

```
# BAD — verbs in URLs
GET  /getEmployee/5
POST /createEmployee
POST /deleteEmployee/5

# GOOD — nouns, HTTP method expresses the action
GET    /api/employees/5
POST   /api/employees
DELETE /api/employees/5
```

**Uniform interface:** Standard HTTP methods have standard meanings. Use them consistently.

**URL conventions:**

```
GET    /api/employees          → list all employees
POST   /api/employees          → create an employee
GET    /api/employees/{id}     → get one employee
PUT    /api/employees/{id}     → replace an employee
PATCH  /api/employees/{id}     → update fields of an employee
DELETE /api/employees/{id}     → delete an employee

GET    /api/departments/{id}/employees  → employees in a department
```

---

## 3. Building REST Controllers

### @RestController

```java
@RestController
@RequestMapping("/api/tasks")
public class TaskController {

    private final TaskService taskService;

    public TaskController(TaskService taskService) {
        this.taskService = taskService;
    }
}
```

`@RequestMapping("/api/tasks")` sets the base URL for all methods in this controller.

### @GetMapping — Read

```java
// GET /api/tasks  → list all tasks
@GetMapping
public List<TaskResponse> getAllTasks() {
    return taskService.findAll();
}

// GET /api/tasks/{id}  → get one task
@GetMapping("/{id}")
public ResponseEntity<TaskResponse> getTaskById(@PathVariable Long id) {
    return taskService.findById(id)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
}
```

### @PostMapping — Create

```java
// POST /api/tasks  → create a task
@PostMapping
public ResponseEntity<TaskResponse> createTask(@Valid @RequestBody CreateTaskRequest request) {
    TaskResponse created = taskService.create(request);
    URI location = URI.create("/api/tasks/" + created.id());
    return ResponseEntity.created(location).body(created);
}
```

### @PutMapping — Replace

```java
// PUT /api/tasks/{id}  → replace a task
@PutMapping("/{id}")
public ResponseEntity<TaskResponse> updateTask(
        @PathVariable Long id,
        @Valid @RequestBody UpdateTaskRequest request) {

    return taskService.update(id, request)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
}
```

### @DeleteMapping — Remove

```java
// DELETE /api/tasks/{id}  → delete a task
@DeleteMapping("/{id}")
public ResponseEntity<Void> deleteTask(@PathVariable Long id) {
    if (taskService.delete(id)) {
        return ResponseEntity.noContent().build(); // 204
    }
    return ResponseEntity.notFound().build(); // 404
}
```

---

## 4. @PathVariable, @RequestParam, @RequestBody

### @PathVariable

Extracts a value from the URL path.

```java
// GET /api/employees/42
@GetMapping("/{id}")
public EmployeeResponse getById(@PathVariable Long id) { ... }

// GET /api/departments/5/employees/12
@GetMapping("/departments/{deptId}/employees/{empId}")
public EmployeeResponse getEmployee(
        @PathVariable Long deptId,
        @PathVariable Long empId) { ... }
```

### @RequestParam

Extracts query parameters from the URL.

```java
// GET /api/employees?page=0&size=20&department=Engineering
@GetMapping
public List<EmployeeResponse> search(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "20") int size,
        @RequestParam(required = false) String department) { ... }
```

### @RequestBody

Deserializes the request body JSON into a Java object (via Jackson).

```java
// POST /api/tasks
// Body: {"title": "New Task", "description": "..."}
@PostMapping
public ResponseEntity<TaskResponse> create(@RequestBody CreateTaskRequest request) { ... }
```

---

## 5. ResponseEntity

`ResponseEntity<T>` gives you full control over the HTTP response: status code, headers, and body.

```java
// Return 200 with a body
return ResponseEntity.ok(employee);

// Return 201 Created with Location header and body
return ResponseEntity.created(URI.create("/api/tasks/" + task.id())).body(task);

// Return 204 No Content (empty body)
return ResponseEntity.noContent().build();

// Return 404 Not Found (empty body)
return ResponseEntity.notFound().build();

// Return 400 with custom error body
return ResponseEntity.badRequest().body(new ErrorResponse("Invalid input"));

// Build a custom response
return ResponseEntity
        .status(HttpStatus.CONFLICT)
        .header("X-Reason", "email-taken")
        .body(new ErrorResponse("Email already registered"));
```

---

## 6. JSON with Jackson

Spring Boot includes Jackson, which automatically serializes Java objects to JSON and deserializes JSON to Java objects.

```java
// This Java object...
public record TaskResponse(
    Long id,
    String title,
    String description,
    TaskStatus status,
    LocalDate dueDate,
    LocalDateTime createdAt
) {}

// ...is serialized to JSON automatically:
// {
//   "id": 1,
//   "title": "Write tests",
//   "description": "Unit tests for TaskService",
//   "status": "PENDING",
//   "dueDate": "2025-12-31",
//   "createdAt": "2025-01-15T10:30:00"
// }
```

Useful Jackson annotations:

```java
public class Employee {

    @JsonIgnore           // exclude from JSON output
    private String passwordHash;

    @JsonProperty("full_name")  // use this name in JSON
    private String fullName;

    @JsonFormat(pattern = "yyyy-MM-dd")  // date format in JSON
    private LocalDate hireDate;
}
```

---

## 7. Input Validation

Never trust client input. Spring Boot's validation starter lets you annotate request objects with constraints.

### Add the Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

### Annotate the Request Object

```java
public record CreateTaskRequest(

    @NotBlank(message = "Title is required")
    @Size(min = 1, max = 200, message = "Title must be between 1 and 200 characters")
    String title,

    @Size(max = 2000, message = "Description cannot exceed 2000 characters")
    String description,

    @NotNull(message = "Due date is required")
    @Future(message = "Due date must be in the future")
    LocalDate dueDate
) {}
```

Common validation annotations:

| Annotation | Validates |
|-----------|----------|
| `@NotNull` | Value is not null |
| `@NotBlank` | String is not null, empty, or whitespace |
| `@NotEmpty` | String/collection is not null or empty |
| `@Size(min, max)` | String length or collection size |
| `@Min(value)` | Number >= value |
| `@Max(value)` | Number <= value |
| `@Email` | Valid email format |
| `@Pattern(regexp)` | Matches regex |
| `@Positive` | Number > 0 |
| `@Future` | Date is in the future |
| `@Past` | Date is in the past |

### Trigger Validation in Controller

Add `@Valid` before the `@RequestBody` parameter:

```java
@PostMapping
public ResponseEntity<TaskResponse> createTask(@Valid @RequestBody CreateTaskRequest request) {
    // if validation fails, Spring throws MethodArgumentNotValidException
    // before this method body runs
    TaskResponse created = taskService.create(request);
    return ResponseEntity.created(URI.create("/api/tasks/" + created.id())).body(created);
}
```

---

## 8. Global Exception Handling with @ControllerAdvice

Without a global exception handler, Spring returns a generic 500 error body for unhandled exceptions. `@ControllerAdvice` lets you intercept exceptions and return consistent, meaningful error responses.

```java
// Error response record
public record ErrorResponse(String message, List<String> errors) {
    public ErrorResponse(String message) {
        this(message, List.of());
    }
}
```

```java
@ControllerAdvice
public class GlobalExceptionHandler {

    // Handle validation errors (from @Valid)
    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidationErrors(
            MethodArgumentNotValidException ex) {

        List<String> errors = ex.getBindingResult()
                .getFieldErrors()
                .stream()
                .map(error -> error.getField() + ": " + error.getDefaultMessage())
                .toList();

        return ResponseEntity.badRequest().body(new ErrorResponse("Validation failed", errors));
    }

    // Handle entity not found
    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(EntityNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
                .body(new ErrorResponse(ex.getMessage()));
    }

    // Handle any other unexpected exception
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        // Log the full exception here (use a logger, not System.out)
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(new ErrorResponse("An unexpected error occurred"));
    }
}
```

---

## 9. Complete Example: Task REST API

### Entity

```java
@Entity
@Table(name = "tasks")
public class Task {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 200)
    private String title;

    @Column(length = 2000)
    private String description;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private TaskStatus status = TaskStatus.PENDING;

    @Column(name = "due_date")
    private LocalDate dueDate;

    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt = LocalDateTime.now();

    // constructors, getters, setters ...
}
```

```java
public enum TaskStatus {
    PENDING, IN_PROGRESS, COMPLETED, CANCELLED
}
```

### Repository

```java
@Repository
public interface TaskRepository extends JpaRepository<Task, Long> {
    List<Task> findByStatus(TaskStatus status);
    List<Task> findByDueDateBefore(LocalDate date);
}
```

### Request / Response Records

```java
public record CreateTaskRequest(
    @NotBlank String title,
    String description,
    @NotNull @Future LocalDate dueDate
) {}

public record UpdateTaskRequest(
    @NotBlank String title,
    String description,
    @NotNull TaskStatus status,
    LocalDate dueDate
) {}

public record TaskResponse(
    Long id, String title, String description,
    TaskStatus status, LocalDate dueDate, LocalDateTime createdAt
) {
    public static TaskResponse from(Task task) {
        return new TaskResponse(task.getId(), task.getTitle(), task.getDescription(),
            task.getStatus(), task.getDueDate(), task.getCreatedAt());
    }
}
```

### Service

```java
@Service
public class TaskService {

    private final TaskRepository taskRepository;

    public TaskService(TaskRepository taskRepository) {
        this.taskRepository = taskRepository;
    }

    @Transactional(readOnly = true)
    public List<TaskResponse> findAll() {
        return taskRepository.findAll().stream()
                .map(TaskResponse::from)
                .toList();
    }

    @Transactional(readOnly = true)
    public Optional<TaskResponse> findById(Long id) {
        return taskRepository.findById(id).map(TaskResponse::from);
    }

    @Transactional
    public TaskResponse create(CreateTaskRequest request) {
        Task task = new Task();
        task.setTitle(request.title());
        task.setDescription(request.description());
        task.setDueDate(request.dueDate());
        return TaskResponse.from(taskRepository.save(task));
    }

    @Transactional
    public Optional<TaskResponse> update(Long id, UpdateTaskRequest request) {
        return taskRepository.findById(id).map(task -> {
            task.setTitle(request.title());
            task.setDescription(request.description());
            task.setStatus(request.status());
            task.setDueDate(request.dueDate());
            return TaskResponse.from(taskRepository.save(task));
        });
    }

    @Transactional
    public boolean delete(Long id) {
        if (!taskRepository.existsById(id)) {
            return false;
        }
        taskRepository.deleteById(id);
        return true;
    }
}
```

### Controller

```java
@RestController
@RequestMapping("/api/tasks")
public class TaskController {

    private final TaskService taskService;

    public TaskController(TaskService taskService) {
        this.taskService = taskService;
    }

    @GetMapping
    public List<TaskResponse> getAllTasks() {
        return taskService.findAll();
    }

    @GetMapping("/{id}")
    public ResponseEntity<TaskResponse> getTaskById(@PathVariable Long id) {
        return taskService.findById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PostMapping
    public ResponseEntity<TaskResponse> createTask(@Valid @RequestBody CreateTaskRequest request) {
        TaskResponse created = taskService.create(request);
        URI location = URI.create("/api/tasks/" + created.id());
        return ResponseEntity.created(location).body(created);
    }

    @PutMapping("/{id}")
    public ResponseEntity<TaskResponse> updateTask(
            @PathVariable Long id,
            @Valid @RequestBody UpdateTaskRequest request) {

        return taskService.update(id, request)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteTask(@PathVariable Long id) {
        return taskService.delete(id)
                ? ResponseEntity.noContent().build()
                : ResponseEntity.notFound().build();
    }
}
```

### Testing with curl

```bash
# Create a task
curl -X POST http://localhost:8080/api/tasks \
  -H "Content-Type: application/json" \
  -d '{"title":"Write tests","description":"Unit tests","dueDate":"2025-12-31"}'

# Get all tasks
curl http://localhost:8080/api/tasks

# Get one task
curl http://localhost:8080/api/tasks/1

# Update a task
curl -X PUT http://localhost:8080/api/tasks/1 \
  -H "Content-Type: application/json" \
  -d '{"title":"Write tests","description":"Updated","status":"IN_PROGRESS","dueDate":"2025-12-31"}'

# Delete a task
curl -X DELETE http://localhost:8080/api/tasks/1
```

---

## Key Takeaways

- Use `GET/POST/PUT/DELETE` with their standard semantics. URLs are nouns.
- Status codes communicate the outcome: 200 for success, 201 for creation, 204 for no-content, 400/404/409 for client errors, 500 for server errors.
- `@PathVariable` extracts from the URL, `@RequestParam` extracts query string values, `@RequestBody` reads the request JSON.
- `ResponseEntity` gives full control over status code, headers, and body.
- Always validate input with `@Valid` and constraint annotations. Never trust the client.
- `@ControllerAdvice` with `@ExceptionHandler` provides consistent error responses across the whole API.
