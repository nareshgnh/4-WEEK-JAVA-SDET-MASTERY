# DAY 4 CHEAT SHEET: OOP PART 1

## ⚡ Quick Syntax Reference

### Class Declaration
```java
public class ClassName {
    // Instance variables
    private dataType variableName;

    // Constructor
    public ClassName(parameters) {
        // initialization
    }

    // Methods
    public returnType methodName(parameters) {
        // method body
    }
}
```

### Creating Objects
```java
ClassName objectName = new ClassName(arguments);
```

### Constructor Syntax
```java
// Default constructor (no parameters)
public ClassName() {
    // initialization with default values
}

// Parameterized constructor
public ClassName(dataType param1, dataType param2) {
    this.variable1 = param1;
    this.variable2 = param2;
}

// Multiple constructors (constructor overloading)
public ClassName(String name) { }
public ClassName(String name, int age) { }
public ClassName(String name, int age, String address) { }
```

### Instance Methods
```java
// Method with no parameters, no return
public void methodName() {
    // code
}

// Method with parameters, returns value
public returnType methodName(dataType param1, dataType param2) {
    // code
    return value;
}

// Method that returns this (for chaining)
public ClassName methodName(parameters) {
    // code
    return this;
}
```

### Encapsulation Pattern
```java
public class MyClass {
    // Private instance variables
    private String name;
    private int age;

    // Constructor
    public MyClass(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Getter methods
    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    // Setter methods with validation
    public void setName(String name) {
        if (name != null && !name.isEmpty()) {
            this.name = name;
        }
    }

    public void setAge(int age) {
        if (age > 0 && age < 120) {
            this.age = age;
        }
    }
}
```

### Using `this` Keyword
```java
public class Example {
    private String value;

    // 1. Distinguish instance variable from parameter
    public Example(String value) {
        this.value = value;  // this.value = instance, value = parameter
    }

    // 2. Call another method of same object
    public void method1() {
        this.method2();  // calling another instance method
    }

    public void method2() {
        // implementation
    }

    // 3. Return current object for method chaining
    public Example setValue(String value) {
        this.value = value;
        return this;  // returns current object
    }
}
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Syntax | Python Equivalent |
|---------|-------------|-------------------|
| **Class Definition** | `public class Car { }` | `class Car:` |
| **Instance Variable** | `private String name;` | `self.name = ...` (in `__init__`) |
| **Constructor** | `public Car(String n) { this.name = n; }` | `def __init__(self, n): self.name = n` |
| **Create Object** | `Car c = new Car("Toyota");` | `c = Car("Toyota")` |
| **Instance Method** | `public void drive() { }` | `def drive(self):` |
| **Self Reference** | `this` (implicit in methods) | `self` (explicit parameter) |
| **Getter** | `public String getName() { return name; }` | `@property def name(self): return self._name` |
| **Setter** | `public void setName(String n) { name = n; }` | `@name.setter def name(self, n): self._name = n` |
| **Access Control** | `private`, `public` (enforced) | `_variable`, `__variable` (convention) |
| **Method Chaining** | `return this;` | `return self` |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **No `new` keyword** | `Car c = Car();` | `Car c = new Car();` |
| **Constructor name mismatch** | `public car() { }` in class `Car` | `public Car() { }` |
| **Missing `this`** | `name = name;` in constructor | `this.name = name;` |
| **Accessing private directly** | `obj.privateVar = 5;` | `obj.setPrivateVar(5);` |
| **Constructor has return type** | `public void Car() { }` | `public Car() { }` (no return type) |
| **Comparing objects with ==** | `if (obj1 == obj2)` | Use `.equals()` for content comparison |
| **Not initializing objects** | `Car c;` then `c.drive();` | `Car c = new Car();` then `c.drive();` |

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ Classes are blueprints, objects are instances created from those blueprints
- ✅ Constructors initialize objects when created with `new` keyword
- ✅ `this` keyword refers to the current object
- ✅ Encapsulation protects data with `private` variables and `public` methods
- ✅ Getters provide read access, setters provide controlled write access
- ✅ Instance variables belong to each object (separate copies)
- ✅ Instance methods operate on object's data
- ✅ Constructor overloading allows multiple ways to create objects
- ✅ Method chaining (returning `this`) makes code more readable
- ✅ Validation in setters protects data integrity

**Critical for Automation:**
- 🎯 Page Object Model uses classes to represent web pages
- 🎯 Encapsulation hides locators and exposes user actions
- 🎯 Constructors initialize WebDriver and page elements
- 🎯 `this` keyword is essential for method chaining in Selenium

---

## 📋 OOP Quick Reference Card

### Class Structure Template
```java
import java.util.ArrayList;

