# Generics in Java

## 1. Generics in Java
Generics allow types (classes and interfaces) to be parameters when defining classes, interfaces, and methods. This provides a way to re-use the same code with different inputs while maintaining type safety.

## 2. Java Generic Class
A generic class is defined with the following syntax:

```java
public class Box<T> {
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
```
### Explanation:
- `T` is a type parameter that will be replaced by a real type when the class is instantiated.
- This allows for type-safe collections and avoids casting.

## 3. Java Generic Interface
A generic interface is defined similarly to a generic class:

```java
public interface Container<T> {
    void add(T item);
    T get(int index);
}
```
### Explanation:
- `T` is a type parameter, making the interface generic and reusable for different types.

## 4. Java Generic Type
Generic types enable classes, interfaces, and methods to operate on objects of various types while providing compile-time type safety.

## 5. Java Generic Method
A generic method is a method that introduces its own type parameters:

```java
public <T> void printArray(T[] array) {
    for (T element : array) {
        System.out.println(element);
    }
}
```
### Explanation:
- `<T>` before the return type indicates that the method is generic.
- `T` can be replaced with any type when the method is called.

## 6. Java Generics Bounded Type Parameters
Bounded type parameters restrict the types that can be used as type arguments:

```java
public <T extends Number> void printArray(T[] array) {
    for (T element : array) {
        System.out.println(element);
    }
}
```
### Explanation:
- `T extends Number` means that `T` must be a subclass of `Number`.

## 7. Java Generics and Inheritance
Generics and inheritance allow for more flexible and reusable code:

### Example: Simple Inheritance with Generics

```java
class Animal {
    public void eat() {
        System.out.println("Animal is eating");
    }
}

class Dog extends Animal {
    public void bark() {
        System.out.println("Dog is barking");
    }
}

class GenericBox<T> {
    private T t;

    public void set(T t) { this.t = t; }
    public T get() { return t; }
}

public class Main {
    public static void main(String[] args) {
        GenericBox<Animal> animalBox = new GenericBox<>();
        animalBox.set(new Dog());  // This works because Dog is an Animal
        animalBox.get().eat();     // Outputs: Animal is eating
        
        // animalBox.get().bark(); // Compilation error: Cannot find method bark in Animal

        // To use Dog-specific methods, we need to cast
        Dog dog = (Dog) animalBox.get();
        dog.bark();                // Outputs: Dog is barking
    }
}
```
### Explanation:
- `GenericBox<Animal>` can hold any object of type `Animal` or its subclasses, like `Dog`.
- Direct access to subclass-specific methods requires casting.

## 8. Java Generic Classes and Subtyping
Generic classes support subtyping:

### Example: Generic Classes and Subtyping

```java
class Parent<T> {
    private T value;

    public void setValue(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}

class Child<T> extends Parent<T> {
    // Child inherits everything from Parent
}

public class Main {
    public static void main(String[] args) {
        Parent<Number> parent = new Parent<>();
        parent.setValue(123);      // Works fine
        Number number = parent.getValue();
        System.out.println(number); // Outputs: 123

        Child<Integer> child = new Child<>();
        child.setValue(456);       // Works fine
        Integer integer = child.getValue();
        System.out.println(integer); // Outputs: 456
    }
}
```

### Explanation:
- `Parent<T>` is a generic class.
- `Child<T>` inherits from `Parent<T>`, meaning it is also generic.
- Instances of `Child<T>` can hold values of type `T`.

### Example: Subtyping with Wildcards

```java
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<? extends Number> numberList = new ArrayList<Integer>(); // Covariant

        // numberList.add(123); // Compilation error: Cannot add to a list with ? extends Number
        Number number = numberList.get(0); // Reading is allowed

        List<? super Integer> integerList = new ArrayList<Number>(); // Contravariant

        integerList.add(123); // Adding is allowed
        // Integer integer = integerList.get(0); // Compilation error: Cannot assign to Integer
        Object obj = integerList.get(0); // Reading is allowed as Object
    }
}
```

### Explanation:
- `List<? extends Number>` allows for reading `Number` types but not adding elements.
- `List<? super Integer>` allows for adding `Integer` types but restricts reading to `Object` types.

## 9. Java Generics Wildcards
Wildcards allow a type parameter to be more flexible:

```java
public void process(List<?> list) {
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```
### Explanation:
- `?` represents an unknown type.

## 10. Java Generics Upper Bounded Wildcard
Upper bounded wildcards restrict the unknown type to a specific range:

```java
public void process(List<? extends Number> list) {
    for (Number num : list) {
        System.out.println(num);
    }
}
```
### Explanation:
- `? extends Number` means any type that is a subclass of `Number`.

## 11. Java Generics Unbounded Wildcard
Unbounded wildcards allow any type:

```java
public void process(List<?> list) {
    for (Object obj : list) {
        System.out.println(obj);
    }
}
```
### Explanation:
- `?` represents any type.

## 12. Java Generics Lower Bounded Wildcard
Lower bounded wildcards restrict the unknown type to a specific range:

```java
public void addNumbers(List<? super Integer> list) {
    list.add(new Integer(50));
}
```
### Explanation:
- `? super Integer` means any type that is a superclass of `Integer`.

## 13. Subtyping using Generics Wildcard
Subtyping with wildcards allows more flexible method signatures:

```java
List<? extends Number> list = new ArrayList<Integer>();
```
### Explanation:
- A list of a subtype of `Number` can be assigned to a list with an upper bounded wildcard.

## 14. Java Generics Type Erasure
Type erasure is a process where type parameters are removed by the compiler to ensure backward compatibility with older versions of Java:

```java
public class Box<T> {
    private T t;
    public void set(T t) { this.t = t; }
    public T get() { return t; }
}
```
### Explanation:
- At runtime, `Box<T>` is treated as `Box` with type information removed.

## Additional Concepts

### Covariance and Contravariance
Covariance and contravariance describe how subtyping between more complex types relates to subtyping between their component types.

#### Covariance
- Covariant types preserve the subtype relationship.
- In Java, this is expressed with `? extends T`.

```java
List<? extends Number> numbers = new ArrayList<Integer>();
```
### Explanation:
- You can read items from a covariant type, but you cannot add items to it (except `null`).

#### Contravariance
- Contravariant types reverse the subtype relationship.
- In Java, this is expressed with `? super T`.

```java
List<? super Integer> integers = new ArrayList<Number>();
```
### Explanation:
- You can add items to a contravariant type, but you cannot read items from it as a specific type (except `Object`).

