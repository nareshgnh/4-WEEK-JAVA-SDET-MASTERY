# DAY 3: ADVANCED API TESTING

## 🎯 Today's Learning Objectives

By end of today, you will:
- [ ] Master different authentication methods (Basic Auth, Bearer Token, OAuth2, API Keys)
- [ ] Use POJOs (Plain Old Java Objects) for serialization and deserialization
- [ ] Extract and manipulate complex response data
- [ ] Chain multiple API calls (use response from one call in another)
- [ ] Build end-to-end API workflows and test scenarios
- [ ] Understand Lombok for reducing boilerplate code in POJOs

**Time Required:** 2.5-3 hours
**Difficulty:** Medium to Advanced
**Prerequisite:** Day 1 & 2 - RestAssured fundamentals, JSON validation

---

## 📚 Core Concepts

### Concept 1: API Authentication - The Gatekeeper

**What is it?**
Authentication is the process of verifying the identity of a user or application making an API request. Most production APIs require authentication to ensure only authorized users can access data or perform actions.

**Why does it matter for automation?**
In real-world projects:
- 90% of APIs require some form of authentication
- You can't test secured endpoints without proper auth
- Different environments use different auth mechanisms
- Interview questions heavily focus on authentication handling

**Common Authentication Types:**

#### 1. Basic Authentication
The simplest form - sends username:password encoded in Base64 in the Authorization header.

**Syntax:**
```java
given()
    .auth().basic("username", "password")
.when()
    .get("/api/users")
.then()
    .statusCode(200);
```

**How it works:**
- Combines username:password as "username:password"
- Encodes in Base64: "dXNlcm5hbWU6cGFzc3dvcmQ="
- Sends as header: `Authorization: Basic dXNlcm5hbWU6cGFzc3dvcmQ=`

**Real Example:**
```java
@Test
public void testBasicAuth() {
    given()
        .auth().basic("admin@reqres.in", "password123")
        .log().headers()  // See the Authorization header
    .when()
        .get("https://httpbin.org/basic-auth/admin@reqres.in/password123")
    .then()
        .statusCode(200)
        .body("authenticated", equalTo(true));
}
```

**When to use:**
- Internal APIs, development environments
- Simple authentication needs
- Not recommended for production (credentials in every request)

---

#### 2. Bearer Token Authentication (Most Common)
Uses a token (usually JWT - JSON Web Token) obtained after login. The token is sent in subsequent requests.

**Syntax:**
```java
String token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...";

given()
    .auth().oauth2(token)  // RestAssured method
.when()
    .get("/api/profile")
.then()
    .statusCode(200);

// OR manually via header
given()
    .header("Authorization", "Bearer " + token)
.when()
    .get("/api/profile")
.then()
    .statusCode(200);
```

**Real Example - Two-Step Process:**
```java
@Test
public void testBearerTokenAuth() {
    // Step 1: Login to get token
    String token = given()
        .contentType("application/json")
        .body("{\"email\":\"eve.holt@reqres.in\",\"password\":\"cityslicka\"}")
    .when()
        .post("https://reqres.in/api/login")
    .then()
        .statusCode(200)
        .extract().path("token");  // Extract token from response

    System.out.println("Token: " + token);

    // Step 2: Use token in subsequent requests
    given()
        .auth().oauth2(token)
    .when()
        .get("https://reqres.in/api/users/2")
    .then()
        .statusCode(200)
        .body("data.email", equalTo("janet.weaver@reqres.in"));
}
```

**When to use:**
- Modern web applications (95% of cases)
- Microservices architecture
- Token expires after time (secure)
- Industry standard

---

#### 3. API Key Authentication
A unique key assigned to each application/user, sent as header or query parameter.

**Syntax:**
```java
// As header
given()
    .header("X-API-Key", "abc123xyz456")
.when()
    .get("/api/data")
.then()
    .statusCode(200);

// As query parameter
given()
    .queryParam("api_key", "abc123xyz456")
.when()
    .get("/api/data")
.then()
    .statusCode(200);
```

**Real Example - GitHub API:**
```java
@Test
public void testGitHubAPIWithToken() {
    String githubToken = "ghp_your_personal_access_token_here";

    given()
        .header("Authorization", "token " + githubToken)
        .header("Accept", "application/vnd.github.v3+json")
    .when()
        .get("https://api.github.com/user")
    .then()
        .statusCode(200)
        .body("login", notNullValue());
}
```

