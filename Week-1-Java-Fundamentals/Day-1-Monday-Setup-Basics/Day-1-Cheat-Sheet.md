# DAY 1 CHEAT SHEET: JAVA SETUP & BASICS

## ⚡ Quick Syntax Reference

### Program Structure
```java
public class ClassName {
    public static void main(String[] args) {
        // Your code here
    }
}
```

### Variable Declaration
```java
// Syntax: dataType variableName = value;
int count = 10;
double price = 99.99;
String name = "Selenium";
boolean isValid = true;
```

### Printing Output
```java
System.out.println("Hello");       // With newline
System.out.print("Hello");         // Without newline
System.out.println("Value: " + x); // With variable
```

### Arithmetic Operators
```java
int sum = a + b;        // Addition
int diff = a - b;       // Subtraction
int product = a * b;    // Multiplication
double quotient = a / b;  // Division
int remainder = a % b;  // Modulus
```

### Comparison Operators
```java
boolean isEqual = (a == b);     // Equal to
boolean notEqual = (a != b);    // Not equal
boolean greater = (a > b);      // Greater than
boolean less = (a < b);         // Less than
boolean greaterEq = (a >= b);   // Greater or equal
boolean lessEq = (a <= b);      // Less or equal
```

### Logical Operators
```java
boolean and = (x && y);  // Both true
boolean or = (x || y);   // At least one true
boolean not = !x;        // Opposite value
```

### Type Casting
```java
// Implicit (automatic)
int i = 100;
double d = i;  // int → double

// Explicit (manual)
double d = 99.99;
int i = (int) d;  // 99 (decimal lost)

// String conversions
int num = Integer.parseInt("123");
String str = String.valueOf(456);
```

### Constants
```java
final double PI = 3.14159;
final String BASE_URL = "https://example.com";
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Syntax | Python Equivalent |
|---------|-------------|-------------------|
| **Print** | `System.out.println("text");` | `print("text")` |
| **Variable** | `int x = 5;` | `x = 5` |
| **String** | `String name = "Java";` | `name = "Java"` |
| **Boolean** | `boolean flag = true;` | `flag = True` |
| **Decimal** | `double price = 9.99;` | `price = 9.99` |
| **Constant** | `final int MAX = 100;` | `MAX = 100` (by convention) |
| **String Compare** | `str1.equals(str2)` | `str1 == str2` |
| **And** | `&&` | `and` |
| **Or** | `\|\|` | `or` |
| **Not** | `!` | `not` |
| **Comment** | `// comment` or `/* comment */` | `# comment` |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Missing semicolon** | `int x = 5` | `int x = 5;` |
| **Missing type** | `count = 10;` | `int count = 10;` |
| **String comparison** | `if (str1 == str2)` | `if (str1.equals(str2))` |
| **Integer division** | `int r = 7 / 2;` gives `3` | `double r = 7.0 / 2;` gives `3.5` |
| **Boolean values** | `boolean b = True;` | `boolean b = true;` |
| **Class name mismatch** | File: `Test.java` Class: `Demo` | Both must match: `Test` |

---

## 📊 Data Types Quick Reference

### Primitive Types

| Type | Size | Range | Default | Example |
|------|------|-------|---------|---------|
| `byte` | 1 byte | -128 to 127 | 0 | `byte age = 25;` |
| `short` | 2 bytes | -32K to 32K | 0 | `short year = 2024;` |
| `int` | 4 bytes | -2B to 2B | 0 | `int count = 1000;` |
| `long` | 8 bytes | Very large | 0L | `long distance = 9999999L;` |
| `float` | 4 bytes | Decimal | 0.0f | `float price = 10.5f;` |
| `double` | 8 bytes | Decimal (precise) | 0.0 | `double pi = 3.14159;` |
| `char` | 2 bytes | Single character | '\u0000' | `char grade = 'A';` |
| `boolean` | 1 bit | true/false | false | `boolean pass = true;` |

### Reference Types
```java
String text = "Hello";           // Text
int[] numbers = {1, 2, 3};       // Array
ArrayList<String> list = new ArrayList<>();  // List (Week 1 Day 7)
```

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ Java program structure (class + main method)
- ✅ Variables must have declared types
- ✅ Semicolons are mandatory
- ✅ 8 primitive types + reference types (String, etc.)
- ✅ Arithmetic, comparison, and logical operators
- ✅ Type casting (implicit and explicit)
- ✅ Constants with `final` keyword
- ✅ `.equals()` for string comparison, not `==`

