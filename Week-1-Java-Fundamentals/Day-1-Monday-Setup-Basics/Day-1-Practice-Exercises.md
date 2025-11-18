# DAY 1 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Build confidence with Java syntax, data types, and operators through hands-on coding. By the end, you'll be comfortable writing basic Java programs and understanding compilation errors.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Variable Declaration and Output
**Objective:** Get comfortable with declaring variables and printing output

**Task:**
Create a program that declares variables for a test execution summary and prints them.

**Requirements:**
- Declare variables for: test suite name (String), total tests (int), passed tests (int), failed tests (int), pass rate (double)
- Print each variable with a descriptive label
- Calculate and print the pass rate

**Starter Code:**
```java
public class Exercise1 {
    public static void main(String[] args) {
        // Declare your variables here
        String testSuiteName = "";
        int totalTests = 0;
        // Add more variables

        // Print the test summary

        // Calculate pass rate
        // passRate = (passed * 100.0) / total

    }
}
```

**Expected Output:**
```
Test Suite: Smoke Test Suite
Total Tests: 50
Passed: 45
Failed: 5
Pass Rate: 90.0%
```

**Hints:**
- Use `String` for text, `int` for whole numbers, `double` for decimals
- Remember to multiply by 100.0 (not 100) for proper decimal division
- Use `System.out.println()` for each line

---

### Exercise 2: Basic Arithmetic Operations
**Objective:** Practice using arithmetic operators

**Task:**
Write a program that calculates and displays the execution time for different test scenarios.

**Starter Code:**
```java
public class Exercise2 {
    public static void main(String[] args) {
        // Test execution times in seconds
        int loginTime = 5;
        int searchTime = 3;
        int checkoutTime = 10;

        // Calculate total time

        // Calculate average time per operation

        // Print results

    }
}
```

**Expected Output:**
```
Login Time: 5 seconds
Search Time: 3 seconds
Checkout Time: 10 seconds
Total Execution Time: 18 seconds
Average Time per Operation: 6 seconds
```

**Hints:**
- Use `+` operator to add times
- Divide total by 3 to get average
- Store results in new variables before printing

---

### Exercise 3: Type Casting Practice
**Objective:** Understand implicit and explicit type casting

**Task:**
Convert between different numeric types and observe the results.

**Starter Code:**
```java
public class Exercise3 {
    public static void main(String[] args) {
        // Scenario: Convert wait times from milliseconds to seconds

        int waitTimeMs = 5500;  // milliseconds

        // Convert to seconds (should be 5.5)
        double waitTimeSec = 0.0;  // Fix this line

        System.out.println(waitTimeMs + " milliseconds = " + waitTimeSec + " seconds");

        // Now convert back to int (will lose decimal part)
        int roundedSeconds = 0;  // Fix this line

        System.out.println("Rounded seconds: " + roundedSeconds);
    }
}
```

**Expected Output:**
```
5500 milliseconds = 5.5 seconds
Rounded seconds: 5
```

**Hints:**
- Divide milliseconds by 1000.0 (not 1000) to get decimal seconds
- Use explicit casting `(int)` to convert double to int

---

## 💪 Core Practice (30 min)

### Exercise 4: BMI Calculator with Multiple Data Types
**Objective:** Work with different data types and perform calculations

**Problem Statement:**
Create a program that calculates Body Mass Index (BMI) for health check data. This simulates processing test data with different types.

**Requirements:**
- Accept person's name (String), weight in kg (double), and height in meters (double)
- Calculate BMI using formula: BMI = weight / (height * height)
- Determine BMI category: Underweight (<18.5), Normal (18.5-24.9), Overweight (>=25)
- Display all information

**Starter Code:**
```java
public class Exercise4 {
    public static void main(String[] args) {
        // Test data
        String personName = "John Doe";
        double weightKg = 70.5;
        double heightM = 1.75;

        // Calculate BMI
        double bmi = 0.0;  // TODO: Implement formula

        // Determine category
        String category = "";  // TODO: Use if-else logic (we'll learn this in detail tomorrow)
        // For now, you can leave it as "To be determined"

        // Display results
        System.out.println("===== BMI Calculator =====");
        // TODO: Print name, weight, height, BMI

    }
}
```

**Test Cases:**
```
Input: weight=70.5kg, height=1.75m
Expected Output:
===== BMI Calculator =====
Name: John Doe
Weight: 70.5 kg
Height: 1.75 m
BMI: 23.02
Category: Normal
```

**Hints:**
- BMI formula: weight divided by (height multiplied by height)
- Use `%.2f` in String.format() for 2 decimal places (advanced) or just print the double value
- For category, you can temporarily use "Normal" as a placeholder

