<table style="border-collapse: collapse; width: 100%;"><thead><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Aspect</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>Stack Allocation</strong></td><td style="border: 1px solid grey; padding: 8px;"><strong>Heap Allocation</strong></td></tr></thead><tbody><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Scope</strong></td><td style="border: 1px solid grey; padding: 8px;">Local variables</td><td style="border: 1px solid grey; padding: 8px;">Dynamically allocated variables</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Lifespan</strong></td><td style="border: 1px solid grey; padding: 8px;">Until the end of the function</td><td style="border: 1px solid grey; padding: 8px;">Until explicitly deallocated</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Size</strong></td><td style="border: 1px solid grey; padding: 8px;">Limited to stack size</td><td style="border: 1px solid grey; padding: 8px;">Limited by available memory</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Management</strong></td><td style="border: 1px solid grey; padding: 8px;">Automatic by compiler</td><td style="border: 1px solid grey; padding: 8px;">Manual by programmer</td></tr><tr><td style="border: 1px solid grey; padding: 8px;"><strong>Speed</strong></td><td style="border: 1px solid grey; padding: 8px;">Faster</td><td style="border: 1px solid grey; padding: 8px;">Slower due to potential fragmentation</td></tr></tbody></table>

### std::move
std::move is a utility that casts an object to an rvalue reference, allowing its resources to be "moved" rather than copied.

### Diff between Abstract class & Interface
Abstract Class:
- Contains a mix of pure virtual functions and other member functions/variables.
- Serves as a partial implementation of a concept.
- Derived classes must implement pure virtual functions.

Interface (as abstract class in C++):
- Contains only pure virtual functions.
- Serves purely as a contract with no implementation.
- Every derived class must implement all its functions.

### Why Constructor Can't be virtual
Constructor Can't be virtual because the object type must be known during the object creation time & with virtual it'll be determined at the runtime. Also, the base part of the object should be created first when creating the derived class object. Making the constructor will disrupt this behavior.

### Throw exception in constructor & destructor
- We can throw exception in constructor - as the object creation is not complete, so destructor will not be called. C++ runtime will do the required cleanup for whatever partially created objects till that time. 
- Throwing Exceptions in destructor can lead to UB + it will cause the program to terminate() without giving an opportunity to handle the exception.
```cpp
class MyObject {
public:
    ~MyObject() {
        std::cout << "Destructor" << std::endl;
        throw std::runtime_error("Error during destruction");
    }
};

int main() {
    try {
        MyObject obj;
    } catch (const std::runtime_error& e) {
        std::cout << "Caught exception: " << e.what() << std::endl;
    } // std::terminate will be called here if the destructor throws

    return 0;
}
```
Instead use try & catch inside the destructor itself.

### Copy Constructor
```cpp
MyClass(const MyClass& obj){ // const - so the obj value don't get changed unintentionally
    // passing obj as reference because
    // 1. To prevent infinite recursion - if passed as value, a copy of obj needs to be made -> to made that copy we need to call the copy constructor again, which will call the copy constructor again leading to infinite recursion.
    // 2. To prevent copy of large object 

    this->data = obj.data // here if data is pointer type, this line will do a shallow copy & both objects will share it
    // -------------------------------------------------------------------------------------------------------
    // Instead get memory from heap & assign the same value there
    // this way there will be deep copy & both object's data ptr will point to different memory locations
    data = new int;
    data = *(obj.data);
}
...
// 2 ways to call this copy constructor
MyClass bobj(aobj); // proper copy constructor calls
MyClass cobj = bobj; // knows as copy initialization (will not call the copy assignment operator)
```
If we don't provide self written copy constructor, the compiler will generate a default copy constructor.
So, If our class have any dynamically allocated memory, in copy constructor it will do the shallow copy only leading to `free(): double free detected in tcache 2` error (if doing `delete` in destructor).

### Copy assignment operator
We need to remember one thing regarding the operators that they will either be unary or binary. So during operator overloading, we can pass other operand in parameters for binary & leave it empty for unary.
```cpp
MyClass& operator=(const MyClass& obj){
    // standard approach
    if(this == &obj) 
        return *this;
    delete data;
    this->data = obj.data;
    // or
    data = new int;
    data = *(obj.data);
    
    return *this;
}
...
// there is one more way to do with void
void operator=(const MyClass& obj){
    // only issue is it can't do assignment chaining like cobj = bobj = aobj
    this->data = obj.data;
}
// We can do operator overloading here by including both implementations but they should differ in their params (like by removing const in one)
...
// We can call assignment operator as
bobj = aobj;
```

