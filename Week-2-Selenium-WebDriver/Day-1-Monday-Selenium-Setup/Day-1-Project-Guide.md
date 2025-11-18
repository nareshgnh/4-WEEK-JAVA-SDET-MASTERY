# DAY 1 MINI PROJECT: GOOGLE SEARCH AUTOMATION

## 🎯 Project Overview

**What You're Building:**
A complete automated test that opens Google, performs a search, and verifies the results - just like a real user would, but programmatically!

**Why This Project:**
- Reinforces WebDriver setup and browser control
- Practices element interaction (finding search box, typing, submitting)
- Introduces basic verification (title checking)
- Builds a complete end-to-end test flow
- Creates your first showcaseable automation script

**Time Required:** 30-45 minutes

**Skills Practiced:**
- Maven project setup
- WebDriver lifecycle (create → use → close)
- Browser navigation
- Element location (By.name locator)
- User interaction simulation (sendKeys, submit)
- Verification and assertions
- Exception handling

---

## 📋 Requirements

### Must-Have Features:
1. **Setup Phase:** Create Maven project with Selenium dependency
2. **Browser Launch:** Open Chrome browser in maximized window
3. **Navigation:** Go to Google homepage (https://www.google.com)
4. **Homepage Verification:** Verify page title is "Google"
5. **Search Execution:** Find search box, type "Selenium WebDriver", submit search
6. **Results Verification:** Verify results page title contains "Selenium WebDriver"
7. **Reporting:** Print PASS/FAIL status with clear messages
8. **Cleanup:** Properly close browser using try-finally

### Nice-to-Have (Bonus):
1. Add timestamps to see how long each step takes
2. Take screenshot of search results page
3. Count number of search results displayed
4. Extract and print the first search result title
5. Add configuration for headless mode (command-line argument)

---

## 🏗️ Project Structure

**Project Name:** `google-search-automation`

**Package Structure:**
```
google-search-automation/
├── pom.xml                           # Maven configuration
├── src/
│   └── test/
│       └── java/
│           └── com/
│               └── automation/
│                   └── tests/
│                       └── GoogleSearchTest.java
```

**Classes Needed:**
1. `GoogleSearchTest.java` - Main test class

**Methods to Implement:**
- `main(String[] args)` - Entry point, orchestrates test flow
- `verifyTitle(WebDriver, String, String)` - Helper method to verify title (optional but recommended)

---

## 📝 Step-by-Step Guide

### Step 1: Create Maven Project

**In IntelliJ IDEA:**
1. File → New → Project
2. Select "Maven"
3. Fill in:
   - Name: `google-search-automation`
   - GroupId: `com.automation`
   - ArtifactId: `google-search-automation`
4. Click "Create"

---

### Step 2: Configure pom.xml

**What to do:**
Add Selenium dependency and configure Java version

**Code Template:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.automation</groupId>
    <artifactId>google-search-automation</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <!-- TODO: Set Java 11 as source and target -->
        <!-- TODO: Set UTF-8 encoding -->
    </properties>

    <dependencies>
        <!-- TODO: Add Selenium 4.15.0 dependency -->
    </dependencies>
</project>
```

**Testing:**
After saving pom.xml, IntelliJ should show "Importing..." in bottom-right. Wait for it to finish.

---

### Step 3: Create Test Class

**What to do:**
Create `GoogleSearchTest.java` in `src/test/java/com/automation/tests/`

**Code Template:**
```java
package com.automation.tests;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class GoogleSearchTest {
    public static void main(String[] args) {
        // We'll build this step by step
    }
}
```

---

### Step 4: Implement Browser Setup

**What to do:**
Create WebDriver instance and maximize window

**Code Template:**
```java
public static void main(String[] args) {
    // TODO: Create ChromeDriver instance
    // Store in WebDriver variable for flexibility

    try {
        // TODO: Maximize browser window
        // Hint: driver.manage().window().maximize()

        System.out.println("✓ Browser launched and maximized");

    } catch (Exception e) {
        // TODO: Print error message and stack trace
    } finally {
        // TODO: Close browser (we'll add this later)
    }
}
```

**Testing:**
Run the program. Chrome should open maximized, then close immediately.

---

### Step 5: Navigate to Google

**What to do:**
Navigate to Google homepage and verify title

**Code Template:**
```java
try {
    driver.manage().window().maximize();
    System.out.println("✓ Browser launched and maximized");

    // TODO: Navigate to https://www.google.com
    // Hint: driver.get(...)

    // TODO: Wait 1 second to see the page load
    // Hint: Thread.sleep(1000)

    // TODO: Get page title
    // TODO: Print "Homepage title: <title>"

    // TODO: Verify title equals "Google"
    // If not, throw new Exception("Title verification failed")

    System.out.println("✓ Google homepage loaded successfully");

} catch (Exception e) {
    // Error handling
}
```

**Testing:**
Run the program. Should open Google, print title, verify it's "Google".

---

### Step 6: Find and Interact with Search Box

**What to do:**
Locate the search box and type search query

**Code Template:**
```java
// After Google homepage verification...

// TODO: Find search box element
// Hint: driver.findElement(By.name("q"))
// Store in WebElement variable

System.out.println("✓ Search box located");

// TODO: Type "Selenium WebDriver" in search box
// Hint: searchBox.sendKeys("...")

System.out.println("✓ Search query entered: Selenium WebDriver");

// TODO: Wait 1 second to see the text
```

**Testing:**
Run the program. Should type "Selenium WebDriver" in search box (but not submit yet).

---

### Step 7: Submit Search and Verify Results

**What to do:**
Submit the search form and verify results page

**Code Template:**
```java
// After entering search text...

// TODO: Submit the search form
// Hint: searchBox.submit()

System.out.println("✓ Search submitted");

// TODO: Wait 2 seconds for results to load

// TODO: Get results page title

// TODO: Verify title contains "Selenium WebDriver"
// Hint: Use .contains() method

System.out.println("✓ Search results page loaded");
System.out.println("Results page title: " + resultsTitle);
```

**Testing:**
Run the program. Should perform complete search and show results.

---

### Step 8: Add Cleanup and Final Reporting

**What to do:**
Ensure browser closes properly and print final test status

**Code Template:**
```java
try {
    // ... all test steps ...

    // If we reach here, all verifications passed
    System.out.println("\n" + "=".repeat(50));
    System.out.println("✓✓✓ GOOGLE SEARCH AUTOMATION - PASSED ✓✓✓");
    System.out.println("=".repeat(50));

} catch (Exception e) {
    System.out.println("\n" + "=".repeat(50));
    System.out.println("✗✗✗ GOOGLE SEARCH AUTOMATION - FAILED ✗✗✗");
    System.out.println("Error: " + e.getMessage());
    System.out.println("=".repeat(50));
    e.printStackTrace();

} finally {
    // TODO: Close browser
    // TODO: Print "Browser closed"
}
```

---

## ✅ Testing Checklist

Test your project against these scenarios:

- [ ] **Project builds successfully:** Run `mvn clean install` without errors
- [ ] **Browser opens:** Chrome launches when you run the test
- [ ] **Window maximizes:** Browser is maximized, not default size
- [ ] **Google loads:** Homepage appears correctly
- [ ] **Title verified:** Console shows "Homepage title: Google"
- [ ] **Search box found:** No "element not found" errors
- [ ] **Text entered:** "Selenium WebDriver" appears in search box
- [ ] **Search submits:** Results page loads
- [ ] **Results verified:** Console shows results page title
- [ ] **Test passes:** Final output shows "PASSED"
- [ ] **Browser closes:** Chrome closes automatically after test
- [ ] **No zombie processes:** Check Task Manager - no leftover Chrome processes

**Expected Console Output:**
```
✓ Browser launched and maximized
✓ Google homepage loaded successfully
Homepage title: Google
✓ Search box located
✓ Search query entered: Selenium WebDriver
✓ Search submitted
✓ Search results page loaded
Results page title: Selenium WebDriver - Google Search

==================================================
✓✓✓ GOOGLE SEARCH AUTOMATION - PASSED ✓✓✓
==================================================
Browser closed
```

---

## 🎓 Complete Solution (Only Look After Attempting!)

### Full Implementation

**pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.automation</groupId>
    <artifactId>google-search-automation</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- Selenium WebDriver -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.15.0</version>
        </dependency>
    </dependencies>
</project>
```

**GoogleSearchTest.java:**
```java
package com.automation.tests;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

/**
 * Day 1 Project: Google Search Automation
 * Demonstrates basic Selenium WebDriver usage:
 * - Browser launch and configuration
 * - Navigation
 * - Element interaction
 * - Verification
 * - Proper cleanup
 */
public class GoogleSearchTest {

    public static void main(String[] args) {
        // Create WebDriver instance (opens Chrome browser)
        WebDriver driver = new ChromeDriver();

        try {
            // ===== SETUP PHASE =====
            driver.manage().window().maximize();
            System.out.println("✓ Browser launched and maximized");

            // ===== NAVIGATION PHASE =====
            driver.get("https://www.google.com");
            Thread.sleep(1000); // Pause to see page load

            // ===== HOMEPAGE VERIFICATION =====
            String homeTitle = driver.getTitle();
            System.out.println("Homepage title: " + homeTitle);

            if (!homeTitle.equals("Google")) {
                throw new Exception("Homepage title verification failed! Expected: Google, Actual: " + homeTitle);
            }
            System.out.println("✓ Google homepage loaded successfully");

            // ===== SEARCH INTERACTION =====
            // Find search box using 'name' attribute
            WebElement searchBox = driver.findElement(By.name("q"));
            System.out.println("✓ Search box located");

            // Type search query
            String searchQuery = "Selenium WebDriver";
            searchBox.sendKeys(searchQuery);
            System.out.println("✓ Search query entered: " + searchQuery);
            Thread.sleep(1000); // Pause to see text entry

            // Submit search form
            searchBox.submit();
            System.out.println("✓ Search submitted");
            Thread.sleep(2000); // Wait for results page

            // ===== RESULTS VERIFICATION =====
            String resultsTitle = driver.getTitle();
            System.out.println("✓ Search results page loaded");
            System.out.println("Results page title: " + resultsTitle);

            if (!resultsTitle.contains(searchQuery)) {
                throw new Exception("Results page title verification failed! Expected to contain: " + searchQuery + ", Actual: " + resultsTitle);
            }

            // ===== SUCCESS REPORTING =====
            System.out.println("\n" + "=".repeat(50));
            System.out.println("✓✓✓ GOOGLE SEARCH AUTOMATION - PASSED ✓✓✓");
            System.out.println("=".repeat(50));
            System.out.println("\nTest Summary:");
            System.out.println("- Navigated to Google homepage");
            System.out.println("- Verified homepage title");
            System.out.println("- Located search box");
            System.out.println("- Entered search query: " + searchQuery);
            System.out.println("- Submitted search");
            System.out.println("- Verified results page title");

        } catch (InterruptedException e) {
            System.out.println("\n✗ Test interrupted: " + e.getMessage());
            e.printStackTrace();

        } catch (Exception e) {
            System.out.println("\n" + "=".repeat(50));
            System.out.println("✗✗✗ GOOGLE SEARCH AUTOMATION - FAILED ✗✗✗");
            System.out.println("=".repeat(50));
            System.out.println("Error: " + e.getMessage());
            e.printStackTrace();

        } finally {
            // ===== CLEANUP PHASE =====
            // CRITICAL: Always close browser, even if test fails
            driver.quit();
            System.out.println("\nBrowser closed");
        }
    }
}
```

**Explanation of Key Parts:**

**1. WebDriver Interface Usage:**
```java
WebDriver driver = new ChromeDriver();
// Using interface (WebDriver) not concrete class (ChromeDriver)
// This allows easy browser switching later:
// WebDriver driver = new FirefoxDriver();  // Just change this line
```

**2. Try-Catch-Finally Structure:**
```java
try {
    // Test steps - might throw exceptions
} catch (InterruptedException e) {
    // Handle Thread.sleep() interruptions
} catch (Exception e) {
    // Handle all other exceptions (element not found, etc.)
} finally {
    // Cleanup - ALWAYS executes, even if exception occurs
    driver.quit();
}
```

**3. Element Location:**
```java
WebElement searchBox = driver.findElement(By.name("q"));
// By.name("q") → Locator strategy (we'll learn 8 types tomorrow)
// Returns WebElement object representing the input field
```

**4. User Interaction:**
```java
searchBox.sendKeys("Selenium WebDriver");  // Types text
searchBox.submit();                         // Submits form (same as pressing Enter)
```

**5. Verification Pattern:**
```java
if (!title.equals("Google")) {
    throw new Exception("Verification failed");
}
// In real tests, we'll use assertions (TestNG/JUnit)
// For now, throwing exceptions makes test fail visibly
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these:

### Enhancement 1: Add Screenshot Capability
```java
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;

// After results page loads:
TakesScreenshot screenshot = (TakesScreenshot) driver;
File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
Files.copy(srcFile.toPath(), Paths.get("search-results.png"));
System.out.println("✓ Screenshot saved: search-results.png");
```

### Enhancement 2: Count Search Results
```java
import java.util.List;

// After results load:
List<WebElement> results = driver.findElements(By.cssSelector("h3"));
System.out.println("Number of results: " + results.size());
```

### Enhancement 3: Extract First Result Title
```java
WebElement firstResult = driver.findElement(By.cssSelector("h3"));
System.out.println("First result: " + firstResult.getText());
```

### Enhancement 4: Configurable Search Query
```java
public static void main(String[] args) {
    String searchQuery = args.length > 0 ? args[0] : "Selenium WebDriver";
    // Now you can run with: java GoogleSearchTest "Java Programming"
}
```

### Enhancement 5: Headless Mode Option
```java
import org.openqa.selenium.chrome.ChromeOptions;

ChromeOptions options = new ChromeOptions();
if (args.length > 0 && args[0].equals("--headless")) {
    options.addArguments("--headless");
}
WebDriver driver = new ChromeDriver(options);
```

---

## 💼 How This Relates to Real SDET Work

**In Production Frameworks, This Becomes:**

**1. Page Object Model (Week 2, Day 6):**
```java
GoogleHomePage homePage = new GoogleHomePage(driver);
homePage.search("Selenium WebDriver");
SearchResultsPage resultsPage = new SearchResultsPage(driver);
resultsPage.verifyResultsFor("Selenium WebDriver");
```

**2. TestNG Integration (Week 3):**
```java
@Test(priority = 1, groups = "smoke")
public void testGoogleSearch() {
    // Your test code
    Assert.assertEquals(title, "Google", "Homepage title mismatch");
}
```

**3. Data-Driven Testing (Week 3):**
```java
@Test(dataProvider = "searchQueries")
public void testSearch(String query, String expectedResult) {
    // Test with different search queries from Excel file
}
```

**4. CI/CD Integration (Week 4):**
```xml
<!-- Jenkins runs this via Maven -->
<execution>
    <goals>
        <goal>test</goal>
    </goals>
</execution>
```

**What You're Learning:**
- Today: Basic WebDriver mechanics
- Week 2: Advanced Selenium techniques
- Week 3: Enterprise test design patterns
- Week 4: Complete production framework

**This simple test is the foundation** of million-dollar test automation frameworks at companies like Google, Amazon, and Netflix!

---

## 🎯 Project Completion Checklist

**Before Moving to Day 2:**
- [ ] Project builds without errors (`mvn clean install`)
- [ ] Test runs successfully and prints "PASSED"
- [ ] Browser opens, performs search, closes properly
- [ ] No zombie Chrome processes after test
- [ ] Code is well-commented
- [ ] You understand every line of code
- [ ] You can explain what each Selenium method does
- [ ] You've tried at least one enhancement

**Optional - Push to GitHub:**
```bash
cd google-search-automation
git init
git add .
git commit -m "Day 1 Project: Google Search Automation"
git branch -M main
git remote add origin <your-repo-url>
git push -u origin main
```

---

## 🎉 Congratulations!

You've built your first complete Selenium automation test! This is a huge milestone.

**What You've Accomplished:**
✅ Set up a Maven Selenium project from scratch
✅ Wrote 50+ lines of automation code
✅ Controlled a browser programmatically
✅ Interacted with web elements
✅ Verified test outcomes
✅ Handled exceptions properly
✅ Created a reusable test structure

**This Test Shows You Can:**
- Configure Selenium projects (setup skill)
- Navigate websites programmatically (navigation skill)
- Locate and interact with elements (interaction skill)
- Verify expected outcomes (testing skill)
- Handle errors gracefully (debugging skill)

**Tomorrow's Preview:**
Day 2 - Locators Mastery: You'll learn 8 different ways to find elements on a page. Today you used `By.name("q")` - tomorrow you'll master all locator strategies!

**Keep building! 🚀**
