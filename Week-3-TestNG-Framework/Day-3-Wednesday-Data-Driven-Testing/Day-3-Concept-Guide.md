# DAY 3: DATA-DRIVEN TESTING

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master @DataProvider for parameterized tests
- [ ] Read test data from Excel files using Apache POI
- [ ] Read test data from CSV files
- [ ] Create data-driven login tests with multiple datasets
- [ ] Understand when to use data-driven testing in automation

**Time Required:** 3 hours
**Difficulty:** Medium-Advanced
**Prerequisite:** Day 1-2 TestNG knowledge

---

## 📚 Core Concepts

### Concept 1: What is Data-Driven Testing?

**What is it?**
Running the same test with different sets of data. Instead of writing 10 separate tests for 10 different logins, you write ONE test and pass it 10 different datasets.

**Why does it matter for automation?**
- **Test login with 50 username/password combinations** - one test, 50 datasets
- **Test search with 100 keywords** - one test, 100 datasets
- **Test form validation with invalid inputs** - one test, many error scenarios
- **Reduces code duplication** - write once, test many scenarios

**Without Data-Driven Testing (Bad):**
```java
@Test
public void testLogin1() {
    login("user1@example.com", "pass123");
    Assert.assertTrue(isLoggedIn());
}

@Test
public void testLogin2() {
    login("user2@example.com", "pass456");
    Assert.assertTrue(isLoggedIn());
}

@Test
public void testLogin3() {
    login("user3@example.com", "pass789");
    Assert.assertTrue(isLoggedIn());
}
// Repeat for 50 users... 😩
```

**With Data-Driven Testing (Good):**
```java
@DataProvider(name = "loginData")
public Object[][] getLoginData() {
    return new Object[][] {
        {"user1@example.com", "pass123"},
        {"user2@example.com", "pass456"},
        {"user3@example.com", "pass789"}
        // ... 47 more
    };
}

@Test(dataProvider = "loginData")
public void testLogin(String username, String password) {
    login(username, password);
    Assert.assertTrue(isLoggedIn());
}
// One test, 50 executions! 🎉
```

---

### Concept 2: @DataProvider Basics

**What is it?**
A TestNG annotation that provides data to test methods. It returns a 2D array where each row is one test execution.

**Syntax:**
```java
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;
import org.testng.Assert;

public class DataProviderExample {

    // Step 1: Create DataProvider method
    @DataProvider(name = "searchData")
    public Object[][] getSearchData() {
        return new Object[][] {
            {"Selenium", 10},      // Row 1: search "Selenium", expect 10+ results
            {"TestNG", 5},         // Row 2: search "TestNG", expect 5+ results
            {"Java", 20}           // Row 3: search "Java", expect 20+ results
        };
    }

    // Step 2: Use DataProvider in test
    @Test(dataProvider = "searchData")
    public void testSearch(String keyword, int expectedCount) {
        int actualCount = performSearch(keyword);
        Assert.assertTrue(actualCount >= expectedCount,
            "Search for '" + keyword + "' should return at least " + expectedCount + " results");
    }

    private int performSearch(String keyword) {
        // Mock implementation
        return 15;
    }
}
```

**Execution Flow:**
```
Test runs 3 times:
1. testSearch("Selenium", 10)
2. testSearch("TestNG", 5)
3. testSearch("Java", 20)

Each execution is reported separately in TestNG results
```

**Real Selenium Example:**
```java
import org.openqa.selenium.By;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.chrome.ChromeDriver;
import org.testng.Assert;
import org.testng.annotations.*;

public class SauceDemoLoginTest {
    WebDriver driver;

    @BeforeMethod
    public void setup() {
        driver = new ChromeDriver();
        driver.get("https://www.saucedemo.com");
    }

    @DataProvider(name = "validUsers")
    public Object[][] getValidUsers() {
        return new Object[][] {
            {"standard_user", "secret_sauce"},
            {"problem_user", "secret_sauce"},
            {"performance_glitch_user", "secret_sauce"}
        };
    }

    @Test(dataProvider = "validUsers")
    public void testValidLogin(String username, String password) {
        driver.findElement(By.id("user-name")).sendKeys(username);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.id("login-button")).click();

        Assert.assertTrue(driver.getCurrentUrl().contains("inventory"),
            "User '" + username + "' should be able to login");
    }

    @AfterMethod
    public void teardown() {
        if (driver != null) driver.quit();
    }
}
```

**DataProvider with Multiple Data Types:**
```java
@DataProvider(name = "registrationData")
public Object[][] getRegistrationData() {
    return new Object[][] {
        // String, int, boolean, String
        {"John Doe", 25, true, "john@example.com"},
        {"Jane Smith", 30, false, "jane@example.com"},
        {"Bob Johnson", 22, true, "bob@example.com"}
    };
}

@Test(dataProvider = "registrationData")
public void testRegistration(String name, int age, boolean subscribe, String email) {
    // Test with different user data
}
```

---

### Concept 3: Reading Data from Excel (Apache POI)

**What is it?**
Apache POI is a Java library for reading/writing Microsoft Office files (Excel, Word). We use it to read test data from Excel files.

