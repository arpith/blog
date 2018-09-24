---
layout: post
date: 2016-01-13 17:58:45 GMT
tags:
- java
- algorithms
- programming
- competition
- star wars
- obi wan kenobi
title: "Obi-Wan meets Java"
---
# Obi-Wan meets Java

<p>Today’s <a href="http://topcoder.com/">Topcoder</a> Single Round Match was Star Wars themed. Here are two problems that Obi-Wan featured in.</p><h2>Obi-Wan and the Droids</h2><p>The <a href="https://community.topcoder.com/stat?c=problem_statement&amp;pm=14115&amp;rd=16648&amp;rm=327813&amp;cr=22670055">first problem</a> was that Obi-Wan is in a building guarded by droids and wishes to choose the safest exit. All the doors are along the same wall, and you are given the coordinates of the doors (distances from the corner) and of the droids along the wall outside (these coordinates are also distances from the corner). The safety of an exit is the distance between the door and the nearest droid.</p><p>This is the solution I came up with:</p><pre>import java.util.Arrays;<br>public class ThePhantomMenace {<br> &nbsp;public int find(int[] doors, int[] droids) {<br> &nbsp; &nbsp;Arrays.sort(doors);<br> &nbsp; &nbsp;Arrays.sort(droids);<br> &nbsp; &nbsp;int maxSafety = 0;<br> &nbsp; &nbsp;for (int door : doors) {<br> &nbsp; &nbsp; &nbsp;int safety = 0;<br> &nbsp; &nbsp; &nbsp;for (int i = 0; i &lt; droids.length; i++) {<br> &nbsp; &nbsp; &nbsp; &nbsp;if (droids[i] &gt;= door) {<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;if (i == 0) safety = droids[i] — door;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;else safety = Math.min(droids[i] — door, door — droids[i-1]);<br> <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;if (safety &gt; maxSafety) maxSafety = safety;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;break;<br> &nbsp; &nbsp; &nbsp; &nbsp;}<br> &nbsp; &nbsp; &nbsp;}<br> &nbsp; &nbsp;}<br> &nbsp; &nbsp;return maxSafety;<br> &nbsp;}<br>}</pre><p>This fails (as I found out the hard way) if there is only one door, at coordinate 100, and one droid, at coordinate 0. Basically, what do you do if there is no droid to the right of the door?</p><h2>Obi-Wan’s laundry</h2><p>The <a href="https://community.topcoder.com/stat?c=problem_statement&amp;pm=13586&amp;rd=16648">second problem</a> was that Obi-Wan is on a mission and is carrying N shirts. After he wears a shirt, it takes N-2 days to wash and dry it before he can wear it again.</p><p>If you’re given the order in which he wore his shirts on the first week of the mission (the week here has N days) and the order on the last week, can you figure out how short (in weeks) the mission could have been?</p><p>My solution was:</p><pre>public class AttackOfTheClones {<br> &nbsp;public int count(int[] firstWeek, int[] lastWeek) {<br> &nbsp; &nbsp;int totalTime = 0;<br> &nbsp; &nbsp;for (int x = 0; x &lt; firstWeek.length; x++) {<br> &nbsp; &nbsp; &nbsp;for (int y = 0; y &lt; lastWeek.length; y++) {<br> &nbsp; &nbsp; &nbsp; &nbsp;if (firstWeek[x] == lastWeek[y]) {<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;int time = 0;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;if (x &gt; y) {<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;time = x — y + 1;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;} else if (x &lt; y) {<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;time = 1;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;} else {<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;time = 0;<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;}<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;if (time &gt; totalTime) totalTime = time; <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;break;<br> &nbsp; &nbsp; &nbsp; &nbsp;}<br> &nbsp; &nbsp; &nbsp;}<br> &nbsp; &nbsp;}<br> &nbsp; &nbsp;if (totalTime == 0) return 1;<br> &nbsp; &nbsp;else return totalTime;<br> &nbsp;}<br>}</pre><p>Why does this work? It basically takes a week to move a shirt from day X to day X-1, or x-y weeks to move a shirt from day x to day y (if y is smaller than x). You have to count the first week too, so the mission would have taken x-y+1 weeks.</p><p>It will take one week to move a shirt from day X to a later day in the week (say X+n). The question that remains is — why don’t these weeks add up?</p><p><a href="http://twitter.com/arpith">Let me know</a> your thoughts!</p>