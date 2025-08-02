# 🔹 Block 6: Advanced Queue Applications – Real-World Problem Solving

## 6.0 Block Overview and Learning Objectives

### 6.0.1 Introduction to Block 6
Block 6 explores advanced applications of queue data structures in solving complex real-world problems. This module covers sophisticated algorithms using queues in graph traversal, caching systems, matrix problems, and tree-based algorithms with practical implementations.

### 6.0.2 Learning Objectives
By the end of Block 6, students will be able to:
- Implement LRU Cache using queue and hashing techniques
- Solve multi-source BFS problems like Rotten Oranges
- Apply topological sorting with queue-based algorithms
- Design efficient graph traversal algorithms using queues
- Implement level-order tree traversal with advanced features
- Optimize queue-based algorithms for real-world applications

### 6.0.3 Prerequisites
- Strong understanding of queue data structures from previous blocks
- Knowledge of graph theory and basic graph algorithms
- Familiarity with tree data structures and traversal methods
- Understanding of hashing and time complexity analysis

### 6.0.4 Block Structure Overview
```
Block 6 Timeline:
Day 1: LRU Cache using Queue + Hashing
Day 2: Rotten Oranges (Matrix + BFS + Queue)
Day 3: Course Schedule (Topological Sort with Queue)
Day 4: Walls and Gates (Multi-source BFS)
Day 5: Time to Inform All Employees (Tree + Queue)
Day 6: Binary Tree Level Order Traversal (Queue)
Day 7: Comprehensive Revision & Advanced Applications
```

---

## 6.1 LRU Cache using Queue + Hashing – Memory Management System

### 6.1.1 Theoretical Foundation and System Design

#### 6.1.1.1 LRU Cache Concept and Architecture
**Least Recently Used (LRU) Cache** is a memory management technique that evicts the least recently used item when the cache reaches its capacity. This strategy is based on the principle of temporal locality - recently accessed items are more likely to be accessed again.

#### 6.1.1.2 System Requirements and Design Constraints
```cpp
// 6.1.1.2.1 LRU Cache System Specification
/*
LRU Cache Requirements:
1. get(key): Retrieve value and mark as recently used - O(1)
2. put(key, value): Insert/update and mark as recently used - O(1)
3. Capacity management: Remove LRU item when full
4. Thread-safety considerations for production systems
5. Memory efficiency and minimal overhead

Design Constraints:
- Fixed capacity specified at initialization
- All operations must be O(1) average time complexity
- Support for generic key-value types
- Efficient memory usage with minimal fragmentation
*/
```

#### 6.1.1.3 Architecture Components and Data Structures

