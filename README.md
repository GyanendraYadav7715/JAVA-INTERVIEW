# Comprehensive Java Interview Questions & Answers
## For 2-3 Years Experience

### 1. How does Java achieve platform independence?

**Detailed Answer:**
Java achieves platform independence through a two-step compilation and execution process:

1. **Compilation Phase**: When you write Java code (.java files), the Java compiler (javac) converts it into bytecode (.class files). This bytecode is not machine-specific; it's an intermediate representation.

2. **Execution Phase**: The Java Virtual Machine (JVM) reads this bytecode and converts it into machine-specific instructions at runtime using Just-In-Time (JIT) compilation.

**Key Points to Mention:**
- "Write Once, Run Anywhere" (WORA) principle
- Bytecode is platform-independent, while machine code is platform-dependent
- Each operating system has its own JVM implementation, but all understand the same bytecode
- The JVM acts as an abstraction layer between the application and the operating system

**Example:** If I compile my Java program on Windows, the same .class file can run on Linux or Mac without recompilation, as long as a compatible JVM is installed.

---

### 2. What is ClassLoader in Java?

**Detailed Answer:**
ClassLoader is a subsystem of JVM responsible for loading class files into memory. When you run a Java program, not all classes are loaded at once; they're loaded dynamically when needed.

**Three Types of ClassLoaders:**

1. **Bootstrap ClassLoader**: Loads core Java classes from rt.jar (like java.lang, java.util)
2. **Extension ClassLoader**: Loads classes from the extension directory (jre/lib/ext)
3. **Application ClassLoader**: Loads classes from the application classpath

**How it Works:**
- Uses delegation model: checks parent classloader first before loading itself
- Ensures classes are loaded only once (prevents duplicate loading)
- You can create custom classloaders for specific requirements like loading encrypted classes

**Real-world Use Case:** Custom classloaders are used in application servers like Tomcat to isolate applications and enable hot deployment.

---

### 3. Write a Java program to check if a number is even or odd

**Detailed Answer:**

```java
// Method 1: Using Modulo Operator (Most Common)
public boolean isEven(int number) {
    return number % 2 == 0;
}

// Method 2: Using Bitwise AND (More Efficient)
public boolean isEvenBitwise(int number) {
    return (number & 1) == 0;
}
```

**Explanation to Interviewer:**
- In binary, even numbers always end with 0, odd numbers with 1
- The bitwise AND operation with 1 checks only the last bit
- If (number & 1) equals 0, it's even; if it equals 1, it's odd
- Bitwise operations are faster as they work directly at the bit level
- Modulo operator is more readable but slightly slower

---

### 4. Difference between ArrayList and HashSet

**Detailed Answer:**

| Feature | ArrayList | HashSet |
|---------|-----------|---------|
| **Interface** | Implements List | Implements Set |
| **Duplicates** | Allows duplicates | No duplicates allowed |
| **Order** | Maintains insertion order | No guaranteed order |
| **Indexing** | Supports index-based access (get(i)) | No index-based access |
| **Null values** | Can have multiple nulls | Only one null allowed |
| **Performance** | O(1) for access, O(n) for search | O(1) for add/remove/contains |
| **Internal Structure** | Uses dynamic array | Uses HashMap internally |

**When to Use:**
- **ArrayList**: When you need ordered collection with duplicates (shopping cart items)
- **HashSet**: When you need unique elements and fast lookup (tracking unique visitors)

---

### 5. What is double-checked locking in Singleton?

**Detailed Answer:**
Double-checked locking is a design pattern used to reduce the overhead of acquiring a lock every time we want to get a singleton instance.

**Implementation:**
```java
public class Singleton {
    private static volatile Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {  // First check (no locking)
            synchronized (Singleton.class) {
                if (instance == null) {  // Second check (with locking)
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

**Why Two Checks?**
1. **First check (outside synchronized)**: Avoids unnecessary locking once instance is created
2. **Second check (inside synchronized)**: Ensures only one thread creates the instance

**Important Note:** The `volatile` keyword is critical here to prevent instruction reordering and ensure visibility across threads.

---

### 6. How to create a thread-safe Singleton?

**Detailed Answer:**
There are several approaches:

**Method 1: Enum Singleton (Best Practice)**
```java
public enum Singleton {
    INSTANCE;
    
    public void doSomething() {
        // business logic
    }
}
```
- Inherently thread-safe
- Prevents reflection and serialization attacks
- Recommended by Joshua Bloch

**Method 2: Bill Pugh Singleton (Lazy Initialization)**
```java
public class Singleton {
    private Singleton() {}
    
    private static class SingletonHelper {
        private static final Singleton INSTANCE = new Singleton();
    }
    
    public static Singleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```
- Lazy initialization without synchronization
- ClassLoader ensures thread safety

**Method 3: Eager Initialization**
```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```
- Simple and thread-safe
- Instance created at class loading time

---

### 7. When to use volatile variable?

**Detailed Answer:**
Use `volatile` when a variable is accessed by multiple threads, and you want changes made by one thread to be immediately visible to other threads.

**What volatile Does:**
1. Prevents caching of variables in CPU registers
2. Ensures reads/writes go directly to main memory
3. Prevents instruction reordering

**Example Scenario:**
```java
public class StatusChecker {
    private volatile boolean running = true;
    
    public void run() {
        while (running) {
            // do work
        }
    }
    
    public void stop() {
        running = false;  // Immediately visible to run() thread
    }
}
```

**Important Limitation:** `volatile` only guarantees visibility, NOT atomicity. For compound operations (like i++), you still need synchronization or atomic classes.

---

### 8. When to use transient variables?

**Detailed Answer:**
Use `transient` when you don't want certain fields to be serialized when the object is converted to a byte stream.

**Common Use Cases:**
1. **Derived/Calculated Fields**: Values that can be recalculated
2. **Sensitive Information**: Passwords, credit card numbers
3. **Non-serializable References**: File handles, database connections
4. **Temporary/Cache Data**: Values that shouldn't persist

**Example:**
```java
public class User implements Serializable {
    private String username;
    private transient String password;  // Won't be serialized
    private transient int loginCount;   // Derived value
    
    // Upon deserialization, password will be null
    // and loginCount will be 0
}
```

---

### 9. Difference between transient and volatile?

**Detailed Answer:**
These keywords serve completely different purposes:

| transient | volatile |
|-----------|----------|
| Related to **Serialization** | Related to **Concurrency** |
| Prevents field from being serialized | Ensures visibility across threads |
| Used with Serializable interface | Used in multithreaded scenarios |
| Field value is lost during serialization | Field value is always read from main memory |

**Key Point:** They can be used together: `private transient volatile boolean flag;`

---

### 10. Difference between Serializable and Externalizable?

**Detailed Answer:**

| Serializable | Externalizable |
|--------------|----------------|
| Marker interface (no methods) | Has writeExternal() and readExternal() |
| Automatic serialization | Manual serialization control |
| Serializes all non-transient fields | You choose what to serialize |
| Less control, easier to use | More control, more complex |
| Slower performance | Better performance |

**Example:**
```java
// Serializable (automatic)
public class User implements Serializable {
    private String name;
    private int age;
}

// Externalizable (manual control)
public class User implements Externalizable {
    private String name;
    private int age;
    
    public void writeExternal(ObjectOutput out) throws IOException {
        out.writeUTF(name);
        // Can add encryption, compression, etc.
    }
    
