# 🔹 Block 4: Monotonic Stack & Queue – Advanced Algorithmic Patterns

## 4.0 Block Overview and Learning Objectives

### 4.0.1 Introduction to Block 4
Block 4 delves into advanced algorithmic patterns using monotonic stack and queue data structures. These powerful techniques are essential for solving complex array problems, optimization challenges, and range query problems with optimal time complexity.

### 4.0.2 Learning Objectives
By the end of Block 4, students will be able to:
- Understand and implement monotonic stack and queue data structures
- Solve array-based problems using monotonic patterns
- Apply sliding window techniques with deque optimization
- Analyze and optimize histogram-based geometric problems
- Design efficient algorithms for range maximum/minimum queries
- Implement advanced stack-based optimization techniques

### 4.0.3 Prerequisites
- Solid understanding of stack and queue operations from Blocks 1-3
- Knowledge of array manipulation and sliding window techniques
- Understanding of time and space complexity analysis
- Familiarity with geometric problems and histogram concepts

### 4.0.4 Block Structure Overview
```
Block 4 Timeline:
Day 1: Monotonic Stack – Concept and Use Cases
Day 2: Daily Temperatures Problem Analysis
Day 3: Largest Rectangle in Histogram
Day 4: Maximal Rectangle (2D Histogram Extension)
Day 5: Monotonic Queue – Sliding Window Maximum
Day 6: Sum of Subarray Minimums
Day 7: Comprehensive Revision & Advanced Applications
```

---

## 4.1 Monotonic Stack – Concepts and Fundamental Applications

### 4.1.1 Theoretical Foundation and Definition

#### 4.1.1.1 Monotonic Stack Definition
A **Monotonic Stack** is a stack data structure where elements are maintained in a specific monotonic order (either strictly increasing or strictly decreasing). When a new element violates the monotonic property, elements are popped from the stack until the property is restored.

#### 4.1.1.2 Mathematical Properties
For a monotonic increasing stack with elements [a₁, a₂, ..., aₖ]:
- Property: a₁ ≤ a₂ ≤ ... ≤ aₖ (non-decreasing order from bottom to top)
- Invariant: ∀i < j, stack[i] ≤ stack[j]

For a monotonic decreasing stack:
- Property: a₁ ≥ a₂ ≥ ... ≥ aₖ (non-increasing order from bottom to top)
- Invariant: ∀i < j, stack[i] ≥ stack[j]

#### 4.1.1.3 Core Applications and Problem Types
```cpp
// 4.1.1.3.1 Problem Classification
/*
Monotonic Stack Applications:
1. Next/Previous Greater Element Problems
2. Next/Previous Smaller Element Problems
3. Histogram Area Calculations
4. Stock Span Analysis
5. Largest Rectangle Problems
6. Maximal Rectangle in Binary Matrix
7. Sliding Window Extrema
8. Subarray Sum Optimizations
*/
```

#### 4.1.1.4 Algorithmic Patterns and Templates

