## C vs C++
<table style="border-collapse: collapse; width: 100%;"><thead><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Aspect</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>C</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>C++</strong></td></tr></thead><tbody><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Paradigm</strong></td><td style="border: 1px solid grey; padding: 8px;">Procedural programming.</td><td style="border: 1px solid grey; padding: 8px;">Multi-paradigm: Procedural, Object-oriented, Generic.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Object-Oriented Features</strong></td><td style="border: 1px solid grey; padding: 8px;">Not supported.</td><td style="border: 1px solid grey; padding: 8px;">Supports classes, objects, inheritance, polymorphism, etc.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Memory Management</strong></td><td style="border: 1px solid grey; padding: 8px;">Manual using <code>malloc()</code> and <code>free()</code>.</td><td style="border: 1px solid grey; padding: 8px;">Manual + automated with smart pointers (<code>std::unique_ptr</code>, <code>std::shared_ptr</code>).</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Standard Library</strong></td><td style="border: 1px solid grey; padding: 8px;">Basic functionalities (<code>stdio.h</code>, <code>stdlib.h</code>).</td><td style="border: 1px solid grey; padding: 8px;">Rich Standard Template Library (STL) with data structures and algorithms.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Compilation</strong></td><td style="border: 1px solid grey; padding: 8px;">Directly compiles to machine code.</td><td style="border: 1px solid grey; padding: 8px;">Compiles to machine code with additional C++-specific features.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Function Overloading</strong></td><td style="border: 1px solid grey; padding: 8px;">Not supported.</td><td style="border: 1px solid grey; padding: 8px;">Supported; allows multiple functions with the same name but different parameters.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Namespaces</strong></td><td style="border: 1px solid grey; padding: 8px;">Not available.</td><td style="border: 1px solid grey; padding: 8px;">Supports namespaces to avoid name conflicts.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Exception Handling</strong></td><td style="border: 1px solid grey; padding: 8px;">Managed using return codes and <code>errno</code>.</td><td style="border: 1px solid grey; padding: 8px;">Built-in exception handling (<code>try</code>, <code>catch</code>, <code>throw</code>).</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Inline Functions</strong></td><td style="border: 1px solid grey; padding: 8px;">Not natively supported (compiler-dependent).</td><td style="border: 1px solid grey; padding: 8px;">Supported to reduce function call overhead.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Type Safety</strong></td><td style="border: 1px solid grey; padding: 8px;">Less type-safe, allows implicit type conversions.</td><td style="border: 1px solid grey; padding: 8px;">More type-safe; stricter type checking (<code>const</code>, references, etc.).</td></tr></tbody></table>

## access specifiers
<table style="border-collapse: collapse; width: 100%;"><thead><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Access Specifier</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>Description</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>Accessibility</strong></td></tr></thead><tbody><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Public</strong></td><td style="border: 1px solid grey; padding: 8px;">Members are <strong>accessible everywhere</strong> in the program.</td><td style="border: 1px solid grey; padding: 8px;">Accessible within the class, derived classes, and outside the class.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Private</strong></td><td style="border: 1px solid grey; padding: 8px;">Members are <strong>only accessible within the class</strong> itself.</td><td style="border: 1px solid grey; padding: 8px;">Not accessible outside the class or in derived classes (unless explicitly granted).</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Protected</strong></td><td style="border: 1px solid grey; padding: 8px;">Members are <strong>accessible within the class and its derived classes</strong> but <strong>not outside</strong> the class.</td><td style="border: 1px solid grey; padding: 8px;">Accessible within the class and derived classes but not directly outside the class.</td></tr></tbody></table>

## Static variables
Static variables are the variables whose life spans over the entire duration of program.
- If declared inside a function, their scope is limited to that function, but their value persists across function calls.
- If declared in a class, their scope is shared across all instances (objects) of the class.
    ```cpp
    class MyClass {
    public:
        static int staticVar; // Declaration of static variable inside the class
    };

    // Definition of static variable outside the class
    // Linker Requirement: When the class is compiled, the compiler only knows about the declaration of the static variable inside the class. 
    //                     To allocate memory and initialize the variable, the linker needs its definition outside the class.
    // Storage Allocation: A static variable is shared among all instances of the class. It does not belong to any specific object 
    //                     but exists independently of them. Defining it outside the class ensures that exactly one copy of the static variable 
    //                     is allocated in the program's memory.
    int MyClass::staticVar = 0; // Memory is allocated and initialized here
    ```