public class PageObject {
    // 1. Private instance variables
    private WebDriver driver;
    private String url;
    private ArrayList<String> actions;

    // 2. Constructor(s)
    public PageObject(WebDriver driver) {
        this.driver = driver;
        this.actions = new ArrayList<>();
    }

    // 3. Public methods (behaviors)
    public void navigateTo(String url) {
        driver.get(url);
        actions.add("Navigated to: " + url);
    }

    // 4. Getters
    public String getUrl() {
        return url;
    }

    public ArrayList<String> getActions() {
        return actions;
    }

    // 5. Setters (if needed)
    public void setUrl(String url) {
        if (url.startsWith("http")) {
            this.url = url;
        }
    }
}
```

### Access Modifiers Quick Guide
```java
public class Example {
    public String publicVar;      // Accessible from anywhere
    private String privateVar;    // Only within this class
    protected String protectedVar; // This class and subclasses (Day 5)
    String defaultVar;            // Same package (rarely used)
}
```

### Common Method Patterns

**1. Void Method (No Return)**
```java
public void performAction() {
    // do something
}
```

**2. Return Value Method**
```java
public String getValue() {
    return someValue;
}
```

**3. Method with Validation**
```java
public void setValue(String value) {
    if (value != null && !value.isEmpty()) {
        this.value = value;
    } else {
        System.out.println("Error: Invalid value");
    }
}
```

**4. Method Chaining Pattern**
```java
public PageObject enterUsername(String username) {
    // enter username
    return this;  // Enable chaining
}

// Usage: page.enterUsername("user").enterPassword("pass").clickLogin();
```

---

## 🎤 Interview Phrases

**When asked about OOP basics, say:**

*"In Java, a class is a blueprint that defines the structure and behavior of objects. An object is an instance of a class created using the `new` keyword. Classes contain instance variables (state) and methods (behavior)."*

**When asked about encapsulation, say:**

*"Encapsulation is hiding internal implementation details by making instance variables private and providing public getter and setter methods. This protects data integrity and allows validation. In test automation, we encapsulate page locators and expose only user actions."*

**When asked about constructors, say:**

*"Constructors are special methods that initialize objects when they're created. They have the same name as the class and no return type. In Selenium frameworks, constructors are used to initialize WebDriver and set up Page Objects."*

**When asked about `this` keyword, say:**

*"The `this` keyword refers to the current object instance. It's used to distinguish instance variables from parameters with the same name, call other instance methods, or return the current object for method chaining—a pattern commonly used in fluent Selenium APIs."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **A class is a blueprint (Page Object), an object is an instance (page in a test). Encapsulate data (private locators), expose behavior (public actions). Initialize in constructors, validate in setters, chain with `this`.**

---

## 🔮 Tomorrow's Preview

**Day 5 Topic:** OOP Part 2 (Inheritance, Polymorphism, Abstract Classes)

**What to review tonight:**
- How classes and objects work
- Constructors and `this` keyword
- Encapsulation pattern

**What to think about:**
- What if multiple classes share common properties? (Hint: Inheritance)
- Can different objects respond differently to the same method call? (Hint: Polymorphism)
- How can we create a template that other classes must follow? (Hint: Abstract classes)

**Connection to Automation:**
Tomorrow you'll learn how to create base page objects that all page classes inherit from, reducing code duplication and making frameworks more maintainable.

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [X] Learned Classes and Objects
- [X] Completed 6 practice exercises
- [X] Debugged 5 OOP challenges
- [X] Built BankAccount class project
- [X] 2.5-3 hours of Java OOP practice

**Cumulative Progress:**
- Days completed: 4/7
- Java concepts mastered: Basic syntax, control flow, arrays/strings, **OOP basics**
- Mini projects built: Calculator, Number guessing game, Anagram checker, **BankAccount**
- Lines of code written: ~300-400

**Momentum Statement:**
*"You've crossed a major milestone! OOP is the foundation of professional Java development. The Page Object Model you'll use in Selenium is pure OOP. Every concept learned today will be used daily in your SDET role. You're 57% through Week 1, and you've just unlocked the most important programming paradigm for automation frameworks. Tomorrow, you'll make these classes even more powerful with inheritance and polymorphism. The 25 LPA goal is getting closer—one class at a time!"*

---

## 📚 Quick Reference: Common Patterns

### Pattern 1: Simple Data Class
```java
public class TestData {
    private String username;
    private String password;

