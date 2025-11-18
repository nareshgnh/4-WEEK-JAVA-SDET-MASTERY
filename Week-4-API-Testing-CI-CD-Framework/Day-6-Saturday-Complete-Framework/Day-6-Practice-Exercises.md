# DAY 6 PRACTICE EXERCISES

## 🎯 Today's Practice Goal

By completing these exercises, you will build a production-ready hybrid framework with:
- Configuration management
- Design patterns (Singleton, Factory, Builder)
- Cross-browser support
- Advanced reporting
- Docker integration

**Time Required:** 60-75 minutes

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Build a Configuration Reader

**Objective:** Create a ConfigReader utility that reads properties files

**Task:**
Create a ConfigReader class that:
1. Loads `config.properties` from `src/test/resources`
2. Provides methods to get values
3. Supports system property overrides
4. Has convenient methods for common configs

**Starter Code:**
```java
// src/test/resources/config.properties
base.url=https://jsonplaceholder.typicode.com
ui.url=https://the-internet.herokuapp.com
browser=chrome
timeout.implicit=10
timeout.explicit=20
headless=false

// src/main/java/config/ConfigReader.java
package config;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class ConfigReader {
    private static Properties properties;

    // TODO: Load properties file in static block

    // TODO: Method to get property value
    public static String get(String key) {
        // Your code here
    }

    // TODO: Method to get property with default value
    public static String get(String key, String defaultValue) {
        // Your code here
    }

    // TODO: Convenience methods
    public static String getBaseUrl() {
        // Your code here
    }

    public static String getUIUrl() {
        // Your code here
    }

    public static String getBrowser() {
        // Support -Dbrowser=firefox override
        // Your code here
    }

    public static int getImplicitWait() {
        // Your code here
    }

    public static boolean isHeadless() {
        // Support -Dheadless=true override
        // Your code here
    }
}
```

**Test Your Code:**
```java
// Test class
public class ConfigReaderTest {
    public static void main(String[] args) {
        System.out.println("Base URL: " + ConfigReader.getBaseUrl());
        System.out.println("Browser: " + ConfigReader.getBrowser());
        System.out.println("Headless: " + ConfigReader.isHeadless());
        System.out.println("Implicit Wait: " + ConfigReader.getImplicitWait());
    }
}

// Run with system property override
// java -Dbrowser=firefox -Dheadless=true ConfigReaderTest
```

**Expected Output:**
```
Base URL: https://jsonplaceholder.typicode.com
Browser: chrome
Headless: false
Implicit Wait: 10
```

**Hints:**
- Use `getClass().getClassLoader().getResourceAsStream()`
- System.getProperty() returns null if not set, use it with fallback

---

### Exercise 2: Implement Singleton DriverManager

**Objective:** Create a thread-safe DriverManager using Singleton pattern

**Task:**
Build a DriverManager that:
1. Uses ThreadLocal for thread safety
2. Creates driver only once per thread
3. Supports Chrome, Firefox, Edge
4. Configures timeouts and options
5. Provides clean quit method

**Starter Code:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.firefox.FirefoxOptions;
import org.openqa.selenium.edge.EdgeDriver;
import io.github.bonigarcia.wdm.WebDriverManager;
import config.ConfigReader;
import java.time.Duration;

public class DriverManager {
    // TODO: Create ThreadLocal variable for WebDriver

    // TODO: Private constructor (Singleton pattern)

    // TODO: Get driver method (creates if null)
    public static WebDriver getDriver() {
        // Your code here
    }

    // TODO: Create driver based on browser config
    private static WebDriver createDriver() {
        String browser = ConfigReader.getBrowser();
        WebDriver driver;

        switch (browser.toLowerCase()) {
            case "chrome":
                // Setup ChromeDriver
                // Add options (headless, maximize, etc.)
                break;

            case "firefox":
                // Setup FirefoxDriver
                // Add options
                break;

            case "edge":
                // Setup EdgeDriver
                break;

            default:
                throw new IllegalArgumentException("Unsupported browser: " + browser);
        }

        // Configure timeouts
        // Return driver
    }

