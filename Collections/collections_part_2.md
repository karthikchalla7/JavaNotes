# Collections Part 2

## Table of Contents
1. [Queue in Collection](#queue-in-collection)
   - [Queue Methods](#queue-methods)
2. [Priority Queue](#priority-queue)
   - [Types of Priority Queues](#types-of-priority-queues)
   - [Time Complexities](#time-complexities)
3. [Comparator vs Comparable](#comparator-vs-comparable)
   - [Comparator](#comparator)
   - [Comparable](#comparable)

## Queue in Collection

Queue is an interface in the Java Collections framework. It follows the FIFO (First In First Out) principle, with exceptions like priority queue. Queue supports all methods available in Collection, plus some additional methods.
![image](https://github.com/user-attachments/assets/a7159e1a-f71c-49d1-aad3-3bb9d2ac81d0)

### Queue Methods

1. **add()**
   - Inserts an element into the queue
   - Returns true if insertion is successful, throws an exception if insertion fails
   - Null elements are not allowed (throws NullPointerException)

2. **offer()**
3. **poll()**
   - Retrieves and removes the head of the queue
   - Returns null if the queue is empty

4. **remove()**
   - Same as poll(), but throws an exception if the queue is empty

5. **peek()**
   - Same as poll() but does not remove the element

6. **element()**
  
![image](https://github.com/user-attachments/assets/4b1c9105-1255-4cda-89fe-8bc9a93fa79b)

## Priority Queue

Priority Queue is used for heap implementation. There are two types: Minimum priority queue and Maximum priority queue. Elements are ordered according to their natural ordering (by default) or by a comparator provided during queue construction.  
![image](https://github.com/user-attachments/assets/0680f925-6e8b-46be-8def-6718f1ae77a5)

### Types of Priority Queues

1. **Min Priority Queue (default order)**
   - Based on min heap
```java
package COLLECTIONS;

import java.util.Arrays;
import java.util.Comparator;
import java.util.PriorityQueue;

public class queues_collection {

       public static void main(String[] args) {

        //min priority queue used to solve problems of min heap.
        PriorityQueue<Integer> minpq = new PriorityQueue<>((a,b)->b-a);

        minpq.add(5);
        minpq.add(2);
        minpq.add(8);
        minpq.add(1);

        //lets print all the values:
        minpq.forEach(val-> System.out.println(val));

        //remove the top element from the PQ and print.
        while (!minpq.isEmpty()){
            int val =minpq.poll();
            System.out.println("Removed from top: "+val);
        }

    }
}

```
2. **Max Priority Queue**
   - Requires the use of a comparator

```java
package COLLECTIONS;

import java.util.Arrays;
import java.util.Comparator;
import java.util.PriorityQueue;

public class queues_collection {

       public static void main(String[] args) {

        //min priority queue used to solve problems of min heap.
        PriorityQueue<Integer> maxpq = new PriorityQueue<>((a,b)->b-a);

        maxpq.add(5);
        maxpq.add(2);
        maxpq.add(8);
        maxpq.add(1);

        //lets print all the values:
        minpq.forEach(val-> System.out.println(val));

        //remove the top element from the PQ and print.
        while (!minpq.isEmpty()){
            int val =minpq.poll();
            System.out.println("Removed from top: "+val);
        }

    }
}

```
Heapify is the process of arranging the elements in a heap to maintain the heap property. The heap property for a min-heap is that every parent node must be less than or equal to its child nodes, and for a max-heap, every parent node must be greater than or equal to its child nodes. Here's a simple explanation of how heapify works:

### Min-Heapify

Min-heapify ensures the smallest element is at the root. Let's look at the process with the elements [5, 8, 2, 1]:

1. **Initial Array**: Insert elements one by one.
    - Insert 5: [5]
    - Insert 8: [5, 8]
    - Insert 2: [5, 8, 2]
    - Insert 1: [5, 8, 2, 1]

2. **Heapify Steps**:
    - Start with the last inserted element (1) and compare it with its parent.
    - 1 is less than 8, so swap them: [5, 1, 2, 8]
    - 1 is less than 5, so swap them: [1, 5, 2, 8]

Final min-heap:
```
    1
   / \
  5   2
 /
8
```

### Max-Heapify

Max-heapify ensures the largest element is at the root. Let's look at the process with the elements [5, 8, 2, 1]:

1. **Initial Array**: Insert elements one by one.
    - Insert 5: [5]
    - Insert 8: [5, 8]
    - Insert 2: [5, 8, 2]
    - Insert 1: [5, 8, 2, 1]

2. **Heapify Steps**:
    - Start with the last inserted element (1) and compare it with its parent.
    - 1 is less than 8, so no swap needed.
    - 2 is less than 5, so no swap needed.
    - 8 is greater than 5, so swap them: [8, 5, 2, 1]

Final max-heap:
```
    8
   / \
  5   2
 /
1
```

### Time Complexities

- Add and offer: O(log n)
- Peek: O(1)
- Poll and Remove: O(log n)
- Remove arbitrary element (last element): O(n)

## Comparator vs Comparable

Both Comparator and Comparable provide ways to sort collections of objects. They help with sorting but don't actually perform the sort operation.

Problems that can be solved using these:
1. Primitive collection sorting
- By default this sorting in ascending order.
2. Object collection sorting
- We can't use Arrays.sort(car) on array of objects.

### Comparator

- Functional interface
- Method: `int compare(T obj1, T obj2)`
- Sorting algorithms use this compare method to decide whether to swap variables
- Return values:
  - 1: if v1 is bigger than v2
  - 0: if v1 and v2 are equal
  - -1: if v1 is smaller than v2
- Most algorithms swap values if the method returns 1
- **Purpose**: To define an external comparison logic, often for different ways to compare objects.
- **Implementation**: A separate class, or a lambda expression, or method reference implements the `Comparator` interface.
- **Method**: `compare(T o1, T o2)`
  - Compares two specified objects (`o1` and `o2`).
- **Usage**: Useful when you need multiple ways to sort objects or when the class cannot be modified.
- **Example**:

```java
  import java.util.Comparator;

  public class PersonNameComparator implements Comparator<Person> {
      @Override
      public int compare(Person p1, Person p2) {
          return p1.getName().compareTo(p2.getName()); // Ordering by name
      }
  }
```

  **Usage**:

  ```java
  List<Person> people = new ArrayList<>();
  people.add(new Person("Alice", 30));
  people.add(new Person("Bob", 25));
  people.add(new Person("Charlie", 35));

  Collections.sort(people, new PersonNameComparator()); // Sorts by name using compare method
  ```

  **Using Lambda Expression**:

  ```java
  Collections.sort(people, (p1, p2) -> p1.getName().compareTo(p2.getName()));
  ```

```java

        Integer[] arr = {17,3,4,2,1};
        //so the second parameter of the sort is comparator we pass.
        // a,b -> b-a descending order
        Arrays.sort(arr,(a,b)->b-a);
        System.out.println(Arrays.toString(arr));
```
### Comparable

- **Purpose**: To define the natural ordering of objects of a class.
- **Implementation**: The class itself implements the `Comparable` interface.
- **Method**: `compareTo(T o)`
  - Compares the current object (`this`) with the specified object (`o`).
- **Usage**: Useful when the class has a single natural sorting logic.
- **Example**:

  ```java
  public class Person implements Comparable<Person> {
      private String name;
      private int age;

      public Person(String name, int age) {
          this.name = name;
          this.age = age;
      }

      @Override
      public int compareTo(Person other) {
          return Integer.compare(this.age, other.age); // Natural ordering by age
      }
  }
  ```

  **Usage**:

  ```java
  List<Person> people = new ArrayList<>();
  people.add(new Person("Alice", 30));
  people.add(new Person("Bob", 25));
  people.add(new Person("Charlie", 35));

  Collections.sort(people); // Sorts by age using compareTo method
  ```

### Example:

```java
package COLLECTIONS;

import java.util.Arrays;

public class car {
    String carname;
    String cartype;

    car(String name,String type){
        this.carname = name;
        this.cartype = type;
    }
}

class run{
    public static void main(String[] args) {
        car[] arr = new car[3];
        arr[0] = new car("suv","petrol");
        arr[1] = new car("sedan","diesel");
        arr[2] = new car("hatch","cng");

// here we can sort based on the parameter we want using comparator.(lambda expression)
        Arrays.sort(arr,(car obj1,car obj2)->obj2.cartype.compareTo(obj1.cartype));
        for(car c:arr){
            System.out.println(c.carname+" "+c.cartype);
        }
    }
}

```


### Key Differences

1. **Location of Implementation**:
   - **Comparable**: Implemented within the class being compared.
   - **Comparator**: Implemented as a separate class or using a lambda expression.

2. **Number of Comparison Logics**:
   - **Comparable**: Defines a single natural ordering for the class.
   - **Comparator**: Allows multiple comparison strategies by defining multiple comparator classes or lambdas.

3. **Method Signature**:
   - **Comparable**: `int compareTo(T o)`
   - **Comparator**: `int compare(T o1, T o2)`

4. **Flexibility**:
   - **Comparable**: Less flexible, as it imposes a single way to compare objects.
   - **Comparator**: More flexible, as it allows different ways to compare objects without modifying the class.

### Summary

- Use **Comparable** (`compareTo` method) when a class has a natural ordering that you want to implement within the class itself.
- Use **Comparator** (`compare` method) when you want to compare objects in different ways or when you cannot modify the class.

`Comparator` is for external comparison logic and can compare any two objects, while `Comparable` is implemented within a class to compare the current object with another instance of the same class.
