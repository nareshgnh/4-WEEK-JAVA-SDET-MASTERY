# DAY 3 MINI PROJECT: ANAGRAM CHECKER

## 🎯 Project Overview

**What You're Building:**
An Anagram Checker that determines if two words or phrases are anagrams of each other. Anagrams are words formed by rearranging letters of another word (e.g., "listen" and "silent").

**Why This Project:**
- Reinforces array manipulation and sorting
- Practices string processing (cleanup, normalization)
- Uses character arrays and string methods
- Simulates test data validation scenarios
- Builds problem-solving skills with algorithms

**Time Required:** 30-45 minutes

**Skills Practiced:**
- String manipulation (toLowerCase, replace, trim)
- Converting strings to character arrays
- Sorting arrays
- Array comparison
- Input validation
- Method creation

---

## 📋 Requirements

### Must-Have Features:
1. **Accept two input strings** (words or phrases)
2. **Clean and normalize input** (remove spaces, convert to lowercase, remove special characters)
3. **Check if anagrams** using character frequency comparison
4. **Display result** with clear messaging
5. **Handle edge cases** (empty strings, null, different lengths, special characters)
6. **Multiple test cases** in main method

### Nice-to-Have (Bonus):
1. **Case-insensitive comparison** (already included in must-have)
2. **Ignore spaces and punctuation** for phrase anagrams
3. **Display sorted character arrays** to show the comparison
4. **User input** using Scanner (instead of hardcoded values)
5. **Multiple anagram pairs** processed in a loop

---

## 🏗️ Project Structure

**Classes Needed:**
1. `AnagramChecker.java` - Main class with anagram checking logic

**Methods to Implement:**
- `main()` - Entry point, test various anagram pairs
- `areAnagrams(String str1, String str2)` - Returns true if anagrams
- `cleanString(String str)` - Removes spaces, punctuation, converts to lowercase
- `sortString(String str)` - Converts to char array, sorts, returns string

---

## 📝 Step-by-Step Guide

### Step 1: Set Up Project Structure

Create a new Java class called `AnagramChecker` with the basic structure:

```java
public class AnagramChecker {
    public static void main(String[] args) {
        // We'll add test cases here
    }

    // Helper method to check if two strings are anagrams
    public static boolean areAnagrams(String str1, String str2) {
        // TODO: Implement logic
        return false;
    }

    // Helper method to clean and normalize string
    public static String cleanString(String str) {
        // TODO: Implement logic
        return "";
    }

    // Helper method to sort string characters
    public static String sortString(String str) {
        // TODO: Implement logic
        return "";
    }
}
```

**Testing:** Create the file and ensure it compiles without errors.

---

### Step 2: Implement cleanString() Method

**What to do:**
Remove spaces, punctuation, and convert to lowercase for fair comparison.

**Code Template:**
```java
public static String cleanString(String str) {
    // TODO: Remove all spaces
    // TODO: Remove all non-letter characters
    // TODO: Convert to lowercase
    // TODO: Return cleaned string

    // Hint: Use replace() to remove spaces
    // Hint: Use replaceAll("[^a-zA-Z]", "") to keep only letters
    // Hint: Use toLowerCase()
}
```

**Testing:**
```java
public static void main(String[] args) {
    System.out.println(cleanString("Listen!"));      // "listen"
    System.out.println(cleanString("Silent"));       // "silent"
    System.out.println(cleanString("A gentleman"));  // "agentleman"
    System.out.println(cleanString("Elegant man"));  // "elegantman"
}
```

**Expected Output:**
```
listen
silent
agentleman
elegantman
```

---

### Step 3: Implement sortString() Method

**What to do:**
Convert string to character array, sort it alphabetically, and convert back to string.

**Code Template:**
```java
import java.util.Arrays;

public static String sortString(String str) {
    // TODO: Convert string to character array
    char[] charArray = str.toCharArray();

    // TODO: Sort the character array
    Arrays.sort(charArray);

    // TODO: Convert character array back to string
    String sorted = new String(charArray);

    return sorted;
}
```

