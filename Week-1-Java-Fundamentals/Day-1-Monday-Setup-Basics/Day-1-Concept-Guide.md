# DAY 1: JAVA SETUP & BASICS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Set up Java development environment (JDK + IntelliJ IDEA)
- [ ] Understand Java program structure and execution
- [ ] Master Java data types, variables, and operators
- [ ] Write and run your first Java programs
- [ ] Build a simple Calculator program

**Time Required:** 2.5-3 hours
**Difficulty:** Easy
**Prerequisite:** None (Fresh start!)

---

## 📚 Core Concepts

### Concept 1: Java Program Structure

**What is it?**
Unlike Python's script-based approach where you can write code directly, Java requires all code to be inside classes. Every Java program needs at least one class with a `main` method as the entry point.

**Why does it matter for automation?**
In Selenium automation, you'll create multiple classes (Page Objects, Test Classes, Utility Classes). Understanding class structure from Day 1 is crucial for building organized test frameworks.

**Syntax:**
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, World!");
    }
}
```

**Explanation:**
- `public class HelloWorld` - Defines a class named HelloWorld (must match filename)
- `public static void main(String[] args)` - The entry point method
  - `public` - accessible from anywhere
  - `static` - can be called without creating an object
  - `void` - doesn't return any value
  - `String[] args` - accepts command-line arguments
- `System.out.println()` - prints output to console (like Python's `print()`)
- `;` - every statement must end with a semicolon
- `{}` - curly braces define code blocks

**Real Example:**
```java
public class FirstTest {
    public static void main(String[] args) {
        System.out.println("Starting test execution...");
        System.out.println("Test Status: PASS");
    }
}
```

---

### Concept 2: Variables and Data Types

**What is it?**
Java is **statically typed**, meaning you must declare the variable's type before using it. The type cannot change later (unlike Python's dynamic typing).

**Why does it matter for automation?**
When working with Selenium, you'll declare WebDriver objects, WebElements, test data variables, etc. Type safety helps catch errors during compilation rather than runtime.

**Syntax:**
```java
// Variable declaration and initialization
dataType variableName = value;

// Examples:
int count = 10;
String name = "Selenium";
boolean isTestPassed = true;
double price = 99.99;
```

**Explanation:**
Java has two categories of data types:

**Primitive Types** (store actual values):
| Type | Size | Range | Example |
|------|------|-------|---------|
| `byte` | 1 byte | -128 to 127 | `byte age = 25;` |
| `short` | 2 bytes | -32,768 to 32,767 | `short year = 2024;` |
| `int` | 4 bytes | -2B to 2B | `int count = 1000;` |
| `long` | 8 bytes | Very large numbers | `long population = 8000000000L;` |
| `float` | 4 bytes | Decimal numbers | `float price = 10.5f;` |
| `double` | 8 bytes | Precise decimals | `double pi = 3.14159;` |
| `char` | 2 bytes | Single character | `char grade = 'A';` |
| `boolean` | 1 bit | true/false | `boolean isPassed = true;` |

**Reference Types** (store memory addresses):
- `String` - text values
- Arrays - collections of same type
- Objects - instances of classes

**Common Use Cases in Test Automation:**
1. `int` - for counters, retry counts, wait times
2. `String` - for test data, URLs, usernames, passwords
3. `boolean` - for test assertions, flags, conditions
4. `double` - for performance metrics, response times

---

### Concept 3: Operators

**What is it?**
Operators perform operations on variables and values. Java has arithmetic, comparison, logical, and assignment operators.

**Why does it matter for automation?**
You'll use operators for test assertions, loop conditions, data validation, and calculations in test reports.

**Syntax:**
```java
// Arithmetic Operators
int a = 10, b = 3;
int sum = a + b;        // 13
int difference = a - b; // 7
int product = a * b;    // 30
int quotient = a / b;   // 3 (integer division)
int remainder = a % b;  // 1 (modulus)

// Comparison Operators (return boolean)
boolean isEqual = (a == b);     // false
boolean isNotEqual = (a != b);  // true
boolean isGreater = (a > b);    // true
boolean isLessEqual = (a <= b); // false

// Logical Operators
boolean x = true, y = false;
boolean and = x && y;  // false (both must be true)
boolean or = x || y;   // true (at least one true)
boolean not = !x;      // false (negation)

