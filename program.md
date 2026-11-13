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


25. Check Palindrome String (Iterative + Recursive) (iQuest, Apexon, Tech Mahindra)


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
26. Count Binary Substrings Starting and Ending with 1 (iQuest, )
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

 Find the Most Frequently Occurring Character in a Word (Expleo, )

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

Frequency of Each Character in a word (Expleo, )

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
char *str; vs char str[]; in C++ (IBM, )

29. Program for Insertion (<<) Operator Overloading (IBM, )
<< = Insertion Operator: Used with cout to output data to the console.
>> = Extraction Operator: Used with cin to input data from the user.

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

30. What will come output of following Program? (Cyient, )
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
output
A constructor
B constructor
A Destructor
31 What make you changes that B Destructor will call ? in 30 number q (Cyient, )
Answer: Make the base class destructor virtual.

Modified Code

cpp
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
Correct Output

A constructor
B constructor
B Destructor
A Destructor


32  Fix the bug (Cyient, )
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
ans:
The bug is caused by object slicing in the function display(Base b).

When a Derived object is passed by value, only the Base part is copied. Therefore, the overridden Derived::show() is not called.

Fixed code

cpp
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
Output

derived


33.Fix the error (Cyient, )
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
solution
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

Alternative solution using atomic

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

34. Reverse a string (Cyient, )
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
solution
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

 reverse the order of words in a sentence or 35  WAP in C++ to Reverse a Line Word by Word (Zwayam, )

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

or

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

36 Program: Insert an Element into a Sorted Array (Siemens, )
Problem: Given a sorted array, insert an element and maintain the sorted order.
Input:
int arr[] = {1, 3, 5, 7};
int n = 6;
Output:
1, 3, 5, 6, 7 



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

36. Pattern: (Intelizign, )
1
1 2
1 2 3


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

37. Pointer behavior (Intelizign, )
{ 
int* p1;
int m = 100;
p1 = &m;
*p1++;  // increment pointer, not value. Equivalent to p1 = p1 + 1
++m;    // m = 101
printf("%d\n", *p1); // Undefined behavior
printf("%d\n", m);   // 101
}

But
{
int m = 100;
int* p1;
p1 = &m;
(*p1)++;  // increment pointer, not value. Equivalent to p1 = p1 + 1
++m;    // m = 101
printf("%d\n", *p1); //102
printf("%d\n", m);   // 102
}

 38. Call dosomething function in main() (Intelizign, )
    Case 1:
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
    
    case 2:
    void dosomething(int &p) {
        cout << "Inside function: " << p << endl;  // no dereferencing needed
    }
    
    int main() {
        int P = 10;
        dosomething(P);  // pass variable by reference
    }
 
39 Create a class with const, reference, char pointer, static data member (Tietoevry, )

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


40 . Create all types of constructors + destructor. Why use them? (Tietoevry, )

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
41. Write output (Tietoevry, )
int arr[] = {10, 20, 30, 40, 50};
int *p = arr;

cout << *p;        // Output: 10
cout << *p++;      // Output: 10 (p now points to arr[1])
cout << (*p)++;    // Output: 20 (then arr[1] becomes 21)


42. Write output & explain (Tietoevry, )
int x = 10, y = 20;
const int* p1 = &x; // Pointer to const int (data is const, pointer not)
int* const p2 = &x; // Constant pointer to int (pointer const, data not)

*p1 = 20;    // Error: can't modify value through pointer to const
p1 = &y;     // OK: pointer itself can point elsewhere

*p2 = 30;    // OK: data can be modified
p2 = &y;     // Error: can't change address held by const pointer

43. Insert pairs in map and print using iterator (Tietoevry, )

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
44. Eliminate duplicate characters from "Hello World!!!" without using STL (Calsoft Inc)



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
45 First Non Repeating Character in a String (Apexon, )
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

46 First Repeating Character in a String (Apexon, )
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

46 Write a C++ program to store integers 1–10 in a vector and remove element 5 from vector then print vector? (FlexTrade, )

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

47 Fix and Explain This Code (Akkodis, )
 Original Code (Problematic):
B obj;
obj & =A obj;  //  Invalid syntax
obj->sum(15,10);  //  obj is not a pointer

Correct version
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

48 Replace Second Occurrence With "l.k" (Akkodis, )
Input: apple
Output: al.kle



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


49 Write 3 functions: add, sub, multiply, each taking 2 parameters and returning an int. then task : Now call the functions using a loop (Cybage, )


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
50 File Handling Example (write to file and read from file) (Cybage, )



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
51.  To access a variable (g_i) defined in first.cpp from second.cpp, you need to declare it as extern in second.cpp. This tells the compiler that the variable is defined in another translation unit (another .cpp file), and the linker will resolve it during linking. (Cybage, )

sol:

first.cpp
Here, we define the global variable g_i.


cpp

#include <iostream>
using namespace std;

// Global variable definition
int g_i = 100;

void display() {
    cout << "Value from first.cpp: " << g_i << endl;
}


 second.cpp
Here, we declare the variable using extern to access the variable defined in first.cpp.


cpp

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

Value of g_i: 100
Updated value: 200


52. Tell me what output will come (WESEE, )

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

 Output


Dog barks
Cat meows


