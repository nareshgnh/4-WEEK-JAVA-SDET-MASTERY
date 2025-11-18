# DAY 3 CHEAT SHEET: ARRAYS & STRINGS

## ⚡ Quick Syntax Reference

### Arrays - Fixed Size
```java
// Declaration and initialization
int[] numbers = {1, 2, 3, 4, 5};
String[] browsers = new String[3];  // Array of size 3
double[] prices = {9.99, 19.99, 29.99};

// Access elements (zero-indexed)
String first = browsers[0];           // First element
String last = browsers[browsers.length - 1];  // Last element

// Modify element
browsers[1] = "firefox";

// Length (property, no parentheses)
int size = browsers.length;

// Traditional for loop
for (int i = 0; i < browsers.length; i++) {
    System.out.println(browsers[i]);
}

// Enhanced for loop (for-each)
for (String browser : browsers) {
    System.out.println(browser);
}

// Multi-dimensional array
int[][] matrix = {{1,2,3}, {4,5,6}};
int value = matrix[0][1];  // 2
```

### ArrayList - Dynamic Size
```java
import java.util.ArrayList;

// Create ArrayList
ArrayList<String> list = new ArrayList<>();

// Add elements
list.add("Element");           // Add at end
list.add(0, "First");          // Add at index

// Access elements
String item = list.get(0);     // Get by index
int index = list.indexOf("Element");  // Find index

// Modify
list.set(0, "Updated");        // Replace element

// Remove
list.remove(0);                // Remove by index
list.remove("Element");        // Remove by value
list.clear();                  // Remove all

// Size (method, with parentheses)
int size = list.size();

// Check
boolean exists = list.contains("Element");
boolean empty = list.isEmpty();

// Iterate
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}

for (String item : list) {
    System.out.println(item);
}
```

### Strings - Immutable
```java
// Create strings
String s1 = "Hello";           // String literal
String s2 = new String("Hi");  // String object
String s3 = "Hello" + " World"; // Concatenation

// Length (method, with parentheses)
int len = s1.length();

// Character access
char first = s1.charAt(0);     // 'H'

// Substring
String sub = s1.substring(0, 3);  // "Hel" (0 to 2)
String sub2 = s1.substring(1);    // "ello" (1 to end)

// Case conversion
String upper = s1.toUpperCase();  // "HELLO"
String lower = s1.toLowerCase();  // "hello"

// Trim whitespace
String trimmed = "  text  ".trim();  // "text"

// Replace
String replaced = s1.replace("l", "L");  // "HeLLo"

// Split
String[] parts = "a,b,c".split(",");  // ["a", "b", "c"]

// Join
String joined = String.join(",", parts);  // "a,b,c"

// Check content
boolean starts = s1.startsWith("He");  // true
boolean ends = s1.endsWith("lo");      // true
boolean contains = s1.contains("ll");  // true

// Find position
int index = s1.indexOf("l");      // 2 (first occurrence)
int lastIndex = s1.lastIndexOf("l"); // 3 (last occurrence)

// Compare (CRITICAL!)
boolean equal = s1.equals("Hello");  // true (content)
boolean equalIgnore = s1.equalsIgnoreCase("hello");  // true
// NEVER use == for String comparison!
```