**When to use:**
- Third-party API integrations (Google Maps, Weather APIs)
- Public APIs with rate limiting
- Simple but secure

---

#### 4. OAuth 2.0 (Advanced)
Industry-standard protocol for authorization. Used by Google, Facebook, GitHub, etc.

**Flow:**
1. Redirect user to authorization server
2. User grants permission
3. Receive authorization code
4. Exchange code for access token
5. Use access token in API requests

**Syntax:**
```java
// Assuming you already have access token
String accessToken = "ya29.a0AfH6SMBx...";

given()
    .auth().oauth2(accessToken)
.when()
    .get("https://www.googleapis.com/oauth2/v1/userinfo")
.then()
    .statusCode(200)
    .body("email", notNullValue());
```

**When to use:**
- Third-party integrations (Login with Google, Facebook)
- When app needs access to user's data on another platform
- Enterprise applications

---

### Concept 2: POJOs - Java Objects for JSON

**What is it?**
POJO (Plain Old Java Object) is a simple Java class with private fields and public getters/setters. In API testing, we use POJOs to represent JSON request/response data as Java objects.

**Why does it matter for automation?**
Without POJOs:
```java
// Manual JSON string - error-prone, hard to maintain
String requestBody = "{\"name\":\"John\",\"job\":\"QA Engineer\"}";
```

With POJOs:
```java
// Type-safe, IDE auto-complete, easy to maintain
User user = new User("John", "QA Engineer");
given().body(user)...  // RestAssured automatically converts to JSON
```

**Benefits:**
- Type safety (compiler catches errors)
- Reusable across tests
- Easy to maintain (change once, applies everywhere)
- Professional framework standard

**Creating a POJO - Manual Way:**
```java
public class User {
    private String name;
    private String job;

    // Constructor
    public User(String name, String job) {
        this.name = name;
        this.job = job;
    }

    // Getters
    public String getName() {
        return name;
    }

    public String getJob() {
        return job;
    }

    // Setters
    public void setName(String name) {
        this.name = name;
    }

    public void setJob(String job) {
        this.job = job;
    }
}
```

**Using the POJO:**
```java
@Test
public void testCreateUserWithPOJO() {
    // Create User object
    User user = new User("morpheus", "leader");

    // RestAssured serializes it to JSON automatically
    given()
        .contentType("application/json")
        .body(user)  // {"name":"morpheus","job":"leader"}
    .when()
        .post("https://reqres.in/api/users")
    .then()
        .statusCode(201)
        .body("name", equalTo("morpheus"))
        .body("job", equalTo("leader"));
}
```

---

### Concept 3: Lombok - Reduce Boilerplate Code

**What is it?**
Lombok is a Java library that automatically generates getters, setters, constructors, toString(), etc. using annotations. It reduces POJO code by 70%.

**Why does it matter for automation?**
Compare the same POJO with and without Lombok:

**Without Lombok (20+ lines):**
```java
public class User {
    private String name;
    private String job;

    public User() {}

    public User(String name, String job) {
        this.name = name;
        this.job = job;
    }

    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getJob() { return job; }
    public void setJob(String job) { this.job = job; }

    @Override
    public String toString() {
        return "User{name='" + name + "', job='" + job + "'}";
    }
}
```

**With Lombok (7 lines):**
```java
import lombok.Data;
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@Data                    // Generates getters, setters, toString, equals, hashCode
@AllArgsConstructor      // Generates constructor with all fields
@NoArgsConstructor       // Generates no-arg constructor (needed for deserialization)
public class User {
    private String name;
    private String job;
}
```

**Lombok Annotations Cheat Sheet:**
| Annotation | What it generates |
|------------|-------------------|
| `@Data` | getters, setters, toString, equals, hashCode |
| `@Getter` | Getter methods only |
| `@Setter` | Setter methods only |
| `@AllArgsConstructor` | Constructor with all fields |
| `@NoArgsConstructor` | Empty constructor |
| `@Builder` | Builder pattern (fluent object creation) |
| `@ToString` | toString() method |

**Maven Dependency:**
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.30</version>
    <scope>provided</scope>
