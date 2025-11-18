# DAY 4 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master advanced interactions: Actions class for complex gestures, handling alerts/prompts, switching between iframes and windows, and performing drag-and-drop operations.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Mouse Hover and Click Submenu
**Objective:** Practice Actions class for hover interactions

**Task:**
Navigate to https://the-internet.herokuapp.com/hovers and hover over images to reveal information.

**Starter Code:**
```java
package com.sdet.advanced;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.interactions.Actions;

public class Exercise1_MouseHover {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/hovers");
            Thread.sleep(2000);

            // Create Actions object
            Actions actions = new Actions(driver);

            // TODO: Find first image
            WebElement image1 = driver.findElement(By.xpath("(//img[@alt='User Avatar'])[1]"));

            // TODO: Hover over image
            actions.moveToElement(image1).perform();
            System.out.println("✓ Hovered over first image");
            Thread.sleep(2000);

            // TODO: Get text that appeared
            WebElement caption = driver.findElement(By.xpath("//h5[text()='name: user1']"));
            System.out.println("✓ Caption text: " + caption.getText());

            // TODO: Hover over second image
            WebElement image2 = driver.findElement(By.xpath("(//img[@alt='User Avatar'])[2]"));
            actions.moveToElement(image2).perform();
            System.out.println("✓ Hovered over second image");
            Thread.sleep(2000);

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 2: JavaScript Alert Handling
**Objective:** Practice accepting, dismissing, and entering text in alerts

**Starter Code:**
```java
import org.openqa.selenium.Alert;
import org.openqa.selenium.support.ui.WebDriverWait;
import org.openqa.selenium.support.ui.ExpectedConditions;
import java.time.Duration;

public class Exercise2_AlertHandling {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/javascript_alerts");

            // Test 1: Simple Alert
            driver.findElement(By.xpath("//button[text()='Click for JS Alert']")).click();
            Alert alert1 = wait.until(ExpectedConditions.alertIsPresent());
            System.out.println("Alert text: " + alert1.getText());
            alert1.accept();
            System.out.println("✓ Alert accepted");
            Thread.sleep(1000);

            // Test 2: Confirm Alert (Accept)
            driver.findElement(By.xpath("//button[text()='Click for JS Confirm']")).click();
            Alert confirm = wait.until(ExpectedConditions.alertIsPresent());
            System.out.println("Confirm text: " + confirm.getText());
            confirm.accept();  // Click OK
            System.out.println("✓ Confirm accepted");
            Thread.sleep(1000);

            // Test 3: Confirm Alert (Dismiss)
            driver.findElement(By.xpath("//button[text()='Click for JS Confirm']")).click();
            Alert confirm2 = wait.until(ExpectedConditions.alertIsPresent());
            confirm2.dismiss();  // Click Cancel
            System.out.println("✓ Confirm dismissed");
            Thread.sleep(1000);

            // Test 4: Prompt Alert
            driver.findElement(By.xpath("//button[text()='Click for JS Prompt']")).click();
            Alert prompt = wait.until(ExpectedConditions.alertIsPresent());
            prompt.sendKeys("Selenium Test");
            prompt.accept();
            System.out.println("✓ Prompt text entered and accepted");

            // Verify result
            WebElement result = driver.findElement(By.id("result"));
            System.out.println("Result: " + result.getText());

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 3: iframe Switching
**Objective:** Practice switching into and out of iframes

**Starter Code:**
```java
public class Exercise3_IframeSwitching {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/iframe");
            Thread.sleep(2000);

            // TODO: Switch to iframe (it has id="mce_0_ifr")
            driver.switchTo().frame("mce_0_ifr");
            System.out.println("✓ Switched to iframe");

            // TODO: Find text editor inside iframe and clear it
            WebElement editor = driver.findElement(By.id("tinymce"));
            editor.clear();
            System.out.println("✓ Cleared existing text");

            // TODO: Type new text
            editor.sendKeys("Hello from Selenium inside iframe!");
            System.out.println("✓ Typed text inside iframe");
            Thread.sleep(2000);

            // TODO: Switch back to main page
            driver.switchTo().defaultContent();
            System.out.println("✓ Switched back to main page");

            // TODO: Interact with element on main page
            WebElement heading = driver.findElement(By.tagName("h3"));
            System.out.println("Main page heading: " + heading.getText());

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 💪 Core Practice (30 min)

### Exercise 4: Drag and Drop
**Objective:** Practice drag-and-drop using Actions class

**Starter Code:**
```java
public class Exercise4_DragAndDrop {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/drag_and_drop");
            Thread.sleep(2000);

