---
layout: post
date: 2010-05-16 10:21:00 GMT
title: "Pointer Subtraction"
---
# [Pointer Subtraction](http://blog.sigfpe.com/2010/05/optimising-pointer-subtraction-with-2.html)

<blockquote><p>It doesn't seem like there could be much to say about that. The A  structure is 7 bytes long so the subtraction implicitly divides by 7. That's about it. But take a look at the assembly language generated when it's compiled with gcc:</p>
<pre><code>
movl 4(%esp), %eax
subl 8(%esp), %eax
imull $-1227133513, %eax, %eax
ret
</code></pre>

<p>Where is the division by 7? Instead we see multiplication by -1227133513.</p>
</blockquote>