</dependency>
```

**Real Example with Builder Pattern:**
```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder  // Enables fluent object creation
public class User {
    private String name;
    private String job;
    private String email;
    private int age;
}

// Usage
@Test
public void testBuilderPattern() {
    User user = User.builder()
        .name("John Doe")
        .job("SDET")
        .email("john@example.com")
        .age(28)
        .build();

    given()
        .contentType("application/json")
        .body(user)
    .when()
        .post("https://reqres.in/api/users")
    .then()
        .statusCode(201);
}
```

---

### Concept 4: Deserialization - JSON to Java Object

**What is it?**
Deserialization is converting JSON response to a Java object (POJO). This allows you to work with response data as typed objects instead of JSON strings.

**Why does it matter for automation?**
Instead of extracting each field separately:
```java
String name = response.path("data.first_name");
String email = response.path("data.email");
// ... tedious for complex objects
```

Deserialize once, use object:
```java
UserResponse user = response.as(UserResponse.class);
System.out.println(user.getData().getFirstName());
System.out.println(user.getData().getEmail());
```

**Example - Complete Flow:**

**POJO for Response:**
```java
@Data
@NoArgsConstructor
public class UserResponse {
    private UserData data;
    private Support support;
}

@Data
@NoArgsConstructor
public class UserData {
    private int id;
    private String email;
    private String first_name;
    private String last_name;
    private String avatar;
}

@Data
@NoArgsConstructor
public class Support {
    private String url;
    private String text;
}
```

**Test with Deserialization:**
```java
@Test
public void testDeserialization() {
    UserResponse user = given()
        .baseUri("https://reqres.in/api")
    .when()
        .get("/users/2")
    .then()
        .statusCode(200)
        .extract().as(UserResponse.class);  // Deserialize to object

    // Now work with Java object
    System.out.println("User ID: " + user.getData().getId());
    System.out.println("Email: " + user.getData().getEmail());
    System.out.println("Name: " + user.getData().getFirst_name() + " " + user.getData().getLast_name());

    // Use in assertions
    assertEquals(2, user.getData().getId());
    assertEquals("janet.weaver@reqres.in", user.getData().getEmail());
    assertTrue(user.getData().getAvatar().contains("https://"));
}
```

**Deserialization for Arrays:**
```java
@Test
public void testDeserializeArray() {
    // Get array of users
    UserData[] users = given()
        .baseUri("https://reqres.in/api")
    .when()
        .get("/users?page=1")
    .then()
        .statusCode(200)
        .extract().path("data");  // Extract "data" array

    System.out.println("Total users: " + users.length);

    // Iterate through users
    for (UserData user : users) {
        System.out.println(user.getFirst_name() + " - " + user.getEmail());
    }
}
```

---

### Concept 5: Response Extraction and Manipulation

**What is it?**
Extracting specific data from API responses and using it in subsequent operations. This is crucial for building realistic test scenarios.

**Extraction Methods:**

**1. Extract Single Value:**
```java
int userId = get("/users/1")
    .then()
    .extract().path("id");

String email = get("/users/1")
    .then()
    .extract().path("data.email");
```

**2. Extract Entire Response:**
```java
Response response = get("/users/1")
    .then()
    .extract().response();

// Use response multiple times
int statusCode = response.statusCode();
String body = response.asString();
String name = response.path("data.first_name");
```

**3. Extract as POJO:**
```java
User user = get("/users/1")
    .then()
    .extract().as(User.class);
```

**4. Extract from Arrays:**
```java
// Get first element
String firstName = get("/users")
    .then()
    .extract().path("data[0].first_name");

// Get all emails as list
List<String> emails = get("/users")
    .then()
    .extract().path("data.email");
