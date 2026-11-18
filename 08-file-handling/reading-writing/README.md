# Reading and Writing Files — Character Streams

## Overview

Java's `java.io` package provides character-based stream classes for reading and writing text files. The two foundational classes are `FileReader` and `FileWriter`. Wrapping them in `BufferedReader` and `BufferedWriter` adds an internal buffer that dramatically reduces the number of disk reads and writes.

> **Always use try-with-resources.** Streams hold OS file handles. If you forget to close a stream, you leak that handle. Try-with-resources guarantees the stream is closed, even if an exception is thrown.

---

## FileReader and FileWriter

### FileReader — reading characters from a file

```java
import java.io.FileReader;
import java.io.IOException;

try (FileReader reader = new FileReader("data/notes.txt")) {
    int ch;
    while ((ch = reader.read()) != -1) {  // read() returns -1 at end of file
        System.out.print((char) ch);
    }
}
```

`FileReader` reads one character at a time, which is fine for small files but slow for large ones. Wrap it in `BufferedReader` for real use.

### FileWriter — writing characters to a file

```java
import java.io.FileWriter;
import java.io.IOException;

try (FileWriter writer = new FileWriter("output/notes.txt")) {
    writer.write("Hello, World!\n");
    writer.write("Second line.\n");
}
// File is automatically closed here
```

> `FileWriter` creates the file if it does not exist. It **overwrites** the file by default.

---

## Appending to a File

Pass `true` as the second argument to `FileWriter` to open in append mode.

```java
try (FileWriter writer = new FileWriter("output/log.txt", true)) {
    writer.write("New log entry\n");
}
```

---

## BufferedReader and BufferedWriter

Wrapping a `FileReader`/`FileWriter` with a buffer reduces system calls from one per character to one per buffer fill (default 8 KB). Always use this for files larger than a few bytes.

### BufferedReader — reading line by line

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

try (BufferedReader reader = new BufferedReader(new FileReader("data/students.csv"))) {
    String line;
    while ((line = reader.readLine()) != null) {  // readLine() returns null at EOF
        System.out.println(line);
    }
}
```

`readLine()` returns the line content without the line terminator, or `null` when the end of file is reached.

### BufferedWriter — writing lines to a file

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

try (BufferedWriter writer = new BufferedWriter(new FileWriter("output/report.txt"))) {
    writer.write("Name,Score");
    writer.newLine();              // platform-correct line separator
    writer.write("Alice,95");
    writer.newLine();
    writer.write("Bob,88");
    writer.newLine();
}
```

Use `writer.newLine()` instead of hardcoding `"\n"` — it uses the system line separator.

---

## Reading All Lines Into a List

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public List<String> readAllLines(String filePath) throws IOException {
    List<String> lines = new ArrayList<>();
    try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
        String line;
        while ((line = reader.readLine()) != null) {
            lines.add(line);
        }
    }
    return lines;
}
```

---

## Writing a List of Strings to a File

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;
import java.util.List;

public void writeLines(String filePath, List<String> lines) throws IOException {
    try (BufferedWriter writer = new BufferedWriter(new FileWriter(filePath))) {
        for (String line : lines) {
            writer.write(line);
            writer.newLine();
        }
    }
}
```

---

## Complete Example — Copy a Text File

```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.FileReader;
import java.io.FileWriter;
import java.io.IOException;

public class FileCopy {

    public static void copyFile(String source, String destination) throws IOException {
        try (
            BufferedReader reader = new BufferedReader(new FileReader(source));
            BufferedWriter writer = new BufferedWriter(new FileWriter(destination))
        ) {
            String line;
            while ((line = reader.readLine()) != null) {
                writer.write(line);
                writer.newLine();
            }
        }
    }

    public static void main(String[] args) throws IOException {
        copyFile("input.txt", "output.txt");
        System.out.println("File copied successfully.");
    }
}
```

Try-with-resources can manage multiple resources — both `reader` and `writer` are closed automatically in reverse order of declaration.

---

## Specifying the Character Encoding

`FileReader` and `FileWriter` use the platform's default encoding, which varies by OS. This can silently corrupt files with accented characters or emoji. Specify UTF-8 explicitly:

```java
import java.io.BufferedReader;
import java.io.FileInputStream;
import java.io.InputStreamReader;
import java.io.BufferedWriter;
import java.io.FileOutputStream;
import java.io.OutputStreamWriter;
import java.nio.charset.StandardCharsets;

// Reading with explicit encoding
try (BufferedReader reader = new BufferedReader(
        new InputStreamReader(new FileInputStream("data.txt"), StandardCharsets.UTF_8))) {
    // ...
}

// Writing with explicit encoding
try (BufferedWriter writer = new BufferedWriter(
        new OutputStreamWriter(new FileOutputStream("output.txt"), StandardCharsets.UTF_8))) {
    // ...
}
```

> This is verbose. For new code, use NIO.2's `Files.readString()` and `Files.writeString()` which accept a charset directly — see [nio-paths/README.md](../nio-paths/README.md).

---

## Class Hierarchy Summary

```
Reader
├── InputStreamReader
│   └── FileReader
└── BufferedReader  (wraps any Reader)

Writer
├── OutputStreamWriter
│   └── FileWriter
└── BufferedWriter  (wraps any Writer)
```

---

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Not closing the stream | Use try-with-resources |
| Hardcoding `"\n"` for newlines | Use `writer.newLine()` |
| Using `FileReader` without a charset | Use `InputStreamReader` with `StandardCharsets.UTF_8`, or switch to NIO.2 |
| Ignoring the return value of `readLine()` without null check | Always check for `null` |
| Opening `FileWriter` in a loop (overwrites each time) | Open once outside the loop |
