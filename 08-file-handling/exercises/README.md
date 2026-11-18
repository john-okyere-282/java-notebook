# Phase 08 Exercises — File Handling and I/O

Complete these exercises in order. Each exercise builds on knowledge from the previous ones.

---

## Exercise 1 — Check File Existence

**Concepts:** `File` class, `exists()`, `isFile()`, `isDirectory()`

Create a program that accepts a path string from the user, then prints:
- Whether the path exists
- Whether it is a file or a directory
- If it is a file: its size in bytes and its parent directory
- If it is a directory: how many entries it contains

---

## Exercise 2 — Create Directory Structure

**Concepts:** `File.mkdirs()`, `File.createNewFile()`

Write a method `createProjectStructure(String projectName)` that creates the following directory tree inside a `projects/` folder:

```
projects/
└── <projectName>/
    ├── src/
    ├── test/
    └── docs/
```

Also create an empty `README.md` file inside each folder. Print a confirmation message for each directory and file created.

---

## Exercise 3 — Write to a File

**Concepts:** `BufferedWriter`, `FileWriter`, try-with-resources

Write a program that creates a file called `shopping-list.txt` and writes the following items to it, one per line:

```
Milk
Bread
Eggs
Butter
Coffee
```

Then re-open the file in append mode and add `"Orange Juice"` to the end. Read the file back and print its contents to verify.

---

## Exercise 4 — Read and Count Lines

**Concepts:** `BufferedReader`, `readLine()`

Write a method `countLines(String filePath)` that opens a text file, counts the number of non-empty lines, and returns the count. Test it on a file you create in a previous exercise.

---

## Exercise 5 — Word Frequency Counter (File Input)

**Concepts:** `BufferedReader`, `String.split()`, `HashMap`

Write a program that:
1. Reads a text file provided by the user.
2. Splits each line into words (split on whitespace, strip punctuation).
3. Counts how many times each word appears (case-insensitive).
4. Prints the top 10 most frequent words and their counts.

---

## Exercise 6 — CSV Reader

**Concepts:** `Files.readAllLines()`, `String.split()`, `List`

Create a CSV file `students.csv` with this content:

```
Name,Score,Grade
Alice,95,A
Bob,72,B
Carol,61,C
David,45,F
Eve,88,B
```

Write a program that reads this file, parses each row, and prints only the students who passed (score >= 50), formatted as: `Alice scored 95 (A)`.

---

## Exercise 7 — NIO.2 File Copy Utility

**Concepts:** `Files.copy()`, `Path`, `StandardCopyOption`

Write a method `copyWithBackup(Path source, Path destination)` that:
1. If the destination already exists, renames it to `<filename>.bak` before copying.
2. Copies the source file to the destination.
3. Verifies the copy succeeded by comparing file sizes.

---

## Exercise 8 — Directory Listing Tool

**Concepts:** `Files.walk()`, `Stream<Path>`, filtering

Write a program that accepts a root directory path and prints a tree-like listing of all files and directories under it. For each file, also print its size in bytes. Only go 2 levels deep.

Expected output format:
```
src/
  main/
    Main.java (1,204 bytes)
    Utils.java (823 bytes)
  test/
    MainTest.java (540 bytes)
```

---

## Exercise 9 — Log File Processor

**Concepts:** `Files.lines()`, `Stream`, `StandardCharsets.UTF_8`, `Files.writeString()`

Create a sample log file with lines like:

```
2024-01-15 INFO  Application started
2024-01-15 DEBUG Loading configuration
2024-01-15 ERROR NullPointerException in UserService.java:42
2024-01-15 INFO  Request processed
2024-01-15 ERROR FileNotFoundException: config.properties not found
```

Write a program that:
1. Reads the log file using `Files.lines()`.
2. Filters only `ERROR` lines.
3. Writes the error lines to a new file `errors.log`.
4. Prints the count of errors found.

---

## Exercise 10 — Notepad Application

**Concepts:** All file I/O concepts, `Scanner` for user input

Build a simple command-line notepad. The program should:
1. Ask the user for a filename on startup.
2. If the file exists, load and display its content.
3. Present a menu:
   - `1` — View content
   - `2` — Append a new line
   - `3` — Overwrite content
   - `4` — Delete the file
   - `5` — Exit
4. All file operations must use NIO.2 (`Files` utility class with `StandardCharsets.UTF_8`).
5. Handle errors gracefully — if the file doesn't exist when trying to read, display a friendly message instead of crashing.
