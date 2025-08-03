# Module 7: Cutting-Edge Research and Future Directions for Stacks and Queues

Welcome to the final module of our comprehensive stack and queue tutorial! Throughout this series, we've covered the fundamentals, implementations, variations, applications, and practical problems related to these essential data structures. In this concluding module, we'll explore advanced system design applications, recent research developments, and future directions for stacks and queues.

## 7.1 Recent Research Developments

While stacks and queues are fundamental data structures that have been well-understood for decades, research continues to enhance their performance, applicability, and integration with modern computing paradigms.

### Lock-Free and Wait-Free Data Structures

In concurrent programming, traditional lock-based implementations can lead to contention, deadlocks, and priority inversion. Recent research has focused on developing efficient lock-free and wait-free stack and queue implementations.

```cpp
#include <iostream>
#include <atomic>
#include <thread>
#include <vector>
using namespace std;

// A simple lock-free stack implementation
template <typename T>
class LockFreeStack {
private:
    struct Node {
        T data;
        Node* next;
        
        Node(const T& value) : data(value), next(nullptr) {}
    };
    
    atomic<Node*> head;
    
public:
    LockFreeStack() : head(nullptr) {}
    
    ~LockFreeStack() {
        // Clean up nodes
        Node* current = head.load();
        while (current) {
            Node* next = current->next;
            delete current;
            current = next;
        }
    }
    
    void push(const T& value) {
        Node* newNode = new Node(value);
        
        // Use compare_exchange_weak for lock-free push
        Node* oldHead = head.load();
        do {
            newNode->next = oldHead;
        } while (!head.compare_exchange_weak(oldHead, newNode));
    }
    
    bool pop(T& result) {
        Node* oldHead = head.load();
        
        // If stack is empty
        if (!oldHead) {
            return false;
        }
        
        // Try to update head to next node
        Node* newHead = oldHead->next;
        while (!head.compare_exchange_weak(oldHead, newHead)) {
            // If head changed, update our local variables
            if (!oldHead) {
                return false;
            }
            newHead = oldHead->next;
        }
        
        // Successfully popped
        result = oldHead->data;
        delete oldHead;
        return true;
    }
    
    bool isEmpty() {
        return head.load() == nullptr;
    }
};

// Example usage in a multi-threaded environment
void testLockFreeStack() {
    LockFreeStack<int> stack;
    
    // Multiple threads pushing elements
    vector<thread> pushThreads;
    for (int i = 0; i < 5; i++) {
        pushThreads.emplace_back([&stack, i]() {
            for (int j = 0; j < 1000; j++) {
                stack.push(i * 1000 + j);
            }
        });
    }
    
    // Multiple threads popping elements
    vector<thread> popThreads;
    vector<vector<int>> poppedValues(5);
    for (int i = 0; i < 5; i++) {
        popThreads.emplace_back([&stack, &poppedValues, i]() {
            int value;
            for (int j = 0; j < 1000; j++) {
                if (stack.pop(value)) {
                    poppedValues[i].push_back(value);
                }
            }
        });
    }
    
    // Join threads
    for (auto& t : pushThreads) {
        t.join();
    }
    for (auto& t : popThreads) {
        t.join();
    }
    
    // Count total popped elements
    int totalPopped = 0;
    for (const auto& vec : poppedValues) {
        totalPopped += vec.size();
    }
    
    cout << "Total elements pushed: 5000" << endl;
    cout << "Total elements popped: " << totalPopped << endl;
    
    // Pop remaining elements
    int value;
    int remaining = 0;
    while (stack.pop(value)) {
        remaining++;
    }
    
    cout << "Remaining elements: " << remaining << endl;
    cout << "Total accounted for: " << (totalPopped + remaining) << endl;
}
```

**Research Highlights**:
- **ABA Problem Solutions**: The ABA problem occurs in lock-free algorithms when a thread reads a value A, other threads change it to B and back to A, causing the first thread to think nothing has changed. Recent research has introduced advanced techniques like hazard pointers and memory reclamation strategies to address this.
- **Memory Reclamation**: Safe memory reclamation in lock-free data structures is a significant research area, with techniques like epoch-based reclamation and reference counting.
- **Practical Performance Analysis**: Studies comparing lock-free structures with traditional lock-based ones under different workloads and contention scenarios.

### Cache-Efficient Data Structures

Modern processors have complex memory hierarchies with multiple cache levels. Research has explored making stacks and queues more cache-efficient:

```cpp
#include <iostream>
#include <vector>
using namespace std;

// A cache-friendly queue implementation
template <typename T>
class CacheEfficientQueue {
private:
    vector<T> data;
    size_t capacity;
    size_t head;
    size_t tail;
    size_t size;
    
    // Calculate the next index with cache-line alignment
    size_t nextIndex(size_t index) {
        // Assume 64-byte cache line and sizeof(T) is known
        constexpr size_t CACHE_LINE_SIZE = 64;
        constexpr size_t ELEMENTS_PER_CACHE_LINE = CACHE_LINE_SIZE / sizeof(T);
        
        // Move to the next cache line if we're at the end of the current one
        if ((index + 1) % ELEMENTS_PER_CACHE_LINE == 0) {
            return (index + 1 + ELEMENTS_PER_CACHE_LINE - 1) & ~(ELEMENTS_PER_CACHE_LINE - 1);
        }
        return index + 1;
    }
    
public:
    CacheEfficientQueue(size_t capacity) : capacity(capacity), head(0), tail(0), size(0) {
        // Allocate extra space to handle cache-line alignment
        data.resize(capacity * 2);
    }
    
    bool enqueue(const T& value) {
        if (size == capacity) {
            return false;  // Queue is full
        }
        
        data[tail] = value;
        tail = nextIndex(tail) % data.size();
        size++;
        return true;
    }
    
    bool dequeue(T& value) {
        if (size == 0) {
            return false;  // Queue is empty
        }
        
        value = data[head];
        head = nextIndex(head) % data.size();
        size--;
        return true;
    }
    
    bool isEmpty() const {
        return size == 0;
    }
    
    bool isFull() const {
        return size == capacity;
    }
    
    size_t getSize() const {
        return size;
    }
};

// Simple benchmark to demonstrate cache efficiency
void benchmarkCacheEfficientQueue() {
    constexpr size_t QUEUE_SIZE = 1000000;
    constexpr size_t OPERATIONS = 10000000;
    
    CacheEfficientQueue<int> queue(QUEUE_SIZE);
    
    // Fill the queue halfway
    for (size_t i = 0; i < QUEUE_SIZE / 2; i++) {
        queue.enqueue(i);
    }
    
    // Perform interleaved enqueue and dequeue operations
    auto start = chrono::high_resolution_clock::now();
    
    for (size_t i = 0; i < OPERATIONS; i++) {
        int value;
        if (i % 2 == 0) {
            if (!queue.isFull()) {
                queue.enqueue(i);
            }
        } else {
            if (!queue.isEmpty()) {
                queue.dequeue(value);
            }
        }
    }
    
    auto end = chrono::high_resolution_clock::now();
    auto duration = chrono::duration_cast<chrono::milliseconds>(end - start);
    
    cout << "Cache-efficient queue: " << OPERATIONS << " operations in " 
         << duration.count() << " ms" << endl;
}
```

**Research Highlights**:
- **Cache Oblivious Algorithms**: Designs that work efficiently without explicit knowledge of cache parameters.
- **Spatial Locality Optimization**: Techniques to ensure consecutive operations access nearby memory locations.
- **False Sharing Prevention**: Methods to avoid performance degradation in parallel systems due to cache line contention.

### Persistent and Non-Volatile Memory Data Structures

With the advent of non-volatile memory (NVM) technologies, research has explored how to make stacks and queues persistent across system failures:

```cpp
#include <iostream>
#include <fstream>
#include <vector>
#include <string>
using namespace std;

// A simplified persistent stack implementation
template <typename T>
class PersistentStack {
private:
    vector<T> data;
    string filename;
    
    // Save the entire stack to disk
    void persist() {
        ofstream file(filename, ios::binary | ios::trunc);
        if (!file) {
            cerr << "Failed to open file for persistence: " << filename << endl;
            return;
        }
        
        // Write size
        size_t size = data.size();
        file.write(reinterpret_cast<char*>(&size), sizeof(size));
        
        // Write data
        if (size > 0) {
            file.write(reinterpret_cast<char*>(data.data()), size * sizeof(T));
        }
        
        file.flush();
    }
    
public:
    PersistentStack(const string& filename) : filename(filename) {
        // Try to load existing data
        ifstream file(filename, ios::binary);
        if (file) {
            // Read size
            size_t size;
            file.read(reinterpret_cast<char*>(&size), sizeof(size));
            
            // Read data
            if (size > 0) {
                data.resize(size);
                file.read(reinterpret_cast<char*>(data.data()), size * sizeof(T));
            }
        }
    }
    
    void push(const T& value) {
        data.push_back(value);
        persist();
    }
    
    bool pop(T& value) {
        if (data.empty()) {
            return false;
        }
        
        value = data.back();
        data.pop_back();
        persist();
        return true;
    }
    
    bool peek(T& value) const {
        if (data.empty()) {
            return false;
        }
        
        value = data.back();
        return true;
    }
    
    bool isEmpty() const {
        return data.empty();
    }
    
    size_t size() const {
        return data.size();
    }
};

// Example usage of persistent stack
void demonstratePersistentStack() {
    {
        // Create and use a persistent stack
        PersistentStack<int> stack("persistent_stack.dat");
        cout << "Initial stack size: " << stack.size() << endl;
        
        // Push some values
        for (int i = 1; i <= 5; i++) {
            stack.push(i * 10);
            cout << "Pushed: " << (i * 10) << endl;
        }
    }
    
    // Restart the program (simulated by creating a new stack object)
    {
        PersistentStack<int> stack("persistent_stack.dat");
        cout << "Stack size after restart: " << stack.size() << endl;
        
        // Pop all values
        int value;
        while (stack.pop(value)) {
            cout << "Popped: " << value << endl;
        }
    }
}
```

