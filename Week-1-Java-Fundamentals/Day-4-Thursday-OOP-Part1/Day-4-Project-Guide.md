# DAY 4 MINI PROJECT: BANKACCOUNT CLASS

## 🎯 Project Overview

**What You're Building:**
A complete `BankAccount` class that simulates a real bank account with deposit, withdrawal, balance checking, and transaction history features. This project demonstrates all OOP concepts learned today: classes, objects, constructors, encapsulation, and methods.

**Why This Project:**
- Reinforces encapsulation (protecting balance from invalid operations)
- Practices constructor usage and validation
- Implements business logic in methods
- Manages state (balance, transaction history)
- Similar to how you'll manage WebDriver state in Page Objects

**Time Required:** 30-45 minutes

**Skills Practiced:**
- Class design and structure
- Private instance variables
- Parameterized constructors
- Getter and setter methods with validation
- Instance methods with business logic
- ArrayList for transaction history
- String formatting for output

---

## 📋 Requirements

### Must-Have Features:
1. **Account Properties:**
   - Account number (String)
   - Account holder name (String)
   - Balance (double)
   - Transaction history (ArrayList of Strings)

2. **Constructor:**
   - Accept account number, account holder name, and initial balance
   - Validate initial balance (must be >= 0)
   - Add initial deposit to transaction history

3. **Deposit Method:**
   - Accept amount to deposit
   - Validate amount (must be > 0)
   - Update balance
   - Record transaction in history

4. **Withdraw Method:**
   - Accept amount to withdraw
   - Validate amount (must be > 0 and <= current balance)
   - Update balance
   - Record transaction in history
   - Prevent overdraft

5. **Balance Inquiry:**
   - Return current balance
   - Display formatted balance

6. **Transaction History:**
   - Display all transactions
   - Show transaction type, amount, and resulting balance

7. **Account Information:**
   - Display account number, holder name, and current balance

### Nice-to-Have (Bonus):
1. Add interest calculation method (e.g., 3% annual interest)
2. Transfer money to another account
3. Set minimum balance requirement
4. Add transaction timestamps

---

## 🏗️ Project Structure

**Class Needed:**
1. `BankAccount.java` - Main bank account class with all features

**Methods to Implement:**
- `BankAccount(String accountNumber, String accountHolder, double initialBalance)` - Constructor
- `deposit(double amount)` - Add money to account
- `withdraw(double amount)` - Remove money from account
- `getBalance()` - Return current balance
- `displayBalance()` - Print formatted balance
- `displayAccountInfo()` - Print account details
- `displayTransactionHistory()` - Print all transactions

**Helper Methods:**
- `addTransaction(String transaction)` - Add to transaction history (private)

---

## 📝 Step-by-Step Guide

### Step 1: Set Up Class Structure and Instance Variables

**What to do:**
Create the BankAccount class with private instance variables for account number, holder name, balance, and transaction history.

**Code Template:**
```java
import java.util.ArrayList;

public class BankAccount {
    // TODO: Add private instance variables
    private String accountNumber;
    private String accountHolder;
    private double balance;
    private ArrayList<String> transactionHistory;

    // Constructor and methods will go here
}
```

**Testing:**
Just create the class structure. No testing yet.

**Hints:**
- Use `private` for encapsulation
- ArrayList stores transaction history as strings
- Need to import `java.util.ArrayList`

---

### Step 2: Implement Constructor with Validation

**What to do:**
Create a constructor that initializes the account with account number, holder name, and initial balance. Validate that initial balance is non-negative.

**Code Template:**
```java
public BankAccount(String accountNumber, String accountHolder, double initialBalance) {
    // TODO: Validate initial balance (must be >= 0)
    if (initialBalance < 0) {
        System.out.println("Error: Initial balance cannot be negative. Setting to 0.");
        initialBalance = 0;
    }

    // TODO: Initialize instance variables
    this.accountNumber = accountNumber;
    this.accountHolder = accountHolder;
    this.balance = initialBalance;

    // TODO: Initialize transaction history ArrayList
    this.transactionHistory = new ArrayList<>();

    // TODO: Add initial transaction to history
    if (initialBalance > 0) {
        transactionHistory.add("Initial deposit: $" + initialBalance + " | Balance: $" + balance);
    }
}
```

