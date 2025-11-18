# DAY 6: COMPLETE FRAMEWORK BUILD

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand hybrid framework architecture (UI + API combined)
- [ ] Master configuration management (properties, YAML, environment variables)
- [ ] Implement design patterns (Singleton, Factory, Builder)
- [ ] Set up cross-browser testing with WebDriverManager
- [ ] Configure Docker for Selenium execution
- [ ] Integrate advanced reporting (ExtentReports, Allure)
- [ ] Build enterprise-grade test framework structure

**Time Required:** 3-3.5 hours
**Difficulty:** Advanced
**Prerequisite:** Days 1-5 (RestAssured, Maven, TestNG, CI/CD)

---

## 📚 Core Concepts

### Concept 1: Hybrid Framework Architecture

**What is it?**
A hybrid framework combines UI automation (Selenium) and API automation (RestAssured) in a single, unified framework. This allows you to test the full stack—frontend and backend—while reusing common components like configuration, utilities, and reporting.

**Why does it matter for automation?**
Modern applications are built with separated frontend and backend (microservices). As an SDET:
- **End-to-end testing** requires both UI and API validation
- **Data setup via API** is faster than UI-based setup
- **Backend validation** catches bugs that UI alone misses
- **Product companies expect** full-stack automation skills
- **Interview advantage** when you can discuss complete framework architecture

**Architecture Diagram:**
```
hybrid-framework/
├── src/
│   ├── main/
│   │   └── java/
│   │       ├── config/             # Configuration management
│   │       │   ├── ConfigReader.java
│   │       │   └── EnvironmentConfig.java
│   │       ├── pages/              # Page Object Model (UI)
│   │       │   ├── LoginPage.java
│   │       │   └── DashboardPage.java
│   │       ├── api/                # API endpoints
│   │       │   ├── UserAPI.java
│   │       │   └── ProductAPI.java
│   │       ├── utils/              # Shared utilities
│   │       │   ├── DriverManager.java (Singleton)
│   │       │   ├── TestDataFactory.java (Factory)
│   │       │   └── APIRequestBuilder.java (Builder)
│   │       └── reporting/          # Reporting
│   │           └── ExtentManager.java
│   └── test/
│       ├── java/
│       │   ├── ui/                 # UI tests
│       │   │   └── LoginTest.java
│       │   ├── api/                # API tests
│       │   │   └── UserAPITest.java
│       │   └── e2e/                # Hybrid E2E tests
│       │       └── UserJourneyTest.java
│       └── resources/
│           ├── config.properties   # Environment configs
│           ├── testng.xml          # Test execution
│           └── allure.properties   # Reporting config
└── pom.xml
```

**Why This Structure?**
- **Separation of concerns**: UI, API, and utilities are separate
- **Reusability**: Common utilities shared across UI and API tests
- **Scalability**: Easy to add new pages, APIs, or tests
- **Maintainability**: Changes in one layer don't affect others

**Real Example:**
```java
// Hybrid E2E Test: Create user via API, verify in UI
@Test
public void testUserCreationEndToEnd() {
    // Step 1: Create user via API (fast data setup)
    User user = UserAPI.createUser(
        TestDataFactory.getRandomUser()
    );

    // Step 2: Login via UI
    LoginPage loginPage = new LoginPage(driver);
    loginPage.login(user.getEmail(), user.getPassword());

    // Step 3: Verify user appears in UI dashboard
    DashboardPage dashboard = new DashboardPage(driver);
    assertTrue(dashboard.isUserDisplayed(user.getName()));

    // Step 4: Verify user via API (backend validation)
    User fetchedUser = UserAPI.getUserById(user.getId());
    assertEquals(fetchedUser.getName(), user.getName());
}
```

**Benefits:**
- **Fast setup**: API creates test data in seconds
- **Full validation**: Both UI and backend are verified
- **Realistic scenario**: Matches actual user journey
- **Catches integration bugs**: UI/API mismatches

---

### Concept 2: Configuration Management

**What is it?**
Configuration management is the practice of externalizing environment-specific values (URLs, credentials, timeouts) into configuration files. This allows you to run the same tests against different environments without code changes.

**Why does it matter for automation?**
In real projects:
- Tests run against **multiple environments**: dev, qa, staging, production
- Each environment has different **URLs, credentials, database connections**
- **Hardcoding values** makes tests brittle and unmaintainable
- **Configuration files** make tests flexible and environment-agnostic

**Configuration Hierarchy:**
```
1. System Properties (highest priority)
   -Denv=qa -Dbrowser=chrome

2. Environment Variables
   export BASE_URL=https://qa.example.com

3. Config Files (properties/YAML)
   config.properties
   config-qa.yaml

4. Default Values (lowest priority)
   Hardcoded fallbacks in code
```

