# DAY 5 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master inheritance and polymorphism by building class hierarchies, overriding methods, and leveraging the power of the `extends` keyword and `super` keyword in real-world scenarios.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Basic Inheritance
**Objective:** Understand the `extends` keyword and method inheritance

**Task:**
Create a `Person` class with name and age fields, and a `introduce()` method. Then create a `Student` class that extends `Person` and adds a `studentId` field. Test that Student inherits Person's method.

**Starter Code:**
```java
public class Person {
    // TODO: Add protected fields: name and age

    // TODO: Create constructor

    // TODO: Create introduce() method that prints name and age
}

public class Student {
    // TODO: Extend Person class
    // TODO: Add private field: studentId

    // TODO: Create constructor using super()

    // TODO: Test in main method
    public static void main(String[] args) {
        // Create a Student and call introduce() method
    }
}
```

**Expected Output:**
```
Hello, my name is Alice and I am 20 years old.
```

**Hints:**
- Use `protected` for parent class fields so child can access them
- Remember `super()` to call parent constructor
- Child inherits `introduce()` method automatically

---

### Exercise 2: Method Overriding
**Objective:** Override a parent method in a child class

**Task:**
Extend the previous exercise. Override the `introduce()` method in Student to also print the student ID.

**Starter Code:**
```java
public class Student extends Person {
    private String studentId;

    public Student(String name, int age, String studentId) {
        super(name, age);
        this.studentId = studentId;
    }

    // TODO: Override introduce() method
    // Use @Override annotation
    // Call super.introduce() first, then print student ID

    public static void main(String[] args) {
        Student student = new Student("Bob", 21, "S12345");
        student.introduce();
    }
}
```

**Expected Output:**
```
Hello, my name is Bob and I am 21 years old.
Student ID: S12345
```

**Hints:**
- Use `@Override` annotation above the method
- Call `super.introduce()` to reuse parent's logic
- Then add student-specific information

---

### Exercise 3: Polymorphism Basics
**Objective:** Understand polymorphism with parent references

**Task:**
Create an array of Person references that hold both Person and Student objects. Loop through and call `introduce()` on each. Observe polymorphism in action.

**Starter Code:**
```java
public class PolymorphismDemo {
    public static void main(String[] args) {
        // TODO: Create array of Person with 3 elements
        // Element 0: Person object
        // Element 1: Student object
        // Element 2: Student object

        // TODO: Loop through array and call introduce() on each

        // Observe: Student objects call their overridden method!
    }
}
```

**Expected Output:**
```
Hello, my name is John and I am 45 years old.
Hello, my name is Alice and I am 20 years old.
Student ID: S12345
Hello, my name is Bob and I am 21 years old.
Student ID: S67890
```

**Hints:**
- Array type: `Person[]`
- Store Student objects in Person references: `Person p = new Student(...)`
- The actual object type determines which method is called

---

## 💪 Core Practice (30 min)

### Exercise 4: Employee Hierarchy
**Objective:** Build a class hierarchy with multiple levels

**Problem Statement:**
Create an employee management system with the following hierarchy:
- `Employee` (base class): name, employeeId, baseSalary
- `Manager` extends Employee: adds teamSize, overrides calculateSalary() to add 20% bonus
- `Developer` extends Employee: adds programmingLanguage, overrides calculateSalary() to add 15% bonus
- `Intern` extends Employee: works part-time, overrides calculateSalary() to pay 50% of base salary

**Requirements:**
- Employee has `calculateSalary()` method that returns baseSalary
- Each subclass overrides to implement its own salary calculation
- Each class has a `displayInfo()` method showing all details
- Use `@Override` annotations
- Use `super()` in constructors