```cpp
#include <iostream>
#include <unordered_map>
#include <list>
#include <chrono>
#include <iomanip>
#include <thread>
#include <mutex>
using namespace std;

// 6.1.1.3.1 Advanced LRU Cache Implementation
template<typename Key, typename Value>
class AdvancedLRUCache {
private:
    // Node structure for doubly linked list
    struct CacheNode {
        Key key;
        Value value;
        chrono::time_point<chrono::steady_clock> accessTime;
        chrono::time_point<chrono::steady_clock> insertTime;
        int accessCount;
        
        CacheNode(const Key& k, const Value& v) 
            : key(k), value(v), accessCount(1) {
            auto now = chrono::steady_clock::now();
            accessTime = insertTime = now;
        }
        
        void updateAccess() {
            accessTime = chrono::steady_clock::now();
            accessCount++;
        }
        
        void displayInfo() const {
            auto now = chrono::steady_clock::now();
            auto ageMs = chrono::duration_cast<chrono::milliseconds>(now - insertTime).count();
            auto lastAccessMs = chrono::duration_cast<chrono::milliseconds>(now - accessTime).count();
            
            cout << "Key: " << key << ", Value: " << value 
                 << ", Age: " << ageMs << "ms"
                 << ", Last Access: " << lastAccessMs << "ms ago"
                 << ", Access Count: " << accessCount << endl;
        }
    };
    
    using CacheList = list<CacheNode>;
    using CacheIterator = typename CacheList::iterator;
    
    int capacity;
    CacheList cacheList;  // Front = most recent, Back = least recent
    unordered_map<Key, CacheIterator> cacheMap;  // Key -> Iterator mapping
    
    // Statistics and monitoring
    mutable int hitCount;
    mutable int missCount;
    mutable int evictionCount;
    mutable chrono::time_point<chrono::steady_clock> creationTime;
    
public:
    // 6.1.1.3.2 Constructor with detailed initialization
    explicit AdvancedLRUCache(int cap) : capacity(cap), hitCount(0), missCount(0), evictionCount(0) {
        if (cap <= 0) {
            throw invalid_argument("Cache capacity must be positive");
        }
        
        creationTime = chrono::steady_clock::now();
        
        cout << "Advanced LRU Cache created with capacity: " << capacity << endl;
        cout << "Creation time: " << getCurrentTimeString() << endl;
    }
    
    // 6.1.1.3.3 Get operation with comprehensive tracking
    pair<bool, Value> get(const Key& key) {
        cout << "\n=== GET Operation ===" << endl;
        cout << "Requesting key: " << key << endl;
        
        auto it = cacheMap.find(key);
        
        if (it != cacheMap.end()) {
            // Cache hit
            hitCount++;
            
            // Move to front (most recently used)
            auto listIt = it->second;
            listIt->updateAccess();
            
            cout << "Cache HIT! Moving to front." << endl;
            listIt->displayInfo();
            
            cacheList.splice(cacheList.begin(), cacheList, listIt);
            
            displayCacheState("After GET (HIT)");
            
            return {true, listIt->value};
        } else {
            // Cache miss
            missCount++;
            cout << "Cache MISS! Key not found." << endl;
            
            displayCacheState("After GET (MISS)");
            
            return {false, Value{}};
        }
    }
    
    // 6.1.1.3.4 Put operation with eviction handling
    void put(const Key& key, const Value& value) {
        cout << "\n=== PUT Operation ===" << endl;
        cout << "Inserting key: " << key << ", value: " << value << endl;
        
        auto it = cacheMap.find(key);
        
        if (it != cacheMap.end()) {
            // Update existing key
            cout << "Updating existing key" << endl;
            
            auto listIt = it->second;
            listIt->value = value;
            listIt->updateAccess();
            
            // Move to front
            cacheList.splice(cacheList.begin(), cacheList, listIt);
            
            cout << "Updated and moved to front:" << endl;
            listIt->displayInfo();
        } else {
            // Insert new key
            cout << "Inserting new key" << endl;
            
            if (cacheList.size() >= capacity) {
                // Need to evict LRU item
                evictLRU();
            }
            
            // Add new item to front
            cacheList.emplace_front(key, value);
            cacheMap[key] = cacheList.begin();
            
            cout << "Inserted new item:" << endl;
            cacheList.begin()->displayInfo();
        }
        
        displayCacheState("After PUT");
    }
    
    // 6.1.1.3.5 LRU eviction with detailed logging
    void evictLRU() {
        if (cacheList.empty()) return;
        
        cout << "\n--- LRU Eviction Process ---" << endl;
        
        auto lruNode = cacheList.back();
        cout << "Evicting LRU item:" << endl;
        lruNode.displayInfo();
        
        // Remove from map and list
        cacheMap.erase(lruNode.key);
        cacheList.pop_back();
        
        evictionCount++;
        cout << "Eviction completed. Total evictions: " << evictionCount << endl;
    }
    
    // 6.1.1.3.6 Comprehensive cache state display
    void displayCacheState(const string& operation = "") const {
        cout << "\n--- Cache State";
        if (!operation.empty()) {
            cout << " (" << operation << ")";
        }
        cout << " ---" << endl;
        
        cout << "Capacity: " << capacity << "/" << cacheList.size() << " used" << endl;
        cout << "Hit rate: " << fixed << setprecision(2) 
             << getHitRate() * 100 << "%" << endl;
        
        if (cacheList.empty()) {
            cout << "Cache is empty" << endl;
        } else {
            cout << "Cache contents (most to least recent):" << endl;
            int position = 0;
            for (const auto& node : cacheList) {
                cout << "  " << position++ << ". ";
                node.displayInfo();
            }
        }
        
        cout << "Statistics: Hits=" << hitCount << ", Misses=" << missCount 
             << ", Evictions=" << evictionCount << endl;
        cout << string(50, '-') << endl;
    }
    
    // 6.1.1.3.7 Performance and analytics methods
    double getHitRate() const {
        int total = hitCount + missCount;
        return total > 0 ? (double)hitCount / total : 0.0;
    }
    
    void displayAnalytics() const {
        cout << "\n=== Cache Analytics ===" << endl;
        
        auto now = chrono::steady_clock::now();
        auto uptimeMs = chrono::duration_cast<chrono::milliseconds>(now - creationTime).count();
        
        cout << "Uptime: " << uptimeMs << " ms" << endl;
        cout << "Total requests: " << (hitCount + missCount) << endl;
        cout << "Cache hits: " << hitCount << endl;
        cout << "Cache misses: " << missCount << endl;
        cout << "Hit rate: " << fixed << setprecision(2) << getHitRate() * 100 << "%" << endl;
        cout << "Evictions: " << evictionCount << endl;
        cout << "Current utilization: " << cacheList.size() << "/" << capacity 
             << " (" << (double)cacheList.size() / capacity * 100 << "%)" << endl;
        
        if (!cacheList.empty()) {
            // Analyze access patterns
            int totalAccesses = 0;
            auto oldestAccess = chrono::steady_clock::now();
            auto newestAccess = chrono::time_point<chrono::steady_clock>::min();
            
            for (const auto& node : cacheList) {
                totalAccesses += node.accessCount;
                oldestAccess = min(oldestAccess, node.accessTime);
                newestAccess = max(newestAccess, node.accessTime);
            }
            
            cout << "Average accesses per item: " << (double)totalAccesses / cacheList.size() << endl;
            
            auto accessSpanMs = chrono::duration_cast<chrono::milliseconds>(newestAccess - oldestAccess).count();
            cout << "Access time span: " << accessSpanMs << " ms" << endl;
        }
    }
    
    // 6.1.1.3.8 Utility methods
    bool empty() const { return cacheList.empty(); }
    int size() const { return cacheList.size(); }
    int getCapacity() const { return capacity; }
    
    void clear() {
        cout << "Clearing cache..." << endl;
        cacheList.clear();
        cacheMap.clear();
        hitCount = missCount = evictionCount = 0;
        cout << "Cache cleared" << endl;
    }
    
private:
    string getCurrentTimeString() const {
        auto now = chrono::system_clock::now();
        auto time_t = chrono::system_clock::to_time_t(now);
        auto ms = chrono::duration_cast<chrono::milliseconds>(
            now.time_since_epoch()) % 1000;
        
        stringstream ss;
        ss << put_time(gmtime(&time_t), "%Y-%m-%d %H:%M:%S");
        ss << '.' << setfill('0') << setw(3) << ms.count();
        return ss.str();
    }
};
```

### 6.1.2 Production-Ready LRU Cache Implementation

#### 6.1.2.1 Thread-Safe LRU Cache