## lvalues & rvalues
- lvalue is something(an expression) that points to a specific memory location (locator value)
- rvalue is something that doesn't points anywhere (temporary) (read value)
- `int x (lvalue) = 5(rvalue);`
- `int *y = &x (rvalue produced by address-of (&) operator, x was lvalue);`
- ```cpp
    int global = 100;

    int& setGlobal() // returning the reference here, thus its an lvalue
    {
        return global;    
    }
    ...
    setGlobal() = 400; // OK
  ```
### lvalue reference (&)
- to create an alias for lvalue , (cannot bind to non-const lvalue ref to an rvalue)
- `const int& yref = 10;` is ok as the temp obj holding 10 's lifetime will get extended with yref but can't modify it.
- ```cpp
  A createA(){
    return A(); // temporary value -> rvalue
  }
  A& objref = createA(); // cannot bind non-const lvalue reference of type 'A&' to an rvalue of type 'A'
  ```
### rvalue reference (&&)
- designed to bind to rvalues (temporary objects)
- to enable "move semantics" for performance optimization.
- `int&& rref = 10;` is valid
- ```cpp
  std::string   s1     = "Hello ";
  std::string   s2     = "world";
  std::string&& s_rref = s1 + s2;    // the result of s1 + s2 is an rvalue
  s_rref += ", my friend";           // I can change the temporary string!
  std::cout << s_rref << '\n';       // prints "Hello world, my friend"
  ```
- ```cpp
  A createA(){
    return A(); // temporary value -> rvalue
  }
  A&& objref = createA(); // no error: can bind to rvalue of type A to rvalue reference of type A&&
  ```
```cpp
int& getb(){ // returning as lvalue ref
    return b; // local var (ravlue)
}
int getbV(){ // returning rvalue ref
    return b; // rvalue
}
...
...
bobj.getb() = 100; // okay bcoz bobj.getb() is lvalue
bobj.getbV() = 100; // error: lvalue required as left operand of assignment
```
## move semantics
```cpp
A createA(){
    return A(); // creating a temp object (as rvalue)
                // will call default constructor (dynamic memory allocation - 1st time)
}
...
A dobj = createA(); // will call copy constructor (dynamic memory allocation - 2nd time)
```
Too many expensive copies! We already have a fully-fledged object, the temporary and short-lived one returning from `createA()`, built for us by the compiler, it's an rvalue that will fade away with no use at the next instruction.

### move constructor
```cpp
MyClass(MyClass&& other){ // rvalue reference
    this->data = other.data;
    other.data = nullptr;
}
```

### move assignment operator
```cpp
MyClass& operator=(MyClass&& other){    // rvalue reference
    if(this != &other){
        if(data)
            delete data;        // release old resources
        data = other.data;      // assign temp obj data
        other.data = nullptr;   // nullify source
    }
    return *this;
}
```
- **Declaring move constructor / move assignment operator will implicitly delete the copy constructor / assignment operator.**
- move semantics is not only about classes. You can make use of it whenever you need to change the ownership of a resource across multiple areas of your application.

### std::move()
It is used to convert lavlue to rvalue. Using this will copy to target but empty the source.
```cpp
A dobj = std::move(bobj)/*(becomes rvalue)*/; // will call move constructor
// bobj will be empty -> bobj.data is null

bobj = std::move(dobj); // calls move assignment operator
// dobj will be empty
```
***If we haven't used std::move() here, it will call the copy constructor.***
- __Moving const objects:__ std::move will not work as expected with const objects. It will cast the object to a const rvalue reference (const T&&), which cannot bind to a move constructor or move assignment operator (as they require a non-const rvalue). The compiler will fall back to a copy operation, resulting in a performance hit.
- 
### Mark you move constructors and move assignment operators with noexcept
`noexcept` means "this function will never throw exceptions bcoz we should not call other code or allocate any memory here. We should only copy data & set the other ptrs to null (**only non-throwing ops**).

