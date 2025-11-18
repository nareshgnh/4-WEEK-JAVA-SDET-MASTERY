# DAY 6 DEBUGGING CHALLENGES

## 🐛 Page Object Model Debugging

---

## Challenge 1: NullPointerException - Forgot super(driver)

**Broken Code:**
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
}

public class LoginPage extends BasePage {
    private By usernameField = By.id("user-name");

    // Constructor - FORGOT to call super()
    public LoginPage(WebDriver driver) {
        // Missing: super(driver);
    }

    public void enterUsername(String username) {
        click(usernameField);  // NullPointerException! driver and wait are null
    }
}
```

**Your Task:** Fix the null pointer exception

---

**Solution:**

**Bug:** Forgot to call `super(driver)` in the child class constructor, so `driver` and `wait` are never initialized

**Fixed Code:**
```java
public class LoginPage extends BasePage {
    private By usernameField = By.id("user-name");

    // ✓ Correctly call parent constructor
    public LoginPage(WebDriver driver) {
        super(driver);  // Initialize parent class fields
    }

    public void enterUsername(String username) {
        click(usernameField);  // ✓ Now works - driver and wait are initialized
    }
}
```

**Key Learning:**
- Every page object extending BasePage MUST call `super(driver)` first
- This initializes the protected fields `driver` and `wait` in BasePage
- Forgetting this causes NullPointerException when trying to use driver or wait
- Java doesn't enforce calling super() if parent has a parameterized constructor

**Best Practice:**
```java
// Always first line in constructor
public LoginPage(WebDriver driver) {
    super(driver);  // ALWAYS call parent constructor first
    // Then do any page-specific initialization
}
```

---

## Challenge 2: Wrong Page Object Returned

**Broken Code:**
```java
public class LoginPage extends BasePage {
    private By loginButton = By.id("login-button");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    // BUG: Returns wrong page type!
    public LoginPage clickLogin() {
        click(loginButton);
        return this;  // WRONG - should navigate to ProductsPage
    }
}

// In test
public class LoginTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        LoginPage loginPage = new LoginPage(driver);

        // This looks wrong - why would login return LoginPage?
        LoginPage resultPage = loginPage
            .navigate()
            .enterUsername("user")
            .enterPassword("pass")
            .clickLogin();  // Should go to ProductsPage!

        // Can't access ProductsPage methods
        // resultPage.addToCart("item");  // Compile error!
    }
}
```

**Your Task:** Fix the return type to match the actual navigation flow

---

**Solution:**

**Bug:** `clickLogin()` returns `LoginPage` but actually navigates to `ProductsPage`

**Fixed Code:**
```java
public class LoginPage extends BasePage {
    private By loginButton = By.id("login-button");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    // ✓ Return the page object where user lands after action
    public ProductsPage clickLogin() {
        click(loginButton);
        return new ProductsPage(driver);  // Return next page
    }

    // For method chaining within same page, return 'this'
    public LoginPage enterUsername(String username) {
        type(usernameField, username);
        return this;
    }
}

// In test - now type-safe and clear
public class LoginTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        LoginPage loginPage = new LoginPage(driver);

        // ✓ Clear navigation flow
        ProductsPage productsPage = loginPage
            .navigate()
            .enterUsername("user")
            .enterPassword("pass")
            .clickLogin();  // Returns ProductsPage

        // ✓ Now can access ProductsPage methods
        productsPage.addToCart("Sauce Labs Backpack");
    }
}
```

**Key Learning:**
- Return type should match where user ends up after action
- Actions that stay on same page → return `this` (fluent interface)
- Actions that navigate to new page → return `new NextPage(driver)`
- Makes test code type-safe and self-documenting

**Pattern:**
```java
// Stays on same page
public LoginPage enterUsername(String username) {
    return this;  // Still on LoginPage
}

// Navigates to different page
public ProductsPage clickLogin() {
    return new ProductsPage(driver);  // Now on ProductsPage
}

