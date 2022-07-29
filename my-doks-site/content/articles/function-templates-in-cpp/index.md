---
title: "Function Templates in C++ 👋"
description: "Overloaded functions have the same code but the type is different. It seems an unnecessary overhead to write the same code again and again and in a long time it adds up to the maintenance cost of the project. To write code once and use it multiple times we use function templates. A function template is itself is not a definition of a function; it is a blueprint for defining a family of functions."   
lead: "Overloaded functions have the exact same code but the type is different. It seems an unnecessary overhead to write the same code again and again and in a long time it adds up to the maintenance cost of the project. To write code once and use it multiple times we use function templates. A function template is itself is not a definition of a function; it is a blueprint for defining a family of functions."
date: 2021-04-22T09:19:42+01:00
lastmod: 2021-06-01T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
toc: true
type: "docs"
url: "/function-templates-in-c-plus-plus/"
keywords:
  - Function Templates
  - Template meta programming
  - C++
tags: ['Cpp']
categories: ["Cpp"]

---

The standard library makes heavy use of function templates and makes sure it works optimally with all data types including the custom types we define in the program. This blog post covers the basics of function templates that work with all the data types you desire.

Continue reading to learn:

1. How to define parameterized function templates that generate a family of related functions.
2. Why parameters are mostly but not always types?
3. In which cases we need to explicitly define the template arguments and why? 
4. Can we overload the function template and why?
5. Can we do return type deduction in combination with templates and is that powerful?
6. Use of decltype keyword.

## 1. Introduction

Function templates are not a definition of a function; It is a blueprint for defining a family of functions. A function template accepts a set of parameters that could be a language-defined data type or a user-defined type. The compiler is responsible for generating the functions from the function template. A function definition that is generated from the template is called the instantiation of the template or the instance of the template. Programmers can explicitly define the parameter type or the compiler will automatically deduct the parameter type from the parameter value. For example, if we pass an integer 5 as a parameter value, the compiler will define the parameter type as an integer and generate a function for it.

Let's consider the below template declaration,

{{< highlight CPP "hl_lines=1-6,linenostart=1" >}}
template <typename T> T smaller(T a, T b) {
   return a < b ? a:b;
}
{{< / highlight >}}

The function template starts with the template keyword to identify it. This is followed by a pair of square brackets that contains one or more template parameters. In the above code, we have only one parameter T. T is commonly used as a name of a parameter; names such as my_type are also acceptable. typename keyword identifies T as a type. T is called as the <b><i>template type parameter</i></b>. We can also use the keyword class but since we pass the language-defined type, typename makes more sense. 

The compiler creates an instance of the template by replacing the T with the specific type with which we call the template function. The type assigned to a type parameter T during instantiation  is called the <b><i>template type argument</i></b>.

Either the template prototype or the whole template must be implemented before calling the template function in the code.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
template <typename T> T smaller(T a, T b);
{{< / highlight >}}

## 2. Creating Instances of a Function Template

