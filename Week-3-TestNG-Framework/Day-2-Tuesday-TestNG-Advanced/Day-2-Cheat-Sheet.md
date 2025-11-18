# DAY 2 CHEAT SHEET: TESTNG ADVANCED

## ⚡ Quick Syntax Reference

### Complete Lifecycle Annotations
```java
@BeforeSuite    // Once before ALL tests in suite
@BeforeTest     // Before each <test> in testng.xml
@BeforeClass    // Once before first test in THIS class
@BeforeMethod   // Before EACH @Test method
@Test           // The actual test
@AfterMethod    // After EACH @Test method
@AfterClass     // Once after all tests in THIS class
@AfterTest      // After each <test> in testng.xml
@AfterSuite     // Once after ALL tests in suite
```

### Test Groups
```java
// Single group
@Test(groups = {"smoke"})
public void test1() { }

// Multiple groups
@Test(groups = {"smoke", "regression", "login"})
public void test2() { }

// No group
@Test
public void test3() { }  // Runs when no group filter applied
```

### Test Priority
```java
@Test(priority = -1)   // Runs first
@Test(priority = 0)    // Default - runs before priority 1
@Test(priority = 1)    // Runs after priority 0
@Test(priority = 100)  // Runs last
```

### Test Dependencies
```java
// Depend on method
@Test
public void login() { }

@Test(dependsOnMethods = {"login"})
public void dashboard() { }  // Skipped if login fails

// Depend on multiple methods
@Test(dependsOnMethods = {"login", "loadData"})
public void test() { }

// Depend on group
@Test(groups = {"setup"})
public void setupDB() { }

@Test(dependsOnGroups = {"setup"})
public void test() { }  // Runs after ALL "setup" tests pass

// Soft dependency (run even if prerequisite fails)
@Test(dependsOnMethods = {"login"}, alwaysRun = true)
public void cleanup() { }
```

### testng.xml Configuration
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="My Suite">
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="LoginTests"/>
            <class name="ProductTests"/>
        </classes>
    </test>
</suite>
```

---

## 🔑 Key Concepts at a Glance

| Concept | Syntax | When to Use |
|---------|--------|-------------|
| **Suite Setup** | `@BeforeSuite` | DB connection, global config (once) |
| **Class Setup** | `@BeforeClass` | Load test data file (once per class) |
| **Method Setup** | `@BeforeMethod` | Open browser (every test) |
| **Smoke Group** | `@Test(groups = {"smoke"})` | Critical tests (run fast) |
| **Priority** | `@Test(priority = 1)` | Control execution order |
| **Dependency** | `@Test(dependsOnMethods = {"test1"})` | Test B needs test A to pass |
| **Always Run** | `@AfterClass(alwaysRun = true)` | Cleanup that must run |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Static context** | `@BeforeClass static` with instance var | Make variable static OR method non-static |
| **Dependency typo** | `dependsOnMethods = {"logIn"}` | Check spelling exactly matches method name |
| **Group typo in XML** | `<include name="somke"/>` | Must match code exactly: "smoke" |
| **Priority vs Dependency** | Expect priority to override | Dependencies ALWAYS override priority |
| **Missing alwaysRun** | Cleanup depends on failed test | Use `alwaysRun = true` for cleanup |
| **Wrong @BeforeClass use** | Open browser in @BeforeClass | Use @BeforeMethod for browser (needs isolation) |

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ Complete TestNG annotation lifecycle (@BeforeSuite through @AfterSuite)
- ✅ Test groups organize tests into categories (smoke, regression, etc.)
- ✅ Priority controls execution order (lower number = earlier)
- ✅ Dependencies create test chains (if A fails, skip B)
- ✅ testng.xml configures which tests run
- ✅ @BeforeClass for expensive setup, @BeforeMethod for fresh resources

**Critical for Automation:**
- 🎯 Use @BeforeMethod for browser (needs fresh instance per test)
- 🎯 Use @BeforeClass for test data (can be shared)
- 🎯 Dependencies override priority (remember this!)
- 🎯 Groups enable selective test execution in CI/CD

---

## 🎤 Interview Phrases

**When asked about test organization:**

*"I organize tests using TestNG groups and multiple test classes. Smoke tests are tagged with @Test(groups = {"smoke"}) for critical functionality that runs quickly. Regression tests include all tests. I use testng.xml to configure which groups run in different environments - smoke tests run on every pull request in about 2 minutes, while full regression runs nightly."*

**When asked about test dependencies:**

*"I use dependsOnMethods when one test logically depends on another, like a checkout test depending on login. If the login test fails, TestNG automatically skips the checkout test since we can't proceed. For cleanup methods, I use alwaysRun = true to ensure they execute even if the test fails, preventing resource leaks."*

**When asked about lifecycle annotations:**

*"I use @BeforeClass for expensive one-time setup like loading test data from files or initializing database connections. @BeforeMethod is for resources that need to be fresh for each test, like WebDriver instances - this ensures test isolation. @AfterMethod closes the browser after each test, and @AfterClass performs final cleanup."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **Lifecycle Hierarchy**: Suite (once) → Test (XML config) → Class (once) → Method (each test)
>
> ```java
> @BeforeSuite   // Once per suite
>   @BeforeClass // Once per class
>     @BeforeMethod // Every test
>       @Test
>     @AfterMethod
>   @AfterClass
> @AfterSuite
> ```
> Choose the right level: Expensive = higher level, Isolated = lower level

---

## 🔧 Quick Decision Guide

### "Should I use @BeforeClass or @BeforeMethod?"

```
Question: Does this need to be FRESH for each test?
├─ YES → Use @BeforeMethod
│   Examples: Browser, cleared cache, test-specific data
└─ NO → Is it expensive to create?
    ├─ YES → Use @BeforeClass
    │   Examples: Load 10MB file, DB connection
    └─ NO → Still use @BeforeMethod for safety
        Example: Small config object
