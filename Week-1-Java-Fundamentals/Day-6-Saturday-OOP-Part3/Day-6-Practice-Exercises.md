# DAY 6 PRACTICE EXERCISES

## 🎯 Today's Practice Goal

Master abstraction principles through hands-on coding with abstract classes and interfaces. By completing these exercises, you'll understand when to use interfaces vs abstract classes and how they make code more flexible and maintainable.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Create Your First Interface

**Objective:** Learn interface syntax and implementation

**Task:**
Create an interface `Printable` with a method `print()`. Create two classes `Document` and `Image` that implement this interface. Each should print a different message.

**Starter Code:**
```java
// Define the interface
interface Printable {
    // TODO: Add print() method
}

// Implement in Document class
class Document implements Printable {
    private String content;

    public Document(String content) {
        this.content = content;
    }

    // TODO: Implement print() method
}

// Implement in Image class
class Image implements Printable {
    private String filename;

    public Image(String filename) {
        this.filename = filename;
    }

    // TODO: Implement print() method
}

public class Exercise1 {
    public static void main(String[] args) {
        Printable doc = new Document("Important Report");
        Printable img = new Image("photo.jpg");

        doc.print();
        img.print();
    }
}
```

**Expected Output:**
```
Printing document: Important Report
Printing image: photo.jpg
```

**Hints:**
- Interface methods are automatically public and abstract
- Remember to use `@Override` annotation
- Both classes must implement the print() method

---

### Exercise 2: Create an Abstract Class

**Objective:** Understand abstract classes with both abstract and concrete methods

**Task:**
Create an abstract class `Employee` with:
- Fields: `name` and `id`
- Constructor to initialize fields
- Concrete method: `displayInfo()` to print name and id
- Abstract method: `calculateSalary()` that returns double

Create two concrete classes `FullTimeEmployee` and `ContractEmployee` that extend Employee and implement calculateSalary differently.

**Starter Code:**
```java
abstract class Employee {
    // TODO: Add fields name and id

    // TODO: Add constructor

    // TODO: Add concrete method displayInfo()

    // TODO: Add abstract method calculateSalary()
}

class FullTimeEmployee extends Employee {
    private double monthlySalary;

    public FullTimeEmployee(String name, int id, double monthlySalary) {
        // TODO: Call super constructor
        // TODO: Initialize monthlySalary
    }

    // TODO: Implement calculateSalary() - return monthly salary
}

class ContractEmployee extends Employee {
    private double hourlyRate;
    private int hoursWorked;

    public ContractEmployee(String name, int id, double hourlyRate, int hoursWorked) {
        // TODO: Call super constructor
        // TODO: Initialize hourlyRate and hoursWorked
    }

    // TODO: Implement calculateSalary() - return hourlyRate * hoursWorked
}

public class Exercise2 {
    public static void main(String[] args) {
        Employee emp1 = new FullTimeEmployee("Alice", 101, 5000);
        Employee emp2 = new ContractEmployee("Bob", 102, 50, 160);

        emp1.displayInfo();
        System.out.println("Salary: $" + emp1.calculateSalary());

        emp2.displayInfo();
        System.out.println("Salary: $" + emp2.calculateSalary());
    }
}
```

**Expected Output:**
```
Employee: Alice, ID: 101
Salary: $5000.0
Employee: Bob, ID: 102
Salary: $8000.0
```

**Hints:**
- Abstract class can have constructor
- Use `super()` to call parent constructor
- Abstract methods must be implemented in concrete classes

---

### Exercise 3: Multiple Interfaces

**Objective:** Implement multiple interfaces in a single class

**Task:**
Create two interfaces `Flyable` (with method `fly()`) and `Swimmable` (with method `swim()`). Create a class `Duck` that implements both interfaces.

**Starter Code:**
```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {
    private String name;

    public Duck(String name) {
        this.name = name;
    }

    // TODO: Implement fly() method

    // TODO: Implement swim() method
}

public class Exercise3 {
    public static void main(String[] args) {
        Duck duck = new Duck("Donald");
        duck.fly();
        duck.swim();

        // Can reference by either interface
        Flyable flyingBird = duck;
        flyingBird.fly();

        Swimmable swimmer = duck;
        swimmer.swim();
    }
}
```