**Testing:**
```java
public static void main(String[] args) {
    System.out.println(sortString("listen"));  // "eilnst"
    System.out.println(sortString("silent"));  // "eilnst"
    System.out.println(sortString("hello"));   // "ehllo"
}
```

**Expected Output:**
```
eilnst
eilnst
ehllo
```

---

### Step 4: Implement areAnagrams() Method

**What to do:**
Use helper methods to clean and sort both strings, then compare.

**Code Template:**
```java
public static boolean areAnagrams(String str1, String str2) {
    // TODO: Handle null or empty strings
    if (str1 == null || str2 == null) {
        return false;
    }

    // TODO: Clean both strings
    String cleaned1 = cleanString(str1);
    String cleaned2 = cleanString(str2);

    // TODO: Check if lengths are different (can't be anagrams)
    if (cleaned1.length() != cleaned2.length()) {
        return false;
    }

    // TODO: Sort both strings
    String sorted1 = sortString(cleaned1);
    String sorted2 = sortString(cleaned2);

    // TODO: Compare sorted strings
    return sorted1.equals(sorted2);
}
```

**Testing:**
```java
public static void main(String[] args) {
    System.out.println(areAnagrams("listen", "silent"));  // true
    System.out.println(areAnagrams("hello", "world"));    // false
    System.out.println(areAnagrams("Dormitory", "Dirty room"));  // true
}
```

**Expected Output:**
```
true
false
true
```

---

### Step 5: Create Comprehensive Test Cases in main()

**What to do:**
Test various scenarios including edge cases.

**Code Template:**
```java
public static void main(String[] args) {
    System.out.println("===== ANAGRAM CHECKER =====\n");

    // Test case 1: Simple anagrams
    testAnagram("listen", "silent");

    // Test case 2: Not anagrams
    testAnagram("hello", "world");

    // Test case 3: Phrase anagrams (with spaces)
    testAnagram("Dormitory", "Dirty room");

    // Test case 4: With punctuation
    testAnagram("Conversation", "Voices rant on!");

    // Test case 5: Same word
    testAnagram("Test", "Test");

    // Test case 6: Different lengths
    testAnagram("short", "longer word");

    // Test case 7: Empty strings
    testAnagram("", "");

    // Test case 8: Case sensitivity
    testAnagram("Listen", "SILENT");
}

// Helper method to display test results
public static void testAnagram(String str1, String str2) {
    boolean result = areAnagrams(str1, str2);
    String symbol = result ? "✓" : "✗";

    System.out.printf("%s '%s' and '%s' -> %s\n",
                      symbol, str1, str2,
                      result ? "ARE anagrams" : "NOT anagrams");
}
```

---

## ✅ Testing Checklist

Test your project against these scenarios:

- [ ] Simple word anagrams: "listen" ↔ "silent"
- [ ] Non-anagrams: "hello" ↔ "world"
- [ ] Phrase anagrams: "A gentleman" ↔ "Elegant man"
- [ ] Case insensitive: "Listen" ↔ "SILENT"
- [ ] With punctuation: "Conversation" ↔ "Voices rant on!"
- [ ] Same word: "test" ↔ "test"
- [ ] Empty strings: "" ↔ ""
- [ ] Different lengths: "short" ↔ "longer word"
- [ ] Single character: "a" ↔ "a"
- [ ] All same characters: "aaa" ↔ "aaa"

**Expected Behavior:**
```
===== ANAGRAM CHECKER =====

✓ 'listen' and 'silent' -> ARE anagrams
✗ 'hello' and 'world' -> NOT anagrams
✓ 'Dormitory' and 'Dirty room' -> ARE anagrams
✓ 'Conversation' and 'Voices rant on!' -> ARE anagrams
✓ 'Test' and 'Test' -> ARE anagrams
✗ 'short' and 'longer word' -> NOT anagrams
✓ '' and '' -> ARE anagrams
✓ 'Listen' and 'SILENT' -> ARE anagrams
```

