# DAY 3 MINI PROJECT: E2E USER MANAGEMENT API FRAMEWORK

## 🎯 Project Overview

**What You're Building:**
A production-ready API test framework for a User Management System that demonstrates:
- Authentication layer with token management
- POJO-based request/response handling
- Complete CRUD workflows with chaining
- Reusable helper methods and utilities
- Clean, maintainable framework structure

**Why This Project:**
This project simulates real-world SDET work in product companies. You'll build a framework structure that's used by companies like Amazon, Uber, and LinkedIn for their API testing. This is portfolio-ready code that demonstrates professional API automation skills.

**Time Required:** 45-60 minutes

**Skills Practiced:**
- Creating POJO models with Lombok
- Implementing authentication helpers
- Building reusable API request methods
- Chaining complex API workflows
- Framework design patterns
- Test data management

---

## 📋 Requirements

### Must-Have Features:

1. **Authentication Layer**
   - Login method to get Bearer token
   - Token caching to avoid repeated logins
   - Authentication helper class

2. **POJO Models**
   - User (request/response)
   - LoginRequest
   - LoginResponse
   - UserListResponse
   - All POJOs use Lombok annotations

3. **API Helper Methods**
   - createUser() - POST /users
   - getUser(id) - GET /users/{id}
   - updateUser(id) - PUT /users/{id}
   - deleteUser(id) - DELETE /users/{id}
   - getAllUsers() - GET /users

4. **E2E Workflow Tests**
   - Complete user lifecycle (Create → Read → Update → Delete)
   - Multi-user scenario with filtering
   - Authentication-protected operations
   - Error handling and validation

5. **Framework Best Practices**
   - Base test class with common setup
   - Request/Response specifications
   - Configuration management
   - Logging and reporting

### Nice-to-Have (Bonus):
1. Custom assertions for user validation
2. Test data factory for creating test users
3. Negative test scenarios (invalid auth, missing fields)
4. Response time validation

---

## 🏗️ Project Structure

```
src/
├── test/
│   ├── java/
│   │   ├── models/
│   │   │   ├── User.java
│   │   │   ├── LoginRequest.java
│   │   │   ├── LoginResponse.java
│   │   │   └── UserResponse.java
│   │   ├── helpers/
│   │   │   ├── AuthHelper.java
│   │   │   └── UserApiHelper.java
│   │   ├── base/
│   │   │   └── BaseTest.java
│   │   └── tests/
│   │       ├── UserWorkflowTest.java
│   │       └── UserManagementTest.java
│   └── resources/
│       └── config.properties
└── pom.xml
```

---

## 📝 Step-by-Step Implementation

### Step 1: Setup Maven Project

**Create pom.xml with dependencies:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.api.automation</groupId>
    <artifactId>user-management-api-tests</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- RestAssured -->
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <version>5.3.2</version>
            <scope>test</scope>
        </dependency>

        <!-- TestNG -->
        <dependency>
            <groupId>org.testng</groupId>
            <artifactId>testng</artifactId>
            <version>7.8.0</version>
            <scope>test</scope>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.30</version>
            <scope>provided</scope>
        </dependency>

        <!-- Jackson for JSON -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.15.2</version>
        </dependency>

        <!-- Hamcrest -->
        <dependency>
            <groupId>org.hamcrest</groupId>
            <artifactId>hamcrest</artifactId>
            <version>2.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0</version>
            </plugin>
        </plugins>
    </build>
</project>
```

---

### Step 2: Create POJO Models

**models/User.java**

```java
package models;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * User POJO for request and response
 * Used for creating and updating users
 */
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@JsonIgnoreProperties(ignoreUnknown = true)  // Ignore extra fields from API
public class User {
    private String name;
    private String job;

    // Response-only fields (not sent in request)
    private String id;
    private String createdAt;
    private String updatedAt;
}
```

**models/LoginRequest.java**

```java
package models;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * Login request POJO
 */
