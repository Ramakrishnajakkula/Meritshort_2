# Module 2: Implementation Approaches for Stacks and Queues

Welcome back, students! In our previous module, we introduced the fundamental concepts of stacks and queues. Today, we'll explore different ways to implement these data structures, comparing their advantages and limitations.

## 2.1 Array-Based Implementations

### Stack Implementation Using Arrays

Array-based stack implementation is straightforward and memory-efficient. We maintain a single pointer (`top`) that keeps track of the top element in the stack.

![Array Stack Visualization](https://cdn.programiz.com/sites/tutorial2program/files/stack-operations.png)

#### Implementation in C++

```cpp
#include <iostream>
using namespace std;

class ArrayStack {
private:
    int* arr;
    int top;
    int capacity;

public:
    ArrayStack(int size) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }
    
    ~ArrayStack() {
        delete[] arr;
    }
    
    void push(int value) {
        if (isFull()) {
            cout << "Stack Overflow\n";
            return;
        }
        arr[++top] = value;
    }
    
    int pop() {
        if (isEmpty()) {
            cout << "Stack Underflow\n";
            return -1;
        }
        return arr[top--];
    }
    
    int peek() {
        if (isEmpty()) {
            cout << "Stack is empty\n";
            return -1;
        }
        return arr[top];
    }
    
    bool isEmpty() {
        return top == -1;
    }
    
    bool isFull() {
        return top == capacity - 1;
    }
    
    int size() {
        return top + 1;
    }
};
```

#### Advantages of Array-Based Stacks
- Simple implementation
- Memory efficient (no overhead for pointers)
- Good cache locality
- O(1) time complexity for all operations

#### Limitations
- Fixed size (can cause stack overflow)
- May waste memory if allocated size is much larger than needed

### Queue Implementation Using Arrays

For queues, we need to keep track of both the front and rear ends. A simple array implementation can lead to "false overflow" situations, where the rear pointer reaches the end while there's still space at the beginning after dequeuing operations.

#### Simple Array Implementation (Inefficient)

```cpp
#include <iostream>
using namespace std;

class SimpleArrayQueue {
private:
    int* arr;
    int front, rear;
    int capacity;

public:
    SimpleArrayQueue(int size) {
        arr = new int[size];
        capacity = size;
        front = 0;
        rear = -1;
    }
    
    ~SimpleArrayQueue() {
        delete[] arr;
    }
    
    void enqueue(int value) {
        if (isFull()) {
            cout << "Queue Overflow\n";
            return;
        }
        arr[++rear] = value;
    }
    
    int dequeue() {
        if (isEmpty()) {
            cout << "Queue Underflow\n";
            return -1;
        }
        int value = arr[front++];
        return value;
    }
    
    int peek() {
        if (isEmpty()) {
            cout << "Queue is empty\n";
            return -1;
        }
        return arr[front];
    }
    
    bool isEmpty() {
        return front > rear;
    }
    
    bool isFull() {
        return rear == capacity - 1;
    }
};
```

The above implementation is inefficient because once `rear` reaches the end of the array, the queue is considered full even if elements have been dequeued from the front. This leads to wasted space.

#### Circular Queue Implementation (Efficient)

A circular queue solves the "false overflow" problem by wrapping around to the beginning of the array when the end is reached.

![Circular Queue Visualization](https://upload.wikimedia.org/wikipedia/commons/thumb/f/fd/Circular_Buffer_Animation.gif/400px-Circular_Buffer_Animation.gif)

```cpp
#include <iostream>
using namespace std;

class CircularQueue {
private:
    int* arr;
    int front, rear;
    int capacity;
    int size;

public:
    CircularQueue(int capacity) {
        this->capacity = capacity;
        arr = new int[capacity];
        front = 0;
        rear = -1;
        size = 0;
    }
    
    ~CircularQueue() {
        delete[] arr;
    }
    
    void enqueue(int value) {
        if (isFull()) {
            cout << "Queue Overflow\n";
            return;
        }
        rear = (rear + 1) % capacity;
        arr[rear] = value;
        size++;
    }
    
    int dequeue() {
        if (isEmpty()) {
            cout << "Queue Underflow\n";
            return -1;
        }
        int value = arr[front];
        front = (front + 1) % capacity;
        size--;
        return value;
    }
    
    int peek() {
        if (isEmpty()) {
            cout << "Queue is empty\n";
            return -1;
        }
        return arr[front];
    }
    
    bool isEmpty() {
        return size == 0;
    }
    
    bool isFull() {
        return size == capacity;
    }
    
    int getSize() {
        return size;
    }
};
```

#### Advantages of Circular Queue
- Efficient use of space
- O(1) time complexity for all operations
- No wasted space due to wrapping around

#### Limitations
- Fixed size
- Slightly more complex implementation

## 2.2 Linked List-Based Implementations

Linked lists offer a dynamic approach to implementing stacks and queues, eliminating the size constraints of array-based implementations.

### Stack Implementation Using Linked List

In a linked list implementation of a stack, we use the head of the linked list as the top of the stack. New elements are added to the head, and elements are removed from the head.

![Linked List Stack Visualization](https://media.geeksforgeeks.org/wp-content/uploads/20220714004311/Stack-660x566.png)

```cpp
#include <iostream>
using namespace std;

class Node {
public:
    int data;
    Node* next;
    
    Node(int value) {
        data = value;
        next = nullptr;
    }
};

class LinkedListStack {
private:
    Node* top;
    int stackSize;
    
public:
    LinkedListStack() {
        top = nullptr;
        stackSize = 0;
    }
    
    ~LinkedListStack() {
        while (!isEmpty()) {
            pop();
        }
    }
    
    void push(int value) {
        Node* newNode = new Node(value);
        newNode->next = top;
        top = newNode;
        stackSize++;
    }
    
    int pop() {
        if (isEmpty()) {
            cout << "Stack Underflow\n";
            return -1;
        }
        
        Node* temp = top;
        int value = temp->data;
        top = top->next;
        delete temp;
        stackSize--;
        return value;
    }
    
    int peek() {
        if (isEmpty()) {
            cout << "Stack is empty\n";
            return -1;
        }
        return top->data;
    }
    
    bool isEmpty() {
        return top == nullptr;
    }
    
    int size() {
        return stackSize;
    }
};
```

### Queue Implementation Using Linked List

For a queue implementation, we maintain pointers to both the front and rear of the linked list. Elements are enqueued at the rear and dequeued from the front.

![Linked List Queue Visualization](https://media.geeksforgeeks.org/wp-content/uploads/20220816162225/Queue-660x431.png)

```cpp
#include <iostream>
using namespace std;

class Node {
public:
    int data;
    Node* next;
    
    Node(int value) {
        data = value;
        next = nullptr;
    }
};

class LinkedListQueue {
private:
    Node* front;
    Node* rear;
    int queueSize;
    
public:
    LinkedListQueue() {
        front = nullptr;
        rear = nullptr;
        queueSize = 0;
    }
    
    ~LinkedListQueue() {
        while (!isEmpty()) {
            dequeue();
        }
    }
    
    void enqueue(int value) {
        Node* newNode = new Node(value);
        
        if (isEmpty()) {
            front = rear = newNode;
        } else {
            rear->next = newNode;
            rear = newNode;
        }
        
        queueSize++;
    }
    
    int dequeue() {
        if (isEmpty()) {
            cout << "Queue Underflow\n";
            return -1;
        }
        
        Node* temp = front;
        int value = temp->data;
        
        front = front->next;
        
        // If front becomes NULL, set rear as NULL too
        if (front == nullptr) {
            rear = nullptr;
        }
        
        delete temp;
        queueSize--;
        return value;
    }
    
    int peek() {
        if (isEmpty()) {
            cout << "Queue is empty\n";
            return -1;
        }
        return front->data;
    }
    
    bool isEmpty() {
        return front == nullptr;
    }
    
    int size() {
        return queueSize;
    }
};
```

#### Advantages of Linked List Implementations
- Dynamic size (grows and shrinks as needed)
- No overflow issues (limited only by system memory)
- Efficient memory utilization for varying sizes

#### Limitations
- Extra memory overhead for storing pointers
- No random access (cannot access middle elements efficiently)
- Slightly slower due to memory allocation/deallocation and pointer chasing

## 2.3 Standard Library Implementations

C++ provides built-in implementations for stacks and queues through the Standard Template Library (STL).

### Stack in C++ STL

The C++ STL provides a `stack` container adapter that is implemented on top of other containers (by default, it uses `deque`).

```cpp
#include <iostream>
#include <stack>
using namespace std;

int main() {
    // Create a stack of integers
    stack<int> s;
    
    // Push elements
    s.push(10);
    s.push(20);
    s.push(30);
    
    cout << "Stack size: " << s.size() << endl;
    cout << "Top element: " << s.top() << endl;
    
    // Pop elements
    while (!s.empty()) {
        cout << "Popped: " << s.top() << endl;
        s.pop();
    }
    
    return 0;
}
```

### Queue in C++ STL

The C++ STL provides a `queue` container adapter.

```cpp
#include <iostream>
#include <queue>
using namespace std;

int main() {
    // Create a queue of integers
    queue<int> q;
    
    // Enqueue elements
    q.push(10);
    q.push(20);
    q.push(30);
    
    cout << "Queue size: " << q.size() << endl;
    cout << "Front element: " << q.front() << endl;
    cout << "Back element: " << q.back() << endl;
    
    // Dequeue elements
    while (!q.empty()) {
        cout << "Dequeued: " << q.front() << endl;
        q.pop();
    }
    
    return 0;
}
```

### Deque in C++ STL

A deque (double-ended queue) allows insertion and deletion at both ends.

```cpp
#include <iostream>
#include <deque>
using namespace std;

int main() {
    // Create a deque of integers
    deque<int> dq;
    
    // Insert at front and back
    dq.push_front(10);
    dq.push_back(20);
    dq.push_front(5);
    dq.push_back(30);
    
    cout << "Deque elements: ";
    for (int num : dq) {
        cout << num << " ";
    }
    cout << endl;
    
    cout << "Front element: " << dq.front() << endl;
    cout << "Back element: " << dq.back() << endl;
    
    // Remove from front and back
    dq.pop_front();
    dq.pop_back();
    
    cout << "After pop_front and pop_back: ";
    for (int num : dq) {
        cout << num << " ";
    }
    cout << endl;
    
    return 0;
}
```

#### Advantages of Using STL Implementations
- Well-tested and optimized
- Type-safe
- Consistent interface with other STL containers
- Supports different underlying container types
- No need to manage memory manually

## Practical Problems

Let's solve some problems related to the implementation approaches we've learned.

### Problem 1: Design a Stack with Increment Operation

**Problem Link**: [LeetCode 1381 - Design a Stack With Increment Operation](https://leetcode.com/problems/design-a-stack-with-increment-operation/)

**Problem Statement**:
Design a stack which supports the following operations:

- `CustomStack(int maxSize)`: Initializes the object with maxSize which is the maximum number of elements in the stack.
- `void push(int x)`: Adds x to the top of the stack if the stack hasn't reached the maxSize.
- `int pop()`: Removes and returns the top of the stack or -1 if the stack is empty.
- `void increment(int k, int val)`: Increments the bottom k elements of the stack by val. If there are less than k elements in the stack, just increment all the elements in the stack.

**Sample Test Cases**:
```
Input:
["CustomStack","push","push","pop","push","push","push","increment","increment","pop","pop","pop","pop"]
[[3],[1],[2],[],[2],[3],[4],[5,100],[2,100],[],[],[],[]]
Output:
[null,null,null,2,null,null,null,null,null,103,202,201,-1]

Explanation:
CustomStack customStack = new CustomStack(3); // Stack is Empty []
customStack.push(1);                          // stack becomes [1]
customStack.push(2);                          // stack becomes [1, 2]
customStack.pop();                            // return 2 --> Return top of the stack 2, stack becomes [1]
customStack.push(2);                          // stack becomes [1, 2]
customStack.push(3);                          // stack becomes [1, 2, 3]
customStack.push(4);                          // stack still [1, 2, 3], Don't add another elements as size is 3
customStack.increment(5, 100);                // stack becomes [101, 102, 103]
customStack.increment(2, 100);                // stack becomes [201, 202, 103]
customStack.pop();                            // return 103 --> Return top of the stack 103, stack becomes [201, 202]
customStack.pop();                            // return 202 --> Return top of the stack 202, stack becomes [201]
customStack.pop();                            // return 201 --> Return top of the stack 201, stack becomes []
customStack.pop();                            // return -1 --> Stack is empty return -1
```

**Approach 1: Brute Force with Array**

The naive approach is to use an array-based stack and perform the increment operation by iterating through the array.

```cpp
class CustomStack {
private:
    vector<int> stack;
    int maxSize;
    
public:
    CustomStack(int maxSize) {
        this->maxSize = maxSize;
    }
    
    void push(int x) {
        if (stack.size() < maxSize) {
            stack.push_back(x);
        }
    }
    
    int pop() {
        if (stack.empty()) {
            return -1;
        }
        int top = stack.back();
        stack.pop_back();
        return top;
    }
    
    void increment(int k, int val) {
        int n = min(k, (int)stack.size());
        for (int i = 0; i < n; i++) {
            stack[i] += val;
        }
    }
};
```

**Time Complexity**:
- push: O(1)
- pop: O(1)
- increment: O(k) where k is the number of elements to increment

**Space Complexity**: O(maxSize)

**Approach 2: Optimal with Lazy Increment**

We can optimize the increment operation by using a lazy approach. Instead of incrementing all elements immediately, we keep track of increments that need to be applied and only apply them when popping elements.

```cpp
class CustomStack {
private:
    vector<int> stack;
    vector<int> inc;
    int maxSize;
    
public:
    CustomStack(int maxSize) {
        this->maxSize = maxSize;
    }
    
    void push(int x) {
        if (stack.size() < maxSize) {
            stack.push_back(x);
            inc.push_back(0);
        }
    }
    
    int pop() {
        if (stack.empty()) {
            return -1;
        }
        
        int i = stack.size() - 1;
        if (i > 0) {
            inc[i-1] += inc[i];
        }
        
        int result = stack[i] + inc[i];
        stack.pop_back();
        inc.pop_back();
        return result;
    }
    
    void increment(int k, int val) {
        int i = min(k, (int)stack.size()) - 1;
        if (i >= 0) {
            inc[i] += val;
        }
    }
};
```

**Time Complexity**:
- push: O(1)
- pop: O(1)
- increment: O(1)

**Space Complexity**: O(maxSize)

**Explanation**:
In the optimal approach, we maintain an additional array `inc` to track increments. When we call `increment(k, val)`, we only update the increment value for the k-th element (or the last element if k exceeds the stack size). When popping, we apply the increment and propagate any remaining increment to the element below it. This way, each element accumulates all the increments that should apply to it.

### Problem 2: Implement a Queue using Fixed Size Arrays

**Problem Statement**:
Implement a queue data structure that uses a fixed number of arrays, each of size 'k', to store elements. The queue should support the following operations:

- `MyQueue(int k)`: Initialize the queue with arrays of size k.
- `void enqueue(int val)`: Add val to the queue.
- `int dequeue()`: Remove and return the first element from the queue.
- `int front()`: Return the first element of the queue without removing it.
- `boolean isEmpty()`: Return true if the queue is empty, false otherwise.

**Sample Test Cases**:
```
Input:
["MyQueue", "enqueue", "enqueue", "enqueue", "enqueue", "dequeue", "enqueue", "dequeue", "dequeue", "dequeue", "dequeue"]
[[3], [1], [2], [3], [4], [], [5], [], [], [], []]
Output:
[null, null, null, null, null, 1, null, 2, 3, 4, 5]

Explanation:
MyQueue q = new MyQueue(3); // Initialize queue with arrays of size 3
q.enqueue(1);               // queue: [1]
q.enqueue(2);               // queue: [1, 2]
q.enqueue(3);               // queue: [1, 2, 3]
q.enqueue(4);               // queue: [1, 2, 3, 4] (new array is created)
q.dequeue();                // return 1, queue: [2, 3, 4]
q.enqueue(5);               // queue: [2, 3, 4, 5]
q.dequeue();                // return 2, queue: [3, 4, 5]
q.dequeue();                // return 3, queue: [4, 5]
q.dequeue();                // return 4, queue: [5]
q.dequeue();                // return 5, queue: []
```

**Approach: Using Linked List of Arrays**

We can implement this by creating a linked list of fixed-size arrays:

```cpp
class MyQueue {
private:
    struct ArrayNode {
        int* arr;
        ArrayNode* next;
        int size;
        int capacity;
        int front;
        
        ArrayNode(int capacity) {
            this->capacity = capacity;
            arr = new int[capacity];
            next = nullptr;
            size = 0;
            front = 0;
        }
        
        ~ArrayNode() {
            delete[] arr;
        }
        
        bool isFull() {
            return size == capacity;
        }
        
        bool isEmpty() {
            return size == 0;
        }
        
        void enqueue(int val) {
            if (isFull()) return;
            int rear = (front + size) % capacity;
            arr[rear] = val;
            size++;
        }
        
        int dequeue() {
            if (isEmpty()) return -1;
            int val = arr[front];
            front = (front + 1) % capacity;
            size--;
            return val;
        }
        
        int peek() {
            if (isEmpty()) return -1;
            return arr[front];
        }
    };
    
    ArrayNode* head;
    ArrayNode* tail;
    int k;
    int totalSize;
    
public:
    MyQueue(int k) {
        this->k = k;
        head = nullptr;
        tail = nullptr;
        totalSize = 0;
    }
    
    ~MyQueue() {
        while (head) {
            ArrayNode* temp = head;
            head = head->next;
            delete temp;
        }
    }
    
    void enqueue(int val) {
        if (head == nullptr) {
            head = new ArrayNode(k);
            tail = head;
        }
        
        if (tail->isFull()) {
            tail->next = new ArrayNode(k);
            tail = tail->next;
        }
        
        tail->enqueue(val);
        totalSize++;
    }
    
    int dequeue() {
        if (isEmpty()) {
            return -1;
        }
        
        int val = head->dequeue();
        
        if (head->isEmpty()) {
            if (head == tail) {
                tail = nullptr;
            }
            ArrayNode* temp = head;
            head = head->next;
            delete temp;
        }
        
        totalSize--;
        return val;
    }
    
    int front() {
        if (isEmpty()) {
            return -1;
        }
        return head->peek();
    }
    
    bool isEmpty() {
        return totalSize == 0;
    }
};
```

**Time Complexity**:
- enqueue: O(1)
- dequeue: O(1)
- front: O(1)
- isEmpty: O(1)

**Space Complexity**: O(n) where n is the total number of elements in the queue

**Explanation**:
This implementation uses a linked list where each node contains a fixed-size array of capacity k. When a node's array is full, a new node is created. This approach maintains O(1) time complexity for all operations while efficiently using memory. The `dequeue` operation removes the node when its array becomes empty, ensuring we don't waste memory on empty arrays.

## Summary

In this module, we've explored different implementation approaches for stacks and queues:

1. **Array-Based Implementations**
   - Simple and memory-efficient
   - Fixed size limitation
   - Circular arrays for efficient queue implementation

2. **Linked List-Based Implementations**
   - Dynamic size
   - No overflow issues
   - Extra memory overhead for pointers

3. **Standard Library Implementations**
   - Pre-built, optimized implementations
   - Type-safe and consistent interface
   - Support for various container adaptors

We've also solved practical problems that demonstrate how to:
- Design custom stack implementations with advanced features
- Implement queues using multiple fixed-size arrays

Each implementation approach has its own advantages and trade-offs. The choice between them depends on the specific requirements of your application, such as:
- Whether the size is known in advance
- Memory constraints
- Performance requirements
- Need for custom operations

In the next module, we'll explore advanced variations and extensions of stacks and queues, including circular queues, double-ended queues (deques), and priority queues.