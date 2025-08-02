# 🔹 Block 5: Recursion & Stack Relation – Deep Dive into Call Stack Mechanics

## 5.0 Block Overview and Learning Objectives

### 5.0.1 Introduction to Block 5
Block 5 explores the fundamental relationship between recursion and stack data structures, providing deep insights into how recursive algorithms utilize the call stack. This module bridges theoretical concepts with practical implementation strategies and optimization techniques.

### 5.0.2 Learning Objectives
By the end of Block 5, students will be able to:
- Understand the intricate relationship between recursion and stack memory
- Analyze tail vs non-tail recursion and their stack implications
- Convert recursive algorithms to iterative implementations using explicit stacks
- Implement and optimize expression evaluation algorithms
- Design stack-based parsers for mathematical expressions
- Optimize recursive algorithms for better space complexity

### 5.0.3 Prerequisites
- Solid understanding of stack data structures from previous blocks
- Knowledge of recursive programming concepts
- Familiarity with function call mechanisms and memory management
- Understanding of mathematical expression parsing

### 5.0.4 Block Structure Overview
```
Block 5 Timeline:
Day 1: How Recursion Uses Stack
Day 2: Tail vs Non-tail Recursion (Stack Usage Analysis)
Day 3: Convert Recursion to Iterative using Stack
Day 4: Infix to Postfix Conversion using Stack
Day 5: Evaluate Postfix Expression
Day 6: Evaluate Prefix Expression
Day 7: Comprehensive Revision & Advanced Applications
```

---

## 5.1 How Recursion Uses Stack – Call Stack Mechanics

### 5.1.1 Theoretical Foundation of Call Stack

#### 5.1.1.1 Call Stack Architecture and Memory Layout
The call stack is a fundamental data structure in computer systems that manages function calls, local variables, and return addresses. Each function call creates a new stack frame containing:

```cpp
// 5.1.1.1.1 Stack Frame Structure Visualization
/*
Stack Frame Layout:
+------------------+  ← High Memory Address
| Return Address   |  (Where to return after function completes)
+------------------+
| Previous Frame   |  (Frame pointer of calling function)
| Pointer          |
+------------------+
| Local Variables  |  (Function's local data)
+------------------+
| Parameters       |  (Function arguments)
+------------------+
| Saved Registers  |  (CPU state preservation)
+------------------+  ← Current Frame Pointer
| ...              |
+------------------+  ← Low Memory Address

Call Stack Growth Direction: High → Low Memory Addresses
*/
```

#### 5.1.1.2 Recursion Memory Model

