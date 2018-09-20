---
layout: post
date: 2014-11-26 04:47:34 GMT
title: "Go's strings.Contains is useful"
---
I wanted to check if a block of bytes repeated itself in a slice so I converted it to a string and looked at each block, checking if it was contained in the slice leading up to it or the slice beyond it.

        blocksize := 16
        for i := 0; i < len(ciphertext); i = i + blocksize {
                block := ciphertext[i:i+blocksize]
                previous := ciphertext[0:i]
                next := ciphertext[i+blocksize:]
                if strings.Contains(previous,block) || strings.Contains(next,block) {
                        s := hex.EncodeToString([]byte(block))
                        fmt.Printf("The block is: %s\n",s)
                        return true
                }
        }
        return false

Will something go wrong when converting from `[]byte` to `string` and back?