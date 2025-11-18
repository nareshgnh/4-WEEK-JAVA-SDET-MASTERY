# DAY 1 MINI PROJECT: SIMPLE CALCULATOR

## 🎯 Project Overview

**What You're Building:**
A command-line calculator that performs basic arithmetic operations (addition, subtraction, multiplication, division, modulus) on two numbers with proper error handling and formatted output.

**Why This Project:**
- Reinforces all Day 1 concepts: variables, data types, operators
- Builds confidence with Java syntax and structure
- Creates your first portfolio-ready Java program
- Simulates test data calculations common in automation frameworks

**Time Required:** 30-45 minutes

**Skills Practiced:**
- Variable declaration and initialization
- Using arithmetic operators
- Type casting and conversions
- String formatting and output
- Basic conditional logic (bonus)
- Code organization and comments

---

## 📋 Requirements

### Must-Have Features:
1. **Accept two numbers as input** - hardcoded for now (we'll add user input next week)
2. **Perform all basic operations:**
   - Addition (+)
   - Subtraction (-)
   - Multiplication (*)
   - Division (/)
   - Modulus (%)
3. **Display results in a formatted, professional manner**
4. **Handle division by zero** - display error message instead of crashing
5. **Use appropriate data types** - double for numbers to handle decimals

### Nice-to-Have (Bonus):
1. Calculate and display the average of the two numbers
2. Format all results to 2 decimal places
3. Add visual separators and symbols for better readability
4. Compare the two numbers (which is larger)

---

## 🏗️ Project Structure

**Files Needed:**
1. `SimpleCalculator.java` - Main calculator class with all operations

**Methods to Implement:**
All code will be in the `main` method for now (we'll learn about custom methods in Day 4).

**Variables Needed:**
- `num1` (double) - First number
- `num2` (double) - Second number
- Result variables for each operation (double)

---

## 📝 Step-by-Step Guide

### Step 1: Set Up Project Structure

**Create a new Java class:**
1. In IntelliJ IDEA, right-click on `src` folder
2. New → Java Class
3. Name it `SimpleCalculator`
4. IntelliJ creates the class structure automatically

**Starter Code Framework:**
```java
public class SimpleCalculator {
    public static void main(String[] args) {
        // This is where you'll write your code
    }
}
```

---

### Step 2: Declare Input Variables

**What to do:**
Declare two variables to hold the numbers you'll calculate with. Use `double` type to handle both whole numbers and decimals.

**Code Template:**
```java
public class SimpleCalculator {
    public static void main(String[] args) {
        // Input numbers
        double num1 = 20.0;
        double num2 = 4.0;

        // TODO: Rest of the code goes here
    }
}
```

**Testing:**
- Try with: `20.0` and `4.0` (easy to verify results)
- Later test with: `15.5` and `3.2` (decimals)
- Edge case: `10.0` and `0.0` (division by zero)

**Why double?**
- Can handle both whole numbers and decimals
- More flexible than `int` for calculations
- Common in test automation for metrics, percentages, response times

---

### Step 3: Add Header Display

**What to do:**
Display a professional header showing the calculator title and input numbers.

**Code Template:**
```java
// Print header
System.out.println("===== SIMPLE CALCULATOR =====");
System.out.println("Number 1: " + num1);
System.out.println("Number 2: " + num2);
System.out.println("============================");
```

**Expected Output:**
```
===== SIMPLE CALCULATOR =====
Number 1: 20.0
Number 2: 4.0
============================
```

**Why this matters:**
- Professional output formatting
- Clear display of inputs (crucial for debugging)
- Similar to test execution reports

---

### Step 4: Implement Addition

**What to do:**
Calculate the sum of the two numbers and display the result.

**Code Template:**
```java
// Addition
double sum = num1 + num2;
System.out.println("Addition: " + num1 + " + " + num2 + " = " + sum);
```

**Expected Output:**
```
Addition: 20.0 + 4.0 = 24.0
```

**Testing:**
- Verify: 20.0 + 4.0 = 24.0 ✓
- Try negative numbers: -5.0 + 3.0 = -2.0 ✓

---

### Step 5: Implement Subtraction

**What to do:**
Calculate the difference between the two numbers and display the result.

**Code Template:**
```java
// Subtraction
double difference = num1 - num2;
System.out.println("Subtraction: " + num1 + " - " + num2 + " = " + difference);
```

**Expected Output:**
```
Subtraction: 20.0 - 4.0 = 16.0
```

**Testing:**
- Verify: 20.0 - 4.0 = 16.0 ✓
- Note: Order matters (num1 - num2 ≠ num2 - num1)

---

### Step 6: Implement Multiplication

**What to do:**
Calculate the product of the two numbers and display the result.

**Code Template:**
```java
// Multiplication
double product = num1 * num2;
System.out.println("Multiplication: " + num1 + " * " + num2 + " = " + product);
```

**Expected Output:**
```
Multiplication: 20.0 * 4.0 = 80.0
```

**Testing:**
- Verify: 20.0 * 4.0 = 80.0 ✓
- Try with 0: 20.0 * 0.0 = 0.0 ✓

---

### Step 7: Implement Division (with Error Handling)

**What to do:**
Calculate the quotient, but first check if the divisor (num2) is zero. If it is, display an error message instead of dividing.

**Code Template:**
```java
// Division (with zero check)
if (num2 != 0) {
    double quotient = num1 / num2;
    System.out.println("Division: " + num1 + " / " + num2 + " = " + quotient);
} else {
    System.out.println("Division: Error - Cannot divide by zero!");
}
```

**Expected Output (normal case):**
```
Division: 20.0 / 4.0 = 5.0
```

**Expected Output (error case, when num2 = 0):**
```
Division: Error - Cannot divide by zero!
```

**Testing:**
- Normal: 20.0 / 4.0 = 5.0 ✓
- Error case: 20.0 / 0.0 → Error message ✓
- Decimal result: 10.0 / 3.0 = 3.333... ✓

**Why this matters:**
- Prevents runtime errors (ArithmeticException)
- Input validation is crucial in test automation
- Professional error handling

---

### Step 8: Implement Modulus

**What to do:**
Calculate the remainder when num1 is divided by num2.

**Code Template:**
```java
// Modulus
double remainder = num1 % num2;
System.out.println("Modulus: " + num1 + " % " + num2 + " = " + remainder);
```

**Expected Output:**
```
Modulus: 20.0 % 4.0 = 0.0
```

**What is modulus?**
- `20 % 4 = 0` (20 divides evenly by 4, no remainder)
- `21 % 4 = 1` (21 ÷ 4 = 5 remainder 1)
- `7 % 3 = 1` (7 ÷ 3 = 2 remainder 1)

**Testing:**
- 20.0 % 4.0 = 0.0 (divides evenly)
- 21.0 % 4.0 = 1.0 (remainder 1)
- 10.0 % 3.0 = 1.0 (remainder 1)

**Use in test automation:**
- Check if test count is even/odd: `testCount % 2 == 0`
- Distribute tests across threads: `testIndex % threadCount`

---

### Step 9: Add Final Touches

**What to do:**
Add a closing separator for professional output.

**Code Template:**
```java
// Footer
System.out.println("============================");
System.out.println("Calculation complete!");
```

**Expected Output:**
```
============================
Calculation complete!
```

---

## ✅ Testing Checklist

Test your calculator against these scenarios:

### Test Case 1: Normal Numbers
- [ ] Input: num1 = 20.0, num2 = 4.0
- [ ] Expected: Addition = 24.0, Subtraction = 16.0, Multiplication = 80.0, Division = 5.0, Modulus = 0.0

### Test Case 2: Decimal Numbers
- [ ] Input: num1 = 15.5, num2 = 3.2
- [ ] Expected: All operations work with decimals

### Test Case 3: Division by Zero
- [ ] Input: num1 = 10.0, num2 = 0.0
- [ ] Expected: Division shows error message, other operations work normally

### Test Case 4: Negative Numbers
- [ ] Input: num1 = -10.0, num2 = 5.0
- [ ] Expected: Addition = -5.0, Subtraction = -15.0, Multiplication = -50.0, Division = -2.0

### Test Case 5: Zero as First Number
- [ ] Input: num1 = 0.0, num2 = 5.0
- [ ] Expected: All operations work (most results are 0.0)

**Expected Behavior for Test Case 1:**
```
===== SIMPLE CALCULATOR =====
Number 1: 20.0
Number 2: 4.0
============================
Addition: 20.0 + 4.0 = 24.0
Subtraction: 20.0 - 4.0 = 16.0
Multiplication: 20.0 * 4.0 = 80.0
Division: 20.0 / 4.0 = 5.0
Modulus: 20.0 % 4.0 = 0.0
============================
Calculation complete!
```

---

## 🎓 Complete Solution (Only Look After Attempting!)

### Basic Implementation (Meets All Requirements)

```java
public class SimpleCalculator {
    public static void main(String[] args) {
        // Input numbers
        double num1 = 20.0;
        double num2 = 4.0;

        // Header
        System.out.println("===== SIMPLE CALCULATOR =====");
        System.out.println("Number 1: " + num1);
        System.out.println("Number 2: " + num2);
        System.out.println("============================");

        // Addition
        double sum = num1 + num2;
        System.out.println("Addition: " + num1 + " + " + num2 + " = " + sum);

        // Subtraction
        double difference = num1 - num2;
        System.out.println("Subtraction: " + num1 + " - " + num2 + " = " + difference);

        // Multiplication
        double product = num1 * num2;
        System.out.println("Multiplication: " + num1 + " * " + num2 + " = " + product);

        // Division (with zero check)
        if (num2 != 0) {
            double quotient = num1 / num2;
            System.out.println("Division: " + num1 + " / " + num2 + " = " + quotient);
        } else {
            System.out.println("Division: Error - Cannot divide by zero!");
        }

        // Modulus
        double remainder = num1 % num2;
        System.out.println("Modulus: " + num1 + " % " + num2 + " = " + remainder);

        // Footer
        System.out.println("============================");
        System.out.println("Calculation complete!");
    }
}
```

---

### Advanced Implementation (With All Bonus Features)

```java
public class SimpleCalculatorAdvanced {
    public static void main(String[] args) {
        // Input numbers
        double num1 = 20.0;
        double num2 = 4.0;

        // Header
        System.out.println("╔════════════════════════════╗");
        System.out.println("║   SIMPLE CALCULATOR v1.0   ║");
        System.out.println("╚════════════════════════════╝");
        System.out.println();
        System.out.println("Number 1: " + num1);
        System.out.println("Number 2: " + num2);
        System.out.println("----------------------------");

        // Arithmetic Operations
        double sum = num1 + num2;
        System.out.println("✓ Addition:       " + num1 + " + " + num2 + " = " + String.format("%.2f", sum));

        double difference = num1 - num2;
        System.out.println("✓ Subtraction:    " + num1 + " - " + num2 + " = " + String.format("%.2f", difference));

        double product = num1 * num2;
        System.out.println("✓ Multiplication: " + num1 + " * " + num2 + " = " + String.format("%.2f", product));

        // Division with error handling
        if (num2 != 0) {
            double quotient = num1 / num2;
            System.out.println("✓ Division:       " + num1 + " / " + num2 + " = " + String.format("%.2f", quotient));
        } else {
            System.out.println("✗ Division:       Error - Cannot divide by zero!");
        }

        double remainder = num1 % num2;
        System.out.println("✓ Modulus:        " + num1 + " % " + num2 + " = " + String.format("%.2f", remainder));

        // Bonus Features
        System.out.println("----------------------------");
        System.out.println("BONUS CALCULATIONS:");

        // Average
        double average = (num1 + num2) / 2;
        System.out.println("✓ Average:        " + String.format("%.2f", average));

        // Comparison
        if (num1 > num2) {
            System.out.println("✓ Comparison:     " + num1 + " is greater than " + num2);
        } else if (num1 < num2) {
            System.out.println("✓ Comparison:     " + num1 + " is less than " + num2);
        } else {
            System.out.println("✓ Comparison:     Both numbers are equal");
        }

        // Footer
        System.out.println("============================");
        System.out.println("✓ All calculations complete!");
        System.out.println();
    }
}
```

**Advanced Features Explained:**
- `String.format("%.2f", number)` → Formats to 2 decimal places (24.00)
- Unicode symbols `✓` and `✗` for visual appeal
- Box drawing characters for professional header
- Comparison logic to determine which number is larger
- Better alignment and spacing

---

## 🚀 Enhancement Ideas

Want to take it further? Try these challenges:

### Enhancement 1: More Operations
Add these calculations:
- Power: num1^num2 (use `Math.pow(num1, num2)`)
- Square root: √num1 (use `Math.sqrt(num1)`)
- Absolute difference: |num1 - num2| (use `Math.abs(num1 - num2)`)

**Code:**
```java
double power = Math.pow(num1, num2);
System.out.println("Power: " + num1 + "^" + num2 + " = " + String.format("%.2f", power));

double sqrt1 = Math.sqrt(num1);
System.out.println("Square Root of " + num1 + " = " + String.format("%.2f", sqrt1));
```

---

### Enhancement 2: Constants for Formatting
Use constants for repeated strings:

```java
public class SimpleCalculator {
    public static void main(String[] args) {
        // Constants
        final String SEPARATOR = "============================";
        final String CHECK_MARK = "✓";

        double num1 = 20.0;
        double num2 = 4.0;

        System.out.println(SEPARATOR);
        // Rest of code uses SEPARATOR and CHECK_MARK
    }
}
```

---

### Enhancement 3: Scientific Calculator Mode
Add trigonometric functions:
```java
// Convert to radians first (Math functions use radians)
double radians = Math.toRadians(num1);

double sine = Math.sin(radians);
double cosine = Math.cos(radians);
double tangent = Math.tan(radians);

System.out.println("Sin(" + num1 + "°) = " + String.format("%.4f", sine));
System.out.println("Cos(" + num1 + "°) = " + String.format("%.4f", cosine));
System.out.println("Tan(" + num1 + "°) = " + String.format("%.4f", tangent));
```

---

## 💼 How This Relates to Selenium

**Test Automation Parallels:**

1. **Input Validation (Division by Zero Check)**
   ```java
   // Calculator
   if (num2 != 0) { /* divide */ }

   // Selenium equivalent
   if (driver.findElements(By.id("loginButton")).size() > 0) {
       // Element exists, safe to interact
       driver.findElement(By.id("loginButton")).click();
   }
   ```

2. **Formatted Output (String.format)**
   ```java
   // Calculator
   String.format("%.2f", passRate);

   // Test reports
   String.format("Pass Rate: %.2f%% (%d/%d tests)", passRate, passed, total);
   ```

3. **Constants**
   ```java
   // Calculator
   final String SEPARATOR = "==========";

   // Test framework
   public static final String BASE_URL = "https://www.example.com";
   public static final int TIMEOUT = 10;
   ```

4. **Calculations in Test Metrics**
   ```java
   // Similar to calculator operations
   int totalTests = 100;
   int passedTests = 85;
   double passRate = (passedTests * 100.0) / totalTests;  // 85.0%

   long startTime = System.currentTimeMillis();
   // run test
   long endTime = System.currentTimeMillis();
   long executionTime = endTime - startTime;  // milliseconds
   ```

---

## 🎯 Project Completion Checklist

Before marking this project complete:

- [ ] Program compiles without errors
- [ ] All 5 arithmetic operations implemented
- [ ] Division by zero handled properly
- [ ] Output is formatted and professional
- [ ] Tested with at least 3 different input sets
- [ ] Code has meaningful variable names
- [ ] Code includes comments
- [ ] Ready to push to GitHub

---

## 📸 GitHub Ready!

This calculator is portfolio-quality. Here's how to showcase it:

**README.md for the project:**
```markdown
# Simple Java Calculator

A command-line calculator demonstrating Java fundamentals.

## Features
- Basic arithmetic operations (+, -, *, /, %)
- Division by zero error handling
- Formatted output with 2 decimal precision
- Clean, professional code structure

## Technologies
- Java 17
- IntelliJ IDEA

## Usage
```java
double num1 = 20.0;
double num2 = 4.0;
// Run SimpleCalculator.main()
```

## Learning Goals
Day 1 project of my 4-week Java SDET mastery plan.
Demonstrates understanding of:
- Variables and data types
- Operators
- Conditional logic
- String formatting
```

**Commit message:**
```
Day 1: Build simple calculator with error handling

- Implemented all basic arithmetic operations
- Added division by zero validation
- Formatted output for professional display
- Completed Day 1 of Java fundamentals
```

---

## 🎉 Congratulations!

You've completed your first Java project!

**What You've Achieved:**
- ✅ Built a working Java program from scratch
- ✅ Used variables, data types, and operators correctly
- ✅ Implemented error handling (division by zero)
- ✅ Created professional formatted output
- ✅ Tested your code thoroughly

**Day 1 Progress:**
- Concepts learned: ✅
- Practice exercises: ✅
- Debugging challenges: ✅
- Mini project: ✅
- **Day 1 COMPLETE!** 🎉

---

## 🔮 Tomorrow Preview

**Day 2: Control Flow**
You'll learn if-else, switch, and loops - then build a Number Guessing Game that uses your calculator logic + decision making.

**How today's project helps:**
- Variables → Store game state (guesses, target number)
- Operators → Compare guess to target (`==`, `>`, `<`)
- Error handling → Validate user input

---

## 📝 Reflection

**Time spent on project:** ___ minutes

**Difficulty (1-10):** ___

**What I learned:**
-
-
-

**Challenges I faced:**
-
-

**How I overcame them:**
-
-

**Confidence with Java syntax (1-10):** ___

---

Move to **Day-1-Cheat-Sheet.md** for a quick reference guide!

**Project complete:** ✅
**Ready to push to GitHub:** Yes / Need polish

---
