# DAY 7: ADVANCED CONCEPTS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master exception handling (try-catch-finally, throw, throws)
- [ ] Create custom exceptions for robust error handling
- [ ] Understand Collections Framework (List, Set, Map)
- [ ] Work with ArrayList, HashSet, and HashMap
- [ ] Implement Generics for type-safe code
- [ ] Build a Student Management System with CRUD operations

**Time Required:** 2.5-3 hours
**Difficulty:** Medium-Advanced
**Prerequisite:** Days 1-6 (OOP concepts)

---

## 📚 Core Concepts

### Concept 1: Exception Handling - try-catch-finally

**What is it?**
Exception handling is Java's mechanism for dealing with runtime errors gracefully. Instead of crashing your program, you can catch errors and handle them appropriately. Think of it as a safety net for your code.

**Why does it matter for automation?**
In Selenium, exceptions are everywhere: element not found, timeout exceptions, stale element references. Proper exception handling makes your tests resilient and provides meaningful error messages instead of cryptic stack traces.

**Syntax:**
```java
try {
    // Code that might throw an exception
    int result = 10 / 0; // Will throw ArithmeticException
} catch (ArithmeticException e) {
    // Handle the exception
    System.out.println("Cannot divide by zero: " + e.getMessage());
} finally {
    // Always executes (cleanup code)
    System.out.println("Execution completed");
}
```

**Explanation:**
- `try` block - Contains code that might throw an exception
- `catch` block - Handles specific exception types
- `finally` block - Always executes (optional), used for cleanup
- `e.getMessage()` - Gets the exception message
- `e.printStackTrace()` - Prints full error stack trace

