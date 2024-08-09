## Collection Part 3

## Table of Contents
1. [Deque](#deque)
2. [ArrayDeque](#arraydeque)
3. [List](#list)
4. [ArrayList](#arraylist)
5. [LinkedList](#linkedlist)
6. [Vector](#vector)
7. [Stack](#stack)

## Deque

- Stands for double-ended queue, meaning addition and removal can be done from both sides of the queue.
- Methods available in Deque interface:
  - Includes methods from Collection and Queue interfaces, plus methods specific to Deque interface.  
![image](https://github.com/user-attachments/assets/34d78f00-3f28-46ff-ad36-422d318baa1e)

## ArrayDeque

- ArrayDeque (concrete class) implements the methods in Queue and Deque interfaces.
- Method mappings:
  - `add()` -> internally calls `addLast()` method
  - `offer()` -> calls `offerLast()` method
  - `poll()` -> calls `pollFirst()` method
  - `remove()` -> calls `removeFirst()` method
  - `peek()` -> calls `peekFirst()` method
  - `element()` -> calls `getFirst()` method

```java
        //ArrayDeque:
        ArrayDeque<Integer> deque = new ArrayDeque<>();
        //insertion
        deque.addLast(1);
        deque.addLast(5);
        deque.addLast(10);

        //deletion
        int ele = deque.removeFirst();

        //lifo(last in first out)
        ArrayDeque<Integer> stackDeque = new ArrayDeque<>();
        stackDeque.addFirst(1);
        stackDeque.addFirst(2);
        stackDeque.addFirst(4);

        //deletion
        int removedele = stackDeque.removeFirst();
        System.out.println(removedele);


```
### Time Complexity

- Insertion: Amortized O(1), except when queue size threshold is reached (O(n)) It copies all the elements to the new queue again.
- Deletion: O(1)
- Search: O(1)
- Space: O(n)

### Thread Safety

- ArrayDeque is not thread-safe
- For thread-safe version, use `ConcurrentLinkedDeque`

![image](https://github.com/user-attachments/assets/ae4948cb-43db-4c50-bba2-3a393dae1439)

## List

- An ordered collection of objects in which duplicate values can be stored.
- Differs from Queue:
  - In Queue, insertion/removal/access can only happen at start or end
  - In List, data can be inserted, removed, or accessed from anywhere
  - List allows user to decide where to insert or access using index (starting from 0)
  - Whereas as in Queue either you can addFirst and addLast.
### Methods

- Includes methods from Collection interface + new methods defined in List interface
![image](https://github.com/user-attachments/assets/07818c73-8a04-4187-8816-7d6c0fa5dcd8)  
![image](https://github.com/user-attachments/assets/5d353a69-4d48-4d20-979d-d1e1e639b056)  
![image](https://github.com/user-attachments/assets/4551da33-475e-49b4-8e22-85959542f584)  
![image](https://github.com/user-attachments/assets/d14fed75-de7c-4651-8961-bc6bdfbb6a62)  

## ArrayList
```java

        //ArrayList
        List<Integer> list = new ArrayList<>();

        //add(int index,element e);
        list.add(0,100);
        list.add(1,200);
        list.add(2,300);

        //addAll(int index,element e)
        List<Integer> list1 = new ArrayList<>();
        list1.add(0,400);
        list1.add(1,500);
        list1.add(2,600);

        list.addAll(2,list1);
        list.forEach((Integer val)-> System.out.println(val));

        //replaceAll(UniaryOperator op)
        list.replaceAll(val->-1*val);
        System.out.println("After replace all");
        list.forEach(val-> System.out.println(val));

        //sorting using comparator.
        list.sort((a,b)->a-b);
        System.out.println("after soring in descneind order");
        list.forEach(val-> System.out.println(val));

        //get(int index)
        System.out.println("Value present at index 2 is:"+list.get(2));

        //set(int index,element e)
        list.set(2,-4000);
        System.out.println("after set method");
        list.forEach(val-> System.out.println(val));

        //remove(int index)
        list.remove(2);
        System.out.println("after removing");
        list.forEach(val-> System.out.println(val));

        //indexOf(object o)
        System.out.println("index of -200 integer object is: "+list.indexOf(-200));
        System.out.println("index of last index of duplicate elements: "+list.lastIndexOf(-500));

        //need to provide the index in listIterator,from where it has to start
        ListIterator<Integer> listIterator = list.listIterator(list.size());

        //travesing backward direction
        while(listIterator.hasPrevious()){
            int prev = listIterator.previous();
            System.out.println("traversion backward: "+prev+" nextIndex "+listIterator.nextIndex()+" previous index "+listIterator.previousIndex());
            if(prev==-200){
                listIterator.set(-50);
            }
        }
        list1.forEach(val-> System.out.println("after set "+val));

        //traversion forward direction
        ListIterator<Integer> listIterator1 = list.listIterator();
        while(listIterator1.hasNext()){
            int next = listIterator1.next();
            System.out.println("traversion forward: "+next+" next Index "+listIterator1.nextIndex()+" prev index "+listIterator1.previousIndex());
            if(next==-200){
                listIterator1.add(-100);
            }
        }
        list.forEach(val-> System.out.println("after add "+val));

        //sublist
        List<Integer> sublist = list.subList(1,4);
        sublist.forEach(val-> System.out.println("sublist "+val));
        sublist.add(1000);
        list.forEach(val-> System.out.println("after value addded in sublist "+val));
        
```

### Thread Safety

- ArrayList is not thread-safe
- Thread-safe version of ArrayList is `CopyOnWriteArrayList<>`
![image](https://github.com/user-attachments/assets/c4215068-8f95-432b-a944-bf71116665f1)


### Time Complexity

- Insertion at end (with sufficient space): O(1)
- Insertion at specific index: O(n)
- Insertion when array size threshold reached: O(n)
- Deletion: O(n)
- Search: O(1)
- Space: O(n)

## LinkedList

- Implements both List and Deque interfaces
- Supports Deque methods like `getFirst()`, `getLast()`, `removeFirst()`, `removeLast()`, etc.
- Supports index-based operations like `get(index)`, `add(index, object)`, etc.
- Not thread-safe
- Thread-safe version is `CopyOnWriteArrayList<>`
![image](https://github.com/user-attachments/assets/957deee0-2447-4f79-98c6-3bc166ed6721)

```java
//linked list example:
        LinkedList<Integer> linkedlist = new LinkedList<>();

        //using deque fucnitonalites
        linkedlist.addLast(200);
        linkedlist.addLast(300);
        linkedlist.addLast(100);
        linkedlist.addLast(400);
        System.out.println(linkedlist.getFirst());

        //using list funcitnaliets:
        LinkedList<Integer> linkedList1 = new LinkedList<>();
        linkedList1.add(0,100);
        linkedList1.add(1,200);
        linkedList1.add(2,300);
        linkedList1.add(1,400);
        System.out.println(linkedList1.get(1)+" and "+linkedList1.get(2));

```
## Vector

- Similar to ArrayList, elements can be accessed via index
- Thread-safe
- Puts lock when operation is performed on vector
- Less efficient than ArrayList due to internal lock/unlock for each operation
![image](https://github.com/user-attachments/assets/2dbb66b3-78da-44b2-a03d-52ea4d1e8b65)

## Stack

- Represents LIFO (Last In, First Out) operation
- Extends Vector, so its methods are also synchronized
- Different from Deque: Stack is thread-safe, while Deque is not
![image](https://github.com/user-attachments/assets/ab34bb64-06e9-40ec-af36-efbed04dc7c8)

### Time Complexity
- Insertion - O(1)
- Deletion - O(1)
- Search - O(n)
- Space - O(n)
