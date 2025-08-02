# 🔹 Block 7: Final Challenges & Practice – Mastery Assessment & Advanced Applications

## 7.2 Medium Stack Problems (Timed Practice)

### 7.2.1 Problem 1: Remove K Digits

#### 7.2.1.1 Problem Statement
Given a non-negative integer `num` represented as a string and an integer `k`, remove `k` digits from the number so that the new number is the smallest possible.

#### 7.2.1.2 Sample Input and Output
```
Input: num = "1432219", k = 3
Output: "1219"

Input: num = "10200", k = 1
Output: "200"

Input: num = "10", k = 2
Output: "0"
```

#### 7.2.1.3 Explanation
- For "1432219" with k=3: We need to remove 3 digits to get the smallest number
- Remove '4', '3', '2' to get "1219" (smallest possible)
- Leading zeros should be removed, and if result is empty, return "0"

#### 7.2.1.4 Brute Force Approach
**Algorithm**: Try all possible combinations of removing k digits
```cpp
// Brute Force - O(C(n,k) * n) where C(n,k) is combinations
string removeKDigitsBruteForce(string num, int k) {
    if (k >= num.length()) return "0";
    
    string minResult = num;
    
    // Generate all combinations of removing k digits
    function<void(string, int, int)> backtrack = [&](string current, int removed, int start) {
        if (removed == k) {
            // Remove leading zeros
            int i = 0;
            while (i < current.length() && current[i] == '0') i++;
            string candidate = current.substr(i);
            if (candidate.empty()) candidate = "0";
            
            // Compare with current minimum
            if (candidate.length() < minResult.length() || 
                (candidate.length() == minResult.length() && candidate < minResult)) {
                minResult = candidate;
            }
            return;
        }
        
        for (int i = start; i < current.length(); i++) {
            string next = current.substr(0, i) + current.substr(i + 1);
            backtrack(next, removed + 1, i);
        }
    };
    
    backtrack(num, 0, 0);
    return minResult;
}
```

**Time Complexity**: O(C(n,k) × n) - exponential
**Space Complexity**: O(n) - recursion depth

#### 7.2.1.5 Optimal Approach - Monotonic Stack
**Algorithm**: Use stack to maintain increasing sequence, remove larger digits greedily
```cpp
string removeKDigits(string num, int k) {
    if (k >= num.length()) return "0";
    
    stack<char> st;
    int toRemove = k;
    
    for (char digit : num) {
        // Remove larger digits from stack while we can
        while (!st.empty() && st.top() > digit && toRemove > 0) {
            st.pop();
            toRemove--;
        }
        st.push(digit);
    }
    
    // Remove remaining digits from end if needed
    while (toRemove > 0 && !st.empty()) {
        st.pop();
        toRemove--;
    }
    
    // Build result string
    string result = "";
    while (!st.empty()) {
        result = st.top() + result;
        st.pop();
    }
    
    // Remove leading zeros
    int start = 0;
    while (start < result.length() && result[start] == '0') {
        start++;
    }
    
    result = result.substr(start);
    return result.empty() ? "0" : result;
}
```

**Time Complexity**: O(n) - each digit pushed and popped at most once
**Space Complexity**: O(n) - stack storage

---

### 7.2.2 Problem 2: Asteroid Collision

#### 7.2.2.1 Problem Statement
We are given an array `asteroids` of integers representing asteroids in a row. For each asteroid, the absolute value represents its size, and the sign represents its direction (positive meaning right, negative meaning left). Find out the state of the asteroids after all collisions.

#### 7.2.2.2 Sample Input and Output
```
Input: asteroids = [5,10,-5]
Output: [5,10]

Input: asteroids = [8,-8]
Output: []

Input: asteroids = [10,2,-5]
Output: [10]
```

#### 7.2.2.3 Explanation
- Asteroids moving right (positive) and left (negative) can collide
- When collision occurs, smaller asteroid explodes; if equal, both explode
- Only right-moving asteroids in stack can collide with incoming left-moving asteroids

#### 7.2.2.4 Brute Force Approach
**Algorithm**: Simulate collisions repeatedly until no more collisions occur
```cpp
// Brute Force - O(n²) in worst case
vector<int> asteroidCollisionBruteForce(vector<int>& asteroids) {
    vector<int> result = asteroids;
    bool hasCollision = true;
    
    while (hasCollision) {
        hasCollision = false;
        vector<int> newResult;
        
        for (int i = 0; i < result.size(); i++) {
            if (i < result.size() - 1 && result[i] > 0 && result[i + 1] < 0) {
                // Collision detected
                hasCollision = true;
                int right = result[i];
                int left = -result[i + 1];
                
                if (right > left) {
                    newResult.push_back(result[i]);
                } else if (left > right) {
                    newResult.push_back(result[i + 1]);
                }
                // If equal, both explode (add neither)
                i++; // Skip next asteroid as it's been processed
            } else {
                newResult.push_back(result[i]);
            }
        }
        result = newResult;
    }
    
    return result;
}
```

