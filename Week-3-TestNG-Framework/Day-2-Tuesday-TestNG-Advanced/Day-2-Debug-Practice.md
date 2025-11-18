# DAY 2 DEBUGGING CHALLENGES

## 🐛 Advanced TestNG Debugging

Today's debugging challenges focus on common mistakes with groups, dependencies, priorities, and lifecycle annotations.

---

## Challenge 1: BeforeClass with Non-Static Variable Access

**Broken Code:**
```java
import org.testng.annotations.*;

public class ConfigTest {
    String baseUrl;  // Instance variable

    @BeforeClass
    public static void setup() {
        baseUrl = "https://example.com";  // Error!
    }

    @Test
    public void testSomething() {
        System.out.println(baseUrl);
    }
}
```

**Error Message:**
```
error: non-static variable baseUrl cannot be referenced from a static context
        baseUrl = "https://example.com";
        ^
```

**Your Task:** Why does this fail? How do you fix it?

---

**Solution:**

**Bug Location:** Trying to access instance variable from static method

**Explanation:**
`@BeforeClass` methods can optionally be `static`. If they are static, they can only access static variables, not instance variables. The code tries to set an instance variable `baseUrl` from a static method.

**Fix Option 1: Make variable static**
```java
public class ConfigTest {
    static String baseUrl;  // Changed to static

    @BeforeClass
    public static void setup() {
        baseUrl = "https://example.com";  // Works now!
    }

    @Test
    public void testSomething() {
        System.out.println(baseUrl);
    }
}
```

**Fix Option 2: Make method non-static**
```java
public class ConfigTest {
    String baseUrl;  // Instance variable

    @BeforeClass
    public void setup() {  // Removed 'static'
        baseUrl = "https://example.com";  // Works!
    }

    @Test
    public void testSomething() {
        System.out.println(baseUrl);
    }
}
```

**Key Learning:**
- `@BeforeClass` can be static or non-static
- Static methods can only access static variables
- For shared configuration across tests, use `static` variables
- For instance-specific data, use non-static variables and methods

---

## Challenge 2: Dependency on Non-Existent Method

**Broken Code:**
```java
import org.testng.annotations.Test;

public class DependencyTest {

    @Test
    public void createUser() {
        System.out.println("User created");
    }

    @Test(dependsOnMethods = {"loginUser"})  // Method doesn't exist!
    public void editProfile() {
        System.out.println("Profile edited");
    }
}
```

**Error Message:**
```
org.testng.TestNGException:
Method DependencyTest.editProfile() depends on nonexistent method loginUser
```

**Your Task:** What's wrong and how to prevent this?

---

**Solution:**

**Bug Location:** `dependsOnMethods` references a method that doesn't exist

**Explanation:**
The test `editProfile` depends on `loginUser`, but there's no method named `loginUser` in the class. This is a runtime error - code compiles but TestNG throws exception when running.

**Fixed Code:**
```java
import org.testng.annotations.Test;

public class DependencyTest {

    @Test
    public void createUser() {
        System.out.println("User created");
    }

    @Test(dependsOnMethods = {"createUser"})  // Method exists!
    public void loginUser() {
        System.out.println("User logged in");
    }

    @Test(dependsOnMethods = {"loginUser"})  // Now this exists
    public void editProfile() {
        System.out.println("Profile edited");
    }
}
```

**Prevention Tips:**
```java
// Bad: String names - typos cause runtime errors
@Test(dependsOnMethods = {"loginUser"})  // If you misspell, error at runtime

// Better: Use constants to avoid typos
private static final String LOGIN_TEST = "loginUser";

@Test
public void loginUser() { }

@Test(dependsOnMethods = {LOGIN_TEST})  // Compile-time safety
public void editProfile() { }
```

**Key Learning:**
- Method names in `dependsOnMethods` are strings - no compile-time checking
- Typos cause runtime failures
- Consider using constants for frequently referenced method names

---

## Challenge 3: Group Spelling Mismatch

**Broken Code:**
```java
// Test class
import org.testng.annotations.Test;

public class GroupTest {

    @Test(groups = {"smoke"})
    public void test1() {
        System.out.println("Smoke test 1");
    }

    @Test(groups = {"smoke"})
    public void test2() {
        System.out.println("Smoke test 2");
    }

    @Test(groups = {"regression"})
    public void test3() {
        System.out.println("Regression test");
    }
}
```

```xml
<!-- testng.xml -->
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Test Suite">
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="somke"/>  <!-- Typo! -->
            </run>
        </groups>
        <classes>
            <class name="GroupTest"/>
        </classes>
    </test>
</suite>
```

