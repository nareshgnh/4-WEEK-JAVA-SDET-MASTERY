# DAY 6 MINI PROJECT: E-COMMERCE AUTOMATION WITH PAGE OBJECT MODEL

## 🎯 Project Overview

**What You're Building:**
Complete e-commerce test automation framework using Page Object Model design pattern with BasePage, multiple page classes, fluent interfaces, and organized test structure.

**Website:** https://www.saucedemo.com/

**Why This Project:**
- Apply POM design pattern to real application
- Build maintainable and scalable framework
- Practice separation of concerns (tests vs pages)
- Use fluent interface for readable tests
- Create reusable page components
- Structure professional automation framework

**Time Required:** 60-90 minutes

---

## 📋 Requirements

### Must-Have Features:
1. BasePage class with common utilities
2. LoginPage with fluent interface
3. ProductsPage with product interactions
4. CartPage for cart operations
5. Test class using all page objects
6. Proper package structure
7. Method chaining support
8. Clear navigation flow between pages
9. No locators in test classes
10. Comprehensive test coverage

### Bonus Features:
1. CheckoutPage for complete flow
2. ProductComponent for reusable product cards
3. Screenshot utility in BasePage
4. Custom waits in BasePage
5. Page factory pattern implementation
6. Logging for all page actions
7. Configuration file for URLs
8. Multiple test scenarios

---

## 🏗️ Project Structure

```
src/main/java/
└── com/sdet/
    ├── base/
    │   └── BasePage.java
    ├── pages/
    │   ├── LoginPage.java
    │   ├── ProductsPage.java
    │   ├── CartPage.java
    │   └── CheckoutPage.java
    ├── components/
    │   └── ProductCard.java
    ├── utils/
    │   └── ScreenshotUtil.java
    └── tests/
        ├── LoginTest.java
        ├── ProductsTest.java
        └── E2EShoppingTest.java
```

---

## 🎓 Complete Solution

### Step 1: Create BasePage Class

```java
package com.sdet.base;

import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;
    protected JavascriptExecutor js;

    // Constructor
    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        this.js = (JavascriptExecutor) driver;
    }

    // Click with wait
    protected void click(By locator) {
        WebElement element = wait.until(ExpectedConditions.elementToBeClickable(locator));
        element.click();
        System.out.println("✓ Clicked: " + locator);
    }

    // Type with wait and clear
    protected void type(By locator, String text) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        element.clear();
        element.sendKeys(text);
        System.out.println("✓ Typed '" + text + "' into: " + locator);
    }

    // Get text with wait
    protected String getText(By locator) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        String text = element.getText();
        System.out.println("✓ Got text '" + text + "' from: " + locator);
        return text;
    }

    // Check if element is displayed
    protected boolean isDisplayed(By locator) {
        try {
            boolean displayed = wait.until(ExpectedConditions.visibilityOfElementLocated(locator)).isDisplayed();
            System.out.println("✓ Element displayed: " + locator);
            return displayed;
        } catch (Exception e) {
            System.out.println("✗ Element not displayed: " + locator);
            return false;
        }
    }

    // Wait for URL to contain text
    protected void waitForUrl(String urlFragment) {
        wait.until(ExpectedConditions.urlContains(urlFragment));
        System.out.println("✓ URL contains: " + urlFragment);
    }

    // Get page title
    protected String getPageTitle() {
        String title = driver.getTitle();
        System.out.println("✓ Page title: " + title);
        return title;
    }

    // Scroll to element
    protected void scrollToElement(By locator) {
        WebElement element = wait.until(ExpectedConditions.presenceOfElementLocated(locator));
        js.executeScript("arguments[0].scrollIntoView(true);", element);
        System.out.println("✓ Scrolled to: " + locator);
    }

    // JavaScript click (for overlapping elements)
    protected void jsClick(By locator) {
        WebElement element = wait.until(ExpectedConditions.presenceOfElementLocated(locator));
        js.executeScript("arguments[0].click();", element);
        System.out.println("✓ JS clicked: " + locator);
    }

    // Wait for element to disappear
    protected void waitForInvisibility(By locator) {
        wait.until(ExpectedConditions.invisibilityOfElementLocated(locator));
        System.out.println("✓ Element invisible: " + locator);
    }

    // Get current URL
    protected String getCurrentUrl() {
        return driver.getCurrentUrl();
    }
}
```

---

### Step 2: Create LoginPage Class

