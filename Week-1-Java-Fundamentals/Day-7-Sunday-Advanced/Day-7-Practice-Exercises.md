# DAY 7 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master exception handling and collections by building robust, error-resilient programs that handle real-world scenarios. By the end, you'll confidently work with try-catch blocks, ArrayList, HashSet, and HashMap in automation contexts.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Basic Exception Handling
**Objective:** Practice try-catch-finally blocks

**Task:**
Write a program that divides two numbers. Handle the ArithmeticException when dividing by zero and use a finally block to print "Calculation complete".

**Starter Code:**
```java
public class Exercise1 {
    public static void main(String[] args) {
        // Your code here
        int num1 = 100;
        int num2 = 0;

        // Add try-catch-finally block
        // Attempt division
        // Handle exception
        // Print completion message in finally
    }
}
```

**Expected Output:**
```
Error: Cannot divide by zero
Calculation complete
```

**Hints:**
- Use `try { }` for the division operation
- Catch `ArithmeticException`
- `finally` block always executes regardless of exception

---

### Exercise 2: ArrayList Basics
**Objective:** Practice ArrayList creation and manipulation

**Task:**
Create an ArrayList of test browsers (Chrome, Firefox, Edge, Safari). Add all browsers, print the list, remove Firefox, and print the updated list. Display the total count before and after removal.

**Starter Code:**
```java
import java.util.ArrayList;

public class Exercise2 {
    public static void main(String[] args) {
        // Create ArrayList of browsers

        // Add 4 browsers

        // Print original list and count

        // Remove Firefox

        // Print updated list and count
    }
}
```

**Expected Output:**
```
Browsers: [Chrome, Firefox, Edge, Safari]
Total browsers: 4
Browsers after removal: [Chrome, Edge, Safari]
Total browsers: 3
```

**Hints:**
- Use `ArrayList<String> browsers = new ArrayList<>();`
- Use `.add()` to add elements
- Use `.remove()` to remove by value
- Use `.size()` for count

---

### Exercise 3: HashMap Basics
**Objective:** Practice HashMap key-value operations

**Task:**
Create a HashMap to store test environment configurations (env=QA, browser=Chrome, timeout=10). Print all configurations, update timeout to 20, and print again.

**Starter Code:**
```java
import java.util.HashMap;

public class Exercise3 {
    public static void main(String[] args) {
        // Create HashMap for config

        // Add 3 key-value pairs

        // Print all configurations

        // Update timeout value

        // Print updated configurations
    }
}
}
```

**Expected Output:**
```
Initial Configuration:
env = QA
browser = Chrome
timeout = 10

Updated Configuration:
env = QA
browser = Chrome
timeout = 20
```

**Hints:**
- Use `HashMap<String, String> config = new HashMap<>();`
- Use `.put(key, value)` to add or update
- Iterate using `for (String key : config.keySet())`
- Updating uses the same `.put()` method

---

## 💪 Core Practice (30 min)

### Exercise 4: Custom Exception for Test Validation
**Objective:** Create and use custom exceptions for input validation

**Problem Statement:**
Create a custom exception called `InvalidPasswordException`. Write a method that validates passwords with these rules:
- Cannot be null or empty
- Must be at least 8 characters
- Must contain at least one digit

If validation fails, throw the custom exception with a descriptive message.

**Requirements:**
- Create `InvalidPasswordException` class extending `Exception`
- Create `validatePassword()` method that throws the custom exception
- Test with valid and invalid passwords in main method

**Starter Code:**
```java
// TODO: Create custom exception class
class InvalidPasswordException extends Exception {
    // Constructor that accepts error message
}

public class Exercise4 {

    // TODO: Create validatePassword method
    public static void validatePassword(String password) throws InvalidPasswordException {
        // Check if null or empty
        // Check minimum length
        // Check for at least one digit
        // If all validations pass, print "Password is valid"
    }

    public static void main(String[] args) {
        String[] testPasswords = {
            "Pass123",      // Valid
            "short",        // Too short
            "nodigitshere", // No digits
            null,           // Null
            ""              // Empty
        };

        // TODO: Test each password with try-catch
    }
}
```

