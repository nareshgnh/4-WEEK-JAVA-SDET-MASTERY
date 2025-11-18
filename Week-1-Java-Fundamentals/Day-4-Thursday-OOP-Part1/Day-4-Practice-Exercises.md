# DAY 4 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master creating classes, objects, constructors, and encapsulation by building real-world objects similar to Page Object Model components.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Create a Simple Book Class
**Objective:** Understand basic class structure with instance variables and methods

**Task:**
Create a `Book` class with title, author, and pages. Add a method to display book information.

**Starter Code:**
```java
public class Book {
    // TODO: Add instance variables for title, author, and pages

    // TODO: Add a method displayInfo() that prints book details

}

public class Exercise1 {
    public static void main(String[] args) {
        // TODO: Create a Book object
        // TODO: Set its properties
        // TODO: Call displayInfo()

    }
}
```

**Expected Output:**
```
Title: Clean Code
Author: Robert Martin
Pages: 464
```

**Hints:**
- Instance variables should be declared at class level (outside methods)
- Use `public void displayInfo()` for the method
- Access instance variables directly using the object: `book.title = "Clean Code";`

---

### Exercise 2: Create a WebElement Class
**Objective:** Practice creating classes relevant to automation

**Task:**
Create a `WebElement` class to represent a web element with locator type, locator value, and element name. Display the element information.

**Starter Code:**
```java
public class WebElement {
    // TODO: Add instance variables
    String locatorType;    // e.g., "id", "css", "xpath"
    String locatorValue;   // e.g., "#username", "//input[@name='user']"
    String elementName;    // e.g., "Username Field"

    // TODO: Add displayInfo() method

}

public class Exercise2 {
    public static void main(String[] args) {
        // TODO: Create a WebElement for username field
        // Locator Type: "id"
        // Locator Value: "username"
        // Element Name: "Username Input Field"

    }
}
```

**Expected Output:**
```
Element: Username Input Field
Locator Type: id
Locator Value: username
```

**Hints:**
- Think about how you identify elements in Selenium
- The displayInfo() method should print all three properties

---

### Exercise 3: Using Constructors
**Objective:** Learn to create and use parameterized constructors

**Task:**
Modify the Book class from Exercise 1 to use a constructor that initializes all properties when the object is created.

**Starter Code:**
```java
public class Book {
    String title;
    String author;
    int pages;

    // TODO: Create a constructor that takes title, author, and pages as parameters
    // Hint: public Book(String title, String author, int pages) { ... }

    public void displayInfo() {
        System.out.println("Title: " + title);
        System.out.println("Author: " + author);
        System.out.println("Pages: " + pages);
    }
}

public class Exercise3 {
    public static void main(String[] args) {
        // TODO: Create a Book object using the constructor
        // Book book = new Book("Clean Code", "Robert Martin", 464);

    }
}
```

**Expected Output:**
```
Title: Clean Code
Author: Robert Martin
Pages: 464
```

**Hints:**
- Constructor has the same name as the class
- Use `this.title = title;` to assign parameter to instance variable
- Constructor has no return type

---

## 💪 Core Practice (30 min)

### Exercise 4: TestCase Class with Multiple Constructors
**Objective:** Practice constructor overloading and the `this` keyword

**Problem Statement:**
Create a `TestCase` class that can be initialized with different amounts of information. Some test cases might have just a name, others might have name and description, and some might have name, description, and priority.

**Requirements:**
- Create instance variables: `name`, `description`, `priority`, `status`
- Create three constructors:
  1. Constructor with only name (set description to "No description", priority to "Medium", status to "Not Run")
  2. Constructor with name and description (set priority to "Medium", status to "Not Run")
  3. Constructor with name, description, and priority (set status to "Not Run")
- Create a method `displayTestCase()` to show all information
- Create a method `markPassed()` that sets status to "Passed"
- Create a method `markFailed()` that sets status to "Failed"

