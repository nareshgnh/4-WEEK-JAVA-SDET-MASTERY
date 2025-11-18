# DAY 5 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As an SDET, you'll spend significant time debugging:
- Test failures caused by code issues
- Inheritance hierarchy problems in page object models
- Method overriding mistakes that cause unexpected behavior
- Polymorphism-related bugs where wrong methods are called

Today's debugging challenges focus on common inheritance and polymorphism mistakes. These are real errors you'll encounter when building Selenium frameworks with Page Object Model!

---

## Challenge 1: Missing super() Call

**Broken Code:**
```java
public class Vehicle {
    protected String brand;
    protected int year;

    public Vehicle(String brand, int year) {
        this.brand = brand;
        this.year = year;
        System.out.println("Vehicle created: " + brand);
    }
}

public class Car extends Vehicle {
    private int doors;

    public Car(String brand, int year, int doors) {
        this.doors = doors;
        System.out.println("Car created with " + doors + " doors");
    }

    public void displayInfo() {
        System.out.println(brand + " " + year + " with " + doors + " doors");
    }

    public static void main(String[] args) {
        Car car = new Car("Toyota", 2024, 4);
        car.displayInfo();
    }
}
```

**Error Message:**
```
java: constructor Vehicle in class Vehicle cannot be applied to given types;
  required: java.lang.String,int
  found:    no arguments
  reason: actual and formal argument lists differ in length
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Look at the Car constructor
- What must you do when parent has a parameterized constructor?

---

**Solution:**

**Bug Location:** Line in Car constructor - missing `super(brand, year);`

**Explanation:**
The parent class `Vehicle` has a constructor that requires two parameters (brand and year). When a child class constructor is called, Java automatically tries to call the parent's no-arg constructor with `super()`. Since Vehicle doesn't have a no-arg constructor, the compiler errors.

**Fixed Code:**
```java
public class Car extends Vehicle {
    private int doors;

    public Car(String brand, int year, int doors) {
        super(brand, year);  // MUST call parent constructor first!
        this.doors = doors;
        System.out.println("Car created with " + doors + " doors");
    }

    public void displayInfo() {
        System.out.println(brand + " " + year + " with " + doors + " doors");
    }

    public static void main(String[] args) {
        Car car = new Car("Toyota", 2024, 4);
        car.displayInfo();
    }
}
```

**Key Learning:**
- If parent has a parameterized constructor, child MUST explicitly call `super(parameters)`
- `super()` must be the first statement in child constructor
- This is a very common mistake when extending BasePage in Selenium frameworks!

---

## Challenge 2: Private Field Access

**Broken Code:**
```java
public class Animal {
    private String name;
    private int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
}

public class Dog extends Animal {
    private String breed;

    public Dog(String name, int age, String breed) {
        super(name, age);
        this.breed = breed;
    }

    public void displayInfo() {
        System.out.println("Name: " + name);        // ERROR!
        System.out.println("Age: " + age);          // ERROR!
        System.out.println("Breed: " + breed);
    }

    public static void main(String[] args) {
        Dog dog = new Dog("Max", 5, "Golden Retriever");
        dog.displayInfo();
    }
}
```

**Error Message:**
```
java: name has private access in Animal
java: age has private access in Animal
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it (TWO possible solutions)

**Hints:**
- Private members are not accessible where?
- What access modifier allows child class access?
- Alternative: use getter methods

---

**Solution:**

**Bug Location:** Animal class fields are `private`, not accessible in Dog class

**Explanation:**
Private fields are not inherited or accessible in child classes. Even though Dog inherits from Animal, it cannot directly access `name` and `age` because they're marked as `private`.

**Fixed Code - Solution 1 (Change to protected):**
```java
public class Animal {
    protected String name;      // Changed from private to protected
    protected int age;          // Changed from private to protected

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
}

public class Dog extends Animal {
    private String breed;

    public Dog(String name, int age, String breed) {
        super(name, age);
        this.breed = breed;
    }

    public void displayInfo() {
        System.out.println("Name: " + name);        // OK now!
        System.out.println("Age: " + age);          // OK now!
        System.out.println("Breed: " + breed);
    }

    public static void main(String[] args) {
        Dog dog = new Dog("Max", 5, "Golden Retriever");
        dog.displayInfo();
    }
}
```

