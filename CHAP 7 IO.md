# Comprehensive Java File I/O Notes
## WIX1002 - Fundamentals of Programming - Chapter 7

---

## Table of Contents
1. [Introduction to File I/O](#1-introduction-to-file-io)
2. [Understanding Streams](#2-understanding-streams)
3. [Text Files vs Binary Files](#3-text-files-vs-binary-files)
4. [Exception Handling in File Operations](#4-exception-handling-in-file-operations)
5. [Writing to Text Files](#5-writing-to-text-files)
6. [Reading from Text Files](#6-reading-from-text-files)
7. [The File Class](#7-the-file-class)
8. [Writing to Binary Files](#8-writing-to-binary-files)
9. [Reading from Binary Files](#9-reading-from-binary-files)
10. [Working with Directories](#10-working-with-directories)
11. [Best Practices and Modern Approaches](#11-best-practices-and-modern-approaches)
12. [Complete Working Examples](#12-complete-working-examples)

---

## 1. Introduction to File I/O

### What is File I/O?
**File I/O** (Input/Output) refers to reading data from files and writing data to files. It's essential for:
- **Persistent Storage**: Data survives after program termination
- **Large Data Sets**: Handling data too large for memory
- **Data Exchange**: Sharing data between programs
- **Configuration**: Storing application settings
- **Logging**: Recording program activities

### Why Use Files?
- **Permanence**: Data persists beyond program execution
- **Capacity**: Can store massive amounts of data
- **Portability**: Files can be moved between systems
- **Backup**: Easy to create copies of data

---

## 2. Understanding Streams

### What is a Stream?
A **stream** is a sequence of data flowing between a source and a destination. Think of it like a pipe through which data flows.

### Types of Streams

#### Input Stream
- **Definition**: Flow of data FROM a source TO your program
- **Source**: Can be a file, keyboard, network, etc.
- **Purpose**: Reading data into your program
- **Analogy**: Like drinking through a straw (data flows in)

#### Output Stream
- **Definition**: Flow of data FROM your program TO a destination
- **Destination**: Can be a file, screen, network, etc.
- **Purpose**: Writing data from your program
- **Analogy**: Like pouring water out (data flows out)

### Stream Hierarchy in Java

```
InputStream (abstract class for byte input)
├── FileInputStream (read bytes from files)
├── ObjectInputStream (read objects from files)
└── ...

OutputStream (abstract class for byte output)
├── FileOutputStream (write bytes to files)
├── ObjectOutputStream (write objects to files)
└── ...

Reader (abstract class for character input)
├── FileReader (read characters from files)
├── BufferedReader (buffered character reading)
└── ...

Writer (abstract class for character output)
├── FileWriter (write characters to files)
├── PrintWriter (formatted text output)
└── ...
```

### Byte Streams vs Character Streams
- **Byte Streams**: Handle raw binary data (8-bit bytes)
  - Use for: Images, videos, binary files, any non-text data
  - Classes: InputStream, OutputStream
  
- **Character Streams**: Handle text data (16-bit Unicode characters)
  - Use for: Text files, CSV, JSON, XML
  - Classes: Reader, Writer
  - Automatically handles character encoding

---

## 3. Text Files vs Binary Files

### Text Files

**Characteristics:**
- Store data as human-readable characters
- Use character encoding (ASCII, UTF-8, etc.)
- Can be opened and edited in text editors
- Each character typically 1 byte (ASCII) or variable bytes (UTF-8)

**Examples:**
- `.txt` files
- `.csv` files
- `.xml` files
- `.json` files
- `.html` files

**Advantages:**
- Human-readable
- Easy to debug
- Platform-independent (mostly)
- Can use version control effectively

**Disadvantages:**
- Larger file size for numeric data
- Slower parsing
- Precision loss for floating-point numbers

**Example Content:**
```
John Doe
25
85.5
```

### Binary Files

**Characteristics:**
- Store data in binary format (sequence of bytes)
- Not human-readable
- Data stored in internal computer representation
- More compact and efficient

**Examples:**
- `.dat` files
- `.bin` files
- Image files (`.jpg`, `.png`)
- Executable files (`.exe`, `.class`)

**Advantages:**
- Smaller file size
- Faster read/write operations
- Exact precision for numbers
- Can store complex objects

**Disadvantages:**
- Not human-readable
- Platform-dependent (sometimes)
- Harder to debug
- Requires exact reading order

**Example (conceptual):**
```
01001010 01101111 01101000 01101110 (binary representation)
```

### When to Use Which?

| Use Text Files When | Use Binary Files When |
|-------------------|---------------------|
| Data needs to be human-readable | Performance is critical |
| Interoperability is important | File size matters |
| Small amounts of data | Large datasets |
| Configuration files | Multimedia data |
| Data exchange between systems | Internal application data |
| Debugging is important | Storing complex objects |

---

## 4. Exception Handling in File Operations

### Why Exception Handling is Mandatory

File operations can fail for many reasons:
- File doesn't exist
- No permission to read/write
- Disk full
- File is locked by another program
- Path is incorrect

### Common File-Related Exceptions

#### FileNotFoundException
```java
// Thrown when trying to open a file that doesn't exist
Scanner input = new Scanner(new FileInputStream("nonexistent.txt"));
```

#### IOException
```java
// General I/O errors during read/write operations
String line = reader.readLine(); // Could throw IOException
```

#### EOFException
```java
// Thrown when reaching end of file in binary streams
int number = inputStream.readInt(); // Throws when no more data
```

### Exception Handling Approaches

#### 1. Try-Catch Block (Basic)
```java
try {
    PrintWriter writer = new PrintWriter(new FileOutputStream("data.txt"));
    writer.println("Hello");
    writer.close();
} catch (IOException e) {
    System.out.println("Error: " + e.getMessage());
}
```

#### 2. Try-With-Resources (Modern - Recommended)
Automatically closes resources even if exception occurs:

```java
try (PrintWriter writer = new PrintWriter(new FileOutputStream("data.txt"))) {
    writer.println("Hello");
} catch (IOException e) {
    System.out.println("Error: " + e.getMessage());
}
// writer.close() is called automatically
```

#### 3. Multiple Catch Blocks
```java
try {
    BufferedReader reader = new BufferedReader(new FileReader("data.txt"));
    String line = reader.readLine();
    reader.close();
} catch (FileNotFoundException e) {
    System.out.println("File not found!");
} catch (IOException e) {
    System.out.println("Error reading file!");
}
```

---

## 5. Writing to Text Files

### Method 1: Using PrintWriter (Most Common)

#### Basic Syntax
```java
PrintWriter outputStream = new PrintWriter(new FileOutputStream("filename.txt"));
```

#### Required Imports
```java
import java.io.PrintWriter;
import java.io.FileOutputStream;
import java.io.IOException;
```

#### Complete Example
```java
import java.io.*;

public class WriteTextFile {
    public static void main(String[] args) {
        try (PrintWriter writer = new PrintWriter(new FileOutputStream("output.txt"))) {
            // Writing different data types
            writer.print("Hello ");           // No newline
            writer.println("World!");         // With newline
            writer.printf("Number: %d%n", 42); // Formatted output
            writer.println(3.14159);
            
            System.out.println("File written successfully!");
        } catch (IOException e) {
            System.out.println("Error writing file: " + e.getMessage());
        }
    }
}
```

#### Writing Methods

| Method | Description | Example |
|--------|-------------|---------|
| `print()` | Write without newline | `writer.print("Hello");` |
| `println()` | Write with newline | `writer.println("Hello");` |
| `printf()` | Formatted output | `writer.printf("%.2f", 3.14);` |

#### Specifying File Path
```java
// Relative path (current directory)
PrintWriter writer = new PrintWriter(new FileOutputStream("data.txt"));

// Absolute path (Windows)
PrintWriter writer = new PrintWriter(new FileOutputStream("C:/Users/Name/data.txt"));

// Absolute path (Unix/Mac)
PrintWriter writer = new PrintWriter(new FileOutputStream("/home/user/data.txt"));

// Subdirectory
PrintWriter writer = new PrintWriter(new FileOutputStream("files/data.txt"));
```

#### Appending to Existing File
```java
// Second parameter 'true' enables append mode
PrintWriter writer = new PrintWriter(
    new FileOutputStream("data.txt", true)  // Append mode
);
writer.println("This will be added to the end");
```

**Without append mode**: File is overwritten (deleted and recreated)
**With append mode**: New data added to end of existing file

#### Complete Example: Currency Exchange
```java
import java.io.*;

public class WriteCurrency {
    public static void main(String[] args) {
        try (PrintWriter writer = new PrintWriter(new FileOutputStream("currency.txt"))) {
            writer.println("USD 0.245");
            writer.println("EUR 0.205");
            writer.println("GBP 0.184");
            writer.println("AUD 0.332");
            writer.println("THB 7.41");
            
            System.out.println("Currency data written successfully!");
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

### Method 2: Using FileWriter
```java
// Simpler but fewer features than PrintWriter
try (FileWriter writer = new FileWriter("data.txt")) {
    writer.write("Hello World\n");
} catch (IOException e) {
    e.printStackTrace();
}
```

---

## 6. Reading from Text Files

### Method 1: Using Scanner (Most Flexible)

#### Basic Syntax
```java
Scanner inputStream = new Scanner(new FileInputStream("filename.txt"));
```

#### Required Imports
```java
import java.util.Scanner;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
```

#### Complete Example
```java
import java.util.Scanner;
import java.io.*;

public class ReadTextFile {
    public static void main(String[] args) {
        try (Scanner scanner = new Scanner(new FileInputStream("data.txt"))) {
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                System.out.println(line);
            }
        } catch (FileNotFoundException e) {
            System.out.println("File not found!");
        }
    }
}
```

#### Scanner Reading Methods

| Method | Description | Example |
|--------|-------------|---------|
| `nextLine()` | Read entire line | `String line = scanner.nextLine();` |
| `next()` | Read next token (word) | `String word = scanner.next();` |
| `nextInt()` | Read next integer | `int num = scanner.nextInt();` |
| `nextDouble()` | Read next double | `double d = scanner.nextDouble();` |
| `nextBoolean()` | Read next boolean | `boolean b = scanner.nextBoolean();` |

#### Checking Methods (hasNext...)

| Method | Description |
|--------|-------------|
| `hasNextLine()` | Check if more lines exist |
| `hasNext()` | Check if more tokens exist |
| `hasNextInt()` | Check if next token is an integer |
| `hasNextDouble()` | Check if next token is a double |

#### Reading Different Data Types
```java
try (Scanner scanner = new Scanner(new FileInputStream("mixed_data.txt"))) {
    // File contains: "John 25 85.5"
    String name = scanner.next();      // "John"
    int age = scanner.nextInt();       // 25
    double score = scanner.nextDouble(); // 85.5
    
    System.out.println(name + " is " + age + " years old, scored " + score);
} catch (FileNotFoundException e) {
    e.printStackTrace();
}
```

#### Reading with Delimiters
```java
// For CSV or custom delimiters
Scanner scanner = new Scanner(new FileInputStream("data.csv"));
scanner.useDelimiter(",");  // Use comma as delimiter

while (scanner.hasNext()) {
    String field = scanner.next();
    System.out.println(field);
}
```

### Method 2: Using BufferedReader (Faster for Large Files)

#### Basic Syntax
```java
BufferedReader inputStream = new BufferedReader(new FileReader("filename.txt"));
```

#### Required Imports
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.io.FileNotFoundException;
```

#### Complete Example
```java
import java.io.*;

public class BufferedReaderExample {
    public static void main(String[] args) {
        try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        } catch (FileNotFoundException e) {
            System.out.println("File not found!");
        } catch (IOException e) {
            System.out.println("Error reading file!");
        }
    }
}
```

#### BufferedReader Methods

| Method | Description | Return Type |
|--------|-------------|-------------|
| `readLine()` | Read one line | String (null at EOF) |
| `read()` | Read single character | int (character code, -1 at EOF) |
| `read(char[] buffer)` | Read into character array | int (number of chars read) |

#### Reading Character by Character
```java
try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
    int charCode;
    while ((charCode = reader.read()) != -1) {
        char c = (char) charCode;
        System.out.print(c);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

### Scanner vs BufferedReader Comparison

| Feature | Scanner | BufferedReader |
|---------|---------|----------------|
| **Ease of Use** | Easier | More code needed |
| **Type Parsing** | Built-in (nextInt, etc.) | Manual (parse yourself) |
| **Performance** | Slower | Faster |
| **Best For** | Small files, mixed types | Large files, line-by-line |
| **Buffering** | No | Yes (more efficient) |

### Example: Reading Currency File
```java
import java.util.Scanner;
import java.io.*;

public class ReadCurrency {
    public static void main(String[] args) {
        try (Scanner scanner = new Scanner(new FileInputStream("currency.txt"))) {
            System.out.println("Currency Exchange Rates:");
            System.out.println("------------------------");
            
            while (scanner.hasNextLine()) {
                String currency = scanner.next();
                double rate = scanner.nextDouble();
                
                System.out.printf("%s: %.3f%n", currency, rate);
                
                // Calculate example conversion
                double myr = 100;
                double foreign = myr * rate;
                System.out.printf("  100 MYR = %.2f %s%n", foreign, currency);
            }
        } catch (FileNotFoundException e) {
            System.out.println("Currency file not found!");
        }
    }
}
```

---

## 7. The File Class

### Purpose
The **File** class doesn't read or write data. Instead, it:
- Represents file and directory paths
- Checks file properties
- Performs file operations (rename, delete, etc.)
- Lists directory contents

### Required Import
```java
import java.io.File;
```

### Creating File Objects

```java
// Represent a file
File file = new File("data.txt");

// Represent a directory
File dir = new File("myFolder");

// File in specific directory
File file = new File("myFolder/data.txt");

// Using File.separator for OS independence
File file = new File("myFolder" + File.separator + "data.txt");
```

### File Information Methods

#### Checking Existence and Type
```java
File file = new File("data.txt");

if (file.exists()) {
    System.out.println("File exists!");
}

if (file.isFile()) {
    System.out.println("It's a regular file");
}

if (file.isDirectory()) {
    System.out.println("It's a directory");
}

if (file.isHidden()) {
    System.out.println("File is hidden");
}
```

#### Getting File Properties
```java
File file = new File("data.txt");

String name = file.getName();           // "data.txt"
String path = file.getPath();           // Relative or absolute path
String absolutePath = file.getAbsolutePath(); // Full path
String parent = file.getParent();       // Parent directory

long size = file.length();              // Size in bytes
long modified = file.lastModified();    // Last modification time (milliseconds)
```

#### Checking Permissions
```java
File file = new File("data.txt");

if (file.canRead()) {
    System.out.println("File is readable");
}

if (file.canWrite()) {
    System.out.println("File is writable");
}

if (file.canExecute()) {
    System.out.println("File is executable");
}
```

### File Operation Methods

#### Creating Files and Directories
```java
File newFile = new File("newfile.txt");
try {
    if (newFile.createNewFile()) {
        System.out.println("File created!");
    } else {
        System.out.println("File already exists");
    }
} catch (IOException e) {
    e.printStackTrace();
}

// Create directory
File dir = new File("newFolder");
if (dir.mkdir()) {
    System.out.println("Directory created!");
}

// Create multiple directories
File dirs = new File("path/to/folders");
if (dirs.mkdirs()) {
    System.out.println("Directories created!");
}
```

#### Deleting Files
```java
File file = new File("data.txt");

if (file.delete()) {
    System.out.println("File deleted successfully");
} else {
    System.out.println("Failed to delete file");
}
```

#### Renaming/Moving Files
```java
File oldFile = new File("data.txt");
File newFile = new File("data_backup.txt");

if (oldFile.renameTo(newFile)) {
    System.out.println("File renamed successfully");
} else {
    System.out.println("Failed to rename file");
}
```

### Complete File Class Example
```java
import java.io.*;

public class FileOperations {
    public static void main(String[] args) {
        File file = new File("data.txt");
        
        // Check if file exists
        if (file.exists()) {
            System.out.println("File Information:");
            System.out.println("Name: " + file.getName());
            System.out.println("Absolute Path: " + file.getAbsolutePath());
            System.out.println("Size: " + file.length() + " bytes");
            System.out.println("Can Read: " + file.canRead());
            System.out.println("Can Write: " + file.canWrite());
            System.out.println("Is Hidden: " + file.isHidden());
            
            // Rename file
            File newFile = new File("data_backup.txt");
            if (file.renameTo(newFile)) {
                System.out.println("File renamed to: " + newFile.getName());
            }
        } else {
            System.out.println("File does not exist!");
            
            // Create the file
            try {
                if (file.createNewFile()) {
                    System.out.println("File created: " + file.getName());
                }
            } catch (IOException e) {
                System.out.println("Error creating file: " + e.getMessage());
            }
        }
    }
}
```

---

## 8. Writing to Binary Files

### Why Use Binary Files?
- **Efficiency**: Faster read/write operations
- **Size**: Smaller file size for numeric data
- **Precision**: Exact representation of numbers
- **Objects**: Can serialize complex objects

### Using ObjectOutputStream

#### Basic Syntax
```java
ObjectOutputStream outputStream = new ObjectOutputStream(
    new FileOutputStream("data.dat")
);
```

#### Required Imports
```java
import java.io.ObjectOutputStream;
import java.io.FileOutputStream;
import java.io.IOException;
```

#### Writing Primitive Data Types

```java
import java.io.*;

public class WriteBinaryFile {
    public static void main(String[] args) {
        try (ObjectOutputStream output = new ObjectOutputStream(
                new FileOutputStream("data.dat"))) {
            
            // Write different primitive types
            output.writeInt(42);                    // Write integer
            output.writeDouble(3.14159);            // Write double
            output.writeChar('A');                  // Write character
            output.writeBoolean(true);              // Write boolean
            output.writeLong(1234567890L);          // Write long
            output.writeFloat(2.5f);                // Write float
            output.writeUTF("Hello Binary World");  // Write string
            
            System.out.println("Binary data written successfully!");
        } catch (IOException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

#### ObjectOutputStream Write Methods

| Method | Description | Example |
|--------|-------------|---------|
| `writeInt(int)` | Write integer (4 bytes) | `output.writeInt(100);` |
| `writeDouble(double)` | Write double (8 bytes) | `output.writeDouble(3.14);` |
| `writeFloat(float)` | Write float (4 bytes) | `output.writeFloat(2.5f);` |
| `writeLong(long)` | Write long (8 bytes) | `output.writeLong(123L);` |
| `writeShort(short)` | Write short (2 bytes) | `output.writeShort((short)10);` |
| `writeChar(char)` | Write character (2 bytes) | `output.writeChar('A');` |
| `writeBoolean(boolean)` | Write boolean (1 byte) | `output.writeBoolean(true);` |
| `writeByte(byte)` | Write byte (1 byte) | `output.writeByte((byte)5);` |
| `writeUTF(String)` | Write string (UTF format) | `output.writeUTF("Hello");` |

#### Writing Arrays
```java
try (ObjectOutputStream output = new ObjectOutputStream(
        new FileOutputStream("array.dat"))) {
    
    int[] numbers = {10, 20, 30, 40, 50};
    
    // Write array length first
    output.writeInt(numbers.length);
    
    // Write each element
    for (int num : numbers) {
        output.writeInt(num);
    }
} catch (IOException e) {
    e.printStackTrace();
}
```

#### Writing Objects (Serialization)
```java
import java.io.*;

// Class must implement Serializable
class Student implements Serializable {
    private String name;
    private int age;
    private double gpa;
    
    public Student(String name, int age, double gpa) {
        this.name = name;
        this.age = age;
        this.gpa = gpa;
    }
}

public class WriteObject {
    public static void main(String[] args) {
        try (ObjectOutputStream output = new ObjectOutputStream(
                new FileOutputStream("student.dat"))) {
            
            Student student = new Student("Alice", 20, 3.8);
            output.writeObject(student);  // Write entire object
            
            System.out.println("Object written successfully!");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 9. Reading from Binary Files

### Using ObjectInputStream

#### Basic Syntax
```java
ObjectInputStream inputStream = new ObjectInputStream(
    new FileInputStream("data.dat")
);
```

#### Required Imports
```java
import java.io.ObjectInputStream;
import java.io.FileInputStream;
import java.io.IOException;
import java.io.FileNotFoundException;
import java.io.EOFException;
```

#### Reading Primitive Data Types

**CRITICAL**: Must read in the SAME ORDER as written!

```java
import java.io.*;

public class ReadBinaryFile {
    public static void main(String[] args) {
        try (ObjectInputStream input = new ObjectInputStream(
                new FileInputStream("data.dat"))) {
            
            // Read in SAME ORDER as written
            int intValue = input.readInt();
            double doubleValue = input.readDouble();
            char charValue = input.readChar();
            boolean boolValue = input.readBoolean();
            long longValue = input.readLong();
            float floatValue = input.readFloat();
            String stringValue = input.readUTF();
            
            System.out.println("Integer: " + intValue);
            System.out.println("Double: " + doubleValue);
            System.out.println("Char: " + charValue);
            System.out.println("Boolean: " + boolValue);
            System.out.println("Long: " + longValue);
            System.out.println("Float: " + floatValue);
            System.out.println("String: " + stringValue);
            
        } catch (FileNotFoundException e) {
            System.out.println("File not found!");
        } catch (IOException e) {
            System.out.println("Error reading file!");
        }
    }
}
```

#### ObjectInputStream Read Methods

| Method | Description | Return Type |
|--------|-------------|-------------|
| `readInt()` | Read integer | int |
| `readDouble()` | Read double | double |
| `readFloat()` | Read float | float |
| `readLong()` | Read long | long |
| `readShort()` | Read short | short |
| `readChar()` | Read character | char |
| `readBoolean()` | Read boolean | boolean |
| `readByte()` | Read byte | byte |
| `readUTF()` | Read string | String |

### Detecting End of File (EOF)

#### Method 1: Using EOFException
```java
import java.io.*;

public class ReadUntilEOF {
    public static void main(String[] args) {
        try (ObjectInputStream input = new ObjectInputStream(
                new FileInputStream("numbers.dat"))) {
            
            try {
                while (true) {
                    int number = input.readInt();
                    System.out.println(number);
                }
            } catch (EOFException e) {
                // End of file reached - this is expected
                System.out.println("End of file reached");
            }
            
        } catch (FileNotFoundException e) {
            System.out.println("File not found!");
        } catch (IOException e) {
            System.out.println("Error reading file!");
        }
    }
}
```

#### Method 2: Using Counter (Better Practice)
```java
// When writing: store count first
try (ObjectOutputStream output = new ObjectOutputStream(
        new FileOutputStream("numbers.dat"))) {
    
    int[] numbers = {10, 20, 30, 40, 50};
    output.writeInt(numbers.length);  // Write count first
    
    for (int num : numbers) {
        output.writeInt(num);
    }
}

// When reading: read count, then loop
try (ObjectInputStream input = new ObjectInputStream(
        new FileInputStream("numbers.dat"))) {
    
    int count = input.readInt();  // Read count first
    
    for (int i = 0; i < count; i++) {
        int number = input.readInt();
        System.out.println(number);
    }
}
```

#### Reading Arrays
```java
try (ObjectInputStream input = new ObjectInputStream(
        new FileInputStream("array.dat"))) {
    
    int length = input.readInt();  // Read array length
    int[] numbers = new int[length];
    
    for (int i = 0; i < length; i++) {
        numbers[i] = input.readInt();
    }
    
    System.out.println("Array: " + Arrays.toString(numbers));
} catch (IOException e) {
    e.printStackTrace();
}
```

#### Reading Objects (Deserialization)
```java
try (ObjectInputStream input = new ObjectInputStream(
        new FileInputStream("student.dat"))) {
    
    Student student = (Student) input.readObject();  // Cast needed
    System.out.println("Student loaded: " + student);
    
} catch (ClassNotFoundException e) {
    System.out.println("Student class not found!");
} catch (IOException e) {
    e.printStackTrace();
}
```

### Complete Example: Random Numbers
```java
import java.io.*;
import java.util.Random;

public class RandomNumbersBinary {
    public static void main(String[] args) {
        // Generate and write random numbers
        writeRandomNumbers();
        
        // Read and analyze
        readAndAnalyze();
    }
    
    public static void writeRandomNumbers() {
        Random rand = new Random();
        int n = rand.nextInt(11) + 20;  // 20-30
        
        try (ObjectOutputStream output = new ObjectOutputStream(
                new FileOutputStream("numbers.dat"))) {
            
            output.writeInt(n);  // Write count
            
            System.out.println("Generating " + n + " random numbers:");
            for (int i = 0; i < n; i++) {
                int num = rand.nextInt(91) + 10;  // 10-100
                output.writeInt(num);
                System.out.print(num + " ");
            }
            System.out.println("\nNumbers written to file.");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static void readAndAnalyze() {
        try (ObjectInputStream input = new ObjectInputStream(
                new FileInputStream("numbers.dat"))) {
            
            int n = input.readInt();
            int max = Integer.MIN_VALUE;
            int min = Integer.MAX_VALUE;
            
            System.out.println("\nReading " + n + " numbers:");
            for (int i = 0; i < n; i++) {
                int num = input.readInt();
                System.out.print(num + " ");
                
                if (num > max) max = num;
                if (num < min) min = num;
            }
            
            System.out.println("\n\nAnalysis:");
            System.out.println("Count: " + n);
            System.out.println("Maximum: " + max);
            System.out.println("Minimum: " + min);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 10. Working with Directories

### Listing Files in a Directory

```java
import java.io.File;

public class ListFiles {
    public static void main(String[] args) {
        File folder = new File("FilesFolder");
        
        // Check if directory exists
        if (!folder.exists()) {
            System.out.println("Directory does not exist!");
            return;
        }
        
        // Check if it's actually a directory
        if (!folder.isDirectory()) {
            System.out.println("Not a directory!");
            return;
        }
        
        // Get list of files
        File[] listOfFiles = folder.listFiles();
        
        if (listOfFiles != null && listOfFiles.length > 0) {
            System.out.println("Files in " + folder.getName() + ":");
            
            for (File file : listOfFiles) {
                if (file.isFile()) {
                    System.out.println("  [FILE] " + file.getName());
                } else if (file.isDirectory()) {
                    System.out.println("  [DIR]  " + file.getName());
                }
            }
        } else {
            System.out.println("Directory is empty!");
        }
    }
}
```

### Filtering Files by Extension
```java
import java.io.File;

public class FilterFiles {
    public static void main(String[] args) {
        File folder = new File(".");
        
        File[] txtFiles = folder.listFiles((dir, name) -> name.endsWith(".txt"));
        
        if (txtFiles != null) {
            System.out.println("Text files found:");
            for (File file : txtFiles) {
                System.out.println("  " + file.getName());
            }
        }
    }
}
```

### Reading All Files in a Directory
```java
import java.io.*;
import java.util.Scanner;

public class ReadAllFiles {
    public static void main(String[] args) {
        File folder = new File("FilesFolder");
        File[] files = folder.listFiles();
        
        if (files != null) {
            for (File file : files) {
                if (file.isFile()) {
                    System.out.println("\n=== " + file.getName() + " ===");
                    readFile(file);
                }
            }
        }
    }
    
    public static void readFile(File file) {
        try (Scanner scanner = new Scanner(file)) {
            while (scanner.hasNextLine()) {
                System.out.println(scanner.nextLine());
            }
        } catch (FileNotFoundException e) {
            System.out.println("Error reading: " + file.getName());
        }
    }
}
```

### Complete Example: Process Multiple Files
```java
import java.io.*;
import java.util.Scanner;

public class ProcessMultipleFiles {
    public static void main(String[] args) {
        File folder = new File("DataFiles");
        
        if (!folder.exists()) {
            folder.mkdir();  // Create directory if doesn't exist
            System.out.println("Created directory: " + folder.getName());
        }
        
        File[] files = folder.listFiles();
        
        if (files != null && files.length > 0) {
            for (File file : files) {
                if (file.isFile()) {
                    String extension = getFileExtension(file);
                    
                    if (extension.equals("txt")) {
                        processTxtFile(file);
                    } else if (extension.equals("csv")) {
                        processCsvFile(file);
                    }
                }
            }
        } else {
            System.out.println("No files found in directory");
        }
    }
    
    public static String getFileExtension(File file) {
        String name = file.getName();
        int lastDot = name.lastIndexOf('.');
        return (lastDot > 0) ? name.substring(lastDot + 1) : "";
    }
    
    public static void processTxtFile(File file) {
        System.out.println("\n[TEXT FILE] " + file.getName());
        try (Scanner scanner = new Scanner(file)) {
            int lineCount = 0;
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                lineCount++;
                System.out.println("  Line " + lineCount + ": " + line);
            }
        } catch (FileNotFoundException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
    
    public static void processCsvFile(File file) {
        System.out.println("\n[CSV FILE] " + file.getName());
        try (Scanner scanner = new Scanner(file)) {
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                String[] fields = line.split(",");
                System.out.println("  Fields: " + String.join(" | ", fields));
            }
        } catch (FileNotFoundException e) {
            System.out.println("Error: " + e.getMessage());
        }
    }
}
```

---

## 11. Best Practices and Modern Approaches

### 1. Always Use Try-With-Resources
```java
// ❌ Old way (manual close)
PrintWriter writer = null;
try {
    writer = new PrintWriter("file.txt");
    writer.println("Hello");
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (writer != null) {
        writer.close();
    }
}

// ✅ Modern way (automatic close)
try (PrintWriter writer = new PrintWriter("file.txt")) {
    writer.println("Hello");
} catch (IOException e) {
    e.printStackTrace();
}
```

### 2. Handle Exceptions Appropriately
```java
// ❌ Don't ignore exceptions
try {
    // code
} catch (IOException e) {
    // Empty catch block
}

// ✅ Handle or log them
try {
    // code
} catch (IOException e) {
    System.err.println("Error: " + e.getMessage());
    e.printStackTrace();
}
```

### 3. Use Specific Exception Types
```java
// ✅ Catch specific exceptions first
try {
    // file operations
} catch (FileNotFoundException e) {
    System.out.println("File not found!");
} catch (IOException e) {
    System.out.println("I/O error occurred!");
}
```

### 4. Check File Existence Before Operations
```java
File file = new File("data.txt");
if (file.exists() && file.canRead()) {
    // Safe to read
} else {
    System.out.println("Cannot read file!");
}
```

### 5. Use BufferedReader for Large Files
```java
// ✅ Buffered reading is more efficient
try (BufferedReader reader = new BufferedReader(
        new FileReader("largefile.txt"))) {
    String line;
    while ((line = reader.readLine()) != null) {
        // process line
    }
}
```

### 6. Close Resources in Correct Order
When manually closing, close in reverse order of opening:
```java
FileInputStream fis = new FileInputStream("file.txt");
BufferedInputStream bis = new BufferedInputStream(fis);

// Close in reverse order
bis.close();  // Close wrapper first
fis.close();  // Then close underlying stream
```

### 7. Use Platform-Independent Path Separators
```java
// ❌ Platform-specific
String path = "folder\\file.txt";  // Windows only

// ✅ Platform-independent
String path = "folder" + File.separator + "file.txt";

// ✅ Even better (Java 7+)
Path path = Paths.get("folder", "file.txt");
```

### 8. Validate File Operations
```java
File file = new File("data.txt");

// Check before creating
if (!file.exists()) {
    if (file.createNewFile()) {
        System.out.println("File created");
    }
}

// Check before deleting
if (file.exists()) {
    if (file.delete()) {
        System.out.println("File deleted");
    }
}
```

---

## 12. Complete Working Examples

### Example 1: Student Grade Manager

```java
import java.io.*;
import java.util.Scanner;
import java.util.ArrayList;

public class StudentGradeManager {
    private static final String FILENAME = "students.txt";
    
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        
        while (true) {
            System.out.println("\n=== Student Grade Manager ===");
            System.out.println("1. Add Student");
            System.out.println("2. View All Students");
            System.out.println("3. Calculate Average");
            System.out.println("4. Exit");
            System.out.print("Choice: ");
            
            int choice = input.nextInt();
            input.nextLine();  // Consume newline
            
            switch (choice) {
                case 1: addStudent(input); break;
                case 2: viewStudents(); break;
                case 3: calculateAverage(); break;
                case 4: 
                    System.out.println("Goodbye!");
                    return;
                default:
                    System.out.println("Invalid choice!");
            }
        }
    }
    
    public static void addStudent(Scanner input) {
        System.out.print("Enter student name: ");
        String name = input.nextLine();
        
        System.out.print("Enter grade: ");
        double grade = input.nextDouble();
        
        try (PrintWriter writer = new PrintWriter(
                new FileOutputStream(FILENAME, true))) {  // Append mode
            writer.println(name + "," + grade);
            System.out.println("Student added successfully!");
        } catch (IOException e) {
            System.out.println("Error saving student: " + e.getMessage());
        }
    }
    
    public static void viewStudents() {
        File file = new File(FILENAME);
        
        if (!file.exists()) {
            System.out.println("No students found!");
            return;
        }
        
        System.out.println("\n=== Student List ===");
        try (Scanner scanner = new Scanner(file)) {
            int count = 0;
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                String[] parts = line.split(",");
                count++;
                System.out.printf("%d. %s - Grade: %s%n", 
                    count, parts[0], parts[1]);
            }
        } catch (FileNotFoundException e) {
            System.out.println("Error reading file!");
        }
    }
    
    public static void calculateAverage() {
        File file = new File(FILENAME);
        
        if (!file.exists()) {
            System.out.println("No students found!");
            return;
        }
        
        try (Scanner scanner = new Scanner(file)) {
            double total = 0;
            int count = 0;
            
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                String[] parts = line.split(",");
                total += Double.parseDouble(parts[1]);
                count++;
            }
            
            if (count > 0) {
                double average = total / count;
                System.out.printf("Class Average: %.2f%n", average);
            }
        } catch (FileNotFoundException e) {
            System.out.println("Error reading file!");
        }
    }
}
```

### Example 2: Binary File Backup System

```java
import java.io.*;

public class BinaryBackupSystem {
    public static void main(String[] args) {
        // Create sample data
        int[] data = {10, 20, 30, 40, 50};
        String originalFile = "original.dat";
        String backupFile = "backup.dat";
        
        // Write original data
        writeData(originalFile, data);
        
        // Create backup
        createBackup(originalFile, backupFile);
        
        // Verify backup
        verifyBackup(backupFile);
    }
    
    public static void writeData(String filename, int[] data) {
        try (ObjectOutputStream output = new ObjectOutputStream(
                new FileOutputStream(filename))) {
            
            output.writeInt(data.length);
            for (int num : data) {
                output.writeInt(num);
            }
            
            System.out.println("Data written to: " + filename);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static void createBackup(String source, String backup) {
        try (FileInputStream fis = new FileInputStream(source);
             FileOutputStream fos = new FileOutputStream(backup)) {
            
            byte[] buffer = new byte[1024];
            int bytesRead;
            
            while ((bytesRead = fis.read(buffer)) != -1) {
                fos.write(buffer, 0, bytesRead);
            }
            
            System.out.println("Backup created: " + backup);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static void verifyBackup(String filename) {
        try (ObjectInputStream input = new ObjectInputStream(
                new FileInputStream(filename))) {
            
            int count = input.readInt();
            System.out.println("\nBackup verification:");
            System.out.println("Count: " + count);
            
            System.out.print("Data: ");
            for (int i = 0; i < count; i++) {
                System.out.print(input.readInt() + " ");
            }
            System.out.println("\n✓ Backup verified!");
            
        } catch (IOException e) {
            System.out.println("✗ Backup verification failed!");
            e.printStackTrace();
        }
    }
}
```

### Example 3: CSV File Processor

```java
import java.io.*;
import java.util.*;

public class CSVProcessor {
    public static void main(String[] args) {
        String inputFile = "sales.csv";
        String outputFile = "summary.txt";
        
        // Create sample CSV file
        createSampleCSV(inputFile);
        
        // Process and summarize
        processCSV(inputFile, outputFile);
    }
    
    public static void createSampleCSV(String filename) {
        try (PrintWriter writer = new PrintWriter(filename)) {
            writer.println("Product,Price,Quantity");
            writer.println("Laptop,2500.00,5");
            writer.println("Mouse,15.50,20");
            writer.println("Keyboard,75.00,15");
            writer.println("Monitor,450.00,8");
            
            System.out.println("Sample CSV created: " + filename);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static void processCSV(String inputFile, String outputFile) {
        try (Scanner scanner = new Scanner(new File(inputFile));
             PrintWriter writer = new PrintWriter(outputFile)) {
            
            // Skip header
            if (scanner.hasNextLine()) {
                scanner.nextLine();
            }
            
            double totalRevenue = 0;
            int totalItems = 0;
            
            writer.println("=== Sales Summary ===\n");
            
            while (scanner.hasNextLine()) {
                String line = scanner.nextLine();
                String[] parts = line.split(",");
                
                String product = parts[0];
                double price = Double.parseDouble(parts[1]);
                int quantity = Integer.parseInt(parts[2]);
                double revenue = price * quantity;
                
                totalRevenue += revenue;
                totalItems += quantity;
                
                writer.printf("%-15s: $%8.2f x %3d = $%10.2f%n",
                    product, price, quantity, revenue);
            }
            
            writer.println("\n" + "=".repeat(45));
            writer.printf("Total Items Sold: %d%n", totalItems);
            writer.printf("Total Revenue: $%.2f%n", totalRevenue);
            
            System.out.println("Summary written to: " + outputFile);
            
        } catch (FileNotFoundException e) {
            System.out.println("File not found!");
        }
    }
}
```

---

## Common Pitfalls and Solutions

### Pitfall 1: Forgetting to Close Files
```java
// ❌ Problem: File not closed
PrintWriter writer = new PrintWriter("file.txt");
writer.println("Data");
// Missing close() - data might not be written!

// ✅ Solution: Use try-with-resources
try (PrintWriter writer = new PrintWriter("file.txt")) {
    writer.println("Data");
}  // Automatically closed
```

### Pitfall 2: Reading in Wrong Order (Binary Files)
```java
// ❌ Problem: Reading in different order than writing
// Write: int, double, string
output.writeInt(42);
output.writeDouble(3.14);
output.writeUTF("Hello");

// Read: WRONG ORDER!
double d = input.readDouble();  // ERROR!
int i = input.readInt();

// ✅ Solution: Read in SAME order
int i = input.readInt();        // Correct
double d = input.readDouble();
String s = input.readUTF();
```

### Pitfall 3: Not Checking File Existence
```java
// ❌ Problem: Assuming file exists
Scanner scanner = new Scanner(new File("data.txt"));  // May crash!

// ✅ Solution: Check first
File file = new File("data.txt");
if (file.exists()) {
    Scanner scanner = new Scanner(file);
    // ...
} else {
    System.out.println("File not found!");
}
```

### Pitfall 4: Mixing nextInt() and nextLine()
```java
// ❌ Problem: nextLine() reads empty string
Scanner scanner = new Scanner(System.in);
int age = scanner.nextInt();
String name = scanner.nextLine();  // Gets empty string!

// ✅ Solution: Consume newline
int age = scanner.nextInt();
scanner.nextLine();  // Consume newline
String name = scanner.nextLine();  // Now works correctly
```

### Pitfall 5: Not Handling Multiple Exceptions
```java
// ❌ Problem: Only catching one exception
try {
    BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
} catch (IOException e) {  // Won't catch FileNotFoundException first
    e.printStackTrace();
}

// ✅ Solution: Catch specific exceptions first
try {
    BufferedReader reader = new BufferedReader(new FileReader("file.txt"));
} catch (FileNotFoundException e) {
    System.out.println("File not found!");
} catch (IOException e) {
    System.out.println("I/O error!");
}
```

---

## Summary Table: When to Use What

| Task | Best Choice | Why |
|------|------------|-----|
| Write text to file | `PrintWriter` | Easy formatting, familiar methods |
| Read text line-by-line | `BufferedReader` | Fast, efficient for large files |
| Read mixed data types | `Scanner` | Parses different types easily |
| Write binary data | `ObjectOutputStream` | Efficient, preserves precision |
| Read binary data | `ObjectInputStream` | Matches ObjectOutputStream |
| Check file properties | `File` class | Get info without reading content |
| List directory files | `File.listFiles()` | Returns array of File objects |
| Small text files | `Scanner` | Simplicity over speed |
| Large text files | `BufferedReader` | Speed and efficiency |
| Storing objects | Serialization | Preserves object state |

---

## Quick Reference: Import Statements

```java
// Text File Writing
import java.io.PrintWriter;
import java.io.FileWriter;
import java.io.FileOutputStream;

// Text File Reading
import java.util.Scanner;
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.FileInputStream;

// Binary File I/O
import java.io.ObjectOutputStream;
import java.io.ObjectInputStream;

// File Operations
import java.io.File;

// Exceptions
import java.io.IOException;
import java.io.FileNotFoundException;
import java.io.EOFException;
```

---

## Practice Exercises with Solutions

### Exercise 1: Temperature Logger
**Task**: Create a program that logs daily temperatures to a file and calculates statistics.

```java
import java.io.*;
import java.util.Scanner;

public class TemperatureLogger {
    private static final String FILENAME = "temperatures.txt";
    
    public static void main(String[] args) {
        Scanner input = new Scanner(System.in);
        
        System.out.println("1. Log Temperature");
        System.out.println("2. View Statistics");
        System.out.print("Choice: ");
        int choice = input.nextInt();
        
        if (choice == 1) {
            logTemperature(input);
        } else {
            viewStatistics();
        }
    }
    
    public static void logTemperature(Scanner input) {
        System.out.print("Enter temperature (°C): ");
        double temp = input.nextDouble();
        
        try (PrintWriter writer = new PrintWriter(
                new FileOutputStream(FILENAME, true))) {
            writer.println(temp);
            System.out.println("Temperature logged!");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static void viewStatistics() {
        try (Scanner scanner = new Scanner(new File(FILENAME))) {
            double sum = 0, min = Double.MAX_VALUE, max = Double.MIN_VALUE;
            int count = 0;
            
            while (scanner.hasNextDouble()) {
                double temp = scanner.nextDouble();
                sum += temp;
                count++;
                if (temp < min) min = temp;
                if (temp > max) max = temp;
            }
            
            if (count > 0) {
                System.out.printf("Average: %.2f°C%n", sum / count);
                System.out.printf("Minimum: %.2f°C%n", min);
                System.out.printf("Maximum: %.2f°C%n", max);
                System.out.printf("Readings: %d%n", count);
            }
        } catch (FileNotFoundException e) {
            System.out.println("No data logged yet!");
        }
    }
}
```

---

This comprehensive guide covers everything your lecture slides mentioned plus much more detail, modern best practices, and complete working examples. The slides were indeed quite basic and lacked proper API introduction - this should give you a much better foundation!