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

----
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
Compilation Error No, this code is invalid because an rvalue reference cannot bind directly to an lvalue.

---

13. **Print the pattern: (HCL, )**
```cpp
      * * * * 
    * * * * 
  * * * * 
* * * *
```
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
21. **Count Binary Substrings Starting and Ending with 1 (iQuest, )**

```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string s;
    cin >> s;

    long long count = 0;

    for (char c : s)
    {
        if (c == '1')
            count++;
    }

    long long result = count * (count - 1) / 2 + count;

    cout << result << endl;

    return 0;
}
```
----
22. **Find the Most Frequently Occurring Character in a Word (Expleo, )**
```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string s;
    cin >> s;

    int freq[256] = {0};

    for (char c : s)
    {
        freq[(unsigned char)c]++;
    }

    int maxFreq = 0;
    char result;

    for (char c : s)
    {
        if (freq[(unsigned char)c] > maxFreq)
        {
            maxFreq = freq[(unsigned char)c];
            result = c;
        }
    }

    cout << "Most frequent character: " << result << endl;
    cout << "Frequency: " << maxFreq << endl;

    return 0;
}
```
---
23. **Frequency of Each Character in a word (Expleo, )**
```cpp
#include <iostream>
#include <string>
using namespace std;

int main()
{
    string s;
    cin >> s;

    int freq[256] = {0};

    // Count frequency of each character
    for (char c : s)
    {
        freq[(unsigned char)c]++;
    }

    // Print frequency of each character
    for (int i = 0; i < s.length(); i++)
    {
        unsigned char c = s[i];

        if (freq[c] != 0)
        {
            cout << c << " : " << freq[c] << endl;

            // Avoid printing duplicate characters
            for (int j = 0; j < s.length(); j++)
            {
                if (s[j] == c)
                    freq[(unsigned char)s[j]] = 0;
            }
        }
    }

    return 0;
}
```
----
24. **char *str; vs char str[]; in C++ (IBM, )**
---
25. Program for Insertion (<<) Operator Overloading (IBM, )
<< = Insertion Operator: Used with cout to output data to the console.
>> = Extraction Operator: Used with cin to input data from the user.
```cpp
#include <iostream>
using namespace std;

class Student
{
    int id;
    string name;

public:
    Student(int i, string n)
    {
        id = i;
        name = n;
    }

    // Overloading insertion operator <<
    friend ostream& operator<<(ostream& out, Student& s)
    {
        out << "Student ID: " << s.id << endl;
        out << "Student Name: " << s.name << endl;

        return out;
    }
};

int main()
{
    Student s1(101, "Rahul");

    cout << s1;

    return 0;
}
```
26. **What will come output of following Program? (Cyient, )**
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
	// your code goes here
	A *p = new B();
	delete p;
	return 0;

}
```
output
```
A constructor
B constructor
A Destructor
```
---
27. **What make you changes that B Destructor will call ? in 30 number q (Cyient, )**
Answer: Make the base class destructor virtual.

```cpp
#include <bits/stdc++.h>
using namespace std;

class A
{
public:
    A()
    {
        cout << "A constructor" << endl;
    }

    virtual ~A()
    {
        cout << "A Destructor" << endl;
    }
};

class B : public A
{
public:
    B()
    {
        cout << "B constructor" << endl;
    }

    ~B()
    {
        cout << "B Destructor" << endl;
    }
};

int main()
{
    A *p = new B();

    delete p;

    return 0;
}
```
Correct Output
A constructor
B constructor
B Destructor
A Destructor

28. **Fix the bug (Cyient, )**
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
ans:
The bug is caused by object slicing in the function display(Base b).

When a Derived object is passed by value, only the Base part is copied. Therefore, the overridden Derived::show() is not called.

Fixed code

```cpp
#include <bits/stdc++.h>
using namespace std;

class Base {
public:
    virtual void show() {
        cout << "base";
    }
};

