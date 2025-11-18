# DAY 4 CHEAT SHEET: ADVANCED INTERACTIONS

## ⚡ Actions Class

```java
import org.openqa.selenium.interactions.Actions;

Actions actions = new Actions(driver);

// Mouse hover
actions.moveToElement(element).perform();

// Right click
actions.contextClick(element).perform();

// Double click
actions.doubleClick(element).perform();

// Drag and drop
actions.dragAndDrop(source, target).perform();

// Keyboard combinations
actions.keyDown(Keys.CONTROL).sendKeys("a").keyUp(Keys.CONTROL).perform();  // Ctrl+A
```

**⚠️ CRITICAL: Always call .perform()!**

---

## 🚨 JavaScript Alerts

```java
import org.openqa.selenium.Alert;

// Wait for alert
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
Alert alert = wait.until(ExpectedConditions.alertIsPresent());

// Get text
String text = alert.getText();

// Accept (OK)
alert.accept();

// Dismiss (Cancel)
alert.dismiss();

// Enter text (for prompts)
alert.sendKeys("input text");
alert.accept();
```

---

## 🖼️ iframes

```java
// Switch to iframe
driver.switchTo().frame(0);           // By index
driver.switchTo().frame("frameName"); // By name/ID
driver.switchTo().frame(element);     // By WebElement

// Work with elements inside iframe
driver.findElement(By.id("inside-frame")).click();

// Switch back to main page
driver.switchTo().defaultContent();

// Switch to parent (if nested)
driver.switchTo().parentFrame();
```

---

## 🪟 Multiple Windows/Tabs

```java
import org.openqa.selenium.WindowType;

// Get current window handle
String mainWindow = driver.getWindowHandle();

// Open new tab/window
driver.switchTo().newWindow(WindowType.TAB);
driver.switchTo().newWindow(WindowType.WINDOW);

// Get all windows
Set<String> allWindows = driver.getWindowHandles();

// Switch to window
driver.switchTo().window(windowHandle);

// Close current window
driver.close();

// Switch back to main
driver.switchTo().window(mainWindow);
```

---

## 💡 Common Patterns

**Hover and Click Submenu:**
```java
Actions actions = new Actions(driver);
actions.moveToElement(menu).perform();
driver.findElement(By.id("submenu")).click();
```

**Handle Alert Flow:**
```java
driver.findElement(By.id("trigger-alert")).click();
Alert alert = wait.until(ExpectedConditions.alertIsPresent());
alert.accept();
```

**iframe Interaction:**
```java
driver.switchTo().frame("myframe");
driver.findElement(By.id("inside")).click();
driver.switchTo().defaultContent();
```

**Window Management:**
```java
String main = driver.getWindowHandle();
// ... new window opens
for (String handle : driver.getWindowHandles()) {
    if (!handle.equals(main)) {
        driver.switchTo().window(handle);
        break;
    }
}
// Work in new window
driver.close();
driver.switchTo().window(main);
```

---

## 📌 Remember This

> **Actions require .perform() to execute. Switching to iframe/window/alert is required before interacting with elements inside them.**

---

**Day 4 Complete! Tomorrow: WebElements Deep Dive 🚀**
