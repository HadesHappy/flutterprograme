---
title: "A Taxonomy of Expression Value Categories in C++👋"
description: "C++ had value categories in C++0X version. The two value categories that were before C++11 are lvalues and rvalues. One of the greatest addition to C++11 was the introduction of movable types. Learning about the value categories is one of the pre-requisites to learn about move semantics. Any expression before the C++11 standard was either lvalue or rvalue. lvalue has an identity and its lifetime depends on the scope of the variable. On the other hand, rvalues don't have an identity and it does not live beyond the life of the expression." 
lead: "C++ had value categories in C++0X version. The two value categories that were before C++11 are lvalues and rvalues. One of the greatest addition to C++11 was the introduction of movable types. Learning about the value categories is one of the pre-requisites to learn about move semantics. Any expression before the C++11 standard was either lvalue or rvalue. lvalue has an identity and its lifetime depends on the scope of the variable. On the other hand, rvalues don't have an identity and it does not live beyond the life of the expression."
date: 2021-05-14T09:19:42+01:00
lastmod: 2021-05-18T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
toc: true
type: "docs"
url: "/taxonomy-of-expression-value-categories-in-cpp/"
keywords:
  - value categories
  - l-value
  - r-value
tags: ['Cpp', 'value categories']
categories: ["Cpp"]

---

## 1. Introduction

Let's consider few examples to understand l-value and the r-values in depth:

{{<highlight CPP >}}
int f();
int& k();

int a;
a = 4;   \\ a is an l-value and 4 is an r-value. 
4 = a;   \\ This expression throws an error because 4 is an r-value which is temporary and won't accept any thing.
a = f(); \\ f() returns an int and that's stored in the l-value a.
f() = a; \\ can't assign or store anything in the temporary int returned by the f().
k() = a; \\ the reference returned by the k() stores the value of a.
{{< / highlight >}}

As already said, before C++11, expressions are categorized as lvalues or rvalues. They are categorized based on the identity of the expression. But, it all changed after the C++11 standard version. Expressions are now categorized based on identity and movability. Below is the short definition for all the value categories. 

<b>x-values</b>  -> "It's abbreviated as 'Expiring values'. Refers to objects that nears the end of it's lifetime. Example: Result of calling a function that returns r-value reference is an x-value. It has an identity and it's movable."<br>
<b>l-values</b>  -> "It has it's own identity name. It lives till the end of the block either global or local variable."<br>
<b>gl-values</b> -> "Abbreviated as 'Genaralized l-value'. Its an l-value or an x-value."<br>
<b>r-values</b>  -> "An r-value appears on the right hand side of the expression and it's an x-value or a temporary object or a sub-object or an value that is not associated with an object." <br>
<b>pr-values</b> -> "It's abbreviated as 'pure r-values'. It has no identity and it's movable."<br>
<center>
<img src="valuecategories.png" height=200px width=500px alt="Value Categories in C++"/>
</center>
Every expression belongs to one of the above categories. Let's dive deep into these value categories using some examples.

## 2. x-values

If an expression states: "a reference to T" then, the expression denotes an object or function denoted by the reference, and the expression is an lvalue or an x-value, depending on the expression. An expression is an x-value in the below-given scenarios:

### 2.1 Scenario 1: The result of calling a function whether explicitly or implicitly, whose return type is an rvalue reference to object type.

Let's consider the below example where a function returns an rvalue reference and makes the expression an x-value.

{{<highlight CPP >}}

#include <iostream>

struct hydrogen {
  int volume;
};

int&& balloon() {
    int a = 7;
    return std::move(a); //'a' is explicitly converted to r-value reference of type int&& and returned.
}

hydrogen&& kite() {
    return std::move(hydrogen{}); //'hydrogen{}' is explicitly converted to r-value reference and returned.
}

int main()
{
    hydrogen&& hydrogenObj = kite(); //x-value expression
    int&& a = balloon(); //x-value expression
    std::cout << a << std::endl;
    return 0;
}
{{< / highlight >}}

In the preceding example, the functions balloon() and kite() returns rvalue references and that makes the calling expressions x-values. As the name denotes, If not returned, the rvalues declared inside the function will expire inside the functions but now we have it returned to the calling expressions in the main function. This behavior makes the calling expression an x-value (eXpiring values). We have extended the expiry of those rvalues by passing the references to the calling expression respectively.

### 2.2 Scenario 2: A cast to an rvalue reference to an object type

Casting an rvalue reference to an object type leads to an x-value expression. Here is an example:

{{<highlight CPP >}}

#include <iostream>

int main()
{
    int&& a = static_cast<int&&>(7); // The expression static_cast<int&&>(7) belongs to the xvalue category, because it is a cast to an rvalue reference to object type.
    std::cout << a << std::endl;

    return 0;
}

{{< / highlight >}}

In the preceding example, we do static_cast to store the address of an rvalue to a variable thus creating an rvalue reference. The whole expression is called an xvalue because it increases the lifetime of the rvalue beyond the declaration of it.

### 2.3 Scenario 3: A class member access expression designating a non-static data member in which the object expression is an xvalue

In this scenario, we need to write an expression that accesses the class data member that is not static. There are some factors which we need to consider while writing the expression. To understand that, we need to know how the "class member access expression" looks like. It has two different parts, an object expression part (The part of expression before the dot operator) and the member identification part (The part of expression after the dot operator). The object expression part of the expression should be an xvalue and the member identification part should be a non-static member of the struct. In the below example, <i>f().m;</i> is an x-value expression because it satisfies the conditions we just discussed above.

{{<highlight CPP >}}

#include <iostream>

struct A {
    int m;
};

 A&& f() {
     A a;
    return std::move(a);
 }
int main()
{
   std::cout << f().m; //x-value expression.
    return 0;
}
{{< / highlight >}}

### 2.4 Scenario 4: A .* pointer-to-member expression in which the first operand is an xvalue and the second operand is a pointer to data member

In this scenario, we need to write an expression that has xvalue as the first operand and it accesses the pointer to the data member declared inside the struct or class. Below is the example that does this.

{{<highlight CPP >}}

#include <iostream>

struct C { 
    int m = 42;
};

int C::*p = &C::m;

C&& get_xvalue() {
        C cObj;
        return std::move(cObj);
    };

int main() {
    std::cout << get_xvalue().*p; //xvalue value expression with the first operand as xvalue and the second operand is pointer to the data member declared in the struct.
    return 0;
}
{{< / highlight >}}

In the above example, we have declared an xvalue expression <i>get_xvalue().*p;</i> in which the first operand <i>get_xvalue()</i> is an method that returns the rvalue reference to the object of struct C. Thus the calling expression in the main() becomes an xvalue because it increases the lifetime of an rvalue. This xvalue is used to access the <i>*p</i> thus makes the whole expression an xvalue.

<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>