**Approach 1: Properties File**
```properties
# config.properties
# Base Configuration
app.name=Automation Framework
app.version=1.0.0

# Environment URLs
base.url=https://api.example.com
ui.url=https://www.example.com

# Timeouts
implicit.wait=10
explicit.wait=20
page.load.timeout=30

# Browser
browser=chrome
headless=false

# Credentials
admin.username=admin@example.com
admin.password=Admin@123

# API Configuration
api.timeout=5000
api.retry.count=3
```

**Java Code to Read Properties:**
```java
public class ConfigReader {
    private static Properties properties;
    private static final String CONFIG_FILE = "config.properties";

    // Static block - loads config when class is first used
    static {
        try {
            properties = new Properties();
            InputStream input = ConfigReader.class
                .getClassLoader()
                .getResourceAsStream(CONFIG_FILE);
            properties.load(input);
        } catch (IOException e) {
            throw new RuntimeException("Failed to load config file: " + CONFIG_FILE);
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
        return Integer.parseInt(get("implicit.wait", "10"));
    }

    public static boolean isHeadless() {
        return Boolean.parseBoolean(
            System.getProperty("headless", get("headless", "false"))
        );
    }
}
```

**Usage in Tests:**
```java
@BeforeClass
public void setup() {
    // Read from config
    RestAssured.baseURI = ConfigReader.getBaseUrl();
    driver = DriverManager.getDriver(ConfigReader.getBrowser());
    driver.get(ConfigReader.getUIUrl());
}

@Test
public void loginTest() {
    // Read credentials from config
    String username = ConfigReader.get("admin.username");
    String password = ConfigReader.get("admin.password");
    loginPage.login(username, password);
}
```

**Approach 2: YAML Configuration (More Advanced)**
```yaml
# config.yaml
environments:
  dev:
    api_url: https://dev-api.example.com
    ui_url: https://dev.example.com
    database: dev_db
  qa:
    api_url: https://qa-api.example.com
    ui_url: https://qa.example.com
    database: qa_db
  prod:
    api_url: https://api.example.com
    ui_url: https://www.example.com
    database: prod_db

browser:
  name: chrome
  headless: false
  timeout:
    implicit: 10
    explicit: 20
    page_load: 30

credentials:
  admin:
    username: admin@example.com
    password: Admin@123
  user:
    username: user@example.com
    password: User@123
```

**Java Code to Read YAML (using SnakeYAML):**
```java
// pom.xml dependency
<dependency>
    <groupId>org.yaml</groupId>
    <artifactId>snakeyaml</artifactId>
    <version>2.2</version>
</dependency>

// YAMLConfigReader.java
import org.yaml.snakeyaml.Yaml;
import java.io.InputStream;
import java.util.Map;

public class YAMLConfigReader {
    private static Map<String, Object> config;
    private static String environment;

    static {
        try {
            Yaml yaml = new Yaml();
            InputStream input = YAMLConfigReader.class
                .getClassLoader()
                .getResourceAsStream("config.yaml");
            config = yaml.load(input);

            // Get environment from system property or default to 'qa'
            environment = System.getProperty("env", "qa");
        } catch (Exception e) {
            throw new RuntimeException("Failed to load YAML config");
        }
    }

    @SuppressWarnings("unchecked")
    public static String getAPIUrl() {
        Map<String, Object> envs = (Map<String, Object>) config.get("environments");
        Map<String, Object> env = (Map<String, Object>) envs.get(environment);
        return (String) env.get("api_url");
    }

    @SuppressWarnings("unchecked")
    public static String getUIUrl() {
        Map<String, Object> envs = (Map<String, Object>) config.get("environments");
        Map<String, Object> env = (Map<String, Object>) envs.get(environment);
        return (String) env.get("ui_url");
    }
}
```

**Environment-Specific Testing:**
```bash
# Run tests against QA environment
mvn test -Denv=qa

# Run tests against Production
mvn test -Denv=prod

# Run headless Chrome
mvn test -Dbrowser=chrome -Dheadless=true
```

---

### Concept 3: Design Patterns for Test Automation

**What are Design Patterns?**
Design patterns are reusable solutions to common software design problems. In test automation, patterns help create maintainable, scalable, and efficient frameworks.

---

#### Pattern 1: Singleton Pattern (Driver Management)

**Problem:**
Creating multiple WebDriver instances wastes resources and causes test conflicts. You need exactly ONE driver instance per thread.

**Solution: Singleton Pattern**
```java
public class DriverManager {
    // ThreadLocal ensures each thread gets its own driver instance
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    // Private constructor prevents external instantiation
    private DriverManager() {}

    // Get driver (creates if doesn't exist)
    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(createDriver());
        }
        return driver.get();
    }

    // Create driver based on config
    private static WebDriver createDriver() {
        String browser = ConfigReader.getBrowser();
        WebDriver webDriver;

        switch (browser.toLowerCase()) {
            case "chrome":
                WebDriverManager.chromedriver().setup();
                ChromeOptions chromeOptions = new ChromeOptions();
                if (ConfigReader.isHeadless()) {
                    chromeOptions.addArguments("--headless");
                }
                chromeOptions.addArguments("--start-maximized");
                chromeOptions.addArguments("--disable-notifications");
                webDriver = new ChromeDriver(chromeOptions);
                break;

            case "firefox":
                WebDriverManager.firefoxdriver().setup();
                FirefoxOptions firefoxOptions = new FirefoxOptions();
                if (ConfigReader.isHeadless()) {
                    firefoxOptions.addArguments("--headless");
                }
                webDriver = new FirefoxDriver(firefoxOptions);
                break;

            case "edge":
                WebDriverManager.edgedriver().setup();
                webDriver = new EdgeDriver();
                break;

            default:
                throw new IllegalArgumentException("Unsupported browser: " + browser);
        }

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
            driver.remove();
        }
    }
}
```

