# DAY 6 MINI PROJECT: SHAPE CALCULATOR

## 🎯 Project Overview

**What You're Building:**
A comprehensive Shape Calculator application that demonstrates abstraction, interfaces, and polymorphism. You'll create a hierarchy of geometric shapes with the ability to calculate area, perimeter, and compare shapes.

**Why This Project:**
This project perfectly demonstrates how interfaces and abstract classes work together. You'll understand why Selenium uses WebDriver as an interface and why your framework might use BasePage as an abstract class.

**Time Required:** 30-45 minutes

**Skills Practiced:**
- Creating and implementing interfaces
- Designing abstract classes
- Implementing concrete classes
- Polymorphism with interface/abstract references
- Working with arrays of abstract types
- Real-world OOP design patterns

---

## 📋 Requirements

### Must-Have Features:

1. **Interface `Measurable`**
   - Defines contract for measurable objects
   - Methods: `getArea()`, `getPerimeter()`

2. **Abstract Class `Shape`**
   - Implements `Measurable` interface
   - Has fields: `name`, `color`
   - Has constructor to initialize fields
   - Has concrete method `displayInfo()` to show all shape information
   - Leaves area/perimeter calculation to concrete classes

3. **Concrete Shape Classes**
   - `Circle` - has radius
   - `Rectangle` - has length and width
   - `Triangle` - has three sides
   - Each implements area and perimeter calculations correctly

4. **Shape Calculator Main Program**
   - Creates multiple shapes
   - Stores them in an array
   - Displays information for all shapes
   - Finds and displays the largest shape by area
   - Demonstrates polymorphism

### Nice-to-Have (Bonus):

1. **Input Validation**
   - No negative dimensions
   - Triangle inequality validation (sum of any two sides > third side)

2. **Additional Shapes**
   - `Square` (extends Rectangle)
   - `EquilateralTriangle` (extends Triangle)

3. **Comparison Features**
   - Implement `Comparable` interface
   - Sort shapes by area
   - Find smallest/largest perimeter

4. **User Input**
   - Accept shape dimensions from user
   - Interactive menu to create shapes

---

## 🏗️ Project Structure

**Classes Needed:**
1. `Measurable.java` (Interface) - Defines measurement contract
2. `Shape.java` (Abstract Class) - Base class for all shapes
3. `Circle.java` (Concrete Class) - Circle implementation
4. `Rectangle.java` (Concrete Class) - Rectangle implementation
5. `Triangle.java` (Concrete Class) - Triangle implementation
6. `ShapeCalculator.java` (Main Class) - Driver program

**Methods to Implement:**

**In Measurable:**
- `double getArea()`
- `double getPerimeter()`

**In Shape:**
- `Shape(String name, String color)` - constructor
- `void displayInfo()` - show all information
- `String getName()` - getter
- `String getColor()` - getter

**In Each Concrete Shape:**
- Constructor with dimensions
- `getArea()` implementation
- `getPerimeter()` implementation

---

## 📝 Step-by-Step Guide

### Step 1: Create the Measurable Interface

**What to do:**
Define the contract that all measurable objects must follow.

**Code Template:**
```java
public interface Measurable {
    // TODO: Add method signature for getArea() that returns double

    // TODO: Add method signature for getPerimeter() that returns double
}
```

**Testing:**
- Compilation should succeed (interface only defines contract)
- No objects created yet

---

### Step 2: Create the Abstract Shape Class

**What to do:**
Create base class for all shapes with common properties and behavior.

**Code Template:**
```java
public abstract class Shape implements Measurable {
    // TODO: Add protected fields for name and color

    // TODO: Add constructor that accepts name and color

    // TODO: Add getters for name and color

    // TODO: Add concrete method displayInfo() that prints:
    //       - Shape name
    //       - Color
    //       - Area (call getArea())
    //       - Perimeter (call getPerimeter())

    // Note: getArea() and getPerimeter() inherited from Measurable
    // Will be implemented by concrete classes
}
```

**Expected displayInfo() Output Format:**
```
Shape: Circle
Color: Red
Area: 78.54
Perimeter: 31.42
```

