# DAY 2: RESTASSURED DEEP DIVE

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master Request and Response Specifications for reusable test configurations
- [ ] Understand and work with HTTP headers (Content-Type, Authorization, custom headers)
- [ ] Differentiate between query parameters and path parameters
- [ ] Use advanced JSON Path expressions for complex response validation
- [ ] Extract and reuse data from API responses
- [ ] Build complete CRUD test suites

**Time Required:** 2.5-3 hours
**Difficulty:** Medium
**Prerequisite:** Day 1 - API Testing Basics, RestAssured fundamentals

---

## 📚 Core Concepts

### Concept 1: Request Specification - DRY Your Tests

**What is it?**
RequestSpecification is a reusable configuration object that holds common request settings (base URI, headers, authentication, content type) that you can apply to multiple tests. Think of it as a "request template."

**Why does it matter for automation?**
In real projects, you'll write hundreds of API tests. Without specifications:
- You repeat the same headers in every test (DRY violation)
- Changing an auth token requires updating 100+ tests
- Tests are verbose and hard to maintain

With RequestSpecification:
- Define common config once, use everywhere
- Update auth/headers in one place
- Tests become cleaner and more readable

**Syntax:**
```java
// Create reusable specification
RequestSpecification requestSpec = new RequestSpecBuilder()
    .setBaseUri("https://jsonplaceholder.typicode.com")
    .setContentType(ContentType.JSON)
    .addHeader("Authorization", "Bearer token123")
    .build();

// Use in tests
given()
    .spec(requestSpec)  // Apply the specification
.when()
    .get("/posts/1")
.then()
    .statusCode(200);
```

**Explanation:**
- `RequestSpecBuilder()` - Creates a builder to construct the spec
- `.setBaseUri()` - Sets the base URL for all requests
- `.setContentType()` - Sets Content-Type header automatically
- `.addHeader()` - Adds custom headers
- `.build()` - Finalizes and returns the RequestSpecification
- `.spec(requestSpec)` - Applies all settings to the request

**Real Example - Product Company Pattern:**
```java
public class BaseTest {
    protected static RequestSpecification requestSpec;

    @BeforeClass
    public static void setup() {
        // Create once, use in all tests
        requestSpec = new RequestSpecBuilder()
            .setBaseUri(ConfigReader.getApiUrl())  // From properties file
            .setContentType(ContentType.JSON)
            .addHeader("X-API-Key", ConfigReader.getApiKey())
            .addHeader("User-Agent", "SDET-Automation/1.0")
            .setRelaxedHTTPSValidation()  // For test environments
            .build();
    }
}

public class UserAPITest extends BaseTest {
    @Test
    public void getUserTest() {
        given()
            .spec(requestSpec)  // All config applied automatically
        .when()
            .get("/users/1")
        .then()
            .statusCode(200)
            .body("id", equalTo(1));
    }
}
```

**When to use RequestSpecification:**
- Multiple tests share the same base URI
- All requests need authentication headers
- API requires specific headers (API keys, custom headers)
- You want to switch between environments (dev, staging, prod)

---

### Concept 2: Response Specification - Reusable Assertions

**What is it?**
ResponseSpecification is a reusable set of common validations that you expect from all or most API responses (status code, response time, headers, common fields).

**Why does it matter for automation?**
Every API endpoint should return certain standard things:
- Content-Type header
- Response time under threshold
- Common fields like "status", "timestamp"

Instead of repeating these validations in every test, define them once.

**Syntax:**
```java
// Create reusable response specification
ResponseSpecification responseSpec = new ResponseSpecBuilder()
    .expectStatusCode(200)
    .expectContentType(ContentType.JSON)
    .expectResponseTime(lessThan(2000L))  // Max 2 seconds
    .expectHeader("Server", notNullValue())
    .build();

// Use in tests
given()
    .baseUri("https://jsonplaceholder.typicode.com")
.when()
    .get("/posts/1")
.then()
    .spec(responseSpec)  // Apply common validations
    .body("id", equalTo(1));  // Add test-specific validations
```