    public TestData(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public String getUsername() { return username; }
    public String getPassword() { return password; }
}
```

### Pattern 2: Page Object Pattern
```java
public class LoginPage {
    private WebDriver driver;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void login(String user, String pass) {
        // find elements and perform login
    }

    public boolean isLoginSuccessful() {
        // verification logic
        return true;
    }
}
```

### Pattern 3: Configuration Class
```java
public class Config {
    private String browser;
    private int timeout;

    public Config() {
        this.browser = "chrome";
        this.timeout = 10;
    }

    public String getBrowser() { return browser; }
    public void setBrowser(String browser) {
        if (browser != null) this.browser = browser;
    }

    public int getTimeout() { return timeout; }
    public void setTimeout(int timeout) {
        if (timeout > 0) this.timeout = timeout;
    }
}
```

### Pattern 4: Builder/Fluent Pattern
```java
public class TestBuilder {
    private String name;
    private String description;
    private String priority;

    public TestBuilder setName(String name) {
        this.name = name;
        return this;
    }

    public TestBuilder setDescription(String description) {
        this.description = description;
        return this;
    }

    public TestBuilder setPriority(String priority) {
        this.priority = priority;
        return this;
    }

    public Test build() {
        return new Test(name, description, priority);
    }
}

// Usage:
Test test = new TestBuilder()
    .setName("Login Test")
    .setDescription("Verify login functionality")
    .setPriority("High")
    .build();
```

---

## 🛠️ IntelliJ IDEA Shortcuts for OOP

| Action | Windows/Linux | Mac |
|--------|---------------|-----|
| Generate Constructor | Alt + Insert | Cmd + N |
| Generate Getters/Setters | Alt + Insert | Cmd + N |
| Refactor → Rename Class | Shift + F6 | Shift + F6 |
| Find Usages | Alt + F7 | Option + F7 |
| Go to Declaration | Ctrl + B | Cmd + B |
| Show Parameters | Ctrl + P | Cmd + P |
| Quick Documentation | Ctrl + Q | Ctrl + J |
| Override Methods | Ctrl + O | Ctrl + O |

---

## ✅ Day 4 Checklist

Before moving to Day 5, ensure you can:

- [ ] Define a class with instance variables
- [ ] Create objects using `new` keyword
- [ ] Write default and parameterized constructors
- [ ] Use `this` keyword correctly in constructors
- [ ] Make instance variables `private`
- [ ] Write getter methods for private variables
- [ ] Write setter methods with validation
- [ ] Create instance methods that operate on object data
- [ ] Implement method chaining by returning `this`
- [ ] Understand the difference between class and object
- [ ] Explain encapsulation and its benefits
- [ ] Debug common OOP errors (NullPointerException, missing `this`, etc.)
- [ ] Connect OOP concepts to Page Object Model

**If you checked all boxes:** You're ready for Day 5!
**If you missed some:** Review those sections in the Concept Guide

---

## 🎊 Congratulations!

You've mastered OOP Part 1! These concepts are the foundation of every professional Java automation framework. Tomorrow you'll build on this with inheritance and polymorphism.

**Keep the momentum going!** 🚀

---
