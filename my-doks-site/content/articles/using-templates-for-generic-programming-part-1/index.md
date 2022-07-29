---
title: "Using Templates for Generic Programming - Part 1 (Embracing the beauty of SFINAE and enable_if)👋"
description: "C++ template programming: SFINAE, std::enable_if, std::enable_if_t, if constexpr, perfect forwarding. In this blog post, we will learn advanced template programming concepts. These concepts include how to change the implementation of classes and functions based on the type provided, how to work with different arguments and how to properly forward them, how to optimize the code in both runtime and compile-time, about SFINAE, std::enable_if, std::enable_if_t."   
lead: "In this blog post, we will learn advanced template programming concepts. These concepts include how to change the implementation of classes and functions based on the type provided, how to work with different arguments and how to properly forward them, how to optimize the code in both runtime and compile-time, about SFINAE, std::enable_if, std::enable_if_t."
date: 2021-04-27T09:20:42+01:00
lastmod: 2021-05-05T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
toc: true
autonumbering: true
type: "docs"
url: "/using-templates-for-generic-programming-sfinae-enable_if/"
keywords:
  - Templates
  - Template meta programming
  - C++
  - SFINAE
  - enable_if
tags: ['Cpp']
categories: ["Cpp"]
---

It is hard to write template programs because we assume that it works in one way but the code works in the other way. This can only be eradicated by properly understanding what template programming is and some advanced concepts in it.
 
Recipe of this blog post:

1. What is template programming? Why does it even exist?⚡️
2. Understanding SFINAE⚡️
3. Understanding and using std::enable_if⚡️

## 1. What is template programming? Why does it even exist?

We create classes and methods but without the use of template programming, we narrow down the usage of it to just one data type. For every single data type the code needs to handle, we need to create a clone of the same code, the only difference is the data type is different. This is unreasonable and it creates lots of headaches in large codebases especially for library developers. This problem gave birth to template meta-programming.

We create templates without even knowing what types are going to be passed to them and what to expect from them. This often leads to unreliable and buggy codebases. What are we going to do about it? We need to explicitly tell the compiler to disable certain pieces of code for certain types. Let me stop telling everything by words and let's jump into the code right away to explain why we need to explicitly tell the compiler to disable certain code pieces for certain types.

