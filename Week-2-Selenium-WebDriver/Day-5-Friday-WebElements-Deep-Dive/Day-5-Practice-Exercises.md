# DAY 5 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master findElement vs findElements, handle dynamic content, work with JavaScript Executor, and manage element collections efficiently.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Count Elements on Page
**Objective:** Practice findElements() to count matching elements

**Task:**
Navigate to https://www.google.com and count all links, images, and buttons.

**Starter Code:**
```java
package com.sdet.webelements;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import java.util.List;

public class Exercise1_CountElements {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://www.google.com");
            Thread.sleep(2000);

            // TODO: Find all links (anchor tags)
            List<WebElement> links = driver.findElements(By.tagName("a"));
            System.out.println("Total links: " + links.size());

            // TODO: Find all images
            List<WebElement> images = driver.findElements(By.tagName("img"));
            System.out.println("Total images: " + images.size());

            // TODO: Find all buttons
            List<WebElement> buttons = driver.findElements(By.tagName("button"));
            System.out.println("Total buttons: " + buttons.size());

            // TODO: Find all inputs
            List<WebElement> inputs = driver.findElements(By.tagName("input"));
            System.out.println("Total inputs: " + inputs.size());

            System.out.println("\n✓ Element counting complete");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
Total links: 25
Total images: 3
Total buttons: 5
Total inputs: 10
✓ Element counting complete
```

