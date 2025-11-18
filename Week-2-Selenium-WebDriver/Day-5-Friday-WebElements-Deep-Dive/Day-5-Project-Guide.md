# DAY 5 MINI PROJECT: DYNAMIC WEB TABLE DATA EXTRACTION

## 🎯 Project Overview

**What You're Building:**
Complete dynamic web table scraper that handles stale elements, uses JavaScript Executor for hidden elements, and processes collections with findElements().

**Website:** https://the-internet.herokuapp.com/

**Why This Project:**
- Practice findElement vs findElements distinction
- Handle dynamic content and stale element exceptions
- Use JavaScript Executor for complex interactions
- Extract and process data from web tables
- Build real data scraping scenario

**Time Required:** 45-60 minutes

---

## 📋 Requirements

### Must-Have Features:
1. Navigate to dynamic table page
2. Extract all table headers
3. Find all table rows using findElements()
4. Loop through rows and extract cell data
5. Store data in appropriate Java collections
6. Handle dynamic loading content
7. Use JavaScript Executor to scroll and highlight elements
8. Handle stale element references properly
9. Print formatted output
10. Proper error handling and validation

### Bonus Features:
1. Sort table data by column
2. Search for specific data in table
3. Calculate totals/aggregates from numeric columns
4. Take screenshot of table
5. Export data to CSV file
6. Handle pagination if present
7. Compare data before and after refresh

---

## 🎓 Complete Solution

