# **HashMap in Java**

A **HashMap** is part of Java's collection framework, specifically from the `java.util` package, and it is a widely used data structure for storing key-value pairs. It allows efficient access, insertion, and deletion of data using a key, providing a near-constant time complexity for these operations under ideal circumstances.

### **Definition**

- **HashMap** is a data structure that implements the `Map` interface, allowing it to store objects in key-value pairs. 
- It is based on **hashing**, where the hash code of a key is used to compute an index into an array (called the **bucket**) where the value is stored.
  
```java
Map<String, Integer> hashMap = new HashMap<>();
```

### **Characteristics of HashMap**
- **Key-Value Pairs**: It stores data in the form of key-value pairs, where each key is unique.
- **Null Values and Keys**: HashMap allows one `null` key and multiple `null` values.
- **Non-Synchronized**: HashMap is not synchronized, meaning it is not thread-safe. If thread safety is required, consider using `Collections.synchronizedMap()` or `ConcurrentHashMap`.
- **No Guaranteed Order**: The order of keys and values in a HashMap is not guaranteed, i.e., it does not maintain insertion order. For insertion-ordered data, use `LinkedHashMap`.

### **Internal Working of HashMap**

HashMap uses an internal array (called a **bucket array**) where each element is a bucket that can hold multiple entries in case of collisions.

1. **Hashing**: 
   When a key-value pair is inserted into the HashMap, the key is passed through a **hash function** to compute an integer called the **hash code**.
   ```java
   int hashCode = key.hashCode();
   ```
   This hash code is then mapped to an index in the internal array using the formula:
   ```java
   index = (n-1) & hashCode  // n is the size of the array
   ```

2. **Collision Handling**: 
   HashMap handles collisions using **separate chaining**, where each bucket contains a linked list (or a binary tree in case of Java 8 onwards for large chains). If two keys produce the same hash code, the new key-value pair is stored in the same bucket but as a new node in the list.

3. **Rehashing**: 
   When the number of entries exceeds the load factor (default is 0.75), HashMap **doubles the size** of its bucket array to reduce the probability of collisions. This process is called **rehashing**.

### **Basic Operations on HashMap**

1. **Insertion**:
   Insertion in a HashMap is done via the `put()` method. The key is hashed, and the key-value pair is inserted into the appropriate bucket.
   ```java
   hashMap.put("John", 25);  // Inserting a key-value pair
   ```

2. **Retrieval**:
   The value for a given key can be retrieved using the `get()` method. It uses the key's hash code to locate the bucket.
   ```java
   int age = hashMap.get("John");  // Retrieving value associated with the key "John"
   ```

3. **Deletion**:
   The `remove()` method is used to delete an entry based on the key. The key is hashed, and the corresponding entry is removed.
   ```java
   hashMap.remove("John");  // Removes the key-value pair with key "John"
   ```

4. **Contains Key/Value**:
   The `containsKey()` and `containsValue()` methods are used to check if a key or value exists in the map.
   ```java
   boolean exists = hashMap.containsKey("John");  // Checks if key "John" exists
   ```

5. **Size**:
   The `size()` method returns the number of key-value pairs in the map.
   ```java
   int size = hashMap.size();  // Returns the number of entries in the map
   ```

6. **Iteration**:
   You can iterate over the keys, values, or entries of a HashMap using loops or Java 8 streams.
   ```java
   for (Map.Entry<String, Integer> entry : hashMap.entrySet()) {
       System.out.println(entry.getKey() + ": " + entry.getValue());
   }
   ```

### **Example Code**

