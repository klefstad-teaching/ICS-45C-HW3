# ICS 45C Homework 3

## Getting the assignment

1. Accept the assignment by clicking the link in Canvas
2. Once you accept the invite, you will reach a page that says "You're ready to go"
3. Click the URL from that page that looks similar to this: `https://github.com/klefstad-teaching/ics-45c-hw3-<YourGitHubUsername>`. It may take a bit of time for the repo to be ready.
4. Click the green `<> Code` Button on the top right, and then click the middle tab `SSH`
5. Copy that link
6. Go to your hub and go into the ICS45C folder, and open up the terminal and type in the following command:
```bash
git clone git@github.com:klefstad-teaching/ics-45c-hw3-<YourGitHubUserName>.git HW3
```
7. Go to VSCode and open up the `HW3` Folder

## Directory Structure

```bash
├── CMakeLists.txt
├── CMakePresets.json
├── gtest
│   ├── gtestmain.cpp
│   ├── string_gtests.cpp
│   ├── standard_main_gtests.cpp
│   └── student_gtests.cpp
├── README.md
└── src
    ├── standard_main.cpp
    ├── string.cpp
    └── string.hpp
```

## Overview and Objectives

Homework 3 is to write a class `String` as a wrapper class that encapsulates low-level C-style strings that are highly efficient, but difficult to use. In object-oriented programming, a wrapper class is a class that encapsulates types, hiding lower level logic and exposing only higher level concepts for the user to manipulate the original data type. This wrapper class `String` behaves similarly to the Standard Template Library (STL) `std::string`.

> Be sure to complete the assigned reading and Reading Quiz first, which are designed to help you understand key concepts for this Homework.
> Note that you will **never use** `std::string` in Homework 3; instead, you are writing similar functionality.