```cpp
#include <iostream>
#include <vector>
#include <iomanip>
#include <chrono>
#include <thread>
using namespace std;

// 5.1.1.2.1 Call Stack Simulation and Analysis
class CallStackSimulator {
private:
    struct StackFrame {
        string functionName;
        vector<pair<string, int>> localVariables;
        vector<pair<string, int>> parameters;
        int frameNumber;
        size_t memoryAddress;
        
        StackFrame(const string& name, int frameNum) 
            : functionName(name), frameNumber(frameNum) {
            // Simulate memory address (for demonstration)
            memoryAddress = 0x7FFF0000 - (frameNum * 0x100);
        }
        
        void addParameter(const string& name, int value) {
            parameters.push_back({name, value});
        }
        
        void addLocalVariable(const string& name, int value) {
            localVariables.push_back({name, value});
        }
        
        void display(int indentLevel = 0) const {
            string indent(indentLevel * 2, ' ');
            cout << indent << "Frame #" << frameNumber << " - " << functionName 
                 << " (0x" << hex << memoryAddress << dec << ")" << endl;
            
            if (!parameters.empty()) {
                cout << indent << "  Parameters: ";
                for (const auto& param : parameters) {
                    cout << param.first << "=" << param.second << " ";
                }
                cout << endl;
            }
            
            if (!localVariables.empty()) {
                cout << indent << "  Local vars: ";
                for (const auto& var : localVariables) {
                    cout << var.first << "=" << var.second << " ";
                }
                cout << endl;
            }
        }
    };
    
    vector<StackFrame> callStack;
    int frameCounter;
    
public:
    CallStackSimulator() : frameCounter(0) {
        cout << "Call Stack Simulator initialized" << endl;
    }
    
    // 5.1.1.2.2 Push new frame onto call stack
    void pushFrame(const string& functionName) {
        StackFrame frame(functionName, frameCounter++);
        callStack.push_back(frame);
        
        cout << "\n>>> CALL: " << functionName << " (Frame #" << frame.frameNumber << ")" << endl;
        displayCurrentStack();
    }
    
    // 5.1.1.2.3 Pop frame from call stack
    void popFrame() {
        if (!callStack.empty()) {
            StackFrame frame = callStack.back();
            callStack.pop_back();
            
            cout << "\n<<< RETURN from: " << frame.functionName 
                 << " (Frame #" << frame.frameNumber << ")" << endl;
            displayCurrentStack();
        }
    }
    
    // 5.1.1.2.4 Add parameter to current frame
    void addParameter(const string& name, int value) {
        if (!callStack.empty()) {
            callStack.back().addParameter(name, value);
        }
    }
    
    // 5.1.1.2.5 Add local variable to current frame
    void addLocalVariable(const string& name, int value) {
        if (!callStack.empty()) {
            callStack.back().addLocalVariable(name, value);
        }
    }
    
    // 5.1.1.2.6 Display current call stack state
    void displayCurrentStack() const {
        cout << "Call Stack (depth=" << callStack.size() << "):" << endl;
        if (callStack.empty()) {
            cout << "  [Empty]" << endl;
            return;
        }
        
        // Display from top to bottom (most recent call first)
        for (int i = callStack.size() - 1; i >= 0; i--) {
            callStack[i].display(callStack.size() - 1 - i);
        }
        
        cout << "Stack memory usage: " << calculateMemoryUsage() << " bytes" << endl;
    }
    
    // 5.1.1.2.7 Calculate estimated memory usage
    size_t calculateMemoryUsage() const {
        // Rough estimation: each frame uses ~100 bytes
        return callStack.size() * 100;
    }
    
    // 5.1.1.2.8 Get current stack depth
    int getStackDepth() const {
        return callStack.size();
    }
};

// 5.1.1.3 Recursive Function Analysis with Stack Tracking
class RecursiveStackAnalysis {
private:
    CallStackSimulator simulator;
    int maxStackDepth;
    
public:
    RecursiveStackAnalysis() : maxStackDepth(0) {}
    
    // 5.1.1.3.1 Factorial with stack tracking
    long long factorialWithStackTracking(int n) {
        cout << "\n=== Factorial Recursion Stack Analysis ===" << endl;
        cout << "Computing factorial(" << n << ")" << endl;
        
        maxStackDepth = 0;
        long long result = factorialHelper(n);
        
        cout << "\nRecursion completed!" << endl;
        cout << "Result: " << result << endl;
        cout << "Maximum stack depth reached: " << maxStackDepth << endl;
        
        return result;
    }
    
private:
    long long factorialHelper(int n) {
        simulator.pushFrame("factorial");
        simulator.addParameter("n", n);
        
        maxStackDepth = max(maxStackDepth, simulator.getStackDepth());
        
        // Base case
        if (n <= 1) {
            simulator.addLocalVariable("result", 1);
            cout << "Base case reached: n=" << n << ", returning 1" << endl;
            
            simulator.popFrame();
            return 1;
        }
        
        // Recursive case
        cout << "Recursive case: n=" << n << ", computing factorial(" << (n-1) << ")" << endl;
        long long recursiveResult = factorialHelper(n - 1);
        
        long long result = n * recursiveResult;
        simulator.addLocalVariable("result", result);
        
        cout << "Computed: " << n << " * " << recursiveResult << " = " << result << endl;
        
        simulator.popFrame();
        return result;
    }
    
public:
    // 5.1.1.3.2 Fibonacci with detailed stack analysis
    long long fibonacciWithStackTracking(int n) {
        cout << "\n=== Fibonacci Recursion Stack Analysis ===" << endl;
        cout << "Computing fibonacci(" << n << ")" << endl;
        
        maxStackDepth = 0;
        int callCount = 0;
        long long result = fibonacciHelper(n, callCount);
        
        cout << "\nFibonacci recursion completed!" << endl;
        cout << "Result: " << result << endl;
        cout << "Maximum stack depth: " << maxStackDepth << endl;
        cout << "Total function calls: " << callCount << endl;
        
        return result;
    }
    
private:
    long long fibonacciHelper(int n, int& callCount) {
        callCount++;
        
        simulator.pushFrame("fibonacci");
        simulator.addParameter("n", n);
        
        maxStackDepth = max(maxStackDepth, simulator.getStackDepth());
        
        // Base cases
        if (n <= 1) {
            simulator.addLocalVariable("result", n);
            cout << "Base case: fibonacci(" << n << ") = " << n << endl;
            
            simulator.popFrame();
            return n;
        }
        
        // Recursive case
        cout << "Computing fibonacci(" << n << ") = fibonacci(" << (n-1) 
             << ") + fibonacci(" << (n-2) << ")" << endl;
        
        long long fib1 = fibonacciHelper(n - 1, callCount);
        long long fib2 = fibonacciHelper(n - 2, callCount);
        
        long long result = fib1 + fib2;
        simulator.addLocalVariable("result", result);
        
        cout << "Computed: fibonacci(" << n << ") = " << fib1 << " + " << fib2 << " = " << result << endl;
        
        simulator.popFrame();
        return result;
    }
    
public:
    // 5.1.1.3.3 Binary tree traversal with stack analysis
    struct TreeNode {
        int val;
        TreeNode* left;
        TreeNode* right;
        
        TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    };
    
    void inorderTraversalWithStackTracking(TreeNode* root) {
        cout << "\n=== Binary Tree Inorder Traversal Stack Analysis ===" << endl;
        
        if (!root) {
            cout << "Empty tree" << endl;
            return;
        }
        
        maxStackDepth = 0;
        vector<int> result;
        
        inorderHelper(root, result);
        
        cout << "\nTraversal completed!" << endl;
        cout << "Inorder sequence: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        cout << "Maximum stack depth: " << maxStackDepth << endl;
    }
    
private:
    void inorderHelper(TreeNode* node, vector<int>& result) {
        if (!node) return;
        
        simulator.pushFrame("inorder");
        simulator.addParameter("node_val", node->val);
        
        maxStackDepth = max(maxStackDepth, simulator.getStackDepth());
        
        cout << "Visiting node " << node->val << endl;
        
        // Left subtree
        if (node->left) {
            cout << "Going to left child of " << node->val << endl;
            inorderHelper(node->left, result);
        }
        
        // Process current node
        result.push_back(node->val);
        cout << "Processing node " << node->val << endl;
        
        // Right subtree
        if (node->right) {
            cout << "Going to right child of " << node->val << endl;
            inorderHelper(node->right, result);
        }
        
        cout << "Returning from node " << node->val << endl;
        simulator.popFrame();
    }
    
public:
    // 5.1.1.3.4 Memory usage analysis
    void analyzeStackGrowth() {
        cout << "\n=== Stack Growth Analysis ===" << endl;
        
        cout << "Testing factorial recursion depths:" << endl;
        for (int n : {5, 10, 15, 20}) {
            cout << "\nFactorial(" << n << "):" << endl;
            
            auto start = chrono::high_resolution_clock::now();
            factorialWithStackTracking(n);
            auto end = chrono::high_resolution_clock::now();
            
            auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
            cout << "Time taken: " << duration.count() << " microseconds" << endl;
            cout << "Estimated stack memory: " << maxStackDepth * 100 << " bytes" << endl;
        }
        
        cout << "\nStack Overflow Risk Analysis:" << endl;
        cout << "- Typical stack size: 1-8 MB" << endl;
        cout << "- Frame size estimate: ~100 bytes" << endl;
        cout << "- Safe recursion depth: ~10,000-80,000 calls" << endl;
        cout << "- Deep recursion (>1000) requires tail call optimization" << endl;
    }
};
```

### 5.1.2 Advanced Stack Frame Analysis

#### 5.1.2.1 Stack Frame Lifecycle and Memory Management

