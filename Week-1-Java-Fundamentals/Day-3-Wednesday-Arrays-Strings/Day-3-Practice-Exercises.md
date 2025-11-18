# DAY 3 PRACTICE EXERCISES

## 🎯 Today's Practice Goal
Master array manipulation and string operations through hands-on coding. By the end, you'll confidently work with test data arrays and perform string validations essential for test automation.

---

## 🏃 Warm-Up Exercises (15 min)

### Exercise 1: Array Declaration and Iteration
**Objective:** Get comfortable with array syntax and iteration

**Task:**
Create a program that stores browser names in an array and prints them with their index positions.

**Requirements:**
- Create an array with at least 5 browser names
- Use a traditional for loop to print each browser with its index
- Use an enhanced for loop to print just the browser names
- Print the total number of browsers

**Starter Code:**
```java
public class Exercise1 {
    public static void main(String[] args) {
        // TODO: Create array of browsers
        String[] browsers = {};

        // TODO: Print using traditional for loop
        System.out.println("Browsers with indices:");

        // TODO: Print using enhanced for loop
        System.out.println("\nBrowsers list:");

        // TODO: Print total count
    }
}
```

**Expected Output:**
```
Browsers with indices:
0: chrome
1: firefox
2: edge
3: safari
4: opera

Browsers list:
chrome
firefox
edge
safari
opera

Total browsers: 5
```

**Hints:**
- Use `String[] browsers = {"chrome", "firefox", ...};`
- Traditional for loop: `for (int i = 0; i < browsers.length; i++)`
- Enhanced for loop: `for (String browser : browsers)`
- Array length: `browsers.length` (no parentheses!)

---

### Exercise 2: ArrayList Basic Operations
**Objective:** Practice ArrayList add, remove, and access operations

**Task:**
Create a test case manager using ArrayList to add, remove, and display test cases.

**Starter Code:**
```java
import java.util.ArrayList;

public class Exercise2 {
    public static void main(String[] args) {
        // TODO: Create ArrayList of test case names
        ArrayList<String> testCases = new ArrayList<>();

        // TODO: Add 5 test cases

        // TODO: Print all test cases with numbers

        // TODO: Remove a test case at index 2

        // TODO: Add a new test case at specific position (index 1)

        // TODO: Print updated list

        // TODO: Check if specific test exists
    }
}
```

**Expected Output:**
```
Initial Test Cases:
1. Verify Login
2. Verify Logout
3. Verify Search
4. Verify Cart
5. Verify Checkout

After removing index 2:
1. Verify Login
2. Verify Logout
3. Verify Cart
4. Verify Checkout

After adding at index 1:
1. Verify Login
2. Verify Registration
3. Verify Logout
4. Verify Cart
5. Verify Checkout

Contains 'Verify Cart': true
```

**Hints:**
- Add element: `testCases.add("Test Name");`
- Add at position: `testCases.add(index, "Test Name");`
- Remove: `testCases.remove(index);`
- Check existence: `testCases.contains("Test Name");`

---

### Exercise 3: String Manipulation Basics
**Objective:** Practice common string methods

**Task:**
Write a program that processes test user email addresses.

**Starter Code:**
```java
public class Exercise3 {
    public static void main(String[] args) {
        String email = "  JOHN.DOE@TESTMAIL.COM  ";

        // TODO: Remove leading/trailing spaces

        // TODO: Convert to lowercase

        // TODO: Extract username (before @)

        // TODO: Extract domain (after @)

        // TODO: Check if email contains "@" and "."

        // TODO: Replace domain

        // Print all results
    }
}
```

**Expected Output:**
```
Original: "  JOHN.DOE@TESTMAIL.COM  "
Trimmed: "JOHN.DOE@TESTMAIL.COM"
Lowercase: "john.doe@testmail.com"
Username: john.doe
Domain: testmail.com
Valid format: true
New email: john.doe@example.com
```