### StringBuilder - Mutable
```java
// Create
StringBuilder sb = new StringBuilder();
StringBuilder sb2 = new StringBuilder("Initial");

// Append
sb.append("Hello");
sb.append(" ").append("World");  // Chaining

// Insert
sb.insert(5, " Java");  // Insert at position

// Delete
sb.delete(5, 10);       // Delete range

// Replace
sb.replace(0, 5, "Hi");  // Replace range

// Reverse
sb.reverse();

// Convert to String
String result = sb.toString();
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Syntax | Python Equivalent |
|---------|-------------|-------------------|
| **Array (fixed)** | `String[] arr = {"a", "b"};` | `arr = ["a", "b"]` |
| **Array access** | `arr[0]` | `arr[0]` |
| **Array length** | `arr.length` | `len(arr)` |
| **ArrayList** | `ArrayList<String> list = new ArrayList<>();` | `list = []` |
| **Add to list** | `list.add("item")` | `list.append("item")` |
| **Get from list** | `list.get(0)` | `list[0]` |
| **List size** | `list.size()` | `len(list)` |
| **String length** | `str.length()` | `len(str)` |
| **String char** | `str.charAt(0)` | `str[0]` |
| **Substring** | `str.substring(0, 3)` | `str[0:3]` |
| **String compare** | `str1.equals(str2)` | `str1 == str2` |
| **Case-insensitive** | `str1.equalsIgnoreCase(str2)` | `str1.lower() == str2.lower()` |
| **Split** | `str.split(",")` | `str.split(",")` |
| **Join** | `String.join(",", arr)` | `",".join(arr)` |
| **Contains** | `str.contains("text")` | `"text" in str` |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Array bounds** | `for (i=0; i<=arr.length; i++)` | `for (i=0; i<arr.length; i++)` |
| **Last element** | `arr[arr.length]` | `arr[arr.length - 1]` |
| **ArrayList access** | `list[0]` | `list.get(0)` |
| **Array vs ArrayList length** | `list.length` or `arr.size()` | `arr.length` and `list.size()` |
| **String comparison** | `if (str1 == str2)` | `if (str1.equals(str2))` |
| **String index** | `str[0]` | `str.charAt(0)` |
| **String immutability** | `str.append("x")` | Use StringBuilder |
| **Null check** | `if (str.isEmpty())` | `if (str != null && str.isEmpty())` |
| **Modify during iteration** | `for (x : list) list.remove(x)` | Iterate backwards or use new list |

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ Arrays are fixed-size, ArrayList is dynamic
- ✅ Arrays use `.length` (property), ArrayList uses `.size()` (method)
- ✅ Access: arrays use `[index]`, ArrayList uses `.get(index)`
- ✅ Strings are immutable - operations create new strings
- ✅ ALWAYS use `.equals()` for String comparison, NEVER `==`
- ✅ StringBuilder for efficient string building in loops
- ✅ String methods: `trim()`, `toLowerCase()`, `substring()`, `split()`
- ✅ Character arrays: `str.toCharArray()` and `Arrays.sort()`
- ✅ Enhanced for loop for cleaner iteration
- ✅ Null checks prevent NullPointerException

**Critical for Automation:**
- 🎯 Use ArrayList for dynamic test data collections
- 🎯 String manipulation for test data cleanup and validation
- 🎯 `.equals()` for all text assertions in Selenium
- 🎯 StringBuilder for building test reports efficiently
- 🎯 Proper null handling prevents flaky tests

---

## 🎯 Array Operations Quick Reference

### Creating Arrays
```java
// Method 1: Initialize with values
int[] nums = {1, 2, 3, 4, 5};

// Method 2: Create then assign
String[] browsers = new String[3];
browsers[0] = "chrome";
browsers[1] = "firefox";
browsers[2] = "edge";

// Method 3: Multi-dimensional
int[][] grid = {
    {1, 2, 3},
    {4, 5, 6}
};
```

### Common Array Patterns
```java
import java.util.Arrays;

// Copy array
int[] original = {1, 2, 3};
int[] copy = Arrays.copyOf(original, original.length);

// Sort array
Arrays.sort(original);  // Sorts in place

// Fill array
int[] nums = new int[5];
Arrays.fill(nums, 10);  // All elements = 10

// Convert to String
System.out.println(Arrays.toString(nums));  // [10, 10, 10, 10, 10]

// Compare arrays
boolean same = Arrays.equals(arr1, arr2);

// Search (requires sorted array)
int index = Arrays.binarySearch(sortedArray, value);
```

---

## 🎯 String Operations Quick Reference

### String Cleaning and Normalization
```java
String raw = "  John.Doe@EXAMPLE.com  ";

