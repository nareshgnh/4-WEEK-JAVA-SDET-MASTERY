# DAY 2: CONTROL FLOW

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master if-else conditional statements
- [ ] Understand and use switch statements
- [ ] Write for, while, and do-while loops
- [ ] Use break and continue keywords effectively
- [ ] Build a Number Guessing Game with loops and conditions

**Time Required:** 2.5-3 hours
**Difficulty:** Easy-Medium
**Prerequisite:** Day 1 (Variables, operators, data types)

---

## 📚 Core Concepts

### Concept 1: if-else Statements

**What is it?**
Control flow statements that execute different code blocks based on conditions. Think of it as decision-making in your code - "if this is true, do this; otherwise, do that."

**Why does it matter for automation?**
Test automation is full of conditional logic: "if element is present, click it; otherwise, fail the test." "If response code is 200, pass; otherwise, report error." Every test uses conditional logic.

**Syntax:**
```java
// Simple if
if (condition) {
    // code executes if condition is true
}

// if-else
if (condition) {
    // code if true
} else {
    // code if false
}

// if-else-if ladder
if (condition1) {
    // code if condition1 is true
} else if (condition2) {
    // code if condition2 is true
} else {
    // code if all conditions are false
}
```

**Explanation:**
- Condition must be a boolean expression (evaluates to true or false)
- Only one block executes - the first condition that's true
- `else` is optional - catches all other cases
- Curly braces `{}` define code blocks

**Real Example:**
```java
int testScore = 85;

if (testScore >= 90) {
    System.out.println("Grade: A - Excellent!");
} else if (testScore >= 80) {
    System.out.println("Grade: B - Good!");
} else if (testScore >= 70) {
    System.out.println("Grade: C - Average");
} else {
    System.out.println("Grade: F - Failed");
}
// Output: Grade: B - Good!
```

**Selenium Example:**
```java
// Check if login was successful
String currentUrl = driver.getCurrentUrl();

if (currentUrl.contains("dashboard")) {
    System.out.println("✓ Login successful");
} else if (currentUrl.contains("login")) {
    System.out.println("✗ Login failed - still on login page");
} else {
    System.out.println("! Unexpected page: " + currentUrl);
}
```

---

### Concept 2: Nested if Statements

**What is it?**
An if statement inside another if statement, used for complex decision-making with multiple conditions.

**Syntax:**
```java
if (outerCondition) {
    if (innerCondition) {
        // executes if both conditions are true
    } else {
        // executes if outer is true but inner is false
    }
} else {
    // executes if outer condition is false
}
```

**Real Example:**
```java
String browser = "chrome";
boolean isHeadless = true;

if (browser.equals("chrome")) {
    if (isHeadless) {
        System.out.println("Starting Chrome in headless mode");
    } else {
        System.out.println("Starting Chrome in normal mode");
    }
} else if (browser.equals("firefox")) {
    if (isHeadless) {
        System.out.println("Starting Firefox in headless mode");
    } else {
        System.out.println("Starting Firefox in normal mode");
    }
}
```

**Better Alternative (using logical operators):**
```java
if (browser.equals("chrome") && isHeadless) {
    System.out.println("Starting Chrome in headless mode");
} else if (browser.equals("chrome") && !isHeadless) {
    System.out.println("Starting Chrome in normal mode");
}
// More readable and concise
```

---

### Concept 3: switch Statement

**What is it?**
A cleaner alternative to multiple if-else-if statements when comparing one variable against multiple values.

**Why does it matter for automation?**
Use switch when you have many possible values for one variable (like browser type, test environment, test priority).

**Syntax:**
```java
switch (variable) {
    case value1:
        // code for value1
        break;
    case value2:
        // code for value2
        break;
    default:
        // code if no case matches
}
```

**Explanation:**
- `switch` evaluates the variable once
- `case` labels define possible values
- `break` exits the switch (without break, execution "falls through" to next case)
- `default` handles all other values (like else in if-else)

**Real Example:**
```java
String browserName = "chrome";

switch (browserName) {
    case "chrome":
        System.out.println("Launching Chrome browser");
        break;
    case "firefox":
        System.out.println("Launching Firefox browser");
        break;
    case "edge":
        System.out.println("Launching Edge browser");
        break;
    case "safari":
        System.out.println("Launching Safari browser");
        break;
    default:
        System.out.println("Unknown browser: " + browserName);
}
```