```java
package com.automation.projects;

import org.openqa.selenium.By;
import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.support.ui.ExpectedConditions;
import org.openqa.selenium.support.ui.WebDriverWait;

import java.time.Duration;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class DynamicTableExtractor {

    private static WebDriver driver;
    private static WebDriverWait wait;
    private static JavascriptExecutor js;

    public static void main(String[] args) {
        setupDriver();

        try {
            System.out.println("=== DYNAMIC TABLE DATA EXTRACTION ===\n");

            // Test Steps
            navigateToTablePage();
            extractTableHeaders();
            List<Map<String, String>> tableData = extractAllTableData();
            displayTableData(tableData);

            System.out.println("\n--- Testing Dynamic Content ---");
            testDynamicLoading();

            System.out.println("\n--- Testing JavaScript Executor ---");
            demonstrateJavaScriptExecutor();

            System.out.println("\n--- Testing Stale Element Handling ---");
            handleStaleElements();

            System.out.println("\n" + "=".repeat(50));
            System.out.println("✓✓✓ DATA EXTRACTION - PASSED ✓✓✓");
            System.out.println("=".repeat(50));

            Thread.sleep(2000);

        } catch (Exception e) {
            System.out.println("\n✗ Test failed: " + e.getMessage());
            e.printStackTrace();
        } finally {
            cleanup();
        }
    }

    private static void setupDriver() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(5));
        wait = new WebDriverWait(driver, Duration.ofSeconds(10));
        js = (JavascriptExecutor) driver;

        System.out.println("✓ Driver setup complete");
    }

    private static void navigateToTablePage() {
        driver.get("https://the-internet.herokuapp.com/tables");
        System.out.println("✓ Navigated to tables page");
    }

    private static void extractTableHeaders() {
        // Find all header cells using findElements
        List<WebElement> headers = driver.findElements(
            By.xpath("//table[@id='table1']//thead//th")
        );

        System.out.println("\n=== TABLE HEADERS ===");
        System.out.print("| ");
        for (WebElement header : headers) {
            System.out.print(header.getText() + " | ");
        }
        System.out.println("\n" + "-".repeat(80));

        System.out.println("✓ Extracted " + headers.size() + " headers");
    }

    private static List<Map<String, String>> extractAllTableData() {
        List<Map<String, String>> allData = new ArrayList<>();

        // Get all table headers first
        List<WebElement> headers = driver.findElements(
            By.xpath("//table[@id='table1']//thead//th")
        );
        List<String> headerNames = new ArrayList<>();
        for (WebElement header : headers) {
            headerNames.add(header.getText());
        }

        // Get all table rows (excluding header)
        List<WebElement> rows = driver.findElements(
            By.xpath("//table[@id='table1']//tbody//tr")
        );

        System.out.println("\n✓ Found " + rows.size() + " data rows");

        // Extract data from each row
        for (int i = 0; i < rows.size(); i++) {
            // Re-find row each iteration to avoid stale element
            WebElement row = driver.findElement(
                By.xpath("//table[@id='table1']//tbody//tr[" + (i + 1) + "]")
            );

            List<WebElement> cells = row.findElements(By.tagName("td"));

            // Store row data in map
            Map<String, String> rowData = new HashMap<>();
            for (int j = 0; j < cells.size() && j < headerNames.size(); j++) {
                String cellText = cells.get(j).getText();
                rowData.put(headerNames.get(j), cellText);
            }

            allData.add(rowData);
        }

        System.out.println("✓ Extracted data from all rows");
        return allData;
    }

    private static void displayTableData(List<Map<String, String>> data) {
        System.out.println("\n=== TABLE DATA ===\n");

        int rowNum = 1;
        for (Map<String, String> row : data) {
            System.out.println("Row " + rowNum + ":");
            System.out.println("  Last Name: " + row.get("Last Name"));
            System.out.println("  First Name: " + row.get("First Name"));
            System.out.println("  Email: " + row.get("Email"));
            System.out.println("  Due: $" + row.get("Due"));
            System.out.println("  Website: " + row.get("Web Site"));
            System.out.println();
            rowNum++;
        }

        System.out.println("✓ Displayed all " + data.size() + " rows");
    }

    private static void testDynamicLoading() {
        driver.get("https://the-internet.herokuapp.com/dynamic_loading/2");
        System.out.println("✓ Navigated to dynamic loading page");

        // Click start button
        WebElement startButton = driver.findElement(By.cssSelector("#start button"));
        startButton.click();
        System.out.println("✓ Clicked start button");

        // Wait for loading to complete
        wait.until(ExpectedConditions.invisibilityOfElementLocated(By.id("loading")));
        System.out.println("✓ Loading indicator disappeared");

        // Wait for result to appear
        WebElement result = wait.until(
            ExpectedConditions.visibilityOfElementLocated(By.id("finish"))
        );

        System.out.println("✓ Result appeared: " + result.getText());
    }

    private static void demonstrateJavaScriptExecutor() {
        driver.get("https://the-internet.herokuapp.com/infinite_scroll");
        System.out.println("✓ Navigated to infinite scroll page");

        // Scroll using JavaScript
        for (int i = 1; i <= 3; i++) {
            js.executeScript("window.scrollTo(0, document.body.scrollHeight)");
            System.out.println("✓ Scroll " + i + " completed");

            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }

        // Get page height using JavaScript
        Long pageHeight = (Long) js.executeScript("return document.body.scrollHeight");
        System.out.println("✓ Page height: " + pageHeight + " pixels");

        // Scroll to top
        js.executeScript("window.scrollTo(0, 0)");
        System.out.println("✓ Scrolled back to top");

        // Get current position
        Long scrollPosition = (Long) js.executeScript("return window.pageYOffset");
        System.out.println("✓ Current scroll position: " + scrollPosition);
    }

    private static void handleStaleElements() {
        driver.get("https://the-internet.herokuapp.com/tables");
        System.out.println("✓ Navigated to tables page");

        // Find element
        WebElement firstRow = driver.findElement(
            By.xpath("//table[@id='table1']//tbody//tr[1]")
        );
        String beforeRefresh = firstRow.findElement(By.xpath(".//td[1]")).getText();
        System.out.println("✓ Before refresh - First cell: " + beforeRefresh);

        // Refresh page (this will make the element stale)
        driver.navigate().refresh();
        System.out.println("✓ Page refreshed (element is now stale)");

        // Wait for table to be present again
        wait.until(ExpectedConditions.presenceOfElementLocated(By.id("table1")));

        // Re-find element (don't use old reference)
        WebElement refreshedFirstRow = driver.findElement(
            By.xpath("//table[@id='table1']//tbody//tr[1]")
        );
        String afterRefresh = refreshedFirstRow.findElement(By.xpath(".//td[1]")).getText();
        System.out.println("✓ After refresh - First cell: " + afterRefresh);

        // Verify data is the same
        if (beforeRefresh.equals(afterRefresh)) {
            System.out.println("✓ Data verified - stale element handled correctly");
        }
    }

    private static void cleanup() {
        if (driver != null) {
            driver.quit();
            System.out.println("\n✓ Browser closed");
        }
    }
}
```

