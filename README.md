Interview Preparedness
======================

This will cover questions to use when interviewing a developer to check that candadites can indeed write code!
This document will ignore imperative approaches and focus only on more functional approaches. Most any language now will give you functional features so these are nice easy approaches to use.

General Approaches
------------------

I've seen people ask to take these problems away and work on them on paper. If you're not sure how to approach a problem, simply start reasoning about the problem verbally. Say out loud "Well, we could start by defining the method" and just work from there. Start with a small piece, talk to the interviewer about what you're thinking. Stop and think about it but don't fall silent - reason out loud.

Fizzbuzz
========

If you're more junior, I'd probably ask for Fizzbuzz. For the numbers 1 to 100, print the number. If it is divisible by 3, print 'fizz' instead. If it is divisible by 5 print 'buzz' instead. If it is divisible by both, print 'fizzbuzz'.

This is a very famous interview question and it really assumes that most developers cannot write code at all. It seems simple because it is simple - no tricks. You only need to be able to write bery basic code.

Conditionals or Pattern Matching
--------------------------------

This isn't asking for a method - it's asking you to deal with 1 to 100 so YAGNI says do the minimum required. It isn't a take home assignment - don't get smart and do the minimum required on the board and talk about it.

    (1 to 100)
    
That's a good start. Now we want to side effect by printing so we can build on the sequence calling foreach.

    (1 to 100).foreach(x => ???)
    
Okay now we have the structure defined - we can fill in the implementation with pattern matching.

    (1 to 100).foreach { x => x match { 
        case x if (x%3==0 && x%5==0) => println("fizzbuzz")
        case x if (x%3==0) => println("fizz")
        case x if (x%5==0) => println("buzz")
        case x => println(x)
      }
    }

You could use conditionals. You aren't being asked to refactor it or save the world. Just answer it simply.    
    
String Reversal
===============
String reversal was the first software development interview question posed to me. An applicant should be able to write basic code on a whiteboard - String Reversal is an easy enough question to answer but suprisingly not many people can actually do it.

There are a few variations you probably want to be aware of. 

Recursive
---------
You can use a recursive function to build the new string.

    def reverseString(input: String, z: String = ""): String = {
        if(input.length==0) z else reverseString(input.tail, input.head + z)
    }

Folds
-----
Erik Meijer said that "recursion is the goto of functional programming". Folds are more explicit in their structure and are a perfect fit for most any recursive processing needed. In this case, we go from right to left using foldRight and pin each element processed onto the end of the collector. This ends up being a much more succinct implementation.

    def reverseString(input: String) = input.foldRight("")((z, x) => x + z)

It has all of the elements of the recursive call but the structure of the fold takes care of the collector and the recursive call. 

Bracket Matching
================
Build a function that takes a string as input and determines if the brackets are balanced. 
Given "(x(x)xx)" return true.
Given "(x)a)a(a" return false. This closes a bracket before one is opened.
Given "(d(dd)r)z(" return false. This has an extra closing bracket.

The aproach I took was to add 1 for every open bracket and subtract one for every closed bracket. Each iteration you check to see if the number is a positive and at the end evaluate if the value is 0.

A more flexible solution would be a heap where you place unclosed braces on the heap and pop one off every time a bracket is closed. They're both more or less the same but the heap adds some flexibility incase you are asked to use different bracket types at the end of your implementation. 
