# DAY 3 MINI PROJECT: E-COMMERCE PRODUCT SEARCH WITH WAITS

## 🎯 Project Overview

**What You're Building:**
Complete e-commerce product search flow with proper wait strategies to ensure reliable, non-flaky automation.

**Website:** https://www.saucedemo.com/

**Why This Project:**
- Practice all WebDriver action methods
- Master different wait strategies
- Handle dynamic content loading
- Build real e-commerce test scenario

**Time Required:** 30-45 minutes

---

## 📋 Requirements

### Must-Have Features:
1. Login with valid credentials
2. Wait for products page to load
3. Search/filter products
4. Click on product to view details
5. Add product to cart
6. Verify cart updates
7. Navigate to cart
8. Verify product in cart
9. Use mix of implicit and explicit waits
10. Proper error handling and reporting

### Bonus Features:
1. Add multiple products to cart
2. Remove product from cart
3. Proceed to checkout
4. Validate price calculations
5. Take screenshots at key steps

---

## 🎓 Complete Solution

```java
package com.automation.projects;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;
import java.util.List;

public class EcommerceProductSearch {

    private static WebDriver driver;
    private static WebDriverWait wait;

    public static void main(String[] args) {
        setupDriver();

        try {
            System.out.println("=== E-COMMERCE PRODUCT SEARCH AUTOMATION ===\n");

            // Test Steps
            navigateToSite();
            login("standard_user", "secret_sauce");
            waitForProductsPage();
            String productName = selectFirstProduct();
            viewProductDetails();
            addToCart();
            verifyCartBadge("1");
            navigateToCart();
            verifyProductInCart(productName);

            System.out.println("\n" + "=".repeat(50));
            System.out.println("✓✓✓ E-COMMERCE AUTOMATION - PASSED ✓✓✓");
            System.out.println("=".repeat(50));

            Thread.sleep(2000);

        } catch (Exception e) {
            System.out.println("\n✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            cleanup();
        }
    }

    private static void setupDriver() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();

        // Implicit wait as baseline
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));

        // Explicit wait for specific conditions
        wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        System.out.println("✓ Driver setup complete");
    }

    private static void navigateToSite() {
        driver.get("https://www.saucedemo.com/");
        System.out.println("✓ Navigated to Sauce Demo");
    }

    private static void login(String username, String password) {
        // Wait for login page to be ready
        WebElement usernameField = wait.until(
            ExpectedConditions.visibilityOfElementLocated(By.id("user-name"))
        );
        usernameField.sendKeys(username);

        WebElement passwordField = driver.findElement(By.id("password"));
        passwordField.sendKeys(password);

        WebElement loginButton = wait.until(
            ExpectedConditions.elementToBeClickable(By.id("login-button"))
        );
        loginButton.click();

        System.out.println("✓ Logged in as: " + username);
    }

    private static void waitForProductsPage() {
        // Wait for URL to confirm we're on products page
        wait.until(ExpectedConditions.urlContains("inventory.html"));

        // Wait for products container to be visible
        wait.until(ExpectedConditions.visibilityOfElementLocated(
            By.className("inventory_list")
        ));

        System.out.println("✓ Products page loaded");
    }

    private static String selectFirstProduct() {
        // Wait for first product to be clickable
        WebElement firstProduct = wait.until(
            ExpectedConditions.elementToBeClickable(
                By.cssSelector(".inventory_item:first-child .inventory_item_name")
            )
        );

        String productName = firstProduct.getText();
        System.out.println("✓ Selected product: " + productName);

        firstProduct.click();

        return productName;
    }

    private static void viewProductDetails() {
        // Wait for product details page to load
        wait.until(ExpectedConditions.urlContains("inventory-item"));

        // Wait for product image to be visible (confirms page loaded)
        wait.until(ExpectedConditions.visibilityOfElementLocated(
            By.className("inventory_details_img")
        ));

        System.out.println("✓ Product details page loaded");
    }

    private static void addToCart() {
        // Wait for Add to Cart button to be clickable
        WebElement addToCartBtn = wait.until(
            ExpectedConditions.elementToBeClickable(
                By.cssSelector("button[id^='add-to-cart']")
            )
        );
        addToCartBtn.click();

        System.out.println("✓ Product added to cart");
    }

    private static void verifyCartBadge(String expectedCount) {
        // Wait for cart badge to appear with expected count
        wait.until(ExpectedConditions.textToBe(
            By.className("shopping_cart_badge"),
            expectedCount
        ));

        String actualCount = driver.findElement(By.className("shopping_cart_badge")).getText();
        System.out.println("✓ Cart badge shows: " + actualCount + " item(s)");

        if (!actualCount.equals(expectedCount)) {
            throw new AssertionError("Cart count mismatch! Expected: " + expectedCount + ", Actual: " + actualCount);
        }
    }

    private static void navigateToCart() {
        WebElement cartIcon = wait.until(
            ExpectedConditions.elementToBeClickable(By.className("shopping_cart_link"))
        );
        cartIcon.click();

        // Wait for cart page to load
        wait.until(ExpectedConditions.urlContains("cart.html"));
        System.out.println("✓ Navigated to cart");
    }

    private static void verifyProductInCart(String expectedProductName) {
        // Wait for cart items to be visible
        List<WebElement> cartItems = wait.until(
            ExpectedConditions.visibilityOfAllElementsLocatedBy(
                By.className("cart_item")
            )
        );

        System.out.println("✓ Found " + cartItems.size() + " item(s) in cart");

        // Verify our product is in the cart
        WebElement productNameElement = driver.findElement(By.className("inventory_item_name"));
        String actualProductName = productNameElement.getText();

        if (actualProductName.equals(expectedProductName)) {
            System.out.println("✓ Product verified in cart: " + actualProductName);
        } else {
            throw new AssertionError("Product mismatch! Expected: " + expectedProductName + ", Found: " + actualProductName);
        }
    }

    private static void cleanup() {
        if (driver != null) {
            driver.quit();
            System.out.println("\n✓ Browser closed");
        }
    }
}
```

