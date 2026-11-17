# Memory Management in C and C++

## 1. Introduction to Memory Management
***Memory Management ? (HCL, )***

**Memory Management** is the process of allocating, using, tracking, and deallocating memory during program execution.

It helps:
- Utilize memory efficiently.
- Prevent memory leaks.
- Avoid dangling pointers.
- Prevent memory corruption.
- Improve application performance.
- Manage object and variable lifetimes.

Memory management in C and C++ can be performed through automatic, static, and dynamic storage mechanisms.

---

## 2. Memory Layout of a C/C++ Program

When a program executes, its memory is commonly organized into the following sections:

1. Code Segment (Text Segment)
2. Data Segment
3. BSS Segment
4. Heap
5. Stack

### Memory Layout

```text
        High Memory Address
        +-------------------+
        |       Stack       |
        |                   |
        |        ↓          |
        +-------------------+
        |                   |
        |   Free Memory     |
        |                   |
        |        ↑          |
        +-------------------+
        |       Heap        |
        +-------------------+
        |    BSS Segment    |
        +-------------------+
        |   Data Segment    |
        +-------------------+
        |   Code Segment    |
        +-------------------+
        Low Memory Address
```

Note: This is a simplified, common memory layout. Actual layouts depend on the operating system, compiler, and architecture.

### 2.1 Code Segment (Text Segment)

- Stores executable instructions of the program.
- Generally read-only.
- Contains compiled machine code.

Example:
```cpp
int add(int a, int b)
{
    return a + b;
}
```

### 2.2 Data Segment

- Stores initialized global variables.
- Stores initialized static variables.
- Memory exists throughout the program execution.

Example:
```cpp
int globalVar = 100;

int main()
{
    static int count = 10;
    return 0;
}
```

### 2.3 BSS Segment

- Stores uninitialized global variables.
- Stores zero-initialized global and static variables.
- Typically initialized to zero before program execution.

Example:
```cpp
int globalVar;

int main()
{
    static int count;
    return 0;
}
```

### 2.4 Stack Memory

- Stores function call information and typically local automatic variables.
- Memory is automatically managed.
- Allocation and deallocation happen as function scopes begin and end.
- Has limited capacity compared to available virtual memory.

Example:
```cpp
void display()
{
    int a = 10;
    int b = 20;
}

int main()
{
    display();
    return 0;
}
```

When `display()` finishes, its local variables' lifetimes end.

### 2.5 Heap Memory

- Used for dynamic memory allocation.
- Memory is allocated at runtime.
- Its lifetime can extend beyond the scope where it was allocated.
- In C, use `malloc()`, `calloc()`, `realloc()` and `free()`.
- In C++, use `new`, `delete`, and preferably smart pointers.

Example:
```cpp
int main()
{
    int* ptr = new int(100);

    delete ptr;
    ptr = nullptr;

    return 0;
}
```

---

## 3. Types of Memory Allocation

Memory allocation is commonly explained through three categories.

### 3.1 Static Memory Allocation

- Memory allocation is determined before program execution.
- Global and static variables have static storage duration.
- Memory remains available throughout the program's execution.

Example:
```cpp
int globalVar = 100;

int main()
{
    static int count = 10;
    return 0;
}
```

### 3.2 Automatic Memory Allocation

- Memory is managed automatically.
- Local variables normally have automatic storage duration.
- Their lifetime generally ends when their scope exits.

Example:
```cpp
int main()
{
    int a = 10;
    int b = 20;

    return 0;
}
```

### 3.3 Dynamic Memory Allocation

- Memory is allocated during runtime.
- Used when memory requirements are not known in advance.
- Memory can remain allocated after the allocating function returns.
- The allocated memory must be released or managed appropriately.

Example:
```cpp
int* ptr = new int(50);

delete ptr;
ptr = nullptr;
```

---

# PART A: MEMORY MANAGEMENT IN C