    public void readExternal(ObjectInput in) throws IOException {
        name = in.readUTF();
    }
}
```

---

### 11. Can we override private methods?

**Detailed Answer:**
**No**, private methods cannot be overridden.

**Reasons:**
1. Private methods are not visible to subclasses
2. Overriding requires method visibility in subclass
3. Private methods are bound at compile time (static binding)
4. You can define a method with the same name in subclass, but it's a new method, not an override

**Example:**
```java
class Parent {
    private void display() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    private void display() {  // This is NOT overriding
        System.out.println("Child");  // It's a new method
    }
}
```

---

### 12. Difference between Hashtable and HashMap?

**Detailed Answer:**

| Feature | HashMap | Hashtable |
|---------|---------|-----------|
| **Synchronization** | Not synchronized | Synchronized |
| **Thread Safety** | Not thread-safe | Thread-safe |
| **Null Keys** | One null key allowed | No null keys |
| **Null Values** | Multiple null values | No null values |
| **Performance** | Faster | Slower (due to synchronization) |
| **Legacy** | Modern (Java 1.2) | Legacy (Java 1.0) |
| **Iterator** | Fail-fast | Fail-fast (Enumerator fail-safe) |

**Recommendation:** Use HashMap in single-threaded scenarios. For thread-safe operations, use ConcurrentHashMap instead of Hashtable.

---

### 13. Difference between List and Set?

**Detailed Answer:**

| List | Set |
|------|-----|
| Allows duplicates | No duplicates |
| Maintains insertion order | Generally unordered (LinkedHashSet maintains order) |
| Index-based access (get(i)) | No index-based access |
| Can have multiple nulls | Maximum one null (HashSet) |
| Examples: ArrayList, LinkedList | Examples: HashSet, TreeSet |

**Real-world Examples:**
- **List**: Order history in e-commerce (duplicates allowed, order matters)
- **Set**: Email recipients (unique emails, order doesn't matter)

---

### 14. Difference between ArrayList and Vector?

**Detailed Answer:**

| ArrayList | Vector |
|-----------|--------|
| Not synchronized | Synchronized |
| Faster | Slower |
| Increases by 50% when full | Doubles when full |
| Modern (Java 1.2) | Legacy (Java 1.0) |
| Use in single-threaded apps | Use in multi-threaded apps |

**Growth Strategy:**
- ArrayList: capacity = (capacity * 3/2) + 1
- Vector: capacity = capacity * 2

**Modern Alternative:** For thread-safety, use `Collections.synchronizedList(new ArrayList<>())` or `CopyOnWriteArrayList` instead of Vector.

---

### 15. Difference between Hashtable and ConcurrentHashMap?

**Detailed Answer:**

| Hashtable | ConcurrentHashMap |
|-----------|-------------------|
| Locks entire table | Locks only segment/bucket |
| One thread at a time | Multiple threads can read/write |
| Poor scalability | Better scalability |
| Slower | Faster |
| No null keys/values | No null keys/values |

**Concurrency Level:**
- Hashtable: 1 lock for entire map
- ConcurrentHashMap: 16 segments by default (configurable)

**Performance:** In high concurrency scenarios, ConcurrentHashMap can be 10-100x faster than Hashtable.

---

### 16. How does ConcurrentHashMap work internally?

**Detailed Answer:**
ConcurrentHashMap achieves thread safety through **segmentation** and **lock striping**:

**Key Concepts:**
1. **Segmentation**: Map is divided into segments (default 16)
2. **Lock Striping**: Each segment has its own lock
3. **Multiple threads can write** to different segments simultaneously
4. **Read operations** don't require locks (most of the time)

**Internal Structure (Java 7):**
```
ConcurrentHashMap
    ├── Segment 0 (lock)
    ├── Segment 1 (lock)
    ├── Segment 2 (lock)
    └── ... (up to 16 by default)
```

**Java 8+ Changes:**
- Uses CAS (Compare-And-Swap) operations
- Reduces to node-level locking
- Better performance with TreeBins for high collision

---

### 17. What methods must be overridden to use objects as HashMap keys?

**Detailed Answer:**
You must override **equals()** and **hashCode()** methods.

**Why Both Are Required:**
1. **hashCode()**: Determines which bucket the key goes into
2. **equals()**: Used to check if the key already exists in that bucket

**Contract Rules:**
- If two objects are equal (equals() returns true), they must have the same hashCode
- If two objects have the same hashCode, they don't necessarily have to be equal
- If you override equals(), you MUST override hashCode()

**Example:**
```java
public class Employee {
    private int id;
    private String name;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Employee employee = (Employee) o;
        return id == employee.id;
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

---

### 18. Difference between wait() and sleep()?

**Detailed Answer:**

| wait() | sleep() |
|--------|---------|
| From Object class | From Thread class |
| **Releases lock** | **Doesn't release lock** |
| Must be in synchronized block | Can be called anywhere |
| Can be woken by notify() | Wakes automatically after time |
| Used for inter-thread communication | Used to pause execution |

**Example:**
```java
synchronized(obj) {
    obj.wait();  // Releases lock, other threads can access obj
}

Thread.sleep(1000);  // Holds lock if any, just pauses
```

**Key Point:** wait() allows other threads to acquire the lock, sleep() doesn't.

---

### 19. Difference between notify() and notifyAll()?

**Detailed Answer:**

| notify() | notifyAll() |
|----------|-------------|
| Wakes **one random** waiting thread | Wakes **all** waiting threads |
| Other threads remain waiting | All threads compete for lock |
| Use when condition benefits only one thread | Use when multiple threads might benefit |
| More efficient | Safer approach |

**Example Scenario:**
```java
// Producer-Consumer problem
synchronized(queue) {
    while (queue.isEmpty()) {
        queue.wait();
    }
    // consume
}

synchronized(queue) {
    queue.add(item);
    queue.notifyAll();  // Wake all consumers
}
```

**Best Practice:** Use `notifyAll()` unless you're certain only one thread needs to be awakened and all threads are waiting for the same condition.

---

### 20. Why override hashCode() and equals()?

**Detailed Answer:**
You must override these methods to ensure correct behavior when objects are stored in hash-based collections (HashMap, HashSet, Hashtable).

**What Happens If You Don't:**
- Default equals() uses reference equality (==)
- Default hashCode() returns memory address
- Your objects won't be found in collections even if logically equal

**Real-world Problem:**
```java
// Without overriding equals() and hashCode()
Person p1 = new Person("John", 25);
Person p2 = new Person("John", 25);

Set<Person> set = new HashSet<>();
set.add(p1);
set.add(p2);  // Both added! Set has 2 "identical" persons

// With proper overrides, p2 won't be added (duplicate)
```

**Contract to Follow:**
1. Consistent: Multiple invocations should return same value
2. Symmetric: a.equals(b) == b.equals(a)
3. Transitive: If a.equals(b) and b.equals(c), then a.equals(c)
4. If equals, then same hashCode

---

### 21. What is HashMap's load factor?

**Detailed Answer:**
Load factor is a measure that determines when HashMap should resize (rehash) itself.

**Default Values:**
- Initial Capacity: 16
- Load Factor: 0.75 (75%)
- Resize Trigger: capacity × load factor = 12 elements

**Formula:**
```
Threshold = Capacity × Load Factor
```

**How It Works:**
When the number of elements exceeds the threshold, HashMap:
1. Doubles its capacity (16 → 32 → 64...)
2. Rehashes all existing entries
3. Redistributes them across new buckets

**Trade-offs:**
- **Higher load factor (0.9)**: Less memory, more collisions, slower lookup
- **Lower load factor (0.5)**: More memory, fewer collisions, faster lookup
- **Default (0.75)**: Good balance

**Example:**
```java
// Default: capacity=16, loadFactor=0.75
Map<String, String> map = new HashMap<>();

// Custom values
Map<String, String> map = new HashMap<>(32, 0.5f);
```

---

### 22. Difference between ArrayList and LinkedList?

**Detailed Answer:**

| Feature | ArrayList | LinkedList |
|---------|-----------|------------|
| **Structure** | Dynamic array | Doubly linked list |
| **Random Access** | O(1) - Fast | O(n) - Slow |
| **Insertion/Deletion** | O(n) - Slow | O(1) - Fast |
| **Memory** | Contiguous | Non-contiguous |
| **Memory Overhead** | Less (just array) | More (node objects) |
| **Best For** | Read-heavy operations | Write-heavy operations |

**Performance Comparison:**
```
Operation          ArrayList    LinkedList
---------          ---------    ----------
get(index)         O(1)         O(n)
add(element)       O(1)*        O(1)
add(index, e)      O(n)         O(n)
remove(index)      O(n)         O(n)
remove(element)    O(n)         O(n)
```

**When to Use:**
- **ArrayList**: Frequent access by index, iteration
- **LinkedList**: Frequent insertions/deletions at beginning/middle

---

### 23. Difference between CountDownLatch and CyclicBarrier?

**Detailed Answer:**

| CountDownLatch | CyclicBarrier |
|----------------|---------------|
| **One-time use** | **Reusable** |
| Count down to zero | Wait for threads to reach barrier |
| Cannot be reset | Resets automatically |
| Thread waits for count to reach zero | Threads wait for each other |

**CountDownLatch Example:**
```java
CountDownLatch latch = new CountDownLatch(3);

// Worker threads
latch.countDown();  // 3 → 2
latch.countDown();  // 2 → 1
latch.countDown();  // 1 → 0

// Main thread
latch.await();  // Waits until count is 0
```

**CyclicBarrier Example:**
```java
CyclicBarrier barrier = new CyclicBarrier(3, () -> {
    System.out.println("All threads reached barrier!");
});

// Each thread calls
barrier.await();  // Wait for all 3 threads

// Can be reused for next cycle
```

**Use Cases:**
- **CountDownLatch**: Starting a service after initialization tasks complete
- **CyclicBarrier**: Parallel computations where threads need to synchronize at multiple points

---

### 24. When to use Runnable vs Thread?

**Detailed Answer:**
**Always prefer Runnable over extending Thread.**

**Reasons:**
1. **Inheritance**: Java doesn't support multiple inheritance. If you extend Thread, you can't extend another class
2. **Separation of Concerns**: Runnable separates task from execution mechanism
3. **Reusability**: Same Runnable can be used with different executors
4. **Resource Pooling**: Works with thread pools (ExecutorService)

**Comparison:**
```java
// Using Thread (Not Recommended)
class MyThread extends Thread {
    public void run() {
        // task
    }
}
new MyThread().start();

// Using Runnable (Recommended)
class MyTask implements Runnable {
    public void run() {
        // task
    }
}
new Thread(new MyTask()).start();

// Or with Lambda (Java 8+)
new Thread(() -> {
    // task
}).start();

// Best: Using ExecutorService
ExecutorService executor = Executors.newFixedThreadPool(10);
executor.submit(() -> {
    // task
});
```

---

### 25. What is Enum type safety?

**Detailed Answer:**
Enum type safety ensures that an enum variable can only hold values from its own enum type, preventing invalid assignments at compile time.

**Type Safety Example:**
```java
enum DayOfWeek {
    MONDAY, TUESDAY, WEDNESDAY
}

enum DayOfMonth {
    FIRST, SECOND, THIRD
}

DayOfWeek day = DayOfWeek.MONDAY;  // ✓ OK
day = DayOfMonth.FIRST;  // ✗ Compile Error!
```

**Benefits:**
1. **Compile-time checking**: Invalid values caught before runtime
2. **No invalid states**: Can't assign arbitrary values
3. **Switch statement exhaustiveness**: Compiler warns if cases are missing
4. **Better than int constants**: No magic numbers

**Comparison with Constants:**
```java
// Old way (Not type-safe)
public static final int MONDAY = 1;
public static final int RED = 1;
int day = MONDAY;
day = RED;  // ✓ Compiles but logically wrong!

// Enum way (Type-safe)
DayOfWeek day = DayOfWeek.MONDAY;
day = Color.RED;  // ✗ Compile error!
```

---

### 26. How does autoboxing of integers work?

**Detailed Answer:**
Autoboxing is the automatic conversion between primitive types and their wrapper objects, primarily using the `valueOf()` method.

**Under the Hood:**
```java
// What you write
Integer num = 5;

// What happens (autoboxing)
Integer num = Integer.valueOf(5);

// Unboxing
int primitive = num;

// What happens
int primitive = num.intValue();
```

**Integer Cache:**
Java caches Integer objects from -128 to 127 for performance:

```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);  // true (same cached object)

Integer c = 128;
Integer d = 128;
System.out.println(c == d);  // false (different objects)
```

**Performance Consideration:**
```java
// Inefficient (creates Integer objects)
Integer sum = 0;
for (int i = 0; i < 1000; i++) {
    sum += i;  // Autoboxing and unboxing in each iteration
}

// Efficient (uses primitive)
int sum = 0;
for (int i = 0; i < 1000; i++) {
    sum += i;
}
```

---

### 27. Difference between PATH and CLASSPATH?

**Detailed Answer:**

| PATH | CLASSPATH |
|------|-----------|
| Operating system environment variable | Java-specific environment variable |
| Locates executable files (.exe, .sh) | Locates .class files and JARs |
| Used by OS to find commands | Used by JVM to find classes |
| Example: java, javac commands | Example: application classes, libraries |

**PATH Example:**
```bash
# Windows
PATH=C:\Program Files\Java\jdk-11\bin

# This allows running: java, javac from anywhere
```

**CLASSPATH Example:**
```bash
# Windows
CLASSPATH=.;C:\myapp\classes;C:\myapp\lib\*

# Tells JVM where to find MyClass.class
java MyClass
```

**Best Practice:** Use `-cp` or `-classpath` flag instead of setting CLASSPATH globally:
```bash
java -cp ".:lib/*" com.myapp.Main
```

---

### 28. Difference between Overloading and Overriding?

**Detailed Answer:**

| Overloading | Overriding |
|-------------|------------|
| Same class | Subclass |
| Different parameters | Same signature |
| Compile-time (static binding) | Runtime (dynamic binding) |
| Return type can be different | Return type must be same or covariant |
| Can change access modifier | Cannot reduce visibility |

**Overloading Example:**
```java
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
    int add(int a, int b, int c) { return a + b + c; }
}
```

**Overriding Example:**
```java
class Animal {
    void sound() { System.out.println("Animal sound"); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }
}
```

**Key Points:**
- Overloading: "Compile-time polymorphism" - decided at compile time
- Overriding: "Runtime polymorphism" - decided at runtime based on object type

---

### 29. How to prevent a class from being subclassed?

**Detailed Answer:**
Use the `final` keyword before the class declaration.

**Method 1: Final Class (Recommended)**
```java
public final class ImmutableClass {
    // Cannot be extended
}

// This will cause compile error
class SubClass extends ImmutableClass {  // ✗ Error!
}
```

**Method 2: Private Constructor**
```java
public class Utility {
    private Utility() {
        // Prevents instantiation and subclassing
    }
    
    public static void helperMethod() {
        // utility methods
    }
}
```

**Real-world Examples:**
- String class is final
- Wrapper classes (Integer, Double) are final
- Math class has private constructor

**Reasons to Make Class Final:**
1. Security (prevent malicious overriding)
2. Immutability (like String)
3. Design decision (class not meant to be extended)
4. Performance (JVM optimizations)

---

### 30. How to prevent instantiation of your class?

**Detailed Answer:**
Make the constructor private or throw an exception from it.

**Method 1: Private Constructor (Utility Classes)**
```java
public class MathUtils {
    private MathUtils() {
        throw new AssertionError("Cannot instantiate utility class");
    }
    
    public static int add(int a, int b) {
        return a + b;
    }
}
```

**Method 2: Abstract Class**
```java
public abstract class Shape {
    // Can't instantiate directly
    // Must create subclass
}
```

**Method 3: Interface**
```java
public interface Constants {
    int MAX_VALUE = 100;
    // Interfaces can't be instantiated
}
```

**Use Cases:**
- Utility classes (Collections, Arrays, Math)
- Factory classes
- Classes with only static methods

---

### 31. Difference between StringBuilder and StringBuffer?

**Detailed Answer:**

| StringBuilder | StringBuffer |
|---------------|--------------|
| Not synchronized | Synchronized |
| Faster | Slower |
| Single-threaded use | Multi-threaded use |
| Java 1.5+ | Java 1.0+ |

**Performance Example:**
```java
// Single-threaded: StringBuilder is ~20% faster
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i);
}

// Multi-threaded: Use StringBuffer
StringBuffer sf = new StringBuffer();
// Safe for concurrent access
```

**When to Use:**
- **StringBuilder**: 99% of cases (single-threaded)
- **StringBuffer**: Only when shared across threads
- **String concatenation**: Use StringBuilder instead of + in loops

**Why Not String?**
```java
// Bad: Creates 10,000 intermediate String objects
String s = "";
for (int i = 0; i < 10000; i++) {
    s += i;  // Creates new String each time
}

// Good: Creates one StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i);
}
```

---

### 32. Difference between Polymorphism and Inheritance?

**Detailed Answer:**

**Inheritance:** Mechanism for code reuse and establishing "is-a" relationship
**Polymorphism:** Ability of objects to take multiple forms

| Inheritance | Polymorphism |
|-------------|--------------|
| Parent-child relationship | One interface, multiple implementations |
| Code reusability | Flexibility in behavior |
| Static relationship | Dynamic behavior |

**Example:**
```java
// Inheritance: Dog IS-A Animal
class Animal {
    void eat() { System.out.println("Eating"); }
}

class Dog extends Animal {
    void bark() { System.out.println("Barking"); }
}

// Polymorphism: Same method, different behavior
class Animal {
    void sound() { System.out.println("Some sound"); }
}

class Dog extends Animal {
    @Override
    void sound() { System.out.println("Bark"); }
}

class Cat extends Animal {
    @Override
    void sound() { System.out.println("Meow"); }
}

// Polymorphic behavior
Animal animal = new Dog();
animal.sound();  // Outputs: Bark

animal = new Cat();
animal.sound();  // Outputs: Meow
```

**Key Point:** Inheritance enables polymorphism, but they serve different purposes.

---

### 33. Can we override static methods?

**Detailed Answer:**
**No**, static methods cannot be overridden. They can be **hidden** but not overridden.

**Reason:**
- Overriding is resolved at **runtime** (dynamic binding)
- Static methods are resolved at **compile time** (static binding)
- Static methods belong to the class, not instances

**Example:**
```java
class Parent {
    static void display() {
        System.out.println("Parent static");
    }
}

class Child extends Parent {
    static void display() {  // This is method hiding, not overriding
        System.out.println("Child static");
    }
}

// Test
Parent obj = new Child();
obj.display();  // Outputs: "Parent static" (not Child!)

// Reference type decides which method is called
Child obj2 = new Child();
obj2.display();  // Outputs: "Child static"
```

**Key Difference:**
- **Overriding**: Object type determines method (runtime)
- **Method Hiding**: Reference type determines method (compile time)

---

### 34. Can we access private methods outside the class?

**Detailed Answer:**
**No**, private methods are only accessible within the same class where they are defined. They cannot be accessed from outside the class, including subclasses.

**Access Levels:**
```java
class MyClass {
    private void privateMethod() {
        System.out.println("Private method");
    }
    
    public void publicMethod() {
        privateMethod();  // ✓ OK - Same class
    }
}

class AnotherClass {
    void test() {
        MyClass obj = new MyClass();
        obj.privateMethod();  // ✗ Compile Error!
    }
}
```

**Exception - Reflection:**
You CAN access private methods using Java Reflection (though not recommended):
```java
MyClass obj = new MyClass();
Method method = MyClass.class.getDeclaredMethod("privateMethod");
method.setAccessible(true);  // Bypass access control
method.invoke(obj);  // Calls private method
```

**Access Modifier Summary:**
- **private**: Only within the same class
- **default** (no modifier): Same package
- **protected**: Same package + subclasses
- **public**: Everywhere

---

### 35. Difference between Interface and Abstract Class?

**Detailed Answer:**

| Feature | Interface | Abstract Class |
|---------|-----------|----------------|
| **Multiple Inheritance** | A class can implement multiple | Can extend only one |
| **Methods** | All methods abstract (before Java 8) | Can have both abstract and concrete methods |
| **Variables** | public static final (constants only) | Can have instance variables |
| **Constructor** | Cannot have | Can have |
| **Access Modifiers** | Methods are public by default | Can use any access modifier |
| **When to Use** | Define contract/capability | Share common code among related classes |

**Java 8+ Changes:**
```java
// Interface with default and static methods
interface Drawable {
    void draw();  // Abstract method
    
    default void display() {  // Default method
        System.out.println("Displaying");
    }
    
    static void info() {  // Static method
        System.out.println("Info");
    }
}

// Abstract class
abstract class Shape {
    protected String color;  // Instance variable
    
    public Shape(String color) {  // Constructor
        this.color = color;
    }
    
    abstract void draw();  // Abstract method
    
    void display() {  // Concrete method
        System.out.println("Color: " + color);
    }
}
```

**Real-world Examples:**
- **Interface**: `Serializable`, `Comparable` (defining capability)
- **Abstract Class**: `HttpServlet`, `InputStream` (sharing common implementation)

**Design Decision:**
- Use **Interface** when unrelated classes need to implement the same contract
- Use **Abstract Class** when you have common code to share among related classes

---

### 36. Difference between DOM and SAX Parser?

**Detailed Answer:**

| Feature | DOM Parser | SAX Parser |
|---------|-----------|------------|
| **Full Form** | Document Object Model | Simple API for XML |
| **Parsing Type** | Tree-based | Event-based |
| **Memory Usage** | Loads entire XML into memory | Processes sequentially, low memory |
| **Speed** | Slower for large files | Faster for large files |
| **Random Access** | Yes (can navigate back and forth) | No (forward-only) |
| **Modification** | Can modify XML | Read-only |
| **Best For** | Small to medium files | Large files |

**DOM Parser Example:**
```java
DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
DocumentBuilder builder = factory.newDocumentBuilder();
Document doc = builder.parse(new File("data.xml"));

// Entire XML loaded in memory as tree
Element root = doc.getDocumentElement();
NodeList nodes = root.getElementsByTagName("employee");

// Can navigate anywhere
for (int i = 0; i < nodes.getLength(); i++) {
    Element emp = (Element) nodes.item(i);
    // Access any element randomly
}
```

**SAX Parser Example:**
```java
SAXParserFactory factory = SAXParserFactory.newInstance();
SAXParser parser = factory.newSAXParser();

DefaultHandler handler = new DefaultHandler() {
    @Override
    public void startElement(String uri, String localName, 
                           String qName, Attributes attributes) {
        // Event fired when element starts
        System.out.println("Start: " + qName);
    }
    
    @Override
    public void characters(char[] ch, int start, int length) {
        // Event fired for element content
        System.out.println("Content: " + new String(ch, start, length));
    }
};

parser.parse(new File("data.xml"), handler);
```

**Performance Comparison:**
- **Small file (< 10MB)**: DOM is faster and more convenient
- **Large file (> 100MB)**: SAX is much more efficient
- **Memory**: DOM can consume 5-10x more memory than SAX

**When to Use:**
- **DOM**: Configuration files, small data sets, need to modify XML
- **SAX**: Log files, large data processing, one-time read operations

---

### 37. Difference between throw and throws?

**Detailed Answer:**

| throw | throws |
|-------|--------|
| **Keyword used to** throw an exception | **Keyword used to** declare exceptions |
| Used inside method body | Used in method signature |
| Can throw only one exception at a time | Can declare multiple exceptions |
| Followed by exception instance | Followed by exception class name |
| Used for explicit exception throwing | Used for exception propagation |

**throw Example:**
```java
public void validateAge(int age) {
    if (age < 18) {
        // throw keyword throws actual exception
        throw new IllegalArgumentException("Age must be 18+");
    }
    System.out.println("Valid age");
}
```

**throws Example:**
```java
// throws keyword declares that method might throw exception
public void readFile(String path) throws IOException, FileNotFoundException {
    FileReader file = new FileReader(path);
    // Method declares it might throw exceptions
    // Caller must handle or propagate
}

// Caller must handle
public void processFile() {
    try {
        readFile("data.txt");
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

**Combined Example:**
```java
public void withdraw(double amount) throws InsufficientFundsException {
    if (amount > balance) {
        // throw actually throws the exception
        throw new InsufficientFundsException("Not enough balance");
    }
    balance -= amount;
}
// throws declares the exception in method signature
```

**Key Points to Remember:**
1. **throw** is used to explicitly raise an exception
2. **throws** is used to inform the caller about possible exceptions
3. **throw** is followed by an object: `throw new Exception()`
4. **throws** is followed by class names: `throws IOException, SQLException`

---

### 38. Difference between Fail-Fast and Fail-Safe Iterators?

**Detailed Answer:**

| Fail-Fast | Fail-Safe |
|-----------|-----------|
| Throws `ConcurrentModificationException` | Does NOT throw exception |
| Works on original collection | Works on clone/copy |
| Memory efficient | Uses more memory |
| Examples: ArrayList, HashMap, HashSet | Examples: CopyOnWriteArrayList, ConcurrentHashMap |

**Fail-Fast Example:**
```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String value = iterator.next();
    list.add("D");  // ✗ ConcurrentModificationException!
}
```

**How Fail-Fast Works:**
- Maintains a `modCount` (modification count)
- Each structural change increments `modCount`
- Iterator checks if `modCount` changed during iteration
- If changed, throws `ConcurrentModificationException`

**Fail-Safe Example:**
```java
List<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");
list.add("C");

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String value = iterator.next();
    list.add("D");  // ✓ No exception! Works on snapshot
}
// Iterator works on copy, so original can be modified
```

**Important Notes:**
- Fail-fast is best effort, not guaranteed
- Fail-safe iterators may not reflect latest changes
- Use fail-safe for concurrent modifications
- Remove through iterator.remove() is safe in fail-fast

**Correct Way to Remove in Fail-Fast:**
```java
Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String value = iterator.next();
    if (value.equals("B")) {
        iterator.remove();  // ✓ Safe way to remove
    }
}
```

---

### 39. Difference between Iterator and Enumeration?

**Detailed Answer:**

| Iterator | Enumeration |
|----------|-------------|
| Universal, works with all collections | Legacy, mainly for Vector and Hashtable |
| Has `remove()` method | Cannot remove elements |
| Method names: `hasNext()`, `next()` | Method names: `hasMoreElements()`, `nextElement()` |
| Fail-fast by default | Fail-safe (doesn't throw exceptions) |
| Introduced in Java 1.2 | Introduced in Java 1.0 |

**Enumeration Example:**
```java
Vector<String> vector = new Vector<>();
vector.add("A");
vector.add("B");