**Research Highlights**:
- **Crash-Consistent Data Structures**: Designs that maintain consistency even if a system crashes during an operation.
- **Efficient Logging and Checkpointing**: Techniques to minimize the overhead of ensuring persistence.
- **NVM-Specific Optimizations**: Approaches that consider the unique characteristics of NVM, such as higher write latency and limited write endurance.

## 7.2 Advanced System Design Applications

Stacks and queues play crucial roles in complex system designs. Let's explore some advanced applications.

### Microservices and Message Queues

Modern distributed systems often use message queues for communication between microservices:

```cpp
#include <iostream>
#include <queue>
#include <string>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <functional>
#include <map>
using namespace std;

// Message structure
struct Message {
    string id;
    string content;
    string source;
    string destination;
    
    Message(string id, string content, string source, string destination)
        : id(id), content(content), source(source), destination(destination) {}
};

// Simple message broker for microservices
class MessageBroker {
private:
    map<string, queue<Message>> queues;
    mutex mtx;
    condition_variable cv;
    
public:
    // Publish a message to a specific queue
    void publish(const string& queueName, const Message& message) {
        lock_guard<mutex> lock(mtx);
        queues[queueName].push(message);
        cv.notify_all();
        
        cout << "Published message [" << message.id << "] from " 
             << message.source << " to queue: " << queueName << endl;
    }
    
    // Consume a message from a queue (blocking)
    Message consume(const string& queueName) {
        unique_lock<mutex> lock(mtx);
        
        // Wait until there's a message in the queue
        cv.wait(lock, [this, &queueName]() {
            return !queues[queueName].empty();
        });
        
        Message message = queues[queueName].front();
        queues[queueName].pop();
        
        cout << "Consumed message [" << message.id << "] by " 
             << message.destination << " from queue: " << queueName << endl;
        
        return message;
    }
    
    // Try to consume a message from a queue (non-blocking)
    bool tryConsume(const string& queueName, Message& message) {
        lock_guard<mutex> lock(mtx);
        
        if (queues[queueName].empty()) {
            return false;
        }
        
        message = queues[queueName].front();
        queues[queueName].pop();
        
        cout << "Consumed message [" << message.id << "] by " 
             << message.destination << " from queue: " << queueName << endl;
        
        return true;
    }
    
    // Get the size of a queue
    size_t queueSize(const string& queueName) {
        lock_guard<mutex> lock(mtx);
        return queues[queueName].size();
    }
};

// Simulate a microservice
void microserviceSimulation() {
    MessageBroker broker;
    
    // Create producer threads
    thread orderService([&broker]() {
        for (int i = 1; i <= 5; i++) {
            this_thread::sleep_for(chrono::milliseconds(500));
            
            Message message(
                "ORD-" + to_string(i),
                "New order created for product " + to_string(i),
                "OrderService",
                "PaymentService"
            );
            
            broker.publish("payment_queue", message);
        }
    });
    
    thread inventoryService([&broker]() {
        for (int i = 1; i <= 3; i++) {
            this_thread::sleep_for(chrono::milliseconds(800));
            
            Message message(
                "INV-" + to_string(i),
                "Inventory updated for product " + to_string(i),
                "InventoryService",
                "NotificationService"
            );
            
            broker.publish("notification_queue", message);
        }
    });
    
    // Create consumer threads
    thread paymentService([&broker]() {
        for (int i = 1; i <= 5; i++) {
            Message message = broker.consume("payment_queue");
            
            // Process the message
            this_thread::sleep_for(chrono::milliseconds(700));
            
            // Forward to shipping
            Message forwardMessage(
                "PAY-" + message.id,
                "Payment processed for " + message.id,
                "PaymentService",
                "ShippingService"
            );
            
            broker.publish("shipping_queue", forwardMessage);
        }
    });
    
    thread shippingService([&broker]() {
        for (int i = 1; i <= 5; i++) {
            Message message = broker.consume("shipping_queue");
            
            // Process the message
            this_thread::sleep_for(chrono::milliseconds(1000));
            
            // Shipping completed
            cout << "Shipping completed for order related to " << message.id << endl;
        }
    });
    
    thread notificationService([&broker]() {
        for (int i = 1; i <= 3; i++) {
            Message message = broker.consume("notification_queue");
            
            // Process the message
            this_thread::sleep_for(chrono::milliseconds(300));
            
            // Notification sent
            cout << "Notification sent for " << message.id << endl;
        }
    });
    
    // Join all threads
    orderService.join();
    inventoryService.join();
    paymentService.join();
    shippingService.join();
    notificationService.join();
}
```

