# DAY 2 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master all 8 locator strategies by automating a complete form. By the end, you'll be able to find ANY element on ANY webpage using the most appropriate locator strategy.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Find Elements Using All 8 Locator Strategies
**Objective:** Practice syntax for all locator types

**Task:**
Navigate to https://www.wikipedia.org and find elements using different locators.

**Starter Code:**
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise1_AllLocators {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.wikipedia.org");
            Thread.sleep(2000);

            // TODO: Find search input by ID
            WebElement searchById = driver.findElement(By.id("searchInput"));
            System.out.println("✓ Found by ID: " + searchById.getTagName());

            // TODO: Find search input by name attribute

            // TODO: Find any element by class name

            // TODO: Find any input element by tag name

            // TODO: Find "Read Wikipedia" link by link text

            // TODO: Find link by partial link text

            // TODO: Find search input using CSS selector

            // TODO: Find search input using XPath

            System.out.println("\n✓ All 8 locator strategies demonstrated!");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Expected Output:**
```
✓ Found by ID: input
✓ Found by name: input
✓ Found by className: div
✓ Found by tagName: input
✓ Found by linkText: a
✓ Found by partialLinkText: a
✓ Found by cssSelector: input
✓ Found by xpath: input

✓ All 8 locator strategies demonstrated!
```

---

### Exercise 2: CSS Selector Practice
**Objective:** Write CSS selectors for various scenarios

**Task:**
Given this HTML, write CSS selectors to find each element:

```html
<div id="login-form" class="form-container">
    <input id="username" name="user" class="input-field" type="text" placeholder="Enter username"/>
    <input id="password" name="pass" class="input-field required" type="password"/>
    <button id="login-btn" class="btn btn-primary" data-testid="submit">Login</button>
    <a href="/forgot-password" class="link">Forgot Password?</a>
</div>
```

**Starter Code:**
```java
// Find username by ID using CSS
By locator1 = By.cssSelector("#username");

// TODO: Find password by class "required"

// TODO: Find button with multiple classes "btn" AND "btn-primary"

// TODO: Find input with name="pass"

// TODO: Find button with data-testid attribute

// TODO: Find link with href containing "forgot"

// TODO: Find second input inside login-form

// TODO: Find all inputs with class "input-field"
```

---

### Exercise 3: XPath Practice
**Objective:** Write XPath expressions for various scenarios

**Starter Code:**
```java
// Using the same HTML from Exercise 2

// Find username by id attribute
By xpath1 = By.xpath("//input[@id='username']");

// TODO: Find button by text "Login"

// TODO: Find input where type="password"

// TODO: Find link that contains text "Forgot"

// TODO: Find button with multiple attributes (id AND class)

// TODO: Find parent div of password input

// TODO: Find all inputs under div with id="login-form"

// TODO: Find link that is a sibling of button
```

---

## 💪 Core Practice (30 min)

### Exercise 4: Form Automation with Multiple Locators
**Objective:** Automate a registration form using best-practice locators

**Problem Statement:**
Navigate to https://demo.automationtesting.in/Register.html and fill the registration form using appropriate locator strategies.

**Requirements:**
- Fill First Name (use By.cssSelector)
- Fill Last Name (use By.xpath)
- Fill Address (use By.cssSelector with attribute)
- Fill Email (use By.cssSelector)
- Fill Phone (use By.xpath)
- Select Gender radio button (use By.cssSelector)
- Select hobbies checkboxes (use By.id)
- Print confirmation message

**Starter Code:**
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise4_FormAutomation {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://demo.automationtesting.in/Register.html");
            Thread.sleep(2000);

            // TODO: Fill First Name using CSS selector
            WebElement firstName = driver.findElement(By.cssSelector("input[placeholder='First Name']"));
            firstName.sendKeys("John");
            System.out.println("✓ First Name entered");

            // TODO: Fill Last Name using XPath

            // TODO: Fill Address (textarea) using CSS with attribute

            // TODO: Fill Email using CSS

            // TODO: Fill Phone using XPath

            // TODO: Select Male/Female radio button using CSS

            // TODO: Select Cricket checkbox using By.id

            // TODO: Select Movies checkbox using By.id

            Thread.sleep(3000); // See the filled form
            System.out.println("\n✓ Form filled successfully!");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Hints:**
- Inspect each element (F12) to see attributes
- Use `input[placeholder='...']` for CSS matching by placeholder
- Use `//input[@ng-model='...']` for XPath with Angular attributes
- Radio buttons and checkboxes use `.click()` method

---

### Exercise 5: Dynamic Element Location
**Objective:** Find elements with dynamic IDs and classes

**Problem Statement:**
Create methods to find elements when IDs are dynamic or elements don't have unique attributes.

**Starter Code:**
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise5_DynamicElements {

    // Method 1: Find element with dynamic ID (starts with specific text)
    public static WebElement findByPartialId(WebDriver driver, String idStartsWith) {
        // TODO: Use CSS selector with ^= (starts with)
        return driver.findElement(By.cssSelector("[id^='" + idStartsWith + "']"));
    }

    // Method 2: Find element by text content
    public static WebElement findByText(WebDriver driver, String text) {
        // TODO: Use XPath with text()
        return null; // Replace with XPath
    }

    // Method 3: Find element by parent's attribute
    public static WebElement findInputInDiv(WebDriver driver, String divId) {
        // TODO: Use CSS selector for parent > child
        return null; // Replace with CSS
    }

    // Method 4: Find element with multiple classes
    public static WebElement findByMultipleClasses(WebDriver driver, String class1, String class2) {
        // TODO: Use CSS with .class1.class2 syntax
        return null; // Replace with CSS
    }

    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.google.com");

            // Test dynamic ID method
            WebElement searchBox = findByPartialId(driver, "APjFqb");
            searchBox.sendKeys("Selenium");
            System.out.println("✓ Found element with partial ID");

            // TODO: Test other methods

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Automate Complete Login Flow (Today's Mini Project)
**Objective:** Build a login automation using best locator strategies

**Requirements:**
- Navigate to https://practicetestautomation.com/practice-test-login/
- Enter username: student
- Enter password: Password123
- Click Submit button
- Verify successful login by checking URL contains "logged-in"
- Verify success message appears
- Click Logout button
- Use different locator strategies for each element
- Add clear console reporting

**Starter Code:**
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise6_LoginAutomation {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();

            // TODO: Navigate to login page

            // TODO: Find username field (use By.id)

            // TODO: Enter username: student

            // TODO: Find password field (use By.name)

            // TODO: Enter password: Password123

            // TODO: Find submit button (use By.cssSelector or By.xpath)

            // TODO: Click submit

            // TODO: Wait for page load

            // TODO: Verify URL contains "logged-in"

            // TODO: Find and verify success message (use XPath with text)

            // TODO: Click Logout button

            System.out.println("\n✓✓✓ LOGIN AUTOMATION - PASSED ✓✓✓");

        } catch (Exception e) {
            System.out.println("✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

**Testing Instructions:**
1. Run the program
2. Watch browser navigate and fill form
3. Verify console shows all steps
4. Verify "PASSED" message appears

**Bonus Challenges:**
- [ ] Test invalid credentials and verify error message
- [ ] Extract and print all links on the logged-in page
- [ ] Count how many elements have class "wp-block-button"
- [ ] Take screenshot of success page

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise1_AllLocators {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.wikipedia.org");
            Thread.sleep(2000);

            // 1. By ID
            WebElement searchById = driver.findElement(By.id("searchInput"));
            System.out.println("✓ Found by ID: " + searchById.getTagName());

            // 2. By name
            WebElement searchByName = driver.findElement(By.name("search"));
            System.out.println("✓ Found by name: " + searchByName.getTagName());

            // 3. By class name
            WebElement divByClass = driver.findElement(By.className("search-container"));
            System.out.println("✓ Found by className: " + divByClass.getTagName());

            // 4. By tag name
            WebElement inputByTag = driver.findElement(By.tagName("input"));
            System.out.println("✓ Found by tagName: " + inputByTag.getTagName());

            // 5. By link text
            WebElement linkByText = driver.findElement(By.linkText("Read Wikipedia in your language"));
            System.out.println("✓ Found by linkText: " + linkByText.getTagName());

            // 6. By partial link text
            WebElement linkByPartial = driver.findElement(By.partialLinkText("Read Wikipedia"));
            System.out.println("✓ Found by partialLinkText: " + linkByPartial.getTagName());

            // 7. By CSS selector
            WebElement searchByCSS = driver.findElement(By.cssSelector("#searchInput"));
            System.out.println("✓ Found by cssSelector: " + searchByCSS.getTagName());

            // 8. By XPath
            WebElement searchByXPath = driver.findElement(By.xpath("//input[@id='searchInput']"));
            System.out.println("✓ Found by xpath: " + searchByXPath.getTagName());

            System.out.println("\n✓ All 8 locator strategies demonstrated!");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 2 Solution (CSS Selectors)
```java
// Find username by ID
By css1 = By.cssSelector("#username");

// Find password by class "required"
By css2 = By.cssSelector(".required");
// Or more specific:
By css2b = By.cssSelector("input.required");

// Find button with multiple classes
By css3 = By.cssSelector(".btn.btn-primary");
// Or with button tag:
By css3b = By.cssSelector("button.btn.btn-primary");

// Find input with name="pass"
By css4 = By.cssSelector("input[name='pass']");

// Find button with data-testid
By css5 = By.cssSelector("[data-testid='submit']");
// Or more specific:
By css5b = By.cssSelector("button[data-testid='submit']");

// Find link with href containing "forgot"
By css6 = By.cssSelector("a[href*='forgot']");

// Find second input inside login-form
By css7 = By.cssSelector("#login-form input:nth-child(2)");
// Or nth-of-type:
By css7b = By.cssSelector("#login-form input:nth-of-type(2)");

// Find all inputs with class "input-field"
By css8 = By.cssSelector("input.input-field");
```

---

### Exercise 3 Solution (XPath)
```java
// Find username by id
By xpath1 = By.xpath("//input[@id='username']");

// Find button by text
By xpath2 = By.xpath("//button[text()='Login']");

// Find input where type="password"
By xpath3 = By.xpath("//input[@type='password']");

// Find link containing text "Forgot"
By xpath4 = By.xpath("//a[contains(text(), 'Forgot')]");

// Find button with multiple attributes
By xpath5 = By.xpath("//button[@id='login-btn' and @class='btn btn-primary']");
// Or contains for class:
By xpath5b = By.xpath("//button[@id='login-btn' and contains(@class, 'btn-primary')]");

// Find parent div of password input
By xpath6 = By.xpath("//input[@id='password']//parent::div");
// Or ancestor:
By xpath6b = By.xpath("//input[@id='password']//ancestor::div");

// Find all inputs under div with id="login-form"
By xpath7 = By.xpath("//div[@id='login-form']//input");

// Find link that is sibling of button
By xpath8 = By.xpath("//button[@id='login-btn']//following-sibling::a");
```

---

### Exercise 4 Solution
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise4_FormAutomation {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            driver.get("https://demo.automationtesting.in/Register.html");
            Thread.sleep(2000);

            // Fill First Name using CSS
            WebElement firstName = driver.findElement(By.cssSelector("input[placeholder='First Name']"));
            firstName.sendKeys("John");
            System.out.println("✓ First Name entered");

            // Fill Last Name using XPath
            WebElement lastName = driver.findElement(By.xpath("//input[@placeholder='Last Name']"));
            lastName.sendKeys("Doe");
            System.out.println("✓ Last Name entered");

            // Fill Address using CSS
            WebElement address = driver.findElement(By.cssSelector("textarea[ng-model='Adress']"));
            address.sendKeys("123 Test Street, Automation City");
            System.out.println("✓ Address entered");

            // Fill Email using CSS
            WebElement email = driver.findElement(By.cssSelector("input[type='email']"));
            email.sendKeys("john.doe@test.com");
            System.out.println("✓ Email entered");

            // Fill Phone using XPath
            WebElement phone = driver.findElement(By.xpath("//input[@type='tel']"));
            phone.sendKeys("1234567890");
            System.out.println("✓ Phone entered");

            // Select Male radio button
            WebElement male = driver.findElement(By.cssSelector("input[value='Male']"));
            male.click();
            System.out.println("✓ Gender selected");

            // Select Cricket checkbox
            WebElement cricket = driver.findElement(By.id("checkbox1"));
            cricket.click();
            System.out.println("✓ Cricket hobby selected");

            // Select Movies checkbox
            WebElement movies = driver.findElement(By.id("checkbox2"));
            movies.click();
            System.out.println("✓ Movies hobby selected");

            Thread.sleep(3000);
            System.out.println("\n✓✓✓ FORM FILLED SUCCESSFULLY! ✓✓✓");

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 5 Solution
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise5_DynamicElements {

    public static WebElement findByPartialId(WebDriver driver, String idStartsWith) {
        return driver.findElement(By.cssSelector("[id^='" + idStartsWith + "']"));
    }

    public static WebElement findByText(WebDriver driver, String text) {
        return driver.findElement(By.xpath("//*[text()='" + text + "']"));
    }

    public static WebElement findInputInDiv(WebDriver driver, String divId) {
        return driver.findElement(By.cssSelector("#" + divId + " > input"));
    }

    public static WebElement findByMultipleClasses(WebDriver driver, String class1, String class2) {
        return driver.findElement(By.cssSelector("." + class1 + "." + class2));
    }

    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.get("https://www.google.com");
            Thread.sleep(1000);

            WebElement searchBox = findByPartialId(driver, "APjFqb");
            searchBox.sendKeys("Selenium Locators");
            System.out.println("✓ Found and interacted with dynamic ID element");

            Thread.sleep(2000);

        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

### Exercise 6 Solution (Mini Project)
```java
package com.sdet.locators;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;

public class Exercise6_LoginAutomation {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            System.out.println("=== LOGIN AUTOMATION TEST ===\n");

            // Navigate to login page
            driver.get("https://practicetestautomation.com/practice-test-login/");
            Thread.sleep(2000);
            System.out.println("✓ Navigated to login page");

            // Find username field by ID
            WebElement username = driver.findElement(By.id("username"));
            username.sendKeys("student");
            System.out.println("✓ Username entered (using By.id)");

            // Find password field by name
            WebElement password = driver.findElement(By.name("password"));
            password.sendKeys("Password123");
            System.out.println("✓ Password entered (using By.name)");

            // Find submit button by CSS selector
            WebElement submitBtn = driver.findElement(By.cssSelector("#submit"));
            submitBtn.click();
            System.out.println("✓ Submit button clicked (using By.cssSelector)");

            // Wait for page load
            Thread.sleep(3000);

            // Verify URL
            String currentUrl = driver.getCurrentUrl();
            if (currentUrl.contains("logged-in")) {
                System.out.println("✓ URL verification passed: " + currentUrl);
            } else {
                throw new Exception("URL verification failed!");
            }

            // Verify success message using XPath with text
            WebElement successMsg = driver.findElement(By.xpath("//strong[contains(text(), 'Congratulations')]"));
            System.out.println("✓ Success message found (using XPath with text): " + successMsg.getText());

            // Find and click Logout button
            WebElement logoutBtn = driver.findElement(By.linkText("Log out"));
            logoutBtn.click();
            System.out.println("✓ Logout successful (using By.linkText)");

            Thread.sleep(2000);

            System.out.println("\n" + "=".repeat(40));
            System.out.println("✓✓✓ LOGIN AUTOMATION - PASSED ✓✓✓");
            System.out.println("=".repeat(40));
            System.out.println("\nLocators used:");
            System.out.println("- By.id() for username");
            System.out.println("- By.name() for password");
            System.out.println("- By.cssSelector() for submit button");
            System.out.println("- By.xpath() with text for success message");
            System.out.println("- By.linkText() for logout button");

        } catch (Exception e) {
            System.out.println("\n✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
        }
    }
}
```

---

## ✅ Answers to Self-Check Questions

**1. Name all 8 Selenium locator strategies in order of preference:**
1. By.id()
2. By.name()
3. By.cssSelector()
4. By.xpath()
5. By.linkText()
6. By.partialLinkText()
7. By.className()
8. By.tagName()

**2. What's wrong with `By.className("btn btn-primary")`?**
The className() method only accepts a single class name, no spaces. For multiple classes, use CSS: `By.cssSelector(".btn.btn-primary")`

**3. How do you find an element by its text content?**
Use XPath: `By.xpath("//button[text()='Login']")` for exact match, or `By.xpath("//button[contains(text(), 'Login')]")` for partial match.

**4. What's the difference between absolute and relative XPath?**
- Absolute: Starts with `/html/body...` - brittle, breaks with any DOM change
- Relative: Starts with `//` - finds element anywhere in DOM, more robust

**5. How can you test a CSS selector before using it in Selenium?**
In Chrome DevTools Console, use `$$("your-css-selector")` - returns array of matching elements. If array is empty, selector won't work.

---

**Great job! You've mastered locators! Tomorrow: WebDriver Actions 🚀**
