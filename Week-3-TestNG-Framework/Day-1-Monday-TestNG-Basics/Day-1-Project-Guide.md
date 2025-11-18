# DAY 1 MINI PROJECT: SELENIUM TEST SUITE WITH TESTNG

## 🎯 Project Overview

**What You're Building:**
A complete TestNG test suite for testing the Sauce Demo website (https://www.saucedemo.com) - a practice e-commerce site designed for automation testing.

**Why This Project:**
- Applies all Day 1 TestNG concepts (annotations, assertions, lifecycle)
- Uses real Selenium WebDriver with proper TestNG structure
- Creates reusable test patterns you'll use in professional frameworks
- Tests a complete user workflow (login → product selection → verification)

**Time Required:** 45-60 minutes

**Skills Practiced:**
- TestNG annotations (@BeforeMethod, @AfterMethod, @Test)
- Hard and soft assertions
- WebDriver integration with TestNG
- Test organization and structure
- Professional test practices

---

## 📋 Requirements

### Must-Have Features:

1. **Base Test Setup**
   - WebDriver initialization in @BeforeMethod
   - Browser cleanup in @AfterMethod
   - Proper implicit wait configuration

2. **Login Tests**
   - Test valid login
   - Test invalid login
   - Verify error messages

3. **Product Page Tests**
   - Verify product page loads
   - Verify product count
   - Verify specific product exists

4. **Assertions**
   - Use both hard and soft assertions
   - Include descriptive failure messages
   - Validate multiple UI elements

5. **Code Quality**
   - Proper naming conventions
   - Comments explaining key sections
   - No hardcoded waits (use implicit waits)

### Nice-to-Have (Bonus):
1. Add test for logout functionality
2. Verify page URLs
3. Take screenshot on test failure (advanced - we'll cover this later in the week)

---

## 🏗️ Project Structure

**Classes Needed:**
1. `SauceDemoTests.java` - Main test class with all test methods

**Test Methods to Implement:**
- `@BeforeMethod setup()` - Initialize WebDriver and navigate to site
- `@AfterMethod teardown()` - Close browser
- `@Test testValidLogin()` - Test login with correct credentials
- `@Test testInvalidLogin()` - Test login with wrong credentials
- `@Test testProductPageLoad()` - Verify products page elements
- `@Test testProductInventoryCount()` - Count products on page
- `@Test testMultipleAssertionsWithSoftAssert()` - Use soft assertions

---

## 📝 Step-by-Step Guide

### Step 1: Set Up Project Structure and Imports

Create a new Java class with all necessary imports.

```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import org.testng.asserts.SoftAssert;
import java.time.Duration;
import java.util.List;

public class SauceDemoTests {
    // Instance variable for WebDriver
    WebDriver driver;

    // Test data
    String baseUrl = "https://www.saucedemo.com";
    String validUsername = "standard_user";
    String validPassword = "secret_sauce";
    String invalidUsername = "invalid_user";
    String invalidPassword = "wrong_password";
}
```

**Explanation:**
- `WebDriver driver` - Instance variable accessible to all test methods
- Constants for URL and credentials - easier to maintain
- Import all necessary TestNG and Selenium packages

---

### Step 2: Implement Setup and Teardown

**What to do:**
Create methods that run before and after each test to manage browser lifecycle.

**Code Template:**
```java
@BeforeMethod
public void setup() {
    // TODO: Initialize ChromeDriver
    driver = new ChromeDriver();

    // TODO: Maximize browser window
    driver.manage().window().maximize();

    // TODO: Set implicit wait
    driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

    // TODO: Navigate to base URL
    driver.get(baseUrl);

    System.out.println("✓ Browser opened and navigated to: " + baseUrl);
}

@AfterMethod
public void teardown() {
    // TODO: Close browser if driver is not null
    if (driver != null) {
        driver.quit();
        System.out.println("✓ Browser closed");
    }
}
```

**Testing:**
Run a simple test to verify setup/teardown work correctly.

```java
@Test
public void testSetupAndTeardown() {
    System.out.println("Test executing - browser should be open!");
    Assert.assertNotNull(driver, "Driver should be initialized");
}
```

---

### Step 3: Implement Valid Login Test

**What to do:**
Create a test that logs in with valid credentials and verifies successful login.

**Code Template:**
```java
@Test
public void testValidLogin() {
    System.out.println("\n>>> TEST: Valid Login");

    // TODO: Find username field and enter valid username
    WebElement usernameField = driver.findElement(By.id("user-name"));
    usernameField.sendKeys(validUsername);

    // TODO: Find password field and enter valid password
    WebElement passwordField = driver.findElement(By.id("password"));
    passwordField.sendKeys(validPassword);

    // TODO: Click login button
    WebElement loginButton = driver.findElement(By.id("login-button"));
    loginButton.click();

    // TODO: Verify we're on the products page by checking URL
    String currentUrl = driver.getCurrentUrl();
    Assert.assertTrue(currentUrl.contains("inventory.html"),
        "After valid login, should be on inventory page. Current URL: " + currentUrl);

    // TODO: Verify products page title/header is visible
    WebElement productsTitle = driver.findElement(By.className("title"));
    Assert.assertEquals(productsTitle.getText(), "Products",
        "Products page title should be 'Products'");

    System.out.println("✓ Valid login successful");
}
```

**Testing:**
Run this test. It should:
1. Open browser
2. Navigate to Sauce Demo
3. Enter credentials
4. Click login
5. Verify redirection to products page
6. Close browser

---

### Step 4: Implement Invalid Login Test

**What to do:**
Test that invalid credentials show appropriate error message.

**Code Template:**
```java
@Test
public void testInvalidLogin() {
    System.out.println("\n>>> TEST: Invalid Login");

    // TODO: Enter invalid credentials
    driver.findElement(By.id("user-name")).sendKeys(invalidUsername);
    driver.findElement(By.id("password")).sendKeys(invalidPassword);

    // TODO: Click login button
    driver.findElement(By.id("login-button")).click();

    // TODO: Verify error message appears
    WebElement errorMessage = driver.findElement(By.cssSelector("[data-test='error']"));
    Assert.assertTrue(errorMessage.isDisplayed(),
        "Error message should be visible for invalid login");

    // TODO: Verify error message text
    String errorText = errorMessage.getText();
    Assert.assertTrue(errorText.contains("Username and password do not match"),
        "Error message should mention invalid credentials. Actual: " + errorText);

    // TODO: Verify we're still on login page (URL hasn't changed)
    String currentUrl = driver.getCurrentUrl();
    Assert.assertEquals(currentUrl, baseUrl + "/",
        "Should remain on login page after invalid login");

    System.out.println("✓ Invalid login handled correctly with error message");
}
```

**Testing:**
Run this test. It should verify that:
1. Invalid credentials don't allow login
2. Error message is displayed
3. User remains on login page

---

### Step 5: Implement Product Page Tests

**What to do:**
After successful login, verify the products page has expected elements.

**Code Template:**
```java
@Test
public void testProductPageLoad() {
    System.out.println("\n>>> TEST: Product Page Load");

    // First, login (reuse login logic)
    driver.findElement(By.id("user-name")).sendKeys(validUsername);
    driver.findElement(By.id("password")).sendKeys(validPassword);
    driver.findElement(By.id("login-button")).click();

    // TODO: Verify page title
    WebElement title = driver.findElement(By.className("title"));
    Assert.assertEquals(title.getText(), "Products");

    // TODO: Verify shopping cart icon is present
    WebElement cartIcon = driver.findElement(By.className("shopping_cart_link"));
    Assert.assertTrue(cartIcon.isDisplayed(), "Shopping cart should be visible");

    // TODO: Verify filter dropdown is present
    WebElement filterDropdown = driver.findElement(By.className("product_sort_container"));
    Assert.assertTrue(filterDropdown.isDisplayed(), "Filter dropdown should be visible");

    System.out.println("✓ Product page loaded with all expected elements");
}

@Test
public void testProductInventoryCount() {
    System.out.println("\n>>> TEST: Product Inventory Count");

    // Login first
    driver.findElement(By.id("user-name")).sendKeys(validUsername);
    driver.findElement(By.id("password")).sendKeys(validPassword);
    driver.findElement(By.id("login-button")).click();

    // TODO: Find all product items
    List<WebElement> products = driver.findElements(By.className("inventory_item"));

    // TODO: Verify there are 6 products (known count for this site)
    Assert.assertEquals(products.size(), 6,
        "Should have 6 products in inventory. Found: " + products.size());

    // TODO: Verify first product has a name
    WebElement firstProductName = driver.findElement(By.className("inventory_item_name"));
    Assert.assertFalse(firstProductName.getText().isEmpty(),
        "Product names should not be empty");

    System.out.println("✓ Inventory count verified: " + products.size() + " products");
}
```

**Testing:**
These tests verify the products page structure and content.

---

### Step 6: Implement Soft Assertions Test

**What to do:**
Demonstrate soft assertions by validating multiple elements on the page without stopping on first failure.

**Code Template:**
```java
@Test
public void testMultipleAssertionsWithSoftAssert() {
    System.out.println("\n>>> TEST: Multiple Validations with Soft Assert");

    // Login first
    driver.findElement(By.id("user-name")).sendKeys(validUsername);
    driver.findElement(By.id("password")).sendKeys(validPassword);
    driver.findElement(By.id("login-button")).click();

    // Create SoftAssert instance
    SoftAssert softAssert = new SoftAssert();

    // TODO: Multiple soft assertions
    // 1. Verify title
    WebElement title = driver.findElement(By.className("title"));
    softAssert.assertEquals(title.getText(), "Products", "Page title check");

    // 2. Verify URL
    softAssert.assertTrue(driver.getCurrentUrl().contains("inventory.html"), "URL check");

    // 3. Verify cart icon
    WebElement cart = driver.findElement(By.className("shopping_cart_link"));
    softAssert.assertTrue(cart.isDisplayed(), "Cart visibility check");

    // 4. Verify menu button
    WebElement menuButton = driver.findElement(By.id("react-burger-menu-btn"));
    softAssert.assertTrue(menuButton.isDisplayed(), "Menu button visibility check");

    // 5. Verify product count
    List<WebElement> products = driver.findElements(By.className("inventory_item"));
    softAssert.assertEquals(products.size(), 6, "Product count check");

    System.out.println("✓ All soft assertions checked");

    // CRITICAL: Must call assertAll() to actually fail test if any assertion failed
    softAssert.assertAll();
}
```

**Testing:**
This test validates multiple things and reports ALL failures, not just the first one.

---

## ✅ Testing Checklist

Test your project against these scenarios:

- [ ] **Setup Test**: Browser opens and navigates to URL
- [ ] **Valid Login**: Credentials work and redirect to products page
- [ ] **Invalid Login**: Wrong credentials show error message
- [ ] **Product Page**: All expected elements are present
- [ ] **Product Count**: Correct number of products displayed
- [ ] **Soft Assertions**: All validations execute and report together
- [ ] **Teardown**: Browser closes after each test
- [ ] **All Tests Pass**: Run entire test class successfully

**Expected Behavior:**
```
Running Tests:
✓ testSetupAndTeardown - PASSED
✓ testValidLogin - PASSED
✓ testInvalidLogin - PASSED
✓ testProductPageLoad - PASSED
✓ testProductInventoryCount - PASSED
✓ testMultipleAssertionsWithSoftAssert - PASSED

===============================================
Total tests run: 6, Passes: 6, Failures: 0, Skips: 0
===============================================
```

---

## 🎓 Complete Solution

### Full Implementation

**SauceDemoTests.java:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import org.testng.asserts.SoftAssert;
import java.time.Duration;
import java.util.List;

public class SauceDemoTests {
    WebDriver driver;

    // Test data
    String baseUrl = "https://www.saucedemo.com";
    String validUsername = "standard_user";
    String validPassword = "secret_sauce";
    String invalidUsername = "invalid_user";
    String invalidPassword = "wrong_password";

    @BeforeMethod
    public void setup() {
        // Initialize ChromeDriver
        driver = new ChromeDriver();

        // Maximize browser window
        driver.manage().window().maximize();

        // Set implicit wait
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

        // Navigate to base URL
        driver.get(baseUrl);

        System.out.println("✓ Browser opened and navigated to: " + baseUrl);
    }

    @AfterMethod
    public void teardown() {
        // Close browser if driver is not null
        if (driver != null) {
            driver.quit();
            System.out.println("✓ Browser closed\n");
        }
    }

    @Test
    public void testValidLogin() {
        System.out.println("\n>>> TEST: Valid Login");

        // Find and fill username
        WebElement usernameField = driver.findElement(By.id("user-name"));
        usernameField.sendKeys(validUsername);

        // Find and fill password
        WebElement passwordField = driver.findElement(By.id("password"));
        passwordField.sendKeys(validPassword);

        // Click login button
        WebElement loginButton = driver.findElement(By.id("login-button"));
        loginButton.click();

        // Verify we're on the products page
        String currentUrl = driver.getCurrentUrl();
        Assert.assertTrue(currentUrl.contains("inventory.html"),
            "After valid login, should be on inventory page. Current URL: " + currentUrl);

        // Verify products page title
        WebElement productsTitle = driver.findElement(By.className("title"));
        Assert.assertEquals(productsTitle.getText(), "Products",
            "Products page title should be 'Products'");

        System.out.println("✓ Valid login successful");
    }

    @Test
    public void testInvalidLogin() {
        System.out.println("\n>>> TEST: Invalid Login");

        // Enter invalid credentials
        driver.findElement(By.id("user-name")).sendKeys(invalidUsername);
        driver.findElement(By.id("password")).sendKeys(invalidPassword);

        // Click login button
        driver.findElement(By.id("login-button")).click();

        // Verify error message appears
        WebElement errorMessage = driver.findElement(By.cssSelector("[data-test='error']"));
        Assert.assertTrue(errorMessage.isDisplayed(),
            "Error message should be visible for invalid login");

        // Verify error message text
        String errorText = errorMessage.getText();
        Assert.assertTrue(errorText.contains("Username and password do not match"),
            "Error message should mention invalid credentials. Actual: " + errorText);

        // Verify we're still on login page
        String currentUrl = driver.getCurrentUrl();
        Assert.assertEquals(currentUrl, baseUrl + "/",
            "Should remain on login page after invalid login");

        System.out.println("✓ Invalid login handled correctly with error message");
    }

    @Test
    public void testProductPageLoad() {
        System.out.println("\n>>> TEST: Product Page Load");

        // Login first
        performLogin();

        // Verify page title
        WebElement title = driver.findElement(By.className("title"));
        Assert.assertEquals(title.getText(), "Products");

        // Verify shopping cart icon is present
        WebElement cartIcon = driver.findElement(By.className("shopping_cart_link"));
        Assert.assertTrue(cartIcon.isDisplayed(), "Shopping cart should be visible");

        // Verify filter dropdown is present
        WebElement filterDropdown = driver.findElement(By.className("product_sort_container"));
        Assert.assertTrue(filterDropdown.isDisplayed(), "Filter dropdown should be visible");

        System.out.println("✓ Product page loaded with all expected elements");
    }

    @Test
    public void testProductInventoryCount() {
        System.out.println("\n>>> TEST: Product Inventory Count");

        // Login first
        performLogin();

        // Find all product items
        List<WebElement> products = driver.findElements(By.className("inventory_item"));

        // Verify there are 6 products
        Assert.assertEquals(products.size(), 6,
            "Should have 6 products in inventory. Found: " + products.size());

        // Verify first product has a name
        WebElement firstProductName = driver.findElement(By.className("inventory_item_name"));
        Assert.assertFalse(firstProductName.getText().isEmpty(),
            "Product names should not be empty");

        System.out.println("✓ Inventory count verified: " + products.size() + " products");
    }

    @Test
    public void testMultipleAssertionsWithSoftAssert() {
        System.out.println("\n>>> TEST: Multiple Validations with Soft Assert");

        // Login first
        performLogin();

        // Create SoftAssert instance
        SoftAssert softAssert = new SoftAssert();

        // Verify title
        WebElement title = driver.findElement(By.className("title"));
        softAssert.assertEquals(title.getText(), "Products", "Page title check");

        // Verify URL
        softAssert.assertTrue(driver.getCurrentUrl().contains("inventory.html"), "URL check");

        // Verify cart icon
        WebElement cart = driver.findElement(By.className("shopping_cart_link"));
        softAssert.assertTrue(cart.isDisplayed(), "Cart visibility check");

        // Verify menu button
        WebElement menuButton = driver.findElement(By.id("react-burger-menu-btn"));
        softAssert.assertTrue(menuButton.isDisplayed(), "Menu button visibility check");

        // Verify product count
        List<WebElement> products = driver.findElements(By.className("inventory_item"));
        softAssert.assertEquals(products.size(), 6, "Product count check");

        System.out.println("✓ All soft assertions checked");

        // CRITICAL: Call assertAll() to fail test if any assertion failed
        softAssert.assertAll();
    }

    // Helper method to avoid duplicating login code
    private void performLogin() {
        driver.findElement(By.id("user-name")).sendKeys(validUsername);
        driver.findElement(By.id("password")).sendKeys(validPassword);
        driver.findElement(By.id("login-button")).click();
    }
}
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these:

1. **Add Logout Test**
   - Open menu and click logout
   - Verify return to login page
   - Use assertion to verify logout success

2. **Add to Cart Test**
   - Click "Add to Cart" button
   - Verify cart badge shows "1"
   - Navigate to cart and verify item present

3. **Test Product Details**
   - Click on a product
   - Verify product detail page loads
   - Verify product name, price, description

4. **Negative Test: Locked User**
   - Try logging in with "locked_out_user" (exists on Sauce Demo)
   - Verify appropriate error message

**Bonus Enhancement Code:**
```java
@Test
public void testAddToCart() {
    performLogin();

    // Find and click first "Add to Cart" button
    WebElement addToCartButton = driver.findElement(
        By.xpath("//button[contains(text(), 'Add to cart')]"));
    addToCartButton.click();

    // Verify cart badge shows "1"
    WebElement cartBadge = driver.findElement(By.className("shopping_cart_badge"));
    Assert.assertEquals(cartBadge.getText(), "1",
        "Cart badge should show 1 item");

    System.out.println("✓ Successfully added item to cart");
}
```

---

## 💼 How This Relates to Real SDET Work

This project demonstrates patterns you'll use daily as a SDET:

**1. Test Organization**
```java
// Professional test structure:
@BeforeMethod - setup (runs before each test)
@Test - actual test logic
@AfterMethod - cleanup (runs after each test)

// This ensures test independence - each test has fresh state
```

**2. Reusable Helper Methods**
```java
private void performLogin() { ... }

// In real frameworks, you'll have:
// - login()
// - logout()
// - navigateToPage()
// - waitForElement()
```

**3. Assertions with Messages**
```java
Assert.assertEquals(actual, expected, "Descriptive message");

// In CI/CD, this message helps debug without re-running locally
```

**4. Test Data Management**
```java
String validUsername = "standard_user";
String validPassword = "secret_sauce";

// In real projects, this comes from:
// - Property files
// - Environment variables
// - Test data builders
```

**Example from Real Framework:**
```java
// This project's pattern
@BeforeMethod
public void setup() {
    driver = new ChromeDriver();
    driver.get(baseUrl);
}

// Professional framework pattern (you'll build this Week 3 Day 7)
@BeforeMethod
public void setup() {
    driver = DriverFactory.getDriver();  // Supports multiple browsers
    driver.get(ConfigReader.getUrl());   // Reads from config file
    PageFactory.initElements(driver, this);  // Initializes page objects
}
```

---

**🎉 Day 1 Project Complete! You've built your first TestNG test suite! Move to Day-1-Cheat-Sheet.md**
