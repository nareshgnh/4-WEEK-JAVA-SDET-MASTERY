# DAY 4 DEBUGGING CHALLENGES

## 🐛 Why Debugging Maven Issues?

As an SDET, you'll spend significant time troubleshooting Maven build failures:
- **Local Development:** Dependency conflicts, compilation errors, test failures
- **CI/CD Pipelines:** Jenkins/GitHub Actions build failures
- **Team Collaboration:** Resolving pom.xml merge conflicts
- **Interview Scenarios:** Demonstrating problem-solving skills

**Real-world stat:** 30% of an SDET's time is spent debugging build and configuration issues, not just writing tests.

---

## Challenge 1: Dependency Conflict - Version Mismatch

**Scenario:**
Your tests were working fine. After adding a new library, you get this runtime error:

**Broken pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.automation</groupId>
    <artifactId>api-tests</artifactId>
    <version>1.0</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <!-- RestAssured 5.3.0 uses Jackson 2.14.x -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>5.3.0</version>
            <scope>test</scope>
        </dependency>

        <!-- You added this for JSON processing -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.10.0</version> <!-- OLD VERSION -->
        </dependency>

        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>7.7.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

**Test Code:**
```java
import io.restassured.response.Response;
import static io.restassured.RestAssured.*;

public class UserTest {
    @Test
    public void testGetUser() {
        Response response = given()
            .when()
                .get("https://reqres.in/api/users/2")
            .then()
                .extract().response();

        // This line throws NoSuchMethodError
        String email = response.jsonPath().getString("data.email");
        System.out.println(email);
    }
}
```

**Error Message:**
```
java.lang.NoSuchMethodError: com.fasterxml.jackson.databind.ObjectMapper.readValue(Ljava/lang/String;)Ljava/lang/Object;
    at io.restassured.internal.mapping.Jackson2Mapper.deserialize(Jackson2Mapper.java:45)
    at io.restassured.path.json.JsonPath.getJsonObject(JsonPath.java:234)
```

**Your Task:**
1. Identify why this error occurs
2. Explain the dependency conflict
3. Fix the pom.xml
4. Verify the fix works

**Hints:**
- Run `mvn dependency:tree` to see all Jackson versions
- Check which version RestAssured expects
- Look at the error message carefully - it's a method signature issue

---

**Solution:**

**Bug Location:** Dependency version conflict between RestAssured's Jackson version (2.14.x) and manually added Jackson (2.10.0)

**Explanation:**
1. RestAssured 5.3.0 internally uses Jackson 2.14.x for JSON processing
2. You explicitly added Jackson 2.10.0 (older version)
3. Maven's dependency resolution picked the older version (2.10.0)
4. RestAssured code calls methods that exist in 2.14.x but not in 2.10.0
5. Result: `NoSuchMethodError` at runtime

**Debugging Steps:**
```bash
# Step 1: Check dependency tree
mvn dependency:tree -Dincludes=com.fasterxml.jackson.core

# Output shows conflict:
# [INFO] +- io.rest-assured:rest-assured:5.3.0
# [INFO] |  \- com.fasterxml.jackson.core:jackson-databind:2.14.1 (conflict with 2.10.0)
# [INFO] \- com.fasterxml.jackson.core:jackson-databind:2.10.0 (wins due to proximity)
```

**Fixed pom.xml (Solution 1 - Upgrade version):**
```xml
<dependencies>
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.0</version>
        <scope>test</scope>
    </dependency>

    <!-- Use compatible Jackson version -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.15.0</version> <!-- Compatible with RestAssured -->
    </dependency>

    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.7.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Fixed pom.xml (Solution 2 - Remove explicit dependency, let RestAssured manage it):**
```xml
<dependencies>
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.0</version>
        <scope>test</scope>
        <!-- Includes Jackson 2.14.x transitively - just use it! -->
    </dependency>

    <!-- Remove explicit Jackson dependency -->
    <!-- RestAssured already includes it -->

    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.7.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Verify:**
```bash
mvn clean test
# Tests should pass now
```

**Key Learning:**
- Always check transitive dependencies before adding explicit ones
- Use `mvn dependency:tree` to troubleshoot version conflicts
- When in doubt, let libraries manage their own dependencies
- NoSuchMethodError = version mismatch at runtime

---

## Challenge 2: Wrong Dependency Scope - Tests Don't Compile