## 4. Dynamic Memory Allocation in C
***Explain Dynamic Memory Allocation with Example in c and cpp (XYZ, Larsen & Toubro)***

C provides four important functions for dynamic memory allocation.

1. malloc()
2. calloc()
3. realloc()
4. free()

Header file:
```c
#include <stdlib.h>
```

### 4.1 malloc()

`malloc()` stands for Memory Allocation.

- Allocates a specified number of bytes.
- Returns a pointer to the allocated memory.
- Memory is uninitialized.
- Returns NULL if allocation fails.

Syntax:
```c
ptr = (int*)malloc(n * sizeof(int));
```

Example:
```c
#include <stdio.h>
#include <stdlib.h>

int main()
{
    int* ptr = (int*)malloc(5 * sizeof(int));

    if (ptr == NULL)
    {
        printf("Memory allocation failed");
        return 1;
    }

    for (int i = 0; i < 5; i++)
    {
        ptr[i] = (i + 1) * 10;
    }

    for (int i = 0; i < 5; i++)
    {
        printf("%d ", ptr[i]);
    }

    free(ptr);
    ptr = NULL;

    return 0;
}
```

Output:
```text
10 20 30 40 50
```

### 4.2 calloc()

`calloc()` stands for Contiguous Allocation.

- Allocates memory for multiple elements.
- Takes two arguments: number of elements and size of each element.
- Initializes all allocated bytes to zero.
- Returns NULL if allocation fails.

Syntax:
```c
ptr = (int*)calloc(n, sizeof(int));
```

Example:
```c
#include <stdio.h>
#include <stdlib.h>

int main()
{
    int* ptr = (int*)calloc(5, sizeof(int));

    if (ptr == NULL)
        return 1;

    for (int i = 0; i < 5; i++)
    {
        printf("%d ", ptr[i]);
    }

    free(ptr);
    ptr = NULL;

    return 0;
}
```

Output:
```text
0 0 0 0 0
```

Note: calloc initializes all bytes to zero. This does not universally guarantee the same representation as a language-level zero value for every possible C type.

### 4.3 realloc()

`realloc()` stands for Reallocation.

- Changes the size of previously allocated memory.
- Can increase or decrease allocated memory.
- Existing data is preserved up to the smaller of the old and new sizes.
- The memory block may move to a new location.
- Returns NULL if resizing fails while the original allocation remains valid.

Syntax:
```c
ptr = (int*)realloc(ptr, newSize);
```

Example:
```c
#include <stdio.h>
#include <stdlib.h>

int main()
{
    int* ptr = (int*)malloc(3 * sizeof(int));

    if (ptr == NULL)
        return 1;

    ptr[0] = 10;
    ptr[1] = 20;
    ptr[2] = 30;

    int* temp = (int*)realloc(ptr, 5 * sizeof(int));

    if (temp == NULL)
    {
        free(ptr);
        return 1;
    }

    ptr = temp;

    ptr[3] = 40;
    ptr[4] = 50;

    for (int i = 0; i < 5; i++)
    {
        printf("%d ", ptr[i]);
    }

    free(ptr);
    ptr = NULL;

    return 0;
}
```

Output:
```text
10 20 30 40 50
```

Important: Never directly overwrite the original pointer with realloc's result without checking for failure.

### 4.4 free()

`free()` releases dynamically allocated memory.

- Releases memory allocated using malloc(), calloc(), or realloc().
- Prevents memory leaks when used correctly.
- Does not automatically set the pointer to NULL.

Syntax:
```c
free(ptr);
ptr = NULL;
```

Example:
```c
int* ptr = (int*)malloc(sizeof(int));

*ptr = 100;

free(ptr);
ptr = NULL;
```

---

## 5. Difference Between malloc() and calloc()

| malloc() | calloc() |
|---|---|
| Allocates a single block of specified bytes. | Allocates space for multiple elements. |
| Takes one argument. | Takes two arguments. |
| Memory is uninitialized. | All allocated bytes are initialized to zero. |
| `malloc(n * sizeof(int))` | `calloc(n, sizeof(int))` |

