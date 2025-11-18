# DAY 4 PROJECT: PROFESSIONAL API AUTOMATION FRAMEWORK WITH MAVEN

## 🎯 Project Overview

**What You're Building:**
A production-ready API automation framework with complete Maven configuration, multi-environment support, proper dependency management, test execution via command line, and professional project structure.

**Why This Project:**
This is exactly what you'll build in real SDET roles at product companies. This framework demonstrates:
- Professional Maven project structure
- Multi-environment configuration using profiles
- Proper dependency management
- Reusable utilities and base classes
- POJO-based test design
- Command-line test execution
- Report generation
- CI/CD ready configuration

**Time Required:** 45-60 minutes

**Skills Practiced:**
- Maven project setup from scratch
- pom.xml configuration (dependencies, plugins, profiles)
- Multi-environment setup
- Surefire plugin configuration
- Build lifecycle management
- Test execution from command line

---

## 📋 Requirements

### Must-Have Features:
1. **Maven Project Structure** - Professional directory layout
2. **Complete pom.xml** - All dependencies, plugins, profiles configured
3. **Multi-Environment Support** - dev, qa, staging, prod profiles
4. **POJO Models** - User, LoginRequest, LoginResponse classes
5. **Base Test Class** - Centralized RestAssured configuration
6. **API Test Suite** - CRUD operations (Create, Read, Update, Delete)
7. **Utility Classes** - ConfigReader, TestDataReader, JsonUtils
8. **TestNG Integration** - Multiple TestNG suite files
9. **Command-Line Execution** - Run tests via Maven commands
10. **Test Reports** - Surefire reports with detailed results

### Nice-to-Have (Bonus):
1. Extent Reports integration
2. Log4j logging configuration
3. JSON schema validation
4. Response time assertions
5. Data-driven tests using CSV/JSON files

---

## 🏗️ Project Structure

```
restassured-maven-framework/
│
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/automation/
│   │   │       ├── config/
│   │   │       │   └── ConfigReader.java
│   │   │       ├── models/
│   │   │       │   ├── User.java
│   │   │       │   ├── LoginRequest.java
│   │   │       │   └── LoginResponse.java
│   │   │       └── utils/
│   │   │           ├── JsonUtils.java
│   │   │           └── TestDataReader.java
│   │   └── resources/
│   │       └── config.properties
│   │
│   └── test/
│       ├── java/
│       │   └── com/automation/
│       │       ├── base/
│       │       │   └── BaseTest.java
│       │       └── tests/
│       │           ├── UserAPITest.java
│       │           ├── AuthenticationTest.java
│       │           └── CRUDWorkflowTest.java
│       └── resources/
│           ├── testdata/
│           │   ├── users.json
│           │   └── login.json
│           ├── testng-smoke.xml
│           ├── testng-regression.xml
│           └── testng-full.xml
│
├── pom.xml
├── .gitignore
└── README.md
```

---

## 📝 Step-by-Step Implementation

### Step 1: Create Project Structure

**Create directories:**
```bash
mkdir -p restassured-maven-framework
cd restassured-maven-framework

# Create main source structure
mkdir -p src/main/java/com/automation/config
mkdir -p src/main/java/com/automation/models
mkdir -p src/main/java/com/automation/utils
mkdir -p src/main/resources

# Create test structure
mkdir -p src/test/java/com/automation/base
mkdir -p src/test/java/com/automation/tests
mkdir -p src/test/resources/testdata
```

---

### Step 2: Create Complete pom.xml

