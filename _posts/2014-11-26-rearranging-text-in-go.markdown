---
layout: post
date: 2014-11-26 04:15:48 GMT
title: "Rearranging text in Go"
---
<p>I wanted to split a string into blocks, and then look at the first bytes of each block, then the second bytes, and so on. I ended up using a two dimensional slice of bytes and filled it by running through the original text once:</p>

<pre><code>
transposed := make([][]byte, blocksize)
for i, b := range text {
    if i &amp;lt; blocksize {
        transposed[i] = make([]byte,textlength/blocksize + 1)
    }
    transposed[i % blocksize][i / blocksize] = b
}
</code></pre>

<p>Note that my slices are <code>textlength / blocksize + 1</code> long because there will be one extra byte in some of the slices (the remainder when the text is divided into blocks will be split up with the first byte going to the first slice, the second into the second slice, etc.)</p>