**Key System Design Aspects**:
- **Decoupling**: Message queues allow microservices to communicate asynchronously, reducing dependencies.
- **Load Leveling**: Queues absorb spikes in traffic, protecting downstream services from being overwhelmed.
- **Resilience**: Messages persist in queues even if a service is temporarily unavailable.
- **Scalability**: Services can be scaled independently based on their queue size and processing capacity.

### Stream Processing and Event Sourcing

Queues are fundamental to stream processing architectures and event sourcing patterns:

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <string>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <functional>
#include <chrono>
using namespace std;

// Event structure for event sourcing
struct Event {
    string id;
    string type;
    string entityId;
    string data;
    chrono::system_clock::time_point timestamp;
    
    Event(string id, string type, string entityId, string data)
        : id(id), type(type), entityId(entityId), data(data), 
          timestamp(chrono::system_clock::now()) {}
    
    string getTimeString() const {
        auto time_t_point = chrono::system_clock::to_time_t(timestamp);
        string time_str = ctime(&time_t_point);
        if (!time_str.empty() && time_str.back() == '\n') {
            time_str.pop_back();
        }
        return time_str;
    }
};

// Event Store - persists all events
class EventStore {
private:
    vector<Event> events;
    mutex mtx;
    
public:
    void append(const Event& event) {
        lock_guard<mutex> lock(mtx);
        events.push_back(event);
    }
    
    vector<Event> getEventsForEntity(const string& entityId) {
        lock_guard<mutex> lock(mtx);
        vector<Event> entityEvents;
        
        for (const auto& event : events) {
            if (event.entityId == entityId) {
                entityEvents.push_back(event);
            }
        }
        
        return entityEvents;
    }
    
    void printAllEvents() const {
        cout << "Event Store Contents:" << endl;
        cout << "-------------------" << endl;
        for (const auto& event : events) {
            cout << "ID: " << event.id 
                 << ", Type: " << event.type 
                 << ", Entity: " << event.entityId 
                 << ", Time: " << event.getTimeString() 
                 << ", Data: " << event.data << endl;
        }
        cout << "-------------------" << endl;
    }
};

// Event Bus - distributes events to subscribers
class EventBus {
private:
    queue<Event> eventQueue;
    mutex mtx;
    condition_variable cv;
    vector<function<void(const Event&)>> subscribers;
    bool running;
    thread processingThread;
    
    void processEvents() {
        while (running) {
            Event event{"-1", "", "", ""};
            bool hasEvent = false;
            
            {
                unique_lock<mutex> lock(mtx);
                cv.wait(lock, [this]() { return !eventQueue.empty() || !running; });
                
                if (!running && eventQueue.empty()) {
                    break;
                }
                
                if (!eventQueue.empty()) {
                    event = eventQueue.front();
                    eventQueue.pop();
                    hasEvent = true;
                }
            }
            
            if (hasEvent) {
                for (const auto& subscriber : subscribers) {
                    subscriber(event);
                }
            }
        }
    }
    
public:
    EventBus() : running(true) {
        processingThread = thread(&EventBus::processEvents, this);
    }
    
    ~EventBus() {
        {
            lock_guard<mutex> lock(mtx);
            running = false;
        }
        cv.notify_all();
        
        if (processingThread.joinable()) {
            processingThread.join();
        }
    }
    