**Expected Output:**
```
Donald is flying in the sky
Donald is swimming in the pond
Donald is flying in the sky
Donald is swimming in the pond
```

**Hints:**
- Separate multiple interfaces with commas
- Implement all methods from all interfaces
- Same object can be referenced by different interface types

---

## 💪 Core Practice (30 min)

### Exercise 4: Payment System with Interfaces

**Objective:** Design a flexible payment system using interfaces

**Problem Statement:**
You're building a payment processing system for an e-commerce site. Different payment methods (Credit Card, PayPal, Bitcoin) should be interchangeable.

**Requirements:**
- Create interface `PaymentMethod` with methods:
  - `void processPayment(double amount)`
  - `String getPaymentType()`
- Create three classes implementing this interface:
  - `CreditCardPayment` - stores card number (last 4 digits only)
  - `PayPalPayment` - stores email
  - `BitcoinPayment` - stores wallet address
- Create a class `PaymentProcessor` with method `executePayment(PaymentMethod payment, double amount)` that works with any payment method

**Starter Code:**
```java
interface PaymentMethod {
    // TODO: Add processPayment(double amount) method
    // TODO: Add getPaymentType() method
}

class CreditCardPayment implements PaymentMethod {
    private String last4Digits;

    public CreditCardPayment(String last4Digits) {
        this.last4Digits = last4Digits;
    }

    @Override
    public void processPayment(double amount) {
        // TODO: Print processing credit card payment with last 4 digits
    }

    @Override
    public String getPaymentType() {
        return "Credit Card";
    }
}

class PayPalPayment implements PaymentMethod {
    private String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    @Override
    public void processPayment(double amount) {
        // TODO: Print processing PayPal payment with email
    }

    @Override
    public String getPaymentType() {
        return "PayPal";
    }
}

class BitcoinPayment implements PaymentMethod {
    private String walletAddress;

    public BitcoinPayment(String walletAddress) {
        this.walletAddress = walletAddress;
    }

    @Override
    public void processPayment(double amount) {
        // TODO: Print processing Bitcoin payment with wallet address
    }

    @Override
    public String getPaymentType() {
        return "Bitcoin";
    }
}

class PaymentProcessor {
    public void executePayment(PaymentMethod payment, double amount) {
        System.out.println("Starting payment processing...");
        System.out.println("Payment Type: " + payment.getPaymentType());
        payment.processPayment(amount);
        System.out.println("Payment completed!\n");
    }
}

public class Exercise4 {
    public static void main(String[] args) {
        PaymentProcessor processor = new PaymentProcessor();

        PaymentMethod creditCard = new CreditCardPayment("1234");
        PaymentMethod paypal = new PayPalPayment("user@example.com");
        PaymentMethod bitcoin = new BitcoinPayment("1A2B3C4D5E");

        processor.executePayment(creditCard, 150.00);
        processor.executePayment(paypal, 75.50);
        processor.executePayment(bitcoin, 200.00);
    }
}
```

**Test Cases:**
```
Input: CreditCardPayment("1234"), amount = 150.00
Expected Output:
Starting payment processing...
Payment Type: Credit Card
Processing Credit Card payment of $150.0 (Card: ****1234)
Payment completed!

Input: PayPalPayment("user@example.com"), amount = 75.50
Expected Output:
Starting payment processing...
Payment Type: PayPal
Processing PayPal payment of $75.5 to user@example.com
Payment completed!

Input: BitcoinPayment("1A2B3C4D5E"), amount = 200.00
Expected Output:
Starting payment processing...
Payment Type: Bitcoin
Processing Bitcoin payment of $200.0 to wallet 1A2B3C4D5E
Payment completed!
```

**Hints:**
- Think about what makes payment methods different (the details) vs what's common (the process)
- PaymentProcessor doesn't care HOW payment is processed, just that it CAN be processed
- This is exactly how Selenium WebDriver works - different browsers, same interface

