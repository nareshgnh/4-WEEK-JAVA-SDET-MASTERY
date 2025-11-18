# DAY 7 CHEAT SHEET: BEST PRACTICES

## ⚡ Framework Structure
```
src/
├── main/java/
│   ├── pages/          # Page Objects
│   ├── utils/          # Utilities
│   └── listeners/      # Listeners
└── test/
    ├── java/
    │   ├── base/       # BaseTest
    │   └── tests/      # Test classes
    └── resources/
        ├── config/     # Properties
        ├── testdata/   # Test data
        └── testng.xml  # Config
```

## 🔑 Key Patterns

### BaseTest
```java
public class BaseTest {
    protected WebDriver driver;
    protected static final Logger log = LogManager.getLogger(BaseTest.class);

    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
    }

    @AfterMethod
    public void tearDown() {
        if (driver != null) driver.quit();
    }
}
```

### Page Object
```java
public class LoginPage {
    @FindBy(id = "username")
    WebElement username;

    public LoginPage(WebDriver driver) {
        PageFactory.initElements(driver, this);
    }

    public void login(String user, String pass) {
        username.sendKeys(user);
        // ...
    }
}
```

### ConfigReader
```java
public class ConfigReader {
    private static Properties props;

    static {
        props = new Properties();
        props.load(new FileInputStream("config.properties"));
    }

    public static String get(String key) {
        return props.getProperty(key);
    }
}
```

## 💡 Best Practices
- ✅ Use BaseTest for common setup
- ✅ Page Object Model for UI interactions
- ✅ ConfigReader for environment settings
- ✅ Log4j for detailed logging
- ✅ Test independence (no dependencies)
- ✅ Meaningful assertion messages
- ✅ Proper naming conventions

## 📌 Remember This
> **Professional Framework = Maintainable + Scalable**
> - Clear structure
> - Separation of concerns
> - Reusable components
> - Configuration management
> - Proper logging

## 🎉 Week 3 Complete!
You now have a production-ready TestNG + Selenium framework!

---

**🏆 CONGRATULATIONS! Move to Week-3-Summary.md**