**Time Complexity**: O(n²) - multiple passes may be needed
**Space Complexity**: O(n) - temporary arrays

#### 7.2.2.5 Optimal Approach - Stack
**Algorithm**: Use stack to track right-moving asteroids, handle collisions as left-moving asteroids arrive
```cpp
vector<int> asteroidCollision(vector<int>& asteroids) {
    stack<int> st;
    
    for (int asteroid : asteroids) {
        bool exploded = false;
        
        // Handle collisions with right-moving asteroids in stack
        while (!st.empty() && st.top() > 0 && asteroid < 0) {
            int rightMoving = st.top();
            int leftMoving = -asteroid;
            
            if (rightMoving < leftMoving) {
                st.pop(); // Right asteroid explodes
            } else if (rightMoving == leftMoving) {
                st.pop(); // Both explode
                exploded = true;
                break;
            } else {
                exploded = true; // Left asteroid explodes
                break;
            }
        }
        
        if (!exploded) {
            st.push(asteroid);
        }
    }
    
    // Convert stack to result vector
    vector<int> result;
    while (!st.empty()) {
        result.push_back(st.top());
        st.pop();
    }
    reverse(result.begin(), result.end());
    
    return result;
}
```

**Time Complexity**: O(n) - each asteroid processed once
**Space Complexity**: O(n) - stack storage

---

### 7.2.3 Problem 3: Decode String

#### 7.2.3.1 Problem Statement
Given an encoded string, return its decoded string. The encoding rule is: `k[encoded_string]`, where the `encoded_string` inside the square brackets should be repeated exactly `k` times.

#### 7.2.3.2 Sample Input and Output
```
Input: s = "3[a]2[bc]"
Output: "aaabcbc"

Input: s = "2[abc]3[cd]ef"
Output: "abcabccdcdcdef"

Input: s = "abc3[cd]xyz"
Output: "abccdcdcdxyz"
```

#### 7.2.3.3 Explanation
- Numbers indicate repetition count for the following bracketed string
- Brackets can be nested: `2[a2[bc]]` becomes `2[abcbc]` becomes `abcbcabcbc`
- Process inner brackets first, then outer brackets

#### 7.2.3.4 Brute Force Approach
**Algorithm**: Recursively find innermost brackets and expand them
```cpp
// Brute Force - O(n * m) where m is max repetitions
string decodeStringBruteForce(string s) {
    // Find innermost bracket
    while (s.find('[') != string::npos) {
        int rightBracket = s.find(']');
        int leftBracket = rightBracket;
        
        // Find corresponding left bracket
        while (leftBracket >= 0 && s[leftBracket] != '[') {
            leftBracket--;
        }
        
        // Find the number before left bracket
        int numStart = leftBracket - 1;
        while (numStart >= 0 && isdigit(s[numStart])) {
            numStart--;
        }
        numStart++;
        
        // Extract components
        int count = stoi(s.substr(numStart, leftBracket - numStart));
        string toRepeat = s.substr(leftBracket + 1, rightBracket - leftBracket - 1);
        
        // Build repeated string
        string repeated = "";
        for (int i = 0; i < count; i++) {
            repeated += toRepeat;
        }
        
        // Replace in original string
        s = s.substr(0, numStart) + repeated + s.substr(rightBracket + 1);
    }
    
    return s;
}
```

**Time Complexity**: O(n × m) where m is maximum repetition factor
**Space Complexity**: O(n × m) for string operations

#### 7.2.3.5 Optimal Approach - Stack
**Algorithm**: Use two stacks to track counts and strings at each nesting level
```cpp
string decodeString(string s) {
    stack<int> countStack;
    stack<string> stringStack;
    string currentString = "";
    int currentCount = 0;
    
    for (char c : s) {
        if (isdigit(c)) {
            currentCount = currentCount * 10 + (c - '0');
        } else if (c == '[') {
            // Save current state and start new level
            countStack.push(currentCount);
            stringStack.push(currentString);
            currentCount = 0;
            currentString = "";
        } else if (c == ']') {
            // Process current level
            int repeatCount = countStack.top();
            countStack.pop();
            string previousString = stringStack.top();
            stringStack.pop();
            
            // Repeat current string and combine with previous
            string repeated = "";
            for (int i = 0; i < repeatCount; i++) {
                repeated += currentString;
            }
            currentString = previousString + repeated;
        } else {
            currentString += c;
        }
    }
    
    return currentString;
}
```

**Time Complexity**: O(n + m) where m is the length of decoded string
**Space Complexity**: O(n) - stack storage

---

### 7.2.4 Problem 4: Validate Stack Sequences

