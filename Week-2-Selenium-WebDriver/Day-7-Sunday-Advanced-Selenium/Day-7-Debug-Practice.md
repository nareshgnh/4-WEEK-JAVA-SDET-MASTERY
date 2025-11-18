# DAY 7 DEBUGGING CHALLENGES

## 🐛 Advanced Selenium Debugging

---

## Challenge 1: Headless Mode Test Failing

**Broken Code:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

public class HeadlessTest {
    public static void main(String[] args) {
        // BUG: Headless configuration incomplete
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--headless");
        // Missing window size!

        WebDriver driver = new ChromeDriver(options);

        try {
            driver.get("https://www.saucedemo.com/");

            // Click login button
            driver.findElement(By.id("login-button")).click();
            // Element not found in headless! Why?

        } finally {
            driver.quit();
        }
    }
}
```

**Symptoms:**
- Test passes with regular Chrome (GUI)
- Test fails in headless mode with ElementNotFound exception
- Error: "element not interactable" or "no such element"

**Your Task:** Fix the headless configuration

---

**Solution:**

**Bug:** Headless mode without window size causes different viewport, making elements render differently or not at all

**Fixed Code:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

public class HeadlessTestFixed {
    public static void main(String[] args) {
        ChromeOptions options = new ChromeOptions();

        // ✓ Complete headless configuration
        options.addArguments("--headless");
        options.addArguments("--window-size=1920,1080");  // CRITICAL - set viewport
        options.addArguments("--disable-gpu");  // Recommended for Windows
        options.addArguments("--no-sandbox");  // For Linux/Docker
        options.addArguments("--disable-dev-shm-usage");  // For Docker

        WebDriver driver = new ChromeDriver(options);

        try {
            driver.get("https://www.saucedemo.com/");

            // Now elements render correctly
            driver.findElement(By.id("login-button")).click();

            System.out.println("✓ Headless test passed");

        } finally {
            driver.quit();
        }
    }
}
```

**Key Learning:**
- Headless browsers have NO default window size
- Elements may not render or be interactable without proper viewport
- Always set `--window-size` in headless mode
- Use same size as your regular tests for consistency
- Common sizes: 1920x1080 (desktop), 1366x768 (laptop), 375x667 (mobile)

**Debugging Steps:**
1. Add window size configuration
2. Check if elements render at that size
3. Take screenshot to verify layout
4. Compare headless vs GUI screenshots

**Prevention:**
```java
// ✅ CORRECT - Always set window size for headless
if (headless) {
    options.addArguments("--window-size=1920,1080");
}

// ❌ WRONG - Headless without size
options.addArguments("--headless");  // Elements may not render!
```

---

## Challenge 2: Screenshots Not Saving

**Broken Code:**
```java
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;

public class ScreenshotTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.google.com");

            // BUG: Try to save screenshot
            TakesScreenshot screenshot = (TakesScreenshot) driver;
            File srcFile = screenshot.getScreenshotAs(OutputType.FILE);

            // This will fail!
            Files.copy(srcFile.toPath(),
                Paths.get("screenshots/google.png"));
            // NoSuchFileException: screenshots directory doesn't exist!

        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
        } finally {
            driver.quit();
        }
    }
}
```

**Symptoms:**
- `NoSuchFileException` or `IOException`
- Error message: "screenshots (No such file or directory)"
- Screenshot not saved to disk

**Your Task:** Fix the screenshot saving

---

**Solution:**

**Bug:** Trying to save to directory that doesn't exist - must create directory first

**Fixed Code:**
```java
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;

public class ScreenshotTestFixed {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.google.com");

            // ✓ Create directory if it doesn't exist
            String screenshotDir = "screenshots/";
            Files.createDirectories(Paths.get(screenshotDir));

            // Take screenshot
            TakesScreenshot screenshot = (TakesScreenshot) driver;
            File srcFile = screenshot.getScreenshotAs(OutputType.FILE);

            // Save to file
            String filePath = screenshotDir + "google.png";
            Files.copy(srcFile.toPath(), Paths.get(filePath));

            System.out.println("✓ Screenshot saved: " + filePath);

        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Key Learning:**
- Always create target directory before saving files
- Use `Files.createDirectories()` not `mkdir()` (creates parent dirs too)
- Check if file already exists to avoid overwriting
- Add timestamp to filename for uniqueness

**Better Solution - Utility Class:**
```java
public class ScreenshotUtil {
    private static final String SCREENSHOT_DIR = "screenshots/";

