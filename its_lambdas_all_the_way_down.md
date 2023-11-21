---
layout: page
title: It’s Lambdas All the Way Down
---

# It’s Lambdas All the Way Down

**My journey in the land of Lisp has just started. I think there’s still a long road to “*[enlightenment](https://twobithistory.org/2018/10/14/lisp.html)*”, but the following “*revelations*” have already blown my mind…**

---

## First Revelation: Lisp is like the Universe

A few years ago, I read Paul Graham’s [The Roots of Lisp](https://www.paulgraham.com/rootsoflisp.html) article. In it, he showed how one could start with a set of programming ********************primitives******************** and build any function based on them, leading to a full-fledged programming language. He wrote his text in PostScript format which I’ve converted to PDF below:

[jmc.pdf](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ba0c8f67-0299-4a29-828c-875b711eae38/jmc.pdf)

PG showed that only the following primitives are needed to create a programming language (I’m intentionally changing the Scheme/Common Lisp syntax by adding a Haskell-esque syntax):

```scheme
1. QUOTE
quote :: Any → Any
(quote x) ; often abbreviated as 'x

2. CONS
cons :: (Any, List) → List
(cons x y)

3. ATOM
atom? :: Any → Bool
(atom? x)

4. CAR
car :: List[Any] → Any
(car x)

5. CDR
cdr :: List[Any] → List[Any]
(cdr x)

6. COND
cond :: (Bool, Any), ... → Any
(cond (p1 e1) ... (pn en))

7. EQ?
eq? :: (Any, Any) → Bool
(eq? x y)
```

- #1 and #2 allow us to create data (either by creating new data via `'x` or by appending data to old data using `cons`).

We also need a way to define functions (so I guess we can count them as yet another primitive feature) using lambda expressions, and denote them with labels:

```scheme
lambda :: Any, ... → Any
(lambda (x1 x2 ... xn)
				(<something>))

; label notation
(label f (lambda (x) (cons x '(5))))
```

> This was my first encounter with ****************lambdas****************, but more was about to come as you’ll see in the rest of this post.
> 

PG provided the following function definitions based on the above primitives (it is legit Common Lisp code, you can try it out in [SBCL](https://www.sbcl.org/)).

- He made an error in the code: `evcon.` and `evlis.` should come before `eval.`.

```lisp
; The Lisp defined in McCarthy's 1960 paper, translated into CL.
; Assumes only quote, atom, eq, cons, car, cdr, cond.
; Bug reports to lispcode@paulgraham.com.

(defun null. (x)
  (eq x '()))

(defun and. (x y)
  (cond (x (cond (y 't) ('t '())))
        ('t '())))

(defun not. (x) 
  (cond (x '())
        ('t 't)))

(defun append. (x y)
  (cond ((null. x) y)
        ('t (cons (car x) (append. (cdr x) y)))))

(defun list. (x y)
  (cons x (cons y '())))

(defun pair. (x y)
  (cond ((and. (null. x) (null. y)) '())
        ((and. (not. (atom x)) (not. (atom y)))
         (cons (list. (car x) (car y))
               (pair. (cdr x) (cdr y))))))

(defun assoc. (x y)
  (cond ((eq (caar y) x) (cadar y))
        ('t (assoc. x (cdr y)))))

(defun evcon. (c a)
  (cond ((eval. (caar c) a)
         (eval. (cadar c) a))
        ('t (evcon. (cdr c) a))))

(defun evlis. (m a)
  (cond ((null. m) '())
        ('t (cons (eval.  (car m) a)
                  (evlis. (cdr m) a)))))

**(defun eval. (e a)
  (cond
    ((atom e) (assoc. e a))
    ((atom (car e))
     (cond
       ((eq (car e) 'quote) (cadr e))
       ((eq (car e) 'atom)  (atom   (eval. (cadr e) a)))
       ((eq (car e) 'eq)    (eq     (eval. (cadr e) a)
                                    (eval. (caddr e) a)))
       ((eq (car e) 'car)   (car    (eval. (cadr e) a)))
       ((eq (car e) 'cdr)   (cdr    (eval. (cadr e) a)))
       ((eq (car e) 'cons)  (cons   (eval. (cadr e) a)
                                    (eval. (caddr e) a)))
       ((eq (car e) 'cond)  (evcon. (cdr e) a))
       ('t (eval. (cons (assoc. (car e) a)
                        (cdr e))
                  a))))
    ((eq (caar e) 'label)
     (eval. (cons (caddar e) (cdr e))
            (cons (list. (cadar e) (car e)) a)))
    ((eq (caar e) 'lambda)
     (eval. (caddar e)
            (append. (pair. (cadar e) (evlis. (cdr e) a))
                     a)))))**
```

I recommend reading his article for more information. The last function defined is `eval.` which evaluates valid Lisp code ******inside****** Lisp! This meta aspect of Lisp was also mentioned in the SICP course by referring to the following Escher drawing—as if `eval` and `apply` write each other:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3cf663a6-85dc-4c8c-acc5-9edb315924b1/Untitled.png)

- William Byrd talks about this “Lisp in Lisp” code during his amusing talk:

https://www.youtube.com/watch?v=OyfBQmvr2Hc

The fact that you can start with a few building blocks and make a whole programming language that allows you to run any computation there ever will be is in itself mind blowing. But did McCarthy ******design****** those primitives, or as PG puts it, he **********discovered********** them? As it turns out, those building blocks could themselves be broken down into smaller pieces. Think of it like atoms in the universe that can be broken into sub-atomic particles. Surprisingly, these particles were found decades ago by [Alonzo Church](https://en.wikipedia.org/wiki/Alonzo_Church), the inventor of [Lambda calculus](https://en.wikipedia.org/wiki/Lambda_calculus#:~:text=Lambda%20calculus%20(also%20written%20as,to%20simulate%20any%20Turing%20machine.) long before electronic computers even existed!!

## Second Revelation: Lisp is a way of thinking

Okay great, so Lisp can be written in itself. So what? I actually [asked this question on HN](https://news.ycombinator.com/item?id=37124846) and got some good answers. I was already impressed by this metacircular aspect of Lisp when I stumbled upon this article:

[Turns out, fancy programming features can be broken down into simpler pieces.](https://stopa.io/post/250)

Turns out, fancy programming features can be broken down into simpler pieces.

I spent years thinking classes and functions are separate things, but equipped with the “Lispy” way of thinking—everything can ultimately be broken into primitives—I found Stepan’s argument interesting. This way of thinking makes a lot of sense and simplifies programming to a large extent. People say Lisp doesn’t have syntax because it’s just `(operator operand)` nested inside each other, making more complex structures. I’m starting to like this approach. It’s ***********predictable*********** as in you know exactly what should go where.

Interestingly, there was a comment on this article on HN as follows:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/26931f27-4f7f-4c47-95fc-9426d6616246/Untitled.png)

I was like: “Are you actually telling me that even some of the Lisp primitives such as `car, cdr, cons` are in fact made out of simple lambda definitions?!

## Third Revelation: Lambdas, Lambdas everywhere!

At this point, I was hooked, I needed to find out the answer. The [StackOverflow link in the picture](https://stackoverflow.com/a/21769444) beautifully explained this concept:

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/67200db3-7022-47aa-9e75-90daecf24402/Untitled.png)

It took me several hours to fully understand what was going on here. First off, we assume there’s no such thing as “lists” in our language—only functions and definitions. If we want to get the head or tail of a list of items, then, typically we’d need to pass them all as arguments to `car` and `cdr` functions which might have definitions like below:

```scheme
(define (car x y)
	(lambda (x y) (x)))

(define (cdr x y)
	(lambda (x y) (y)))

(car 1 2)
; 1

(cdr 1 2)
; 2
```

But what if we didn’t want to write out the arguments each time? If we were able to pass something called `l` which stores our data (i.e., `1, 2`), then things would be much simpler as we’d only have to do `(car l)` and `(cdr l)`. Since we don’t have any data structure in our language, though, we need to improvise. What if we store our items as arguments of a function? It doesn’t really matter what that function is:

```scheme
(define (l)
	(lambda (m) (m 1 2))
```

Now we can rewrite the definitions of `car` and `cdr` as follows:

```scheme
(define (car l)
	(...))

(define (cdr l)
	(...))
```

Wait! How do we *******get inside******* `l` and retrieve its arguments? There’s really not much we can do with `l`. We:

1. can either give it a function (`m`) and it applies it to our data, or
2. we pass `l` as the argument of another function and nothing happens.

Let’s try the first one. What function should we pass as `m` to our data function `l` to retrieve its first element? Well, how about this:

```scheme
(lambda (x y) x)) ; a function that returns its first argument
```

Given to `l`, this will produce:

```scheme
(define (l)
	(lambda (m) (m 1 2))

(l (lambda (x y) (x)))
↓
((lambda (x y) (x)) (1 2))
; 1
```

Tada! It worked. Now let’s generalize this solution to any data, not just `1, 2`. To do that, we just need to turn `l` into a function that receives `x, y` (it already is a function which receives a function `m` as input—we often call these functions ****************closures****************):

```scheme
(define (cons x y)
	(lambda (m) (m x y)))
```

which we can now use with our definitions of `car` and `cdr`:

```scheme
**(define (cons x y)
	(lambda (m) (m x y)))

(define (car foo)
	(foo (lambda (x y) (x)))

(define (cdr foo)
	(foo (lambda (x y) (y))))**
```

The old saying about Lisp is ******************************************************************************code is data, data is code******************************************************************************. What we see here is deeper than that:

> ******************************Data is Lambda (”open closure”).
Code is Lambda (”closed closure”).******************************
> 
- If you’re not familiar with closures, please do yourself a favor and check out these links because closures are one of the tricky programming concepts and unfortunately everyone defines them differently. The definition in these links is close to the formal Lambda calculus definition:
    - https://stackoverflow.com/a/36879264
    - https://stackoverflow.com/a/36878651

## Forth Revelation: Numbers are lambdas too!

Stepan’s articles generally blow my mind and make me rethink some of the concepts I’ve learnt throughout the years. His following article answers one of my curiosities about numbers. Have you ever asked yourself: “What are numbers anyway?” In other words, beyond the *******symbols******* we assign to them, what constitutes a numbers? Can they, too, be broken into primitives? 4-5 years ago I thought so. I thought that you should be able to start with any number and build any other number through **********************computation**********************. If you give me “0”, I will get to “10” one day; it’s just a matter of time. Stepan uses the formal language of Lambda calculus to demonstrate this:

https://stopa.io/post/263

So as it turns out, even *******numbers******* can be defined in terms of… you guessed it, **************lambdas**************! For example, number “4” is nothing but something like `(S(S(S(S(0)))))`, that is, we start with nothing (0) and depending on how many time we apply `S`, we get higher numbers ([See these comments](https://news.ycombinator.com/item?id=24425408) too).

## Where do we go from here?

I’m more interested in the foundations of Lisp and Lambda calculus, not the nitty-gritty details of  advanced Lisp programs. I know that due to my work, I’ll still use Python for most projects, but these revelations have changed the way I think about my Python code, too.

The idea is ***not*** to go ahead and write numbers using lambdas or re-define core language primitives such as `car` in terms of lambdas—I will still keep using `l[0]` and `l[1:]` in Python (the equivalents of `car` and `cdr` in Lisp, respectively). But something has definitely changed in the way I think about programming. Functions, higher-order functions, closures, lambdas, etc. These are all terms that you’d hear from the ********************************************functional programming******************************************** folks. My little experience with Haskell showed me the elegance of this style of programming (not that imperative programming is wrong). There’s just a sense of beauty in writing “stateless” code (using Lambda calculus and FP) as opposed to code that modifies state. They both work and achieve the goal, but one also gives you some joy as you’re writing it. 😊

---

**Disclaimer:** I don’t have a classic CS background. My B.Sc. was in Electrical Engineering, my M.Sc. in Operations Management, and my Ph.D. (ongoing) in Quantitative Marketing and Management Science. But I’ve always been fascinated by computers and their history. If you have a comment about this article or you think it can be improved in some way, please let me know!

[Contact](https://www.notion.so/Contact-e624f18382ca457fa801fd66716aa8f2?pvs=21)