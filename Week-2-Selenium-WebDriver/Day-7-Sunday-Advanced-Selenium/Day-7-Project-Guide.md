# DAY 7 CAPSTONE PROJECT: PRODUCTION-READY TEST FRAMEWORK

## 🎯 Project Overview

**What You're Building:**
Complete production-ready test automation framework combining ALL Week 2 concepts: headless execution, cross-browser support, Page Object Model, screenshot utilities, browser factory pattern, and advanced Selenium features.

**Why This Project:**
- Consolidates all Week 2 learning
- Builds industry-standard framework structure
- Demonstrates production best practices
- Ready for CI/CD integration
- Portfolio-worthy project

**Time Required:** 90-120 minutes

---

## 📋 Requirements

### Must-Have Features:
1. Browser Factory supporting Chrome, Firefox, Edge, and headless modes
2. Screenshot utility with automatic directory creation
3. BasePage with common utilities
4. Complete POM implementation (Login, Products, Cart pages)
5. BaseTest for test infrastructure
6. Cross-browser test execution
7. Headless mode support
8. Screenshot capture on test failure
9. Proper package organization
10. Configuration via system properties

### Bonus Features:
1. Configuration file support (properties)
2. DriverManager with ThreadLocal for parallel execution
3. Custom WebDriverWait wrapper
4. Logging framework integration
5. TestNG integration with XML configuration
6. Multiple test suites (smoke, regression, full)
7. Retry logic for flaky tests
8. HTML test reports

---

## 🏗️ Complete Project Structure

```
selenium-production-framework/
├── src/main/java/com/framework/
│   ├── base/
│   │   └── BasePage.java
│   ├── pages/
│   │   ├── LoginPage.java
│   │   ├── ProductsPage.java
│   │   └── CartPage.java
│   ├── utils/
│   │   ├── BrowserFactory.java
│   │   ├── ScreenshotUtil.java
│   │   ├── DriverManager.java (bonus)
│   │   └── ConfigReader.java (bonus)
│   └── tests/
│       ├── BaseTest.java
│       ├── LoginTests.java
│       ├── ProductsTests.java
│       └── E2ETests.java
├── src/test/resources/
│   ├── testng.xml
│   ├── testng-smoke.xml
│   └── config.properties
├── screenshots/
└── pom.xml
```

---

## 🎓 Step-by-Step Build Guide

### Step 1: Create BrowserFactory (Foundation)

**File:** `src/main/java/com/framework/utils/BrowserFactory.java`

```java
package com.framework.utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.edge.EdgeOptions;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.firefox.FirefoxOptions;

public class BrowserFactory {

    /**
     * Creates WebDriver instance based on browser type and headless flag
     * @param browser Browser name (chrome, firefox, edge)
     * @param headless Run in headless mode
     * @return WebDriver instance
     */
    public static WebDriver createDriver(String browser, boolean headless) {
        WebDriver driver;

        switch (browser.toLowerCase()) {
            case "chrome":
                ChromeOptions chromeOptions = new ChromeOptions();
                configureChromeOptions(chromeOptions, headless);
                driver = new ChromeDriver(chromeOptions);
                break;

            case "firefox":
                FirefoxOptions firefoxOptions = new FirefoxOptions();
                configureFirefoxOptions(firefoxOptions, headless);
                driver = new FirefoxDriver(firefoxOptions);
                break;

            case "edge":
                EdgeOptions edgeOptions = new EdgeOptions();
                configureEdgeOptions(edgeOptions, headless);
                driver = new EdgeDriver(edgeOptions);
                break;

            default:
                System.out.println("Unknown browser: " + browser + ". Using Chrome.");
                driver = new ChromeDriver();
        }

        System.out.println("✓ Created " + browser + " driver (headless: " + headless + ")");
        return driver;
    }

    /**
     * Creates driver using system properties
     * Usage: mvn test -Dbrowser=firefox -Dheadless=true
     */
    public static WebDriver createDriver() {
        String browser = System.getProperty("browser", "chrome");
        boolean headless = Boolean.parseBoolean(System.getProperty("headless", "false"));
        return createDriver(browser, headless);
    }

    /**
     * Configures Chrome options for optimal performance
     */
    private static void configureChromeOptions(ChromeOptions options, boolean headless) {
        if (headless) {
            options.addArguments("--headless");
            options.addArguments("--window-size=1920,1080");
            options.addArguments("--disable-gpu");
            options.addArguments("--no-sandbox");
            options.addArguments("--disable-dev-shm-usage");
        } else {
            options.addArguments("--start-maximized");
        }

        // Common options for all modes
        options.addArguments("--disable-notifications");
        options.addArguments("--disable-extensions");
        options.addArguments("--disable-infobars");
        options.addArguments("--disable-blink-features=AutomationControlled");
        options.setExperimentalOption("excludeSwitches", new String[]{"enable-automation"});
    }

    /**
     * Configures Firefox options
     */
    private static void configureFirefoxOptions(FirefoxOptions options, boolean headless) {
        if (headless) {
            options.addArguments("--headless");
            options.addArguments("--width=1920");
            options.addArguments("--height=1080");
        }
    }

    /**
     * Configures Edge options
     */
    private static void configureEdgeOptions(EdgeOptions options, boolean headless) {
        if (headless) {
            options.addArguments("--headless");
            options.addArguments("--window-size=1920,1080");
        } else {
            options.addArguments("--start-maximized");
        }
    }
}
```