Enumeration<String> enumeration = vector.elements();
while (enumeration.hasMoreElements()) {
    String element = enumeration.nextElement();
    System.out.println(element);
    // Cannot remove elements
}
```

**Iterator Example:**
```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");

Iterator<String> iterator = list.iterator();
while (iterator.hasNext()) {
    String element = iterator.next();
    System.out.println(element);
    
    if (element.equals("A")) {
        iterator.remove();  // ✓ Can remove
    }
}
```

**Method Comparison:**
```java
// Enumeration (old)
hasMoreElements()
nextElement()

// Iterator (modern)
hasNext()
next()
remove()  // Additional functionality
```

**Best Practice:** Always use Iterator instead of Enumeration. Enumeration is kept only for backward compatibility with legacy code.

---

### 40. What is IdentityHashMap?

**Detailed Answer:**
IdentityHashMap is a special implementation of Map that uses reference equality (`==`) instead of object equality (`equals()`) when comparing keys.

**Normal HashMap vs IdentityHashMap:**
```java
// Regular HashMap (uses equals())
Map<String, String> hashMap = new HashMap<>();
String key1 = new String("key");
String key2 = new String("key");

hashMap.put(key1, "value1");
hashMap.put(key2, "value2");
System.out.println(hashMap.size());  // 1 (same logical key)

