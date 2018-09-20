---
layout: post
date: 2014-11-18 07:38:00 GMT
title: "Stumbling through Go"
---
Today I'm [XOR](http://en.wikipedia.org/wiki/Exclusive_or)'ing byte arrays; fortunately, [this blog post](http://blog.golang.org/strings) seems to indicate that plain strings will do: 

> Strings are built from bytes so indexing them yields bytes, not characters. A string might not even hold characters. In fact, the definition of "character" is ambiguous and it would be a mistake to try to resolve the ambiguity by defining that strings are made of characters.

I'm too lazy to input two strings each time I run the program, so I'm going to [read them from a file](https://gobyexample.com/reading-files)

    dat, err := ioutil.ReadFile("challenge2.in")

Since there is one more error I'll have to check for ([Sscanf](http://golang.org/pkg/fmt/)) I'm doing as the page suggests and adding a small function on top,

    func check(e error) {
        if e != nil {
            panic(e)
        }
    }

I don't really care about the number of items successfully scanned, so I tried 

     _, e := fmt.Sscanf(string(dat),"%x\n%x",&string1,&string2)

which seemed to work. Googling for a link to explain it and found out I was wrong about tuples yesterday, apparently [the multiple values returned by functions do not represent a first class object](http://stackoverflow.com/questions/13670818/pair-tuple-data-type-in-go) so I googled [multiple returns](https://golang.org/doc/effective_go.html#multiple-returns) which lead me to the section on [error handling](https://golang.org/doc/effective_go.html#errors) which mentioned

> This is only an example but real library functions should avoid panic. If the problem can be masked or worked around, it's always better to let things continue to run rather than taking down the whole program. One possible counterexample is during initialization: if the library truly cannot set itself up, it might be reasonable to panic, so to speak.

I can't really do anything without reading the file and scanning the strings so I'm going to leave my `check` function as it is. I also found my [blank identifier](https://golang.org/doc/effective_go.html#blank). As for the actual XOR'ing of the strings, it's [been explained](https://groups.google.com/forum/#!topic/golang-nuts/m7tTO2jnXG4) [before](http://play.golang.org/p/kPmk_r9MUl):

    n := len(string1)
    b := make([]byte, n)
    for i := 0; i < n; i++ {
        b[i] = string1[i] ^ string2[i]
    }

So! Unlike yesterday, let's read a bit more about `[]byte`: [`make` says something interesting](http://golang.org/pkg/builtin/#make),

> Slice: The size specifies the length. The capacity of the slice is
equal to its length. A second integer argument may be provided to
specify a different capacity; it must be no smaller than the
length, so make([]int, 0, 10) allocates a slice of length 0 and
capacity 10.

Which [this fascinating blog post](http://blog.golang.org/slices) clarifies.

> What if we want to grow the slice beyond its capacity? You can't! By definition, the capacity is the limit to growth. But you can achieve an equivalent result by allocating a new array, copying the data over, and modifying the slice to describe the new array.

The blog post goes on to talk about how `append` works, but I saw this `extend` function and wondered where the old slice goes.

    // Slice is full; must grow.
    // We double its size and add 1, so if the size is zero we still grow.
    newSlice := make([]int, len(slice), 2*len(slice)+1)
    copy(newSlice, slice)
    slice = newSlice  

Go has [garbage collection](https://golang.org/doc/faq#stack_or_heap)!

> When possible, the Go compilers will allocate variables that are local to a function in that function's stack frame. However, if the compiler cannot prove that the variable is not referenced after the function returns, then the compiler must allocate the variable on the garbage-collected heap to avoid dangling pointer errors. Also, if a local variable is very large, it might make more sense to store it on the heap rather than the stack.

> In the current compilers, if a variable has its address taken, that variable is a candidate for allocation on the heap. However, a basic escape analysis recognizes some cases when such variables will not live past the return from the function and can reside on the stack.

The faq lead me to this explanation of [allocation with `make`](https://golang.org/doc/effective_go.html#allocation_make)

> The built-in function make(T, args) serves a purpose different from new(T). It creates slices, maps, and channels only, and it returns an initialized (not zeroed) value of type T (not *T). The reason for the distinction is that these three types represent, under the covers, references to data structures that must be initialized before use. A slice, for example, is a three-item descriptor containing a pointer to the data (inside an array), the length, and the capacity, and until those items are initialized, the slice is nil. For slices, maps, and channels, make initializes the internal data structure and prepares the value for use. 

Finally, back to the actual program I was writing, I've replaced `for i := 0; i < n; i++` with `for i := range b`. Note that you [can't replace `b[i]` with `bvalue`](https://code.google.com/p/go-wiki/wiki/Range) (if you had used `for i, bvalue := range b`). [`for` statements](http://golang.org/ref/spec#For_statements) are interesting, 

> In its simplest form, a "for" statement specifies the repeated execution of a block as long as a boolean condition evaluates to true. The condition is evaluated before each iteration. If the condition is absent, it is equivalent to the boolean value true.

Like a while loop! 