**Benefits:**
- **Resource efficiency**: Only one driver per thread
- **Thread safety**: ThreadLocal prevents conflicts in parallel execution
- **Centralized management**: All driver logic in one place
- **Easy configuration**: Change browser in one location

**Usage:**
```java
@BeforeMethod
public void setup() {
    driver = DriverManager.getDriver();  // Always gets the right driver
}

@AfterMethod
public void teardown() {
    DriverManager.quitDriver();  // Clean shutdown
}
```

---

#### Pattern 2: Factory Pattern (Test Data)

**Problem:**
Tests need varied test data (users, products, orders). Creating test data manually in each test is repetitive and unmaintainable.

**Solution: Factory Pattern**
```java
// Model class
public class User {
    private String firstName;
    private String lastName;
    private String email;
    private String password;
    private String role;

    // Constructor, getters, setters
    public User(String firstName, String lastName, String email,
                String password, String role) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
        this.password = password;
        this.role = role;
    }

    // Getters
    public String getFullName() {
        return firstName + " " + lastName;
    }

    public String getEmail() { return email; }
    public String getPassword() { return password; }
    public String getRole() { return role; }
}

// Factory class
import com.github.javafaker.Faker;

public class TestDataFactory {
    private static Faker faker = new Faker();

    // Generate random user
    public static User getRandomUser() {
        String firstName = faker.name().firstName();
        String lastName = faker.name().lastName();
        String email = firstName.toLowerCase() + "." + lastName.toLowerCase()
                      + faker.number().digits(3) + "@test.com";
        String password = "Test@" + faker.number().digits(4);

        return new User(firstName, lastName, email, password, "user");
    }

    // Get admin user
    public static User getAdminUser() {
        return new User(
            "Admin",
            "User",
            "admin@example.com",
            "Admin@123",
            "admin"
        );
    }

    // Get user by role
    public static User getUserByRole(String role) {
        switch (role.toLowerCase()) {
            case "admin":
                return getAdminUser();
            case "manager":
                return getManagerUser();
            default:
                return getRandomUser();
        }
    }

    private static User getManagerUser() {
        return new User(
            "Manager",
            "User",
            "manager@example.com",
            "Manager@123",
            "manager"
        );
    }

    // Generate random product
    public static Product getRandomProduct() {
        return new Product(
            faker.commerce().productName(),
            Double.parseDouble(faker.commerce().price()),
            faker.commerce().department(),
            faker.number().numberBetween(1, 100)
        );
    }
}
```

**Maven Dependency for Faker:**
```xml
<dependency>
    <groupId>com.github.javafaker</groupId>
    <artifactId>javafaker</artifactId>
    <version>1.0.2</version>
</dependency>
```

**Benefits:**
- **Centralized data creation**: One place for all test data
- **Realistic data**: Faker generates believable names, emails, etc.
- **Reusability**: Same factory used across UI and API tests
- **Easy maintenance**: Change data format in one location

**Usage:**
```java
@Test
public void createUserTest() {
    // Get random user from factory
    User user = TestDataFactory.getRandomUser();

    // Use in API test
    given()
        .body(user)
        .post("/users")
        .then()
        .statusCode(201)
        .body("email", equalTo(user.getEmail()));
}

@Test
public void adminLoginTest() {
    // Get specific user type
    User admin = TestDataFactory.getAdminUser();
    loginPage.login(admin.getEmail(), admin.getPassword());
    assertTrue(dashboardPage.isAdminPanelVisible());
}
```

---

#### Pattern 3: Builder Pattern (API Requests)

**Problem:**
API requests have many optional parameters (headers, query params, body, auth). Creating methods for every combination is impractical.

**Solution: Builder Pattern**
```java
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
}
```

**Benefits:**
- **Fluent API**: Readable, chainable method calls
- **Flexibility**: Add only the parameters you need
- **Reusability**: Same builder for all API requests
- **Maintainability**: Add new features without breaking existing code

