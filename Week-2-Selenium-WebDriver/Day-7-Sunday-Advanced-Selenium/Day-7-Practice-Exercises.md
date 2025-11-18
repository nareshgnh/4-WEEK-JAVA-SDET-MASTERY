# DAY 7 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master advanced Selenium techniques: headless browsers, cross-browser testing, ChromeOptions, screenshot utilities, browser factory pattern, and performance optimization.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Headless Chrome Execution
**Objective:** Run tests in headless mode for faster CI/CD execution

**Task:**
Create a test that runs in headless Chrome mode and verifies it works correctly.

**Starter Code:**
```java
package com.sdet.advanced;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

public class Exercise1_HeadlessChrome {
    public static void main(String[] args) {
        // TODO: Create ChromeOptions
        ChromeOptions options = new ChromeOptions();

        // TODO: Add headless argument
        options.addArguments("--headless");

        // TODO: Add other recommended headless options
        options.addArguments("--disable-gpu");
        options.addArguments("--window-size=1920,1080");
        options.addArguments("--no-sandbox");

        // TODO: Create driver with options
        WebDriver driver = new ChromeDriver(options);

        try {
            // TODO: Navigate to website
            driver.get("https://www.google.com");

            // TODO: Verify page loaded
            String title = driver.getTitle();
            System.out.println("✓ Headless test executed");
            System.out.println("✓ Page title: " + title);

            // TODO: Verify headless mode worked
            if (title.contains("Google")) {
                System.out.println("✓ Headless Chrome test PASSED");
            }

        } finally {
            driver.quit();
            System.out.println("✓ Browser closed");
        }
    }
}
```

**Expected Output:**
```
✓ Headless test executed
✓ Page title: Google
✓ Headless Chrome test PASSED
✓ Browser closed
```

**Key Learning:**
- Headless mode runs browser without GUI (faster, less resources)
- Essential for CI/CD pipelines (Jenkins, GitHub Actions)
- Use `--headless` flag for Chrome
- Add `--window-size` to ensure correct rendering
- Perfect for automated testing in Docker containers

---

### Exercise 2: Chrome Options Configuration
**Objective:** Configure browser behavior using ChromeOptions

**Starter Code:**
```java
package com.sdet.advanced;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

public class Exercise2_ChromeOptions {
    public static void main(String[] args) {
        ChromeOptions options = new ChromeOptions();

        // TODO: Disable notifications
        options.addArguments("--disable-notifications");

        // TODO: Start maximized
        options.addArguments("--start-maximized");

        // TODO: Disable infobars
        options.addArguments("--disable-infobars");

        // TODO: Incognito mode
        options.addArguments("--incognito");

        // TODO: Disable extensions
        options.addArguments("--disable-extensions");

        // TODO: Set download directory
        options.addArguments("--download.default_directory=/path/to/downloads");

        WebDriver driver = new ChromeDriver(options);

        try {
            driver.get("https://www.saucedemo.com/");
            System.out.println("✓ Chrome configured with custom options");
            System.out.println("✓ Window size: " +
                driver.manage().window().getSize());

        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
✓ Chrome configured with custom options
✓ Window size: (1936, 1056)
```

**Common ChromeOptions:**
```java
// Performance
options.addArguments("--disable-dev-shm-usage");  // Docker fix
options.addArguments("--no-sandbox");  // Linux fix

// Privacy
options.addArguments("--incognito");

// UI
options.addArguments("--start-maximized");
options.addArguments("--window-size=1920,1080");

// Automation
options.addArguments("--disable-blink-features=AutomationControlled");
options.setExperimentalOption("excludeSwitches", new String[]{"enable-automation"});
```

---

### Exercise 3: Screenshot Utility
**Objective:** Create reusable screenshot utility for test evidence

**Starter Code:**
```java
package com.sdet.utils;

import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;

import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;
import java.text.SimpleDateFormat;
import java.util.Date;

public class ScreenshotUtil {

    // TODO: Create screenshots directory
    private static final String SCREENSHOT_DIR = "screenshots/";

    static {
        try {
            Files.createDirectories(Paths.get(SCREENSHOT_DIR));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    // TODO: Take screenshot with custom name
    public static String takeScreenshot(WebDriver driver, String testName) {
        try {
            // Cast driver to TakesScreenshot
            TakesScreenshot screenshot = (TakesScreenshot) driver;

            // Capture screenshot as file
            File srcFile = screenshot.getScreenshotAs(OutputType.FILE);

            // Create unique filename with timestamp
            String timestamp = new SimpleDateFormat("yyyyMMdd_HHmmss").format(new Date());
            String fileName = testName + "_" + timestamp + ".png";
            String filePath = SCREENSHOT_DIR + fileName;

            // Copy to destination
            Files.copy(srcFile.toPath(), Paths.get(filePath));

            System.out.println("✓ Screenshot saved: " + filePath);
            return filePath;

        } catch (Exception e) {
            System.out.println("✗ Screenshot failed: " + e.getMessage());
            return null;
        }
    }

    // TODO: Take screenshot on test failure (for TestNG)
    public static void takeScreenshotOnFailure(WebDriver driver, String testName) {
        takeScreenshot(driver, "FAILED_" + testName);
    }
}
```

