# DAY 2 MINI PROJECT: NUMBER GUESSING GAME

## 🎯 Project Overview

**What You're Building:**
An interactive number guessing game where the computer randomly selects a number between 1 and 100, and the user tries to guess it within a limited number of attempts. The program provides "higher" or "lower" hints after each guess.

**Why This Project:**
- Reinforces all Day 2 control flow concepts: if-else, loops, conditions
- Combines Day 1 concepts: variables, operators, input/output
- Simulates test retry logic and validation patterns used in automation
- Creates an engaging, portfolio-ready Java program

**Time Required:** 30-45 minutes

**Skills Practiced:**
- Random number generation
- For/while loops for iteration
- If-else statements for decision making
- Boolean flags and counters
- Input validation
- User interaction patterns
- Code organization

---

## 📋 Requirements

### Must-Have Features (Basic Version):
1. **Generate random number** - between 1 and 100
2. **User input** - accept guesses from the user (using Scanner)
3. **Feedback system** - tell user if guess is too high, too low, or correct
4. **Attempt tracking** - count and display number of attempts
5. **Win condition** - congratulate user when they guess correctly
6. **Attempt limit** - maximum 7 guesses allowed
7. **Lose condition** - reveal number if user runs out of attempts

### Nice-to-Have (Advanced Version):
1. **Input validation** - reject invalid inputs (non-numbers, out of range)
2. **Guess history** - track and display all previous guesses
3. **Duplicate detection** - warn if user guesses same number twice
4. **Play again option** - allow multiple rounds without restarting
5. **Difficulty levels** - easy (1-50), medium (1-100), hard (1-500)
6. **Best score tracking** - remember the lowest attempt count

---

## 🏗️ Project Structure

**Files Needed:**
1. `NumberGuessingGame.java` - Main game class

**Key Components:**
- Random number generator: `new Random().nextInt(100) + 1`
- User input scanner: `Scanner scanner = new Scanner(System.in)`
- Game loop: while or for loop for multiple attempts
- Comparison logic: if-else to compare guess to target
- Counters: track attempts and maintain game state

**Variables Needed:**
```java
int targetNumber;      // The secret number to guess
int userGuess;         // User's current guess
int attempts;          // Number of guesses made
int maxAttempts;       // Maximum allowed guesses
boolean hasWon;        // Flag to track if user won
Scanner scanner;       // For reading user input
```

---

## 📝 Step-by-Step Guide

### Step 1: Set Up Project Structure and Imports

**What to do:**
Create the class, import necessary utilities, and set up the main method.

**Code Template:**
```java
import java.util.Scanner;
import java.util.Random;

public class NumberGuessingGame {
    public static void main(String[] args) {
        // Game code will go here
    }
}
```

**Explanation:**
- `Scanner` - reads user input from console
- `Random` - generates random numbers
- Both are in `java.util` package, so we import them

---

### Step 2: Initialize Game Variables

**What to do:**
Declare and initialize all variables needed for the game.

**Code Template:**
```java
public static void main(String[] args) {
    // Initialize Random and Scanner
    Random random = new Random();
    Scanner scanner = new Scanner(System.in);

    // Game configuration
    final int MIN_NUMBER = 1;
    final int MAX_NUMBER = 100;
    final int MAX_ATTEMPTS = 7;

    // Generate random target number
    int targetNumber = random.nextInt(MAX_NUMBER) + 1;

    // Game state variables
    int attempts = 0;
    boolean hasWon = false;

    // TODO: Rest of game logic
}
```

**Explanation:**
- `random.nextInt(100)` generates 0-99, so we add 1 to get 1-100
- Constants (final) for configuration values
- `hasWon` flag tracks game outcome
- `attempts` counts how many guesses user has made

**Testing:**
Temporarily print the target number to verify it's working:
```java
System.out.println("DEBUG: Target is " + targetNumber);  // Remove later
```

---

### Step 3: Display Welcome Message

**What to do:**
Print a professional welcome screen with game instructions.

**Code Template:**
```java
// Welcome message
System.out.println("╔════════════════════════════════════════╗");
System.out.println("║     NUMBER GUESSING GAME v1.0          ║");
System.out.println("╚════════════════════════════════════════╝");
System.out.println();
System.out.println("I'm thinking of a number between " + MIN_NUMBER + " and " + MAX_NUMBER);
System.out.println("You have " + MAX_ATTEMPTS + " attempts to guess it!");
System.out.println("Let's begin...");
System.out.println("========================================");
```