**Hints:**
- Trim: `email.trim()`
- Lowercase: `email.toLowerCase()`
- Find @ position: `email.indexOf("@")`
- Substring: `email.substring(startIndex, endIndex)` or `email.substring(startIndex)`
- Replace: `email.replace("old", "new")`

---

## 💪 Core Practice (30 min)

### Exercise 4: Test Data Array Processor
**Objective:** Process and validate test data using arrays

**Problem Statement:**
Create a program that processes login test data. You have arrays of usernames and passwords. Find valid pairs and count successful/failed login attempts.

**Requirements:**
- Create parallel arrays for usernames and passwords
- Create an array of expected results (boolean)
- Process each username/password pair
- Count and display statistics
- Validate that username is not empty and password length >= 6

**Starter Code:**
```java
public class Exercise4 {
    public static void main(String[] args) {
        // Test data
        String[] usernames = {"john@test.com", "", "jane@test.com", "bob@test.com"};
        String[] passwords = {"pass123", "12345", "secure456", "weak"};
        boolean[] expectedResults = {true, false, true, false};

        int validLogins = 0;
        int invalidLogins = 0;

        System.out.println("===== Login Test Data Processor =====\n");

        // TODO: Process each login attempt
        for (int i = 0; i < usernames.length; i++) {
            // TODO: Validate username (not empty)
            // TODO: Validate password (length >= 6)
            // TODO: Determine if login should succeed
            // TODO: Compare with expected result
            // TODO: Update counters
            // TODO: Print result
        }

        // TODO: Print statistics
        System.out.println("\n===== Statistics =====");
        System.out.println("Valid logins: " + validLogins);
        System.out.println("Invalid logins: " + invalidLogins);
    }
}
```

**Expected Output:**
```
===== Login Test Data Processor =====

Test 1: john@test.com / pass123
  Username valid: true
  Password valid: true
  Should succeed: true
  Expected: true
  ✓ Test Passed

Test 2:  / 12345
  Username valid: false
  Password valid: false
  Should succeed: false
  Expected: false
  ✓ Test Passed

Test 3: jane@test.com / secure456
  Username valid: true
  Password valid: true
  Should succeed: true
  Expected: true
  ✓ Test Passed

Test 4: bob@test.com / weak
  Username valid: true
  Password valid: false
  Should succeed: false
  Expected: false
  ✓ Test Passed

===== Statistics =====
Valid logins: 2
Invalid logins: 2
All tests passed: 4/4
```

**Hints:**
- Check empty string: `username.isEmpty()` or `username.equals("")`
- Check password length: `password.length() >= 6`
- Login succeeds if: username not empty AND password length >= 6
- Compare result: `actualResult == expectedResults[i]`

---

### Exercise 5: String Array Search and Filter
**Objective:** Search and filter arrays based on string conditions

**Problem Statement:**
Create a test case filter that searches through test case names and filters them based on criteria (contains keyword, starts with prefix, etc.).

**Requirements:**
- Create array of test case names
- Implement search by keyword (case-insensitive)
- Filter test cases that start with specific prefix
- Count test cases containing specific word
- Display results using ArrayList for dynamic storage

**Starter Code:**
```java
import java.util.ArrayList;

public class Exercise5 {
    public static void main(String[] args) {
        String[] allTests = {
            "Login with valid credentials",
            "Login with invalid password",
            "Logout after successful login",
            "Search for product by name",
            "Search for product by category",
            "Add product to cart",
            "Remove product from cart",
            "Checkout with credit card",
            "Checkout with PayPal"
        };

        // TODO: Find all test cases containing "login" (case-insensitive)
        System.out.println("===== Tests containing 'login' =====");
        ArrayList<String> loginTests = new ArrayList<>();
        // Your code here

        // TODO: Find all test cases starting with "Search"
        System.out.println("\n===== Tests starting with 'Search' =====");
        ArrayList<String> searchTests = new ArrayList<>();
        // Your code here

        // TODO: Count test cases containing "product"
        System.out.println("\n===== Tests containing 'product' =====");
        int productTestCount = 0;
        // Your code here

        // TODO: Find all checkout tests and store in ArrayList
        System.out.println("\n===== Checkout Tests =====");
        ArrayList<String> checkoutTests = new ArrayList<>();
        // Your code here
    }
}
```

