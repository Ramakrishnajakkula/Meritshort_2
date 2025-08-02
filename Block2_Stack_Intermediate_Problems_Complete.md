# 🔹 Block 2: Stack – Intermediate Problems & Advanced Applications

## 2.0 Block Overview and Learning Objectives

### 2.0.1 Introduction to Block 2
Block 2 focuses on intermediate to advanced stack applications, building upon the fundamental stack concepts from Block 1. This comprehensive module covers practical problem-solving using stack data structures, including expression evaluation, bracket matching, and monotonic stack algorithms.

### 2.0.2 Learning Objectives
By the end of Block 2, students will be able to:
- Solve complex bracket matching problems with multiple bracket types
- Implement and analyze monotonic stack algorithms for array problems
- Design stack-based solutions for expression evaluation and conversion
- Apply stack concepts to solve real-world programming challenges
- Optimize stack-based algorithms for time and space complexity
- Understand the relationship between recursion and stack data structures

### 2.0.3 Prerequisites
- Basic understanding of stack operations (push, pop, top)
- Knowledge of array and string manipulation
- Understanding of time and space complexity analysis
- Familiarity with basic programming constructs and recursion

### 2.0.4 Block Structure Overview
```
Block 2 Timeline:
Day 1: Valid Parentheses & Bracket Matching
Day 2: Next Greater Element Problems
Day 3: Previous Greater Element Analysis
Day 4: Stock Span Problem Applications
Day 5: Min/Max Stack Design Patterns
Day 6: Balanced Brackets with Multiple Types
Day 7: Comprehensive Revision & Practice
```

---

## 2.1 Valid Parentheses and Bracket Matching

### 2.1.1 Problem Analysis and Theoretical Foundation

#### 2.1.1.1 Problem Statement
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid. An input string is valid if:
1. Open brackets must be closed by the same type of brackets
2. Open brackets must be closed in the correct order
3. Every close bracket has a corresponding open bracket of the same type

#### 2.1.1.2 Mathematical Foundation
The bracket matching problem can be modeled as a **Context-Free Grammar (CFG)** problem:
```
S → SS | (S) | [S] | {S} | ε
```
Where S represents a valid bracket sequence and ε represents an empty string.

#### 2.1.1.3 Real-world Applications
- **Compiler Design**: Syntax checking in programming languages
- **Mathematical Expression Parsing**: Validating mathematical formulas
- **HTML/XML Validation**: Ensuring proper tag nesting
- **Code Editors**: Real-time syntax highlighting and error detection
- **Configuration File Parsing**: JSON, YAML, and other structured formats

### 2.1.2 Sample Test Cases and Analysis

#### 2.1.2.1 Basic Test Cases
```
Test Case 1: Valid Nested Brackets
Input: "()"
Output: true
Explanation: Single pair of matching parentheses

Test Case 2: Multiple Valid Pairs
Input: "()[]{}"
Output: true
Explanation: Three different types of brackets, all properly closed

Test Case 3: Valid Nested Structure
Input: "([{}])"
Output: true
Explanation: Proper nesting order maintained

Test Case 4: Invalid Closing Order
Input: "([)]"
Output: false
Explanation: Square bracket closes before parenthesis
```

#### 2.1.2.2 Edge Cases
```
Test Case 5: Empty String
Input: ""
Output: true
Explanation: Empty string is considered valid

Test Case 6: Single Opening Bracket
Input: "("
Output: false
Explanation: No corresponding closing bracket

Test Case 7: Single Closing Bracket
Input: ")"
Output: false
Explanation: No corresponding opening bracket

Test Case 8: Complex Nested Structure
Input: "{[()()]}"
Output: true
Explanation: Multiple levels of nesting with proper order
```

### 2.1.3 Brute Force Approach Analysis

#### 2.1.3.1 Naive Character Counting Method

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;

class ValidParenthesesBruteForce {
private:
    // 2.1.3.1.1 Helper method to check if character is opening bracket
    bool isOpeningBracket(char c) {
        return c == '(' || c == '[' || c == '{';
    }
    
    // 2.1.3.1.2 Helper method to check if character is closing bracket
    bool isClosingBracket(char c) {
        return c == ')' || c == ']' || c == '}';
    }
    
    // 2.1.3.1.3 Helper method to get matching bracket
    char getMatchingBracket(char c) {
        switch(c) {
            case ')': return '(';
            case ']': return '[';
            case '}': return '{';
            default: return '\0';
        }
    }
    
public:
    // 2.1.3.1.4 Brute Force Validation - O(n²) approach
    bool isValidBruteForce(string s) {
        cout << "=== Brute Force Approach ===" << endl;
        cout << "Input string: \"" << s << "\"" << endl;
        
        if (s.empty()) {
            cout << "Empty string - Valid" << endl;
            return true;
        }
        
        // Keep removing valid pairs until no more can be removed
        bool changed = true;
        string current = s;
        int iteration = 0;
        
        while (changed && !current.empty()) {
            changed = false;
            iteration++;
            cout << "Iteration " << iteration << ": \"" << current << "\"" << endl;
            
            string next = "";
            
            for (int i = 0; i < current.length(); i++) {
                if (i < current.length() - 1) {
                    char curr = current[i];
                    char next_char = current[i + 1];
                    
                    // Check for valid pairs
                    if ((curr == '(' && next_char == ')') ||
                        (curr == '[' && next_char == ']') ||
                        (curr == '{' && next_char == '}')) {
                        
                        cout << "Found pair: " << curr << next_char << " at position " << i << endl;
                        changed = true;
                        i++; // Skip the next character as well
                        continue;
                    }
                }
                next += current[i];
            }
            
            current = next;
            cout << "After removal: \"" << current << "\"" << endl;
        }
        
        bool result = current.empty();
        cout << "Final result: " << (result ? "Valid" : "Invalid") << endl;
        return result;
    }
    