**Starter Code:**
```java
public class Employee {
    protected String name;
    protected String employeeId;
    protected double baseSalary;

    public Employee(String name, String employeeId, double baseSalary) {
        this.name = name;
        this.employeeId = employeeId;
        this.baseSalary = baseSalary;
    }

    public double calculateSalary() {
        return baseSalary;
    }

    public void displayInfo() {
        System.out.println("Name: " + name);
        System.out.println("ID: " + employeeId);
        System.out.println("Salary: $" + calculateSalary());
    }
}

// TODO: Create Manager class
public class Manager extends Employee {
    // TODO: Add teamSize field
    // TODO: Create constructor
    // TODO: Override calculateSalary() - add 20% bonus
    // TODO: Override displayInfo() - include team size
}

// TODO: Create Developer class
public class Developer extends Employee {
    // TODO: Add programmingLanguage field
    // TODO: Create constructor
    // TODO: Override calculateSalary() - add 15% bonus
    // TODO: Override displayInfo() - include programming language
}

// TODO: Create Intern class
public class Intern extends Employee {
    // TODO: Add university field
    // TODO: Create constructor
    // TODO: Override calculateSalary() - pay 50% of base
    // TODO: Override displayInfo() - include university
}

public class EmployeeTest {
    public static void main(String[] args) {
        // TODO: Create array of Employee references
        // Add Manager, Developer, and Intern objects
        // Loop through and display info for each
        // Demonstrate polymorphism
    }
}
```

**Test Cases:**
```
Input: Manager("Alice", "M001", 100000, 10)
Expected Output:
Name: Alice
ID: M001
Team Size: 10
Salary: $120000.0

Input: Developer("Bob", "D001", 80000, "Java")
Expected Output:
Name: Bob
ID: D001
Programming Language: Java
Salary: $92000.0

Input: Intern("Charlie", "I001", 40000, "MIT")
Expected Output:
Name: Charlie
ID: I001
University: MIT
Salary: $20000.0
```

**Hints:**
- In Manager: `return baseSalary * 1.20;`
- In Developer: `return baseSalary * 1.15;`
- In Intern: `return baseSalary * 0.50;`
- Use `super.displayInfo()` then add child-specific details

---

### Exercise 5: Shape Calculator with Inheritance
**Objective:** Use inheritance and method overriding for geometric shapes

**Problem Statement:**
Create a hierarchy of shapes with area and perimeter calculations:
- `Shape` (abstract base): has color field
- `Rectangle` extends Shape: width and height
- `Circle` extends Shape: radius
- `Triangle` extends Shape: three sides

**Requirements:**
- Each shape calculates its own area and perimeter
- Override `toString()` to display shape info
- Use `super()` in constructors
- Demonstrate polymorphism with an array of shapes

**Starter Code:**
```java
public class Shape {
    protected String color;

    public Shape(String color) {
        this.color = color;
    }

    // Default implementation (to be overridden)
    public double calculateArea() {
        return 0.0;
    }

    public double calculatePerimeter() {
        return 0.0;
    }

    @Override
    public String toString() {
        return "Shape [color=" + color + "]";
    }
}

// TODO: Create Rectangle class
public class Rectangle extends Shape {
    private double width;
    private double height;

    // TODO: Constructor
    // TODO: Override calculateArea() - width * height
    // TODO: Override calculatePerimeter() - 2 * (width + height)
    // TODO: Override toString()
}

// TODO: Create Circle class
public class Circle extends Shape {
    private double radius;

    // TODO: Constructor
    // TODO: Override calculateArea() - PI * radius^2
    // TODO: Override calculatePerimeter() - 2 * PI * radius
    // TODO: Override toString()
}

// TODO: Create Triangle class
public class Triangle extends Shape {
    private double side1, side2, side3;

    // TODO: Constructor
    // TODO: Override calculateArea() - use Heron's formula
    // TODO: Override calculatePerimeter() - sum of all sides
    // TODO: Override toString()
}

public class ShapeTest {
    public static void main(String[] args) {
        // TODO: Create array of Shape with different shapes
        // TODO: Loop and print each shape's area and perimeter
        // TODO: Demonstrate polymorphism
    }
}
```