### Copy-and-swap
Intent is to create an exception-safe implementation of operator assignment operator & constructor too.
```cpp
class MyClass
{
    char * str;
public:
    MyClass & operator=(const MyClass & other){
        if (this != &other)
        {
            MyClass(other).swap(*this); //Copy-constructor and non-throwing swap
        }
        // Old resources are released with the destruction of the temporary above
        return *this;
    }

    MyClass & operator = (MyClass other) // the pass-by-value parameter serves as a temporary
    {
        // This swap operation implicitly handles resource release.
        // The temporary 'other' now holds the original resources of *this.
        // When 'other' is destroyed at the end of the function,
        // its destructor will be called, and 'delete a;' will be executed.
        other.swap (*this); // Non-throwing swap
        return *this;
    } // Old resources released when destructor of s is called.
    
    void swap(MyClass & other) noexcept // Also see non-throwing swap idiom
    {
        std::swap(this->str, other.str);
    }

    // -------------------------------------------------------------------
    // other way of writing the swap
    friend void swap(MyClass& first, MyClass& second) noexcept {
        using std::swap;
        swap(first.data, second.data);
    }

    // Copy-and-Swap Assignment Operator
    MyClass& operator=(MyClass other) noexcept { // Passed by value
        swap(*this, other); // just because its friend
        return *this;
    }
};
```

## Copy Elision
Copy elision is a compiler optimization that removes unnecessary copy and move operations when creating objects.
1. Return Value Optimization (RVO) - 
RVO applies to functions that return an object by value, where the return value is an unnamed, temporary object.
```cpp
MyClass createObject() {
    return MyClass(); // Returning an unnamed temporary
}
```
2. Named Return Value Optimization (NRVO)- 
NRVO is similar to RVO but applies when a function returns a named local variable. 
```cpp
MyClass createNamedObject() {
    MyClass temp;
    return temp; // Returning a named variable
}
```
## std::forward
It's a utility function used for perfect forwarding. It conditionally casts an argument to an rvalue iff that argument was originally an rvalue.
- primarily used in template functions that accepts a forwarding reference & need to pass that arg preserving its original category
### Issue
When we pass an argument to a template function by a forwarding reference (T&&), the parameter arg inside that function is always treated as an lvalue, even if it was originally an rvalue. This is because a named variable is always an lvalue. 
```cpp
template <typename T>
void wrapper(T&& arg) {
    // Inside wrapper, `arg` is always an lvalue,
    // so this will call the copy constructor, even if `arg` was an rvalue.
    AnotherClass obj = arg;
}
// AnotherClass has both a copy and a move constructor.
...
...
template <typename T>
void wrapper(T&& arg) { // `arg` is a forwarding reference
    // std::forward preserves the original value category
    AnotherClass obj = std::forward<T>(arg);
}
```
- static_cast<T&&>arg will also work same but lacks safety & intent.

There are four core rules that dictate how reference types collapse:
1. lvalue + lvalue -> lvalue
   1. Expression: T& & collapses to T&
   2. Meaning: If you combine an lvalue reference with another lvalue reference, the result is always an lvalue reference.
2. lvalue + rvalue -> lvalue
   1. Expression: T& && collapses to T&
   2. Meaning: An lvalue reference "wins" over an rvalue reference. If any lvalue reference is involved, the result is an lvalue reference.
3. rvalue + lvalue -> lvalue
   1. Expression: T&& & collapses to T&
   2. Meaning: This is the same rule as the previous one, demonstrating the dominance of the lvalue reference.
4. rvalue + rvalue -> rvalue
   1. Expression: T&& && collapses to T&&
   2. Meaning: An rvalue reference is only produced when both references are rvalue references. 

## Type casting
process of converting a variable from one data type to another.

### Implicit type conversion
it's automatically performed by the compiler
- also known as coercion
- safe when converting from a smaller data type to larger (int to double)
- data loss when larger type to smaller

### Explicit type conversion
performed manually
### c-style cast
it doesn't performs safety checks, can lead to UB. It can perform any type of cast (static to reinterpret_cast)
- `(target_type) expression`

### static_cast
- it is a compile time cast operator
- used for conversions which are well defined & checked at `compile time`.
- performs no runtime checks
- to do upcasting (derived to base (implicit))
- bw compatible numerical types (data loss with larger data type to smaller data type conversion)
- common use with searchability in code
- converting the void* 
    ```cpp
    void* ptr = &i;
    int* int_ptr = static_cast<int*>(ptr);
    ```
