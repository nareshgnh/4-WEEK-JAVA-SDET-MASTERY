# DAY 6 CHEAT SHEET: PAGE OBJECT MODEL

## ⚡ POM Structure

```
src/main/java/
  └── pages/
      ├── BasePage.java       // Common functionality
      ├── LoginPage.java      // Login page object
      └── HomePage.java       // Home page object

src/test/java/
  └── tests/
      └── LoginTest.java      // Test using page objects
```

---

## 📝 BasePage Template

```java
public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;
    
    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }
    
    protected void click(By locator) {
        wait.until(ExpectedConditions.elementToBeClickable(locator)).click();
    }
    
    protected void type(By locator, String text) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        element.clear();
        element.sendKeys(text);
    }
    
    protected String getText(By locator) {
        return wait.until(ExpectedConditions.visibilityOfElementLocated(locator)).getText();
    }
}
```

---

## 📄 Page Class Template

```java
public class LoginPage extends BasePage {
    // Locators (private)
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-btn");
    
    // Constructor
    public LoginPage(WebDriver driver) {
        super(driver);
    }
    
    // Actions
    public void enterUsername(String username) {
        type(usernameField, username);
    }
    
    public HomePage clickLogin() {
        click(loginButton);
        return new HomePage(driver);  // Return next page
    }
    
    // Fluent interface
    public LoginPage login(String user, String pass) {
        enterUsername(user);
        enterPassword(pass);
        return this;
    }
}
```

---

## 🧪 Test Class Template

```java
public class LoginTest {
    private WebDriver driver;
    private LoginPage loginPage;
    
    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.get("https://example.com");
        loginPage = new LoginPage(driver);
    }
    
    @Test
    public void testLogin() {
        HomePage home = loginPage
            .login("user", "pass")
            .submit();
        
        assert home.isLoggedIn();
    }
    
    @AfterMethod
    public void tearDown() {
        driver.quit();
    }
}
```

---

## 💡 Best Practices

**✅ DO:**
- Keep locators private in page classes
- Return page objects from methods
- Use fluent interface for readability
- Keep assertions in test classes
- One page class per page

**❌ DON'T:**
- Put assertions in page classes
- Make locators public
- Return void from navigation methods
- Mix test and page logic

---

## 📌 Remember This

> **Page Objects contain WHAT to do, Tests contain WHAT to verify.**

```java
// Page Object: Actions
public HomePage login(String user, String pass) {
    // How to login
}

// Test: Verification
assert homePage.isLoggedIn();  // What to verify
```

---

**Tomorrow: Advanced Selenium! 🚀**
