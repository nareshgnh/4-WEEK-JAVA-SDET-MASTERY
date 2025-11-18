# DAY 1: TESTNG BASICS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand what TestNG is and why it's preferred over JUnit for Selenium
- [ ] Master core TestNG annotations (@Test, @BeforeMethod, @AfterMethod, etc.)
- [ ] Write assertions for test validation
- [ ] Convert existing Selenium tests to TestNG format
- [ ] Execute TestNG tests and interpret results

**Time Required:** 3 hours
**Difficulty:** Medium
**Prerequisite:** Week 2 Selenium knowledge (you should have basic Selenium tests)

---

## 📚 Core Concepts

### Concept 1: What is TestNG?

**What is it?**
TestNG (Test Next Generation) is a testing framework for Java that provides powerful features for organizing, executing, and reporting tests. It's specifically designed to cover all categories of tests: unit, functional, end-to-end, integration, etc.

**Why does it matter for automation?**
In test automation, especially with Selenium, you need:
- **Organized test execution** (setup/teardown, test grouping)
- **Parallel execution** (run tests faster)
- **Data-driven testing** (test with multiple datasets)
- **Detailed reporting** (know what passed/failed and why)
- **Test dependencies** (one test depends on another)

TestNG provides all of this out-of-the-box, making it the industry standard for Selenium automation.

**Syntax:**
```java
import org.testng.annotations.Test;

public class MyFirstTest {
    @Test
    public void testMethod() {
        // Test code here
        System.out.println("My first TestNG test!");
    }
}
```

**Explanation:**
- `import org.testng.annotations.Test;` - Imports the @Test annotation
- `@Test` - Marks a method as a test case
- `public void testMethod()` - The actual test method (must be public, returns void)
- TestNG will automatically discover and execute all @Test methods

**Real Example:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.Test;

public class GoogleSearchTest {
    @Test
    public void verifyGoogleTitle() {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.google.com");

        String actualTitle = driver.getTitle();
        Assert.assertEquals(actualTitle, "Google");

        driver.quit();
    }
}
```

---

### Concept 2: TestNG Annotations Lifecycle

**What is it?**
Annotations in TestNG control the flow of test execution. They define what happens before/after tests, classes, and suites.

**Why does it matter for automation?**
In real automation frameworks:
- You need to **start the browser before each test** (@BeforeMethod)
- You need to **close the browser after each test** (@AfterMethod)
- You might **set up test data once** before all tests (@BeforeClass)
- You need **clean up resources** after everything (@AfterClass)

**Common Use Cases in Test Automation:**
1. **Browser Management** - Start/close browser for each test
2. **Test Data Setup** - Load test data once before all tests
3. **Login/Logout** - Perform login before test, logout after
4. **Database Connections** - Open connection before suite, close after

**Annotation Hierarchy:**
```
@BeforeSuite
    @BeforeTest
        @BeforeClass
            @BeforeMethod
                @Test
            @AfterMethod
        @AfterClass
    @AfterTest
@AfterSuite
```

**Syntax:**
```java
import org.testng.annotations.*;

public class TestNGLifecycle {

    @BeforeSuite
    public void beforeSuite() {
        System.out.println("1. @BeforeSuite - Runs once before entire suite");
    }

    @BeforeTest
    public void beforeTest() {
        System.out.println("2. @BeforeTest - Runs before <test> tag in testng.xml");
    }

    @BeforeClass
    public void beforeClass() {
        System.out.println("3. @BeforeClass - Runs once before first test method in class");
    }

    @BeforeMethod
    public void beforeMethod() {
        System.out.println("4. @BeforeMethod - Runs before each @Test method");
    }

    @Test
    public void test1() {
        System.out.println("5. @Test - Test case 1");
    }

    @Test
    public void test2() {
        System.out.println("5. @Test - Test case 2");
    }

    @AfterMethod
    public void afterMethod() {
        System.out.println("6. @AfterMethod - Runs after each @Test method");
    }

    @AfterClass
    public void afterClass() {
        System.out.println("7. @AfterClass - Runs once after all tests in class");
    }

    @AfterTest
    public void afterTest() {
        System.out.println("8. @AfterTest - Runs after <test> tag");
    }

    @AfterSuite
    public void afterSuite() {
        System.out.println("9. @AfterSuite - Runs once after entire suite");
    }
}
```

**Execution Order:**
```
1. @BeforeSuite
2. @BeforeTest
3. @BeforeClass
4. @BeforeMethod
5. @Test - test1
6. @AfterMethod
4. @BeforeMethod
5. @Test - test2
6. @AfterMethod
7. @AfterClass
8. @AfterTest
9. @AfterSuite
```

---

### Concept 3: Assertions in TestNG

**What is it?**
Assertions are verification points in your tests. They check if expected results match actual results. If assertion fails, test fails.

**Why does it matter for automation?**
Without assertions, your test would just perform actions but never verify if they worked correctly. Assertions are what make a test script an actual "test".

**Syntax:**
```java
import org.testng.Assert;

