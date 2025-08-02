# 🔹 Block 1: Stack – Fundamentals & Basic Operations

## 1.0 Block Overview and Learning Objectives

### 1.0.1 Introduction to Block 1
Block 1 provides a comprehensive foundation for understanding stack data structures, their implementations, and fundamental operations. This module serves as the cornerstone for advanced stack applications covered in subsequent blocks.

### 1.0.2 Learning Objectives
By the end of Block 1, students will be able to:
- Understand the fundamental principles of Last In First Out (LIFO) data structures
- Implement stack using both array and linked list approaches
- Analyze time and space complexity of stack operations
- Apply stack concepts to solve basic computational problems
- Compare different stack implementation strategies
- Recognize real-world applications of stack data structures

### 1.0.3 Prerequisites
- Basic programming knowledge in C++
- Understanding of arrays and basic data structures
- Familiarity with pointers and dynamic memory allocation
- Knowledge of algorithmic complexity analysis

### 1.0.4 Block Structure Overview
```
Block 1 Timeline:
Day 1: Introduction to Stack – Definition, Applications, and Operations
Day 2: Implementation of Stack using Array
Day 3: Implementation of Stack using Linked List
Day 4: Introduction to Queue – Definition and Basic Operations
Day 5: Implementation of Queue using Array
Day 6: Implementation of Queue using Linked List
Day 7: Comparative Analysis – Stack vs Queue
```

---

## 1.1 Introduction to Stack Data Structure

### 1.1.1 Theoretical Foundation and Definition

#### 1.1.1.1 Formal Definition
A **Stack** is a linear data structure that follows the Last In First Out (LIFO) principle. Elements are added and removed from the same end, called the "top" of the stack. The stack operates on the principle that the last element inserted is the first element to be removed.

#### 1.1.1.2 Mathematical Model
A stack can be formally defined as an Abstract Data Type (ADT) with the following operations:
- **S**: Set of elements in the stack
- **top**: Pointer to the topmost element
- **push(x)**: S ← S ∪ {x}, where x becomes the new top
- **pop()**: S ← S - {top}, return and remove top element
- **peek()/top()**: Return top element without removal
- **isEmpty()**: Return true if |S| = 0, false otherwise

#### 1.1.1.3 Stack Properties and Characteristics
1. **LIFO Ordering**: Last element added is first to be removed
2. **Single Point Access**: All operations occur at the top
3. **Dynamic Size**: Can grow and shrink during runtime
4. **Restricted Access**: No random access to middle elements
5. **Memory Efficiency**: Optimal space usage for sequential operations

#### 1.1.1.4 Historical Context and Development
The stack concept was introduced in the 1940s-1950s during early computer science development:
- **1946**: First implementation in electronic computers for subroutine calls
- **1957**: FORTRAN introduced stack-based function calls
- **1960s**: Stack became fundamental in recursive algorithm implementation
- **Modern Era**: Essential component in compilers, operating systems, and applications

### 1.1.2 Real-world Applications and Use Cases

#### 1.1.2.1 Computer Science Applications
```cpp
// 1.1.2.1.1 Application Categories
/*
1. Function Call Management:
   - Maintaining function call hierarchy
   - Parameter passing and local variable storage
   - Return address management

2. Expression Evaluation:
   - Infix to postfix conversion
   - Arithmetic expression parsing
   - Operator precedence handling

3. Memory Management:
   - Stack memory allocation
   - Automatic variable cleanup
   - Scope-based resource management

4. Compiler Design:
   - Syntax analysis and parsing
   - Symbol table management
   - Code generation optimization

5. Operating Systems:
   - Process stack management
   - Interrupt handling
   - Context switching
*/
```

#### 1.1.2.2 Everyday Life Analogies
- **Plate Stack in Cafeteria**: Last plate placed is first to be taken
- **Browser History**: Back button follows LIFO order
- **Undo Operations**: Last action is first to be undone
- **Book Stack**: Last book placed on top is first to be picked
- **Call Stack in Programming**: Function calls follow LIFO execution

#### 1.1.2.3 Advanced Applications
```cpp
// 1.1.2.3.1 Modern Stack Applications
class StackApplications {
public:
    void demonstrateApplications() {
        cout << "=== Modern Stack Applications ===" << endl;
        
        cout << "1. Web Browsers:" << endl;
        cout << "   - Page navigation history" << endl;
        cout << "   - JavaScript call stack" << endl;
        cout << "   - DOM manipulation operations" << endl;
        
        cout << "\n2. Mobile Applications:" << endl;
        cout << "   - Screen navigation stack" << endl;
        cout << "   - Activity lifecycle management" << endl;
        cout << "   - Event handling queues" << endl;
        
        cout << "\n3. Gaming Systems:" << endl;
        cout << "   - Game state management" << endl;
        cout << "   - Move history in chess/puzzle games" << endl;
        cout << "   - Resource allocation tracking" << endl;
        
        cout << "\n4. Financial Systems:" << endl;
        cout << "   - Transaction rollback mechanisms" << endl;
        cout << "   - Order processing pipelines" << endl;
        cout << "   - Risk calculation frameworks" << endl;
    }
};
```

### 1.1.3 Stack Operations Specification

#### 1.1.3.1 Core Operations Definition

```cpp
#include <iostream>
#include <stdexcept>
using namespace std;

// 1.1.3.1.1 Abstract Stack Interface
template<typename T>
class StackInterface {
public:
    virtual ~StackInterface() = default;
    
    // Primary operations
    virtual void push(const T& element) = 0;
    virtual T pop() = 0;
    virtual T top() const = 0;
    
    // Status operations
    virtual bool isEmpty() const = 0;
    virtual bool isFull() const = 0;
    virtual int size() const = 0;
    
    // Utility operations
    virtual void clear() = 0;
    virtual void display() const = 0;
};

// 1.1.3.1.2 Operation Complexity Analysis
class StackComplexityAnalysis {
public:
    void analyzeOperations() {
        cout << "=== Stack Operations Complexity Analysis ===" << endl;
        
        cout << "Operation | Time Complexity | Space Complexity | Description" << endl;
        cout << "----------|-----------------|------------------|-------------" << endl;
        cout << "push()    | O(1)           | O(1)            | Add to top" << endl;
        cout << "pop()     | O(1)           | O(1)            | Remove from top" << endl;
        cout << "top()     | O(1)           | O(1)            | Access top element" << endl;
        cout << "isEmpty() | O(1)           | O(1)            | Check if empty" << endl;
        cout << "isFull()  | O(1)           | O(1)            | Check if full" << endl;
        cout << "size()    | O(1)           | O(1)            | Get element count" << endl;
        cout << "clear()   | O(n)           | O(1)            | Remove all elements" << endl;
        
        cout << "\nKey Insights:" << endl;
        cout << "- All primary operations are O(1) constant time" << endl;
        cout << "- Space efficiency depends on implementation strategy" << endl;
        cout << "- No random access capability (unlike arrays)" << endl;
        cout << "- Memory locality varies by implementation" << endl;
    }
};
```

