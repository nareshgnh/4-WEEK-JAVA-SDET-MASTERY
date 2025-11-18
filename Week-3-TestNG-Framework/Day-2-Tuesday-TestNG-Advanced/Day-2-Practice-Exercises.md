# DAY 2 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master TestNG advanced features: complete lifecycle annotations, test groups, dependencies, and priorities to build organized, production-ready test suites.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Complete Lifecycle Demonstration
**Objective:** Understand the execution order of all TestNG annotations

**Task:**
Create a test class that uses all lifecycle annotations and prints execution order.

**Starter Code:**
```java
import org.testng.annotations.*;

public class Exercise1_LifecycleDemo {

    @BeforeSuite
    public void beforeSuite() {
        // TODO: Print "1. BeforeSuite executed"
    }

    @BeforeTest
    public void beforeTest() {
        // TODO: Print "2. BeforeTest executed"
    }

    @BeforeClass
    public void beforeClass() {
        // TODO: Print "3. BeforeClass executed"
    }

    @BeforeMethod
    public void beforeMethod() {
        // TODO: Print "4. BeforeMethod executed"
    }

    @Test
    public void test1() {
        System.out.println("   → Test 1");
    }

    @Test
    public void test2() {
        System.out.println("   → Test 2");
    }

    @AfterMethod
    public void afterMethod() {
        // TODO: Print "5. AfterMethod executed"
    }

    @AfterClass
    public void afterClass() {
        // TODO: Print "6. AfterClass executed"
    }

    @AfterTest
    public void afterTest() {
        // TODO: Print "7. AfterTest executed"
    }

    @AfterSuite
    public void afterSuite() {
        // TODO: Print "8. AfterSuite executed"
    }
}
```

**Expected Output:**
```
1. BeforeSuite executed
2. BeforeTest executed
3. BeforeClass executed
4. BeforeMethod executed
   → Test 1
5. AfterMethod executed
4. BeforeMethod executed
   → Test 2
5. AfterMethod executed
6. AfterClass executed
7. AfterTest executed
8. AfterSuite executed
```

**Hints:**
- Each annotation runs at a different scope (suite, test, class, method)
- BeforeMethod and AfterMethod run for EACH test
- BeforeClass and AfterClass run ONCE per class

---

### Exercise 2: Test Groups Practice
**Objective:** Organize tests into smoke and regression groups

**Task:**
Create tests with different groups and verify they can be run selectively.

**Starter Code:**
```java
import org.testng.annotations.Test;

public class Exercise2_Groups {

    @Test(groups = {"smoke"})
    public void testCriticalFeature1() {
        // TODO: Print "SMOKE: Critical Feature 1"
    }

    @Test(groups = {"smoke"})
    public void testCriticalFeature2() {
        // TODO: Print "SMOKE: Critical Feature 2"
    }

    @Test(groups = {"regression"})
    public void testDetailedFeature1() {
        // TODO: Print "REGRESSION: Detailed Feature 1"
    }

    @Test(groups = {"regression"})
    public void testDetailedFeature2() {
        // TODO: Print "REGRESSION: Detailed Feature 2"
    }

    @Test(groups = {"smoke", "regression"})
    public void testImportantFeature() {
        // TODO: Print "BOTH: Important Feature (in both groups)"
    }

    @Test  // No group
    public void testUngrouped() {
        // TODO: Print "UNGROUPED: This test has no group"
    }
}
```

**Testing Instructions:**
1. Run all tests - all 6 should execute
2. Create testng.xml to run only "smoke" group
3. Verify only 3 tests execute (the 2 smoke + 1 in both groups)

**Hints:**
- A test can belong to multiple groups: `groups = {"smoke", "regression"}`
- Tests without groups run when no group filter is applied

---

### Exercise 3: Priority Practice
**Objective:** Control test execution order using priority

**Task:**
Create tests that execute in specific order regardless of method names.