// Clean email
String email = raw.trim()           // Remove spaces
                  .toLowerCase()    // Convert to lowercase
                  .replace(" ", ""); // Remove internal spaces

// Extract username
String username = email.substring(0, email.indexOf("@"));

// Extract domain
String domain = email.substring(email.indexOf("@") + 1);
```

### String Validation
```java
String text = "TestData123";

// Check content
boolean hasNumbers = text.matches(".*\\d.*");
boolean onlyLetters = text.matches("[a-zA-Z]+");
boolean validEmail = text.contains("@") && text.contains(".");

// Length check
if (text.length() >= 6 && text.length() <= 20) {
    System.out.println("Valid length");
}

// Not empty
if (text != null && !text.isEmpty()) {
    System.out.println("Not empty");
}
```

### String Building
```java
// ❌ Bad for loops
String result = "";
for (int i = 0; i < 100; i++) {
    result += i + ",";  // Creates 100 String objects!
}

// ✅ Good for loops
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 100; i++) {
    sb.append(i).append(",");
}
String result = sb.toString();
```

---

## 🎤 Interview Phrases

**When asked about arrays vs ArrayList:**

*"Arrays are fixed-size collections of elements that must be defined at creation and cannot be resized. ArrayList is a dynamic resizable array from the Collections framework that can grow and shrink. In test automation, I use arrays for fixed test data like browser configurations, and ArrayList for dynamic data like search results where the count varies."*

**When asked about String immutability:**

*"Strings in Java are immutable, meaning once created, they cannot be changed. Any operation that appears to modify a String actually creates a new String object. This is beneficial for security and thread-safety but inefficient for concatenation in loops. That's why I use StringBuilder when building strings dynamically, such as generating test reports with hundreds of results."*

**When asked about proper String comparison:**

*"The critical distinction is that `==` compares object references while `.equals()` compares content. For test assertions comparing element text, error messages, or any String content, I always use `.equals()` or `.equalsIgnoreCase()` to ensure accurate content comparison. Using `==` can cause intermittent test failures."*

**When asked about ArrayIndexOutOfBoundsException:**

*"This occurs when accessing an invalid array index. Arrays are zero-indexed, so valid indices range from 0 to length-1. I prevent this by ensuring loop conditions use `i < array.length` not `i <= array.length`, and accessing the last element with `array[array.length - 1]`. In test automation, this commonly occurs when iterating through WebElement lists."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **Arrays hold data, Strings carry meaning. Master `.equals()` for String comparison, use `< length` for array iteration, and choose ArrayList when size is dynamic. These three rules prevent 90% of array/string bugs in test automation.**

---

## 🎯 Test Automation Patterns

### Pattern 1: Test Data Arrays
```java
public class TestData {
    // Fixed test data
    public static final String[] BROWSERS = {"chrome", "firefox", "edge"};
    public static final String[] TEST_URLS = {
        "https://www.example.com",
        "https://www.test.com",
        "https://www.demo.com"
    };

    // Parallel arrays for test credentials
    public static final String[] USERNAMES = {
        "admin@test.com",
        "user@test.com",
        "guest@test.com"
    };
    public static final String[] PASSWORDS = {
        "admin123",
        "user123",
        "guest123"
    };
}
```

### Pattern 2: Dynamic Test Suite Management
```java
public class TestSuiteBuilder {
    private ArrayList<String> testCases = new ArrayList<>();

    public void addTest(String testName) {
        testCases.add(testName);
    }

    public void removeTest(String testName) {
        testCases.remove(testName);
    }

    public ArrayList<String> getTestsByTag(String tag) {
        ArrayList<String> filtered = new ArrayList<>();
        for (String test : testCases) {
            if (test.toLowerCase().contains(tag.toLowerCase())) {
                filtered.add(test);
            }
        }
        return filtered;
    }
}
```

### Pattern 3: Element Text Validation
```java
public class ElementValidator {
    public static boolean validateText(String actualText, String expectedText) {
        // Normalize both texts
        String actual = actualText.trim().replaceAll("\\s+", " ");
        String expected = expectedText.trim().replaceAll("\\s+", " ");

        // Case-insensitive comparison
        return actual.equalsIgnoreCase(expected);
    }