```cpp
// 6.1.2.1.1 Thread-safe LRU Cache for production use
template<typename Key, typename Value>
class ThreadSafeLRUCache {
private:
    struct Node {
        Key key;
        Value value;
        shared_ptr<Node> prev, next;
        
        Node(const Key& k, const Value& v) : key(k), value(v) {}
    };
    
    using NodePtr = shared_ptr<Node>;
    
    int capacity;
    unordered_map<Key, NodePtr> cache;
    NodePtr head, tail;  // Dummy nodes
    mutable shared_mutex cacheMutex;  // Allows multiple readers
    
    // Statistics (atomic for thread safety)
    mutable atomic<long long> hitCount{0};
    mutable atomic<long long> missCount{0};
    mutable atomic<long long> evictionCount{0};
    
public:
    // 6.1.2.1.2 Thread-safe constructor
    explicit ThreadSafeLRUCache(int cap) : capacity(cap) {
        if (cap <= 0) {
            throw invalid_argument("Capacity must be positive");
        }
        
        // Initialize dummy nodes
        head = make_shared<Node>(Key{}, Value{});
        tail = make_shared<Node>(Key{}, Value{});
        head->next = tail;
        tail->prev = head;
        
        cout << "Thread-safe LRU Cache created with capacity: " << capacity << endl;
    }
    
    // 6.1.2.1.3 Thread-safe get operation
    pair<bool, Value> get(const Key& key) {
        shared_lock<shared_mutex> lock(cacheMutex);  // Read lock
        
        auto it = cache.find(key);
        if (it != cache.end()) {
            hitCount++;
            
            // Move to front (requires write access)
            lock.unlock();
            unique_lock<shared_mutex> writeLock(cacheMutex);
            
            moveToHead(it->second);
            return {true, it->second->value};
        } else {
            missCount++;
            return {false, Value{}};
        }
    }
    
    // 6.1.2.1.4 Thread-safe put operation
    void put(const Key& key, const Value& value) {
        unique_lock<shared_mutex> lock(cacheMutex);  // Write lock
        
        auto it = cache.find(key);
        if (it != cache.end()) {
            // Update existing
            it->second->value = value;
            moveToHead(it->second);
        } else {
            // Add new
            if (cache.size() >= capacity) {
                evictTail();
            }
            
            NodePtr newNode = make_shared<Node>(key, value);
            cache[key] = newNode;
            addToHead(newNode);
        }
    }
    
    // 6.1.2.1.5 Thread-safe analytics
    void displayThreadSafeAnalytics() const {
        shared_lock<shared_mutex> lock(cacheMutex);
        
        cout << "\n=== Thread-Safe Cache Analytics ===" << endl;
        cout << "Capacity: " << capacity << endl;
        cout << "Current size: " << cache.size() << endl;
        cout << "Hit count: " << hitCount.load() << endl;
        cout << "Miss count: " << missCount.load() << endl;
        cout << "Eviction count: " << evictionCount.load() << endl;
        
        long long total = hitCount.load() + missCount.load();
        if (total > 0) {
            cout << "Hit rate: " << fixed << setprecision(2) 
                 << (double)hitCount.load() / total * 100 << "%" << endl;
        }
    }
    
private:
    void moveToHead(NodePtr node) {
        removeNode(node);
        addToHead(node);
    }
    
    void addToHead(NodePtr node) {
        node->prev = head;
        node->next = head->next;
        head->next->prev = node;
        head->next = node;
    }
    
    void removeNode(NodePtr node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }
    
    void evictTail() {
        NodePtr last = tail->prev;
        removeNode(last);
        cache.erase(last->key);
        evictionCount++;
    }
};

// 6.1.2.2 Memory-Optimized LRU Cache
template<typename Key, typename Value>
class MemoryOptimizedLRUCache {
private:
    struct CompactNode {
        Key key;
        Value value;
        int prev, next;  // Use indices instead of pointers
        
        CompactNode() : prev(-1), next(-1) {}
        CompactNode(const Key& k, const Value& v) : key(k), value(v), prev(-1), next(-1) {}
    };
    
    vector<CompactNode> nodes;
    unordered_map<Key, int> keyToIndex;
    vector<int> freeList;  // Track free slots for reuse
    
    int capacity;
    int head, tail;  // Indices of dummy nodes
    int currentSize;
    
public:
    // 6.1.2.2.1 Memory-optimized constructor
    explicit MemoryOptimizedLRUCache(int cap) : capacity(cap), currentSize(0) {
        // Pre-allocate memory
        nodes.reserve(cap + 2);  // +2 for dummy nodes
        
        // Initialize dummy nodes
        nodes.emplace_back();  // head (index 0)
        nodes.emplace_back();  // tail (index 1)
        
        head = 0;
        tail = 1;
        nodes[head].next = tail;
        nodes[tail].prev = head;
        
        cout << "Memory-optimized LRU Cache created" << endl;
        cout << "Pre-allocated memory for " << (cap + 2) << " nodes" << endl;
        cout << "Memory usage: " << sizeof(CompactNode) * (cap + 2) << " bytes" << endl;
    }
    
    // 6.1.2.2.2 Get with memory tracking
    pair<bool, Value> get(const Key& key) {
        auto it = keyToIndex.find(key);
        if (it != keyToIndex.end()) {
            int nodeIndex = it->second;
            moveToHead(nodeIndex);
            return {true, nodes[nodeIndex].value};
        }
        return {false, Value{}};
    }
    
    // 6.1.2.2.3 Put with memory reuse
    void put(const Key& key, const Value& value) {
        auto it = keyToIndex.find(key);
        if (it != keyToIndex.end()) {
            int nodeIndex = it->second;
            nodes[nodeIndex].value = value;
            moveToHead(nodeIndex);
        } else {
            if (currentSize >= capacity) {
                evictTail();
            }
            
            int newIndex = allocateNode(key, value);
            keyToIndex[key] = newIndex;
            addToHead(newIndex);
            currentSize++;
        }
    }
    
    // 6.1.2.2.4 Memory usage analysis
    void analyzeMemoryUsage() const {
        cout << "\n=== Memory Usage Analysis ===" << endl;
        
        size_t nodeMemory = sizeof(CompactNode) * nodes.capacity();
        size_t mapMemory = keyToIndex.size() * (sizeof(Key) + sizeof(int));
        size_t freeListMemory = freeList.capacity() * sizeof(int);
        
        cout << "Node array memory: " << nodeMemory << " bytes" << endl;
        cout << "Hash map memory: " << mapMemory << " bytes" << endl;
        cout << "Free list memory: " << freeListMemory << " bytes" << endl;
        cout << "Total memory: " << (nodeMemory + mapMemory + freeListMemory) << " bytes" << endl;
        
        cout << "Memory efficiency: " << fixed << setprecision(1)
             << (double)currentSize / capacity * 100 << "%" << endl;
        cout << "Free slots available: " << freeList.size() << endl;
    }
    
private:
    int allocateNode(const Key& key, const Value& value) {
        if (!freeList.empty()) {
            int index = freeList.back();
            freeList.pop_back();
            nodes[index] = CompactNode(key, value);
            return index;
        } else {
            nodes.emplace_back(key, value);
            return nodes.size() - 1;
        }
    }
    
    void deallocateNode(int index) {
        freeList.push_back(index);
        nodes[index] = CompactNode();  // Clear data
    }
    
    void moveToHead(int nodeIndex) {
        removeNode(nodeIndex);
        addToHead(nodeIndex);
    }
    
    void addToHead(int nodeIndex) {
        nodes[nodeIndex].prev = head;
        nodes[nodeIndex].next = nodes[head].next;
        nodes[nodes[head].next].prev = nodeIndex;
        nodes[head].next = nodeIndex;
    }
    
    void removeNode(int nodeIndex) {
        nodes[nodes[nodeIndex].prev].next = nodes[nodeIndex].next;
        nodes[nodes[nodeIndex].next].prev = nodes[nodeIndex].prev;
    }
    
    void evictTail() {
        int lastIndex = nodes[tail].prev;
        removeNode(lastIndex);
        
        keyToIndex.erase(nodes[lastIndex].key);
        deallocateNode(lastIndex);
        currentSize--;
    }
};
```