**Real Example:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class SeleniumExceptionExample {
    public static void main(String[] args) {
        WebDriver driver = null;
        try {
            driver = new ChromeDriver();
            driver.get("https://www.example.com");
            // Test automation code here
            System.out.println("Test executed successfully");
        } catch (Exception e) {
            System.out.println("Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // Always close the browser
            if (driver != null) {
                driver.quit();
                System.out.println("Browser closed");
            }
        }
    }
}
```

**Multiple Catch Blocks:**
```java
try {
    String text = null;
    System.out.println(text.length()); // NullPointerException
    int[] arr = new int[3];
    arr[5] = 10; // ArrayIndexOutOfBoundsException
} catch (NullPointerException e) {
    System.out.println("Null value encountered: " + e.getMessage());
} catch (ArrayIndexOutOfBoundsException e) {
    System.out.println("Array index error: " + e.getMessage());
} catch (Exception e) {
    // Generic catch for any other exception
    System.out.println("Unexpected error: " + e.getMessage());
}
```

---

### Concept 2: throw and throws Keywords

**What is it?**
- `throw` - Explicitly throw an exception from your code
- `throws` - Declare that a method might throw exceptions (caller must handle it)

**Why does it matter for automation?**
You'll create validation methods that throw exceptions when test data is invalid or when expected conditions aren't met. This makes test failures explicit and traceable.

**Syntax:**
```java
// Using 'throw' to throw an exception
public void validateAge(int age) {
    if (age < 0 || age > 120) {
        throw new IllegalArgumentException("Age must be between 0 and 120");
    }
    System.out.println("Valid age: " + age);
}

// Using 'throws' to declare exceptions
public void readFile(String filename) throws IOException {
    FileReader file = new FileReader(filename);
    // File reading code
}
```

**Real Example:**
```java
public class TestDataValidator {

    // Method that throws exception
    public static void validateUsername(String username) {
        if (username == null || username.isEmpty()) {
            throw new IllegalArgumentException("Username cannot be empty");
        }
        if (username.length() < 3) {
            throw new IllegalArgumentException("Username must be at least 3 characters");
        }
        System.out.println("Valid username: " + username);
    }

    public static void main(String[] args) {
        try {
            validateUsername("ab"); // Will throw exception
        } catch (IllegalArgumentException e) {
            System.out.println("Validation failed: " + e.getMessage());
        }
    }
}
```

**Common Use Cases in Test Automation:**
1. Validating test data before executing tests
2. Throwing custom exceptions when expected elements aren't found
3. Enforcing preconditions for test execution

---

### Concept 3: Custom Exceptions

**What is it?**
You can create your own exception classes by extending `Exception` (checked) or `RuntimeException` (unchecked). This makes your code more expressive and error messages more meaningful.

**Why does it matter for automation?**
Custom exceptions help distinguish between different types of test failures: configuration errors, test data issues, environment problems, etc.

**Syntax:**
```java
// Custom Exception Class
public class InvalidTestDataException extends Exception {
    public InvalidTestDataException(String message) {
        super(message);
    }
}

// Custom Runtime Exception
public class ElementNotFoundException extends RuntimeException {
    private String elementName;

    public ElementNotFoundException(String elementName, String message) {
        super(message);
        this.elementName = elementName;
    }

    public String getElementName() {
        return elementName;
    }
}
```

**Real Example:**
```java
public class LoginTest {

    public static void login(String username, String password)
            throws InvalidTestDataException {
        // Validate credentials
        if (username == null || username.isEmpty()) {
            throw new InvalidTestDataException("Username is required");
        }
        if (password == null || password.length() < 6) {
            throw new InvalidTestDataException("Password must be at least 6 characters");
        }

        System.out.println("Login successful for user: " + username);
    }

    public static void main(String[] args) {
        try {
            login("testuser", "12345"); // Will fail - password too short
        } catch (InvalidTestDataException e) {
            System.out.println("Test setup failed: " + e.getMessage());
        }
    }
}
```

---

### Concept 4: Collections Framework - Overview

**What is it?**
Collections Framework is a unified architecture for storing and manipulating groups of objects. It provides interfaces (List, Set, Map) and implementations (ArrayList, HashSet, HashMap).

**Why does it matter for automation?**
In test automation, you'll frequently work with collections:
- List of test data
- Set of unique test IDs
- Map of configuration properties
- Collection of WebElements from Selenium

**Key Interfaces:**

| Interface | Ordered | Duplicates | Key-Value | Example Implementation |
|-----------|---------|------------|-----------|----------------------|
| `List` | Yes | Yes | No | ArrayList, LinkedList |
| `Set` | No* | No | No | HashSet, TreeSet |
| `Map` | No** | No (keys) | Yes | HashMap, TreeMap |

*TreeSet is ordered
**TreeMap is ordered by keys

**Common Use Cases in Test Automation:**
1. `List` - Store test data, WebElements, test results
2. `Set` - Track unique test IDs, remove duplicate entries
3. `Map` - Configuration properties, test data mappings

---

### Concept 5: ArrayList - Dynamic Arrays

**What is it?**
ArrayList is a resizable array implementation. Unlike regular arrays with fixed size, ArrayList can grow and shrink dynamically.

**Why does it matter for automation?**
When working with Selenium, you often don't know how many elements you'll find on a page. ArrayList handles dynamic collections perfectly.

**Syntax:**
```java
import java.util.ArrayList;

// Create ArrayList
ArrayList<String> names = new ArrayList<>();

// Add elements
names.add("John");
names.add("Jane");
names.add("Bob");

// Access elements
String first = names.get(0); // "John"

// Update element
names.set(1, "Janet"); // Replace "Jane" with "Janet"

// Remove element
names.remove(2); // Remove "Bob"
names.remove("John"); // Remove by value

// Size
int size = names.size(); // 1

// Check if exists
boolean hasJane = names.contains("Janet"); // true

// Clear all
names.clear();
```

**Real Example:**
```java
import java.util.ArrayList;

public class TestDataManager {
    public static void main(String[] args) {
        // Store test usernames
        ArrayList<String> testUsers = new ArrayList<>();

        // Add test data
        testUsers.add("user1@test.com");
        testUsers.add("user2@test.com");
        testUsers.add("admin@test.com");

        System.out.println("Total test users: " + testUsers.size());

        // Iterate through test data
        for (String user : testUsers) {
            System.out.println("Testing with user: " + user);
        }

        // Remove admin user
        testUsers.remove("admin@test.com");

        // Check if user exists
        if (testUsers.contains("user1@test.com")) {
            System.out.println("User1 found in test data");
        }
    }
}
```

**Common ArrayList Methods:**
```java
ArrayList<Integer> numbers = new ArrayList<>();

// Adding
numbers.add(10);              // Add at end
numbers.add(0, 5);            // Add at index 0
numbers.addAll(otherList);    // Add all from another list

// Accessing
numbers.get(0);               // Get element at index
numbers.indexOf(10);          // Get index of element
numbers.isEmpty();            // Check if empty

// Removing
numbers.remove(0);            // Remove by index
numbers.remove(Integer.valueOf(10)); // Remove by value
numbers.clear();              // Remove all
```

---

### Concept 6: HashSet - Unique Elements

**What is it?**
HashSet stores unique elements only. It doesn't maintain insertion order and doesn't allow duplicates.

**Why does it matter for automation?**
Perfect for tracking unique test IDs, removing duplicate test data, or checking if a value has been processed.

**Syntax:**
```java
import java.util.HashSet;

// Create HashSet
HashSet<String> testIds = new HashSet<>();

// Add elements
testIds.add("TEST-001");
testIds.add("TEST-002");
testIds.add("TEST-001"); // Duplicate - won't be added

// Size
System.out.println(testIds.size()); // 2 (not 3)

// Check if exists
boolean hasTest = testIds.contains("TEST-001"); // true

// Remove
testIds.remove("TEST-002");

// Iterate
for (String id : testIds) {
    System.out.println(id);
}
```

**Real Example:**
```java
import java.util.HashSet;

public class DuplicateChecker {
    public static void main(String[] args) {
        HashSet<String> processedOrders = new HashSet<>();

        String[] orders = {"ORD-001", "ORD-002", "ORD-001", "ORD-003", "ORD-002"};

        System.out.println("Processing orders...");
        for (String order : orders) {
            if (processedOrders.add(order)) {
                System.out.println("Processing new order: " + order);
            } else {
                System.out.println("Duplicate order detected: " + order);
            }
        }

        System.out.println("\nTotal unique orders: " + processedOrders.size());
    }
}
```

**Output:**
```
Processing orders...
Processing new order: ORD-001
Processing new order: ORD-002
Duplicate order detected: ORD-001
Processing new order: ORD-003
Duplicate order detected: ORD-002

Total unique orders: 3
```

---

### Concept 7: HashMap - Key-Value Pairs

**What is it?**
HashMap stores data in key-value pairs. Each key is unique and maps to exactly one value. Think of it like a Python dictionary.

**Why does it matter for automation?**
Essential for configuration management, test data mapping, storing element locators, and managing test results.

**Syntax:**
```java
import java.util.HashMap;

// Create HashMap
HashMap<String, String> config = new HashMap<>();

// Add key-value pairs
config.put("browser", "chrome");
config.put("url", "https://www.example.com");
config.put("timeout", "10");

// Get value by key
String browser = config.get("browser"); // "chrome"

// Update value
config.put("browser", "firefox"); // Updates existing key

// Remove
config.remove("timeout");

// Check if key exists
boolean hasUrl = config.containsKey("url"); // true

// Check if value exists
boolean hasChrome = config.containsValue("chrome"); // false (changed to firefox)

// Size
int size = config.size(); // 2
```

**Real Example:**
```java
import java.util.HashMap;

public class TestConfiguration {
    public static void main(String[] args) {
        // Store test configuration
        HashMap<String, String> testConfig = new HashMap<>();

        testConfig.put("baseUrl", "https://www.saucedemo.com");
        testConfig.put("browser", "chrome");
        testConfig.put("implicitWait", "10");
        testConfig.put("explicitWait", "20");
        testConfig.put("headless", "false");

        System.out.println("Test Configuration:");
        System.out.println("==================");

        // Iterate through all entries
        for (String key : testConfig.keySet()) {
            String value = testConfig.get(key);
            System.out.println(key + " = " + value);
        }

        // Access specific config
        System.out.println("\nStarting test with browser: " + testConfig.get("browser"));
        System.out.println("URL: " + testConfig.get("baseUrl"));
    }
}
```

**Common HashMap Methods:**
```java
HashMap<String, Integer> scores = new HashMap<>();

// Adding
scores.put("Test1", 95);
scores.put("Test2", 87);

// Accessing
scores.get("Test1");           // 95
scores.getOrDefault("Test3", 0); // 0 (default if key not found)

// Checking
scores.containsKey("Test1");   // true
scores.containsValue(95);      // true
scores.isEmpty();              // false

// Iterating
for (String key : scores.keySet()) {
    System.out.println(key + ": " + scores.get(key));
}

// Alternative iteration
for (HashMap.Entry<String, Integer> entry : scores.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

---

### Concept 8: Generics - Type Safety

**What is it?**
Generics allow you to write code that works with different types while maintaining type safety. The `<T>` syntax specifies what type of objects a collection can hold.

**Why does it matter for automation?**
Generics prevent runtime errors by catching type mismatches at compile time. Your IDE will warn you if you try to add a String to an Integer list.

**Syntax:**
```java
// Without Generics (old way - avoid this)
ArrayList list = new ArrayList();
list.add("Hello");
list.add(123);
list.add(true);
String text = (String) list.get(0); // Manual casting required

// With Generics (modern way)
ArrayList<String> names = new ArrayList<String>();
// Or using diamond operator (Java 7+)
ArrayList<String> names = new ArrayList<>();
names.add("Hello");
// names.add(123); // Compile error - type safety!
String text = names.get(0); // No casting needed
```

**Real Example:**
```java
import java.util.ArrayList;
import java.util.HashMap;

public class GenericCollections {
    public static void main(String[] args) {
        // Type-safe list of strings
        ArrayList<String> browsers = new ArrayList<>();
        browsers.add("Chrome");
        browsers.add("Firefox");
        // browsers.add(123); // Won't compile!

        // Type-safe list of integers
        ArrayList<Integer> timeouts = new ArrayList<>();
        timeouts.add(10);
        timeouts.add(20);
        // timeouts.add("30"); // Won't compile!

        // Type-safe map
        HashMap<String, Integer> testResults = new HashMap<>();
        testResults.put("LoginTest", 1); // 1 = pass
        testResults.put("CheckoutTest", 0); // 0 = fail
        // testResults.put(123, "pass"); // Won't compile!

        // No casting needed
        for (String browser : browsers) {
            System.out.println("Browser: " + browser);
        }
    }
}
```

**Generic Classes:**
```java
// Create a generic class
public class TestResult<T> {
    private T data;
    private boolean passed;

    public TestResult(T data, boolean passed) {
        this.data = data;
        this.passed = passed;
    }

    public T getData() {
        return data;
    }

    public boolean isPassed() {
        return passed;
    }
}

// Usage
TestResult<String> stringResult = new TestResult<>("Login test completed", true);
TestResult<Integer> numberResult = new TestResult<>(42, false);
```

---

### Concept 9: Iterating Collections

**What is it?**
Multiple ways to loop through collections: for-each loop, traditional for loop, Iterator, and Java 8 forEach.

**Syntax:**
```java
ArrayList<String> items = new ArrayList<>();
items.add("Item1");
items.add("Item2");
items.add("Item3");

// Method 1: Enhanced for-loop (for-each)
for (String item : items) {
    System.out.println(item);
}

// Method 2: Traditional for loop
for (int i = 0; i < items.size(); i++) {
    System.out.println(items.get(i));
}

// Method 3: Iterator
Iterator<String> iterator = items.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    System.out.println(item);
}

// Method 4: Java 8 forEach (lambda)
items.forEach(item -> System.out.println(item));
```

**Iterating Maps:**
```java
HashMap<String, Integer> scores = new HashMap<>();
scores.put("Test1", 95);
scores.put("Test2", 87);

// Method 1: Iterate keys
for (String key : scores.keySet()) {
    System.out.println(key + " = " + scores.get(key));
}

// Method 2: Iterate entries
for (HashMap.Entry<String, Integer> entry : scores.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}

// Method 3: Java 8 forEach
scores.forEach((key, value) -> System.out.println(key + " = " + value));
```

---

## 🐍 Python vs Java Comparison

### Exception Handling in Python (What You Know)
```python
# Python
try:
    result = 10 / 0
except ZeroDivisionError as e:
    print(f"Error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
finally:
    print("Cleanup")

# Raising exceptions
def validate_age(age):
    if age < 0:
        raise ValueError("Age cannot be negative")
```

### Exception Handling in Java (What You're Learning)
```java
// Java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Error: " + e.getMessage());
} catch (Exception e) {
    System.out.println("Unexpected error: " + e.getMessage());
} finally {
    System.out.println("Cleanup");
}

// Throwing exceptions
public void validateAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

### Collections in Python (What You Know)
```python
# Python List
names = ["John", "Jane", "Bob"]
names.append("Alice")
print(names[0])

# Python Set
unique_ids = {"ID1", "ID2", "ID1"}  # Only 2 items
unique_ids.add("ID3")

# Python Dictionary
config = {
    "browser": "chrome",
    "url": "https://example.com"
}
print(config["browser"])
config["timeout"] = "10"
```

### Collections in Java (What You're Learning)
```java
// Java ArrayList
ArrayList<String> names = new ArrayList<>();
names.add("John");
names.add("Jane");
names.add("Bob");
names.add("Alice");
System.out.println(names.get(0));

// Java HashSet
HashSet<String> uniqueIds = new HashSet<>();
uniqueIds.add("ID1");
uniqueIds.add("ID2");
uniqueIds.add("ID1"); // Ignored - duplicate
uniqueIds.add("ID3");

// Java HashMap
HashMap<String, String> config = new HashMap<>();
config.put("browser", "chrome");
config.put("url", "https://example.com");
System.out.println(config.get("browser"));
config.put("timeout", "10");
```

**Key Differences:**
| Aspect | Python | Java |
|--------|--------|------|
| Exception Syntax | `try/except/finally` | `try/catch/finally` |
| Raising Exceptions | `raise ValueError()` | `throw new Exception()` |
| List Type | `list = []` | `ArrayList<T> list = new ArrayList<>()` |
| Set Type | `set = {}` | `HashSet<T> set = new HashSet<>()` |
| Dictionary/Map | `dict = {}` | `HashMap<K,V> map = new HashMap<>()` |
| Type Safety | Dynamic (runtime) | Static with Generics (compile-time) |

**Mental Model Shift:**
In Python, you can put any type in a list. In Java with Generics, you declare the type upfront (`ArrayList<String>`), and the compiler ensures type safety. This catches errors earlier!

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Not Handling Exceptions Properly
❌ **Wrong:**
```java
public void processData(String data) {
    // Ignoring potential exceptions
    int value = Integer.parseInt(data); // Will crash if data is not a number
}
```

✅ **Correct:**
```java
public void processData(String data) {
    try {
        int value = Integer.parseInt(data);
        System.out.println("Processed value: " + value);
    } catch (NumberFormatException e) {
        System.out.println("Invalid number format: " + e.getMessage());
    }
}
```

**Why it matters:** In automation, test data can be unpredictable. Always validate and handle potential exceptions.

---

### Mistake 2: Empty Catch Blocks
❌ **Wrong:**
```java
try {
    driver.findElement(By.id("loginButton")).click();
} catch (Exception e) {
    // Silent failure - very bad!
}
```

✅ **Correct:**
```java
try {
    driver.findElement(By.id("loginButton")).click();
} catch (NoSuchElementException e) {
    System.out.println("Login button not found: " + e.getMessage());
    e.printStackTrace();
    // Log the error or take screenshot
} catch (Exception e) {
    System.out.println("Unexpected error: " + e.getMessage());
    e.printStackTrace();
}
```

**Why it matters:** Silent failures make debugging impossible. Always log exceptions!

---

### Mistake 3: Not Using Generics
❌ **Wrong:**
```java
ArrayList list = new ArrayList(); // No type specified
list.add("Hello");
list.add(123);
String text = (String) list.get(0); // Manual casting
```

✅ **Correct:**
```java
ArrayList<String> list = new ArrayList<>(); // Type specified
list.add("Hello");
// list.add(123); // Won't compile!
String text = list.get(0); // No casting needed
```

**Why it matters:** Generics catch type errors at compile time, not runtime. Safer code!

---

### Mistake 4: Modifying Collection While Iterating
❌ **Wrong:**
```java
ArrayList<String> items = new ArrayList<>();
items.add("A");
items.add("B");
items.add("C");

for (String item : items) {
    if (item.equals("B")) {
        items.remove(item); // ConcurrentModificationException!
    }
}
```

✅ **Correct:**
```java
ArrayList<String> items = new ArrayList<>();
items.add("A");
items.add("B");
items.add("C");

Iterator<String> iterator = items.iterator();
while (iterator.hasNext()) {
    String item = iterator.next();
    if (item.equals("B")) {
        iterator.remove(); // Safe removal
    }
}
```

**Why it matters:** Modifying a collection while iterating causes runtime exceptions. Use Iterator.remove() instead.

---

### Mistake 5: NullPointerException with Collections
❌ **Wrong:**
```java
ArrayList<String> testData = null;
testData.add("data"); // NullPointerException!
```

✅ **Correct:**
```java
ArrayList<String> testData = new ArrayList<>(); // Initialize properly
testData.add("data"); // Safe

// Or check for null
if (testData != null) {
    testData.add("data");
}
```

**Why it matters:** Always initialize collections before use. Null collections are a common source of crashes.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use ArrayList for Test Data**
Store test data in ArrayList for easy iteration and management. Perfect for data-driven testing.

**Tip 2: HashMap for Configuration**
Use HashMap to store test configuration (URLs, credentials, timeouts). Easy to update and maintain.

**Tip 3: Custom Exceptions for Test Failures**
Create custom exceptions like `TestDataException`, `ConfigurationException` to categorize failures clearly.

**Tip 4: Finally Block for Cleanup**
Always close resources (browser, files, DB connections) in finally block to ensure cleanup even if tests fail.

**Tip 5: Generics Prevent Runtime Errors**
Use generics (`ArrayList<String>`) to catch type errors during development, not during test execution.

**Tip 6: Collections for WebElements**
Selenium returns `List<WebElement>`. Understanding ArrayList makes working with multiple elements easier.

---

## 🔍 Deep Dive: Exception Handling Best Practices

**What experienced developers know:**
Exception handling is not just about catching errors - it's about making your code resilient, maintainable, and debuggable. In test automation, proper exception handling is the difference between tests that fail gracefully with clear messages and tests that crash with confusing stack traces.

**Best Practices:**

1. **Catch Specific Exceptions First**
```java
try {
    // Selenium code
} catch (NoSuchElementException e) {
    // Handle missing element
} catch (TimeoutException e) {
    // Handle timeout
} catch (WebDriverException e) {
    // Handle general Selenium errors
} catch (Exception e) {
    // Catch-all for unexpected errors
}
```

2. **Provide Meaningful Error Messages**
```java
try {
    driver.findElement(By.id("submitBtn")).click();
} catch (NoSuchElementException e) {
    throw new RuntimeException(
        "Submit button not found on page: " + driver.getCurrentUrl() +
        ". Expected element ID: submitBtn", e
    );
}
```

3. **Log and Rethrow When Needed**
```java
try {
    performLogin(username, password);
} catch (Exception e) {
    System.out.println("Login failed for user: " + username);
    e.printStackTrace();
    throw e; // Rethrow for test framework to handle
}
```

**When to use this:** Always in production test frameworks. Clear exception handling makes debugging failed tests much faster.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Exception** | An event that disrupts normal program flow | `NullPointerException` |
| **try-catch** | Block to handle exceptions | `try { } catch (Exception e) { }` |
| **throw** | Keyword to throw an exception | `throw new Exception("Error")` |
| **throws** | Declares method may throw exceptions | `public void test() throws Exception` |
| **finally** | Block that always executes | `finally { driver.quit(); }` |
| **Collection** | Group of objects | `ArrayList`, `HashSet`, `HashMap` |
| **List** | Ordered collection allowing duplicates | `ArrayList<String>` |
| **Set** | Unordered collection of unique elements | `HashSet<Integer>` |
| **Map** | Key-value pair storage | `HashMap<String, String>` |
| **Generics** | Type parameters for type safety | `<T>`, `<K,V>` |
| **Iterator** | Object for traversing collections | `Iterator<String> it = list.iterator()` |

---

## 🎓 Interview Prep

**Common Interview Questions on Exception Handling and Collections:**

**Q1: What is the difference between throw and throws?**
**A:** `throw` is used to explicitly throw an exception from code: `throw new Exception("error")`. `throws` is used in method signature to declare that a method may throw exceptions: `public void readFile() throws IOException`. The caller must handle exceptions declared with `throws`.

```java
// Example
public void validateAge(int age) throws IllegalArgumentException {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

**Q2: What is the difference between ArrayList and HashSet?**
**A:** ArrayList maintains insertion order and allows duplicate elements. HashSet does not maintain order and only stores unique elements. ArrayList provides index-based access `get(index)`, while HashSet doesn't have index-based access. Use ArrayList when order matters and duplicates are allowed. Use HashSet when you need unique elements.

```java
ArrayList<String> list = new ArrayList<>();
list.add("A"); list.add("B"); list.add("A"); // Size: 3

HashSet<String> set = new HashSet<>();
set.add("A"); set.add("B"); set.add("A"); // Size: 2 (duplicate ignored)
```

**Q3: Why use Generics in Java collections?**
**A:** Generics provide compile-time type safety, eliminating runtime ClassCastException errors. Without generics, you can add any object to a collection and must manually cast when retrieving. With generics like `ArrayList<String>`, the compiler ensures only Strings are added, and no casting is needed. This catches errors earlier and makes code cleaner.

```java
// Without generics - unsafe
ArrayList list = new ArrayList();
list.add("Hello");
String s = (String) list.get(0); // Manual cast needed

// With generics - type safe
ArrayList<String> list = new ArrayList<>();
list.add("Hello");
String s = list.get(0); // No cast needed
```

**Q4: How do you handle exceptions in Selenium WebDriver?**
**A:** In Selenium, use try-catch blocks to handle common exceptions like NoSuchElementException, TimeoutException, and StaleElementReferenceException. Always use finally block to close the browser even if tests fail. Create custom exceptions for test-specific failures. Log meaningful error messages with page URL and element details.

```java
WebDriver driver = null;
try {
    driver = new ChromeDriver();
    driver.get("https://example.com");
    driver.findElement(By.id("btn")).click();
} catch (NoSuchElementException e) {
    System.out.println("Element not found: " + e.getMessage());
} finally {
    if (driver != null) driver.quit();
}
```

**Q5: What is the difference between HashMap and ArrayList?**
**A:** ArrayList stores elements by index (0, 1, 2...) and retrieves by position. HashMap stores key-value pairs and retrieves by key. ArrayList maintains insertion order; HashMap doesn't guarantee order. Use ArrayList for ordered lists of elements. Use HashMap for lookups by key, like configuration settings or data mappings.

```java
ArrayList<String> list = new ArrayList<>();
list.add("First"); // Index 0
String value = list.get(0); // Access by index

HashMap<String, String> map = new HashMap<>();
map.put("key1", "First"); // Store by key
String value = map.get("key1"); // Access by key
```

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. What is the purpose of the finally block, and when does it execute?
2. What's the difference between checked and unchecked exceptions?
3. When would you use a HashSet instead of an ArrayList?
4. What does `ArrayList<String>` mean, and why is it better than `ArrayList`?
5. How do you iterate through a HashMap and access both keys and values?

**Answers at the end of Practice file.**

---