// Assignment Operators
int count = 5;
count += 3;  // count = count + 3; now count is 8
count -= 2;  // count = count - 2; now count is 6
count *= 2;  // count = count * 2; now count is 12
count /= 4;  // count = count / 4; now count is 3

// Increment/Decrement
int num = 10;
num++;  // post-increment: use then increase (num becomes 11)
++num;  // pre-increment: increase then use (num becomes 12)
num--;  // post-decrement: use then decrease (num becomes 11)
--num;  // pre-decrement: decrease then use (num becomes 10)
```

**Common Use Cases in Test Automation:**
1. Arithmetic: Calculating total test cases, pass/fail percentages
2. Comparison: Assertions in test validation
3. Logical: Combining multiple test conditions
4. Increment: Loop counters, retry attempts

---

### Concept 4: Constants and Final Keyword

**What is it?**
Constants are variables whose values cannot be changed after initialization. Use the `final` keyword to declare constants.

**Syntax:**
```java
final double PI = 3.14159;
final String BASE_URL = "https://www.example.com";
final int MAX_RETRY_COUNT = 3;

// Convention: Constants use UPPER_CASE_WITH_UNDERSCORES
```

**Common Use Cases in Test Automation:**
```java
public class TestConfig {
    public static final String BASE_URL = "https://www.saucedemo.com";
    public static final int IMPLICIT_WAIT = 10;
    public static final int EXPLICIT_WAIT = 20;
    public static final String BROWSER = "chrome";
}
```

---

### Concept 5: Type Casting

**What is it?**
Converting one data type to another. Can be automatic (implicit) or manual (explicit).

**Syntax:**
```java
// Implicit Casting (automatic - smaller to larger type)
int myInt = 100;
double myDouble = myInt;  // int -> double (automatic)

// Explicit Casting (manual - larger to smaller type)
double price = 99.99;
int roundedPrice = (int) price;  // 99 (decimal part lost)

// String to int conversion
String numberStr = "123";
int number = Integer.parseInt(numberStr);

// int to String conversion
int count = 456;
String countStr = String.valueOf(count);
// or
String countStr2 = Integer.toString(count);
```

**Common Use Cases in Test Automation:**
```java
// Converting string test data to numbers
String maxWaitTime = "30";
int wait = Integer.parseInt(maxWaitTime);

// Converting numbers to strings for reporting
int testCount = 150;
System.out.println("Total tests: " + testCount);  // Auto-converts to string
```

---

## 🐍 Python vs Java Comparison

### Variables in Python (What You Know)
```python
# Python - Dynamic typing
name = "Selenium"      # type inferred automatically
count = 10             # can change type later
count = "ten"          # valid in Python
price = 99.99          # float
is_passed = True       # boolean (capitalized)

# No semicolons needed
# Indentation defines blocks
```

### Variables in Java (What You're Learning)
```java
// Java - Static typing
String name = "Selenium";  // must declare type
int count = 10;            // type cannot change
// count = "ten";          // ERROR! Type mismatch
double price = 99.99;      // double
boolean isPassed = true;   // boolean (lowercase)

