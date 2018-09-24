---
layout: post
date: 2014-11-22 07:10:00 GMT
title: "Go: Range, runes and bytes"
---
# Go: Range, runes and bytes

Just a quick note on using [range](https://golang.org/ref/spec#RangeClause) in a [for loop](https://golang.org/ref/spec#For_statements) to iterate over a string. Like I [noted earlier](http://arpith.co/post/103198032247/variables-scopes-and-fiddling-with-strings-in-go) the second value returned is a [`rune`](https://golang.org/ref/spec#Rune_literals) whereas using the first value as an index [gives you a `byte`](https://golang.org/ref/spec#String_types) (which is what I was doing in [this post](http://arpith.co/post/102944903612/stumbling-through-go)).