**Usage:**
```java
// Simple GET request
Response response = APIRequestBuilder.builder()
    .baseUri("https://api.example.com")
    .get("/users/1");

// Complex POST with auth, headers, and body
User user = TestDataFactory.getRandomUser();
Response response = APIRequestBuilder.builder()
    .baseUri(ConfigReader.getBaseUrl())
    .auth(token)
    .header("X-Request-ID", UUID.randomUUID().toString())
    .queryParam("sendEmail", "true")
    .body(user)
    .log()
    .post("/users");

// Update request with path parameter
Response response = APIRequestBuilder.builder()
    .baseUri(ConfigReader.getBaseUrl())
    .auth(token)
    .pathParam("id", "123")
    .body(Map.of("status", "active"))
    .put("/users/{id}");
```

---

### Concept 4: Cross-Browser Testing

**What is it?**
Cross-browser testing ensures your application works correctly across different browsers (Chrome, Firefox, Edge, Safari). This is critical because users access your app from various browsers.

**Why does it matter for automation?**
- **Different browser engines**: Chrome (Blink), Firefox (Gecko), Safari (WebKit)
- **Browser-specific bugs**: Features work in Chrome but fail in Firefox
- **Market share**: You need to support browsers your users actually use
- **Product company requirement**: Must test on multiple browsers

**Traditional Approach (Manual Driver Management):**
```java
// Old way - download drivers manually, set system property
System.setProperty("webdriver.chrome.driver", "/path/to/chromedriver");
WebDriver driver = new ChromeDriver();

// Problem:
// - Different OS needs different drivers
// - Driver version must match browser version
// - Manual downloads and updates
```

**Modern Approach: WebDriverManager**
```xml
<!-- pom.xml -->
<dependency>
    <groupId>io.github.bonigarcia</groupId>
    <artifactId>webdrivermanager</artifactId>
    <version>5.6.2</version>
</dependency>
```

**WebDriverManager automatically:**
- Downloads the correct driver for your OS
- Matches driver version to installed browser version
- Caches drivers to avoid repeated downloads
- Handles all system property configuration

**Enhanced DriverManager with Cross-Browser Support:**
```java
public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        return getDriver(ConfigReader.getBrowser());
    }

    public static WebDriver getDriver(String browser) {
        if (driver.get() == null) {
            driver.set(createDriver(browser));
        }
        return driver.get();
    }

    private static WebDriver createDriver(String browser) {
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

            case "safari":
                // Safari driver comes with macOS, no WebDriverManager needed
                webDriver = new SafariDriver();
                break;

            default:
                throw new IllegalArgumentException("Unsupported browser: " + browser);
        }

        // Common configuration for all browsers
        webDriver.manage().timeouts()
            .implicitlyWait(Duration.ofSeconds(ConfigReader.getImplicitWait()));
        webDriver.manage().timeouts()
            .pageLoadTimeout(Duration.ofSeconds(30));
        webDriver.manage().window().maximize();

        return webDriver;
    }

    public static void quitDriver() {
        if (driver.get() != null) {
            driver.get().quit();
            driver.remove();
        }
    }
}
```

**Running Cross-Browser Tests:**
```bash
# Chrome
mvn test -Dbrowser=chrome

# Firefox
mvn test -Dbrowser=firefox

# Edge
mvn test -Dbrowser=edge

# Headless Chrome (for CI/CD)
mvn test -Dbrowser=chrome -Dheadless=true
```

**TestNG Parallel Cross-Browser Execution:**
```xml
<!-- testng.xml -->
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Cross-Browser Suite" parallel="tests" thread-count="3">

    <test name="Chrome Tests">
        <parameter name="browser" value="chrome"/>
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>

    <test name="Firefox Tests">
        <parameter name="browser" value="firefox"/>
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>

    <test name="Edge Tests">
        <parameter name="browser" value="edge"/>
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>

</suite>
```

**Test Class with Browser Parameter:**
```java
public class LoginTest extends BaseTest {

    @Parameters("browser")
    @BeforeMethod
    public void setup(@Optional("chrome") String browser) {
        driver = DriverManager.getDriver(browser);
        driver.get(ConfigReader.getUIUrl());
    }

    @Test
    public void loginTest() {
        // Same test runs on all browsers
        loginPage.login("user@example.com", "password");
        assertTrue(dashboardPage.isLoaded());
    }
}
```

---

### Concept 5: Docker for Selenium

**What is Docker?**
Docker is a platform that packages applications and their dependencies into containers. For Selenium, Docker provides pre-configured browser environments that run consistently across any machine.

