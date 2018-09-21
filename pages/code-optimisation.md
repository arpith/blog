---
layout: post
date: 2010-04-19 22:52:00 GMT
title: "Code Optimisation"
---
# [Code Optimisation](http://zenhabits.tumblr.com/post/499582559/all-thats-interesting-couch-surfing)

I was reading about how `object.toString()` isn't forced to return a string ([and other trivia](http://www.adequatelygood.com/2010/3/Object-to-Primitive-Conversions-in-JavaScript)), and got distracted by the difference in performance between Chrome and Safari (and really, everything else).

>After understanding the complexity that goes into these implicit conversion, I got curious about how that affects performance. So I decided to test the time it takes to perform an `[].join(obj)` over 1,000,000 iterations in the major browsers. I did one test with the object being implicitly cast to a string, and one where I called the `toString` method manually (i.e. `[].join(obj.toString())`). As expected, the explicit call was faster in most cases.
>
> * Firefox 3.6.2: 874ms vs. 320ms - almost 3x faster.
> * Chrome 5: 94ms vs. 47ms - 2x faster.
> * Opera 10.50: 155ms vs 182ms - a little slower.
> * Safari 4: 409ms vs 280ms - almost 2x faster.
> * Internet Explorer 8: 2856ms vs 2786ms - about the same.
> * Internet Explorer 9 (preview): 645ms vs 633ms - about the same.

I knew some people at Mozilla were working on a [new JavaScript engine](http://blog.mozilla.com/dmandelin/2010/02/26/starting-jagermonkey/)--borrowing a component from WebKit--and that one thing they were interested in was when not to optimise code.

> The first thing we needed was a fast assembler to generate the native code. TraceMonkey has a native code compiler, nanojit, but we thought nanojit wasn't ideal for JägerMonkey. Nanojit does a fair number of compiler backend optimizations, like dead store elimination and common subexpression elimination, which allows to generate faster code, but makes it take longer to generate that code. We don't expect those optimizations to help much in the Jäger domain, so we wanted something simpler and faster.

More [here](http://hacks.mozilla.org/2010/03/improving-javascript-performance-with-jagermonkey/) and [here](http://hacks.mozilla.org/2010/03/a-quick-note-on-javascript-engine-components/), but I had no idea what dead store elimination (_spoiler_: I don't know) and common subexpression elimination (_spoiler_: just what it says) were.  The copy of the dragon book that I had (red, if that matters) roughly said
that there are optimisations possible both in the intermediate code that the compiler generates, and in the final target code, like using the addressing modes of the machine when choosing instructions---or even just storing useful variables in the registers---but that this seems suitably machine specific to not be very useful in an ancient book.

One thing to remember is that if we're taking somebody's code and modifying it in the hope that it will run better, it's useful to ask ourselves if it really does, but more so to verify that it behaves the same way on the input (no use adding bugs).  The attitude prescribed seems to be "when in doubt, don't".


##Basic Blocks
* DAG
* Common subexpressions
* Copy propagation
* Dead code
* Algebra
  * Arithmetic identities
  * Reduction in strength
  * Constant folding
  * Commutativity
  * Relational operators
  * Associativity
##Loops
* Moving code
* Induction variables
* Dominators, dominating tree
* "Natural" loops
* Inner loops
* Pre-headers
* Reducible flow graphs
##Global Analysis
* Data-flow equations
  * Generating and killing
  * Blocks vs. statements
  * Procedure calls, pointer variable assignments, array variable assignments
* Points and paths
* Unambiguous and ambiguous definitions
* Data-flow analysis of the program
  * Being conservative
  * Computing `in` and `out`
  * Loops
* Representing the sets
* Local reaching definitions and use definition chains
* Evaluation order
* General control flow
##Solving Iteratively
* Forward vs. backward, confluence operators
* Iterative reaching
* Available expressions
* Live-variable analysis
* Definition-use chains
##Code Improving
* Eliminating global common sub-expressions
* Copy propogation
* Detecting loop invariants
* Performing code motion
  * Alternatives
  * Maintaining data-flow information
* Eliminating induction variables
  * Detection
  * Strength reduction
  * Elimination
  * Loop-invariant expressions
##Next
* Aliases
* Analysing structured flow graphs
* Efficient data-flow algorithms (and a tool)
* Type estimation
* Symbolic debugging
