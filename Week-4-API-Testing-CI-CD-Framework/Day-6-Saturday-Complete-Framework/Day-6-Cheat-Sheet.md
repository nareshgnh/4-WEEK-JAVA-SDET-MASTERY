# DAY 6 CHEAT SHEET: COMPLETE FRAMEWORK BUILD

## ⚡ Quick Syntax Reference

### Configuration Reader Pattern
```java
// Static initialization
public class ConfigReader {
    private static Properties properties;

    static {
        InputStream input = ConfigReader.class
            .getClassLoader()
            .getResourceAsStream("config.properties");
        properties.load(input);
    }

    public static String get(String key) {
        return System.getProperty(key, properties.getProperty(key));
    }
}

// Usage
String url = ConfigReader.get("base.url");
mvn test -Dbase.url=https://custom.com  // Override
```

### Singleton Pattern (DriverManager)
```java
public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    private DriverManager() {}  // Private constructor

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(new ChromeDriver());
        }
        return driver.get();
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();  // Prevent memory leak
        }
    }
}
```

### Factory Pattern (Test Data)
```java
public class TestDataFactory {
    private static Faker faker = new Faker();

    public static User getRandomUser() {
        return new User(
            faker.name().firstName(),
            faker.name().lastName(),
            faker.internet().emailAddress()
        );
    }

    public static User getAdminUser() {
        return new User("Admin", "User", "admin@example.com");
    }
}

// Usage
User user = TestDataFactory.getRandomUser();
```

### Builder Pattern (API Requests)
```java
public class APIRequestBuilder {
    private RequestSpecification request;

    private APIRequestBuilder() {
        request = given();
    }

    public static APIRequestBuilder builder() {
        return new APIRequestBuilder();
    }

    public APIRequestBuilder header(String key, String value) {
        request.header(key, value);
        return this;  // Enable chaining
    }

    public Response get(String endpoint) {
        return request.get(endpoint);
    }
}

// Usage
Response response = APIRequestBuilder.builder()
    .header("Auth", "token")
    .body(user)
    .post("/users");
```

---

## 🔑 Key Concepts at a Glance

### Design Patterns Comparison

| Pattern | Purpose | Example | Key Feature |
|---------|---------|---------|-------------|
| **Singleton** | One instance per thread | DriverManager | ThreadLocal + private constructor |
| **Factory** | Create objects | TestDataFactory | Multiple creation methods |
| **Builder** | Build complex objects | APIRequestBuilder | Fluent API with chaining |
| **Page Object** | Encapsulate page | LoginPage extends BasePage | @FindBy + PageFactory |

### Configuration Hierarchy

```
Priority (High to Low):
1. System Properties    -Dbrowser=firefox
2. Config File          browser=chrome
3. Default Value        "chrome"

Code:
System.getProperty("browser", properties.getProperty("browser", "chrome"))
```

### Thread Safety

| Approach | Thread-Safe? | Use Case |
|----------|--------------|----------|
| `static WebDriver driver` | ❌ No | Single-threaded only |
| `ThreadLocal<WebDriver> driver` | ✅ Yes | Parallel execution |
| Instance variable | ✅ Yes | One instance per test |

---

## 🐳 Docker Commands Reference

### Basic Docker Selenium

```bash
# Run standalone Chrome
docker run -d -p 4444:4444 -p 7900:7900 --shm-size="2g" \
  selenium/standalone-chrome:latest

# Run standalone Firefox
docker run -d -p 4444:4444 -p 7900:7900 --shm-size="2g" \
  selenium/standalone-firefox:latest

# Access VNC viewer
open http://localhost:7900
# Password: secret

# Check Grid status
curl http://localhost:4444/status

# Stop container
docker stop <container_id>
docker rm <container_id>
```

### Docker Compose (Grid Setup)

```yaml
# docker-compose.yml
version: '3'
services:
  hub:
    image: selenium/hub:latest
    ports:
      - "4444:4444"
      - "4442:4442"
      - "4443:4443"

  chrome:
    image: selenium/node-chrome:latest
    depends_on:
      - hub
    environment:
      - SE_EVENT_BUS_HOST=hub
      - SE_EVENT_BUS_PUBLISH_PORT=4442
      - SE_EVENT_BUS_SUBSCRIBE_PORT=4443
    shm_size: '2gb'
    ports:
      - "7900:7900"
```