**Hints:**
- `findElements()` returns List<WebElement>
- Returns empty list if no matches (doesn't throw exception)
- Use `.size()` to get count

---

### Exercise 2: Iterate Through Elements
**Objective:** Loop through element collections and extract data

**Starter Code:**
```java
public class Exercise2_IterateElements {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/");
            Thread.sleep(1000);

            // TODO: Find all links on the page
            List<WebElement> links = driver.findElements(By.tagName("a"));

            System.out.println("=== All Links on Page ===\n");

            // TODO: Loop through each link
            for (WebElement link : links) {
                String text = link.getText();
                String href = link.getAttribute("href");

                // Only print links with visible text
                if (text.length() > 0) {
                    System.out.println("Text: " + text);
                    System.out.println("URL: " + href);
                    System.out.println("---");
                }
            }

            System.out.println("\n✓ Extracted " + links.size() + " links");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
=== All Links on Page ===

Text: A/B Testing
URL: https://the-internet.herokuapp.com/abtest
---
Text: Add/Remove Elements
URL: https://the-internet.herokuapp.com/add_remove_elements/
---
...
```

---

### Exercise 3: Check Element Existence Without Exception
**Objective:** Use findElements() to check if element exists

**Starter Code:**
```java
public class Exercise3_CheckExistence {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://the-internet.herokuapp.com/");

            // TODO: Check if element exists using findElements
            List<WebElement> searchBox = driver.findElements(By.id("search-box"));

            if (!searchBox.isEmpty()) {
                System.out.println("✓ Search box exists");
                searchBox.get(0).sendKeys("test");
            } else {
                System.out.println("✗ Search box not found");
            }

            // TODO: Check another element
            List<WebElement> loginButton = driver.findElements(By.id("login-btn"));

            if (loginButton.isEmpty()) {
                System.out.println("✗ Login button not found (expected)");
            } else {
                System.out.println("✓ Login button exists");
            }

            System.out.println("\n✓ Existence checks complete");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Key Pattern:**
```java
// Instead of try-catch with findElement():
try {
    driver.findElement(By.id("optional")).click();
} catch (NoSuchElementException e) {
    // Handle
}

// Use findElements():
List<WebElement> elements = driver.findElements(By.id("optional"));
if (!elements.isEmpty()) {
    elements.get(0).click();
}
```

---

## 💪 Core Practice (30 min)

### Exercise 4: Handle Stale Element Reference
**Objective:** Learn to handle and prevent stale element exceptions

**Problem Statement:**
Navigate to dynamic content page, interact with elements that reload, and handle stale references.

**Starter Code:**
```java
import org.openqa.selenium.support.ui.WebDriverWait;
import org.openqa.selenium.support.ui.ExpectedConditions;
import java.time.Duration;

public class Exercise4_StaleElements {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/dynamic_loading/1");

            // TODO: Find and click start button
            WebElement startButton = driver.findElement(By.cssSelector("#start button"));
            startButton.click();
            System.out.println("✓ Clicked start button");

            // TODO: Wait for loading indicator to disappear
            wait.until(ExpectedConditions.invisibilityOfElementLocated(By.id("loading")));
            System.out.println("✓ Loading complete");

            // TODO: Re-find the finish element (it was hidden, now visible)
            WebElement finishText = wait.until(
                ExpectedConditions.visibilityOfElementLocated(By.id("finish"))
            );
            System.out.println("✓ Finish text: " + finishText.getText());

            // Demonstrate stale element
            System.out.println("\n--- Demonstrating Stale Element ---");
            driver.navigate().refresh();

            // startButton is now stale!
            // Re-find it
            startButton = wait.until(
                ExpectedConditions.presenceOfElementLocated(By.cssSelector("#start button"))
            );
            System.out.println("✓ Re-found element after refresh");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Test Cases:**
```
Test 1: Click element, wait for content to change
Expected: Element interaction works

Test 2: Refresh page
Expected: Old element reference becomes stale

Test 3: Re-find element after refresh
Expected: New element reference works
```

---

### Exercise 5: JavaScript Executor for Scrolling
**Objective:** Use JavaScript Executor to scroll and interact with elements

**Starter Code:**
```java
import org.openqa.selenium.JavascriptExecutor;

public class Exercise5_JavaScriptExecutor {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        JavascriptExecutor js = (JavascriptExecutor) driver;

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/infinite_scroll");

            System.out.println("=== JavaScript Executor Demo ===\n");

            // TODO: Scroll down 5 times using JavaScript
            for (int i = 1; i <= 5; i++) {
                js.executeScript("window.scrollTo(0, document.body.scrollHeight)");
                System.out.println("✓ Scroll " + i + " completed");
                Thread.sleep(1000);
            }

            // TODO: Scroll to top
            js.executeScript("window.scrollTo(0, 0)");
            System.out.println("✓ Scrolled to top");
            Thread.sleep(1000);

            // TODO: Get page height using JavaScript
            Long pageHeight = (Long) js.executeScript("return document.body.scrollHeight");
            System.out.println("\nPage height: " + pageHeight + " pixels");

            // TODO: Get current scroll position
            Long scrollPosition = (Long) js.executeScript("return window.pageYOffset");
            System.out.println("Current scroll position: " + scrollPosition);

            System.out.println("\n✓ JavaScript execution complete");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
=== JavaScript Executor Demo ===

✓ Scroll 1 completed
✓ Scroll 2 completed
✓ Scroll 3 completed
✓ Scroll 4 completed
✓ Scroll 5 completed
✓ Scrolled to top

Page height: 3500 pixels
Current scroll position: 0

✓ JavaScript execution complete
```

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Dynamic Web Table Data Extraction
**Objective:** Extract all data from a web table using findElements

**Requirements:**
- Navigate to table page
- Find all table rows
- Extract data from each cell
- Store in data structure
- Print formatted output
- Handle dynamic content

**Starter Code:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

public class Exercise6_TableDataExtraction {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/tables");

            System.out.println("=== TABLE DATA EXTRACTION ===\n");

            // TODO: Find table element
            WebElement table = driver.findElement(By.id("table1"));

            // TODO: Find all rows (excluding header)
            List<WebElement> rows = table.findElements(By.xpath(".//tbody/tr"));
            System.out.println("Total rows: " + rows.size());

            // TODO: Extract data from each row
            for (int i = 0; i < rows.size(); i++) {
                WebElement row = rows.get(i);

                // Find all cells in this row
                List<WebElement> cells = row.findElements(By.tagName("td"));

                String lastName = cells.get(0).getText();
                String firstName = cells.get(1).getText();
                String email = cells.get(2).getText();
                String due = cells.get(3).getText();
                String website = cells.get(4).getText();

                System.out.println("\nRow " + (i + 1) + ":");
                System.out.println("  Name: " + firstName + " " + lastName);
                System.out.println("  Email: " + email);
                System.out.println("  Due: $" + due);
                System.out.println("  Website: " + website);
            }

            // TODO: Find specific data using XPath
            String specificEmail = driver.findElement(
                By.xpath("//table[@id='table1']//td[contains(text(), 'Frank')]/../td[3]")
            ).getText();
            System.out.println("\n✓ Frank's email: " + specificEmail);

            System.out.println("\n✓ Table extraction complete");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Testing Instructions:**
1. Run the program
2. Verify all rows extracted
3. Check data formatting
4. Verify specific data query works

**Bonus Challenges:**
- [ ] Sort table data by last name
- [ ] Calculate total "due" amount
- [ ] Find row with specific email
- [ ] Extract header row separately
- [ ] Handle table with dynamic row count

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
// Key points:
// - findElements() returns List<WebElement>
// - Empty list if no matches (no exception)
// - Use .size() to count
```

### Exercise 4 Complete Solution
```java
// Stale element handling pattern:
public WebElement getElementWithRetry(By locator, int maxRetries) {
    for (int i = 0; i < maxRetries; i++) {
        try {
            return driver.findElement(locator);
        } catch (StaleElementReferenceException e) {
            System.out.println("Stale element, retry " + (i + 1));
            try {
                Thread.sleep(500);
            } catch (InterruptedException ie) {
                // Ignore
            }
        }
    }
    throw new RuntimeException("Element is stale after " + maxRetries + " retries");
}
```

### Exercise 5 JavaScript Executor Patterns
```java
// Common JavaScript operations:

// 1. Scroll to element
js.executeScript("arguments[0].scrollIntoView(true);", element);

// 2. Click (bypass overlays)
js.executeScript("arguments[0].click();", element);

// 3. Change element value
js.executeScript("arguments[0].value='new value';", element);

// 4. Get attribute
String value = (String) js.executeScript("return arguments[0].getAttribute('value');", element);

// 5. Highlight element (debugging)
js.executeScript("arguments[0].style.border='3px solid red'", element);
```

---

## ✅ Answers to Self-Check Questions

**1. What does findElements() return if no elements match?**
Empty List<WebElement> - it does NOT throw NoSuchElementException like findElement()

**2. How do you count how many buttons are on a page?**
```java
int buttonCount = driver.findElements(By.tagName("button")).size();
```

**3. What causes StaleElementReferenceException?**
- Page refresh
- Navigation to new page
- DOM update/re-render
- Element removed and re-added to DOM

**4. How do you scroll to an element using JavaScript?**
```java
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].scrollIntoView(true);", element);
```

**5. How do you check if element is visible?**
```java
boolean isVisible = element.isDisplayed();

// Or with safety check:
List<WebElement> elements = driver.findElements(By.id("optional"));
if (!elements.isEmpty()) {
    boolean visible = elements.get(0).isDisplayed();
}
```

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Count Elements | ☐ | __/10 | ___ min | |
| 2 - Iterate Elements | ☐ | __/10 | ___ min | |
| 3 - Check Existence | ☐ | __/10 | ___ min | |
| 4 - Stale Elements | ☐ | __/10 | ___ min | |
| 5 - JavaScript Executor | ☐ | __/10 | ___ min | |
| 6 - Table Extraction | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

---

**Excellent work! Tomorrow: Page Object Model (framework design) 🚀**
