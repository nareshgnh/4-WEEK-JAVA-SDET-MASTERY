# DAY 4: OOP PART 1 (Classes, Objects, Methods, Constructors)

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand classes as blueprints and objects as instances
- [ ] Create classes with instance variables and methods
- [ ] Master constructors (default and parameterized)
- [ ] Use the `this` keyword effectively
- [ ] Implement encapsulation with getters and setters
- [ ] Connect OOP concepts to Page Object Model pattern

**Time Required:** 2.5-3 hours
**Difficulty:** Medium
**Prerequisite:** Day 1-3 (Java basics, control flow, arrays)

---

## 📚 Core Concepts

### Concept 1: Classes and Objects

**What is it?**
A **class** is a blueprint or template that defines the structure and behavior of objects. An **object** is an instance (concrete realization) of a class. Think of a class as a cookie cutter and objects as the actual cookies made from it.

**Why does it matter for automation?**
In Selenium, the Page Object Model (POM) pattern uses classes to represent web pages. Each page class contains WebElements (properties) and actions (methods). Understanding classes/objects is fundamental to building maintainable automation frameworks.

**Syntax:**
```java
// Class definition (blueprint)
public class Car {
    // Instance variables (attributes/properties)
    String brand;
    String model;
    int year;

    // Instance method (behavior)
    public void displayInfo() {
        System.out.println(year + " " + brand + " " + model);
    }
}

// Creating objects (instances)
public class Main {
    public static void main(String[] args) {
        // Create first object
        Car car1 = new Car();
        car1.brand = "Toyota";
        car1.model = "Camry";
        car1.year = 2024;
        car1.displayInfo();  // Output: 2024 Toyota Camry

        // Create second object
        Car car2 = new Car();
        car2.brand = "Honda";
        car2.model = "Accord";
        car2.year = 2023;
        car2.displayInfo();  // Output: 2023 Honda Accord
    }
}
```

**Explanation:**
- `public class Car` - Defines a class named Car
- `String brand;` - Instance variable (each object has its own copy)
- `public void displayInfo()` - Instance method (behavior)
- `new Car()` - Creates a new object in memory
- `car1`, `car2` - References to different objects
- Each object has its own set of instance variables

**Real Example for Automation:**
```java
public class LoginPage {
    // Instance variables (page elements)
    String usernameField = "input#username";
    String passwordField = "input#password";
    String loginButton = "button[type='submit']";

    // Instance method (page action)
    public void login(String username, String password) {
        System.out.println("Entering username: " + username);
        System.out.println("Entering password: " + password);
        System.out.println("Clicking login button");
    }
}

// Using the page object
public class TestLogin {
    public static void main(String[] args) {
        LoginPage loginPage = new LoginPage();
        loginPage.login("standard_user", "secret_sauce");
    }
}
```

---

### Concept 2: Constructors

**What is it?**
A **constructor** is a special method that is automatically called when an object is created. It initializes the object's state. Constructors have the same name as the class and no return type.

**Why does it matter for automation?**
Constructors are used to initialize WebDriver, set up page objects, configure test data, and prepare test environment. Every Selenium test framework uses constructors extensively.

**Syntax:**
```java
public class Student {
    String name;
    int age;
    String grade;

    // Default Constructor (no parameters)
    public Student() {
        name = "Unknown";
        age = 0;
        grade = "Not Assigned";
    }

    // Parameterized Constructor
    public Student(String studentName, int studentAge) {
        name = studentName;
        age = studentAge;
        grade = "A";
    }

    // Constructor with all parameters
    public Student(String studentName, int studentAge, String studentGrade) {
        name = studentName;
        age = studentAge;
        grade = studentGrade;
    }

    public void displayInfo() {
        System.out.println("Name: " + name + ", Age: " + age + ", Grade: " + grade);
    }
}

// Using different constructors
public class Main {
    public static void main(String[] args) {
        Student s1 = new Student();                          // Default constructor
        Student s2 = new Student("Alice", 20);               // 2-parameter constructor
        Student s3 = new Student("Bob", 22, "B+");           // 3-parameter constructor

        s1.displayInfo();  // Output: Name: Unknown, Age: 0, Grade: Not Assigned
        s2.displayInfo();  // Output: Name: Alice, Age: 20, Grade: A
        s3.displayInfo();  // Output: Name: Bob, Age: 22, Grade: B+
    }
}
```