### 6.1.3 LRU Cache Testing and Benchmarking

#### 6.1.3.1 Comprehensive Testing Framework

```cpp
// 6.1.3.1.1 LRU Cache testing and benchmarking
class LRUCacheTestSuite {
public:
    // 6.1.3.1.2 Basic functionality tests
    void testBasicOperations() {
        cout << "=== LRU Cache Basic Operations Test ===" << endl;
        
        AdvancedLRUCache<int, string> cache(3);
        
        // Test 1: Basic put and get
        cout << "\nTest 1: Basic put and get operations" << endl;
        cache.put(1, "one");
        cache.put(2, "two");
        cache.put(3, "three");
        
        auto [found1, value1] = cache.get(1);
        assert(found1 && value1 == "one");
        cout << "✓ Get existing key passed" << endl;
        
        auto [found4, value4] = cache.get(4);
        assert(!found4);
        cout << "✓ Get non-existing key passed" << endl;
        
        // Test 2: LRU eviction
        cout << "\nTest 2: LRU eviction test" << endl;
        cache.put(4, "four");  // Should evict key 2 (LRU)
        
        auto [found2, value2] = cache.get(2);
        assert(!found2);
        cout << "✓ LRU eviction passed" << endl;
        
        // Test 3: Update existing key
        cout << "\nTest 3: Update existing key" << endl;
        cache.put(1, "ONE");  // Update key 1
        auto [found1_new, value1_new] = cache.get(1);
        assert(found1_new && value1_new == "ONE");
        cout << "✓ Update existing key passed" << endl;
        
        cache.displayAnalytics();
    }
    
    // 6.1.3.1.3 Performance benchmarking
    void benchmarkPerformance() {
        cout << "\n=== LRU Cache Performance Benchmark ===" << endl;
        
        const int CACHE_SIZE = 1000;
        const int NUM_OPERATIONS = 100000;
        
        AdvancedLRUCache<int, int> cache(CACHE_SIZE);
        
        // Benchmark put operations
        auto start = chrono::high_resolution_clock::now();
        
        for (int i = 0; i < NUM_OPERATIONS; i++) {
            cache.put(i % (CACHE_SIZE * 2), i);  // 50% cache hit rate
        }
        
        auto putEnd = chrono::high_resolution_clock::now();
        
        // Benchmark get operations
        for (int i = 0; i < NUM_OPERATIONS; i++) {
            cache.get(i % (CACHE_SIZE * 2));
        }
        
        auto getEnd = chrono::high_resolution_clock::now();
        
        // Calculate performance metrics
        auto putDuration = chrono::duration_cast<chrono::microseconds>(putEnd - start);
        auto getDuration = chrono::duration_cast<chrono::microseconds>(getEnd - putEnd);
        
        cout << "Performance Results:" << endl;
        cout << "Put operations: " << NUM_OPERATIONS << " in " << putDuration.count() << " μs" << endl;
        cout << "Average put time: " << (double)putDuration.count() / NUM_OPERATIONS << " μs" << endl;
        cout << "Get operations: " << NUM_OPERATIONS << " in " << getDuration.count() << " μs" << endl;
        cout << "Average get time: " << (double)getDuration.count() / NUM_OPERATIONS << " μs" << endl;
        
        cache.displayAnalytics();
    }
    
    // 6.1.3.1.4 Multi-threaded stress test
    void stressTestThreadSafety() {
        cout << "\n=== Thread Safety Stress Test ===" << endl;
        
        ThreadSafeLRUCache<int, string> cache(100);
        const int NUM_THREADS = 4;
        const int OPERATIONS_PER_THREAD = 10000;
        
        vector<thread> threads;
        atomic<int> completedOperations{0};
        
        auto worker = [&cache, &completedOperations, OPERATIONS_PER_THREAD](int threadId) {
            for (int i = 0; i < OPERATIONS_PER_THREAD; i++) {
                int key = (threadId * OPERATIONS_PER_THREAD + i) % 200;
                string value = "thread" + to_string(threadId) + "_value" + to_string(i);
                
                // Mix of operations
                if (i % 3 == 0) {
                    cache.put(key, value);
                } else {
                    cache.get(key);
                }
                
                completedOperations++;
            }
        };
        
        auto start = chrono::high_resolution_clock::now();
        
        // Launch threads
        for (int i = 0; i < NUM_THREADS; i++) {
            threads.emplace_back(worker, i);
        }
        
        // Wait for completion
        for (auto& t : threads) {
            t.join();
        }
        
        auto end = chrono::high_resolution_clock::now();
        auto duration = chrono::duration_cast<chrono::milliseconds>(end - start);
        
        cout << "Stress test completed!" << endl;
        cout << "Total operations: " << completedOperations.load() << endl;
        cout << "Time taken: " << duration.count() << " ms" << endl;
        cout << "Operations per second: " << (completedOperations.load() * 1000.0) / duration.count() << endl;
        
        cache.displayThreadSafeAnalytics();
    }
    
    // 6.1.3.1.5 Memory efficiency comparison
    void compareMemoryEfficiency() {
        cout << "\n=== Memory Efficiency Comparison ===" << endl;
        
        const int CACHE_SIZE = 1000;
        
        // Test standard LRU cache
        cout << "\nStandard LRU Cache:" << endl;
        {
            AdvancedLRUCache<int, string> standardCache(CACHE_SIZE);
            for (int i = 0; i < CACHE_SIZE; i++) {
                standardCache.put(i, "value" + to_string(i));
            }
            standardCache.displayAnalytics();
        }
        
        // Test memory-optimized cache
        cout << "\nMemory-Optimized LRU Cache:" << endl;
        {
            MemoryOptimizedLRUCache<int, string> optimizedCache(CACHE_SIZE);
            for (int i = 0; i < CACHE_SIZE; i++) {
                optimizedCache.put(i, "value" + to_string(i));
            }
            optimizedCache.analyzeMemoryUsage();
        }
        
        cout << "\nComparison Results:" << endl;
        cout << "- Standard cache uses linked list with pointers" << endl;
        cout << "- Optimized cache uses index-based approach" << endl;
        cout << "- Memory savings: ~30-50% depending on pointer size" << endl;
        cout << "- Cache performance: Similar O(1) operations" << endl;
    }
    
    // 6.1.3.1.6 Real-world usage patterns
    void simulateRealWorldUsage() {
        cout << "\n=== Real-World Usage Simulation ===" << endl;
        
        AdvancedLRUCache<string, string> webCache(50);
        
        // Simulate web page caching
        vector<string> pages = {
            "/home", "/about", "/products", "/contact", "/blog",
            "/login", "/dashboard", "/profile", "/settings", "/help"
        };
        
        cout << "Simulating web page access patterns..." << endl;
        
        // Simulate realistic access patterns
        random_device rd;
        mt19937 gen(rd());
        uniform_int_distribution<> dis(0, pages.size() - 1);
        
        for (int i = 0; i < 1000; i++) {
            string page = pages[dis(gen)];
            string content = "Content for " + page + " (request " + to_string(i) + ")";
            
            // 70% chance of accessing existing page, 30% new content
            if (i % 10 < 7) {
                auto [found, cachedContent] = webCache.get(page);
                if (!found) {
                    webCache.put(page, content);
                }
            } else {
                webCache.put(page + "_v" + to_string(i), content);
            }
        }
        
        cout << "Web cache simulation completed" << endl;
        webCache.displayAnalytics();
    }
    
    // 6.1.3.1.7 Run all tests
    void runAllTests() {
        cout << "Starting comprehensive LRU Cache test suite..." << endl;
        cout << "Current time: " << getCurrentTimeString() << endl;
        cout << string(60, '=') << endl;
        
        testBasicOperations();
        benchmarkPerformance();
        stressTestThreadSafety();
        compareMemoryEfficiency();
        simulateRealWorldUsage();
        
        cout << string(60, '=') << endl;
        cout << "All tests completed successfully!" << endl;
    }
    
private:
    string getCurrentTimeString() const {
        auto now = chrono::system_clock::now();
        auto time_t = chrono::system_clock::to_time_t(now);
        stringstream ss;
        ss << put_time(gmtime(&time_t), "%Y-%m-%d %H:%M:%S");
        return ss.str();
    }
};
```