```

**Real Example - Extract and Use:**
```java
@Test
public void testExtractAndUse() {
    // Create user and extract ID
    int userId = given()
        .contentType("application/json")
        .body("{\"name\":\"John\",\"job\":\"SDET\"}")
    .when()
        .post("https://reqres.in/api/users")
    .then()
        .statusCode(201)
        .extract().path("id");  // Extract created user's ID

    System.out.println("Created user ID: " + userId);

    // Use extracted ID in another test
    given()
    .when()
        .get("https://reqres.in/api/users/" + userId)
    .then()
        .statusCode(200);
}
```

---

### Concept 6: Chaining API Calls - Real-World Workflows

**What is it?**
Chaining means using the response from one API call as input to another. This simulates real user workflows.

**Why does it matter for automation?**
Real applications don't make isolated API calls:
- User signs up → receives confirmation email → verifies email
- User logs in → gets token → uses token to fetch profile → updates profile
- Create product → add to cart → checkout → verify order

**Pattern 1: Extract and Reuse**
```java
@Test
public void testUserWorkflow() {
    // 1. Create user
    String userId = given()
        .contentType("application/json")
        .body("{\"name\":\"John\",\"job\":\"SDET\"}")
    .when()
        .post("https://reqres.in/api/users")
    .then()
        .statusCode(201)
        .extract().path("id");

    System.out.println("Step 1: Created user with ID: " + userId);

    // 2. Get user details
    Response userDetails = given()
    .when()
        .get("https://reqres.in/api/users/" + userId)
    .then()
        .statusCode(200)
        .extract().response();

    System.out.println("Step 2: Retrieved user: " + userDetails.asString());

    // 3. Update user
    given()
        .contentType("application/json")
        .body("{\"name\":\"John Updated\",\"job\":\"Senior SDET\"}")
    .when()
        .put("https://reqres.in/api/users/" + userId)
    .then()
        .statusCode(200)
        .body("name", equalTo("John Updated"))
        .body("job", equalTo("Senior SDET"));

    System.out.println("Step 3: Updated user successfully");

    // 4. Delete user
    given()
    .when()
        .delete("https://reqres.in/api/users/" + userId)
    .then()
        .statusCode(204);

    System.out.println("Step 4: Deleted user successfully");
}
```

**Pattern 2: Authentication Chain**
```java
@Test
public void testAuthenticationChain() {
    // Step 1: Register user
    String userEmail = "eve.holt@reqres.in";
    String password = "pistol";

    given()
        .contentType("application/json")
        .body("{\"email\":\"" + userEmail + "\",\"password\":\"" + password + "\"}")
    .when()
        .post("https://reqres.in/api/register")
    .then()
        .statusCode(200);

    System.out.println("Step 1: User registered");

    // Step 2: Login to get token
    String token = given()
        .contentType("application/json")
        .body("{\"email\":\"" + userEmail + "\",\"password\":\"" + password + "\"}")
    .when()
        .post("https://reqres.in/api/login")
    .then()
        .statusCode(200)
        .extract().path("token");

    System.out.println("Step 2: Login successful. Token: " + token);

    // Step 3: Use token to access protected resource
    given()
        .auth().oauth2(token)
    .when()
        .get("https://reqres.in/api/users/2")
    .then()
        .statusCode(200)
        .body("data.email", notNullValue());

    System.out.println("Step 3: Accessed protected resource with token");
}
```

**Pattern 3: Complex E-commerce Flow**
```java
@Test
public void testEcommerceWorkflow() {
    String baseUri = "https://jsonplaceholder.typicode.com";

    // Step 1: Get all products (posts as products)
    List<Integer> productIds = given()
        .baseUri(baseUri)
    .when()
        .get("/posts")
    .then()
        .statusCode(200)
        .extract().path("id");

    System.out.println("Step 1: Retrieved " + productIds.size() + " products");

    // Step 2: Get details of first product
    int selectedProductId = productIds.get(0);
    Response productDetails = given()
        .baseUri(baseUri)
    .when()
        .get("/posts/" + selectedProductId)
    .then()
        .statusCode(200)
        .extract().response();

    String productTitle = productDetails.path("title");
    System.out.println("Step 2: Selected product: " + productTitle);

    // Step 3: Add comment (simulate adding to cart)
    int commentId = given()
        .baseUri(baseUri)
        .contentType("application/json")
        .body("{\"postId\":" + selectedProductId + ",\"name\":\"John\",\"email\":\"john@example.com\",\"body\":\"Added to cart\"}")
    .when()
        .post("/comments")
    .then()
        .statusCode(201)
        .extract().path("id");

    System.out.println("Step 3: Added to cart. Comment ID: " + commentId);

    // Step 4: Verify comment exists (verify cart item)
    given()
        .baseUri(baseUri)
    .when()
        .get("/comments/" + commentId)
    .then()
        .statusCode(200)
        .body("postId", equalTo(selectedProductId))
        .body("email", equalTo("john@example.com"));

    System.out.println("Step 4: Verified cart item successfully");
}
```

---

## 🐍 Python vs Java Comparison

### Authentication in Python (What You Know)
```python
import requests

