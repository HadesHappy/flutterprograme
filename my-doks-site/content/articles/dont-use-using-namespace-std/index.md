---
title: "C++ Best Practice #1: Don't simply use: using namespace std; 👋"
description: "Never try to import the whole std namespace into your program. Namespaces are developed to avoid naming conflicts. But, when we import the whole namespace there is a possibility that this could lead to name conflicts. You can do more harm than more good." 
lead: "Never try to import the whole std namespace into your program. Namespaces are developed to avoid naming conflicts. But, when we import the whole namespace there is a possibility that this could lead to name conflicts. You can do more harm than more good."
date: 2021-04-21T09:19:42+01:00
lastmod: 2021-04-26T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
toc: true
type: "docs"
url: "/dont-use-using-namespace-std/"
keywords:
  - namespace
  - using namespace std
  - C++
  - Best practice
tags: ['Cpp', 'Best-Practice-in-cpp']
categories: ["Cpp"]

---

## Description

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}
using namespace std;
{{< / highlight >}}

 It is okay to import the whole std library in toy programs but in production-grade code, It is bad. <code>using namespace std;</code> makes every symbol declared in the namespace std accessible without the namespace qualifier. Now, let's say that you upgrade to a newer version of C++ and more new std namespace symbols are injected into your program which you have no idea about. You may already have those symbols used in your program. Now the compiler will have a hard time figuring out whether the symbol declared belongs to your own implementation or from the namespace you imported without any idea. Some compilers throw errors. If you are unlucky, the compiler chose the wrong implementation and compile it which for sure leads to run time crashes.

For example, Consider the below code,

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

#include <iostreeam>
#include "foo.h" \\ Contains the implementation for same_as

using namespace std; \\Bad practice
class semiregular  { }; \\Implemented in foo.h

int main() {
  semiregular  b;
  ............
  cout << "Hello, Congrats.You are on your way to becoming an expert in programming...">>
}

{{</highlight >}}

The above code won't trigger any compile-time error if compiled with C++17. It may throw an error when compiled with C++20 that the header file <concepts> is not included. This is because semiregular is a feature implemented in C++20. So it's always better to be on the safer side by not loading up your program with all the symbols a namespace offer. Instead use the :: annotation and use the full qualifier like std::cout. 

{{<highlight CPP "h1_lines=1-6,linenostart=1" >}}

#include <iostreeam>
#include "foo.h" \\ Contains the implementation for same_as

class semiregular  { }; \\Implemented in foo.h

int main() {
  semiregular  b;
  ............
  std::cout << "Hello, Congrats.You are on your way to becoming an expert in programming...">>
}

{{</highlight >}}

The above code didn't load the whole of std so semiregular is not considered to be part of std and it considers the class implemented by us.

## Conclusion

Never use "using namespace std;". This also applies to all the namespaces. There is a possibility for naming conflict.


<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>