// IdentityHashMap (uses ==)
Map<String, String> identityMap = new IdentityHashMap<>();
identityMap.put(key1, "value1");
identityMap.put(key2, "value2");
System.out.println(identityMap.size());  // 2 (different objects)
```

**How It Works:**
- Uses `System.identityHashCode()` instead of `hashCode()`
- Compares keys with `==` instead of `equals()`
- Two keys are equal only if they reference the same object

**Real-world Use Cases:**

1. **Serialization**: Tracking which objects have been serialized
```java
Map<Object, Integer> serialized = new IdentityHashMap<>();
// Track actual object instances, not logical equality
```

2. **Proxy Objects**: Maintaining object-to-proxy mappings
```java
Map<Object, Proxy> proxyCache = new IdentityHashMap<>();
```

3. **Topology Preservation**: Deep copying where you need to preserve object identity
```java
Map<Object, Object> visited = new IdentityHashMap<>();
// Avoid infinite loops in circular references
```

**Example Demonstration:**
```java
String s1 = "test";
String s2 = "test";
String s3 = new String("test");

IdentityHashMap<String, Integer> map = new IdentityHashMap<>();
map.put(s1, 1);  // s1 and s2 point to same object in string pool
map.put(s2, 2);  // Overwrites s1's value
map.put(s3, 3);  // Different object, new entry