---

## 🔍 Key Concepts Demonstrated

### 1. findElements() for Collections
```java
// Returns List<WebElement> - empty list if no matches
List<WebElement> rows = driver.findElements(
    By.xpath("//table[@id='table1']//tbody//tr")
);

System.out.println("Found " + rows.size() + " rows");

// Loop through all elements
for (WebElement row : rows) {
    String text = row.getText();
    System.out.println(text);
}
```

### 2. Avoiding Stale Element Exceptions
```java
// ❌ BAD - Stores element references
List<WebElement> rows = driver.findElements(By.xpath("//tr"));
for (WebElement row : rows) {
    // If page updates, row becomes stale
    row.click(); // May throw StaleElementReferenceException
}

// ✅ GOOD - Re-find each iteration
List<WebElement> rows = driver.findElements(By.xpath("//tr"));
for (int i = 0; i < rows.size(); i++) {
    // Re-find fresh element each time
    WebElement row = driver.findElement(By.xpath("//tr[" + (i + 1) + "]"));
    row.click(); // Safe!
}
```

### 3. JavaScript Executor Usage
```java
// Cast driver to JavascriptExecutor
JavascriptExecutor js = (JavascriptExecutor) driver;

// Scroll operations
js.executeScript("window.scrollTo(0, document.body.scrollHeight)");

// Get return values
Long height = (Long) js.executeScript("return document.body.scrollHeight");

// Interact with elements
js.executeScript("arguments[0].scrollIntoView(true);", element);
js.executeScript("arguments[0].click();", element);
```

### 4. Data Storage in Collections
```java
// List of Maps pattern for table data
List<Map<String, String>> allData = new ArrayList<>();

for (WebElement row : rows) {
    Map<String, String> rowData = new HashMap<>();
    rowData.put("Name", row.findElement(By.xpath(".//td[1]")).getText());
    rowData.put("Email", row.findElement(By.xpath(".//td[2]")).getText());
    allData.add(rowData);
}

// Access later
for (Map<String, String> row : allData) {
    System.out.println(row.get("Name") + " - " + row.get("Email"));
}
```

### 5. Dynamic Content Waiting
```java
// Click trigger
driver.findElement(By.id("start")).click();

// Wait for loader to disappear
wait.until(ExpectedConditions.invisibilityOfElementLocated(By.id("loading")));

// Wait for result to appear
WebElement result = wait.until(
    ExpectedConditions.visibilityOfElementLocated(By.id("finish"))
);
```

---

## 🚀 Enhancement Ideas

### Enhancement 1: Search for Specific Data
```java
private static Map<String, String> findRowByEmail(
    List<Map<String, String>> data,
    String email
) {
    for (Map<String, String> row : data) {
        if (row.get("Email").equals(email)) {
            return row;
        }
    }
    return null;
}

// Usage
Map<String, String> found = findRowByEmail(tableData, "jsmith@gmail.com");
if (found != null) {
    System.out.println("Found: " + found.get("First Name") + " " + found.get("Last Name"));
}
```