## 6. Difference Between malloc() and new (HCL, )

| malloc() | new |
|---|---|
| Used in C and available in C++. | C++ operator. |
| Returns void pointer in C. | Returns a typed pointer. |
| Does not call constructors. | Calls constructors for class objects. |
| Returns NULL on failure. | Throws std::bad_alloc by default on failure. |
| Requires free() for deallocation. | Requires delete for single-object deallocation. |
| Does not initialize memory by default. | Can initialize objects. |

## 7. delete vs free(). (HCL, )
delete is a C++ operator that deallocates memory allocated using new and calls the object's destructor, whereas free() is a C library function that releases memory allocated using malloc(), calloc(), or realloc() without calling a destructor.

---

# PART B: MEMORY MANAGEMENT IN C++

## 7. Dynamic Memory Allocation in C++
***Explain Dynamic Memory Allocation with Example in c and cpp (XYZ, Larsen & Toubro)***

C++ provides four primary operators:

1. new
2. delete
3. new[]
4. delete[]

### 7.1 new Operator

- Allocates memory dynamically.
- Calls the constructor for class objects.
- Returns a typed pointer.
- Throws std::bad_alloc if allocation fails by default.

Syntax:
```cpp
int* ptr = new int;
```

Initialization:
```cpp
int* ptr = new int(100);
```

Example:
```cpp
#include <iostream>
using namespace std;

int main()
{
    int* ptr = new int(100);

    cout << *ptr << endl;

    delete ptr;
    ptr = nullptr;

    return 0;
}
```

Output:
```text
100
```

### 7.2 delete Operator

- Releases memory allocated using new.
- Calls the object's destructor.
- Prevents memory leaks when used correctly.

Example:
```cpp
int* ptr = new int(50);

delete ptr;
ptr = nullptr;
```

### 7.3 new[] Operator

Used to allocate memory for an array dynamically.

Example:
```cpp
int* arr = new int[5];

for (int i = 0; i < 5; i++)
{
    arr[i] = (i + 1) * 10;
}
```

### 7.4 delete[] Operator

Used to release memory allocated using new[].

Example:
```cpp
int* arr = new int[5];

delete[] arr;
arr = nullptr;
```

Important:
- Use delete for memory allocated with new.
- Use delete[] for memory allocated with new[].
- Never mix new/delete with malloc/free.

---

## 8. Stack vs Heap Memory

| Stack | Heap |
|---|---|
| Used for automatic local variables and call frames. | Used for dynamic memory allocation. |
| Automatically managed. | Lifetime must be managed. |
| Generally faster to allocate. | Allocation may involve more overhead. |
| Limited capacity. | Typically has more available capacity. |
| Lifetime follows scope. | Can outlive the allocating scope. |
| No explicit delete required for automatic variables. | Requires appropriate deallocation or ownership management. |

Example:

```cpp
#include <iostream>
using namespace std;

int main()
{
    // Stack
    int a = 10;

    // Heap
    int* ptr = new int(20);

    cout << a << endl;
    cout << *ptr << endl;

    delete ptr;
    ptr = nullptr;

    return 0;
}
```

---

## 9. Memory Management Problems

### 9.1 Memory Leak
***What is memory leak in C++? (63 Moons, ), Memory Leak with Example & How to Prevent It (Zwayam, )***

A memory leak occurs when dynamically allocated memory is no longer accessible for deallocation but remains allocated.

Example:
```cpp
void display()
{
    int* ptr = new int(100);

    // Missing delete
}
```

Solution:
```cpp
void display()
{
    int* ptr = new int(100);

    delete ptr;
    ptr = nullptr;
}
```

### 9.2 Dangling Pointer

A dangling pointer refers to memory whose lifetime has ended or which has been deallocated.

Example:
```cpp
int* ptr = new int(100);

delete ptr;

// ptr is now dangling
ptr = nullptr;
```