**Explanation:**
- `ResponseSpecBuilder()` - Creates builder for response spec
- `.expectStatusCode()` - Common status code to check
- `.expectContentType()` - Expected content type
- `.expectResponseTime()` - Performance threshold
- `.expectHeader()` - Validate response headers
- `.spec(responseSpec)` - Applies all common validations

**Real Example - API Health Checks:**
```java
public class APISpecifications {

    // Spec for successful GET requests
    public static ResponseSpecification successResponseSpec =
        new ResponseSpecBuilder()
            .expectStatusCode(200)
            .expectContentType(ContentType.JSON)
            .expectResponseTime(lessThan(3000L))
            .expectHeader("Content-Type", containsString("application/json"))
            .build();

    // Spec for successful POST requests
    public static ResponseSpecification createdResponseSpec =
        new ResponseSpecBuilder()
            .expectStatusCode(201)
            .expectContentType(ContentType.JSON)
            .expectHeader("Location", notNullValue())  // Created resource URL
            .build();

    // Spec for DELETE requests
    public static ResponseSpecification deleteResponseSpec =
        new ResponseSpecBuilder()
            .expectStatusCode(isOneOf(200, 204))  // Either OK or No Content
            .expectResponseTime(lessThan(1000L))
            .build();
}

// Use in tests
@Test
public void createUserTest() {
    given()
        .spec(requestSpec)
        .body(newUser)
    .when()
        .post("/users")
    .then()
        .spec(APISpecifications.createdResponseSpec)  // Reusable validations
        .body("name", equalTo(newUser.getName()));    // Test-specific
}
```

**Pro Pattern - Combine Request and Response Specs:**
```java
given()
    .spec(requestSpec)  // Request configuration
.when()
    .get("/users/1")
.then()
    .spec(responseSpec)  // Response validations
    .body("name", equalTo("Leanne Graham"));
```

---

### Concept 3: HTTP Headers Deep Dive

**What are HTTP Headers?**
Headers are key-value pairs sent with HTTP requests and responses that provide metadata about the request/response (data format, authentication, caching, etc.).

**Why does it matter for automation?**
Most real-world APIs require specific headers:
- **Content-Type** - Tells server what format you're sending
- **Authorization** - Proves who you are
- **Accept** - Tells server what format you want back
- **Custom headers** - API-specific requirements (X-API-Key, X-Request-ID)

#### Common Headers Explained:

**1. Content-Type (Request Header)**
```java
// Tells server "I'm sending JSON data"
.header("Content-Type", "application/json")

// Shorthand
.contentType(ContentType.JSON)

// Other common types:
.contentType(ContentType.XML)           // application/xml
.contentType(ContentType.TEXT)          // text/plain
.contentType(ContentType.HTML)          // text/html
.contentType("application/x-www-form-urlencoded")  // Form data
```

**When to use:** Always when sending a request body (POST, PUT, PATCH)

**2. Accept (Request Header)**
```java
// "I want JSON response back"
.header("Accept", "application/json")

// "I want XML response back"
.header("Accept", "application/xml")

// Accept multiple formats (preference order)
.header("Accept", "application/json, application/xml;q=0.9")
```

**When to use:** When API supports multiple response formats

**3. Authorization (Request Header)**
```java
// Bearer Token (OAuth2, JWT)
.header("Authorization", "Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...")

// Basic Auth (username:password encoded in Base64)
.header("Authorization", "Basic dXNlcm5hbWU6cGFzc3dvcmQ=")

// API Key
.header("X-API-Key", "abc123xyz789")

// RestAssured auth helpers
.auth().basic("username", "password")
.auth().oauth2("access_token")
```

**When to use:** Protected endpoints that require authentication

**4. Custom Headers**
```java
// API versioning
.header("X-API-Version", "v2")

// Request tracking
.header("X-Request-ID", UUID.randomUUID().toString())

// Client information
.header("User-Agent", "SDET-Automation/1.0")

// Rate limiting bypass
.header("X-RateLimit-Bypass", "testing-token")

// Correlation ID for distributed tracing
.header("X-Correlation-ID", "test-run-" + System.currentTimeMillis())
```

