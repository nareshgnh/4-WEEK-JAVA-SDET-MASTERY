# DAY 6 PRACTICE EXERCISES

## Exercise 1: Basic Listener
```java
public class SimpleListener implements ITestListener {
    @Override
    public void onTestStart(ITestResult result) {
        System.out.println("Starting: " + result.getName());
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        System.out.println("Passed: " + result.getName());
    }

    @Override
    public void onTestFailure(ITestResult result) {
        System.out.println("Failed: " + result.getName());
    }
}
```

## Exercise 2: Retry Analyzer
```java
public class RetryAnalyzer implements IRetryAnalyzer {
    private int count = 0;
    private static final int maxRetry = 2;

    @Override
    public boolean retry(ITestResult result) {
        if (count < maxRetry) {
            count++;
            System.out.println("Retrying " + result.getName() + " - attempt " + count);
            return true;
        }
        return false;
    }
}

// Use in test
@Test(retryAnalyzer = RetryAnalyzer.class)
public void flakyTest() {
    if (Math.random() > 0.5) {
        Assert.fail("Random failure");
    }
}
```

## Exercise 3: Screenshot Listener
```java
public class ScreenshotListener implements ITestListener {
    @Override
    public void onTestFailure(ITestResult result) {
        Object instance = result.getInstance();
        WebDriver driver = ((BaseTest) instance).getDriver();

        if (driver != null) {
            TakesScreenshot ts = (TakesScreenshot) driver;
            File screenshot = ts.getScreenshotAs(OutputType.FILE);
            try {
                FileUtils.copyFile(screenshot,
                    new File("screenshots/" + result.getName() + ".png"));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

## Exercise 4: Complete Framework
Combine listeners and retry:
```xml
<suite>
    <listeners>
        <listener class-name="listeners.TestListener"/>
        <listener class-name="listeners.RetryListener"/>
        <listener class-name="listeners.ScreenshotListener"/>
    </listeners>
    <test>...</test>
</suite>
```

---

**🎉 Practice Complete! Move to Day-6-Debug-Practice.md**