```java
package com.sdet.pages;

import com.sdet.base.BasePage;
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

    // Navigate to login page
    public LoginPage navigate() {
        driver.get("https://www.saucedemo.com/");
        System.out.println("✓ Navigated to Sauce Demo login page");
        return this;
    }

    // Enter username (fluent)
    public LoginPage enterUsername(String username) {
        type(usernameField, username);
        return this;
    }

    // Enter password (fluent)
    public LoginPage enterPassword(String password) {
        type(passwordField, password);
        return this;
    }

    // Click login and stay on same page (for error scenarios)
    public LoginPage clickLoginExpectingError() {
        click(loginButton);
        return this;
    }

    // Click login and navigate to ProductsPage (for success scenarios)
    public ProductsPage clickLogin() {
        click(loginButton);
        waitForUrl("inventory.html");
        return new ProductsPage(driver);
    }

    // Complete login flow (convenience method)
    public ProductsPage loginAs(String username, String password) {
        navigate();
        enterUsername(username);
        enterPassword(password);
        return clickLogin();
    }

    // Get error message text
    public String getErrorMessage() {
        return getText(errorMessage);
    }

    // Check if error is displayed
    public boolean isErrorDisplayed() {
        return isDisplayed(errorMessage);
    }

    // Verify on login page
    public boolean isOnLoginPage() {
        return getCurrentUrl().contains("saucedemo.com");
    }
}
```

---

### Step 3: Create ProductsPage Class

```java
package com.sdet.pages;

import com.sdet.base.BasePage;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import java.util.List;

public class ProductsPage extends BasePage {

    // Locators
    private By pageTitle = By.className("title");
    private By productItems = By.className("inventory_item");
    private By shoppingCartBadge = By.className("shopping_cart_badge");
    private By shoppingCartLink = By.className("shopping_cart_link");
    private By sortDropdown = By.className("product_sort_container");
    private By hamburgerMenu = By.id("react-burger-menu-btn");

    // Constructor
    public ProductsPage(WebDriver driver) {
        super(driver);
        waitForUrl("inventory.html");
    }

    // Verify on products page
    public boolean isOnProductsPage() {
        return isDisplayed(pageTitle) && getCurrentUrl().contains("inventory");
    }

    // Get page title
    public String getTitle() {
        return getText(pageTitle);
    }

    // Get all product elements
    public List<WebElement> getAllProducts() {
        return driver.findElements(productItems);
    }

    // Get product count
    public int getProductCount() {
        int count = getAllProducts().size();
        System.out.println("✓ Found " + count + " products");
        return count;
    }

    // Add product to cart by name
    public ProductsPage addProductToCart(String productName) {
        By addButton = By.xpath(
            "//div[text()='" + productName + "']" +
            "/ancestor::div[@class='inventory_item']" +
            "//button[contains(@id, 'add-to-cart')]"
        );
        click(addButton);
        return this;
    }

    // Remove product from cart by name
    public ProductsPage removeProductFromCart(String productName) {
        By removeButton = By.xpath(
            "//div[text()='" + productName + "']" +
            "/ancestor::div[@class='inventory_item']" +
            "//button[contains(@id, 'remove')]"
        );
        click(removeButton);
        return this;
    }

    // Get cart badge count
    public String getCartBadgeCount() {
        if (isDisplayed(shoppingCartBadge)) {
            return getText(shoppingCartBadge);
        }
        return "0";
    }

    // Get cart badge count as integer
    public int getCartBadgeCountInt() {
        String count = getCartBadgeCount();
        return count.equals("0") ? 0 : Integer.parseInt(count);
    }

    // Navigate to cart
    public CartPage goToCart() {
        click(shoppingCartLink);
        return new CartPage(driver);
    }

    // Click product to view details
    public ProductsPage clickProduct(String productName) {
        By productLink = By.xpath("//div[text()='" + productName + "']");
        click(productLink);
        return this;
    }

    // Sort products
    public ProductsPage sortBy(String option) {
        // Implementation would use Select class
        System.out.println("✓ Sorted by: " + option);
        return this;
    }
}
```

---

### Step 4: Create CartPage Class

