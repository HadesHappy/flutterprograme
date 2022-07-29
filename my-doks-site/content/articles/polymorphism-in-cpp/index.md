---
title: "Polymorphism in C++ 👋"
description: "Polymorphism in C++ is explored deeply in this article"   
lead: "Polymorphism means many forms. Ability of a message to be displayed in many forms is what it is all about. Polymorphism is very important feature in Object Oriented Programming. In C++, we have four different types of polymorphism as mentioned below"
date: 2022-06-10T09:20:42+01:00
lastmod: 2022-06-10T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
toc: true
type: "docs"
url: "/polymorphism-in-cpp/"
keywords:
  - Polymorphism in C++
  - Run time polymorphism
  - compile time polymorphism
  - early binding
  - late binding
  - C++
tags: ['Cpp']
categories: ["Cpp"]
---

## Polymorphism - Introduction

1. Compile-time polymorphism
2. Run time polymorphism
3. Ad-hoc polymorphism
4. Coercion polymorphism

The above four polymorphisms also have different names. Run time polymorphism is also called as Subtype polymorphism, Compile-time polymorphism is also called as the Parametric polymorphism, Ad-hoc polymorphism is also known as overloading, and Coercion polymorphism is also known as the (implicit or explicit) casting.

All this various polymorphisms will make sense after reading some examples which I have shown below.

## Compile-time polymorphism (Parametric polymorphism)

Parametric polymorphism is all about executing the same code for any type. Templates are very good example for the parametric polymorphism. One of the simplest example using templates is shown below:

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>
#include <string>

template <typename T> 
T max(T a, T b) {
  return a > b ? a:b;
}

int main() {
  std::cout << max(9,5) << std::endl;
  std::cout << max(123.1902, 12332.29019) << std::endl;
  return 0;
}
{{< / highlight >}}

 Here the <code>max()</code> function is polymorphic on the type <code>T</code>. Note that if we specialize the <code>max()</code> function then it won't be a parametric polymorphism anymore. It will be an example of Ad-hoc polymorphism or Function overloading.

 Parametric polymorphism is also known as compile-time polymorphism because the types are applied to the template at the compile time and many forms of the template is created.

## Runtime polymorphism (Subtype polymorphism)

Runtime polymorphism as the name defines is the ability to use the derived classes through the base class pointers and references. Never ever forget the <code>virtual</code> keyword when it comes to the runtime polymorphism. Overiding the base class function in the derived classes is all what triggers runtime polymorphism.

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>
#include <string>

class Bike {
  public:
  virtual void speedup() = 0;
};

class Honda : public Bike {
  public:
  void speedup() {
    std::cout << "Honda speeds at 100 km per hour" << std::endl;
  }
};

class BMW : public Bike {
  public:
  void speedup() {
    std::cout << "BMW speeds at 200 km per hour" << std::endl;
  }
};


void do_speedup(Bike *bike_obj) {
  bike_obj->speedup();
}

int main() {
  Honda honda_bike_obj;
  BMW BMW_bike_obj;

  do_speedup(&honda_bike_obj);
  do_speedup(&BMW_bike_obj);

  return 0;
}
{{< / highlight >}}


The resolution of polymorphic function calls happens at runtime through an indirection via the virtual table. Compiler does not locate the address of the function to be called at the compile-time, instead the function is called by dereferencing the right pointer in the virtual table. 

So this is also called as inclusion polymorphism (or) runtime polymorphism (or) subtype polymorphism.


## Ad-hoc polymorphism (Overloading)

Ad-hoc polymorphism allows functions with the same name to act differently for each type. For example, two <code>int</code> variables and the <code>+</code> operator, it adds them together. Given two <code>std::string</code> variables it concatenates them together. This is called overloading. Wait, not just overloading. Function overloading. We also have operator overloading which is to overload operators. Ad-hoc polymorphism appears in C++ if we specialize a template, overload a function or if we overload an operator. 

Here is an example of Ad-hoc polymorphism that implements <code>add</code> for <code>int</code> and <code>string</code> variables,

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>
#include <string>

int add(int a, int b) {
  return a + b;
}

std::string add(const char *a, const char *b) {
  std::string result(a);
  result +=b;
  return result;
}

int main() {
  std::cout << add(4,7) << std::endl;
  std::cout << add("hello", "world") << std::endl;
}
{{< / highlight >}}

Ad-hoc polymorphism also appears when we specialize the templates as shown below

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
template<>
const char *max(const char *a, const char *b) {
  return strcmp(a, b) > 0? a: b;
}
{{< /highlight >}}

Now we just call <code>::max("aaa","bbb")</code> to find the maximum of strings "aaa" and "bbb".

## Coercion polymorphism (Casting)

Coercion happens when the object or a primitive is cast into another object type or primitive type. For example,

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
float b = 6; //example of implicit casting. int gets casted to float
int a = 9.99; //example of implicit casting. float gets casted to int
{{< /highlight >}}


Explicit casting happens when you use C's type casting expressions or C++ casting expressions like <code>static_cast</code>, <code>const_cast</code>,
<code>reinterpret_cast</code>, or <code>dynamic_cast</code>.

Coercion also happens if the constructor if the constructor is not explicit, for example, 

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
#include <iostream>

class A {
  int foo;
  public:
    A(int a) : foo(a) {}

    void method1(() {
      std::cout << foo << std::endl;
    }
};

void function1(A a) {
  a.method1();
}

int main() {
  function1(75); //explicitly converts the argument 75 to the parameter of object of A.
  return 1;
}
{{< /highlight >}}

<blockquote><p>
If you've liked this blog post, consider donating or otherwise supporting me.
</p></blockquote>


<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>
