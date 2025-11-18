# DAY 3 PRACTICE EXERCISES

## 🎯 Today's Practice Goal

Master authentication, POJOs, response extraction, and API call chaining through progressive hands-on exercises. By the end, you'll build complete end-to-end workflows that mirror real-world API testing scenarios.

**APIs Used:**
- **ReqRes API:** https://reqres.in/ (authentication, CRUD operations)
- **JSONPlaceholder:** https://jsonplaceholder.typicode.com (practice chaining)
- **HTTPBin:** https://httpbin.org (authentication testing)

---

## 🏃 Warm-Up Exercises (20 min)

### Exercise 1: Basic Authentication

**Objective:** Learn to implement Basic Auth in RestAssured

**Task:**
1. Test HTTPBin's basic auth endpoint
2. Use username: `admin` and password: `secret`
3. Validate that authentication succeeds
4. Try with wrong credentials and expect 401

**Starter Code:**
```java
import io.restassured.RestAssured;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise1_BasicAuth {

    @Test
    public void testBasicAuthSuccess() {
        // TODO: Make GET request to https://httpbin.org/basic-auth/admin/secret
        // TODO: Use .auth().basic("admin", "secret")
        // TODO: Validate status code 200
        // TODO: Validate body contains "authenticated": true
    }

    @Test
    public void testBasicAuthFailure() {
        // TODO: Make GET request with wrong credentials
        // TODO: Expect status code 401 (Unauthorized)
    }
}
```

**Expected Output:**
```
Test 1: Status 200, authenticated: true
Test 2: Status 401 (Unauthorized)
```

**Hints:**
- Use `.auth().basic(username, password)` before `.when()`
- Use `.body("authenticated", equalTo(true))` for validation
- For failure test, use wrong password and expect `.statusCode(401)`

---

### Exercise 2: Bearer Token Authentication

**Objective:** Get a token via login and use it in subsequent requests

**Task:**
1. Login to ReqRes API to get a token
2. Extract the token from response
3. Print the token
4. Use the token to access a user endpoint (simulate protected resource)

**Starter Code:**
```java
public class Exercise2_BearerToken {

    @Test
    public void testBearerTokenFlow() {
        String baseUri = "https://reqres.in/api";

        // Step 1: Login to get token
        // TODO: POST to /login with body:
        // {"email": "eve.holt@reqres.in", "password": "cityslicka"}
        // TODO: Extract token from response using .extract().path("token")

        // TODO: Print the token

        // Step 2: Use token to access user endpoint
        // TODO: GET /users/2 with Bearer token
        // TODO: Use .auth().oauth2(token)
        // TODO: Validate status code 200
    }
}
```

**Expected Output:**
```
Login successful
Token: QpwL5tke4Pnpja7X4
Accessed protected resource with token successfully
```

**Hints:**
- Use `.contentType("application/json")` for POST request
- Extract token: `.extract().path("token")`
- Use token: `.auth().oauth2(token)`
- Store token in a String variable

---

### Exercise 3: Create POJO and Serialize

**Objective:** Create a User POJO and use it to send JSON request

**Task:**
1. Create a `User` POJO class with fields: `name` and `job`
2. Add Lombok annotations: `@Data`, `@AllArgsConstructor`, `@NoArgsConstructor`
3. Create a User object and POST it to ReqRes API
4. Validate the response contains the same name and job
5. Extract and print the created user's ID

**Starter Code:**
```java
// TODO: Create User.java class
// import lombok.Data;
// import lombok.AllArgsConstructor;
// import lombok.NoArgsConstructor;
//
// @Data
// @AllArgsConstructor
// @NoArgsConstructor
// public class User {
//     private String name;
//     private String job;
// }

public class Exercise3_POJOSerialization {

    @Test
    public void testCreateUserWithPOJO() {
        String baseUri = "https://reqres.in/api";

        // TODO: Create User object with name="morpheus" and job="leader"

        // TODO: POST to /users with User object as body
        // TODO: Validate status code 201
        // TODO: Validate response body "name" equals "morpheus"
        // TODO: Validate response body "job" equals "leader"
        // TODO: Extract and print the "id" from response
    }
}
```

