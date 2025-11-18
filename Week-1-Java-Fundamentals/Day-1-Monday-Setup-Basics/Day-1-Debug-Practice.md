# DAY 1 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As an SDET, you'll spend 30-40% of your time debugging test failures, flaky tests, and framework issues. Learning to quickly identify and fix bugs is as important as writing code.

**Skills You'll Build:**
- Reading and understanding compiler error messages
- Identifying syntax errors
- Recognizing logical errors
- Developing a debugging mindset

**Pro Tip:** In Java, many errors are caught at compile-time (before running), unlike Python where many errors only appear at runtime. This is actually an advantage!

---

## Challenge 1: Missing Semicolon

**Broken Code:**
```java
public class Debug1 {
    public static void main(String[] args) {
        String browser = "Chrome"
        System.out.println("Testing with: " + browser);
    }
}
```

**Error Message:**
```
java: ';' expected
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Look carefully at each line - what's missing?
- Remember: Java requires something at the end of each statement

---

**Solution:**

**Bug Location:** Line 3 - missing semicolon after `"Chrome"`

**Explanation:**
Every statement in Java must end with a semicolon (`;`). This is different from Python where line breaks indicate statement ends. The Java compiler expects a semicolon after string assignment and stops compilation when it doesn't find one.

**Fixed Code:**
```java
public class Debug1 {
    public static void main(String[] args) {
        String browser = "Chrome";  // Added semicolon
        System.out.println("Testing with: " + browser);
    }
}
```

**Key Learning:**
- Semicolons are mandatory in Java (most common beginner error!)
- Compiler error messages are very helpful - "';' expected" tells you exactly what's missing
- Get in the habit: type `;` after every statement automatically
- IntelliJ IDEA will show red underlines for missing semicolons

---

## Challenge 2: Type Mismatch

**Broken Code:**
```java
public class Debug2 {
    public static void main(String[] args) {
        int testCount = "50";
        System.out.println("Running " + testCount + " tests");
    }
}
```

**Error Message:**
```
java: incompatible types: java.lang.String cannot be converted to int
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Look at the data type and the value being assigned
- What type is "50" (with quotes)?

---

**Solution:**

**Bug Location:** Line 3 - assigning string to int variable

**Explanation:**
`"50"` (with quotes) is a String, not a number. Java is statically typed, meaning once you declare a variable as `int`, you can only assign integer values to it, not strings. The compiler catches this mismatch and prevents you from running the code.

**Fixed Code:**
```java
public class Debug2 {
    public static void main(String[] args) {
        int testCount = 50;  // Removed quotes - now it's an integer
        System.out.println("Running " + testCount + " tests");
    }
}
```

**Alternative Fix (if you want to keep the string):**
```java
public class Debug2 {
    public static void main(String[] args) {
        String testCount = "50";  // Changed type to String
        System.out.println("Running " + testCount + " tests");
    }
}
```

**Key Learning:**
- Quotes make something a String: `"50"` is text, `50` is a number
- Java's type system catches these errors at compile-time (before running)
- This prevents many runtime bugs that Python developers encounter
- In test automation, this helps catch configuration errors early

---

## Challenge 3: Variable Not Declared

**Broken Code:**
```java
public class Debug3 {
    public static void main(String[] args) {
        System.out.println("Base URL: " + baseUrl);
    }
}
```