**Testing Code:**
```java
public class Exercise3_ScreenshotTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.saucedemo.com/");

            // Take screenshot
            String path = ScreenshotUtil.takeScreenshot(driver, "LoginPage");

            if (path != null) {
                System.out.println("✓ Screenshot utility test PASSED");
            }

        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
✓ Screenshot saved: screenshots/LoginPage_20250118_143022.png
✓ Screenshot utility test PASSED
```

---

## 💪 Core Practice (30 min)

### Exercise 4: Browser Factory Pattern
**Objective:** Create flexible browser factory for cross-browser testing

**Starter Code:**
```java
package com.sdet.factory;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.edge.EdgeOptions;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.firefox.FirefoxOptions;

public class BrowserFactory {

    // TODO: Create driver based on browser type
    public static WebDriver createDriver(String browser) {
        return createDriver(browser, false);
    }

    // TODO: Create driver with headless option
    public static WebDriver createDriver(String browser, boolean headless) {
        WebDriver driver;

        switch (browser.toLowerCase()) {
            case "chrome":
                ChromeOptions chromeOptions = new ChromeOptions();
                if (headless) {
                    chromeOptions.addArguments("--headless");
                    chromeOptions.addArguments("--window-size=1920,1080");
                }
                chromeOptions.addArguments("--disable-notifications");
                chromeOptions.addArguments("--start-maximized");
                driver = new ChromeDriver(chromeOptions);
                break;

            case "firefox":
                FirefoxOptions firefoxOptions = new FirefoxOptions();
                if (headless) {
                    firefoxOptions.addArguments("--headless");
                    firefoxOptions.addArguments("--width=1920");
                    firefoxOptions.addArguments("--height=1080");
                }
                driver = new FirefoxDriver(firefoxOptions);
                break;

            case "edge":
                EdgeOptions edgeOptions = new EdgeOptions();
                if (headless) {
                    edgeOptions.addArguments("--headless");
                    edgeOptions.addArguments("--window-size=1920,1080");
                }
                driver = new EdgeDriver(edgeOptions);
                break;

            default:
                System.out.println("Unknown browser: " + browser + ". Using Chrome.");
                driver = new ChromeDriver();
        }

        System.out.println("✓ Created " + browser + " driver (headless: " + headless + ")");
        return driver;
    }

    // TODO: Get browser from system property or config
    public static WebDriver createDriver() {
        String browser = System.getProperty("browser", "chrome");
        String headless = System.getProperty("headless", "false");
        return createDriver(browser, Boolean.parseBoolean(headless));
    }
}
```

**Testing Code:**
```java
public class Exercise4_BrowserFactoryTest {
    public static void main(String[] args) {
        // Test 1: Chrome
        System.out.println("=== Test 1: Chrome ===");
        WebDriver chromeDriver = BrowserFactory.createDriver("chrome");
        chromeDriver.get("https://www.google.com");
        System.out.println("Chrome title: " + chromeDriver.getTitle());
        chromeDriver.quit();

        // Test 2: Headless Chrome
        System.out.println("\n=== Test 2: Headless Chrome ===");
        WebDriver headlessDriver = BrowserFactory.createDriver("chrome", true);
        headlessDriver.get("https://www.google.com");
        System.out.println("Headless title: " + headlessDriver.getTitle());
        headlessDriver.quit();

        // Test 3: From system property
        System.out.println("\n=== Test 3: From System Property ===");
        System.setProperty("browser", "chrome");
        System.setProperty("headless", "true");
        WebDriver sysDriver = BrowserFactory.createDriver();
        sysDriver.get("https://www.google.com");
        System.out.println("System property title: " + sysDriver.getTitle());
        sysDriver.quit();

        System.out.println("\n✓ Browser Factory test PASSED");
    }
}
```

**Expected Output:**
```
=== Test 1: Chrome ===
✓ Created chrome driver (headless: false)
Chrome title: Google

=== Test 2: Headless Chrome ===
✓ Created chrome driver (headless: true)
Headless title: Google

=== Test 3: From System Property ===
✓ Created chrome driver (headless: true)
System property title: Google

✓ Browser Factory test PASSED
```

