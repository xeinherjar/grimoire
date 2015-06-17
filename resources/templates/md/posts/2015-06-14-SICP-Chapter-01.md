{:title "Alternative ways to think about programming"
 :layout :post
 :tags ["lisp" "SICP"]}

I've spent a lot of time trying to think of what I want to learn next.  The Iron Yard was great at getting me up to speed with JavaScript and I got to build some neat things.  My day job keeps me busy in JavaScript land, but there is more to life than JavaScript and I'm one of those people that loves abstractions but I need to know what they are abstracting.

I have a strong interest in computer science type things.  While I lack the time and money to go to school to get a CS degree, there are many free resources I can use to help me.

I've had a few ideas, the first was to grab a book about data structures and algorithms, but I've a serious curiosity about Lisps.  Enter SICP.

## SICP

[SICP](https://mitpress.mit.edu/sicp/) was the book MIT used to teach programming.  It was written in 1984 but according to the internet it still holds up.  It aims to teach the principles of computer programming.

Chapter One covers things like this...

### Applicative Order Evaluation vs. Normal Order Evaluation

Ever wondered how your programing language evaluates its arguments in function calls? [Wikipedia: Evaluation Strategy](https://en.wikipedia.org/wiki/Evaluation_strategy)

One of the first exercises [1.5] they give you helps you to really understand how these two differentiate.  The question they ask is

Given:

```
(define (p) (p))

(define (test x y)
  (if (= x 0)
      0
      y))

(test 0 (p))
```

What happens when its evaluated using Applicative Order vs Normal Order?

The answer:

Applicative Order Evaluation will loop forever.  It will try to reduce all arguments, even if they aren't needed.  From what I gather since `(p)` is defined as `(p)` it will call itself forever.  Yay recursion!

Normal Order Evaluation on the other hand only evaluates/reduces its arguments to values as they are needed.  To me this reads similar to boolean operators that short circuit.  The special form `if` will eval `(= x 0)` which reduces to `(= 0 0)` which is `true` so it doesn't need to reduce `(p)` to its value.

This explains why `if` need to be a special form?  Or in the larger context, why there needs to be special forms.

Exercise 1.6 demonstrates it well.

They define `new-if` with `cond` and ask what happens when you call a recursive function that uses an `if` form as the guard.

`new-if` will work just fine with something like
```
(new-if (= 1 1) 0 5)
```
since `5` is already a value, there is no need to reduce it to a value.  But if you have a recursive function and you use `if` as the guard to return, well ...

The default evaluation strategy for `cond` uses Applicative Order Evaluation, what makes `if` special is that it used Normal Order Evaluation and as demonstrated above can short circuit.  Since `if` is used as the guard to break from a recursive function you must have an evaluation strategy that can break early, otherwise you will be in a infinite loop or possibly blow the stack.

And that is just section one of chapter one.  I'm excited to continue with this book.
