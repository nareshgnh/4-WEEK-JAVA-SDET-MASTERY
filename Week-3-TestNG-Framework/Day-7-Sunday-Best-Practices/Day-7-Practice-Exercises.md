# DAY 7 PRACTICE EXERCISES

## Exercise 1: BaseTest Class
Create a robust base test class:
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.*;
import org.apache.logging.log4j.*;

public class BaseTest {
    protected WebDriver driver;
    protected static final Logger log = LogManager.getLogger(BaseTest.class);

    @BeforeMethod
    public void setUp() {
        log.info("Initializing browser");
        driver = new ChromeDriver();
        driver.manage().window().maximize();
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
            log.info("Browser closed");
        }
    }

    protected void navigateTo(String url) {
        log.info("Navigating to: " + url);
        driver.get(url);
    }
}
```

## Exercise 2: ConfigReader
```java
public class ConfigReader {
    private static Properties props;

    static {
        try {
            FileInputStream fis = new FileInputStream("config.properties");
            props = new Properties();
            props.load(fis);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static String get(String key) {
        return props.getProperty(key);
    }
}
```

## Exercise 3: Page Object Model
```java
public class LoginPage {
    WebDriver driver;

    @FindBy(id = "user-name")
    WebElement username;

    @FindBy(id = "password")
    WebElement password;

    @FindBy(id = "login-button")
    WebElement loginBtn;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }

    public void login(String user, String pass) {
        username.sendKeys(user);
        password.sendKeys(pass);
        loginBtn.click();
    }
}
```

## Exercise 4: Professional Test
```java
public class LoginTest extends BaseTest {
    LoginPage loginPage;

    @BeforeMethod
    public void init() {
        super.setUp();
        loginPage = new LoginPage(driver);
        navigateTo(ConfigReader.get("baseUrl"));
    }

    @Test
    public void testValidLogin() {
        log.info("Testing valid login");
        loginPage.login(
            ConfigReader.get("username"),
            ConfigReader.get("password")
        );
        Assert.assertTrue(driver.getCurrentUrl().contains("inventory"));
    }
}
```

## Exercise 5: Complete Framework
Build the complete structure:
```
src/
├── main/java/
│   ├── pages/LoginPage.java
│   └── utils/ConfigReader.java
└── test/java/
    ├── base/BaseTest.java
    └── tests/LoginTest.java
```

---

**🎉 Practice Complete! Move to Day-7-Debug-Practice.md**