**Explanation:**
- Constructor has same name as class (`Student`)
- No return type (not even `void`)
- Can have multiple constructors with different parameters (constructor overloading)
- If you don't define a constructor, Java provides a default no-argument constructor
- Constructors run automatically when you use `new` keyword

**Common Use Cases in Test Automation:**
1. **Page Object initialization:**
```java
public class LoginPage {
    WebDriver driver;

    // Constructor receives WebDriver instance
    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }
}
```

2. **Test data setup:**
```java
public class TestData {
    String baseURL;
    String browser;

    public TestData(String environment) {
        if (environment.equals("QA")) {
            baseURL = "https://qa.example.com";
        } else {
            baseURL = "https://prod.example.com";
        }
        browser = "chrome";
    }
}
```

3. **Test configuration:**
```java
public class TestConfig {
    int timeout;
    boolean headless;

    public TestConfig() {
        timeout = 10;      // default values
        headless = false;
    }

    public TestConfig(int timeout, boolean headless) {
        this.timeout = timeout;
        this.headless = headless;
    }
}
```

---

### Concept 3: The `this` Keyword

**What is it?**
The `this` keyword is a reference to the current object. It's used to distinguish between instance variables and parameters with the same name, and to pass the current object as a parameter.

**Why does it matter for automation?**
In page objects and test classes, `this` is crucial for clarity when initializing objects and chaining method calls (fluent pattern in Selenium).

**Syntax:**
```java
public class Employee {
    String name;
    int id;
    double salary;

    // Constructor - this.variable refers to instance variable
    public Employee(String name, int id, double salary) {
        this.name = name;        // this.name (instance) = name (parameter)
        this.id = id;
        this.salary = salary;
    }

    // Method using this to call another method
    public void displayBasicInfo() {
        System.out.println("ID: " + this.id + ", Name: " + this.name);
    }

    public void displayFullInfo() {
        this.displayBasicInfo();  // calling another method of same object
        System.out.println("Salary: $" + this.salary);
    }
}
```

**Without `this` (ambiguous):**
```java
public class Employee {
    String name;

    public Employee(String name) {
        name = name;  // Which name? Confusing! Both refer to parameter
    }
}
```

**With `this` (clear):**
```java
public class Employee {
    String name;

    public Employee(String name) {
        this.name = name;  // this.name = instance variable, name = parameter
    }
}
```

**Real Example for Automation:**
```java
public class LoginPage {
    WebDriver driver;
    String url;

    public LoginPage(WebDriver driver, String url) {
        this.driver = driver;  // Clearly assigns parameter to instance variable
        this.url = url;
        this.driver.get(this.url);
    }

    public LoginPage enterUsername(String username) {
        // find element and enter username
        return this;  // Returns current object for method chaining
    }

    public LoginPage enterPassword(String password) {
        // find element and enter password
        return this;  // Returns current object for method chaining
    }

    public void clickLogin() {
        // click login button
    }
}

// Method chaining using 'this'
LoginPage page = new LoginPage(driver, "https://example.com");
page.enterUsername("user@test.com")
    .enterPassword("password123")
    .clickLogin();
```

---

### Concept 4: Methods (Instance Methods)

**What is it?**
Methods are functions defined inside a class that describe the behaviors/actions an object can perform. Instance methods operate on instance variables and can access other instance methods.

**Syntax:**
```java
// Method structure
accessModifier returnType methodName(parameters) {
    // method body
    return value;  // if returnType is not void
}

public class Calculator {
    int result;

    // Method with no parameters, no return value
    public void reset() {
        result = 0;
    }

    // Method with parameters, returns value
    public int add(int a, int b) {
        result = a + b;
        return result;
    }

    // Method with parameters, no return value
    public void displayResult() {
        System.out.println("Result: " + result);
    }

    // Method that calls another method
    public int addAndDisplay(int a, int b) {
        int sum = add(a, b);
        displayResult();
        return sum;
    }
}
```

**Method Types:**

1. **No parameters, no return value:**
```java
public void printWelcome() {
    System.out.println("Welcome!");
}
```

2. **With parameters, no return value:**
```java
public void greet(String name) {
    System.out.println("Hello, " + name);
}
```

3. **No parameters, returns value:**
```java
public int getCurrentYear() {
    return 2024;
}
```

