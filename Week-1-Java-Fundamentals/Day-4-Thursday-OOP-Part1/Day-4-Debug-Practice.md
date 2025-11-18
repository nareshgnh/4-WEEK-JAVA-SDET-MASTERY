# DAY 4 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As a Java SDET, you'll spend significant time debugging test failures, framework issues, and application bugs. OOP-related errors are among the most common in Selenium automation:
- Null pointer exceptions from uninitialized objects
- Constructor errors in Page Objects
- Encapsulation violations
- Method signature mismatches

These challenges will sharpen your debugging skills with real-world OOP scenarios.

---

## Challenge 1: Constructor Name Mismatch

**Broken Code:**
```java
public class LoginPage {
    String url;
    String title;

    // Constructor
    public loginPage(String url, String title) {
        this.url = url;
        this.title = title;
    }

    public void displayInfo() {
        System.out.println("URL: " + url);
        System.out.println("Title: " + title);
    }
}

public class Test {
    public static void main(String[] args) {
        LoginPage page = new LoginPage("https://example.com", "Login");
        page.displayInfo();
    }
}
```

**Error Message:**
```
Error: constructor LoginPage in class LoginPage cannot be applied to given types;
  required: no arguments
  found: String,String
  reason: actual and formal argument lists differ in length
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Look carefully at the constructor name
- Java is case-sensitive
- Constructor name must match class name exactly

---

**Solution:**

**Bug Location:** Line 5 - Constructor name is `loginPage` (lowercase 'l') but class name is `LoginPage` (uppercase 'L')

**Explanation:**
In Java, constructor names must match the class name exactly (case-sensitive). When the name doesn't match, Java treats it as a regular method with no return type, which is also an error. Since there's no valid constructor, Java looks for the default no-argument constructor, but you're passing two arguments.

**Fixed Code:**
```java
public class LoginPage {
    String url;
    String title;

    // Constructor - name matches class name
    public LoginPage(String url, String title) {  // Changed from 'loginPage' to 'LoginPage'
        this.url = url;
        this.title = title;
    }

    public void displayInfo() {
        System.out.println("URL: " + url);
        System.out.println("Title: " + title);
    }
}

public class Test {
    public static void main(String[] args) {
        LoginPage page = new LoginPage("https://example.com", "Login");
        page.displayInfo();
    }
}
```

**Key Learning:**
- Constructor name must exactly match class name (case-sensitive)
- This is a common error when renaming classes
- Always use IDE's refactor feature when renaming classes to avoid this

---

## Challenge 2: Missing `this` Keyword

**Broken Code:**
```java
public class TestData {
    String username;
    String password;
    String environment;

    public TestData(String username, String password, String environment) {
        username = username;
        password = password;
        environment = environment;
    }

    public void displayData() {
        System.out.println("Username: " + username);
        System.out.println("Password: " + password);
        System.out.println("Environment: " + environment);
    }
}

public class Test {
    public static void main(String[] args) {
        TestData data = new TestData("testuser", "pass123", "QA");
        data.displayData();
    }
}
```

**Expected Output:**
```
Username: testuser
Password: pass123
Environment: QA
```

**Actual Output:**
```
Username: null
Password: null
Environment: null
```

**Your Task:** Find and fix the logic error (no compilation error, but wrong output)

**Hints:**
- Code compiles but doesn't work as expected
- Look at the constructor assignments
- Which variable is being assigned to which?

---

**Solution:**

**Bug Location:** Lines 6-8 - Constructor assignments without `this` keyword

**Explanation:**
Without the `this` keyword, `username = username` assigns the parameter to itself, not to the instance variable. All three assignments are parameter-to-parameter, leaving instance variables at their default value (null for String objects). This is a classic shadowing problem where parameter names hide instance variable names.

**Fixed Code:**
```java
public class TestData {
    String username;
    String password;
    String environment;

