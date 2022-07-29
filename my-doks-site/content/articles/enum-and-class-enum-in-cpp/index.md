---
title: "Enumerations and Class Enumerations in C++ 👋"
description: "Enumerations are always fun to use in C++ programs. C++ core guidelines define eight different rules to define the enumerations. And also, C++11 introduced the scoped enumerations also called as class enumerations. Scoped enums also have few advantages over plain enums which are also covered in this article."

lead: "Enumerations are always fun to use in C++ programs. C++ core guidelines define eight different rules to define the enumerations. And also, C++11 introduced the scoped enumerations also called as class enumerations. Scoped enums also have few advantages over plain enums which are also covered in this article."
date: 2021-07-05T09:19:42+01:00
lastmod: 2021-07-05T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
toc: true
type: "docs"
url: "/enumerators-and-class-enumerators-in-cpp/"
keywords:
  - enumerators
  - class enumerators
  - cpp
  - scoped enumerators
tags: ['Cpp', 'enumerators', 'class enumerators']
categories: ["Cpp"]

---

## Plain Enum

An enumeration is a user-defined data type that consists of integral constants. To define an enumeration, the keyword enum is used. The below example is a plain enum.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
enum courses {
  cpp,
  algorithms,
  machine-learning,
  english,
  programming,
  math,
  science
};
{{< / highlight >}}

In the above example, every attribute declared inside the plain enum are values of type enum. If we haven't assigned the integer value to all the values declared inside the enum then the compiler will assign one starting from zero.

By default, cpp is 0,algorithms is 1 etc. We can also change the default values by assigning one as shown below:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
enum courses {
  cpp = 4,
  algorithms = 7,
  machine-learning = 6,
  english = 0,
  programming = 2,
  math = 3,
  science = 9
};
{{< / highlight >}}

The above enum can be called by using the below statement,

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
enum courses selected_course = cpp;
{{< / highlight >}}

enum is used mostly for declaring flags. Using enum is less expensive because it just needs int (4 bytes) to store the values by default.

We can also declare the return type of the enum as shown in the below example,

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
enum courses : int32_t {
  cpp = 4,
  algorithms = 7,
  machine-learning = 6,
  english = 0,
  programming = 2,
  math = 3,
  science = 9
};
{{< / highlight >}}

By default, enum returns integer values. In the above example, we declare that the enum courses returns 32-bit integer.

### Declare multiple values with the same name inside the enum

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
enum courses : int32_t {
  cpp = 4,
  cpp = 33,
  algorithms = 7,
  machine-learning = 6,
  english = 0,
  programming = 2,
  math = 3,
  science = 9
};
{{< / highlight >}}

In the above example, we have declared <i>cpp</i> twice. This results in the compile time error as shown below,

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
main.cpp:6:5: error: redefinition of 'cpp'
{{< / highlight >}}

## Why enum classes?

enum classes were introduced in C++11 and it addresses three benefits compared to the plain enum. Class enum is also called as the scoped enum or new enum or strong enum.

From <a href="https://www.stroustrup.com/C++11FAQ.html#enum">Bjarne Stroustrup's C++11 FAQ:</a>


1. conventional enums implicitly convert to int, causing errors when someone does not want an enumeration to act as an integer.
2. conventional enums export their enumerators to the surrounding scope, causing name clashes.
3. the underlying type of an enum cannot be specified, causing confusion, compatibility problems, and makes forward declaration possible.

The new enums are called the class enums because they combine aspects of traditional enumerations (named values) with aspects of classes (scoped members and absence of conversions).

### conventional enums implicitly convert to int, causing errors when someone does not want an enumeration to act as an integer.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
enum courses
{
    cpp,
    python,
    golang
};

enum class courses_programming
{
    cpp_1,
    python_1,
    golang_1
};

int main()
{
    //! Implicit conversion is possible
    int i = cpp;

    //! Need enum class name followed by access specifier. Ex: courses_programming::cpp_1
    int j = cpp_1; // error C2065: 'cpp_1': undeclared identifier

    //! Implicit converison is not possible. Solution Ex: int k = (int)courses_programming::cpp_1;
    int k = courses_programming::cpp_1; // error C2440: 'initializing': cannot convert from 'courses_programming' to 'int'

    return 0;
}
{{< / highlight >}}


