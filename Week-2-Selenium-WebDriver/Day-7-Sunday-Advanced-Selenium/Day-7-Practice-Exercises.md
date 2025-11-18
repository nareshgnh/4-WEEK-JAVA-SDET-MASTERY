# DAY 7 PRACTICE EXERCISES

## Exercise 1: Headless Chrome Test
```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--headless");
options.addArguments("--disable-gpu");

WebDriver driver = new ChromeDriver(options);
driver.get("https://www.google.com");
System.out.println("Headless test - Title: " + driver.getTitle());
driver.quit();
```

## Exercise 2: Browser Factory
```java
public class BrowserFactory {
    public static WebDriver getDriver(String browser) {
        return switch (browser.toLowerCase()) {
            case "chrome" -> new ChromeDriver();
            case "firefox" -> new FirefoxDriver();
            case "headless" -> {
                ChromeOptions options = new ChromeOptions();
                options.addArguments("--headless");
                yield new ChromeDriver(options);
            }
            default -> new ChromeDriver();
        };
    }
}
```

## Exercise 3: Screenshot on Failure
```java
@AfterMethod
public void takeScreenshotOnFailure(ITestResult result) {
    if (result.getStatus() == ITestResult.FAILURE) {
        TakesScreenshot screenshot = (TakesScreenshot) driver;
        File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
        String fileName = result.getName() + "_" + System.currentTimeMillis() + ".png";
        Files.copy(srcFile.toPath(), Paths.get("screenshots/" + fileName));
    }
}
```

**Answers to Self-Check:**
1. `options.addArguments("--headless");`
2. Automatic driver download and version matching
3. Use TakesScreenshot in @AfterMethod with ITestResult
4. Parallel execution on multiple machines/browsers