// Hard Assertions (test stops if assertion fails)
Assert.assertEquals(actual, expected);                    // Values must be equal
Assert.assertTrue(condition);                             // Condition must be true
Assert.assertFalse(condition);                            // Condition must be false
Assert.assertNotNull(object);                             // Object must not be null
Assert.assertNull(object);                                // Object must be null
Assert.fail("Test failed intentionally");                 // Force test to fail

// With custom messages
Assert.assertEquals(actual, expected, "Title mismatch!");
Assert.assertTrue(isDisplayed, "Element not visible!");
```

**Common Selenium Assertions:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.testng.Assert;

// 1. Verify page title
String actualTitle = driver.getTitle();
Assert.assertEquals(actualTitle, "Expected Title", "Page title doesn't match!");

// 2. Verify element is displayed
WebElement loginButton = driver.findElement(By.id("login"));
Assert.assertTrue(loginButton.isDisplayed(), "Login button not visible!");

// 3. Verify element text
String actualText = element.getText();
Assert.assertEquals(actualText, "Welcome", "Welcome message not found!");

// 4. Verify URL contains specific text
String currentUrl = driver.getCurrentUrl();
Assert.assertTrue(currentUrl.contains("dashboard"), "Not on dashboard page!");

// 5. Verify element is enabled
Assert.assertTrue(submitButton.isEnabled(), "Submit button should be enabled!");

// 6. Verify element is selected (for checkboxes/radio buttons)
Assert.assertTrue(checkbox.isSelected(), "Checkbox should be checked!");
```

---

## 🐍 Python vs Java Comparison

### Test Framework in Python (What You Know)
```python
# Pytest approach (familiar to you)
import pytest
from selenium import webdriver

class TestGoogle:
    def setup_method(self):
        """Runs before each test"""
        self.driver = webdriver.Chrome()

    def teardown_method(self):
        """Runs after each test"""
        self.driver.quit()

    def test_google_title(self):
        """Test case"""
        self.driver.get("https://www.google.com")
        assert self.driver.title == "Google"

    def test_google_search(self):
        """Another test case"""
        self.driver.get("https://www.google.com")
        assert "Google" in self.driver.title
```

### Test Framework in Java TestNG (What You're Learning)
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;

public class GoogleTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        // Runs before each test
        driver = new ChromeDriver();
    }

    @AfterMethod
    public void teardown() {
        // Runs after each test
        driver.quit();
    }

    @Test
    public void testGoogleTitle() {
        // Test case
        driver.get("https://www.google.com");
        Assert.assertEquals(driver.getTitle(), "Google");
    }

    @Test
    public void testGoogleSearch() {
        // Another test case
        driver.get("https://www.google.com");
        Assert.assertTrue(driver.getTitle().contains("Google"));
    }
}
```

**Key Differences:**
| Aspect | Python (Pytest) | Java (TestNG) |
|--------|----------------|---------------|
| **Setup** | `setup_method()` or `@pytest.fixture` | `@BeforeMethod` |
| **Teardown** | `teardown_method()` | `@AfterMethod` |
| **Test Method** | `def test_name(self):` | `@Test public void testName()` |
| **Assertions** | `assert condition` | `Assert.assertEquals/assertTrue()` |
| **Class Variables** | `self.driver` | `WebDriver driver;` (instance variable) |
| **Test Discovery** | Auto-discovers `test_*.py` files | Annotations `@Test` |

**Mental Model Shift:**
- **Python:** Convention-based (method names starting with `test_`)
- **Java TestNG:** Annotation-based (explicit `@Test` markers)
- **Python:** Implicit (less boilerplate)
- **Java:** Explicit (more verbose but clearer intent)

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Forgetting to import TestNG Assert class
❌ **Wrong:**
```java
public class LoginTest {
    @Test
    public void testLogin() {
        String title = driver.getTitle();
        assertEquals(title, "Login Page");  // Compile error!
    }
}
```

✅ **Correct:**
```java
import org.testng.Assert;  // Import TestNG Assert

public class LoginTest {
    @Test
    public void testLogin() {
        String title = driver.getTitle();
        Assert.assertEquals(title, "Login Page");  // Works!
    }
}
```

**Why it matters:** Java requires explicit imports. Unlike Python where you might have star imports, Java needs specific class imports. Always import `org.testng.Assert`.

---

### Mistake 2: Using JUnit instead of TestNG imports
❌ **Wrong:**
```java
import org.junit.Test;          // JUnit annotation
import org.junit.Assert;        // JUnit assertions

public class MyTest {
    @Test
    public void testSomething() {
        Assert.assertEquals("expected", "actual");
    }
}
```

✅ **Correct:**
```java
import org.testng.annotations.Test;  // TestNG annotation
import org.testng.Assert;            // TestNG assertions

public class MyTest {
    @Test
    public void testSomething() {
        Assert.assertEquals("actual", "expected");  // Note: order is different!
    }
}
```

**Why it matters:**
- JUnit and TestNG have similar-looking annotations but different features
- TestNG `Assert.assertEquals(actual, expected)` has **reversed parameter order** compared to JUnit
- Product companies primarily use TestNG with Selenium

---

### Mistake 3: Not making test methods public
❌ **Wrong:**
```java
public class SearchTest {
    @Test
    void testSearch() {  // Missing 'public' modifier
        // Test code
    }
}
```

✅ **Correct:**
```java
public class SearchTest {
    @Test
    public void testSearch() {  // Must be public
        // Test code
    }
}
```

**Why it matters:** TestNG requires test methods to be **public**. Private or package-private methods won't be executed. Python doesn't have this requirement, but Java does.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1:** Always use meaningful test method names that describe what you're testing
```java
// Bad
@Test
public void test1() { }