**Setting Multiple Headers:**
```java
// Method 1: Chain multiple .header() calls
given()
    .header("Content-Type", "application/json")
    .header("Authorization", "Bearer token123")
    .header("X-API-Version", "v2")
.when()
    .get("/users");

// Method 2: Use Map
Map<String, String> headers = new HashMap<>();
headers.put("Content-Type", "application/json");
headers.put("Authorization", "Bearer token123");
headers.put("X-API-Version", "v2");

given()
    .headers(headers)  // Set all at once
.when()
    .get("/users");

// Method 3: Headers object
Headers headers = new Headers(
    new Header("Content-Type", "application/json"),
    new Header("Authorization", "Bearer token123"),
    new Header("X-API-Version", "v2")
);

given()
    .headers(headers)
.when()
    .get("/users");
```

**Validating Response Headers:**
```java
.then()
    .header("Content-Type", "application/json; charset=utf-8")
    .header("Server", notNullValue())
    .header("X-RateLimit-Remaining", greaterThan("0"))
    .header("Cache-Control", containsString("no-cache"))
    .headers("Content-Type", "application/json",  // Multiple headers
             "Server", notNullValue());
```

---

### Concept 4: Path Parameters vs Query Parameters

**What's the difference?**
Both add dynamic data to URLs, but they serve different purposes.

#### Path Parameters (Part of URL Path)

**What:** Variables embedded in the URL path that identify specific resources.

**Format:** `/users/{id}/posts/{postId}`

**Example:**
```java
// URL: GET /users/123/posts/456
given()
    .pathParam("userId", 123)
    .pathParam("postId", 456)
.when()
    .get("/users/{userId}/posts/{postId}")
.then()
    .statusCode(200);

// Multiple path params
given()
    .pathParams("userId", 123, "postId", 456)
.when()
    .get("/users/{userId}/posts/{postId}");
```

**When to use:**
- Identifying specific resources (user ID, product ID)
- Hierarchical data (user → posts → comments)
- RESTful resource paths

**Real-world examples:**
- `/users/{userId}` - Get specific user
- `/products/{productId}/reviews` - Get reviews for a product
- `/orders/{orderId}/items/{itemId}` - Get specific item in an order

#### Query Parameters (After `?` in URL)

**What:** Optional filters, sorting, pagination added after `?` in URL.

**Format:** `/users?page=2&limit=10&sort=name`

**Example:**
```java
// URL: GET /users?page=2&limit=10&sort=name
given()
    .queryParam("page", 2)
    .queryParam("limit", 10)
    .queryParam("sort", "name")
.when()
    .get("/users")
.then()
    .statusCode(200);

// Multiple query params
given()
    .queryParams("page", 2, "limit", 10, "sort", "name")
.when()
    .get("/users");

// Using Map
Map<String, Object> params = new HashMap<>();
params.put("page", 2);
params.put("limit", 10);
params.put("sort", "name");

given()
    .queryParams(params)
.when()
    .get("/users");
```

**When to use:**
- Filtering results (`?status=active`)
- Pagination (`?page=2&limit=20`)
- Sorting (`?sort=name&order=asc`)
- Searching (`?search=john`)
- Optional parameters

**Real-world examples:**
- `/products?category=electronics&minPrice=100&maxPrice=500`
- `/users?search=john&status=active&role=admin`
- `/orders?startDate=2024-01-01&endDate=2024-12-31&status=completed`

#### Path vs Query: Decision Guide

| Aspect | Path Parameters | Query Parameters |
|--------|----------------|------------------|
| **Purpose** | Identify specific resource | Filter/sort/paginate collection |
| **Required?** | Usually required | Usually optional |
| **Example** | `/users/{id}` | `/users?status=active` |
| **REST Style** | Resource identification | Query/search parameters |
| **URL** | Part of path | After `?` |
| **When** | "Get THIS user" | "Get users WHERE..." |