```cpp
// 5.1.2.1.1 Detailed Stack Frame Lifecycle
class StackFrameLifecycle {
public:
    struct DetailedFrame {
        string functionName;
        vector<string> parameters;
        vector<string> localVariables;
        string returnAddress;
        size_t frameSize;
        chrono::time_point<chrono::high_resolution_clock> creationTime;
        chrono::time_point<chrono::high_resolution_clock> destructionTime;
        
        DetailedFrame(const string& name) : functionName(name) {
            creationTime = chrono::high_resolution_clock::now();
            frameSize = 64; // Base frame size
        }
        
        void addParameter(const string& param) {
            parameters.push_back(param);
            frameSize += param.length() + 8; // Rough estimation
        }
        
        void addLocalVariable(const string& var) {
            localVariables.push_back(var);
            frameSize += var.length() + 8;
        }
        
        void markDestruction() {
            destructionTime = chrono::high_resolution_clock::now();
        }
        
        long long getLifetimeNanoseconds() const {
            if (destructionTime.time_since_epoch().count() == 0) {
                return 0; // Not yet destroyed
            }
            return chrono::duration_cast<chrono::nanoseconds>(
                destructionTime - creationTime).count();
        }
        
        void displayLifecycle() const {
            cout << "Frame: " << functionName << endl;
            cout << "  Size: " << frameSize << " bytes" << endl;
            cout << "  Parameters: " << parameters.size() << endl;
            cout << "  Local variables: " << localVariables.size() << endl;
            cout << "  Lifetime: " << getLifetimeNanoseconds() << " ns" << endl;
        }
    };
    
private:
    vector<DetailedFrame> frameHistory;
    vector<DetailedFrame> activeFrames;
    
public:
    // 5.1.2.1.2 Create and track new frame
    void createFrame(const string& functionName) {
        DetailedFrame frame(functionName);
        activeFrames.push_back(frame);
        
        cout << "Created frame for " << functionName 
             << " (Active frames: " << activeFrames.size() << ")" << endl;
    }
    
    // 5.1.2.1.3 Destroy frame and record lifecycle
    void destroyFrame() {
        if (!activeFrames.empty()) {
            DetailedFrame frame = activeFrames.back();
            frame.markDestruction();
            
            frameHistory.push_back(frame);
            activeFrames.pop_back();
            
            cout << "Destroyed frame for " << frame.functionName 
                 << " (Active frames: " << activeFrames.size() << ")" << endl;
        }
    }
    
    // 5.1.2.1.4 Add tracking information
    void addParameter(const string& param) {
        if (!activeFrames.empty()) {
            activeFrames.back().addParameter(param);
        }
    }
    
    void addLocalVariable(const string& var) {
        if (!activeFrames.empty()) {
            activeFrames.back().addLocalVariable(var);
        }
    }
    
    // 5.1.2.1.5 Analysis methods
    void analyzeFrameLifecycles() {
        cout << "\n=== Frame Lifecycle Analysis ===" << endl;
        
        long long totalLifetime = 0;
        size_t totalFrameSize = 0;
        
        for (const auto& frame : frameHistory) {
            frame.displayLifecycle();
            totalLifetime += frame.getLifetimeNanoseconds();
            totalFrameSize += frame.frameSize;
        }
        
        if (!frameHistory.empty()) {
            cout << "\nSummary:" << endl;
            cout << "Total frames created: " << frameHistory.size() << endl;
            cout << "Average frame lifetime: " << (totalLifetime / frameHistory.size()) << " ns" << endl;
            cout << "Average frame size: " << (totalFrameSize / frameHistory.size()) << " bytes" << endl;
            cout << "Total memory used: " << totalFrameSize << " bytes" << endl;
        }
    }
    
    size_t getCurrentStackSize() const {
        size_t totalSize = 0;
        for (const auto& frame : activeFrames) {
            totalSize += frame.frameSize;
        }
        return totalSize;
    }
    
    void displayCurrentStack() const {
        cout << "\nCurrent Active Stack:" << endl;
        cout << "Stack depth: " << activeFrames.size() << endl;
        cout << "Total memory: " << getCurrentStackSize() << " bytes" << endl;
        
        for (int i = activeFrames.size() - 1; i >= 0; i--) {
            cout << "  " << (activeFrames.size() - 1 - i) << ". " 
                 << activeFrames[i].functionName 
                 << " (" << activeFrames[i].frameSize << " bytes)" << endl;
        }
    }
};

// 5.1.2.2 Recursive Algorithm Analysis with Lifecycle Tracking
class RecursiveLifecycleAnalysis {
private:
    StackFrameLifecycle lifecycle;
    
public:
    // 5.1.2.2.1 Comprehensive recursive analysis
    void analyzeRecursiveAlgorithms() {
        cout << "=== Comprehensive Recursive Algorithm Analysis ===" << endl;
        
        // Test different recursive patterns
        cout << "\n1. Linear Recursion (Factorial):" << endl;
        int factResult = trackedFactorial(5);
        
        cout << "\n2. Binary Recursion (Fibonacci):" << endl;
        int fibResult = trackedFibonacci(5);
        
        cout << "\n3. Tree Recursion (Binary Tree):" << endl;
        TreeNode* root = createSampleTree();
        trackedTreeTraversal(root);
        
        // Analyze all frame lifecycles
        lifecycle.analyzeFrameLifecycles();
        
        // Cleanup
        deleteTree(root);
    }
    
private:
    // Helper structures and methods
    struct TreeNode {
        int val;
        TreeNode* left;
        TreeNode* right;
        TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    };
    
    TreeNode* createSampleTree() {
        TreeNode* root = new TreeNode(1);
        root->left = new TreeNode(2);
        root->right = new TreeNode(3);
        root->left->left = new TreeNode(4);
        root->left->right = new TreeNode(5);
        return root;
    }
    
    void deleteTree(TreeNode* root) {
        if (root) {
            deleteTree(root->left);
            deleteTree(root->right);
            delete root;
        }
    }
    
    int trackedFactorial(int n) {
        lifecycle.createFrame("factorial");
        lifecycle.addParameter("n=" + to_string(n));
        
        int result;
        if (n <= 1) {
            result = 1;
            lifecycle.addLocalVariable("result=" + to_string(result));
        } else {
            int recursiveResult = trackedFactorial(n - 1);
            result = n * recursiveResult;
            lifecycle.addLocalVariable("result=" + to_string(result));
        }
        
        lifecycle.destroyFrame();
        return result;
    }
    
    int trackedFibonacci(int n) {
        lifecycle.createFrame("fibonacci");
        lifecycle.addParameter("n=" + to_string(n));
        
        int result;
        if (n <= 1) {
            result = n;
            lifecycle.addLocalVariable("result=" + to_string(result));
        } else {
            int fib1 = trackedFibonacci(n - 1);
            int fib2 = trackedFibonacci(n - 2);
            result = fib1 + fib2;
            lifecycle.addLocalVariable("result=" + to_string(result));
        }
        
        lifecycle.destroyFrame();
        return result;
    }
    
    void trackedTreeTraversal(TreeNode* node) {
        if (!node) return;
        
        lifecycle.createFrame("treeTraversal");
        lifecycle.addParameter("nodeVal=" + to_string(node->val));
        
        // Inorder traversal
        if (node->left) {
            trackedTreeTraversal(node->left);
        }
        
        lifecycle.addLocalVariable("processed=" + to_string(node->val));
        
        if (node->right) {
            trackedTreeTraversal(node->right);
        }
        
        lifecycle.destroyFrame();
    }
};
```

