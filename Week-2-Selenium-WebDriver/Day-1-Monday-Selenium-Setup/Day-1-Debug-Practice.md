# DAY 1 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As an SDET, you'll spend 30-40% of your time debugging failing tests. Selenium-specific errors are different from regular Java errors - you need to recognize patterns like:
- Browser driver issues
- Timing problems
- Element not found errors
- Stale element exceptions

**Today's Focus:** Setup and configuration errors - the most common issues when starting with Selenium.

---

## Challenge 1: Missing Dependency Error

**Broken Code:**
```java
package com.sdet.debug;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Debug1_MissingDependency {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.google.com");
        driver.quit();
    }
}
```

**Error Message:**
```
java: package org.openqa.selenium does not exist
java: package org.openqa.selenium.chrome does not exist
java: cannot find symbol ChromeDriver
```

**Your Task:**
1. Identify what's missing
2. Explain why this error occurs
3. Fix it

**Hints:**
- Look at the import statements
- What does Maven need to download Selenium?
- Check your `pom.xml` file

---

**Solution:**

**Bug Location:** Missing Selenium dependency in `pom.xml`

**Explanation:**
Java can't find the Selenium classes because:
1. The import statements reference `org.openqa.selenium.*` packages
2. These packages are not part of standard Java (JDK)
3. Maven needs to download these libraries from Maven Central Repository
4. Without the dependency in `pom.xml`, the JAR files are never downloaded

**Fixed pom.xml:**
```xml
<dependencies>
    <!-- Add this dependency -->
    <dependency>
        <groupId>org.seleniumhq.selenium</groupId>
        <artifactId>selenium-java</artifactId>
        <version>4.15.0</version>
    </dependency>
</dependencies>
```

**After adding:**
1. Right-click `pom.xml` → Maven → Reload Project
2. Or run: `mvn clean install`
3. Check External Libraries folder shows selenium-java

**Key Learning:**
- Import errors usually mean missing dependencies
- Always check `pom.xml` first when you see "package does not exist"
- Maven must download libraries before you can import classes

---

## Challenge 2: Browser Not Closing (Memory Leak)

**Broken Code:**
```java
package com.sdet.debug;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Debug2_MemoryLeak {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        driver.get("https://www.google.com");

        String title = driver.getTitle();
        System.out.println("Title: " + title);

        // Test completed
    }
}
```

**Error Message:**
```
(No compile error, but...)
Chrome browser stays open after program ends
Running test 10 times = 10 Chrome windows stay open
Eventually: System runs out of memory
```

**Your Task:**
1. Identify the bug
2. Explain the consequence
3. Fix it properly

**Hints:**
- What happens to the browser after the test ends?
- How should you clean up WebDriver resources?
- What if an exception occurs before cleanup?

---

**Solution:**

**Bug Location:** Missing `driver.quit()` call

**Explanation:**
```java
WebDriver driver = new ChromeDriver(); // Opens browser process
// ... test code ...
// Program ends, but browser process keeps running!
```

When you create a ChromeDriver instance:
1. Java process starts separate Chrome process
2. These are two different OS processes
3. When Java program ends, it doesn't automatically kill Chrome
4. Chrome process becomes "orphaned" - running but not controlled
5. Each test run creates another orphaned Chrome process
6. Eventually: System runs out of memory or process limits

**Fixed Code:**
```java
package com.sdet.debug;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Debug2_MemoryLeak_Fixed {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.google.com");
            String title = driver.getTitle();
            System.out.println("Title: " + title);
        } finally {
            // ALWAYS close browser, even if exception occurs
            driver.quit();
        }
    }
}
```

**Why try-finally?**
```java
// What if an exception occurs?
WebDriver driver = new ChromeDriver();
driver.get("https://invalid-url"); // Throws exception
driver.quit(); // NEVER EXECUTES - browser stays open!

// With try-finally:
WebDriver driver = new ChromeDriver();
try {
    driver.get("https://invalid-url"); // Exception
} finally {
    driver.quit(); // ALWAYS EXECUTES, even after exception
}
```

**Key Learning:**
- **ALWAYS** use try-finally with WebDriver
- `quit()` must execute even if test fails
- Memory leaks are subtle - you won't notice until CI/CD fails
- In real frameworks, this goes in `@AfterMethod` (TestNG) or `@AfterEach` (JUnit)

---

## Challenge 3: Wrong Browser Close Method

**Broken Code:**
```java
package com.sdet.debug;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Debug3_WrongCloseMethod {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Open Google in main window
            driver.get("https://www.google.com");

            // Open a new tab
            driver.switchTo().newWindow(WindowType.TAB);
            driver.get("https://www.amazon.com");

            System.out.println("Test completed");
        } finally {
            driver.close(); // Using close() instead of quit()
        }
    }
}
```