**Broken pom.xml:**
```xml
<dependencies>
    <!-- WRONG SCOPE - RestAssured in runtime instead of test -->
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.0</version>
        <scope>runtime</scope> <!-- WRONG! -->
    </dependency>

    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.7.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Test Code (src/test/java/UserTest.java):**
```java
import io.restassured.RestAssured;
import org.testng.annotations.Test;

import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class UserTest {

    @Test
    public void testGetUser() {
        given()
            .when()
                .get("https://reqres.in/api/users/2")
            .then()
                .statusCode(200);
    }
}
```

**Error Message:**
```
[ERROR] /path/to/UserTest.java:[1,24] package io.restassured does not exist
[ERROR] /path/to/UserTest.java:[5,32] package io.restassured does not exist
[ERROR] /path/to/UserTest.java:[11,9] cannot find symbol
  symbol:   method given()
  location: class UserTest
[ERROR] COMPILATION ERROR
```

**Your Task:**
1. Identify the scope issue
2. Explain why compilation fails
3. Fix the scope
4. Understand when to use each scope

**Hints:**
- Check the dependency scope
- Remember: runtime scope is NOT available at compile time
- Review scope availability table

---

**Solution:**

**Bug Location:** Line with `<scope>runtime</scope>` for RestAssured dependency

**Explanation:**
1. **runtime** scope means dependency is available only during test execution, NOT during compilation
2. Test code tries to import `io.restassured.RestAssured` at compile time
3. Compiler can't find RestAssured classes (not on classpath during compilation)
4. Result: Compilation error "package does not exist"

**Scope Availability Reminder:**
| Scope | Compile Time | Test Compile | Runtime | Test Runtime |
|-------|--------------|--------------|---------|--------------|
| compile | ✅ | ✅ | ✅ | ✅ |
| test | ❌ | ✅ | ❌ | ✅ |
| runtime | ❌ | ❌ | ✅ | ✅ |
| provided | ✅ | ✅ | ❌ | ❌ |

**Fixed Code:**
```xml
<dependencies>
    <!-- CORRECT SCOPE for test framework -->
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.0</version>
        <scope>test</scope> <!-- FIXED! -->
    </dependency>

    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.7.1</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Verify:**
```bash
mvn clean compile
# Main code compiles

mvn clean test-compile
# Test code compiles successfully now

mvn clean test
# Tests run successfully
```

**Key Learning:**
- **test** scope for all test frameworks (TestNG, JUnit, RestAssured, Mockito)
- **runtime** scope for drivers (JDBC, Loggers) - not needed at compile time
- **compile** scope for POJOs, utilities used in main code
- **provided** scope for server-provided libraries (Servlet API, Lombok)

---

## Challenge 3: Missing Plugin Configuration - Tests Not Running

**Broken pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>
    <groupId>com.automation</groupId>
    <artifactId>api-tests</artifactId>
    <version>1.0</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>5.3.0</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>7.7.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <!-- NO BUILD SECTION! -->

</project>
```

**Test Code (UserTest.java):**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class UserTest {

    @Test
    public void testGetUser() {
        given()
            .when()
                .get("https://reqres.in/api/users/2")
            .then()
                .statusCode(200);
        System.out.println("Test executed!");
    }
}
```

**Run:**
```bash
mvn clean test
```

**Output:**
```
[INFO] --- maven-surefire-plugin:2.12.4:test (default-test) @ api-tests ---
[INFO] No tests to run.
[INFO] BUILD SUCCESS
```

**Problem:** Tests are not running! Build succeeds but shows "No tests to run."

**Your Task:**
1. Why are tests not executing?
2. What's missing in pom.xml?
3. Fix the configuration
4. Understand Surefire test detection rules

**Hints:**
- Surefire plugin needs configuration for TestNG
- Check Surefire version
- Default Surefire doesn't recognize TestNG @Test

---

**Solution:**

**Bug Location:** Missing Surefire plugin configuration in `<build>` section

**Explanation:**
1. Maven uses Surefire plugin (default version 2.12.4) to run tests
2. Old Surefire versions (< 2.19) don't auto-detect TestNG tests
3. Surefire needs explicit configuration to run TestNG suite or specific classes
4. Without configuration, Surefire skips all tests → "No tests to run"

**Why tests weren't detected:**
- Default Surefire looks for JUnit tests (Test*.java, *Test.java, *TestCase.java)
- TestNG uses annotations (@Test) which old Surefire doesn't recognize
- Need to either upgrade Surefire or add TestNG suite configuration

