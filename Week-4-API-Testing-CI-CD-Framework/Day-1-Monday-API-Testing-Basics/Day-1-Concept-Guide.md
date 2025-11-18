# DAY 1: API TESTING BASICS

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Understand REST API fundamentals and architecture
- [ ] Master HTTP methods (GET, POST, PUT, DELETE, PATCH)
- [ ] Know HTTP status codes and their meanings
- [ ] Set up RestAssured in a Java Maven project
- [ ] Write your first API automation test

**Time Required:** 2.5-3 hours
**Difficulty:** Easy to Medium
**Prerequisite:** Java basics (Week 1), Maven knowledge

---

## 📚 Core Concepts

### Concept 1: What is an API and Why Test It?

**What is it?**
An API (Application Programming Interface) is a set of rules and protocols that allows different software applications to communicate with each other. REST APIs use HTTP protocol to exchange data, typically in JSON or XML format.

**Why does it matter for automation?**
Modern applications follow microservices architecture where frontend and backend are separate. As an SDET:
- 70% of bugs exist in the API layer (business logic)
- API tests are faster than UI tests (10-100x)
- API tests are more stable (no UI flakiness)
- Product companies expect API testing skills

**Real Example:**
```java
// When you click "Add to Cart" on an e-commerce site:
// UI sends: POST /api/cart/items
// Backend processes the request
// Returns: {"status": "success", "cartCount": 3}
// UI updates the cart icon
```

**Explanation:**
Testing at the API level means you validate the business logic directly without going through the UI. This catches bugs earlier and runs faster.

---

### Concept 2: REST API Fundamentals

**What is REST?**
REST (Representational State Transfer) is an architectural style for building web services. It uses standard HTTP methods and is stateless (each request contains all necessary information).

**Why does it matter for automation?**
Understanding REST principles helps you design better API tests and validate responses correctly.

**Key REST Principles:**
1. **Client-Server Architecture** - Frontend and backend are separate
2. **Stateless** - Server doesn't store client session data
3. **Cacheable** - Responses can be cached for performance
4. **Uniform Interface** - Standard HTTP methods and status codes
5. **Layered System** - Multiple servers can handle requests

**REST vs SOAP:**
| Aspect | REST | SOAP |
|--------|------|------|
| **Protocol** | Uses HTTP | Can use HTTP, SMTP, etc. |
| **Format** | JSON, XML | Only XML |
| **Speed** | Faster | Slower |
| **Industry** | Modern apps (90%) | Legacy systems |

**Common Use Cases in Test Automation:**
1. **User Management** - Create, read, update, delete users
2. **E-commerce** - Product catalog, cart operations, orders
3. **Authentication** - Login, logout, token validation
4. **Data Setup** - Create test data via API before UI tests
5. **Validation** - Verify backend state after UI actions

---

### Concept 3: HTTP Methods (CRUD Operations)

**What are HTTP Methods?**
HTTP methods define the type of operation you want to perform on a resource.

**The Big 5 Methods:**