**Combined Example:**
```java
// Get comments for specific post, paginated, sorted
// Path param: postId (which post)
// Query params: page, limit, sort (how to display)
given()
    .pathParam("postId", 1)
    .queryParam("page", 1)
    .queryParam("limit", 5)
    .queryParam("sort", "createdAt")
.when()
    .get("/posts/{postId}/comments")  // Result: /posts/1/comments?page=1&limit=5&sort=createdAt
.then()
    .statusCode(200)
    .body("$", hasSize(5));
```

---

### Concept 5: Advanced JSON Path Expressions

**What is JSON Path?**
JSON Path is a query language for JSON, similar to XPath for XML. It allows you to navigate and extract data from complex JSON structures.

**Why does it matter for automation?**
Real API responses are complex and nested. You need to validate specific values deep within the JSON structure.

#### JSON Path Syntax Guide:

**Basic JSON Response:**
```json
{
  "id": 1,
  "name": "Leanne Graham",
  "email": "leanne@example.com",
  "address": {
    "street": "Kulas Light",
    "city": "Gwenborough",
    "zipcode": "92998-3874"
  },
  "company": {
    "name": "Romaguera-Crona",
    "catchPhrase": "Multi-layered client-server neural-net"
  }
}
```

**1. Root Level Fields:**
```java
.body("id", equalTo(1))
.body("name", equalTo("Leanne Graham"))
.body("email", containsString("@"))
```

**2. Nested Objects (Dot Notation):**
```java
.body("address.city", equalTo("Gwenborough"))
.body("address.zipcode", startsWith("92998"))
.body("company.name", containsString("Romaguera"))
```

**3. Arrays:**
```json
{
  "users": [
    {"id": 1, "name": "John", "active": true},
    {"id": 2, "name": "Jane", "active": false},
    {"id": 3, "name": "Bob", "active": true}
  ]
}
```

```java
// Array size
.body("users.size()", equalTo(3))

// Access by index
.body("users[0].name", equalTo("John"))
.body("users[1].id", equalTo(2))

// Get all names
.body("users.name", hasItems("John", "Jane", "Bob"))

// Get all IDs
.body("users.id", hasItems(1, 2, 3))

// Check every item
.body("users.id", everyItem(notNullValue()))
```

**4. Filtering with findAll:**
```java
// Get all active users
.body("users.findAll{it.active == true}.size()", equalTo(2))

// Get names of active users
.body("users.findAll{it.active == true}.name", hasItems("John", "Bob"))

// Find users with id > 1
.body("users.findAll{it.id > 1}.size()", equalTo(2))

// Find user with specific name
.body("users.find{it.name == 'Jane'}.id", equalTo(2))
```

**5. Complex Nested Arrays:**
```json
{
  "company": "TechCorp",
  "departments": [
    {
      "name": "Engineering",
      "employees": [
        {"id": 1, "name": "Alice", "salary": 100000},
        {"id": 2, "name": "Bob", "salary": 90000}
      ]
    },
    {
      "name": "Sales",
      "employees": [
        {"id": 3, "name": "Charlie", "salary": 80000}
      ]
    }
  ]
}
```

```java
// Access nested arrays
.body("departments[0].name", equalTo("Engineering"))
.body("departments[0].employees[0].name", equalTo("Alice"))

// Get all employee names across all departments
.body("departments.employees.flatten().name", hasItems("Alice", "Bob", "Charlie"))

// Count total employees
.body("departments.employees.flatten().size()", equalTo(3))

// Find employees with salary > 85000
.body("departments.employees.flatten().findAll{it.salary > 85000}.size()", equalTo(2))
```

**6. Root Array (When response is array itself):**
```json
[
  {"id": 1, "title": "Post 1"},
  {"id": 2, "title": "Post 2"},
  {"id": 3, "title": "Post 3"}
]
```

```java
// Array size
.body("$", hasSize(3))

// Or
.body("size()", equalTo(3))

// Access elements
.body("[0].title", equalTo("Post 1"))
.body("[1].id", equalTo(2))

// Get all titles
.body("title", hasItems("Post 1", "Post 2", "Post 3"))

// Filter
.body("findAll{it.id > 1}.size()", equalTo(2))
```

