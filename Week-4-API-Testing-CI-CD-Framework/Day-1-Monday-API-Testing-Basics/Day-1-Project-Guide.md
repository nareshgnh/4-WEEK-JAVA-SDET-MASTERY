# DAY 1 MINI PROJECT: API TEST AUTOMATION SUITE

## 🎯 Project Overview

**What You're Building:**
A comprehensive API test suite for the JSONPlaceholder API that tests all major endpoints with positive, negative, and edge case scenarios.

**Why This Project:**
- Reinforces all Day 1 concepts (GET, POST, PUT, DELETE, validations)
- Creates a portfolio-ready test suite structure
- Practices real-world test organization
- Builds foundation for Week 4's final framework

**Time Required:** 30-45 minutes

**Skills Practiced:**
- RestAssured syntax and assertions
- HTTP methods and status codes
- Test organization with TestNG
- JSON response validation
- Test data management

---

## 📋 Requirements

### Must-Have Features:

1. **User API Tests**
   - Test GET single user
   - Test GET all users
   - Test user not found (negative)
   - Validate nested address object

2. **Post API Tests**
   - Test CREATE new post
   - Test GET post by ID
   - Test UPDATE post
   - Test DELETE post

3. **Data Validation**
   - Validate all HTTP status codes
   - Validate response body fields
   - Validate data types
   - Validate collections

4. **Test Organization**
   - Use TestNG annotations
   - Proper test naming conventions
   - Setup and teardown methods
   - Execution priorities

### Nice-to-Have (Bonus):
1. Extract common configurations to @BeforeClass
2. Add response time validation
3. Create negative test cases (404, 400 scenarios)
4. Add test comments and documentation

---

## 🏗️ Project Structure

**Classes Needed:**
1. `UserAPITest.java` - Tests for /users endpoint
2. `PostAPITest.java` - Tests for /posts endpoint
3. `BaseTest.java` (Bonus) - Common setup/configuration

**Test Methods to Implement:**

**UserAPITest:**
- `testGetAllUsers()` - Validate GET /users returns 10 users
- `testGetSingleUser()` - Validate GET /users/1 returns correct data
- `testGetNonExistentUser()` - Validate GET /users/999 returns 404
- `testUserAddressData()` - Validate nested address object

**PostAPITest:**
- `testCreatePost()` - POST /posts creates new post
- `testGetPost()` - GET /posts/1 returns post data
- `testUpdatePost()` - PUT /posts/1 updates post
- `testPartialUpdatePost()` - PATCH /posts/1 updates specific field
- `testDeletePost()` - DELETE /posts/1 removes post

---

## 📝 Step-by-Step Guide

### Step 1: Set Up Maven Project

**Create a new Maven project with this pom.xml:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.apitest</groupId>
    <artifactId>day1-api-project</artifactId>
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

        <!-- Hamcrest for assertions -->
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
                <configuration>
                    <suiteXmlFiles>
                        <suiteXmlFile>testng.xml</suiteXmlFile>
                    </suiteXmlFiles>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

**Project Structure:**
```
day1-api-project/
├── pom.xml
├── testng.xml
└── src/
    └── test/
        └── java/
            └── com/
                └── apitest/
                    ├── UserAPITest.java
                    └── PostAPITest.java
```

---

### Step 2: Create UserAPITest Class

**What to do:**
Create tests for the /users endpoint with comprehensive validations.

**Code Template:**
```java
package com.apitest;

import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class UserAPITest {

    @BeforeClass
    public void setup() {
        // TODO: Set base URI
        baseURI = "https://jsonplaceholder.typicode.com";
    }

    @Test(priority = 1)
    public void testGetAllUsers() {
        // TODO: GET /users
        // Validate: status 200, size = 10
    }

    @Test(priority = 2)
    public void testGetSingleUser() {
        // TODO: GET /users/1
        // Validate: status 200, id=1, name="Leanne Graham"
    }

    @Test(priority = 3)
    public void testGetNonExistentUser() {
        // TODO: GET /users/999
        // Validate: status 404
    }

    @Test(priority = 4)
    public void testUserAddressData() {
        // TODO: GET /users/1
        // Validate: address.city, address.zipcode
    }
}
```

