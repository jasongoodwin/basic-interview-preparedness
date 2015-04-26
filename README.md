Interview Preparedness
======================

I've been interviewing lately and I'm quite suprised by what I see.
I'll try to put together a few questions and answers here that I think you should be able to do.

This will cover questions to use when interviewing, and also cover what the interviewer may want to think about.
This document will ignore imperative approaches and focus on functional ones, preferring immutability at the expense of using a bit more memory.

String Reversal
===============
String reversal was the first software development interview question posed to me. An applicant should be able to write basic code on a whiteboard - String Reversal is an easy enough question to answer but suprisingly not many people can actually do it.

There are a few variations you probably want to be aware of. 

Recursive
---------
You can use a recursive function to build the new string.

    scala> def reverseString(input: String, z: String = ""): String = {
         |   if(input.length==0) z else reverseString(input.tail, input.head + z)
         | }
    reverseString: (input: String, z: String)String
    scala> reverseString("hello")
    res6: String = olleh

Folds
-----
Erik Meijer said that "recursion is the goto of functional programming". Folds are more 