**Expected Output:**
```
Created user with ID: 123
Name: morpheus
Job: leader
```

**Hints:**
- Add Lombok dependency to `pom.xml` first
- Create User: `User user = new User("morpheus", "leader");`
- Use `.body(user)` to serialize automatically
- Extract ID: `.extract().path("id")`

---

## 💪 Core Practice (40 min)

### Exercise 4: Deserialization - JSON to POJO

**Objective:** Deserialize API response to Java objects and validate using POJOs

**Task:**
1. Create POJOs for ReqRes `/users/2` endpoint response
2. Make GET request and deserialize to POJO
3. Validate fields using Java assertions (not Hamcrest)
4. Print user details from the POJO

**Response Structure:**
```json
{
  "data": {
    "id": 2,
    "email": "janet.weaver@reqres.in",
    "first_name": "Janet",
    "last_name": "Weaver",
    "avatar": "https://reqres.in/img/faces/2-image.jpg"
  },
  "support": {
    "url": "https://reqres.in/#support-heading",
    "text": "To keep ReqRes free, contributions towards server costs are appreciated!"
  }
}
```

**Starter Code:**
```java
import lombok.Data;
import lombok.NoArgsConstructor;
import static org.testng.Assert.*;

// TODO: Create UserResponse POJO
// @Data
// @NoArgsConstructor
// public class UserResponse {
//     private UserData data;
//     private Support support;
// }

// TODO: Create UserData POJO
// @Data
// @NoArgsConstructor
// public class UserData {
//     private int id;
//     private String email;
//     private String first_name;
//     private String last_name;
//     private String avatar;
// }

// TODO: Create Support POJO
// @Data
// @NoArgsConstructor
// public class Support {
//     private String url;
//     private String text;
// }

public class Exercise4_Deserialization {

    @Test
    public void testDeserializeUserResponse() {
        String baseUri = "https://reqres.in/api";

        // TODO: GET /users/2
        // TODO: Deserialize to UserResponse using .extract().as(UserResponse.class)

        // TODO: Print user details from POJO
        // System.out.println("ID: " + userResponse.getData().getId());
        // System.out.println("Email: " + userResponse.getData().getEmail());
        // System.out.println("Name: " + userResponse.getData().getFirst_name() + " " + userResponse.getData().getLast_name());

        // TODO: Validate using TestNG assertions
        // assertEquals(userResponse.getData().getId(), 2);
        // assertEquals(userResponse.getData().getEmail(), "janet.weaver@reqres.in");
        // assertTrue(userResponse.getData().getAvatar().contains("https://"));
        // assertNotNull(userResponse.getSupport().getUrl());
    }
}
```

**Expected Output:**
```
ID: 2
Email: janet.weaver@reqres.in
Name: Janet Weaver
Avatar URL: https://reqres.in/img/faces/2-image.jpg
Support URL: https://reqres.in/#support-heading
All validations passed!
```

**Hints:**
- Create three separate POJO classes
- Use `@NoArgsConstructor` for deserialization to work
- Field names must match JSON keys exactly (use underscore for `first_name`)
- Import `static org.testng.Assert.*` for assertions

---

### Exercise 5: Advanced Extraction - Arrays and Nested Objects

**Objective:** Extract data from arrays and nested JSON structures

**Task:**
1. Get list of users from `/users?page=1` (returns array of users)
2. Extract all email addresses as a List
3. Extract the first user's first name
4. Extract and print total number of users
5. Validate that all users have an avatar URL

**Starter Code:**
```java
import java.util.List;

public class Exercise5_AdvancedExtraction {

    @Test
    public void testExtractFromArray() {
        String baseUri = "https://reqres.in/api";

        Response response = given()
            .baseUri(baseUri)
        .when()
            .get("/users?page=1")
        .then()
            .statusCode(200)
            .extract().response();

        // TODO: Extract all emails as List<String>
        // List<String> emails = response.path("data.email");

        // TODO: Print all emails

        // TODO: Extract first user's first name
        // String firstName = response.path("data[0].first_name");

        // TODO: Extract total users count
        // int totalUsers = response.path("data.size()");

        // TODO: Validate all users have avatar
        // response.then().body("data.avatar", everyItem(notNullValue()));
    }
}
```