4. **With parameters, returns value:**
```java
public double calculateArea(double length, double width) {
    return length * width;
}
```

**Common Use Cases in Test Automation:**
```java
public class HomePage {
    WebDriver driver;

    // Method to navigate to page
    public void navigateTo(String url) {
        driver.get(url);
    }

    // Method that returns element status
    public boolean isLoginButtonDisplayed() {
        // check if element is displayed
        return true;
    }

    // Method that returns page title
    public String getPageTitle() {
        return driver.getTitle();
    }

    // Method with multiple parameters
    public void login(String username, String password) {
        // enter credentials and click login
    }
}
```

---

### Concept 5: Encapsulation (Private Variables + Getters/Setters)

**What is it?**
**Encapsulation** is the practice of hiding internal implementation details and exposing only what's necessary through public methods. We make instance variables `private` and provide `public` getter/setter methods to access them.

**Why does it matter for automation?**
Encapsulation protects test data, ensures validation, and makes frameworks more maintainable. You can change internal implementation without breaking existing test code.

**Syntax:**
```java
public class BankAccount {
    // Private instance variables (hidden from outside)
    private String accountNumber;
    private String accountHolder;
    private double balance;

    // Constructor
    public BankAccount(String accountNumber, String accountHolder, double initialBalance) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = initialBalance;
    }

    // Getter methods (read access)
    public String getAccountNumber() {
        return accountNumber;
    }

    public String getAccountHolder() {
        return accountHolder;
    }

    public double getBalance() {
        return balance;
    }

    // Setter methods with validation (controlled write access)
    public void setAccountHolder(String accountHolder) {
        if (accountHolder != null && !accountHolder.isEmpty()) {
            this.accountHolder = accountHolder;
        }
    }

    // Method to modify balance with business logic
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            System.out.println("Deposited: $" + amount);
        } else {
            System.out.println("Invalid deposit amount");
        }
    }

    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            System.out.println("Withdrawn: $" + amount);
        } else {
            System.out.println("Invalid withdrawal amount or insufficient funds");
        }
    }
}

// Using encapsulated class
public class Main {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("ACC123", "John Doe", 1000.0);

        // Can't access private variables directly
        // account.balance = -500;  // ERROR: balance has private access

        // Must use public methods
        System.out.println("Balance: $" + account.getBalance());  // 1000.0
        account.deposit(500);        // Deposited: $500
        account.withdraw(200);       // Withdrawn: $200
        System.out.println("Balance: $" + account.getBalance());  // 1300.0
        account.withdraw(2000);      // Invalid withdrawal - insufficient funds
    }
}
```

**Benefits of Encapsulation:**
1. **Data Protection**: Cannot set invalid values directly
2. **Validation**: Setters can validate input before setting values
3. **Flexibility**: Can change internal implementation without affecting users
4. **Read-only/Write-only**: Can provide only getters (read-only) or only setters (write-only)

**Real Example for Automation:**
```java
public class TestConfig {
    private String baseURL;
    private String browser;
    private int timeout;

    public TestConfig() {
        this.baseURL = "https://qa.example.com";
        this.browser = "chrome";
        this.timeout = 10;
    }

    // Getter methods
    public String getBaseURL() {
        return baseURL;
    }

    public String getBrowser() {
        return browser;
    }

    public int getTimeout() {
        return timeout;
    }

    // Setter with validation
    public void setBaseURL(String baseURL) {
        if (baseURL.startsWith("http://") || baseURL.startsWith("https://")) {
            this.baseURL = baseURL;
        } else {
            System.out.println("Invalid URL format");
        }
    }

    public void setBrowser(String browser) {
        String[] validBrowsers = {"chrome", "firefox", "edge", "safari"};
        for (String valid : validBrowsers) {
            if (valid.equalsIgnoreCase(browser)) {
                this.browser = browser.toLowerCase();
                return;
            }
        }
        System.out.println("Invalid browser. Using default: chrome");
    }

    public void setTimeout(int timeout) {
        if (timeout > 0 && timeout <= 60) {
            this.timeout = timeout;
        } else {
            System.out.println("Timeout must be between 1-60 seconds");
        }
    }
}
```

---

## 🐍 Python vs Java Comparison

