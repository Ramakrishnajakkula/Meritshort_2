# Module 6: Practical Problems and Interview Preparation for Stacks and Queues

Welcome back, students! In our previous modules, we've covered the fundamentals, implementations, variations, and applications of stacks and queues. In this module, we'll focus on practical problems that commonly appear in coding interviews and competitive programming contests. Mastering these problems will strengthen your understanding and prepare you for technical interviews.

## 6.1 Classic Stack Problems

Stack-based problems appear frequently in technical interviews. Let's solve some classic stack problems that demonstrate key problem-solving patterns.

### The Next Greater Element Problem

**Problem Statement**:
Given an array, find the next greater element for each element in the array. The next greater element is the first element to the right that is greater than the current element. If no such element exists, output -1.

**Sample Test Cases**:
```
Input: [4, 5, 2, 25]
Output: [5, 25, 25, -1]

Input: [13, 7, 6, 12]
Output: [-1, 12, 12, -1]
```

**Brute Force Approach**:
We can use nested loops to find the next greater element for each array element:

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> nextGreaterBruteForce(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (arr[j] > arr[i]) {
                result[i] = arr[j];
                break;
            }
        }
    }
    
    return result;
}
```

**Time Complexity**: O(n²) - For each element, we might need to scan the entire remaining array
**Space Complexity**: O(1) - Excluding the output array

**Optimal Approach Using Stack**:
We can solve this problem more efficiently using a stack:

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

vector<int> nextGreaterElement(vector<int>& arr) {
    int n = arr.size();
    vector<int> result(n, -1);
    stack<int> st;  // Stack to store indices
    
    for (int i = 0; i < n; i++) {
        // While stack is not empty and current element is greater than element at stack top
        while (!st.empty() && arr[i] > arr[st.top()]) {
            // Set the next greater element for the element at stack top
            result[st.top()] = arr[i];
            st.pop();
        }
        // Push current index to stack
        st.push(i);
    }
    
    return result;
}

// Function to print vector
void printVector(const vector<int>& vec) {
    cout << "[";
    for (int i = 0; i < vec.size(); i++) {
        cout << vec[i];
        if (i < vec.size() - 1)
            cout << ", ";
    }
    cout << "]" << endl;
}

int main() {
    vector<int> arr1 = {4, 5, 2, 25};
    vector<int> result1 = nextGreaterElement(arr1);
    cout << "Input: ";
    printVector(arr1);
    cout << "Next Greater Elements: ";
    printVector(result1);
    
    vector<int> arr2 = {13, 7, 6, 12};
    vector<int> result2 = nextGreaterElement(arr2);
    cout << "Input: ";
    printVector(arr2);
    cout << "Next Greater Elements: ";
    printVector(result2);
    
    return 0;
}
```

**Time Complexity**: O(n) - Each element is pushed and popped at most once
**Space Complexity**: O(n) for the stack

**Explanation**:
1. We use a stack to keep track of indices of elements for which we haven't found the next greater element yet
2. For each element, we check if it's greater than elements at indices stored in the stack
3. If it is, we've found the next greater element for those indices, so we update the result and pop them from the stack
4. We then push the current index onto the stack
5. Any indices remaining in the stack at the end have no next greater element, so their result values remain -1

### Stock Span Problem

**Problem Statement**:
The stock span problem is a financial problem where we have a series of n daily price quotes for a stock, and we need to calculate the span of the stock's price for all n days. The span of the stock's price on a given day is defined as the maximum number of consecutive days (starting from that day and going backward) for which the stock price was less than or equal to the price on the current day.

**Sample Test Cases**:
```
Input: [100, 80, 60, 70, 60, 75, 85]
Output: [1, 1, 1, 2, 1, 4, 6]

Input: [10, 4, 5, 90, 120, 80]
Output: [1, 1, 2, 4, 5, 1]
```