### Enhancement 2: Calculate Totals
```java
private static double calculateTotalDue(List<Map<String, String>> data) {
    double total = 0.0;

    for (Map<String, String> row : data) {
        String dueStr = row.get("Due").replace("$", "").trim();
        total += Double.parseDouble(dueStr);
    }

    return total;
}

// Usage
double total = calculateTotalDue(tableData);
System.out.println("Total Due: $" + String.format("%.2f", total));
```

### Enhancement 3: Sort Data
```java
import java.util.Comparator;

private static void sortByLastName(List<Map<String, String>> data) {
    data.sort(new Comparator<Map<String, String>>() {
        @Override
        public int compare(Map<String, String> row1, Map<String, String> row2) {
            return row1.get("Last Name").compareTo(row2.get("Last Name"));
        }
    });
}

// Or with Lambda (Java 8+)
private static void sortByLastNameLambda(List<Map<String, String>> data) {
    data.sort((row1, row2) ->
        row1.get("Last Name").compareTo(row2.get("Last Name"))
    );
}
```

### Enhancement 4: Export to CSV
```java
import java.io.FileWriter;
import java.io.IOException;

private static void exportToCSV(
    List<Map<String, String>> data,
    String filename
) throws IOException {
    FileWriter writer = new FileWriter(filename);

    // Write header
    if (!data.isEmpty()) {
        Map<String, String> firstRow = data.get(0);
        String header = String.join(",", firstRow.keySet());
        writer.write(header + "\n");

        // Write data rows
        for (Map<String, String> row : data) {
            String rowStr = String.join(",", row.values());
            writer.write(rowStr + "\n");
        }
    }

    writer.close();
    System.out.println("✓ Data exported to " + filename);
}

// Usage
exportToCSV(tableData, "table_data.csv");
```

### Enhancement 5: Highlight Elements with JavaScript
```java
private static void highlightElement(WebElement element) {
    String originalStyle = element.getAttribute("style");

    // Highlight in red
    js.executeScript(
        "arguments[0].setAttribute('style', 'border: 3px solid red; background: yellow;');",
        element
    );

    try {
        Thread.sleep(500);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }

    // Restore original style
    js.executeScript(
        "arguments[0].setAttribute('style', '" + originalStyle + "');",
        element
    );
}

// Usage - highlight each row as you process it
for (WebElement row : rows) {
    highlightElement(row);
    // Process row...
}
```

### Enhancement 6: Take Screenshot
```java
import org.openqa.selenium.OutputType;
import org.openqa.selenium.TakesScreenshot;
import java.io.File;
import java.nio.file.Files;
import java.nio.file.Paths;

private static void takeScreenshot(String filename) throws Exception {
    TakesScreenshot screenshot = (TakesScreenshot) driver;
    File srcFile = screenshot.getScreenshotAs(OutputType.FILE);
    Files.copy(srcFile.toPath(), Paths.get(filename + ".png"));
    System.out.println("✓ Screenshot saved: " + filename + ".png");
}

// Usage
takeScreenshot("table_data_screenshot");
```

---

## 💼 Real-World Application

**This project teaches:**
1. **Web Scraping Fundamentals:** Extracting structured data from HTML tables
2. **Collection Handling:** Using List and Map for structured data storage
3. **Stale Element Prevention:** Re-finding elements to avoid exceptions
4. **JavaScript Integration:** Using JavaScript for advanced browser control
5. **Dynamic Content Handling:** Waiting for AJAX-loaded content
6. **Data Processing:** Searching, sorting, and aggregating scraped data

**Real-World Use Cases:**
- **Price Monitoring:** Scrape competitor prices from product tables
- **Report Validation:** Extract table data and validate against expected values
- **Data Migration:** Extract data from legacy web apps for migration
- **Automated Reporting:** Scrape dashboard data for automated reports
- **Test Data Extraction:** Get test data from web-based admin panels

