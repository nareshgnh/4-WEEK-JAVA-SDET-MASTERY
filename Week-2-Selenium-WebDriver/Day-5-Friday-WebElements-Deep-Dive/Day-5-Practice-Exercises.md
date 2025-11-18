# DAY 5 PRACTICE EXERCISES

## Exercise 1: findElements() - Count and Iterate
```java
driver.get("https://www.google.com");
List<WebElement> links = driver.findElements(By.tagName("a"));
System.out.println("Total links: " + links.size());
for (WebElement link : links) {
    if (link.getText().length() > 0) {
        System.out.println(link.getText());
    }
}
```

## Exercise 2: Handle Stale Elements
```java
driver.get("https://the-internet.herokuapp.com/dynamic_loading/1");
WebElement start = driver.findElement(By.cssSelector("#start button"));
start.click();

// Wait and re-find element after it appears
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
wait.until(ExpectedConditions.invisibilityOfElementLocated(By.id("loading")));

WebElement finish = driver.findElement(By.id("finish"));
System.out.println(finish.getText());
```

## Exercise 3: JavaScript Executor Scroll
```java
driver.get("https://the-internet.herokuapp.com/infinite_scroll");
JavascriptExecutor js = (JavascriptExecutor) driver;

for (int i = 0; i < 5; i++) {
    js.executeScript("window.scrollTo(0, document.body.scrollHeight)");
    Thread.sleep(1000);
}
System.out.println("Scrolled 5 times");
```

## Exercise 4: Extract All Attributes
```java
driver.get("https://the-internet.herokuapp.com/");
List<WebElement> links = driver.findElements(By.tagName("a"));

for (WebElement link : links) {
    String text = link.getText();
    String href = link.getAttribute("href");
    if (text.length() > 0) {
        System.out.println(text + " -> " + href);
    }
}
```

## Exercise 5: Check Element States
```java
driver.get("https://the-internet.herokuapp.com/dynamic_controls");

WebElement checkbox = driver.findElement(By.cssSelector("#checkbox input"));
System.out.println("Displayed: " + checkbox.isDisplayed());
System.out.println("Enabled: " + checkbox.isEnabled());
System.out.println("Selected: " + checkbox.isSelected());

checkbox.click();
System.out.println("After click - Selected: " + checkbox.isSelected());
```

**Answers to Self-Check:**
1. Empty List<WebElement>
2. `driver.findElements(By.tagName("button")).size()`
3. DOM refresh, page navigation, element re-render
4. `js.executeScript("arguments[0].scrollIntoView();", element)`
5. `element.isDisplayed()`