    // TODO: Quit driver and remove from ThreadLocal
    public static void quitDriver() {
        // Your code here
    }
}
```

**Test Your Code:**
```java
public class DriverManagerTest {
    public static void main(String[] args) {
        WebDriver driver = DriverManager.getDriver();
        driver.get("https://www.google.com");
        System.out.println("Title: " + driver.getTitle());
        DriverManager.quitDriver();
    }
}
```

**Hints:**
- ThreadLocal syntax: `private static ThreadLocal<WebDriver> driver = new ThreadLocal<>()`
- WebDriverManager.chromedriver().setup() handles driver download
- Always remove from ThreadLocal after quit to prevent memory leaks

---

### Exercise 3: Create Test Data Factory

**Objective:** Build a Factory class that generates test data

**Task:**
Create a TestDataFactory that:
1. Uses JavaFaker to generate realistic data
2. Provides methods for different user types
3. Returns POJO objects
4. Supports random and fixed data

**Starter Code:**
```java
// pom.xml - add dependency
<dependency>
    <groupId>com.github.javafaker</groupId>
    <artifactId>javafaker</artifactId>
    <version>1.0.2</version>
</dependency>

// User.java
package models;

public class User {
    private String firstName;
    private String lastName;
    private String email;
    private String password;

    public User(String firstName, String lastName, String email, String password) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.password = password;
    }

    // Getters
    public String getFirstName() { return firstName; }
    public String getLastName() { return lastName; }
    public String getEmail() { return email; }
    public String getPassword() { return password; }

    public String getFullName() {
        return firstName + " " + lastName;
    }
}

// TestDataFactory.java
package utils;

import com.github.javafaker.Faker;
import models.User;

public class TestDataFactory {
    private static Faker faker = new Faker();

    // TODO: Generate random user
    public static User getRandomUser() {
        // Generate random first name, last name
        // Email format: firstname.lastname###@test.com
        // Password format: Test@####
        // Your code here
    }

    // TODO: Get admin user (fixed credentials)
    public static User getAdminUser() {
        // Return fixed admin user
        // Your code here
    }

    // TODO: Get user with specific email domain
    public static User getUserWithDomain(String domain) {
        // Generate user with email @domain
        // Your code here
    }
}
```

**Test Your Code:**
```java
public class TestDataFactoryTest {
    public static void main(String[] args) {
        User randomUser = TestDataFactory.getRandomUser();
        System.out.println("Random User: " + randomUser.getFullName());
        System.out.println("Email: " + randomUser.getEmail());

        User adminUser = TestDataFactory.getAdminUser();
        System.out.println("Admin: " + adminUser.getEmail());

        User companyUser = TestDataFactory.getUserWithDomain("company.com");
        System.out.println("Company User: " + companyUser.getEmail());
    }
}
```

**Expected Output:**
```
Random User: John Smith
Email: john.smith342@test.com
Admin: admin@example.com
Company User: jane.doe789@company.com
```

**Hints:**
- faker.name().firstName(), faker.name().lastName()
- faker.number().digits(3) for random numbers
- toLowerCase() to ensure email is lowercase

---

## 💪 Core Practice (35 min)

### Exercise 4: Build API Request Builder

**Objective:** Implement Builder pattern for RestAssured requests

**Task:**
Create an APIRequestBuilder with fluent API for building requests

**Problem Statement:**
RestAssured requests can get verbose with multiple headers, query params, auth, etc. Build a fluent builder to simplify request creation.

**Requirements:**
- Support baseUri, headers, query params, path params
- Support body, auth (Bearer token, Basic auth)
- Support logging
- Terminal methods: get, post, put, delete

**Starter Code:**
```java
package api;

import io.restassured.response.Response;
import io.restassured.specification.RequestSpecification;
import io.restassured.http.ContentType;
import java.util.Map;
import static io.restassured.RestAssured.given;

public class APIRequestBuilder {
    private RequestSpecification request;

    // TODO: Private constructor
    private APIRequestBuilder() {
        // Initialize request
    }

    // TODO: Static factory method
    public static APIRequestBuilder builder() {
        // Your code here
    }

    // TODO: Builder methods (return this for chaining)
    public APIRequestBuilder baseUri(String uri) {
        // Your code here
    }

    public APIRequestBuilder header(String key, String value) {
        // Your code here
    }

    public APIRequestBuilder headers(Map<String, String> headers) {
        // Your code here
    }

    public APIRequestBuilder queryParam(String key, String value) {
        // Your code here
    }

    public APIRequestBuilder pathParam(String key, String value) {
        // Your code here
    }

    public APIRequestBuilder body(Object body) {
        // Set content type to JSON
        // Add body
        // Your code here
    }

    public APIRequestBuilder auth(String token) {
        // Add Bearer token authorization
        // Your code here
    }

    public APIRequestBuilder basicAuth(String username, String password) {
        // Your code here
    }

    public APIRequestBuilder log() {
        // Your code here
    }

    // TODO: Terminal methods
    public Response get(String endpoint) {
        // Your code here
    }

    public Response post(String endpoint) {
        // Your code here
    }

