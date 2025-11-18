# DAY 7 CHEAT SHEET: ADVANCED CONCEPTS

## ⚡ Quick Syntax Reference

### Exception Handling - try-catch-finally
```java
try {
    // Code that might throw exception
    int result = 10 / 0;
} catch (ArithmeticException e) {
    // Handle specific exception
    System.out.println("Error: " + e.getMessage());
} catch (Exception e) {
    // Catch any other exception
    System.out.println("Unexpected: " + e.getMessage());
} finally {
    // Always executes (cleanup)
    System.out.println("Cleanup complete");
}
```

### throw - Throwing Exceptions
```java
public void validateAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

### throws - Declaring Exceptions
```java
public void readFile(String path) throws IOException {
    FileReader file = new FileReader(path);
    // Method caller must handle IOException
}
```

### Custom Exception
```java
public class InvalidDataException extends Exception {
    public InvalidDataException(String message) {
        super(message);
    }
}

// Usage
throw new InvalidDataException("Invalid data provided");
```

### ArrayList - Dynamic Lists
```java
import java.util.ArrayList;

ArrayList<String> list = new ArrayList<>();
list.add("Item1");              // Add element
list.add(0, "First");           // Add at index
list.get(0);                    // Get by index
list.set(0, "Updated");         // Update
list.remove(0);                 // Remove by index
list.remove("Item1");           // Remove by value
list.size();                    // Get count
list.contains("Item1");         // Check exists
list.clear();                   // Remove all
list.isEmpty();                 // Check if empty
```

### HashSet - Unique Elements
```java
import java.util.HashSet;

HashSet<String> set = new HashSet<>();
set.add("A");                   // Add element
set.add("A");                   // Ignored (duplicate)
set.contains("A");              // Check exists
set.remove("A");                // Remove element
set.size();                     // Get count
set.clear();                    // Remove all
set.isEmpty();                  // Check if empty
```

### HashMap - Key-Value Pairs
```java
import java.util.HashMap;

HashMap<String, Integer> map = new HashMap<>();
map.put("key1", 100);           // Add/Update
map.get("key1");                // Get value
map.remove("key1");             // Remove by key
map.containsKey("key1");        // Check key exists
map.containsValue(100);         // Check value exists
map.size();                     // Get count
map.keySet();                   // Get all keys
map.values();                   // Get all values
map.clear();                    // Remove all
```

### Iterating ArrayList
```java
// For-each loop
for (String item : list) {
    System.out.println(item);
}

// Traditional for loop
for (int i = 0; i < list.size(); i++) {
    System.out.println(list.get(i));
}

// Iterator
Iterator<String> it = list.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
}
```

### Iterating HashMap
```java
// Iterate keys
for (String key : map.keySet()) {
    System.out.println(key + " = " + map.get(key));
}

// Iterate entries
for (HashMap.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}

// Java 8 forEach
map.forEach((k, v) -> System.out.println(k + " = " + v));
```

### Generics - Type Safety
```java
// Generic ArrayList
ArrayList<String> strings = new ArrayList<>();
ArrayList<Integer> numbers = new ArrayList<>();

// Generic HashMap
HashMap<String, String> stringMap = new HashMap<>();
HashMap<String, Integer> intMap = new HashMap<>();