class Derived : public Base {
public:
    void show() override {
        cout << "derived";
    }
};

// Fix: Pass Base by reference
void display(Base &b) {
    b.show();
}

int main() {
    Derived d;
    display(d);

    return 0;
}
```
Output

derived


29. **Fix the error (Cyient, )**
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
solution
```cpp
#include <bits/stdc++.h>
#include <thread>
using namespace std;

int counter = 0;
mutex mtx;

void worker() {
    for (int i = 0; i < 1000; i++) {
        mtx.lock();
        counter++;
        mtx.unlock();
    }
}

int main() {
    thread t1(worker);
    thread t2(worker);

    t1.join();
    t2.join();

    cout << counter;

    return 0;
}
```
Alternative solution using atomic
```cpp
#include <bits/stdc++.h>
#include <thread>
#include <atomic>
using namespace std;

atomic<int> counter(0);

void worker() {
    for (int i = 0; i < 1000; i++) {
        counter++;
    }
}

int main() {
    thread t1(worker);
    thread t2(worker);

    t1.join();
    t2.join();

    cout << counter;

    return 0;
}
```
---
30. **Reverse a string (Cyient, Infinite )**
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
solution
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    string str;
    getline(cin, str);

    int n = str.length() - 1;
    int i = 0;

    while (i < n) {
        char temp = str[i];
        str[i] = str[n];
        str[n] = temp;

        i++;
        n--;
    }

    cout << str;

    return 0;
}
```
---
 31. **Write a C++ program to reverse the order of words in a given sentence without reversing the individual characters of each word. Input string: "This is my text" → Output string: "text my is This" — explain implement.(Zwayam, Tietoevry)**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    string str;
    getline(cin, str);

    stringstream ss(str);
    string word;
    vector<string> words;

    while (ss >> word) {
        words.push_back(word);
    }

    for (int i = words.size() - 1; i >= 0; i--) {
        cout << words[i] << " ";
    }

    return 0;
}
```

or
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    string str;
    getline(cin, str);

    // Reverse the entire string
    reverse(str.begin(), str.end());

    int start = 0;

    for (int i = 0; i <= str.length(); i++) {
        if (i == str.length() || str[i] == ' ') {
            reverse(str.begin() + start, str.begin() + i);
            start = i + 1;
        }
    }

    cout << str;

    return 0;
}
```
---
32. Program: Insert an Element into a Sorted Array (Siemens, )
Problem: Given a sorted array, insert an element and maintain the sorted order.
Input:
int arr[] = {1, 3, 5, 7};
int n = 6;
Output:
1, 3, 5, 6, 7 


```cpp
#include <stdio.h>

int main() {
    int arr[10] = {1, 3, 5, 7};
    int n = 6;
    int sizearr = 4;
    int i;

    // Find the correct position
    for(i = 0; i < sizearr; i++) {
        if(arr[i] > n) {
            break;
        }
    }

    // Shift elements to the right
    for(int j = sizearr; j > i; j--) {
        arr[j] = arr[j - 1];
    }

    // Insert the element
    arr[i] = n;

    sizearr++;

    // Print the array
    for(int j = 0; j < sizearr; j++) {
        printf("%d ", arr[j]);
    }

    return 0;
}
```
---
33. **Pattern: (Intelizign, )**
```
1
1 2
1 2 3
```
```cpp
#include <iostream>
using namespace std;

int main() {
    int n = 3;

    for(int i = 1; i <= n; i++) {
        for(int j = 1; j <= i; j++) {
            cout << j << " ";
        }
        cout << endl;
    }

    return 0;
}
```
----
34. **Pointer behavior (Intelizign, )**
```
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
But
```
{
int m = 100;
int* p1;
p1 = &m;
(*p1)++;  // increment pointer, not value. Equivalent to p1 = p1 + 1
++m;    // m = 101
printf("%d\n", *p1); //102
printf("%d\n", m);   // 102
}
```

 35. **Call dosomething function in main() (Intelizign, )**
    Case 1:
```
    #include <iostream>
    using namespace std;
    
    void dosomething(int *p) {
        cout << "Inside function: " << *p << endl;  // prints the value pointed by p
    }
    
    int main() {
        int P = 10;
        int *ptr = &P;       // create pointer to P
        dosomething(ptr);    // pass pointer to function
        return 0;
    }
    ```
    case 2:
``` void dosomething(int &p) {
        cout << "Inside function: " << p << endl;  // no dereferencing needed
    }
    
    int main() {
        int P = 10;
        dosomething(P);  // pass variable by reference
    }
 ```
36. **Create a class with const, reference, char pointer, static data member (Tietoevry, )**
```cpp
#include <iostream>
using namespace std;