**Expected Output:**
```
Total users: 6
Emails:
  george.bluth@reqres.in
  janet.weaver@reqres.in
  emma.wong@reqres.in
  eve.holt@reqres.in
  charles.morris@reqres.in
  tracey.ramos@reqres.in
First user's name: George
All users have avatar URLs: true
```

**Hints:**
- Extract list: `response.path("data.email")` returns `List<String>`
- Array index: `data[0].first_name` for first element
- Array size: `data.size()` returns count
- Iterate list: `for (String email : emails)`

---

### Exercise 6: Chaining API Calls - Complete CRUD Flow

**Objective:** Chain multiple API calls to simulate a complete user lifecycle

**Task:**
Create a complete workflow:
1. **CREATE**: Create a new user and extract the ID
2. **READ**: Get the created user's details
3. **UPDATE**: Update the user's information
4. **DELETE**: Delete the user
5. Log each step with meaningful messages

**Starter Code:**
```java
public class Exercise6_ChainingAPICalls {

    @Test
    public void testCompleteCRUDWorkflow() {
        String baseUri = "https://reqres.in/api";

        // Step 1: CREATE USER
        System.out.println("=== STEP 1: CREATE USER ===");
        // TODO: Create user with name="John Doe" and job="Automation Engineer"
        // TODO: Extract and store the ID
        // TODO: Print: "Created user with ID: <id>"

        // Step 2: READ USER (Simulate - ReqRes doesn't persist data)
        System.out.println("\n=== STEP 2: READ USER ===");
        // TODO: GET /users/2 (use any existing user since ReqRes doesn't persist)
        // TODO: Validate status 200
        // TODO: Print user's email

        // Step 3: UPDATE USER
        System.out.println("\n=== STEP 3: UPDATE USER ===");
        // TODO: PUT /users/2 with updated data
        // TODO: body: {"name":"John Doe Updated","job":"Senior SDET"}
        // TODO: Validate status 200
        // TODO: Validate response contains updated name and job
        // TODO: Print: "Updated user successfully"

        // Step 4: DELETE USER
        System.out.println("\n=== STEP 4: DELETE USER ===");
        // TODO: DELETE /users/2
        // TODO: Validate status 204 (No Content)
        // TODO: Print: "Deleted user successfully"

        System.out.println("\n=== WORKFLOW COMPLETED ===");
    }
}
```

**Expected Output:**
```
=== STEP 1: CREATE USER ===
Created user with ID: 789

=== STEP 2: READ USER ===
User email: janet.weaver@reqres.in

=== STEP 3: UPDATE USER ===
Updated user successfully
Name: John Doe Updated
Job: Senior SDET

=== STEP 4: DELETE USER ===
Deleted user successfully

=== WORKFLOW COMPLETED ===
```

**Hints:**
- Store extracted ID: `String userId = response.path("id");`
- Use `System.out.println()` for logging
- DELETE returns 204 (No Content), not 200
- Chain by using extracted values in subsequent calls

---

## 🚀 Challenge Exercise (25 min)

### Exercise 7: Authentication-Protected Workflow with GitHub API

**Objective:** Work with real-world API requiring authentication and chain multiple operations

**Task:**
Build a complete workflow using GitHub API:
1. Authenticate with GitHub using Personal Access Token
2. Get authenticated user's details
3. List user's repositories
4. Get details of the first repository
5. Create helper methods for reusable code

**Setup:**
- Create a GitHub Personal Access Token: https://github.com/settings/tokens
- Select scopes: `repo`, `user`
- Store token securely (use environment variable or properties file)