**7. JSON Path Functions:**
```java
// Size
.body("users.size()", equalTo(10))

// Min/Max
.body("products.price.min()", equalTo(10.99))
.body("products.price.max()", equalTo(99.99))

// Sum
.body("items.quantity.sum()", equalTo(100))

// Unique values
.body("users.role.unique()", hasItems("admin", "user", "guest"))
```

---

### Concept 6: Response Extraction and Reuse

**What is it?**
Extracting values from API responses to use in subsequent tests. Essential for chaining API calls and building realistic test scenarios.

**Why does it matter for automation?**
Real-world scenarios involve multiple API calls:
1. Create user → Get userId from response
2. Use userId to create order
3. Use orderId to add items
4. Verify order details

**Basic Extraction:**
```java
// Extract single value
int userId = given()
    .contentType(ContentType.JSON)
    .body("{\"name\":\"John\",\"email\":\"john@example.com\"}")
.when()
    .post("/users")
.then()
    .statusCode(201)
    .extract().path("id");  // Extract the 'id' field

System.out.println("Created user ID: " + userId);  // Use in subsequent tests
```

**Extract Entire Response:**
```java
// Extract as Response object
Response response = given()
    .when().get("/users/1")
    .then().statusCode(200)
    .extract().response();

// Now extract multiple values
String name = response.path("name");
String email = response.path("email");
int id = response.path("id");
String city = response.path("address.city");
```

**Extract as String:**
```java
// Get raw response body as string
String responseBody = get("/users/1")
    .then().extract().asString();

System.out.println("Raw JSON: " + responseBody);
```

**Extract Headers:**
```java
Response response = get("/users/1").then().extract().response();

String contentType = response.header("Content-Type");
String server = response.header("Server");
int statusCode = response.statusCode();
long responseTime = response.time();  // in milliseconds
```

**Extract and Deserialize to POJO (Advanced):**
```java
// If you have a User class
User user = get("/users/1")
    .then()
    .statusCode(200)
    .extract().as(User.class);

// Now use the object
assertEquals("Leanne Graham", user.getName());
```

**Real-World Scenario - Chained API Calls:**
```java
@Test
public void createUserAndVerifyPostsTest() {
    // Step 1: Create user
    int userId = given()
        .contentType(ContentType.JSON)
        .body("{\"name\":\"Test User\",\"email\":\"test@example.com\"}")
    .when()
        .post("/users")
    .then()
        .statusCode(201)
        .extract().path("id");

    // Step 2: Create post for this user
    int postId = given()
        .contentType(ContentType.JSON)
        .body("{\"userId\":" + userId + ",\"title\":\"My Post\",\"body\":\"Post content\"}")
    .when()
        .post("/posts")
    .then()
        .statusCode(201)
        .extract().path("id");

    // Step 3: Verify the post belongs to correct user
    given()
        .pathParam("postId", postId)
    .when()
        .get("/posts/{postId}")
    .then()
        .statusCode(200)
        .body("userId", equalTo(userId))
        .body("title", equalTo("My Post"));
}
```

---

## 🐍 Python vs Java Comparison

### Headers in Python (What You Know)
```python
import requests

# Python with requests library
headers = {
    "Content-Type": "application/json",
    "Authorization": "Bearer token123",
    "X-API-Key": "abc123"
}

response = requests.get("https://api.example.com/users/1", headers=headers)
assert response.status_code == 200
assert response.headers["Content-Type"] == "application/json"
```

### Headers in Java with RestAssured (What You're Learning)
```java
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

// Method 1: Individual headers
given()
    .header("Content-Type", "application/json")
    .header("Authorization", "Bearer token123")
    .header("X-API-Key", "abc123")
.when()
    .get("/users/1")
.then()
    .statusCode(200)
    .header("Content-Type", "application/json");

// Method 2: Headers map
Map<String, String> headers = new HashMap<>();
headers.put("Content-Type", "application/json");
headers.put("Authorization", "Bearer token123");

given()
    .headers(headers)
.when()
    .get("/users/1");
```