class Student {
    const int id;       // Constant data member
    int &ref;           // Reference data member
    const char *name;   // Character pointer
    static int count;   // Static data member

public:
    Student(int i, int &r, const char *n)
        : id(i), ref(r), name(n) {
        count++;
    }

    void display() {
        cout << "ID: " << id << endl;
        cout << "Reference value: " << ref << endl;
        cout << "Name: " << name << endl;
        cout << "Count: " << count << endl;
    }

    static void showCount() {
        cout << "Total objects: " << count << endl;
    }
};

// Initialize static data member
int Student::count = 0;

int main() {
    int x = 100;

    Student s1(1, x, "Rahul");
    Student s2(2, x, "Amit");

    s1.display();

    cout << "\nAfter creating second object:\n";
    s2.display();

    Student::showCount();

    return 0;
}
```

37. **Create all types of constructors + destructor. Why use them? (Tietoevry, )**
```cpp
#include <iostream>
#include <utility>
using namespace std;

class Student {
    int id;
    string name;

public:

    // 1. Default constructor
    Student() {
        id = 0;
        name = "Unknown";
        cout << "Default Constructor called" << endl;
    }

    // 2. Parameterized constructor
    Student(int i, string n) {
        id = i;
        name = n;
        cout << "Parameterized Constructor called" << endl;
    }

    // 3. Copy constructor
    Student(const Student &s) {
        id = s.id;
        name = s.name;
        cout << "Copy Constructor called" << endl;
    }

    // 4. Constructor with default arguments
    Student(int i) {
        id = i;
        name = "Default Name";
        cout << "Default Argument Constructor called" << endl;
    }

    // 5. Move constructor
    Student(Student &&s) noexcept {
        id = s.id;
        name = move(s.name);
        cout << "Move Constructor called" << endl;
    }

    // Display function
    void display() {
        cout << "ID: " << id << endl;
        cout << "Name: " << name << endl;
    }

    // Destructor
    ~Student() {
        cout << "Destructor called for " << name << endl;
    }
};

int main() {

    // Default constructor
    Student s1;
    s1.display();

    cout << endl;

    // Parameterized constructor
    Student s2(101, "Rahul");
    s2.display();

    cout << endl;

    // Copy constructor
    Student s3(s2);
    s3.display();

    cout << endl;

    // Constructor with default arguments
    Student s4(102);
    s4.display();

    cout << endl;

    // Move constructor
    Student s5(move(s2));
    s5.display();

    return 0;
}
```
---
38. **Write output (Tietoevry, )**
```
int arr[] = {10, 20, 30, 40, 50};
int *p = arr;
cout << *p;        // Output: 10
cout << *p++;      // Output: 10 (p now points to arr[1])
cout << (*p)++;    // Output: 20 (then arr[1] becomes 21)
```
---
39. **Write output & explain (Tietoevry, )**
```
int x = 10, y = 20;
const int* p1 = &x; // Pointer to const int (data is const, pointer not)
int* const p2 = &x; // Constant pointer to int (pointer const, data not)

*p1 = 20;    // Error: can't modify value through pointer to const
p1 = &y;     // OK: pointer itself can point elsewhere