**Testing:**
Run the tests using:
```bash
mvn test -Dtest=UserAPITest
```

---

### Step 3: Implement UserAPITest Methods

**Implementation Guide:**

#### Test 1: Get All Users
```java
@Test(priority = 1, description = "Verify GET all users returns 10 users")
public void testGetAllUsers() {
    when()
        .get("/users")
    .then()
        .statusCode(200)
        .body("$", hasSize(10))
        .body("[0].id", equalTo(1))
        .body("email", everyItem(notNullValue()))
        .log().ifValidationFails();
}
```

**Key Points:**
- `"$"` refers to root of JSON (the array)
- `hasSize(10)` validates array length
- `everyItem()` checks all elements

#### Test 2: Get Single User
```java
@Test(priority = 2, description = "Verify GET single user returns correct data")
public void testGetSingleUser() {
    when()
        .get("/users/1")
    .then()
        .statusCode(200)
        .body("id", equalTo(1))
        .body("name", equalTo("Leanne Graham"))
        .body("username", equalTo("Bret"))
        .body("email", equalTo("Sincere@april.biz"))
        .body("phone", notNullValue())
        .body("website", notNullValue())
        .time(lessThan(2000L));  // Response time < 2 seconds
}
```

**Key Points:**
- Multiple field validations
- Response time assertion
- Use exact values from API documentation

#### Test 3: Non-Existent User (Negative Test)
```java
@Test(priority = 3, description = "Verify 404 for non-existent user")
public void testGetNonExistentUser() {
    when()
        .get("/users/999")
    .then()
        .statusCode(404);
}
```

**Key Points:**
- Negative testing is crucial
- 404 = Not Found is expected
- Real APIs might return error messages in body

#### Test 4: Nested Address Validation
```java
@Test(priority = 4, description = "Verify user address data is correct")
public void testUserAddressData() {
    when()
        .get("/users/1")
    .then()
        .statusCode(200)
        .body("address.street", equalTo("Kulas Light"))
        .body("address.suite", equalTo("Apt. 556"))
        .body("address.city", equalTo("Gwenborough"))
        .body("address.zipcode", equalTo("92998-3874"))
        .body("address.geo.lat", notNullValue())
        .body("address.geo.lng", notNullValue());
}
```

**Key Points:**
- Dot notation for nested objects: `address.city`
- Can go multiple levels deep: `address.geo.lat`
- Validate structure, not just top-level fields

---

### Step 4: Create PostAPITest Class

**What to do:**
Create comprehensive CRUD tests for the /posts endpoint.

**Full Implementation:**

```java
package com.apitest;

import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class PostAPITest {

    private int createdPostId;

    @BeforeClass
    public void setup() {
        baseURI = "https://jsonplaceholder.typicode.com";
    }

    @Test(priority = 1, description = "Create a new post")
    public void testCreatePost() {
        String newPost = "{ \"title\": \"API Testing with RestAssured\", " +
                        "\"body\": \"Learning API automation\", " +
                        "\"userId\": 1 }";

        createdPostId = given()
            .header("Content-Type", "application/json")
            .body(newPost)
        .when()
            .post("/posts")
        .then()
            .statusCode(201)
            .body("title", equalTo("API Testing with RestAssured"))
            .body("body", equalTo("Learning API automation"))
            .body("userId", equalTo(1))
            .body("id", notNullValue())
            .extract().path("id");  // Extract ID for later use

        System.out.println("Created post with ID: " + createdPostId);
    }

    @Test(priority = 2, description = "Get post by ID")
    public void testGetPost() {
        when()
            .get("/posts/1")
        .then()
            .statusCode(200)
            .body("id", equalTo(1))
            .body("userId", equalTo(1))
            .body("title", notNullValue())
            .body("body", notNullValue());
    }

    @Test(priority = 3, description = "Update post using PUT")
    public void testUpdatePost() {
        String updatedPost = "{ \"id\": 1, " +
                            "\"title\": \"Updated Title\", " +
                            "\"body\": \"Updated Body\", " +
                            "\"userId\": 1 }";

        given()
            .header("Content-Type", "application/json")
            .body(updatedPost)
        .when()
            .put("/posts/1")
        .then()
            .statusCode(200)
            .body("title", equalTo("Updated Title"))
            .body("body", equalTo("Updated Body"))
            .body("id", equalTo(1));
    }

    @Test(priority = 4, description = "Partial update using PATCH")
    public void testPartialUpdatePost() {
        String partialUpdate = "{ \"title\": \"Patched Title\" }";

        given()
            .header("Content-Type", "application/json")
            .body(partialUpdate)
        .when()
            .patch("/posts/1")
        .then()
            .statusCode(200)
            .body("title", equalTo("Patched Title"))
            .body("id", equalTo(1))
            .body("userId", equalTo(1));
    }

    @Test(priority = 5, description = "Delete a post")
    public void testDeletePost() {
        when()
            .delete("/posts/1")
        .then()
            .statusCode(200);
    }

    @Test(priority = 6, description = "Verify deleted post returns 404")
    public void testGetDeletedPost() {
        // Note: JSONPlaceholder doesn't actually delete, but in real API this would be 404
        when()
            .get("/posts/1")
        .then()
            .statusCode(200);  // In real API, this would be 404
    }
}
```

