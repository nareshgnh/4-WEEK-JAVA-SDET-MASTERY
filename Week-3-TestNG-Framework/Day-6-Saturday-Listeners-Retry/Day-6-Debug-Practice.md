# DAY 6 DEBUGGING CHALLENGES

## Challenge 1: Listener Not Executing
```java
public class TestListener implements ITestListener {
    public void onTestStart(ITestResult result) {
        System.out.println("Test started");
    }
}
// But listener not registered!
```
**Fix:** Add `@Listeners(TestListener.class)` or register in testng.xml

## Challenge 2: Infinite Retry
```java
public boolean retry(ITestResult result) {
    return true;  // Always retry!
}
```
**Fix:** Add retry limit:
```java
if (count < maxRetry) {
    count++;
    return true;
}
return false;
```

## Challenge 3: ClassCastException
```java
WebDriver driver = ((BaseTest) testClass).getDriver();
// ERROR if testClass is not BaseTest
```
**Fix:** Add instanceof check:
```java
if (testClass instanceof BaseTest) {
    WebDriver driver = ((BaseTest) testClass).getDriver();
}
```

---

**🔧 Debugging Complete! Move to Day-6-Project-Guide.md**