*p2 = 30;    // OK: data can be modified
p2 = &y;     // Error: can't change address held by const pointer
```
---
40. **Insert pairs in map and print using iterator (Tietoevry, )**
```cpp
#include <iostream>
#include <map>
using namespace std;

int main() {

    map<int, string> m;

    // Insert pairs into map
    m.insert({1, "Rahul"});
    m.insert({2, "Amit"});
    m.insert({3, "Priya"});
    m.insert({4, "John"});

    // Print using iterator
    map<int, string>::iterator it;

    for(it = m.begin(); it != m.end(); it++) {
        cout << it->first << " : " << it->second << endl;
    }

    return 0;
}
```
---

41. **Eliminate duplicate characters from "Hello World!!!" without using STL (Calsoft Inc)**
```cpp
#include <iostream>
using namespace std;

int main() {
    char str[] = "Hello World!!!";

    int n = 0;

    // Find string length
    while(str[n] != '\0') {
        n++;
    }

    // Remove duplicate characters
    for(int i = 0; i < n; i++) {

        for(int j = i + 1; j < n; j++) {

            if(str[i] == str[j]) {

                // Shift characters to the left
                for(int k = j; k < n; k++) {
                    str[k] = str[k + 1];
                }

                n--;
                j--;
            }
        }
    }

    cout << "String after removing duplicates: ";

    for(int i = 0; i < n; i++) {
        cout << str[i];
    }

    return 0;
}
```
---

42. **First Non Repeating Character in a String (Apexon, )**
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
----
43 **First Repeating Character in a String (Apexon, )**
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
---
44. **Write a C++ program to store integers 1–10 in a vector and remove element 5 from vector then print vector? (FlexTrade, )**

```cpp
#include <iostream>
#include <vector>
using namespace std;

int main() {
    vector<int> arr;

    // Insert numbers 1 to 10
    for(int i = 0; i < 10; i++) {
        arr.push_back(i + 1);
    }

    // Remove element 5 by shifting
    for(int i = 4; i < 9; i++) {
        arr[i] = arr[i + 1];
    }

    arr.pop_back();

    // Print vector
    for(int i = 0; i < arr.size(); i++) {
        cout << arr[i] << " ";
    }

    return 0;
}
```
---

45 **Fix and Explain This Code (Akkodis, )**
Original Code (Problematic):
B obj;
obj & =A obj;  //  Invalid syntax
obj->sum(15,10);  //  obj is not a pointer

Correct version
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
    // Make object dynamically
    B* obj = new B();

    // Make another reference as base class
    A* ref = obj;

    // Call overridden function
    ref->sum(15, 10);  // Will call B's version (runtime polymorphism)

    // Clean up
    delete obj;

    return 0;
}
```
---
46. **Replace Second Occurrence With "l.k" (Akkodis, )**
Input: apple
Output: al.kle


```cpp
#include <iostream>
using namespace std;

int main() {
    char str[] = "apple";
    int count = 0;

    for(int i = 0; str[i] != '\0'; i++) {

        if(str[i] == 'p') {
            count++;

            if(count == 2) {

                // Shift characters to make space
                for(int j = 5; j >= i; j--) {
                    str[j + 3] = str[j];
                }

                str[i] = 'l';
                str[i + 1] = '.';
                str[i + 2] = 'k';

                break;
            }
        }
    }

    cout << str;

    return 0;
}
```
---
47. **Write 3 functions: add, sub, multiply, each taking 2 parameters and returning an int. then task : Now call the functions using a loop (Cybage, )**

```cpp
#include <iostream>
using namespace std;

// Addition function
int add(int a, int b) {
    return a + b;
}

// Subtraction function
int sub(int a, int b) {
    return a - b;
}

// Multiplication function
int multiply(int a, int b) {
    return a * b;
}

int main() {

    int a = 20, b = 10;

    // Function pointers
    int (*func[3])(int, int) = {
        add, sub, multiply
    };

    // Call functions using loop
    for(int i = 0; i < 3; i++) {
        cout << func[i](a, b) << endl;
    }

    return 0;
}
```
---
48. **File Handling Example (write to file and read from file) (Cybage, )**

