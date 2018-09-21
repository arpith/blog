---
layout: post
date: 2014-11-24 06:40:25 GMT
title: "Hamming it in Go"
---
# Hamming it in Go

The [bitwise AND operation](http://en.wikipedia.org/wiki/Bitwise_operation#AND) is a convenient way to count the number of ones in a byte: 

        count := 0
        mask := byte(1)
        for i := 0; i < 8; i++ {
                if mask == mask & b { // eg: 0011 & 0010 = 0010 (mask is 2)
                        count++
                }
                mask = mask * 2
        }

This helps me compute the [Hamming weight](http://en.wikipedia.org/wiki/Hamming_weight) of two strings by XOR'ing individual bytes

        count := 0
        for i := range string1 {
                b := string1[i] ^ string2[i]
                count += numberOfOnes(b)
        }

There are better ways to do this of course!
