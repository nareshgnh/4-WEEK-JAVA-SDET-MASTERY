# DAY 2 CHEAT SHEET: RESTASSURED DEEP DIVE

## ⚡ Quick Syntax Reference

### Request Specification
```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.specification.RequestSpecification;
import io.restassured.http.ContentType;

// Create reusable spec
RequestSpecification requestSpec = new RequestSpecBuilder()
    .setBaseUri("https://api.example.com")
    .setContentType(ContentType.JSON)
    .addHeader("Authorization", "Bearer token123")
    .addHeader("X-API-Key", "abc123")
    .build();

// Use in tests
given()
    .spec(requestSpec)  // Apply all settings
.when()
    .get("/users/1");
```

### Response Specification
```java
import io.restassured.builder.ResponseSpecBuilder;
import io.restassured.specification.ResponseSpecification;

// Create reusable response validations
ResponseSpecification responseSpec = new ResponseSpecBuilder()
    .expectStatusCode(200)
    .expectContentType(ContentType.JSON)
    .expectResponseTime(lessThan(3000L))
    .expectHeader("Server", notNullValue())
    .build();

// Use in tests
.then()
    .spec(responseSpec)  // Apply all validations
    .body("name", equalTo("John"));  // Add specific checks
```

### Headers
```java
// Single header
.header("Content-Type", "application/json")
.header("Authorization", "Bearer token123")

// Multiple headers (chaining)
.header("Content-Type", "application/json")
.header("Accept", "application/json")
.header("User-Agent", "RestAssured/1.0")

// Using Map
Map<String, String> headers = new HashMap<>();
headers.put("Content-Type", "application/json");
headers.put("Authorization", "Bearer token");
given().headers(headers)

// Shorthand for Content-Type
.contentType(ContentType.JSON)         // application/json
.contentType(ContentType.XML)          // application/xml
.contentType(ContentType.TEXT)         // text/plain

// Common headers
.header("Content-Type", "application/json")     // Request body format
.header("Accept", "application/json")           // Desired response format
.header("Authorization", "Bearer token")        // Auth token
.header("X-API-Key", "abc123")                  // API key
```

### Path Parameters
```java
// Single path parameter
.pathParam("userId", 123)
.get("/users/{userId}")                    // Result: /users/123

// Multiple path parameters
.pathParam("userId", 123)
.pathParam("postId", 456)
.get("/users/{userId}/posts/{postId}")    // Result: /users/123/posts/456

// Using pathParams
.pathParams("userId", 123, "postId", 456)
.get("/users/{userId}/posts/{postId}")
```

### Query Parameters
```java
// Single query parameter
.queryParam("page", 2)
.get("/users")                            // Result: /users?page=2

// Multiple query parameters
.queryParam("page", 2)
.queryParam("limit", 10)
.queryParam("sort", "name")
.get("/users")                            // Result: /users?page=2&limit=10&sort=name

// Using queryParams
.queryParams("page", 2, "limit", 10)
.get("/users")

// Using Map
Map<String, Object> params = new HashMap<>();
params.put("page", 2);
params.put("limit", 10);
given().queryParams(params)
```

### Combined Path and Query Parameters
```java
// /posts/1/comments?page=2&limit=5
given()
    .pathParam("postId", 1)
    .queryParam("page", 2)
    .queryParam("limit", 5)
.when()
    .get("/posts/{postId}/comments")
```

---

## 🔑 JSON Path Expressions

### Root Level Fields
```java
// Response: {"id": 1, "name": "John", "email": "john@example.com"}
.body("id", equalTo(1))
.body("name", equalTo("John"))
.body("email", containsString("@"))
```

### Nested Objects (Dot Notation)
```java
// Response: {"user": {"address": {"city": "NYC", "zip": "10001"}}}
.body("user.address.city", equalTo("NYC"))
.body("user.address.zip", equalTo("10001"))
```

