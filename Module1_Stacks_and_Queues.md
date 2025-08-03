# Module 1: Introduction to Stacks and Queues

## 1.1 Fundamentals of Linear Data Structures

Good morning, students! Today we're beginning our journey into two of the most fundamental data structures in computer science: stacks and queues. Before we dive into these specific structures, let's establish some context.

### What are Linear Data Structures?

Linear data structures are collections of data elements arranged in a sequential manner, where each element is attached to its previous and next adjacent elements. Examples include:

- Arrays
- Linked Lists
- Stacks
- Queues

In linear data structures, data elements can be traversed in a single run - that is, if we start from the first element, we can visit all elements one by one in a single pass.

### Arrays vs. Linked Lists - A Quick Refresher

**Arrays:**
- Elements stored in contiguous memory locations
- Fixed size (in many languages)
- Random access (O(1) time complexity)
- Insertion and deletion operations are expensive (O(n) time complexity) because elements need to be shifted

```cpp
// Array declaration and initialization in C++
int arr[5] = {1, 2, 3, 4, 5};
```

**Linked Lists:**
- Elements (nodes) stored in non-contiguous memory locations
- Dynamic size
- Sequential access (O(n) time complexity to access arbitrary elements)
- Efficient insertions and deletions (O(1) time complexity if position is known)

```cpp
// Basic linked list node in C++
struct Node {
    int data;
    Node* next;
    
    Node(int val) : data(val), next(nullptr) {}
};
```

## 1.2 Stack Basics

### What is a Stack?

A stack is a linear data structure that follows the Last-In-First-Out (LIFO) principle. Think of a stack of plates - you can only take the top plate (the last one placed), not any plate from the middle.

