# DAY 3 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master WebDriver actions and wait strategies to build reliable, non-flaky tests. Learn when and how to use different wait mechanisms.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Basic Element Interactions
**Objective:** Practice click, sendKeys, clear, submit methods

**Task:**
Navigate to https://www.google.com and practice all basic interactions.

**Starter Code:**
```java
package com.sdet.actions;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise1_BasicInteractions {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://www.google.com");
            Thread.sleep(1000);

            // TODO: Find search box
            WebElement searchBox = driver.findElement(By.name("q"));

            // TODO: Type "Selenium"
            searchBox.sendKeys("Selenium");
            System.out.println("✓ Text entered");
            Thread.sleep(1000);

            // TODO: Clear the text
            searchBox.clear();
            System.out.println("✓ Text cleared");
            Thread.sleep(1000);

            // TODO: Type "WebDriver" and submit
            searchBox.sendKeys("WebDriver");
            searchBox.submit();
            System.out.println("✓ Search submitted");

            Thread.sleep(2000);

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 2: Browser Navigation Methods
**Objective:** Practice navigate(), back(), forward(), refresh()

**Starter Code:**
```java
public class Exercise2_Navigation {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();

            // TODO: Navigate to Google
            driver.get("https://www.google.com");
            System.out.println("At: " + driver.getCurrentUrl());
            Thread.sleep(2000);

            // TODO: Navigate to Wikipedia
            driver.navigate().to("https://www.wikipedia.org");
            System.out.println("At: " + driver.getCurrentUrl());
            Thread.sleep(2000);

            // TODO: Go back to Google
            driver.navigate().back();
            System.out.println("Back to: " + driver.getCurrentUrl());
            Thread.sleep(2000);

            // TODO: Go forward to Wikipedia
            driver.navigate().forward();
            System.out.println("Forward to: " + driver.getCurrentUrl());
            Thread.sleep(2000);

            // TODO: Refresh page
            driver.navigate().refresh();
            System.out.println("✓ Page refreshed");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 3: Implicit Wait
**Objective:** Set up and understand implicit waits

**Starter Code:**
```java
import java.time.Duration;

public class Exercise3_ImplicitWait {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // TODO: Set implicit wait to 10 seconds
            driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

            driver.manage().window().maximize();
            driver.get("https://www.google.com");

            // TODO: Find element (will wait up to 10 seconds if not immediately found)
            WebElement searchBox = driver.findElement(By.name("q"));
            searchBox.sendKeys("Implicit Wait Demo");

            System.out.println("✓ Element found with implicit wait");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 💪 Core Practice (30 min)

### Exercise 4: Explicit Wait with Multiple Conditions
**Objective:** Practice WebDriverWait with different ExpectedConditions

**Problem Statement:**
Navigate to a login page and use explicit waits for different element states.

**Starter Code:**
```java
import org.openqa.selenium.support.ui.WebDriverWait;
import org.openqa.selenium.support.ui.ExpectedConditions;
import java.time.Duration;

public class Exercise4_ExplicitWait {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://practicetestautomation.com/practice-test-login/");

            // Create WebDriverWait object
            WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

            // TODO: Wait for username field to be visible
            WebElement username = wait.until(
                ExpectedConditions.visibilityOfElementLocated(By.id("username"))
            );
            username.sendKeys("student");
            System.out.println("✓ Username entered (waited for visibility)");

            // TODO: Wait for password field to be clickable
            WebElement password = wait.until(
                ExpectedConditions.elementToBeClickable(By.id("password"))
            );
            password.sendKeys("Password123");
            System.out.println("✓ Password entered (waited for clickable)");

            // TODO: Wait for submit button and click
            WebElement submitBtn = wait.until(
                ExpectedConditions.elementToBeClickable(By.id("submit"))
            );
            submitBtn.click();
            System.out.println("✓ Submit clicked");

            // TODO: Wait for URL to contain "logged-in"
            wait.until(ExpectedConditions.urlContains("logged-in"));
            System.out.println("✓ URL changed (waited for URL condition)");