    public TestData(String username, String password, String environment) {
        this.username = username;      // this.username (instance) = username (parameter)
        this.password = password;      // this.password (instance) = password (parameter)
        this.environment = environment; // this.environment (instance) = environment (parameter)
    }

    public void displayData() {
        System.out.println("Username: " + username);
        System.out.println("Password: " + password);
        System.out.println("Environment: " + environment);
    }
}

public class Test {
    public static void main(String[] args) {
        TestData data = new TestData("testuser", "pass123", "QA");
        data.displayData();
    }
}
```

**Output:**
```
Username: testuser
Password: pass123
Environment: QA
```

**Key Learning:**
- Always use `this.variableName` in constructors when parameter names match instance variable names
- This is one of the most common OOP bugs in Java
- Modern IDEs warn about this issue
- Alternative: Use different parameter names (e.g., `newUsername`) but `this` is more conventional

---

## Challenge 3: Accessing Private Variables

**Broken Code:**
```java
public class BrowserConfig {
    private String browserName;
    private int timeout;

    public BrowserConfig(String browserName, int timeout) {
        this.browserName = browserName;
        this.timeout = timeout;
    }

    public void displayConfig() {
        System.out.println("Browser: " + browserName + ", Timeout: " + timeout);
    }
}

public class Test {
    public static void main(String[] args) {
        BrowserConfig config = new BrowserConfig("chrome", 10);
        config.displayConfig();

        // Update configuration
        config.browserName = "firefox";
        config.timeout = 30;

        config.displayConfig();
    }
}
```

**Error Message:**
```
Error: browserName has private access in BrowserConfig
Error: timeout has private access in BrowserConfig
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it by adding proper methods

**Hints:**
- Private variables cannot be accessed directly from outside the class
- Need to provide public methods to access/modify private variables
- Think about getters and setters

---

**Solution:**

**Bug Location:** Lines 21-22 - Attempting to access private variables directly from outside the class

**Explanation:**
The `private` access modifier means the variables can only be accessed within the class itself. To modify private variables from outside, we need public setter methods. This is the principle of encapsulation - hiding internal details and providing controlled access through public methods.

**Fixed Code:**
```java
public class BrowserConfig {
    private String browserName;
    private int timeout;

    public BrowserConfig(String browserName, int timeout) {
        this.browserName = browserName;
        this.timeout = timeout;
    }

    // Getter methods
    public String getBrowserName() {
        return browserName;
    }

    public int getTimeout() {
        return timeout;
    }

    // Setter methods
    public void setBrowserName(String browserName) {
        this.browserName = browserName;
    }

    public void setTimeout(int timeout) {
        this.timeout = timeout;
    }

    public void displayConfig() {
        System.out.println("Browser: " + browserName + ", Timeout: " + timeout);
    }
}

public class Test {
    public static void main(String[] args) {
        BrowserConfig config = new BrowserConfig("chrome", 10);
        config.displayConfig();

        // Update configuration using setters
        config.setBrowserName("firefox");
        config.setTimeout(30);

        config.displayConfig();
    }
}
```

**Output:**
```
Browser: chrome, Timeout: 10
Browser: firefox, Timeout: 30
```

**Key Learning:**
- Private variables enforce encapsulation
- Use public getters to read private variables
- Use public setters to modify private variables
- This allows adding validation logic in setters
- Essential pattern in Page Object Model

---

## Challenge 4: NullPointerException - Uninitialized Object

**Broken Code:**
```java
public class WebDriver {
    String browserType;
    String version;

    public void getBrowserInfo() {
        System.out.println("Browser: " + browserType + ", Version: " + version);
    }
}

public class TestRunner {
    WebDriver driver;

    public void runTest() {
        System.out.println("Starting test...");
        driver.getBrowserInfo();
        System.out.println("Test completed.");
    }
}

public class Test {
    public static void main(String[] args) {
        TestRunner runner = new TestRunner();
        runner.runTest();
    }
}
```

