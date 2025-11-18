# DAY 6 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As a SDET, you'll spend significant time debugging test failures, framework issues, and code problems. Understanding common abstraction-related errors will make you faster at identifying and fixing issues in real Selenium frameworks.

**Skills You'll Build:**
- Identifying interface/abstract class errors
- Understanding compilation vs runtime errors
- Reading Java error messages
- Fixing inheritance issues
- Debugging polymorphism problems

---

## Challenge 1: Cannot Instantiate Abstract Class

**Broken Code:**
```java
abstract class WebPage {
    protected String url;

    public WebPage(String url) {
        this.url = url;
    }

    public void navigate() {
        System.out.println("Navigating to: " + url);
    }

    abstract void verifyPageLoaded();
}

class HomePage extends WebPage {
    public HomePage() {
        super("https://www.example.com");
    }

    @Override
    void verifyPageLoaded() {
        System.out.println("Home page loaded successfully");
    }
}

public class Challenge1 {
    public static void main(String[] args) {
        WebPage page1 = new WebPage("https://test.com");  // Line 1
        page1.navigate();
        page1.verifyPageLoaded();

        HomePage page2 = new HomePage();  // Line 2
        page2.navigate();
        page2.verifyPageLoaded();
    }
}
```

**Error Message:**
```
Challenge1.java:22: error: WebPage is abstract; cannot be instantiated
        WebPage page1 = new WebPage("https://test.com");
                        ^
1 error
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Read the error message carefully - which line is the problem?
- Can you create objects from abstract classes?
- What's different between Line 1 and Line 2?

---

**Solution:**

**Bug Location:** Line 1 - `new WebPage("https://test.com")`

**Explanation:**
You cannot instantiate (create objects from) abstract classes. `WebPage` is abstract, so you cannot do `new WebPage()`. Abstract classes are blueprints that must be extended by concrete classes.

**Why This Causes an Error:**
Abstract classes may have abstract methods without implementations. If Java allowed you to create abstract class objects, what would happen when you call an abstract method? There's no code to execute!

**Fixed Code:**
```java
public class Challenge1 {
    public static void main(String[] args) {
        // Option 1: Create a concrete class for generic pages
        WebPage page1 = new GenericPage("https://test.com");  // ✅
        page1.navigate();
        page1.verifyPageLoaded();

        HomePage page2 = new HomePage();  // ✅ Already correct
        page2.navigate();
        page2.verifyPageLoaded();
    }
}

// Add a concrete class
class GenericPage extends WebPage {
    public GenericPage(String url) {
        super(url);
    }

    @Override
    void verifyPageLoaded() {
        System.out.println("Page loaded: " + url);
    }
}
```

**Alternative Fix (if you only need HomePage):**
```java
public class Challenge1 {
    public static void main(String[] args) {
        // Just use HomePage directly
        WebPage page = new HomePage();  // ✅ Use concrete class
        page.navigate();
        page.verifyPageLoaded();
    }
}
```

**Key Learning:**
- Abstract classes cannot be instantiated with `new`
- You must create concrete subclasses
- In Selenium, you never do `new WebDriver()` - you do `new ChromeDriver()` instead

---

## Challenge 2: Missing Interface Method Implementation

**Code:**
```java
interface Clickable {
    void click();
    void doubleClick();
    boolean isClickable();
}

interface Visible {
    boolean isVisible();
    void waitForVisibility();
}

class Button implements Clickable, Visible {
    private String buttonName;

    public Button(String buttonName) {
        this.buttonName = buttonName;
    }

    @Override
    public void click() {
        System.out.println("Clicking button: " + buttonName);
    }

    @Override
    public void doubleClick() {
        System.out.println("Double clicking button: " + buttonName);
    }

    @Override
    public boolean isVisible() {
        return true;
    }

    // Missing method implementation!
}

public class Challenge2 {
    public static void main(String[] args) {
        Button loginButton = new Button("Login");
        loginButton.click();
    }
}
```

**Error Message:**
```
Challenge2.java:13: error: Button is not abstract and does not override abstract method waitForVisibility() in Visible
class Button implements Clickable, Visible {
^
Challenge2.java:13: error: Button is not abstract and does not override abstract method isClickable() in Clickable
class Button implements Clickable, Visible {
^
2 errors
```

**Your Task:**
Find and fix the missing method implementations.

**Hints:**
- Count the methods in both interfaces
- Count the implemented methods in Button class
- Which methods are missing?

---

**Solution:**

**Bug Location:** Button class - missing `isClickable()` from Clickable interface and `waitForVisibility()` from Visible interface

**Explanation:**
When a class implements an interface, it MUST implement ALL methods from that interface. Button implements two interfaces (Clickable and Visible) but only implements 3 out of 5 methods.

**Missing Methods:**
1. `isClickable()` from Clickable interface
2. `waitForVisibility()` from Visible interface

**Fixed Code:**
```java
class Button implements Clickable, Visible {
    private String buttonName;