**File:** `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <!-- Project Coordinates -->
    <groupId>com.automation</groupId>
    <artifactId>restassured-maven-framework</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <name>RestAssured Maven Framework</name>
    <description>Production-ready API Automation Framework with Maven</description>

    <!-- Properties - Centralized Version Management -->
    <properties>
        <!-- Java Version -->
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
        <commons-io.version>2.11.0</commons-io.version>
        <json-simple.version>1.1.1</json-simple.version>

        <!-- Plugin Versions -->
        <maven-compiler-plugin.version>3.11.0</maven-compiler-plugin.version>
        <maven-surefire-plugin.version>3.0.0</maven-surefire-plugin.version>

        <!-- Default Environment Properties (overridden by profiles) -->
        <base.url>http://localhost:8080</base.url>
        <environment>DEV</environment>
        <test.suite>testng-smoke.xml</test.suite>
    </properties>

    <!-- Dependencies -->
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

        <!-- Jackson for JSON Processing (Serialization/Deserialization) -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>${jackson.version}</version>
        </dependency>

        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>${jackson.version}</version>
        </dependency>

        <!-- Extent Reports for Beautiful Test Reports -->
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

        <!-- Lombok for reducing boilerplate code in POJOs -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>${lombok.version}</version>
            <scope>provided</scope>
        </dependency>

        <!-- Commons IO for file operations -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>${commons-io.version}</version>
        </dependency>

        <!-- JSON Simple for JSON parsing -->
        <dependency>
            <groupId>com.googlecode.json-simple</groupId>
            <artifactId>json-simple</artifactId>
            <version>${json-simple.version}</version>
        </dependency>
    </dependencies>

    <!-- Build Configuration -->
    <build>
        <plugins>
            <!-- Maven Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>${maven-compiler-plugin.version}</version>
                <configuration>
                    <source>11</source>
                    <target>11</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>

            <!-- Maven Surefire Plugin for running tests -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>${maven-surefire-plugin.version}</version>
                <configuration>
                    <!-- TestNG Suite File (dynamic based on profile) -->
                    <suiteXmlFiles>
                        <suiteXmlFile>src/test/resources/${test.suite}</suiteXmlFile>
                    </suiteXmlFiles>

                    <!-- Parallel Execution -->
                    <parallel>methods</parallel>
                    <threadCount>3</threadCount>

                    <!-- System Properties (accessible in tests) -->
                    <systemPropertyVariables>
                        <baseUrl>${base.url}</baseUrl>
                        <environment>${environment}</environment>
                    </systemPropertyVariables>

                    <!-- Continue build on test failure (for CI/CD) -->
                    <testFailureIgnore>false</testFailureIgnore>
                </configuration>
            </plugin>
        </plugins>
    </build>

    <!-- Profiles for Multi-Environment Support -->
    <profiles>
        <!-- Development Environment -->
        <profile>
            <id>dev</id>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
            <properties>
                <base.url>http://localhost:8080</base.url>
                <environment>DEVELOPMENT</environment>
                <test.suite>testng-smoke.xml</test.suite>
            </properties>
        </profile>

        <!-- QA Environment -->
        <profile>
            <id>qa</id>
            <properties>
                <base.url>https://reqres.in</base.url>
                <environment>QA</environment>
                <test.suite>testng-regression.xml</test.suite>
            </properties>
        </profile>

        <!-- Staging Environment -->
        <profile>
            <id>staging</id>
            <properties>
                <base.url>https://staging.reqres.in</base.url>
                <environment>STAGING</environment>
                <test.suite>testng-full.xml</test.suite>
            </properties>
        </profile>

        <!-- Production Environment (Smoke Tests Only) -->
        <profile>
            <id>prod</id>
            <properties>
                <base.url>https://reqres.in</base.url>
                <environment>PRODUCTION</environment>
                <test.suite>testng-smoke.xml</test.suite>
            </properties>
        </profile>
    </profiles>

</project>
```

**What This pom.xml Provides:**
- ✅ All dependencies with version management
- ✅ Compiler plugin (Java 11)
- ✅ Surefire plugin (parallel execution, TestNG integration)
- ✅ 4 environment profiles (dev, qa, staging, prod)
- ✅ System properties passed to tests
- ✅ Dynamic suite file selection

---

### Step 3: Create Configuration Files

**File:** `src/main/resources/config.properties`

```properties
# Default Configuration
base.url=https://reqres.in
api.version=/api
timeout=30

# Default Credentials (override in profiles)
admin.username=admin@reqres.in
admin.password=password123

# Test Data
default.user.email=test@example.com
default.user.name=Test User
```

**File:** `src/main/java/com/automation/config/ConfigReader.java`