### Query Params in Python vs Java

**Python:**
```python
params = {
    "page": 2,
    "limit": 10,
    "sort": "name"
}

response = requests.get("https://api.example.com/users", params=params)
```

**Java:**
```java
given()
    .queryParam("page", 2)
    .queryParam("limit", 10)
    .queryParam("sort", "name")
.when()
    .get("/users");

// Or with Map
Map<String, Object> params = new HashMap<>();
params.put("page", 2);
params.put("limit", 10);
params.put("sort", "name");

given().queryParams(params).when().get("/users");
```

### Response Extraction Comparison

**Python:**
```python
response = requests.post("https://api.example.com/users", json={"name": "John"})
user_id = response.json()["id"]  # Extract ID

# Use in next request
response2 = requests.get(f"https://api.example.com/users/{user_id}")
```

**Java:**
```java
int userId = given()
    .body("{\"name\":\"John\"}")
    .post("/users")
    .then()
    .extract().path("id");  // Extract ID

// Use in next request
given()
    .pathParam("userId", userId)
    .get("/users/{userId}");
```

**Key Differences:**

| Aspect | Python | Java/RestAssured |
|--------|--------|------------------|
| **Headers** | Dictionary | .header() or Map |
| **Query Params** | params dict | .queryParam() or Map |
| **Extraction** | response.json()["key"] | .extract().path("key") |
| **Reusability** | Manual function/class | RequestSpec/ResponseSpec |
| **Assertions** | Manual assert | Fluent .body() matchers |

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Confusing Path Params and Query Params

❌ **Wrong:**
```java
// Want: /users/123?status=active
given()
    .queryParam("userId", 123)  // Wrong! This creates /users?userId=123
    .queryParam("status", "active")
.when()
    .get("/users");
```

✅ **Correct:**
```java
// /users/123?status=active
given()
    .pathParam("userId", 123)  // Path param
    .queryParam("status", "active")  // Query param
.when()
    .get("/users/{userId}");
```

**Why it matters:** Path params identify resources, query params filter them. Using wrong type breaks the API call.

---

### Mistake 2: Wrong JSON Path Syntax for Arrays

❌ **Wrong:**
```java
// Response: [{"id": 1}, {"id": 2}]
.body("[0]id", equalTo(1))  // Missing dot
.body("0.id", equalTo(1))   // Wrong syntax
```

✅ **Correct:**
```java
// Response: [{"id": 1}, {"id": 2}]
.body("[0].id", equalTo(1))  // Correct
.body("id", hasItem(1))      // Check array contains
.body("size()", equalTo(2))  // Array size
```

**Why it matters:** Wrong syntax causes test failures with unclear error messages.

---

### Mistake 3: Not Reusing Specifications

❌ **Wrong:**
```java
@Test
public void test1() {
    given()
        .baseUri("https://api.example.com")
        .header("Authorization", "Bearer token")
        .contentType(ContentType.JSON)
    .when().get("/users/1").then().statusCode(200);
}

@Test
public void test2() {
    given()
        .baseUri("https://api.example.com")  // Repeated
        .header("Authorization", "Bearer token")  // Repeated
        .contentType(ContentType.JSON)  // Repeated
    .when().get("/users/2").then().statusCode(200);
}
```

✅ **Correct:**
```java
public class APITest {
    static RequestSpecification requestSpec;

    @BeforeClass
    public static void setup() {
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://api.example.com")
            .addHeader("Authorization", "Bearer token")
            .setContentType(ContentType.JSON)
            .build();
    }

    @Test
    public void test1() {
        given().spec(requestSpec)
            .when().get("/users/1").then().statusCode(200);
    }

    @Test
    public void test2() {
        given().spec(requestSpec)
            .when().get("/users/2").then().statusCode(200);
    }
}
```

**Why it matters:** DRY principle. Changes need to be made only once, not in every test.

---

### Mistake 4: Forgetting Content-Type Header for POST/PUT