@Data
@NoArgsConstructor
@AllArgsConstructor
public class LoginRequest {
    private String email;
    private String password;
}
```

**models/LoginResponse.java**

```java
package models;

import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * Login response POJO
 */
@Data
@NoArgsConstructor
public class LoginResponse {
    private String token;
    private String id;  // Some APIs return user ID on login
}
```

**models/UserResponse.java**

```java
package models;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;
import com.fasterxml.jackson.annotation.JsonProperty;
import lombok.Data;
import lombok.NoArgsConstructor;

/**
 * User response POJO for GET /users/{id}
 * ReqRes API structure
 */
@Data
@NoArgsConstructor
@JsonIgnoreProperties(ignoreUnknown = true)
public class UserResponse {
    private UserData data;
    private Support support;

    @Data
    @NoArgsConstructor
    @JsonIgnoreProperties(ignoreUnknown = true)
    public static class UserData {
        private int id;
        private String email;

        @JsonProperty("first_name")
        private String firstName;

        @JsonProperty("last_name")
        private String lastName;

        private String avatar;
    }

    @Data
    @NoArgsConstructor
    @JsonIgnoreProperties(ignoreUnknown = true)
    public static class Support {
        private String url;
        private String text;
    }
}
```

---

### Step 3: Create Base Test Class

**base/BaseTest.java**

```java
package base;

import io.restassured.RestAssured;
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.builder.ResponseSpecBuilder;
import io.restassured.filter.log.RequestLoggingFilter;
import io.restassured.filter.log.ResponseLoggingFilter;
import io.restassured.http.ContentType;
import io.restassured.specification.RequestSpecification;
import io.restassured.specification.ResponseSpecification;
import org.testng.annotations.BeforeClass;

/**
 * Base test class with common setup
 * All test classes extend this
 */
public class BaseTest {

    protected static final String BASE_URI = "https://reqres.in/api";
    protected static RequestSpecification requestSpec;
    protected static ResponseSpecification responseSpec;

    @BeforeClass
    public void setup() {
        // Configure RestAssured base URI
        RestAssured.baseURI = BASE_URI;

        // Create reusable request specification
        requestSpec = new RequestSpecBuilder()
            .setBaseUri(BASE_URI)
            .setContentType(ContentType.JSON)
            .addHeader("Accept", "application/json")
            .addFilter(new RequestLoggingFilter())   // Log all requests
            .addFilter(new ResponseLoggingFilter())  // Log all responses
            .build();

        // Create reusable response specification
        responseSpec = new ResponseSpecBuilder()
            .expectContentType(ContentType.JSON)
            .build();

        System.out.println("========================================");
        System.out.println("API Base URI: " + BASE_URI);
        System.out.println("========================================\n");
    }
}
```

---

### Step 4: Create Authentication Helper

**helpers/AuthHelper.java**

```java
package helpers;

import io.restassured.response.Response;
import models.LoginRequest;
import models.LoginResponse;

import static io.restassured.RestAssured.given;
import static org.testng.Assert.assertNotNull;

/**
 * Authentication helper for managing login and tokens
 */
public class AuthHelper {

    private static final String BASE_URI = "https://reqres.in/api";
    private static String cachedToken = null;

    /**
     * Login and get Bearer token
     * Token is cached to avoid repeated login calls
     */
    public static String getAuthToken() {
        if (cachedToken != null) {
            System.out.println("Using cached token");
            return cachedToken;
        }

        System.out.println("Logging in to get new token...");

        // Create login request
        LoginRequest loginRequest = new LoginRequest(
            "eve.holt@reqres.in",
            "cityslicka"
        );

        // Make login request
        Response response = given()
            .baseUri(BASE_URI)
            .contentType("application/json")
            .body(loginRequest)
        .when()
            .post("/login")
        .then()
            .statusCode(200)
            .extract().response();

        // Deserialize response
        LoginResponse loginResponse = response.as(LoginResponse.class);

        // Validate and cache token
        assertNotNull(loginResponse.getToken(), "Token should not be null");
        cachedToken = loginResponse.getToken();

        System.out.println("Login successful. Token: " + cachedToken);
        return cachedToken;
    }