**Why Docker for Selenium?**
- **Consistency**: Same browser version everywhere (local, CI, colleague's machine)
- **No installation**: No need to install browsers or drivers
- **Isolation**: Tests run in containers, don't affect your system
- **Scalability**: Easily run parallel tests on multiple containers
- **CI/CD ready**: Perfect for Jenkins, GitHub Actions

**Docker Selenium Images:**
```bash
# Standalone Chrome
docker pull selenium/standalone-chrome:latest

# Standalone Firefox
docker pull selenium/standalone-firefox:latest

# Selenium Grid Hub + Nodes (for parallel execution)
docker pull selenium/hub:latest
docker pull selenium/node-chrome:latest
docker pull selenium/node-firefox:latest
```

**Option 1: Standalone Container (Simple)**
```bash
# Run Chrome in Docker container
docker run -d -p 4444:4444 -p 7900:7900 --shm-size="2g" \
  selenium/standalone-chrome:latest

# Access VNC viewer (watch tests run)
# Open browser: http://localhost:7900
# Password: secret
```

**Java Code to Connect to Docker Selenium:**
```java
public class DockerDriverManager {

    public static RemoteWebDriver getRemoteDriver(String browser) {
        String hubUrl = "http://localhost:4444";  // Docker Selenium Hub

        DesiredCapabilities capabilities;

        switch (browser.toLowerCase()) {
            case "chrome":
                ChromeOptions chromeOptions = new ChromeOptions();
                chromeOptions.addArguments("--start-maximized");
                chromeOptions.addArguments("--disable-notifications");
                capabilities = new DesiredCapabilities();
                capabilities.setCapability(ChromeOptions.CAPABILITY, chromeOptions);
                break;

            case "firefox":
                FirefoxOptions firefoxOptions = new FirefoxOptions();
                capabilities = new DesiredCapabilities();
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
                .implicitlyWait(Duration.ofSeconds(10));
            return driver;
        } catch (MalformedURLException e) {
            throw new RuntimeException("Invalid Selenium Hub URL: " + hubUrl);
        }
    }
}
```

**Option 2: Docker Compose (Grid Setup for Parallel Execution)**
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
      - "7901:7900"

  firefox-node:
    image: selenium/node-firefox:latest
    depends_on:
      - selenium-hub
    environment:
      - SE_EVENT_BUS_HOST=selenium-hub
      - SE_EVENT_BUS_PUBLISH_PORT=4442
      - SE_EVENT_BUS_SUBSCRIBE_PORT=4443
      - SE_NODE_MAX_SESSIONS=3
    shm_size: '2gb'
    ports:
      - "7902:7900"
```

**Start Docker Grid:**
```bash
# Start all services
docker-compose up -d

# View Grid Console
# http://localhost:4444/ui

# Stop all services
docker-compose down
```

**Benefits:**
- **Hub manages**: Multiple browser nodes
- **Parallel execution**: Tests run simultaneously on different nodes
- **Load balancing**: Hub distributes tests across available nodes
- **Scalable**: Add more nodes as needed

---

### Concept 6: Advanced Reporting (ExtentReports vs Allure)

**Why Advanced Reporting?**
Default TestNG/JUnit reports are basic text/HTML. Product companies need:
- **Visual dashboards**: Charts, graphs, trends
- **Screenshots**: Attached to failed tests
- **Detailed logs**: Step-by-step execution
- **Historical data**: Track pass/fail trends over time
- **Stakeholder-friendly**: Non-technical people can understand

---

#### Option 1: ExtentReports

**Features:**
- Beautiful HTML reports with charts
- Screenshot attachment
- Real-time report generation
- Easy integration with TestNG/JUnit

**Maven Dependency:**
```xml
<dependency>
    <groupId>com.aventstack</groupId>
    <artifactId>extentreports</artifactId>
    <version>5.1.1</version>
</dependency>
```

**ExtentReports Manager (Singleton):**
```java
import com.aventstack.extentreports.ExtentReports;
import com.aventstack.extentreports.ExtentTest;
import com.aventstack.extentreports.reporter.ExtentSparkReporter;
import com.aventstack.extentreports.reporter.configuration.Theme;

public class ExtentManager {
    private static ExtentReports extent;
    private static ThreadLocal<ExtentTest> test = new ThreadLocal<>();

    // Initialize ExtentReports
    public static ExtentReports createInstance(String fileName) {
        ExtentSparkReporter sparkReporter = new ExtentSparkReporter(fileName);

        // Configure report
        sparkReporter.config().setTheme(Theme.DARK);
        sparkReporter.config().setDocumentTitle("Automation Test Report");
        sparkReporter.config().setReportName("Hybrid Framework - Test Results");
        sparkReporter.config().setTimeStampFormat("MMM dd, yyyy HH:mm:ss");

        extent = new ExtentReports();
        extent.attachReporter(sparkReporter);

        // System info
        extent.setSystemInfo("OS", System.getProperty("os.name"));
        extent.setSystemInfo("Java Version", System.getProperty("java.version"));
        extent.setSystemInfo("Environment", ConfigReader.get("env", "QA"));
        extent.setSystemInfo("Browser", ConfigReader.getBrowser());

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
```

**TestNG Listener for ExtentReports:**
```java
import com.aventstack.extentreports.Status;
import org.testng.*;

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
        ExtentManager.getTest().log(Status.PASS, "Test Passed");
    }

    @Override
    public void onTestFailure(ITestResult result) {
        ExtentManager.getTest().log(Status.FAIL, "Test Failed");
        ExtentManager.getTest().log(Status.FAIL, result.getThrowable());

        // Attach screenshot for UI tests
        if (DriverManager.getDriver() != null) {
            String screenshot = captureScreenshot(result.getMethod().getMethodName());
            ExtentManager.getTest().addScreenCaptureFromPath(screenshot);
        }
    }

    @Override
    public void onTestSkipped(ITestResult result) {
        ExtentManager.getTest().log(Status.SKIP, "Test Skipped");
    }

    @Override
    public void onFinish(ITestContext context) {
        ExtentManager.getExtent().flush();
    }

    private String captureScreenshot(String testName) {
        TakesScreenshot ts = (TakesScreenshot) DriverManager.getDriver();
        File source = ts.getScreenshotAs(OutputType.FILE);
        String dest = "test-output/screenshots/" + testName + ".png";
        try {
            FileUtils.copyFile(source, new File(dest));
        } catch (IOException e) {
            e.printStackTrace();
        }
        return dest;
    }
}
```

**Enable Listener in testng.xml:**
```xml
<suite name="Test Suite">
    <listeners>
        <listener class-name="listeners.ExtentReportListener"/>
    </listeners>
    <test name="Tests">
        <classes>
            <class name="tests.LoginTest"/>
        </classes>
    </test>
</suite>
```

---

#### Option 2: Allure Reports

**Features:**
- Extremely detailed reports with attachments
- Step-by-step test execution
- Built-in categorization
- Trending over time
- Industry standard for large projects

**Maven Dependencies:**
```xml
<dependency>
    <groupId>io.qameta.allure</groupId>
    <artifactId>allure-testng</artifactId>
    <version>2.24.0</version>
</dependency>

<plugin>
    <groupId>io.qameta.allure</groupId>
    <artifactId>allure-maven</artifactId>
    <version>2.12.0</version>
</plugin>
```

**Allure Annotations:**
```java
import io.qameta.allure.*;

@Epic("User Management")
@Feature("Login")
@Story("User Login Functionality")
public class LoginTest extends BaseTest {

    @Test(description = "Verify user can login with valid credentials")
    @Severity(SeverityLevel.BLOCKER)
    @Owner("QA Team")
    public void testValidLogin() {
        step("Navigate to login page", () -> {
            driver.get(ConfigReader.getUIUrl() + "/login");
        });

        step("Enter username", () -> {
            loginPage.enterUsername("user@example.com");
        });

        step("Enter password", () -> {
            loginPage.enterPassword("password");
        });

        step("Click login button", () -> {
            loginPage.clickLogin();
        });

        step("Verify user is logged in", () -> {
            assertTrue(dashboardPage.isLoaded());
        });
    }

    @Attachment(value = "Screenshot", type = "image/png")
    public byte[] saveScreenshot() {
        return ((TakesScreenshot) driver)
            .getScreenshotAs(OutputType.BYTES);
    }
}
```

**Generate Allure Report:**
```bash
# Run tests (generates allure-results)
mvn clean test

# Generate HTML report
mvn allure:report

# Open report in browser
mvn allure:serve
```

**Comparison: ExtentReports vs Allure**

| Aspect | ExtentReports | Allure |
|--------|---------------|--------|
| **Setup** | Simple | Moderate |
| **Real-time** | Yes | No (post-execution) |
| **Annotations** | Limited | Extensive (@Step, @Severity, @Epic) |
| **Screenshots** | Manual attachment | Auto-attachment |
| **Trends** | Basic | Advanced (historical data) |
| **Best For** | Small-medium projects | Large enterprise projects |
| **Industry** | Common in startups | Standard in product companies |

**Recommendation:**
- **ExtentReports**: Quick setup, good for demos, real-time visibility
- **Allure**: Production frameworks, detailed analysis, trending

---

## 🐍 Python vs Java Framework Comparison

### Configuration Management

**Python (pytest with conftest.py):**
```python
# conftest.py
import pytest
import os

@pytest.fixture(scope="session")
def base_url():
    env = os.getenv("ENV", "qa")
    urls = {
        "qa": "https://qa.example.com",
        "prod": "https://www.example.com"
    }
    return urls[env]

@pytest.fixture
def api_client(base_url):
    return requests.Session()

# Usage
def test_api(api_client, base_url):
    response = api_client.get(f"{base_url}/users")
    assert response.status_code == 200
```

**Java (Properties + Singleton):**
```java
// ConfigReader.java (Singleton pattern)
public class ConfigReader {
    private static Properties properties;

    static {
        properties = new Properties();
        properties.load(new FileInputStream("config.properties"));
    }

    public static String getBaseUrl() {
        String env = System.getProperty("env", "qa");
        return properties.getProperty("base.url." + env);
    }
}

// Usage
@BeforeClass
public void setup() {
    RestAssured.baseURI = ConfigReader.getBaseUrl();
}
```

### Design Patterns

**Python (Functions and Fixtures):**
```python
# Python uses fixtures instead of Singleton
@pytest.fixture(scope="session")
def driver():
    driver = webdriver.Chrome()
    yield driver
    driver.quit()

# Python uses factory functions
def create_user():
    return {
        "name": fake.name(),
        "email": fake.email()
    }
```

**Java (Formal Design Patterns):**
```java
// Java uses Singleton pattern
public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(new ChromeDriver());
        }
        return driver.get();
    }
}