#### 7.2.4.1 Problem Statement
Given two sequences `pushed` and `popped` with distinct values, return `true` if and only if this could have been the result of a sequence of push and pop operations on an initially empty stack.

#### 7.2.4.2 Sample Input and Output
```
Input: pushed = [1,2,3,4,5], popped = [4,5,3,2,1]
Output: true

Input: pushed = [1,2,3,4,5], popped = [4,3,5,1,2]
Output: false
```

#### 7.2.4.3 Explanation
- We need to check if we can generate `popped` sequence by performing push/pop operations
- For each element in `popped`, it must be either on top of stack or pushable from remaining `pushed` elements

#### 7.2.4.4 Brute Force Approach
**Algorithm**: Try all possible sequences of push/pop operations
```cpp
// Brute Force - exponential complexity
bool validateStackSequencesBruteForce(vector<int>& pushed, vector<int>& popped) {
    // Generate all possible valid sequences and check if any matches popped
    vector<int> result;
    
    function<bool(stack<int>, int, int)> backtrack = [&](stack<int> st, int pushIdx, int popIdx) -> bool {
        if (popIdx == popped.size()) {
            return pushIdx == pushed.size() && st.empty();
        }
        
        // Try pop operation
        if (!st.empty() && st.top() == popped[popIdx]) {
            stack<int> newSt = st;
            newSt.pop();
            if (backtrack(newSt, pushIdx, popIdx + 1)) {
                return true;
            }
        }
        
        // Try push operation
        if (pushIdx < pushed.size()) {
            stack<int> newSt = st;
            newSt.push(pushed[pushIdx]);
            if (backtrack(newSt, pushIdx + 1, popIdx)) {
                return true;
            }
        }
        
        return false;
    };
    
    return backtrack(stack<int>(), 0, 0);
}
```

**Time Complexity**: O(2^n) - exponential
**Space Complexity**: O(n) - recursion depth

#### 7.2.4.5 Optimal Approach - Simulation
**Algorithm**: Simulate the stack operations by pushing elements and checking if we can pop required elements
```cpp
bool validateStackSequences(vector<int>& pushed, vector<int>& popped) {
    stack<int> st;
    int pushIndex = 0;
    
    for (int target : popped) {
        // Push elements until we find the target or exhaust pushed array
        while (st.empty() || st.top() != target) {
            if (pushIndex >= pushed.size()) {
                return false; // Can't find target
            }
            st.push(pushed[pushIndex++]);
        }
        
        // Pop the target element
        st.pop();
    }
    
    return true;
}
```

**Time Complexity**: O(n) - each element pushed and popped at most once
**Space Complexity**: O(n) - stack storage

---

### 7.2.5 Problem 5: Next Greater Element II (Circular Array)

#### 7.2.5.1 Problem Statement
Given a circular integer array `nums`, return the next greater number for every element. The next greater number of a number `x` is the first greater number to its traversing-order next in the array, which means you could search circularly to find its next greater number.

#### 7.2.5.2 Sample Input and Output
```
Input: nums = [1,2,1]
Output: [2,-1,2]

Input: nums = [1,2,3,4,3]
Output: [2,3,4,-1,4]
```

#### 7.2.5.3 Explanation
- For circular array, after reaching end, continue from beginning
- [1,2,1]: First 1 has next greater 2, 2 has no greater (-1), last 1 has next greater 2 (circular)

#### 7.2.5.4 Brute Force Approach
**Algorithm**: For each element, search circularly for next greater element
```cpp
// Brute Force - O(n²)
vector<int> nextGreaterElementsBruteForce(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    
    for (int i = 0; i < n; i++) {
        // Search circularly for next greater element
        for (int j = 1; j < n; j++) {
            int nextIndex = (i + j) % n;
            if (nums[nextIndex] > nums[i]) {
                result[i] = nums[nextIndex];
                break;
            }
        }
    }
    
    return result;
}
```

**Time Complexity**: O(n²) - nested loops
**Space Complexity**: O(1) - excluding result array

#### 7.2.5.5 Optimal Approach - Monotonic Stack with Circular Handling
**Algorithm**: Process array twice using monotonic stack to handle circular nature
```cpp
vector<int> nextGreaterElements(vector<int>& nums) {
    int n = nums.size();
    vector<int> result(n, -1);
    stack<int> st; // Store indices
    
    // Process array twice to handle circular nature
    for (int i = 2 * n - 1; i >= 0; i--) {
        int actualIndex = i % n;
        
        // Remove smaller or equal elements
        while (!st.empty() && nums[st.top()] <= nums[actualIndex]) {
            st.pop();
        }
        
        // Set result only for first pass (when i < n)
        if (i < n && !st.empty()) {
            result[actualIndex] = nums[st.top()];
        }
        
        st.push(actualIndex);
    }
    
    return result;
}
```

**Time Complexity**: O(n) - each element processed at most twice
**Space Complexity**: O(n) - stack storage