**Starter Code:**
```java
import org.testng.annotations.Test;

public class Exercise3_Priority {

    @Test(priority = 3)
    public void testC() {
        // TODO: Print "Test C - Priority 3"
    }

    @Test(priority = 1)
    public void testA() {
        // TODO: Print "Test A - Priority 1"
    }

    @Test  // Default priority = 0
    public void testDefault() {
        // TODO: Print "Test Default - Priority 0"
    }

    @Test(priority = 2)
    public void testB() {
        // TODO: Print "Test B - Priority 2"
    }

    @Test(priority = -1)
    public void testSetup() {
        // TODO: Print "Test Setup - Priority -1 (runs first)"
    }

    @Test(priority = 100)
    public void testCleanup() {
        // TODO: Print "Test Cleanup - Priority 100 (runs last)"
    }
}
```

**Expected Execution Order:**
```
Test Setup - Priority -1 (runs first)
Test Default - Priority 0
Test A - Priority 1
Test B - Priority 2
Test C - Priority 3
Test Cleanup - Priority 100 (runs last)
```

**Hints:**
- Lower priority number = runs earlier
- Default priority is 0
- Negative priorities run before default

---

## 💪 Core Practice (30 min)

### Exercise 4: Test Dependencies
**Objective:** Create a user workflow with dependent tests

**Problem Statement:**
Build a test suite for a user registration and login flow where tests depend on each other. If registration fails, login shouldn't run.

**Requirements:**
- Test 1: Verify registration page loads
- Test 2: Register new user (depends on Test 1)
- Test 3: Login with new user (depends on Test 2)
- Test 4: Verify dashboard after login (depends on Test 3)
- Test 5: Logout (depends on Test 3)

**Starter Code:**
```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class Exercise4_Dependencies {
    String userId = null;
    boolean isLoggedIn = false;

    @Test
    public void test1_VerifyRegistrationPage() {
        System.out.println("1. Verifying registration page loads...");
        // TODO: Simulate page load check
        boolean pageLoaded = true;  // Simulate
        Assert.assertTrue(pageLoaded, "Registration page should load");
        System.out.println("   ✓ Registration page loaded");
    }

    @Test(dependsOnMethods = {"test1_VerifyRegistrationPage"})
    public void test2_RegisterUser() {
        System.out.println("2. Registering new user...");
        // TODO: Simulate user registration
        userId = "USER_" + System.currentTimeMillis();
        Assert.assertNotNull(userId, "User ID should be generated");
        System.out.println("   ✓ User registered with ID: " + userId);
    }

    @Test(dependsOnMethods = {"test2_RegisterUser"})
    public void test3_LoginUser() {
        System.out.println("3. Logging in...");
        // TODO: Simulate login
        Assert.assertNotNull(userId, "User should exist before login");
        isLoggedIn = true;
        System.out.println("   ✓ User logged in successfully");
    }

    @Test(dependsOnMethods = {"test3_LoginUser"})
    public void test4_VerifyDashboard() {
        System.out.println("4. Verifying dashboard...");
        // TODO: Verify dashboard elements
        Assert.assertTrue(isLoggedIn, "User must be logged in to access dashboard");
        System.out.println("   ✓ Dashboard verified");
    }

    @Test(dependsOnMethods = {"test3_LoginUser"})
    public void test5_Logout() {
        System.out.println("5. Logging out...");
        // TODO: Simulate logout
        Assert.assertTrue(isLoggedIn, "User must be logged in to logout");
        isLoggedIn = false;
        System.out.println("   ✓ User logged out");
    }
}
```

**Testing:**
1. Run all tests - all should pass
2. Add `Assert.fail()` in test2_RegisterUser
3. Verify that test3, test4, test5 are SKIPPED

**Hints:**
- If a test fails, all tests that depend on it are skipped
- Multiple tests can depend on the same test
- Use descriptive test names with step numbers

---

### Exercise 5: Real Selenium Test with Groups and Priority
**Objective:** Build a complete Selenium test suite with organization

