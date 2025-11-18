# DAY 2 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master control flow structures (if-else, switch, for, while, do-while loops) to add decision-making and repetition to your programs. By the end, you'll build a Number Guessing Game that combines all these concepts.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Test Status Checker (if-else)
**Objective:** Practice basic if-else statements for decision making

**Task:**
Create a program that checks test execution status and prints appropriate messages.

**Requirements:**
- Declare a test status code (int): 0=Pass, 1=Fail, 2=Skip
- Use if-else to print the appropriate message
- Add color-coded output indicators

**Starter Code:**
```java
public class Exercise1 {
    public static void main(String[] args) {
        // Test status: 0=Pass, 1=Fail, 2=Skip
        int testStatus = 0;

        // TODO: Add if-else logic to print status message

    }
}
```

**Expected Output:**
```
✓ Test Status: PASSED
All assertions successful!
```

**Hints:**
- Use `if (testStatus == 0)` for the first condition
- Chain with `else if` for additional conditions
- Use `else` for the default case

---

### Exercise 2: Environment Selector (switch)
**Objective:** Practice switch statements for multiple branching

**Task:**
Write a program that selects the appropriate URL based on environment name.

**Starter Code:**
```java
public class Exercise2 {
    public static void main(String[] args) {
        String environment = "staging";
        String baseUrl;

        // TODO: Use switch statement to set baseUrl based on environment
        // dev -> http://localhost:3000
        // qa -> https://qa.example.com
        // staging -> https://staging.example.com
        // prod -> https://www.example.com
        // default -> print error message

        System.out.println("Environment: " + environment);
        System.out.println("Base URL: " + baseUrl);
    }
}
```

**Expected Output:**
```
Environment: staging
Base URL: https://staging.example.com
```

**Hints:**
- Switch on the `environment` variable
- Don't forget `break;` after each case
- Use `default:` for unrecognized environments

---

### Exercise 3: Test Suite Runner (for loop)
**Objective:** Practice for loops for iteration

**Task:**
Simulate running 5 test cases and print execution messages.

**Starter Code:**
```java
public class Exercise3 {
    public static void main(String[] args) {
        int totalTests = 5;

        System.out.println("===== Test Suite Execution =====");

        // TODO: Use for loop to run tests 1 through 5
        // Print: "Running Test #X..."

        System.out.println("================================");
        System.out.println("All tests completed!");
    }
}
```

**Expected Output:**
```
===== Test Suite Execution =====
Running Test #1...
Running Test #2...
Running Test #3...
Running Test #4...
Running Test #5...
================================
All tests completed!
```

**Hints:**
- Use: `for (int i = 1; i <= totalTests; i++)`
- Loop variable `i` represents the test number
- Remember to include the closing brace

---

## 💪 Core Practice (30 min)

### Exercise 4: Pass Rate Validator
**Objective:** Combine if-else with calculations and validation

**Problem Statement:**
Create a program that calculates test pass rate and determines if it meets quality standards. This simulates real test automation reporting.

**Requirements:**
- Accept total tests, passed tests, and failed tests
- Calculate pass rate percentage
- Validate that passed + failed equals total
- Determine quality grade:
  - Pass rate >= 90%: "Excellent"
  - Pass rate >= 75%: "Good"
  - Pass rate >= 60%: "Acceptable"
  - Pass rate < 60%: "Needs Improvement"
- Print detailed report with appropriate messages

**Starter Code:**
```java
public class Exercise4 {
    public static void main(String[] args) {
        // Test execution data
        int totalTests = 50;
        int passedTests = 42;
        int failedTests = 8;

        // TODO: Validate that passed + failed = total

        // TODO: Calculate pass rate

        // TODO: Determine quality grade using if-else

        // TODO: Print detailed report

    }
}
```

**Test Cases:**
```
Input: total=50, passed=42, failed=8
Expected Output:
===== Test Execution Report =====
Total Tests: 50
Passed: 42
Failed: 8
Pass Rate: 84.00%
Quality Grade: Good
================================
```

**Hints:**
- Validate first: `if (passedTests + failedTests != totalTests)`
- Calculate pass rate: `(passedTests * 100.0) / totalTests`
- Use nested if-else for grade determination
- Format percentage: `String.format("%.2f%%", passRate)`

---

### Exercise 5: Retry Logic Simulator
**Objective:** Practice while loops for conditional repetition

**Problem Statement:**
Simulate a test retry mechanism that attempts to pass a flaky test up to 3 times before giving up.

