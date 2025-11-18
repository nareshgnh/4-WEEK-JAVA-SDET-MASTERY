# DAY 1 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Set up your first Selenium WebDriver project and write automated tests that control a real browser. By completing these exercises, you'll master project setup, basic navigation, and WebDriver lifecycle management.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Create Maven Project & Add Selenium Dependency
**Objective:** Understand Maven project structure and dependency management

**Task:**
1. Create a new Maven project in IntelliJ IDEA
2. Add Selenium dependency to `pom.xml`
3. Verify dependencies are downloaded

**Step-by-Step Instructions:**

**In IntelliJ IDEA:**
1. File → New → Project
2. Select "Maven" (left sidebar)
3. Project Name: `selenium-day1-practice`
4. GroupId: `com.sdet.practice`
5. ArtifactId: `selenium-day1-practice`
6. Click "Create"

**Modify pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sdet.practice</groupId>
    <artifactId>selenium-day1-practice</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- TODO: Add Selenium dependency here -->
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <version>4.15.0</version>
        </dependency>
    </dependencies>
</project>
```

**Expected Output:**
- IntelliJ shows "Importing..." in the bottom right
- `External Libraries` folder shows selenium-java and dependencies
- No red underlines in pom.xml

**Verification:**
```bash
# In IntelliJ Terminal:
mvn clean install
# Should say "BUILD SUCCESS"
```

**Hints:**
- Right-click pom.xml → Maven → Reload Project if dependencies don't load
- Check internet connection if downloads fail
- Look for `.m2/repository/org/seleniumhq/selenium/` folder in your home directory

---

### Exercise 2: First WebDriver Test - Open Browser
**Objective:** Write your first Selenium test to open a browser

**Task:**
Create a Java class that opens Chrome browser, prints a message, and closes it.

**Starter Code:**
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise2_FirstTest {
    public static void main(String[] args) {
        // TODO: Create ChromeDriver instance

        // TODO: Print "Browser opened successfully"

        // TODO: Wait 3 seconds (Thread.sleep(3000))

        // TODO: Close browser with driver.quit()
    }
}
```

**Expected Output:**
```
Browser opened successfully
(Chrome browser opens for 3 seconds, then closes)
```

**Hints:**
- Create the class in `src/test/java/com/sdet/practice/` directory
- Use `WebDriver driver = new ChromeDriver();`
- Wrap in try-catch for Thread.sleep()
- Use try-finally to ensure quit() is called

---

### Exercise 3: Navigate to a Website
**Objective:** Learn to navigate to websites using driver.get()

**Task:**
Write a test that:
1. Opens Chrome browser
2. Navigates to https://www.google.com
3. Prints the current URL
4. Prints the page title
5. Closes the browser

**Starter Code:**
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise3_Navigation {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // TODO: Navigate to https://www.google.com

            // TODO: Get and print current URL using driver.getCurrentUrl()

            // TODO: Get and print page title using driver.getTitle()

        } finally {
            // TODO: Close browser
        }
    }
}
```

**Expected Output:**
```
Current URL: https://www.google.com/
Page Title: Google
```

**Hints:**
- `driver.get(url)` - Navigate to URL
- `driver.getCurrentUrl()` - Returns String of current URL
- `driver.getTitle()` - Returns String of page title
- Always use try-finally for proper cleanup

---

## 💪 Core Practice (30 min)

### Exercise 4: Multi-Site Navigation Test
**Objective:** Navigate between multiple websites and verify page titles

**Problem Statement:**
Create a test that visits 3 different websites, verifies each page title, and prints a report.

**Requirements:**
- Navigate to Google, Amazon, and Wikipedia
- For each site, verify the title contains expected text
- Print "PASS" or "FAIL" for each verification
- Use proper exception handling

**Starter Code:**
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise4_MultiSite {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // TODO: Navigate to https://www.google.com
            // TODO: Verify title contains "Google"

            // TODO: Navigate to https://www.amazon.com
            // TODO: Verify title contains "Amazon"

            // TODO: Navigate to https://www.wikipedia.org
            // TODO: Verify title contains "Wikipedia"

        } finally {
            driver.quit();
        }
    }

    // Helper method
    public static void verifyTitle(WebDriver driver, String expectedTitlePart, String siteName) {
        // TODO: Get actual title
        // TODO: Check if it contains expected text
        // TODO: Print PASS or FAIL
    }
}
```

