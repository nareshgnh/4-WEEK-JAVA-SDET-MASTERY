# DAY 2 CHEAT SHEET: CONTROL FLOW

## ⚡ Quick Syntax Reference

### If-Else Statement
```java
if (condition) {
    // Execute if condition is true
} else if (anotherCondition) {
    // Execute if first is false and this is true
} else {
    // Execute if all above are false
}

// Example
if (testStatus == 0) {
    System.out.println("PASSED");
} else if (testStatus == 1) {
    System.out.println("FAILED");
} else {
    System.out.println("SKIPPED");
}
```

### Switch Statement
```java
switch (variable) {
    case value1:
        // Code for value1
        break;
    case value2:
        // Code for value2
        break;
    default:
        // Code if no case matches
        break;
}

// Example
switch (browser) {
    case "chrome":
        System.out.println("Using Chrome");
        break;
    case "firefox":
        System.out.println("Using Firefox");
        break;
    default:
        System.out.println("Unknown browser");
        break;
}
```

### For Loop
```java
// Syntax: for (initialization; condition; increment)
for (int i = 0; i < 10; i++) {
    // Execute 10 times (i = 0 to 9)
}

// Example: Loop through array
String[] tests = {"Login", "Search", "Checkout"};
for (int i = 0; i < tests.length; i++) {
    System.out.println("Running: " + tests[i]);
}
```

### While Loop
```java
while (condition) {
    // Execute while condition is true
    // MUST update condition inside loop
}

// Example
int attempts = 0;
while (attempts < 5) {
    System.out.println("Attempt: " + attempts);
    attempts++;  // MUST increment!
}
```

### Do-While Loop
```java
do {
    // Execute at least once
    // Then check condition
} while (condition);

// Example
int count = 0;
do {
    System.out.println("Count: " + count);
    count++;
} while (count < 5);
```

### Enhanced For Loop (For-Each)
```java
for (Type item : collection) {
    // Execute for each item
}

// Example
String[] browsers = {"Chrome", "Firefox", "Safari"};
for (String browser : browsers) {
    System.out.println(browser);
}
```

### Loop Control
```java
break;     // Exit loop immediately
continue;  // Skip to next iteration

// Example
for (int i = 0; i < 10; i++) {
    if (i == 5) break;      // Stop loop at 5
    if (i % 2 == 0) continue;  // Skip even numbers
    System.out.println(i);  // Prints: 1, 3
}
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Syntax | Python Equivalent |
|---------|-------------|-------------------|
| **If** | `if (x > 5) { }` | `if x > 5:` |
| **Else If** | `else if (x == 5) { }` | `elif x == 5:` |
| **Else** | `else { }` | `else:` |
| **Switch** | `switch (x) { case 1: break; }` | `match x: case 1:` (3.10+) |
| **For Loop** | `for (int i=0; i<10; i++)` | `for i in range(10):` |
| **While** | `while (x < 10) { }` | `while x < 10:` |
| **Do-While** | `do { } while (x < 10);` | No direct equivalent |
| **For-Each** | `for (String s : arr) { }` | `for s in arr:` |
| **And** | `if (a && b) { }` | `if a and b:` |
| **Or** | `if (a \|\| b) { }` | `if a or b:` |
| **Not** | `if (!a) { }` | `if not a:` |
| **Break** | `break;` | `break` |
| **Continue** | `continue;` | `continue` |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Missing braces** | `if (x > 5) System.out.println("hi");` next line not in if! | `if (x > 5) { System.out.println("hi"); }` |
| **Assignment vs comparison** | `if (x = 5)` | `if (x == 5)` |
| **Missing break** | `case "a": doX();` (falls through!) | `case "a": doX(); break;` |
| **Infinite loop** | `while (i < 10) { }` (i never changes) | `while (i < 10) { i++; }` |
| **Off-by-one** | `for (int i=0; i<=arr.length; i++)` | `for (int i=0; i<arr.length; i++)` |
| **Semicolon after if** | `if (x > 5);` (empty statement!) | `if (x > 5) { code; }` |
| **Wrong for syntax** | `for (i = 0, i < 10, i++)` | `for (int i=0; i<10; i++)` |

---

## 💡 Control Flow Patterns for Test Automation

### Pattern 1: Retry Logic
```java
int maxRetries = 3;
int retryCount = 0;
boolean success = false;

