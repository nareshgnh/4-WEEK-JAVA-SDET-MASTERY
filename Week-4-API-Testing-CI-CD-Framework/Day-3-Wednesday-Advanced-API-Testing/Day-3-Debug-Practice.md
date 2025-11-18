# DAY 3 DEBUGGING CHALLENGES

## 🐛 Why Debugging Practice?

In real SDET roles, you'll spend 40% of your time debugging test failures. Authentication issues, POJO mismatches, and chaining errors are the most common problems in API automation. These challenges prepare you for production scenarios where tests fail at 3 AM and you need to diagnose quickly.

**Skills You'll Develop:**
- Reading error messages and stack traces
- Identifying authentication issues
- Debugging POJO serialization/deserialization errors
- Fixing data extraction problems
- Troubleshooting chained API call failures

---

## Challenge 1: Authentication Header Missing

**Scenario:** Your colleague wrote this test for a protected endpoint, but it's failing with 401 Unauthorized.

**Broken Code:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Challenge1_AuthIssue {

    @Test
    public void testGetProtectedResource() {
        String token = "QpwL5tke4Pnpja7X4";

        given()
            .baseUri("https://reqres.in/api")
            .header("Content-Type", "application/json")
        .when()
            .get("/users/2")
        .then()
            .statusCode(200)  // Expecting 200 but might fail
            .body("data.email", equalTo("janet.weaver@reqres.in"));
    }
}
```

**Error Message:**
```
java.lang.AssertionError: 1 expectation failed.
Expected status code <200> but was <401>.
```

**Your Task:**
1. Identify what's missing in the code
2. Explain why it causes 401 error
3. Fix the code

**Hints:**
- The token exists but isn't being used
- Check the request headers

---

**Solution:**

**Bug Location:** Lines 10-14 - Missing authorization header

**Explanation:**
The test extracts a token but never uses it in the request. The endpoint requires authentication (Bearer token in Authorization header), but the code only sets Content-Type header. Without the Authorization header, the API returns 401 Unauthorized.

**Fixed Code:**
```java
@Test
public void testGetProtectedResource() {
    String token = "QpwL5tke4Pnpja7X4";

    given()
        .baseUri("https://reqres.in/api")
        .auth().oauth2(token)  // FIX: Add authentication
        // OR: .header("Authorization", "Bearer " + token)
    .when()
        .get("/users/2")
    .then()
        .statusCode(200)
        .body("data.email", equalTo("janet.weaver@reqres.in"));
}
```

**Key Learning:**
- Having a token is not enough - you must send it in the Authorization header
- `.auth().oauth2(token)` automatically adds `Authorization: Bearer <token>` header
- 401 error = Authentication problem (missing or invalid credentials)
- Always check request headers when debugging auth issues (use `.log().headers()`)

---

## Challenge 2: POJO Deserialization Failure

**Scenario:** This test tries to deserialize a user response to a POJO but crashes.

**Broken Code:**
```java
import lombok.Data;
import lombok.AllArgsConstructor;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

// POJO Class
@Data
@AllArgsConstructor  // Only this constructor
public class User {
    private int id;
    private String email;
    private String first_name;
    private String last_name;
}

public class Challenge2_DeserializationIssue {

    @Test
    public void testDeserializeUser() {
        User user = given()
            .baseUri("https://reqres.in/api")
        .when()
            .get("/users/2")
        .then()
            .statusCode(200)
            .extract().path("data");  // Extracts nested object

        System.out.println("User: " + user.getEmail());
    }
}
```

**Error Message:**
```
com.fasterxml.jackson.databind.exc.InvalidDefinitionException:
Cannot construct instance of `User` (no Creators, like default constructor, exist):
cannot deserialize from Object value (no delegate- or property-based Creator)
```

**Your Task:**
1. Identify the problem
2. Explain why Jackson can't deserialize
3. Fix the POJO

**Hints:**
- Look at the constructor annotations
- Jackson needs a specific constructor to deserialize

---

**Solution:**

**Bug Location:** Line 7 - Missing `@NoArgsConstructor`

**Explanation:**
Jackson deserialization works in two steps:
1. Create empty object using no-argument constructor
2. Populate fields using setters

The POJO only has `@AllArgsConstructor` which creates `User(int id, String email, ...)`. Jackson can't find a no-arg constructor `User()`, so deserialization fails.

**Fixed Code:**
```java
import lombok.Data;
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;  // FIX: Import added

