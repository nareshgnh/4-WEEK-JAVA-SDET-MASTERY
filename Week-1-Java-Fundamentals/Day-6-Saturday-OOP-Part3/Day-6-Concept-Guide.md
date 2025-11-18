# DAY 6: OOP PART 3 (ABSTRACTION, INTERFACES, ABSTRACT CLASSES)

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand abstraction and why it's crucial for test frameworks
- [ ] Master interfaces and their role in Selenium WebDriver
- [ ] Learn abstract classes and when to use them
- [ ] Differentiate between interfaces and abstract classes
- [ ] Build a Shape Calculator using abstraction principles

**Time Required:** 2.5-3 hours
**Difficulty:** Medium
**Prerequisite:** Day 5 (Inheritance and Polymorphism)

---

## 📚 Core Concepts

### Concept 1: Abstraction

**What is it?**
Abstraction is hiding implementation details and showing only essential features. It's about **what** an object does, not **how** it does it. You define behaviors that must exist without specifying how they work.

**Why does it matter for automation?**
In Selenium, you interact with `WebDriver` interface, not the actual browser implementations. You call `driver.findElement()` without knowing how Chrome, Firefox, or Edge find elements internally. This is abstraction in action.

**Real-World Analogy:**
When you drive a car, you use the steering wheel, brake, and accelerator without knowing how the engine works internally. The car **abstracts** the complex mechanics and provides simple controls.

**Syntax:**
```java
// You cannot create objects of abstract classes or interfaces directly
// You must extend/implement them in concrete classes

abstract class Vehicle {
    abstract void start();  // No implementation
}

interface Drivable {
    void drive();  // No implementation
}
```

**Explanation:**
- Abstraction is achieved using `abstract classes` and `interfaces`
- Abstract methods have no body (no `{}`)
- Concrete classes must implement all abstract methods
- Provides a contract that subclasses must follow

**Real Example from Automation:**
```java
// WebDriver is an interface in Selenium
WebDriver driver = new ChromeDriver();  // ChromeDriver implements WebDriver
driver.get("https://www.google.com");   // We don't know HOW Chrome opens URL
driver.findElement(By.id("search"));    // We don't know HOW it finds element

// We use abstraction - we know WHAT it does, not HOW
```

---

### Concept 2: Abstract Classes

