# DAY 5: WEBELEMENTS DEEP DIVE

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master findElement vs findElements
- [ ] Handle stale element references
- [ ] Work with dynamic content
- [ ] Use JavaScript Executor for complex scenarios
- [ ] Extract and validate element attributes
- [ ] Handle disabled and hidden elements

**Time Required:** 3 hours  
**Difficulty:** Medium-Advanced  
**Prerequisite:** Days 1-4

---

## 📚 Core Concepts

### Concept 1: findElement() vs findElements()

**findElement() - Single Element**
```java
// Returns first matching element
WebElement element = driver.findElement(By.className("button"));
// Throws NoSuchElementException if not found
```

**findElements() - Multiple Elements**
```java
// Returns List of all matching elements
List<WebElement> elements = driver.findElements(By.className("button"));
// Returns empty list if none found (doesn't throw exception)

System.out.println("Found " + elements.size() + " buttons");

// Iterate through elements
for (WebElement element : elements) {
    System.out.println(element.getText());
}
```

**When to use findElements():**
- Counting elements on page
- Iterating through similar elements
- Checking if element exists without exception

**Pattern for checking element existence:**
```java
List<WebElement> elements = driver.findElements(By.id("optional-element"));
if (elements.size() > 0) {
    System.out.println("Element exists");
    elements.get(0).click();
} else {
    System.out.println("Element not found");
}
```

---

### Concept 2: Stale Element Reference

**What is it?**
Element reference becomes "stale" when the DOM is refreshed and the element is re-rendered.

**When it happens:**
- Page refresh
- Navigation to new page
- AJAX content updates
- DOM manipulation via JavaScript

**Example Problem:**
```java
WebElement element = driver.findElement(By.id("dynamic"));
String text1 = element.getText();  // Works

driver.navigate().refresh();  // Page refreshes

String text2 = element.getText();  // StaleElementReferenceException!
```

**Solution:**
```java
WebElement element = driver.findElement(By.id("dynamic"));
String text1 = element.getText();

driver.navigate().refresh();

// Re-find element after page change
element = driver.findElement(By.id("dynamic"));
String text2 = element.getText();  // Works!
```

**Better Solution - Reusable Method:**
```java
public WebElement getElement(By locator, int retries) {
    WebElement element = null;
    for (int i = 0; i < retries; i++) {
        try {
            element = driver.findElement(locator);
            return element;
        } catch (StaleElementReferenceException e) {
            System.out.println("Stale element, retrying...");
        }
    }
    throw new RuntimeException("Element is stale after " + retries + " retries");
}
```

---

### Concept 3: JavaScript Executor

**What is it?**
Executes JavaScript code in the browser context. Useful for operations Selenium can't handle directly.

**Common Use Cases:**

**1. Scroll to Element**
```java
JavascriptExecutor js = (JavascriptExecutor) driver;
WebElement element = driver.findElement(By.id("footer-element"));
js.executeScript("arguments[0].scrollIntoView(true);", element);
```

**2. Click Hidden/Overlapped Element**
```java
JavascriptExecutor js = (JavascriptExecutor) driver;
WebElement element = driver.findElement(By.id("hidden-button"));
js.executeScript("arguments[0].click();", element);
```

**3. Get Element Attributes Not Visible**
```java
JavascriptExecutor js = (JavascriptExecutor) driver;
WebElement element = driver.findElement(By.id("input"));

// Get value using JS
String value = (String) js.executeScript("return arguments[0].value;", element);
```

**4. Set Element Value**
```java
JavascriptExecutor js = (JavascriptExecutor) driver;
WebElement input = driver.findElement(By.id("readonly-input"));
js.executeScript("arguments[0].value='New Value';", input);
```

**5. Execute Page-Level JavaScript**
```java
JavascriptExecutor js = (JavascriptExecutor) driver;

// Scroll to bottom of page
js.executeScript("window.scrollTo(0, document.body.scrollHeight)");

// Get page title via JS
String title = (String) js.executeScript("return document.title;");

// Refresh page
js.executeScript("location.reload()");
```