```java
package com.automation.config;

import java.io.FileInputStream;
import java.io.IOException;
import java.util.Properties;

public class ConfigReader {

    private static Properties properties;

    static {
        try {
            String configPath = "src/main/resources/config.properties";
            FileInputStream fis = new FileInputStream(configPath);
            properties = new Properties();
            properties.load(fis);
            fis.close();
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException("Failed to load config.properties file");
        }
    }

    /**
     * Get base URL - Priority: System Property > config.properties
     */
    public static String getBaseUrl() {
        String baseUrl = System.getProperty("baseUrl");
        if (baseUrl != null && !baseUrl.isEmpty()) {
            return baseUrl;
        }
        return properties.getProperty("base.url");
    }

    /**
     * Get environment
     */
    public static String getEnvironment() {
        String env = System.getProperty("environment");
        if (env != null && !env.isEmpty()) {
            return env;
        }
        return "DEV";
    }

    /**
     * Get API version
     */
    public static String getApiVersion() {
        return properties.getProperty("api.version");
    }

    /**
     * Get request timeout
     */
    public static int getTimeout() {
        return Integer.parseInt(properties.getProperty("timeout"));
    }

    /**
     * Print current configuration
     */
    public static void printConfiguration() {
        System.out.println("╔════════════════════════════════════════════════╗");
        System.out.println("║       TEST CONFIGURATION                       ║");
        System.out.println("╠════════════════════════════════════════════════╣");
        System.out.println("║ Environment  : " + getEnvironment());
        System.out.println("║ Base URL     : " + getBaseUrl());
        System.out.println("║ API Version  : " + getApiVersion());
        System.out.println("║ Timeout      : " + getTimeout() + " seconds");
        System.out.println("╚════════════════════════════════════════════════╝");
    }
}
```

---

### Step 4: Create POJO Models

**File:** `src/main/java/com/automation/models/User.java`

```java
package com.automation.models;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;

@JsonIgnoreProperties(ignoreUnknown = true)
public class User {

    private int id;
    private String email;

    @JsonProperty("first_name")
    private String firstName;

    @JsonProperty("last_name")
    private String lastName;

    private String avatar;

    // Default constructor (required for Jackson)
    public User() {
    }

    public User(String firstName, String lastName, String email) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.email = email;
    }

    // Getters and Setters
    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getFirstName() {
        return firstName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public String getAvatar() {
        return avatar;
    }

    public void setAvatar(String avatar) {
        this.avatar = avatar;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", email='" + email + '\'' +
                ", firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                '}';
    }
}
```

**File:** `src/main/java/com/automation/models/LoginRequest.java`

```java
package com.automation.models;

public class LoginRequest {

    private String email;
    private String password;

    public LoginRequest() {
    }

    public LoginRequest(String email, String password) {
        this.email = email;
        this.password = password;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "LoginRequest{email='" + email + "'}";
    }
}
```

**File:** `src/main/java/com/automation/models/LoginResponse.java`

```java
package com.automation.models;

public class LoginResponse {

    private String token;
    private String error;

    public LoginResponse() {
    }

    public String getToken() {
        return token;
    }

    public void setToken(String token) {
        this.token = token;
    }

    public String getError() {
        return error;
    }

    public void setError(String error) {
        this.error = error;
    }

    @Override
    public String toString() {
        return "LoginResponse{token='" + token + "', error='" + error + "'}";
    }
}
```

---

### Step 5: Create Utility Classes

**File:** `src/main/java/com/automation/utils/JsonUtils.java`

```java
package com.automation.utils;

import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;

public class JsonUtils {

    private static final ObjectMapper objectMapper = new ObjectMapper();

    static {
        objectMapper.enable(SerializationFeature.INDENT_OUTPUT);
    }

    /**
     * Convert Java object to JSON string
     */
    public static String toJson(Object object) {
        try {
            return objectMapper.writeValueAsString(object);
        } catch (Exception e) {
            throw new RuntimeException("Failed to convert object to JSON", e);
        }
    }

    /**
     * Convert JSON string to Java object
     */
    public static <T> T fromJson(String json, Class<T> clazz) {
        try {
            return objectMapper.readValue(json, clazz);
        } catch (Exception e) {
            throw new RuntimeException("Failed to convert JSON to object", e);
        }
    }

    /**
     * Pretty print JSON
     */
    public static void printJson(Object object) {
        System.out.println(toJson(object));
    }
}
```

**File:** `src/main/java/com/automation/utils/TestDataReader.java`

```java
package com.automation.utils;

import java.io.IOException;
import java.io.InputStream;
import java.nio.charset.StandardCharsets;

public class TestDataReader {

    /**
     * Read JSON file from src/test/resources/testdata/
     */
    public static String readJsonFile(String fileName) {
        try {
            InputStream inputStream = TestDataReader.class
                    .getClassLoader()
                    .getResourceAsStream("testdata/" + fileName);

            if (inputStream == null) {
                throw new RuntimeException("Test data file not found: " + fileName);
            }

            return new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        } catch (IOException e) {
            throw new RuntimeException("Error reading test data file: " + fileName, e);
        }
    }

    /**
     * Read JSON and convert to object
     */
    public static <T> T readJsonAsObject(String fileName, Class<T> clazz) {
        String json = readJsonFile(fileName);
        return JsonUtils.fromJson(json, clazz);
    }
}
```