### Classes in Python (What You Know)
```python
# Python class
class Car:
    def __init__(self, brand, model, year):  # Constructor
        self.brand = brand      # Instance variables
        self.model = model
        self.year = year

    def display_info(self):     # Instance method
        print(f"{self.year} {self.brand} {self.model}")

# Creating object
car1 = Car("Toyota", "Camry", 2024)
car1.display_info()

# Can access/modify instance variables directly
car1.brand = "Honda"  # No protection
car1.year = -2024     # Can set invalid values
```

### Classes in Java (What You're Learning)
```java
// Java class
public class Car {
    private String brand;   // Private instance variables
    private String model;
    private int year;

    // Constructor
    public Car(String brand, String model, int year) {
        this.brand = brand;
        this.model = model;
        this.year = year;
    }

    // Instance method
    public void displayInfo() {
        System.out.println(year + " " + brand + " " + model);
    }

    // Getters
    public String getBrand() { return brand; }
    public String getModel() { return model; }
    public int getYear() { return year; }

    // Setters with validation
    public void setYear(int year) {
        if (year > 1900 && year <= 2025) {
            this.year = year;
        }
    }
}

// Creating object
Car car1 = new Car("Toyota", "Camry", 2024);
car1.displayInfo();

// Cannot access private variables directly
// car1.brand = "Honda";  // ERROR: brand has private access

// Must use public methods
car1.setYear(2023);      // Validated
String brand = car1.getBrand();
```

**Key Differences:**
| Aspect | Python | Java |
|--------|--------|------|
| **Constructor** | `__init__(self, ...)` | `ClassName(...)` |
| **Self Reference** | `self` (explicit parameter) | `this` (implicit) |
| **Access Control** | Convention (`_variable`) | Enforced (`private`) |
| **Encapsulation** | Optional, not enforced | Strongly encouraged |
| **Method Definition** | `def method_name(self):` | `public void methodName()` |
| **Creating Objects** | `obj = ClassName()` | `ClassName obj = new ClassName();` |

**Mental Model Shift:**
Python trusts developers to follow conventions ("we're all adults here"). Java enforces rules at compile-time. For large automation frameworks with multiple team members, Java's enforcement prevents accidental bugs and makes code more maintainable.

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Forgetting `new` Keyword When Creating Objects
❌ **Wrong:**
```java
Car myCar = Car();  // Missing 'new'
```

✅ **Correct:**
```java
Car myCar = new Car();  // Correct - using 'new'
```

**Why it matters:** In Java, `new` allocates memory for the object and calls the constructor. Without it, you'll get a compilation error.

---

### Mistake 2: Constructor Name Doesn't Match Class Name
❌ **Wrong:**
```java
public class Student {
    String name;

    public Students(String name) {  // Constructor name is Students, class is Student
        this.name = name;
    }
}
```

✅ **Correct:**
```java
public class Student {
    String name;

    public Student(String name) {  // Constructor name matches class name
        this.name = name;
    }
}
```

**Why it matters:** Constructor must have the exact same name as the class (case-sensitive). If different, Java treats it as a regular method.

---

### Mistake 3: Accessing Private Variables Directly
❌ **Wrong:**
```java
public class BankAccount {
    private double balance;
}

public class Main {
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        account.balance = 1000;  // ERROR: balance has private access
    }
}
```

✅ **Correct:**
```java
public class BankAccount {
    private double balance;

    public void setBalance(double balance) {
        this.balance = balance;
    }

    public double getBalance() {
        return balance;
    }
}

public class Main {
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        account.setBalance(1000);  // Correct - using setter
        System.out.println(account.getBalance());
    }
}
```

**Why it matters:** Private variables enforce encapsulation. Must use public getters/setters to access them.

---

### Mistake 4: Not Using `this` in Constructor When Parameter Names Match
❌ **Wrong:**
```java
public class Student {
    String name;

    public Student(String name) {
        name = name;  // Both refer to parameter, instance variable unchanged!
    }
}
```

✅ **Correct:**
```java
public class Student {
    String name;

    public Student(String name) {
        this.name = name;  // this.name (instance) = name (parameter)
    }
}
```

**Why it matters:** Without `this`, you're assigning the parameter to itself. Instance variable remains uninitialized (null for objects, 0 for numbers).

---