while (retryCount < maxRetries && !success) {
    retryCount++;
    try {
        // Attempt test action
        success = true;
    } catch (Exception e) {
        System.out.println("Attempt " + retryCount + " failed");
    }
}
```

### Pattern 2: Environment Configuration
```java
String env = "qa";
String baseUrl;

switch (env) {
    case "dev":
        baseUrl = "http://localhost:3000";
        break;
    case "qa":
        baseUrl = "https://qa.example.com";
        break;
    case "prod":
        baseUrl = "https://www.example.com";
        break;
    default:
        throw new IllegalArgumentException("Unknown env: " + env);
}
```

### Pattern 3: Test Data Iteration
```java
String[] testUsers = {"user1", "user2", "admin"};

for (int i = 0; i < testUsers.length; i++) {
    String user = testUsers[i];
    System.out.println("Testing with user: " + user);
    // Run test with this user
}
```

### Pattern 4: Wait for Element
```java
int maxWait = 10;
int elapsed = 0;
boolean found = false;

while (elapsed < maxWait && !found) {
    if (elementExists()) {
        found = true;
    } else {
        Thread.sleep(1000);
        elapsed++;
    }
}

if (!found) {
    throw new TimeoutException("Element not found");
}
```

### Pattern 5: Test Status Validation
```java
int passRate = 85;
String status;

if (passRate >= 90) {
    status = "EXCELLENT";
} else if (passRate >= 75) {
    status = "GOOD";
} else if (passRate >= 60) {
    status = "ACCEPTABLE";
} else {
    status = "FAILED";
}
```

---

## 📊 Loop Comparison Table

| Feature | For Loop | While Loop | Do-While | For-Each |
|---------|----------|------------|----------|----------|
| **Best for** | Known iterations | Unknown iterations | At least 1 execution | Iterating collections |
| **Counter** | Built-in | Manual | Manual | Automatic |
| **Syntax** | Complex | Simple | Simple | Simplest |
| **Index access** | Yes | Manual | Manual | No |
| **Empty condition** | Skips all | Skips all | Runs once | Skips all |
| **Modify collection** | Yes | Yes | Yes | No (read-only) |

**When to use which:**
- **For:** "Run this 10 times" or "Loop through array indices"
- **While:** "Keep trying until success" or "Read until EOF"
- **Do-While:** "Execute, then check if repeat needed" (rare)
- **For-Each:** "Process each item in this collection"

---

## 🎯 Decision Flow Chart

```
           ┌─────────────┐
           │  Condition  │
           └──────┬──────┘
                  │
         ┌────────┴────────┐
         ▼                 ▼
     ┌───────┐         ┌───────┐
     │ TRUE  │         │ FALSE │
     └───┬───┘         └───┬───┘
         │                 │
         ▼                 ▼
    ┌─────────┐      ┌─────────┐
    │ If Block│      │Else Block│
    └─────────┘      └─────────┘
```

**Multiple conditions:**
```
if (condition1) {
    // Path A
} else if (condition2) {
    // Path B
} else if (condition3) {
    // Path C
} else {
    // Path D (default)
}
```

Only ONE path executes. First true condition wins!

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ If-else for decision making
- ✅ Switch for multiple specific values
- ✅ For loops for known iterations
- ✅ While loops for conditional repetition
- ✅ Do-while for at-least-once execution
- ✅ Break and continue for loop control
- ✅ Boolean flags for state tracking
- ✅ Compound conditions with &&, ||, !

**Critical for Automation:**
- 🎯 If-else for test result validation
- 🎯 Switch for environment/browser selection
- 🎯 For loops for data-driven testing
- 🎯 While loops for element waiting
- 🎯 Break for early exit on failure
- 🎯 Flags for test status tracking

---

## 🎤 Interview Phrases

**When asked about control flow, say:**

**On If-Else:**
*"If-else statements enable conditional execution in Java. They're essential in test automation for validating results, handling different test scenarios, and making decisions based on runtime conditions. For example, I use if-else to check if a test passed or failed and take appropriate actions like capturing screenshots on failure."*

**On Loops:**
*"Java provides several loop structures. I use for loops when I know the iteration count, like processing a fixed set of test data. While loops are better for conditional scenarios like waiting for an element to appear with a timeout. In Selenium, I commonly use for-each loops to iterate through collections of WebElements."*

**On Switch:**
*"Switch statements provide clean multi-way branching when checking a single variable against multiple values. In test frameworks, I use switch for environment configuration selection, browser type handling, and test priority routing. It's more readable than chained if-else when you have many specific cases."*

**On Break/Continue:**
*"Break exits a loop immediately, which I use when finding what I'm searching for in a collection - no need to continue. Continue skips the current iteration and moves to the next, useful for filtering or skipping invalid test data. Both improve efficiency by avoiding unnecessary iterations."*

**On Loop Choice:**
*"The choice between for and while depends on whether iteration count is known. For data-driven tests with a known dataset, I use for loops. For polling scenarios like waiting for page load or AJAX completion with a timeout, while loops are more appropriate as we don't know how many checks we'll need."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **Control flow is decision-making power. If-else asks "Should I?", loops ask "How many times?", and break/continue ask "Should I stop or skip?" Master these, and your code can adapt to any situation - the foundation of intelligent test automation.**

---

## 🔍 Syntax Deep Dive

### For Loop Anatomy
```java
for (initialization; condition; increment) {
    // Loop body
}

     ↓              ↓           ↓
   Start        Continue?    After each
   value        Yes/No       iteration
