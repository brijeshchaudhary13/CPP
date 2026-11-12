1. **Write a program for smart pointers with raw pointer? (Capigimini, )**
```cpp
#include <iostream>
#include <memory>
using namespace std;

// 1. Raw Pointer
void rawPointers() {
    int *ptr = new int(5);

    cout << "Raw Pointer: " << *ptr << endl;

    delete ptr;  // Manually free memory
}

// 2. Unique Pointer
void uniquePtr() {
    unique_ptr<int> ptr = make_unique<int>(10);

    cout << "Unique Pointer: " << *ptr << endl;
}

// 3. Shared Pointer
void sharedPtr() {
    shared_ptr<int> ptr = make_shared<int>(20);
    shared_ptr<int> ptr2 = ptr;

    cout << "Shared Pointer: " << *ptr << " " << *ptr2 << endl;

    cout << "Reference Count: " << ptr.use_count() << endl;
}

// 4. Weak Pointer
void weakPtr() {
    shared_ptr<int> ptr = make_shared<int>(30);

    weak_ptr<int> weak = ptr;

    if (auto temp = weak.lock()) {
        cout << "Weak Pointer: " << *temp << endl;
    }
    else {
        cout << "Object no longer exists" << endl;
    }
}

int main() {
    rawPointers();
    uniquePtr();
    sharedPtr();
    weakPtr();

    return 0;
}
```
----
2. **WAP for Define integer array {1,2,3,4,5} ,Calculate the size of the array, Print each element on a newline (Planystech, )**
```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[] = {1, 2, 3, 4, 5};

    // Calculate array size
    int sizearr = sizeof(arr) / sizeof(arr[0]);

    cout << "Size of array: " << sizearr << endl;

    // Print each element on a new line
    for (int i = 0; i < sizearr; i++) {
        cout << arr[i] << endl;
    }

    return 0;
}
```
----
3. **Binary Search vs Linear Search also write Program? (Planystech, )**
```cpp
#include <iostream>
using namespace std;

// Binary Search
bool binarySearch(int arr[], int sizearr, int k)
{
    int s = 0;
    int e = sizearr - 1;

    while (s <= e)
    {
        int m = s + (e - s) / 2;

        if (k == arr[m])
        {
            return true;
        }
        else if (k > arr[m])
        {
            s = m + 1;
        }
        else
        {
            e = m - 1;
        }
    }

    return false;
}

// Linear Search
bool linearSearch(int arr[], int sizearr, int k)
{
    for (int i = 0; i < sizearr; i++)
    {
        if (k == arr[i])
        {
            return true;
        }
    }

    return false;
}

int main()
{
    int arr[] = {1, 2, 3, 4, 5};

    int sizearr = sizeof(arr) / sizeof(arr[0]);

    int k = 4;

    if (linearSearch(arr, sizearr, k))
        cout << "Linear Search: Element Found" << endl;
    else
        cout << "Linear Search: Element Not Found" << endl;

    if (binarySearch(arr, sizearr, k))
        cout << "Binary Search: Element Found" << endl;
    else
        cout << "Binary Search: Element Not Found" << endl;

    return 0;
}
```
----
4. **Write Program for Function Overloading? (XYZ, )**
```cpp
#include <iostream>
using namespace std;

class Addition {
public:

    // Function with integer parameters
    int sum(int a, int b) {
        return a + b;
    }

    // Function with float parameters
    float sum(float a, float b) {
        return a + b;
    }
};

int main() {

    Addition obj;

    cout << "Integer Sum: " << obj.sum(10, 5) << endl;

    cout << "Float Sum: " << obj.sum(10.5f, 5.4f) << endl;

    return 0;
}
```
----
5. **Write a program for Method Overriding (XYZ, )**
```cpp
#include <iostream>
using namespace std;

// Base class
class Animal {
public:
    virtual void sound() {
        cout << "Animal makes a sound" << endl;
    }
};

// Derived class
class Dog : public Animal {
public:
    void sound() override {
        cout << "Dog barks" << endl;
    }
};

// Derived class
class Cat : public Animal {
public:
    void sound() override {
        cout << "Cat meows" << endl;
    }
};

int main() {

    Animal* animal;

    Dog dog;
    Cat cat;

    animal = &dog;
    animal->sound();

    animal = &cat;
    animal->sound();

    return 0;
}
```
---
6. **int a = 5;
   int b[a];
   What happens here? (HCL, )**