**Problem Statement:**
Create a test suite for Sauce Demo with:
- Smoke tests (login, verify products page)
- Regression tests (product details, cart operations)
- Proper execution order
- Test isolation (each test has fresh browser)

**Requirements:**
- Use @BeforeMethod/@AfterMethod for browser management
- Use @BeforeClass to store test credentials
- Categorize tests into smoke/regression groups
- Use priority to control execution order

**Starter Code:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import java.time.Duration;

public class Exercise5_OrganizedSuite {
    WebDriver driver;
    static String baseUrl;
    static String username;
    static String password;

    @BeforeClass
    public void setupTestData() {
        // TODO: Initialize test data (runs ONCE)
        baseUrl = "https://www.saucedemo.com";
        username = "standard_user";
        password = "secret_sauce";
        System.out.println("✓ Test data initialized");
    }

    @BeforeMethod
    public void setupBrowser() {
        // TODO: Initialize browser (runs BEFORE EACH test)
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        System.out.println("✓ Browser opened");
    }

    @AfterMethod
    public void teardownBrowser() {
        // TODO: Close browser (runs AFTER EACH test)
        if (driver != null) {
            driver.quit();
            System.out.println("✓ Browser closed\n");
        }
    }

    @Test(priority = 1, groups = {"smoke", "login"})
    public void test1_VerifyLoginPageLoad() {
        System.out.println("TEST 1: Verify Login Page");
        // TODO: Navigate to baseUrl
        // TODO: Verify login button is displayed
        // TODO: Assert page title or URL
    }

    @Test(priority = 2, groups = {"smoke", "login"})
    public void test2_VerifyValidLogin() {
        System.out.println("TEST 2: Valid Login");
        // TODO: Navigate and login
        // TODO: Verify redirect to products page
    }

    @Test(priority = 3, groups = {"regression", "login"})
    public void test3_VerifyInvalidLogin() {
        System.out.println("TEST 3: Invalid Login");
        // TODO: Try invalid credentials
        // TODO: Verify error message appears
    }

    @Test(priority = 4, groups = {"smoke", "products"})
    public void test4_VerifyProductsDisplay() {
        System.out.println("TEST 4: Products Display");
        // TODO: Login first
        // TODO: Verify products are displayed
    }

    @Test(priority = 5, groups = {"regression", "products"})
    public void test5_VerifyProductCount() {
        System.out.println("TEST 5: Product Count");
        // TODO: Login and count products
        // TODO: Assert count equals 6
    }
}
```

**Testing:**
1. Run all tests - verify execution order by priority
2. Run only "smoke" group - only tests 1, 2, 4 should run
3. Run only "regression" group - only tests 3, 5 should run

**Hints:**
- Each test gets fresh browser from @BeforeMethod
- @BeforeClass data is shared across all tests
- Combine priority and groups for organized execution

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Complete E-commerce Test Suite
**Objective:** Build a production-ready test suite with all Day 2 concepts

**Requirements:**
Create a comprehensive test suite that includes:
1. **@BeforeClass**: Load configuration (URL, credentials)
2. **@BeforeMethod**: Open browser
3. **@AfterMethod**: Close browser
4. **Test Groups**: smoke, regression, api
5. **Test Priority**: Ordered execution
6. **Test Dependencies**: Cart tests depend on login
7. **Multiple test classes**: LoginTests, ProductTests, CartTests

**Starter Code:**
```java
// LoginTests.java
import org.testng.annotations.*;

public class LoginTests {
    static String baseUrl;

    @BeforeClass
    public void setupClass() {
        baseUrl = "https://www.saucedemo.com";
        System.out.println("=== LOGIN TESTS SUITE ===");
    }

    @Test(priority = 1, groups = {"smoke", "login"})
    public void verifyLoginWithValidCredentials() {
        // TODO: Implement
    }