---

## 6.2 Rotten Oranges – Multi-Source BFS Matrix Problem

### 6.2.1 Problem Definition and Analysis

#### 6.2.1.1 Problem Statement and Real-World Context
The Rotten Oranges problem simulates the spread of contamination in a grid. Given a 2D grid representing oranges where:
- 0 = empty cell
- 1 = fresh orange  
- 2 = rotten orange

Each minute, fresh oranges adjacent (4-directionally) to rotten oranges become rotten. Determine the minimum time for all oranges to rot, or return -1 if impossible.

#### 6.2.1.2 Mathematical Model and Algorithm Analysis

```cpp
#include <iostream>
#include <vector>
#include <queue>
#include <iomanip>
#include <chrono>
using namespace std;

// 6.2.1.2.1 Comprehensive Rotten Oranges Solution
class RottenOrangesAnalyzer {
public:
    struct Cell {
        int row, col, time;
        
        Cell(int r, int c, int t = 0) : row(r), col(c), time(t) {}
        
        void display() const {
            cout << "(" << row << "," << col << ") at time " << time;
        }
        
        bool isValid(int rows, int cols) const {
            return row >= 0 && row < rows && col >= 0 && col < cols;
        }
    };
    
    // 6.2.1.2.2 Enhanced solution with detailed tracking
    int orangesRotting(vector<vector<int>>& grid) {
        cout << "=== Rotten Oranges Problem Analysis ===" << endl;
        
        if (grid.empty() || grid[0].empty()) {
            cout << "Empty grid provided" << endl;
            return 0;
        }
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        cout << "Grid dimensions: " << rows << " x " << cols << endl;
        displayGrid(grid, "Initial Grid");
        
        // Initialize BFS
        queue<Cell> rottenQueue;
        int freshCount = 0;
        int totalOranges = 0;
        
        // Find all initially rotten oranges and count fresh ones
        cout << "\nScanning for initial state:" << endl;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    rottenQueue.push(Cell(i, j, 0));
                    cout << "Found rotten orange at (" << i << "," << j << ")" << endl;
                } else if (grid[i][j] == 1) {
                    freshCount++;
                    totalOranges++;
                }
                
                if (grid[i][j] != 0) {
                    totalOranges++;
                }
            }
        }
        
        cout << "Initial fresh oranges: " << freshCount << endl;
        cout << "Initial rotten oranges: " << rottenQueue.size() << endl;
        cout << "Total oranges: " << totalOranges << endl;
        
        // If no fresh oranges, return 0
        if (freshCount == 0) {
            cout << "No fresh oranges to rot!" << endl;
            return 0;
        }
        
        // If no rotten oranges but fresh exist, impossible
        if (rottenQueue.empty()) {
            cout << "No rotten oranges to start infection!" << endl;
            return -1;
        }
        
        // Multi-source BFS
        return performMultiSourceBFS(grid, rottenQueue, freshCount);
    }
    
private:
    // 6.2.1.2.3 Multi-source BFS implementation
    int performMultiSourceBFS(vector<vector<int>>& grid, queue<Cell>& rottenQueue, int freshCount) {
        cout << "\n=== Multi-Source BFS Process ===" << endl;
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        // 4-directional movement
        vector<pair<int, int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        string dirNames[] = {"UP", "DOWN", "LEFT", "RIGHT"};
        
        int maxTime = 0;
        int minute = 0;
        
        while (!rottenQueue.empty() && freshCount > 0) {
            int currentLevelSize = rottenQueue.size();
            cout << "\n--- Minute " << minute << " (Processing " << currentLevelSize << " rotten oranges) ---" << endl;
            
            bool newInfections = false;
            
            // Process all oranges that became rotten at current time
            for (int i = 0; i < currentLevelSize; i++) {
                Cell current = rottenQueue.front();
                rottenQueue.pop();
                
                cout << "Processing rotten orange at ";
                current.display();
                cout << endl;
                
                // Check all 4 directions
                for (int d = 0; d < 4; d++) {
                    int newRow = current.row + directions[d].first;
                    int newCol = current.col + directions[d].second;
                    Cell neighbor(newRow, newCol, current.time + 1);
                    
                    cout << "  Checking " << dirNames[d] << " direction: (" << newRow << "," << newCol << ")";
                    
                    if (neighbor.isValid(rows, cols) && grid[newRow][newCol] == 1) {
                        // Fresh orange found - make it rotten
                        grid[newRow][newCol] = 2;
                        rottenQueue.push(neighbor);
                        freshCount--;
                        maxTime = max(maxTime, neighbor.time);
                        newInfections = true;
                        
                        cout << " → INFECTED! (Fresh count: " << freshCount << ")" << endl;
                    } else {
                        cout << " → ";
                        if (!neighbor.isValid(rows, cols)) {
                            cout << "Out of bounds";
                        } else if (grid[newRow][newCol] == 0) {
                            cout << "Empty cell";
                        } else if (grid[newRow][newCol] == 2) {
                            cout << "Already rotten";
                        }
                        cout << endl;
                    }
                }
            }
            
            if (newInfections) {
                cout << "Grid after minute " << minute << ":" << endl;
                displayGrid(grid, "After minute " + to_string(minute));
            }
            
            minute++;
        }
        
        cout << "\n=== BFS Completed ===" << endl;
        cout << "Final fresh oranges remaining: " << freshCount << endl;
        cout << "Maximum time reached: " << maxTime << endl;
        
        // Check if all fresh oranges were infected
        if (freshCount > 0) {
            cout << "❌ Some fresh oranges remain unreachable!" << endl;
            return -1;
        } else {
            cout << "✅ All oranges successfully rotten!" << endl;
            return maxTime;
        }
    }
    
    // 6.2.1.2.4 Grid visualization
    void displayGrid(const vector<vector<int>>& grid, const string& title = "") const {
        if (!title.empty()) {
            cout << "\n" << title << ":" << endl;
        }
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        // Print column headers
        cout << "   ";
        for (int j = 0; j < cols; j++) {
            cout << setw(3) << j;
        }
        cout << endl;
        
        // Print grid with row numbers
        for (int i = 0; i < rows; i++) {
            cout << setw(2) << i << " ";
            for (int j = 0; j < cols; j++) {
                char symbol;
                switch (grid[i][j]) {
                    case 0: symbol = '.'; break;  // Empty
                    case 1: symbol = 'O'; break;  // Fresh orange
                    case 2: symbol = 'X'; break;  // Rotten orange
                    default: symbol = '?'; break;
                }
                cout << setw(3) << symbol;
            }
            cout << endl;
        }
        
        cout << "Legend: . = Empty, O = Fresh Orange, X = Rotten Orange" << endl;
    }
    
public:
    // 6.2.1.2.5 Advanced analysis with statistics
    struct AnalysisResult {
        int minTime;
        int totalCells;
        int freshOranges;
        int rottenOranges;
        int emptyCells;
        double infectionRate;
        vector<vector<int>> finalGrid;
        
        void display() const {
            cout << "\n=== Analysis Results ===" << endl;
            cout << "Minimum time to rot all oranges: " << minTime << endl;
            cout << "Total cells: " << totalCells << endl;
            cout << "Initial fresh oranges: " << freshOranges << endl;
            cout << "Initial rotten oranges: " << rottenOranges << endl;
            cout << "Empty cells: " << emptyCells << endl;
            cout << "Grid density: " << fixed << setprecision(2) 
                 << (double)(freshOranges + rottenOranges) / totalCells * 100 << "%" << endl;
            if (minTime >= 0) {
                cout << "Average infection rate: " << fixed << setprecision(2) 
                     << (double)freshOranges / (minTime == 0 ? 1 : minTime) << " oranges/minute" << endl;
            }
        }
    };
    
    AnalysisResult analyzeOrangeInfection(vector<vector<int>> grid) {
        cout << "=== Comprehensive Orange Infection Analysis ===" << endl;
        
        AnalysisResult result;
        result.totalCells = grid.size() * grid[0].size();
        result.freshOranges = 0;
        result.rottenOranges = 0;
        result.emptyCells = 0;
        
        // Count initial state
        for (const auto& row : grid) {
            for (int cell : row) {
                switch (cell) {
                    case 0: result.emptyCells++; break;
                    case 1: result.freshOranges++; break;
                    case 2: result.rottenOranges++; break;
                }
            }
        }
        
        // Perform the rot simulation
        result.minTime = orangesRotting(grid);
        result.finalGrid = grid;
        
        result.display();
        return result;
    }
};
```

