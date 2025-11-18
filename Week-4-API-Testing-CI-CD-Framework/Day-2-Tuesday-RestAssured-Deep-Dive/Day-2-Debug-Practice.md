# DAY 2 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

As an SDET, 40% of your time is spent debugging failing tests. The ability to quickly identify and fix issues in API tests is crucial for:
- Maintaining test suites as APIs evolve
- Troubleshooting CI/CD pipeline failures
- Reviewing pull requests with test changes
- Mentoring junior team members

**Today's Focus:** Common mistakes with specifications, headers, parameters, and JSON path expressions.

---

## Challenge 1: Wrong Parameter Type

**Broken Code:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class BuggyTest1 {

    @Test
    public void getUserPosts() {
        baseURI = "https://jsonplaceholder.typicode.com";

        given()
            .queryParam("userId", 1)
        .when()
            .get("/posts/{userId}")  // Trying to get posts for user
        .then()
            .statusCode(200)
            .body("userId", everyItem(equalTo(1)));
    }
}
```

**Error Message:**
```
java.lang.IllegalArgumentException: Path parameters were not correctly defined.
Could not find path parameter with name: userId
```

**Your Task:**
1. Identify the bug
2. Explain why it's wrong
3. Fix it

**Hints:**
- Look at how userId is being used
- Remember the difference between path params and query params

---

**Solution:**

**Bug Location:** Line 10 - mixing query parameter with path parameter placeholder

**Explanation:**
The code uses `.queryParam("userId", 1)` but then tries to use `{userId}` in the URL path. The `{userId}` syntax is for path parameters, not query parameters. Since no path parameter named "userId" was defined with `.pathParam()`, RestAssured throws an error.

**The endpoint `/posts/{userId}` doesn't exist.** The correct endpoint is `/posts` with a query parameter.

**Fixed Code:**
```java
@Test
public void getUserPosts() {
    baseURI = "https://jsonplaceholder.typicode.com";

    given()
        .queryParam("userId", 1)  // Query parameter
    .when()
        .get("/posts")  // Correct endpoint without path param
    .then()
        .statusCode(200)
        .body("userId", everyItem(equalTo(1)));
}
```

**Alternative Fix (if you want to use path param):**
```java
// If the API supported /posts/userId/{id} endpoint
given()
    .pathParam("userId", 1)  // Path parameter
.when()
    .get("/posts/userId/{userId}")  // With path param in URL
.then()
    .statusCode(200);
```

**Key Learning:**
- **Query params** → `.queryParam()` → no placeholder in URL → `/posts?userId=1`
- **Path params** → `.pathParam()` → placeholder in URL → `/posts/{userId}`
- Don't mix the syntax!

---

## Challenge 2: Missing Content-Type Header

**Broken Code:**
```java
@Test
public void createUser() {
    baseURI = "https://jsonplaceholder.typicode.com";

    String requestBody = "{\n" +
        "  \"name\": \"John Doe\",\n" +
        "  \"email\": \"john@example.com\"\n" +
        "}";

    given()
        .body(requestBody)
    .when()
        .post("/users")
    .then()
        .statusCode(201)
        .body("name", equalTo("John Doe"));
}
```

**Error/Issue:**
```
Test might pass but server may not parse JSON correctly
OR
415 Unsupported Media Type error
```

**Your Task:**
1. Find what's missing
2. Explain why it's important
3. Fix the code

**Hints:**
- What tells the server you're sending JSON?
- Check what headers are needed for POST requests

---

**Solution:**

**Bug Location:** Missing Content-Type header in the request

**Explanation:**
When sending JSON data in a POST/PUT/PATCH request, you must tell the server what format you're sending using the `Content-Type` header. Without it:
- Server doesn't know how to parse the request body
- May result in 415 Unsupported Media Type error
- Or server might accept it but parse incorrectly

**Fixed Code:**
```java
@Test
public void createUser() {
    baseURI = "https://jsonplaceholder.typicode.com";

    String requestBody = "{\n" +
        "  \"name\": \"John Doe\",\n" +
        "  \"email\": \"john@example.com\"\n" +
        "}";

    given()
        .header("Content-Type", "application/json")  // FIX: Add Content-Type
        .body(requestBody)
    .when()
        .post("/users")
    .then()
        .statusCode(201)
        .body("name", equalTo("John Doe"));
}
```

**Better Fix (Using RestAssured's shorthand):**
```java
given()
    .contentType(ContentType.JSON)  // Cleaner way
    .body(requestBody)