#### 1.1.3.2 Operation Behavior Specification

```cpp
// 1.1.3.2.1 Detailed Operation Specifications
class StackOperationSpecs {
public:
    void demonstrateOperationBehavior() {
        cout << "=== Stack Operation Behavior Specifications ===" << endl;
        
        cout << "\n1. PUSH Operation:" << endl;
        cout << "   Pre-condition: Stack is not full" << endl;
        cout << "   Action: Add element to top position" << endl;
        cout << "   Post-condition: Element becomes new top, size increases by 1" << endl;
        cout << "   Exception: Stack overflow if capacity exceeded" << endl;
        
        cout << "\n2. POP Operation:" << endl;
        cout << "   Pre-condition: Stack is not empty" << endl;
        cout << "   Action: Remove and return top element" << endl;
        cout << "   Post-condition: Previous top removed, size decreases by 1" << endl;
        cout << "   Exception: Stack underflow if stack is empty" << endl;
        
        cout << "\n3. TOP/PEEK Operation:" << endl;
        cout << "   Pre-condition: Stack is not empty" << endl;
        cout << "   Action: Return top element without removal" << endl;
        cout << "   Post-condition: Stack remains unchanged" << endl;
        cout << "   Exception: Invalid access if stack is empty" << endl;
        
        cout << "\n4. isEmpty Operation:" << endl;
        cout << "   Pre-condition: None" << endl;
        cout << "   Action: Check if stack contains no elements" << endl;
        cout << "   Post-condition: Returns boolean result" << endl;
        cout << "   Exception: None" << endl;
    }
    
    void demonstrateEdgeCases() {
        cout << "\n=== Edge Cases and Error Handling ===" << endl;
        
        cout << "1. Empty Stack Operations:" << endl;
        cout << "   - pop() on empty stack → Exception or error code" << endl;
        cout << "   - top() on empty stack → Exception or error code" << endl;
        cout << "   - size() on empty stack → Returns 0" << endl;
        
        cout << "\n2. Full Stack Operations (Fixed Size):" << endl;
        cout << "   - push() on full stack → Exception or error code" << endl;
        cout << "   - Capacity checking before push operations" << endl;
        
        cout << "\n3. Single Element Stack:" << endl;
        cout << "   - Normal operations should work correctly" << endl;
        cout << "   - pop() should transition to empty state" << endl;
        
        cout << "\n4. Memory Management:" << endl;
        cout << "   - Dynamic memory allocation/deallocation" << endl;
        cout << "   - Prevention of memory leaks" << endl;
        cout << "   - Handling of object destruction" << endl;
    }
};
```

---

## 1.2 Stack Implementation Using Arrays

### 1.2.1 Design Strategy and Architecture

#### 1.2.1.1 Array-Based Implementation Approach
Array-based stack implementation uses a fixed-size array to store elements and maintains a top pointer to track the current position. This approach provides excellent cache locality and predictable memory usage patterns.

#### 1.2.1.2 Memory Layout and Organization
```
Array-based Stack Memory Layout:
+---+---+---+---+---+---+---+---+
| 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |  ← Array indices
+---+---+---+---+---+---+---+---+
| A | B | C | D |   |   |   |   |  ← Stack elements
+---+---+---+---+---+---+---+---+
              ↑
             top = 3
             size = 4
             capacity = 8
```

### 1.2.2 Comprehensive Array-Based Implementation

#### 1.2.2.1 Basic Array Stack Implementation