```

### "Should I use priority or dependencies?"

```
Question: Does test B REQUIRE test A to pass?
├─ YES → Use dependsOnMethods
│   Example: Can't test checkout if login failed
└─ NO → Use priority for preferred order
    Example: Run smoke tests before regression (independent tests)
```

### "Should I use @BeforeClass or @BeforeSuite?"

```
Question: Do ALL test classes need this setup?
├─ YES → Use @BeforeSuite
│   Example: Database connection for entire suite
└─ NO → Use @BeforeClass
    Example: Test data specific to this class
```

---

## 🎯 Code Templates

### Template 1: Organized Test Class
```java
import org.testng.annotations.*;

public class MyTests {
    static SomeExpensiveResource resource;  // Class-level
    WebDriver driver;  // Instance-level

    @BeforeClass
    public void setupClass() {
        // Expensive - do ONCE
        resource = loadLargeFile();
    }

    @BeforeMethod
    public void setupMethod() {
        // Fresh - do EVERY TIME
        driver = new ChromeDriver();
    }

    @Test(priority = 1, groups = {"smoke"})
    public void test1() { }

    @Test(priority = 2, groups = {"regression"})
    public void test2() { }

    @AfterMethod
    public void teardownMethod() {
        if (driver != null) driver.quit();
    }

    @AfterClass(alwaysRun = true)
    public void teardownClass() {
        // Cleanup even if tests fail
        resource.close();
    }
}
```

### Template 2: Dependent Tests
```java
public class UserJourneyTests {

    @Test(priority = 1, groups = {"setup"})
    public void createUser() {
        // Step 1: Create user
    }

    @Test(priority = 2, dependsOnMethods = {"createUser"})
    public void login() {
        // Step 2: Login (needs user to exist)
    }

    @Test(priority = 3, dependsOnMethods = {"login"})
    public void editProfile() {
        // Step 3: Edit (needs to be logged in)
    }

    @Test(priority = 100, dependsOnGroups = {"setup"}, alwaysRun = true)
    public void cleanup() {
        // Always runs, even if tests fail
        deleteUser();
    }
}
```

### Template 3: testng.xml
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Test Suite" parallel="false">

    <!-- Smoke: Fast, critical tests -->
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="LoginTests"/>
            <class name="ProductTests"/>
            <class name="CheckoutTests"/>
        </classes>
    </test>

    <!-- Regression: All tests -->
    <test name="Regression Tests">
        <classes>
            <class name="LoginTests"/>
            <class name="ProductTests"/>
            <class name="CheckoutTests"/>
        </classes>
    </test>

</suite>
```

---

## 🔮 Tomorrow's Preview

**Day 3 Topic:** Data-Driven Testing with @DataProvider

**What to review tonight:**
- How groups work (you'll create data-driven tests in groups)
- Method parameters (DataProvider passes parameters to tests)

**What to think about:**
- How would you test login with 10 different username/password combinations?
- How would you read test data from Excel/CSV files?
- How can you run the same test with different datasets?

**Tomorrow you'll learn:**
- @DataProvider for parameterized tests
- Reading data from Excel (Apache POI)
- Reading data from CSV files
- Creating data-driven test suites

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- ✅ Learned complete TestNG lifecycle
- ✅ Mastered test groups and organization
- ✅ Implemented dependencies and priorities
- ✅ Built organized multi-class test suite
- ✅ 3 hours of advanced TestNG practice

**Cumulative Progress:**
- Days completed: 2/7 (Week 3)
- Can organize professional test suites ✓
- Understand CI/CD test execution strategies ✓

**Momentum Statement:**

*You're now organizing tests like a senior SDET. The groups, dependencies, and lifecycle management you learned today are used in every professional automation framework. You can now structure test suites that scale from 10 tests to 1000+ tests.*

---

## 📚 Quick Reference Card

```java
// Most Common Pattern in Real Projects
public class TestClass {
    static Properties config;      // @BeforeClass loads once
    WebDriver driver;              // @BeforeMethod creates fresh

    @BeforeClass
    public void loadConfig() {
        config = loadFromFile();   // Expensive, once
    }

    @BeforeMethod
    public void openBrowser() {
        driver = new ChromeDriver(); // Fresh, every test
    }

    @Test(priority = 1, groups = {"smoke", "login"})
    public void criticalTest() {
        // Runs in smoke suite, priority 1
    }

    @Test(dependsOnMethods = {"criticalTest"})
    public void dependentTest() {
        // Skipped if criticalTest fails
    }

    @AfterMethod
    public void closeBrowser() {
        if (driver != null) driver.quit();
    }

    @AfterClass(alwaysRun = true)
    public void cleanup() {
        // Runs even if tests fail
    }
}
```

**Remember:** Suite > Class > Method. Dependencies > Priority. alwaysRun for cleanup.

---

**🎉 Excellent work on Day 2! Tomorrow: Data-Driven Testing!**