    public static boolean containsText(String actualText, String searchText) {
        return actualText.toLowerCase().contains(searchText.toLowerCase());
    }
}

// Usage in Selenium:
// String elementText = driver.findElement(By.id("message")).getText();
// assert validateText(elementText, "Welcome User!");
```

### Pattern 4: URL Builder and Parser
```java
public class URLHelper {
    public static String buildURL(String base, String... paths) {
        StringBuilder url = new StringBuilder(base);
        for (String path : paths) {
            if (!url.toString().endsWith("/") && !path.startsWith("/")) {
                url.append("/");
            }
            url.append(path);
        }
        return url.toString();
    }

    public static String addQueryParams(String url, String... params) {
        StringBuilder result = new StringBuilder(url);
        result.append("?");
        for (int i = 0; i < params.length; i++) {
            if (i > 0) result.append("&");
            result.append(params[i]);
        }
        return result.toString();
    }
}

// Usage:
// String url = URLHelper.buildURL("https://example.com", "api", "users");
// url = URLHelper.addQueryParams(url, "page=1", "limit=10");
// Result: https://example.com/api/users?page=1&limit=10
```

### Pattern 5: Test Report Builder
```java
public class ReportBuilder {
    private StringBuilder report;
    private int totalTests = 0;
    private int passedTests = 0;

    public ReportBuilder(String suiteName) {
        report = new StringBuilder();
        report.append("===== ").append(suiteName).append(" =====\n");
    }

    public void addTestResult(String testName, boolean passed) {
        totalTests++;
        if (passed) passedTests++;

        String status = passed ? "✓ PASS" : "✗ FAIL";
        report.append(String.format("%d. %s: %s\n",
                                     totalTests, testName, status));
    }