---

### Step 2: Create Screenshot Utility

**File:** `src/main/java/com/framework/utils/ScreenshotUtil.java`

```java
package com.framework.utils;

import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;

import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.text.SimpleDateFormat;
import java.util.Date;

public class ScreenshotUtil {

    private static final String SCREENSHOT_DIR = "screenshots/";

    // Create directory on class load
    static {
        try {
            Files.createDirectories(Paths.get(SCREENSHOT_DIR));
            System.out.println("✓ Screenshot directory created: " + SCREENSHOT_DIR);
        } catch (Exception e) {
            System.out.println("✗ Failed to create screenshot directory: " + e.getMessage());
        }
    }

    /**
     * Takes screenshot and saves with unique timestamp
     * @param driver WebDriver instance
     * @param testName Name of the test
     * @return Path to saved screenshot
     */
    public static String takeScreenshot(WebDriver driver, String testName) {
        try {
            TakesScreenshot screenshot = (TakesScreenshot) driver;
            File srcFile = screenshot.getScreenshotAs(OutputType.FILE);

            // Create unique filename
            String timestamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
            String fileName = testName + "_" + timestamp + ".png";
            String filePath = SCREENSHOT_DIR + fileName;

            // Copy to destination
            Files.copy(srcFile.toPath(), Paths.get(filePath));

            System.out.println("✓ Screenshot saved: " + filePath);
            return filePath;

        } catch (Exception e) {
            System.out.println("✗ Screenshot failed: " + e.getMessage());
            e.printStackTrace();
            return null;
        }
    }

    /**
     * Takes screenshot specifically for test failures
     */
    public static String takeFailureScreenshot(WebDriver driver, String testName) {
        return takeScreenshot(driver, "FAILED_" + testName);
    }
}
```

---

### Step 3: Create BasePage (POM Foundation)

**File:** `src/main/java/com/framework/base/BasePage.java`

```java
package com.framework.base;

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

    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        this.js = (JavascriptExecutor) driver;
    }

    // Click with explicit wait
    protected void click(By locator) {
        WebElement element = wait.until(ExpectedConditions.elementToBeClickable(locator));
        element.click();
    }

    // Type with explicit wait and clear
    protected void type(By locator, String text) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        element.clear();
        element.sendKeys(text);
    }

    // Get text with explicit wait
    protected String getText(By locator) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        return element.getText();
    }

    // Check if element is displayed
    protected boolean isDisplayed(By locator) {
        try {
            return wait.until(ExpectedConditions.visibilityOfElementLocated(locator)).isDisplayed();
        } catch (Exception e) {
            return false;
        }
    }

    // Wait for URL to contain text
    protected void waitForUrl(String urlFragment) {
        wait.until(ExpectedConditions.urlContains(urlFragment));
    }

    // Get current URL
    protected String getCurrentUrl() {
        return driver.getCurrentUrl();
    }

    // JavaScript click (for hidden/overlapping elements)
    protected void jsClick(By locator) {
        WebElement element = wait.until(ExpectedConditions.presenceOfElementLocated(locator));
        js.executeScript("arguments[0].click();", element);
    }

    // Scroll to element
    protected void scrollToElement(By locator) {
        WebElement element = wait.until(ExpectedConditions.presenceOfElementLocated(locator));
        js.executeScript("arguments[0].scrollIntoView(true);", element);
    }
}
```

