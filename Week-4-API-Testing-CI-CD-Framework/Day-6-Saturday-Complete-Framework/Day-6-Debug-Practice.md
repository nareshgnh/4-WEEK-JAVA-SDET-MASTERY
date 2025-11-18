# DAY 6 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

Framework-level bugs are harder to spot than test-level bugs. As an SDET, you'll spend significant time debugging:
- Configuration loading issues
- Singleton pattern problems
- Thread safety issues in parallel execution
- Docker connectivity failures
- Reporting integration errors

**These challenges mirror real issues you'll face in production frameworks.**

**Time Required:** 20-25 minutes

---

## Challenge 1: Configuration File Not Found

**Broken Code:**
```java
package config;

import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

public class ConfigReader {
    private static Properties properties;
    private static final String CONFIG_FILE = "config.properties";

    static {
        try {
            properties = new Properties();
            FileInputStream input = new FileInputStream(CONFIG_FILE);
            properties.load(input);
        } catch (IOException e) {
            throw new RuntimeException("Failed to load config file");
        }
    }

    public static String get(String key) {
        return properties.getProperty(key);
    }

    public static String getBaseUrl() {
        return get("base.url");
    }
}
```

**Test Code:**
```java
public class ConfigTest {
    public static void main(String[] args) {
        System.out.println("Base URL: " + ConfigReader.getBaseUrl());
    }
}
```

**Error Message:**
```
Exception in thread "main" java.lang.ExceptionInInitializerError
Caused by: java.lang.RuntimeException: Failed to load config file
Caused by: java.io.FileNotFoundException: config.properties (No such file or directory)
```

**Your Task:**
1. Identify why the config file is not found
2. Explain the difference between FileInputStream and ClassLoader approach
3. Fix the code to properly load from src/test/resources

**Hints:**
- Think about where Maven places resources
- FileInputStream looks in project root, not classpath
- ClassLoader is the correct approach for resources

---

**Solution:**

**Bug Location:** Line 11 - `FileInputStream input = new FileInputStream(CONFIG_FILE);`

**Explanation:**
`FileInputStream` looks for files in the project's root directory (filesystem path), not in the classpath. Maven copies resources from `src/test/resources` to `target/test-classes`, which is on the classpath. When packaged in a JAR, filesystem paths don't work at all.

**Fixed Code:**
```java
package config;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class ConfigReader {
    private static Properties properties;
    private static final String CONFIG_FILE = "config.properties";

    static {
        try {
            properties = new Properties();
            // Use ClassLoader to load from classpath (src/test/resources)
            InputStream input = ConfigReader.class
                .getClassLoader()
                .getResourceAsStream(CONFIG_FILE);

            if (input == null) {
                throw new RuntimeException("Unable to find " + CONFIG_FILE);
            }

            properties.load(input);
        } catch (IOException e) {
            throw new RuntimeException("Failed to load config file", e);
        }
    }

    public static String get(String key) {
        return properties.getProperty(key);
    }

    public static String getBaseUrl() {
        return get("base.url");
    }
}
```

**Key Learning:**
- **FileInputStream**: Filesystem path (❌ for resources)
- **ClassLoader.getResourceAsStream()**: Classpath resource (✅ correct)
- Always check if InputStream is null before loading
- Resources in `src/test/resources` end up on classpath

**Interview Tip:** "I use ClassLoader to load configuration files from the classpath because it works both in IDE and when packaged as JAR. FileInputStream would only work with absolute paths, which aren't portable."

---

## Challenge 2: Singleton Not Thread-Safe (Parallel Execution Fails)

**Broken Code:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import io.github.bonigarcia.wdm.WebDriverManager;

public class DriverManager {
    private static WebDriver driver;  // Shared across all threads!

    public static WebDriver getDriver() {
        if (driver == null) {
            WebDriverManager.chromedriver().setup();
            driver = new ChromeDriver();
        }
        return driver;
    }

    public static void quitDriver() {
        if (driver != null) {
            driver.quit();
            driver = null;
        }
    }
}
```

**Test Code (Parallel Execution):**
```java
public class ParallelTest {

    @Test
    public void test1() {
        WebDriver driver = DriverManager.getDriver();
        driver.get("https://www.google.com");
        System.out.println("Test 1: " + driver.getTitle());
        DriverManager.quitDriver();
    }