**Test Cases:**
```
Input: Rectangle("Red", 5, 10)
Expected Output:
Rectangle [color=Red, width=5.0, height=10.0]
Area: 50.0
Perimeter: 30.0

Input: Circle("Blue", 7)
Expected Output:
Circle [color=Blue, radius=7.0]
Area: 153.94
Perimeter: 43.98

Input: Triangle("Green", 3, 4, 5)
Expected Output:
Triangle [color=Green, sides=3.0, 4.0, 5.0]
Area: 6.0
Perimeter: 12.0
```

**Hints:**
- Circle area: `Math.PI * radius * radius`
- Circle perimeter: `2 * Math.PI * radius`
- Heron's formula: `s = (a+b+c)/2; area = sqrt(s*(s-a)*(s-b)*(s-c))`
- Use `Math.sqrt()` for square root

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Banking System with Inheritance
**Objective:** Build today's mini project - a banking hierarchy with inheritance and polymorphism

**Requirements:**
Create a banking system with:
1. **BankAccount** (parent class):
   - Fields: accountNumber, accountHolderName, balance
   - Methods: deposit(), withdraw(), displayBalance()
   - Constructor to initialize all fields

2. **SavingsAccount** extends BankAccount:
   - Additional field: interestRate
   - Override withdraw() - minimum balance of $500 required
   - New method: addInterest() - adds interest to balance

3. **CheckingAccount** extends BankAccount:
   - Additional field: overdraftLimit
   - Override withdraw() - allow overdraft up to limit
   - New method: displayOverdraftAvailable()

4. **BusinessAccount** extends BankAccount:
   - Additional field: transactionFee (charged per transaction)
   - Override deposit() and withdraw() - deduct transaction fee
   - New method: displayTotalFees()

**Starter Code:**
```java
public class BankAccount {
    protected String accountNumber;
    protected String accountHolderName;
    protected double balance;

    public BankAccount(String accountNumber, String accountHolderName, double balance) {
        this.accountNumber = accountNumber;
        this.accountHolderName = accountHolderName;
        this.balance = balance;
    }

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
            System.out.println("Insufficient funds or invalid amount");
        }
    }

    public void displayBalance() {
        System.out.println("Account: " + accountNumber);
        System.out.println("Holder: " + accountHolderName);
        System.out.println("Balance: $" + balance);
    }
}

// TODO: Create SavingsAccount class
public class SavingsAccount extends BankAccount {
    private double interestRate;

    // TODO: Constructor
    // TODO: Override withdraw() - check minimum balance $500
    // TODO: Create addInterest() method
}

// TODO: Create CheckingAccount class
public class CheckingAccount extends BankAccount {
    private double overdraftLimit;

    // TODO: Constructor
    // TODO: Override withdraw() - allow overdraft
    // TODO: Create displayOverdraftAvailable() method
}

// TODO: Create BusinessAccount class
public class BusinessAccount extends BankAccount {
    private double transactionFee;
    private double totalFeesCollected;

    // TODO: Constructor
    // TODO: Override deposit() - deduct fee
    // TODO: Override withdraw() - deduct fee
    // TODO: Create displayTotalFees() method
}

public class BankingSystemTest {
    public static void main(String[] args) {
        // TODO: Create different account types
        // TODO: Test deposit, withdraw, and special methods
        // TODO: Store in BankAccount array to demonstrate polymorphism
    }
}
```

**Testing Instructions:**
Test these scenarios:
1. SavingsAccount: Try withdrawing below $500 minimum (should fail)
2. SavingsAccount: Add interest at 3.5% rate
3. CheckingAccount: Withdraw into overdraft (should succeed within limit)
4. BusinessAccount: Deposit and withdraw, verify fees are deducted
5. Use polymorphism: BankAccount[] with all three types

**Bonus Challenges:**
- [ ] Add transaction history tracking (ArrayList of transactions)
- [ ] Implement `equals()` method to compare accounts by accountNumber
- [ ] Add `toString()` override for better printing
- [ ] Create a `closeAccount()` method that sets balance to 0
- [ ] Add input validation for negative amounts

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
public class Person {
    protected String name;
    protected int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void introduce() {
        System.out.println("Hello, my name is " + name + " and I am " + age + " years old.");
    }
}