---

### Concept 4: Element Attributes and Properties

**getAttribute() - Get HTML Attributes**
```java
WebElement link = driver.findElement(By.tagName("a"));

String href = link.getAttribute("href");
String target = link.getAttribute("target");
String className = link.getAttribute("class");

System.out.println("Link href: " + href);
System.out.println("Opens in: " + target);
System.out.println("CSS classes: " + className);
```

**Common Attributes:**
- `href` - Link URL
- `src` - Image/script source
- `value` - Input field value
- `class` - CSS classes
- `id` - Element ID
- `name` - Element name
- `type` - Input type
- `disabled` - Is element disabled
- `checked` - Is checkbox/radio checked

**getCssValue() - Get CSS Properties**
```java
WebElement element = driver.findElement(By.id("styled-element"));

String color = element.getCssValue("color");
String fontSize = element.getCssValue("font-size");
String display = element.getCssValue("display");

System.out.println("Text color: " + color);
System.out.println("Font size: " + fontSize);
System.out.println("Display: " + display);
```

---

### Concept 5: Handling Hidden and Disabled Elements

**Check if Element is Displayed:**
```java
WebElement element = driver.findElement(By.id("maybe-hidden"));

if (element.isDisplayed()) {
    System.out.println("Element is visible");
    element.click();
} else {
    System.out.println("Element is hidden");
}
```

**Check if Element is Enabled:**
```java
WebElement button = driver.findElement(By.id("submit"));

if (button.isEnabled()) {
    button.click();
} else {
    System.out.println("Button is disabled");
}
```

**Force Click on Hidden Element:**
```java
WebElement hidden = driver.findElement(By.id("hidden-element"));

// Regular click won't work
// hidden.click();  // ElementNotInteractableException

// Use JavaScript to click
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].click();", hidden);
```

---

## 💡 Best Practices

**✅ DO:**
```java
// Use findElements() to check existence
List<WebElement> elements = driver.findElements(By.id("optional"));
if (!elements.isEmpty()) {
    elements.get(0).click();
}

// Re-find elements after page changes
element = driver.findElement(By.id("dynamic"));

// Use JavaScript Executor for tricky situations
JavascriptExecutor js = (JavascriptExecutor) driver;
js.executeScript("arguments[0].scrollIntoView();", element);

// Extract all needed data before page changes
String text = element.getText();
String value = element.getAttribute("value");
```

**❌ DON'T:**
```java
// Don't ignore stale element exceptions
// Catch and re-find instead

// Don't overuse JavaScript Executor
// Use only when Selenium methods fail

// Don't trust old element references
// Re-find after navigation/refresh
```

---

## 🎓 Interview Questions

**Q: What's the difference between findElement() and findElements()?**

**A:** 
- `findElement()` returns single WebElement, throws NoSuchElementException if not found
- `findElements()` returns List<WebElement>, returns empty list if none found
- Use findElements() when you need all matching elements or want to check existence without exception

**Q: How do you handle stale element references?**

**A:** Re-find the element after page changes:
```java
try {
    element.click();
} catch (StaleElementReferenceException e) {
    element = driver.findElement(locator);
    element.click();
}
```

**Q: When would you use JavaScript Executor?**

**A:** When Selenium methods fail:
- Scrolling to element
- Clicking hidden/overlapped elements
- Setting values in readonly fields
- Getting computed styles
- Custom browser operations

---

## ✅ Self-Check Questions

1. What does findElements() return if no elements match?
2. How do you count how many buttons are on a page?
3. What causes StaleElementReferenceException?
4. How do you scroll to an element using JavaScript?
5. How do you check if element is visible?

**Answers in Practice Exercises**

---

**Ready to master WebElements! 🎯**