    static {
        try {
            // Create directory on class load
            Files.createDirectories(Paths.get(SCREENSHOT_DIR));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static String takeScreenshot(WebDriver driver, String testName) {
        try {
            TakesScreenshot screenshot = (TakesScreenshot) driver;
            File srcFile = screenshot.getScreenshotAs(OutputType.FILE);

            // Unique filename with timestamp
            String timestamp = new SimpleDateFormat("yyyyMMdd_HHmmss")
                .format(new Date());
            String fileName = testName + "_" + timestamp + ".png";
            String filePath = SCREENSHOT_DIR + fileName;

            Files.copy(srcFile.toPath(), Paths.get(filePath));

            return filePath;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

**Debugging Steps:**
1. Check if directory exists: `Files.exists(Paths.get("screenshots"))`
2. Create directory: `Files.createDirectories()`
3. Verify write permissions
4. Check disk space

---

## Challenge 3: Cross-Browser Test Inconsistency

**Broken Code:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;

public class CrossBrowserTest {
    public static void testLogin(WebDriver driver) {
        driver.get("https://www.saucedemo.com/");

        // BUG: Works in Chrome, fails in Firefox
        driver.findElement(By.id("user-name")).sendKeys("standard_user");
        driver.findElement(By.id("password")).sendKeys("secret_sauce");
        driver.findElement(By.id("login-button")).click();

        // Firefox needs more time here!
        String url = driver.getCurrentUrl();
        if (!url.contains("inventory")) {
            System.out.println("Login failed!");
        }
    }

    public static void main(String[] args) {
        // Test 1: Chrome - PASSES
        System.out.println("=== Chrome ===");
        WebDriver chromeDriver = new ChromeDriver();
        testLogin(chromeDriver);
        chromeDriver.quit();

        // Test 2: Firefox - FAILS
        System.out.println("\n=== Firefox ===");
        WebDriver firefoxDriver = new FirefoxDriver();
        testLogin(firefoxDriver);  // URL check fails!
        firefoxDriver.quit();
    }
}
```

**Symptoms:**
- Test passes in Chrome
- Same test fails in Firefox
- Timing issues between browsers
- URL doesn't contain expected value

**Your Task:** Make test work across all browsers

---

**Solution:**

**Bug:** Firefox is slower than Chrome - need explicit waits, not assumptions about timing

**Fixed Code:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class CrossBrowserTestFixed {
    public static void testLogin(WebDriver driver) {
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        driver.get("https://www.saucedemo.com/");

        // Type credentials
        driver.findElement(By.id("user-name")).sendKeys("standard_user");
        driver.findElement(By.id("password")).sendKeys("secret_sauce");
        driver.findElement(By.id("login-button")).click();

        // ✓ Wait for URL to change (works for all browsers)
        wait.until(ExpectedConditions.urlContains("inventory"));

        String url = driver.getCurrentUrl();
        if (url.contains("inventory")) {
            System.out.println("✓ Login successful");
        }
    }

    public static void main(String[] args) {
        // Test 1: Chrome
        System.out.println("=== Chrome ===");
        WebDriver chromeDriver = new ChromeDriver();
        testLogin(chromeDriver);
        chromeDriver.quit();

        // Test 2: Firefox
        System.out.println("\n=== Firefox ===");
        WebDriver firefoxDriver = new FirefoxDriver();
        testLogin(firefoxDriver);  // Now works!
        firefoxDriver.quit();

        System.out.println("\n✓ Cross-browser test passed");
    }
}
```

**Key Learning:**
- Different browsers have different performance characteristics
- Never rely on implicit timing - use explicit waits
- Wait for specific conditions, not arbitrary delays
- Test on all target browsers, not just one

**Browser Differences:**
```java
// Chrome: Faster rendering, more aggressive caching
// Firefox: Slower startup, different JavaScript engine
// Safari: Different WebDriver implementation
// Edge: Based on Chromium but has differences

// ✅ CORRECT - Wait for conditions
wait.until(ExpectedConditions.urlContains("expected"));
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("element")));

// ❌ WRONG - Assume timing
Thread.sleep(2000);  // May work in Chrome, fail in Firefox
```

**Debugging Cross-Browser Issues:**
1. Add explicit waits for all interactions
2. Increase timeout values
3. Check browser-specific console logs
4. Verify locators work in all browsers
5. Test with browser DevTools (F12)

---

## Challenge 4: BrowserFactory Not Flexible

**Broken Code:**
```java
public class BrowserFactory {
    public static WebDriver createDriver(String browser) {
        // BUG: Hardcoded, not configurable
        if (browser.equals("chrome")) {
            return new ChromeDriver();
        } else if (browser.equals("firefox")) {
            return new FirefoxDriver();
        }
        return new ChromeDriver();  // Default
    }
}

// Usage - can't control headless mode!
WebDriver driver = BrowserFactory.createDriver("chrome");
// How do I make it headless? Can't!
```

**Symptoms:**
- Can't run tests in headless mode via factory
- Can't configure browser options
- Hardcoded behavior
- Not flexible for CI/CD

**Your Task:** Make factory configurable

---

**Solution:**

**Bug:** Factory doesn't support browser options like headless mode

**Fixed Code:**
```java
public class BrowserFactory {

    // ✓ Support headless parameter
    public static WebDriver createDriver(String browser, boolean headless) {
        WebDriver driver;

        switch (browser.toLowerCase()) {
            case "chrome":
                ChromeOptions chromeOptions = new ChromeOptions();
                if (headless) {
                    chromeOptions.addArguments("--headless");
                    chromeOptions.addArguments("--window-size=1920,1080");
                    chromeOptions.addArguments("--disable-gpu");
                }
                chromeOptions.addArguments("--disable-notifications");
                driver = new ChromeDriver(chromeOptions);
                break;

            case "firefox":
                FirefoxOptions firefoxOptions = new FirefoxOptions();
                if (headless) {
                    firefoxOptions.addArguments("--headless");
                }
                driver = new FirefoxDriver(firefoxOptions);
                break;

            default:
                driver = new ChromeDriver();
        }

        return driver;
    }

    // ✓ Read from system properties
    public static WebDriver createDriver() {
        String browser = System.getProperty("browser", "chrome");
        boolean headless = Boolean.parseBoolean(
            System.getProperty("headless", "false")
        );
        return createDriver(browser, headless);
    }
}

// Usage - now flexible!
WebDriver driver = BrowserFactory.createDriver("chrome", true);  // Headless

// Or from command line:
// mvn test -Dbrowser=firefox -Dheadless=true
WebDriver driver = BrowserFactory.createDriver();
```

**Key Learning:**
- Factory should accept configuration parameters
- Support system properties for command-line control
- Provide sensible defaults
- Make it easy to add new browsers

**Enhancement - Config File:**
```java
// config.properties
browser=chrome
headless=false
timeout=10

// Usage
public static WebDriver createDriver() {
    String browser = ConfigReader.getProperty("browser");
    boolean headless = Boolean.parseBoolean(
        ConfigReader.getProperty("headless")
    );
    return createDriver(browser, headless);
}
```

---

## Challenge 5: Performance Still Slow with Waits

**Broken Code:**
```java
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import java.time.Duration;

public class SlowTest {
    public static void main(String[] args) throws InterruptedException {
        WebDriver driver = new ChromeDriver();

        // BUG: Using Thread.sleep everywhere
        driver.get("https://www.saucedemo.com/");
        Thread.sleep(3000);  // Wait for page to load

        driver.findElement(By.id("user-name")).sendKeys("standard_user");
        Thread.sleep(1000);  // Wait after typing

        driver.findElement(By.id("password")).sendKeys("secret_sauce");
        Thread.sleep(1000);  // Wait after typing

        driver.findElement(By.id("login-button")).click();
        Thread.sleep(5000);  // Wait for login

        // Total: 10 seconds of unnecessary waiting!
        driver.quit();
    }
}
```

**Symptoms:**
- Tests take very long to run
- Fixed delays regardless of actual load time
- Sometimes too short (flaky), sometimes too long (slow)

**Your Task:** Replace Thread.sleep with proper waits

---

**Solution:**

**Bug:** Using Thread.sleep wastes time and doesn't adapt to actual page load times

**Fixed Code:**
```java
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class FastTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        // ✓ Page loads - waits only as long as needed
        driver.get("https://www.saucedemo.com/");

        // ✓ Wait for element to be clickable (not fixed time)
        WebElement usernameField = wait.until(
            ExpectedConditions.elementToBeClickable(By.id("user-name"))
        );
        usernameField.sendKeys("standard_user");

        // ✓ No wait needed - next element interaction has implicit wait
        driver.findElement(By.id("password")).sendKeys("secret_sauce");

        // ✓ Click and wait for URL change
        driver.findElement(By.id("login-button")).click();
        wait.until(ExpectedConditions.urlContains("inventory"));

        // Total: ~2-3 seconds (vs 10 with Thread.sleep!)
        System.out.println("✓ Test completed quickly");

        driver.quit();
    }
}
```

**Performance Comparison:**
```
Thread.sleep version: 10 seconds (fixed)
WebDriverWait version: 2-3 seconds (adaptive)
Speed improvement: 70-80%!
```

**Key Learning:**
- `Thread.sleep` wastes time waiting for worst-case scenario
- `WebDriverWait` waits only until condition is met
- Tests run faster and are more reliable
- Better for CI/CD where time = money

**Wait Strategy:**
```java
// ❌ BAD - Fixed delays
Thread.sleep(3000);  // Always waits 3 seconds

// ✅ GOOD - Adaptive waits
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("element")));
// Waits 0.5s if fast, up to 10s if slow

// ✅ BEST - Configure implicit wait once
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
// All findElement calls wait up to 10s automatically
```

---

## 🔧 Debugging Checklist

**When headless tests fail:**
- [ ] Did you set window size with `--window-size=1920,1080`?
- [ ] Are you using recommended headless flags?
- [ ] Does the element exist at that viewport size?
- [ ] Can you take a screenshot to verify layout?

**When screenshots don't save:**
- [ ] Does the target directory exist?
- [ ] Did you create it with `Files.createDirectories()`?
- [ ] Do you have write permissions?
- [ ] Is the filename unique (using timestamp)?

**When cross-browser tests fail:**
- [ ] Are you using explicit waits?
- [ ] Did you test on the failing browser?
- [ ] Are locators browser-specific?
- [ ] Do you have the correct WebDriver for that browser?

**When factory pattern isn't flexible:**
- [ ] Can you pass configuration parameters?
- [ ] Do you support system properties?
- [ ] Can users override from command line?
- [ ] Are defaults sensible?

**When tests are slow:**
- [ ] Are you using Thread.sleep?
- [ ] Can you replace with WebDriverWait?
- [ ] Are implicit waits configured?
- [ ] Is headless mode enabled for CI/CD?

---

## 💡 Best Practices Summary

```java
// ✅ CORRECT PATTERNS

// 1. Headless configuration
ChromeOptions options = new ChromeOptions();
if (headless) {
    options.addArguments("--headless");
    options.addArguments("--window-size=1920,1080");  // REQUIRED
    options.addArguments("--disable-gpu");
    options.addArguments("--no-sandbox");
}

// 2. Screenshot with directory creation
String dir = "screenshots/";
Files.createDirectories(Paths.get(dir));  // Create first
TakesScreenshot ss = (TakesScreenshot) driver;
Files.copy(ss.getScreenshotAs(OutputType.FILE).toPath(),
    Paths.get(dir + "test.png"));

// 3. Cross-browser waits
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.urlContains("expected"));

// 4. Configurable factory
public static WebDriver createDriver(String browser, boolean headless) {
    // Support parameters
}

public static WebDriver createDriver() {
    // Read from config/system properties
    String browser = System.getProperty("browser", "chrome");
    return createDriver(browser, false);
}

// 5. Performance optimization
// Use explicit waits, not Thread.sleep
wait.until(ExpectedConditions.elementToBeClickable(locator));

// ❌ ANTI-PATTERNS TO AVOID

// Don't skip window size in headless
options.addArguments("--headless");  // WRONG - no window size

// Don't assume directory exists
Files.copy(src, Paths.get("screenshots/test.png"));  // WRONG - may not exist

// Don't hardcode timing
Thread.sleep(5000);  // WRONG - wastes time

// Don't make factory inflexible
public static WebDriver createDriver() {
    return new ChromeDriver();  // WRONG - not configurable
}
```

---

## 🎯 Quick Reference: Common Fixes

| Problem | Cause | Fix |
|---------|-------|-----|
| Headless test fails | No window size | Add `--window-size=1920,1080` |
| Screenshot error | Directory missing | `Files.createDirectories()` first |
| Firefox test fails | Different timing | Use `WebDriverWait` |
| Can't run headless | Factory not flexible | Add headless parameter |
| Tests too slow | Using Thread.sleep | Replace with WebDriverWait |
| Element not found in headless | Viewport too small | Increase window size |

---

**Advanced Selenium debugging mastery achieved! 🎯**