System.out.println(map.size());  // 2 entries
System.out.println(map.get(s1)); // 2 (same as s2)
System.out.println(map.get(s3)); // 3
```

**Important Note:** IdentityHashMap violates the general contract of Map interface, so use it only when you specifically need reference equality.

---

### 41. What is String Pool in Java?

**Detailed Answer:**
String Pool (also called String Intern Pool) is a special memory region in Java heap where String literals are stored to optimize memory usage and improve performance.

**How String Pool Works:**
```java
// String literals go to pool
String s1 = "Hello";  // Created in pool
String s2 = "Hello";  // Reuses same object from pool
System.out.println(s1 == s2);  // true (same reference)

// new keyword creates object in heap
String s3 = new String("Hello");  // New object in heap
System.out.println(s1 == s3);  // false (different references)

// intern() moves string to pool
String s4 = s3.intern();  // Returns reference from pool
System.out.println(s1 == s4);  // true (same reference)
```

**Memory Location (Important Change in JDK 7):**
- **Before JDK 7**: String Pool was in PermGen space (fixed size)
- **JDK 7+**: String Pool moved to Heap (can be garbage collected)

**Benefits of String Pool:**
1. **Memory Efficiency**: Identical strings share same memory
2. **Performance**: String comparison faster with `==`
3. **Immutability**: Safe sharing because strings are immutable

**Visual Representation:**
```
String s1 = "Java";     →  [String Pool]
                            "Java" ←
String s2 = "Java";     →      ↑
                               |
String s3 = new String("Java"); → [Heap] "Java" (different object)
```

**intern() Method Usage:**
```java
String s1 = new String("Hello").intern();
String s2 = "Hello";
System.out.println(s1 == s2);  // true

// Useful when working with strings from external sources
String fromDatabase = resultSet.getString("name").intern();
// Now can use == for comparison
```

**Performance Consideration:**
```java
// Bad: Creates many String objects
String result = "";
for (int i = 0; i < 10000; i++) {
    result += i;  // Creates new String each time
}

// Good: Uses StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

**String Pool Size:**
You can configure string pool size with JVM parameter:
```bash
-XX:StringTableSize=100000
```

---

### 42. Can Serializable classes have non-serializable fields?

**Detailed Answer:**
**Yes**, but you must mark them as `transient` or `static`, otherwise you'll get a `NotSerializableException` at runtime.

**Problem Example:**
```java
class Employee implements Serializable {
    private String name;
    private Department dept;  // ✗ If Department is not Serializable
    // This will throw NotSerializableException
}
```

**Solution 1: Mark as transient**
```java
class Employee implements Serializable {
    private String name;
    private transient Department dept;  // Won't be serialized
    
    // After deserialization, dept will be null
}
```

**Solution 2: Make the field Serializable**
```java
class Employee implements Serializable {
    private String name;
    private Department dept;  // ✓ If Department implements Serializable
}

class Department implements Serializable {
    private String name;
}
```