**Testing:**
```java
public class TestBankAccount {
    public static void main(String[] args) {
        BankAccount account1 = new BankAccount("ACC001", "John Doe", 1000.0);
        BankAccount account2 = new BankAccount("ACC002", "Jane Smith", -500.0);  // Test validation

        // Verify objects created successfully
        System.out.println("Accounts created successfully!");
    }
}
```

**Expected Output:**
```
Error: Initial balance cannot be negative. Setting to 0.
Accounts created successfully!
```

---

### Step 3: Implement Deposit Method

**What to do:**
Create a method to deposit money into the account. Validate amount, update balance, and record transaction.

**Code Template:**
```java
public void deposit(double amount) {
    // TODO: Validate deposit amount (must be > 0)
    if (amount <= 0) {
        System.out.println("Error: Deposit amount must be positive.");
        return;
    }

    // TODO: Update balance
    balance += amount;

    // TODO: Record transaction
    String transaction = "Deposit: $" + amount + " | Balance: $" + balance;
    transactionHistory.add(transaction);

    // TODO: Print confirmation
    System.out.println("Deposited: $" + amount);
    System.out.println("New balance: $" + balance);
}
```

**Testing:**
```java
public class TestBankAccount {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("ACC001", "John Doe", 1000.0);

        account.deposit(500.0);   // Valid deposit
        account.deposit(-100.0);  // Invalid - should show error
        account.deposit(0);       // Invalid - should show error
    }
}
```

**Expected Output:**
```
Deposited: $500.0
New balance: $1500.0
Error: Deposit amount must be positive.
Error: Deposit amount must be positive.
```

---

### Step 4: Implement Withdraw Method

**What to do:**
Create a method to withdraw money from the account. Validate amount (positive and sufficient balance), update balance, and record transaction.

**Code Template:**
```java
public void withdraw(double amount) {
    // TODO: Validate withdrawal amount (must be > 0)
    if (amount <= 0) {
        System.out.println("Error: Withdrawal amount must be positive.");
        return;
    }

    // TODO: Check for sufficient balance
    if (amount > balance) {
        System.out.println("Error: Insufficient funds. Current balance: $" + balance);
        return;
    }

    // TODO: Update balance
    balance -= amount;

    // TODO: Record transaction
    String transaction = "Withdrawal: $" + amount + " | Balance: $" + balance;
    transactionHistory.add(transaction);

    // TODO: Print confirmation
    System.out.println("Withdrawn: $" + amount);
    System.out.println("New balance: $" + balance);
}
```

**Testing:**
```java
public class TestBankAccount {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("ACC001", "John Doe", 1000.0);

        account.withdraw(300.0);   // Valid withdrawal
        account.withdraw(800.0);   // Invalid - insufficient funds
        account.withdraw(-50.0);   // Invalid - negative amount
    }
}
```

**Expected Output:**
```
Withdrawn: $300.0
New balance: $700.0
Error: Insufficient funds. Current balance: $700.0
Error: Withdrawal amount must be positive.
```

---

### Step 5: Implement Balance and Account Info Methods

**What to do:**
Create methods to check balance and display account information.

**Code Template:**
```java
// Getter for balance
public double getBalance() {
    return balance;
}

// Display formatted balance
public void displayBalance() {
    System.out.println("Current Balance: $" + balance);
}

// Display complete account information
public void displayAccountInfo() {
    System.out.println("========================================");
    System.out.println("         ACCOUNT INFORMATION           ");
    System.out.println("========================================");
    System.out.println("Account Number: " + accountNumber);
    System.out.println("Account Holder: " + accountHolder);
    System.out.println("Current Balance: $" + balance);
    System.out.println("========================================");
}

// Getters for encapsulated fields
public String getAccountNumber() {
    return accountNumber;
}

public String getAccountHolder() {
    return accountHolder;
}
```

**Testing:**
```java
public class TestBankAccount {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("ACC001", "John Doe", 1000.0);

        account.displayAccountInfo();
        account.deposit(500.0);
        account.displayBalance();
    }
}
```

**Expected Output:**
```
========================================
         ACCOUNT INFORMATION
========================================
Account Number: ACC001
Account Holder: John Doe
Current Balance: $1000.0
========================================
Deposited: $500.0
New balance: $1500.0
Current Balance: $1500.0
```

---

### Step 6: Implement Transaction History Display

**What to do:**
Create a method to display all transactions recorded in the account history.

