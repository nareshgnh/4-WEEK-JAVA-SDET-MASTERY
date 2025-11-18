# DAY 2 MINI PROJECT: ORGANIZED TEST SUITE

## 🎯 Project Overview

**What You're Building:**
A properly organized test suite for Sauce Demo e-commerce site with multiple test classes, groups, dependencies, and priorities - mimicking a real-world automation framework structure.

**Why This Project:**
- Demonstrates enterprise test organization patterns
- Uses all Day 2 concepts (lifecycle, groups, dependencies, priorities)
- Creates reusable structure for future projects
- Shows how professional SDET teams organize their test suites

**Time Required:** 45-60 minutes

**Skills Practiced:**
- Multiple test classes organization
- @BeforeClass/@AfterClass for shared setup
- Test groups (smoke, regression, functional areas)
- Test dependencies for user workflows
- Priority for execution control
- Professional testng.xml configuration

---

## 📋 Requirements

### Must-Have Features:

1. **Three Test Classes:**
   - LoginTests.java - All login-related tests
   - ProductTests.java - Product page tests
   - CheckoutTests.java - Cart and checkout tests

2. **Test Organization:**
   - Smoke tests (5-7 critical tests)
   - Regression tests (all tests)
   - Functional groups (login, products, checkout)

3. **Proper Lifecycle:**
   - @BeforeClass for test data setup
   - @BeforeMethod for browser initialization
   - @AfterMethod for browser cleanup
   - @AfterClass for summary

4. **Dependencies:**
   - Checkout tests depend on login working
   - Use both method and group dependencies

5. **testng.xml Configuration:**
   - Smoke suite (runs fast tests)
   - Regression suite (runs all tests)
   - Proper test organization

---

## 🏗️ Project Structure

```
src/test/java/
├── LoginTests.java
├── ProductTests.java
├── CheckoutTests.java
└── testng.xml
```

---

## 📝 Step-by-Step Guide

### Step 1: Create LoginTests Class

**File: LoginTests.java**

```java
package tests;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import java.time.Duration;

public class LoginTests {
    WebDriver driver;
    static String baseUrl;
    static String validUsername;
    static String validPassword;

    @BeforeClass
    public void setupClass() {
        // Runs ONCE before all login tests
        baseUrl = "https://www.saucedemo.com";
        validUsername = "standard_user";
        validPassword = "secret_sauce";
        System.out.println("\n===== LOGIN TESTS SUITE =====");
    }

    @BeforeMethod
    public void setupBrowser() {
        // Runs before EACH test
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        driver.get(baseUrl);
    }

    @AfterMethod
    public void teardownBrowser() {
        // Runs after EACH test
        if (driver != null) {
            driver.quit();
        }
    }

    @Test(priority = 1, groups = {"smoke", "login"})
    public void testLoginPageLoad() {
        System.out.println("TEST: Login Page Load");

        WebElement loginButton = driver.findElement(By.id("login-button"));
        Assert.assertTrue(loginButton.isDisplayed(),
            "Login button should be visible");

        WebElement logo = driver.findElement(By.className("login_logo"));
        Assert.assertTrue(logo.isDisplayed(),
            "Logo should be visible");

        System.out.println("✓ Login page loaded successfully");
    }

    @Test(priority = 2, groups = {"smoke", "login"})
    public void testValidLogin() {
        System.out.println("TEST: Valid Login");

        driver.findElement(By.id("user-name")).sendKeys(validUsername);
        driver.findElement(By.id("password")).sendKeys(validPassword);
        driver.findElement(By.id("login-button")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("inventory.html"),
            "Should redirect to inventory page after valid login");

        WebElement title = driver.findElement(By.className("title"));
        Assert.assertEquals(title.getText(), "Products",
            "Products page title should be visible");

        System.out.println("✓ Valid login successful");
    }

    @Test(priority = 3, groups = {"regression", "login"})
    public void testInvalidUsername() {
        System.out.println("TEST: Invalid Username");

        driver.findElement(By.id("user-name")).sendKeys("invalid_user");
        driver.findElement(By.id("password")).sendKeys(validPassword);
        driver.findElement(By.id("login-button")).click();

        WebElement error = driver.findElement(By.cssSelector("[data-test='error']"));
        Assert.assertTrue(error.isDisplayed(),
            "Error message should appear for invalid username");

        String errorText = error.getText();
        Assert.assertTrue(errorText.contains("Username and password do not match"),
            "Error message should mention invalid credentials");

        System.out.println("✓ Invalid username handled correctly");
    }

    @Test(priority = 4, groups = {"regression", "login"})
    public void testInvalidPassword() {
        System.out.println("TEST: Invalid Password");

        driver.findElement(By.id("user-name")).sendKeys(validUsername);
        driver.findElement(By.id("password")).sendKeys("wrongpassword");
        driver.findElement(By.id("login-button")).click();

        WebElement error = driver.findElement(By.cssSelector("[data-test='error']"));
        Assert.assertTrue(error.isDisplayed(),
            "Error message should appear for invalid password");

        System.out.println("✓ Invalid password handled correctly");
    }

    @Test(priority = 5, groups = {"regression", "login"})
    public void testLockedOutUser() {
        System.out.println("TEST: Locked Out User");

        driver.findElement(By.id("user-name")).sendKeys("locked_out_user");
        driver.findElement(By.id("password")).sendKeys(validPassword);
        driver.findElement(By.id("login-button")).click();

        WebElement error = driver.findElement(By.cssSelector("[data-test='error']"));
        Assert.assertTrue(error.getText().contains("locked out"),
            "Should show locked out error message");

        System.out.println("✓ Locked user handled correctly");
    }

    @AfterClass
    public void cleanupClass() {
        System.out.println("===== LOGIN TESTS COMPLETE =====\n");
    }
}
```