**Critical for Automation:**
- 🎯 Type safety prevents runtime errors
- 🎯 Constants for config values (URLs, timeouts)
- 🎯 Proper division for test metrics (use `.0`)
- 🎯 String comparison with `.equals()` for assertions

---

## 🔢 Operator Precedence (Order of Operations)

From highest to lowest priority:

| Priority | Operators | Example |
|----------|-----------|---------|
| 1 | `()` | `(a + b) * c` - parentheses first |
| 2 | `++`, `--` | `x++`, `--y` - increment/decrement |
| 3 | `*`, `/`, `%` | `a * b / c` - multiply/divide/modulus |
| 4 | `+`, `-` | `a + b - c` - addition/subtraction |
| 5 | `<`, `>`, `<=`, `>=` | `a < b` - comparison |
| 6 | `==`, `!=` | `a == b` - equality check |
| 7 | `&&` | `a && b` - logical AND |
| 8 | `\|\|` | `a \|\| b` - logical OR |
| 9 | `=`, `+=`, `-=`, etc. | `a = b` - assignment |

**Example:**
```java
int result = 5 + 3 * 2;     // result = 11 (not 16!)
                            // Multiplication (*) happens before addition (+)

int result = (5 + 3) * 2;   // result = 16
                            // Parentheses force addition first
```

---

## 🎤 Interview Phrases

**When asked about Java basics, say:**

**On Java Structure:**
*"Every Java program requires a class definition and a main method as the entry point. The main method signature must be `public static void main(String[] args)` where `public` makes it accessible, `static` allows it to run without instantiation, `void` means no return value, and `String[] args` accepts command-line arguments."*

**On Type System:**
*"Java is statically typed, meaning variables must have declared types that cannot change. This provides compile-time type checking, which catches errors before the code runs. In contrast to dynamically typed languages like Python, Java's type system improves code reliability and IDE support - critical for large-scale test automation frameworks."*

**On Data Types:**
*"Java has eight primitive types like int, double, and boolean that store actual values, and reference types like String and objects that store memory addresses. For test automation, I commonly use int for counters and timeouts, double for performance metrics, boolean for assertions, and String for test data."*

**On Operators:**
*"When calculating test metrics, I'm careful to avoid integer division. For example, to calculate pass rate, I use `(passed * 100.0) / total` where the `.0` ensures double division giving accurate decimal results rather than truncated integers."*

**On String Comparison:**
*"A critical distinction in Java is that `==` compares object references while `.equals()` compares content. For test assertions comparing text from web elements or API responses, I always use `.equals()` or `.equalsIgnoreCase()` to ensure accurate content comparison."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **Java is strict, but that's its strength. Every type declaration, every semicolon, and every explicit cast prevents bugs before they happen. Embrace the verbosity - it builds bulletproof automation frameworks.**

---

## 🎯 Syntax Patterns for Test Automation

### Pattern 1: Test Configuration Constants
```java
public class TestConfig {
    public static final String BASE_URL = "https://www.saucedemo.com";
    public static final String BROWSER = "chrome";
    public static final int IMPLICIT_WAIT = 10;
    public static final int EXPLICIT_WAIT = 20;
    public static final int PAGE_LOAD_TIMEOUT = 30;
}
```

### Pattern 2: Test Metrics Calculation
```java
int totalTests = 100;
int passedTests = 85;
int failedTests = 15;

// Calculate pass rate (note the .0 for decimal result)
double passRate = (passedTests * 100.0) / totalTests;

// Format for report
String report = String.format("Pass Rate: %.2f%% (%d/%d)",
                              passRate, passedTests, totalTests);
```

### Pattern 3: Input Validation
```java
String username = "testuser";
String password = "password123";

// Validate before using
if (username != null && !username.isEmpty()) {
    // Safe to proceed
    driver.findElement(By.id("username")).sendKeys(username);
} else {
    System.out.println("Error: Username cannot be empty");
}
```

