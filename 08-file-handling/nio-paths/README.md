# NIO.2 — The Modern File API

## Overview

Java 7 introduced the `java.nio.file` package (NIO.2), which replaces most uses of `java.io.File`. The two key abstractions are:

- **`Path`** — represents a file or directory path (like `File`, but richer).
- **`Files`** — a utility class with static methods for all common file operations.

NIO.2 throws `IOException` subclasses with descriptive messages (e.g. `NoSuchFileException`, `FileAlreadyExistsException`) instead of silently returning `false`. It also supports symbolic links, file attributes, file watching, and async I/O.

---

## Path and Paths

```java
import java.nio.file.Path;
import java.nio.file.Paths;

Path path = Path.of("data/report.txt");        // Java 11+ factory method (preferred)
Path legacy = Paths.get("data/report.txt");    // Java 7+ — same result

Path absolute = Path.of("C:/Users/john/notes.txt");
Path combined = Path.of("data").resolve("report.txt"); // "data/report.txt"

System.out.println(path.getFileName());   // report.txt
System.out.println(path.getParent());     // data
System.out.println(path.toAbsolutePath()); // full path from root
```

`Path` is immutable. All methods return new `Path` instances.

---

## Reading File Content

### Read the entire file as a String

```java
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.charset.StandardCharsets;
import java.io.IOException;

Path path = Path.of("data/notes.txt");
String content = Files.readString(path, StandardCharsets.UTF_8); // Java 11+
System.out.println(content);
```

### Read all lines into a List

```java
import java.util.List;

List<String> lines = Files.readAllLines(Path.of("data/students.csv"), StandardCharsets.UTF_8);
for (String line : lines) {
    System.out.println(line);
}
```

### Read as a Stream of lines (lazy — good for large files)

```java
import java.util.stream.Stream;

try (Stream<String> lines = Files.lines(Path.of("data/large.log"), StandardCharsets.UTF_8)) {
    lines.filter(line -> line.contains("ERROR"))
         .forEach(System.out::println);
}
```

> Wrap `Files.lines()` in try-with-resources — it opens a stream that must be closed.

---

## Writing File Content

### Write a String to a file

```java
import java.nio.file.StandardOpenOption;

Path path = Path.of("output/report.txt");
String content = "Hello, World!\nSecond line.\n";

Files.writeString(path, content, StandardCharsets.UTF_8); // Java 11+ — overwrites
```

### Append to a file

```java
Files.writeString(
    Path.of("output/log.txt"),
    "New entry\n",
    StandardCharsets.UTF_8,
    StandardOpenOption.APPEND,
    StandardOpenOption.CREATE   // create if does not exist
);
```

### Write a list of lines

```java
List<String> lines = List.of("Alice,95", "Bob,88", "Carol,72");
Files.write(
    Path.of("output/grades.csv"),
    lines,
    StandardCharsets.UTF_8
);
```

---

## Checking Existence and File Metadata

```java
Path path = Path.of("data/report.txt");

boolean exists    = Files.exists(path);
boolean isFile    = Files.isRegularFile(path);
boolean isDir     = Files.isDirectory(path);
boolean readable  = Files.isReadable(path);
boolean writable  = Files.isWritable(path);
long    size      = Files.size(path);         // throws if file doesn't exist

System.out.println("Size: " + size + " bytes");
```

---

## Creating Files and Directories

```java
// Create a single empty file (throws FileAlreadyExistsException if exists)
Files.createFile(Path.of("output/newfile.txt"));

// Create a single directory
Files.createDirectory(Path.of("output/reports"));

// Create all missing parent directories (like mkdir -p)
Files.createDirectories(Path.of("output/reports/2024/january"));
```

---

## Deleting Files

```java
// Throws NoSuchFileException if does not exist
Files.delete(Path.of("output/temp.txt"));

// Does not throw if file does not exist
boolean deleted = Files.deleteIfExists(Path.of("output/temp.txt"));
```

---

## Copying and Moving Files