### Mistake 5: Modifying Private Variables Without Validation
❌ **Wrong:**
```java
public class Student {
    private int age;

    public void setAge(int age) {
        this.age = age;  // No validation - can set negative age!
    }
}
```

✅ **Correct:**
```java
public class Student {
    private int age;

    public void setAge(int age) {
        if (age > 0 && age < 120) {
            this.age = age;
        } else {
            System.out.println("Invalid age");
        }
    }
}
```

**Why it matters:** One of the main benefits of encapsulation is validating data before setting it. Always add validation in setters.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use IDE shortcuts to generate constructors and getters/setters**
- IntelliJ IDEA: Right-click → Generate (Alt+Insert) → Constructor/Getter/Setter
- This saves time and prevents typos

**Tip 2: Page Object Model is just OOP in action**
```java
// Each page is a class
public class LoginPage {
    private WebDriver driver;  // Instance variable

    // Constructor initializes the page
    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    // Methods represent user actions
    public void login(String username, String password) {
        // implementation
    }

    // Methods can return other page objects
    public HomePage clickLogin() {
        // click login
        return new HomePage(driver);  // Returns next page
    }
}
```

**Tip 3: Name methods as actions for better readability**
- Good: `clickLoginButton()`, `enterUsername()`, `verifyWelcomeMessage()`
- Bad: `button()`, `username()`, `check()`

**Tip 4: Use constructor overloading for flexibility**
```java
public class TestConfig {
    private String url;
    private String browser;

    // Default configuration
    public TestConfig() {
        this.url = "https://qa.example.com";
        this.browser = "chrome";
    }

    // Custom configuration
    public TestConfig(String url, String browser) {
        this.url = url;
        this.browser = browser;
    }
}
```

---

## 🔍 Deep Dive: Page Object Model Pattern

**What experienced developers know:**
The Page Object Model (POM) is the industry standard for organizing Selenium tests. It's built entirely on OOP principles you're learning today.

**Example:**
```java
// LoginPage.java - A class representing the login page
public class LoginPage {
    // Private instance variables (WebElements)
    private WebDriver driver;
    private String usernameLocator = "input#username";
    private String passwordLocator = "input#password";
    private String loginButtonLocator = "button#login";

    // Constructor - initializes the page object
    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    // Methods represent user actions
    public void enterUsername(String username) {
        driver.findElement(By.cssSelector(usernameLocator)).sendKeys(username);
    }

    public void enterPassword(String password) {
        driver.findElement(By.cssSelector(passwordLocator)).sendKeys(password);
    }

    public void clickLoginButton() {
        driver.findElement(By.cssSelector(loginButtonLocator)).click();
    }

    // High-level method combining actions
    public HomePage login(String username, String password) {
        enterUsername(username);
        enterPassword(password);
        clickLoginButton();
        return new HomePage(driver);  // Returns next page object
    }

    // Getter for verification
    public String getPageTitle() {
        return driver.getTitle();
    }
}

// Test class using the Page Object
public class LoginTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://example.com");

        // Create page object
        LoginPage loginPage = new LoginPage(driver);

        // Use page object methods
        HomePage homePage = loginPage.login("user@test.com", "password123");

        // Clean and readable test code!
    }
}
```

**When to use this:** Starting Week 2 when you learn Selenium! Today's OOP concepts are the foundation.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Class** | Blueprint/template for creating objects | `public class Car {}` |
| **Object** | Instance of a class | `Car myCar = new Car();` |
| **Instance Variable** | Variable belonging to an object | `private String name;` |
| **Constructor** | Special method to initialize objects | `public Car() {}` |
| **this** | Reference to current object | `this.name = name;` |
| **Encapsulation** | Hiding internal details, exposing interface | `private` variables + `public` getters/setters |
| **Getter** | Method to read private variable | `public String getName() { return name; }` |
| **Setter** | Method to modify private variable | `public void setName(String name) { this.name = name; }` |
| **Instance Method** | Method that operates on object's data | `public void displayInfo() {}` |
| **Constructor Overloading** | Multiple constructors with different parameters | Different versions of `public Car(...)` |

---

## 🎓 Interview Prep

**Common Interview Questions on OOP Basics:**

**Q1:** What is the difference between a class and an object?
**A:**
- **Class** is a blueprint or template that defines structure and behavior
- **Object** is an instance (concrete realization) of a class
- One class can create many objects

