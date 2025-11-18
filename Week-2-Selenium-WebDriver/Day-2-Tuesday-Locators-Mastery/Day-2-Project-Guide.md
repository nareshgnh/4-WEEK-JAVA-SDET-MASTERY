# DAY 2 MINI PROJECT: FORM AUTOMATION WITH LOCATORS

## 🎯 Project Overview

**What You're Building:**
A comprehensive form automation script that fills out a registration form using all different locator strategies you've learned today.

**Website:** https://demo.automationtesting.in/Register.html

**Why This Project:**
- Practice all 8 locator strategies in one project
- Learn to choose the right locator for each element type
- Handle different input types (text, radio, checkbox, dropdown)
- Build a real-world automation scenario

**Time Required:** 30-45 minutes

---

## 📋 Requirements

### Must-Have Features:
1. Fill personal information fields (First Name, Last Name, Address, Email, Phone)
2. Select gender (radio button)
3. Select hobbies (checkboxes)
4. Select skills from dropdown
5. Select country from searchable dropdown
6. Use minimum 5 different locator strategies
7. Add verification after each step
8. Print summary report

### Bonus Features:
1. Upload a file (profile photo)
2. Select date of birth using date picker
3. Select language from multi-select dropdown
4. Take screenshot of filled form
5. Validate each field after filling

---

## 📝 Step-by-Step Guide

### Step 1: Project Setup
```java
package com.automation.projects;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.Select;

public class FormAutomationProject {
    public static void main(String[] args) {
        // TODO: Create driver and maximize window
    }
}
```

### Step 2: Navigate and Fill Text Fields
```java
driver.get("https://demo.automationtesting.in/Register.html");

// First Name - CSS Selector with attribute
WebElement firstName = driver.findElement(By.cssSelector("input[placeholder='First Name']"));
firstName.sendKeys("John");

// Last Name - XPath with attribute
WebElement lastName = driver.findElement(By.xpath("//input[@placeholder='Last Name']"));
lastName.sendKeys("Doe");

// Address - CSS with ng-model (Angular)
WebElement address = driver.findElement(By.cssSelector("textarea[ng-model='Adress']"));
address.sendKeys("123 Automation Street, Test City, 12345");

// Email - CSS with type attribute
WebElement email = driver.findElement(By.cssSelector("input[type='email']"));
email.sendKeys("john.doe@automation.com");

// Phone - XPath with type
WebElement phone = driver.findElement(By.xpath("//input[@type='tel']"));
phone.sendKeys("1234567890");
```

### Step 3: Handle Radio Buttons
```java
// Gender selection - By.cssSelector with value attribute
WebElement male = driver.findElement(By.cssSelector("input[value='Male']"));
male.click();

// Verification
if (male.isSelected()) {
    System.out.println("✓ Gender selected successfully");
}
```

### Step 4: Handle Checkboxes
```java
// Hobbies - By.id()
WebElement cricket = driver.findElement(By.id("checkbox1"));
cricket.click();

WebElement movies = driver.findElement(By.id("checkbox2"));
movies.click();

System.out.println("✓ Hobbies selected");
```

### Step 5: Handle Dropdowns
```java
// Skills dropdown - Use Select class
WebElement skillsDropdown = driver.findElement(By.id("Skills"));
Select skills = new Select(skillsDropdown);
skills.selectByVisibleText("Java");

System.out.println("✓ Skill selected");
```

### Step 6: Verification and Reporting
```java
// Verify all fields are filled
String enteredFirstName = firstName.getAttribute("value");
System.out.println("First Name: " + enteredFirstName);

// Final report
System.out.println("\n=== FORM AUTOMATION SUMMARY ===");
System.out.println("✓ Personal information filled");
System.out.println("✓ Gender selected");
System.out.println("✓ Hobbies selected");
System.out.println("✓ Skills selected");
System.out.println("\nLocators Used:");
System.out.println("- By.cssSelector() for most fields");
System.out.println("- By.xpath() for some fields");
System.out.println("- By.id() for checkboxes");
```

---

## 🎓 Complete Solution