**Brute Force Approach**:
For each day, we can count backward until we find a day with a higher price:

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> calculateSpanBruteForce(vector<int>& prices) {
    int n = prices.size();
    vector<int> span(n, 1);  // Initialize spans to 1
    
    for (int i = 0; i < n; i++) {
        int j = i - 1;
        while (j >= 0 && prices[j] <= prices[i]) {
            span[i]++;
            j--;
        }
    }
    
    return span;
}
```

**Time Complexity**: O(n²) in the worst case
**Space Complexity**: O(1) excluding the output array

**Optimal Approach Using Stack**:
We can use a stack to keep track of days with higher prices:

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

vector<int> calculateSpan(vector<int>& prices) {
    int n = prices.size();
    vector<int> span(n);
    stack<int> st;  // Stack to store indices of days
    
    for (int i = 0; i < n; i++) {
        // Pop elements from stack while prices[st.top()] <= prices[i]
        while (!st.empty() && prices[st.top()] <= prices[i]) {
            st.pop();
        }
        
        // If stack is empty, span is i+1, else i - st.top()
        span[i] = st.empty() ? (i + 1) : (i - st.top());
        
        // Push current day to stack
        st.push(i);
    }
    
    return span;
}

void printVector(const vector<int>& vec) {
    cout << "[";
    for (int i = 0; i < vec.size(); i++) {
        cout << vec[i];
        if (i < vec.size() - 1)
            cout << ", ";
    }
    cout << "]" << endl;
}

int main() {
    vector<int> prices1 = {100, 80, 60, 70, 60, 75, 85};
    vector<int> span1 = calculateSpan(prices1);
    cout << "Input: ";
    printVector(prices1);
    cout << "Stock Spans: ";
    printVector(span1);
    
    vector<int> prices2 = {10, 4, 5, 90, 120, 80};
    vector<int> span2 = calculateSpan(prices2);
    cout << "Input: ";
    printVector(prices2);
    cout << "Stock Spans: ";
    printVector(span2);
    
    return 0;
}
```

**Time Complexity**: O(n) - Each price is pushed and popped at most once
**Space Complexity**: O(n) for the stack

**Explanation**:
1. We use a stack to keep track of indices of days with higher prices
2. For each day, we pop elements from the stack as long as the price at the stack top is less than or equal to the current price
3. The span is calculated as:
   - If stack is empty, all previous prices are smaller, so span is i+1
   - Otherwise, span is the difference between current index and the index at stack top
4. We then push the current index onto the stack

### Largest Rectangle in Histogram

**Problem Statement**:
Given n non-negative integers representing the histogram's bar heights where the width of each bar is 1, find the area of the largest rectangle in the histogram.

**Sample Test Cases**:
```
Input: [2, 1, 5, 6, 2, 3]
Output: 10
Explanation: The largest rectangle has area = 10, formed by the bars with heights 5 and 6.
```

**Brute Force Approach**:
For each pair of bars, calculate the area of the rectangle with the minimum height:

```cpp
#include <iostream>
#include <vector>
using namespace std;

int largestRectangleAreaBruteForce(vector<int>& heights) {
    int n = heights.size();
    int maxArea = 0;
    
    for (int i = 0; i < n; i++) {
        int minHeight = heights[i];
        for (int j = i; j < n; j++) {
            minHeight = min(minHeight, heights[j]);
            int width = j - i + 1;
            maxArea = max(maxArea, minHeight * width);
        }
    }
    
    return maxArea;
}
```

**Time Complexity**: O(n²)
**Space Complexity**: O(1)

**Optimal Approach Using Stack**:
We can use a stack to find the largest rectangle efficiently:

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    stack<int> st;  // Stack to store indices
    int maxArea = 0;
    
    for (int i = 0; i <= n; i++) {
        // Process all bars or add a dummy bar of height 0 at the end
        int h = (i == n) ? 0 : heights[i];
        
        // If current bar is smaller, calculate area for all bars that are taller
        while (!st.empty() && h < heights[st.top()]) {
            int height = heights[st.top()];
            st.pop();
            
            // Calculate width: If stack is empty, all bars to the left are taller
            // Otherwise, width is the distance from current position to the next bar on stack
            int width = st.empty() ? i : i - st.top() - 1;
            
            maxArea = max(maxArea, height * width);
        }
        
        // Push current bar's index
        st.push(i);
    }
    
    return maxArea;
}