```cpp
#include <iostream>
#include <vector>
#include <stack>
#include <algorithm>
#include <climits>
using namespace std;

// 4.1.1.4.1 Generic Monotonic Stack Template
template<typename T>
class MonotonicStack {
public:
    enum StackType {
        INCREASING,  // Maintains increasing order (bottom to top)
        DECREASING   // Maintains decreasing order (bottom to top)
    };
    
private:
    stack<pair<T, int>> monoStack;  // {value, index}
    StackType stackType;
    
public:
    // 4.1.1.4.2 Constructor
    explicit MonotonicStack(StackType type) : stackType(type) {
        cout << "Monotonic Stack created - Type: " 
             << (type == INCREASING ? "Increasing" : "Decreasing") << endl;
    }
    
    // 4.1.1.4.3 Core processing method
    vector<int> processElement(const T& value, int index) {
        vector<int> poppedIndices;
        
        cout << "Processing element " << value << " at index " << index << endl;
        
        // Remove elements that violate monotonic property
        while (!monoStack.empty()) {
            T topValue = monoStack.top().first;
            int topIndex = monoStack.top().second;
            
            bool shouldPop = false;
            if (stackType == INCREASING) {
                shouldPop = (topValue > value);  // Remove if current is smaller
            } else {
                shouldPop = (topValue < value);  // Remove if current is larger
            }
            
            if (shouldPop) {
                poppedIndices.push_back(topIndex);
                monoStack.pop();
                cout << "  Popped: " << topValue << " (index " << topIndex << ")" << endl;
            } else {
                break;
            }
        }
        
        // Add current element
        monoStack.push({value, index});
        cout << "  Added: " << value << " (index " << index << ")" << endl;
        
        displayCurrentState();
        return poppedIndices;
    }
    
    // 4.1.1.4.4 Query operations
    bool isEmpty() const {
        return monoStack.empty();
    }
    
    pair<T, int> top() const {
        if (monoStack.empty()) {
            throw runtime_error("Stack is empty");
        }
        return monoStack.top();
    }
    
    int size() const {
        return monoStack.size();
    }
    
    // 4.1.1.4.5 Utility methods
    void clear() {
        while (!monoStack.empty()) {
            monoStack.pop();
        }
        cout << "Monotonic stack cleared" << endl;
    }
    
    void displayCurrentState() const {
        if (monoStack.empty()) {
            cout << "  Stack: [] (Empty)" << endl;
            return;
        }
        
        stack<pair<T, int>> temp = monoStack;
        vector<pair<T, int>> elements;
        
        while (!temp.empty()) {
            elements.push_back(temp.top());
            temp.pop();
        }
        
        cout << "  Stack (top to bottom): [";
        for (int i = 0; i < elements.size(); i++) {
            cout << elements[i].first << "(" << elements[i].second << ")";
            if (i < elements.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
    }
    
    // 4.1.1.4.6 Get all remaining elements
    vector<pair<T, int>> getAllElements() const {
        vector<pair<T, int>> result;
        stack<pair<T, int>> temp = monoStack;
        
        while (!temp.empty()) {
            result.push_back(temp.top());
            temp.pop();
        }
        
        reverse(result.begin(), result.end());
        return result;
    }
};

// 4.1.1.5 Practical Implementation Examples
class MonotonicStackApplications {
public:
    // 4.1.1.5.1 Next Greater Element using Monotonic Stack
    vector<int> nextGreaterElement(vector<int>& nums) {
        cout << "=== Next Greater Element using Monotonic Stack ===" << endl;
        cout << "Input: [";
        for (int i = 0; i < nums.size(); i++) {
            cout << nums[i];
            if (i < nums.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        MonotonicStack<int> monoStack(MonotonicStack<int>::DECREASING);
        
        // Process from right to left for NGE
        for (int i = n - 1; i >= 0; i--) {
            // Remove smaller elements
            monoStack.processElement(nums[i], i);
            
            // Find NGE
            auto elements = monoStack.getAllElements();
            for (auto& elem : elements) {
                if (elem.second != i && elem.first > nums[i]) {
                    result[i] = elem.first;
                    break;
                }
            }
        }
        
        cout << "Result: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
    // 4.1.1.5.2 Previous Smaller Element
    vector<int> previousSmallerElement(vector<int>& nums) {
        cout << "\n=== Previous Smaller Element using Monotonic Stack ===" << endl;
        
        int n = nums.size();
        vector<int> result(n, -1);
        MonotonicStack<int> monoStack(MonotonicStack<int>::INCREASING);
        
        for (int i = 0; i < n; i++) {
            cout << "\nProcessing index " << i << " (value " << nums[i] << ")" << endl;
            
            // Remove elements >= current
            monoStack.processElement(nums[i], i);
            
            // Find PSE (top of stack after processing)
            if (monoStack.size() > 1) {  // More than just current element
                auto elements = monoStack.getAllElements();
                if (elements.size() >= 2) {
                    result[i] = elements[elements.size() - 2].first;  // Second from top
                }
            }
            
            cout << "PSE for " << nums[i] << ": " << result[i] << endl;
        }
        
        return result;
    }
    
    // 4.1.1.5.3 Stock Span Problem
    vector<int> stockSpan(vector<int>& prices) {
        cout << "\n=== Stock Span Problem using Monotonic Stack ===" << endl;
        cout << "Prices: [";
        for (int i = 0; i < prices.size(); i++) {
            cout << prices[i];
            if (i < prices.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = prices.size();
        vector<int> spans(n);
        stack<int> monoStack;  // Stores indices
        
        for (int i = 0; i < n; i++) {
            cout << "\nDay " << i << " - Price: " << prices[i] << endl;
            
            // Remove days with prices <= current price
            while (!monoStack.empty() && prices[monoStack.top()] <= prices[i]) {
                int removedIndex = monoStack.top();
                monoStack.pop();
                cout << "  Removed day " << removedIndex << " (price " << prices[removedIndex] << ")" << endl;
            }
            
            // Calculate span
            spans[i] = monoStack.empty() ? i + 1 : i - monoStack.top();
            cout << "  Span: " << spans[i] << " days" << endl;
            
            monoStack.push(i);
            
            // Display current stack
            if (!monoStack.empty()) {
                stack<int> temp = monoStack;
                vector<int> stackElements;
                while (!temp.empty()) {
                    stackElements.push_back(temp.top());
                    temp.pop();
                }
                
                cout << "  Stack indices (top to bottom): [";
                for (int j = 0; j < stackElements.size(); j++) {
                    cout << stackElements[j];
                    if (j < stackElements.size() - 1) cout << ", ";
                }
                cout << "]" << endl;
            }
        }
        
        cout << "\nStock Spans: [";
        for (int i = 0; i < spans.size(); i++) {
            cout << spans[i];
            if (i < spans.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return spans;
    }
};
```

### 4.1.2 Advanced Monotonic Stack Patterns

#### 4.1.2.1 Bidirectional Monotonic Stack Analysis