**Fixed Code - Solution 2 (Use getters - better encapsulation):**
```java
public class Animal {
    private String name;
    private int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void makeSound() {
        System.out.println(name + " makes a sound");
    }
}

public class Dog extends Animal {
    private String breed;

    public Dog(String name, int age, String breed) {
        super(name, age);
        this.breed = breed;
    }

    public void displayInfo() {
        System.out.println("Name: " + getName());   // Use getter
        System.out.println("Age: " + getAge());     // Use getter
        System.out.println("Breed: " + breed);
    }

    public static void main(String[] args) {
        Dog dog = new Dog("Max", 5, "Golden Retriever");
        dog.displayInfo();
    }
}
```

**Key Learning:**
- Private = not accessible anywhere except the class itself
- Protected = accessible in child classes
- Best practice: Use private with getters for better encapsulation
- In Selenium: WebDriver and WebDriverWait are often `protected` in BasePage

---

## Challenge 3: Method Override Signature Mismatch

**Broken Code:**
```java
public class Shape {
    protected String color;

    public Shape(String color) {
        this.color = color;
    }

    public double calculateArea() {
        return 0.0;
    }

    public void displayInfo() {
        System.out.println("Shape color: " + color);
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
    public int calculateArea() {  // ERROR: Return type mismatch!
        return (int) (width * height);
    }

    @Override
    public void displayInfo(String prefix) {  // ERROR: Different parameters!
        System.out.println(prefix + " " + color);
    }

    public static void main(String[] args) {
        Rectangle rect = new Rectangle("Red", 5.5, 10.0);
        System.out.println("Area: " + rect.calculateArea());
        rect.displayInfo("Rectangle color:");
    }
}
```

**Error Message:**
```
java: calculateArea() in Rectangle cannot override calculateArea() in Shape
  return type int is not compatible with double

java: method does not override or implement a method from a supertype
```

**Your Task:**
1. Identify both bugs
2. Explain why they're wrong
3. Fix them

**Hints:**
- Method signature includes return type and parameters
- Overriding requires exact match (with covariant return types exception)

---

**Solution:**

**Bug Location 1:** `calculateArea()` has wrong return type (int instead of double)
**Bug Location 2:** `displayInfo()` has different parameters than parent (overloading, not overriding)

**Explanation:**
When overriding a method:
1. Return type must be the same (or a covariant subtype)
2. Method name must be identical
3. Parameters must be identical (type, number, order)
4. `@Override` annotation helps catch these mistakes!

The second method isn't actually an override - it's an overload (different parameters). The `@Override` annotation correctly triggers a compiler error.

**Fixed Code:**
```java
public class Rectangle extends Shape {
    private double width;
    private double height;

    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }

    @Override
    public double calculateArea() {  // Fixed: Return double, not int
        return width * height;
    }

    @Override
    public void displayInfo() {  // Fixed: No parameters
        super.displayInfo();  // Call parent's version
        System.out.println("Width: " + width + ", Height: " + height);
    }

    public static void main(String[] args) {
        Rectangle rect = new Rectangle("Red", 5.5, 10.0);
        System.out.println("Area: " + rect.calculateArea());
        rect.displayInfo();
    }
}
```

**Key Learning:**
- Always use `@Override` annotation - it catches signature mismatches!
- Overriding requires identical signature (except covariant return types)
- Different parameters = overloading, not overriding
- Return type must match exactly (or be a subtype)

---

## Challenge 4: Polymorphism Type Casting Bug

**Broken Code:**
```java
public class Vehicle {
    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    public void start() {
        System.out.println(brand + " is starting...");
    }
}

public class Car extends Vehicle {
    private int doors;

    public Car(String brand, int doors) {
        super(brand);
        this.doors = doors;
    }

    public void openTrunk() {
        System.out.println("Trunk opening...");
    }
}

public class Motorcycle extends Vehicle {
    private boolean hasSidecar;

    public Motorcycle(String brand, boolean hasSidecar) {
        super(brand);
        this.hasSidecar = hasSidecar;
    }

    public void wheelie() {
        System.out.println("Doing a wheelie!");
    }
}

public class VehicleTest {
    public static void main(String[] args) {
        Vehicle[] vehicles = new Vehicle[2];
        vehicles[0] = new Car("Toyota", 4);
        vehicles[1] = new Motorcycle("Harley", false);

        for (Vehicle v : vehicles) {
            v.start();
            v.openTrunk();  // ERROR!
            v.wheelie();    // ERROR!
        }
    }
}
```

