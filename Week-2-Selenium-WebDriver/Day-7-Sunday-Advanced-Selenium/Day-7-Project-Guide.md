# DAY 7 PROJECT: COMPLETE POM FRAMEWORK

## Final Week 2 Project: E-commerce Test Suite

### Project Structure
```
selenium-framework/
├── src/main/java/
│   ├── pages/
│   │   ├── BasePage.java
│   │   ├── LoginPage.java
│   │   ├── ProductsPage.java
│   │   └── CartPage.java
│   └── utils/
│       ├── BrowserFactory.java
│       └── ScreenshotUtil.java
├── src/test/java/
│   └── tests/
│       ├── BaseTest.java
│       └── EcommerceTest.java
├── screenshots/
└── pom.xml
```

### BaseTest.java
```java
public class BaseTest {
    protected WebDriver driver;
    
    @BeforeMethod
    @Parameters("browser")
    public void setup(@Optional("chrome") String browser) {
        driver = BrowserFactory.getDriver(browser);
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
    }
    
    @AfterMethod
    public void tearDown(ITestResult result) {
        if (result.getStatus() == ITestResult.FAILURE) {
            ScreenshotUtil.takeScreenshot(driver, result.getName());
        }
        if (driver != null) {
            driver.quit();
        }
    }
}
```

### EcommerceTest.java
```java
public class EcommerceTest extends BaseTest {
    @Test
    public void testLoginAndAddToCart() {
        LoginPage loginPage = new LoginPage(driver);
        driver.get("https://www.saucedemo.com/");
        
        ProductsPage products = loginPage
            .enterUsername("standard_user")
            .enterPassword("secret_sauce")
            .clickLogin();
        
        assert products.isProductsPageDisplayed();
        
        products.addFirstProductToCart();
        assert products.getCartCount().equals("1");
    }
    
    @Test(groups = "smoke")
    public void testHeadlessLogin() {
        // This test runs in headless mode via testng.xml configuration
        LoginPage loginPage = new LoginPage(driver);
        driver.get("https://www.saucedemo.com/");
        
        loginPage
            .enterUsername("standard_user")
            .enterPassword("secret_sauce")
            .clickLogin();
        
        assert driver.getCurrentUrl().contains("inventory");
    }
}
```

### BrowserFactory.java
```java
public class BrowserFactory {
    public static WebDriver getDriver(String browser) {
        WebDriver driver;
        ChromeOptions options = new ChromeOptions();
        
        switch (browser.toLowerCase()) {
            case "chrome":
                options.addArguments("--start-maximized");
                driver = new ChromeDriver(options);
                break;
            case "headless":
                options.addArguments("--headless");
                options.addArguments("--disable-gpu");
                options.addArguments("--window-size=1920,1080");
                driver = new ChromeDriver(options);
                break;
            case "firefox":
                driver = new FirefoxDriver();
                break;
            default:
                driver = new ChromeDriver();
        }
        
        return driver;
    }
}
```

### ScreenshotUtil.java
```java
public class ScreenshotUtil {
    public static void takeScreenshot(WebDriver driver, String testName) {
        try {
            Files.createDirectories(Paths.get("screenshots"));
            
            TakesScreenshot screenshot = (TakesScreenshot) driver;
            File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
            String fileName = testName + "_" + System.currentTimeMillis() + ".png";
            
            Files.copy(srcFile.toPath(), Paths.get("screenshots/" + fileName));
            System.out.println("Screenshot saved: screenshots/" + fileName);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

## Learning Outcomes

**Week 2 Complete! You've mastered:**
✅ Selenium WebDriver setup and configuration
✅ All 8 locator strategies (ID, CSS, XPath, etc.)
✅ WebDriver actions and proper wait strategies
✅ Advanced interactions (Actions class, alerts, iframes, windows)
✅ WebElements handling and JavaScript Executor
✅ Page Object Model framework design
✅ Headless testing and performance optimization
✅ Cross-browser testing
✅ Screenshot capturing and error handling

**You can now:**
- Automate ANY web application
- Build maintainable test frameworks
- Handle complex UI scenarios
- Optimize test execution
- Work with production-grade automation

**Next: Week 3 - TestNG Framework for enterprise test design! 🚀**