```cpp
#include <iostream>
#include <fstream>
using namespace std;

int main() {

    // Writing data into file
    ofstream fout("student.txt");

    fout << "Hello World" << endl;
    fout << "Welcome to C++ File Handling" << endl;

    fout.close();

    // Reading data from file
    ifstream fin("student.txt");

    string line;

    while(getline(fin, line)) {
        cout << line << endl;
    }

    fin.close();

    return 0;
}
```
---
49. **To access a variable (g_i) defined in first.cpp from second.cpp, you need to declare it as extern in second.cpp. This tells the compiler that the variable is defined in another translation unit (another .cpp file), and the linker will resolve it during linking. (Cybage, )**

first.cpp
Here, we define the global variable g_i.

```cpp
#include <iostream>
using namespace std;

// Global variable definition
int g_i = 100;

void display() {
    cout << "Value from first.cpp: " << g_i << endl;
}
```

second.cpp
Here, we declare the variable using extern to access the variable defined in first.cpp.

```cpp
#include <iostream>
using namespace std;

// Declaration of variable defined in another file
extern int g_i;

int main() {

    cout << "Value of g_i: " << g_i << endl;

    g_i = 200;

    cout << "Updated value: " << g_i << endl;

    return 0;
}
```
```
Value of g_i: 100
Updated value: 200
```
---
50. **Tell me what output will come (WESEE, )**

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
Output

```
Dog barks
Cat meows
```
---
51. **Tell me what Output will come (WESEE, )**
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
    ptr->sound();    // Dog barks

    ptr = &c;
    ptr->sound();    // Cat meows

    return 0;
}
```
---
52. **Write all type constructor and given variable, use following variable (Ceipal for Siemens , )**
	```
	const char* ptr;   // pointer to const char*
    double num;         // a number
    string str;         // a string
    double &num2;       // reference to a double
    ```

```cpp
#include <iostream>
#include <string>
#include <utility>
using namespace std;

class Demo {
    const char* ptr;
    double num;
    string str;
    double &num2;

public:

    // 1. Default constructor
    Demo(double &r)
        : ptr("Default"), num(0.0),
          str("Unknown"), num2(r) {
        cout << "Default Constructor" << endl;
    }

    // 2. Parameterized constructor
    Demo(const char* p, double n, string s, double &r)
        : ptr(p), num(n), str(s), num2(r) {
        cout << "Parameterized Constructor" << endl;
    }

    // 3. Copy constructor
    Demo(const Demo &obj)
        : ptr(obj.ptr), num(obj.num),
          str(obj.str), num2(obj.num2) {
        cout << "Copy Constructor" << endl;
    }

    // 4. Constructor with default arguments
    Demo(const char* p, double n, double &r,
         string s = "Hello")
        : ptr(p), num(n), str(s), num2(r) {
        cout << "Default Argument Constructor" << endl;
    }

    // 5. Move constructor
    Demo(Demo &&obj) noexcept
        : ptr(obj.ptr), num(obj.num),
          str(move(obj.str)), num2(obj.num2) {
        cout << "Move Constructor" << endl;
    }

    void display() {
        cout << "Pointer value: " << ptr << endl;
        cout << "Number: " << num << endl;
        cout << "String: " << str << endl;
        cout << "Reference value: " << num2 << endl;
    }

    ~Demo() {
        cout << "Destructor called" << endl;
    }
};