**Test Cases:**
```
Input: "Pass123"
Expected Output: Password is valid

Input: "short"
Expected Output: Password validation failed: Password must be at least 8 characters

Input: "nodigitshere"
Expected Output: Password validation failed: Password must contain at least one digit

Input: null
Expected Output: Password validation failed: Password cannot be null or empty
```

**Hints:**
- Custom exception: `public InvalidPasswordException(String message) { super(message); }`
- Check null/empty: `if (password == null || password.isEmpty())`
- Check length: `if (password.length() < 8)`
- Check digit: Use loop with `Character.isDigit(password.charAt(i))`

---

### Exercise 5: Test Results Manager with Collections
**Objective:** Use ArrayList and HashMap together for data management

**Problem Statement:**
Build a test results manager that stores test case results. Each test has a name and status (PASS/FAIL). Implement methods to:
1. Add test results
2. Get total tests executed
3. Get pass count and fail count
4. Display all results

Use ArrayList to store test names in order and HashMap to store test status.

**Requirements:**
- Store test names in ArrayList
- Store test results in HashMap (test name -> status)
- Calculate pass/fail statistics
- Display formatted results

**Starter Code:**
```java
import java.util.ArrayList;
import java.util.HashMap;

public class Exercise5 {

    // Collections to store test data
    static ArrayList<String> testNames = new ArrayList<>();
    static HashMap<String, String> testResults = new HashMap<>();

    // TODO: Method to add test result
    public static void addTestResult(String testName, String status) {
        // Add to ArrayList
        // Add to HashMap
    }

    // TODO: Method to get pass count
    public static int getPassCount() {
        int count = 0;
        // Iterate through HashMap and count PASS
        return count;
    }

    // TODO: Method to get fail count
    public static int getFailCount() {
        int count = 0;
        // Iterate through HashMap and count FAIL
        return count;
    }

    // TODO: Method to display all results
    public static void displayResults() {
        System.out.println("Test Execution Summary");
        System.out.println("=====================");
        // Display each test and its status
        System.out.println("\nStatistics:");
        // Display total, pass, fail counts
    }

    public static void main(String[] args) {
        // Add test results
        addTestResult("LoginTest", "PASS");
        addTestResult("SearchTest", "PASS");
        addTestResult("CheckoutTest", "FAIL");
        addTestResult("LogoutTest", "PASS");
        addTestResult("ProfileTest", "FAIL");

        // Display results
        displayResults();
    }
}
```

**Expected Output:**
```
Test Execution Summary
=====================
1. LoginTest: PASS
2. SearchTest: PASS
3. CheckoutTest: FAIL
4. LogoutTest: PASS
5. ProfileTest: FAIL

Statistics:
Total Tests: 5
Passed: 3
Failed: 2
Pass Rate: 60.0%
```

**Hints:**
- Use `testNames.add()` and `testResults.put()` in addTestResult
- Iterate ArrayList with index: `for (int i = 0; i < testNames.size(); i++)`
- Get status from HashMap: `testResults.get(testName)`
- Calculate percentage: `(passCount * 100.0) / totalCount`

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Duplicate Email Detector with Exception Handling
**Objective:** Combine HashSet, exceptions, and validation in a real-world scenario

**Requirements:**
Create a program that manages user email registrations. Use HashSet to prevent duplicate emails. Implement:
1. Custom exception `DuplicateEmailException`
2. Method to register email (throws exception if duplicate)
3. Method to display all registered emails
4. Proper exception handling in main method

**Starter Code:**
```java
import java.util.HashSet;

// TODO: Create custom exception
class DuplicateEmailException extends Exception {
    // Constructor
}

public class Exercise6 {

    static HashSet<String> registeredEmails = new HashSet<>();

    // TODO: Method to register email
    public static void registerEmail(String email) throws DuplicateEmailException {
        // Validate email is not null or empty
        // Check if email already exists in HashSet
        // If exists, throw DuplicateEmailException
        // If not, add to HashSet
        // Print success message
    }

    // TODO: Method to display all emails
    public static void displayRegisteredEmails() {
        System.out.println("\nRegistered Emails:");
        System.out.println("==================");
        // Display all emails with numbering
        System.out.println("Total registered: " + registeredEmails.size());
    }

    public static void main(String[] args) {
        String[] emails = {
            "user1@test.com",
            "user2@test.com",
            "user1@test.com",  // Duplicate
            "admin@test.com",
            "user2@test.com"   // Duplicate
        };

        // TODO: Register each email with exception handling
        // Print appropriate messages for success and duplicates

        // Display final list
        displayRegisteredEmails();
    }
}
```