int main() {
    vector<int> heights = {2, 1, 5, 6, 2, 3};
    cout << "Input: [2, 1, 5, 6, 2, 3]" << endl;
    cout << "Largest Rectangle Area: " << largestRectangleArea(heights) << endl;
    
    return 0;
}
```

**Time Complexity**: O(n) - Each height is pushed and popped at most once
**Space Complexity**: O(n) for the stack

**Explanation**:
1. We use a stack to keep track of indices of bars in increasing order of height
2. When we encounter a bar shorter than the one at the top of the stack, we calculate the area for all taller bars
3. The width of the rectangle is determined by the current position and the next bar on the stack
4. We add a dummy bar of height 0 at the end to ensure all calculations are done
5. The maximum area is tracked throughout the process

## 6.2 Classic Queue Problems

Let's examine some classic problems that leverage queues for efficient solutions.

### Sliding Window Maximum

**Problem Statement**:
Given an array nums and a sliding window of size k that moves from the left to the right, return the maximum element in each window.

**Sample Test Cases**:
```
Input: nums = [1, 3, -1, -3, 5, 3, 6, 7], k = 3
Output: [3, 3, 5, 5, 6, 7]
Explanation:
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7      3
 1 [3  -1  -3] 5  3  6  7      3
 1  3 [-1  -3  5] 3  6  7      5
 1  3  -1 [-3  5  3] 6  7      5
 1  3  -1  -3 [5  3  6] 7      6
 1  3  -1  -3  5 [3  6  7]     7
```

**Brute Force Approach**:
For each window position, find the maximum element:

```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<int> maxSlidingWindowBruteForce(vector<int>& nums, int k) {
    int n = nums.size();
    vector<int> result;
    
    for (int i = 0; i <= n - k; i++) {
        int maxVal = nums[i];
        for (int j = 1; j < k; j++) {
            maxVal = max(maxVal, nums[i + j]);
        }
        result.push_back(maxVal);
    }
    
    return result;
}
```

**Time Complexity**: O(n * k)
**Space Complexity**: O(1) excluding the output array

**Optimal Approach Using Deque**:
We can use a deque to maintain a decreasing order of elements in the window:

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;

vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    int n = nums.size();
    vector<int> result;
    deque<int> dq;  // Store indices of elements in decreasing order
    
    for (int i = 0; i < n; i++) {
        // Remove elements outside the current window
        if (!dq.empty() && dq.front() == i - k) {
            dq.pop_front();
        }
        
        // Remove all elements smaller than current
        while (!dq.empty() && nums[dq.back()] < nums[i]) {
            dq.pop_back();
        }
        
        // Add current element index
        dq.push_back(i);
        
        // If window has k elements, add maximum to result
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}

void printVector(const vector<int>& vec) {
    cout << "[";
    for (int i = 0; i < vec.size(); i++) {
        cout << vec[i];
        if (i < vec.size() - 1)
            cout << ", ";
    }
    cout << "]" << endl;
}

int main() {
    vector<int> nums = {1, 3, -1, -3, 5, 3, 6, 7};
    int k = 3;
    
    vector<int> result = maxSlidingWindow(nums, k);
    
    cout << "Input: [1, 3, -1, -3, 5, 3, 6, 7], k = 3" << endl;
    cout << "Sliding Window Maximums: ";
    printVector(result);
    
    return 0;
}
```

**Time Complexity**: O(n) - Each element is processed once
**Space Complexity**: O(k) for the deque

**Explanation**:
1. We use a deque to store indices of elements in the current window that could be the maximum for some future window
2. For each element:
   - Remove elements that are outside the current window
   - Remove elements that are smaller than the current element (as they can never be the maximum)
   - Add the current element's index to the deque
   - If we have processed at least k elements, the front of the deque contains the maximum for the current window

### Generating Numbers with Given Digits

**Problem Statement**:
Given two digits d1 and d2, generate the first n numbers that can be formed using only these digits.

**Sample Test Cases**:
```
Input: d1 = 5, d2 = 6, n = 10
Output: [5, 6, 55, 56, 65, 66, 555, 556, 565, 566]
```

**Approach Using Queue**:
We can use a queue to generate numbers in increasing order:

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <string>
using namespace std;