**Why Excel?**
- Non-technical stakeholders can maintain test data
- Easy to organize (sheets, columns, rows)
- Can store hundreds/thousands of test cases
- Business analysts can add test scenarios

**Setup (pom.xml):**
```xml
<dependencies>
    <dependency>
        <groupId>org.apache.poi</groupId>
        <artifactId>poi</artifactId>
        <version>5.2.3</version>
    </dependency>
    <dependency>
        <groupId>org.apache.poi</groupId>
        <artifactId>poi-ooxml</artifactId>
        <version>5.2.3</version>
    </dependency>
</dependencies>
```

**Excel File Structure (testdata.xlsx):**
```
Sheet: LoginData
A          | B
username   | password
-----------|-------------
user1      | pass123
user2      | pass456
user3      | pass789
```

**Code to Read Excel:**
```java
import org.apache.poi.ss.usermodel.*;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import java.io.FileInputStream;
import java.io.IOException;

public class ExcelUtil {

    public static Object[][] getExcelData(String filePath, String sheetName) throws IOException {
        FileInputStream fis = new FileInputStream(filePath);
        Workbook workbook = new XSSFWorkbook(fis);
        Sheet sheet = workbook.getSheet(sheetName);

        int rowCount = sheet.getLastRowNum();  // Excluding header
        int colCount = sheet.getRow(0).getLastCellNum();

        Object[][] data = new Object[rowCount][colCount];

        for (int i = 0; i < rowCount; i++) {
            Row row = sheet.getRow(i + 1);  // Skip header row
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
            case STRING:
                return cell.getStringCellValue();
            case NUMERIC:
                return String.valueOf((int) cell.getNumericCellValue());
            case BOOLEAN:
                return String.valueOf(cell.getBooleanCellValue());
            default:
                return "";
        }
    }
}
```

**Using Excel in DataProvider:**
```java
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

public class ExcelDataDrivenTest {

    @DataProvider(name = "loginData")
    public Object[][] getLoginDataFromExcel() throws IOException {
        String filePath = "src/test/resources/testdata.xlsx";
        return ExcelUtil.getExcelData(filePath, "LoginData");
    }

    @Test(dataProvider = "loginData")
    public void testLogin(String username, String password) {
        System.out.println("Testing login with: " + username + " / " + password);
        // Perform actual login test
    }
}
```

---

### Concept 4: Reading Data from CSV Files

**What is it?**
CSV (Comma-Separated Values) is a simple text format for storing tabular data. Easier than Excel, no external library needed.

**CSV File (testdata.csv):**
```
username,password,expectedResult
standard_user,secret_sauce,success
locked_out_user,secret_sauce,locked
invalid_user,wrong_pass,error
```

**Code to Read CSV:**
```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class CSVUtil {

    public static Object[][] getCSVData(String filePath) throws IOException {
        List<String[]> data = new ArrayList<>();
        BufferedReader br = new BufferedReader(new FileReader(filePath));

        String line;
        boolean firstLine = true;

        while ((line = br.readLine()) != null) {
            if (firstLine) {
                firstLine = false;  // Skip header
                continue;
            }
            String[] values = line.split(",");
            data.add(values);
        }

        br.close();

        // Convert List to Object[][]
        Object[][] result = new Object[data.size()][];
        for (int i = 0; i < data.size(); i++) {
            result[i] = data.get(i);
        }

        return result;
    }
}
```

**Using CSV in DataProvider:**
```java
@DataProvider(name = "csvData")
public Object[][] getLoginDataFromCSV() throws IOException {
    return CSVUtil.getCSVData("src/test/resources/testdata.csv");
}

@Test(dataProvider = "csvData")
public void testLogin(String username, String password, String expectedResult) {
    System.out.println("Testing: " + username + " -> " + expectedResult);
    // Perform test and verify expected result
}
```

---

## 🐍 Python vs Java Comparison

### Data-Driven Testing in Python (Pytest)
```python
import pytest

# Using pytest.mark.parametrize
@pytest.mark.parametrize("username,password", [
    ("user1", "pass1"),
    ("user2", "pass2"),
    ("user3", "pass3"),
])
def test_login(username, password):
    # Test runs 3 times
    login(username, password)
    assert is_logged_in()

# Reading from CSV
import csv

def get_csv_data():
    with open('testdata.csv') as f:
        return list(csv.reader(f))

@pytest.mark.parametrize("username,password", get_csv_data())
def test_login_from_csv(username, password):
    login(username, password)
```

### Data-Driven Testing in Java (TestNG)
```java
// Using @DataProvider
@DataProvider(name = "loginData")
public Object[][] getLoginData() {
    return new Object[][] {
        {"user1", "pass1"},
        {"user2", "pass2"},
        {"user3", "pass3"}
    };
}

@Test(dataProvider = "loginData")
public void testLogin(String username, String password) {
    // Test runs 3 times
    login(username, password);
    Assert.assertTrue(isLoggedIn());
}

// Reading from CSV
@DataProvider(name = "csvData")
public Object[][] getCSVData() throws IOException {
    return CSVUtil.getCSVData("testdata.csv");
}

@Test(dataProvider = "csvData")
public void testLoginFromCSV(String username, String password) {
    login(username, password);
}
```