### 6.2.2 Advanced Variations and Extensions

#### 6.2.2.1 Multi-Level Infection Simulation

```cpp
// 6.2.2.1.1 Advanced infection models with different spread rates
class AdvancedInfectionSimulator {
public:
    enum class InfectionType {
        STANDARD,     // Normal 4-directional spread
        DIAGONAL,     // 8-directional spread
        PROBABILISTIC,// Random infection chance
        MULTI_STRAIN  // Different rot types with different speeds
    };
    
    struct InfectionCell {
        int row, col, time;
        int strainType;  // Different strains spread at different rates
        double infectionProbability;
        
        InfectionCell(int r, int c, int t = 0, int strain = 0, double prob = 1.0) 
            : row(r), col(c), time(t), strainType(strain), infectionProbability(prob) {}
    };
    
    // 6.2.2.1.2 Probabilistic infection model
    int simulateProbabilisticInfection(vector<vector<int>>& grid, double infectionChance = 0.8) {
        cout << "=== Probabilistic Infection Simulation ===" << endl;
        cout << "Infection probability: " << infectionChance * 100 << "%" << endl;
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        queue<InfectionCell> infectionQueue;
        int freshCount = 0;
        
        // Initialize with rotten oranges
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    infectionQueue.push(InfectionCell(i, j, 0, 0, 1.0));
                } else if (grid[i][j] == 1) {
                    freshCount++;
                }
            }
        }
        
        if (freshCount == 0) return 0;
        if (infectionQueue.empty()) return -1;
        
        vector<pair<int, int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        random_device rd;
        mt19937 gen(rd());
        uniform_real_distribution<> dis(0.0, 1.0);
        
        int maxTime = 0;
        int simulationRounds = 0;
        
        while (!infectionQueue.empty() && freshCount > 0) {
            int levelSize = infectionQueue.size();
            simulationRounds++;
            
            cout << "\nSimulation round " << simulationRounds 
                 << " (Processing " << levelSize << " infection sources)" << endl;
            
            for (int i = 0; i < levelSize; i++) {
                InfectionCell current = infectionQueue.front();
                infectionQueue.pop();
                
                for (auto [dr, dc] : directions) {
                    int newRow = current.row + dr;
                    int newCol = current.col + dc;
                    
                    if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols 
                        && grid[newRow][newCol] == 1) {
                        
                        // Probabilistic infection
                        double randomValue = dis(gen);
                        if (randomValue < infectionChance) {
                            grid[newRow][newCol] = 2;
                            infectionQueue.push(InfectionCell(newRow, newCol, current.time + 1));
                            freshCount--;
                            maxTime = max(maxTime, current.time + 1);
                            
                            cout << "  Infected (" << newRow << "," << newCol 
                                 << ") with probability " << fixed << setprecision(3) 
                                 << randomValue << " < " << infectionChance << endl;
                        } else {
                            cout << "  Failed to infect (" << newRow << "," << newCol 
                                 << ") - probability " << fixed << setprecision(3) 
                                 << randomValue << " >= " << infectionChance << endl;
                        }
                    }
                }
            }
        }
        
        cout << "\nProbabilistic simulation completed" << endl;
        cout << "Remaining fresh oranges: " << freshCount << endl;
        cout << "Simulation rounds: " << simulationRounds << endl;
        
        return freshCount > 0 ? -1 : maxTime;
    }
    
    // 6.2.2.1.3 Multi-strain infection model
    int simulateMultiStrainInfection(vector<vector<int>>& grid) {
        cout << "=== Multi-Strain Infection Simulation ===" << endl;
        
        // Different strains with different spread rates
        vector<int> strainSpeeds = {1, 2, 3};  // Time units per spread
        vector<string> strainNames = {"Slow", "Medium", "Fast"};
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        queue<InfectionCell> infectionQueue;
        int freshCount = 0;
        
        // Initialize with different strain types
        int strainAssignment = 0;
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    int strain = strainAssignment % strainSpeeds.size();
                    infectionQueue.push(InfectionCell(i, j, 0, strain));
                    cout << "Initial " << strainNames[strain] << " strain at (" << i << "," << j << ")" << endl;
                    strainAssignment++;
                } else if (grid[i][j] == 1) {
                    freshCount++;
                }
            }
        }
        
        vector<pair<int, int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int maxTime = 0;
        
        while (!infectionQueue.empty() && freshCount > 0) {
            InfectionCell current = infectionQueue.front();
            infectionQueue.pop();
            
            int spreadTime = strainSpeeds[current.strainType];
            
            for (auto [dr, dc] : directions) {
                int newRow = current.row + dr;
                int newCol = current.col + dc;
                
                if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols 
                    && grid[newRow][newCol] == 1) {
                    
                    grid[newRow][newCol] = 2;
                    int newTime = current.time + spreadTime;
                    infectionQueue.push(InfectionCell(newRow, newCol, newTime, current.strainType));
                    freshCount--;
                    maxTime = max(maxTime, newTime);
                    
                    cout << "  " << strainNames[current.strainType] << " strain infected (" 
                         << newRow << "," << newCol << ") at time " << newTime << endl;
                }
            }
        }
        
        return freshCount > 0 ? -1 : maxTime;
    }
    
    // 6.2.2.1.4 Diagonal spread model
    int simulateDiagonalInfection(vector<vector<int>>& grid) {
        cout << "=== Diagonal Infection Simulation ===" << endl;
        cout << "Infection spreads in 8 directions (including diagonals)" << endl;
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        queue<InfectionCell> infectionQueue;
        int freshCount = 0;
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    infectionQueue.push(InfectionCell(i, j, 0));
                } else if (grid[i][j] == 1) {
                    freshCount++;
                }
            }
        }
        
        // 8-directional movement (including diagonals)
        vector<pair<int, int>> directions = {
            {-1, -1}, {-1, 0}, {-1, 1},
            { 0, -1},          { 0, 1},
            { 1, -1}, { 1, 0}, { 1, 1}
        };
        
        string dirNames[] = {"NW", "N", "NE", "W", "E", "SW", "S", "SE"};
        
        int maxTime = 0;
        
        while (!infectionQueue.empty() && freshCount > 0) {
            int levelSize = infectionQueue.size();
            
            for (int i = 0; i < levelSize; i++) {
                InfectionCell current = infectionQueue.front();
                infectionQueue.pop();
                
                cout << "Processing infection from (" << current.row << "," << current.col << ")" << endl;
                
                for (int d = 0; d < 8; d++) {
                    int newRow = current.row + directions[d].first;
                    int newCol = current.col + directions[d].second;
                    
                    if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols 
                        && grid[newRow][newCol] == 1) {
                        
                        grid[newRow][newCol] = 2;
                        infectionQueue.push(InfectionCell(newRow, newCol, current.time + 1));
                        freshCount--;
                        maxTime = max(maxTime, current.time + 1);
                        
                        cout << "  Infected " << dirNames[d] << " direction: (" 
                             << newRow << "," << newCol << ")" << endl;
                    }
                }
            }
        }
        
        return freshCount > 0 ? -1 : maxTime;
    }
};
```