    @Test(priority = 2, groups = {"regression", "login"})
    public void verifyLoginWithInvalidCredentials() {
        // TODO: Implement
    }

    @Test(priority = 3, groups = {"regression", "login"})
    public void verifyLockedUserLogin() {
        // TODO: Implement locked_out_user scenario
    }

    @AfterClass
    public void cleanupClass() {
        System.out.println("=== LOGIN TESTS COMPLETE ===\n");
    }
}

// ProductTests.java
public class ProductTests {

    @BeforeClass
    public void setupClass() {
        System.out.println("=== PRODUCT TESTS SUITE ===");
    }

    @Test(priority = 10, groups = {"smoke", "products"}, dependsOnGroups = {"login"})
    public void verifyProductsPageLoad() {
        // TODO: Depends on login group passing
    }

    @Test(priority = 11, groups = {"regression", "products"})
    public void verifyProductFiltering() {
        // TODO: Test product filters
    }

    @Test(priority = 12, groups = {"regression", "products"})
    public void verifyProductDetails() {
        // TODO: Click product and verify details page
    }

    @AfterClass
    public void cleanupClass() {
        System.out.println("=== PRODUCT TESTS COMPLETE ===\n");
    }
}

// CartTests.java
public class CartTests {

    @BeforeClass
    public void setupClass() {
        System.out.println("=== CART TESTS SUITE ===");
    }

    @Test(priority = 20, groups = {"smoke", "cart"}, dependsOnGroups = {"login"})
    public void verifyAddToCart() {
        // TODO: Add item to cart
    }

    @Test(priority = 21, groups = {"regression", "cart"})
    public void verifyRemoveFromCart() {
        // TODO: Remove item from cart
    }

    @Test(priority = 22, groups = {"smoke", "cart"})
    public void verifyCheckout() {
        // TODO: Complete checkout flow
    }

    @AfterClass
    public void cleanupClass() {
        System.out.println("=== CART TESTS COMPLETE ===\n");
    }
}
```

**Create testng.xml:**
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="E-commerce Test Suite">
    <!-- Smoke Tests -->
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="LoginTests"/>
            <class name="ProductTests"/>
            <class name="CartTests"/>
        </classes>
    </test>

    <!-- Regression Tests -->
    <test name="Regression Tests">
        <groups>
            <run>
                <include name="regression"/>
            </run>
        </groups>
        <classes>
            <class name="LoginTests"/>
            <class name="ProductTests"/>
            <class name="CartTests"/>
        </classes>
    </test>
</suite>
```

**Bonus Challenges:**
- [ ] Add @BeforeSuite to initialize WebDriver property once
- [ ] Add @AfterSuite to generate summary report
- [ ] Implement actual Selenium code for all TODO sections
- [ ] Add soft assertions to verify multiple elements

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
import org.testng.annotations.*;

public class Exercise1_LifecycleDemo {

    @BeforeSuite
    public void beforeSuite() {
        System.out.println("1. BeforeSuite executed");
    }

    @BeforeTest
    public void beforeTest() {
        System.out.println("2. BeforeTest executed");
    }

    @BeforeClass
    public void beforeClass() {
        System.out.println("3. BeforeClass executed");
    }

    @BeforeMethod
    public void beforeMethod() {
        System.out.println("4. BeforeMethod executed");
    }

    @Test
    public void test1() {
        System.out.println("   → Test 1");
    }

    @Test
    public void test2() {
        System.out.println("   → Test 2");
    }

    @AfterMethod
    public void afterMethod() {
        System.out.println("5. AfterMethod executed\n");
    }

    @AfterClass
    public void afterClass() {
        System.out.println("6. AfterClass executed");
    }

    @AfterTest
    public void afterTest() {
        System.out.println("7. AfterTest executed");
    }

    @AfterSuite
    public void afterSuite() {
        System.out.println("8. AfterSuite executed");
    }
}
```

**Key Takeaways:**
- Suite annotations run once for entire execution
- Class annotations run once per class
- Method annotations run for each test
- This hierarchy provides flexibility for different setup/teardown needs

---

### Exercise 2 Solution
```java
import org.testng.annotations.Test;