// Good
@Test
public void verifyLoginWithValidCredentials() { }
```

**Tip 2:** Use assertion messages to make failures easier to debug
```java
// Without message
Assert.assertEquals(actualTitle, expectedTitle);

// With message (better for debugging)
Assert.assertEquals(actualTitle, expectedTitle,
    "Page title mismatch! Expected: " + expectedTitle + " but got: " + actualTitle);
```

**Tip 3:** Close resources in @AfterMethod to ensure cleanup even if test fails
```java
@AfterMethod
public void teardown() {
    if (driver != null) {
        driver.quit();  // Runs even if test fails
    }
}
```

---

## 🔍 Deep Dive: Hard vs Soft Assertions

**What experienced developers know:**
TestNG has two types of assertions:
1. **Hard Assertions** - Test stops immediately when assertion fails
2. **Soft Assertions** - Test continues even if assertion fails, reports all failures at end

**Example:**
```java
import org.testng.Assert;
import org.testng.asserts.SoftAssert;
import org.testng.annotations.Test;

public class AssertionTypes {

    @Test
    public void hardAssertionExample() {
        Assert.assertEquals("Actual", "Expected", "First check");
        System.out.println("This won't print if above fails");
        Assert.assertTrue(false, "Second check");
        System.out.println("This definitely won't print");
    }

    @Test
    public void softAssertionExample() {
        SoftAssert softAssert = new SoftAssert();

        softAssert.assertEquals("Actual", "Expected", "First check");
        System.out.println("This WILL print even if above fails");

        softAssert.assertTrue(false, "Second check");
        System.out.println("This also prints");

        softAssert.assertAll();  // IMPORTANT: Must call at end to mark test as failed
    }
}
```

**When to use this:**
- **Hard Assertions:** When one failure makes rest of test meaningless (e.g., login failed, no point testing dashboard)
- **Soft Assertions:** When you want to collect all validation failures in one run (e.g., validating multiple fields on a form)

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Annotation** | Metadata that provides information to TestNG framework | `@Test`, `@BeforeMethod` |
| **Test Method** | A method marked with @Test annotation | `@Test public void loginTest() {}` |
| **Assertion** | Verification point that determines test pass/fail | `Assert.assertEquals(actual, expected)` |
| **Test Suite** | Collection of test cases executed together | Defined in `testng.xml` |
| **Test Class** | Java class containing test methods | `public class LoginTests {}` |
| **Lifecycle Methods** | Methods that run before/after tests | `@BeforeMethod`, `@AfterMethod` |

---

## 🎓 Interview Prep

**Common Interview Questions on TestNG Basics:**

**Q1:** What is TestNG and why do we use it over JUnit for Selenium?
**A:** TestNG is a testing framework for Java that provides advanced features specifically useful for Selenium automation:
```java
// TestNG advantages:
1. Parallel execution support
2. Data-driven testing with @DataProvider
3. Test groups and dependencies
4. Flexible test configuration with testng.xml
5. Better reporting out-of-the-box

@Test(groups = {"smoke", "regression"})
public void testLogin() {
    // Can group and run selectively
}
```

**Q2:** Explain the TestNG annotation execution order.
**A:** TestNG annotations execute in this order:
```java
@BeforeSuite    // Once before all tests in suite
  @BeforeTest   // Before each <test> in testng.xml
    @BeforeClass   // Once before first test in class
      @BeforeMethod  // Before each @Test method
        @Test        // Actual test
      @AfterMethod   // After each @Test method
    @AfterClass    // Once after all tests in class
  @AfterTest     // After each <test>
@AfterSuite      // Once after all tests in suite
```

**Q3:** What's the difference between hard and soft assertions?
**A:**
```java
// Hard Assertion - stops test immediately on failure
@Test
public void hardAssertTest() {
    Assert.assertEquals(1, 2);  // Test fails here
    System.out.println("Won't execute");  // Never runs
}

// Soft Assertion - continues test, reports all failures
@Test
public void softAssertTest() {
    SoftAssert soft = new SoftAssert();
    soft.assertEquals(1, 2);  // Logs failure
    System.out.println("Executes!");  // Still runs
    soft.assertAll();  // Marks test as failed with all failures
}
```

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What annotation do you use to mark a method as a test case in TestNG?**

2. **If you need to open a browser before each test method, which annotation should you use?**

3. **What's the correct way to assert that a string variable `title` equals "Google" in TestNG?**

4. **What will happen if an assertion fails in a test method?**

5. **What's the difference between `@BeforeClass` and `@BeforeMethod`?**

**Answers at the end of Practice file.**

---

**🎯 Ready for hands-on practice? Move to Day-1-Practice-Exercises.md**
