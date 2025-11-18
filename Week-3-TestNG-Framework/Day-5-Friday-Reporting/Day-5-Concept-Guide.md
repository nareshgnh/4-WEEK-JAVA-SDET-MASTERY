# DAY 5: TEST REPORTING

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Generate TestNG default HTML reports
- [ ] Integrate ExtentReports for beautiful reports
- [ ] Configure Allure reports
- [ ] Capture screenshots on test failure
- [ ] Create professional test reports for stakeholders

**Time Required:** 3 hours
**Difficulty:** Medium
**Prerequisite:** Days 1-4 TestNG knowledge

---

## 📚 Core Concepts

### Concept 1: TestNG Default Reports

**What is it?**
TestNG automatically generates HTML and XML reports after test execution.

**Location:**
```
test-output/
├── index.html          (Main report)
├── emailable-report.html  (Email-friendly report)
└── testng-results.xml  (XML format)
```

**No configuration needed!** Reports are generated automatically.

### Concept 2: ExtentReports

**What is it?**
ExtentReports is a popular third-party library for creating beautiful, detailed HTML reports with charts, screenshots, and logs.

**Setup (pom.xml):**
```xml
<dependency>
    <groupId>com.aventstack</groupId>
    <artifactId>extentreports</artifactId>
    <version>5.0.9</version>
</dependency>
```

**Basic Usage:**
```java
import com.aventstack.extentreports.ExtentReports;
import com.aventstack.extentreports.ExtentTest;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;
import org.testng.annotations.*;

public class ExtentReportExample {
    ExtentReports extent;
    ExtentTest test;

    @BeforeSuite
    public void setupReport() {
        ExtentSparkReporter spark = new ExtentSparkReporter("reports/extent-report.html");
        extent = new ExtentReports();
        extent.attachReporter(spark);
    }

    @BeforeMethod
    public void startTest(Method method) {
        test = extent.createTest(method.getName());
    }

    @Test
    public void testLogin() {
        test.info("Starting login test");
        test.pass("Login successful");
    }

    @AfterSuite
    public void tearDown() {
        extent.flush();  // Write report to file
    }
}
```

### Concept 3: Screenshots on Failure

**Capture Screenshot Method:**
```java
import org.openqa.selenium.*;
import org.apache.commons.io.FileUtils;
import java.io.File;
import java.io.IOException;

public class ScreenshotUtil {
    public static String captureScreenshot(WebDriver driver, String testName) {
        TakesScreenshot ts = (TakesScreenshot) driver;
        File source = ts.getScreenshotAs(OutputType.FILE);
        String destination = "screenshots/" + testName + "_" + System.currentTimeMillis() + ".png";

        try {
            FileUtils.copyFile(source, new File(destination));
        } catch (IOException e) {
            e.printStackTrace();
        }

        return destination;
    }
}
```

**Use in @AfterMethod:**
```java
@AfterMethod
public void tearDown(ITestResult result) {
    if (result.getStatus() == ITestResult.FAILURE) {
        String screenshotPath = ScreenshotUtil.captureScreenshot(driver, result.getName());
        test.fail("Test failed").addScreenshotFromPath(screenshotPath);
    }
    driver.quit();
}
```

### Concept 4: Allure Reports

**Setup (pom.xml):**
```xml
<dependency>
    <groupId>io.qameta.allure</groupId>
    <artifactId>allure-testng</artifactId>
    <version>2.24.0</version>
</dependency>
```

**Usage:**
```java
import io.qameta.allure.*;

@Epic("E-commerce")
@Feature("Login")
public class LoginTest {

    @Test
    @Story("Valid Login")
    @Severity(SeverityLevel.CRITICAL)
    @Description("Test login with valid credentials")
    public void testValidLogin() {
        Allure.step("Navigate to login page");
        Allure.step("Enter username");
        Allure.step("Enter password");
        Allure.step("Click login");
        Allure.step("Verify dashboard");
    }
}
```

**Generate Report:**
```bash
allure serve allure-results
```

---

## ⚠️ Common Mistakes

### Mistake 1: Forgetting extent.flush()
```java
@AfterSuite
public void tearDown() {
    // Missing extent.flush() → No report generated!
}
```
**Fix:** Always call `extent.flush()`

### Mistake 2: Screenshot Path Issues
```java
test.fail("Failed").addScreenshotFromPath("screenshot.png");  // Relative path might not work
```
**Fix:** Use absolute path or proper relative path

---

## 💡 Pro Tips

**Tip 1:** Create separate reports directory
```bash
reports/
├── extent-report.html
├── allure-results/
└── screenshots/
```

**Tip 2:** Add test info to reports
```java
@BeforeMethod
public void startTest(Method method) {
    test = extent.createTest(method.getName());
    test.assignAuthor("Your Name");
    test.assignCategory("Smoke");
}
```

**Tip 3:** Log important steps
```java
test.info("Step 1: Navigate to login");
test.info("Step 2: Enter credentials");
test.pass("Login successful");
```

---

## 🎓 Interview Prep

**Q: How do you generate test reports in your framework?**

**A:** I use ExtentReports for detailed HTML reports with screenshots. In my base test class, I initialize ExtentReports in @BeforeSuite, create a test instance for each test method in @BeforeMethod, log important steps during test execution, and capture screenshots on failure in @AfterMethod. Finally, I call extent.flush() in @AfterSuite to generate the report. This provides stakeholders with clear, visual reports showing test results, execution time, and failure screenshots.

---

**🎯 Ready to practice? Move to Day-5-Practice-Exercises.md**
