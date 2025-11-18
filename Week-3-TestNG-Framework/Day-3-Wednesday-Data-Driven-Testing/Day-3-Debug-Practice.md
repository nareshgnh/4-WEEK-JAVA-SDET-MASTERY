# DAY 3 DEBUGGING CHALLENGES

## Challenge 1: DataProvider Not Found
```java
@DataProvider(name = "loginData")
public Object[][] getData() {
    return new Object[][] {{"user", "pass"}};
}

@Test(dataProvider = "loginDta")  // Typo!
public void test(String u, String p) { }
```
**Error:** `Data Provider loginDta not found`
**Fix:** Match names exactly: `dataProvider = "loginData"`

## Challenge 2: Wrong Parameter Count
```java
@DataProvider(name = "data")
public Object[][] getData() {
    return new Object[][] {{"user", "pass", "email"}};  // 3 values
}

@Test(dataProvider = "data")
public void test(String u, String p) { }  // Only 2 parameters!
```
**Error:** Parameter count mismatch
**Fix:** Add third parameter: `public void test(String u, String p, String email)`

## Challenge 3: Excel File Not Found
```java
@DataProvider
public Object[][] getData() throws IOException {
    return ExcelUtil.getExcelData("testdata.xlsx", "Sheet1");  // Wrong path!
}
```
**Error:** `FileNotFoundException`
**Fix:** Use correct path: `"src/test/resources/testdata.xlsx"`

## Challenge 4: NullPointerException from Empty Cell
```java
private static String getCellValue(Cell cell) {
    return cell.getStringCellValue();  // NPE if cell is null!
}
```
**Fix:**
```java
private static String getCellValue(Cell cell) {
    if (cell == null) return "";
    switch (cell.getCellType()) {
        case STRING: return cell.getStringCellValue();
        default: return "";
    }
}
```

## Challenge 5: CSV Split Issues
```java
String[] values = line.split(",");  // Fails if data contains commas!
```
**Data:** `"John Doe,25",password123`
**Problem:** Splits into 3 parts instead of 2

**Fix:** Use proper CSV parser or escape commas

## Challenge 6: DataProvider Method Not Static
```java
public class Test {
    @DataProvider(name = "data")
    public Object[][] getData() {  // Works
        return new Object[][] {{"a"}};
    }

    @Test(dataProvider = "data")
    public void test1(String a) { }  // Works - same class
}

public class AnotherTest {
    @Test(dataProvider = "data", dataProviderClass = Test.class)
    public void test2(String a) { }  // ERROR! getData() must be static
}
```

**Fix:** Make DataProvider static when used across classes:
```java
@DataProvider(name = "data")
public static Object[][] getData() {  // Now static
    return new Object[][] {{"a"}};
}
```

---

**🔧 Day 3 Debugging Complete! Move to Day-3-Project-Guide.md**