**Starter Code:**
```java
public class Exercise7_GitHubAPIWorkflow {

    private static final String GITHUB_API = "https://api.github.com";
    private static final String GITHUB_TOKEN = "YOUR_TOKEN_HERE";  // Replace or use System.getProperty()

    // Helper method to get RequestSpecification with auth
    private RequestSpecification getGitHubSpec() {
        return new RequestSpecBuilder()
            .setBaseUri(GITHUB_API)
            .addHeader("Authorization", "token " + GITHUB_TOKEN)
            .addHeader("Accept", "application/vnd.github.v3+json")
            .build();
    }

    @Test
    public void testGitHubWorkflow() {
        // Step 1: Get authenticated user
        System.out.println("=== STEP 1: GET AUTHENTICATED USER ===");
        // TODO: GET /user with authentication
        // TODO: Extract and print username, name, email
        // TODO: Store username for next step

        // Step 2: List user's repositories
        System.out.println("\n=== STEP 2: LIST REPOSITORIES ===");
        // TODO: GET /users/{username}/repos
        // TODO: Extract repository names as List
        // TODO: Print all repository names
        // TODO: Store first repository name

        // Step 3: Get first repository details
        System.out.println("\n=== STEP 3: GET REPOSITORY DETAILS ===");
        // TODO: GET /repos/{username}/{repo_name}
        // TODO: Print: name, description, stars, language
        // TODO: Validate repository is not private
    }

    @Test
    public void testGitHubRateLimit() {
        // TODO: GET /rate_limit
        // TODO: Extract remaining requests
        // TODO: Print rate limit info
        // TODO: Validate remaining requests > 0
    }
}
```

**Expected Output:**
```
=== STEP 1: GET AUTHENTICATED USER ===
Username: johndoe
Name: John Doe
Email: john@example.com

=== STEP 2: LIST REPOSITORIES ===
Total repositories: 15
Repositories:
  - my-awesome-project
  - selenium-framework
  - api-automation
  - java-practice
  ...

=== STEP 3: GET REPOSITORY DETAILS ===
Repository: my-awesome-project
Description: Awesome project for automation
Stars: 42
Language: Java
Private: false

Rate Limit:
  Remaining: 4998/5000
  Resets at: 2024-01-15T12:00:00Z
```

**Hints:**
- Store token in environment variable: `System.getenv("GITHUB_TOKEN")`
- Use RequestSpecification to avoid repeating auth headers
- Extract from arrays: `path("name")` returns `List<String>` for array of objects
- GitHub API docs: https://docs.github.com/en/rest

**Bonus Challenges:**
- [ ] Create a POJO for Repository and deserialize
- [ ] Filter repositories by language (e.g., only Java repos)
- [ ] Sort repositories by stars
- [ ] Handle rate limit errors gracefully

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution: Basic Authentication

```java
import io.restassured.RestAssured;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise1_BasicAuth {

    @Test
    public void testBasicAuthSuccess() {
        given()
            .auth().basic("admin", "secret")  // Provide credentials
            .log().headers()  // See Authorization header in logs
        .when()
            .get("https://httpbin.org/basic-auth/admin/secret")
        .then()
            .statusCode(200)
            .body("authenticated", equalTo(true))
            .body("user", equalTo("admin"))
            .log().body();

        System.out.println("✓ Basic Auth succeeded");
    }

    @Test
    public void testBasicAuthFailure() {
        given()
            .auth().basic("admin", "wrongpassword")  // Wrong credentials
        .when()
            .get("https://httpbin.org/basic-auth/admin/secret")
        .then()
            .statusCode(401);  // Unauthorized

        System.out.println("✓ Basic Auth correctly rejected wrong credentials");
    }
}
```

**Explanation:**
- `.auth().basic()` automatically encodes credentials in Base64
- Adds header: `Authorization: Basic YWRtaW46c2VjcmV0`
- HTTPBin validates credentials and returns 200 if correct, 401 if wrong
- `.log().headers()` shows the Authorization header in console

**Key Takeaways:**
- Basic Auth is simple but not secure (credentials in every request)
- Always test both success and failure scenarios
- RestAssured handles Base64 encoding automatically

---

### Exercise 2 Solution: Bearer Token Authentication

