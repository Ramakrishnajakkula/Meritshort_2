# Module 3: Advanced Variations and Extensions of Stacks and Queues

Welcome back, students! In our previous modules, we covered the fundamentals of stacks and queues and explored different implementation approaches. Today, we'll delve into advanced variations and extensions of these data structures, which provide enhanced functionality for solving more complex problems.

## 3.1 Circular Queue

As we saw in the previous module, a standard queue implementation using arrays can lead to inefficient memory usage. Even though we implemented a circular queue there, let's dive deeper into its operations, applications, and advanced implementations.

### Circular Queue Visualization

A circular queue "wraps around" when it reaches the end of the underlying array:

![Circular Queue Visualization](https://www.programiz.com/sites/tutorial2program/files/circular-queue.png)

### Detailed Implementation in C++

```cpp
#include <iostream>
using namespace std;

class CircularQueue {
private:
    int* arr;
    int front, rear;
    int capacity;
    int count; // Current number of elements

public:
    CircularQueue(int size) {
        capacity = size;
        arr = new int[capacity];
        front = 0;
        rear = -1;
        count = 0;
    }
    
    ~CircularQueue() {
        delete[] arr;
    }
    
    // Add element to the queue
    bool enqueue(int value) {
        if (isFull()) {
            return false; // Queue is full
        }
        
        rear = (rear + 1) % capacity;
        arr[rear] = value;
        count++;
        return true;
    }
    
    // Remove element from the queue
    bool dequeue(int& value) {
        if (isEmpty()) {
            return false; // Queue is empty
        }
        
        value = arr[front];
        front = (front + 1) % capacity;
        count--;
        return true;
    }
    
    // Get the front element without removing it
    bool peek(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[front];
        return true;
    }
    
    // Get the rear element without removing it
    bool rear_val(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[rear];
        return true;
    }
    
    // Check if queue is empty
    bool isEmpty() {
        return count == 0;
    }
    
    // Check if queue is full
    bool isFull() {
        return count == capacity;
    }
    
    // Get the current size of the queue
    int size() {
        return count;
    }
    
    // Display all elements in the queue
    void display() {
        if (isEmpty()) {
            cout << "Queue is empty" << endl;
            return;
        }
        
        cout << "Elements: ";
        int i = front;
        int elements = 0;
        
        while (elements < count) {
            cout << arr[i] << " ";
            i = (i + 1) % capacity;
            elements++;
        }
        cout << endl;
    }
};
```

### Applications of Circular Queues

1. **CPU Scheduling**: Operating systems use circular queues for round-robin scheduling.
2. **Traffic Light Control**: Managing traffic signal timings.
3. **Memory Management**: Managing buffers in embedded systems.
4. **Data Streaming**: Managing streaming data where old data can be overwritten.

### Extending Circular Queue: Resizable Circular Queue

One limitation of our basic circular queue is its fixed size. Let's implement a version that can dynamically resize:

```cpp
#include <iostream>
using namespace std;

class ResizableCircularQueue {
private:
    int* arr;
    int front, rear;
    int capacity;
    int count;
    
    // Helper function to resize the queue
    void resize(int newCapacity) {
        int* newArr = new int[newCapacity];
        int j = 0;
        
        // Copy elements from old array to new array
        for (int i = 0; i < count; i++) {
            newArr[i] = arr[(front + i) % capacity];
        }
        
        delete[] arr;
        arr = newArr;
        capacity = newCapacity;
        front = 0;
        rear = count - 1;
    }

public:
    ResizableCircularQueue(int initialSize = 10) {
        capacity = initialSize;
        arr = new int[capacity];
        front = 0;
        rear = -1;
        count = 0;
    }
    
    ~ResizableCircularQueue() {
        delete[] arr;
    }
    
    void enqueue(int value) {
        // Resize if queue is full
        if (isFull()) {
            resize(capacity * 2);
        }
        
        rear = (rear + 1) % capacity;
        arr[rear] = value;
        count++;
    }
    
    bool dequeue(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[front];
        front = (front + 1) % capacity;
        count--;
        
        // Shrink the array if it's too empty
        if (count > 0 && count == capacity / 4) {
            resize(capacity / 2);
        }
        
        return true;
    }
    
    bool peek(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[front];
        return true;
    }
    
    bool isEmpty() {
        return count == 0;
    }
    
    bool isFull() {
        return count == capacity;
    }
    
    int size() {
        return count;
    }
};
```

## 3.2 Double-Ended Queue (Deque)

A deque (pronounced "deck") is a double-ended queue that allows insertion and deletion at both ends.

![Deque Visualization](https://media.geeksforgeeks.org/wp-content/uploads/anod.png)

### Operations on Deque

1. **insertFront()**: Adds an element to the front
2. **insertRear()**: Adds an element to the rear
3. **deleteFront()**: Removes and returns the front element
4. **deleteRear()**: Removes and returns the rear element
5. **getFront()**: Returns the front element without removing it
6. **getRear()**: Returns the rear element without removing it
7. **isEmpty()**: Checks if the deque is empty
8. **isFull()**: Checks if the deque is full

### Array Implementation of Deque

```cpp
#include <iostream>
using namespace std;

class Deque {
private:
    int* arr;
    int front, rear;
    int capacity;
    int count;

public:
    Deque(int size) {
        capacity = size;
        arr = new int[capacity];
        front = -1;
        rear = 0;
        count = 0;
    }
    
    ~Deque() {
        delete[] arr;
    }
    
    bool insertFront(int value) {
        if (isFull()) {
            return false;
        }
        
        if (front == -1) {
            front = 0;
            rear = 0;
        } else if (front == 0) {
            front = capacity - 1;
        } else {
            front--;
        }
        
        arr[front] = value;
        count++;
        return true;
    }
    
    bool insertRear(int value) {
        if (isFull()) {
            return false;
        }
        
        if (front == -1) {
            front = 0;
            rear = 0;
        } else if (rear == capacity - 1) {
            rear = 0;
        } else {
            rear++;
        }
        
        arr[rear] = value;
        count++;
        return true;
    }
    
    bool deleteFront(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[front];
        
        if (front == rear) {
            front = -1;
            rear = -1;
        } else if (front == capacity - 1) {
            front = 0;
        } else {
            front++;
        }
        
        count--;
        return true;
    }
    
    bool deleteRear(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[rear];
        
        if (front == rear) {
            front = -1;
            rear = -1;
        } else if (rear == 0) {
            rear = capacity - 1;
        } else {
            rear--;
        }
        
        count--;
        return true;
    }
    
    bool getFront(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[front];
        return true;
    }
    
    bool getRear(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = arr[rear];
        return true;
    }
    
    bool isEmpty() {
        return front == -1;
    }
    
    bool isFull() {
        return ((front == 0 && rear == capacity - 1) || front == rear + 1);
    }
    
    int size() {
        return count;
    }
    
    void display() {
        if (isEmpty()) {
            cout << "Deque is empty" << endl;
            return;
        }
        
        cout << "Elements: ";
        int i = front;
        
        if (front <= rear) {
            while (i <= rear) {
                cout << arr[i] << " ";
                i++;
            }
        } else {
            while (i < capacity) {
                cout << arr[i] << " ";
                i++;
            }
            i = 0;
            while (i <= rear) {
                cout << arr[i] << " ";
                i++;
            }
        }
        
        cout << endl;
    }
};
```

### Linked List Implementation of Deque

For a more flexible implementation that doesn't have a fixed size, we can use a doubly linked list:

```cpp
#include <iostream>
using namespace std;

class DequeNode {
public:
    int data;
    DequeNode* prev;
    DequeNode* next;
    
    DequeNode(int value) {
        data = value;
        prev = nullptr;
        next = nullptr;
    }
};

class LinkedListDeque {
private:
    DequeNode* front;
    DequeNode* rear;
    int count;

public:
    LinkedListDeque() {
        front = nullptr;
        rear = nullptr;
        count = 0;
    }
    
    ~LinkedListDeque() {
        while (!isEmpty()) {
            int dummy;
            deleteFront(dummy);
        }
    }
    
    void insertFront(int value) {
        DequeNode* newNode = new DequeNode(value);
        
        if (isEmpty()) {
            front = rear = newNode;
        } else {
            newNode->next = front;
            front->prev = newNode;
            front = newNode;
        }
        
        count++;
    }
    
    void insertRear(int value) {
        DequeNode* newNode = new DequeNode(value);
        
        if (isEmpty()) {
            front = rear = newNode;
        } else {
            newNode->prev = rear;
            rear->next = newNode;
            rear = newNode;
        }
        
        count++;
    }
    
    bool deleteFront(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        DequeNode* temp = front;
        value = temp->data;
        
        if (front == rear) {
            front = rear = nullptr;
        } else {
            front = front->next;
            front->prev = nullptr;
        }
        
        delete temp;
        count--;
        return true;
    }
    
    bool deleteRear(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        DequeNode* temp = rear;
        value = temp->data;
        
        if (front == rear) {
            front = rear = nullptr;
        } else {
            rear = rear->prev;
            rear->next = nullptr;
        }
        
        delete temp;
        count--;
        return true;
    }
    
    bool getFront(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = front->data;
        return true;
    }
    
    bool getRear(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = rear->data;
        return true;
    }
    
    bool isEmpty() {
        return front == nullptr;
    }
    
    int size() {
        return count;
    }
    
    void display() {
        if (isEmpty()) {
            cout << "Deque is empty" << endl;
            return;
        }
        
        cout << "Elements: ";
        DequeNode* current = front;
        
        while (current != nullptr) {
            cout << current->data << " ";
            current = current->next;
        }
        
        cout << endl;
    }
};
```

### Applications of Deque

1. **Undo operations** in text editors
2. **Browser history** (forward and backward navigation)
3. **Work stealing algorithms** in parallel processing
4. **A-Steal scheduling algorithm**
5. **Implementing other data structures** like stacks and queues

## 3.3 Priority Queue

A priority queue is a type of queue where elements have associated priorities and are served according to their priority rather than their arrival order.

![Priority Queue Visualization](https://media.geeksforgeeks.org/wp-content/cdn-uploads/Priority-Queue-min-1024x512.png)

### Key Characteristics

1. Elements have priorities associated with them
2. Higher priority elements are dequeued before lower priority ones
3. If elements have the same priority, they are served according to their order in the queue

### Implementation Approaches

There are several ways to implement a priority queue:

1. **Array-based**: Simple but inefficient for large datasets (O(n) insertion or deletion)
2. **Linked list-based**: Elements are kept in sorted order by priority
3. **Heap-based**: Most efficient implementation (O(log n) operations)

### Binary Heap Implementation

The most common and efficient implementation uses a binary heap:

```cpp
#include <iostream>
#include <vector>
using namespace std;

class PriorityQueue {
private:
    // We'll use a min-heap where smaller values have higher priority
    vector<int> heap;
    
    // Helper methods for maintaining heap property
    int parent(int i) { return (i - 1) / 2; }
    int leftChild(int i) { return (2 * i) + 1; }
    int rightChild(int i) { return (2 * i) + 2; }
    
    // Move element up to its correct position
    void heapifyUp(int index) {
        // If we reach the root, we're done
        if (index == 0) return;
        
        int p = parent(index);
        // If parent has higher value (lower priority), swap and continue
        if (heap[p] > heap[index]) {
            swap(heap[p], heap[index]);
            heapifyUp(p);
        }
    }
    
    // Move element down to its correct position
    void heapifyDown(int index) {
        int smallest = index;
        int left = leftChild(index);
        int right = rightChild(index);
        
        // Find the smallest value among parent and children
        if (left < heap.size() && heap[left] < heap[smallest]) {
            smallest = left;
        }
        
        if (right < heap.size() && heap[right] < heap[smallest]) {
            smallest = right;
        }
        
        // If the smallest isn't the parent, swap and continue
        if (smallest != index) {
            swap(heap[index], heap[smallest]);
            heapifyDown(smallest);
        }
    }

public:
    PriorityQueue() {}
    
    // Add element with given priority
    void enqueue(int value) {
        // Add to the end of the vector
        heap.push_back(value);
        // Fix the heap property
        heapifyUp(heap.size() - 1);
    }
    
    // Remove and return the highest priority element
    bool dequeue(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        // The root contains the highest priority element
        value = heap[0];
        
        // Move the last element to the root and remove the last position
        heap[0] = heap.back();
        heap.pop_back();
        
        // If the heap isn't empty, fix the heap property
        if (!heap.empty()) {
            heapifyDown(0);
        }
        
        return true;
    }
    
    // Get the highest priority element without removing it
    bool peek(int& value) {
        if (isEmpty()) {
            return false;
        }
        
        value = heap[0];
        return true;
    }
    
    bool isEmpty() {
        return heap.empty();
    }
    
    int size() {
        return heap.size();
    }
    
    void display() {
        if (isEmpty()) {
            cout << "Priority queue is empty" << endl;
            return;
        }
        
        cout << "Elements (in heap order): ";
        for (int value : heap) {
            cout << value << " ";
        }
        cout << endl;
    }
};
```

### Using C++ Standard Library Priority Queue

C++ provides a built-in priority queue implementation in the Standard Template Library:

```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    // By default, std::priority_queue is a max-heap
    // (larger values have higher priority)
    priority_queue<int> pq;
    
    // Insert elements
    pq.push(30);
    pq.push(10);
    pq.push(50);
    pq.push(20);
    
    cout << "Priority Queue elements (in order of removal): ";
    while (!pq.empty()) {
        cout << pq.top() << " "; // 50, 30, 20, 10
        pq.pop();
    }
    cout << endl;
    
    // For a min-heap (smaller values have higher priority)
    priority_queue<int, vector<int>, greater<int>> min_pq;
    
    min_pq.push(30);
    min_pq.push(10);
    min_pq.push(50);
    min_pq.push(20);
    
    cout << "Min Priority Queue elements (in order of removal): ";
    while (!min_pq.empty()) {
        cout << min_pq.top() << " "; // 10, 20, 30, 50
        min_pq.pop();
    }
    cout << endl;
    
    return 0;
}
```

### Custom Comparator for Priority Queue

We can also use a custom comparator to define our own priority logic:

```cpp
#include <iostream>
#include <queue>
#include <string>
using namespace std;

struct Person {
    string name;
    int age;
    
    Person(string n, int a) : name(n), age(a) {}
};

// Custom comparator to prioritize by age (younger people first)
struct ComparePersonByAge {
    bool operator()(const Person& p1, const Person& p2) {
        // In a priority queue, elements are popped in "highest priority" first
        // For a min heap, we return true if first has lower priority than second
        return p1.age > p2.age; // Younger people have higher priority
    }
};

int main() {
    priority_queue<Person, vector<Person>, ComparePersonByAge> person_queue;
    
    person_queue.push(Person("Alice", 30));
    person_queue.push(Person("Bob", 25));
    person_queue.push(Person("Charlie", 40));
    person_queue.push(Person("David", 20));
    
    cout << "People in order of age (youngest first):" << endl;
    while (!person_queue.empty()) {
        Person p = person_queue.top();
        cout << p.name << " - " << p.age << " years old" << endl;
        person_queue.pop();
    }
    
    return 0;
}
```

## Practical Problems

Now let's solve some problems using these advanced data structures.

### Problem 1: Sliding Window Maximum

**Problem Link**: [LeetCode 239 - Sliding Window Maximum](https://leetcode.com/problems/sliding-window-maximum/)

**Problem Statement**:
You are given an array of integers `nums`, there is a sliding window of size `k` which is moving from the very left of the array to the very right. You can only see the `k` numbers in the window. Each time the sliding window moves right by one position.

Return the max sliding window.

**Sample Test Cases**:
```
Input: nums = [1,3,-1,-3,5,3,6,7], k = 3
Output: [3,3,5,5,6,7]
Explanation: 
Window position                Max
---------------               -----
[1  3  -1] -3  5  3  6  7       3
 1 [3  -1  -3] 5  3  6  7       3
 1  3 [-1  -3  5] 3  6  7       5
 1  3  -1 [-3  5  3] 6  7       5
 1  3  -1  -3 [5  3  6] 7       6
 1  3  -1  -3  5 [3  6  7]      7
```

**Approach 1: Brute Force**

For each window position, find the maximum element in the window:

```cpp
vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    vector<int> result;
    int n = nums.size();
    
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

**Time Complexity**: O(n * k) where n is the array length and k is the window size
**Space Complexity**: O(1) excluding the output array

**Approach 2: Using Deque (Optimal)**

We can use a deque to efficiently track potential maximum values in the current window:

```cpp
#include <iostream>
#include <vector>
#include <deque>
using namespace std;

vector<int> maxSlidingWindow(vector<int>& nums, int k) {
    vector<int> result;
    deque<int> dq; // Will store indices, not values
    
    for (int i = 0; i < nums.size(); i++) {
        // Remove elements outside the current window
        if (!dq.empty() && dq.front() == i - k) {
            dq.pop_front();
        }
        
        // Remove all elements smaller than the current element from the back
        while (!dq.empty() && nums[dq.back()] < nums[i]) {
            dq.pop_back();
        }
        
        // Add the current element's index
        dq.push_back(i);
        
        // If we have processed at least k elements, add the maximum to the result
        if (i >= k - 1) {
            result.push_back(nums[dq.front()]);
        }
    }
    
    return result;
}

int main() {
    vector<int> nums = {1, 3, -1, -3, 5, 3, 6, 7};
    int k = 3;
    
    vector<int> result = maxSlidingWindow(nums, k);
    
    cout << "Maximum in each sliding window: ";
    for (int val : result) {
        cout << val << " ";
    }
    cout << endl;
    
    return 0;
}
```

**Time Complexity**: O(n) where n is the array length
**Space Complexity**: O(k) for the deque

**Explanation**:

This approach uses a deque to maintain indices of elements that are:
1. Within the current window
2. Potential maximum values for future windows

For each new element:
1. We remove elements that are outside the current window (indices < i-k+1)
2. We remove elements that are smaller than the current element (since they can never be the maximum)
3. We add the current element's index
4. We add the maximum element (front of the deque) to our result

This approach is optimal because:
1. Each element is pushed and popped at most once
2. We only keep potential maximums, reducing the work needed
3. The front of the deque always contains the maximum of the current window

### Problem 2: Design a Data Structure with Min/Max Stack Operations

**Problem Statement**:
Design a data structure that supports all the following operations in O(1) time:
- `push(x)`: Push element x onto the stack.
- `pop()`: Remove the element on top of the stack.
- `top()`: Get the top element.
- `getMin()`: Retrieve the minimum element in the stack.
- `getMax()`: Retrieve the maximum element in the stack.

**Sample Test Cases**:
```
Input:
["MinMaxStack", "push", "push", "push", "getMin", "getMax", "top", "pop", "getMin", "getMax"]
[[], [3], [5], [2], [], [], [], [], [], []]

Output:
[null, null, null, null, 2, 5, 2, null, 3, 5]

Explanation:
MinMaxStack stack = new MinMaxStack();
stack.push(3);   // stack: [3]
stack.push(5);   // stack: [3, 5]
stack.push(2);   // stack: [3, 5, 2]
stack.getMin();  // return 2
stack.getMax();  // return 5
stack.top();     // return 2
stack.pop();     // stack: [3, 5]
stack.getMin();  // return 3
stack.getMax();  // return 5
```

**Approach: Using Additional Stacks**

We'll maintain three stacks:
1. A main stack for regular push/pop operations
2. A minStack that keeps track of the minimum at each state
3. A maxStack that keeps track of the maximum at each state

```cpp
#include <iostream>
#include <stack>
using namespace std;

class MinMaxStack {
private:
    stack<int> mainStack; // For regular push/pop operations
    stack<int> minStack;  // Tracks the minimum element at each state
    stack<int> maxStack;  // Tracks the maximum element at each state

public:
    MinMaxStack() {
        // Initialize stacks
    }
    
    void push(int x) {
        mainStack.push(x);
        
        // Update minimum
        if (minStack.empty() || x <= minStack.top()) {
            minStack.push(x);
        } else {
            minStack.push(minStack.top());
        }
        
        // Update maximum
        if (maxStack.empty() || x >= maxStack.top()) {
            maxStack.push(x);
        } else {
            maxStack.push(maxStack.top());
        }
    }
    
    void pop() {
        if (!mainStack.empty()) {
            mainStack.pop();
            minStack.pop();
            maxStack.pop();
        }
    }
    
    int top() {
        if (mainStack.empty()) {
            return -1; // Error value for empty stack
        }
        return mainStack.top();
    }
    
    int getMin() {
        if (minStack.empty()) {
            return -1; // Error value for empty stack
        }
        return minStack.top();
    }
    
    int getMax() {
        if (maxStack.empty()) {
            return -1; // Error value for empty stack
        }
        return maxStack.top();
    }
};

int main() {
    MinMaxStack stack;
    
    stack.push(3);
    stack.push(5);
    stack.push(2);
    
    cout << "Min: " << stack.getMin() << endl;  // 2
    cout << "Max: " << stack.getMax() << endl;  // 5
    cout << "Top: " << stack.top() << endl;     // 2
    
    stack.pop();
    
    cout << "After pop - Min: " << stack.getMin() << endl;  // 3
    cout << "After pop - Max: " << stack.getMax() << endl;  // 5
    
    return 0;
}
```

**Time Complexity**: O(1) for all operations (push, pop, top, getMin, getMax)
**Space Complexity**: O(n) where n is the number of elements in the stack

**Explanation**:

The key insight in this approach is to maintain auxiliary stacks that keep track of the minimum and maximum at each state of the main stack:

1. When we push an element, we also push the current minimum and maximum to the respective auxiliary stacks
2. When we pop an element, we also pop from the auxiliary stacks
3. The tops of the auxiliary stacks always represent the current minimum and maximum values in the main stack

This allows us to perform all operations in O(1) time. The trade-off is additional space usage.

## Summary

In this module, we've explored advanced variations and extensions of basic stacks and queues:

1. **Circular Queue**:
   - Efficient use of array space by wrapping around
   - Applications in scheduling and buffering
   - Can be extended to support dynamic resizing

2. **Double-Ended Queue (Deque)**:
   - Allows insertion and deletion at both ends
   - Implemented using arrays or linked lists
   - Useful for algorithms requiring bidirectional access

3. **Priority Queue**:
   - Elements are served based on priority rather than arrival order
   - Most efficiently implemented using binary heaps
   - Built-in support in C++ STL with customizable priorities

We've also solved practical problems that demonstrate the power of these data structures:
- Using deques for the sliding window maximum problem
- Creating a custom stack with constant-time minimum and maximum operations

These advanced data structures extend the capabilities of basic stacks and queues and are essential tools for solving complex algorithmic problems efficiently.

In our next module, we'll explore advanced stack applications including expression evaluation, syntax checking, and implementing other data structures using stacks.