    public Response put(String endpoint) {
        // Your code here
    }

    public Response delete(String endpoint) {
        // Your code here
    }
}
```

**Test Cases:**
```java
// Test 1: Simple GET
Response response = APIRequestBuilder.builder()
    .baseUri("https://jsonplaceholder.typicode.com")
    .get("/users/1");
System.out.println("Status: " + response.statusCode());

// Test 2: POST with body
Map<String, String> user = new HashMap<>();
user.put("name", "John Doe");
user.put("email", "john@example.com");

Response response = APIRequestBuilder.builder()
    .baseUri("https://jsonplaceholder.typicode.com")
    .body(user)
    .log()
    .post("/users");

// Test 3: GET with query params
Response response = APIRequestBuilder.builder()
    .baseUri("https://jsonplaceholder.typicode.com")
    .queryParam("userId", "1")
    .get("/posts");

// Test 4: DELETE with auth
Response response = APIRequestBuilder.builder()
    .baseUri("https://jsonplaceholder.typicode.com")
    .auth("fake-token-123")
    .delete("/users/1");
```

**Hints:**
- Each builder method should return `this` to enable chaining
- `request.contentType(ContentType.JSON)` sets JSON content type
- `request.header("Authorization", "Bearer " + token)` for auth

---

### Exercise 5: Setup ExtentReports Integration

**Objective:** Integrate ExtentReports with TestNG

**Task:**
Create ExtentManager and TestNG listener for reporting

**Requirements:**
- Singleton ExtentReports instance
- ThreadLocal for parallel execution safety
- TestNG listener to track test results
- Screenshot attachment on failure

**Starter Code:**
```java
// pom.xml dependency
<dependency>
    <groupId>com.aventstack</groupId>
    <artifactId>extentreports</artifactId>
    <version>5.1.1</version>
</dependency>

// ExtentManager.java
package reporting;

import com.aventstack.extentreports.ExtentReports;
import com.aventstack.extentreports.ExtentTest;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;
import com.aventstack.extentreports.reporter.configuration.Theme;

public class ExtentManager {
    private static ExtentReports extent;
    private static ThreadLocal<ExtentTest> test = new ThreadLocal<>();

    // TODO: Create ExtentReports instance
    public static ExtentReports createInstance(String fileName) {
        // Create SparkReporter
        // Configure theme, title, report name
        // Attach reporter to ExtentReports
        // Set system info
        // Your code here
    }

    // TODO: Get extent instance (create if null)
    public static ExtentReports getExtent() {
        // Your code here
    }

    // TODO: Set current test
    public static void setTest(ExtentTest extentTest) {
        // Your code here
    }

    // TODO: Get current test
    public static ExtentTest getTest() {
        // Your code here
    }
}

// ExtentReportListener.java
package listeners;

import com.aventstack.extentreports.Status;
import org.testng.*;
import reporting.ExtentManager;

public class ExtentReportListener implements ITestListener {

    @Override
    public void onStart(ITestContext context) {
        // Initialize ExtentReports
    }

    @Override
    public void onTestStart(ITestResult result) {
        // Create test in ExtentReports
        // Set in ThreadLocal
    }

    @Override
    public void onTestSuccess(ITestResult result) {
        // Log success
    }

    @Override
    public void onTestFailure(ITestResult result) {
        // Log failure
        // Log exception
        // TODO BONUS: Attach screenshot if UI test
    }

    @Override
    public void onTestSkipped(ITestResult result) {
        // Log skipped
    }

    @Override
    public void onFinish(ITestContext context) {
        // Flush report
    }
}
```

**Test Your Code:**
```java
// SampleTest.java
import org.testng.annotations.Test;

public class SampleTest {

    @Test
    public void passTest() {
        System.out.println("This test passes");
    }

    @Test
    public void failTest() {
        System.out.println("This test fails");
        throw new RuntimeException("Intentional failure");
    }

    @Test(enabled = false)
    public void skipTest() {
        System.out.println("This test is skipped");
    }
}

// testng.xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Test Suite">
    <listeners>
        <listener class-name="listeners.ExtentReportListener"/>
    </listeners>
    <test name="Sample Tests">
        <classes>
            <class name="SampleTest"/>
        </classes>
    </test>
