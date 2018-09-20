---
layout: post
date: 2014-11-17 07:13:00 GMT
title: "My first Go program"
---
First off: [installing Go](http://golang.org/doc/install) looks complicated, but blindly following the instructions is easy! No pets will be harmed (I hope!)

I want to convert hex to [base64](http://en.wikipedia.org/wiki/Base64). [Gobyexample.com](https://gobyexample.com/) explains how to [grab command line arguments](https://gobyexample.com/command-line-arguments). The first thing I noticed was that you can create a variable and assign it a value using `:=`

    hex := os.Args[1] // zero is the path to the program

To get the hex values from the string as bytes I tried the [`Sscan` function](http://golang.org/pkg/fmt/) and got nothing. So the second thing I learnt was that functions can return <s>tuples</s> [multiple values](https://golang.org/doc/effective_go.html#multiple-returns)

    var b string
    n, err := fmt.Sscan(hex, "%x", b) // number of items successfully scanned

I got `0`, and `type is not a pointer: string`. Googling took me to a [go cheat sheet](https://github.com/basti1302/go-lang-cheat-sheet) so I tried `&b` as an argument to `Sscan`. I've also switched from

    $ go build program.go
    $ ./program "hex string"

to just 
    
    $ go run program.go "hex string"

Looking over [golang.org/pkg/fmt](http://golang.org/pkg/fmt/) again I realised I wanted `Sscanf`, but was typing `Sscan` (here as well!). Finally, Go conveniently [supports base64 encoding](https://gobyexample.com/base64-encoding) and that page says the encoder requires a `[]byte` so I've changed `var b string` to `var b []byte` and used

    sEnc := b64.StdEncoding.EncodeToString(b)

Of course, I've imported `"encoding/base64"` as `b64` not to save space (as the page suggests) but because pasting is more fun than typing. 

Seems to work! 