---

## 🔍 Key Concepts Demonstrated

### 1. Driver Setup with Waits
```java
// Implicit wait - baseline for all findElement calls
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));

// Explicit wait - for specific conditions
wait = new WebDriverWait(driver, Duration.ofSeconds(10));
```

### 2. Wait for Page Transitions
```java
// Wait for URL change
wait.until(ExpectedConditions.urlContains("inventory.html"));
```

### 3. Wait for Element States
```java
// Wait for visible
ExpectedConditions.visibilityOfElementLocated(By.id("element"))

// Wait for clickable
ExpectedConditions.elementToBeClickable(By.id("button"))
```

### 4. Wait for Text
```java
// Wait for specific text in element
wait.until(ExpectedConditions.textToBe(
    By.className("shopping_cart_badge"), "1"
));
```

### 5. Method Extraction for Reusability
```java
// Each test step is a separate method
// Makes code readable and maintainable
private static void login(String username, String password) { }
private static void addToCart() { }
```

---

## 🚀 Enhancement Ideas

### Enhancement 1: Screenshot on Failure
```java
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;

private static void takeScreenshot(String fileName) throws Exception {
    TakesScreenshot screenshot = (TakesScreenshot) driver;
    File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
    Files.copy(srcFile.toPath(), Paths.get(fileName + ".png"));
}
```

### Enhancement 2: Add Multiple Products
```java
private static void addMultipleProducts(int count) {
    driver.navigate().back(); // Back to products page
    wait.until(ExpectedConditions.urlContains("inventory.html"));

    List<WebElement> addButtons = driver.findElements(
        By.cssSelector("button[id^='add-to-cart']")
    );

    for (int i = 0; i < Math.min(count, addButtons.size()); i++) {
        addButtons.get(i).click();
    }
}
```

### Enhancement 3: Price Validation
```java
private static void validateTotalPrice() {
    String itemPrice = driver.findElement(By.className("inventory_item_price"))
        .getText().replace("$", "");

    String subtotal = driver.findElement(By.className("summary_subtotal_label"))
        .getText().split("$")[1];

    if (itemPrice.equals(subtotal)) {
        System.out.println("✓ Price validated");
    }
}
```

---

## 💼 Real-World Application

**This project teaches:**
1. **Wait Strategy Selection:** When to use implicit vs explicit waits
2. **Element State Handling:** Waiting for different element states
3. **Page Transitions:** Handling navigation between pages
4. **Verification Patterns:** Validating UI state changes
5. **Code Organization:** Breaking tests into logical methods

**In production (Week 3):**
- Methods become Page Object classes
- Waits abstracted into BasePage
- Assertions use TestNG/JUnit
- Screenshots on test failure
- Reporting with ExtentReports

---

## ✅ Project Completion Checklist

- [ ] Login successful with waits
- [ ] Products page loads properly
- [ ] Product details accessed
- [ ] Add to cart works
- [ ] Cart badge updates correctly
- [ ] Cart page shows correct product
- [ ] All waits are explicit or implicit (no Thread.sleep)
- [ ] Code is well-organized with methods
- [ ] Console output is clear
- [ ] Error handling in place

---

**Congratulations! You've built a robust e-commerce test with proper wait strategies! 🎉**

**Tomorrow:** Advanced Interactions (Actions class for hover, drag-drop, alerts, frames, windows)