.when()
    .post("/users");
```

**Even Better (Using RequestSpecification):**
```java
RequestSpecification requestSpec = new RequestSpecBuilder()
    .setBaseUri("https://jsonplaceholder.typicode.com")
    .setContentType(ContentType.JSON)  // Set once for all tests
    .build();

given()
    .spec(requestSpec)
    .body(requestBody)
.when()
    .post("/users");
```

**Key Learning:**
- Always set Content-Type for requests with body (POST, PUT, PATCH)
- Use `.contentType(ContentType.JSON)` instead of manual header
- Best practice: Include Content-Type in RequestSpecification

---

## Challenge 3: Incorrect JSON Path for Arrays

**Broken Code:**
```java
@Test
public void validateFirstPost() {
    baseURI = "https://jsonplaceholder.typicode.com";

    given()
    .when()
        .get("/posts")
    .then()
        .statusCode(200)
        .body("0.id", equalTo(1))  // Bug here
        .body("0.userId", equalTo(1));  // Bug here
}
```

**Error Message:**
```
java.lang.AssertionError:
Expected: is <1>
     but: was null
```

**Your Task:**
1. Identify the incorrect JSON path syntax
2. Understand why it returns null
3. Provide the correct syntax

**Hints:**
- How do you access array elements in JSON path?
- Response is an array: `[{...}, {...}]`

---

**Solution:**

**Bug Location:** Lines 8-9 - Wrong array access syntax

**Explanation:**
When the response is a root-level array, you must use bracket notation `[index]`, not dot notation.

**Response structure:**
```json
[
  {"id": 1, "userId": 1, "title": "..."},
  {"id": 2, "userId": 1, "title": "..."}
]
```

Using `"0.id"` tries to find an object with key "0", which doesn't exist. The correct syntax is `"[0].id"`.

**Fixed Code:**
```java
@Test
public void validateFirstPost() {
    baseURI = "https://jsonplaceholder.typicode.com";

    given()
    .when()
        .get("/posts")
    .then()
        .statusCode(200)
        .body("[0].id", equalTo(1))      // Fixed: bracket notation
        .body("[0].userId", equalTo(1))   // Fixed: bracket notation
        .body("[0].title", notNullValue());
}
```

**Additional Validations:**
```java
.then()
    .statusCode(200)
    // First element
    .body("[0].id", equalTo(1))
    // Last element
    .body("[-1].id", equalTo(100))  // -1 for last element
    // Array size
    .body("size()", equalTo(100))
    // All elements have userId
    .body("userId", everyItem(notNullValue()))
    // Get all titles
    .body("title", hasSize(100));
```

**Key Learning:**
- Root array: Use `[index]` → `[0].id`, `[1].name`
- Nested array: Use dot then bracket → `users[0].name`
- Use `size()` to get array length
- Use `everyItem()` to validate all elements

---

## Challenge 4: Specification Not Applied Correctly

**Broken Code:**
```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.specification.RequestSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

public class BuggyTest4 {

    private RequestSpecification requestSpec;

    @BeforeClass
    public void setup() {
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://jsonplaceholder.typicode.com")
            .addHeader("Content-Type", "application/json")
            .build();
    }

    @Test
    public void getUser() {
        given()
            .spec(requestSpec)
        .when()
            .get("/users/1")
        .then()
            .statusCode(200);
    }
}
```

**Error Message:**
```
java.lang.NullPointerException
    at BuggyTest4.getUser(BuggyTest4.java:19)