**Error Message:**
```
java: cannot find symbol
  symbol:   method openTrunk()
  location: variable v of type Vehicle

java: cannot find symbol
  symbol:   method wheelie()
  location: variable v of type Vehicle
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- What methods are available through a Vehicle reference?
- How can you call child-specific methods?
- Consider using `instanceof` operator

---

**Solution:**

**Bug Location:** Trying to call child-specific methods through Vehicle reference

**Explanation:**
When you use a parent reference (Vehicle), you can only call methods defined in the parent class. Even though the actual objects are Car and Motorcycle, the reference type (Vehicle) determines what methods are accessible at compile time.

`openTrunk()` is only in Car, and `wheelie()` is only in Motorcycle. Neither exists in Vehicle.

**Fixed Code:**
```java
public class VehicleTest {
    public static void main(String[] args) {
        Vehicle[] vehicles = new Vehicle[2];
        vehicles[0] = new Car("Toyota", 4);
        vehicles[1] = new Motorcycle("Harley", false);

        for (Vehicle v : vehicles) {
            v.start();  // Works - defined in Vehicle

            // Check actual type before calling child-specific methods
            if (v instanceof Car) {
                Car car = (Car) v;  // Downcast to Car
                car.openTrunk();
            } else if (v instanceof Motorcycle) {
                Motorcycle bike = (Motorcycle) v;  // Downcast to Motorcycle
                bike.wheelie();
            }
        }
    }
}
```

**Alternative Solution (Better Design):**
```java
// Add common methods to Vehicle class
public class Vehicle {
    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    public void start() {
        System.out.println(brand + " is starting...");
    }

    // Common method that each subclass overrides
    public void performSpecialAction() {
        System.out.println("Generic vehicle action");
    }
}

public class Car extends Vehicle {
    private int doors;

    public Car(String brand, int doors) {
        super(brand);
        this.doors = doors;
    }

    @Override
    public void performSpecialAction() {
        openTrunk();
    }

    public void openTrunk() {
        System.out.println("Trunk opening...");
    }
}

public class Motorcycle extends Vehicle {
    private boolean hasSidecar;

    public Motorcycle(String brand, boolean hasSidecar) {
        super(brand);
        this.hasSidecar = hasSidecar;
    }

    @Override
    public void performSpecialAction() {
        wheelie();
    }

    public void wheelie() {
        System.out.println("Doing a wheelie!");
    }
}

public class VehicleTest {
    public static void main(String[] args) {
        Vehicle[] vehicles = new Vehicle[2];
        vehicles[0] = new Car("Toyota", 4);
        vehicles[1] = new Motorcycle("Harley", false);

        for (Vehicle v : vehicles) {
            v.start();
            v.performSpecialAction();  // Polymorphism!
        }
    }
}
```

**Key Learning:**
- Reference type determines accessible methods, not object type
- Use `instanceof` to check actual type before downcasting
- Better design: Define common methods in parent, override in children
- Avoid excessive downcasting - it defeats polymorphism benefits

---

## Challenge 5: Constructor Call Order Confusion (Logic Bug)

**Code:**
```java
public class Parent {
    protected int value;

    public Parent() {
        value = 10;
        initialize();
        System.out.println("Parent constructor: value = " + value);
    }

    public void initialize() {
        value = 20;
    }
}

public class Child extends Parent {
    private int childValue;

    public Child() {
        super();
        childValue = 100;
        System.out.println("Child constructor: childValue = " + childValue);
    }

    @Override
    public void initialize() {
        childValue = childValue * 2;  // Problem: childValue not initialized yet!
        System.out.println("Child initialize: childValue = " + childValue);
    }