**Expected Output:**
```
===== Tests containing 'login' =====
1. Login with valid credentials
2. Login with invalid password
3. Logout after successful login
Total: 3 tests

===== Tests starting with 'Search' =====
1. Search for product by name
2. Search for product by category
Total: 2 tests

===== Tests containing 'product' =====
1. Search for product by name
2. Search for product by category
3. Add product to cart
4. Remove product from cart
Total: 4 tests

===== Checkout Tests =====
1. Checkout with credit card
2. Checkout with PayPal
Total: 2 tests
```

**Hints:**
- Case-insensitive contains: `testName.toLowerCase().contains("login")`
- Starts with: `testName.startsWith("Search")`
- Add to ArrayList: `list.add(testName)`
- Print ArrayList size: `list.size()`

---

## 🚀 Challenge Exercise (15 min)

### Exercise 6: URL Parser and Validator
**Objective:** Build a URL parser using string methods to extract components

**Problem Statement:**
Create a URL validator and parser that breaks down test URLs into protocol, domain, path, and query parameters.

**Requirements:**
- Parse URL into components: protocol, domain, path, query string
- Validate URL structure (must have protocol and domain)
- Extract query parameters into key-value pairs
- Support multiple test URLs in an array
- Display parsed information in organized format

**Starter Code:**
```java
public class Exercise6 {
    public static void main(String[] args) {
        String[] testUrls = {
            "https://www.example.com/login",
            "https://www.shop.com/products/laptop?id=123&color=silver",
            "http://test.local/api/users?page=2&limit=10",
            "www.invalid.com/page"  // Invalid - no protocol
        };

        System.out.println("===== URL PARSER AND VALIDATOR =====\n");

        for (int i = 0; i < testUrls.length; i++) {
            String url = testUrls[i];
            System.out.println("URL " + (i + 1) + ": " + url);

            // TODO: Check if URL has protocol (http:// or https://)
            boolean hasProtocol = false;

            if (!hasProtocol) {
                System.out.println("  ✗ Invalid: Missing protocol\n");
                continue;
            }

            // TODO: Extract protocol (http or https)
            String protocol = "";

            // TODO: Extract domain (between :// and next /)
            String domain = "";

            // TODO: Extract path (from domain to ? or end)
            String path = "";

            // TODO: Extract query string (after ? if exists)
            String queryString = "";

            // TODO: Display parsed components
            System.out.println("  ✓ Valid URL");
            System.out.println("  Protocol: " + protocol);
            System.out.println("  Domain: " + domain);
            System.out.println("  Path: " + path);
            if (!queryString.isEmpty()) {
                System.out.println("  Query: " + queryString);

                // BONUS: Parse query parameters
                // Split by & and then by =
            }
            System.out.println();
        }
    }
}
```

**Expected Output:**
```
===== URL PARSER AND VALIDATOR =====

URL 1: https://www.example.com/login
  ✓ Valid URL
  Protocol: https
  Domain: www.example.com
  Path: /login
  Query: none

URL 2: https://www.shop.com/products/laptop?id=123&color=silver
  ✓ Valid URL
  Protocol: https
  Domain: www.shop.com
  Path: /products/laptop
  Query: id=123&color=silver
    Parameter: id = 123
    Parameter: color = silver

URL 3: http://test.local/api/users?page=2&limit=10
  ✓ Valid URL
  Protocol: http
  Domain: test.local
  Path: /api/users
  Query: page=2&limit=10
    Parameter: page = 2
    Parameter: limit = 10

URL 4: www.invalid.com/page
  ✗ Invalid: Missing protocol
```