```java
// Class (blueprint)
public class Car {
    String brand;
    void drive() { }
}

// Objects (instances)
Car car1 = new Car();  // First instance
Car car2 = new Car();  // Second instance - separate object
```

*Example: "In my Selenium framework, I have a LoginPage class (blueprint) that defines login page structure. I can create multiple LoginPage objects for different test scenarios, each with its own WebDriver instance."*

---

**Q2:** What is a constructor and why is it important?
**A:**
A constructor is a special method that:
- Has the same name as the class
- Has no return type
- Is automatically called when an object is created
- Initializes the object's state

```java
public class WebDriverManager {
    WebDriver driver;

    // Constructor initializes WebDriver
    public WebDriverManager(String browser) {
        if (browser.equals("chrome")) {
            this.driver = new ChromeDriver();
        } else {
            this.driver = new FirefoxDriver();
        }
    }
}
```

*Example: "Constructors are crucial in test automation for initializing WebDriver, setting up page objects, and configuring test environment before running tests."*

---

**Q3:** What is encapsulation and why is it important in automation frameworks?
**A:**
Encapsulation is hiding internal implementation details and exposing only necessary functionality through public methods.

**Benefits:**
1. **Data Protection**: Cannot set invalid values
2. **Maintainability**: Can change internals without breaking tests
3. **Validation**: Control how data is accessed/modified

```java
public class TestConfig {
    private String baseURL;  // Hidden

    // Controlled access with validation
    public void setBaseURL(String url) {
        if (url.startsWith("https://")) {
            this.baseURL = url;
        }
    }

    public String getBaseURL() {
        return baseURL;
    }
}
```

*Example: "In our framework, we encapsulate WebDriver initialization logic. Tests don't need to know how WebDriver is created—they just call getDriver() method. If we switch from ChromeDriver to RemoteWebDriver, no test code changes."*

---

**Q4:** Explain the `this` keyword with an example.
**A:**
`this` is a reference to the current object. Used to:
1. Distinguish instance variables from parameters
2. Call other constructors
3. Pass current object as parameter

```java
public class LoginPage {
    private WebDriver driver;

    public LoginPage(WebDriver driver) {
        this.driver = driver;  // this.driver (instance) = driver (parameter)
    }

    public LoginPage enterUsername(String username) {
        // enter username
        return this;  // Return current object for method chaining
    }
}
```

---

**Q5:** What is the difference between instance variables and local variables?
**A:**
```java
public class Example {
    private int instanceVar = 10;  // Instance variable (class level)

    public void method() {
        int localVar = 20;  // Local variable (method level)
    }
}
```

| Aspect | Instance Variable | Local Variable |
|--------|------------------|----------------|
| **Scope** | Entire class | Only inside method/block |
| **Lifetime** | As long as object exists | Until method completes |
| **Default Value** | Yes (0, null, false) | No default value |
| **Access** | Can use access modifiers | No access modifiers |

*Example: "In page objects, WebDriver is an instance variable (shared across all methods), while element locator strings might be local variables (used only in specific methods)."*

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What happens if you don't define any constructor in a class?**

2. **Why do we make instance variables `private` and provide `public` getters/setters?**

3. **What's the difference between these two constructors?**
   ```java
   public Student(String name) {
       name = name;
   }

   public Student(String name) {
       this.name = name;
   }
   ```

4. **How does the Page Object Model pattern use OOP concepts?**

5. **Predict the output:**
   ```java
   public class Counter {
       private int count = 0;

       public void increment() {
           count++;
       }

       public int getCount() {
           return count;
       }
   }

   public class Main {
       public static void main(String[] args) {
           Counter c1 = new Counter();
           Counter c2 = new Counter();
           c1.increment();
           c1.increment();
           c2.increment();
           System.out.println(c1.getCount());
           System.out.println(c2.getCount());
       }
   }
   ```

**Answers in Practice Exercises file.**

---

## 📝 Today's Mantra

> "A class is a promise, an object is the fulfillment. Encapsulation is protection, methods are actions. Master these, and you'll master Selenium's Page Object Model."

---

## 🎯 Ready for Practice?

Now move to **Day-4-Practice-Exercises.md** to build real classes and objects.

**Time spent on concepts:** ____ minutes
**Concepts understood:** Yes / Need review
**Ready for OOP coding:** Let's go!

---