```cpp
// 4.1.2.1.1 Finding both NGE and PSE in single pass
class BidirectionalMonotonicAnalysis {
public:
    struct ElementInfo {
        int value;
        int index;
        int nextGreater;
        int prevSmaller;
        int nextSmaller;
        int prevGreater;
        
        ElementInfo(int v, int i) : value(v), index(i), 
                                   nextGreater(-1), prevSmaller(-1),
                                   nextSmaller(-1), prevGreater(-1) {}
    };
    
    // 4.1.2.1.2 Comprehensive analysis method
    vector<ElementInfo> completeAnalysis(vector<int>& nums) {
        cout << "=== Bidirectional Monotonic Stack Analysis ===" << endl;
        
        int n = nums.size();
        vector<ElementInfo> analysis;
        
        for (int i = 0; i < n; i++) {
            analysis.emplace_back(nums[i], i);
        }
        
        // Find Next Greater Elements
        findNextGreaterElements(nums, analysis);
        
        // Find Previous Smaller Elements  
        findPreviousSmallerElements(nums, analysis);
        
        // Find Next Smaller Elements
        findNextSmallerElements(nums, analysis);
        
        // Find Previous Greater Elements
        findPreviousGreaterElements(nums, analysis);
        
        // Display comprehensive results
        displayCompleteAnalysis(analysis);
        
        return analysis;
    }
    
private:
    void findNextGreaterElements(vector<int>& nums, vector<ElementInfo>& analysis) {
        cout << "\n--- Finding Next Greater Elements ---" << endl;
        
        stack<int> monoStack;
        int n = nums.size();
        
        for (int i = n - 1; i >= 0; i--) {
            while (!monoStack.empty() && nums[monoStack.top()] <= nums[i]) {
                monoStack.pop();
            }
            
            if (!monoStack.empty()) {
                analysis[i].nextGreater = monoStack.top();
            }
            
            monoStack.push(i);
        }
    }
    
    void findPreviousSmallerElements(vector<int>& nums, vector<ElementInfo>& analysis) {
        cout << "\n--- Finding Previous Smaller Elements ---" << endl;
        
        stack<int> monoStack;
        int n = nums.size();
        
        for (int i = 0; i < n; i++) {
            while (!monoStack.empty() && nums[monoStack.top()] >= nums[i]) {
                monoStack.pop();
            }
            
            if (!monoStack.empty()) {
                analysis[i].prevSmaller = monoStack.top();
            }
            
            monoStack.push(i);
        }
    }
    
    void findNextSmallerElements(vector<int>& nums, vector<ElementInfo>& analysis) {
        cout << "\n--- Finding Next Smaller Elements ---" << endl;
        
        stack<int> monoStack;
        int n = nums.size();
        
        for (int i = n - 1; i >= 0; i--) {
            while (!monoStack.empty() && nums[monoStack.top()] >= nums[i]) {
                monoStack.pop();
            }
            
            if (!monoStack.empty()) {
                analysis[i].nextSmaller = monoStack.top();
            }
            
            monoStack.push(i);
        }
    }
    
    void findPreviousGreaterElements(vector<int>& nums, vector<ElementInfo>& analysis) {
        cout << "\n--- Finding Previous Greater Elements ---" << endl;
        
        stack<int> monoStack;
        int n = nums.size();
        
        for (int i = 0; i < n; i++) {
            while (!monoStack.empty() && nums[monoStack.top()] <= nums[i]) {
                monoStack.pop();
            }
            
            if (!monoStack.empty()) {
                analysis[i].prevGreater = monoStack.top();
            }
            
            monoStack.push(i);
        }
    }
    
    void displayCompleteAnalysis(const vector<ElementInfo>& analysis) {
        cout << "\n=== Complete Monotonic Analysis Results ===" << endl;
        cout << "Index | Value | NGE | PSE | NSE | PGE" << endl;
        cout << "------|-------|-----|-----|-----|-----" << endl;
        
        for (const auto& info : analysis) {
            cout << setw(5) << info.index << " | "
                 << setw(5) << info.value << " | "
                 << setw(3) << (info.nextGreater == -1 ? -1 : info.nextGreater) << " | "
                 << setw(3) << (info.prevSmaller == -1 ? -1 : info.prevSmaller) << " | "
                 << setw(3) << (info.nextSmaller == -1 ? -1 : info.nextSmaller) << " | "
                 << setw(3) << (info.prevGreater == -1 ? -1 : info.prevGreater) << endl;
        }
    }
};
```

---

## 4.2 Daily Temperatures Problem – Practical Application

### 4.2.1 Problem Definition and Analysis

#### 4.2.1.1 Problem Statement
Given an array of daily temperatures, return an array such that for each day, it tells you how many days you have to wait until a warmer temperature. If there is no future day for which this is possible, put 0 instead.

#### 4.2.1.2 Mathematical Formulation
For temperature array T = [T₀, T₁, T₂, ..., Tₙ₋₁], find array R where:
```
R[i] = min{j - i | j > i and T[j] > T[i]} if such j exists
     = 0 otherwise
```

#### 4.2.1.3 Real-world Context and Applications
- **Weather Forecasting**: Predicting temperature trends and patterns
- **Climate Analysis**: Studying warming/cooling cycles
- **Agricultural Planning**: Crop planting and harvesting decisions
- **Energy Management**: Heating/cooling system optimization
- **Event Planning**: Outdoor activity scheduling

### 4.2.2 Sample Test Cases and Edge Cases

#### 4.2.2.1 Comprehensive Test Cases

```cpp
// 4.2.2.1.1 Test Case Analysis
class DailyTemperaturesTestCases {
public:
    void displayTestCases() {
        cout << "=== Daily Temperatures Test Cases ===" << endl;
        
        cout << "\nTest Case 1: Standard Weather Pattern" << endl;
        cout << "Input:  [73, 74, 75, 71, 69, 72, 76, 73]" << endl;
        cout << "Output: [1,  1,  4,  2,  1,  1,  0,  0]" << endl;
        cout << "Analysis:" << endl;
        cout << "- Day 0 (73°): Next warmer is day 1 (74°) → wait 1 day" << endl;
        cout << "- Day 1 (74°): Next warmer is day 2 (75°) → wait 1 day" << endl;
        cout << "- Day 2 (75°): Next warmer is day 6 (76°) → wait 4 days" << endl;
        cout << "- Day 3 (71°): Next warmer is day 5 (72°) → wait 2 days" << endl;
        cout << "- Day 4 (69°): Next warmer is day 5 (72°) → wait 1 day" << endl;
        cout << "- Day 5 (72°): Next warmer is day 6 (76°) → wait 1 day" << endl;
        cout << "- Day 6 (76°): No warmer day → wait 0 days" << endl;
        cout << "- Day 7 (73°): No warmer day → wait 0 days" << endl;
        
        cout << "\nTest Case 2: Increasing Temperatures" << endl;
        cout << "Input:  [30, 40, 50, 60]" << endl;
        cout << "Output: [1,  1,  1,  0]" << endl;
        cout << "Analysis: Each day except last has next day warmer" << endl;
        
        cout << "\nTest Case 3: Decreasing Temperatures" << endl;
        cout << "Input:  [30, 60, 90]" << endl;
        cout << "Output: [1,  1,  0]" << endl;
        cout << "Analysis: Continuously warming, last day has no warmer day" << endl;
        
        cout << "\nTest Case 4: All Same Temperatures" << endl;
        cout << "Input:  [30, 30, 30, 30]" << endl;
        cout << "Output: [0,  0,  0,  0]" << endl;
        cout << "Analysis: No warmer days available" << endl;
    }
};
```

### 4.2.3 Brute Force Implementation and Analysis

#### 4.2.3.1 Nested Loop Approach