---

## 5.2 Tail vs Non-tail Recursion – Stack Optimization Analysis

### 5.2.1 Theoretical Foundation and Definitions

#### 5.2.1.1 Tail Recursion Definition and Properties
**Tail Recursion** occurs when the recursive call is the last operation performed in the function. No computation follows the recursive call, allowing for potential stack optimization.

**Non-tail Recursion** requires maintaining the calling function's context because additional operations must be performed after the recursive call returns.

#### 5.2.1.2 Mathematical Analysis of Stack Usage

```cpp
// 5.2.1.2.1 Tail vs Non-tail Recursion Comparison Framework
class TailRecursionAnalysis {
public:
    struct RecursionMetrics {
        int maxStackDepth;
        int totalCalls;
        long long executionTimeNs;
        size_t memoryUsage;
        string recursionType;
        
        void display() const {
            cout << "Recursion Type: " << recursionType << endl;
            cout << "Max Stack Depth: " << maxStackDepth << endl;
            cout << "Total Function Calls: " << totalCalls << endl;
            cout << "Execution Time: " << executionTimeNs << " ns" << endl;
            cout << "Memory Usage: " << memoryUsage << " bytes" << endl;
            cout << "Memory per call: " << (memoryUsage / totalCalls) << " bytes" << endl;
        }
    };
    
    // 5.2.1.2.2 Non-tail recursive factorial
    RecursionMetrics analyzeNonTailFactorial(int n) {
        cout << "=== Non-Tail Recursive Factorial Analysis ===" << endl;
        cout << "Computing factorial(" << n << ") - Non-tail recursion" << endl;
        
        RecursionMetrics metrics;
        metrics.recursionType = "Non-tail";
        metrics.maxStackDepth = 0;
        metrics.totalCalls = 0;
        
        auto start = chrono::high_resolution_clock::now();
        
        long long result = nonTailFactorialHelper(n, metrics, 0);
        
        auto end = chrono::high_resolution_clock::now();
        metrics.executionTimeNs = chrono::duration_cast<chrono::nanoseconds>(end - start).count();
        metrics.memoryUsage = metrics.maxStackDepth * 64; // Estimate 64 bytes per frame
        
        cout << "Result: " << result << endl;
        metrics.display();
        
        return metrics;
    }
    
    // 5.2.1.2.3 Tail recursive factorial
    RecursionMetrics analyzeTailFactorial(int n) {
        cout << "\n=== Tail Recursive Factorial Analysis ===" << endl;
        cout << "Computing factorial(" << n << ") - Tail recursion" << endl;
        
        RecursionMetrics metrics;
        metrics.recursionType = "Tail";
        metrics.maxStackDepth = 0;
        metrics.totalCalls = 0;
        
        auto start = chrono::high_resolution_clock::now();
        
        long long result = tailFactorialHelper(n, 1, metrics, 0);
        
        auto end = chrono::high_resolution_clock::now();
        metrics.executionTimeNs = chrono::duration_cast<chrono::nanoseconds>(end - start).count();
        metrics.memoryUsage = metrics.maxStackDepth * 64;
        
        cout << "Result: " << result << endl;
        metrics.display();
        
        return metrics;
    }
    
    // 5.2.1.2.4 Iterative factorial for comparison
    RecursionMetrics analyzeIterativeFactorial(int n) {
        cout << "\n=== Iterative Factorial Analysis ===" << endl;
        cout << "Computing factorial(" << n << ") - Iterative approach" << endl;
        
        RecursionMetrics metrics;
        metrics.recursionType = "Iterative";
        metrics.maxStackDepth = 1; // Only one function frame
        metrics.totalCalls = 1;
        
        auto start = chrono::high_resolution_clock::now();
        
        long long result = 1;
        for (int i = 2; i <= n; i++) {
            result *= i;
        }
        
        auto end = chrono::high_resolution_clock::now();
        metrics.executionTimeNs = chrono::duration_cast<chrono::nanoseconds>(end - start).count();
        metrics.memoryUsage = 64; // Single frame
        
        cout << "Result: " << result << endl;
        metrics.display();
        
        return metrics;
    }
    
private:
    // Non-tail recursive implementation
    long long nonTailFactorialHelper(int n, RecursionMetrics& metrics, int depth) {
        metrics.totalCalls++;
        metrics.maxStackDepth = max(metrics.maxStackDepth, depth + 1);
        
        cout << string(depth * 2, ' ') << "nonTailFactorial(" << n << ") - depth " << depth << endl;
        
        if (n <= 1) {
            cout << string(depth * 2, ' ') << "Base case: returning 1" << endl;
            return 1;
        }
        
        // Recursive call - NOT in tail position
        long long recursiveResult = nonTailFactorialHelper(n - 1, metrics, depth + 1);
        
        // Additional computation after recursive call (makes it non-tail)
        long long result = n * recursiveResult;
        
        cout << string(depth * 2, ' ') << "Computed: " << n << " * " << recursiveResult 
             << " = " << result << endl;
        
        return result;
    }
    
    // Tail recursive implementation
    long long tailFactorialHelper(int n, long long accumulator, RecursionMetrics& metrics, int depth) {
        metrics.totalCalls++;
        metrics.maxStackDepth = max(metrics.maxStackDepth, depth + 1);
        
        cout << string(depth * 2, ' ') << "tailFactorial(" << n << ", " << accumulator 
             << ") - depth " << depth << endl;
        
        if (n <= 1) {
            cout << string(depth * 2, ' ') << "Base case: returning accumulator " << accumulator << endl;
            return accumulator;
        }
        
        // Tail recursive call - IS in tail position
        cout << string(depth * 2, ' ') << "Tail call: tailFactorial(" << (n-1) 
             << ", " << (n * accumulator) << ")" << endl;
        
        return tailFactorialHelper(n - 1, n * accumulator, metrics, depth + 1);
    }
    
public:
    // 5.2.1.2.5 Comprehensive comparison
    void compareRecursionTypes(int n) {
        cout << "=== Comprehensive Recursion Comparison ===" << endl;
        cout << "Testing with n = " << n << endl;
        cout << string(60, '=') << endl;
        
        // Test all three approaches
        RecursionMetrics nonTail = analyzeNonTailFactorial(n);
        RecursionMetrics tail = analyzeTailFactorial(n);
        RecursionMetrics iterative = analyzeIterativeFactorial(n);
        
        // Comparison table
        cout << "\n=== Performance Comparison ===" << endl;
        cout << setw(15) << "Metric" << setw(12) << "Non-tail" << setw(12) << "Tail" << setw(12) << "Iterative" << endl;
        cout << string(51, '-') << endl;
        
        cout << setw(15) << "Stack Depth:" << setw(12) << nonTail.maxStackDepth 
             << setw(12) << tail.maxStackDepth << setw(12) << iterative.maxStackDepth << endl;
        
        cout << setw(15) << "Function Calls:" << setw(12) << nonTail.totalCalls 
             << setw(12) << tail.totalCalls << setw(12) << iterative.totalCalls << endl;
        
        cout << setw(15) << "Memory (bytes):" << setw(12) << nonTail.memoryUsage 
             << setw(12) << tail.memoryUsage << setw(12) << iterative.memoryUsage << endl;
        
        cout << setw(15) << "Time (ns):" << setw(12) << nonTail.executionTimeNs 
             << setw(12) << tail.executionTimeNs << setw(12) << iterative.executionTimeNs << endl;
        
        // Analysis
        cout << "\n=== Analysis ===" << endl;
        cout << "Space Complexity:" << endl;
        cout << "  Non-tail: O(n) - each call requires stack frame" << endl;
        cout << "  Tail: O(n) - but optimizable to O(1) with TCO" << endl;
        cout << "  Iterative: O(1) - constant space" << endl;
        
        cout << "\nTime Complexity: All approaches are O(n)" << endl;
        
        cout << "\nTail Call Optimization (TCO) Impact:" << endl;
        cout << "  Without TCO: Tail recursion uses O(n) space like non-tail" << endl;
        cout << "  With TCO: Tail recursion uses O(1) space like iterative" << endl;
        cout << "  C++ compilers may optimize tail calls in release builds" << endl;
    }
};
```