**Solution 3: Custom Serialization**
```java
class Employee implements Serializable {
    private String name;
    private transient Department dept;
    
    private void writeObject(ObjectOutputStream out) throws IOException {
        out.defaultWriteObject();
        // Manually serialize non-serializable field
        out.writeObject(dept.getName());
    }
    
    private void readObject(ObjectInputStream in) 
            throws IOException, ClassNotFoundException {
        in.defaultReadObject();
        // Manually deserialize
        String deptName = (String) in.readObject();
        dept = new Department(deptName);
    }
}
```

**Static Fields:**
```java
class Employee implements Serializable {
    private String name;
    private static int count;  // Static fields are NOT serialized
    // They belong to class, not instance
}
```

**Key Points:**
1. `transient` fields are not serialized (value will be null/0/false after deserialization)
2. `static` fields are not serialized (belong to class, not instance)
3. Can implement custom serialization with `writeObject()`/`readObject()`
4. Parent class fields are serialized only if parent implements Serializable

---

### 43. Difference between 'this' and 'super' keywords?

**Detailed Answer:**

| this | super |
|------|-------|
| Refers to current object | Refers to parent class object |
| Access current class members | Access parent class members |
| Call current class constructor | Call parent class constructor |
| Used in any method | Mainly used when overriding |

**'this' Keyword Examples:**

```java
class Employee {
    private String name;
    private int age;
    
    // 1. Distinguish instance variable from parameter
    public Employee(String name, int age) {
        this.name = name;  // this.name = instance variable
        this.age = age;
    }
    
    // 2. Call another constructor in same class
    public Employee(String name) {
        this(name, 0);  // Calls Employee(String, int)
    }
    
    // 3. Pass current object as parameter
    public void display() {
        printEmployee(this);
    }
    
    // 4. Return current object
    public Employee setName(String name) {
        this.name = name;
        return this;  // Method chaining
    }
}
```

**'super' Keyword Examples:**

```java
class Animal {
    protected String name = "Animal";
    
    public Animal(String name) {
        this.name = name;
    }
    
    public void eat() {
        System.out.println("Animal eating");
    }
}

class Dog extends Animal {
    private String name = "Dog";
    
    public Dog(String name) {
        // 1. Call parent constructor (must be first statement)
        super(name);
    }
    
    public void display() {
        // 2. Access parent class variable
        System.out.println("Parent name: " + super.name);
        System.out.println("Child name: " + this.name);
    }
    
    @Override
    public void eat() {
        // 3. Call parent class method
        super.eat();
        System.out.println("Dog eating");
    }
}
```

**Common Use Cases:**

**Constructor Chaining:**
```java
class Person {
    public Person() {
        this("Unknown");  // Calls Person(String)
    }
    
    public Person(String name) {
        System.out.println("Person: " + name);
    }
}

class Employee extends Person {
    public Employee() {
        super("Employee");  // Calls Person(String)
    }
}
```

**Method Chaining with 'this':**
```java
class Builder {
    private String name;
    private int age;
    
    public Builder setName(String name) {
        this.name = name;
        return this;
    }
    
    public Builder setAge(int age) {
        this.age = age;
        return this;
    }
}

// Usage
Builder builder = new Builder()
    .setName("John")
    .setAge(30);
```

**Important Rules:**
1. `super()` or `this()` must be the first statement in constructor
2. Cannot use both `super()` and `this()` in same constructor
3. `this` can be used anywhere in class
4. `super` is mainly used to access parent class members

---

### 44. Difference between Comparator and Comparable?

**Detailed Answer:**

| Comparable | Comparator |
|------------|------------|
| In same class | External class |
| Single sorting sequence | Multiple sorting sequences |
| Uses `compareTo()` method | Uses `compare()` method |
| Natural ordering | Custom ordering |
| In `java.lang` package | In `java.util` package |

**Comparable Example (Natural Ordering):**
```java
class Employee implements Comparable<Employee> {
    private String name;
    private int age;
    
    @Override
    public int compareTo(Employee other) {
        // Natural ordering by age
        return this.age - other.age;
        // or: return Integer.compare(this.age, other.age);
    }
}

// Usage
List<Employee> employees = new ArrayList<>();
Collections.sort(employees);  // Uses compareTo()
```

**Comparator Example (Custom Ordering):**
```java
class Employee {
    private String name;
    private int age;
    private double salary;
}

// Sort by name
class NameComparator implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        return e1.getName().compareTo(e2.getName());
    }
}

// Sort by salary
class SalaryComparator implements Comparator<Employee> {
    @Override
    public int compare(Employee e1, Employee e2) {
        return Double.compare(e1.getSalary(), e2.getSalary());
    }
}

// Usage
Collections.sort(employees, new NameComparator());
Collections.sort(employees, new SalaryComparator());
```

**Java 8+ Lambda Syntax:**
```java
// Sort by name
employees.sort((e1, e2) -> e1.getName().compareTo(e2.getName()));

// Sort by age
employees.sort(Comparator.comparing(Employee::getAge));

// Sort by salary (descending)
employees.sort(Comparator.comparing(Employee::getSalary).reversed());

// Multiple criteria: first by age, then by name
employees.sort(Comparator.comparing(Employee::getAge)
                         .thenComparing(Employee::getName));
```

**Return Values:**
- **Negative** (`< 0`): First object comes before second
- **Zero** (`0`): Objects are equal
- **Positive** (`> 0`): First object comes after second

**When to Use:**
- **Comparable**: When there's one obvious natural ordering (e.g., integers by value)
- **Comparator**: When you need multiple sorting options or can't modify the class

**Real-world Examples:**
- `String` implements `Comparable` (alphabetical order)
- `Integer` implements `Comparable` (numerical order)
- `Collections.reverseOrder()` returns a `Comparator`

---

### 45. Difference between java.util.Date and java.sql.Date?

**Detailed Answer:**

| java.util.Date | java.sql.Date |
|----------------|---------------|
| Contains date AND time | Contains ONLY date (no time) |
| General purpose | Database operations |
| Has time component (hours, minutes, seconds) | Time set to 00:00:00 |
| Parent class | Extends java.util.Date |

**java.util.Date Example:**
```java
java.util.Date utilDate = new java.util.Date();
System.out.println(utilDate);
// Output: Wed Jan 21 14:30:45 IST 2026
// Contains both date and time
```

**java.sql.Date Example:**
```java
java.sql.Date sqlDate = new java.sql.Date(System.currentTimeMillis());
System.out.println(sqlDate);
// Output: 2026-01-21
// Only date, no time component
```

**Conversion Between Them:**
```java
// util.Date to sql.Date
java.util.Date utilDate = new java.util.Date();
java.sql.Date sqlDate = new java.sql.Date(utilDate.getTime());

// sql.Date to util.Date
java.sql.Date sqlDate = new java.sql.Date(System.currentTimeMillis());
java.util.Date utilDate = new java.util.Date(sqlDate.getTime());
```

**Database Usage:**
```java
// Inserting date into database
PreparedStatement ps = connection.prepareStatement(
    "INSERT INTO employees (name, hire_date) VALUES (?, ?)"
);
ps.setString(1, "John");
ps.setDate(2, new java.sql.Date(System.currentTimeMillis()));

// Retrieving date from database
ResultSet rs = statement.executeQuery("SELECT hire_date FROM employees");
java.sql.Date hireDate = rs.getDate("hire_date");
```

**Important Notes:**
1. **Deprecated**: Both classes are largely deprecated. Use `java.time` API (Java 8+)
2. **sql.Date**: Overrides time methods to throw IllegalArgumentException
3. **Precision**: sql.Date loses time information

**Modern Alternative (Java 8+):**
```java
// Use LocalDate for dates without time
LocalDate localDate = LocalDate.now();
System.out.println(localDate);  // 2026-01-21

// Use LocalDateTime for date with time
LocalDateTime localDateTime = LocalDateTime.now();
System.out.println(localDateTime);  // 2026-01-21T14:30:45.123

// Database conversion
java.sql.Date sqlDate = java.sql.Date.valueOf(localDate);
LocalDate converted = sqlDate.toLocalDate();
```