</suite>
```

**Run and Verify:**
```bash
mvn test
# Check test-output/ExtentReport.html
```

**Hints:**
- ExtentSparkReporter takes file path: "test-output/ExtentReport.html"
- extent.createTest(testName) creates a new test
- extent.flush() writes the report to disk

---

### Exercise 6: Docker Selenium Setup

**Objective:** Configure framework to run on Docker Selenium

**Task:**
1. Create docker-compose.yml for Selenium Grid
2. Update DriverManager to support RemoteWebDriver
3. Run tests on Docker Selenium

**Requirements:**
- Docker Compose with Hub + Chrome node
- Config property to toggle local vs Docker execution
- RemoteWebDriver configuration

**Starter Code:**
```yaml
# docker-compose.yml
version: '3'
services:
  # TODO: Define selenium-hub service
  selenium-hub:
    image: # Your code here
    ports:
      # Your code here
    environment:
      # Your code here

  # TODO: Define chrome-node service
  chrome-node:
    image: # Your code here
    depends_on:
      # Your code here
    environment:
      # Your code here
    shm_size: # Your code here
    ports:
      # Your code here (VNC port)
```

**Enhanced DriverManager:**
```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.chrome.ChromeOptions;
import config.ConfigReader;
import java.net.URL;

public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            // Check if Docker execution
            boolean useDocker = Boolean.parseBoolean(
                ConfigReader.get("use.docker", "false")
            );

            if (useDocker) {
                driver.set(createRemoteDriver());
            } else {
                driver.set(createLocalDriver());
            }
        }
        return driver.get();
    }

    private static WebDriver createLocalDriver() {
        // Your existing local driver code
    }

    // TODO: Create RemoteWebDriver for Docker
    private static WebDriver createRemoteDriver() {
        String hubUrl = ConfigReader.get("selenium.hub", "http://localhost:4444");
        String browser = ConfigReader.getBrowser();

        // Create browser options
        // Create capabilities
        // Create RemoteWebDriver with hub URL and capabilities
        // Your code here
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();
        }
    }
}
```

**config.properties additions:**
```properties
use.docker=false
selenium.hub=http://localhost:4444
```

**Test Steps:**
```bash
# 1. Start Docker Selenium
docker-compose up -d

# 2. Verify Grid is running
# Open browser: http://localhost:4444/ui

# 3. Run tests on Docker
mvn test -Duse.docker=true

# 4. Watch tests execute via VNC
# Open browser: http://localhost:7900
# Password: secret

# 5. Stop Docker Selenium
docker-compose down
```

**Hints:**
- RemoteWebDriver constructor: `new RemoteWebDriver(new URL(hubUrl), capabilities)`
- ChromeOptions for remote: same as local ChromeOptions
- Docker hub URL: http://localhost:4444 (or http://localhost:4444/wd/hub for older versions)

---

## 🚀 Challenge Exercise (20 min)

### Exercise 7: Build Complete Hybrid Test

**Objective:** Create an end-to-end test combining API and UI

**Requirements:**
1. Use APIRequestBuilder to create a user via API
2. Use DriverManager to launch browser
3. Verify user appears in UI
4. Use TestDataFactory for test data
5. Generate ExtentReport

**Starter Code:**
```java
package tests;

import org.testng.annotations.*;
import org.openqa.selenium.WebDriver;
import io.restassured.response.Response;
import utils.DriverManager;
import utils.TestDataFactory;
import api.APIRequestBuilder;
import models.User;
import config.ConfigReader;
import static org.testng.Assert.*;

public class HybridUserTest {

    private WebDriver driver;
    private String userId;

    @BeforeMethod
    public void setup() {
        // TODO: Initialize driver
        // TODO: Set RestAssured baseURI
    }

    @Test
    public void testCreateUserEndToEnd() {
        // Step 1: Generate test data
        // TODO: Get random user from TestDataFactory

        // Step 2: Create user via API
        // TODO: Use APIRequestBuilder to POST /users
        // TODO: Validate status code 201
        // TODO: Extract user ID from response

        // Step 3: Verify via UI (simulated - use JSONPlaceholder API)
        // TODO: Navigate to user page
        // TODO: Verify user data is displayed

        // Step 4: Verify via API GET
        // TODO: Use APIRequestBuilder to GET /users/{id}
        // TODO: Validate response contains correct user data
    }

    @AfterMethod
    public void teardown() {
        // TODO: Quit driver
    }

    @AfterClass
    public void cleanup() {
        // TODO: Delete test user via API (if needed)
    }
}
```

**Complete Implementation:**
Write the full hybrid test that:
- Creates user via API (JSONPlaceholder)
- Extracts user ID
- Navigates to user page in browser
- Verifies data matches
- Cleans up

**Bonus Challenges:**
- [ ] Add Allure @Step annotations
- [ ] Add screenshot on failure
- [ ] Run on 3 browsers in parallel
- [ ] Run on Docker Selenium
- [ ] Add retry logic for flaky steps

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution: Configuration Reader

```java
package config;