---

## 🎓 Complete Solution (Only Look After Attempting!)

### Full Implementation

```java
import java.util.Arrays;

public class AnagramChecker {

    public static void main(String[] args) {
        System.out.println("===== ANAGRAM CHECKER =====\n");

        // Test various anagram pairs
        testAnagram("listen", "silent");
        testAnagram("hello", "world");
        testAnagram("Dormitory", "Dirty room");
        testAnagram("The eyes", "They see");
        testAnagram("Conversation", "Voices rant on");
        testAnagram("Test", "Test");
        testAnagram("short", "longer word");
        testAnagram("", "");
        testAnagram("a", "a");
        testAnagram("Astronomer", "Moon starer");

        System.out.println("\n===== FAMOUS ANAGRAM PAIRS =====\n");
        testAnagram("William Shakespeare", "I am a weakish speller");
        testAnagram("Clint Eastwood", "Old West Action");
        testAnagram("The Morse Code", "Here come dots");
    }

    /**
     * Checks if two strings are anagrams of each other
     * @param str1 First string to compare
     * @param str2 Second string to compare
     * @return true if strings are anagrams, false otherwise
     */
    public static boolean areAnagrams(String str1, String str2) {
        // Handle null inputs
        if (str1 == null || str2 == null) {
            return false;
        }

        // Clean both strings (remove spaces, punctuation, convert to lowercase)
        String cleaned1 = cleanString(str1);
        String cleaned2 = cleanString(str2);

        // If lengths differ after cleaning, can't be anagrams
        if (cleaned1.length() != cleaned2.length()) {
            return false;
        }

        // Sort both strings and compare
        String sorted1 = sortString(cleaned1);
        String sorted2 = sortString(cleaned2);

        // Compare sorted strings
        return sorted1.equals(sorted2);
    }

    /**
     * Cleans string by removing spaces, punctuation, and converting to lowercase
     * @param str String to clean
     * @return Cleaned string containing only lowercase letters
     */
    public static String cleanString(String str) {
        // Remove all non-letter characters (spaces, punctuation, numbers)
        // [^a-zA-Z] means "anything that's NOT a letter"
        String cleaned = str.replaceAll("[^a-zA-Z]", "");

        // Convert to lowercase for case-insensitive comparison
        return cleaned.toLowerCase();
    }

    /**
     * Sorts characters in a string alphabetically
     * @param str String to sort
     * @return New string with characters sorted alphabetically
     */
    public static String sortString(String str) {
        // Convert string to character array
        char[] charArray = str.toCharArray();

        // Sort the array alphabetically
        Arrays.sort(charArray);

        // Convert character array back to string
        return new String(charArray);
    }

    /**
     * Helper method to test and display anagram comparison
     * @param str1 First string
     * @param str2 Second string
     */
    public static void testAnagram(String str1, String str2) {
        boolean result = areAnagrams(str1, str2);
        String symbol = result ? "✓" : "✗";

        System.out.printf("%s '%s' and '%s'\n",
                          symbol, str1, str2);
        System.out.printf("   -> %s\n\n",
                          result ? "ARE anagrams" : "NOT anagrams");
    }
}
```

**Output:**
```
===== ANAGRAM CHECKER =====

✓ 'listen' and 'silent'
   -> ARE anagrams

✗ 'hello' and 'world'
   -> NOT anagrams

✓ 'Dormitory' and 'Dirty room'
   -> ARE anagrams

✓ 'The eyes' and 'They see'
   -> ARE anagrams

✓ 'Conversation' and 'Voices rant on'
   -> ARE anagrams

✓ 'Test' and 'Test'
   -> ARE anagrams

✗ 'short' and 'longer word'
   -> NOT anagrams

✓ '' and ''
   -> ARE anagrams

✓ 'a' and 'a'
   -> ARE anagrams

✓ 'Astronomer' and 'Moon starer'
   -> ARE anagrams

===== FAMOUS ANAGRAM PAIRS =====

✓ 'William Shakespeare' and 'I am a weakish speller'
   -> ARE anagrams

✓ 'Clint Eastwood' and 'Old West Action'
   -> ARE anagrams

✓ 'The Morse Code' and 'Here come dots'
   -> ARE anagrams
```