    @Test
    public void test2() {
        WebDriver driver = DriverManager.getDriver();
        driver.get("https://www.wikipedia.org");
        System.out.println("Test 2: " + driver.getTitle());
        DriverManager.quitDriver();
    }
}
```

**testng.xml:**
```xml
<suite name="Parallel Suite" parallel="methods" thread-count="2">
    <test name="Tests">
        <classes>
            <class name="ParallelTest"/>
        </classes>
    </test>
</suite>
```

**Error:**
```
org.openqa.selenium.NoSuchSessionException: Session ID is null. Using WebDriver after calling quit()?
Test 1: Google
Test 2: [ERROR - session not found]
```

**Your Task:**
1. Explain why parallel tests are failing
2. What happens when Thread A quits while Thread B is using the driver?
3. Fix using ThreadLocal

**Hints:**
- Both threads share the same `static WebDriver driver`
- Thread A quits the driver, Thread B tries to use it
- ThreadLocal gives each thread its own copy

---

**Solution:**

**Bug Location:** Line 6 - `private static WebDriver driver;` (shared across threads)

**Explanation:**
When running tests in parallel:
1. **Thread A** calls `getDriver()`, creates driver, navigates to Google
2. **Thread B** calls `getDriver()`, sees `driver != null`, reuses **same driver**
3. **Thread A** calls `quitDriver()`, closes the browser
4. **Thread B** tries to use the driver → **NoSuchSessionException** (driver was quit)

Without ThreadLocal, all threads share one driver instance. When one thread quits, it affects all threads.

**Fixed Code:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import io.github.bonigarcia.wdm.WebDriverManager;

public class DriverManager {
    // ThreadLocal: Each thread gets its own driver instance
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        // Check if current thread has a driver
        if (driver.get() == null) {
            WebDriverManager.chromedriver().setup();
            driver.set(new ChromeDriver());  // Set for current thread
        }
        return driver.get();  // Get current thread's driver
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();  // Remove from ThreadLocal (prevents memory leak)
        }
    }
}
```

**Key Learning:**
- **Static variable**: Shared across ALL threads (❌ parallel execution)
- **ThreadLocal**: Each thread has its own copy (✅ parallel safe)
- **driver.set()**: Sets value for current thread
- **driver.get()**: Gets value for current thread
- **driver.remove()**: Cleans up ThreadLocal (important!)

**Interview Tip:** "For parallel execution, I use ThreadLocal to ensure each thread has its own WebDriver instance. Without it, threads would interfere with each other, causing flaky test failures."

---

## Challenge 3: System Property Not Overriding Config

**Broken Code:**
```java
package config;

import java.io.InputStream;
import java.util.Properties;

public class ConfigReader {
    private static Properties properties;

    static {
        try {
            properties = new Properties();
            InputStream input = ConfigReader.class
                .getClassLoader()
                .getResourceAsStream("config.properties");
            properties.load(input);
        } catch (Exception e) {
            throw new RuntimeException("Failed to load config");
        }
    }

    public static String getBrowser() {
        return properties.getProperty("browser");
    }
}
```

**config.properties:**
```properties
browser=chrome
```

**Command:**
```bash
# Trying to run with Firefox
mvn test -Dbrowser=firefox
```

**Problem:**
Tests still run on Chrome instead of Firefox. The `-Dbrowser=firefox` system property is ignored.

**Your Task:**
1. Why is the system property not being used?
2. What is the correct priority order for configuration?
3. Fix the code to support system property override

**Hints:**
- System.getProperty("browser") gets command-line argument
- Need to check system property BEFORE config file
- Use fallback chain: System Property → Config File → Default Value

---

**Solution:**

**Bug Location:** Line 18 - `getBrowser()` only reads from properties file

**Explanation:**
The code only reads from the properties file, ignoring command-line system properties. For flexible configuration, you need a priority chain:
1. **System Property** (highest priority) - runtime override
2. **Config File** - default values
3. **Hardcoded Default** (lowest priority) - fallback

