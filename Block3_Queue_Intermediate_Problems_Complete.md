# 🔹 Block 3: Queue – Intermediate Problems & Advanced Implementations

## 3.0 Block Overview and Learning Objectives

### 3.0.1 Introduction to Block 3
Block 3 focuses on advanced queue concepts and implementations, building upon the foundational queue knowledge from previous blocks. This comprehensive module covers circular queues, double-ended queues (deques), and complex problem-solving using queue data structures.

### 3.0.2 Learning Objectives
By the end of Block 3, students will be able to:
- Design and implement circular queue data structures with optimal space utilization
- Create double-ended queue (deque) implementations supporting bi-directional operations
- Solve streaming data problems using queue-based approaches
- Convert between different data structures (stack-queue conversions)
- Analyze time and space complexity of queue operations
- Apply advanced queue concepts to real-world programming scenarios

### 3.0.3 Prerequisites
- Basic understanding of linear data structures
- Proficiency in array and linked list implementations
- Knowledge of basic queue operations (enqueue, dequeue)
- Understanding of time and space complexity analysis

---

## 3.1 Circular Queue Design and Implementation

### 3.1.1 Theoretical Foundation

#### 3.1.1.1 Definition and Concept
A **Circular Queue** is a linear data structure that follows the First In First Out (FIFO) principle while connecting the end of the queue back to the front, forming a circular arrangement. This design eliminates the space wastage problem inherent in simple linear queues.

#### 3.1.1.2 Mathematical Model
The circular queue operates on modular arithmetic principles:
```
Next Position = (Current Position + 1) % Queue Size
Previous Position = (Current Position - 1 + Queue Size) % Queue Size
```

#### 3.1.1.3 Key Characteristics
- **Space Efficiency**: Reuses memory locations freed by dequeue operations
- **Fixed Capacity**: Predetermined maximum size that cannot be exceeded
- **Wrap-around Logic**: Automatic position wrapping when boundaries are reached
- **State Management**: Sophisticated logic to distinguish between full and empty states

#### 3.1.1.4 Real-world Applications
- **Operating Systems**: CPU scheduling algorithms (Round Robin)
- **Network Programming**: Buffer management for data streams
- **Embedded Systems**: Circular buffers for sensor data
- **Gaming**: Turn-based game mechanics
- **Traffic Management**: Traffic light control systems

### 3.1.2 Problem Statement and Specification

#### 3.1.2.1 Formal Problem Definition
Design and implement a circular queue data structure that supports the following operations with optimal time complexity:

**Required Operations:**
- `CircularQueue(k)`: Initialize the queue with maximum capacity k
- `enQueue(value)`: Insert element into the circular queue
- `deQueue()`: Remove element from the front of the queue
- `Front()`: Retrieve the front element without removal
- `Rear()`: Retrieve the rear element without removal
- `isEmpty()`: Check if the queue contains no elements
- `isFull()`: Check if the queue has reached maximum capacity

#### 3.1.2.2 Sample Test Cases

**Test Case 1: Basic Operations**
```
Input Sequence:
["CircularQueue", "enQueue", "enQueue", "enQueue", "enQueue", "Rear", "isFull", "deQueue", "enQueue", "Rear"]
[[3], [1], [2], [3], [4], [], [], [], [4], []]

Expected Output:
[null, true, true, true, false, 3, true, true, true, 4]

Explanation:
1. Initialize queue with capacity 3
2. enQueue(1) → success, queue: [1, _, _], front=0, rear=0
3. enQueue(2) → success, queue: [1, 2, _], front=0, rear=1
4. enQueue(3) → success, queue: [1, 2, 3], front=0, rear=2
5. enQueue(4) → failure (queue full), queue: [1, 2, 3]
6. Rear() → returns 3
7. isFull() → returns true
8. deQueue() → success, queue: [_, 2, 3], front=1, rear=2
9. enQueue(4) → success, queue: [4, 2, 3], front=1, rear=0 (wrapped)
10. Rear() → returns 4
```

**Test Case 2: Edge Cases**
```
Input: Empty queue operations
Operations: Front(), Rear(), deQueue() on empty queue
Expected: All operations should return -1 or false appropriately

Input: Single element operations
Operations: enQueue(1), Front(), Rear(), deQueue(), isEmpty()
Expected: Proper handling of single-element state transitions
```

### 3.1.3 Brute Force Approach Analysis

#### 3.1.3.1 Naive Implementation Strategy
The brute force approach uses a dynamic array where dequeue operations shift all remaining elements forward, resulting in inefficient space and time usage.

#### 3.1.3.2 Brute Force Implementation

```cpp
#include <iostream>
#include <vector>
using namespace std;

class CircularQueueBruteForce {
private:
    vector<int> data;
    int maxCapacity;
    
public:
    // 3.1.3.2.1 Constructor
    CircularQueueBruteForce(int k) : maxCapacity(k) {
        data.reserve(k); // Pre-allocate memory
    }
    
    // 3.1.3.2.2 Enqueue Operation
    bool enQueue(int value) {
        if (data.size() >= maxCapacity) {
            cout << "Queue overflow: Cannot insert " << value << endl;
            return false;
        }
        
        data.push_back(value);
        cout << "Enqueued: " << value << ", Size: " << data.size() << endl;
        return true;
    }
    
    // 3.1.3.2.3 Dequeue Operation - O(n) complexity
    bool deQueue() {
        if (data.empty()) {
            cout << "Queue underflow: Cannot dequeue from empty queue" << endl;
            return false;
        }
        
        int removedValue = data[0];
        data.erase(data.begin()); // O(n) operation - shifts all elements
        cout << "Dequeued: " << removedValue << ", Size: " << data.size() << endl;
        return true;
    }
    
    // 3.1.3.2.4 Access Operations
    int Front() {
        if (data.empty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        return data[0];
    }
    
    int Rear() {
        if (data.empty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        return data[data.size() - 1];
    }
    
    // 3.1.3.2.5 State Check Operations
    bool isEmpty() {
        return data.empty();
    }
    
    bool isFull() {
        return data.size() == maxCapacity;
    }
    
    // 3.1.3.2.6 Utility Functions
    void displayQueue() {
        if (data.empty()) {
            cout << "Queue: []" << endl;
            return;
        }
        
        cout << "Queue: [";
        for (size_t i = 0; i < data.size(); i++) {
            cout << data[i];
            if (i < data.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
    }
    
    void displayStatistics() {
        cout << "Queue Statistics:" << endl;
        cout << "- Current Size: " << data.size() << endl;
        cout << "- Maximum Capacity: " << maxCapacity << endl;
        cout << "- Space Utilization: " << (double)data.size() / maxCapacity * 100 << "%" << endl;
        cout << "- Is Empty: " << (isEmpty() ? "Yes" : "No") << endl;
        cout << "- Is Full: " << (isFull() ? "Yes" : "No") << endl;
    }
};
```

