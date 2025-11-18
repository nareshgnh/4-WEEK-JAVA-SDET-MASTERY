# DAY 6 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master Page Object Model (POM) design pattern: create BasePage, implement page classes, use fluent interfaces, and structure test automation frameworks professionally.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Create BasePage Class
**Objective:** Build a reusable base class with common WebDriver methods

**Task:**
Create a BasePage class that all page objects will extend, containing utility methods for common actions.

**Starter Code:**
```java
package com.sdet.pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;

    // Constructor
    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }

    // TODO: Add method to click element with wait
    protected void click(By locator) {
        WebElement element = wait.until(ExpectedConditions.elementToBeClickable(locator));
        element.click();
    }

    // TODO: Add method to type text with wait
    protected void type(By locator, String text) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        element.clear();
        element.sendKeys(text);
    }

    // TODO: Add method to get text
    protected String getText(By locator) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        return element.getText();
    }

    // TODO: Add method to check if element is displayed
    protected boolean isDisplayed(By locator) {
        try {
            return wait.until(ExpectedConditions.visibilityOfElementLocated(locator)).isDisplayed();
        } catch (Exception e) {
            return false;
        }
    }

    // TODO: Add method to wait for URL to contain text
    protected void waitForUrl(String urlPart) {
        wait.until(ExpectedConditions.urlContains(urlPart));
    }

    // TODO: Add method to get page title
    protected String getPageTitle() {
        return driver.getTitle();
    }
}
```

**Expected Output:**
```
✓ BasePage created with common utility methods
✓ All page objects can extend this class
✓ Wait strategies built into each method
✓ Reusable across entire framework
```

**Key Learning:**
- BasePage centralizes common WebDriver operations
- All page classes extend BasePage to inherit utilities
- Built-in waits make interactions reliable
- Reduces code duplication across page objects

---

### Exercise 2: Create LoginPage Class
**Objective:** Build a page object class for login functionality

**Starter Code:**
```java
package com.sdet.pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPage extends BasePage {

    // Locators
    private By usernameField = By.id("user-name");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-button");
    private By errorMessage = By.cssSelector("[data-test='error']");

    // Constructor
    public LoginPage(WebDriver driver) {
        super(driver);
    }

    // TODO: Navigate to login page
    public void navigate() {
        driver.get("https://www.saucedemo.com/");
        System.out.println("✓ Navigated to login page");
    }

    // TODO: Enter username
    public void enterUsername(String username) {
        type(usernameField, username);
        System.out.println("✓ Entered username: " + username);
    }

    // TODO: Enter password
    public void enterPassword(String password) {
        type(passwordField, password);
        System.out.println("✓ Entered password");
    }

    // TODO: Click login button
    public void clickLogin() {
        click(loginButton);
        System.out.println("✓ Clicked login button");
    }

    // TODO: Complete login flow (all steps at once)
    public void login(String username, String password) {
        navigate();
        enterUsername(username);
        enterPassword(password);
        clickLogin();
    }

    // TODO: Get error message text
    public String getErrorMessage() {
        return getText(errorMessage);
    }

    // TODO: Check if error message is displayed
    public boolean isErrorDisplayed() {
        return isDisplayed(errorMessage);
    }
}
```

**Testing Code:**
```java
package com.sdet.tests;

import com.sdet.pages.LoginPage;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise2_TestLoginPage {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();

        try {
            // Create page object
            LoginPage loginPage = new LoginPage(driver);

            // Test valid login
            System.out.println("=== Test 1: Valid Login ===");
            loginPage.login("standard_user", "secret_sauce");
            Thread.sleep(2000);

            // Test invalid login
            System.out.println("\n=== Test 2: Invalid Login ===");
            driver.get("https://www.saucedemo.com/");
            loginPage.enterUsername("invalid_user");
            loginPage.enterPassword("wrong_password");
            loginPage.clickLogin();

            if (loginPage.isErrorDisplayed()) {
                System.out.println("✓ Error displayed: " + loginPage.getErrorMessage());
            }

            System.out.println("\n✓ LoginPage tests complete");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
=== Test 1: Valid Login ===
✓ Navigated to login page
✓ Entered username: standard_user
✓ Entered password
✓ Clicked login button

=== Test 2: Invalid Login ===
✓ Entered username: invalid_user
✓ Entered password
✓ Clicked login button
✓ Error displayed: Epic sadface: Username and password do not match

✓ LoginPage tests complete
```

