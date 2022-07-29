---
title: "Volatile Keyword in C and C++ 👋"
description: "Quoting from the C++ Standard ($7.1.5.1/8):

[..] volatile is a hint to the implementation to avoid aggressive optimization involving the object because the value of the object might be changed by means undetectable by an implementation.[…]"
lead: "Quoting from the C++ Standard ($7.1.5.1/8)

[..] volatile is a hint to the implementation to avoid aggressive optimization involving the object because the value of the object might be changed by means undetectable by an implementation.[…]"
date: 2021-04-21T09:19:42+01:00
lastmod: 2021-04-24T09:19:42+01:00
draft: false
weight: 50
images: []
contributors: ["FluentProgrammer"]
url: "/volatile-keyword-in-c-and-c-plus-plus/"
tags: ['Cpp']
categories: ["Cpp"]

---
Consider this code,

{{< highlight CPP "hl_lines=1-6,linenostart=1" >}}

int some_int = 100;

while(some_int == 100)
{
   //your code
}

{{< / highlight >}}

When this program gets compiled, the compiler may optimize this code, if it finds that the program never ever makes any attempt to change the value of some_int, so it may be tempted to optimize the while loop by changing it from while(some_int == 100) to something which is equivalent to while(true) so that the execution could be fast (since the condition in while loop appears to be true always). (if the compiler doesn’t optimize it, then it has to fetch the value of some_int and compare it with 100, in each iteration which obviously is a little bit slow.)

However, sometimes, optimization (of some parts of your program) may be undesirable, because it may be that someone else is changing the value of some_int from outside the program which compiler is not aware of, since it can’t see it; but it’s how you’ve designed it. In that case, compiler’s optimization would not produce the desired result!

So, to ensure the desired result, you need to somehow stop the compiler from optimizing the while loop. That is where the volatile keyword plays its role. All you need to do is this,

{{< highlight cpp "hl_lines=1-2,linenostart=1" >}}

volatile int some_int = 100; 
//note the 'volatile' qualifier now!

{{< / highlight >}}

In other words, I would explain this as follows:

volatile tells the compiler that,

“Hey compiler, I’m volatile and, you know, I can be changed by some XYZ that you’re not even aware of. That XYZ could be anything. Maybe some alien outside this planet called program. Maybe some lightning, some form of interrupt, volcanoes, etc can mutate me. Maybe. You never know who is going to change me! So O you ignorant, stop playing an all-knowing god, and don’t dare touch the code where I’m present. Okay?”

Well, that is how volatile prevents the compiler from optimizing code. Now search the web to see some sample examples.

Quoting from the C++ Standard ($7.1.5.1/8)

[..] volatile is a hint to the implementation to avoid aggressive optimization involving the object because the value of the object might be changed by means undetectable by an implementation.[…]


<script src="https://utteranc.es/client.js"
      repo="77Bala7790/fluentprogrammer-discussions"
      issue-term="pathname"
      label="Comment"
      theme="github-light"
      crossorigin="anonymous"
      async>
</script>