# DAY 1 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master RestAssured syntax by testing a real public API (JSONPlaceholder) and build confidence in writing GET, POST, PUT, and DELETE API tests.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Setup RestAssured Project
**Objective:** Create a Maven project with RestAssured dependencies

**Task:**
1. Create a new Maven project in IntelliJ IDEA
2. Add RestAssured dependency to pom.xml
3. Create a test class and verify setup with a simple GET request

**Starter Code:**
```xml
<!-- Add to pom.xml -->
<dependencies>
    <dependency>
        <groupId>io.rest-assured</groupId>
        <artifactId>rest-assured</artifactId>
        <version>5.3.2</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.testng</groupId>
        <artifactId>testng</artifactId>
        <version>7.8.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class APIWarmUp {
    @Test
    public void testSetup() {
        // Your code here - make a GET request to:
        // https://jsonplaceholder.typicode.com/users/1
        // Verify status code is 200

    }
}
```

**Expected Output:**
```
Test should pass with status 200 OK
```

**Hints:**
- Use `given().when().then()` pattern
- Use `.statusCode(200)` for assertion
- Don't forget static imports

---

### Exercise 2: Simple GET Request
**Objective:** Fetch a single user and validate response

**Task:**
Write a test that fetches user with ID 1 from JSONPlaceholder API and validates:
- Status code is 200
- User name is "Leanne Graham"

**Starter Code:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class GetRequestTest {
    @Test
    public void getSingleUser() {
        // TODO: GET request to https://jsonplaceholder.typicode.com/users/1
        // Validate status code and name

    }
}
```

**Expected Output:**
```
Status code: 200
Response body:
{
  "id": 1,
  "name": "Leanne Graham",
  "username": "Bret",
  "email": "Sincere@april.biz",
  ...
}
```

**Hints:**
- Use `.body("name", equalTo("Leanne Graham"))` for validation
- Import `org.hamcrest.Matchers.*` for matchers

---

### Exercise 3: Validate Multiple Fields
**Objective:** Learn to validate multiple response fields

**Task:**
Fetch user 1 and validate:
- id = 1
- name = "Leanne Graham"
- email contains "@"
- username is not null

**Starter Code:**
```java
@Test
public void validateMultipleFields() {
    given()
        .baseUri("https://jsonplaceholder.typicode.com")
    .when()
        .get("/users/1")
    .then()
        .statusCode(200)
        // TODO: Add validations for name, email, username
        ;
}
```

**Hints:**
- Chain multiple `.body()` assertions
- Use `containsString("@")` for email validation
- Use `notNullValue()` for username

---

## 💪 Core Practice (30 min)

### Exercise 4: GET All Users and Validate Collection
**Objective:** Work with collections in API responses

**Problem Statement:**
Fetch all users from the API and validate:
- Status code is 200
- Response is a list with 10 users
- First user's name is "Leanne Graham"
- All users have non-null email addresses

**Requirements:**
- Use endpoint: `/users`
- Validate collection size
- Validate nested properties

**Starter Code:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class CollectionValidationTest {

    @Test
    public void getAllUsers() {
        String baseURI = "https://jsonplaceholder.typicode.com";

        given()
            .baseUri(baseURI)
        .when()
            // TODO: GET request to /users
        .then()
            // TODO: Validate status code
            // TODO: Validate there are 10 users
            // TODO: Validate first user name
            // TODO: Validate all emails are not null
            ;
    }
}
```

**Test Cases:**
```
Input: GET /users
Expected Output:
- Status: 200
- Array size: 10
- users[0].name: "Leanne Graham"
- All emails: not null
```

**Hints:**
- Use `.body("$", hasSize(10))` to check array size
- Use `.body("[0].name", equalTo("Leanne Graham"))` for first element
- Use `.body("email", everyItem(notNullValue()))` for all emails

---

### Exercise 5: POST Request - Create New Resource
**Objective:** Learn to send POST requests with JSON body

**Problem Statement:**
Create a new post using the JSONPlaceholder API. Send a POST request with:
- title: "My First API Test"
- body: "This is created via RestAssured"
- userId: 1

Validate that:
- Status code is 201 (Created)
- Response contains the same title and body
- Response includes an auto-generated id

**Requirements:**
- Use endpoint: POST `/posts`
- Set Content-Type header to "application/json"
- Send request body as JSON string

