# DAY 4: TESTNG XML CONFIGURATION

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master testng.xml suite configuration
- [ ] Understand Suite/Test/Class/Method hierarchy
- [ ] Configure parallel test execution
- [ ] Include/exclude tests dynamically
- [ ] Run tests from command line with different configurations

**Time Required:** 3 hours
**Difficulty:** Medium
**Prerequisite:** Days 1-3 TestNG knowledge

---

## 📚 Core Concepts

### Concept 1: What is testng.xml?

**What is it?**
testng.xml is an XML configuration file that controls which tests run, how they run (parallel/sequential), and what parameters to pass. Think of it as the "control center" for your test execution.

**Why does it matter for automation?**
- **CI/CD Integration**: Jenkins/GitHub Actions run specific test suites
- **Flexible Execution**: Run smoke tests in 2 min, regression in 1 hour
- **Parallel Execution**: Run 100 tests in 10 minutes instead of 100 minutes
- **Environment Config**: Different settings for dev/staging/production

**Basic Structure:**
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Test Suite Name">
    <test name="Test Name">
        <classes>
            <class name="com.example.TestClass"/>
        </classes>
    </test>
</suite>
```

**Hierarchy:**
```
Suite (test-automation-suite)
  ├── Test (smoke-tests)
  │     ├── Class (LoginTests)
  │     │     ├── Method (testValidLogin)
  │     │     └── Method (testInvalidLogin)
  │     └── Class (ProductTests)
  └── Test (regression-tests)
        └── Class (CheckoutTests)
```

### Concept 2: Suite, Test, Class, Method Configuration

**Complete Example:**
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="E-commerce Test Suite" verbose="1">

    <!-- Suite-level parameter -->
    <parameter name="browser" value="chrome"/>

    <!-- Test 1: Smoke Tests -->
    <test name="Smoke Tests">
        <parameter name="environment" value="staging"/>

        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>

        <classes>
            <class name="tests.LoginTests">
                <methods>
                    <include name="testValidLogin"/>
                    <include name="testLoginPageLoad"/>
                </methods>
            </class>
            <class name="tests.ProductTests"/>
        </classes>
    </test>

    <!-- Test 2: Regression Tests -->
    <test name="Regression Tests">
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
            <class name="tests.CheckoutTests"/>
        </classes>
    </test>

</suite>
```

**Key Elements:**

1. **`<suite>`** - Top level container
   - `name`: Suite name in reports
   - `verbose`: Log level (1-10)
   - `parallel`: Parallel execution mode

2. **`<test>`** - Logical test group
   - `name`: Test name in reports
   - Can have different configurations

3. **`<classes>`** - Test classes to include
   - Full qualified class name
   - Can include/exclude specific methods

4. **`<parameter>`** - Pass values to tests
   - Available in @BeforeMethod, @Test, etc.

### Concept 3: Include/Exclude Tests

**Include Specific Methods:**
```xml
<test name="Critical Tests Only">
    <classes>
        <class name="tests.LoginTests">
            <methods>
                <include name="testValidLogin"/>
                <include name="testLoginPageLoad"/>
                <!-- Only these 2 methods run -->
            </methods>
        </class>
    </classes>
</test>
```

**Exclude Specific Methods:**
```xml
<test name="All Tests Except Flaky">
    <classes>
        <class name="tests.LoginTests">
            <methods>
                <exclude name="flakyTest"/>
                <!-- All methods except flakyTest -->
            </methods>
        </class>
    </classes>
</test>
```

**Include/Exclude by Groups:**
```xml
<test name="Smoke Tests">
    <groups>
        <run>
            <include name="smoke"/>
            <exclude name="slow"/>
        </run>
    </groups>
    <classes>
        <class name="tests.LoginTests"/>
        <class name="tests.ProductTests"/>
    </classes>
</test>
```

**Include Packages:**
```xml
<test name="All Tests in Package">
    <packages>
        <package name="tests.login.*"/>
        <package name="tests.product.*"/>
    </packages>
</test>
```

### Concept 4: Parallel Execution

**Why Parallel Execution?**
- **Sequential**: 100 tests × 1 minute = 100 minutes
- **Parallel (10 threads)**: 100 tests ÷ 10 = 10 minutes

**Parallel Modes:**

1. **Parallel Methods** - Each @Test method runs in parallel
```xml
<suite name="Suite" parallel="methods" thread-count="5">
    <test name="Test">
        <classes>
            <class name="TestClass"/>
        </classes>
    </test>
</suite>
```

2. **Parallel Classes** - Each test class runs in parallel
```xml
<suite name="Suite" parallel="classes" thread-count="3">
    <test name="Test">
        <classes>
            <class name="LoginTests"/>
            <class name="ProductTests"/>
            <class name="CheckoutTests"/>
        </classes>
    </test>
</suite>
```