---

### Step 2: Create ProductTests Class

**File: ProductTests.java**

```java
package tests;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.Select;
import org.testng.Assert;
import org.testng.annotations.*;
import java.time.Duration;
import java.util.List;

public class ProductTests {
    WebDriver driver;
    static String baseUrl = "https://www.saucedemo.com";
    static String username = "standard_user";
    static String password = "secret_sauce";

    @BeforeClass
    public void setupClass() {
        System.out.println("\n===== PRODUCT TESTS SUITE =====");
    }

    @BeforeMethod
    public void setupBrowser() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        driver.get(baseUrl);

        // Login before each test
        driver.findElement(By.id("user-name")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login-button")).click();
    }

    @AfterMethod
    public void teardownBrowser() {
        if (driver != null) {
            driver.quit();
        }
    }

    @Test(priority = 10, groups = {"smoke", "products"}, dependsOnGroups = {"login"})
    public void testProductsPageLoad() {
        System.out.println("TEST: Products Page Load");

        WebElement title = driver.findElement(By.className("title"));
        Assert.assertEquals(title.getText(), "Products",
            "Products page title should be 'Products'");

        WebElement cart = driver.findElement(By.className("shopping_cart_link"));
        Assert.assertTrue(cart.isDisplayed(),
            "Shopping cart icon should be visible");

        System.out.println("✓ Products page loaded successfully");
    }

    @Test(priority = 11, groups = {"smoke", "products"})
    public void testProductCount() {
        System.out.println("TEST: Product Count");

        List<WebElement> products = driver.findElements(By.className("inventory_item"));
        Assert.assertEquals(products.size(), 6,
            "Should display 6 products");

        System.out.println("✓ Product count verified: " + products.size());
    }

    @Test(priority = 12, groups = {"regression", "products"})
    public void testProductDetails() {
        System.out.println("TEST: Product Details");

        WebElement firstProduct = driver.findElement(By.className("inventory_item_name"));
        String productName = firstProduct.getText();
        Assert.assertFalse(productName.isEmpty(),
            "Product name should not be empty");

        WebElement price = driver.findElement(By.className("inventory_item_price"));
        Assert.assertTrue(price.getText().startsWith("$"),
            "Price should start with $");

        System.out.println("✓ Product details displayed correctly");
    }

    @Test(priority = 13, groups = {"regression", "products"})
    public void testProductSorting() {
        System.out.println("TEST: Product Sorting");

        WebElement sortDropdown = driver.findElement(By.className("product_sort_container"));
        Select select = new Select(sortDropdown);

        // Test different sort options
        select.selectByValue("lohi");  // Price low to high
        Assert.assertEquals(select.getFirstSelectedOption().getText(), "Price (low to high)");

        select.selectByValue("hilo");  // Price high to low
        Assert.assertEquals(select.getFirstSelectedOption().getText(), "Price (high to low)");

        System.out.println("✓ Product sorting works correctly");
    }

    @AfterClass
    public void cleanupClass() {
        System.out.println("===== PRODUCT TESTS COMPLETE =====\n");
    }
}
```

---

### Step 3: Create CheckoutTests Class

**File: CheckoutTests.java**

