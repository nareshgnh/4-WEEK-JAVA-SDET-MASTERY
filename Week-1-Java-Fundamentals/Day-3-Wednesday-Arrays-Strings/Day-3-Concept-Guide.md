# DAY 3: ARRAYS & STRINGS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master array declaration, initialization, and manipulation
- [ ] Understand ArrayList for dynamic arrays
- [ ] Work with String class and its methods
- [ ] Perform string manipulation operations
- [ ] Use StringBuilder for efficient string building
- [ ] Build an Anagram Checker program

**Time Required:** 2.5-3 hours
**Difficulty:** Medium
**Prerequisite:** Day 1 (Variables), Day 2 (Loops, Control Flow)

---

## 📚 Core Concepts

### Concept 1: Arrays - Fixed-Size Collections

**What is it?**
An array is a fixed-size container that holds multiple values of the same data type in contiguous memory. Once created, its size cannot be changed.

**Why does it matter for automation?**
In Selenium, you'll store multiple test data values, WebElements, test URLs, or test results in arrays. Arrays are fundamental to working with `findElements()` which returns a list of WebElements.

**Syntax:**
```java
// Declaration
dataType[] arrayName;

// Initialization
int[] numbers = new int[5];  // Array of 5 integers (all initialized to 0)

// Declaration + Initialization
String[] browsers = {"chrome", "firefox", "edge", "safari"};

// Access elements (zero-indexed)
String firstBrowser = browsers[0];  // "chrome"
browsers[2] = "brave";  // Modify element

// Array length
int size = browsers.length;  // 4 (note: length, not length())
```