```java
import io.restassured.response.Response;
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise2_BearerToken {

    @Test
    public void testBearerTokenFlow() {
        String baseUri = "https://reqres.in/api";

        // Step 1: Login to get token
        System.out.println("=== Step 1: Login ===");
        String token = given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body("{\"email\":\"eve.holt@reqres.in\",\"password\":\"cityslicka\"}")
            .log().all()  // Log request details
        .when()
            .post("/login")
        .then()
            .statusCode(200)
            .body("token", notNullValue())
            .log().body()  // Log response
            .extract().path("token");  // Extract token

        System.out.println("Login successful");
        System.out.println("Token: " + token);

        // Step 2: Use token to access user endpoint
        System.out.println("\n=== Step 2: Use Token ===");
        given()
            .baseUri(baseUri)
            .auth().oauth2(token)  // Use extracted token
            .log().headers()  // See Authorization: Bearer <token>
        .when()
            .get("/users/2")
        .then()
            .statusCode(200)
            .body("data.email", equalTo("janet.weaver@reqres.in"))
            .body("data.first_name", equalTo("Janet"))
            .log().body();

        System.out.println("✓ Accessed protected resource with token successfully");
    }
}
```

**Explanation:**
- First POST request gets the token from login response
- `.extract().path("token")` pulls the token value
- Second GET request uses the token with `.auth().oauth2(token)`
- This simulates real-world authentication flow

**Key Takeaways:**
- Tokens are obtained via login/authentication endpoint
- Store token in variable to use in subsequent requests
- `.auth().oauth2()` adds header: `Authorization: Bearer <token>`
- Always validate token is not null before using it

---

### Exercise 3 Solution: POJO Serialization

**User.java:**
```java
import lombok.Data;
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User {
    private String name;
    private String job;
}
```

**Test Class:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise3_POJOSerialization {

    @Test
    public void testCreateUserWithPOJO() {
        String baseUri = "https://reqres.in/api";

        // Create User object
        User user = new User("morpheus", "leader");

        System.out.println("Creating user: " + user);

        // POST with POJO - RestAssured serializes to JSON automatically
        String userId = given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body(user)  // POJO automatically converted to JSON
            .log().body()  // See JSON: {"name":"morpheus","job":"leader"}
        .when()
            .post("/users")
        .then()
            .statusCode(201)  // Created
            .body("name", equalTo("morpheus"))
            .body("job", equalTo("leader"))
            .body("id", notNullValue())
            .body("createdAt", notNullValue())
            .log().body()
            .extract().path("id");

        System.out.println("✓ Created user with ID: " + userId);
        System.out.println("✓ User name: morpheus");
        System.out.println("✓ User job: leader");
    }
}
```

**Explanation:**
- `@Data` generates getters, setters, toString, equals, hashCode
- `@AllArgsConstructor` creates constructor with all fields
- `@NoArgsConstructor` creates empty constructor (needed for deserialization)
- RestAssured uses Jackson library to serialize POJO to JSON
- Much cleaner than building JSON strings manually

**Key Takeaways:**
- POJOs make code type-safe and maintainable
- Lombok reduces boilerplate code by 70%
- RestAssured handles serialization automatically when you pass a POJO to `.body()`

---

### Exercise 4 Solution: Deserialization

**UserResponse.java:**
```java
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class UserResponse {
    private UserData data;
    private Support support;
}
```

**UserData.java:**
```java
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class UserData {
    private int id;
    private String email;
    private String first_name;
    private String last_name;
    private String avatar;
}
```

**Support.java:**
```java
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class Support {
    private String url;
    private String text;
}
```

**Test Class:**
```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.testng.Assert.*;

public class Exercise4_Deserialization {