```java
package tests;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import java.time.Duration;
import java.util.List;

public class CheckoutTests {
    WebDriver driver;
    static String baseUrl = "https://www.saucedemo.com";
    static String username = "standard_user";
    static String password = "secret_sauce";

    @BeforeClass
    public void setupClass() {
        System.out.println("\n===== CHECKOUT TESTS SUITE =====");
    }

    @BeforeMethod
    public void setupBrowser() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        driver.get(baseUrl);

        // Login
        driver.findElement(By.id("user-name")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login-button")).click();
    }

    @AfterMethod
    public void teardownBrowser() {
        if (driver != null) {
            driver.quit();
        }
    }

    @Test(priority = 20, groups = {"smoke", "checkout"}, dependsOnGroups = {"login"})
    public void testAddToCart() {
        System.out.println("TEST: Add to Cart");

        WebElement addButton = driver.findElement(
            By.xpath("//button[contains(text(), 'Add to cart')]"));
        addButton.click();

        WebElement cartBadge = driver.findElement(By.className("shopping_cart_badge"));
        Assert.assertEquals(cartBadge.getText(), "1",
            "Cart badge should show 1 item");

        System.out.println("✓ Item added to cart successfully");
    }

    @Test(priority = 21, groups = {"regression", "checkout"})
    public void testRemoveFromCart() {
        System.out.println("TEST: Remove from Cart");

        // Add item first
        driver.findElement(By.xpath("//button[contains(text(), 'Add to cart')]")).click();

        // Remove item
        driver.findElement(By.xpath("//button[contains(text(), 'Remove')]")).click();

        List<WebElement> cartBadges = driver.findElements(By.className("shopping_cart_badge"));
        Assert.assertEquals(cartBadges.size(), 0,
            "Cart badge should not be visible when cart is empty");

        System.out.println("✓ Item removed from cart successfully");
    }

    @Test(priority = 22, groups = {"smoke", "checkout"})
    public void testCartPage() {
        System.out.println("TEST: Cart Page");

        // Add item
        driver.findElement(By.xpath("//button[contains(text(), 'Add to cart')]")).click();

        // Go to cart
        driver.findElement(By.className("shopping_cart_link")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("cart.html"),
            "Should navigate to cart page");

        WebElement cartItem = driver.findElement(By.className("cart_item"));
        Assert.assertTrue(cartItem.isDisplayed(),
            "Cart item should be displayed");

        System.out.println("✓ Cart page displayed correctly");
    }

    @Test(priority = 23, groups = {"regression", "checkout"}, dependsOnMethods = {"testCartPage"})
    public void testCheckoutFlow() {
        System.out.println("TEST: Checkout Flow");

        // Add item and go to cart
        driver.findElement(By.xpath("//button[contains(text(), 'Add to cart')]")).click();
        driver.findElement(By.className("shopping_cart_link")).click();

        // Start checkout
        driver.findElement(By.id("checkout")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("checkout-step-one"),
            "Should navigate to checkout page");

        // Fill checkout info
        driver.findElement(By.id("first-name")).sendKeys("John");
        driver.findElement(By.id("last-name")).sendKeys("Doe");
        driver.findElement(By.id("postal-code")).sendKeys("12345");
        driver.findElement(By.id("continue")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("checkout-step-two"),
            "Should navigate to checkout overview");

        // Complete checkout
        driver.findElement(By.id("finish")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("checkout-complete"),
            "Should navigate to completion page");

        WebElement confirmationMessage = driver.findElement(By.className("complete-header"));
        Assert.assertEquals(confirmationMessage.getText(), "Thank you for your order!",
            "Should show confirmation message");

        System.out.println("✓ Checkout completed successfully");
    }

    @AfterClass
    public void cleanupClass() {
        System.out.println("===== CHECKOUT TESTS COMPLETE =====\n");
    }
}
```

---

### Step 4: Create testng.xml Configuration

**File: testng.xml**

```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Sauce Demo Test Suite">

    <!-- Smoke Tests Suite - Run critical tests only -->
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
            <class name="tests.CheckoutTests"/>
        </classes>
    </test>

    <!-- Regression Tests Suite - Run all tests -->
    <test name="Regression Tests">
        <groups>
            <run>
                <include name="regression"/>
            </run>
        </groups>
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
            <class name="tests.CheckoutTests"/>
        </classes>
    </test>

    <!-- Full Suite - Run everything -->
    <test name="Full Test Suite">
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
            <class name="tests.CheckoutTests"/>
        </classes>
    </test>

</suite>
```

---

## ✅ Testing Checklist

- [ ] **Smoke Suite**: Run smoke tests only (should execute ~7 tests)
- [ ] **Regression Suite**: Run regression tests (should execute all tests)
- [ ] **Full Suite**: Run all tests without group filter
- [ ] **Verify Dependencies**: Checkout tests depend on login
- [ ] **Verify Priority**: Tests execute in order within each class
- [ ] **Verify Isolation**: Each test gets fresh browser
- [ ] **Verify Cleanup**: All browsers close after tests

**Run Commands:**
```bash
# Run smoke tests only
mvn test -DsuiteXmlFile=testng.xml -Dtestng.test.name="Smoke Tests"

# Run regression tests
mvn test -DsuiteXmlFile=testng.xml -Dtestng.test.name="Regression Tests"

# Run full suite
mvn test -DsuiteXmlFile=testng.xml -Dtestng.test.name="Full Test Suite"
```

---

## 💼 How This Relates to Real SDET Work

**This project demonstrates:**

1. **Test Organization** - Separate classes for different functional areas
2. **Group Strategy** - Smoke tests run in 2 minutes, regression in 10 minutes
3. **Dependency Management** - Can't test checkout if login doesn't work
4. **Parallel-Ready** - Each test is independent, can run in parallel later
5. **Configuration Management** - testng.xml controls what runs in CI/CD

**In Real Companies:**
```
Pull Request → Run Smoke Tests (2 min) → Merge
Nightly Build → Run Regression Tests (1 hour) → Report
Release Build → Run Full Suite (3 hours) → Sign off
```

---

**🎉 Day 2 Project Complete! Move to Day-2-Cheat-Sheet.md**
