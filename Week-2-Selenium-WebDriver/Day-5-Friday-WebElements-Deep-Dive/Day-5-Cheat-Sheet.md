# DAY 5 CHEAT SHEET: WEBELEMENTS DEEP DIVE

## ⚡ findElement vs findElements

```java
// findElement - Single element, throws exception if not found
WebElement element = driver.findElement(By.id("submit"));

// findElements - List of elements, empty list if not found
List<WebElement> elements = driver.findElements(By.className("btn"));
System.out.println("Found " + elements.size() + " elements");

// Check existence without exception
List<WebElement> optional = driver.findElements(By.id("optional"));
if (!optional.isEmpty()) {
    optional.get(0).click();
}
```

---

## 🔄 Stale Element Handling

```java
// Problem: Element becomes stale after page refresh/navigation
WebElement element = driver.findElement(By.id("dynamic"));
driver.navigate().refresh();
// element.click();  // StaleElementReferenceException!

// Solution: Re-find element
element = driver.findElement(By.id("dynamic"));
element.click();  // Works!

// Pattern with retry
public WebElement findWithRetry(By locator) {
    for (int i = 0; i < 3; i++) {
        try {
            return driver.findElement(locator);
        } catch (StaleElementReferenceException e) {
            // Retry
        }
    }
    throw new RuntimeException("Element is stale");
}
```

---

## ⚙️ JavaScript Executor

```java
import org.openqa.selenium.JavascriptExecutor;

JavascriptExecutor js = (JavascriptExecutor) driver;

// Scroll to element
js.executeScript("arguments[0].scrollIntoView(true);", element);

// Click (bypasses overlays)
js.executeScript("arguments[0].click();", element);

// Set value (even if readonly)
js.executeScript("arguments[0].value='text';", element);

// Scroll to bottom
js.executeScript("window.scrollTo(0, document.body.scrollHeight)");

// Get page title via JS
String title = (String) js.executeScript("return document.title;");
```

---

## 📊 Element Attributes & Properties

```java
WebElement element = driver.findElement(By.id("input"));

// Get attributes
String value = element.getAttribute("value");
String className = element.getAttribute("class");
String href = element.getAttribute("href");

// Get CSS properties
String color = element.getCssValue("color");
String fontSize = element.getCssValue("font-size");

// Get text
String text = element.getText();

// Check states
boolean displayed = element.isDisplayed();
boolean enabled = element.isEnabled();
boolean selected = element.isSelected();  // checkbox/radio
```

---

## 👁️ Hidden & Disabled Elements

```java
// Check if visible
if (element.isDisplayed()) {
    element.click();
}

// Check if enabled
if (element.isEnabled()) {
    element.click();
}

// Force click hidden element
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].click();", hiddenElement);
```

---

## 📋 Common Patterns

### Pattern 1: Count Elements
```java
List<WebElement> buttons = driver.findElements(By.tagName("button"));
System.out.println("Total buttons: " + buttons.size());
```

### Pattern 2: Iterate Elements
```java
List<WebElement> links = driver.findElements(By.tagName("a"));
for (WebElement link : links) {
    System.out.println(link.getText() + " -> " + link.getAttribute("href"));
}
```

### Pattern 3: Get All Text from List
```java
List<WebElement> items = driver.findElements(By.className("item"));
List<String> texts = new ArrayList<>();
for (WebElement item : items) {
    texts.add(item.getText());
}
```

### Pattern 4: Find Child Element
```java
WebElement parent = driver.findElement(By.id("parent"));
WebElement child = parent.findElement(By.className("child"));
```

---

## 📌 Remember This

> **Always re-find elements after page navigation/refresh to avoid StaleElementReferenceException. Use JavaScript Executor only when Selenium methods fail.**

---

**Day 5 Complete! Tomorrow: Page Object Model 🚀**