---

### Step 5: Create TestNG XML Configuration

**Create testng.xml:**
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE suite SYSTEM "http://testng.org/testng-1.0.dtd">
<suite name="API Test Suite">
    <test name="User API Tests">
        <classes>
            <class name="com.apitest.UserAPITest"/>
        </classes>
    </test>
    <test name="Post API Tests">
        <classes>
            <class name="com.apitest.PostAPITest"/>
        </classes>
    </test>
</suite>
```

**Run tests:**
```bash
mvn test
```

---

### Step 6: Add Logging and Reporting

**Enhanced Test with Logging:**
```java
@Test
public void testWithLogging() {
    given()
        .log().all()  // Log request
    .when()
        .get("/users/1")
    .then()
        .log().all()  // Log response
        .statusCode(200);
}
```

**Conditional Logging:**
```java
.then()
    .log().ifError()  // Only log if test fails
    .statusCode(200);
```

---

## ✅ Testing Checklist

Test your project against these scenarios:

### User API Tests:
- [ ] GET all users returns 10 users
- [ ] GET single user returns correct data
- [ ] GET non-existent user returns 404
- [ ] Nested address data is validated correctly
- [ ] All users have valid email addresses

### Post API Tests:
- [ ] POST creates new post with status 201
- [ ] Created post has all required fields
- [ ] GET retrieves post successfully
- [ ] PUT updates entire post
- [ ] PATCH updates partial fields
- [ ] DELETE removes post
- [ ] Response times are under 2 seconds

### Code Quality:
- [ ] All tests have descriptive names
- [ ] Tests use priority for execution order
- [ ] Base URI is configured in @BeforeClass
- [ ] Proper static imports used
- [ ] Code is properly formatted and commented

**Expected Output:**
```
[TestNG] Running: testng.xml

User API Tests
  ✓ testGetAllUsers - PASSED
  ✓ testGetSingleUser - PASSED
  ✓ testGetNonExistentUser - PASSED
  ✓ testUserAddressData - PASSED

Post API Tests
  ✓ testCreatePost - PASSED
  ✓ testGetPost - PASSED
  ✓ testUpdatePost - PASSED
  ✓ testPartialUpdatePost - PASSED
  ✓ testDeletePost - PASSED

===============================================
Total tests run: 9, Passes: 9, Failures: 0, Skips: 0
===============================================
```

---

## 🎓 Complete Solution (Only Look After Attempting!)

### UserAPITest.java - Complete
```java
package com.apitest;

import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

/**
 * Test suite for User API endpoints
 * Base URL: https://jsonplaceholder.typicode.com
 * Endpoint: /users
 */
public class UserAPITest {

    @BeforeClass
    public void setup() {
        baseURI = "https://jsonplaceholder.typicode.com";
        System.out.println("=== Starting User API Tests ===");
    }

    @Test(priority = 1, description = "Verify GET all users returns 10 users")
    public void testGetAllUsers() {
        System.out.println("Testing: GET all users");

        when()
            .get("/users")
        .then()
            .statusCode(200)
            .body("$", hasSize(10))
            .body("[0].id", equalTo(1))
            .body("email", everyItem(notNullValue()))
            .body("email", everyItem(containsString("@")))
            .time(lessThan(2000L))
            .log().ifValidationFails();
    }