```cpp
// 4.2.3.1.1 Brute Force Solution
class DailyTemperaturesBruteForce {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        cout << "=== Daily Temperatures - Brute Force Approach ===" << endl;
        cout << "Input temperatures: [";
        for (int i = 0; i < temperatures.size(); i++) {
            cout << temperatures[i];
            if (i < temperatures.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = temperatures.size();
        vector<int> result(n, 0);
        int totalComparisons = 0;
        
        for (int i = 0; i < n; i++) {
            cout << "\nDay " << i << " (Temperature: " << temperatures[i] << "°)" << endl;
            
            int comparisons = 0;
            for (int j = i + 1; j < n; j++) {
                comparisons++;
                totalComparisons++;
                
                cout << "  Checking day " << j << " (" << temperatures[j] << "°): ";
                
                if (temperatures[j] > temperatures[i]) {
                    result[i] = j - i;
                    cout << "WARMER! Wait " << result[i] << " days" << endl;
                    break;
                } else {
                    cout << "Not warmer" << endl;
                }
            }
            
            if (result[i] == 0) {
                cout << "  No warmer day found - result: 0" << endl;
            }
            
            cout << "  Comparisons made: " << comparisons << endl;
        }
        
        cout << "\nBrute Force Statistics:" << endl;
        cout << "Total comparisons: " << totalComparisons << endl;
        cout << "Average comparisons per day: " << (double)totalComparisons / n << endl;
        cout << "Worst case complexity: O(n²)" << endl;
        
        cout << "\nResult: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
    // 4.2.3.1.2 Performance analysis
    void analyzeComplexity(int n) {
        cout << "\n=== Brute Force Complexity Analysis ===" << endl;
        cout << "Array size: " << n << endl;
        cout << "Time Complexity: O(n²)" << endl;
        cout << "  - Outer loop: " << n << " iterations" << endl;
        cout << "  - Inner loop: Up to " << (n-1) << " iterations each" << endl;
        cout << "  - Total comparisons: Up to " << (n * (n-1)) / 2 << endl;
        
        cout << "Space Complexity: O(1) additional space" << endl;
        
        cout << "Performance estimates:" << endl;
        cout << "  n = 100: ~5,000 operations" << endl;
        cout << "  n = 1,000: ~500,000 operations" << endl;
        cout << "  n = 10,000: ~50,000,000 operations" << endl;
    }
};
```

### 4.2.4 Optimal Monotonic Stack Solution

#### 4.2.4.1 Stack-Based Implementation

```cpp
// 4.2.4.1.1 Optimal Monotonic Stack Solution
class DailyTemperaturesOptimal {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        cout << "=== Daily Temperatures - Optimal Monotonic Stack ===" << endl;
        cout << "Input temperatures: [";
        for (int i = 0; i < temperatures.size(); i++) {
            cout << temperatures[i];
            if (i < temperatures.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int n = temperatures.size();
        vector<int> result(n, 0);
        stack<int> monoStack;  // Stores indices of temperatures in decreasing order
        
        cout << "\nProcessing temperatures with monotonic stack:" << endl;
        
        for (int i = 0; i < n; i++) {
            cout << "\nDay " << i << " - Temperature: " << temperatures[i] << "°" << endl;
            
            // Process all days that will have current day as their answer
            int processedDays = 0;
            while (!monoStack.empty() && temperatures[monoStack.top()] < temperatures[i]) {
                int prevDay = monoStack.top();
                monoStack.pop();
                result[prevDay] = i - prevDay;
                processedDays++;
                
                cout << "  Resolved day " << prevDay << " (" << temperatures[prevDay] 
                     << "°): wait " << result[prevDay] << " days" << endl;
            }
            
            if (processedDays == 0) {
                cout << "  No previous days resolved" << endl;
            }
            
            // Add current day to stack
            monoStack.push(i);
            cout << "  Added day " << i << " to stack" << endl;
            
            // Display current stack state
            displayStackState(monoStack, temperatures);
        }
        
        // Handle remaining days in stack (no warmer day found)
        cout << "\nFinal processing - days with no warmer future:" << endl;
        while (!monoStack.empty()) {
            int day = monoStack.top();
            monoStack.pop();
            cout << "Day " << day << " (" << temperatures[day] << "°): No warmer day → 0" << endl;
        }
        
        cout << "\nOptimal Result: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
    // 4.2.4.1.2 Alternative implementation with detailed tracking
    vector<int> dailyTemperaturesWithTracking(vector<int>& temperatures) {
        cout << "\n=== Enhanced Implementation with Operation Tracking ===" << endl;
        
        int n = temperatures.size();
        vector<int> result(n, 0);
        stack<pair<int, int>> monoStack;  // {temperature, index}
        
        int stackOperations = 0;
        int totalComparisons = 0;
        
        for (int i = 0; i < n; i++) {
            int currentTemp = temperatures[i];
            cout << "\nProcessing day " << i << " (" << currentTemp << "°)" << endl;
            
            int popsInThisIteration = 0;
            
            while (!monoStack.empty() && monoStack.top().first < currentTemp) {
                auto [prevTemp, prevIndex] = monoStack.top();
                monoStack.pop();
                stackOperations++;
                totalComparisons++;
                popsInThisIteration++;
                
                result[prevIndex] = i - prevIndex;
                
                cout << "  Popped day " << prevIndex << " (" << prevTemp 
                     << "°) → wait " << result[prevIndex] << " days" << endl;
            }
            
            if (popsInThisIteration == 0) {
                cout << "  No days popped from stack" << endl;
            }
            
            monoStack.push({currentTemp, i});
            stackOperations++;
            
            cout << "  Stack size after processing: " << monoStack.size() << endl;
            cout << "  Operations in this iteration: " << (popsInThisIteration + 1) << endl;
        }
        
        cout << "\nPerformance Statistics:" << endl;
        cout << "Total stack operations: " << stackOperations << endl;
        cout << "Total comparisons: " << totalComparisons << endl;
        cout << "Average operations per day: " << (double)stackOperations / n << endl;
        cout << "Time complexity: O(n) - each element pushed and popped at most once" << endl;
        
        return result;
    }
    
    // 4.2.4.1.3 Memory-optimized version
    vector<int> dailyTemperaturesMemoryOptimized(vector<int>& temperatures) {
        cout << "\n=== Memory-Optimized Implementation ===" << endl;
        
        int n = temperatures.size();
        vector<int> result(n, 0);
        
        // Process from right to left to avoid extra space
        for (int i = n - 2; i >= 0; i--) {
            int j = i + 1;
            
            // Skip days that won't help
            while (j < n && temperatures[j] <= temperatures[i] && result[j] > 0) {
                j += result[j];
            }
            
            if (j < n && temperatures[j] > temperatures[i]) {
                result[i] = j - i;
            }
        }
        
        cout << "Memory-optimized result: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
private:
    void displayStackState(const stack<int>& s, const vector<int>& temperatures) {
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
            cout << "Day" << elements[i] << "(" << temperatures[elements[i]] << "°)";
            if (i < elements.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
    }
};
```

