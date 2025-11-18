# DAY 2: TESTNG ADVANCED FEATURES

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master all TestNG lifecycle annotations (@BeforeClass, @AfterClass, @BeforeSuite, @AfterSuite)
- [ ] Organize tests using groups (smoke, regression, sanity, etc.)
- [ ] Control test execution order with priority and dependencies
- [ ] Build a properly organized test suite with multiple test classes
- [ ] Understand when to use each annotation type

**Time Required:** 3 hours
**Difficulty:** Medium
**Prerequisite:** Day 1 TestNG Basics (@Test, @BeforeMethod, @AfterMethod, assertions)

---

## 📚 Core Concepts

### Concept 1: Complete Annotation Lifecycle

**What is it?**
Yesterday you learned @BeforeMethod and @AfterMethod. Today you'll master the complete hierarchy of TestNG annotations that control setup and teardown at different levels (method, class, test, suite).

**Why does it matter for automation?**
In real automation frameworks:
- Some setup is expensive (database connections, test data loading) - do once with **@BeforeClass**
- Some setup must be fresh for each test (browser session) - do every time with **@BeforeMethod**
- Some setup is needed for the entire test run (API authentication tokens) - do once with **@BeforeSuite**

Choosing the right annotation level makes your tests faster and more maintainable.

**Complete Hierarchy:**
```
@BeforeSuite      ← Runs ONCE before entire test suite
    @BeforeTest   ← Runs before each <test> tag in testng.xml
        @BeforeClass   ← Runs ONCE before first @Test in class
            @BeforeMethod  ← Runs before EACH @Test method
                @Test
            @AfterMethod   ← Runs after EACH @Test method
        @AfterClass    ← Runs ONCE after all @Test in class
    @AfterTest     ← Runs after each <test> tag
@AfterSuite       ← Runs ONCE after entire test suite
```

**Syntax:**
```java
import org.testng.annotations.*;

public class LifecycleDemo {

    @BeforeSuite
    public void beforeSuite() {
        System.out.println("1. BeforeSuite: Runs ONCE before all tests");
        // Example: Initialize database connection pool
    }

    @BeforeTest
    public void beforeTest() {
        System.out.println("2. BeforeTest: Runs before <test> in testng.xml");
        // Example: Set test environment variables
    }

    @BeforeClass
    public void beforeClass() {
        System.out.println("3. BeforeClass: Runs ONCE before first test in class");
        // Example: Load test data from file
    }

    @BeforeMethod
    public void beforeMethod() {
        System.out.println("4. BeforeMethod: Runs before EACH test");
        // Example: Open fresh browser
    }

    @Test
    public void test1() {
        System.out.println("   → Test 1 executing");
    }

    @Test
    public void test2() {
        System.out.println("   → Test 2 executing");
    }

    @AfterMethod
    public void afterMethod() {
        System.out.println("5. AfterMethod: Runs after EACH test");
        // Example: Close browser
    }

    @AfterClass
    public void afterClass() {
        System.out.println("6. AfterClass: Runs ONCE after all tests in class");
        // Example: Clear test data
    }

    @AfterTest
    public void afterTest() {
        System.out.println("7. AfterTest: Runs after <test> tag");
        // Example: Generate test report
    }

    @AfterSuite
    public void afterSuite() {
        System.out.println("8. AfterSuite: Runs ONCE after all tests");
        // Example: Close database connections, send email report
    }
}
```

**Execution Output:**
```
1. BeforeSuite: Runs ONCE before all tests
2. BeforeTest: Runs before <test> in testng.xml
3. BeforeClass: Runs ONCE before first test in class
4. BeforeMethod: Runs before EACH test
   → Test 1 executing
5. AfterMethod: Runs after EACH test
4. BeforeMethod: Runs before EACH test
   → Test 2 executing
5. AfterMethod: Runs after EACH test
6. AfterClass: Runs ONCE after all tests in class
7. AfterTest: Runs after <test> tag
8. AfterSuite: Runs ONCE after all tests
```

**Real Example:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.*;
import java.time.Duration;
import java.util.Properties;

public class RealWorldExample {
    static Properties testData;  // Shared across all tests
    WebDriver driver;  // Fresh for each test