    @Test(priority = 2, description = "Verify GET single user returns correct data")
    public void testGetSingleUser() {
        System.out.println("Testing: GET single user");

        when()
            .get("/users/1")
        .then()
            .statusCode(200)
            .body("id", equalTo(1))
            .body("name", equalTo("Leanne Graham"))
            .body("username", equalTo("Bret"))
            .body("email", equalTo("Sincere@april.biz"))
            .body("phone", notNullValue())
            .body("website", notNullValue())
            .body("company.name", notNullValue())
            .time(lessThan(2000L));
    }

    @Test(priority = 3, description = "Verify 404 for non-existent user")
    public void testGetNonExistentUser() {
        System.out.println("Testing: GET non-existent user (negative test)");

        when()
            .get("/users/999")
        .then()
            .statusCode(404);
    }

    @Test(priority = 4, description = "Verify user address data is correct")
    public void testUserAddressData() {
        System.out.println("Testing: Nested address validation");

        when()
            .get("/users/1")
        .then()
            .statusCode(200)
            .body("address.street", equalTo("Kulas Light"))
            .body("address.suite", equalTo("Apt. 556"))
            .body("address.city", equalTo("Gwenborough"))
            .body("address.zipcode", equalTo("92998-3874"))
            .body("address.geo.lat", notNullValue())
            .body("address.geo.lng", notNullValue());
    }

    @Test(priority = 5, description = "Verify all users have complete address information")
    public void testAllUsersHaveAddress() {
        System.out.println("Testing: All users have address data");

        when()
            .get("/users")
        .then()
            .statusCode(200)
            .body("address.city", everyItem(notNullValue()))
            .body("address.zipcode", everyItem(notNullValue()))
            .body("address.geo", everyItem(notNullValue()));
    }
}
```

### PostAPITest.java - Complete
```java
package com.apitest;

import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

/**
 * Test suite for Post API endpoints
 * Tests CRUD operations: Create, Read, Update, Delete
 */
public class PostAPITest {

    private int createdPostId;

    @BeforeClass
    public void setup() {
        baseURI = "https://jsonplaceholder.typicode.com";
        System.out.println("=== Starting Post API Tests ===");
    }

    @Test(priority = 1, description = "Create a new post via POST request")
    public void testCreatePost() {
        System.out.println("Testing: CREATE new post");

        String newPost = "{ \"title\": \"API Testing with RestAssured\", " +
                        "\"body\": \"Learning API automation is essential for SDETs\", " +
                        "\"userId\": 1 }";

        createdPostId = given()
            .header("Content-Type", "application/json")
            .body(newPost)
        .when()
            .post("/posts")
        .then()
            .statusCode(201)
            .body("title", equalTo("API Testing with RestAssured"))
            .body("body", equalTo("Learning API automation is essential for SDETs"))
            .body("userId", equalTo(1))
            .body("id", notNullValue())
            .time(lessThan(2000L))
            .extract().path("id");

        System.out.println("✓ Created post with ID: " + createdPostId);
    }

    @Test(priority = 2, description = "Retrieve post by ID")
    public void testGetPost() {
        System.out.println("Testing: GET post by ID");

        when()
            .get("/posts/1")
        .then()
            .statusCode(200)
            .body("id", equalTo(1))
            .body("userId", equalTo(1))
            .body("title", notNullValue())
            .body("body", notNullValue())
            .time(lessThan(2000L));
    }

    @Test(priority = 3, description = "Update entire post using PUT")
    public void testUpdatePost() {
        System.out.println("Testing: UPDATE post with PUT");

        String updatedPost = "{ \"id\": 1, " +
                            "\"title\": \"Updated: Complete Replacement\", " +
                            "\"body\": \"This replaces the entire post content\", " +
                            "\"userId\": 1 }";

        given()
            .header("Content-Type", "application/json")
            .body(updatedPost)
        .when()
            .put("/posts/1")
        .then()
            .statusCode(200)
            .body("title", equalTo("Updated: Complete Replacement"))
            .body("body", equalTo("This replaces the entire post content"))
            .body("id", equalTo(1))
            .time(lessThan(2000L));
    }

