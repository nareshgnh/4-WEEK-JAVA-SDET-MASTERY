# DAY 5 CHEAT SHEET: INHERITANCE & POLYMORPHISM

## ⚡ Quick Syntax Reference

### Basic Inheritance
```java
// Parent class
public class Parent {
    protected String field;

    public Parent(String field) {
        this.field = field;
    }

    public void method() {
        System.out.println("Parent method");
    }
}

// Child class
public class Child extends Parent {
    private String childField;

    public Child(String field, String childField) {
        super(field);  // Call parent constructor FIRST
        this.childField = childField;
    }
}
```

### Method Overriding
```java
public class Parent {
    public void greet() {
        System.out.println("Hello from Parent");
    }
}

public class Child extends Parent {
    @Override  // Always use this annotation!
    public void greet() {
        super.greet();  // Optional: call parent's version
        System.out.println("Hello from Child");
    }
}
```

### Polymorphism
```java
// Parent reference, child object
Parent obj = new Child();
obj.greet();  // Calls Child's version (runtime polymorphism)

// Array of parent type holding different child objects
Parent[] objects = new Parent[3];
objects[0] = new Child1();
objects[1] = new Child2();
objects[2] = new Child3();

for (Parent p : objects) {
    p.method();  // Each calls its own overridden version
}
```

### Using super Keyword
```java
public class Child extends Parent {
    public Child() {
        super();  // Call parent's no-arg constructor
    }

    @Override
    public void method() {
        super.method();  // Call parent's method
        // Add child-specific code
    }

    public void accessParentField() {
        System.out.println(super.field);  // Access parent's field
    }
}
```

### Type Checking and Casting
```java
// Check type before casting
if (obj instanceof Car) {
    Car car = (Car) obj;  // Downcast (parent to child)
    car.carSpecificMethod();
}

// Upcast (automatic)
Car car = new Car();
Vehicle vehicle = car;  // Automatic upcast (child to parent)
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Syntax | Python Equivalent |
|---------|-------------|-------------------|
| **Inheritance** | `class Child extends Parent` | `class Child(Parent):` |
| **Constructor Call** | `super(args);` | `super().__init__(args)` |
| **Method Override** | `@Override public void method()` | `def method(self):` (no annotation) |
| **Parent Method** | `super.method();` | `super().method()` |
| **Type Check** | `obj instanceof Type` | `isinstance(obj, Type)` |
| **Downcast** | `(ChildType) parentObj` | Not needed (duck typing) |
| **Protected** | `protected String field;` | `_field` (by convention) |
| **Final Method** | `public final void method()` | No direct equivalent |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Missing super()** | `Child() { this.x = 5; }` | `Child() { super(); this.x = 5; }` |
| **No @Override** | `public void method()` | `@Override public void method()` |
| **Wrong signature** | `@Override public int method()` | `@Override public void method()` (match parent) |
| **Private parent field** | `private String x;` in parent | `protected String x;` in parent |
| **More restrictive** | `protected void method()` (parent is public) | `public void method()` |
| **Calling child method on parent ref** | `Parent p = new Child(); p.childMethod();` | Check type first with `instanceof` |

---

## 📊 Access Modifiers in Inheritance

| Modifier | Same Class | Child Class | Same Package | Other |
|----------|------------|-------------|--------------|-------|
| `private` | ✅ | ❌ | ❌ | ❌ |
| `default` | ✅ | ❌* | ✅ | ❌ |
| `protected` | ✅ | ✅ | ✅ | ❌ |
| `public` | ✅ | ✅ | ✅ | ✅ |

*Unless child is in same package

**For Inheritance, Use:**
- `protected` - Fields/methods you want child classes to access
- `private` - Internal implementation details
- `public` - Methods that are part of the public API

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ How to create class hierarchies with `extends`
- ✅ Constructor chaining with `super()`
- ✅ Method overriding with `@Override`
- ✅ Polymorphism for flexible code
- ✅ Access modifiers in inheritance context
- ✅ Type checking with `instanceof`
- ✅ Upcasting and downcasting

**Critical for Automation:**
- 🎯 BasePage inheritance eliminates code duplication
- 🎯 Polymorphism enables uniform page handling
- 🎯 Method overriding allows page-specific behavior
- 🎯 `super()` properly initializes WebDriver in base class

---

## 🎯 Method Overriding Rules

**Must Match:**
- ✅ Method name (exact)
- ✅ Parameters (type, number, order)
- ✅ Return type (same or covariant)

**Access Modifier:**
- ✅ Same or less restrictive
- ❌ Cannot be more restrictive

**Cannot Override:**
- ❌ `final` methods
- ❌ `static` methods (can hide them)
- ❌ `private` methods (not inherited)
- ❌ Constructors

**Best Practice:**
- ✅ Always use `@Override` annotation

---

## 🔄 Polymorphism Quick Guide

### Compile-Time Polymorphism (Method Overloading)
```java
public void print(int x) { }
public void print(String s) { }
public void print(int x, int y) { }
```
- Same method name, different parameters
- Decided at compile time

### Runtime Polymorphism (Method Overriding)
```java
Vehicle v = new Car();
v.start();  // Calls Car's start(), not Vehicle's
```
- Parent reference, child object
- Decided at runtime
- Enables flexible, extensible code

---

## 📌 super Keyword Usage

**Three Uses:**

1. **Call Parent Constructor:**
```java
public Child(String name) {
    super(name);  // MUST be first statement
}
```

2. **Call Parent Method:**
```java
@Override
public void method() {
    super.method();  // Call parent's version
    // Add child-specific code
}
```

3. **Access Parent Field:**
```java
public void display() {
    System.out.println(super.field);  // If child hides it
}
```

**Rules:**
- `super()` must be first statement in constructor
- If no explicit `super()`, Java calls no-arg parent constructor
- If parent has no no-arg constructor, must explicitly call `super(args)`

---

## 🎤 Interview Phrases

**When asked about inheritance, say:**

**On Inheritance:**
*"Inheritance is a mechanism where a child class acquires properties and behaviors of a parent class using the `extends` keyword. It promotes code reusability and establishes an 'is-a' relationship. In my Selenium framework, I use inheritance by creating a BasePage class with common methods like waitForElement() and clickElement(), which all specific page objects extend."*

**On Polymorphism:**
*"Polymorphism allows treating objects of different types uniformly through a common interface. In Java, this is achieved through method overriding and runtime method dispatch. For example, I can store different page objects (LoginPage, HomePage, CheckoutPage) in a BasePage array and call methods on them - each will execute its own overridden version. This enables writing flexible, maintainable test code."*

**On Method Overriding:**
*"Method overriding allows a child class to provide a specific implementation of a method already defined in its parent class. The method signature must match exactly, and I always use the @Override annotation for compile-time verification. This is essential in Page Object Model where different pages need different implementations of common methods like waitForPageLoad()."*

**On super Keyword:**
*"The super keyword provides a reference to the parent class. It's used to call parent constructors with super(), access parent methods with super.method(), or access parent fields. In Selenium frameworks, every page object constructor calls super(driver) to pass the WebDriver instance to the BasePage, ensuring proper initialization of the base class."*

**On Access Modifiers:**
*"I use protected for fields like WebDriver and WebDriverWait in BasePage so child page objects can access them. Private is used for internal helper methods that should not be accessible outside the class. Public is for methods that form the page's API that tests will call. This encapsulation ensures proper boundaries while enabling inheritance."*

---

## 📝 Code Patterns for Automation

### Pattern 1: BasePage with Common Methods
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
}
```

