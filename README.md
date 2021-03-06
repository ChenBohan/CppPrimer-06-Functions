# CppPrimer-06-Functions
Solutions for Chapter 6. Functions exercises of C++ Primer 5th.

### Exercise 6.1 What is the difference between a parameter and an argument?

- **Parameters**: Local variable declared inside the function parameter list. they are initialized by the **arguments** provided in the each function call.

- **Arguments**: Values supplied in a function call that are used to initialize the function's **parameters**.

### Exercise 6.5 Write a function to return the absolute value of its argument.

```cpp
template <typename T>
T abs(T i)
{
    return i >= 0 ? i : -i;
}
```

### Exercise 6.6 Explain the differences between a parameter, a local variable, and a local static variable. 

- **local variable**: Variables defined inside a block;

- **parameter**: Local variables declared inside the function parameter list

- **local static variable**: local static variable（object）is initialized before the first time execution passes through the object’s definition. Local statics are not destroyed when a function ends, they are destroyed when the program terminates.

### Exercise 6.7 Write a function that returns 0 when it is first called and then generates numbers in sequence each time it is called again.

```cpp
size_t generate()
{
    static size_t n = 0;
    return n++;
}
```

### Exercise 6.10 Using pointers, write a function to swap the values of two int s. Test the function by calling it and printing the swapped values.
```cpp
void swap(int *a, int *b)
{
    auto tmp = *a;
    *a = *b;
    *b = tmp;
}
```

### Exercise 6.23 Write your own versions of each of the print functions presented in this section. Call each of these functions to print i and j defined as follows: int i = 0, j[2] = {0, 1};

```cpp
// Using a Marker to Specify the Extent of an Array
//   Functions that deal with C-style strings stop
//   processing the array when they see a null character
void print(const char* p) {
    if (p) {
        while (*p) {
            cout << *p++ << " ";
        }
        cout << endl;
    }
}

// Using the Standard Library Conventions
//   to pass pointers to the first and one past the last element in the array.
//   This approach is inspired by techniques used in the standard library.
void print(const int* begin, const int* end) {
    while (begin != end) {
        cout << *begin++ << " ";
    }
    cout << endl;
}

// Explicitly Passing a Size Parameter
//    to define a second parameter that indicates the size of the arrays
void print(const int a[], size_t size) {
    for (size_t i = 0; i != size; ++i){
        cout << a[i] << " ";
    }
    cout << endl;
}

// When a function does not need write access to the array elements, the
// array parameter should be a pointer to const

// Array Reference Parameters
//   define a parameter that is a reference to an array
void print(const int (&arr)[2]) {
    for (auto i : arr) {
        cout << i << " ";
    }
    cout << endl;
}
```

### Exercise 6.24

```
Arrays have two special properties that affect how we define and use functions that operate on arrays: We cannot copy an array, and when we use an array it is (usually) converted to a pointer.
```

So we cannot pass an array by value, and when we pass an array to a function, we are actually passing a pointer to the array's first element.

In this question, const int ia[10] is actually same as const int*, and the size of the array is irrelevant. we can pass const int ia[3] or const int ia[255], there are no differences. If we want to pass an array which size is ten, we should use reference like that:

```cpp
void print10(const int (&ia)[10]) { /*...*/ }
```
see more discusses at http://stackoverflow.com/questions/26530659/confused-about-array-parameters

### Exercise 6.29 When you use an `initializer_list` in a range `for` would you ever use a reference as the loop control variable? I f so, why? If not, why not ?

```
- Depends on the type of elements of initializer_list. 
    - When the type is PODType, reference is unnecessary. Because POD is cheap to copy(such as int).
    - Otherwise, Using reference(const) is the better choice.
 ```

### Exercise 6.31 When is it valid to return a reference? A reference to const?

when you can find the preexisted object that the reference refered.

### Exercise 6.36: Write the declaration for a function that returns a reference to an array of ten string s, without using either a trailing return, decltype, or a type alias.

```cpp
// Declaring a Function That Returns a Pointer to an Array
string (&func(string (&arrStr)[10]))[10]
```

### Exercise 6.37: Write three additional declarations for the function in the previous exercise. One should use a type alias, one should use a trailing return, and the third should use decltype.

```cpp
using ArrT = string[10];
ArrT& func1(ArrT& arr);

// Using a Trailing Return Type
auto func2(ArrT& arr) -> string(&)[10];

// Using decltype
string arrS[10];
decltype(arrS)& func3(ArrT& arr);
```

> The only tricky part is that we must remember that decltype does not automatically convert an array to its corresponding pointer type. The type returned by decltype is an array type, to which we must add a * to indicate that arrPtr returns a pointer.


### Exercise 6.44: Rewrite the isShorter function from § 6.2.2 (p. 211) to be inline.

```cpp
inline bool isShorter(const string &s1, const string &s2) {
    return s1.size() < s2.size();
}
```

### Exercise 6.46: Would it be possible to define isShorter as a constexpr? If so, do so. I f not, explain why not.

No, std::string(parameter of isShorter) is not a literal type.

> A constexpr function is defined like any other function but must meet certain restrictions: The return type and the type of each parameter in a must be a literal type

### Exercise 6.49: What is a candidate function? What is a viable function?

- candidate function: Set of functions that are considered when resolving a function call. (all the functions with the name used in the call for which a declaration is in scope at the time of the call.)

- viable function: Subset of the candidate functions that could match a given call. It have the same number of parameters as arguments to the call, and each argument type can be converted to the corresponding parameter type.

### Exercise 6.54: Write a declaration for a function that takes two int parameters and returns an int, and declare a vector whose elements have this function pointer type.

```cpp
int func(int a, int b);

// when we apply decltype to a function, it returns a function type, not a pointer to function type.
// We must add a * to indicate that we are returning a pointer, not a function.
using pFunc1 = decltype(func) *;
std::vector<pFunc1> vec1;

typedef decltype(func) *pFunc2;
typedef int(*pFunc2)(int a, int b);     // equivalent type
std::vector<pFunc2> vec2;

using pFunc3 = int (*)(int a, int b);   // pFunc3 is a pointer type
std::vector<pFunc3> vec3;

using pFunc4 = int(int a, int b);       // pFunc4 is a function type, not a pointer
std::vector<pFunc4*> vec4

typedef int(*pFunc5)(int a, int b);
std::vector<pFunc5> vec5;

using pFunc6 = decltype(func);
std::vector<pFunc6*> vec6;
```

### Exercise 6.55: Write four functions that add, subtract, multiply, and divide two int values. Store pointers to these values in your vector from the previous exercise. Exercise 6.56: Call each element in the vector and print their result.

```cpp
int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }
int multiply(int a, int b) { return a * b; }
int divide(int a, int b) { return b != 0 ? a / b : 0; }

std::vector<decltype(func) *> vec{add, subtract, multiply, divide};
for (auto f : vec)
    std::cout << f(2, 2) << std::endl;
```