    // 2.1.3.1.5 Advanced Brute Force with Position Tracking
    bool isValidAdvancedBruteForce(string s) {
        cout << "\n=== Advanced Brute Force with Position Tracking ===" << endl;
        cout << "Input string: \"" << s << "\"" << endl;
        
        if (s.empty()) return true;
        
        // Create array to mark removed characters
        vector<bool> removed(s.length(), false);
        bool found = true;
        int iteration = 0;
        
        while (found) {
            found = false;
            iteration++;
            cout << "Iteration " << iteration << ":" << endl;
            
            // Display current state
            cout << "Current state: ";
            for (int i = 0; i < s.length(); i++) {
                if (!removed[i]) {
                    cout << s[i];
                } else {
                    cout << "_";
                }
            }
            cout << endl;
            
            // Find adjacent valid pairs
            for (int i = 0; i < s.length() - 1; i++) {
                if (removed[i]) continue;
                
                // Find next non-removed character
                int j = i + 1;
                while (j < s.length() && removed[j]) j++;
                
                if (j < s.length()) {
                    if ((s[i] == '(' && s[j] == ')') ||
                        (s[i] == '[' && s[j] == ']') ||
                        (s[i] == '{' && s[j] == '}')) {
                        
                        cout << "Removing pair: " << s[i] << s[j] << " at positions " << i << "," << j << endl;
                        removed[i] = removed[j] = true;
                        found = true;
                        break; // Process one pair per iteration for clarity
                    }
                }
            }
        }
        
        // Check if all characters are removed
        bool allRemoved = true;
        for (bool r : removed) {
            if (!r) {
                allRemoved = false;
                break;
            }
        }
        
        cout << "Final result: " << (allRemoved ? "Valid" : "Invalid") << endl;
        return allRemoved;
    }
    
    // 2.1.3.1.6 Display Analysis
    void analyzeComplexity() {
        cout << "\n=== Brute Force Complexity Analysis ===" << endl;
        cout << "Time Complexity: O(n²) - In worst case, we need n/2 iterations, each scanning n characters" << endl;
        cout << "Space Complexity: O(n) - For storing intermediate strings or boolean arrays" << endl;
        cout << "Drawbacks:" << endl;
        cout << "1. Multiple passes through the string" << endl;
        cout << "2. String manipulation overhead" << endl;
        cout << "3. Not efficient for large inputs" << endl;
        cout << "4. Doesn't handle nested structures efficiently" << endl;
    }
};
```

### 2.1.4 Optimal Stack-Based Approach

#### 2.1.4.1 Algorithm Design and Implementation

```cpp
#include <iostream>
#include <string>
#include <stack>
#include <unordered_map>
#include <chrono>
using namespace std;

class ValidParenthesesOptimal {
private:
    // 2.1.4.1.1 Bracket mapping for quick lookup
    unordered_map<char, char> bracketMap = {
        {')', '('},
        {']', '['},
        {'}', '{'}
    };
    
    // 2.1.4.1.2 Helper method to check opening brackets
    bool isOpeningBracket(char c) {
        return c == '(' || c == '[' || c == '{';
    }
    
    // 2.1.4.1.3 Helper method to check closing brackets
    bool isClosingBracket(char c) {
        return c == ')' || c == ']' || c == '}';
    }
    
public:
    // 2.1.4.1.4 Optimal Stack-based Solution - O(n)
    bool isValid(string s) {
        cout << "=== Optimal Stack-based Approach ===" << endl;
        cout << "Input string: \"" << s << "\"" << endl;
        
        stack<char> bracketStack;
        
        for (int i = 0; i < s.length(); i++) {
            char current = s[i];
            cout << "Processing character '" << current << "' at position " << i << endl;
            
            if (isOpeningBracket(current)) {
                // Push opening bracket onto stack
                bracketStack.push(current);
                cout << "Pushed opening bracket '" << current << "' onto stack" << endl;
                displayStackState(bracketStack);
            }
            else if (isClosingBracket(current)) {
                // Check for matching opening bracket
                if (bracketStack.empty()) {
                    cout << "Error: Closing bracket '" << current << "' with no matching opening bracket" << endl;
                    return false;
                }
                
                char topBracket = bracketStack.top();
                char expectedOpening = bracketMap[current];
                
                if (topBracket == expectedOpening) {
                    bracketStack.pop();
                    cout << "Found matching pair: '" << topBracket << current << "'" << endl;
                    displayStackState(bracketStack);
                } else {
                    cout << "Error: Mismatched brackets - expected '" << expectedOpening 
                         << "' but found '" << topBracket << "'" << endl;
                    return false;
                }
            }
            // Ignore non-bracket characters (if any)
        }
        
        bool result = bracketStack.empty();
        cout << "Final stack state: " << (result ? "Empty (Valid)" : "Not Empty (Invalid)") << endl;
        
        if (!result) {
            cout << "Remaining unmatched opening brackets: ";
            displayStackState(bracketStack);
        }
        
        return result;
    }
    
    // 2.1.4.1.5 Enhanced version with detailed tracking
    bool isValidWithTracking(string s) {
        cout << "\n=== Enhanced Stack Approach with Position Tracking ===" << endl;
        cout << "Input string: \"" << s << "\"" << endl;
        
        stack<pair<char, int>> bracketStack; // Store character and position
        vector<pair<int, int>> matchedPairs; // Store matched bracket pairs
        
        for (int i = 0; i < s.length(); i++) {
            char current = s[i];
            
            if (isOpeningBracket(current)) {
                bracketStack.push({current, i});
                cout << "Position " << i << ": Pushed '" << current << "'" << endl;
            }
            else if (isClosingBracket(current)) {
                if (bracketStack.empty()) {
                    cout << "Position " << i << ": Error - Closing '" << current 
                         << "' with no opening bracket" << endl;
                    return false;
                }
                
                auto [topChar, topPos] = bracketStack.top();
                char expectedOpening = bracketMap[current];
                
                if (topChar == expectedOpening) {
                    bracketStack.pop();
                    matchedPairs.push_back({topPos, i});
                    cout << "Position " << i << ": Matched '" << topChar 
                         << "' (pos " << topPos << ") with '" << current << "'" << endl;
                } else {
                    cout << "Position " << i << ": Error - Expected '" << expectedOpening 
                         << "' but found '" << topChar << "' at position " << topPos << endl;
                    return false;
                }
            }
        }
        
        // Display results
        if (bracketStack.empty()) {
            cout << "\nResult: VALID" << endl;
            cout << "Matched pairs:" << endl;
            for (auto [start, end] : matchedPairs) {
                cout << "  " << s[start] << s[end] << " (positions " << start << "-" << end << ")" << endl;
            }
            return true;
        } else {
            cout << "\nResult: INVALID" << endl;
            cout << "Unmatched opening brackets:" << endl;
            while (!bracketStack.empty()) {
                auto [ch, pos] = bracketStack.top();
                bracketStack.pop();
                cout << "  '" << ch << "' at position " << pos << endl;
            }
            return false;
        }
    }
    