- `static_cast<target_type>(expression)`
- example
    ```cpp
    Derived d;
    Base &bref = static_cast<Base&>(d); // just a ref
    ...
    ...
    Derived* d = new Derived();
    Base *bref = static_cast<Base*>(d); // just a ptr ref
    // only diff between bref & d is with virtual functions
    ...
    ...
    Derived d;
    Base bref = static_cast<Base>(d);

    // downcasting also possible but risky
    Base b;
    Derived& d = static_cast<Derived&>(b);
    ```

### dynamic_cast
- c++ cast operator used for safe **runtime** checked conversions
- used for safe downcasting in polymorphic classes 
- uses RTTI (RunTime Type Information) to check target obj is of correct type (RTTI is enabled by vtable only)
- so dynamic cast can only be used with polymorphic classes
- performs a runtime check to verify if the source object is of target type
  - if same, returns valid pointer
  - if not same, returns nullptr or throws std::bad_cast
- In case of reference (&), it will throw bad_cast
- `dynamic_cast<target_type>(expression)`
- example 
    ```cpp
    // syntax
    dynamic_cast<Derived*>(base_ptr);
    dynamic_cast<Derived&>(base_ref);
    ...
    ...
    Base* b = new Derived();
    // Safe downcasting with dynamic_cast
    Derived* d = dynamic_cast<Derived*>(b);
    ...
    ...
    Base b;
    Derived& d = dynamic_cast<Derived&>(b); // terminate called after throwing an instance of 'std::bad_cast'
    ...
    ...
    Derived t;
    Base &b = t; // base itself is ref
    Derived& d = dynamic_cast<Derived&>(b); // base ref to derived ref

    ```

### const_cast
- used to add or remove `const` or `volatile` from a ptr or reference
- intent & common use cases 
  - when calling a function which was not written originally in a way or with the intent to have const correctness
  - interacting with legacy c apis
-  it is only safe to modify an object through a const_cast if the object was not originally declared as const else UB
    ```cpp
    // safe
    int regular_int = 5;
    const int* const_ptr = &regular_int;
    int* non_const_ptr = const_cast<int*>(const_ptr);
    *non_const_ptr = 10; // Safe: regular_int is not const
    ...
    // undefined Behavior
    const int const_int = 5;
    const int* const_ptr = &const_int;
    int* non_const_ptr = const_cast<int*>(const_ptr);
    *non_const_ptr = 10; // Undefined behavior: const_int is const

    ```
- `const_cast<target_type>(expression)`

### reinterpret_cast
- it's a low level cast, used for converting between unrelated pointer types or between pointer & integer types
- it reinterprets the bit pattern of the source as the target type without any safety checks.
- casting an int* to a float* doesn't change the underlying bits; it just tells the compiler to treat those bits as a float.
- have to use it with proper understanding of the memory layouts
- if you cast a pointer to a different type using reinterpret_cast and then cast it back to the original type, you will get the original value. One practical and safe use is within a hash function, where you re-interpret a pointer as an integral type to be used as a hash index.
- `reinterpret_cast<target_type>(expression)`

## const correctness
| Declaration           | Meaning                                                                 | Example                                  |
|----------------------|--------------------------------------------------------|------------------------------------------|
| `const int* p`       | Pointer-to-const: Data cannot be changed through this pointer, but the pointer can be reassigned. | `const int* p = &a;`<br>`*p = 10; // Error`<br>`p = &b; // OK` |
| `int* const p`       | Const pointer: The pointer's address cannot be changed, but the data it points to is mutable.     | `int* const p = &a;`<br>`*p = 10; // OK`<br>`p = &b; // Error` |
| `const int* const p` | Const pointer-to-const: Neither the pointer's address nor the data it points to can be changed.   | `const int* const p = &a;`<br>`*p = 10; // Error`<br>`p = &b; // Error` |

- `const int* p;` means p is a * pointer to an int that is const.
- `int* const p;` means p is a const pointer to an int. 

### const memeber functions
- They can be called on both const and non-const objects.
- They cannot call other non-const member functions of the same object.
- They cannot modify non-mutable data members. 
- this pointer, which becomes a const-qualified pointer (const MyClass* const this)
```cpp
private:
  std::string name;
  int id;
public:
  // Non-const member function (mutator)
  void set_name(const std::string& new_name) {
    name = new_name;
  }
  // Const member function (inspector)
  std::string get_name() const {
    return name;
  }
```
const User object, you can only call get_name(), not set_name().

> `mutable` keyword allows a data member to be changed even inside a const member function