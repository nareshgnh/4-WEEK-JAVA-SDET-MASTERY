# DAY 3 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master data-driven testing by creating tests with @DataProvider, reading from Excel/CSV files, and building data-driven Selenium tests.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Basic @DataProvider
**Objective:** Create your first data-driven test

**Task:**
```java
import org.testng.Assert;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

public class Exercise1_BasicDataProvider {

    @DataProvider(name = "numbers")
    public Object[][] getNumbers() {
        // TODO: Return 2D array with 5 rows of [number, square]
        // Example: {2, 4}, {3, 9}, {4, 16}, etc.
        return new Object[][] {
            // TODO
        };
    }

    @Test(dataProvider = "numbers")
    public void testSquare(int number, int expectedSquare) {
        // TODO: Calculate square and assert
        int actualSquare = number * number;
        // TODO: Add assertion
    }
}
```

**Expected Output:** Test runs 5 times, all pass

---

### Exercise 2: String Data Provider
**Objective:** Work with string data

**Task:**
```java
@DataProvider(name = "emails")
public Object[][] getEmailData() {
    // TODO: Return emails with validation status
    return new Object[][] {
        {"user@example.com", true},
        {"invalid.email", false},
        {"test@test.com", true},
        {"@nodomain.com", false}
    };
}

@Test(dataProvider = "emails")
public void testEmailValidation(String email, boolean shouldBeValid) {
    boolean isValid = email.contains("@") && email.contains(".");
    Assert.assertEquals(isValid, shouldBeValid,
        "Email '" + email + "' validation failed");
}
```

---

### Exercise 3: CSV Reading
**Objective:** Read data from CSV file

**Task:**
1. Create `testdata.csv`:
```csv
username,password
user1,pass123
user2,pass456
user3,pass789
```

2. Implement CSVUtil and use it:
```java
public class Exercise3_CSVReader {

    @DataProvider(name = "csvData")
    public Object[][] getCSVData() throws IOException {
        // TODO: Read from CSV file
        return CSVUtil.getCSVData("src/test/resources/testdata.csv");
    }

    @Test(dataProvider = "csvData")
    public void testLogin(String username, String password) {
        System.out.println("Testing: " + username + " / " + password);
        Assert.assertNotNull(username);
        Assert.assertNotNull(password);
    }
}
```

---

## 💪 Core Practice (30 min)

### Exercise 4: Data-Driven Selenium Login Test
**Objective:** Build real data-driven Selenium test

**Problem Statement:**
Test Sauce Demo login with multiple users

**Starter Code:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import java.time.Duration;

public class Exercise4_SauceDemoDataDriven {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        driver.get("https://www.saucedemo.com");
    }

    @DataProvider(name = "validUsers")
    public Object[][] getValidUsers() {
        return new Object[][] {
            {"standard_user", "secret_sauce", true},
            {"locked_out_user", "secret_sauce", false},
            {"problem_user", "secret_sauce", true},
            {"performance_glitch_user", "secret_sauce", true}
        };
    }

    @Test(dataProvider = "validUsers")
    public void testLogin(String username, String password, boolean shouldSucceed) {
        // TODO: Implement login
        driver.findElement(By.id("user-name")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login-button")).click();

        // TODO: Verify based on shouldSucceed
        if (shouldSucceed) {
            Assert.assertTrue(driver.getCurrentUrl().contains("inventory"),
                username + " should login successfully");
        } else {
            Assert.assertTrue(driver.findElement(By.cssSelector("[data-test='error']")).isDisplayed(),
                username + " should show error");
        }
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) driver.quit();
    }
}
```

---

### Exercise 5: Excel-Driven Tests
**Objective:** Read test data from Excel

**Setup:**
Create `testdata.xlsx` with sheet "SearchData":
```
keyword    | expectedMinResults
Selenium   | 5
TestNG     | 3
Java       | 10
```

**Code:**
```java
public class Exercise5_ExcelDriven {

    @DataProvider(name = "searchData")
    public Object[][] getSearchData() throws IOException {
        return ExcelUtil.getExcelData("src/test/resources/testdata.xlsx", "SearchData");
    }