```cpp
#include <iostream>
#include <vector>
#include <stdexcept>
#include <iomanip>
#include <chrono>
using namespace std;

// 1.2.2.1.1 Template-based Array Stack
template<typename T>
class ArrayStack {
private:
    vector<T> stackArray;    // Dynamic array for storage
    int topIndex;            // Index of top element
    int capacity;            // Maximum capacity
    int currentSize;         // Current number of elements
    
    // Statistics for analysis
    mutable long long pushOperations;
    mutable long long popOperations;
    mutable long long accessOperations;
    
public:
    // 1.2.2.1.2 Constructor with capacity specification
    explicit ArrayStack(int cap = 10) : capacity(cap), topIndex(-1), currentSize(0),
                                       pushOperations(0), popOperations(0), accessOperations(0) {
        if (cap <= 0) {
            throw invalid_argument("Stack capacity must be positive");
        }
        
        stackArray.reserve(cap);
        stackArray.resize(cap);
        
        cout << "Array Stack created with capacity: " << cap << endl;
        displayMemoryLayout();
    }
    
    // 1.2.2.1.3 Destructor with cleanup
    ~ArrayStack() {
        cout << "Array Stack destroyed. Final statistics:" << endl;
        displayStatistics();
    }
    
    // 1.2.2.1.4 Push operation - O(1)
    void push(const T& element) {
        cout << "\n=== PUSH Operation ===" << endl;
        cout << "Attempting to push: " << element << endl;
        
        if (isFull()) {
            cout << "Error: Stack overflow! Cannot push " << element << endl;
            displayCurrentState();
            throw overflow_error("Stack is full");
        }
        
        // Pre-operation state
        cout << "Before push:" << endl;
        displayCurrentState();
        
        // Perform push
        topIndex++;
        stackArray[topIndex] = element;
        currentSize++;
        pushOperations++;
        
        // Post-operation state
        cout << "After push:" << endl;
        displayCurrentState();
        
        cout << "Push operation completed successfully" << endl;
        cout << "New top index: " << topIndex << ", New size: " << currentSize << endl;
    }
    
    // 1.2.2.1.5 Pop operation - O(1)
    T pop() {
        cout << "\n=== POP Operation ===" << endl;
        
        if (isEmpty()) {
            cout << "Error: Stack underflow! Cannot pop from empty stack" << endl;
            displayCurrentState();
            throw underflow_error("Stack is empty");
        }
        
        // Pre-operation state
        cout << "Before pop:" << endl;
        displayCurrentState();
        
        // Perform pop
        T poppedElement = stackArray[topIndex];
        topIndex--;
        currentSize--;
        popOperations++;
        
        cout << "Popped element: " << poppedElement << endl;
        
        // Post-operation state
        cout << "After pop:" << endl;
        displayCurrentState();
        
        cout << "Pop operation completed successfully" << endl;
        return poppedElement;
    }
    
    // 1.2.2.1.6 Top/Peek operation - O(1)
    T top() const {
        cout << "\n=== TOP/PEEK Operation ===" << endl;
        accessOperations++;
        
        if (isEmpty()) {
            cout << "Error: Cannot peek into empty stack" << endl;
            throw underflow_error("Stack is empty");
        }
        
        T topElement = stackArray[topIndex];
        cout << "Top element: " << topElement << " (at index " << topIndex << ")" << endl;
        displayCurrentState();
        
        return topElement;
    }
    
    // 1.2.2.1.7 Status checking operations
    bool isEmpty() const {
        bool empty = (currentSize == 0);
        cout << "isEmpty() check: " << (empty ? "true" : "false") 
             << " (size=" << currentSize << ")" << endl;
        return empty;
    }
    
    bool isFull() const {
        bool full = (currentSize == capacity);
        cout << "isFull() check: " << (full ? "true" : "false") 
             << " (size=" << currentSize << ", capacity=" << capacity << ")" << endl;
        return full;
    }
    
    int size() const {
        cout << "Current stack size: " << currentSize << endl;
        return currentSize;
    }
    
    int getCapacity() const {
        return capacity;
    }
    
    // 1.2.2.1.8 Utility operations
    void clear() {
        cout << "\n=== CLEAR Operation ===" << endl;
        cout << "Clearing stack with " << currentSize << " elements" << endl;
        
        topIndex = -1;
        currentSize = 0;
        
        cout << "Stack cleared successfully" << endl;
        displayCurrentState();
    }
    
    void display() const {
        cout << "\n=== Stack Display ===" << endl;
        
        if (isEmpty()) {
            cout << "Stack is empty" << endl;
            return;
        }
        
        cout << "Stack contents (top to bottom):" << endl;
        for (int i = topIndex; i >= 0; i--) {
            cout << "Index " << setw(2) << i << ": " << stackArray[i];
            if (i == topIndex) {
                cout << " ← TOP";
            }
            cout << endl;
        }
        
        cout << "Stack size: " << currentSize << "/" << capacity << endl;
    }
    
    // 1.2.2.1.9 Advanced display and analysis methods
    void displayCurrentState() const {
        cout << "Stack State: size=" << currentSize << ", capacity=" << capacity 
             << ", topIndex=" << topIndex << endl;
        
        cout << "Array contents: [";
        for (int i = 0; i < capacity; i++) {
            if (i <= topIndex) {
                cout << stackArray[i];
            } else {
                cout << "_";
            }
            if (i < capacity - 1) cout << ", ";
        }
        cout << "]" << endl;
    }
    
    void displayMemoryLayout() const {
        cout << "\n=== Memory Layout Analysis ===" << endl;
        cout << "Stack array address: " << &stackArray[0] << endl;
        cout << "Element size: " << sizeof(T) << " bytes" << endl;
        cout << "Total allocated memory: " << capacity * sizeof(T) << " bytes" << endl;
        cout << "Memory efficiency: " << (double)(currentSize * sizeof(T)) / (capacity * sizeof(T)) * 100 << "%" << endl;
    }
    
    void displayStatistics() const {
        cout << "\n=== Performance Statistics ===" << endl;
        cout << "Total push operations: " << pushOperations << endl;
        cout << "Total pop operations: " << popOperations << endl;
        cout << "Total access operations: " << accessOperations << endl;
        cout << "Net elements added: " << (pushOperations - popOperations) << endl;
        cout << "Average utilization: " << (double)currentSize / capacity * 100 << "%" << endl;
    }
    
    // 1.2.2.1.10 Performance testing methods
    void performanceTest(int operations) {
        cout << "\n=== Performance Test (" << operations << " operations) ===" << endl;
        
        auto start = chrono::high_resolution_clock::now();
        
        // Push operations
        for (int i = 0; i < operations && !isFull(); i++) {
            try {
                push(static_cast<T>(i));
            } catch (const overflow_error& e) {
                cout << "Stack overflow during performance test at operation " << i << endl;
                break;
            }
        }
        
        // Pop operations
        for (int i = 0; i < operations && !isEmpty(); i++) {
            try {
                pop();
            } catch (const underflow_error& e) {
                cout << "Stack underflow during performance test at operation " << i << endl;
                break;
            }
        }
        
        auto end = chrono::high_resolution_clock::now();
        auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
        
        cout << "Performance test completed in " << duration.count() << " microseconds" << endl;
        cout << "Average time per operation: " << (double)duration.count() / (operations * 2) << " microseconds" << endl;
    }
};

// 1.2.2.2 Specialized Integer Stack with Additional Features
class IntegerArrayStack {
private:
    int* stackArray;
    int topIndex;
    int capacity;
    int currentSize;
    
    // Additional features
    int minElement;
    int maxElement;
    long long sum;
    
public:
    // 1.2.2.2.1 Constructor with manual memory management
    explicit IntegerArrayStack(int cap) : capacity(cap), topIndex(-1), currentSize(0),
                                         minElement(INT_MAX), maxElement(INT_MIN), sum(0) {
        stackArray = new int[capacity];
        cout << "Integer Stack created with manual memory management (capacity: " << cap << ")" << endl;
    }
    
    // 1.2.2.2.2 Destructor
    ~IntegerArrayStack() {
        delete[] stackArray;
        cout << "Integer Stack destroyed, memory deallocated" << endl;
    }
    
    // 1.2.2.2.3 Enhanced push with statistics
    void push(int element) {
        if (currentSize >= capacity) {
            throw overflow_error("Stack overflow");
        }
        
        topIndex++;
        stackArray[topIndex] = element;
        currentSize++;
        
        // Update statistics
        sum += element;
        if (currentSize == 1) {
            minElement = maxElement = element;
        } else {
            minElement = min(minElement, element);
            maxElement = max(maxElement, element);
        }
        
        cout << "Pushed " << element << " | Min: " << minElement 
             << ", Max: " << maxElement << ", Sum: " << sum << endl;
    }
    
    // 1.2.2.2.4 Enhanced pop with statistics update
    int pop() {
        if (currentSize <= 0) {
            throw underflow_error("Stack underflow");
        }
        
        int poppedElement = stackArray[topIndex];
        topIndex--;
        currentSize--;
        sum -= poppedElement;
        
        // Recalculate min/max if necessary
        if (currentSize == 0) {
            minElement = INT_MAX;
            maxElement = INT_MIN;
        } else if (poppedElement == minElement || poppedElement == maxElement) {
            recalculateMinMax();
        }
        
        cout << "Popped " << poppedElement;
        if (currentSize > 0) {
            cout << " | Min: " << minElement << ", Max: " << maxElement << ", Sum: " << sum;
        }
        cout << endl;
        
        return poppedElement;
    }
    
    // 1.2.2.2.5 Statistical methods
    int getMin() const {
        if (currentSize == 0) throw underflow_error("Stack is empty");
        return minElement;
    }
    
    int getMax() const {
        if (currentSize == 0) throw underflow_error("Stack is empty");
        return maxElement;
    }
    
    double getAverage() const {
        if (currentSize == 0) throw underflow_error("Stack is empty");
        return (double)sum / currentSize;
    }
    
    long long getSum() const {
        return sum;
    }
    
    void displayStatistics() const {
        if (currentSize == 0) {
            cout << "Stack is empty - no statistics available" << endl;
            return;
        }
        
        cout << "\n=== Stack Statistics ===" << endl;
        cout << "Size: " << currentSize << "/" << capacity << endl;
        cout << "Minimum element: " << minElement << endl;
        cout << "Maximum element: " << maxElement << endl;
        cout << "Sum of elements: " << sum << endl;
        cout << "Average value: " << fixed << setprecision(2) << getAverage() << endl;
        cout << "Range: " << (maxElement - minElement) << endl;
    }
    
private:
    // 1.2.2.2.6 Helper method to recalculate min/max
    void recalculateMinMax() {
        if (currentSize == 0) {
            minElement = INT_MAX;
            maxElement = INT_MIN;
            return;
        }
        
        minElement = maxElement = stackArray[0];
        for (int i = 1; i <= topIndex; i++) {
            minElement = min(minElement, stackArray[i]);
            maxElement = max(maxElement, stackArray[i]);
        }
    }
};
```