---

### Exercise 5: Currency Converter
**Objective:** Practice with constants, arithmetic, and formatting

**Problem Statement:**
Create a currency converter that converts USD to INR, EUR, and GBP. This simulates test data conversion scenarios.

**Requirements:**
- Use `final` keyword for exchange rates (constants)
- Accept amount in USD (double)
- Calculate equivalent amounts in INR, EUR, GBP
- Display all conversions in a formatted manner

**Starter Code:**
```java
public class Exercise5 {
    public static void main(String[] args) {
        // Exchange rates (constants)
        final double USD_TO_INR = 83.12;
        final double USD_TO_EUR = 0.92;
        final double USD_TO_GBP = 0.79;

        // Amount to convert
        double amountUSD = 100.0;

        // Perform conversions
        double amountINR = 0.0;  // TODO: Calculate
        double amountEUR = 0.0;  // TODO: Calculate
        double amountGBP = 0.0;  // TODO: Calculate

        // Display results
        System.out.println("===== Currency Converter =====");
        System.out.println("USD: $" + amountUSD);
        // TODO: Print other conversions

    }
}
```

**Expected Output:**
```
===== Currency Converter =====
USD: $100.0
INR: ₹8312.0
EUR: €92.0
GBP: £79.0
```

**Hints:**
- Multiply USD amount by each exchange rate
- You can use currency symbols like ₹, €, £ directly in strings
- Make sure constants are declared with `final` keyword

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Simple Calculator (Today's Mini Project)
**Objective:** Build today's deliverable - a simple calculator using variables and operators

**Requirements:**
Create a calculator that:
1. Takes two numbers as input (you can hardcode them for now)
2. Performs all basic operations: addition, subtraction, multiplication, division, modulus
3. Displays results in a formatted manner
4. Handles edge case: division by zero (just print a warning message)

**Starter Code:**
```java
public class SimpleCalculator {
    public static void main(String[] args) {
        // Input numbers
        double num1 = 20.0;
        double num2 = 4.0;

        System.out.println("===== SIMPLE CALCULATOR =====");
        System.out.println("Number 1: " + num1);
        System.out.println("Number 2: " + num2);
        System.out.println("============================");

        // Perform operations
        // TODO: Addition
        // TODO: Subtraction
        // TODO: Multiplication
        // TODO: Division (check if num2 is not zero first)
        // TODO: Modulus

    }
}
```

**Expected Output:**
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
```

**Testing Instructions:**
Test with these scenarios:
1. Normal numbers: 20.0 and 4.0
2. Decimal numbers: 15.5 and 3.2
3. Division by zero: 10.0 and 0.0 (should print error message instead of dividing)

**Bonus Challenges:**
- [ ] Add a check for division by zero and print "Error: Cannot divide by zero"
- [ ] Format output to show only 2 decimal places
- [ ] Add operation symbols (✓) next to each calculation
- [ ] Calculate and display the average of the two numbers

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
public class Exercise1 {
    public static void main(String[] args) {
        // Declare variables for test execution summary
        String testSuiteName = "Smoke Test Suite";
        int totalTests = 50;
        int passedTests = 45;
        int failedTests = 5;
        double passRate;

        // Calculate pass rate (multiply by 100.0 for percentage)
        passRate = (passedTests * 100.0) / totalTests;

        // Print the test summary
        System.out.println("Test Suite: " + testSuiteName);
        System.out.println("Total Tests: " + totalTests);
        System.out.println("Passed: " + passedTests);
        System.out.println("Failed: " + failedTests);
        System.out.println("Pass Rate: " + passRate + "%");
    }
}
```

**Explanation:**
- We use `String` for the test suite name (text data)
- `int` for test counts (whole numbers)
- `double` for pass rate (decimal percentage)
- Multiply by `100.0` (not `100`) to force double division, giving us decimals like 90.0 instead of 90
- String concatenation with `+` combines text and variables

**Key Takeaways:**
- Always choose the right data type for your data
- Use `100.0` (double) not `100` (int) when you need decimal results
- String concatenation in Java uses `+` operator (same as Python)

---

### Exercise 2 Solution
```java
public class Exercise2 {
    public static void main(String[] args) {
        // Test execution times in seconds
        int loginTime = 5;
        int searchTime = 3;
        int checkoutTime = 10;

        // Calculate total time
        int totalTime = loginTime + searchTime + checkoutTime;

        // Calculate average time per operation
        int averageTime = totalTime / 3;

        // Print results
        System.out.println("Login Time: " + loginTime + " seconds");
        System.out.println("Search Time: " + searchTime + " seconds");
        System.out.println("Checkout Time: " + checkoutTime + " seconds");
        System.out.println("Total Execution Time: " + totalTime + " seconds");
        System.out.println("Average Time per Operation: " + averageTime + " seconds");
    }
}
```

**Explanation:**
- We add all three time values to get total
- Integer division (totalTime / 3) gives us 6, dropping any decimal
- Each println statement combines text and variables with `+`

**Key Takeaways:**
- Arithmetic operators work the same as in Python
- Integer division truncates decimals (18/3 = 6, not 6.0)
- Variables make code readable and maintainable

---

### Exercise 3 Solution
```java
public class Exercise3 {
    public static void main(String[] args) {
        // Scenario: Convert wait times from milliseconds to seconds
        int waitTimeMs = 5500;  // milliseconds

        // Convert to seconds (divide by 1000.0 to get decimal result)
        double waitTimeSec = waitTimeMs / 1000.0;

        System.out.println(waitTimeMs + " milliseconds = " + waitTimeSec + " seconds");

        // Convert double to int (explicit casting - loses decimal part)
        int roundedSeconds = (int) waitTimeSec;

        System.out.println("Rounded seconds: " + roundedSeconds);
    }
}
```

**Explanation:**
- `waitTimeMs / 1000.0` - dividing int by double gives double result (5.5)
- `(int) waitTimeSec` - explicit cast from double to int (5.5 becomes 5, decimal dropped)
- If we used `waitTimeMs / 1000`, we'd get integer division: 5500/1000 = 5 (not 5.5!)

**Key Takeaways:**
- Implicit casting: smaller type → larger type (int → double) happens automatically
- Explicit casting: larger type → smaller type needs manual cast `(targetType)`
- Always use `.0` when dividing if you want decimal results

---

### Exercise 4 Solution
```java
public class Exercise4 {
    public static void main(String[] args) {
        // Test data
        String personName = "John Doe";
        double weightKg = 70.5;
        double heightM = 1.75;

        // Calculate BMI = weight / (height * height)
        double bmi = weightKg / (heightM * heightM);

        // Determine category (basic version for now)
        String category;
        if (bmi < 18.5) {
            category = "Underweight";
        } else if (bmi < 25.0) {
            category = "Normal";
        } else {
            category = "Overweight";
        }

        // Display results
        System.out.println("===== BMI Calculator =====");
        System.out.println("Name: " + personName);
        System.out.println("Weight: " + weightKg + " kg");
        System.out.println("Height: " + heightM + " m");
        System.out.println("BMI: " + String.format("%.2f", bmi));
        System.out.println("Category: " + category);
    }
}
```

**Explanation:**
- BMI formula requires multiplication first (heightM * heightM), then division
- Parentheses ensure correct order of operations
- `String.format("%.2f", bmi)` rounds to 2 decimal places (23.02 instead of 23.02040816326531)
- The if-else logic will be covered in detail in Day 2, but it's straightforward

**Key Takeaways:**
- Use parentheses to control operation order
- `String.format("%.2f", number)` formats decimals to 2 places
- Real test automation often involves calculations like this for test metrics

---

### Exercise 5 Solution
```java
public class Exercise5 {
    public static void main(String[] args) {
        // Exchange rates (constants - cannot be changed)
        final double USD_TO_INR = 83.12;
        final double USD_TO_EUR = 0.92;
        final double USD_TO_GBP = 0.79;

        // Amount to convert
        double amountUSD = 100.0;

        // Perform conversions
        double amountINR = amountUSD * USD_TO_INR;
        double amountEUR = amountUSD * USD_TO_EUR;
        double amountGBP = amountUSD * USD_TO_GBP;

        // Display results
        System.out.println("===== Currency Converter =====");
        System.out.println("USD: $" + amountUSD);
        System.out.println("INR: ₹" + amountINR);
        System.out.println("EUR: €" + amountEUR);
        System.out.println("GBP: £" + amountGBP);
    }
}
```

**Explanation:**
- `final` keyword makes variables constants (convention: UPPER_CASE naming)
- Simple multiplication converts between currencies
- Java supports Unicode characters (₹, €, £) in strings
- Constants are useful for test configuration values that shouldn't change

**Key Takeaways:**
- Use `final` for values that should never change (like exchange rates, URLs, timeouts)
- Constants use UPPER_SNAKE_CASE naming convention
- This pattern is very common in test automation frameworks for configuration

---

### Exercise 6 Solution (Calculator Mini Project)
```java
public class SimpleCalculator {
    public static void main(String[] args) {
        // Input numbers
        double num1 = 20.0;
        double num2 = 4.0;

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
            System.out.println("Division: Error - Cannot divide by zero");
        }

        // Modulus
        double remainder = num1 % num2;
        System.out.println("Modulus: " + num1 + " % " + num2 + " = " + remainder);
    }
}
```

**Explanation:**
- Each operation stores result in a variable before printing
- Division includes a check: `if (num2 != 0)` to prevent division by zero error
- Modulus (%) gives remainder after division (20 % 4 = 0, because 20 divides evenly by 4)
- Clean formatting with separators makes output professional

**Bonus Solution (with all challenges):**
```java
public class SimpleCalculatorAdvanced {
    public static void main(String[] args) {
        // Input numbers
        double num1 = 20.0;
        double num2 = 4.0;

        System.out.println("===== SIMPLE CALCULATOR =====");
        System.out.println("Number 1: " + num1);
        System.out.println("Number 2: " + num2);
        System.out.println("============================");

        // Addition
        double sum = num1 + num2;
        System.out.println("✓ Addition: " + num1 + " + " + num2 + " = " + String.format("%.2f", sum));

        // Subtraction
        double difference = num1 - num2;
        System.out.println("✓ Subtraction: " + num1 + " - " + num2 + " = " + String.format("%.2f", difference));

        // Multiplication
        double product = num1 * num2;
        System.out.println("✓ Multiplication: " + num1 + " * " + num2 + " = " + String.format("%.2f", product));

        // Division (with zero check)
        if (num2 != 0) {
            double quotient = num1 / num2;
            System.out.println("✓ Division: " + num1 + " / " + num2 + " = " + String.format("%.2f", quotient));
        } else {
            System.out.println("✗ Division: Error - Cannot divide by zero");
        }

        // Modulus
        double remainder = num1 % num2;
        System.out.println("✓ Modulus: " + num1 + " % " + num2 + " = " + String.format("%.2f", remainder));

        // Average
        double average = (num1 + num2) / 2;
        System.out.println("============================");
        System.out.println("✓ Average: " + String.format("%.2f", average));
    }
}
```

**Key Takeaways:**
- Always validate inputs (like checking for zero before division)
- Format output to 2 decimal places for cleaner display
- This calculator demonstrates all operators you learned today
- You just built your first real Java program - ready for GitHub!

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Variables & Output | ☐ | __/10 | ___ min | |
| 2 - Arithmetic | ☐ | __/10 | ___ min | |
| 3 - Type Casting | ☐ | __/10 | ___ min | |
| 4 - BMI Calculator | ☐ | __/10 | ___ min | |
| 5 - Currency Converter | ☐ | __/10 | ___ min | |
| 6 - Simple Calculator | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
- [ ] Variable declaration syntax
- [ ] Type casting
- [ ] Integer vs double division
- [ ] String concatenation
- [ ] Using operators
- [ ] Other: _______________

**Areas I excelled at:**
- [ ] Understanding data types
- [ ] Using operators correctly
- [ ] Formatting output
- [ ] Debugging compilation errors
- [ ] Completing the calculator project
- [ ] Other: _______________

---

## 🎯 Answers to Self-Check Questions from Concept Guide

1. **What are the three mandatory parts of a Java program structure?**
   - A class declaration (`public class ClassName`)
   - A main method (`public static void main(String[] args)`)
   - Curly braces `{}` to define code blocks

2. **What's the difference between `int` and `Integer` in Java?**
   - `int` is a primitive type (stores actual value, faster, cannot be null)
   - `Integer` is a wrapper class (object, can be null, has utility methods)
   - Example: `int x = 5;` vs `Integer y = 5;`

3. **If you write `int result = 7 / 2;`, what value will `result` have and why?**
   - `result` will be `3` (not 3.5)
   - Because both 7 and 2 are integers, Java performs integer division
   - To get 3.5: `double result = 7.0 / 2;` or `double result = 7 / 2.0;`

4. **Why do we use the `final` keyword in test automation frameworks?**
   - To create constants that cannot be modified (like BASE_URL, TIMEOUT values)
   - Prevents accidental changes to configuration values
   - Makes code more maintainable and less error-prone

5. **Predict the output:**
   ```java
   int x = 5;
   System.out.println(x++);  // Output: 5 (post-increment: use then increase)
   System.out.println(++x);  // Output: 7 (pre-increment: increase then use; x was 6, now 7)
   ```

---

## 🚀 Next Step

Move to **Day-1-Debug-Practice.md** to sharpen your debugging skills!

**Practice complete:** ✅
**Confidence level (1-10):** ____
**Ready for debugging:** Yes / Need more practice

---