---

### Step 4: Create Page Objects

**File:** `src/main/java/com/framework/pages/LoginPage.java`

```java
package com.framework.pages;

import com.framework.base.BasePage;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;

public class LoginPage extends BasePage {

    // Locators
    private By usernameField = By.id("user-name");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-button");
    private By errorMessage = By.cssSelector("[data-test='error']");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    // Actions
    public LoginPage navigate() {
        driver.get("https://www.saucedemo.com/");
        return this;
    }

    public LoginPage enterUsername(String username) {
        type(usernameField, username);
        return this;
    }

    public LoginPage enterPassword(String password) {
        type(passwordField, password);
        return this;
    }

    public ProductsPage clickLogin() {
        click(loginButton);
        waitForUrl("inventory.html");
        return new ProductsPage(driver);
    }

    // Complete login flow
    public ProductsPage loginAs(String username, String password) {
        navigate();
        enterUsername(username);
        enterPassword(password);
        return clickLogin();
    }

    // Verifications
    public boolean isErrorDisplayed() {
        return isDisplayed(errorMessage);
    }

    public String getErrorMessage() {
        return getText(errorMessage);
    }
}
```

**File:** `src/main/java/com/framework/pages/ProductsPage.java`

```java
package com.framework.pages;

import com.framework.base.BasePage;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;

import java.util.List;

public class ProductsPage extends BasePage {

    private By pageTitle = By.className("title");
    private By productItems = By.className("inventory_item");
    private By cartBadge = By.className("shopping_cart_badge");
    private By cartLink = By.className("shopping_cart_link");

    public ProductsPage(WebDriver driver) {
        super(driver);
        waitForUrl("inventory.html");
    }

    public boolean isOnProductsPage() {
        return isDisplayed(pageTitle) && getCurrentUrl().contains("inventory");
    }

    public int getProductCount() {
        List<WebElement> products = driver.findElements(productItems);
        return products.size();
    }

    public ProductsPage addProductToCart(String productName) {
        By addButton = By.xpath(
            "//div[text()='" + productName + "']" +
            "/ancestor::div[@class='inventory_item']" +
            "//button[contains(@id, 'add-to-cart')]"
        );
        click(addButton);
        return this;
    }

    public String getCartBadgeCount() {
        if (isDisplayed(cartBadge)) {
            return getText(cartBadge);
        }
        return "0";
    }

    public CartPage goToCart() {
        click(cartLink);
        return new CartPage(driver);
    }
}
```

**File:** `src/main/java/com/framework/pages/CartPage.java`

```java
package com.framework.pages;

import com.framework.base.BasePage;
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;

import java.util.ArrayList;
import java.util.List;

public class CartPage extends BasePage {

    private By pageTitle = By.className("title");
    private By cartItems = By.className("cart_item");
    private By itemNames = By.className("inventory_item_name");
    private By checkoutButton = By.id("checkout");

    public CartPage(WebDriver driver) {
        super(driver);
        waitForUrl("cart.html");
    }

    public boolean isOnCartPage() {
        return isDisplayed(pageTitle) && getCurrentUrl().contains("cart");
    }

    public int getItemCount() {
        List<WebElement> items = driver.findElements(cartItems);
        return items.size();
    }

    public List<String> getItemNames() {
        List<WebElement> nameElements = driver.findElements(itemNames);
        List<String> names = new ArrayList<>();
        for (WebElement element : nameElements) {
            names.add(element.getText());
        }
        return names;
    }

    public boolean isItemInCart(String itemName) {
        return getItemNames().contains(itemName);
    }
}
```

---

### Step 5: Create BaseTest (Test Infrastructure)

**File:** `src/main/java/com/framework/tests/BaseTest.java`