#### 3.1.3.3 Complexity Analysis of Brute Force Approach

**Time Complexity:**
- `enQueue(value)`: O(1) - Simple append operation
- `deQueue()`: O(n) - Requires shifting all remaining elements
- `Front()`: O(1) - Direct array access
- `Rear()`: O(1) - Direct array access
- `isEmpty()`: O(1) - Size check
- `isFull()`: O(1) - Size comparison

**Space Complexity:**
- O(k) where k is the maximum capacity
- Additional overhead for dynamic array management

**Drawbacks:**
- Inefficient dequeue operation requiring element shifting
- Poor performance for high-frequency dequeue operations
- Does not utilize the circular nature for space optimization

### 3.1.4 Optimal Approach Design

#### 3.1.4.1 Circular Array Strategy
The optimal approach uses a fixed-size array with two pointers (front and rear) that wrap around using modular arithmetic, achieving O(1) complexity for all operations.

#### 3.1.4.2 State Management Techniques
To distinguish between empty and full states in a circular queue, we use multiple approaches:

1. **Size Counter Method**: Maintain an explicit size counter
2. **Wasted Slot Method**: Keep one slot always empty
3. **Flag Method**: Use boolean flags for state tracking

#### 3.1.4.3 Optimal Implementation

```cpp
#include <iostream>
#include <vector>
#include <iomanip>
using namespace std;

class CircularQueueOptimal {
private:
    vector<int> queue;
    int front, rear, currentSize, capacity;
    
    // 3.1.4.3.1 Internal Helper Methods
    int getNextPosition(int position) const {
        return (position + 1) % capacity;
    }
    
    int getPreviousPosition(int position) const {
        return (position - 1 + capacity) % capacity;
    }
    
public:
    // 3.1.4.3.2 Constructor with Initialization
    CircularQueueOptimal(int k) : capacity(k), currentSize(0) {
        if (k <= 0) {
            throw invalid_argument("Queue capacity must be positive");
        }
        
        queue.resize(k);
        front = -1;  // Initialize to invalid position
        rear = -1;   // Initialize to invalid position
        
        cout << "Circular Queue initialized with capacity: " << k << endl;
    }
    
    // 3.1.4.3.3 Enqueue Operation - O(1)
    bool enQueue(int value) {
        if (isFull()) {
            cout << "Enqueue failed: Queue is full (capacity: " << capacity << ")" << endl;
            return false;
        }
        
        if (isEmpty()) {
            // First element insertion
            front = rear = 0;
        } else {
            // Normal insertion with wrap-around
            rear = getNextPosition(rear);
        }
        
        queue[rear] = value;
        currentSize++;
        
        cout << "Enqueued: " << value << " at position " << rear 
             << " (Size: " << currentSize << "/" << capacity << ")" << endl;
        return true;
    }
    
    // 3.1.4.3.4 Dequeue Operation - O(1)
    bool deQueue() {
        if (isEmpty()) {
            cout << "Dequeue failed: Queue is empty" << endl;
            return false;
        }
        
        int removedValue = queue[front];
        
        if (currentSize == 1) {
            // Last element removal - reset to empty state
            front = rear = -1;
        } else {
            // Normal removal with wrap-around
            front = getNextPosition(front);
        }
        
        currentSize--;
        
        cout << "Dequeued: " << removedValue << " from position " << 
                (currentSize == 0 ? -1 : getPreviousPosition(front))
             << " (Size: " << currentSize << "/" << capacity << ")" << endl;
        return true;
    }
    
    // 3.1.4.3.5 Access Operations - O(1)
    int Front() const {
        if (isEmpty()) {
            cout << "Front access failed: Queue is empty" << endl;
            return -1;
        }
        return queue[front];
    }
    
    int Rear() const {
        if (isEmpty()) {
            cout << "Rear access failed: Queue is empty" << endl;
            return -1;
        }
        return queue[rear];
    }
    
    // 3.1.4.3.6 State Check Operations - O(1)
    bool isEmpty() const {
        return currentSize == 0;
    }
    
    bool isFull() const {
        return currentSize == capacity;
    }
    
    int size() const {
        return currentSize;
    }
    
    int getCapacity() const {
        return capacity;
    }
    
    // 3.1.4.3.7 Advanced Utility Functions
    void displayQueue() const {
        if (isEmpty()) {
            cout << "Queue: [] (Empty)" << endl;
            return;
        }
        
        cout << "Queue: [";
        int current = front;
        for (int i = 0; i < currentSize; i++) {
            cout << queue[current];
            if (i < currentSize - 1) cout << ", ";
            current = getNextPosition(current);
        }
        cout << "] (Size: " << currentSize << "/" << capacity << ")" << endl;
    }
    
    void displayInternalState() const {
        cout << "\n=== Internal State ===" << endl;
        cout << "Capacity: " << capacity << endl;
        cout << "Current Size: " << currentSize << endl;
        cout << "Front Index: " << front << endl;
        cout << "Rear Index: " << rear << endl;
        cout << "Is Empty: " << (isEmpty() ? "Yes" : "No") << endl;
        cout << "Is Full: " << (isFull() ? "Yes" : "No") << endl;
        
        cout << "Array State: [";
        for (int i = 0; i < capacity; i++) {
            if (isEmpty()) {
                cout << "_";
            } else if ((front <= rear && i >= front && i <= rear) ||
                      (front > rear && (i >= front || i <= rear))) {
                cout << queue[i];
            } else {
                cout << "_";
            }
            if (i < capacity - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        cout << "Position Info: [";
        for (int i = 0; i < capacity; i++) {
            if (i == front && i == rear && !isEmpty()) {
                cout << "FR";
            } else if (i == front) {
                cout << "F";
            } else if (i == rear) {
                cout << "R";
            } else {
                cout << "_";
            }
            if (i < capacity - 1) cout << ", ";
        }
        cout << "]" << endl;
        cout << "=====================" << endl;
    }
    
    // 3.1.4.3.8 Performance Analysis Methods
    void performanceTest(int operations) {
        cout << "\n=== Performance Test (" << operations << " operations) ===" << endl;
        
        auto start = chrono::high_resolution_clock::now();
        
        // Enqueue operations
        for (int i = 0; i < operations && !isFull(); i++) {
            enQueue(i);
        }
        
        // Mixed enqueue/dequeue operations
        for (int i = 0; i < operations; i++) {
            if (i % 2 == 0 && !isFull()) {
                enQueue(i + operations);
            } else if (!isEmpty()) {
                deQueue();
            }
        }
        
        auto end = chrono::high_resolution_clock::now();
        auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
        
        cout << "Total time: " << duration.count() << " microseconds" << endl;
        cout << "Average time per operation: " << 
                (double)duration.count() / (operations * 2) << " microseconds" << endl;
    }
};

// 3.1.4.4 Comprehensive Testing Suite
void testCircularQueue() {
    cout << "=== Circular Queue Comprehensive Testing ===" << endl;
    
    // Test Case 1: Basic Operations
    cout << "\n--- Test Case 1: Basic Operations ---" << endl;
    CircularQueueOptimal cq(5);
    
    cq.enQueue(10);
    cq.enQueue(20);
    cq.enQueue(30);
    cq.displayQueue();
    cq.displayInternalState();
    
    cout << "Front: " << cq.Front() << endl;
    cout << "Rear: " << cq.Rear() << endl;
    
    // Test Case 2: Wrap-around Behavior
    cout << "\n--- Test Case 2: Wrap-around Behavior ---" << endl;
    cq.enQueue(40);
    cq.enQueue(50);
    cq.displayQueue();
    
    cq.deQueue();
    cq.deQueue();
    cq.displayQueue();
    
    cq.enQueue(60);
    cq.enQueue(70);
    cq.displayQueue();
    cq.displayInternalState();
    
    // Test Case 3: Edge Cases
    cout << "\n--- Test Case 3: Edge Cases ---" << endl;
    CircularQueueOptimal smallQueue(1);
    smallQueue.enQueue(100);
    smallQueue.displayInternalState();
    smallQueue.deQueue();
    smallQueue.displayInternalState();
    
    // Test Case 4: Performance Test
    cout << "\n--- Test Case 4: Performance Test ---" << endl;
    CircularQueueOptimal perfQueue(1000);
    perfQueue.performanceTest(500);
}
```