**Starter Code:**
```java
@Test
public void createNewPost() {
    String requestBody = "{ \"title\": \"My First API Test\", " +
                        "\"body\": \"This is created via RestAssured\", " +
                        "\"userId\": 1 }";

    given()
        .baseUri("https://jsonplaceholder.typicode.com")
        // TODO: Add Content-Type header
        // TODO: Add request body
    .when()
        // TODO: POST to /posts
    .then()
        // TODO: Validate status code 201
        // TODO: Validate response title
        // TODO: Validate response body field
        // TODO: Validate id is not null
        ;
}
```

**Test Cases:**
```
Input:
  POST /posts
  Body: {"title": "My First API Test", "body": "This is created via RestAssured", "userId": 1}

Expected Output:
  Status: 201
  Response: {"id": 101, "title": "My First API Test", "body": "This is created via RestAssured", "userId": 1}
```

**Hints:**
- Use `.header("Content-Type", "application/json")`
- Use `.body(requestBody)`
- Validate with `.body("id", notNullValue())`

---

### Exercise 6: PUT Request - Update Resource
**Objective:** Learn to update resources using PUT

**Problem Statement:**
Update post with id=1 by sending a PUT request with:
- title: "Updated Title"
- body: "Updated Body"
- userId: 1

Validate:
- Status code is 200
- Response shows updated title and body

**Starter Code:**
```java
@Test
public void updatePost() {
    int postId = 1;
    String updatedData = "{ \"title\": \"Updated Title\", " +
                        "\"body\": \"Updated Body\", " +
                        "\"userId\": 1 }";

    given()
        .baseUri("https://jsonplaceholder.typicode.com")
        .header("Content-Type", "application/json")
        .body(updatedData)
    .when()
        // TODO: PUT to /posts/{postId}
    .then()
        // TODO: Validate status code
        // TODO: Validate updated title
        // TODO: Validate updated body
        ;
}
```

**Hints:**
- Use `.put("/posts/" + postId)`
- Or use path parameter: `.put("/posts/{id}", postId)`

---

## 🚀 Challenge Exercise (15 min)

### Exercise 7: Complete CRUD Test Suite
**Objective:** Build a complete test suite that performs all CRUD operations

**Requirements:**
Create a test class with 4 tests:
1. **testCreatePost** - Create a new post (POST)
2. **testGetPost** - Retrieve the created post (GET)
3. **testUpdatePost** - Update the post (PUT)
4. **testDeletePost** - Delete the post (DELETE)

For the DELETE test, validate status code 200 (JSONPlaceholder returns 200 for DELETE).

**Starter Code:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class CRUDTestSuite {

    private String baseURI = "https://jsonplaceholder.typicode.com";

    @Test(priority = 1)
    public void testCreatePost() {
        // TODO: Create a new post
        // Validate status 201 and response
    }

    @Test(priority = 2)
    public void testGetPost() {
        // TODO: GET post with id=1
        // Validate status 200 and data
    }

    @Test(priority = 3)
    public void testUpdatePost() {
        // TODO: Update post with id=1
        // Validate status 200 and updated data
    }

    @Test(priority = 4)
    public void testDeletePost() {
        // TODO: DELETE post with id=1
        // Validate status 200
    }
}
```

**Testing Instructions:**
1. Run all tests in sequence using TestNG priority
2. All tests should pass
3. Check console for request/response logs

**Bonus Challenges:**
- [ ] Add `.log().all()` to see request and response details
- [ ] Validate response time is under 2 seconds using `.time(lessThan(2000L))`
- [ ] Extract id from create response and use it in subsequent tests

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;

public class APIWarmUp {
    @Test
    public void testSetup() {
        given()
            .baseUri("https://jsonplaceholder.typicode.com")
        .when()
            .get("/users/1")
        .then()
            .statusCode(200);
    }
}
```

**Explanation:**
- `given()` - Setup section (preconditions)
- `baseUri()` - Sets the base URL
- `when().get()` - Performs GET request
- `then().statusCode(200)` - Asserts status code is 200

**Key Takeaways:**
- Always import RestAssured static methods for clean syntax
- BDD-style: given-when-then pattern makes tests readable
- No need for explicit Response object for simple validations

---