### Pattern 2: Page Object Extending BasePage
```java
public class LoginPage extends BasePage {
    private By usernameField = By.id("username");
    private By passwordField = By.id("password");
    private By loginButton = By.id("login");

    public LoginPage(WebDriver driver) {
        super(driver);  // Initialize BasePage
    }

    public void login(String username, String password) {
        type(usernameField, username);  // Inherited method
        type(passwordField, password);  // Inherited method
        click(loginButton);             // Inherited method
    }
}
```

### Pattern 3: Polymorphic Page Management
```java
public class TestBase {
    protected WebDriver driver;

    protected void navigateAllPages(BasePage... pages) {
        for (BasePage page : pages) {
            page.navigateTo();  // Each page has its own URL
            page.waitForPageLoad();  // Each page waits for different elements
        }
    }

    @Test
    public void testFlow() {
        navigateAllPages(
            new LoginPage(driver),
            new HomePage(driver),
            new CheckoutPage(driver)
        );  // Polymorphism!
    }
}
```

### Pattern 4: Type-Specific Actions
```java
public void performPageAction(BasePage page) {
    // Common action
    page.captureScreenshot();

    // Type-specific actions
    if (page instanceof LoginPage) {
        ((LoginPage) page).verifyLoginForm();
    } else if (page instanceof CheckoutPage) {
        ((CheckoutPage) page).calculateTotal();
    }
}
```

---

## 🔮 Tomorrow's Preview

**Day 6 Topic:** OOP Part 3 (Abstraction & Interfaces)

**What to review tonight:**
- How inheritance establishes "is-a" relationships
- Method overriding mechanism
- Polymorphism concepts
- The difference between concrete and abstract methods

**What to think about:**
*"Sometimes we want to define methods that MUST be implemented by child classes, but have no implementation in the parent. How could we enforce this?"*