---

### Step 6: Create Base Test Class

**File:** `src/test/java/com/automation/base/BaseTest.java`

```java
package com.automation.base;

import com.automation.config.ConfigReader;
import io.restassured.RestAssured;
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.filter.log.RequestLoggingFilter;
import io.restassured.filter.log.ResponseLoggingFilter;
import io.restassured.http.ContentType;
import io.restassured.specification.RequestSpecification;
import org.testng.annotations.BeforeClass;

public class BaseTest {

    protected RequestSpecification requestSpec;

    @BeforeClass
    public void setup() {
        // Print configuration
        ConfigReader.printConfiguration();

        // Set base URI
        RestAssured.baseURI = ConfigReader.getBaseUrl();
        RestAssured.basePath = ConfigReader.getApiVersion();

        // Build request specification
        requestSpec = new RequestSpecBuilder()
                .setContentType(ContentType.JSON)
                .setAccept(ContentType.JSON)
                .addFilter(new RequestLoggingFilter())   // Log requests
                .addFilter(new ResponseLoggingFilter())  // Log responses
                .build();

        System.out.println("Base URL configured: " + RestAssured.baseURI + RestAssured.basePath);
        System.out.println("Test suite execution started...\n");
    }
}
```

---

### Step 7: Create Test Classes

**File:** `src/test/java/com/automation/tests/AuthenticationTest.java`

```java
package com.automation.tests;

import com.automation.base.BaseTest;
import com.automation.models.LoginRequest;
import com.automation.models.LoginResponse;
import io.restassured.response.Response;
import org.testng.Assert;
import org.testng.annotations.Test;

import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.*;

public class AuthenticationTest extends BaseTest {

    @Test(priority = 1, groups = {"smoke", "authentication"})
    public void testSuccessfulLogin() {
        LoginRequest loginRequest = new LoginRequest("eve.holt@reqres.in", "cityslicka");

        LoginResponse loginResponse = given()
                .spec(requestSpec)
                .body(loginRequest)
        .when()
                .post("/login")
        .then()
                .statusCode(200)
                .body("token", notNullValue())
                .extract().as(LoginResponse.class);

        Assert.assertNotNull(loginResponse.getToken(), "Token should not be null");
        System.out.println("Login successful! Token: " + loginResponse.getToken());
    }

    @Test(priority = 2, groups = {"regression", "authentication"})
    public void testLoginWithoutPassword() {
        LoginRequest loginRequest = new LoginRequest("eve.holt@reqres.in", null);

        given()
                .spec(requestSpec)
                .body(loginRequest)
        .when()
                .post("/login")
        .then()
                .statusCode(400)
                .body("error", equalTo("Missing password"));
    }

    @Test(priority = 3, groups = {"regression", "authentication"})
    public void testLoginWithInvalidUser() {
        LoginRequest loginRequest = new LoginRequest("invalid@example.com", "password");

        given()
                .spec(requestSpec)
                .body(loginRequest)
        .when()
                .post("/login")
        .then()
                .statusCode(400)
                .body("error", notNullValue());
    }
}
```

**File:** `src/test/java/com/automation/tests/UserAPITest.java`