```java
import java.util.HashMap;

public class HashMapExample {
    public static void main(String[] args) {
        HashMap<String, Integer> hashMap = new HashMap<>();
        
        // Inserting elements
        hashMap.put("John", 25);
        hashMap.put("Alice", 30);
        hashMap.put("Bob", 22);
        
        // Retrieving value
        System.out.println("John's age: " + hashMap.get("John"));
        
        // Checking if key exists
        if (hashMap.containsKey("Alice")) {
            System.out.println("Alice is present in the map.");
        }
        
        // Removing element
        hashMap.remove("Bob");
        System.out.println("After removing Bob: " + hashMap);
        
        // Iterating over HashMap
        for (Map.Entry<String, Integer> entry : hashMap.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

### **Advantages of HashMap**

1. **Fast Access**: HashMap offers **constant time complexity (O(1))** for basic operations like insertion, deletion, and retrieval when there are minimal collisions.
2. **Flexible Storage**: It allows null values and a single null key.
3. **Dynamic Resizing**: HashMap automatically resizes itself when the load factor threshold is reached, ensuring efficient use of memory.
4. **No Key Constraints**: Keys can be any object, allowing a wide variety of key types.

### **Disadvantages of HashMap**

1. **Not Synchronized**: By default, HashMap is not thread-safe. To use it in multi-threaded applications, synchronization has to be manually applied or alternatives like `ConcurrentHashMap` should be used.
2. **Unordered**: HashMap does not maintain any order of elements, such as insertion order. If order is needed, `LinkedHashMap` should be used.
3. **Memory Overhead**: Due to the internal array resizing mechanism, there can be significant memory overhead.

### **When to Use HashMap**

- When you need fast lookups, insertions, and deletions, and the order of the elements doesn’t matter.
- Use it in scenarios where duplicate keys are not allowed, but you need quick access to the associated values.

### **When Not to Use HashMap**

- In cases where you require a **thread-safe** data structure, consider using `ConcurrentHashMap`.
- If you need to maintain **insertion order**, use `LinkedHashMap`.
- If you need to **sort the map** based on keys, use `TreeMap`.

### Threshold in HashMap

The **threshold** in a HashMap is the product of its capacity and load factor, and it determines when the HashMap will resize itself (i.e., rehash). 

- **Capacity**: This is the number of buckets in the HashMap. The default capacity is 16.
- **Load Factor**: This is the measure of how full the HashMap can get before it needs resizing. The default load factor is 0.75.

The threshold is calculated as:
```java
threshold = capacity * loadFactor;
```
For example, with a capacity of 16 and a load factor of 0.75, the threshold will be 12. Once the number of entries exceeds this threshold, the HashMap will resize by doubling the number of buckets (i.e., the capacity will be doubled).

#### Example
If you insert the 13th element into a HashMap with an initial capacity of 16 and a load factor of 0.75, the HashMap will resize to have 32 buckets (double the previous size) and rehash all existing entries.

#### **Purpose of Threshold and Load Factor**
- **Efficient Use of Space**: The load factor controls the balance between time and space efficiency. A higher load factor reduces space usage but increases lookup cost due to collisions.
- **Avoiding Collisions**: Keeping the load factor at a moderate value (such as 0.75) ensures a balance between memory usage and avoiding excessive collisions.

### Binary Tree in Java 8 HashMap

In **Java 8**, HashMap underwent a significant optimization: when the number of elements in a single bucket exceeds a certain threshold (by default, 8 entries), the HashMap uses a **balanced binary tree** (specifically a Red-Black Tree) rather than a linked list to store entries in that bucket. This reduces the time complexity for retrieval from **O(n)** to **O(log n)** in the worst case, where `n` is the number of entries in a single bucket.

#### **How Java 8 HashMap Works**

1. **Before Java 8**: If multiple keys hash to the same bucket (i.e., a collision occurs), they were stored in a linked list. Retrieval time for a single key in such a scenario was **O(n)**.
   
2. **After Java 8**: Java 8 introduced **treeification**. When the number of entries in a bucket exceeds the threshold (default is 8), the linked list is replaced by a balanced Red-Black Tree. This reduces the lookup time complexity to **O(log n)** in such a bucket.

#### **Steps in Java 8 HashMap Treeification**
- **Bucket Size Threshold**: If a bucket contains more than 8 entries, HashMap replaces the linked list with a Red-Black Tree.
- **Untreeify Threshold**: If the number of entries in a bucket decreases below 6, the Red-Black Tree reverts to a linked list.
- **Resize Threshold**: Treeification only occurs if the HashMap capacity is at least 64 (to avoid treeification for small maps).

#### **Why Red-Black Tree?**
- **Balanced Structure**: Red-Black trees are a type of self-balancing binary search tree. This ensures that the tree height remains balanced, leading to **O(log n)** complexity for search, insert, and delete operations in the worst case.
- **Efficient Performance**: Linked lists result in linear time lookups when collisions are high. Treeification reduces this to logarithmic time, making lookups and inserts more efficient when many collisions occur.

#### **Implementation of Treeification**
Treeification is handled internally by the `HashMap` class. You don't need to write extra code to utilize treeification—it is triggered automatically when the number of elements in a bucket exceeds the threshold.

### **Example of How Treeification Happens**
If 10 keys map to the same bucket due to collisions, the first 8 keys will be stored in a linked list. When the 9th key is inserted, Java 8's HashMap will convert this list into a Red-Black Tree to reduce the lookup time for future entries.

### **Sample Code (Java 8 HashMap Optimization)**

```java
import java.util.HashMap;