### 9.3 Wild Pointer

A wild pointer is an uninitialized pointer.

Example:
```cpp
int* ptr;

*ptr = 100;  // Undefined behavior
```

Solution:
```cpp
int* ptr = nullptr;
```

### 9.4 Double Delete

Releasing the same allocated memory more than once causes undefined behavior.

Example:
```cpp
int* ptr = new int(100);

delete ptr;
delete ptr;  // Undefined behavior
```

Solution:
```cpp
delete ptr;
ptr = nullptr;
```

### 9.5 Buffer Overflow

Occurs when a program accesses memory beyond the allocated buffer.

Example:
```cpp
int arr[5];

arr[5] = 100;  // Out of bounds
```

Solution: Always validate array indexes and buffer sizes.

---

## 10. Smart Pointers in Modern C++

C++11 introduced smart pointers to manage dynamically allocated memory automatically through ownership and RAII.

Header:
```cpp
#include <memory>
```

Three important smart pointers:

### 10.1 unique_ptr

- Owns an object exclusively.
- Cannot be copied.
- Can be moved.
- Automatically releases memory when its owner is destroyed.

Example:
```cpp
#include <iostream>
#include <memory>
using namespace std;

int main()
{
    unique_ptr<int> ptr = make_unique<int>(100);

    cout << *ptr << endl;

    return 0;
}
```

### 10.2 shared_ptr

- Allows multiple pointers to share ownership.
- Uses reference counting.
- Memory is released when the last owning shared_ptr is destroyed.

Example:
```cpp
#include <iostream>
#include <memory>
using namespace std;

int main()
{
    shared_ptr<int> ptr1 = make_shared<int>(100);

    shared_ptr<int> ptr2 = ptr1;

    cout << *ptr1 << endl;
    cout << *ptr2 << endl;

    cout << ptr1.use_count() << endl;

    return 0;
}
```

Output:
```text
100
100
2
```

### 10.3 weak_ptr

- Does not own the object.
- Does not increase the shared_ptr reference count.
- Used to observe an object managed by shared_ptr.
- Helps break circular ownership.

Example:
```cpp
#include <iostream>
#include <memory>
using namespace std;

int main()
{
    shared_ptr<int> ptr1 = make_shared<int>(100);

    weak_ptr<int> ptr2 = ptr1;

    if (auto temp = ptr2.lock())
    {
        cout << *temp << endl;
    }

    return 0;
}
```

### Smart Pointer Comparison

| unique_ptr | shared_ptr | weak_ptr |
|---|---|---|
| Exclusive ownership. | Shared ownership. | Non-owning reference. |
| Cannot be copied. | Can be copied. | Can be copied. |
| Supports move. | Supports reference counting. | Does not increase strong reference count. |
| Automatic cleanup. | Automatic cleanup. | Does not control object lifetime. |

---

## 11. RAII (Resource Acquisition Is Initialization)

RAII is a C++ programming technique in which resource acquisition and ownership are tied to an object's lifetime.

- Resources are acquired during object construction.
- Resources are released during object destruction.
- Helps prevent memory leaks.
- Used by smart pointers, containers, locks, and file streams.

Example:
```cpp
#include <iostream>
#include <memory>
using namespace std;

class Test
{
public:
    Test()
    {
        cout << "Constructor called" << endl;
    }

    ~Test()
    {
        cout << "Destructor called" << endl;
    }
};

int main()
{
    {
        unique_ptr<Test> ptr = make_unique<Test>();
    }

    return 0;
}
```

Output:
```text
Constructor called
Destructor called
```

---

## 12. Shallow Copy vs Deep Copy

### Shallow Copy

Copies member values directly, including pointer addresses.

Both objects may point to the same dynamically allocated memory.

### Deep Copy

Creates separate memory and copies the actual data.

Each object manages its own memory.

