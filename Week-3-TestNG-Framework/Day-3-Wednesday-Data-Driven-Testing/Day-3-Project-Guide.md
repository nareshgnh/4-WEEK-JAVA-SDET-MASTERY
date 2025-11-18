# DAY 3 PROJECT: DATA-DRIVEN LOGIN TESTS

## 🎯 Project Overview
Build a complete data-driven test framework for Sauce Demo with test data in CSV/Excel files.

## 📋 Requirements
1. CSV file with login test scenarios
2. Excel file with product search data
3. Utility classes (CSVUtil, ExcelUtil)
4. Data-driven Selenium tests
5. Proper assertions and reporting

## 🏗️ Project Structure
```
src/test/
├── java/
│   ├── utils/
│   │   ├── CSVUtil.java
│   │   └── ExcelUtil.java
│   └── tests/
│       ├── DataDrivenLoginTest.java
│       └── DataDrivenSearchTest.java
└── resources/
    ├── login_data.csv
    └── search_data.xlsx
```

## 📝 Step-by-Step Implementation

### Step 1: Create CSV Test Data
**File: src/test/resources/login_data.csv**
```csv
testCase,username,password,expectedResult,description
TC001,standard_user,secret_sauce,success,Valid login
TC002,locked_out_user,secret_sauce,locked,Locked user error
TC003,invalid_user,wrong_pass,error,Invalid credentials
TC004,standard_user,wrong_pass,error,Wrong password
TC005,,secret_sauce,error,Empty username
```

### Step 2: Create Excel Test Data
**File: src/test/resources/search_data.xlsx**
- Sheet: SearchData
- Columns: keyword, expectedMinResults

### Step 3: Create CSVUtil
```java
package utils;

import java.io.*;
import java.util.*;

public class CSVUtil {
    public static Object[][] getCSVData(String filePath) throws IOException {
        List<String[]> data = new ArrayList<>();
        BufferedReader br = new BufferedReader(new FileReader(filePath));

        String line;
        br.readLine(); // Skip header

        while ((line = br.readLine()) != null) {
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

### Step 4: Create ExcelUtil
```java
package utils;

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
                data[i][j] = getCellValue(row.getCell(j));
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
            default: return "";
        }
    }
}
```

### Step 5: Create Data-Driven Tests
```java
package tests;

import org.openqa.selenium.*;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;
import utils.CSVUtil;
import java.time.Duration;

public class DataDrivenLoginTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.manage().window().maximize();
        driver.manage().timeouts().implicitlyWait(Duration.ofSeconds(10));
        driver.get("https://www.saucedemo.com");
    }

    @DataProvider(name = "loginData")
    public Object[][] getLoginData() throws Exception {
        return CSVUtil.getCSVData("src/test/resources/login_data.csv");
    }

    @Test(dataProvider = "loginData")
    public void testLogin(String testCase, String username, String password,
                         String expectedResult, String description) {
        System.out.println("Executing: " + testCase + " - " + description);

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
                    testCase + ": Should login successfully");
                System.out.println("✓ " + testCase + " PASSED");
                break;
            case "locked":
            case "error":
                WebElement error = driver.findElement(By.cssSelector("[data-test='error']"));
                Assert.assertTrue(error.isDisplayed(),
                    testCase + ": Should show error message");
                System.out.println("✓ " + testCase + " PASSED");
                break;
        }
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) driver.quit();
    }
}
```

## ✅ Testing Checklist
- [ ] CSV file created with test data
- [ ] Excel file created with search data
- [ ] CSVUtil reads CSV correctly
- [ ] ExcelUtil reads Excel correctly
- [ ] All login scenarios pass
- [ ] Error handling for invalid data
- [ ] Clear test reports with descriptions

## 💼 Real-World Application
This pattern is used in production frameworks:
- Business analysts maintain test data in Excel
- Hundreds of test scenarios without code changes
- Easy to add new test cases
- Centralized test data management

---

**🎉 Day 3 Project Complete! Move to Day-3-Cheat-Sheet.md**