---

### 46. Why are wait() and notify() methods in Object class?

**Detailed Answer:**
`wait()` and `notify()` are in the Object class because they work with object-level locks (monitors), and every object in Java can potentially be a lock.

**Key Reasons:**

1. **Every Object Can Be a Lock:**
```java
Object lock = new Object();
synchronized(lock) {  // Any object can be a monitor
    lock.wait();
    lock.notify();
}
```

2. **Intrinsic Lock (Monitor):**
Every Java object has an intrinsic lock. When you use `synchronized`, you're acquiring that object's lock. `wait()` and `notify()` work with this same lock.

3. **Not Thread-Specific:**
These methods are about object state, not thread state. Multiple threads can wait on the same object.

**How They Work Together:**
```java
class SharedResource {
    private List<Integer> queue = new ArrayList<>();
    private static final int MAX_SIZE = 10;
    
    // Producer
    public synchronized void produce(int value) throws InterruptedException {
        while (queue.size() == MAX_SIZE) {
            wait();  // Release lock and wait
        }
        queue.add(value);
        notify();  // Wake up consumer
    }
    
    // Consumer
    public synchronized int consume() throws InterruptedException {
        while (queue.isEmpty()) {
            wait();  // Release lock and wait
        }
        int value = queue.remove(0);
        notify();  // Wake up producer
        return value;
    }
}
```

**Why Not in Thread Class?**
- Multiple threads might wait on the same object
- One thread might wait on multiple objects
- The lock belongs to the object, not the thread

**Important Rules:**
1. Must be called from synchronized context (otherwise IllegalMonitorStateException)
2. `wait()` releases the lock temporarily
3. `notify()` doesn't release the lock immediately (waits for synchronized block to end)

**Example of Incorrect Usage:**
```java
Object obj = new Object();
obj.wait();  // ✗ IllegalMonitorStateException!
// Must be in synchronized block
```

**Correct Usage:**
```java
Object obj = new Object();
synchronized(obj) {
    obj.wait();  // ✓ Correct
}
```

---

### 47. Why doesn't Java support multiple inheritance?

**Detailed Answer:**
Java doesn't support multiple inheritance (of classes) primarily to avoid the **Diamond Problem** and reduce complexity, though Java 8+ supports multiple inheritance of type through interfaces.

**The Diamond Problem:**
```
      Animal
      /    \
   Dog      Cat
      \    /
      PetDog

Which eat() method should PetDog inherit?
```

**In C++ (Diamond Problem):**
```cpp
class Animal {
    void eat() { }
};

class Dog : public Animal { };
class Cat : public Animal { };

class PetDog : public Dog, public Cat {  // Ambiguity!
    // Which eat() method to inherit?
};
```

**Java's Solution:**
```java
// ✗ Not allowed - Compile error
class PetDog extends Dog, Cat {  // Compilation error!
}

// ✓ Allowed - Multiple interfaces
interface Walkable {
    void walk();
}

interface Swimmable {
    void swim();
}

class Duck implements Walkable, Swimmable {
    public void walk() { }
    public void swim() { }
}
```

**Java 8+ Diamond Problem with Default Methods:**
```java
interface A {
    default void display() {
        System.out.println("A");
    }
}

interface B {
    default void display() {
        System.out.println("B");
    }
}

// Must explicitly resolve conflict
class C implements A, B {
    @Override
    public void display() {
        A.super.display();  // Choose which one
        // Or provide own implementation
    }
}
```

**Multiple Inheritance Flavors in Java:**

1. **Multiple Inheritance of Type** (✓ Allowed)
```java
interface I1 { }
interface I2 { }
class MyClass implements I1, I2 { }  // OK
```

2. **Multiple Inheritance of State** (✗ Not Allowed)
```java
class C1 { int x; }
class C2 { int y; }
class C3 extends C1, C2 { }  // Compilation error!
```

3. **Multiple Inheritance of Implementation** (✓ Partially Allowed via Interfaces)
```java
interface I1 {
    default void method() { }
}
interface I2 {
    default void method() { }
}
class MyClass implements I1, I2 {  // Must resolve
    public void method() {
        I1.super.method();
    }
}
```

**Advantages of Java's Approach:**
1. **Simplicity**: Easier to understand and maintain
2. **No ambiguity**: Clear inheritance hierarchy
3. **Composition over inheritance**: Encourages better design

**Alternative Design Pattern:**
```java
// Use composition instead
class PetDog {
    private Dog dog;
    private Cat cat;
    
    public void bark() {
        dog.bark();
    }
    
    public void meow() {
        cat.meow();
    }
}
```

---

### 48. Difference between Checked and Unchecked Exceptions?

**Detailed Answer:**

| Checked Exception | Unchecked Exception |
|-------------------|---------------------|
| Checked at compile time | Checked at runtime |
| Must handle with try-catch or throws | Optional to handle |
| Extends Exception (but not RuntimeException) | Extends RuntimeException |
| Recoverable conditions | Programming errors |
| Examples: IOException, SQLException | Examples: NullPointerException, ArrayIndexOutOfBoundsException |

**Checked Exception Example:**
```java
// Must handle or declare
public void readFile(String path) throws IOException {  // declare
    FileReader file = new FileReader(path);
}

// Or handle
public void readFile(String path) {
    try {
        FileReader file = new FileReader(path);  // must catch
    } catch (IOException e) {
        e.printStackTrace();
    }
}
```

**Unchecked Exception Example:**
```java
// No requirement to handle
public int divide(int a, int b) {
    return a / b;  // Can throw ArithmeticException
    // No need to declare or catch
}

// But you CAN handle if you want
public int divide(int a, int b) {
    try {
        return a / b;
    } catch (ArithmeticException e) {
        System.out.println("Cannot divide by zero");
        return 0;
    }
}
```

**Exception Hierarchy:**
```
Throwable
├── Error (unchecked)
│   ├── OutOfMemoryError
│   └── StackOverflowError
└── Exception
    ├── IOException (checked)
    ├── SQLException (checked)
    └── RuntimeException (unchecked)
        ├── NullPointerException
        ├── ArithmeticException
        └── ArrayIndexOutOfBoundsException
```

**Common Checked Exceptions:**
- `IOException`: File operations
- `SQLException`: Database operations
- `ClassNotFoundException`: Class loading
- `InterruptedException`: Thread operations

**Common Unchecked Exceptions:**
- `NullPointerException`: Accessing null reference
- `ArrayIndexOutOfBoundsException`: Invalid array index
- `IllegalArgumentException`: Invalid method argument
- `ArithmeticException`: Math errors (divide by zero)

**When to Use Which:**

**Checked Exceptions:**
- Conditions from which caller can reasonably recover
- External resource failures (file, network, database)
- Example: File not found → user can provide different path

**Unchecked Exceptions:**
- Programming errors/bugs
- Conditions that shouldn't normally occur
- Example: NullPointerException → fix the code

**Best Practices:**
```java
// Good: Specific checked exception for recoverable condition
public User findUser(int id) throws UserNotFoundException {
    // Caller can handle and retry
}

// Good: Unchecked exception for programming error
public void setAge(int age) {
    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}

// Bad: Catching generic Exception
try {
    // code
} catch (Exception e) {  // Too broad!
}

// Good: Catch specific exceptions
try {
    // code
} catch (IOException e) {
    // Handle IO issues
} catch (SQLException e) {
    // Handle DB issues
}
```

---

### 49. Difference between Error and Exception?

**Detailed Answer:**

| Error | Exception |
|-------|-----------|
| Serious problems | Reasonable to catch and handle |
| Cannot be recovered | Can be recovered |
| Unchecked (no need to declare) | Can be checked or unchecked |
| JVM/system issues | Application-level issues |
| Examples: OutOfMemoryError, StackOverflowError | Examples: IOException, NullPointerException |