### 4.2.5 Advanced Variations and Extensions

#### 4.2.5.1 Multi-dimensional Temperature Analysis

```cpp
// 4.2.5.1.1 Extended temperature analysis
class AdvancedTemperatureAnalysis {
public:
    struct TemperatureAnalysis {
        vector<int> daysToWarmer;
        vector<int> daysToCooler;
        vector<int> longestWarmStreak;
        vector<int> longestCoolStreak;
        
        void display() const {
            cout << "\n=== Complete Temperature Analysis ===" << endl;
            cout << "Days to warmer:      [";
            for (int i = 0; i < daysToWarmer.size(); i++) {
                cout << daysToWarmer[i];
                if (i < daysToWarmer.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            cout << "Days to cooler:      [";
            for (int i = 0; i < daysToCooler.size(); i++) {
                cout << daysToCooler[i];
                if (i < daysToCooler.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            cout << "Longest warm streak: [";
            for (int i = 0; i < longestWarmStreak.size(); i++) {
                cout << longestWarmStreak[i];
                if (i < longestWarmStreak.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            cout << "Longest cool streak: [";
            for (int i = 0; i < longestCoolStreak.size(); i++) {
                cout << longestCoolStreak[i];
                if (i < longestCoolStreak.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
        }
    };
    
    TemperatureAnalysis completeAnalysis(vector<int>& temperatures) {
        cout << "=== Advanced Temperature Pattern Analysis ===" << endl;
        
        TemperatureAnalysis analysis;
        int n = temperatures.size();
        
        // Calculate days to warmer (original problem)
        analysis.daysToWarmer = calculateDaysToWarmer(temperatures);
        
        // Calculate days to cooler
        analysis.daysToCooler = calculateDaysToCooler(temperatures);
        
        // Calculate longest warming streaks
        analysis.longestWarmStreak = calculateWarmStreaks(temperatures);
        
        // Calculate longest cooling streaks
        analysis.longestCoolStreak = calculateCoolStreaks(temperatures);
        
        analysis.display();
        return analysis;
    }
    
private:
    vector<int> calculateDaysToWarmer(vector<int>& temperatures) {
        // Standard daily temperatures solution
        int n = temperatures.size();
        vector<int> result(n, 0);
        stack<int> monoStack;
        
        for (int i = 0; i < n; i++) {
            while (!monoStack.empty() && temperatures[monoStack.top()] < temperatures[i]) {
                int prevIndex = monoStack.top();
                monoStack.pop();
                result[prevIndex] = i - prevIndex;
            }
            monoStack.push(i);
        }
        
        return result;
    }
    
    vector<int> calculateDaysToCooler(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> result(n, 0);
        stack<int> monoStack;
        
        for (int i = 0; i < n; i++) {
            while (!monoStack.empty() && temperatures[monoStack.top()] > temperatures[i]) {
                int prevIndex = monoStack.top();
                monoStack.pop();
                result[prevIndex] = i - prevIndex;
            }
            monoStack.push(i);
        }
        
        return result;
    }
    
    vector<int> calculateWarmStreaks(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> result(n, 1);
        
        for (int i = 1; i < n; i++) {
            if (temperatures[i] > temperatures[i-1]) {
                result[i] = result[i-1] + 1;
            }
        }
        
        return result;
    }
    
    vector<int> calculateCoolStreaks(vector<int>& temperatures) {
        int n = temperatures.size();
        vector<int> result(n, 1);
        
        for (int i = 1; i < n; i++) {
            if (temperatures[i] < temperatures[i-1]) {
                result[i] = result[i-1] + 1;
            }
        }
        
        return result;
    }
};
```

---

## 4.3 Largest Rectangle in Histogram – Geometric Optimization

### 4.3.1 Problem Definition and Geometric Analysis

#### 4.3.1.1 Problem Statement
Given an array of integers representing the histogram's bar height where the width of each bar is 1, find the area of the largest rectangle in the histogram.

#### 4.3.1.2 Mathematical Foundation
For histogram heights H = [h₀, h₁, h₂, ..., hₙ₋₁], find maximum area:
```
MaxArea = max{h[i] × width | width = (right[i] - left[i] - 1)}
where:
- left[i] = index of previous smaller element than h[i]
- right[i] = index of next smaller element than h[i]
```

#### 4.3.1.3 Geometric Interpretation and Visualization

```cpp
// 4.3.1.3.1 Histogram visualization and analysis
class HistogramVisualizer {
public:
    void visualizeHistogram(const vector<int>& heights) {
        cout << "=== Histogram Visualization ===" << endl;
        cout << "Heights: [";
        for (int i = 0; i < heights.size(); i++) {
            cout << heights[i];
            if (i < heights.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        int maxHeight = *max_element(heights.begin(), heights.end());
        
        cout << "\nVisualization:" << endl;
        for (int level = maxHeight; level >= 1; level--) {
            cout << setw(2) << level << " |";
            for (int height : heights) {
                if (height >= level) {
                    cout << " █";
                } else {
                    cout << "  ";
                }
            }
            cout << endl;
        }
        
        cout << "   +";
        for (int i = 0; i < heights.size(); i++) {
            cout << "--";
        }
        cout << endl;
        
        cout << "    ";
        for (int i = 0; i < heights.size(); i++) {
            cout << setw(2) << i;
        }
        cout << endl;
    }
    
    void analyzeRectanglePossibilities(const vector<int>& heights) {
        cout << "\n=== Rectangle Analysis ===" << endl;
        
        for (int i = 0; i < heights.size(); i++) {
            cout << "Bar " << i << " (height " << heights[i] << "):" << endl;
            
            // Find maximum rectangle with this bar as the shortest
            int left = i, right = i;
            
            // Expand left
            while (left > 0 && heights[left - 1] >= heights[i]) {
                left--;
            }
            
            // Expand right
            while (right < heights.size() - 1 && heights[right + 1] >= heights[i]) {
                right++;
            }
            
            int width = right - left + 1;
            int area = heights[i] * width;
            
            cout << "  Can extend from index " << left << " to " << right;
            cout << " → width=" << width << ", area=" << area << endl;
        }
    }
};
```

