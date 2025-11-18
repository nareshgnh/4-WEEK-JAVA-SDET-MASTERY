# DAY 5: OOP PART 2 - INHERITANCE & POLYMORPHISM

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master inheritance and the `extends` keyword
- [ ] Understand and use the `super` keyword
- [ ] Implement method overriding with `@Override` annotation
- [ ] Grasp polymorphism concepts and applications
- [ ] Build a Vehicle hierarchy with real inheritance
- [ ] Apply OOP concepts to Selenium Page Object Model

**Time Required:** 2.5-3 hours
**Difficulty:** Medium
**Prerequisite:** Day 4 (Classes, Objects, Encapsulation, Constructors)

---

## 📚 Core Concepts

### Concept 1: Inheritance

**What is it?**
Inheritance allows a class (child/subclass) to inherit properties and methods from another class (parent/superclass). It promotes code reuse and establishes a hierarchical relationship between classes.

**Why does it matter for automation?**
In Selenium frameworks, you'll create a BasePage class with common methods (like `waitForElement`, `clickElement`) and have specific page classes (LoginPage, HomePage) inherit from it. This eliminates code duplication across page objects.

**Syntax:**
```java
// Parent class (Superclass)
public class Vehicle {
    protected String brand;
    protected int year;

    public void start() {
        System.out.println("Vehicle is starting...");
    }

    public void stop() {
        System.out.println("Vehicle is stopping...");
    }
}

// Child class (Subclass)
public class Car extends Vehicle {
    private int doors;

    public void openTrunk() {
        System.out.println("Trunk is opening...");
    }
}
```

**Explanation:**
- `extends` keyword establishes inheritance relationship
- `Car` inherits all accessible fields and methods from `Vehicle`
- `protected` allows access in child classes (not possible with `private`)
- Child class can add its own unique fields and methods
- Child class has access to: `brand`, `year`, `start()`, `stop()`, plus its own `doors` and `openTrunk()`

**Real Example:**
```java
public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;

    public BasePage(WebDriver driver) {
        this.driver = driver;
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }

    protected WebElement waitForElement(By locator) {
        return wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
    }

    protected void clickElement(By locator) {
        waitForElement(locator).click();
    }
}

public class LoginPage extends BasePage {
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login");

    public LoginPage(WebDriver driver) {
        super(driver);  // Call parent constructor
    }

    public void login(String username, String password) {
        clickElement(usernameField);  // Inherited method!
        driver.findElement(usernameField).sendKeys(username);
        clickElement(passwordField);
        driver.findElement(passwordField).sendKeys(password);
        clickElement(loginButton);
    }
}
```

---

### Concept 2: The `super` Keyword

**What is it?**
`super` is a reference to the parent class. It's used to:
1. Call the parent class constructor
2. Access parent class methods
3. Access parent class fields (when hidden by child class)

**Why does it matter for automation?**
When extending BasePage or BaseTest classes in frameworks, you need `super()` to properly initialize the parent class, ensuring WebDriver and other shared resources are set up correctly.

**Syntax:**
```java
public class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public void makeSound() {
        System.out.println("Some generic animal sound");
    }
}

public class Dog extends Animal {
    private String breed;

    // Constructor using super to call parent constructor
    public Dog(String name, String breed) {
        super(name);  // Must be first statement in constructor
        this.breed = breed;
    }

    @Override
    public void makeSound() {
        super.makeSound();  // Call parent's method first
        System.out.println("Woof! Woof!");
    }
}
```

**Explanation:**
- `super(name)` calls the parent constructor with `name` parameter
- `super.makeSound()` calls the parent's version of the method
- `super()` must be the first statement in a constructor
- If you don't call `super()` explicitly, Java calls the no-arg parent constructor automatically

**Common Use Cases in Test Automation:**
1. **Extending BasePage:** `super(driver)` passes WebDriver to parent
2. **Test setup:** `super.setUp()` calls parent's setup before adding child-specific setup
3. **Reusing parent logic:** `super.method()` then add child-specific behavior

---

### Concept 3: Method Overriding

**What is it?**
Method overriding allows a child class to provide its own implementation of a method that's already defined in the parent class. The child's version "overrides" the parent's version.