// Semicolons required
// Braces {} define blocks
```

**Key Differences:**
| Aspect | Python | Java |
|--------|--------|------|
| **Type Declaration** | `x = 5` | `int x = 5;` |
| **Type Changing** | Allowed | Not allowed |
| **Syntax End** | No semicolon | Semicolon required |
| **Boolean Values** | `True/False` | `true/false` |
| **Statement Blocks** | Indentation | Curly braces `{}` |
| **Print Statement** | `print("text")` | `System.out.println("text");` |

**Mental Model Shift:**
In Python, you tell the computer *what to do*. In Java, you tell the computer *what type everything is*, then what to do. This strictness prevents many runtime errors and makes code more predictable in large automation frameworks.

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Forgetting Semicolons
❌ **Wrong:**
```java
int count = 10
System.out.println(count)
```

✅ **Correct:**
```java
int count = 10;
System.out.println(count);
```

**Why it matters:** Java compiler requires semicolons to know where statements end. This will be your most common error in Week 1!

---

### Mistake 2: Class Name Doesn't Match File Name
❌ **Wrong:**
```java
// File: MyProgram.java
public class TestClass {  // Class name doesn't match file name
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

✅ **Correct:**
```java
// File: MyProgram.java
public class MyProgram {  // Class name matches file name
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}
```

**Why it matters:** Java enforces one public class per file, and the class name must match the filename exactly (case-sensitive).

---

### Mistake 3: Using Undefined Variables
❌ **Wrong:**
```java
public class Test {
    public static void main(String[] args) {
        System.out.println(message);  // message not declared
    }
}
```

✅ **Correct:**
```java
public class Test {
    public static void main(String[] args) {
        String message = "Hello";
        System.out.println(message);
    }
}
```

**Why it matters:** Java variables must be declared before use. The compiler catches this, preventing runtime errors.

---

### Mistake 4: Integer Division Confusion
❌ **Wrong:**
```java
int a = 10;
int b = 3;
double result = a / b;  // result is 3.0, not 3.333...
```

✅ **Correct:**
```java
int a = 10;
int b = 3;
double result = (double) a / b;  // result is 3.333...
// OR
double result = a / (double) b;  // result is 3.333...
```

**Why it matters:** When dividing two integers, Java performs integer division (truncates decimal). Cast at least one operand to double for decimal results.

---

### Mistake 5: Comparing Strings with ==
❌ **Wrong:**
```java
String browser1 = "chrome";
String browser2 = "chrome";
if (browser1 == browser2) {  // Compares memory addresses, not content!
    System.out.println("Same browser");
}
```

✅ **Correct:**
```java
String browser1 = "chrome";
String browser2 = "chrome";
if (browser1.equals(browser2)) {  // Compares actual content
    System.out.println("Same browser");
}
```

**Why it matters:** `==` compares object references (memory addresses), not content. Use `.equals()` for string comparison. This is critical for test assertions!

---

## 💡 Pro Tips for Automation Engineers

**Tip 1:** Use meaningful variable names from Day 1
Instead of `String s = "chrome";`, use `String browserName = "chrome";`
Your future self (and team) will thank you when reading test code.

**Tip 2:** Follow Java naming conventions:
- Classes: `PascalCase` (LoginPage, TestRunner)
- Variables/Methods: `camelCase` (userName, clickLoginButton)
- Constants: `UPPER_SNAKE_CASE` (MAX_WAIT_TIME, BASE_URL)

**Tip 3:** IntelliJ IDEA is your best friend
- Type `sout` and press Tab → auto-completes `System.out.println()`
- Type `main` and press Tab → auto-creates main method
- Ctrl/Cmd + Space → auto-complete suggestions
- Alt + Enter → quick fixes for errors

**Tip 4:** Compile errors are good!
In Python, you only discover some errors at runtime. Java catches type errors, syntax errors, and many logical errors during compilation. This saves debugging time in test automation.

---

## 🔍 Deep Dive: Why Java for Test Automation?

**What experienced developers know:**
Java's "verbose" nature (more code to write) is actually an advantage for large-scale test automation:

1. **Type Safety**: Catches errors before tests run
2. **IDE Support**: IntelliJ can auto-complete and suggest fixes because types are explicit
3. **Refactoring**: Changing code is safer because the compiler validates all references
4. **Team Collaboration**: Explicit types make code self-documenting

**Example:**
```java
// In Java, this is clear and self-documenting:
public WebDriver initializeDriver(String browserName, boolean headless) {
    // Implementation
}

// vs Python where types are ambiguous:
def initialize_driver(browser_name, headless):
    # What types are these? Need to check docs or code
```

**When to use this:** From Day 1! Embrace Java's verbosity - it pays off in maintainable test frameworks.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **JDK** | Java Development Kit - tools to develop Java applications | Required for compiling Java code |
| **JRE** | Java Runtime Environment - runs Java applications | End users need this to run your program |
| **Compilation** | Converting .java files to .class (bytecode) files | `javac HelloWorld.java` |
| **Bytecode** | Intermediate code that JVM executes | .class files contain bytecode |
| **JVM** | Java Virtual Machine - executes bytecode | Makes Java platform-independent |
| **Class** | Blueprint for creating objects | `public class Calculator {}` |
| **Method** | Function inside a class | `public static void main() {}` |
| **Variable** | Named storage location for data | `int count = 10;` |
| **Data Type** | Defines what kind of data a variable can hold | `int`, `String`, `boolean` |
| **Operator** | Performs operations on variables | `+`, `-`, `*`, `/`, `==`, `!=` |

---

## 🎓 Interview Prep

**Common Interview Questions on Java Basics:**

**Q1:** What is the difference between JDK, JRE, and JVM?
**A:**
- **JVM** (Java Virtual Machine): Executes Java bytecode. Platform-specific.
- **JRE** (Java Runtime Environment): JVM + libraries needed to run Java applications. For end users.
- **JDK** (Java Development Kit): JRE + development tools (compiler, debugger). For developers.

*Example: "As an SDET, I use JDK to develop and compile my test automation framework. The CI/CD server needs at least JRE to execute the compiled tests."*

---

**Q2:** Explain primitive vs reference data types in Java.
**A:**
- **Primitive types** (`int`, `boolean`, `double`, etc.) store actual values in memory
- **Reference types** (`String`, objects, arrays) store memory addresses pointing to objects

```java
int num = 10;          // stores value 10 directly
String name = "Java";  // stores memory address of "Java" object

int a = num;           // copies value 10 to a
String b = name;       // copies memory address to b (both point to same object)
```

*Example: "In Selenium, WebDriver is a reference type. When I assign `WebDriver driver2 = driver1;`, both variables point to the same browser instance."*

---

**Q3:** Why is Java called "platform-independent"?
**A:**
Java source code (.java) is compiled to bytecode (.class), not machine code. The JVM interprets this bytecode for the specific platform (Windows, Mac, Linux). Same bytecode runs on any platform with a JVM.

*Example: "I can develop my Selenium tests on Mac, and the same .class files run on our Linux-based Jenkins server without recompilation."*

---

**Q4:** What is the purpose of the `public static void main(String[] args)` method?
**A:**
It's the entry point of any Java application:
- `public`: Accessible from anywhere (JVM can call it)
- `static`: Can be called without creating a class instance
- `void`: Doesn't return any value
- `String[] args`: Accepts command-line arguments

```java
// Run with: java TestRunner smoke regression
public class TestRunner {
    public static void main(String[] args) {
        System.out.println("Running: " + args[0]);  // "smoke"
        System.out.println("And: " + args[1]);      // "regression"
    }
}
```

---

**Q5:** How do you take user input in Java?
**A:**
```java
import java.util.Scanner;

public class InputExample {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter username: ");
        String username = scanner.nextLine();

        System.out.print("Enter age: ");
        int age = scanner.nextInt();

        System.out.println("Hello " + username + ", age " + age);
        scanner.close();
    }
}
```

*Example: "While automated tests don't usually need user input, I use Scanner for creating test data generators and utility scripts."*

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What are the three mandatory parts of a Java program structure?**

2. **What's the difference between `int` and `Integer` in Java?**

3. **If you write `int result = 7 / 2;`, what value will `result` have and why?**

4. **Why do we use the `final` keyword in test automation frameworks?**

5. **Predict the output:**
   ```java
   int x = 5;
   System.out.println(x++);
   System.out.println(++x);
   ```

**Answers in Practice Exercises file.**

---

## 🚀 Environment Setup Guide

### Step 1: Install Java JDK

**Windows:**
1. Download JDK 17 from [Oracle](https://www.oracle.com/java/technologies/downloads/) or [Adoptium](https://adoptium.net/)
2. Run installer, follow prompts
3. Verify installation:
   ```bash
   java -version
   javac -version
   ```

**Mac:**
```bash
brew install openjdk@17
java -version
```

**Linux (Ubuntu):**
```bash
sudo apt update
sudo apt install openjdk-17-jdk
java -version
```

---

### Step 2: Install IntelliJ IDEA Community Edition

1. Download from [JetBrains](https://www.jetbrains.com/idea/download/)
2. Install and launch
3. Create new project:
   - New Project → Java
   - Choose JDK 17
   - Project name: "JavaSDETLearning"
   - Create

---

### Step 3: Write Your First Program

1. Right-click `src` → New → Java Class → "HelloWorld"
2. Type this code:
```java
public class HelloWorld {
    public static void main(String[] args) {
        System.out.println("Hello, Java SDET!");
        System.out.println("Day 1 of mastery begins now.");
    }
}
```
3. Right-click inside the file → Run 'HelloWorld.main()'
4. See output in console

**Congratulations!** You've run your first Java program.

---

## 📝 Today's Mantra

> "Java is verbose, but verbosity brings clarity. Every type declaration is a promise, every semicolon is precision. Embrace the structure - it's building your SDET foundation."

---

## 🎯 Ready for Practice?

Now move to **Day-1-Practice-Exercises.md** to apply these concepts with hands-on coding exercises.

**Time spent on concepts:** ____ minutes
**Ready to code:** Yes / Review once more

---