**Usage in Tests:**
```java
// In test class
WebDriver driver = BrowserFactory.createDriver("chrome", true);

// From command line
// mvn test -Dbrowser=firefox -Dheadless=true
```

---

### Exercise 5: Window Management
**Objective:** Control browser window size, position, and state

**Starter Code:**
```java
package com.sdet.advanced;

import org.openqa.selenium.Dimension;
import org.openqa.selenium.Point;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise5_WindowManagement {
    public static void main(String[] args) throws InterruptedException {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.saucedemo.com/");

            // TODO: Get current window size
            Dimension currentSize = driver.manage().window().getSize();
            System.out.println("✓ Current size: " + currentSize.width + "x" + currentSize.height);

            // TODO: Set custom window size
            driver.manage().window().setSize(new Dimension(1024, 768));
            System.out.println("✓ Resized to: 1024x768");
            Thread.sleep(1000);

            // TODO: Get current position
            Point currentPosition = driver.manage().window().getPosition();
            System.out.println("✓ Current position: " + currentPosition);

            // TODO: Move window
            driver.manage().window().setPosition(new Point(100, 100));
            System.out.println("✓ Moved to: (100, 100)");
            Thread.sleep(1000);

            // TODO: Maximize window
            driver.manage().window().maximize();
            System.out.println("✓ Maximized");
            Thread.sleep(1000);

            // TODO: Fullscreen mode
            driver.manage().window().fullscreen();
            System.out.println("✓ Fullscreen");
            Thread.sleep(1000);

            System.out.println("\n✓ Window management test PASSED");

        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
✓ Current size: 1050x660
✓ Resized to: 1024x768
✓ Current position: Point (10, 10)
✓ Moved to: (100, 100)
✓ Maximized
✓ Fullscreen

✓ Window management test PASSED
```

**Use Cases:**
```java
// Responsive testing
driver.manage().window().setSize(new Dimension(375, 667));  // iPhone
driver.manage().window().setSize(new Dimension(768, 1024)); // iPad
driver.manage().window().setSize(new Dimension(1920, 1080)); // Desktop

// Consistent screenshots
driver.manage().window().setSize(new Dimension(1920, 1080));

// Multi-monitor testing
driver.manage().window().setPosition(new Point(1920, 0));  // Second monitor
```

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Complete Test Framework Utilities
**Objective:** Build comprehensive utility class combining all advanced features

**Starter Code:**
```java
package com.sdet.framework;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.Dimension;
import com.sdet.factory.BrowserFactory;
import com.sdet.utils.ScreenshotUtil;

import java.time.Duration;

public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    // TODO: Initialize driver with configuration
    public static void initDriver(String browser, boolean headless) {
        WebDriver webDriver = BrowserFactory.createDriver(browser, headless);

        // Configure timeouts
        webDriver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        webDriver.manage().timeouts().pageLoadTimeout(Duration.ofSeconds(30));
        webDriver.manage().timeouts().scriptTimeout(Duration.ofSeconds(30));

        // Set window size
        if (!headless) {
            webDriver.manage().window().maximize();
        } else {
            webDriver.manage().window().setSize(new Dimension(1920, 1080));
        }

        driver.set(webDriver);
        System.out.println("✓ Driver initialized: " + browser);
    }

    // TODO: Get driver instance
    public static WebDriver getDriver() {
        return driver.get();
    }

    // TODO: Quit driver
    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();
            System.out.println("✓ Driver quit");
        }
    }

    // TODO: Take screenshot
    public static String takeScreenshot(String testName) {
        return ScreenshotUtil.takeScreenshot(getDriver(), testName);
    }
}
```

**Testing Code:**
```java
public class Exercise6_CompleteFrameworkTest {
    public static void main(String[] args) {
        try {
            // Initialize
            DriverManager.initDriver("chrome", true);

            // Navigate
            DriverManager.getDriver().get("https://www.saucedemo.com/");
            System.out.println("✓ Navigated to application");

            // Take screenshot
            DriverManager.takeScreenshot("ApplicationHomePage");

            // Verify
            String title = DriverManager.getDriver().getTitle();
            if (title.contains("Swag Labs")) {
                System.out.println("✓ Application loaded correctly");
            }

            System.out.println("\n✓ Framework utilities test PASSED");

        } finally {
            DriverManager.quitDriver();
        }
    }
}
```