#### 1. GET - Retrieve Data
```java
// GET request - Fetch user details
// URL: GET https://api.example.com/users/123
// Response: {"id": 123, "name": "John Doe", "email": "john@example.com"}
```
- **Purpose:** Read/fetch data
- **Safe:** Yes (doesn't modify data)
- **Idempotent:** Yes (same result every time)
- **Request Body:** No

#### 2. POST - Create New Resource
```java
// POST request - Create new user
// URL: POST https://api.example.com/users
// Body: {"name": "Jane Doe", "email": "jane@example.com"}
// Response: {"id": 124, "name": "Jane Doe", "email": "jane@example.com"}
```
- **Purpose:** Create new resource
- **Safe:** No (modifies data)
- **Idempotent:** No (multiple calls create multiple resources)
- **Request Body:** Yes

#### 3. PUT - Update Entire Resource
```java
// PUT request - Replace user data completely
// URL: PUT https://api.example.com/users/123
// Body: {"name": "John Smith", "email": "johnsmith@example.com"}
// Response: {"id": 123, "name": "John Smith", "email": "johnsmith@example.com"}
```
- **Purpose:** Replace entire resource
- **Safe:** No
- **Idempotent:** Yes (same result if called multiple times)
- **Request Body:** Yes (full object)

#### 4. PATCH - Partial Update
```java
// PATCH request - Update only email
// URL: PATCH https://api.example.com/users/123
// Body: {"email": "newemail@example.com"}
// Response: {"id": 123, "name": "John Doe", "email": "newemail@example.com"}
```
- **Purpose:** Update specific fields only
- **Safe:** No
- **Idempotent:** Usually yes
- **Request Body:** Yes (partial object)

#### 5. DELETE - Remove Resource
```java
// DELETE request - Remove user
// URL: DELETE https://api.example.com/users/123
// Response: {"message": "User deleted successfully"}
```
- **Purpose:** Delete resource
- **Safe:** No
- **Idempotent:** Yes
- **Request Body:** Usually no

**Mnemonic: CRUD = HTTP Methods**
| CRUD | HTTP Method | Example |
|------|-------------|---------|
| **C**reate | POST | Create new user |
| **R**ead | GET | Get user details |
| **U**pdate | PUT/PATCH | Update user info |
| **D**elete | DELETE | Delete user |

---

### Concept 4: HTTP Status Codes

**What are they?**
3-digit codes that indicate the result of an HTTP request. First digit indicates the category.

**The 5 Categories:**

#### 1xx - Informational (Rare in testing)
- **100 Continue** - Server received request headers, send body

#### 2xx - Success ✅
- **200 OK** - Request successful (GET, PUT, PATCH)
- **201 Created** - Resource created successfully (POST)
- **202 Accepted** - Request accepted for processing
- **204 No Content** - Success but no response body (DELETE)

#### 3xx - Redirection
- **301 Moved Permanently** - Resource moved to new URL
- **302 Found** - Temporary redirect

#### 4xx - Client Errors ❌
- **400 Bad Request** - Invalid request syntax
- **401 Unauthorized** - Authentication required
- **403 Forbidden** - No permission to access
- **404 Not Found** - Resource doesn't exist
- **405 Method Not Allowed** - Wrong HTTP method used
- **409 Conflict** - Request conflicts with current state

#### 5xx - Server Errors 🔥
- **500 Internal Server Error** - Generic server error
- **502 Bad Gateway** - Invalid response from upstream
- **503 Service Unavailable** - Server temporarily down
- **504 Gateway Timeout** - Upstream server timeout

**Testing Status Codes:**
```java
// Positive test - expect 200
given()
    .when().get("/users/123")
    .then().statusCode(200);

// Negative test - expect 404
given()
    .when().get("/users/99999")
    .then().statusCode(404);

// Unauthorized test - expect 401
given()
    .when().get("/admin/users")
    .then().statusCode(401);
```

---

### Concept 5: Request and Response Structure

**Anatomy of HTTP Request:**
```
GET /api/users/123 HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...

{
  "key": "value"
}
```

**Parts:**
1. **Method** - GET, POST, etc.
2. **Path** - /api/users/123
3. **Headers** - Metadata (content type, auth, etc.)
4. **Body** - Data payload (for POST, PUT, PATCH)

**Anatomy of HTTP Response:**
```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 58

{
  "id": 123,
  "name": "John Doe",
  "email": "john@example.com"
}
```

**Parts:**
1. **Status Line** - HTTP/1.1 200 OK
2. **Headers** - Response metadata
3. **Body** - Response data (JSON, XML, etc.)

---

### Concept 6: RestAssured Introduction

**What is RestAssured?**
RestAssured is a Java library that simplifies API testing with a fluent, readable syntax.

**Why RestAssured?**
- Most popular API testing library for Java
- Fluent BDD-style syntax (given-when-then)
- Built-in JSON/XML parsing
- Seamless TestNG/JUnit integration
- Used by 80%+ product companies

**Basic Syntax Pattern:**
```java
given()              // Preconditions (base URI, headers, body, auth)
    .baseUri("https://api.example.com")
    .header("Content-Type", "application/json")
.when()              // Action (HTTP method and endpoint)
    .get("/users/1")
.then()              // Assertions (status code, response body)
    .statusCode(200)
    .body("name", equalTo("Leanne Graham"));
```

**Maven Dependency:**
```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <version>5.3.2</version>
    <scope>test</scope>
</dependency>
```

---

## 🐍 Python vs Java API Testing Comparison

### API Testing in Python (What You Know)
```python
# Python with requests library
import requests

# GET request
response = requests.get("https://api.example.com/users/1")
assert response.status_code == 200
assert response.json()["name"] == "Leanne Graham"

# POST request
payload = {"name": "John", "email": "john@example.com"}
response = requests.post("https://api.example.com/users", json=payload)
assert response.status_code == 201
```

### API Testing in Java with RestAssured (What You're Learning)
```java
// Java with RestAssured
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

// GET request
given()
    .baseUri("https://api.example.com")
.when()
    .get("/users/1")
.then()
    .statusCode(200)
    .body("name", equalTo("Leanne Graham"));

// POST request
given()
    .baseUri("https://api.example.com")
    .contentType("application/json")
    .body("{\"name\":\"John\",\"email\":\"john@example.com\"}")
.when()
    .post("/users")
.then()
    .statusCode(201);
```

**Key Differences:**
| Aspect | Python (requests) | Java (RestAssured) |
|--------|-------------------|-------------------|
| **Syntax** | Simple, minimal | Fluent, verbose |
| **Setup** | pip install requests | Maven dependency |
| **Assertions** | Manual assert statements | Built-in matchers |
| **JSON Parsing** | response.json() | Built-in body() matchers |
| **Readability** | Procedural | BDD-style (given-when-then) |
| **IDE Support** | Basic | Excellent autocomplete |

**Mental Model Shift:**
- Python: Imperative style (do this, then check that)
- RestAssured: Declarative style (given these conditions, when I do this, then expect that)

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Not Importing Static Methods
❌ **Wrong:**
```java
public class APITest {
    @Test
    public void getUserTest() {
        RestAssured.given()  // verbose
            .when().get("/users/1")
            .then().statusCode(200);
    }
}
```

✅ **Correct:**
```java
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class APITest {
    @Test
    public void getUserTest() {
        given()  // clean
            .when().get("/users/1")
            .then().statusCode(200);
    }
}
```

**Why it matters:** Static imports make RestAssured code much more readable and follows industry standards.

---

### Mistake 2: Hardcoding Base URI in Every Test
❌ **Wrong:**
```java
@Test
public void test1() {
    given()
        .baseUri("https://api.example.com")  // repeated
        .when().get("/users/1")
        .then().statusCode(200);
}

@Test
public void test2() {
    given()
        .baseUri("https://api.example.com")  // repeated again
        .when().get("/users/2")
        .then().statusCode(200);
}
```

✅ **Correct:**
```java
public class APITest {
    @BeforeClass
    public void setup() {
        RestAssured.baseURI = "https://api.example.com";  // set once
    }

    @Test
    public void test1() {
        when().get("/users/1")
            .then().statusCode(200);
    }

    @Test
    public void test2() {
        when().get("/users/2")
            .then().statusCode(200);
    }
}
```

**Why it matters:** DRY principle (Don't Repeat Yourself). Easier to change base URI for different environments.

---

### Mistake 3: Not Validating Response Body
❌ **Wrong:**
```java
@Test
public void getUserTest() {
    when().get("/users/1")
        .then().statusCode(200);  // only status code check
}
```

✅ **Correct:**
```java
@Test
public void getUserTest() {
    when().get("/users/1")
        .then()
            .statusCode(200)
            .body("id", equalTo(1))
            .body("name", notNullValue())
            .body("email", containsString("@"));
}
```

**Why it matters:** Status code 200 doesn't mean the data is correct. Always validate response body.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use JSON Path for Complex Response Validation**
Instead of manually parsing JSON, use RestAssured's built-in JSON path expressions:
```java
// Response: {"users": [{"name": "John"}, {"name": "Jane"}]}
.then()
    .body("users.size()", equalTo(2))
    .body("users[0].name", equalTo("John"))
    .body("users.findAll{it.name.startsWith('J')}.size()", equalTo(2));
```

**Tip 2: Log Requests and Responses for Debugging**
```java
given()
    .log().all()  // logs request
.when()
    .get("/users/1")
.then()
    .log().all()  // logs response
    .statusCode(200);
```

**Tip 3: Use API Tests for Test Data Setup**
Before UI tests, use API to create test data (faster and more reliable):
```java
@BeforeMethod
public void createTestUser() {
    userId = given()
        .body("{\"name\":\"Test User\",\"email\":\"test@example.com\"}")
        .post("/users")
        .then().extract().path("id");
}

@Test
public void uiTestWithAPIDataSetup() {
    // Use userId created via API
    driver.get("https://example.com/users/" + userId);
    // Continue with UI test...
}
```

---

## 🔍 Deep Dive: Hamcrest Matchers

**What experienced developers know:**
RestAssured uses Hamcrest matchers for assertions. Understanding these makes your tests more powerful and readable.

**Common Matchers:**
```java
import static org.hamcrest.Matchers.*;

// Equality
.body("name", equalTo("John"))
.body("age", is(30))

// Not equal
.body("status", not("inactive"))

// Null checks
.body("email", notNullValue())
.body("middleName", nullValue())

// String matchers
.body("email", containsString("@"))
.body("name", startsWith("John"))
.body("name", endsWith("Doe"))

// Number comparisons
.body("age", greaterThan(18))
.body("age", lessThanOrEqualTo(65))

// Collection matchers
.body("tags", hasSize(3))
.body("tags", hasItem("java"))
.body("tags", hasItems("java", "testing"))
.body("tags", everyItem(notNullValue()))

// Nested objects
.body("address.city", equalTo("New York"))
.body("address.zipCode", matchesPattern("\\d{5}"))
```

**When to use this:** For robust API validation that reads like English and provides clear failure messages.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Endpoint** | Specific URL path for API resource | `/api/users/123` |
| **Payload** | Data sent in request body | `{"name": "John"}` |
| **Header** | Metadata sent with request/response | `Content-Type: application/json` |
| **JSON** | JavaScript Object Notation data format | `{"key": "value"}` |
| **Base URI** | Root URL for API | `https://api.example.com` |
| **Path Parameter** | Dynamic part of URL path | `/users/{id}` where id=123 |
| **Query Parameter** | Key-value pair after `?` in URL | `/users?page=2&limit=10` |
| **Idempotent** | Same result when called multiple times | GET, PUT, DELETE |
| **Status Code** | 3-digit HTTP response code | `200`, `404`, `500` |
| **RestAssured** | Java library for API testing | `given().when().then()` |

---

## 🎓 Interview Prep

**Common Interview Questions on API Testing:**

**Q1:** What is the difference between PUT and PATCH?
**A:** PUT replaces the entire resource with new data, while PATCH updates only specific fields. For example:
```java
// PUT - must send all fields
PUT /users/1 {"name": "John", "email": "john@example.com", "age": 30}

// PATCH - send only what you want to update
PATCH /users/1 {"email": "newemail@example.com"}
```
PUT is idempotent (same result if called multiple times), PATCH may or may not be.

**Q2:** How do you test a REST API with authentication?
**A:** Depends on auth type:
```java
// Basic Auth
given()
    .auth().basic("username", "password")
    .when().get("/users")
    .then().statusCode(200);

// Bearer Token
given()
    .header("Authorization", "Bearer " + token)
    .when().get("/users")
    .then().statusCode(200);

// OAuth2
given()
    .auth().oauth2(accessToken)
    .when().get("/users")
    .then().statusCode(200);
```

**Q3:** Why is API testing important in microservices architecture?
**A:** In microservices:
- Each service has its own API
- UI depends on multiple backend services
- API bugs affect multiple frontends
- API tests are faster and catch bugs earlier than UI tests
- Testing at API level validates business logic directly

**Q4:** How would you validate a complex JSON response?
**A:** Use JSON Path expressions:
```java
// Response: {"data": {"users": [{"name": "John", "active": true}]}}
.then()
    .body("data.users.size()", equalTo(1))
    .body("data.users[0].name", equalTo("John"))
    .body("data.users[0].active", is(true));
```

**Q5:** What status code do you expect for successful POST request?
**A:** **201 Created** is the correct status code when a new resource is created. Some APIs return **200 OK**, but 201 is the RESTful standard.

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What is the difference between REST and SOAP?**
2. **Which HTTP method would you use to create a new user? Update their email? Delete them?**
3. **What does status code 404 mean? What about 201?**
4. **How is RestAssured's syntax different from Python's requests library?**
5. **Predict the output: `given().when().get("/users/999999").then().statusCode(?)`**
   (assuming user doesn't exist)

**Answers at the end of Practice file.**

---

## 🚀 Next Steps

Now that you understand API testing fundamentals, move to:
1. **Day-1-Practice-Exercises.md** - Hands-on coding with RestAssured
2. **Day-1-Debug-Practice.md** - Fix broken API tests
3. **Day-1-Project-Guide.md** - Build your first API test suite

**Tomorrow Preview:** Day 2 will cover advanced RestAssured features like request/response specifications, serialization, and complex JSON validation.

---

**🎯 Day 1 Complete!** You now understand why API testing is critical and the fundamentals of REST APIs.