**Code Template:**
```java
public void displayTransactionHistory() {
    System.out.println("========================================");
    System.out.println("       TRANSACTION HISTORY             ");
    System.out.println("========================================");

    if (transactionHistory.isEmpty()) {
        System.out.println("No transactions yet.");
    } else {
        for (int i = 0; i < transactionHistory.size(); i++) {
            System.out.println((i + 1) + ". " + transactionHistory.get(i));
        }
    }

    System.out.println("========================================");
}
```

**Testing:**
```java
public class TestBankAccount {
    public static void main(String[] args) {
        BankAccount account = new BankAccount("ACC001", "John Doe", 1000.0);

        account.deposit(500.0);
        account.withdraw(200.0);
        account.deposit(1000.0);
        account.withdraw(300.0);

        account.displayTransactionHistory();
    }
}
```

**Expected Output:**
```
Deposited: $500.0
New balance: $1500.0
Withdrawn: $200.0
New balance: $1300.0
Deposited: $1000.0
New balance: $2300.0
Withdrawn: $300.0
New balance: $2000.0
========================================
       TRANSACTION HISTORY
========================================
1. Initial deposit: $1000.0 | Balance: $1000.0
2. Deposit: $500.0 | Balance: $1500.0
3. Withdrawal: $200.0 | Balance: $1300.0
4. Deposit: $1000.0 | Balance: $2300.0
5. Withdrawal: $300.0 | Balance: $2000.0
========================================
```

---

## ✅ Testing Checklist

Test your BankAccount class against these scenarios:

### Basic Functionality:
- [ ] Create account with valid initial balance
- [ ] Create account with negative initial balance (should show error and set to 0)
- [ ] Deposit valid amount
- [ ] Deposit negative amount (should show error)
- [ ] Deposit zero (should show error)
- [ ] Withdraw valid amount
- [ ] Withdraw more than balance (should show error)
- [ ] Withdraw negative amount (should show error)

### Edge Cases:
- [ ] Withdraw exact balance amount (should work, balance becomes 0)
- [ ] Create account with zero initial balance
- [ ] Multiple deposits and withdrawals
- [ ] Check balance after each transaction
- [ ] Verify transaction history is accurate

### Expected Behavior:
```java
public class ComprehensiveTest {
    public static void main(String[] args) {
        System.out.println("=== Test 1: Create Account ===");
        BankAccount account = new BankAccount("ACC123", "Alice Johnson", 5000.0);
        account.displayAccountInfo();

        System.out.println("\n=== Test 2: Deposits ===");
        account.deposit(1000.0);
        account.deposit(500.0);

        System.out.println("\n=== Test 3: Withdrawals ===");
        account.withdraw(2000.0);
        account.withdraw(10000.0);  // Should fail - insufficient funds

        System.out.println("\n=== Test 4: Check Balance ===");
        account.displayBalance();

        System.out.println("\n=== Test 5: Transaction History ===");
        account.displayTransactionHistory();

        System.out.println("\n=== Test 6: Invalid Operations ===");
        account.deposit(-500.0);   // Should fail
        account.withdraw(-200.0);  // Should fail
        account.deposit(0);        // Should fail

        System.out.println("\n=== Final Account Info ===");
        account.displayAccountInfo();
    }
}
```

---

## 🎓 Complete Solution (Only Look After Attempting!)

### Full BankAccount Class Implementation

