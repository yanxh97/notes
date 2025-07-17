# SOLID Principle

Why SOLID?  Loose coupling, stable, robust, flexible, sustainable, manageable, efficient.

- Single Responsibility -- _"There should never be more than one reason for a class to change."_

- Open for Extension, Closed for Modification -- _"Software entities should be open for extension, but closed for modification."_

- Liskov Substitution -- every subclass or derived class should be substitutable for their base or parent class -- _"If S is a subtype of T, then objects of type T may be replaced with objects of type S without altering any of the desirable properties of the program."_

- Interface Segregation -- _"Many client-specific interfaces are better than one general-purpose interface."_

- Dependency Inversion -- _"Depend upon abstractions, not concretions."_


# Day 1

## OOP

### What is an object

A data field that has unique attributes and behaviors.
State: attributes, variables
Behaviors: functions methods

### Polymorphism
- Polymorphism: multiple forms, same **method** can have different ways to perform
- Overloading: compile time
	- The same method name, but different variables: such as the number of variables, the types
- Overriding: runtime
	- It happens in a child class. Child class would like to inherit some superclass's methods, you can implement it in a different way
	- Must have larger(looser) access modifier  (except private, cannot be inherited, thus no restriction)
- Can we override a data member in class? **NO** 

## Encapsulation 
- private String str
- Use getter to get the current value, setter method to reset or update your value. 
- Why we need to use this? We always need to protect data

### Abstraction: ``abstract`` vs ``interface``

Abstract class
- An abstract class is a class that **cannot** be instantiated, it serves as a base class for subclass
- It can have both abstract and non-abstract method
- It can have constructor
- A class can extend **only one** abstract class
- Methods and properties can have any access modifier(public, protected, private)
- Can have member variables (final, non-final, static, non-static)

Interface
- Specifies a set of methods a class must implement
- methods are abstract by default. (static, default allowed in java 8, see below)
- You are not allowed to have constructor for an interface
- A subclass can implement more than one interface
- Methods and properties are implicitly public
- Variables are implicitly public, static and final

- You can have **default** (a default method implementation for any class that implement this interface, in case of diamond problem, i.e. multiple inheritance, one should explicitly provide an implementation for the method) and **static** (called by using the interface name preceding the method name) **method** after java 8
- Private method is allowed after java 9

- ``default`` keyword: switch - case, annotations, default methods of interface (Java 8)

Why inheritance? For code reusability

---
## Java Types

### Primitive Types vs Wrapper Types

Wrapper type has more functions you can use.  

| primitive type | default value | length    |
| -------------- | ------------- | --------- |
| boolean        | false         | 1 bit     |
| int            | 0             | 4 byte??? |
| short          | 0             | 2 byte??? |
| long           | 0L            | 8 byte    |
| double         | .0            | 8 byte    |
| float          | .0f           | 4 byte    |
| char           | '\u0000'      | 2 byte??? |
Wrapper type
Watch out the range of wrapper type. ``Integer`` range [-128, 127] would be cached so ``i1 == i2`` would return true, otherwise won't.  
```java
Integer i1 = 127;
Integer i2 = 127;
System.out.println(i1 == i2); // true

Integer i3 = 128;
Integer i4 = 128;
System.out.println(i3 == i4); //false

String s1 = "abc";
String s2 = "abc";
System.out.println(s1 == s2); //true because of string constant pool in heap

String s3 = "abc";
String s4 = new String("abc");
System.out.println(s3 == s4); //false created in the heap but outside SCP
System.out.println(s3 == s4.intern()); //true
```

### String, StringBuilder, StringBuffer

- String class is immutable class, which means you cannot change value of string later  
- StringBuilder allows you to modify value of string, used when we concatenate strings with ``+``
- StringBuffer is synchronized

### Access Modifiers

Access modifiers are used for setting the access level to classes, variables, methods, and constructors.

| access modifiers | class | package | subclass | world |
| ---------------- | ----- | ------- | -------- | ----- |
| ``public``       | Y     | Y       | Y        | Y     |
| ``protected``    | Y     | Y       | Y        | N     |
| (default)        | Y     | Y       | N        | N     |
| ``private``      | Y     | N       | N        | N     |

 ``final`` - apply restrictions on classes, methods, and variables. It ensures that once a **variable** is assigned, it **cannot be changed**, that **a method cannot be overridden**, and that **a class cannot be subclassed**. A reference is assigned, it cannot be changed, but the object it is referring to may experience some inner state change, i.e. ``final List<Integer>``

### ``final`` 

final variables -- can only be initialized once. cannot be reassigned.

***local variable*** can be initialized after declared, before use, and before block/method is finished. But only once.
***instance member variable (non-static)*** can be initialized either during declaration or in all the constructors.   (or in a instance initializer block)
***class static variable*** must be initialized when declared. (or in a static initializer block), final static also called "constant"
***reference*** the inner state of the object may change, but the reference variable cannot be reassigned

final methods -- prevent from overriding

final classes -- prevent from inheriting

final parameters -- prevent from reassigning

 
 Usage - Can be used to describe class variables, class methods, classes, and parameters in method.

``static`` - called by using the interface name preceding the method/variable name
Usage - variables, methods, blocks, nested classes

### Java Language Specification

For field modifiers
- Annotation
- public/protected/private
- static
- final
- transient
- volatile

For class modifiers
- Annotation
- public/protected/private
- abstract
- static
- final
- strictfp

For method modifiers
- Annotation
- public/protected/private
- abstract
- static
- final
- synchronized
- native
- strictfp

### Pass by values or reference?

Answer: 
Values (of reference). Primitive type being passed by actual values. 

### Shallow & Deep Copy 

- shallow copy: copies the primitive fields directly; but for member non-primitive variables, both original object and copied object point to **the same address**. It's sufficient if the copied object only contains primitive fields and immutable objects.
- deep copy: original (mutable) object and copied object point to the **different address/references**
- A deep copy is an alternative that **each mutable object in the object graph is recursively copied**.
- Cloneable interface
```java
class Address { 
	private String street; 
	private String city; 
	private String country; 
	
	// standard constructors, getters and setters 
}
class User { 
	private String firstName; 
	private String lastName; 
	private Address address; 
	
	// standard constructors, getters and setters 
}
```

One way is copy constructor

```java
public Address(Address that) { 
	this(that.getStreet(), that.getCity(), that.getCountry()); 
}

public User(User that) { 
	this(that.getFirstName(), that.getLastName(), new Address(that.getAddress()));
}
```

Another way is override the ``clone`` method inherited from ``Object``. It's ``protected`` but we need to override it with ``public``. 
We also add a marker interface ``Cloneable``,  to the class to indicate that the classes are actually cloneable. 
```java
class Address implement Cloneable {}

...
// In Address class
@Override 
public Object clone() { 
	try { 
		return (Address) super.clone(); 
	} catch (CloneNotSupportedException e) { 
		return new Address(this.street, this.getCity(), this.getCountry()); 
	} 
}

// In User class
@Override 
public Object clone() { 
	User user = null; 
	try { 
		user = (User) super.clone(); 
	} catch (CloneNotSupportedException e) { 
		user = new User( this.getFirstName(), this.getLastName(), this.getAddress()); 
	} 
	user.address = (Address) this.address.clone(); 
	return user; 
}
```


--- 

# Day 2

### ``this`` vs ``super``

``this`` points to current object. There are 4 ways to use ``this`` keyword
- reference of current object
- ``this()`` to call constructor, must be **the first statement in a constructor body**
- ``this`` keyword can be used as an parameter
- as a constructor parameter  
- Chaining function ``return this``

``super`` 
- refer to current object as a parent object
- call parent method
- call parent constructor, must be **the first statement in a constructor body**

### Exceptions

exceptions: thrown expected errors.
errors vs exceptions
- errors are something not expected
- exceptions are something expected

checked exceptions: compile time
IOException, SQLExceptions

unchecked exceptions: runtime exceptions
indexOutOfBound, nullPointerException

errors
outOfMemory

Dealing with exceptions
- ``throw someException;`` 
- ``void methodName() throws someExceptions``
- ``try catch finally`` block, finally is executed before throwing runtime exceptions

### ``static``
- static methods/variables belong to the class, not some instance
- non-static method cannot be called (directly, without instance) inside a static method

## Java 8 Collections

### ``ArrayList``

1.  To access one element by using indexing -> why we can do this?
	**Because ArrayList implement RandomAccess interface to allow you access or get any random element at the same speed -> O(1)**
2. **Default capacity = 10**
3. ArrayList is using array ``Object[]`` to store elements
4. modCount -> for multi threading environment, throw concurrentModificationException
5. oldCapacity >> 1   right shift operation   0010 -> 2 -> 0010 >> 1 -> 1
	**new capacity = old + 0.5 * old capacity = 1.5 capacity**
6. time complexity in array list:
        add(1) -> O(1) in the best case, O(N) -> when -> when array list has to resize
        add(index, element) -> O(N)
		get() -> always O(1)
		remove() -> O(N)
		indexof() -> O(N)
	    contains() -> O(N)

## ``Stack`` & ``Vector``
1. stack grow method: new cap =
	old cap + capacityIncrement
	2 * old cap
2. time complexity:
	 stack push O(1) pop O(1) size() O(1)
	 vector: add remove, size() -> O(N?1?)

### Synchronized List
how can you make general arraylist to synchronized?
```java
List<String> myGeneralList = new ArrayList<>();
List<String> list = Collections.synchronizedList(myGeneralList);
```



