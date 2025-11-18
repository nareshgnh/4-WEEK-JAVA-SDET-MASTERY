# DAY 2 DEBUGGING CHALLENGES

## 🐛 Why Control Flow Debugging?

Control flow bugs are among the most common in test automation:
- Infinite loops crash your test suite
- Missing breaks cause tests to run incorrectly
- Off-by-one errors skip test cases
- Wrong conditions cause false passes/failures

**Today's Focus:** Learn to spot and fix control flow errors quickly. These skills will save you hours of debugging in real automation projects.

---

## Challenge 1: Missing Break Statement

**Broken Code:**
```java
public class Debug1 {
    public static void main(String[] args) {
        String testType = "smoke";
        int testsToRun = 0;

        switch (testType) {
            case "smoke":
                testsToRun = 10;
            case "regression":
                testsToRun = 50;
            case "full":
                testsToRun = 200;
            default:
                testsToRun = 5;
        }

        System.out.println("Running " + testsToRun + " tests");
    }
}
```

**Expected Output:** `Running 10 tests`
**Actual Output:** `Running 5 tests`

**Your Task:**
1. Identify the bug
2. Explain why it produces wrong output
3. Fix it

**Hints:**
- Look at the switch statement - what happens after each case?
- Does execution stop after matching a case?

---

**Solution:**

**Bug Location:** Missing `break;` statements after each case in the switch

**Explanation:**
Without `break;` statements, execution "falls through" to the next case. Here's what happens:
1. `testType` is "smoke", so execution enters the first case
2. `testsToRun = 10` is executed
3. **NO BREAK** - execution continues to next case
4. `testsToRun = 50` is executed (overwrites 10!)
5. **NO BREAK** - execution continues to next case
6. `testsToRun = 200` is executed (overwrites 50!)
7. **NO BREAK** - execution continues to default
8. `testsToRun = 5` is executed (overwrites 200!)
9. Final value: 5

This is called "fall-through" behavior - it executes ALL cases from the match point onward.

**Fixed Code:**
```java
public class Debug1 {
    public static void main(String[] args) {
        String testType = "smoke";
        int testsToRun = 0;

        switch (testType) {
            case "smoke":
                testsToRun = 10;
                break;  // Added - stops execution here
            case "regression":
                testsToRun = 50;
                break;  // Added
            case "full":
                testsToRun = 200;
                break;  // Added
            default:
                testsToRun = 5;
                break;  // Optional for default but good practice
        }

        System.out.println("Running " + testsToRun + " tests");
    }
}
```

**Key Learning:**
- **ALWAYS add `break;` after each case** unless you intentionally want fall-through
- Missing breaks is one of the most common switch bugs
- In test automation, this could cause wrong test suites to run
- Some developers intentionally use fall-through, but it's rare and should be commented

---

## Challenge 2: Infinite Loop

**Broken Code:**
```java
public class Debug2 {
    public static void main(String[] args) {
        int testCount = 0;
        int maxTests = 5;

        System.out.println("Running tests...");

        while (testCount < maxTests) {
            System.out.println("Executing test #" + testCount);
            // Bug: forgot to increment testCount
        }

        System.out.println("All tests complete!");
    }
}
```

**Error Message:**
```
Running tests...
Executing test #0
Executing test #0
Executing test #0
Executing test #0
... (continues forever)
```

**Your Task:**
1. Identify why this creates an infinite loop
2. Explain what's missing
3. Fix it

**Hints:**
- What needs to change for the loop to eventually stop?
- Is `testCount` ever modified inside the loop?

---

**Solution:**

**Bug Location:** Line 8 - missing increment statement `testCount++;`

**Explanation:**
An infinite loop occurs when the loop condition never becomes false. In this case:
1. `testCount` starts at 0
2. Loop condition: `testCount < maxTests` (0 < 5) → true, enter loop
3. Print message
4. Loop repeats: condition check (0 < 5) → still true!
5. `testCount` never changes, so it's always 0
6. Loop never ends because 0 < 5 is always true

The loop has no way to progress toward completion because the loop variable never changes.