    void publish(const Event& event) {
        {
            lock_guard<mutex> lock(mtx);
            eventQueue.push(event);
        }
        cv.notify_one();
    }
    
    void subscribe(function<void(const Event&)> subscriber) {
        lock_guard<mutex> lock(mtx);
        subscribers.push_back(subscriber);
    }
};

// Aggregate Root - an entity that's reconstituted from events
class OrderAggregate {
private:
    string orderId;
    string customerId;
    vector<string> orderItems;
    string status;
    
public:
    OrderAggregate(string orderId) : orderId(orderId), status("CREATED") {}
    
    void applyEvent(const Event& event) {
        if (event.type == "OrderCreated") {
            customerId = event.data;
            status = "CREATED";
            cout << "Order " << orderId << " created for customer " << customerId << endl;
        } 
        else if (event.type == "ItemAdded") {
            orderItems.push_back(event.data);
            cout << "Item " << event.data << " added to order " << orderId << endl;
        } 
        else if (event.type == "OrderPaid") {
            status = "PAID";
            cout << "Order " << orderId << " marked as paid" << endl;
        } 
        else if (event.type == "OrderShipped") {
            status = "SHIPPED";
            cout << "Order " << orderId << " marked as shipped" << endl;
        }
    }
    
    void printOrder() const {
        cout << "Order " << orderId << " Details:" << endl;
        cout << "-------------------" << endl;
        cout << "Customer: " << customerId << endl;
        cout << "Status: " << status << endl;
        cout << "Items:" << endl;
        for (const auto& item : orderItems) {
            cout << "- " << item << endl;
        }
        cout << "-------------------" << endl;
    }
};

// Command Handler - processes commands and emits events
class OrderCommandHandler {
private:
    EventStore& eventStore;
    EventBus& eventBus;
    
public:
    OrderCommandHandler(EventStore& store, EventBus& bus) 
        : eventStore(store), eventBus(bus) {}
    
    void createOrder(const string& orderId, const string& customerId) {
        Event event("EVT-" + to_string(chrono::system_clock::now().time_since_epoch().count()),
                   "OrderCreated", orderId, customerId);
        
        eventStore.append(event);
        eventBus.publish(event);
    }
    
    void addItem(const string& orderId, const string& itemName) {
        Event event("EVT-" + to_string(chrono::system_clock::now().time_since_epoch().count()),
                   "ItemAdded", orderId, itemName);
        
        eventStore.append(event);
        eventBus.publish(event);
    }
    
    void payOrder(const string& orderId) {
        Event event("EVT-" + to_string(chrono::system_clock::now().time_since_epoch().count()),
                   "OrderPaid", orderId, "");
        
        eventStore.append(event);
        eventBus.publish(event);
    }
    
    void shipOrder(const string& orderId) {
        Event event("EVT-" + to_string(chrono::system_clock::now().time_since_epoch().count()),
                   "OrderShipped", orderId, "");
        
        eventStore.append(event);
        eventBus.publish(event);
    }
    
    OrderAggregate getOrder(const string& orderId) {
        OrderAggregate order(orderId);
        
        vector<Event> events = eventStore.getEventsForEntity(orderId);
        for (const auto& event : events) {
            order.applyEvent(event);
        }
        
        return order;
    }
};

// Demonstrate event sourcing pattern
void eventSourcingDemo() {
    EventStore eventStore;
    EventBus eventBus;
    
    // Create a projection that updates when events occur
    map<string, int> orderCountByCustomer;
    eventBus.subscribe([&orderCountByCustomer](const Event& event) {
        if (event.type == "OrderCreated") {
            orderCountByCustomer[event.data]++;
            cout << "Projection updated: Customer " << event.data 
                 << " now has " << orderCountByCustomer[event.data] << " orders" << endl;
        }
    });
    
    // Create command handler
    OrderCommandHandler handler(eventStore, eventBus);
    
    // Process commands
    handler.createOrder("ORD-001", "CUST-001");
    this_thread::sleep_for(chrono::milliseconds(100));
    
    handler.addItem("ORD-001", "Product A");
    this_thread::sleep_for(chrono::milliseconds(100));
    
    handler.addItem("ORD-001", "Product B");
    this_thread::sleep_for(chrono::milliseconds(100));
    
    handler.createOrder("ORD-002", "CUST-001");
    this_thread::sleep_for(chrono::milliseconds(100));
    
    handler.addItem("ORD-002", "Product C");
    this_thread::sleep_for(chrono::milliseconds(100));
    
    handler.payOrder("ORD-001");
    this_thread::sleep_for(chrono::milliseconds(100));
    
    handler.shipOrder("ORD-001");
    this_thread::sleep_for(chrono::milliseconds(100));
    
    // Reconstruct the aggregate from events
    cout << "\nReconstructing orders from events:" << endl;
    OrderAggregate order1 = handler.getOrder("ORD-001");
    order1.printOrder();
    
    OrderAggregate order2 = handler.getOrder("ORD-002");
    order2.printOrder();
    
    // Print all events
    cout << "\nEvent history:" << endl;
    eventStore.printAllEvents();
}
```

**Key System Design Aspects**:
- **Event Sourcing**: Storing all state changes as a sequence of events.
- **Command-Query Responsibility Segregation (CQRS)**: Separating operations that change state from those that read state.
- **Eventual Consistency**: Updates are propagated asynchronously through the system.
- **Projections**: Derived views of data rebuilt from the event stream.

### Distributed System Coordination

Queues play a vital role in distributed system coordination:

```cpp
#include <iostream>
#include <queue>
#include <string>
#include <thread>
#include <mutex>
#include <condition_variable>
#include <chrono>
#include <random>
#include <functional>
#include <set>
using namespace std;