**Error Message:**
```
java: cannot find symbol
  symbol:   variable baseUrl
  location: class Debug3
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Is `baseUrl` defined anywhere in the code?
- What must you do before using a variable in Java?

---

**Solution:**

**Bug Location:** Line 3 - using undeclared variable

**Explanation:**
In Java, you must declare a variable before using it. The variable `baseUrl` is referenced in the println statement, but it was never declared or initialized. The compiler doesn't know what `baseUrl` is, so it gives a "cannot find symbol" error.

**Fixed Code:**
```java
public class Debug3 {
    public static void main(String[] args) {
        String baseUrl = "https://www.saucedemo.com";  // Declare and initialize
        System.out.println("Base URL: " + baseUrl);
    }
}
```

**Key Learning:**
- Always declare variables before use: `Type variableName = value;`
- "Cannot find symbol" means Java doesn't recognize the variable name
- Could be: variable not declared, misspelled name, or wrong scope
- In test automation, this error often appears when you forget to initialize WebDriver or page objects

---

## Challenge 4: Integer Division Surprise

**Code:**
```java
public class Debug4 {
    public static void main(String[] args) {
        int totalTests = 10;
        int passedTests = 7;

        double passRate = (passedTests / totalTests) * 100;

        System.out.println("Pass Rate: " + passRate + "%");
    }
}
```

**Expected Output:** `Pass Rate: 70.0%`
**Actual Output:** `Pass Rate: 0.0%`

**Your Task:**
This code compiles and runs without errors, but produces wrong output. Find and fix the logic bug.

**Hints:**
- When you divide two integers, what type of division does Java perform?
- The result is assigned to a double, but the division happens first...

---

**Solution:**

**Bug Location:** Line 6 - integer division before multiplication

**Explanation:**
This is a **logic bug** (not a syntax error). Here's what happens:
1. `passedTests / totalTests` → `7 / 10` → integer division → `0` (not 0.7!)
2. `0 * 100` → `0`
3. `0` is then assigned to double `passRate`, becoming `0.0`

Java performs integer division when both operands are integers, even if the result is stored in a double. The division happens first, truncating to 0, then it's multiplied and converted to double.

**Fixed Code:**
```java
public class Debug4 {
    public static void main(String[] args) {
        int totalTests = 10;
        int passedTests = 7;

        // Cast at least one operand to double for decimal division
        double passRate = (passedTests * 100.0) / totalTests;
        // or: double passRate = ((double) passedTests / totalTests) * 100;

        System.out.println("Pass Rate: " + passRate + "%");  // Now: 70.0%
    }
}
```

**Key Learning:**
- **Critical for test metrics calculations!**
- Integer division: `7 / 10 = 0` (truncates decimal)
- Double division: `7.0 / 10 = 0.7` (preserves decimal)
- Always use `.0` or cast to double when you need decimal results
- In test reporting, this bug could make a 90% pass rate show as 0%!

---

## Challenge 5: String Comparison Bug

**Code:**
```java
public class Debug5 {
    public static void main(String[] args) {
        String expectedBrowser = "Chrome";
        String actualBrowser = "Chrome";

        if (expectedBrowser == actualBrowser) {
            System.out.println("✓ Browser match!");
        } else {
            System.out.println("✗ Browser mismatch!");
        }
    }
}
```

**Current Output:** `✓ Browser match!`

**But when you change it to:**
```java
String expectedBrowser = new String("Chrome");
String actualBrowser = new String("Chrome");
```

**Output becomes:** `✗ Browser mismatch!`

**Your Task:**
Explain why the output changes and fix it so strings are compared correctly in both scenarios.

**Hints:**
- `==` compares object references (memory addresses), not content
- Strings have a special method for content comparison

---

**Solution:**

**Bug Location:** Line 6 - using `==` to compare strings

**Explanation:**
`==` operator compares memory addresses (object references), not the actual string content.

**Why it works sometimes:**
- String literals like `"Chrome"` are stored in a "string pool" - Java reuses the same memory location
- So `"Chrome" == "Chrome"` returns true (same memory address)

**Why it fails other times:**
- `new String("Chrome")` creates a new object in different memory location
- Even though content is same, memory addresses differ
- So `new String("Chrome") == new String("Chrome")` returns false

**In real test automation:**
- Strings often come from different sources (web elements, databases, API responses)
- They won't share the same memory location
- Using `==` would fail even when strings match!

**Fixed Code:**
```java
public class Debug5 {
    public static void main(String[] args) {
        String expectedBrowser = "Chrome";
        String actualBrowser = "Chrome";

        if (expectedBrowser.equals(actualBrowser)) {  // Use .equals() method
            System.out.println("✓ Browser match!");
        } else {
            System.out.println("✗ Browser mismatch!");
        }
    }
}
```

**For case-insensitive comparison:**
```java
if (expectedBrowser.equalsIgnoreCase(actualBrowser)) {
    System.out.println("✓ Browser match!");
}
// "Chrome" equals "chrome" equals "CHROME"
```

**Key Learning:**
- **CRITICAL FOR TEST ASSERTIONS!**
- `==` → compares references (memory addresses)
- `.equals()` → compares actual content
- `.equalsIgnoreCase()` → compares content ignoring case
- In Selenium, when comparing text from web elements, ALWAYS use `.equals()`
- Using `==` for strings is one of the most common bugs in test automation

---

## 🎯 Debugging Skills Checkpoint

After completing these challenges, you should be able to:

- [ ] Recognize and fix syntax errors (missing semicolons, braces)
- [ ] Identify type mismatch errors
- [ ] Understand "cannot find symbol" errors
- [ ] Catch integer division bugs before they cause wrong calculations
- [ ] Always use `.equals()` for string comparison

---

## 💡 Debugging Tips for SDETs

### Tip 1: Read Error Messages Carefully
```
java: cannot find symbol
  symbol:   variable userName
  location: class LoginTest
