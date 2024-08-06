# Java Collections Framework

## Table of Contents
1. [Introduction](#introduction)
   - [What is a Collection Framework?](#what-is-a-collection-framework)
   - [Why do we need Java Collections Framework?](#why-do-we-need-java-collections-framework)
2. [Java Collections Hierarchy](#java-collections-hierarchy)
3. [Key Interfaces](#key-interfaces)
   - [Iterable](#iterable)
   - [Collection](#collection)
4. [Collection vs Collections](#collection-vs-collections)

# Java Collections Framework

## 1. Introduction
### 1.1 What is a Collection Framework?
- Added in Java version 1.2
- Collections as groups of objects
- Located in java.util package
- Framework provides architecture for managing object groups

### 1.2 Why do we need Java Collections Framework?
- Prior solutions: Array, Vector, HashTable
- Problem: No common interface, difficult to remember methods for every type.

## 2. Java Collections Hierarchy
![image](https://github.com/user-attachments/assets/541c9f52-43f5-4be2-b702-5b1ead667b1b)

- This map interface and its childern are different from collection.
![image](https://github.com/user-attachments/assets/623cecd6-bc96-48b9-8d8e-13f63c2ce54b)

## 3. Key Interfaces
### 3.1 Iterable
- Used to traverse the collection
- Methods:
  - iterator()
  - forEach()
![image](https://github.com/user-attachments/assets/5c02ca89-e2f3-443e-a812-60fa07edffd6)

```java
package COLLECTIONS;

import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class iterable {

    public static void main(String[] args) {

        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);

        //using iterator
        System.out.println("Iterating the values using iterator method");
        Iterator<Integer> valueIterator = list.iterator();
        while (valueIterator.hasNext()){
            int val = valueIterator.next();
            System.out.println(val);
            if(val==3){
                //this removes the value of the last where the iterator points is from.
                valueIterator.remove();
            }
        }

        System.out.println("Iterating values using for-each loop");
        for(int val:list){
            System.out.println(val);
        }

        //using forEach Method
        System.out.println("Tesing forEach method");
        list.forEach((Integer val)-> System.out.println(val));


    }
}

```
### 3.2 Collection
- It repesents the group of objects,its an interface which provides methods to work on group of objects.
- Below are the most common used methods which are impelmented by its child classes like ArrayList,Stack,LinkedList etc
```java
package COLLECTIONS;

import java.util.ArrayList;
import java.util.List;
import java.util.Stack;

public class collectionEx {

    public static void main(String[] args) {

        List<Integer> values = new ArrayList<>();
        values.add(2);
        values.add(3);
        values.add(4);

        //size
        System.out.println("size "+values.size());
        //isEmpty
        System.out.println("isEmpty "+values.isEmpty());
        //contains
        System.out.println("Contains"+values.contains(5));
        //add
        values.add(5);
        System.out.println("added "+values.contains(5));
        //remove using index
        values.remove(3);
        System.out.println("removed using index "+values.contains(5));
        //remove using ojbect,removes the first occurence of the value.
        values.remove(Integer.valueOf(3));
        System.out.println("removed using ojbect "+values.contains(3));

        Stack<Integer> stack = new Stack<>();
        stack.add(6);
        stack.add(7);
        stack.add(8);

        //addAll
        values.addAll(stack);
        System.out.println("addAll test using containsAll "+values.containsAll(stack));

        //containsAll
        values.remove(Integer.valueOf(7));
        System.out.println("containsAll after removing one element: "+values.containsAll(stack));

        //removeAll
        values.removeAll(stack);
        System.out.println("removeAll "+values.contains(8));

        //clear
        values.clear();
        System.out.println("clear "+values.isEmpty());
    }
}

```
## 4. Collection vs Collections
Collection:
- It is a part of Java Collection Framework.And its an interface,which expose various methods which is implemented by various collections classes like ArrayList,Stack,LinkedList etc.
- It's an interface in Java
- Part of the java.util package
- Represents a group of objects, known as elements
- Defines basic operations for working with groups of objects

Collections:
- It is a utility class provides static methods
- Which are used to operate on collections like sorting,swapping,searching,reverse,copy etc.
- It's a utility class in Java
- Also part of the java.util package
- Contains static methods for operating on or returning collections
- Provides algorithms for working with collections, like sorting or searching

- Some collections methods(static):  
![image](https://github.com/user-attachments/assets/a08d2108-22a8-4c2a-8e1e-2e8ec430c025)

```java
package COLLECTIONS;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

//Collections Example that differentiates the collection.
public class collectionsEx {

    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>();
        list.add(1);
        list.add(2);
        list.add(3);
        list.add(4);

        System.out.println("max value: "+ Collections.max(list));
        System.out.println("min value: "+Collections.min(list));
        Collections.sort(list);
        System.out.println("Sorted");
        list.forEach(val -> System.out.println(val));
    }
}

```