// Generic Method
public <T> void printArray(T[] array) {
    for (T element : array) {
        System.out.println(element);
    }
}
```

---

## 🔑 Key Concepts at a Glance

| Concept | Java Syntax | Python Equivalent |
|---------|-------------|-------------------|
| Try-Catch | `try { } catch (Exception e) { }` | `try: ... except Exception as e:` |
| Throw Exception | `throw new Exception("error")` | `raise Exception("error")` |
| Declare Exception | `throws Exception` | No equivalent (not needed) |
| Dynamic List | `ArrayList<String> list = new ArrayList<>()` | `list = []` |
| Unique Set | `HashSet<String> set = new HashSet<>()` | `set = set()` |
| Dictionary/Map | `HashMap<K, V> map = new HashMap<>()` | `dict = {}` |
| Generics | `ArrayList<String>` | Type hints: `List[str]` |
| Add to List | `list.add("item")` | `list.append("item")` |
| Get from List | `list.get(0)` | `list[0]` |
| Add to Map | `map.put("key", "value")` | `dict["key"] = "value"` |
| Get from Map | `map.get("key")` | `dict["key"]` or `dict.get("key")` |

---

## ❌ Common Mistakes

| Mistake | Wrong | Correct |
|---------|-------|---------|
| Null ArrayList | `ArrayList<String> list = null;` | `ArrayList<String> list = new ArrayList<>();` |
| No Generics | `ArrayList list = new ArrayList();` | `ArrayList<String> list = new ArrayList<>();` |
| Index Out of Bounds | `list.get(list.size())` | `list.get(list.size() - 1)` |
| Empty Catch | `catch (Exception e) { }` | `catch (Exception e) { e.printStackTrace(); }` |
| Modify While Iterating | `for (String s : list) { list.remove(s); }` | Use `Iterator.remove()` |
| Null Check Missing | `String s = map.get("key"); s.length();` | `if (s != null) s.length();` |
| Wrong HashMap Value | `map.get(nonExistentKey).toString()` | `String v = map.getOrDefault(key, "default")` |

---

## 💡 Key Takeaways

**Today You Learned:**
- ✅ Exception handling with try-catch-finally blocks
- ✅ Creating and throwing custom exceptions
- ✅ Using throw vs throws keywords
- ✅ ArrayList for dynamic, ordered collections
- ✅ HashSet for unique element storage
- ✅ HashMap for key-value pair management
- ✅ Generics for type-safe collections
- ✅ Multiple ways to iterate collections
- ✅ Building CRUD operations with collections
- ✅ Input validation and error handling

**Critical for Automation:**
- 🎯 Exception handling prevents test crashes and provides clear error messages
- 🎯 Collections manage test data, WebElements, and test results efficiently
- 🎯 HashMap perfect for configuration management in test frameworks
- 🎯 ArrayList ideal for storing multiple WebElements from Selenium
- 🎯 Custom exceptions categorize test failures clearly

---

## 🎤 Interview Phrases

**When asked about exception handling, say:**

*"Exception handling in Java uses try-catch-finally blocks. The try block contains code that might throw exceptions, catch blocks handle specific exception types, and the finally block executes cleanup code regardless of exceptions. I use throw to explicitly throw exceptions for validation, and throws to declare that a method may throw exceptions. Custom exceptions make error messages domain-specific. In test automation, proper exception handling is essential for handling NoSuchElementException, TimeoutException, and other Selenium exceptions gracefully."*

**When asked about Collections Framework, say:**

*"The Java Collections Framework provides interfaces like List, Set, and Map with implementations like ArrayList, HashSet, and HashMap. ArrayList maintains insertion order and allows duplicates, HashSet stores only unique elements, and HashMap stores key-value pairs. I use generics like ArrayList<String> for compile-time type safety. In test automation, ArrayList is perfect for managing test data and WebElement lists, HashMap for configuration properties, and HashSet for tracking unique test IDs."*

**When asked about difference between ArrayList and Array, say:**

*"Arrays have fixed size set at creation, while ArrayList is dynamically resizable. ArrayList provides methods like add(), remove(), contains() which arrays don't have. ArrayList only stores objects (not primitives directly), while arrays can store both. ArrayList uses generics for type safety. In automation, ArrayList is more flexible for managing variable numbers of WebElements or test data."*

---

## 📌 Remember This

**The ONE thing to remember from today:**

> Exception handling and collections are the foundation of robust test automation frameworks. Use try-catch-finally to handle errors gracefully, ArrayList for ordered data, HashMap for quick lookups, and always use generics for type safety. Never let your tests crash silently - handle exceptions with meaningful messages!

---

## 🔮 Tomorrow's Preview (Week 2, Day 1)

**Next Topic:** Selenium WebDriver Setup & Basics

**What to review tonight:**
- Object-oriented programming (you'll create Page Object classes)
- Exception handling (for handling WebDriver exceptions)
- Collections (for managing multiple WebElements)
- Classes and methods (building test classes)

**What to think about:**
- How would you represent a web page as a Java class?
- What information would you store about web elements?
- How would you organize your test code?

**Connection to Today:**
- WebDriver exceptions → try-catch blocks
- List of WebElements → ArrayList
- Element locators → HashMap
- Page Objects → Classes with methods

---

## 🎯 Daily Challenge Completed!

**Today's Achievement:**
- [x] Learned exception handling (try-catch-finally, throw, throws)
- [x] Created custom exceptions for validation
- [x] Mastered ArrayList operations
- [x] Used HashSet for uniqueness
- [x] Implemented HashMap for key-value storage
- [x] Applied generics for type safety
- [x] Built Student Management System (CRUD operations)
- [x] 2.5-3 hours of focused Java practice

**Cumulative Progress:**
- Days completed: 7/7 ✅
- Week 1 COMPLETE! 🎉
- Java concepts mastered: 50+
- Mini projects built: 7
- Lines of code written: ~1000+

**Momentum Statement:**
*You've completed Week 1 of your Java SDET journey! You started with zero Java knowledge and now you understand variables, control flow, arrays, strings, OOP (classes, inheritance, polymorphism, abstraction, interfaces), exception handling, and collections. You've built 7 working projects including a full Student Management System. This is exactly the foundation needed for Selenium automation. Week 2 starts tomorrow - you're about to apply all this Java knowledge to real web automation!*

---

## 📊 Week 1 Complete - Quick Reference Card

### Core Java Syntax
```java
// Class structure
public class ClassName {
    public static void main(String[] args) {
        // Code here
    }
}