**Error Message:**
```
(Compile error first:)
java: cannot find symbol WindowType

(After fixing that, runtime issue:)
Chrome window stays open after test
Only Amazon tab closes, Google tab remains
```

**Your Task:**
1. Fix the compile error first
2. Explain what close() does vs quit()
3. Fix the cleanup issue

**Hints:**
- WindowType needs an import
- close() vs quit() - what's the difference?
- How many windows/tabs are open?

---

**Solution:**

**Bug Location 1:** Missing import for WindowType

**Fixed Import:**
```java
import org.openqa.selenium.WindowType; // Add this import
```

**Bug Location 2:** Using `close()` instead of `quit()`

**Explanation:**
```java
driver.close();  // Closes ONLY current window/tab
driver.quit();   // Closes ALL windows/tabs AND ends WebDriver session
```

**What happens with close():**
```
1. Test opens main window (Google)
2. Test opens new tab (Amazon)
3. driver.close() → Closes Amazon tab (current window)
4. Google tab still open!
5. WebDriver session still alive but unreachable
6. Memory leak
```

**Fixed Code:**
```java
package com.sdet.debug;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WindowType;
import org.openqa.selenium.chrome.ChromeDriver;

public class Debug3_WrongCloseMethod_Fixed {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Open Google in main window
            driver.get("https://www.google.com");

            // Open a new tab
            driver.switchTo().newWindow(WindowType.TAB);
            driver.get("https://www.amazon.com");

            System.out.println("Test completed");
        } finally {
            // Use quit() to close ALL windows
            driver.quit();
        }
    }
}
```

**Key Learning:**
- **close()** → Closes current window only
- **quit()** → Closes all windows + ends WebDriver session
- **ALWAYS use quit()** in cleanup code
- Only use close() if intentionally closing one window while keeping others open

---

## Challenge 4: Incorrect Maven Compiler Version

**Broken pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sdet.practice</groupId>
    <artifactId>selenium-project</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.15.0</version>
        </dependency>
    </dependencies>
</project>
```

**Error Message:**
```
Error: Java compiler level does not match the version of the installed Java SDK
```
**Or:**
```
Error: Source option 5 is no longer supported. Use 7 or later.
```

**Your Task:**
1. Identify what's missing
2. Explain why Maven needs this
3. Add the missing configuration

**Hints:**
- What Java version are you using? (Run `java -version`)
- Maven defaults to Java 5 if not specified
- Selenium 4 requires Java 11+

---

**Solution:**

**Bug Location:** Missing `<properties>` section with compiler version

**Explanation:**
- Maven defaults to Java 1.5 if you don't specify version
- Selenium 4 requires Java 11 or higher
- Modern Java features (var, lambdas, etc.) won't work with Java 5
- You must explicitly tell Maven which Java version to use

**Fixed pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sdet.practice</groupId>
    <artifactId>selenium-project</artifactId>
    <version>1.0-SNAPSHOT</version>

    <!-- ADD THIS SECTION -->
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.15.0</version>
        </dependency>
    </dependencies>
</project>
```

**What each property means:**
```xml
<maven.compiler.source>11</maven.compiler.source>
<!-- Java version of your source code -->

<maven.compiler.target>11</maven.compiler.target>
<!-- Java version of compiled bytecode -->

<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
<!-- Character encoding (prevents warnings) -->
```

**Key Learning:**
- Always set Java version in Maven projects
- Use Java 11 or 17 (LTS versions)
- Match source and target versions
- These three properties are in EVERY professional Maven project

---

## Challenge 5: Logic Bug (No Exception, Wrong Behavior)

**Code:**
```java
package com.sdet.debug;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Debug5_LogicBug {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.google.com");

            // Verify we're on Google
            String expectedTitle = "Google";
            String actualTitle = driver.getTitle();

            if (expectedTitle == actualTitle) {
                System.out.println("PASS: Title is correct");
            } else {
                System.out.println("FAIL: Title mismatch");
                System.out.println("Expected: " + expectedTitle);
                System.out.println("Actual: " + actualTitle);
            }
        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:** `PASS: Title is correct`
**Actual Output:**
```
FAIL: Title mismatch
Expected: Google
Actual: Google
```

**Your Task:**
Find and fix the logic error (hint: it compiles and runs, but comparison fails)

---

**Solution:**

**Bug Location:** Line comparing titles: `if (expectedTitle == actualTitle)`

**Explanation:**
```java
// WRONG: Compares object references, not content
if (expectedTitle == actualTitle) { ... }