# Basic Auth
response = requests.get('https://api.example.com/users',
                       auth=('username', 'password'))

# Bearer Token
headers = {'Authorization': 'Bearer token123'}
response = requests.get('https://api.example.com/profile', headers=headers)

# API Key
headers = {'X-API-Key': 'abc123'}
response = requests.get('https://api.example.com/data', headers=headers)
```

### Authentication in Java (What You're Learning)
```java
// Basic Auth
given()
    .auth().basic("username", "password")
.when()
    .get("/users")
.then()
    .statusCode(200);

// Bearer Token
given()
    .auth().oauth2("token123")
.when()
    .get("/profile")
.then()
    .statusCode(200);

// API Key
given()
    .header("X-API-Key", "abc123")
.when()
    .get("/data")
.then()
    .statusCode(200);
```

### POJOs vs Python Dictionaries

**Python Way:**
```python
# Python uses dictionaries - dynamic typing
user = {
    "name": "John",
    "job": "SDET"
}

response = requests.post('https://reqres.in/api/users', json=user)
data = response.json()  # Returns dictionary
print(data['name'])  # No type safety
```

**Java Way:**
```java
// Java uses POJOs - static typing
@Data
@AllArgsConstructor
public class User {
    private String name;
    private String job;
}

User user = new User("John", "SDET");

User response = given()
    .body(user)
.when()
    .post("/users")
.then()
    .extract().as(User.class);

System.out.println(response.getName());  // Type-safe, IDE autocomplete
```

**Key Differences:**
| Aspect | Python | Java |
|--------|--------|------|
| **Data Structure** | Dictionaries (dynamic) | POJOs (static) |
| **Type Safety** | Runtime errors | Compile-time errors |
| **IDE Support** | Limited autocomplete | Full autocomplete |
| **Maintenance** | Harder with typos | Compiler catches errors |
| **Learning Curve** | Easier initially | More upfront work, easier long-term |

**Mental Model Shift:**
Python's dynamic typing is flexible but error-prone at scale. Java's static typing requires upfront POJO creation, but catches errors early and makes frameworks maintainable. In product companies with large teams, type safety wins.

---

## ⚠️ Common Mistakes & How to Avoid Them

### Mistake 1: Hardcoding Credentials
❌ **Wrong:**
```java
@Test
public void testAPI() {
    given()
        .auth().basic("admin", "password123")  // Hardcoded!
    .when()
        .get("/users");
}
```

✅ **Correct:**
```java
public class ConfigReader {
    public static String getUsername() {
        return System.getProperty("api.username", "default_user");
    }

    public static String getPassword() {
        return System.getProperty("api.password", "default_pass");
    }
}

@Test
public void testAPI() {
    given()
        .auth().basic(ConfigReader.getUsername(), ConfigReader.getPassword())
    .when()
        .get("/users");
}
```

**Why it matters:** Credentials should never be in code. Use environment variables, properties files, or CI/CD secrets.

---

### Mistake 2: Forgetting @NoArgsConstructor for POJOs
❌ **Wrong:**
```java
@Data
@AllArgsConstructor  // Only this constructor
public class User {
    private String name;
    private String job;
}

// Deserialization fails!
User user = response.as(User.class);  // Error: No default constructor
```

✅ **Correct:**
```java
@Data
@AllArgsConstructor
@NoArgsConstructor  // Required for deserialization
public class User {
    private String name;
    private String job;
}
```

**Why it matters:** JSON deserialization requires a no-argument constructor to create the object before setting fields.

---

### Mistake 3: Not Validating After Extraction
❌ **Wrong:**
```java
// Extract token but don't validate
String token = response.path("token");

// Use immediately
given().auth().oauth2(token).get("/profile");  // What if token is null?
```

✅ **Correct:**
```java
String token = response.then()
    .statusCode(200)
    .body("token", notNullValue())
    .extract().path("token");

assertNotNull(token, "Token should not be null");
assertTrue(token.length() > 10, "Token seems invalid");

given().auth().oauth2(token).get("/profile");
```

**Why it matters:** Always validate extracted data before using it. Null token = cascading failures in chained tests.

---

### Mistake 4: Ignoring Token Expiration
❌ **Wrong:**
```java
@BeforeClass
public void setup() {
    token = getTokenFromLogin();  // Get token once
}