```java
import java.util.ArrayList;

public class BankAccount {
    // Private instance variables (Encapsulation)
    private String accountNumber;
    private String accountHolder;
    private double balance;
    private ArrayList<String> transactionHistory;

    // Constructor with validation
    public BankAccount(String accountNumber, String accountHolder, double initialBalance) {
        // Validate initial balance
        if (initialBalance < 0) {
            System.out.println("Error: Initial balance cannot be negative. Setting to 0.");
            initialBalance = 0;
        }

        // Initialize instance variables
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.balance = initialBalance;
        this.transactionHistory = new ArrayList<>();

        // Record initial deposit if any
        if (initialBalance > 0) {
            transactionHistory.add("Initial deposit: $" + initialBalance + " | Balance: $" + balance);
        }
    }

    // Deposit method with validation
    public void deposit(double amount) {
        if (amount <= 0) {
            System.out.println("Error: Deposit amount must be positive.");
            return;
        }

        balance += amount;
        String transaction = "Deposit: $" + amount + " | Balance: $" + balance;
        transactionHistory.add(transaction);

        System.out.println("Deposited: $" + amount);
        System.out.println("New balance: $" + balance);
    }

    // Withdraw method with validation
    public void withdraw(double amount) {
        if (amount <= 0) {
            System.out.println("Error: Withdrawal amount must be positive.");
            return;
        }

        if (amount > balance) {
            System.out.println("Error: Insufficient funds. Current balance: $" + balance);
            return;
        }

        balance -= amount;
        String transaction = "Withdrawal: $" + amount + " | Balance: $" + balance;
        transactionHistory.add(transaction);

        System.out.println("Withdrawn: $" + amount);
        System.out.println("New balance: $" + balance);
    }

    // Get current balance
    public double getBalance() {
        return balance;
    }

    // Display formatted balance
    public void displayBalance() {
        System.out.println("Current Balance: $" + balance);
    }

    // Display account information
    public void displayAccountInfo() {
        System.out.println("========================================");
        System.out.println("         ACCOUNT INFORMATION           ");
        System.out.println("========================================");
        System.out.println("Account Number: " + accountNumber);
        System.out.println("Account Holder: " + accountHolder);
        System.out.println("Current Balance: $" + balance);
        System.out.println("========================================");
    }

    // Display transaction history
    public void displayTransactionHistory() {
        System.out.println("========================================");
        System.out.println("       TRANSACTION HISTORY             ");
        System.out.println("========================================");

        if (transactionHistory.isEmpty()) {
            System.out.println("No transactions yet.");
        } else {
            for (int i = 0; i < transactionHistory.size(); i++) {
                System.out.println((i + 1) + ". " + transactionHistory.get(i));
            }
        }

        System.out.println("========================================");
    }

    // Getter methods (Encapsulation)
    public String getAccountNumber() {
        return accountNumber;
    }

    public String getAccountHolder() {
        return accountHolder;
    }

    // Main method for testing
    public static void main(String[] args) {
        System.out.println("=== Creating Bank Account ===");
        BankAccount account = new BankAccount("ACC12345", "John Doe", 1000.0);
        account.displayAccountInfo();

        System.out.println("\n=== Performing Transactions ===");
        account.deposit(500.0);
        account.deposit(250.0);
        account.withdraw(300.0);
        account.withdraw(200.0);

        System.out.println("\n=== Testing Invalid Operations ===");
        account.deposit(-100.0);      // Invalid: negative amount
        account.withdraw(5000.0);     // Invalid: insufficient funds
        account.withdraw(-50.0);      // Invalid: negative amount

        System.out.println("\n=== Checking Balance ===");
        account.displayBalance();

        System.out.println("\n=== Transaction History ===");
        account.displayTransactionHistory();

        System.out.println("\n=== Final Account Info ===");
        account.displayAccountInfo();
    }
}
```

**Sample Output:**
```
=== Creating Bank Account ===
========================================
         ACCOUNT INFORMATION
========================================
Account Number: ACC12345
Account Holder: John Doe
Current Balance: $1000.0
========================================

=== Performing Transactions ===
Deposited: $500.0
New balance: $1500.0
Deposited: $250.0
New balance: $1750.0
Withdrawn: $300.0
New balance: $1450.0
Withdrawn: $200.0
New balance: $1250.0

=== Testing Invalid Operations ===
Error: Deposit amount must be positive.
Error: Insufficient funds. Current balance: $1250.0
Error: Withdrawal amount must be positive.

=== Checking Balance ===
Current Balance: $1250.0

=== Transaction History ===
========================================
       TRANSACTION HISTORY
========================================
1. Initial deposit: $1000.0 | Balance: $1000.0
2. Deposit: $500.0 | Balance: $1500.0
3. Deposit: $250.0 | Balance: $1750.0
4. Withdrawal: $300.0 | Balance: $1450.0
5. Withdrawal: $200.0 | Balance: $1250.0
========================================

=== Final Account Info ===
========================================
         ACCOUNT INFORMATION
========================================
Account Number: ACC12345
Account Holder: John Doe
Current Balance: $1250.0
========================================
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these bonus features:

### 1. Interest Calculation
```java
public void addInterest(double interestRate) {
    double interest = balance * (interestRate / 100);
    balance += interest;
    String transaction = "Interest added (" + interestRate + "%): $" + interest + " | Balance: $" + balance;
    transactionHistory.add(transaction);
    System.out.println("Interest of $" + interest + " added to account.");
}
```

### 2. Transfer to Another Account
```java
public void transferTo(BankAccount targetAccount, double amount) {
    if (amount <= 0) {
        System.out.println("Error: Transfer amount must be positive.");
        return;
    }

    if (amount > balance) {
        System.out.println("Error: Insufficient funds for transfer.");
        return;
    }

    this.withdraw(amount);
    targetAccount.deposit(amount);
    System.out.println("Transferred $" + amount + " to " + targetAccount.getAccountHolder());
}
```

### 3. Minimum Balance Requirement
```java
private double minimumBalance = 100.0;

