---
layout: post
title: "Building Blocks of a compiler"
author: "Mathias Rieder"
---

This post will talk a bit about how we approached to first quesion you have to ask yourself
when building a cmpiler. How does one build a compiler? What are the obvious technology
choices, what is the architecture and where to start?

Compilers (and especially parsers) have been a huge topic in computer-science related 
research from the very beginning. There are tons of papers and books on how build parsers 
and compilers. We definetly need to mention the 
[dragon book](https://en.wikipedia.org/wiki/Compilers:_Principles,_Techniques,_and_Tools) (1986, 2006)
which is widely regarded as the definitive source about compiler construction (although 
the topic is still a big topic in research).

## LLVM Compilers
LLVM is a framework that offers a standardised way to create compilers without the hassel creating platform specific and even chip/cpu-specific machine code. It basically splits the compiler into 3 parts: the Frontend, the Mid-Part and the Backend.


![Basic architecture of LLVM compilers](/assets/img/frontend_opt_backend.png)


The *Frontend* is the part that we need to create. It reads the program we want to compile and transforms it into a generic representation - the [Intermediate Representation](https://en.wikipedia.org/wiki/LLVM#Intermediate_representation) or short *IR*. IR is actually a RISC instruction set which abstracts most target specific details. *IR* offers a generic *function-call* concept and it offers an infinite number of virtual registers and therefore makes it much easier to work with compared to target-specific instruction sets.

The *Mid-Part* takes the *IR* as input and performs a rich set of optimizations. These optimizations can be performed on the generalized representation of the language wich enables LLVM-compilers to share optimizations across different languages and compilers. This basically means that we can get clang-like code optimization basically for free - which is kind of amazing.

The *Backend* is the last step of an llvm-compiler. It converts the optimized *IR* into target specific machine-code. Similar to the optimizer in the *Mid-Part* this phase works with the *IR* and therefore is not specific to our language. This again means that we can basically use all available backend-implementations of the llvm-ecosystem. It again means that we basically get a compiler for all kinds of different plattforms (Windows, Linux, Mac, etc.) and targets (x86, ARM, etc.) for free - again amazing.

So the one thing we can take away from this small introduction is, that we have to build the Frontend that translates the source code (Structured Text - we'll deal with the other languages later) into *LLVM-IR*. The rest will be handled by the LLVM ecosystem.

## LLVM Frontends