### 1.2.3 Advanced Array Stack Features

#### 1.2.3.1 Dynamic Array Stack Implementation

```cpp
// 1.2.3.1.1 Self-resizing Array Stack
template<typename T>
class DynamicArrayStack {
private:
    T* stackArray;
    int topIndex;
    int capacity;
    int currentSize;
    
    static const int INITIAL_CAPACITY = 4;
    static const double GROWTH_FACTOR;
    static const double SHRINK_THRESHOLD;
    
public:
    // Constructor
    DynamicArrayStack() : topIndex(-1), currentSize(0), capacity(INITIAL_CAPACITY) {
        stackArray = new T[capacity];
        cout << "Dynamic Array Stack created with initial capacity: " << capacity << endl;
    }
    
    // Destructor
    ~DynamicArrayStack() {
        delete[] stackArray;
        cout << "Dynamic Array Stack destroyed" << endl;
    }
    
    // 1.2.3.1.2 Auto-resizing push operation
    void push(const T& element) {
        cout << "Pushing " << element << " (current capacity: " << capacity << ")" << endl;
        
        // Check if resize is needed
        if (currentSize >= capacity) {
            resize(capacity * 2);
        }
        
        topIndex++;
        stackArray[topIndex] = element;
        currentSize++;
        
        cout << "Element pushed successfully. New size: " << currentSize << endl;
    }
    
    // 1.2.3.1.3 Auto-shrinking pop operation
    T pop() {
        if (currentSize <= 0) {
            throw underflow_error("Stack underflow");
        }
        
        T poppedElement = stackArray[topIndex];
        topIndex--;
        currentSize--;
        
        // Check if shrinking is needed
        if (currentSize <= capacity / 4 && capacity > INITIAL_CAPACITY) {
            resize(capacity / 2);
        }
        
        cout << "Popped " << poppedElement << " (current capacity: " << capacity << ")" << endl;
        return poppedElement;
    }
    
    // Other operations remain similar to basic implementation
    T top() const {
        if (currentSize <= 0) throw underflow_error("Stack is empty");
        return stackArray[topIndex];
    }
    
    bool isEmpty() const { return currentSize == 0; }
    int size() const { return currentSize; }
    int getCapacity() const { return capacity; }
    
private:
    // 1.2.3.1.4 Dynamic resize operation
    void resize(int newCapacity) {
        cout << "Resizing stack from " << capacity << " to " << newCapacity << endl;
        
        T* newArray = new T[newCapacity];
        
        // Copy existing elements
        for (int i = 0; i < currentSize; i++) {
            newArray[i] = stackArray[i];
        }
        
        delete[] stackArray;
        stackArray = newArray;
        capacity = newCapacity;
        
        cout << "Resize completed. New capacity: " << capacity << endl;
    }
};

// Static member definitions
template<typename T>
const double DynamicArrayStack<T>::GROWTH_FACTOR = 2.0;

template<typename T>
const double DynamicArrayStack<T>::SHRINK_THRESHOLD = 0.25;
```

### 1.2.4 Comprehensive Testing Suite

#### 1.2.4.1 Array Stack Testing Framework

```cpp
// 1.2.4.1.1 Comprehensive test suite
void testArrayStackImplementation() {
    cout << "=== Array Stack Implementation Testing ===" << endl;
    
    // Test Case 1: Basic Operations
    cout << "\n--- Test Case 1: Basic Operations ---" << endl;
    try {
        ArrayStack<int> stack(5);
        
        // Test push operations
        for (int i = 1; i <= 3; i++) {
            stack.push(i * 10);
        }
        
        // Test peek operation
        cout << "Top element: " << stack.top() << endl;
        
        // Test pop operations
        while (!stack.isEmpty()) {
            cout << "Popped: " << stack.pop() << endl;
        }
        
    } catch (const exception& e) {
        cout << "Exception caught: " << e.what() << endl;
    }
    
    // Test Case 2: Edge Cases
    cout << "\n--- Test Case 2: Edge Cases ---" << endl;
    try {
        ArrayStack<string> stringStack(2);
        
        // Test empty stack operations
        cout << "Testing empty stack operations:" << endl;
        try {
            stringStack.pop(); // Should throw exception
        } catch (const underflow_error& e) {
            cout << "Caught expected underflow: " << e.what() << endl;
        }
        
        // Test full stack operations
        stringStack.push("First");
        stringStack.push("Second");
        
        try {
            stringStack.push("Third"); // Should throw exception
        } catch (const overflow_error& e) {
            cout << "Caught expected overflow: " << e.what() << endl;
        }
        
    } catch (const exception& e) {
        cout << "Unexpected exception: " << e.what() << endl;
    }
    
    // Test Case 3: Performance Analysis
    cout << "\n--- Test Case 3: Performance Analysis ---" << endl;
    ArrayStack<int> perfStack(1000);
    perfStack.performanceTest(500);
    
    // Test Case 4: Integer Stack with Statistics
    cout << "\n--- Test Case 4: Statistical Features ---" << endl;
    try {
        IntegerArrayStack intStack(10);
        
        vector<int> testData = {15, 8, 23, 4, 42, 16, 7, 31};
        
        cout << "Pushing test data: ";
        for (int val : testData) {
            cout << val << " ";
            intStack.push(val);
        }
        cout << endl;
        
        intStack.displayStatistics();
        
        cout << "\nPopping some elements:" << endl;
        for (int i = 0; i < 3; i++) {
            intStack.pop();
        }
        
        intStack.displayStatistics();
        
    } catch (const exception& e) {
        cout << "Exception in statistical test: " << e.what() << endl;
    }
    
    // Test Case 5: Dynamic Stack
    cout << "\n--- Test Case 5: Dynamic Resizing ---" << endl;
    DynamicArrayStack<int> dynStack;
    
    cout << "Testing dynamic growth:" << endl;
    for (int i = 1; i <= 10; i++) {
        dynStack.push(i);
        cout << "Size: " << dynStack.size() << ", Capacity: " << dynStack.getCapacity() << endl;
    }
    
    cout << "\nTesting dynamic shrinking:" << endl;
    while (!dynStack.isEmpty()) {
        dynStack.pop();
        cout << "Size: " << dynStack.size() << ", Capacity: " << dynStack.getCapacity() << endl;
    }
}
```