**Hints:**
- Check protocol: `url.startsWith("http://") || url.startsWith("https://")`
- Extract protocol: `url.substring(0, url.indexOf("://"))`
- Find domain start: `url.indexOf("://") + 3`
- Find domain end: `url.indexOf("/", domainStart)`
- Check for query: `url.contains("?")`
- Split query: `queryString.split("&")` then split each by `"="`

---

## 🎯 Solutions & Explanations

### Exercise 1 Solution
```java
public class Exercise1 {
    public static void main(String[] args) {
        // Create array of browsers
        String[] browsers = {"chrome", "firefox", "edge", "safari", "opera"};

        // Print using traditional for loop
        System.out.println("Browsers with indices:");
        for (int i = 0; i < browsers.length; i++) {
            System.out.println(i + ": " + browsers[i]);
        }

        // Print using enhanced for loop
        System.out.println("\nBrowsers list:");
        for (String browser : browsers) {
            System.out.println(browser);
        }

        // Print total count
        System.out.println("\nTotal browsers: " + browsers.length);
    }
}
```

**Explanation:**
- Array initialization: `String[] browsers = {"chrome", "firefox", ...}` creates and populates in one line
- Traditional for loop uses index `i` to access elements: `browsers[i]`
- Enhanced for loop iterates without needing indices: cleaner when you don't need position
- `browsers.length` is a property (no parentheses), unlike ArrayList's `size()` method

**Key Takeaways:**
- Use traditional for loop when you need index positions
- Use enhanced for loop for simpler iteration when index isn't needed
- Remember: `array.length` (property) vs `list.size()` (method)

---

### Exercise 2 Solution
```java
import java.util.ArrayList;

public class Exercise2 {
    public static void main(String[] args) {
        // Create ArrayList of test case names
        ArrayList<String> testCases = new ArrayList<>();

        // Add 5 test cases
        testCases.add("Verify Login");
        testCases.add("Verify Logout");
        testCases.add("Verify Search");
        testCases.add("Verify Cart");
        testCases.add("Verify Checkout");

        // Print all test cases with numbers
        System.out.println("Initial Test Cases:");
        for (int i = 0; i < testCases.size(); i++) {
            System.out.println((i + 1) + ". " + testCases.get(i));
        }

        // Remove test case at index 2
        testCases.remove(2);

        System.out.println("\nAfter removing index 2:");
        for (int i = 0; i < testCases.size(); i++) {
            System.out.println((i + 1) + ". " + testCases.get(i));
        }

        // Add new test case at index 1
        testCases.add(1, "Verify Registration");

        System.out.println("\nAfter adding at index 1:");
        for (int i = 0; i < testCases.size(); i++) {
            System.out.println((i + 1) + ". " + testCases.get(i));
        }

        // Check if specific test exists
        boolean hasCartTest = testCases.contains("Verify Cart");
        System.out.println("\nContains 'Verify Cart': " + hasCartTest);
    }
}
```

**Explanation:**
- `ArrayList<String>` requires import: `import java.util.ArrayList;`
- `add(element)` appends to end
- `add(index, element)` inserts at specific position, shifting others right
- `remove(index)` deletes element, shifting others left
- `get(index)` retrieves element (not bracket notation like arrays)
- `contains(element)` checks if element exists

**Key Takeaways:**
- ArrayList automatically resizes - no need to specify capacity
- Always use methods (`add`, `get`, `remove`), not bracket notation
- Print loop counter as `i + 1` for human-readable numbering (1, 2, 3...)

---

### Exercise 3 Solution
```java
public class Exercise3 {
    public static void main(String[] args) {
        String email = "  JOHN.DOE@TESTMAIL.COM  ";

        // Remove leading/trailing spaces
        String trimmed = email.trim();

        // Convert to lowercase
        String lowercase = trimmed.toLowerCase();

        // Extract username (before @)
        int atIndex = lowercase.indexOf("@");
        String username = lowercase.substring(0, atIndex);

        // Extract domain (after @)
        String domain = lowercase.substring(atIndex + 1);

        // Check if email contains @ and .
        boolean isValid = lowercase.contains("@") && lowercase.contains(".");

        // Replace domain
        String newEmail = lowercase.replace(domain, "example.com");

        // Print results
        System.out.println("Original: \"" + email + "\"");
        System.out.println("Trimmed: \"" + trimmed + "\"");
        System.out.println("Lowercase: \"" + lowercase + "\"");
        System.out.println("Username: " + username);
        System.out.println("Domain: " + domain);
        System.out.println("Valid format: " + isValid);
        System.out.println("New email: " + newEmail);
    }
}
```