**Expected Output:**
```
✓ Created chrome driver (headless: true)
✓ Driver initialized: chrome
✓ Navigated to application
✓ Screenshot saved: screenshots/ApplicationHomePage_20250118_143532.png
✓ Application loaded correctly

✓ Framework utilities test PASSED
✓ Driver quit
```

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution: Headless Benefits
```java
// Headless mode advantages:
// 1. Faster execution (no GUI rendering)
// 2. Less memory usage
// 3. Works in CI/CD without display
// 4. Parallel execution without window conflicts
// 5. Docker container compatible

// Essential headless options:
options.addArguments("--headless");  // Enable headless
options.addArguments("--window-size=1920,1080");  // Set viewport
options.addArguments("--disable-gpu");  // Disable GPU (Windows)
options.addArguments("--no-sandbox");  // Docker/Linux fix
```

### Exercise 4 Solution: Factory Pattern Benefits
```java
// Why use Factory Pattern:
// 1. Centralized browser configuration
// 2. Easy to switch browsers (just change parameter)
// 3. Consistent options across all tests
// 4. Easy to add new browser support
// 5. Can read from config files or system properties

// Usage patterns:
WebDriver driver = BrowserFactory.createDriver("chrome");  // Explicit
WebDriver driver = BrowserFactory.createDriver();  // From config
// mvn test -Dbrowser=firefox  // Command line override
```

---

## ✅ Answers to Self-Check Questions

**1. How do you enable headless mode in Chrome?**
```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--headless");
options.addArguments("--window-size=1920,1080");
WebDriver driver = new ChromeDriver(options);
```

**2. What are common ChromeOptions for test automation?**
- `--disable-notifications` - Block notification popups
- `--start-maximized` - Start with maximized window
- `--incognito` - Private browsing mode
- `--disable-extensions` - No browser extensions
- `--no-sandbox` - Linux/Docker compatibility

**3. How do you take screenshots?**
```java
TakesScreenshot screenshot = (TakesScreenshot) driver;
File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
Files.copy(srcFile.toPath(), Paths.get("screenshot.png"));
```

**4. What is the Browser Factory pattern?**
Design pattern that centralizes browser creation and configuration, allowing easy switching between browsers and consistent setup across all tests.

**5. Why use ThreadLocal for WebDriver?**
For parallel test execution - each thread gets its own driver instance, preventing interference between tests running simultaneously.

**6. When should you use headless mode?**
- CI/CD pipelines (Jenkins, GitHub Actions)
- Docker containers
- Parallel execution at scale
- Faster test execution when UI inspection not needed
- Background automation tasks

---

## 📊 Self-Assessment

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Headless Chrome | ☐ | __/10 | ___ min | |
| 2 - ChromeOptions | ☐ | __/10 | ___ min | |
| 3 - Screenshot Utility | ☐ | __/10 | ___ min | |
| 4 - Browser Factory | ☐ | __/10 | ___ min | |
| 5 - Window Management | ☐ | __/10 | ___ min | |
| 6 - Framework Utilities | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

---

## 💡 Best Practices Summary

```java
// ✅ PRODUCTION-READY PATTERNS

// 1. Headless configuration for CI/CD
ChromeOptions options = new ChromeOptions();
if (System.getenv("CI") != null) {  // Detect CI environment
    options.addArguments("--headless");
    options.addArguments("--window-size=1920,1080");
    options.addArguments("--no-sandbox");
    options.addArguments("--disable-dev-shm-usage");
}

// 2. Browser factory with config
public static WebDriver createDriver() {
    String browser = ConfigReader.getProperty("browser", "chrome");
    boolean headless = Boolean.parseBoolean(
        ConfigReader.getProperty("headless", "false")
    );
    return BrowserFactory.createDriver(browser, headless);
}

// 3. Screenshot on failure (TestNG)
@AfterMethod
public void afterMethod(ITestResult result) {
    if (result.getStatus() == ITestResult.FAILURE) {
        ScreenshotUtil.takeScreenshot(driver, result.getName());
    }
}

// 4. ThreadLocal for parallel execution
private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

public static void initDriver() {
    driver.set(new ChromeDriver());
}

public static WebDriver getDriver() {
    return driver.get();
}

// ❌ ANTI-PATTERNS TO AVOID

// Don't hardcode browser
WebDriver driver = new ChromeDriver();  // WRONG - not flexible

// Don't skip window size in headless
options.addArguments("--headless");  // WRONG - missing window-size

// Don't ignore screenshots
// Always capture on failure for debugging
```

---

**Excellent work! You've mastered advanced Selenium techniques for production frameworks! 🚀**

**Week 2 Complete!** Next: Week 3 (TestNG Framework & Advanced Testing)
