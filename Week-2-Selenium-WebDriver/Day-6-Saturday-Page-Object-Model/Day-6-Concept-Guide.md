# DAY 6: PAGE OBJECT MODEL (POM)

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand Page Object Model design pattern
- [ ] Create BasePage and Page classes
- [ ] Implement PageFactory pattern
- [ ] Build reusable page components
- [ ] Organize test automation framework
- [ ] Follow POM best practices

**Time Required:** 3 hours  
**Difficulty:** Medium  
**Prerequisite:** Days 1-5 (All Selenium basics)

---

## 📚 Core Concepts

### Concept 1: What is Page Object Model?

**Definition:**
Page Object Model (POM) is a design pattern where each web page is represented as a class, and page elements are defined as variables. Actions on pages become methods.

**Without POM (Bad):**
```java
// Test class with all locators and actions
public class LoginTest {
    public void testLogin() {
        driver.findElement(By.id("username")).sendKeys("user");
        driver.findElement(By.id("password")).sendKeys("pass");
        driver.findElement(By.id("login-btn")).click();
        
        // If locator changes, must update ALL tests!
    }
}
```

**With POM (Good):**
```java
// LoginPage.java - Page Object
public class LoginPage {
    private WebDriver driver;
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-btn");
    
    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }
    
    public void enterUsername(String username) {
        driver.findElement(usernameField).sendKeys(username);
    }
    
    public void enterPassword(String password) {
        driver.findElement(passwordField).sendKeys(password);
    }
    
    public void clickLogin() {
        driver.findElement(loginButton).click();
    }
}

// LoginTest.java - Test class
public class LoginTest {
    public void testLogin() {
        LoginPage loginPage = new LoginPage(driver);
        loginPage.enterUsername("user");
        loginPage.enterPassword("pass");
        loginPage.clickLogin();
        
        // If locator changes, update only LoginPage class!
    }
}
```

**Benefits:**
1. **Maintainability:** Locators in one place
2. **Reusability:** Page methods used by multiple tests
3. **Readability:** Tests read like user actions
4. **Separation:** UI logic separate from test logic

---

### Concept 2: BasePage Class

**Purpose:** Common functionality shared by all Page Objects

**BasePage.java:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;

public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;
    
    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }
    
    // Reusable methods for all pages
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
            return driver.findElement(locator).isDisplayed();
        } catch (Exception e) {
            return false;
        }
    }
}
```

---

### Concept 3: Page Class Structure

**LoginPage.java - Inherits from BasePage:**
```java
public class LoginPage extends BasePage {
    // Locators
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-btn");
    private By errorMessage = By.className("error");
    
    // Constructor
    public LoginPage(WebDriver driver) {
        super(driver);  // Call BasePage constructor
    }
    
    // Page Actions
    public void enterUsername(String username) {
        type(usernameField, username);
    }
    
    public void enterPassword(String password) {
        type(passwordField, password);
    }
    
    public HomePage clickLogin() {
        click(loginButton);
        return new HomePage(driver);  // Returns next page
    }
    
    // Fluent interface - method chaining
    public LoginPage login(String username, String password) {
        enterUsername(username);
        enterPassword(password);
        return this;
    }
    
    public HomePage submit() {
        return clickLogin();
    }
    
    // Verification methods
    public String getErrorMessage() {
        return getText(errorMessage);
    }
    
    public boolean isErrorDisplayed() {
        return isDisplayed(errorMessage);
    }
}
```

---

### Concept 4: Test Class with POM

**LoginTest.java:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;

public class LoginTest {
    private WebDriver driver;
    private LoginPage loginPage;
    
    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.get("https://example.com/login");
        loginPage = new LoginPage(driver);
    }
    
    @Test
    public void testValidLogin() {
        HomePage homePage = loginPage
            .login("validuser", "validpass")
            .submit();
        
        // Assertions on HomePage
        assert homePage.isWelcomeMessageDisplayed();
    }
    
    @Test
    public void testInvalidLogin() {
        loginPage
            .login("invalid", "invalid")
            .submit();
        
        assert loginPage.isErrorDisplayed();
        assert loginPage.getErrorMessage().contains("Invalid");
    }
    
    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}
```

---

### Concept 5: PageFactory Pattern (Optional)

**Using @FindBy Annotations:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.support.FindBy;
import org.openqa.selenium.support.PageFactory;

public class LoginPageFactory {
    private WebDriver driver;
    
    // @FindBy replaces By locators
    @FindBy(id = "username")
    private WebElement usernameField;
    
    @FindBy(id = "password")
    private WebElement passwordField;
    
    @FindBy(id = "login-btn")
    private WebElement loginButton;
    
    // Constructor initializes elements
    public LoginPageFactory(WebDriver driver) {
        this.driver = driver;
        PageFactory.initElements(driver, this);
    }
    
    // Actions use WebElement directly
    public void enterUsername(String username) {
        usernameField.sendKeys(username);
    }
    
    public void enterPassword(String password) {
        passwordField.sendKeys(password);
    }
    
    public void clickLogin() {
        loginButton.click();
    }
}
```

---

## 💡 POM Best Practices

**✅ DO:**
- One page class per page
- Keep locators private in page classes
- Return next page from methods
- Use BasePage for common functionality
- Make tests readable (fluent interface)
- Keep assertions in test classes, not page classes

**❌ DON'T:**
- Put assertions in page classes
- Return void from navigation methods
- Mix test logic with page logic
- Make locators public
- Create god classes (too many responsibilities)

---

## 🎓 Interview Questions

**Q: What is Page Object Model?**
**A:** Design pattern where each page is a class with elements as variables and actions as methods. Separates page logic from test logic, improves maintainability.

**Q: What are the advantages of POM?**
**A:** 
1. Code reusability across tests
2. Easy maintenance (locator changes in one place)
3. Improved readability (tests read like user actions)
4. Separation of concerns

**Q: What is the difference between POM and PageFactory?**
**A:** POM uses By locators and findElement(), PageFactory uses @FindBy annotations and initializes elements automatically. PageFactory is optional, POM is the pattern.

---

## ✅ Self-Check

1. What goes in a Page class vs Test class?
2. Why should methods return page objects?
3. What is BasePage used for?
4. Should assertions be in page classes?

**Answers in Practice file**

---

**Ready to build frameworks! 🎯**
