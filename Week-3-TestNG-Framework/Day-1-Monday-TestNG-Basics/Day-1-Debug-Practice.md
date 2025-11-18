# DAY 1 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As a SDET, you'll spend significant time debugging:
- Test failures in CI/CD pipelines
- Framework issues in existing automation suites
- Integration problems between TestNG and Selenium
- Flaky tests that pass/fail intermittently

Understanding common TestNG errors helps you:
- Quickly identify root causes
- Fix issues without asking for help
- Write better code that prevents these errors
- Debug teammates' code efficiently

---

## Challenge 1: Missing Import

**Broken Code:**
```java
import org.testng.annotations.Test;

public class LoginTest {

    @Test
    public void testLogin() {
        String username = "admin";
        String password = "pass123";

        assertEquals(username, "admin");
        assertEquals(password, "pass123");
    }
}
```

**Error Message:**
```
Error: cannot find symbol
  symbol:   method assertEquals(String,String)
  location: class LoginTest
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- The error says "cannot find symbol" for assertEquals
- What class provides the assertEquals method in TestNG?

---

**Solution:**

**Bug Location:** Missing import statement for TestNG Assert class

**Explanation:** The code uses `assertEquals()` method but doesn't import the `org.testng.Assert` class. In Java, you must explicitly import classes before using their static methods. Unlike Python where you might have wildcard imports, Java requires specific imports.

**Fixed Code:**
```java
import org.testng.annotations.Test;
import org.testng.Assert;  // Added this import

public class LoginTest {

    @Test
    public void testLogin() {
        String username = "admin";
        String password = "pass123";

        Assert.assertEquals(username, "admin");  // Now works!
        Assert.assertEquals(password, "pass123");
    }
}
```

**Key Learning:** Always import `org.testng.Assert` when using assertions. This is one of the most common beginner mistakes when transitioning from Python.

---

## Challenge 2: Wrong Annotation Order

**Broken Code:**
```java
import org.testng.annotations.*;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GoogleTest {
    WebDriver driver;

    @AfterMethod
    public void teardown() {
        driver.quit();
        System.out.println("Browser closed");
    }

    @Test
    public void testGoogle() {
        driver.get("https://www.google.com");
        System.out.println("Opened Google");
    }

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        System.out.println("Browser opened");
    }
}
```

**Error Message:**
```
java.lang.NullPointerException
    at GoogleTest.testGoogle(GoogleTest.java:16)
    at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    ...
```

**Your Task:**
1. Why does this cause a NullPointerException?
2. Does annotation order in the code matter?
3. Fix the issue

**Hints:**
- Look at when driver is initialized vs when it's used
- TestNG annotations execute based on their type, not their position in the file

---

**Solution:**

**Bug Location:** This is actually a **trick question** - there's no real bug!

**Explanation:**
- TestNG executes annotations based on their **type**, not their order in the source code
- Even though `@AfterMethod` appears before `@BeforeMethod` in the code, TestNG will correctly execute `@BeforeMethod` first
- However, the NullPointerException suggests the driver is null when used

**Actual Problem:** The code shown would work fine. But if you see this error, it means `@BeforeMethod` didn't run. Possible causes:
1. Method is not public
2. TestNG dependency not properly configured
3. Test runner not recognizing annotations

**Fixed Code (with best practice organization):**
```java
import org.testng.annotations.*;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class GoogleTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        System.out.println("Browser opened");
    }

    @Test
    public void testGoogle() {
        driver.get("https://www.google.com");
        System.out.println("Opened Google");
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) {  // Added null check
            driver.quit();
            System.out.println("Browser closed");
        }
    }
}
```

**Key Learning:**
- Annotation order in code doesn't affect execution order
- But organizing code logically (setup → test → teardown) improves readability
- Always add null checks in cleanup methods
- TestNG determines execution order by annotation type

---

## Challenge 3: Assertion Parameter Order Confusion

**Broken Code:**
```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class CalculatorTest {

    @Test
    public void testAddition() {
        int expected = 10;
        int actual = 5 + 5;

        // This looks correct but fails with confusing message
        Assert.assertEquals(expected, actual);
    }
}
```

**Error Message:**
```
java.lang.AssertionError: expected [5] but found [10]
Expected :5
Actual   :10
```

**Your Task:**
1. Why is the error message backwards?
2. What's the correct parameter order?
3. Fix it

**Hints:**
- Read the TestNG documentation for assertEquals
- Compare with JUnit parameter order

---

**Solution:**

**Bug Location:** Parameters passed in wrong order to `assertEquals()`

**Explanation:**
TestNG's `Assert.assertEquals()` expects: `assertEquals(actual, expected)`

This is **OPPOSITE** of JUnit which expects: `assertEquals(expected, actual)`

The code passed `expected` first, so TestNG thought:
- actual value = 10 (first parameter)
- expected value = 5 (second parameter)
- Hence error: "expected [5] but found [10]"

**Fixed Code:**
```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class CalculatorTest {

    @Test
    public void testAddition() {
        int expected = 10;
        int actual = 5 + 5;

        // Correct order: actual first, then expected
        Assert.assertEquals(actual, expected, "Addition should equal 10");
    }
}
```

**Memory Aid:**
```java
// TestNG (what you're learning)
Assert.assertEquals(actual, expected);