vector<int> generateNumbers(int d1, int d2, int n) {
    vector<int> result;
    queue<string> q;
    
    // Start with the single-digit numbers
    q.push(to_string(d1));
    q.push(to_string(d2));
    
    // Generate n numbers
    while (result.size() < n) {
        string curr = q.front();
        q.pop();
        
        // Add current number to result
        result.push_back(stoi(curr));
        
        // If d1 and d2 are different, generate two new numbers
        // If they are the same, generate only one to avoid duplicates
        q.push(curr + to_string(d1));
        if (d1 != d2) {
            q.push(curr + to_string(d2));
        }
    }
    
    return result;
}

void printVector(const vector<int>& vec) {
    cout << "[";
    for (int i = 0; i < vec.size(); i++) {
        cout << vec[i];
        if (i < vec.size() - 1)
            cout << ", ";
    }
    cout << "]" << endl;
}

int main() {
    int d1 = 5, d2 = 6, n = 10;
    
    vector<int> result = generateNumbers(d1, d2, n);
    
    cout << "First " << n << " numbers using digits " << d1 << " and " << d2 << ": ";
    printVector(result);
    
    return 0;
}
```

**Time Complexity**: O(n) - We generate exactly n numbers
**Space Complexity**: O(n) for the queue and result

**Explanation**:
1. We start with the single-digit numbers (d1 and d2)
2. For each number in the queue, we generate two new numbers by appending d1 and d2
3. We continue until we have generated n numbers
4. The queue ensures we generate numbers in increasing order of length and value

### First Non-Repeating Character in a Stream

**Problem Statement**:
Given a stream of characters, find the first non-repeating character at each step.

**Sample Test Cases**:
```
Input: Stream = "aabcbcd"
Output: "a#bbccd"
Explanation:
a - First non-repeating is 'a'
aa - No non-repeating character, output '#'
aab - First non-repeating is 'b'
aabc - First non-repeating is 'b'
aabcb - First non-repeating is 'c'
aabcbc - First non-repeating is 'c'
aabcbcd - First non-repeating is 'd'
```

**Approach Using Queue**:
We can use a queue to keep track of the order of characters:

```cpp
#include <iostream>
#include <queue>
#include <unordered_map>
using namespace std;

string firstNonRepeating(string str) {
    int n = str.length();
    string result = "";
    
    // Queue to store characters in order of appearance
    queue<char> q;
    
    // Map to store frequency of characters
    unordered_map<char, int> freq;
    
    for (int i = 0; i < n; i++) {
        char ch = str[i];
        
        // Increase frequency
        freq[ch]++;
        
        // Push character to queue
        q.push(ch);
        
        // Remove characters from front of queue if they are repeating
        while (!q.empty() && freq[q.front()] > 1) {
            q.pop();
        }
        
        // If queue is empty, no non-repeating character
        if (q.empty()) {
            result += '#';
        } else {
            result += q.front();
        }
    }
    
    return result;
}

int main() {
    string stream = "aabcbcd";
    string result = firstNonRepeating(stream);
    
    cout << "Input stream: " << stream << endl;
    cout << "First non-repeating characters: " << result << endl;
    
    return 0;
}
```

**Time Complexity**: O(n) - Each character is processed once
**Space Complexity**: O(1) - At most 26 lowercase English letters in the queue and map

**Explanation**:
1. We use a queue to maintain the order of characters in the stream
2. For each character, we update its frequency in the map
3. We remove characters from the front of the queue if they are repeating (frequency > 1)
4. The first character in the queue is the first non-repeating character
5. If the queue is empty, there is no non-repeating character, so we output '#'

## 6.3 Stack and Queue Interview Questions

Let's review some common interview questions related to stacks and queues, with approaches to solve them efficiently.

### Implement a Queue using Stacks with Amortized O(1) Operations

**Problem Statement**:
Implement a queue using only two stacks. The implemented queue should support all the functions of a normal queue (push, peek, pop, and empty).

**Approach**:
We'll use two stacks: one for enqueue operations and one for dequeue operations.

```cpp
#include <iostream>
#include <stack>
using namespace std;

class MyQueue {
private:
    stack<int> s1; // For enqueue
    stack<int> s2; // For dequeue
    
    // Transfer elements from s1 to s2
    void transfer() {
        if (s2.empty()) {
            while (!s1.empty()) {
                s2.push(s1.top());
                s1.pop();
            }
        }
    }
    
public:
    MyQueue() {}
    
    // Push element x to the back of queue
    void push(int x) {
        s1.push(x);
    }
    
