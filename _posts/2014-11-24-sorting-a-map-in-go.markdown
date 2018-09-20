---
layout: post
date: 2014-11-24 12:15:00 GMT
title: "Sorting a map in Go"
---
I wanted to sort a bunch of guesses I made (I had scores for each of them). There doesn't seem to be an easy way to retrieve the keys for a map in Go,
so I ended up adding them to a slice while I was creating the map.

        guesses := make(map[float64]int) 
        mapKeys := make([]float64,38)
        for i := 2; i < 40; i++ {
                n := normalizedEditDistance(s[0:i],s[i:2*i]) 
                guesses[n] = i   // the "guess" is that i is the length I want
                mapKeys[i-2] = n
        }

        sort.Float64s(mapKeys)

        sortedGuesses := make([]int,38)
        for i, mapKey := range mapKeys {
                sortedGuesses[i] = guesses[mapKey]
        }

I sorted the keys (the scores for each guess) and used another slice to store the guesses (the values in the map). This will all go terribly wrong if there is more than one guess with the same score. 