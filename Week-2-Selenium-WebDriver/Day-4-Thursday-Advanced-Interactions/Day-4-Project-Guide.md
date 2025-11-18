# DAY 4 MINI PROJECT: COMPLEX UI INTERACTIONS AUTOMATION

## 🎯 Project Overview

**What You're Building:**
A comprehensive test suite demonstrating all advanced interactions: Actions class, alerts, iframes, multiple windows, and complex user gestures.

**Website:** https://the-internet.herokuapp.com/

**Why This Project:**
- Practice all Actions class methods
- Handle real-world UI challenges
- Master context switching (iframes, windows, alerts)
- Build production-ready interaction patterns

**Time Required:** 30-45 minutes

---

## 📋 Requirements

### Must-Have Features:
1. Drag and drop interaction
2. JavaScript alert/confirm/prompt handling
3. iframe text editing
4. Multiple window management
5. Mouse hover interactions
6. Right-click context menu (if available)
7. Keyboard combinations
8. Proper wait strategies for each interaction
9. Clean error handling
10. Detailed reporting

---

## 🎓 Complete Solution

```java
package com.automation.projects;

import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.interactions.Actions;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;
import java.time.Duration;
import java.util.Set;

public class ComplexUIInteractionsProject {

    private static WebDriver driver;
    private static WebDriverWait wait;
    private static Actions actions;

    public static void main(String[] args) {
        setupDriver();

        try {
            System.out.println("=== COMPLEX UI INTERACTIONS TEST SUITE ===\n");

            testDragAndDrop();
            testJavaScriptAlerts();
            testIframeInteraction();
            testMultipleWindows();
            testMouseHover();
            testKeyboardActions();

            System.out.println("\n" + "=".repeat(60));
            System.out.println("✓✓✓ ALL COMPLEX INTERACTIONS - PASSED ✓✓✓");
            System.out.println("=".repeat(60));
            System.out.println("\nTests Executed:");
            System.out.println("1. Drag and Drop ✓");
            System.out.println("2. JavaScript Alerts (Simple, Confirm, Prompt) ✓");
            System.out.println("3. iframe Text Editing ✓");
            System.out.println("4. Multiple Windows Management ✓");
            System.out.println("5. Mouse Hover ✓");
            System.out.println("6. Keyboard Actions ✓");

        } catch (Exception e) {
            System.out.println("\n✗ Test suite failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            cleanup();
        }
    }

    private static void setupDriver() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));
        wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        actions = new Actions(driver);
        System.out.println("✓ Driver setup complete\n");
    }

    private static void testDragAndDrop() throws InterruptedException {
        System.out.println("--- Test 1: Drag and Drop ---");
        driver.get("https://the-internet.herokuapp.com/drag_and_drop");
        Thread.sleep(1000);

        WebElement columnA = driver.findElement(By.id("column-a"));
        WebElement columnB = driver.findElement(By.id("column-b"));

        String beforeA = columnA.getText();
        String beforeB = columnB.getText();
        System.out.println("Before: Column A = " + beforeA + ", Column B = " + beforeB);

        // Perform drag and drop
        actions.dragAndDrop(columnA, columnB).perform();
        Thread.sleep(1000);

        String afterA = columnA.getText();
        String afterB = columnB.getText();
        System.out.println("After:  Column A = " + afterA + ", Column B = " + afterB);

        if (!beforeA.equals(afterA) && !beforeB.equals(afterB)) {
            System.out.println("✓ Drag and drop successful\n");
        } else {
            throw new AssertionError("Drag and drop failed - elements not swapped");
        }
    }

    private static void testJavaScriptAlerts() throws InterruptedException {
        System.out.println("--- Test 2: JavaScript Alerts ---");
        driver.get("https://the-internet.herokuapp.com/javascript_alerts");
        Thread.sleep(1000);

        // Test 2a: Simple Alert
        driver.findElement(By.xpath("//button[text()='Click for JS Alert']")).click();
        Alert alert = wait.until(ExpectedConditions.alertIsPresent());
        String alertText = alert.getText();
        System.out.println("Simple Alert: " + alertText);
        alert.accept();

        WebElement result = driver.findElement(By.id("result"));
        System.out.println("Result: " + result.getText());
        System.out.println("✓ Simple alert handled");
        Thread.sleep(500);

        // Test 2b: Confirm Alert (Accept)
        driver.findElement(By.xpath("//button[text()='Click for JS Confirm']")).click();
        Alert confirm = wait.until(ExpectedConditions.alertIsPresent());
        System.out.println("Confirm Alert: " + confirm.getText());
        confirm.accept();
        System.out.println("✓ Confirm alert accepted");
        System.out.println("Result: " + result.getText());
        Thread.sleep(500);

        // Test 2c: Confirm Alert (Dismiss)
        driver.findElement(By.xpath("//button[text()='Click for JS Confirm']")).click();
        Alert confirm2 = wait.until(ExpectedConditions.alertIsPresent());
        confirm2.dismiss();
        System.out.println("✓ Confirm alert dismissed");
        System.out.println("Result: " + result.getText());
        Thread.sleep(500);

        // Test 2d: Prompt Alert
        driver.findElement(By.xpath("//button[text()='Click for JS Prompt']")).click();
        Alert prompt = wait.until(ExpectedConditions.alertIsPresent());
        String inputText = "Selenium Advanced Test";
        prompt.sendKeys(inputText);
        prompt.accept();
        System.out.println("✓ Prompt handled with input: " + inputText);
        System.out.println("Result: " + result.getText());
        System.out.println();
    }

    private static void testIframeInteraction() throws InterruptedException {
        System.out.println("--- Test 3: iframe Interaction ---");
        driver.get("https://the-internet.herokuapp.com/iframe");
        Thread.sleep(1000);

        // Get main page heading before switching
        WebElement mainHeading = driver.findElement(By.tagName("h3"));
        System.out.println("Main page heading: " + mainHeading.getText());

        // Switch to iframe
        wait.until(ExpectedConditions.frameToBeAvailableAndSwitchToIt("mce_0_ifr"));
        System.out.println("✓ Switched to iframe");

        // Interact with editor inside iframe
        WebElement editor = driver.findElement(By.id("tinymce"));
        String originalText = editor.getText();
        System.out.println("Original text: " + originalText);

        editor.clear();
        String newText = "Testing iframe interaction with Selenium!";
        editor.sendKeys(newText);
        System.out.println("✓ Text updated in iframe: " + newText);
        Thread.sleep(1000);

        // Switch back to main page
        driver.switchTo().defaultContent();
        System.out.println("✓ Switched back to main page");

        // Verify we can access main page elements again
        mainHeading = driver.findElement(By.tagName("h3"));
        System.out.println("Back on main page: " + mainHeading.getText());
        System.out.println();
    }

    private static void testMultipleWindows() throws InterruptedException {
        System.out.println("--- Test 4: Multiple Windows ---");
        driver.get("https://the-internet.herokuapp.com/windows");
        Thread.sleep(1000);

        // Store main window handle
        String mainWindow = driver.getWindowHandle();
        String mainTitle = driver.getTitle();
        System.out.println("Main window: " + mainTitle);

        // Click to open new window
        driver.findElement(By.linkText("Click Here")).click();
        Thread.sleep(1000);

        // Get all window handles
        Set<String> allWindows = driver.getWindowHandles();
        System.out.println("Total windows open: " + allWindows.size());

        // Switch to new window
        for (String window : allWindows) {
            if (!window.equals(mainWindow)) {
                driver.switchTo().window(window);
                System.out.println("✓ Switched to new window");
                break;
            }
        }

        // Verify new window
        String newWindowTitle = driver.getTitle();
        System.out.println("New window title: " + newWindowTitle);

        WebElement heading = driver.findElement(By.tagName("h3"));
        System.out.println("New window heading: " + heading.getText());

        // Close new window
        driver.close();
        System.out.println("✓ New window closed");

        // Switch back to main window
        driver.switchTo().window(mainWindow);
        System.out.println("✓ Switched back to main window");
        System.out.println("Main window title: " + driver.getTitle());
        System.out.println();
    }

    private static void testMouseHover() throws InterruptedException {
        System.out.println("--- Test 5: Mouse Hover ---");
        driver.get("https://the-internet.herokuapp.com/hovers");
        Thread.sleep(1000);

        // Hover over each image and verify caption appears
        for (int i = 1; i <= 3; i++) {
            WebElement image = driver.findElement(
                By.xpath("(//img[@alt='User Avatar'])[" + i + "]")
            );

            actions.moveToElement(image).perform();
            System.out.println("✓ Hovered over image " + i);

            // Verify caption is visible
            WebElement caption = wait.until(ExpectedConditions.visibilityOfElementLocated(
                By.xpath("(//div[@class='figure'])[" + i + "]//h5")
            ));
            System.out.println("Caption: " + caption.getText());
            Thread.sleep(500);
        }
        System.out.println();
    }

    private static void testKeyboardActions() throws InterruptedException {
        System.out.println("--- Test 6: Keyboard Actions ---");
        driver.get("https://the-internet.herokuapp.com/key_presses");
        Thread.sleep(1000);

        WebElement input = driver.findElement(By.id("target"));

        // Test various key presses
        String[] keys = {"ENTER", "TAB", "ESCAPE", "SPACE", "BACK_SPACE"};

        for (String keyName : keys) {
            Keys key = Keys.valueOf(keyName);
            input.sendKeys(key);

            WebElement result = driver.findElement(By.id("result"));
            String resultText = result.getText();
            System.out.println("Pressed " + keyName + ": " + resultText);
            Thread.sleep(300);
        }

        // Test key combination (Ctrl+A)
        System.out.println("\n✓ Testing key combinations:");
        input.clear();
        input.sendKeys("Hello Selenium");

        // Select all text (Ctrl+A)
        actions.keyDown(Keys.CONTROL)
               .sendKeys("a")
               .keyUp(Keys.CONTROL)
               .perform();
        System.out.println("✓ Ctrl+A performed (Select All)");

        // Type to replace selected text
        input.sendKeys("Replaced Text");
        System.out.println("✓ Text replaced after selection");
        System.out.println();
    }

    private static void cleanup() {
        if (driver != null) {
            driver.quit();
            System.out.println("\n✓ Browser closed");
        }
    }
}
```