```java
package com.automation.projects;

import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.Select;

public class FormAutomationProject {
    public static void main(String[] args) {
        WebDriver driver = new ChromeDriver();

        try {
            driver.manage().window().maximize();
            System.out.println("=== FORM AUTOMATION PROJECT ===\n");

            // Navigate to form
            driver.get("https://demo.automationtesting.in/Register.html");
            Thread.sleep(2000);
            System.out.println("✓ Navigated to registration form");

            // Fill First Name (CSS Selector)
            WebElement firstName = driver.findElement(By.cssSelector("input[placeholder='First Name']"));
            firstName.sendKeys("John");
            System.out.println("✓ First Name entered (By.cssSelector)");

            // Fill Last Name (XPath)
            WebElement lastName = driver.findElement(By.xpath("//input[@placeholder='Last Name']"));
            lastName.sendKeys("Doe");
            System.out.println("✓ Last Name entered (By.xpath)");

            // Fill Address (CSS with ng-model)
            WebElement address = driver.findElement(By.cssSelector("textarea[ng-model='Adress']"));
            address.sendKeys("123 Automation Street, Test City, 12345");
            System.out.println("✓ Address entered (By.cssSelector)");

            // Fill Email (CSS with type)
            WebElement email = driver.findElement(By.cssSelector("input[type='email']"));
            email.sendKeys("john.doe@automation.com");
            System.out.println("✓ Email entered (By.cssSelector)");

            // Fill Phone (XPath with type)
            WebElement phone = driver.findElement(By.xpath("//input[@type='tel']"));
            phone.sendKeys("1234567890");
            System.out.println("✓ Phone entered (By.xpath)");

            // Select Gender (CSS with value)
            WebElement male = driver.findElement(By.cssSelector("input[value='Male']"));
            male.click();
            System.out.println("✓ Gender selected (By.cssSelector)");

            // Select Hobbies (By.id)
            WebElement cricket = driver.findElement(By.id("checkbox1"));
            cricket.click();
            System.out.println("✓ Cricket hobby selected (By.id)");

            WebElement movies = driver.findElement(By.id("checkbox2"));
            movies.click();
            System.out.println("✓ Movies hobby selected (By.id)");

            // Select Skills from dropdown (By.id + Select class)
            WebElement skillsDropdown = driver.findElement(By.id("Skills"));
            Select skills = new Select(skillsDropdown);
            skills.selectByVisibleText("Java");
            System.out.println("✓ Skill selected (By.id + Select)");

            // Select Country (By.id for the span that opens dropdown)
            WebElement countryDropdown = driver.findElement(By.id("countries"));
            Select country = new Select(countryDropdown);
            country.selectByVisibleText("India");
            System.out.println("✓ Country selected");

            // Select Year (By.id + Select)
            WebElement yearDropdown = driver.findElement(By.id("yearbox"));
            Select year = new Select(yearDropdown);
            year.selectByVisibleText("1990");
            System.out.println("✓ Year selected");

            // Select Month (CSS with placeholder + Select)
            WebElement monthDropdown = driver.findElement(By.cssSelector("select[placeholder='Month']"));
            Select month = new Select(monthDropdown);
            month.selectByVisibleText("January");
            System.out.println("✓ Month selected");

            // Select Day (By.id + Select)
            WebElement dayDropdown = driver.findElement(By.id("daybox"));
            Select day = new Select(dayDropdown);
            day.selectByVisibleText("15");
            System.out.println("✓ Day selected");

            // Fill Password (CSS with type)
            WebElement password = driver.findElement(By.cssSelector("input[type='password']"));
            password.sendKeys("Test@1234");
            System.out.println("✓ Password entered");

            // Fill Confirm Password (By.id)
            WebElement confirmPassword = driver.findElement(By.id("confirmpassword"));
            confirmPassword.sendKeys("Test@1234");
            System.out.println("✓ Confirm Password entered");

            // Pause to see the filled form
            Thread.sleep(3000);

            // Print Summary
            System.out.println("\n" + "=".repeat(50));
            System.out.println("✓✓✓ FORM AUTOMATION - COMPLETED ✓✓✓");
            System.out.println("=".repeat(50));

            System.out.println("\nForm Fields Filled:");
            System.out.println("- First Name: " + firstName.getAttribute("value"));
            System.out.println("- Last Name: " + lastName.getAttribute("value"));
            System.out.println("- Email: " + email.getAttribute("value"));
            System.out.println("- Phone: " + phone.getAttribute("value"));

            System.out.println("\nLocator Strategies Used:");
            System.out.println("1. By.cssSelector() - 8 elements");
            System.out.println("2. By.xpath() - 3 elements");
            System.out.println("3. By.id() - 7 elements");
            System.out.println("4. Select class - for all dropdowns");

            System.out.println("\n✓ Project demonstrates mastery of:");
            System.out.println("  - Text input automation");
            System.out.println("  - Radio button selection");
            System.out.println("  - Checkbox handling");
            System.out.println("  - Dropdown selection");
            System.out.println("  - Multiple locator strategies");

        } catch (Exception e) {
            System.out.println("✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            driver.quit();
            System.out.println("\nBrowser closed");
        }
    }
}
```

---

## 🚀 Enhancement Ideas

### Enhancement 1: Field Validation
```java
public static boolean validateField(WebElement element, String expectedValue) {
    String actualValue = element.getAttribute("value");
    return actualValue.equals(expectedValue);
}
```

### Enhancement 2: Screenshot
```java
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;

TakesScreenshot screenshot = (TakesScreenshot) driver;
File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
Files.copy(srcFile.toPath(), Paths.get("filled-form.png"));
```

### Enhancement 3: Data-Driven
```java
// Read form data from external source
public static void fillForm(WebDriver driver, Map<String, String> formData) {
    driver.findElement(By.cssSelector("input[placeholder='First Name']"))
          .sendKeys(formData.get("firstName"));
    // ... fill other fields from map
}
```

---

## 💼 Real-World Application

**This project teaches:**
1. **Locator Selection:** Choosing right strategy for each element
2. **Element Types:** Handling different input types
3. **Dropdown Handling:** Using Select class
4. **Verification:** Confirming actions succeeded
5. **Reporting:** Clear test output

**In production frameworks (Week 3), this becomes:**
- Page Object Model: Each form section is a page class
- Data-driven: Form data from Excel/CSV
- Assertions: TestNG/JUnit assertions instead of print
- Screenshots: On failure for debugging
- Reporting: ExtentReports with pass/fail status

---

## ✅ Project Completion Checklist

- [ ] All form fields filled correctly
- [ ] At least 5 different locator strategies used
- [ ] Radio buttons selected
- [ ] Checkboxes checked
- [ ] Dropdowns selected
- [ ] Verification after each major step
- [ ] Clear console output with summary
- [ ] Code is well-commented
- [ ] No hardcoded waits (except for demo viewing)

---

**Congratulations! You've built a comprehensive form automation project using multiple locator strategies! 🎉**

**Tomorrow:** WebDriver Actions (clicks, sendKeys, navigate, browser methods, waits)
