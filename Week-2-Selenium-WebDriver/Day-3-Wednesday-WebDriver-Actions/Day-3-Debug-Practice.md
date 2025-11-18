# DAY 3 DEBUGGING CHALLENGES

## 🐛 Wait-Related Debugging

Most Selenium failures are timing issues. Master debugging waits and you'll fix 80% of flaky tests.

---

## Challenge 1: ElementNotInteractableException

**Broken Code:**
```java
driver.get("https://example.com");
WebElement button = driver.findElement(By.id("submit"));
button.click();  // ElementNotInteractableException!
```

**Error Message:**
```
ElementNotInteractableException: element not interactable
```

**Your Task:** Fix the timing issue

---

**Solution:**

**Bug:** Element found but not yet clickable (still loading, covered by another element, or not in view)

**Fixed Code:**
```java
driver.get("https://example.com");

// Wait for element to be clickable
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement button = wait.until(
    ExpectedConditions.elementToBeClickable(By.id("submit"))
);
button.click();  // Now works!
```

**Key Learning:** Always wait for `elementToBeClickable` before clicking, not just `presenceOfElement`

---

## Challenge 2: Stale Element Reference

**Broken Code:**
```java
WebElement element = driver.findElement(By.id("dynamic"));
String text1 = element.getText();

driver.navigate().refresh();  // Page refreshes

String text2 = element.getText();  // StaleElementReferenceException!
```

**Error Message:**
```
StaleElementReferenceException: stale element reference: element is not attached to the page document
```

**Your Task:** Fix the stale element issue

---

**Solution:**

**Bug:** Element reference becomes invalid after page refresh/DOM change

**Fixed Code:**
```java
WebElement element = driver.findElement(By.id("dynamic"));
String text1 = element.getText();

driver.navigate().refresh();

// Re-find element after page change
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
element = wait.until(ExpectedConditions.presenceOfElementLocated(By.id("dynamic")));
String text2 = element.getText();  // Works!
```

**Key Learning:** Always re-find elements after page changes (refresh, navigation, DOM updates)

---

## Challenge 3: NoSuchElementException Despite Wait

**Broken Code:**
```java
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
driver.get("https://example.com");

// Element loads via JavaScript after 15 seconds
WebElement element = driver.findElement(By.id("slow-element"));  // NoSuchElementException!
```

**Your Task:** Fix the insufficient wait time

---

**Solution:**

**Bug:** Implicit wait (10s) is shorter than element load time (15s)

**Fixed Code:**
```java
// Option 1: Increase implicit wait (not recommended - makes all tests slower)
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(20));

// Option 2: Use explicit wait for this specific element (BETTER)
driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
driver.get("https://example.com");

WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(20));
WebElement element = wait.until(
    ExpectedConditions.presenceOfElementLocated(By.id("slow-element"))
);
```

**Key Learning:** Use explicit waits for elements with longer load times

---

## Challenge 4: Text Not Found Despite Element Present

**Broken Code:**
```java
driver.get("https://example.com");
WebElement message = driver.findElement(By.id("status"));
System.out.println(message.getText());  // Prints empty string!
```

**Your Task:** Wait for text to appear

---

**Solution:**

**Bug:** Element exists but text loads later via JavaScript

**Fixed Code:**
```java
driver.get("https://example.com");

WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));

// Wait for element to contain expected text
wait.until(ExpectedConditions.textToBePresentInElementLocated(
    By.id("status"), "Success"
));

WebElement message = driver.findElement(By.id("status"));
System.out.println(message.getText());  // Now prints "Success"
```

**Key Learning:** Use `textToBePresentInElementLocated` when text loads dynamically

---

## Challenge 5: Thread.sleep() Making Tests Slow

**Broken Code:**
```java
driver.get("https://example.com");
Thread.sleep(5000);  // Wait 5 seconds every time
driver.findElement(By.id("element")).click();
// Element usually loads in 1 second, but we wait 5 every time!
```

**Your Task:** Replace Thread.sleep() with smart wait

---

**Solution:**

**Bug:** Thread.sleep() always waits full time, even if element ready sooner

**Fixed Code:**
```java
driver.get("https://example.com");

// Waits UP TO 5 seconds, but proceeds as soon as element is clickable
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(5));
WebElement element = wait.until(
    ExpectedConditions.elementToBeClickable(By.id("element"))
);
element.click();

// If element appears in 1 second, test continues after 1 second, not 5!
```

**Key Learning:** WebDriverWait is smart - waits only as long as needed, up to maximum

---

## 🔧 Debugging Wait Issues - Checklist

When element interaction fails:

1. **Check if element exists:** Use browser DevTools to verify element is in DOM
2. **Check if element visible:** Element may exist but be hidden
3. **Check if element clickable:** May be covered by another element
4. **Add appropriate wait:**
   - `presenceOfElementLocated` - Element in DOM
   - `visibilityOfElementLocated` - Element visible
   - `elementToBeClickable` - Element ready to click
5. **Check for iframe:** Element may be in iframe (need to switch)
6. **Check for dynamic ID:** ID may change on each page load
7. **Verify locator:** Use browser console to test locator

---

## 💡 Wait Strategy Best Practices

```java
// ✅ RECOMMENDED PATTERN
public class BasePage {
    protected WebDriver driver;
    protected WebDriverWait wait;

    public BasePage(WebDriver driver) {
        this.driver = driver;
        // Implicit wait as safety net
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));
        // Explicit wait for specific conditions
        this.wait = new WebDriverWait(driver, Duration.ofSeconds(10));
    }

    protected void clickElement(By locator) {
        wait.until(ExpectedConditions.elementToBeClickable(locator)).click();
    }

    protected void typeText(By locator, String text) {
        WebElement element = wait.until(ExpectedConditions.visibilityOfElementLocated(locator));
        element.clear();
        element.sendKeys(text);
    }

    protected String getText(By locator) {
        return wait.until(ExpectedConditions.visibilityOfElementLocated(locator)).getText();
    }
}
```

---

**Debugging mastery achieved! 🎯**
