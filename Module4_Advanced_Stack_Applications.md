# Module 4: Advanced Stack Applications

Welcome back, students! In our previous modules, we've explored the fundamentals of stacks and queues and their variations. Today, we'll dive into advanced applications of stacks that demonstrate their power and versatility in solving complex problems.

## 4.1 Expression Evaluation and Conversion

One of the most important applications of stacks is in evaluating and converting mathematical expressions. Let's explore how stacks help us handle infix, prefix, and postfix notations.

### Understanding Different Expression Notations

**Infix Notation**: This is the standard notation we typically use, where operators are placed between operands (e.g., `A + B`).

**Prefix Notation (Polish Notation)**: Operators appear before their operands (e.g., `+ A B`).

**Postfix Notation (Reverse Polish Notation)**: Operators appear after their operands (e.g., `A B +`).

![Expression Notations](https://media.geeksforgeeks.org/wp-content/uploads/20190708181559/Untitled-Diagram-219.png)

### Why Use Different Notations?

- **Infix**: Intuitive for humans but requires operator precedence rules and parentheses
- **Prefix and Postfix**: Eliminate the need for parentheses and precedence rules, making them easier for computers to evaluate

### Infix to Postfix Conversion

Converting infix expressions to postfix involves using a stack to keep track of operators based on their precedence.

#### Algorithm:

1. Initialize an empty stack and an empty result string
2. Scan the infix expression from left to right
3. If the character is an operand, add it to the result string
4. If the character is an opening parenthesis '(', push it onto the stack
5. If the character is a closing parenthesis ')', pop operators from the stack and add them to the result string until an opening parenthesis is encountered, then discard both parentheses
6. If the character is an operator:
   - Pop operators from the stack and add them to the result string while the stack is not empty and the top of the stack has higher or equal precedence
   - Push the current operator onto the stack
7. After scanning the entire expression, pop any remaining operators from the stack and add them to the result string

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

// Function to determine the precedence of operators
int precedence(char op) {
    if (op == '+' || op == '-')
        return 1;
    if (op == '*' || op == '/')
        return 2;
    if (op == '^')
        return 3;
    return 0; // For '(' and other characters
}

// Function to check if the character is an operand
bool isOperand(char ch) {
    return (ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z') || (ch >= '0' && ch <= '9');
}

// Function to convert infix expression to postfix
string infixToPostfix(string infix) {
    stack<char> stk;
    string postfix = "";
    
    for (int i = 0; i < infix.length(); i++) {
        char ch = infix[i];
        
        // If the character is an operand, add it to the postfix expression
        if (isOperand(ch)) {
            postfix += ch;
        }
        // If the character is an opening bracket, push it onto the stack
        else if (ch == '(') {
            stk.push(ch);
        }
        // If the character is a closing bracket
        else if (ch == ')') {
            // Pop all operators from the stack and add them to postfix until an opening bracket is encountered
            while (!stk.empty() && stk.top() != '(') {
                postfix += stk.top();
                stk.pop();
            }
            // Pop the opening bracket
            if (!stk.empty() && stk.top() == '(') {
                stk.pop();
            }
        }
        // If the character is an operator
        else {
            // Pop operators with higher or equal precedence and add them to postfix
            while (!stk.empty() && precedence(ch) <= precedence(stk.top())) {
                postfix += stk.top();
                stk.pop();
            }
            // Push the current operator onto the stack
            stk.push(ch);
        }
    }
    
    // Pop any remaining operators from the stack
    while (!stk.empty()) {
        postfix += stk.top();
        stk.pop();
    }
    
    return postfix;
}

// Driver code to test the function
int main() {
    string infix = "a+b*(c^d-e)^(f+g*h)-i";
    string postfix = infixToPostfix(infix);
    
    cout << "Infix expression: " << infix << endl;
    cout << "Postfix expression: " << postfix << endl;
    
    return 0;
}
```

### Infix to Prefix Conversion

Converting infix to prefix is similar but requires a few additional steps:

1. Reverse the infix expression
2. Replace '(' with ')' and vice versa
3. Convert the modified expression to postfix
4. Reverse the postfix expression to get the prefix expression

```cpp
#include <iostream>
#include <stack>
#include <string>
#include <algorithm>
using namespace std;

// Function to determine the precedence of operators
int precedence(char op) {
    if (op == '+' || op == '-')
        return 1;
    if (op == '*' || op == '/')
        return 2;
    if (op == '^')
        return 3;
    return 0;
}

// Function to check if the character is an operand
bool isOperand(char ch) {
    return (ch >= 'a' && ch <= 'z') || (ch >= 'A' && ch <= 'Z') || (ch >= '0' && ch <= '9');
}

// Function to convert infix expression to prefix
string infixToPrefix(string infix) {
    // Reverse the infix expression
    reverse(infix.begin(), infix.end());
    
    // Replace opening brackets with closing brackets and vice versa
    for (int i = 0; i < infix.length(); i++) {
        if (infix[i] == '(')
            infix[i] = ')';
        else if (infix[i] == ')')
            infix[i] = '(';
    }
    
    // Use modified infix to postfix conversion
    stack<char> stk;
    string prefix = "";
    
    for (int i = 0; i < infix.length(); i++) {
        char ch = infix[i];
        
        if (isOperand(ch)) {
            prefix += ch;
        }
        else if (ch == '(') {
            stk.push(ch);
        }
        else if (ch == ')') {
            while (!stk.empty() && stk.top() != '(') {
                prefix += stk.top();
                stk.pop();
            }
            if (!stk.empty() && stk.top() == '(') {
                stk.pop();
            }
        }
        else {
            while (!stk.empty() && precedence(ch) <= precedence(stk.top())) {
                prefix += stk.top();
                stk.pop();
            }
            stk.push(ch);
        }
    }
    
    while (!stk.empty()) {
        prefix += stk.top();
        stk.pop();
    }
    
    // Reverse the postfix expression to get the prefix expression
    reverse(prefix.begin(), prefix.end());
    
    return prefix;
}

int main() {
    string infix = "a+b*(c^d-e)^(f+g*h)-i";
    string prefix = infixToPrefix(infix);
    
    cout << "Infix expression: " << infix << endl;
    cout << "Prefix expression: " << prefix << endl;
    
    return 0;
}
```

### Evaluating Postfix Expressions

Evaluating a postfix expression is straightforward using a stack:

1. Scan the postfix expression from left to right
2. If the character is an operand, push it onto the stack
3. If the character is an operator, pop the required number of operands from the stack, perform the operation, and push the result back onto the stack
4. After scanning the entire expression, the stack will contain the final result

```cpp
#include <iostream>
#include <stack>
#include <string>
#include <cmath>
using namespace std;

// Function to evaluate a postfix expression
int evaluatePostfix(string postfix) {
    stack<int> stk;
    
    for (int i = 0; i < postfix.length(); i++) {
        char ch = postfix[i];
        
        // If the character is an operand, push it onto the stack
        if (isdigit(ch)) {
            stk.push(ch - '0'); // Convert char to int
        }
        // If the character is an operator
        else {
            // Pop the required operands
            int val2 = stk.top(); stk.pop();
            int val1 = stk.top(); stk.pop();
            
            // Perform the operation
            switch (ch) {
                case '+': stk.push(val1 + val2); break;
                case '-': stk.push(val1 - val2); break;
                case '*': stk.push(val1 * val2); break;
                case '/': stk.push(val1 / val2); break;
                case '^': stk.push(pow(val1, val2)); break;
            }
        }
    }
    
    // The final result is on top of the stack
    return stk.top();
}

int main() {
    string postfix = "23*5+";
    int result = evaluatePostfix(postfix);
    
    cout << "Postfix expression: " << postfix << endl;
    cout << "Result: " << result << endl; // Should output 11 (2*3+5 = 11)
    
    return 0;
}
```

## 4.2 Parentheses Matching and Syntax Checking

Stacks are excellent for checking balanced parentheses and validating syntax in programming languages, markup languages, and more.

### Balanced Parentheses Checking

This is a classic stack problem. The algorithm checks if a string containing different types of brackets is balanced.

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

// Function to check if the parentheses are balanced
bool areParenthesesBalanced(string expr) {
    stack<char> stk;
    
    for (int i = 0; i < expr.length(); i++) {
        char ch = expr[i];
        
        // If the current character is an opening bracket, push it onto the stack
        if (ch == '(' || ch == '[' || ch == '{') {
            stk.push(ch);
            continue;
        }
        
        // If the stack is empty and we encounter a closing bracket, the expression is unbalanced
        if (stk.empty())
            return false;
        
        // Check for matching pairs
        char top;
        switch (ch) {
            case ')':
                top = stk.top();
                stk.pop();
                if (top != '(')
                    return false;
                break;
            
            case '}':
                top = stk.top();
                stk.pop();
                if (top != '{')
                    return false;
                break;
            
            case ']':
                top = stk.top();
                stk.pop();
                if (top != '[')
                    return false;
                break;
        }
    }
    
    // Check if the stack is empty (all brackets matched)
    return stk.empty();
}

int main() {
    string expr1 = "{[()]}";
    string expr2 = "{[(])}";
    
    cout << expr1 << " is " << (areParenthesesBalanced(expr1) ? "balanced" : "not balanced") << endl;
    cout << expr2 << " is " << (areParenthesesBalanced(expr2) ? "balanced" : "not balanced") << endl;
    
    return 0;
}
```

### HTML/XML Tag Matching

This is an extension of the balanced parentheses problem, where we check if HTML or XML tags are properly nested.

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

// Function to check if HTML/XML tags are balanced
bool areTagsBalanced(string html) {
    stack<string> stk;
    
    for (int i = 0; i < html.length(); i++) {
        // If we find an opening tag
        if (html[i] == '<') {
            string tag = "";
            i++; // Move past '<'
            
            // If it's a closing tag
            bool isClosing = false;
            if (html[i] == '/') {
                isClosing = true;
                i++; // Move past '/'
            }
            
            // Extract the tag name
            while (i < html.length() && html[i] != '>' && html[i] != ' ') {
                tag += html[i];
                i++;
            }
            
            // Skip attributes if any
            while (i < html.length() && html[i] != '>') {
                i++;
            }
            
            // If it was an opening tag, push it onto the stack
            if (!isClosing) {
                stk.push(tag);
            }
            // If it was a closing tag, check if it matches the top of the stack
            else {
                if (stk.empty() || stk.top() != tag)
                    return false;
                stk.pop();
            }
        }
    }
    
    // Check if all tags were closed
    return stk.empty();
}

int main() {
    string html1 = "<html><body><div>Hello</div></body></html>";
    string html2 = "<html><body><div>Hello</p></body></html>";
    
    cout << "HTML 1 is " << (areTagsBalanced(html1) ? "balanced" : "not balanced") << endl;
    cout << "HTML 2 is " << (areTagsBalanced(html2) ? "balanced" : "not balanced") << endl;
    
    return 0;
}
```

### Syntax Validation in Compilers

Compilers use stacks for syntax checking and parsing. Here's a simplified example of checking balanced tokens in a programming language:

```cpp
#include <iostream>
#include <stack>
#include <string>
#include <vector>
using namespace std;

enum TokenType {
    OPEN_BRACE, CLOSE_BRACE,
    OPEN_PAREN, CLOSE_PAREN,
    OPEN_BRACKET, CLOSE_BRACKET,
    IF, ELSE, WHILE, FOR,
    SEMICOLON
};

struct Token {
    TokenType type;
    string value;
    int line;
    
    Token(TokenType t, string v, int l) : type(t), value(v), line(l) {}
};

// Simplified function to validate syntax
bool validateSyntax(const vector<Token>& tokens) {
    stack<Token> bracesStack;
    stack<Token> controlStack;
    
    for (const Token& token : tokens) {
        switch (token.type) {
            case OPEN_BRACE:
                bracesStack.push(token);
                break;
                
            case CLOSE_BRACE:
                if (bracesStack.empty()) {
                    cout << "Error: Unmatched closing brace at line " << token.line << endl;
                    return false;
                }
                bracesStack.pop();
                
                // Check if there's an unmatched control structure
                if (!controlStack.empty() && bracesStack.size() < controlStack.size()) {
                    Token control = controlStack.top();
                    controlStack.pop();
                    cout << "Warning: Control structure " << control.value 
                         << " at line " << control.line << " is now closed" << endl;
                }
                break;
                
            case IF:
            case WHILE:
            case FOR:
                controlStack.push(token);
                break;
                
            // Handle other tokens...
        }
    }
    
    // Check for unclosed braces
    if (!bracesStack.empty()) {
        Token brace = bracesStack.top();
        cout << "Error: Unclosed brace from line " << brace.line << endl;
        return false;
    }
    
    return true;
}

// This is just a demonstration of the concept
int main() {
    // Example tokens for: if (x > 0) { y = 1; }
    vector<Token> tokens = {
        Token(IF, "if", 1),
        Token(OPEN_PAREN, "(", 1),
        // ... more tokens
        Token(CLOSE_PAREN, ")", 1),
        Token(OPEN_BRACE, "{", 1),
        // ... more tokens
        Token(SEMICOLON, ";", 2),
        Token(CLOSE_BRACE, "}", 3)
    };
    
    cout << "Syntax is " << (validateSyntax(tokens) ? "valid" : "invalid") << endl;
    
    return 0;
}
```

## 4.3 Implementation of Other Data Structures

Stacks can be used to implement other data structures and algorithms. Here are some interesting examples:

### Implementing Queues Using Stacks

As we saw in Module 1, we can implement a queue using two stacks. Here's a detailed implementation:

```cpp
#include <iostream>
#include <stack>
using namespace std;

class QueueUsingStacks {
private:
    stack<int> inbox;   // For enqueue operation
    stack<int> outbox;  // For dequeue operation
    
    // Transfer elements from inbox to outbox when needed
    void transfer() {
        if (outbox.empty()) {
            while (!inbox.empty()) {
                outbox.push(inbox.top());
                inbox.pop();
            }
        }
    }
    
public:
    // Add an element to the queue
    void enqueue(int x) {
        inbox.push(x);
    }
    
    // Remove and return the front element
    int dequeue() {
        transfer();
        
        if (outbox.empty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        
        int front = outbox.top();
        outbox.pop();
        return front;
    }
    
    // Get the front element without removing it
    int peek() {
        transfer();
        
        if (outbox.empty()) {
            cout << "Queue is empty" << endl;
            return -1;
        }
        
        return outbox.top();
    }
    
    // Check if queue is empty
    bool isEmpty() {
        return inbox.empty() && outbox.empty();
    }
    
    // Get the size of the queue
    int size() {
        return inbox.size() + outbox.size();
    }
};

int main() {
    QueueUsingStacks q;
    
    q.enqueue(1);
    q.enqueue(2);
    q.enqueue(3);
    
    cout << "Front element: " << q.peek() << endl;
    cout << "Queue size: " << q.size() << endl;
    
    cout << "Dequeued: " << q.dequeue() << endl;
    cout << "Dequeued: " << q.dequeue() << endl;
    
    q.enqueue(4);
    
    cout << "Front element: " << q.peek() << endl;
    cout << "Queue size: " << q.size() << endl;
    
    return 0;
}
```

### Stack-Based Implementation of Recursion

Recursion is internally implemented using a call stack. We can simulate recursion using an explicit stack to avoid stack overflow for deep recursion:

```cpp
#include <iostream>
#include <stack>
using namespace std;

// Recursive factorial
int factorial_recursive(int n) {
    if (n <= 1)
        return 1;
    return n * factorial_recursive(n - 1);
}

// Iterative factorial using a stack
int factorial_iterative(int n) {
    stack<pair<int, int>> stk; // Pair of (n, result so far)
    stk.push({n, 1});
    
    int result = 1;
    
    while (!stk.empty()) {
        auto [current, partial_result] = stk.top();
        stk.pop();
        
        if (current <= 1) {
            result = partial_result;
            continue;
        }
        
        stk.push({current - 1, partial_result * current});
    }
    
    return result;
}

// Recursive Fibonacci
int fibonacci_recursive(int n) {
    if (n <= 1)
        return n;
    return fibonacci_recursive(n - 1) + fibonacci_recursive(n - 2);
}

// Iterative Fibonacci using a stack
int fibonacci_iterative(int n) {
    if (n <= 1)
        return n;
    
    // We'll use a stack to keep track of function calls
    // Each entry contains: (n, state, result)
    // state: 0 = initial call, 1 = computing fib(n-1), 2 = computing fib(n-2)
    stack<tuple<int, int, int>> stk;
    stk.push({n, 0, 0});
    
    int finalResult = 0;
    
    while (!stk.empty()) {
        auto [current, state, result] = stk.top();
        stk.pop();
        
        // Base cases
        if (current <= 1) {
            finalResult = current;
            continue;
        }
        
        // Process based on state
        if (state == 0) {
            // Initial call, compute fib(n-1)
            stk.push({current, 1, 0}); // Will return to process fib(n-1) result
            stk.push({current - 1, 0, 0}); // Compute fib(n-1)
        }
        else if (state == 1) {
            // Received result of fib(n-1), now compute fib(n-2)
            int fib_n_1 = finalResult;
            stk.push({current, 2, fib_n_1}); // Will return to process both results
            stk.push({current - 2, 0, 0}); // Compute fib(n-2)
        }
        else if (state == 2) {
            // Received result of fib(n-2), combine with fib(n-1)
            int fib_n_1 = result;
            int fib_n_2 = finalResult;
            finalResult = fib_n_1 + fib_n_2;
        }
    }
    
    return finalResult;
}

int main() {
    int n = 5;
    
    cout << "Factorial of " << n << " (recursive): " << factorial_recursive(n) << endl;
    cout << "Factorial of " << n << " (iterative): " << factorial_iterative(n) << endl;
    
    cout << "Fibonacci of " << n << " (recursive): " << fibonacci_recursive(n) << endl;
    cout << "Fibonacci of " << n << " (iterative): " << fibonacci_iterative(n) << endl;
    
    return 0;
}
```

### Backtracking Algorithms Using Stacks

Backtracking algorithms like maze solving can be implemented using stacks:

```cpp
#include <iostream>
#include <stack>
#include <vector>
using namespace std;

// Structure to represent a position in the maze
struct Position {
    int row, col;
    Position(int r, int c) : row(r), col(c) {}
};

// Function to print the maze
void printMaze(const vector<vector<char>>& maze) {
    for (const auto& row : maze) {
        for (char cell : row) {
            cout << cell << " ";
        }
        cout << endl;
    }
    cout << endl;
}

// Function to solve the maze using stack-based backtracking
bool solveMaze(vector<vector<char>>& maze, Position start, Position end) {
    int rows = maze.size();
    int cols = maze[0].size();
    
    // Define the four possible moves (up, right, down, left)
    vector<pair<int, int>> moves = {{-1, 0}, {0, 1}, {1, 0}, {0, -1}};
    
    // Stack to keep track of positions to explore
    stack<Position> stk;
    stk.push(start);
    
    // Stack to keep track of the path
    stack<Position> path;
    
    // Set to mark visited positions
    vector<vector<bool>> visited(rows, vector<bool>(cols, false));
    visited[start.row][start.col] = true;
    
    while (!stk.empty()) {
        Position current = stk.top();
        stk.pop();
        
        // Add to path
        path.push(current);
        
        // If we've reached the end
        if (current.row == end.row && current.col == end.col) {
            // Mark the path in the maze
            while (!path.empty()) {
                Position p = path.top();
                path.pop();
                maze[p.row][p.col] = '*'; // Mark the path
            }
            return true;
        }
        
        bool foundMove = false;
        
        // Try all possible moves
        for (const auto& move : moves) {
            int newRow = current.row + move.first;
            int newCol = current.col + move.second;
            
            // Check if the move is valid
            if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols &&
                !visited[newRow][newCol] && maze[newRow][newCol] == '0') {
                
                // Mark as visited
                visited[newRow][newCol] = true;
                
                // Push to stack for exploration
                stk.push(Position(newRow, newCol));
                foundMove = true;
                break;
            }
        }
        
        // If no valid move was found, backtrack
        if (!foundMove) {
            path.pop(); // Remove the current position from the path
        }
    }
    
    return false; // No path found
}

int main() {
    // Create a maze (0 = path, 1 = wall)
    vector<vector<char>> maze = {
        {'0', '1', '0', '0', '0'},
        {'0', '1', '0', '1', '0'},
        {'0', '0', '0', '0', '0'},
        {'0', '1', '1', '1', '0'},
        {'0', '0', '0', '0', '0'}
    };
    
    Position start(0, 0);
    Position end(4, 4);
    
    cout << "Original Maze:" << endl;
    printMaze(maze);
    
    if (solveMaze(maze, start, end)) {
        cout << "Path found!" << endl;
        cout << "Solved Maze:" << endl;
        printMaze(maze);
    } else {
        cout << "No path found!" << endl;
    }
    
    return 0;
}
```

## Practical Problems

Let's apply our knowledge of advanced stack applications to solve some interesting problems.

### Problem 1: Basic Calculator

**Problem Link**: [LeetCode 224 - Basic Calculator](https://leetcode.com/problems/basic-calculator/)

**Problem Statement**:
Given a string s representing a valid expression, implement a basic calculator to evaluate it and return the result of the evaluation.

The expression string may contain open `(` and closing parentheses `)`, the plus `+` or minus sign `-`, non-negative integers, and empty spaces.

**Sample Test Cases**:
```
Input: s = "1 + 1"
Output: 2

Input: s = " 2-1 + 2 "
Output: 3

Input: s = "(1+(4+5+2)-3)+(6+8)"
Output: 23
```

**Approach 1: Using Stack**

We can use a stack to handle the parentheses and keep track of the results:

```cpp
#include <iostream>
#include <stack>
#include <string>
using namespace std;

int calculate(string s) {
    stack<int> stk;
    int result = 0;
    int number = 0;
    int sign = 1;  // 1 means positive, -1 means negative
    
    for (int i = 0; i < s.length(); i++) {
        char c = s[i];
        
        if (isdigit(c)) {
            number = number * 10 + (c - '0');
        }
        else if (c == '+') {
            result += sign * number;
            number = 0;
            sign = 1;
        }
        else if (c == '-') {
            result += sign * number;
            number = 0;
            sign = -1;
        }
        else if (c == '(') {
            // Push the current result and sign onto the stack
            stk.push(result);
            stk.push(sign);
            
            // Reset result and sign for the subexpression
            result = 0;
            sign = 1;
        }
        else if (c == ')') {
            // Calculate the final result for the current subexpression
            result += sign * number;
            number = 0;
            
            // Apply the sign of the subexpression
            result *= stk.top();
            stk.pop();
            
            // Add the result of the subexpression to the previous result
            result += stk.top();
            stk.pop();
        }
    }
    
    return result + sign * number;
}

int main() {
    string expression1 = "1 + 1";
    string expression2 = " 2-1 + 2 ";
    string expression3 = "(1+(4+5+2)-3)+(6+8)";
    
    cout << expression1 << " = " << calculate(expression1) << endl;
    cout << expression2 << " = " << calculate(expression2) << endl;
    cout << expression3 << " = " << calculate(expression3) << endl;
    
    return 0;
}
```

**Time Complexity**: O(n), where n is the length of the string
**Space Complexity**: O(n) for the stack in the worst case (lots of nested parentheses)

**Explanation**:
1. We scan the expression from left to right
2. For digits, we build the number
3. For operators (+/-), we apply the previous calculation and update the sign
4. For opening parenthesis, we push the current result and sign onto the stack
5. For closing parenthesis, we finalize the subexpression and combine it with the previous result
6. At the end, we add the last number with its sign to the result

### Problem 2: Largest Rectangle in Histogram

**Problem Link**: [LeetCode 84 - Largest Rectangle in Histogram](https://leetcode.com/problems/largest-rectangle-in-histogram/)

**Problem Statement**:
Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

**Sample Test Cases**:
```
Input: heights = [2,1,5,6,2,3]
Output: 10
Explanation: The largest rectangle has area = 10, which is formed by the bars at indices 2, 3.
```

**Approach 1: Brute Force**

For each bar, we can extend as far as possible to the left and right while maintaining the height, and calculate the maximum area:

```cpp
#include <iostream>
#include <vector>
using namespace std;

int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    int maxArea = 0;
    
    for (int i = 0; i < n; i++) {
        int height = heights[i];
        
        // Extend to the left
        int left = i;
        while (left > 0 && heights[left - 1] >= height) {
            left--;
        }
        
        // Extend to the right
        int right = i;
        while (right < n - 1 && heights[right + 1] >= height) {
            right++;
        }
        
        // Calculate area with current bar as the smallest
        int width = right - left + 1;
        int area = height * width;
        maxArea = max(maxArea, area);
    }
    
    return maxArea;
}
```

**Time Complexity**: O(n²) in the worst case
**Space Complexity**: O(1)

**Approach 2: Using Stack (Optimal)**

We can use a stack to keep track of increasing heights and calculate areas when we find a smaller height:

```cpp
#include <iostream>
#include <vector>
#include <stack>
using namespace std;

int largestRectangleArea(vector<int>& heights) {
    int n = heights.size();
    stack<int> stk; // Store indices of bars
    int maxArea = 0;
    
    for (int i = 0; i <= n; i++) {
        int h = (i == n ? 0 : heights[i]);
        
        // If the current bar is smaller than the bar at the top of the stack
        // Pop the stack and calculate the area
        while (!stk.empty() && h < heights[stk.top()]) {
            int height = heights[stk.top()];
            stk.pop();
            
            int width = stk.empty() ? i : i - stk.top() - 1;
            int area = height * width;
            maxArea = max(maxArea, area);
        }
        
        stk.push(i);
    }
    
    return maxArea;
}

int main() {
    vector<int> heights = {2, 1, 5, 6, 2, 3};
    cout << "Largest rectangle area: " << largestRectangleArea(heights) << endl;
    
    return 0;
}
```

**Time Complexity**: O(n) - each bar is pushed and popped at most once
**Space Complexity**: O(n) for the stack

**Explanation**:
1. We use a stack to store the indices of bars in increasing order of height
2. When we find a bar shorter than the one at the top of the stack, we know we need to calculate the area for all taller bars
3. For each popped bar, we calculate the area as `height * width`
4. The width is determined by the current position and the next bar on the stack (or the beginning if the stack is empty)
5. We add a dummy bar of height 0 at the end to ensure all calculations are done

This approach is optimal because:
- We process each bar exactly once for pushing and at most once for popping
- We avoid recalculating areas for the same ranges multiple times

## Summary

In this module, we've explored advanced applications of stacks:

1. **Expression Evaluation and Conversion**:
   - Converting between infix, prefix, and postfix notations
   - Evaluating postfix expressions
   - Handling operator precedence and parentheses

2. **Parentheses Matching and Syntax Checking**:
   - Checking balanced parentheses
   - Validating HTML/XML tags
   - Syntax checking in programming languages

3. **Implementation of Other Data Structures**:
   - Implementing queues using stacks
   - Stack-based implementation of recursion
   - Backtracking algorithms using stacks

We've also solved complex problems that demonstrate the power of stacks:
- Implementing a basic calculator to evaluate expressions with parentheses
- Finding the largest rectangle in a histogram

These applications highlight how stacks are not just simple data structures but powerful tools for solving a wide range of algorithmic problems. The key to using stacks effectively is recognizing patterns where last-in-first-out behavior is beneficial, such as nested structures, backtracking, and deferred operations.

In our next module, we'll explore advanced queue applications, including Breadth-First Search, CPU scheduling, and message queues.