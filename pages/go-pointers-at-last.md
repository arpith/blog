---
layout: post
date: 2014-11-26 06:56:00 GMT
title: "Go: Pointers at last"
---
I've finally had to use [pointers](https://gobyexample.com/pointers) because I'm [parsing command line flags](https://gobyexample.com/command-line-flags)

        blockPtr := flag.String("block", "YELLOW SUBMARINE", "the block to be padded")
        lengthPtr := flag.Int("length", 20, "final block length")
        flag.Parse()

        n := *lengthPtr - len(*blockPtr)

Another thing I noted was that `%02X` formats a byte as a two character hexadecimal (padded with 0 in front) 

        strs[i] = fmt.Sprintf("%02X",byte(n))
 
This won't work for `n > 255`