### conventional enums export their enumerators to the surrounding scope, causing name clashes.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

// Header.h

enum vehicle
{
    Car,
    Bus,
    Bike,
    Autorickshow
};

enum FourWheeler
{
    Car,        // error C2365: 'Car': redefinition; previous definition was 'enumerator'
    SmallBus
};

enum class Editor
{
    vim,
    eclipes,
    VisualStudio
};

enum class CppEditor
{
    eclipes,       // No error of redefinitions
    VisualStudio,  // No error of redefinitions
    QtCreator
};

{{< / highlight >}}

### The underlying type of an enum cannot be specified, causing confusion, compatibility problems, and makes forward declaration impossible.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

// Header1.h
#include <iostream>

using namespace std;

enum class Port : unsigned char; // Forward declare

class MyClass
{
public:
    void PrintPort(enum class Port p);
};

void MyClass::PrintPort(enum class Port p)
{
    cout << (int)p << endl;
}

{{< / highlight >}}


{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

// Header.h
enum class Port : unsigned char // Declare enum type explicitly
{
    PORT_1 = 0x01,
    PORT_2 = 0x02,
    PORT_3 = 0x04
};

{{< / highlight >}}


{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

// Source.cpp
#include "Header1.h"
#include "Header.h"

using namespace std;
int main()
{
    MyClass m;
    m.PrintPort(Port::PORT_1);

    return 0;
}
{{< / highlight >}}

## Interesting way to use enum classes with #define, #undef, #include

In the below program, I have mentioned an interesting way to declare all the enum values in a separate file and then include that file in the enum class as shown below:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

#include <iostream>
enum class ObjectType : int {
    #define DB_OBJECT_TYPE_ENUM
    #define a(id) id
    #define id id 
    #include "main1.h"
    #undef DB_OBJECT_TYPE_ENUM
};

/** Returns the string representation of the input object type. */
void object_type_str(ObjectType object_type) {
  switch (object_type) {
    case ObjectType::INVALID:
      std::cout << "Invalid" << std::endl;
      return;
    case ObjectType::GROUP:
      std::cout << "Group"  << ": " << static_cast<int>( ObjectType::GROUP ) << std::endl;
      return;
    case ObjectType::ARRAY:
      std::cout << "Array" << std::endl;
      return;
    case ObjectType::ASDFGF:
      std::cout << "Asdfgf" << std::endl;
      return;
    default:
      std::cout << "default:" << " " << static_cast<int>(ObjectType::QQQ) << std::endl;
      return;
  }
}

int main() {
    object_type_str(ObjectType::QQQ);
    object_type_str(ObjectType::GROUP);

    return 0;
}

{{< / highlight >}}

Code in the main1.h file is shown below:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

#ifdef DB_OBJECT_TYPE_ENUM
    /** Invalid object */
    a(INVALID) = 0,
    /** Group object */
    a(GROUP) = 7,
    /** Array object */
    a(ARRAY) = 2,
    a(QWERTY)  = 9,
    a(ASDFGF) = 3,
    QQQ,
// We remove 3 (KEY_VALUE), so we should probably reserve it
#endif
{{< / highlight >}}

In the above code, we use #ifdef directive to define the values to be added in the enum class. If the DB_OBJECT_TYPE_ENUM is defined in the enum then all the values declared inside the #ifdef are included. In the above enum, we declared an array kind of enum values. It's always needed to declare a #define macro to define the array structure so the enum class can understand that the nature of the values declared in the enum class.

## C++ guidelines to declare enum and enum class

<a href="http://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines#Renum-value"> C++ core guidelines </a> define the below guidelines to declare the enum and enum classes. 

1. Prefer enumerations over macros.
2. Use enumerations to represent sets of related named constants.
3. Prefer enum classes over plain enums.
4. Define operations on enumerations for safe and simple use.
5. Don't use ALL_CAPS for enumerators.
6. Avoid unnamed enumerations.
7. Specify the underlying type of enumerations only when necessary.
8. Specify enumerator values only when necessary.

## Conclusion

It's always better to use plain enum instead of macros. But, class enums are always better compared to plain enums and macros.

<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>