@Data
@AllArgsConstructor
@NoArgsConstructor  // FIX: Add no-args constructor for Jackson
public class User {
    private int id;
    private String email;
    private String first_name;
    private String last_name;
}
```

**Alternative Fix (Manual Constructor):**
```java
public class User {
    private int id;
    private String email;
    private String first_name;
    private String last_name;

    // No-arg constructor for Jackson
    public User() {}

    // All-args constructor for convenience
    public User(int id, String email, String first_name, String last_name) {
        this.id = id;
        this.email = email;
        this.first_name = first_name;
        this.last_name = last_name;
    }

    // Getters and setters...
}
```

**Key Learning:**
- **ALWAYS add `@NoArgsConstructor` to POJOs used for deserialization**
- Jackson requires a no-argument constructor to instantiate objects
- Error message "no Creators, like default constructor, exist" = missing no-args constructor
- `@AllArgsConstructor` alone is not sufficient for deserialization

---

## Challenge 3: Field Name Mismatch

**Scenario:** The POJO deserializes without error, but all fields are null.

**Broken Code:**
```java
import lombok.Data;
import lombok.NoArgsConstructor;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.testng.Assert.*;

// JSON Response:
// {
//   "data": {
//     "id": 2,
//     "email": "janet.weaver@reqres.in",
//     "first_name": "Janet",
//     "last_name": "Weaver"
//   }
// }

@Data
@NoArgsConstructor
public class UserData {
    private int id;
    private String email;
    private String firstName;   // Camel case
    private String lastName;    // Camel case
}

public class Challenge3_FieldMismatch {

    @Test
    public void testUserDeserialization() {
        UserData user = given()
            .baseUri("https://reqres.in/api")
        .when()
            .get("/users/2")
        .then()
            .statusCode(200)
            .extract().path("data");

        System.out.println("User ID: " + user.getId());
        System.out.println("User Email: " + user.getEmail());
        System.out.println("First Name: " + user.getFirstName());  // null!
        System.out.println("Last Name: " + user.getLastName());    // null!

        assertNotNull(user.getFirstName(), "First name should not be null");
    }
}
```

**Error Output:**
```
User ID: 2
User Email: janet.weaver@reqres.in
First Name: null
Last Name: null

java.lang.AssertionError: First name should not be null
```

**Your Task:**
1. Why are firstName and lastName null while id and email work?
2. How does Jackson match POJO fields to JSON keys?
3. Fix the POJO without changing field names

**Hints:**
- Compare POJO field names with JSON keys
- There's an annotation to map different names

---

**Solution:**

**Bug Location:** Lines 19-20 - Field names don't match JSON keys

**Explanation:**
Jackson matches POJO field names to JSON keys **exactly**. The JSON has `first_name` and `last_name` (snake_case), but the POJO has `firstName` and `lastName` (camelCase).

- `id` works because JSON has "id" and POJO has "id" ✓
- `email` works because both are "email" ✓
- `firstName` doesn't work because JSON has "first_name" not "firstName" ✗
- `lastName` doesn't work because JSON has "last_name" not "lastName" ✗

**Fix Option 1: Match JSON exactly**
```java
@Data
@NoArgsConstructor
public class UserData {
    private int id;
    private String email;
    private String first_name;  // Match JSON exactly
    private String last_name;   // Match JSON exactly
}
```

**Fix Option 2: Use @JsonProperty annotation (Better)**
```java
import com.fasterxml.jackson.annotation.JsonProperty;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class UserData {
    private int id;
    private String email;

    @JsonProperty("first_name")  // Maps JSON "first_name" to Java "firstName"
    private String firstName;

    @JsonProperty("last_name")   // Maps JSON "last_name" to Java "lastName"
    private String lastName;
}
```

**Key Learning:**
- POJO field names must match JSON keys exactly (case-sensitive)
- Use `@JsonProperty("json_key")` to map different names
- No error is thrown if fields don't match - they're just null
- Always validate deserialized objects to catch mapping issues early

---

## Challenge 4: Null Pointer in Chained Calls

**Scenario:** This workflow creates a user and then tries to update it, but crashes with NullPointerException.

**Broken Code:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Challenge4_NullPointerInChain {

    @Test
    public void testCreateAndUpdateUser() {
        String baseUri = "https://reqres.in/api";

        // Step 1: Create user
        System.out.println("Creating user...");
        String userId = given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body("{\"name\":\"John\",\"job\":\"SDET\"}")
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .body("name", equalTo("John"))
            .extract().path("userId");  // Wrong path!

        System.out.println("Created user with ID: " + userId);

        // Step 2: Update user
        given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body("{\"name\":\"John Updated\",\"job\":\"Senior SDET\"}")
        .when()
            .put("/users/" + userId)  // userId is null!
        .then()
            .statusCode(200);
    }
}
```