### 4.3.2 Brute Force Approaches

#### 4.3.2.1 Naive O(n³) Solution

```cpp
// 4.3.2.1.1 Complete brute force implementation
class LargestRectangleBruteForce {
public:
    int largestRectangleAreaBruteForce(vector<int>& heights) {
        cout << "=== Largest Rectangle - Brute Force O(n³) ===" << endl;
        
        HistogramVisualizer visualizer;
        visualizer.visualizeHistogram(heights);
        
        int n = heights.size();
        int maxArea = 0;
        int totalOperations = 0;
        
        cout << "\nChecking all possible rectangles:" << endl;
        
        // Try all possible left boundaries
        for (int left = 0; left < n; left++) {
            // Try all possible right boundaries
            for (int right = left; right < n; right++) {
                // Find minimum height in range [left, right]
                int minHeight = INT_MAX;
                for (int k = left; k <= right; k++) {
                    minHeight = min(minHeight, heights[k]);
                    totalOperations++;
                }
                
                int width = right - left + 1;
                int area = minHeight * width;
                
                cout << "Rectangle [" << left << "," << right << "]: ";
                cout << "minHeight=" << minHeight << ", width=" << width;
                cout << ", area=" << area;
                
                if (area > maxArea) {
                    maxArea = area;
                    cout << " ← NEW MAX";
                }
                cout << endl;
            }
        }
        
        cout << "\nBrute Force Results:" << endl;
        cout << "Maximum area: " << maxArea << endl;
        cout << "Total operations: " << totalOperations << endl;
        cout << "Time complexity: O(n³)" << endl;
        
        return maxArea;
    }
    
    // 4.3.2.1.2 Optimized O(n²) brute force
    int largestRectangleAreaOptimizedBruteForce(vector<int>& heights) {
        cout << "\n=== Optimized Brute Force O(n²) ===" << endl;
        
        int n = heights.size();
        int maxArea = 0;
        int totalOperations = 0;
        
        for (int left = 0; left < n; left++) {
            int minHeight = heights[left];
            
            for (int right = left; right < n; right++) {
                // Update minimum height incrementally
                minHeight = min(minHeight, heights[right]);
                totalOperations++;
                
                int width = right - left + 1;
                int area = minHeight * width;
                
                if (area > maxArea) {
                    maxArea = area;
                    cout << "New max area: " << area << " (rectangle [" << left << "," << right << "])" << endl;
                }
            }
        }
        
        cout << "Optimized brute force results:" << endl;
        cout << "Maximum area: " << maxArea << endl;
        cout << "Total operations: " << totalOperations << endl;
        cout << "Time complexity: O(n²)" << endl;
        
        return maxArea;
    }
};
```

### 4.3.3 Optimal Monotonic Stack Solution

#### 4.3.3.1 Stack-Based Implementation