    @BeforeSuite
    public void loadTestData() {
        // Expensive operation - do ONCE for entire suite
        testData = new Properties();
        testData.setProperty("baseUrl", "https://example.com");
        testData.setProperty("apiKey", "abc123");
        System.out.println("✓ Test data loaded (ONCE for suite)");
    }

    @BeforeClass
    public void setupTestUser() {
        // Create test user in database - do ONCE per class
        System.out.println("✓ Test user created (ONCE for class)");
    }

    @BeforeMethod
    public void openBrowser() {
        // Fresh browser for each test - do BEFORE EACH test
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        System.out.println("✓ Browser opened (BEFORE EACH test)");
    }

    @Test
    public void testLogin() {
        driver.get(testData.getProperty("baseUrl"));
        // Login test
    }

    @Test
    public void testLogout() {
        driver.get(testData.getProperty("baseUrl"));
        // Logout test
    }

    @AfterMethod
    public void closeBrowser() {
        if (driver != null) {
            driver.quit();
            System.out.println("✓ Browser closed (AFTER EACH test)");
        }
    }

    @AfterClass
    public void cleanupTestUser() {
        // Delete test user from database - do ONCE after all tests
        System.out.println("✓ Test user deleted (ONCE after class)");
    }

    @AfterSuite
    public void cleanup() {
        // Final cleanup - do ONCE after entire suite
        System.out.println("✓ All resources cleaned up (ONCE after suite)");
    }
}
```

---

### Concept 2: Test Groups

**What is it?**
Groups allow you to categorize tests (smoke, regression, sanity, etc.) and run specific categories instead of all tests. A test can belong to multiple groups.

**Why does it matter for automation?**
In real projects:
- **Smoke tests** (20 critical tests) run after every deployment - 5 minutes
- **Regression tests** (500+ tests) run nightly - 2 hours
- **Sanity tests** run before starting manual testing
- **API tests** vs **UI tests** can run in different pipelines

**Syntax:**
```java
import org.testng.annotations.Test;

public class GroupsExample {

    @Test(groups = {"smoke"})
    public void testLogin() {
        System.out.println("Login test - SMOKE group");
    }

    @Test(groups = {"smoke", "regression"})
    public void testDashboard() {
        System.out.println("Dashboard test - SMOKE and REGRESSION groups");
    }

    @Test(groups = {"regression"})
    public void testAdvancedFilters() {
        System.out.println("Filters test - REGRESSION group only");
    }

    @Test(groups = {"api", "smoke"})
    public void testUserAPI() {
        System.out.println("API test - API and SMOKE groups");
    }

    @Test  // No group specified
    public void testHelper() {
        System.out.println("Helper test - no group");
    }
}
```

**How to Run Specific Groups:**

**Option 1: Using testng.xml**
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Smoke Test Suite">
    <test name="Smoke Tests Only">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="GroupsExample"/>
        </classes>
    </test>
</suite>
```

**Option 2: Command Line**
```bash
mvn test -Dgroups=smoke
mvn test -Dgroups=smoke,regression
```

**Real Example:**
```java
import org.testng.annotations.Test;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class EcommerceTests {

    @Test(groups = {"smoke", "login"})
    public void verifyLoginPageLoad() {
        // Critical: Login page must load
        System.out.println("SMOKE: Verify login page loads");
    }

    @Test(groups = {"smoke", "login"})
    public void verifyValidLogin() {
        // Critical: Must be able to login
        System.out.println("SMOKE: Verify valid login works");
    }

    @Test(groups = {"regression", "login"})
    public void verifyInvalidLoginMessages() {
        // Detailed: All error messages correct
        System.out.println("REGRESSION: Verify all login error messages");
    }

    @Test(groups = {"regression", "login"})
    public void verifyPasswordReset() {
        // Detailed: Password reset flow
        System.out.println("REGRESSION: Verify password reset");
    }

    @Test(groups = {"smoke", "checkout"})
    public void verifyAddToCart() {
        // Critical: Must be able to add to cart
        System.out.println("SMOKE: Verify add to cart");
    }

    @Test(groups = {"regression", "checkout"})
    public void verifyCartQuantityUpdate() {
        // Detailed: Quantity updates correctly
        System.out.println("REGRESSION: Verify cart quantity changes");
    }

    @Test(groups = {"smoke", "checkout", "payment"})
    public void verifyCheckout() {
        // Critical: Checkout must work
        System.out.println("SMOKE: Verify checkout completes");
    }

    @Test(groups = {"regression", "payment"})
    public void verifyMultiplePaymentMethods() {
        // Detailed: All payment options work
        System.out.println("REGRESSION: Verify credit card, PayPal, etc.");
    }
}
```

