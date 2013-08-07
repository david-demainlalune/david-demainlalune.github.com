---
layout: post
title: "fnth is beautiful"
date: 2012-11-27 14:50
comments: true
categories: 
---

I have encountered a beautiful species of functional composition in my travels in the lands of clojure. My guide book, the excellent [joy of clojure](http://joyofclojure.com/), named it fnth.

``` clojure

; cf the joy of clojure -> p.127

(def fnth [n]
     (apply comp
     	    (cons first
	    	  (take (dec n) (repeat rest)))))

```

This function returns a function that returns the value of the nth element of a collection.
For instance, let's find the second element of the vector [1 2 3]:


``` clojure

((fnth 2) [1 2 3])  

=> 2


```

We should note that Clojure has plenty of ways of getting elements out of vectors. Fnth is just an excuse to enjoy the functional nature of the language.

Let's decompose fnth to admire its inner beauty.

First (sic), we must know of "first" and "rest". "first" gives us the first element of a list. "rest" gives us the rest of the list, generally speaking another list (I happily ignore some clojure specifics here). These essential commands allow us to access the second element of our vector in the following fashion:

``` clojure

(first (rest [1 2 3]))

; inner form
(rest [1 2 3])
=> [2 3]

; outer form
(first [2 3])
=> 2


```

Let's now look at the innermost form of fnth -> (take (dec n) (repeat rest))

(dec n) is easy. "dec" returns the value of its argument minus one.

(repeat rest) returns an infinite list of "rest" functions. Functions are first class citizens: just like any value they can be stored in a list. "repeat" just gives us an unending stream of whatever its argument is.
For instance:


``` clojure

(take 3 (repeat 5))
=> (5 5 5)


```

We can't really consume an infinite list, so we limit ourselves and "take" only what we need.
Back to the inner form of fnth, assuming n = 2:

``` clojure

; for n = 2

(take 1 (repeat rest))
=> (list rest)


```

This gives us a list of (n-1) rest functions.

The next step (cons first ...) appends the "first" function to the list.
we now have a list with "first" and (n-1) "rests".

``` clojure

; for n = 2
(apply comp (list first rest))

; for n = 3
(apply comp (list first rest rest))

; etc...

```

"apply" takes a function and a list. It extracts the elements of the list and feeds them as arguments to the function.

``` clojure

(apply + [1 2 3])
=> 6

```

comp takes a set of functions and returns a composed function. Function composition generally helps readability. An example:

``` clojure

(first (rest (rest [1 2 3])))

; with comp the intent is clearer
((comp first rest rest) [1 2 3])


```

We now have all the elements to deconstruct fnth.

``` clojure

; we assume n = 2

(def fnth[2]
     (apply comp
     	    (cons first
	    	  (take (dec 2) (repeat rest)))))

; simplify
(apply comp
       (cons first
       	     (take 1 (repeat rest))))

; simplify
(apply comp (cons first
       	    	  (list rest)))

; simplify
(apply comp (list first rest))

; done 
(comp first rest)

; apply to arguments for test

((comp first rest) [1 2 3])
=> 2


```

One may argue that the example is maybe a bit silly in terms of everyday use. Nevertheless I find it's a beautiful example of functional elegance.