@Test
public void test1() { /* uses token */ }
@Test
public void test2() { /* uses token */ }  // May fail if token expired
```

✅ **Correct:**
```java
@BeforeClass
public void setup() {
    token = getTokenFromLogin();
}

@BeforeMethod
public void validateToken() {
    // Refresh token if needed
    if (isTokenExpired(token)) {
        token = getTokenFromLogin();
    }
}
```

**Why it matters:** Tokens expire. Long test suites fail midway if token expires.

---

### Mistake 5: Wrong POJO Field Names
❌ **Wrong:**
```java
// JSON: {"first_name": "John"}
@Data
public class User {
    private String firstName;  // Doesn't match JSON!
}
```

✅ **Correct:**
```java
// Option 1: Match JSON exactly
@Data
public class User {
    private String first_name;  // Matches JSON
}

// Option 2: Use Jackson annotation
import com.fasterxml.jackson.annotation.JsonProperty;

@Data
public class User {
    @JsonProperty("first_name")  // Maps JSON field to Java field
    private String firstName;
}
```

**Why it matters:** Field names must match JSON keys exactly, or use `@JsonProperty` annotation.

---

## 💡 Pro Tips for Automation Engineers

**Tip 1: Use Static Imports for Cleaner Code**
```java
import static io.restassured.RestAssured.*;
import static org.hamcrest.Matchers.*;
import static org.testng.Assert.*;

// Now you can use given(), equalTo(), assertEquals() directly
```

**Tip 2: Create a Base Authentication Class**
```java
public class AuthHelper {
    public static String getBearerToken() {
        return given()
            .contentType("application/json")
            .body("{\"email\":\"eve.holt@reqres.in\",\"password\":\"cityslicka\"}")
        .when()
            .post("https://reqres.in/api/login")
        .then()
            .statusCode(200)
            .extract().path("token");
    }
}

// Use in tests
@Test
public void test() {
    given()
        .auth().oauth2(AuthHelper.getBearerToken())
    .when()
        .get("/users/2");
}
```

**Tip 3: Use Builder Pattern for Complex POJOs**
```java
User user = User.builder()
    .name("John")
    .email("john@example.com")
    .age(28)
    .build();
```

**Tip 4: Log Chained API Calls**
```java
System.out.println("=== Step 1: Create User ===");
// API call

System.out.println("=== Step 2: Get User ===");
// API call

// Helps debugging complex workflows
```

**Tip 5: Extract Common Chains to Helper Methods**
```java
public int createUserAndReturnId(String name, String job) {
    return given()
        .contentType("application/json")
        .body("{\"name\":\"" + name + "\",\"job\":\"" + job + "\"}")
    .when()
        .post("https://reqres.in/api/users")
    .then()
        .statusCode(201)
        .extract().path("id");
}
```

---

## 🔍 Deep Dive: JWT Tokens - What SDETs Must Know

**What experienced developers know:**
JWT (JSON Web Token) is not encrypted, just Base64 encoded. You can decode and read its contents without the secret key.

**JWT Structure:**
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c

Part 1: Header (algorithm & token type)
Part 2: Payload (user data, expiration)
Part 3: Signature (verification)
```

**Decoding JWT in Tests:**
```java
import java.util.Base64;

public String decodeJWT(String token) {
    String[] parts = token.split("\\.");
    String payload = new String(Base64.getDecoder().decode(parts[1]));
    return payload;  // Returns JSON with user data
}

// Use in validation
@Test
public void testJWTContainsUserData() {
    String token = getTokenFromLogin();
    String payload = decodeJWT(token);

    assertTrue(payload.contains("user_id"));
    assertTrue(payload.contains("email"));
}
```

**When to use this:**
- Validate token contains correct user data
- Check token expiration time
- Debug authentication issues
- Interview question: "Have you worked with JWTs?"

---

## 📖 Key Terms Glossary

