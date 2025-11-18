# DAY 7 MINI PROJECT: STUDENT MANAGEMENT SYSTEM

## 🎯 Project Overview

**What You're Building:**
A complete Student Management System with CRUD operations (Create, Read, Update, Delete). The system will manage student records using ArrayList, validate input data, and handle exceptions gracefully. This is a real-world application that demonstrates all Week 1 concepts working together.

**Why This Project:**
This project combines OOP (classes, objects), collections (ArrayList, HashMap), exception handling, and user input validation. It's similar to managing test data in automation frameworks - you'll add test cases, update results, search for specific tests, and handle invalid data.

**Time Required:** 45-60 minutes

**Skills Practiced:**
- Custom exception classes
- ArrayList for dynamic student storage
- HashMap for quick student lookup
- Input validation and error handling
- CRUD operations (Create, Read, Update, Delete)
- User interaction with Scanner
- Try-catch-finally blocks
- Object-oriented design

---

## 📋 Requirements

### Must-Have Features:
1. **Add Student** - Create new student records with ID, name, age, and grade
2. **Display All Students** - Show all students in a formatted table
3. **Search Student** - Find student by ID
4. **Update Student** - Modify student information (grade, age)
5. **Delete Student** - Remove student from system
6. **Input Validation** - Validate all user inputs with custom exceptions
7. **Exception Handling** - Handle all errors gracefully (no crashes)
8. **Menu System** - Interactive menu for user operations

### Nice-to-Have (Bonus):
1. Calculate and display average grade
2. Find students by grade range
3. Sort students by name or grade
4. Save/load data to file
5. Search students by name (partial match)

---

## 🏗️ Project Structure

**Classes Needed:**
1. `Student.java` - Student data model with properties and methods
2. `StudentManagementSystem.java` - Main system with CRUD operations
3. `InvalidStudentDataException.java` - Custom exception for validation errors

**Methods to Implement:**
- `addStudent()` - Add new student to the system
- `displayAllStudents()` - Show all students
- `searchStudent()` - Find student by ID
- `updateStudent()` - Update student information
- `deleteStudent()` - Remove student
- `validateStudentData()` - Validate input data

---

## 📝 Step-by-Step Guide

### Step 1: Create Custom Exception Class

**What to do:**
Create a custom exception to handle invalid student data (invalid age, empty name, duplicate ID, etc.)

**Code Template:**
```java
// InvalidStudentDataException.java
public class InvalidStudentDataException extends Exception {
    public InvalidStudentDataException(String message) {
        super(message);
    }
}
```

**Testing:**
Try throwing and catching this exception to verify it works.

---

### Step 2: Create Student Class

**What to do:**
Create a Student class with properties (id, name, age, grade) and methods (getters, setters, toString)

**Code Template:**
```java
// Student.java
public class Student {
    // TODO: Add private fields
    private String studentId;
    private String name;
    private int age;
    private double grade;

    // TODO: Constructor
    public Student(String studentId, String name, int age, double grade) {
        // Initialize all fields
    }

    // TODO: Getters
    public String getStudentId() {
        // Return studentId
    }

    // Add getters for name, age, grade

    // TODO: Setters (for updateable fields)
    public void setAge(int age) {
        // Update age
    }

    public void setGrade(double grade) {
        // Update grade
    }

    // TODO: toString method for display
    @Override
    public String toString() {
        // Return formatted student information
    }
}
```

**Testing:**
Create a Student object and print it to verify toString() works.

---

### Step 3: Create StudentManagementSystem Class Structure

**What to do:**
Set up the main class with ArrayList to store students and HashMap for quick lookup

**Code Template:**
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Scanner;

public class StudentManagementSystem {

    // TODO: Create collections to store students
    private static ArrayList<Student> studentList = new ArrayList<>();
    private static HashMap<String, Student> studentMap = new HashMap<>();
    private static Scanner scanner = new Scanner(System.in);