// No navigation needed (void or boolean for verification)
public boolean isErrorDisplayed() {
    return isDisplayed(errorMessage);
}
```

---

## Challenge 3: Locator Access Violation

**Broken Code:**
```java
public class LoginPage extends BasePage {
    // Locators are private (correct)
    private By usernameField = By.id("user-name");
    private By passwordField = By.id("password");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    public void login(String user, String pass) {
        type(usernameField, user);
        type(passwordField, pass);
    }
}

// In test - trying to access locator directly
public class LoginTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        LoginPage loginPage = new LoginPage(driver);

        // BUG: Trying to access private locator
        driver.findElement(loginPage.usernameField).sendKeys("test");
        // Compile error: usernameField has private access in LoginPage

        // BUG: Trying to expose WebElement
        // WebElement username = loginPage.getUsernameElement();
        // username.sendKeys("test");  // Breaks encapsulation!
    }
}
```

**Your Task:** Understand why this is correct design and fix the test

---

**Solution:**

**Bug (in test):** Trying to access page internals (locators or WebElements) directly from test

**Why it's actually CORRECT:**
- Locators should be private - this is good encapsulation
- Tests should interact through page methods, not directly with elements
- This is the whole point of POM!

**Fixed Code:**
```java
public class LoginPage extends BasePage {
    // ✓ Locators remain private - good encapsulation
    private By usernameField = By.id("user-name");
    private By passwordField = By.id("password");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    // ✓ Provide public methods for actions
    public LoginPage enterUsername(String username) {
        type(usernameField, username);
        return this;
    }

    public LoginPage enterPassword(String password) {
        type(passwordField, password);
        return this;
    }

    public ProductsPage clickLogin() {
        click(loginButton);
        return new ProductsPage(driver);
    }
}

// ✓ Fixed test - uses page methods
public class LoginTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        LoginPage loginPage = new LoginPage(driver);

        // ✓ Interact through page methods (proper POM)
        ProductsPage productsPage = loginPage
            .enterUsername("standard_user")
            .enterPassword("secret_sauce")
            .clickLogin();

        // No direct access to locators or WebElements needed!
    }
}
```

**Key Learning:**
- **Private locators** = Good! Hides implementation details
- **Public methods** = Interface for tests to interact with page
- **Never expose WebElements** from page objects
- If you need a new interaction, add a new method to page object

**Anti-Patterns to Avoid:**
```java
// ❌ DON'T expose locators
public By getUsernameLocator() {
    return usernameField;  // WRONG - breaks encapsulation
}

// ❌ DON'T expose WebElements
public WebElement getUsernameField() {
    return driver.findElement(usernameField);  // WRONG
}

// ❌ DON'T use public locators
public By usernameField = By.id("username");  // WRONG - should be private

// ✅ DO provide action methods
public LoginPage enterUsername(String username) {
    type(usernameField, username);
    return this;  // CORRECT
}
```

---

## Challenge 4: Locator Updated But Test Still Fails

**Broken Code:**
```java
// Page class
public class LoginPage extends BasePage {
    // Updated locator after website changed
    private By usernameField = By.id("username-input");  // Changed from "user-name"
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-button");

    public LoginPage(WebDriver driver) {
        super(driver);
    }

    public void login(String user, String pass) {
        type(usernameField, user);
        type(passwordField, pass);
        click(loginButton);
    }
}

// Another class with same name in different package!
package com.old.pages;

public class LoginPage {
    private By usernameField = By.id("user-name");  // OLD LOCATOR

    // ... old implementation
}

// Test - using wrong import!
package com.sdet.tests;

import com.old.pages.LoginPage;  // BUG: Importing old page class!
// Should be: import com.sdet.pages.LoginPage;

public class LoginTest {
    public static void main(String[] args) {
        // Uses old LoginPage with old locators
        LoginPage loginPage = new LoginPage(driver);
        loginPage.login("user", "pass");  // Fails - old locator
    }
}
```

**Your Task:** Find why test uses old locator despite updating the page class

---

**Solution:**

**Bug:** Test imports old page class from different package instead of updated one

**Fixed Code:**
```java
package com.sdet.tests;