public class Student extends Person {
    private String studentId;

    public Student(String name, int age, String studentId) {
        super(name, age);  // Call parent constructor
        this.studentId = studentId;
    }

    public static void main(String[] args) {
        Student student = new Student("Alice", 20, "S12345");
        student.introduce();  // Inherited method works!
    }
}
```

**Explanation:**
- `extends Person` establishes inheritance relationship
- `protected` fields in Person are accessible in Student
- `super(name, age)` calls Person's constructor to initialize parent fields
- Student automatically inherits `introduce()` method without rewriting it

**Key Takeaways:**
- Inheritance eliminates code duplication
- Use `super()` to initialize parent class
- Child class "is-a" type of parent class

---

### Exercise 2 Solution
```java
public class Student extends Person {
    private String studentId;

    public Student(String name, int age, String studentId) {
        super(name, age);
        this.studentId = studentId;
    }

    @Override
    public void introduce() {
        super.introduce();  // Call parent's version first
        System.out.println("Student ID: " + studentId);
    }

    public static void main(String[] args) {
        Student student = new Student("Bob", 21, "S12345");
        student.introduce();
    }
}
```

**Explanation:**
- `@Override` annotation tells compiler we're overriding a parent method
- `super.introduce()` reuses parent's logic (DRY principle)
- Then we add student-specific information
- Method signature must match parent exactly

**Key Takeaways:**
- Method overriding allows customization while reusing parent logic
- Always use `@Override` annotation for safety
- `super.methodName()` calls the parent's version

---

### Exercise 3 Solution
```java
public class PolymorphismDemo {
    public static void main(String[] args) {
        // Parent reference, mixed object types
        Person[] people = new Person[3];
        people[0] = new Person("John", 45);
        people[1] = new Student("Alice", 20, "S12345");
        people[2] = new Student("Bob", 21, "S67890");

        // Polymorphism in action
        for (Person person : people) {
            person.introduce();  // Calls appropriate version based on actual object
            System.out.println();
        }
    }
}
```

**Explanation:**
- Array type is `Person[]` but can hold Student objects (Student IS-A Person)
- When calling `introduce()`, Java looks at actual object type, not reference type
- Person objects call Person's `introduce()`
- Student objects call Student's overridden `introduce()`
- This is runtime polymorphism (dynamic method dispatch)

**Key Takeaways:**
- Polymorphism enables treating related objects uniformly
- Actual behavior is determined at runtime
- Essential for building flexible frameworks

---

### Exercise 4 Solution
```java
public class Employee {
    protected String name;
    protected String employeeId;
    protected double baseSalary;

    public Employee(String name, String employeeId, double baseSalary) {
        this.name = name;
        this.employeeId = employeeId;
        this.baseSalary = baseSalary;
    }

    public double calculateSalary() {
        return baseSalary;
    }

    public void displayInfo() {
        System.out.println("Name: " + name);
        System.out.println("ID: " + employeeId);
        System.out.println("Salary: $" + calculateSalary());
    }
}

public class Manager extends Employee {
    private int teamSize;

    public Manager(String name, String employeeId, double baseSalary, int teamSize) {
        super(name, employeeId, baseSalary);
        this.teamSize = teamSize;
    }

    @Override
    public double calculateSalary() {
        return baseSalary * 1.20;  // 20% bonus
    }

    @Override
    public void displayInfo() {
        super.displayInfo();  // Reuse parent's logic
        System.out.println("Team Size: " + teamSize);
    }
}

public class Developer extends Employee {
    private String programmingLanguage;

    public Developer(String name, String employeeId, double baseSalary, String programmingLanguage) {
        super(name, employeeId, baseSalary);
        this.programmingLanguage = programmingLanguage;
    }