**Expected Output:**
```
Registering: user1@test.com
Successfully registered: user1@test.com

Registering: user2@test.com
Successfully registered: user2@test.com

Registering: user1@test.com
Error: Email already registered: user1@test.com

Registering: admin@test.com
Successfully registered: admin@test.com

Registering: user2@test.com
Error: Email already registered: user2@test.com

Registered Emails:
==================
1. user1@test.com
2. user2@test.com
3. admin@test.com
Total registered: 3
```

**Testing Instructions:**
1. Run the program with the provided test emails
2. Verify duplicates are detected
3. Verify final list contains only unique emails
4. Try modifying the emails array to test edge cases

**Bonus Challenges:**
- [ ] Add email format validation (must contain @ and .)
- [ ] Make email comparison case-insensitive
- [ ] Add method to unregister (remove) an email
- [ ] Track registration timestamp using HashMap

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
public class Exercise1 {
    public static void main(String[] args) {
        int num1 = 100;
        int num2 = 0;

        try {
            // Attempt division
            int result = num1 / num2;
            System.out.println("Result: " + result);
        } catch (ArithmeticException e) {
            // Handle exception
            System.out.println("Error: Cannot divide by zero");
        } finally {
            // Always executes
            System.out.println("Calculation complete");
        }
    }
}
```

**Explanation:**
- The `try` block contains code that might throw an exception (division by zero)
- `catch` block catches `ArithmeticException` and handles it gracefully
- `finally` block executes regardless of whether exception occurred
- This pattern is essential for cleanup operations in test automation (closing browser, files, etc.)

**Key Takeaways:**
- Always use specific exception types when possible
- Finally block is perfect for cleanup code
- Program doesn't crash - it handles the error gracefully

---

### Exercise 2 Solution
```java
import java.util.ArrayList;

public class Exercise2 {
    public static void main(String[] args) {
        // Create ArrayList of browsers
        ArrayList<String> browsers = new ArrayList<>();

        // Add 4 browsers
        browsers.add("Chrome");
        browsers.add("Firefox");
        browsers.add("Edge");
        browsers.add("Safari");

        // Print original list and count
        System.out.println("Browsers: " + browsers);
        System.out.println("Total browsers: " + browsers.size());

        // Remove Firefox
        browsers.remove("Firefox");

        // Print updated list and count
        System.out.println("Browsers after removal: " + browsers);
        System.out.println("Total browsers: " + browsers.size());
    }
}
```

**Explanation:**
- `ArrayList<String>` creates a type-safe list that only accepts Strings
- `add()` method appends elements to the end of the list
- `remove()` can take either an index or the object itself
- `size()` returns the number of elements
- ArrayList maintains insertion order

**Key Takeaways:**
- ArrayList is perfect for maintaining ordered collections
- Generic type `<String>` ensures type safety
- ArrayList size is dynamic - grows and shrinks automatically

---

### Exercise 3 Solution
```java
import java.util.HashMap;

public class Exercise3 {
    public static void main(String[] args) {
        // Create HashMap for config
        HashMap<String, String> config = new HashMap<>();

        // Add 3 key-value pairs
        config.put("env", "QA");
        config.put("browser", "Chrome");
        config.put("timeout", "10");

        // Print all configurations
        System.out.println("Initial Configuration:");
        for (String key : config.keySet()) {
            System.out.println(key + " = " + config.get(key));
        }

        // Update timeout value
        config.put("timeout", "20");

        // Print updated configurations
        System.out.println("\nUpdated Configuration:");
        for (String key : config.keySet()) {
            System.out.println(key + " = " + config.get(key));
        }
    }
}
```

**Explanation:**
- `HashMap<String, String>` stores key-value pairs (both Strings)
- `put()` method adds new pairs or updates existing ones
- `keySet()` returns all keys for iteration
- `get(key)` retrieves the value for a specific key
- Calling `put()` with existing key updates the value

**Key Takeaways:**
- HashMap is ideal for configuration management
- Keys must be unique; values can be duplicated
- Iterating through keySet() gives access to all entries

---

### Exercise 4 Solution
```java
// Custom exception class
class InvalidPasswordException extends Exception {
    public InvalidPasswordException(String message) {
        super(message);
    }
}