**Fixed Code:**
```java
package config;

import java.io.InputStream;
import java.util.Properties;

public class ConfigReader {
    private static Properties properties;

    static {
        try {
            properties = new Properties();
            InputStream input = ConfigReader.class
                .getClassLoader()
                .getResourceAsStream("config.properties");
            properties.load(input);
        } catch (Exception e) {
            throw new RuntimeException("Failed to load config");
        }
    }

    public static String getBrowser() {
        // Priority: System Property → Config File → Default
        return System.getProperty("browser",
            properties.getProperty("browser", "chrome")
        );
    }

    public static boolean isHeadless() {
        // Support both -Dheadless=true and config file
        return Boolean.parseBoolean(
            System.getProperty("headless",
                properties.getProperty("headless", "false")
            )
        );
    }

    public static String getEnvironment() {
        // Common pattern for environment switching
        return System.getProperty("env",
            properties.getProperty("env", "qa")
        );
    }
}
```

**How It Works:**
```bash
# No system property: uses config file value (chrome)
mvn test

# System property override: uses firefox
mvn test -Dbrowser=firefox

# Multiple overrides
mvn test -Dbrowser=firefox -Dheadless=true -Denv=prod
```

**Key Learning:**
- **System.getProperty("key", default)**: Checks command-line arguments first
- **Priority chain**: System Property → Config File → Hardcoded Default
- **Flexibility**: Same tests run with different configs without code changes

**Interview Tip:** "I implement configuration with a priority hierarchy: system properties for runtime overrides, config files for defaults, and hardcoded fallbacks. This allows running the same test suite against different environments using command-line flags."

---

## Challenge 4: Docker Selenium Connection Refused

**Broken Code:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.chrome.ChromeOptions;
import java.net.URL;

public class DockerDriverManager {

    public static WebDriver getRemoteDriver() {
        try {
            String hubUrl = "http://localhost:4444/wd/hub";
            ChromeOptions options = new ChromeOptions();
            DesiredCapabilities capabilities = new DesiredCapabilities();
            capabilities.setCapability(ChromeOptions.CAPABILITY, options);

            return new RemoteWebDriver(new URL(hubUrl), capabilities);
        } catch (Exception e) {
            throw new RuntimeException("Failed to connect to Selenium Grid", e);
        }
    }
}
```

**docker-compose.yml:**
```yaml
version: '3'
services:
  selenium-hub:
    image: selenium/hub:latest
    ports:
      - "4444:4444"

  chrome-node:
    image: selenium/node-chrome:latest
    depends_on:
      - selenium-hub
    environment:
      - SE_EVENT_BUS_HOST=selenium-hub
      - SE_EVENT_BUS_PUBLISH_PORT=4442
      - SE_EVENT_BUS_SUBSCRIBE_PORT=4443
```

**Error:**
```
java.net.ConnectException: Connection refused (Connection refused)
    at DockerDriverManager.getRemoteDriver
```

**Steps to Reproduce:**
```bash
# Start Docker Selenium
docker-compose up -d

# Run test
mvn test -Duse.docker=true
```

**Your Task:**
1. Why is the connection refused?
2. What ports does Selenium Grid 4 use?
3. How to verify Grid is running?
4. Fix the code

**Hints:**
- Selenium Grid 4 changed the default URL structure
- Check http://localhost:4444/ui to see if Grid is running
- Modern Grid uses http://localhost:4444 (not /wd/hub)

---

**Solution:**

**Bug Location:** Line 11 - Hub URL for Selenium Grid 4

**Explanation:**
**Selenium Grid 3** used: `http://localhost:4444/wd/hub`
**Selenium Grid 4** uses: `http://localhost:4444` (no `/wd/hub` suffix)

The code uses the old Grid 3 URL pattern. Selenium Grid 4 still accepts `/wd/hub` for backward compatibility, but the error suggests the Grid isn't running or the URL is wrong.

**Debugging Steps:**
```bash
# 1. Check if containers are running
docker ps

# 2. Check Grid console (should open in browser)
open http://localhost:4444/ui

# 3. Check container logs
docker logs <container_name>

# 4. Check if port 4444 is accessible
curl http://localhost:4444/status
```

**Fixed Code:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.chrome.ChromeOptions;
import config.ConfigReader;
import java.net.URL;
import java.net.MalformedURLException;

public class DockerDriverManager {