---

### Exercise 3: Add Fluent Interface to LoginPage
**Objective:** Implement method chaining for cleaner test code

**Starter Code:**
```java
package com.sdet.pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPageFluent extends BasePage {

    private By usernameField = By.id("user-name");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-button");

    public LoginPageFluent(WebDriver driver) {
        super(driver);
    }

    // TODO: Return 'this' to enable chaining
    public LoginPageFluent navigate() {
        driver.get("https://www.saucedemo.com/");
        return this;
    }

    public LoginPageFluent enterUsername(String username) {
        type(usernameField, username);
        return this;
    }

    public LoginPageFluent enterPassword(String password) {
        type(passwordField, password);
        return this;
    }

    public LoginPageFluent clickLogin() {
        click(loginButton);
        return this;
    }

    // Alternative: Return different page object
    public ProductsPage loginAs(String username, String password) {
        navigate();
        enterUsername(username);
        enterPassword(password);
        clickLogin();
        return new ProductsPage(driver);  // Return next page
    }
}
```

**Usage Example:**
```java
// With fluent interface - method chaining
LoginPageFluent loginPage = new LoginPageFluent(driver);

loginPage.navigate()
         .enterUsername("standard_user")
         .enterPassword("secret_sauce")
         .clickLogin();

// Or even cleaner
ProductsPage productsPage = new LoginPageFluent(driver)
    .loginAs("standard_user", "secret_sauce");
```

**Key Learning:**
- Returning `this` enables method chaining
- Makes test code more readable (fluent style)
- Can return different page objects representing navigation flow
- Common pattern in modern test frameworks

---

## 💪 Core Practice (30 min)

### Exercise 4: Create ProductsPage with Component
**Objective:** Build page object with reusable component (product card)

**Starter Code:**
```java
package com.sdet.pages;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import java.util.List;

public class ProductsPage extends BasePage {

    // Locators
    private By pageTitle = By.className("title");
    private By productItems = By.className("inventory_item");
    private By shoppingCartBadge = By.className("shopping_cart_badge");

    public ProductsPage(WebDriver driver) {
        super(driver);
    }

    // TODO: Verify on products page
    public boolean isOnProductsPage() {
        waitForUrl("inventory.html");
        return isDisplayed(pageTitle);
    }

    // TODO: Get page title
    public String getTitle() {
        return getText(pageTitle);
    }

    // TODO: Get all product elements
    public List<WebElement> getAllProducts() {
        return driver.findElements(productItems);
    }

    // TODO: Get count of products
    public int getProductCount() {
        return getAllProducts().size();
    }

    // TODO: Add product to cart by name
    public void addProductToCart(String productName) {
        // Find product by name and click its "Add to cart" button
        By addButton = By.xpath(
            "//div[text()='" + productName + "']/ancestor::div[@class='inventory_item']//button"
        );
        click(addButton);
        System.out.println("✓ Added to cart: " + productName);
    }

    // TODO: Get cart badge count
    public String getCartBadgeCount() {
        return getText(shoppingCartBadge);
    }

    // TODO: Click cart icon
    public CartPage goToCart() {
        click(shoppingCartBadge);
        return new CartPage(driver);
    }
}
```

**Product Component (Bonus):**
```java
package com.sdet.components;

import org.openqa.selenium.By;
import org.openqa.selenium.WebElement;

public class ProductCard {
    private WebElement rootElement;

    public ProductCard(WebElement rootElement) {
        this.rootElement = rootElement;
    }

    public String getName() {
        return rootElement.findElement(By.className("inventory_item_name")).getText();
    }

    public String getPrice() {
        return rootElement.findElement(By.className("inventory_item_price")).getText();
    }

    public void clickAddToCart() {
        rootElement.findElement(By.tagName("button")).click();
    }
}
```