    public Button(String buttonName) {
        this.buttonName = buttonName;
    }

    @Override
    public void click() {
        System.out.println("Clicking button: " + buttonName);
    }

    @Override
    public void doubleClick() {
        System.out.println("Double clicking button: " + buttonName);
    }

    @Override
    public boolean isClickable() {  // ✅ Added
        System.out.println("Checking if " + buttonName + " is clickable");
        return true;
    }

    @Override
    public boolean isVisible() {
        return true;
    }

    @Override
    public void waitForVisibility() {  // ✅ Added
        System.out.println("Waiting for " + buttonName + " to be visible");
    }
}
```

**Key Learning:**
- When implementing multiple interfaces, you must implement ALL methods from ALL interfaces
- Use `@Override` annotation to catch typos early
- This is common in Selenium when using ChromeDriver (implements WebDriver, JavascriptExecutor, TakesScreenshot, etc.)

---

## Challenge 3: Incorrect Method Visibility in Interface Implementation

**Code:**
```java
interface TestRunner {
    void setup();
    void runTest();
    void teardown();
}

class SeleniumTest implements TestRunner {
    private String testName;

    public SeleniumTest(String testName) {
        this.testName = testName;
    }

    @Override
    private void setup() {  // Bug here!
        System.out.println("Setting up test: " + testName);
    }

    @Override
    protected void runTest() {  // Bug here!
        System.out.println("Running test: " + testName);
    }