The compiler creates instances of the template from any statement that uses the smaller() function.
{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
std::cout << "Smaller of 1.4 and 3.5 << smaller(1.5, 2.5) << std::endl;
{{< / highlight >}}

You don't need to specify a value for the template. The compiler deduces the value of the T by itself. This mechanism is referred to as <b><i>template argument deduction</i></b>. The arguments to the smaller() are literals of type double and the compiler deducts it and substitutes T with double.

The resulting function definition accepts arguments of type double and returns a double value. Plugging in the double in place of T, the template instance will effectively as follows:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
double smaller(double a, double b) {
  return a < b ? a : b;
}
{{< / highlight >}}

The compiler makes sure to generate each instance only once. If a subsequent function call requires the same instance, then it calls the instance is already created.
Let's road to function template:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>
#include <string>

template<typename T> T smaller(T a, T b);

int main() {
  std::cout << "Smaller of 1.4 and 1.9 is " << smaller(1.4, 1.9) << std::endl;
  std::cout << "Smaller of 2 and 4 is  " << smaller(2,4) << std::endl;
  return 0;
}

template<typename T> 
T smaller(T a, T b) {
  return a < b ? a:b;
}
{{< / highlight >}}

This produces the following result:

Smaller of 1.4 and 1.9 is 1.4<br>
Smaller of 2 and 4 is 2


The above program generates both double and int instances of the smaller() method.

## 3. Deriving from the template type parameters

T is a template type parameter that accepts any language-defined data type or user-defined type. This type could also be used to derive types such as T&, const T&, etc. Passing by values generates many copies of the same variable so we can avoid that by passing the reference to the template function. Considering this fact, we can redefine the template function smaller() as follows:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
template<typename T> 
const T& smaller(const T& a, const T& b) {
  return a < b ? a:b;
}
{{< / highlight >}}

All the arguments and the return type are declared as const reference and that avoids multiple copies of the variable.

## 4. Explicit Template Arguments

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
std::cout << "Smaller of 1 and 2.5 << smaller(1, 2.5) << std::endl;
{{< / highlight >}}

If the above-given code is used to call the template then the compiler won't implicitly assign the template type argument. The first argument is an integer and the second one is a floating-point number.
This creates ambiguity. So, as a programmer, it is our job to explicitly tell the compiler the data type to pass into the template.


The below code has the data type <i>float</i> in the angular brackets and this is how we explicitly tell the compiler. The first argument will be converted to a floating-point number. Here, the compiler believes the programmer and does the conversion.

Now, let's consider a problematic scenario:


{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
std::cout << "Smaller of 1 and 2.5: " << smaller<int>(1, 2.5) << std::endl;
{{< / highlight >}}

In the above code, we explicitly tell the compiler to convert the second argument to an integer. The result of this conversion is 2, which may not be what you really want in this case. If you are lucky, the compiler will warn you or else it will be a disaster.

## 5. Function Template Specialization

Consider passing pointers as an argument to the template function as shown in the below program,

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>

template<typename T>
const T smaller(const T a, const T b) {
  return a < b ? a:b;
}

int main() {
    int a = 5;
    int *a_ptr = &a;
    int b = 10;
    int *b_ptr = &b;
  int*c = smaller(a_ptr,b_ptr); //Undefined behavior
  return 0;
}
{{< / highlight >}}

This is because we pass pointer references as arguments to the function template that doesn't accept the pointer references. To resolve the error, we need to define a specialization to the function template <i>smaller</i> as shown below:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

template<>
int* smaller<int*>(int* a, int* b) {
  return *a < *b ? *a:*b;
}
{{< / highlight>}}

By adding the above template specialization, we gave the power to handle the pointer template arguments. Below given the compilable version of the program by adding all the bits and pieces explained above,

{{<highlight CPP>}}

#include <iostream>

template<typename T>
 T smaller( T a,  T b) {
  return a < b ? a:b;
}

template<>
int* smaller <int*>(int* a, int* b) {
    return *a < *b ? a:b;
}

int main() {
    int a = 5;
    int *a_ptr = &a;
    int b = 10;
    int *b_ptr = &b;

    std::cout << *smaller<int*>(a_ptr,b_ptr) << std::endl; //Throws an error because there is no specialization of the template that carries smaller function.
     return 0;
}

{{< / highlight>}}


## 6. Function Templates and Overloading

You can overload a function declared in a template by defining another template function that has the same name. Note that this is not specialization of the original template but a distinct overloaded version. Let's reconsider the above <i>smaller()</i> function and convert the specialization to distinct overloaded version. 

{{<highlight CPP>}}

#include <iostream>

template <typename T>
T smaller(T a, T b) {
  return a < b ? a:b;
}

template <typename T> 
T* smaller(T *a, T *b) {
  return *a < *b ?a:b;
}

int main() {
  int a = 5;
  int* a_ptr = &a;
  int b = 10;
  int* b_ptr = &b;

  std::cout << *smaller(a_ptr, b_ptr) << std::endl;

  return 0;
}
{{< / highlight>}}

We have successfully converted the specialized function to overloaded function template. Now, it's the job of the compiler to chose which overloaded function to call based on the arguments we pass in. As of now, the above program could accept usual literal types and pointers but not arrays or any other containers. We could define some more overloaded template functions to accept containers as the arguments. I am leaving that as an exercise. 

## 7. Passing multiple parameters to Function Templates

We have been using function templates with single parameter but there are some scenarios where we need to pass multiple parameters. For example, consider the following code,

{{<highlight CPP>}}

std::cout << "Smaller number: " << smaller(2.5, 5) << std::endl;

{{< /highlight>}}

Compiler throws an error because the template definition explained upto this point can accept only one type. The above example has <i>float</i> and <i> int</i> type arguments and we haven't explicitly mentioned the type to be passed to the template. Let's now declare a template to accept multiple parameters.

{{<highlight CPP>}}
#include <iostream>

template <typename T1, typename T2>
??? smaller(T1 a, T2 b) {
  return a < b ? a:b;
}

int main() {
  std::cout << "Smaller value: " << smaller(2.5, 5) << std::endl;
}

{{< /highlight>}}

The preceding example is not a compilable version of templates that accept multiple parameters. Notice the awkward <i> ??? </i> in the place where we need to mention the return type.
If we do a mathematical operation or comparison using two different types then what would be the return type? One approach is to explicitly specifying the return type to the template as shown below:
{{<highlight CPP>}}
#include <iostream>

template<typename TReturnType, typename T1, typename T2>
TReturnType smaller(T1 a, T2 b) {
  return a < b ? a : b;
}

int main() {
  int a = 5.7878;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<float,int,float> (a, b) << std::endl;
  return 0;
}
{{< /highlight>}}

The compiler cannot deduce the return type on its own so we must always specify the return type when we call the above defined template function. Most important thing here is the sequence of parameter that we pass to the template is important here. In the above example, return type is <i>float</i>, T1 is <i>int</i> and T2 is <i>float</i>. The order must not be changed because the compiler can't figure it by itself. If you had specified the return type as the second parameter and specified only one parameter when calling the template function then the compiler will assign <i>undefined</i> to the return type which leads to an error. 

We have illustrated how multiple parameters can be defined but it looks complicated and it's not a satisfactory solution. The solution we discussed is totally dependent on the developer's thought on what the return type would be. Is there a way that the language could detect it by itself without the developer explicitly mentioning it? Yes, there is a way and we discuss it in the next section.

## 8. Return type deduction for Templates

### 8.1 auto keyword

In the previous section, we learned a way to pass the type of return value as a template parameter. But there are more expressive ways to do this. There is no easy way to deduce the return type but the compiler does that after the template instantiation.

{{<highlight CPP>}}
#include <iostream>

template<typename T1, typename T2>
auto smaller(T1 a, T2 b) {
  return a < b ? a : b;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<int,float> (a, b) << std::endl;
  return 0;
}
{{< /highlight>}}

<i>auto</i> keyword helps the compiler assign the return type by interpreting it from the return expression. 

### 8.2 decltype() and trailing return types

Function return type deduction was only introduced recently in C++14. Before this, the only way to deduct the function return type is using the <i>auto</i> keyword. Note that the compiler can only deduce the return type of the function template using the auto keyword. But the programmers who analyze the code want to depend on their intuition. 

Instead of simply using only the <i>auto</i> keyword, we can use the decltype() function that adds more expressiveness to the code. Note the below example won't compile and the reason is explained after the program.

{{<highlight CPP>}}
#include <iostream>

template<typename T1, typename T2>
decltype(a < b ? a : b) smaller(T1 a, T2 b) {   // a and are declared in the decltype() before even it is declared.
  return a < b ? a : b;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<int,float> (a, b) << std::endl;
  return 0;
}
{{< /highlight>}}

In the preceding example, the identifiers a and b were used before the declaration of it. In C++, the compiler reads the expression from left to right and that clearly tells what is happening here. C++ introduced the concept of trailing return type to avoid the above kind of compile-time error. This allows the return type specification to appear after the parameter list. 

{{<highlight CPP>}}
#include <iostream>

template<typename T1, typename T2>
auto smaller(T1 a, T2 b) -> decltype(a > b ? a : b) {   
  return a < b ? a : b;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<int,float> (a, b) << std::endl;
  return 0;
}
{{< /highlight>}}

Note that the compiler never evaluates the function declared inside the decltype() function. It just uses the expression to find the return type at the compile time. Note that there are more expressive ways to deduce the return type of the function template. 

{{<highlight CPP>}}
#include <iostream>

template<typename T1, typename T2>
decltype(auto) smaller(T1 a, T2 b) {   
  return a < b ? a : b;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<int,float> (a, b) << std::endl;
  return 0;
}
{{< /highlight>}}

The preceding example is the most beautiful and elegant way to deduce the return type of a function template. We also need to keep in mind that the return type deduction using auto is not equal to the return type deduction using the decltype(auto) or decltype(). Unlike auto, decltype() and decltype(auto) preserves the reference types and also the <i>const</i> specifiers.

## 9. Default values for Template Parameters

You can specify default values for the template parameters. For example, you can specify a default value for the return type and all the other template parameters as shown in the below example:

{{<highlight CPP>}}
#include <iostream>

template<typename TReturn=double, typename T1, typename T2>
TReturn smaller(T1 a, T2 b) {   
  return a < b ? a : b;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller(a, b) << std::endl;
  return 0;
}
{{< /highlight>}}

In the above code we have a default value to the return type template parameter. Notice that we have mentioned the template parameter with the default value as the first one. We can have the template parameter with the default value in the middle or at the last. Be careful, when mentioning all the template parameters with the default values in the front. If we haven't explicitly passed the type for rest of the values the program throws an compiler error. The proceding example explains that.


{{<highlight CPP>}}
#include <iostream>

template<typename TReturn=double, typename T1, typename T2>
TReturn smaller(T1 a, T2 b) {   
  return a < b ? a : b;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<double,double>(a, b) << std::endl; //Compilation error because we passed only two types explicitly and not the third one.
  return 0;
}
{{< /highlight>}}


In the above example, we have passed double explicitly to the TReturn and double to the T1 but nothing to the T2. This causes the compilation issue. So, It's always the best practice to have all the default template parameters at the end in the template declaration.

{{<highlight CPP>}}
#include <iostream>

template<typename T1, typename T2, typename TReturn=double>
TReturn smaller(T1 a, T2 b) {   
  return a < b ? a : b;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<double,double>(a, b) << std::endl;  // This lines compiles without an error because the third template parameter has a default value assigned to it.
  return 0;
}
{{< /highlight>}}

We can also assign template parameter that's already declared to the other template parameter as shown below,

{{<highlight CPP>}}
template<typename T1, typename T2=T1, typename TReturn=double>
....
....
 std::cout << "Smaller value: " << smaller<double>(a, b) << std::endl;
{{< /highlight>}}

## 10. Non-Type Template Parameters

We can also pass non-types as a template parameter. This includes the integral type, enumeration type, a pointer or reference to object, function or a pointer to the class member.
If we pass the non-types as a template parameter then they all will be evaluated during the compile time. If it doesn't match then a compile-time error is generated. 

{{<highlight CPP>}}
#include <iostream>

template<typename T1, typename T2, typename TReturn=double, int c, int d>
TReturn smaller(T1 a, T2 b) {   
  return a < b ? c : d;
}

int main() {
  int a = 5;
  int b = 10.7878;
  std::cout << "Smaller value: " << smaller<double,double,double,5,10>(a, b) << std::endl;  
  return 0;
}

{{< /highlight>}}

With this I like to conclude the function templates. We have learned about how to declare a function template, different ways to use it, advantages of it etc. 

<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>