```

**Example breakdown:**
```java
for (int i = 0; i < 5; i++) {
    System.out.println(i);
}

// Step by step:
// 1. int i = 0      → i is now 0
// 2. i < 5?         → 0 < 5? YES → enter loop
// 3. Print 0
// 4. i++            → i is now 1
// 5. i < 5?         → 1 < 5? YES → enter loop
// 6. Print 1
// 7. i++            → i is now 2
// ... continues until i = 5
// 8. i < 5?         → 5 < 5? NO → exit loop
```

### Switch Fall-Through
```java
int day = 1;
switch (day) {
    case 1:
        System.out.println("Monday");
        // NO BREAK - execution continues!
    case 2:
        System.out.println("Tuesday");
        break;
}
// Output: Monday
//         Tuesday
// (Both print because case 1 has no break!)
```

**Intentional fall-through (rare but valid):**
```java
switch (priority) {
    case "critical":
    case "high":
        runImportantTests();
        break;
    case "medium":
    case "low":
        runNormalTests();
        break;
}
// Both "critical" and "high" run the same code
```

---

## 🐛 Debugging Quick Reference

**Problem: Loop runs forever**
- Check: Is loop variable modified inside loop?
- Check: Can condition ever become false?
- Fix: Add increment/decrement or break condition

**Problem: Loop skips elements**
- Check: Starting from 0 for arrays?
- Check: Using `<` not `<=` for length?
- Fix: `for (int i = 0; i < arr.length; i++)`

**Problem: Switch does unexpected things**
- Check: Break after each case?
- Check: Default case exists?
- Fix: Add `break;` at end of each case

**Problem: Condition never true**
- Check: Using `==` not `=`?
- Check: Correct comparison operator?
- Check: Variable value is what you expect?
- Fix: Print variable value before condition

**Problem: Code after if doesn't run**
- Check: Is there a semicolon after if?
- Fix: Remove semicolon, add braces

---

## 📋 Control Flow Decision Guide

**Choose If-Else when:**
- ✅ Checking true/false conditions
- ✅ Comparing ranges (x > 10)
- ✅ Complex conditions (a && b || c)
- ✅ Different types of checks

**Choose Switch when:**
- ✅ Checking ONE variable against many specific values
- ✅ All cases are equality checks
- ✅ More than 3-4 conditions
- ✅ Cleaner than long if-else chain

**Choose For Loop when:**
- ✅ You know iteration count
- ✅ Working with array indices
- ✅ Counting (1 to 10)
- ✅ Need loop counter variable

**Choose While Loop when:**
- ✅ Unknown iteration count
- ✅ Conditional repetition
- ✅ "Do until success" pattern
- ✅ Polling/waiting scenarios

**Choose Do-While when:**
- ✅ Must execute at least once
- ✅ Validate after action
- ✅ Menu systems (rare in tests)

**Choose For-Each when:**
- ✅ Just need each item
- ✅ Don't need index
- ✅ Read-only iteration
- ✅ Simplest code

---

## 🔮 Tomorrow's Preview

**Day 3 Topic:** Arrays & Strings

**What to review tonight:**
- For loops (you'll use them to iterate arrays)
- For-each loops (perfect for collections)
- String concatenation with +

**What to think about:**
*"How would I store multiple test results in one variable? How would I check if two strings have the same letters (anagram)?"*

**Connection to Day 2:**
- For loops → Iterate through arrays
- If-else → Compare array elements
- While loops → Search through arrays
- Today's loops are tomorrow's array tools!

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [x] Learned control flow structures
- [x] Completed 6 practice exercises
- [x] Built Number Guessing Game
- [x] ~2.5 hours of Java practice

**Cumulative Progress:**
- Days completed: 2/7
- Java concepts mastered: 16
- Lines of code written: ~150-250

**Momentum Statement:**
*"Day 2 complete! Your code can now make decisions and repeat actions. You've added intelligence to your programs. With variables (Day 1) and control flow (Day 2), you can build almost anything. Tomorrow you'll learn to handle collections of data. You're 28% of the way to Java SDET mastery!"*

---

## 📝 Quick Reference Card (Print This!)

```
╔═══════════════════════════════════════════════╗
║       CONTROL FLOW - DAY 2 REFERENCE          ║
╠═══════════════════════════════════════════════╣
║ IF-ELSE:                                      ║
║ if (condition) {                              ║
║     // true path                              ║
║ } else {                                      ║
║     // false path                             ║
║ }                                             ║
╠═══════════════════════════════════════════════╣
║ SWITCH:                                       ║
║ switch (var) {                                ║
║     case 1: code; break;                      ║
║     default: code; break;                     ║
║ }                                             ║
╠═══════════════════════════════════════════════╣
║ FOR LOOP:                                     ║
║ for (int i=0; i<10; i++) {                    ║
║     // runs 10 times                          ║
║ }                                             ║
╠═══════════════════════════════════════════════╣
║ WHILE LOOP:                                   ║
║ while (condition) {                           ║
║     // update condition!                      ║
║ }                                             ║
╠═══════════════════════════════════════════════╣
║ FOR-EACH:                                     ║
║ for (String s : array) {                      ║
║     // process s                              ║
║ }                                             ║
╠═══════════════════════════════════════════════╣
║ LOOP CONTROL:                                 ║
║ break;      // exit loop                      ║
║ continue;   // skip to next iteration         ║
╠═══════════════════════════════════════════════╣
║ CRITICAL RULES:                               ║
║ • == compares, = assigns                      ║
║ • Arrays: i < length (not <=)                 ║
║ • Switch: always add break;                   ║
║ • While: must update condition                ║
║ • For array: start at 0                       ║
╠═══════════════════════════════════════════════╣
║ OPERATORS:                                    ║
║ &&  AND (both must be true)                   ║
║ ||  OR (at least one true)                    ║
║ !   NOT (flips true/false)                    ║
║ ==  equals                                    ║
║ !=  not equals                                ║
║ <   less than                                 ║
║ >   greater than                              ║
║ <=  less or equal                             ║
║ >=  greater or equal                          ║
╚═══════════════════════════════════════════════╝
```

---

## 🚀 You're Ready for Day 3!

**Prerequisites Checklist:**
- [x] Understand if-else statements
- [x] Know how to write for loops
- [x] Comfortable with while loops
- [x] Can use break and continue
- [x] Understand boolean logic (&&, ||, !)
- [x] Completed Number Guessing Game

**Confidence Check:**
- If-else: ___/10
- Loops: ___/10
- Switch: ___/10
- Overall readiness: ___/10

**If any rating is below 6:** Review the Concept Guide and practice exercises again before moving on.

**If all ratings are 6+:** You're ready! Let's learn arrays and strings tomorrow! 🎉

---

## 💾 Save This Cheat Sheet

**Ways to use this:**
1. **Print it** - Reference while coding Day 3 exercises
2. **Review it** - Before technical interviews
3. **Compare** - See how it differs from Python
4. **Test yourself** - Cover solutions, try to recall syntax

**Pro Tip:** When you can write a for loop without looking at syntax, you know you've internalized it!

---

**END OF DAY 2** ✅

Tomorrow: Day 3 - Arrays & Strings (Anagram Checker)

**Control flow mastered!** Now you can make your code think and repeat. 🧠🔁

---
