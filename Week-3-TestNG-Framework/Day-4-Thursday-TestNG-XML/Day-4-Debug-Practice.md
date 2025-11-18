# DAY 4 DEBUGGING CHALLENGES

## Challenge 1: Missing DOCTYPE
```xml
<suite name="Suite">  <!-- Missing DOCTYPE! -->
    <test name="Test">...</test>
</suite>
```
**Error:** XML parsing error
**Fix:** Add DOCTYPE:
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Suite">
```

## Challenge 2: Wrong Class Name
```xml
<class name="LoginTests"/>  <!-- Missing package! -->
```
**Error:** Class not found
**Fix:** Include full package:
```xml
<class name="tests.LoginTests"/>
```

## Challenge 3: Classes Outside Test
```xml
<suite name="Suite">
    <classes>  <!-- Wrong level! -->
        <class name="Test"/>
    </classes>
</suite>
```
**Fix:**
```xml
<suite name="Suite">
    <test name="Test Name">
        <classes>
            <class name="Test"/>
        </classes>
    </test>
</suite>
```

## Challenge 4: Parallel Without Thread Safety
```xml
<suite parallel="methods" thread-count="5">
```
```java
WebDriver driver;  // Class variable

@BeforeClass  // Runs ONCE - shared!
public void setup() {
    driver = new ChromeDriver();
}
```
**Problem:** 5 tests share 1 browser → errors
**Fix:** Use @BeforeMethod:
```java
@BeforeMethod  // Each test gets own browser
public void setup() {
    driver = new ChromeDriver();
}
```

## Challenge 5: Parameter Not Found
```xml
<suite name="Suite">
    <test name="Test">
        <classes><class name="Test"/></classes>
    </test>
</suite>
```
```java
@Parameters("browser")  // "browser" not defined in XML!
public void test(String browser) { }
```
**Fix:** Add parameter:
```xml
<parameter name="browser" value="chrome"/>
```

## Challenge 6: Wrong Parallel Mode
```xml
<!-- Want tests in parallel, but using wrong mode -->
<suite parallel="methods">  <!-- Runs methods in parallel -->
    <test name="Test1">...</test>
    <test name="Test2">...</test>
</suite>
```
**Fix:** Use `parallel="tests"`:
```xml
<suite parallel="tests" thread-count="2">
```

---

**🔧 Debugging Complete! Move to Day-4-Project-Guide.md**
