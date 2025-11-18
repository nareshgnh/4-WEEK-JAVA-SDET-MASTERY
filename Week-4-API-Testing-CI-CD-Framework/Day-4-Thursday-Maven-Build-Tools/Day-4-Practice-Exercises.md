# DAY 4 PRACTICE EXERCISES

## 🎯 Today's Practice Goal

Master Maven project setup, dependency management, and build configuration by creating real-world automation projects with proper Maven structure, profiles, and plugins.

**Time Required:** 60-70 minutes

---

## 🏃 Warm-Up Exercises (20 min)

### Exercise 1: Create Maven Project from Scratch

**Objective:** Learn to create a Maven project manually and understand the basic structure.

**Task:**
1. Create a Maven project structure manually (without IDE)
2. Write a simple pom.xml
3. Add a Java class and compile it using Maven
4. Verify the build output

**Step-by-Step Instructions:**

```bash
# Step 1: Create directory structure
mkdir -p maven-basics
cd maven-basics
mkdir -p src/main/java/com/practice
mkdir -p src/test/java/com/practice

# Step 2: Create pom.xml (copy content below)
```

**Create pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.practice</groupId>
    <artifactId>maven-basics</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>Maven Basics Practice</name>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

</project>
```

**Create src/main/java/com/practice/Calculator.java:**
```java
package com.practice;

public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public double divide(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("Cannot divide by zero");
        }
        return (double) a / b;
    }

    public static void main(String[] args) {
        Calculator calc = new Calculator();
        System.out.println("Addition: 10 + 5 = " + calc.add(10, 5));
        System.out.println("Subtraction: 10 - 5 = " + calc.subtract(10, 5));
        System.out.println("Multiplication: 10 * 5 = " + calc.multiply(10, 5));
        System.out.println("Division: 10 / 5 = " + calc.divide(10, 5));
    }
}
```

**Run Maven Commands:**
```bash
# Compile the code
mvn compile

# Expected output:
# [INFO] Compiling 1 source file to target/classes
# [INFO] BUILD SUCCESS

# Run the main class
mvn exec:java -Dexec.mainClass="com.practice.Calculator"

# Package into JAR
mvn package

# Check output
ls -l target/
# You should see: maven-basics-1.0-SNAPSHOT.jar
```

**Verification Checklist:**
- [ ] Directory structure created correctly
- [ ] pom.xml is valid (no errors)
- [ ] `mvn compile` succeeds
- [ ] target/classes/ contains compiled .class files
- [ ] `mvn package` creates JAR file

**Expected Output:**
```
Addition: 10 + 5 = 15
Subtraction: 10 - 5 = 5
Multiplication: 10 * 5 = 50
Division: 10 / 5 = 2.0
```

**Hints:**
- Make sure package declaration matches directory structure
- Check Java version (java -version) matches pom.xml properties
- If build fails, run with `-e` flag: `mvn compile -e`

---

### Exercise 2: Add Dependencies to Project

**Objective:** Learn to add external dependencies and use them in code.

**Task:**
Add Jackson library for JSON processing and write code to serialize/deserialize objects.

**Update pom.xml (add dependencies section):**
```xml
<dependencies>
    <!-- Jackson for JSON processing -->
    <dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.15.0</version>
    </dependency>
</dependencies>
```

**Create src/main/java/com/practice/User.java:**
```java
package com.practice;

public class User {
    private int id;
    private String name;
    private String email;

    // Default constructor (required for Jackson)
    public User() {
    }

    public User(int id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
    }

    // Getters and Setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "', email='" + email + "'}";
    }
}
```

**Create src/main/java/com/practice/JsonDemo.java:**
```java
package com.practice;

import com.fasterxml.jackson.databind.ObjectMapper;

public class JsonDemo {