### Root Array
```java
// Response: [{"id": 1}, {"id": 2}, {"id": 3}]
.body("size()", equalTo(3))              // Array size
.body("[0].id", equalTo(1))              // First element
.body("[1].id", equalTo(2))              // Second element
.body("[-1].id", equalTo(3))             // Last element
.body("id", hasItems(1, 2, 3))           // All IDs
.body("id", everyItem(notNullValue()))   // All IDs not null
```

### Nested Arrays
```java
// Response: {"users": [{"name": "John"}, {"name": "Jane"}]}
.body("users.size()", equalTo(2))
.body("users[0].name", equalTo("John"))
.body("users[1].name", equalTo("Jane"))
.body("users.name", hasItems("John", "Jane"))
```

### Advanced GPath Expressions
```java
// Response: {"products": [{"name": "A", "price": 100}, {"name": "B", "price": 50}]}

// Filter with findAll
.body("products.findAll{it.price > 75}.size()", equalTo(1))

// Find single item
.body("products.find{it.name == 'A'}.price", equalTo(100))

// Get all names
.body("products.collect{it.name}", hasItems("A", "B"))

// Min/Max/Sum
.body("products.price.min()", equalTo(50))
.body("products.price.max()", equalTo(100))
.body("products.price.sum()", equalTo(150))

// Any/Every
.body("products.any{it.price > 75}", is(true))
.body("products.every{it.price > 0}", is(true))
```

---

## 🎯 Response Extraction

### Extract Single Value
```java
// Extract field value
int userId = given()
    .when().get("/users/1")
    .then().statusCode(200)
    .extract().path("id");

// Extract nested value
String city = get("/users/1")
    .then().extract().path("address.city");

// Extract from array
String firstName = get("/users")
    .then().extract().path("[0].name");
```

### Extract Response Object
```java
// Get entire response
Response response = get("/users/1")
    .then().extract().response();

// Extract multiple values
int id = response.path("id");
String name = response.path("name");
int statusCode = response.statusCode();
String header = response.header("Content-Type");
long responseTime = response.time();
```

### Extract as String
```java
// Get raw JSON
String jsonResponse = get("/users/1")
    .then().extract().asString();

System.out.println("JSON: " + jsonResponse);
```

### Extract and Reuse Pattern
```java
// Create resource and use ID
int postId = given()
    .body(newPost)
    .post("/posts")
    .then()
    .statusCode(201)
    .extract().path("id");

// Use extracted ID
given()
    .pathParam("id", postId)
    .get("/posts/{id}")
    .then()
    .statusCode(200);
```

---

## 📋 Common Patterns

### Pattern 1: CRUD Test Flow
```java
// 1. CREATE
int id = given().body(data).post("/users")
         .then().statusCode(201).extract().path("id");

// 2. READ
given().pathParam("id", id).get("/users/{id}")
    .then().statusCode(200).body("name", equalTo(data.getName()));

// 3. UPDATE
given().pathParam("id", id).body(updatedData).put("/users/{id}")
    .then().statusCode(200);

// 4. DELETE
given().pathParam("id", id).delete("/users/{id}")
    .then().statusCode(200);
```

### Pattern 2: Specifications in Base Class
```java
public class BaseTest {
    protected static RequestSpecification requestSpec;

    @BeforeClass
    public static void setup() {
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://api.example.com")
            .setContentType(ContentType.JSON)
            .build();
    }
}

public class UserTest extends BaseTest {
    @Test
    public void test() {
        given().spec(requestSpec).get("/users");
    }
}
```

### Pattern 3: Test Data Management
```java
public class TestContext {
    private static Map<String, Object> testData = new HashMap<>();

    public static void set(String key, Object value) {
        testData.put(key, value);
    }

    public static Object get(String key) {
        return testData.get(key);
    }
}

// Use across tests
TestContext.set("userId", 123);
int userId = (Integer) TestContext.get("userId");
```