```

**Your Task:**
1. Find why requestSpec is null
2. Understand the scope issue
3. Fix the code

**Hints:**
- Check the `static` keyword usage
- TestNG lifecycle and static vs instance variables

---

**Solution:**

**Bug Location:** Line 8 - Missing `static` keyword for instance variable

**Explanation:**
The `@BeforeClass` method is `static`, which means it runs once per class (not per instance). Inside a static method, you can only access static variables.

The `requestSpec` is an instance variable (non-static), so it cannot be accessed from the static `setup()` method. The spec never gets initialized, remaining null, causing NullPointerException when used in tests.

**Fixed Code - Option 1 (Make spec static):**
```java
public class FixedTest4 {

    private static RequestSpecification requestSpec;  // Added static

    @BeforeClass
    public static void setup() {  // static method
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://jsonplaceholder.typicode.com")
            .addHeader("Content-Type", "application/json")
            .build();
    }

    @Test
    public void getUser() {
        given()
            .spec(requestSpec)  // Now works!
        .when()
            .get("/users/1")
        .then()
            .statusCode(200);
    }
}
```

**Fixed Code - Option 2 (Use @BeforeMethod):**
```java
public class FixedTest4 {

    private RequestSpecification requestSpec;  // Non-static

    @BeforeMethod  // Changed to @BeforeMethod (runs before each test)
    public void setup() {  // Non-static method
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://jsonplaceholder.typicode.com")
            .addHeader("Content-Type", "application/json")
            .build();
    }

    @Test
    public void getUser() {
        given()
            .spec(requestSpec)
        .when()
            .get("/users/1")
        .then()
            .statusCode(200);
    }
}
```

**Which approach is better?**
- **Option 1 (static + @BeforeClass):** Creates spec once, shared across all tests. More efficient.
- **Option 2 (instance + @BeforeMethod):** Creates new spec for each test. Needed if specs are modified per test.

**Best Practice:**
```java
// Most common pattern in production
public class APITestBase {
    protected static RequestSpecification requestSpec;

    @BeforeClass
    public static void globalSetup() {
        requestSpec = new RequestSpecBuilder()
            .setBaseUri(ConfigReader.getProperty("api.url"))
            .setContentType(ContentType.JSON)
            .build();
    }
}

// Test class extends base
public class UserAPITest extends APITestBase {
    @Test
    public void getUser() {
        given().spec(requestSpec).when().get("/users/1");
    }
}
```

**Key Learning:**
- **Static** variables with **@BeforeClass** = created once, shared
- **Instance** variables with **@BeforeMethod** = created per test
- Match the variable type (static/instance) with the method type
- Common pattern: static specs in @BeforeClass for efficiency

---

## Challenge 5: Wrong Data Type in Assertion

**Broken Code:**
```java
@Test
public void validatePostId() {
    baseURI = "https://jsonplaceholder.typicode.com";

    given()
    .when()
        .get("/posts/1")
    .then()
        .statusCode(200)
        .body("id", equalTo("1"))  // Bug here
        .body("userId", equalTo("1"));  // Bug here
}
```

**Error Message:**
```
java.lang.AssertionError:
1 expectation failed.
JSON path id doesn't match.
Expected: "1"
  Actual: 1
```

**Your Task:**
1. Identify the type mismatch
2. Understand JSON number vs string
3. Fix the assertion

**Hints:**
- Is "1" the same as 1 in JSON?
- Check the actual response structure

---

**Solution:**

**Bug Location:** Lines 9-10 - Using string "1" instead of integer 1

**Explanation:**
JSON has different data types:
- **Number:** `{"id": 1}` - no quotes
- **String:** `{"name": "John"}` - with quotes

The API returns `id` and `userId` as **numbers** (no quotes), but the test expects **strings** ("1" with quotes). This causes a type mismatch.

**Actual Response:**
```json
{
  "id": 1,          // Number
  "userId": 1,      // Number
  "title": "sunt aut facere..."  // String
}
```

**Fixed Code:**
```java
@Test
public void validatePostId() {
    baseURI = "https://jsonplaceholder.typicode.com";

    given()
    .when()
        .get("/posts/1")
    .then()
        .statusCode(200)
        .body("id", equalTo(1))      // Fixed: number without quotes
        .body("userId", equalTo(1))   // Fixed: number without quotes
        .body("title", containsString("sunt"));  // String with quotes
}
```

**Type Reference:**
```java
// Numbers (no quotes)
.body("id", equalTo(1))
.body("price", equalTo(99.99))
.body("quantity", greaterThan(0))