---

## 7.3 Medium Queue Problems (Timed Practice)

### 7.3.1 Problem 1: Design Circular Queue

#### 7.3.1.1 Problem Statement
Design your implementation of the circular queue. The circular queue is a linear data structure in which the operations are performed based on FIFO principle and the last position is connected back to the first position to make a circle.

#### 7.3.1.2 Sample Input and Output
```
Input:
["MyCircularQueue","enQueue","enQueue","enQueue","enQueue","Rear","isFull","deQueue","enQueue","Rear"]
[[3],[1],[2],[3],[4],[],[],[],[4],[]]

Output:
[null,true,true,true,false,3,true,true,true,4]
```

#### 7.3.1.3 Explanation
- Initialize queue with size 3
- Operations: enQueue(1), enQueue(2), enQueue(3), enQueue(4) fails, Rear() returns 3, isFull() true, deQueue(), enQueue(4), Rear() returns 4

#### 7.3.1.4 Brute Force Approach
**Algorithm**: Use dynamic array and shift elements on dequeue
```cpp
// Brute Force - O(n) dequeue due to shifting
class MyCircularQueueBruteForce {
private:
    vector<int> data;
    int capacity;

public:
    MyCircularQueueBruteForce(int k) : capacity(k) {}
    
    bool enQueue(int value) {
        if (data.size() >= capacity) return false;
        data.push_back(value);
        return true;
    }
    
    bool deQueue() {
        if (data.empty()) return false;
        data.erase(data.begin()); // O(n) operation
        return true;
    }
    
    int Front() {
        return data.empty() ? -1 : data[0];
    }
    
    int Rear() {
        return data.empty() ? -1 : data.back();
    }
    
    bool isEmpty() { return data.empty(); }
    bool isFull() { return data.size() == capacity; }
};
```

**Time Complexity**: O(1) for most operations, O(n) for deQueue
**Space Complexity**: O(k) where k is capacity

#### 7.3.1.5 Optimal Approach - Circular Array
**Algorithm**: Use circular array with front and rear pointers
```cpp
class MyCircularQueue {
private:
    vector<int> queue;
    int front, rear, size, capacity;

public:
    MyCircularQueue(int k) : capacity(k), front(0), rear(-1), size(0) {
        queue.resize(k);
    }
    
    bool enQueue(int value) {
        if (isFull()) return false;
        rear = (rear + 1) % capacity;
        queue[rear] = value;
        size++;
        return true;
    }
    
    bool deQueue() {
        if (isEmpty()) return false;
        front = (front + 1) % capacity;
        size--;
        return true;
    }
    
    int Front() {
        return isEmpty() ? -1 : queue[front];
    }
    
    int Rear() {
        return isEmpty() ? -1 : queue[rear];
    }
    
    bool isEmpty() { return size == 0; }
    bool isFull() { return size == capacity; }
};
```

**Time Complexity**: O(1) for all operations
**Space Complexity**: O(k) where k is capacity

---

### 7.3.2 Problem 2: Sliding Window Maximum

#### 7.3.2.1 Problem Statement
You are given an array of integers `nums`, and there is a sliding window of size `k` which is moving from the very left of the array to the very right. Return the max sliding window.

#### 7.3.2.2 Sample Input and Output
```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]

Input: nums = [1], k = 1
Output: [1]
```

#### 7.3.2.3 Explanation
- Window positions and maximums:
  [1 3 -1] -3 5 3 6 7 → max = 3
  1 [3 -1 -3] 5 3 6 7 → max = 3  
  1 3 [-1 -3 5] 3 6 7 → max = 5

#### 7.3.2.4 Brute Force Approach
**Algorithm**: For each window, find maximum by linear search
```cpp
// Brute Force - O(n*k)
vector<int> maxSlidingWindowBruteForce(vector<int>& nums, int k) {
    vector<int> result;
    
    for (int i = 0; i <= nums.size() - k; i++) {
        int maxVal = nums[i];
        for (int j = i; j < i + k; j++) {
            maxVal = max(maxVal, nums[j]);
        }
        result.push_back(maxVal);
    }
    
    return result;
}
```

**Time Complexity**: O(n × k) where n is array size
**Space Complexity**: O(1) excluding result array

#### 7.3.2.5 Optimal Approach - Deque
**Algorithm**: Use deque to maintain elements in decreasing order within window
```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    deque<int> dq; // Store indices
    vector<int> result;
    
    for (int i = 0; i < nums.size(); i++) {
        // Remove indices outside current window
        while (!dq.empty() && dq.front() <= i - k) {
            dq.pop_front();
        }
        
        // Remove indices of smaller elements
        while (!dq.empty() && nums[dq.back()] < nums[i]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
        
        // Add to result if window is complete
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}
```

**Time Complexity**: O(n) - each element added and removed at most once
**Space Complexity**: O(k) - deque size