**Problem:** No tests run, no error message!

**Your Task:** Find the issue

---

**Solution:**

**Bug Location:** Typo in testng.xml group name: "somke" instead of "smoke"

**Explanation:**
TestNG silently ignores non-matching group names. The XML says run "somke" group, but no tests have that group, so zero tests execute. No error is thrown - this is a silent failure.

**Fixed Code:**
```xml
<groups>
    <run>
        <include name="smoke"/>  <!-- Fixed spelling -->
    </run>
</groups>
```

**Prevention Tips:**
```java
// Define group names as constants
public class TestGroups {
    public static final String SMOKE = "smoke";
    public static final String REGRESSION = "regression";
    public static final String API = "api";
}

// Use in tests
@Test(groups = {TestGroups.SMOKE})  // Compile-time safety
public void test1() { }

// In testng.xml, still use strings but at least you can copy-paste from constants
```

**Key Learning:**
- Group name mismatches fail silently
- Always verify test count after running grouped tests
- Consider using constants for group names
- Double-check testng.xml spelling

---

## Challenge 4: Priority Doesn't Override Dependencies

**Broken Code:**
```java
import org.testng.annotations.Test;

public class OrderTest {

    @Test(priority = 1)
    public void test1() {
        System.out.println("Test 1 - Priority 1");
    }

    @Test(priority = 2, dependsOnMethods = {"test3"})
    public void test2() {
        System.out.println("Test 2 - Priority 2, depends on test3");
    }

    @Test(priority = 3)
    public void test3() {
        System.out.println("Test 3 - Priority 3");
    }
}
```

**Expected Order:** test1, test2, test3 (by priority)
**Actual Order:** test1, test3, test2

**Your Task:** Why doesn't test2 run at priority 2?

---

**Solution:**

**Bug Location:** This is not a bug - it's expected behavior!

**Explanation:**
Dependencies OVERRIDE priority. Even though test2 has priority 2, it depends on test3. TestNG must run test3 first to satisfy the dependency, then run test2.

**Execution Order:**
```
1. test1 (priority 1, no dependencies)
2. test3 (priority 3, but needed by test2)
3. test2 (priority 2, but waits for test3)
```

**Rule: Dependencies > Priority**

**Correct Code (if you want specific order):**
```java
// Option 1: Remove dependency, use only priority
@Test(priority = 1)
public void test1() { }

@Test(priority = 2)
public void test2() { }  // Removed dependency

@Test(priority = 3)
public void test3() { }

// Option 2: Align priorities with dependencies
@Test(priority = 1)
public void test1() { }

@Test(priority = 3)  // Changed to 3
public void test3() { }

@Test(priority = 4, dependsOnMethods = {"test3"})  // After test3
public void test2() { }
```

**Key Learning:**
- Dependencies always take precedence over priority
- If test B depends on test A, test A runs first regardless of priority
- Don't mix dependencies and priority on the same test unless you understand the interaction

---

## Challenge 5: AfterClass Not Running

**Broken Code:**
```java
import org.testng.Assert;
import org.testng.annotations.*;

public class CleanupTest {
    static int testCount = 0;

    @BeforeMethod
    public void setup() {
        System.out.println("Setup");
    }

    @Test
    public void test1() {
        testCount++;
        System.out.println("Test 1");
    }

    @Test
    public void test2() {
        testCount++;
        Assert.fail("Test 2 intentionally fails");
    }

    @Test
    public void test3() {
        testCount++;
        System.out.println("Test 3");
    }

    @AfterClass
    public void cleanup() {
        // This DOES run even if tests fail
        System.out.println("Cleanup - Tests run: " + testCount);
    }
}
```

**Question:** Does @AfterClass run if a test fails?

---

**Solution:**

**Not a Bug:** @AfterClass ALWAYS runs, even if tests fail

**Explanation:**
This is actually correct behavior. The confusion comes from thinking @AfterClass won't run if a test fails. Actually:

- **@AfterMethod** runs even if test fails
- **@AfterClass** runs even if tests fail
- **@AfterSuite** runs even if entire suite fails

Only exception: If you forcefully kill the JVM or there's an unhandled error in @BeforeClass.

**Output:**
```
Setup
Test 1
Setup
Test 2 intentionally fails  ← Test fails here
Setup
Test 3
Cleanup - Tests run: 3  ← @AfterClass still runs!
```

**When @AfterClass Doesn't Run:**
```java
@BeforeClass
public void beforeClass() {
    throw new RuntimeException("Unhandled exception!");
    // If @BeforeClass fails, @AfterClass won't run
}
```