    // Remove the element from the front of queue and return it
    int pop() {
        transfer();
        if (s2.empty()) {
            throw runtime_error("Queue is empty");
        }
        int front = s2.top();
        s2.pop();
        return front;
    }
    
    // Get the front element
    int peek() {
        transfer();
        if (s2.empty()) {
            throw runtime_error("Queue is empty");
        }
        return s2.top();
    }
    
    // Return whether the queue is empty
    bool empty() {
        return s1.empty() && s2.empty();
    }
};

int main() {
    MyQueue queue;
    
    queue.push(1);
    queue.push(2);
    queue.push(3);
    
    cout << "Front element: " << queue.peek() << endl;
    cout << "Pop: " << queue.pop() << endl;
    cout << "Is empty? " << (queue.empty() ? "Yes" : "No") << endl;
    cout << "Front element after pop: " << queue.peek() << endl;
    
    queue.push(4);
    cout << "Front element after push: " << queue.peek() << endl;
    
    return 0;
}
```

**Time Complexity**:
- push: O(1)
- pop/peek: Amortized O(1) - each element is transferred at most once
- empty: O(1)

**Space Complexity**: O(n) for storing the elements

**Explanation**:
1. We use stack `s1` for push operations and stack `s2` for pop/peek operations
2. When popping or peeking, if `s2` is empty, we transfer all elements from `s1` to `s2`
3. This reverses the order of elements, effectively converting the LIFO behavior of stacks to the FIFO behavior of a queue
4. Each element is transferred at most once, so the amortized time complexity is O(1)

### Design a Stack with Min Operation

**Problem Statement**:
Design a stack that supports push, pop, top, and retrieving the minimum element in constant time.

**Approach**:
We'll use an auxiliary stack to keep track of the minimum element at each state of the main stack.

```cpp
#include <iostream>
#include <stack>
using namespace std;

class MinStack {
private:
    stack<int> mainStack;    // Main stack for elements
    stack<int> minStack;     // Stack to track minimum values
    
public:
    MinStack() {}
    
    void push(int val) {
        mainStack.push(val);
        
        // Update minStack
        if (minStack.empty() || val <= minStack.top()) {
            minStack.push(val);
        }
    }
    
    void pop() {
        if (mainStack.empty()) {
            throw runtime_error("Stack is empty");
        }
        
        // If top of mainStack is the minimum, also pop from minStack
        if (mainStack.top() == minStack.top()) {
            minStack.pop();
        }
        
        mainStack.pop();
    }
    
    int top() {
        if (mainStack.empty()) {
            throw runtime_error("Stack is empty");
        }
        return mainStack.top();
    }
    
    int getMin() {
        if (minStack.empty()) {
            throw runtime_error("Stack is empty");
        }
        return minStack.top();
    }
};

int main() {
    MinStack minStack;
    
    minStack.push(-2);
    minStack.push(0);
    minStack.push(-3);
    
    cout << "Minimum element: " << minStack.getMin() << endl;  // Returns -3
    
    minStack.pop();
    cout << "Top element: " << minStack.top() << endl;        // Returns 0
    cout << "Minimum element: " << minStack.getMin() << endl;  // Returns -2
    
    return 0;
}
```

**Time Complexity**:
- push: O(1)
- pop: O(1)
- top: O(1)
- getMin: O(1)

**Space Complexity**: O(n) in the worst case

**Explanation**:
1. We maintain two stacks: `mainStack` for regular stack operations and `minStack` to track the minimum values
2. When pushing an element, we also push to the `minStack` if it's less than or equal to the current minimum
3. When popping, we check if the popped element is the current minimum, and if so, we also pop from `minStack`
4. This ensures that `minStack.top()` always contains the minimum element in the current state of the stack

### Check if a String of Parentheses is Valid

**Problem Statement**:
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid. The string is valid if all parentheses are properly closed in the correct order.

**Approach**:
We can use a stack to keep track of opening brackets and match them with closing brackets.

```cpp
#include <iostream>
#include <stack>
#include <string>
#include <unordered_map>
using namespace std;