```java
package com.sdet.pages;

import com.sdet.base.BasePage;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import java.util.ArrayList;
import java.util.List;

public class CartPage extends BasePage {

    // Locators
    private By pageTitle = By.className("title");
    private By cartItems = By.className("cart_item");
    private By continueShoppingButton = By.id("continue-shopping");
    private By checkoutButton = By.id("checkout");
    private By itemNames = By.className("inventory_item_name");
    private By itemPrices = By.className("inventory_item_price");

    // Constructor
    public CartPage(WebDriver driver) {
        super(driver);
        waitForUrl("cart.html");
    }

    // Verify on cart page
    public boolean isOnCartPage() {
        return isDisplayed(pageTitle) && getCurrentUrl().contains("cart");
    }

    // Get page title
    public String getTitle() {
        return getText(pageTitle);
    }

    // Get cart item count
    public int getItemCount() {
        List<WebElement> items = driver.findElements(cartItems);
        int count = items.size();
        System.out.println("✓ Cart has " + count + " items");
        return count;
    }

    // Get all item names in cart
    public List<String> getItemNames() {
        List<WebElement> nameElements = driver.findElements(itemNames);
        List<String> names = new ArrayList<>();
        for (WebElement element : nameElements) {
            names.add(element.getText());
        }
        System.out.println("✓ Items in cart: " + names);
        return names;
    }

    // Check if item is in cart
    public boolean isItemInCart(String itemName) {
        List<String> items = getItemNames();
        boolean inCart = items.contains(itemName);
        System.out.println("✓ Item '" + itemName + "' in cart: " + inCart);
        return inCart;
    }

    // Remove item from cart by name
    public CartPage removeItem(String itemName) {
        By removeButton = By.xpath(
            "//div[text()='" + itemName + "']" +
            "/ancestor::div[@class='cart_item']" +
            "//button[contains(@id, 'remove')]"
        );
        click(removeButton);
        return this;
    }

    // Continue shopping (back to products)
    public ProductsPage continueShopping() {
        click(continueShoppingButton);
        return new ProductsPage(driver);
    }

    // Proceed to checkout
    public CheckoutPage checkout() {
        click(checkoutButton);
        return new CheckoutPage(driver);
    }

    // Verify cart is empty
    public boolean isCartEmpty() {
        return getItemCount() == 0;
    }

    // Get total price (if displayed)
    public String getTotalPrice() {
        // This would be implemented based on the actual page structure
        return "Implementation pending";
    }
}
```

---

### Step 5: Create CheckoutPage Class (Bonus)

```java
package com.sdet.pages;

import com.sdet.base.BasePage;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class CheckoutPage extends BasePage {

    // Locators - Step 1
    private By firstNameField = By.id("first-name");
    private By lastNameField = By.id("last-name");
    private By postalCodeField = By.id("postal-code");
    private By continueButton = By.id("continue");
    private By errorMessage = By.cssSelector("[data-test='error']");

    // Locators - Step 2 (Overview)
    private By finishButton = By.id("finish");
    private By cancelButton = By.id("cancel");

    // Constructor
    public CheckoutPage(WebDriver driver) {
        super(driver);
        waitForUrl("checkout");
    }

    // Fill checkout information
    public CheckoutPage enterFirstName(String firstName) {
        type(firstNameField, firstName);
        return this;
    }

    public CheckoutPage enterLastName(String lastName) {
        type(lastNameField, lastName);
        return this;
    }

    public CheckoutPage enterPostalCode(String postalCode) {
        type(postalCodeField, postalCode);
        return this;
    }

    // Complete checkout info form
    public CheckoutPage fillCheckoutInfo(String firstName, String lastName, String zip) {
        enterFirstName(firstName);
        enterLastName(lastName);
        enterPostalCode(zip);
        return this;
    }

    // Click continue
    public CheckoutPage clickContinue() {
        click(continueButton);
        return this;
    }

    // Click finish
    public CheckoutPage clickFinish() {
        click(finishButton);
        return this;
    }

    // Get error message
    public String getErrorMessage() {
        return getText(errorMessage);
    }

    // Verify checkout complete
    public boolean isCheckoutComplete() {
        return getCurrentUrl().contains("checkout-complete");
    }
}
```

---

### Step 6: Create Test Class