**Connection to Day 5:**
- Inheritance → Abstraction (next level of OOP)
- Method overriding → Abstract methods (must override)
- Polymorphism → Interface polymorphism (more flexible)
- `extends` → `implements` (different relationship)

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [x] Learned inheritance with `extends`
- [x] Mastered method overriding
- [x] Understood polymorphism
- [x] Built Vehicle Hierarchy project
- [x] ~2.5-3 hours of OOP practice

**Cumulative Progress:**
- Days completed: 5/7
- Java concepts mastered: 35+
- Lines of code written: ~500+
- OOP understanding: Advanced

**Momentum Statement:**
*"Day 5 complete! You've mastered inheritance and polymorphism - the backbone of professional object-oriented design. These concepts power every major Java framework, including Selenium's Page Object Model. Tomorrow you'll learn abstraction and interfaces - the final pieces of the OOP puzzle. You're 71% through Week 1 and your Java SDET skills are accelerating!"*

---

## 📝 Quick Reference Card (Print This!)

```
╔═══════════════════════════════════════════════════════╗
║    INHERITANCE & POLYMORPHISM - DAY 5 REFERENCE       ║
╠═══════════════════════════════════════════════════════╣
║ INHERITANCE:                                          ║
║ public class Child extends Parent {                   ║
║     public Child() {                                  ║
║         super();  // Call parent constructor          ║
║     }                                                 ║
║ }                                                     ║
╠═══════════════════════════════════════════════════════╣
║ METHOD OVERRIDING:                                    ║
║ @Override                                             ║
║ public void method() {                                ║
║     super.method();  // Optional: call parent         ║
║     // Child-specific code                            ║
║ }                                                     ║
╠═══════════════════════════════════════════════════════╣
║ POLYMORPHISM:                                         ║
║ Parent obj = new Child();  // Parent ref, child obj  ║
║ obj.method();              // Calls Child's version  ║
╠═══════════════════════════════════════════════════════╣
║ TYPE CHECKING:                                        ║
║ if (obj instanceof Child) {                           ║
║     Child child = (Child) obj;  // Downcast           ║
║     child.childMethod();                              ║
║ }                                                     ║
╠═══════════════════════════════════════════════════════╣
║ ACCESS MODIFIERS:                                     ║
║ private   - NOT accessible in child                  ║
║ protected - ACCESSIBLE in child                      ║
║ public    - ACCESSIBLE everywhere                    ║
╠═══════════════════════════════════════════════════════╣
║ CRITICAL RULES:                                       ║
║ • super() must be first in constructor               ║
║ • Always use @Override annotation                    ║
║ • Match method signature exactly                     ║
║ • Cannot override final methods                      ║
║ • Cannot override private methods                    ║
║ • Cannot be more restrictive                         ║
╠═══════════════════════════════════════════════════════╣
║ SELENIUM APPLICATION:                                 ║
║ BasePage → Common methods (click, type, wait)        ║
║ LoginPage extends BasePage                           ║
║ HomePage extends BasePage                            ║
║ CheckoutPage extends BasePage                        ║
║                                                       ║
║ BasePage[] pages = {login, home, checkout};          ║
║ for (BasePage p : pages) {                            ║
║     p.navigateTo();  // Polymorphism!                ║
║ }                                                     ║
╚═══════════════════════════════════════════════════════╝
```

---

## 🎓 Key Relationships

### IS-A vs HAS-A

**IS-A (Inheritance):**
```java
class Car extends Vehicle { }
// Car IS-A Vehicle ✅
```

**HAS-A (Composition):**
```java
class Car {
    private Engine engine;  // Car HAS-A Engine ✅
}
```

**Rule of Thumb:**
- If you can say "X is a Y" → Use inheritance
- If you can say "X has a Y" → Use composition

---

## 🚀 You're Ready for Day 6!

**Prerequisites Checklist:**
- [x] Understand inheritance syntax
- [x] Know how to use super keyword
- [x] Can override methods correctly
- [x] Understand polymorphism
- [x] Completed Vehicle Hierarchy project

**Confidence Check:**
- Inheritance: ___/10
- Method Overriding: ___/10
- Polymorphism: ___/10
- Overall OOP understanding: ___/10

**If any rating is below 7:** Review the Concept Guide and practice exercises again.

**If all ratings are 7+:** You're ready for abstraction and interfaces! 🎉

---

## 💾 Save This Cheat Sheet

**Ways to use this:**
1. **Print it** - Keep next to keyboard while coding
2. **Bookmark it** - Quick reference during practice
3. **Review it** - Before Day 6 starts
4. **Test yourself** - Cover right column, recall syntax

**Pro Tip:** By Week 2, you should be writing inheritance hierarchies without looking at references!

---

**END OF DAY 5** ✅

Tomorrow: Day 6 - OOP Part 3 (Abstraction, Abstract Classes, Interfaces)

**You've mastered the core of OOP! Ready to level up?** 🚀

---
