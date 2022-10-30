---
layout: post
title: "How it started: The world needs more compilers!"
author: "Mathias Rieder"
---
There are several milestones in a software-engineer's life:
1. Write your own logging-framework
2. Write your own testing framework
3. Write your own compiler

It is 2022 and we are stumbling from one global crisis into the other so it looks like this is the right time for milestone number #3.

# Why a new compiler?
This project started as a side project by my colleague [Ghaith](https://github.com/ghaith) and [me](https://github.com/riederm). We are working as software engineers in the domain of automation and in particular we were both developing and maintaining the programming tools. The products included IDEs and SDKs for IEC 61131-3 programming languages popular for automation applications. The IEC 61131-3 standard includes a set of programming languages tailored for different tasks in automation applications. There are graphical- and text-based languages. Since through our work we already had a deep understanding of these languages including their syntactical and semantic rules, their runtime behavior and also the limitations and drawbacks of existing compilers we decided one night to learn something new - namely to create a modern open source compiler for IEC 61131-3 languages!

The standard was first published in 1993 and it received its last update in 2013. At first sight these languages look a bit old-fashioned if you look through the eyes of a 2022 software engineer. To be fair, they are old-fashioned in their syntax as well as in regard to their feature set. They lack established syntax-patterns and also language features which are part of any modern language in 2022. These languages were designed when parsers, compilers and developing machines were limited. Excellent! What this means is that parsing and compiling the language should be a bit easier and that there are modern features we can add to enhance the language.

As mentioned earlier, the standard proposes several languages for automation applications. Some of them are graphical, some are text-based. It feels natural to start with a text-based language and think about how to parse and compile a graphical language later. The most relevant language from IEC 61131-3 (from my perspective) is *Structured Text*.

# Structured Text - ST
I guess we will talk a lot about the specifics of ST in the course of this journey, I would like to give a short introduction of the language:

In 2017 [Tom Stevic](https://www.linkedin.com/in/thomasstevic/) gave a great introduction into the [history of PLC programming platforms](https://www.controldesign.com/control/control-software/article/11317865/a-very-short-history-of-plc-programming-platforms). He mentions that the IEC 61131 standard was developed throughout the 1980's. Therefore we should expect heavy influences from that area when we think about the programming languages they specified back then. Structured Text as a language is basically derived from Pascal and only differs in small details when it comes to syntax. Lets take a look at a small piece of code from the [ST's wikipedia article](https://en.wikipedia.org/wiki/Structured_text):
```iecst
PROGRAM Monitor_Start_Stop    (* Actual Program *)
    VAR_EXTERNAL
        Start_Stop  : BOOL;
        ON_OFF      : BOOL;
    END_VAR
    (* Temporary variables for logic handling *)
    VAR                              
        ONS_Trig    : BOOL;
        Rising_ONS  : BOOL;
    END_VAR

    (* Start of Logic
       Catch the Rising Edge One Shot of the Start_Stop input *)
    ONS_Trig    := Start_Stop AND NOT Rising_ONS;
    
    (* Main Logic for Run_Contact -- Toggle ON / Toggle OFF --- *)
    ON_OFF := (ONS_Trig AND NOT ON_OFF) OR (ON_OFF AND NOT ONS_Trig);

    (* Rising One Shot logic *)
    Rising_ONS := Start_Stop;
END_PROGRAM
```
So what we see on first sight is:
- The language looks nothing like today's kind of established C-like syntax that you see everywhere from C/C++ to Java to C# and JavaScript.
- It preferes keywords over curly brackets
- It has some strong opinions on where declarations and code belongs to

We will see if these aspects actually reduce the complexity of a compiler for such a language or not. But I am confident that if people have been able to write compilers for such a language since the 1970s there is a slight chance that we can duplicate their work and maybe even add a bit of 2020's flavor to it.

# What to expect
When we started this project we already took a couple of important design decisions:
1. RuSTy will be written in Rust because to me Rust has been the most exciting new programming language since 10 years - and we wanted to learn it thoroughly. This is also how Ghaith came up with this quite generic working title `RuSTy` - he wanted to adress Rust and ST. I agreed to this title because it allows us to pick the character of *Rusty Griswold* from 1983's [National Lampoon's Vacation](https://en.wikipedia.org/wiki/National_Lampoon%27s_Vacation) as a mascot. Since this is arguably one of the greatest Movies of all time we picked `RuSTy`as a working title.
![Various actors playing Rusty Griswold](/assets/img/rusty_griswolds.png)

2. We had little knowledge about how to write a compiler, but we really knew nothing about what it means to generate state-of-the-art machine code. Luckily this problem was solved by Vikram Adve and Chris Lattner in the 2000s when they published [LLVM](https://en.wikipedia.org/wiki/LLVM). So RuSTy is a LLVM-Frontend for Structured Text. The platform-dependend machine-code is generated by the LLVM backend.
![LLVM Logo](/assets/img/LLVM_logo.png)

3. In 2013 the IEC 61131-3 standard was updated. Object-oriented features were introduced into the languages. While we will definetely cover the 2013 additions we will first target the pre 2013 understanding of the language to not early introduce advanced concepts like inheritence, exceptions and polymorphism while we are still fighting the very basics of compiler construction. 



This blog will follow the development of the RuSTy compiler and try to explain the implemenation, the design decisions we took, the problems we faced and how we solved them, as well as acting as a platform to talk about the incredible work of these awesome [people](https://github.com/PLC-lang/rusty/graphs/contributors) working on this [project](https://github.com/PLC-lang/rusty).

Stay tuned for more on how to develop a compiler for a 40 years old language in 2022!

*y'all take it easy -  mathias*