**Common Group Strategies:**
```java
// By test type
@Test(groups = {"smoke"})      // 20 most critical tests
@Test(groups = {"regression"}) // All tests
@Test(groups = {"sanity"})     // Quick health check

// By functionality
@Test(groups = {"login"})
@Test(groups = {"checkout"})
@Test(groups = {"search"})

// By layer
@Test(groups = {"ui"})
@Test(groups = {"api"})
@Test(groups = {"database"})

// By environment
@Test(groups = {"production-safe"})
@Test(groups = {"staging-only"})

// Multiple groups
@Test(groups = {"smoke", "api", "login"})
```

---

### Concept 3: Test Dependencies

**What is it?**
Make one test depend on another test's success. If the prerequisite test fails, dependent tests are skipped automatically.

**Why does it matter for automation?**
Sometimes test order matters:
- Can't test "Edit Profile" if "Login" failed
- Can't test "Checkout" if "Add to Cart" failed
- Can't test "Delete User" if "Create User" failed

**Syntax:**
```java
import org.testng.annotations.Test;

public class DependencyExample {

    @Test
    public void createUser() {
        System.out.println("1. User created");
        // Create user logic
    }

    @Test(dependsOnMethods = {"createUser"})
    public void loginUser() {
        System.out.println("2. User logged in");
        // Can't login if user creation failed
    }

    @Test(dependsOnMethods = {"loginUser"})
    public void editProfile() {
        System.out.println("3. Profile edited");
        // Can't edit profile if login failed
    }

    @Test(dependsOnMethods = {"loginUser"})
    public void changePassword() {
        System.out.println("4. Password changed");
        // Also depends on login, but independent of editProfile
    }

    @Test(dependsOnMethods = {"editProfile", "changePassword"})
    public void deleteUser() {
        System.out.println("5. User deleted");
        // Cleanup - depends on multiple tests
    }
}
```

**If a test fails:**
```
createUser - FAILED (assertion failed)
loginUser - SKIPPED (depends on createUser)
editProfile - SKIPPED (depends on loginUser)
changePassword - SKIPPED (depends on loginUser)
deleteUser - SKIPPED (depends on editProfile and changePassword)
```

**Real Example:**
```java
import org.testng.Assert;
import org.testng.annotations.Test;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class UserJourneyTest {
    WebDriver driver;
    String userId;

    @Test
    public void step1_OpenWebsite() {
        driver = new ChromeDriver();
        driver.get("https://example.com");
        Assert.assertTrue(driver.getTitle().contains("Example"),
            "Website should load");
        System.out.println("✓ Step 1: Website opened");
    }

    @Test(dependsOnMethods = {"step1_OpenWebsite"})
    public void step2_Register() {
        driver.findElement(By.id("register")).click();
        driver.findElement(By.id("username")).sendKeys("testuser");
        driver.findElement(By.id("password")).sendKeys("password123");
        driver.findElement(By.id("submit")).click();

        userId = driver.findElement(By.id("userId")).getText();
        Assert.assertNotNull(userId, "User ID should be generated");
        System.out.println("✓ Step 2: User registered with ID: " + userId);
    }

    @Test(dependsOnMethods = {"step2_Register"})
    public void step3_Login() {
        driver.findElement(By.id("username")).sendKeys("testuser");
        driver.findElement(By.id("password")).sendKeys("password123");
        driver.findElement(By.id("login")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("dashboard"),
            "Should redirect to dashboard");
        System.out.println("✓ Step 3: User logged in");
    }

    @Test(dependsOnMethods = {"step3_Login"})
    public void step4_UpdateProfile() {
        driver.findElement(By.id("profile")).click();
        driver.findElement(By.id("firstName")).sendKeys("John");
        driver.findElement(By.id("save")).click();

        Assert.assertTrue(driver.findElement(By.id("successMessage")).isDisplayed(),
            "Success message should appear");
        System.out.println("✓ Step 4: Profile updated");
    }

    @Test(dependsOnMethods = {"step4_UpdateProfile"}, alwaysRun = true)
    public void step5_Cleanup() {
        // alwaysRun = true means this runs even if step4 failed
        if (driver != null) {
            driver.quit();
            System.out.println("✓ Step 5: Cleanup completed");
        }
    }
}
```

