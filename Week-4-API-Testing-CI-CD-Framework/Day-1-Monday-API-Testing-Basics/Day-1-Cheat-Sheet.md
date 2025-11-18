# DAY 1 CHEAT SHEET: API TESTING BASICS

## ⚡ Quick Syntax Reference

### RestAssured Basic Structure
```java
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

given()              // Setup: headers, body, auth, base URI
    .baseUri("https://api.example.com")
    .header("Content-Type", "application/json")
.when()              // Action: HTTP method + endpoint
    .get("/users/1")
.then()              // Assertions: status code, response body
    .statusCode(200)
    .body("name", equalTo("John"));
```

### HTTP Methods
```java
// GET - Read data
.when().get("/users/1")

// POST - Create resource
.when().post("/users")

// PUT - Update entire resource
.when().put("/users/1")

// PATCH - Update partial resource
.when().patch("/users/1")

// DELETE - Remove resource
.when().delete("/users/1")
```

### Common Headers
```java
.header("Content-Type", "application/json")
.header("Authorization", "Bearer " + token)
.header("Accept", "application/json")

// Shorthand for Content-Type
.contentType("application/json")
```

### Request Body
```java
// String body
.body("{\"name\": \"John\", \"email\": \"john@example.com\"}")

// POJO object (covered Day 3)
.body(userObject)
```

### Response Validation
```java
// Status code
.statusCode(200)
.statusCode(isOneOf(200, 201))

// Single field
.body("name", equalTo("John"))
.body("age", is(30))

// Multiple fields
.body("name", equalTo("John"))
.body("email", containsString("@"))
.body("active", is(true))

// Nested objects
.body("address.city", equalTo("New York"))
.body("company.name", notNullValue())

// Arrays
.body("$", hasSize(10))                      // Array size
.body("[0].name", equalTo("John"))           // First element
.body("name", hasItem("John"))               // Contains item
.body("email", everyItem(notNullValue()))    // All items validation
```

---

## 🔑 Key Concepts at a Glance

| Concept | Explanation | Example |
|---------|-------------|---------|
| **REST** | Architectural style for web services | GET /users/1 |
| **Endpoint** | URL path for resource | `/api/users/123` |
| **JSON** | Data format (key-value pairs) | `{"name": "John"}` |
| **Status Code** | HTTP response status | `200 OK`, `404 Not Found` |
| **Header** | Request/response metadata | `Content-Type: application/json` |
| **Body** | Request/response data payload | POST body with user data |

### HTTP Methods = CRUD

| CRUD | HTTP Method | Status | Use Case |
|------|-------------|--------|----------|
| **Create** | POST | 201 | Create new user |
| **Read** | GET | 200 | Get user details |
| **Update** | PUT/PATCH | 200 | Update user info |
| **Delete** | DELETE | 200/204 | Remove user |

### HTTP Status Codes

| Code | Meaning | When to expect |
|------|---------|----------------|
| **200** | OK | Successful GET, PUT, PATCH |
| **201** | Created | Successful POST |
| **204** | No Content | Successful DELETE (no body) |
| **400** | Bad Request | Invalid data sent |
| **401** | Unauthorized | Missing/invalid auth |
| **403** | Forbidden | No permission |
| **404** | Not Found | Resource doesn't exist |
| **500** | Server Error | Backend problem |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Missing imports** | `given()` not found | `import static io.restassured.RestAssured.*;` |
| **Wrong method** | `.get()` to create | `.post()` for creating |
| **Missing header** | POST without Content-Type | `.header("Content-Type", "application/json")` |
| **Wrong status** | Expect 200 for POST | Expect 201 for POST |
| **Type mismatch** | `.body("id", equalTo("1"))` | `.body("id", equalTo(1))` (no quotes for numbers) |
| **Wrong path** | `.body("user.name", ...)` on array | `.body("[0].name", ...)` for arrays |

---

## 🎯 Essential Hamcrest Matchers

```java
import static org.hamcrest.Matchers.*;

// Equality
equalTo("John")           // Exact match
is(30)                    // Same as equalTo
not("inactive")           // Not equal

// Null checks
notNullValue()            // Field exists and not null
nullValue()               // Field is null

// String matchers
containsString("@")       // Contains substring
startsWith("John")        // Starts with
endsWith("Doe")           // Ends with

// Number comparisons
greaterThan(18)           // > 18
lessThan(65)              // < 65
greaterThanOrEqualTo(18)  // >= 18
lessThanOrEqualTo(65)     // <= 65

// Collection matchers
hasSize(10)               // Exactly 10 items
hasItem("java")           // Contains item
hasItems("java", "api")   // Contains multiple items
everyItem(notNullValue()) // All items match
```

---

## 🚀 Advanced Shortcuts

### Set Base URI Once
```java
@BeforeClass
public void setup() {
    RestAssured.baseURI = "https://api.example.com";
}

// Now all tests use this base URI
@Test
public void test() {
    when().get("/users").then().statusCode(200);
}
```

### Logging
```java
// Log everything
.log().all()

// Log only if error
.log().ifError()

// Log only if validation fails
.log().ifValidationFails()

// Log request
given().log().all()

// Log response
.then().log().all()
```

### Extract Values
```java
// Extract single value
int userId = get("/users/1").then().extract().path("id");

// Extract entire response
Response response = get("/users/1").then().extract().response();
String name = response.path("name");
int statusCode = response.statusCode();
```

### Response Time Validation
```java
import static java.util.concurrent.TimeUnit.SECONDS;

.then()
    .time(lessThan(2000L))                    // < 2000ms
    .time(lessThan(2L, SECONDS));             // < 2 seconds
```

