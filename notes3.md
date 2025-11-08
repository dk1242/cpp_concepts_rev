- c++ code -> preprocess code -> compilation -> object files + libraries -> linking -> .exe
- qt moc
- coding standard (maybe)
- class
- cpp modules
- Q_OBJECT macro
- virtual function (interfaces)
- <table style="border-collapse: collapse; width: 100%;"><thead><tr><td style="border: 1px solid grey; padding: 8px;">Feature</td><td style="border: 1px solid grey; padding: 8px;">Pointer</td><td style="border: 1px solid grey; padding: 8px;">Reference</td></tr></thead><tbody><tr><td style="border: 1px solid grey; padding: 8px;">Nullability</td><td style="border: 1px solid grey; padding: 8px;">Can be <code>nullptr</code></td><td style="border: 1px solid grey; padding: 8px;">Cannot be null</td></tr><tr><td style="border: 1px solid grey; padding: 8px;">Reassignment</td><td style="border: 1px solid grey; padding: 8px;">Can point to different objects</td><td style="border: 1px solid grey; padding: 8px;">Cannot be reassigned</td></tr><tr><td style="border: 1px solid grey; padding: 8px;">Syntax</td><td style="border: 1px solid grey; padding: 8px;">Requires <code>*</code> for dereferencing</td><td style="border: 1px solid grey; padding: 8px;">Acts as alias, no special syntax</td></tr><tr><td style="border: 1px solid grey; padding: 8px;">Memory Arithmetic</td><td style="border: 1px solid grey; padding: 8px;">Supports arithmetic operations</td><td style="border: 1px solid grey; padding: 8px;">Does not support arithmetic</td></tr><tr><td style="border: 1px solid grey; padding: 8px;">Initialization</td><td style="border: 1px solid grey; padding: 8px;">Can be declared without initialization</td><td style="border: 1px solid grey; padding: 8px;">Must be initialized immediately</td></tr></tbody></table>
- Need to understand
```cpp
std::vector<int> vec;
...
const std::vector<int>::iterator iter = // iter acts like a T* const
vec.begin();
*iter = 10; // OK, changes what iter points to
++iter; // error! iter is const
...
std::vector<int>::const_iterator cIter = // cIter acts like a const T* vec.begin();
*cIter = 10; // error! *cIter is const
++cIter; // fine, changes cIter
```

### Smart pointer
1. Automatic Memory Management:
Eliminates the need for manual delete, reducing the risk of memory leaks.
2. Exception Safety:
Ensures proper cleanup even when exceptions are thrown.
3. Avoids Dangling Pointers:
Automatically deletes memory when the pointer goes out of scope.
4. Reference Counting:
std::shared_ptr handles scenarios where multiple pointers share ownership.

- unique Ptr: a smart pointer that manages a dynamically allocated object, only one std::unique_ptr can manage the object at a time
    ```cpp
    unique_ptr<Rectangle> P1(new Rectangle(10, 5));
    // std::unique_ptr<int> ptr = std::make_unique<int>(10);
    cout << P1->area() << endl;

    unique_ptr<Rectangle> P2;

    // Copy the addres of P1 into p2
    P2 = move(P1);
    cout << P2->area();
    ```

- shared_ptr: A smart pointer that allows shared ownership of a dynamically allocated object, Maintains a reference count so that multiple std::shared_ptr instances can manage the same object.
    ```cpp
    shared_ptr<A> p1(new A);
    // Printing the address of the managed object
    cout << p1.get() << endl;
    p1->show(); // class A function
  
    // Creating a new shared pointer that shares ownership
    shared_ptr<A> p2(p1);
    p2->show();
  
    // Printing addresses of p1 and p2
    cout << p1.get() << endl;
    cout << p2.get() << endl;
  
    // Returns the number of shared_ptr objects
    // referring to the same managed object
    cout << p1.use_count() << endl; // 2
    cout << p2.use_count() << endl; // 2
  
    // Relinquishes ownership of p1 on the object
    // and pointer becomes NULL
    p1.reset();
    cout << p1.get() << endl; // This will print nullptr or 0
    cout << p2.use_count() << endl; // 1
    cout << p2.get() << endl;
    ```