Declaration

Valid C++?

int b[5];

Yes

const int a = 5; int b[a];

Yes

int a = 5; int b[a];

No, not standard C++

vector<int> b(a);

Yes

----
7. **A a;
    a += a;
    Explanation this? (HCL, )**

This is an example of Operator Overloading in C++. The += operator must be overloaded in class A for this operation to work.
----
8. **Write array input random 0–9, with odd numbers first, even numbers next (in-place) (HCL, )**
```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[10];

    for (int i = 0; i < 10; i++) {
        cin >> arr[i];
    }

    int index = 0;

    for (int i = 0; i < 10; i++) {
        if (arr[i] % 2 != 0) {
            swap(arr[i], arr[index]);
            index++;
        }
    }

    for (int i = 0; i < 10; i++) {
        cout << arr[i] << " ";
    }

    return 0;
}
```
---
9. **Reads a string and prints each word along with its frequency.(HCL, )**

```cpp
#include <iostream>
#include <sstream>
#include <unordered_map>
using namespace std;

int main() {
    string str;
    getline(cin, str);

    string word;
    unordered_map<string, int> freq;

    stringstream ss(str);

    while (ss >> word) {
        freq[word]++;
    }

    for (auto it : freq) {
        cout << it.first << " : " << it.second << endl;
    }

    return 0;
}
```
---
10. **Given a string s, return the first character that does not repeat (i.e., appears exactly once) in the string. (HCL, )**
```cpp
#include <iostream>
#include <string>
#include <unordered_map>
using namespace std;

char firstNonRepeating(string s) {
    unordered_map<char, int> freq;

    // Count frequency of each character
    for (char ch : s) {
        freq[ch]++;
    }

    // Find the first non-repeating character
    for (char ch : s) {
        if (freq[ch] == 1) {
            return ch;
        }
    }

    return '\0'; // No non-repeating character
}

int main() {
    string s = "swiss";

    char result = firstNonRepeating(s);

    if (result == '\0') {
        cout << "No non-repeating character";
    } else {
        cout << "First non-repeating character: " << result;
    }

    return 0;
}
```
---
11. **What does this code do? (HCL, )**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    []{}();
    return 0;
}
```
Answer
This code compiles successfully without any error and executes normally.

It demonstrates a Lambda Function (Anonymous Function) in C++.

---
12. **Is this code valid? Why or why not? (HCL)**

```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int a;
    int &&b = a;
    return 0;
}
```
Answer
Compilation Error
No, this code is invalid because an rvalue reference cannot bind directly to an lvalue.
---
13. **Print the pattern: (HCL, )**
      * * * * 
    * * * * 
  * * * * 
* * * *

```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cin >> n;

    for (int i = n - 1; i >= 0; i--) {

        // Print spaces
        for (int j = 0; j < i * 2; j++) {
            cout << " ";
        }

        // Print stars
        for (int k = 0; k < n; k++) {
            cout << "* ";
        }

        cout << endl;
    }

    return 0;
}
```
---
14. **Is this code valid? Why or why not? (HCL, )**
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
----
15. **What is the issue in the following code? (HCL, )**
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
Ans: Memory Leak problem
The code has a memory leak because dynamically allocated memory is never released using delete[].

---
16. **Remove duplicate elements while maintaining the order
Problem Statement: Write a C++ program to remove duplicate elements from an array while maintaining the original order of appearance. (Gentrack, )**
```cpp
#include <iostream>
#include <vector>
#include <unordered_set>
using namespace std;