// ✓ Import correct page class
import com.sdet.pages.LoginPage;  // NEW package
// NOT: import com.old.pages.LoginPage;

public class LoginTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        // ✓ Now uses correct LoginPage with updated locators
        LoginPage loginPage = new LoginPage(driver);
        loginPage.login("standard_user", "secret_sauce");

        System.out.println("✓ Login successful with updated locators");
    }
}
```

**Key Learning:**
- Always verify your imports when you have multiple classes with same name
- IDE might auto-import wrong class
- Use full package structure to organize page objects
- Delete old/unused page classes to avoid confusion

**Debugging Steps:**
1. Check which class is imported (hover over class name in IDE)
2. Verify package name in import statement
3. Delete old unused page classes
4. Use unique class names when possible
5. Organize pages by module/feature in packages

**Best Practice - Package Structure:**
```
src/main/java/
└── com/sdet/
    ├── pages/
    │   ├── login/
    │   │   └── LoginPage.java
    │   ├── products/
    │   │   ├── ProductsPage.java
    │   │   └── ProductDetailsPage.java
    │   └── cart/
    │       └── CartPage.java
    ├── base/
    │   └── BasePage.java
    └── tests/
        └── LoginTest.java
```

---

## Challenge 5: Shared State Between Tests

**Broken Code:**
```java
public class LoginPage extends BasePage {
    private By usernameField = By.id("user-name");

    // BUG: Static driver shared across all instances!
    private static WebDriver sharedDriver;

    public LoginPage(WebDriver driver) {
        super(driver);
        sharedDriver = driver;  // All instances share same driver
    }

    public void navigate() {
        sharedDriver.get("https://www.saucedemo.com/");
    }
}

// Test 1
public class Test1 {
    public static void main(String[] args) {
        WebDriver driver1 = new ChromeDriver();
        LoginPage page1 = new LoginPage(driver1);
        page1.navigate();
        // driver1 is at login page
    }
}

// Test 2 running in parallel
public class Test2 {
    public static void main(String[] args) {
        WebDriver driver2 = new ChromeDriver();
        LoginPage page2 = new LoginPage(driver2);

        // BUG: sharedDriver is now driver2, affecting Test1!
        page2.navigate();

        // Both tests interfere with each other!
    }
}
```

**Your Task:** Fix the shared state issue

---

**Solution:**

**Bug:** Using static driver causes tests to share state and interfere with each other

**Fixed Code:**
```java
public class LoginPage extends BasePage {
    private By usernameField = By.id("user-name");

    // ✓ Each instance gets its own driver (no static)
    public LoginPage(WebDriver driver) {
        super(driver);  // Store in instance variable
        // NO static driver!
    }

    public void navigate() {
        driver.get("https://www.saucedemo.com/");  // Use instance driver
    }
}

// ✓ Tests are now isolated
public class Test1 {
    public static void main(String[] args) {
        WebDriver driver1 = new ChromeDriver();
        LoginPage page1 = new LoginPage(driver1);
        page1.navigate();
        // driver1 independent
    }
}

public class Test2 {
    public static void main(String[] args) {
        WebDriver driver2 = new ChromeDriver();
        LoginPage page2 = new LoginPage(driver2);
        page2.navigate();
        // driver2 independent - no interference!
    }
}
```

**Key Learning:**
- **Never use static WebDriver** in page objects
- Each page instance should use its own driver instance
- BasePage stores driver as `protected WebDriver driver` (not static)
- This allows parallel test execution without interference

**When Static is OK:**
- Utility methods that don't use WebDriver
- Constants (final static String URL = "...")
- Configuration values

**When Static is WRONG:**
- WebDriver instances
- WebElements
- Page state

```java
// ❌ WRONG - Static state
public class LoginPage {
    private static WebDriver driver;  // WRONG
    private static WebElement username;  // WRONG
    private static boolean isLoggedIn;  // WRONG
}

