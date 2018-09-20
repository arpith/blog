---
layout: post
date: 2015-11-18 17:45:13 GMT
tags:
- algorithms
- java
- topcoder
- competition
title: "Limak the Bear Meets Java"
---
Today’s Topcoder Single Round Match (673) featured Limak the Bear in these questions:

> Bear Limak has recently learned about musical notes. He then listened to a song and noticed that some notes appeared less often than others. In fact, some notes were so rare that they appeared in the song only once!
> Limak now wants to look for such notes in other songs. Write a program that will look for the rare notes.
> You are given a int[] notes that describes a song. Each number in notes represents one note of the song. Different numbers represent different notes, equal numbers represent equal notes.
> Compute and return the number of notes that occur exactly once in the given song.

Fortunately, [Java’s Set](http://docs.oracle.com/javase/7/docs/api/java/util/Set.html) can do the work for us. My initial (incorrect) solution was:

    import java.util.*;
    public class BearSong {
        public int countRareNotes(int[] notes) {
            Set<integer> rareNotes = new HashSet<integer>();
            for (int note: notes) {
                if (rareNotes.contains(note)) rareNotes.remove(note);
                else rareNotes.add(note);
            }
            return rareNotes.size(); 
        }
    }
    
I found out the hard way that this fails on simple inputs like [2,2,2]. It’s probably better to keep track of repeated notes (in addition to the notes have seen so far):

    import java.util.*;
    public class BearSong {
        public int countRareNotes(int[] notes) {
            Set<Integer> allNotes = new HashSet<Integer>();
            Set<Integer> repeatedNotes = new HashSet<Integer>();
            for (int note: notes) {
                if (allNotes.contains(note)) repeatedNotes.add(note);
                else allNotes.add(note);
            }
            return allNotes.size() - repeatedNotes.size(); 
        }
    }
    
Spot anything wrong? Let me know!

The next question had Limak sorting a sequence of numbers:

> Limak is a little polar bear. He has a int[] w containing a sequence of N distinct numbers. He wants to sort this sequence into ascending order.
> Limak knows some fast sorting algorithms but in the real world such knowledge sometimes isn’t enough. In order to sort the sequence w Limak must physically move the numbers into their correct places. Such a thing can be hard for a little bear.
> In a single move Limak can take all elements he can reach and sort them into ascending order. The problem is that Limak’s arms are too short. Regardless of where he stands, he can only reach N-1 consecutive elements of w. Hence, in each move he can either sort all elements except for the last one, or all elements except for the first one.
> Limak can make the moves in any order he likes. Compute and return the smallest number of moves necessary to sort the given sequence w.


I guessed that any sequence can be sorted in three moves, because if the first element is the minimum (or the last element is the maximum) you can exclude it and sort the rest in one move. If the minimum element is last in the list, it’ll take you two moves to get it to the front of the list, and then another move if the original first element was the maximum.

    import java.util.*;
    public class BearSlowlySorts {
        public int minMoves(int[] w) {
            int count = 0;
            ArrayList<integer> l = new ArrayList<integer>();
            for (int i: w) {
                l.add(new Integer(i));
            }
            ArrayList<integer> s = new ArrayList<integer>(l);
            Collections.sort(s);
            int first = l.get(0);
            int last = l.get(l.size() -1);
            int max = Collections.max(l);
            int min = Collections.min(l);
            if (l.equals(s)) return 0;
            else if ((first == min) || (last == max)) return 1;
            else if ((first == max) && (last == min)) return 3;
            else return 2;
        }
    }
    
For fun, here are some sequences that I played with:

Single move:

14352

1. 12345 (after sorting the last four)

32145

1. 12345 (after sorting the first four)

Three moves:

52341

1. 51234 (after sorting the last four)
2. 12354 (after sorting the first four)
3. 12345 (after sorting the last four)

Was my guess right? We’ll find out tomorrow, when the results are out. </integer></integer></integer></integer></integer></integer>

Edit: Looks like I was right!