    // TODO: Add method stubs (will implement in next steps)
    public static void main(String[] args) {
        // Main menu loop
    }
}
```

---

### Step 4: Implement Add Student Feature

**What to do:**
Create method to add new students with full validation

**Code Template:**
```java
public static void addStudent() throws InvalidStudentDataException {
    System.out.println("\n--- Add New Student ---");

    // TODO: Get student ID
    System.out.print("Enter Student ID: ");
    String id = scanner.nextLine().trim();

    // TODO: Validate ID is not empty
    if (id.isEmpty()) {
        throw new InvalidStudentDataException("Student ID cannot be empty");
    }

    // TODO: Check for duplicate ID
    if (studentMap.containsKey(id)) {
        throw new InvalidStudentDataException("Student ID already exists: " + id);
    }

    // TODO: Get and validate name
    System.out.print("Enter Student Name: ");
    String name = scanner.nextLine().trim();
    if (name.isEmpty()) {
        throw new InvalidStudentDataException("Student name cannot be empty");
    }

    // TODO: Get and validate age
    System.out.print("Enter Age: ");
    int age = Integer.parseInt(scanner.nextLine());
    if (age < 5 || age > 100) {
        throw new InvalidStudentDataException("Age must be between 5 and 100");
    }

    // TODO: Get and validate grade
    System.out.print("Enter Grade (0-100): ");
    double grade = Double.parseDouble(scanner.nextLine());
    if (grade < 0 || grade > 100) {
        throw new InvalidStudentDataException("Grade must be between 0 and 100");
    }

    // TODO: Create student and add to collections
    Student student = new Student(id, name, age, grade);
    studentList.add(student);
    studentMap.put(id, student);

    System.out.println("✓ Student added successfully!");
}
```

**Testing:**
Add several students and verify they're stored correctly.

---

### Step 5: Implement Display All Students

**What to do:**
Show all students in a formatted table

**Code Template:**
```java
public static void displayAllStudents() {
    System.out.println("\n--- All Students ---");

    // TODO: Check if list is empty
    if (studentList.isEmpty()) {
        System.out.println("No students in the system.");
        return;
    }

    // TODO: Print table header
    System.out.println("================================================================================");
    System.out.printf("%-15s %-20s %-10s %-10s%n", "Student ID", "Name", "Age", "Grade");
    System.out.println("================================================================================");

    // TODO: Print each student
    for (Student student : studentList) {
        System.out.printf("%-15s %-20s %-10d %-10.2f%n",
                student.getStudentId(),
                student.getName(),
                student.getAge(),
                student.getGrade());
    }

    System.out.println("================================================================================");
    System.out.println("Total Students: " + studentList.size());
}
```

**Testing:**
Display the list after adding students.

---

### Step 6: Implement Search Student

**What to do:**
Find and display a specific student by ID using HashMap for fast lookup

**Code Template:**
```java
public static void searchStudent() {
    System.out.println("\n--- Search Student ---");

    System.out.print("Enter Student ID: ");
    String id = scanner.nextLine().trim();

    // TODO: Search using HashMap (O(1) lookup)
    Student student = studentMap.get(id);

    if (student == null) {
        System.out.println("✗ Student not found with ID: " + id);
    } else {
        System.out.println("\n✓ Student Found:");
        System.out.println("================================================================================");
        System.out.printf("%-15s %-20s %-10s %-10s%n", "Student ID", "Name", "Age", "Grade");
        System.out.println("================================================================================");
        System.out.printf("%-15s %-20s %-10d %-10.2f%n",
                student.getStudentId(),
                student.getName(),
                student.getAge(),
                student.getGrade());
        System.out.println("================================================================================");
    }
}
```

**Testing:**
Search for existing and non-existing student IDs.

---

### Step 7: Implement Update Student

**What to do:**
Allow updating student age and grade

**Code Template:**
```java
public static void updateStudent() throws InvalidStudentDataException {
    System.out.println("\n--- Update Student ---");

    System.out.print("Enter Student ID: ");
    String id = scanner.nextLine().trim();

    // TODO: Find student
    Student student = studentMap.get(id);
    if (student == null) {
        throw new InvalidStudentDataException("Student not found with ID: " + id);
    }

    // TODO: Display current info
    System.out.println("Current Information: " + student);

    // TODO: Update age
    System.out.print("Enter new Age (current: " + student.getAge() + "): ");
    int age = Integer.parseInt(scanner.nextLine());
    if (age < 5 || age > 100) {
        throw new InvalidStudentDataException("Age must be between 5 and 100");
    }

    // TODO: Update grade
    System.out.print("Enter new Grade (current: " + student.getGrade() + "): ");
    double grade = Double.parseDouble(scanner.nextLine());
    if (grade < 0 || grade > 100) {
        throw new InvalidStudentDataException("Grade must be between 0 and 100");
    }

    // TODO: Apply updates
    student.setAge(age);
    student.setGrade(grade);

    System.out.println("✓ Student updated successfully!");
    System.out.println("Updated Information: " + student);
}
```

**Testing:**
Update a student's information and verify changes.

---

### Step 8: Implement Delete Student

**What to do:**
Remove student from both ArrayList and HashMap

**Code Template:**
```java
public static void deleteStudent() throws InvalidStudentDataException {
    System.out.println("\n--- Delete Student ---");

    System.out.print("Enter Student ID to delete: ");
    String id = scanner.nextLine().trim();

    // TODO: Find student
    Student student = studentMap.get(id);
    if (student == null) {
        throw new InvalidStudentDataException("Student not found with ID: " + id);
    }

    // TODO: Confirm deletion
    System.out.println("Student to delete: " + student);
    System.out.print("Are you sure? (yes/no): ");
    String confirm = scanner.nextLine().trim().toLowerCase();

    if (confirm.equals("yes")) {
        // TODO: Remove from both collections
        studentList.remove(student);
        studentMap.remove(id);
        System.out.println("✓ Student deleted successfully!");
    } else {
        System.out.println("Deletion cancelled.");
    }
}
```

**Testing:**
Delete a student and verify it's removed from the system.

---

### Step 9: Create Main Menu

**What to do:**
Build interactive menu with exception handling

**Code Template:**
```java
public static void main(String[] args) {
    System.out.println("===================================");
    System.out.println("  STUDENT MANAGEMENT SYSTEM");
    System.out.println("===================================");

    while (true) {
        try {
            System.out.println("\n--- Main Menu ---");
            System.out.println("1. Add Student");
            System.out.println("2. Display All Students");
            System.out.println("3. Search Student");
            System.out.println("4. Update Student");
            System.out.println("5. Delete Student");
            System.out.println("6. Exit");
            System.out.print("Enter your choice: ");

            int choice = Integer.parseInt(scanner.nextLine());

            switch (choice) {
                case 1:
                    addStudent();
                    break;
                case 2:
                    displayAllStudents();
                    break;
                case 3:
                    searchStudent();
                    break;
                case 4:
                    updateStudent();
                    break;
                case 5:
                    deleteStudent();
                    break;
                case 6:
                    System.out.println("Thank you for using Student Management System!");
                    scanner.close();
                    System.exit(0);
                default:
                    System.out.println("Invalid choice. Please enter 1-6.");
            }

        } catch (InvalidStudentDataException e) {
            System.out.println("✗ Validation Error: " + e.getMessage());
        } catch (NumberFormatException e) {
            System.out.println("✗ Invalid input. Please enter a valid number.");
        } catch (Exception e) {
            System.out.println("✗ Unexpected error: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

**Testing:**
Run the full menu and test all operations.

---

## ✅ Testing Checklist

Test your project against these scenarios:

- [ ] Add a student with valid data
- [ ] Add a student with duplicate ID (should fail)
- [ ] Add a student with empty name (should fail)
- [ ] Add a student with invalid age (should fail)
- [ ] Add a student with invalid grade (should fail)
- [ ] Display all students (should show formatted table)
- [ ] Display when no students exist
- [ ] Search for existing student
- [ ] Search for non-existing student
- [ ] Update student's age and grade
- [ ] Try to update non-existing student
- [ ] Delete a student (confirm yes)
- [ ] Delete a student (cancel with no)
- [ ] Try to delete non-existing student
- [ ] Enter invalid menu choice
- [ ] Enter non-numeric input for choice

**Expected Behavior:**
- All validation errors should show clear messages
- No crashes or unhandled exceptions
- Data should remain consistent after operations
- Both ArrayList and HashMap should stay synchronized

---

## 🎓 Complete Solution (Only Look After Attempting!)

### InvalidStudentDataException.java
```java
public class InvalidStudentDataException extends Exception {
    public InvalidStudentDataException(String message) {
        super(message);
    }
}
```

### Student.java
```java
public class Student {
    private String studentId;
    private String name;
    private int age;
    private double grade;

    public Student(String studentId, String name, int age, double grade) {
        this.studentId = studentId;
        this.name = name;
        this.age = age;
        this.grade = grade;
    }

    // Getters
    public String getStudentId() {
        return studentId;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public double getGrade() {
        return grade;
    }

    // Setters (for updateable fields only)
    public void setAge(int age) {
        this.age = age;
    }

    public void setGrade(double grade) {
        this.grade = grade;
    }

    @Override
    public String toString() {
        return String.format("Student[ID=%s, Name=%s, Age=%d, Grade=%.2f]",
                studentId, name, age, grade);
    }
}
```

### StudentManagementSystem.java
```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Scanner;

public class StudentManagementSystem {

    private static ArrayList<Student> studentList = new ArrayList<>();
    private static HashMap<String, Student> studentMap = new HashMap<>();
    private static Scanner scanner = new Scanner(System.in);

    public static void addStudent() throws InvalidStudentDataException {
        System.out.println("\n--- Add New Student ---");

        System.out.print("Enter Student ID: ");
        String id = scanner.nextLine().trim();

        if (id.isEmpty()) {
            throw new InvalidStudentDataException("Student ID cannot be empty");
        }

        if (studentMap.containsKey(id)) {
            throw new InvalidStudentDataException("Student ID already exists: " + id);
        }

        System.out.print("Enter Student Name: ");
        String name = scanner.nextLine().trim();
        if (name.isEmpty()) {
            throw new InvalidStudentDataException("Student name cannot be empty");
        }

        System.out.print("Enter Age: ");
        int age = Integer.parseInt(scanner.nextLine());
        if (age < 5 || age > 100) {
            throw new InvalidStudentDataException("Age must be between 5 and 100");
        }

        System.out.print("Enter Grade (0-100): ");
        double grade = Double.parseDouble(scanner.nextLine());
        if (grade < 0 || grade > 100) {
            throw new InvalidStudentDataException("Grade must be between 0 and 100");
        }

        Student student = new Student(id, name, age, grade);
        studentList.add(student);
        studentMap.put(id, student);

        System.out.println("✓ Student added successfully!");
    }

    public static void displayAllStudents() {
        System.out.println("\n--- All Students ---");

        if (studentList.isEmpty()) {
            System.out.println("No students in the system.");
            return;
        }

        System.out.println("================================================================================");
        System.out.printf("%-15s %-20s %-10s %-10s%n", "Student ID", "Name", "Age", "Grade");
        System.out.println("================================================================================");

        for (Student student : studentList) {
            System.out.printf("%-15s %-20s %-10d %-10.2f%n",
                    student.getStudentId(),
                    student.getName(),
                    student.getAge(),
                    student.getGrade());
        }

        System.out.println("================================================================================");
        System.out.println("Total Students: " + studentList.size());
    }

    public static void searchStudent() {
        System.out.println("\n--- Search Student ---");

        System.out.print("Enter Student ID: ");
        String id = scanner.nextLine().trim();

        Student student = studentMap.get(id);

        if (student == null) {
            System.out.println("✗ Student not found with ID: " + id);
        } else {
            System.out.println("\n✓ Student Found:");
            System.out.println("================================================================================");
            System.out.printf("%-15s %-20s %-10s %-10s%n", "Student ID", "Name", "Age", "Grade");
            System.out.println("================================================================================");
            System.out.printf("%-15s %-20s %-10d %-10.2f%n",
                    student.getStudentId(),
                    student.getName(),
                    student.getAge(),
                    student.getGrade());
            System.out.println("================================================================================");
        }
    }

    public static void updateStudent() throws InvalidStudentDataException {
        System.out.println("\n--- Update Student ---");

        System.out.print("Enter Student ID: ");
        String id = scanner.nextLine().trim();

        Student student = studentMap.get(id);
        if (student == null) {
            throw new InvalidStudentDataException("Student not found with ID: " + id);
        }

        System.out.println("Current Information: " + student);

        System.out.print("Enter new Age (current: " + student.getAge() + "): ");
        int age = Integer.parseInt(scanner.nextLine());
        if (age < 5 || age > 100) {
            throw new InvalidStudentDataException("Age must be between 5 and 100");
        }

        System.out.print("Enter new Grade (current: " + student.getGrade() + "): ");
        double grade = Double.parseDouble(scanner.nextLine());
        if (grade < 0 || grade > 100) {
            throw new InvalidStudentDataException("Grade must be between 0 and 100");
        }

        student.setAge(age);
        student.setGrade(grade);

        System.out.println("✓ Student updated successfully!");
        System.out.println("Updated Information: " + student);
    }

    public static void deleteStudent() throws InvalidStudentDataException {
        System.out.println("\n--- Delete Student ---");

        System.out.print("Enter Student ID to delete: ");
        String id = scanner.nextLine().trim();

        Student student = studentMap.get(id);
        if (student == null) {
            throw new InvalidStudentDataException("Student not found with ID: " + id);
        }

        System.out.println("Student to delete: " + student);
        System.out.print("Are you sure? (yes/no): ");
        String confirm = scanner.nextLine().trim().toLowerCase();

        if (confirm.equals("yes")) {
            studentList.remove(student);
            studentMap.remove(id);
            System.out.println("✓ Student deleted successfully!");
        } else {
            System.out.println("Deletion cancelled.");
        }
    }

    public static void main(String[] args) {
        System.out.println("===================================");
        System.out.println("  STUDENT MANAGEMENT SYSTEM");
        System.out.println("===================================");

        while (true) {
            try {
                System.out.println("\n--- Main Menu ---");
                System.out.println("1. Add Student");
                System.out.println("2. Display All Students");
                System.out.println("3. Search Student");
                System.out.println("4. Update Student");
                System.out.println("5. Delete Student");
                System.out.println("6. Exit");
                System.out.print("Enter your choice: ");

                int choice = Integer.parseInt(scanner.nextLine());

                switch (choice) {
                    case 1:
                        addStudent();
                        break;
                    case 2:
                        displayAllStudents();
                        break;
                    case 3:
                        searchStudent();
                        break;
                    case 4:
                        updateStudent();
                        break;
                    case 5:
                        deleteStudent();
                        break;
                    case 6:
                        System.out.println("\nThank you for using Student Management System!");
                        System.out.println("Goodbye!");
                        scanner.close();
                        System.exit(0);
                    default:
                        System.out.println("✗ Invalid choice. Please enter 1-6.");
                }

            } catch (InvalidStudentDataException e) {
                System.out.println("✗ Validation Error: " + e.getMessage());
            } catch (NumberFormatException e) {
                System.out.println("✗ Invalid input. Please enter a valid number.");
            } catch (Exception e) {
                System.out.println("✗ Unexpected error: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these:

1. **Calculate Average Grade**
   ```java
   public static void calculateAverageGrade() {
       if (studentList.isEmpty()) {
           System.out.println("No students to calculate average.");
           return;
       }
       double total = 0;
       for (Student s : studentList) {
           total += s.getGrade();
       }
       double average = total / studentList.size();
       System.out.printf("Average Grade: %.2f%n", average);
   }
   ```

2. **Find Top Performers**
   ```java
   public static void findTopPerformers() {
       studentList.stream()
           .filter(s -> s.getGrade() >= 90)
           .forEach(s -> System.out.println(s));
   }
   ```

3. **Sort Students by Grade**
   ```java
   Collections.sort(studentList, (s1, s2) ->
       Double.compare(s2.getGrade(), s1.getGrade()));
   ```

4. **Export to CSV**
   Write student data to a CSV file for external use

5. **Import from CSV**
   Load student data from a CSV file on startup

---

## 💼 How This Relates to Selenium

**Exception Handling:**
```java
// Similar pattern in Selenium test framework
try {
    WebElement element = driver.findElement(By.id("submit"));
    element.click();
} catch (NoSuchElementException e) {
    System.out.println("Element not found: " + e.getMessage());
    // Take screenshot, log error
} finally {
    driver.quit(); // Always close browser
}
```

**Collections for Test Data:**
```java
// Managing test users (like students)
ArrayList<User> testUsers = new ArrayList<>();
HashMap<String, String> credentials = new HashMap<>();

// Similar CRUD operations for test data
addTestUser(user);
searchTestUser(userId);
updateTestCredentials(userId, newPassword);
deleteTestUser(userId);
```

**Data Validation:**
```java
// Validating test data before execution
public void validateTestData(TestData data) throws InvalidTestDataException {
    if (data.getUsername().isEmpty()) {
        throw new InvalidTestDataException("Username required");
    }
    // Similar validation as Student Management System
}
```

This Student Management System is a miniature version of test data management frameworks used in real automation projects!

---

## 🎉 Project Complete!

**What You've Built:**
- Full CRUD application with exception handling
- Custom exception for validation
- ArrayList and HashMap integration
- Input validation and error handling
- Interactive menu system

**Skills Demonstrated:**
- OOP principles (classes, objects, encapsulation)
- Collections (ArrayList, HashMap)
- Exception handling (try-catch, custom exceptions)
- User input and validation
- Code organization and structure

**Add to Your Portfolio:**
This is a complete, working application you can showcase on GitHub!

---