bool isValid(string s) {
    stack<char> st;
    unordered_map<char, char> bracketPairs = {
        {')', '('},
        {'}', '{'},
        {']', '['}
    };
    
    for (char c : s) {
        // If it's an opening bracket, push to stack
        if (c == '(' || c == '{' || c == '[') {
            st.push(c);
        }
        // If it's a closing bracket
        else if (bracketPairs.find(c) != bracketPairs.end()) {
            // If stack is empty or top doesn't match, string is invalid
            if (st.empty() || st.top() != bracketPairs[c]) {
                return false;
            }
            st.pop();
        }
    }
    
    // String is valid if stack is empty (all brackets matched)
    return st.empty();
}

int main() {
    string s1 = "()";
    string s2 = "()[]{}";
    string s3 = "(]";
    string s4 = "([)]";
    string s5 = "{[]}";
    
    cout << "\"" << s1 << "\" is " << (isValid(s1) ? "valid" : "invalid") << endl;
    cout << "\"" << s2 << "\" is " << (isValid(s2) ? "valid" : "invalid") << endl;
    cout << "\"" << s3 << "\" is " << (isValid(s3) ? "valid" : "invalid") << endl;
    cout << "\"" << s4 << "\" is " << (isValid(s4) ? "valid" : "invalid") << endl;
    cout << "\"" << s5 << "\" is " << (isValid(s5) ? "valid" : "invalid") << endl;
    
    return 0;
}
```

**Time Complexity**: O(n) where n is the length of the string
**Space Complexity**: O(n) in the worst case

**Explanation**:
1. We use a stack to keep track of opening brackets
2. For each closing bracket, we check if it matches the most recent opening bracket
3. If there's a mismatch or the stack is empty when we encounter a closing bracket, the string is invalid
4. After processing all characters, the string is valid if the stack is empty (all brackets matched)

### Circular Tour (Gas Station Problem)

**Problem Statement**:
There are n gas stations along a circular route, where the amount of gas at station i is gas[i]. You have a car with an unlimited gas tank, and it costs cost[i] of gas to travel from station i to station i+1. You begin the journey with an empty tank at one of the gas stations. Return the starting gas station's index if you can travel around the circuit once, otherwise return -1.

**Approach**:
We can solve this using a queue-like approach:

```cpp
#include <iostream>
#include <vector>
using namespace std;

int canCompleteCircuit(vector<int>& gas, vector<int>& cost) {
    int n = gas.size();
    
    int totalGas = 0;
    int totalCost = 0;
    int tank = 0;
    int startStation = 0;
    
    for (int i = 0; i < n; i++) {
        totalGas += gas[i];
        totalCost += cost[i];
        
        tank += gas[i] - cost[i];
        
        // If we can't reach the next station
        if (tank < 0) {
            // Start from the next station
            startStation = i + 1;
            // Reset the tank
            tank = 0;
        }
    }
    
    // If total gas is less than total cost, we can't complete the circuit
    if (totalGas < totalCost) {
        return -1;
    }
    
    return startStation;
}

int main() {
    vector<int> gas = {1, 2, 3, 4, 5};
    vector<int> cost = {3, 4, 5, 1, 2};
    
    int result = canCompleteCircuit(gas, cost);
    
    cout << "Gas: [1, 2, 3, 4, 5]" << endl;
    cout << "Cost: [3, 4, 5, 1, 2]" << endl;
    
    if (result != -1) {
        cout << "Can complete circuit starting from station " << result << endl;
    } else {
        cout << "Cannot complete the circuit" << endl;
    }
    
    return 0;
}
```

**Time Complexity**: O(n)
**Space Complexity**: O(1)

**Explanation**:
1. We track the total gas and total cost to determine if a solution exists at all
2. We simulate driving around the circuit, keeping track of the gas in the tank
3. If at any point the tank becomes negative, we can't reach the next station from the current starting point
4. In that case, we update the starting station to the next one and reset the tank
5. If total gas is less than total cost, it's impossible to complete the circuit

### Design and Implement an LRU Cache

**Problem Statement**:
Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: get and put.

- get(key): Get the value of the key if the key exists in the cache, otherwise return -1.
- put(key, value): Set or insert the value if the key is not already present. When the cache reaches its capacity, it should invalidate the least recently used item before inserting a new item.

**Approach**:
We'll use a combination of a hash map and a doubly linked list:

```cpp
#include <iostream>
#include <unordered_map>
using namespace std;