**Fixed pom.xml (Solution 1 - Add Surefire with TestNG suite):**
```xml
<build>
    <plugins>
        <!-- Maven Surefire Plugin for running TestNG tests -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0</version> <!-- Use modern version -->
            <configuration>
                <suiteXmlFiles>
                    <suiteXmlFile>src/test/resources/testng.xml</suiteXmlFile>
                </suiteXmlFiles>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Create src/test/resources/testng.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="API Test Suite">
    <test name="User Tests">
        <classes>
            <class name="UserTest"/>
        </classes>
    </test>
</suite>
```

**Fixed pom.xml (Solution 2 - Configure Surefire to scan for TestNG classes):**
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0</version>
            <configuration>
                <!-- Auto-detect TestNG tests -->
                <includes>
                    <include>**/*Test.java</include>
                    <include>**/*Tests.java</include>
                </includes>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Verify:**
```bash
mvn clean test

# Expected Output:
[INFO] --- maven-surefire-plugin:3.0.0:test ---
[INFO] Running UserTest
Test executed!
[INFO] Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
[INFO] BUILD SUCCESS
```

**Key Learning:**
- Always configure Surefire plugin explicitly
- Modern Surefire (3.0+) better at auto-detection
- TestNG requires suite file OR proper include patterns
- "No tests to run" = configuration issue, not code issue

---

## Challenge 4: Build Failure - Compiler Version Mismatch

**Broken pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.automation</groupId>
    <artifactId>api-tests</artifactId>
    <version>1.0</version>

    <!-- NO PROPERTIES - No compiler version specified! -->

    <dependencies>
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>5.3.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

**Test Code using Java 11 features:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class UserTest {

    @Test
    public void testGetUser() {
        // Java 11 feature: var keyword
        var response = given()
            .when()
                .get("https://reqres.in/api/users/2")
            .then()
                .statusCode(200)
                .extract().response();

        // Java 11 feature: String method
        var email = response.path("data.email").toString();
        System.out.println(email.isBlank()); // Java 11 method
    }
}
```

**Error Message:**
```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.1:compile
[ERROR] Source option 5 is no longer supported. Use 7 or later.
[ERROR] Target option 5 is no longer supported. Use 7 or later.
```

OR

```
[ERROR] cannot find symbol: class var
  location: class UserTest
```

**Your Task:**
1. Identify the compiler version issue
2. Explain why Maven defaults to Java 5
3. Fix the pom.xml to use Java 11
4. Understand compiler plugin configuration

**Hints:**
- Maven's default compiler target is Java 5 (ancient!)
- Modern Java features require Java 8+
- Need to specify source and target version

---

**Solution:**

**Bug Location:** Missing compiler version configuration in properties

**Explanation:**
1. Maven Compiler Plugin defaults to Java 5 (for backwards compatibility)
2. Java 5 doesn't support `var` keyword (Java 10+) or `isBlank()` (Java 11+)
3. Compiler tries to compile modern Java code with Java 5 rules
4. Result: Compilation errors or "Source option 5 no longer supported"

**Fixed pom.xml (Solution 1 - Using properties):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.automation</groupId>
    <artifactId>api-tests</artifactId>
    <version>1.0</version>

    <!-- FIX: Add compiler properties -->
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>5.3.0</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

**Fixed pom.xml (Solution 2 - Using compiler plugin configuration):**
```xml
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
</properties>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.11.0</version>
            <configuration>
                <source>11</source>
                <target>11</target>
                <release>11</release> <!-- Ensures true Java 11 compatibility -->
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Verify:**
```bash
mvn clean compile

# Expected Output:
[INFO] --- maven-compiler-plugin:3.11.0:compile ---
[INFO] Compiling 1 source file to target/classes
[INFO] BUILD SUCCESS
```

**Check compiled bytecode version:**
```bash
javap -v target/test-classes/UserTest.class | grep "major version"
# Should show: major version: 55 (Java 11)
```

**Key Learning:**
- **ALWAYS** specify Java version in pom.xml
- Use properties for consistency across all plugins
- `source` = Java version of your source code
- `target` = Java version of compiled bytecode
- `release` = Ensures full compatibility (Java 9+)

**Common Java Versions:**
- Java 8: source/target = 8 or 1.8
- Java 11: source/target = 11
- Java 17: source/target = 17

---

## Challenge 5: Profile Not Activating - Wrong Environment

