---
layout: post
date: 2010-05-30 10:57:00 GMT
title: "Fisher-Yates Shuffle Algorithm"
---
# [Fisher-Yates Shuffle Algorithm](http://eli.thegreenplace.net/2010/05/28/the-intuition-behind-fisher-yates-shuffling)

<blockquote>In particular, the most common naive algorithm that comes up is [1]:

<pre><code>naive_shuffle(arr):
  if len(arr) > 1:
    for i in 0 .. len(arr) - 1:
      s = random from inclusive range [0:len(arr)-1]
      swap arr[s] with arr[i]
</code></pre>

This algorithm produces results that are badly skewed. For more information consult <a href=http://www.codinghorror.com/blog/2007/12/the-danger-of-naivete.html>this post by Jeff Attwood</a>, and <a href=http://stackoverflow.com/questions/859253/why-does-this-simple-shuffle-algorithm-produce-biased-results-what-is-a-simple%20%282nd%20answer%29>this SO discussion</a>.

The correct answer is to use the Fisher-Yates shuffle algorithm:

<pre><code>fisher_yates_shuffle(arr):
  if len(arr) > 1:
    i = len(arr) - 1
    while i > 0:
      s = random from inclusive range [0:i]
      swap arr[s] with arr[i]
      i--
</code></pre>
</blockquote>

Rewritten,
<pre><code>non_naive_shuffle(arr):
  if len(arr) > 1:
    for i in 0 .. len(arr) - 1:
      s = random from inclusive range [i:len(arr)-1]
      swap arr[s] with arr[i]
</code></pre>