Example:
```cpp
#include <iostream>
using namespace std;

class Test
{
public:
    int* ptr;

    Test(int value)
    {
        ptr = new int(value);
    }

    // Deep copy constructor
    Test(const Test& obj)
    {
        ptr = new int(*obj.ptr);
    }

    ~Test()
    {
        delete ptr;
    }
};

int main()
{
    Test obj1(100);

    Test obj2 = obj1;

    *obj2.ptr = 200;

    cout << *obj1.ptr << endl;
    cout << *obj2.ptr << endl;

    return 0;
}
```

Output:
```text
100
200
```

Note: A complete resource-owning class should also correctly implement or disable copy assignment and provide appropriate move operations (Rule of Five). In production code, prefer standard library containers and smart pointers where possible.

---

## 13. Memory Management in C vs C++

| Feature | C | C++ |
|---|---|---|
| Dynamic allocation | malloc(), calloc() | new, make_unique(), make_shared() |
| Deallocation | free() | delete, smart pointers |
| Reallocation | realloc() | No direct equivalent; use containers or allocate and move/copy |
| Constructor | Not supported for C structs | Supported for class objects |
| Destructor | Not supported | Supported |
| Smart pointers | Not built into the language | unique_ptr, shared_ptr, weak_ptr |
| Memory management style | Mostly manual | Manual and RAII-based |
| Type safety | void* allocation result | Typed allocation result |

---

## 14. Best Practices for Memory Management

1. Initialize pointers with nullptr.
2. Always check whether memory allocation succeeded.
3. Release dynamically allocated resources.
4. Use delete[] for arrays allocated with new[].
5. Never use a pointer after deallocation.
6. Avoid double deletion.
7. Prefer smart pointers in modern C++.
8. Prefer std::vector instead of manually managing dynamic arrays.
9. Use RAII for resource management.
10. Avoid unnecessary dynamic memory allocation.

---

# PART C: INTERVIEW QUESTIONS

### Q1. What is Memory Management?

**Ans.:** Memory management is the process of allocating, using, tracking, and deallocating memory during program execution.

### Q2. What are the different memory segments?

**Ans.:** Code segment, Data segment, BSS segment, Heap, and Stack.

### Q3. What is Stack Memory?

**Ans.:** Stack memory stores function call information and typically local automatic variables. Its lifetime is managed automatically.

### Q4. What is Heap Memory?

**Ans.:** Heap memory is used for dynamic memory allocation at runtime. Its lifetime is managed through explicit deallocation or ownership mechanisms.

### Q5. What is Dynamic Memory Allocation?

**Ans.:** Dynamic memory allocation is the process of allocating memory at runtime according to program requirements.

### Q6. What is the difference between malloc() and calloc()?

**Ans.:** malloc() allocates a specified number of bytes without initializing them. calloc() allocates memory for multiple elements and initializes all allocated bytes to zero.

### Q7. What is realloc()?

**Ans.:** realloc() changes the size of an existing dynamically allocated memory block. It may move the block to another memory location.

### Q8. What is free()?

**Ans.:** free() releases dynamically allocated memory in C.

### Q9. What is the difference between new and malloc()?

**Ans.:** new is a C++ operator that allocates memory and invokes constructors for objects. malloc() is a C library function that allocates raw memory without calling constructors.

### Q10. What is the difference between delete and delete[]?

**Ans.:** delete releases a single dynamically allocated object, whereas delete[] releases an array allocated using new[].

### Q11. What is a Memory Leak?

**Ans.:** A memory leak occurs when allocated memory is not released and the program loses the ability to access and deallocate it.

### Q12. What is a Dangling Pointer?

**Ans.:** A dangling pointer refers to memory that is no longer valid, typically because it has been deallocated or its lifetime has ended.

### Q13. What is a Wild Pointer?

**Ans.:** A wild pointer is an uninitialized pointer that contains an indeterminate address.

### Q14. What is a Smart Pointer?

**Ans.:** A smart pointer is a C++ object that manages resource ownership and automatically releases dynamically allocated memory when appropriate.

### Q15. What is RAII?