### Pattern 4: Environment-Based Config
```java
public class APISpecifications {
    public static RequestSpecification getSpec(String env) {
        String baseUri = env.equals("prod")
            ? "https://api.example.com"
            : "https://dev-api.example.com";

        return new RequestSpecBuilder()
            .setBaseUri(baseUri)
            .build();
    }
}

// Use
String env = System.getProperty("env", "dev");
given().spec(APISpecifications.getSpec(env))
```

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Path vs Query** | `.queryParam("id", 5).get("/users/{id}")` | `.pathParam("id", 5).get("/users/{id}")` |
| **Array syntax** | `.body("0.name", ...)` | `.body("[0].name", ...)` |
| **Type mismatch** | `.body("id", equalTo("1"))` | `.body("id", equalTo(1))` |
| **Nested path** | `.body("user-address-city", ...)` | `.body("user.address.city", ...)` |
| **Missing Content-Type** | `given().body(json).post()` | `given().contentType(JSON).body(json).post()` |
| **Static spec** | `RequestSpec spec;` in static method | `static RequestSpec spec;` |
| **Spec conflict** | ResponseSpec expects 200 for POST | Create separate specs or override |

---

## 💡 Key Takeaways

**Today You Mastered:**
- ✅ RequestSpecification for reusable request configs
- ✅ ResponseSpecification for reusable validations
- ✅ Headers (Content-Type, Authorization, Accept, custom)
- ✅ Path parameters for resource identification (`/users/{id}`)
- ✅ Query parameters for filtering (`/users?status=active`)
- ✅ Advanced JSON Path with GPath expressions
- ✅ Response extraction and value reuse
- ✅ Complete CRUD test patterns

**Critical for Automation:**
- 🎯 **Use Specifications** - Eliminates 80% of code duplication
- 🎯 **Path vs Query** - Path identifies resource, Query filters collection
- 🎯 **Extract and Reuse** - Real tests chain multiple API calls
- 🎯 **JSON Path Mastery** - Essential for complex response validation

---

## 🔍 Path vs Query Decision Tree

```
Question: "Get posts WHERE userId = 1"
Answer: Query parameter → /posts?userId=1

Question: "Get THE post with id = 5"
Answer: Path parameter → /posts/{id} with id=5

Question: "Get comments FOR post 1, page 2"
Answer: Path + Query → /posts/{postId}/comments?page=2
```

**Rule of Thumb:**
- **Required** and **identifies resource** → Path param
- **Optional** and **filters/sorts** → Query param

---

## 🎤 Interview Phrases

**When asked about API test framework design, say:**

*"I design API frameworks with a layered architecture. I use RequestSpecification to centralize common configurations like base URI, headers, and authentication, and ResponseSpecification for reusable validations like response time and content type. This follows the DRY principle and makes tests maintainable. I also implement a test data manager to share state between tests, enabling realistic test flows like create-read-update-delete sequences."*

**When asked about JSON validation:**

*"I use RestAssured's JSON Path expressions with GPath for validating complex responses. For nested objects, I use dot notation like `user.address.city`. For arrays, I use bracket notation like `[0].name`. I also leverage GPath's filtering capabilities with findAll and find to validate specific subsets of data, which is especially useful for testing search and filter APIs."*

**When asked about authentication in API tests:**

*"I handle authentication through RequestSpecification by adding Authorization headers or using RestAssured's built-in auth methods like `.auth().oauth2()` or `.auth().basic()`. For token-based auth, I typically have a setup method that authenticates and stores the token in a test context, then all subsequent tests use that token through the specification."*

---

## 📊 Hamcrest Matchers Quick Reference

```java
// Equality
equalTo(value)               // Exact match
is(value)                    // Same as equalTo
not(value)                   // Not equal

// Null/Empty
notNullValue()              // Not null
nullValue()                 // Is null
emptyString()               // ""
not(emptyString())          // Not empty

// Strings
containsString("text")      // Contains substring
startsWith("prefix")        // Starts with
endsWith("suffix")          // Ends with
equalToIgnoringCase("TEXT") // Case-insensitive

// Numbers
greaterThan(10)             // > 10
lessThan(10)                // < 10
greaterThanOrEqualTo(10)    // >= 10
lessThanOrEqualTo(10)       // <= 10

// Collections
hasSize(10)                 // Size is 10
hasItem("value")            // Contains value
hasItems("a", "b")          // Contains all
everyItem(notNullValue())   // All items match

// Combining
allOf(matcher1, matcher2)   // Both match
anyOf(matcher1, matcher2)   // Either matches
isOneOf(1, 2, 3)           // Value is one of
```