    public static WebDriver getRemoteDriver() {
        // Modern Selenium Grid 4 URL (no /wd/hub)
        String hubUrl = ConfigReader.get("selenium.hub", "http://localhost:4444");

        System.out.println("Connecting to Selenium Grid: " + hubUrl);

        ChromeOptions options = new ChromeOptions();
        options.addArguments("--start-maximized");
        options.addArguments("--disable-notifications");

        DesiredCapabilities capabilities = new DesiredCapabilities();
        capabilities.setCapability(ChromeOptions.CAPABILITY, options);

        try {
            RemoteWebDriver driver = new RemoteWebDriver(
                new URL(hubUrl),
                capabilities
            );
            System.out.println("Successfully connected to Grid");
            return driver;
        } catch (MalformedURLException e) {
            throw new RuntimeException("Invalid Grid URL: " + hubUrl, e);
        } catch (Exception e) {
            System.err.println("Failed to connect to Selenium Grid at: " + hubUrl);
            System.err.println("Make sure Grid is running: docker-compose up -d");
            System.err.println("Check Grid UI: http://localhost:4444/ui");
            throw new RuntimeException("Failed to connect to Selenium Grid", e);
        }
    }
}
```

**Updated docker-compose.yml (Complete Grid Setup):**
```yaml
version: '3'
services:
  selenium-hub:
    image: selenium/hub:latest
    ports:
      - "4444:4444"
      - "4442:4442"  # Event bus publish
      - "4443:4443"  # Event bus subscribe
    environment:
      - SE_SESSION_REQUEST_TIMEOUT=300
      - SE_NODE_SESSION_TIMEOUT=300

  chrome-node:
    image: selenium/node-chrome:latest
    depends_on:
      - selenium-hub
    environment:
      - SE_EVENT_BUS_HOST=selenium-hub
      - SE_EVENT_BUS_PUBLISH_PORT=4442
      - SE_EVENT_BUS_SUBSCRIBE_PORT=4443
      - SE_NODE_MAX_SESSIONS=3
    shm_size: '2gb'  # Important for Chrome stability
    ports:
      - "7900:7900"  # VNC port
```

**Verification:**
```bash
# Start Grid
docker-compose up -d

# Wait for Grid to be ready (10-15 seconds)
sleep 15

# Check status
curl http://localhost:4444/status | jq '.value.ready'
# Should return: true

# View Grid UI
open http://localhost:4444/ui

# Run tests
mvn test -Duse.docker=true
```

**Key Learning:**
- Selenium Grid 4 uses different URL structure than Grid 3
- Always verify Grid is running before running tests
- Add helpful error messages for debugging
- `shm_size: '2gb'` is important for Chrome stability in Docker

**Interview Tip:** "When working with Docker Selenium, I ensure proper error handling and verification. I check if the Grid is accessible before attempting to create sessions, and provide clear error messages pointing to the Grid UI for debugging."

---

## Challenge 5: ExtentReports Not Generated (Missing flush())

**Broken Code:**
```java
package reporting;

import com.aventstack.extentreports.ExtentReports;
import com.aventstack.extentreports.ExtentTest;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;

public class ExtentManager {
    private static ExtentReports extent;
    private static ThreadLocal<ExtentTest> test = new ThreadLocal<>();

    public static ExtentReports getExtent() {
        if (extent == null) {
            ExtentSparkReporter reporter = new ExtentSparkReporter("test-output/report.html");
            extent = new ExtentReports();
            extent.attachReporter(reporter);
        }
        return extent;
    }

    public static void setTest(ExtentTest extentTest) {
        test.set(extentTest);
    }

    public static ExtentTest getTest() {
        return test.get();
    }
}
```

**Listener:**
```java
package listeners;

import com.aventstack.extentreports.Status;
import org.testng.*;
import reporting.ExtentManager;

public class ExtentListener implements ITestListener {

    @Override
    public void onTestStart(ITestResult result) {
        ExtentTest test = ExtentManager.getExtent()
            .createTest(result.getMethod().getMethodName());
        ExtentManager.setTest(test);
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        ExtentManager.getTest().log(Status.PASS, "Test passed");
    }

    @Override
    public void onTestFailure(ITestResult result) {
        ExtentManager.getTest().log(Status.FAIL, "Test failed");
    }

