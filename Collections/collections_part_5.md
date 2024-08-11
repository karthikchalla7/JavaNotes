## Collections (Map)

### Table of Contents
1. [LinkedHashMap](#linkedhashmap)
   - [Overview](#overview)
   - [Key Features](#key-features)
   - [Internal Structure](#internal-structure)
   - [Visualization](#visualization)
   - [How It Works](#how-it-works)
   - [Example](#example)
   - [Insertion Order Maintenance](#insertion-order-maintenance)
   - [Access Order Example](#access-order-example)
2. [TreeMap](#treemap)
   - [Overview](#overview-1)
   - [Key Features](#key-features-1)
   - [Internal Structure](#internal-structure-1)
   - [Visualization](#visualization-1)
   - [How It Works](#how-it-works-1)
   - [Example](#example-1)

---

### LinkedHashMap

### Overview
- **LinkedHashMap** is a combination of `HashMap` and `LinkedList`. It maintains a doubly-linked list running through all its entries, which defines the order in which keys were inserted.
- It provides two types of ordering:
  1. **Insertion Order**: The order in which keys were inserted.
  2. **Access Order**: The order in which entries were accessed (get or put operation).

### Key Features
- **Maintains Order**: `LinkedHashMap` maintains the order of elements, either insertion order or access order, depending on its configuration.
- **Performance**: The average time complexity for basic operations like `put`, `get`, and `remove` is O(1), similar to `HashMap`.
- **Not Thread-Safe**: `LinkedHashMap` is not synchronized. To make it thread-safe, it must be wrapped using `Collections.synchronizedMap`.

```java
Map<Integer, String> map = Collections.synchronizedMap(new LinkedHashMap<>());
```

### Internal Structure
- **Hash Table**: Similar to `HashMap`, `LinkedHashMap` uses a hash table to store key-value pairs.
- **Doubly-Linked List**: In addition to the hash table, `LinkedHashMap` maintains a doubly-linked list that connects all the entries in the map. This list defines the iteration order of the map.
![image](https://github.com/user-attachments/assets/500e3a72-5bd5-456e-9a3c-c089c3bcdaf3)

### Visualization

```
LinkedHashMap: Array of LinkedNodes
-----------------------------------
Index  |  Node (Key, Value, Hash, Next, Prev, NextLink)
-----------------------------------
  0    |  null
  1    |  null
  2    |  (5, "Apple", 42, null, null, Node(13, "Orange", 130)) -> (13, "Orange", 130, null, Node(5, "Apple", 42), Node(7, "Banana", 99))
  3    |  null
  4    |  null
  5    |  (7, "Banana", 99, null, Node(13, "Orange", 130), Node(9, "Mango", 55)) -> null
  6    |  null
  7    |  (9, "Mango", 55, null, Node(7, "Banana", 99), Node(12, "Grapes", 102)) -> null
  8    |  null
  9    |  (12, "Grapes", 102, null, Node(9, "Mango", 55), Node(1, "Pear", 83)) -> null
 10    |  null
 11    |  null
 12    |  (1, "Pear", 83, null, Node(12, "Grapes", 102), null) -> null
 13    |  null
 14    |  null
 15    |  null
-----------------------------------
```

### How It Works
1. **Node Structure**:
   - Each node in the `LinkedHashMap` contains the following:
     - `Key`: The key associated with the entry.
     - `Value`: The value associated with the key.
     - `Hash`: The hash code of the key.
     - `Next`: A reference to the next node in the same bucket (collision handling).
     - `Prev`: A reference to the previous node in the linked list.
     - `NextLink`: A reference to the next node in the linked list.

2. **Insertion Order Maintenance**:
   - When a new entry is inserted, it's added to the end of the linked list.
   - The `Prev` and `NextLink` pointers ensure that the insertion order is preserved.

3. **Access Order**:
   - If the map is configured to maintain access order, every time an entry is accessed (through `get` or `put`), that entry is moved to the end of the linked list.

### Example
- Let's insert the following key-value pairs in a `LinkedHashMap`:
  - `put(5, "Apple")`
  - `put(13, "Orange")`
  - `put(7, "Banana")`
  - `put(9, "Mango")`
  - `put(12, "Grapes")`
  - `put(1, "Pear")`

### Insertion Order Maintenance
- **Insertion Order Visualization**:
  - The linked list forms as:
    - `(5, "Apple") -> (13, "Orange") -> (7, "Banana") -> (9, "Mango") -> (12, "Grapes") -> (1, "Pear") -> null`
  - The hash table contains the nodes at the respective indices.

### Access Order Example
- If `LinkedHashMap` is set to maintain access order:
  - After performing `get(7)` on the above map, the linked list will reorder to:
    - `(5, "Apple") -> (13, "Orange") -> (9, "Mango") -> (12, "Grapes") -> (1, "Pear") -> (7, "Banana") -> null`

---
```java
package COLLECTIONS;

import java.util.LinkedHashMap;
import java.util.Map;
import java.util.HashMap;
public class linkedHashmap {

    public static void main(String[] args) {

       //insertion order.
        System.out.println("---below is linkedHashMap output---");
        Map<Integer,String> map = new LinkedHashMap<>();

        map.put(1,"a");
        map.put(21,"b");
        map.put(23,"c");
        map.put(141,"d");
        map.put(25,"e");

        map.forEach((key,value)-> System.out.println(key+" "+value));
        System.out.println("----below is normal hash map output---");
        Map<Integer,String> map1 = new HashMap<>();

        map1.put(1,"a");
        map1.put(23,"b");
        map1.put(21,"c");
        map1.put(141,"d");
        map1.put(25,"e");

        map1.forEach((key,value)-> System.out.println(key+" "+value));

        //access order.
        //this puts the most frequently used at the last it is used in cache purging.
        //cache purgin is the process of clearing out old or unneeded data from a cahce to enusre that only the most relevant and up-to-dat information is kept.
        System.out.println("----access order using linkedhashmap----");
        Map<Integer,String > map2 = new LinkedHashMap<>(16,0.75F,true);

        map2.put(1,"a");
        map2.put(23,"b");
        map2.put(21,"c");
        map2.put(141,"d");
        map2.put(25,"e");

        //acessing some data
        map2.get(23);
        map2.forEach((key,value)-> System.out.println(key+" "+value));
    }
}

```
### TreeMap  

### Overview
- **TreeMap** is a `Map` implementation that stores key-value pairs in a sorted order based on the natural ordering of the keys or by a custom comparator provided during the map's creation.
- It is based on a **Red-Black Tree**, a self-balancing binary search tree (BST).
![image](https://github.com/user-attachments/assets/1a53d707-f286-4b06-b191-338b86b83909)

### Key Features
- **Sorted Order**: `TreeMap` sorts entries according to the natural order of the keys (e.g., alphabetical or numerical order) or a custom comparator.
- **Performance**: Operations like `put`, `get`, and `remove` have a time complexity of O(log n) due to the underlying tree structure.
- **Thread-Safety**: `TreeMap` is not synchronized. To make it thread-safe, it must be wrapped using `Collections.synchronizedMap`.

```java
Map<Integer, String> treeMap = Collections.synchronizedMap(new TreeMap<>());
```

### Internal Structure
- **Red-Black Tree**: A self-balancing binary search tree, where each node contains a key-value pair.
- **Balance**: The tree maintains balance through rotations and color changes (red and black) after each insertion and deletion.
- left,parent,Key,value,right.
![image](https://github.com/user-attachments/assets/df5c9eb6-f69f-4778-94dd-03287178ab87)

### Visualization

```plaintext
TreeMap: Red-Black Tree Structure
-----------------------------------
           (9, "Mango")
          /               \
(5, "Apple")           (12, "Grapes")
      \                    /      \
  (7, "Banana")   (10, "Peach") (13, "Orange")
-----------------------------------
```

### How It Works
1. **Node Structure**:
   - Each node in the `TreeMap` contains:
     - `Key`: The key associated with the entry.
     - `Value`: The value associated with the key.
     - `Left`: A reference to the left child node.
     - `Right`: A reference to the right child node.
     - `Color`: Red or black (used for balancing).

2. **Sorting**:
   - The keys are stored in sorted order, and the tree adjusts itself after each insertion or deletion to maintain balance.

3. **Balancing**:
   - **Red-Black Properties**:
     - Every node is either red or black.
     - The root is always black.
     - Red nodes cannot have red children.
     - Every path from the root to a leaf must have the same number of black nodes.

4. **Operations**:
   - **Insertion**: Adds a node to the tree, followed by a rebalancing step.
   - **Deletion**: Removes a node from the tree, followed by a rebalancing step.
   - **Searching**: Traverses the tree in O(log n) time to find the desired key.

### Example
- Let's insert the following key-value pairs in a `TreeMap`:
  - `put(9, "Mango")`
  - `put(5, "Apple")`
  - `put(12, "Grapes")`
  - `put(7, "Banana")`
  - `put(13, "Orange")`
  - `put(10, "Peach")`

- **Sorted Order Visualization**:
  - The `TreeMap` will self-balance to maintain the sorted order of keys:
    - `(9, "Mango")` as the root.
    - `(5, "Apple")` as the left child of the root.
    - `(12, "Grapes")` as the right child of the root.
    - `(7, "Banana")` as the right child of `Apple`.
    - `(13, "Orange")` as the right child of `Grapes`.
    - `(10, "Peach")` as the left child of `Grapes`.

---
```java
package COLLECTIONS;

import java.util.Map;
import java.util.SortedMap;
import java.util.TreeMap;

public class treeMap {

    public static void main(String[] args) {
        Map<Integer,String > map = new TreeMap<>((key1,key2)->key2-key1);
        map.put(21,"ss");
        map.put(11,"lk");
        map.put(13,"kj");
        map.put(5,"th");

        //decreasing order because of the comparator passed.
        map.forEach((key,value)-> System.out.println(key+" "+value));

        System.out.println("-----------------");
        Map<Integer,String > map1 = new TreeMap<>();
        map1.put(21,"ss");
        map1.put(11,"lk");
        map1.put(13,"kj");
        map1.put(5,"th");
        //increason order
        map1.forEach((key,value)-> System.out.println(key+" "+value));

    }
}

```

### Methods available in SORTEDMAP interface:
![image](https://github.com/user-attachments/assets/4b5d7176-bba7-4ac2-8319-2803828dadd2)

```java
//sorted map methods
        System.out.println("-----------------");
        SortedMap<Integer,String > map2 = new TreeMap<>();
        map2.put(21,"ss");
        map2.put(11,"lk");
        map2.put(13,"kj");
        map2.put(5,"th");

        //it prints till that key(exclusive)
        System.out.println(map2.headMap(13));

        //from the key (inclusive)
        System.out.println(map2.tailMap(13));
        System.out.println(map2.firstKey());
        System.out.println(map2.lastKey());

```

### Methods available in NAVIGABLEMAP interface:
![image](https://github.com/user-attachments/assets/3b4ae5de-b60d-4460-9b75-cc9dc7c3285f)  
![image](https://github.com/user-attachments/assets/2e9bf5b7-dde1-45de-8399-4ed3172b1b53)  
![image](https://github.com/user-attachments/assets/109d3ed9-bcfb-4865-ab08-58d0844c6ee7)  