**Broken pom.xml:**
```xml
<properties>
    <base.url>http://default.com</base.url>
</properties>

<profiles>
    <!-- QA Profile -->
    <profile>
        <id>qa</id>
        <properties>
            <base.url>https://qa.company.com</base.url>
            <environment>QA</environment>
        </properties>
    </profile>

    <!-- Production Profile -->
    <profile>
        <id>prod</id>
        <properties>
            <base.url>https://www.company.com</base.url>
            <environment>PRODUCTION</environment>
        </properties>
    </profile>
</profiles>

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0</version>
            <configuration>
                <systemPropertyVariables>
                    <baseUrl>${base.url}</baseUrl>
                    <env>${environment}</env>
                </systemPropertyVariables>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Test Code:**
```java
public class ConfigTest {
    @Test
    public void testConfiguration() {
        String baseUrl = System.getProperty("baseUrl");
        String env = System.getProperty("env");

        System.out.println("Base URL: " + baseUrl);
        System.out.println("Environment: " + env);

        // Expecting QA environment
        Assert.assertEquals(env, "QA", "Should be QA environment");
    }
}
```

**Run:**
```bash
mvn clean test -Pqa
```

**Output:**
```
Base URL: https://qa.company.com
Environment: null

java.lang.AssertionError: Should be QA environment expected [QA] but found [null]
```

**Problem:** `environment` property is not being passed to tests even though profile is activated!

**Your Task:**
1. Find why `environment` is null
2. Identify the variable reference issue
3. Fix the configuration
4. Verify profile activation

**Hints:**
- Check property names carefully
- Look at how properties are referenced in Surefire config
- Property must exist before it can be used

---

**Solution:**

**Bug Location:** The property `${environment}` is only defined inside profiles, not at top level

**Explanation:**
1. Surefire configuration references `${environment}` variable
2. When no profile is active, `${environment}` is undefined
3. Maven doesn't throw error for undefined properties, just passes "null" or empty string
4. Result: System property `env` is null in tests

**Why baseUrl works but environment doesn't:**
- `base.url` has default value at top level (`http://default.com`)
- `environment` only exists inside profiles, no default value
- When Maven resolves `${environment}` without active profile, it's undefined

**Fixed pom.xml (Solution 1 - Add default environment):**
```xml
<properties>
    <base.url>http://default.com</base.url>
    <environment>DEV</environment> <!-- ADD DEFAULT -->
</properties>

<profiles>
    <profile>
        <id>qa</id>
        <properties>
            <base.url>https://qa.company.com</base.url>
            <environment>QA</environment> <!-- Overrides default -->
        </properties>
    </profile>

    <profile>
        <id>prod</id>
        <properties>
            <base.url>https://www.company.com</base.url>
            <environment>PRODUCTION</environment>
        </properties>
    </profile>
</profiles>
```

**Fixed pom.xml (Solution 2 - Make dev profile default):**
```xml
<profiles>
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault> <!-- Default profile -->
        </activation>
        <properties>
            <base.url>http://localhost:8080</base.url>
            <environment>DEV</environment>
        </properties>
    </profile>

    <profile>
        <id>qa</id>
        <properties>
            <base.url>https://qa.company.com</base.url>
            <environment>QA</environment>
        </properties>
    </profile>
</profiles>
```

**Verify:**
```bash
# Test default profile
mvn clean test
# Should use DEV environment

# Test QA profile
mvn clean test -Pqa
# Output:
# Base URL: https://qa.company.com
# Environment: QA

# Verify active profiles
mvn help:active-profiles -Pqa
# Output shows: qa profile is active
```

**Key Learning:**
- All properties used in configuration should have defaults
- Use `activeByDefault` to specify default profile
- Verify profile activation with `mvn help:active-profiles -P<profile-id>`
- Undefined properties silently become null/empty

---

## Challenge 6: Missing Test Resources - File Not Found

**Project Structure:**
```
api-tests/
├── pom.xml
├── src/
│   └── test/
│       ├── java/
│       │   └── UserTest.java
│       └── resources/
│           └── testdata/
│               └── user.json  (File exists here)
```

**Test Code:**
```java
import java.io.File;
import java.nio.file.Files;
import org.testng.annotations.Test;

public class UserTest {

    @Test
    public void testWithJsonFile() throws Exception {
        // Trying to read test data file
        File jsonFile = new File("testdata/user.json");

        if (!jsonFile.exists()) {
            System.out.println("File not found at: " + jsonFile.getAbsolutePath());
            throw new RuntimeException("Test data file not found!");
        }

        String json = new String(Files.readAllBytes(jsonFile.toPath()));
        System.out.println("JSON: " + json);
    }
}
```