// Task structure for distributed processing
struct Task {
    string id;
    string description;
    int priority;
    chrono::seconds processingTime;
    
    Task(string id, string description, int priority, chrono::seconds processingTime)
        : id(id), description(description), priority(priority), processingTime(processingTime) {}
    
    // For priority queue
    bool operator<(const Task& other) const {
        return priority < other.priority;
    }
};

// Distributed task coordinator
class TaskCoordinator {
private:
    priority_queue<Task> taskQueue;
    set<string> processingTasks;
    set<string> completedTasks;
    mutex mtx;
    condition_variable cv;
    
    // For generating random processing times
    mt19937 rng;
    uniform_int_distribution<int> processingTimeDist;
    
public:
    TaskCoordinator() : rng(random_device()()), processingTimeDist(1, 5) {}
    
    // Add a new task to the queue
    void addTask(const Task& task) {
        lock_guard<mutex> lock(mtx);
        taskQueue.push(task);
        cv.notify_one();
        
        cout << "Task [" << task.id << "] added to queue with priority " 
             << task.priority << endl;
    }
    
    // Get a task to process (worker calls this)
    bool getTask(Task& task) {
        unique_lock<mutex> lock(mtx);
        
        // Wait for a task to become available
        cv.wait(lock, [this]() { return !taskQueue.empty(); });
        
        task = taskQueue.top();
        taskQueue.pop();
        
        // Mark task as being processed
        processingTasks.insert(task.id);
        
        cout << "Worker fetched task [" << task.id << "] for processing" << endl;
        return true;
    }
    
    // Mark a task as completed
    void completeTask(const string& taskId) {
        lock_guard<mutex> lock(mtx);
        
        // Remove from processing and add to completed
        processingTasks.erase(taskId);
        completedTasks.insert(taskId);
        
        cout << "Task [" << taskId << "] completed" << endl;
    }
    
    // Worker function
    void workerFunction(int workerId) {
        cout << "Worker " << workerId << " started" << endl;
        
        while (true) {
            Task task{"", "", 0, chrono::seconds(0)};
            
            // Try to get a task
            if (getTask(task)) {
                // Simulate processing
                cout << "Worker " << workerId << " processing task [" << task.id 
                     << "]: " << task.description << " (Priority: " << task.priority 
                     << ", Time: " << task.processingTime.count() << "s)" << endl;
                
                this_thread::sleep_for(task.processingTime);
                
                // Mark as completed
                completeTask(task.id);
            } else {
                // No more tasks
                break;
            }
        }
        
        cout << "Worker " << workerId << " finished" << endl;
    }
    
    // Generate random tasks
    void generateRandomTasks(int count) {
        for (int i = 1; i <= count; i++) {
            int priority = processingTimeDist(rng);
            chrono::seconds time(processingTimeDist(rng));
            
            Task task(
                "TASK-" + to_string(i),
                "Process data batch " + to_string(i),
                priority,
                time
            );
            
            addTask(task);
            
            // Small delay between task generation
            this_thread::sleep_for(chrono::milliseconds(100));
        }
    }
    
    // Print status
    void printStatus() {
        lock_guard<mutex> lock(mtx);
        
        cout << "\nTask Coordinator Status:" << endl;
        cout << "------------------------" << endl;
        cout << "Tasks in queue: " << taskQueue.size() << endl;
        cout << "Tasks being processed: " << processingTasks.size() << endl;
        cout << "Tasks completed: " << completedTasks.size() << endl;
        cout << "------------------------" << endl;
    }
};

