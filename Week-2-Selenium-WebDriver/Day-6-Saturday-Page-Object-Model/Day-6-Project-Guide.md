# DAY 6 PROJECT: LOGIN AUTOMATION WITH POM

## Complete POM Framework

### BasePage.java
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
    
    protected boolean isDisplayed(By locator) {
        try {
            return wait.until(ExpectedConditions.visibilityOfElementLocated(locator)).isDisplayed();
        } catch (Exception e) {
            return false;
        }
    }
}
```

### LoginPage.java
```java
public class LoginPage extends BasePage {
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("submit");
    private By errorMessage = By.className("error");
    
    public LoginPage(WebDriver driver) {
        super(driver);
    }
    
    public LoginPage enterUsername(String username) {
        type(usernameField, username);
        return this;
    }
    
    public LoginPage enterPassword(String password) {
        type(passwordField, password);
        return this;
    }
    
    public DashboardPage clickLogin() {
        click(loginButton);
        return new DashboardPage(driver);
    }
    
    public String getErrorMessage() {
        return getText(errorMessage);
    }
    
    public boolean isErrorDisplayed() {
        return isDisplayed(errorMessage);
    }
}
```

### DashboardPage.java
```java
public class DashboardPage extends BasePage {
    private By welcomeMessage = By.className("welcome");
    private By logoutButton = By.linkText("Log out");
    
    public DashboardPage(WebDriver driver) {
        super(driver);
    }
    
    public String getWelcomeMessage() {
        return getText(welcomeMessage);
    }
    
    public boolean isLoggedIn() {
        return isDisplayed(welcomeMessage);
    }
    
    public LoginPage logout() {
        click(logoutButton);
        return new LoginPage(driver);
    }
}
```

### LoginTest.java
```java
public class LoginTest {
    private WebDriver driver;
    private LoginPage loginPage;
    
    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://practicetestautomation.com/practice-test-login/");
        loginPage = new LoginPage(driver);
    }
    
    @Test
    public void testValidLogin() {
        DashboardPage dashboard = loginPage
            .enterUsername("student")
            .enterPassword("Password123")
            .clickLogin();
        
        assert dashboard.isLoggedIn();
        assert dashboard.getWelcomeMessage().contains("successfully");
    }
    
    @Test
    public void testInvalidLogin() {
        loginPage
            .enterUsername("invalid")
            .enterPassword("invalid")
            .clickLogin();
        
        assert loginPage.isErrorDisplayed();
    }
    
    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

**Learning Outcomes:**
- Clean separation of concerns
- Reusable page objects
- Maintainable test structure
- Fluent interface pattern
- Proper framework organization