**Without break (fall-through):**
```java
int priority = 1;

switch (priority) {
    case 1:
    case 2:
    case 3:
        System.out.println("High priority test");
        break;
    case 4:
    case 5:
        System.out.println("Medium priority test");
        break;
    default:
        System.out.println("Low priority test");
}
// If priority is 1, 2, or 3, prints "High priority test"
```

---

### Concept 4: for Loop

**What is it?**
A loop that repeats code a specific number of times. Perfect when you know how many iterations you need.

**Why does it matter for automation?**
Run the same test multiple times, iterate through test data, process collections of web elements, retry failed operations.

**Syntax:**
```java
for (initialization; condition; increment/decrement) {
    // code to repeat
}
```

**Explanation:**
1. **Initialization:** Runs once at the start (e.g., `int i = 0`)
2. **Condition:** Checked before each iteration (e.g., `i < 10`)
3. **Increment/Decrement:** Runs after each iteration (e.g., `i++`)

**Real Example:**
```java
// Print numbers 1 to 5
for (int i = 1; i <= 5; i++) {
    System.out.println("Count: " + i);
}
/* Output:
Count: 1
Count: 2
Count: 3
Count: 4
Count: 5
*/
```

**Selenium Example:**
```java
// Click through pagination pages
for (int page = 1; page <= 5; page++) {
    System.out.println("Processing page " + page);
    driver.findElement(By.linkText("Page " + page)).click();
    // Process page content
}
```

**Common Patterns:**
```java
// Count up: 0 to 9
for (int i = 0; i < 10; i++) { }

// Count down: 10 to 1
for (int i = 10; i >= 1; i--) { }

// Step by 2: 0, 2, 4, 6, 8
for (int i = 0; i < 10; i += 2) { }

// Iterate through array (we'll learn arrays on Day 3)
for (int i = 0; i < array.length; i++) {
    System.out.println(array[i]);
}
```

---

### Concept 5: while Loop

**What is it?**
A loop that repeats code while a condition is true. Use when you don't know how many iterations you'll need.

**Why does it matter for automation?**
Wait for elements to appear, retry operations until success, process dynamic data of unknown size.

**Syntax:**
```java
while (condition) {
    // code to repeat while condition is true
}
```

**Explanation:**
- Condition is checked BEFORE each iteration
- If condition is false initially, the loop never runs
- Loop continues until condition becomes false

**Real Example:**
```java
int attempts = 0;
int maxAttempts = 5;
boolean success = false;

while (attempts < maxAttempts && !success) {
    System.out.println("Attempt " + (attempts + 1));
    // Try operation
    // success = tryOperation();
    attempts++;
}

if (success) {
    System.out.println("Operation succeeded!");
} else {
    System.out.println("Operation failed after " + maxAttempts + " attempts");
}
```

**Selenium Example:**
```java
// Wait for element to be clickable (simplified)
int waitTime = 0;
int maxWait = 10; // seconds

while (waitTime < maxWait) {
    if (driver.findElements(By.id("loginButton")).size() > 0) {
        driver.findElement(By.id("loginButton")).click();
        break; // Exit loop when element is found
    }
    Thread.sleep(1000); // Wait 1 second
    waitTime++;
}
```

**Infinite Loop Warning:**
```java
// DON'T DO THIS - infinite loop!
while (true) {
    System.out.println("This runs forever!");
    // No break or condition change - stuck forever
}

// FIX: Always have an exit condition
while (true) {
    System.out.println("This can exit");
    if (someCondition) {
        break; // Exit the loop
    }
}
```

---

### Concept 6: do-while Loop

**What is it?**
Similar to while loop, but condition is checked AFTER each iteration. Guarantees at least one execution.

**Syntax:**
```java
do {
    // code to execute
} while (condition);
```

**Explanation:**
- Code block executes at least once (even if condition is false)
- Condition checked after each iteration
- Use when you need to execute at least once (like showing a menu)

**Real Example:**
```java
int retryCount = 0;

do {
    System.out.println("Executing test (attempt " + (retryCount + 1) + ")");
    // Run test
    retryCount++;
} while (retryCount < 3);
// Runs at least once, up to 3 times
```

**while vs do-while:**
```java
// while loop - may not run at all
int x = 10;
while (x < 5) {
    System.out.println("This never prints");
}

// do-while loop - runs at least once
int y = 10;
do {
    System.out.println("This prints once");
} while (y < 5);
```

---

### Concept 7: break and continue

**What is it?**
Keywords to control loop execution.
- `break`: Immediately exits the loop
- `continue`: Skips the rest of current iteration, goes to next iteration