### 1.2.5 Complexity Analysis and Optimization

#### 1.2.5.1 Detailed Complexity Analysis

```cpp
// 1.2.5.1.1 Complexity Analysis Class
class ArrayStackComplexityAnalysis {
public:
    void analyzeTimeComplexity() {
        cout << "=== Array Stack Time Complexity Analysis ===" << endl;
        
        cout << "\nPrimary Operations:" << endl;
        cout << "Operation    | Best Case | Average Case | Worst Case | Explanation" << endl;
        cout << "-------------|-----------|--------------|------------|-------------" << endl;
        cout << "push()       | O(1)      | O(1)         | O(1)*      | Direct array access" << endl;
        cout << "pop()        | O(1)      | O(1)         | O(1)*      | Direct array access" << endl;
        cout << "top()        | O(1)      | O(1)         | O(1)       | Direct array access" << endl;
        cout << "isEmpty()    | O(1)      | O(1)         | O(1)       | Simple comparison" << endl;
        cout << "isFull()     | O(1)      | O(1)         | O(1)       | Simple comparison" << endl;
        cout << "size()       | O(1)      | O(1)         | O(1)       | Return stored value" << endl;
        
        cout << "\n* For dynamic arrays, resize operations may cause O(n) complexity" << endl;
        cout << "  but amortized analysis shows O(1) average time per operation" << endl;
        
        cout << "\nAmortized Analysis for Dynamic Arrays:" << endl;
        cout << "- Doubling strategy: Each element is copied at most log(n) times" << endl;
        cout << "- Total cost for n operations: O(n)" << endl;
        cout << "- Amortized cost per operation: O(1)" << endl;
    }
    
    void analyzeSpaceComplexity() {
        cout << "\n=== Array Stack Space Complexity Analysis ===" << endl;
        
        cout << "\nFixed-size Array Stack:" << endl;
        cout << "- Array storage: O(capacity)" << endl;
        cout << "- Auxiliary variables: O(1)" << endl;
        cout << "- Total space: O(capacity)" << endl;
        cout << "- Space utilization: currentSize/capacity" << endl;
        
        cout << "\nDynamic Array Stack:" << endl;
        cout << "- Array storage: O(n) where n is current capacity" << endl;
        cout << "- Worst-case waste: 50% (just after shrinking)" << endl;
        cout << "- Best-case waste: 0% (when full)" << endl;
        cout << "- Average space efficiency: ~75%" << endl;
        
        cout << "\nMemory Characteristics:" << endl;
        cout << "- Excellent cache locality (contiguous memory)" << endl;
        cout << "- Predictable memory access patterns" << endl;
        cout << "- Low memory overhead per element" << endl;
        cout << "- Suitable for high-performance applications" << endl;
    }
    
    void compareWithOtherImplementations() {
        cout << "\n=== Comparison with Other Stack Implementations ===" << endl;
        
        cout << "Feature                | Array Stack | Linked List | Dynamic Array" << endl;
        cout << "----------------------|-------------|-------------|---------------" << endl;
        cout << "Memory allocation     | Static      | Dynamic     | Semi-dynamic" << endl;
        cout << "Cache performance     | Excellent   | Poor        | Good" << endl;
        cout << "Memory overhead       | Low         | High        | Medium" << endl;
        cout << "Resize capability     | No          | Natural     | Yes" << endl;
        cout << "Access time           | O(1)        | O(1)        | O(1)" << endl;
        cout << "Memory fragmentation  | None        | Possible    | Minimal" << endl;
        cout << "Suitable for          | Fixed size  | Unknown size| Variable size" << endl;
    }
};
```

---

## 1.3 Stack Implementation Using Linked List

### 1.3.1 Design Philosophy and Architecture

#### 1.3.1.1 Linked List Approach Rationale
Linked list implementation provides dynamic memory allocation, unlimited capacity (within system limits), and efficient memory usage by allocating only the required space. Each stack element is stored in a node with a pointer to the next element.

#### 1.3.1.2 Memory Structure Visualization
```
Linked List Stack Structure:
top → [Data|Next] → [Data|Next] → [Data|NULL]
      (Node 3)      (Node 2)      (Node 1)
      
Memory Layout:
- Nodes can be anywhere in memory
- Each node: sizeof(T) + sizeof(pointer)
- No wasted space for unused capacity
- Dynamic allocation as needed
```

### 1.3.2 Comprehensive Linked List Implementation

#### 1.3.2.1 Node Structure and Stack Class