**Testing Code:**
```java
public class Exercise4_TestProductsPage {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();

        try {
            // Login first
            LoginPageFluent loginPage = new LoginPageFluent(driver);
            ProductsPage productsPage = loginPage.loginAs("standard_user", "secret_sauce");

            System.out.println("=== Products Page Test ===");

            // Verify on products page
            if (productsPage.isOnProductsPage()) {
                System.out.println("✓ On products page");
                System.out.println("✓ Page title: " + productsPage.getTitle());
                System.out.println("✓ Product count: " + productsPage.getProductCount());
            }

            // Add products to cart
            productsPage.addProductToCart("Sauce Labs Backpack");
            productsPage.addProductToCart("Sauce Labs Bike Light");

            // Check cart badge
            String cartCount = productsPage.getCartBadgeCount();
            System.out.println("✓ Cart badge shows: " + cartCount + " items");

            // Go to cart
            CartPage cartPage = productsPage.goToCart();
            System.out.println("✓ Navigated to cart page");

            System.out.println("\n✓ Products page test complete");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
=== Products Page Test ===
✓ On products page
✓ Page title: Products
✓ Product count: 6
✓ Added to cart: Sauce Labs Backpack
✓ Added to cart: Sauce Labs Bike Light
✓ Cart badge shows: 2 items
✓ Navigated to cart page

✓ Products page test complete
```

---

### Exercise 5: Create Complete Page Object Test Suite
**Objective:** Build end-to-end test using multiple page objects

**Complete Test:**
```java
package com.sdet.tests;

import com.sdet.pages.*;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise5_E2ETestWithPOM {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();

        try {
            System.out.println("=== E-COMMERCE END-TO-END TEST (POM) ===\n");

            // Step 1: Login
            System.out.println("--- Step 1: Login ---");
            LoginPageFluent loginPage = new LoginPageFluent(driver);
            ProductsPage productsPage = loginPage.loginAs("standard_user", "secret_sauce");
            Thread.sleep(1000);

            // Step 2: Browse products
            System.out.println("\n--- Step 2: Browse Products ---");
            System.out.println("Total products available: " + productsPage.getProductCount());
            Thread.sleep(1000);

            // Step 3: Add items to cart
            System.out.println("\n--- Step 3: Add Items to Cart ---");
            productsPage.addProductToCart("Sauce Labs Backpack");
            productsPage.addProductToCart("Sauce Labs Bike Light");
            productsPage.addProductToCart("Sauce Labs Bolt T-Shirt");
            System.out.println("Cart badge count: " + productsPage.getCartBadgeCount());
            Thread.sleep(1000);

            // Step 4: Go to cart
            System.out.println("\n--- Step 4: View Cart ---");
            CartPage cartPage = productsPage.goToCart();
            Thread.sleep(1000);

            // TODO: Add CartPage implementation
            // int itemsInCart = cartPage.getItemCount();
            // System.out.println("Items in cart: " + itemsInCart);

            System.out.println("\n" + "=".repeat(50));
            System.out.println("✓✓✓ E2E TEST WITH POM - PASSED ✓✓✓");
            System.out.println("=".repeat(50));

            Thread.sleep(2000);

        } catch (Exception e) {
            System.out.println("\n✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Refactor Old Test to Use POM
**Objective:** Compare code maintainability before and after POM

**Before POM (Old Style):**
```java
public class OldStyleTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Login code mixed with locators
            driver.get("https://www.saucedemo.com/");
            driver.findElement(By.id("user-name")).sendKeys("standard_user");
            driver.findElement(By.id("password")).sendKeys("secret_sauce");
            driver.findElement(By.id("login-button")).click();

            // Products page code
            WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
            wait.until(ExpectedConditions.urlContains("inventory"));

            // Add to cart
            driver.findElement(By.xpath("//div[text()='Sauce Labs Backpack']" +
                "/ancestor::div[@class='inventory_item']//button")).click();

            // If locator changes, need to update everywhere!

        } finally {
            driver.quit();
        }
    }
}
```

**After POM (Clean Style):**
```java
public class NewStyleTestWithPOM {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Much cleaner - no locators in test
            ProductsPage productsPage = new LoginPageFluent(driver)
                .loginAs("standard_user", "secret_sauce");

            productsPage.addProductToCart("Sauce Labs Backpack");

            // If locator changes, only update LoginPage class!

        } finally {
            driver.quit();
        }
    }
}
```

**Your Task:**
1. Take an old test you wrote earlier this week
2. Identify page interactions
3. Create page object classes for each page
4. Refactor test to use page objects
5. Compare before/after code

**Benefits to Document:**
- [ ] Separation of test logic from page structure
- [ ] Locators centralized in page classes
- [ ] Test code is more readable
- [ ] Easier to maintain when UI changes
- [ ] Page methods are reusable
- [ ] Less code duplication

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution: BasePage Benefits
```java
// BasePage provides:
// 1. Common wait strategies
// 2. Utility methods used by all pages
// 3. Single place to update common logic
// 4. Consistent interaction patterns