---

### 7.3.3 Problem 3: Task Scheduler

#### 7.3.3.1 Problem Statement
Given a char array `tasks` representing tasks CPU need to do and integer `n` representing the cooldown period between two same tasks, return the least number of units of times that the CPU will take to finish all the given tasks.

#### 7.3.3.2 Sample Input and Output
```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8

Input: tasks = ["A","A","A","B","B","B"], n = 0
Output: 6
```

#### 7.3.3.3 Explanation
- With n=2, same task needs 2 units of cooldown
- Optimal: A → B → idle → A → B → idle → A → B (8 units)
- With n=0, no cooldown needed: A → A → A → B → B → B (6 units)

#### 7.3.3.4 Brute Force Approach
**Algorithm**: Simulate the scheduling process step by step
```cpp
// Brute Force - O(time * unique_tasks)
int leastIntervalBruteForce(vector<char>& tasks, int n) {
    unordered_map<char, int> count;
    for (char task : tasks) count[task]++;
    
    unordered_map<char, int> lastExecution;
    int time = 0;
    
    while (!count.empty()) {
        char chosen = 0;
        
        // Find available task with highest count
        for (auto& [task, freq] : count) {
            if (lastExecution[task] + n < time) { // Available
                if (chosen == 0 || freq > count[chosen]) {
                    chosen = task;
                }
            }
        }
        
        if (chosen != 0) {
            lastExecution[chosen] = time;
            count[chosen]--;
            if (count[chosen] == 0) {
                count.erase(chosen);
            }
        }
        // else: idle time
        
        time++;
    }
    
    return time;
}
```

**Time Complexity**: O(time × unique_tasks) - can be very large
**Space Complexity**: O(unique_tasks)

#### 7.3.3.5 Optimal Approach - Priority Queue + Queue
**Algorithm**: Use priority queue for task frequencies and queue for cooldown tracking
```cpp
int leastInterval(vector<char>& tasks, int n) {
    // Count task frequencies
    unordered_map<char, int> count;
    for (char task : tasks) count[task]++;
    
    // Max heap for frequencies
    priority_queue<int> pq;
    for (auto& [task, freq] : count) {
        pq.push(freq);
    }
    
    queue<pair<int, int>> cooldown; // {frequency, available_time}
    int time = 0;
    
    while (!pq.empty() || !cooldown.empty()) {
        time++;
        
        // Add tasks back from cooldown
        if (!cooldown.empty() && cooldown.front().second == time) {
            pq.push(cooldown.front().first);
            cooldown.pop();
        }
        
        // Execute highest frequency task
        if (!pq.empty()) {
            int freq = pq.top() - 1;
            pq.pop();
            
            if (freq > 0) {
                cooldown.push({freq, time + n + 1});
            }
        }
    }
    
    return time;
}
```

**Time Complexity**: O(n × log(unique_tasks)) where n is total time
**Space Complexity**: O(unique_tasks)

---

### 7.3.4 Problem 4: Design Hit Counter

#### 7.3.4.1 Problem Statement
Design a hit counter which counts the number of hits received in the past 5 minutes (300 seconds). Each function accepts a timestamp parameter and you may assume that calls are being made to the system in chronological order.

#### 7.3.4.2 Sample Input and Output
```
Input:
["HitCounter","hit","hit","hit","getHits","hit","getHits","getHits"]
[[],[1],[2],[3],[4],[300],[300],[301]]

Output:
[null,null,null,null,3,null,4,3]
```

#### 7.3.4.3 Explanation
- hit(1), hit(2), hit(3): Add hits at timestamps 1, 2, 3
- getHits(4): Return 3 (all hits within 300 seconds)
- hit(300): Add hit at timestamp 300
- getHits(300): Return 4 (all hits within [1, 300])
- getHits(301): Return 3 (hits within [2, 301], timestamp 1 is out of range)

#### 7.3.4.4 Brute Force Approach
**Algorithm**: Store all hits and count valid ones for each query
```cpp
// Brute Force - O(n) per getHits call
class HitCounterBruteForce {
private:
    vector<int> hits;

public:
    void hit(int timestamp) {
        hits.push_back(timestamp);
    }
    
    int getHits(int timestamp) {
        int count = 0;
        for (int hitTime : hits) {
            if (hitTime > timestamp - 300) {
                count++;
            }
        }
        return count;
    }
};
```

**Time Complexity**: O(1) for hit, O(n) for getHits
**Space Complexity**: O(n) where n is total hits

#### 7.3.4.5 Optimal Approach - Queue with Cleanup
**Algorithm**: Use queue to maintain hits within time window, remove expired hits
```cpp
class HitCounter {
private:
    queue<int> hits;

public:
    void hit(int timestamp) {
        hits.push(timestamp);
    }
    
    int getHits(int timestamp) {
        // Remove hits older than 300 seconds
        while (!hits.empty() && hits.front() <= timestamp - 300) {
            hits.pop();
        }
        return hits.size();
    }
};
```