            // Find source and target elements
            WebElement source = driver.findElement(By.id("column-a"));
            WebElement target = driver.findElement(By.id("column-b"));

            System.out.println("Before drag:");
            System.out.println("Column A header: " + source.getText());
            System.out.println("Column B header: " + target.getText());

            // TODO: Perform drag and drop
            Actions actions = new Actions(driver);
            actions.dragAndDrop(source, target).perform();
            System.out.println("✓ Drag and drop performed");
            Thread.sleep(2000);

            // Verify swap occurred
            System.out.println("\nAfter drag:");
            System.out.println("Column A header: " + source.getText());
            System.out.println("Column B header: " + target.getText());

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 5: Multiple Windows Handling
**Objective:** Practice switching between multiple browser windows

**Starter Code:**
```java
import java.util.Set;

public class Exercise5_MultipleWindows {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://the-internet.herokuapp.com/windows");
            Thread.sleep(1000);

            // TODO: Get main window handle
            String mainWindow = driver.getWindowHandle();
            System.out.println("Main window title: " + driver.getTitle());

            // TODO: Click link that opens new window
            driver.findElement(By.linkText("Click Here")).click();
            System.out.println("✓ Clicked link to open new window");
            Thread.sleep(2000);

            // TODO: Get all window handles
            Set<String> allWindows = driver.getWindowHandles();
            System.out.println("Total windows: " + allWindows.size());

            // TODO: Switch to new window
            for (String window : allWindows) {
                if (!window.equals(mainWindow)) {
                    driver.switchTo().window(window);
                    System.out.println("✓ Switched to new window");
                    break;
                }
            }

            // TODO: Get new window title
            System.out.println("New window title: " + driver.getTitle());
            Thread.sleep(2000);

            // TODO: Close new window
            driver.close();
            System.out.println("✓ Closed new window");

            // TODO: Switch back to main window
            driver.switchTo().window(mainWindow);
            System.out.println("✓ Switched back to main window");
            System.out.println("Main window title: " + driver.getTitle());

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Complex Interactions Project
**Objective:** Combine all advanced interactions in one test

**Requirements:**
- Navigate to https://the-internet.herokuapp.com/
- Practice multiple advanced interactions:
  1. Handle JavaScript alerts
  2. Work with iframes
  3. Manage multiple windows
  4. Perform drag and drop
  5. Use mouse hover
- Proper error handling and reporting

**Starter Code:**
```java
public class Exercise6_ComplexInteractions {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();
        WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        Actions actions = new Actions(driver);