// Every page extends BasePage
public class LoginPage extends BasePage {
    // Inherits click(), type(), getText(), etc.
}
```

### Exercise 2 Solution: Page Object Structure
```java
public class LoginPage extends BasePage {
    // 1. Locators (private)
    private By usernameField = By.id("user-name");

    // 2. Constructor (initialize)
    public LoginPage(WebDriver driver) {
        super(driver);
    }

    // 3. Actions (public methods)
    public void login(String user, String pass) {
        // Use BasePage methods
        type(usernameField, user);
    }

    // 4. Verifications (public methods)
    public boolean isErrorDisplayed() {
        return isDisplayed(errorMessage);
    }
}
```

### Exercise 3 Solution: Fluent Pattern
```java
// Return 'this' for chaining
public LoginPage enterUsername(String username) {
    type(usernameField, username);
    return this;  // Enables chaining
}

// Usage
page.enterUsername("user").enterPassword("pass").clickLogin();

// Return next page object for navigation
public ProductsPage clickLogin() {
    click(loginButton);
    return new ProductsPage(driver);
}
```

---

## ✅ Answers to Self-Check Questions

**1. What is the Page Object Model pattern?**
Design pattern that creates an object repository for web elements. Each web page has a corresponding page class containing locators and methods for that page.

**2. What are the benefits of POM?**
- Separation of test code and page-specific code
- Centralized locator management
- Improved code maintainability
- Reduced code duplication
- Easier to update when UI changes
- More readable tests

**3. What should be in a Page Object class?**
```java
public class LoginPage {
    // 1. Locators (private)
    private By usernameField;

    // 2. Constructor
    public LoginPage(WebDriver driver)

    // 3. Action methods (public)
    public void login()

    // 4. Verification methods (public)
    public boolean isErrorDisplayed()
}
```

**4. What is a BasePage and why use it?**
A base class that all page objects extend. Contains common utility methods (click, type, wait, etc.) to avoid code duplication.

**5. What is a fluent interface?**
Design pattern where methods return `this` to enable method chaining, making code more readable:
```java
page.enterUsername("user")
    .enterPassword("pass")
    .clickLogin();
```

**6. Should test logic be in page objects?**
No! Page objects should only contain:
- Locators
- Actions (click, type, navigate)
- Verifications (is element displayed)

Test logic (assertions, test data, test flow) stays in test classes.

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - BasePage | ☐ | __/10 | ___ min | |
| 2 - LoginPage | ☐ | __/10 | ___ min | |
| 3 - Fluent Interface | ☐ | __/10 | ___ min | |
| 4 - ProductsPage | ☐ | __/10 | ___ min | |
| 5 - E2E Test with POM | ☐ | __/10 | ___ min | |
| 6 - Refactor to POM | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

---

## 💡 POM Best Practices

```java
// ✅ GOOD PRACTICES

// 1. One page class per page
public class LoginPage extends BasePage { }
public class ProductsPage extends BasePage { }

// 2. Private locators
private By usernameField = By.id("username");

// 3. Public action methods
public void login(String user, String pass) { }

// 4. Return page objects for navigation
public ProductsPage clickLogin() {
    click(loginButton);
    return new ProductsPage(driver);
}

// 5. Descriptive method names
public void enterUsername(String username) { }  // Clear
// NOT: setUser(String u) { }  // Unclear

// ❌ BAD PRACTICES

// Don't put assertions in page objects
public void verifyTitle() {
    assertEquals(getTitle(), "Expected");  // WRONG - belongs in test
}

// Don't expose WebElements
public WebElement getUsernameField() {
    return driver.findElement(usernameField);  // WRONG - keep internal
}

// Don't put test logic in page objects
public void testInvalidLogin() {  // WRONG - this is a test, not page action
    login("bad", "wrong");
    assert(isErrorDisplayed());
}
```

---

**Excellent work! Tomorrow: Advanced Selenium (Headless, Grid, Screenshots) 🚀**
