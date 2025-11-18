# DAY 3: WEBDRIVER ACTIONS & WAITS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master all WebDriver interaction methods (click, sendKeys, clear, submit)
- [ ] Understand browser navigation methods
- [ ] Master wait strategies (Implicit, Explicit, Fluent Waits)
- [ ] Learn to handle timing issues in tests
- [ ] Build robust, non-flaky automation

**Time Required:** 3 hours  
**Difficulty:** Medium  
**Prerequisite:** Days 1-2 (Selenium setup, Locators)

---

## 📚 Core Concepts

### Concept 1: WebElement Interaction Methods

**click() - Most Common Action**
```java
WebElement button = driver.findElement(By.id("submit"));
button.click();
```
**Use for:** Buttons, links, radio buttons, checkboxes, any clickable element

**sendKeys() - Text Input**
```java
WebElement input = driver.findElement(By.name("username"));
input.sendKeys("testuser");
```
**Use for:** Text fields, text areas, any input that accepts keyboard input

**clear() - Clear Existing Text**
```java
WebElement input = driver.findElement(By.id("search"));
input.clear();  // Remove existing text
input.sendKeys("new search");
```
**Use for:** Clearing fields before entering new text

**submit() - Submit Forms**
```java
WebElement searchBox = driver.findElement(By.name("q"));
searchBox.sendKeys("Selenium");
searchBox.submit();  // Same as clicking Search button
```
**Use for:** Submitting forms (works on any element inside a form)

**getText() - Get Element Text**
```java
WebElement message = driver.findElement(By.id("success-msg"));
String text = message.getText();
System.out.println("Message: " + text);
```

**getAttribute() - Get Attribute Value**
```java
WebElement input = driver.findElement(By.name("email"));
String value = input.getAttribute("value");  // Get input value
String placeholder = input.getAttribute("placeholder");
```

**isDisplayed(), isEnabled(), isSelected() - Element State**
```java
WebElement element = driver.findElement(By.id("submit"));
boolean visible = element.isDisplayed();   // Is element visible?
boolean enabled = element.isEnabled();     // Is element enabled?

WebElement checkbox = driver.findElement(By.id("terms"));
boolean selected = checkbox.isSelected();  // Is checkbox/radio selected?
```

---

### Concept 2: Browser Navigation Methods

**driver.get() - Navigate to URL**
```java
driver.get("https://www.google.com");  // Navigates and waits for page load
```

**driver.navigate() Methods**
```java
// Navigate to URL (same as get())
driver.navigate().to("https://www.google.com");

// Browser back button
driver.navigate().back();

// Browser forward button
driver.navigate().forward();

// Refresh page
driver.navigate().refresh();
```

**Window Management**
```java
// Maximize window
driver.manage().window().maximize();

// Fullscreen
driver.manage().window().fullscreen();

// Set size
Dimension newSize = new Dimension(1024, 768);
driver.manage().window().setSize(newSize);

// Set position
Point newPosition = new Point(0, 0);
driver.manage().window().setPosition(newPosition);
```

---

### Concept 3: Wait Strategies - CRITICAL!

**Problem: Timing Issues**
```java
// ❌ This often fails
driver.get("https://example.com");
driver.findElement(By.id("dynamic-content")).click();  // NoSuchElementException!
// Element loads after page load via JavaScript
```

**Solution 1: Implicit Wait (Global)**
```java
// Set once, applies to all findElement() calls
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));

// Now if element not immediately found, waits up to 10 seconds
driver.findElement(By.id("dynamic-content")).click();  // Works!
```

**Pros:** Set once, works for all elements  
**Cons:** Always waits max time if element not found, not flexible

**Solution 2: Explicit Wait (Specific Conditions)**
```java
import org.openqa.selenium.support.ui.WebDriverWait;
import org.openqa.selenium.support.ui.ExpectedConditions;

// Wait up to 10 seconds for specific condition
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Wait for element to be clickable
WebElement element = wait.until(ExpectedConditions.elementToBeClickable(By.id("submit")));
element.click();

// Wait for element to be visible
WebElement message = wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("success")));

// Wait for element to have specific text
wait.until(ExpectedConditions.textToBe(By.id("status"), "Complete"));
```

**Pros:** Flexible, waits only as long as needed  
**Cons:** Must write for each specific wait

**Solution 3: Fluent Wait (Most Flexible)**
```java
import org.openqa.selenium.support.ui.FluentWait;

Wait<WebDriver> fluentWait = new FluentWait<>(driver)
    .withTimeout(Duration.ofSeconds(30))
    .pollingEvery(Duration.ofSeconds(2))
    .ignoring(NoSuchElementException.class);

WebElement element = fluentWait.until(driver -> {
    return driver.findElement(By.id("dynamic-element"));
});
```

**Pros:** Maximum control, can ignore specific exceptions  
**Cons:** More complex syntax

---

### Concept 4: Common ExpectedConditions

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Element conditions
wait.until(ExpectedConditions.presenceOfElementLocated(By.id("element")));
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("element")));
wait.until(ExpectedConditions.elementToBeClickable(By.id("button")));
wait.until(ExpectedConditions.invisibilityOfElementLocated(By.id("loader")));

// Text conditions
wait.until(ExpectedConditions.textToBe(By.id("status"), "Ready"));
wait.until(ExpectedConditions.textToBePresent InElementLocated(By.id("msg"), "Success"));

// Attribute conditions
wait.until(ExpectedConditions.attributeToBe(By.id("input"), "value", "test"));

// Alert conditions
wait.until(ExpectedConditions.alertIsPresent());

// Title/URL conditions
wait.until(ExpectedConditions.titleContains("Google"));
wait.until(ExpectedConditions.urlContains("dashboard"));
```

---

## 💡 Best Practices

**✅ DO:**
```java
// Always use waits, never Thread.sleep()
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.elementToBeClickable(By.id("button"))).click();

// Combine implicit + explicit waits carefully
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));
// Use explicit for special cases

// Clear before entering text
element.clear();
element.sendKeys("new text");
```

**❌ DON'T:**
```java
// Don't use Thread.sleep() in production
Thread.sleep(5000);  // ❌ Wastes time, unreliable

// Don't set very long implicit waits
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(60));  // ❌ Too long

// Don't mix implicit and explicit waits carelessly
// Can cause unpredictable wait times
```

---

## 🎓 Interview Questions

**Q: What's the difference between implicit and explicit waits?**

**A:** 
- **Implicit Wait:** Global setting, applies to all findElement() calls, waits up to specified time for element to appear
- **Explicit Wait:** Targeted wait for specific conditions (clickable, visible, etc.), more flexible
- **Best Practice:** Use implicit wait as baseline (5-10s), explicit wait for specific scenarios

**Q: Why should we avoid Thread.sleep()?**

**A:** Thread.sleep() makes tests:
1. Slower (always waits full time)
2. Unreliable (may need more/less time)
3. Unprofessional (Selenium has better wait mechanisms)
4. Non-adaptive (doesn't respond to actual page state)

Use WebDriverWait instead - waits only as long as needed.

---

## ✅ Self-Check Questions

1. What's the difference between `driver.get()` and `driver.navigate().to()`?
2. When would you use `submit()` vs `click()`?
3. What's the difference between `presenceOfElementLocated` and `visibilityOfElementLocated`?
4. How do you wait for an element to be clickable?
5. What happens if you click an element that's not clickable yet?

**Answers in Practice Exercises.**

---

**Ready for hands-on practice! 🎯**