**Requirements:**
- Start with attempt counter at 0
- Use a boolean `testPassed` to track success (hardcode it to fail first 2 attempts, pass on 3rd)
- Maximum 3 attempts allowed
- Print each attempt number
- Use while loop to retry until pass or max attempts reached
- Print final result

**Starter Code:**
```java
public class Exercise5 {
    public static void main(String[] args) {
        final int MAX_ATTEMPTS = 3;
        int attemptCount = 0;
        boolean testPassed = false;

        System.out.println("===== Test Retry Mechanism =====");

        // TODO: Use while loop to attempt test up to 3 times
        while (attemptCount < MAX_ATTEMPTS && !testPassed) {
            attemptCount++;
            System.out.println("Attempt #" + attemptCount + "...");

            // TODO: Simulate test execution (fail first 2, pass on 3rd)
            if (attemptCount == 3) {
                testPassed = true;
                System.out.println("✓ Test PASSED!");
            } else {
                System.out.println("✗ Test FAILED - Retrying...");
            }
        }

        // TODO: Print final result

    }
}
```

**Expected Output:**
```
===== Test Retry Mechanism =====
Attempt #1...
✗ Test FAILED - Retrying...
Attempt #2...
✗ Test FAILED - Retrying...
Attempt #3...
✓ Test PASSED!
================================
Final Result: Test passed after 3 attempts
```

**Hints:**
- While condition: `attemptCount < MAX_ATTEMPTS && !testPassed`
- Increment counter at the start of loop
- Check attempt number to simulate flaky behavior
- Print summary after loop completes

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Number Guessing Game (Mini Project Preview)
**Objective:** Build a simplified version of today's deliverable combining all control flow concepts

**Requirements:**
Create a number guessing game that:
1. Generates a "random" number between 1-10 (hardcode it to 7 for now)
2. User has 5 guesses (hardcode the guesses in an array)
3. For each guess, provide feedback: "Too high", "Too low", or "Correct!"
4. Use a for loop to iterate through guesses
5. Use if-else to compare guess to target
6. Track if the number was found
7. Print win/lose message

**Starter Code:**
```java
public class Exercise6 {
    public static void main(String[] args) {
        // Target number (secret)
        final int TARGET_NUMBER = 7;
        final int MAX_GUESSES = 5;

        // Simulated user guesses (later we'll use Scanner for real input)
        int[] guesses = {3, 8, 7, 0, 0};  // User guesses 3, then 8, then 7

        boolean numberFound = false;
        int attempts = 0;

        System.out.println("===== Number Guessing Game =====");
        System.out.println("Guess a number between 1 and 10!");
        System.out.println("You have " + MAX_GUESSES + " attempts.");
        System.out.println("================================");

        // TODO: Use for loop to process each guess

        // TODO: Print final result (win or lose)

    }
}
```

**Expected Output:**
```
===== Number Guessing Game =====
Guess a number between 1 and 10!
You have 5 attempts.
================================
Attempt #1: You guessed 3
Too low! Try again.

Attempt #2: You guessed 8
Too high! Try again.

Attempt #3: You guessed 7
🎉 Correct! You found the number!
================================
You won in 3 attempts!
```

**Testing Instructions:**
Test with these scenarios:
1. Guesses: {3, 8, 7} - should win on attempt 3
2. Guesses: {1, 2, 3, 4, 5} - should lose (never finds 7)
3. Guesses: {7, 0, 0, 0, 0} - should win on attempt 1