```bash
# Start Grid
docker-compose up -d

# View logs
docker-compose logs -f

# Scale Chrome nodes
docker-compose up -d --scale chrome=3

# Stop Grid
docker-compose down

# Remove volumes
docker-compose down -v
```

### RemoteWebDriver Configuration

```java
public static WebDriver createRemoteDriver() {
    String hubUrl = "http://localhost:4444";

    ChromeOptions options = new ChromeOptions();
    options.addArguments("--start-maximized");

    DesiredCapabilities capabilities = new DesiredCapabilities();
    capabilities.setCapability(ChromeOptions.CAPABILITY, options);

    return new RemoteWebDriver(new URL(hubUrl), capabilities);
}
```

---

## 📁 Framework Structure Template

```
hybrid-framework/
├── pom.xml                      # Maven dependencies
├── docker-compose.yml           # Selenium Grid
├── testng.xml                   # Test execution
├── src/
│   ├── main/java/
│   │   ├── config/
│   │   │   └── ConfigReader.java       # Singleton
│   │   ├── utils/
│   │   │   ├── DriverManager.java      # Singleton + ThreadLocal
│   │   │   └── TestDataFactory.java    # Factory
│   │   ├── api/
│   │   │   ├── builders/
│   │   │   │   └── APIRequestBuilder.java  # Builder
│   │   │   ├── models/
│   │   │   │   └── User.java           # POJO
│   │   │   └── UserAPI.java            # API layer
│   │   └── pages/
│   │       ├── BasePage.java           # Page Object base
│   │       └── LoginPage.java          # Page Object
│   └── test/
│       ├── java/
│       │   ├── base/
│       │   │   └── BaseTest.java       # Test base
│       │   ├── ui/
│       │   │   └── LoginTests.java
│       │   ├── api/
│       │   │   └── UserAPITests.java
│       │   └── e2e/
│       │       └── HybridTest.java
│       └── resources/
│           └── config.properties       # Configuration
```

---

## 🎯 Essential Code Snippets

### Complete ConfigReader

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
            throw new RuntimeException("Failed to load config", e);
        }
    }

    public static String get(String key) {
        return properties.getProperty(key);
    }

    public static String get(String key, String defaultValue) {
        return properties.getProperty(key, defaultValue);
    }

    // System property override
    public static String getBrowser() {
        return System.getProperty("browser", get("browser", "chrome"));
    }

    public static boolean isHeadless() {
        return Boolean.parseBoolean(
            System.getProperty("headless", get("headless", "false"))
        );
    }
}
```

### Complete DriverManager

```java
package utils;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.remote.RemoteWebDriver;
import io.github.bonigarcia.wdm.WebDriverManager;
import config.ConfigReader;
import java.time.Duration;

public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    private DriverManager() {}

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(ConfigReader.useDocker() ?
                createRemoteDriver() : createLocalDriver());
        }
        return driver.get();
    }

    private static WebDriver createLocalDriver() {
        WebDriverManager.chromedriver().setup();
        ChromeOptions options = new ChromeOptions();
        if (ConfigReader.isHeadless()) {
            options.addArguments("--headless=new");
        }
        WebDriver driver = new ChromeDriver(options);
        configureDriver(driver);
        return driver;
    }

    private static WebDriver createRemoteDriver() {
        String hubUrl = ConfigReader.getSeleniumHub();
        ChromeOptions options = new ChromeOptions();
        WebDriver driver = new RemoteWebDriver(new URL(hubUrl), options);
        configureDriver(driver);
        return driver;
    }

    private static void configureDriver(WebDriver driver) {
        driver.manage().timeouts()
            .implicitlyWait(Duration.ofSeconds(10));
        driver.manage().window().maximize();
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();
        }
    }
}
```

### Complete BaseTest

```java
package base;

import org.testng.annotations.*;
import org.openqa.selenium.WebDriver;
import utils.DriverManager;
import config.ConfigReader;
import io.restassured.RestAssured;

