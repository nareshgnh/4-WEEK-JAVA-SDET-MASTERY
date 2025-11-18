# DAY 3 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As an SDET, you'll spend significant time debugging test failures, investigating flaky tests, and fixing code issues. Arrays and strings are common sources of bugs:
- **ArrayIndexOutOfBoundsException** - Most common array error
- **NullPointerException** - Missing null checks on strings
- **Off-by-one errors** - Wrong loop conditions
- **String comparison bugs** - Using `==` instead of `.equals()`
- **Logic errors** - Wrong substring indices or split logic

These challenges simulate real debugging scenarios you'll face in test automation.

**Time Required:** 20 minutes
**Skills Practiced:** Error reading, logical thinking, Java debugging

---

## Challenge 1: ArrayIndexOutOfBoundsException

**Broken Code:**
```java
public class Challenge1 {
    public static void main(String[] args) {
        String[] testCases = {"Login", "Logout", "Search"};

        System.out.println("Executing test suite:");
        for (int i = 0; i <= testCases.length; i++) {
            System.out.println("Running test: " + testCases[i]);
        }

        // Access last test case
        String lastTest = testCases[3];
        System.out.println("Last test: " + lastTest);
    }
}
```

**Error Message:**
```
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 3 out of bounds for length 3
```

**Your Task:**
1. Identify the bug(s)
2. Explain why it's wrong
3. Fix it

**Hints:**
- How many elements are in the array?
- What are the valid indices?
- Check the loop condition carefully

---

**Solution:**

**Bug Locations:**
1. **Line 6**: `i <= testCases.length` - Wrong loop condition
2. **Line 11**: `testCases[3]` - Index out of bounds

**Explanation:**
- Array `testCases` has 3 elements at indices 0, 1, 2
- Loop condition `i <= testCases.length` tries to access index 3 (when i equals 3)
- Valid indices are 0 to `length - 1`, not 0 to `length`
- Direct access to `testCases[3]` is also invalid

**Fixed Code:**
```java
public class Challenge1 {
    public static void main(String[] args) {
        String[] testCases = {"Login", "Logout", "Search"};

        System.out.println("Executing test suite:");
        // Fix 1: Change <= to < to avoid accessing index 3
        for (int i = 0; i < testCases.length; i++) {
            System.out.println("Running test: " + testCases[i]);
        }

        // Fix 2: Use length - 1 to get last element
        String lastTest = testCases[testCases.length - 1];
        System.out.println("Last test: " + lastTest);
    }
}
```

**Key Learning:**
- **Always use `i < array.length`**, not `i <= array.length`
- **Last element** is at index `array.length - 1`
- Arrays are zero-indexed: 3 elements = indices 0, 1, 2

---

## Challenge 2: String Comparison Bug

**Broken Code:**
```java
public class Challenge2 {
    public static void main(String[] args) {
        // Simulating Selenium getTitle() result
        String expectedTitle = "Dashboard";
        String actualTitle = new String("Dashboard");

        // Assertion
        if (actualTitle == expectedTitle) {
            System.out.println("✓ Test Passed: Title matches");
        } else {
            System.out.println("✗ Test Failed: Expected '" + expectedTitle +
                             "' but got '" + actualTitle + "'");
        }

        // Check error message
        String expectedError = "Invalid credentials";
        String actualError = "invalid credentials";

        if (actualError == expectedError) {
            System.out.println("✓ Error message displayed");
        } else {
            System.out.println("✗ Error message not found");
        }
    }
}
```

**Error Message:**
```
No compilation error, but test fails unexpectedly:
✗ Test Failed: Expected 'Dashboard' but got 'Dashboard'
✗ Error message not found
```

**Your Task:**
1. Identify why the test fails even though strings look identical
2. Explain the difference between `==` and `.equals()`
3. Fix the comparison logic

**Hints:**
- What does `==` compare for objects?
- What does `.equals()` compare?
- Is there a case-sensitivity issue?

---

**Solution:**

**Bug Location:** Lines 8 and 19 - Using `==` for String comparison

**Explanation:**
- `==` compares **object references** (memory addresses), not content
- `actualTitle == expectedTitle` compares if both variables point to same object
- Even though content is identical, they're different objects in memory
- String comparison must use `.equals()` or `.equalsIgnoreCase()`