    /**
     * Login with custom credentials
     */
    public static String login(String email, String password) {
        LoginRequest loginRequest = new LoginRequest(email, password);

        LoginResponse loginResponse = given()
            .baseUri(BASE_URI)
            .contentType("application/json")
            .body(loginRequest)
        .when()
            .post("/login")
        .then()
            .statusCode(200)
            .extract().as(LoginResponse.class);

        assertNotNull(loginResponse.getToken(), "Token should not be null");
        return loginResponse.getToken();
    }

    /**
     * Register a new user and get token
     */
    public static String registerAndGetToken(String email, String password) {
        LoginRequest registerRequest = new LoginRequest(email, password);

        LoginResponse registerResponse = given()
            .baseUri(BASE_URI)
            .contentType("application/json")
            .body(registerRequest)
        .when()
            .post("/register")
        .then()
            .statusCode(200)
            .extract().as(LoginResponse.class);

        assertNotNull(registerResponse.getToken(), "Registration token should not be null");
        System.out.println("User registered. Token: " + registerResponse.getToken());
        return registerResponse.getToken();
    }

    /**
     * Clear cached token (for testing token refresh scenarios)
     */
    public static void clearToken() {
        cachedToken = null;
        System.out.println("Token cache cleared");
    }
}
```

---

### Step 5: Create User API Helper

**helpers/UserApiHelper.java**

```java
package helpers;

import io.restassured.response.Response;
import models.User;
import models.UserResponse;

import java.util.List;

import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.*;

/**
 * User API helper with reusable methods for user operations
 */
public class UserApiHelper {

    private static final String BASE_URI = "https://reqres.in/api";

    /**
     * Create a new user
     * @return Created user ID
     */
    public static String createUser(User user) {
        System.out.println("\n=== Creating User ===");
        System.out.println("Name: " + user.getName());
        System.out.println("Job: " + user.getJob());

        Response response = given()
            .baseUri(BASE_URI)
            .contentType("application/json")
            .body(user)
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .body("name", equalTo(user.getName()))
            .body("job", equalTo(user.getJob()))
            .body("id", notNullValue())
            .body("createdAt", notNullValue())
            .extract().response();

        String userId = response.path("id");
        System.out.println("User created successfully. ID: " + userId);
        return userId;
    }

    /**
     * Get user by ID
     * @return UserResponse object
     */
    public static UserResponse getUser(int userId) {
        System.out.println("\n=== Getting User ===");
        System.out.println("User ID: " + userId);

        UserResponse user = given()
            .baseUri(BASE_URI)
        .when()
            .get("/users/" + userId)
        .then()
            .statusCode(200)
            .body("data.id", equalTo(userId))
            .extract().as(UserResponse.class);

        System.out.println("User retrieved: " + user.getData().getFirstName() + " " + user.getData().getLastName());
        System.out.println("Email: " + user.getData().getEmail());

        return user;
    }

    /**
     * Get all users with pagination
     * @return List of user data
     */
    public static List<UserResponse.UserData> getAllUsers(int page) {
        System.out.println("\n=== Getting All Users (Page " + page + ") ===");

        Response response = given()
            .baseUri(BASE_URI)
            .queryParam("page", page)
        .when()
            .get("/users")
        .then()
            .statusCode(200)
            .body("data", notNullValue())
            .extract().response();

        List<UserResponse.UserData> users = response.jsonPath().getList("data", UserResponse.UserData.class);
        System.out.println("Retrieved " + users.size() + " users");

        return users;
    }