**Starter Code:**
```java
public class TestCase {
    String name;
    String description;
    String priority;
    String status;

    // TODO: Constructor 1 - only name
    public TestCase(String name) {
        // Set all properties
    }

    // TODO: Constructor 2 - name and description
    public TestCase(String name, String description) {
        // Set all properties
    }

    // TODO: Constructor 3 - name, description, and priority
    public TestCase(String name, String description, String priority) {
        // Set all properties
    }

    // TODO: Add displayTestCase() method

    // TODO: Add markPassed() method

    // TODO: Add markFailed() method

}

public class Exercise4 {
    public static void main(String[] args) {
        // TODO: Create three test cases using different constructors

    }
}
```

**Test Cases:**
```
Test Case 1:
Input: new TestCase("Login Test")
Expected Output:
Name: Login Test
Description: No description
Priority: Medium
Status: Not Run

Test Case 2:
Input: new TestCase("Login Test", "Verify user can login with valid credentials")
Expected Output:
Name: Login Test
Description: Verify user can login with valid credentials
Priority: Medium
Status: Not Run

Test Case 3:
Input: new TestCase("Login Test", "Verify user can login", "High")
Then call: markPassed()
Expected Output:
Name: Login Test
Description: Verify user can login
Priority: High
Status: Passed
```

**Hints:**
- Use `this.name = name;` to distinguish instance variable from parameter
- Constructor overloading allows multiple constructors with different parameters
- Each constructor sets default values for properties not provided

---

### Exercise 5: Encapsulation with Browser Configuration
**Objective:** Implement proper encapsulation with private variables and getters/setters

**Problem Statement:**
Create a `BrowserConfig` class that stores browser configuration with validation. The class should protect its data and ensure only valid values are set.

**Requirements:**
- Private instance variables: `browserName`, `headless`, `timeout`
- Constructor that takes browserName (set headless to false, timeout to 10 by default)
- Getter methods for all variables
- Setter for browserName that only accepts "chrome", "firefox", "edge", "safari" (case-insensitive)
- Setter for timeout that only accepts values between 5 and 60
- Setter for headless (boolean, no validation needed)
- Method `displayConfig()` to show current configuration

**Starter Code:**
```java
public class BrowserConfig {
    // TODO: Add private instance variables

    // TODO: Add constructor
    public BrowserConfig(String browserName) {

    }

    // TODO: Add getter for browserName

    // TODO: Add getter for headless

    // TODO: Add getter for timeout

    // TODO: Add setter for browserName with validation
    public void setBrowserName(String browserName) {
        // Validate browser name
        // Only accept: chrome, firefox, edge, safari (case-insensitive)
        // If invalid, print error and keep current value
    }

    // TODO: Add setter for timeout with validation
    public void setTimeout(int timeout) {
        // Only accept values between 5 and 60
        // If invalid, print error and keep current value
    }

    // TODO: Add setter for headless

    // TODO: Add displayConfig() method

}

public class Exercise5 {
    public static void main(String[] args) {
        BrowserConfig config = new BrowserConfig("chrome");
        config.displayConfig();

        // Test valid changes
        config.setTimeout(30);
        config.setHeadless(true);
        config.displayConfig();

        // Test invalid changes
        config.setBrowserName("internet explorer");  // Should print error
        config.setTimeout(100);                       // Should print error
        config.displayConfig();                       // Should show previous valid values
    }
}
```

**Expected Output:**
```
Browser Configuration:
Browser: chrome
Headless Mode: false
Timeout: 10 seconds

Browser Configuration:
Browser: chrome
Headless Mode: true
Timeout: 30 seconds

Error: Invalid browser name. Valid options: chrome, firefox, edge, safari
Error: Timeout must be between 5 and 60 seconds

Browser Configuration:
Browser: chrome
Headless Mode: true
Timeout: 30 seconds
```

**Hints:**
- Use `private` keyword for instance variables
- Create getter: `public String getBrowserName() { return browserName; }`
- Create setter with validation: check condition before setting value
- Use `toLowerCase()` to handle case-insensitive browser names

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: User Class with Method Chaining
**Objective:** Build a class that demonstrates method chaining (fluent pattern)