#### 3.1.4.4 Complexity Analysis of Optimal Approach

**Time Complexity:**
- All operations (`enQueue`, `deQueue`, `Front`, `Rear`, `isEmpty`, `isFull`): O(1)

**Space Complexity:**
- O(k) where k is the queue capacity
- No additional space overhead beyond the array

**Advantages:**
- Constant time complexity for all operations
- Optimal space utilization with wrap-around capability
- No element shifting required
- Predictable memory usage pattern

---

## 3.2 Double-Ended Queue (Deque) Implementation

### 3.2.1 Theoretical Foundation

#### 3.2.1.1 Deque Definition and Properties
A **Double-Ended Queue (Deque)** is a generalized queue data structure that allows insertion and deletion operations at both ends (front and rear). It combines the functionality of both stack and queue data structures.

#### 3.2.1.2 Deque Classification
1. **Input-Restricted Deque**: Insertion allowed at only one end, deletion at both ends
2. **Output-Restricted Deque**: Deletion allowed at only one end, insertion at both ends
3. **Unrestricted Deque**: Both insertion and deletion allowed at both ends

#### 3.2.1.3 Mathematical Operations
For a deque with indices [0, 1, 2, ..., n-1]:
- Front insertion: Elements shift right
- Rear insertion: Direct append
- Front deletion: Elements shift left
- Rear deletion: Direct removal

#### 3.2.1.4 Real-world Applications
- **Undo-Redo Operations**: Software applications (text editors, graphics software)
- **Browser History**: Forward and backward navigation
- **A-Steal Algorithm**: Work-stealing in parallel computing
- **Sliding Window Problems**: Maximum/minimum in sliding windows
- **Palindrome Checking**: Efficient character comparison from both ends

### 3.2.2 Problem Statement and Specification

#### 3.2.2.1 Formal Problem Definition
Design a circular double-ended queue that supports insertion and deletion at both ends with optimal time complexity.

**Required Operations:**
- `MyCircularDeque(k)`: Initialize deque with maximum capacity k
- `insertFront(value)`: Insert element at the front
- `insertLast(value)`: Insert element at the rear
- `deleteFront()`: Delete element from the front
- `deleteLast()`: Delete element from the rear
- `getFront()`: Get front element without removal
- `getRear()`: Get rear element without removal
- `isEmpty()`: Check if deque is empty
- `isFull()`: Check if deque is full

#### 3.2.2.2 Sample Test Cases

**Test Case 1: Comprehensive Operations**
```
Input Sequence:
["MyCircularDeque","insertLast","insertLast","insertFront","insertFront","getRear","isFull","deleteLast","insertFront","getFront"]
[[3],[1],[2],[3],[4],[],[],[],[4],[]]

Expected Output:
[null,true,true,true,false,2,true,true,true,4]

Step-by-step Execution:
1. MyCircularDeque(3) → Initialize with capacity 3
2. insertLast(1) → [1], rear=0, front=0
3. insertLast(2) → [1,2], rear=1, front=0  
4. insertFront(3) → [3,1,2], rear=1, front=2 (wrapped)
5. insertFront(4) → false (deque full)
6. getRear() → 2
7. isFull() → true
8. deleteLast() → [3,1], rear=0, front=2
9. insertFront(4) → [4,3,1], rear=0, front=1
10. getFront() → 4
```

### 3.2.3 Brute Force Approach

#### 3.2.3.1 Dynamic Array Implementation

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

class CircularDequeBruteForce {
private:
    vector<int> data;
    int maxCapacity;
    
public:
    // 3.2.3.1.1 Constructor
    CircularDequeBruteForce(int k) : maxCapacity(k) {
        data.reserve(k);
        cout << "Deque initialized with capacity: " << k << endl;
    }
    
    // 3.2.3.1.2 Front Insertion - O(n)
    bool insertFront(int value) {
        if (data.size() >= maxCapacity) {
            cout << "insertFront(" << value << ") failed: Deque is full" << endl;
            return false;
        }
        
        data.insert(data.begin(), value); // O(n) - shifts all elements
        cout << "insertFront(" << value << ") successful, size: " << data.size() << endl;
        return true;
    }
    
