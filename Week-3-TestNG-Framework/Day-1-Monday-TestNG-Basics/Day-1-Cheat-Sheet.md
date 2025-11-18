# DAY 1 CHEAT SHEET: TESTNG BASICS

## ⚡ Quick Syntax Reference

### Basic Test Structure
```java
import org.testng.annotations.Test;
import org.testng.Assert;

public class MyTest {
    @Test
    public void testMethod() {
        // Test logic here
        Assert.assertEquals(actual, expected);
    }
}
```

### Lifecycle Annotations
```java
@BeforeSuite    // Runs once before all tests in suite
@BeforeTest     // Runs before each <test> in testng.xml
@BeforeClass    // Runs once before first test in class
@BeforeMethod   // Runs before each @Test method
@Test           // The actual test method
@AfterMethod    // Runs after each @Test method
@AfterClass     // Runs once after all tests in class
@AfterTest      // Runs after each <test>
@AfterSuite     // Runs once after all tests in suite
```

### Common Assertions
```java
// Equality
Assert.assertEquals(actual, expected, "message");
Assert.assertNotEquals(actual, expected, "message");

// Boolean
Assert.assertTrue(condition, "message");
Assert.assertFalse(condition, "message");

// Null checks
Assert.assertNull(object, "message");
Assert.assertNotNull(object, "message");

// Force fail
Assert.fail("Intentional failure message");
```

### Soft Assertions
```java
SoftAssert softAssert = new SoftAssert();
softAssert.assertEquals(actual, expected, "message");
softAssert.assertTrue(condition, "message");
softAssert.assertAll();  // MUST call at end!
```

### Selenium Integration
```java
WebDriver driver;

@BeforeMethod
public void setup() {
    driver = new ChromeDriver();
    driver.manage().window().maximize();
    driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
}

@Test
public void testSomething() {
    driver.get("https://example.com");
    // Test logic
}

@AfterMethod
public void teardown() {
    if (driver != null) {
        driver.quit();
    }
}
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java/TestNG Syntax | Python/Pytest Equivalent |
|---------|-------------------|--------------------------|
| **Mark test** | `@Test public void test1()` | `def test_1():` |
| **Setup each test** | `@BeforeMethod` | `def setup_method():` |
| **Teardown each test** | `@AfterMethod` | `def teardown_method():` |
| **Setup once** | `@BeforeClass` | `@pytest.fixture(scope="class")` |
| **Assert equal** | `Assert.assertEquals(a, e)` | `assert a == e` |
| **Assert true** | `Assert.assertTrue(cond)` | `assert cond` |
| **Soft assert** | `SoftAssert + assertAll()` | `pytest.assume` (with plugin) |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Missing import** | `assertEquals(a, b);` | `import org.testng.Assert;`<br>`Assert.assertEquals(a, b);` |
| **Not public** | `@Test void test1()` | `@Test public void test1()` |
| **Wrong order** | `Assert.assertEquals(expected, actual)` | `Assert.assertEquals(actual, expected)` |
| **Forgot assertAll** | `SoftAssert s = new SoftAssert();`<br>`s.assertEquals(a, b);` | `s.assertEquals(a, b);`<br>`s.assertAll();` |
| **No null check** | `driver.quit();` in @AfterMethod | `if(driver != null) driver.quit();` |

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ TestNG is the industry standard for Selenium automation in Java
- ✅ @Test annotation marks methods as test cases
- ✅ @BeforeMethod/@AfterMethod manage test lifecycle (setup/teardown)
- ✅ Assert class provides validation methods (assertEquals, assertTrue, etc.)
- ✅ TestNG executes annotations by type, not by code position
- ✅ Soft assertions allow multiple checks without stopping on first failure

**Critical for Automation:**
- 🎯 Browser lifecycle MUST be managed with @BeforeMethod/@AfterMethod
- 🎯 Always use `Assert.assertEquals(actual, expected)` - order matters!
- 🎯 Include descriptive messages in all assertions for easier debugging

---

## 🎤 Interview Phrases

**When asked about TestNG, say:**

*"TestNG is a testing framework for Java that provides powerful features for organizing and executing tests. It's particularly popular for Selenium automation because it supports parallel execution, data-driven testing through @DataProvider, flexible test configuration with testng.xml, and generates detailed HTML reports out of the box."*

**When asked about test lifecycle:**

*"In TestNG, I use @BeforeMethod to initialize the WebDriver before each test to ensure test independence, and @AfterMethod to close the browser and clean up resources. For one-time setup like loading test data or database connections, I use @BeforeClass and @AfterClass."*

**When asked about assertions:**

*"I use TestNG's Assert class for validation. For most scenarios, I use hard assertions like Assert.assertEquals() which fail the test immediately. For comprehensive form validation where I want to see all failures at once, I use SoftAssert with assertAll() at the end."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> TestNG annotations control test execution flow, and the most important pattern for Selenium is:
> ```
> @BeforeMethod → setup browser
> @Test → perform test actions + assertions
> @AfterMethod → close browser
> ```
> This ensures each test runs in isolation with a fresh browser instance.

---

## 🔧 Quick Commands

### Run TestNG Tests

**From Command Line:**
```bash
# Run specific test class
mvn test -Dtest=ClassName

