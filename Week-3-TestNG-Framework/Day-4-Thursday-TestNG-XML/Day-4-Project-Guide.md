# DAY 4 PROJECT: PARALLEL TEST EXECUTION

## 🎯 Project Overview
Configure parallel test execution with multiple testng.xml files for different scenarios.

## 📋 Requirements
1. Three testng.xml files (smoke, regression, parallel)
2. Proper parallel configuration
3. Parameter passing
4. Group-based execution
5. Run from command line

## 🏗️ Project Structure
```
testng-smoke.xml
testng-regression.xml
testng-parallel.xml
```

## 📝 Implementation

### File 1: testng-smoke.xml
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Smoke Test Suite">
    <parameter name="browser" value="chrome"/>

    <test name="Critical Tests">
        <groups>
            <run><include name="smoke"/></run>
        </groups>
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
        </classes>
    </test>
</suite>
```

### File 2: testng-regression.xml
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Regression Suite">
    <test name="All Tests">
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
            <class name="tests.CheckoutTests"/>
        </classes>
    </test>
</suite>
```

### File 3: testng-parallel.xml
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Parallel Suite" parallel="methods" thread-count="5">
    <parameter name="baseUrl" value="https://www.saucedemo.com"/>

    <test name="Parallel Tests">
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
        </classes>
    </test>
</suite>
```

## ✅ Testing
```bash
# Run smoke
mvn test -DsuiteXmlFile=testng-smoke.xml

# Run regression
mvn test -DsuiteXmlFile=testng-regression.xml

# Run parallel
mvn test -DsuiteXmlFile=testng-parallel.xml
```

## 💼 Real-World Usage
```
PR Builds      → testng-smoke.xml (2 min)
Nightly Build  → testng-regression.xml (30 min)
Performance    → testng-parallel.xml (5 min with 10 threads)
```

---

**🎉 Project Complete! Move to Day-4-Cheat-Sheet.md**