### Exercise 2 Solution
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class GetRequestTest {
    @Test
    public void getSingleUser() {
        given()
            .baseUri("https://jsonplaceholder.typicode.com")
        .when()
            .get("/users/1")
        .then()
            .statusCode(200)
            .body("name", equalTo("Leanne Graham"));
    }
}
```

**Explanation:**
- `.body("name", equalTo("Leanne Graham"))` uses JSON path to extract `name` field
- `equalTo()` is a Hamcrest matcher that checks exact equality
- RestAssured automatically parses JSON response

**Key Takeaways:**
- JSON path expressions access nested fields easily
- Hamcrest matchers provide readable assertions
- Multiple assertions can be chained

---

### Exercise 3 Solution
```java
@Test
public void validateMultipleFields() {
    given()
        .baseUri("https://jsonplaceholder.typicode.com")
    .when()
        .get("/users/1")
    .then()
        .statusCode(200)
        .body("id", equalTo(1))
        .body("name", equalTo("Leanne Graham"))
        .body("email", containsString("@"))
        .body("username", notNullValue());
}
```

**Explanation:**
- Each `.body()` call adds another assertion
- `containsString()` checks if string contains substring
- `notNullValue()` ensures field exists and is not null
- All assertions are evaluated even if one fails (soft assertions within same then() block)

**Key Takeaways:**
- Chain multiple validations for comprehensive testing
- Use appropriate matchers for different validation types
- RestAssured provides clear error messages when assertions fail

---

### Exercise 4 Solution
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class CollectionValidationTest {

    @Test
    public void getAllUsers() {
        String baseURI = "https://jsonplaceholder.typicode.com";

        given()
            .baseUri(baseURI)
        .when()
            .get("/users")
        .then()
            .statusCode(200)
            .body("$", hasSize(10))                        // Array size
            .body("[0].name", equalTo("Leanne Graham"))    // First element
            .body("email", everyItem(notNullValue()));     // All emails
    }
}
```

**Explanation:**
- `"$"` refers to root of JSON (the array itself)
- `hasSize(10)` validates array contains exactly 10 elements
- `"[0].name"` accesses first element's name field using array index
- `everyItem(notNullValue())` iterates through all elements and checks each email

**Key Takeaways:**
- RestAssured handles arrays and collections elegantly
- Can validate individual elements or all elements in collection
- JSON path supports array indexing and iteration

---

### Exercise 5 Solution
```java
@Test
public void createNewPost() {
    String requestBody = "{ \"title\": \"My First API Test\", " +
                        "\"body\": \"This is created via RestAssured\", " +
                        "\"userId\": 1 }";

    given()
        .baseUri("https://jsonplaceholder.typicode.com")
        .header("Content-Type", "application/json")
        .body(requestBody)
    .when()
        .post("/posts")
    .then()
        .statusCode(201)
        .body("title", equalTo("My First API Test"))
        .body("body", equalTo("This is created via RestAssured"))
        .body("userId", equalTo(1))
        .body("id", notNullValue());
}
```

**Explanation:**
- POST requests typically require Content-Type header
- `.body(requestBody)` sends the JSON string in request body
- `.post("/posts")` specifies HTTP POST method
- Status 201 indicates resource was created successfully
- Server returns the created object with an auto-generated id

**Key Takeaways:**
- POST creates new resources
- Always validate both status code AND response body
- JSONPlaceholder is a mock API, doesn't actually persist data

---

### Exercise 6 Solution
```java
@Test
public void updatePost() {
    int postId = 1;
    String updatedData = "{ \"title\": \"Updated Title\", " +
                        "\"body\": \"Updated Body\", " +
                        "\"userId\": 1 }";

    given()
        .baseUri("https://jsonplaceholder.typicode.com")
        .header("Content-Type", "application/json")
        .body(updatedData)
    .when()
        .put("/posts/" + postId)
    .then()
        .statusCode(200)
        .body("title", equalTo("Updated Title"))
        .body("body", equalTo("Updated Body"))
        .body("id", equalTo(postId));
}
```

**Explanation:**
- PUT replaces entire resource with new data
- Must include all required fields in request body
- Path includes resource ID: `/posts/1`
- Status 200 indicates successful update
- Response should reflect the updated values

**Alternative with path parameter:**
```java
.put("/posts/{id}", postId)
```

**Key Takeaways:**
- PUT vs POST: PUT updates existing, POST creates new
- PUT is idempotent (same result if called multiple times)
- Always validate the response matches what you sent

---