**Fixed Code:**
```java
public class Debug2 {
    public static void main(String[] args) {
        int testCount = 0;
        int maxTests = 5;

        System.out.println("Running tests...");

        while (testCount < maxTests) {
            System.out.println("Executing test #" + testCount);
            testCount++;  // Added - increments counter each iteration
        }

        System.out.println("All tests complete!");
    }
}
```

**Alternative Fix (using for loop instead):**
```java
public class Debug2Fixed {
    public static void main(String[] args) {
        int maxTests = 5;

        System.out.println("Running tests...");

        // For loop handles initialization, condition, and increment
        for (int testCount = 0; testCount < maxTests; testCount++) {
            System.out.println("Executing test #" + testCount);
        }

        System.out.println("All tests complete!");
    }
}
```

**Key Learning:**
- While loops need manual increment/update of loop variable
- For loops are safer when you know the iteration count (auto-increment)
- Always verify loop conditions can eventually become false
- In test automation, infinite loops can freeze your entire test suite
- If your program hangs, check for infinite loops first!

---

## Challenge 3: Off-by-One Error

**Code:**
```java
public class Debug3 {
    public static void main(String[] args) {
        String[] testCases = {"Login", "Search", "Checkout", "Logout"};

        System.out.println("Executing test suite:");

        // Run all test cases
        for (int i = 0; i <= testCases.length; i++) {
            System.out.println("Running: " + testCases[i]);
        }

        System.out.println("Suite complete!");
    }
}
```

**Error Message:**
```
Executing test suite:
Running: Login
Running: Search
Running: Checkout
Running: Logout
Exception in thread "main" java.lang.ArrayIndexOutOfBoundsException: Index 4 out of bounds for length 4
	at Debug3.main(Debug3.java:8)
```

**Your Task:**
This code crashes with an ArrayIndexOutOfBoundsException. Find and fix the bug.

**Hints:**
- Arrays in Java are 0-indexed
- What is the last valid index for an array of length 4?
- Look at the loop condition carefully

---

**Solution:**

**Bug Location:** Line 7 - loop condition uses `<=` instead of `<`

**Explanation:**
This is a classic "off-by-one" error:
- Array `testCases` has 4 elements
- Valid indices: 0, 1, 2, 3
- Array length: 4
- Loop condition: `i <= testCases.length` means `i <= 4`
- Loop runs when i = 0, 1, 2, 3, 4
- When i = 4, code tries to access `testCases[4]` which doesn't exist!
- Last valid index is always `length - 1`

**Why it crashes:**
```
testCases[0] = "Login"    ✓ Valid
testCases[1] = "Search"   ✓ Valid
testCases[2] = "Checkout" ✓ Valid
testCases[3] = "Logout"   ✓ Valid
testCases[4] = ???        ✗ ERROR - index out of bounds!
```

**Fixed Code:**
```java
public class Debug3 {
    public static void main(String[] args) {
        String[] testCases = {"Login", "Search", "Checkout", "Logout"};

        System.out.println("Executing test suite:");

        // Use < instead of <=
        for (int i = 0; i < testCases.length; i++) {
            System.out.println("Running: " + testCases[i]);
        }

        System.out.println("Suite complete!");
    }
}
```

**Key Learning:**
- Arrays are 0-indexed: first element at index 0, last at length-1
- Loop condition for arrays: `i < array.length` (NOT `<=`)
- Off-by-one errors are extremely common in loops
- In test automation, this could cause:
  - Skipping test cases (starting at 1 instead of 0)
  - Crashing when processing test data
  - Missing the last element in a collection
- **Memory trick:** "Less than length, not less-or-equal"

---

## Challenge 4: Wrong Condition Logic

**Code:**
```java
public class Debug4 {
    public static void main(String[] args) {
        int totalTests = 10;
        int passedTests = 7;
        int failedTests = 3;

        // Check if all tests passed
        if (passedTests = totalTests) {
            System.out.println("✓ All tests passed!");
        } else {
            System.out.println("✗ Some tests failed");
            System.out.println("Failed: " + failedTests);
        }
    }
}
```

**Error Message:**
```
java: incompatible types: int cannot be converted to boolean
```

**Your Task:**
1. Identify the syntax error
2. Explain the difference between the operators
3. Fix it