        try {
            driver.manage().window().maximize();
            System.out.println("=== ADVANCED INTERACTIONS TEST ===\n");

            // Part 1: Drag and Drop
            System.out.println("--- Part 1: Drag and Drop ---");
            driver.get("https://the-internet.herokuapp.com/drag_and_drop");
            WebElement columnA = driver.findElement(By.id("column-a"));
            WebElement columnB = driver.findElement(By.id("column-b"));
            actions.dragAndDrop(columnA, columnB).perform();
            System.out.println("✓ Drag and drop completed");
            Thread.sleep(1000);

            // Part 2: JavaScript Alerts
            System.out.println("\n--- Part 2: JavaScript Alerts ---");
            driver.get("https://the-internet.herokuapp.com/javascript_alerts");

            driver.findElement(By.xpath("//button[text()='Click for JS Alert']")).click();
            Alert alert = wait.until(ExpectedConditions.alertIsPresent());
            System.out.println("Alert message: " + alert.getText());
            alert.accept();
            System.out.println("✓ Alert handled");
            Thread.sleep(1000);

            driver.findElement(By.xpath("//button[text()='Click for JS Prompt']")).click();
            Alert prompt = wait.until(ExpectedConditions.alertIsPresent());
            prompt.sendKeys("Advanced Selenium Test");
            prompt.accept();
            System.out.println("✓ Prompt handled with input");
            Thread.sleep(1000);

            // Part 3: iframes
            System.out.println("\n--- Part 3: iframe Interaction ---");
            driver.get("https://the-internet.herokuapp.com/iframe");
            driver.switchTo().frame("mce_0_ifr");
            WebElement editor = driver.findElement(By.id("tinymce"));
            editor.clear();
            editor.sendKeys("Testing iframe interaction");
            System.out.println("✓ iframe text updated");
            driver.switchTo().defaultContent();
            Thread.sleep(1000);

            // Part 4: Multiple Windows
            System.out.println("\n--- Part 4: Multiple Windows ---");
            driver.get("https://the-internet.herokuapp.com/windows");
            String mainWindow = driver.getWindowHandle();

            driver.findElement(By.linkText("Click Here")).click();
            Thread.sleep(1000);

            for (String window : driver.getWindowHandles()) {
                if (!window.equals(mainWindow)) {
                    driver.switchTo().window(window);
                    System.out.println("New window title: " + driver.getTitle());
                    driver.close();
                    break;
                }
            }
            driver.switchTo().window(mainWindow);
            System.out.println("✓ Window switching completed");
            Thread.sleep(1000);

            // Part 5: Mouse Hover
            System.out.println("\n--- Part 5: Mouse Hover ---");
            driver.get("https://the-internet.herokuapp.com/hovers");
            WebElement image = driver.findElement(By.xpath("(//img[@alt='User Avatar'])[1]"));
            actions.moveToElement(image).perform();
            System.out.println("✓ Hover action performed");
            Thread.sleep(1000);

            System.out.println("\n" + "=".repeat(50));
            System.out.println("✓✓✓ ADVANCED INTERACTIONS - PASSED ✓✓✓");
            System.out.println("=".repeat(50));

        } catch (Exception e) {
            System.out.println("\n✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 🎯 Solutions & Key Learnings

### Exercise 1 - Mouse Hover
**Key Learning:** Actions class requires `.perform()` to execute. Without it, nothing happens!

### Exercise 2 - Alerts
**Key Learning:**
- Always wait for alert: `wait.until(ExpectedConditions.alertIsPresent())`
- Three methods: `accept()`, `dismiss()`, `sendKeys()`

### Exercise 3 - iframes
**Key Learning:**
- Must switch to iframe before interacting with elements inside it
- Must switch back to defaultContent() to access main page elements
- Common mistake: Forgetting to switch back causes NoSuchElementException

### Exercise 5 - Multiple Windows
**Key Learning:**
- `getWindowHandle()` - Current window
- `getWindowHandles()` - All windows (Set)
- Use loop to find and switch to new window
- `close()` vs `quit()` - close() closes current window, quit() closes all

---

## ✅ Answers to Self-Check Questions

**1. What happens if you forget to call perform() on Actions?**
Nothing! The action is queued but never executed. Always end with `.perform()`.

**2. How do you accept a JavaScript alert?**
```java
Alert alert = driver.switchTo().alert();  // or wait for it
alert.accept();
```

**3. What's the difference between switchTo().defaultContent() and switchTo().parentFrame()?**
- `defaultContent()` - Switches to main page (top level)
- `parentFrame()` - Switches to immediate parent frame (for nested iframes)

**4. How do you switch to a newly opened tab?**
```java
String mainWindow = driver.getWindowHandle();
Set<String> allWindows = driver.getWindowHandles();
for (String window : allWindows) {
    if (!window.equals(mainWindow)) {
        driver.switchTo().window(window);
        break;
    }
}
```

**5. What's the Actions class method for mouse hover?**
`actions.moveToElement(element).perform()`

---

**Excellent work! Tomorrow: WebElements Deep Dive 🚀**