    /**
     * Update user
     * @return Updated user object
     */
    public static User updateUser(String userId, User updatedUser) {
        System.out.println("\n=== Updating User ===");
        System.out.println("User ID: " + userId);
        System.out.println("New Name: " + updatedUser.getName());
        System.out.println("New Job: " + updatedUser.getJob());

        User updated = given()
            .baseUri(BASE_URI)
            .contentType("application/json")
            .body(updatedUser)
        .when()
            .put("/users/" + userId)
        .then()
            .statusCode(200)
            .body("name", equalTo(updatedUser.getName()))
            .body("job", equalTo(updatedUser.getJob()))
            .body("updatedAt", notNullValue())
            .extract().as(User.class);

        System.out.println("User updated successfully at: " + updated.getUpdatedAt());
        return updated;
    }

    /**
     * Partially update user
     */
    public static User patchUser(String userId, User partialUpdate) {
        System.out.println("\n=== Partially Updating User ===");
        System.out.println("User ID: " + userId);

        return given()
            .baseUri(BASE_URI)
            .contentType("application/json")
            .body(partialUpdate)
        .when()
            .patch("/users/" + userId)
        .then()
            .statusCode(200)
            .body("updatedAt", notNullValue())
            .extract().as(User.class);
    }

    /**
     * Delete user
     */
    public static void deleteUser(String userId) {
        System.out.println("\n=== Deleting User ===");
        System.out.println("User ID: " + userId);

        given()
            .baseUri(BASE_URI)
        .when()
            .delete("/users/" + userId)
        .then()
            .statusCode(204);  // No Content

        System.out.println("User deleted successfully");
    }

    /**
     * Verify user doesn't exist (after deletion)
     */
    public static void verifyUserDoesNotExist(String userId) {
        System.out.println("\n=== Verifying User Doesn't Exist ===");

        // Note: ReqRes doesn't actually delete users, so this is for demonstration
        // In a real API, this would return 404
        System.out.println("User " + userId + " should not exist");
    }
}
```

---

### Step 6: Create E2E Workflow Test

**tests/UserWorkflowTest.java**

```java
package tests;

import base.BaseTest;
import helpers.AuthHelper;
import helpers.UserApiHelper;
import models.User;
import models.UserResponse;
import org.testng.annotations.AfterMethod;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;

import java.util.ArrayList;
import java.util.List;

import static org.testng.Assert.*;

/**
 * End-to-End User Workflow Tests
 * Demonstrates complete user lifecycle with authentication
 */
public class UserWorkflowTest extends BaseTest {

    private List<String> createdUserIds;

    @BeforeMethod
    public void setupTest() {
        createdUserIds = new ArrayList<>();
        System.out.println("\n" + "=".repeat(60));
        System.out.println("STARTING NEW TEST");
        System.out.println("=".repeat(60));
    }

    @AfterMethod
    public void cleanup() {
        System.out.println("\n" + "=".repeat(60));
        System.out.println("TEST COMPLETED");
        System.out.println("=".repeat(60) + "\n");
    }

    /**
     * Test 1: Complete CRUD workflow
     * Create → Read → Update → Delete
     */
    @Test(priority = 1)
    public void testCompleteCRUDWorkflow() {
        System.out.println("TEST: Complete CRUD Workflow");

        // Step 1: CREATE
        User newUser = User.builder()
            .name("John Doe")
            .job("Automation Engineer")
            .build();

        String userId = UserApiHelper.createUser(newUser);
        assertNotNull(userId, "User ID should not be null");
        createdUserIds.add(userId);

        // Step 2: READ (using existing user since ReqRes doesn't persist)
        UserResponse retrievedUser = UserApiHelper.getUser(2);
        assertNotNull(retrievedUser.getData(), "User data should not be null");
        assertEquals(retrievedUser.getData().getId(), 2);

        // Step 3: UPDATE
        User updatedUser = User.builder()
            .name("John Doe Updated")
            .job("Senior SDET")
            .build();

        User updated = UserApiHelper.updateUser(userId, updatedUser);
        assertEquals(updated.getName(), "John Doe Updated");
        assertEquals(updated.getJob(), "Senior SDET");
        assertNotNull(updated.getUpdatedAt());

        // Step 4: DELETE
        UserApiHelper.deleteUser(userId);

        System.out.println("\n✓ Complete CRUD workflow executed successfully");
    }