```java
package com.automation.tests;

import com.automation.base.BaseTest;
import com.automation.models.User;
import io.restassured.response.Response;
import org.testng.Assert;
import org.testng.annotations.Test;

import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.*;

public class UserAPITest extends BaseTest {

    @Test(priority = 1, groups = {"smoke", "users"})
    public void testGetAllUsers() {
        given()
                .spec(requestSpec)
                .queryParam("page", 2)
        .when()
                .get("/users")
        .then()
                .statusCode(200)
                .body("page", equalTo(2))
                .body("data", not(empty()))
                .body("data.size()", greaterThan(0));
    }

    @Test(priority = 2, groups = {"smoke", "users"})
    public void testGetSingleUser() {
        User user = given()
                .spec(requestSpec)
                .pathParam("id", 2)
        .when()
                .get("/users/{id}")
        .then()
                .statusCode(200)
                .body("data.id", equalTo(2))
                .body("data.email", containsString("@reqres.in"))
                .body("data.first_name", notNullValue())
                .extract().path("data");

        System.out.println("Retrieved User: " + user);
        Assert.assertEquals(user.getId(), 2);
    }

    @Test(priority = 3, groups = {"regression", "users"})
    public void testGetUserNotFound() {
        given()
                .spec(requestSpec)
                .pathParam("id", 999)
        .when()
                .get("/users/{id}")
        .then()
                .statusCode(404);
    }

    @Test(priority = 4, groups = {"regression", "users"})
    public void testCreateUser() {
        User newUser = new User("John", "Doe", "john.doe@example.com");

        Response response = given()
                .spec(requestSpec)
                .body(newUser)
        .when()
                .post("/users")
        .then()
                .statusCode(201)
                .body("first_name", equalTo("John"))
                .body("last_name", equalTo("Doe"))
                .body("id", notNullValue())
                .body("createdAt", notNullValue())
                .extract().response();

        String userId = response.path("id");
        System.out.println("Created User ID: " + userId);
        Assert.assertNotNull(userId);
    }

    @Test(priority = 5, groups = {"regression", "users"})
    public void testUpdateUser() {
        User updatedUser = new User("Jane", "Smith", "jane.smith@example.com");

        given()
                .spec(requestSpec)
                .pathParam("id", 2)
                .body(updatedUser)
        .when()
                .put("/users/{id}")
        .then()
                .statusCode(200)
                .body("first_name", equalTo("Jane"))
                .body("last_name", equalTo("Smith"))
                .body("updatedAt", notNullValue());
    }

    @Test(priority = 6, groups = {"regression", "users"})
    public void testDeleteUser() {
        given()
                .spec(requestSpec)
                .pathParam("id", 2)
        .when()
                .delete("/users/{id}")
        .then()
                .statusCode(204);
    }
}
```

**File:** `src/test/java/com/automation/tests/CRUDWorkflowTest.java`

```java
package com.automation.tests;

import com.automation.base.BaseTest;
import com.automation.models.User;
import io.restassured.response.Response;
import org.testng.Assert;
import org.testng.annotations.Test;

import static io.restassured.RestAssured.given;

public class CRUDWorkflowTest extends BaseTest {

    private String userId;

    @Test(priority = 1, groups = {"workflow"})
    public void testCreateUserWorkflow() {
        User newUser = new User("Workflow", "Test", "workflow@test.com");

        Response response = given()
                .spec(requestSpec)
                .body(newUser)
        .when()
                .post("/users")
        .then()
                .statusCode(201)
                .extract().response();

        userId = response.path("id");
        System.out.println("Step 1: User created with ID: " + userId);
        Assert.assertNotNull(userId);
    }

    @Test(priority = 2, groups = {"workflow"}, dependsOnMethods = "testCreateUserWorkflow")
    public void testReadUserWorkflow() {
        // In real API, this would retrieve the created user
        // Reqres doesn't persist data, so we use a known user
        given()
                .spec(requestSpec)
                .pathParam("id", 2)
        .when()
                .get("/users/{id}")
        .then()
                .statusCode(200);

        System.out.println("Step 2: User retrieved successfully");
    }

    @Test(priority = 3, groups = {"workflow"}, dependsOnMethods = "testReadUserWorkflow")
    public void testUpdateUserWorkflow() {
        User updatedUser = new User("Updated", "User", "updated@test.com");

        given()
                .spec(requestSpec)
                .pathParam("id", 2)
                .body(updatedUser)
        .when()
                .put("/users/{id}")
        .then()
                .statusCode(200);

        System.out.println("Step 3: User updated successfully");
    }

    @Test(priority = 4, groups = {"workflow"}, dependsOnMethods = "testUpdateUserWorkflow")
    public void testDeleteUserWorkflow() {
        given()
                .spec(requestSpec)
                .pathParam("id", 2)
        .when()
                .delete("/users/{id}")
        .then()
                .statusCode(204);

        System.out.println("Step 4: User deleted successfully");
        System.out.println("Complete CRUD workflow executed!");
    }
}
```

---

### Step 8: Create TestNG Suite Files

**File:** `src/test/resources/testng-smoke.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Smoke Test Suite" parallel="classes" thread-count="2">
    <test name="Smoke Tests">
        <groups>
            <run>
                <include name="smoke"/>
            </run>
        </groups>
        <classes>
            <class name="com.automation.tests.AuthenticationTest"/>
            <class name="com.automation.tests.UserAPITest"/>
        </classes>
    </test>
</suite>
```