**Time Complexity**: O(1) amortized for both operations
**Space Complexity**: O(k) where k is hits in last 300 seconds

---

### 7.3.5 Problem 5: Design Snake Game

#### 7.3.5.1 Problem Statement
Design a Snake game that is played on a device with screen size `height × width`. The snake is initially positioned at the top left corner `(0, 0)` with length 1. You are given a list of food's positions. When a snake eats the food, its length and the game's score both increase by 1.

#### 7.3.5.2 Sample Input and Output
```
Input:
["SnakeGame","move","move","move","move","move","move"]
[[3,2,[[1,2],[0,1]]],["R"],["D"],["R"],["U"],["L"],["U"]]

Output:
[null,0,0,1,1,2,-1]
```

#### 7.3.5.3 Explanation
- 3×2 board, food at [1,2] and [0,1]
- Snake starts at (0,0), moves R→D→R→U→L→U
- Game ends when snake hits itself or boundary

#### 7.3.5.4 Brute Force Approach
**Algorithm**: Use list to track snake body, check entire body for collisions
```cpp
// Brute Force - O(n) per move where n is snake length
class SnakeGameBruteForce {
private:
    int width, height, score, foodIndex;
    vector<vector<int>> food;
    vector<pair<int, int>> snake;

public:
    SnakeGameBruteForce(int width, int height, vector<vector<int>>& food) 
        : width(width), height(height), score(0), foodIndex(0), food(food) {
        snake.push_back({0, 0});
    }
    
    int move(string direction) {
        auto [headX, headY] = snake[0];
        
        // Calculate new head position
        if (direction == "U") headX--;
        else if (direction == "D") headX++;
        else if (direction == "L") headY--;
        else if (direction == "R") headY++;
        
        // Check boundary
        if (headX < 0 || headX >= height || headY < 0 || headY >= width) {
            return -1;
        }
        
        // Check self collision (excluding tail for now)
        for (int i = 0; i < snake.size() - 1; i++) {
            if (snake[i].first == headX && snake[i].second == headY) {
                return -1;
            }
        }
        
        // Check food
        bool ateFood = false;
        if (foodIndex < food.size() && 
            food[foodIndex][0] == headX && food[foodIndex][1] == headY) {
            score++;
            foodIndex++;
            ateFood = true;
        }
        
        // Update snake
        snake.insert(snake.begin(), {headX, headY});
        if (!ateFood) {
            snake.pop_back();
        }
        
        return score;
    }
};
```

**Time Complexity**: O(n) per move for collision detection
**Space Complexity**: O(n) where n is snake length

#### 7.3.5.5 Optimal Approach - Deque + HashSet
**Algorithm**: Use deque for snake body and hashset for O(1) collision detection
```cpp
class SnakeGame {
private:
    int width, height, score, foodIndex;
    vector<vector<int>> food;
    deque<pair<int, int>> snake;
    unordered_set<string> snakeSet; // For O(1) collision detection

public:
    SnakeGame(int width, int height, vector<vector<int>>& food) 
        : width(width), height(height), score(0), foodIndex(0), food(food) {
        snake.push_back({0, 0});
        snakeSet.insert("0,0");
    }
    
    int move(string direction) {
        auto [headX, headY] = snake.front();
        
        // Calculate new head position
        if (direction == "U") headX--;
        else if (direction == "D") headX++;
        else if (direction == "L") headY--;
        else if (direction == "R") headY++;
        
        // Check boundary
        if (headX < 0 || headX >= height || headY < 0 || headY >= width) {
            return -1;
        }
        
        string newHead = to_string(headX) + "," + to_string(headY);
        
        // Check food
        bool ateFood = false;
        if (foodIndex < food.size() && 
            food[foodIndex][0] == headX && food[foodIndex][1] == headY) {
            score++;
            foodIndex++;
            ateFood = true;
        }
        
        // Remove tail if no food eaten
        if (!ateFood) {
            auto [tailX, tailY] = snake.back();
            snake.pop_back();
            snakeSet.erase(to_string(tailX) + "," + to_string(tailY));
        }
        
        // Check self collision
        if (snakeSet.count(newHead)) {
            return -1;
        }
        
        // Add new head
        snake.push_front({headX, headY});
        snakeSet.insert(newHead);
        
        return score;
    }
};
```

**Time Complexity**: O(1) per move
**Space Complexity**: O(n) where n is snake length

---

## 7.4 Hard Stack Problems

### 7.4.1 Problem 1: Largest Rectangle in Histogram

#### 7.4.1.1 Problem Statement
Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

#### 7.4.1.2 Sample Input and Output
```
Input: heights = [2,1,5,6,2,3]
Output: 10

Input: heights = [2,4]
Output: 4
```

