# DAY 7 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As a SDET, you'll spend significant time debugging failed tests and investigating production issues. Exception handling bugs are among the most common in test automation. These challenges will sharpen your ability to:
- Identify exception-related bugs quickly
- Understand collection-related errors
- Fix NullPointerException issues
- Debug ClassCastException problems
- Handle concurrent modification errors

**Real-world relevance:** In Selenium automation, you'll encounter these exact errors when handling WebElements, test data, and page objects. Mastering these now will save hours of debugging later.

---

## Challenge 1: NullPointerException

**Broken Code:**
```java
import java.util.ArrayList;

public class DebugChallenge1 {
    public static void main(String[] args) {
        ArrayList<String> browsers = null;

        browsers.add("Chrome");
        browsers.add("Firefox");

        System.out.println("Total browsers: " + browsers.size());

        for (String browser : browsers) {
            System.out.println("Browser: " + browser);
        }
    }
}
```

**Error Message:**
```
Exception in thread "main" java.lang.NullPointerException
    at DebugChallenge1.main(DebugChallenge1.java:7)
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Look at how the ArrayList is initialized
- What does `null` mean?
- What must you do before using a collection?

---

**Solution:**

**Bug Location:** Line 6 - `ArrayList<String> browsers = null;`

**Explanation:**
The ArrayList is declared but not initialized. It's set to `null`, which means it doesn't point to any object. When we try to call `.add()` on a null reference, Java throws NullPointerException. You cannot call methods on a null object.

**Fixed Code:**
```java
import java.util.ArrayList;

public class DebugChallenge1 {
    public static void main(String[] args) {
        // FIX: Initialize the ArrayList instead of setting it to null
        ArrayList<String> browsers = new ArrayList<>();

        browsers.add("Chrome");
        browsers.add("Firefox");

        System.out.println("Total browsers: " + browsers.size());

        for (String browser : browsers) {
            System.out.println("Browser: " + browser);
        }
    }
}
```

**Key Learning:**
Always initialize collections before use. `ArrayList<String> list = new ArrayList<>()` creates an empty list ready for use. Setting to `null` means "no object exists." This is the #1 cause of NullPointerException in Java and Selenium automation.

**Selenium Context:**
```java
// Common mistake in Selenium
List<WebElement> elements = null;
elements = driver.findElements(By.className("item")); // If this fails, elements stays null
elements.get(0).click(); // NullPointerException if driver.findElements failed!

// Better approach
List<WebElement> elements = driver.findElements(By.className("item"));
if (!elements.isEmpty()) {
    elements.get(0).click();
}
```

---

## Challenge 2: IndexOutOfBoundsException

**Broken Code:**
```java
import java.util.ArrayList;

public class DebugChallenge2 {
    public static void main(String[] args) {
        ArrayList<String> testCases = new ArrayList<>();
        testCases.add("LoginTest");
        testCases.add("SearchTest");
        testCases.add("CheckoutTest");

        System.out.println("First test: " + testCases.get(0));
        System.out.println("Second test: " + testCases.get(1));
        System.out.println("Third test: " + testCases.get(2));
        System.out.println("Fourth test: " + testCases.get(3));
    }
}
```

**Error Message:**
```
First test: LoginTest
Second test: SearchTest
Third test: CheckoutTest
Exception in thread "main" java.lang.IndexOutOfBoundsException: Index 3 out of bounds for length 3
    at DebugChallenge2.main(DebugChallenge2.java:13)
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Count how many elements were added
- What are valid indices for this ArrayList?
- Remember: indices start at 0!

---

**Solution:**

**Bug Location:** Line 13 - `testCases.get(3)`

**Explanation:**
The ArrayList contains 3 elements at indices 0, 1, and 2. When we try to access index 3, we're asking for the 4th element, which doesn't exist. ArrayList indices are zero-based, so:
- Index 0 = "LoginTest"
- Index 1 = "SearchTest"
- Index 2 = "CheckoutTest"
- Index 3 = doesn't exist!

**Fixed Code:**
```java
import java.util.ArrayList;

public class DebugChallenge2 {
    public static void main(String[] args) {
        ArrayList<String> testCases = new ArrayList<>();
        testCases.add("LoginTest");
        testCases.add("SearchTest");
        testCases.add("CheckoutTest");

        System.out.println("First test: " + testCases.get(0));
        System.out.println("Second test: " + testCases.get(1));
        System.out.println("Third test: " + testCases.get(2));

        // FIX: Only access valid indices (0 to size-1)
        // If you need a 4th element, add it first
        if (testCases.size() > 3) {
            System.out.println("Fourth test: " + testCases.get(3));
        } else {
            System.out.println("Only " + testCases.size() + " tests available");
        }
    }
}
```