### Iterator

- iterator check modCount therefore may throw ConcurrentModificationException
- basic usage, like linked list with head pointer
```java
Iterator<T> it = LinkedList.iterator();
while(it.hasNext()){
	System.out.println((T)it.next);
}
```

### LinkedList

- Does not implement RandomAccess, get() complexity O(N)
- addFirst(), addLast() the same as linkFirst(), linkLast()
- time complexity:
	add() -> O(1)
	add(index, element) -> O(N)
	get() O(N)
	remove O(N)

### Deque & LinkedList

- FILO and FIFO
- offerFirst() -> addFirst() = push() -> linkFirst()
- basically no difference, offer is preferred when size-restrited
- getFirst() throws exception, peekFirst() does not but return null
- pollFirst() -> unlinkFisrt() <- removeFirst() = pop()
- removeFirst() throws exception, pollFirst() does not but return null 

### PriorityQueue

- default **min** heap, in contrast, C++ max heap
- Use array of Object to store data
- parentIndex is given, 
	- leftIndexNo = parent index * 2 + 1
	- rightIndexNo = parent index * 2 + 2
	- parentNode = (node index - 1) / 2
- siftup function
```java

PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

PriorityQueue<Integer> maxHeap = new PriorityQueue<>(new Comparator<Node>(){
	@override
	public int compare(Node n1, Node n2){
		// normally, min-heap -> n1-n2; max-heap -> n2- n1
	}
});

}
```


### HashMap

- before java 8 hashmap use linked list to store node
- after java 8 hashmap use linked list + red - black tree
- when hashmap start to transfer from linked list to red -black tree
	- the linked list size is greater than 8
- when does hashmap start to transfer red-black tree to linked list
	- the number of elements  in tree are less than 6
- how does hashmap put() works?
1: calculate hash value
```java
static final int hash(Object key) {
	int h;
	return (key == null) ? 0 : (h = key.hashCode()) ^ (h >>> 16);
}
```

h = key.hashCode(): 
1111 1111 1111 1111 1111 0000 1110 1010 -> random value
0000 0000 0000 0000 1111 1111 1111 1111
1111 1111 1111 1111 0000 1111 0001 0101 - > (h = key.hashCode()) ^ (h >>> 16)