**Error Message:**
```
Creating user...
Created user with ID: null

java.lang.NullPointerException: Cannot invoke "String.toString()" because "userId" is null
```

**Your Task:**
1. Why is userId null?
2. What should be the correct path?
3. How can you prevent this in production code?

**Hints:**
- Check the JSON response from POST /users
- Look at the actual field name for ID

---

**Solution:**

**Bug Location:** Line 21 - Wrong extraction path `"userId"`

**Explanation:**
The response from POST /users contains:
```json
{
  "name": "John",
  "job": "SDET",
  "id": "123",        ← Actual field name
  "createdAt": "2024-01-15T12:00:00.000Z"
}
```

The field is named `"id"`, not `"userId"`. Using `.path("userId")` tries to extract a non-existent field, returning null. Then the PUT request tries to use null in the URL: `/users/null`, causing NullPointerException.

**Fixed Code:**
```java
@Test
public void testCreateAndUpdateUser() {
    String baseUri = "https://reqres.in/api";

    // Step 1: Create user
    System.out.println("Creating user...");
    String userId = given()
        .baseUri(baseUri)
        .contentType("application/json")
        .body("{\"name\":\"John\",\"job\":\"SDET\"}")
    .when()
        .post("/users")
    .then()
        .statusCode(201)
        .body("name", equalTo("John"))
        .body("id", notNullValue())  // FIX: Validate ID exists
        .extract().path("id");  // FIX: Correct path

    System.out.println("Created user with ID: " + userId);

    // FIX: Validate extracted value before using
    assertNotNull(userId, "User ID should not be null");

    // Step 2: Update user
    given()
        .baseUri(baseUri)
        .contentType("application/json")
        .body("{\"name\":\"John Updated\",\"job\":\"Senior SDET\"}")
    .when()
        .put("/users/" + userId)
    .then()
        .statusCode(200)
        .body("name", equalTo("John Updated"));

    System.out.println("Updated user successfully");
}
```

**Prevention Best Practices:**
```java
// 1. Validate before extraction
String userId = response
    .then()
    .body("id", notNullValue())  // Fails fast if null
    .extract().path("id");

// 2. Validate after extraction
String userId = response.path("id");
assertNotNull(userId, "User ID must not be null");
assertTrue(userId.length() > 0, "User ID must not be empty");

// 3. Log extracted values
System.out.println("Extracted user ID: " + userId);

// 4. Use Response object to debug
Response response = post("/users").then().extract().response();
System.out.println("Full response: " + response.asString());
String userId = response.path("id");
```

**Key Learning:**
- **Always validate extracted values before using them**
- Use `.log().body()` to see actual response and field names
- Add assertions for extracted values: `assertNotNull()`, `assertTrue()`
- Null values cause cascading failures in chained tests
- Print extracted values to debug chaining issues

---

## Challenge 5: Token Extraction Error

**Scenario:** Login test fails to extract token correctly.

**Broken Code:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class Challenge5_TokenExtraction {

    @Test
    public void testLoginAndExtractToken() {
        // Login response structure:
        // {
        //   "token": "QpwL5tke4Pnpja7X4"
        // }

        String token = given()
            .baseUri("https://reqres.in/api")
            .contentType("application/json")
            .body("{\"email\":\"eve.holt@reqres.in\",\"password\":\"cityslicka\"}")
        .when()
            .post("/login")
        .then()
            .statusCode(200)
            .extract().path("data.token");  // Wrong path!

        System.out.println("Token: " + token);  // null

        // Use token
        given()
            .auth().oauth2(token)  // Fails: token is null
        .when()
            .get("https://reqres.in/api/users/2")
        .then()
            .statusCode(200);
    }
}
```

**Error Message:**
```
Token: null

