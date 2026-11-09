# C / C++ Interview Questions (Company-wise Collection)

> Source: Dassault Systèmes–related prep list. Company names in brackets show where the question was asked.

---

## Table of Contents
1. [Theory Questions](#theory-questions)
2. [Programs](#programs)
3. [Multithreading](#multithreading)
4. [STL](#stl)
5. [DSA](#dsa)
6. [Design Patterns](#design-patterns)
7. [Linux](#linux)
8. [Qt](#qt)
9. [Project](#project)
10. [DBMS](#dbms)
11. [Medical Imaging](#medical-imaging)
12. [Test Automation (Selenium / API / Java / Git)](#test-automation-selenium--api--java--git)
13. [HR / Managerial](#hr--managerial)

---

## Theory Questions

### Self-rating & Profile
- How would you rate yourself in C? (7/10) (Larsen & Toubro)
- How would you rate yourself in C++? (8/10) (Larsen & Toubro)
- What is your role and responsibility? (Cyient, Zwayam)
- Do you have testing experience? (Zwayam)

### C++ Basics
- What is C++ (CPP)? (Siemens)
- Explain the major differences between C++98, C++11, C++14, C++17, and C++20. Which features have you used most, and which version have you used? (FlexTrade)
- What are the embedded concepts of C? (Larsen & Toubro)
- 1. Difference between C & C++? (HCL, Infinite)
- What is an operator and its types in C++? (IBM)
- Describe static and const variables in C++. (63 Moons)
- What are storage classes in C++ and their types? (63 Moons)
- What is `static`? (Intelizign)
- What is a namespace in C++? (63 Moons)
- Scope resolution operator (Tech Mahindra)
- Explain the difference between pre-increment (`++i`) and post-increment (`i++`). (Infinite)
- Recursion, Inline (Infinite, Tech Mahindra)
- Difference between static and dynamic linking (nCircle)
- What is Name Mangling in C++? (BMW)
- What is a Template Class in C++? (BMW, Akkodis)
- If a class variable is declared `mutable`, what is its relevance/use? (Hughes Systique (HSC))
- What is the purpose of the `volatile` keyword? (Hughes Systique (HSC))
- What is `dynamic_cast`? (Hughes Systique (HSC))
- What is the use of the `override` keyword in C++? (Tietoevry)
- What are the advantages of using the `override` keyword? (Tietoevry)
- What is the purpose of the `final` keyword in C++? (Tietoevry)

### OOP
- 2. Explain the OOP pillars. (HCL, 63 Moons, IBM, Siemens, Apexon, Larsen & Toubro)
- What is OOPs? Explain. (Intelizign, Akkodis, WESEE, Tech Mahindra)
- Class vs structure in C++ (IBM)
- Difference between Class and Object (Siemens)
- Size of a class in C++ (BMW)
- If a class has a virtual function, what is its size? (BMW)
- What is an access specifier in C++? (Infinite)
- What is encapsulation? (Infinite)
- What is polymorphism? Types? (Prescient Technologies)
- Polymorphism types and implementations (nCircle)
- Explain runtime polymorphism in C++. (Delta IoT – John Deere, Hughes Systique (HSC))
- We use `virtual` to enable runtime polymorphism (dynamic binding). Without `virtual`, compile-time (static) binding happens. (HCL)
- Explain function overriding, write a program and explain the code. (HCL, Hughes Systique (HSC))
- Explain method overloading vs overriding. (63 Moons, Apexon, Akkodis)
- What is an abstract class in C++? (63 Moons)
- What is an abstract class in C++? Give an example. (FlexTrade)
- Can we make an instance of an abstract class? (FlexTrade)
- How to achieve abstraction in C++? (Delta IoT – John Deere)
- Abstraction vs Interface (in C++); also write code. (Delta IoT – John Deere)
- What is Upcasting and Downcasting? (Tech Mahindra)
- What is Virtual Inheritance? (Tech Mahindra, Dassault Systèmes)
- What is the Diamond Problem? Why does it occur? How can it be resolved? Explain the solution with an example. How can you solve it using virtual inheritance? (Prescient Technologies, Dassault Systèmes)
- What is inheritance in C++? (Dassault Systèmes)
- What are the different types of inheritance in C++? (Dassault Systèmes)
- What is multiple inheritance? (Dassault Systèmes)
- What is hierarchical inheritance? (Dassault Systèmes)
- What is the difference between multiple and hierarchical inheritance? (Dassault Systèmes)
- What is the difference between normal inheritance and virtual inheritance? (Dassault Systèmes)
- What happens if two base classes contain functions with the same name? (Dassault Systèmes)
- How do you resolve ambiguity in multiple inheritance? (Dassault Systèmes)
- In what order are constructors called in multiple inheritance? (Dassault Systèmes)
- What is the difference between compile-time and runtime polymorphism? (Dassault Systèmes)
- Explain a practical use case for multiple inheritance in your project. (Dassault Systèmes)
- Explain a practical use case for hierarchical inheritance in your project. (Dassault Systèmes)
- What is the difference between composition and inheritance? (Hughes Systique (HSC))

### Constructors & Destructors
- 3. Constructors & Destructors? (HCL, Tech Mahindra)
- Can constructors be virtual? (nCircle)
- What is a Virtual Constructor? (Cyient)
- Why do we have virtual destructors but not virtual constructors in C++? (Tech Mahindra)
- Why is a virtual destructor required? (Tech Mahindra)
- Why can we not overload a destructor in C++? (Tech Mahindra)
- Why does a destructor take no parameters? (Tech Mahindra)
- In which scenarios is the copy constructor called? (Tech Mahindra)
- What happens when we don't use `&` in the copy constructor? (Tech Mahindra)
- Move Constructor, Default Constructor, Default Destructor (Intelizign)
- What is a copy constructor? Why define it manually in C++? Write an example. (63 Moons, WESEE)
- If we do not create a constructor, how is it handled in C++? (Tietoevry)
- Constructor and vtables (BMW)
- Explain the constructor initializer list and its advantages. (Delta IoT – John Deere)

### Virtual Functions
- 5. Virtual Functions & the `virtual` keyword? (HCL, Larsen & Toubro)
- What is a virtual function? Explain how virtual functions work internally. Why use them? (FlexTrade, Larsen & Toubro)
- What is a pure virtual function? Why do we use them? (FlexTrade, Delta IoT – John Deere)
- What happens if we remove the `virtual` keyword from a pure virtual function? (Delta IoT – John Deere)
- What is VPtr and VTable? When are they created? (Tietoevry, Hughes Systique (HSC))

### Copy, Move & Related
- 4. Deep copy & Shallow copy? (HCL, Zwayam, Delta IoT – John Deere, nCircle)
- Combined program showing shallow copy vs deep copy (Delta IoT – John Deere)
- 13. Copy & Move Semantics? (HCL)
- 14. Rule of 3 / Rule of 5 (HCL, Delta IoT – John Deere)

### Pointers, References & Memory
- 6. Pointers & References? (HCL, Delta IoT – John Deere)
- Pointer vs Object (FlexTrade)
- Why not use an object instead of a pointer? (FlexTrade)
- Dangling pointer with example (Zwayam, Akkodis)
- What is the use of pointers in C++? (63 Moons)
- What is call by value and call by reference? (63 Moons, Infinite, Tech Mahindra)
- Pass by Value vs Pass by Reference (iQuest, Intelizign)
- 7. NULL and nullptr? (HCL)
- 8. Memory Management? (HCL)
- How to delete memory in C++? (Akkodis)
- What is a memory leak in C++? (63 Moons)
- Memory leak with example & how to prevent it (Zwayam)
- How to convert 2 stacks into a heap? (63 Moons)
- 9. Explain dynamic memory allocation with example in C and C++ (XYZ, Larsen & Toubro)
- 9. `new` vs `malloc()` (HCL)
- 10. `delete` vs `free()` (HCL)
- 11. Smart pointers and their use cases (HCL, Akkodis, Delta IoT – John Deere, nCircle)
- What is `shared_ptr`? (Intelizign)
- 12. RAII? (HCL)

### Modern C++ & Misc.
- 15. Lambda expressions? Why use them? Example. (HCL, iQuest, Akkodis, Ceipal for Siemens)
- Lambda vs Normal Function (Akkodis)
- 16. Debugging & Segmentation Faults? (HCL)
- 17. Friend function? (HCL)
- Friend function in C++: implement a friend function to access private members of a class (Delta IoT – John Deere)
- 18. Exception Handling? (HCL)
- Which debugging tools are commonly used? (Hughes Systique (HSC))
- What tools are used to detect memory leaks? (Hughes Systique (HSC))
- How do you run GDB (GNU Debugger)? (Hughes Systique (HSC))

---

## Programs

### 1. Smart pointers with raw pointer (Capgemini)
Write a program for smart pointers with a raw pointer.

### 2. Array basics (Planystech)
Define integer array `{1,2,3,4,5}`, calculate the size of the array, print each element on a new line.

### 3. Insert at position: Array vs List (Planystech)
How to insert, access, and delete an element at the 1st and 3rd position in an Array vs Linked List? Also state the time complexity.

### 3a. Design patterns other than Singleton? (Capgemini)

### 4. Binary Search vs Linear Search + program (Planystech)

### 5. How to check connection to another computer in Linux? (Planystech)

### 6. How to check your IP in Linux? (Planystech)

### 7. Write a program for Function Overloading (XYZ)

### 8. Write a program for Method Overriding (XYZ)

### 9. What happens here? (HCL)
```cpp
int a = 5;
int b[a];
```

### 10. Explain this (HCL)
```cpp
A a;
a += a;
```

### 11. Odd first, even next (in-place) (HCL)
Write array input (random 0–9) with odd numbers first, even numbers next (in-place).

### 12. Sort words by length (HCL)
Given a sentence, rearrange the words in order of increasing length. The first word in the resulting sentence should be capitalized, and all other words lowercase. Ignore punctuation and the original capitalization of words, except that the first word in the final result must start with a capital letter.

### 12a. Word frequency (HCL)
Read a string and print each word along with its frequency.

### 13. First non-repeating character (HCL)
Given a string `s`, return the first character that does not repeat (appears exactly once).

### 14. What does this code do? (HCL)
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    []{}();
    return 0;
}
```

### 15. Is this code valid? Why or why not? (HCL)
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int a;
    int &&b = a;
    return 0;
}
```

### 16. Print the pattern (HCL)
```
      * * * * 
    * * * * 
  * * * * 
* * * * 
```

### 17. Is this code valid? Why or why not? (HCL)
```cpp
#include <iostream>

int main() {
    int&& r1 = 10;     // OK: rvalue reference binds to a literal (rvalue)
    std::cout << r1 << "\n";  // prints 10

    // int a = 5;
    // int&& r2 = a;   // ERROR: cannot bind rvalue reference to lvalue

    return 0;
}
```

### 18. What is the issue in the following code? (HCL)
```cpp
void createMemory() {
    int* p = new int[100];    
}
 
int main() {
    for (int i = 0; i < 1000; ++i) {
        createMemory();
    }
    return 0;
}
```

### 19. Remove duplicate elements while maintaining order (Gentrack)
Write a C++ program to remove duplicate elements from an array while maintaining the original order of appearance.

### 20. Transpose a square matrix in-place (Gentrack)
Write a C++ function to transpose a square matrix in-place.

### 21. Find the second highest income in SQL (Gentrack)

### 22. Fibonacci (Gentrack)
Generate the Fibonacci sequence (first N terms); also the recursive version.

### 23. FizzBuzz (Gentrack)
Print numbers from 1 to N. For multiples of 3 print "Fizz", for 5 print "Buzz", and for both print "FizzBuzz".

### 24. Nearest floor request in given direction (Gentrack)
A lift (elevator) is currently at a floor and moving in a specific direction ("Up" or "Down"). It has received exactly 3 floor requests from users waiting on different floors. Write a function to determine which requested floor is nearest to the current floor in the same direction as the lift is moving. Return that floor number. If no floor request is valid in the given direction, return -1.

### 25. Check palindrome string — iterative + recursive (iQuest, Apexon, Tech Mahindra)

### 26. Count binary substrings starting and ending with 1 (iQuest)

### 26a. Most frequently occurring character in a word (Expleo)

### 27. Frequency of each character in a word (Expleo)

### 28. `char *str;` vs `char str[];` in C++ (IBM)

### 29. Insertion (`<<`) operator overloading program (IBM)
- `<<` = Insertion operator: used with `cout` to output data to the console.
- `>>` = Extraction operator: used with `cin` to input data from the user.

### 30. What will be the output of the following program? (Cyient)
```cpp
#include <bits/stdc++.h>
using namespace std;

class A 
    { public: 
        A() 
        { 
            cout << "A constructor"<<endl;
        } 
        ~A() {
            cout << "A Destructor"<<endl;
        }
    };
    
class B : public A{ 
    public:
    B(){
        cout << "B constructor"<<endl;
    } 
    ~B() {
        cout << "B Destructor"<<endl;
    } 
}; 

int main() {
    A *p = new B();
    delete p;
    return 0;
}
```

### 31. What change makes `B Destructor` get called in Q30? (Cyient)

### 32. Fix the bug (Cyient)
```cpp
#include <bits/stdc++.h>
using namespace std;

class Base{
    public:
    virtual void show(){
        cout<<"base";
    }
};
class Derived : public Base{
    public:
    void show(){
        cout<<"derived";
    }
};
void display(Base b){
    b.show();
}
//void display(Base &b){    fixed bug
    
int main() {
   Derived d;
   display(d);
}
```

### 33. Fix the error (Cyient)
```cpp
#include <bits/stdc++.h>
#include<thread>
using namespace std;

int counter=0;
void worker(){
    for(int i=0; i<1000; i++){
        counter++;
    }
}
int main() {
    thread t1(worker);
    thread t2(worker);
    t1.join();
    t2.join();
    cout<<counter;
}
```

### 34. Reverse a string (Cyient)
```cpp
#include <bits/stdc++.h>
using namespace std;
int counter=0;
int main() {
    string str;
    getline(cin,str);
    //cin>>str;
    int n=str.length()-1;
    int i=0;
    while(i<n){
        char temp = str[i];
        str[i]=str[n];
        str[n]=temp;
        i++;
        n--;
    }
    cout<<str<<"end";
}
```

### 35. Reverse a line word by word in C++ (Zwayam)

### 36. Insert an element into a sorted array (Siemens)
Given a sorted array, insert an element and maintain the sorted order.
```
Input:
int arr[] = {1, 3, 5, 7};
int n = 6;
Output:
1, 3, 5, 6, 7 
```

### 36a. Pattern (Intelizign)
```
1
1 2
1 2 3
```

### 37. Pointer behavior (Intelizign)
```cpp
{ 
int* p1;
int m = 100;
p1 = &m;
*p1++;  // increment pointer, not value. Equivalent to p1 = p1 + 1
++m;    // m = 101
printf("%d\n", *p1); // Undefined behavior
printf("%d\n", m);   // 101
}
```
But:
```cpp
{
int m = 100;
int* p1;
p1 = &m;
(*p1)++;  // increments value
++m;      // m = 102 overall
printf("%d\n", *p1); // 102
printf("%d\n", m);   // 102
}
```

### 38. Call `dosomething` function in `main()` (Intelizign)
Case 1 (pointer):
```cpp
#include <iostream>
using namespace std;

void dosomething(int *p) {
    cout << "Inside function: " << *p << endl;
}

int main() {
    int P = 10;
    int *ptr = &P;
    dosomething(ptr);
    return 0;
}
```
Case 2 (reference):
```cpp
void dosomething(int &p) {
    cout << "Inside function: " << p << endl;
}

int main() {
    int P = 10;
    dosomething(P);
}
```

### 39. Create a class with const, reference, char pointer, static data member (Tietoevry)

### 40. Create all types of constructors + destructor. Why use them? (Tietoevry)

### 41. Write output (Tietoevry)
```cpp
int arr[] = {10, 20, 30, 40, 50};
int *p = arr;

cout << *p;        // Output: 10
cout << *p++;      // Output: 10 (p now points to arr[1])
cout << (*p)++;    // Output: 20 (then arr[1] becomes 21)
```

### 42. Write output & explain (Tietoevry)
```cpp
int x = 10, y = 20;
const int* p1 = &x; // Pointer to const int (data is const, pointer not)
int* const p2 = &x; // Constant pointer to int (pointer const, data not)

*p1 = 20;    // Error: can't modify value through pointer to const
p1 = &y;     // OK: pointer itself can point elsewhere

*p2 = 30;    // OK: data can be modified
p2 = &y;     // Error: can't change address held by const pointer
```

### 43. Insert pairs in a map and print using an iterator (Tietoevry)

### 44. Eliminate duplicate characters from `"Hello World!!!"` without using STL (Calsoft Inc)

### 45. First non-repeating character in a string (Apexon)
```cpp
#include <iostream>
using namespace std;

int main() {
    string str;
    cin >> str;
    int found = 0;

    for (int i = 0; str[i] != '\0'; i++) {
        found = 0;
        for (int j = 0; str[j] != '\0'; j++) {
            if (str[i] == str[j] && i != j) {
                found = 1;
                break;
            }
        }
        if (!found) {
            cout << "First non-repeating character: " << str[i] << endl;
            return 0;
        }
    }

    cout << "No non-repeating character found." << endl;
    return 0;
}
```

### 46. First repeating character in a string (Apexon)
```cpp
#include <iostream>
using namespace std;

int main() {
    string str;
    cin >> str;
    int found = 0;

    for (int i = 0; str[i] != '\0'; i++) {
        for (int j = i + 1; str[j] != '\0'; j++) {
            if (str[i] == str[j]) {
                cout << "First repeating character: " << str[i] << endl;
                return 0;
            }
        }
    }

    cout << "No repeating character found." << endl;
    return 0;
}
```

### 46a. Vector: store 1–10, remove 5, print (FlexTrade)
Write a C++ program to store integers 1–10 in a vector, remove element 5, then print the vector.

### 47. Fix and explain this code (Akkodis)
Original (problematic):
```cpp
B obj;
obj & =A obj;  // Invalid syntax
obj->sum(15,10);  // obj is not a pointer
```
Correct version:
```cpp
#include <iostream>
using namespace std;

class A {
public:
    virtual int sum(int a, int b) {
        return a + b;
    }
    void sum(float a, float b) {
        cout << "Float sum: " << a + b << endl;
    }
};

class B : public A {
public:
    int sum(int a, int b) override {
        int c = a + b;
        cout << "B::sum(int, int): " << c << endl;
        return c;
    }
};

int main() {
    B* obj = new B();
    A* ref = obj;

    ref->sum(15, 10);  // Will call B's version (runtime polymorphism)

    delete obj;
    return 0;
}
```

### 48. Replace second occurrence with "l.k" (Akkodis)
```
Input:  apple
Output: al.kle
```

### 49. Add, sub, multiply functions called in a loop (Cybage)
Write 3 functions (add, sub, multiply), each taking 2 parameters and returning an int. Then call the functions using a loop.

### 50. File handling example (Cybage)
Write to a file and read from a file.

### 51. `extern` across files (Cybage)
To access a variable (`g_i`) defined in `first.cpp` from `second.cpp`, declare it as `extern` in `second.cpp`. This tells the compiler the variable is defined in another translation unit, and the linker resolves it during linking.

### 52. What output will come? (WESEE)
```cpp
#include <iostream>
using namespace std;

class Animal {
public:
    virtual void sound() {
        cout << "Animal makes a sound" << endl;
    }
};

class Dog : public Animal {
public:
    void sound() override {
        cout << "Dog barks" << endl;
    }
};

class Cat : public Animal {
public:
    void sound() override {
        cout << "Cat meows" << endl;
    }
};

int main() {
    Animal *ptr;

    Dog d;
    Cat c;

    ptr = &d;
    ptr->sound();    // Dog barks

    ptr = &c;
    ptr->sound();    // Cat meows

    return 0;
}
```

### 53. What output will come? (WESEE)
```cpp
#include <iostream>
using namespace std;

class Animal {
public:
     void sound() {
        cout << "Animal makes a sound" << endl;
    }
};

class Dog : public Animal {
public:
    void sound() override {
        cout << "Dog barks" << endl;
    }
};

class Cat : public Animal {
public:
    void sound() override {
        cout << "Cat meows" << endl;
    }
};

int main() {
    Animal *ptr;

    Dog d;
    Cat c;

    ptr = &d;
    ptr->sound();

    ptr = &c;
    ptr->sound();

    return 0;
}
```

### 54. Write all types of constructors for the given variables (Ceipal for Siemens)
```cpp
const char* ptr;   // pointer to const char
double num;        // a number
string str;        // a string
double &num2;      // reference to a double
```

### 55. Store values and display them in a vector (Delta IoT – John Deere)

### 56. Lambda to filter even numbers (Delta IoT – John Deere)
Take input in an array and use a lambda function to filter even numbers.

### 57. Class with an integer member and a pointer member (Delta IoT – John Deere)
Implement:
- Default constructor
- Copy constructor
- Assignment operator
- Destructor

Also answer:
- Why do we use `return *this;` in the last line of the assignment operator function?
- When do we use `void` as a return type instead of returning `*this`?
- What is chaining assignment in C++?

### 58. Reverse a string (Infinite)

### 59. Swap two numbers (Infinite)

### 60. Sum of digits (nCircle)

### 61. Reverse a linked list (GeeksforGeeks problem) (nCircle)

### 62. Operator overloading example (nCircle)

### 63. `const` and reference example (nCircle)

### 64. Pointers with `const` (nCircle)
```cpp
int x = 20;
int c = 30;
const int* a;      // pointer to const int (you cannot modify *a)
int const* b;      // same as above
int* const d = &x; // constant pointer (cannot change address stored)

a = &x;   // OK
//*a = 5; // Error: *a is const
a = &c;   // OK

*d = 25;  // OK: can change value through pointer
//d = &c; // Error: d is a const pointer
```

### 65. Base and Derived class example (Tietoevry)
Constructor initialization + member initialization + output explanation.
```cpp
#include <iostream>
using namespace std;

class Base {
    int id1;
    int id2;
public:
    Base(int i, int j) : id2(j), id1(i) {
        cout << "Base class\n";
    }
};

class Derived : public Base {
    int id3;
    int id4;
public:
 //Derived(int a, int b, int c, int d) :  id3(c), id4(d + id3), Base(a, b) {
    Derived(int a, int b, int c, int d) : Base(a, b), id3(c), id4(d + id3) {
        cout << "Derived class\n";
    }

    void getVal() {
        cout << id3 << " " << id4 << "\n";
    }
};

int main() {
    Derived d(10, 20, 30, 40);
    d.getVal();
    return 0;
}
```

### 69. Flow of constructor calls in inheritance + simple runtime polymorphism (Tietoevry)
Demonstrates `virtual`, `override`, and const initialization.
```cpp
#include <iostream>
using namespace std;

class A {
public:
    const int x;
    A(int val) : x(val) {}

    virtual void show() {
        cout << "Class A" << endl;
    }
};

class B : public A {
public:
    B(int val) : A(val) {}

    void show() override {
        cout << "Class B" << endl;
    }
};

int main() {
    A* obj = new B(10);
    obj->show();   // Runtime polymorphism
    delete obj;
    return 0;
}
```

### 70. Reverse words (Tietoevry)
Input string: `"This is my text"` → Output string: `"text my is This"` — explain or implement.

### 70a. `const int` in a class (Tietoevry)
Explain the use of `const int` in a class and how to initialize it.

### 71. Simple runtime polymorphism code using base and derived classes (Tietoevry)

### 72. How do you initialize a constant data member inside a class?

### 73. Print the pattern (n = 5) (Tech Mahindra)
```
    *
   **
  ***
 ****
*****
```

### 74. Replace zeros and sort the final array (Tech Mahindra)
```
Array 1: 1 2 3 0 0 0
Array 2: 2 5 6
```

### 75. Shared pointer class implementation (Hughes Systique (HSC))
Write a shared pointer class implementation (without using any library). Also explain: what is a shared pointer?

### 76. Class `A` with member variables (Hughes Systique (HSC))
```cpp
static int x;
char* y;
std::string s;
const int p;
int& q;
```

#### 76.1 Follow-ups
- Why is `int A::x = 0;` initialized with zero?
- Write a method to display the static variable; inside this method increment `x++` and `q++`.
- Add one more method named `add()`. If we perform `p++` inside `add()`, what will happen?
- If we make `add()` const, what changes?
- If we remove `p++`, will the code compile and work?

### 77. Demonstrate all major OOPS features in C++ (Acitivia)
- Smart pointers: write a program for all smart pointers. (Acitivia)

### 78. Function pointer program (Acitivia)
Takes two integers and returns one float.

### 79. Sum of both diagonals of a matrix (Prescient Technologies)
Take the matrix size dynamically as input from the user.

### 80. Implement `strcpy` without using any inbuilt functions (Hughes Systique (HSC))

### 81. Multiple inheritance program (Dassault Systèmes)
Write a C++ program to demonstrate multiple inheritance.

### 82. Hierarchical inheritance program (Dassault Systèmes)
Write a C++ program to demonstrate hierarchical inheritance.

---

## Multithreading
1. What is multithreading in C++? (63 Moons, Cyient, Akkodis, Ceipal for Siemens, Delta IoT – John Deere, Larsen & Toubro)
2. Write a multithreading example in C++. (Cyient)
3. What is `join()` in threads? What happens if we don't use `join()`? (Cyient)
4. What is a race condition? How to prevent it? (Apexon)
5. What is mutual exclusion? (Apexon)
6. Functors (Ceipal for Siemens)
7. What is synchronization? (Apexon)
8. How to prevent deadlock? (Delta IoT – John Deere)
9. Can you give an example of a mutex? (Larsen & Toubro)

---

## STL
1. What is STL? Basics of STL in C++. (63 Moons, Delta IoT – John Deere, Larsen & Toubro)
2. Map, Array vs Map, Vector, Vector vs Array (HCL)
3. Difference between Array and ArrayList (Gentrack)
4. Difference between List, Set, and HashMap (Gentrack)
5. Describe LinkedList (Gentrack)
6. `size()` vs `capacity()` in `std::vector` (BMW)
7. Array vs Vector vs List (Intelizign, Delta IoT – John Deere)
8. What is the difference between list and vector in C++? (Larsen & Toubro)
9. Difference between vector and queue? (Apexon)
10. What is the difference between arrays and vectors in C++? When would you prefer one over the other? (FlexTrade)
11. What are the pros and cons of using `std::list` and `std::vector` in C++? (Delta IoT – John Deere)

---

## DSA
1. What's your favorite DSA question? (63 Moons)
2. Stack implementation (using STL stack, and without STL) (Cybage)
3. What is a list? (Apexon)
4. How do you detect a cycle in a circular linked list? (Apexon)
5. Difference between singly and doubly linked list? (Apexon)
6. What is the difference between an iterator and recursion? (Infinite)
7. Linked list reversal algorithms (nCircle)

---

## Design Patterns
- What are design patterns? (Delta IoT – John Deere)
- 1. Singleton class (HCL, Cybage, Delta IoT – John Deere, nCircle)
  - Try to copy a Singleton object (copy constructor) (Cybage)
- Do you use design patterns in your project? (Cyient)
- 2. Explain SOLID principles. (Intelizign)
- What is the difference between composition and inheritance? (Hughes Systique (HSC))

---

## Linux
- Do you know Linux? (IBM)

---

## Qt
1. Explain Signals and Slots in Qt. (WESEE)
2. What are Qt Widgets? (WESEE)
3. Explain QObject and QCoreApplication in Qt. (WESEE)
4. Write a Qt Widgets program to create a window using QWidget. (Prescient Technologies)
5. Write a QML program to create a window with a button. (Prescient Technologies)
6. Write a program in Qt Widgets and QML where clicking a button displays a log message. (Prescient Technologies)

---

## Project
- Tell me about yourself. (Prescient Technologies, Dassault Systèmes)
- Tell me about your current project. (Prescient Technologies, Dassault Systèmes)
- Tell me about your previous project at TCS. (Dassault Systèmes)
- Give your introduction based on your project in detail. (Prescient Technologies)
- What were your roles and responsibilities in this project? (Tech Mahindra, IBM)
- What was your daily work / day-to-day activities? (Tech Mahindra, IBM)
- Tell me about your project in detail. (Apexon, WESEE, IBM)
- How did you make 32-bit signal support 64-bit signal in your project? (Apexon)
- Any issue related to pointers you faced? How did you solve it? (Apexon)
- Do you use pointers? (Apexon)
- Do you use STL? (Apexon)
- Do you use multithreading? (Apexon, Larsen & Toubro)
- Do you use any library for multithreading? (Apexon)
- Explain any scenario where you used multithreading. (Apexon)
- Write the algorithm for your daily routine in your project. (Infinite)
- Describe your roles and responsibilities in the project. (Infinite)
- What is GitHub and how is it used in software development? (Infinite)
- How do clients request changes or updates in your project, and how do you manage those requests? (Infinite)
- How do you check, install, or update packages in a GitHub repository? (Infinite)
- What is the MDF4 format and where is it used? (Infinite)
- Describe a major problem or challenge you faced in your project and how you solved it. (Infinite)
- How many modules does your project have, and what are their main functions? (Infinite)
- What is the use case of STL in your project? (Larsen & Toubro)
- What are the use cases of vector, map, and list in your project? (Larsen & Toubro)

---

## DBMS
- What is the difference between a primary key and a unique key in databases? Table, Data? (Infinite)
- Do you have experience in SQL? (Larsen & Toubro)
- Write a SQL query to find the third largest salary from the employees table. (IBM) *(related: second highest income, Gentrack, Program 21)*

---

## Medical Imaging
- What is the difference between PNG, JPG, and DICOM images? (Prescient Technologies)
- What is metadata in medical imaging? (Prescient Technologies)
- What is a DICOM tag? (Prescient Technologies)
- What is DCMTK, and why is it used? (Prescient Technologies)
- Why do we use OpenCV in medical imaging? (Prescient Technologies)
- Write a C++ program to load a CT DICOM image and display image information using DICOM tags. The program does not need to be executed. (Prescient Technologies)

---

## Test Automation (Selenium / API / Java / Git)

### Round 1 (Technical) (IBM)
1. Explain about your project. (also listed under Project)
2. How do you perform right click in Selenium?
3. What are the different ways to click on a button in Selenium?
4. What are Java Streams?
5. Explain `git pull` vs `git fetch`.
6. Explain your framework architecture.
7. Write a sample test script, as per the framework structure you explained.
8. What are `RequestSpecification` and `ResponseSpecification`?
9. Explain Fluent Wait; write down the syntax as well.
10. Write a SQL query to find the third largest salary from the employees table.

### Round 2 (Techno-Managerial) (IBM)
1. Explain your roles and responsibilities in your current project. (also listed under Project)
2. What are the challenges you faced in your team?
3. You want to create a new framework; how will you proceed?
4. What is the Message House technique? Explain it.
5. How will you collaborate with your team?
6. Suppose I give you a website; how will you proceed with automation, and what questions will you ask?
7. What is your day-to-day task? (also listed under Project)
8. What is Agile? How long is your sprint?
9. API status code 502: when does it come?

---

## HR / Managerial

- What is your notice period? (Dassault Systèmes)
- Salary discussion. (IBM, Round 3 HR)
- Location where you are currently put up. (IBM, Round 3 HR)
- How soon can you join? (IBM, Round 3 HR)