import java.io.IOException;
import java.io.InputStream;
import java.util.Properties;

public class ConfigReader {
    private static Properties properties;
    private static final String CONFIG_FILE = "config.properties";

    // Static block - loads when class is first used
    static {
        try {
            properties = new Properties();
            InputStream input = ConfigReader.class
                .getClassLoader()
                .getResourceAsStream(CONFIG_FILE);

            if (input == null) {
                throw new RuntimeException("Unable to find " + CONFIG_FILE);
            }

            properties.load(input);
        } catch (IOException e) {
            throw new RuntimeException("Failed to load config file: " + CONFIG_FILE, e);
        }
    }

    // Get value with no default
    public static String get(String key) {
        return properties.getProperty(key);
    }

    // Get value with default fallback
    public static String get(String key, String defaultValue) {
        return properties.getProperty(key, defaultValue);
    }

    // Convenience methods
    public static String getBaseUrl() {
        return get("base.url");
    }

    public static String getUIUrl() {
        return get("ui.url");
    }

    public static String getBrowser() {
        // System property overrides config file
        return System.getProperty("browser", get("browser", "chrome"));
    }

    public static int getImplicitWait() {
        return Integer.parseInt(get("timeout.implicit", "10"));
    }

    public static boolean isHeadless() {
        return Boolean.parseBoolean(
            System.getProperty("headless", get("headless", "false"))
        );
    }
}
```

**Explanation:**
- **Static block**: Runs once when class is loaded, ensures properties are ready
- **getClassLoader().getResourceAsStream()**: Finds file in src/test/resources
- **System.getProperty() with fallback**: Allows runtime override via -D flags
- **Error handling**: Throws meaningful exceptions if file not found

**Key Takeaways:**
- Properties file provides default values
- System properties allow runtime overrides
- Singleton-like behavior through static members
- All config access centralized in one class

---

### Exercise 2 Solution: Singleton DriverManager

```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.firefox.FirefoxDriver;
import org.openqa.selenium.firefox.FirefoxOptions;
import org.openqa.selenium.edge.EdgeDriver;
import org.openqa.selenium.edge.EdgeOptions;
import io.github.bonigarcia.wdm.WebDriverManager;
import config.ConfigReader;
import java.time.Duration;

public class DriverManager {
    // ThreadLocal ensures each thread gets its own driver
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    // Private constructor prevents external instantiation (Singleton)
    private DriverManager() {}

    // Get driver (creates if doesn't exist)
    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(createDriver());
        }
        return driver.get();
    }

    // Create driver based on browser config
    private static WebDriver createDriver() {
        String browser = ConfigReader.getBrowser();
        WebDriver webDriver;
        boolean headless = ConfigReader.isHeadless();

        switch (browser.toLowerCase()) {
            case "chrome":
                WebDriverManager.chromedriver().setup();
                ChromeOptions chromeOptions = new ChromeOptions();
                if (headless) {
                    chromeOptions.addArguments("--headless=new");
                }
                chromeOptions.addArguments("--start-maximized");
                chromeOptions.addArguments("--disable-notifications");
                chromeOptions.addArguments("--disable-gpu");
                chromeOptions.addArguments("--no-sandbox");
                webDriver = new ChromeDriver(chromeOptions);
                break;

            case "firefox":
                WebDriverManager.firefoxdriver().setup();
                FirefoxOptions firefoxOptions = new FirefoxOptions();
                if (headless) {
                    firefoxOptions.addArguments("--headless");
                }
                firefoxOptions.addPreference("dom.webnotifications.enabled", false);
                webDriver = new FirefoxDriver(firefoxOptions);
                break;

            case "edge":
                WebDriverManager.edgedriver().setup();
                EdgeOptions edgeOptions = new EdgeOptions();
                if (headless) {
                    edgeOptions.addArguments("--headless");
                }
                edgeOptions.addArguments("--start-maximized");
                webDriver = new EdgeDriver(edgeOptions);
                break;

            default:
                throw new IllegalArgumentException("Unsupported browser: " + browser);
        }

        // Configure timeouts
        webDriver.manage().timeouts()
            .implicitlyWait(Duration.ofSeconds(ConfigReader.getImplicitWait()));
        webDriver.manage().timeouts()
            .pageLoadTimeout(Duration.ofSeconds(30));

        return webDriver;
    }

    // Quit driver and remove from ThreadLocal
    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();  // Important: prevents memory leaks
        }
    }
}
```

**Explanation:**
- **ThreadLocal**: Each thread (parallel test) gets its own driver instance
- **Singleton pattern**: Private constructor + static methods
- **WebDriverManager**: Auto-downloads and configures drivers
- **Switch statement**: Supports multiple browsers
- **Options**: Headless mode, maximize window, disable notifications
- **driver.remove()**: Cleans up ThreadLocal to prevent memory leaks

**Key Takeaways:**
- ThreadLocal is essential for parallel execution
- Centralized driver creation makes changes easy
- WebDriverManager eliminates manual driver management
- Always remove from ThreadLocal after quit

---

### Exercise 3 Solution: Test Data Factory

```java
package utils;

