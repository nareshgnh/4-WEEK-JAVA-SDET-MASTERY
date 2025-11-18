# DAY 5 DEBUGGING CHALLENGES

## Challenge 1: No Report Generated
```java
@AfterSuite
public void tearDown() {
    // Missing extent.flush()!
}
```
**Fix:** Add `extent.flush();`

## Challenge 2: Screenshot Not Found
```java
test.fail("Failed").addScreenshotFromPath("screenshot.png");  // Wrong path!
```
**Fix:** Use correct path: `"screenshots/test.png"` or absolute path

## Challenge 3: Extent Not Initialized
```java
@BeforeMethod
public void startTest() {
    test = extent.createTest("test");  // extent is null!
}
```
**Fix:** Initialize in @BeforeSuite first

## Challenge 4: Screenshot for Closed Browser
```java
@AfterMethod
public void tearDown(ITestResult result) {
    driver.quit();  // Browser closed!
    if (result.getStatus() == ITestResult.FAILURE) {
        captureScreenshot(driver, "test");  // ERROR: No browser!
    }
}
```
**Fix:** Capture screenshot BEFORE quitting:
```java
if (result.getStatus() == ITestResult.FAILURE) {
    captureScreenshot(driver, "test");
}
driver.quit();
```

---

**🔧 Debugging Complete! Move to Day-5-Project-Guide.md**