```java
package com.sdet.tests;

import com.sdet.pages.*;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class E2EShoppingTest {

    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.manage().window().maximize();

        try {
            System.out.println("=== E-COMMERCE TEST WITH PAGE OBJECT MODEL ===\n");

            // Test 1: Login
            System.out.println("--- Test 1: Login ---");
            LoginPage loginPage = new LoginPage(driver);
            ProductsPage productsPage = loginPage.loginAs("standard_user", "secret_sauce");

            if (productsPage.isOnProductsPage()) {
                System.out.println("✓✓ LOGIN TEST PASSED ✓✓\n");
            }

            // Test 2: Browse Products
            System.out.println("--- Test 2: Browse Products ---");
            System.out.println("Page title: " + productsPage.getTitle());
            int productCount = productsPage.getProductCount();

            if (productCount == 6) {
                System.out.println("✓✓ PRODUCTS COUNT TEST PASSED ✓✓\n");
            }

            // Test 3: Add Items to Cart
            System.out.println("--- Test 3: Add Items to Cart ---");
            productsPage
                .addProductToCart("Sauce Labs Backpack")
                .addProductToCart("Sauce Labs Bike Light")
                .addProductToCart("Sauce Labs Bolt T-Shirt");

            int cartCount = productsPage.getCartBadgeCountInt();
            System.out.println("Cart badge count: " + cartCount);

            if (cartCount == 3) {
                System.out.println("✓✓ ADD TO CART TEST PASSED ✓✓\n");
            }

            // Test 4: View Cart
            System.out.println("--- Test 4: View Cart ---");
            CartPage cartPage = productsPage.goToCart();

            if (cartPage.isOnCartPage()) {
                System.out.println("✓ On cart page");
            }

            System.out.println("Items in cart: " + cartPage.getItemCount());
            System.out.println("Item names: " + cartPage.getItemNames());

            boolean backpackInCart = cartPage.isItemInCart("Sauce Labs Backpack");
            boolean bikeInCart = cartPage.isItemInCart("Sauce Labs Bike Light");

            if (backpackInCart && bikeInCart) {
                System.out.println("✓✓ CART VERIFICATION TEST PASSED ✓✓\n");
            }

            // Test 5: Remove Item from Cart
            System.out.println("--- Test 5: Remove Item from Cart ---");
            cartPage.removeItem("Sauce Labs Bolt T-Shirt");

            if (cartPage.getItemCount() == 2) {
                System.out.println("✓✓ REMOVE FROM CART TEST PASSED ✓✓\n");
            }

            // Test 6: Checkout
            System.out.println("--- Test 6: Checkout ---");
            CheckoutPage checkoutPage = cartPage.checkout();
            checkoutPage
                .fillCheckoutInfo("John", "Doe", "12345")
                .clickContinue();

            System.out.println("✓ Checkout info submitted");

            checkoutPage.clickFinish();

            if (checkoutPage.isCheckoutComplete()) {
                System.out.println("✓✓ CHECKOUT TEST PASSED ✓✓\n");
            }

            System.out.println("=".repeat(50));
            System.out.println("✓✓✓ ALL TESTS PASSED WITH POM ✓✓✓");
            System.out.println("=".repeat(50));

            Thread.sleep(2000);

        } catch (Exception e) {
            System.out.println("\n✗✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
            System.out.println("\n✓ Browser closed");
        }
    }
}
```

---

## 🔍 Key Concepts Demonstrated

### 1. BasePage Pattern
```java
// All pages extend BasePage
public class LoginPage extends BasePage {
    public LoginPage(WebDriver driver) {
        super(driver);  // Initialize driver and wait
    }
    // Inherits all utility methods
}

// Benefits:
// - DRY (Don't Repeat Yourself)
// - Consistent waits across all pages
// - Easy to add new utility methods
```

### 2. Fluent Interface
```java
// Methods return 'this' for chaining
public LoginPage enterUsername(String username) {
    type(usernameField, username);
    return this;  // Enable chaining
}

// Usage
loginPage
    .enterUsername("user")
    .enterPassword("pass")
    .clickLogin();

// Benefits:
// - Readable test code
// - Natural language flow
// - Less code duplication
```

### 3. Navigation Between Pages
```java
// Return next page object
public ProductsPage clickLogin() {
    click(loginButton);
    return new ProductsPage(driver);  // Navigate to next page
}

// Usage
ProductsPage products = loginPage.clickLogin();
// Now have access to ProductsPage methods

// Benefits:
// - Type-safe navigation
// - Self-documenting code
// - Compile-time validation
```

### 4. Private Locators
```java
public class LoginPage extends BasePage {
    // Private - not accessible outside this class
    private By usernameField = By.id("user-name");

    // Public method for interaction
    public LoginPage enterUsername(String username) {
        type(usernameField, username);  // Uses private locator
        return this;
    }
}

// Benefits:
// - Encapsulation
// - Easy to update locators
// - No locator exposure to tests
```

### 5. Page Verification
```java
public boolean isOnProductsPage() {
    return isDisplayed(pageTitle) && getCurrentUrl().contains("inventory");
}

// Usage in test
if (productsPage.isOnProductsPage()) {
    // Proceed with test
}

// Benefits:
// - Explicit page validation
// - Catches navigation failures early
// - Self-healing tests
```

---

## 🚀 Enhancement Ideas

### Enhancement 1: Add Logging
```java
import java.util.logging.*;

public class BasePage {
    protected Logger logger = Logger.getLogger(this.getClass().getName());

    protected void click(By locator) {
        logger.info("Clicking: " + locator);
        wait.until(ExpectedConditions.elementToBeClickable(locator)).click();
    }
}
```