// Java uses Factory pattern
public class TestDataFactory {
    public static User createUser() {
        return new User(faker.name(), faker.email());
    }
}
```

**Key Differences:**

| Aspect | Python | Java |
|--------|--------|------|
| **Configuration** | Environment variables, conftest.py | Properties files, YAML |
| **Singleton** | Module-level variables | ThreadLocal + private constructor |
| **Factory** | Factory functions | Factory classes |
| **Builder** | kwargs, dataclasses | Builder pattern classes |
| **Reporting** | pytest-html, Allure | ExtentReports, Allure |

**Mental Model Shift:**
- **Python**: Flexible, functional, fixture-based
- **Java**: Structured, object-oriented, pattern-based
- **Both**: Achieve same goals with different approaches

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Not Using ThreadLocal for Parallel Execution

❌ **Wrong:**
```java
public class DriverManager {
    private static WebDriver driver;  // Shared across threads!

    public static WebDriver getDriver() {
        if (driver == null) {
            driver = new ChromeDriver();
        }
        return driver;
    }
}
// Problem: Parallel tests interfere with each other
```

✅ **Correct:**
```java
public class DriverManager {
    private static ThreadLocal<WebDriver> driver = new ThreadLocal<>();

    public static WebDriver getDriver() {
        if (driver.get() == null) {
            driver.set(new ChromeDriver());
        }
        return driver.get();
    }
}
// Each thread gets its own driver instance
```

**Why it matters:** Without ThreadLocal, parallel tests will fail randomly because threads share the same driver.

---

### Mistake 2: Hardcoding Environment URLs

❌ **Wrong:**
```java
@BeforeClass
public void setup() {
    RestAssured.baseURI = "https://qa-api.example.com";  // Hardcoded
    driver.get("https://qa.example.com");  // Hardcoded
}
```

✅ **Correct:**
```java
@BeforeClass
public void setup() {
    RestAssured.baseURI = ConfigReader.getBaseUrl();  // From config
    driver.get(ConfigReader.getUIUrl());  // From config
}

