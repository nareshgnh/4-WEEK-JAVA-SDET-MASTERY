# DAY 1 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master TestNG annotations and assertions by converting basic tests to TestNG format and writing new tests from scratch.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Your First TestNG Test
**Objective:** Get comfortable with @Test annotation and test execution

**Task:**
Create a simple TestNG test class with two test methods that print messages and verify basic assertions.

**Starter Code:**
```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class Exercise1_FirstTest {

    @Test
    public void testAddition() {
        // TODO: Calculate 5 + 3 and assert it equals 8


    }

    @Test
    public void testStringComparison() {
        // TODO: Create two strings and assert they are equal


    }
}
```

**Expected Output:**
```
Test passes: 2
Test failures: 0

===============================================
Default Suite
Total tests run: 2, Passes: 2, Failures: 0, Skips: 0
===============================================
```

**Hints:**
- Use `Assert.assertEquals()` for comparing values
- For addition: `int result = 5 + 3;`
- Remember to import `org.testng.Assert`

---

### Exercise 2: Using @BeforeMethod and @AfterMethod
**Objective:** Understand setup and teardown lifecycle

**Task:**
Create a test class that demonstrates @BeforeMethod and @AfterMethod by printing messages before and after each test.

**Starter Code:**
```java
import org.testng.annotations.*;

public class Exercise2_Lifecycle {

    @BeforeMethod
    public void setup() {
        // TODO: Print "Setting up test..."

    }

    @AfterMethod
    public void teardown() {
        // TODO: Print "Cleaning up after test..."

    }

    @Test
    public void test1() {
        System.out.println("Executing Test 1");
    }

    @Test
    public void test2() {
        System.out.println("Executing Test 2");
    }
}
```

**Expected Output:**
```
Setting up test...
Executing Test 1
Cleaning up after test...
Setting up test...
Executing Test 2
Cleaning up after test...
```

**Hints:**
- @BeforeMethod runs before EACH test method
- Notice the pattern: setup → test → teardown → setup → test → teardown

---

### Exercise 3: Multiple Assertion Types
**Objective:** Practice different assertion methods

**Task:**
Write a test that uses at least 5 different assertion types to verify various conditions.

**Starter Code:**
```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class Exercise3_MultipleAssertions {

    @Test
    public void testVariousAssertions() {
        // TODO: Assert that 10 > 5 is true


        // TODO: Assert that "TestNG" equals "TestNG"


        // TODO: Assert that a string is not null
        String text = "Hello";


        // TODO: Assert that 7 is not equal to 10


        // TODO: Assert that "automation" contains "auto"

    }
}
```

**Expected Output:**
```
Test passes: 1
All assertions passed!
```

**Hints:**
- `Assert.assertTrue()` for boolean conditions
- `Assert.assertEquals()` for equality
- `Assert.assertNotNull()` for null checks
- `Assert.assertNotEquals()` for inequality
- Use `.contains()` method for string checking

---

## 💪 Core Practice (30 min)

### Exercise 4: Calculator Test with TestNG
**Objective:** Create a calculator class and test it using TestNG

**Problem Statement:**
Create a `Calculator` class with add, subtract, multiply, divide methods. Then write TestNG tests to verify each operation works correctly.

**Requirements:**
- Calculator class with 4 methods (add, subtract, multiply, divide)
- Test class with minimum 5 test methods
- Use @BeforeClass to create Calculator instance
- Use meaningful assertions with custom messages