import com.github.javafaker.Faker;
import models.User;

public class TestDataFactory {
    private static Faker faker = new Faker();

    // Generate random user
    public static User getRandomUser() {
        String firstName = faker.name().firstName();
        String lastName = faker.name().lastName();
        String email = firstName.toLowerCase() + "." + lastName.toLowerCase()
                      + faker.number().digits(3) + "@test.com";
        String password = "Test@" + faker.number().digits(4);

        return new User(firstName, lastName, email, password);
    }

    // Get admin user (fixed credentials)
    public static User getAdminUser() {
        return new User(
            "Admin",
            "User",
            "admin@example.com",
            "Admin@123"
        );
    }

    // Get user with specific email domain
    public static User getUserWithDomain(String domain) {
        String firstName = faker.name().firstName();
        String lastName = faker.name().lastName();
        String email = firstName.toLowerCase() + "." + lastName.toLowerCase()
                      + faker.number().digits(3) + "@" + domain;
        String password = "Test@" + faker.number().digits(4);

        return new User(firstName, lastName, email, password);
    }

    // Generate user with specific role
    public static User getUserByRole(String role) {
        switch (role.toLowerCase()) {
            case "admin":
                return getAdminUser();
            case "manager":
                return new User("Manager", "User", "manager@example.com", "Manager@123");
            default:
                return getRandomUser();
        }
    }
}
```

**Explanation:**
- **Faker library**: Generates realistic names, emails, etc.
- **Factory pattern**: Centralized object creation
- **Different methods**: Random vs fixed data
- **Parameterized methods**: Flexible data generation

**Key Takeaways:**
- Factory pattern centralizes test data creation
- Faker generates realistic, varied data
- Same factory used across UI and API tests
- Easy to maintain and extend

---

### Exercise 4 Solution: API Request Builder

```java
package api;

import io.restassured.response.Response;
import io.restassured.specification.RequestSpecification;
import io.restassured.http.ContentType;
import java.util.Map;
import static io.restassured.RestAssured.given;

public class APIRequestBuilder {
    private RequestSpecification request;

    // Private constructor
    private APIRequestBuilder() {
        request = given();
    }

    // Static factory method
    public static APIRequestBuilder builder() {
        return new APIRequestBuilder();
    }

    // Builder methods (return this for chaining)
    public APIRequestBuilder baseUri(String uri) {
        request.baseUri(uri);
        return this;
    }

    public APIRequestBuilder header(String key, String value) {
        request.header(key, value);
        return this;
    }

    public APIRequestBuilder headers(Map<String, String> headers) {
        request.headers(headers);
        return this;
    }

    public APIRequestBuilder queryParam(String key, String value) {
        request.queryParam(key, value);
        return this;
    }

    public APIRequestBuilder pathParam(String key, String value) {
        request.pathParam(key, value);
        return this;
    }

    public APIRequestBuilder body(Object body) {
        request.contentType(ContentType.JSON);
        request.body(body);
        return this;
    }

    public APIRequestBuilder auth(String token) {
        request.header("Authorization", "Bearer " + token);
        return this;
    }

    public APIRequestBuilder basicAuth(String username, String password) {
        request.auth().basic(username, password);
        return this;
    }

    public APIRequestBuilder log() {
        request.log().all();
        return this;
    }

    // Terminal methods
    public Response get(String endpoint) {
        return request.get(endpoint);
    }

    public Response post(String endpoint) {
        return request.post(endpoint);
    }

    public Response put(String endpoint) {
        return request.put(endpoint);
    }

    public Response delete(String endpoint) {
        return request.delete(endpoint);
    }

    public Response patch(String endpoint) {
        return request.patch(endpoint);
    }
}
```

**Explanation:**
- **Builder pattern**: Fluent API with method chaining
- **Private constructor**: Forces use of builder() method
- **Return this**: Enables chaining (builder().header().body().post())
- **Terminal methods**: Actually execute the request

**Key Takeaways:**
- Builder pattern creates readable, flexible code
- Each method returns `this` for chaining
- Separates configuration (builder methods) from execution (terminal methods)
- Reusable across all API tests

---

### Exercise 5 Solution: ExtentReports Integration

```java
// ExtentManager.java
package reporting;