**Error Message:**
```
Starting test...
Exception in thread "main" java.lang.NullPointerException
    at TestRunner.runTest(TestRunner.java:6)
    at Test.main(Test.java:4)
```

**Your Task:** Fix the NullPointerException

**Hints:**
- The error occurs when calling `driver.getBrowserInfo()`
- What is the value of `driver` when `runTest()` is called?
- Objects must be initialized before use

---

**Solution:**

**Bug Location:** Line 11 - `WebDriver driver;` is declared but never initialized

**Explanation:**
In Java, instance variables of reference types (objects) are initialized to `null` by default. When we try to call a method on a null reference (`driver.getBrowserInfo()`), we get a NullPointerException. The `driver` object was declared but never created using `new`.

**Fixed Code (Option 1: Initialize in constructor):**
```java
public class WebDriver {
    String browserType;
    String version;

    public WebDriver(String browserType, String version) {
        this.browserType = browserType;
        this.version = version;
    }

    public void getBrowserInfo() {
        System.out.println("Browser: " + browserType + ", Version: " + version);
    }
}

public class TestRunner {
    WebDriver driver;

    // Constructor initializes the driver
    public TestRunner() {
        driver = new WebDriver("Chrome", "120.0");
    }

    public void runTest() {
        System.out.println("Starting test...");
        driver.getBrowserInfo();
        System.out.println("Test completed.");
    }
}

public class Test {
    public static void main(String[] args) {
        TestRunner runner = new TestRunner();
        runner.runTest();
    }
}
```

**Fixed Code (Option 2: Initialize before use):**
```java
public class WebDriver {
    String browserType;
    String version;

    public WebDriver(String browserType, String version) {
        this.browserType = browserType;
        this.version = version;
    }

    public void getBrowserInfo() {
        System.out.println("Browser: " + browserType + ", Version: " + version);
    }
}

public class TestRunner {
    WebDriver driver;

    public void runTest() {
        System.out.println("Starting test...");
        // Initialize driver before using
        driver = new WebDriver("Chrome", "120.0");
        driver.getBrowserInfo();
        System.out.println("Test completed.");
    }
}

public class Test {
    public static void main(String[] args) {
        TestRunner runner = new TestRunner();
        runner.runTest();
    }
}
```

**Output:**
```
Starting test...
Browser: Chrome, Version: 120.0
Test completed.
```

**Key Learning:**
- NullPointerException is the most common runtime error in Java
- Objects must be initialized (created with `new`) before use
- Instance variables are `null` by default until initialized
- In Selenium, this often happens when WebDriver isn't initialized in @BeforeTest
- Always initialize objects in constructors or before first use

---

## Challenge 5: Method Signature Mismatch (Logic Error)

**Broken Code:**
```java
public class Calculator {
    private int result;

    public void add(int a, int b) {
        result = a + b;
    }

    public void multiply(int a, int b) {
        result = a * b;
    }

    public int getResult() {
        return result;
    }
}

public class Test {
    public static void main(String[] args) {
        Calculator calc = new Calculator();

        // Calculate: (5 + 3) * 2
        calc.add(5, 3);
        calc.multiply(calc.getResult(), 2);

        System.out.println("Result: " + calc.getResult());
        // Expected: 16
        // Actual: 4
    }
}
```

**Expected Output:**
```
Result: 16
```

**Actual Output:**
```
Result: 4
```

**Your Task:**
1. Identify why the result is wrong
2. Explain the logic error
3. Fix it

**Hints:**
- Code compiles without errors
- Trace through the execution step by step
- What happens when `multiply()` is called?
- Check what parameters are being passed to multiply

---

**Solution:**

**Bug Location:** Line 23 - Logic error in how multiply is called

**Explanation:**
The issue is subtle. Let's trace the execution:
1. `calc.add(5, 3)` sets result to 8
2. `calc.multiply(calc.getResult(), 2)` is called with parameters (8, 2)
3. However, `multiply()` computes `a * b = 8 * 2 = 16` and stores it in `result`
4. But wait - we're calling `getResult()` WHILE calling `multiply()`, so:
   - `getResult()` is evaluated first (returns 8)
   - But by the time we print, result is actually the multiplication of the wrong values