#### 7.4.1.3 Explanation
- The largest rectangle has area 10 (width=2, height=5)
- We can form this rectangle using bars at indices 2 and 3

#### 7.4.1.4 Brute Force Approach
**Algorithm**: For each bar, find maximum width rectangle with that bar as minimum height
```cpp
// Brute Force - O(n²)
int largestRectangleAreaBruteForce(vector<int>& heights) {
    int maxArea = 0;
    int n = heights.size();
    
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

#### 7.4.1.5 Optimal Approach - Monotonic Stack
**Algorithm**: Use stack to find previous and next smaller elements for each bar
```cpp
int largestRectangleArea(vector<int>& heights) {
    stack<int> st;
    int maxArea = 0;
    
    for (int i = 0; i <= heights.size(); i++) {
        int currentHeight = (i == heights.size()) ? 0 : heights[i];
        
        while (!st.empty() && heights[st.top()] > currentHeight) {
            int height = heights[st.top()];
            st.pop();
            
            int width = st.empty() ? i : i - st.top() - 1;
            maxArea = max(maxArea, height * width);
        }
        
        st.push(i);
    }
    
    return maxArea;
}
```

**Time Complexity**: O(n) - each element pushed and popped once
**Space Complexity**: O(n) - stack storage

---

### 7.4.2 Problem 2: Basic Calculator

#### 7.4.2.1 Problem Statement
Given a string `s` representing a valid expression, implement a basic calculator to evaluate it and return the result. The expression may contain open and closing parentheses, plus and minus operators, non-negative integers, and empty spaces.

#### 7.4.2.2 Sample Input and Output
```
Input: s = "1 + 1"
Output: 2

Input: s = " 2-1 + 2 "
Output: 3

Input: s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

#### 7.4.2.3 Explanation
- Parse expression with parentheses, addition, and subtraction
- Handle nested parentheses and operator precedence
- Ignore whitespace characters

#### 7.4.2.4 Brute Force Approach
**Algorithm**: Recursively evaluate expressions by finding innermost parentheses
```cpp
// Brute Force - exponential in worst case
int calculateBruteForce(string s) {
    // Remove spaces
    string cleaned = "";
    for (char c : s) {
        if (c != ' ') cleaned += c;
    }
    
    // Find innermost parentheses and evaluate
    while (cleaned.find('(') != string::npos) {
        int start = -1, end = -1;
        
        for (int i = 0; i < cleaned.length(); i++) {
            if (cleaned[i] == '(') start = i;
            else if (cleaned[i] == ')') {
                end = i;
                break;
            }
        }
        
        string subExpr = cleaned.substr(start + 1, end - start - 1);
        int result = evaluateSimple(subExpr);
        cleaned = cleaned.substr(0, start) + to_string(result) + cleaned.substr(end + 1);
    }
    
    return evaluateSimple(cleaned);
}

int evaluateSimple(string expr) {
    int result = 0, sign = 1, num = 0;
    
    for (char c : expr) {
        if (isdigit(c)) {
            num = num * 10 + (c - '0');
        } else {
            result += sign * num;
            num = 0;
            sign = (c == '+') ? 1 : -1;
        }
    }
    
    return result + sign * num;
}
```

**Time Complexity**: O(n²) in worst case due to string operations
**Space Complexity**: O(n) for string manipulation

#### 7.4.2.5 Optimal Approach - Stack
**Algorithm**: Use stack to handle parentheses and maintain running sum/sign
```cpp
int calculate(string s) {
    stack<int> st;
    int result = 0, sign = 1, num = 0;
    
    for (char c : s) {
        if (isdigit(c)) {
            num = num * 10 + (c - '0');
        } else if (c == '+' || c == '-') {
            result += sign * num;
            sign = (c == '+') ? 1 : -1;
            num = 0;
        } else if (c == '(') {
            // Save current state
            st.push(result);
            st.push(sign);
            result = 0;
            sign = 1;
        } else if (c == ')') {
            // Restore previous state
            result += sign * num;
            num = 0;
            
            result *= st.top(); st.pop(); // Previous sign
            result += st.top(); st.pop(); // Previous result
        }
    }
    
    return result + sign * num;
}
```

**Time Complexity**: O(n) - single pass through string
**Space Complexity**: O(n) - stack for nested parentheses

---

## 7.5 Hard Queue Problems

### 7.5.1 Problem 1: Sliding Window Median

#### 7.5.1.1 Problem Statement
The median is the middle value in an ordered integer list. Given an array `nums`, there is a sliding window of size `k` which is moving from the very left to the very right. Return the median array for each window.

#### 7.5.1.2 Sample Input and Output
```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [1.00000,-1.00000,-1.00000,3.00000,5.00000,6.00000]

Input: nums = [1,2,3,4,2,3,1,4,2], k = 3
Output: [2.00000,3.00000,3.00000,3.00000,2.00000,3.00000,2.00000]
```