---

### Exercise 5: Test Automation Framework Base Class

**Objective:** Create a realistic abstract base class for test automation

**Problem Statement:**
Create an abstract `BaseTest` class that handles common test setup and teardown. Concrete test classes should extend this and implement their specific test logic.

**Requirements:**
- Abstract class `BaseTest` with:
  - Field `testName` (String)
  - Constructor that accepts testName
  - Concrete method `setUp()` - prints setup message
  - Concrete method `tearDown()` - prints teardown message
  - Abstract method `runTest()` - to be implemented by test classes
  - Concrete method `executeTest()` - calls setUp(), runTest(), tearDown() in order
- Create two concrete test classes:
  - `LoginTest` - prints login test steps
  - `SearchTest` - prints search test steps

**Starter Code:**
```java
abstract class BaseTest {
    protected String testName;

    public BaseTest(String testName) {
        // TODO: Initialize testName
    }

    public void setUp() {
        // TODO: Print "Setting up test: [testName]"
        // TODO: Print "Opening browser..."
        // TODO: Print "Navigating to application..."
    }

    public void tearDown() {
        // TODO: Print "Cleaning up test: [testName]"
        // TODO: Print "Closing browser..."
    }

    // TODO: Add abstract method runTest()

    public void executeTest() {
        System.out.println("========================================");
        setUp();
        System.out.println("--- Running Test ---");
        runTest();
        tearDown();
        System.out.println("========================================\n");
    }
}

class LoginTest extends BaseTest {
    public LoginTest() {
        super("Login Test");
    }

    @Override
    public void runTest() {
        // TODO: Print login test steps
        // 1. Enter username
        // 2. Enter password
        // 3. Click login button
        // 4. Verify homepage displayed
    }
}

class SearchTest extends BaseTest {
    public SearchTest() {
        super("Search Test");
    }

    @Override
    public void runTest() {
        // TODO: Print search test steps
        // 1. Enter search query
        // 2. Click search button
        // 3. Verify results displayed
        // 4. Validate result count
    }
}

public class Exercise5 {
    public static void main(String[] args) {
        BaseTest test1 = new LoginTest();
        test1.executeTest();

        BaseTest test2 = new SearchTest();
        test2.executeTest();
    }
}
```

**Test Cases:**
```
Expected Output:
========================================
Setting up test: Login Test
Opening browser...
Navigating to application...
--- Running Test ---
Step 1: Enter username
Step 2: Enter password
Step 3: Click login button
Step 4: Verify homepage displayed
Cleaning up test: Login Test
Closing browser...
========================================

========================================
Setting up test: Search Test
Opening browser...
Navigating to application...
--- Running Test ---
Step 1: Enter search query
Step 2: Click search button
Step 3: Verify results displayed
Step 4: Validate result count
Cleaning up test: Search Test
Closing browser...
========================================
```

**Hints:**
- This pattern is VERY common in real test frameworks (TestNG, JUnit)
- BaseTest handles repetitive setup/teardown
- Each test focuses only on its specific test logic
- `executeTest()` provides a template method pattern

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Complete Shape Hierarchy (Mini Project Preview)

**Objective:** Build a shape calculator system using both abstract classes and interfaces

**Requirements:**
- Interface `Measurable` with methods:
  - `double getArea()`
  - `double getPerimeter()`
- Abstract class `Shape` with:
  - Field `name` (String)
  - Constructor
  - Concrete method `displayInfo()` - prints name and calls getArea()/getPerimeter()
  - Abstract methods for area and perimeter calculations
- Three concrete shape classes:
  - `Circle` - has radius
  - `Rectangle` - has length and width
  - `Triangle` - has three sides
- Each class implements the abstract methods with proper formulas