**user.json (src/test/resources/testdata/user.json):**
```json
{
  "name": "John Doe",
  "job": "SDET"
}
```

**Run:**
```bash
mvn clean test
```

**Error:**
```
File not found at: /home/user/api-tests/testdata/user.json
java.lang.RuntimeException: Test data file not found!
```

**Problem:** File exists in `src/test/resources/testdata/` but test can't find it!

**Your Task:**
1. Understand Maven resource handling
2. Identify correct way to load resources
3. Fix the file loading code
4. Learn classpath vs file system paths

**Hints:**
- Files in src/test/resources/ are copied to target/test-classes/
- Use ClassLoader to load resources from classpath
- Don't use File() for resources in src/test/resources/

---

**Solution:**

**Bug Location:** Using `new File()` to load resource instead of ClassLoader

**Explanation:**
1. Maven copies `src/test/resources/` to `target/test-classes/` during build
2. File path `testdata/user.json` looks in project root directory, not classpath
3. The file is in classpath at `target/test-classes/testdata/user.json`
4. Need to use ClassLoader or getResourceAsStream() to load from classpath

**Fixed Code (Solution 1 - Using ClassLoader):**
```java
import java.io.InputStream;
import java.nio.charset.StandardCharsets;
import org.testng.annotations.Test;

public class UserTest {

    @Test
    public void testWithJsonFile() throws Exception {
        // Load from classpath using ClassLoader
        ClassLoader classLoader = getClass().getClassLoader();
        InputStream inputStream = classLoader.getResourceAsStream("testdata/user.json");

        if (inputStream == null) {
            throw new RuntimeException("Test data file not found!");
        }

        String json = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        System.out.println("JSON: " + json);
        inputStream.close();

        // Now use the JSON in your test
        given()
            .contentType("application/json")
            .body(json)
        .when()
            .post("https://reqres.in/api/users")
        .then()
            .statusCode(201);
    }
}
```

**Fixed Code (Solution 2 - Using getResourceAsStream directly):**
```java
@Test
public void testWithJsonFile() throws Exception {
    // Direct resource loading
    InputStream inputStream = getClass()
        .getResourceAsStream("/testdata/user.json"); // Note leading "/"

    String json = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
    System.out.println("JSON: " + json);
}
```

**Fixed Code (Solution 3 - Using File with correct path):**
```java
@Test
public void testWithJsonFile() throws Exception {
    // If you must use File, reference the actual location
    File jsonFile = new File("target/test-classes/testdata/user.json");

    String json = new String(Files.readAllBytes(jsonFile.toPath()));
    System.out.println("JSON: " + json);
}
```

**Best Practice - Create Utility Method:**
```java
public class TestDataReader {

    public static String readJsonFile(String fileName) {
        try {
            InputStream inputStream = TestDataReader.class
                .getClassLoader()
                .getResourceAsStream("testdata/" + fileName);

            if (inputStream == null) {
                throw new RuntimeException("File not found: " + fileName);
            }

            return new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        } catch (Exception e) {
            throw new RuntimeException("Error reading file: " + fileName, e);
        }
    }
}

// Usage in tests
@Test
public void testCreateUser() {
    String json = TestDataReader.readJsonFile("user.json");

    given()
        .contentType("application/json")
        .body(json)
    .when()
        .post("https://reqres.in/api/users")
    .then()
        .statusCode(201);
}
```

**Verify:**
```bash
mvn clean test

# Check that resources are copied
ls target/test-classes/testdata/
# Should show: user.json
```

**Key Learning:**
- Files in `src/test/resources/` → copied to `target/test-classes/`
- Use ClassLoader to load resources from classpath
- `getResourceAsStream()` for files in src/test/resources/
- Leading "/" in resource path = absolute classpath path
- No leading "/" = relative to package

---

## Challenge 7: Stale Build - Old Code Executing

**Scenario:**
You fixed a bug in your code, but the test still fails with the old behavior.

**Original Code (UserAPI.java):**
```java
package com.automation.utils;

public class UserAPI {
    public static String getBaseUrl() {
        return "https://reqres.in/api"; // Old URL
    }
}
```