// Demonstrate distributed task coordination
void distributedTaskDemo() {
    TaskCoordinator coordinator;
    
    // Start worker threads
    vector<thread> workers;
    int numWorkers = 3;
    
    for (int i = 1; i <= numWorkers; i++) {
        workers.emplace_back([&coordinator, i]() {
            coordinator.workerFunction(i);
        });
    }
    
    // Generate tasks
    thread taskGenerator([&coordinator]() {
        coordinator.generateRandomTasks(10);
    });
    
    // Join the task generator
    taskGenerator.join();
    
    // Wait a bit to allow processing
    this_thread::sleep_for(chrono::seconds(2));
    coordinator.printStatus();
    
    this_thread::sleep_for(chrono::seconds(10));
    coordinator.printStatus();
    
    // Join all workers
    for (auto& worker : workers) {
        worker.join();
    }
    
    // Final status
    coordinator.printStatus();
}
```

**Key System Design Aspects**:
- **Work Distribution**: Tasks are distributed among workers efficiently.
- **Priority-Based Processing**: Critical tasks are processed first.
- **Coordination**: Workers coordinate through a central queue without direct communication.
- **Status Tracking**: The coordinator maintains the state of all tasks in the system.

## 7.3 Learning Resources and Next Steps

As you continue your journey with stacks and queues, here are some resources and next steps to deepen your understanding.

### Advanced Data Structures to Explore

Now that you've mastered stacks and queues, consider exploring these related data structures:

1. **Deques (Double-Ended Queues)**: Generalization of stacks and queues that allows insertion and deletion at both ends.

2. **Priority Queues**: Queue where elements have priority values and higher priority elements are served first.

3. **Treaps**: Combination of binary search trees and heaps.

4. **Skip Lists**: Probabilistic data structure that allows O(log n) search complexity like balanced trees but with simpler implementation.

5. **Bloom Filters**: Space-efficient probabilistic data structure for membership testing.

Here's a sample implementation of a skip list, which combines ideas from linked lists and trees:

```cpp
#include <iostream>
#include <vector>
#include <random>
#include <cstdlib>
#include <ctime>
using namespace std;

// Skip list implementation
template <typename K, typename V>
class SkipList {
private:
    struct Node {
        K key;
        V value;
        vector<Node*> forward;
        
        Node(K k, V v, int level) : key(k), value(v), forward(level, nullptr) {}
    };
    
    Node* head;
    int maxLevel;
    float probability;
    int currentLevel;
    mt19937 rng;
    uniform_real_distribution<float> dist;
    
    int randomLevel() {
        int level = 1;
        while (dist(rng) < probability && level < maxLevel) {
            level++;
        }
        return level;
    }
    
public:
    SkipList(int maxLevel = 16, float prob = 0.5) 
        : maxLevel(maxLevel), probability(prob), currentLevel(1), 
          rng(random_device()()), dist(0.0, 1.0) {
        
        K k{};
        V v{};
        head = new Node(k, v, maxLevel);
    }
    
    ~SkipList() {
        Node* current = head;
        while (current) {
            Node* next = current->forward[0];
            delete current;
            current = next;
        }
    }
    
    void insert(K key, V value) {
        vector<Node*> update(maxLevel, nullptr);
        Node* current = head;
        
        // Find position to insert
        for (int i = currentLevel - 1; i >= 0; i--) {
            while (current->forward[i] && current->forward[i]->key < key) {
                current = current->forward[i];
            }
            update[i] = current;
        }
        
        current = current->forward[0];
        
        // If key already exists, update value
        if (current && current->key == key) {
            current->value = value;
            return;
        }
        
        // Generate random level for new node
        int newLevel = randomLevel();
        
        // If new level is higher than current, update head pointers
        if (newLevel > currentLevel) {
            for (int i = currentLevel; i < newLevel; i++) {
                update[i] = head;
            }
            currentLevel = newLevel;
        }
        
        // Create new node
        Node* newNode = new Node(key, value, newLevel);
        
        // Insert node by updating pointers
        for (int i = 0; i < newLevel; i++) {
            newNode->forward[i] = update[i]->forward[i];
            update[i]->forward[i] = newNode;
        }
    }
    