    // 2.1.4.1.6 Performance Analysis Method
    void performanceAnalysis(const vector<string>& testCases) {
        cout << "\n=== Performance Analysis ===" << endl;
        
        for (const string& testCase : testCases) {
            auto start = chrono::high_resolution_clock::now();
            
            bool result = isValid(testCase);
            
            auto end = chrono::high_resolution_clock::now();
            auto duration = chrono::duration_cast<chrono::nanoseconds>(end - start);
            
            cout << "Test: \"" << testCase << "\" - " << (result ? "Valid" : "Invalid") 
                 << " (Time: " << duration.count() << " ns)" << endl;
        }
    }
    
private:
    // 2.1.4.1.7 Helper method to display stack state
    void displayStackState(const stack<char>& s) {
        if (s.empty()) {
            cout << "Stack: [] (Empty)" << endl;
            return;
        }
        
        stack<char> temp = s;
        vector<char> elements;
        
        while (!temp.empty()) {
            elements.push_back(temp.top());
            temp.pop();
        }
        
        cout << "Stack: [";
        for (int i = elements.size() - 1; i >= 0; i--) {
            cout << "'" << elements[i] << "'";
            if (i > 0) cout << ", ";
        }
        cout << "] (Top: '" << elements[0] << "')" << endl;
    }
};

// 2.1.4.2 Comprehensive Testing Suite
void testValidParentheses() {
    cout << "=== Valid Parentheses Comprehensive Testing ===" << endl;
    
    ValidParenthesesBruteForce bruteForce;
    ValidParenthesesOptimal optimal;
    
    vector<string> testCases = {
        "()",           // Simple valid
        "()[]{}", 
        "([{}])",      // Nested valid
        "([)]",        // Invalid order
        "",            // Empty string
        "(",           // Single opening
        ")",           // Single closing
        "{[()()]}",    // Complex valid
        "(((",         // Multiple opening
        ")))",         // Multiple closing
        "({[]})",      // All types nested
        "({[}])"       // Invalid crossing
    };
    
    cout << "\n--- Comparing Brute Force vs Optimal ---" << endl;
    
    for (const string& test : testCases) {
        cout << "\n" << string(50, '=') << endl;
        cout << "Testing: \"" << test << "\"" << endl;
        cout << string(50, '=') << endl;
        
        // Test brute force
        bool bruteResult = bruteForce.isValidBruteForce(test);
        
        // Test optimal
        bool optimalResult = optimal.isValid(test);
        
        // Verify consistency
        if (bruteResult == optimalResult) {
            cout << "✓ Both approaches agree: " << (bruteResult ? "VALID" : "INVALID") << endl;
        } else {
            cout << "✗ Approaches disagree! Brute: " << bruteResult << ", Optimal: " << optimalResult << endl;
        }
    }
    
    // Performance comparison
    cout << "\n--- Performance Analysis ---" << endl;
    optimal.performanceAnalysis(testCases);
    
    bruteForce.analyzeComplexity();
}
```

#### 2.1.4.2 Complexity Analysis of Optimal Approach

**Time Complexity Analysis:**
- Single pass through the string: O(n)
- Each character is pushed and popped at most once: O(1) per operation
- Hash map lookup for bracket matching: O(1)
- **Total: O(n)**

**Space Complexity Analysis:**
- Stack storage in worst case (all opening brackets): O(n)
- Hash map for bracket mapping: O(1) constant space
- **Total: O(n)**

**Optimality Proof:**
- We must examine each character at least once: Ω(n)
- Our algorithm examines each character exactly once: O(n)
- Therefore, the algorithm is optimal: Θ(n)

---

## 2.2 Next Greater Element Problems

### 2.2.1 Problem Analysis and Theoretical Foundation

#### 2.2.1.1 Problem Statement
Given an array of integers, find the next greater element for each element. The next greater element for an element x is the first greater element on the right side of x in the array. Elements for which no greater element exists are marked as -1.

#### 2.2.1.2 Mathematical Foundation
The Next Greater Element problem is a classic application of **monotonic stack** data structures. A monotonic stack maintains elements in a specific order (increasing or decreasing) to efficiently solve range query problems.

**Properties of Monotonic Stack for NGE:**
- Stack maintains elements in decreasing order from bottom to top
- When a larger element is found, it serves as NGE for elements in stack
- Time complexity reduces from O(n²) to O(n) using this approach

#### 2.2.1.3 Variants and Applications
1. **Next Greater Element I**: Finding NGE in the same array
2. **Next Greater Element II**: Circular array version
3. **Next Smaller Element**: Finding next smaller instead of greater
4. **Stock Span Problem**: Days until stock price exceeds current
5. **Daily Temperatures**: Days until warmer temperature

### 2.2.2 Sample Test Cases and Analysis

#### 2.2.2.1 Basic Test Cases

```
Test Case 1: Simple Ascending
Input: [1, 3, 5, 7]
Output: [3, 5, 7, -1]
Explanation: Each element has a greater element to its right except the last

Test Case 2: Simple Descending
Input: [7, 5, 3, 1]
Output: [-1, -1, -1, -1]
Explanation: No element has a greater element to its right

