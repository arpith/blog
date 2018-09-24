---
layout: post
date: 2014-11-26 04:29:28 GMT
title: "Transforming slices in Go"
---
# Transforming slices in Go

I have a function I want to perform on blocks on a slice, and I saw a neat way to do it [here](https://code.google.com/p/go/issues/detail?id=5597): 

        dst := make([]byte, len(src))
        dstslice := dstslice

        for len(src) > 0 {
                performFunction(dstslice, src)
                dstslice = dstslice[blocksize:]
                src = src[blocksize:]
        }

I suppose there is a way to do this with `dst` and not `dstslice`, maybe in a function? 