**Syntax:**
```java
// break example
for (int i = 1; i <= 10; i++) {
    if (i == 5) {
        break; // Exit loop when i is 5
    }
    System.out.println(i);
}
// Output: 1 2 3 4

// continue example
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue; // Skip 3
    }
    System.out.println(i);
}
// Output: 1 2 4 5 (skips 3)
```

**Selenium Example:**
```java
// Find and click the first visible button
for (int i = 0; i < buttons.size(); i++) {
    if (!buttons.get(i).isDisplayed()) {
        continue; // Skip hidden buttons
    }
    buttons.get(i).click();
    break; // Found and clicked, exit loop
}
```

---

## 🐍 Python vs Java Comparison

### if-else in Python (What You Know)
```python
score = 85

if score >= 90:
    print("Grade: A")
elif score >= 80:
    print("Grade: B")
else:
    print("Grade: C")

# No parentheses needed
# Indentation defines blocks
# elif keyword
```

### if-else in Java (What You're Learning)
```java
int score = 85;

if (score >= 90) {
    System.out.println("Grade: A");
} else if (score >= 80) {
    System.out.println("Grade: B");
} else {
    System.out.println("Grade: C");
}

// Parentheses required
// Curly braces define blocks
// else if (two words)
```

### Loops Comparison

| Feature | Python | Java |
|---------|--------|------|
| **for loop** | `for i in range(5):` | `for (int i = 0; i < 5; i++)` |
| **while loop** | `while condition:` | `while (condition) { }` |
| **break** | `break` | `break;` |
| **continue** | `continue` | `continue;` |
| **switch** | No switch (use dict) | `switch (var) { case x: }` |

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Using = Instead of == in Conditions
❌ **Wrong:**
```java
int x = 5;
if (x = 10) {  // Assignment, not comparison!
    System.out.println("This won't compile");
}
```

✅ **Correct:**
```java
int x = 5;
if (x == 10) {  // Comparison
    System.out.println("x equals 10");
}
```

**Why it matters:** `=` assigns a value, `==` compares values. Java won't compile this error (good!), but in some languages it would cause subtle bugs.

---

### Mistake 2: Missing break in switch
❌ **Wrong:**
```java
String browser = "chrome";

switch (browser) {
    case "chrome":
        System.out.println("Chrome");
        // Missing break - falls through!
    case "firefox":
        System.out.println("Firefox");
        break;
}
// Output: Chrome
//         Firefox (unintended!)
```

✅ **Correct:**
```java
String browser = "chrome";

switch (browser) {
    case "chrome":
        System.out.println("Chrome");
        break;  // Added break
    case "firefox":
        System.out.println("Firefox");
        break;
}
// Output: Chrome (only)
```

---

### Mistake 3: Off-by-One Errors in Loops
❌ **Wrong:**
```java
// Want to print 1 to 10
for (int i = 1; i < 10; i++) {  // Stops at 9!
    System.out.println(i);
}
```

✅ **Correct:**
```java
// Print 1 to 10
for (int i = 1; i <= 10; i++) {  // <= includes 10
    System.out.println(i);
}

// Or start from 0
for (int i = 0; i < 10; i++) {  // 0 to 9
    System.out.println(i + 1);   // Print 1 to 10
}
```

---

### Mistake 4: Infinite Loops
❌ **Wrong:**
```java
int count = 0;
while (count < 5) {
    System.out.println("Count: " + count);
    // Forgot to increment - runs forever!
}
```

✅ **Correct:**
```java
int count = 0;
while (count < 5) {
    System.out.println("Count: " + count);
    count++;  // Don't forget to increment!
}
```

---

### Mistake 5: Forgetting Parentheses in Conditions
❌ **Wrong:**
```java
if x > 5 {  // Missing parentheses
    System.out.println("Greater");
}
```

✅ **Correct:**
```java
if (x > 5) {  // Parentheses required in Java
    System.out.println("Greater");
}
```

---

## 💡 Pro Tips for Automation Engineers

**Tip 1:** Use descriptive conditions
```java
// Bad
if (x == 200) { }

// Good
boolean isSuccess = (responseCode == 200);
if (isSuccess) { }
```

**Tip 2:** Avoid magic numbers in conditions
```java
// Bad
if (retryCount < 3) { }

// Good
final int MAX_RETRIES = 3;
if (retryCount < MAX_RETRIES) { }
```

