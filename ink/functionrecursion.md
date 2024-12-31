---
title: Recursive Functions in Ink
parent: Ink
description: Iteration is not the only way! 
---

{% include ink.html %}

# Recursive Functions in Ink
Learn to be dangerous with Ink function recursion

## Intro 
Loops can be achieved in Ink because Ink supports diverts which are like goto/jump statements. With a label and a divert pointing to that label under specific conditions, we can accomplish basic loops.

```ink
~temp i = 10
- (loop)
{ i >= 0 :
    Countdown:{i}
    ~ i--
    -> loop
}
``` 

Ink can also run loops using function recursion. This is where a function invokes itself in its own code. Typically, recursion in general programming often has performance costs and the Ink Official Guide also attests to Ink having similar costs. However, until it becomes a noticeable problem, it's not worth worrying about. As it is said, premature optimization is the root of all evil.

## Basic Function Recursion
Factorial calculation is a common example of function recursion and it's necessary when computing permutations and combinations. This is what it could look like in Ink with a recursive function. 

```ink
{ factorial(6) / factorial(4) } // outputs: 30 (720/24)

=== function factorial(x)
{ x == 1 :
    ~ return 1
}
~ return x * factorial(x - 1) 
```
Since functions in Ink do not support diverts, trying to accomplish the same effect would mean using Knots which is doable but arguably a little unwieldy to use and difficult to decipher.
```ink
~ temp output = 0
-> knotFactorial(4,  output) ->
{ output } // 24

=== knotFactorial(val, ref output)
~ output = val
- (facloop)
{ val > 1 :
   ~ output = output * (val-1)
   ~ val--
   -> facloop
- else :
   ->->
}
```

As you can see we need to use tunnels and also temporary variables for storing the output calculation. 

There could be other ways of writing factorials as knots but ultimately some problems are better solved as recursion. It can result in cleaner scripts that are easier to read and use. Also, the recursive function in Ink can be a fallback for an identical recursive function declared in the external game engine. External functions will most likely run faster and have less overhead than Ink functions so this can alleviate performance costs, should they arise. 

## Recursion with Ink Lists
When it comes to recursion, the most important thing to consider is that we are solving a problem by evaluating smaller versions of the same problem. It is crucial to be able to break the chain of recursion or we'll recurse till we reach a state of stack overflow. 

In recursion, we can conceptualize a base case and a recursive case; the base case is where the recursion stops because the input or output is too simple to be processed by the function while the recursive case is where the function calls itself and performs the breakdown or simplification to reach the base case.

So if we want to recurse with Ink Lists, we need to consider a base case and a recursive case. 
* The base case would be checking the list count and terminating if it's empty. 
* The recursive case would make the list smaller by popping an item out of it and calling the same function on this simpler list. 

Below is an example of this in action where we simply print out the contents of a list.

```ink
LIST days = Mon,Tue,Wed,Thu,Fri,Sat,Sun
VAR weekdays = (Mon,Tue,Wed,Thu,Fri)
~ print(weekdays)

=== function print(blist)
{ LIST_COUNT(blist) > 0 :
    ~ temp b = LIST_MIN(blist)
    Day: { b }
    ~ return print(blist - b)
}
```

### 🔁Bit Ops with List Recursion
So elsewhere, I mentioned that Ink does not support bitwise operations but actually you can accomplish this with list recursion. After all, what is an Ink list but a boolean set and so using it as a bitset should be somewhat straightforward. Here is a rough implementation.
```ink
LIST bits = b0 = 1, b1 = 2, b2 = 4, b3 = 8, b4 = 16, b5 = 32, b6 = 64
VAR seven = (b2, b1, b0)

{ bits_to_dec(seven) } //  outputs 7
{ dec_to_bits(7, ()) } //  outputs b0, b1, b2

=== function bits_to_dec(blist)
{ LIST_COUNT(blist) > 0 :
~ temp bit = LIST_MIN(blist)
~ return LIST_VALUE(bit) + bits_to_dec(blist - bit)
}
~ return 0

=== function dec_to_bits(num, list)
{ list == () : 
    ~ list = LIST_ALL(bits) 
}
{ num != 0 :
    ~ temp bit = LIST_MIN(list)
    { num % 2 == 0 :
        ~ bit = ()
    }
    ~ return bit + dec_to_bits(num / 2, list - LIST_MIN(list))
}
~ return 0
```

We have two recursive functions here. The first one `bits_to_dec` adds up the values of each bit in the list. To skip some `POW` calculations, I opted to directly assign the powers-of-two directly to each list variable. The recursive case ensures we shrink our list while the base case terminates when our list count is 0.

The second one is a bit more complex, no pun intended. We need to go through all possible bits while also halving the input number during each recursive call. So both values are needed as parameters. We return a collection of list variables that correspond to each bit. 

### 🔁Functions in Recursion
We can pass functions as parameters during recursion which can help us accomplish something akin to functional programming.

```ink
LIST monster = Bulbar,Charmin,Skwittle,Zyduck
LIST monster2 = Venbar,Charzan,Bortle,Holduck 

{ each(->evolve, (Charmin, Skwittle, Zyduck)) } // outputs: Charzan, Bortle, Holduck

=== function evolve(m)
~ return monster2(LIST_VALUE(m))

=== function each(->func, mlist)
{ LIST_COUNT(mlist) > 0 :
~ temp m = LIST_MIN(mlist)
~ return func(m) + each(func, mlist - m)
}
~ return 0
```

Here we have an `each` function which runs a function on each element in a list, then accumulates and returns each output. We can now pass in any function to process a list with. In this case, an `evolve` function swaps monsters from type 1 to type 2. Running it in an `each` will evolve all monsters in a list. 

## Conclusion
Recursive functions in Ink are as interesting as they are dangerous. Because Ink functions can return values and invoke themselves, we open up interesting possibilities, some of which we've seen in this document.