    // 3.2.3.1.3 Rear Insertion - O(1)
    bool insertLast(int value) {
        if (data.size() >= maxCapacity) {
            cout << "insertLast(" << value << ") failed: Deque is full" << endl;
            return false;
        }
        
        data.push_back(value);
        cout << "insertLast(" << value << ") successful, size: " << data.size() << endl;
        return true;
    }
    
    // 3.2.3.1.4 Front Deletion - O(n)
    bool deleteFront() {
        if (data.empty()) {
            cout << "deleteFront() failed: Deque is empty" << endl;
            return false;
        }
        
        int removedValue = data[0];
        data.erase(data.begin()); // O(n) - shifts all elements
        cout << "deleteFront() removed: " << removedValue << ", size: " << data.size() << endl;
        return true;
    }
    
    // 3.2.3.1.5 Rear Deletion - O(1)
    bool deleteLast() {
        if (data.empty()) {
            cout << "deleteLast() failed: Deque is empty" << endl;
            return false;
        }
        
        int removedValue = data.back();
        data.pop_back();
        cout << "deleteLast() removed: " << removedValue << ", size: " << data.size() << endl;
        return true;
    }
    
    // 3.2.3.1.6 Access Operations
    int getFront() {
        if (data.empty()) {
            cout << "getFront() failed: Deque is empty" << endl;
            return -1;
        }
        return data[0];
    }
    
    int getRear() {
        if (data.empty()) {
            cout << "getRear() failed: Deque is empty" << endl;
            return -1;
        }
        return data.back();
    }
    
    // 3.2.3.1.7 State Operations
    bool isEmpty() {
        return data.empty();
    }
    
    bool isFull() {
        return data.size() == maxCapacity;
    }
    
    // 3.2.3.1.8 Utility Functions
    void display() {
        if (data.empty()) {
            cout << "Deque: [] (Empty)" << endl;
            return;
        }
        
        cout << "Deque: [";
        for (size_t i = 0; i < data.size(); i++) {
            cout << data[i];
            if (i < data.size() - 1) cout << ", ";
        }
        cout << "] (Front=" << data[0] << ", Rear=" << data.back() << ")" << endl;
    }
};
```

#### 3.2.3.2 Complexity Analysis of Brute Force

**Time Complexity:**
- `insertFront(value)`: O(n) - requires shifting all elements
- `insertLast(value)`: O(1) - direct append
- `deleteFront()`: O(n) - requires shifting all elements
- `deleteLast()`: O(1) - direct removal
- Access operations: O(1)

**Space Complexity:** O(k) where k is capacity

### 3.2.4 Optimal Circular Array Implementation

#### 3.2.4.1 Advanced Pointer Management

```cpp
#include <iostream>
#include <vector>
#include <stdexcept>
using namespace std;

class MyCircularDeque {
private:
    vector<int> arr;
    int front, rear, currentSize, capacity;
    
    // 3.2.4.1.1 Helper Methods for Pointer Arithmetic
    int getNextPosition(int pos) const {
        return (pos + 1) % capacity;
    }
    
    int getPreviousPosition(int pos) const {
        return (pos - 1 + capacity) % capacity;
    }
    
public:
    // 3.2.4.1.2 Enhanced Constructor
    MyCircularDeque(int k) {
        if (k <= 0) {
            throw invalid_argument("Deque capacity must be positive");
        }
        
        capacity = k;
        arr.resize(k);
        front = 0;
        rear = 0;
        currentSize = 0;
        
        cout << "Circular Deque initialized with capacity: " << k << endl;
    }
    
    // 3.2.4.1.3 Front Insertion - O(1)
    bool insertFront(int value) {
        if (isFull()) {
            cout << "insertFront(" << value << ") failed: Deque is full (capacity: " 
                 << capacity << ")" << endl;
            return false;
        }
        
        if (currentSize == 0) {
            // First element insertion
            arr[front] = value;
        } else {
            // Move front pointer backward and insert
            front = getPreviousPosition(front);
            arr[front] = value;
        }
        
        currentSize++;
        cout << "insertFront(" << value << ") at position " << front 
             << " (Size: " << currentSize << "/" << capacity << ")" << endl;
        return true;
    }
    
    // 3.2.4.1.4 Rear Insertion - O(1)
    bool insertLast(int value) {
        if (isFull()) {
            cout << "insertLast(" << value << ") failed: Deque is full (capacity: " 
                 << capacity << ")" << endl;
            return false;
        }
        
        if (currentSize == 0) {
            // First element insertion
            arr[rear] = value;
        } else {
            // Move rear pointer forward and insert
            rear = getNextPosition(rear);
            arr[rear] = value;
        }
        
        currentSize++;
        cout << "insertLast(" << value << ") at position " << rear 
             << " (Size: " << currentSize << "/" << capacity << ")" << endl;
        return true;
    }
    
    // 3.2.4.1.5 Front Deletion - O(1)
    bool deleteFront() {
        if (isEmpty()) {
            cout << "deleteFront() failed: Deque is empty" << endl;
            return false;
        }
        
        int removedValue = arr[front];
        
        if (currentSize == 1) {
            // Last element removal - reset pointers
            front = rear = 0;
        } else {
            // Move front pointer forward
            front = getNextPosition(front);
        }
        
        currentSize--;
        cout << "deleteFront() removed: " << removedValue 
             << " (Size: " << currentSize << "/" << capacity << ")" << endl;
        return true;
    }
    
    // 3.2.4.1.6 Rear Deletion - O(1)
    bool deleteLast() {
        if (isEmpty()) {
            cout << "deleteLast() failed: Deque is empty" << endl;
            return false;
        }
        
        int removedValue = arr[rear];
        
        if (currentSize == 1) {
            // Last element removal - reset pointers
            front = rear = 0;
        } else {
            // Move rear pointer backward
            rear = getPreviousPosition(rear);
        }
        
        currentSize--;
        cout << "deleteLast() removed: " << removedValue 
             << " (Size: " << currentSize << "/" << capacity << ")" << endl;
        return true;
    }
    
    // 3.2.4.1.7 Access Operations - O(1)
    int getFront() {
        if (isEmpty()) {
            cout << "getFront() failed: Deque is empty" << endl;
            return -1;
        }
        return arr[front];
    }
    