**Starter Code:**
```java
// Calculator.java
public class Calculator {

    public int add(int a, int b) {
        // TODO: Implement addition
        return 0;
    }

    public int subtract(int a, int b) {
        // TODO: Implement subtraction
        return 0;
    }

    public int multiply(int a, int b) {
        // TODO: Implement multiplication
        return 0;
    }

    public double divide(int a, int b) {
        // TODO: Implement division
        // Handle divide by zero
        return 0;
    }
}

// CalculatorTest.java
import org.testng.Assert;
import org.testng.annotations.*;

public class CalculatorTest {
    Calculator calc;

    @BeforeClass
    public void setupCalculator() {
        // TODO: Initialize calculator

    }

    @Test
    public void testAddition() {
        // TODO: Test add method

    }

    @Test
    public void testSubtraction() {
        // TODO: Test subtract method

    }

    @Test
    public void testMultiplication() {
        // TODO: Test multiply method

    }

    @Test
    public void testDivision() {
        // TODO: Test divide method

    }

    @Test
    public void testDivideByZero() {
        // TODO: Test division by zero
        // Hint: It should throw ArithmeticException or return infinity

    }
}
```

**Test Cases:**
```
Test 1: 10 + 5 = 15
Test 2: 20 - 8 = 12
Test 3: 6 * 7 = 42
Test 4: 20 / 4 = 5.0
Test 5: 10 / 0 should handle error gracefully
```

**Hints:**
- Use `Assert.assertEquals()` with custom messages
- For divide by zero, you can check if result is `Double.POSITIVE_INFINITY`
- @BeforeClass runs once before all tests in the class

---

### Exercise 5: Simple Selenium Test with TestNG
**Objective:** Convert a basic Selenium script to TestNG format

**Problem Statement:**
Create a TestNG test that opens Google, verifies the title, and closes the browser. Use proper setup/teardown methods.

**Requirements:**
- Use @BeforeMethod to initialize WebDriver
- Use @AfterMethod to close browser
- Test should verify Google homepage title
- Use meaningful assertion messages

**Starter Code:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;

public class GoogleTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        // TODO: Initialize ChromeDriver

    }

    @AfterMethod
    public void teardown() {
        // TODO: Close browser

    }

    @Test
    public void testGoogleTitle() {
        // TODO: Navigate to Google

        // TODO: Get title and assert it equals "Google"

    }
}
```

**Testing Instructions:**
1. Make sure ChromeDriver is in your system PATH or use WebDriverManager
2. Run the test
3. Verify browser opens, navigates to Google, and closes automatically
4. Check TestNG report for pass/fail status

**Expected Behavior:**
```
✓ Browser opens
✓ Navigates to https://www.google.com
✓ Title verification passes
✓ Browser closes automatically
```

**Hints:**
- `driver.get("url")` to navigate
- `driver.getTitle()` to get page title
- `driver.quit()` to close browser
- Add null check before quit: `if(driver != null) driver.quit();`

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Complete Test Class with Multiple Tests
**Objective:** Build a comprehensive test class demonstrating all today's concepts

**Requirements:**
Create a test class for a login scenario with:
1. @BeforeClass: Setup test data (username, password)
2. @BeforeMethod: Print "Starting test: [testname]"
3. @AfterMethod: Print "Test completed"
4. @AfterClass: Print summary
5. At least 3 test methods with assertions
6. Use both hard assertions and demonstrate soft assertions

**Starter Code:**
```java
import org.testng.Assert;
import org.testng.annotations.*;
import org.testng.asserts.SoftAssert;

public class LoginTest {
    String validUsername;
    String validPassword;

    @BeforeClass
    public void setupTestData() {
        // TODO: Initialize test data

    }

    @BeforeMethod
    public void beforeEachTest() {
        // TODO: Print test start message

    }

    @AfterMethod
    public void afterEachTest() {
        // TODO: Print test completion message

    }

    @AfterClass
    public void summary() {
        // TODO: Print overall summary

    }

    @Test
    public void testValidUsernameFormat() {
        // TODO: Verify username is not empty and has minimum length

    }

    @Test
    public void testValidPasswordFormat() {
        // TODO: Verify password meets criteria

    }