    @Override
    public double calculateSalary() {
        return baseSalary * 1.15;  // 15% bonus
    }

    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("Programming Language: " + programmingLanguage);
    }
}

public class Intern extends Employee {
    private String university;

    public Intern(String name, String employeeId, double baseSalary, String university) {
        super(name, employeeId, baseSalary);
        this.university = university;
    }

    @Override
    public double calculateSalary() {
        return baseSalary * 0.50;  // 50% of base (part-time)
    }

    @Override
    public void displayInfo() {
        super.displayInfo();
        System.out.println("University: " + university);
    }
}

public class EmployeeTest {
    public static void main(String[] args) {
        // Polymorphism: Employee array holding different employee types
        Employee[] employees = new Employee[3];
        employees[0] = new Manager("Alice", "M001", 100000, 10);
        employees[1] = new Developer("Bob", "D001", 80000, "Java");
        employees[2] = new Intern("Charlie", "I001", 40000, "MIT");

        // Process all employees uniformly
        for (Employee emp : employees) {
            emp.displayInfo();
            System.out.println("---");
        }

        // Calculate total payroll
        double totalPayroll = 0;
        for (Employee emp : employees) {
            totalPayroll += emp.calculateSalary();
        }
        System.out.println("Total Payroll: $" + totalPayroll);
    }
}
```

**Explanation:**
- Each subclass has its own salary calculation logic
- `displayInfo()` reuses parent's version with `super.displayInfo()`
- Polymorphism allows processing all employee types uniformly
- Each object maintains its own behavior despite being stored as Employee reference

**Key Takeaways:**
- Inheritance models real-world "is-a" relationships
- Method overriding enables specialized behavior
- Polymorphism simplifies code that works with multiple related types

---

### Exercise 5 Solution
```java
public class Shape {
    protected String color;

    public Shape(String color) {
        this.color = color;
    }

    public double calculateArea() {
        return 0.0;
    }

    public double calculatePerimeter() {
        return 0.0;
    }

    @Override
    public String toString() {
        return "Shape [color=" + color + "]";
    }
}

public class Rectangle extends Shape {
    private double width;
    private double height;

    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }

    @Override
    public double calculateArea() {
        return width * height;
    }

    @Override
    public double calculatePerimeter() {
        return 2 * (width + height);
    }

    @Override
    public String toString() {
        return "Rectangle [color=" + color + ", width=" + width + ", height=" + height + "]";
    }
}

public class Circle extends Shape {
    private double radius;

    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }

    @Override
    public double calculatePerimeter() {
        return 2 * Math.PI * radius;
    }

    @Override
    public String toString() {
        return "Circle [color=" + color + ", radius=" + radius + "]";
    }
}

public class Triangle extends Shape {
    private double side1, side2, side3;

    public Triangle(String color, double side1, double side2, double side3) {
        super(color);
        this.side1 = side1;
        this.side2 = side2;
        this.side3 = side3;
    }

    @Override
    public double calculateArea() {
        // Heron's formula
        double s = (side1 + side2 + side3) / 2;
        return Math.sqrt(s * (s - side1) * (s - side2) * (s - side3));
    }

    @Override
    public double calculatePerimeter() {
        return side1 + side2 + side3;
    }

    @Override
    public String toString() {
        return "Triangle [color=" + color + ", sides=" + side1 + ", " + side2 + ", " + side3 + "]";
    }
}

public class ShapeTest {
    public static void main(String[] args) {
        Shape[] shapes = new Shape[3];
        shapes[0] = new Rectangle("Red", 5, 10);
        shapes[1] = new Circle("Blue", 7);
        shapes[2] = new Triangle("Green", 3, 4, 5);

        for (Shape shape : shapes) {
            System.out.println(shape);  // Calls overridden toString()
            System.out.printf("Area: %.2f\n", shape.calculateArea());
            System.out.printf("Perimeter: %.2f\n", shape.calculatePerimeter());
            System.out.println();
        }
    }
}
```

**Explanation:**
- Each shape overrides calculation methods with its own formulas
- `toString()` provides meaningful string representation
- Polymorphism allows treating all shapes uniformly
- Each shape's specific calculations are called at runtime

**Key Takeaways:**
- Override Object class methods like `toString()` for better debugging
- Use `Math` class for mathematical operations
- Polymorphism makes code extensible (easy to add new shapes)

---

### Exercise 6 Solution
```java
public class BankAccount {
    protected String accountNumber;
    protected String accountHolderName;
    protected double balance;