public class Exercise4 {

    public static void validatePassword(String password) throws InvalidPasswordException {
        // Check if null or empty
        if (password == null || password.isEmpty()) {
            throw new InvalidPasswordException("Password cannot be null or empty");
        }

        // Check minimum length
        if (password.length() < 8) {
            throw new InvalidPasswordException("Password must be at least 8 characters");
        }

        // Check for at least one digit
        boolean hasDigit = false;
        for (int i = 0; i < password.length(); i++) {
            if (Character.isDigit(password.charAt(i))) {
                hasDigit = true;
                break;
            }
        }

        if (!hasDigit) {
            throw new InvalidPasswordException("Password must contain at least one digit");
        }

        System.out.println("Password is valid");
    }

    public static void main(String[] args) {
        String[] testPasswords = {
            "Pass123",      // Valid
            "short",        // Too short
            "nodigitshere", // No digits
            null,           // Null
            ""              // Empty
        };

        for (String password : testPasswords) {
            try {
                System.out.println("\nTesting password: " + password);
                validatePassword(password);
            } catch (InvalidPasswordException e) {
                System.out.println("Password validation failed: " + e.getMessage());
            }
        }
    }
}
```

**Explanation:**
- Custom exception extends `Exception` for checked exception behavior
- Constructor calls `super(message)` to pass message to parent class
- Method declares `throws InvalidPasswordException` in signature
- Validation checks happen in sequence; first failure throws exception
- Each test password is validated in try-catch block

**Key Takeaways:**
- Custom exceptions make error messages domain-specific
- Throwing exceptions stops method execution immediately
- Caller must handle checked exceptions with try-catch
- This pattern is perfect for test data validation

---

### Exercise 5 Solution
```java
import java.util.ArrayList;
import java.util.HashMap;

public class Exercise5 {

    static ArrayList<String> testNames = new ArrayList<>();
    static HashMap<String, String> testResults = new HashMap<>();

    public static void addTestResult(String testName, String status) {
        testNames.add(testName);
        testResults.put(testName, status);
    }

    public static int getPassCount() {
        int count = 0;
        for (String status : testResults.values()) {
            if (status.equals("PASS")) {
                count++;
            }
        }
        return count;
    }

    public static int getFailCount() {
        int count = 0;
        for (String status : testResults.values()) {
            if (status.equals("FAIL")) {
                count++;
            }
        }
        return count;
    }

    public static void displayResults() {
        System.out.println("Test Execution Summary");
        System.out.println("=====================");

        // Display each test and its status
        for (int i = 0; i < testNames.size(); i++) {
            String testName = testNames.get(i);
            String status = testResults.get(testName);
            System.out.println((i + 1) + ". " + testName + ": " + status);
        }

        // Display statistics
        System.out.println("\nStatistics:");
        int total = testNames.size();
        int passed = getPassCount();
        int failed = getFailCount();
        double passRate = (passed * 100.0) / total;

        System.out.println("Total Tests: " + total);
        System.out.println("Passed: " + passed);
        System.out.println("Failed: " + failed);
        System.out.printf("Pass Rate: %.1f%%\n", passRate);
    }

