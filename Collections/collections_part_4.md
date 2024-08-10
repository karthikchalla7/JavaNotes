# Java `Map` Interface and `HashMap` Internal Design
![image](https://github.com/user-attachments/assets/1d73e91c-e197-4769-8116-347176754551)

## Overview

This document provides an in-depth explanation of the `Map` interface in Java, particularly focusing on the `HashMap` and its internal workings. It covers key concepts like load factor, collisions, treeification, and more. The information is organized systematically to ensure a thorough understanding of these topics.

## Table of Contents

1. [Why Is `Map` Not a Child of `Collection`?](#why-is-map-not-a-child-of-collection)
2. [Properties of `Map`](#properties-of-map)
   - [Interface and Implementations](#interface-and-implementations)
   - [Key-Value Mapping](#key-value-mapping)
3. [Methods Available in the `Map` Interface](#methods-available-in-the-map-interface)
4. [Internal Design of `HashMap`](#internal-design-of-hashmap)
   - [Key Concepts](#key-concepts)
   - [How `HashMap` Works Internally](#how-hashmap-works-internally)
   - [Handling Collisions](#handling-collisions)
   - [The `get()` Operation](#the-get-operation)
5. [Contract Between `hashCode()` and `equals()`](#contract-between-hashcode-and-equals)
6. [Time Complexity](#time-complexity)
   - [Average Case](#average-case)
   - [Worst Case](#worst-case)
7. [Load Factor and Re-Hashing](#load-factor-and-re-hashing)
   - [Treeify Threshold](#treeify-threshold)

---

## Why Is `Map` Not a Child of `Collection`?

The `Map` interface in Java is not a child of the `Collection` interface because it operates on a fundamentally different concept. The `Collection` interface and its implementations (like `List`, `Set`, etc.) deal with single values or elements. For example, a `List` is a collection of elements where each element is a single value.

In contrast, a `Map` deals with key-value pairs, where each key is mapped to a value. This key-value mapping mechanism is fundamentally different from the single-value element handling in `Collection`. As a result, `Map` does not extend the `Collection` interface; it has its own interface with methods tailored to handling key-value pairs.

## Properties of `Map`

### Interface and Implementations

- **HashMap**: Does not maintain any order. It allows `null` values and one `null` key.
- **Hashtable**: A synchronized version of `HashMap` but does not allow `null` values or keys.
- **LinkedHashMap**: Maintains insertion order, so elements are returned in the order they were inserted.
- **TreeMap**: Sorts the keys in natural order or by a specified comparator.

### Key-Value Mapping

- `Map` objects map keys to values.
- A key in a `Map` must be unique. That means no two keys can map to the same value.
- A `Map` cannot contain duplicate keys, but it can have duplicate values.

## Methods Available in the `Map` Interface

1. **`size()`**: Returns the number of key-value pairs in the map.
2. **`isEmpty()`**: Checks if the map is empty.
3. **`containsKey(Object key)`**: Checks if the map contains a mapping for the specified key.
4. **`containsValue(Object value)`**: Checks if the map contains one or more keys mapped to the specified value.
5. **`get(Object key)`**: Returns the value to which the specified key is mapped, or `null` if the map contains no mapping for the key.
6. **`put(K key, V value)`**: Associates the specified value with the specified key in the map.
![image](https://github.com/user-attachments/assets/98f60260-7a09-4650-be6b-f001ac0cd814)

```java
package COLLECTIONS;

import java.util.Collection;
import java.util.HashMap;
import java.util.Map;

public class hashExample {

    public static void main(String[] args) {
        Map<Integer,String> map = new HashMap();
        map.put(null,"test");
        map.put(0,null);
        map.put(1,"A");
        map.put(2,"B");

        //compute if present
        map.putIfAbsent(null,"TEST");
        map.putIfAbsent(0,"ZERO");
        map.putIfAbsent(1,"C");

        for(Map.Entry<Integer,String> entrymap : map.entrySet()){
            Integer key = entrymap.getKey();
            String value = entrymap.getValue();
            System.out.println("Key: "+key+" value: "+value);
        }

        //isEmpty
        System.out.println("isEmpty(): "+map.isEmpty());

        //size
        System.out.println("Size: "+map.size());

        //containsKey
        System.out.println("containsKey(3): "+map.containsKey(3));

        //get(Key)
        System.out.println("get(1): "+map.get(1));

        //getOrDefault
        System.out.println("get(9): "+map.getOrDefault(9,"default value"));

        //remove(key)
        System.out.println("remove(null): "+map.remove(null));

        for (Map.Entry<Integer,String> entryMap:map.entrySet()){
            int key = entryMap.getKey();
            String value = entryMap.getValue();
            System.out.println("Key: "+key+" value "+value);
        }

        //keySet()
        for(Integer key:map.keySet()){
            System.out.println("Key: "+key);
        }

        //values
        Collection<String> values = map.values();
        for(String value:values){
            System.out.println("value: "+value);
        }

    }
}

```
## Internal Design of `HashMap`

Understanding the internal design of `HashMap` is crucial as it is one of the most widely used data structures in Java. Let's break it down step by step:
- Can store null key or value(Hash Table do not contains null key or value)
- HashMap do not maintains the insertion order
- Its not thread safe(instead use ConcurrentHashMap or HashTable for thread safe HashMap implementation).

### Key Concepts

1. **Load Factor**
   - The load factor is a measure of how full the `HashMap` can get before its capacity is automatically increased.
   - The default load factor is `0.75`, meaning that when the `HashMap` is 75% full, it will increase its size (i.e., the number of buckets).
   - This helps in balancing memory usage and performance. A higher load factor reduces space overhead but increases the likelihood of collisions, while a lower load factor increases space overhead but reduces collision chances.

2. **Entry<K, V> Interface**
   - In the internal implementation, each bucket in the `HashMap` is essentially a linked list (or a tree structure in case of high collisions) of `Entry` objects.
   - Each `Entry` object contains a key, a value, and a reference to the next `Entry` object in the same bucket.

3. **Re-Hashing**
   - Re-hashing is the process of resizing the `HashMap` when the number of entries exceeds the capacity defined by the load factor.
   - When re-hashing occurs, the `HashMap` increases its capacity, and all existing entries are rehashed and placed into the new buckets.

4. **Performance**
   - `HashMap` provides O(1) time complexity for insertion, deletion, and retrieval operations on average.
   - However, in the worst case, the time complexity can be O(n) if many keys hash to the same index, forming a long linked list.

### How `HashMap` Works Internally

1. **Initialization**
   - A `HashMap` is initialized with an array of `Node<K, V>` (which implements `Entry<K, V>`). The default size of this array is 16.

2. **The `put()` Operation**
   - **Step 1**: When you call `put(1, "sj")`, the `HashMap` first computes the hash of the key using its `hashCode()` method.
   - **Step 2**: It then calculates the index in the array using the formula `index = hash % array_length`. This ensures that the index is within the bounds of the array.
   - **Step 3**: The key-value pair is then stored at this computed index. If the index is already occupied, it checks if the key is the same. If it is, it overwrites the value; otherwise, it creates a new `Node<K, V>` and adds it to the linked list at that index.

3. **Handling Collisions**

   - **Scenario 1**: If two keys hash to the same index, a collision occurs.
   - **Scenario 2**: For example, if `put(10, "kj")` results in the same index as an existing entry, the `HashMap` checks the key:
     - If the key matches, it updates the value.
     - If the key does not match, it adds the new `Node<K, V>` to the linked list at that index.
   - **Scenario 3**: If too many collisions occur (i.e., a long linked list is formed), the `HashMap` will transform the list into a balanced binary tree (red-black tree) to improve lookup time.

4. **The `get()` Operation**
   - **Step 1**: To retrieve a value, the `HashMap` calculates the hash of the key and finds the corresponding index.
   - **Step 2**: It then checks the entries at that index. If there is a linked list or tree at that index, it iterates through it (or traverses the tree) to find the correct key and return the associated value.

### Contract Between `hashCode()` and `equals()`

- **`hashCode()` Method**
  - Generates an integer hash code for an object.
  - This hash code is used to compute the index in the `HashMap`.

- **`equals()` Method**
  - Compares two objects for equality.

- **Contract**
  1. If two objects are equal (`equals()` returns `true`), their `hashCode()` must be the same.
  2. If two objects have the same `hashCode()`, they are not necessarily equal.

## Time Complexity

### Average Case

- Insertion, deletion, and searching in a `HashMap` have an average time complexity of O(1). This is because, under normal circumstances, the `HashMap` is well-distributed, and collisions are minimal.
![image](https://github.com/user-attachments/assets/0341d934-b6fa-4bc4-9da9-d5f62e9a09ce)

### Worst Case

- If many keys hash to the same index, forming a long linked list, the time complexity can degrade to O(n) for these operations.
- However, with treeification (conversion of the linked list to a tree), the time complexity improves to O(log n) in the worst case.

## Load Factor and Re-Hashing

### Load Factor

- The default load factor of `0.75` means that when the `HashMap` reaches 75% of its capacity, it automatically resizes and rehashes the entries.
- This resizing reduces collisions and maintains the O(1) average time complexity.

### Treeify Threshold

- If too many collisions occur on a single index (by default, more than 8), the `HashMap` converts the linked list at that index into a balanced binary tree.
- This tree structure improves search time from O(n) to O(log n).


```plaintext
HashMap: Array of Nodes
-----------------------------------
Index  |  Node (Key, Value, Hash, Next)
-----------------------------------
  0    |  null
  1    |  null
  2    |  (5, "Apple", 42) -> (13, "Orange", 130) -> null
  3    |  null
  4    |  null
  5    |  (7, "Banana", 99) -> null
  6    |  null
  7    |  (9, "Mango", 55) -> null
  8    |  null
  9    |  (12, "Grapes", 102) -> null
 10    |  null
 11    |  null
 12    |  (1, "Pear", 83) -> null
 13    |  null
 14    |  null
 15    |  null
-----------------------------------
```

### How It Works:

1. **Initialization**:
   - `HashMap` is initialized with an array of size 16 (default).
   - Each array element is a bucket that can hold linked lists of `Node` objects.

2. **Adding Entries**:
   - Each key-value pair is stored in a `Node` object.
   - The `Node` object contains:
     - `Key`: The key to be stored (e.g., `5`).
     - `Value`: The value associated with the key (e.g., `"Apple"`).
     - `Hash`: The hash code computed from the key (e.g., `42`).
     - `Next`: A reference to the next node in the linked list (initially `null`).

3. **Index Calculation**:
   - The index in the array is calculated using the formula: `index = hash % array_length`.
   - For example, if the hash code is `42` and the array length is `16`, the index is `42 % 16 = 2`.

4. **Handling Collisions**:
   - If two keys result in the same index, they form a linked list at that index.
   - For example:
     - `Key 5 ("Apple")` and `Key 13 ("Orange")` both hash to index `2`.
     - The `Node` for `Key 13` is added to the `Next` reference of the `Node` for `Key 5`, creating a linked list.

5. **Visualization Example**:
   - At index `2`, the linked list looks like this:
     - `(5, "Apple", 42) -> (13, "Orange", 130) -> null`
   - The `Next` reference in the first `Node` points to the second `Node`, and the `Next` reference in the second `Node` is `null`, indicating the end of the list.

### Node Structure

```plaintext
Node Structure:
-----------------------------------------
Key  |  Value   |  Hash |  Next
-----------------------------------------
  5  |  "Apple" |  42   |  Node(13, "Orange", 130) -> null
 13  | "Orange" | 130   |  null
-----------------------------------------
```

### Visualizing a `get()` Operation:

```plaintext
// To retrieve the value for key 13:
1. Calculate the hash of the key: 130
2. Calculate the index: 130 % 16 = 2
3. At index 2, traverse the linked list:
   - Check the first node: Key 5 (not a match)
   - Move to the next node: Key 13 (match found)
4. Return the value: "Orange"
```