    public BankAccount(String accountNumber, String accountHolderName, double balance) {
        this.accountNumber = accountNumber;
        this.accountHolderName = accountHolderName;
        this.balance = balance;
    }

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
            System.out.println("Insufficient funds or invalid amount");
        }
    }

    public void displayBalance() {
        System.out.println("Account: " + accountNumber);
        System.out.println("Holder: " + accountHolderName);
        System.out.println("Balance: $" + balance);
    }
}

public class SavingsAccount extends BankAccount {
    private double interestRate;
    private static final double MINIMUM_BALANCE = 500.0;

    public SavingsAccount(String accountNumber, String accountHolderName, double balance, double interestRate) {
        super(accountNumber, accountHolderName, balance);
        this.interestRate = interestRate;
    }

    @Override
    public void withdraw(double amount) {
        if (amount > 0 && (balance - amount) >= MINIMUM_BALANCE) {
            balance -= amount;
            System.out.println("Withdrawn: $" + amount);
        } else {
            System.out.println("Cannot withdraw: Must maintain minimum balance of $" + MINIMUM_BALANCE);
        }
    }

    public void addInterest() {
        double interest = balance * (interestRate / 100);
        balance += interest;
        System.out.println("Interest added: $" + interest);
    }
}

public class CheckingAccount extends BankAccount {
    private double overdraftLimit;

    public CheckingAccount(String accountNumber, String accountHolderName, double balance, double overdraftLimit) {
        super(accountNumber, accountHolderName, balance);
        this.overdraftLimit = overdraftLimit;
    }

    @Override
    public void withdraw(double amount) {
        if (amount > 0 && amount <= (balance + overdraftLimit)) {
            balance -= amount;
            System.out.println("Withdrawn: $" + amount);
            if (balance < 0) {
                System.out.println("Warning: Account is overdrawn");
            }
        } else {
            System.out.println("Cannot withdraw: Exceeds overdraft limit");
        }
    }

    public void displayOverdraftAvailable() {
        double available = balance + overdraftLimit;
        System.out.println("Available with overdraft: $" + available);
    }
}

public class BusinessAccount extends BankAccount {
    private double transactionFee;
    private double totalFeesCollected;

    public BusinessAccount(String accountNumber, String accountHolderName, double balance, double transactionFee) {
        super(accountNumber, accountHolderName, balance);
        this.transactionFee = transactionFee;
        this.totalFeesCollected = 0;
    }

    @Override
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
            balance -= transactionFee;
            totalFeesCollected += transactionFee;
            System.out.println("Deposited: $" + amount + " (Fee: $" + transactionFee + ")");
        } else {
            System.out.println("Invalid deposit amount");
        }
    }

    @Override
    public void withdraw(double amount) {
        double totalDeduction = amount + transactionFee;
        if (amount > 0 && totalDeduction <= balance) {
            balance -= totalDeduction;
            totalFeesCollected += transactionFee;
            System.out.println("Withdrawn: $" + amount + " (Fee: $" + transactionFee + ")");
        } else {
            System.out.println("Insufficient funds (including transaction fee)");
        }
    }

    public void displayTotalFees() {
        System.out.println("Total fees collected: $" + totalFeesCollected);
    }
}

