# DAY 4: ADVANCED INTERACTIONS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master the Actions class for complex user interactions
- [ ] Handle JavaScript alerts, confirms, and prompts
- [ ] Switch between iframes
- [ ] Manage multiple windows and tabs
- [ ] Perform drag-and-drop operations
- [ ] Handle mouse hover and right-click

**Time Required:** 3 hours  
**Difficulty:** Medium-Advanced  
**Prerequisite:** Days 1-3

---

## 📚 Core Concepts

### Concept 1: Actions Class - Complex User Interactions

**What is it?**
The Actions class simulates complex user interactions that simple click() and sendKeys() cannot handle.

**When to use:**
- Mouse hover (moveToElement)
- Right-click (contextClick)
- Double-click (doubleClick)
- Drag and drop
- Key combinations (Ctrl+A, Ctrl+C)

**Basic Pattern:**
```java
import org.openqa.selenium.interactions.Actions;

Actions actions = new Actions(driver);
actions.moveToElement(element).click().perform();  // Must call perform()!
```

**Mouse Hover:**
```java
WebElement menu = driver.findElement(By.id("menu"));
Actions actions = new Actions(driver);
actions.moveToElement(menu).perform();

// Now submenu is visible, can click it
WebElement submenu = driver.findElement(By.id("submenu"));
submenu.click();
```

**Drag and Drop:**
```java
WebElement source = driver.findElement(By.id("draggable"));
WebElement target = driver.findElement(By.id("droppable"));

Actions actions = new Actions(driver);
actions.dragAndDrop(source, target).perform();

// Or with offset:
actions.dragAndDropBy(source, 100, 200).perform();  // x, y pixels
```

**Right Click:**
```java
WebElement element = driver.findElement(By.id("item"));
Actions actions = new Actions(driver);
actions.contextClick(element).perform();
```

**Double Click:**
```java
WebElement element = driver.findElement(By.id("file"));
Actions actions = new Actions(driver);
actions.doubleClick(element).perform();
```

**Keyboard Actions:**
```java
import org.openqa.selenium.Keys;

// Select all text (Ctrl+A)
actions.keyDown(Keys.CONTROL).sendKeys("a").keyUp(Keys.CONTROL).perform();

// Copy (Ctrl+C)
actions.keyDown(Keys.CONTROL).sendKeys("c").keyUp(Keys.CONTROL).perform();

// Type in CAPS
actions.keyDown(Keys.SHIFT).sendKeys("hello").keyUp(Keys.SHIFT).perform();
```

---

### Concept 2: Handling JavaScript Alerts

**Types of Alerts:**
1. **Alert:** Simple message with OK button
2. **Confirm:** Message with OK and Cancel
3. **Prompt:** Message with text input field

**Switching to Alert:**
```java
import org.openqa.selenium.Alert;

// Wait for alert to appear
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
Alert alert = wait.until(ExpectedConditions.alertIsPresent());

// Get alert text
String alertText = alert.getText();
System.out.println("Alert says: " + alertText);

// Accept alert (click OK)
alert.accept();

// Dismiss alert (click Cancel) - for confirm dialogs
alert.dismiss();

// Enter text in prompt
alert.sendKeys("My input");
alert.accept();
```

**Complete Example:**
```java
// Trigger alert
driver.findElement(By.id("alert-button")).click();

// Handle alert
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
Alert alert = wait.until(ExpectedConditions.alertIsPresent());
System.out.println("Alert: " + alert.getText());
alert.accept();
```

---

### Concept 3: Working with iframes

**What is an iframe?**
An inline frame - a separate HTML document embedded in the current page. Selenium cannot access elements inside iframe without switching to it.

**How to identify iframe:**
```html
<iframe id="myframe" name="frameName" src="page.html"></iframe>
```

**Switch to iframe:**
```java
// By index (0-based)
driver.switchTo().frame(0);

// By name or ID
driver.switchTo().frame("frameName");
driver.switchTo().frame("myframe");

// By WebElement
WebElement iframeElement = driver.findElement(By.id("myframe"));
driver.switchTo().frame(iframeElement);

// Switch back to main page
driver.switchTo().defaultContent();

// Switch to parent frame (if nested frames)
driver.switchTo().parentFrame();
```

**Complete Example:**
```java
// Switch to iframe
driver.switchTo().frame("myframe");

// Now can interact with elements inside iframe
WebElement insideFrame = driver.findElement(By.id("element-in-frame"));
insideFrame.click();

// Switch back to main page
driver.switchTo().defaultContent();

// Now can interact with main page elements again
driver.findElement(By.id("main-page-element")).click();
```

---

### Concept 4: Managing Multiple Windows/Tabs

**Opening New Window:**
```java
// Selenium 4 way (recommended)
driver.switchTo().newWindow(WindowType.TAB);     // New tab
driver.switchTo().newWindow(WindowType.WINDOW);  // New window
```

**Switching Between Windows:**
```java
// Get current window handle
String mainWindow = driver.getWindowHandle();

// Click link that opens new window
driver.findElement(By.linkText("Open New Window")).click();

// Get all window handles
Set<String> allWindows = driver.getWindowHandles();

// Switch to new window
for (String window : allWindows) {
    if (!window.equals(mainWindow)) {
        driver.switchTo().window(window);
        break;
    }
}

// Do something in new window
System.out.println("New window title: " + driver.getTitle());

// Close new window
driver.close();

// Switch back to main window
driver.switchTo().window(mainWindow);
```

---

## 💡 Best Practices

**✅ DO:**
```java
// Always call perform() with Actions
actions.moveToElement(element).perform();

// Wait for alert before accepting
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
Alert alert = wait.until(ExpectedConditions.alertIsPresent());

// Switch back to defaultContent after iframe
driver.switchTo().frame("myframe");
// ... work in iframe
driver.switchTo().defaultContent();

// Store main window handle before opening new windows
String mainWindow = driver.getWindowHandle();
```

**❌ DON'T:**
```java
// Forget to call perform()
actions.moveToElement(element);  // ❌ Nothing happens!

// Try to interact with alert without switching
driver.findElement(By.id("ok-button")).click();  // ❌ Won't work on alert

// Forget to switch back from iframe
driver.switchTo().frame("myframe");
// ... then try to access main page element - will fail!
```

---

## 🎓 Interview Questions

**Q: What's the difference between driver.close() and driver.quit()?**

**A:** 
- `driver.close()` - Closes current window/tab only
- `driver.quit()` - Closes ALL windows/tabs and ends WebDriver session
- After switching to new window, use close() to close it, then switchTo() main window

**Q: How do you handle elements inside an iframe?**

**A:** 
1. Switch to iframe using switchTo().frame()
2. Interact with elements inside iframe
3. Switch back to main content using switchTo().defaultContent()
4. If you don't switch, you'll get NoSuchElementException

---

## ✅ Self-Check Questions

1. What happens if you forget to call perform() on Actions?
2. How do you accept a JavaScript alert?
3. What's the difference between switchTo().defaultContent() and switchTo().parentFrame()?
4. How do you switch to a newly opened tab?
5. What's the Actions class method for mouse hover?

**Answers in Practice Exercises**

---

**Ready for advanced automation! 🎯**