**Starter Code:**
```java
interface Measurable {
    double getArea();
    double getPerimeter();
}

abstract class Shape implements Measurable {
    protected String name;

    public Shape(String name) {
        this.name = name;
    }

    public void displayInfo() {
        System.out.println("Shape: " + name);
        System.out.printf("Area: %.2f\n", getArea());
        System.out.printf("Perimeter: %.2f\n", getPerimeter());
        System.out.println("---");
    }

    // Abstract methods inherited from Measurable
    // Will be implemented by concrete classes
}

class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        super("Circle");
        this.radius = radius;
    }

    @Override
    public double getArea() {
        // TODO: Return π * r²
        // Use Math.PI
        return 0;
    }

    @Override
    public double getPerimeter() {
        // TODO: Return 2 * π * r
        return 0;
    }
}

class Rectangle extends Shape {
    private double length;
    private double width;

    public Rectangle(double length, double width) {
        super("Rectangle");
        this.length = length;
        this.width = width;
    }

    @Override
    public double getArea() {
        // TODO: Return length * width
        return 0;
    }

    @Override
    public double getPerimeter() {
        // TODO: Return 2 * (length + width)
        return 0;
    }
}

class Triangle extends Shape {
    private double side1;
    private double side2;
    private double side3;

    public Triangle(double side1, double side2, double side3) {
        super("Triangle");
        this.side1 = side1;
        this.side2 = side2;
        this.side3 = side3;
    }

    @Override
    public double getArea() {
        // TODO: Use Heron's formula
        // s = (side1 + side2 + side3) / 2
        // area = sqrt(s * (s - side1) * (s - side2) * (s - side3))
        // Use Math.sqrt()
        return 0;
    }

    @Override
    public double getPerimeter() {
        // TODO: Return sum of all sides
        return 0;
    }
}

public class Exercise6 {
    public static void main(String[] args) {
        Shape circle = new Circle(5);
        Shape rectangle = new Rectangle(4, 6);
        Shape triangle = new Triangle(3, 4, 5);

        // Array of shapes
        Shape[] shapes = {circle, rectangle, triangle};

        System.out.println("SHAPE CALCULATOR");
        System.out.println("================\n");

        for (Shape shape : shapes) {
            shape.displayInfo();
        }

        // Can also reference as Measurable
        System.out.println("Using Measurable interface:");
        Measurable m = circle;
        System.out.printf("Circle area: %.2f\n", m.getArea());
    }
}
```

**Testing Instructions:**
Test with these shapes:
1. Circle with radius 5
2. Rectangle with length 4 and width 6
3. Triangle with sides 3, 4, 5

**Expected Output:**
```
SHAPE CALCULATOR
================

Shape: Circle
Area: 78.54
Perimeter: 31.42
---
Shape: Rectangle
Area: 24.00
Perimeter: 20.00
---
Shape: Triangle
Area: 6.00
Perimeter: 12.00
---
Using Measurable interface:
Circle area: 78.54
```

**Bonus Challenges:**
- [ ] Add input validation (no negative dimensions)
- [ ] Add a `Square` class that extends Rectangle
- [ ] Create a method to find the largest shape by area
- [ ] Add a `toString()` method to each shape

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution

```java
interface Printable {
    void print();
}

class Document implements Printable {
    private String content;

    public Document(String content) {
        this.content = content;
    }

    @Override
    public void print() {
        System.out.println("Printing document: " + content);
    }
}

class Image implements Printable {
    private String filename;

    public Image(String filename) {
        this.filename = filename;
    }

    @Override
    public void print() {
        System.out.println("Printing image: " + filename);
    }
}

public class Exercise1 {
    public static void main(String[] args) {
        Printable doc = new Document("Important Report");
        Printable img = new Image("photo.jpg");

        doc.print();
        img.print();
    }
}
```

**Explanation:**
- Interface `Printable` defines a contract - any class implementing it must have a `print()` method
- Both `Document` and `Image` implement this interface differently
- We can reference both using `Printable` interface type - this is polymorphism
- Each class provides its own implementation of `print()`

**Key Takeaways:**
- Interfaces define **what** to do, not **how** to do it
- Multiple unrelated classes can implement the same interface
- Interface reference can point to any implementing class object

---

### Exercise 2 Solution