**Hints:**
- Look at the if condition carefully
- Is this comparing or assigning?
- What's the difference between `=` and `==`?

---

**Solution:**

**Bug Location:** Line 7 - uses `=` (assignment) instead of `==` (comparison)

**Explanation:**
- `=` is the **assignment operator** - it assigns a value to a variable
- `==` is the **comparison operator** - it compares two values
- `if (passedTests = totalTests)` tries to assign 10 to passedTests, not compare them
- The result of assignment is the value (10), not a boolean
- if statement requires a boolean condition (true/false)
- Java compiler error: "int cannot be converted to boolean"

**What happens:**
```java
if (passedTests = totalTests)  // Tries to assign 10 to passedTests
                               // Returns 10 (an int, not boolean)
                               // Compiler error!
```

**Fixed Code:**
```java
public class Debug4 {
    public static void main(String[] args) {
        int totalTests = 10;
        int passedTests = 7;
        int failedTests = 3;

        // Use == for comparison
        if (passedTests == totalTests) {
            System.out.println("✓ All tests passed!");
        } else {
            System.out.println("✗ Some tests failed");
            System.out.println("Failed: " + failedTests);
        }
    }
}
```

**Key Learning:**
- `=` → assignment (gives a value TO a variable)
- `==` → comparison (checks if two values are equal)
- Common mistake: writing `if (x = 5)` instead of `if (x == 5)`
- In some languages (C, JavaScript), this compiles but causes logic bugs
- Java catches this at compile-time - another advantage of strong typing!
- For strings, use `.equals()` not `==`

**Comparison operators:**
```java
==  equal to
!=  not equal to
>   greater than
<   less than
>=  greater than or equal to
<=  less than or equal to
```

---

## Challenge 5: Unreachable Code After Loop

**Code:**
```java
public class Debug5 {
    public static void main(String[] args) {
        String[] browsers = {"Chrome", "Firefox", "Safari"};
        boolean chromeFound = false;

        // Search for Chrome browser
        for (int i = 0; i < browsers.length; i++) {
            if (browsers[i].equals("Chrome")) {
                chromeFound = true;
                System.out.println("Chrome found at index " + i);
                break;
            } else {
                System.out.println("Chrome not found!");
                break;
            }
        }

        if (chromeFound) {
            System.out.println("✓ Chrome is supported");
        } else {
            System.out.println("✗ Chrome is not supported");
        }
    }
}
```

**Expected Output:**
```
Chrome found at index 0
✓ Chrome is supported
```

**Actual Output:**
```
Chrome found at index 0
✓ Chrome is supported
```

Wait, this looks correct! But there's a subtle bug...

**Try changing the array to:** `String[] browsers = {"Firefox", "Chrome", "Safari"};`

**Now the output is:**
```
Chrome not found!
✗ Chrome is not supported
```

**Your Task:**
The code works when Chrome is first but fails when Chrome is in the middle. Find and fix the logic error.

**Hints:**
- What happens when the first element is NOT Chrome?
- Should the loop exit after checking just one element?
- When should `break` be used?

---

**Solution:**

**Bug Location:** Line 12 - `break;` in the else block exits the loop prematurely

**Explanation:**
The bug is in the logic flow:
1. Loop starts, checks first element (Firefox)
2. `browsers[0].equals("Chrome")` → false
3. Goes to else block
4. Prints "Chrome not found!"
5. **Executes break;** → exits loop immediately
6. Never checks remaining elements (Chrome, Safari)
7. `chromeFound` stays false
8. Final message: "Chrome is not supported" (wrong!)

The break in the else block causes the loop to stop after checking ONLY the first element. It should continue searching through all elements.

**Fixed Code:**
```java
public class Debug5 {
    public static void main(String[] args) {
        String[] browsers = {"Firefox", "Chrome", "Safari"};
        boolean chromeFound = false;

        // Search for Chrome browser
        for (int i = 0; i < browsers.length; i++) {
            if (browsers[i].equals("Chrome")) {
                chromeFound = true;
                System.out.println("Chrome found at index " + i);
                break;  // OK to break here - we found it!
            }
            // Removed the else block with break
            // Let the loop continue to next iteration
        }

        if (chromeFound) {
            System.out.println("✓ Chrome is supported");
        } else {
            System.out.println("✗ Chrome is not supported");
        }
    }
}
```