    @Test
    public void testDeserializeUserResponse() {
        String baseUri = "https://reqres.in/api";

        // Get user and deserialize to POJO
        UserResponse userResponse = given()
            .baseUri(baseUri)
        .when()
            .get("/users/2")
        .then()
            .statusCode(200)
            .log().body()
            .extract().as(UserResponse.class);  // Deserialize JSON to POJO

        // Now we have a typed Java object
        System.out.println("=== User Details ===");
        System.out.println("ID: " + userResponse.getData().getId());
        System.out.println("Email: " + userResponse.getData().getEmail());
        System.out.println("Name: " + userResponse.getData().getFirst_name() + " " + userResponse.getData().getLast_name());
        System.out.println("Avatar: " + userResponse.getData().getAvatar());
        System.out.println("\n=== Support Info ===");
        System.out.println("URL: " + userResponse.getSupport().getUrl());
        System.out.println("Text: " + userResponse.getSupport().getText());

        // Validate using TestNG assertions
        assertEquals(userResponse.getData().getId(), 2);
        assertEquals(userResponse.getData().getEmail(), "janet.weaver@reqres.in");
        assertEquals(userResponse.getData().getFirst_name(), "Janet");
        assertEquals(userResponse.getData().getLast_name(), "Weaver");
        assertTrue(userResponse.getData().getAvatar().contains("https://"));
        assertNotNull(userResponse.getSupport().getUrl());
        assertTrue(userResponse.getSupport().getUrl().startsWith("https://"));

        System.out.println("\n✓ All validations passed!");
    }
}
```

**Explanation:**
- `.extract().as(UserResponse.class)` deserializes JSON to Java object
- POJO field names must match JSON keys exactly
- `@NoArgsConstructor` is required for Jackson to create object
- We can now use Java assertions instead of Hamcrest matchers
- Type-safe access with IDE autocomplete

**Key Takeaways:**
- Deserialization converts JSON → Java object
- Requires `@NoArgsConstructor` annotation
- Field names must match JSON or use `@JsonProperty`
- Enables type-safe validation and cleaner test code

---

### Exercise 5 Solution: Advanced Extraction

```java
import io.restassured.response.Response;
import org.testng.annotations.Test;
import java.util.List;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise5_AdvancedExtraction {

    @Test
    public void testExtractFromArray() {
        String baseUri = "https://reqres.in/api";

        Response response = given()
            .baseUri(baseUri)
        .when()
            .get("/users?page=1")
        .then()
            .statusCode(200)
            .extract().response();

        // Extract all emails as List
        List<String> emails = response.path("data.email");

        System.out.println("=== User Emails ===");
        System.out.println("Total users: " + emails.size());
        emails.forEach(email -> System.out.println("  - " + email));

        // Extract first user's first name
        String firstName = response.path("data[0].first_name");
        System.out.println("\nFirst user's name: " + firstName);

        // Extract total users count
        int totalUsers = response.path("data.size()");
        System.out.println("Total users (via size): " + totalUsers);

        // Extract all first names
        List<String> firstNames = response.path("data.first_name");
        System.out.println("\nAll first names: " + firstNames);

        // Validate all users have avatar
        response.then().body("data.avatar", everyItem(notNullValue()));
        response.then().body("data.avatar", everyItem(containsString("https://")));

        System.out.println("\n✓ All users have valid avatar URLs");
    }

    @Test
    public void testExtractSpecificFields() {
        String baseUri = "https://reqres.in/api";

        // Extract multiple fields
        Response response = get(baseUri + "/users?page=2");

        List<Integer> ids = response.path("data.id");
        List<String> emails = response.path("data.email");
        List<String> avatars = response.path("data.avatar");

        System.out.println("=== Page 2 Users ===");
        for (int i = 0; i < ids.size(); i++) {
            System.out.println("User " + ids.get(i) + ": " + emails.get(i));
        }
    }
}
```

**Explanation:**
- `response.path("data.email")` extracts all email fields from array
- `data[0].first_name` accesses first element of array
- `data.size()` returns array length
- `everyItem()` matcher validates all elements in array

**Key Takeaways:**
- Extract arrays as `List<T>` using path expressions
- Use `[index]` for specific array elements
- `everyItem()` validates all elements match condition
- Can extract multiple fields and iterate together

---

### Exercise 6 Solution: Chaining API Calls

```java
import org.testng.annotations.Test;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise6_ChainingAPICalls {

    @Test
    public void testCompleteCRUDWorkflow() {
        String baseUri = "https://reqres.in/api";

        // Step 1: CREATE USER
        System.out.println("=== STEP 1: CREATE USER ===");
        String userId = given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body("{\"name\":\"John Doe\",\"job\":\"Automation Engineer\"}")
        .when()
            .post("/users")
        .then()
            .statusCode(201)  // Created
            .body("name", equalTo("John Doe"))
            .body("job", equalTo("Automation Engineer"))
            .body("id", notNullValue())
            .log().body()
            .extract().path("id");

        System.out.println("Created user with ID: " + userId);

        // Step 2: READ USER
        System.out.println("\n=== STEP 2: READ USER ===");
        String email = given()
            .baseUri(baseUri)
        .when()
            .get("/users/2")  // ReqRes doesn't persist, so we read existing user
        .then()
            .statusCode(200)
            .body("data.id", equalTo(2))
            .log().body()
            .extract().path("data.email");

        System.out.println("User email: " + email);

        // Step 3: UPDATE USER
        System.out.println("\n=== STEP 3: UPDATE USER ===");
        given()
            .baseUri(baseUri)
            .contentType("application/json")
            .body("{\"name\":\"John Doe Updated\",\"job\":\"Senior SDET\"}")
        .when()
            .put("/users/2")
        .then()
            .statusCode(200)
            .body("name", equalTo("John Doe Updated"))
            .body("job", equalTo("Senior SDET"))
            .body("updatedAt", notNullValue())
            .log().body();

        System.out.println("Updated user successfully");
        System.out.println("  Name: John Doe Updated");
        System.out.println("  Job: Senior SDET");

        // Step 4: DELETE USER
        System.out.println("\n=== STEP 4: DELETE USER ===");
        given()
            .baseUri(baseUri)
        .when()
            .delete("/users/2")
        .then()
            .statusCode(204);  // No Content

        System.out.println("Deleted user successfully");

        System.out.println("\n=== ✓ WORKFLOW COMPLETED ===");
    }
}
```

**Explanation:**
- Each step uses output from previous step (or would in a real API)
- Extracted ID from CREATE is used in subsequent operations
- Validates status codes and response bodies at each step
- Logs progress with meaningful messages

**Key Takeaways:**
- Chain calls by extracting values and using in next request
- DELETE typically returns 204 (No Content)
- PUT returns 200 with updated data
- POST returns 201 with created resource

---

### Exercise 7 Solution: GitHub API Workflow

```java
import io.restassured.builder.RequestSpecBuilder;
import io.restassured.response.Response;
import io.restassured.specification.RequestSpecification;
import org.testng.annotations.BeforeClass;
import org.testng.annotations.Test;
import java.util.List;
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;