**In Production Frameworks:**
- Table extraction becomes reusable utility class
- Data models created for different table structures
- CSV/Excel export for test evidence
- Screenshots for test reports
- Retry logic for stale element handling

---

## 🎯 Testing Scenarios

### Scenario 1: Basic Table Extraction
**Steps:**
1. Navigate to table page
2. Extract all headers
3. Extract all row data
4. Print formatted output

**Expected:** All table data correctly extracted and displayed

### Scenario 2: Dynamic Content
**Steps:**
1. Navigate to dynamic loading page
2. Click start button
3. Wait for loading to complete
4. Verify result text appears

**Expected:** "Hello World!" text appears after loading

### Scenario 3: Stale Element Handling
**Steps:**
1. Find table row
2. Refresh page
3. Try to access same element (should fail)
4. Re-find element (should succeed)

**Expected:** Stale element handled without exception

### Scenario 4: JavaScript Scrolling
**Steps:**
1. Navigate to infinite scroll page
2. Execute scroll JavaScript 3 times
3. Verify page height increases
4. Scroll back to top

**Expected:** Page scrolls smoothly, height values returned

---

## ✅ Project Completion Checklist

**Data Extraction:**
- [ ] Table headers extracted correctly
- [ ] All rows extracted using findElements()
- [ ] Data stored in List<Map> structure
- [ ] Formatted output displays all data
- [ ] No hard-coded row counts (uses .size())

**Dynamic Content:**
- [ ] Dynamic loading page handled
- [ ] Waits for loading indicator to disappear
- [ ] Result text extracted correctly
- [ ] No Thread.sleep() used (only WebDriverWait)

**JavaScript Executor:**
- [ ] Driver cast to JavascriptExecutor
- [ ] Scroll operations work correctly
- [ ] Return values retrieved from JavaScript
- [ ] Page height calculations accurate

**Stale Element Handling:**
- [ ] Elements re-found after page refresh
- [ ] No StaleElementReferenceException thrown
- [ ] Data verified before and after refresh
- [ ] Proper wait after refresh

**Code Quality:**
- [ ] Methods well-organized and named
- [ ] Proper exception handling
- [ ] Console output is clear and informative
- [ ] Driver cleanup in finally block
- [ ] No magic numbers (use constants)

**Bonus Challenges:**
- [ ] Search functionality implemented
- [ ] Sorting by column works
- [ ] Total calculation accurate
- [ ] CSV export successful
- [ ] Screenshot capture works

---

## 📚 Learning Outcomes

After completing this project, you should be able to:

✅ Distinguish between `findElement()` (single) and `findElements()` (collection)
✅ Loop through element collections safely
✅ Store web data in Java collections (List, Map)
✅ Handle stale element exceptions by re-finding elements
✅ Use JavascriptExecutor for advanced browser control
✅ Wait for dynamic content to load properly
✅ Extract and process structured data from web tables
✅ Build reusable data extraction utilities

---

## 🎓 Interview Questions You Can Answer

**Q: What's the difference between findElement and findElements?**
A: `findElement()` returns a single WebElement and throws NoSuchElementException if not found. `findElements()` returns List<WebElement> and returns empty list if no matches - never throws exception.

**Q: How do you handle stale element exceptions?**
A: Re-find the element after any page change (refresh, navigation, DOM update). Don't reuse old WebElement references after the page changes.

**Q: When would you use JavascriptExecutor?**
A: For scrolling, clicking elements blocked by overlays, getting/setting element properties directly, or executing complex browser operations not possible with WebDriver API.

**Q: How do you extract all data from a table?**
A: Use `findElements()` to get all rows, loop through each row, use `findElements()` again to get cells in each row, store data in List<Map<String,String>> structure.

---

**Congratulations! You've built a production-ready data extraction utility with proper element handling! 🎉**

**Tomorrow:** Page Object Model (Learn to structure test code professionally)
