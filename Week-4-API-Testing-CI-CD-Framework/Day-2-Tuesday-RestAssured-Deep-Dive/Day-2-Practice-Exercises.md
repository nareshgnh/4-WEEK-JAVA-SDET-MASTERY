# DAY 2 PRACTICE EXERCISES

## 🎯 Today's Practice Goal

Master request/response specifications, headers, parameters, and advanced JSON validation through hands-on exercises using the JSONPlaceholder API (https://jsonplaceholder.typicode.com).

**API Documentation:** https://jsonplaceholder.typicode.com/guide/

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Query Parameters Practice

**Objective:** Learn to use query parameters for filtering and pagination

**Task:**
1. Get all posts using `/posts` endpoint
2. Filter posts by userId = 1 using query parameter
3. Validate that all returned posts belong to userId 1
4. Verify the response contains 10 posts

**Starter Code:**
```java
import io.restassured.RestAssured;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise1 {

    @Test
    public void testQueryParameters() {
        // TODO: Set base URI to https://jsonplaceholder.typicode.com

        // TODO: GET /posts with query parameter userId=1
        // TODO: Validate status code is 200
        // TODO: Validate all posts have userId = 1
        // TODO: Validate response has exactly 10 posts
    }
}
```

**Expected Output:**
```
Status Code: 200
Response body contains 10 posts
All posts have userId = 1
```

**Hints:**
- Use `.queryParam("userId", 1)` to add query parameter
- Use `.body("size()", equalTo(10))` for array size
- Use `.body("userId", everyItem(equalTo(1)))` to check all items

---

### Exercise 2: Path Parameters Practice

**Objective:** Learn to use path parameters for resource identification

**Task:**
1. Get a specific post with id = 5 using path parameter
2. Validate the post's userId, id, and title
3. Verify the title is not null or empty

**Starter Code:**
```java
@Test
public void testPathParameters() {
    RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

    // TODO: Use path parameter to get post with id=5
    // TODO: Validate status code 200
    // TODO: Validate id equals 5
    // TODO: Validate userId is not null
    // TODO: Validate title is not empty

}
```

**Expected Output:**
```
Status Code: 200
id: 5
userId: 1
title: "nesciunt quas odio"
```

**Hints:**
- Use `.pathParam("postId", 5)` and `.get("/posts/{postId}")`
- Use `notNullValue()` matcher for null checks
- Use `not(emptyString())` for empty string checks

---

### Exercise 3: Multiple Headers Practice

**Objective:** Learn to set and validate multiple headers

**Task:**
1. Make a GET request to `/posts/1`
2. Add multiple headers: Content-Type, Accept, User-Agent
3. Validate the response Content-Type header
4. Log the request and response for debugging

**Starter Code:**
```java
@Test
public void testMultipleHeaders() {
    // TODO: Add headers:
    //   Content-Type: application/json
    //   Accept: application/json
    //   User-Agent: RestAssured-Test

    // TODO: Make GET request to /posts/1
    // TODO: Log request and response
    // TODO: Validate response Content-Type header contains "application/json"
    // TODO: Validate status code 200
}
```

**Expected Output:**
```
Request Headers:
  Content-Type: application/json
  Accept: application/json
  User-Agent: RestAssured-Test

Response Headers:
  Content-Type: application/json; charset=utf-8

Status Code: 200
```

**Hints:**
- Use `.header("Header-Name", "value")` for each header
- Use `.log().all()` for logging
- Response header validation: `.header("Content-Type", containsString("application/json"))`

---

## 💪 Core Practice (30 min)

### Exercise 4: Create Request Specification

**Objective:** Build reusable RequestSpecification to avoid repetition

**Task:**
1. Create a RequestSpecification with:
   - Base URI: https://jsonplaceholder.typicode.com
   - Content-Type: application/json
   - Custom header: X-API-Version: v1
2. Use this spec in 3 different tests:
   - Get all posts
   - Get specific user (id=1)
   - Get comments for post (id=1)
3. Each test should use the same specification

**Starter Code:**
```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.specification.RequestSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;

public class Exercise4 {

    private static RequestSpecification requestSpec;

    @BeforeClass
    public static void setup() {
        // TODO: Create RequestSpecification with:
        //   - baseUri
        //   - contentType
        //   - custom header X-API-Version: v1

    }

    @Test
    public void testGetPosts() {
        // TODO: Use requestSpec to get all posts
        // Validate status code 200
    }

    @Test
    public void testGetUser() {
        // TODO: Use requestSpec to get user with id=1
        // Validate status code 200
        // Validate user name is "Leanne Graham"
    }

    @Test
    public void testGetComments() {
        // TODO: Use requestSpec to get comments for post id=1
        // Use query parameter: postId=1
        // Validate status code 200
    }
}
```

**Expected Output:**
```
All three tests pass using the same request specification
testGetPosts: PASSED
testGetUser: PASSED (name = "Leanne Graham")
testGetComments: PASSED
```

**Hints:**
- Create spec in @BeforeClass: `requestSpec = new RequestSpecBuilder()...build();`
- Use in tests: `given().spec(requestSpec).when().get("/posts")`

---

### Exercise 5: Advanced JSON Path Validation

**Objective:** Master complex JSON path expressions for nested data

**Task:**
1. Get user with id=1 (GET /users/1)
2. Validate nested address fields:
   - address.street is "Kulas Light"
   - address.city is "Gwenborough"
   - address.geo.lat is "-37.3159"
3. Validate nested company fields:
   - company.name contains "Romaguera"
   - company.catchPhrase is not null

**Starter Code:**
```java
@Test
public void testNestedJsonValidation() {
    RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

    // TODO: GET /users/1
    // TODO: Validate status code 200

    // TODO: Validate nested address fields:
    //   - address.street
    //   - address.city
    //   - address.geo.lat

    // TODO: Validate nested company fields:
    //   - company.name contains "Romaguera"
    //   - company.catchPhrase is not null
}
```

**Sample Response Structure:**
```json
{
  "id": 1,
  "name": "Leanne Graham",
  "address": {
    "street": "Kulas Light",
    "city": "Gwenborough",
    "geo": {
      "lat": "-37.3159",
      "lng": "81.1496"
    }
  },
  "company": {
    "name": "Romaguera-Crona",
    "catchPhrase": "Multi-layered client-server neural-net"
  }
}
```

**Expected Output:**
```
✓ address.street = "Kulas Light"
✓ address.city = "Gwenborough"
✓ address.geo.lat = "-37.3159"
✓ company.name contains "Romaguera"
✓ company.catchPhrase is not null
```

**Hints:**
- Use dot notation: `.body("address.city", equalTo("Gwenborough"))`
- For nested objects: `.body("address.geo.lat", equalTo("-37.3159"))`
- Use `containsString()` for partial matches

---

### Exercise 6: Response Extraction and Reuse

**Objective:** Extract data from one response and use in another request

**Task:**
1. Get all users (GET /users)
2. Extract the ID of the first user
3. Use that ID to get all posts by that user
4. Validate the posts belong to the extracted user ID
5. Count and print the number of posts

**Starter Code:**
```java
@Test
public void testResponseExtraction() {
    RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

    // Step 1: Get all users and extract first user's ID
    // TODO: GET /users
    // TODO: Extract first user's id: .extract().path("[0].id")

    // Step 2: Get posts for this user
    // TODO: GET /posts with query param userId = extracted id
    // TODO: Validate all posts have userId matching extracted id
    // TODO: Extract and print the number of posts

}
```

**Expected Output:**
```
Extracted user ID: 1
Number of posts by user 1: 10
All posts belong to user 1: PASSED
```

**Hints:**
- First request: `int userId = get("/users").then().extract().path("[0].id");`
- Second request: `.queryParam("userId", userId)`
- Print: `System.out.println("Posts count: " + response.path("size()"));`

---

## 🚀 Challenge Exercise (15 min)

### Exercise 7: Complete CRUD Test Suite with Specifications

**Objective:** Build a complete CRUD test suite using request/response specifications

**Task:**
Implement a full CRUD test suite for Posts endpoint with proper specifications:

1. **CREATE (POST):**
   - Create new post with title, body, and userId
   - Validate response status 201
   - Extract the created post ID

2. **READ (GET):**
   - Use extracted ID to fetch the post
   - Validate all fields match what was created

3. **UPDATE (PUT):**
   - Update the post's title
   - Validate status 200
   - Validate title is updated

4. **DELETE:**
   - Delete the post
   - Validate status 200

**Requirements:**
- Use RequestSpecification for common settings
- Use ResponseSpecification for common validations
- All tests should be in proper sequence
- Use extracted values between tests

**Starter Code:**
```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.builder.ResponseSpecBuilder;
import io.restassured.http.ContentType;
import io.restassured.specification.RequestSpecification;
import io.restassured.specification.ResponseSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise7_CRUD {

    private static RequestSpecification requestSpec;
    private static ResponseSpecification successResponseSpec;
    private int createdPostId;

    @BeforeClass
    public void setup() {
        // TODO: Create request specification with:
        //   - Base URI
        //   - Content Type JSON

        // TODO: Create response specification with:
        //   - Content Type JSON validation
        //   - Response time < 3000ms
    }

    @Test(priority = 1)
    public void testCreatePost() {
        // TODO: Create new post
        String requestBody = "{\n" +
            "  \"title\": \"Test Post\",\n" +
            "  \"body\": \"This is a test post\",\n" +
            "  \"userId\": 1\n" +
            "}";

        // TODO: POST /posts with body
        // TODO: Validate status code 201
        // TODO: Validate response body contains title and body
        // TODO: Extract and store post id in createdPostId
    }

    @Test(priority = 2)
    public void testGetPost() {
        // TODO: GET /posts/{id} using createdPostId
        // TODO: Validate status code 200
        // TODO: Validate title equals "Test Post"
        // TODO: Validate body equals "This is a test post"
        // TODO: Validate userId equals 1
    }

    @Test(priority = 3)
    public void testUpdatePost() {
        // TODO: Update post title
        String updateBody = "{\n" +
            "  \"id\": " + createdPostId + ",\n" +
            "  \"title\": \"Updated Test Post\",\n" +
            "  \"body\": \"This is a test post\",\n" +
            "  \"userId\": 1\n" +
            "}";

        // TODO: PUT /posts/{id} with updated body
        // TODO: Validate status code 200
        // TODO: Validate title is updated to "Updated Test Post"
    }

    @Test(priority = 4)
    public void testDeletePost() {
        // TODO: DELETE /posts/{id}
        // TODO: Validate status code 200
    }
}
```

**Expected Output:**
```
Test 1 - CREATE: PASSED (Post created with ID: 101)
Test 2 - READ: PASSED (Post retrieved successfully)
Test 3 - UPDATE: PASSED (Title updated to "Updated Test Post")
Test 4 - DELETE: PASSED (Post deleted successfully)

All CRUD operations completed successfully!
```

**Bonus Challenges:**
- [ ] Add response time validation (< 2 seconds)
- [ ] Add logging only for failed tests
- [ ] Create a separate test to verify deleted post returns 404
- [ ] Extract response as POJO (create Post class)

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution

```java
import io.restassured.RestAssured;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise1Solution {

    @Test
    public void testQueryParameters() {
        RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

        given()
            .queryParam("userId", 1)  // Filter by userId=1
        .when()
            .get("/posts")
        .then()
            .statusCode(200)
            .body("size()", equalTo(10))  // Exactly 10 posts
            .body("userId", everyItem(equalTo(1)));  // All have userId=1
    }
}
```

**Explanation:**
1. `.queryParam("userId", 1)` - Adds `?userId=1` to URL
2. `.body("size()", equalTo(10))` - Validates array has 10 elements
3. `.body("userId", everyItem(equalTo(1)))` - Validates every post's userId is 1

**Key Takeaways:**
- Query params filter collections
- `everyItem()` matcher validates all array elements
- `size()` function returns array length

---

### Exercise 2 Solution

```java
@Test
public void testPathParameters() {
    RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

    given()
        .pathParam("postId", 5)
    .when()
        .get("/posts/{postId}")
    .then()
        .statusCode(200)
        .body("id", equalTo(5))
        .body("userId", notNullValue())
        .body("title", not(emptyString()))
        .body("title", equalTo("nesciunt quas odio"));
}
```

**Explanation:**
1. `.pathParam("postId", 5)` - Replaces `{postId}` in URL with 5
2. URL becomes `/posts/5`
3. `notNullValue()` - Ensures field exists and is not null
4. `not(emptyString())` - Ensures string is not empty

**Key Takeaways:**
- Path params identify specific resources
- Use `{paramName}` in URL and `.pathParam()` to set value
- Multiple null/empty checks ensure data quality

---

### Exercise 3 Solution

```java
@Test
public void testMultipleHeaders() {
    RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

    given()
        .header("Content-Type", "application/json")
        .header("Accept", "application/json")
        .header("User-Agent", "RestAssured-Test")
        .log().all()  // Log request details
    .when()
        .get("/posts/1")
    .then()
        .log().all()  // Log response details
        .statusCode(200)
        .header("Content-Type", containsString("application/json"));
}
```

**Explanation:**
1. `.header("name", "value")` - Sets request header
2. `.log().all()` in given() - Logs request (method, URL, headers, body)
3. `.log().all()` in then() - Logs response (status, headers, body)
4. `.header()` in then() - Validates response header

**Key Takeaways:**
- Chain multiple `.header()` calls for multiple headers
- Logging helps debug API issues
- Response headers can be validated like body fields

---

### Exercise 4 Solution

```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.http.ContentType;
import io.restassured.specification.RequestSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise4Solution {

    private static RequestSpecification requestSpec;

    @BeforeClass
    public static void setup() {
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://jsonplaceholder.typicode.com")
            .setContentType(ContentType.JSON)
            .addHeader("X-API-Version", "v1")
            .build();
    }

    @Test
    public void testGetPosts() {
        given()
            .spec(requestSpec)
        .when()
            .get("/posts")
        .then()
            .statusCode(200)
            .body("size()", greaterThan(0));
    }

    @Test
    public void testGetUser() {
        given()
            .spec(requestSpec)
        .when()
            .get("/users/1")
        .then()
            .statusCode(200)
            .body("name", equalTo("Leanne Graham"));
    }

    @Test
    public void testGetComments() {
        given()
            .spec(requestSpec)
            .queryParam("postId", 1)
        .when()
            .get("/comments")
        .then()
            .statusCode(200)
            .body("size()", greaterThan(0))
            .body("postId", everyItem(equalTo(1)));
    }
}
```

**Explanation:**
1. `RequestSpecBuilder()` - Builder pattern to create specification
2. `.setBaseUri()` - Sets base URL once for all tests
3. `.addHeader()` - Adds custom header to all requests
4. `.build()` - Finalizes and returns RequestSpecification object
5. `.spec(requestSpec)` - Applies all settings from specification

**Key Takeaways:**
- RequestSpec eliminates repetition across tests
- Created once in @BeforeClass, used in all tests
- Easy to maintain - change once, applies everywhere
- Can still add test-specific params (query params, path params)

---

### Exercise 5 Solution

```java
@Test
public void testNestedJsonValidation() {
    RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

    given()
    .when()
        .get("/users/1")
    .then()
        .statusCode(200)
        // Validate nested address fields
        .body("address.street", equalTo("Kulas Light"))
        .body("address.city", equalTo("Gwenborough"))
        .body("address.geo.lat", equalTo("-37.3159"))
        // Validate nested company fields
        .body("company.name", containsString("Romaguera"))
        .body("company.catchPhrase", notNullValue())
        .body("company.catchPhrase", not(emptyString()));
}
```

**Explanation:**
1. Dot notation navigates nested objects: `address.city`
2. Multiple levels: `address.geo.lat` goes 3 levels deep
3. `containsString()` - Partial string match
4. `notNullValue()` - Field exists and not null

**Key Takeaways:**
- Use dot notation for nested objects
- Can go multiple levels deep (address.geo.lat)
- Combine multiple matchers for thorough validation
- JSON path expressions work same for nested objects and arrays

---

### Exercise 6 Solution

```java
import io.restassured.response.Response;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise6Solution {

    @Test
    public void testResponseExtraction() {
        RestAssured.baseURI = "https://jsonplaceholder.typicode.com";

        // Step 1: Get all users and extract first user's ID
        int userId = given()
            .when()
                .get("/users")
            .then()
                .statusCode(200)
                .extract().path("[0].id");  // Extract first user's ID

        System.out.println("Extracted user ID: " + userId);

        // Step 2: Get posts for this user
        Response response = given()
            .queryParam("userId", userId)
        .when()
            .get("/posts")
        .then()
            .statusCode(200)
            .body("userId", everyItem(equalTo(userId)))  // All posts belong to user
            .extract().response();

        // Extract and print count
        int postCount = response.path("size()");
        System.out.println("Number of posts by user " + userId + ": " + postCount);
    }
}
```

**Explanation:**
1. `.extract().path("[0].id")` - Extracts 'id' field from first array element
2. Store in variable: `int userId = ...`
3. Use in next request: `.queryParam("userId", userId)`
4. `.extract().response()` - Gets entire response object
5. `response.path("size()")` - Extract array size from response

**Key Takeaways:**
- Extraction enables chaining API calls
- `.extract().path()` for single values
- `.extract().response()` for multiple values
- Mimics real-world scenarios (create → get → update → delete)

---

### Exercise 7 Solution

```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.builder.ResponseSpecBuilder;
import io.restassured.http.ContentType;
import io.restassured.specification.RequestSpecification;
import io.restassured.specification.ResponseSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;
import static java.util.concurrent.TimeUnit.MILLISECONDS;

public class Exercise7Solution {

    private static RequestSpecification requestSpec;
    private static ResponseSpecification successResponseSpec;
    private int createdPostId;

    @BeforeClass
    public void setup() {
        // Request specification - common request settings
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://jsonplaceholder.typicode.com")
            .setContentType(ContentType.JSON)
            .addHeader("Accept", "application/json")
            .build();

        // Response specification - common validations
        successResponseSpec = new ResponseSpecBuilder()
            .expectContentType(ContentType.JSON)
            .expectResponseTime(lessThan(3000L), MILLISECONDS)
            .build();
    }

    @Test(priority = 1)
    public void testCreatePost() {
        String requestBody = "{\n" +
            "  \"title\": \"Test Post\",\n" +
            "  \"body\": \"This is a test post\",\n" +
            "  \"userId\": 1\n" +
            "}";

        createdPostId = given()
            .spec(requestSpec)
            .body(requestBody)
        .when()
            .post("/posts")
        .then()
            .log().ifValidationFails()
            .statusCode(201)  // Created
            .spec(successResponseSpec)
            .body("title", equalTo("Test Post"))
            .body("body", equalTo("This is a test post"))
            .body("userId", equalTo(1))
            .extract().path("id");

        System.out.println("Created post with ID: " + createdPostId);
    }

    @Test(priority = 2)
    public void testGetPost() {
        given()
            .spec(requestSpec)
            .pathParam("id", createdPostId)
        .when()
            .get("/posts/{id}")
        .then()
            .log().ifValidationFails()
            .statusCode(200)
            .spec(successResponseSpec)
            .body("id", equalTo(createdPostId))
            .body("title", equalTo("Test Post"))
            .body("body", equalTo("This is a test post"))
            .body("userId", equalTo(1));

        System.out.println("Successfully retrieved post ID: " + createdPostId);
    }

    @Test(priority = 3)
    public void testUpdatePost() {
        String updateBody = "{\n" +
            "  \"id\": " + createdPostId + ",\n" +
            "  \"title\": \"Updated Test Post\",\n" +
            "  \"body\": \"This is a test post\",\n" +
            "  \"userId\": 1\n" +
            "}";

        given()
            .spec(requestSpec)
            .pathParam("id", createdPostId)
            .body(updateBody)
        .when()
            .put("/posts/{id}")
        .then()
            .log().ifValidationFails()
            .statusCode(200)
            .spec(successResponseSpec)
            .body("title", equalTo("Updated Test Post"))
            .body("id", equalTo(createdPostId));

        System.out.println("Successfully updated post ID: " + createdPostId);
    }

    @Test(priority = 4)
    public void testDeletePost() {
        given()
            .spec(requestSpec)
            .pathParam("id", createdPostId)
        .when()
            .delete("/posts/{id}")
        .then()
            .log().ifValidationFails()
            .statusCode(200);

        System.out.println("Successfully deleted post ID: " + createdPostId);
    }
}
```

**Explanation:**
1. **RequestSpec** - Holds base URI, content type, headers (DRY)
2. **ResponseSpec** - Validates content type and response time automatically
3. **Test Priority** - `priority = 1,2,3,4` ensures execution order
4. **Extraction** - `createdPostId` stored and reused across tests
5. **Conditional Logging** - `.log().ifValidationFails()` logs only failures
6. **Complete CRUD** - Create → Read → Update → Delete flow

**Key Takeaways:**
- Specifications make tests clean and maintainable
- Instance variables share data between tests
- Test priorities control execution order
- Complete CRUD suite tests all operations
- Real-world pattern used in production frameworks

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Query Params | ☐ | __/10 | ___ min | |
| 2 - Path Params | ☐ | __/10 | ___ min | |
| 3 - Headers | ☐ | __/10 | ___ min | |
| 4 - Request Spec | ☐ | __/10 | ___ min | |
| 5 - Nested JSON | ☐ | __/10 | ___ min | |
| 6 - Extraction | ☐ | __/10 | ___ min | |
| 7 - CRUD Suite | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
-
-

**Areas I excelled at:**
-
-

**Concepts to review:**
-
-

---

## ✅ Self-Check Question Answers

From Concept Guide:

1. **What is the benefit of using RequestSpecification over setting headers in every test?**
   - **Answer:** Eliminates repetition (DRY), centralizes configuration, easier to maintain (change once applies everywhere), cleaner test code.

2. **Write the URL that results from: `.pathParam("id", 5).queryParam("status", "active").get("/users/{id}")`**
   - **Answer:** `/users/5?status=active`

3. **What's the JSON Path expression to get the city from: `{"user": {"address": {"city": "NYC"}}}`?**
   - **Answer:** `user.address.city`

4. **Why do POST requests typically need a Content-Type header?**
   - **Answer:** Tells the server what format the request body is in (JSON, XML, etc.) so it knows how to parse it.

5. **How would you extract and reuse a user ID from a POST response?**
   - **Answer:**
   ```java
   int userId = given().body(newUser).post("/users")
                .then().extract().path("id");
   // Use in next request
   given().pathParam("id", userId).get("/users/{id}");
   ```

---

## 🎯 What You Accomplished Today

**Congratulations!** You completed:
- ✅ 7 progressive API testing exercises
- ✅ Mastered query and path parameters
- ✅ Built reusable request/response specifications
- ✅ Validated complex nested JSON
- ✅ Extracted and reused API response data
- ✅ Created complete CRUD test suite

**You're now ready for Day 2 debugging challenges and the project!**

---

## 🚀 Next Steps

1. Move to **Day-2-Debug-Practice.md** - Fix broken API tests
2. Then tackle **Day-2-Project-Guide.md** - Build enhanced API framework
3. Keep **Day-2-Cheat-Sheet.md** handy for quick reference

**Tomorrow (Day 3):** Serialization/Deserialization with POJOs and data-driven testing!