// Variables
int number = 10;
String text = "Hello";
boolean flag = true;

// Conditionals
if (condition) { } else { }

// Loops
for (int i = 0; i < 10; i++) { }
while (condition) { }

// Arrays
int[] arr = new int[5];
String[] names = {"A", "B", "C"};
```

### OOP Essentials
```java
// Class with constructor
public class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}

// Inheritance
public class Student extends Person {
    private int grade;

    public Student(String name, int grade) {
        super(name);
        this.grade = grade;
    }
}

// Interface
public interface Testable {
    void runTest();
}
```

### Collections Essentials
```java
// ArrayList
ArrayList<String> list = new ArrayList<>();
list.add("item");

// HashMap
HashMap<String, String> map = new HashMap<>();
map.put("key", "value");

// HashSet
HashSet<String> set = new HashSet<>();
set.add("unique");
```

### Exception Handling
```java
try {
    // Risky code
} catch (SpecificException e) {
    // Handle it
} finally {
    // Cleanup
}

throw new Exception("error");
```

---

## 🎓 Week 1 Mastery Checklist

**Syntax & Basics:**
- [ ] Variables and data types
- [ ] Operators (arithmetic, logical, comparison)
- [ ] Conditionals (if-else, switch)
- [ ] Loops (for, while, do-while)
- [ ] Arrays and ArrayLists
- [ ] String manipulation

**Object-Oriented Programming:**
- [ ] Classes and objects
- [ ] Constructors
- [ ] Encapsulation (private fields, public methods)
- [ ] Inheritance (extends)
- [ ] Polymorphism (method overriding)
- [ ] Abstraction (abstract classes)
- [ ] Interfaces (implements)

**Advanced Concepts:**
- [ ] Exception handling (try-catch-finally)
- [ ] Custom exceptions
- [ ] ArrayList operations
- [ ] HashMap operations
- [ ] HashSet operations
- [ ] Generics

**Projects Built:**
- [ ] Calculator
- [ ] Number Guessing Game
- [ ] Anagram Checker
- [ ] BankAccount Class
- [ ] Vehicle Hierarchy
- [ ] Shape Calculator
- [ ] Student Management System

---

## 🚀 Ready for Week 2: Selenium WebDriver!

**You now have:**
- Solid Java fundamentals
- OOP mastery
- Exception handling skills
- Collections expertise

**Week 2 Preview:**
- Day 1: Selenium setup, first test
- Day 2: Locators and WebElements
- Day 3: Page Object Model
- Day 4: Waits and synchronization
- Day 5: TestNG framework
- Day 6: Data-driven testing
- Day 7: Complete test suite

**Your Java foundation is SOLID. Time to automate! 🔥**

---