```java
import java.nio.file.StandardCopyOption;

Path source = Path.of("data/original.txt");
Path target = Path.of("backup/original.txt");

// Copy — fails if target exists unless REPLACE_EXISTING is specified
Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);

// Move (rename) — atomic on most filesystems
Files.move(source, Path.of("archive/original.txt"), StandardCopyOption.REPLACE_EXISTING);
```

---

## Directory Traversal with `Files.walk()`

`Files.walk()` returns a lazy `Stream<Path>` of all entries under a directory, recursively.

```java
import java.util.stream.Stream;

Path dir = Path.of("src");

// Print all .java files
try (Stream<Path> stream = Files.walk(dir)) {
    stream.filter(p -> p.toString().endsWith(".java"))
          .forEach(System.out::println);
}
```

### Delete a directory tree recursively

```java
try (Stream<Path> stream = Files.walk(Path.of("output/temp"))) {
    stream.sorted(java.util.Comparator.reverseOrder()) // delete children before parents
          .forEach(p -> {
              try { Files.delete(p); }
              catch (IOException e) { throw new RuntimeException(e); }
          });
}
```

---

## Walking Only Immediate Children

```java
// Non-recursive — only direct children
try (Stream<Path> entries = Files.list(Path.of("data"))) {
    entries.forEach(System.out::println);
}
```

---

## StandardCharsets.UTF_8

Always pass `StandardCharsets.UTF_8` explicitly to avoid platform-encoding bugs.

```java
import java.nio.charset.StandardCharsets;

// Good
String text = Files.readString(path, StandardCharsets.UTF_8);

// Bad — uses platform default, which may not be UTF-8 on Windows
String text = Files.readString(path);
```

`StandardCharsets` provides constants for common charsets: `UTF_8`, `US_ASCII`, `ISO_8859_1`, `UTF_16`.

---

## Complete Example — Read, Process, Write

```java
import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.nio.file.Files;
import java.nio.file.Path;
import java.util.List;
import java.util.stream.Collectors;

public class NioExample {

    public static void main(String[] args) throws IOException {
        Path input  = Path.of("data/scores.csv");
        Path output = Path.of("output/passing.csv");

        Files.createDirectories(output.getParent());

        // Read all lines, filter for scores >= 50, write result
        List<String> passing = Files.lines(input, StandardCharsets.UTF_8)
            .filter(line -> {
                String[] parts = line.split(",");
                return Integer.parseInt(parts[1].trim()) >= 50;
            })
            .collect(Collectors.toList());

        Files.write(output, passing, StandardCharsets.UTF_8);
        System.out.println("Wrote " + passing.size() + " passing students.");
    }
}
```

---

## NIO.2 vs java.io — When to Use Which

| Situation | Use |
|-----------|-----|
| New code, all file operations | NIO.2 (`Path` + `Files`) |
| API requires a `File` parameter | `file.toPath()` to convert, or `path.toFile()` |
| Reading/writing large files character by character | `BufferedReader`/`BufferedWriter` via `Files.newBufferedReader()` |
| Legacy codebase | `java.io.File` as-is |

```java
// Convert between File and Path
File oldFile = new File("data/notes.txt");
Path path    = oldFile.toPath();      // File → Path

Path newPath = Path.of("data/notes.txt");
File asFile  = newPath.toFile();      // Path → File
```

---

## Key NIO.2 Classes Summary

| Class / Interface | Package | Purpose |
|-------------------|---------|---------|
| `Path` | `java.nio.file` | Represents a path; immutable |
| `Paths` | `java.nio.file` | Factory for `Path` (use `Path.of()` in Java 11+) |
| `Files` | `java.nio.file` | Static utility methods for file operations |
| `StandardOpenOption` | `java.nio.file` | Options like `APPEND`, `CREATE`, `TRUNCATE_EXISTING` |
| `StandardCopyOption` | `java.nio.file` | Options like `REPLACE_EXISTING`, `COPY_ATTRIBUTES` |
| `StandardCharsets` | `java.nio.charset` | Charset constants (`UTF_8`, `US_ASCII`, etc.) |
| `FileVisitor` | `java.nio.file` | Interface for `Files.walkFileTree()` callbacks |