    public String getReport() {
        report.append(String.format("\nTotal: %d | Passed: %d | Failed: %d\n",
                                     totalTests, passedTests,
                                     totalTests - passedTests));
        double passRate = (passedTests * 100.0) / totalTests;
        report.append(String.format("Pass Rate: %.2f%%\n", passRate));
        return report.toString();
    }
}
```

---

## 🔮 Tomorrow's Preview

**Day 4 Topic:** OOP Part 1 (Classes, Objects, Constructors, Methods)

**What to review tonight:**
- How arrays and objects are related (arrays store objects)
- String class methods (you've been using objects all along!)
- The concept of methods (you created them in today's project)

**What to think about:**
*"If String has methods like `.length()` and `.trim()`, who defines those methods? Tomorrow you'll learn to create your own classes with custom methods - the foundation of Page Object Model in Selenium."*

**Connection to Day 3:**
- Arrays store objects → Tomorrow you'll create your own objects
- String is a class → Tomorrow you'll create your own classes
- Methods like `areAnagrams()` → Tomorrow you'll organize methods in classes
- Test data arrays → Tomorrow you'll encapsulate data in objects

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [x] Learned arrays (fixed-size collections)
- [x] Learned ArrayList (dynamic collections)
- [x] Mastered String class and methods
- [x] Used StringBuilder for efficiency
- [x] Completed 6 practice exercises
- [x] Built Anagram Checker project
- [x] ~2.5-3 hours of Java practice

**Cumulative Progress:**
- Days completed: 3/7
- Java concepts mastered: 20+
- Lines of code written: ~300-400

**Momentum Statement:**
*"Day 3 complete! You've mastered data structures and string manipulation - the building blocks of test data handling. Arrays and strings are everywhere in automation: storing test cases, processing element text, building reports. Tomorrow you'll learn OOP, and suddenly everything clicks: classes, objects, methods - the architecture of real automation frameworks. You're 43% through Week 1. The transformation from Python to Java SDET is happening!"*

---

## 📝 Quick Reference Card (Print This!)

```
╔═══════════════════════════════════════════════════════════╗
║       ARRAYS & STRINGS - DAY 3 REFERENCE                  ║
╠═══════════════════════════════════════════════════════════╣
║ ARRAYS (Fixed Size):                                      ║
║ String[] arr = {"a", "b", "c"};                          ║
║ arr[0]           // Access                               ║
║ arr.length       // Size (property)                      ║
║ for (int i=0; i<arr.length; i++) { arr[i] }            ║
║ for (String s : arr) { s }  // Enhanced for             ║
╠═══════════════════════════════════════════════════════════╣
║ ARRAYLIST (Dynamic):                                      ║
║ ArrayList<String> list = new ArrayList<>();              ║
║ list.add("item")        // Add                           ║
║ list.get(0)             // Access                        ║
║ list.set(0, "new")      // Modify                        ║
║ list.remove(0)          // Remove                        ║
║ list.size()             // Size (method)                 ║
║ list.contains("x")      // Check exists                  ║
╠═══════════════════════════════════════════════════════════╣
║ STRINGS (Immutable):                                      ║
║ str.length()            // Length                        ║
║ str.charAt(0)           // Get char                      ║
║ str.substring(0, 3)     // Substring                     ║
║ str.toLowerCase()       // Lowercase                     ║
║ str.toUpperCase()       // Uppercase                     ║
║ str.trim()              // Remove spaces                 ║
║ str.replace("a", "b")   // Replace                       ║
║ str.split(",")          // Split to array               ║
║ str.startsWith("x")     // Check start                   ║
║ str.endsWith("x")       // Check end                     ║
║ str.contains("x")       // Check contains                ║
║ str.indexOf("x")        // Find position                 ║
║ str.equals(str2)        // Compare content ✓            ║
║ str.equalsIgnoreCase()  // Case-insensitive             ║
╠═══════════════════════════════════════════════════════════╣
║ STRINGBUILDER (Mutable):                                  ║
║ StringBuilder sb = new StringBuilder();                   ║
║ sb.append("text")       // Add                           ║
║ sb.insert(5, "x")       // Insert                        ║
║ sb.delete(0, 5)         // Delete                        ║
║ sb.toString()           // Convert to String             ║
╠═══════════════════════════════════════════════════════════╣
║ CRITICAL RULES:                                           ║
║ • Loop condition: i < arr.length (NOT <=)                 ║
║ • Last element: arr[arr.length - 1]                       ║
║ • String compare: str1.equals(str2) (NOT ==)              ║
║ • Null check first: if (str != null && ...)               ║
║ • Array: arr.length | ArrayList: list.size()              ║
║ • Array: arr[i] | ArrayList: list.get(i)                  ║
║ • StringBuilder for loops, String otherwise               ║
╚═══════════════════════════════════════════════════════════╝
```

---

## 🚀 You're Ready for Day 4!

**Prerequisites Checklist:**
- [x] Understand array declaration and access
- [x] Know ArrayList methods
- [x] Comfortable with String methods
- [x] Use `.equals()` for String comparison
- [x] Handle null values properly
- [x] Completed Anagram Checker project

**Confidence Check:**
- Arrays: ___/10
- ArrayList: ___/10
- Strings: ___/10
- StringBuilder: ___/10
- Overall readiness: ___/10

**If any rating is below 6:** Review the Concept Guide and practice exercises again.

**If all ratings are 6+:** You're ready for OOP! Day 4 will transform how you think about code organization! 🎉

---

## 💾 Save This Cheat Sheet

**Ways to use this:**
1. **Print it** - Keep next to keyboard
2. **Bookmark it** - Quick reference while coding
3. **Review it** - Before Day 4
4. **Test yourself** - Cover right side, recall syntax

**Pro Tip:** By Day 7, you should be able to write array and string operations from memory. That's when you know it's muscle memory!

---

**END OF DAY 3** ✅

Tomorrow: Day 4 - OOP Part 1 (Classes, Objects, Constructors, Methods, BankAccount Project)

---