**Key Differences:**

| Aspect | Python (Pytest) | Java (TestNG) |
|--------|----------------|---------------|
| **Decorator** | `@pytest.mark.parametrize` | `@DataProvider` |
| **Method** | Direct decorator on test | Separate method + link |
| **Return Type** | List/tuple | `Object[][]` (2D array) |
| **CSV Reading** | `csv.reader()` built-in | Custom util or Apache Commons CSV |
| **Excel Reading** | `openpyxl`, `pandas` | Apache POI library |

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: DataProvider name mismatch

❌ **Wrong:**
```java
@DataProvider(name = "loginData")
public Object[][] getData() {
    return new Object[][] { {"user", "pass"} };
}

@Test(dataProvider = "loginDta")  // Typo!
public void testLogin(String u, String p) { }
```

**Error:** `org.testng.TestNGException: Data Provider loginDta not found`

✅ **Correct:**
```java
@DataProvider(name = "loginData")
public Object[][] getData() {
    return new Object[][] { {"user", "pass"} };
}

@Test(dataProvider = "loginData")  // Matches exactly
public void testLogin(String u, String p) { }
```

---

### Mistake 2: Parameter count mismatch

❌ **Wrong:**
```java
@DataProvider(name = "data")
public Object[][] getData() {
    return new Object[][] {
        {"user1", "pass1"},  // 2 values per row
        {"user2", "pass2"}
    };
}

@Test(dataProvider = "data")
public void test(String username) {  // Only 1 parameter!
    // TestNG error: wrong number of parameters
}
```

✅ **Correct:**
```java
@Test(dataProvider = "data")
public void test(String username, String password) {  // 2 parameters to match 2 values
    login(username, password);
}
```

---

### Mistake 3: Null pointer from Excel empty cells

❌ **Wrong:**
```java
private static String getCellValue(Cell cell) {
    return cell.getStringCellValue();  // NPE if cell is null!
}
```

✅ **Correct:**
```java
private static String getCellValue(Cell cell) {
    if (cell == null) return "";  // Handle null cells

    switch (cell.getCellType()) {
        case STRING: return cell.getStringCellValue();
        case NUMERIC: return String.valueOf((int) cell.getNumericCellValue());
        default: return "";
    }
}
```

---

## 💡 Pro Tips

**Tip 1: Use meaningful DataProvider names**
```java
@DataProvider(name = "validLoginCredentials")  // Clear purpose
@DataProvider(name = "invalidEmailFormats")    // Descriptive
@DataProvider(name = "boundaryAgeValues")      // Specific
```

**Tip 2: Store test data files in resources folder**
```
src/
  test/
    resources/
      testdata/
        login.csv
        users.xlsx
        products.json
```

**Tip 3: Create reusable utility classes**
```java
public class TestDataUtil {
    public static Object[][] getExcelData(String file, String sheet) { }
    public static Object[][] getCSVData(String file) { }
    public static Object[][] getJSONData(String file) { }
}
```

**Tip 4: Include test data in error messages**
```java
@Test(dataProvider = "loginData")
public void testLogin(String user, String pass) {
    boolean success = login(user, pass);
    Assert.assertTrue(success,
        "Login failed for user: " + user + " with password: " + pass);
        // Makes debugging easier!
}
```

---

## 🎓 Interview Prep

**Q1: What is data-driven testing and why do we use it?**

**A:** Data-driven testing means running the same test with different datasets. Instead of writing 50 separate tests for 50 different login scenarios, I write one test method and use TestNG's @DataProvider to supply 50 different username/password combinations. This reduces code duplication, makes tests easier to maintain, and allows non-technical team members to add test scenarios by simply adding rows to an Excel file.

**Q2: How would you implement data-driven testing in TestNG?**

**A:**
```java
// 1. Create DataProvider method
@DataProvider(name = "loginData")
public Object[][] getLoginData() throws IOException {
    // Can return hardcoded data, or read from Excel/CSV
    return ExcelUtil.getExcelData("testdata.xlsx", "LoginSheet");
}

// 2. Link DataProvider to test
@Test(dataProvider = "loginData")
public void testLogin(String username, String password) {
    login(username, password);
    Assert.assertTrue(isLoggedIn());
}

// Test runs once for each row in the DataProvider
```

**Q3: What's the advantage of reading test data from Excel?**

**A:** Reading from Excel allows business analysts and manual QA to contribute test scenarios without writing code. They can add new test cases by adding rows to the Excel file. It also centralizes test data management - if we need to update a password for testing, we change it in one place (Excel) rather than searching through code.

---

## ✅ Self-Check Questions

1. **What does @DataProvider return?**
2. **How do you link a DataProvider to a test method?**
3. **If DataProvider returns 10 rows, how many times does the test execute?**
4. **What library do we use to read Excel files in Java?**
5. **What's the advantage of CSV over Excel for test data?**

**Answers in Practice file.**

---

**🎯 Ready for data-driven practice? Move to Day-3-Practice-Exercises.md**
