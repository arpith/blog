---
layout: post
date: 2014-11-22 06:25:00 GMT
title: "Go: Reading lines from (small) files"
---
Not much to talk about today, I'm going to be reading a bunch of strings from a file (not [just two](http://arpith.co/post/102944903612/stumbling-through-go)). It's not really a whole lot, so I'm going to be lazy and just read the file and split it by newline characters:

        dat, e := ioutil.ReadFile("challenge4.in")
        if e != nil {
                panic(e)
        }
        strs := strings.Split(string(dat), "\n")

Note that [Split deals with strings](http://golang.org/pkg/strings/#Split) but [ReadFile returns a slice of bytes](http://golang.org/pkg/io/ioutil/#ReadFile).