int main() {

    double x = 100.5;

    // Default constructor
    Demo d1(x);
    d1.display();

    cout << "\n";

    // Parameterized constructor
    Demo d2("Siemens", 25.5, "C++", x);
    d2.display();

    cout << "\n";

    // Copy constructor
    Demo d3(d2);
    d3.display();

    cout << "\n";

    // Default argument constructor
    Demo d4("Interview", 50.5, x);
    d4.display();

    cout << "\n";

    // Move constructor
    Demo d5(move(d2));
    d5.display();

    return 0;
}
```
---
53. **Write a program to store values, and display them in vector? (Delta IoT - John Deere, )**
```cpp
#include <iostream>
#include <vector>
using namespace std;

int main()
{
    vector<int> vec;
    int n, value;

    cout << "Enter number of elements: ";
    cin >> n;

    for (int i = 0; i < n; i++)
    {
        cout << "Enter value: ";
        cin >> value;
        vec.push_back(value);
    }

    cout << "Vector elements: ";

    for (int i = 0; i < vec.size(); i++)
    {
        cout << vec[i] << " ";
    }

    return 0;
}
```
---
54. **Write a program to take input in array and use a lambda function to filter even numbers (Delta IoT - John Deere, )**
```cpp
#include <iostream>
using namespace std;

int main()
{
    int arr[100], n;

    cout << "Enter number of elements: ";
    cin >> n;

    cout << "Enter array elements: ";

    for (int i = 0; i < n; i++)
    {
        cin >> arr[i];
    }

    auto isEven = [](int num)
    {
        return num % 2 == 0;
    };

    cout << "Even numbers: ";

    for (int i = 0; i < n; i++)
    {
        if (isEven(arr[i]))
        {
            cout << arr[i] << " ";
        }
    }

    return 0;
}
```
---
55. **Write a C++ class that contains one integer member and one pointer member. Implement default constructor, copy constructor, assignment operator and destructor.**
```cpp
#include <iostream>
using namespace std;

class Test
{
private:
    int num;
    int* ptr;

public:
    // Default constructor
    Test()
    {
        num = 0;
        ptr = new int(0);
    }

    // Copy constructor (Deep Copy)
    Test(const Test& obj)
    {
        num = obj.num;
        ptr = new int(*obj.ptr);
    }

    // Assignment operator
    Test& operator=(const Test& obj)
    {
        if (this != &obj)
        {
            num = obj.num;
            *ptr = *obj.ptr;
        }

        return *this;
    }

    // Destructor
    ~Test()
    {
        delete ptr;
    }

    void display()
    {
        cout << "Num: " << num
             << ", Ptr: " << *ptr << endl;
    }
};

int main()
{
    Test obj1;

    Test obj2(obj1);  // Copy constructor

    Test obj3;
    obj3 = obj1;      // Assignment operator

    obj1.display();
    obj2.display();
    obj3.display();

    return 0;
}
```

1. Why do we use return *this;?
- this is a pointer to the current object.
- *this represents the current object.
- Returning *this by reference allows assignment operators to return the current object.
2. When do we use void instead of return *this?
- Use void when the function does not need to return any value.
- Use ClassName& with return *this when implementing an assignment operator to support chaining.
3. What is chaining assignment in C++?
Assigning multiple objects in a single statement.
obj3 = obj2 = obj1;


Execution:
1. obj2 = obj1 executes first.
2. Assignment operator returns obj2 by reference.
3. obj3 = obj2 executes next.

---
56. **Swap Two Numbers (Infinite, )**
```cpp
#include <iostream>
using namespace std;

int main()
{
    int a = 10, b = 20, temp;

    temp = a;
    a = b;
    b = temp;

    cout << "a = " << a << endl;
    cout << "b = " << b << endl;

    return 0;
}
```
Without using a third variable (using arithmetic):
```
a = a + b;
b = a - b;
a = a - b;
```
---

57. **Sum of Digits (nCircle, )**
```cpp
#include <iostream>
using namespace std;

int main()
{
    int num = 12345;
    int sum = 0;

    while (num > 0)
    {
        sum += num % 10;
        num /= 10;
    }

    cout << "Sum of digits: " << sum;

    return 0;
}
```
---
58. **Reverse a Linked List (GeeksforGeeks Problem)(nCircle, )**
```cpp
struct Node
{
    int data;
    Node* next;
};