```java
abstract class Employee {
    protected String name;
    protected int id;

    public Employee(String name, int id) {
        this.name = name;
        this.id = id;
    }

    public void displayInfo() {
        System.out.println("Employee: " + name + ", ID: " + id);
    }

    abstract double calculateSalary();
}

class FullTimeEmployee extends Employee {
    private double monthlySalary;

    public FullTimeEmployee(String name, int id, double monthlySalary) {
        super(name, id);
        this.monthlySalary = monthlySalary;
    }

    @Override
    double calculateSalary() {
        return monthlySalary;
    }
}

class ContractEmployee extends Employee {
    private double hourlyRate;
    private int hoursWorked;

    public ContractEmployee(String name, int id, double hourlyRate, int hoursWorked) {
        super(name, id);
        this.hourlyRate = hourlyRate;
        this.hoursWorked = hoursWorked;
    }

    @Override
    double calculateSalary() {
        return hourlyRate * hoursWorked;
    }
}

public class Exercise2 {
    public static void main(String[] args) {
        Employee emp1 = new FullTimeEmployee("Alice", 101, 5000);
        Employee emp2 = new ContractEmployee("Bob", 102, 50, 160);

        emp1.displayInfo();
        System.out.println("Salary: $" + emp1.calculateSalary());

        emp2.displayInfo();
        System.out.println("Salary: $" + emp2.calculateSalary());
    }
}
```

**Explanation:**
- Abstract class `Employee` has both concrete (displayInfo) and abstract (calculateSalary) methods
- Constructor in abstract class initializes common fields
- Each concrete class implements `calculateSalary()` differently based on employment type
- Abstract classes allow code reuse while enforcing method implementation

**Key Takeaways:**
- Abstract classes are perfect when you have shared code and specific behaviors
- Constructors in abstract classes are called via `super()`
- Use `protected` for fields that subclasses need to access

---

### Exercise 3 Solution

```java
interface Flyable {
    void fly();
}

interface Swimmable {
    void swim();
}

class Duck implements Flyable, Swimmable {
    private String name;

    public Duck(String name) {
        this.name = name;
    }

    @Override
    public void fly() {
        System.out.println(name + " is flying in the sky");
    }

    @Override
    public void swim() {
        System.out.println(name + " is swimming in the pond");
    }
}

public class Exercise3 {
    public static void main(String[] args) {
        Duck duck = new Duck("Donald");
        duck.fly();
        duck.swim();

        Flyable flyingBird = duck;
        flyingBird.fly();

        Swimmable swimmer = duck;
        swimmer.swim();
    }
}
```

**Explanation:**
- `Duck` implements both `Flyable` and `Swimmable` interfaces
- Must implement all methods from both interfaces
- Same duck object can be referenced by different interface types
- This enables multiple inheritance of capabilities

**Key Takeaways:**
- A class can implement multiple interfaces (separated by commas)
- Each interface adds a capability/contract
- Very useful in Selenium: `ChromeDriver implements WebDriver, JavascriptExecutor, TakesScreenshot`

---

### Exercise 4 Solution