**Fixed Code:**
```java
public class Challenge2 {
    public static void main(String[] args) {
        // Simulating Selenium getTitle() result
        String expectedTitle = "Dashboard";
        String actualTitle = new String("Dashboard");

        // Fix: Use .equals() for content comparison
        if (actualTitle.equals(expectedTitle)) {
            System.out.println("✓ Test Passed: Title matches");
        } else {
            System.out.println("✗ Test Failed: Expected '" + expectedTitle +
                             "' but got '" + actualTitle + "'");
        }

        // Check error message
        String expectedError = "Invalid credentials";
        String actualError = "invalid credentials";

        // Fix: Use .equalsIgnoreCase() for case-insensitive comparison
        if (actualError.equalsIgnoreCase(expectedError)) {
            System.out.println("✓ Error message displayed");
        } else {
            System.out.println("✗ Error message not found");
        }
    }
}
```

**Key Learning:**
- **NEVER use `==` for String comparison in tests**
- Use `.equals()` for exact match
- Use `.equalsIgnoreCase()` for case-insensitive match
- This is the #1 bug in beginner Selenium tests

---

## Challenge 3: NullPointerException with Strings

**Broken Code:**
```java
public class Challenge3 {
    public static void main(String[] args) {
        String[] usernames = {"john@test.com", null, "jane@test.com", ""};
        String[] passwords = {"pass123", "pass456", "pass789", null};

        System.out.println("Validating test credentials:");

        for (int i = 0; i < usernames.length; i++) {
            String username = usernames[i];
            String password = passwords[i];

            // Validate username
            if (username.isEmpty()) {
                System.out.println("Test " + (i+1) + ": Username is empty");
                continue;
            }

            // Validate password length
            if (password.length() < 6) {
                System.out.println("Test " + (i+1) + ": Password too short");
                continue;
            }

            System.out.println("Test " + (i+1) + ": Valid credentials");
        }
    }
}
```

**Error Message:**
```
Exception in thread "main" java.lang.NullPointerException
    at Challenge3.main(Challenge3.java:12)
```

**Your Task:**
1. Identify where NullPointerException occurs
2. Explain why null causes the error
3. Add proper null checks

**Hints:**
- What happens when you call a method on null?
- Which variables could be null?
- Check before calling methods

---

**Solution:**

**Bug Location:** Lines 12 and 18 - Calling methods on potentially null strings

**Explanation:**
- `usernames[1]` is `null` (not an empty string, but null)
- Calling `username.isEmpty()` on null throws NullPointerException
- You cannot call any method on null - must check null first
- Same issue with `password.length()` when password is null

**Fixed Code:**
```java
public class Challenge3 {
    public static void main(String[] args) {
        String[] usernames = {"john@test.com", null, "jane@test.com", ""};
        String[] passwords = {"pass123", "pass456", "pass789", null};

        System.out.println("Validating test credentials:");

        for (int i = 0; i < usernames.length; i++) {
            String username = usernames[i];
            String password = passwords[i];

            // Fix: Check null before calling methods
            if (username == null) {
                System.out.println("Test " + (i+1) + ": Username is null");
                continue;
            }

            if (username.isEmpty()) {
                System.out.println("Test " + (i+1) + ": Username is empty");
                continue;
            }

            // Fix: Check null before calling methods
            if (password == null) {
                System.out.println("Test " + (i+1) + ": Password is null");
                continue;
            }

            if (password.length() < 6) {
                System.out.println("Test " + (i+1) + ": Password too short");
                continue;
            }

            System.out.println("Test " + (i+1) + ": ✓ Valid credentials");
        }
    }
}
```

**Alternative Fix (Constant-First Comparison):**
```java
// For equality checks, put constant/literal first to avoid NPE
if ("expected".equals(actualString)) {  // Safe even if actualString is null
    // ...
}

// Instead of:
if (actualString.equals("expected")) {  // NPE if actualString is null
    // ...
}
```

**Key Learning:**
- **Always check for null** before calling methods on Strings
- Null is not the same as empty string (`""`)
- Use `variable == null` or `variable != null` to check
- Or use constant-first pattern: `"constant".equals(variable)`

---

## Challenge 4: Logic Bug - Wrong Substring Indices

**Broken Code:**
```java
public class Challenge4 {
    public static void main(String[] args) {
        String testUrl = "https://www.example.com/products/laptop";

        // Extract domain (should be "www.example.com")
        int startIndex = testUrl.indexOf("://");
        String domain = testUrl.substring(startIndex, testUrl.indexOf("/products"));

        System.out.println("Domain: " + domain);

        // Extract product (should be "laptop")
        String product = testUrl.substring(testUrl.indexOf("/products"));

        System.out.println("Product: " + product);

        // Extract protocol (should be "https")
        String protocol = testUrl.substring(0, testUrl.indexOf(":"));

        System.out.println("Protocol: " + protocol);
    }
}
```