    @Test(dataProvider = "searchData")
    public void testSearch(String keyword, String minResults) {
        System.out.println("Search: " + keyword + " (expect min " + minResults + ")");
        // In real test, perform search and verify
        Assert.assertNotNull(keyword);
    }
}
```

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: Complete Data-Driven Framework
**Objective:** Build comprehensive data-driven test suite

**Requirements:**
1. CSV file with test data
2. Utility class to read CSV
3. Multiple data-driven tests
4. Selenium integration
5. Proper assertions

**CSV File (login_scenarios.csv):**
```csv
testCase,username,password,expectedResult,description
TC001,standard_user,secret_sauce,success,Valid user login
TC002,locked_out_user,secret_sauce,locked,Locked user
TC003,invalid_user,wrong_pass,error,Invalid credentials
TC004,,secret_sauce,error,Empty username
TC005,standard_user,,error,Empty password
```

**Implementation:**
```java
public class Exercise6_Framework {

    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.get("https://www.saucedemo.com");
    }

    @DataProvider(name = "loginScenarios")
    public Object[][] getLoginScenarios() throws IOException {
        return CSVUtil.getCSVData("src/test/resources/login_scenarios.csv");
    }

    @Test(dataProvider = "loginScenarios")
    public void testLoginScenarios(String testCase, String username, String password,
                                   String expectedResult, String description) {
        System.out.println("Running: " + testCase + " - " + description);

        // Perform login
        if (!username.isEmpty()) {
            driver.findElement(By.id("user-name")).sendKeys(username);
        }
        if (!password.isEmpty()) {
            driver.findElement(By.id("password")).sendKeys(password);
        }
        driver.findElement(By.id("login-button")).click();

        // Verify result
        switch (expectedResult) {
            case "success":
                Assert.assertTrue(driver.getCurrentUrl().contains("inventory"),
                    testCase + " should succeed");
                break;
            case "locked":
            case "error":
                Assert.assertTrue(driver.findElement(By.cssSelector("[data-test='error']")).isDisplayed(),
                    testCase + " should show error");
                break;
        }
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) driver.quit();
    }
}
```

---

## 🎯 Solutions

### Exercise 1 Solution
```java
@DataProvider(name = "numbers")
public Object[][] getNumbers() {
    return new Object[][] {
        {2, 4},
        {3, 9},
        {4, 16},
        {5, 25},
        {10, 100}
    };
}

@Test(dataProvider = "numbers")
public void testSquare(int number, int expectedSquare) {
    int actualSquare = number * number;
    Assert.assertEquals(actualSquare, expectedSquare,
        "Square of " + number + " should be " + expectedSquare);
}
```

### CSVUtil Implementation
```java
import java.io.*;
import java.util.*;

public class CSVUtil {
    public static Object[][] getCSVData(String filePath) throws IOException {
        List<String[]> data = new ArrayList<>();
        BufferedReader br = new BufferedReader(new FileReader(filePath));

        String line;
        boolean firstLine = true;

        while ((line = br.readLine()) != null) {
            if (firstLine) {
                firstLine = false;
                continue;
            }
            data.add(line.split(","));
        }

        br.close();

        Object[][] result = new Object[data.size()][];
        for (int i = 0; i < data.size(); i++) {
            result[i] = data.get(i);
        }

        return result;
    }
}
```

### ExcelUtil Implementation
```java
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import java.io.*;

public class ExcelUtil {
    public static Object[][] getExcelData(String filePath, String sheetName) throws IOException {
        FileInputStream fis = new FileInputStream(filePath);
        Workbook workbook = new XSSFWorkbook(fis);
        Sheet sheet = workbook.getSheet(sheetName);

        int rowCount = sheet.getLastRowNum();
        int colCount = sheet.getRow(0).getLastCellNum();

        Object[][] data = new Object[rowCount][colCount];

        for (int i = 0; i < rowCount; i++) {
            Row row = sheet.getRow(i + 1);
            for (int j = 0; j < colCount; j++) {
                Cell cell = row.getCell(j);
                data[i][j] = getCellValue(cell);
            }
        }

        workbook.close();
        fis.close();
        return data;
    }

    private static String getCellValue(Cell cell) {
        if (cell == null) return "";
        switch (cell.getCellType()) {
            case STRING: return cell.getStringCellValue();
            case NUMERIC: return String.valueOf((int) cell.getNumericCellValue());
            case BOOLEAN: return String.valueOf(cell.getBooleanCellValue());
            default: return "";
        }
    }
}
```

---

## ✅ Self-Check Answers

1. **What does @DataProvider return?** - Object[][] (2D array)
2. **How do you link a DataProvider to a test?** - `@Test(dataProvider = "name")`
3. **If DataProvider returns 10 rows, how many times does test execute?** - 10 times
4. **What library for Excel?** - Apache POI
5. **CSV advantage?** - Simple text format, no library needed

---

**🎉 Day 3 Practice Complete! Move to Day-3-Debug-Practice.md**