### 5.2.2 Advanced Tail Recursion Patterns

#### 5.2.2.1 Complex Tail Recursive Algorithms

```cpp
// 5.2.2.1.1 Advanced tail recursive implementations
class AdvancedTailRecursion {
public:
    // 5.2.2.1.2 Tail recursive Fibonacci with memoization
    struct FibonacciTailResult {
        long long value;
        int stackDepth;
        int totalCalls;
        
        void display() const {
            cout << "Fibonacci result: " << value << endl;
            cout << "Stack depth: " << stackDepth << endl;
            cout << "Total calls: " << totalCalls << endl;
        }
    };
    
    FibonacciTailResult fibonacciTail(int n) {
        cout << "=== Tail Recursive Fibonacci ===" << endl;
        cout << "Computing fibonacci(" << n << ")" << endl;
        
        FibonacciTailResult result;
        result.stackDepth = 0;
        result.totalCalls = 0;
        
        if (n <= 1) {
            result.value = n;
            result.stackDepth = 1;
            result.totalCalls = 1;
        } else {
            result.value = fibonacciTailHelper(n, 0, 1, result, 0);
        }
        
        result.display();
        return result;
    }
    
private:
    long long fibonacciTailHelper(int n, long long a, long long b, 
                                 FibonacciTailResult& result, int depth) {
        result.totalCalls++;
        result.stackDepth = max(result.stackDepth, depth + 1);
        
        cout << string(depth * 2, ' ') << "fibTail(" << n << ", " << a << ", " << b << ")" << endl;
        
        if (n == 0) {
            return a;
        }
        if (n == 1) {
            return b;
        }
        
        // Tail recursive call
        return fibonacciTailHelper(n - 1, b, a + b, result, depth + 1);
    }
    
public:
    // 5.2.2.1.3 Tail recursive list operations
    struct ListNode {
        int val;
        ListNode* next;
        
        ListNode(int x) : val(x), next(nullptr) {}
    };
    
    // Tail recursive list reversal
    ListNode* reverseListTail(ListNode* head) {
        cout << "\n=== Tail Recursive List Reversal ===" << endl;
        
        if (!head || !head->next) {
            return head;
        }
        
        return reverseListTailHelper(head, nullptr, 0);
    }
    
private:
    ListNode* reverseListTailHelper(ListNode* current, ListNode* prev, int depth) {
        cout << string(depth * 2, ' ') << "reverseTail(";
        if (current) cout << current->val;
        else cout << "null";
        cout << ", ";
        if (prev) cout << prev->val;
        else cout << "null";
        cout << ")" << endl;
        
        if (!current) {
            return prev;
        }
        
        ListNode* next = current->next;
        current->next = prev;
        
        // Tail recursive call
        return reverseListTailHelper(next, current, depth + 1);
    }
    
public:
    // 5.2.2.1.4 Tail recursive tree operations
    struct TreeNode {
        int val;
        TreeNode* left;
        TreeNode* right;
        
        TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    };
    
    // Tail recursive tree sum with continuation
    int treeSumTail(TreeNode* root) {
        cout << "\n=== Tail Recursive Tree Sum ===" << endl;
        
        if (!root) return 0;
        
        return treeSumTailHelper(root, 0, 0);
    }
    
private:
    int treeSumTailHelper(TreeNode* node, int accumulator, int depth) {
        cout << string(depth * 2, ' ') << "treeSumTail(";
        if (node) cout << "node=" << node->val;
        else cout << "null";
        cout << ", acc=" << accumulator << ")" << endl;
        
        if (!node) {
            return accumulator;
        }
        
        // Process current node
        int newAccumulator = accumulator + node->val;
        
        // Handle children with tail recursion simulation
        if (!node->left && !node->right) {
            // Leaf node - return accumulated sum
            return newAccumulator;
        }
        
        if (node->left && !node->right) {
            // Only left child - tail recursive call
            return treeSumTailHelper(node->left, newAccumulator, depth + 1);
        }
        
        if (!node->left && node->right) {
            // Only right child - tail recursive call
            return treeSumTailHelper(node->right, newAccumulator, depth + 1);
        }
        
        // Both children exist - need to simulate tail recursion
        // This isn't truly tail recursive, but we can optimize it
        int leftSum = treeSumTailHelper(node->left, 0, depth + 1);
        int rightSum = treeSumTailHelper(node->right, 0, depth + 1);
        
        return newAccumulator + leftSum + rightSum;
    }
    
public:
    // 5.2.2.1.5 Tail recursion optimization simulation
    void demonstrateTailCallOptimization() {
        cout << "\n=== Tail Call Optimization Demonstration ===" << endl;
        
        // Compare tail vs non-tail with large inputs
        cout << "\nTesting with large input (n=1000):" << endl;
        
        try {
            // This might cause stack overflow without TCO
            cout << "Testing tail recursive factorial..." << endl;
            TailRecursionAnalysis analyzer;
            auto tailMetrics = analyzer.analyzeTailFactorial(100); // Smaller to avoid overflow
            
            cout << "\nTail recursion completed successfully" << endl;
            
        } catch (const exception& e) {
            cout << "Exception caught: " << e.what() << endl;
        }
        
        cout << "\nTCO Benefits:" << endl;
        cout << "1. Converts O(n) space recursion to O(1) space" << endl;
        cout << "2. Eliminates stack overflow risk for deep recursion" << endl;
        cout << "3. Performance similar to iterative solutions" << endl;
        cout << "4. Maintains recursive code clarity" << endl;
        
        cout << "\nC++ TCO Status:" << endl;
        cout << "- Not guaranteed by standard" << endl;
        cout << "- GCC/Clang may optimize with -O2/-O3" << endl;
        cout << "- MSVC has limited TCO support" << endl;
        cout << "- Better to use iterative for critical performance" << endl;
    }
    
    // 5.2.2.1.6 Helper methods for testing
    void testAdvancedTailRecursion() {
        cout << "=== Advanced Tail Recursion Testing ===" << endl;
        
        // Test Fibonacci
        fibonacciTail(10);
        
        // Test list reversal
        ListNode* head = new ListNode(1);
        head->next = new ListNode(2);
        head->next->next = new ListNode(3);
        head->next->next->next = new ListNode(4);
        
        cout << "\nOriginal list: 1 -> 2 -> 3 -> 4" << endl;
        ListNode* reversed = reverseListTail(head);
        
        cout << "Reversed list: ";
        ListNode* current = reversed;
        while (current) {
            cout << current->val;
            if (current->next) cout << " -> ";
            current = current->next;
        }
        cout << endl;
        
        // Test tree sum
        TreeNode* root = new TreeNode(1);
        root->left = new TreeNode(2);
        root->right = new TreeNode(3);
        root->left->left = new TreeNode(4);
        root->left->right = new TreeNode(5);
        
        int sum = treeSumTail(root);
        cout << "Tree sum: " << sum << endl;
        
        // Demonstrate TCO
        demonstrateTailCallOptimization();
        
        // Cleanup
        cleanupList(reversed);
        cleanupTree(root);
    }
    
private:
    void cleanupList(ListNode* head) {
        while (head) {
            ListNode* temp = head;
            head = head->next;
            delete temp;
        }
    }
    
    void cleanupTree(TreeNode* root) {
        if (root) {
            cleanupTree(root->left);
            cleanupTree(root->right);
            delete root;
        }
    }
};
```