**Expected Output:**
```
╔════════════════════════════════════════╗
║     NUMBER GUESSING GAME v1.0          ║
╚════════════════════════════════════════╝

I'm thinking of a number between 1 and 100
You have 7 attempts to guess it!
Let's begin...
========================================
```

---

### Step 4: Implement Main Game Loop

**What to do:**
Create a loop that runs until user wins or runs out of attempts.

**Code Template:**
```java
// Main game loop
while (attempts < MAX_ATTEMPTS && !hasWon) {
    attempts++;
    System.out.println();
    System.out.print("Attempt #" + attempts + " - Enter your guess: ");

    // TODO: Get user input
    // TODO: Validate input
    // TODO: Compare guess to target
    // TODO: Provide feedback
}
```

**Why this loop condition?**
- `attempts < MAX_ATTEMPTS` - stop if user runs out of attempts
- `!hasWon` - stop if user guesses correctly
- Loop continues while BOTH conditions are true (haven't won AND attempts remain)

**Testing:**
Run the loop to verify it iterates 7 times maximum.

---

### Step 5: Get and Validate User Input

**What to do:**
Read user input and validate it's a number within the valid range.

**Code Template:**
```java
while (attempts < MAX_ATTEMPTS && !hasWon) {
    attempts++;
    System.out.println();
    System.out.print("Attempt #" + attempts + " - Enter your guess: ");

    // Get user input
    if (!scanner.hasNextInt()) {
        System.out.println("⚠ Invalid input! Please enter a number.");
        scanner.next();  // Clear the invalid input
        attempts--;  // Don't count this as an attempt
        continue;  // Skip to next iteration
    }

    int userGuess = scanner.nextInt();

    // Validate range
    if (userGuess < MIN_NUMBER || userGuess > MAX_NUMBER) {
        System.out.println("⚠ Please enter a number between " + MIN_NUMBER + " and " + MAX_NUMBER);
        attempts--;  // Don't count this as an attempt
        continue;  // Skip to next iteration
    }

    // TODO: Compare guess to target
}
```

**Explanation:**
- `scanner.hasNextInt()` checks if next input is an integer
- If not a number: print error, clear bad input, don't count attempt
- `continue;` skips rest of loop and starts next iteration
- Range validation ensures guess is between 1 and 100
- Invalid inputs don't count against attempt limit (user-friendly)

---

### Step 6: Compare Guess to Target and Provide Feedback

**What to do:**
Use if-else to compare the guess and give appropriate feedback.

**Code Template:**
```java
    // Previous code...
    int userGuess = scanner.nextInt();

    // Validate range (from previous step)
    if (userGuess < MIN_NUMBER || userGuess > MAX_NUMBER) {
        System.out.println("⚠ Please enter a number between " + MIN_NUMBER + " and " + MAX_NUMBER);
        attempts--;
        continue;
    }

    // Compare guess to target
    if (userGuess == targetNumber) {
        hasWon = true;
        System.out.println("🎉 Congratulations! You guessed it!");
    } else if (userGuess < targetNumber) {
        System.out.println("📈 Too low! Try a higher number.");
        System.out.println("Attempts remaining: " + (MAX_ATTEMPTS - attempts));
    } else {
        System.out.println("📉 Too high! Try a lower number.");
        System.out.println("Attempts remaining: " + (MAX_ATTEMPTS - attempts));
    }
}  // End of while loop
```

**Logic Breakdown:**
- If guess equals target: user wins, set `hasWon = true`
- If guess is less than target: hint "too low"
- If guess is greater than target: hint "too high"
- Display remaining attempts to keep user informed

**Testing:**
Test all three paths:
1. Correct guess (equals target)
2. Too low guess (less than target)
3. Too high guess (greater than target)

---

### Step 7: Display Final Result

**What to do:**
After the loop ends, check if user won or lost and display appropriate message.

**Code Template:**
```java
// After the while loop ends

System.out.println();
System.out.println("========================================");

if (hasWon) {
    System.out.println("🏆 VICTORY! 🏆");
    System.out.println("You found the number " + targetNumber + " in " + attempts + " attempts!");

    // Performance rating
    if (attempts <= 3) {
        System.out.println("⭐⭐⭐ Excellent! You're a master guesser!");
    } else if (attempts <= 5) {
        System.out.println("⭐⭐ Great job! Well done!");
    } else {
        System.out.println("⭐ You made it! Nice work!");
    }
} else {
    System.out.println("😞 GAME OVER");
    System.out.println("You've used all " + MAX_ATTEMPTS + " attempts.");
    System.out.println("The number was: " + targetNumber);
    System.out.println("Better luck next time!");
}

System.out.println("========================================");

// Close the scanner
scanner.close();
```

**Explanation:**
- Check `hasWon` flag to determine outcome
- Display different messages for win vs. loss
- Show performance rating based on attempts
- Always close Scanner when done: `scanner.close();`

---

### Step 8: Add Final Touches

**What to do:**
Add helpful statistics and polish the output.

**Code Template:**
```java
// Before closing message
System.out.println();
System.out.println("GAME STATISTICS:");
System.out.println("Target Number: " + targetNumber);
System.out.println("Your Attempts: " + attempts);
if (hasWon) {
    double efficiency = ((double) attempts / MAX_ATTEMPTS) * 100;
    System.out.println("Efficiency: " + String.format("%.1f%%", efficiency) + " of max attempts used");
}
System.out.println();
System.out.println("Thanks for playing!");
```

---

## ✅ Testing Checklist

Test your game against these scenarios:

### Test Case 1: Win on First Try
- [ ] Make your first guess the target number
- [ ] Should display victory message
- [ ] Should show 1 attempt
- [ ] Should show "Excellent!" rating

### Test Case 2: Win Within Attempts
- [ ] Guess several times, find the number before attempt 7
- [ ] Should track attempts correctly
- [ ] Should provide accurate "higher/lower" hints
- [ ] Should stop after correct guess

### Test Case 3: Lose (Run Out of Attempts)
- [ ] Make 7 wrong guesses
- [ ] Should display "Game Over" message
- [ ] Should reveal the target number
- [ ] Should show all 7 attempts used

### Test Case 4: Invalid Input - Non-Number
- [ ] Enter text like "abc" when prompted
- [ ] Should show error message
- [ ] Should not count as an attempt
- [ ] Should allow retry

### Test Case 5: Invalid Input - Out of Range
- [ ] Enter numbers like 0, -5, 200
- [ ] Should show error message
- [ ] Should not count as an attempt
- [ ] Should prompt for valid range

### Test Case 6: Optimal Strategy
- [ ] Use binary search strategy (start with 50)
- [ ] Should be able to find any number in 7 or fewer guesses
- [ ] Hints should guide you correctly

**Example Game Flow:**
```
Target: 67 (you don't know this)
Guess 50 → "Too low"
Guess 75 → "Too high"
Guess 62 → "Too low"
Guess 68 → "Too high"
Guess 65 → "Too low"
Guess 67 → "Correct!" (6 attempts)
```

---

## 🎓 Complete Solution (Only Look After Attempting!)

### Basic Implementation

```java
import java.util.Scanner;
import java.util.Random;

public class NumberGuessingGame {
    public static void main(String[] args) {
        // Initialize utilities
        Random random = new Random();
        Scanner scanner = new Scanner(System.in);

        // Game configuration
        final int MIN_NUMBER = 1;
        final int MAX_NUMBER = 100;
        final int MAX_ATTEMPTS = 7;

        // Generate random target number
        int targetNumber = random.nextInt(MAX_NUMBER) + 1;

        // Game state
        int attempts = 0;
        boolean hasWon = false;

        // Welcome message
        System.out.println("╔════════════════════════════════════════╗");
        System.out.println("║     NUMBER GUESSING GAME v1.0          ║");
        System.out.println("╚════════════════════════════════════════╝");
        System.out.println();
        System.out.println("I'm thinking of a number between " + MIN_NUMBER + " and " + MAX_NUMBER);
        System.out.println("You have " + MAX_ATTEMPTS + " attempts to guess it!");
        System.out.println("Let's begin...");
        System.out.println("========================================");

        // Main game loop
        while (attempts < MAX_ATTEMPTS && !hasWon) {
            attempts++;
            System.out.println();
            System.out.print("Attempt #" + attempts + " - Enter your guess: ");

            // Validate input is a number
            if (!scanner.hasNextInt()) {
                System.out.println("⚠ Invalid input! Please enter a number.");
                scanner.next();  // Clear invalid input
                attempts--;  // Don't count this attempt
                continue;
            }

            int userGuess = scanner.nextInt();

            // Validate range
            if (userGuess < MIN_NUMBER || userGuess > MAX_NUMBER) {
                System.out.println("⚠ Please enter a number between " + MIN_NUMBER + " and " + MAX_NUMBER);
                attempts--;
                continue;
            }

            // Compare guess to target
            if (userGuess == targetNumber) {
                hasWon = true;
                System.out.println("🎉 Congratulations! You guessed it!");
            } else if (userGuess < targetNumber) {
                System.out.println("📈 Too low! Try a higher number.");
                System.out.println("Attempts remaining: " + (MAX_ATTEMPTS - attempts));
            } else {
                System.out.println("📉 Too high! Try a lower number.");
                System.out.println("Attempts remaining: " + (MAX_ATTEMPTS - attempts));
            }
        }

        // Display final result
        System.out.println();
        System.out.println("========================================");

        if (hasWon) {
            System.out.println("🏆 VICTORY! 🏆");
            System.out.println("You found the number " + targetNumber + " in " + attempts + " attempts!");

            // Performance rating
            if (attempts <= 3) {
                System.out.println("⭐⭐⭐ Excellent! You're a master guesser!");
            } else if (attempts <= 5) {
                System.out.println("⭐⭐ Great job! Well done!");
            } else {
                System.out.println("⭐ You made it! Nice work!");
            }
        } else {
            System.out.println("😞 GAME OVER");
            System.out.println("You've used all " + MAX_ATTEMPTS + " attempts.");
            System.out.println("The number was: " + targetNumber);
            System.out.println("Better luck next time!");
        }

        // Game statistics
        System.out.println();
        System.out.println("GAME STATISTICS:");
        System.out.println("Target Number: " + targetNumber);
        System.out.println("Your Attempts: " + attempts);

        System.out.println("========================================");
        System.out.println("Thanks for playing!");

        // Clean up
        scanner.close();
    }
}
```

---

### Advanced Implementation (With All Bonus Features)

```java
import java.util.Scanner;
import java.util.Random;
import java.util.ArrayList;

public class NumberGuessingGameAdvanced {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        Random random = new Random();
        boolean playAgain = true;
        int bestScore = Integer.MAX_VALUE;  // Track best score across games

        while (playAgain) {
            // Difficulty selection
            System.out.println("╔════════════════════════════════════════╗");
            System.out.println("║     NUMBER GUESSING GAME v2.0          ║");
            System.out.println("╚════════════════════════════════════════╝");
            System.out.println();
            System.out.println("Select Difficulty:");
            System.out.println("1. Easy (1-50, 8 attempts)");
            System.out.println("2. Medium (1-100, 7 attempts)");
            System.out.println("3. Hard (1-500, 10 attempts)");
            System.out.print("Enter choice (1-3): ");

            int difficulty = scanner.nextInt();
            int MIN_NUMBER = 1;
            int MAX_NUMBER;
            int MAX_ATTEMPTS;

            switch (difficulty) {
                case 1:
                    MAX_NUMBER = 50;
                    MAX_ATTEMPTS = 8;
                    System.out.println("Easy mode selected!");
                    break;
                case 3:
                    MAX_NUMBER = 500;
                    MAX_ATTEMPTS = 10;
                    System.out.println("Hard mode selected! Good luck!");
                    break;
                default:
                    MAX_NUMBER = 100;
                    MAX_ATTEMPTS = 7;
                    System.out.println("Medium mode selected!");
            }

            int targetNumber = random.nextInt(MAX_NUMBER) + 1;
            int attempts = 0;
            boolean hasWon = false;
            ArrayList<Integer> guessHistory = new ArrayList<>();

            System.out.println();
            System.out.println("I'm thinking of a number between " + MIN_NUMBER + " and " + MAX_NUMBER);
            System.out.println("You have " + MAX_ATTEMPTS + " attempts.");
            System.out.println("========================================");

            // Main game loop
            while (attempts < MAX_ATTEMPTS && !hasWon) {
                attempts++;
                System.out.println();
                System.out.print("Attempt #" + attempts + " - Enter your guess: ");

                if (!scanner.hasNextInt()) {
                    System.out.println("⚠ Invalid input! Please enter a number.");
                    scanner.next();
                    attempts--;
                    continue;
                }

                int userGuess = scanner.nextInt();

                if (userGuess < MIN_NUMBER || userGuess > MAX_NUMBER) {
                    System.out.println("⚠ Please enter a number between " + MIN_NUMBER + " and " + MAX_NUMBER);
                    attempts--;
                    continue;
                }

                // Check for duplicate guess
                if (guessHistory.contains(userGuess)) {
                    System.out.println("⚠ You already guessed " + userGuess + "! Try a different number.");
                    attempts--;
                    continue;
                }

                guessHistory.add(userGuess);

                // Compare and provide feedback
                if (userGuess == targetNumber) {
                    hasWon = true;
                    System.out.println("🎉 Congratulations! You guessed it!");
                } else if (userGuess < targetNumber) {
                    System.out.println("📈 Too low! Try a higher number.");

                    // Give extra hint if close
                    int difference = targetNumber - userGuess;
                    if (difference <= 5) {
                        System.out.println("💡 You're very close!");
                    } else if (difference <= 10) {
                        System.out.println("💡 You're getting warmer!");
                    }

                    System.out.println("Attempts remaining: " + (MAX_ATTEMPTS - attempts));
                } else {
                    System.out.println("📉 Too high! Try a lower number.");

                    // Give extra hint if close
                    int difference = userGuess - targetNumber;
                    if (difference <= 5) {
                        System.out.println("💡 You're very close!");
                    } else if (difference <= 10) {
                        System.out.println("💡 You're getting warmer!");
                    }

                    System.out.println("Attempts remaining: " + (MAX_ATTEMPTS - attempts));
                }
            }

            // Display results
            System.out.println();
            System.out.println("========================================");

            if (hasWon) {
                System.out.println("🏆 VICTORY! 🏆");
                System.out.println("You found " + targetNumber + " in " + attempts + " attempts!");

                if (attempts < bestScore) {
                    bestScore = attempts;
                    System.out.println("🌟 NEW BEST SCORE! 🌟");
                }

                if (attempts <= 3) {
                    System.out.println("⭐⭐⭐ Excellent!");
                } else if (attempts <= 5) {
                    System.out.println("⭐⭐ Great job!");
                } else {
                    System.out.println("⭐ Nice work!");
                }
            } else {
                System.out.println("😞 GAME OVER");
                System.out.println("The number was: " + targetNumber);
            }

            // Statistics
            System.out.println();
            System.out.println("GAME STATISTICS:");
            System.out.println("Target: " + targetNumber);
            System.out.println("Your guesses: " + guessHistory);
            System.out.println("Total attempts: " + attempts);
            if (bestScore != Integer.MAX_VALUE) {
                System.out.println("Your best score: " + bestScore + " attempts");
            }

            // Play again?
            System.out.println();
            System.out.print("Play again? (yes/no): ");
            String response = scanner.next().toLowerCase();
            playAgain = response.startsWith("y");

            System.out.println();
        }

        System.out.println("========================================");
        System.out.println("Thanks for playing!");
        System.out.println("Final best score: " + (bestScore == Integer.MAX_VALUE ? "N/A" : bestScore + " attempts"));
        System.out.println("========================================");

        scanner.close();
    }
}
```

**Advanced Features Explained:**
- **Difficulty levels** - Different ranges and attempt limits
- **Guess history** - ArrayList tracks all guesses
- **Duplicate detection** - Prevents guessing same number twice
- **Proximity hints** - "Very close" when within 5, "Warmer" within 10
- **Play again** - Loop allows multiple games
- **Best score** - Tracks lowest attempt count across sessions

---

## 🚀 Enhancement Ideas

### Enhancement 1: Hint System
Add hints that cost attempts:
```java
System.out.println("Type 'hint' for a clue (costs 1 attempt)");

if (input.equals("hint")) {
    attempts++;
    if (targetNumber % 2 == 0) {
        System.out.println("💡 The number is EVEN");
    } else {
        System.out.println("💡 The number is ODD");
    }
}
```

### Enhancement 2: Timer
Track how long the game takes:
```java
long startTime = System.currentTimeMillis();
// ... game logic ...
long endTime = System.currentTimeMillis();
long duration = (endTime - startTime) / 1000;
System.out.println("Time taken: " + duration + " seconds");
```

### Enhancement 3: Leaderboard
Save scores to a file (we'll learn file I/O in Week 2):
```java
// Pseudo-code for future implementation
saveScore(playerName, attempts, difficulty);
displayLeaderboard();
```

---

## 💼 How This Relates to Selenium

**Test Automation Parallels:**

### 1. Retry Logic (While Loop)
```java
// Guessing game
while (attempts < MAX_ATTEMPTS && !hasWon) {
    // Try to guess
}

// Selenium WebDriver wait
int maxRetries = 10;
int retryCount = 0;
boolean elementFound = false;

while (retryCount < maxRetries && !elementFound) {
    try {
        driver.findElement(By.id("submitButton"));
        elementFound = true;
    } catch (NoSuchElementException e) {
        Thread.sleep(500);
        retryCount++;
    }
}
```

### 2. Input Validation
```java
// Guessing game
if (userGuess < MIN || userGuess > MAX) {
    System.out.println("Invalid range!");
}

// Test data validation
if (username == null || username.isEmpty()) {
    throw new IllegalArgumentException("Username cannot be empty");
}
```

### 3. State Tracking (Boolean Flags)
```java
// Guessing game
boolean hasWon = false;

// Test automation
boolean testPassed = false;
boolean elementVisible = false;
boolean pageLoaded = false;
```

### 4. Attempt Counting
```java
// Guessing game
int attempts = 0;

// Flaky test retry mechanism
int retryCount = 0;
final int MAX_RETRIES = 3;

while (retryCount < MAX_RETRIES && !testPassed) {
    retryCount++;
    runTest();
}
```

---

## 🎯 Project Completion Checklist

Before marking this project complete:

- [ ] Program compiles without errors
- [ ] Random number generation works
- [ ] User input accepted via Scanner
- [ ] Input validation prevents crashes
- [ ] "Too high/low" feedback is accurate
- [ ] Attempt counter works correctly
- [ ] Win condition detected properly
- [ ] Lose condition shows target number
- [ ] Scanner is closed at end
- [ ] Tested with multiple playthroughs
- [ ] Code has meaningful variable names
- [ ] Code includes comments
- [ ] Ready to push to GitHub

---

## 📸 GitHub Ready!

**README.md for the project:**
```markdown
# Number Guessing Game

An interactive console game demonstrating Java control flow structures.

## Features
- Random number generation (1-100)
- Input validation
- Attempt tracking and limiting
- Helpful hints (higher/lower)
- Win/lose conditions
- Performance ratings

## Technologies
- Java 17
- Scanner for user input
- Random for number generation

## How to Play
1. Run NumberGuessingGame.java
2. Enter guesses when prompted
3. Follow the hints to find the number
4. Win within 7 attempts!

## Learning Goals
Day 2 project of my 4-week Java SDET mastery plan.
Demonstrates understanding of:
- If-else conditionals
- While loops
- Input validation
- Boolean flags
- Random generation
```

**Commit message:**
```
Day 2: Build Number Guessing Game with control flow

- Implemented random number generation
- Added while loop for game logic
- Input validation prevents invalid guesses
- Feedback system guides user to solution
- Track attempts and determine win/lose conditions
- Completed Day 2 of Java fundamentals
```

---

## 🎉 Congratulations!

**What You've Achieved:**
- ✅ Built an interactive Java game
- ✅ Used Scanner for user input
- ✅ Implemented while loops for game logic
- ✅ Applied if-else for decision making
- ✅ Validated user input properly
- ✅ Tracked game state with boolean flags

**Day 2 Progress:**
- Concepts learned: ✅
- Practice exercises: ✅
- Debugging challenges: ✅
- Mini project: ✅
- **Day 2 COMPLETE!** 🎉

---

## 🔮 Tomorrow Preview

**Day 3: Arrays & Strings**
You'll learn arrays, ArrayLists, and String manipulation - then build an Anagram Checker that compares strings.

**How today's project helps:**
- Loops → Iterate through arrays
- Conditionals → Compare string characters
- Input validation → Parse and validate strings

---

## 📝 Reflection

**Time spent on project:** ___ minutes

**Difficulty (1-10):** ___

**What I learned:**
-
-
-

**Challenges I faced:**
-
-

**Best attempt count I achieved:** ___ attempts

**Confidence with control flow (1-10):** ___

---

Move to **Day-2-Cheat-Sheet.md** for a quick reference guide!

**Project complete:** ✅
**Ready to push to GitHub:** Yes / Need polish

---
