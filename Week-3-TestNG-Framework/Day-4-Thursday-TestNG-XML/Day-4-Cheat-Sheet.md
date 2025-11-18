# DAY 4 CHEAT SHEET: TESTNG XML

## ⚡ Quick Syntax

### Basic Suite
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Suite Name">
    <test name="Test Name">
        <classes>
            <class name="full.package.ClassName"/>
        </classes>
    </test>
</suite>
```

### Parallel Execution
```xml
<suite parallel="methods" thread-count="5">
    <!-- methods, classes, tests, or both -->
</suite>
```

### Groups
```xml
<test name="Smoke">
    <groups>
        <run>
            <include name="smoke"/>
            <exclude name="slow"/>
        </run>
    </groups>
    <classes>...</classes>
</test>
```

### Parameters
```xml
<suite>
    <parameter name="browser" value="chrome"/>
    <test>...</test>
</suite>
```

```java
@Parameters("browser")
public void test(String browser) { }
```

### Include/Exclude Methods
```xml
<class name="TestClass">
    <methods>
        <include name="test1"/>
        <exclude name="test2"/>
    </methods>
</class>
```

## 🔑 Key Concepts

| Element | Purpose | Example |
|---------|---------|---------|
| **`<suite>`** | Top container | `<suite name="Main">` |
| **`<test>`** | Test group | `<test name="Smoke">` |
| **`<classes>`** | Classes to run | `<class name="pkg.Test"/>` |
| **`parallel`** | Parallel mode | `parallel="methods"` |
| **`thread-count`** | Thread pool size | `thread-count="5"` |
| **`<groups>`** | Filter by group | `<include name="smoke"/>` |
| **`<parameter>`** | Pass values | `<parameter name="x" value="y"/>` |

## 💡 Key Takeaways
- ✅ testng.xml controls test execution
- ✅ Hierarchy: Suite → Test → Classes → Methods
- ✅ Parallel execution speeds up tests dramatically
- ✅ Parameters make tests configurable
- ✅ Groups enable selective execution

## 📌 Remember This
> **Parallel = Fast Tests**
> ```xml
> <suite parallel="methods" thread-count="5">
>   5 tests run simultaneously
>   100 tests in 20 min instead of 100 min
> ```

## 🔮 Tomorrow
**Day 5:** Reporting - ExtentReports, Allure, Screenshots

---

**🎉 Day 4 Complete!**