**Problem Statement:**
Create a `User` class that represents a user account. Implement methods that return the current object to enable method chaining (similar to how Selenium PageFactory works).

**Requirements:**
- Private instance variables: `username`, `email`, `password`, `age`, `country`
- Constructor that takes username
- Methods that set each property and return `this`:
  - `setEmail(String email)` - validates email contains "@"
  - `setPassword(String password)` - validates password is at least 8 characters
  - `setAge(int age)` - validates age is between 18 and 120
  - `setCountry(String country)` - no validation
- Method `displayUser()` that prints all user information (doesn't return anything)
- All setter methods should return the User object for chaining

**Starter Code:**
```java
public class User {
    private String username;
    private String email;
    private String password;
    private int age;
    private String country;

    // TODO: Constructor
    public User(String username) {
        this.username = username;
    }

    // TODO: Implement setEmail with validation and return this
    public User setEmail(String email) {
        // Validate email contains "@"
        // If valid, set email
        // Return this
    }

    // TODO: Implement setPassword with validation and return this
    public User setPassword(String password) {
        // Validate password length >= 8
        // If valid, set password
        // Return this
    }

    // TODO: Implement setAge with validation and return this

    // TODO: Implement setCountry and return this

    // TODO: Implement displayUser() - doesn't return anything
    public void displayUser() {
        System.out.println("=== User Information ===");
        System.out.println("Username: " + username);
        System.out.println("Email: " + email);
        System.out.println("Password: " + "*".repeat(password.length())); // Hide password
        System.out.println("Age: " + age);
        System.out.println("Country: " + country);
    }

    // TODO: Add getters for all private variables
}

public class Exercise6 {
    public static void main(String[] args) {
        // TODO: Create user and chain method calls
        User user = new User("john_doe")
                .setEmail("john@example.com")
                .setPassword("securePass123")
                .setAge(25)
                .setCountry("USA");

        user.displayUser();

        // Test validation
        System.out.println("\n--- Testing Validation ---");
        User invalidUser = new User("jane_doe")
                .setEmail("invalidemail")        // Should print error
                .setPassword("short")             // Should print error
                .setAge(150)                      // Should print error
                .setCountry("UK");

        invalidUser.displayUser();
    }
}
```

**Expected Output:**
```
=== User Information ===
Username: john_doe
Email: john@example.com
Password: ********
Age: 25
Country: USA

--- Testing Validation ---
Error: Email must contain @
Error: Password must be at least 8 characters
Error: Age must be between 18 and 120
=== User Information ===
Username: jane_doe
Email: null
Password: null
Age: 0
Country: UK
```

**Hints:**
- Return `this` from setter methods to enable chaining
- Use `return this;` at the end of each setter
- Validation: `if (email.contains("@"))` for email check
- Validation: `if (password.length() >= 8)` for password check

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
public class Book {
    String title;
    String author;
    int pages;

    public void displayInfo() {
        System.out.println("Title: " + title);
        System.out.println("Author: " + author);
        System.out.println("Pages: " + pages);
    }
}

public class Exercise1 {
    public static void main(String[] args) {
        Book book = new Book();
        book.title = "Clean Code";
        book.author = "Robert Martin";
        book.pages = 464;
        book.displayInfo();
    }
}
```

**Explanation:**
- Instance variables (`title`, `author`, `pages`) are declared at class level
- `displayInfo()` is an instance method that accesses instance variables
- Object is created using `new Book()`
- Properties are set using dot notation: `book.title = "Clean Code"`
- Method is called using dot notation: `book.displayInfo()`

**Key Takeaways:**
- Classes are blueprints, objects are instances
- Instance variables store object state
- Instance methods define object behavior
- Each object has its own copy of instance variables

---

### Exercise 2 Solution
```java
public class WebElement {
    String locatorType;
    String locatorValue;
    String elementName;

    public void displayInfo() {
        System.out.println("Element: " + elementName);
        System.out.println("Locator Type: " + locatorType);
        System.out.println("Locator Value: " + locatorValue);
    }
}

public class Exercise2 {
    public static void main(String[] args) {
        WebElement usernameField = new WebElement();
        usernameField.locatorType = "id";
        usernameField.locatorValue = "username";
        usernameField.elementName = "Username Input Field";
        usernameField.displayInfo();
    }
}
```

**Explanation:**
- This class models a web element similar to Selenium's element representation
- `locatorType` represents how we find the element (id, css, xpath)
- `locatorValue` is the actual selector string
- `elementName` is a descriptive name for reporting
- This is a simplified version of what happens in Page Object Model

**Key Takeaways:**
- Class design should reflect real-world entities
- In automation, classes often represent pages, elements, or test data
- Meaningful naming makes code self-documenting

---

### Exercise 3 Solution
```java
public class Book {
    String title;
    String author;
    int pages;

    // Constructor
    public Book(String title, String author, int pages) {
        this.title = title;
        this.author = author;
        this.pages = pages;
    }

    public void displayInfo() {
        System.out.println("Title: " + title);
        System.out.println("Author: " + author);
        System.out.println("Pages: " + pages);
    }
}

public class Exercise3 {
    public static void main(String[] args) {
        Book book = new Book("Clean Code", "Robert Martin", 464);
        book.displayInfo();
    }
}
```

**Explanation:**
- Constructor has same name as class: `public Book(...)`
- Constructor has no return type (not even void)
- `this.title = title` - `this.title` is instance variable, `title` is parameter
- Constructor is automatically called when using `new Book(...)`
- Object is created and initialized in one line

**Key Takeaways:**
- Constructors initialize object state
- `this` keyword distinguishes instance variables from parameters
- Parameterized constructors make object creation cleaner
- Constructor runs automatically when object is created

---

### Exercise 4 Solution
```java
public class TestCase {
    String name;
    String description;
    String priority;
    String status;

    // Constructor 1 - only name
    public TestCase(String name) {
        this.name = name;
        this.description = "No description";
        this.priority = "Medium";
        this.status = "Not Run";
    }

    // Constructor 2 - name and description
    public TestCase(String name, String description) {
        this.name = name;
        this.description = description;
        this.priority = "Medium";
        this.status = "Not Run";
    }

    // Constructor 3 - name, description, and priority
    public TestCase(String name, String description, String priority) {
        this.name = name;
        this.description = description;
        this.priority = priority;
        this.status = "Not Run";
    }

    public void displayTestCase() {
        System.out.println("Name: " + name);
        System.out.println("Description: " + description);
        System.out.println("Priority: " + priority);
        System.out.println("Status: " + status);
        System.out.println();
    }

    public void markPassed() {
        status = "Passed";
    }

    public void markFailed() {
        status = "Failed";
    }
}

public class Exercise4 {
    public static void main(String[] args) {
        // Test Case 1
        TestCase tc1 = new TestCase("Login Test");
        tc1.displayTestCase();

        // Test Case 2
        TestCase tc2 = new TestCase("Login Test", "Verify user can login with valid credentials");
        tc2.displayTestCase();

        // Test Case 3
        TestCase tc3 = new TestCase("Login Test", "Verify user can login", "High");
        tc3.markPassed();
        tc3.displayTestCase();
    }
}
```

**Explanation:**
- **Constructor Overloading**: Multiple constructors with different parameter lists
- Java determines which constructor to call based on arguments provided
- Each constructor sets appropriate default values for missing parameters
- `this.` keyword makes it clear we're setting instance variables
- Methods like `markPassed()` modify object state

**Key Takeaways:**
- Constructor overloading provides flexibility in object creation
- Default values make objects usable even with minimal information
- This pattern is common in test frameworks for test configuration
- Real-world example: TestNG's @Test annotation has optional parameters

---

### Exercise 5 Solution
```java
public class BrowserConfig {
    private String browserName;
    private boolean headless;
    private int timeout;

    public BrowserConfig(String browserName) {
        this.browserName = browserName.toLowerCase();
        this.headless = false;
        this.timeout = 10;
    }

    // Getters
    public String getBrowserName() {
        return browserName;
    }

    public boolean isHeadless() {
        return headless;
    }

    public int getTimeout() {
        return timeout;
    }

    // Setter with validation
    public void setBrowserName(String browserName) {
        String[] validBrowsers = {"chrome", "firefox", "edge", "safari"};
        String lowerCaseBrowser = browserName.toLowerCase();

        for (String valid : validBrowsers) {
            if (valid.equals(lowerCaseBrowser)) {
                this.browserName = lowerCaseBrowser;
                return;
            }
        }
        System.out.println("Error: Invalid browser name. Valid options: chrome, firefox, edge, safari");
    }

    public void setTimeout(int timeout) {
        if (timeout >= 5 && timeout <= 60) {
            this.timeout = timeout;
        } else {
            System.out.println("Error: Timeout must be between 5 and 60 seconds");
        }
    }

    public void setHeadless(boolean headless) {
        this.headless = headless;
    }

    public void displayConfig() {
        System.out.println("Browser Configuration:");
        System.out.println("Browser: " + browserName);
        System.out.println("Headless Mode: " + headless);
        System.out.println("Timeout: " + timeout + " seconds");
        System.out.println();
    }
}

public class Exercise5 {
    public static void main(String[] args) {
        BrowserConfig config = new BrowserConfig("chrome");
        config.displayConfig();

        // Test valid changes
        config.setTimeout(30);
        config.setHeadless(true);
        config.displayConfig();

        // Test invalid changes
        config.setBrowserName("internet explorer");
        config.setTimeout(100);
        config.displayConfig();
    }
}
```

**Explanation:**
- **Encapsulation**: Instance variables are `private`, accessed only through public methods
- **Getters**: Provide read access to private variables
- **Setters with Validation**: Control how values are modified
- Invalid values are rejected, protecting object integrity
- Boolean getter uses `isHeadless()` convention instead of `getHeadless()`

**Key Takeaways:**
- Encapsulation protects data from invalid states
- Validation in setters ensures data integrity
- This pattern is essential in automation frameworks for configuration management
- Private variables prevent accidental modification from outside the class

---

### Exercise 6 Solution
```java
public class User {
    private String username;
    private String email;
    private String password;
    private int age;
    private String country;

    public User(String username) {
        this.username = username;
    }

    public User setEmail(String email) {
        if (email.contains("@")) {
            this.email = email;
        } else {
            System.out.println("Error: Email must contain @");
        }
        return this;
    }

    public User setPassword(String password) {
        if (password.length() >= 8) {
            this.password = password;
        } else {
            System.out.println("Error: Password must be at least 8 characters");
        }
        return this;
    }

    public User setAge(int age) {
        if (age >= 18 && age <= 120) {
            this.age = age;
        } else {
            System.out.println("Error: Age must be between 18 and 120");
        }
        return this;
    }

    public User setCountry(String country) {
        this.country = country;
        return this;
    }

    public void displayUser() {
        System.out.println("=== User Information ===");
        System.out.println("Username: " + username);
        System.out.println("Email: " + email);
        if (password != null) {
            System.out.println("Password: " + "*".repeat(password.length()));
        } else {
            System.out.println("Password: null");
        }
        System.out.println("Age: " + age);
        System.out.println("Country: " + country);
    }

    // Getters
    public String getUsername() { return username; }
    public String getEmail() { return email; }
    public String getPassword() { return password; }
    public int getAge() { return age; }
    public String getCountry() { return country; }
}

public class Exercise6 {
    public static void main(String[] args) {
        User user = new User("john_doe")
                .setEmail("john@example.com")
                .setPassword("securePass123")
                .setAge(25)
                .setCountry("USA");

        user.displayUser();

        System.out.println("\n--- Testing Validation ---");
        User invalidUser = new User("jane_doe")
                .setEmail("invalidemail")
                .setPassword("short")
                .setAge(150)
                .setCountry("UK");

        invalidUser.displayUser();
    }
}
```

**Explanation:**
- **Method Chaining (Fluent Pattern)**: Each setter returns `this` (the current object)
- This allows chaining: `user.setEmail(...).setPassword(...).setAge(...)`
- Very readable and concise code
- Each setter still performs validation
- `displayUser()` doesn't return anything (void) so it ends the chain

**Key Takeaways:**
- Method chaining improves code readability
- Return `this` to enable chaining
- This pattern is used extensively in Selenium (PageFactory)
- Example: `driver.findElement(By.id("x")).sendKeys("text").submit();`
- Fluent pattern makes test code read like natural language

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 | ☐ | __/10 | ___ min | |
| 2 | ☐ | __/10 | ___ min | |
| 3 | ☐ | __/10 | ___ min | |
| 4 | ☐ | __/10 | ___ min | |
| 5 | ☐ | __/10 | ___ min | |
| 6 | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ minutes

**Areas I struggled with:**
- [ ] Class structure
- [ ] Constructors
- [ ] Using `this` keyword
- [ ] Encapsulation (private variables, getters/setters)
- [ ] Method chaining
- [ ] Other: ___________

**Areas I excelled at:**
- [ ] Creating classes and objects
- [ ] Writing constructors
- [ ] Using `this` keyword
- [ ] Implementing getters/setters
- [ ] Understanding method chaining
- [ ] Other: ___________

---

## ✅ Answers to Self-Check Questions (from Concept Guide)

**Q1: What happens if you don't define any constructor in a class?**
**A:** Java automatically provides a default no-argument constructor that initializes instance variables to default values (0 for numbers, null for objects, false for booleans).

**Q2: Why do we make instance variables `private` and provide `public` getters/setters?**
**A:** Encapsulation provides:
- Data protection from invalid values
- Ability to validate input in setters
- Flexibility to change internal implementation without breaking code that uses the class
- Control over read/write access (can have read-only or write-only properties)

**Q3: What's the difference between these two constructors?**
```java
// Version 1
public Student(String name) {
    name = name;  // Both refer to parameter! Instance variable unchanged
}

// Version 2
public Student(String name) {
    this.name = name;  // this.name is instance variable, name is parameter
}
```
**A:** Version 1 assigns the parameter to itself (instance variable remains null). Version 2 correctly assigns parameter to instance variable using `this`.

**Q4: How does the Page Object Model pattern use OOP concepts?**
**A:**
- **Classes**: Each page is a class (LoginPage, HomePage)
- **Instance Variables**: WebElements and locators
- **Constructors**: Initialize WebDriver and page elements
- **Methods**: User actions on the page (login, clickButton)
- **Encapsulation**: Hide locators, expose actions
- **Objects**: Create page instances in tests

**Q5: Predict the output:**
```java
public class Counter {
    private int count = 0;

    public void increment() {
        count++;
    }

    public int getCount() {
        return count;
    }
}

public class Main {
    public static void main(String[] args) {
        Counter c1 = new Counter();
        Counter c2 = new Counter();
        c1.increment();
        c1.increment();
        c2.increment();
        System.out.println(c1.getCount());  // Output: 2
        System.out.println(c2.getCount());  // Output: 1
    }
}
```
**A:** Output is `2` then `1`. Each object (c1, c2) has its own copy of instance variables. c1 was incremented twice, c2 only once. Objects are independent.

---

## 🎯 Next Steps

**After completing these exercises:**
1. Move to **Day-4-Debug-Practice.md** for debugging challenges
2. Then tackle **Day-4-Project-Guide.md** to build the BankAccount class
3. Review **Day-4-Cheat-Sheet.md** for quick reference

**Practice Time:** ___ minutes
**Confidence Level:** __/10
**Ready for debugging:** Yes / Need more practice

---
