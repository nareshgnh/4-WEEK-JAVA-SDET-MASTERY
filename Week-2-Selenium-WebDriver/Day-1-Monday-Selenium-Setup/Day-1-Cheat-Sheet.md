# DAY 1 CHEAT SHEET: SELENIUM SETUP

## ⚡ Quick Syntax Reference

### Create WebDriver Instance
```java
// Chrome browser
WebDriver driver = new ChromeDriver();

// Firefox browser
WebDriver driver = new FirefoxDriver();

// Edge browser
WebDriver driver = new EdgeDriver();
```

### Navigate to URL
```java
driver.get("https://www.google.com");  // Navigate and wait for page load
```

### Get Page Information
```java
String title = driver.getTitle();           // Get page title
String url = driver.getCurrentUrl();        // Get current URL
String pageSource = driver.getPageSource(); // Get HTML source
```

### Browser Actions
```java
driver.manage().window().maximize();        // Maximize window
driver.manage().window().fullscreen();      // Fullscreen mode
driver.navigate().back();                   // Browser back button
driver.navigate().forward();                // Browser forward button
driver.navigate().refresh();                // Refresh page
```

### Close Browser
```java
driver.close();  // Close current window
driver.quit();   // Close all windows and end session (ALWAYS USE THIS)
```

### ChromeOptions Configuration
```java
ChromeOptions options = new ChromeOptions();
options.addArguments("--headless");         // Run without UI
options.addArguments("--start-maximized");  // Start maximized
options.addArguments("--incognito");        // Private mode
options.addArguments("--disable-notifications");
WebDriver driver = new ChromeDriver(options);
```

### Basic Element Interaction (Preview for Tomorrow)
```java
// Find element
WebElement element = driver.findElement(By.name("q"));

// Type text
element.sendKeys("Selenium WebDriver");

// Submit form
element.submit();

// Click element
element.click();
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Syntax | Python Playwright Equivalent |
|---------|-------------|------------------------------|
| **Import** | `import org.openqa.selenium.WebDriver;` | `from playwright.sync_api import sync_playwright` |
| **Create Browser** | `WebDriver driver = new ChromeDriver();` | `browser = p.chromium.launch()` |
| **Navigate** | `driver.get("url")` | `page.goto("url")` |
| **Get Title** | `driver.getTitle()` | `page.title()` |
| **Get URL** | `driver.getCurrentUrl()` | `page.url` |
| **Close Browser** | `driver.quit()` | `browser.close()` |
| **Find Element** | `driver.findElement(By.name("q"))` | `page.locator('input[name="q"]')` |
| **Type Text** | `element.sendKeys("text")` | `element.fill("text")` |

---

## 📦 Maven pom.xml Template

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

---

## 🎯 Basic Test Template

```java
package com.sdet.tests;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class BasicTest {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Test steps here
            driver.manage().window().maximize();
            driver.get("https://www.google.com");
            System.out.println("Title: " + driver.getTitle());

        } catch (Exception e) {
            System.out.println("Test failed: " + e.getMessage());
            e.printStackTrace();

        } finally {
            driver.quit(); // ALWAYS close browser
        }
    }
}
```

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Forgetting to close browser** | No `driver.quit()` | Always use `try-finally` with `driver.quit()` |
| **Using close() instead of quit()** | `driver.close()` | `driver.quit()` (closes all windows) |
| **String comparison** | `if (str1 == str2)` | `if (str1.equals(str2))` |
| **Missing dependency** | No selenium in pom.xml | Add `<dependency>` for selenium-java |
| **Wildcard import** | `import org.openqa.selenium.*` | Import specific classes |
| **No Java version** | Missing `<properties>` in pom.xml | Set maven.compiler.source/target to 11+ |

---

## 🚨 Common Errors & Quick Fixes

| Error Message | Cause | Fix |
|---------------|-------|-----|
| `package org.openqa.selenium does not exist` | Missing Selenium dependency | Add selenium-java to pom.xml, reload Maven |
| `cannot find symbol: ChromeDriver` | Missing import | `import org.openqa.selenium.chrome.ChromeDriver;` |
| Chrome stays open after test | Forgot `driver.quit()` | Add `finally { driver.quit(); }` |
| `Source option 5 is no longer supported` | No Java version in pom.xml | Add `<properties>` with compiler source/target 11+ |
| Title comparison fails but looks same | Using `==` instead of `.equals()` | Use `title.equals("Google")` |

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ Set up Maven projects for Selenium automation
- ✅ Configure dependencies in pom.xml
- ✅ Create WebDriver instances for different browsers
- ✅ Navigate to websites and get page information
- ✅ Use try-finally for proper resource cleanup
- ✅ Configure ChromeOptions for custom browser behavior
- ✅ Understand driver.close() vs driver.quit()

**Critical for Automation:**
- 🎯 **ALWAYS use `driver.quit()` in finally block** - Prevents memory leaks
- 🎯 **Use interface reference `WebDriver`** - Enables browser switching
- 🎯 **Maven manages all dependencies** - No manual JAR downloads
- 🎯 **Set Java 11+ in pom.xml** - Required for Selenium 4

---

## 🐍 Python → Java Key Differences

### Selenium Setup
**Python Playwright:**
```python
with sync_playwright() as p:
    browser = p.chromium.launch()
    page = browser.new_page()
    page.goto("https://google.com")
    print(page.title())
    browser.close()