java.lang.NullPointerException: Cannot invoke "String.toString()" because "token" is null
```

**Your Task:**
1. What's wrong with the extraction path?
2. How to find the correct path?
3. Fix the code

---

**Solution:**

**Bug Location:** Line 21 - Incorrect path `"data.token"`

**Explanation:**
The login response is:
```json
{
  "token": "QpwL5tke4Pnpja7X4"
}
```

The token is at the root level, not nested under "data". Using `.path("data.token")` looks for:
```json
{
  "data": {
    "token": "..."
  }
}
```
which doesn't exist, so it returns null.

**Fixed Code:**
```java
@Test
public void testLoginAndExtractToken() {
    String token = given()
        .baseUri("https://reqres.in/api")
        .contentType("application/json")
        .body("{\"email\":\"eve.holt@reqres.in\",\"password\":\"cityslicka\"}")
        .log().all()  // FIX: Log request
    .when()
        .post("/login")
    .then()
        .statusCode(200)
        .log().body()  // FIX: Log response to see structure
        .body("token", notNullValue())  // FIX: Validate token exists
        .extract().path("token");  // FIX: Correct path (root level)

    System.out.println("Token: " + token);
    assertNotNull(token, "Token should not be null");

    // Use token
    given()
        .auth().oauth2(token)
    .when()
        .get("https://reqres.in/api/users/2")
    .then()
        .statusCode(200);

    System.out.println("✓ Successfully used token for authentication");
}
```

**How to Debug Extraction Issues:**
```java
// Method 1: Log response body
.then()
    .log().body()  // See full JSON structure
    .extract().path("token");

// Method 2: Extract as Response first
Response response = post("/login").then().extract().response();
System.out.println("Full response: " + response.asString());
String token = response.path("token");

// Method 3: Pretty print JSON
System.out.println(response.prettyPrint());
```

**Key Learning:**
- Use `.log().body()` to see actual response structure
- Root-level fields: `.path("fieldName")`
- Nested fields: `.path("parent.child")`
- Array elements: `.path("array[0].field")`
- Always validate extracted values exist before using

---

## Challenge 6: Serialization Type Mismatch

**Scenario:** Creating a user with POJO fails with JSON parsing error.

**Broken Code:**
```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreateUserRequest {
    private String name;
    private String job;
    private String age;  // Should be int!
}

public class Challenge6_SerializationError {

    @Test
    public void testCreateUser() {
        // API expects: {"name":"John","job":"SDET","age":28}
        // But age should be a number, not string

        CreateUserRequest user = new CreateUserRequest("John", "SDET", "28");

        given()
            .baseUri("https://reqres.in/api")
            .contentType("application/json")
            .body(user)
            .log().body()  // Will show: "age":"28" instead of "age":28
        .when()
            .post("/users")
        .then()
            .statusCode(201);
    }
}
```

**API Response:**
```
Status: 400 Bad Request
{
  "error": "age must be a number"
}
```

**Your Task:**
1. What's the type issue?
2. How does it affect JSON serialization?
3. Fix the POJO

---

**Solution:**

**Bug Location:** Line 13 - `age` declared as String instead of int

**Explanation:**
When `age` is declared as `String`, the serialized JSON becomes:
```json
{
  "name": "John",
  "job": "SDET",
  "age": "28"        ← String (quoted)
}
```

But the API expects:
```json
{
  "name": "John",
  "job": "SDET",
  "age": 28          ← Number (not quoted)
}
```

The API validates types and rejects the request with 400 Bad Request.

**Fixed Code:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor
public class CreateUserRequest {
    private String name;
    private String job;
    private int age;  // FIX: Changed from String to int
}

@Test
public void testCreateUser() {
    CreateUserRequest user = new CreateUserRequest("John", "SDET", 28);  // FIX: Pass int, not String

    given()
        .baseUri("https://reqres.in/api")
        .contentType("application/json")
        .body(user)
        .log().body()  // Now shows: "age":28 (number)
    .when()
        .post("/users")
    .then()
        .statusCode(201)
        .body("age", equalTo(28))  // Validate as number
        .log().body();
}
```

**Type Mapping Guide:**