```cpp
// 4.3.3.1.1 Optimal monotonic stack solution
class LargestRectangleOptimal {
public:
    int largestRectangleArea(vector<int>& heights) {
        cout << "=== Largest Rectangle - Optimal Monotonic Stack ===" << endl;
        
        HistogramVisualizer visualizer;
        visualizer.visualizeHistogram(heights);
        
        int n = heights.size();
        stack<int> monoStack;  // Stores indices
        int maxArea = 0;
        
        cout << "\nProcessing with monotonic stack:" << endl;
        
        for (int i = 0; i <= n; i++) {
            // Use 0 as sentinel for final processing
            int currentHeight = (i == n) ? 0 : heights[i];
            
            cout << "\nStep " << (i + 1) << ": ";
            if (i < n) {
                cout << "Processing bar " << i << " (height " << currentHeight << ")" << endl;
            } else {
                cout << "Final processing (sentinel height 0)" << endl;
            }
            
            while (!monoStack.empty() && heights[monoStack.top()] > currentHeight) {
                int heightIndex = monoStack.top();
                monoStack.pop();
                
                int height = heights[heightIndex];
                int width = monoStack.empty() ? i : i - monoStack.top() - 1;
                int area = height * width;
                
                cout << "  Calculating rectangle with bar " << heightIndex << " as shortest:" << endl;
                cout << "    Height: " << height << endl;
                cout << "    Left boundary: " << (monoStack.empty() ? 0 : monoStack.top() + 1) << endl;
                cout << "    Right boundary: " << (i - 1) << endl;
                cout << "    Width: " << width << endl;
                cout << "    Area: " << area << endl;
                
                if (area > maxArea) {
                    maxArea = area;
                    cout << "    ★ NEW MAXIMUM AREA: " << maxArea << endl;
                }
            }
            
            if (i < n) {
                monoStack.push(i);
                cout << "  Added bar " << i << " to stack" << endl;
            }
            
            displayStackState(monoStack, heights);
        }
        
        cout << "\nOptimal Solution Results:" << endl;
        cout << "Maximum rectangle area: " << maxArea << endl;
        cout << "Time complexity: O(n)" << endl;
        cout << "Space complexity: O(n)" << endl;
        
        return maxArea;
    }
    
    // 4.3.3.1.2 Enhanced version with detailed rectangle tracking
    struct Rectangle {
        int left, right, height, area;
        
        Rectangle(int l, int r, int h) : left(l), right(r), height(h) {
            area = height * (right - left + 1);
        }
        
        void display() const {
            cout << "Rectangle[" << left << "," << right << "] - ";
            cout << "height=" << height << ", width=" << (right - left + 1);
            cout << ", area=" << area << endl;
        }
    };
    
    pair<int, vector<Rectangle>> largestRectangleWithDetails(vector<int>& heights) {
        cout << "\n=== Detailed Rectangle Analysis ===" << endl;
        
        int n = heights.size();
        stack<int> monoStack;
        vector<Rectangle> allRectangles;
        int maxArea = 0;
        
        for (int i = 0; i <= n; i++) {
            int currentHeight = (i == n) ? 0 : heights[i];
            
            while (!monoStack.empty() && heights[monoStack.top()] > currentHeight) {
                int heightIndex = monoStack.top();
                monoStack.pop();
                
                int height = heights[heightIndex];
                int left = monoStack.empty() ? 0 : monoStack.top() + 1;
                int right = i - 1;
                
                Rectangle rect(left, right, height);
                allRectangles.push_back(rect);
                
                maxArea = max(maxArea, rect.area);
            }
            
            if (i < n) {
                monoStack.push(i);
            }
        }
        
        // Display all calculated rectangles
        cout << "All possible rectangles:" << endl;
        sort(allRectangles.begin(), allRectangles.end(), 
             [](const Rectangle& a, const Rectangle& b) {
                 return a.area > b.area;
             });
        
        for (int i = 0; i < min(10, (int)allRectangles.size()); i++) {
            cout << "  " << (i + 1) << ". ";
            allRectangles[i].display();
        }
        
        return {maxArea, allRectangles};
    }
    
    // 4.3.3.1.3 Space-optimized version
    int largestRectangleAreaSpaceOptimized(vector<int>& heights) {
        cout << "\n=== Space-Optimized Implementation ===" << endl;
        
        // Add sentinel values to avoid edge case handling
        heights.push_back(0);
        heights.insert(heights.begin(), 0);
        
        int n = heights.size();
        stack<int> monoStack;
        int maxArea = 0;
        
        for (int i = 0; i < n; i++) {
            while (!monoStack.empty() && heights[monoStack.top()] > heights[i]) {
                int height = heights[monoStack.top()];
                monoStack.pop();
                int width = i - monoStack.top() - 1;
                maxArea = max(maxArea, height * width);
            }
            monoStack.push(i);
        }
        
        // Remove sentinel values
        heights.pop_back();
        heights.erase(heights.begin());
        
        cout << "Space-optimized result: " << maxArea << endl;
        return maxArea;
    }
    
private:
    void displayStackState(const stack<int>& s, const vector<int>& heights) {
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
            cout << "i" << elements[i] << "(h" << heights[elements[i]] << ")";
            if (i < elements.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
    }
};
```

### 4.3.4 Advanced Applications and Extensions

#### 4.3.4.1 Dynamic Histogram Updates

```cpp
// 4.3.4.1.1 Dynamic histogram with real-time updates
class DynamicHistogram {
private:
    vector<int> heights;
    
public:
    DynamicHistogram() {
        cout << "Dynamic Histogram created" << endl;
    }
    
    void addBar(int height) {
        heights.push_back(height);
        cout << "Added bar with height " << height << endl;
        
        // Calculate current max rectangle
        LargestRectangleOptimal solver;
        int maxArea = solver.largestRectangleArea(heights);
        
        cout << "Current maximum rectangle area: " << maxArea << endl;
    }
    
    void updateBar(int index, int newHeight) {
        if (index >= 0 && index < heights.size()) {
            int oldHeight = heights[index];
            heights[index] = newHeight;
            
            cout << "Updated bar " << index << " from height " << oldHeight 
                 << " to " << newHeight << endl;
            
            LargestRectangleOptimal solver;
            int maxArea = solver.largestRectangleArea(heights);
            
            cout << "New maximum rectangle area: " << maxArea << endl;
        }
    }
    
    void removeLastBar() {
        if (!heights.empty()) {
            int removedHeight = heights.back();
            heights.pop_back();
            
            cout << "Removed bar with height " << removedHeight << endl;
            
            if (!heights.empty()) {
                LargestRectangleOptimal solver;
                int maxArea = solver.largestRectangleArea(heights);
                cout << "New maximum rectangle area: " << maxArea << endl;
            } else {
                cout << "Histogram is now empty" << endl;
            }
        }
    }
    
    void displayCurrentState() {
        HistogramVisualizer visualizer;
        visualizer.visualizeHistogram(heights);
    }
};
```

---

## 4.4 Maximal Rectangle in Binary Matrix – 2D Extension

### 4.4.1 Problem Definition and 2D Geometric Analysis

#### 4.4.1.1 Problem Statement
Given a binary matrix filled with 0's and 1's, find the largest rectangle containing only 1's and return its area.

#### 4.4.1.2 Mathematical Foundation
For binary matrix M[m][n], convert each row into a histogram problem:
```
For each row i:
  height[j] = 0 if M[i][j] == 0
            = height[j] + 1 if M[i][j] == 1
  
MaxArea = max{largestRectangleInHistogram(height) for all rows}
```

#### 4.4.1.3 Algorithm Strategy and Decomposition