public class HashMapTreeificationExample {
    public static void main(String[] args) {
        HashMap<String, Integer> map = new HashMap<>();

        // Adding more than 8 colliding keys (these strings should ideally hash to the same bucket)
        for (int i = 0; i < 10; i++) {
            map.put("Key" + i, i);
        }

        // The map automatically handles treeification when the number of entries in a bucket exceeds 8
        System.out.println(map);
    }
}
```

In this example, when more than 8 keys hash to the same bucket, HashMap automatically treeifies the bucket for better performance.

### **Advantages of Treeification**
- **Performance Improvement**: Lookup time is improved from O(n) to O(log n) when a bucket is treeified.
- **Balanced Growth**: Red-Black Trees are self-balancing, ensuring that the height of the tree remains minimal even when there are many collisions.

### **Disadvantages of Treeification**
- **Memory Overhead**: Trees have a higher memory footprint compared to linked lists due to additional pointers and balancing mechanisms.
- **Complexity**: Although Java handles the treeification automatically, the internal mechanics of Red-Black Trees are more complex compared to linked lists.

### **Program to explain above mentioned all features**
### Code Example:

```java
import java.util.HashMap;
import java.util.Map;

public class HashMapDemo {

    public static void main(String[] args) {
        // Demonstrating default behavior of HashMap
        HashMap<String, Integer> defaultMap = new HashMap<>();
        
        System.out.println("Default HashMap:");
        System.out.println("Capacity (default): " + 16);
        System.out.println("Load factor (default): " + 0.75);
        System.out.println("Threshold for resize (default): " + (16 * 0.75) + " entries");

        // Adding entries to trigger resize
        for (int i = 0; i < 20; i++) {
            defaultMap.put("Key" + i, i);
        }
        
        // Map will automatically resize after exceeding the threshold of 12
        System.out.println("Entries in defaultMap: " + defaultMap.size());
        System.out.println("HashMap resized after threshold exceeded.\n");

        // Demonstrating treeification when collisions exceed threshold (Java 8+)
        // Use custom keys with same hashcode to cause collisions
        System.out.println("Demonstrating Treeification:");
        HashMap<CollidingKey, Integer> treeifiedMap = new HashMap<>();
        
        for (int i = 0; i < 10; i++) {
            CollidingKey key = new CollidingKey(i);
            treeifiedMap.put(key, i);
        }
        
        System.out.println("Entries with same hashcode (causing collisions): " + treeifiedMap.size());
        System.out.println("Treeification occurs when more than 8 entries in a bucket.\n");

        // Demonstrating key/value retrieval
        for (Map.Entry<CollidingKey, Integer> entry : treeifiedMap.entrySet()) {
            System.out.println("Key: " + entry.getKey() + ", Value: " + entry.getValue());
        }
    }
}