public class Exercise2_Groups {

    @Test(groups = {"smoke"})
    public void testCriticalFeature1() {
        System.out.println("SMOKE: Critical Feature 1");
    }

    @Test(groups = {"smoke"})
    public void testCriticalFeature2() {
        System.out.println("SMOKE: Critical Feature 2");
    }

    @Test(groups = {"regression"})
    public void testDetailedFeature1() {
        System.out.println("REGRESSION: Detailed Feature 1");
    }

    @Test(groups = {"regression"})
    public void testDetailedFeature2() {
        System.out.println("REGRESSION: Detailed Feature 2");
    }

    @Test(groups = {"smoke", "regression"})
    public void testImportantFeature() {
        System.out.println("BOTH: Important Feature (in both groups)");
    }

    @Test
    public void testUngrouped() {
        System.out.println("UNGROUPED: This test has no group");
    }
}
```

**testng.xml for smoke only:**
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Smoke Suite">
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="Exercise2_Groups"/>
        </classes>
    </test>
</suite>
```

**Key Takeaways:**
- Tests can belong to multiple groups
- Groups enable selective test execution
- Essential for CI/CD where you run different test sets

---

### Exercise 3 Solution
```java
import org.testng.annotations.Test;

public class Exercise3_Priority {

    @Test(priority = 3)
    public void testC() {
        System.out.println("Test C - Priority 3");
    }

    @Test(priority = 1)
    public void testA() {
        System.out.println("Test A - Priority 1");
    }

    @Test
    public void testDefault() {
        System.out.println("Test Default - Priority 0");
    }

    @Test(priority = 2)
    public void testB() {
        System.out.println("Test B - Priority 2");
    }

    @Test(priority = -1)
    public void testSetup() {
        System.out.println("Test Setup - Priority -1 (runs first)");
    }

    @Test(priority = 100)
    public void testCleanup() {
        System.out.println("Test Cleanup - Priority 100 (runs last)");
    }
}
```

**Key Takeaways:**
- Priority controls execution order
- Lower number = earlier execution
- Use gaps (1, 10, 20) for flexibility
- Useful for setup/cleanup tests

---

### Exercise 4 Solution
(Already complete in starter code - just needs testing)

**Testing Scenario 1: All Pass**
```
1. Verifying registration page loads...
   ✓ Registration page loaded
2. Registering new user...
   ✓ User registered with ID: USER_1234567890
3. Logging in...
   ✓ User logged in successfully
4. Verifying dashboard...
   ✓ Dashboard verified
5. Logging out...
   ✓ User logged out
```

**Testing Scenario 2: Registration Fails**
```
1. Verifying registration page loads...
   ✓ Registration page loaded
2. Registering new user...
   ✗ FAILED
3. Logging in...
   ⊘ SKIPPED (depends on test2_RegisterUser)
4. Verifying dashboard...
   ⊘ SKIPPED (depends on test3_LoginUser)
5. Logging out...
   ⊘ SKIPPED (depends on test3_LoginUser)
```

**Key Takeaways:**
- Dependencies create test chains
- If one fails, dependent tests skip
- Good for user workflows where order matters

---

### Exercise 5 Solution
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import java.time.Duration;
import java.util.List;

public class Exercise5_OrganizedSuite {
    WebDriver driver;
    static String baseUrl;
    static String username;
    static String password;

    @BeforeClass
    public void setupTestData() {
        baseUrl = "https://www.saucedemo.com";
        username = "standard_user";
        password = "secret_sauce";
        System.out.println("✓ Test data initialized");
    }