public class BaseTest {
    protected WebDriver driver;

    @BeforeSuite
    public void globalSetup() {
        ConfigReader.printConfig();
        RestAssured.baseURI = ConfigReader.getAPIBaseUrl();
    }

    @BeforeMethod
    public void setup() {
        driver = DriverManager.getDriver();
    }

    @AfterMethod(alwaysRun = true)
    public void teardown() {
        DriverManager.quitDriver();
    }
}
```

---

## 🧪 Test Execution Commands

### Maven Commands

```bash
# Run all tests
mvn clean test

# Run specific test class
mvn test -Dtest=LoginTests

# Run specific test method
mvn test -Dtest=LoginTests#testValidLogin

# Run with custom browser
mvn test -Dbrowser=firefox

# Run headless
mvn test -Dheadless=true

# Run on Docker
mvn test -Duse.docker=true

# Run with environment
mvn test -Denv=qa

# Run with suite file
mvn test -DsuiteXmlFile=testng-smoke.xml

# Parallel execution
mvn test -Dthreads=5

# Skip tests
mvn clean install -DskipTests

# Debug mode
mvn test -X
```

### TestNG XML Examples

**Basic Suite:**
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Test Suite">
    <test name="Tests">
        <classes>
            <class name="ui.LoginTests"/>
            <class name="api.UserAPITests"/>
        </classes>
    </test>
</suite>
```

**Parallel Suite:**
```xml
<suite name="Parallel Suite" parallel="methods" thread-count="5">
    <test name="Tests">
        <classes>
            <class name="ui.LoginTests"/>
        </classes>
    </test>
</suite>
```

**Cross-Browser Suite:**
```xml
<suite name="Cross-Browser" parallel="tests" thread-count="3">
    <test name="Chrome">
        <parameter name="browser" value="chrome"/>
        <classes>
            <class name="ui.LoginTests"/>
        </classes>
    </test>

    <test name="Firefox">
        <parameter name="browser" value="firefox"/>
        <classes>
            <class name="ui.LoginTests"/>
        </classes>
    </test>
</suite>
```

### Allure Report Commands

```bash
# Generate report
mvn allure:report

# Serve report (opens browser)
mvn allure:serve

# Clean results
mvn clean

# Custom results directory
mvn test -Dallure.results.directory=target/my-results
```

---

## 📊 Reporting: Allure Annotations

### Test Organization

```java
@Epic("User Management")        // High-level feature group
@Feature("Login")               // Feature within epic
@Story("User Login")            // User story
@Severity(SeverityLevel.BLOCKER) // Critical, Blocker, Normal, etc.
@Owner("QA Team")               // Test owner
@Link("https://jira.com/TICKET-123")  // External link
```

### Test Steps

```java
import io.qameta.allure.Step;

@Step("Login with username: {username}")
public void login(String username, String password) {
    // Implementation
}

@Step("Verify user is logged in")
public void verifyLogin() {
    // Verification
}
```

### Attachments

```java
import io.qameta.allure.Attachment;

@Attachment(value = "Screenshot", type = "image/png")
public byte[] saveScreenshot() {
    return ((TakesScreenshot) driver)
        .getScreenshotAs(OutputType.BYTES);
}

@Attachment(value = "Request", type = "application/json")
public String attachRequest(String json) {
    return json;
}
```

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Config file loading** | `new FileInputStream("config.properties")` | `getClassLoader().getResourceAsStream("config.properties")` |
| **Static driver** | `private static WebDriver driver;` | `private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();` |
| **Forget to remove** | `driver.quit();` | `driver.quit(); driver.remove();` |
| **Builder without return** | `public void header(...) {}` | `public Builder header(...) { return this; }` |
| **Missing flush** | ExtentReports created | `extent.flush();` in onFinish |
| **Docker URL** | `http://localhost:4444/wd/hub` | `http://localhost:4444` (Grid 4) |
| **Hardcoded values** | `driver.get("https://qa.com");` | `driver.get(ConfigReader.getURL());` |

---

## 💡 Pro Tips

### Tip 1: Configuration Validation
```java
@BeforeSuite
public void validateConfig() {
    assertNotNull(ConfigReader.getBaseUrl(), "Base URL is null");
    System.out.println("✅ Config validated");
}
```