Test Case 3: Mixed Pattern
Input: [4, 5, 2, 25, 7, 8]
Output: [5, 25, 25, -1, 8, -1]
Explanation: Complex pattern requiring careful tracking

Test Case 4: All Equal Elements
Input: [3, 3, 3, 3]
Output: [-1, -1, -1, -1]
Explanation: No strictly greater elements exist
```

#### 2.2.2.2 Edge Cases

```
Test Case 5: Single Element
Input: [5]
Output: [-1]
Explanation: No other elements to compare

Test Case 6: Two Elements Ascending
Input: [1, 2]
Output: [2, -1]

Test Case 7: Two Elements Descending
Input: [2, 1]
Output: [-1, -1]

Test Case 8: Peak in Middle
Input: [1, 5, 3, 4, 2]
Output: [5, -1, 4, -1, -1]
```

### 2.2.3 Brute Force Approach

#### 2.2.3.1 Nested Loop Implementation

```cpp
#include <iostream>
#include <vector>
#include <chrono>
#include <algorithm>
using namespace std;

class NextGreaterElementBruteForce {
public:
    // 2.2.3.1.1 Basic Brute Force - O(n²)
    vector<int> nextGreaterElementsBruteForce(vector<int>& nums) {
        cout << "=== Brute Force Approach ===" << endl;
        cout << "Input array: [";
        for (int i = 0; i < nums.size(); i++) {
            cout << nums[i];
            if (i < nums.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        
        // For each element, search for next greater element
        for (int i = 0; i < n; i++) {
            cout << "\nProcessing element " << nums[i] << " at index " << i << endl;
            
            // Search in the right side
            for (int j = i + 1; j < n; j++) {
                cout << "  Comparing with " << nums[j] << " at index " << j;
                
                if (nums[j] > nums[i]) {
                    result[i] = nums[j];
                    cout << " -> Found! NGE = " << nums[j] << endl;
                    break;
                } else {
                    cout << " -> Not greater" << endl;
                }
            }
            
            if (result[i] == -1) {
                cout << "  No greater element found for " << nums[i] << endl;
            }
        }
        
        cout << "\nResult: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
    // 2.2.3.1.2 Enhanced Brute Force with Statistics
    vector<int> nextGreaterElementsWithStats(vector<int>& nums) {
        cout << "\n=== Enhanced Brute Force with Statistics ===" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        int totalComparisons = 0;
        int maxComparisons = 0;
        
        for (int i = 0; i < n; i++) {
            int comparisons = 0;
            
            for (int j = i + 1; j < n; j++) {
                comparisons++;
                totalComparisons++;
                
                if (nums[j] > nums[i]) {
                    result[i] = nums[j];
                    break;
                }
            }
            
            maxComparisons = max(maxComparisons, comparisons);
            cout << "Element " << nums[i] << ": " << comparisons << " comparisons, NGE = " << result[i] << endl;
        }
        
        cout << "\nStatistics:" << endl;
        cout << "Total comparisons: " << totalComparisons << endl;
        cout << "Average comparisons per element: " << (double)totalComparisons / n << endl;
        cout << "Maximum comparisons for any element: " << maxComparisons << endl;
        cout << "Theoretical worst case: " << (n * (n - 1)) / 2 << " comparisons" << endl;
        
        return result;
    }
    
    // 2.2.3.1.3 Complexity Analysis
    void analyzeComplexity(int n) {
        cout << "\n=== Brute Force Complexity Analysis ===" << endl;
        cout << "Input size: " << n << endl;
        cout << "Time Complexity: O(n²)" << endl;
        cout << "  - Outer loop: " << n << " iterations" << endl;
        cout << "  - Inner loop: Up to " << (n-1) << " iterations each" << endl;
        cout << "  - Total operations: Up to " << (n * (n-1)) / 2 << " comparisons" << endl;
        cout << "Space Complexity: O(1) additional space (excluding result array)" << endl;
        cout << "\nScaling Analysis:" << endl;
        cout << "  n = 100: ~5,000 operations" << endl;
        cout << "  n = 1,000: ~500,000 operations" << endl;
        cout << "  n = 10,000: ~50,000,000 operations" << endl;
    }
};
```

### 2.2.4 Optimal Monotonic Stack Approach

#### 2.2.4.1 Advanced Stack-Based Implementation

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <chrono>
using namespace std;

class NextGreaterElementOptimal {
public:
    // 2.2.4.1.1 Optimal Stack-based Solution - O(n)
    vector<int> nextGreaterElements(vector<int>& nums) {
        cout << "=== Optimal Monotonic Stack Approach ===" << endl;
        cout << "Input array: [";
        for (int i = 0; i < nums.size(); i++) {
            cout << nums[i];
            if (i < nums.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        stack<int> monoStack; // Stores indices, maintains decreasing order
        
        cout << "\nProcessing elements from right to left:" << endl;
        
        // Process elements from right to left
        for (int i = n - 1; i >= 0; i--) {
            cout << "\nStep " << (n - i) << ": Processing element " << nums[i] << " at index " << i << endl;
            
            // Remove elements smaller than or equal to current element
            int removedCount = 0;
            while (!monoStack.empty() && nums[monoStack.top()] <= nums[i]) {
                int removedIdx = monoStack.top();
                monoStack.pop();
                removedCount++;
                cout << "  Removed " << nums[removedIdx] << " (index " << removedIdx << ") from stack" << endl;
            }
            
            if (removedCount == 0) {
                cout << "  No elements removed from stack" << endl;
            }
            
            // The top of stack (if exists) is the next greater element
            if (!monoStack.empty()) {
                result[i] = nums[monoStack.top()];
                cout << "  NGE for " << nums[i] << " is " << result[i] << " (index " << monoStack.top() << ")" << endl;
            } else {
                cout << "  No NGE found for " << nums[i] << " (stack empty)" << endl;
            }
            
            // Push current element index to stack
            monoStack.push(i);
            cout << "  Pushed " << nums[i] << " (index " << i << ") to stack" << endl;
            
            // Display current stack state
            displayStackState(monoStack, nums);
        }
        
        cout << "\nFinal Result: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
    // 2.2.4.1.2 Left-to-Right Alternative Implementation
    vector<int> nextGreaterElementsLeftToRight(vector<int>& nums) {
        cout << "\n=== Alternative: Left-to-Right Processing ===" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        stack<int> monoStack; // Stores indices of elements waiting for NGE
        
        cout << "Processing elements from left to right:" << endl;
        
        for (int i = 0; i < n; i++) {
            cout << "\nStep " << (i + 1) << ": Processing element " << nums[i] << " at index " << i << endl;
            
            // Current element is NGE for elements in stack
            while (!monoStack.empty() && nums[monoStack.top()] < nums[i]) {
                int idx = monoStack.top();
                monoStack.pop();
                result[idx] = nums[i];
                cout << "  " << nums[i] << " is NGE for " << nums[idx] << " (index " << idx << ")" << endl;
            }
            
            // Push current index to stack
            monoStack.push(i);
            cout << "  Pushed index " << i << " (value " << nums[i] << ") to stack" << endl;
            
            displayStackState(monoStack, nums);
        }
        
        // Remaining elements in stack have no NGE
        cout << "\nElements with no NGE:" << endl;
        while (!monoStack.empty()) {
            int idx = monoStack.top();
            monoStack.pop();
            cout << "  " << nums[idx] << " at index " << idx << endl;
        }
        
        return result;
    }
    
    // 2.2.4.1.3 Circular Array Version (NGE II)
    vector<int> nextGreaterElementsCircular(vector<int>& nums) {
        cout << "\n=== Circular Array Version (NGE II) ===" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        stack<int> monoStack;
        
        // Process array twice to handle circular nature
        for (int i = 2 * n - 1; i >= 0; i--) {
            int actualIndex = i % n;
            int currentValue = nums[actualIndex];
            
            if (i < n) {
                cout << "Second pass - Index " << actualIndex << ", Value " << currentValue << endl;
            }
            
            // Remove smaller or equal elements
            while (!monoStack.empty() && nums[monoStack.top()] <= currentValue) {
                monoStack.pop();
            }
            
            // Record result only in second pass
            if (i < n) {
                if (!monoStack.empty()) {
                    result[actualIndex] = nums[monoStack.top()];
                    cout << "  NGE for " << currentValue << " is " << result[actualIndex] << endl;
                } else {
                    cout << "  No NGE found for " << currentValue << endl;
                }
            }
            
            monoStack.push(actualIndex);
        }
        
        return result;
    }
    
    // 2.2.4.1.4 Performance Comparison
    void performanceComparison(vector<int>& nums) {
        cout << "\n=== Performance Comparison ===" << endl;
        
        // Test Right-to-Left approach
        auto start = chrono::high_resolution_clock::now();
        vector<int> result1 = nextGreaterElements(nums);
        auto end = chrono::high_resolution_clock::now();
        auto duration1 = chrono::duration_cast<chrono::microseconds>(end - start);
        
        // Test Left-to-Right approach
        start = chrono::high_resolution_clock::now();
        vector<int> result2 = nextGreaterElementsLeftToRight(nums);
        end = chrono::high_resolution_clock::now();
        auto duration2 = chrono::duration_cast<chrono::microseconds>(end - start);
        
        cout << "Right-to-Left approach: " << duration1.count() << " microseconds" << endl;
        cout << "Left-to-Right approach: " << duration2.count() << " microseconds" << endl;
        
        // Verify results are identical
        bool identical = (result1 == result2);
        cout << "Results identical: " << (identical ? "Yes" : "No") << endl;
        
        if (!identical) {
            cout << "Result 1: [";
            for (int i = 0; i < result1.size(); i++) {
                cout << result1[i];
                if (i < result1.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            cout << "Result 2: [";
            for (int i = 0; i < result2.size(); i++) {
                cout << result2[i];
                if (i < result2.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
        }
    }
    
private:
    // 2.2.4.1.5 Helper method to display stack state
    void displayStackState(const stack<int>& s, const vector<int>& nums) {
        if (s.empty()) {
            cout << "  Stack: [] (Empty)" << endl;
            return;
        }
        
        stack<int> temp = s;
        vector<int> elements;
        
        while (!temp.empty()) {
            elements.push_back(temp.top());
            temp.pop();
        }
        
        cout << "  Stack (top to bottom): [";
        for (int i = 0; i < elements.size(); i++) {
            cout << nums[elements[i]] << "(" << elements[i] << ")";
            if (i < elements.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
    }
};

// 2.2.4.2 Comprehensive Testing Suite
void testNextGreaterElement() {
    cout << "=== Next Greater Element Comprehensive Testing ===" << endl;
    
    NextGreaterElementBruteForce bruteForce;
    NextGreaterElementOptimal optimal;
    
    vector<vector<int>> testCases = {
        {4, 5, 2, 25, 7, 8},     // Mixed pattern
        {1, 3, 5, 7},            // Ascending
        {7, 5, 3, 1},            // Descending
        {3, 3, 3, 3},            // All equal
        {5},                     // Single element
        {1, 2},                  // Two ascending
        {2, 1},                  // Two descending
        {1, 5, 3, 4, 2},         // Peak in middle
        {13, 7, 6, 12},          // Complex case
        {2, 1, 3}                // Simple case
    };
    
    for (int i = 0; i < testCases.size(); i++) {
        cout << "\n" << string(60, '=') << endl;
        cout << "Test Case " << (i + 1) << endl;
        cout << string(60, '=') << endl;
        
        vector<int> nums = testCases[i];
        
        // Test brute force
        cout << "\n--- Brute Force Approach ---" << endl;
        vector<int> bruteResult = bruteForce.nextGreaterElementsBruteForce(nums);
        
        // Test optimal
        cout << "\n--- Optimal Approach ---" << endl;
        vector<int> optimalResult = optimal.nextGreaterElements(nums);
        
        // Verify consistency
        if (bruteResult == optimalResult) {
            cout << "\n✓ Both approaches produce identical results" << endl;
        } else {
            cout << "\n✗ Results differ!" << endl;
            cout << "Brute Force: [";
            for (int j = 0; j < bruteResult.size(); j++) {
                cout << bruteResult[j];
                if (j < bruteResult.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            cout << "Optimal: [";
            for (int j = 0; j < optimalResult.size(); j++) {
                cout << optimalResult[j];
                if (j < optimalResult.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
        }
        
        // Performance comparison for larger arrays
        if (nums.size() >= 5) {
            optimal.performanceComparison(nums);
        }
    }
    
    // Complexity analysis
    cout << "\n" << string(60, '=') << endl;
    cout << "Complexity Analysis" << endl;
    cout << string(60, '=') << endl;
    
    bruteForce.analyzeComplexity(1000);
    
    cout << "\nOptimal Approach Complexity:" << endl;
    cout << "Time Complexity: O(n)" << endl;
    cout << "  - Each element is pushed and popped at most once" << endl;
    cout << "  - Total stack operations: 2n (n pushes + n pops)" << endl;
    cout << "Space Complexity: O(n) for the stack in worst case" << endl;
    cout << "  - Worst case: Decreasing array, all elements in stack" << endl;
    cout << "  - Best case: Ascending array, stack never has more than 1 element" << endl;
}
```

#### 2.2.4.2 Complexity Analysis and Optimality

**Time Complexity Analysis:**
- Each element is pushed onto the stack exactly once: n pushes
- Each element is popped from the stack at most once: ≤ n pops
- Total operations: ≤ 2n = O(n)

**Space Complexity Analysis:**
- Stack space in worst case (decreasing array): O(n)
- Result array: O(n)
- Total: O(n)

**Amortized Analysis:**
- Although inner while loop may run multiple times, each element contributes to at most one pop operation across the entire algorithm
- This gives us O(1) amortized time per element
- Total amortized time: O(n)

---

## 2.3 Previous Greater Element Analysis

### 2.3.1 Problem Statement and Variations

#### 2.3.1.1 Core Problem Definition
Given an array of integers, find the previous greater element for each element. The previous greater element for an element x is the first greater element on the left side of x in the array. Elements for which no greater element exists are marked as -1.

#### 2.3.1.2 Relationship to Next Greater Element
The Previous Greater Element (PGE) problem is the symmetric counterpart to the Next Greater Element problem. While NGE looks forward in the array, PGE looks backward, but both use similar monotonic stack principles.

#### 2.3.1.3 Applications and Use Cases
- **Stock Market Analysis**: Finding previous peak prices
- **Histogram Problems**: Calculating areas of rectangles
- **Range Queries**: Building segment trees and sparse tables
- **Dynamic Programming**: Optimizing subarray problems
- **Sliding Window Maximum**: Maintaining window extremes

### 2.3.2 Sample Test Cases and Edge Cases

#### 2.3.2.1 Comprehensive Test Cases

```cpp
// 2.3.2.1.1 Test Case Documentation
/*
Test Case 1: Basic Mixed Array
Input: [4, 5, 2, 25, 7, 8]
Output: [-1, -1, 5, -1, 25, 25]
Explanation:
- 4: No previous element greater than 4 → -1
- 5: No previous element greater than 5 → -1  
- 2: Previous greater element is 5 → 5
- 25: No previous element greater than 25 → -1
- 7: Previous greater element is 25 → 25
- 8: Previous greater element is 25 → 25

Test Case 2: Ascending Array
Input: [1, 3, 5, 7, 9]
Output: [-1, -1, -1, -1, -1]
Explanation: Each element is greater than all previous elements

Test Case 3: Descending Array  
Input: [9, 7, 5, 3, 1]
Output: [-1, 9, 7, 5, 3]
Explanation: Each element has the immediate previous as greater

Test Case 4: Complex Pattern
Input: [6, 8, 0, 1, 3, 7, 5, 2]
Output: [-1, -1, 8, 8, 8, 8, 7, 5]
Explanation: Demonstrates multiple level lookbacks
*/
```

### 2.3.3 Brute Force Implementation

#### 2.3.3.1 Nested Loop Approach

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class PreviousGreaterElementBruteForce {
public:
    // 2.3.3.1.1 Basic Brute Force - O(n²)
    vector<int> previousGreaterElementsBruteForce(vector<int>& nums) {
        cout << "=== Previous Greater Element - Brute Force ===" << endl;
        cout << "Input array: [";
        for (int i = 0; i < nums.size(); i++) {
            cout << nums[i];
            if (i < nums.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        
        // For each element, search backwards for greater element
        for (int i = 0; i < n; i++) {
            cout << "\nProcessing element " << nums[i] << " at index " << i << endl;
            
            // Search backwards
            for (int j = i - 1; j >= 0; j--) {
                cout << "  Checking " << nums[j] << " at index " << j;
                
                if (nums[j] > nums[i]) {
                    result[i] = nums[j];
                    cout << " -> Found! PGE = " << nums[j] << endl;
                    break;
                } else {
                    cout << " -> Not greater" << endl;
                }
            }
            
            if (result[i] == -1) {
                cout << "  No previous greater element found for " << nums[i] << endl;
            }
        }
        
        return result;
    }
    
    // 2.3.3.1.2 Enhanced Version with Distance Tracking
    vector<int> previousGreaterWithDistance(vector<int>& nums) {
        cout << "\n=== Enhanced Brute Force with Distance Tracking ===" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        vector<int> distances(n, -1);
        int totalSearches = 0;
        
        for (int i = 0; i < n; i++) {
            int searches = 0;
            
            for (int j = i - 1; j >= 0; j--) {
                searches++;
                totalSearches++;
                
                if (nums[j] > nums[i]) {
                    result[i] = nums[j];
                    distances[i] = i - j;
                    break;
                }
            }
            
            cout << "Element " << nums[i] << " (index " << i << "): ";
            cout << searches << " searches, ";
            cout << "PGE = " << result[i];
            if (distances[i] != -1) {
                cout << " (distance: " << distances[i] << ")";
            }
            cout << endl;
        }
        
        cout << "\nStatistics:" << endl;
        cout << "Total searches: " << totalSearches << endl;
        cout << "Average searches per element: " << (double)totalSearches / n << endl;
        
        return result;
    }
};
```

### 2.3.4 Optimal Monotonic Stack Approach

#### 2.3.4.1 Left-to-Right Stack Processing

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

class PreviousGreaterElementOptimal {
public:
    // 2.3.4.1.1 Optimal Stack Solution - O(n)
    vector<int> previousGreaterElements(vector<int>& nums) {
        cout << "=== Previous Greater Element - Optimal Stack ===" << endl;
        cout << "Input array: [";
        for (int i = 0; i < nums.size(); i++) {
            cout << nums[i];
            if (i < nums.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        stack<int> monoStack; // Maintains decreasing order (indices)
        
        cout << "\nProcessing elements from left to right:" << endl;
        
        for (int i = 0; i < n; i++) {
            cout << "\nStep " << (i + 1) << ": Processing " << nums[i] << " at index " << i << endl;
            
            // Remove elements <= current element
            int removedCount = 0;
            while (!monoStack.empty() && nums[monoStack.top()] <= nums[i]) {
                int removedIdx = monoStack.top();
                monoStack.pop();
                removedCount++;
                cout << "  Removed " << nums[removedIdx] << " (index " << removedIdx << ") - not useful anymore" << endl;
            }
            
            if (removedCount == 0) {
                cout << "  No elements removed from stack" << endl;
            }
            
            // Top of stack is the previous greater element
            if (!monoStack.empty()) {
                result[i] = nums[monoStack.top()];
                cout << "  PGE for " << nums[i] << " is " << result[i] << " (index " << monoStack.top() << ")" << endl;
            } else {
                cout << "  No PGE found for " << nums[i] << " (stack empty)" << endl;
            }
            
            // Push current element
            monoStack.push(i);
            cout << "  Pushed " << nums[i] << " (index " << i << ") to stack" << endl;
            
            displayStackState(monoStack, nums);
        }
        
        cout << "\nFinal Result: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
    // 2.3.4.1.2 Alternative Implementation with Value Storage
    vector<int> previousGreaterElementsValueStack(vector<int>& nums) {
        cout << "\n=== Alternative: Storing Values Instead of Indices ===" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        stack<int> valueStack; // Stores actual values, not indices
        
        for (int i = 0; i < n; i++) {
            cout << "Processing " << nums[i] << " at index " << i << endl;
            
            // Remove smaller or equal values
            while (!valueStack.empty() && valueStack.top() <= nums[i]) {
                int removed = valueStack.top();
                valueStack.pop();
                cout << "  Removed " << removed << " from stack" << endl;
            }
            
            // Top is the previous greater element
            if (!valueStack.empty()) {
                result[i] = valueStack.top();
                cout << "  PGE = " << valueStack.top() << endl;
            } else {
                cout << "  No PGE found" << endl;
            }
            
            valueStack.push(nums[i]);
            
            // Display stack
            stack<int> temp = valueStack;
            vector<int> stackContents;
            while (!temp.empty()) {
                stackContents.push_back(temp.top());
                temp.pop();
            }
            
            cout << "  Stack: [";
            for (int j = stackContents.size() - 1; j >= 0; j--) {
                cout << stackContents[j];
                if (j > 0) cout << ", ";
            }
            cout << "]" << endl;
        }
        
        return result;
    }
    
    // 2.3.4.1.3 Combined NGE and PGE Calculation
    pair<vector<int>, vector<int>> calculateBothGreaterElements(vector<int>& nums) {
        cout << "\n=== Combined NGE and PGE Calculation ===" << endl;
        
        int n = nums.size();
        vector<int> nge(n, -1);
        vector<int> pge(n, -1);
        
        // Calculate PGE using left-to-right scan
        stack<int> pgeStack;
        for (int i = 0; i < n; i++) {
            while (!pgeStack.empty() && nums[pgeStack.top()] <= nums[i]) {
                pgeStack.pop();
            }
            
            if (!pgeStack.empty()) {
                pge[i] = nums[pgeStack.top()];
            }
            
            pgeStack.push(i);
        }
        
        // Calculate NGE using right-to-left scan
        stack<int> ngeStack;
        for (int i = n - 1; i >= 0; i--) {
            while (!ngeStack.empty() && nums[ngeStack.top()] <= nums[i]) {
                ngeStack.pop();
            }
            
            if (!ngeStack.empty()) {
                nge[i] = nums[ngeStack.top()];
            }
            
            ngeStack.push(i);
        }
        
        cout << "Input:  [";
        for (int i = 0; i < n; i++) {
            cout << nums[i];
            if (i < n - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        cout << "PGE:    [";
        for (int i = 0; i < n; i++) {
            cout << pge[i];
            if (i < n - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        cout << "NGE:    [";
        for (int i = 0; i < n; i++) {
            cout << nge[i];
            if (i < n - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return {nge, pge};
    }
    
    // 2.3.4.1.4 Application: Largest Rectangle in Histogram
    int largestRectangleArea(vector<int>& heights) {
        cout << "\n=== Application: Largest Rectangle in Histogram ===" << endl;
        cout << "Heights: [";
        for (int i = 0; i < heights.size(); i++) {
            cout << heights[i];
            if (i < heights.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = heights.size();
        vector<int> left(n), right(n);
        
        // Find previous smaller element (left boundary)
        stack<int> st;
        for (int i = 0; i < n; i++) {
            while (!st.empty() && heights[st.top()] >= heights[i]) {
                st.pop();
            }
            left[i] = st.empty() ? -1 : st.top();
            st.push(i);
        }
        
        // Clear stack for next calculation
        while (!st.empty()) st.pop();
        
        // Find next smaller element (right boundary)
        for (int i = n - 1; i >= 0; i--) {
            while (!st.empty() && heights[st.top()] >= heights[i]) {
                st.pop();
            }
            right[i] = st.empty() ? n : st.top();
            st.push(i);
        }
        
        // Calculate maximum area
        int maxArea = 0;
        cout << "\nRectangle calculations:" << endl;
        for (int i = 0; i < n; i++) {
            int width = right[i] - left[i] - 1;
            int area = heights[i] * width;
            maxArea = max(maxArea, area);
            
            cout << "Bar " << i << " (height " << heights[i] << "): ";
            cout << "left=" << left[i] << ", right=" << right[i];
            cout << ", width=" << width << ", area=" << area << endl;
        }
        
        cout << "Maximum rectangle area: " << maxArea << endl;
        return maxArea;
    }
    
private:
    // 2.3.4.1.5 Helper method to display stack state
    void displayStackState(const stack<int>& s, const vector<int>& nums) {
        if (s.empty()) {
            cout << "  Stack: [] (Empty)" << endl;
            return;
        }
        
        stack<int> temp = s;
        vector<int> elements;
        
        while (!temp.empty()) {
            elements.push_back(temp.top());
            temp.pop();
        }
        
        cout << "  Stack (top to bottom): [";
        for (int i = 0; i < elements.size(); i++) {
            cout << nums[elements[i]] << "(" << elements[i] << ")";
            if (i < elements.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
    }
};

// 2.3.4.2 Comprehensive Testing
void testPreviousGreaterElement() {
    cout << "=== Previous Greater Element Comprehensive Testing ===" << endl;
    
    PreviousGreaterElementBruteForce bruteForce;
    PreviousGreaterElementOptimal optimal;
    
    vector<vector<int>> testCases = {
        {4, 5, 2, 25, 7, 8},      // Standard mixed case
        {1, 3, 5, 7, 9},          // Ascending array
        {9, 7, 5, 3, 1},          // Descending array
        {6, 8, 0, 1, 3, 7, 5, 2}, // Complex pattern
        {3, 3, 3, 3},             // All equal
        {5},                      // Single element
        {2, 1, 4, 3, 6, 5},       // Alternating pattern
        {2, 1, 5, 6, 2, 3}        // Histogram-like
    };
    
    for (int i = 0; i < testCases.size(); i++) {
        cout << "\n" << string(70, '=') << endl;
        cout << "Test Case " << (i + 1) << endl;
        cout << string(70, '=') << endl;
        
        vector<int> nums = testCases[i];
        
        // Test brute force
        vector<int> bruteResult = bruteForce.previousGreaterElementsBruteForce(nums);
        
        // Test optimal
        vector<int> optimalResult = optimal.previousGreaterElements(nums);
        
        // Verify results match
        bool match = (bruteResult == optimalResult);
        cout << "\n" << (match ? "✓" : "✗") << " Results " << (match ? "match" : "differ") << endl;
        
        if (!match) {
            cout << "Brute: [";
            for (int j = 0; j < bruteResult.size(); j++) {
                cout << bruteResult[j];
                if (j < bruteResult.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            cout << "Optimal: [";
            for (int j = 0; j < optimalResult.size(); j++) {
                cout << optimalResult[j];
                if (j < optimalResult.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
        }
        
        // Test combined calculation
        if (nums.size() <= 8) { // Only for smaller arrays to avoid too much output
            optimal.calculateBothGreaterElements(nums);
        }
        
        // Test histogram application for suitable arrays
        if (i == 7) { // Last test case resembles histogram
            optimal.largestRectangleArea(nums);
        }
    }
}
```

---

## 2.4 Stock Span Problem Applications

### 2.4.1 Problem Definition and Real-world Context

#### 2.4.1.1 Problem Statement
The stock span problem asks: Given daily stock prices, calculate the span of stock's price for each day. The span of the stock's price on a given day is defined as the maximum number of consecutive days (including the current day) for which the stock price was less than or equal to the current day's price.

#### 2.4.1.2 Financial Context and Applications
- **Trading Strategy**: Identify momentum periods and trend reversals
- **Technical Analysis**: Calculate support and resistance levels  
- **Risk Management**: Assess volatility patterns and market behavior
- **Algorithmic Trading**: Develop automated trading signals
- **Market Research**: Analyze historical price patterns and cycles

#### 2.4.1.3 Mathematical Formulation
For price array P = [p₀, p₁, p₂, ..., pₙ₋₁], the span S[i] is:
```
S[i] = max{j | j ≤ i and P[k] ≤ P[i] for all k in [i-j+1, i]}
```

### 2.4.2 Sample Test Cases and Market Scenarios

#### 2.4.2.1 Realistic Stock Price Scenarios

```cpp
// 2.4.2.1.1 Test Case Documentation with Market Context
/*
Test Case 1: Bull Market Rally
Prices: [100, 80, 60, 70, 60, 75, 85]
Spans:  [1,   1,  1,  2,  1,  4,  6]
Analysis:
- Day 0: $100 (span=1, first day)
- Day 1: $80 (span=1, price dropped)  
- Day 2: $60 (span=1, continued decline)
- Day 3: $70 (span=2, higher than previous day)
- Day 4: $60 (span=1, price dropped again)
- Day 5: $75 (span=4, higher than last 3 days)
- Day 6: $85 (span=6, rally continues from day 1)

Test Case 2: Market Crash Scenario
Prices: [50, 45, 40, 35, 30, 25, 20]
Spans:  [1,  1,  1,  1,  1,  1,  1]
Analysis: Continuous decline, each day has span=1

Test Case 3: Volatile Market
Prices: [31, 41, 48, 59, 79, 46, 58, 68, 58]
Spans:  [1,  2,  3,  4,  5,  1,  2,  3,  1]
Analysis: Mixed trends with multiple peaks and valleys
*/
```

### 2.4.3 Brute Force Implementation

#### 2.4.3.1 Naive Approach with Financial Context

```cpp
#include <iostream>
#include <vector>
#include <iomanip>
using namespace std;

class StockSpanBruteForce {
public:
    // 2.4.3.1.1 Basic Brute Force - O(n²)
    vector<int> calculateStockSpans(vector<int>& prices) {
        cout << "=== Stock Span Problem - Brute Force Analysis ===" << endl;
        cout << "Stock Price History