    // Missing onFinish method!
}
```

**Problem:**
After running tests, `test-output/report.html` file is created but is empty (0 bytes) or shows "No tests found".

**Your Task:**
1. Why isn't the report being generated?
2. What is the purpose of `flush()`?
3. Fix the listener

**Hints:**
- ExtentReports buffers data in memory
- flush() writes buffered data to disk
- Call flush() after all tests complete

---

**Solution:**

**Bug Location:** Missing `onFinish()` method in listener

**Explanation:**
ExtentReports accumulates test data in memory during execution. The `flush()` method writes this buffered data to the HTML file. Without calling `flush()`, the report file is created but remains empty.

**Lifecycle:**
1. `onStart()`: Initialize ExtentReports
2. `onTestStart()`: Create test entry
3. `onTestSuccess/Failure()`: Log results
4. `onFinish()`: **MUST call flush()** to write to disk

**Fixed Code:**
```java
package listeners;

import com.aventstack.extentreports.Status;
import org.testng.*;
import reporting.ExtentManager;

public class ExtentListener implements ITestListener {

    @Override
    public void onStart(ITestContext context) {
        // Initialize ExtentReports at the start of suite
        ExtentManager.getExtent();
        System.out.println("ExtentReports initialized");
    }

    @Override
    public void onTestStart(ITestResult result) {
        ExtentTest test = ExtentManager.getExtent()
            .createTest(result.getMethod().getMethodName());
        ExtentManager.setTest(test);
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        ExtentManager.getTest().log(Status.PASS,
            "Test passed: " + result.getMethod().getMethodName());
    }

    @Override
    public void onTestFailure(ITestResult result) {
        ExtentManager.getTest().log(Status.FAIL,
            "Test failed: " + result.getMethod().getMethodName());
        ExtentManager.getTest().log(Status.FAIL, result.getThrowable());
    }

    @Override
    public void onTestSkipped(ITestResult result) {
        ExtentManager.getTest().log(Status.SKIP,
            "Test skipped: " + result.getMethod().getMethodName());
    }

    @Override
    public void onFinish(ITestContext context) {
        // THIS IS CRITICAL: flush() writes report to disk
        ExtentManager.getExtent().flush();
        System.out.println("ExtentReports flushed to: test-output/report.html");
    }
}
```

**Key Learning:**
- **flush()** is mandatory to generate the report file
- Call `flush()` in `onFinish()` (after all tests complete)
- Without `flush()`, report data stays in memory only
- Good practice: Print confirmation message after flush

**Interview Tip:** "In ExtentReports integration, the flush() method is critical—it writes all buffered test results to the HTML file. I call it in the TestNG listener's onFinish() method to ensure the report is generated after all tests complete."

---

## Challenge 6: Builder Pattern Returns Null

**Broken Code:**
```java
package api;

import io.restassured.response.Response;
import io.restassured.specification.RequestSpecification;
import static io.restassured.RestAssured.given;

public class APIRequestBuilder {
    private RequestSpecification request;

    private APIRequestBuilder() {
        request = given();
    }

    public static APIRequestBuilder builder() {
        return new APIRequestBuilder();
    }

    public APIRequestBuilder baseUri(String uri) {
        request.baseUri(uri);
        // Missing return statement!
    }

    public APIRequestBuilder header(String key, String value) {
        request.header(key, value);
        // Missing return statement!
    }

    public Response get(String endpoint) {
        return request.get(endpoint);
    }
}
```

**Test Code:**
```java
Response response = APIRequestBuilder.builder()
    .baseUri("https://jsonplaceholder.typicode.com")
    .header("Content-Type", "application/json")
    .get("/users/1");
```

**Error:**
```
java.lang.NullPointerException
    at APIRequestBuilder.header
```

**Your Task:**
1. Why is there a NullPointerException?
2. What's wrong with the builder methods?
3. Fix the code

**Hints:**
- Builder pattern methods must return `this`
- Without return, method returns `null` (void methods)
- Chaining fails when intermediate method returns null

---

**Solution:**

**Bug Location:** Lines 17 and 22 - Missing `return this;` statements

**Explanation:**
Builder pattern works through **method chaining**:
```java
builder()           // Returns APIRequestBuilder instance
  .baseUri("...")   // Should return same instance for chaining
  .header("...")    // Should return same instance for chaining
  .get("/endpoint") // Terminal method, returns Response