// JUnit (for comparison)
assertEquals(expected, actual);

// Remember: TestNG = "Actual first, Expected second" = A before E alphabetically
```

**Key Learning:**
- **TestNG**: `Assert.assertEquals(actual, expected)`
- **JUnit**: `assertEquals(expected, actual)`
- Always include a message as third parameter to avoid confusion
- This is a critical difference that trips up many developers

---

## Challenge 4: Test Method Not Executing

**Broken Code:**
```java
import org.testng.annotations.Test;

public class SearchTest {

    @Test
    private void testSearch() {
        System.out.println("Searching...");
    }

    @Test
    void testFilter() {
        System.out.println("Filtering...");
    }
}
```

**Error/Observation:**
```
No tests executed
TestNG didn't find any tests to run
```

**Your Task:**
1. Why aren't the tests executing?
2. What's required for TestNG to recognize a test method?
3. Fix it

**Hints:**
- Check the method access modifiers
- What does "public" mean in Java?

---

**Solution:**

**Bug Location:** Test methods are not public

**Explanation:**
TestNG requires all test methods to be **public**. The code has:
- `private void testSearch()` - TestNG cannot access private methods
- `void testFilter()` - Package-private (default), also not accessible

In Python, you don't worry about access modifiers. But in Java, they're crucial:
- `private` - only accessible within the class
- `package-private` (no modifier) - accessible within same package
- `public` - accessible everywhere (required for TestNG)

**Fixed Code:**
```java
import org.testng.annotations.Test;

public class SearchTest {

    @Test
    public void testSearch() {  // Added 'public'
        System.out.println("Searching...");
    }

    @Test
    public void testFilter() {  // Added 'public'
        System.out.println("Filtering...");
    }
}
```

**Key Learning:**
- All `@Test` methods MUST be `public`
- All `@BeforeMethod`, `@AfterMethod` etc. must also be `public`
- TestNG uses reflection to discover and execute tests
- Private/package-private methods are invisible to TestNG

---

## Challenge 5: Logic Bug (No Error Message)

**Code:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;

public class TitleTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
    }

    @Test
    public void verifyGoogleTitle() {
        driver.get("https://www.google.com");
        String title = driver.getTitle();

        // This assertion passes but it shouldn't!
        Assert.assertTrue(title.equals("Google"));
    }

    @AfterMethod
    public void teardown() {
        driver.quit();
    }
}
```

**Expected Behavior:** Test should verify title equals "Google"
**Actual Behavior:** Test always passes, even when title is different

**Your Task:** Find and fix the logic error

---

**Solution:**

**Bug Location:** Unnecessary use of `.equals()` inside `assertTrue()`

**Explanation:**
The code works, but it's not the best approach. The issue is subtle:

```java
Assert.assertTrue(title.equals("Google"));
```

This passes if title equals "Google", but the error message would be:
```
java.lang.AssertionError: expected [true] but found [false]
```
This doesn't tell you what the actual title was!

**Better Approach:**
```java
Assert.assertEquals(title, "Google", "Title should be 'Google'");
```

Error message with assertEquals:
```
java.lang.AssertionError: Title should be 'Google'
Expected :Google
Actual   :Google Search
```

**Fixed Code:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;