    @Test(priority = 4, description = "Partial update using PATCH")
    public void testPartialUpdatePost() {
        System.out.println("Testing: PARTIAL update with PATCH");

        String partialUpdate = "{ \"title\": \"Patched: Only Title Updated\" }";

        given()
            .header("Content-Type", "application/json")
            .body(partialUpdate)
        .when()
            .patch("/posts/1")
        .then()
            .statusCode(200)
            .body("title", equalTo("Patched: Only Title Updated"))
            .body("id", equalTo(1))
            .body("userId", equalTo(1))
            .time(lessThan(2000L));
    }

    @Test(priority = 5, description = "Delete a post")
    public void testDeletePost() {
        System.out.println("Testing: DELETE post");

        when()
            .delete("/posts/1")
        .then()
            .statusCode(200)
            .time(lessThan(2000L));
    }

    @Test(priority = 6, description = "Get all posts and validate collection")
    public void testGetAllPosts() {
        System.out.println("Testing: GET all posts");

        when()
            .get("/posts")
        .then()
            .statusCode(200)
            .body("$", hasSize(100))
            .body("userId", everyItem(notNullValue()))
            .body("title", everyItem(notNullValue()))
            .body("[0].id", equalTo(1))
            .time(lessThan(2000L));
    }
}
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these:

1. **Add Query Parameter Tests**
```java
@Test
public void testFilterPostsByUser() {
    given()
        .queryParam("userId", 1)
    .when()
        .get("/posts")
    .then()
        .statusCode(200)
        .body("userId", everyItem(equalTo(1)));
}
```

2. **Add Comments API Tests**
```java
@Test
public void testGetPostComments() {
    when()
        .get("/posts/1/comments")
    .then()
        .statusCode(200)
        .body("$", hasSize(greaterThan(0)))
        .body("postId", everyItem(equalTo(1)));
}
```

3. **Create Base Test Class**
```java
public class BaseTest {
    @BeforeClass
    public void globalSetup() {
        RestAssured.baseURI = "https://jsonplaceholder.typicode.com";
        RestAssured.enableLoggingOfRequestAndResponseIfValidationFails();
    }
}

// Then extend in your test classes
public class UserAPITest extends BaseTest {
    // Tests here
}
```

4. **Add Schema Validation**
```xml
<!-- Add dependency -->
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>json-schema-validator</artifactId>
    <version>5.3.2</version>
</dependency>
```
```java
import static io.restassured.module.jsv.JsonSchemaValidator.matchesJsonSchemaInClasspath;

@Test
public void testUserSchema() {
    when()
        .get("/users/1")
    .then()
        .body(matchesJsonSchemaInClasspath("user-schema.json"));
}
```

---

## 💼 How This Relates to Real SDET Work

**In actual product companies, you would:**

1. **Test Real APIs** - Internal microservices, not public APIs
2. **Authentication** - Add bearer tokens, OAuth, API keys
3. **Environment Management** - Different base URLs for dev/staging/prod
4. **Data Driven** - Test data from Excel/CSV/database
5. **CI/CD Integration** - Tests run in Jenkins/GitHub Actions
6. **Reporting** - ExtentReports, Allure for stakeholders
7. **Framework Structure** - Page Object Model equivalent for APIs

**This project gives you:**
- ✅ RestAssured fluency
- ✅ TestNG organization
- ✅ Portfolio project to showcase
- ✅ Foundation for Week 4's final framework

---

## 📊 Project Completion Checklist

- [ ] Maven project created with correct dependencies
- [ ] UserAPITest class with all 4+ tests passing
- [ ] PostAPITest class with all 6+ tests passing
- [ ] testng.xml configured correctly
- [ ] All tests run successfully with `mvn test`
- [ ] Code is clean, commented, and follows Java conventions
- [ ] Pushed to GitHub repository

**Congratulations!** You've completed Day 1's project. You now have a working API test suite that demonstrates:
- HTTP methods mastery
- RestAssured proficiency
- TestNG test organization
- Response validation techniques

**Next:** Day-1-Cheat-Sheet.md for quick reference, then Day 2: Advanced RestAssured features!

---

**🎯 Day 1 Project Complete!** This is your first portfolio piece for Week 4. Keep building on this!