### Exercise 7 Solution
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class CRUDTestSuite {

    private String baseURI = "https://jsonplaceholder.typicode.com";

    @Test(priority = 1)
    public void testCreatePost() {
        String newPost = "{\"title\": \"Test Post\", \"body\": \"Test Body\", \"userId\": 1}";

        given()
            .baseUri(baseURI)
            .header("Content-Type", "application/json")
            .body(newPost)
        .when()
            .post("/posts")
        .then()
            .statusCode(201)
            .body("title", equalTo("Test Post"))
            .body("id", notNullValue())
            .log().all();  // Logs response for verification
    }

    @Test(priority = 2)
    public void testGetPost() {
        given()
            .baseUri(baseURI)
        .when()
            .get("/posts/1")
        .then()
            .statusCode(200)
            .body("id", equalTo(1))
            .body("userId", equalTo(1))
            .body("title", notNullValue());
    }

    @Test(priority = 3)
    public void testUpdatePost() {
        String updatedPost = "{\"id\": 1, \"title\": \"Updated\", \"body\": \"Updated Body\", \"userId\": 1}";

        given()
            .baseUri(baseURI)
            .header("Content-Type", "application/json")
            .body(updatedPost)
        .when()
            .put("/posts/1")
        .then()
            .statusCode(200)
            .body("title", equalTo("Updated"))
            .body("body", equalTo("Updated Body"));
    }

    @Test(priority = 4)
    public void testDeletePost() {
        given()
            .baseUri(baseURI)
        .when()
            .delete("/posts/1")
        .then()
            .statusCode(200);  // JSONPlaceholder returns 200 for DELETE
    }
}
```

**Explanation:**
- `priority` ensures tests run in order
- Complete CRUD cycle: Create → Read → Update → Delete
- Each test is independent (can run alone)
- `.log().all()` helps debug by showing request/response

**Bonus Solution - Extract and Reuse ID:**
```java
public class CRUDTestSuiteAdvanced {
    private String baseURI = "https://jsonplaceholder.typicode.com";
    private int createdPostId;

    @Test(priority = 1)
    public void testCreatePost() {
        String newPost = "{\"title\": \"Test Post\", \"body\": \"Test Body\", \"userId\": 1}";

        createdPostId = given()
            .baseUri(baseURI)
            .header("Content-Type", "application/json")
            .body(newPost)
        .when()
            .post("/posts")
        .then()
            .statusCode(201)
            .extract().path("id");  // Extract id from response

        System.out.println("Created post with ID: " + createdPostId);
    }

    @Test(priority = 2, dependsOnMethods = "testCreatePost")
    public void testGetPost() {
        given()
            .baseUri(baseURI)
        .when()
            .get("/posts/" + createdPostId)
        .then()
            .statusCode(200)
            .body("id", equalTo(createdPostId));
    }
}
```

**Key Takeaways:**
- CRUD operations are the foundation of API testing
- Use TestNG features (priority, dependsOnMethods) to control test flow
- Extracting values from responses enables dynamic testing
- Real APIs would require authentication and have different status codes

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Setup | ☐ | __/10 | ___ min | |
| 2 - Simple GET | ☐ | __/10 | ___ min | |
| 3 - Multiple Fields | ☐ | __/10 | ___ min | |
| 4 - Collections | ☐ | __/10 | ___ min | |
| 5 - POST Request | ☐ | __/10 | ___ min | |
| 6 - PUT Request | ☐ | __/10 | ___ min | |
| 7 - CRUD Suite | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
-
-

**Areas I excelled at:**
-
-

**Questions for further study:**
-
-

---

## 📝 Self-Check Answers

1. **What is the difference between REST and SOAP?**
   - REST uses HTTP methods and typically JSON; SOAP uses XML and can use various protocols. REST is lighter, faster, and more common in modern applications.

2. **Which HTTP method would you use to create a new user? Update their email? Delete them?**
   - Create: POST
   - Update email: PATCH (or PUT if sending entire object)
   - Delete: DELETE

3. **What does status code 404 mean? What about 201?**
   - 404: Resource not found (client error)
   - 201: Resource created successfully (success)

4. **How is RestAssured's syntax different from Python's requests library?**
   - RestAssured uses fluent BDD-style (given-when-then), Python requests is procedural. RestAssured has built-in assertions, Python requires manual asserts.

5. **Predict the output: `given().when().get("/users/999999").then().statusCode(?)`**
   - Status code 404 (Not Found) - the user doesn't exist

---

## 🎯 Next Steps

Congratulations! You've completed Day 1 Practice. You can now:
- ✅ Make GET, POST, PUT, DELETE requests
- ✅ Validate status codes and response bodies
- ✅ Use Hamcrest matchers for assertions
- ✅ Work with JSONPlaceholder API

**Move on to:**
- Day-1-Debug-Practice.md - Fix broken API tests
- Day-1-Project-Guide.md - Build a complete API test suite

**Pro Tip:** Before moving on, try modifying the exercises to test different endpoints from JSONPlaceholder:
- `/users` - Users
- `/posts` - Blog posts
- `/comments` - Comments
- `/albums` - Photo albums
- `/todos` - Todo items

This will reinforce your learning and build muscle memory!