( ``p = tab[i = (n - 1) & hash]`` where "hash" = (h = key.hashCode()) ^ (h >>> 16)
1111 1111 1111 1111 0000 1111 0001 0101
0000 0000 0000 0000 0000 0000 0000 1111
0000 0000 0000 0000 0000 0000 0000 0101 = 5 -> index


2: if there is null, tab[5] == null -> means that it is empty and no hash collision, then just store current value

3: if there has an element or exits hash collision. hashmap will do the following steps
	3.1: IF hashmap is using red-black tree, then call red-black tree insertion to put value
	3.2: if hashmap is not using red-black tree( using linked list), then put value. After inserting new node into tab table, hashmap will check the size, if the size> threshold then resize to red-black tree.

elements: ``Map.Entry<K,V>``
default cap = 16
new cap = old * 2

 average case in hashmap:
 *  insertion: put() : O(1)
 *  delete: remove(): O(1)
 *  search: get(): O(1)
 
 worst case in hashmap:
 *  insertion: O(N) ->
 *  delete:O(N)
 *  search:O(N)

 *  treeMap -> sorted

 *  hash set -> unique values

 *  tree set -> sorted hash set
 
### hashset vs tree set
 can we have **null value** in tree set and hashset?
 *  hashset allows you to insert null value, whereas treeset you cann't have null value

---

# Day 3

## Java 8 new features

### Lambdas

A lambda is an anonymous function that we can handle as a first-class language citizen. We can pass it to or return it from a method.

Do not use ``this`` in Lambda, scope problem. It's not inner class.

Keep it short and self-explanatory.
	- Avoid blocks of code in Lambda's body
	- Avoid specifying parameter type - type inference
	- Avoid parenthesis around a single parameter, but not in case of no or multiple parameters
	- Avoid return statement and braces
	- Use method reference like ``String::toLowerCase``, can be non-static

Be careful with mutable objects in the scope.

```java
// Before Java 8
new Thread(new Runnable(){
	@override
	public void run(){
		System.out.print("I am creating a new thread");
	}
}).start();

// After Java 8
new Thread(() -> System.out.print("I am creating a new thread")).start();

// -> double all elements in this arraylist using labmda function
List<Integer> number = Arrays.asList(1,2,4,5,6,7);
number.forEach( n -> {
	int x = n * 2;
	System.out.println(x);
});

PriorityQueue<Integer> pq = new PriorityQueue<>((a,b) -> b -a);

List<String> str = Arrays.asList("java","Spring", "Kotlin", "Scala");
Collections.sort(str, (String a, String b) -> a.compareTo(b));
Collections.sort(str, String::compareTo); // <---- method reference here


```

### Functional Interface

Functional interface has **only one abstract method** and it has ``@functinalInterface`` annotation

Functional interface can have multiple default or static methods (new feature of Java 8) but be careful with diamond problem.

Functional Interface is usually instantiated with lambda expression.

Use Function for one-input-one-output functional interface.
```java
import java.util.function.*

// Prefer not to use
@FunctionalInterface
public interface Foo{
	String method(String string);
}

Foo foo = par -> par + " from lambda";
String res = foo.method("Message")l
// Message from lambda

// Instead, use Function class
Function<String, String> fn = par -> par + "from Lambda";
String res = foo.method("Message");

```

Avoid overloading methods with functional interfaces as parameters, i.e. compiler cannot distinguish ``Callabel<T>`` and ``Supplier<T>`` without typecast

```java
public static interface Processor {  
    String process(Callable<String> c) throws Exception;  
    String process(Supplier<String> s);  
}  
  
public static class ProcessorImpl implements Processor {  
    @Override  
    public String process(Callable<String> c) throws Exception {  
        // implementation details  
        return c.call() + "c";  
    }  
  
    @Override  
    public String process(Supplier<String> s) {  
        // implementation details  
        return s.get() + "s";  
    }  
}

// Error: ambiguous match
String result = processor.process(() -> "abc");
// type cast
String result = processor.process((Supplier<String>)() -> "abc");
```


Example: ``runnable, callable, comparator, consumer, Function class ``(apply() method) ``,predicate, supplier``


## ``stream`` API

``stream`` API allows you to perform some operations on java collections like ArrayList.

It has two types:  **intermediate** function and **terminal** function

```java

// stream api
// select stream start with "a"
List<String> list = Arrays.asList("a1", "a2", "b1", "a3", "d4", "A5", "Acd123");  
list.stream()  
        .filter(s -> s.startsWith("a"))  
        .forEach(System.out::println);

list.stream()
	.filter(s -> s.startsWith("a"))
	.map(String::toUpperCase)
	.forEach(System.out::println);

List<String> newList = list.stream()
	.filter(s -> s.chars().anyMatch(Charactor::isUpperCase))
	.collect(Collecttors.toList());

System.out.println(newList);

list.stream()  
	// REGEX
	.filter(s -> s.matches(".*\\d.*"))  
	.filter(s -> s.chars().anyMatch(Character::isLowerCase))  
	.forEach(System.out::println);

// use of reduce
List<Integer> nums = Arrays.asList(1,2,3,4,5);  
int sum = nums.stream()  
        .filter(i -> i %2 == 0)  
        .reduce(0, Integer::sum);  
System.out.println(sum);

int max = nums.stream()
	// without initVal, return Optional<T>
	.reduce(Integer::max)
	.get();  
System.out.println(max);

```

***Intermediate***
- filter
- map
- flatMap
- distinct
- sorted 
- peek(Consumer action) forEach but not terminate
- limit(n) truncate the elements after the n-th element
- skip(n) skipping the first n elements

***Terminal***
- forEach(consumer)
- reduce([init, ] binary_opration)
- collect(Collectors.toList())
- anyMatch(Predicate)
- allMatch(Predicate)
- noneMatch(Predicate)
- count()
- min()
- max()

### Stream API vs Collections

- collections are data structures that **store** values, whereas stream API does not store values
- collections can be modified by adding or removing elements, whereas stream API does **NOT modify** the original data -> create a stream to do operations
- collections are useful for storing and organizing data, whereas stream API is useful for processing data



---

# Day 4

## Method Reference

- **reference to static method**: ``ContainingClass::staticMethodName``
- **reference to method of an arbitrary object of a particular  type**: ``ContainingType::methodName``
- **reference to method of a particular object**:  ``objectName::methodName``
- **reference to constructor of a class**: ``ClassName::new``
```java
// static method
char[] carr = {'a','b'};  
Function<char[], String> mr = String::valueOf;  
System.out.println(mr.apply(carr));

// method of an arbitrary object of a particular type
String str = "abc";  
Function<String, Integer> mr1 = String::length;
System.out.println(mr1.apply(str));

// method of an instance
Supplier<Integer> mr2 = str::length;
System.out.println(mr2.get());

// constructor
Function<char[], String> mr3 = String::new;  
char[] c = {'a','b','c'};  
System.out.println(mr3.apply(c));

List<Integer> l = Arrays.asList(1, 2, 3, 4);
// List implement iterable interface, 
l.forEach(System.out::println);

```

## Optional

How to avoid null pointer exception in java? -- Optional box is the best practice.

```java
Optional<String> password = account.getPassword();

// protected from null pointer
password = Optional.empty();

// Check the value is
password.isPresent();

// if null, return the value in orElse, if not null, return the original
password.getPassword().orElse("Default password");

// using a lambda function, throw exception if supplying function is null
password.getPassword().orElseGet(Supplier<T> other);

// throw exception
password.getPassword().orElseThrow(new RuntimeException("User does not have pw."));
```

## Completable Future

Perform asynchronous computations -> to build responsive application

Using with thread pool. Why?
-  Java create/destroy a thread -> cost a lot CPU resources
-  Can we have a pool keep thread(s) alive? - then you can use thread pool to speed up app.
-  Why speed up? Because you don't need to create/destroy threads

In real project, we don't use ``Executors.newFixedThreadPool(n)``, we use ``new ThreadPoolExecutor(n, n, 0, TimeUnit,MILLISECONDS, new LinkedBlockingQueue<Runnable>());`` to customize our thread pool. The queue size would be very large if we call ``Executors.newFixedThreadPool(n)``, but real project, we often have size 100 or so.  

```java

long startTime = System.currentTimeMillis();

// There are numerous types of pools in Executors class
ExecutorService threadpool  = Executors.newFixedThreadPool(3);

FutureTask<String> futureTask1 = new FutureTask<>( ()->{
	try{
		// send an email to IT team
		TimeUnit.MILLISECONDS.sleep(500);
	}catch (InterruptedException e){
		e.printStackTrace();
	}
	return "IT Team receive email";
});
// We are not using
// Thread t1 = new Thread(futureTask1, "task 1: IT");
// t1.start();
threadpool.submit(futureTask1);
threadpool.submit(futureTask2);
threadpool.submit(futureTask3);
try{
	futureTask1.get();
	futureTask2.get();
	futureTask3.get();
}catch (Exception e){
	e.printStackTrace();
}
long endTime = System.currentTimeMillis();
System.out.println("cost time :  " + (endTime - startTime));
// Don't forget to shutdown
threadpool.shutdown();

```

Consumer ``accept``, optionally ``andThen``, Supplier ``get``.  

Completable Future has **more** functions than future task.

``runAsync`` return ``CompletableFuture<void>``
``supplyAsync`` return ``CompletableFuture<T>``
``get()`` throws exception in its definition, forcing the caller to handle it, but ``join()`` does not
You can specify the executor as the second parameter, or it would use forkJoinPool

``whenComplete`` would return another completable future.

```java

CompletableFuture<Void> completableFuture1 = CompletableFuture.runAsync(()->{
	System.out.println(Thread.currentThread().getName());
	try{
		TimeUnit.SECONDS.sleep(1);
	}catch (InterruptedException e){
		e.printStackTrace();
	}
});
System.out.println(completableFuture1.get());

ExecutorService executorService3 = Executors.newFixedThreadPool(3);
CompletableFuture<String> completableFuture4 = CompletableFuture.supplyAsync(()->{
	System.out.println(Thread.currentThread().getName());
	try{
		TimeUnit.SECONDS.sleep(1);
	}catch (InterruptedException e){
		e.printStackTrace();
	}
	// int i=1/0;
	return "Hello, I am supply Async with executors";
},executorService3).whenComplete((r,e)->{
	System.out.println(" task is finished");
}).exceptionally(e->{
	e.printStackTrace();
	System.out.println("exception is happen");
	return null;
});
System.out.println(completableFuture4.join());
executorService3.shutdown();

```


``thenApply``, ``thenRun`` , ``thenAccept``

```java
// then apply example

ExecutorService executorService4 = Executors.newFixedThreadPool(3);
CompletableFuture<Integer> completableFuture5 = CompletableFuture.supplyAsync(()->{
		System.out.println(Thread.currentThread().getName());
		try{
			TimeUnit.SECONDS.sleep(1);
		}catch (InterruptedException e){
			e.printStackTrace();
		}
		return 3;
	},executorService4).thenApply( r->{// this "r" will take result form the top,
		System.out.println("this is step 2 and compute 3");
		return r + 1;
	}).thenApply( r->{ // this "r" will take result from r + 1
		System.out.println("this is step 3");
		return r + 2;
	}).whenComplete((r,e) ->{
		if(e == null){
			System.out.println("the result is " + r);
		}
	});
	System.out.println(completableFuture5.join());
	executorService4.shutdown();
}
```


---

# Day 5

## JVM

Java virtual machine, allowing you to run a Java program.

.java file --- Java compiler ---> .class bytecode ---JVM ---> running program

JVM  ---> class loader <---> runtime data area <---> execution engine

- class loader: will load .class file into your JVM
- runtime data areas: memory -> bytecode, objects, local variables, return values...
- execution engine: interpreting and executing your bytecode: .class file. -> also handle memory management and GC

sub component of runtime area

**stack**
- It used to store data for method invocation: method arguments, return value. local variables -> this allow us to track the execution of method invocation
- this area is not shared
- Error: stackOverFlowError:

**memory areas**
- In memory areas, we could find method data, .class file, method definition, constant, state variable.
- This area is shared, which means other thread can access

**heap**
Three important components
1. eden space (young generation): this place is where you store the new objects (very big object directly goes to old generation)
2. survival space (young generation): S0, S1 -> store live objects after a GC
3. old generation: store live objects after multiple GC
4. metaspace: information about class file

GS setting: run-config to change setting.  Program running slow- first check code, then consider GC

How can we define a live object?
Old way: reference counting method. Big problem when we do **cycle** referencing.
``A.instance = B.instance; B.instance = A.instance`` won't delete in GC.

### GC roots method

It is an object in Java, which can be
- local variable
- static variable
- thread stack
- classes, and class loaders

For every reference it makes, reach out a branch to a new node.

Now heap consists of a lot of graph

We define **live objects** as object  that we can reach from a GC root.

## Garbage Collections

STW: stop the world, stop the application, focusing on only GC task

Better GC reduce STW, and improve performance of program.

IN CMS,  GC on young gen -- minor GC,  GC on all gen -- full GC.

1. Mark-sweep algorithm - normal deletion, fragments of free memory

2. Mark-sweep-compact algorithm - organize your heap after mark and deletion, allow large object

3. Concurrent mark and sweep, CMS -> reducing number of STW
	- initial mark, STW -- identify the gc root set
	- *concurrent* mark, no STW -- while the application is running, the CMS algorithm continues the marking process by tracing the object from GC roots set
	- remark, STW -- remarking all object again, in case new gc roots appeared just now
	- *concurrent* sweep - reset -- reclaim memory from dead objects and prepare for the next cycle

4. G1 -- garbage first, useful with system that is predictable with STW. Memory organization looks like a colored grid. While in CMS, looks like   |    eden   | sur0 | sur1 |    old      |
	- Initial mark -- mark the live object in the **old generation** -> this phase is done concurrently with the application threads. You have minimal impact on app performance.
	- Concurrent marking -- mark more live object in **all of the heap** -> the application keeps running, therefore may have new or deleted gc roots
	- Remark -- mark any objects that may have been modified since initial marking phase -> STW
	- concurrent clean up -> no STW
	- evacuation --  to move live objects from one region to another.-> improve overall heap utilization

### Thread

3 ways to create thread  
- extends thread  
-  implement runnable, then new Thread(runnable object).start()  
-  implement callable\<T\>  

Differences between runnable and callable:  
- Runnable does not return, callable does.
- Runnable does not throw, callable does.

### Thread States

- NEW -- Thread state for a thread which has not yet started.

- RUNNABLE -- Thread state for a runnable thread. A thread in the runnable state is executing in the Java virtual machine but it may be waiting for other resources from the operating system such as processor.

- BLOCKED -- Thread state for a thread blocked waiting for a monitor lock. A thread in the blocked state is waiting for a monitor lock to enter a synchronized block/ method or reenter a synchronized block/ method after calling ``Object.wait``.

- WAITING -- Thread state for a waiting thread. A thread is in the waiting state due to calling one of the following methods: ``Object.wait()``, ``Thread.join()``, ``LockSupport.park()``.  A thread in the waiting state is waiting for another thread to perform a particular action. For example, a thread that has called ``Object. wait()`` on an object is waiting for another thread to call ``Object. notify()`` or ``Object. notifyAll()`` on that object. A thread that has called ``Thread. join()`` is waiting for a specified thread to terminate.

- TIMED_WAITING -- Thread state for a waiting thread with a specified waiting time. A thread is in the timed waiting state due to calling one of the following methods with a specified positive waiting time:

- TERMINATED -- Thread state for a terminated thread. The thread has completed execution.

new --->---- runnable \=\=\=\=\=\=\=\=\=\=\=\=\=running --------->---- terminate
             '<------ Non-runnable ----<-'

--- 
# Day 6 

### wait vs sleep

**wait**
this is a method that can be called by a thread and also release the shared resources. Back to runnable when ``notify()`` or ``notifyAll()`` is called by a holder of the same lock.

**sleep**
thread can be temporarily suspend and it does not release the shared resources


### ``run()`` vs ``start()``

``It is never legal to start a thread more than once.``

| diff                  | run                                    | start                                           |
| --------------------- | -------------------------------------- | ----------------------------------------------- |
| New thread?           | No, run as if a method on main thread. | Yes, start new thread                           |
| Called multiple time? | Yes, like normal method                | No. Once state NEW -> RUNNABLE, state exception |
| When to start?        | Immediately                            | Wait for CPU resources                          |

### Concurrent Problems

Atomicity - operations that won't be interrupted, ``volatile`` does **NOT** guarantee atomicity
Visibility - update immediately visible by other threads (always read/write from main memory)
Ordering - instructions are executed in order

### ``volatile``

Java memory model (JMM)

Each thread has its own local memory and shared resource replica; in main memory, we have shared resources. 

Once a resource is described by volatile, it has two properties: **prevention from reordering and visibility.** Notice that **volatile does not guarantee atomicity**  

- make different threads able to communicate with each other -- once updated, all thread sharing the resource know it
- prevent reordering

### ``synchronized``

``synchronized`` keyword makes sure only **one thread** can access shared resources at the same time. It's a pessimistic lock.

- As long as one thread call one sync method at certain time other sync thread(s) should wait the current thread to finish -> in another world, sync keyword locks the current object: **"this"**, there is **resource competition**.

- Non-sync method **WON'T** have resource competition with sync method

- There is **NO** resource competition between sync method of one object and sync method of another object, i.e. **different objects**.

- For the **non-static** method, your locker: sycn will lock the **current object: this**; for the **static** method, your locker will lock the **class resources**

- Calling **static method** from an instance still **lock the class**, not the object/this.


### CAS

Compare and Swap/Set is an atomic operation.

**Atomic Operation**: it is unit of operations(codes) always execute together, or none of them execute if any exceptions happen.

CAS process:
- Memory location(V): atomic operation will read a value from memory location -> V
- expected old value(A): I expected the value of V == old value A
- new value(B): if V == A -> then update with B, otherwise don't do anything.

ABA problem
- A read V1  -> B write/CAS V2 -> B write/CAS V1 -> A read V1, possibly CAS to V3 with V1 as expected value, judging that shared resources has not changed, which is not the case.
- To solve it, use ``AtomicStampedReference``

### AtomicInteger

```JAVA
	AtomicInteger atomicInteger = new AtomicInteger(0);
	int expectedvalue = 0;
	int newValue = 1;
	System.out.println("result is " + atomicInteger.compareAndSet(expectedvalue,newValue));

	AtomicInteger atomicInteger1 = new AtomicInteger(0);
	int expectedvalue1 = 1;
	int newValue1 = 1;
	System.out.println("result is " + atomicInteger1.compareAndSet(expectedvalue1,newValue1));
```


If you are given int i =0; and two threads, write for updating value of i and read thread for reading value of i

1. Use volatile, always read recent value. Can go wrong if read thread is trying to modify int. Lack of atomicity.
2. Use AtomicInteger. Guarantee atomicity.  

---

# Day 7 

## ConcurrentHashMap

Concurrent hashmap is a hashmap with synchronized keywords. 

**In concurrent hashmap we could NOT have null key or null value**

Not every functions in concurrent hashmap has sync keyword? No. ``get()`` does not have sync.  

We are using CAS in concurrent hashmap.

The array in concurrent hashmap is divided into segments, and lock is applied to each segment instead of the whole object.  

### BlockingQueue

BlockingQueue provides thread safe, and extends queue in java. It is producer - consumer model.  

```java

private BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(10);  
  
public void produce() throws InterruptedException {  
    int value = 0;  
    while(true){  
        System.out.println("produce: " + value);  
        queue.put(value++);  
    }  
}  
  
public void consume() throws InterruptedException{  
    while (true){  
        if(queue.size() == 10){  
            int value = queue.take();  
            System.out.println("Consume: " + value);  
            Thread.sleep(1000);  
        }  
    }  
}  
public static void main(String[] args) {  
    // two threads  
    Day7 day7 = new Day7();  
    Thread produceThread = new Thread(() -> {  
        try {  
            day7.produce();  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    });  
  
  
    Thread consumerThread = new Thread(() -> {  
        try {  
            day7.consume();  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    });  
    produceThread.start();  
    consumerThread.start();  
}

```

### Drawbacks of ``synchronized``
- lower performance your application.
- Deadlock -- if two or more threads are waiting for each other to release lock(shared resources) then deadlock will occur -> your program will hang.  thread will never complete. **Mutual Exclusion, Hold and Wait, No-Preemption, Circular Waiting** 
	- thread 1 ->  access shared resource 1 -> depends on shared resource 2
	- thread 2 -> access resource 2 -> resource 1
- Debugging -- it can be harder to identify the root cause of issues
- Distributed case -- running on two JVM cannot synchronize on shared resources.

## Lock

- pessimistic lock -- lock shared resource, one thread accessing and others are blocked, waiting outside. example : sync, reentrant

- optimistic lock -- can concurrent read and update, detect and resolve conflicts when committing. -- AtomicInteger

- reentrantlock
	- mutual exclusion: only one thread can hold locker at the same time. preventing multiple threads from accessing  or modifying the same code or variable
	- reentrancy: A thread can have another lock after getting one lock
	- Fairness: suppose you have 3 threads are waiting outside and trying to get lock \[thread 1, thread2, thread3\], thread 0 release the lock at this moment, thread 1 will always get the lock first

- readwrite lock -> take ReentrantReadWriteLock as an example
	- mutual exclusion: yes for write lock, no for read lock
	- reentrancy: yes
	- Fairness: yes

```java

// Example of reentrant lock
private int counter = 0;
private final ReentrantLock reentrantLock = new ReentrantLock();
public static void main(String[] args) {
	Day7 day7 = new Day7();
	Runnable task = new Runnable() {
		@Override
		public void run() {
			for(int i = 0; i < 10; i++){
				day7.increment();
				// do something else here
				try{
					Thread.sleep(1000);
				}catch (Exception e){
					e.printStackTrace();
				}
			}
		}
	};

	// multiple threads
	Thread t1 = new Thread(task, "1");
	Thread t2 = new Thread(task, "2");
	t1.start();
	t2.start();
}

public void increment(){
	reentrantLock.lock();
	try {
		counter++;
		System.out.println("counter is " + counter);
	}catch (Exception e){
		e.printStackTrace();
	}finally {
		reentrantLock.unlock();//release
	}
}

```

### Thread Pool

- fixed - size thread pool -- it is thread pool has a fixed number of threads -> once you create , all threads are always keep alive, if you know how much threads in you project, you can use it.

- cached thread pool -- it it a thread pool has no fixed number of threads -> you can create thread as much as you want.  If a thread in your cached thread pool is **idle** for a specified time, this thread **will be terminated**. It has a **negative impact on your performance**. 

- single thread pool -- you have only one thread.

- fork join pool: implement work-stealing thread pool
	- suppose you have a lot huge task need to be executed
	- this pool will divide huge task into a smaller task, this smaller task will be executed in parallel
	- if one thread finished first, it will "steal" task from other thread,
	- suppose thread 1 has very fast execution time, thread 2 has longer execution time
	- when thread 1 finish tasks, thread 1 try to get some tasks from thread 2

### Executor, ExecutorService, Executors

- Executor: it is an **interface** that has only one method, execute() -> you can execute runnable task

- ExecutorService: used for submitting tasks, shutting down. managing tasks, other than creation

- Executors: is used for **creating thread pool**

---

# Day 8

## Network

### OSI model

1: application layer: this a layer close to end-user (you)

2: presentation: it can be used for transforming data into another format(encrypt, decrypt, compression)

3:session layer: it can be used for establish connection, maintenance, terminate

4: transport layer: you can know data is send to someone else

5: network layer: you can know where you need to send data (IP address) -> routing, logical addressing

6: data link layer: data can be encoded or decoded into bits -> hello -> 101010101010

7: physical layer: it converts data bits into electrical impulses or radio signals

### TCP/IP model -> 4 layers

application layer is the same as osi model

transport layer is the same sa osi model

network layer: cantians source and destination ip address and send data over the network

network access layer: transfers the packets between different hosts

### TCP hand shakes -- connection establish

SYN: used to establish connection between client and server

ACK: let us know the other side that has received the SYN

FIN: to terminate the connection

SYN-ACK: SYN message from local device and ack of earlier packet

SEQ: keep track how many data it has send and make sure data is deliverd in the correct order

ACKnum: next sequence number you expect to receive

| state    | client                  | server                               | state    |
| -------- | ----------------------- | ------------------------------------ | -------- |
| CLOSE    |                         |                                      | LISTEN   |
| SYN_SENT | SYN , Seq = x -->       |                                      |          |
|          |                         |                                      | SYN_RCVD |
|          |                         | <-- ACK , ACKnum = x+1, Seq = y, SYN |          |
| ESTB     |                         |                                      |          |
|          | ACK , ACKnum = y+1 ---> |                                      |          |
|          |                         |                                      | ESTB     |

- what if the first handshake is lost

	the client will resend SYN to server side

- what if the second handshake is lost

	the client will resend SYN and server will resend everything -> back to first step

## TCP hand shakes -- connection terminate


| state             | client                | server                        | state       |
| ----------------- | --------------------- | ----------------------------- | ----------- |
| ESTB              |                       |                               | ESTB        |
| FIN_WAIT_1        | FIN, seq = x -->      |                               |             |
|                   |                       |                               | CLOSED_WAIT |
|                   |                       | <-- ACK ,ACKnum = x+1,Seq = y |             |
| FIN_WAIT_2        |                       |                               |             |
|                   |                       | server can still send data    |             |
|                   |                       | <--- FIN,Seq = z              | LAST_ACK    |
| TIMED_WAIT        |                       |                               |             |
| ( 2 \* maximum    | ACK, ACKnum = z+1 --> |                               |             |
| segment lifetime) |                       |                               | CLOSE       |
| CLOSE             |                       |                               |             |
- what if the first handshake is lost

	the client will resend fin

- what if the second handshake is lost

	resend FIN and ACK

- what if the third one is lost

	resend FIN at the server side

- what if the fourth one is lost

	the server side resend FIN

### UDP

it is used for time-sensitive transmissions , less reliable than tcp

### DNS

used to transfer human readable domain name to IP address -> www.google.com -> 123.123.123.123

1: user enter www.google.com into your web

2: DNS server: internet service provider -> ATT, version ...

3: DNS server will lookup ip address with www.google.com. if ATT cached this ip address -> send back to you

if ATT does not cache this ip address -> will go to root server -> and resolve ip address for you

4: ip address back to ATT(DNS server) and cache it

5: back to you

### HTTP

allows us to send or fetch data, such as HTML, Video, image, document. used for any data exchange one the website

stateless -> each request is treated independently

### HTTP methods ->

GET: retrieve data from your resources

HEAD: same to GET, you will only get header information

POST: create new data if it does not exit, otherwise will update data

PUT: update whole data

patch: update part of data

delete: delete data

...

### HTTP status:

2 kinds: 

1: your request is received - >

1XX (100 - 199) -> your request is received and you can continue next step

100 -> continue:

2XX(200 - 299)

200 OK

201 you request is received and create a new resource

204: you request is received, not returning

3XX(300 - 399)

redirection

301 old URL has been changed permanently

302, the server found a temporary redirection URL, and redirection to it

...

2: errors

client side error: 4XX (400 - 499)

404: not found

400 bad request

401: unauthorized

server side error 5XX

500: interanl server error

503 : service not available

504: gateway timeout


## Encryption Algorithm:

-  symmetric algorithm: use the same key for both encryption and decryption

-  Asymmetric algorithm: use different key for encryption and decryption

public key for encrypt and private key for decrypt

HTTPS -> HTTP + SSL/TLS

1: client wants to connect: TCP

2: server sends certificate and public key-> CA stands for certificate authority

3: client will do verification for CA

4: client side will generate pre-master secret

pre-master secret used for generating session key

session key used for encryption

5: secure key exchange

client encrypt pre-master secret with public key

server side will decrypt pre-master secret with private key

6: use pre-master secret to generate session key

7: data exchange

data is encrypted and decrypted on each sides with session key



---

# Lecture 9  8/19 Review

--- 

# Lecture 10  8/20

## OSI 7 layers 
Please Do Not Throw Sausage Pizza Away

Physical -- cable
Data Link -- ethernet, mac. mac addr 1:1 private ip
Network -- IP
Transport -- TCP UDP
Session -- Socket
Presentation -- SSL
Application -- HTTP

## HTTP
PUT vs POST
PUT is idempotent
POST always create new, does not change existing data.

MQ easy to get duplicate message

CAP theory
ACID(atomic consistent isolation durable) or BASE(basically available, soft state, eventually consistent)

## Rest API
Not a protocol(HTTP), but a recommendation/architecture/standard.
rest api = restful api = rest endpoint = rest url
start from **monolithic** application -- single server

- 1:  Based on HTTP
- 2: No verb, only noun (same prefix) in url -- http does it. For example, you want CRUD on student, 
	- get all  -- use url like: /student -- http method: GET
	- get by id -- /student/{id} -- pathvariable -- GET
	- delete -- /student/{id} -- pathvariable -- DELETE
	- create -- /student --  pathvariable -- POST
	- update -- /student/{id} -- pathvariable -- PUT
	- request parameter /student?name=xxx&age=xxx 
- Wrong design
	- ``/getStudent`` -- verb
	- ``/students`` but ``/student/{id}`` -- inconsistent
	- ``/student?id=xxx`` -- id should be used  by ``/student/{id}``
- Versioning ``/v1/student/`` and ``/v2/student/``; put versioning in header but controller should base on header and do corresponding things -- ugly and hard to maintain
- 3:  Request and Response Body
	 - GET method -- no request body, only respond body. in format of ``json``(easy to read, write and understand) or ``xml``   
	 - create: response body include ``{relocationid/id: xxx}``, status code 201 created
	 - update: 204: success without response body. 
	 - delete: 204: success without response body. 
```
{
	key: value,
	key: {
		key: value,
		key: []
	}
}
```
 - Error Case
	 - 500 Internal Server Error -- in all method
	 - 400 Client Side Error -- in all method
	 - 404 Resource not found -- in GET, DELETE
 - Some may have business code in response body.
 - 4: stateless -- compared to stateful, save previous results on server side, e.g. session  -- stateless means whenever we sent restful request, we bring every thing with us.  

Session implementation (old) in Tomcat
1. Sent request (header, cookie) from browser to server.
2. Server would check cookie (in request header) if there is session id, if not, generate (key, value) and send the key as session id in cookie back (in request header)
3. Next time, with session_id in cookie, read data from cache (redis, memcached, db ) based on session id.


## Maven
package manager

## Spring MVC
Old implementation: servlet 
- Socket assign connection to worker thread
- execute the logic based on http info
- /student get -> servlet class 1 -- have to write a lot of configuration mapping
Spring MVC
- Application scan mapping itself and register mapping information automatically
- After connection -> DispatcherServlet (/\*, handle all request) -> handler mapping -> controller
- For ``/student`` handler would map to ``StudentController`` and process data and return to view resolver, generating a view and send back to client (old version)
- New version: return to http message converter -> jackson library (convert between json and object) then send back to client


## Tomcat vs webflux

old time : as in spring mvc, package application into ``war`` file, and upload to tomcat. But now tomcat is included in ``jar`` file.  

webflix -- netty nio server -- try to max CPU ability, use few threads to a lot of things

-> request -> event loop (queue for things to do) -> worker thread pool (some thread pickup)
if thread is blocked by i/o -> thread start doing other thing
when i/o not blocked -> some thread pick up and send back 

thread pool size
1 cpu based task -> N (# cores) +1 threads
2 mix task (io:cpu = 3:7) -> (N/0.7) + 1 
3 or used cached thread pool min 0, max int_max


---

# Lecture 11 8/21

## Singleton (Design Pattern)
Spring IOC mainly used Singleton, Factory building containers.

Only one instance for a class, keep and share the instance. i.e. logger, properties, concurrent HM

Reduce memory and heap usage.

**Eager Loading** and **LazyLoading(multi-thread)**
```java

final class EagerLoadingSingleton{
	// private for encapsulation, static for static get method, final for disabling reassignment
	private static final EagerLoadingSingleton instance = new EageLoadingSingleton();

	private int a = 0;
	// cannot be non-private, don't want to expose
	private EagerLoadingSingleton() {}

	// cannot get instance by new Class, only by using getInstance()
	public static EagerLoadingSingleton getInstance(){
		return instance;
	}
	
	public int getA(){
		return a;
	}
}

// For space saving -- lazy loading
// For thread safe -- double check
final class DoubleCheckLazyLoadingSingleton{
	// Not final but volatile 
	// Why not final?
	// static final has to be initialized at declaration.
	// Why volatile?
	// thread would assign space for instance and then write to it
	// between which other thread may try to access the instance
	// getting not-updated incorrect values 
	private static volatile DoubleCheckLazyLoadingSingleton instance;

	private DoubleCheckLazyLoadingSingleton() {}

	public static DoubleCheckLazyLoadingSingleton getInstance(){
		// if not null, return immediately, no sync, improve performance
		if (instance == null){
			// Way to lock a block in a static method
			synchronized (DoubleCheckLazyLoadingSingleton.class){
				// Why double check? 
				// Suppose multiple threads go into the firt condition statement, 
				// then after the first one initializes the instance and release
				// the lock, the following one would have the lock and create an 
				// instance again, not singleton anymore
				if (instance == null){
					instance = new DoubleCheckLazyLoadingSingleton();
					
				}
			}
		}
		return instance;
	}

}

// Elegant way
public final class Singleton {
    private Singleton() {}
    public static Singleton getInstance() {
        return LazyHolder.INSTANCE;
    }
    private static class LazyHolder {
        private static final Singleton INSTANCE = new Singleton();
    }
}

// Enum way
public enum EnumSingleton{
	MYINSTANCE;
	int value;
	// getter, setter
}
```


Problems of Singleton (non-enum realization): reflection , cloneable and serializable
```java

// reflection example
... throws Exception
Class clazz = DoubleCheckLazyLoadingSingleton.class;
// for constructor in parent class, use getConstructors()
Constructor constructor = clazz.getDeclaredConstructors()[0];
// modify access flag
constructor.setAccessible(true);
DoubleCheckLazyLoadingSingleton obj1 = (DoubleCheckLazyLoadingSingleton) constructor.newInstance()
// To avoid this, in private constructor, add some conditions like
// if (instance != null) ...

```
## Factory

Why factory? 
decouple creation and utilization, decouple creation and types.n 
reuse
S & D in SOLID

Thinking about changing thousands of ``new MyCarA()`` to ``new MyCarB()``.  To save work, use factory method to create new instances, and change the factory realization only once to do the same thing.


Simple Factory
```Java
class MyCarFactory{
	public static MyCar createCar(String make){
		switch (make){
			case "BMW":
				return new BMWCar(); 
			// ...
		}
		return null;
	}
}
```

Factory Interface

Define an interface for creating an object, but let subclasses decide which class to instantiate. Factory method lets a class(now an interface) defer instantiation to subclasses.

```Java
public interface CarFactory{
	Car buildACar();
}
```

Abstract Factory
decouple creation and utilization
producing objects of a bunch of types (i.e. the same factory create different classes of cars)
creating new subclass of factory is simple, Open-Closed

but modifying the interface structure can be hard
```Java
public interface CarFactory{
	Coupe buildACoupe();
	SUV buildASUV();
}
```

## Builder

What if you have a lot of parameters during creation? Factory may not work well.
Dynamic creation process.  
Like stream builder.

```java

// builder within a class by returning this in setter
class Week3Student {
    private String name;
    private int age;

    public Week3Student() {
    }

    public Week3Student(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public Week3Student setName(String name) {
        this.name = name;
        return this;
    }

    public int getAge() {
        return age;
    }

    public Week3Student setAge(int age) {
        this.age = age;
        return this;
    }

    @Override
    public String toString() {
        return "Week3Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}

Week3Student stu1 = new Week3Student().setAge(5).setName("abc");

// builder class
class Week3StudentBuilder {
    private String name;
    private int age;

    public Week3StudentBuilder setName(String name) {
        this.name = name;
        return this;
    }

    public Week3StudentBuilder setAge(int age) {
        this.age = age;
        return this;
    }

    public Week3Student build() {
        return new Week3Student(name, age);
    }
}

Week3Student stu1 = new Week3Student().setAge(5).setName("abc");
Week3Student stu2 = new Week3StudentBuilder()
	.setAge(4)
	.setName("cdf")
	.build();
```

### Bridge

Decouple abstraction from its implementation so that the two can vary independently. Like cross product. Then no need to inherit.



## POJO class
plain old java object.
just information field, metadata, no complicated operations, no util/service
always **private** field, with **public getter, setter**
We can have nested pojo class.


## MVC structure
View -- front end view
Controller -- Spring Controller Class + Spring Service Class
Model -- Spring Repository + Database Storage

DDD domain driven development

## Spring 

**Spring bean** -- instances whose lift cyples are managed by spring

IOC : a concept, realized by Spring / Angular / .Net etc
Dependency Injection: implementation of IOC

```

Dependency injection (DI) is a specialized form of IoC, whereby objects define their dependencies (that is, the other objects they work with) only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method.

```


Use spring IOC containers to manage bean life cycle. IOC container follow **factory** design pattern, using concurrent hashmap managing beans.  ``map<BeanName, Instance>``.  By default, instance would use **singleton** design pattern.  It's not like heap, but more like an object in heap. 
\
Annotation itself don't contain any logic, but spring interpret it and control it.  Inject bean to different locations (to realize factory)  

bean annotation ``@Component @Controller @Service @Repository @Bean``
The middle three are extended from component 

bean injection ``@Autowired + @Qaulifier`` by type vs by name  
Construction Injection -- we put ``@Autowired`` at the top of constructor, spring would use reflection to inject data to constructor 
Setter Injection
Field Injection -- put

bean scope: Singleton (default) / Prototype (whenever inject, create a new one) / Request (create new instance for diff request) / Session / Global Session 

Spring would first load metadata of the bean, insert into IOC container, we call it application context. (Will be covered later)

# Spring MVC Practice
## Hands-on

Use spring initializer if possible.
Dependency problem -- get a maven tree, and exclude some problematic dependency
Another way -- download from spring.io and change it to a maven project

Packaging -- put controller, service, and repository into the same package; or create controller, service, repository package respectively

``@RequestMapping(value = "/student")`` to centralize the prefix

outside the package -- use @ComponentScan

``@PostMapping`` better to have non-parameter constructor in pojo class

``@GetMapping(value = "/{id}", parms = {"countries", "states"})`` to get values and parameters in the GET request.

``@PathVariable("id")`` to get path variable
``@RequestParam(value = "states", required = false)`` to get request parameter

Return ``ResponseEntity<>`` by ``new ResponseEntity<>(pojo, HttpStatus.NO_CONTENT);


## Controller

``@RestController`` at the beginning of the controller class.

``@RequestBody`` to add common prefix of the endpoint (all request types)

``private final`` for singleton fields (class variables)

Prefer to use constructor injection by name (``@Qualifier``)

### GetMapping

``@GetMapping()`` to get endpoint, may specify elements like **name, value/path, params, method, consumes, produces, headers**

``@GetMapping("/{id}")`` <=> ``@GetMapping(value = "/{id}")`` to get endpoint by id, an (implicit) ``value`` is an **alias** for ``path``.  

How to decide which method handles the url request if there are multiple matches? See how AntPathMatcher uses scores to compare.

``@GetMapping(parms = {"country", "countries"})`` to get parameters in url like ``../search?countries=xxx&counties=yyy&country=zzz``. In this case, ``country`` and ``countries`` are mandatory parameters, whereas we can catch more non-required parameters by ``@RequestParam(value = "myValue", required = false)`` . Also we can do some exclusion like ``parms = {"coutry!=Japan", "!ID"}``



---

# Lecture 12 8/22

## Service

By dependency inversion, always create service interface first. Put ``@Service`` at the top of both interface and implementation.

In the controller, we need to use service, and this is where dependency injection comes into play.

## Dependency Injection

Two type by type and by name
Different usage -- constructor, field, setter

``@Autowired`` over the field, would scan through the package and find implemented class, **by type field injection**, it is not preferred -- unit test have to start the whole IOC container.

``@Autowired`` over the constructor (not necessary but still recommended), **constructor injection**,  put ``private final`` keyword to the field, 99% of time! 

Why always constructor injection? During unit test, we don't have to start the entire container. Also, avoid null injection.

setter injection -- method could be called anytime, there may be null injection.

When we get multiple implemented classes for the service interface -- ambiguity, get an error. Now we use **by name injection** 

Add ``@Qualifier("beanName")`` before parameter of constructor -- notice it's not class name but like an instance name.

 **DEPRECATED** name Injection can also be done by naming the bean as if an instance of an implemented class ``@Autowired private MyInterface myImplementedClass`` This works for field injection, does not work for constructor injection (used to work at lower version java 11 springboot 2.1.8).

Or use annotation ``@Primary``

The singleton could be not thread safe -- depending on whether we are accessing shared resources in the service logic.
Explain: whenever a request come, create a new thread, push method frame into stack, if there's shared resources then not safe. Also bear in mind that ``volatile`` alone  does not guarantee thread safe, thinking about ``a++`` include data read and write

## Immutable

Careful about the mutable list, even described with ``final`` keyword, in a class that is supposed to be immutable. During construction and getter, you have to create a copy of the list instead of using the list/parameter list itself. i.e. ``this.list = new ArrayList<>();`` and ``List<> listToReturn = new ArrayList<>();``

Basically like deep copy.

## DTO

You are going to have a lot of pojos, but you don't want them exposed to users.
In order to do that, and in order to reduce data between front end and back end, we have data transfer object, only containing interesting fields.

## ``@Configuration`` For 3rd party class
For example rest template
```Java
@Configuration
public class RestTemplateConfig{

	@Bean
	public RestTemplate restTemplate(){
		return new RestTemplate();
	}
}
```

## Why Spring Boot?
Don't need to config hibernate, spring mvc, bean configuration.
To change config, write it in ``application.properties``.
``EnableAutoConfiguration`` auto read ``spring.factories`` file.
``@SpringBootApplication`` is the entry point.
Embedded tomcat, spring boot start, auto scan, easy to config, spring boot actuator.

## Reflection
class, field, method, annotation, all of them can be reflected.
```Java

public class Reflection {

    public static void main(String[] args) throws Exception {
	    // Class reflection
        Class<?> clazz = RefStudent.class;
        
        // Anonymous constructor Reflection
        Constructor constructor = clazz.getDeclaredConstructors()[0];
        RefStudent stu = (RefStudent) constructor.newInstance();
	    
	    // Field Reflection
        Field f = clazz.getDeclaredField("name");
        f.setAccessible(true);
        System.out.println(stu);
        f.set(stu, "ABC");
        System.out.println(stu);
        
        // Method Reflection
        Method m = clazz.getDeclaredMethod("getName", null);
        System.out.println(m.invoke(stu));

		// Annotation Reflection
        MyClassAnno annotation = clazz.getDeclaredAnnotation(MyClassAnno.class);
        System.out.println(annotation.value());
    }
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface MyClassAnno {
    String value() default "myValue";
}

@MyClassAnno(value = "my new value")
class RefStudent {
    private String name;

    public String getName() {
        return this.name;
    }

    @Override
    public String toString() {
        return "RefStudent{" +
                "name='" + name + '\'' +
                '}';
    }
}

```


## Dynamic Proxy

Before-after logic. Use case: record time, log info, try catch exception, 

Use case in spring -- spring aop, spring exception handler, spring transaction annotation, spring controller advice

Two implementation -- cglib and ?

use ``Proxy.newProxyInstance`` to create a proxy instance, itself does not contain any logic, we have to specify ``(ClassLoader, Class[] /*(of interface)*/, Class /* class that implements InvocationHandler*/)``

```Java
public class DynamicProxyExample {
    public static void main(String[] args) {
        DynamicProxyStudent stu = (DynamicProxyStudent) Proxy.newProxyInstance(
                DynamicProxyExample.class.getClassLoader(),
                new Class[]{DynamicProxyStudent.class},
                new DynamicProxyStudentInvocationHandler(new DynamicProxyStudentImpl())
        );
        System.out.println(stu.getStudentId());
//        System.out.println(stu.getStuName());
    }
}

class DynamicProxyStudentInvocationHandler implements InvocationHandler {
    private final Object instance;

    public DynamicProxyStudentInvocationHandler(Object instance) {
        this.instance = instance;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println(method);
        System.out.println("before");
        Object res = method.invoke(instance, args);
        System.out.println("after");
        return res;
    }
}


interface DynamicProxyStudent {
    int getStudentId();
    String getStuName();
}

class DynamicProxyStudentImpl implements DynamicProxyStudent {
    @Override
    public int getStudentId() {
        return 5;
    }

    @Override
    public String getStuName() {
        return "Tom";
    }
}
```

Don't need to rewrite duplicate before-after logic codes.

That's how the framework works, and spring provides aop features so you don't have to create new proxy instance as above.

## Spring AOP -- aspect oriented programming

centralize some before after logics in one place.

Point Cut : the location in the method you want to do aop. (.*  like regex pattern)
Aspect: @Before @After @Around @AfterThrows @AfterReturn

Then those instance would be replace by a proxy instance containing that original instance. Whenever you trigger function, it would go to invocation handler first and then do the procedure.

## Why spring boot?

1. embedded tomcat
2. application.properties
3. starter
4. actuator
5. auto configuration
6. easy for microservcies
7. package everything into a jar file

## Exception Handler in Spring Boot

1. try catch
2. ``@ExceptionHandler`` inside the class
3. Global exception handler -- ``@ControllerAdvice``
Centralize exception-related logic in the handler. Hide exception details from user, just return 

```Java


@ControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGlobalException(Exception ex) {
        ErrorResponse errorDetails = new ErrorResponse(ex.getMessage(), 
	        new Date(), 0);
		return new ResponseEntity<>(errorDetails, 
			HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```



---

# Lecture 13 8/23

## Homework Review

```Java
	// configure url in application.properties
	// or use public static final
	@Value("${university.url}")
    private String url;
	
	// restTemplate usage
	restTemplate.getForObject(url, UniversityDTO[].class);

	// List of parameters in rest api
	// /university?key=1&key=b&key=c
	@Override
    public List<UniversityDTO> getAllByCountries(List<String> countries) {
        List<CompletableFuture<UniversityDTO[]>> futures = new ArrayList<>();
        for(String country: countries) {
            futures.add(
                    CompletableFuture.supplyAsync(
                            () -> restTemplate.getForObject(url + "?country=" + country, UniversityDTO[].class),
                            threadPool
                    )
            );
        }
        List<UniversityDTO> res = new ArrayList<>();
        CompletableFuture.allOf(futures.toArray(new CompletableFuture[0]))
                .thenAccept(VOID -> futures.stream().map(CompletableFuture::join).forEach(arr -> res.addAll(Arrays.asList(arr))))
                .join();
        return res;
    }
```

## How to design rest endpoint?
1. Start with api design -- endpoint, method, status code, request body, response body ...  exception
2. Controller Service Repo only framework todo, no actual implementation
3. Unit test (junit, mockito)
4. Implement todo area
5. test code with unit test cases
6. push to the (feature) branch production/release/development, pull request, code review

```Java
class A {
    public int getNumber() {
        //logic
        return 3;
    }
}

import static org.junit.jupiter.api.Assertions.*;

class ATest {
	// final or not; static or not
    private A a;

	// Before all -- static type and static init / before each -- non-static
    @BeforeEach
    public void init() {
        a = new A();
    }

    @Test
    public void testGetNumber() {
        assertTrue(a.getNumber() == 5);
        assertThrows();
    }
}
```

## How does rest api meet production standard?
1. Performance, response time, rps can handle.
2. Security (access, authorization, ddos attack)
3. Log info / debug / error -- sout is not easy to check and maintain, and metrics logs for analytics
	AWS Glue -> ETL -> csv / parquet -> S3 -> training -> save model into s3 -> sagemaker api
4. exception -- at low level always throws to upper layer -- or always response with 200, hard to find where the issue is. Lost track/ lost trace. To throw multiple exception, ``addSuppressed``
```Java
try{}
catch (XXXException e){
	log...
	// should not throw new exception, that would lose track
	// unless you want to covert checked to unchecked
	throw e
}
return default value
```
5. documentation -- google doc, wiki page, swagger(spring)
6. test, unit test, integration test, functional test, automation test
7. when your api calls 3rd party api/downstream api, we have to deal with failure of those apis.
	1. retry a few times
	2. circuit breaker (tool configurable, like a proxy)
			status -> on/off
			status -> on  : request -> 3rd party api; 
				3 exception/timeouts out of 5 request, status turns to off
			status -> off : request -> return default (pre configured) value
			background thread -> keep checking 3rd party health status -> once back online, 
				change status to on

---

# Lecture 14 8/26

## RDBMS\

Most of time, we use single leader cluster. Write to the leader but may read from the follower.

Types:
- RDBMS(Disk): Oracle, Mysql, PostgreSQL, SQL Server

- In memory RDBMS(not kv cache like redis): H2, Derby (remove when restart. for testing)

- In memory cache: redis(event loop, command lua script, single thread), Memcached, CDN (CloudFront, cache videos etc. in edge location), DAX(in DynamoDB, medium or large instance)

- Key-value DB: DynamoDB

- NoSQL: MongoDB(document DB), Cassandra(column DB) -- cluster  

- Object storage: S3 (everything immutable, config lifecycle from S3 to S3 glacier), S3 Glacier (take hours to retrieve data)

 - csv(table) parquet files(column) (no row/record lock)

Redshift use postgre?

Hibernate lies on the top of jdbc, providing more functionality, make queries easier, using like connection pool, caching. 

## SQL

Widely use, popular, stable, long history, big community, lots of support, transaction friendly, easy, easy join if normalized.

### Practice
Use alias! Oracle can make alias with just space, no "AS" keyword
``in (100, 101)`` not range, but a set
don't use ``count(1)``, it rarely does what you want it to do. Always ``count(*)`` when you want to use ``count(1)``. ``count(col)`` removes null value.
```PostgreSQL
CASE ... WHEN ... THEN ... WHEN ... THEN ... ELSE ... END
```
### Aggregation function
``min, max, count, sum, avg`` aggregation functions, only one column in the bracket
Always select grouped columns. Use aggregation function to capsule those ungrouped columns.

## Correlated
Subqueries in where/select clause can be correlated with the main query. In from clause, it's usually meaningless. 

## Select the 2nd highest salary

Solution 1, does not select the other information, or have to be nested further
```SQL
SELECT max(e.salary) AS secondMaxSalary
FROM hr.employees e
WHERE e.salary < (SELECT max(salary) FROM hr.employees)
```

 Solution 2, correlated subqueries
 ```SQL
 SELECT e.salary AS secondMaxSalary, e.employee_id
 FROM hr.employees e
 WHERE 1 = (SELECT COUNT(DISTINCT salary) FROM hr.employees WHERE salary > e.salary);
```

Solution 3, dense rank (dense_rank 1 2 2 3; rank 1 2 2 4)
```PostgreSQL(Oracle)
SELECT e.salary, e.employee_id, dense_rank() OVER (ORDER BY e.salary DESC) AS rnk
FROM hr.employees e
```

## Keyword

GROUP BY / UNION / UNION ALL / MINUS/EXCEPT / INTERSECT

PARTITION BY -- rank in each group, use in a window function
```PostgreSQL(Oracle)
select empolyee_id, salary, rank() over (partition by department_id order by salary desc)
from hr.employees
```

Oracle would read from blocks/pages on the disk, and it follows some locality so we don't have to scan all.

MongoDB use sharding, each sharding can be a cluster
id 1~1000 fills a sharding with one primary node and second nodes
or use hash id.

NoSQL also has 1 to many, many to one, many to many, may need normalization sometimes.

cross join/ inner join / left/right join/ full join

---

# Lecture 15 8/27

## Interview question

TODO: testing, daily work, hibernate, jpa

### Stored Procedure
Define a cursor and control it.
SQL would create cursor for you.
### ``finally`` block
executed just after computing what to return and before actual return.
usually close connection, closeable interface
## bigdecimal
more accurate than double/float

### Why stream api
functional programming, chain operation, parallel stream, lambda expression, (final variable problem)

### Js vs java
js is single thread, flexible type.
java var type just like alias Map<> mm; <==> var mm.

### clean code (production standard)
solid principle, complexity, exception handling, log, documentation, tdd

### test private method
power mock, reflection

## DB transaction

A transaction is a single logical unit of work that accesses and possibly modifies the contents of a database. Transaction keeps your data clean.

### ACID
Transaction properties in DBMS like MySQL.

A atomicity -- either nothing or every things happen, from one valid state to another valid state -- prevent dirty data.

C consistency -- integrity constraints must be maintained.

I isolation -- multiple transactions can occur concurrently without leading to the inconsistency of the database state.

D durability --  once the transaction has completed execution, the updates and modifications to the database are stored in and written to disk and they persist even if a system failure occurs.

ACID Atomicity vs BASE Eventually consistency

RDBMS -- implemented by MVCC multi-version concurrency control.
Cassandra -- based on time stamp. Last write wins.

Oracle Isolation level (read committed -- default, serialization, read only)
### MySql Isolation level

Even you're not providing begin transaction syntax during write/insert/update/delete, you're actually in a transaction, and there would be locks.

- **Read Uncommitted**
user1  select 100                            select 50 (dirty)                            select 100/50
------------------------------------------------------------------------------------------------>
user2    begin tx          del 50                           rollback/commit

This can have dirty data issue, not recommended.

- **Read Committed** (solve dirty read, but still phantom read, non-repeatable read)
user1 select 100        =          select 100          !=                select 50
----------------------------------------------------------------------------------------->
user2  begin tx       del 50                         commit    

Read the most recent after transaction, but when you're in a long query or some subquery, you just want the result as last time, you don't want the most updated results.

How MVCC does it? A linked list at the interesting row, containing both old version and new version.

- **Repeatable Read** (solve dirty read and non-repeatable read, but still phantom read)
user1 select 100        =                   select 100         =                select 100
----------------------------------------------------------------------------------------->
user2  begin tx       del/update 50                         commit    

*phantom read issue*:
user1 select 100        =                   select 100         !=                select 150
----------------------------------------------------------------------------------------->
user2  begin tx       insert 50                            commit    

there would be phantom read issue in repeatable read.


- **Serializable** (no issue)

### How to achieve those isolation?

MySQL uses MVCC in read committed and repeatable read, achieved by three hidden column
1. rowid (not shown in \* but still selectable)
2. tx_id transaction id, auto increament
3. rollback pointer

Start with

| emp_id | emp_name | rowid | tx_id | rollback |
| ------ | -------- | ----- | ----- | -------- |
| 1      | Tom      | xxx   | 1     | null     |

Update Tom to Jerry

| emp_id | emp_name | rowid | tx_id | rollback    |     |     |     |     |      |
| ------ | -------- | ----- | ----- | ----------- | --- | --- | --- | --- | ---- |
| 1      | Jerry    | xxx   | 2     | point to -> | 1   | Tom | xxx | 1   | null |

Every time you do a select, the database would generate a **read view** consisting of list of **committed tx id** for you. For committed read, this list is always up to date, while for repeatable read, this list is fixed when created. For each row, then the transaction id would be compared with the ids in the list until you find the desired one.

Postgre is using tx timestamp instead of tx id.

## Lock in DB
for update, delete, we will have row lock.

To lock rows (exclusively/ write lock), use
```PostgreSQL
SELECT ... FROM ... WHERE ... FOR UPDATE
```

In repeatable read,  there would also be gap lock. 

In serialization, all select statements would be attached with share read lock.
write lock would block read and write lock
read lock would block write lock

### Lost Update and Optimistic Lock
Lost update
When two users want to do i++ at the same time, probably result in only one increment.

**Optimistic Lock** -- to avoid lost update problem
You need extra columns to implement optimistic locks.
Provide a version/last_update_timestamp column.

## Stored Procedure, Function, Trigger

TODO




---

# Lecture 16 8/28

## B+ tree index


## Bitmap index

## Clustered and non clustered index

## Execution Plan

## Hint

## Oracle Architecture


---

# Lecture 17 8/29

### Decoupling columns
A - B
1 - 1 then either can be a foreign key of the other
1-m then A must be a foreign key of B
m-m then use a third junction/associate table to record the relationship (probably unique constraints)

### Normalization
For checking existing table, not to design tables.

functional dependency, closure
super key, candidate key -- prime attributes, primary key
key, the whole key, nothing but the key 1st, 2nd, 3rd normal form

On-premises servers

### NoSQL structure in RDBMS

survey 1 name age email address
survey 2 name work hometown ..

solution 1
id  survey_id  col1  col2  col3 ...
drawbacks: If we specify the attribute of each column, there would be a lot of null values. If we allow combined attribute -- colx could probably have inconsistent meaning, i.e. col2 stands for age in survey 1 but work in survey 2, then put them together would make it hard to write sql query.

solution 2
template design pattern
shared data in one centralized tables, survey-specific attributes in separate secondary tables.  

solution 3
name age email json(for the rest attributes)

solution 4
entity-attribute-value pattern

| id  | column_name | column_type | column_value | survey_id | user_id |
| --- | ----------- | ----------- | ------------ | --------- | ------- |
| 1   | "name"      | "String"    | "Alex"       | 1         | 1       |
| 2   | "Age"       | "Integer"   | "24"         | 1         | 1       |

### JDBC and ORM

prepare statement?

url, user, password called connection in jdbc, or source in Hibernate (saved in PGA)

callable statement -- stored procedure
prepared statement -- some not hard-coded input

why not hard coded? Prevent SQL injection (attack like or true), prepared statement let or true become part of the string.

***Why not JDBC? / Why use ORM?***
1. JDBC does not have connection pool (some use blocking queues inside, one idle queue and one busy), cannot reuse or dynamically control, while in ORM has.
2. JDBC has to construct POJO manually, ORM would directly (table-class) map to POJO objects
3. JDBC writes native query (syntax difference between different RDBMS') , instead, in ORM, we have HQL/JPQL/ORM query language, centralized, just need to create dialect.
4. criteria query (dynamic query / builder pattern to build query)
5. Cache query results in ORM.

Hibernate is one of  the implementations of ORM. JPA is a standard/interface (like restAPI), and Hibernate realizes it.

JPA use entity manager, persist(create) / merge(if exist, update, or create)
Apart from entity manager, Hibernate also uses session, save / update

### How to use hibernate / orm / jpa
1. Dependency -- database, spring data jpa (with hibernate in it),
2. Configure user name, password, url .. dialect -- datasource. Two ways to config, ``application.properties``(only one database, universally) or class configuration (flexible, more databases)
3. (Optional) Configure session factory or entity manager factory
		datasource 1-1 session factory 1-m sessions 1-1 user request
4. entity class (can have partial things in the table)
```Java
@Entity
@Table("student")
Class Student{
	// tell hibernate should use/autoincrease a sequence over this attribute in db when creating data
	@ID
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private String id;
	@Column
	private String name;
	@OneToMany(mappedBy = "s1234") // field name
	private List<Teacher> listTeacher;
}

@Entity
@Table("teacher")
Class Teacher{
	@ManyToOne
	// name of the foreign key column
	@JoinColumn(name = "student_id")
	private Student s1234;
}

```
5. Repository Layer -- like service, interface, implementation, inject entityManager
6. Instead of writing entity manager, we only need to extend JPA - repository
7. Use ``@transactional`` at service level



### Lazy and Eager Loading
Lazy Loading (default one to many)
```
Student student = (Student)entityManager.createQuery("..").getSingleResult();
// Only get the same layer
List<Teacher> teachers = student.getTeacherList();
// Call any function in teachers -> trigger proxy -> send query to db to fetch all related teacher data
teachers.size();
```

N+1 issues

Eager Loading
In first statement, it would get all teachers objects.


### hibernate fetch join vs join

``entityManager.createQuery(".. fetch join ..")`` does eager loading

## Persistent Context
A place for hibernate to keep objects, caches, data, etc. like IOC containers in spring. 

new instance would be in transient state, hibernate is not managing it.

proxied / attached object

unproxy / detach object

in lazy loading, if you detach after getSingleResult, and then try to access listTeacher, you would get ``LazyInitialization Exception``. 



# Lecture 18 9/3

## Security

### How to keep your application/rest api secured?
0. Do not say Spring Security directly.
1. Authentication -- user name and password 
	1. return 401 if failed, return token if success
2. Authorization -- 
	1. return 403 if access is denied.
	2. OAuth 2.0
	private authorization service  user -- app -- auth, store token in cache
	third party authorization service 3rd -- user -- app -- 3rd, app registers on 3rd party and has its credentials
	expiration time
	3. JWT json web token -- header, payload, signature
	header has encryption information; payload has user id, rowid, exp time, refreshable, raw data; signature to encrypt header and payload to make sure token is not modified by someone else.  JWT is for authorization, not authentication. Server would not save JWT in db.
	
3. HTTPS
When user send token back to server, it must use https.
4. SQL injection, XML injection, log injection
5. CORS cross origin source sharing -- specify ``Access-Control-Allow-Origin`` header in http response, or use nginx to unify access domain
6. CSRF attack. Related to session.  Use form and hidden layer.
7. DDOS -- scaling, downgrade to return static page, defense service
8. Passwords are hashed before stored into db, heap dump can also leak password.

### Spring Sector
1. Request -> Filter Chain -> Dispatcher Servlet  Before and Post filter. Can be done in one class.
```Java
// pre fileter
chain.doFilter();
// post filter
// post logics
```
2. user 1-1 request 1-1 Thread 1-1 ThreadLocal. Save login info in threadlocal.
3. 

## Security Hands-On



# Lecture 19 9/4

## MicroService

Monolithic project, package all things in a ``jar`` file.

Why not monolithic?
1. Really hard to redeploy the service. Blue-green deployment.
2. For horizontal scaling, we need load balancer
3. Everyone works in the same project
4. Deep inheritance relationship, hard to maintain

MicroService
1. Scalability
2. Different languages in different services
3. Different team/developers can work parallely
4. Deploy
5. Can use message queue to achieve asynchronous
6. Fault tolerance

Challenges / Need to consider when you design microservices
1. bug trace -> generate co-relation id (global unique id gen at gateway, added to header)\
2. 
3. 


change dir in config and 