    bool search(K key, V& value) {
        Node* current = head;
        
        // Start from highest level and work down
        for (int i = currentLevel - 1; i >= 0; i--) {
            while (current->forward[i] && current->forward[i]->key < key) {
                current = current->forward[i];
            }
        }
        
        current = current->forward[0];
        
        // Check if key exists
        if (current && current->key == key) {
            value = current->value;
            return true;
        }
        
        return false;
    }
    
    bool remove(K key) {
        vector<Node*> update(maxLevel, nullptr);
        Node* current = head;
        
        // Find position to remove
        for (int i = currentLevel - 1; i >= 0; i--) {
            while (current->forward[i] && current->forward[i]->key < key) {
                current = current->forward[i];
            }
            update[i] = current;
        }
        
        current = current->forward[0];
        
        // Key not found
        if (!current || current->key != key) {
            return false;
        }
        
        // Update pointers
        for (int i = 0; i < currentLevel; i++) {
            if (update[i]->forward[i] != current) {
                break;
            }
            update[i]->forward[i] = current->forward[i];
        }
        
        // Delete node
        delete current;
        
        // Update current level if necessary
        while (currentLevel > 1 && head->forward[currentLevel - 1] == nullptr) {
            currentLevel--;
        }
        
        return true;
    }
    
    void printList() {
        cout << "Skip List:" << endl;
        for (int i = currentLevel - 1; i >= 0; i--) {
            Node* node = head->forward[i];
            cout << "Level " << i << ": ";
            while (node) {
                cout << "(" << node->key << "," << node->value << ") ";
                node = node->forward[i];
            }
            cout << endl;
        }
    }
};

// Demonstrate skip list
void skipListDemo() {
    SkipList<int, string> skipList;
    
    skipList.insert(3, "three");
    skipList.insert(7, "seven");
    skipList.insert(8, "eight");
    skipList.insert(1, "one");
    skipList.insert(10, "ten");
    
    skipList.printList();
    
    string value;
    if (skipList.search(7, value)) {
        cout << "Found key 7 with value: " << value << endl;
    } else {
        cout << "Key 7 not found" << endl;
    }
    
    if (skipList.search(5, value)) {
        cout << "Found key 5 with value: " << value << endl;
    } else {
        cout << "Key 5 not found" << endl;
    }
    
    skipList.remove(7);
    cout << "After removing key 7:" << endl;
    skipList.printList();
}
```

### Recommended Books and Courses

1. **Books**:
   - "Algorithms" by Robert Sedgewick and Kevin Wayne
   - "Introduction to Algorithms" by Cormen, Leiserson, Rivest, and Stein
   - "Elements of Programming Interviews" for practical interview problems
   - "Designing Data-Intensive Applications" by Martin Kleppmann for system design aspects

2. **Online Courses**:
   - Stanford's "Algorithms: Design and Analysis" on Coursera
   - Princeton's "Algorithms" on Coursera
   - MIT's "Introduction to Algorithms" on OpenCourseWare

3. **Websites**:
   - LeetCode for practicing coding problems
   - GeeksforGeeks for conceptual understanding
   - HackerRank for competitive programming

### Future Research Directions

If you're interested in contributing to the field, here are some promising research areas:

1. **Quantum Computing Data Structures**: How do stacks and queues translate to quantum computing paradigms?

2. **Machine Learning-Enhanced Data Structures**: Can ML techniques optimize the performance of traditional data structures?

3. **Bio-Inspired Data Structures**: Drawing inspiration from biological systems for more efficient data organization.

4. **Energy-Efficient Data Structures**: Designing structures that minimize energy consumption in battery-powered devices.

5. **Self-Adapting Data Structures**: Structures that automatically adjust their behavior based on usage patterns.

## Summary

In this final module, we've explored:

1. **Recent Research Developments**:
   - Lock-free and wait-free data structures
   - Cache-efficient implementations
   - Persistent data structures for non-volatile memory

2. **Advanced System Design Applications**:
   - Microservices and message queues
   - Stream processing and event sourcing
   - Distributed system coordination

3. **Learning Resources and Next Steps**:
   - Advanced data structures to explore
   - Recommended books and courses
   - Future research directions

Throughout this comprehensive tutorial series, we've journeyed from the basic concepts of stacks and queues to their most advanced applications and cutting-edge developments. These fundamental data structures remain at the heart of computer science and software engineering, continually evolving to meet new challenges in distributed systems, concurrent programming, and emerging computing paradigms.

Remember that mastering these concepts is an ongoing process. Continue to practice implementing these data structures, solve problems that utilize them, and explore their applications in real-world systems. The depth of understanding you develop will serve you well throughout your career in computer science and software engineering.