3. **Parallel Tests** - Each `<test>` runs in parallel
```xml
<suite name="Suite" parallel="tests" thread-count="2">
    <test name="Test 1">...</test>
    <test name="Test 2">...</test>
</suite>
```

4. **Combined Parallel** - Tests in parallel, methods within test in parallel
```xml
<suite name="Suite" parallel="both" thread-count="10">
    <!-- Both tests and methods run in parallel -->
</suite>
```

**Best Practice for Selenium:**
```xml
<suite name="Selenium Suite" parallel="methods" thread-count="5">
    <!-- 5 browsers open simultaneously -->
    <!-- Each test gets its own browser (from @BeforeMethod) -->
</suite>
```

**Thread Pool:**
```xml
<suite name="Suite" parallel="methods" thread-count="10" data-provider-thread-count="5">
    <!-- 10 threads for tests, 5 for data providers -->
</suite>
```

### Concept 5: Parameters

**Define Parameters:**
```xml
<suite name="Suite">
    <parameter name="browser" value="chrome"/>
    <parameter name="baseUrl" value="https://staging.example.com"/>

    <test name="Test">
        <parameter name="username" value="testuser"/>
        <classes>...</classes>
    </test>
</suite>
```

**Receive Parameters in Test:**
```java
@Test
@Parameters({"browser", "baseUrl"})
public void testLogin(String browser, String baseUrl) {
    System.out.println("Browser: " + browser);  // chrome
    System.out.println("URL: " + baseUrl);      // https://staging.example.com
}

@BeforeMethod
@Parameters("username")
public void setup(String username) {
    System.out.println("Username: " + username);  // testuser
}
```

**Optional Parameters:**
```java
@Parameters({"browser"})
public void test(@Optional("firefox") String browser) {
    // If "browser" not in XML, defaults to "firefox"
}
```

---

## 🐍 Python vs Java Comparison

### Python (Pytest)
```bash
# Run specific tests
pytest tests/test_login.py::test_valid_login

# Run by marker
pytest -m smoke

# Parallel execution
pytest -n 5  # 5 workers
```

### Java (TestNG)
```xml
<!-- testng.xml -->
<suite name="Suite">
    <test name="Specific Test">
        <classes>
            <class name="tests.LoginTests">
                <methods>
                    <include name="testValidLogin"/>
                </methods>
            </class>
        </classes>
    </test>

    <test name="Smoke">
        <groups>
            <run><include name="smoke"/></run>
        </groups>
    </test>
</suite>

<!-- Parallel -->
<suite parallel="methods" thread-count="5">
```

```bash
mvn test -DsuiteXmlFile=testng.xml
```

---

## ⚠️ Common Mistakes

### Mistake 1: Wrong Class Name
```xml
<class name="LoginTests"/>  <!-- Missing package! -->
```
**Fix:** `<class name="tests.LoginTests"/>`

### Mistake 2: Parallel Without Thread-Safe Tests
```xml
<suite parallel="methods" thread-count="5">
    <!-- Tests share WebDriver instance → ERRORS -->
</suite>
```
**Fix:** Each test must have its own WebDriver (use @BeforeMethod)

### Mistake 3: Incorrect Hierarchy
```xml
<suite>
    <classes>  <!-- Wrong! classes must be inside test -->
        <class name="Test"/>
    </classes>
</suite>
```
**Fix:**
```xml
<suite>
    <test name="Test Name">
        <classes><class name="Test"/></classes>
    </test>
</suite>
```

---

## 💡 Pro Tips

**Tip 1: Multiple XML Files**
```
testng-smoke.xml     → Smoke tests
testng-regression.xml → All tests
testng-nightly.xml   → Long-running tests
```

**Tip 2: Organize by Feature**
```xml
<suite name="Suite">
    <test name="Login Feature">...</test>
    <test name="Checkout Feature">...</test>
</suite>
```

**Tip 3: Start with parallel="methods" thread-count="5"**
```xml
<!-- Good starting point for most projects -->
<suite parallel="methods" thread-count="5">
```

---

## 🎓 Interview Prep

**Q: How do you configure parallel execution in TestNG?**

**A:** I use the `parallel` attribute in testng.xml. For Selenium tests, I typically use `parallel="methods"` with `thread-count` set based on available resources. Each test method gets its own WebDriver instance created in @BeforeMethod, ensuring thread safety. For example:

```xml
<suite name="Suite" parallel="methods" thread-count="5">
    <test name="Tests">
        <classes>
            <class name="LoginTests"/>
        </classes>
    </test>
</suite>
```

This runs 5 tests simultaneously, reducing execution time significantly.

---

**🎯 Ready to practice? Move to Day-4-Practice-Exercises.md**