public class Exercise7_GitHubAPIWorkflow {

    private static final String GITHUB_API = "https://api.github.com";
    private RequestSpecification githubSpec;

    @BeforeClass
    public void setup() {
        // Get token from environment variable (recommended) or system property
        String token = System.getenv("GITHUB_TOKEN");
        if (token == null) {
            token = System.getProperty("github.token", "YOUR_TOKEN_HERE");
        }

        // Create reusable specification
        githubSpec = new RequestSpecBuilder()
            .setBaseUri(GITHUB_API)
            .addHeader("Authorization", "token " + token)
            .addHeader("Accept", "application/vnd.github.v3+json")
            .build();
    }

    @Test
    public void testGitHubWorkflow() {
        // Step 1: Get authenticated user
        System.out.println("=== STEP 1: GET AUTHENTICATED USER ===");
        Response userResponse = given()
            .spec(githubSpec)
        .when()
            .get("/user")
        .then()
            .statusCode(200)
            .body("login", notNullValue())
            .log().body()
            .extract().response();

        String username = userResponse.path("login");
        String name = userResponse.path("name");
        String email = userResponse.path("email");

        System.out.println("Username: " + username);
        System.out.println("Name: " + name);
        System.out.println("Email: " + email);

        // Step 2: List user's repositories
        System.out.println("\n=== STEP 2: LIST REPOSITORIES ===");
        Response reposResponse = given()
            .spec(githubSpec)
        .when()
            .get("/users/" + username + "/repos")
        .then()
            .statusCode(200)
            .extract().response();

        List<String> repoNames = reposResponse.path("name");
        System.out.println("Total repositories: " + repoNames.size());
        System.out.println("Repositories:");
        repoNames.forEach(repo -> System.out.println("  - " + repo));

        if (!repoNames.isEmpty()) {
            String firstRepo = repoNames.get(0);

            // Step 3: Get first repository details
            System.out.println("\n=== STEP 3: GET REPOSITORY DETAILS ===");
            given()
                .spec(githubSpec)
            .when()
                .get("/repos/" + username + "/" + firstRepo)
            .then()
                .statusCode(200)
                .body("name", equalTo(firstRepo))
                .body("owner.login", equalTo(username))
                .log().body();

            Response repoDetails = given().spec(githubSpec)
                .get("/repos/" + username + "/" + firstRepo)
                .then().extract().response();

            System.out.println("Repository: " + repoDetails.path("name"));
            System.out.println("Description: " + repoDetails.path("description"));
            System.out.println("Stars: " + repoDetails.path("stargazers_count"));
            System.out.println("Language: " + repoDetails.path("language"));
            System.out.println("Private: " + repoDetails.path("private"));
        }
    }