    public static void main(String[] args) {
        try {
            ObjectMapper mapper = new ObjectMapper();

            // Java object to JSON (Serialization)
            User user = new User(1, "John Doe", "john@example.com");
            String json = mapper.writeValueAsString(user);
            System.out.println("Serialized JSON:");
            System.out.println(json);

            // JSON to Java object (Deserialization)
            String jsonString = "{\"id\":2,\"name\":\"Jane Smith\",\"email\":\"jane@example.com\"}";
            User deserializedUser = mapper.readValue(jsonString, User.class);
            System.out.println("\nDeserialized Object:");
            System.out.println(deserializedUser);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

**Run:**
```bash
# Maven automatically downloads Jackson JARs
mvn clean compile

# Run the demo
mvn exec:java -Dexec.mainClass="com.practice.JsonDemo"
```

**Expected Output:**
```
Serialized JSON:
{"id":1,"name":"John Doe","email":"john@example.com"}

Deserialized Object:
User{id=2, name='Jane Smith', email='jane@example.com'}
```

**Hints:**
- Check ~/.m2/repository/com/fasterxml/jackson/ to see downloaded JARs
- Run `mvn dependency:tree` to see Jackson's transitive dependencies
- If "package com.fasterxml.jackson does not exist", run `mvn clean compile`

---

### Exercise 3: View and Analyze Dependency Tree

**Objective:** Understand transitive dependencies and how Maven resolves them.

**Task:**
Analyze the dependency tree for Jackson library and understand what it brings in.

**Commands:**
```bash
# View full dependency tree
mvn dependency:tree

# View dependency tree for specific dependency
mvn dependency:tree -Dincludes=com.fasterxml.jackson.core

# Export to file for analysis
mvn dependency:tree > dependencies.txt
```

**Expected Output:**
```
[INFO] com.practice:maven-basics:jar:1.0-SNAPSHOT
[INFO] \- com.fasterxml.jackson.core:jackson-databind:jar:2.15.0:compile
[INFO]    +- com.fasterxml.jackson.core:jackson-annotations:jar:2.15.0:compile
[INFO]    \- com.fasterxml.jackson.core:jackson-core:jar:2.15.0:compile
```

**Analysis Questions:**
1. How many JAR files did Maven download when you added jackson-databind?
2. What are the transitive dependencies?
3. What scope are they in?

**Answers:**
1. Three JARs: jackson-databind, jackson-annotations, jackson-core
2. jackson-annotations and jackson-core (dependencies of jackson-databind)
3. All are in 'compile' scope (available everywhere)

---

## 💪 Core Practice (30 min)

### Exercise 4: Create API Automation Project with Complete Dependencies

**Objective:** Set up a professional API automation project with all necessary dependencies and proper structure.

**Task:**
Create a complete Maven project for API automation with RestAssured, TestNG, Jackson, and reporting libraries.

**Project Structure:**
```
api-automation-framework/
├── src/
│   ├── main/java/com/automation/
│   │   ├── models/
│   │   │   └── User.java
│   │   └── utils/
│   │       └── ConfigReader.java
│   └── test/java/com/automation/
│       ├── tests/
│       │   └── UserAPITest.java
│       └── base/
│           └── BaseTest.java
├── src/test/resources/
│   └── testng.xml
└── pom.xml
```

**Complete pom.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.automation</groupId>
    <artifactId>api-automation-framework</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>API Automation Framework</name>
    <description>RestAssured API Testing Framework with TestNG</description>

    <!-- Properties for version management -->
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <!-- Dependency Versions -->
        <restassured.version>5.3.0</restassured.version>
        <testng.version>7.7.1</testng.version>
        <jackson.version>2.15.0</jackson.version>
        <extentreports.version>5.0.9</extentreports.version>
        <log4j.version>2.20.0</log4j.version>
        <lombok.version>1.18.28</lombok.version>
    </properties>

    <dependencies>
        <!-- RestAssured for API Testing -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>${restassured.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- RestAssured JSON Schema Validation -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>json-schema-validator</artifactId>
            <version>${restassured.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- TestNG for Test Execution -->
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>${testng.version}</version>
            <scope>test</scope>
        </dependency>

        <!-- Jackson for JSON Processing -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${jackson.version}</version>
        </dependency>

        <!-- Extent Reports for Test Reporting -->
        <dependency>
            <groupId>com.aventstack</groupId>
            <artifactId>extentreports</artifactId>
            <version>${extentreports.version}</version>
        </dependency>

        <!-- Log4j for Logging -->
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-core</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-api</artifactId>
            <version>${log4j.version}</version>
        </dependency>

        <!-- Lombok for reducing boilerplate code -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- JSON Simple for JSON parsing -->
        <dependency>
            <groupId>com.googlecode.json-simple</groupId>
            <artifactId>json-simple</artifactId>
            <version>1.1.1</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Maven Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                </configuration>
            </plugin>

            <!-- Maven Surefire Plugin for running tests -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0</version>
                <configuration>
                    <suiteXmlFiles>
                        <suiteXmlFile>src/test/resources/testng.xml</suiteXmlFile>
                    </suiteXmlFiles>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

**Create src/main/java/com/automation/models/User.java:**
```java
package com.automation.models;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@JsonIgnoreProperties(ignoreUnknown = true)
public class User {
    private int id;
    private String name;
    private String email;
    private String gender;
    private String status;

    // Default constructor
    public User() {
    }

    public User(String name, String email, String gender, String status) {
        this.name = name;
        this.email = email;
        this.gender = gender;
        this.status = status;
    }

    // Getters and Setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", gender='" + gender + '\'' +
                ", status='" + status + '\'' +
                '}';
    }
}
```

**Create src/test/java/com/automation/base/BaseTest.java:**
```java
package com.automation.base;

import io.restassured.RestAssured;
import org.testng.annotations.BeforeClass;

public class BaseTest {

    @BeforeClass
    public void setup() {
        RestAssured.baseURI = "https://reqres.in";
        RestAssured.basePath = "/api";
        System.out.println("Base URL configured: " + RestAssured.baseURI + RestAssured.basePath);
    }
}
```

**Create src/test/java/com/automation/tests/UserAPITest.java:**
```java
package com.automation.tests;

import com.automation.base.BaseTest;
import io.restassured.response.Response;
import org.testng.Assert;
import org.testng.annotations.Test;

import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class UserAPITest extends BaseTest {

    @Test(priority = 1)
    public void testGetUsers() {
        given()
            .log().all()
        .when()
            .get("/users?page=2")
        .then()
            .log().all()
            .statusCode(200)
            .body("page", equalTo(2))
            .body("data", not(empty()));
    }

    @Test(priority = 2)
    public void testGetSingleUser() {
        given()
            .pathParam("id", 2)
        .when()
            .get("/users/{id}")
        .then()
            .statusCode(200)
            .body("data.id", equalTo(2))
            .body("data.email", containsString("@reqres.in"));
    }

    @Test(priority = 3)
    public void testUserNotFound() {
        given()
            .pathParam("id", 999)
        .when()
            .get("/users/{id}")
        .then()
            .statusCode(404);
    }

    @Test(priority = 4)
    public void testCreateUser() {
        String requestBody = "{\n" +
                "    \"name\": \"John Doe\",\n" +
                "    \"job\": \"SDET\"\n" +
                "}";

        Response response = given()
            .header("Content-Type", "application/json")
            .body(requestBody)
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .body("name", equalTo("John Doe"))
            .body("job", equalTo("SDET"))
            .body("id", notNullValue())
            .body("createdAt", notNullValue())
            .extract().response();

        // Additional assertions
        String userId = response.path("id");
        Assert.assertNotNull(userId, "User ID should not be null");
        System.out.println("Created User ID: " + userId);
    }
}
```

**Create src/test/resources/testng.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="API Automation Test Suite">
    <test name="User API Tests">
        <classes>
            <class name="com.automation.tests.UserAPITest"/>
        </classes>
    </test>
</suite>
```

**Run the tests:**
```bash
# Create project structure
mkdir -p api-automation-framework/src/main/java/com/automation/models
mkdir -p api-automation-framework/src/main/java/com/automation/utils
mkdir -p api-automation-framework/src/test/java/com/automation/tests
mkdir -p api-automation-framework/src/test/java/com/automation/base
mkdir -p api-automation-framework/src/test/resources

cd api-automation-framework

# Create all files as shown above

# Run tests
mvn clean test

# View dependency tree
mvn dependency:tree

# Package project
mvn clean package
```

**Expected Output:**
```
[INFO] Running TestSuite
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0
[INFO] BUILD SUCCESS
```

**Verification:**
- [ ] All dependencies download successfully
- [ ] Tests compile without errors
- [ ] All 4 tests pass
- [ ] Surefire reports generated in target/surefire-reports/

---

### Exercise 5: Configure Maven Surefire Plugin for TestNG

**Objective:** Learn to configure Surefire plugin for different test execution scenarios.

**Task:**
Configure Surefire to run tests in parallel and with different TestNG suite files.

**Update pom.xml build section:**
```xml
<build>
    <plugins>
        <!-- Maven Surefire Plugin with advanced configuration -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>3.0.0</version>
            <configuration>
                <!-- TestNG suite file -->
                <suiteXmlFiles>
                    <suiteXmlFile>src/test/resources/testng.xml</suiteXmlFile>
                </suiteXmlFiles>

                <!-- Parallel execution -->
                <parallel>methods</parallel>
                <threadCount>3</threadCount>

                <!-- Include/Exclude tests -->
                <includes>
                    <include>**/*Test.java</include>
                    <include>**/*Tests.java</include>
                </includes>

                <!-- System properties accessible in tests -->
                <systemPropertyVariables>
                    <environment>QA</environment>
                    <browser>chrome</browser>
                </systemPropertyVariables>

                <!-- Continue build even if tests fail -->
                <testFailureIgnore>false</testFailureIgnore>

                <!-- Reporting -->
                <reportFormat>xml</reportFormat>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Create src/test/resources/testng-smoke.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Smoke Test Suite" parallel="methods" thread-count="3">
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="com.automation.tests.UserAPITest"/>
        </classes>
    </test>
</suite>
```

**Update UserAPITest.java to add groups:**
```java
@Test(priority = 1, groups = {"smoke", "regression"})
public void testGetUsers() {
    // ... existing code
}

@Test(priority = 2, groups = {"smoke"})
public void testGetSingleUser() {
    // ... existing code
}

@Test(priority = 3, groups = {"regression"})
public void testUserNotFound() {
    // ... existing code
}

@Test(priority = 4, groups = {"regression"})
public void testCreateUser() {
    // ... existing code
}
```

**Run different scenarios:**
```bash
# Run default suite
mvn test

# Run smoke tests only
mvn test -DsuiteXmlFile=src/test/resources/testng-smoke.xml

# Run specific test class
mvn test -Dtest=UserAPITest

# Run specific test method
mvn test -Dtest=UserAPITest#testGetUsers

# Run with custom properties
mvn test -Denvironment=PROD -Dbrowser=firefox

# Run without parallel execution
mvn test -Dparallel=none
```

**Access system properties in test:**
```java
@Test
public void testSystemProperties() {
    String env = System.getProperty("environment", "DEV");
    String browser = System.getProperty("browser", "chrome");
    System.out.println("Running on: " + env + " with browser: " + browser);
}
```

---

### Exercise 6: Create Multi-Environment Setup Using Profiles

**Objective:** Configure Maven profiles for different environments (dev, QA, staging, prod).

**Task:**
Set up profiles with environment-specific properties and demonstrate switching between them.

**Update pom.xml with profiles:**
```xml
<properties>
    <!-- Default values -->
    <base.url>http://localhost:8080</base.url>
    <test.suite>testng.xml</test.suite>
    <log.level>INFO</log.level>
</properties>

<profiles>
    <!-- Development Environment -->
    <profile>
        <id>dev</id>
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
        <properties>
            <base.url>http://localhost:8080</base.url>
            <test.suite>testng-smoke.xml</test.suite>
            <log.level>DEBUG</log.level>
            <environment>DEVELOPMENT</environment>
        </properties>
    </profile>

    <!-- QA Environment -->
    <profile>
        <id>qa</id>
        <properties>
            <base.url>https://qa.reqres.in</base.url>
            <test.suite>testng-regression.xml</test.suite>
            <log.level>INFO</log.level>
            <environment>QA</environment>
        </properties>
    </profile>

    <!-- Staging Environment -->
    <profile>
        <id>staging</id>
        <properties>
            <base.url>https://staging.reqres.in</base.url>
            <test.suite>testng-full.xml</test.suite>
            <log.level>WARN</log.level>
            <environment>STAGING</environment>
        </properties>
    </profile>

    <!-- Production Environment (Smoke tests only) -->
    <profile>
        <id>prod</id>
        <properties>
            <base.url>https://reqres.in</base.url>
            <test.suite>testng-smoke.xml</test.suite>
            <log.level>ERROR</log.level>
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
                <suiteXmlFiles>
                    <suiteXmlFile>src/test/resources/${test.suite}</suiteXmlFile>
                </suiteXmlFiles>
                <systemPropertyVariables>
                    <baseUrl>${base.url}</baseUrl>
                    <environment>${environment}</environment>
                    <logLevel>${log.level}</logLevel>
                </systemPropertyVariables>
            </configuration>
        </plugin>
    </plugins>
</build>
```

**Create src/main/java/com/automation/utils/ConfigReader.java:**
```java
package com.automation.utils;

public class ConfigReader {

    public static String getBaseUrl() {
        return System.getProperty("baseUrl", "https://reqres.in");
    }

    public static String getEnvironment() {
        return System.getProperty("environment", "DEV");
    }

    public static String getLogLevel() {
        return System.getProperty("logLevel", "INFO");
    }

    public static void printConfiguration() {
        System.out.println("========== TEST CONFIGURATION ==========");
        System.out.println("Environment: " + getEnvironment());
        System.out.println("Base URL: " + getBaseUrl());
        System.out.println("Log Level: " + getLogLevel());
        System.out.println("========================================");
    }
}
```

**Update BaseTest.java to use ConfigReader:**
```java
package com.automation.base;

import com.automation.utils.ConfigReader;
import io.restassured.RestAssured;
import org.testng.annotations.BeforeClass;

public class BaseTest {

    @BeforeClass
    public void setup() {
        // Print configuration
        ConfigReader.printConfiguration();

        // Set base URL from Maven profile
        RestAssured.baseURI = ConfigReader.getBaseUrl();
        RestAssured.basePath = "/api";

        System.out.println("Base URL configured: " + RestAssured.baseURI + RestAssured.basePath);
    }
}
```

**Create testng-regression.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Regression Test Suite">
    <test name="All Tests">
        <classes>
            <class name="com.automation.tests.UserAPITest"/>
        </classes>
    </test>
</suite>
```

**Run with different profiles:**
```bash
# Run with dev profile (default)
mvn clean test
# Uses: http://localhost:8080, testng-smoke.xml, DEBUG logging

# Run with QA profile
mvn clean test -Pqa
# Uses: https://qa.reqres.in, testng-regression.xml, INFO logging

# Run with staging profile
mvn clean test -Pstaging
# Uses: https://staging.reqres.in, testng-full.xml, WARN logging

# Run with prod profile
mvn clean test -Pprod
# Uses: https://reqres.in, testng-smoke.xml, ERROR logging

# Override base URL from command line
mvn clean test -Pqa -DbaseUrl=https://custom-qa.example.com
```

**Expected Output (QA Profile):**
```
========== TEST CONFIGURATION ==========
Environment: QA
Base URL: https://qa.reqres.in
Log Level: INFO
========================================
Base URL configured: https://qa.reqres.in/api
[INFO] Tests run: 4, Failures: 0, Errors: 0, Skipped: 0
```

---

## 🚀 Challenge Exercise (20 min)

### Exercise 7: Build Complete Multi-Module Maven Project

**Objective:** Create a multi-module Maven project simulating a real enterprise automation framework structure.

**Task:**
Create a parent-child multi-module project with shared configurations.

**Project Structure:**
```
automation-parent/
├── pom.xml (parent POM)
├── api-tests/
│   ├── pom.xml
│   └── src/...
└── ui-tests/
    ├── pom.xml
    └── src/...
```

**Parent pom.xml (automation-parent/pom.xml):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.automation</groupId>
    <artifactId>automation-parent</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <name>Automation Parent Project</name>

    <!-- Child modules -->
    <modules>
        <module>api-tests</module>
        <module>ui-tests</module>
    </modules>

    <!-- Shared properties -->
    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <!-- Shared dependency versions -->
        <testng.version>7.7.1</testng.version>
        <restassured.version>5.3.0</restassured.version>
        <selenium.version>4.10.0</selenium.version>
    </properties>

    <!-- Dependency Management (versions defined here, used in child POMs) -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.testng</groupId>
                <artifactId>testng</artifactId>
                <version>${testng.version}</version>
                <scope>test</scope>
            </dependency>

            <dependency>
                <groupId>io.rest-assured</groupId>
                <artifactId>rest-assured</artifactId>
                <version>${restassured.version}</version>
                <scope>test</scope>
            </dependency>

            <dependency>
                <groupId>org.seleniumhq.selenium</groupId>
                <artifactId>selenium-java</artifactId>
                <version>${selenium.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <!-- Shared build configuration -->
    <build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.11.0</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>

                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-surefire-plugin</artifactId>
                    <version>3.0.0</version>
                </plugin>
            </plugins>
        </pluginManagement>
    </build>

</project>
```

**Child POM 1 (automation-parent/api-tests/pom.xml):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <!-- Parent reference -->
    <parent>
        <groupId>com.automation</groupId>
        <artifactId>automation-parent</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>api-tests</artifactId>
    <name>API Tests Module</name>

    <!-- Dependencies (versions inherited from parent) -->
    <dependencies>
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <!-- Version inherited from parent dependencyManagement -->
        </dependency>

        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <!-- Version inherited from parent dependencyManagement -->
        </dependency>
    </dependencies>

</project>
```

**Child POM 2 (automation-parent/ui-tests/pom.xml):**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.automation</groupId>
        <artifactId>automation-parent</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>ui-tests</artifactId>
    <name>UI Tests Module</name>

    <dependencies>
        <dependency>
            <groupId>org.seleniumhq.selenium</groupId>
            <artifactId>selenium-java</artifactId>
            <!-- Version inherited from parent -->
        </dependency>

        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <!-- Version inherited from parent -->
        </dependency>
    </dependencies>

</project>
```

**Build commands:**
```bash
# From parent directory
cd automation-parent

# Build all modules
mvn clean install

# Build specific module
mvn clean install -pl api-tests

# Build module and its dependencies
mvn clean install -pl ui-tests -am

# Run tests in all modules
mvn clean test

# Run tests in specific module
cd api-tests
mvn test
```

**Benefits of Multi-Module:**
- Centralized dependency version management
- Shared build configuration
- Build all modules with one command
- Reusable across team
- Enterprise-standard structure

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution

**Complete working structure created.**

**Key Takeaways:**
- Maven follows strict directory conventions
- pom.xml must be at project root
- Source code goes in src/main/java
- Package structure must match directory structure
- target/ is auto-generated (add to .gitignore)

---

### Exercise 2 Solution

**Jackson dependency added and used successfully.**

**Key Takeaways:**
- Dependencies auto-download from Maven Central
- Transitive dependencies handled automatically
- External libraries immediately available in code
- No manual JAR management needed

---

### Exercise 3 Solution

**Dependency tree shows:**
- jackson-databind (what we added)
- jackson-annotations (transitive)
- jackson-core (transitive)

**Key Takeaways:**
- One dependency can bring 10+ JARs
- Maven handles all transitive dependencies
- Use `dependency:tree` to troubleshoot conflicts

---

### Exercise 4 Solution

**Complete API automation framework created.**

**Key Takeaways:**
- Professional projects use properties for versions
- Test scope for test-only dependencies
- Surefire plugin required for TestNG execution
- Clear separation: main code vs test code

---

### Exercise 5 Solution

**Surefire configured for parallel execution and groups.**

**Key Takeaways:**
- Surefire plugin controls test execution
- Can run tests in parallel for speed
- System properties pass config to tests
- Different suite files for different scenarios

---

### Exercise 6 Solution

**Multi-environment setup working with profiles.**

**Key Takeaways:**
- Profiles enable environment-specific configurations
- Properties can be overridden at command line
- One codebase, multiple environments
- Critical for CI/CD pipelines

---

### Exercise 7 Solution

**Multi-module project structure created.**

**Key Takeaways:**
- Parent POM defines shared configuration
- Child POMs inherit from parent
- Centralized version management
- Enterprise-standard approach

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Maven Project | ☐ | __/10 | ___ min | |
| 2 - Add Dependencies | ☐ | __/10 | ___ min | |
| 3 - Dependency Tree | ☐ | __/10 | ___ min | |
| 4 - Complete Framework | ☐ | __/10 | ___ min | |
| 5 - Surefire Config | ☐ | __/10 | ___ min | |
| 6 - Maven Profiles | ☐ | __/10 | ___ min | |
| 7 - Multi-Module | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ minutes

**Areas I struggled with:**
-
-

**Areas I excelled at:**
-
-

**Command Line Confidence:**
- [ ] Comfortable with `mvn clean test`
- [ ] Can run specific tests
- [ ] Understand profiles
- [ ] Can troubleshoot build failures

---

## ✅ Answers to Self-Check Questions (from Concept Guide)

1. **What are the three components of Maven coordinates (GAV)?**
   - GroupId (organization), ArtifactId (project name), Version (version number)

2. **What is the difference between `compile` and `test` dependency scope?**
   - compile: Available everywhere (main code, test code, runtime, packaged in JAR)
   - test: Only available in test code and test execution (NOT packaged in JAR)

3. **Name the Maven phases that execute when you run `mvn package`.**
   - validate → compile → test → package

4. **How do you run only smoke tests using Maven?**
   - `mvn test -Dgroups=smoke` (TestNG groups)
   - OR `mvn test -DsuiteXmlFile=testng-smoke.xml`

5. **What command would you use to see all transitive dependencies?**
   - `mvn dependency:tree`

---

**Next:** Day-4-Debug-Practice.md