import com.aventstack.extentreports.ExtentReports;
import com.aventstack.extentreports.ExtentTest;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;
import com.aventstack.extentreports.reporter.configuration.Theme;

public class ExtentManager {
    private static ExtentReports extent;
    private static ThreadLocal<ExtentTest> test = new ThreadLocal<>();

    public static ExtentReports createInstance(String fileName) {
        ExtentSparkReporter sparkReporter = new ExtentSparkReporter(fileName);

        // Configure report
        sparkReporter.config().setTheme(Theme.DARK);
        sparkReporter.config().setDocumentTitle("Automation Test Report");
        sparkReporter.config().setReportName("Hybrid Framework Test Results");
        sparkReporter.config().setTimeStampFormat("MMM dd, yyyy HH:mm:ss");

        extent = new ExtentReports();
        extent.attachReporter(sparkReporter);

        // System info
        extent.setSystemInfo("OS", System.getProperty("os.name"));
        extent.setSystemInfo("Java Version", System.getProperty("java.version"));
        extent.setSystemInfo("Browser", "Chrome");

        return extent;
    }

    public static ExtentReports getExtent() {
        if (extent == null) {
            createInstance("test-output/ExtentReport.html");
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

// ExtentReportListener.java
package listeners;

import com.aventstack.extentreports.Status;
import org.testng.*;
import reporting.ExtentManager;

public class ExtentReportListener implements ITestListener {

    @Override
    public void onStart(ITestContext context) {
        ExtentManager.getExtent();
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
            "Test Passed: " + result.getMethod().getMethodName());
    }

    @Override
    public void onTestFailure(ITestResult result) {
        ExtentManager.getTest().log(Status.FAIL,
            "Test Failed: " + result.getMethod().getMethodName());
        ExtentManager.getTest().log(Status.FAIL, result.getThrowable());
    }

    @Override
    public void onTestSkipped(ITestResult result) {
        ExtentManager.getTest().log(Status.SKIP,
            "Test Skipped: " + result.getMethod().getMethodName());
    }

    @Override
    public void onFinish(ITestContext context) {
        ExtentManager.getExtent().flush();
    }
}
```

**Explanation:**
- **ExtentManager**: Singleton for ExtentReports instance
- **ThreadLocal**: Thread-safe test instance for parallel execution
- **Listener**: Hooks into TestNG lifecycle events
- **flush()**: Writes report to disk

**Key Takeaways:**
- Listener pattern integrates reporting with TestNG
- ThreadLocal enables parallel test reporting
- ExtentReports provides visual HTML reports
- No changes needed in test code

---

### Exercise 6 Solution: Docker Selenium Setup

```yaml
# docker-compose.yml
version: '3'
services:
  selenium-hub:
    image: selenium/hub:latest
    ports:
      - "4444:4444"
      - "4442:4442"
      - "4443:4443"
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
    shm_size: '2gb'
    ports:
      - "7900:7900"
```

```java
// Enhanced DriverManager with Docker support
private static WebDriver createRemoteDriver() {
    String hubUrl = ConfigReader.get("selenium.hub", "http://localhost:4444");
    String browser = ConfigReader.getBrowser();

    DesiredCapabilities capabilities = new DesiredCapabilities();

    switch (browser.toLowerCase()) {
        case "chrome":
            ChromeOptions chromeOptions = new ChromeOptions();
            chromeOptions.addArguments("--start-maximized");
            chromeOptions.addArguments("--disable-notifications");
            capabilities.setCapability(ChromeOptions.CAPABILITY, chromeOptions);
            break;

        case "firefox":
            FirefoxOptions firefoxOptions = new FirefoxOptions();
            capabilities.setCapability(FirefoxOptions.FIREFOX_OPTIONS, firefoxOptions);
            break;

        default:
            throw new IllegalArgumentException("Unsupported browser: " + browser);
    }

    try {
        RemoteWebDriver driver = new RemoteWebDriver(
            new URL(hubUrl),
            capabilities
        );
        driver.manage().timeouts()
            .implicitlyWait(Duration.ofSeconds(ConfigReader.getImplicitWait()));
        return driver;
    } catch (MalformedURLException e) {
        throw new RuntimeException("Invalid Selenium Hub URL: " + hubUrl, e);
    }
}
```

**Explanation:**
- **Docker Compose**: Defines Hub and Chrome node
- **RemoteWebDriver**: Connects to Selenium Grid
- **Configuration toggle**: Switch between local and Docker
- **VNC port 7900**: Watch tests execute in real-time

**Key Takeaways:**
- Docker provides consistent browser environments
- Grid enables parallel cross-browser testing
- Same test code works locally and on Docker
- Perfect for CI/CD pipelines

---

### Exercise 7 Solution: Complete Hybrid Test

```java
package tests;

import org.testng.annotations.*;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.By;
import io.restassured.response.Response;
import utils.DriverManager;
import utils.TestDataFactory;
import api.APIRequestBuilder;
import models.User;
import config.ConfigReader;
import static org.testng.Assert.*;
import static io.restassured.RestAssured.given;

public class HybridUserTest {

    private WebDriver driver;
    private String userId;

    @BeforeMethod
    public void setup() {
        driver = DriverManager.getDriver();
    }

    @Test
    public void testCreateUserEndToEnd() {
        // Step 1: Generate test data
        User user = TestDataFactory.getRandomUser();
        System.out.println("Creating user: " + user.getFullName());

        // Step 2: Create user via API
        Response createResponse = APIRequestBuilder.builder()
            .baseUri(ConfigReader.getBaseUrl())
            .body(user)
            .log()
            .post("/users");

        assertEquals(createResponse.statusCode(), 201, "User creation failed");
        userId = createResponse.jsonPath().getString("id");
        System.out.println("User created with ID: " + userId);

        // Step 3: Verify via UI
        driver.get(ConfigReader.getBaseUrl() + "/users/" + userId);
        String pageSource = driver.getPageSource();
        assertTrue(pageSource.contains(user.getFirstName()),
            "User first name not found in UI");

        // Step 4: Verify via API GET
        Response getResponse = APIRequestBuilder.builder()
            .baseUri(ConfigReader.getBaseUrl())
            .get("/users/" + userId);

        assertEquals(getResponse.statusCode(), 200);
        assertEquals(getResponse.jsonPath().getString("id"), userId);

        System.out.println("Hybrid test completed successfully!");
    }

    @AfterMethod
    public void teardown() {
        DriverManager.quitDriver();
    }
}
```

**Explanation:**
- **API data setup**: Fast user creation via POST
- **UI verification**: Navigate to user page, check content
- **API validation**: Verify user exists via GET
- **Hybrid approach**: Combines speed of API with UI validation

**Key Takeaways:**
- API tests are faster for data setup
- UI tests verify actual user experience
- Hybrid approach catches integration bugs
- Same framework supports both UI and API

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - ConfigReader | ☐ | __/10 | ___ min | |
| 2 - DriverManager | ☐ | __/10 | ___ min | |
| 3 - TestDataFactory | ☐ | __/10 | ___ min | |
| 4 - APIRequestBuilder | ☐ | __/10 | ___ min | |
| 5 - ExtentReports | ☐ | __/10 | ___ min | |
| 6 - Docker Selenium | ☐ | __/10 | ___ min | |
| 7 - Hybrid Test | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**What I learned:**
-

**What I struggled with:**
-

**What I'll practice more:**
-

---

## ✅ Answers to Self-Check Questions (from Concept Guide)

1. **What is a hybrid framework and why is it valuable?**
   - A framework that combines UI (Selenium) and API (RestAssured) testing in one codebase. Valuable because it enables end-to-end testing, fast data setup via API, and full-stack validation.

2. **Explain the difference between Singleton, Factory, and Builder patterns.**
   - **Singleton**: Ensures one instance of a class (e.g., DriverManager with ThreadLocal)
   - **Factory**: Creates objects without specifying exact class (e.g., TestDataFactory generates different user types)
   - **Builder**: Constructs complex objects step-by-step with fluent API (e.g., APIRequestBuilder)

3. **How does ThreadLocal enable parallel execution?**
   - ThreadLocal gives each thread its own copy of a variable. Without it, parallel tests would share the same WebDriver instance and interfere with each other.

4. **What are the benefits of Docker Selenium over local browsers?**
   - Consistent browser versions, no installation needed, isolation, easy parallel execution, CI/CD ready

5. **Compare ExtentReports and Allure—which would you choose for a large project?**
   - **ExtentReports**: Real-time, simple setup, good for small-medium projects
   - **Allure**: Advanced features, trending, better for large enterprise projects
   - **For large project**: Allure for detailed analysis and historical trends

---

**🎉 Congratulations!** You've built all core components of an enterprise-grade hybrid framework!