```java
package com.framework.tests;

import com.framework.utils.BrowserFactory;
import com.framework.utils.ScreenshotUtil;
import org.openqa.selenium.WebDriver;
import org.testng.ITestResult;
import org.testng.annotations.*;

import java.time.Duration;

public class BaseTest {

    protected WebDriver driver;

    @BeforeMethod
    @Parameters({"browser", "headless"})
    public void setup(
        @Optional("chrome") String browser,
        @Optional("false") String headless
    ) {
        // Create driver
        boolean isHeadless = Boolean.parseBoolean(headless);
        driver = BrowserFactory.createDriver(browser, isHeadless);

        // Configure driver
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        driver.manage().timeouts().pageLoadTimeout(Duration.ofSeconds(30));

        if (!isHeadless) {
            driver.manage().window().maximize();
        }

        System.out.println("✓ Test setup complete");
    }

    @AfterMethod
    public void tearDown(ITestResult result) {
        // Take screenshot on failure
        if (result.getStatus() == ITestResult.FAILURE) {
            System.out.println("✗ Test failed: " + result.getName());
            ScreenshotUtil.takeFailureScreenshot(driver, result.getName());
        } else {
            System.out.println("✓ Test passed: " + result.getName());
        }

        // Quit driver
        if (driver != null) {
            driver.quit();
            System.out.println("✓ Browser closed");
        }
    }
}
```

---

### Step 6: Create Tests

**File:** `src/main/java/com/framework/tests/LoginTests.java`

```java
package com.framework.tests;

import com.framework.pages.LoginPage;
import com.framework.pages.ProductsPage;
import org.testng.Assert;
import org.testng.annotations.Test;

public class LoginTests extends BaseTest {

    @Test(priority = 1, groups = {"smoke", "regression"})
    public void testValidLogin() {
        LoginPage loginPage = new LoginPage(driver);
        ProductsPage productsPage = loginPage.loginAs("standard_user", "secret_sauce");

        Assert.assertTrue(productsPage.isOnProductsPage(),
            "User should be on products page after successful login");
    }

    @Test(priority = 2, groups = {"regression"})
    public void testInvalidLogin() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.navigate()
                .enterUsername("invalid_user")
                .enterPassword("wrong_password")
                .clickLogin();

        Assert.assertTrue(loginPage.isErrorDisplayed(),
            "Error message should be displayed for invalid login");
    }

    @Test(priority = 3, groups = {"smoke"})
    public void testLockedOutUser() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.navigate()
                .enterUsername("locked_out_user")
                .enterPassword("secret_sauce")
                .clickLogin();

        Assert.assertTrue(loginPage.isErrorDisplayed(),
            "Error message should be displayed for locked out user");
        Assert.assertTrue(loginPage.getErrorMessage().contains("locked out"),
            "Error message should mention user is locked out");
    }
}
```

**File:** `src/main/java/com/framework/tests/E2ETests.java`

```java
package com.framework.tests;

import com.framework.pages.CartPage;
import com.framework.pages.LoginPage;
import com.framework.pages.ProductsPage;
import org.testng.Assert;
import org.testng.annotations.Test;

public class E2ETests extends BaseTest {

    @Test(groups = {"e2e", "regression"})
    public void testCompleteShoppingFlow() {
        // Login
        LoginPage loginPage = new LoginPage(driver);
        ProductsPage productsPage = loginPage.loginAs("standard_user", "secret_sauce");

        Assert.assertTrue(productsPage.isOnProductsPage(),
            "Should be on products page");

        // Add items to cart
        productsPage
                .addProductToCart("Sauce Labs Backpack")
                .addProductToCart("Sauce Labs Bike Light");

        String cartCount = productsPage.getCartBadgeCount();
        Assert.assertEquals(cartCount, "2",
            "Cart should show 2 items");

        // View cart
        CartPage cartPage = productsPage.goToCart();
        Assert.assertTrue(cartPage.isOnCartPage(),
            "Should be on cart page");

        Assert.assertEquals(cartPage.getItemCount(), 2,
            "Cart should contain 2 items");

        Assert.assertTrue(cartPage.isItemInCart("Sauce Labs Backpack"),
            "Cart should contain Backpack");
        Assert.assertTrue(cartPage.isItemInCart("Sauce Labs Bike Light"),
            "Cart should contain Bike Light");
    }
}
```

---

### Step 7: Create TestNG Configuration

