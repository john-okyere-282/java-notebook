# Project 02 — Student Grade Management System

**Difficulty:** 🟢 Beginner  
**Estimated time:** 4–6 hours  
**Key skills:** OOP, ArrayList, methods, averages, letter grades

> **Attempt the implementation yourself before reading the reference solution.**  
> Reference solution available in: `15-projects/solutions/README.md`

---

## Real-World Problem

A teacher manages a class of students and wants a simple tool to record grades, calculate averages, and get a quick view of class performance. Each student can have multiple grades, and the system must correctly convert numeric averages to letter grades.

---

## Learning Objectives

- Model a real entity as a Java class with fields and behavior
- Use `ArrayList<Double>` to store a variable number of grades
- Write methods that compute values from a list (`calculateAverage`, `getLetterGrade`)
- Build a simple text-based menu application
- Apply encapsulation: grades are managed through methods, not direct field access

---

## Requirements

### Student Model

The `Student` class must have:

| Field | Type | Description |
|-------|------|-------------|
| `name` | `String` | Student's full name |
| `studentId` | `String` | Unique identifier (e.g., `"STU-001"`) |
| `grades` | `List<Double>` | Private list of grades (0–100) |

Methods:

| Method | Description |
|--------|-------------|
| `addGrade(double grade)` | Adds a grade; throw `IllegalArgumentException` if grade < 0 or > 100 |
| `calculateAverage()` | Returns average of all grades; returns 0.0 if no grades |
| `getLetterGrade()` | Returns `A`, `B`, `C`, `D`, or `F` based on average |
| `getGrades()` | Returns an unmodifiable copy of the grades list |

**Letter Grade Scale:**

| Average | Letter |
|---------|--------|
| 90–100 | A |
| 80–89 | B |
| 70–79 | C |
| 60–69 | D |
| Below 60 | F |

### Application Features

1. **Add a student** — prompt for name and ID, create a `Student`, add to the list
2. **Add a grade to a student** — find student by ID, add a grade
3. **View all students** — display each student's name, ID, average, and letter grade
4. **View student detail** — show one student's full grade list, average, and letter grade
5. **Class statistics** — show the class average across all students
6. **Exit**

### Validation Rules

- Student ID must be unique — reject duplicate IDs
- Grades must be between 0 and 100
- When searching by ID, show a clear message if the student is not found

---

## Suggested Class Structure

```
src/
├── Student.java         ← data model + grade logic
├── GradeSystem.java     ← manages the list of students; business operations
└── Main.java            ← menu, Scanner, user interaction
```

---

## Implementation Steps

1. **Create `Student.java`** with private fields, a constructor that takes `name` and `studentId`, and all required methods.

2. **Implement `calculateAverage()`** using either a loop or `grades.stream().mapToDouble(...).average().orElse(0)`.

3. **Implement `getLetterGrade()`** using the scale above. Consider using `if-else if` or a `switch` expression.

4. **Create `GradeSystem.java`** with a `List<Student>` field and methods:
   - `addStudent(Student student)` — rejects duplicate IDs
   - `findById(String id)` — returns `Optional<Student>`
   - `getAllStudents()` — returns unmodifiable list
   - `calculateClassAverage()` — average of all student averages

5. **Create `Main.java`** with the menu loop and Scanner.

6. **Test manually** by adding 3–5 students, adding grades, and checking the output.

---

## Expected Behavior — Sample Run

```
=== Student Grade Management System ===
1. Add Student
2. Add Grade
3. View All Students
4. View Student Detail
5. Class Statistics
6. Exit

Choice: 1
Name: Alice Johnson
Student ID: STU-001
Student Alice Johnson added.

Choice: 2
Student ID: STU-001
Grade: 92
Grade 92.0 added for Alice Johnson.

Choice: 2
Student ID: STU-001
Grade: 88
Grade 88.0 added for Alice Johnson.

Choice: 3
ID        Name                  Average   Grade
STU-001   Alice Johnson         90.0      A

Choice: 4
Student ID: STU-001
Student: Alice Johnson (STU-001)
Grades: [92.0, 88.0]
Average: 90.0
Letter Grade: A

Choice: 5
Class average: 90.0

Choice: 6
Goodbye!
```

---

## Testing Requirements

- [ ] Add multiple students and verify they appear in "View All"
- [ ] Add grades to a student and verify average updates correctly
- [ ] Add a grade below 0 or above 100 — application should not crash
- [ ] Try to add a student with a duplicate ID — should be rejected
- [ ] Search for a non-existent student ID — should show a clear message
- [ ] A student with no grades should show 0.0 average and F grade
- [ ] Class statistics with multiple students

---

## Extension Challenges

1. **Remove a student** — by ID, with confirmation.
2. **Remove a grade** — the most recently added grade for a given student.
3. **Sort students** — by average descending when viewing all.
4. **Top performer** — a method that returns the student with the highest average.
5. **Grade distribution** — count how many A, B, C, D, F grades exist across all students.
6. **File save/load** — save students and grades to a CSV file and reload on startup (prepares you for Project 10).