**Test Cases:**
```
Test Case 1:
URL: https://www.google.com
Expected: Title contains "Google"
Result: PASS if contains, FAIL otherwise

Test Case 2:
URL: https://www.amazon.com
Expected: Title contains "Amazon"
Result: PASS if contains, FAIL otherwise

Test Case 3:
URL: https://www.wikipedia.org
Expected: Title contains "Wikipedia"
Result: PASS if contains, FAIL otherwise
```

**Hints:**
- Use `String.contains()` method: `title.contains("Google")`
- Create a helper method to avoid code duplication
- Use if-else to print PASS/FAIL

---

### Exercise 5: Browser Configuration with ChromeOptions
**Objective:** Learn to configure browser behavior using ChromeOptions

**Problem Statement:**
Create tests with different ChromeOptions configurations:
1. Headless mode (browser runs without UI)
2. Maximized window
3. Incognito mode
4. Disable notifications

**Starter Code:**
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

public class Exercise5_ChromeOptions {

    public static void testHeadless() {
        ChromeOptions options = new ChromeOptions();
        // TODO: Add headless argument

        WebDriver driver = new ChromeDriver(options);
        try {
            driver.get("https://www.google.com");
            System.out.println("Headless test - Title: " + driver.getTitle());
        } finally {
            driver.quit();
        }
    }

    public static void testMaximized() {
        ChromeOptions options = new ChromeOptions();
        // TODO: Add start-maximized argument

        WebDriver driver = new ChromeDriver(options);
        try {
            driver.get("https://www.google.com");
            // TODO: Get window size and print it
            System.out.println("Window size: " + /* TODO */);
        } finally {
            driver.quit();
        }
    }

    public static void testIncognito() {
        ChromeOptions options = new ChromeOptions();
        // TODO: Add incognito argument

        WebDriver driver = new ChromeDriver(options);
        try {
            driver.get("https://www.google.com");
            System.out.println("Incognito test completed");
        } finally {
            driver.quit();
        }
    }

    public static void main(String[] args) {
        System.out.println("=== Testing Different Chrome Options ===");
        testHeadless();
        testMaximized();
        testIncognito();
    }
}
```

**Test Cases:**
```
Test 1: Headless Mode
Expected: Browser runs without visible window, prints title
Verification: No browser window appears

Test 2: Maximized Window
Expected: Browser opens maximized
Verification: Window dimensions match screen size