- Static variables are stored in the data segment of memory, not in the stack.
- Static member functions can access only static variables and other static member functions of the class because they do not operate on an instance of the class.
    - Uses:
    - Static member functions can be used as helper functions that are logically associated with the class but do not need instance-specific data.
        ```cpp
        #include <iostream>
        class MathUtils {
        public:
            static int add(int a, int b) {
                return a + b;
            }

            static int multiply(int a, int b) {
                return a * b;
            }
        };

        int main() {
            std::cout << "Addition: " << MathUtils::add(5, 3) << std::endl;       // Output: 8
            std::cout << "Multiplication: " << MathUtils::multiply(5, 3) << std::endl; // Output: 15
            return 0;
        }
        ```
    - Static member functions are ideal for managing global counters, flags, or state variables that are shared across all instances of a class.
- A static **global** variable has a file scope, meaning it is only accessible within the file where it is declared. It cannot be accessed from other files, even if they include the file where the static variable is defined.

## Inline functions vs macros
<table style="border-collapse: collapse; width: 100%;"><thead><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Aspect</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>Inline Functions</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>Macros</strong></td></tr></thead><tbody><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Definition</strong></td><td style="border: 1px solid grey; padding: 8px;">Defined using the <code>inline</code> keyword in C++.</td><td style="border: 1px solid grey; padding: 8px;">Defined using the <code>#define</code> preprocessor directive.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Type Safety</strong></td><td style="border: 1px solid grey; padding: 8px;">Type-safe; checks types at compile time.</td><td style="border: 1px solid grey; padding: 8px;">Not type-safe; no type checking (text substitution only).</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Syntax</strong></td><td style="border: 1px solid grey; padding: 8px;">Follows proper C++ syntax with arguments and return types.</td><td style="border: 1px solid grey; padding: 8px;">No strict syntax rules; uses textual substitution.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Compilation</strong></td><td style="border: 1px solid grey; padding: 8px;">Handled by the compiler; replaces function calls with inline code.</td><td style="border: 1px solid grey; padding: 8px;">Handled by the preprocessor; substitutes code before compilation.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Debugging</strong></td><td style="border: 1px solid grey; padding: 8px;">Easier to debug; treated like regular functions.</td><td style="border: 1px solid grey; padding: 8px;">Harder to debug; errors occur in substituted text.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Scope</strong></td><td style="border: 1px solid grey; padding: 8px;">Has a proper scope (local to classes or namespaces).</td><td style="border: 1px solid grey; padding: 8px;">Global scope; substitution is applied throughout the code.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Complexity</strong></td><td style="border: 1px solid grey; padding: 8px;">Can include complex logic, loops, and type-checking.</td><td style="border: 1px solid grey; padding: 8px;">Limited to simple text substitution; cannot handle complex logic.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Usage</strong></td><td style="border: 1px solid grey; padding: 8px;">Suitable for small, frequently called functions.</td><td style="border: 1px solid grey; padding: 8px;">Often used for constants or very simple operations.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Examples</strong>:</td><td style="border: 1px solid grey; padding: 8px;">inline int square(int x) {return x * x;}
int main() {std::cout << square(5); // Output: 25}
</td><td style="border: 1px solid grey; padding: 8px;">#define SQUARE(x) ((x) * (x)) int main() {std::cout << SQUARE(5); // Output: 25}
</td></tr><tr><td style="border: 1px solid grey; padding: 8px;">Inline Function:</td><td style="border: 1px solid grey; padding: 8px;"></td><td style="border: 1px solid grey; padding: 8px;"></td></tr></tbody></table>

## Namespaces
Namespaces in C++ are used to group variables, functions, and classes under a unique name to avoid name conflicts in large projects.
- can access the members of a namespace using the scope resolution operator (::).
- can use the using keyword to bring the namespace into scope.
- An anonymous namespace is a namespace without a name. Its members are only accessible within the file where it is defined, making them similar to static global variables.
- ```cpp
    namespace OuterNamespace {
        namespace InnerNamespace {
            void myFunction() {
                std::cout << "Hello from InnerNamespace!" << std::endl;
            }
        }
    }

    namespace Alias = OuterNamespace::InnerNamespace; // Alias for long or nested namespaces

    int main() {
        Alias::myFunction(); // Access using alias
        return 0;
    }
    ```

## new vs malloc()
<table style="border-collapse: collapse; width: 100%;"><thead><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Aspect</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong><code>new</code> Operator</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong><code>malloc()</code> Function</strong></td></tr></thead><tbody><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Language</strong></td><td style="border: 1px solid grey; padding: 8px;">C++ only.</td><td style="border: 1px solid grey; padding: 8px;">C and C++ (from the <code>&lt;cstdlib&gt;</code> header).</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Syntax</strong></td><td style="border: 1px solid grey; padding: 8px;"><code>pointer = new Type;</code></td><td style="border: 1px solid grey; padding: 8px;"><code>pointer = (Type*) malloc(size);</code></td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Initialization</strong></td><td style="border: 1px solid grey; padding: 8px;">Automatically calls the constructor for objects.</td><td style="border: 1px solid grey; padding: 8px;">Does not initialize memory; returns uninitialized memory.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Type Safety</strong></td><td style="border: 1px solid grey; padding: 8px;">Type-safe; no need for explicit type casting.</td><td style="border: 1px solid grey; padding: 8px;">Requires explicit type casting.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Memory Deallocation</strong></td><td style="border: 1px solid grey; padding: 8px;">Memory is freed using <code>delete</code> or <code>delete[]</code>.</td><td style="border: 1px solid grey; padding: 8px;">Memory is freed using <code>free()</code>.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Behavior with Objects</strong></td><td style="border: 1px solid grey; padding: 8px;">Calls the constructor and allocates memory.</td><td style="border: 1px solid grey; padding: 8px;">Allocates memory but does not call the constructor.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Overloading</strong></td><td style="border: 1px solid grey; padding: 8px;">Can be overloaded in classes.</td><td style="border: 1px solid grey; padding: 8px;">Cannot be overloaded; part of <code>&lt;cstdlib&gt;</code>.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Exception Handling</strong></td><td style="border: 1px solid grey; padding: 8px;">Throws <code>std::bad_alloc</code> exception on failure.</td><td style="border: 1px solid grey; padding: 8px;">Returns <code>nullptr</code> on failure.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Usage for Arrays</strong></td><td style="border: 1px solid grey; padding: 8px;"><code>new[]</code> is used to allocate arrays.</td><td style="border: 1px solid grey; padding: 8px;">Requires manual size calculation and pointer arithmetic.</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Performance</strong></td><td style="border: 1px solid grey; padding: 8px;">Slightly slower due to additional features (e.g., constructor calls).</td><td style="border: 1px solid grey; padding: 8px;">Faster as it only allocates raw memory.</td></tr></tbody></table>

### What Happens in the Original `new` Implementation?
1. Memory Allocation: 
The new operator internally calls malloc() (or an equivalent allocator) to allocate raw memory of the required size for the object.

2. Object Construction:
After memory is allocated, the constructor of the class is automatically called to initialize the object.

3. Type Casting:
The raw memory pointer returned by malloc() (which is of type void*) is automatically type-casted to the appropriate object type (MyClass* in this case) by the compiler.

```cpp
MyClass* obj = new Myclass();
// can be extended as
void* rawMemory = operator new(sizeof(MyClass));  // Allocate memory (it will call malloc() & return void*)
MyClass* typedPointer = static_cast<MyClass*>(rawMemory); // Cast to MyClass*
typedPointer->MyClass(); // Call constructor
return typedPointer;
```
### `new` operator overloading
```cpp
#include <cstdlib> // Required for malloc()

class MyClass {
public:
    int data;
    double a;
    MyClass(){
        std::cout<<"Constructor\n";
    }
    // Overloading the new operator
    // Memory allocation happens before an object instance is created. At this point, there is no this pointer available, 
    // so it must be a static function.
    static void* operator new(size_t size) {
        std::cout << "Custom new operator called for size: " << size << std::endl;
        void* ptr = malloc(size); // Allocate memory using malloc()
        if (!ptr) {
            throw std::bad_alloc();
        }
        return ptr;
    }

    // Overloading the delete operator (optional)
    static void operator delete(void* ptr) {
        std::cout << "Custom delete operator called" << std::endl;
        free(ptr); // Free memory using free()
    }
};

int main() {
    MyClass* obj = new MyClass(); // Calls the overloaded new operator
    obj->a=10;
    delete obj;                  // Calls the overloaded delete operator
    return 0;
}

// output
// Custom new operator called for size: 16
// Constructor
// Custom delete operator called
```
* `new` returns a pointer of the type of the object being created but `operator new` will return a `void*`.

## placement new
Placement new is a special form of the new operator that allows us to construct an object at a specific memory location.
```cpp
char buffer[sizeof(MyClass)];

// Use placement new to construct the object in the buffer
MyClass* obj = new (buffer) MyClass();
```
### Use Cases
1. Custom Memory Management:
    - Useful for constructing objects in pre-allocated memory (e.g., memory pools or arenas).
2. Performance Optimization:
    - Avoids dynamic memory allocation overhead by reusing memory.
3. Embedded Systems:
    * Constructing objects in specific memory locations (e.g., hardware buffers).
4. Constructing Objects in Shared Memory:
    - Allows objects to be created in memory regions shared between processes.

### Overloading placement new
```cpp
class MyClass {
public:
    int data;

    // Overloading placement new
    static void* operator new(size_t size, void* location) {
        std::cout << "Placement new called" << std::endl;
        return location; // Return the memory location provided
    }
};

int main() {
    char buffer[sizeof(MyClass)]; // Allocate buffer
    MyClass* obj = new (buffer) MyClass(); // Calls placement new
    std::cout << "Object created at address: " << obj << std::endl;
    return 0;
}
```

## Dangling Pointers
A dangling pointer refers to a pointer that points to a memory location which has been freed or deallocated.
- ```cpp
    int* createDanglingPointer() {
        int localVar = 42; // Local variable
        return &localVar;  // Returning address of local variable
    }
    int main() {
        int* ptr = createDanglingPointer(); // ptr points to invalid memory
        std::cout << *ptr;                 // Dereferencing a dangling pointer (undefined behavior)
        return 0;
    }
    ```
- ```cpp
    int main() {
        int* ptr = new int(42);  // Dynamically allocate memory
        delete ptr;              // Memory deallocated
        // ptr now points to freed memory (dangling pointer)

        std::cout << *ptr;       // Dereferencing a dangling pointer (undefined behavior)
        return 0;
    }
    ```

## Wild Pointers
a pointer that has not been initialized and points to a random or unpredictable memory location.
```cpp
int main() {
    int* ptr;  // Pointer declared but not initialized
    *ptr = 42; // Dereferencing a wild pointer (undefined behavior)

    int* ptr = new int(42); // Dynamically allocate memory
    delete ptr;             // Memory deallocated
    *ptr = 52;              // Writing to a wild pointer (undefined behavior)
    
    return 0;
}
```

## Memory Leak
A memory leak occurs when a program allocates memory (usually on the heap) but fails to release it after it is no longer needed, resulting in an accumulation of unused memory. Over time, this can cause the program to consume excessive memory, degrade performance, and eventually crash due to running out of available memory.

## Smart Pointers
Smart pointers are objects in c++ that manage the lifetime of dynamically allocated memory automatically.

Internally, they contain a raw pointer, but we interact with them as objects that provide pointer-like behavior (using operator*, operator->, etc.).
### unique_ptr
- std::unique_ptr uniquely owns the object it manages. No other smart pointer can share ownership of the object.
- Memory is automatically freed when the std::unique_ptr goes out of scope.
```cpp
std::unique_ptr<MyClass> ptr = std::make_unique<MyClass>(42); // Create a unique_ptr
ptr->print(); // Access the object through the unique_ptr

std::unique_ptr<int[]> arr = std::make_unique<int[]>(5);
```
### shared_ptr
A std::shared_ptr allows multiple smart pointers to share ownership of the object. The object is destroyed only when the last std::shared_ptr to it is destroyed.
```cpp
std::shared_ptr<MyClass> ptr1 = std::make_shared<MyClass>(42); // Create a shared_ptr
std::shared_ptr<MyClass> ptr2 = ptr1; // Share ownership

ptr1->print(); // Access the object through ptr1
ptr2->print(); // Access the object through ptr2
```
- Multiple threads can read or create copies of a std::shared_ptr without interfering with one another.
- The reference count is managed using an atomic operation, which ensures that incrementing (shared_ptr creation) or decrementing (shared_ptr destruction) the count does not result in a data race.

### weak_ptr
- weak_ptr does not own the object but observes it
- When you need to access an object managed by std::shared_ptr without influencing its lifetime (no effect on reference_count)
```cpp
int main() {
    std::shared_ptr<MyClass> sharedPtr = std::make_shared<MyClass>(42); // Create a shared_ptr
    std::weak_ptr<MyClass> weakPtr = sharedPtr; // Create a weak_ptr observing sharedPtr

    if (auto lockedPtr = weakPtr.lock()) { // Lock weak_ptr to access the shared_ptr
        lockedPtr->print();
    } else {
        std::cout << "Resource is no longer available!" << std::endl;
    }

    sharedPtr.reset(); // Release ownership of the resource

    if (auto lockedPtr = weakPtr.lock()) {
        lockedPtr->print();
    } else {
        std::cout << "Resource is no longer available!" << std::endl;
    }

    return 0;
}
// Output:
// Value: 42
// Resource is no longer available!
```
- Reason for Locking
    1. A std::weak_ptr does not affect the reference count of the underlying object.
    2. If the last std::shared_ptr managing the object is destroyed, the object is deallocated, and the std::weak_ptr becomes expired.
    3. To safely access the object, you must lock the std::weak_ptr using weak_ptr.lock(), which returns a std::shared_ptr if the object is still alive. If the object has been destroyed, lock() returns nullptr.

### Cyclic dependency with shared_ptr
```cpp
#include <iostream>
#include <memory>

class B; // Forward declaration

class A {
public:
    std::shared_ptr<B> bPtr; // Shared ownership of B
    // std::weak_ptr<B> bPtr; // Weak ownership of B
    ~A() { std::cout << "A destroyed" << std::endl; }
};

class B {
public:
    std::shared_ptr<A> aPtr; // Shared ownership of A
    ~B() { std::cout << "B destroyed" << std::endl; }
};

int main() {
    std::shared_ptr<A> a = std::make_shared<A>();
    std::shared_ptr<B> b = std::make_shared<B>();

    a->bPtr = b; // A owns B | A observes B
    b->aPtr = a; // B owns A

    // Memory leak: Neither A nor B is destroyed due to cyclic reference (No destructor call)
    // |
    // No memory leak: A and B are properly destroyed (destructor call)
    return 0;
}
```
## OOPS
A programming paradigm based on the concept of "objects", which are instances of classes. These objects encapsulate data (attributes) and behavior (methods or functions) into a single unit. It allows us to model real-world entities as objects & classes.
### Encapsulation
- Encapsulation involves bundling data (variables) and functions (methods) into a single unit (class).
- It also involves restricting the direct access to some of an object's components, which is achieved using access specifiers (public, private, protected).
### Inheritance
- Inheritance allows a class (child/derived class) to acquire properties and behavior from another class (parent/base class)
```cpp
class Base {
public:
    int publicMember = 1;
protected:
    int protectedMember = 2;
private:
    int privateMember = 3; // Not accessible in the derived class
};

class Derived : public Base {
public:
    void show() {
        std::cout << "Public: " << publicMember << std::endl;       // Accessible
        std::cout << "Protected: " << protectedMember << std::endl; // Accessible
    }
};

class Derived : protected Base {
    void show() {
        std::cout << "Public (now protected): " << publicMember << std::endl;       // Accessible
        std::cout << "Protected: " << protectedMember << std::endl;                // Accessible
    }
};

class Derived : private Base {
    void show() {
        std::cout << "Public (now private): " << publicMember << std::endl;       // Accessible
        std::cout << "Protected (now private): " << protectedMember << std::endl; // Accessible
    }
};
```
### Polymorphism
- ability to represent same functions in different forms.
- allows objects of different classes to be treated as objects of a common base class, primarily through function overloading and function overriding.

Operator overloading allows us to redefine the behavior of operators (e.g., +, -, *) for user-defined types.
```cpp
Complex operator+(const Complex& other) {
    return Complex(real + other.real, imag + other.imag);
}
Complex c3 = c1 + c2;
```
### Abstraction
Abstraction refers to the process of hiding implementation details and exposing only the functionality to the user.
- focus on what an object does rather than how it does it.
- Abstraction separates the interface (abstract class) from the implementation, making the code modular and easier to understand.
- Abstract classes are often used to design interfaces that define a common Interface (set of behaviors) for different derived classes.
```cpp
// Interface (abstract class with only pure virtual functions)
class IShape {
public:
    virtual void draw() = 0; // Pure virtual function
    virtual ~IShape() {}     // Virtual destructor for proper cleanup
};
```
### Virtual functions
Virtual Functions in C++ enable runtime polymorphism by allowing the derived classes to override methods of a base class. When a virtual function is called on a base class pointer or reference, the function of the actual object (derived class implementation) is invoked, rather than the base class implementation.
```cpp
class Base {
public:
    virtual void display() { // Virtual function
        std::cout << "Base class display" << std::endl;
    }
};

class Derived : public Base {
public:
    void display() override { // Overriding base class method
        std::cout << "Derived class display" << std::endl;
    }
};

int main() {
    Base* basePtr;
    Derived derivedObj;

    basePtr = &derivedObj; // Base class pointer pointing to Derived object
    basePtr->display(); // Calls Derived's display() due to runtime polymorphism

    // Base *b = new Derived();

    return 0;
}
```

### VTable Mechanism
Virtual Table (VTable) is a table created per class (if they have virtual functions or overriding functions) and this table contains pointers to the virtual functions of that class.

VPointer (VPtr) is a hidden pointer in each object that points to the VTable of the object's class.

Steps in VTable Mechanism
1. VTable Creation:
    - The compiler creates a VTable for each class that has virtual functions.
    - The VTable contains function pointers to the virtual functions of the class.
2. VPtr Initialization:
    - When an object of a class is created, the compiler inserts a hidden pointer (vptr) into the object.
    - The vptr points to the VTable of the class that the object belongs to.
3. Function Call Resolution:
    - When a virtual function is called on a base class pointer or reference, the vptr of the actual object is used to look up the correct function in the VTable.
    - This ensures that the overridden function in the derived class is called.
```cpp
int main() {
    Base baseObj;
    Derived derivedObj;

    Base* basePtr;

    // Base class object
    basePtr = &baseObj;
    basePtr->display(); // Calls Base's display()

    // Derived class object
    basePtr = &derivedObj;
    basePtr->display(); // Calls Derived's display()

    return 0;
}
```
Step-by-Step Explanation:
1. VTable Construction:
    - For the Base class, the VTable contains a pointer to Base::display().
    - For the Derived class, the VTable contains a pointer to Derived::display().
2. VPtr Initialization:
    - When Base baseObj is created, its vptr points to the VTable of Base.
    - When Derived derivedObj is created, its vptr points to the VTable of Derived.
3. Function Call Resolution:
    - When basePtr->display() is called:
        - The vptr of the object (baseObj or derivedObj) is used to look up the correct function in the VTable.
        - For baseObj, the VTable points to Base::display().
        - For derivedObj, the VTable points to Derived::display()