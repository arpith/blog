---
layout: post
date: 2015-12-01 16:47:04 GMT
tags:
- algorithms
- java
- bijection
- math
- programming
- topcoder
- hashmap
- srm
- competition
- solution
title: "Bijections in Java"
---
<p>The <a href="https://community.topcoder.com/stat?c=problem_statement&amp;pm=14008&amp;rd=16624&amp;rm=327591&amp;cr=22670055">first problem</a> in today’s <a href="http://topcoder.com/">TopCoder</a> Single Round Match was on&nbsp;<a href="https://en.wikipedia.org/wiki/Bijection">bijections</a>:</p><figure data-orig-width="800" data-orig-height="600" class="tmblr-full"><img src="/images/d8cea84bbcfa8c5c7e69b5fc30e433f54128cbb390914d4a3f68cf1ac6b2f13d.jpg" data-orig-width="800" data-orig-height="600"></figure><p><br></p><blockquote>Formally, a relation is a set of ordered pairs of elements. The teacher gave Weiwei one such relation. You are also given a description of this relation: int[]sdomain and range. For each valid i, the relation contains the ordered pair (domain[i],range[i]).</blockquote><blockquote>Let X be the set of elements that appear at least once in domain. Similarly, let Y be the set of elements that appear at least once in range. We say that an element x of X is paired to an element y of Y if the relation contains the ordered pair (x, y).</blockquote><blockquote>We will say that our relation is a bijection if each element of X is paired to exactly one element of Y, and each element of Y is paired to exactly one element of X.</blockquote><p>That is, given a list of pairs, determine whether an element appears in more than one pair (there are no identical pairs). We can use <a href="https://docs.oracle.com/javase/7/docs/api/java/util/HashMap.html">Java’s HashMap</a> to store these pairs, and when we’re adding a pair we can check if either of the elements has already been added in another pair.</p><pre>import java.util.*;<br> <br>public class RelationClassifier {<br> &nbsp;public String isBijection(int[] domain, int[] range) {<br> &nbsp; &nbsp;HashMap&lt;Integer, Integer&gt; relations = new HashMap&lt;Integer, Integer&gt;();<br> &nbsp; &nbsp;for (int i=0; i&lt;domain.length; i++) {<br> &nbsp; &nbsp; &nbsp;if (relations.containsKey(domain[i])) return "Not";<br> &nbsp; &nbsp; &nbsp;else if (relations.containsValue(range[i])) return "Not";<br> &nbsp; &nbsp; &nbsp;else relations.put(domain[i], range[i]);<br> &nbsp; &nbsp;}<br> &nbsp; &nbsp;return "Bijection";<br> &nbsp;}<br>}</pre><p><a href="https://community.topcoder.com/stat?c=problem_statement&amp;pm=14007&amp;rd=16624&amp;rm=327591&amp;cr=22670055">The second problem</a> looked quite interesting, but I couldn’t get a solution in time. You’re playing a game where you have one shot that will eliminate any points (on a plane) that lie on the x and y-axes . Before you take the shot, though, you’re allowed to rotate all the points (together) and move them together in any direction — any number of times. The aim is to eliminate as many points as possible.</p><p>Have any ideas or thoughts? <a href="http://twitter.com/arpith">Let</a> <a href="http://medium.com/@arpith">me</a> <a href="http://arpith.co/">know</a>!</p>