---

## 🔍 Key Patterns Demonstrated

### 1. Drag and Drop Pattern
```java
WebElement source = driver.findElement(By.id("draggable"));
WebElement target = driver.findElement(By.id("droppable"));
actions.dragAndDrop(source, target).perform();
```

### 2. Alert Handling Pattern
```java
driver.findElement(By.id("trigger-alert")).click();
Alert alert = wait.until(ExpectedConditions.alertIsPresent());
String text = alert.getText();
alert.accept();  // or dismiss() or sendKeys()
```

### 3. iframe Switching Pattern
```java
// Switch to iframe
driver.switchTo().frame("iframe-id");
// Work with elements
driver.findElement(By.id("inside")).click();
// Switch back
driver.switchTo().defaultContent();
```

### 4. Multiple Windows Pattern
```java
String mainWindow = driver.getWindowHandle();
// ... new window opens
for (String handle : driver.getWindowHandles()) {
    if (!handle.equals(mainWindow)) {
        driver.switchTo().window(handle);
        break;
    }
}
// ... work in new window
driver.close();
driver.switchTo().window(mainWindow);
```

### 5. Mouse Hover Pattern
```java
WebElement element = driver.findElement(By.id("menu"));
actions.moveToElement(element).perform();
// Submenu now visible
driver.findElement(By.id("submenu")).click();
```

---

## 💼 Real-World Applications

**This project teaches:**
1. **Complex Gestures:** Drag-drop, hover, right-click
2. **Context Switching:** iframes, windows, alerts
3. **User Simulation:** Realistic interaction patterns
4. **Error Handling:** Proper waits for each interaction type
5. **Code Organization:** Separate methods for each test type

**In production frameworks:**
- Each interaction type becomes a utility method
- BasePage class contains common interaction patterns
- Page Objects use these utilities
- Tests remain clean and readable

---

## ✅ Project Completion Checklist

- [ ] Drag and drop works correctly
- [ ] All three alert types handled (simple, confirm, prompt)
- [ ] iframe text editing successful
- [ ] Multiple windows managed properly
- [ ] Mouse hover reveals hidden elements
- [ ] Keyboard actions executed
- [ ] All waits are appropriate for interaction type
- [ ] Code is well-organized with methods
- [ ] Error handling in place
- [ ] Console output is clear and informative

---

**Congratulations! You've mastered advanced Selenium interactions! 🎉**

**Tomorrow:** WebElements Deep Dive (findElements, dynamic content, JavaScript Executor)