```cpp
#include <iostream>
#include <memory>
#include <stdexcept>
using namespace std;

// 1.3.2.1.1 Node structure template
template<typename T>
struct StackNode {
    T data;
    StackNode* next;
    
    // Constructor
    StackNode(const T& value, StackNode* nextNode = nullptr) 
        : data(value), next(nextNode) {
        cout << "Node created with value: " << value << " at address: " << this << endl;
    }
    
    // Destructor
    ~StackNode() {
        cout << "Node with value " << data << " destroyed at address: " << this << endl;
    }
};

// 1.3.2.1.2 Comprehensive Linked List Stack
template<typename T>
class LinkedListStack {
private:
    StackNode<T>* topNode;
    int currentSize;
    
    // Statistics tracking
    mutable long long totalAllocations;
    mutable long long totalDeallocations;
    mutable long long maxSize;
    
public:
    // 1.3.2.1.3 Constructor
    LinkedListStack() : topNode(nullptr), currentSize(0), 
                       totalAllocations(0), totalDeallocations(0), maxSize(0) {
        cout << "Linked List Stack created (initially empty)" << endl;
    }
    
    // 1.3.2.1.4 Destructor with complete cleanup
    ~LinkedListStack() {
        cout << "\nDestroying Linked List Stack..." << endl;
        clear();
        displayFinalStatistics();
    }
    
    // 1.3.2.1.5 Push operation - O(1)
    void push(const T& element) {
        cout << "\n=== PUSH Operation (Linked List) ===" << endl;
        cout << "Pushing element: " << element << endl;
        
        // Display state before push
        cout << "Before push - Size: " << currentSize << endl;
        if (topNode) {
            cout << "Current top: " << topNode->data << " at " << topNode << endl;
        } else {
            cout << "Stack is currently empty" << endl;
        }
        
        // Create new node
        StackNode<T>* newNode = new StackNode<T>(element, topNode);
        totalAllocations++;
        
        // Update top pointer
        topNode = newNode;
        currentSize++;
        maxSize = max(maxSize, (long long)currentSize);
        
        // Display state after push
        cout << "After push - Size: " << currentSize << endl;
        cout << "New top: " << topNode->data << " at " << topNode << endl;
        cout << "Push operation completed successfully" << endl;
        
        displayMemoryInfo();
    }
    
    // 1.3.2.1.6 Pop operation - O(1)
    T pop() {
        cout << "\n=== POP Operation (Linked List) ===" << endl;
        
        if (isEmpty()) {
            cout << "Error: Attempting to pop from empty stack" << endl;
            throw underflow_error("Stack is empty");
        }
        
        // Display state before pop
        cout << "Before pop - Size: " << currentSize << endl;
        cout << "Current top: " << topNode->data << " at " << topNode << endl;
        
        // Save data and node pointer
        T poppedData = topNode->data;
        StackNode<T>* nodeToDelete = topNode;
        
        // Update top pointer
        topNode = topNode->next;
        currentSize--;
        
        // Delete the node
        delete nodeToDelete;
        totalDeallocations++;
        
        // Display state after pop
        cout << "Popped element: " << poppedData << endl;
        cout << "After pop - Size: " << currentSize << endl;
        if (topNode) {
            cout << "New top: " << topNode->data << " at " << topNode << endl;
        } else {
            cout << "Stack is now empty" << endl;
        }
        
        displayMemoryInfo();
        return poppedData;
    }
    
    // 1.3.2.1.7 Top/Peek operation - O(1)
    T top() const {
        cout << "\n=== TOP/PEEK Operation (Linked List) ===" << endl;
        
        if (isEmpty()) {
            cout << "Error: Attempting to peek into empty stack" << endl;
            throw underflow_error("Stack is empty");
        }
        
        cout << "Top element: " << topNode->data << " at address " << topNode << endl;
        return topNode->data;
    }
    
    // 1.3.2.1.8 Status operations
    bool isEmpty() const {
        bool empty = (topNode == nullptr);
        cout << "isEmpty() check: " << (empty ? "true" : "false") 
             << " (topNode=" << topNode << ", size=" << currentSize << ")" << endl;
        return empty;
    }
    
    int size() const {
        cout << "Current stack size: " << currentSize << endl;
        return currentSize;
    }
    
    // 1.3.2.1.9 Clear operation
    void clear() {
        cout << "\n=== CLEAR Operation (Linked List) ===" << endl;
        cout << "Clearing stack with " << currentSize << " elements" << endl;
        
        int deletedCount = 0;
        while (topNode != nullptr) {
            StackNode<T>* nodeToDelete = topNode;
            topNode = topNode->next;
            delete nodeToDelete;
            totalDeallocations++;
            deletedCount++;
        }
        
        currentSize = 0;
        cout << "Successfully deleted " << deletedCount << " nodes" << endl;
        cout << "Stack is now empty" << endl;
    }
    
    // 1.3.2.1.10 Display operations
    void display() const {
        cout << "\n=== Stack Display (Linked List) ===" << endl;
        
        if (isEmpty()) {
            cout << "Stack is empty" << endl;
            return;
        }
        
        cout << "Stack contents (top to bottom):" << endl;
        StackNode<T>* current = topNode;
        int position = 0;
        
        while (current != nullptr) {
            cout << "Position " << position << ": " << current->data 
                 << " (address: " << current << ", next: " << current->next << ")";
            if (position == 0) {
                cout << " ← TOP";
            }
            cout << endl;
            
            current = current->next;
            position++;
        }
        
        cout << "Total elements: " << currentSize << endl;
    }
    
    void displayMemoryInfo() const {
        cout << "Memory Info - Allocations: " << totalAllocations 
             << ", Deallocations: " << totalDeallocations 
             << ", Active nodes: " << (totalAllocations - totalDeallocations) << endl;
    }
    
    void displayDetailedState() const {
        cout << "\n=== Detailed Stack State ===" << endl;
        cout << "Current size: " << currentSize << endl;
        cout << "Maximum size reached: " << maxSize << endl;
        cout << "Top node address: " << topNode << endl;
        
        if (topNode) {
            cout << "Top node data: " << topNode->data << endl;
            cout << "Top node next pointer: " << topNode->next << endl;
        }
        
        displayMemoryInfo();
        
        // Memory usage estimation
        size_t nodeSize = sizeof(StackNode<T>);
        size_t totalMemory = currentSize * nodeSize;
        cout << "Estimated memory usage: " << totalMemory << " bytes" << endl;
        cout << "Memory per node: " << nodeSize << " bytes" << endl;
    }
    
private:
    void displayFinalStatistics() const {
        cout << "\n=== Final Stack Statistics ===" << endl;
        cout << "Total allocations performed: " << totalAllocations << endl;
        cout << "Total deallocations performed: " << totalDeallocations << endl;
        cout << "Maximum size reached: " << maxSize << endl;
        cout << "Memory leaks: " << (totalAllocations - totalDeallocations) << " nodes" << endl;
        
        if (totalAllocations - totalDeallocations > 0) {
            cout << "WARNING: Potential memory leak detected!" << endl;
        } else {
            cout << "Memory management: Clean (no leaks detected)" << endl;
        }
    }
};
```

### 1.3.3 Advanced Linked List Features

#### 1.3.3.1 Smart Pointer Implementation