### 5.2.3 Stack Optimization Techniques

#### 5.2.3.1 Manual Tail Call Optimization

```cpp
// 5.2.3.1.1 Manual TCO implementation patterns
class ManualTailCallOptimization {
public:
    // 5.2.3.1.2 Trampoline pattern for TCO
    template<typename T>
    class Trampoline {
    public:
        virtual ~Trampoline() = default;
        virtual bool isComplete() const = 0;
        virtual T getResult() const = 0;
        virtual unique_ptr<Trampoline<T>> bounce() = 0;
    };
    
    template<typename T>
    class TrampolineComplete : public Trampoline<T> {
    private:
        T result;
        
    public:
        TrampolineComplete(T res) : result(res) {}
        
        bool isComplete() const override { return true; }
        T getResult() const override { return result; }
        unique_ptr<Trampoline<T>> bounce() override { return nullptr; }
    };
    
    template<typename T>
    class TrampolineContinue : public Trampoline<T> {
    private:
        function<unique_ptr<Trampoline<T>>()> continuation;
        
    public:
        TrampolineContinue(function<unique_ptr<Trampoline<T>>()> cont) 
            : continuation(cont) {}
        
        bool isComplete() const override { return false; }
        T getResult() const override { throw runtime_error("Not complete"); }
        unique_ptr<Trampoline<T>> bounce() override { return continuation(); }
    };
    
    // 5.2.3.1.3 Trampoline-based factorial
    long long factorialTrampoline(int n) {
        cout << "=== Trampoline-based Factorial ===" << endl;
        cout << "Computing factorial(" << n << ") with trampoline" << endl;
        
        auto trampoline = factorialTrampolineHelper(n, 1);
        int bounces = 0;
        
        while (!trampoline->isComplete()) {
            trampoline = trampoline->bounce();
            bounces++;
            
            if (bounces % 100 == 0) {
                cout << "Bounces: " << bounces << endl;
            }
        }
        
        long long result = trampoline->getResult();
        cout << "Result: " << result << " (after " << bounces << " bounces)" << endl;
        cout << "Stack depth: O(1) - constant!" << endl;
        
        return result;
    }
    
private:
    unique_ptr<Trampoline<long long>> factorialTrampolineHelper(int n, long long acc) {
        if (n <= 1) {
            return make_unique<TrampolineComplete<long long>>(acc);
        }
        
        return make_unique<TrampolineContinue<long long>>(
            [n, acc, this]() {
                return factorialTrampolineHelper(n - 1, n * acc);
            }
        );
    }
    
public:
    // 5.2.3.1.4 Continuation-Passing Style (CPS)
    template<typename T, typename Cont>
    void factorialCPS(int n, Cont continuation) {
        factorialCPSHelper(n, 1, continuation);
    }
    
private:
    template<typename Cont>
    void factorialCPSHelper(int n, long long acc, Cont continuation) {
        if (n <= 1) {
            continuation(acc);
            return;
        }
        
        // Use trampoline to avoid stack overflow
        factorialCPSHelper(n - 1, n * acc, continuation);
    }
    
public:
    // 5.2.3.1.5 Iterative conversion of tail recursion
    long long factorialIterativeFromTail(int n) {
        cout << "\n=== Iterative Conversion from Tail Recursion ===" << endl;
        cout << "Converting tail recursive factorial to iterative" << endl;
        
        // Tail recursive version:
        // factorial(n, acc) = n <= 1 ? acc : factorial(n-1, n*acc)
        
        // Iterative conversion:
        long long acc = 1;
        
        cout << "Initial: n=" << n << ", acc=" << acc << endl;
        
        while (n > 1) {
            acc = n * acc;
            n = n - 1;
            cout << "Step: n=" << n << ", acc=" << acc << endl;
        }
        
        cout << "Final result: " << acc << endl;
        cout << "Stack usage: O(1)" << endl;
        
        return acc;
    }
    
    // 5.2.3.1.6 Stack-based simulation of recursion
    struct StackFrame {
        int n;
        long long acc;
        
        StackFrame(int n_val, long long acc_val) : n(n_val), acc(acc_val) {}
    };
    
    long long factorialStackSimulation(int n) {
        cout << "\n=== Stack-based Simulation of Tail Recursion ===" << endl;
        cout << "Simulating tail recursion with explicit stack" << endl;
        
        stack<StackFrame> callStack;
        callStack.push(StackFrame(n, 1));
        
        while (!callStack.empty()) {
            StackFrame frame = callStack.top();
            callStack.pop();
            
            cout << "Processing frame: n=" << frame.n << ", acc=" << frame.acc << endl;
            
            if (frame.n <= 1) {
                if (callStack.empty()) {
                    cout << "Final result: " << frame.acc << endl;
                    return frame.acc;
                }
                // This shouldn't happen in tail recursion simulation
            } else {
                // Push next frame (simulating tail call)
                callStack.push(StackFrame(frame.n - 1, frame.n * frame.acc));
            }
        }
        
        return 1; // Default case
    }
    
    // 5.2.3.1.7 Performance comparison of optimization techniques
    void compareOptimizationTechniques(int n) {
        cout << "\n=== Optimization Techniques Comparison ===" << endl;
        cout << "Testing with n = " << n << endl;
        
        // Measure performance of different techniques
        auto measureTime = [](function<long long()> func, const string& name) {
            auto start = chrono::high_resolution_clock::now();
            long long result = func();
            auto end = chrono::high_resolution_clock::now();
            
            auto duration = chrono::duration_cast<chrono::microseconds>(end - start);
            cout << name << ": " << result << " (" << duration.count() << " μs)" << endl;
        };
        
        measureTime([this, n]() { return factorialTrampoline(n); }, "Trampoline");
        measureTime([this, n]() { return factorialIterativeFromTail(n); }, "Iterative");
        measureTime([this, n]() { return factorialStackSimulation(n); }, "Stack Simulation");
        
        cout << "\nTechnique Analysis:" << endl;
        cout << "1. Trampoline: Elegant but overhead from function objects" << endl;
        cout << "2. Iterative: Best performance, O(1) space" << endl;
        cout << "3. Stack Simulation: Educational, shows explicit stack management" << endl;
        cout << "4. For production code: Use iterative for tail-recursive algorithms" << endl;
    }
};
```