<br><b>example1.cpp</b>
{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>

template<typename T>
class MyClass
{
public:
    void exampleMethod(T const& x) {
      std::cout << "Hello from l-value reference method." << std::endl;
    }
    void exampleMethod(T&& x) {
      std::cout << "Hello from r-value reference method." << std::endl;
    }
};

int main() {
  int a = 5;
  MyClass<int&> obj;
  obj.exampleMethod(a);
  return 0;
}

{{< / highlight >}}

The preceding example declared a template for the class <i>MyClass</i> that has a function named <i> exampleMethod</i>, which is overloaded to accept an l-value and an r-value references. The above class won't compile when <b> T</b> is a reference. Because, r-value reference assumes that the argument does not have its own address. So, the code <i> void exampleMethod(T&& x) </i> throws an error.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
main.cpp:10:10: error: ‘void MyClass::exampleMethod(T&&) [with T = int&]’ cannot be overloaded
     void exampleMethod(T&& x) {
          ^~~~~~~~~~~~~
main.cpp:7:10: error: with ‘void MyClass::exampleMethod(const T&) [with T = int&]’
     void exampleMethod(T const& x) {
{{< / highlight >}}

If we have a way to tell the compiler to disable the <i> void exampleMethod(T&& x) </i> method then we win the game. There is a way. That's SFINAE.

## 2. Understanding SFINAE

SFINAE stands for <i>Substitution failure is not an error</i>. It helps us to disable certain pieces of code that don't accept the type that we pass. In other words, it is a way for the programmers to tell which type will activate the method defined in the class. Let's jump right in and demystify the SFINAE. I did few modifications in the above example code that incorporates the concept of SFINAE, so the code runs for any type.

<br><b>example2.cpp</b>
{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>

struct lValueRef {
  using lValueRefType = int&;
};

struct rValueRef {
  using rValueRefType = int&&;
};

class MyClass
{
public:
    template<typename T>
        void exampleMethod(typename T::lValueRefType x) {
            std::cout << "Hello from l-value reference method." << std::endl;
        }
    
    template<typename T>
        void exampleMethod(typename T::rValueRefType x) {
            std::cout << "Hello from r-value reference method." << std::endl;
        }
};

int main() {
  int a = 5;
  MyClass obj;
  obj.exampleMethod<lValueRef>(a);
  obj.exampleMethod<rValueRef>(std::move(a));
  return 0;
}
{{< / highlight >}}

The preceding example shows an innovative way to disable the methods when it doesn't fit the type it recieves. One of the <i>exampleMethod</i> method accepts l-value reference as an argument and the other accepts r-value reference as an argument.  When we pass l-value reference, the overloaded exampleMethod that accepts an r-value reference is disabled. This is done by declaring a struct and initializing a type with value <i> int&. </i> In one of the <i>exampleMethod</i> overload, it specifically looks for argument to which <i>int&</i> is passed in: (lValueRef::lValueRefType = int&). When this happens, <i>rValueRef::rValueRefType</i> is not initialized so the overloaded <i>exampleMethod</i> knew that the method copy that accepts l-value reference must be activated and all the other methods are disabled. In other words, we can tell, substitution has been failed for all the other methods. As the name of this concept defines, <b>Substitution Failure Is Not An Error.</b> The most important takeaway is the compiler was able to pick between the two versions of <i>exampleMethod</i>.

It all works fine. But, Don't you think this is an ugly way of doing template programming. Continue ready to learn better ways to do the same thing efficiently.

## 3. std::enable_if and std::enable_if_t - a compile-time switch for templates

<i> enable_if </i> was standarized in C++11 and it was part to Boost library long before that. It helps us to efficiently  force the compiler to create the substitution error and pick a different version of the template function. <i>std::enable_if</i> is defined as follows:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

template<bool B, class T = void>
struct enable_if {};

template<class T>
struct enable_if<true, T> {typedef T type;}
{{< / highlight >}}

The first enable_if is a genaralized one and the second enable_if struct is more of a specialized version of it. When the boolean value for <i> B</i> is <i>true</i> then the type that is passed to it is returned back or else void will be returned as specified in the genaralized implementation of enable_if. 

 ### 3.1 Custom-made functions as a first argument to enable_if

<br><b>example3.cpp</b>

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

#include <iostream>
#include <type_traits>

template<typename T>
constexpr auto is_int() {
  return false;
}

template<>
constexpr auto is_int<int>() {
  return true;
}

template<typename T, typename std::enable_if<is_int<T>(), T>::type = 0>

void display_output(T value) {
  std::cout << " Answer is: " << value << std::endl;
}

int main() {
  display_output(100);
  return 0;
}

{{< / highlight>}}

The preceding example shows the use of enable_if. The first parameter in the angular bracket calls the method <i>is_int</i> and returns <i>true</i> when the type passed is an integer. If not, it returns <i>false</i>. If  it returns <i>true</i> then the value of T is passed to the method <i> display_output</i> and it will be executed. Or else, it throws an error and in this case it is an substitution failure and it is not an error. If the T type is not <i>int</i>, std::enable_if turns into nothing. Assigning 0 to nothing results in compilation error.

Check out how the compiler looks at the above template code after assigning the template argument parameters.

{{<highlight CPP "h1_lines=1-6,linenostart=1">}}
#include <iostream>
#include <type_traits>

template<typename T>
constexpr auto is_int() {
  return false;
}

template<>
inline constexpr bool is_int<int>()
{
  return true;
}


template<typename T, typename std::enable_if<is_int<T>(), T>::type = 0>

void display_output(T value) {
  std::cout << " Answer is: " << value << std::endl;
}

/* First instantiated from: insights.cpp:21 */
#ifdef INSIGHTS_USE_TEMPLATE
template<>
void display_output<int, 0>(int value)
{
  std::operator<<(std::cout, " Answer is: ").operator<<(value).operator<<(std::endl);
}
#endif


int main()
{
  display_output(100);
  return 0;
}
{{< /highlight>}}


### 3.2 Standard library function as a first argument to enable_if with return type as a int pointer

Let's look into an another example:

<br><b>example4.cpp</b>

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

#include <iostream>
#include <type_traits>

template<typename T, typename std::enable_if<std::is_integral<T>::value, T>::type* = nullptr>

void display_output(T value) {
  std::cout << " Answer is: " << value <<  std::endl;
}

int main() {
  display_output<int>(100);
  return 0;
}

{{< / highlight>}}

The preceding variation is another example of <i>enable_if</i> that accepts standard library defined function to return back the boolean value. If the type we pass is an integer then <i>std::is_integral<T>::value </i> turns itself to int and in the above case it's int pointer and then we assign nullptr to it.

Check out how the compiler looks at the above template code:

{{<highlight CPP "h1_lines=1-6,linenostart=1">}}

#include <iostream>
#include <type_traits>

template<typename T, typename std::enable_if<std::is_integral<T>::value, T>::type* = nullptr>

void display_output(T value) {
  std::cout << " Answer is: " << value <<  std::endl;
}

/* First instantiated from: insights.cpp:12 */
#ifdef INSIGHTS_USE_TEMPLATE
template<>
void display_output<int, int *>(int value)
{
  std::operator<<(std::cout, " Answer is: ").operator<<(value).operator<<(std::endl);
}
#endif


int main()
{
  display_output<int>(100);
  return 0;
}

{{< /highlight >}}

### 3.3 Using enable_if as a return type of the function

<br><b>example5.cpp</b>

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

#include <iostream>
#include <type_traits>

template<typename T>

typename std::enable_if<std::is_integral<T>::value>::type display_output(T value) {
  std::cout << " Answer is: " << value <<  std::endl;
}

int main() {
  display_output<int>(100);
  return 0;
}

{{< / highlight>}}

In the above example, if <i>T</i> is not an integer then <i>void</i> is not returned by the enable_if. Thus the return type of display_output becomes <i>nothing</i> instead of <i> void </i>. And, that leads to compilation error which is nothing but a substitution failure.

### 3.4 How to eradicate ::value, ::type and typename and make the code simple to read?

All the above examples looks ugly and it's definitely not looking beautiful. Let's make it beautiful so you can embrace the benefit of the compile time switch <i> enable_if </i>.

We knew that enable_if was introduced with boost library many years back and it was first added to standard library in C++11. A variant of enable_if was introduced in C++14 and it's compliant with C++11. You just need to add the below code if you haven't upgraded your codebase to C++14 or later versions. Below is the code for the new variant:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

template< bool Condition, typename T = void >
using enable_if_t = typename std::enable_if<Condition, T>::type;

{{< / highlight>}}

The above code helps us to write simple and neat template programming. Our example SFINAE can be reduced from this:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

template<typename T, typename std::enable_if<std::is_integral<T>::value, T>::type* = nullptr>

{{< / highlight>}}

To the below code:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

template<typename T, std::enable_if_t<std::is_integral<T>::value, T>* = nullptr>

{{< / highlight>}}

std::is_integral<T>::value can further be reduced to std::is_integral_v<T> as shown below:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

template<typename T, std::enable_if_t<std::is_integral_v<T>, T>* = nullptr>

{{< / highlight>}}

<b>Further improvements: </b>Instead of appending <i> _v</i> to the prefix instead of <i> ::value </i> to the std::is_integral, it's better to have {} added to the prefix after the angular brackets as shown in the below code.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

template<typename T, std::enable_if_t<std::is_integral<T> {}, T>* = nullptr>

{{< / highlight>}}

This instantiates a value to std::is_integral<T>, which inherits from std::true_type or std::false_type, based on the type T that we pass to std::is_integral. std::true_type or std::false_type both are implicitly convertible to <i>bool</i> (i.e.) true or false respectively.


Let's again consider the <b> example1.cpp</b> and improve it with all the learning we accumulated. To remind, The below code is the example1.cpp:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>

template<typename T>
class MyClass
{
public:
    void exampleMethod(T const& x) {
      std::cout << "Hello from l-value reference method." << std::endl;
    }
    void exampleMethod(T&& x) {
      std::cout << "Hello from r-value reference method." << std::endl;
    }
};

int main() {
  int a = 5;
  MyClass<int&> obj;
  obj.exampleMethod(a);
  return 0;
}

{{< / highlight >}}

Now, we can make it beautiful with the learning we had and here is the final code shown below:

<br><b>example6.cpp</b>

{{<highlight CPP "h1_lines=1-6,linenostart=1,style=monokailight" >}}
#include <iostream>

template<typename T>
class MyClass
{
public:
    void exampleMethod(T const& x) {
      std::cout << "Hello from l-value reference method." << std::endl;
    }
    template<typename T_ = T, typename = std::enable_if_t<!std::is_reference_v<T_>>>
    void exampleMethod(T_&& x) {
      std::cout << "Hello from r-value reference method." << std::endl;
    }
};

int main() {
  int a = 5;
  MyClass<int&> obj;
  obj.exampleMethod(std::move(a)); //std::move() converts an l-value reference to r-value reference.
  return 0;
}

{{< / highlight >}}

Here is the result of the example6.cpp program:

{{<highlight CPP " h1_lines=1-6, linenostart=1, style=monokailight" >}}
Hello from r-value reference method.
{{< / highlight >}}

<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>