**Testing:**
- Compilation should succeed
- Cannot create Shape objects (it's abstract)

---

### Step 3: Implement Circle Class

**What to do:**
Create Circle class with radius and implement area/perimeter calculations.

**Formulas:**
- Area = π × r²
- Perimeter (Circumference) = 2 × π × r

**Code Template:**
```java
public class Circle extends Shape {
    private double radius;

    public Circle(double radius, String color) {
        // TODO: Call super constructor with "Circle" as name and color
        // TODO: Initialize radius field
    }

    @Override
    public double getArea() {
        // TODO: Return area using Math.PI and radius
        return 0;
    }

    @Override
    public double getPerimeter() {
        // TODO: Return circumference using Math.PI and radius
        return 0;
    }

    // TODO: Add getter for radius (useful for debugging)
    public double getRadius() {
        return radius;
    }
}
```

**Testing:**
```java
// Test in main method
Circle circle = new Circle(5, "Red");
circle.displayInfo();

// Expected output:
// Shape: Circle
// Color: Red
// Area: 78.54
// Perimeter: 31.42
```

---

### Step 4: Implement Rectangle Class

**What to do:**
Create Rectangle class with length and width.

**Formulas:**
- Area = length × width
- Perimeter = 2 × (length + width)

**Code Template:**
```java
public class Rectangle extends Shape {
    private double length;
    private double width;

    public Rectangle(double length, double width, String color) {
        // TODO: Call super constructor with "Rectangle" as name and color
        // TODO: Initialize length and width
    }

    @Override
    public double getArea() {
        // TODO: Return area
        return 0;
    }

    @Override
    public double getPerimeter() {
        // TODO: Return perimeter
        return 0;
    }

    // TODO: Add getters for length and width
}
```

**Testing:**
```java
Rectangle rectangle = new Rectangle(4, 6, "Blue");
rectangle.displayInfo();

// Expected output:
// Shape: Rectangle
// Color: Blue
// Area: 24.00
// Perimeter: 20.00
```

---

### Step 5: Implement Triangle Class

**What to do:**
Create Triangle class with three sides and implement Heron's formula for area.

**Formulas:**
- Area = √[s(s-a)(s-b)(s-c)] where s = (a+b+c)/2 (Heron's formula)
- Perimeter = a + b + c

**Code Template:**
```java
public class Triangle extends Shape {
    private double side1;
    private double side2;
    private double side3;

    public Triangle(double side1, double side2, double side3, String color) {
        // TODO: Call super constructor with "Triangle" as name and color
        // TODO: Initialize all three sides
    }

    @Override
    public double getArea() {
        // TODO: Calculate semi-perimeter: s = (side1 + side2 + side3) / 2
        // TODO: Calculate area using Heron's formula
        // TODO: Use Math.sqrt() for square root
        return 0;
    }

    @Override
    public double getPerimeter() {
        // TODO: Return sum of all sides
        return 0;
    }

    // TODO: Add method to validate triangle (bonus)
    public boolean isValidTriangle() {
        // Sum of any two sides must be greater than third side
        return (side1 + side2 > side3) &&
               (side2 + side3 > side1) &&
               (side1 + side3 > side2);
    }
}
```

**Testing:**
```java
Triangle triangle = new Triangle(3, 4, 5, "Green");
triangle.displayInfo();

// Expected output:
// Shape: Triangle
// Color: Green
// Area: 6.00
// Perimeter: 12.00
```

---

### Step 6: Create the Main ShapeCalculator Program

**What to do:**
Create main program that demonstrates all features and polymorphism.

**Code Template:**
```java
public class ShapeCalculator {
    public static void main(String[] args) {
        System.out.println("=====================================");
        System.out.println("    SHAPE CALCULATOR APPLICATION");
        System.out.println("=====================================\n");

        // TODO: Create different shapes
        Shape circle = new Circle(5, "Red");
        Shape rectangle = new Rectangle(4, 6, "Blue");
        Shape triangle = new Triangle(3, 4, 5, "Green");
        Shape largeCircle = new Circle(10, "Yellow");

        // TODO: Store shapes in array (polymorphism!)
        Shape[] shapes = {circle, rectangle, triangle, largeCircle};

        // TODO: Display all shapes
        System.out.println("All Shapes:");
        System.out.println("-----------");
        for (Shape shape : shapes) {
            shape.displayInfo();
            System.out.println();
        }

        // TODO: Find shape with largest area
        Shape largest = findLargestShape(shapes);
        System.out.println("Largest Shape by Area:");
        System.out.println("----------------------");
        largest.displayInfo();

        // TODO: Calculate total area
        double totalArea = calculateTotalArea(shapes);
        System.out.printf("\nTotal Area of All Shapes: %.2f\n", totalArea);

        // TODO: Demonstrate interface reference
        System.out.println("\nDemonstrating Measurable Interface:");
        System.out.println("-----------------------------------");
        Measurable m = circle;  // Interface reference!
        System.out.printf("Circle area via Measurable: %.2f\n", m.getArea());
    }

    // TODO: Implement helper method to find largest shape
    public static Shape findLargestShape(Shape[] shapes) {
        Shape largest = shapes[0];
        for (Shape shape : shapes) {
            if (shape.getArea() > largest.getArea()) {
                largest = shape;
            }
        }
        return largest;
    }

    // TODO: Implement helper method to calculate total area
    public static double calculateTotalArea(Shape[] shapes) {
        double total = 0;
        for (Shape shape : shapes) {
            total += shape.getArea();
        }
        return total;
    }
}
```

**Testing:**
Run the program and verify all outputs are correct.

---

## ✅ Testing Checklist

Test your project against these scenarios:

- [ ] **Circle Calculations**
  - Circle(5, "Red") → Area: 78.54, Perimeter: 31.42
  - Circle(10, "Yellow") → Area: 314.16, Perimeter: 62.83

- [ ] **Rectangle Calculations**
  - Rectangle(4, 6, "Blue") → Area: 24.00, Perimeter: 20.00
  - Rectangle(5, 5, "Purple") → Area: 25.00, Perimeter: 20.00

- [ ] **Triangle Calculations**
  - Triangle(3, 4, 5, "Green") → Area: 6.00, Perimeter: 12.00
  - Triangle(5, 5, 5, "Orange") → Area: 10.83, Perimeter: 15.00

- [ ] **Polymorphism**
  - Can store all shapes in Shape[] array
  - Can reference shapes as Measurable interface
  - displayInfo() works for all shape types

- [ ] **Edge Cases** (Bonus)
  - Circle(0, "Black") → Area: 0.00, Perimeter: 0.00
  - Rectangle(1, 1, "White") → Area: 1.00, Perimeter: 4.00
  - Triangle(1, 2, 10, "Red") → Invalid triangle (should handle gracefully)

**Expected Complete Output:**
```
=====================================
    SHAPE CALCULATOR APPLICATION
=====================================

All Shapes:
-----------
Shape: Circle
Color: Red
Area: 78.54
Perimeter: 31.42

Shape: Rectangle
Color: Blue
Area: 24.00
Perimeter: 20.00

Shape: Triangle
Color: Green
Area: 6.00
Perimeter: 12.00

Shape: Circle
Color: Yellow
Area: 314.16
Perimeter: 62.83

Largest Shape by Area:
----------------------
Shape: Circle
Color: Yellow
Area: 314.16
Perimeter: 62.83

Total Area of All Shapes: 422.70

Demonstrating Measurable Interface:
-----------------------------------
Circle area via Measurable: 78.54
```

---

## 🎓 Complete Solution (Only Look After Attempting!)

### Measurable.java
```java
public interface Measurable {
    double getArea();
    double getPerimeter();
}
```

### Shape.java
```java
public abstract class Shape implements Measurable {
    protected String name;
    protected String color;

    public Shape(String name, String color) {
        this.name = name;
        this.color = color;
    }

    public String getName() {
        return name;
    }

    public String getColor() {
        return color;
    }

    public void displayInfo() {
        System.out.println("Shape: " + name);
        System.out.println("Color: " + color);
        System.out.printf("Area: %.2f\n", getArea());
        System.out.printf("Perimeter: %.2f\n", getPerimeter());
    }

    // getArea() and getPerimeter() are inherited from Measurable
    // Must be implemented by concrete classes
}
```

### Circle.java
```java
public class Circle extends Shape {
    private double radius;

    public Circle(double radius, String color) {
        super("Circle", color);
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

    public double getRadius() {
        return radius;
    }
}
```

### Rectangle.java
```java
public class Rectangle extends Shape {
    private double length;
    private double width;

    public Rectangle(double length, double width, String color) {
        super("Rectangle", color);
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

    public double getLength() {
        return length;
    }

    public double getWidth() {
        return width;
    }
}
```

### Triangle.java
```java
public class Triangle extends Shape {
    private double side1;
    private double side2;
    private double side3;

    public Triangle(double side1, double side2, double side3, String color) {
        super("Triangle", color);
        this.side1 = side1;
        this.side2 = side2;
        this.side3 = side3;
    }

    @Override
    public double getArea() {
        // Heron's formula
        double s = (side1 + side2 + side3) / 2;  // semi-perimeter
        return Math.sqrt(s * (s - side1) * (s - side2) * (s - side3));
    }

    @Override
    public double getPerimeter() {
        return side1 + side2 + side3;
    }

    public boolean isValidTriangle() {
        return (side1 + side2 > side3) &&
               (side2 + side3 > side1) &&
               (side1 + side3 > side2);
    }
}
```

### ShapeCalculator.java
```java
public class ShapeCalculator {
    public static void main(String[] args) {
        System.out.println("=====================================");
        System.out.println("    SHAPE CALCULATOR APPLICATION");
        System.out.println("=====================================\n");

        // Create different shapes
        Shape circle = new Circle(5, "Red");
        Shape rectangle = new Rectangle(4, 6, "Blue");
        Shape triangle = new Triangle(3, 4, 5, "Green");
        Shape largeCircle = new Circle(10, "Yellow");

        // Store shapes in array - POLYMORPHISM!
        Shape[] shapes = {circle, rectangle, triangle, largeCircle};

        // Display all shapes
        System.out.println("All Shapes:");
        System.out.println("-----------");
        for (Shape shape : shapes) {
            shape.displayInfo();
            System.out.println();
        }

        // Find shape with largest area
        Shape largest = findLargestShape(shapes);
        System.out.println("Largest Shape by Area:");
        System.out.println("----------------------");
        largest.displayInfo();

        // Calculate total area
        double totalArea = calculateTotalArea(shapes);
        System.out.printf("\nTotal Area of All Shapes: %.2f\n", totalArea);

        // Demonstrate interface reference
        System.out.println("\nDemonstrating Measurable Interface:");
        System.out.println("-----------------------------------");
        Measurable m = circle;  // Interface reference!
        System.out.printf("Circle area via Measurable: %.2f\n", m.getArea());
    }

    public static Shape findLargestShape(Shape[] shapes) {
        if (shapes == null || shapes.length == 0) {
            return null;
        }

        Shape largest = shapes[0];
        for (Shape shape : shapes) {
            if (shape.getArea() > largest.getArea()) {
                largest = shape;
            }
        }
        return largest;
    }

    public static double calculateTotalArea(Shape[] shapes) {
        double total = 0;
        for (Shape shape : shapes) {
            total += shape.getArea();
        }
        return total;
    }
}
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these:

### Enhancement 1: Input Validation
Add validation to prevent invalid shapes:

```java
public class Circle extends Shape {
    private double radius;

    public Circle(double radius, String color) {
        super("Circle", color);

        if (radius <= 0) {
            throw new IllegalArgumentException("Radius must be positive");
        }

        this.radius = radius;
    }
    // ... rest of code
}
```

### Enhancement 2: Square Class
Create Square as a special case of Rectangle:

```java
public class Square extends Rectangle {
    public Square(double side, String color) {
        super(side, side, color);  // length = width = side
        // Override name to "Square"
    }
}
```

### Enhancement 3: Sort Shapes by Area
Implement Comparable interface:

```java
public abstract class Shape implements Measurable, Comparable<Shape> {
    // ... existing code ...

    @Override
    public int compareTo(Shape other) {
        return Double.compare(this.getArea(), other.getArea());
    }
}

// In main:
Arrays.sort(shapes);  // Sorts by area!
```

### Enhancement 4: User Input
Add interactive shape creation:

```java
import java.util.Scanner;

public class InteractiveShapeCalculator {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.println("Enter shape type (circle/rectangle/triangle): ");
        String type = scanner.nextLine();

        Shape shape = null;

        if (type.equalsIgnoreCase("circle")) {
            System.out.print("Enter radius: ");
            double radius = scanner.nextDouble();
            scanner.nextLine();  // consume newline
            System.out.print("Enter color: ");
            String color = scanner.nextLine();
            shape = new Circle(radius, color);
        }
        // ... similar for rectangle and triangle

        if (shape != null) {
            shape.displayInfo();
        }

        scanner.close();
    }
}
```

### Enhancement 5: Shape Statistics
Add more analysis methods:

```java
public static void printStatistics(Shape[] shapes) {
    System.out.println("\nShape Statistics:");
    System.out.println("----------------");
    System.out.println("Total shapes: " + shapes.length);
    System.out.printf("Average area: %.2f\n", calculateTotalArea(shapes) / shapes.length);
    System.out.printf("Largest area: %.2f\n", findLargestShape(shapes).getArea());
    System.out.printf("Smallest area: %.2f\n", findSmallestShape(shapes).getArea());
}
```

---

## 💼 How This Relates to Selenium

This project demonstrates the exact same patterns used in Selenium:

### Pattern 1: Interface for Contract (WebDriver)
```java
// In our project:
Measurable m = new Circle(5, "Red");  // Interface reference
m.getArea();  // Don't care if it's Circle, Rectangle, or Triangle

// In Selenium:
WebDriver driver = new ChromeDriver();  // Interface reference
driver.get("https://google.com");  // Don't care if Chrome, Firefox, or Edge
```

### Pattern 2: Abstract Class for Shared Code (RemoteWebDriver)
```java
// In our project:
abstract class Shape {
    // Common displayInfo() for all shapes
    // Abstract methods for specific calculations
}

// In Selenium:
abstract class RemoteWebDriver implements WebDriver {
    // Common code for all remote browsers
    // Browser-specific implementations in subclasses
}
```

### Pattern 3: Polymorphism for Flexibility
```java
// In our project:
Shape[] shapes = {circle, rectangle, triangle};  // Different types, same array
for (Shape shape : shapes) {
    shape.displayInfo();  // Works for all!
}

// In Selenium framework:
WebDriver[] browsers = {new ChromeDriver(), new FirefoxDriver(), new EdgeDriver()};
for (WebDriver browser : browsers) {
    browser.get("https://google.com");  // Same test, all browsers!
}
```

### Pattern 4: Factory Pattern
```java
// Bonus: Create a shape factory
public class ShapeFactory {
    public static Shape createShape(String type, double... dimensions) {
        switch(type.toLowerCase()) {
            case "circle":
                return new Circle(dimensions[0], "Default");
            case "rectangle":
                return new Rectangle(dimensions[0], dimensions[1], "Default");
            case "triangle":
                return new Triangle(dimensions[0], dimensions[1], dimensions[2], "Default");
            default:
                throw new IllegalArgumentException("Unknown shape type");
        }
    }
}

// Usage:
Shape s = ShapeFactory.createShape("circle", 5);

// This is exactly how BrowserFactory works in Selenium!
```

---

## 🎯 Project Completion Checklist

- [ ] All interfaces and classes compile without errors
- [ ] All shapes calculate area correctly
- [ ] All shapes calculate perimeter correctly
- [ ] Polymorphism works (Shape[] array holds all types)
- [ ] displayInfo() works for all shapes
- [ ] findLargestShape() identifies correct shape
- [ ] calculateTotalArea() sums correctly
- [ ] Interface reference (Measurable) works
- [ ] Code is well-commented
- [ ] Output matches expected format

**Congratulations!** You've built a real OOP application using abstraction principles. This is the foundation for understanding Selenium WebDriver architecture and building maintainable test frameworks!

---
