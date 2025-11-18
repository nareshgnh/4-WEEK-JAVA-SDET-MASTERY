# DAY 3 CHEAT SHEET: ADVANCED API TESTING

## ⚡ Quick Syntax Reference

### Authentication Patterns

```java
// 1. Basic Authentication
given()
    .auth().basic("username", "password")
.when()
    .get("/protected-resource")
.then()
    .statusCode(200);

// 2. Bearer Token (Most Common)
String token = "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...";

given()
    .auth().oauth2(token)
.when()
    .get("/profile")
.then()
    .statusCode(200);

// OR manually
given()
    .header("Authorization", "Bearer " + token)
.when()
    .get("/profile");

// 3. API Key (Header)
given()
    .header("X-API-Key", "your-api-key-here")
.when()
    .get("/data");

// 4. API Key (Query Parameter)
given()
    .queryParam("api_key", "your-api-key")
.when()
    .get("/data");
```

### Token Extraction and Reuse

```java
// Extract token from login response
String token = given()
    .contentType("application/json")
    .body("{\"email\":\"user@example.com\",\"password\":\"pass123\"}")
.when()
    .post("/login")
.then()
    .statusCode(200)
    .body("token", notNullValue())  // Validate before extracting
    .extract().path("token");

// Use token in next request
given()
    .auth().oauth2(token)
.when()
    .get("/users/me")
.then()
    .statusCode(200);
```

---

## 🏗️ POJO Quick Templates

### Basic POJO with Lombok

```java
import lombok.Data;
import lombok.AllArgsConstructor;
import lombok.NoArgsConstructor;

@Data                    // Generates getters, setters, toString, equals, hashCode
@AllArgsConstructor      // Constructor with all fields
@NoArgsConstructor       // Empty constructor (REQUIRED for deserialization)
public class User {
    private String name;
    private String email;
    private int age;
}

// Usage
User user = new User("John", "john@example.com", 28);
```

### POJO with Builder Pattern

```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder  // Enables fluent object creation
public class User {
    private String name;
    private String email;
    private int age;
}

// Usage - Much cleaner!
User user = User.builder()
    .name("John")
    .email("john@example.com")
    .age(28)
    .build();
```

### POJO with JSON Field Mapping

```java
import com.fasterxml.jackson.annotation.JsonProperty;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
public class User {
    private int id;

    @JsonProperty("first_name")  // Maps JSON "first_name" to Java "firstName"
    private String firstName;

    @JsonProperty("last_name")
    private String lastName;

    private String email;
}
```

### Nested POJO Example

```java
@Data
@NoArgsConstructor
public class UserResponse {
    private UserData data;
    private Support support;

    @Data
    @NoArgsConstructor
    public static class UserData {
        private int id;
        private String email;

        @JsonProperty("first_name")
        private String firstName;
    }

    @Data
    @NoArgsConstructor
    public static class Support {
        private String url;
        private String text;
    }
}
```

---

## 🔄 Serialization & Deserialization

### Serialization (Java Object → JSON)

```java
// Create POJO
User user = new User("John", "john@example.com", 28);

// RestAssured automatically serializes to JSON
given()
    .contentType("application/json")
    .body(user)  // POJO → JSON: {"name":"John","email":"john@example.com","age":28}
.when()
    .post("/users")
.then()
    .statusCode(201);
```

### Deserialization (JSON → Java Object)

```java
// Deserialize single object
User user = given()
    .get("/users/1")
.then()
    .statusCode(200)
    .extract().as(User.class);  // JSON → User object

System.out.println(user.getName());  // Access via Java object

// Deserialize array
User[] users = given()
    .get("/users")
.then()
    .statusCode(200)
    .extract().as(User[].class);  // JSON array → User[]

// Or as List
List<User> userList = Arrays.asList(
    given().get("/users").then().extract().as(User[].class)
);
```

---

## 📤 Response Extraction Patterns

### Extract Single Value

```java
// Extract primitive values
int userId = get("/users/1").then().extract().path("id");
String email = get("/users/1").then().extract().path("data.email");
boolean active = get("/users/1").then().extract().path("active");

// With validation before extraction
String token = given()
    .body(loginPayload)
.when()
    .post("/login")
.then()
    .statusCode(200)
    .body("token", notNullValue())  // Validate first
    .extract().path("token");
```