// Run against different environments
// mvn test -Denv=qa
// mvn test -Denv=prod
```

**Why it matters:** Hardcoded values require code changes for different environments. Configuration makes tests environment-agnostic.

---

### Mistake 3: Creating Driver in Every Test Method

❌ **Wrong:**
```java
@Test
public void test1() {
    WebDriver driver = new ChromeDriver();  // Create
    // test logic
    driver.quit();
}

@Test
public void test2() {
    WebDriver driver = new ChromeDriver();  // Create again
    // test logic
    driver.quit();
}
// Problem: Slow, repetitive, resource waste
```

✅ **Correct:**
```java
private WebDriver driver;

@BeforeMethod
public void setup() {
    driver = DriverManager.getDriver();  // Centralized
}

@AfterMethod
public void teardown() {
    DriverManager.quitDriver();
}

@Test
public void test1() {
    // test logic
}

@Test
public void test2() {
    // test logic
}
```

**Why it matters:** Centralized driver management makes code DRY and easier to maintain.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use Builder Pattern for Readable Test Code**
```java
// Instead of this
User user = new User("John", "Doe", "john@test.com", "Pass@123", "admin");

// Use this
User user = User.builder()
    .firstName("John")
    .lastName("Doe")
    .email("john@test.com")
    .password("Pass@123")
    .role("admin")
    .build();
// More readable, self-documenting, optional parameters
```

**Tip 2: Environment-Specific Properties Files**
```
src/test/resources/
├── config.properties       # Default
├── config-dev.properties   # Development
├── config-qa.properties    # QA
└── config-prod.properties  # Production

// Load based on -Denv=qa
String env = System.getProperty("env", "qa");
String configFile = "config-" + env + ".properties";
```

**Tip 3: Docker for Consistent CI/CD**
```yaml
# GitHub Actions with Docker Selenium
- name: Start Selenium Grid
  run: docker-compose up -d

- name: Run tests
  run: mvn test -Dselenium.hub=http://localhost:4444

- name: Stop Selenium Grid
  run: docker-compose down
```

**Tip 4: Fail Fast in Framework**
```java
// Run smoke tests first, stop if they fail
@BeforeClass(alwaysRun = true)
public void smokeTests() {
    Response response = when().get("/health");
    if (response.statusCode() != 200) {
        throw new SkipException("API is down. Skipping all tests.");
    }
}
```

---

## 🔍 Deep Dive: Framework Architecture Best Practices

**What experienced developers know:**

1. **Separation of Concerns**: Keep Page Objects, API classes, utilities, and tests separate
2. **DRY Principle**: Reuse code through utilities, factories, and base classes
3. **Configuration Over Code**: Externalize all environment-specific values
4. **Design Patterns**: Use Singleton, Factory, Builder for clean, maintainable code
5. **Reporting**: Integrate advanced reporting from day one
6. **CI/CD Ready**: Framework should run seamlessly in pipelines

**Example: Enterprise-Grade BaseTest Class**
```java
public class BaseTest {
    protected WebDriver driver;