**Why does it matter for automation?**
Different page objects may need different implementations of common methods. For example, a `waitForPageLoad()` method might wait for different elements on different pages.

**Syntax:**
```java
public class Vehicle {
    public void displayInfo() {
        System.out.println("This is a vehicle");
    }

    public double calculateFuelEfficiency() {
        return 25.0;  // Default MPG
    }
}

public class ElectricCar extends Vehicle {
    @Override  // Annotation to ensure we're actually overriding
    public void displayInfo() {
        System.out.println("This is an electric car");
    }

    @Override
    public double calculateFuelEfficiency() {
        return 100.0;  // Electric cars are more efficient
    }
}
```

**Rules for Method Overriding:**
1. Method signature must be identical (name, parameters, return type)
2. Access modifier cannot be more restrictive (can be less restrictive)
3. Cannot override `final` methods
4. Cannot override `static` methods (but can hide them)
5. Use `@Override` annotation (compiler will verify you're overriding correctly)

**Example:**
```java
Vehicle car = new ElectricCar();
car.displayInfo();  // Output: "This is an electric car"
// Child's overridden method is called
```

---

### Concept 4: Polymorphism

**What is it?**
Polymorphism means "many forms." It allows objects of different classes to be treated as objects of a common parent class. The actual method that gets executed is determined at runtime based on the object's actual type.

**Why does it matter for automation?**
Polymorphism enables writing flexible, maintainable test code. You can write methods that accept a BasePage and work with any specific page (LoginPage, HomePage, etc.).

**Syntax:**
```java
// Parent reference, child object
Vehicle myVehicle1 = new Car();
Vehicle myVehicle2 = new Truck();
Vehicle myVehicle3 = new Motorcycle();

// All can be treated as Vehicle, but each behaves differently
myVehicle1.start();  // Calls Car's version
myVehicle2.start();  // Calls Truck's version
myVehicle3.start();  // Calls Motorcycle's version
```

**Explanation:**
- **Reference type:** `Vehicle` (what it looks like)
- **Object type:** `Car`, `Truck`, `Motorcycle` (what it actually is)
- When you call a method, Java looks at the actual object type, not the reference type
- This is called **runtime polymorphism** or **dynamic method dispatch**

**Real Example:**
```java
public class TestRunner {
    // This method can work with ANY page that extends BasePage
    public void performCommonAction(BasePage page) {
        page.waitForPageLoad();  // Each page implements this differently
        page.captureScreenshot();
    }

    public void runTests() {
        BasePage login = new LoginPage(driver);
        BasePage home = new HomePage(driver);
        BasePage checkout = new CheckoutPage(driver);

        performCommonAction(login);     // Works!
        performCommonAction(home);      // Works!
        performCommonAction(checkout);  // Works!
    }
}
```

**Types of Polymorphism:**

1. **Compile-time Polymorphism (Method Overloading)** - Covered in Day 4
```java
public void login(String username, String password) { }
public void login(String username) { }  // Different parameters
```

2. **Runtime Polymorphism (Method Overriding)** - Today's focus
```java
Vehicle v = new Car();
v.start();  // Which start()? Determined at runtime!
```

---

### Concept 5: The `@Override` Annotation

**What is it?**
`@Override` is an annotation that tells the compiler "I intend to override a parent method." The compiler will error if you're not actually overriding anything.

**Why does it matter for automation?**
Prevents bugs from typos or signature mismatches. If you misspell a method name or get parameters wrong, the compiler catches it immediately.

**Syntax:**
```java
public class Parent {
    public void testMethod() {
        System.out.println("Parent method");
    }
}

public class Child extends Parent {
    @Override
    public void testMethod() {  // Compiler verifies this overrides parent
        System.out.println("Child method");
    }

    // @Override
    // public void testMetod() {  // Typo! Compiler error with @Override
    //     System.out.println("Oops");
    // }
}
```

**Best Practice:**
Always use `@Override` when overriding methods. It's a safety net that catches errors at compile time.

---

### Concept 6: Access Modifiers in Inheritance

**What is it?**
Understanding which members of a parent class are accessible to child classes.

| Modifier | Same Class | Same Package | Child Class | Other |
|----------|------------|--------------|-------------|-------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| `default` (no modifier) | ✅ | ✅ | ❌* | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

*Unless child is in same package

**Syntax:**
```java
public class Vehicle {
    private String vin;           // NOT inherited (not accessible in child)
    protected String brand;       // Inherited and accessible
    public int year;              // Inherited and accessible

    private void privateMethod() { }      // NOT accessible in child
    protected void protectedMethod() { }  // Accessible in child
    public void publicMethod() { }        // Accessible in child
}

public class Car extends Vehicle {
    public void testAccess() {
        // System.out.println(vin);         // ERROR: private
        System.out.println(brand);          // OK: protected
        System.out.println(year);           // OK: public

        // privateMethod();                 // ERROR: private
        protectedMethod();                  // OK: protected
        publicMethod();                     // OK: public
    }
}
```

**Common Use Cases in Test Automation:**
- `protected WebDriver driver` - Accessible in all page object subclasses
- `protected WebDriverWait wait` - Shared waiting mechanism
- `private` helper methods - Internal to base class only

---

## 🐍 Python vs Java Comparison

### Inheritance in Python (What You Know)
```python
# Python inheritance
class Vehicle:
    def __init__(self, brand):
        self.brand = brand

    def start(self):
        print("Vehicle starting")

class Car(Vehicle):  # Inheritance with parentheses
    def __init__(self, brand, doors):
        super().__init__(brand)  # Call parent constructor
        self.doors = doors

    def start(self):  # Override (no special syntax needed)
        super().start()  # Call parent method
        print("Car starting")

# Polymorphism
vehicle = Car("Toyota", 4)
vehicle.start()  # Calls Car's version
```

### Inheritance in Java (What You're Learning)
```java
// Java inheritance
public class Vehicle {
    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    public void start() {
        System.out.println("Vehicle starting");
    }
}

public class Car extends Vehicle {  // extends keyword
    private int doors;

    public Car(String brand, int doors) {
        super(brand);  // Call parent constructor
        this.doors = doors;
    }

    @Override  // Explicit annotation
    public void start() {
        super.start();  // Call parent method
        System.out.println("Car starting");
    }
}

// Polymorphism
Vehicle vehicle = new Car("Toyota", 4);
vehicle.start();  // Calls Car's version
```

**Key Differences:**
| Aspect | Python | Java |
|--------|--------|------|
| **Inheritance Syntax** | `class Child(Parent):` | `class Child extends Parent` |
| **Super Call** | `super().__init__()` | `super()` |
| **Override Indicator** | No special syntax | `@Override` annotation |
| **Multiple Inheritance** | Supported | Not supported (use interfaces) |
| **Access Control** | Convention (`_private`) | Enforced (`private`, `protected`) |
| **Polymorphism** | Duck typing | Type-based |

**Mental Model Shift:**
Python uses duck typing ("if it walks like a duck..."), so inheritance is more about code reuse. Java uses type-based polymorphism, so inheritance also defines the "is-a" relationship that the type system understands.

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Forgetting `super()` in Constructor
❌ **Wrong:**
```java
public class Car extends Vehicle {
    private int doors;

    public Car(String brand, int doors) {
        // Forgot to call super(brand)!
        this.doors = doors;  // Compiler error if Vehicle has no no-arg constructor
    }
}
```

✅ **Correct:**
```java
public class Car extends Vehicle {
    private int doors;

    public Car(String brand, int doors) {
        super(brand);  // Must call parent constructor
        this.doors = doors;
    }
}
```

**Why it matters:** Parent class must be properly initialized. If parent has no no-arg constructor, you must explicitly call `super()` with arguments.

---

### Mistake 2: Trying to Override Private Methods
❌ **Wrong:**
```java
public class Vehicle {
    private void start() {
        System.out.println("Starting vehicle");
    }
}

public class Car extends Vehicle {
    @Override  // ERROR! Can't override private method
    private void start() {
        System.out.println("Starting car");
    }
}
```

✅ **Correct:**
```java
public class Vehicle {
    protected void start() {  // Or public
        System.out.println("Starting vehicle");
    }
}

public class Car extends Vehicle {
    @Override
    public void start() {  // Can be same or less restrictive
        System.out.println("Starting car");
    }
}
```

**Why it matters:** Private methods are not inherited, so they can't be overridden. Use `protected` or `public` for methods you want to override.

---

### Mistake 3: Hiding Parent Fields
❌ **Wrong (Field Hiding - Confusing):**
```java
public class Vehicle {
    protected String brand = "Generic";
}

public class Car extends Vehicle {
    private String brand = "Tesla";  // Hides parent's brand!

    public void display() {
        System.out.println(brand);        // Prints "Tesla"
        System.out.println(super.brand);  // Prints "Generic"
    }
}
```

✅ **Correct:**
```java
public class Vehicle {
    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }
}

public class Car extends Vehicle {
    // No duplicate brand field, use parent's
    public Car(String brand) {
        super(brand);
    }
}
```

**Why it matters:** Field hiding creates confusion. Fields are not overridden like methods - both versions exist. Always use the parent's field or rename to avoid hiding.

---

### Mistake 4: More Restrictive Override
❌ **Wrong:**
```java
public class Vehicle {
    public void start() {
        System.out.println("Starting");
    }
}

public class Car extends Vehicle {
    @Override
    protected void start() {  // ERROR! Can't be more restrictive
        System.out.println("Car starting");
    }
}
```

✅ **Correct:**
```java
public class Vehicle {
    protected void start() {
        System.out.println("Starting");
    }
}

public class Car extends Vehicle {
    @Override
    public void start() {  // OK: Less restrictive (or same)
        System.out.println("Car starting");
    }
}
```

**Why it matters:** Child class must honor the parent's contract. If parent promises `public` access, child can't restrict it.

---

### Mistake 5: Calling Overridden Method from Constructor
❌ **Wrong (Dangerous):**
```java
public class Vehicle {
    public Vehicle() {
        initialize();  // Calls child's version if overridden!
    }

    public void initialize() {
        System.out.println("Vehicle initialized");
    }
}

public class Car extends Vehicle {
    private int doors = 4;

    @Override
    public void initialize() {
        System.out.println("Car has " + doors + " doors");
        // Prints "Car has 0 doors" - doors not initialized yet!
    }
}
```

✅ **Correct:**
```java
public class Vehicle {
    public Vehicle() {
        // Don't call overridable methods from constructor
    }

    public final void initialize() {  // Make it final, or
        initializeVehicle();           // call private method
    }

    private void initializeVehicle() {
        System.out.println("Vehicle initialized");
    }
}
```

**Why it matters:** During construction, the child class fields aren't initialized yet, but overridden methods can be called. This leads to NullPointerException or unexpected values.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use inheritance to build a robust Page Object Model**
Create a BasePage with common functionality:
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

// All page objects extend BasePage
public class LoginPage extends BasePage { /* ... */ }
public class HomePage extends BasePage { /* ... */ }
```

**Tip 2: Override `toString()` for better debugging**
```java
public class Vehicle {
    @Override
    public String toString() {
        return "Vehicle{brand='" + brand + "', year=" + year + "}";
    }
}
// When debugging: System.out.println(car) gives useful info
```

**Tip 3: Use polymorphism for test data builders**
```java
public List<Vehicle> getTestVehicles() {
    List<Vehicle> vehicles = new ArrayList<>();
    vehicles.add(new Car("Toyota", 2020, 4));
    vehicles.add(new Truck("Ford", 2019, 2000));
    vehicles.add(new Motorcycle("Harley", 2021, true));
    // All stored as Vehicle type, but each maintains its own behavior
    return vehicles;
}
```

**Tip 4: Prefer composition over inheritance when appropriate**
Not everything should inherit. If relationship is "has-a" not "is-a", use composition:
```java
// BAD: Car is-a Engine? No!
public class Car extends Engine { }

// GOOD: Car has-a Engine
public class Car {
    private Engine engine;
}
```

---

## 🔍 Deep Dive: The Object Class

**What experienced developers know:**
Every class in Java implicitly extends `Object` class. You're always using inheritance, even if you don't realize it!

**Example:**
```java
public class Vehicle {  // Actually: public class Vehicle extends Object
    // Inherits methods from Object:
    // - toString()
    // - equals(Object obj)
    // - hashCode()
    // - getClass()
    // - etc.
}

Vehicle car = new Vehicle();
System.out.println(car.toString());     // Works! Inherited from Object
System.out.println(car.getClass());     // Works! Inherited from Object
```

**Commonly Overridden Object Methods:**
```java
public class Vehicle {
    private String brand;
    private int year;

    @Override
    public String toString() {
        return "Vehicle{brand='" + brand + "', year=" + year + "}";
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Vehicle vehicle = (Vehicle) obj;
        return year == vehicle.year && brand.equals(vehicle.brand);
    }

    @Override
    public int hashCode() {
        return Objects.hash(brand, year);
    }
}
```

**When to use this:** Override `toString()` for debugging, `equals()` and `hashCode()` when objects need to be compared or stored in collections.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Inheritance** | Mechanism where a class acquires properties/methods of another class | `class Car extends Vehicle` |
| **Superclass/Parent** | Class being inherited from | `Vehicle` is superclass of `Car` |
| **Subclass/Child** | Class that inherits | `Car` is subclass of `Vehicle` |
| **`extends`** | Keyword to establish inheritance | `class Child extends Parent` |
| **`super`** | Reference to parent class | `super.method()` or `super()` |
| **Method Overriding** | Child provides own implementation of parent method | `@Override public void start()` |
| **`@Override`** | Annotation to verify method override | `@Override` |
| **Polymorphism** | Treating objects of child classes as parent type | `Vehicle v = new Car();` |
| **Runtime Polymorphism** | Actual method determined at runtime | Dynamic method dispatch |
| **`protected`** | Access modifier visible to subclasses | `protected String brand;` |
| **`final` method** | Method that cannot be overridden | `public final void lock()` |
| **IS-A relationship** | Inheritance relationship | Car IS-A Vehicle |

---

## 🎓 Interview Prep

**Common Interview Questions on Inheritance & Polymorphism:**

**Q1:** What is inheritance and why is it useful?
**A:**
Inheritance is a mechanism where a new class (child/subclass) acquires the properties and behaviors of an existing class (parent/superclass). It promotes code reusability and establishes a hierarchical relationship.

```java
public class Vehicle {
    protected String brand;
    public void start() { }
}

public class Car extends Vehicle {
    // Inherits brand and start() method
    // Can add its own members
}
```

*Example: "In my Selenium framework, I have a BasePage class with common methods like waitForElement() and clickElement(). All specific page classes (LoginPage, HomePage) extend BasePage, inheriting these utilities. This eliminates code duplication across 50+ page objects."*

---

**Q2:** Explain polymorphism with an example.
**A:**
Polymorphism means "many forms." It allows treating objects of different classes as objects of a common superclass. The actual method called is determined at runtime based on the object's actual type.

```java
Vehicle v1 = new Car();
Vehicle v2 = new Truck();

v1.start();  // Calls Car's start() method
v2.start();  // Calls Truck's start() method
```

*Example: "In my test framework, I have a method `executeTest(BasePage page)` that accepts any page object. At runtime, it correctly calls the overridden methods of LoginPage, HomePage, or CheckoutPage - whichever is actually passed in. This enables flexible, reusable test code."*

---

**Q3:** What's the difference between method overriding and method overloading?
**A:**

| Aspect | Overriding | Overloading |
|--------|------------|-------------|
| **Definition** | Child class redefines parent method | Same class, multiple methods with same name |
| **Signature** | Must be identical | Must differ (parameters) |
| **Inheritance** | Required | Not required |
| **Polymorphism** | Runtime polymorphism | Compile-time polymorphism |
| **Annotation** | `@Override` | None |

```java
// Overloading (same class)
public void login(String username, String password) { }
public void login(String username) { }

// Overriding (parent-child)
public class Parent {
    public void test() { }
}
public class Child extends Parent {
    @Override
    public void test() { }  // Same signature
}
```

---

**Q4:** What is the purpose of the `super` keyword?
**A:**
`super` is a reference to the parent class, used for:
1. Calling parent constructor: `super(args)`
2. Accessing parent methods: `super.methodName()`
3. Accessing parent fields: `super.fieldName`

```java
public class Car extends Vehicle {
    public Car(String brand) {
        super(brand);  // Call parent constructor
    }

    @Override
    public void start() {
        super.start();  // Call parent's start() first
        System.out.println("Car specific startup");
    }
}
```

*Example: "In my page objects, each constructor calls `super(driver)` to pass the WebDriver instance to BasePage, ensuring the parent class is properly initialized with the driver reference."*

---

**Q5:** Can you override a private or final method?
**A:**
- **Private methods:** Cannot be overridden because they're not inherited by child classes
- **Final methods:** Cannot be overridden - this is enforced by the compiler

```java
public class Parent {
    private void privateMethod() { }    // Not inherited
    public final void finalMethod() { } // Cannot override
}

public class Child extends Parent {
    // private void privateMethod() { }  // This is a NEW method, not override
    // public void finalMethod() { }     // ERROR: Cannot override final
}
```

*Example: "In my BasePage class, I mark the `waitForElement()` method as `final` to prevent subclasses from changing its behavior, ensuring consistent waiting logic across all page objects."*

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What's the difference between `extends` and `implements` in Java?**

2. **If a parent constructor has parameters, what must the child constructor do?**

3. **What will happen in this code?**
   ```java
   Vehicle v = new Car();
   Car c = v;  // Will this compile?
   ```

4. **Why should you always use `@Override` annotation when overriding methods?**

5. **Predict the output:**
   ```java
   class A {
       public void test() { System.out.println("A"); }
   }
   class B extends A {
       public void test() { System.out.println("B"); }
   }
   A obj = new B();
   obj.test();
   ```

**Answers in Practice Exercises file.**

---

## 🚀 Real-World Selenium Example

### Building a Page Object Hierarchy

```java
// BasePage.java - Parent class
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

// LoginPage.java - Child class
public class LoginPage extends BasePage {
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login-button");
    private By errorMessage = By.className("error");

    public LoginPage(WebDriver driver) {
        super(driver);  // Initialize BasePage
    }

    public void enterUsername(String username) {
        type(usernameField, username);  // Using inherited method!
    }

    public void enterPassword(String password) {
        type(passwordField, password);  // Using inherited method!
    }

    public void clickLogin() {
        click(loginButton);  // Using inherited method!
    }

    public String getErrorMessage() {
        return getText(errorMessage);  // Using inherited method!
    }

    // Convenience method
    public void login(String username, String password) {
        enterUsername(username);
        enterPassword(password);
        clickLogin();
    }
}

// HomePage.java - Another child class
public class HomePage extends BasePage {
    private By welcomeMessage = By.id("welcome");
    private By logoutButton = By.id("logout");

    public HomePage(WebDriver driver) {
        super(driver);
    }

    public String getWelcomeMessage() {
        return getText(welcomeMessage);  // Using inherited method!
    }

    public void logout() {
        click(logoutButton);  // Using inherited method!
    }
}

// Test using polymorphism
public class LoginTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        // Polymorphism: BasePage reference, LoginPage object
        BasePage loginPage = new LoginPage(driver);
        BasePage homePage = new HomePage(driver);

        // Using actual types
        LoginPage login = new LoginPage(driver);
        login.login("user@example.com", "password123");

        HomePage home = new HomePage(driver);
        System.out.println(home.getWelcomeMessage());

        driver.quit();
    }
}
```

**Benefits of this hierarchy:**
- No code duplication across page objects
- Consistent waiting and interaction logic
- Easy to add new page objects - just extend BasePage
- Changes to common functionality happen in one place

---

## 📝 Today's Mantra

> "Inheritance builds hierarchies, polymorphism makes them flexible. Together, they eliminate duplication and enable frameworks that grow gracefully. Master these concepts, and your Selenium code will be clean, maintainable, and professional."

---

## 🎯 Ready for Practice?

Now move to **Day-5-Practice-Exercises.md** to apply inheritance and polymorphism with hands-on coding exercises.

**Time spent on concepts:** ____ minutes
**Ready to code:** Yes / Review once more

---