- weak ptr: The weak_ptr, just like shared_ptr has the capability to point to the resource owned by another shared_ptr but without owning it.
    - a smart pointer that provides a non-owning reference to an object managed by a std::shared_ptr.
     the existence of a std::weak_ptr does not prevent the object from being destroyed when all std::shared_ptr instances managing it go out of scope.
    - For example, if two objects reference each other using std::shared_ptr, neither can be destroyed because the reference count will never reach zero. Using std::weak_ptr in such cases solves this problem.
    ```cpp
    shared_ptr<Object> sharedObjectA
        = make_shared<Object>(42);

    // creating weak pointer to the previously created
    // shared objects
    weak_ptr<Object> weakObjectA = sharedObjectA;

    // Access objects using weak_ptr
    if (!weakObjectA.expired()) {
        cout << "The value stored in sharedObjectA:"
             << (*weakObjectA.lock()).data << endl;
    }

    // deleting object
    sharedObjectA.reset();
    // another method 
    if (auto data = it->second.lock()) {
        return data;
    } else {
        std::cout << "Data for key \"" << key << "\" has expired." << std::endl;
    }
    ```
- A virtual function in C++ is a member function of a class that is declared using the virtual keyword. It allows runtime polymorphism, meaning the decision about which function to call is made at runtime, based on the type of the object being pointed to, rather than the type of the pointer.
    - When a base class pointer or reference is used to point to a derived class object, the overridden function in the derived class is called, instead of the base class version.
    - If a class has virtual functions, its destructor should also be declared as virtual to ensure proper cleanup of derived class objects when deleted through a base class pointer.
    ```cpp
    class Base {
    public:
        virtual ~Base() { // Virtual destructor
            std::cout << "Base destructor called" << std::endl;
        }
    };

    class Derived : public Base {
    public:
        ~Derived() {
            std::cout << "Derived destructor called" << std::endl;
        }
    };

    int main() {
        Base* obj = new Derived();
        delete obj; // Both destructors will be called
        return 0;
    }
    ```
- The type of the pointer (Base*) determines what methods are accessible, not the type of the object it points to.
- A virtual function is called a pure virtual function if it does not have any implementation and is assigned = 0. A class that contains a pure virtual function is called an abstract class.

- object slicing, when a derived object is copied to base object, the extra parts of derived are lost.
    ```cpp
    Derived d; // no new ptr
    Base b = d; // no & , b is pure Base object, not polymorphic anymore
    ...
    Derived derivedObj;
    Base* basePtr = &derivedObj;  // Use a pointer instead
    basePtr->display();  // Calls Derived's display
    ```
- RAII (RESOURCE AQUISITION IS INITIALIZATION) - ties resource management to object lifetime
    - aquire resources in constructor
    - release resources in destructor

## Rule of 3
The Rule of 3 states that if a class requires a user-defined destructor, copy constructor, or copy assignment operator, then it likely needs all three.

Why?

If a class manages a resource (e.g., dynamically allocated memory), the default implementations of these functions (provided by the compiler) may not perform the necessary cleanup or deep copy operations. Implementing all three ensures proper resource management.

Components of the Rule of 3:
### Destructor:
Cleans up resources when an object is destroyed.

Example:
```cpp
class MyClass {
private:
    int* data;
public:
    MyClass() { data = new int(42); }
    ~MyClass() { delete data; } // Proper cleanup
};
```
### Copy Constructor:
Creates a new object as a copy of an existing object.
Ensures a deep copy if the class uses dynamic resources.

Example:
```cpp
MyClass(const MyClass& other) {
    data = new int(*other.data); // Deep copy
}
```
### Copy Assignment Operator:
Assigns the values of one existing object to another.
Must handle self-assignment and ensure proper cleanup of existing resources before copying.

Example:
```cpp
MyClass& operator=(const MyClass& other) {
    if (this != &other) { // Avoid self-assignment
        delete data; // Cleanup existing resource
        data = new int(*other.data); // Deep copy
    }
    return *this;
}
```
### Move Constructor:
Transfers ownership of resources from one object to another, avoiding deep copies and improving performance.

Example:
```cpp
MyClass(MyClass&& other) noexcept {
    data = other.data; // Take ownership of the resource
    other.data = nullptr; // Leave the moved-from object in a valid state
}
```
### Move Assignment Operator:
Transfers ownership during assignment.

Example:
```cpp
MyClass& operator=(MyClass&& other) noexcept {
    if (this != &other) {
        delete data; // Cleanup existing resource
        data = other.data; // Take ownership of the resource
        other.data = nullptr; // Leave the moved-from object in a valid state
    }
    return *this;
}
```
## Rule of 0
The Rule of 0 is a guideline that suggests avoiding manual implementation of special member functions altogether by relying on RAII (Resource Acquisition Is Initialization) and standard library types.
### Why Rule of 0?
Modern C++ provides tools like smart pointers (std::unique_ptr, std::shared_ptr) and container classes (std::vector, std::string) that automatically manage resources. By using these, you can avoid writing custom destructors, copy/move constructors, and assignment operators, significantly reducing boilerplate code and potential bugs.

