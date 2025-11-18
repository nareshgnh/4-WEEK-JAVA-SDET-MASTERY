# DAY 7 DEBUGGING CHALLENGES

## Challenge 1: Page Factory Not Initialized
```java
public class LoginPage {
    @FindBy(id = "username")
    WebElement usernameField;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        // Missing: PageFactory.initElements(driver, this);
    }
}
```
**Error:** NullPointerException when accessing usernameField
**Fix:** Add `PageFactory.initElements(driver, this);` in constructor

## Challenge 2: Config File Not Found
```java
FileInputStream fis = new FileInputStream("config.properties");
```
**Error:** FileNotFoundException
**Fix:** Use correct path: `"src/test/resources/config/config.properties"`

## Challenge 3: BaseTest Not Called
```java
public class LoginTest extends BaseTest {
    @BeforeMethod
    public void init() {
        // Forgot to call super.setUp()
        navigateTo("https://example.com");  // driver is null!
    }
}
```
**Fix:** Call `super.setUp();` first

---

**🔧 Debugging Complete! Move to Day-7-Project-Guide.md**
