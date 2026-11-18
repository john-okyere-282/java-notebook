# The Legacy `File` Class

## What Is It?

`java.io.File` is the original Java class for representing a file or directory path. It was introduced in Java 1.0. It does **not** open or read files — it only holds a path and provides metadata operations on it. For reading and writing content, you use streams (covered in [reading-writing](../reading-writing/README.md)) or the modern NIO.2 API (covered in [nio-paths](../nio-paths/README.md)).

> **Why learn it?** Legacy codebases use `File` extensively. Libraries and frameworks still accept `File` arguments. You need to recognise and work with it.

---

## Creating a File Object

A `File` object is just a path representation — the file does not need to exist on disk.

```java
import java.io.File;

File file = new File("data/report.txt");        // relative path
File absolute = new File("C:/Users/john/notes.txt"); // absolute path
File fromParts = new File("data", "report.txt"); // parent + child
```

---

## Checking Existence and Type

```java
File file = new File("data/report.txt");

boolean exists      = file.exists();       // true if file or directory exists
boolean isFile      = file.isFile();       // true if it is a regular file
boolean isDirectory = file.isDirectory();  // true if it is a directory

System.out.println("Exists: " + exists);
System.out.println("Is file: " + isFile);
System.out.println("Is directory: " + isDirectory);
```

---

## Getting Path Information

```java
File file = new File("data/report.txt");

String name         = file.getName();          // "report.txt"
String path         = file.getPath();          // "data/report.txt" (as given)
String absolutePath = file.getAbsolutePath();  // "C:\Users\john\project\data\report.txt"
String parent       = file.getParent();        // "data"
```

| Method | Returns |
|--------|---------|
| `getName()` | The last component of the path (filename or directory name) |
| `getPath()` | The path as it was passed to the constructor |
| `getAbsolutePath()` | The full path from the filesystem root |
| `getParent()` | The parent directory path string, or `null` if none |

---

## File Size

```java
File file = new File("data/report.txt");

long bytes = file.length(); // size in bytes; 0 if file does not exist
System.out.println("Size: " + bytes + " bytes");
```

---

## Listing Directory Contents

```java
File directory = new File("data");

// Returns array of name strings, or null if not a directory
String[] names = directory.list();
if (names != null) {
    for (String name : names) {
        System.out.println(name);
    }
}

// Returns array of File objects — more useful
File[] files = directory.listFiles();
if (files != null) {
    for (File f : files) {
        System.out.println(f.getAbsolutePath() + " | " + (f.isDirectory() ? "DIR" : "FILE"));
    }
}
```

---

## Creating Directories

```java
File dir = new File("output/reports/2024");

// mkdir() creates only the final directory; fails if parents do not exist
boolean created = dir.mkdir();

// mkdirs() creates all missing parent directories
boolean createdAll = dir.mkdirs();
System.out.println("Directories created: " + createdAll);
```

---

## Deleting Files and Directories

```java
File file = new File("data/temp.txt");
boolean deleted = file.delete(); // returns true on success

// Deleting a directory only works if it is empty
File emptyDir = new File("output/empty");
boolean dirDeleted = emptyDir.delete();
```

> **Warning:** `delete()` is permanent — there is no recycle bin. For recursive directory deletion, use NIO.2's `Files.walk()`.

---

## Creating a New File

```java
import java.io.File;
import java.io.IOException;

File file = new File("data/newfile.txt");
try {
    boolean created = file.createNewFile(); // creates if not exists; false if already exists
    System.out.println("File created: " + created);
} catch (IOException e) {
    System.err.println("Could not create file: " + e.getMessage());
}
```

---

## Complete Example

```java
import java.io.File;
import java.io.IOException;

public class FileClassDemo {

    public static void main(String[] args) throws IOException {
        File dir = new File("sandbox");
        dir.mkdirs();

        File file = new File(dir, "notes.txt");
        file.createNewFile();

        System.out.println("Name:          " + file.getName());
        System.out.println("Absolute path: " + file.getAbsolutePath());
        System.out.println("Exists:        " + file.exists());
        System.out.println("Is file:       " + file.isFile());
        System.out.println("Size:          " + file.length() + " bytes");

        file.delete();
        dir.delete();
    }
}
```

---

## Summary of Key Methods

| Method | Return Type | Description |
|--------|-------------|-------------|
| `exists()` | `boolean` | Whether the path exists on disk |
| `isFile()` | `boolean` | Whether it is a regular file |
| `isDirectory()` | `boolean` | Whether it is a directory |
| `getName()` | `String` | Last component of the path |
| `getPath()` | `String` | Path as given to constructor |
| `getAbsolutePath()` | `String` | Full absolute path |
| `getParent()` | `String` | Parent directory path |
| `length()` | `long` | File size in bytes |
| `list()` | `String[]` | Names of files in directory |
| `listFiles()` | `File[]` | File objects in directory |
| `mkdirs()` | `boolean` | Create directory tree |
| `delete()` | `boolean` | Delete file or empty directory |
| `createNewFile()` | `boolean` | Atomically create a new empty file |

---

## When to Use `File` vs NIO.2

Use `File` when:
- Working with a legacy API that requires a `File` parameter.
- Doing a quick `exists()` check in older code.

Use NIO.2 (`Path` / `Files`) for all new code — it has better exception messages, supports symbolic links, and covers far more operations. See [nio-paths/README.md](../nio-paths/README.md).