class LRUCache {
private:
    // Doubly linked list node
    struct Node {
        int key;
        int value;
        Node* prev;
        Node* next;
        
        Node(int k, int v) : key(k), value(v), prev(nullptr), next(nullptr) {}
    };
    
    int capacity;
    unordered_map<int, Node*> cache;
    Node* head; // Most recently used
    Node* tail; // Least recently used
    
    // Add node to front (most recently used)
    void addToFront(Node* node) {
        node->next = head->next;
        node->prev = head;
        head->next->prev = node;
        head->next = node;
    }
    
    // Remove node from list
    void removeNode(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }
    
    // Move node to front (mark as recently used)
    void moveToFront(Node* node) {
        removeNode(node);
        addToFront(node);
    }
    
    // Remove the least recently used node (from tail)
    Node* removeLRU() {
        Node* lru = tail->prev;
        removeNode(lru);
        return lru;
    }
    
public:
    LRUCache(int capacity) {
        this->capacity = capacity;
        
        // Initialize dummy head and tail nodes
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head->next = tail;
        tail->prev = head;
    }
    
    ~LRUCache() {
        Node* current = head;
        while (current) {
            Node* next = current->next;
            delete current;
            current = next;
        }
    }
    
    int get(int key) {
        if (cache.find(key) == cache.end()) {
            return -1;
        }
        
        // Move the accessed node to the front
        Node* node = cache[key];
        moveToFront(node);
        
        return node->value;
    }
    
    void put(int key, int value) {
        // If key exists, update value and move to front
        if (cache.find(key) != cache.end()) {
            Node* node = cache[key];
            node->value = value;
            moveToFront(node);
            return;
        }
        
        // If cache is full, remove LRU
        if (cache.size() == capacity) {
            Node* lru = removeLRU();
            cache.erase(lru->key);
            delete lru;
        }
        
        // Create new node and add to front
        Node* newNode = new Node(key, value);
        cache[key] = newNode;
        addToFront(newNode);
    }
};

int main() {
    LRUCache cache(2);  // Capacity = 2
    
    cache.put(1, 1);
    cache.put(2, 2);
    cout << "get(1): " << cache.get(1) << endl;       // returns 1
    
    cache.put(3, 3);                                  // evicts key 2
    cout << "get(2): " << cache.get(2) << endl;       // returns -1 (not found)
    
    cache.put(4, 4);                                  // evicts key 1
    cout << "get(1): " << cache.get(1) << endl;       // returns -1 (not found)
    cout << "get(3): " << cache.get(3) << endl;       // returns 3
    cout << "get(4): " << cache.get(4) << endl;       // returns 4
    
    return 0;
}
```

**Time Complexity**:
- get: O(1)
- put: O(1)

**Space Complexity**: O(capacity)

**Explanation**:
1. We use a hash map for O(1) lookups and a doubly linked list to maintain the order of use
2. The doubly linked list is ordered from most recently used (front) to least recently used (back)
3. When we access a key, we move its node to the front of the list
4. When we add a new key and the cache is full, we remove the node at the end of the list (least recently used)
5. The hash map maps keys to nodes in the linked list for quick access

## Summary

In this module, we've explored practical problems and interview questions related to stacks and queues:

1. **Classic Stack Problems**:
   - Next Greater Element
   - Stock Span Problem
   - Largest Rectangle in Histogram

2. **Classic Queue Problems**:
   - Sliding Window Maximum
   - Generating Numbers with Given Digits
   - First Non-Repeating Character in a Stream

3. **Stack and Queue Interview Questions**:
   - Implementing a Queue using Stacks
   - Designing a Stack with Min Operation
   - Validating Parentheses Strings
   - Solving the Circular Tour Problem
   - Implementing an LRU Cache

These problems showcase the versatility and power of stacks and queues in solving complex algorithmic challenges. The key takeaways from this module include:

- Recognizing patterns where stacks or queues can provide efficient solutions
- Understanding the trade-offs between different approaches
- Using auxiliary data structures to enhance functionality
- Optimizing for time and space complexity

By mastering these problems, you'll be well-prepared for technical interviews and competitive programming contests. Remember to practice implementing these solutions on your own to reinforce your understanding.

In our next module, we'll explore advanced applications and system design aspects of stacks and queues.