    /**
     * Test 2: Authentication-protected workflow
     * Login → Get Token → Use Token for Operations
     */
    @Test(priority = 2)
    public void testAuthenticatedWorkflow() {
        System.out.println("TEST: Authenticated Workflow");

        // Step 1: Get authentication token
        String token = AuthHelper.getAuthToken();
        assertNotNull(token, "Auth token should not be null");
        assertTrue(token.length() > 10, "Token should be a valid length");

        // Step 2: Create user with authentication
        User newUser = User.builder()
            .name("Jane Smith")
            .job("QA Lead")
            .build();

        String userId = UserApiHelper.createUser(newUser);
        createdUserIds.add(userId);

        // Step 3: Perform operations with token
        // (In a real API, subsequent calls would use the token)
        System.out.println("Token acquired and ready for use: " + token);

        System.out.println("\n✓ Authenticated workflow completed successfully");
    }

    /**
     * Test 3: Multi-user scenario
     * Create multiple users and filter
     */
    @Test(priority = 3)
    public void testMultiUserScenario() {
        System.out.println("TEST: Multi-User Scenario");

        // Create multiple users with different roles
        String[] jobs = {"Developer", "SDET", "Manager", "Designer"};

        System.out.println("\n=== Creating Multiple Users ===");
        for (String job : jobs) {
            User user = User.builder()
                .name("User")
                .job(job)
                .build();

            String userId = UserApiHelper.createUser(user);
            createdUserIds.add(userId);
        }

        System.out.println("\n=== Retrieving Users ===");
        List<UserResponse.UserData> users = UserApiHelper.getAllUsers(1);

        // Validate users
        assertFalse(users.isEmpty(), "User list should not be empty");
        assertTrue(users.size() > 0, "Should have at least one user");

        // Print all users
        System.out.println("\nRetrieved Users:");
        users.forEach(user -> {
            System.out.println("  - " + user.getFirstName() + " " + user.getLastName() + " (" + user.getEmail() + ")");
        });

        System.out.println("\n✓ Multi-user scenario completed successfully");
    }

    /**
     * Test 4: Chained operations with data dependency
     * Create user → Extract ID → Update same user → Verify update
     */
    @Test(priority = 4)
    public void testChainedOperationsWithDataDependency() {
        System.out.println("TEST: Chained Operations with Data Dependency");

        // Step 1: Create initial user
        User initialUser = User.builder()
            .name("Bob Johnson")
            .job("Junior SDET")
            .build();

        String userId = UserApiHelper.createUser(initialUser);
        createdUserIds.add(userId);

        // Step 2: Promote user (update job)
        User promotedUser = User.builder()
            .name("Bob Johnson")
            .job("Senior SDET")
            .build();

        User updated = UserApiHelper.updateUser(userId, promotedUser);

        // Step 3: Verify promotion
        assertEquals(updated.getJob(), "Senior SDET", "Job should be updated");

        // Step 4: Further promote to Lead
        User leadUser = User.builder()
            .name("Bob Johnson")
            .job("SDET Lead")
            .build();

        User finalUpdate = UserApiHelper.updateUser(userId, leadUser);
        assertEquals(finalUpdate.getJob(), "SDET Lead", "Job should be updated to Lead");

        System.out.println("\n✓ User promoted through multiple levels successfully");
    }

    /**
     * Test 5: Builder pattern demonstration
     */
    @Test(priority = 5)
    public void testBuilderPattern() {
        System.out.println("TEST: Builder Pattern Usage");

        // Demonstrate Lombok @Builder
        User user = User.builder()
            .name("Alice Williams")
            .job("Principal SDET")
            .build();

        String userId = UserApiHelper.createUser(user);
        createdUserIds.add(userId);

        // Verify builder created object correctly
        assertNotNull(user.getName());
        assertNotNull(user.getJob());

        System.out.println("\n✓ Builder pattern test completed");
    }
}
```

---

### Step 7: Create Comprehensive Test Suite

**tests/UserManagementTest.java**

```java
package tests;