❌ **Wrong:**
```java
given()
    .body("{\"name\":\"John\"}")  // Sending JSON but no Content-Type
.when()
    .post("/users")
.then()
    .statusCode(201);  // Might fail with 415 Unsupported Media Type
```

✅ **Correct:**
```java
given()
    .contentType(ContentType.JSON)  // Tell server you're sending JSON
    .body("{\"name\":\"John\"}")
.when()
    .post("/users")
.then()
    .statusCode(201);
```

**Why it matters:** Server needs to know what format you're sending. Missing Content-Type often results in 415 errors.

---

### Mistake 5: Incorrect Type in JSON Path Validation

❌ **Wrong:**
```java
// Response: {"id": 123, "name": "John"}
.body("id", equalTo("123"))  // Wrong! 123 is a number, not string
```

✅ **Correct:**
```java
// Response: {"id": 123, "name": "John"}
.body("id", equalTo(123))  // Correct - no quotes for numbers
.body("name", equalTo("John"))  // Correct - quotes for strings
```

**Why it matters:** Type mismatch causes test failures even when the value is correct.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use Specs for Different Environments**
```java
public class EnvironmentConfig {
    public static RequestSpecification getDevSpec() {
        return new RequestSpecBuilder()
            .setBaseUri("https://dev-api.example.com")
            .addHeader("X-Environment", "dev")
            .build();
    }

    public static RequestSpecification getProdSpec() {
        return new RequestSpecBuilder()
            .setBaseUri("https://api.example.com")
            .addHeader("X-Environment", "prod")
            .setRelaxedHTTPSValidation(false)  // Strict in prod
            .build();
    }
}

// Use based on environment variable
RequestSpecification spec = System.getenv("ENV").equals("prod")
    ? EnvironmentConfig.getProdSpec()
    : EnvironmentConfig.getDevSpec();
```

**Tip 2: Log Only Failed Tests**
```java
// Don't clutter logs with successful test details
.then()
    .log().ifValidationFails()  // Only log if assertions fail
    .statusCode(200);
```

**Tip 3: Store Tokens in Test Context**
```java
public class TestContext {
    private static String authToken;
    private static Map<String, Object> testData = new HashMap<>();

    public static void setAuthToken(String token) {
        authToken = token;
    }

    public static String getAuthToken() {
        return authToken;
    }

    public static void setTestData(String key, Object value) {
        testData.put(key, value);
    }

    public static Object getTestData(String key) {
        return testData.get(key);
    }
}

// Use in tests
@BeforeClass
public void login() {
    String token = given()
        .body("{\"username\":\"admin\",\"password\":\"pass\"}")
        .post("/auth/login")
        .then().extract().path("token");

    TestContext.setAuthToken(token);
}

@Test
public void getUserTest() {
    given()
        .header("Authorization", "Bearer " + TestContext.getAuthToken())
    .when().get("/users/1");
}
```

---

## 🔍 Deep Dive: GPath (Groovy Path) in RestAssured