**Tip 3:** Keep conditions simple and readable
```java
// Hard to read
if (a && b || c && !d || e) { }

// Better - extract to variables
boolean firstCondition = a && b;
boolean secondCondition = c && !d;
if (firstCondition || secondCondition || e) { }
```

**Tip 4:** Prefer for loops for known iterations, while for unknown
```java
// Known count - use for
for (int i = 0; i < 10; i++) {
    // Run test 10 times
}

// Unknown count - use while
while (!elementFound && attempts < maxAttempts) {
    // Keep searching
}
```

---

## 🔍 Deep Dive: Short-Circuit Evaluation

**What experienced developers know:**

Java uses "short-circuit" evaluation for `&&` and `||` operators:

```java
// AND (&&): Stops at first false
if (expensiveCheck() && cheapCheck()) {
    // If expensiveCheck() is false, cheapCheck() never runs
}

// OR (||): Stops at first true
if (quickCheck() || slowCheck()) {
    // If quickCheck() is true, slowCheck() never runs
}
```

**Why it matters in Selenium:**
```java
// Prevent NullPointerException
if (driver != null && driver.getCurrentUrl().contains("login")) {
    // If driver is null, second condition never evaluates
    // No crash!
}

// Without short-circuit, this would crash
if (driver.getCurrentUrl().contains("login") && driver != null) {
    // CRASH! If driver is null, getCurrentUrl() throws exception
}
```

**Key Rule:** Put cheapest/safest checks first in && chains.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Conditional** | Code that executes based on a boolean condition | `if (x > 5) { }` |
| **Block** | Group of statements enclosed in `{}` | `{ statement1; statement2; }` |
| **Iteration** | One execution of a loop body | Loop runs 5 times = 5 iterations |
| **Break** | Exit a loop immediately | `if (found) break;` |
| **Continue** | Skip to next iteration | `if (skip) continue;` |
| **Fall-through** | In switch, execution continues to next case without break | Intentional or bug |
| **Short-circuit** | Boolean evaluation stops early when result is determined | `false && anything = false` |

---

## 🎓 Interview Prep

**Q1:** Explain the difference between if-else and switch statements. When would you use each?
**A:**
- **if-else:** Used for complex conditions, ranges, and boolean expressions
  ```java
  if (score >= 90) { }  // Can use ranges
  if (isLoggedIn && hasPermission) { }  // Complex conditions
  ```
- **switch:** Used when comparing one variable against multiple specific values
  ```java
  switch (browserName) {  // One variable, multiple exact values
      case "chrome": break;
      case "firefox": break;
  }
  ```
*"In test automation, I use if-else for assertion logic and complex conditions, while switch is perfect for configuration like browser selection or environment setup."*

---

**Q2:** What's the difference between `break` and `continue`?
**A:**
- **break:** Exits the entire loop immediately
- **continue:** Skips the rest of the current iteration, moves to next iteration

```java
for (int i = 1; i <= 5; i++) {
    if (i == 3) break;
    System.out.print(i + " ");
}
// Output: 1 2

for (int i = 1; i <= 5; i++) {
    if (i == 3) continue;
    System.out.print(i + " ");
}
// Output: 1 2 4 5
```

---

**Q3:** Explain short-circuit evaluation in Java.
**A:**
In logical AND (`&&`) and OR (`||`), Java stops evaluating as soon as the result is determined:
- `&&`: If first operand is false, second isn't checked (result is false anyway)
- `||`: If first operand is true, second isn't checked (result is true anyway)

*"This is critical in Selenium to avoid NullPointerExceptions. I always check if driver is initialized before calling its methods: `if (driver != null && driver.getTitle().equals(...))`"*

---

## ✅ Self-Check Questions

1. **What's the output?**
   ```java
   for (int i = 0; i < 3; i++) {
       if (i == 1) continue;
       System.out.print(i + " ");
   }
   ```

2. **Will this compile? If not, why?**
   ```java
   int x = 5;
   if (x = 10) {
       System.out.println("x is 10");
   }
   ```

3. **How many times does this loop run?**
   ```java
   int i = 0;
   while (i < 5) {
       System.out.println(i);
       i++;
   }
   ```

4. **What happens if you forget `break` in a switch case?**

5. **What's the difference between `while` and `do-while` loops?**

**Answers in Practice Exercises file.**

---

## 🚀 Ready for Practice?

Move to **Day-2-Practice-Exercises.md** to build your control flow skills!

**Concepts understood:** ___/7
**Ready to code:** Yes / Review needed

---