// Strings (quotes)
.body("name", equalTo("John"))
.body("email", containsString("@"))

// Booleans
.body("active", equalTo(true))
.body("verified", is(false))

// Null
.body("middleName", nullValue())
.body("email", notNullValue())
```

**Common Type Errors:**
```java
// Wrong
.body("id", equalTo("123"))        // String "123"
.body("price", equalTo("99.99"))   // String "99.99"
.body("active", equalTo("true"))   // String "true"

// Correct
.body("id", equalTo(123))          // Number 123
.body("price", equalTo(99.99))     // Number 99.99
.body("active", equalTo(true))     // Boolean true
```

**Key Learning:**
- Match the exact data type from JSON response
- Numbers: no quotes in matcher
- Strings: use quotes
- Use logging to see actual response types: `.then().log().all()`

---

## Challenge 6: Nested JSON Path Error

**Broken Code:**
```java
@Test
public void validateUserAddress() {
    baseURI = "https://jsonplaceholder.typicode.com";

    given()
    .when()
        .get("/users/1")
    .then()
        .statusCode(200)
        .body("address-city", equalTo("Gwenborough"))  // Bug
        .body("address/geo/lat", equalTo("-37.3159"));  // Bug
}
```

**Error Message:**
```
java.lang.AssertionError:
1 expectation failed.
JSON path address-city doesn't match.
Expected: Gwenborough
  Actual: null
```

**Your Task:**
1. Find the wrong path syntax
2. Learn correct nested object notation
3. Fix both paths

**Response Structure:**
```json
{
  "id": 1,
  "address": {
    "street": "Kulas Light",
    "city": "Gwenborough",
    "geo": {
      "lat": "-37.3159",
      "lng": "81.1496"
    }
  }
}
```

---

**Solution:**

**Bug Location:** Lines 9-10 - Wrong nested object path syntax

**Explanation:**
For nested JSON objects, RestAssured uses **dot notation** (.), not hyphens (-) or slashes (/).

- `address-city` looks for a field literally named "address-city" (doesn't exist)
- `address/geo/lat` uses wrong separator

The correct syntax is dot notation: `address.city` and `address.geo.lat`

**Fixed Code:**
```java
@Test
public void validateUserAddress() {
    baseURI = "https://jsonplaceholder.typicode.com";

    given()
    .when()
        .get("/users/1")
    .then()
        .statusCode(200)
        .body("address.city", equalTo("Gwenborough"))     // Fixed: dot notation
        .body("address.geo.lat", equalTo("-37.3159"))     // Fixed: dot notation
        .body("address.street", equalTo("Kulas Light"))
        .body("address.geo.lng", equalTo("81.1496"));
}
```

**More Nested Examples:**
```java
// Response with deep nesting
{
  "user": {
    "profile": {
      "personal": {
        "name": "John",
        "age": 30
      }
    }
  }
}

// Validation
.body("user.profile.personal.name", equalTo("John"))
.body("user.profile.personal.age", equalTo(30))
```

**Nested Arrays:**
```java
// Response
{
  "company": {
    "employees": [
      {"name": "John", "role": "Dev"},
      {"name": "Jane", "role": "QA"}
    ]
  }
}

// Validation
.body("company.employees[0].name", equalTo("John"))
.body("company.employees[1].role", equalTo("QA"))
.body("company.employees.name", hasItems("John", "Jane"))
.body("company.employees.size()", equalTo(2))
```

**Path Syntax Reference:**
```java
// Nested objects: use dot (.)
"address.city"
"user.profile.email"
"company.address.zipcode"

// Arrays: use brackets []
"[0].name"           // Root array, first element
"users[0].name"      // Nested array, first element
"users[1].email"