Node* reverseList(Node* head)
{
    Node* prev = nullptr;
    Node* current = head;
    Node* next = nullptr;

    while (current != nullptr)
    {
        next = current->next;  // Store next node
        current->next = prev;  // Reverse link
        prev = current;        // Move prev forward
        current = next;        // Move current forward
    }

    return prev;
}
```
---
59. **Operator Overloading Example(nCircle, )** already done
---
60. **const and Reference Example(nCircle, )** already done
---
61. **Pointers with const(nCircle, )**
```cpp
int x = 20;
int c = 30;
const int* a;     // pointer to const int (you cannot modify *a)
int const* b;     // same as above
int* const d = &x; // constant pointer (cannot change address stored)

a = &x;   // OK
//*a = 5; // ❌ Error: *a is const
a = &c;   // OK

*d = 25;  // OK: can change value through pointer
//d = &c; // ❌ Error: d is a const pointer
```
---

62. **Base and Derived Class Example (Tietoevry, )**
(Constructor initialization + member initialization + output explanation)
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
---
63. **Explain the flow of constructor calls in inheritance Simple Runtime Polymorphism (Tietoevry, ) Example. (Demonstrates virtual, override, and const initialization)**
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
----

64. **Explain the use of const int in a class and how to initialize it. (Tietoevry, )**

Ans.: A const int data member is used to store a value that cannot be modified after initialization.

We must initialize it using the constructor initializer list.
```cpp
#include <iostream>
using namespace std;

class Test
{
    const int value;

public:
    Test(int v) : value(v)
    {
        cout << "Constructor called\n";
    }

    void display()
    {
        cout << "Value: " << value << endl;
    }
};

int main()
{
    Test obj(100);
    obj.display();

    return 0;
}
```
---
65. **Write a simple runtime polymorphism code using base and derived classes.(Tietoevry, )**
```cpp
#include <iostream>
using namespace std;

class Base
{
public:
    virtual void display()
    {
        cout << "Base class display()" << endl;
    }
};

class Derived : public Base
{
public:
    void display() override
    {
        cout << "Derived class display()" << endl;
    }
};

int main()
{
    Base* ptr;
    Derived obj;

    ptr = &obj;

    ptr->display();

    return 0;
}
```
66. **How do you initialize a constant data member inside a class?**
```cpp
#include <iostream>
using namespace std;

class Test
{
    const int value;

public:
    Test(int v) : value(v)
    {
    }

    void display()
    {
        cout << value;
    }
};

int main()
{
    Test obj(100);
    obj.display();

    return 0;
}
```
---
67. **Print the following pattern (n = 5): (Tech Mahindra, )**
```
    *
   **
  ***
 ****
*****
```
---
68. **Replace zeros and sort the final array (Tech Mahindra)**
Array 1: 1 2 3 0 0 0 Array 2: 2 5 6
---
69. **Write shared pointer class implementation (without using any library). Also explain: what is a shared pointer? (Hughes Systique (HSC), )**
---
70. **Write a class A having the following member variables:(Hughes Systique (HSC), )**
static int x
char* y
std::string s
const int p
int& q
solution :
---
71. **Why is int A::x = 0; initialized with zero?(Hughes Systique (HSC), )
Also, write a method to display the static variable, and inside this method increment x++ and q++.
Add one more method named add(). If we perform p++ inside the add method, what will happen?
If we make the add method const, what changes?
If we remove p++, will the code compile and work?**
---
72. **Write a C++ program demonstrating all major features of OOPS. (Acitivia,  )**
73. **Smart pointers: write a program for all smart pointers.(Acitivia,  )**
74. **Write a function pointer program that takes two integers and returns one float.(Acitivia,  )**
75. **Write a C++ program to find the sum of both diagonals of a matrix. Take the matrix size dynamically as input from the user. (Prescient Technologies, )**