**Ans.:** RAII is a C++ technique that ties resource management to object lifetime. Resources are acquired in constructors and released in destructors.

### Q16. What is Shallow Copy?

**Ans.:** Shallow copy copies member values, including pointer addresses, so multiple objects may refer to the same memory.

### Q17. What is Deep Copy?

**Ans.:** Deep copy creates separate memory and copies the actual data, ensuring independent ownership.

### Q18. Can we convert Stack Memory into Heap Memory?

**Ans.:** We cannot directly convert an existing stack variable into heap memory. We allocate new heap memory and copy or move the data into it.

### Q19. What happens if we don't delete dynamically allocated memory?

**Ans.:** The allocated memory remains occupied until it is released or the process terminates. If it becomes inaccessible, it causes a memory leak.

### Q20. Which is better: Stack or Heap?

**Ans.:** Neither is universally better. Stack is suitable for short-lived local variables, while heap is useful for dynamic-sized data and objects that need longer lifetimes.

---

## 15. Important Functions and Operators: Quick Revision

| C | C++ | Purpose |
|---|---|---|
| malloc() | new | Allocate memory |
| calloc() | new with initialization | Allocate and initialize |
| realloc() | Containers / manual reallocation | Resize allocated storage |
| free() | delete | Deallocate memory |
| N/A | new[] | Allocate array |
| N/A | delete[] | Deallocate array |
| N/A | unique_ptr | Exclusive ownership |
| N/A | shared_ptr | Shared ownership |
| N/A | weak_ptr | Non-owning reference |

**Final Interview Takeaway:**

Memory Management is the complete process of handling memory. Dynamic Memory Allocation is one part of it. C primarily uses malloc(), calloc(), realloc(), and free(), while C++ supports new/delete and modern RAII-based smart pointers.


### **Dynamic Memory Allocation in C**

In C, **dynamic memory allocation** allows you to allocate memory at runtime (i.e., while the program is running), rather than at compile time. This is essential when the amount of memory required is not known in advance.

---

### **Key Functions for Dynamic Memory Allocation** (in `<stdlib.h>`):

| Function    | Purpose                                                  |
| ----------- | -------------------------------------------------------- |
| `malloc()`  | Allocates a specified number of bytes                    |
| `calloc()`  | Allocates memory for an array and initializes it to zero |
| `realloc()` | Reallocates memory to change its size                    |
| `free()`    | Frees dynamically allocated memory                       |

---

### **1. `malloc()` (Memory Allocation)**

* Allocates memory block of given size (in bytes).
* Does **not initialize** memory (contains garbage values).

```c
#include <stdlib.h>

int *ptr = (int *) malloc(5 * sizeof(int));  // Allocates memory for 5 integers
```

---

### **2. `calloc()` (Contiguous Allocation)**

* Allocates memory for an array of elements.
* Initializes all bytes to **zero**.

```c
int *ptr = (int *) calloc(5, sizeof(int));  // Allocates memory for 5 integers and initializes to 0
```

---

### **3. `realloc()` (Reallocation)**

* Changes the size of memory previously allocated.
* May return a **new memory location**.

```c
ptr = (int *) realloc(ptr, 10 * sizeof(int));  // Resize to 10 integers
```

---

###  **4. `free()`**

* Frees memory allocated by `malloc`, `calloc`, or `realloc`.
* Prevents memory leaks.

```c
free(ptr);  // Deallocates memory
```

---