### Extract from Arrays

```java
// First element
String firstName = get("/users").then().extract().path("data[0].first_name");

// All emails as List
List<String> emails = get("/users").then().extract().path("data.email");

// All IDs as List
List<Integer> ids = get("/users").then().extract().path("data.id");

// Iterate over extracted list
emails.forEach(email -> System.out.println(email));
```

### Extract Entire Response

```java
// Extract as Response object
Response response = get("/users/1").then().extract().response();

// Use multiple times
int statusCode = response.statusCode();
String body = response.asString();
String name = response.path("data.first_name");
Headers headers = response.headers();

// Pretty print for debugging
System.out.println(response.prettyPrint());
```

---

## 🔗 API Chaining Patterns

### Pattern 1: Sequential Operations (Create → Read → Update → Delete)

```java
@Test
public void testCRUDWorkflow() {
    String baseUri = "https://api.example.com";

    // CREATE
    String userId = given()
        .baseUri(baseUri)
        .body(userPayload)
    .when()
        .post("/users")
    .then()
        .statusCode(201)
        .body("id", notNullValue())
        .extract().path("id");

    System.out.println("Created user: " + userId);

    // READ
    given()
        .baseUri(baseUri)
    .when()
        .get("/users/" + userId)
    .then()
        .statusCode(200)
        .body("id", equalTo(userId));

    // UPDATE
    given()
        .baseUri(baseUri)
        .body(updatedPayload)
    .when()
        .put("/users/" + userId)
    .then()
        .statusCode(200);

    // DELETE
    given()
        .baseUri(baseUri)
    .when()
        .delete("/users/" + userId)
    .then()
        .statusCode(204);
}
```

### Pattern 2: Authentication Chain (Login → Use Token)

```java
@Test
public void testAuthenticationChain() {
    // Step 1: Login
    String token = given()
        .body(loginPayload)
    .when()
        .post("/login")
    .then()
        .statusCode(200)
        .extract().path("token");

    // Step 2: Use token
    given()
        .auth().oauth2(token)
    .when()
        .get("/protected-resource")
    .then()
        .statusCode(200);
}
```

### Pattern 3: Multi-Step Workflow

```java
@Test
public void testComplexWorkflow() {
    // Step 1: Create product
    int productId = createProduct("Widget", 19.99);

    // Step 2: Add to cart
    int cartId = addToCart(productId, quantity: 2);

    // Step 3: Checkout
    int orderId = checkout(cartId);

    // Step 4: Verify order
    verifyOrder(orderId, productId, expectedTotal: 39.98);
}

private int createProduct(String name, double price) {
    return given()
        .body("{\"name\":\"" + name + "\",\"price\":" + price + "}")
    .when()
        .post("/products")
    .then()
        .statusCode(201)
        .extract().path("id");
}
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Code | Python Equivalent |
|---------|-----------|-------------------|
| **Basic Auth** | `.auth().basic("user", "pass")` | `auth=('user', 'pass')` |
| **Bearer Token** | `.auth().oauth2(token)` | `headers={'Authorization': f'Bearer {token}'}` |
| **Serialization** | `.body(userObject)` | `json=user_dict` |
| **Deserialization** | `.extract().as(User.class)` | `response.json()` |
| **Extract Field** | `.extract().path("id")` | `response.json()['id']` |
| **Builder Pattern** | `User.builder().name("John").build()` | `User(name="John")` |

---

## 📋 Authentication Quick Reference

| Type | When to Use | Syntax | Example APIs |
|------|-------------|--------|--------------|
| **Basic Auth** | Internal APIs, dev environments | `.auth().basic(user, pass)` | HTTPBin, internal tools |
| **Bearer Token** | Modern web apps (90% of cases) | `.auth().oauth2(token)` | ReqRes, most SaaS APIs |
| **API Key (Header)** | Third-party integrations | `.header("X-API-Key", key)` | OpenWeatherMap, Google Maps |
| **API Key (Query)** | Public APIs | `.queryParam("api_key", key)` | Some weather APIs |
| **OAuth 2.0** | Third-party login, user consent | `.auth().oauth2(accessToken)` | Google, Facebook, GitHub |

---

## ❌ Common Mistakes & Fixes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| **Missing @NoArgsConstructor** | `@Data @AllArgsConstructor` only | `@Data @AllArgsConstructor @NoArgsConstructor` |
| **Field name mismatch** | `private String firstName;` for `"first_name"` | `@JsonProperty("first_name") private String firstName;` |
| **Not validating before extraction** | `String id = extract().path("id");` | `.body("id", notNullValue()).extract().path("id");` |
| **Wrong token header** | `.header("Authorization", token)` | `.header("Authorization", "Bearer " + token)` |
| **Hardcoded credentials** | `.auth().basic("admin", "pass123")` | `.auth().basic(getUser(), getPass())` |
| **Using String for numbers** | `private String age;` → `"age":"28"` | `private int age;` → `"age":28` |

---

## 💡 Essential Lombok Annotations

```java
// Basic POJO
@Data                   // All: getters, setters, toString, equals, hashCode
@NoArgsConstructor      // User()
@AllArgsConstructor     // User(String name, String email)