**File:** `src/test/resources/testng-regression.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Regression Test Suite" parallel="methods" thread-count="3">
    <test name="Regression Tests">
        <groups>
            <run>
                <include name="regression"/>
            </run>
        </groups>
        <classes>
            <class name="com.automation.tests.AuthenticationTest"/>
            <class name="com.automation.tests.UserAPITest"/>
        </classes>
    </test>
</suite>
```

**File:** `src/test/resources/testng-full.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "https://testng.org/testng-1.0.dtd">
<suite name="Full Test Suite" parallel="methods" thread-count="5">
    <test name="All Tests">
        <classes>
            <class name="com.automation.tests.AuthenticationTest"/>
            <class name="com.automation.tests.UserAPITest"/>
            <class name="com.automation.tests.CRUDWorkflowTest"/>
        </classes>
    </test>
</suite>
```

---

### Step 9: Create Test Data Files

**File:** `src/test/resources/testdata/users.json`

```json
[
  {
    "first_name": "Test",
    "last_name": "User1",
    "email": "test1@example.com"
  },
  {
    "first_name": "Test",
    "last_name": "User2",
    "email": "test2@example.com"
  }
]
```

**File:** `src/test/resources/testdata/login.json`

```json
{
  "email": "eve.holt@reqres.in",
  "password": "cityslicka"
}
```

---

### Step 10: Create .gitignore

**File:** `.gitignore`

```gitignore
# Maven
target/
pom.xml.tag
pom.xml.releaseBackup
pom.xml.versionsBackup
pom.xml.next
release.properties
dependency-reduced-pom.xml
buildNumber.properties
.mvn/timing.properties

# IDE
.idea/
*.iml
.project
.classpath
.settings/
.vscode/

# Test Reports
test-output/
reports/
logs/

# OS
.DS_Store
Thumbs.db
```

---

## ✅ Testing the Framework

### Run Tests with Different Commands

```bash
# Navigate to project directory
cd restassured-maven-framework

# 1. Compile the project
mvn clean compile

# 2. Run all tests (dev profile, smoke suite)
mvn clean test

# 3. Run with QA profile
mvn clean test -Pqa

# 4. Run with staging profile
mvn clean test -Pstaging

# 5. Run smoke tests only
mvn clean test -DsuiteXmlFile=src/test/resources/testng-smoke.xml

# 6. Run regression tests
mvn clean test -Pqa -DsuiteXmlFile=src/test/resources/testng-regression.xml

# 7. Run full suite
mvn clean test -Pstaging -DsuiteXmlFile=src/test/resources/testng-full.xml

# 8. Run specific test class
mvn test -Dtest=UserAPITest

# 9. Run specific test method
mvn test -Dtest=UserAPITest#testGetSingleUser

# 10. Run tests by group
mvn test -Dgroups=smoke

# 11. Run multiple groups
mvn test -Dgroups="smoke,regression"

# 12. Package the project
mvn clean package

# 13. Install to local repository
mvn clean install

# 14. Skip tests during build
mvn clean package -DskipTests

# 15. Run with custom base URL
mvn test -Pqa -DbaseUrl=https://custom-api.com

# 16. Debug mode (verbose output)
mvn clean test -X

# 17. View dependency tree
mvn dependency:tree

# 18. Check for updates
mvn versions:display-dependency-updates
```

---

## 📊 Verify Test Reports

After running tests, check the generated reports:

```bash
# Surefire Reports (XML)
ls target/surefire-reports/
# Files: TEST-*.xml, *.txt

# View test results
cat target/surefire-reports/testng-results.xml

# View test summary
cat target/surefire-reports/index.html
```

**Expected Console Output:**
```
╔════════════════════════════════════════════════╗
║       TEST CONFIGURATION                       ║
╠════════════════════════════════════════════════╣
║ Environment  : QA
║ Base URL     : https://reqres.in
║ API Version  : /api
║ Timeout      : 30 seconds
╚════════════════════════════════════════════════╝

Base URL configured: https://reqres.in/api
Test suite execution started...

[INFO] Running TestSuite
Step 1: User created with ID: 123
Step 2: User retrieved successfully
Step 3: User updated successfully
Step 4: User deleted successfully
Complete CRUD workflow executed!

[INFO] Tests run: 12, Failures: 0, Errors: 0, Skipped: 0
[INFO] BUILD SUCCESS
```