// Class to generate custom keys with same hashcode to simulate collisions
class CollidingKey {
    private int value;

    public CollidingKey(int value) {
        this.value = value;
    }

    @Override
    public int hashCode() {
        // Custom hashcode to simulate collisions by returning the same hash for all objects
        return 1;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        CollidingKey that = (CollidingKey) obj;
        return value == that.value;
    }

    @Override
    public String toString() {
        return "CollidingKey{" + "value=" + value + '}';
    }
}
```
### Explanation:
1. **Threshold and Resizing**: The HashMap will automatically resize when the number of entries exceeds the capacity * load factor.
2. **Treeification**: When a large number of collisions occur (more than 8 in a single bucket), the HashMap will switch from a linked list to a Red-Black Tree for better lookup performance.
3. **Default Behavior**: Demonstrating default capacity, load factor, and how entries are stored.
4. **Custom Hash Function**: Demonstrating the collisions and treeification.
   
### Explanation of Code:
1. **Default HashMap**:
   - It starts with a default capacity of 16 and a load factor of 0.75.
   - When the number of entries exceeds the threshold (16 * 0.75 = 12), the map resizes itself by doubling the capacity.

2. **Treeification**:
   - The `CollidingKey` class forces all keys to have the same hashcode (`hashCode()` always returns 1). This simulates many keys ending up in the same bucket.
   - When the number of entries in a single bucket exceeds 8, Java 8’s HashMap converts the linked list into a Red-Black Tree to reduce the lookup time from O(n) to O(log n).
  
3. **`hashCode` Method**:
   - In the `CollidingKey` class, the `hashCode` method always returns 1. This ensures that all objects of this class collide into the same bucket in the HashMap.
   - In a real-world scenario, the `hashCode` method typically provides a unique hash based on the object's state to ensure efficient key distribution across different buckets.

### Breakdown of Features:
1. **Threshold and Resizing**:
   - The `defaultMap` starts with a capacity of 16. After adding 20 elements, the map will resize to ensure performance is not degraded.

2. **Treeification**:
   - The `treeifiedMap` demonstrates Java 8's optimization where linked lists become Red-Black Trees after many collisions. This is done to improve the performance of lookups from O(n) to O(log n) in cases where multiple keys hash to the same bucket.

### Key Points:
- **Threshold**: Calculated as `capacity * load factor`. Once exceeded, the HashMap resizes.
- **Treeification**: When more than 8 keys hash to the same bucket, the linked list in that bucket is replaced with a Red-Black Tree.
- **Capacity Doubling**: The capacity of the HashMap doubles when the threshold is exceeded, ensuring the map remains efficient as the number of entries grows.

### Use Cases:
- **Threshold & Resizing**: A typical use case for threshold and resizing is to ensure that HashMap operations (like lookup and insertion) remain fast even as the number of entries grows.
- **Treeification**: In systems where there is a high likelihood of hash collisions, treeification helps maintain efficient lookups and avoids performance degradation.

### **Conclusion**
- The **HashMap** is a crucial part of the Java Collection Framework and is extremely efficient for storing and retrieving data based on keys. It is widely used in scenarios where performance matters, and order is not crucial. However, care should be taken when using it in multi-threaded environments.
- The **threshold** value in a HashMap controls when the map resizes itself, maintaining efficient performance as the number of elements grows.
- In **Java 8**, **treeification** was introduced to improve performance when there are many hash collisions by converting linked lists into balanced Red-Black Trees when necessary.

These changes ensure that the HashMap remains efficient even in cases where there are a large number of collisions, keeping performance predictable and fast for typical operations like insertion and retrieval.