// Combined
"departments[0].employees[0].name"  // Nested arrays
```

**Key Learning:**
- Nested objects: **dot notation** (.)
- Arrays: **bracket notation** ([])
- Can go multiple levels deep: `a.b.c.d.e`
- Use `.log().all()` to see response structure when debugging

---

## Challenge 7: Specification Override Issue

**Broken Code:**
```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.builder.ResponseSpecBuilder;
import io.restassured.http.ContentType;
import io.restassured.specification.RequestSpecification;
import io.restassured.specification.ResponseSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

public class BuggyTest7 {

    private static RequestSpecification requestSpec;
    private static ResponseSpecification responseSpec;

    @BeforeClass
    public static void setup() {
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://jsonplaceholder.typicode.com")
            .setContentType(ContentType.JSON)
            .build();

        responseSpec = new ResponseSpecBuilder()
            .expectStatusCode(200)  // Expects 200 for all
            .expectContentType(ContentType.JSON)
            .build();
    }

    @Test
    public void createPost() {
        String body = "{\"title\":\"Test\",\"body\":\"Content\",\"userId\":1}";

        given()
            .spec(requestSpec)
            .body(body)
        .when()
            .post("/posts")
        .then()
            .spec(responseSpec);  // Bug: expects 200, but POST returns 201
    }
}
```

**Error Message:**
```
java.lang.AssertionError:
1 expectation failed.
Expected status code <200> but was <201>.
```

**Your Task:**
1. Identify why the test fails
2. Understand specification limitations
3. Provide solutions

---

**Solution:**

**Bug Location:** Line 22 and 38 - ResponseSpec expects 200, but POST returns 201

**Explanation:**
The `responseSpec` is configured to always expect status code 200. This works fine for GET requests, but POST requests that create resources should return **201 Created**, not 200.

Applying the same response spec to all tests creates this conflict.

**Solution 1: Override Status Code in Test**
```java
@Test
public void createPost() {
    String body = "{\"title\":\"Test\",\"body\":\"Content\",\"userId\":1}";

    given()
        .spec(requestSpec)
        .body(body)
    .when()
        .post("/posts")
    .then()
        .statusCode(201)  // Override the spec's 200 expectation
        .contentType(ContentType.JSON);  // Still validate content type
}
```

**Solution 2: Create Multiple Response Specs**
```java
@BeforeClass
public static void setup() {
    requestSpec = new RequestSpecBuilder()
        .setBaseUri("https://jsonplaceholder.typicode.com")
        .setContentType(ContentType.JSON)
        .build();

    // For GET requests
    ResponseSpecification getResponseSpec = new ResponseSpecBuilder()
        .expectStatusCode(200)
        .expectContentType(ContentType.JSON)
        .build();

    // For POST requests
    ResponseSpecification postResponseSpec = new ResponseSpecBuilder()
        .expectStatusCode(201)
        .expectContentType(ContentType.JSON)
        .build();

    // For DELETE requests
    ResponseSpecification deleteResponseSpec = new ResponseSpecBuilder()
        .expectStatusCode(isOneOf(200, 204))  // Either OK or No Content
        .build();
}

@Test
public void createPost() {
    given()
        .spec(requestSpec)
        .body(body)
    .when()
        .post("/posts")
    .then()
        .spec(postResponseSpec);  // Use POST-specific spec
}
```

**Solution 3: Remove Status Code from Response Spec (Best Practice)**
```java
@BeforeClass
public static void setup() {
    requestSpec = new RequestSpecBuilder()
        .setBaseUri("https://jsonplaceholder.typicode.com")
        .setContentType(ContentType.JSON)
        .build();

    // Only common validations, not status code
    responseSpec = new ResponseSpecBuilder()
        .expectContentType(ContentType.JSON)
        .expectResponseTime(lessThan(3000L))  // Performance check
        .expectHeader("Server", notNullValue())
        .build();
}

@Test
public void createPost() {
    given()
        .spec(requestSpec)
        .body(body)
    .when()
        .post("/posts")
    .then()
        .statusCode(201)  // Test-specific status code
        .spec(responseSpec);  // Common validations
}

@Test
public void getPost() {
    given()
        .spec(requestSpec)
    .when()
        .get("/posts/1")
    .then()
        .statusCode(200)  // Different status code
        .spec(responseSpec);  // Same common validations
}
```

**Best Practice Pattern:**
```java
public class APISpecifications {

