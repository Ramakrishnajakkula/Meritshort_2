# Module 5: Advanced Queue Applications

Welcome back, students! In our previous module, we explored advanced applications of stacks. Today, we'll focus on advanced applications of queues, which are essential for solving a wide range of problems in computer science and software engineering.

## 5.1 Breadth-First Search (BFS)

Breadth-First Search is one of the most important graph traversal algorithms, and it relies heavily on queues. Unlike Depth-First Search (which uses a stack), BFS explores all neighbors at the current depth before moving to nodes at the next depth level.

### How BFS Works

1. Start at a source node and mark it as visited
2. Enqueue the source node
3. While the queue is not empty:
   - Dequeue a node
   - Process the node
   - Enqueue all unvisited neighbors of the node and mark them as visited

![BFS Visualization](https://he-s3.s3.amazonaws.com/media/uploads/fdec3c2.jpg)

### BFS Implementation for Graph Traversal

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

class Graph {
private:
    int V; // Number of vertices
    vector<vector<int>> adj; // Adjacency list

public:
    Graph(int vertices) {
        V = vertices;
        adj.resize(V);
    }

    // Add an edge to the graph
    void addEdge(int v, int w) {
        adj[v].push_back(w);
    }

    // BFS traversal starting from a given source
    void BFS(int source) {
        // Mark all vertices as not visited
        vector<bool> visited(V, false);
        
        // Create a queue for BFS
        queue<int> q;
        
        // Mark the source node as visited and enqueue it
        visited[source] = true;
        q.push(source);
        
        cout << "BFS traversal starting from vertex " << source << ": ";
        
        while (!q.empty()) {
            // Dequeue a vertex from queue and print it
            int u = q.front();
            q.pop();
            cout << u << " ";
            
            // Get all adjacent vertices of the dequeued vertex
            // If an adjacent vertex has not been visited, mark it
            // as visited and enqueue it
            for (int v : adj[u]) {
                if (!visited[v]) {
                    visited[v] = true;
                    q.push(v);
                }
            }
        }
        cout << endl;
    }
};

int main() {
    // Create a graph
    Graph g(7);
    g.addEdge(0, 1);
    g.addEdge(0, 2);
    g.addEdge(1, 3);
    g.addEdge(1, 4);
    g.addEdge(2, 5);
    g.addEdge(2, 6);
    
    // Perform BFS traversal
    g.BFS(0);
    
    return 0;
}
```

### Level Order Traversal of Trees

Level order traversal of a binary tree is a classic application of BFS:

```cpp
#include <iostream>
#include <queue>
using namespace std;

// Definition for a binary tree node
struct TreeNode {
    int val;
    TreeNode* left;
    TreeNode* right;
    
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

// Function for level order traversal
void levelOrderTraversal(TreeNode* root) {
    if (root == nullptr) return;
    
    queue<TreeNode*> q;
    q.push(root);
    
    cout << "Level Order Traversal: ";
    
    while (!q.empty()) {
        int levelSize = q.size(); // Number of nodes at current level
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            cout << node->val << " ";
            
            // Enqueue child nodes
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
    }
    cout << endl;
}

// Function to print level order traversal with level information
void levelOrderTraversalWithLevels(TreeNode* root) {
    if (root == nullptr) return;
    
    queue<TreeNode*> q;
    q.push(root);
    
    int level = 0;
    
    while (!q.empty()) {
        int levelSize = q.size();
        
        cout << "Level " << level << ": ";
        
        for (int i = 0; i < levelSize; i++) {
            TreeNode* node = q.front();
            q.pop();
            
            cout << node->val << " ";
            
            if (node->left) q.push(node->left);
            if (node->right) q.push(node->right);
        }
        
        cout << endl;
        level++;
    }
}

int main() {
    // Create a binary tree
    TreeNode* root = new TreeNode(1);
    root->left = new TreeNode(2);
    root->right = new TreeNode(3);
    root->left->left = new TreeNode(4);
    root->left->right = new TreeNode(5);
    root->right->left = new TreeNode(6);
    root->right->right = new TreeNode(7);
    
    // Perform level order traversal
    levelOrderTraversal(root);
    
    // Perform level order traversal with level information
    levelOrderTraversalWithLevels(root);
    
    // Clean up tree nodes to prevent memory leaks
    // (Not implemented here for brevity)
    
    return 0;
}
```

### Shortest Path in Unweighted Graphs

BFS can find the shortest path in an unweighted graph:

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

class Graph {
private:
    int V; // Number of vertices
    vector<vector<int>> adj; // Adjacency list

public:
    Graph(int vertices) {
        V = vertices;
        adj.resize(V);
    }

    // Add an edge to the graph
    void addEdge(int v, int w) {
        adj[v].push_back(w);
        adj[w].push_back(v); // For undirected graph
    }

    // Find shortest path from source to all vertices
    void shortestPath(int source) {
        // Mark all vertices as not visited
        vector<bool> visited(V, false);
        
        // Create a queue for BFS
        queue<int> q;
        
        // Distance array
        vector<int> distance(V, -1);
        
        // Parent array to store the path
        vector<int> parent(V, -1);
        
        // Mark the source node as visited and enqueue it
        visited[source] = true;
        distance[source] = 0;
        q.push(source);
        
        while (!q.empty()) {
            // Dequeue a vertex from queue
            int u = q.front();
            q.pop();
            
            // Get all adjacent vertices of the dequeued vertex
            for (int v : adj[u]) {
                if (!visited[v]) {
                    visited[v] = true;
                    distance[v] = distance[u] + 1;
                    parent[v] = u;
                    q.push(v);
                }
            }
        }
        
        // Print shortest distances
        cout << "Shortest distances from source " << source << ":" << endl;
        for (int i = 0; i < V; i++) {
            cout << "Distance to vertex " << i << " is " << distance[i] << endl;
        }
        
        // Print paths
        cout << "\nShortest paths from source " << source << ":" << endl;
        for (int i = 0; i < V; i++) {
            if (i != source && distance[i] != -1) {
                cout << "Path to vertex " << i << ": ";
                printPath(parent, i);
                cout << endl;
            }
        }
    }
    
private:
    // Utility function to print the path
    void printPath(const vector<int>& parent, int j) {
        if (parent[j] == -1)
            return;
        
        printPath(parent, parent[j]);
        cout << parent[j] << " -> ";
        
        if (j == parent.size() - 1)
            cout << j;
    }
};

int main() {
    // Create a graph
    Graph g(8);
    g.addEdge(0, 1);
    g.addEdge(0, 3);
    g.addEdge(1, 2);
    g.addEdge(3, 4);
    g.addEdge(3, 7);
    g.addEdge(4, 5);
    g.addEdge(4, 6);
    g.addEdge(4, 7);
    g.addEdge(5, 6);
    g.addEdge(6, 7);
    
    // Find shortest paths
    g.shortestPath(0);
    
    return 0;
}
```

## 5.2 CPU Scheduling and Job Processing

Queues are fundamental to operating systems for managing processes and resources. Let's implement some common CPU scheduling algorithms using queues.

### First-Come, First-Served (FCFS) Scheduling

The simplest scheduling algorithm is FCFS, which processes jobs in the order they arrive:

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

struct Process {
    int id;
    int arrivalTime;
    int burstTime;
    int completionTime;
    int turnaroundTime;
    int waitingTime;
    
    Process(int i, int arrival, int burst) 
        : id(i), arrivalTime(arrival), burstTime(burst),
          completionTime(0), turnaroundTime(0), waitingTime(0) {}
};

void FCFS_Scheduling(vector<Process>& processes) {
    // Sort processes by arrival time
    sort(processes.begin(), processes.end(), 
         [](const Process& a, const Process& b) {
             return a.arrivalTime < b.arrivalTime;
         });
    
    int currentTime = 0;
    double totalWaitingTime = 0;
    double totalTurnaroundTime = 0;
    
    cout << "\nProcess Execution Order: ";
    
    for (auto& p : processes) {
        // If current time is less than arrival time, CPU is idle
        if (currentTime < p.arrivalTime) {
            currentTime = p.arrivalTime;
        }
        
        p.waitingTime = currentTime - p.arrivalTime;
        
        // Execute the process
        currentTime += p.burstTime;
        
        p.completionTime = currentTime;
        p.turnaroundTime = p.completionTime - p.arrivalTime;
        
        totalWaitingTime += p.waitingTime;
        totalTurnaroundTime += p.turnaroundTime;
        
        cout << "P" << p.id << " ";
    }
    
    double avgWaitingTime = totalWaitingTime / processes.size();
    double avgTurnaroundTime = totalTurnaroundTime / processes.size();
    
    cout << "\n\nProcess\tArrival\tBurst\tCompletion\tTurnaround\tWaiting" << endl;
    for (const auto& p : processes) {
        cout << "P" << p.id << "\t" 
             << p.arrivalTime << "\t" 
             << p.burstTime << "\t" 
             << p.completionTime << "\t\t"
             << p.turnaroundTime << "\t\t" 
             << p.waitingTime << endl;
    }
    
    cout << "\nAverage Waiting Time: " << avgWaitingTime << endl;
    cout << "Average Turnaround Time: " << avgTurnaroundTime << endl;
}

int main() {
    vector<Process> processes = {
        Process(1, 0, 6),
        Process(2, 1, 4),
        Process(3, 2, 2),
        Process(4, 3, 8)
    };
    
    cout << "First-Come, First-Served (FCFS) Scheduling" << endl;
    FCFS_Scheduling(processes);
    
    return 0;
}
```

### Round-Robin Scheduling

Round-Robin scheduling is a preemptive version of FCFS that uses a time quantum:

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

struct Process {
    int id;
    int arrivalTime;
    int burstTime;
    int remainingTime;
    int completionTime;
    int turnaroundTime;
    int waitingTime;
    
    Process(int i, int arrival, int burst) 
        : id(i), arrivalTime(arrival), burstTime(burst), remainingTime(burst),
          completionTime(0), turnaroundTime(0), waitingTime(0) {}
};

void RoundRobin_Scheduling(vector<Process>& processes, int timeQuantum) {
    int n = processes.size();
    
    // Create a ready queue
    queue<int> readyQueue;  // Stores process indices
    
    // Sort processes by arrival time
    sort(processes.begin(), processes.end(), 
         [](const Process& a, const Process& b) {
             return a.arrivalTime < b.arrivalTime;
         });
    
    int currentTime = 0;
    vector<bool> completed(n, false);
    int completedCount = 0;
    int index = 0;  // Index to track newly arriving processes
    
    // Add the first process to the ready queue
    if (!processes.empty()) {
        readyQueue.push(0);
        index = 1;
    }
    
    cout << "\nProcess Execution Order: ";
    
    while (completedCount < n) {
        // If ready queue is empty but processes are still pending
        if (readyQueue.empty()) {
            currentTime = processes[index].arrivalTime;
            readyQueue.push(index++);
        }
        
        // Get the next process from the ready queue
        int i = readyQueue.front();
        readyQueue.pop();
        
        cout << "P" << processes[i].id << " ";
        
        // Execute the process for time quantum or until completion
        int executeTime = min(timeQuantum, processes[i].remainingTime);
        currentTime += executeTime;
        processes[i].remainingTime -= executeTime;
        
        // Check for newly arrived processes during this time slice
        while (index < n && processes[index].arrivalTime <= currentTime) {
            readyQueue.push(index++);
        }
        
        // If the process is not completed, add it back to the ready queue
        if (processes[i].remainingTime > 0) {
            readyQueue.push(i);
        } else {
            // Process is completed
            completed[i] = true;
            completedCount++;
            
            // Record completion metrics
            processes[i].completionTime = currentTime;
            processes[i].turnaroundTime = processes[i].completionTime - processes[i].arrivalTime;
            processes[i].waitingTime = processes[i].turnaroundTime - processes[i].burstTime;
        }
    }
    
    // Calculate averages
    double totalWaitingTime = 0;
    double totalTurnaroundTime = 0;
    
    for (const auto& p : processes) {
        totalWaitingTime += p.waitingTime;
        totalTurnaroundTime += p.turnaroundTime;
    }
    
    double avgWaitingTime = totalWaitingTime / n;
    double avgTurnaroundTime = totalTurnaroundTime / n;
    
    cout << "\n\nProcess\tArrival\tBurst\tCompletion\tTurnaround\tWaiting" << endl;
    for (const auto& p : processes) {
        cout << "P" << p.id << "\t" 
             << p.arrivalTime << "\t" 
             << p.burstTime << "\t" 
             << p.completionTime << "\t\t"
             << p.turnaroundTime << "\t\t" 
             << p.waitingTime << endl;
    }
    
    cout << "\nAverage Waiting Time: " << avgWaitingTime << endl;
    cout << "Average Turnaround Time: " << avgTurnaroundTime << endl;
}

int main() {
    vector<Process> processes = {
        Process(1, 0, 10),
        Process(2, 1, 5),
        Process(3, 2, 8),
        Process(4, 3, 2)
    };
    
    int timeQuantum = 2;
    
    cout << "Round-Robin Scheduling (Time Quantum = " << timeQuantum << ")" << endl;
    RoundRobin_Scheduling(processes, timeQuantum);
    
    return 0;
}
```

### Priority Scheduling

Priority scheduling assigns priorities to processes and executes them accordingly:

```cpp
#include <iostream>
#include <queue>
#include <vector>
using namespace std;

struct Process {
    int id;
    int arrivalTime;
    int burstTime;
    int priority;  // Lower value means higher priority
    int completionTime;
    int turnaroundTime;
    int waitingTime;
    
    Process(int i, int arrival, int burst, int prio) 
        : id(i), arrivalTime(arrival), burstTime(burst), priority(prio),
          completionTime(0), turnaroundTime(0), waitingTime(0) {}
};

void PriorityScheduling(vector<Process>& processes) {
    int n = processes.size();
    
    // Sort processes by arrival time
    sort(processes.begin(), processes.end(), 
         [](const Process& a, const Process& b) {
             return a.arrivalTime < b.arrivalTime;
         });
    
    int currentTime = 0;
    vector<bool> completed(n, false);
    int completedCount = 0;
    
    cout << "\nProcess Execution Order: ";
    
    while (completedCount < n) {
        // Find the highest priority process that has arrived and is not completed
        int selectedProcess = -1;
        int highestPriority = INT_MAX;
        
        for (int i = 0; i < n; i++) {
            if (!completed[i] && processes[i].arrivalTime <= currentTime && 
                processes[i].priority < highestPriority) {
                highestPriority = processes[i].priority;
                selectedProcess = i;
            }
        }
        
        // If no process is available, increment time
        if (selectedProcess == -1) {
            currentTime++;
            continue;
        }
        
        // Execute the selected process
        cout << "P" << processes[selectedProcess].id << " ";
        
        currentTime += processes[selectedProcess].burstTime;
        processes[selectedProcess].completionTime = currentTime;
        processes[selectedProcess].turnaroundTime = 
            processes[selectedProcess].completionTime - processes[selectedProcess].arrivalTime;
        processes[selectedProcess].waitingTime = 
            processes[selectedProcess].turnaroundTime - processes[selectedProcess].burstTime;
        
        completed[selectedProcess] = true;
        completedCount++;
    }
    
    // Calculate averages
    double totalWaitingTime = 0;
    double totalTurnaroundTime = 0;
    
    for (const auto& p : processes) {
        totalWaitingTime += p.waitingTime;
        totalTurnaroundTime += p.turnaroundTime;
    }
    
    double avgWaitingTime = totalWaitingTime / n;
    double avgTurnaroundTime = totalTurnaroundTime / n;
    
    cout << "\n\nProcess\tArrival\tBurst\tPriority\tCompletion\tTurnaround\tWaiting" << endl;
    for (const auto& p : processes) {
        cout << "P" << p.id << "\t" 
             << p.arrivalTime << "\t" 
             << p.burstTime << "\t" 
             << p.priority << "\t\t"
             << p.completionTime << "\t\t"
             << p.turnaroundTime << "\t\t" 
             << p.waitingTime << endl;
    }
    
    cout << "\nAverage Waiting Time: " << avgWaitingTime << endl;
    cout << "Average Turnaround Time: " << avgTurnaroundTime << endl;
}

int main() {
    vector<Process> processes = {
        Process(1, 0, 5, 2),
        Process(2, 1, 3, 1),
        Process(3, 2, 8, 4),
        Process(4, 3, 2, 3)
    };
    
    cout << "Priority Scheduling" << endl;
    PriorityScheduling(processes);
    
    return 0;
}
```

## 5.3 Message Queues and Event Handling

Message queues are essential for asynchronous communication between different parts of a system. Let's explore how they work and implement a simple message queue system.

### Producer-Consumer Problem

The producer-consumer problem is a classic example of inter-process communication:

```cpp
#include <iostream>
#include <queue>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <chrono>
using namespace std;

const int BUFFER_SIZE = 5;

class MessageQueue {
private:
    queue<int> buffer;
    mutex mtx;
    condition_variable not_full;
    condition_variable not_empty;
    int capacity;

public:
    MessageQueue(int cap) : capacity(cap) {}
    
    void produce(int item) {
        unique_lock<mutex> lock(mtx);
        
        // Wait if the buffer is full
        not_full.wait(lock, [this]() { return buffer.size() < capacity; });
        
        // Add item to buffer
        buffer.push(item);
        cout << "Produced: " << item << " (Buffer size: " << buffer.size() << ")" << endl;
        
        // Notify that the buffer is not empty
        not_empty.notify_one();
    }
    
    int consume() {
        unique_lock<mutex> lock(mtx);
        
        // Wait if the buffer is empty
        not_empty.wait(lock, [this]() { return !buffer.empty(); });
        
        // Remove item from buffer
        int item = buffer.front();
        buffer.pop();
        cout << "Consumed: " << item << " (Buffer size: " << buffer.size() << ")" << endl;
        
        // Notify that the buffer is not full
        not_full.notify_one();
        
        return item;
    }
};

void producer(MessageQueue& queue, int start, int count) {
    for (int i = start; i < start + count; i++) {
        this_thread::sleep_for(chrono::milliseconds(rand() % 500 + 500));
        queue.produce(i);
    }
}

void consumer(MessageQueue& queue, int id, int count) {
    for (int i = 0; i < count; i++) {
        this_thread::sleep_for(chrono::milliseconds(rand() % 1000 + 1000));
        int item = queue.consume();
    }
}

int main() {
    srand(time(nullptr));
    
    MessageQueue queue(BUFFER_SIZE);
    
    // Create producer and consumer threads
    thread producerThread1(producer, ref(queue), 1, 5);
    thread producerThread2(producer, ref(queue), 100, 5);
    thread consumerThread1(consumer, ref(queue), 1, 5);
    thread consumerThread2(consumer, ref(queue), 2, 5);
    
    // Wait for threads to finish
    producerThread1.join();
    producerThread2.join();
    consumerThread1.join();
    consumerThread2.join();
    
    return 0;
}
```

### Event-Driven Programming

Event-driven programming is a paradigm where the flow of the program is determined by events:

```cpp
#include <iostream>
#include <queue>
#include <map>
#include <functional>
#include <string>
#include <thread>
#include <mutex>
#include <condition_variable>
using namespace std;

// Define different types of events
enum class EventType {
    BUTTON_CLICK,
    MOUSE_MOVE,
    KEY_PRESS,
    TIMER
};

// Event structure
struct Event {
    EventType type;
    string data;
    
    Event(EventType t, string d) : type(t), data(d) {}
};

// Event handler type
using EventHandler = function<void(const Event&)>;

class EventQueue {
private:
    queue<Event> events;
    mutex mtx;
    condition_variable cv;
    bool running;
    
    // Event handlers
    map<EventType, vector<EventHandler>> handlers;

public:
    EventQueue() : running(true) {}
    
    // Register an event handler
    void registerHandler(EventType type, EventHandler handler) {
        lock_guard<mutex> lock(mtx);
        handlers[type].push_back(handler);
    }
    
    // Add an event to the queue
    void pushEvent(const Event& event) {
        lock_guard<mutex> lock(mtx);
        events.push(event);
        cv.notify_one();
    }
    
    // Process events in the queue
    void processEvents() {
        while (running) {
            unique_lock<mutex> lock(mtx);
            
            // Wait for an event
            cv.wait(lock, [this]() { return !events.empty() || !running; });
            
            if (!running) break;
            
            // Get the next event
            Event event = events.front();
            events.pop();
            
            // Release the lock while processing the event
            lock.unlock();
            
            // Call the handlers for this event type
            if (handlers.find(event.type) != handlers.end()) {
                for (const auto& handler : handlers[event.type]) {
                    handler(event);
                }
            }
        }
    }
    
    // Stop processing events
    void stop() {
        lock_guard<mutex> lock(mtx);
        running = false;
        cv.notify_all();
    }
};

int main() {
    EventQueue eventQueue;
    
    // Register event handlers
    eventQueue.registerHandler(EventType::BUTTON_CLICK, [](const Event& e) {
        cout << "Button clicked: " << e.data << endl;
    });
    
    eventQueue.registerHandler(EventType::KEY_PRESS, [](const Event& e) {
        cout << "Key pressed: " << e.data << endl;
    });
    
    // Start the event processing thread
    thread eventThread(&EventQueue::processEvents, &eventQueue);
    
    // Simulate some events
    eventQueue.pushEvent(Event(EventType::BUTTON_CLICK, "Submit Button"));
    this_thread::sleep_for(chrono::milliseconds(500));
    
    eventQueue.pushEvent(Event(EventType::KEY_PRESS, "Enter"));
    this_thread::sleep_for(chrono::milliseconds(500));
    
    eventQueue.pushEvent(Event(EventType::BUTTON_CLICK, "Cancel Button"));
    this_thread::sleep_for(chrono::milliseconds(500));
    
    // Stop the event queue
    eventQueue.stop();
    eventThread.join();
    
    return 0;
}
```

### Message Broker Implementation

A message broker mediates communication between different parts of a distributed application:

```cpp
#include <iostream>
#include <queue>
#include <map>
#include <string>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <functional>
#include <vector>
using namespace std;

// Message structure
struct Message {
    string topic;
    string data;
    
    Message(string t, string d) : topic(t), data(d) {}
};

// Subscriber callback type
using SubscriberCallback = function<void(const Message&)>;

class MessageBroker {
private:
    map<string, vector<SubscriberCallback>> subscribers;
    queue<Message> messageQueue;
    mutex mtx;
    condition_variable cv;
    bool running;
    
    thread dispatcherThread;

public:
    MessageBroker() : running(true) {
        // Start the dispatcher thread
        dispatcherThread = thread(&MessageBroker::dispatchMessages, this);
    }
    
    ~MessageBroker() {
        if (running) {
            stop();
        }
    }
    
    // Subscribe to a topic
    void subscribe(const string& topic, SubscriberCallback callback) {
        lock_guard<mutex> lock(mtx);
        subscribers[topic].push_back(callback);
    }
    
    // Publish a message to a topic
    void publish(const string& topic, const string& data) {
        lock_guard<mutex> lock(mtx);
        messageQueue.push(Message(topic, data));
        cv.notify_one();
    }
    
    // Dispatch messages to subscribers
    void dispatchMessages() {
        while (running) {
            unique_lock<mutex> lock(mtx);
            
            // Wait for a message
            cv.wait(lock, [this]() { return !messageQueue.empty() || !running; });
            
            if (!running && messageQueue.empty()) break;
            
            // Get the next message
            Message message = messageQueue.front();
            messageQueue.pop();
            
            // Get subscribers for this topic
            auto it = subscribers.find(message.topic);
            if (it != subscribers.end()) {
                vector<SubscriberCallback> topicSubscribers = it->second;
                
                // Release the lock while notifying subscribers
                lock.unlock();
                
                // Notify all subscribers
                for (const auto& callback : topicSubscribers) {
                    callback(message);
                }
            } else {
                lock.unlock();
            }
        }
    }
    
    // Stop the message broker
    void stop() {
        lock_guard<mutex> lock(mtx);
        running = false;
        cv.notify_all();
        if (dispatcherThread.joinable()) {
            dispatcherThread.join();
        }
    }
};

int main() {
    MessageBroker broker;
    
    // Subscribe to topics
    broker.subscribe("weather", [](const Message& msg) {
        cout << "Weather update: " << msg.data << endl;
    });
    
    broker.subscribe("news", [](const Message& msg) {
        cout << "News alert: " << msg.data << endl;
    });
    
    broker.subscribe("weather", [](const Message& msg) {
        cout << "Weather alert for mobile: " << msg.data << endl;
    });
    
    // Publish messages
    broker.publish("weather", "It's going to rain today");
    this_thread::sleep_for(chrono::milliseconds(500));
    
    broker.publish("news", "Breaking news: Important announcement");
    this_thread::sleep_for(chrono::milliseconds(500));
    
    broker.publish("weather", "Temperature dropping to 10°C");
    this_thread::sleep_for(chrono::milliseconds(500));
    
    broker.publish("sports", "Team A wins the championship");
    this_thread::sleep_for(chrono::milliseconds(500));
    
    // Stop the broker
    broker.stop();
    
    return 0;
}
```

## Practical Problems

Let's solve some practical problems that utilize queues.

### Problem 1: Rotting Oranges

**Problem Link**: [LeetCode 994 - Rotting Oranges](https://leetcode.com/problems/rotting-oranges/)

**Problem Statement**:
You are given an `m x n` grid where each cell can have one of three values:
- 0 representing an empty cell,
- 1 representing a fresh orange, or
- 2 representing a rotten orange.

Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten.

Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return -1.

**Sample Test Cases**:
```
Input: grid = [[2,1,1],[1,1,0],[0,1,1]]
Output: 4

Input: grid = [[2,1,1],[0,1,1],[1,0,1]]
Output: -1
Explanation: The orange in the bottom left corner (row 2, column 0) is never rotten, because rotting only happens 4-directionally.

Input: grid = [[0,2]]
Output: 0
Explanation: Since there are already no fresh oranges at minute 0, the answer is just 0.
```

**Approach 1: BFS**

We can use BFS to simulate the rotting process:

```cpp
#include <iostream>
#include <vector>
#include <queue>
using namespace std;

int orangesRotting(vector<vector<int>>& grid) {
    int rows = grid.size();
    int cols = grid[0].size();
    
    // Queue to store positions of rotten oranges
    queue<pair<int, int>> q;
    int freshOranges = 0;
    
    // Count fresh oranges and enqueue rotten oranges
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            if (grid[i][j] == 2) {
                q.push({i, j});
            } else if (grid[i][j] == 1) {
                freshOranges++;
            }
        }
    }
    
    // If there are no fresh oranges, return 0
    if (freshOranges == 0) return 0;
    
    // Directions for 4-directional adjacency
    vector<pair<int, int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
    
    int minutes = 0;
    
    // BFS
    while (!q.empty()) {
        int size = q.size();
        bool rotted = false;
        
        for (int i = 0; i < size; i++) {
            auto [r, c] = q.front();
            q.pop();
            
            // Check all adjacent cells
            for (auto [dr, dc] : directions) {
                int nr = r + dr;
                int nc = c + dc;
                
                // If valid cell and has fresh orange
                if (nr >= 0 && nr < rows && nc >= 0 && nc < cols && grid[nr][nc] == 1) {
                    grid[nr][nc] = 2; // Make it rotten
                    freshOranges--;
                    q.push({nr, nc});
                    rotted = true;
                }
            }
        }
        
        if (rotted) minutes++;
    }
    
    // If there are still fresh oranges, return -1
    return freshOranges > 0 ? -1 : minutes;
}

int main() {
    vector<vector<int>> grid1 = {{2, 1, 1}, {1, 1, 0}, {0, 1, 1}};
    cout << "Grid 1 minutes: " << orangesRotting(grid1) << endl;
    
    vector<vector<int>> grid2 = {{2, 1, 1}, {0, 1, 1}, {1, 0, 1}};
    cout << "Grid 2 minutes: " << orangesRotting(grid2) << endl;
    
    vector<vector<int>> grid3 = {{0, 2}};
    cout << "Grid 3 minutes: " << orangesRotting(grid3) << endl;
    
    return 0;
}
```

**Time Complexity**: O(rows * cols) - each cell is processed at most once
**Space Complexity**: O(rows * cols) for the queue

**Explanation**:
1. First, we count all fresh oranges and enqueue all rotten oranges
2. We perform BFS starting from all rotten oranges simultaneously
3. For each minute, we rot all adjacent fresh oranges and enqueue them
4. We continue until no more oranges can be rotted
5. If there are still fresh oranges left, we return -1
6. Otherwise, we return the number of minutes elapsed

### Problem 2: Task Scheduler

**Problem Link**: [LeetCode 621 - Task Scheduler](https://leetcode.com/problems/task-scheduler/)

**Problem Statement**:
Given a characters array `tasks`, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer `n` that represents the cooldown period between two same tasks (the same letter in the array), that is that there must be at least `n` units of time between any two same tasks.

Return the least number of units of time that the CPU will take to finish all the given tasks.

**Sample Test Cases**:
```
Input: tasks = ["A","A","A","B","B","B"], n = 2
Output: 8
Explanation: 
A -> B -> idle -> A -> B -> idle -> A -> B
There is at least 2 units of time between any two same tasks.

Input: tasks = ["A","A","A","B","B","B"], n = 0
Output: 6
Explanation: On this case any permutation of size 6 would work since n = 0.
["A","A","A","B","B","B"]
["A","B","A","B","A","B"]
["B","B","B","A","A","A"]
...
And so on.

Input: tasks = ["A","A","A","A","A","A","B","C","D","E","F","G"], n = 2
Output: 16
Explanation: 
One possible solution is
A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> idle -> idle -> A -> idle -> idle -> A
```

**Approach 1: Greedy with Priority Queue**

We can use a priority queue to always schedule the most frequent tasks first:

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <unordered_map>
using namespace std;

int leastInterval(vector<char>& tasks, int n) {
    // Count the frequency of each task
    unordered_map<char, int> freq;
    for (char task : tasks) {
        freq[task]++;
    }
    
    // Use priority queue to keep track of the most frequent tasks
    priority_queue<int> pq;
    for (auto& [task, count] : freq) {
        pq.push(count);
    }
    
    int cycles = 0;
    
    while (!pq.empty()) {
        // Tasks that will be executed in the current cycle
        vector<int> temp;
        
        // Try to schedule up to n+1 different tasks
        for (int i = 0; i <= n; i++) {
            if (!pq.empty()) {
                temp.push_back(pq.top() - 1); // Execute one instance of the task
                pq.pop();
            }
        }
        
        // Put the tasks with remaining instances back into the priority queue
        for (int count : temp) {
            if (count > 0) {
                pq.push(count);
            }
        }
        
        // If there are tasks remaining, we need a full cycle (n+1)
        // Otherwise, we just need the number of tasks we executed
        cycles += pq.empty() ? temp.size() : n + 1;
    }
    
    return cycles;
}

int main() {
    vector<char> tasks1 = {'A', 'A', 'A', 'B', 'B', 'B'};
    int n1 = 2;
    cout << "Least interval for tasks1: " << leastInterval(tasks1, n1) << endl;
    
    vector<char> tasks2 = {'A', 'A', 'A', 'B', 'B', 'B'};
    int n2 = 0;
    cout << "Least interval for tasks2: " << leastInterval(tasks2, n2) << endl;
    
    vector<char> tasks3 = {'A', 'A', 'A', 'A', 'A', 'A', 'B', 'C', 'D', 'E', 'F', 'G'};
    int n3 = 2;
    cout << "Least interval for tasks3: " << leastInterval(tasks3, n3) << endl;
    
    return 0;
}
```

**Approach 2: Mathematical Solution**

There's also a mathematical solution that doesn't require a priority queue:

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <algorithm>
using namespace std;

int leastInterval(vector<char>& tasks, int n) {
    // Count the frequency of each task
    unordered_map<char, int> freq;
    for (char task : tasks) {
        freq[task]++;
    }
    
    // Find the maximum frequency
    int maxFreq = 0;
    for (auto& [task, count] : freq) {
        maxFreq = max(maxFreq, count);
    }
    
    // Count how many tasks have the maximum frequency
    int maxCount = 0;
    for (auto& [task, count] : freq) {
        if (count == maxFreq) {
            maxCount++;
        }
    }
    
    // Calculate the result
    int result = (maxFreq - 1) * (n + 1) + maxCount;
    
    // The result cannot be less than the number of tasks
    return max(result, (int)tasks.size());
}

int main() {
    vector<char> tasks1 = {'A', 'A', 'A', 'B', 'B', 'B'};
    int n1 = 2;
    cout << "Least interval for tasks1: " << leastInterval(tasks1, n1) << endl;
    
    vector<char> tasks2 = {'A', 'A', 'A', 'B', 'B', 'B'};
    int n2 = 0;
    cout << "Least interval for tasks2: " << leastInterval(tasks2, n2) << endl;
    
    vector<char> tasks3 = {'A', 'A', 'A', 'A', 'A', 'A', 'B', 'C', 'D', 'E', 'F', 'G'};
    int n3 = 2;
    cout << "Least interval for tasks3: " << leastInterval(tasks3, n3) << endl;
    
    return 0;
}
```

**Time Complexity**: O(n) where n is the number of tasks
**Space Complexity**: O(1) since there are at most 26 different tasks

**Explanation**:
1. We count the frequency of each task
2. We find the maximum frequency (maxFreq) and how many tasks have this frequency (maxCount)
3. The minimum time is at least (maxFreq - 1) * (n + 1) + maxCount
   - (maxFreq - 1) represents the number of cycles needed for all but the last occurrence of the most frequent tasks
   - (n + 1) is the length of each cycle
   - maxCount is the number of tasks that need to be performed in the last cycle
4. However, if there are many different types of tasks, we might be able to fill all the idle slots, so the result cannot be less than the total number of tasks

## Summary

In this module, we've explored advanced applications of queues:

1. **Breadth-First Search (BFS)**:
   - Graph traversal algorithm that uses a queue to visit nodes level by level
   - Level order traversal of trees
   - Finding shortest paths in unweighted graphs

2. **CPU Scheduling and Job Processing**:
   - First-Come, First-Served (FCFS) scheduling
   - Round-Robin scheduling with time quanta
   - Priority scheduling based on job importance

3. **Message Queues and Event Handling**:
   - Producer-consumer pattern for asynchronous communication
   - Event-driven programming using event queues
   - Message broker systems for distributed applications

We've also solved practical problems that demonstrate the power of queues:
- Using BFS to simulate the spread of rot among oranges
- Scheduling tasks with cooldown periods using a priority queue

These applications highlight how queues are essential for managing orderly processing, level-by-level traversal, and asynchronous communication in various computing systems.

In our next module, we'll explore practical problems and interview preparation related to stacks and queues.