    @Test
    public void testGitHubRateLimit() {
        System.out.println("=== GITHUB RATE LIMIT ===");
        Response rateLimitResponse = given()
            .spec(githubSpec)
        .when()
            .get("/rate_limit")
        .then()
            .statusCode(200)
            .body("resources.core.remaining", greaterThan(0))
            .log().body()
            .extract().response();

        int remaining = rateLimitResponse.path("resources.core.remaining");
        int limit = rateLimitResponse.path("resources.core.limit");
        String reset = rateLimitResponse.path("resources.core.reset");

        System.out.println("Rate Limit: " + remaining + "/" + limit);
        System.out.println("Resets at: " + reset);
    }
}
```

**Explanation:**
- Uses RequestSpecification to avoid repeating auth headers
- Chains three API calls using extracted username and repo name
- Real-world authentication with GitHub Personal Access Token
- Validates rate limits to avoid exceeding API quota

**Key Takeaways:**
- Use RequestSpecification for consistent auth across tests
- Extract data from one call to use in next (username → repos)
- Real APIs often have rate limits - always check
- Store tokens in environment variables, never hardcode

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Basic Auth | ☐ | __/10 | ___ min | |
| 2 - Bearer Token | ☐ | __/10 | ___ min | |
| 3 - POJO Serialization | ☐ | __/10 | ___ min | |
| 4 - Deserialization | ☐ | __/10 | ___ min | |
| 5 - Advanced Extraction | ☐ | __/10 | ___ min | |
| 6 - Chaining Calls | ☐ | __/10 | ___ min | |
| 7 - GitHub API | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Concepts I mastered:**
- [ ] Basic authentication
- [ ] Bearer token authentication
- [ ] POJO creation with Lombok
- [ ] Serialization (Java → JSON)
- [ ] Deserialization (JSON → Java)
- [ ] Response extraction
- [ ] Chaining API calls
- [ ] Real-world API integration

**Areas I struggled with:**
1. ________________
2. ________________

**Questions for review:**
1. ________________
2. ________________

---

## ✅ Answers to Self-Check Questions (from Concept Guide)

**Q1: What are the four main authentication types, and when would you use each?**
- **Basic Auth:** Username/password in every request. Use for simple internal APIs, development. Not secure for production.
- **Bearer Token:** Token obtained via login, sent in subsequent requests. Use for 90% of modern applications. Most common in production.
- **API Key:** Unique key per user/app. Use for third-party integrations (Google Maps, Weather APIs). Simple and secure.
- **OAuth 2.0:** Industry-standard protocol. Use for third-party integrations needing user consent (Login with Google, access user's data).

**Q2: Why do we need @NoArgsConstructor annotation on POJOs used for deserialization?**
Jackson (JSON library) needs to create an empty object first, then populate fields via setters. Without @NoArgsConstructor, Jackson can't instantiate the object, and deserialization fails with "No default constructor" error.

**Q3: How would you extract a token from a login response and use it in the next request?**
```java
String token = given()
    .body(loginPayload)
.when()
    .post("/login")
.then()
    .extract().path("token");

given()
    .auth().oauth2(token)
.when()
    .get("/profile");
```

**Q4: What's the difference between `.extract().path("id")` and `.extract().as(User.class)`?**
- `.path("id")` extracts a single field value (returns String, int, etc.)
- `.as(User.class)` deserializes entire JSON to a Java object (returns User object)

**Q5: How does Lombok's @Data annotation help in reducing code?**
@Data generates: getters, setters, toString(), equals(), hashCode(). Reduces a 50-line POJO to 5 lines. Makes code maintainable and reduces boilerplate.

---

**Congratulations! You've completed Day 3 practice exercises. Move to Day-3-Debug-Practice.md to sharpen your debugging skills!** 🎯