###  **Example:**

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *arr;
    int n = 5;

    arr = (int *) malloc(n * sizeof(int));  // Allocate memory

    if (arr == NULL) {
        printf("Memory not allocated!\n");
        return 1;
    }

    for (int i = 0; i < n; i++) {
        arr[i] = i + 1;
    }

    printf("Array: ");
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }

    free(arr);  // Deallocate memory
    return 0;
}
```

---

### **Why Use Dynamic Memory?**

* When array size is not known during compilation.
* For flexible and efficient memory usage.
* To avoid wastage of stack memory.

---

### **Dynamic Memory Allocation in C++**

**Dynamic memory allocation** allows you to allocate memory **at runtime** (not at compile time), using **pointers**.
This is useful when:

* You don't know the required memory size in advance.
* You want to allocate memory during program execution.

---

## Operators Used

| Operator   | Purpose                   | Syntax                   |
| ---------- | ------------------------- | ------------------------ |
| `new`      | Allocates memory          | `int* p = new int;`      |
| `new[]`    | Allocates array of memory | `int* arr = new int[5];` |
| `delete`   | Deallocates single object | `delete p;`              |
| `delete[]` | Deallocates array         | `delete[] arr;`          |

---

## Example 1: Allocate Single Variable

```cpp
#include <iostream>
using namespace std;

int main() {
    int* p = new int;     // allocate memory for 1 int
    *p = 42;              // assign value
    cout << *p << endl;   // Output: 42

    delete p;             // free memory
    return 0;
}
```

---

## Example 2: Allocate Array

```cpp
#include <iostream>
using namespace std;

int main() {
    int* arr = new int[3];   // allocate memory for array of 3 ints

    for (int i = 0; i < 3; ++i)
        arr[i] = i * 10;

    for (int i = 0; i < 3; ++i)
        cout << arr[i] << " ";  // Output: 0 10 20

    delete[] arr;  // free memory
    return 0;
}
```

---

## Important Points

* Memory allocated with `new`/`new[]` **stays until** you explicitly `delete`/`delete[]` it.
* Not deallocating memory causes **memory leaks**.
* Always use `delete`/`delete[]` after `new`/`new[]`.

---

## Dynamic Memory for Custom Class

```cpp
class Student {
public:
    Student() {
        cout << "Constructor called\n";
    }
    ~Student() {
        cout << "Destructor called\n";
    }
};

int main() {
    Student* s = new Student();  // Constructor called
    delete s;                    // Destructor called

    Student* arr = new Student[2];  // Constructor called 2x
    delete[] arr;                   // Destructor called 2x
    return 0;
}
```

---

## 🔚 Summary

| Task              | Use                     |
| ----------------- | ----------------------- |
| Allocate 1 object | `ptr = new Type;`       |
| Allocate array    | `ptr = new Type[size];` |
| Free 1 object     | `delete ptr;`           |
| Free array        | `delete[] ptr;`         |

---

### `delete` vs `delete[]` in C++

Both `delete` and `delete[]` are used to **deallocate memory** in C++ that was previously allocated using `new` or `new[]`.
But they are **not interchangeable**.

---

## 1. `delete`

* Used to **free memory** allocated for **a single object**.
* Matches with `new`.

### Example:

```cpp
int* ptr = new int;   // allocate memory for 1 int
*ptr = 10;

delete ptr;           // correct
```

---

## 2. `delete[]`

* Used to **free memory** allocated for **an array of objects**.
* Matches with `new[]`.

### Example:

```cpp
int* arr = new int[5];   // allocate memory for array of 5 ints

delete[] arr;            // correct
```

---

## What if you mix them up?

### Using `delete` on an array:

```cpp
int* arr = new int[5];
delete arr;   // ❌ undefined behavior (may cause memory leak or crash)
```

### Using `delete[]` on a single object:

```cpp
int* ptr = new int;
delete[] ptr; // ❌ also undefined behavior
```

---

## For Custom Classes

If your class allocates resources, calling the **destructor properly** for each element matters.

```cpp
class MyClass {
public:
    MyClass()  { cout << "Constructor\n"; }
    ~MyClass() { cout << "Destructor\n"; }
};