#### 7.5.1.3 Explanation
- For each window of size k, find the median
- If k is odd, median is middle element; if even, average of two middle elements

#### 7.5.1.4 Brute Force Approach
**Algorithm**: For each window, sort elements and find median
```cpp
// Brute Force - O(n * k * log(k))
vector<double> medianSlidingWindowBruteForce(vector<int>& nums, int k) {
    vector<double> result;
    
    for (int i = 0; i <= nums.size() - k; i++) {
        vector<int> window(nums.begin() + i, nums.begin() + i + k);
        sort(window.begin(), window.end());
        
        double median;
        if (k % 2 == 1) {
            median = window[k / 2];
        } else {
            median = ((long long)window[k / 2 - 1] + window[k / 2]) / 2.0;
        }
        result.push_back(median);
    }
    
    return result;
}
```

**Time Complexity**: O(n × k × log(k))
**Space Complexity**: O(k) for window storage

#### 7.5.1.5 Optimal Approach - Two Heaps + Lazy Deletion
**Algorithm**: Use max heap for smaller half and min heap for larger half
```cpp
vector<double> medianSlidingWindow(vector<int>& nums, int k) {
    multiset<int> window(nums.begin(), nums.begin() + k);
    auto mid = next(window.begin(), k / 2);
    vector<double> result;
    
    for (int i = k; ; i++) {
        // Calculate median
        if (k % 2 == 1) {
            result.push_back(*mid);
        } else {
            result.push_back(((long long)*mid + *prev(mid)) / 2.0);
        }
        
        if (i == nums.size()) break;
        
        // Add new element
        window.insert(nums[i]);
        if (nums[i] < *mid) mid--;
        
        // Remove old element
        if (nums[i - k] <= *mid) mid++;
        window.erase(window.lower_bound(nums[i - k]));
    }
    
    return result;
}
```

**Time Complexity**: O(n × log(k)) - log(k) for multiset operations
**Space Complexity**: O(k) - window storage

---

### 7.5.2 Problem 2: Shortest Subarray with Sum at Least K

#### 7.5.2.1 Problem Statement
Given an integer array `nums` and an integer `k`, return the length of the shortest non-empty subarray of `nums` with a sum of at least `k`. If there is no such subarray, return `-1`.

#### 7.5.2.2 Sample Input and Output
```
Input: nums = [1], k = 1
Output: 1

Input: nums = [1,2], k = 4
Output: -1

Input: nums = [2,-1,2], k = 3
Output: 3
```

#### 7.5.2.3 Explanation
- Find shortest contiguous subarray with sum ≥ k
- Array can contain negative numbers, making sliding window approach invalid
- Need to handle negative values correctly

#### 7.5.2.4 Brute Force Approach
**Algorithm**: Check all possible subarrays
```cpp
// Brute Force - O(n²)
int shortestSubarrayBruteForce(vector<int>& nums, int k) {
    int n = nums.size();
    int minLen = INT_MAX;
    
    for (int i = 0; i < n; i++) {
        int sum = 0;
        for (int j = i; j < n; j++) {
            sum += nums[j];
            if (sum >= k) {
                minLen = min(minLen, j - i + 1);
                break; // Found shortest starting at i
            }
        }
    }
    
    return minLen == INT_MAX ? -1 : minLen;
}
```

**Time Complexity**: O(n²)
**Space Complexity**: O(1)

#### 7.5.2.5 Optimal Approach - Deque with Prefix Sum
**Algorithm**: Use deque to maintain prefix sums in increasing order
```cpp
int shortestSubarray(vector<int>& nums, int k) {
    int n = nums.size();
    vector<long long> prefixSum(n + 1, 0);
    
    // Calculate prefix sums
    for (int i = 0; i < n; i++) {
        prefixSum[i + 1] = prefixSum[i] + nums[i];
    }
    
    deque<int> dq; // Store indices
    int minLen = INT_MAX;
    
    for (int i = 0; i <= n; i++) {
        // Check if we can form a valid subarray
        while (!dq.empty() && prefixSum[i] - prefixSum[dq.front()] >= k) {
            minLen = min(minLen, i - dq.front());
            dq.pop_front();
        }
        
        // Maintain increasing order of prefix sums
        while (!dq.empty() && prefixSum[i] <= prefixSum[dq.back()]) {
            dq.pop_back();
        }
        
        dq.push_back(i);
    }
    
    return minLen == INT_MAX ? -1 : minLen;
}
```

**Time Complexity**: O(n) - each index added and removed at most once
**Space Complexity**: O(n) - prefix sum array and deque

This completes Block 7 with comprehensive problem statements, explanations, and both brute force and optimal solutions for all major stack and queue problems at medium and hard difficulty levels.