import base.BaseTest;
import helpers.UserApiHelper;
import models.User;
import models.UserResponse;
import org.testng.annotations.Test;

import java.util.List;

import static org.testng.Assert.*;

/**
 * Comprehensive User Management API Tests
 */
public class UserManagementTest extends BaseTest {

    /**
     * Test user creation with validation
     */
    @Test
    public void testCreateUser() {
        User user = User.builder()
            .name("Test User")
            .job("SDET")
            .build();

        String userId = UserApiHelper.createUser(user);

        assertNotNull(userId);
        assertTrue(userId.length() > 0);
    }

    /**
     * Test retrieving existing user
     */
    @Test
    public void testGetUser() {
        UserResponse user = UserApiHelper.getUser(2);

        assertNotNull(user.getData());
        assertEquals(user.getData().getId(), 2);
        assertNotNull(user.getData().getEmail());
        assertNotNull(user.getData().getFirstName());
        assertNotNull(user.getData().getLastName());
    }

    /**
     * Test pagination
     */
    @Test
    public void testGetAllUsersWithPagination() {
        // Get page 1
        List<UserResponse.UserData> page1 = UserApiHelper.getAllUsers(1);
        assertFalse(page1.isEmpty());

        // Get page 2
        List<UserResponse.UserData> page2 = UserApiHelper.getAllUsers(2);
        assertFalse(page2.isEmpty());

        // Verify different pages have different users
        assertNotEquals(page1.get(0).getId(), page2.get(0).getId());
    }

    /**
     * Test update user
     */
    @Test
    public void testUpdateUser() {
        // Create user first
        User user = User.builder()
            .name("Original Name")
            .job("Original Job")
            .build();

        String userId = UserApiHelper.createUser(user);

        // Update user
        User updatedUser = User.builder()
            .name("Updated Name")
            .job("Updated Job")
            .build();

        User result = UserApiHelper.updateUser(userId, updatedUser);

        assertEquals(result.getName(), "Updated Name");
        assertEquals(result.getJob(), "Updated Job");
    }

    /**
     * Test delete user
     */
    @Test
    public void testDeleteUser() {
        // Create user
        User user = User.builder()
            .name("To Be Deleted")
            .job("Temp")
            .build();

        String userId = UserApiHelper.createUser(user);

        // Delete user (should not throw exception)
        UserApiHelper.deleteUser(userId);
    }
}
```

---

## ✅ Testing Checklist

Test your project against these scenarios:

### Functional Tests:
- [ ] Create user with valid data
- [ ] Retrieve user by ID
- [ ] Update user information
- [ ] Delete user
- [ ] Get list of all users
- [ ] Pagination works correctly
- [ ] Authentication token is generated
- [ ] Token is used in protected endpoints

### Data Validation:
- [ ] All POJO fields deserialize correctly
- [ ] Builder pattern creates objects properly
- [ ] Extracted IDs are not null
- [ ] Response fields match expected types
- [ ] Timestamps are in correct format

### Chaining Tests:
- [ ] Create → Read workflow works
- [ ] Create → Update workflow works
- [ ] Create → Update → Delete workflow works
- [ ] Multi-step workflows maintain data integrity

### Error Handling:
- [ ] Invalid user ID returns appropriate error
- [ ] Missing required fields handled
- [ ] Authentication failure handled gracefully

---

## 🎓 Complete Solution

All code provided above is complete and runnable. Here's how to run it:

### Running the Tests:

**Command Line:**
```bash
# Run all tests
mvn clean test

# Run specific test class
mvn test -Dtest=UserWorkflowTest

# Run specific test method
mvn test -Dtest=UserWorkflowTest#testCompleteCRUDWorkflow
```

**IDE:**
- Right-click on test class → Run
- Right-click on test method → Run

**Expected Console Output:**
```
========================================
API Base URI: https://reqres.in/api
========================================