**You changed it to:**
```java
package com.automation.utils;

public class UserAPI {
    public static String getBaseUrl() {
        return "https://reqres.in/api/v2"; // New URL
    }
}
```

**Test:**
```java
@Test
public void testGetUser() {
    String url = UserAPI.getBaseUrl() + "/users/2";
    System.out.println("Using URL: " + url);

    given()
        .when()
            .get(url)
        .then()
            .statusCode(200);
}
```

**Run:**
```bash
mvn test
```

**Output:**
```
Using URL: https://reqres.in/api/users/2
```

**Problem:** Still shows old URL! You changed the code but it's using old version!

**Your Task:**
1. Understand Maven incremental compilation
2. Identify when stale classes cause issues
3. Fix the build process
4. Know when to use `clean`

**Hints:**
- Check target/classes/ for compiled .class files
- Maven doesn't always detect file changes
- Old compiled classes can persist

---

**Solution:**

**Bug Location:** Stale compiled class in `target/classes/` from previous build

**Explanation:**
1. First compilation created `UserAPI.class` in `target/classes/`
2. You changed source code but didn't run `clean`
3. Maven's incremental compilation sometimes misses changes
4. Old `.class` file still exists and gets executed
5. Result: Code changes don't reflect in test execution

**Fix:**
```bash
# ALWAYS clean before important builds
mvn clean test

# Or clean and compile
mvn clean compile

# Or clean and package
mvn clean package
```

**Why this happens:**
- Maven tries to be smart about recompilation (speed optimization)
- Sometimes timestamps get out of sync
- Manual file edits, IDE compilation can cause mismatch
- target/ directory can have stale artifacts

**Verification:**
```bash
# Delete target directory manually
rm -rf target/

# Compile fresh
mvn compile

# Check compiled class
javap target/classes/com/automation/utils/UserAPI.class
# Should show new method implementation
```

**Fixed Output:**
```bash
mvn clean test

# Output:
Using URL: https://reqres.in/api/v2/users/2
```

**When to use `mvn clean`:**
- ✅ Before demos or important runs
- ✅ After pulling code from Git
- ✅ When tests behave unexpectedly
- ✅ Before building release JARs
- ✅ In CI/CD pipelines (always!)
- ❌ Not needed for every development build (slow)

**Best Practices:**
```bash
# Development (quick feedback)
mvn test

# Before commit
mvn clean test

# Before push
mvn clean install

# CI/CD pipeline
mvn clean verify
```

**Key Learning:**
- `target/` can contain stale artifacts
- Always use `mvn clean` when behavior seems wrong
- CI/CD should ALWAYS use `clean`
- Add `target/` to `.gitignore`

---

## 🎯 Summary of Common Maven Issues

| Issue | Symptom | Root Cause | Solution |
|-------|---------|------------|----------|
| **Dependency Conflict** | NoSuchMethodError | Version mismatch | Check dependency:tree, use exclusions |
| **Wrong Scope** | Compilation error | runtime/provided scope instead of test | Change to `<scope>test</scope>` |
| **Tests Not Running** | "No tests to run" | Missing Surefire config | Add Surefire plugin with TestNG suite |
| **Compiler Version** | "Source option 5 not supported" | No Java version specified | Add compiler properties (Java 11+) |
| **Profile Not Working** | Wrong environment | Property undefined without profile | Add default values or activeByDefault |
| **Resource Not Found** | FileNotFoundException | Using File() instead of ClassLoader | Use getResourceAsStream() |
| **Stale Build** | Old code executes | Cached .class files in target/ | Run `mvn clean` |

---

## 💡 Pro Debugging Commands

```bash
# View full build log
mvn clean test -X

# Show dependency tree
mvn dependency:tree

# Show dependency conflicts
mvn dependency:tree -Dverbose

# Show active profiles
mvn help:active-profiles -P<profile-id>

# Show effective POM (with all inheritance and properties resolved)
mvn help:effective-pom

# Analyze dependency issues
mvn dependency:analyze

# Check for plugin updates
mvn versions:display-plugin-updates

# Check for dependency updates
mvn versions:display-dependency-updates

# Force re-download dependencies
mvn clean install -U

# Skip tests (compilation still happens)
mvn clean install -DskipTests

# Skip test compilation and execution
mvn clean install -Dmaven.test.skip=true

# Run in offline mode (no internet)
mvn clean test -o
```

---

**Debugging Mastery Achieved!** 🎉

**Next:** Day-4-Project-Guide.md