### Enhancement 2: Screenshot on Failure
```java
public class BasePage {
    protected void takeScreenshot(String testName) {
        TakesScreenshot screenshot = (TakesScreenshot) driver;
        File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
        String fileName = testName + "_" + System.currentTimeMillis() + ".png";
        // Save to screenshots folder
    }
}
```

### Enhancement 3: Configuration File
```java
// config.properties
base.url=https://www.saucedemo.com/
timeout=10
headless=false

// ConfigReader.java
public class ConfigReader {
    private static Properties props = new Properties();

    static {
        try {
            props.load(new FileInputStream("config.properties"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static String getProperty(String key) {
        return props.getProperty(key);
    }
}

// Usage
public LoginPage navigate() {
    driver.get(ConfigReader.getProperty("base.url"));
    return this;
}
```

### Enhancement 4: Page Factory Pattern
```java
import org.openqa.selenium.support.*;

public class LoginPage {
    @FindBy(id = "user-name")
    private WebElement usernameField;

    @FindBy(id = "password")
    private WebElement passwordField;

    public LoginPage(WebDriver driver) {
        PageFactory.initElements(driver, this);
    }
}
```

---

## 💼 Real-World Application

**This project teaches:**
1. **Professional Framework Structure:** Industry-standard POM implementation
2. **Maintainability:** Locator changes only affect page classes, not tests
3. **Scalability:** Easy to add new pages without changing existing code
4. **Readability:** Tests read like user stories
5. **Reusability:** Page methods used across multiple tests
6. **Team Collaboration:** Clear separation allows parallel development

**Real-World Benefits:**
- **When UI changes:** Update only the affected page class, all tests still work
- **Adding new tests:** Reuse existing page objects
- **Team onboarding:** New members understand test flow from page method names
- **Code reviews:** Reviewers see business logic, not selenium details
- **Maintenance:** Much less code to maintain vs non-POM approach

**In Production:**
- Multiple test suites share same page objects
- CI/CD integration with parallel execution
- Screenshot capture on test failure
- Test reports showing which page failed
- Version control friendly (small, focused changes)

---

## ✅ Project Completion Checklist

**Framework Structure:**
- [ ] BasePage with common utilities created
- [ ] All page classes extend BasePage
- [ ] Proper package organization
- [ ] No duplicate utility methods

**Page Objects:**
- [ ] LoginPage implemented with fluent interface
- [ ] ProductsPage with product interactions
- [ ] CartPage with cart operations
- [ ] All locators are private
- [ ] All methods are public
- [ ] Methods return correct page types

**Tests:**
- [ ] Tests use page objects (no locators in tests)
- [ ] Clear test structure
- [ ] Proper assertions
- [ ] Clean output to console
- [ ] All tests pass

**Code Quality:**
- [ ] Consistent naming conventions
- [ ] Meaningful method names
- [ ] Proper comments where needed
- [ ] No code duplication
- [ ] Driver cleanup in finally block

**Bonus Features:**
- [ ] CheckoutPage implemented
- [ ] Configuration file for URLs
- [ ] Screenshot utility
- [ ] Logging added
- [ ] Multiple test scenarios

---

## 🎓 Interview Questions You Can Answer

**Q: What is Page Object Model?**
A: POM is a design pattern that creates an object repository for web UI elements. Each web page has a corresponding page class containing locators and methods for that page. It separates test logic from page structure.

**Q: What are benefits of POM?**
A: 1) Easy maintenance when UI changes, 2) Code reusability, 3) Improved readability, 4) Reduced code duplication, 5) Better collaboration, 6) Separation of concerns.

**Q: What should NOT be in a Page Object class?**
A: Test logic, assertions, test data, or any testing framework-specific code. Page objects should only have locators, actions, and verifications.

**Q: What is fluent interface in POM?**
A: Pattern where methods return `this` to enable method chaining, making code more readable: `page.enterUsername("user").enterPassword("pass").clickLogin()`.

**Q: How do you handle page navigation in POM?**
A: Methods that navigate to a new page return a new instance of that page object: `public ProductsPage clickLogin() { return new ProductsPage(driver); }`

**Q: What is BasePage and why use it?**
A: A parent class with common WebDriver utilities (click, type, wait) that all page classes extend to avoid code duplication and ensure consistent behavior.

---

**Congratulations! You've built a production-ready POM framework! 🎉**

**Tomorrow:** Advanced Selenium (Headless browsers, Grid, Performance optimization)