public class TitleTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
    }

    @Test
    public void verifyGoogleTitle() {
        driver.get("https://www.google.com");
        String title = driver.getTitle();

        // Better: Shows actual vs expected in failure message
        Assert.assertEquals(title, "Google",
            "Expected title 'Google' but got: " + title);
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

**Key Learning:**
- Use `assertEquals()` for comparing values (better error messages)
- Use `assertTrue()` for boolean conditions
- Always include descriptive failure messages
- Consider what information you'll need when debugging failures

---

## Challenge 6: Forgotten assertAll() with Soft Assertions

**Broken Code:**
```java
import org.testng.annotations.Test;
import org.testng.asserts.SoftAssert;

public class FormValidationTest {

    @Test
    public void validateRegistrationForm() {
        SoftAssert softAssert = new SoftAssert();

        String username = "";
        String email = "notanemail";
        String password = "123";  // Too short

        softAssert.assertFalse(username.isEmpty(), "Username should not be empty");
        softAssert.assertTrue(email.contains("@"), "Email should contain @");
        softAssert.assertTrue(password.length() >= 8, "Password should be 8+ characters");

        System.out.println("All validations complete!");
        // Missing something important here...
    }
}
```

**Problem:** Test passes even though all assertions failed!

**Your Task:**
1. Why does the test pass when assertions failed?
2. What's missing?
3. Fix it

**Hints:**
- Soft assertions need a final step
- What makes soft assertions different from hard assertions?

---

**Solution:**

**Bug Location:** Missing `softAssert.assertAll()` at the end

**Explanation:**
Soft assertions collect all failures but don't fail the test automatically. You MUST call `assertAll()` at the end to actually mark the test as failed.

Without `assertAll()`:
- Assertions are evaluated
- Failures are logged internally
- But test is marked as PASSED
- Defeats the purpose of testing!

**Fixed Code:**
```java
import org.testng.annotations.Test;
import org.testng.asserts.SoftAssert;

public class FormValidationTest {

    @Test
    public void validateRegistrationForm() {
        SoftAssert softAssert = new SoftAssert();

        String username = "";
        String email = "notanemail";
        String password = "123";  // Too short

        softAssert.assertFalse(username.isEmpty(), "Username should not be empty");
        softAssert.assertTrue(email.contains("@"), "Email should contain @");
        softAssert.assertTrue(password.length() >= 8, "Password should be 8+ characters");

        System.out.println("All validations complete!");

        // CRITICAL: Must call assertAll() to fail the test if any assertion failed
        softAssert.assertAll();
    }
}
```

**Now the test fails with:**
```
java.lang.AssertionError: The following asserts failed:
	Username should not be empty,
	Email should contain @,
	Password should be 8+ characters
```

**Key Learning:**
- Soft assertions need `assertAll()` at the end
- Without it, test passes even with failures
- Common mistake in code reviews
- Pattern: Create SoftAssert → Use it → Call assertAll()

---

## 🎯 Debugging Best Practices

**1. Read Error Messages Carefully**
```
Before jumping to solutions, understand:
- What line failed?
- What was expected vs actual?
- What type of error? (NullPointer, Assertion, Compilation)
```

**2. Common TestNG Error Patterns**
```java
// NullPointerException → Check @BeforeMethod ran
// Cannot find symbol → Missing import
// No tests found → Method not public or missing @Test
// Test passes unexpectedly → Forgot assertAll() for soft assertions
```

**3. Debugging Workflow**
```
1. Read error message and stack trace
2. Identify the line number
3. Check what happened before that line
4. Verify imports and annotations
5. Add print statements to trace execution
6. Run test in debug mode
```

**4. Python vs Java Debugging Mindset**
```python
# Python: Error messages are usually straightforward
assert actual == expected  # Clear error if fails

# Java: Need to understand framework behavior
Assert.assertEquals(actual, expected, "message");
// Error depends on TestNG configuration, assertion type, etc.
```

---

## 💡 Pro Tips

**Tip 1: Add Print Statements in Lifecycle Methods**
```java
@BeforeMethod
public void setup() {
    System.out.println(">>> BEFORE METHOD RUNNING");
    driver = new ChromeDriver();
}

@Test
public void test1() {
    System.out.println(">>> TEST RUNNING");
}

@AfterMethod
public void teardown() {
    System.out.println(">>> AFTER METHOD RUNNING");
}
```

**Tip 2: Use Descriptive Test Method Names**
```java
// Bad - hard to debug from reports
@Test
public void test1() { }

// Good - immediately know what failed
@Test
public void verifyLoginPageTitleAfterNavigation() { }
```

**Tip 3: Always Include Assertion Messages**
```java
// Without message - unhelpful when fails
Assert.assertEquals(actual, expected);

// With message - immediately know the issue
Assert.assertEquals(actual, expected,
    "Login button text should be 'Sign In' but was: " + actual);
```

---

## 🎓 Debug Challenge Summary

| Challenge | Error Type | Key Lesson |
|-----------|------------|------------|
| 1 | Missing Import | Always import `org.testng.Assert` |
| 2 | Annotation Order | Position doesn't matter, type does |
| 3 | Parameter Order | TestNG: actual first, expected second |
| 4 | Access Modifier | Test methods must be public |
| 5 | Wrong Assertion | Use assertEquals for values, assertTrue for booleans |
| 6 | Soft Assert | Must call assertAll() at end |

---

**🔧 Debugging Skills Acquired! Move to Day-1-Project-Guide.md**