---

## 🚀 Enhancement Ideas

Want to take it further? Try these:

### Enhancement 1: Character Frequency Map (Alternative Algorithm)
Instead of sorting, count frequency of each character.

```java
import java.util.HashMap;

public static boolean areAnagramsFrequency(String str1, String str2) {
    String cleaned1 = cleanString(str1);
    String cleaned2 = cleanString(str2);

    if (cleaned1.length() != cleaned2.length()) {
        return false;
    }

    // Count character frequencies
    HashMap<Character, Integer> charCount = new HashMap<>();

    // Add characters from first string
    for (char c : cleaned1.toCharArray()) {
        charCount.put(c, charCount.getOrDefault(c, 0) + 1);
    }

    // Subtract characters from second string
    for (char c : cleaned2.toCharArray()) {
        if (!charCount.containsKey(c)) {
            return false;  // Character not in first string
        }
        charCount.put(c, charCount.get(c) - 1);
        if (charCount.get(c) < 0) {
            return false;  // Too many of this character
        }
    }

    return true;
}
```

### Enhancement 2: User Input with Scanner
```java
import java.util.Scanner;

public static void main(String[] args) {
    Scanner scanner = new Scanner(System.in);

    System.out.println("===== ANAGRAM CHECKER =====");
    System.out.print("Enter first word/phrase: ");
    String str1 = scanner.nextLine();

    System.out.print("Enter second word/phrase: ");
    String str2 = scanner.nextLine();

    testAnagram(str1, str2);

    scanner.close();
}
```

### Enhancement 3: Find All Anagrams in a List
```java
public static void findAnagrams(String target, String[] wordList) {
    System.out.println("Anagrams of '" + target + "':");

    int count = 0;
    for (String word : wordList) {
        if (areAnagrams(target, word) && !target.equalsIgnoreCase(word)) {
            System.out.println("  - " + word);
            count++;
        }
    }

    System.out.println("Found " + count + " anagrams");
}

// Test it
public static void main(String[] args) {
    String[] words = {"listen", "silent", "enlist", "hello", "world", "tinsel"};
    findAnagrams("listen", words);
}
```

### Enhancement 4: Performance Comparison
```java
public static void main(String[] args) {
    String str1 = "Conversation";
    String str2 = "Voices rant on";

    // Measure sorting approach
    long start1 = System.nanoTime();
    boolean result1 = areAnagrams(str1, str2);
    long end1 = System.nanoTime();

    // Measure frequency approach
    long start2 = System.nanoTime();
    boolean result2 = areAnagramsFrequency(str1, str2);
    long end2 = System.nanoTime();

    System.out.println("Sorting approach: " + (end1 - start1) + " ns");
    System.out.println("Frequency approach: " + (end2 - start2) + " ns");
}
```

---

## 💼 How This Relates to Selenium

**Real-World Test Automation Scenarios:**

### Scenario 1: Password Strength Validation
```java
// Check if password contains all required character types
public static boolean hasAllCharacterTypes(String password) {
    boolean hasLower = false, hasUpper = false, hasDigit = false, hasSpecial = false;

    for (char c : password.toCharArray()) {
        if (Character.isLowerCase(c)) hasLower = true;
        if (Character.isUpperCase(c)) hasUpper = true;
        if (Character.isDigit(c)) hasDigit = true;
        if (!Character.isLetterOrDigit(c)) hasSpecial = true;
    }

    return hasLower && hasUpper && hasDigit && hasSpecial;
}
```