| JSON Type | Java Type |
|-----------|-----------|
| String | `String` |
| Number (integer) | `int`, `Integer`, `long`, `Long` |
| Number (decimal) | `double`, `Double`, `float`, `Float` |
| Boolean | `boolean`, `Boolean` |
| Array | `List<T>`, `T[]` |
| Object | Custom POJO class |
| null | Use wrapper classes (`Integer`, not `int`) |

**Key Learning:**
- POJO field types must match JSON data types
- String "28" ≠ Number 28 in JSON
- Use primitive types (`int`) for required fields
- Use wrapper classes (`Integer`) for optional/nullable fields
- Always check API documentation for expected types

---

## Challenge 7: Complex Chaining Logic Error

**Scenario:** This test creates multiple users and tries to find the user with a specific job, but the logic is flawed.

**Broken Code:**
```java
import org.testng.annotations.Test;
import java.util.List;
import static io.restassured.RestAssured.*;
import static org.testng.Assert.*;

public class Challenge7_ChainingLogicError {

    @Test
    public void testCreateMultipleUsersAndFind() {
        String baseUri = "https://reqres.in/api";

        // Create 3 users
        String[] jobs = {"Developer", "SDET", "Manager"};
        String targetUserId = null;

        for (String job : jobs) {
            String userId = given()
                .baseUri(baseUri)
                .contentType("application/json")
                .body("{\"name\":\"User\",\"job\":\"" + job + "\"}")
            .when()
                .post("/users")
            .then()
                .statusCode(201)
                .extract().path("id");

            System.out.println("Created user with job: " + job + ", ID: " + userId);

            // BUG: This overwrites targetUserId every iteration!
            if (job.equals("SDET")) {
                targetUserId = userId;
            }
        }

        // Find and validate the SDET user
        // BUG: ReqRes doesn't persist data, so this will fail!
        given()
            .baseUri(baseUri)
        .when()
            .get("/users/" + targetUserId)
        .then()
            .statusCode(200)
            .body("data.job", equalTo("SDET"));  // This field doesn't exist in response
    }
}
```

**Issues:**
1. Logic works but API doesn't persist data (ReqRes limitation)
2. Validation expects field that doesn't exist in response
3. No verification that targetUserId was set

**Your Task:**
1. Identify all the issues
2. How would you fix this for a real API?
3. What validations are missing?

---

**Solution:**

**Bugs Identified:**

1. **Line 32-36:** ReqRes API doesn't persist created users, so GET /users/{id} returns existing user, not the created one
2. **Line 39:** Response structure doesn't have "data.job" field
3. **Line 31:** No validation that targetUserId was actually set (could be null if loop fails)

**Explanation:**

ReqRes is a mock API that doesn't persist POST/PUT/DELETE operations. When you create a user with `POST /users`, it returns a fake ID, but that user doesn't actually exist in the database. Subsequent `GET /users/{id}` will fail or return an unrelated existing user.

For the response structure:
- Created user response: `{"name":"User","job":"SDET","id":"123"}`
- GET user response: `{"data":{"id":2,"email":"...","first_name":"..."}}`

The "job" field only exists in the create response, not in the get response.

**Fixed Code (Pattern for Real APIs):**
```java
@Test
public void testCreateMultipleUsersAndFind() {
    String baseUri = "https://reqres.in/api";

    String[] jobs = {"Developer", "SDET", "Manager"};
    String targetUserId = null;
    String targetJob = null;

    // Create users and store SDET user's info
    for (String job : jobs) {
        Response createResponse = given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body("{\"name\":\"User\",\"job\":\"" + job + "\"}")
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .log().body()
            .extract().response();

        String userId = createResponse.path("id");
        String createdJob = createResponse.path("job");

        System.out.println("Created user with job: " + job + ", ID: " + userId);

        // Store SDET user ID
        if (job.equals("SDET")) {
            targetUserId = userId;
            targetJob = createdJob;
        }
    }

    // FIX: Validate that we found the SDET user
    assertNotNull(targetUserId, "SDET user ID should not be null");
    assertNotNull(targetJob, "SDET user job should not be null");
    System.out.println("\nTarget SDET user ID: " + targetUserId);

    // FIX: For ReqRes, validate from the create response
    // In a real API, you would GET the user here
    assertEquals(targetJob, "SDET", "Target user should have job SDET");

    System.out.println("✓ Successfully created and validated SDET user");
}
```