**Best Practice for Cleanup:**
```java
@AfterClass(alwaysRun = true)  // Run even if class is skipped
public void cleanup() {
    // Critical cleanup code
    if (connection != null) connection.close();
}
```

**Key Learning:**
- @AfterClass runs even when tests fail
- Use `alwaysRun = true` for critical cleanup
- Only unhandled exceptions in @BeforeClass prevent @AfterClass

---

## Challenge 6: dependsOnGroups Not Working

**Broken Code:**
```java
import org.testng.annotations.Test;

public class GroupDependencyTest {

    @Test(groups = {"setup"})
    public void setupDatabase() {
        System.out.println("Database setup");
    }

    @Test(groups = {"setup"})
    public void setupTestData() {
        System.out.println("Test data loaded");
    }

    @Test(dependsOnGroups = {"setup"})
    public void test1() {
        // This should run after ALL "setup" group tests
        System.out.println("Test 1");
    }

    @Test(groups = {"setup"})  // Added AFTER test1
    public void lateSetup() {
        System.out.println("Late setup");
    }
}
```

**Actual Execution:**
```
Database setup
Test data loaded
Test 1  ← Runs before lateSetup!
Late setup
```

**Your Task:** Why does test1 run before lateSetup?

---

**Solution:**

**Bug Location:** Misunderstanding of `dependsOnGroups` behavior

**Explanation:**
`dependsOnGroups` depends on the tests in that group that are **defined before** the dependent test, not ALL tests in that group.

In the code:
- setupDatabase (group: setup)
- setupTestData (group: setup)
- test1 (dependsOnGroups: setup) ← Depends on setup tests ABOVE it
- lateSetup (group: setup) ← Defined AFTER test1, so test1 doesn't wait for it

**Fixed Code (define all setup tests first):**
```java
import org.testng.annotations.Test;

public class GroupDependencyTest {

    @Test(groups = {"setup"})
    public void setupDatabase() {
        System.out.println("Database setup");
    }

    @Test(groups = {"setup"})
    public void setupTestData() {
        System.out.println("Test data loaded");
    }

    @Test(groups = {"setup"})
    public void lateSetup() {  // Moved before test1
        System.out.println("Late setup");
    }

    @Test(dependsOnGroups = {"setup"})
    public void test1() {
        // Now runs after ALL setup tests
        System.out.println("Test 1");
    }
}
```

**Or use priority:**
```java
@Test(groups = {"setup"}, priority = 1)
public void setupDatabase() { }

@Test(groups = {"setup"}, priority = 2)
public void setupTestData() { }

@Test(groups = {"setup"}, priority = 3)
public void lateSetup() { }

@Test(dependsOnGroups = {"setup"}, priority = 10)
public void test1() { }
```

**Key Learning:**
- `dependsOnGroups` only waits for group tests defined before it
- Order of test methods in class file matters
- Combine with priority for predictable execution

---

## 🎯 Debugging Best Practices

**1. Always Check Test Counts**
```java
// After running grouped tests, verify count
Expected: 5 smoke tests
Actual: 0 tests ran
→ Check for group name typo in testng.xml
```

**2. Print Execution Order**
```java
@BeforeMethod
public void before() {
    System.out.println(">>> Starting: " +
        new Exception().getStackTrace()[1].getMethodName());
}
```

**3. Use alwaysRun for Critical Cleanup**
```java
@AfterClass(alwaysRun = true)
@AfterMethod(alwaysRun = true)
public void cleanup() {
    // Runs even if test/class is skipped
}
```

**4. Validate Dependencies Exist**
```java
// Create constants for method names
private static final String CREATE_USER = "createUser";

@Test
public void createUser() { }

@Test(dependsOnMethods = {CREATE_USER})  // Safer than string
public void editUser() { }
```

---

## 💡 Common Pitfalls Summary

| Issue | Symptom | Solution |
|-------|---------|----------|
| **Static vs Instance** | Can't access variable from @BeforeClass | Make variable static or method non-static |
| **Wrong dependency name** | Runtime exception about nonexistent method | Double-check spelling, use constants |
| **Group typo** | No tests run, no error | Verify group names match in code and XML |
| **Priority vs Dependency** | Tests run in unexpected order | Remember: Dependencies override priority |
| **dependsOnGroups** | Doesn't wait for all group tests | Define all group tests before dependent test |
| **Cleanup not running** | @AfterClass doesn't execute | Usually it does; check for @BeforeClass errors |

---

**🔧 Day 2 Debugging Complete! Move to Day-2-Project-Guide.md**