![Stack Visualization](https://media.geeksforgeeks.org/wp-content/cdn-uploads/gq/2013/03/stack.png)

### Core Operations

1. **Push**: Add an element to the top of the stack
2. **Pop**: Remove the top element from the stack
3. **Peek/Top**: View the top element without removing it
4. **isEmpty**: Check if the stack is empty

### Time Complexity

All basic stack operations (push, pop, peek, isEmpty) have O(1) time complexity when implemented correctly.

### Basic Implementation in C++

```cpp
#include <iostream>
using namespace std;

class Stack {
private:
    int *arr;
    int top;
    int capacity;

public:
    Stack(int size) {
        arr = new int[size];
        capacity = size;
        top = -1;
    }
    
    ~Stack() {
        delete[] arr;
    }
    
    // Add element to top of stack
    void push(int val) {
        if (isFull()) {
            cout << "Stack Overflow" << endl;
            return;
        }
        arr[++top] = val;
    }
    
    // Remove element from top of stack
    int pop() {
        if (isEmpty()) {
            cout << "Stack Underflow" << endl;
            return -1;
        }
        return arr[top--];
    }
    
    // Return top element
    int peek() {
        if (isEmpty()) {
            cout << "Stack is empty" << endl;
            return -1;
        }
        return arr[top];
    }
    
    // Check if stack is empty
    bool isEmpty() {
        return top == -1;
    }
    
    // Check if stack is full
    bool isFull() {
        return top == capacity - 1;
    }
};
```

## 1.3 Queue Basics

### What is a Queue?

A queue is a linear data structure that follows the First-In-First-Out (FIFO) principle. Think of a queue at a ticket counter - the first person in line is the first one to be served.

![Queue Visualization](https://media.geeksforgeeks.org/wp-content/cdn-uploads/gq/2014/02/Queue.png)

### Core Operations

1. **Enqueue**: Add an element to the rear of the queue
2. **Dequeue**: Remove the element from the front of the queue
3. **Front/Peek**: View the front element without removing it
4. **isEmpty**: Check if the queue is empty

### Time Complexity

All basic queue operations (enqueue, dequeue, front, isEmpty) have O(1) time complexity when implemented correctly.

### Basic Implementation in C++

```cpp
#include <iostream>
using namespace std;

class Queue {
private:
    int *arr;
    int front, rear;
    int capacity;
    int count;  // Current size of queue

public:
    Queue(int size) {
        arr = new int[size];
        capacity = size;
        front = 0;
        rear = -1;
        count = 0;
    }
    
    ~Queue() {
        delete[] arr;
    }
    
    // Add element to the queue
    void enqueue(int val) {
        if (isFull()) {
            cout << "Queue Overflow" << endl;
            return;
        }
        rear = (rear + 1) % capacity;
        arr[rear] = val;
        count++;
    }
    
    // Remove element from the queue
    int dequeue() {
        if (isEmpty()) {
            cout << "Queue Underflow" << endl;
            return -1;
        }
        int val = arr[front];
        front = (front + 1) % capacity;
        count--;
        return val;
    }
    
    // Return front element
    int peek() {
        if (isEmpty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        return arr[front];
    }
    
    // Check if queue is empty
    bool isEmpty() {
        return count == 0;
    }
    
    // Check if queue is full
    bool isFull() {
        return count == capacity;
    }
};
```

## Practical Problems

Now let's apply our knowledge to solve some problems!

### Problem 1: Valid Parentheses

**Problem Link**: [LeetCode 20 - Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

**Problem Statement**:
Given a string s containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

An input string is valid if:
1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

**Sample Test Cases**:
```
Input: s = "()"
Output: true

Input: s = "()[]{}"
Output: true

Input: s = "(]"
Output: false
```

**Explanation**:
This problem is a classic application of stacks. When we encounter an opening bracket, we push it onto the stack. When we encounter a closing bracket, we check if the stack is empty (invalid if empty) and if the top of the stack contains the corresponding opening bracket.

**Approach**:
1. Create an empty stack
2. Traverse the string character by character
3. If the current character is an opening bracket, push it onto the stack
4. If the current character is a closing bracket:
   a. If the stack is empty, return false
   b. If the top of the stack doesn't match the current closing bracket, return false
   c. Otherwise, pop the top element from the stack
5. After traversing the string, if the stack is empty, return true; otherwise, return false

**Implementation**:

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

bool isValid(string s) {
    stack<char> st;
    
    for (char c : s) {
        if (c == '(' || c == '{' || c == '[') {
            // If opening bracket, push to stack
            st.push(c);
        } else {
            // If closing bracket
            if (st.empty()) return false;
            
            if (c == ')' && st.top() != '(') return false;
            if (c == '}' && st.top() != '{') return false;
            if (c == ']' && st.top() != '[') return false;
            
            st.pop();
        }
    }
    
    // If stack is empty, all brackets matched
    return st.empty();
}

int main() {
    string s1 = "()";
    string s2 = "()[]{}";
    string s3 = "(]";
    
    cout << "\"" << s1 << "\" is " << (isValid(s1) ? "valid" : "invalid") << endl;
    cout << "\"" << s2 << "\" is " << (isValid(s2) ? "valid" : "invalid") << endl;
    cout << "\"" << s3 << "\" is " << (isValid(s3) ? "valid" : "invalid") << endl;
    
    return 0;
}
```

**Time Complexity**: O(n) where n is the length of the string
**Space Complexity**: O(n) in the worst case (e.g., "((((")

### Problem 2: Implement Queue using Stacks

**Problem Link**: [LeetCode 232 - Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)

**Problem Statement**:
Implement a first in first out (FIFO) queue using only two stacks. The implemented queue should support all the functions of a normal queue (push, peek, pop, and empty).

Implement the MyQueue class:
- `void push(int x)` Pushes element x to the back of the queue.
- `int pop()` Removes the element from the front of the queue and returns it.
- `int peek()` Returns the element at the front of the queue.
- `boolean empty()` Returns true if the queue is empty, false otherwise.

**Sample Test Cases**:
```
Input:
["MyQueue", "push", "push", "peek", "pop", "empty"]
[[], [1], [2], [], [], []]
Output:
[null, null, null, 1, 1, false]

Explanation:
MyQueue myQueue = new MyQueue();
myQueue.push(1); // queue is: [1]
myQueue.push(2); // queue is: [1, 2]
myQueue.peek(); // return 1
myQueue.pop(); // return 1, queue is [2]
myQueue.empty(); // return false
```

**Explanation**:
This problem tests our understanding of both stacks and queues. We need to implement a queue (FIFO) using only stacks (LIFO). The key insight is to use two stacks and transfer elements between them to reverse the order when needed.

**Brute Force Approach**:
For each dequeue operation, transfer all elements from the first stack to the second stack, pop the top element from the second stack (which was at the bottom of the first stack), and then transfer all elements back to the first stack.

**Time Complexity**: Push - O(1), Pop/Peek - O(n)

**Optimal Approach**:
Use two stacks: an "input" stack for pushing elements and an "output" stack for popping/peeking. When pop/peek is called and the output stack is empty, transfer all elements from the input stack to the output stack. This amortizes the cost of transfers.

**Implementation**:

```cpp
#include <iostream>
#include <stack>
using namespace std;

class MyQueue {
private:
    stack<int> inputStack;  // For push operation
    stack<int> outputStack; // For pop/peek operations
    
    // Transfer elements from input to output stack when needed
    void transfer() {
        if (outputStack.empty()) {
            while (!inputStack.empty()) {
                outputStack.push(inputStack.top());
                inputStack.pop();
            }
        }
    }
    
public:
    MyQueue() {
        // Constructor
    }
    
    void push(int x) {
        inputStack.push(x);
    }
    
    int pop() {
        transfer();
        int front = outputStack.top();
        outputStack.pop();
        return front;
    }
    
    int peek() {
        transfer();
        return outputStack.top();
    }
    
    bool empty() {
        return inputStack.empty() && outputStack.empty();
    }
};

int main() {
    MyQueue myQueue;
    myQueue.push(1);
    myQueue.push(2);
    cout << "Front element: " << myQueue.peek() << endl; // should return 1
    cout << "Popped element: " << myQueue.pop() << endl; // should return 1
    cout << "Is queue empty? " << (myQueue.empty() ? "Yes" : "No") << endl; // should return No
    
    return 0;
}
```

**Time Complexity**:
- Push: O(1)
- Pop: Amortized O(1)
- Peek: Amortized O(1)
- Empty: O(1)

**Space Complexity**: O(n)

**Note**: The "amortized" O(1) time complexity means that while some operations might take O(n) time, the average time over many operations approaches O(1).

## Summary

In this module, we've learned about:

1. **Linear data structures** and their characteristics
2. **Stacks** and their LIFO principle
   - Push, pop, peek, and isEmpty operations
   - Implementation using arrays
   - Application in solving the Valid Parentheses problem
3. **Queues** and their FIFO principle
   - Enqueue, dequeue, front, and isEmpty operations
   - Implementation using arrays
   - Implementation using two stacks

In the next module, we'll explore more advanced implementations of stacks and queues, including linked list-based approaches and standard library implementations in C++.