**For a Real Persistent API:**
```java
@Test
public void testCreateMultipleUsersAndFind_RealAPI() {
    String baseUri = "https://real-api.example.com";

    // Create 3 users
    String[] jobs = {"Developer", "SDET", "Manager"};
    List<String> createdUserIds = new ArrayList<>();

    for (String job : jobs) {
        String userId = given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body("{\"name\":\"User\",\"job\":\"" + job + "\"}")
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .extract().path("id");

        createdUserIds.add(userId);
        System.out.println("Created user: " + job + " (ID: " + userId + ")");
    }

    // Find the SDET user (2nd in array)
    String sdETUserId = createdUserIds.get(1);

    // Verify the user exists and has correct job
    given()
        .baseUri(baseUri)
    .when()
        .get("/users/" + sdETUserId)
    .then()
        .statusCode(200)
        .body("job", equalTo("SDET"))  // Validate job field
        .log().body();

    System.out.println("✓ Found and validated SDET user");

    // Cleanup: Delete created users
    for (String userId : createdUserIds) {
        given()
            .baseUri(baseUri)
        .when()
            .delete("/users/" + userId)
        .then()
            .statusCode(204);
    }

    System.out.println("✓ Cleaned up test data");
}
```

**Key Learning:**
- Understand API limitations (ReqRes doesn't persist data)
- Always validate extracted IDs before using them
- Check API documentation for response structure
- For real APIs: create → verify → cleanup
- Store all created IDs for cleanup in teardown
- Use proper data structures (List) to track multiple created resources

---

## 🎯 Debugging Best Practices Checklist

After completing these challenges, you should follow these practices in all API tests:

### Before Running Tests:
- [ ] Check API documentation for request/response structure
- [ ] Verify field names match between JSON and POJOs
- [ ] Ensure POJOs have `@NoArgsConstructor` for deserialization
- [ ] Confirm authentication is properly configured

### During Test Development:
- [ ] Use `.log().all()` to see request/response
- [ ] Validate extracted values are not null
- [ ] Print extracted values to console for debugging
- [ ] Add assertions after extraction: `assertNotNull()`

### When Tests Fail:
- [ ] Read the full error message and stack trace
- [ ] Check status code (401 = auth, 400 = bad request, 404 = not found)
- [ ] Log response body: `.then().log().body()`
- [ ] Verify field paths with `.prettyPrint()`
- [ ] Check POJO field names match JSON exactly

### For Chained Tests:
- [ ] Validate each extracted value before using in next call
- [ ] Log each step with meaningful messages
- [ ] Handle API limitations (persistence, rate limits)
- [ ] Clean up created test data in `@AfterMethod`

---

## 📊 Debugging Challenge Summary

| Challenge | Issue Type | Key Lesson |
|-----------|------------|------------|
| 1 | Auth header missing | Always include Authorization header for protected endpoints |
| 2 | POJO deserialization | POJOs need `@NoArgsConstructor` for Jackson |
| 3 | Field name mismatch | POJO fields must match JSON keys or use `@JsonProperty` |
| 4 | Null in chained calls | Validate extracted values before using them |
| 5 | Wrong extraction path | Use `.log().body()` to see JSON structure |
| 6 | Type mismatch | POJO field types must match JSON data types |
| 7 | Complex chaining logic | Understand API behavior and add proper validations |

---

## 💡 Interview Tip: Talking About Debugging

**When asked: "How do you debug failing API tests?"**

**Answer:**
*"I follow a systematic approach. First, I check the error message and status code - 401 means authentication issue, 400 means bad request, 404 means endpoint not found. I use RestAssured's `.log().all()` to see the full request and response. For extraction issues, I print extracted values and use assertions to validate they're not null before using them in subsequent calls. For POJO issues, I verify field names match the JSON exactly using `.log().body()`, and ensure the class has `@NoArgsConstructor` for deserialization. I also check API documentation to confirm I'm using the correct endpoint and request format. Finally, I add meaningful log statements to trace the test flow, especially in chained API calls where one failure cascades to others."*

---

**Excellent work! You've mastered debugging advanced API test scenarios. Move to Day-3-Project-Guide.md to build a complete E2E framework!** 🚀