// RIGHT: Compares string content
if (expectedTitle.equals(actualTitle)) { ... }
```

**Why `==` fails:**
```java
String str1 = "Google";
String str2 = driver.getTitle(); // Returns new String object

// str1 and str2 point to DIFFERENT objects in memory
str1 == str2 → false (different memory addresses)

// But they contain the SAME characters
str1.equals(str2) → true (same content)
```

**Fixed Code:**
```java
if (expectedTitle.equals(actualTitle)) {
    System.out.println("PASS: Title is correct");
} else {
    System.out.println("FAIL: Title mismatch");
    System.out.println("Expected: " + expectedTitle);
    System.out.println("Actual: " + actualTitle);
}
```

**Even Better (null-safe):**
```java
// Handles null values safely
if (Objects.equals(expectedTitle, actualTitle)) {
    // ...
}

// Or put constant first to avoid NullPointerException
if ("Google".equals(actualTitle)) {
    // If actualTitle is null, this returns false instead of NPE
}
```

**Key Learning:**
- **NEVER use `==` to compare Strings** (compares references, not content)
- **ALWAYS use `.equals()`** to compare String content
- This is a very common mistake when coming from Python (where `==` works for strings)
- In Python: `"a" == "a"` → True (compares content)
- In Java: `"a" == "a"` → Maybe true, maybe false (compares references)

**Python vs Java String Comparison:**
```python
# Python - == compares content
expected = "Google"
actual = get_title()
if expected == actual:  # Compares content ✓
    print("Match")
```

```java
// Java - MUST use .equals()
String expected = "Google";
String actual = driver.getTitle();
if (expected == actual) {  // Compares references ✗
    System.out.println("Match");
}

if (expected.equals(actual)) {  // Compares content ✓
    System.out.println("Match");
}
```

---

## 🎯 Debugging Skills Summary

**Today You Learned to Debug:**
1. ✅ Missing dependencies (import errors)
2. ✅ Resource leaks (browser not closing)
3. ✅ Method confusion (close vs quit)
4. ✅ Maven configuration (Java version)
5. ✅ Logic bugs (String comparison)

**Common Selenium Error Patterns:**
| Error Type | Common Cause | Quick Fix |
|------------|-------------|-----------|
| `package org.openqa.selenium does not exist` | Missing dependency | Add selenium-java to pom.xml |
| Chrome stays open | Missing quit() | Use try-finally with driver.quit() |
| `cannot find symbol` | Missing import | Alt+Enter in IntelliJ for auto-import |
| Comparison fails | Using `==` for Strings | Use `.equals()` instead |
| Java version error | Missing properties | Add compiler source/target to pom.xml |

**Debugging Checklist:**
- [ ] Are all dependencies in pom.xml?
- [ ] Did I reload Maven project after changing pom.xml?
- [ ] Is driver.quit() in a finally block?
- [ ] Am I using .equals() for String comparison?
- [ ] Is Java version set correctly? (Java 11+)
- [ ] Did I import all required classes?

---

## 💡 Pro Debugging Tips

**Tip 1: Read Error Messages Carefully**
```
java: package org.openqa.selenium does not exist
      ^^^^^^^^
      Focus on "package" - this is a missing dependency issue
```

**Tip 2: Use IntelliJ's Quick Fixes**
- Red underline? → Alt+Enter → Auto-import
- Missing import? → Alt+Enter → Add import
- Maven dependency? → Alt+Enter → Add Maven dependency

**Tip 3: Check Selenium Logs**
```java
// Enable verbose logging
System.setProperty("webdriver.chrome.verboseLogging", "true");
```

**Tip 4: Verify Setup**
```bash
# Check Java version
java -version  # Should be 11 or higher

# Check Maven
mvn -version   # Should show Maven 3.x

# Clean rebuild
mvn clean install
```

**Tip 5: Isolate the Problem**
```java
// Simplest possible test
public class DiagnosticTest {
    public static void main(String[] args) {
        System.out.println("Java works");

        WebDriver driver = new ChromeDriver();
        System.out.println("ChromeDriver works");

        driver.quit();
        System.out.println("Quit works");
    }
}
```

---

## 🎓 Debugging Challenge Complete!

You've practiced debugging the 5 most common Selenium setup issues. These skills will save you hours of frustration as you progress.

**Remember:**
- Error messages are your friends - read them carefully
- Most issues are configuration, not code logic
- IntelliJ's auto-fix (Alt+Enter) solves 80% of problems
- When stuck: Clean, rebuild, restart IDE

**Next:** Day 1 Project Guide - Build your first complete automation test!