**Error Examples:**
```java
// OutOfMemoryError - Cannot be handled
public void createLargeArray() {
    int[] arr = new int[Integer.MAX_VALUE];  // OutOfMemoryError
    // No point catching - application should terminate
}

// StackOverflowError - Usually from infinite recursion
public void recursiveMethod() {
    recursiveMethod();  // StackOverflowError
}
```

**Exception Examples:**
```java
// IOException - Can be handled
public void readFile() {
    try {
        FileReader file = new FileReader("data.txt");
    } catch (IOException e) {
        System.out.println("File not found, using default data");
        // Application continues
    }
}

// NullPointerException - Can be prevented/handled
public void processUser(User user) {
    if (user != null) {  // Prevent exception
        user.getName();
    }
}
```

**Hierarchy:**
```
Throwable
├── Error (Don't catch)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   ├── NoClassDefFoundError
│   └── VirtualMachineError
└── Exception (Can catch)
    ├── Checked Exceptions
    │   ├── IOException
    │   └── SQLException
    └── Unchecked Exceptions (RuntimeException)
        ├── NullPointerException
        └── ArrayIndexOutOfBoundsException
```

**Common Errors:**
1. **OutOfMemoryError**: Heap space exhausted
2. **StackOverflowError**: Stack space exhausted (usually recursion)
3. **NoClassDefFoundError**: Class was present during compile but not at runtime
4. **LinkageError**: Class loading issues

**Common Exceptions:**
1. **IOException**: File/network operations
2. **SQLException**: Database operations
3. **NullPointerException**: Accessing null
4. **IllegalArgumentException**: Invalid method arguments

**Should You Catch Errors?**
```java
// Generally NO - Don't catch Error
try {
    // code
} catch (Error e) {  // ✗ Bad practice!
    // Can't recover from Error
}

// Exception: Sometimes catch for logging/cleanup
try {
    // code
} catch (OutOfMemoryError e) {
    log.error("Out of memory!");
    // Cleanup and terminate gracefully
    System.exit(1);
}
```

**Best Practices:**
```java
// 1. Catch specific exceptions, not Error
try {
    processData();
} catch (IOException e) {  // ✓ Good
    handleIOError(e);
}

// 2. Don't catch Throwable (includes Error)
try {
    processData();
} catch (Throwable t) {  // ✗ Bad - catches Error too!
}

// 3. Let Errors propagate
public void myMethod() {
    // If OutOfMemoryError occurs, let it propagate
    // Don't try to handle
}
```

**When Error Occurs:**
- Application should typically terminate
- Log the error for debugging
- Perform minimal cleanup if possible
- Don't try to continue normal execution

**Real-world Analogy:**
- **Error**: House on fire (evacuate, can't fix it yourself)
- **Exception**: Flat tire (fixable, can continue journey)

---

### 50. Difference between Race Condition and Deadlock?

**Detailed Answer:**

| Race Condition | Deadlock |
|----------------|----------|
| Timing-dependent bug | Resource dependency cycle |
| Output depends on thread scheduling | Threads waiting for each other |
| Unpredictable results | Complete halt |
| Can be intermittent | Permanent until intervention |
| Prevention: Synchronization | Prevention: Lock ordering |

**Race Condition:**

**Definition:** When multiple threads access shared data concurrently, and outcome depends on the sequence/timing of execution.

**Example:**
```java
class Counter {
    private int count = 0;
    
    // Not thread-safe - Race condition!
    public void increment() {
        count++;  // Actually 3 operations:
        // 1. Read count
        // 2. Increment
        // 3. Write count
    }
}

// Two threads execute increment simultaneously:
// Thread 1: Read 0 → Calculate 1 → (interrupted)
// Thread 2: Read 0 → Calculate 1 → Write 1
// Thread 1: Write 1
// Result: 1 instead of 2!
```

**Solution:**
```java
class Counter {
    private int count = 0;
    
    // Thread-safe
    public synchronized void increment() {
        count++;
    }
    
    // Or use AtomicInteger
    private AtomicInteger atomicCount = new AtomicInteger(0);
    public void increment() {
        atomicCount.incrementAndGet();
    }
}
```

**Deadlock:**

**Definition:** Two or more threads are blocked forever, waiting for each other to release locks.

**Classic Example:**
```java
class DeadlockDemo {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized(lock1) {           // Thread 1 acquires lock1
            System.out.println("Lock1 acquired");
            Thread.sleep(100);
            
            synchronized(lock2) {       // Waits for lock2 (held by Thread 2)
                System.out.println("Lock2 acquired");
            }
        }
    }
    
    public void method2() {
        synchronized(lock2) {           // Thread 2 acquires lock2
            System.out.println("Lock2 acquired");
            Thread.sleep(100);
            
            synchronized(lock1) {       // Waits for lock1 (held by Thread 1)
                System.out.println("Lock1 acquired");
            }
        }
    }
}

// Thread 1 calls method1() - acquires lock1, waits for lock2
// Thread 2 calls method2() - acquires lock2, waits for lock1
// DEADLOCK! Both wait forever
```

**Deadlock Conditions (All 4 must be true):**
1. **Mutual Exclusion**: Resources cannot be shared
2. **Hold and Wait**: Thread holding resource waits for another
3. **No Preemption**: Resource cannot be forcibly taken
4. **Circular Wait**: Circular chain of threads waiting

**Deadlock Prevention:**
```java
// Solution 1: Lock Ordering
class SafeClass {
    private final Object lock1 = new Object();
    private final Object lock2 = new Object();
    
    public void method1() {
        synchronized(lock1) {        // Always acquire lock1 first
            synchronized(lock2) {    // Then lock2
                // work
            }
        }
    }
    
    public void method2() {
        synchronized(lock1) {        // Same order - lock1 first
            synchronized(lock2) {    // Then lock2
                // work
            }
        }
    }
}

// Solution 2: Timeout
Lock lock1 = new ReentrantLock();
Lock lock2 = new ReentrantLock();

if (lock1.tryLock(1000, TimeUnit.MILLISECONDS)) {
    try {
        if (lock2.tryLock(1000, TimeUnit.MILLISECONDS)) {
            try {
                // work
            } finally {
                lock2.unlock();
            }
        }
    } finally {
        lock1.unlock();
    }
}
```

**Race Condition vs Deadlock Summary:**

**Race Condition:**
```java
// Problem: Incorrect result
Thread 1: balance = 100
Thread 2: balance = 100
Both increment
Result: 101 (should be 102)
```

**Deadlock:**
```java
// Problem: No progress
Thread 1: Holds A, waits for B
Thread 2: Holds B, waits for A
Result: Both stuck forever
```

**Detection:**

**Race Condition Detection:**
- Run tests multiple times
- Use thread analysis tools (e.g., Thread Sanitizer)
- Look for inconsistent results
- Hard to reproduce

**Deadlock Detection:**
- Thread dumps show waiting threads
- jstack utility
- VisualVM, JConsole
- Look for circular dependencies
- Reproducible

**Real-world Examples:**

**Race Condition:**
- Bank account transfers
- Inventory management
- Web request counters

**Deadlock:**
- Database transactions
- Resource allocation systems
- Complex synchronization scenarios

---

## Summary Tips for Interview

**Key Preparation Points:**

1. **Understand the "Why"**: Don't just memorize, understand why certain design decisions were made

2. **Use Real Examples**: Relate concepts to real-world scenarios you've encountered

3. **Code Examples**: Be ready to write code on whiteboard or computer

4. **Compare and Contrast**: Many questions ask for differences - use tables in your mind

5. **Know Performance Implications**: Understand time/space complexity

6. **Stay Updated**: Know changes from Java 8, 11, 17, 21

7. **Be Honest**: If you don't know, say "I'm not sure, but I think..." or "I'd need to verify that"

8. **Ask Clarifying Questions**: Shows you think before answering

**Common Follow-up Areas:**
- Multithreading and Concurrency
- Collections Framework
- Exception Handling
- JVM Internals
- Design Patterns
- Modern Java Features (Streams, Lambdas, etc.)

Good luck with your interview!