**File:** `src/test/resources/testng.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<suite name="E-commerce Test Suite" parallel="false">

    <parameter name="browser" value="chrome"/>
    <parameter name="headless" value="false"/>

    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <packages>
            <package name="com.framework.tests"/>
        </packages>
    </test>

    <test name="Regression Tests">
        <groups>
            <run>
                <include name="regression"/>
            </run>
        </groups>
        <packages>
            <package name="com.framework.tests"/>
        </packages>
    </test>

</suite>
```

**File:** `src/test/resources/testng-headless.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<suite name="Headless Test Suite" parallel="false">

    <!-- Run all tests in headless mode for CI/CD -->
    <parameter name="browser" value="chrome"/>
    <parameter name="headless" value="true"/>

    <test name="All Tests Headless">
        <packages>
            <package name="com.framework.tests"/>
        </packages>
    </test>

</suite>
```

---

## 🚀 Running the Framework

### Command Line Execution

```bash
# Run all tests (normal mode)
mvn test

# Run smoke tests only
mvn test -DsuiteXmlFile=testng-smoke.xml

# Run in headless mode
mvn test -DsuiteXmlFile=testng-headless.xml

# Run with specific browser
mvn test -Dbrowser=firefox

# Run specific test class
mvn test -Dtest=LoginTests

# Run with custom parameters
mvn test -Dbrowser=chrome -Dheadless=true
```

---

## ✅ Project Completion Checklist

**Framework Structure:**
- [ ] BrowserFactory supports Chrome, Firefox, Edge
- [ ] Headless mode configurable
- [ ] Screenshot utility with auto directory creation
- [ ] BasePage with common utilities
- [ ] All page objects implement POM properly

**Test Infrastructure:**
- [ ] BaseTest sets up and tears down driver
- [ ] Screenshots captured on test failure
- [ ] TestNG integration working
- [ ] Multiple test suites configured

**Tests:**
- [ ] Login tests cover valid/invalid scenarios
- [ ] E2E test covers complete shopping flow
- [ ] Tests use page objects (no locators in tests)
- [ ] Proper assertions in all tests

**Execution:**
- [ ] Tests run successfully in normal mode
- [ ] Tests run successfully in headless mode
- [ ] Cross-browser execution works
- [ ] Screenshots saved on failures

**Code Quality:**
- [ ] Proper package organization
- [ ] Meaningful names for classes and methods
- [ ] Comments on complex logic
- [ ] No code duplication
- [ ] Follows POM best practices

---

## 🎓 Learning Outcomes

**Week 2 Mastery Achieved! You can now:**

✅ Build production-ready test frameworks from scratch
✅ Implement Page Object Model professionally
✅ Configure cross-browser testing
✅ Run tests in headless mode for CI/CD
✅ Capture screenshots automatically on failures
✅ Organize tests with TestNG
✅ Use factory pattern for flexible driver creation
✅ Structure code following industry standards
✅ Handle advanced Selenium scenarios
✅ Optimize test execution performance

**Technical Skills Acquired:**
- Selenium WebDriver 4
- Page Object Model design pattern
- Factory pattern for driver creation
- TestNG test organization
- Maven project structure
- Screenshot utilities
- Headless browser automation
- Cross-browser testing strategies
- CI/CD-ready test configuration

---

## 💼 Real-World Application

**This framework is ready for:**
- Production test automation
- CI/CD pipeline integration (Jenkins, GitHub Actions)
- Parallel test execution
- Cross-browser testing
- Team collaboration (multiple testers working together)
- Continuous testing
- Regression test suites

**In Your Resume:**
*"Built production-ready Selenium WebDriver framework using Page Object Model, supporting cross-browser and headless execution with automatic screenshot capture on test failures. Integrated with TestNG for test organization and CI/CD pipelines for continuous testing."*

---

## 🎉 Congratulations!

**Week 2 Complete!** You've built a comprehensive, production-ready test automation framework that demonstrates:
- Professional code organization
- Industry best practices
- Scalable architecture
- Maintainable test code
- CI/CD readiness

**Portfolio Project:** This framework is portfolio-ready and demonstrates real-world automation skills to potential employers.

**Next:** Week 3 - TestNG Framework & Advanced Testing Techniques 🚀

---

**You're now ready to automate ANY web application professionally! 🎯**