**What is it?**
An abstract class is a class that cannot be instantiated (you can't create objects from it). It can have both abstract methods (without implementation) and concrete methods (with implementation).

**Why does it matter for automation?**
Abstract classes let you create base test classes with common setup/teardown logic while forcing subclasses to implement specific test methods.

**Syntax:**
```java
// Abstract class with abstract keyword
abstract class Animal {
    String name;  // Regular field

    // Constructor in abstract class
    public Animal(String name) {
        this.name = name;
    }

    // Concrete method (with implementation)
    public void sleep() {
        System.out.println(name + " is sleeping");
    }

    // Abstract method (no implementation)
    abstract void makeSound();
}

// Concrete class extends abstract class
class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    // MUST implement abstract method
    @Override
    void makeSound() {
        System.out.println(name + " says: Woof!");
    }
}

// Usage
class Main {
    public static void main(String[] args) {
        // Animal a = new Animal("Generic"); // ❌ ERROR - cannot instantiate
        Dog dog = new Dog("Buddy");
        dog.makeSound();  // Buddy says: Woof!
        dog.sleep();      // Buddy is sleeping
    }
}
```

**Explanation:**
- `abstract class` keyword declares abstract class
- Can have fields, constructors, concrete methods, and abstract methods
- At least one abstract method makes class abstract
- Child classes MUST implement ALL abstract methods (unless child is also abstract)
- Use `extends` keyword to inherit from abstract class

**Common Use Cases in Test Automation:**
```java
// Base test class with common setup
public abstract class BaseTest {
    protected WebDriver driver;

    // Concrete method - common for all tests
    @BeforeMethod
    public void setUp() {
        driver = new ChromeDriver();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
    }

    // Abstract method - each test defines its own test logic
    public abstract void runTest();

    // Concrete method - common for all tests
    @AfterMethod
    public void tearDown() {
        if (driver != null) {
            driver.quit();
        }
    }
}

// Specific test class
public class LoginTest extends BaseTest {
    @Override
    public void runTest() {
        driver.get("https://example.com/login");
        // Login test logic here
    }
}
```

---

### Concept 3: Interfaces

**What is it?**
An interface is a complete abstraction - it's a contract that defines **what** a class can do. All methods in an interface are abstract by default (before Java 8). A class can implement multiple interfaces.

**Why does it matter for automation?**
Selenium WebDriver is an interface. This allows different browsers to implement it differently while providing the same methods. Page Object Model uses interfaces to define page behaviors.

**Syntax:**
```java
// Interface declaration
interface Playable {
    // All methods are public abstract by default
    void play();
    void pause();
    void stop();
}

// Class implements interface
class MusicPlayer implements Playable {
    @Override
    public void play() {
        System.out.println("Playing music...");
    }

    @Override
    public void pause() {
        System.out.println("Music paused");
    }

    @Override
    public void stop() {
        System.out.println("Music stopped");
    }
}

// Multiple interfaces
interface Recordable {
    void record();
}

class AdvancedPlayer implements Playable, Recordable {
    public void play() { System.out.println("Playing..."); }
    public void pause() { System.out.println("Paused"); }
    public void stop() { System.out.println("Stopped"); }
    public void record() { System.out.println("Recording..."); }
}
```

**Explanation:**
- Use `interface` keyword to declare
- All methods are `public abstract` by default (no need to write)
- All fields are `public static final` by default (constants)
- Use `implements` keyword to implement interface
- A class can implement multiple interfaces (separated by commas)
- MUST implement ALL methods from interface

**Interface Features (Java 8+):**
```java
interface Vehicle {
    // Abstract method (must be implemented)
    void start();

    // Default method (has implementation, can be overridden)
    default void honk() {
        System.out.println("Beep beep!");
    }

    // Static method (called using Interface name)
    static void service() {
        System.out.println("Vehicle servicing guidelines...");
    }

    // Constants (public static final by default)
    int MAX_SPEED = 120;
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car engine started");
    }

    // Can override default method (optional)
    @Override
    public void honk() {
        System.out.println("Car horn: HONK HONK!");
    }
}

// Usage
Car car = new Car();
car.start();           // Car engine started
car.honk();           // Car horn: HONK HONK!
Vehicle.service();    // Vehicle servicing guidelines...
System.out.println(Vehicle.MAX_SPEED);  // 120
```

**Common Use Cases in Test Automation:**
```java
// WebDriver interface in Selenium
WebDriver driver = new ChromeDriver();  // ChromeDriver implements WebDriver
driver.get("https://www.google.com");
driver.findElement(By.id("search"));

// SearchContext interface (parent of WebDriver)
SearchContext context = driver;
context.findElement(By.name("q"));

// TakesScreenshot interface
TakesScreenshot screenshot = (TakesScreenshot) driver;
File srcFile = screenshot.getScreenshotAs(OutputType.FILE);

// Multiple interfaces in Page Object
interface Clickable {
    void click();
}

interface Typeable {
    void typeText(String text);
}

class LoginPage implements Clickable, Typeable {
    public void click() { /* click login button */ }
    public void typeText(String text) { /* type in field */ }
}
```

---

### Concept 4: Abstract Class vs Interface

**When to use which?**

| Aspect | Abstract Class | Interface |
|--------|---------------|-----------|
| **Purpose** | Partial abstraction | Complete abstraction |
| **Methods** | Can have abstract AND concrete | All abstract (before Java 8) |
| **Fields** | Can have any type of fields | Only constants (public static final) |
| **Constructor** | Can have constructors | Cannot have constructors |
| **Inheritance** | Single inheritance (`extends`) | Multiple inheritance (`implements`) |
| **Access Modifiers** | Can use any (public, private, protected) | All methods public by default |
| **When to use** | "IS-A" relationship with shared code | "CAN-DO" capability/contract |

**Syntax Comparison:**
```java
// ABSTRACT CLASS
abstract class Animal {
    protected String name;  // Can have fields

    public Animal(String name) {  // Can have constructor
        this.name = name;
    }

    public void eat() {  // Concrete method
        System.out.println(name + " is eating");
    }

    abstract void makeSound();  // Abstract method
}

class Dog extends Animal {  // Single inheritance only
    public Dog(String name) {
        super(name);
    }

    @Override
    void makeSound() {
        System.out.println("Woof!");
    }
}

// INTERFACE
interface Swimmable {
    // int speed;  // ❌ ERROR - fields must be constants
    int MAX_DEPTH = 100;  // ✅ OK - constant

    // public Swimmable() {}  // ❌ ERROR - no constructors

    void swim();  // Abstract method (public abstract by default)

    default void rest() {  // Default method (Java 8+)
        System.out.println("Resting after swimming");
    }
}

interface Flyable {
    void fly();
}

// Multiple interface implementation
class Duck extends Animal implements Swimmable, Flyable {
    public Duck(String name) {
        super(name);
    }

    @Override
    void makeSound() {
        System.out.println("Quack!");
    }

    @Override
    public void swim() {
        System.out.println("Duck is swimming");
    }

    @Override
    public void fly() {
        System.out.println("Duck is flying");
    }
}
```

**Decision Tree:**
```
Need to share code among related classes?
    ├─ YES → Use Abstract Class
    │         (Example: BaseTest, BasePage)
    │
    └─ NO → Use Interface
              (Example: WebDriver, Clickable, Searchable)

Multiple inheritance needed?
    └─ YES → Must use Interface
              (Example: class implementing WebDriver, TakesScreenshot, JavascriptExecutor)

Just defining a contract/capability?
    └─ YES → Use Interface
              (Example: Comparable, Runnable, Serializable)
```

**Real Example from Selenium:**
```java
// Selenium uses BOTH

// WebDriver is an INTERFACE - defines contract
public interface WebDriver extends SearchContext {
    void get(String url);
    String getTitle();
    WebElement findElement(By by);
    void close();
    void quit();
}

// RemoteWebDriver is an ABSTRACT CLASS - provides common implementation
public abstract class RemoteWebDriver implements WebDriver, JavascriptExecutor,
                                                  TakesScreenshot {
    // Common code for all remote browsers
    protected SessionId sessionId;

    public void get(String url) {
        // Common implementation
    }
}

// ChromeDriver is a CONCRETE CLASS - specific implementation
public class ChromeDriver extends RemoteWebDriver {
    // Chrome-specific implementation
}

// Your test uses the interface
WebDriver driver = new ChromeDriver();  // Interface reference, concrete object
```

---

### Concept 5: Real Selenium Examples

**Example 1: WebDriver Interface Hierarchy**
```java
// SearchContext (top-level interface)
public interface SearchContext {
    List<WebElement> findElements(By by);
    WebElement findElement(By by);
}

// WebDriver extends SearchContext
public interface WebDriver extends SearchContext {
    void get(String url);
    String getTitle();
    void close();
    void quit();
    // ... other methods
}

// Your usage
WebDriver driver = new ChromeDriver();
driver.get("https://google.com");  // WebDriver method
WebElement element = driver.findElement(By.id("search"));  // SearchContext method
```

**Example 2: Multiple Interface Implementation**
```java
// ChromeDriver implements multiple interfaces
public class ChromeDriver implements WebDriver, JavascriptExecutor, TakesScreenshot {
    // Implements all methods from all interfaces
}

// You can reference it by any interface
WebDriver driver = new ChromeDriver();
JavascriptExecutor js = (JavascriptExecutor) driver;
TakesScreenshot screenshot = (TakesScreenshot) driver;

// Use different capabilities
driver.get("https://example.com");  // WebDriver capability
js.executeScript("return document.title;");  // JavascriptExecutor capability
screenshot.getScreenshotAs(OutputType.FILE);  // TakesScreenshot capability
```

**Example 3: Page Object with Interface**
```java
// Define page behavior contract
interface Searchable {
    void search(String query);
    int getResultCount();
}

interface Navigable {
    void goToHomePage();
    void goToLoginPage();
}

// Page class implements both
class GoogleHomePage implements Searchable, Navigable {
    private WebDriver driver;

    public GoogleHomePage(WebDriver driver) {
        this.driver = driver;
    }

    @Override
    public void search(String query) {
        driver.findElement(By.name("q")).sendKeys(query);
        driver.findElement(By.name("q")).submit();
    }

    @Override
    public int getResultCount() {
        String statsText = driver.findElement(By.id("result-stats")).getText();
        // Parse and return count
        return 0;  // Simplified
    }

    @Override
    public void goToHomePage() {
        driver.get("https://www.google.com");
    }

    @Override
    public void goToLoginPage() {
        driver.findElement(By.linkText("Sign in")).click();
    }
}
```

---

## 🐍 Python vs Java Comparison

### Duck Typing in Python (What You Know)
```python
# Python uses duck typing - "If it walks like a duck and quacks like a duck..."
# No explicit interfaces needed

class Dog:
    def make_sound(self):
        print("Woof!")

class Cat:
    def make_sound(self):
        print("Meow!")

class Car:
    def make_sound(self):
        print("Honk!")

# Any object with make_sound() method works
def make_it_sound(thing):
    thing.make_sound()  # Python doesn't care about type

make_it_sound(Dog())  # Woof!
make_it_sound(Cat())  # Meow!
make_it_sound(Car())  # Honk!

# Python Selenium
from selenium import webdriver

# No explicit interface declaration
driver = webdriver.Chrome()
driver.get("https://google.com")
```

### Interfaces in Java (What You're Learning)
```java
// Java requires explicit contracts via interfaces
// Compile-time type checking

interface SoundMaker {
    void makeSound();
}

class Dog implements SoundMaker {
    @Override
    public void makeSound() {
        System.out.println("Woof!");
    }
}

class Cat implements SoundMaker {
    @Override
    public void makeSound() {
        System.out.println("Meow!");
    }
}

class Car implements SoundMaker {
    @Override
    public void makeSound() {
        System.out.println("Honk!");
    }
}

// Method requires interface type - compile-time safety
public static void makeItSound(SoundMaker thing) {
    thing.makeSound();
}

// Java Selenium
WebDriver driver = new ChromeDriver();  // Explicit interface
driver.get("https://google.com");
```

**Key Differences:**

| Aspect | Python | Java |
|--------|--------|------|
| **Type System** | Duck typing (runtime) | Interface-based (compile-time) |
| **Contract** | Implicit (if method exists) | Explicit (`implements` keyword) |
| **Error Detection** | Runtime errors | Compile-time errors |
| **Flexibility** | More flexible, less safe | Less flexible, more safe |
| **Documentation** | Method names in docstrings | Interface defines contract |
| **Multiple Inheritance** | Multiple base classes | Multiple interfaces |

**Mental Model Shift:**

**Python Mindset:**
*"As long as the object has the method I need, I can use it."*

**Java Mindset:**
*"The object must explicitly promise (via interface) that it has the methods I need."*

**Why Java's approach matters for large teams:**
```java
// In a 50-person automation team, interfaces provide:

// 1. Clear contracts
interface PageObject {
    boolean isLoaded();
    void waitForLoad();
}

// Everyone knows exactly what methods MUST exist

// 2. Compile-time safety
PageObject page = new LoginPage();  // Must implement isLoaded() and waitForLoad()

// 3. IDE autocomplete
page.  // IDE shows exactly what methods are available

// 4. Refactoring safety
// If you rename a method, compiler catches all usages
```

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Trying to Instantiate Abstract Classes or Interfaces

❌ **Wrong:**
```java
abstract class Animal {
    abstract void makeSound();
}

interface Flyable {
    void fly();
}

// In main method
Animal animal = new Animal();  // ❌ ERROR: Animal is abstract; cannot be instantiated
Flyable bird = new Flyable();  // ❌ ERROR: Flyable is abstract; cannot be instantiated
```

✅ **Correct:**
```java
// Create concrete class first
class Dog extends Animal {
    @Override
    void makeSound() {
        System.out.println("Woof!");
    }
}

class Bird implements Flyable {
    @Override
    public void fly() {
        System.out.println("Flying!");
    }
}

// Now you can create objects
Animal animal = new Dog();  // ✅ OK - Dog is concrete
Flyable bird = new Bird();  // ✅ OK - Bird is concrete
```

**Why it matters:** Abstract classes and interfaces are blueprints, not actual objects. Just like you can't live in a house blueprint, you can't create objects from abstractions.

---

### Mistake 2: Forgetting to Implement All Abstract Methods

❌ **Wrong:**
```java
interface Vehicle {
    void start();
    void stop();
    void accelerate();
}

class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }

    // Missing stop() and accelerate() implementations
    // ❌ ERROR: Car is not abstract and does not override abstract method stop()
}
```

✅ **Correct:**
```java
class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }

    @Override
    public void stop() {
        System.out.println("Car stopped");
    }

    @Override
    public void accelerate() {
        System.out.println("Car accelerating");
    }
}

// OR make the class abstract if you don't want to implement all methods
abstract class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }

    // stop() and accelerate() can be implemented by subclasses
}
```

**Why it matters:** Interfaces are contracts. If you promise to implement an interface, you must fulfill the entire contract.

---

### Mistake 3: Using Interface Methods as Private

❌ **Wrong:**
```java
interface Clickable {
    void click();
}

class Button implements Clickable {
    @Override
    private void click() {  // ❌ ERROR: Cannot reduce visibility
        System.out.println("Button clicked");
    }
}
```

✅ **Correct:**
```java
class Button implements Clickable {
    @Override
    public void click() {  // ✅ Must be public
        System.out.println("Button clicked");
    }
}
```

**Why it matters:** Interface methods are public by default. You cannot reduce visibility when implementing. This ensures the contract is accessible to everyone.

---

### Mistake 4: Confusing Abstract Class with Interface

❌ **Wrong:**
```java
// Using abstract class when interface is better
abstract class Swimmable {
    abstract void swim();
}

abstract class Flyable {
    abstract void fly();
}

// ❌ ERROR: Cannot extend multiple abstract classes
class Duck extends Swimmable, Flyable {  // Compilation error
}
```

✅ **Correct:**
```java
// Use interfaces for multiple capabilities
interface Swimmable {
    void swim();
}

interface Flyable {
    void fly();
}

class Duck implements Swimmable, Flyable {  // ✅ OK - can implement multiple
    @Override
    public void swim() {
        System.out.println("Duck swimming");
    }

    @Override
    public void fly() {
        System.out.println("Duck flying");
    }
}
```

**Why it matters:** Java allows only single inheritance for classes but multiple inheritance for interfaces. Choose interfaces when you need multiple capabilities.

---

### Mistake 5: Not Using @Override Annotation

❌ **Wrong:**
```java
interface Printable {
    void print();
}

class Document implements Printable {
    // Typo in method name - no error caught!
    public void prit() {  // Wrong method name
        System.out.println("Printing document");
    }
}

// Compilation error only when you try to instantiate
// Document doc = new Document();  // ERROR: Document is not abstract but doesn't override print()
```

✅ **Correct:**
```java
class Document implements Printable {
    @Override  // Compiler immediately catches typos
    public void print() {  // Correct method name
        System.out.println("Printing document");
    }
}
```

**Why it matters:** `@Override` annotation helps catch typos and ensures you're actually implementing the interface method correctly.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use Interfaces for Page Object Contracts**
Define what a page can do in an interface, then implement it. Makes tests more maintainable and allows easy mocking for unit tests.
```java
interface LoginPageActions {
    void enterUsername(String username);
    void enterPassword(String password);
    void clickLogin();
    boolean isLoginSuccessful();
}
```

**Tip 2: Abstract Base Test Classes Save Time**
Create abstract base classes for common test setup and teardown. Extend them in actual test classes.
```java
abstract class BaseTest {
    protected WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
    }

    @AfterMethod
    public void teardown() {
        driver.quit();
    }

    abstract void runTest();
}
```

**Tip 3: WebDriver Interface Enables Multi-Browser Testing**
Because WebDriver is an interface, you can swap browsers without changing test code:
```java
WebDriver driver;
String browser = System.getProperty("browser", "chrome");

switch(browser) {
    case "chrome": driver = new ChromeDriver(); break;
    case "firefox": driver = new FirefoxDriver(); break;
    case "edge": driver = new EdgeDriver(); break;
}

// Same test code works for all browsers!
driver.get("https://example.com");
```

**Tip 4: Use Default Methods for Common Interface Logic**
Java 8+ default methods let you share code across interface implementations:
```java
interface PageObject {
    WebDriver getDriver();

    default void clickElement(By locator) {
        getDriver().findElement(locator).click();
    }

    default void typeText(By locator, String text) {
        getDriver().findElement(locator).sendKeys(text);
    }
}
```

---

## 🔍 Deep Dive: Why Selenium Uses Interfaces

**What experienced developers know:**

Selenium's architecture is brilliant because it leverages interfaces for flexibility:

```java
// WebDriver interface allows ANY browser implementation
public interface WebDriver {
    void get(String url);
    // ... other methods
}

// Each browser implements the interface differently
class ChromeDriver implements WebDriver {
    public void get(String url) {
        // Chrome-specific implementation using ChromeDriver binary
    }
}

class FirefoxDriver implements WebDriver {
    public void get(String url) {
        // Firefox-specific implementation using GeckoDriver
    }
}

// Your test code is decoupled from browser specifics
WebDriver driver = new ChromeDriver();  // Easy to swap
driver.get("https://google.com");  // Works the same regardless of browser
```

**Example: How This Enables Advanced Patterns**
```java
// Strategy Pattern using WebDriver interface
public class BrowserFactory {
    public static WebDriver getBrowser(String browserName) {
        switch(browserName.toLowerCase()) {
            case "chrome":
                return new ChromeDriver();
            case "firefox":
                return new FirefoxDriver();
            case "edge":
                return new EdgeDriver();
            default:
                throw new IllegalArgumentException("Browser not supported: " + browserName);
        }
    }
}

// Your test
WebDriver driver = BrowserFactory.getBrowser("chrome");
// Change one line to test on Firefox!
```

**When to use this:** Always design your automation framework with interfaces. It makes your code flexible, testable, and maintainable.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Abstraction** | Hiding implementation details, showing only functionality | `WebDriver driver = new ChromeDriver();` |
| **Abstract Class** | Class that cannot be instantiated, may have abstract methods | `abstract class BasePage { }` |
| **Abstract Method** | Method declared without implementation | `abstract void click();` |
| **Interface** | Complete abstraction, contract with method signatures | `interface Clickable { void click(); }` |
| **implements** | Keyword to implement an interface | `class Button implements Clickable` |
| **extends** | Keyword to inherit from abstract class | `class LoginPage extends BasePage` |
| **@Override** | Annotation indicating method overrides parent/interface | `@Override public void click() { }` |
| **Default Method** | Interface method with default implementation (Java 8+) | `default void log() { print(); }` |
| **Multiple Inheritance** | Implementing multiple interfaces | `class X implements A, B, C` |
| **Contract** | Promise that class will implement certain methods | Interface defines contract |

---

## 🎓 Interview Prep

**Common Interview Questions on Abstraction:**

**Q1: What is the difference between abstract class and interface in Java?**
**A:**
"Abstract classes provide partial abstraction and can have both abstract and concrete methods, constructors, and fields. Interfaces provide complete abstraction with only method signatures (before Java 8). A class can extend only one abstract class but implement multiple interfaces.

In Selenium, WebDriver is an interface because it defines a contract that all browser drivers must implement, allowing any class to implement WebDriver regardless of its inheritance hierarchy. We use abstract classes like BasePage when we want to share common code across related page objects while forcing subclasses to implement page-specific methods."

**Code Example:**
```java
// Abstract class - shared setup
abstract class BasePage {
    protected WebDriver driver;

    public BasePage(WebDriver driver) {
        this.driver = driver;
    }

    public void clickElement(By locator) {  // Concrete
        driver.findElement(locator).click();
    }

    abstract boolean isLoaded();  // Abstract
}

// Interface - contract
interface Searchable {
    void search(String query);
    int getResultCount();
}

// LoginPage extends abstract class, implements interface
class LoginPage extends BasePage implements Searchable {
    public LoginPage(WebDriver driver) {
        super(driver);
    }

    @Override
    boolean isLoaded() {
        return driver.findElement(By.id("login-form")).isDisplayed();
    }

    @Override
    public void search(String query) {
        // Implementation
    }

    @Override
    public int getResultCount() {
        return 0;
    }
}
```

---

**Q2: Can you instantiate an abstract class? Why or why not?**
**A:**
"No, you cannot instantiate an abstract class because it may have abstract methods without implementations. The purpose of an abstract class is to serve as a blueprint for subclasses. However, abstract classes can have constructors which are called when you create objects of concrete subclasses.

In test automation, we use this pattern with base test classes. We can't create a BaseTest object directly, but when we create a LoginTest object, the BaseTest constructor runs to set up WebDriver."

**Code Example:**
```java
abstract class BaseTest {
    protected WebDriver driver;

    public BaseTest() {  // Constructor in abstract class
        System.out.println("BaseTest constructor called");
    }

    abstract void runTest();
}

class LoginTest extends BaseTest {
    public LoginTest() {
        super();  // Calls BaseTest constructor
        System.out.println("LoginTest constructor called");
    }

    @Override
    void runTest() {
        System.out.println("Running login test");
    }
}

// Usage
// BaseTest test = new BaseTest();  // ❌ ERROR
LoginTest test = new LoginTest();  // ✅ OK
// Output:
// BaseTest constructor called
// LoginTest constructor called
```

---

**Q3: Why can a class implement multiple interfaces but extend only one class?**
**A:**
"Java avoids the 'diamond problem' of multiple inheritance. If a class could extend multiple classes with the same method, Java wouldn't know which implementation to use. Interfaces don't have this problem because they don't provide implementations (except default methods, which have resolution rules).

This is crucial in Selenium. ChromeDriver implements multiple interfaces: WebDriver, JavascriptExecutor, TakesScreenshot, and HasCapabilities. Each interface adds a specific capability without conflicting implementations. This makes the framework flexible and allows classes to have multiple behaviors."

**Code Example:**
```java
// ChromeDriver in Selenium
public class ChromeDriver implements WebDriver, JavascriptExecutor,
                                     TakesScreenshot, HasCapabilities {
    // Implements methods from all interfaces

    // From WebDriver
    public void get(String url) { }

    // From JavascriptExecutor
    public Object executeScript(String script, Object... args) { }

    // From TakesScreenshot
    public <X> X getScreenshotAs(OutputType<X> target) { }

    // From HasCapabilities
    public Capabilities getCapabilities() { }
}

// Usage - reference by any interface
WebDriver driver = new ChromeDriver();
JavascriptExecutor js = (JavascriptExecutor) driver;
TakesScreenshot ts = (TakesScreenshot) driver;

// All work with the same object!
```

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What is the main difference between an abstract class and an interface?**
   - Hint: Think about partial vs complete abstraction

2. **Can an abstract class have a constructor? Can an interface have a constructor?**
   - Hint: One yes, one no

3. **Why does Selenium use WebDriver as an interface instead of an abstract class?**
   - Hint: Think about multiple browsers and flexibility

4. **If a class implements an interface but doesn't implement all methods, what happens?**
   - Hint: Compilation error or make class abstract

5. **Give a real example from Selenium where you've used an interface.**
   - Hint: WebDriver driver = new ChromeDriver();

**Answers at the end of Practice file.**

---