```

**Java Selenium:**
```java
WebDriver driver = new ChromeDriver();
try {
    driver.get("https://google.com");
    System.out.println(driver.getTitle());
} finally {
    driver.quit();
}
```

### Key Differences
| Aspect | Python | Java |
|--------|--------|------|
| **Context Manager** | `with` statement auto-closes | Manual try-finally |
| **Typing** | `browser = p.chromium.launch()` | `WebDriver driver = new ChromeDriver()` |
| **String Comparison** | `==` works | Must use `.equals()` |
| **Dependency Mgmt** | `pip install playwright` | Maven pom.xml |

---

## 🎤 Interview Phrases

**When asked about Selenium WebDriver, say:**

*"Selenium WebDriver is an open-source browser automation framework that provides a programming interface to control browsers programmatically. It follows the W3C WebDriver protocol and supports multiple browsers like Chrome, Firefox, and Edge. Unlike Selenium IDE which is record-and-playback, WebDriver gives you full programmatic control and integrates with test frameworks like TestNG for enterprise test automation."*

**When asked about Maven, say:**

*"Maven is a build automation and dependency management tool for Java projects. In Selenium automation, Maven handles dependency downloads from Maven Central Repository, provides standardized project structure, manages the build lifecycle, and integrates seamlessly with CI/CD tools like Jenkins. The pom.xml file is the heart of Maven configuration where we define dependencies, plugins, and project properties."*

**When asked about close() vs quit(), say:**

*"The key difference is scope and cleanup. `driver.close()` closes only the current browser window that WebDriver is controlling, while `driver.quit()` closes all browser windows opened during the session and completely terminates the WebDriver session, freeing all resources. Best practice is to always use `driver.quit()` in a finally block to ensure proper cleanup even if the test fails."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **ALWAYS use `WebDriver driver = new ChromeDriver()` (interface reference) and ALWAYS call `driver.quit()` in a finally block. This ensures browser flexibility and proper resource cleanup.**

```java
WebDriver driver = new ChromeDriver();  // Interface reference
try {
    // Your test code
} finally {
    driver.quit();  // ALWAYS cleanup
}
```

---

## 🔮 Tomorrow's Preview

**Day 2 Topic:** Locators Mastery

**What to review tonight:**
- How you used `By.name("q")` to find the search box
- What makes an element unique on a page
- HTML basics (if rusty): id, name, class attributes

**What to think about:**
*"If you need to find a specific button on a webpage, how would you describe its location? By its text? By its position? By a unique ID? Tomorrow you'll learn 8 different strategies!"*

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- ✅ Learned Selenium WebDriver basics
- ✅ Set up Maven project
- ✅ Wrote first browser automation test
- ✅ Completed Google Search automation project
- ✅ ~3 hours of Selenium practice

**Cumulative Progress:**
- Days completed: 1/7 (Week 2)
- Selenium concepts mastered: Browser setup, navigation, basic interaction
- Tests written: 6+ practice exercises + 1 project

**Momentum Statement:**
*"You just automated a browser in Java - something that seemed complex yesterday is now in your toolkit. In Week 1, you learned Java. Today, you made Java control a real browser. Tomorrow, you'll master finding ANY element on ANY webpage. You're 1/28th of the way to your 25 LPA SDET goal. Keep this momentum!"*

---

## 📚 Quick Reference Commands

**Maven Commands:**
```bash
mvn clean install      # Build project and download dependencies
mvn clean             # Clean target directory
mvn compile           # Compile source code
mvn test              # Run tests
```

**Useful Imports:**
```java
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;
import org.openqa.selenium.By;
import org.openqa.selenium.WebElement;
```

**IntelliJ Shortcuts:**
```
Alt + Enter           # Quick fix / Auto import
Ctrl + Space          # Code completion
Ctrl + Shift + F10    # Run current file
Shift + F10           # Run last configuration
```

---

## 🔧 Troubleshooting Checklist

**If your test doesn't work:**
- [ ] Is Selenium dependency in pom.xml?
- [ ] Did you reload Maven project after changing pom.xml?
- [ ] Is Java version 11+ set in pom.xml properties?
- [ ] Did you import required classes?
- [ ] Is `driver.quit()` in a finally block?
- [ ] Are you using `.equals()` for String comparison?
- [ ] Is Chrome browser installed on your system?
- [ ] Do you have internet connection? (for dependency downloads)

**Emergency Reset:**
```bash
# If all else fails:
mvn clean install -U  # Force update dependencies
# File → Invalidate Caches → Restart (IntelliJ)
```

---

## 🎉 Well Done!

You've completed Day 1! Save this cheat sheet - you'll refer to it often.

**Pro Tip:** Print this page or keep it open in a second monitor while coding. Quick reference saves time!

**Tomorrow:** Learn 8 ways to find elements (ID, Name, ClassName, CSS, XPath, LinkText, PartialLinkText, TagName)

**Rest up - Day 2 is even more exciting! 🚀**
