# DAY 7: BEST PRACTICES & PROFESSIONAL FRAMEWORK

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Build a professional base test class
- [ ] Implement proper logging with Log4j
- [ ] Create reusable utility classes
- [ ] Organize framework structure professionally
- [ ] Apply test design patterns

**Time Required:** 3 hours
**Difficulty:** Advanced
**Prerequisite:** Days 1-6 complete

---

## 📚 Core Concepts

### Concept 1: Base Test Class

**What is it?**
A parent class that all test classes extend. Contains common setup/teardown logic, avoiding code duplication.

**Implementation:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.*;
import java.time.Duration;

public class BaseTest {
    protected WebDriver driver;
    protected static Logger log = Logger.getLogger(BaseTest.class);

    @BeforeMethod
    public void setUp() {
        log.info("Setting up test");
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
            log.info("Browser closed");
        }
    }

    // Utility methods
    protected void navigateTo(String url) {
        log.info("Navigating to: " + url);
        driver.get(url);
    }
}
```

**Usage:**
```java
public class LoginTest extends BaseTest {
    @Test
    public void testLogin() {
        navigateTo("https://example.com");
        // driver and all utility methods available
    }
}
```

### Concept 2: Logging with Log4j

**Setup (pom.xml):**
```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.20.0</version>
</dependency>
```

**Configuration (log4j2.xml):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="WARN">
    <Appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
        <File name="File" fileName="logs/test-execution.log">
            <PatternLayout pattern="%d{yyyy-MM-dd HH:mm:ss} %-5level %logger{36} - %msg%n"/>
        </File>
    </Appenders>
    <Loggers>
        <Root level="info">
            <AppenderRef ref="Console"/>
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>
</Configuration>
```

**Usage:**
```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class LoginTest {
    private static final Logger log = LogManager.getLogger(LoginTest.class);

    @Test
    public void testLogin() {
        log.info("Starting login test");
        log.debug("Debug information");
        log.warn("Warning message");
        log.error("Error occurred");
    }
}
```

### Concept 3: Framework Structure

**Professional Structure:**
```
src/
├── main/java/
│   ├── pages/              # Page Object Models
│   │   ├── LoginPage.java
│   │   └── DashboardPage.java
│   ├── utils/              # Utility classes
│   │   ├── ConfigReader.java
│   │   ├── ExcelUtil.java
│   │   └── ScreenshotUtil.java
│   └── listeners/          # TestNG listeners
│       ├── TestListener.java
│       └── RetryAnalyzer.java
├── test/java/
│   ├── base/              # Base classes
│   │   └── BaseTest.java
│   └── tests/             # Test classes
│       ├── LoginTests.java
│       └── ProductTests.java
└── test/resources/
    ├── testdata/          # Test data
    │   ├── testdata.xlsx
    │   └── testdata.csv
    ├── config/            # Configuration
    │   └── config.properties
    └── testng.xml         # TestNG config
```

### Concept 4: Configuration Management

**config.properties:**
```properties
baseUrl=https://www.saucedemo.com
browser=chrome
implicitWait=10
username=standard_user
password=secret_sauce
```

**ConfigReader.java:**
```java
import java.io.*;
import java.util.Properties;

public class ConfigReader {
    private static Properties properties;

    static {
        try {
            FileInputStream fis = new FileInputStream("src/test/resources/config/config.properties");
            properties = new Properties();
            properties.load(fis);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static String getProperty(String key) {
        return properties.getProperty(key);
    }

    public static String getBaseUrl() {
        return properties.getProperty("baseUrl");
    }

    public static String getBrowser() {
        return properties.getProperty("browser");
    }
}
```

**Usage:**
```java
@BeforeMethod
public void setUp() {
    String browser = ConfigReader.getBrowser();
    if (browser.equals("chrome")) {
        driver = new ChromeDriver();
    }
    driver.get(ConfigReader.getBaseUrl());
}
```

### Concept 5: Page Object Model (POM)

**LoginPage.java:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;

public class LoginPage {
    WebDriver driver;

    @FindBy(id = "user-name")
    WebElement usernameField;

    @FindBy(id = "password")
    WebElement passwordField;

    @FindBy(id = "login-button")
    WebElement loginButton;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public void login(String username, String password) {
        usernameField.sendKeys(username);
        passwordField.sendKeys(password);
        loginButton.click();
    }

    public boolean isLoginButtonDisplayed() {
        return loginButton.isDisplayed();
    }
}
```

**Usage in Test:**
```java
public class LoginTest extends BaseTest {
    @Test
    public void testLogin() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.login("standard_user", "secret_sauce");
        Assert.assertTrue(driver.getCurrentUrl().contains("inventory"));
    }
}
```

---

## 💡 Best Practices

**1. Naming Conventions:**
```java
// Test classes end with "Test" or "Tests"
LoginTest.java
ProductTests.java

// Test methods start with "test" or use descriptive names
testValidLogin()
verifyLoginWithInvalidCredentials()

// Page classes end with "Page"
LoginPage.java
DashboardPage.java
```

**2. Test Independence:**
```java
// BAD: Tests depend on each other
@Test
public void test1_createUser() { }

@Test(dependsOnMethods = "test1_createUser")
public void test2_editUser() { }

// GOOD: Each test is independent
@Test
public void testCreateUser() {
    createUser();  // Helper method
}

@Test
public void testEditUser() {
    createUser();  // Create user in this test too
    editUser();
}
```

**3. Assertions:**
```java
// Always include meaningful messages
Assert.assertEquals(actualTitle, expectedTitle,
    "Page title should be 'Products' but was: " + actualTitle);

// Use appropriate assertion types
Assert.assertEquals()  // For equality
Assert.assertTrue()    // For boolean conditions
Assert.assertNotNull() // For null checks
```

**4. Waits:**
```java
// Use explicit waits instead of Thread.sleep()
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.visibilityOf(element));

// NOT: Thread.sleep(5000);
```

---

## 🎓 Interview Prep

**Q: How do you structure your automation framework?**

**A:** I follow a layered architecture:
- **Base Layer**: BaseTest class with common setup/teardown
- **Page Layer**: Page Object Model for UI interactions
- **Test Layer**: Test classes extending BaseTest
- **Utils Layer**: Reusable utilities (ConfigReader, ExcelUtil, etc.)
- **Listeners**: For reporting and retry logic

I use Log4j for logging, properties files for configuration, and follow naming conventions. Tests are independent, data-driven where appropriate, and integrated with CI/CD pipelines using testng.xml configurations.

---

**🎯 Ready for final practice? Move to Day-7-Practice-Exercises.md**
