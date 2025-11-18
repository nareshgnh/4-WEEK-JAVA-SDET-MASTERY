# DAY 3 CHEAT SHEET: DATA-DRIVEN TESTING

## ⚡ Quick Syntax

### Basic DataProvider
```java
@DataProvider(name = "data")
public Object[][] getData() {
    return new Object[][] {
        {"value1", 123},
        {"value2", 456}
    };
}

@Test(dataProvider = "data")
public void test(String str, int num) {
    // Runs twice
}
```

### CSV Reading
```java
@DataProvider(name = "csvData")
public Object[][] getCSVData() throws IOException {
    return CSVUtil.getCSVData("file.csv");
}

@Test(dataProvider = "csvData")
public void test(String col1, String col2) { }
```

### Excel Reading
```java
@DataProvider(name = "excelData")
public Object[][] getExcelData() throws IOException {
    return ExcelUtil.getExcelData("file.xlsx", "Sheet1");
}

@Test(dataProvider = "excelData")
public void test(String col1, String col2) { }
```

## 🔑 Key Concepts

| Concept | Syntax | Use Case |
|---------|--------|----------|
| **DataProvider** | `@DataProvider(name = "data")` | Supply test data |
| **Link to Test** | `@Test(dataProvider = "data")` | Connect data to test |
| **Return Type** | `Object[][]` | 2D array of test data |
| **CSV** | `CSVUtil.getCSVData()` | Read from CSV |
| **Excel** | `ExcelUtil.getExcelData()` | Read from Excel (Apache POI) |
| **Shared DP** | `static Object[][] getData()` | Use across classes |

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Name mismatch** | `dataProvider = "loginDta"` | Match exactly: `"loginData"` |
| **Param count** | 2 params for 3 values | Match parameter count |
| **File path** | `"testdata.xlsx"` | `"src/test/resources/testdata.xlsx"` |
| **Null cell** | `cell.getStringCellValue()` | Check null first |
| **Not static** | Used across classes | Make DataProvider static |

## 💡 Key Takeaways

**Today You Learned:**
- ✅ @DataProvider enables data-driven testing
- ✅ One test method, multiple executions with different data
- ✅ Read from CSV (simple), Excel (Apache POI)
- ✅ Reduces code duplication dramatically
- ✅ Business analysts can maintain test data

**Critical for Automation:**
- 🎯 Test same flow with many datasets
- 🎯 Separate test logic from test data
- 🎯 Non-technical people can add test scenarios

## 🎤 Interview Phrase

*"I use TestNG's @DataProvider for data-driven testing. Instead of writing 50 separate login tests, I write one test method and use @DataProvider to supply 50 different username/password combinations from a CSV or Excel file. This separates test data from test logic, reduces code duplication, and allows business analysts to add test scenarios without touching code."*

## 📌 Remember This

> **Data-Driven Testing = One Test, Many Datasets**
> ```java
> @DataProvider → Returns Object[][]
> @Test(dataProvider = "name") → Receives data as parameters
> Test runs N times for N rows of data
> ```

## 🔮 Tomorrow's Preview

**Day 4:** testng.xml configuration - suites, tests, parallel execution

---

**🎉 Day 3 Complete! Data-driven testing mastered!**