// Builder Pattern
@Builder                // User.builder().name("John").build()

// Individual annotations (if you don't want @Data)
@Getter                 // Generate getters only
@Setter                 // Generate setters only
@ToString               // Generate toString()
@EqualsAndHashCode      // Generate equals() and hashCode()

// Required args constructor
@RequiredArgsConstructor  // Constructor for @NonNull and final fields
```

**Maven Dependency:**
```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.30</version>
    <scope>provided</scope>
</dependency>
```

---

## 🎯 JSON Path Expression Cheat Sheet

```java
// Root level field
.path("name")                    // {"name": "John"} → "John"

// Nested object
.path("data.email")              // {"data": {"email": "..."}} → email value

// Array index
.path("data[0].name")            // First element's name
.path("data[1].id")              // Second element's id

// Array field (returns List)
.path("data.email")              // All email fields as List<String>

// Array size
.path("data.size()")             // Number of elements in array

// Find with condition
.path("data.find { it.id == 2 }") // Find object where id == 2

// Filter
.path("data.findAll { it.age > 18 }") // All objects where age > 18
```

---

## 🚀 Advanced Patterns

### Helper Method for Authentication

```java
public class AuthHelper {
    private static String cachedToken = null;

    public static String getToken() {
        if (cachedToken != null) {
            return cachedToken;
        }

        cachedToken = given()
            .body(loginPayload)
        .when()
            .post("/login")
        .then()
            .extract().path("token");

        return cachedToken;
    }
}

// Usage
given()
    .auth().oauth2(AuthHelper.getToken())
.when()
    .get("/profile");
```

### Reusable CRUD Helper

```java
public class UserApiHelper {
    public static String createUser(User user) {
        return given()
            .body(user)
        .when()
            .post("/users")
        .then()
            .statusCode(201)
            .extract().path("id");
    }

    public static User getUser(String id) {
        return given()
            .get("/users/" + id)
        .then()
            .statusCode(200)
            .extract().as(User.class);
    }
}
```

### RequestSpecification Pattern

```java
public class BaseTest {
    protected static RequestSpecification requestSpec;

    @BeforeClass
    public void setup() {
        requestSpec = new RequestSpecBuilder()
            .setBaseUri("https://api.example.com")
            .setContentType(ContentType.JSON)
            .addHeader("Authorization", "Bearer " + getToken())
            .build();
    }
}

// Usage in tests
given()
    .spec(requestSpec)
.when()
    .get("/users")
.then()
    .statusCode(200);
```

---

## 🎤 Interview Phrases - Advanced API Testing

### Authentication

**Question:** "How do you handle authentication in API testing?"

**Answer:**
*"I've implemented multiple authentication methods in RestAssured. For Basic Auth, I use `.auth().basic(username, password)`. For modern APIs using Bearer tokens, I first make a POST request to the login endpoint, extract the token using `.extract().path("token")`, validate it's not null, and then use it in subsequent requests with `.auth().oauth2(token)`. I implement token caching in a helper class to avoid repeated login calls. For API keys, I add them as headers or query parameters. I always externalize credentials using environment variables or properties files for security."*

**Code Example to Share:**
```java
// Token extraction and reuse
String token = given()
    .body(loginRequest)