public class BankingSystemTest {
    public static void main(String[] args) {
        System.out.println("=== Savings Account Test ===");
        SavingsAccount savings = new SavingsAccount("SA001", "Alice", 1000, 3.5);
        savings.displayBalance();
        savings.withdraw(600);  // Should fail (below minimum)
        savings.withdraw(200);  // Should succeed
        savings.addInterest();
        savings.displayBalance();

        System.out.println("\n=== Checking Account Test ===");
        CheckingAccount checking = new CheckingAccount("CA001", "Bob", 500, 200);
        checking.displayBalance();
        checking.displayOverdraftAvailable();
        checking.withdraw(600);  // Uses overdraft
        checking.displayBalance();

        System.out.println("\n=== Business Account Test ===");
        BusinessAccount business = new BusinessAccount("BA001", "TechCorp", 5000, 2.5);
        business.displayBalance();
        business.deposit(1000);  // Fee deducted
        business.withdraw(500);  // Fee deducted
        business.displayTotalFees();
        business.displayBalance();

        System.out.println("\n=== Polymorphism Test ===");
        BankAccount[] accounts = {savings, checking, business};
        for (BankAccount account : accounts) {
            System.out.println("Processing account: " + account.accountNumber);
            account.displayBalance();
            System.out.println();
        }
    }
}
```

**Explanation:**
- Each account type overrides methods with specific business rules
- SavingsAccount enforces minimum balance in withdraw()
- CheckingAccount allows overdraft
- BusinessAccount deducts fees from both deposit and withdraw
- Polymorphism allows storing all accounts in BankAccount array

**Key Takeaways:**
- Inheritance models different account types with shared behavior
- Method overriding implements account-specific rules
- Constants (MINIMUM_BALANCE) improve maintainability
- Polymorphism enables uniform account processing

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
- [ ] Understanding inheritance syntax
- [ ] Using super keyword correctly
- [ ] Method overriding rules
- [ ] Polymorphism concepts
- [ ] Constructor chaining
- [ ] Access modifiers in inheritance

**Areas I excelled at:**
- [ ] Creating class hierarchies
- [ ] Overriding methods
- [ ] Using @Override annotation
- [ ] Applying polymorphism
- [ ] Building real-world examples

---

## ✅ Answers to Self-Check Questions

**Q1: What's the difference between `extends` and `implements` in Java?**
- `extends`: Used for class inheritance (one class inherits from another class)
- `implements`: Used for interfaces (covered in Day 6)
- A class can extend only ONE class but can implement MULTIPLE interfaces

**Q2: If a parent constructor has parameters, what must the child constructor do?**
The child constructor must explicitly call `super(parameters)` as the first statement. If the parent has no no-arg constructor, failing to call `super()` results in a compilation error.

**Q3: What will happen in this code?**
```java
Vehicle v = new Car();
Car c = v;  // Will this compile?
```
**Answer:** No, this will not compile. You need an explicit cast: `Car c = (Car) v;`
- Reason: `v` is a Vehicle reference. Even though it holds a Car object, the compiler only sees the Vehicle type. Downcasting (parent to child) requires explicit casting.

**Q4: Why should you always use `@Override` annotation when overriding methods?**
- Compiler verification: Ensures you're actually overriding a parent method
- Catches typos: If method name is misspelled, compiler errors immediately
- Prevents signature mismatches: If parameters don't match, you'll know
- Improves code readability: Makes intent clear to other developers

**Q5: Predict the output:**
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
**Answer:** Output is `B`
- Reason: Runtime polymorphism. Even though `obj` is an A reference, it points to a B object. Java calls the method based on the actual object type (B), not the reference type (A).

---

## 🎯 Next Steps

**You've completed the practice exercises! Now:**
1. Move to **Day-5-Debug-Practice.md** for debugging challenges
2. Then tackle **Day-5-Project-Guide.md** for the Vehicle Hierarchy project
3. Review **Day-5-Cheat-Sheet.md** for quick reference

**Confidence Check:**
- Inheritance: ___/10
- Method Overriding: ___/10
- Polymorphism: ___/10
- Overall: ___/10

If any rating is below 7, review the exercises and try them again before moving on!

---

**Great work! You're mastering OOP concepts that are critical for building professional Selenium frameworks!** 🚀

---
