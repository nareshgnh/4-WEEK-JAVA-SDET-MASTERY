# DAY 7 PROJECT: PROFESSIONAL TEST FRAMEWORK

## 🎯 Final Deliverable
Build a complete, production-ready Selenium + TestNG framework.

## 📁 Complete Structure
```
SeleniumTestNGFramework/
├── src/
│   ├── main/java/
│   │   ├── pages/
│   │   │   ├── LoginPage.java
│   │   │   └── ProductsPage.java
│   │   ├── utils/
│   │   │   ├── ConfigReader.java
│   │   │   ├── ExcelUtil.java
│   │   │   └── ScreenshotUtil.java
│   │   └── listeners/
│   │       ├── TestListener.java
│   │       └── RetryAnalyzer.java
│   └── test/
│       ├── java/
│       │   ├── base/
│       │   │   └── BaseTest.java
│       │   └── tests/
│       │       ├── LoginTests.java
│       │       └── ProductTests.java
│       └── resources/
│           ├── config/
│           │   └── config.properties
│           ├── testdata/
│           │   └── testdata.xlsx
│           └── testng.xml
├── logs/
├── reports/
├── screenshots/
└── pom.xml
```

## 📝 Key Files

### BaseTest.java
```java
public class BaseTest {
    protected WebDriver driver;
    protected static final Logger log = LogManager.getLogger(BaseTest.class);

    @BeforeMethod
    public void setUp() {
        log.info("Setting up test");
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(
            Duration.ofSeconds(Integer.parseInt(ConfigReader.get("implicitWait")))
        );
    }

    @AfterMethod
    public void tearDown(ITestResult result) {
        if (result.getStatus() == ITestResult.FAILURE) {
            ScreenshotUtil.capture(driver, result.getName());
        }
        if (driver != null) {
            driver.quit();
        }
    }
}
```

### LoginPage.java
```java
public class LoginPage {
    WebDriver driver;

    @FindBy(id = "user-name")
    WebElement username;

    @FindBy(id = "password")
    WebElement password;

    @FindBy(id = "login-button")
    WebElement loginButton;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public void login(String user, String pass) {
        username.sendKeys(user);
        password.sendKeys(pass);
        loginButton.click();
    }
}
```

### LoginTests.java
```java
public class LoginTests extends BaseTest {
    LoginPage loginPage;

    @BeforeMethod
    public void initPages() {
        driver.get(ConfigReader.get("baseUrl"));
        loginPage = new LoginPage(driver);
    }

    @Test(groups = {"smoke"})
    public void testValidLogin() {
        log.info("Testing valid login");
        loginPage.login(
            ConfigReader.get("validUsername"),
            ConfigReader.get("validPassword")
        );
        Assert.assertTrue(driver.getCurrentUrl().contains("inventory"));
    }
}
```

## ✅ Features
- ✓ Page Object Model
- ✓ Configuration management
- ✓ Logging (Log4j)
- ✓ Screenshots on failure
- ✓ Data-driven tests
- ✓ Retry mechanism
- ✓ Parallel execution
- ✓ ExtentReports
- ✓ Proper structure
- ✓ Best practices

---

**🎉 PROFESSIONAL FRAMEWORK COMPLETE!**