**Bonus Challenges:**
- [ ] Count and display the number of incorrect guesses
- [ ] Keep track of all guessed numbers and display them
- [ ] Add validation to ignore guesses outside 1-10 range
- [ ] Display remaining attempts after each guess

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
public class Exercise1 {
    public static void main(String[] args) {
        // Test status: 0=Pass, 1=Fail, 2=Skip
        int testStatus = 0;

        // Check status and print appropriate message
        if (testStatus == 0) {
            System.out.println("✓ Test Status: PASSED");
            System.out.println("All assertions successful!");
        } else if (testStatus == 1) {
            System.out.println("✗ Test Status: FAILED");
            System.out.println("One or more assertions failed.");
        } else if (testStatus == 2) {
            System.out.println("⊘ Test Status: SKIPPED");
            System.out.println("Test was not executed.");
        } else {
            System.out.println("? Test Status: UNKNOWN");
            System.out.println("Invalid status code: " + testStatus);
        }
    }
}
```

**Explanation:**
- `if (testStatus == 0)` checks for pass condition
- `else if` handles additional specific conditions (fail, skip)
- `else` catches any unexpected values
- Each block has its own print statements
- Using Unicode symbols (✓, ✗, ⊘) makes output more visual

**Key Takeaways:**
- if-else allows different code paths based on conditions
- Use `==` for comparing numbers, `.equals()` for strings
- else-if chains can handle multiple specific cases
- Always include a final `else` for unexpected values

---

### Exercise 2 Solution
```java
public class Exercise2 {
    public static void main(String[] args) {
        String environment = "staging";
        String baseUrl;

        // Select URL based on environment
        switch (environment) {
            case "dev":
                baseUrl = "http://localhost:3000";
                break;
            case "qa":
                baseUrl = "https://qa.example.com";
                break;
            case "staging":
                baseUrl = "https://staging.example.com";
                break;
            case "prod":
                baseUrl = "https://www.example.com";
                break;
            default:
                baseUrl = "ERROR: Unknown environment";
                System.out.println("Warning: Invalid environment '" + environment + "'");
                break;
        }

        System.out.println("Environment: " + environment);
        System.out.println("Base URL: " + baseUrl);
    }
}
```

**Explanation:**
- `switch (environment)` evaluates the string once
- Each `case` represents a possible value
- `break;` is crucial - without it, execution "falls through" to next case
- `default:` handles any unrecognized environment
- Switch is cleaner than multiple if-else when checking one variable against many values

**Key Takeaways:**
- Switch works with: int, byte, short, char, String, enum
- ALWAYS include `break;` unless you want fall-through behavior
- Use `default:` like `else` - catches everything not explicitly handled
- Perfect for configuration selection in test automation

---

### Exercise 3 Solution
```java
public class Exercise3 {
    public static void main(String[] args) {
        int totalTests = 5;

        System.out.println("===== Test Suite Execution =====");

        // Run each test in sequence
        for (int i = 1; i <= totalTests; i++) {
            System.out.println("Running Test #" + i + "...");
        }

        System.out.println("================================");
        System.out.println("All tests completed!");
    }
}
```

**Explanation:**
- `for (int i = 1; i <= totalTests; i++)` breaks down as:
  - `int i = 1` - initialization (runs once at start)
  - `i <= totalTests` - condition (checked before each iteration)
  - `i++` - increment (runs after each iteration)
- Loop body executes 5 times: when i=1, 2, 3, 4, 5
- After i becomes 6, condition `i <= 5` is false, loop ends

**Key Takeaways:**
- For loops are perfect when you know how many iterations you need
- Loop variable (i) is commonly used for counters and array indices
- Can start from any number and increment by any amount
- Common pattern: `for (int i = 0; i < length; i++)` for arrays

---

### Exercise 4 Solution
```java
public class Exercise4 {
    public static void main(String[] args) {
        // Test execution data
        int totalTests = 50;
        int passedTests = 42;
        int failedTests = 8;

        // Validate test counts
        if (passedTests + failedTests != totalTests) {
            System.out.println("ERROR: Test counts don't add up!");
            System.out.println("Passed (" + passedTests + ") + Failed (" + failedTests +
                             ") should equal Total (" + totalTests + ")");
            return;  // Exit the program
        }

        // Calculate pass rate
        double passRate = (passedTests * 100.0) / totalTests;

        // Determine quality grade
        String grade;
        String recommendation;

        if (passRate >= 90.0) {
            grade = "Excellent";
            recommendation = "Quality standards exceeded!";
        } else if (passRate >= 75.0) {
            grade = "Good";
            recommendation = "Quality standards met.";
        } else if (passRate >= 60.0) {
            grade = "Acceptable";
            recommendation = "Consider investigating failures.";
        } else {
            grade = "Needs Improvement";
            recommendation = "CRITICAL: Review and fix failing tests!";
        }

        // Print detailed report
        System.out.println("===== Test Execution Report =====");
        System.out.println("Total Tests: " + totalTests);
        System.out.println("Passed: " + passedTests);
        System.out.println("Failed: " + failedTests);
        System.out.println("Pass Rate: " + String.format("%.2f%%", passRate));
        System.out.println("Quality Grade: " + grade);
        System.out.println(recommendation);
        System.out.println("================================");
    }
}
```

**Explanation:**
- First validates data integrity (passed + failed must equal total)
- `return;` exits the method early if validation fails
- Calculates pass rate with decimal precision (`100.0` not `100`)
- Uses nested if-else to assign grade based on thresholds
- Formats percentage with 2 decimal places: `String.format("%.2f%%", passRate)`
- Double `%%` in format string displays a single `%` character

**Key Takeaways:**
- Always validate input data before calculations
- Early return prevents executing invalid logic
- Nested if-else for range-based categorization
- This pattern is used extensively in test reporting frameworks
- String.format() provides professional output formatting

---

### Exercise 5 Solution
```java
public class Exercise5 {
    public static void main(String[] args) {
        final int MAX_ATTEMPTS = 3;
        int attemptCount = 0;
        boolean testPassed = false;

        System.out.println("===== Test Retry Mechanism =====");

        // Retry loop - continue while attempts remain AND test hasn't passed
        while (attemptCount < MAX_ATTEMPTS && !testPassed) {
            attemptCount++;
            System.out.println("Attempt #" + attemptCount + "...");

            // Simulate flaky test (fails first 2, passes on 3rd)
            if (attemptCount == 3) {
                testPassed = true;
                System.out.println("✓ Test PASSED!");
            } else {
                System.out.println("✗ Test FAILED - Retrying...");
            }
        }

        System.out.println("================================");

        // Print final result
        if (testPassed) {
            System.out.println("Final Result: Test passed after " + attemptCount + " attempts");
        } else {
            System.out.println("Final Result: Test failed after " + attemptCount + " attempts");
            System.out.println("Maximum retry limit reached.");
        }
    }
}
```

**Explanation:**
- While loop condition: `attemptCount < MAX_ATTEMPTS && !testPassed`
  - Continues if attempts remain AND test hasn't passed yet
  - `!testPassed` means "not passed" (still false)
- Loop stops when: attempts exhausted OR test passes
- Increment counter at start of each iteration
- After loop, check `testPassed` to determine outcome

**Key Takeaways:**
- While loops for unknown/conditional iterations
- Compound conditions with `&&` (both must be true to continue)
- `!` negates boolean (flips true/false)
- This pattern is critical for handling flaky tests in real automation
- Always track loop variables to prevent infinite loops

---

### Exercise 6 Solution (Number Guessing Game)
```java
public class Exercise6 {
    public static void main(String[] args) {
        // Target number (secret)
        final int TARGET_NUMBER = 7;
        final int MAX_GUESSES = 5;

        // Simulated user guesses
        int[] guesses = {3, 8, 7, 0, 0};

        boolean numberFound = false;
        int attempts = 0;

        System.out.println("===== Number Guessing Game =====");
        System.out.println("Guess a number between 1 and 10!");
        System.out.println("You have " + MAX_GUESSES + " attempts.");
        System.out.println("================================");

        // Process each guess
        for (int i = 0; i < MAX_GUESSES; i++) {
            int currentGuess = guesses[i];

            // Skip if guess is 0 (no more guesses)
            if (currentGuess == 0) {
                break;  // Exit loop early
            }

            attempts++;
            System.out.println("\nAttempt #" + attempts + ": You guessed " + currentGuess);

            // Compare guess to target
            if (currentGuess == TARGET_NUMBER) {
                System.out.println("🎉 Correct! You found the number!");
                numberFound = true;
                break;  // Exit loop - game won!
            } else if (currentGuess < TARGET_NUMBER) {
                System.out.println("Too low! Try again.");
            } else {
                System.out.println("Too high! Try again.");
            }
        }

        // Print final result
        System.out.println("================================");
        if (numberFound) {
            System.out.println("🏆 You won in " + attempts + " attempts!");
        } else {
            System.out.println("😞 Game Over! The number was " + TARGET_NUMBER);
            System.out.println("You used all " + attempts + " attempts.");
        }
    }
}
```

**Explanation:**
- Array stores predefined guesses: `int[] guesses = {3, 8, 7, 0, 0};`
- For loop iterates through array: `for (int i = 0; i < MAX_GUESSES; i++)`
- `break;` exits loop immediately when number is found or no more guesses
- Nested if-else provides "too high" or "too low" feedback
- Boolean flag `numberFound` tracks success
- Final if-else determines win/loss message

**Bonus Solution (with all challenges):**
```java
public class Exercise6Bonus {
    public static void main(String[] args) {
        final int TARGET_NUMBER = 7;
        final int MAX_GUESSES = 5;
        int[] guesses = {3, 8, 7, 0, 0};

        boolean numberFound = false;
        int attempts = 0;
        int incorrectGuesses = 0;
        String guessHistory = "";

        System.out.println("===== Number Guessing Game =====");
        System.out.println("Guess a number between 1 and 10!");
        System.out.println("You have " + MAX_GUESSES + " attempts.");
        System.out.println("================================");

        for (int i = 0; i < MAX_GUESSES; i++) {
            int currentGuess = guesses[i];

            if (currentGuess == 0) break;

            attempts++;
            int remaining = MAX_GUESSES - attempts;

            // Validate guess range
            if (currentGuess < 1 || currentGuess > 10) {
                System.out.println("\n⚠ Invalid guess: " + currentGuess +
                                 " (must be 1-10) - doesn't count!");
                attempts--;  // Don't count invalid guesses
                continue;  // Skip to next iteration
            }

            // Add to history
            if (!guessHistory.isEmpty()) {
                guessHistory += ", ";
            }
            guessHistory += currentGuess;

            System.out.println("\nAttempt #" + attempts + ": You guessed " + currentGuess);
            System.out.println("Remaining attempts: " + remaining);

            if (currentGuess == TARGET_NUMBER) {
                System.out.println("🎉 Correct! You found the number!");
                numberFound = true;
                break;
            } else if (currentGuess < TARGET_NUMBER) {
                System.out.println("Too low! Try again.");
                incorrectGuesses++;
            } else {
                System.out.println("Too high! Try again.");
                incorrectGuesses++;
            }
        }

        System.out.println("================================");
        System.out.println("Your guesses: " + guessHistory);

        if (numberFound) {
            System.out.println("🏆 You won in " + attempts + " attempts!");
            System.out.println("Incorrect guesses: " + incorrectGuesses);
        } else {
            System.out.println("😞 Game Over! The number was " + TARGET_NUMBER);
        }
    }
}
```

**Key Takeaways:**
- Combining loops, conditionals, and arrays creates interactive programs
- `break;` exits loop immediately
- `continue;` skips to next iteration
- String concatenation builds history: `history += ", " + value`
- This exercise demonstrates all Day 2 concepts in one program!

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Test Status (if-else) | ☐ | __/10 | ___ min | |
| 2 - Environment (switch) | ☐ | __/10 | ___ min | |
| 3 - Test Runner (for) | ☐ | __/10 | ___ min | |
| 4 - Pass Rate Validator | ☐ | __/10 | ___ min | |
| 5 - Retry Mechanism | ☐ | __/10 | ___ min | |
| 6 - Number Guessing Game | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
- [ ] if-else syntax and logic
- [ ] Switch statement and breaks
- [ ] For loop structure
- [ ] While loop conditions
- [ ] Combining multiple control structures
- [ ] Arrays and indexing
- [ ] Other: _______________

**Areas I excelled at:**
- [ ] Understanding conditional logic
- [ ] Writing loop structures
- [ ] Debugging control flow issues
- [ ] Completing the guessing game
- [ ] Clean code organization
- [ ] Other: _______________

---

## 🎯 Control Flow Patterns for Automation

### Pattern 1: Test Execution Control
```java
// Run tests based on priority
String priority = "high";

