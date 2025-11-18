# DAY 4 DEBUGGING CHALLENGES

## 🐛 Advanced Interaction Debugging

---

## Challenge 1: Actions Not Executing

**Broken Code:**
```java
Actions actions = new Actions(driver);
WebElement element = driver.findElement(By.id("menu"));
actions.moveToElement(element);  // Nothing happens!
```

**Your Task:** Fix the missing piece

---

**Solution:**

**Bug:** Forgot to call `.perform()`

**Fixed Code:**
```java
Actions actions = new Actions(driver);
WebElement element = driver.findElement(By.id("menu"));
actions.moveToElement(element).perform();  // ✓ Now it works!
```

**Key Learning:** Actions class methods are queued, not executed immediately. Must call `.perform()` to execute the action chain.

---

## Challenge 2: Alert Not Found

**Broken Code:**
```java
driver.findElement(By.id("alert-button")).click();
Alert alert = driver.switchTo().alert();  // NoAlertPresentException!
alert.accept();
```

**Your Task:** Fix the timing issue

---

**Solution:**

**Bug:** Alert takes time to appear, not immediately available

**Fixed Code:**
```java
driver.findElement(By.id("alert-button")).click();

// Wait for alert to be present
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
Alert alert = wait.until(ExpectedConditions.alertIsPresent());
alert.accept();
```

**Key Learning:** Always wait for alerts using `ExpectedConditions.alertIsPresent()`

---

## Challenge 3: Element Inside iframe Not Found

**Broken Code:**
```java
driver.get("https://example.com");
WebElement insideFrame = driver.findElement(By.id("element-in-iframe"));
insideFrame.click();  // NoSuchElementException!
```

**Your Task:** Identify the context switch issue

---

**Solution:**

**Bug:** Trying to find element inside iframe without switching to it first

**Fixed Code:**
```java
driver.get("https://example.com");

// Switch to iframe first
driver.switchTo().frame("iframe-id");

// Now can find element
WebElement insideFrame = driver.findElement(By.id("element-in-iframe"));
insideFrame.click();

// Switch back when done
driver.switchTo().defaultContent();
```

**Key Learning:** Must switch context to iframe before accessing its elements

---

## Challenge 4: Wrong Window Closed

**Broken Code:**
```java
String mainWindow = driver.getWindowHandle();
driver.findElement(By.linkText("Open New Window")).click();

// Switch to new window
for (String window : driver.getWindowHandles()) {
    driver.switchTo().window(window);
}

driver.close();  // Closes main window instead of new window!
driver.switchTo().window(mainWindow);  // NoSuchWindowException!
```

**Your Task:** Fix the window management logic

---

**Solution:**

**Bug:** Loop doesn't check which window to switch to, ends up on main window

**Fixed Code:**
```java
String mainWindow = driver.getWindowHandle();
driver.findElement(By.linkText("Open New Window")).click();

// Switch to new window only
for (String window : driver.getWindowHandles()) {
    if (!window.equals(mainWindow)) {
        driver.switchTo().window(window);
        break;  // Exit loop once new window found
    }
}

driver.close();  // Closes new window
driver.switchTo().window(mainWindow);  // Switch back to main
```

**Key Learning:** Always track which window is which using window handles

---

## Challenge 5: Drag and Drop Not Working

**Broken Code:**
```java
WebElement source = driver.findElement(By.id("draggable"));
WebElement target = driver.findElement(By.id("droppable"));

Actions actions = new Actions(driver);
actions.clickAndHold(source);
actions.moveToElement(target);
actions.release();  // Doesn't work!
```

**Your Task:** Fix the action chain

---

**Solution:**

**Bug:** Each action is separate, not chained. Missing `.perform()`

**Fixed Code:**
```java
WebElement source = driver.findElement(By.id("draggable"));
WebElement target = driver.findElement(By.id("droppable"));

Actions actions = new Actions(driver);

// Option 1: Chain actions together
actions.clickAndHold(source)
       .moveToElement(target)
       .release()
       .perform();  // Execute entire chain

// Option 2: Use dragAndDrop method (simpler)
actions.dragAndDrop(source, target).perform();
```

**Key Learning:** Chain actions together and call `.perform()` once at the end

---

## 🔧 Debugging Checklist

**When Actions don't work:**
- [ ] Did you call `.perform()`?
- [ ] Is element visible and enabled?
- [ ] Is element in view (may need scroll)?
- [ ] Are you using correct Actions methods?

**When Alert handling fails:**
- [ ] Did you wait for alert to appear?
- [ ] Is it actually a JavaScript alert (not a div styled as alert)?
- [ ] Did you switch to alert before interacting?

**When iframe elements not found:**
- [ ] Did you switch to iframe first?
- [ ] Is iframe loaded (may need wait)?
- [ ] Did you switch back to defaultContent after?
- [ ] Are you using correct iframe identifier?

**When window switching fails:**
- [ ] Did you store main window handle before opening new window?
- [ ] Did you loop through handles correctly?
- [ ] Did you close correct window?
- [ ] Did you switch back to valid window?

---

## 💡 Best Practices Summary

```java
// ✅ CORRECT PATTERNS

// Actions
Actions actions = new Actions(driver);
actions.moveToElement(element).perform();  // Always perform()

// Alerts
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
Alert alert = wait.until(ExpectedConditions.alertIsPresent());
alert.accept();

// iframes
driver.switchTo().frame("myframe");
// ... work in iframe
driver.switchTo().defaultContent();  // Always switch back

// Windows
String main = driver.getWindowHandle();
// ... open new window
for (String handle : driver.getWindowHandles()) {
    if (!handle.equals(main)) {
        driver.switchTo().window(handle);
        break;
    }
}
// ... work in new window
driver.close();
driver.switchTo().window(main);
```

---

**Debugging mastery for advanced interactions achieved! 🎯**