### Lambda function
```cpp
[capture list](params)->return_type{
    function stmts
}
int main() {
    auto sum = [](int a, int b) {
        return a + b; // Body of the lambda
    };

    std::cout << "Sum: " << sum(5, 10) << std::endl; // Output: Sum: 15
    return 0;
}
```

### auto
- const auto (for const)
    ```cpp
    void func(auto x) { } // Error: 'auto' is not allowed here
    ```
### constexpr
- to define expressions, variables, functions, and constructors that can be evaluated at compile-time.
### decltype
- to query the type of an expression at compile-time
    ```cpp
    const int x = 42;
    decltype(x) y = x; // y is deduced as const int
    ```
## Multithreading
```cpp
void printSum(int a, int b) {
    std::cout << "Sum: " << (a + b) << "\n";
}

int main() {
    std::thread t(printSum, 5, 10); // Pass arguments 5 and 10
    t.join(); // Wait for the thread to finish
    std::cout << "Hello from main thread!\n";
    return 0;
}
```
### Mutex
- is a synchronzation tool, to lock a resource, so only 1 thread can access it at a time
    - If a thread tries to lock a mutex that is already locked by another thread, it will block (wait) until the mutex becomes available.
    ```cpp
    std::mutex mtx;
    void incrementCounter() {
        for (int i = 0; i < 1000; ++i) {
            mtx.lock();    // Lock the mutex
            ++counter;     // Critical section
            mtx.unlock();  // Unlock the mutex
        }
    }
    ...
    // lock_guard - no need to handle unlock (lock will get unlocked at the end of scope)
    void incrementCounter() {
        for (int i = 0; i < 1000; ++i) {
            std::lock_guard<std::mutex> lock(mtx); // Automatically locks and unlocks the mutex
            ++counter;  // Critical section
        }
    }
    ```


### condition variable
- to wait for a specific condition before proceeding.

### atomic counter
- to ensure thread safe operations on shared data without using the mutex.
## What are the static members and static member functions?
When a variable in a class is declared static, space for it is allocated for the lifetime of the program. No matter how many objects of that class have been created, there is only one copy of the static member. So same static member can be accessed by all the objects of that class.

A static member function can be called even if no objects of the class exist and the static function are accessed using only the class name and the scope resolution operator ::.

## What is an abstract class and when do you use it?
A class is called an abstract class whose objects can never be created. Such a class exists as a parent for the derived classes. We can make a class abstract by placing a pure virtual function in the class.

- We can declare an array of pointers but an array of references is not possible.

### Dangling pointer
A dangling pointer in programming refers to a pointer that no longer points to a valid memory location. This typically occurs when the memory it refers to has been deallocated or freed, but the pointer itself has not been updated (e.g., set to NULL). 
```cpp
1. int* func() {
    int x = 10;
    return &x;  // Returning address of a local variable
}
2. Pointer outliving the scope: If a pointer is used after the object it points to has gone out of scope, it becomes dangling.
```
### More types of such pointers
1. Null pointer
    ```cpp
    int* ptr = NULL; // ptr points to nothing
    *ptr = 5;        // Dereferencing causes undefined behavior
    ```
2. Uninitialized pointer
    ```cpp
    int* ptr;    // Uninitialized pointer
    *ptr = 10;   // Undefined behavior
    ```
3. Void pointer
    ```cpp
    void* ptr;
    int x = 10;
    ptr = &x;         // Void pointer points to integer
    printf("%d", *(int*)ptr); // Proper type casting required
    ```
### Operator overloading
```cpp
#include <iostream>
using namespace std;
class Vector {
    ...
    // Overloading the '+' operator
    Vector operator+(const Vector& v) {
        return Vector(x + v.x, y + v.y);
    }
};

int main() {
    Vector v1(1, 2);
    Vector v2(3, 4);

    Vector result = v1 + v2; // Using the overloaded '+' operator
    result.display();        // Output: (4, 6)
    return 0;
}
```
### What is a subobject?
In C++, a subobject refers to the part of a derived class object that is contributed by its base class. This is different from a standalone object of the base class.
- When you create an instance of a derived class (e.g., SiClientAdasObjectDetectionUpdate), the memory layout of that object includes the base class (SiClientMmAcHmiBase) as its first part.
- This base class part is called a base class subobject.
- It is not a separate, independent object; it is a part of the derived object.

Example
```cpp
class Base { ... };
class Derived : public Base { ... };

Derived d;
```
- Here, d is a Derived object.
- The Base part of d is the base class subobject.
- You can access it via casting: Base* basePtr = &d;