### Pattern 4: Execution Time Tracking
```java
long startTime = System.currentTimeMillis();

// Execute test steps
// ...

long endTime = System.currentTimeMillis();
long executionTime = endTime - startTime;

System.out.println("Test executed in " + executionTime + " milliseconds");
```

---

## 🔮 Tomorrow's Preview

**Day 2 Topic:** Control Flow (if-else, switch, loops)

**What to review tonight:**
- Boolean operators (`&&`, `||`, `!`)
- Comparison operators (`==`, `!=`, `<`, `>`)
- How to evaluate conditions

**What to think about:**
*"How would I add logic to my calculator to only accept positive numbers? How would I repeat calculations without copying code?"*

**Connection to Day 1:**
- Variables store data → Control flow decides what to do with it
- Operators create conditions → Control flow evaluates them
- Calculator performs once → Loops can repeat it

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [x] Learned Java program structure
- [x] Completed 6 practice exercises
- [x] Built SimpleCalculator project
- [x] ~2.5 hours of Java practice

**Cumulative Progress:**
- Days completed: 1/7
- Java concepts mastered: 8
- Lines of code written: ~50-100

**Momentum Statement:**
*"Day 1 complete! You've learned in one day what takes some people a week. Java syntax is now in your muscle memory. Tomorrow you'll add intelligence to your code with decision-making and loops. You're 14% of the way to Java SDET mastery. Keep going!"*

---

## 📝 Quick Reference Card (Print This!)

```
╔═══════════════════════════════════════════════╗
║         JAVA BASICS - DAY 1 REFERENCE         ║
╠═══════════════════════════════════════════════╣
║ STRUCTURE:                                    ║
║ public class Name {                           ║
║     public static void main(String[] args) {  ║
║         // code                               ║
║     }                                         ║
║ }                                             ║
╠═══════════════════════════════════════════════╣
║ VARIABLES:                                    ║
║ int x = 5;              // whole number       ║
║ double y = 3.14;        // decimal            ║
║ String s = "text";      // text               ║
║ boolean b = true;       // true/false         ║
║ final int MAX = 100;    // constant           ║
╠═══════════════════════════════════════════════╣
║ OPERATORS:                                    ║
║ + - * / %               // arithmetic         ║
║ == != < > <= >=         // comparison         ║
║ && || !                 // logical            ║
║ = += -= *= /=           // assignment         ║
╠═══════════════════════════════════════════════╣
║ CRITICAL RULES:                               ║
║ • All statements end with ;                   ║
║ • Variables need types: int x = 5;            ║
║ • Class name = File name                      ║
║ • Strings: use .equals() not ==               ║
║ • Decimal division: use 7.0 / 2 not 7 / 2     ║
╠═══════════════════════════════════════════════╣
║ OUTPUT:                                       ║
║ System.out.println("Hello");                  ║
║ System.out.println("Value: " + variable);     ║
╠═══════════════════════════════════════════════╣
║ TYPE CASTING:                                 ║
║ double d = (double) intVar;  // int → double  ║
║ int i = (int) doubleVar;     // double → int  ║
║ int n = Integer.parseInt("123");  // parse    ║
╚═══════════════════════════════════════════════╝
```

---

## 🚀 You're Ready for Day 2!

**Prerequisites Checklist:**
- [x] Java JDK installed and working
- [x] IntelliJ IDEA set up
- [x] Understand variable declaration
- [x] Know how to use operators
- [x] Can compile and run Java programs
- [x] Completed SimpleCalculator project

**Confidence Check:**
- Java syntax: ___/10
- Data types: ___/10
- Operators: ___/10
- Overall readiness: ___/10

**If any rating is below 6:** Review the Concept Guide and practice exercises again before moving on.

**If all ratings are 6+:** You're ready! Let's learn control flow tomorrow! 🎉

---

## 💾 Save This Cheat Sheet

**Ways to use this:**
1. **Print it** - Keep it next to your keyboard
2. **Bookmark it** - Quick reference while coding
3. **Review it** - Before starting Day 2
4. **Test yourself** - Cover the right side, try to recall syntax

**Pro Tip:** By Day 7, try to code without looking at cheat sheets. That's when you know it's in your muscle memory!

---

**END OF DAY 1** ✅

Tomorrow: Day 2 - Control Flow (if-else, loops, Number Guessing Game)

---