```java
interface PaymentMethod {
    void processPayment(double amount);
    String getPaymentType();
}

class CreditCardPayment implements PaymentMethod {
    private String last4Digits;

    public CreditCardPayment(String last4Digits) {
        this.last4Digits = last4Digits;
    }

    @Override
    public void processPayment(double amount) {
        System.out.println("Processing Credit Card payment of $" + amount + " (Card: ****" + last4Digits + ")");
    }

    @Override
    public String getPaymentType() {
        return "Credit Card";
    }
}

class PayPalPayment implements PaymentMethod {
    private String email;

    public PayPalPayment(String email) {
        this.email = email;
    }

    @Override
    public void processPayment(double amount) {
        System.out.println("Processing PayPal payment of $" + amount + " to " + email);
    }

    @Override
    public String getPaymentType() {
        return "PayPal";
    }
}

class BitcoinPayment implements PaymentMethod {
    private String walletAddress;

    public BitcoinPayment(String walletAddress) {
        this.walletAddress = walletAddress;
    }

    @Override
    public void processPayment(double amount) {
        System.out.println("Processing Bitcoin payment of $" + amount + " to wallet " + walletAddress);
    }

    @Override
    public String getPaymentType() {
        return "Bitcoin";
    }
}

class PaymentProcessor {
    public void executePayment(PaymentMethod payment, double amount) {
        System.out.println("Starting payment processing...");
        System.out.println("Payment Type: " + payment.getPaymentType());
        payment.processPayment(amount);
        System.out.println("Payment completed!\n");
    }
}

public class Exercise4 {
    public static void main(String[] args) {
        PaymentProcessor processor = new PaymentProcessor();

        PaymentMethod creditCard = new CreditCardPayment("1234");
        PaymentMethod paypal = new PayPalPayment("user@example.com");
        PaymentMethod bitcoin = new BitcoinPayment("1A2B3C4D5E");

        processor.executePayment(creditCard, 150.00);
        processor.executePayment(paypal, 75.50);
        processor.executePayment(bitcoin, 200.00);
    }
}
```

**Explanation:**
- `PaymentMethod` interface defines contract for all payment types
- Each payment class implements the interface with specific details
- `PaymentProcessor.executePayment()` accepts ANY PaymentMethod - doesn't care about specifics
- Easy to add new payment methods without changing PaymentProcessor

**Key Takeaways:**
- Interfaces enable loose coupling - PaymentProcessor doesn't depend on specific payment classes
- Open/Closed Principle - open for extension (new payment types), closed for modification
- This is EXACTLY how Selenium WebDriver works

---

### Exercise 5 Solution

```java
abstract class BaseTest {
    protected String testName;

    public BaseTest(String testName) {
        this.testName = testName;
    }

    public void setUp() {
        System.out.println("Setting up test: " + testName);
        System.out.println("Opening browser...");
        System.out.println("Navigating to application...");
    }

    public void tearDown() {
        System.out.println("Cleaning up test: " + testName);
        System.out.println("Closing browser...");
    }

    abstract void runTest();

    public void executeTest() {
        System.out.println("========================================");
        setUp();
        System.out.println("--- Running Test ---");
        runTest();
        tearDown();
        System.out.println("========================================\n");
    }
}

class LoginTest extends BaseTest {
    public LoginTest() {
        super("Login Test");
    }

    @Override
    public void runTest() {
        System.out.println("Step 1: Enter username");
        System.out.println("Step 2: Enter password");
        System.out.println("Step 3: Click login button");
        System.out.println("Step 4: Verify homepage displayed");
    }
}

class SearchTest extends BaseTest {
    public SearchTest() {
        super("Search Test");
    }

    @Override
    public void runTest() {
        System.out.println("Step 1: Enter search query");
        System.out.println("Step 2: Click search button");
        System.out.println("Step 3: Verify results displayed");
        System.out.println("Step 4: Validate result count");
    }
}

public class Exercise5 {
    public static void main(String[] args) {
        BaseTest test1 = new LoginTest();
        test1.executeTest();

        BaseTest test2 = new SearchTest();
        test2.executeTest();
    }
}
```

**Explanation:**
- `BaseTest` handles common setup and teardown for all tests
- Abstract method `runTest()` forces each test to implement its specific logic
- `executeTest()` provides a template - same flow for all tests
- Each test focuses only on its unique test steps

**Key Takeaways:**
- Template Method Pattern - common in test frameworks
- DRY principle - Don't Repeat Yourself (setup/teardown)
- This is how TestNG's @BeforeMethod and @AfterMethod work conceptually

---

### Exercise 6 Solution