```

Without `return this;`, the methods return `null` (default return for void methods). When you try to call `.header()` on `null`, you get NullPointerException.

**Fixed Code:**
```java
package api;

import io.restassured.response.Response;
import io.restassured.specification.RequestSpecification;
import io.restassured.http.ContentType;
import static io.restassured.RestAssured.given;

public class APIRequestBuilder {
    private RequestSpecification request;

    private APIRequestBuilder() {
        request = given();
    }

    public static APIRequestBuilder builder() {
        return new APIRequestBuilder();
    }

    // Builder methods MUST return this for chaining
    public APIRequestBuilder baseUri(String uri) {
        request.baseUri(uri);
        return this;  // ✅ Essential for chaining
    }

    public APIRequestBuilder header(String key, String value) {
        request.header(key, value);
        return this;  // ✅ Essential for chaining
    }

    public APIRequestBuilder queryParam(String key, String value) {
        request.queryParam(key, value);
        return this;
    }

    public APIRequestBuilder body(Object body) {
        request.contentType(ContentType.JSON);
        request.body(body);
        return this;
    }

    // Terminal methods return Response (not this)
    public Response get(String endpoint) {
        return request.get(endpoint);
    }

    public Response post(String endpoint) {
        return request.post(endpoint);
    }
}
```

**How Chaining Works:**
```java
APIRequestBuilder.builder()      // Returns APIRequestBuilder instance
    .baseUri("...")               // Modifies request, returns this
    .header("key", "value")       // Modifies request, returns this
    .body(user)                   // Modifies request, returns this
    .post("/users");              // Returns Response
```

**Key Learning:**
- **Builder methods**: Return `this` to enable chaining
- **Terminal methods**: Return the final result (Response, Object, etc.)
- **Fluent API**: Reads like English, very readable
- **Common mistake**: Forgetting `return this;`

**Interview Tip:** "In Builder pattern, intermediate configuration methods return 'this' to enable method chaining, while terminal methods return the final result. This creates a fluent API that's readable and flexible."

---

## Challenge 7: Memory Leak in Parallel Execution

**Broken Code:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import io.github.bonigarcia.wdm.WebDriverManager;

public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            WebDriverManager.chromedriver().setup();
            driver.set(new ChromeDriver());
        }
        return driver.get();
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            // Missing: driver.remove()
        }
    }
}
```

**Test Suite (100 tests running in parallel):**
```xml
<suite name="Large Suite" parallel="methods" thread-count="10">
    <test name="Tests">
        <classes>
            <class name="LoginTests"/>
            <class name="DashboardTests"/>
            <!-- 98 more test classes... -->
        </classes>
    </test>
</suite>
```

**Problem:**
After running the full suite:
- Memory usage keeps increasing
- Test execution slows down over time
- Eventually: `OutOfMemoryError: Java heap space`

**Your Task:**
1. Why is memory usage increasing?
2. What causes the memory leak in ThreadLocal?
3. How does `remove()` prevent memory leaks?
4. Fix the code

**Hints:**
- ThreadLocal stores data per thread
- Without remove(), ThreadLocal holds references even after driver.quit()
- Thread pools reuse threads, old ThreadLocal data accumulates

---

**Solution:**

**Bug Location:** Line 17 - Missing `driver.remove()`

**Explanation:**
**ThreadLocal Memory Leak Scenario:**
1. Thread pool has 10 threads (for parallel execution)
2. Each thread runs 10 tests (100 tests total)
3. Each test calls `getDriver()`, which sets ThreadLocal value
4. Each test calls `quitDriver()`, which quits browser but **doesn't remove from ThreadLocal**
5. ThreadLocal still holds reference to the quit driver
6. Thread is reused for next test, ThreadLocal accumulates old values
7. After 100 tests: 100 driver references stored in memory
8. Result: **Memory leak → OutOfMemoryError**

**Why This Happens:**
- Thread pools **reuse threads** for efficiency
- ThreadLocal values persist **for the lifetime of the thread**
- Without `remove()`, each test adds a new ThreadLocal entry
- Old driver references can't be garbage collected (ThreadLocal holds them)

**Fixed Code:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import io.github.bonigarcia.wdm.WebDriverManager;

