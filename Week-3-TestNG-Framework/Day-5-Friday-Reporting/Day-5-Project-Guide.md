# DAY 5 PROJECT: BEAUTIFUL TEST REPORTS

## 🎯 Project Overview
Build a complete reporting framework with ExtentReports and screenshot capture.

## 📋 Implementation

### BaseTest.java
```java
import com.aventstack.extentreports.*;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;
import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.ITestResult;
import org.testng.annotations.*;
import java.io.File;
import org.apache.commons.io.FileUtils;

public class BaseTest {
    protected WebDriver driver;
    protected static ExtentReports extent;
    protected ExtentTest test;

    @BeforeSuite
    public void setupReport() {
        ExtentSparkReporter spark = new ExtentSparkReporter("reports/TestReport.html");
        spark.config().setDocumentTitle("Automation Report");
        spark.config().setReportName("Test Results");

        extent = new ExtentReports();
        extent.attachReporter(spark);
        extent.setSystemInfo("OS", "Windows");
        extent.setSystemInfo("Browser", "Chrome");
    }

    @BeforeMethod
    public void setup(Method method) {
        test = extent.createTest(method.getName());
        driver = new ChromeDriver();
        driver.manage().window().maximize();
    }

    @AfterMethod
    public void tearDown(ITestResult result) {
        if (result.getStatus() == ITestResult.FAILURE) {
            String screenshotPath = captureScreenshot(result.getName());
            test.fail("Test Failed").addScreenshotFromPath(screenshotPath);
            test.fail(result.getThrowable());
        } else if (result.getStatus() == ITestResult.SUCCESS) {
            test.pass("Test Passed");
        }

        if (driver != null) {
            driver.quit();
        }
    }

    @AfterSuite
    public void tearDownReport() {
        extent.flush();
    }

    private String captureScreenshot(String testName) {
        TakesScreenshot ts = (TakesScreenshot) driver;
        File source = ts.getScreenshotAs(OutputType.FILE);
        String path = "screenshots/" + testName + "_" + System.currentTimeMillis() + ".png";

        try {
            FileUtils.copyFile(source, new File(path));
        } catch (Exception e) {
            e.printStackTrace();
        }

        return new File(path).getAbsolutePath();
    }
}
```

### LoginTest.java
```java
public class LoginTest extends BaseTest {

    @Test
    public void testValidLogin() {
        test.info("Navigate to Sauce Demo");
        driver.get("https://www.saucedemo.com");

        test.info("Enter username");
        driver.findElement(By.id("user-name")).sendKeys("standard_user");

        test.info("Enter password");
        driver.findElement(By.id("password")).sendKeys("secret_sauce");

        test.info("Click login button");
        driver.findElement(By.id("login-button")).click();

        test.pass("Login successful - redirected to products page");
        Assert.assertTrue(driver.getCurrentUrl().contains("inventory"));
    }
}
```

## ✅ Result
Professional HTML report with:
- Test execution summary
- Pass/fail status with colors
- Execution time
- Screenshots for failures
- Step-by-step logs

---

**🎉 Project Complete! Move to Day-5-Cheat-Sheet.md**
