# DAY 4 PRACTICE EXERCISES

## Exercise 1: Basic testng.xml
Create testng.xml to run LoginTests class:
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Login Suite">
    <test name="Login Tests">
        <classes>
            <class name="tests.LoginTests"/>
        </classes>
    </test>
</suite>
```

## Exercise 2: Multiple Tests
Create suite with smoke and regression tests:
```xml
<suite name="Suite">
    <test name="Smoke">
        <groups>
            <run><include name="smoke"/></run>
        </groups>
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
        </classes>
    </test>

    <test name="Regression">
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
            <class name="tests.CheckoutTests"/>
        </classes>
    </test>
</suite>
```

## Exercise 3: Include/Exclude Methods
Run only specific methods:
```xml
<test name="Critical Tests">
    <classes>
        <class name="tests.LoginTests">
            <methods>
                <include name="testValidLogin"/>
                <exclude name="flakyTest"/>
            </methods>
        </class>
    </classes>
</test>
```

## Exercise 4: Parallel Execution
Run tests in parallel:
```xml
<suite name="Parallel Suite" parallel="methods" thread-count="3">
    <test name="Tests">
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
        </classes>
    </test>
</suite>
```

## Exercise 5: Parameters
Pass browser parameter:
```xml
<suite name="Suite">
    <parameter name="browser" value="chrome"/>
    <test name="Test">
        <classes><class name="tests.LoginTests"/></classes>
    </test>
</suite>
```

```java
@Test
@Parameters("browser")
public void testLogin(String browser) {
    System.out.println("Running on: " + browser);
}
```

## Exercise 6: Complete Configuration
Build comprehensive testng.xml:
```xml
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="E-commerce Suite" parallel="methods" thread-count="5" verbose="1">

    <parameter name="baseUrl" value="https://www.saucedemo.com"/>

    <test name="Smoke Tests">
        <groups>
            <run><include name="smoke"/></run>
        </groups>
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
        </classes>
    </test>

    <test name="Regression Tests">
        <groups>
            <run><include name="regression"/></run>
        </groups>
        <classes>
            <class name="tests.LoginTests"/>
            <class name="tests.ProductTests"/>
            <class name="tests.CheckoutTests"/>
        </classes>
    </test>

</suite>
```

**Run Command:**
```bash
mvn test -DsuiteXmlFile=testng.xml
```

---

**🎉 Practice Complete! Move to Day-4-Debug-Practice.md**