### Scenario 2: URL Comparison (Ignore Query Parameter Order)
```java
public static boolean sameUrlIgnoringParamOrder(String url1, String url2) {
    // Extract base URL
    String base1 = url1.contains("?") ? url1.substring(0, url1.indexOf("?")) : url1;
    String base2 = url2.contains("?") ? url2.substring(0, url2.indexOf("?")) : url2;

    if (!base1.equals(base2)) return false;

    // Extract and sort query parameters
    String query1 = url1.contains("?") ? url1.substring(url1.indexOf("?") + 1) : "";
    String query2 = url2.contains("?") ? url2.substring(url2.indexOf("?") + 1) : "";

    String[] params1 = query1.split("&");
    String[] params2 = query2.split("&");

    Arrays.sort(params1);
    Arrays.sort(params2);

    return Arrays.equals(params1, params2);
}
```

### Scenario 3: Element Text Normalization
```java
// In Selenium tests, comparing element text with expected value
public static boolean elementTextMatches(String actualText, String expectedText) {
    // Clean both texts (remove extra whitespace, normalize case)
    String cleaned1 = actualText.trim().replaceAll("\\s+", " ").toLowerCase();
    String cleaned2 = expectedText.trim().replaceAll("\\s+", " ").toLowerCase();

    return cleaned1.equals(cleaned2);
}

// Example usage:
// String elementText = driver.findElement(By.id("message")).getText();
// assert elementTextMatches(elementText, "Welcome User");
```

---

## 📊 Algorithm Complexity Analysis

**Sorting Approach:**
- Time Complexity: O(n log n) - due to Arrays.sort()
- Space Complexity: O(n) - for character arrays

**Frequency Approach:**
- Time Complexity: O(n) - single pass through both strings
- Space Complexity: O(1) - HashMap has at most 26 entries (alphabet)

**Which is better?**
- For short strings: Sorting is simpler and fast enough
- For long strings or performance-critical code: Frequency is more efficient
- For learning: Both teach important concepts!

---

## 🎯 Project Completion Checklist

**Code Quality:**
- [ ] All methods have clear, descriptive names
- [ ] Code includes comments explaining logic
- [ ] Handles edge cases (null, empty, special characters)
- [ ] Uses helper methods for organization
- [ ] Follows Java naming conventions

**Functionality:**
- [ ] Correctly identifies anagrams
- [ ] Case-insensitive comparison works
- [ ] Handles phrases with spaces
- [ ] Ignores punctuation
- [ ] Works with empty strings
- [ ] Returns false for different lengths

**Testing:**
- [ ] All test cases pass
- [ ] Edge cases tested
- [ ] Output is formatted clearly
- [ ] No exceptions thrown

**Bonus Features:**
- [ ] User input with Scanner
- [ ] Alternative algorithm implemented
- [ ] Performance comparison
- [ ] Find all anagrams in list

---

## 🎉 Congratulations!

**You've completed Day 3's project!**

**What you've accomplished:**
- ✅ Mastered string manipulation (trim, toLowerCase, replace)
- ✅ Worked with character arrays
- ✅ Used array sorting
- ✅ Created reusable helper methods
- ✅ Handled edge cases properly
- ✅ Built a complete, working program

**This project demonstrates skills for:**
- Data validation in test automation
- Text processing and comparison
- Problem-solving with algorithms
- Writing clean, maintainable code

**GitHub-Ready:**
This project is portfolio-worthy! Push it to GitHub with a README explaining:
- What it does
- How to use it
- Algorithm explanation
- Example output

---

## 📝 Reflection Questions

1. **What was the most challenging part of this project?**

2. **How would you optimize the anagram checker for very long strings?**

3. **What other real-world applications could use similar string comparison logic?**

4. **How confident are you now with string and array manipulation? (1-10)**

---

## 🚀 Next Step

Move to **Day-3-Cheat-Sheet.md** for a quick reference of today's concepts!

**Project complete:** ✅
**Ready for cheat sheet:** Yes / Want to enhance project more

---
