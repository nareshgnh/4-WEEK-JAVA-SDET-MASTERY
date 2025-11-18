# DAY 3 CHEAT SHEET: WEBDRIVER ACTIONS & WAITS

## ⚡ WebElement Interaction Methods

```java
// Click
element.click();

// Type text
element.sendKeys("text");

// Clear text
element.clear();

// Submit form
element.submit();

// Get text
String text = element.getText();

// Get attribute
String value = element.getAttribute("value");

// Check state
boolean visible = element.isDisplayed();
boolean enabled = element.isEnabled();
boolean selected = element.isSelected();  // checkbox/radio
```

---

## 🌐 Browser Navigation

```java
// Navigate to URL
driver.get("https://example.com");
driver.navigate().to("https://example.com");

// Browser controls
driver.navigate().back();
driver.navigate().forward();
driver.navigate().refresh();

// Window management
driver.manage().window().maximize();
driver.manage().window().fullscreen();
```

---

## ⏰ Wait Strategies (CRITICAL!)

### Implicit Wait (Set Once, Global)
```java
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
```

### Explicit Wait (Targeted, Recommended)
```java
import org.openqa.selenium.support.ui.WebDriverWait;
import org.openqa.selenium.support.ui.ExpectedConditions;

WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Wait for clickable
wait.until(ExpectedConditions.elementToBeClickable(By.id("submit"))).click();

// Wait for visible
wait.until(ExpectedConditions.visibilityOfElementLocated(By.id("message")));

// Wait for text
wait.until(ExpectedConditions.textToBe(By.id("status"), "Complete"));

// Wait for URL
wait.until(ExpectedConditions.urlContains("dashboard"));
```

### Common ExpectedConditions
```java
// Element presence/visibility
presenceOfElementLocated(By locator)
visibilityOfElementLocated(By locator)
elementToBeClickable(By locator)
invisibilityOfElementLocated(By locator)

// Text conditions
textToBe(By locator, String text)
textToBePresentInElementLocated(By locator, String text)

// Attribute
attributeToBe(By locator, String attribute, String value)

// Alert/Title/URL
alertIsPresent()
titleContains(String title)
urlContains(String url)
```

---

## 💡 Best Practices

**✅ DO:**
```java
// Use explicit waits
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.elementToBeClickable(By.id("btn"))).click();

// Clear before sendKeys
element.clear();
element.sendKeys("new text");

// Maximize window
driver.manage().window().maximize();
```

**❌ DON'T:**
```java
// Never use Thread.sleep() in production
Thread.sleep(5000);  // ❌

// Don't set very long waits
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(60));  // ❌
```

---

## 🎯 Quick Decision Guide

**When element won't click:**
1. Add explicit wait for clickable
2. Check if element is in view (may need scroll)
3. Check if element is in iframe

**When element not found:**
1. Add implicit wait (if not set)
2. Add explicit wait for presence
3. Verify locator is correct
4. Check if element loads via JavaScript

**When text doesn't appear:**
1. Wait for visibility
2. Wait for specific text
3. Check if in iframe

---

## 📌 Remember This

> **NEVER use Thread.sleep() - ALWAYS use WebDriverWait with ExpectedConditions**

```java
// ❌ BAD
Thread.sleep(5000);
element.click();

// ✅ GOOD
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.elementToBeClickable(element)).click();
```

---

**Day 3 Complete! Tomorrow: Advanced Interactions (Actions class, alerts, iframes) 🚀**