Actually, let me reconsider. The actual issue is:
1. `calc.add(5, 3)` → result = 8
2. `calc.multiply(calc.getResult(), 2)`:
   - `getResult()` returns 8
   - But there's a race condition in evaluation

Wait, I need to reconsider this more carefully. Let me check the logic:

1. `calc.add(5, 3)` → result = 8
2. `calc.multiply(calc.getResult(), 2)` → multiply(8, 2) → result = 16
3. `getResult()` → returns 16

This should actually work correctly and output 16. Let me reconsider what would cause output of 4...

Actually, the problem might be that I set this up incorrectly. Let me create a different scenario that would actually cause the wrong output:

**Corrected Bug Explanation:**

Actually, the code as written should produce 16, not 4. Let me create a more realistic bug:

**Fixed Code:**
```java
public class Calculator {
    private int result;

    public int add(int a, int b) {
        result = a + b;
        return result;
    }

    public int multiply(int a, int b) {
        result = a * b;
        return result;
    }

    public int getResult() {
        return result;
    }
}

public class Test {
    public static void main(String[] args) {
        Calculator calc = new Calculator();

        // Calculate: (5 + 3) * 2
        int sum = calc.add(5, 3);           // sum = 8
        int product = calc.multiply(sum, 2); // product = 16

        System.out.println("Result: " + calc.getResult());  // Outputs: 16
    }
}
```

**Alternative Bug Scenario (More realistic):**

Let me create a different, more realistic bug:

**Broken Code (Revised):**
```java
public class Calculator {
    private int result;

    public void add(int a, int b) {
        result = a + b;
    }

    public void multiply(int a, int b) {
        result = a * b;
    }

    public int getResult() {
        return result;
    }

    public void reset() {
        result = 0;
    }
}

public class Test {
    public static void main(String[] args) {
        Calculator calc = new Calculator();

        // Calculate: (5 + 3) * 2
        int sum = calc.add(5, 3);  // Bug: add() returns void, sum is uninitialized
        calc.multiply(sum, 2);

        System.out.println("Result: " + calc.getResult());
    }
}
```

Actually, this won't compile either. Let me provide a better, working example:

**Better Broken Code:**
```java
public class PageObject {
    private String url;
    private String title;

    public void setUrl(String url) {
        url = url;  // Bug: missing 'this'
    }

    public void setTitle(String title) {
        title = title;  // Bug: missing 'this'
    }

    public String getUrl() {
        return url;
    }

    public String getTitle() {
        return title;
    }
}

public class Test {
    public static void main(String[] args) {
        PageObject page = new PageObject();
        page.setUrl("https://example.com");
        page.setTitle("Home Page");

        System.out.println("URL: " + page.getUrl());      // Expected: https://example.com, Actual: null
        System.out.println("Title: " + page.getTitle());  // Expected: Home Page, Actual: null
    }
}
```

This is actually a duplicate of Challenge 2. Let me create a genuinely different challenge:

**Final Broken Code:**
```java
public class Counter {
    private int count;

    public Counter(int initialValue) {
        count = initialValue;
    }

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class Test {
    public static void main(String[] args) {
        Counter counter = new Counter(10);

        counter.increment();
        counter.increment();
        counter.increment();

        Counter counter2 = counter;  // Bug: This doesn't create a new object
        counter2.increment();

        System.out.println("Counter 1: " + counter.getCount());   // Expected: 13, Actual: 14
        System.out.println("Counter 2: " + counter2.getCount());  // Expected: 1, Actual: 14
    }
}
```

**Expected Output (what developer thinks):**
```
Counter 1: 13
Counter 2: 1
```

**Actual Output:**
```
Counter 1: 14
Counter 2: 14
```