**Dependency on Groups:**
```java
@Test(groups = {"init"})
public void setupDatabase() {
    System.out.println("Database initialized");
}

@Test(groups = {"init"})
public void setupTestData() {
    System.out.println("Test data loaded");
}

@Test(dependsOnGroups = {"init"})
public void testUserCreation() {
    // This runs only after ALL tests in "init" group pass
    System.out.println("Testing user creation");
}
```

**Hard vs Soft Dependencies:**
```java
// Hard dependency (default): Skip if prerequisite fails
@Test(dependsOnMethods = {"login"})
public void testDashboard() { }

// Soft dependency: Run even if prerequisite fails
@Test(dependsOnMethods = {"login"}, alwaysRun = true)
public void testDashboard() { }
```

---

### Concept 4: Test Priority

**What is it?**
Control the execution order of tests using priority numbers. Lower priority runs first.

**Why does it matter for automation?**
By default, TestNG runs tests alphabetically. Sometimes you want specific order:
- Run critical smoke tests first
- Run cleanup tests last
- Run prerequisite tests before dependent tests

**Syntax:**
```java
import org.testng.annotations.Test;

public class PriorityExample {

    @Test(priority = 1)
    public void firstTest() {
        System.out.println("Priority 1: Runs first");
    }

    @Test(priority = 2)
    public void secondTest() {
        System.out.println("Priority 2: Runs second");
    }

    @Test(priority = 3)
    public void thirdTest() {
        System.out.println("Priority 3: Runs third");
    }

    @Test  // No priority = default priority 0
    public void defaultPriorityTest() {
        System.out.println("Priority 0: Runs before priority 1");
    }

    @Test(priority = -1)
    public void negativeTest() {
        System.out.println("Priority -1: Runs before priority 0");
    }
}
```

**Execution Order:**
```
Priority -1: Runs before priority 0
Priority 0: Runs before priority 1
Priority 1: Runs first
Priority 2: Runs second
Priority 3: Runs third
```

**Real Example:**
```java
public class EcommerceTestSuite {

    @Test(priority = 1, groups = {"smoke"})
    public void verifyHomePage() {
        System.out.println("1. Homepage loads");
    }

    @Test(priority = 2, groups = {"smoke"})
    public void verifyLogin() {
        System.out.println("2. Login works");
    }

    @Test(priority = 3)
    public void verifyProductSearch() {
        System.out.println("3. Search works");
    }

    @Test(priority = 4)
    public void verifyAddToCart() {
        System.out.println("4. Add to cart works");
    }

    @Test(priority = 5)
    public void verifyCheckout() {
        System.out.println("5. Checkout completes");
    }

    @Test(priority = 100)  // High number = runs last
    public void cleanupTestData() {
        System.out.println("100. Cleanup test data");
    }
}
```

**Priority Best Practices:**
```java
// Use gaps between priorities for flexibility
@Test(priority = 10)  // Not priority = 1
@Test(priority = 20)  // Not priority = 2
@Test(priority = 30)  // Not priority = 3
// Now you can add priority 15 later without renumbering

// Combine with groups
@Test(priority = 1, groups = {"smoke"})
@Test(priority = 1, groups = {"regression"})  // Same priority, different groups

// Use negative for setup, high for cleanup
@Test(priority = -100)  // Setup
@Test(priority = 1)     // Normal tests
@Test(priority = 1000)  // Cleanup
```

---

## 🐍 Python vs Java Comparison

### Test Organization in Python (What You Know)
```python
import pytest

class TestEcommerce:

    @classmethod
    def setup_class(cls):
        """Runs once before all tests in class"""
        cls.test_data = {"user": "test@example.com"}
        print("Setup class: Load test data")

    def setup_method(self):
        """Runs before each test"""
        self.driver = webdriver.Chrome()
        print("Setup method: Open browser")

    def test_1_login(self):
        """Test 1: Login"""
        print("Test: Login")

    def test_2_dashboard(self):
        """Test 2: Dashboard"""
        print("Test: Dashboard")

    def teardown_method(self):
        """Runs after each test"""
        self.driver.quit()
        print("Teardown method: Close browser")

    @classmethod
    def teardown_class(cls):
        """Runs once after all tests"""
        print("Teardown class: Cleanup")

# Run specific tests
pytest.mark.smoke
pytest.mark.regression
```