// ✅ CORRECT - Instance state
public class LoginPage extends BasePage {
    // Inherited from BasePage (instance variable)
    // protected WebDriver driver;

    private By username = By.id("user");  // Instance
    // No state stored in page objects ideally
}

// ✅ CORRECT - Static constants only
public class LoginPage extends BasePage {
    private static final String LOGIN_URL = "https://example.com/login";  // OK
    private static final int TIMEOUT = 10;  // OK

    private By username = By.id("user");  // Instance locator
}
```

---

## 🔧 Debugging Checklist

**When getting NullPointerException in page object:**
- [ ] Did you call `super(driver)` in page constructor?
- [ ] Is driver being passed to page object constructor?
- [ ] Are you trying to use driver before initialization?

**When page navigation doesn't work as expected:**
- [ ] Does method return type match where user lands?
- [ ] Are you returning `new NextPage(driver)` for navigation?
- [ ] Are you returning `this` for same-page actions?

**When tests can't access page elements:**
- [ ] Are locators private (they should be)?
- [ ] Have you created public methods for interactions?
- [ ] Are you trying to access WebElements directly (don't)?

**When locator changes don't take effect:**
- [ ] Did you update the correct page class?
- [ ] Are you importing the right package?
- [ ] Do you have duplicate page classes?
- [ ] Did you recompile/restart application?

**When tests interfere with each other:**
- [ ] Are you using static WebDriver (don't)?
- [ ] Is each test getting its own driver instance?
- [ ] Are page objects storing state they shouldn't?

---

## 💡 Best Practices Summary

```java
// ✅ CORRECT PAGE OBJECT PATTERN

public class LoginPage extends BasePage {

    // 1. Private locators (never expose these)
    private By usernameField = By.id("user-name");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-button");

    // 2. Constructor calls super(driver)
    public LoginPage(WebDriver driver) {
        super(driver);  // ALWAYS first
    }

    // 3. Public methods for actions
    // - Return 'this' for same page
    // - Return new page object for navigation
    public LoginPage enterUsername(String username) {
        type(usernameField, username);
        return this;  // Stays on LoginPage
    }

    public LoginPage enterPassword(String password) {
        type(passwordField, password);
        return this;  // Stays on LoginPage
    }

    public ProductsPage clickLogin() {
        click(loginButton);
        return new ProductsPage(driver);  // Navigates to ProductsPage
    }

    // 4. Verification methods (return boolean or String)
    public boolean isErrorDisplayed() {
        return isDisplayed(errorMessage);
    }

    // 5. NO STATIC driver or WebElements
    // 6. NO exposed WebElements or locators
    // 7. NO assertions (those belong in tests)
}

// ❌ ANTI-PATTERNS TO AVOID

public class BadLoginPage {
    // WRONG: Public locators
    public By username = By.id("user");

    // WRONG: Static driver
    private static WebDriver driver;

    // WRONG: Exposed WebElement
    public WebElement getUsernameField() {
        return driver.findElement(By.id("user"));
    }

    // WRONG: Assertion in page object
    public void verifyLogin() {
        assertEquals(driver.getTitle(), "Products");  // Belongs in test!
    }

    // WRONG: Forgot super(driver)
    public BadLoginPage(WebDriver driver) {
        // Missing: super(driver);
    }
}
```

---

## 🎯 Quick Reference: Common Fixes

| Error | Cause | Fix |
|-------|-------|-----|
| NullPointerException | Forgot `super(driver)` | Add `super(driver)` as first line in constructor |
| Can't access page methods | Wrong return type | Return correct page object type |
| Locators not accessible | Private locators (correct!) | Use public methods instead of accessing locators |
| Changes don't take effect | Wrong import/duplicate class | Verify imports, delete old classes |
| Tests interfere | Static driver | Remove static, use instance variables |
| Compile error on WebElement | Exposing WebElement (wrong) | Return page object or void, not WebElement |

---

**Page Object Model debugging mastery achieved! 🎯**
