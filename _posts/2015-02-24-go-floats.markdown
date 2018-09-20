---
layout: post
date: 2015-02-24 08:58:00 GMT
title: "Go: floats"
---
My [Go script to create a video from slides and audio](https://github.com/arpith/slides2video) now uses timestamps (in milliseconds) instead of slide durations. 

    var imgDuration float64
    if (i == len(lines)-1) || (lines[i+1] == "") {
           imgDuration = float64(timestamp) / 1000
    } else {
           nextLineSplit := strings.Split(lines[i+1], " ")
           nextTimestamp, err := strconv.Atoi(nextLineSplit[0])
           if err != nil {
                 log.Fatal(err)
           }
           imgDuration = float64(nextTimestamp - timestamp) / 1000
    }
    imgDurationString := strconv.FormatFloat(imgDuration, 'f', 3, 64)
    
    
If we are on the last slide the assumption is that the slide can't be on for more than half the show (we can trim to the size of the audio using ffmpeg). Otherwise, we look at the timestamp for the next slide and convert the difference into seconds. Note that [there is no `float` type](http://stackoverflow.com/questions/19230191/golang-convert-integer-to-float-number). Once again [strconv](http://golang.org/pkg/strconv/#FormatFloat) comes in handy, (this time to get a string with the three digits past the decimal point - `64` is to indicate we are dealing with `float64`'s). Also note that we [declared](https://golang.org/ref/spec#Variable_declarations) `imgDuration` outside the [`if` statement](https://golang.org/ref/spec#If_statements) because we need to use it outside the [block](https://golang.org/ref/spec#Blocks).