    public static void main(String[] args) {
        addTestResult("LoginTest", "PASS");
        addTestResult("SearchTest", "PASS");
        addTestResult("CheckoutTest", "FAIL");
        addTestResult("LogoutTest", "PASS");
        addTestResult("ProfileTest", "FAIL");

        displayResults();
    }
}
```

**Explanation:**
- ArrayList maintains test execution order
- HashMap stores test results for quick lookup
- `values()` method returns all values in HashMap
- Loop through ArrayList with index for numbered display
- Calculate statistics by counting PASS/FAIL values

**Key Takeaways:**
- Combining ArrayList and HashMap leverages benefits of both
- ArrayList for order, HashMap for lookups
- This pattern is common in test reporting frameworks
- Static collections allow access from all methods

---

### Exercise 6 Solution
```java
import java.util.HashSet;

class DuplicateEmailException extends Exception {
    public DuplicateEmailException(String message) {
        super(message);
    }
}

public class Exercise6 {

    static HashSet<String> registeredEmails = new HashSet<>();

    public static void registerEmail(String email) throws DuplicateEmailException {
        // Validate email is not null or empty
        if (email == null || email.isEmpty()) {
            throw new IllegalArgumentException("Email cannot be null or empty");
        }

        // Check if email already exists
        if (registeredEmails.contains(email)) {
            throw new DuplicateEmailException("Email already registered: " + email);
        }

        // Add to HashSet
        registeredEmails.add(email);
        System.out.println("Successfully registered: " + email);
    }

    public static void displayRegisteredEmails() {
        System.out.println("\nRegistered Emails:");
        System.out.println("==================");

        int count = 1;
        for (String email : registeredEmails) {
            System.out.println(count + ". " + email);
            count++;
        }

        System.out.println("Total registered: " + registeredEmails.size());
    }

    public static void main(String[] args) {
        String[] emails = {
            "user1@test.com",
            "user2@test.com",
            "user1@test.com",  // Duplicate
            "admin@test.com",
            "user2@test.com"   // Duplicate
        };

        for (String email : emails) {
            try {
                System.out.println("\nRegistering: " + email);
                registerEmail(email);
            } catch (DuplicateEmailException e) {
                System.out.println("Error: " + e.getMessage());
            }
        }

        displayRegisteredEmails();
    }
}
```

**Explanation:**
- HashSet automatically prevents duplicates
- `contains()` checks if element already exists
- Custom exception provides clear error message
- Loop processes all emails, catching duplicates
- HashSet's uniqueness property ensures no duplicates in final list

**Key Takeaways:**
- HashSet is perfect for duplicate detection
- Custom exceptions make error messages business-specific
- Exception handling doesn't stop the loop - program continues
- This pattern is valuable for user registration systems

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
- [ ] Exception handling syntax
- [ ] Custom exceptions
- [ ] ArrayList operations
- [ ] HashMap operations
- [ ] HashSet uniqueness
- [ ] Iterating collections
- [ ] Generics syntax

**Areas I excelled at:**
- [ ] Understanding try-catch flow
- [ ] Working with ArrayList
- [ ] Using HashMap
- [ ] Creating custom exceptions
- [ ] Combining multiple collections

---

## ✅ Answers to Self-Check Questions (from Concept Guide)

1. **What is the purpose of the finally block, and when does it execute?**
   - The finally block contains cleanup code that always executes, whether an exception occurred or not. It's perfect for closing resources like browsers, files, or database connections.

2. **What's the difference between checked and unchecked exceptions?**
   - Checked exceptions (extend Exception) must be declared with `throws` or handled with try-catch. Unchecked exceptions (extend RuntimeException) don't require declaration. Custom exceptions for validation are typically checked.

3. **When would you use a HashSet instead of an ArrayList?**
   - Use HashSet when you need unique elements and don't care about order. Use ArrayList when order matters or duplicates are allowed. Example: HashSet for unique user IDs, ArrayList for ordered test results.

4. **What does `ArrayList<String>` mean, and why is it better than `ArrayList`?**
   - `<String>` is a generic type parameter that ensures only Strings can be added. It provides compile-time type safety, prevents runtime errors, and eliminates the need for casting.

5. **How do you iterate through a HashMap and access both keys and values?**
   - Use `for (String key : map.keySet())` to iterate keys, then `map.get(key)` for values. Or use `for (Map.Entry<K,V> entry : map.entrySet())` to access both directly with `entry.getKey()` and `entry.getValue()`.

---