    // Request specs
    public static RequestSpecification getRequestSpec(String baseUri) {
        return new RequestSpecBuilder()
            .setBaseUri(baseUri)
            .setContentType(ContentType.JSON)
            .addHeader("Accept", "application/json")
            .build();
    }

    // Common response validations (no status code)
    public static ResponseSpecification getCommonResponseSpec() {
        return new ResponseSpecBuilder()
            .expectContentType(ContentType.JSON)
            .expectResponseTime(lessThan(5000L))
            .build();
    }
}

// Usage
@Test
public void test() {
    given()
        .spec(APISpecifications.getRequestSpec("https://api.example.com"))
    .when()
        .post("/users")
    .then()
        .statusCode(201)  // Test-specific
        .spec(APISpecifications.getCommonResponseSpec());  // Common validations
}
```

**Key Learning:**
- Don't put test-specific validations (status codes) in shared specs
- Response specs should contain only truly common validations
- Create multiple specs for different scenarios (GET, POST, DELETE)
- Test-specific assertions should be in the test itself
- Balance between reusability and flexibility

---

## 🎯 Debugging Skills Assessment

After completing all challenges:

| Challenge | Solved Independently? | Time Taken | Confidence (1-10) |
|-----------|----------------------|------------|-------------------|
| 1 - Path vs Query | ☐ | ___ min | __/10 |
| 2 - Content-Type | ☐ | ___ min | __/10 |
| 3 - Array JSON Path | ☐ | ___ min | __/10 |
| 4 - Static Spec | ☐ | ___ min | __/10 |
| 5 - Data Types | ☐ | ___ min | __/10 |
| 6 - Nested Paths | ☐ | ___ min | __/10 |
| 7 - Spec Override | ☐ | ___ min | __/10 |

**Most Challenging:**
-

**Easiest:**
-

**Skills Improved:**
-

---

## 🚀 Pro Debugging Tips

**Tip 1: Always Log When Debugging**
```java
given()
    .log().all()  // Log request
.when()
    .get("/users/1")
.then()
    .log().all()  // Log response
    .statusCode(200);
```

**Tip 2: Use .extract() to Inspect Response**
```java
Response response = get("/users/1").then().extract().response();
System.out.println("Full response: " + response.asString());
System.out.println("Status: " + response.statusCode());
System.out.println("City: " + response.path("address.city"));
```

**Tip 3: Validate Response Structure First**
```java
// Before complex validations, ensure basic structure
.then()
    .statusCode(200)
    .body("$", notNullValue())  // Response not empty
    .body("id", notNullValue())  // Key fields exist
    .log().ifValidationFails();  // Only log failures
```

**Tip 4: Use Online JSON Validators**
- Copy response from logs
- Paste into https://jsonpathfinder.com/
- Test JSON path expressions interactively

---

## 💡 Common Debugging Checklist

When a test fails, check:

1. **Status Code**
   - [ ] Expected status matches actual (200, 201, 404, etc.)
   - [ ] Using correct HTTP method (GET vs POST vs PUT)

2. **Headers**
   - [ ] Content-Type header set for POST/PUT/PATCH
   - [ ] Authorization header included if endpoint is protected
   - [ ] Accept header if API supports multiple formats

3. **Parameters**
   - [ ] Path params: using `.pathParam()` with `{placeholder}` in URL
   - [ ] Query params: using `.queryParam()` without placeholder

4. **JSON Path**
   - [ ] Correct syntax: `object.field` not `object-field` or `object/field`
   - [ ] Array access: `[0].field` not `0.field`
   - [ ] Data types match: number `1` not string `"1"`

5. **Specifications**
   - [ ] Static variables with @BeforeClass or instance with @BeforeMethod
   - [ ] Spec expectations don't conflict with test (e.g., status code)
   - [ ] Spec is actually applied: `.spec(requestSpec)`

---

**🎯 Debugging Practice Complete!** You can now identify and fix common API testing bugs quickly!

Next: **Day-2-Project-Guide.md** - Build your enhanced API framework!