    @BeforeMethod
    public void setupBrowser() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        System.out.println("✓ Browser opened");
    }

    @AfterMethod
    public void teardownBrowser() {
        if (driver != null) {
            driver.quit();
            System.out.println("✓ Browser closed\n");
        }
    }

    @Test(priority = 1, groups = {"smoke", "login"})
    public void test1_VerifyLoginPageLoad() {
        System.out.println("TEST 1: Verify Login Page");
        driver.get(baseUrl);

        WebElement loginButton = driver.findElement(By.id("login-button"));
        Assert.assertTrue(loginButton.isDisplayed(), "Login button should be visible");
        System.out.println("   ✓ Login page loaded successfully");
    }

    @Test(priority = 2, groups = {"smoke", "login"})
    public void test2_VerifyValidLogin() {
        System.out.println("TEST 2: Valid Login");
        driver.get(baseUrl);

        driver.findElement(By.id("user-name")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login-button")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("inventory"),
            "Should redirect to inventory page");
        System.out.println("   ✓ Valid login successful");
    }

    @Test(priority = 3, groups = {"regression", "login"})
    public void test3_VerifyInvalidLogin() {
        System.out.println("TEST 3: Invalid Login");
        driver.get(baseUrl);

        driver.findElement(By.id("user-name")).sendKeys("invalid_user");
        driver.findElement(By.id("password")).sendKeys("wrong_password");
        driver.findElement(By.id("login-button")).click();

        WebElement error = driver.findElement(By.cssSelector("[data-test='error']"));
        Assert.assertTrue(error.isDisplayed(), "Error message should appear");
        System.out.println("   ✓ Invalid login handled correctly");
    }

    @Test(priority = 4, groups = {"smoke", "products"})
    public void test4_VerifyProductsDisplay() {
        System.out.println("TEST 4: Products Display");
        driver.get(baseUrl);

        // Login first
        driver.findElement(By.id("user-name")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login-button")).click();

        WebElement title = driver.findElement(By.className("title"));
        Assert.assertEquals(title.getText(), "Products");
        System.out.println("   ✓ Products page displayed");
    }

    @Test(priority = 5, groups = {"regression", "products"})
    public void test5_VerifyProductCount() {
        System.out.println("TEST 5: Product Count");
        driver.get(baseUrl);

        // Login
        driver.findElement(By.id("user-name")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login-button")).click();

        List<WebElement> products = driver.findElements(By.className("inventory_item"));
        Assert.assertEquals(products.size(), 6, "Should have 6 products");
        System.out.println("   ✓ Product count verified: " + products.size());
    }
}
```

**Key Takeaways:**
- @BeforeClass for shared data (efficient)
- @BeforeMethod for isolated resources (browser)
- Priority + Groups = organized execution
- Each test is independent with fresh browser

---

## 📊 Self-Assessment

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Lifecycle | ☐ | __/10 | ___ min | |
| 2 - Groups | ☐ | __/10 | ___ min | |
| 3 - Priority | ☐ | __/10 | ___ min | |
| 4 - Dependencies | ☐ | __/10 | ___ min | |
| 5 - Organized Suite | ☐ | __/10 | ___ min | |
| 6 - Complete Suite | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

---

## ✅ Answers to Self-Check Questions

1. **If you have 5 test methods in a class, how many times does @BeforeClass run?**
   - Answer: **Once** - @BeforeClass runs once before the first test in the class

2. **What happens to dependent tests if the prerequisite test fails?**
   - Answer: They are **SKIPPED** - TestNG marks them as skipped, not failed

3. **Can a test belong to multiple groups? How do you specify this?**
   - Answer: **Yes** - Use array syntax: `@Test(groups = {"smoke", "regression"})`

4. **What's the difference between dependsOnMethods and dependsOnGroups?**
   - Answer: `dependsOnMethods` depends on specific method names. `dependsOnGroups` depends on ALL tests in specified groups passing.

5. **If you don't specify priority, what priority does a test have by default?**
   - Answer: **Priority 0** - Tests without priority run before priority 1, after priority -1

---

**🎉 Day 2 Practice Complete! Move to Day-2-Debug-Practice.md**