```java
interface Measurable {
    double getArea();
    double getPerimeter();
}

abstract class Shape implements Measurable {
    protected String name;

    public Shape(String name) {
        this.name = name;
    }

    public void displayInfo() {
        System.out.println("Shape: " + name);
        System.out.printf("Area: %.2f\n", getArea());
        System.out.printf("Perimeter: %.2f\n", getPerimeter());
        System.out.println("---");
    }
}

class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        super("Circle");
        this.radius = radius;
    }

    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }

    @Override
    public double getPerimeter() {
        return 2 * Math.PI * radius;
    }
}

class Rectangle extends Shape {
    private double length;
    private double width;

    public Rectangle(double length, double width) {
        super("Rectangle");
        this.length = length;
        this.width = width;
    }

    @Override
    public double getArea() {
        return length * width;
    }

    @Override
    public double getPerimeter() {
        return 2 * (length + width);
    }
}

class Triangle extends Shape {
    private double side1;
    private double side2;
    private double side3;

    public Triangle(double side1, double side2, double side3) {
        super("Triangle");
        this.side1 = side1;
        this.side2 = side2;
        this.side3 = side3;
    }

    @Override
    public double getArea() {
        // Heron's formula
        double s = (side1 + side2 + side3) / 2;
        return Math.sqrt(s * (s - side1) * (s - side2) * (s - side3));
    }

    @Override
    public double getPerimeter() {
        return side1 + side2 + side3;
    }
}

public class Exercise6 {
    public static void main(String[] args) {
        Shape circle = new Circle(5);
        Shape rectangle = new Rectangle(4, 6);
        Shape triangle = new Triangle(3, 4, 5);

        Shape[] shapes = {circle, rectangle, triangle};

        System.out.println("SHAPE CALCULATOR");
        System.out.println("================\n");

        for (Shape shape : shapes) {
            shape.displayInfo();
        }

        System.out.println("Using Measurable interface:");
        Measurable m = circle;
        System.out.printf("Circle area: %.2f\n", m.getArea());
    }
}
```

**Explanation:**
- `Measurable` interface defines what measurable objects can do
- `Shape` abstract class implements Measurable and provides common displayInfo()
- Each concrete shape implements area/perimeter formulas
- Polymorphism allows treating all shapes uniformly in array

**Key Takeaways:**
- Combining interfaces and abstract classes is powerful
- Interface defines contract, abstract class provides shared implementation
- Each concrete class focuses on its specific formula
- Easy to add new shapes without changing existing code

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 | ☐ | __/10 | ___ min | |
| 2 | ☐ | __/10 | ___ min | |
| 3 | ☐ | __/10 | ___ min | |
| 4 | ☐ | __/10 | ___ min | |
| 5 | ☐ | __/10 | ___ min | |
| 6 | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
- [ ] Interface syntax
- [ ] Abstract class vs interface decision
- [ ] Multiple interface implementation
- [ ] @Override annotation
- [ ] Understanding when to use each

**Areas I excelled at:**
- [ ] Creating interfaces
- [ ] Implementing abstract methods
- [ ] Understanding polymorphism
- [ ] Seeing Selenium connection

---

## ✅ Answers to Self-Check Questions (from Concept Guide)

1. **What is the main difference between an abstract class and an interface?**
   - **Answer:** Abstract class provides partial abstraction with both abstract and concrete methods, fields, and constructors. Interface provides complete abstraction with only method signatures (all abstract before Java 8). A class can extend one abstract class but implement multiple interfaces.

2. **Can an abstract class have a constructor? Can an interface have a constructor?**
   - **Answer:** YES, abstract classes can have constructors (called when creating subclass objects). NO, interfaces cannot have constructors (they can't be instantiated).

3. **Why does Selenium use WebDriver as an interface instead of an abstract class?**
   - **Answer:** Because browser drivers (ChromeDriver, FirefoxDriver) might need to extend other classes. Using an interface allows any class to implement WebDriver regardless of inheritance hierarchy. Also enables multiple interface implementation (WebDriver, JavascriptExecutor, TakesScreenshot).

4. **If a class implements an interface but doesn't implement all methods, what happens?**
   - **Answer:** Compilation error. You must either implement all methods OR declare the class as abstract.

5. **Give a real example from Selenium where you've used an interface.**
   - **Answer:** `WebDriver driver = new ChromeDriver();` - WebDriver is an interface, ChromeDriver is the concrete implementation.

---