**Explanation:**
- `trim()` removes spaces from both ends
- `toLowerCase()` converts entire string to lowercase
- `indexOf("@")` finds position of @ character
- `substring(0, atIndex)` extracts from start to @ (exclusive)
- `substring(atIndex + 1)` extracts from after @ to end
- `contains()` checks if substring exists
- `replace(old, new)` replaces all occurrences

**Key Takeaways:**
- String methods don't modify original - they return new strings
- `substring(start, end)` is exclusive of end index
- `substring(start)` goes from start to end of string
- Chain validations with `&&` for complete validation

---

### Exercise 4 Solution
```java
public class Exercise4 {
    public static void main(String[] args) {
        // Test data
        String[] usernames = {"john@test.com", "", "jane@test.com", "bob@test.com"};
        String[] passwords = {"pass123", "12345", "secure456", "weak"};
        boolean[] expectedResults = {true, false, true, false};

        int validLogins = 0;
        int invalidLogins = 0;
        int passedTests = 0;

        System.out.println("===== Login Test Data Processor =====\n");

        // Process each login attempt
        for (int i = 0; i < usernames.length; i++) {
            String username = usernames[i];
            String password = passwords[i];

            System.out.println("Test " + (i + 1) + ": " + username + " / " + password);

            // Validate username
            boolean usernameValid = !username.isEmpty();
            System.out.println("  Username valid: " + usernameValid);

            // Validate password
            boolean passwordValid = password.length() >= 6;
            System.out.println("  Password valid: " + passwordValid);

            // Determine if login should succeed
            boolean shouldSucceed = usernameValid && passwordValid;
            System.out.println("  Should succeed: " + shouldSucceed);

            // Compare with expected
            boolean expected = expectedResults[i];
            System.out.println("  Expected: " + expected);

            // Check test result
            if (shouldSucceed == expected) {
                System.out.println("  ✓ Test Passed");
                passedTests++;
            } else {
                System.out.println("  ✗ Test Failed");
            }

            // Update counters
            if (shouldSucceed) {
                validLogins++;
            } else {
                invalidLogins++;
            }

            System.out.println();
        }

        // Print statistics
        System.out.println("===== Statistics =====");
        System.out.println("Valid logins: " + validLogins);
        System.out.println("Invalid logins: " + invalidLogins);
        System.out.println("All tests passed: " + passedTests + "/" + usernames.length);
    }
}
```

**Explanation:**
- Parallel arrays: same index represents related data across arrays
- `!username.isEmpty()` checks username is not empty
- `password.length() >= 6` validates minimum password length
- `shouldSucceed == expected` compares actual vs expected result
- Counters track statistics across iterations

**Key Takeaways:**
- Parallel arrays must have same length and corresponding indices
- Use boolean logic to combine validation conditions
- Always validate test data before processing
- Track both test results and business logic outcomes

---