**Expected Output:**
```
Domain: www.example.com
Product: laptop
Protocol: https
```

**Actual Output:**
```
Domain: ://www.example.com
Product: /products/laptop
Protocol: https
```

**Your Task:**
1. Identify incorrect substring extractions
2. Fix the start/end indices
3. Ensure output matches expected

**Hints:**
- `substring(start, end)` is exclusive of end index
- `indexOf()` returns position of first character
- You may need to add/subtract from indices

---

**Solution:**

**Bug Locations:**
1. **Line 7**: `startIndex` should skip over "://"
2. **Line 11**: Product extraction includes "/products/"

**Explanation:**
- `indexOf("://")` returns 5 (position of ':')
- Need to add 3 to skip "://" → `startIndex + 3`
- Product extraction needs to find "/laptop" not "/products"
- Or extract after last "/" for cleaner solution

**Fixed Code:**
```java
public class Challenge4 {
    public static void main(String[] args) {
        String testUrl = "https://www.example.com/products/laptop";

        // Extract domain - skip over "://"
        int protocolEnd = testUrl.indexOf("://");
        String domain = testUrl.substring(protocolEnd + 3, testUrl.indexOf("/products"));

        System.out.println("Domain: " + domain);

        // Extract product - get text after last "/"
        int lastSlash = testUrl.lastIndexOf("/");
        String product = testUrl.substring(lastSlash + 1);

        System.out.println("Product: " + product);

        // Extract protocol (this was correct)
        String protocol = testUrl.substring(0, testUrl.indexOf(":"));

        System.out.println("Protocol: " + protocol);
    }
}
```

**Output:**
```
Domain: www.example.com
Product: laptop
Protocol: https
```

**Key Learning:**
- `indexOf()` gives you the position, often need to add offset
- `substring(start)` goes to end of string (no end index needed)
- `lastIndexOf()` finds last occurrence, useful for file/path parsing
- Always test with actual data to verify substring boundaries

---

## Challenge 5: ArrayList Modification During Iteration

**Broken Code:**
```java
import java.util.ArrayList;

public class Challenge5 {
    public static void main(String[] args) {
        ArrayList<String> testCases = new ArrayList<>();
        testCases.add("Login Test");
        testCases.add("SKIP: Logout Test");
        testCases.add("Search Test");
        testCases.add("SKIP: Admin Panel Test");
        testCases.add("Checkout Test");

        System.out.println("Removing skipped tests...");

        // Remove tests marked with SKIP
        for (String test : testCases) {
            if (test.startsWith("SKIP:")) {
                testCases.remove(test);
            }
        }

        System.out.println("\nFinal test suite:");
        for (int i = 0; i < testCases.size(); i++) {
            System.out.println((i+1) + ". " + testCases.get(i));
        }
    }
}
```

**Error Message:**
```
Exception in thread "main" java.util.ConcurrentModificationException
    at java.util.ArrayList$Itr.checkForComodification(ArrayList.java:...)
    at java.util.ArrayList$Itr.next(ArrayList.java:...)
```

**Your Task:**
1. Understand why modifying ArrayList during iteration causes error
2. Fix using backward iteration or separate list
3. Verify all SKIP tests are removed

**Hints:**
- Enhanced for loop uses an iterator internally
- Can't modify collection while iterating with enhanced for
- Try iterating backwards, or use traditional for loop with index

---

**Solution:**

**Bug Location:** Line 16 - Removing from ArrayList during enhanced for loop iteration

**Explanation:**
- Enhanced for loop (`for (String test : testCases)`) uses an iterator
- Modifying the list (add/remove) while iterating corrupts the iterator
- This throws `ConcurrentModificationException`
- Cannot use enhanced for loop when modifying the collection

**Fixed Code (Option 1: Backward Iteration):**
```java
import java.util.ArrayList;

public class Challenge5 {
    public static void main(String[] args) {
        ArrayList<String> testCases = new ArrayList<>();
        testCases.add("Login Test");
        testCases.add("SKIP: Logout Test");
        testCases.add("Search Test");
        testCases.add("SKIP: Admin Panel Test");
        testCases.add("Checkout Test");

        System.out.println("Removing skipped tests...");

        // Fix: Iterate backwards to safely remove elements
        for (int i = testCases.size() - 1; i >= 0; i--) {
            if (testCases.get(i).startsWith("SKIP:")) {
                testCases.remove(i);
                System.out.println("Removed: " + testCases.get(i));
            }
        }

        System.out.println("\nFinal test suite:");
        for (int i = 0; i < testCases.size(); i++) {
            System.out.println((i+1) + ". " + testCases.get(i));
        }
    }
}
```