    @Test
    public void testMultipleValidationsWithSoftAssert() {
        // TODO: Use SoftAssert to validate multiple things
        SoftAssert softAssert = new SoftAssert();

        // TODO: Add multiple soft assertions


        // TODO: Don't forget softAssert.assertAll() at the end!

    }
}
```

**Bonus Challenges:**
- [ ] Add a test that intentionally fails to see how TestNG reports it
- [ ] Use assertion messages for all assertions
- [ ] Print the execution order to understand lifecycle

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class Exercise1_FirstTest {

    @Test
    public void testAddition() {
        // Calculate 5 + 3
        int result = 5 + 3;

        // Assert result equals 8
        Assert.assertEquals(result, 8, "Addition failed: 5 + 3 should equal 8");

        System.out.println("Addition test passed!");
    }

    @Test
    public void testStringComparison() {
        // Create two strings
        String expected = "TestNG";
        String actual = "TestNG";

        // Assert they are equal
        Assert.assertEquals(actual, expected, "Strings should be equal");

        System.out.println("String comparison test passed!");
    }
}
```

**Explanation:**
- We created two test methods using `@Test` annotation
- Each test performs a simple operation and validates with `Assert.assertEquals()`
- Custom messages help identify what failed if assertion doesn't pass
- TestNG discovers these methods automatically and executes them

**Key Takeaways:**
- `@Test` marks a method as executable test
- `Assert.assertEquals(actual, expected, message)` compares values
- Test methods must be public and return void

---

### Exercise 2 Solution
```java
import org.testng.annotations.*;

public class Exercise2_Lifecycle {

    @BeforeMethod
    public void setup() {
        System.out.println("Setting up test...");
    }

    @AfterMethod
    public void teardown() {
        System.out.println("Cleaning up after test...\n");
    }

    @Test
    public void test1() {
        System.out.println("Executing Test 1");
    }

    @Test
    public void test2() {
        System.out.println("Executing Test 2");
    }
}
```

**Explanation:**
When you run this, you'll see:
```
Setting up test...
Executing Test 1
Cleaning up after test...

Setting up test...
Executing Test 2
Cleaning up after test...
```

Notice that @BeforeMethod and @AfterMethod run for EACH test. This is crucial for:
- Browser setup/teardown in Selenium
- Database connection/close
- Test data reset

**Key Takeaways:**
- @BeforeMethod runs before each @Test method
- @AfterMethod runs after each @Test method
- Perfect for browser lifecycle management

---

### Exercise 3 Solution
```java
import org.testng.Assert;
import org.testng.annotations.Test;

public class Exercise3_MultipleAssertions {

    @Test
    public void testVariousAssertions() {
        // Assert that 10 > 5 is true
        Assert.assertTrue(10 > 5, "10 should be greater than 5");

        // Assert that "TestNG" equals "TestNG"
        Assert.assertEquals("TestNG", "TestNG", "Strings should match");

        // Assert that a string is not null
        String text = "Hello";
        Assert.assertNotNull(text, "Text should not be null");

        // Assert that 7 is not equal to 10
        Assert.assertNotEquals(7, 10, "7 and 10 should be different");

        // Assert that "automation" contains "auto"
        String word = "automation";
        Assert.assertTrue(word.contains("auto"), "Word should contain 'auto'");

        System.out.println("All assertions passed!");
    }
}
```

**Explanation:**
We used 5 different assertion types:
1. `assertTrue()` - for boolean conditions
2. `assertEquals()` - for exact matches
3. `assertNotNull()` - to verify object exists
4. `assertNotEquals()` - for inequality checks
5. `assertTrue(string.contains())` - for substring checking