### Exercise 5 Solution
```java
import java.util.ArrayList;

public class Exercise5 {
    public static void main(String[] args) {
        String[] allTests = {
            "Login with valid credentials",
            "Login with invalid password",
            "Logout after successful login",
            "Search for product by name",
            "Search for product by category",
            "Add product to cart",
            "Remove product from cart",
            "Checkout with credit card",
            "Checkout with PayPal"
        };

        // Find all test cases containing "login" (case-insensitive)
        System.out.println("===== Tests containing 'login' =====");
        ArrayList<String> loginTests = new ArrayList<>();
        for (String test : allTests) {
            if (test.toLowerCase().contains("login")) {
                loginTests.add(test);
            }
        }
        for (int i = 0; i < loginTests.size(); i++) {
            System.out.println((i + 1) + ". " + loginTests.get(i));
        }
        System.out.println("Total: " + loginTests.size() + " tests");

        // Find all test cases starting with "Search"
        System.out.println("\n===== Tests starting with 'Search' =====");
        ArrayList<String> searchTests = new ArrayList<>();
        for (String test : allTests) {
            if (test.startsWith("Search")) {
                searchTests.add(test);
            }
        }
        for (int i = 0; i < searchTests.size(); i++) {
            System.out.println((i + 1) + ". " + searchTests.get(i));
        }
        System.out.println("Total: " + searchTests.size() + " tests");

        // Count test cases containing "product"
        System.out.println("\n===== Tests containing 'product' =====");
        int productTestCount = 0;
        for (String test : allTests) {
            if (test.toLowerCase().contains("product")) {
                System.out.println((productTestCount + 1) + ". " + test);
                productTestCount++;
            }
        }
        System.out.println("Total: " + productTestCount + " tests");

        // Find all checkout tests
        System.out.println("\n===== Checkout Tests =====");
        ArrayList<String> checkoutTests = new ArrayList<>();
        for (String test : allTests) {
            if (test.toLowerCase().contains("checkout")) {
                checkoutTests.add(test);
            }
        }
        for (int i = 0; i < checkoutTests.size(); i++) {
            System.out.println((i + 1) + ". " + checkoutTests.get(i));
        }
        System.out.println("Total: " + checkoutTests.size() + " tests");
    }
}
```

**Explanation:**
- `toLowerCase().contains("keyword")` for case-insensitive search
- `startsWith("prefix")` checks beginning of string
- Use ArrayList to dynamically collect matching results
- Enhanced for loop is cleaner when just checking conditions

**Key Takeaways:**
- Always convert to same case (lowercase) for case-insensitive operations
- ArrayList is perfect for storing filtered results of unknown size
- Combine string methods for powerful searches
- This pattern is common in test suite filtering

---

### Exercise 6 Solution
```java
public class Exercise6 {
    public static void main(String[] args) {
        String[] testUrls = {
            "https://www.example.com/login",
            "https://www.shop.com/products/laptop?id=123&color=silver",
            "http://test.local/api/users?page=2&limit=10",
            "www.invalid.com/page"
        };

        System.out.println("===== URL PARSER AND VALIDATOR =====\n");

        for (int i = 0; i < testUrls.length; i++) {
            String url = testUrls[i];
            System.out.println("URL " + (i + 1) + ": " + url);

            // Check if URL has protocol
            boolean hasProtocol = url.startsWith("http://") || url.startsWith("https://");

            if (!hasProtocol) {
                System.out.println("  ✗ Invalid: Missing protocol\n");
                continue;
            }

            // Extract protocol
            String protocol = url.substring(0, url.indexOf("://"));

            // Extract domain
            int domainStart = url.indexOf("://") + 3;
            int domainEnd = url.indexOf("/", domainStart);
            if (domainEnd == -1) {
                domainEnd = url.length();  // No path after domain
            }
            String domain = url.substring(domainStart, domainEnd);

            // Extract path
            String path = "none";
            if (domainEnd < url.length()) {
                int pathEnd = url.contains("?") ? url.indexOf("?") : url.length();
                path = url.substring(domainEnd, pathEnd);
            }

            // Extract query string
            String queryString = "";
            if (url.contains("?")) {
                queryString = url.substring(url.indexOf("?") + 1);
            }

            // Display parsed components
            System.out.println("  ✓ Valid URL");
            System.out.println("  Protocol: " + protocol);
            System.out.println("  Domain: " + domain);
            System.out.println("  Path: " + path);

            if (!queryString.isEmpty()) {
                System.out.println("  Query: " + queryString);

                // Parse query parameters
                String[] params = queryString.split("&");
                for (String param : params) {
                    String[] keyValue = param.split("=");
                    if (keyValue.length == 2) {
                        System.out.println("    Parameter: " + keyValue[0] + " = " + keyValue[1]);
                    }
                }
            } else {
                System.out.println("  Query: none");
            }

            System.out.println();
        }
    }
}
```

