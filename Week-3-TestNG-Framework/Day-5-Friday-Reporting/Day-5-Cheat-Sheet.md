# DAY 5 CHEAT SHEET: REPORTING

## ⚡ Quick Syntax

### ExtentReports Setup
```java
@BeforeSuite
public void setupReport() {
    ExtentSparkReporter spark = new ExtentSparkReporter("reports/report.html");
    extent = new ExtentReports();
    extent.attachReporter(spark);
}

@BeforeMethod
public void startTest(Method method) {
    test = extent.createTest(method.getName());
}

@AfterSuite
public void tearDown() {
    extent.flush();  // MUST call!
}
```

### Logging
```java
test.info("Step description");
test.pass("Test passed");
test.fail("Test failed");
test.warning("Warning message");
```

### Screenshots
```java
@AfterMethod
public void afterTest(ITestResult result) {
    if (result.getStatus() == ITestResult.FAILURE) {
        String path = captureScreenshot(result.getName());
        test.fail("Failed").addScreenshotFromPath(path);
    }
}
```

### Capture Screenshot Method
```java
public String captureScreenshot(WebDriver driver, String name) {
    TakesScreenshot ts = (TakesScreenshot) driver;
    File source = ts.getScreenshotAs(OutputType.FILE);
    String path = "screenshots/" + name + ".png";
    FileUtils.copyFile(source, new File(path));
    return path;
}
```

## 🔑 Key Concepts

| Concept | Purpose | Code |
|---------|---------|------|
| **ExtentReports** | Create report | `extent = new ExtentReports()` |
| **ExtentTest** | Individual test | `test = extent.createTest("name")` |
| **flush()** | Write report | `extent.flush()` |
| **Screenshot** | Capture failure | `test.fail().addScreenshotFromPath()` |

## 💡 Key Takeaways
- ✅ ExtentReports creates beautiful HTML reports
- ✅ Always call extent.flush() in @AfterSuite
- ✅ Capture screenshots on failure
- ✅ Log important test steps with test.info()

## 📌 Remember This
> **Report = Communicate Results**
> - Setup in @BeforeSuite
> - Log steps during test
> - Screenshots on failure
> - Flush in @AfterSuite

## 🔮 Tomorrow
**Day 6:** Listeners & Retry mechanism

---

**🎉 Day 5 Complete!**