public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            WebDriverManager.chromedriver().setup();
            driver.set(new ChromeDriver());
        }
        return driver.get();
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();  // ✅ CRITICAL: Removes from ThreadLocal
        }
    }
}
```

**Best Practice Pattern:**
```java
@AfterMethod(alwaysRun = true)
public void teardown() {
    // alwaysRun = true ensures cleanup even if test fails
    DriverManager.quitDriver();
}
```

**Memory Usage:**
```
Without driver.remove():
Thread-1: [Driver1, Driver2, Driver3, ...]  ❌ Keeps growing
Thread-2: [Driver1, Driver2, Driver3, ...]  ❌ Keeps growing

With driver.remove():
Thread-1: [Driver1] → removed → [Driver2] → removed  ✅ Constant memory
Thread-2: [Driver1] → removed → [Driver2] → removed  ✅ Constant memory
```

**Key Learning:**
- **Always call `remove()` after `get()`** when using ThreadLocal
- ThreadLocal values persist for thread lifetime
- Thread pools reuse threads → old values accumulate
- Memory leak is subtle: tests pass but memory grows
- Use `alwaysRun=true` in @AfterMethod to ensure cleanup

**Interview Tip:** "When using ThreadLocal for WebDriver in parallel execution, I always call remove() after quitting the driver. This prevents memory leaks because thread pools reuse threads, and without remove(), old ThreadLocal values accumulate, eventually causing OutOfMemoryError."

---

## 🎯 Debugging Checklist

**After completing challenges, check your understanding:**

- [ ] I can load configuration files from classpath
- [ ] I understand why ThreadLocal is needed for parallel execution
- [ ] I know how to implement system property overrides
- [ ] I can debug Docker Selenium connection issues
- [ ] I know that ExtentReports requires flush() to write reports
- [ ] I understand Builder pattern requires `return this;`
- [ ] I always call `remove()` after using ThreadLocal

**Common Framework Debugging Tools:**
```java
// Print all environment variables
System.getenv().forEach((k, v) -> System.out.println(k + "=" + v));

// Print all system properties
System.getProperties().forEach((k, v) -> System.out.println(k + "=" + v));

// Check if file exists on classpath
InputStream is = getClass().getClassLoader().getResourceAsStream("file.properties");
System.out.println("File found: " + (is != null));

// Print current thread name
System.out.println("Thread: " + Thread.currentThread().getName());

// Print WebDriver session ID
System.out.println("Session: " + ((RemoteWebDriver) driver).getSessionId());
```

---

## 💡 Pro Debugging Tips

**Tip 1: Add Detailed Logging**
```java
public static WebDriver getDriver() {
    System.out.println("[" + Thread.currentThread().getName() + "] Getting driver");
    if (driver.get() == null) {
        System.out.println("[" + Thread.currentThread().getName() + "] Creating new driver");
        driver.set(createDriver());
    } else {
        System.out.println("[" + Thread.currentThread().getName() + "] Reusing existing driver");
    }
    return driver.get();
}
```

**Tip 2: Validate Configuration on Startup**
```java
@BeforeSuite
public void validateConfig() {
    System.out.println("=== Configuration ===");
    System.out.println("Base URL: " + ConfigReader.getBaseUrl());
    System.out.println("Browser: " + ConfigReader.getBrowser());
    System.out.println("Headless: " + ConfigReader.isHeadless());
    System.out.println("Docker: " + ConfigReader.get("use.docker"));
    System.out.println("=====================");
}
```

**Tip 3: Health Check for Docker Selenium**
```java
@BeforeSuite
public void checkSeleniumGrid() {
    if (Boolean.parseBoolean(ConfigReader.get("use.docker"))) {
        try {
            String hubUrl = ConfigReader.get("selenium.hub");
            Response response = given().get(hubUrl + "/status");
            if (response.statusCode() == 200) {
                System.out.println("✅ Selenium Grid is ready");
            } else {
                throw new RuntimeException("❌ Selenium Grid is not ready");
            }
        } catch (Exception e) {
            System.err.println("❌ Cannot reach Selenium Grid");
            System.err.println("Run: docker-compose up -d");
            throw e;
        }
    }
}
```

---

**🎉 Debugging Challenges Complete!** You can now diagnose and fix common framework issues.