.when()
    .post("/login")
.then()
    .body("token", notNullValue())
    .extract().path("token");

given()
    .auth().oauth2(token)
.when()
    .get("/protected-resource");
```

---

### POJOs and Serialization

**Question:** "What are POJOs and how do you use them in API automation?"

**Answer:**
*"POJOs are Plain Old Java Objects that represent our data models. Instead of building JSON strings manually, I create POJOs with fields matching the API's JSON structure. I use Lombok annotations like `@Data`, `@Builder`, and `@NoArgsConstructor` to reduce boilerplate code. For serialization, I pass the POJO to `.body(userObject)` and RestAssured automatically converts it to JSON. For deserialization, I use `.extract().as(User.class)` to convert JSON responses back to Java objects. This approach gives us type safety, IDE autocomplete, and makes tests much more maintainable than working with raw JSON strings."*

**Code Example:**
```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class User {
    private String name;
    private String email;
}

// Serialization
User user = User.builder().name("John").email("john@example.com").build();
given().body(user).post("/users");

// Deserialization
User response = get("/users/1").as(User.class);
```

---

### API Chaining

**Question:** "How do you handle dependent API calls?"

**Answer:**
*"In real-world scenarios, APIs are often interdependent. I chain calls by extracting data from one response and using it in the next request. For example, in an e-commerce flow: I create a user and extract the userId, use that to create an order and extract orderId, then use orderId to verify the order. I always validate extracted values are not null before using them to prevent cascading failures. For complex workflows, I create helper methods to keep tests readable and maintainable. I also implement proper cleanup in `@AfterMethod` to delete test data."*

**Code Example:**
```java
// Create user → Get user → Update user → Delete user
String userId = createUser(userData);
assertNotNull(userId);

User user = getUser(userId);
assertEquals(user.getId(), userId);

updateUser(userId, updatedData);
deleteUser(userId);
```

---

### Debugging

**Question:** "How do you debug failing API tests?"

**Answer:**
*"I follow a systematic debugging approach. First, I check the status code - 401 means auth issue, 400 means bad request, 404 means endpoint not found. I use `.log().all()` to see full request and response. For extraction issues, I validate extracted values with assertions before using them. For POJO deserialization problems, I verify field names match JSON keys exactly using `.log().body()`, and ensure the POJO has `@NoArgsConstructor`. I also print extracted values and add meaningful log statements to trace test flow, especially in chained operations where one failure can cascade."*

---

## 📌 Remember This

**The ONE thing to remember from Day 3:**

> **Modern API automation uses POJOs + Authentication + Chaining. Master these three, and you can automate any REST API in production.**

---

## 🔮 Tomorrow's Preview

**Day 4 Topic:** Test Data Management & Schema Validation

**What you'll learn:**
- JSON Schema validation
- Data-driven API testing
- Test data generators (Faker, random data)
- Database integration for data setup
- Performance testing basics

**What to review tonight:**
- Practice creating POJOs for complex JSON structures
- Review authentication patterns
- Think about: How would you validate API response structure (not just data)?

**What to prepare:**
- Ensure all Day 3 exercises are working
- Push your Day 3 project to GitHub
- Review Lombok annotations

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- ✅ Mastered authentication (Basic, Bearer Token, OAuth, API Keys)
- ✅ Created POJOs with Lombok for clean code
- ✅ Implemented serialization and deserialization
- ✅ Extracted and manipulated complex response data
- ✅ Chained multiple API calls in workflows
- ✅ Built production-ready API framework
- ✅ 2.5-3 hours of advanced API practice

**Cumulative Progress:**
- Days completed: 3/7 (Week 4)
- Topics mastered: API Basics, RestAssured Deep Dive, Advanced API Testing
- Tests written: 30+ comprehensive API tests
- Portfolio projects: User Management API Framework

**Week 4 Progress: 43% Complete** 🎯

```
Week 4 Progress Bar:
[████████████░░░░░░░░░░░░░░░░] 43%