**Key Takeaways:**
- Different assertion methods for different validation types
- Always include descriptive messages
- Multiple assertions can exist in one test (but be careful - if first fails, rest won't execute with hard assertions)

---

### Exercise 4 Solution

**Calculator.java:**
```java
public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public double divide(int a, int b) {
        if (b == 0) {
            return Double.POSITIVE_INFINITY;  // Or throw exception
        }
        return (double) a / b;
    }
}
```

**CalculatorTest.java:**
```java
import org.testng.Assert;
import org.testng.annotations.*;

public class CalculatorTest {
    Calculator calc;

    @BeforeClass
    public void setupCalculator() {
        calc = new Calculator();
        System.out.println("Calculator initialized for all tests");
    }

    @Test
    public void testAddition() {
        int result = calc.add(10, 5);
        Assert.assertEquals(result, 15, "10 + 5 should equal 15");
        System.out.println("Addition test passed: 10 + 5 = " + result);
    }

    @Test
    public void testSubtraction() {
        int result = calc.subtract(20, 8);
        Assert.assertEquals(result, 12, "20 - 8 should equal 12");
        System.out.println("Subtraction test passed: 20 - 8 = " + result);
    }

    @Test
    public void testMultiplication() {
        int result = calc.multiply(6, 7);
        Assert.assertEquals(result, 42, "6 * 7 should equal 42");
        System.out.println("Multiplication test passed: 6 * 7 = " + result);
    }

    @Test
    public void testDivision() {
        double result = calc.divide(20, 4);
        Assert.assertEquals(result, 5.0, "20 / 4 should equal 5.0");
        System.out.println("Division test passed: 20 / 4 = " + result);
    }

    @Test
    public void testDivideByZero() {
        double result = calc.divide(10, 0);
        Assert.assertEquals(result, Double.POSITIVE_INFINITY,
            "Division by zero should return infinity");
        System.out.println("Divide by zero handled correctly");
    }
}
```

**Explanation:**
- Created Calculator class with 4 mathematical operations
- Used @BeforeClass to create single Calculator instance for all tests
- Each test verifies one operation with meaningful assertions
- Handled edge case: division by zero

**Key Takeaways:**
- @BeforeClass runs once before all tests (good for expensive setup)
- Each test method focuses on one specific functionality
- Edge cases should be tested separately

---

### Exercise 5 Solution
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;

public class GoogleTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        // Initialize ChromeDriver
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        System.out.println("Browser opened");
    }

    @AfterMethod
    public void teardown() {
        // Close browser
        if (driver != null) {
            driver.quit();
            System.out.println("Browser closed");
        }
    }

    @Test
    public void testGoogleTitle() {
        // Navigate to Google
        driver.get("https://www.google.com");
        System.out.println("Navigated to Google");

        // Get title and assert
        String actualTitle = driver.getTitle();
        Assert.assertEquals(actualTitle, "Google",
            "Expected title 'Google' but got '" + actualTitle + "'");

        System.out.println("Title verification passed!");
    }
}
```

**Explanation:**
This is your first Selenium test with TestNG structure:
1. **@BeforeMethod** - Creates fresh browser instance for each test
2. **@Test** - Actual test logic (navigate + verify)
3. **@AfterMethod** - Ensures browser closes even if test fails

**Key Takeaways:**
- Browser lifecycle managed by TestNG annotations
- Each test gets fresh browser (test isolation)
- Cleanup happens automatically
- This is the foundation of all Selenium + TestNG frameworks

---

### Exercise 6 Solution
```java
import org.testng.Assert;
import org.testng.annotations.*;
import org.testng.asserts.SoftAssert;

public class LoginTest {
    String validUsername;
    String validPassword;
    int testCount = 0;

    @BeforeClass
    public void setupTestData() {
        validUsername = "testuser@example.com";
        validPassword = "SecurePass123!";
        System.out.println("=== Test Data Initialized ===");
        System.out.println("Username: " + validUsername);
        System.out.println("Password: " + validPassword);
        System.out.println("=============================\n");
    }

    @BeforeMethod
    public void beforeEachTest() {
        testCount++;
        System.out.println(">>> Starting Test #" + testCount);
    }

    @AfterMethod
    public void afterEachTest() {
        System.out.println("<<< Test Completed\n");
    }