---

## 🚀 Advanced Tips

### Tip 1: Conditional Logging
```java
// Only log if test fails
.then()
    .log().ifValidationFails()
    .statusCode(200);

// Log only if status is error (4xx, 5xx)
.then()
    .log().ifError()
    .statusCode(200);
```

### Tip 2: Response Time Validation
```java
import static java.util.concurrent.TimeUnit.*;

.then()
    .time(lessThan(2000L))              // < 2000ms
    .time(lessThan(2L, SECONDS))        // < 2 seconds
    .time(greaterThan(100L))            // > 100ms
```

### Tip 3: Extract to POJO
```java
// If you have a User class
User user = get("/users/1")
    .then()
    .statusCode(200)
    .extract().as(User.class);

// Now use object
assertEquals("John", user.getName());
```

### Tip 4: Multiple Specs
```java
// Combine request and response specs
given()
    .spec(requestSpec)              // Request config
.when()
    .get("/users/1")
.then()
    .spec(successResponseSpec)      // Common validations
    .body("name", equalTo("John")); // Test-specific
```

### Tip 5: Session Filter for Cookies
```java
import io.restassured.filter.session.SessionFilter;

SessionFilter sessionFilter = new SessionFilter();

// Login and capture session
given()
    .filter(sessionFilter)
    .body(credentials)
    .post("/login");

// Use session in subsequent requests
given()
    .filter(sessionFilter)
    .get("/protected/resource");
```

---

## 📌 Remember This

**The ONE thing to remember from today:**

> **Specifications eliminate repetition. Request specs hold common configs (base URI, headers, auth), response specs hold common validations. Extract values from responses to chain API calls. Path params identify resources, query params filter them.**

---

## 🔮 Tomorrow's Preview

**Day 3 Topic:** Serialization/Deserialization with POJOs

**What to review tonight:**
- Java classes and objects (Week 1)
- JSON structure and validation (Days 1-2)

**What to think about:**
- How would you convert JSON to Java objects?
- What if you need to send complex nested objects?
- How to make test data more maintainable?

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [X] Learned Request/Response Specifications
- [X] Mastered headers and parameters
- [X] Advanced JSON Path validation
- [X] Built enhanced API framework
- [X] 3 hours of API testing practice

**Cumulative Progress:**
- Days completed: 2/5 (Week 4)
- API concepts mastered: 12+
- Framework components built: 5
- Tests written: 20+

**Momentum Statement:**
*You've progressed from basic API tests to building professional frameworks. RequestSpecs and ResponseSpecs are used in every major product company. You're now writing production-quality API automation code—the same patterns used by teams at Google, Amazon, and Microsoft. Keep this momentum!*

---

## 📖 Quick Reference Card (Print This!)

```
REQUEST SPECIFICATION
requestSpec = new RequestSpecBuilder()
  .setBaseUri(url)
  .setContentType(JSON)
  .addHeader(name, value)
  .build()
given().spec(requestSpec)

RESPONSE SPECIFICATION
responseSpec = new ResponseSpecBuilder()
  .expectStatusCode(200)
  .expectContentType(JSON)
  .expectResponseTime(lessThan(3000L))
  .build()
.then().spec(responseSpec)

PATH PARAMETERS
.pathParam("id", 5)
.get("/users/{id}")
→ /users/5

QUERY PARAMETERS
.queryParam("page", 2)
.get("/users")
→ /users?page=2

NESTED JSON
.body("address.city", equalTo("NYC"))
.body("users[0].name", equalTo("John"))
.body("products.findAll{it.price > 50}.size()", equalTo(3))

EXTRACTION
int id = get("/users").then().extract().path("[0].id");
Response r = get("/users/1").then().extract().response();
String name = r.path("name");
```

---

**🎯 Day 2 Cheat Sheet Complete!** Keep this handy for quick reference during practice and interviews!