    public static void main(String[] args) {
        Child child = new Child();
        System.out.println("Final childValue: " + child.childValue);
    }
}
```

**Expected Output:**
```
Child initialize: childValue = 200
Parent constructor: value = 10
Child constructor: childValue = 100
Final childValue: 100
```

**Actual Output:**
```
Child initialize: childValue = 0
Parent constructor: value = 10
Child constructor: childValue = 100
Final childValue: 100
```

**Your Task:**
Find and fix the logic error

**Hints:**
- When is the child's `initialize()` method called?
- What is the default value of int in Java?
- Why is childValue 0 instead of 100?

---

**Solution:**

**Bug Location:** Calling overridable method `initialize()` from parent constructor

**Explanation:**
Constructor execution order:
1. Parent constructor starts
2. `value = 10`
3. `initialize()` is called - but it's overridden by Child!
4. Child's `initialize()` runs, but Child's fields aren't initialized yet (still at default value 0)
5. `childValue * 2` = `0 * 2` = `0`
6. Parent constructor completes
7. Child constructor runs
8. `childValue = 100` sets the value

**The problem:** When parent constructor calls `initialize()`, the child's version runs before the child's fields are initialized!

**Fixed Code:**
```java
public class Parent {
    protected int value;

    public Parent() {
        value = 10;
        initializeParent();  // Call non-overridable method
        System.out.println("Parent constructor: value = " + value);
    }

    // Make this final so it can't be overridden
    private void initializeParent() {
        value = 20;
    }

    // If child needs to override, call it separately
    public void initialize() {
        // Override this if needed, but don't call from constructor!
    }
}

public class Child extends Parent {
    private int childValue;

    public Child() {
        super();
        childValue = 100;
        initialize();  // Call after child fields are initialized
        System.out.println("Child constructor: childValue = " + childValue);
    }

    @Override
    public void initialize() {
        childValue = childValue * 2;
        System.out.println("Child initialize: childValue = " + childValue);
    }

    public static void main(String[] args) {
        Child child = new Child();
        System.out.println("Final childValue: " + child.childValue);
    }
}
```

**Better Output:**
```
Parent constructor: value = 10
Child initialize: childValue = 200
Child constructor: childValue = 200
Final childValue: 200
```

**Key Learning:**
- **NEVER call overridable methods from constructors!**
- Child's overridden method can execute before child's fields are initialized
- Use `final` or `private` methods in constructors
- Or call initialization methods after object is fully constructed
- This is a subtle bug that's hard to debug!

**Real-World Impact:**
In Selenium frameworks, if BasePage constructor calls an overridable method, subclass page objects might not be fully initialized, leading to NullPointerException or unexpected behavior.

---

## 🎯 Debugging Best Practices

**Lessons from Today's Challenges:**

1. **Always use `@Override`** - Catches signature mismatches at compile time
2. **Remember `super()`** - Parent constructors must be called
3. **Use `protected` wisely** - Balance encapsulation with child class needs
4. **Think about reference vs object type** - Determines accessible methods
5. **Avoid calling overridable methods in constructors** - Child might not be initialized
6. **Use `instanceof` before downcasting** - Prevent ClassCastException
7. **Read error messages carefully** - They tell you exactly what's wrong

**Debugging Tools:**
- IntelliJ's debugger: Step through constructors to see initialization order
- Breakpoints: See which method version is actually called
- Call stack: Understand inheritance hierarchy and method calls
- Type hierarchy view: Visualize parent-child relationships

---

## 📊 Debugging Skills Assessment

Rate your understanding:

| Concept | Before | After | Improved? |
|---------|--------|-------|-----------|
| Constructor chaining with super() | __/10 | __/10 | ☐ |
| Access modifiers in inheritance | __/10 | __/10 | ☐ |
| Method override rules | __/10 | __/10 | ☐ |
| Polymorphism and type casting | __/10 | __/10 | ☐ |
| Constructor call order | __/10 | __/10 | ☐ |

**Areas to review:** ______________________________

**Confidence in debugging inheritance issues:** __/10

---

## 🚀 Next Steps

**You've debugged common inheritance issues! Now:**
1. Move to **Day-5-Project-Guide.md** for the Vehicle Hierarchy project
2. Apply what you learned about avoiding these bugs
3. Review **Day-5-Cheat-Sheet.md** for quick reference

**Remember:** The best debugging skill is writing code that doesn't need debugging! Use `@Override`, call `super()` correctly, and avoid calling overridable methods in constructors.

---

**Great debugging work! These skills will save you hours when building Selenium frameworks!** 🔍

---
