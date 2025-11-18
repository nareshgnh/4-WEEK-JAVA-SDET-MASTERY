# DAY 6 PROJECT: RETRY FRAMEWORK

## 🎯 Project Overview
Build a complete retry and listener framework for robust test execution.

## 📝 Implementation

### RetryAnalyzer.java
```java
import org.testng.*;

public class RetryAnalyzer implements IRetryAnalyzer {
    private int retryCount = 0;
    private static final int maxRetryCount = 2;

    @Override
    public boolean retry(ITestResult result) {
        if (retryCount < maxRetryCount) {
            retryCount++;
            System.out.println("Retrying " + result.getName() +
                " - Attempt " + retryCount + "/" + maxRetryCount);
            return true;
        }
        return false;
    }
}
```

### RetryListener.java
```java
import org.testng.*;
import org.testng.annotations.ITestAnnotation;
import java.lang.reflect.*;

public class RetryListener implements IAnnotationTransformer {
    @Override
    public void transform(ITestAnnotation annotation, Class testClass,
                         Constructor testConstructor, Method testMethod) {
        annotation.setRetryAnalyzer(RetryAnalyzer.class);
    }
}
```

### TestListener.java
```java
import org.testng.*;

public class TestListener implements ITestListener {
    @Override
    public void onTestStart(ITestResult result) {
        System.out.println("\n>>> Starting: " + result.getName());
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        System.out.println("✓ PASSED: " + result.getName());
    }

    @Override
    public void onTestFailure(ITestResult result) {
        System.out.println("✗ FAILED: " + result.getName());
    }

    @Override
    public void onFinish(ITestContext context) {
        System.out.println("\n=== Test Suite Complete ===");
        System.out.println("Passed: " + context.getPassedTests().size());
        System.out.println("Failed: " + context.getFailedTests().size());
        System.out.println("Skipped: " + context.getSkippedTests().size());
    }
}
```

### testng.xml
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Retry Framework Suite">
    <listeners>
        <listener class-name="listeners.TestListener"/>
        <listener class-name="listeners.RetryListener"/>
    </listeners>

    <test name="Tests">
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>
</suite>
```

## ✅ Result
- Failed tests automatically retry up to 2 times
- Detailed console output for all test events
- Professional test execution framework

---

**🎉 Project Complete! Move to Day-6-Cheat-Sheet.md**