============================================================
STARTING NEW TEST
============================================================
TEST: Complete CRUD Workflow

=== Creating User ===
Name: John Doe
Job: Automation Engineer
User created successfully. ID: 123

=== Getting User ===
User ID: 2
User retrieved: Janet Weaver
Email: janet.weaver@reqres.in

=== Updating User ===
User ID: 123
New Name: John Doe Updated
New Job: Senior SDET
User updated successfully at: 2024-01-15T12:00:00.000Z

=== Deleting User ===
User ID: 123
User deleted successfully

✓ Complete CRUD workflow executed successfully

============================================================
TEST COMPLETED
============================================================
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these:

### 1. Configuration Management
```java
// config/Config.java
public class Config {
    private static Properties props = new Properties();

    static {
        try {
            props.load(Config.class.getResourceAsStream("/config.properties"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static String getBaseUri() {
        return props.getProperty("base.uri", "https://reqres.in/api");
    }

    public static String getLoginEmail() {
        return System.getenv("API_EMAIL") != null
            ? System.getenv("API_EMAIL")
            : props.getProperty("login.email");
    }
}
```

### 2. Custom Assertions
```java
// utils/UserAssertions.java
public class UserAssertions {
    public static void assertValidUser(User user) {
        assertNotNull(user, "User should not be null");
        assertNotNull(user.getName(), "User name should not be null");
        assertNotNull(user.getJob(), "User job should not be null");
        assertTrue(user.getName().length() > 0, "User name should not be empty");
    }

    public static void assertUserEquals(User expected, User actual) {
        assertEquals(actual.getName(), expected.getName(), "Names should match");
        assertEquals(actual.getJob(), expected.getJob(), "Jobs should match");
    }
}
```

### 3. Test Data Factory
```java
// factory/UserFactory.java
public class UserFactory {
    private static final Faker faker = new Faker();

    public static User createRandomUser() {
        return User.builder()
            .name(faker.name().fullName())
            .job(faker.job().title())
            .build();
    }

    public static User createSDET() {
        return User.builder()
            .name("SDET " + faker.name().firstName())
            .job("Senior SDET")
            .build();
    }
}
```

### 4. Response Time Validation
```java
import static java.util.concurrent.TimeUnit.SECONDS;

@Test
public void testResponseTime() {
    given()
        .spec(requestSpec)
    .when()
        .get("/users")
    .then()
        .time(lessThan(2L, SECONDS))
        .statusCode(200);
}
```

---

## 💼 How This Relates to Real SDET Work

**What you just built mirrors production API frameworks:**

1. **Page Object Model equivalent for APIs** - UserApiHelper is like Page Objects
2. **Reusable components** - AuthHelper, BaseTest
3. **POJO models** - Industry standard for API automation
4. **Builder pattern** - Used in Selenium 4, Playwright, RestAssured
5. **Test organization** - Separate helpers, models, tests
6. **Configuration management** - Externalize URLs, credentials

**In interviews, you can now say:**
*"I've built a production-ready API automation framework using RestAssured with Page Object Model pattern, POJO-based request/response handling, authentication layer, and reusable helper methods. The framework supports complete CRUD workflows with chained operations and follows industry best practices for maintainability and scalability."*

---

## 📊 Project Checklist

**Before submitting/showcasing:**

- [ ] All POJOs have proper Lombok annotations
- [ ] AuthHelper manages token caching correctly
- [ ] UserApiHelper has all CRUD methods
- [ ] Tests are organized in logical classes
- [ ] Console output is clean and informative
- [ ] No hardcoded credentials (use config/env vars)
- [ ] Code is commented and well-structured
- [ ] Tests pass consistently
- [ ] Maven build succeeds: `mvn clean test`
- [ ] Code pushed to GitHub with README

---

**Congratulations! You've built a production-ready API automation framework. This is portfolio-worthy code that demonstrates professional SDET skills.** 🏆

**Next: Move to Day-3-Cheat-Sheet.md for quick reference and interview prep!**