```cpp
// 4.4.1.3.1 2D Matrix Rectangle Analysis
class MaximalRectangleAnalyzer {
public:
    void analyzeMatrix(vector<vector<char>>& matrix) {
        cout << "=== 2D Binary Matrix Analysis ===" << endl;
        
        if (matrix.empty() || matrix[0].empty()) {
            cout << "Empty matrix" << endl;
            return;
        }
        
        int rows = matrix.size();
        int cols = matrix[0].size();
        
        cout << "Matrix dimensions: " << rows << " x " << cols << endl;
        displayMatrix(matrix);
        
        // Convert to histogram representation
        vector<vector<int>> histograms = convertToHistograms(matrix);
        displayHistograms(histograms);
        
        // Analyze rectangles in each row
        analyzeRowByRow(histograms);
    }
    
private:
    void displayMatrix(const vector<vector<char>>& matrix) {
        cout << "\nOriginal Binary Matrix:" << endl;
        cout << "   ";
        for (int j = 0; j < matrix[0].size(); j++) {
            cout << setw(2) << j;
        }
        cout << endl;
        
        for (int i = 0; i < matrix.size(); i++) {
            cout << setw(2) << i << " ";
            for (int j = 0; j < matrix[i].size(); j++) {
                cout << setw(2) << matrix[i][j];
            }
            cout << endl;
        }
    }
    
    vector<vector<int>> convertToHistograms(const vector<vector<char>>& matrix) {
        int rows = matrix.size();
        int cols = matrix[0].size();
        vector<vector<int>> histograms(rows, vector<int>(cols, 0));
        
        // First row
        for (int j = 0; j < cols; j++) {
            histograms[0][j] = (matrix[0][j] == '1') ? 1 : 0;
        }
        
        // Subsequent rows
        for (int i = 1; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (matrix[i][j] == '1') {
                    histograms[i][j] = histograms[i-1][j] + 1;
                } else {
                    histograms[i][j] = 0;
                }
            }
        }
        
        return histograms;
    }
    
    void displayHistograms(const vector<vector<int>>& histograms) {
        cout << "\nHistogram Heights for Each Row:" << endl;
        
        for (int i = 0; i < histograms.size(); i++) {
            cout << "Row " << i << ": [";
            for (int j = 0; j < histograms[i].size(); j++) {
                cout << histograms[i][j];
                if (j < histograms[i].size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            // Visualize histogram
            if (!histograms[i].empty()) {
                visualizeHistogram(histograms[i], i);
            }
        }
    }
    
    void visualizeHistogram(const vector<int>& heights, int rowNum) {
        int maxHeight = *max_element(heights.begin(), heights.end());
        if (maxHeight == 0) return;
        
        cout << "  Histogram visualization for row " << rowNum << ":" << endl;
        for (int level = maxHeight; level >= 1; level--) {
            cout << "    " << setw(2) << level << " |";
            for (int height : heights) {
                if (height >= level) {
                    cout << " █";
                } else {
                    cout << "  ";
                }
            }
            cout << endl;
        }
        cout << "       +";
        for (int i = 0; i < heights.size(); i++) {
            cout << "--";
        }
        cout << endl;
    }
    
    void analyzeRowByRow(const vector<vector<int>>& histograms) {
        cout << "\n=== Row-by-Row Rectangle Analysis ===" << endl;
        
        LargestRectangleOptimal solver;
        int globalMaxArea = 0;
        int bestRow = -1;
        
        for (int i = 0; i < histograms.size(); i++) {
            cout << "\nAnalyzing row " << i << ":" << endl;
            
            vector<int> heights = histograms[i];
            if (all_of(heights.begin(), heights.end(), [](int h) { return h == 0; })) {
                cout << "  All zeros - no rectangles possible" << endl;
                continue;
            }
            
            int rowMaxArea = solver.largestRectangleArea(heights);
            
            if (rowMaxArea > globalMaxArea) {
                globalMaxArea = rowMaxArea;
                bestRow = i;
                cout << "  ★ NEW GLOBAL MAXIMUM: " << globalMaxArea << endl;
            }
        }
        
        cout << "\n=== Final Results ===" << endl;
        cout << "Maximum rectangle area: " << globalMaxArea << endl;
        cout << "Found in row: " << bestRow << endl;
    }
};
```

### 4.4.2 Complete Implementation

#### 4.4.2.1 Optimal 2D Rectangle Solution

```cpp
// 4.4.2.1.1 Complete maximal rectangle implementation
class MaximalRectangle {
public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        cout << "=== Maximal Rectangle in Binary Matrix ===" << endl;
        
        if (matrix.empty() || matrix[0].empty()) {
            cout << "Empty matrix - returning 0" << endl;
            return 0;
        }
        
        MaximalRectangleAnalyzer analyzer;
        analyzer.analyzeMatrix(matrix);
        
        int rows = matrix.size();
        int cols = matrix[0].size();
        vector<int> heights(cols, 0);
        int maxArea = 0;
        
        cout << "\nProcessing each row as histogram:" << endl;
        
        for (int i = 0; i < rows; i++) {
            cout << "\n--- Processing Row " << i << " ---" << endl;
            
            // Update heights array
            updateHeights(matrix[i], heights);
            
            cout << "Current heights: [";
            for (int j = 0; j < heights.size(); j++) {
                cout << heights[j];
                if (j < heights.size() - 1) cout << ", ";
            }
            cout << "]" << endl;
            
            // Find largest rectangle in current histogram
            int currentArea = largestRectangleInHistogram(heights);
            cout << "Largest rectangle area in this histogram: " << currentArea << endl;
            
            if (currentArea > maxArea) {
                maxArea = currentArea;
                cout << "★ New maximum area: " << maxArea << " (at row " << i << ")" << endl;
            }
        }
        
        cout << "\nFinal maximum rectangle area: " << maxArea << endl;
        return maxArea;
    }
    
    // 4.4.2.1.2 Enhanced version with rectangle coordinates
    struct RectangleInfo {
        int area;
        int topRow, bottomRow;
        int leftCol, rightCol;
        
        RectangleInfo() : area(0), topRow(-1), bottomRow(-1), leftCol(-1), rightCol(-1) {}
        
        RectangleInfo(int a, int t, int b, int l, int r) 
            : area(a), topRow(t), bottomRow(b), leftCol(l), rightCol(r) {}
        
        void display() const {
            cout << "Rectangle: area=" << area 
                 << ", coordinates=(" << topRow << "," << leftCol 
                 << ") to (" << bottomRow << "," << rightCol << ")" << endl;
            cout << "Dimensions: " << (bottomRow - topRow + 1) << " x " 
                 << (rightCol - leftCol + 1) << endl;
        }
    };
    
    RectangleInfo maximalRectangleWithCoordinates(vector<vector<char>>& matrix) {
        cout << "\n=== Finding Maximal Rectangle with Coordinates ===" << endl;
        
        if (matrix.empty() || matrix[