# Run all tests
mvn test

# Run with testng.xml
mvn test -DsuiteXmlFile=testng.xml
```

**From IDE (IntelliJ/Eclipse):**
```
Right-click on:
- Test class → Run 'ClassName'
- Test method → Run 'methodName'
- testng.xml → Run 'testng.xml'
```

---

## 🎯 Code Snippets to Memorize

### Template 1: Basic Selenium Test
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;

public class BasicTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
    }

    @Test
    public void test1() {
        driver.get("https://example.com");
        Assert.assertEquals(driver.getTitle(), "Expected Title");
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) driver.quit();
    }
}
```

### Template 2: Soft Assertions
```java
import org.testng.asserts.SoftAssert;
import org.testng.annotations.Test;

public class SoftAssertTest {
    @Test
    public void multipleChecks() {
        SoftAssert soft = new SoftAssert();

        soft.assertEquals(actual1, expected1, "Check 1");
        soft.assertTrue(condition, "Check 2");
        soft.assertNotNull(object, "Check 3");

        soft.assertAll();  // Don't forget!
    }
}
```

### Template 3: Helper Method Pattern
```java
public class LoginTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
    }

    @Test
    public void testDashboard() {
        performLogin("user", "pass");  // Reusable
        // Test dashboard
    }

    @Test
    public void testProfile() {
        performLogin("user", "pass");  // Reusable
        // Test profile
    }

    private void performLogin(String user, String pass) {
        driver.get("https://example.com/login");
        driver.findElement(By.id("username")).sendKeys(user);
        driver.findElement(By.id("password")).sendKeys(pass);
        driver.findElement(By.id("login")).click();
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) driver.quit();
    }
}
```

---

## 🔮 Tomorrow's Preview

**Day 2 Topic:** TestNG Advanced Features

**What to review tonight:**
- Today's annotation lifecycle (@BeforeClass vs @BeforeMethod)
- The concept of test grouping (we'll dive deep tomorrow)

**What to think about:**
- How would you run only "smoke tests" vs "regression tests"?
- What if one test depends on another (e.g., can't test checkout without login)?
- How would you run tests in a specific order?

**Tomorrow you'll learn:**
- @BeforeClass / @AfterClass / @BeforeSuite / @AfterSuite
- Test groups (@Test(groups = {"smoke", "regression"}))
- Test dependencies (@Test(dependsOnMethods = {...}))
- Test priority (@Test(priority = 1))

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- ✅ Learned TestNG framework basics
- ✅ Completed 6 practice exercises
- ✅ Built Sauce Demo test suite
- ✅ 3 hours of TestNG + Selenium practice

**Cumulative Progress:**
- Days completed: 1/7 (Week 3)
- TestNG concepts mastered: Annotations, Assertions, Lifecycle
- Real test suite built: ✓ Sauce Demo Tests

**Momentum Statement:**

*You've taken your first major step in Week 3. You now have a working TestNG + Selenium test suite - the foundation that every automation framework builds on. Companies pay 20-25 LPA for SDETs who can architect robust test frameworks, and you're building that expertise day by day.*

*Tomorrow: You'll learn advanced TestNG features that separate junior automation engineers from senior SDETs.*

---

## 📚 Additional Resources

**Official Documentation:**
- TestNG: https://testng.org/doc/documentation-main.html
- TestNG Annotations: https://testng.org/doc/documentation-main.html#annotations

**Practice Sites:**
- Sauce Demo: https://www.saucedemo.com
- The Internet: https://the-internet.herokuapp.com
- DemoQA: https://demoqa.com

**Quick Reference:**
```java
// Most used imports
import org.testng.annotations.*;  // All annotations
import org.testng.Assert;         // Hard assertions
import org.testng.asserts.SoftAssert;  // Soft assertions

// Most used annotations (in order of execution)
@BeforeClass → @BeforeMethod → @Test → @AfterMethod → @AfterClass

// Most used assertions
Assert.assertEquals(actual, expected, "message");
Assert.assertTrue(condition, "message");
Assert.assertNotNull(object, "message");
```

---

**🎉 Excellent work on Day 1! Rest up and get ready for TestNG Advanced Features tomorrow!**
