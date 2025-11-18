# DAY 7 CHEAT SHEET: ADVANCED SELENIUM

## ⚡ Headless Browser

```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--headless");
options.addArguments("--disable-gpu");
options.addArguments("--window-size=1920,1080");

WebDriver driver = new ChromeDriver(options);
```

---

## 📦 WebDriverManager

```java
// Add to pom.xml
<dependency>
    <groupId>io.github.bonigarcia</groupId>
    <artifactId>webdrivermanager</artifactId>
    <version>5.5.3</version>
</dependency>

// Use in code
WebDriverManager.chromedriver().setup();
WebDriver driver = new ChromeDriver();
```

---

## 📸 Screenshots

```java
public void takeScreenshot(String fileName) {
    TakesScreenshot screenshot = (TakesScreenshot) driver;
    File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
    Files.copy(srcFile.toPath(), Paths.get("screenshots/" + fileName + ".png"));
}

// On test failure (TestNG)
@AfterMethod
public void afterMethod(ITestResult result) {
    if (result.getStatus() == ITestResult.FAILURE) {
        takeScreenshot(result.getName());
    }
}
```

---

## 🌐 Cross-Browser

```java
public static WebDriver createDriver(String browser) {
    WebDriver driver;
    switch (browser.toLowerCase()) {
        case "chrome":
            driver = new ChromeDriver();
            break;
        case "firefox":
            driver = new FirefoxDriver();
            break;
        case "headless":
            ChromeOptions options = new ChromeOptions();
            options.addArguments("--headless");
            driver = new ChromeDriver(options);
            break;
        default:
            driver = new ChromeDriver();
    }
    return driver;
}
```

---

## 🔧 Chrome Options

```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--start-maximized");
options.addArguments("--headless");
options.addArguments("--disable-notifications");
options.addArguments("--incognito");
options.addArguments("--disable-gpu");
options.addArguments("--no-sandbox");

WebDriver driver = new ChromeDriver(options);
```

---

## ⚡ Performance Tips

```java
// 1. Headless mode (30-50% faster)
options.addArguments("--headless");

// 2. Proper waits (not Thread.sleep)
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// 3. Disable images (faster page load)
options.addArguments("--blink-settings=imagesEnabled=false");

// 4. Reuse browser sessions when possible
driver.get(baseUrl);  // Instead of quit() and new driver
```

---

## 📌 Remember This

> **Use headless mode in CI/CD, take screenshots on failures, implement proper waits, and organize tests with Page Object Model.**

---

**WEEK 2 COMPLETE! Ready for TestNG Framework! 🎉**
