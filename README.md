Interview Preparedness
======================

This will cover questions to use when interviewing a developer to check that candadites can indeed write code!
This document will ignore imperative approaches and focus only on functional approaches in Scala. Most any language now will give you functional features so these are nice easy approaches to use if you're interviewing.

I've been interviewing a great deal lately for enterprise developer roles and I'm very amazed by what whiteboarding and takehome assignments uncover about people's experience.

General Approaches
------------------

I've seen people ask to take these problems away and work on them on paper. If you're not sure how to approach a problem, simply start reasoning about the problem verbally. Say out loud "Well, we could start by defining the method" and just work from there. Start with a small piece, talk to the interviewer about what you're thinking. Stop and think about it but don't fall silent - reason out loud.

These are a great fit for phone interviews using something like collabedit - it's a bit hard to move text around on a whiteboard so a pre-screening call with some coding is a good start. The hire slow philosophy states that there should be multiple steps in the interview process. I feel that prescreening with coding, design and knowledge checking in person, and then a take-away assignment is a good basic process to use for evaluating resources. 

For a takehome assignment I usually ask for a project that does something simple like determine word frequency in a document. The applicant should show good OO approaches like DI, Single Responsibility Principle etc and actually be able to submit a working project with unit tests. If an applicant is applying for a developer role and they can't follow instructions or build a simple working project, that may be a indicator that the developer has a bit less experience than you may need for the role you're filling.

Face to Face
------------

For the face to face I generally go over an applicant's recent experience with them and ask them questions about everything they write. If they say they have SCJP/OCP, I ask about details like string interning. If they say they know Agile, I ask them about the ceremonies, how they would throw a retrospective. If they say they have ITIL/ITSM experience, I ask them the difference between incident and problem management. If they say they know about distributed computing, I ask them about CAP theorem. If they say they know about high availability, I ask them to show me how they would design a system for 0 downtime deployments. In my experience, simply drilling into the experience people present will show if they do indeed understand the topics they claim to have experience in.

Live Coding: Jr Assignment - Fizzbuzz
=====================================

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

You aren't being asked to refactor it or save the world. Just answer it simply.    
There are other approaches you could take but if someone is asking you to show them fizzbuzz they just want to make sure you can actually write code.

    val tuples = for(x <- 1 to 100) yield(x, x%3, x%5)
    tuples.foreach(x => x match {
      case (_, 0, 0) => println("fizzbuzz")
      case (_, 0, _) => println("fizz")
      case (_, _, 0) => println("buzz")
      case (current, _, _) => println(current)
    })
    
Live Coding: Jr Assignment - String Reversal
============================================
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
Erik Meijer said that "recursion is the goto of functional programming". Folds are more explicit in their structure and are a perfect fit for most any recursive processing needed when you need to recurse a structure in its entirety. In this case, we go from right to left using foldRight and pin each element processed onto the end of the collector. This ends up being a much more succinct implementation.

    def reverseString(input: String) = input.foldRight("")((z, x) => x + z)

It has all of the elements of the recursive call but the structure of the fold takes care of the collector and the recursive call. You can see that it's much more succinct. Folds are a great thing to learn - they can really whittle down any recursive functions you need to write to a much simpler form.

Live Coding: Intermediate Assignment - Bracket Balance
======================================================

Build a function that takes a string as input and determines if the brackets are balanced. 
Given "(x(x)xx)" return true.
Given "(x)a)a(a" return false. This closes a bracket before one is opened.
Given "(d(dd)r)z(" return false. This has an extra closing bracket.

The aproach I took was to add 1 for every open bracket and subtract one for every closed bracket. Each iteration you check to see if the number is a positive and at the end evaluate if the value is 0.

A more flexible solution would be a heap where you place unclosed braces on the heap and pop one off every time a bracket is closed. They're both more or less the same but the heap adds some flexibility incase you are asked to use different bracket types at the end of your implementation. 

    import scala.util.Try
    def bracketBalance(input: String): Boolean = {
      Try(input.foldLeft(List.empty[Char])((z,char) => {
        char match {
          case _ if char == '(' => char :: z ::: Nil
          case _ if char == ')' =>
            if (z.head == '(') z.tail
            else throw new Exception("no match")
          case _ => z
        }
      }).size == 0).getOrElse(false)
    }

This is arguably not a very good first attempt - it has a few unclear effects. It throws an exception for flow control as an optimization, but also calling z.head will throw an exception if z is empty which is a very unclear effect. You could simply add the value to the heap which would increase the readability at the expensive of worse best-case runtime complexity. We'll do this next and add a twist.

In an interview I was in, I was asked how I would implement handling '{[()]}' after finishing my implementation. Using the linked-list as a heap, this is a fairly simple task. We clean up the exceptions here at the cost of traversing the entire string.

    def bracketBalanced(input: String): Boolean = {
      input.foldLeft(List.empty[Char])((z,char) => {
        char match {
          case '(' | '{' | '[' => char :: z ::: Nil
          case ')' if z.headOption == Some('(') => z.tail
          case ']' if z.headOption == Some('[') => z.tail
          case '}' if z.headOption == Some('{') => z.tail
          case ')' | ']' | '}' => char :: z ::: Nil
          case _ => z
        }
      }).size == 0
    }
    
This is a fairly simple solution. This foregoes the Try so it's a bit less efficient best case complexity but is simpler, eliminating effects. We have to use headOption incase the heap/list is empty.

Finally, to improve the runtime complexity, we could change the solution to use recursion - the solution now will always traverse the entire list of characters. I think the fold is a clearer solution though and would tend to use it whenever whiteboarding as it's clearer, more concise, and easier to structure than recursion. 

Basic Interview Question - Convert Excel Cell To Value
------------------------------------------------------
I saw this on the interview question list and it seems pretty simple.

Write a function (with helper functions if needed) called to Excel that takes an excel column value (A,B,C,D…AA,AB,AC,… AAA..) and returns a corresponding integer value (A=1,B=2,… AA=26..).

I chose to seperate validation of input, the convertion of the individual values, and the handling of the entirety of the input in seperate functions and then compose that together.

    import scala.util.{Success, Failure, Try}
    
    def convertCharToVal(input: Char): Int = {
      input-64
    }
    
    def validateInput(input: String): Try[String] = {
      val valid = input.foldLeft(true)((z,x) => {
        if(z == false || x<'A' || x>'Z')
          false
        else
          true
      })
    
      if(valid)
        Success(input)
      else
        Failure(new Exception("invalid input"))
    }
    
    def convertCellToValueWithValidation(input: String): Try[Int] = {
      validateInput(input).map(convertCharToValue(_))
    }
    
    convertCellToValueWithValidation("ZZ") //52

Conclusion
==========
Here are a few common whiteboard excercises I personally like to throw at people who are interviewing, as well as general interviewing approaches. I think you should be able to reason through problems like these on the white board or in collab edit without an IDE's help - it only takes a bit of preparation to get comfortable enough to work out simple challenges on a whiteboard. These are not google interview questions - they're just basic whiteboard problems. 