            // TODO: Wait for success message with specific text
            wait.until(ExpectedConditions.textToBePresentInElementLocated(
                By.className("post-title"), "Logged In Successfully"
            ));
            System.out.println("✓ Success message appeared (waited for text)");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 5: Wait for Element to Disappear
**Objective:** Wait for loading spinners or elements to become invisible

**Starter Code:**
```java
public class Exercise5_WaitForInvisibility {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/dynamic_loading/1");

            // TODO: Click Start button
            driver.findElement(By.cssSelector("#start button")).click();
            System.out.println("✓ Start clicked");

            // TODO: Wait for loading bar to disappear
            wait.until(ExpectedConditions.invisibilityOfElementLocated(
                By.id("loading")
            ));
            System.out.println("✓ Loading finished (waited for invisibility)");

            // TODO: Get final text
            WebElement result = driver.findElement(By.id("finish"));
            System.out.println("Result: " + result.getText());

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: E-commerce Product Search (Today's Mini Project)
**Objective:** Build product search automation with proper waits

**Requirements:**
- Navigate to https://www.saucedemo.com/
- Login with credentials (standard_user / secret_sauce)
- Wait for products page to load
- Find and click on first product
- Verify product details page loaded
- Add to cart
- Verify cart badge updates
- Use different wait strategies appropriately

**Starter Code:**
```java
public class Exercise6_ProductSearch {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        try {
            driver.manage().window().maximize();
            driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));

            System.out.println("=== E-COMMERCE PRODUCT SEARCH ===\n");

            // TODO: Navigate to site
            driver.get("https://www.saucedemo.com/");
            System.out.println("✓ Navigated to Sauce Demo");

            // TODO: Login
            driver.findElement(By.id("user-name")).sendKeys("standard_user");
            driver.findElement(By.id("password")).sendKeys("secret_sauce");
            driver.findElement(By.id("login-button")).click();
            System.out.println("✓ Logged in");

            // TODO: Wait for products page (URL contains inventory)
            wait.until(ExpectedConditions.urlContains("inventory"));
            System.out.println("✓ Products page loaded");

            // TODO: Wait for first product to be clickable and click
            WebElement firstProduct = wait.until(
                ExpectedConditions.elementToBeClickable(
                    By.cssSelector(".inventory_item:first-child .inventory_item_name")
                )
            );
            String productName = firstProduct.getText();
            firstProduct.click();
            System.out.println("✓ Clicked on: " + productName);

            // TODO: Wait for product details page
            wait.until(ExpectedConditions.urlContains("inventory-item"));
            System.out.println("✓ Product details page loaded");

            // TODO: Add to cart
            driver.findElement(By.id("add-to-cart-sauce-labs-backpack")).click();
            System.out.println("✓ Added to cart");

            // TODO: Wait for cart badge to show "1"
            wait.until(ExpectedConditions.textToBe(
                By.className("shopping_cart_badge"), "1"
            ));
            System.out.println("✓ Cart badge updated");

            System.out.println("\n✓✓✓ PRODUCT SEARCH - PASSED ✓✓✓");

            Thread.sleep(2000);

        } catch (Exception e) {
            System.out.println("✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 🎯 Solutions

### Exercise 1 Solution
```java
// Complete solution provided in starter code
// Key points:
// - sendKeys() types text
// - clear() removes existing text
// - submit() submits the form
```

### Exercise 4 Complete Solution
```java
// Demonstrates all common wait conditions:
// 1. visibilityOfElementLocated - element is visible
// 2. elementToBeClickable - element can be clicked
// 3. urlContains - URL has specific text
// 4. textToBePresentInElementLocated - element has specific text
```

---

## ✅ Answers to Self-Check Questions

**1. What's the difference between driver.get() and driver.navigate().to()?**
- Both navigate to URL
- `driver.get()` - Simple, most commonly used
- `driver.navigate().to()` - Part of navigation interface, allows chaining with back(), forward(), refresh()
- In practice, use `driver.get()` for initial navigation

**2. When would you use submit() vs click()?**
- `submit()` - Works on any form element, submits the entire form
- `click()` - Clicks specific element (button, link, etc.)
- Use `submit()` on input fields to submit form
- Use `click()` on buttons for explicit click action

**3. What's the difference between presenceOfElementLocated and visibilityOfElementLocated?**
- `presenceOfElementLocated` - Element exists in DOM (may not be visible)
- `visibilityOfElementLocated` - Element exists AND is visible (height/width > 0, not hidden)
- Use visibility when you need to interact with element

**4. How do you wait for an element to be clickable?**
```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement element = wait.until(
    ExpectedConditions.elementToBeClickable(By.id("submit"))
);
element.click();
```

**5. What happens if you click an element that's not clickable yet?**
- May throw `ElementNotInteractableException`
- May click wrong element if page still loading
- May fail silently if element is hidden
- Solution: Always wait for `elementToBeClickable` before clicking

---

**Excellent work! Tomorrow: Advanced Interactions (Actions class, alerts, frames) 🚀**