Test 3: Incognito Mode
Expected: Browser opens in incognito/private mode
Verification: Browser address bar shows incognito icon
```

**Hints:**
- `options.addArguments("--headless")` for headless mode
- `options.addArguments("--start-maximized")` for maximized window
- `options.addArguments("--incognito")` for incognito mode
- `driver.manage().window().getSize()` returns window dimensions

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Google Search Automation (Today's Mini Project)
**Objective:** Build your first real automation test - automate Google search

**Requirements:**
- Open Google homepage
- Verify Google logo is present (we'll just verify title for now)
- Perform a search for "Selenium WebDriver"
- Verify search results page title contains "Selenium WebDriver"
- Maximize browser window
- Take it slow - add 2-second pauses between steps to see automation in action
- Proper exception handling and browser cleanup

**Starter Code:**
```java
package com.sdet.practice;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise6_GoogleSearch {
    public static void main(String[] args) {
        // TODO: Create WebDriver instance

        try {
            // TODO: Maximize window

            // TODO: Navigate to https://www.google.com

            // TODO: Verify title is "Google"

            // TODO: Find search box by name "q"

            // TODO: Type "Selenium WebDriver" in search box

            // TODO: Submit the search (press Enter)

            // TODO: Wait 2 seconds to see results

            // TODO: Verify results page title contains "Selenium WebDriver"

            // TODO: Print "Google Search Automation - PASSED"

        } catch (Exception e) {
            System.out.println("Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // TODO: Close browser
        }
    }
}
```

**Testing Instructions:**
1. Run the program
2. Watch Chrome open and perform search automatically
3. Verify console output shows "PASSED"
4. Verify browser closes properly

**Expected Console Output:**
```
Homepage title: Google
Search box found
Text entered: Selenium WebDriver
Search submitted
Results page title: Selenium WebDriver - Google Search
Google Search Automation - PASSED
```

**Bonus Challenges:**
- [ ] Add a method to take screenshot of results page
- [ ] Count how many search results are displayed
- [ ] Click the first search result
- [ ] Navigate back and verify you're on search results page

**Hints:**
- Use `driver.findElement(By.name("q"))` to find search box
- Use `.sendKeys("text")` to type in input field
- Use `.submit()` to submit the form (same as pressing Enter)
- Use `Thread.sleep(2000)` to pause (we'll learn better waits later)

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
**Complete pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.sdet.practice</groupId>
    <artifactId>selenium-day1-practice</artifactId>
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

**Explanation:**
- `modelVersion`: Maven POM version (always 4.0.0)
- `groupId`: Usually your company domain reversed (com.company)
- `artifactId`: Project name (becomes JAR file name)
- `version`: Your project version (1.0-SNAPSHOT means development version)
- `properties`: Java version and encoding settings
- `dependencies`: External libraries (Selenium in this case)

**Key Takeaways:**
- Maven Central Repository automatically downloads dependencies
- Transitive dependencies (libraries that Selenium needs) are also downloaded
- `pom.xml` is the heart of Maven project - handle with care!

---

### Exercise 2 Solution
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise2_FirstTest {
    public static void main(String[] args) {
        // Create ChromeDriver instance - opens browser
        WebDriver driver = new ChromeDriver();

        try {
            // Print success message
            System.out.println("Browser opened successfully");

            // Wait 3 seconds so you can see the browser
            Thread.sleep(3000);

        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            // Close browser - ALWAYS in finally block
            driver.quit();
            System.out.println("Browser closed");
        }
    }
}
```

**Explanation:**
1. `WebDriver driver = new ChromeDriver()` - Creates new Chrome browser session
2. `Thread.sleep(3000)` - Pauses for 3 seconds (3000 milliseconds)
3. `driver.quit()` - Closes all windows and ends WebDriver session
4. `try-finally` - Ensures quit() runs even if exception occurs

**Key Takeaways:**
- Browser opens the moment you create `new ChromeDriver()`
- Without `driver.quit()`, browser process stays running (memory leak)
- `finally` block ensures cleanup code always executes
- Selenium 4 automatically manages ChromeDriver (no manual download needed)

---

### Exercise 3 Solution
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise3_Navigation {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Navigate to Google
            driver.get("https://www.google.com");

            // Get and print current URL
            String currentUrl = driver.getCurrentUrl();
            System.out.println("Current URL: " + currentUrl);

            // Get and print page title
            String pageTitle = driver.getTitle();
            System.out.println("Page Title: " + pageTitle);

        } finally {
            // Close browser
            driver.quit();
        }
    }
}
```

**Explanation:**
- `driver.get(url)` - Navigates to specified URL, waits for page load
- `driver.getCurrentUrl()` - Returns String of current page URL
- `driver.getTitle()` - Returns String of page title (from `<title>` tag)
- No need for Thread.sleep() because `get()` waits for page load automatically

**Key Takeaways:**
- `get()` method blocks (waits) until page load event fires
- Always store return values in variables (String url, String title)
- Print statements help debug - you'll remove these in real tests

---

### Exercise 4 Solution
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise4_MultiSite {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Test Google
            driver.get("https://www.google.com");
            verifyTitle(driver, "Google", "Google Homepage");

            // Test Amazon
            driver.get("https://www.amazon.com");
            verifyTitle(driver, "Amazon", "Amazon Homepage");

            // Test Wikipedia
            driver.get("https://www.wikipedia.org");
            verifyTitle(driver, "Wikipedia", "Wikipedia Homepage");

        } finally {
            driver.quit();
        }
    }

    /**
     * Helper method to verify page title contains expected text
     * @param driver WebDriver instance
     * @param expectedTitlePart Text that should be in title
     * @param siteName Name of site being tested (for reporting)
     */
    public static void verifyTitle(WebDriver driver, String expectedTitlePart, String siteName) {
        String actualTitle = driver.getTitle();

        if (actualTitle.contains(expectedTitlePart)) {
            System.out.println("✓ PASS: " + siteName + " - Title: " + actualTitle);
        } else {
            System.out.println("✗ FAIL: " + siteName);
            System.out.println("  Expected title to contain: " + expectedTitlePart);
            System.out.println("  Actual title: " + actualTitle);
        }
    }
}
```