**Better Approach Using Loop:**
```java
// Safe way to iterate - no hardcoded indices
for (int i = 0; i < testCases.size(); i++) {
    System.out.println("Test " + (i + 1) + ": " + testCases.get(i));
}
```

**Key Learning:**
Always check collection size before accessing by index. Valid indices are 0 to (size - 1). Use loops or check bounds to avoid this error. In automation, WebElement lists from Selenium can have varying sizes based on page state.

---

## Challenge 3: ConcurrentModificationException

**Broken Code:**
```java
import java.util.ArrayList;

public class DebugChallenge3 {
    public static void main(String[] args) {
        ArrayList<String> testData = new ArrayList<>();
        testData.add("ValidData1");
        testData.add("InvalidData");
        testData.add("ValidData2");
        testData.add("InvalidData");
        testData.add("ValidData3");

        System.out.println("Cleaning test data...");

        // Remove all invalid data
        for (String data : testData) {
            if (data.equals("InvalidData")) {
                testData.remove(data);
            }
        }

        System.out.println("Clean data: " + testData);
    }
}
```

**Error Message:**
```
Cleaning test data...
Exception in thread "main" java.util.ConcurrentModificationException
    at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:...)
    at java.util.ArrayList$Itr.next(ArrayList.java:...)
    at DebugChallenge3.main(DebugChallenge3.java:14)
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- What happens when you modify a collection while iterating it?
- Can you change a list while a for-each loop is running on it?
- What's the safe way to remove during iteration?

---

**Solution:**

**Bug Location:** Line 17 - Removing from ArrayList during for-each loop

**Explanation:**
You cannot modify (add/remove) a collection while iterating through it with a for-each loop. When you call `testData.remove(data)`, it modifies the underlying collection, but the iterator (used by for-each) doesn't know about this change. This causes ConcurrentModificationException. The collection and iterator are out of sync.

**Fixed Code - Solution 1 (Using Iterator):**
```java
import java.util.ArrayList;
import java.util.Iterator;

public class DebugChallenge3 {
    public static void main(String[] args) {
        ArrayList<String> testData = new ArrayList<>();
        testData.add("ValidData1");
        testData.add("InvalidData");
        testData.add("ValidData2");
        testData.add("InvalidData");
        testData.add("ValidData3");

        System.out.println("Cleaning test data...");

        // FIX: Use Iterator.remove() for safe removal
        Iterator<String> iterator = testData.iterator();
        while (iterator.hasNext()) {
            String data = iterator.next();
            if (data.equals("InvalidData")) {
                iterator.remove(); // Safe removal
            }
        }

        System.out.println("Clean data: " + testData);
    }
}
```

**Fixed Code - Solution 2 (Traditional For Loop Backwards):**
```java
// Alternative: Loop backwards to avoid index shifting issues
for (int i = testData.size() - 1; i >= 0; i--) {
    if (testData.get(i).equals("InvalidData")) {
        testData.remove(i);
    }
}
```

**Fixed Code - Solution 3 (Create New List):**
```java
// Alternative: Create a new filtered list
ArrayList<String> cleanData = new ArrayList<>();
for (String data : testData) {
    if (!data.equals("InvalidData")) {
        cleanData.add(data);
    }
}
testData = cleanData;
```

**Key Learning:**
Never modify a collection during for-each iteration. Use Iterator.remove(), loop backwards with index, or create a new collection. This is crucial in Selenium when filtering WebElement lists based on criteria.

---

## Challenge 4: Missing Exception Handling

**Broken Code:**
```java
import java.util.HashMap;

public class DebugChallenge4 {

    public static void processTestConfig(HashMap<String, String> config) {
        String timeout = config.get("timeout");
        int timeoutValue = Integer.parseInt(timeout);
        System.out.println("Timeout configured: " + timeoutValue + " seconds");
    }

    public static void main(String[] args) {
        HashMap<String, String> config1 = new HashMap<>();
        config1.put("browser", "chrome");
        config1.put("timeout", "10");
        processTestConfig(config1);

        HashMap<String, String> config2 = new HashMap<>();
        config2.put("browser", "firefox");
        // Missing timeout key!
        processTestConfig(config2);

        HashMap<String, String> config3 = new HashMap<>();
        config3.put("browser", "edge");
        config3.put("timeout", "invalid"); // Invalid number!
        processTestConfig(config3);
    }
}
```

**Error Message:**
```
Timeout configured: 10 seconds
Exception in thread "main" java.lang.NullPointerException
    at DebugChallenge4.processTestConfig(DebugChallenge4.java:7)
    at DebugChallenge4.main(DebugChallenge4.java:20)