**Explanation:**
- `indexOf("://")` finds where protocol ends
- Handle case where domain is at end of URL (no path) with `indexOf("/", startFrom)`
- `indexOf()` returns -1 if not found - use this for conditional logic
- `split("&")` breaks query into individual parameters
- `split("=")` breaks each parameter into key and value

**Key Takeaways:**
- Complex string parsing requires multiple `indexOf()` and `substring()` calls
- Always check for -1 return value from `indexOf()`
- `split()` is powerful for breaking strings into arrays
- Real automation often requires parsing URLs for dynamic test data

---

## 📊 Self-Assessment

After completing exercises, rate yourself:

| Exercise | Completed? | Difficulty (1-10) | Time Taken | Notes |
|----------|------------|-------------------|------------|-------|
| 1 - Array Basics | ☐ | __/10 | ___ min | |
| 2 - ArrayList Ops | ☐ | __/10 | ___ min | |
| 3 - String Methods | ☐ | __/10 | ___ min | |
| 4 - Test Data Processor | ☐ | __/10 | ___ min | |
| 5 - Array Search/Filter | ☐ | __/10 | ___ min | |
| 6 - URL Parser | ☐ | __/10 | ___ min | |

**Total Practice Time:** ___ hours

**Areas I struggled with:**
- [ ] Array syntax and iteration
- [ ] ArrayList vs array differences
- [ ] String method chaining
- [ ] Index management
- [ ] Splitting and parsing strings
- [ ] Logic for validation
- [ ] Other: _______________

**Areas I excelled at:**
- [ ] Using for loops
- [ ] String manipulation
- [ ] ArrayList operations
- [ ] Problem-solving approach
- [ ] Understanding test scenarios
- [ ] Other: _______________

---

## 🎯 Answers to Self-Check Questions from Concept Guide

1. **What's the difference between `array.length` and `arrayList.size()`?**
   - `array.length` is a **property** (no parentheses) that returns array size
   - `arrayList.size()` is a **method** (with parentheses) that returns ArrayList size
   - Arrays have fixed size, ArrayList size changes as you add/remove

2. **What will this code output?**
   ```java
   String s1 = "test";
   String s2 = "test";
   String s3 = new String("test");
   System.out.println(s1 == s2);      // true (string pool)
   System.out.println(s1 == s3);      // false (different objects)
   System.out.println(s1.equals(s3)); // true (same content)
   ```

3. **Why is this code inefficient?**
   ```java
   String result = "";
   for (int i = 0; i < 100; i++) {
       result = result + i;  // Creates 100 intermediate String objects!
   }
   ```
   Because Strings are immutable, each concatenation creates a new String object. With 100 iterations, this creates 100 intermediate objects. Use StringBuilder instead for efficient building.

4. **What's wrong with this code?**
   ```java
   String[] browsers = {"chrome", "firefox"};
   browsers.add("edge");  // ❌ Arrays don't have add() method!
   ```
   Arrays are fixed size and don't have an `add()` method. Use ArrayList for dynamic arrays, or create a larger array and copy elements.

5. **How would you check if a string contains "@" symbol?**
   ```java
   String email = "test@example.com";
   if (email.contains("@")) {
       System.out.println("Valid email format");
   }
   // Or check index
   if (email.indexOf("@") != -1) {
       System.out.println("Contains @");
   }
   ```

---

## 🚀 Next Step

Move to **Day-3-Debug-Practice.md** to sharpen your debugging skills with arrays and strings!

**Practice complete:** ✅
**Confidence level (1-10):** ____
**Ready for debugging:** Yes / Need more practice

---