```
This tells you:
- WHAT: "cannot find symbol"
- WHICH: "variable userName"
- WHERE: "class LoginTest"

### Tip 2: Common Error Patterns

| Error Message | Usually Means | Fix |
|---------------|---------------|-----|
| `';' expected` | Missing semicolon | Add `;` at end of line |
| `cannot find symbol` | Variable not declared or typo | Declare variable or check spelling |
| `incompatible types` | Type mismatch | Change value or variable type |
| `class X is public, should be declared in a file named X.java` | Filename doesn't match class name | Rename file |
| `variable X might not have been initialized` | Variable declared but not assigned | Initialize with a value |

### Tip 3: IntelliJ IDEA Helpers
- **Red underline** → Syntax error (fix before running)
- **Yellow underline** → Warning (code works but could be better)
- **Hover over error** → See explanation
- **Alt + Enter** → See quick fixes

### Tip 4: Debugging Workflow
1. Read the error message completely
2. Note the line number
3. Understand what the error means
4. Fix the root cause (not just symptoms)
5. Recompile and verify fix

---

## 🧪 Extra Challenge: Find All Bugs!

**Buggy Code (Multiple Errors):**
```java
public class TestRunner
    public static void main(String[] args) {
        int totalTests = 20
        int passedTests = 18;

        String testSuite = "Regression Suite"

        double passPercentage = (passedTests / totalTests) * 100;

        System.out.println(TestSuite + ": " + passPercentage + "%");
    }
}
```

**How many bugs can you find?** ___

Try to fix them all before looking at the solution below!

---

**Solution:**

**Bugs Found: 5**

1. **Line 1:** Missing opening brace `{` after class declaration
2. **Line 3:** Missing semicolon after `int totalTests = 20`
3. **Line 6:** Missing semicolon after string assignment
4. **Line 8:** Integer division bug - should use `100.0` or cast to double
5. **Line 10:** Variable name case mismatch - `TestSuite` should be `testSuite` (Java is case-sensitive)

**Fixed Code:**
```java
public class TestRunner {  // Added {
    public static void main(String[] args) {
        int totalTests = 20;  // Added ;
        int passedTests = 18;

        String testSuite = "Regression Suite";  // Added ;

        double passPercentage = (passedTests * 100.0) / totalTests;  // Fixed division

        System.out.println(testSuite + ": " + passPercentage + "%");  // Fixed case
    }
}
```

---

## 🏆 Debugging Confidence Check

**How many challenges did you solve independently?** ___/5

**Rating:**
- 5/5: Debugging master! 🏆
- 3-4/5: Strong debugging skills ✅
- 1-2/5: Review concept guide, try again 🔄
- 0/5: That's okay - debugging comes with practice! 💪

---

## 🎯 Key Takeaways

1. **Compiler errors are your friends** - they prevent runtime bugs
2. **Read error messages carefully** - they tell you exactly what's wrong
3. **Common Day 1 errors:**
   - Missing semicolons
   - Type mismatches
   - Undeclared variables
   - Integer division when you need decimals
   - Using `==` instead of `.equals()` for strings

4. **Test automation specific:**
   - Integer division bugs affect test metrics
   - String comparison with `==` causes assertion failures
   - Type safety prevents configuration errors

---

## 🚀 Ready for the Project?

Move to **Day-1-Project-Guide.md** to build your Calculator mini-project!

**Debugging practice complete:** ✅
**Bugs squashed today:** ___ bugs
**Debugging confidence (1-10):** ____

---

## 📝 Debugging Log (Optional but Recommended)

Track bugs you encounter while coding:

| Date | Bug Description | Error Message | Solution | Time to Fix |
|------|-----------------|---------------|----------|-------------|
| | | | | |
| | | | | |

**Why track bugs?**
- Learn from patterns
- Get faster at fixing similar issues
- Great talking point in interviews: "I track and learn from my errors"

---