```

**Your Task:**
1. Identify all potential bugs
2. Explain why they fail
3. Fix with proper exception handling

**Hints:**
- What happens when a HashMap key doesn't exist?
- What if the timeout value isn't a valid number?
- How should you handle these scenarios?

---

**Solution:**

**Bug Locations:**
1. Line 6: `config.get("timeout")` returns `null` if key doesn't exist
2. Line 7: `Integer.parseInt(timeout)` throws `NumberFormatException` if timeout is not a valid number
3. No null checking before using the value

**Explanation:**
When `config.get("timeout")` is called but "timeout" key doesn't exist, it returns `null`. Then `Integer.parseInt(null)` throws NullPointerException. Even if the key exists, if the value is "invalid", parseInt throws NumberFormatException. The code needs defensive checks and exception handling.

**Fixed Code:**
```java
import java.util.HashMap;

public class DebugChallenge4 {

    public static void processTestConfig(HashMap<String, String> config) {
        try {
            // FIX 1: Check if key exists and value is not null
            if (!config.containsKey("timeout")) {
                System.out.println("Warning: timeout not configured, using default: 10");
                return;
            }

            String timeout = config.get("timeout");

            // FIX 2: Check for null value
            if (timeout == null || timeout.isEmpty()) {
                System.out.println("Warning: timeout is empty, using default: 10");
                return;
            }

            // FIX 3: Handle NumberFormatException
            int timeoutValue = Integer.parseInt(timeout);
            System.out.println("Timeout configured: " + timeoutValue + " seconds");

        } catch (NumberFormatException e) {
            System.out.println("Error: Invalid timeout value '" + config.get("timeout") +
                             "'. Must be a number. Using default: 10");
        } catch (Exception e) {
            System.out.println("Unexpected error: " + e.getMessage());
        }
    }

    public static void main(String[] args) {
        HashMap<String, String> config1 = new HashMap<>();
        config1.put("browser", "chrome");
        config1.put("timeout", "10");
        processTestConfig(config1);

        HashMap<String, String> config2 = new HashMap<>();
        config2.put("browser", "firefox");
        // Missing timeout key!
        processTestConfig(config2);

        HashMap<String, String> config3 = new HashMap<>();
        config3.put("browser", "edge");
        config3.put("timeout", "invalid"); // Invalid number!
        processTestConfig(config3);
    }
}
```

**Output:**
```
Timeout configured: 10 seconds
Warning: timeout not configured, using default: 10
Error: Invalid timeout value 'invalid'. Must be a number. Using default: 10
```

**Key Learning:**
Always validate data from collections before using it:
1. Check if key exists with `containsKey()`
2. Check if value is null
3. Handle parsing exceptions with try-catch
4. Provide meaningful error messages and defaults

This is essential in test automation when reading configuration files or test data.

---

## Challenge 5: Logic Bug - No Compiler Error

**Code:**
```java
import java.util.ArrayList;
import java.util.HashSet;

public class DebugChallenge5 {
    public static void main(String[] args) {
        // Test case: Remove duplicate test IDs
        ArrayList<String> testIds = new ArrayList<>();
        testIds.add("TEST-001");
        testIds.add("TEST-002");
        testIds.add("TEST-001"); // Duplicate
        testIds.add("TEST-003");
        testIds.add("TEST-002"); // Duplicate

        System.out.println("Original test IDs: " + testIds);
        System.out.println("Count: " + testIds.size());

        // Remove duplicates using HashSet
        HashSet<String> uniqueIds = new HashSet<>();
        for (String id : testIds) {
            uniqueIds.add(id);
        }

        System.out.println("\nUnique test IDs: " + uniqueIds);
        System.out.println("Count: " + uniqueIds.size());

        // Display in original order
        System.out.println("\nFinal ordered unique IDs:");
        for (String id : uniqueIds) {
            System.out.println(id);
        }
    }
}
```

**Expected Output:**
```
Original test IDs: [TEST-001, TEST-002, TEST-001, TEST-003, TEST-002]
Count: 5

Unique test IDs: [TEST-001, TEST-002, TEST-003]
Count: 3

Final ordered unique IDs:
TEST-001
TEST-002
TEST-003
```

**Actual Output:**
```
Original test IDs: [TEST-001, TEST-002, TEST-001, TEST-003, TEST-002]
Count: 5

Unique test IDs: [TEST-002, TEST-001, TEST-003]
Count: 3

Final ordered unique IDs:
TEST-002
TEST-001
TEST-003
```

**Your Task:** Find and fix the logic error - order is not preserved!

**Hints:**
- HashSet doesn't maintain insertion order
- What collection maintains both uniqueness AND order?
- Think about LinkedHashSet

---

**Solution:**

**Bug:** Using `HashSet` which doesn't maintain insertion order. The unique IDs should appear in the order they were first encountered.

**Explanation:**
HashSet doesn't guarantee any particular order. When you add elements to a HashSet, they may come out in any order. If you need both uniqueness (no duplicates) AND insertion order, you should use LinkedHashSet instead.

**Fixed Code:**
```java
import java.util.ArrayList;
import java.util.LinkedHashSet;