public void withdraw(double amount) {
    if (amount <= 0) {
        System.out.println("Error: Withdrawal amount must be positive.");
        return;
    }

    if ((balance - amount) < minimumBalance) {
        System.out.println("Error: Cannot withdraw. Minimum balance of $" + minimumBalance + " required.");
        return;
    }

    balance -= amount;
    // ... rest of withdrawal logic
}
```

### 4. Transaction Timestamps
```java
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

private String getCurrentTimestamp() {
    LocalDateTime now = LocalDateTime.now();
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    return now.format(formatter);
}

public void deposit(double amount) {
    // ... validation ...
    balance += amount;
    String transaction = "[" + getCurrentTimestamp() + "] Deposit: $" + amount + " | Balance: $" + balance;
    transactionHistory.add(transaction);
    // ... rest of method
}
```

---

## 💼 How This Relates to Selenium

This BankAccount project demonstrates key patterns you'll use in Selenium automation:

### Pattern 1: Page Object Structure
```java
// BankAccount structure is similar to Page Object
public class LoginPage {
    private WebDriver driver;        // Like accountNumber, accountHolder
    private String url;

    // Constructor initializes the page
    public LoginPage(WebDriver driver) {
        this.driver = driver;
        this.url = "https://example.com/login";
    }

    // Methods perform actions (like deposit/withdraw)
    public void login(String username, String password) {
        // find elements, enter data, click button
    }

    // Getters for verification (like getBalance)
    public String getPageTitle() {
        return driver.getTitle();
    }
}
```

### Pattern 2: Validation in Methods
```java
// Just like validating deposit/withdraw amounts
public void enterUsername(String username) {
    if (username == null || username.isEmpty()) {
        throw new IllegalArgumentException("Username cannot be empty");
    }
    driver.findElement(By.id("username")).sendKeys(username);
}
```

### Pattern 3: State Management
```java
// Transaction history is like test execution logs
private ArrayList<String> testSteps = new ArrayList<>();

public void clickLoginButton() {
    driver.findElement(By.id("login")).click();
    testSteps.add("Clicked login button at " + getCurrentTime());
}

public void displayTestSteps() {
    for (String step : testSteps) {
        System.out.println(step);
    }
}
```

### Pattern 4: Encapsulation
```java
// Private locators, public actions (like private balance, public deposit)
public class CartPage {
    private String addToCartButton = "button.add-to-cart";  // Private
    private String cartIcon = "div.cart-icon";               // Private

    // Public method exposes functionality
    public void addItemToCart(String itemName) {
        // implementation uses private locators
    }
}
```

**Key Insight:** The BankAccount class structure (private data, public methods, validation, state management) is exactly how you'll structure Page Objects in Selenium frameworks!

---

## 🎯 Project Completed!

**Congratulations!** You've built a complete BankAccount class demonstrating:
- ✅ Class design and encapsulation
- ✅ Constructor with validation
- ✅ Instance methods with business logic
- ✅ State management (balance, transactions)
- ✅ Data validation and error handling
- ✅ Getters for controlled access
- ✅ ArrayList for storing history

**Time Spent:** ___ minutes
**Features Implemented:** __/7 core features
**Bonus Features:** __/4 bonus features
**Code Quality:** Self-rate __/10

**Next Step:** Review **Day-4-Cheat-Sheet.md** for quick reference on all OOP concepts!

---

## 📝 Reflection Questions

1. **How does encapsulation protect the BankAccount balance?**

2. **Why is validation important in the deposit and withdraw methods?**

3. **How is the BankAccount class structure similar to a Page Object in Selenium?**

4. **What would happen if instance variables were public instead of private?**

5. **How does ArrayList help manage transaction history?**

**Think about these as you move to tomorrow's OOP Part 2 lesson on inheritance and polymorphism!**

---