### Path Parameters
```java
// Instead of string concatenation
.get("/users/" + userId)

// Use path parameter
.get("/users/{id}", userId)
```

### Query Parameters
```java
// Single query param: /users?page=2
.queryParam("page", 2)
.get("/users")

// Multiple: /users?page=2&limit=10
.queryParams("page", 2, "limit", 10)
.get("/users")
```

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ REST API fundamentals and architecture
- ✅ HTTP methods: GET, POST, PUT, PATCH, DELETE
- ✅ HTTP status codes and their meanings
- ✅ RestAssured syntax: given-when-then pattern
- ✅ JSON path expressions for nested validation
- ✅ Hamcrest matchers for flexible assertions
- ✅ Request/response structure and headers

**Critical for Automation:**
- 🎯 **API testing is faster and more stable than UI testing** - Use it for backend validation
- 🎯 **Always validate both status code AND response body** - Don't assume 200 means correct data
- 🎯 **RestAssured uses BDD style** - Makes tests readable like plain English

---

## 📋 Maven Dependency Quick Reference

```xml
<!-- pom.xml essentials -->
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

    <!-- Hamcrest (comes with RestAssured but explicit is better) -->
    <dependency>
        <groupId>org.hamcrest</groupId>
        <artifactId>hamcrest</artifactId>
        <version>2.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

---

## 🎤 Interview Phrases

**When asked about API testing, say:**

*"API testing validates the business logic layer of applications by making HTTP requests and verifying responses. It's essential in microservices architecture because it's faster, more stable, and catches bugs earlier than UI testing. I use RestAssured with Java, which provides a fluent BDD-style syntax for writing maintainable API tests."*

**When asked about REST:**

*"REST is an architectural style that uses HTTP methods for CRUD operations. RESTful APIs are stateless, use standard status codes, and typically exchange data in JSON format. This makes them lightweight, scalable, and easy to test."*

**When asked about HTTP methods:**

*"The main HTTP methods map to CRUD operations: GET for reading data (status 200), POST for creating resources (status 201), PUT for complete updates, PATCH for partial updates, and DELETE for removing resources. Understanding which method to use is crucial for proper API testing."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **API testing is the backbone of modern test automation. Master RestAssured's given-when-then pattern, and you can test any REST API in the world.**

---

## 🔮 Tomorrow's Preview

**Day 2 Topic:** RestAssured Deep Dive

**What you'll learn:**
- Request and Response Specifications (reusable configs)
- Headers, query params, path params in depth
- JSON serialization and deserialization
- Advanced response extraction and manipulation

**What to review tonight:**
- Practice JSON path expressions
- Get comfortable with Hamcrest matchers
- Review HTTP methods and status codes

**What to think about:**
- How would you handle authentication in API tests?
- How can you make API tests data-driven?
- What if you need to chain multiple API calls?

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- ✅ Learned REST API fundamentals
- ✅ Mastered HTTP methods and status codes
- ✅ Set up RestAssured in Maven
- ✅ Wrote first API automation tests
- ✅ Built complete API test suite
- ✅ 2.5-3 hours of hands-on practice

**Cumulative Progress:**
- Days completed: 1/7 (Week 4)
- Topics mastered: API Testing Basics
- Tests written: 10+ API tests
- Portfolio projects: 1 (API Test Suite)

**Week 4 Progress: 14% Complete** 🎯

**Momentum Statement:**

*You just learned in one day what most QA engineers take weeks to master. API testing is the highest-impact skill for SDET roles, and you're already writing professional-grade tests. Tomorrow you'll learn advanced RestAssured features that will make you more efficient than 90% of automation engineers.*

**This is Day 1 of becoming a full-stack SDET. UI + API + CI/CD = 25 LPA offers. Keep going!** 💪🚀

---

## 📚 Quick Reference Card (Print This!)

```
╔══════════════════════════════════════════════════════════╗
║              REST ASSURED CHEAT SHEET                    ║
╠══════════════════════════════════════════════════════════╣
║ IMPORTS:                                                 ║
║   import static io.restassured.RestAssured.*;            ║
║   import static org.hamcrest.Matchers.*;                 ║
║                                                          ║
║ STRUCTURE:                                               ║
║   given().header/body/auth                               ║
║   .when().get/post/put/patch/delete                      ║
║   .then().statusCode/body                                ║
║                                                          ║
║ HTTP METHODS:                                            ║
║   GET (200)  → Read      POST (201)   → Create          ║
║   PUT (200)  → Update    PATCH (200)  → Partial Update  ║
║   DELETE (200/204) → Delete                              ║
║                                                          ║
║ VALIDATIONS:                                             ║
║   .statusCode(200)                                       ║
║   .body("name", equalTo("John"))                         ║
║   .body("email", containsString("@"))                    ║
║   .body("$", hasSize(10))                                ║
║   .body("[0].id", equalTo(1))                            ║
║                                                          ║
║ MATCHERS:                                                ║
║   equalTo, is, not, notNullValue                         ║
║   containsString, startsWith, endsWith                   ║
║   greaterThan, lessThan, hasSize, hasItem                ║
║                                                          ║
║ LOGGING:                                                 ║
║   .log().all()  .log().ifError()  .log().ifValidation() ║
╚══════════════════════════════════════════════════════════╝
```

---

**Ready for Day 2?** Review this cheat sheet, practice the exercises one more time, and get ready to level up with advanced RestAssured features tomorrow! 🔥