public class DebugChallenge5 {
    public static void main(String[] args) {
        // Test case: Remove duplicate test IDs
        ArrayList<String> testIds = new ArrayList<>();
        testIds.add("TEST-001");
        testIds.add("TEST-002");
        testIds.add("TEST-001"); // Duplicate
        testIds.add("TEST-003");
        testIds.add("TEST-002"); // Duplicate

        System.out.println("Original test IDs: " + testIds);
        System.out.println("Count: " + testIds.size());

        // FIX: Use LinkedHashSet to maintain insertion order
        LinkedHashSet<String> uniqueIds = new LinkedHashSet<>();
        for (String id : testIds) {
            uniqueIds.add(id);
        }

        System.out.println("\nUnique test IDs: " + uniqueIds);
        System.out.println("Count: " + uniqueIds.size());

        // Display in original order
        System.out.println("\nFinal ordered unique IDs:");
        for (String id : uniqueIds) {
            System.out.println(id);
        }

        // Alternative: Convert back to ArrayList if needed
        ArrayList<String> orderedUniqueList = new ArrayList<>(uniqueIds);
        System.out.println("\nAs ArrayList: " + orderedUniqueList);
    }
}
```

**Output:**
```
Original test IDs: [TEST-001, TEST-002, TEST-001, TEST-003, TEST-002]
Count: 5

Unique test IDs: [TEST-001, TEST-002, TEST-003]
Count: 3

Final ordered unique IDs:
TEST-001
TEST-002
TEST-003

As ArrayList: [TEST-001, TEST-002, TEST-003]
```

**Key Learning:**
Choose the right collection for your needs:
- `HashSet` - Fast, no duplicates, no order
- `LinkedHashSet` - No duplicates, maintains insertion order
- `TreeSet` - No duplicates, sorted order
- `ArrayList` - Order maintained, allows duplicates

In test automation, when you need to remove duplicates from test data while preserving order, use LinkedHashSet!

---

## 🎯 Debugging Checklist

When you encounter errors in your code, follow this systematic approach:

### NullPointerException
- [ ] Is the object initialized? (not null)
- [ ] Did I create the object with `new`?
- [ ] Could a method return null?
- [ ] Did I check for null before using?

### IndexOutOfBoundsException
- [ ] Did I check the collection size?
- [ ] Are my indices 0-based?
- [ ] Am I accessing index >= size?
- [ ] Should I use a loop instead of hardcoded indices?

### ConcurrentModificationException
- [ ] Am I modifying a collection during iteration?
- [ ] Should I use Iterator.remove()?
- [ ] Can I loop backwards instead?
- [ ] Should I create a new collection?

### NumberFormatException
- [ ] Is my string actually a valid number?
- [ ] Did I validate input before parsing?
- [ ] Should I use try-catch?
- [ ] Do I have a default value?

### Logic Errors (Wrong Output)
- [ ] Am I using the right collection type?
- [ ] Does my collection maintain order?
- [ ] Are duplicates being handled correctly?
- [ ] Did I test with edge cases?

---

## 💡 Pro Debugging Tips

**Tip 1: Use Meaningful Error Messages**
```java
// Bad
catch (Exception e) { }

// Good
catch (Exception e) {
    System.out.println("Error processing test data: " + e.getMessage());
    e.printStackTrace();
}
```

**Tip 2: Add Defensive Checks**
```java
// Validate before using
if (list != null && !list.isEmpty()) {
    list.get(0).process();
}
```

**Tip 3: Use IDE Debugger**
- Set breakpoints at the error line
- Inspect variable values
- Step through code line by line
- Check collection contents

**Tip 4: Print Debug Information**
```java
System.out.println("DEBUG: Collection size: " + list.size());
System.out.println("DEBUG: Value: " + value);
```

**Tip 5: Test Edge Cases**
- Empty collections
- Null values
- Invalid data
- Boundary conditions

---

## 🚀 Challenge Complete!

You've debugged 5 common exception and collection errors. These skills are essential for:
- Debugging failed Selenium tests
- Handling WebDriver exceptions
- Managing test data collections
- Building robust test frameworks

**What You've Mastered:**
- NullPointerException prevention
- IndexOutOfBoundsException handling
- ConcurrentModificationException avoidance
- Exception handling best practices
- Collection type selection

**Next Step:** Apply these debugging skills in the Student Management System project!

---
