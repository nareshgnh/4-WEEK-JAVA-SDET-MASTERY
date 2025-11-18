# DAY 5 PRACTICE EXERCISES

## Exercise 1: TestNG Default Reports
Run any test and examine default reports:
```bash
mvn test
# Check: test-output/index.html
```

## Exercise 2: ExtentReports Setup
```java
import com.aventstack.extentreports.*;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;
import org.testng.annotations.*;

public class ExtentReportTest {
    ExtentReports extent;
    ExtentTest test;

    @BeforeSuite
    public void setup() {
        ExtentSparkReporter spark = new ExtentSparkReporter("reports/extent.html");
        extent = new ExtentReports();
        extent.attachReporter(spark);
    }

    @BeforeMethod
    public void startTest(Method method) {
        test = extent.createTest(method.getName());
    }

    @Test
    public void test1() {
        test.info("Test 1 started");
        test.pass("Test 1 passed");
    }

    @AfterSuite
    public void tearDown() {
        extent.flush();
    }
}
```

## Exercise 3: Screenshots on Failure
```java
@AfterMethod
public void tearDown(ITestResult result) {
    if (result.getStatus() == ITestResult.FAILURE) {
        String screenshot = captureScreenshot(driver, result.getName());
        test.fail("Test failed").addScreenshotFromPath(screenshot);
    }
    driver.quit();
}

public String captureScreenshot(WebDriver driver, String name) {
    TakesScreenshot ts = (TakesScreenshot) driver;
    File source = ts.getScreenshotAs(OutputType.FILE);
    String path = "screenshots/" + name + ".png";
    FileUtils.copyFile(source, new File(path));
    return path;
}
```

## Exercise 4: Complete Reporting Framework
Build test class with full ExtentReports integration:
```java
public class ReportingFramework extends BaseTest {
    @Test
    public void testLogin() {
        test.info("Navigate to login page");
        driver.get("https://www.saucedemo.com");

        test.info("Enter credentials");
        driver.findElement(By.id("user-name")).sendKeys("standard_user");
        driver.findElement(By.id("password")).sendKeys("secret_sauce");

        test.info("Click login");
        driver.findElement(By.id("login-button")).click();

        test.pass("Login successful");
    }
}
```

---

**🎉 Practice Complete! Move to Day-5-Debug-Practice.md**