switch (priority) {
    case "critical":
        runSmokeTests();
        runRegressionTests();
        break;
    case "high":
        runSmokeTests();
        break;
    case "medium":
        runSanityTests();
        break;
    default:
        System.out.println("Unknown priority");
}
```

### Pattern 2: Element Wait Loop
```java
// Wait for element to be visible (simplified Selenium pattern)
int maxWaitSeconds = 10;
int elapsedSeconds = 0;
boolean elementFound = false;

while (elapsedSeconds < maxWaitSeconds && !elementFound) {
    // Check if element exists
    if (checkElementExists()) {
        elementFound = true;
        System.out.println("Element found!");
    } else {
        Thread.sleep(1000);  // Wait 1 second
        elapsedSeconds++;
    }
}

if (!elementFound) {
    System.out.println("Timeout: Element not found");
}
```

### Pattern 3: Test Data Iteration
```java
// Run same test with different data sets
String[] browsers = {"Chrome", "Firefox", "Edge", "Safari"};

for (int i = 0; i < browsers.length; i++) {
    String browser = browsers[i];
    System.out.println("Testing on: " + browser);

    // Launch browser and run tests
    // ...
}
```

---

## 🚀 Next Step

Move to **Day-2-Debug-Practice.md** to practice debugging control flow errors!

**Practice complete:** ✅
**Confidence level (1-10):** ____
**Ready for debugging:** Yes / Need more practice

---
