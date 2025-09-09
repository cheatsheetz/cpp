# C++ Cheat Sheet

Comprehensive reference for C++ programming language covering syntax, STL, memory management, and modern C++ features from C++11 onwards.

---

## Table of Contents
- [Basic Syntax](#basic-syntax)
- [Data Types](#data-types)
- [Pointers and References](#pointers-and-references)
- [Memory Management](#memory-management)
- [Object-Oriented Programming](#object-oriented-programming)
- [STL Containers](#stl-containers)
- [STL Algorithms](#stl-algorithms)
- [Modern C++ Features](#modern-c-features)
- [Templates](#templates)
- [Exception Handling](#exception-handling)

---

## Basic Syntax

| Feature | Syntax | Example |
|---------|--------|---------|
| Include Header | `#include <header>` | `#include <iostream>` |
| Namespace | `using namespace name;` | `using namespace std;` |
| Function Declaration | `returnType function(parameters);` | `int add(int a, int b);` |
| Variable Declaration | `dataType variableName;` | `int count = 0;` |
| Constants | `const dataType NAME` | `const int MAX_SIZE = 100;` |
| Comments | `// single line` or `/* multi-line */` | `// This is a comment` |

### Basic Program Structure
```cpp
#include <iostream>
using namespace std;

int main() {
    cout << "Hello, World!" << endl;
    return 0;
}
```

## Data Types

### Fundamental Types
| Type | Size (typical) | Range | Example |
|------|----------------|-------|---------|
| `bool` | 1 byte | true/false | `bool flag = true;` |
| `char` | 1 byte | -128 to 127 | `char c = 'A';` |
| `int` | 4 bytes | -2^31 to 2^31-1 | `int num = 42;` |
| `long` | 8 bytes | -2^63 to 2^63-1 | `long big = 1000L;` |
| `float` | 4 bytes | IEEE 754 | `float f = 3.14f;` |
| `double` | 8 bytes | IEEE 754 | `double d = 3.14159;` |

### Type Modifiers
```cpp
unsigned int positive = 100u;
signed int negative = -100;
short int small = 32767;
long long huge = 9223372036854775807LL;

// Auto type deduction (C++11)
auto x = 10;        // int
auto y = 3.14;      // double
auto z = "hello";   // const char*
```

### String Handling
```cpp
#include <string>

string str = "Hello";
string str2("World");

// Common operations
str.length()                    // Length
str.size()                      // Same as length()
str[0]                          // Character at index
str.at(0)                       // Safe character access
str.substr(0, 3)                // Substring
str + " " + str2                // Concatenation
str.find("ell")                 // Find substring
str.replace(0, 5, "Hi")         // Replace substring
```

## Pointers and References

### Pointers
```cpp
int x = 10;
int* ptr = &x;          // Pointer to x
int** ptr2 = &ptr;      // Pointer to pointer

cout << x;              // 10
cout << &x;             // Address of x
cout << ptr;            // Address of x
cout << *ptr;           // 10 (dereferencing)

// Null pointers
int* nullPtr = nullptr; // C++11 way
int* oldNull = NULL;    // C-style (avoid)
```

### References
```cpp
int x = 10;
int& ref = x;           // Reference to x

ref = 20;               // Changes x to 20
cout << x;              // 20

// References must be initialized
// int& badRef;         // Error!

// Function parameters
void swap(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}
```

### Pointer Arithmetic
```cpp
int arr[] = {1, 2, 3, 4, 5};
int* p = arr;

cout << *p;             // 1
cout << *(p + 1);       // 2
cout << *(++p);         // 2 (p now points to arr[1])
cout << *(p++);         // 2 (post-increment)
cout << *p;             // 3
```

## Memory Management

### Stack vs Heap
```cpp
// Stack allocation (automatic)
int stackVar = 10;
int stackArray[100];

// Heap allocation (dynamic)
int* heapVar = new int(10);
int* heapArray = new int[100];

// Don't forget to delete!
delete heapVar;
delete[] heapArray;
```

### Smart Pointers (C++11)
```cpp
#include <memory>

// unique_ptr - exclusive ownership
unique_ptr<int> uptr = make_unique<int>(10);
unique_ptr<int> uptr2 = move(uptr); // Transfer ownership

// shared_ptr - shared ownership
shared_ptr<int> sptr = make_shared<int>(10);
shared_ptr<int> sptr2 = sptr;       // Share ownership

// weak_ptr - non-owning observer
weak_ptr<int> wptr = sptr;
if (auto locked = wptr.lock()) {
    cout << *locked << endl;
}
```

### RAII Pattern
```cpp
class Resource {
private:
    int* data;
public:
    Resource(int size) : data(new int[size]) {}
    ~Resource() { delete[] data; }
    
    // Rule of Five (C++11)
    Resource(const Resource& other) = delete;                    // Copy constructor
    Resource& operator=(const Resource& other) = delete;         // Copy assignment
    Resource(Resource&& other) noexcept : data(other.data) {     // Move constructor
        other.data = nullptr;
    }
    Resource& operator=(Resource&& other) noexcept {             // Move assignment
        if (this != &other) {
            delete[] data;
            data = other.data;
            other.data = nullptr;
        }
        return *this;
    }
};
```

## Object-Oriented Programming

### Classes and Objects
```cpp
class Car {
private:
    string brand;
    int year;
    
public:
    // Constructor
    Car(string b, int y) : brand(b), year(y) {}
    
    // Destructor
    ~Car() { cout << "Car destroyed" << endl; }
    
    // Getter
    string getBrand() const { return brand; }
    
    // Setter
    void setBrand(const string& b) { brand = b; }
    
    // Member function
    void start() const {
        cout << brand << " is starting..." << endl;
    }
};

// Usage
Car car("Toyota", 2023);
car.start();
```

### Inheritance
```cpp
class Animal {
protected:
    string name;
    
public:
    Animal(string n) : name(n) {}
    virtual ~Animal() = default;   // Virtual destructor
    
    virtual void makeSound() const {
        cout << name << " makes a sound" << endl;
    }
};

class Dog : public Animal {
public:
    Dog(string n) : Animal(n) {}
    
    void makeSound() const override {
        cout << name << " barks" << endl;
    }
    
    void wagTail() const {
        cout << name << " wags tail" << endl;
    }
};

// Polymorphism
Animal* animal = new Dog("Buddy");
animal->makeSound();        // "Buddy barks"
delete animal;
```

### Virtual Functions and Abstract Classes
```cpp
class Shape {
public:
    virtual double getArea() const = 0;     // Pure virtual function
    virtual void draw() const {             // Virtual function
        cout << "Drawing a shape" << endl;
    }
    virtual ~Shape() = default;
};

class Circle : public Shape {
private:
    double radius;
    
public:
    Circle(double r) : radius(r) {}
    
    double getArea() const override {
        return 3.14159 * radius * radius;
    }
    
    void draw() const override {
        cout << "Drawing a circle" << endl;
    }
};
```

## STL Containers

### Vector
```cpp
#include <vector>

vector<int> vec = {1, 2, 3};
vec.push_back(4);           // Add to end
vec.pop_back();             // Remove from end
vec.insert(vec.begin(), 0); // Insert at beginning
vec.erase(vec.begin());     // Remove first element

// Access
vec[0];                     // Direct access (no bounds check)
vec.at(0);                  // Safe access (bounds check)
vec.front();                // First element
vec.back();                 // Last element

// Properties
vec.size();                 // Number of elements
vec.capacity();             // Allocated capacity
vec.empty();                // Check if empty
vec.clear();                // Remove all elements
```

### List (Doubly Linked)
```cpp
#include <list>

list<int> lst = {1, 2, 3};
lst.push_front(0);          // Add to front
lst.push_back(4);           // Add to back
lst.pop_front();            // Remove from front
lst.pop_back();             // Remove from back

// Insertion and removal
auto it = lst.begin();
lst.insert(it, 10);         // Insert before iterator
lst.erase(it);              // Remove at iterator
```

### Map and Unordered Map
```cpp
#include <map>
#include <unordered_map>

// Map (ordered, typically red-black tree)
map<string, int> m;
m["key1"] = 10;
m["key2"] = 20;
m.insert({"key3", 30});

// Check if key exists
if (m.find("key1") != m.end()) {
    cout << "Found key1" << endl;
}

// Unordered Map (hash table)
unordered_map<string, int> um;
um["key1"] = 10;
um.emplace("key2", 20);     // Construct in place

// Iteration
for (const auto& pair : m) {
    cout << pair.first << ": " << pair.second << endl;
}
```

### Set and Unordered Set
```cpp
#include <set>
#include <unordered_set>

// Set (ordered)
set<int> s = {3, 1, 4, 1, 5}; // Duplicates removed, sorted
s.insert(2);
s.erase(1);
bool contains = s.count(3) > 0;

// Unordered Set (hash set)
unordered_set<string> us = {"apple", "banana", "cherry"};
us.insert("date");
us.erase("banana");
bool exists = us.find("apple") != us.end();
```

### Stack, Queue, Priority Queue
```cpp
#include <stack>
#include <queue>

// Stack (LIFO)
stack<int> stk;
stk.push(1);
stk.push(2);
int top = stk.top();        // 2
stk.pop();                  // Remove top element

// Queue (FIFO)
queue<int> q;
q.push(1);
q.push(2);
int front = q.front();      // 1
q.pop();                    // Remove front element

// Priority Queue (max heap by default)
priority_queue<int> pq;
pq.push(3);
pq.push(1);
pq.push(2);
int max = pq.top();         // 3
pq.pop();

// Min heap
priority_queue<int, vector<int>, greater<int>> minPq;
```

## STL Algorithms

### Common Algorithms
```cpp
#include <algorithm>
#include <numeric>

vector<int> vec = {3, 1, 4, 1, 5, 9, 2, 6};

// Sorting
sort(vec.begin(), vec.end());                    // Ascending
sort(vec.begin(), vec.end(), greater<int>());    // Descending

// Searching
auto it = find(vec.begin(), vec.end(), 4);
bool found = binary_search(vec.begin(), vec.end(), 4);

// Counting
int count = count(vec.begin(), vec.end(), 1);
int countIf = count_if(vec.begin(), vec.end(), [](int x) { return x > 5; });

// Transformation
transform(vec.begin(), vec.end(), vec.begin(), [](int x) { return x * 2; });

// Accumulation
int sum = accumulate(vec.begin(), vec.end(), 0);
int product = accumulate(vec.begin(), vec.end(), 1, multiplies<int>());

// Min/Max
auto minIt = min_element(vec.begin(), vec.end());
auto maxIt = max_element(vec.begin(), vec.end());
auto minMax = minmax_element(vec.begin(), vec.end());

// Copying and moving
vector<int> dest(vec.size());
copy(vec.begin(), vec.end(), dest.begin());
```

### Lambda Expressions with Algorithms
```cpp
vector<int> numbers = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};

// Filter even numbers
numbers.erase(
    remove_if(numbers.begin(), numbers.end(), [](int x) { return x % 2 != 0; }),
    numbers.end()
);

// Find first element greater than 5
auto it = find_if(numbers.begin(), numbers.end(), [](int x) { return x > 5; });

// Sort by custom criteria
vector<string> words = {"apple", "pie", "cherry", "a"};
sort(words.begin(), words.end(), [](const string& a, const string& b) {
    return a.length() < b.length();
});
```

## Modern C++ Features

### Auto and Decltype (C++11)
```cpp
auto x = 10;                    // int
auto y = 3.14;                  // double
auto z = "hello";               // const char*

vector<int> vec;
auto it = vec.begin();          // vector<int>::iterator

decltype(x) another_x = 20;     // same type as x (int)
```

### Range-Based For Loops (C++11)
```cpp
vector<int> vec = {1, 2, 3, 4, 5};

// By value
for (auto x : vec) {
    cout << x << " ";
}

// By reference (to modify)
for (auto& x : vec) {
    x *= 2;
}

// By const reference (efficient for large objects)
for (const auto& x : vec) {
    cout << x << " ";
}
```

### Lambda Expressions (C++11)
```cpp
// Basic lambda
auto add = [](int a, int b) { return a + b; };
int result = add(3, 4);

// Capture by value
int x = 10;
auto lambda1 = [x](int y) { return x + y; };

// Capture by reference
auto lambda2 = [&x](int y) { x = y; };

// Capture all by value/reference
auto lambda3 = [=](int y) { return x + y; };  // Capture all by value
auto lambda4 = [&](int y) { x = y; };         // Capture all by reference

// Mutable lambda
auto lambda5 = [x](int y) mutable { x++; return x + y; };
```

### Move Semantics (C++11)
```cpp
class MyClass {
private:
    int* data;
    size_t size;
    
public:
    // Move constructor
    MyClass(MyClass&& other) noexcept 
        : data(other.data), size(other.size) {
        other.data = nullptr;
        other.size = 0;
    }
    
    // Move assignment
    MyClass& operator=(MyClass&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            size = other.size;
            other.data = nullptr;
            other.size = 0;
        }
        return *this;
    }
};

// Using std::move
MyClass obj1;
MyClass obj2 = std::move(obj1);  // Move constructor called
```

### Nullptr (C++11)
```cpp
int* ptr = nullptr;             // C++11 way
int* old_ptr = NULL;            // C-style (avoid)

if (ptr == nullptr) {
    cout << "Pointer is null" << endl;
}
```

### Constexpr (C++11)
```cpp
constexpr int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}

constexpr int result = factorial(5);    // Computed at compile time
```

### Structured Bindings (C++17)
```cpp
#include <tuple>

pair<int, string> getPair() {
    return {42, "hello"};
}

auto [number, text] = getPair();
cout << number << ", " << text << endl;

// With maps
map<string, int> m = {{"a", 1}, {"b", 2}};
for (const auto& [key, value] : m) {
    cout << key << ": " << value << endl;
}
```

## Templates

### Function Templates
```cpp
template<typename T>
T maximum(T a, T b) {
    return (a > b) ? a : b;
}

// Usage
int maxInt = maximum(10, 20);
double maxDouble = maximum(3.14, 2.71);
string maxString = maximum(string("apple"), string("banana"));

// Template specialization
template<>
const char* maximum<const char*>(const char* a, const char* b) {
    return (strcmp(a, b) > 0) ? a : b;
}
```

### Class Templates
```cpp
template<typename T>
class Stack {
private:
    vector<T> elements;
    
public:
    void push(const T& item) {
        elements.push_back(item);
    }
    
    T pop() {
        if (elements.empty()) {
            throw runtime_error("Stack is empty");
        }
        T item = elements.back();
        elements.pop_back();
        return item;
    }
    
    bool empty() const {
        return elements.empty();
    }
};

// Usage
Stack<int> intStack;
Stack<string> stringStack;
```

### Variadic Templates (C++11)
```cpp
template<typename... Args>
void print(Args... args) {
    ((cout << args << " "), ...);  // C++17 fold expression
    cout << endl;
}

// C++11 way
template<typename T>
void print(T&& t) {
    cout << t << endl;
}

template<typename T, typename... Args>
void print(T&& t, Args&&... args) {
    cout << t << " ";
    print(args...);
}

// Usage
print(1, 2.5, "hello", 'c');
```

## Exception Handling

### Try-Catch-Throw
```cpp
#include <stdexcept>

try {
    int x = 10;
    int y = 0;
    if (y == 0) {
        throw runtime_error("Division by zero");
    }
    int result = x / y;
} catch (const runtime_error& e) {
    cout << "Runtime error: " << e.what() << endl;
} catch (const exception& e) {
    cout << "General exception: " << e.what() << endl;
} catch (...) {
    cout << "Unknown exception" << endl;
}
```

### Custom Exceptions
```cpp
class CustomException : public exception {
private:
    string message;
    
public:
    CustomException(const string& msg) : message(msg) {}
    
    const char* what() const noexcept override {
        return message.c_str();
    }
};

void validate(int age) {
    if (age < 0) {
        throw CustomException("Age cannot be negative");
    }
}
```

### RAII and Exception Safety
```cpp
class FileHandler {
private:
    FILE* file;
    
public:
    FileHandler(const string& filename) {
        file = fopen(filename.c_str(), "r");
        if (!file) {
            throw runtime_error("Cannot open file");
        }
    }
    
    ~FileHandler() {
        if (file) {
            fclose(file);
        }
    }
    
    // Prevent copying
    FileHandler(const FileHandler&) = delete;
    FileHandler& operator=(const FileHandler&) = delete;
};

// Usage - file automatically closed even if exception occurs
try {
    FileHandler fh("data.txt");
    // Use file...
    // File automatically closed when fh goes out of scope
} catch (const exception& e) {
    cout << e.what() << endl;
}
```

---

## Resources
- [C++ Reference](https://en.cppreference.com/)
- [ISO C++ Foundation](https://isocpp.org/)
- [C++ Core Guidelines](https://github.com/isocpp/CppCoreGuidelines)
- [cplusplus.com](https://cplusplus.com/)

---
*Originally compiled from various sources. Contributions welcome!*