| Term | Definition | Example |
|------|------------|---------|
| **Authentication** | Proving who you are | Login with username/password |
| **Authorization** | What you're allowed to do | Admin can delete, user can only read |
| **Bearer Token** | Token sent in Authorization header | `Authorization: Bearer abc123` |
| **JWT** | JSON Web Token - self-contained token | Used by 90% of modern APIs |
| **POJO** | Plain Old Java Object - simple class | User, Product classes |
| **Serialization** | Java object → JSON | `User` object → `{"name":"John"}` |
| **Deserialization** | JSON → Java object | `{"name":"John"}` → `User` object |
| **Lombok** | Library to reduce boilerplate | `@Data` generates getters/setters |
| **Chaining** | Using response from one call in another | Create user → Update user |
| **Extraction** | Getting data from response | `extract().path("id")` |

---

## 🎓 Interview Prep

**Common Interview Questions on Advanced API Testing:**

**Q1:** How do you handle authentication in RestAssured?
**A:** "I've worked with multiple authentication methods in RestAssured. For Basic Auth, I use `.auth().basic(username, password)`. For Bearer tokens, which are more common in production, I first make a POST request to the login endpoint to get the token, extract it using `.extract().path("token")`, and then use it in subsequent requests with `.auth().oauth2(token)`. For API keys, I add them as headers using `.header("X-API-Key", key)`. I always externalize credentials using properties files or environment variables, never hardcode them."

**Example:**
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
    .get("/protected-resource");
```

---

**Q2:** What are POJOs and why use them in API testing?
**A:** "POJOs are Plain Old Java Objects - simple classes that represent our data models. Instead of working with raw JSON strings, I create POJOs that map to the API's JSON structure. This gives us type safety, IDE autocomplete, and makes tests maintainable. For example, instead of manually building a JSON string, I create a User POJO with fields matching the JSON, and RestAssured automatically serializes it. I use Lombok annotations like @Data to reduce boilerplate code. For deserialization, I convert JSON responses back to POJOs using `.extract().as(User.class)`, which makes assertions much cleaner."

**Example:**
```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private String email;
}

// Serialization
User user = new User("John", "john@example.com");
given().body(user).post("/users");

// Deserialization
User response = get("/users/1").as(User.class);
```

---

**Q3:** How do you chain multiple API calls?
**A:** "In real-world scenarios, APIs are interdependent. I chain calls by extracting data from one response and using it in the next request. For example, in an e-commerce flow: I create a user and extract the userId, then use that userId to create an order, extract the orderId, and use it to verify the order details. I use `.extract().path("id")` to extract specific values. For complex chains, I create helper methods to keep tests readable. This simulates actual user workflows and catches integration issues that isolated API tests would miss."

**Example:**
```java
// Step 1: Create user, extract ID
int userId = given()
    .body(userPayload)
.when()
    .post("/users")
.then()
    .extract().path("id");

// Step 2: Use extracted ID to create order
int orderId = given()
    .body("{\"userId\":" + userId + "}")
.when()
    .post("/orders")
.then()
    .extract().path("id");

// Step 3: Verify order
get("/orders/" + orderId)
    .then()
    .body("userId", equalTo(userId));
```

---

**Q4:** What's the difference between serialization and deserialization?
**A:** "Serialization is converting a Java object to JSON format for the request body. When I create a User object and pass it to `.body(user)`, RestAssured serializes it to JSON. Deserialization is the opposite - converting JSON response to a Java object using `.as(User.class)`. This requires the POJO to have a no-args constructor. Both processes use Jackson library under the hood. Understanding this is crucial because field names in the POJO must match JSON keys exactly, or we need to use `@JsonProperty` annotations."

---

**Q5:** How do you handle token expiration in long-running test suites?
**A:** "Token expiration is a common issue in API test automation. I implement a token refresh mechanism. I store the token in a static variable and check its validity before each test using `@BeforeMethod`. If the token is expired or about to expire, I make a fresh login call to get a new token. For JWT tokens, I can decode the payload to check the expiration time. This prevents mid-suite failures and makes tests more robust."

---

## ✅ Self-Check Questions

Before moving to practice, answer these:

1. What are the four main authentication types, and when would you use each?
2. Why do we need @NoArgsConstructor annotation on POJOs used for deserialization?
3. How would you extract a token from a login response and use it in the next request?
4. What's the difference between `.extract().path("id")` and `.extract().as(User.class)`?
5. How does Lombok's @Data annotation help in reducing code?

**Answers at the end of Practice file.**

---

**Next: Move to Day-3-Practice-Exercises.md for hands-on practice!**