    @BeforeMethod
    public void setup() {
        // Initialize driver
        driver = DriverManager.getDriver();
        driver.get(ConfigReader.getUIUrl());

        // Initialize RestAssured
        RestAssured.baseURI = ConfigReader.getBaseUrl();
        RestAssured.requestSpecification = new RequestSpecBuilder()
            .setContentType(ContentType.JSON)
            .build();

        // Initialize reporting
        ExtentTest test = ExtentManager.getExtent()
            .createTest(this.getClass().getSimpleName());
        ExtentManager.setTest(test);
    }

    @AfterMethod
    public void teardown() {
        DriverManager.quitDriver();
    }
}
```

**When to use this:** Every production framework. This approach scales from 10 tests to 10,000 tests.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Hybrid Framework** | UI + API tests in one framework | Selenium + RestAssured |
| **Configuration Management** | Externalizing environment values | config.properties |
| **Singleton Pattern** | Exactly one instance of a class | DriverManager |
| **Factory Pattern** | Creates objects without specifying class | TestDataFactory |
| **Builder Pattern** | Constructs complex objects step-by-step | APIRequestBuilder |
| **ThreadLocal** | Thread-specific variable storage | Parallel execution safety |
| **WebDriverManager** | Auto-downloads and configures drivers | No manual driver setup |
| **Docker Selenium** | Containerized browser environments | Consistent testing |
| **ExtentReports** | Visual HTML test reports | Real-time reporting |
| **Allure** | Advanced test reporting framework | Detailed analysis |

---

## 🎓 Interview Prep

**Common Interview Questions on Framework Design:**

**Q1:** Explain your automation framework architecture.
**A:** "I've built a hybrid framework that combines UI automation using Selenium and API automation using RestAssured. The framework follows a layered architecture:
- **Page Object Layer**: Selenium page classes for UI
- **API Layer**: RestAssured API endpoint classes
- **Utilities Layer**: DriverManager (Singleton pattern), TestDataFactory (Factory pattern), ConfigReader
- **Test Layer**: TestNG test classes that use both UI and API components
- **Reporting**: Integrated Allure for detailed reporting with screenshots
- **Configuration**: Properties files for environment management (dev/qa/prod)

This structure ensures separation of concerns, reusability, and maintainability. The same framework runs locally and in CI/CD (Jenkins/GitHub Actions) using Docker Selenium for consistency."

**Q2:** How do you handle configuration management across environments?
**A:** "I use a combination of properties files and system properties:
1. **Properties files** store environment-specific values (URLs, timeouts)
2. **System properties** override config at runtime (`-Denv=qa`)
3. **ConfigReader utility** loads the right config based on environment
4. **Priority order**: System properties → Config file → Default values

This allows the same test suite to run against dev, qa, or prod without code changes."

**Q3:** What design patterns do you use and why?
**A:**
- **Singleton (DriverManager)**: Ensures one driver per thread, prevents resource waste
- **Factory (TestDataFactory)**: Centralizes test data creation, uses Faker for realistic data
- **Builder (APIRequestBuilder)**: Creates complex API requests with fluent, readable syntax
- **Page Object Model**: Separates page structure from test logic

**Q4:** How do you run tests in parallel safely?
**A:** "I use ThreadLocal in DriverManager to ensure each thread gets its own WebDriver instance. Without ThreadLocal, parallel tests would share the same driver and fail randomly. TestNG's `parallel='methods'` or `parallel='tests'` handles the threading, and ThreadLocal ensures thread safety."

**Q5:** Docker vs local browser execution—which do you prefer?
**A:** "For local development, I use WebDriverManager with local browsers for speed. For CI/CD, I use Docker Selenium because:
- Consistent browser versions across environments
- No need to install browsers on CI servers
- Easy parallel execution with Docker Grid
- Isolation from other processes

The framework supports both through configuration."

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What is a hybrid framework and why is it valuable?**
2. **Explain the difference between Singleton, Factory, and Builder patterns.**
3. **How does ThreadLocal enable parallel execution?**
4. **What are the benefits of Docker Selenium over local browsers?**
5. **Compare ExtentReports and Allure—which would you choose for a large project?**

**Answers at the end of Practice file.**

---

## 🚀 Next Steps

Now that you understand complete framework architecture, move to:
1. **Day-6-Practice-Exercises.md** - Build framework components step-by-step
2. **Day-6-Debug-Practice.md** - Fix common framework issues
3. **Day-6-Project-Guide.md** - Build production-ready hybrid framework

**Tomorrow Preview:** Day 7 is the final project where you'll polish and document your complete framework, making it portfolio-ready.

---

**🎯 Day 6 Complete!** You now understand enterprise-grade framework architecture combining UI and API testing with professional design patterns.