    @AfterClass
    public void summary() {
        System.out.println("=================================");
        System.out.println("Total tests executed: " + testCount);
        System.out.println("All login validation tests complete!");
        System.out.println("=================================");
    }

    @Test
    public void testValidUsernameFormat() {
        // Verify username is not empty
        Assert.assertFalse(validUsername.isEmpty(), "Username should not be empty");

        // Verify username has minimum length
        Assert.assertTrue(validUsername.length() >= 5,
            "Username should have at least 5 characters");

        // Verify it's an email format
        Assert.assertTrue(validUsername.contains("@"),
            "Username should be in email format");

        System.out.println("✓ Username format is valid");
    }

    @Test
    public void testValidPasswordFormat() {
        // Verify password is not empty
        Assert.assertFalse(validPassword.isEmpty(), "Password should not be empty");

        // Verify minimum length
        Assert.assertTrue(validPassword.length() >= 8,
            "Password should have at least 8 characters");

        // Verify it contains a number
        Assert.assertTrue(validPassword.matches(".*\\d.*"),
            "Password should contain at least one digit");

        System.out.println("✓ Password format is valid");
    }

    @Test
    public void testMultipleValidationsWithSoftAssert() {
        SoftAssert softAssert = new SoftAssert();

        // Multiple validations that won't stop test execution
        softAssert.assertNotNull(validUsername, "Username should not be null");
        softAssert.assertNotNull(validPassword, "Password should not be null");
        softAssert.assertTrue(validUsername.length() > 0, "Username should have content");
        softAssert.assertTrue(validPassword.length() > 0, "Password should have content");
        softAssert.assertTrue(validUsername.contains("@"), "Username should contain @");
        softAssert.assertTrue(validPassword.contains("!") || validPassword.contains("@"),
            "Password should contain special character");

        System.out.println("✓ All soft assertions checked");

        // This line actually marks test as failed if any assertion failed
        softAssert.assertAll();
    }
}
```

**Explanation:**
This comprehensive example demonstrates:
- **@BeforeClass**: Setup that runs once (test data initialization)
- **@BeforeMethod**: Runs before each test (test counter)
- **@AfterMethod**: Runs after each test (completion message)
- **@AfterClass**: Cleanup that runs once (summary)
- **Hard Assertions**: Tests fail immediately if assertion fails
- **Soft Assertions**: All assertions checked, all failures reported together

**Key Takeaways:**
- Lifecycle methods provide structure and organization
- Soft assertions useful when you want to see ALL failures, not just first one
- Always call `softAssert.assertAll()` at the end of soft assertion tests

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - First Test | ☐ | __/10 | ___ min | |
| 2 - Lifecycle | ☐ | __/10 | ___ min | |
| 3 - Assertions | ☐ | __/10 | ___ min | |
| 4 - Calculator | ☐ | __/10 | ___ min | |
| 5 - Selenium Test | ☐ | __/10 | ___ min | |
| 6 - Complete Class | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
-
-

**Areas I excelled at:**
-
-

---

## ✅ Answers to Self-Check Questions from Concept Guide

1. **What annotation do you use to mark a method as a test case in TestNG?**
   - Answer: `@Test`

2. **If you need to open a browser before each test method, which annotation should you use?**
   - Answer: `@BeforeMethod` (runs before each test)

3. **What's the correct way to assert that a string variable `title` equals "Google" in TestNG?**
   - Answer: `Assert.assertEquals(title, "Google");` or with message: `Assert.assertEquals(title, "Google", "Title mismatch!");`

4. **What will happen if an assertion fails in a test method?**
   - Answer: The test method stops immediately (for hard assertions), is marked as FAILED, and moves to @AfterMethod if present

5. **What's the difference between `@BeforeClass` and `@BeforeMethod`?**
   - Answer: `@BeforeClass` runs once before all tests in the class. `@BeforeMethod` runs before each individual test method.

---

**🎉 Day 1 Practice Complete! Move to Day-1-Debug-Practice.md**