int main() {
    MyClass* obj1 = new MyClass;
    delete obj1;         // Calls 1 destructor

    MyClass* obj2 = new MyClass[3];
    delete[] obj2;       // Calls destructor 3 times
}
```

**Output:**

```
Constructor
Destructor
Constructor
Constructor
Constructor
Destructor
Destructor
Destructor
```

---

## Summary Table

| Keyword    | Use With         | Calls Destructors | Matches With |
| ---------- | ---------------- | ----------------- | ------------ |
| `delete`   | Single object    | One               | `new`        |
| `delete[]` | Array of objects | Multiple          | `new[]`      |

---
---

###  **1. How is memory freed in C?**

In C, memory allocated dynamically using `malloc()`, `calloc()`, or `realloc()` must be released using the `free()` function to prevent memory leaks.

####  Example:

```c
int* ptr = (int*)malloc(sizeof(int) * 5);  // allocate memory
// use ptr...
free(ptr);  // free allocated memory
```

---

###  **2. How do you allocate memory for a single object and an array in C++?**

Use the `new` operator for dynamic memory allocation in C++.

####  Single Object:

```cpp
int* p = new int;   // allocates memory for one int
*p = 10;
```

#### � Array:

```cpp
int* arr = new int[5];  // allocates memory for array of 5 ints
```

Memory should be released using `delete` or `delete[]`:

```cpp
delete p;
delete[] arr;
```

---

###  **3. What happens if you use `delete` on memory allocated with `malloc()`?**

💥 **Undefined behavior.**
You **must not mix** C and C++ memory management functions.

* `malloc()` → `free()`
* `new` → `delete`
* `new[]` → `delete[]`

Using the wrong deallocator may cause **crashes** or **corruption**.

---

### **4. What is a memory leak? How does it occur in C++?**

A **memory leak** occurs when:

* Memory is allocated dynamically,
* But is **not deallocated** (no `delete`/`free` called),
* So it remains **unusable until program exits**.

#### Example:

```cpp
int* p = new int(10);
// No delete ⇒ memory leak
```

In long-running apps (like servers), leaks can lead to **performance degradation or crashes**.

---

### **5. Explain the use of `new[]` and `delete[]` in C++.**

* `new[]` allocates memory for an **array of objects**.
* `delete[]` deallocates that array.

#### Example:

```cpp
int* arr = new int[5];   // allocates memory for 5 integers
// ... use arr
delete[] arr;            // properly deallocates the array
```

> ❗ Using `delete` instead of `delete[]` on an array results in **undefined behavior**.

---

### **6. What are the differences between stack and heap memory?**

| Feature          | Stack                            | Heap                            |
| ---------------- | -------------------------------- | ------------------------------- |
| Allocation speed | Fast                             | Slower                          |
| Lifetime         | Automatically managed            | Manually managed                |
| Size limit       | Limited (depends on OS)          | Much larger (depends on RAM)    |
| Allocation       | At compile-time or function call | At runtime using `new`/`malloc` |
| Deallocation     | Automatic (function return)      | Manual (`delete` / `free`)      |

---

### **7. How can tools like Valgrind help with dynamic memory?**

**Valgrind** is a tool that:

* Detects **memory leaks**.
* Identifies **invalid memory access** (e.g., using after free).
* Tracks **uninitialized memory reads**.
* Shows **exact line numbers** causing leaks.

#### Example Usage (Linux):

```bash
valgrind ./your_program
```

It reports:

* Blocks of memory that were never freed.
* Locations of memory mismanagement.

---

###  **8. Is it possible to allocate memory for a 2D array dynamically? How?**

Yes. You can allocate a 2D array using **pointers to pointers**.

####  Example in C/C++:

```cpp
int rows = 3, cols = 4;

// Step 1: Allocate memory for row pointers
int** arr = new int*[rows];

// Step 2: Allocate memory for each row
for (int i = 0; i < rows; i++) {
    arr[i] = new int[cols];
}

// ... use arr[i][j]

// Free memory
for (int i = 0; i < rows; i++) {
    delete[] arr[i];
}
delete[] arr;
```

This is commonly used when the array dimensions are not known at compile-time.

---

## **License**
This project is licensed under the MIT License.

---

Happy Coding!