**Fixed Code (Option 2: Separate List):**
```java
import java.util.ArrayList;

public class Challenge5Option2 {
    public static void main(String[] args) {
        ArrayList<String> testCases = new ArrayList<>();
        testCases.add("Login Test");
        testCases.add("SKIP: Logout Test");
        testCases.add("Search Test");
        testCases.add("SKIP: Admin Panel Test");
        testCases.add("Checkout Test");

        System.out.println("Filtering skipped tests...");

        // Fix: Create new list with non-skipped tests
        ArrayList<String> activeTests = new ArrayList<>();
        for (String test : testCases) {
            if (!test.startsWith("SKIP:")) {
                activeTests.add(test);
            }
        }

        System.out.println("\nFinal test suite:");
        for (int i = 0; i < activeTests.size(); i++) {
            System.out.println((i+1) + ". " + activeTests.get(i));
        }
    }
}
```

**Output (Both Options):**
```
Final test suite:
1. Login Test
2. Search Test
3. Checkout Test
```

**Key Learning:**
- **Never modify a collection** while using enhanced for loop
- **Iterate backwards** (from end to start) when removing during iteration
- **Or create a new collection** with filtered results
- This is a common pattern in test suite filtering

---

## 🎯 Debugging Patterns Summary

### Pattern 1: Array Bounds Checking
```java
// ❌ Wrong
for (int i = 0; i <= array.length; i++) {
    array[i] = value;  // Out of bounds when i == length
}

// ✅ Correct
for (int i = 0; i < array.length; i++) {
    array[i] = value;
}
```

### Pattern 2: String Comparison
```java
// ❌ Wrong
if (str1 == str2) { }

// ✅ Correct
if (str1.equals(str2)) { }

// ✅ Case-insensitive
if (str1.equalsIgnoreCase(str2)) { }

// ✅ Null-safe
if ("constant".equals(variable)) { }  // Won't NPE if variable is null
```

### Pattern 3: Null Checking
```java
// ❌ Wrong
if (str.isEmpty()) { }  // NPE if str is null

// ✅ Correct
if (str != null && str.isEmpty()) { }

// ✅ Or reverse check
if ("value".equals(str)) { }  // Safe
```

### Pattern 4: Substring Extraction
```java
String url = "https://www.example.com/page";

// ❌ Wrong - includes delimiters
String domain = url.substring(url.indexOf("://"), ...);  // "://www..."

// ✅ Correct - skip delimiters
String domain = url.substring(url.indexOf("://") + 3, ...);  // "www..."
```

### Pattern 5: ArrayList Modification
```java
ArrayList<String> list = new ArrayList<>();

// ❌ Wrong
for (String item : list) {
    if (condition) {
        list.remove(item);  // ConcurrentModificationException
    }
}

// ✅ Correct - iterate backwards
for (int i = list.size() - 1; i >= 0; i--) {
    if (condition) {
        list.remove(i);
    }
}
```

---

## 🧪 Debugging Best Practices for SDETs

1. **Read Error Messages Carefully**
   - Line number tells you exactly where
   - Exception type tells you what went wrong
   - Stack trace shows the call sequence

2. **Use Print Statements**
   ```java
   System.out.println("DEBUG: i = " + i + ", length = " + array.length);
   ```

3. **Check Edge Cases**
   - Empty arrays/strings
   - Null values
   - First and last elements
   - Single-element collections

4. **Validate Assumptions**
   ```java
   // Don't assume - verify!
   if (array != null && array.length > 0) {
       // Safe to access
   }
   ```

5. **Use IDE Debugger**
   - Set breakpoints
   - Step through code
   - Inspect variable values
   - Watch expressions

---

## ✅ Debugging Skills Self-Check

After completing these challenges:

**I can now:**
- [ ] Identify and fix ArrayIndexOutOfBoundsException
- [ ] Use `.equals()` instead of `==` for String comparison
- [ ] Add null checks before calling String methods
- [ ] Calculate correct substring indices
- [ ] Safely modify ArrayLists during iteration
- [ ] Read and understand Java error messages
- [ ] Apply defensive programming practices

**Confidence Level:**
- Array debugging: ___/10
- String debugging: ___/10
- Null handling: ___/10
- Overall debugging: ___/10

---

## 🚀 Next Step

Move to **Day-3-Project-Guide.md** to build your Anagram Checker project!

**Debugging practice complete:** ✅
**Bugs fixed successfully:** ___/5
**Ready for project:** Yes / Review challenges

---