### 6.2.3 Performance Analysis and Optimization

#### 6.2.3.1 Algorithmic Complexity and Optimization Techniques

```cpp
// 6.2.3.1.1 Performance analysis and optimization
class RottenOrangesOptimizer {
public:
    // 6.2.3.1.2 Space-optimized solution
    int optimizedOrangesRotting(vector<vector<int>>& grid) {
        cout << "=== Space-Optimized Rotten Oranges Solution ===" << endl;
        
        if (grid.empty() || grid[0].empty()) return 0;
        
        int rows = grid.size();
        int cols = grid[0].size();
        
        // Instead of storing Cell objects, use coordinate encoding
        queue<int> rottenQueue;  // Encode (row, col) as row * cols + col
        int freshCount = 0;
        
        // Single pass to initialize
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    rottenQueue.push(i * cols + j);
                } else if (grid[i][j] == 1) {
                    freshCount++;
                }
            }
        }
        
        if (freshCount == 0) return 0;
        if (rottenQueue.empty()) return -1;
        
        vector<pair<int, int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        int minutes = 0;
        
        while (!rottenQueue.empty() && freshCount > 0) {
            int levelSize = rottenQueue.size();
            
            for (int i = 0; i < levelSize; i++) {
                int encoded = rottenQueue.front();
                rottenQueue.pop();
                
                int row = encoded / cols;
                int col = encoded % cols;
                
                for (auto [dr, dc] : directions) {
                    int newRow = row + dr;
                    int newCol = col + dc;
                    
                    if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols 
                        && grid[newRow][newCol] == 1) {
                        
                        grid[newRow][newCol] = 2;
                        rottenQueue.push(newRow * cols + newCol);
                        freshCount--;
                    }
                }
            }
            
            minutes++;
        }
        
        cout << "Space-optimized solution completed in " << minutes << " minutes" << endl;
        cout << "Space saved: No Cell objects, encoded coordinates" << endl;
        
        return freshCount > 0 ? -1 : minutes - 1;
    }
    
    // 6.2.3.1.3 In-place solution without extra queue
    int inPlaceOrangesRotting(vector<vector<int>>& grid) {
        cout << "=== In-Place Rotten Oranges Solution ===" << endl;
        cout << "Using grid itself to track time, no extra queue space" << endl;
        
        if (grid.empty() || grid[0].empty()) return 0;
        
        int rows = grid.size();
        int cols = grid[0].size();
        int maxTime = 0;
        bool hasRotten = false;
        
        // Mark initial rotten oranges with time 2
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 2) {
                    hasRotten = true;
                    break;
                }
            }
            if (hasRotten) break;
        }
        
        if (!hasRotten) {
            // Check if there are any fresh oranges
            for (int i = 0; i < rows; i++) {
                for (int j = 0; j < cols; j++) {
                    if (grid[i][j] == 1) return -1;
                }
            }
            return 0;
        }
        
        vector<pair<int, int>> directions = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        bool changed = true;
        
        while (changed) {
            changed = false;
            
            // Create a copy to avoid modifying while reading
            vector<vector<int>> nextGrid = grid;
            
            for (int i = 0; i < rows; i++) {
                for (int j = 0; j < cols; j++) {
                    if (grid[i][j] >= 2) {  // Rotten orange (value >= 2 represents time)
                        for (auto [dr, dc] : directions) {
                            int newRow = i + dr;
                            int newCol = j + dc;
                            
                            if (newRow >= 0 && newRow < rows && newCol >= 0 && newCol < cols 
                                && grid[newRow][newCol] == 1) {
                                
                                nextGrid[newRow][newCol] = grid[i][j] + 1;
                                maxTime = max(maxTime, nextGrid[newRow][newCol]);
                                changed = true;
                            }
                        }
                    }
                }
            }
            
            grid = nextGrid;
        }
        
        // Check for remaining fresh oranges
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 1) {
                    cout << "Fresh orange remains at (" << i << "," << j << ")" << endl;
                    return -1;
                }
            }
        }
        
        int result = maxTime > 2 ? maxTime - 2 : 0;
        cout << "In-place solution completed, time: " << result << endl;
        return result;
    }
    
    // 6.2.3.1.4 Performance comparison
    void comparePerformance() {
        cout << "=== Performance Comparison ===" << endl;
        
        // Test with different grid sizes
        vector<int> sizes = {10, 50, 100, 200};
        
        for (int size : sizes) {
            cout << "\nTesting with " << size << "x" << size << " grid:" << endl;
            
            // Create test grid
            vector<vector<int>> grid(size, vector<int>(size, 1));  // All fresh
            
            // Add some rotten oranges
            for (int i = 0; i < size; i += size/4) {
                for (int j = 0; j < size; j += size/4) {
                    grid[i][j] = 2;
                }
            }
            
            // Test standard solution
            auto grid1 = grid;
            auto start1 = chrono::high_resolution_clock::now();
            RottenOrangesAnalyzer analyzer;
            int result1 = analyzer.orangesRotting(grid1);
            auto end1 = chrono::high_resolution_clock::now();
            auto duration1 = chrono::duration_cast<chrono::microseconds>(end1 - start1);
            
            // Test optimized solution
            auto grid2 = grid;
            auto start2 = chrono::high_resolution_clock::now();
            int result2 = optimizedOrangesRotting(grid2);
            auto end2 = chrono::high_resolution_clock::now();
            auto duration2 = chrono::duration_cast<chrono::microseconds>(end2 - start2);
            
            // Test in-place solution
            auto grid3 = grid;
            auto start3 = chrono::high_resolution_clock::now();
            int result3 = inPlaceOrangesRotting(grid3);
            auto end3 = chrono::high_resolution_clock::now();
            auto duration3 = chrono::duration_cast<chrono::microseconds>(end3 - start3);
            
            cout << "Standard solution: " << duration1.count() << " μs, result: " << result1 << endl;