**What experienced developers know:**
RestAssured uses GPath (Groovy's JSON/XML path implementation) which is more powerful than simple dot notation.

**Advanced GPath Features:**

```java
// Response with complex data
{
  "products": [
    {"id": 1, "name": "Laptop", "price": 999, "inStock": true},
    {"id": 2, "name": "Mouse", "price": 25, "inStock": false},
    {"id": 3, "name": "Keyboard", "price": 75, "inStock": true}
  ]
}

// Find all in-stock products
.body("products.findAll{it.inStock == true}.size()", equalTo(2))

// Get names of expensive products (price > 50)
.body("products.findAll{it.price > 50}.name", hasItems("Laptop", "Keyboard"))

// Find product by name
.body("products.find{it.name == 'Mouse'}.price", equalTo(25))

// Get min/max prices
.body("products.price.min()", equalTo(25))
.body("products.price.max()", equalTo(999))

// Sum all prices
.body("products.price.sum()", equalTo(1099))

// Collect specific fields
.body("products.collect{it.name}", hasItems("Laptop", "Mouse", "Keyboard"))

// Any/every matching
.body("products.any{it.price > 500}", is(true))
.body("products.every{it.id != null}", is(true))
```

**When to use:** Complex filtering, aggregation, and conditional checks on JSON arrays.

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **RequestSpecification** | Reusable request configuration | Base URI, headers, auth |
| **ResponseSpecification** | Reusable response validations | Status code, response time |
| **Path Parameter** | Dynamic part of URL path | `/users/{id}` |
| **Query Parameter** | Filter/sort parameter after `?` | `/users?status=active` |
| **JSON Path** | Query language for JSON | `address.city` |
| **GPath** | Groovy's path expressions | `users.findAll{it.active}` |
| **Content-Type** | Format of request body | `application/json` |
| **Accept** | Desired response format | `application/xml` |
| **Authorization** | Authentication credentials | `Bearer token123` |
| **Extract** | Pull values from response | `.extract().path("id")` |

---

## 🎓 Interview Prep

**Common Interview Questions on Advanced RestAssured:**

**Q1:** What is the difference between RequestSpecification and setting base URI directly?
**A:** RequestSpecification is a reusable object that holds multiple configurations (base URI, headers, auth, content type) that can be applied to multiple tests. Setting base URI directly only sets the URL. Example:
```java
// RequestSpecification - reusable, holds all config
RequestSpecification spec = new RequestSpecBuilder()
    .setBaseUri("https://api.example.com")
    .addHeader("Authorization", "Bearer token")
    .setContentType(ContentType.JSON)
    .build();

// Use in many tests
given().spec(spec).when().get("/users");
given().spec(spec).when().get("/posts");

// vs. setting base URI only
RestAssured.baseURI = "https://api.example.com";
// Still need to add headers in each test
```

**Q2:** When would you use path parameters vs query parameters?
**A:**
- **Path parameters** identify the specific resource you want (required): `/users/{userId}`
- **Query parameters** filter, sort, or paginate a collection (optional): `/users?status=active&page=2`

Example: `GET /users/123/posts?limit=10` - 123 is path param (which user), limit is query param (how many posts to return).

**Q3:** How do you validate a nested JSON response?
**A:** Use dot notation for nested objects and bracket notation for arrays:
```java
// Response: {"user": {"address": {"city": "NYC"}}}
.body("user.address.city", equalTo("NYC"))

// Response: {"users": [{"name": "John"}]}
.body("users[0].name", equalTo("John"))
```

**Q4:** How would you extract a value from response and use it in next request?
**A:** Use `.extract().path()`:
```java
int userId = given().body(newUser)
    .post("/users")
    .then().extract().path("id");

given().pathParam("userId", userId)
    .when().get("/users/{userId}");
```

**Q5:** What headers are typically required for a POST request?
**A:**
- **Content-Type** (required) - Tells server the format of data you're sending
- **Authorization** (if protected) - Authentication token
- **Accept** (optional) - Format you want back
```java
given()
    .contentType(ContentType.JSON)
    .header("Authorization", "Bearer token")
    .header("Accept", "application/json")
    .body(requestBody)
.when()
    .post("/users");
```

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. **What is the benefit of using RequestSpecification over setting headers in every test?**
2. **Write the URL that results from: `.pathParam("id", 5).queryParam("status", "active").get("/users/{id}")`**
3. **What's the JSON Path expression to get the city from: `{"user": {"address": {"city": "NYC"}}}`?**
4. **Why do POST requests typically need a Content-Type header?**
5. **How would you extract and reuse a user ID from a POST response?**

**Answers at the end of Practice file.**

---

## 🚀 Next Steps

Now that you understand advanced RestAssured concepts, move to:
1. **Day-2-Practice-Exercises.md** - 7 progressive exercises with JSONPlaceholder API
2. **Day-2-Debug-Practice.md** - Fix common specification and header bugs
3. **Day-2-Project-Guide.md** - Build enhanced API framework with specs

**Tomorrow Preview:** Day 3 will cover serialization/deserialization, POJO mapping, and data-driven API testing.

---

**🎯 Day 2 Complete!** You now master request/response specs, headers, parameters, and advanced JSON validation!