Day 1: ✅ API Testing Basics
Day 2: ✅ RestAssured Deep Dive
Day 3: ✅ Advanced API Testing  ← YOU ARE HERE
Day 4: ⬜ Test Data & Schema Validation
Day 5: ⬜ TestNG Framework Integration
Day 6: ⬜ CI/CD Pipeline Setup
Day 7: ⬜ Complete Framework Project
```

**Momentum Statement:**

*Today you learned advanced API automation skills that most SDETs take months to master. You can now handle authentication, work with POJOs, chain complex workflows, and build maintainable frameworks. You're building production-quality code that companies like Amazon and Google use in their test automation. Tomorrow you'll add data-driven testing and schema validation - the final pieces that make you a complete API automation expert.*

**Day 3 of Week 4 = You're 57% away from being a full-stack SDET. The 25 LPA offers are coming!** 💪🚀

---

## 📚 Quick Reference Card (Print This!)

```
╔══════════════════════════════════════════════════════════════════╗
║           ADVANCED API TESTING CHEAT SHEET - DAY 3               ║
╠══════════════════════════════════════════════════════════════════╣
║ AUTHENTICATION:                                                  ║
║   Basic:  .auth().basic("user", "pass")                          ║
║   Bearer: .auth().oauth2(token)                                  ║
║   API Key: .header("X-API-Key", key)                             ║
║                                                                  ║
║ POJO (with Lombok):                                              ║
║   @Data @AllArgsConstructor @NoArgsConstructor                   ║
║   public class User {                                            ║
║       private String name;                                       ║
║       private String email;                                      ║
║   }                                                              ║
║                                                                  ║
║ SERIALIZATION (Java → JSON):                                     ║
║   User user = new User("John", "john@example.com");              ║
║   given().body(user).post("/users");                             ║
║                                                                  ║
║ DESERIALIZATION (JSON → Java):                                   ║
║   User user = get("/users/1").as(User.class);                    ║
║                                                                  ║
║ EXTRACTION:                                                      ║
║   Single: .extract().path("id")                                  ║
║   Array:  .extract().path("data.email")  // List<String>         ║
║   Object: .extract().as(User.class)                              ║
║                                                                  ║
║ CHAINING:                                                        ║
║   1. Create user → extract ID                                    ║
║   2. Use ID in next request                                      ║
║   3. Always validate: assertNotNull(id)                          ║
║                                                                  ║
║ BUILDER PATTERN:                                                 ║
║   User user = User.builder()                                     ║
║       .name("John")                                              ║
║       .email("john@example.com")                                 ║
║       .build();                                                  ║
║                                                                  ║
║ CRITICAL RULE:                                                   ║
║   POJOs MUST have @NoArgsConstructor for deserialization!        ║
║                                                                  ║
║ DEBUGGING:                                                       ║
║   .log().all()           // See request & response               ║
║   .log().ifError()       // Log only on failure                  ║
║   .body("id", notNullValue())  // Validate before extract        ║
╚══════════════════════════════════════════════════════════════════╝
```

---

## 🏆 Skills Unlocked Today

**Technical Skills:**
- ✅ Authentication implementation (4 types)
- ✅ POJO design with Lombok
- ✅ JSON serialization/deserialization
- ✅ Complex response extraction
- ✅ API call chaining
- ✅ Framework architecture
- ✅ Token management
- ✅ Builder pattern usage

**Professional Skills:**
- ✅ Production-ready code structure
- ✅ Debugging complex API issues
- ✅ Framework design principles
- ✅ Best practices for maintainability
- ✅ Interview-ready explanations

**Portfolio Pieces:**
- ✅ User Management API Framework (GitHub-ready)
- ✅ Authentication helper classes
- ✅ Reusable POJO models
- ✅ E2E workflow tests

---

**You're now ready to automate ANY REST API. Tomorrow: Data-driven testing and schema validation!** 🔥

**Keep this cheat sheet handy during coding. Review it before interviews. You've got this!** 💪