### Test Organization in Java TestNG (What You're Learning)
```java
import org.testng.annotations.*;

public class EcommerceTests {
    static Properties testData;  // Class-level
    WebDriver driver;  // Instance-level

    @BeforeClass
    public void setupClass() {
        // Runs ONCE before all tests in class
        testData = new Properties();
        testData.setProperty("user", "test@example.com");
        System.out.println("Setup class: Load test data");
    }

    @BeforeMethod
    public void setupMethod() {
        // Runs before EACH test
        driver = new ChromeDriver();
        System.out.println("Setup method: Open browser");
    }

    @Test(priority = 1, groups = {"smoke"})
    public void testLogin() {
        System.out.println("Test: Login");
    }

    @Test(priority = 2, groups = {"regression"})
    public void testDashboard() {
        System.out.println("Test: Dashboard");
    }

    @AfterMethod
    public void teardownMethod() {
        // Runs after EACH test
        if (driver != null) driver.quit();
        System.out.println("Teardown method: Close browser");
    }

    @AfterClass
    public void teardownClass() {
        // Runs ONCE after all tests
        System.out.println("Teardown class: Cleanup");
    }
}
```

**Key Differences:**

| Aspect | Python (Pytest) | Java (TestNG) |
|--------|----------------|---------------|
| **Class setup** | `@classmethod setup_class()` | `@BeforeClass` |
| **Method setup** | `setup_method()` | `@BeforeMethod` |
| **Test marker** | `def test_name():` | `@Test public void testName()` |
| **Test groups** | `@pytest.mark.smoke` | `@Test(groups = {"smoke"})` |
| **Test order** | Test name prefixes `test_1_`, `test_2_` | `@Test(priority = 1)` |
| **Dependencies** | Plugins required | Built-in `dependsOnMethods` |
| **Suite setup** | `conftest.py` fixtures | `@BeforeSuite` |

**Mental Model Shift:**
- **Python:** Relies on naming conventions and decorators
- **Java TestNG:** Explicit annotations with parameters
- **Python:** Suite-level setup via conftest.py
- **Java:** Suite-level setup via @BeforeSuite in any class

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Using @BeforeClass with instance variables incorrectly

❌ **Wrong:**
```java
public class LoginTest {
    WebDriver driver;  // Instance variable

    @BeforeClass
    public void setup() {
        driver = new ChromeDriver();  // Created ONCE for all tests
    }

    @Test
    public void test1() {
        driver.get("https://example.com");
        // Works fine
    }

    @Test
    public void test2() {
        driver.get("https://another.com");
        // Uses SAME browser instance as test1
        // Could have leftover state (cookies, localStorage)
    }

    @AfterClass
    public void teardown() {
        driver.quit();  // Closes after ALL tests
    }
}
```

✅ **Correct:**
```java
public class LoginTest {
    WebDriver driver;  // Instance variable

    @BeforeMethod  // Changed to BeforeMethod
    public void setup() {
        driver = new ChromeDriver();  // Fresh browser for EACH test
    }

    @Test
    public void test1() {
        driver.get("https://example.com");
        // Fresh browser
    }

    @Test
    public void test2() {
        driver.get("https://another.com");
        // NEW fresh browser, no leftover state
    }

    @AfterMethod  // Changed to AfterMethod
    public void teardown() {
        if (driver != null) driver.quit();  // Closes after EACH test
    }
}
```

**Why it matters:**
- Use @BeforeClass for **expensive setup that can be shared** (loading data files, DB connections)
- Use @BeforeMethod for **isolated setup** (browser instances, test-specific data)
- Tests should be **independent** - each test should work even if others fail

---

### Mistake 2: Circular dependencies

❌ **Wrong:**
```java
@Test(dependsOnMethods = {"test2"})
public void test1() {
    System.out.println("Test 1");
}

@Test(dependsOnMethods = {"test1"})
public void test2() {
    System.out.println("Test 2");
}
// This creates circular dependency: test1 needs test2, test2 needs test1
```