    int getRear() {
        if (isEmpty()) {
            cout << "getRear() failed: Deque is empty" << endl;
            return -1;
        }
        return arr[rear];
    }
    
    // 3.2.4.1.8 State Operations - O(1)
    bool isEmpty() {
        return currentSize == 0;
    }
    
    bool isFull() {
        return currentSize == capacity;
    }
    
    int size() {
        return currentSize;
    }
    
    // 3.2.4.1.9 Advanced Display Functions
    void display() {
        if (isEmpty()) {
            cout << "Deque: [] (Empty)" << endl;
            return;
        }
        
        cout << "Deque: [";
        int current = front;
        for (int i = 0; i < currentSize; i++) {
            cout << arr[current];
            if (i < currentSize - 1) cout << ", ";
            current = getNextPosition(current);
        }
        cout << "] (Front=" << arr[front] << ", Rear=" << arr[rear] 
             << ", Size=" << currentSize << ")" << endl;
    }
    
    void displayInternalStructure() {
        cout << "\n=== Deque Internal Structure ===" << endl;
        cout << "Capacity: " << capacity << endl;
        cout << "Current Size: " << currentSize << endl;
        cout << "Front Index: " << front << endl;
        cout << "Rear Index: " << rear << endl;
        
        cout << "Array: [";
        for (int i = 0; i < capacity; i++) {
            if (isEmpty()) {
                cout << "_";
            } else if (currentSize == 1 && i == front) {
                cout << arr[i];
            } else if (front <= rear && i >= front && i <= rear) {
                cout << arr[i];
            } else if (front > rear && (i >= front || i <= rear)) {
                cout << arr[i];
            } else {
                cout << "_";
            }
            if (i < capacity - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        cout << "Markers: [";
        for (int i = 0; i < capacity; i++) {
            if (isEmpty()) {
                cout << "_";
            } else if (i == front && i == rear) {
                cout << "FR";
            } else if (i == front) {
                cout << "F";
            } else if (i == rear) {
                cout << "R";
            } else {
                cout << "_";
            }
            if (i < capacity - 1) cout << ", ";
        }
        cout << "]" << endl;
        cout << "================================" << endl;
    }
};

// 3.2.4.2 Comprehensive Testing Suite
void testCircularDeque() {
    cout << "=== Circular Deque Comprehensive Testing ===" << endl;
    
    // Test Case 1: Basic Operations
    cout << "\n--- Test Case 1: Basic Operations ---" << endl;
    MyCircularDeque deque(4);
    
    deque.insertLast(10);
    deque.insertLast(20);
    deque.insertFront(5);
    deque.insertFront(1);
    deque.display();
    deque.displayInternalStructure();
    
    // Test Case 2: Deletion Operations
    cout << "\n--- Test Case 2: Deletion Operations ---" << endl;
    deque.deleteFront();
    deque.deleteLast();
    deque.display();
    deque.displayInternalStructure();
    
    // Test Case 3: Wrap-around Behavior
    cout << "\n--- Test Case 3: Wrap-around Behavior ---" << endl;
    deque.insertFront(100);
    deque.insertLast(200);
    deque.display();
    deque.displayInternalStructure();
    
    // Test Case 4: Edge Cases
    cout << "\n--- Test Case 4: Edge Cases ---" << endl;
    MyCircularDeque smallDeque(1);
    smallDeque.insertFront(42);
    smallDeque.displayInternalStructure();
    smallDeque.deleteFront();
    smallDeque.displayInternalStructure();
}
```

---

## 3.3 First Non-Repeating Character in Stream Processing

### 3.3.1 Problem Analysis and Definition

#### 3.3.1.1 Problem Statement
Given a continuous stream of characters, design a data structure that can efficiently find the first non-repeating character at any point in time. The solution should handle real-time character processing with optimal time complexity.

#### 3.3.1.2 Detailed Requirements
- Process characters one by one as they arrive in the stream
- At any moment, return the first character that appears exactly once
- If no non-repeating character exists, return a special marker ('#')
- Handle both lowercase and uppercase letters efficiently
- Support real-time querying without preprocessing the entire stream

#### 3.3.1.3 Sample Test Cases

**Test Case 1: Basic Stream**
```
Input Stream: "aabc"
Expected Output: "a#bb"

Detailed Execution:
- After 'a': frequency={'a':1}, queue=['a'], result='a'
- After 'a': frequency={'a':2}, queue=[], result='#'
- After 'b': frequency={'a':2,'b':1}, queue=['b'], result='b'  
- After 'c': frequency={'a':2,'b':1,'c':1}, queue=['b','c'], result='b'
```

**Test Case 2: Complex Stream**
```
Input Stream: "abccba"
Expected Output: "aabbcc"

Detailed Execution:
- After 'a': frequency={'a':1}, queue=['a'], result='a'
- After 'b': frequency={'a':1,'b':1}, queue=['a','b'], result='a'
- After 'c': frequency={'a':1,'b':1,'c':1}, queue=['a','b','c'], result='a'
- After 'c': frequency={'a':1,'b':1,'c':2}, queue=['a','b'], result='a'
- After 'b': frequency={'a':1,'b':2,'c':2}, queue=['a'], result='a'
- After 'a': frequency={'a':2,'b':2,'c':2}, queue=[], result='#'
```

### 3.3.2 Brute Force Approach

#### 3.3.2.1 Naive String Scanning Method

```cpp
#include <iostream>
#include <string>
#include <unordered_map>
#include <vector>
using namespace std;

class FirstNonRepeatingBruteForce {
private:
    string processedStream;
    
public:
    // 3.3.2.1.1 Constructor
    FirstNonRepeatingBruteForce() {
        processedStream = "";
        cout << "First Non-Repeating Character Tracker (Brute Force) initialized" << endl;
    }
    
    // 3.3.2.1.2 Process Character - O(n²)
    char processCharacter(char ch) {
        // Add character to processed stream
        processedStream += ch;
        
        cout << "Processing character: '" << ch << "'" << endl;
        
        // Count frequency of each character - O(n)
        unordered_map<char, int> frequency;
        for (char c : processedStream) {
            frequency[c]++;
        }
        
        // Find first character with frequency 1 - O(n)
        for (char c : processedStream) {
            if (frequency[c] == 1) {
                cout << "First non-repeating character: '" << c << "'" << endl;
                return c;
            }
        }
        
        cout << "No non-repeating character found: '#'" << endl;
        return '#';
    }
    
    // 3.3.2.1.3 Display Current State
    void displayState() {
        cout << "Processed Stream: \"" << processedStream << "\"" << endl;
        
        // Display frequency count
        unordered_map<char, int> frequency;
        for (char c : processedStream) {
            frequency[c]++;
        }
        
        cout << "Character Frequencies: ";
        for (auto& pair : frequency) {
            cout << "'" << pair.first << "':" << pair.second << " ";
        }
        cout << endl;
    }
    
    // 3.3.2.1.4 Reset Stream
    void reset() {
        processedStream = "";
        cout << "Stream reset" << endl;
    }
};

// 3.3.2.2 Testing Brute Force Approach
void testBruteForceApproach() {
    cout << "=== Testing Brute Force Approach ===" << endl;
    
    FirstNonRepeatingBruteForce tracker;
    string testStream = "aabc";
    string result = "";
    
    cout << "\nProcessing stream: \"" << testStream << "\"" << endl;
    for (char ch : testStream) {
        char nonRepeating = tracker.processCharacter(ch);
        result += nonRepeating;
        tracker.displayState();
        cout << "Current result: \"" << result << "\"" << endl;
        cout << "---" << endl;
    }
    
    cout << "Final result: \"" << result << "\"" << endl;
}
```

#### 3.3.2.2 Complexity Analysis of Brute Force

**Time Complexity:**
- For each character: O(n) for frequency counting + O(n) for finding first non-repeating
- Total for n characters: O(n²)

**Space Complexity:**
- O(n) for storing the processed stream
- O(k) for frequency map where k is unique characters

### 3.3.3 Optimal Queue-Based Approach

#### 3.3.3.1 Algorithm Design
The optimal approach uses a queue to maintain the order of potentially non-repeating characters and a frequency array for O(1) character frequency lookups.

#### 3.3.3.2 Implementation

```cpp
#include <iostream>
#include <queue>
#include <vector>
#include <unordered_map>
#include <chrono>
using namespace std;

class FirstNonRepeatingOptimal {
private:
    queue<char> candidateQueue;    // Stores potential non-repeating characters
    vector<int> frequency;         // Frequency array for a-z (or full ASCII)
    bool useFullASCII;
    unordered_map<char, int> frequencyMap; // For full character set support
    
public:
    // 3.3.3.2.1 Constructor with Character Set Option
    FirstNonRepeatingOptimal(bool fullASCII = false) : useFullASCII(fullASCII) {
        if (!fullASCII) {
            frequency.resize(26, 0); // For lowercase a-z only
            cout << "First Non-Repeating Tracker initialized (lowercase a-z)" << endl;
        } else {
            cout << "First Non-Repeating Tracker initialized (full ASCII)" << endl;
        }
    }
    
    // 3.3.3.2.2 Process Character - O(1) Amortized
    char processCharacter(char ch) {
        cout << "Processing character: '" << ch << "'" << endl;
        
        // Add character to queue for order tracking
        candidateQueue.push(ch);
        
        // Update frequency
        if (useFullASCII) {
            frequencyMap[ch]++;
        } else {
            if (ch >= 'a' && ch <= 'z') {
                frequency[ch - 'a']++;
            } else {
                cout << "Warning: Character '" << ch << "' not in range a-z" << endl;
                return '#';
            }
        }
        
        // Remove characters from front of queue that have frequency > 1
        while (!candidateQueue.empty()) {
            char frontChar = candidateQueue.front();
            int charFreq = useFullASCII ? frequencyMap[frontChar] : 
                          (frontChar >= 'a' && frontChar <= 'z' ? frequency[frontChar - 'a'] : 0);
            
            if (charFreq > 1) {
                candidateQueue.pop();
                cout << "Removed '" << frontChar << "' from candidates (frequency: " << charFreq << ")" << endl;
            } else {
                break; // First character with frequency 1 found
            }
        }
        
        // Return result
        char result = candidateQueue.empty() ? '#' : candidateQueue.front();
        cout << "First non-repeating character: '" << result << "'" << endl;
        return result;
    }
    
    // 3.3.3.2.3 Display Internal State
    void displayState() {
        cout << "\n=== Internal State ===" << endl;
        
        // Display frequency information
        if (useFullASCII) {
            cout << "Character Frequencies (ASCII): ";
            for (auto& pair : frequencyMap) {
                cout << "'" << pair.first << "':" << pair.second << " ";
            }
        } else {
            cout << "Character Frequencies (a-z): ";
            for (int i = 0; i < 26; i++) {
                if (frequency[i] > 0) {
                    cout << "'" << (char)('a' + i) << "':" << frequency[i] << " ";
                }
            }
        }
        cout << endl;
        
        // Display candidate queue
        if (candidateQueue.empty()) {
            cout << "Candidate Queue: []" << endl;
        } else {
            queue<char> tempQueue = candidateQueue;
            cout << "Candidate Queue: [";
            bool first = true;
            while (!tempQueue.empty()) {
                if (!first) cout << ", ";
                cout << "'" << tempQueue.front() << "'";
                tempQueue.pop();
                first = false;
            }
            cout << "]" << endl;
        }
        cout << "==================" << endl;
    }
    
    // 3.3.3.2.4 Reset Tracker
    void reset() {
        while (!candidateQueue.empty()) {
            candidateQueue.pop();
        }
        
        if (useFullASCII) {
            frequencyMap.clear();
        } else {
            fill(frequency.begin(), frequency.end(), 0);
        }
        
        cout << "Tracker reset" << endl;
    }
    
    // 3.3.3.2.5 Performance Analysis
    void analyzePerformance(const string& stream) {
        cout << "\n=== Performance Analysis ===" << endl;
        
        auto start = chrono::high_resolution_clock::now();
        
        string result = "";
        for (char ch : stream) {
            char nonRepeating = processCharacter(ch);
            result += nonRepeating;
        }
        
        auto end = chrono::high_resolution_clock::now();
        auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
        
        cout << "Stream: \"" << stream << "\"" << endl;
        cout << "Result: \"" << result << "\"" << endl;
        cout << "Processing Time: " << duration.count() << " microseconds" << endl;
        cout << "Average per character: " << (double)duration.count() / stream.length() << " microseconds" << endl;
    }
};

// 3.3.3.3 Enhanced Testing Suite
void testOptimalApproach() {
    cout << "\n=== Testing Optimal Approach ===" << endl;
    
    // Test Case 1: Basic functionality
    cout << "\n--- Test Case 1: Basic Stream Processing ---" << endl;
    FirstNonRepeatingOptimal tracker;
    string testStream1 = "aabc";
    
    cout << "Processing stream: \"" << testStream1 << "\"" << endl;
    string result1 = "";
    for (char ch : testStream1) {
        char nonRepeating = tracker.processCharacter(ch);
        result1 += nonRepeating;
        tracker.displayState();
    }
    cout << "Final result: \"" << result1 << "\"" << endl;
    
    // Test Case 2: Complex stream
    cout << "\n--- Test Case 2: Complex Stream ---" << endl;
    tracker.reset();
    string testStream2 = "abccba";
    
    cout << "Processing stream: \"" << testStream2 << "\"" << endl;
    string result2 = "";
    for (char ch : testStream2) {
        char nonRepeating = tracker.processCharacter(ch);
        result2 += nonRepeating;
    }
    cout << "Final result: \"" << result2 << "\"" << endl;
    
    // Test Case 3: Performance comparison
    cout << "\n--- Test Case 3: Performance Analysis ---" << endl;
    FirstNonRepeatingOptimal perfTracker;
    string longStream = "abcdefghijklmnopqrstuvwxyzzyxwvutsrqponmlkjihgfedcba";
    perfTracker.analyzePerformance(longStream);
}
```

#### 3.3.3.3 Complexity Analysis of Optimal Approach

**Time Complexity:**
- `processCharacter()`: O(1) amortized - each character is added and removed from queue at most once
- Total for n characters: O(n)

**Space Complexity:**
- O(26) or O(256) for frequency array (constant space)
- O(k) for queue where k ≤ unique characters in stream
- Total: O(1) + O(k) = O(k)

**Key Advantages:**
- Constant time per character processing
- Efficient memory usage
- Real-time processing capability
- Scalable to large streams

---

## 3.4 Stack Implementation Using Queues

### 3.4.1 Problem Analysis and Design Strategies

#### 3.4.1.1 Problem Statement
Implement a Last In First Out (LIFO) stack using only queue data structures. The implementation should support all standard stack operations while maintaining the stack's LIFO semantics using FIFO queue operations.

#### 3.4.1.2 Design Approaches
1. **Make Push Costly**: Transfer elements during push to maintain stack order
2. **Make Pop Costly**: Transfer elements during pop to access the last element
3. **Single Queue with Rotation**: Use queue rotation to simulate stack behavior

#### 3.4.1.3 Required Operations
- `MyStack()`: Initialize the stack
- `push(x)`: Push element x onto the stack
- `pop()`: Remove and return the top element
- `top()`: Return the top element without removal
- `empty()`: Check if the stack is empty

### 3.4.2 Approach 1: Make Pop Costly (Two Queues)

#### 3.4.2.1 Algorithm Description
- Use two queues: primary (q1) for storage, auxiliary (q2) for operations
- Push: Direct insertion into q1 - O(1)
- Pop: Transfer all elements except the last from q1 to q2, return last element, swap queues - O(n)

#### 3.4.2.2 Implementation

```cpp
#include <iostream>
#include <queue>
#include <chrono>
using namespace std;

class StackUsingQueuePopCostly {
private:
    queue<int> q1, q2;
    int stackSize;
    
public:
    // 3.4.2.2.1 Constructor
    StackUsingQueuePopCostly() : stackSize(0) {
        cout << "Stack using Queues (Pop Costly) initialized" << endl;
    }
    
    // 3.4.2.2.2 Push Operation - O(1)
    void push(int x) {
        q1.push(x);
        stackSize++;
        cout << "Pushed: " << x << " (Stack size: " << stackSize << ")" << endl;
    }
    
    // 3.4.2.2.3 Pop Operation - O(n)
    int pop() {
        if (empty()) {
            cout << "Pop failed: Stack is empty" << endl;
            return -1;
        }
        
        // Transfer all elements except the last to q2
        while (q1.size() > 1) {
            q2.push(q1.front());
            q1.pop();
        }
        
        // Get the last element (top of stack)
        int topElement = q1.front();
        q1.pop();
        stackSize--;
        
        // Swap queues to maintain q1 as primary
        swap(q1, q2);
        
        cout << "Popped: " << topElement << " (Stack size: " << stackSize << ")" << endl;
        return topElement;
    }
    
    // 3.4.2.2.4 Top Operation - O(n)
    int top() {
        if (empty()) {
            cout << "Top failed: Stack is empty" << endl;
            return -1;
        }
        
        // Transfer all elements except the last to q2
        while (q1.size() > 1) {
            q2.push(q1.front());
            q1.pop();
        }
        
        // Get the last element but keep it
        int topElement = q1.front();
        q2.push(q1.front());
        q1.pop();
        
        // Swap queues
        swap(q1, q2);
        
        cout << "Top element: " << topElement << endl;
        return topElement;
    }
    
    // 3.4.2.2.5 Empty Check - O(1)
    bool empty() {
        return stackSize == 0;
    }
    
    // 3.4.2.2.6 Display Stack
    void display() {
        if (empty()) {
            cout << "Stack: [] (Empty)" << endl;
            return;
        }
        
        // Create temporary queue to preserve state
        queue<int> temp = q1;
        vector<int> elements;
        
        while (!temp.empty()) {
            elements.push_back(temp.front());
            temp.pop();
        }
        
        cout << "Stack (top to bottom): [";
        for (int i = elements.size() - 1; i >= 0; i--) {
            cout << elements[i];
            if (i > 0) cout << ", ";
        }
        cout << "]" << endl;
    }
    
    // 3.4.2.2.7 Internal State Display
    void displayInternalState() {
        cout << "\n=== Internal State (Pop Costly) ===" << endl;
        cout << "Stack Size: " << stackSize << endl;
        cout << "Q1 Size: " << q1.size() << endl;
        cout << "Q2 Size: " << q2.size() << endl;
        
        if (!q1.empty()) {
            queue<int> temp = q1;
            cout << "Q1 Contents: [";
            bool first = true;
            while (!temp.empty()) {
                if (!first) cout << ", ";
                cout << temp.front();
                temp.pop();
                first = false;
            }
            cout << "]" << endl;
        } else {
            cout << "Q1 Contents: []" << endl;
        }
        cout << "===============================" << endl;
    }
};
```

### 3.4.3 Approach 2: Make Push Costly (Two Queues)

#### 3.4.3.1 Algorithm Description
- Push: Transfer all elements to auxiliary queue, add new element, transfer back - O(n)
- Pop: Direct removal from primary queue - O(1)

#### 3.4.3.2 Implementation

```cpp
class StackUsingQueuePushCostly {
private:
    queue<int> q1, q2;
    
public:
    // 3.4.3.2.1 Constructor
    StackUsingQueuePushCostly() {
        cout << "Stack using Queues (Push Costly) initialized" << endl;
    }
    
    // 3.4.3.2.2 Push Operation - O(n)
    void push(int x) {
        // Add new element to q2
        q2.push(x);
        
        // Transfer all elements from q1 to q2
        while (!q1.empty()) {
            q2.push(q1.front());
            q1.pop();
        }
        
        // Swap q1 and q2 (q1 becomes the main queue)
        swap(q1, q2);
        
        cout << "Pushed: " << x << " (Stack size: " << q1.size() << ")" << endl;
    }
    
    // 3.4.3.2.3 Pop Operation - O(1)
    int pop() {
        if (empty()) {
            cout << "Pop failed: Stack is empty" << endl;
            return -1;
        }
        
        int topElement = q1.front();
        q1.pop();
        
        cout << "Popped: " << topElement << " (Stack size: " << q1.size() << ")" << endl;
        return topElement;
    }
    
    // 3.4.3.2.4 Top Operation - O(1)
    int top() {
        if (empty()) {
            cout << "Top failed: Stack is empty" << endl;
            return -1;
        }
        
        int topElement = q1.front();
        cout << "Top element: " << topElement << endl;
        return topElement;
    }
    
    // 3.4.3.2.5 Empty Check - O(1)
    bool empty() {
        return q1.empty();
    }
    
    // 3.4.3.2.6 Display and Analysis Methods
    void display() {
        if (empty()) {
            cout << "Stack: [] (Empty)" << endl;
            return;
        }
        
        queue<int> temp = q1;
        cout << "Stack (top to bottom): [";
        bool first = true;
        while (!temp.empty()) {
            if (!first) cout << ", ";
            cout << temp.front();
            temp.pop();
            first = false;
        }
        cout << "]" << endl;
    }
};
```

### 3.4.4 Approach 3: Single Queue with Rotation

#### 3.4.4.1 Algorithm Description
- Use rotation technique: after pushing element, rotate queue to bring new element to front
- Push: Add element, then rotate queue - O(n)
- Pop: Direct removal - O(1)

#### 3.4.4.2 Optimal Single Queue Implementation

```cpp
class StackUsingSingleQueue {
private:
    queue<int> q;
    
public:
    // 3.4.4.2.1 Constructor
    StackUsingSingleQueue() {
        cout << "Stack using Single Queue (Rotation) initialized" << endl;
    }
    
    // 3.4.4.2.2 Push Operation with Rotation - O(n)
    void push(int x) {
        int initialSize = q.size();
        
        // Add new element
        q.push(x);
        
        // Rotate queue to bring new element to front
        for (int i = 0; i < initialSize; i++) {
            q.push(q.front());
            q.pop();
        }
        
        cout << "Pushed: " << x << " with " << initialSize << " rotations (Stack size: " << q.size() << ")" << endl;
    }
    
    // 3.4.4.2.3 Pop Operation - O(1)
    int pop() {
        if (empty()) {
            cout << "Pop failed: Stack is empty" << endl;
            return -1;
        }
        
        int topElement = q.front();
        q.pop();
        
        cout << "Popped: " << topElement << " (Stack size: " << q.size() << ")" << endl;
        return topElement;
    }
    
    // 3.4.4.2.4 Top Operation - O(1)
    int top() {
        if (empty()) {
            cout << "Top failed: Stack is empty" << endl;
            return -1;
        }
        
        cout << "Top element: " << q.front() << endl;
        return q.front();
    }
    
    // 3.4.4.2.5 Empty Check - O(1)
    bool empty() {
        return q.empty();
    }
    
    // 3.4.4.2.6 Advanced Display Functions
    void display() {
        if (empty()) {
            cout << "Stack: [] (Empty)" << endl;
            return;
        }
        
        queue<int> temp = q;
        cout << "Stack (top to bottom): [";
        bool first = true;
        while (!temp.empty()) {
            if (!first) cout << ", ";
            cout << temp.front();
            temp.pop();
            first = false;
        }
        cout << "]" << endl;
    }
    
    void demonstrateRotation(int x) {
        cout << "\n--- Demonstrating Rotation for push(" << x << ") ---" << endl;
        
        // Show queue state before
        cout << "Before push: ";
        display();
        
        int initialSize = q.size();
        cout << "Initial size: " << initialSize << endl;
        
        // Add element
        q.push(x);
        cout << "After adding " << x << ": ";
        
        // Show intermediate state
        queue<int> temp = q;
        cout << "[";
        bool first = true;
        while (!temp.empty()) {
            if (!first) cout << ", ";
            cout << temp.front();
            temp.pop();
            first = false;
        }
        cout << "]" << endl;
        
        // Perform rotations step by step
        for (int i = 0; i < initialSize; i++) {
            int frontElement = q.front();
            q.push(frontElement);
            q.pop();
            
            cout << "Rotation " << (i+1) << ": moved " << frontElement << " to back -> ";
            temp = q;
            cout << "[";
            first = true;
            while (!temp.empty()) {
                if (!first) cout << ", ";
                cout << temp.front();
                temp.pop();
                first = false;
            }
            cout << "]" << endl;
        }
        
        cout << "Final state: ";
        display();
        cout << "----------------------------------------" << endl;
    }
};

// 3.4.4.3 Comprehensive Testing and Comparison
void testStackImplementations() {
    cout << "=== Stack Implementation Comparison ===" << endl;
    
    // Test Case 1: Basic Operations Comparison
    cout << "\n--- Test Case 1: Basic Operations ---" << endl;
    
    StackUsingSingleQueue stack1;
    StackUsingQueuePushCostly stack2;
    StackUsingQueuePopCostly stack3;
    
    cout << "\nTesting Single Queue Approach:" << endl;
    stack1.push(1);
    stack1.push(2);
    stack1.push(3);
    stack1.display();
    stack1.pop();
    stack1.display();
    
    cout << "\nTesting Push Costly Approach:" << endl;
    stack2.push(1);
    stack2.push(2);
    stack2.push(3);