**Explanation:**
- Helper method reduces code duplication (DRY principle - Don't Repeat Yourself)
- `contains()` is more flexible than exact match (titles can vary)
- Clear PASS/FAIL reporting makes test results obvious
- JavaDoc comment explains method parameters

**Key Takeaways:**
- Extract repeated logic into helper methods
- Use descriptive parameter names
- Add JavaDoc comments for reusable methods
- This pattern scales to hundreds of tests

---

### Exercise 5 Solution
```java
package com.sdet.practice;

import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.chrome.ChromeOptions;

public class Exercise5_ChromeOptions {

    public static void testHeadless() {
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--headless"); // Run without UI
        options.addArguments("--disable-gpu"); // Recommended with headless

        WebDriver driver = new ChromeDriver(options);
        try {
            driver.get("https://www.google.com");
            System.out.println("Headless test - Title: " + driver.getTitle());
        } finally {
            driver.quit();
        }
    }

    public static void testMaximized() {
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--start-maximized");

        WebDriver driver = new ChromeDriver(options);
        try {
            driver.get("https://www.google.com");

            // Get window size
            int width = driver.manage().window().getSize().getWidth();
            int height = driver.manage().window().getSize().getHeight();
            System.out.println("Window size: " + width + " x " + height);

        } finally {
            driver.quit();
        }
    }

    public static void testIncognito() {
        ChromeOptions options = new ChromeOptions();
        options.addArguments("--incognito");

        WebDriver driver = new ChromeDriver(options);
        try {
            driver.get("https://www.google.com");
            System.out.println("Incognito test completed");
            Thread.sleep(2000); // Pause to see incognito icon
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }

    public static void main(String[] args) {
        System.out.println("=== Testing Different Chrome Options ===\n");

        testHeadless();
        System.out.println();

        testMaximized();
        System.out.println();

        testIncognito();
    }
}
```

**Explanation:**
- `ChromeOptions` configures browser before launch
- `addArguments()` passes command-line flags to Chrome
- Common arguments:
  - `--headless` - No browser UI (faster, CI/CD friendly)
  - `--start-maximized` - Opens maximized window
  - `--incognito` - Private browsing mode
  - `--disable-notifications` - Block notification popups
  - `--disable-gpu` - Required for headless on Windows

**Key Takeaways:**
- ChromeOptions is powerful for customizing browser behavior
- Headless mode is essential for CI/CD pipelines
- Different options for different test scenarios
- Options are set BEFORE creating ChromeDriver instance

---

### Exercise 6 Solution (Mini Project)
```java
package com.sdet.practice;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise6_GoogleSearch {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            // Maximize browser window
            driver.manage().window().maximize();
            System.out.println("Browser maximized");

            // Navigate to Google
            driver.get("https://www.google.com");
            Thread.sleep(1000); // Pause to see navigation

            // Verify homepage title
            String homeTitle = driver.getTitle();
            System.out.println("Homepage title: " + homeTitle);
            if (!homeTitle.equals("Google")) {
                throw new Exception("Homepage title verification failed!");
            }

            // Find search box by name attribute
            WebElement searchBox = driver.findElement(By.name("q"));
            System.out.println("Search box found");
            Thread.sleep(1000);

            // Type search query
            String searchQuery = "Selenium WebDriver";
            searchBox.sendKeys(searchQuery);
            System.out.println("Text entered: " + searchQuery);
            Thread.sleep(1000);

            // Submit the search form
            searchBox.submit();
            System.out.println("Search submitted");
            Thread.sleep(2000); // Wait for results page

            // Verify results page title
            String resultsTitle = driver.getTitle();
            System.out.println("Results page title: " + resultsTitle);

            if (resultsTitle.contains(searchQuery)) {
                System.out.println("\n✓✓✓ Google Search Automation - PASSED ✓✓✓");
            } else {
                System.out.println("\n✗✗✗ Google Search Automation - FAILED ✗✗✗");
                System.out.println("Expected title to contain: " + searchQuery);
                System.out.println("Actual title: " + resultsTitle);
            }

        } catch (Exception e) {
            System.out.println("\n✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // Close browser
            driver.quit();
            System.out.println("\nBrowser closed");
        }
    }
}
```

**Explanation:**
Step-by-step breakdown:
1. **Create driver & maximize** - Start with full window
2. **Navigate to Google** - driver.get() waits for page load
3. **Verify title** - Basic homepage check
4. **Find search box** - Using By.name("q") locator
5. **Type search text** - sendKeys() simulates typing
6. **Submit form** - submit() simulates pressing Enter
7. **Verify results** - Check title contains search query

**Key Takeaways:**
- `findElement()` returns WebElement object
- `By.name("q")` is a locator strategy (we'll learn 8 types on Day 2)
- `sendKeys()` types text into input fields
- `submit()` submits the form (works on any form element)
- Thread.sleep() lets you see automation in action (we'll replace with proper waits later)
- This is a complete end-to-end test!

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Maven Setup | ☐ | __/10 | ___ min | |
| 2 - First Test | ☐ | __/10 | ___ min | |
| 3 - Navigation | ☐ | __/10 | ___ min | |
| 4 - Multi-Site | ☐ | __/10 | ___ min | |
| 5 - ChromeOptions | ☐ | __/10 | ___ min | |
| 6 - Google Search | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
- [Area 1]
- [Area 2]

**Areas I excelled at:**
- [Area 1]
- [Area 2]

**Questions to ask/research:**
- [Question 1]
- [Question 2]

---

## ✅ Answers to Self-Check Questions

**1. What is the role of Maven in a Selenium project?**
Maven manages dependencies (automatically downloads Selenium JARs), provides standardized project structure, handles build lifecycle (compile, test, package), and integrates with CI/CD tools.

**2. Why should you use `WebDriver driver` instead of `ChromeDriver driver`?**
Using the interface (`WebDriver`) instead of the concrete class (`ChromeDriver`) allows for:
- Easy browser switching (change ChromeDriver to FirefoxDriver in one place)
- Better code flexibility and reusability
- Following polymorphism best practices
- Method parameters can accept any WebDriver implementation

**3. What happens if you forget to call `driver.quit()` in your test?**
- Browser process keeps running in background (memory leak)
- Multiple test runs accumulate zombie browser processes
- System runs out of resources
- CI/CD pipelines fail due to too many open browsers
- Always use try-finally to ensure quit() is called

**4. How is Selenium WebDriver different from Selenium IDE?**
- **IDE**: Record-and-playback tool, Chrome/Firefox extension, no coding required, limited flexibility
- **WebDriver**: Programming interface, full control, integrates with test frameworks, industry standard for real projects

**5. What is the purpose of ChromeOptions class?**
ChromeOptions customizes Chrome browser behavior:
- Run headless (no UI)
- Set window size
- Enable/disable features (notifications, popups)
- Add browser extensions
- Set download directory
- Configure proxy settings

---

## 🎉 Congratulations!

You've completed Day 1 practice! You can now:
✅ Set up Selenium projects with Maven
✅ Create WebDriver instances and control browsers
✅ Navigate to websites and verify page properties
✅ Use ChromeOptions to customize browser behavior
✅ Write complete automated tests

**Tomorrow:** Day 2 - Locators Mastery (8 ways to find elements on a page)

**Keep the momentum going! 🚀**