**Error:**
```
org.testng.TestNGException: test1 and test2 have cyclic dependencies
```

✅ **Correct:**
```java
@Test
public void test1() {
    System.out.println("Test 1 - independent");
}

@Test(dependsOnMethods = {"test1"})
public void test2() {
    System.out.println("Test 2 - depends on test1");
}
```

**Why it matters:** Dependencies should form a directed acyclic graph (DAG), not cycles. Think of it as a flowchart where arrows point in one direction only.

---

### Mistake 3: Wrong group name in testng.xml

❌ **Wrong:**
```java
// Test class
@Test(groups = {"smoke"})
public void testLogin() { }
```

```xml
<!-- testng.xml -->
<groups>
    <run>
        <include name="somke"/>  <!-- Typo: "somke" instead of "smoke" -->
    </run>
</groups>
```

**Result:** No tests run, no error message!

✅ **Correct:**
```xml
<groups>
    <run>
        <include name="smoke"/>  <!-- Correct spelling -->
    </run>
</groups>
```

**Why it matters:** TestNG silently ignores non-matching group names. Always double-check spelling in testng.xml.

---

### Mistake 4: Forgetting alwaysRun for cleanup

❌ **Wrong:**
```java
@Test
public void createTestUser() {
    // Create user in database
    userId = createUser();
}

@Test(dependsOnMethods = {"createTestUser"})
public void testUserLogin() {
    // Login test
}

@Test(dependsOnMethods = {"testUserLogin"})
public void deleteTestUser() {
    // Delete user from database
    deleteUser(userId);
    // If testUserLogin fails, this never runs!
    // Test user remains in database!
}
```

✅ **Correct:**
```java
@Test
public void createTestUser() {
    userId = createUser();
}

@Test(dependsOnMethods = {"createTestUser"})
public void testUserLogin() {
    // Login test
}

@Test(dependsOnMethods = {"testUserLogin"}, alwaysRun = true)
public void deleteTestUser() {
    // alwaysRun = true means this runs even if testUserLogin fails
    deleteUser(userId);
}
```

**Why it matters:** Cleanup should ALWAYS run, even if tests fail. Otherwise you get "dirty" test data that affects future runs.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use meaningful group names**
```java
// Bad
@Test(groups = {"g1"})
@Test(groups = {"group"})

// Good
@Test(groups = {"smoke"})
@Test(groups = {"regression"})
@Test(groups = {"smoke", "login", "critical"})
```

**Tip 2: Combine priority and groups**
```java
// Within smoke tests, login runs first, then dashboard
@Test(priority = 1, groups = {"smoke"})
public void testLogin() { }

@Test(priority = 2, groups = {"smoke"})
public void testDashboard() { }

// Regression tests start after smoke tests complete
@Test(priority = 10, groups = {"regression"})
public void testAdvancedFeature() { }
```

**Tip 3: Use @BeforeClass for expensive setup**
```java
@BeforeClass
public void loadLargeDataFile() {
    // Loading 10MB CSV file - do ONCE
    testData = loadCSV("test-data.csv");
}

@BeforeMethod
public void setupBrowser() {
    // Browser needs to be fresh - do EVERY TIME
    driver = new ChromeDriver();
}
```

**Tip 4: Document dependencies**
```java
/**
 * This test depends on:
 * - User created by createUser test
 * - Database initialized by @BeforeClass
 */
@Test(dependsOnMethods = {"createUser"})
public void testUserLogin() {
    // Clear what this depends on
}
```

---

## 🔍 Deep Dive: When to Use Each Annotation

**Decision Tree:**

```
Question: Does this setup need to run before EVERY test?
├─ YES → Use @BeforeMethod
│   Example: Opening browser, clearing cache
└─ NO → Does it need to run once for the CLASS?
    ├─ YES → Use @BeforeClass
    │   Example: Loading test data file, setting up test user
    └─ NO → Does it need to run once for the ENTIRE SUITE?
        └─ YES → Use @BeforeSuite
            Example: Database connection, API authentication
```