C-style strings are implemented as arrays of type `char`, in which the logical length is defined as the characters that are stored inside the array up to the first occurrence of the null terminator (`'\0'`), which is a [sentinel](https://en.wikipedia.org/wiki/Sentinel_value) indicating the end of logical length. C-strings also have a capacity, a fixed length of the array. Interface operations include asking the length, finding a character within a string, finding a string pattern within a string, reversing a string, concatenating two strings, printing strings, and reading strings. You will write helper functions that support all those operations.

Test all the static functions of class `String` thoroughly in a program named **student_gtests**, and call and use the functions in a program called **standard_main**. The purpose of student_gtests in particular is to learn to follow a careful incremental, **test-driven development (TDD)** process.

**Relation to Course Objectives**

By encapsulating these arrays of type `char` in a C++ class, you will gain more experience, skill, and coding fluency with both classes and arrays, fundamental to C++. By using TDD, you will increase your skill in approaching a problem by carefully breaking it down into subproblems and thoroughly testing each one so that you can produce error-free code. You will also gain a deeper understanding of what is happening under the hood in `std::string`, and why it is so efficient. Finally, you will also gain skill in using `sanitizers` and `valgrind` to detect hidden errors in accessing memory.

The primary goal of Homework 3 is to gain **fluency in arrays**, which are fundamental for building data structures. It is also good to understand how C-strings work, because they are used in a lot of code. You will also learn exactly WHY we normally avoid using C-style strings—because they are error-prone! You will encounter many errors as you attempt to implement their functionality. In the end, you may appreciate what the programmers of the `std::string` have accomplished.

A Roadmap to the Arc of Objectives of Homeworks 3, 4, and 5

**Homeworks 3, 4, and 5 are critical Homeworks in which you learn the essential prerequisite skills for ICS 46 and beyond - fluency with arrays and linked lists. You must complete these Homeworks with a 60% or better.**

Homework 3 is a deeper dive into the implementation of classes using arrays while implementing class `String`. The following Homeworks 4 and 5 also implement class `String`, but with different approaches for storing the characters inside this `String`:

 - Homework 3: class `String` implemented as an array with **fixed** allocation
 - Homework 4: class `String` again, but implemented as an array with **dynamic** allocation
 - Homework 5: class `String` again, but implemented as a **singly-linked list**

As with Homeworks 1 (`stack` and `letter_count`), Homework 3 uses a fixed array of a maximum capacity, in which the array is a member of a `String` object from its birth, so we don't need to allocate the array itself.

For Homework 4, the array will be allocated later, once we know what size array we need—with **dynamic allocation** on the **heap**. This programming will be much more challenging, because heap storage requires the use of pointers, which are challenging and error prone.

> For Homework 4, you will reuse some of your static helper methods from Homework 3, so BE SURE to write and thoroughly test these for Homework 3. 

For Homework 5, the representation is a **singly-linked list** of characters, a fundamental data structure that requires dynamically allocating **nodes** and correctly managing pointers to those nodes, which is also notoriously challenging.

> Each Homework builds upon what you learned in the previous Homeworks, so it is essential to master the concepts and details in each Homework.

All of these Homeworks from now on will give you lots of new challenges in managing memory, so it is critical—and required—that you now use the `sanitizers`, which the build files on GitHub will do for you, and also [valgrind](https://valgrind.org/docs/manual/manual-core.html) (introduced in Homework 0). *From now on, every time you get anything in your program semi-sort of working, you're going to run it with sanitizers and valgrind, to detect errors that may not be detectable with simple testing. Detecting these errors will require more exhaustive testing.*

> ❗O**ne of the biggest errors that you'll make in Homework 3 is indexing an array out of bounds of `this->buf` array.**  Normal C++ does not detect these errors. The sanitizers are very good at catching these errors. `valgrind`, on the other hand, is better at catching reads from uninitialized arrays and variables. C++ doesn't initialize many variables by default, but `valgrind` will check to make sure that you don't access the value and end up with a garbage or undefined value.

## Overview of Classes and Files

> Be sure to use **exactly** the names given in these instructions for **files**, **functions**, and **classes**, because the autograder will be expecting exactly those names.

Class `String` provides an interface with non-static methods, designed to allow users of class `String` to do any needed string operations efficiently. These interface methods call static helper functions to do all the hard work. (These static helper functions would normally be private after class `String` is fully implemented and tested, but we keep them public here so that you and the autograder can test them). The static helper functions provide much of the functionality as described in the C-string [documentation](https://cplusplus.com/reference/cstring/).

## 1 string.hpp
In a file named `string.hpp`, **declare** a `String` class which is implemented as an array of type `char`. The array is named `buf` (short for buffer, which helps us remember it is an array of characters). Do not add any other data members to class `String`.

> DO NOT **define** any methods or functions in `string.hpp`. All definitions must go in `string.cpp`.

`buf` is an array with a fixed physical maximum size of `MAXLEN`, but `Strings` will have varying *logical* lengths inside the `buf` array. For example, the empty string has a null terminator in `buf[0]`. Any characters **after** the null terminator (i.e., a higher index value than the index of the null) are **not part** of the logical string. Never initialize or clear values beyond the null character, because that is a waste of time and leads to inefficiency.

`String` class contains the interface methods given in the class declaration below, including constructors, assignment, equality and relational operators, the index `operator []`, reverse, indexOf, print, and read.

 - Certain interface methods should check `MAXLEN` to see if the physical capacity has been exceeded (`String(char *)`, `operator +`, `operator +=`).  If so, print an error message, `ERROR: String Capacity Exceeded.` Then do something reasonable to recover, so that the program can continue running.
 - `operator []` must ensure the index is not out of bounds of the logical length of the array of char. If an index is out of bounds, print the error message `ERROR: Index Out Of Bounds`, then return a reasonable value, in this case `buf[0].`
 - Remember, `MAXLEN`, which is the maximum physical length of the array `buf`, is a **constant**. Always, always, **always** reference it as **`MAXLEN`** any time that value is needed. The number `1024` should **never** appear anywhere else in your program❗
 - `indexOf()` uses C++ overloading, in which functions may have the same name when they take different types as parameters. A function or method may have the same name as another, as long as the parameters are of a different type - enough that the compiler can figure out which one is being called by the context.
    - If I call `indexOf()` and pass in a `char`, it calls the method in line 29.  If I call `indexOf()` and pass in a `String` object, it will call the method on line 32.

Note that our `size()` method below returns the **logical length** of the string, not the same as the `std::size()` discussed in the reading ([LearnCpp 17.10 C-strings](https://www.learncpp.com/cpp-tutorial/c-style-strings/)).

Also note in line 4, we use a trimmed-down version of `<iostream>` named `<iosfwd>`.

> Remember: never copy/paste code from any source; you must write everything yourself.

string.hpp screenshot

![string.hpp screenshot](/assets/1-1.png)

## 2 string.cpp

In the file named `string.cpp`, define the methods given in the `string.hpp` declaration. (Use the same `#includes` and `using` as in standard_main.cpp, given below.)

To implement the methods, follow the [Steps of incremental development](#steps-of-incremental-development) given below. **Some key details may be given only in the Steps.**

Example of identifying static helper methods needed for an interface method:
As the Steps detail,  identify one or a couple tightly-coupled interface methods, and implement the static helper methods needed, one at a time, testing each one for correctness.

The `String` constructor, an interface method, must copy a C-string into `buf`. When reading about the C-string static helper functions listed in this [documentation](https://cplusplus.com/reference/cstring/), I see that copying is exactly the role of `strcpy()` and `strncpy()`, so we can write the constructor assuming these helper functions are already written and available to use.  I realize the logical length of C-strings could exceed MAXLEN, so I consider that possibility when choosing which static helper function is appropriate here.

```cpp
// note the default parameter is not given in the definition,
// only in the declaration
String::String(const char *s)
{
     strncpy(buf, s, MAXLEN-1);
}
```

Why use `strncpy()`?  Because the C-string `s` is of unknown length and may exceed our buffer capacity.  Why do we use `MAXLEN - 1` for the limit?  Because we must ensure there is one extra space for the null terminator. Now we write `strncpy()`

```cpp
char *String::strncpy(char *dest, const char *src, int n)
{
    int i=0;
    for ( ; src[i] != '\0' && i < n; ++i )
        dest[i] = src[i];
    dest[i] = '\0';
    return dest;
}
```

**Note that this definition of `strncpy` is actually different from the `strncpy` from the C standard library to make it easier to use in this homework.** For the other static helpers, you can read about what they should do exactly in their [documentation](https://cplusplus.com/reference/cstring/) (e.g., [strcmp](https://cplusplus.com/reference/cstring/strcmp/)), then write them yourself to implement your `String` class. The static methods are the workhorse of the implementation. The loops through the C-strings will happen in these static methods, NOT in the interface methods of class `String`, which will simply call these static helper methods to do the work.

**If you find yourself writing a `for` loop in an interface method, you should ask yourself, did I write a static helper function that does this already?** If so, call that static helper function with the appropriate actual parameters, instead of repeating the low-level instruction already defined and tested in the static helper function you have defined.

Static methods have no `this` parameter; all parameters are explicitly declared in the parameter list. Static methods are directly visible to other methods in class `String`. These static `str` functions should not check `MAXLEN`; however, `MAXLEN` may be passed in to some functions such as `strncpy()`.

> **You will reuse these static helper methods in HW4, so write them well❗**

Add const where appropriate

Some of the methods in the `String` class modify the `String` instance (i.e., `this`), whereas others do not. For example, the comparisons do not change the string. Methods which do not modify their instance should normally be marked `const `in C++. There are several other methods which do not modify their instance but which are missing `const`. **Add const to both the declaration and definition of every method which does not modify its instance. This usage of const is tested in the autograder.**

You can do this before you even write the code, but you can also add `const` after writing an implementation.

## 2 student_gtests.cpp:  Test the static helper methods in class String

In a file called `student_gtests.cpp`, write GTests for the static helper methods in the `String` class. The file contains one test for each method, with the tests for `strlen` and `strcpy` being examples for you. Use `EXPECT_EQ(a, b)` to check that `a == b` and use `EXPECT_STREQ(s, t)` to check that the C-strings s and t are logically the same (internally, this comparison also uses a `strcmp` function just like yours!).

Write these tests at the same time as you are developing the static helpers using TDD - method by method (see [Steps of incremental development](#steps-of-incremental-development)). This process will help you find bugs early and will help you to understand what these methods *should* do. **Make sure that you test some common edge-cases like empty strings and not just the "happy path". The edge cases are usually where the bugs are!**

**We will also grade these tests in the autograder.** Your tests should pass for a correct implementation of these helper functions but ***should fail*** on buggy implementations that we will run them on. Each test should test only the function it is named after. Do not add entirely new tests to this file, as the names are important - instead use `string_gtests.cpp`.

An important point is that we require not only that your tests pass for **your** own code, they must also pass for **our** correct implementation. So you should not make extra assumptions in your tests or test behavior that we didn't specify. For example, `strcmp` is not required to return -1,0, or 1, and none of the string helper functions are required to work on `nullptr` inputs.

> ⚠️ **Warning**: Because GTest internally includes the standard library header `<string.h>`, there are versions of `strcmp`, `strcat`, etc. in the global namespace. Make sure that you always qualify your static member functions. For example, you have to call `String::strcmp`, not `strcmp`, or you are not testing your own code!

## 3 standard_main: Use class String

After your class `String` is working, use your class `String` in a standard `main()` written in a file named `standard_main.cpp`. `standard_main` calls and uses your class `String` functions in the typical ways they would be used.

 > Your class `String` functions will also be thoroughly tested with test cases by the autograder.

standard_main.cpp screenshot

![standard_main.cpp screenshot](/assets/3-1.png)

## Steps of incremental development

![Steps of incremental development](/assets/3-2.png)

Small steps of incremental development and testing help to isolate and limit the number of possible issues that arise. Taking too large a leap (with Big Bang development) leads to an overwhelming maze of issues that may be extremely difficult to diagnose and debug.

Think carefully about how you are going to get from the empty class definition given above, to a class declaration that is fully functional. A short-sighted approach is to code it all up, then start debugging. You won’t have a working program until it is nearly complete. And when it does not work correctly, it will be impossible to find the cause of the error!

Instead, use test driven development (TDD) to identify the minimum functionality you need to test, then write a test case for that, then implement the functionality until the test performs correctly.  Then you identify the next feature to add, write the test, implement the feature, test, etc.

**That is, decide the method(s) to write first, then write the GTests for it, then define the method(s) in string.cpp, then compile and run. Get the method(s) working before moving on to the next!**

Each test function will focus on testing one static helper method, or a couple tightly related/dependent methods. The first two tests, `strlen()` and `strcpy()`, are written for you as examples. There are about 8 more for you to write. At any point, you can temporarily comment out any other tests in the given file that you want to ignore for now, so that you can focus on one or two specific tests.

Linker error messages can help you identify which functions to write next. When you compile and link, the linker will give you error messages if you call a method without defining it. Whenever you get such a message, note which function is undefined, then go define it properly. Never give empty body definitions for critical methods, such as constructors, or operator =, since that will lead to serious run-time errors.

## TDD Starting Steps for Homework 3

**Step 1.** Write the declaration of class `String` in a file named `string.hpp`, by typing the code provided in the `string.hpp` screenshot. In `string.cpp`, `#include string.hpp`.

**Step 2.** Decide which interface method(s) to write first, then identify and write the GTests for them. The static helpers should be tested in `student_gtests.cpp` whereas other tests should go into `string_gtests.cpp`. To start, to write the simplest possible test for class `String`, you need **functioning constructors**, **the `size()` method**, and a **functioning destructor**.

```cpp
TEST(StringClass, Constructors) {
    String s("hello");
    EXPECT_EQ(s.size(), 5);

    String t(s);
    EXPECT_EQ(t.size(), 5);
    EXPECT_EQ(s.size(), 5);
}
```

Why? Each constructor must do the work of construction from the very start, or you will get serious run-time errors. Constructors and the destructor may also be called automatically, so even if you don’t explicitly call them, they are still being called. You ***must*** write the copy constructor, because it will be called automatically whenever you pass a parameter by copy/value, or return a `String` by copy/value from a function. Notice that in the declaration in `string.hpp`, the inserter `String` parameter is passed by value (i.e., a copy), which will call the copy constructor. To write the constructors, you need the helper function, `strncpy()`. Both constructors should call `strncpy()` to copy the characters from the parameter into `this->buf`.

At the same step, write the **print** method and the inserter operator **<<**, so that you can print out the objects you construct to see if they are being constructed correctly.

Because every object that is born must eventually die, you must define the destructor from the start, but for now, have the  `String` destructor `String::~String()` only print the message `"String <buf> is destructing"` . You will see that objects are being destructed just before they are deallocated. At some point, you will get tired of seeing the destructor message, so you can comment it out. When first learning to write C++ classes, it is a good strategy to have the destructor do nothing until the basic functionality of your class is implemented, tested, and working correctly. For HW3, the `String` destructor does not need to do anything because our array `buf` is part of our `String` object, but for HW4 and HW5, the destructor will have important work to do to clean up the heap memory allocated for each `String` object.

An early test case should declare a `String` and initialize it via the constructor, then print out the `String` to see if it was constructed correctly. It also calls the copy constructor to pass the `String` object to `operator <<`.

**Step 3.** Define each method required by your new test cases. The profile given in the `.cpp` file must match the declaration given in the `.hpp` file; however, the name must be preceded with the class name followed by the scope qualifier, e.g., `String::`, to indicate the function belongs to your class `String`.

There are a few important things to know about declaration vs definition syntax. The following example shows 3 different situations encountered in Homework 3:

Example: to define the constructor and the static method `strlen()` and method `size()` declared in `class String` in the file `string.hpp` like this:

```cpp
explicit String(const char *s = "");
static int strlen(const char *s);
int size();
```

Write them in string.cpp like this

```cpp
// note explicit and default for parameter s below are gone
String::String(const char *s) {
    strncpy(buf, s, MAXLEN-1);
}

// notice static is gone
int String::strlen(const char *s) {
    int len=0;
    while (s[len] != '\0')
        ++len;
    return len;
}

int String::size() {
    return strlen(buf);
}
```

**Step 4.** Next, figure out which methods you need in order to run your next test case correctly, by compiling and running the tests that you have so far, to see which `undefined function/method` messages are printed when their functions are called by this one test case. To do this, compile student_gtests and `string_gtests` following the build instructions [here](/#build).

**Step 5.** Now, implement the methods identified from the `undefined function/method` messages printed in Step 5.

**Step 6.** Now, test and debug what you have written thus far, until it is functioning correctly.

 > At this point, develop a habit of **always** running under `sanitizers` and/or `valgrind` as you test and debug. Often one catches bugs that the other does not - so using both is even more recommended!

 > Learn more about `sanitizers` and `valgrind` [here](https://developers.redhat.com/blog/2021/05/05/memory-error-checking-in-c-and-c-comparing-sanitizers-and-valgrind).

**Step 7. Repeat the Steps 1-6 of this process for each method of class String—one at a time.**

## Tips for approaching the rest of the methods:

Next, you can think about writing `operator =` or `operator ==`, but whatever you decide to write next, first add the test case to `string_gtests.cpp`, then implement the new function and test it. The relationals and equality operators use `strcmp()` to do the hard work.

Finally, do not call `strlen()` unless absolutely necessary.  It is expensive, ***O(N)***, and is not the way to iterate through a C-string with `for` loops.  In Lecture, I show you how to iterate through C-strings using the test for the null terminating character.  That way is the correct and efficient way to do it.  Note that it is difficult to write `reverse_cpy()` without first calling `strlen()`, but call it once and save the value in a local int variable for later use in the function.

Here is an example of calling `strlen()` in the terminating condition of a loop. For example,

![solution 1](/assets/3-3.png)

Here, if n is the number of characters in the `String s`, this `for` loop calling `strlen()` in each iteration has just turned this function f into an ***O(n^2)*** function! You can easily convert it back to ***O(n)*** by **factoring out the call to `strlen()`** as follows:

![solution 2](/assets/3-4.png)

But this is still making **two passes** down `String s`. An even better solution is not to call `strlen()` at all, and use the `null` string terminator to end the loop:

![solution 3](/assets/3-5.png)

Below is a start on an algorithm you can use for `strcmp()`

![solution 4](/assets/3-6.png)

How to Test, Compile, and Run the programs
Follow the Steps given in the [Steps of incremental development](#steps-of-incremental-development) section, adding test cases to the tests given in the GitHub hw3 repo, building with CMake repeatedly, and running with sanitizers and/or valgrind until you are convinced that everything works correctly.

## Build Instructions

```bash
# Produce the `build` folder with the presets provided for the homework:
cmake --preset default

# Build all targets at once:
cmake --build build

# Build only standard_main.cpp:
cmake --build build --target standard_main

# Build only string_main gtests:
cmake --build build --target standard_main_gtests

# Build string and student gtests:
cmake --build build --target string_gtests

# Build student gtests:
cmake --build build --target student_gtests
```

To run the above targets after compiling them:

```bash
./build/standard_main        # Runs the 'main' function from src/standard_main.cpp
./build/standard_main_gtests # Runs the 'standard_main' gtest set of tests
./build/string_gtests        # Runs the 'string' and 'student' gtest sets
./build/student_gtests       # Runs the 'student' gtests
```

Once you have run the code above and it either produces the output you expected or passes
all provided tests, congratulations! You are now ready to [submit](#submission) your homework!

## Submission

As with previous submissions, submit via `GitHub` by the following steps:

1. `git add .`
2. `git commit -a -m "Commit Message Here"`
3. `git push --set-upstream origin main`

to push your changes to your private GitHub repository, and then submit `hw3` to `Gradescope`.

## Credit

All code moved from [ICS45c](https://github.com/RayKlefstad/ICS45c/tree/hw3)
