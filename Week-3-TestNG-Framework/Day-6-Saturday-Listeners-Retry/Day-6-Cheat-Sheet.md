# DAY 6 CHEAT SHEET: LISTENERS & RETRY

## ⚡ Quick Syntax

### ITestListener
```java
public class TestListener implements ITestListener {
    @Override
    public void onTestStart(ITestResult result) { }

    @Override
    public void onTestSuccess(ITestResult result) { }

    @Override
    public void onTestFailure(ITestResult result) { }

    @Override
    public void onTestSkipped(ITestResult result) { }
}
```

### RetryAnalyzer
```java
public class RetryAnalyzer implements IRetryAnalyzer {
    private int count = 0;
    private static final int maxRetry = 2;

    @Override
    public boolean retry(ITestResult result) {
        if (count < maxRetry) {
            count++;
            return true;
        }
        return false;
    }
}
```

### Register Listener
```xml
<suite>
    <listeners>
        <listener class-name="listeners.TestListener"/>
    </listeners>
</suite>
```

Or:
```java
@Listeners(TestListener.class)
public class TestClass { }
```

## 🔑 Key Concepts

| Concept | Purpose | Interface |
|---------|---------|-----------|
| **ITestListener** | Listen to test events | `onTestStart`, `onTestFailure` |
| **IRetryAnalyzer** | Retry failed tests | `retry(ITestResult)` |
| **IAnnotationTransformer** | Modify annotations | Apply retry to all tests |

## 💡 Key Takeaways
- ✅ Listeners handle test lifecycle events
- ✅ Retry mechanism handles flaky tests
- ✅ Don't overuse retry - fix flaky tests
- ✅ Combine listeners for robust framework

## 📌 Remember This
> **Listeners = Custom Behavior**
> - onTestFailure → Screenshot
> - onTestStart → Log
> - Retry → Handle flaky tests (2-3 max)

## 🔮 Tomorrow
**Day 7:** Best Practices & Professional Framework

---

**🎉 Day 6 Complete!**