**Real-World Example:**
```java
public class ComprehensiveExample {

    static Database db;          // Suite-level
    static Properties config;    // Class-level
    WebDriver driver;            // Method-level

    @BeforeSuite
    public void suiteSetup() {
        // Expensive: Connect to database ONCE for entire suite
        db = new Database("jdbc:mysql://localhost:3306/test");
        System.out.println("SUITE: Database connected");
    }

    @BeforeClass
    public void classSetup() {
        // Medium cost: Load configuration ONCE for this class
        config = new Properties();
        config.load(new FileInputStream("config.properties"));
        System.out.println("CLASS: Config loaded");
    }

    @BeforeMethod
    public void methodSetup() {
        // Must be fresh: New browser for EACH test
        driver = new ChromeDriver();
        driver.get(config.getProperty("baseUrl"));
        System.out.println("METHOD: Browser opened");
    }

    @Test
    public void test1() {
        // Has access to db, config, and driver
    }

    @Test
    public void test2() {
        // Same db and config, but NEW driver
    }

    @AfterMethod
    public void methodTeardown() {
        if (driver != null) driver.quit();
        System.out.println("METHOD: Browser closed");
    }

    @AfterClass
    public void classTeardown() {
        // Config will be garbage collected
        System.out.println("CLASS: Cleanup");
    }

    @AfterSuite
    public void suiteTeardown() {
        // Close database connection
        if (db != null) db.close();
        System.out.println("SUITE: Database closed");
    }
}
```

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Test Suite** | Collection of test classes executed together | Defined in testng.xml |
| **Test Group** | Category/tag for organizing tests | `@Test(groups = {"smoke"})` |
| **Priority** | Execution order number (lower = earlier) | `@Test(priority = 1)` |
| **Dependency** | One test requires another to pass first | `dependsOnMethods = {"login"}` |
| **@BeforeClass** | Runs once before first test in class | Setup test data |
| **@AfterClass** | Runs once after last test in class | Cleanup test data |
| **@BeforeSuite** | Runs once before entire test suite | DB connection |
| **@AfterSuite** | Runs once after entire test suite | Close DB connection |
| **alwaysRun** | Run even if dependencies fail | Cleanup methods |

---

## 🎓 Interview Prep

**Q1: Explain the difference between @BeforeClass and @BeforeMethod.**

**A:**
```java
// @BeforeClass runs ONCE before the first test in the class
@BeforeClass
public void loadTestData() {
    // Expensive operation: Load 10MB CSV file
    // Called ONCE regardless of number of tests
}

// @BeforeMethod runs BEFORE EACH test method
@BeforeMethod
public void openBrowser() {
    // Called before every @Test
    // Ensures test isolation with fresh browser
}

// In a class with 10 tests:
// - @BeforeClass runs 1 time
// - @BeforeMethod runs 10 times (once per test)
```

**Q2: How would you organize smoke and regression tests in TestNG?**

**A:**
```java
public class TestSuite {

    @Test(groups = {"smoke", "login"}, priority = 1)
    public void verifyLogin() {
        // Critical test: Part of smoke suite
    }

    @Test(groups = {"regression", "login"})
    public void verifyForgotPassword() {
        // Detailed test: Regression only
    }

    @Test(groups = {"smoke", "checkout"}, priority = 2)
    public void verifyCheckout() {
        // Critical test: Part of smoke suite
    }
}

// In testng.xml, run only smoke tests:
// <include name="smoke"/>

// Or run both:
// <include name="smoke"/>
// <include name="regression"/>
```

**Q3: When would you use test dependencies vs test priority?**

**A:**
```java
// Use DEPENDENCIES when test order matters for correctness
@Test
public void createUser() { }  // Must succeed

@Test(dependsOnMethods = {"createUser"})
public void loginUser() { }  // Skipped if createUser fails

// Use PRIORITY when you want specific order but tests are independent
@Test(priority = 1)
public void importantTest() { }  // Run first

@Test(priority = 2)
public void lessImportantTest() { }  // Run second

// Both are independent - lessImportantTest runs even if importantTest fails
```

---

## ✅ Self-Check Questions

1. **If you have 5 test methods in a class, how many times does @BeforeClass run?**

2. **What happens to dependent tests if the prerequisite test fails?**

3. **Can a test belong to multiple groups? How do you specify this?**

4. **What's the difference between `dependsOnMethods` and `dependsOnGroups`?**

5. **If you don't specify priority, what priority does a test have by default?**

**Answers at the end of Practice file.**

---

**🎯 Ready for advanced practice? Move to Day-2-Practice-Exercises.md**