**Better Solution (with helpful messages):**
```java
public class Debug5Better {
    public static void main(String[] args) {
        String[] browsers = {"Firefox", "Chrome", "Safari"};
        boolean chromeFound = false;
        int chromeIndex = -1;

        // Search for Chrome browser
        for (int i = 0; i < browsers.length; i++) {
            System.out.println("Checking: " + browsers[i]);

            if (browsers[i].equals("Chrome")) {
                chromeFound = true;
                chromeIndex = i;
                System.out.println("✓ Chrome found at index " + i);
                break;  // Found it - can stop searching
            }
        }

        System.out.println("---");
        if (chromeFound) {
            System.out.println("✓ Chrome is supported (position " + chromeIndex + ")");
        } else {
            System.out.println("✗ Chrome is not supported");
        }
    }
}
```

**Key Learning:**
- `break;` exits the entire loop immediately
- Only use `break;` when you want to stop looping (like finding what you're searching for)
- Don't put `break;` in else blocks when searching - it stops searching too early
- In test automation, this pattern is used for:
  - Searching for elements in lists
  - Finding specific test data
  - Checking if a value exists in a collection
- Wrong break placement = logic bugs that pass tests incorrectly

---

## 🎯 Advanced Debugging Challenge: Multiple Bugs!

**Buggy Code (Find all 4 bugs!):**
```java
public class DebugChallenge {
    public static void main(String[] args) {
        int[] testScores = {85, 92, 78, 95, 88};
        int passingScore = 80;
        int passCount = 0;
        int failCount = 0;

        System.out.println("===== Test Score Analysis =====");

        // Bug 1: Wrong loop condition
        for (int i = 1; i <= testScores.length; i++) {
            int score = testScores[i];

            // Bug 2: Wrong comparison operator
            if (score = passingScore) {
                passCount++;
                System.out.println("Test " + i + ": PASS (" + score + ")");
            } else {
                failCount++;
                System.out.println("Test " + i + ": FAIL (" + score + ")");
            }
        }

        // Bug 3: Integer division
        double passRate = (passCount / testScores.length) * 100;

        System.out.println("================================");
        System.out.println("Total Tests: " + testScores.length);
        System.out.println("Passed: " + passCount);
        System.out.println("Failed: " + failCount);
        System.out.println("Pass Rate: " + passRate + "%");

        // Bug 4: Wrong condition
        if (passRate > 80) {
            System.out.println("Status: EXCELLENT");
        } else if (passRate > 80) {
            System.out.println("Status: GOOD");
        } else {
            System.out.println("Status: NEEDS IMPROVEMENT");
        }
    }
}
```

**How many bugs can you find?** ___

Try to fix them all before looking at the solution!

---

**Solution:**

**Bugs Found: 4**

**Bug 1 - Line 10:** Loop starts at 1 instead of 0, and uses `<=` instead of `<`
- Arrays are 0-indexed
- Loop should be: `for (int i = 0; i < testScores.length; i++)`
- Current code: skips first element (index 0), tries to access invalid index 5

**Bug 2 - Line 14:** Uses `=` (assignment) instead of `==` (comparison)
- `if (score = passingScore)` assigns 80 to score, doesn't compare
- Should be: `if (score >= passingScore)`
- Note: Using `>=` not `==` because 80 and above should pass

**Bug 3 - Line 23:** Integer division loses decimal precision
- `(passCount / testScores.length)` uses integer division
- If passCount=4 and length=5: 4/5 = 0 (not 0.8!)
- Then 0 * 100 = 0 (should be 80!)
- Fix: `(passCount * 100.0) / testScores.length`

**Bug 4 - Line 30:** Second condition is unreachable
- First condition: `if (passRate > 80)`
- Second condition: `else if (passRate > 80)` - same as first!
- If passRate > 80, first block executes, second never reached
- Should be: `else if (passRate > 60)` or `else if (passRate >= 70)`

**Fixed Code:**
```java
public class DebugChallenge {
    public static void main(String[] args) {
        int[] testScores = {85, 92, 78, 95, 88};
        int passingScore = 80;
        int passCount = 0;
        int failCount = 0;

        System.out.println("===== Test Score Analysis =====");

        // Fixed: Start at 0, use <
        for (int i = 0; i < testScores.length; i++) {
            int score = testScores[i];

            // Fixed: Use >= for comparison
            if (score >= passingScore) {
                passCount++;
                System.out.println("Test " + (i + 1) + ": PASS (" + score + ")");
            } else {
                failCount++;
                System.out.println("Test " + (i + 1) + ": FAIL (" + score + ")");
            }
        }

        // Fixed: Multiply by 100.0 first for decimal precision
        double passRate = (passCount * 100.0) / testScores.length;

        System.out.println("================================");
        System.out.println("Total Tests: " + testScores.length);
        System.out.println("Passed: " + passCount);
        System.out.println("Failed: " + failCount);
        System.out.println("Pass Rate: " + String.format("%.2f%%", passRate));

        // Fixed: Different thresholds for each condition
        if (passRate >= 90) {
            System.out.println("Status: EXCELLENT");
        } else if (passRate >= 70) {
            System.out.println("Status: GOOD");
        } else {
            System.out.println("Status: NEEDS IMPROVEMENT");
        }
    }
}
```

---

## 💡 Debugging Checklist for Control Flow

When debugging loops and conditionals, check:

### For Loops:
- [ ] Correct initialization: `i = 0` for arrays
- [ ] Correct condition: `i < length` not `i <= length`
- [ ] Increment/decrement in right direction
- [ ] No infinite loops

### While Loops:
- [ ] Loop variable is modified inside the loop
- [ ] Condition can eventually become false
- [ ] Initialized before the loop
- [ ] No infinite loops

### If-Else:
- [ ] Using `==` not `=` for comparison
- [ ] Correct comparison operators (>, <, >=, <=, !=)
- [ ] Logical operators correct (&&, ||, !)
- [ ] All conditions are reachable
- [ ] No duplicate conditions

### Switch:
- [ ] Every case has `break;`
- [ ] Default case exists
- [ ] Correct case values
- [ ] No fall-through unless intentional

### Arrays:
- [ ] 0-indexed (first element at index 0)
- [ ] Last index is length - 1
- [ ] Loop doesn't exceed bounds
- [ ] Check for null/empty arrays

---

## 🏆 Debugging Confidence Check

**How many challenges did you solve independently?** ___/5 (___/4 for the bonus)

**Rating:**
- 5/5: Control flow master! 🏆
- 3-4/5: Strong debugging skills ✅
- 1-2/5: Review concept guide, try again 🔄
- 0/5: Keep practicing - debugging improves with experience! 💪

---

## 🎯 Key Takeaways

**Common Control Flow Bugs:**
1. **Missing break in switch** - causes fall-through
2. **Infinite loops** - forgot to update loop variable
3. **Off-by-one errors** - using `<=` instead of `<` for arrays
4. **Wrong operators** - using `=` instead of `==`
5. **Premature loop exit** - break in wrong place

**Prevention Strategies:**
- Use for loops when you know iteration count
- Always increment loop variables in while loops
- Remember: arrays use `i < length` not `i <= length`
- Double-check `=` vs `==` in conditions
- Only use break when you want to exit the loop

**Test Automation Impact:**
- Infinite loops freeze test suites
- Off-by-one errors skip test cases
- Wrong conditions cause false passes
- Missing breaks run wrong test configurations

---

## 🚀 Ready for the Project?

Move to **Day-2-Project-Guide.md** to build your Number Guessing Game!

**Debugging practice complete:** ✅
**Bugs squashed today:** ___ bugs
**Debugging confidence (1-10):** ____

---

## 📝 Bug Tracker (Keep a log!)

| Bug Type | What I Learned | How to Prevent |
|----------|----------------|----------------|
| Missing break | Causes switch fall-through | Always add break; |
| Infinite loop | Forgot to increment counter | Use for loops when possible |
| Off-by-one | Used <= instead of < | Arrays: i < length |
| = vs == | Assignment vs comparison | Read conditions twice |
| Premature break | Stopped searching too early | Only break when done |

**Most valuable lesson from today's debugging:**
_____________________________________

---