---

## 5.3 Convert Recursion to Iterative using Stack

### 5.3.1 Systematic Conversion Methodology

#### 5.3.1.1 General Conversion Algorithm and Framework

```cpp
#include <iostream>
#include <stack>
#include <vector>
#include <functional>
using namespace std;

// 5.3.1.1.1 Generic recursion-to-iteration conversion framework
template<typename StateType, typename ResultType>
class RecursionToIterationConverter {
public:
    // State representation for iterative conversion
    struct IterativeState {
        StateType data;
        int phase;  // Which part of recursive function we're in
        ResultType partialResult;
        
        IterativeState(const StateType& d, int p = 0) 
            : data(d), phase(p), partialResult(ResultType{}) {}
    };
    
    // 5.3.1.1.2 Generic conversion template
    ResultType convertToIterative(
        const StateType& initialState,
        function<bool(const StateType&)> isBaseCase,
        function<ResultType(const StateType&)> baseCase,
        function<vector<StateType>(const StateType&)> getSubproblems,
        function<ResultType(const StateType&, const vector<ResultType>&)> combine
    ) {
        cout << "=== Generic Recursion to Iteration Conversion ===" << endl;
        
        stack<IterativeState> callStack;
        stack<ResultType> resultStack;
        
        callStack.push(IterativeState(initialState, 0));
        
        while (!callStack.empty()) {
            IterativeState& current = callStack.top();
            
            cout << "Processing state: phase=" << current.phase << endl;
            
            if (current.phase == 0) {
                // First visit to this state
                if (isBaseCase(current.data)) {
                    ResultType result = baseCase(current.data);
                    callStack.pop();
                    resultStack.push(result);
                    cout << "Base case reached, result: " << result << endl;
                } else {
                    // Mark as processing and push subproblems
                    current.phase = 1;
                    vector<StateType> subproblems = getSubproblems(current.data);
                    
                    cout << "Pushing " << subproblems.size() << " subproblems" << endl;
                    
                    // Push in reverse order to maintain correct processing order
                    for (int i = subproblems.size() - 1; i >= 0; i--) {
                        callStack.push(IterativeState(subproblems[i], 0));
                    }
                }
            } else {
                // Returning from subproblems - collect results
                vector<ResultType> subResults;
                for (size_t i = 0; i < getSubproblems(current.data).size(); i++) {
                    subResults.push_back(resultStack.top());
                    resultStack.pop();
                }
                
                // Reverse to get correct order
                reverse(subResults.begin(), subResults.end());
                
                ResultType result = combine(current.data, subResults);
                callStack.pop();
                resultStack.push(result);
                
                cout << "Combined result: " << result << endl;
            }
        }
        
        return resultStack.top();
    }
};

// 5.3.1.2 Specific Algorithm Conversions
class SpecificAlgorithmConverters {
public:
    // 5.3.1.2.1 Factorial conversion
    long long factorialIterative(int n) {
        cout << "=== Factorial: Recursive to Iterative Conversion ===" << endl;
        cout << "Converting factorial(" << n << ") to iterative" << endl;
        
        // Original recursive logic:
        // factorial(n) = n <= 1 ? 1 : n * factorial(n-1)
        
        struct FactorialState {
            int n;
            long long result;
            bool computed;
            
            FactorialState(int num) : n(num), result(1), computed(false) {}
        };
        
        stack<FactorialState> stateStack;
        stateStack.push(FactorialState(n));
        
        cout << "Initial stack size: " << stateStack.size() << endl;
        
        while (!stateStack.empty()) {
            FactorialState& current = stateStack.top();
            
            cout << "Processing n=" << current.n << ", computed=" << current.computed << endl;
            
            if (!current.computed) {
                if (current.n <= 1) {
                    current.result = 1;
                    current.computed = true;
                    cout << "Base case: factorial(" << current.n << ") = 1" << endl;
                } else {
                    current.computed = true;  // Mark as being processed
                    stateStack.push(FactorialState(current.n - 1));
                    cout << "Pushed subproblem: factorial(" << (current.n - 1) << ")" << endl;
                }
            } else {
                // We have the result for n-1, now compute n * factorial(n-1)
                if (current.n > 1 && stateStack.size() > 1) {
                    long long subResult = stateStack.top().result;
                    stateStack.pop();  // Remove the subproblem
                    
                    current.result = current.n * subResult;
                    cout << "Computed: " << current.n << " * " << subResult 
                         << " = " << current.result << endl;
                } else {
                    break;  // We're done
                }
            }
        }
        
        long long result = stateStack.top().result;
        cout << "Final result: " << result << endl;
        return result;
    }
    
    // 5.3.1.2.2 Fibonacci conversion with detailed tracking
    long long fibonacciIterative(int n) {
        cout << "\n=== Fibonacci: Recursive to Iterative Conversion ===" << endl;
        cout << "Converting fibonacci(" << n << ") to iterative" << endl;
        
        // Original recursive logic:
        // fib(n) = n <= 1 ? n : fib(n-1) + fib(n-2)
        
        struct FibState {
            int n;
            long long result;
            int phase;  // 0: initial, 1: computed fib(n-1), 2: computed both
            long long fib_n_1, fib_n_2;
            
            FibState(int num) : n(num), result(0), phase(0), fib_n_1(0), fib_n_2(0) {}
        };
        
        stack<FibState> stateStack;
        stateStack.push(FibState(n));
        
        int iterations = 0;
        
        while (!stateStack.empty()) {
            FibState& current = stateStack.top();
            iterations++;
            
            cout << "Iteration " << iterations << ": n=" << current.n 
                 << ", phase=" << current.phase << endl;
            
            if (current.n <= 1) {
                current.result = current.n;
                cout << "Base case: fib(" << current.n << ") = " << current.n << endl;
                
                // Pop and continue with parent if exists
                FibState completed = current;
                stateStack.pop();
                
                if (!stateStack.empty()) {
                    FibState& parent = stateStack.top();
                    if (parent.phase == 0) {
                        parent.fib_n_1 = completed.result;
                        parent.phase = 1;
                        stateStack.push(FibState(parent.n - 2));
                        cout << "Stored fib(" << (parent.n-1) << ")=" << completed.result 
                             << ", now computing fib(" << (parent.n-2) << ")" << endl;
                    } else if (parent.phase == 1) {
                        parent.fib_n_2 = completed.result;
                        parent.result = parent.fib_n_1 + parent.fib_n_2;
                        parent.phase = 2;
                        cout << "Computed fib(" << parent.n << ") = " << parent.fib_n_1 
                             << " + " << parent.fib_n_2 << " = " << parent.result << endl;
                    }
                }
            } else {
                if (current.phase == 0) {
                    current.phase = 1;
                    stateStack.push(FibState(current.n - 1));
                    cout << "Pushed subproblem: fib(" << (current.n - 1) << ")" << endl;
                } else if (current.phase == 2) {
                    // Both subproblems computed, bubble up result
                    FibState completed = current;
                    stateStack.pop();
                    
                    if (!stateStack.empty()) {
                        FibState& parent = stateStack.top();
                        if (parent.phase == 0) {
                            parent.fib_n_1 = completed.result;
                            parent.phase = 1;
                            stateStack.push(FibState(parent.n - 2));
                        } else if (parent.phase == 1) {
                            parent.fib_n_2 = completed.result;
                            parent.result = parent.fib_n_1 + parent.fib_n_2;
                            parent.phase = 2;
                        }
                    }
                }
            }
        }
        
        cout << "Total iterations: " << iterations << endl;
        return stateStack.empty() ? 0 : stateStack.top().result;
    }
    
    // 5.3.1.2.3 Binary tree traversal conversion
    struct TreeNode {
        int val;
        TreeNode* left;
        TreeNode* right;
        
        TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
    };
    
    vector<int> inorderTraversalIterative(TreeNode* root) {
        cout << "\n=== Binary Tree Inorder: Recursive to Iterative ===" << endl;
        
        if (!root) {
            cout << "Empty tree" << endl;
            return {};
        }
        
        // Original recursive logic:
        // inorder(node) = inorder(left) + [node.val] + inorder(right)
        
        vector<int> result;
        stack<TreeNode*> nodeStack;
        TreeNode* current = root;
        
        cout << "Starting iterative inorder traversal" << endl;
        
        while (current || !nodeStack.empty()) {
            // Go to leftmost node
            while (current) {
                nodeStack.push(current);
                cout << "Pushed node " << current->val << " to stack" << endl;
                current = current->left;
            }
            
            // Process current node
            current = nodeStack.top();
            nodeStack.pop();
            
            result.push_back(current->val);
            cout << "Processed node " << current->val << endl;
            
            // Move to right subtree
            current = current->right;
        }
        
        cout << "Traversal complete. Result: [";
        for (int i = 0; i < result.size(); i++) {
            cout << result[i];
            if (i < result.size() - 1) cout << ", ";
        }
        cout << "]" << endl;
        
        return result;
    }
    
    // 5.3.1.2.4 More complex example: Binary tree paths
    vector<string> binaryTreePathsIterative(TreeNode* root) {
        cout << "\n=== Binary Tree Paths: Recursive to Iterative ===" << endl;
        
        if (!root) return {};
        
        vector<string> paths;
        stack<pair<TreeNode*, string>> stateStack;
        
        stateStack.push({root, to_string(root->val)});
        
        while (!stateStack.empty()) {
            auto [node, path] = stateStack.top();
            stateStack.pop();
            
            cout << "Processing node " << node->val << " with