### Tip 2: Health Check for Docker
```java
@BeforeSuite
public void checkSeleniumGrid() {
    if (ConfigReader.useDocker()) {
        Response r = given().get(ConfigReader.getSeleniumHub() + "/status");
        assertEquals(r.statusCode(), 200, "Grid not ready");
    }
}
```

### Tip 3: Unique Test Data
```java
// Avoid conflicts in parallel execution
String uniqueEmail = "user_" + System.currentTimeMillis() + "@test.com";
```

### Tip 4: Conditional Test Execution
```java
@BeforeMethod
public void checkEnvironment() {
    if (!"prod".equals(ConfigReader.getEnv())) {
        throw new SkipException("Skipping in non-prod");
    }
}
```

### Tip 5: Retry Flaky Tests
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

@Test(retryAnalyzer = RetryAnalyzer.class)
public void flakyTest() {
    // Test logic
}
```

---

## 🎤 Interview Phrases

**When asked about your framework, say:**

*"I built a hybrid automation framework that combines UI testing using Selenium and API testing using RestAssured. The framework implements design patterns like Singleton for driver management with ThreadLocal for parallel execution, Factory for test data generation, and Builder for fluent API request creation. I use configuration files for environment management, supporting dev, qa, and production with system property overrides. The framework supports cross-browser testing on Chrome, Firefox, and Edge, and can run on Docker Selenium Grid for consistent CI/CD execution. I integrated Allure for detailed reporting with step annotations and screenshot attachments. The entire framework is CI/CD ready with Maven, TestNG, and works seamlessly in Jenkins and GitHub Actions."*

**When asked about design patterns:**

*"I use Singleton pattern for DriverManager with ThreadLocal to ensure each test thread gets its own WebDriver instance, preventing conflicts in parallel execution. Factory pattern centralizes test data creation using JavaFaker for realistic data. Builder pattern creates fluent API requests with method chaining, making code readable and flexible. Page Object Model encapsulates UI elements and actions, separating test logic from page structure."*

**When asked about parallel execution:**

*"ThreadLocal is essential for parallel execution because it gives each thread its own WebDriver instance. Without it, threads would share a driver and interfere with each other. I always call remove() after quit() to prevent memory leaks, since thread pools reuse threads and ThreadLocal values persist for the thread's lifetime."*

**When asked about Docker:**

*"I use Docker Selenium for consistent browser environments across local development and CI/CD. The docker-compose setup includes a Hub and Chrome/Firefox nodes. Tests connect via RemoteWebDriver to the Hub URL. This ensures the same browser versions everywhere, eliminates driver installation, and scales easily for parallel execution. I can watch tests execute in real-time via VNC on port 7900."*

---

## 📌 Key Takeaways

**Today You Learned:**
- ✅ Hybrid framework architecture (UI + API in one framework)
- ✅ Configuration management (properties files, system overrides)
- ✅ Design patterns (Singleton, Factory, Builder)
- ✅ Thread-safe driver management (ThreadLocal)
- ✅ Cross-browser testing (Chrome, Firefox, Edge)
- ✅ Docker Selenium (Grid setup, RemoteWebDriver)
- ✅ Advanced reporting (Allure with steps and attachments)
- ✅ Production-ready framework structure

**Critical for Automation:**
- 🎯 **ThreadLocal is mandatory** for parallel execution
- 🎯 **Always call driver.remove()** to prevent memory leaks
- 🎯 **System properties override config** for flexibility
- 🎯 **Builder pattern returns this** for method chaining
- 🎯 **Docker Selenium ensures consistency** across environments

---

## 📋 Framework Checklist

**Before calling your framework production-ready:**

- [ ] Configuration management (properties + system overrides)
- [ ] Thread-safe driver management (ThreadLocal)
- [ ] Design patterns implemented (Singleton, Factory, Builder)
- [ ] Page Object Model for UI tests
- [ ] API layer with request/response models
- [ ] Base test class with setup/teardown
- [ ] Cross-browser support (Chrome, Firefox, Edge)
- [ ] Docker Selenium configuration
- [ ] Parallel execution support
- [ ] Allure reporting integration
- [ ] TestNG XML for different suites
- [ ] CI/CD ready (Maven, Jenkins/GitHub Actions)
- [ ] README with setup instructions
- [ ] Code follows Java naming conventions
- [ ] Proper exception handling
- [ ] Logging for debugging

---

## 🔮 Tomorrow's Preview

**Day 7 (Sunday): Documentation & Polish**
- Framework documentation
- Code cleanup and refactoring
- Adding utilities (screenshot, retry, custom assertions)
- Performance optimization
- Portfolio preparation
- Interview prep with framework walkthrough

**What to review tonight:**
- Your complete framework structure
- Design pattern implementations
- Configuration management approach
- How you would explain your framework in an interview

**What to think about:**
- What makes your framework different?
- What problems does it solve?
- How would you improve it further?
- How does it compare to pytest frameworks you've used?

---

## 🎯 Day 6 Challenge Completed!

**Today's Achievement:**
- [x] Built complete hybrid automation framework
- [x] Implemented 3 design patterns (Singleton, Factory, Builder)
- [x] Created configuration management layer
- [x] Integrated Docker Selenium
- [x] Set up Allure reporting
- [x] Configured parallel execution
- [x] Built UI, API, and E2E tests
- [x] ~3.5 hours of framework development

**Cumulative Progress:**
- Days completed: 6/7 (Week 4)
- Weeks completed: 4/4
- Framework components: 15+ classes created
- Design patterns mastered: 4 (Singleton, Factory, Builder, POM)
- Test types: UI, API, E2E hybrid
- CI/CD ready: ✅
- Interview ready: 95% 🚀

**Momentum Statement:**
*"You've built an enterprise-grade hybrid automation framework from scratch! This framework demonstrates ALL the skills product companies look for: design patterns, configuration management, parallel execution, Docker support, and advanced reporting. Combined with your Java, Selenium, TestNG, API, and CI/CD knowledge, you're now ready to walk into any SDET interview with confidence. One more day to polish and document your work!"*

---

## 🌟 Week 4 Progress

**Week 4 Progress:**
```
Day 1: API Testing Basics          ✅ Complete
Day 2: RestAssured Deep Dive       ✅ Complete
Day 3: Advanced API Testing        ✅ Complete
Day 4: Maven & Build Tools         ✅ Complete
Day 5: CI/CD Integration           ✅ Complete
Day 6: Complete Framework          ✅ Complete (You are here!)
Day 7: Documentation & Polish      ⏳ Tomorrow
```

**Overall Journey:**
```
Week 1: Java Fundamentals          ✅ 100%
Week 2: Selenium WebDriver         ✅ 100%
Week 3: TestNG & Frameworks        ✅ 100%
Week 4: API Testing & CI/CD        ⏳ 86% (6/7 days)
```

**You're 99% done with the entire 4-week program! 🎉**

---

## 📚 Quick Reference URLs

**Documentation:**
- Selenium: https://www.selenium.dev/documentation/
- RestAssured: https://rest-assured.io/
- TestNG: https://testng.org/doc/
- Allure: https://docs.qameta.io/allure/
- Maven: https://maven.apache.org/guides/

**Tools:**
- WebDriverManager: https://github.com/bonigarcia/webdrivermanager
- JavaFaker: https://github.com/DiUS/java-faker
- Docker Selenium: https://github.com/SeleniumHQ/docker-selenium
- Lombok: https://projectlombok.org/

**Learning:**
- Design Patterns: https://refactoring.guru/design-patterns
- ThreadLocal: https://docs.oracle.com/javase/8/docs/api/java/lang/ThreadLocal.html

---

## 💪 Final Push

**You've accomplished something huge today!** You built a framework that combines:
- UI automation (Selenium)
- API automation (RestAssured)
- Professional design patterns
- Configuration management
- Cross-browser testing
- Docker support
- Advanced reporting

**This is the framework you'll showcase in interviews.**

**Tomorrow, you'll add the final polish: documentation, cleanup, and interview preparation.**

**Keep going! You're one day away from completing the entire 4-week program! 🚀**

---