```cpp
// 1.3.3.1.1 Modern C++ approach with smart pointers
template<typename T>
class SmartPointerStack {
private:
    struct Node {
        T data;
        unique_ptr<Node> next;
        
        Node(const T& value) : data(value), next(nullptr) {
            cout << "Smart pointer node created with value: " << value << endl;
        }
        
        ~Node() {
            cout << "Smart pointer node with value " << data << " destroyed" << endl;
        }
    };
    
    unique_ptr<Node> topNode;
    int currentSize;
    
public:
    SmartPointerStack() : topNode(nullptr), currentSize(0) {
        cout << "Smart Pointer Stack created" << endl;
    }
    
    ~SmartPointerStack() {
        cout << "Smart Pointer Stack destroyed (automatic cleanup)" << endl;
        // Automatic cleanup via smart pointers
    }
    
    void push(const T& element) {
        cout << "Smart push: " << element << endl;
        
        auto newNode = make_unique<Node>(element);
        newNode->next = move(topNode);
        topNode = move(newNode);
        currentSize++;
        
        cout << "Smart push completed, size: " << currentSize << endl;
    }
    
    T pop() {
        if (!topNode) {
            throw underflow_error("Stack is empty");
        }
        
        T data = topNode->data;
        topNode = move(topNode->next);  // Automatic cleanup of old top
        currentSize--;
        
        cout << "Smart pop: " << data << ", size: " << currentSize << endl;
        return data;
    }
    
    T top() const {
        if (!topNode) {
            throw underflow_error("Stack is empty");
        }
        return topNode->data;
    }
    
    bool isEmpty() const {
        return topNode == nullptr;
    }
    
    int size() const {
        return currentSize;
    }
    
    void display() const {
        cout << "\nSmart Pointer Stack contents:" << endl;
        Node* current = topNode.get();
        int pos = 0;
        
        while (current) {
            cout << "Position " << pos << ": " << current->data;
            if (pos == 0) cout << " ← TOP";
            cout << endl;
            current = current->next.get();
            pos++;
        }
    }
};
```

#### 1.3.3.2 Thread-Safe Linked List Stack

```cpp
// 1.3.3.2.1 Thread-safe implementation
#include <mutex>
#include <atomic>

template<typename T>
class ThreadSafeLinkedStack {
private:
    struct Node {
        T data;
        Node* next;
        
        Node(const T& value, Node* nextNode = nullptr) 
            : data(value), next(nextNode) {}
    };
    
    Node* topNode;
    atomic<int> currentSize;
    mutable mutex stackMutex;
    
public:
    ThreadSafeLinkedStack() : topNode(nullptr), currentSize(0) {
        cout << "Thread-safe Linked Stack created" << endl;
    }
    
    ~ThreadSafeLinkedStack() {
        clear();
        cout << "Thread-safe Linked Stack destroyed" << endl;
    }
    
    void push(const T& element) {
        lock_guard<mutex> lock(stackMutex);
        
        Node* newNode = new Node(element, topNode);
        topNode = newNode;
        currentSize++;
        
        cout << "Thread-safe push: " << element << " (size: " << currentSize << ")" << endl;
    }
    
    T pop() {
        lock_guard<mutex> lock(stackMutex);
        
        if (topNode == nullptr) {
            throw underflow_error("Stack is empty");
        }
        
        T data = topNode->data;
        Node* nodeToDelete = topNode;
        topNode = topNode->next;
        delete nodeToDelete;
        currentSize--;
        
        cout << "Thread-safe pop: " << data << " (size: " << currentSize << ")" << endl;
        return data;
    }
    
    T top() const {
        lock_guard<mutex> lock(stackMutex);
        
        if (topNode == nullptr) {
            throw underflow_error("Stack is empty");
        }
        
        return topNode->data;
    }
    
    bool isEmpty() const {
        lock_guard<mutex> lock(stackMutex);
        return topNode == nullptr;
    }
    
    int size() const {
        return currentSize.load();
    }
    
    void clear() {
        lock_guard<mutex> lock(stackMutex);
        
        while (topNode != nullptr) {
            Node* nodeToDelete = topNode;
            topNode = topNode->next;
            delete nodeToDelete;
        }
        currentSize = 0;
    }
};
```

### 1.3.4 Performance Analysis and Testing

#### 1.3.4.1 Comprehensive Testing Suite

```cpp
// 1.3.4.1.1 Linked List Stack Testing
void testLinkedListStackImplementation() {
    cout << "=== Linked List Stack Implementation Testing ===" << endl;
    
    // Test Case 1: Basic Operations
    cout << "\n--- Test Case 1: Basic Operations ---" << endl;
    try {
        LinkedListStack<string> stringStack;
        
        // Test with string data
        vector<string> testData = {"First", "Second", "Third", "Fourth"};
        
        cout << "Pushing strings:" << endl;
        for (const string& str : testData) {
            stringStack.push(str);
        }
        
        stringStack.display();
        stringStack.displayDetailedState();
        
        cout << "\nPopping strings:" << endl;
        while (!stringStack.isEmpty()) {
            cout << "Popped: " << stringStack.pop() << endl;
        }
        
    } catch (const exception& e) {
        cout << "Exception caught: " << e.what() << endl;
    }
    
    // Test Case 2: Large Data Set Performance
    cout << "\n--- Test Case 2: Performance with Large Dataset ---" << endl;
    {
        LinkedListStack<int> perfStack;
        
        auto start = chrono::high_resolution_clock::now();
        
        // Push 1000 elements
        for (int i = 0; i < 1000; i++) {
            perfStack.push(i);
        }
        
        // Pop all elements
        while (!perfStack.isEmpty()) {
            perfStack.pop();
        }
        
        auto end = chrono::high_resolution_clock::now();
        auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
        
        cout << "1000 push + 1000 pop operations completed in: " 
             << duration.count() << " microseconds" << endl;
    }
    
    // Test Case 3: Smart Pointer Implementation
    cout << "\n--- Test Case 3: Smart Pointer Implementation ---" << endl;
    {
        SmartPointerStack<double> smartStack;
        
        vector<double> values = {3.14, 2.718, 1.414, 0.577};
        
        for (double val : values) {
            smartStack.push(val);
        }
        
        smartStack.display();
        
        while (!smartStack.isEmpty()) {
            cout << "Smart popped: " << smartStack.pop() << endl;
        }
    }
    
    // Test Case 4: Memory Management Verification
    cout << "\n--- Test Case 4: Memory Management ---" << endl;
    {
        LinkedListStack<int> memStack;
        
        // Push some elements
        for (int i = 1; i <= 5; i++) {
            memStack.push(i * 100);
        }
        
        memStack.displayDetailedState();
        
        // Pop some elements
        for (int i = 0; i < 3; i++) {
            memStack.pop();
        }
        
        memStack.displayDetailedState();
        
        // Clear remaining
        memStack.clear();
        memStack.displayDetailedState();
    }
    
    // Test Case 5: Exception Handling
    cout << "\n--- Test Case 5: Exception Handling ---" << endl;
    {
        LinkedListStack<char> emptyStack;
        
        try {
            emptyStack.top(); // Should throw
        } catch (const underflow_error& e) {
            cout << "Caught expected exception for top(): " << e.what() << endl;
        }
        
        try {
            emptyStack.pop(); // Should throw
        } catch (const underflow_error& e) {
            cout << "Caught expected exception for pop(): " << e.what() << endl;
        }
    }
}
```