---

## 🚀 CI/CD Integration Examples

### Jenkins Integration

**Jenkinsfile:**
```groovy
pipeline {
    agent any

    tools {
        maven 'Maven 3.8.6'
        jdk 'JDK 11'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/yourcompany/restassured-framework.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Run Smoke Tests') {
            steps {
                sh 'mvn test -Pqa -DsuiteXmlFile=src/test/resources/testng-smoke.xml'
            }
        }

        stage('Run Regression Tests') {
            when {
                branch 'main'
            }
            steps {
                sh 'mvn test -Pqa -DsuiteXmlFile=src/test/resources/testng-regression.xml'
            }
        }
    }

    post {
        always {
            junit 'target/surefire-reports/*.xml'
            archiveArtifacts artifacts: 'target/surefire-reports/*', allowEmptyArchive: true
        }
    }
}
```

### GitHub Actions

**.github/workflows/api-tests.yml:**
```yaml
name: API Automation Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v3

    - name: Set up JDK 11
      uses: actions/setup-java@v3
      with:
        java-version: '11'
        distribution: 'temurin'

    - name: Cache Maven packages
      uses: actions/cache@v3
      with:
        path: ~/.m2
        key: ${{ runner.os }}-m2-${{ hashFiles('**/pom.xml') }}
        restore-keys: ${{ runner.os }}-m2

    - name: Run Smoke Tests
      run: mvn clean test -Pqa

    - name: Publish Test Report
      uses: dorny/test-reporter@v1
      if: always()
      with:
        name: Test Results
        path: target/surefire-reports/*.xml
        reporter: java-junit
```

---

## 📖 Framework Features Summary

**What You've Built:**

✅ **Professional Maven Structure**
- Standard directory layout
- Separation of main and test code
- Resource management

✅ **Comprehensive pom.xml**
- Centralized dependency management
- Plugin configuration (Compiler, Surefire)
- Multi-environment profiles

✅ **Configuration Management**
- Properties file
- ConfigReader utility
- System property override support

✅ **POJO-Based Design**
- User, LoginRequest, LoginResponse models
- Jackson serialization/deserialization
- Clean, maintainable code

✅ **Reusable Components**
- BaseTest class
- JsonUtils utility
- TestDataReader utility

✅ **Complete Test Coverage**
- Authentication tests
- CRUD operations
- End-to-end workflows
- Positive and negative scenarios

✅ **Test Organization**
- TestNG groups (smoke, regression)
- Multiple suite files
- Parallel execution support

✅ **CI/CD Ready**
- Command-line execution
- Profile-based environment switching
- Jenkins and GitHub Actions examples

---

## 💼 How This Relates to Real SDET Work

**In Product Companies:**

1. **Project Structure** - This is the standard structure for Java automation frameworks
2. **Maven Configuration** - Critical for CI/CD pipelines (Jenkins, GitLab CI)
3. **Multi-Environment** - Same tests run against dev, QA, staging, prod
4. **POJOs** - Industry standard for API automation
5. **Base Classes** - Promotes code reuse and maintainability
6. **Command-Line Execution** - Required for automation servers
7. **Reports** - Surefire reports integrate with Jenkins, Allure, etc.

**Interview Questions This Project Answers:**

- "How do you structure your automation framework?" ✅
- "How do you manage dependencies?" ✅
- "How do you run tests in different environments?" ✅
- "How do you integrate with CI/CD?" ✅
- "Show me your pom.xml" ✅
- "How do you handle test data?" ✅
- "How do you organize test suites?" ✅

---

## 🎓 Key Takeaways

**Maven Mastery:**
- Created professional Maven project from scratch
- Configured dependencies, plugins, and profiles
- Executed tests via command line with different configurations

**Framework Design:**
- Implemented industry-standard project structure
- Created reusable base classes and utilities
- Organized tests logically with TestNG

**Professional Skills:**
- Multi-environment support (critical for real projects)
- CI/CD integration readiness
- Code maintainability and scalability

**Interview Readiness:**
- Can explain every part of pom.xml
- Understands Maven build lifecycle
- Knows how to run tests in different environments
- Demonstrates professional framework design

---

**Congratulations!** You've built a production-ready API automation framework with Maven. This is portfolio-worthy and demonstrates enterprise-level skills. 🎉

**Next:** Day-4-Cheat-Sheet.md