**Explanation:**
- Arrays are **zero-indexed**: first element is at index 0
- `arrayName.length` gives the number of elements (it's a property, not a method)
- All elements must be the same type
- Size is fixed - you cannot add or remove elements after creation
- Default values: 0 for numbers, `false` for boolean, `null` for objects

**Real Example:**
```java
public class TestDataArray {
    public static void main(String[] args) {
        // Store test user credentials
        String[] usernames = {"user1@test.com", "user2@test.com", "admin@test.com"};
        String[] passwords = {"pass123", "pass456", "admin999"};

        // Iterate through test data
        for (int i = 0; i < usernames.length; i++) {
            System.out.println("Testing with: " + usernames[i]);
            // In real automation: login(usernames[i], passwords[i]);
        }
    }
}
```

**Common Array Operations:**
```java
// 1. Declaring arrays of different types
int[] numbers = {1, 2, 3, 4, 5};
double[] prices = {9.99, 19.99, 29.99};
boolean[] testResults = {true, true, false, true};
String[] testNames = {"Login", "Logout", "Search", "Checkout"};

// 2. Accessing elements
int firstNumber = numbers[0];           // 1
String lastTest = testNames[testNames.length - 1];  // "Checkout"

// 3. Modifying elements
numbers[2] = 100;  // Change third element from 3 to 100

// 4. Iterating with for loop
for (int i = 0; i < numbers.length; i++) {
    System.out.println("Element at index " + i + ": " + numbers[i]);
}

// 5. Iterating with enhanced for loop (for-each)
for (int num : numbers) {
    System.out.println("Number: " + num);
}

// 6. Multi-dimensional arrays
int[][] matrix = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
int element = matrix[1][2];  // 6 (row 1, column 2)
```

---

### Concept 2: ArrayList - Dynamic Arrays

**What is it?**
ArrayList is a resizable array from the Java Collections Framework. It can grow and shrink dynamically as you add or remove elements.

**Why does it matter for automation?**
When you don't know the exact number of elements (like dynamic test data, varying number of search results, or WebElements), ArrayList is your go-to choice. Selenium's `driver.findElements()` often returns data you'll convert to ArrayList for manipulation.

**Syntax:**
```java
import java.util.ArrayList;

// Declaration and creation
ArrayList<String> testCases = new ArrayList<>();

// Adding elements
testCases.add("Login Test");
testCases.add("Logout Test");
testCases.add("Search Test");

// Accessing elements
String firstTest = testCases.get(0);  // "Login Test" (use get(), not [])

// Modifying elements
testCases.set(1, "Updated Logout Test");

// Removing elements
testCases.remove(2);  // Remove by index
testCases.remove("Login Test");  // Remove by value

// Size
int count = testCases.size();  // Use size(), not length
```

**Explanation:**
- `ArrayList<Type>` - Type in angle brackets specifies what it holds (generics)
- Use `add()` to append elements
- Use `get(index)` to access elements (not array bracket notation)
- Use `set(index, value)` to modify
- Use `size()` to get number of elements (method, not property)
- Automatically resizes as needed

**Real Example:**
```java
import java.util.ArrayList;

public class TestSuiteBuilder {
    public static void main(String[] args) {
        // Build dynamic test suite
        ArrayList<String> smokeSuite = new ArrayList<>();

        // Add critical tests
        smokeSuite.add("Verify Homepage Loads");
        smokeSuite.add("Verify Login Works");
        smokeSuite.add("Verify Search Functions");
        smokeSuite.add("Verify Cart Updates");

        // Print test suite
        System.out.println("Smoke Test Suite (" + smokeSuite.size() + " tests):");
        for (int i = 0; i < smokeSuite.size(); i++) {
            System.out.println((i + 1) + ". " + smokeSuite.get(i));
        }

        // Check if specific test exists
        if (smokeSuite.contains("Verify Login Works")) {
            System.out.println("Login test is included in suite");
        }
    }
}
```

**Common ArrayList Methods:**
```java
ArrayList<String> list = new ArrayList<>();

// Adding
list.add("Element");           // Add at end
list.add(0, "First");          // Add at specific index

// Accessing
String item = list.get(0);     // Get element
int index = list.indexOf("Element");  // Find index (-1 if not found)

// Modifying
list.set(0, "Updated");        // Replace element

// Removing
list.remove(0);                // Remove by index
list.remove("Element");        // Remove by value
list.clear();                  // Remove all elements

// Checking
boolean exists = list.contains("Element");  // Check if exists
boolean empty = list.isEmpty();             // Check if empty
int size = list.size();                     // Get size

// Converting to array
String[] array = list.toArray(new String[0]);
```

---

### Concept 3: String Class - Immutable Text

**What is it?**
String is a class representing immutable sequences of characters. Once created, a String cannot be modified - any operation that appears to modify it actually creates a new String.

**Why does it matter for automation?**
Strings are everywhere in test automation: URLs, test data, element text, assertions, error messages, locators. Understanding String operations is critical for data manipulation and validation.

**Syntax:**
```java
// Creating strings
String s1 = "Hello";           // String literal
String s2 = new String("Hello");  // String object
String s3 = "";                // Empty string
String s4 = "Hello" + " World"; // Concatenation

// String is immutable
String original = "Test";
String modified = original.concat(" Case");  // Creates new String
// original is still "Test", modified is "Test Case"
```

**Common String Methods:**
```java
String text = "Selenium WebDriver";

// Length
int len = text.length();  // 17 (note: method, not property like array)

// Character access
char firstChar = text.charAt(0);  // 'S'
char lastChar = text.charAt(text.length() - 1);  // 'r'

// Substring
String sub1 = text.substring(0, 8);  // "Selenium" (start to end-1)
String sub2 = text.substring(9);     // "WebDriver" (from index to end)

// Case conversion
String upper = text.toUpperCase();  // "SELENIUM WEBDRIVER"
String lower = text.toLowerCase();  // "selenium webdriver"

// Trimming whitespace
String padded = "  test  ";
String trimmed = padded.trim();  // "test"

// Replacement
String replaced = text.replace("WebDriver", "Grid");  // "Selenium Grid"

// Splitting
String csv = "chrome,firefox,edge";
String[] browsers = csv.split(",");  // ["chrome", "firefox", "edge"]

// Checking content
boolean starts = text.startsWith("Selenium");  // true
boolean ends = text.endsWith("Driver");        // true
boolean contains = text.contains("Web");       // true

// Comparing
boolean equal = text.equals("Selenium WebDriver");  // true
boolean equalIgnoreCase = text.equalsIgnoreCase("selenium webdriver");  // true

// Finding
int index = text.indexOf("Web");  // 9 (position of first occurrence)
int lastIndex = text.lastIndexOf("e");  // 14
```

**Real Example - Test Automation:**
```java
public class StringTestExample {
    public static void main(String[] args) {
        // Scenario: Validate URL structure
        String testUrl = "https://www.saucedemo.com/inventory.html";

        // Validation
        if (testUrl.startsWith("https://")) {
            System.out.println("✓ Secure connection");
        }

        if (testUrl.contains("saucedemo.com")) {
            System.out.println("✓ Correct domain");
        }

        // Extract page name
        String[] urlParts = testUrl.split("/");
        String page = urlParts[urlParts.length - 1];
        System.out.println("Testing page: " + page);  // inventory.html

        // Build dynamic locator
        String buttonId = "add-to-cart";
        String productName = "Sauce Labs Backpack";
        String locator = buttonId + "-" + productName.toLowerCase().replace(" ", "-");
        System.out.println("Locator: " + locator);
        // Output: add-to-cart-sauce-labs-backpack
    }
}
```

---

### Concept 4: StringBuilder - Efficient String Building

**What is it?**
StringBuilder is a mutable sequence of characters. Unlike String, it can be modified without creating new objects, making it more efficient for building strings in loops.

**Why does it matter for automation?**
When generating test reports, building dynamic queries, or concatenating many strings in a loop, StringBuilder is much more efficient than String concatenation.

**Syntax:**
```java
// Creating StringBuilder
StringBuilder sb = new StringBuilder();
StringBuilder sb2 = new StringBuilder("Initial text");

// Appending
sb.append("Hello");
sb.append(" ");
sb.append("World");

// Converting to String
String result = sb.toString();  // "Hello World"

// Other operations
sb.insert(5, " Java");  // Insert at position
sb.delete(5, 10);       // Delete range
sb.reverse();           // Reverse content
sb.setCharAt(0, 'h');   // Change character
```

**String vs StringBuilder Comparison:**
```java
// ❌ Inefficient (creates multiple String objects)
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i + ",";  // Creates new String object in each iteration!
}

// ✅ Efficient (modifies same StringBuilder object)
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i).append(",");  // Modifies existing object
}
String result = sb.toString();
```

**Real Example - Test Report:**
```java
public class TestReportBuilder {
    public static void main(String[] args) {
        // Building test execution report
        StringBuilder report = new StringBuilder();

        report.append("===== Test Execution Report =====\n");
        report.append("Date: 2024-01-15\n");
        report.append("Suite: Regression Suite\n\n");

        String[] tests = {"Login", "Search", "Checkout", "Logout"};
        boolean[] results = {true, true, false, true};

        report.append("Test Results:\n");
        for (int i = 0; i < tests.length; i++) {
            report.append((i + 1))
                  .append(". ")
                  .append(tests[i])
                  .append(": ")
                  .append(results[i] ? "PASS" : "FAIL")
                  .append("\n");
        }

        report.append("\nSummary: 3 passed, 1 failed");

        // Convert to String and print
        System.out.println(report.toString());
    }
}
```

---

### Concept 5: String Comparison and Equality

**What is it?**
Proper string comparison is critical in Java. Using `==` compares memory addresses, while `.equals()` compares actual content.

**Why does it matter for automation?**
Test assertions fail if you use wrong comparison. When validating element text, error messages, or API responses, you must use `.equals()`.

**Syntax:**
```java
String s1 = "chrome";
String s2 = "chrome";
String s3 = new String("chrome");

// ❌ WRONG - Compares memory references
if (s1 == s2) {  // May be true (string pool optimization)
    System.out.println("Same reference");
}
if (s1 == s3) {  // False (different objects)
    System.out.println("Won't print");
}

// ✅ CORRECT - Compares actual content
if (s1.equals(s2)) {  // True (same content)
    System.out.println("Same content");
}
if (s1.equals(s3)) {  // True (same content)
    System.out.println("Same content");
}

// Case-insensitive comparison
if (s1.equalsIgnoreCase("CHROME")) {  // True
    System.out.println("Same ignoring case");
}

// Null-safe comparison
String s4 = null;
if (s1.equals(s4)) {  // Safe (s1 is not null)
    // Won't execute
}
// if (s4.equals(s1)) {  // ❌ NullPointerException!
```

**Real Example - Selenium Assertion:**
```java
public class LoginTestExample {
    public static void main(String[] args) {
        // Simulating Selenium test
        String expectedTitle = "Dashboard";
        String actualTitle = "Dashboard";  // From driver.getTitle()

        // ❌ WRONG
        if (actualTitle == expectedTitle) {
            System.out.println("Test Passed");  // Might work, but unreliable
        }

        // ✅ CORRECT
        if (actualTitle.equals(expectedTitle)) {
            System.out.println("✓ Title validation passed");
        } else {
            System.out.println("✗ Expected: " + expectedTitle + ", Got: " + actualTitle);
        }

        // Case-insensitive validation
        String errorMsg = "Invalid Credentials";
        String actualError = "invalid credentials";
        if (actualError.equalsIgnoreCase(errorMsg)) {
            System.out.println("✓ Error message displayed correctly");
        }
    }
}
```

---

## 🐍 Python vs Java Comparison

### Arrays/Lists in Python (What You Know)
```python
# Python - Lists are dynamic
browsers = ["chrome", "firefox", "edge"]
browsers.append("safari")  # Can add elements
browsers[0] = "brave"       # Modify element
first = browsers[0]         # Access element
length = len(browsers)      # Get length

# List comprehension
numbers = [1, 2, 3, 4, 5]
squared = [x**2 for x in numbers]

# Slicing
sub_list = browsers[1:3]    # ["firefox", "edge"]
```

### Arrays/Lists in Java (What You're Learning)
```java
// Java - Arrays are fixed size
String[] browsers = {"chrome", "firefox", "edge"};
// browsers.add("safari");  // ❌ Can't do this with arrays!
browsers[0] = "brave";       // ✅ Can modify
String first = browsers[0];  // Access element
int length = browsers.length; // Get length (property, not method)

// ArrayList for dynamic behavior
ArrayList<String> browserList = new ArrayList<>();
browserList.add("chrome");
browserList.add("firefox");  // ✅ Can add to ArrayList

// No direct list comprehension (use loops or streams)
int[] numbers = {1, 2, 3, 4, 5};
int[] squared = new int[numbers.length];
for (int i = 0; i < numbers.length; i++) {
    squared[i] = numbers[i] * numbers[i];
}

// No direct slicing (use Arrays.copyOfRange or ArrayList.subList)
import java.util.Arrays;
String[] subArray = Arrays.copyOfRange(browsers, 1, 3);
```

### Strings in Python vs Java

**Python:**
```python
# Python strings
text = "hello world"
upper = text.upper()           # Method call
length = len(text)             # Function
first_char = text[0]           # Index access
substring = text[0:5]          # Slicing
words = text.split(" ")        # Split
joined = " ".join(words)       # Join

# String formatting
name = "John"
age = 30
message = f"Name: {name}, Age: {age}"  # f-string
```

**Java:**
```java
// Java strings
String text = "hello world";
String upper = text.toUpperCase();    // Method call
int length = text.length();           // Method (note the ())
char firstChar = text.charAt(0);      // Method, not index
String substring = text.substring(0, 5); // Method
String[] words = text.split(" ");     // Split
String joined = String.join(" ", words); // Join

// String formatting
String name = "John";
int age = 30;
String message = String.format("Name: %s, Age: %d", name, age);
// Or concatenation
String message2 = "Name: " + name + ", Age: " + age;
```

**Key Differences:**
| Aspect | Python | Java |
|--------|--------|------|
| **List/Array Type** | Single type (list) | Two types (array, ArrayList) |
| **Size** | Always dynamic | Array=fixed, ArrayList=dynamic |
| **Add Element** | `list.append(x)` | `arrayList.add(x)` |
| **Access Element** | `list[0]` | `array[0]` or `list.get(0)` |
| **Length** | `len(list)` | `array.length` or `list.size()` |
| **String Length** | `len(str)` | `str.length()` |
| **String Index** | `str[0]` | `str.charAt(0)` |
| **String Slice** | `str[1:5]` | `str.substring(1, 5)` |
| **String Compare** | `str1 == str2` | `str1.equals(str2)` |
| **Mutability** | Strings immutable | Strings immutable, use StringBuilder |

**Mental Model Shift:**
In Python, lists are your go-to for any collection. In Java, choose based on need: arrays for fixed-size (faster), ArrayList for dynamic (flexible). Python's simple `str[0]` becomes Java's `str.charAt(0)` - more verbose but explicit.

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: ArrayIndexOutOfBoundsException
❌ **Wrong:**
```java
String[] browsers = {"chrome", "firefox", "edge"};
String fourth = browsers[3];  // Error! Only indices 0, 1, 2 exist
```

✅ **Correct:**
```java
String[] browsers = {"chrome", "firefox", "edge"};
// Always check array bounds
if (3 < browsers.length) {
    String fourth = browsers[3];
} else {
    System.out.println("Index out of bounds");
}

// Or use length - 1 for last element
String last = browsers[browsers.length - 1];  // "edge"
```

**Why it matters:** This is the #1 runtime error with arrays. In test automation, if you're iterating through WebElements and go out of bounds, your test crashes.

---

### Mistake 2: Comparing Strings with ==
❌ **Wrong:**
```java
String expected = "Login Successful";
String actual = driver.getTitle();  // Simulated
if (actual == expected) {  // Unreliable!
    System.out.println("Test passed");
}
```

✅ **Correct:**
```java
String expected = "Login Successful";
String actual = driver.getTitle();  // Simulated
if (actual.equals(expected)) {  // Correct content comparison
    System.out.println("✓ Test passed");
} else {
    System.out.println("✗ Expected: " + expected + ", Got: " + actual);
}
```

**Why it matters:** Using `==` compares memory addresses, not content. Test assertions will randomly fail. Always use `.equals()` or `.equalsIgnoreCase()`.

---

### Mistake 3: Modifying String in Loop (Inefficient)
❌ **Wrong:**
```java
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i + ",";  // Creates 1000 new String objects!
}
```

✅ **Correct:**
```java
StringBuilder result = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    result.append(i).append(",");  // Modifies same object
}
String finalResult = result.toString();
```

**Why it matters:** String concatenation in loops is extremely inefficient. When building test reports or log messages, use StringBuilder to avoid performance issues.

---

### Mistake 4: Null Pointer with Strings
❌ **Wrong:**
```java
String username = null;
if (username.equals("admin")) {  // NullPointerException!
    System.out.println("Admin user");
}
```

✅ **Correct:**
```java
String username = null;
// Check null first
if (username != null && username.equals("admin")) {
    System.out.println("Admin user");
}

// Or reverse the comparison (constant first)
if ("admin".equals(username)) {  // Safe even if username is null
    System.out.println("Admin user");
}
```

**Why it matters:** Null strings are common when elements aren't found or data is missing. Always null-check or use constant-first comparison.

---

### Mistake 5: Confusing Array Length vs ArrayList Size
❌ **Wrong:**
```java
String[] array = {"a", "b", "c"};
int arraySize = array.size();  // ❌ Compile error

ArrayList<String> list = new ArrayList<>();
list.add("a");
int listLength = list.length;  // ❌ Compile error
```

✅ **Correct:**
```java
String[] array = {"a", "b", "c"};
int arraySize = array.length;  // ✅ Property (no parentheses)

ArrayList<String> list = new ArrayList<>();
list.add("a");
int listSize = list.size();  // ✅ Method (with parentheses)
```

**Why it matters:** Easy to forget - arrays use `.length` (property), ArrayList uses `.size()` (method). This will cause compile errors.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Choose the Right Data Structure**
- **Array**: Fixed test data (browsers, test URLs, status codes)
- **ArrayList**: Dynamic data (search results, WebElement lists, test cases)
- **StringBuilder**: Building strings in loops (reports, logs)

**Tip 2: String Utility Methods for Test Automation**
```java
// Clean test data
String userInput = "  john@example.com  ";
String cleaned = userInput.trim();  // Remove spaces

// Build dynamic locators
String productName = "Sauce Labs Backpack";
String locatorId = "add-to-cart-" + productName.toLowerCase().replace(" ", "-");
// "add-to-cart-sauce-labs-backpack"

// Validate error messages (case-insensitive)
String expectedError = "Invalid credentials";
String actualError = "INVALID CREDENTIALS";
boolean matches = expectedError.equalsIgnoreCase(actualError);  // true
```

**Tip 3: Enhanced For Loop for Cleaner Code**
```java
// Instead of traditional for loop
for (int i = 0; i < browsers.length; i++) {
    System.out.println(browsers[i]);
}

// Use enhanced for (when you don't need index)
for (String browser : browsers) {
    System.out.println(browser);
}
```

**Tip 4: String Format for Clean Output**
```java
// Instead of messy concatenation
System.out.println("Test: " + testName + ", Status: " + status + ", Time: " + time + "ms");

// Use String.format
System.out.println(String.format("Test: %s, Status: %s, Time: %dms", testName, status, time));
```

---

## 🔍 Deep Dive: String Pool and Immutability

**What experienced developers know:**
Java optimizes memory by using a "String pool" for string literals. When you create `String s = "test"`, Java first checks if "test" exists in the pool. If yes, it reuses that object.

**Example:**
```java
String s1 = "chrome";  // Created in string pool
String s2 = "chrome";  // Reuses same object from pool
String s3 = new String("chrome");  // Creates new object in heap

System.out.println(s1 == s2);  // true (same reference in pool)
System.out.println(s1 == s3);  // false (different objects)
System.out.println(s1.equals(s3));  // true (same content)
```

**Why Strings are Immutable:**
```java
String original = "Hello";
String modified = original.concat(" World");

// original is still "Hello" (unchanged)
// modified is "Hello World" (new String object)
```

This immutability makes Strings thread-safe and secure, but inefficient for concatenation in loops - that's why StringBuilder exists.

**When to use this:**
- Use String literals (`"text"`) for constants and small values
- Use `new String()` rarely (only when you need distinct objects)
- Use StringBuilder when building strings dynamically
- Always use `.equals()` for comparison, never rely on `==`

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Array** | Fixed-size collection of same-type elements | `int[] nums = {1,2,3};` |
| **ArrayList** | Resizable array from Collections framework | `ArrayList<String> list = new ArrayList<>();` |
| **Index** | Position of element in array (starts at 0) | `array[0]` is first element |
| **String** | Immutable sequence of characters | `String s = "test";` |
| **StringBuilder** | Mutable sequence of characters | `StringBuilder sb = new StringBuilder();` |
| **Immutable** | Cannot be changed after creation | String objects |
| **Mutable** | Can be modified after creation | StringBuilder, arrays |
| **Zero-indexed** | First element is at position 0, not 1 | `array[0]`, `list.get(0)` |
| **Out of bounds** | Accessing index that doesn't exist | Accessing `array[10]` when length is 5 |
| **Substring** | Portion of a string | `"Hello".substring(0,3)` = "Hel" |

---

## 🎓 Interview Prep

**Common Interview Questions on Arrays & Strings:**

**Q1:** What's the difference between an array and ArrayList in Java?
**A:**
- **Array**: Fixed size, can hold primitives or objects, faster access, uses `[]` syntax
- **ArrayList**: Dynamic size, only holds objects (not primitives), uses methods like `add()`, `get()`, `remove()`

```java
// Array - fixed size
int[] numbers = new int[5];  // Must specify size, can't change
numbers[0] = 10;

// ArrayList - dynamic size
ArrayList<Integer> numberList = new ArrayList<>();
numberList.add(10);  // Can keep adding elements
numberList.add(20);
```

*Example: "In Selenium, I use arrays for fixed test data like browser configurations. For dynamic data like varying search results, I use ArrayList because the size isn't known beforehand."*

---

**Q2:** Why are Strings immutable in Java?
**A:**
Strings are immutable for security, thread-safety, and optimization:
- **Security**: String values (like passwords, URLs) can't be modified after creation
- **Thread-safety**: Multiple threads can safely share String objects
- **Performance**: String literals are cached in String pool, saving memory
- **Hashcode caching**: Strings can safely be used as HashMap keys

```java
String password = "secret123";
// No method can actually modify this String
// Any "modification" creates a new String object
```

*Example: "In test automation, String immutability ensures test data integrity. If I pass a URL to multiple test methods, I know it won't be accidentally modified."*

---

**Q3:** When should you use StringBuilder instead of String?
**A:**
Use StringBuilder when building strings in loops or with many concatenations:

```java
// ❌ Bad - creates many objects
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // Creates 1000 intermediate String objects
}

// ✅ Good - efficient
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);  // Modifies same object
}
String result = sb.toString();
```

*Example: "When generating test execution reports with hundreds of test results, I use StringBuilder to avoid creating thousands of intermediate String objects, significantly improving performance."*

---

**Q4:** How do you properly compare strings in Java?
**A:**
- Use `.equals()` for content comparison
- Use `.equalsIgnoreCase()` for case-insensitive comparison
- Never use `==` for String comparison (compares references, not content)

```java
String expected = "Pass";
String actual = "Pass";

// ❌ Wrong - unreliable
if (actual == expected) { }

// ✅ Correct
if (actual.equals(expected)) { }

// ✅ Case-insensitive
if (actual.equalsIgnoreCase("PASS")) { }

// ✅ Null-safe (constant first)
if ("Pass".equals(actual)) {  // Won't throw NPE if actual is null
}
```

*Example: "In Selenium assertions, I always use `.equals()` to compare expected vs actual text from web elements. Using `==` can cause flaky tests because it compares object references."*

---

**Q5:** Explain ArrayIndexOutOfBoundsException and how to prevent it.
**A:**
This exception occurs when accessing an invalid array index (negative or >= length).

**Prevention:**
```java
String[] browsers = {"chrome", "firefox", "edge"};

// ✅ Check bounds before access
if (index >= 0 && index < browsers.length) {
    String browser = browsers[index];
}

// ✅ Use enhanced for loop (no index needed)
for (String browser : browsers) {
    System.out.println(browser);
}

// ✅ Safe iteration
for (int i = 0; i < browsers.length; i++) {  // Note: i < length, not <=
    System.out.println(browsers[i]);
}
```

*Example: "When iterating through WebElements returned by `findElements()`, I ensure my loop condition is `i < elements.size()` to prevent accessing non-existent elements, which would crash the test."*

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What's the difference between `array.length` and `arrayList.size()`?**

2. **What will this code output?**
   ```java
   String s1 = "test";
   String s2 = "test";
   String s3 = new String("test");
   System.out.println(s1 == s2);
   System.out.println(s1 == s3);
   System.out.println(s1.equals(s3));
   ```

3. **Why is this code inefficient?**
   ```java
   String result = "";
   for (int i = 0; i < 100; i++) {
       result = result + i;
   }
   ```

4. **What's wrong with this code?**
   ```java
   String[] browsers = {"chrome", "firefox"};
   browsers.add("edge");
   ```

5. **How would you check if a string contains "@" symbol?**

**Answers in Practice Exercises file.**

---

## 🚀 Common String Operations for Test Automation

### Pattern 1: URL Validation
```java
String url = "https://www.example.com/login?user=test";

// Check protocol
if (url.startsWith("https://")) {
    System.out.println("✓ Secure connection");
}

// Extract domain
String domain = url.substring(url.indexOf("://") + 3, url.indexOf("/", 8));
System.out.println("Domain: " + domain);  // www.example.com

// Check query parameters
if (url.contains("?")) {
    String queryString = url.substring(url.indexOf("?") + 1);
    System.out.println("Query: " + queryString);  // user=test
}
```

### Pattern 2: Test Data Cleanup
```java
String rawInput = "  john.doe@EXAMPLE.com  ";

// Clean and normalize
String cleaned = rawInput.trim().toLowerCase();
System.out.println(cleaned);  // john.doe@example.com

// Validate email format
if (cleaned.contains("@") && cleaned.contains(".")) {
    System.out.println("✓ Valid email format");
}
```

### Pattern 3: Dynamic Locator Building
```java
String productName = "Sauce Labs Backpack";

// Build locator ID
String locatorId = "add-to-cart-" +
                   productName.toLowerCase()
                              .replace(" ", "-");
System.out.println(locatorId);
// Output: add-to-cart-sauce-labs-backpack
```

### Pattern 4: Parsing Test Results
```java
String testResult = "Test: Login, Status: PASS, Time: 1250ms";

// Extract information
String[] parts = testResult.split(",");
String testName = parts[0].split(":")[1].trim();  // Login
String status = parts[1].split(":")[1].trim();    // PASS
String time = parts[2].split(":")[1].trim();      // 1250ms

System.out.println("Test '" + testName + "' " + status + " in " + time);
```

### Pattern 5: Building Test Reports
```java
ArrayList<String> tests = new ArrayList<>();
tests.add("Login");
tests.add("Search");
tests.add("Checkout");

StringBuilder report = new StringBuilder();
report.append("===== Test Suite =====\n");
report.append("Total Tests: ").append(tests.size()).append("\n");

for (int i = 0; i < tests.size(); i++) {
    report.append(String.format("%d. %s\n", i + 1, tests.get(i)));
}

System.out.println(report.toString());
```

---

## 📝 Today's Mantra

> "Arrays store data, Strings carry meaning, and together they form the foundation of test automation. Master string manipulation, and you master test data handling."

---

## 🎯 Ready for Practice?

Now move to **Day-3-Practice-Exercises.md** to apply these concepts with hands-on coding exercises.

**Time spent on concepts:** ____ minutes
**Concepts confidence (1-10):** ____
**Ready to code:** Yes / Review once more

---