**Explanation:**
The line `Counter counter2 = counter;` does NOT create a new Counter object. It creates a new reference to the same object. Both `counter` and `counter2` point to the same Counter object in memory. When `counter2.increment()` is called, it increments the same object that `counter` refers to.

**Fixed Code:**
```java
public class Counter {
    private int count;

    public Counter(int initialValue) {
        count = initialValue;
    }

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class Test {
    public static void main(String[] args) {
        Counter counter = new Counter(10);

        counter.increment();
        counter.increment();
        counter.increment();

        // Create a NEW object, not just a new reference
        Counter counter2 = new Counter(0);  // Fixed: using 'new' to create separate object
        counter2.increment();

        System.out.println("Counter 1: " + counter.getCount());   // Output: 13
        System.out.println("Counter 2: " + counter2.getCount());  // Output: 1
    }
}
```

**Key Learning:**
- Assignment (`=`) copies references, not objects
- To create a new object, you must use the `new` keyword
- Multiple references can point to the same object
- This is crucial in Selenium when passing WebDriver between Page Objects
- Understanding references vs objects prevents many bugs in automation frameworks

---

## 🎯 Debugging Checklist

When you encounter OOP-related bugs in your automation framework:

**Constructor Issues:**
- [ ] Does constructor name match class name exactly (case-sensitive)?
- [ ] Are you using `this` when parameter names match instance variables?
- [ ] Are all instance variables initialized properly?

**Access Issues:**
- [ ] Are you trying to access private variables from outside the class?
- [ ] Do you have getter/setter methods for private variables?
- [ ] Are access modifiers (public/private) appropriate?

**NullPointerException:**
- [ ] Are all objects initialized before use?
- [ ] Did you use `new` keyword to create objects?
- [ ] In Page Objects, is WebDriver initialized in constructor?

**Reference Issues:**
- [ ] Are you using `=` thinking it creates a new object?
- [ ] Do you need `new` keyword to create a separate object?
- [ ] Are multiple variables pointing to the same object unintentionally?

**Method Issues:**
- [ ] Are method return types correct?
- [ ] Are you handling return values properly?
- [ ] Do method signatures match when calling them?

---

## 💡 Pro Debugging Tips for SDETs

**Tip 1: Use IDE Debugger**
- Set breakpoints in constructors
- Step through object initialization
- Watch instance variable values
- Check if objects are null

**Tip 2: Print Object State**
Add a debug method to your classes:
```java
public void debugPrint() {
    System.out.println("=== Object State ===");
    System.out.println("Field1: " + field1);
    System.out.println("Field2: " + field2);
    // ... all fields
}
```

**Tip 3: Null Checks**
Add defensive programming:
```java
public void someMethod() {
    if (driver == null) {
        System.out.println("ERROR: WebDriver not initialized!");
        return;
    }
    // proceed with method
}
```

**Tip 4: Constructor Validation**
```java
public PageObject(WebDriver driver) {
    if (driver == null) {
        throw new IllegalArgumentException("WebDriver cannot be null");
    }
    this.driver = driver;
}
```

---

## 🎓 Key Takeaways

**From Challenge 1:**
- Constructor name must match class name exactly (case-sensitive)

**From Challenge 2:**
- Always use `this` keyword in constructors when parameter names match instance variables

**From Challenge 3:**
- Private variables require public getters/setters for external access
- Encapsulation protects data integrity

**From Challenge 4:**
- Objects must be initialized with `new` before use
- NullPointerException is the most common runtime error
- Initialize objects in constructors or before first use

**From Challenge 5:**
- Assignment (`=`) copies references, not objects
- Use `new` keyword to create separate objects
- Understanding references prevents framework bugs

---

## ✅ Debug Practice Complete!

**Challenges Solved:** __/5
**Time Taken:** ___ minutes
**Confidence in Debugging:** __/10

**Next Step:** Move to **Day-4-Project-Guide.md** to build the BankAccount class project!

---