    @Override
    public void teardown() {
        System.out.println("Tearing down test: " + testName);
    }
}

public class Challenge3 {
    public static void main(String[] args) {
        TestRunner test = new SeleniumTest("Login Test");
        test.setup();
        test.runTest();
        test.teardown();
    }
}
```

**Error Message:**
```
Challenge3.java:14: error: setup() in SeleniumTest cannot implement setup() in TestRunner
    private void setup() {
                 ^
  attempting to assign weaker access privileges; was public

Challenge3.java:19: error: runTest() in SeleniumTest cannot implement runTest() in TestRunner
    protected void runTest() {
                   ^
  attempting to assign weaker access privileges; was public
2 errors
```

**Your Task:**
Fix the access modifier issues

**Hints:**
- Interface methods are `public` by default
- Can you make implementing methods more restrictive?
- What's the access level hierarchy: private < default < protected < public

---

**Solution:**

**Bug Location:** Lines with `private void setup()` and `protected void runTest()`

**Explanation:**
Interface methods are implicitly `public`. When you implement an interface method, you cannot reduce its visibility. You cannot make it `private` or `protected`.

**Access Modifier Hierarchy:**
```
private < package-private (default) < protected < public
```

When overriding/implementing, you can:
- ✅ Keep same visibility (public → public)
- ✅ Increase visibility (protected → public)
- ❌ Reduce visibility (public → protected or private) - COMPILATION ERROR

**Fixed Code:**
```java
class SeleniumTest implements TestRunner {
    private String testName;

    public SeleniumTest(String testName) {
        this.testName = testName;
    }

    @Override
    public void setup() {  // ✅ Changed to public
        System.out.println("Setting up test: " + testName);
    }

    @Override
    public void runTest() {  // ✅ Changed to public
        System.out.println("Running test: " + testName);
    }

    @Override
    public void teardown() {  // ✅ Already public
        System.out.println("Tearing down test: " + testName);
    }
}
```

**Key Learning:**
- All interface methods are `public` by default (even if you don't write it)
- Implementing class methods must be `public`
- Error message "attempting to assign weaker access privileges" = you're reducing visibility
- This ensures interface contract remains accessible to everyone

---

## Challenge 4: Abstract Class Constructor Not Called

**Code:**
```java
abstract class BasePage {
    protected String pageName;
    protected String url;

    public BasePage(String pageName, String url) {
        this.pageName = pageName;
        this.url = url;
        System.out.println("BasePage constructor: " + pageName);
    }

    public void navigate() {
        System.out.println("Navigating to: " + url);
    }

    abstract void verifyElements();
}

class LoginPage extends BasePage {
    private String usernameField;
    private String passwordField;

    public LoginPage() {  // Bug here!
        this.usernameField = "username";
        this.passwordField = "password";
        System.out.println("LoginPage constructor");
    }

    @Override
    void verifyElements() {
        System.out.println("Verifying login page elements");
    }
}

public class Challenge4 {
    public static void main(String[] args) {
        LoginPage page = new LoginPage();
        page.navigate();
        page.verifyElements();
    }
}
```

**Error Message:**
```
Challenge4.java:22: error: constructor BasePage in class BasePage cannot be applied to given types;
    public LoginPage() {
           ^
  required: String,String
  found: no arguments
  reason: actual and formal argument lists differ in length
1 error
```

**Your Task:**
Fix the constructor issue

**Hints:**
- What does BasePage constructor need?
- What does LoginPage constructor provide?
- How do you call parent constructor in Java?

---

**Solution:**

**Bug Location:** LoginPage constructor - missing `super()` call to parent constructor

**Explanation:**
When a parent class (abstract or concrete) has a parameterized constructor, the child class MUST explicitly call it using `super()` with the required arguments. Java automatically calls `super()` with no arguments, but BasePage doesn't have a no-argument constructor.

**Why This Happens:**
1. BasePage has constructor: `BasePage(String pageName, String url)`
2. LoginPage constructor doesn't call `super(pageName, url)`
3. Java tries to implicitly call `super()` (no arguments)
4. BasePage has no no-argument constructor → ERROR

**Fixed Code:**
```java
class LoginPage extends BasePage {
    private String usernameField;
    private String passwordField;

    public LoginPage() {
        super("Login Page", "https://example.com/login");  // ✅ Call parent constructor
        this.usernameField = "username";
        this.passwordField = "password";
        System.out.println("LoginPage constructor");
    }

    @Override
    void verifyElements() {
        System.out.println("Verifying login page elements");
    }
}
```

**Better Approach (More Flexible):**
```java
class LoginPage extends BasePage {
    private String usernameField;
    private String passwordField;

    public LoginPage(String url) {  // Accept URL as parameter
        super("Login Page", url);  // Pass to parent
        this.usernameField = "username";
        this.passwordField = "password";
        System.out.println("LoginPage constructor");
    }

    @Override
    void verifyElements() {
        System.out.println("Verifying login page elements");
    }
}

// Usage
LoginPage page = new LoginPage("https://staging.example.com/login");
```

**Key Learning:**
- Always call parent constructor with `super()` as first line
- If parent has parameterized constructor, child must call it explicitly
- Common in Selenium page objects extending BasePage

---

## Challenge 5: Logic Bug - Interface Default Method Not Overridden

**Code:**
```java
interface Validator {
    boolean validate(String input);

    // Default method - provides default implementation
    default String getValidationMessage(boolean isValid) {
        if (isValid) {
            return "Validation passed";
        } else {
            return "Validation failed";
        }
    }
}

class EmailValidator implements Validator {
    @Override
    public boolean validate(String input) {
        return input != null && input.contains("@");
    }

    // Not overriding getValidationMessage - uses default
}

class PasswordValidator implements Validator {
    @Override
    public boolean validate(String input) {
        return input != null && input.length() >= 8;
    }

    // Intentionally using different message but forgot to override!
}

public class Challenge5 {
    public static void main(String[] args) {
        Validator emailVal = new EmailValidator();
        String email = "user@example.com";
        boolean result1 = emailVal.validate(email);
        System.out.println("Email: " + email);
        System.out.println(emailVal.getValidationMessage(result1));
        System.out.println();

        Validator passVal = new PasswordValidator();
        String password = "12345";  // Too short!
        boolean result2 = passVal.validate(password);
        System.out.println("Password: " + password);
        System.out.println(passVal.getValidationMessage(result2));
    }
}
```

**Expected Output:**
```
Email: user@example.com
Validation passed

Password: 12345
Password must be at least 8 characters long
```

**Actual Output:**
```
Email: user@example.com
Validation passed

Password: 12345
Validation failed
```

**Your Task:**
The program runs without errors but the password validation message is not specific. Fix it to show the proper password requirement message.

**Hints:**
- No compilation error - this is a logic bug
- Default interface methods CAN be overridden
- PasswordValidator should provide a more specific message

---

**Solution:**

**Bug Location:** PasswordValidator class - should override `getValidationMessage()` to provide specific feedback

**Explanation:**
This is a logic bug, not a compilation error. The default interface method works but provides generic messages. For better user experience, PasswordValidator should override this method to give specific feedback about the 8-character requirement.

**Why Generic Messages Are Bad:**
- User sees "Validation failed" but doesn't know WHY
- In test automation, unclear error messages waste debugging time
- Good validators provide actionable feedback

**Fixed Code:**
```java
class EmailValidator implements Validator {
    @Override
    public boolean validate(String input) {
        return input != null && input.contains("@");
    }

    @Override  // ✅ Override for specific message
    public String getValidationMessage(boolean isValid) {
        if (isValid) {
            return "Email format is valid";
        } else {
            return "Email must contain @ symbol";
        }
    }
}

class PasswordValidator implements Validator {
    @Override
    public boolean validate(String input) {
        return input != null && input.length() >= 8;
    }

    @Override  // ✅ Override for specific message
    public String getValidationMessage(boolean isValid) {
        if (isValid) {
            return "Password meets requirements";
        } else {
            return "Password must be at least 8 characters long";
        }
    }
}
```

**Output After Fix:**
```
Email: user@example.com
Email format is valid

Password: 12345
Password must be at least 8 characters long
```

**Key Learning:**
- Default interface methods (Java 8+) provide default behavior
- You CAN override them for custom behavior
- Always override for specific, actionable messages
- In Selenium frameworks, specific error messages save hours of debugging

**Real Selenium Example:**
```java
interface PageObject {
    WebDriver getDriver();

    // Default method - generic wait
    default void waitForElement(By locator) {
        // Generic wait implementation
        getDriver().findElement(locator);
    }
}

class LoginPage implements PageObject {
    private WebDriver driver;

    public WebDriver getDriver() {
        return driver;
    }

    @Override
    public void waitForElement(By locator) {
        // Override with explicit wait for login page
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
    }
}
```

---

## 🎯 Debugging Checklist

When you encounter abstraction-related errors, check:

**For Abstract Classes:**
- [ ] Am I trying to instantiate an abstract class with `new`?
- [ ] Did I implement all abstract methods in the concrete class?
- [ ] Did I call `super()` in child class constructor if parent has parameterized constructor?
- [ ] Are my overriding methods at least as visible as the abstract methods?

**For Interfaces:**
- [ ] Did I implement ALL methods from ALL interfaces?
- [ ] Are all implementing methods `public`?
- [ ] Did I use `implements` keyword (not `extends`)?
- [ ] If using default methods, do I need to override them for specific behavior?

**For Both:**
- [ ] Am I using `@Override` annotation to catch typos?
- [ ] Is my class marked `abstract` if I don't implement all methods?
- [ ] Am I confusing interface reference with concrete object creation?

---

## 💡 Common Error Messages and Solutions

| Error Message | Meaning | Solution |
|--------------|---------|----------|
| "X is abstract; cannot be instantiated" | Trying to create object from abstract class/interface | Create concrete subclass |
| "X is not abstract and does not override abstract method Y" | Concrete class missing method implementation | Implement the method or make class abstract |
| "attempting to assign weaker access privileges" | Making method less visible than parent | Change to `public` for interfaces |
| "constructor X cannot be applied to given types" | Missing super() call or wrong arguments | Call super() with correct arguments |
| "X is already defined" | Duplicate method signatures | Check for typos or duplicate implementations |

---

## 🚀 Bonus Challenge: Find All Bugs

**Broken Code (Multiple Bugs):**
```java
interface Drawable {
    void draw();
    int getArea();
}

abstract class Shape implements Drawable {
    String color;

    public Shape(String color) {
        this.color = color;
    }

    void displayColor() {
        System.out.println("Color: " + color);
    }
}

class Circle extends Shape {
    private int radius;

    public Circle(int radius) {  // Bug 1
        this.radius = radius;
    }

    @Override
    private void draw() {  // Bug 2
        System.out.println("Drawing a circle");
    }
    // Bug 3: Missing method implementation
}

public class BonusChallenge {
    public static void main(String[] args) {
        Shape circle = new Circle(5);
        circle.draw();
        System.out.println("Area: " + circle.getArea());
    }
}
```

**How Many Bugs Can You Find?**

**Answers:**
1. **Bug 1:** Circle constructor doesn't call `super(color)`
2. **Bug 2:** `draw()` method is `private` (should be `public`)
3. **Bug 3:** `getArea()` method not implemented in Circle

**Fixed Code:**
```java
class Circle extends Shape {
    private int radius;

    public Circle(int radius, String color) {  // ✅ Accept color
        super(color);  // ✅ Call parent constructor
        this.radius = radius;
    }

    @Override
    public void draw() {  // ✅ Changed to public
        System.out.println("Drawing a circle with color " + color);
    }

    @Override  // ✅ Implement missing method
    public int getArea() {
        return (int) (Math.PI * radius * radius);
    }
}
```

---

## 📚 Key Takeaways

**From Today's Debugging:**
1. Abstract classes and interfaces cannot be instantiated
2. All abstract methods must be implemented (or make class abstract)
3. Interface methods must be public when implemented
4. Always call parent constructor if it has parameters
5. Default methods can be overridden for specific behavior
6. `@Override` annotation catches many bugs early

**For Selenium Automation:**
- Same principles apply to Selenium framework code
- Page objects extending BasePage must call super()
- WebDriver interface methods must be public
- Understanding these errors saves debugging time

---