int main() {
    int n;
    cin >> n;

    vector<int> arr(n);

    for (int i = 0; i < n; i++) {
        cin >> arr[i];
    }

    unordered_set<int> seen;

    cout << "Array after removing duplicates: ";

    for (int i = 0; i < n; i++) {
        if (seen.find(arr[i]) == seen.end()) {
            cout << arr[i] << " ";
            seen.insert(arr[i]);
        }
    }

    return 0;
}
```
Alternative: Without Using STL (Nested Loops)
```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cin >> n;

    int arr[100];

    for (int i = 0; i < n; i++) {
        cin >> arr[i];
    }

    cout << "Array after removing duplicates: ";

    for (int i = 0; i < n; i++) {
        bool duplicate = false;

        for (int j = 0; j < i; j++) {
            if (arr[i] == arr[j]) {
                duplicate = true;
                break;
            }
        }

        if (!duplicate) {
            cout << arr[i] << " ";
        }
    }

    return 0;
}
```
---
17. **Write a C++ function to transpose a square matrix in-place.(Gentrack, )**

```cpp
#include <iostream>
using namespace std;

void transpose(int arr[100][100], int n) {

    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {

            // Swap rows and columns
            swap(arr[i][j], arr[j][i]);
        }
    }
}

int main() {
    int n;
    cin >> n;

    int arr[100][100];

    // Input matrix
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cin >> arr[i][j];
        }
    }

    transpose(arr, n);

    // Print transposed matrix
    cout << "Transpose of matrix:" << endl;

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cout << arr[i][j] << " ";
        }
        cout << endl;
    }

    return 0;
}
```
---
18. **Generate Fibonacci Sequence (first N terms), Calculate the Fibonacci Sequence (Recursive version)(Gentrack, )**
```cpp
#include <iostream>
using namespace std;

// Recursive function
int fibonacci(int n) {
    if (n == 0)
        return 0;

    if (n == 1)
        return 1;

    return fibonacci(n - 1) + fibonacci(n - 2);
}

int main() {
    int n;
    cin >> n;

    cout << "Fibonacci Series: ";

    for (int i = 0; i < n; i++) {
        cout << fibonacci(i) << " ";
    }

    return 0;
}
```
```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cin >> n;

    int a = 0, b = 1;

    cout << "Fibonacci Series: ";

    for (int i = 0; i < n; i++) {
        cout << a << " ";

        int c = a + b;
        a = b;
        b = c;
    }

    return 0;
}
```
---
19. **FizzBuzz Problem 
Problem Statement: Print numbers from 1 to N. For multiples of 3 print "Fizz", for 5 print "Buzz", and for both print "FizzBuzz". (Gentrack, )**

```cpp
#include <iostream>
using namespace std;

int main() {
    int n;
    cin >> n;

    for (int i = 1; i <= n; i++) {

        if (i % 15 == 0) {
            cout << "FizzBuzz";
        }
        else if (i % 3 == 0) {
            cout << "Fizz";
        }
        else if (i % 5 == 0) {
            cout << "Buzz";
        }
        else {
            cout << i;
        }

        cout << endl;
    }

    return 0;
}
```
---
20. **Check Palindrome String (Iterative + Recursive) (iQuest, Apexon, Tech Mahindra)**

```cpp
#include <iostream>
#include <string>
using namespace std;

bool checkPalindrome(string str) {
    int len = str.length();

    int s = 0;
    int e = len - 1;

    while (s < e) {
        if (str[s] != str[e]) {
            return false;
        }

        s++;
        e--;
    }

    return true;
}

int main() {
    string str;
    cin >> str;

    if (checkPalindrome(str)) {
        cout << "Palindrome";
    } else {
        cout << "Not Palindrome";
    }

    return 0;
}
```

```cpp
#include <iostream>
#include <string>
using namespace std;

bool checkPalindrome(string str, int s, int e) {

    // Base condition
    if (s >= e) {
        return true;
    }

    // If characters don't match
    if (str[s] != str[e]) {
        return false;
    }

    // Recursive call
    return checkPalindrome(str, s + 1, e - 1);
}

int main() {
    string str;
    cin >> str;

    if (checkPalindrome(str, 0, str.length() - 1)) {
        cout << "Palindrome";
    } else {
        cout << "Not Palindrome";
    }

    return 0;
}
```
----
