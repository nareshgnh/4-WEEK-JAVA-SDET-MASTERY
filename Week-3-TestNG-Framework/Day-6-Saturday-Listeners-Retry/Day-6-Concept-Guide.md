# DAY 6: LISTENERS & RETRY MECHANISM

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand TestNG listeners (ITestListener)
- [ ] Implement retry logic for flaky tests
- [ ] Create custom listeners for reporting
- [ ] Handle test failures gracefully
- [ ] Build a robust retry framework

**Time Required:** 3 hours
**Difficulty:** Advanced
**Prerequisite:** Days 1-5 TestNG knowledge

---

## 📚 Core Concepts

### Concept 1: ITestListener

**What is it?**
A listener that gets notified of TestNG events (test start, pass, fail, skip, etc.). Use it to add custom behavior at different test lifecycle points.

**Basic Implementation:**
```java
import org.testng.*;

public class TestListener implements ITestListener {

    @Override
    public void onTestStart(ITestResult result) {
        System.out.println("Test Started: " + result.getName());
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        System.out.println("Test Passed: " + result.getName());
    }

    @Override
    public void onTestFailure(ITestResult result) {
        System.out.println("Test Failed: " + result.getName());
        // Capture screenshot, log error, etc.
    }

    @Override
    public void onTestSkipped(ITestResult result) {
        System.out.println("Test Skipped: " + result.getName());
    }

    @Override
    public void onStart(ITestContext context) {
        System.out.println("Test Suite Started: " + context.getName());
    }

    @Override
    public void onFinish(ITestContext context) {
        System.out.println("Test Suite Finished: " + context.getName());
        System.out.println("Passed: " + context.getPassedTests().size());
        System.out.println("Failed: " + context.getFailedTests().size());
    }
}
```

**Register Listener:**

**Method 1: Annotation**
```java
@Listeners(TestListener.class)
public class LoginTest {
    @Test
    public void testLogin() { }
}
```

**Method 2: testng.xml**
```xml
<suite>
    <listeners>
        <listener class-name="listeners.TestListener"/>
    </listeners>
    <test>...</test>
</suite>
```

### Concept 2: Retry Mechanism

**What is it?**
Automatically re-run failed tests. Useful for flaky tests (network issues, timing issues).

**IRetryAnalyzer Implementation:**
```java
import org.testng.IRetryAnalyzer;
import org.testng.ITestResult;

public class RetryAnalyzer implements IRetryAnalyzer {
    private int retryCount = 0;
    private static final int maxRetryCount = 2;  // Retry 2 times

    @Override
    public boolean retry(ITestResult result) {
        if (retryCount < maxRetryCount) {
            retryCount++;
            System.out.println("Retrying test " + result.getName() +
                " for the " + retryCount + " time");
            return true;  // Retry
        }
        return false;  // Don't retry
    }
}
```

**Use Retry in Test:**
```java
@Test(retryAnalyzer = RetryAnalyzer.class)
public void flakyTest() {
    // This test will retry up to 2 times if it fails
}
```

**Apply Retry to All Tests (Listener):**
```java
import org.testng.*;
import java.lang.reflect.Constructor;

public class RetryListener implements IAnnotationTransformer {

    @Override
    public void transform(ITestAnnotation annotation, Class testClass,
                         Constructor testConstructor, Method testMethod) {
        annotation.setRetryAnalyzer(RetryAnalyzer.class);
        // Now ALL @Test methods will use RetryAnalyzer
    }
}
```

**Register in testng.xml:**
```xml
<suite>
    <listeners>
        <listener class-name="listeners.RetryListener"/>
    </listeners>
    <test>...</test>
</suite>
```

### Concept 3: Real-World Listener Example

**Screenshot on Failure Listener:**
```java
import org.openqa.selenium.*;
import org.testng.*;
import org.apache.commons.io.FileUtils;
import java.io.File;

public class ScreenshotListener implements ITestListener {

    @Override
    public void onTestFailure(ITestResult result) {
        Object testClass = result.getInstance();
        WebDriver driver = ((BaseTest) testClass).getDriver();

        if (driver != null) {
            String screenshotPath = captureScreenshot(driver, result.getName());
            System.out.println("Screenshot saved: " + screenshotPath);
        }
    }

    private String captureScreenshot(WebDriver driver, String testName) {
        TakesScreenshot ts = (TakesScreenshot) driver;
        File source = ts.getScreenshotAs(OutputType.FILE);
        String path = "screenshots/" + testName + "_" + System.currentTimeMillis() + ".png";

        try {
            FileUtils.copyFile(source, new File(path));
        } catch (Exception e) {
            e.printStackTrace();
        }

        return path;
    }
}
```

---

## ⚠️ Common Mistakes

### Mistake 1: Infinite Retry Loop
```java
public boolean retry(ITestResult result) {
    return true;  // Always retry → infinite loop!
}
```
**Fix:** Add max retry count

### Mistake 2: Listener Not Registered
```java
public class TestListener implements ITestListener { }
// But not registered in testng.xml or @Listeners
```
**Fix:** Register listener properly

---

## 💡 Pro Tips

**Tip 1:** Use retry sparingly - fix flaky tests instead of hiding them
**Tip 2:** Combine listeners for comprehensive reporting
**Tip 3:** Log retry attempts for debugging

---

## 🎓 Interview Prep

**Q: How do you handle flaky tests in your framework?**

**A:** I implement IRetryAnalyzer to automatically retry failed tests up to 2 times. This handles transient failures like network timeouts. However, I also log all retry attempts and regularly review them to identify truly flaky tests that need investigation. The goal is to fix root causes, not mask problems with retries.

---

**🎯 Ready to practice? Move to Day-6-Practice-Exercises.md**