#### 1.3.4.2 Complexity and Performance Analysis

```cpp
// 1.3.4.2.1 Detailed complexity analysis
class LinkedListStackAnalysis {
public:
    void analyzeComplexity() {
        cout << "=== Linked List Stack Complexity Analysis ===" << endl;
        
        cout << "\nTime Complexity:" << endl;
        cout << "Operation | Complexity | Explanation" << endl;
        cout << "----------|------------|-------------" << endl;
        cout << "push()    | O(1)       | Create node and update top pointer" << endl;
        cout << "pop()     | O(1)       | Delete top node and update pointer" << endl;
        cout << "top()     | O(1)       | Access top node data directly" << endl;
        cout << "isEmpty() | O(1)       | Check if top pointer is null" << endl;
        cout << "size()    | O(1)       | Return stored size counter" << endl;
        cout << "clear()   | O(n)       | Delete all n nodes sequentially" << endl;
        cout << "display() | O(n)       | Traverse all n nodes" << endl;
        
        cout << "\nSpace Complexity:" << endl;
        cout << "- Node overhead: sizeof(T) + sizeof(pointer) per element" << endl;
        cout << "- Total space: O(n) where n is number of elements" << endl;
        cout << "- No wasted space (unlike fixed arrays)" << endl;
        cout << "- Memory fragmentation possible" << endl;
        
        cout << "\nMemory Characteristics:" << endl;
        cout << "- Allocation: Dynamic (as needed)" << endl;
        cout << "- Deallocation: Immediate (when popped)" << endl;
        cout << "- Cache performance: Poor (non-contiguous memory)" << endl;
        cout << "- Memory overhead: ~50% higher than arrays" << endl;
    }
    
    void compareImplementations() {
        cout << "\n=== Implementation Comparison ===" << endl;
        
        cout << "Aspect               | Array Stack | Linked List | Smart Pointer" << endl;
        cout << "---------------------|-------------|-------------|---------------" << endl;
        cout << "Memory allocation    | Fixed       | Dynamic     | Dynamic" << endl;
        cout << "Memory overhead      | Low         | Medium      | Medium" << endl;
        cout << "Cache performance    | Excellent   | Poor        | Poor" << endl;
        cout << "Memory safety        | Manual      | Manual      | Automatic" << endl;
        cout << "Thread safety        | None        | None        | None*" << endl;
        cout << "Resize capability    | Limited     | Unlimited   | Unlimited" << endl;
        cout << "Initialization cost  | O(1)        | O(1)        | O(1)" << endl;
        cout << "Destruction cost     | O(1)        | O(n)        | O(n)" << endl;
        
        cout << "\n* Thread-safe version available with mutex" << endl;
        
        cout << "\nRecommendations:" << endl;
        cout << "- Use Array Stack when: Fixed size, high performance needed" << endl;
        cout << "- Use Linked List when: Unknown size, frequent resizing" << endl;
        cout << "- Use Smart Pointer when: Memory safety is critical" << endl;
        cout << "- Use Thread-Safe when: Multi-threaded environment" << endl;
    }
    
    void benchmarkPerformance() {
        cout << "\n=== Performance Benchmark ===" << endl;
        
        const int OPERATIONS = 10000;
        
        // Benchmark Array Stack
        {
            ArrayStack<int> arrayStack(OPERATIONS);
            auto start = chrono::high_resolution_clock::now();
            
            for (int i = 0; i < OPERATIONS; i++) {
                arrayStack.push(i);
            }
            for (int i = 0; i < OPERATIONS; i++) {
                arrayStack.pop();
            }
            
            auto end = chrono::high_resolution_clock::now();
            auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
            cout << "Array Stack (" << OPERATIONS << " ops): " << duration.count() << " μs" << endl;
        }
        
        // Benchmark Linked List Stack
        {
            LinkedListStack<int> linkedStack;
            auto start = chrono::high_resolution_clock::now();
            
            for (int i = 0; i < OPERATIONS; i++) {
                linkedStack.push(i);
            }
            for (int i = 0; i < OPERATIONS; i++) {
                linkedStack.pop();
            }
            
            auto end = chrono::high_resolution_clock::now();
            auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
            cout << "Linked List Stack (" << OPERATIONS << " ops): " << duration.count() << " μs" << endl;
        }
        
        // Benchmark Smart Pointer Stack
        {
            SmartPointerStack<int> smartStack;
            auto start = chrono::high_resolution_clock::now();
            
            for (int i = 0; i < OPERATIONS; i++) {
                smartStack.push(i);
            }
            for (int i = 0; i < OPERATIONS; i++) {
                smartStack.pop();
            }
            
            auto end = chrono::high_resolution_clock::now();
            auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
            cout << "Smart Pointer Stack (" << OPERATIONS << " ops): " << duration.count() << " μs" << endl;
        }
    }
};
```

---

## 1.4 Introduction to Queue Data Structure

### 1.4.1 Theoretical Foundation and Definition

#### 1.4.1.1 Formal Definition
A **Queue** is a linear data structure that follows the First In First Out (FIFO) principle. Elements are added at one end (rear/back) and removed from the other end (front). The queue operates on the principle that the first element inserted is the first element to be removed.

#### 1.4.1.2 Mathematical Model
A queue can be formally defined as an Abstract Data Type (ADT) with the following operations:
- **Q**: Set of elements in the queue
- **front**: Pointer to the first element
- **rear**: Pointer to the last element
- **enqueue(x)**: Add element x to the rear of the queue
- **dequeue()**: Remove and return element from the front
- **front()**: Return front element without removal
- **isEmpty()**: Return true if queue is empty

#### 1.4.1.3 Queue vs Stack Comparison

```cpp
// 1.4.1.3.1 Conceptual Comparison
class QueueStackComparison {
public:
    void displayComparison() {
        cout << "=== Queue vs Stack Comparison ===" << endl;
        
        cout << "\nCharacteristic | Queue (FIFO) | Stack (LIFO)" << endl;
        cout << "---------------|--------------|-------------" << endl;
        cout << "Order          | First In First Out | Last In First Out" << endl;
        cout << "Insertion      | Rear (back)        | Top" << endl;
        cout << "Deletion       | Front              | Top" << endl;
        cout << "Access Points  | Two (front, rear)  | One (top)" << endl;
        cout << "Applications   | Scheduling, BFS    | Recursion, DFS" << endl;
        cout << "Real-world     | Line of people     | Stack of plates" << endl;
        
        cout << "\nVisualization:" << endl;
        cout << "Queue:  [1][2][3][4]  →  enqueue(5)  →  [1][2][3][4][5]" << endl;
        