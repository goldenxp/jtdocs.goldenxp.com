---
title: Ink Numeric Input
parent: Ink
description: How to allow numeric input in Ink
---

{% include ink.html %}

# Ink Numeric Input
Learn how to setup user input for numbers in Ink

## Intro
The motivation for this document is as interesting as it is subjective. Some exposition is necessary.


To me, there's an interesting conflict nested in the Ink paradigm. The focus of Ink is writing not programming. It's a middleware so you typically want data processing to happen in your game engine, where it'll (hopefully) be easier to program/debug and also faster to calculate. These data values can then be used in Ink after processing. The problem with this is that you can no longer accurately run your Ink story in Inky (or equivalent tools) and are reliant on the game engine for data evaluation. Ink is now coupled with the game engine. With some extra scripting, it's possible to make the coupling looser but ultimately, there's still a coupling; the Ink story needs the game engine for accurate reading or writing.


In some production environments, this could mean that your writers now need access to the game engine environment, which could mean paying for new licenses or even new equipment. There are also steep learning curves and engine build stability to contend with. It's an unnecessary hurdle for writing in Ink. To me, this says that you want your Ink projects to be able to run in Inky as much as possible. Your Ink story needs to work outside of your game engine and that means you actually want to do as much programming as possible in Ink itself! This is the conflict of "coding" in Ink. 


There are other advantages to making your Ink story rely mainly on internal code and not external code. 
- If you need to switch game engines for any reason, you don't need to rewrite any logic. Your story would still work and all your test cases maintain validity. 
- Besides Inky/Inklecate, you can take advantage of web tools such as [Graphink](https://yannick-lohse.fr/graphink/) and [Inky Doc](https://www.inky-doc.com/home). This means you can work on Ink projects on any device that supports modern web browsers. 
- Ink files can act as a [Single Source of Truth](https://en.wikipedia.org/wiki/Single_source_of_truth). In reality, SSOT is very hard to achieve in game development but it's worth striving for. 

So what does all this have to do with numeric inputs? 

Well...I was trying to port the original [BASIC version of Oregon Trail](https://github.com/scottmcclenning/oregon-trail/blob/master/OREGON.bas) to Ink with no intention of integrating it into Unity or any other game engine. I just wanted to make Oregon Trail in pure Ink. But in Oregon Trail, the player uses the console to input numbers for buying items among other things. And the only user input in Ink is the choice (aka option) mechanic. Now it is possible to use this mechanic to allow numeric input. It's a bit janky but hey it works™️. 

## Scripting Digits in Ink
The basic idea is that to support numeric input we need to afford at least 10 choices, one for each digit from 0 to 9. This is quite easy to do.
```ink
-> choose_digit
=== choose_digit
VAR choice = 0
+ [1] 
  ~ choice = 1
+ [2] 
  ~ choice = 2
+ [3] 
  ~ choice = 3
+ [4] 
  ~ choice = 4 
+ [5] 
  ~ choice = 5 
+ [6] 
  ~ choice = 6 
+ [7] 
  ~ choice = 7 
+ [8]
  ~ choice = 8 
+ [9]
  ~ choice = 9
+ [0]
  ~ choice = 0
- >> Chose: {choice}
-> choose_digit
```

The above is an infinite loop of letting us pick digits but it does nothing with them. In order to concatenate them into a single number, we can do a few things. Let's first take a look at what I did for Oregon Trail.

Oregon Trail did not really use numbers that were greater than 1000 so I was able to take some shortcuts by assuming we could only input numbers with 3 digits. 
```ink
=== pick_number(ref num)
~ num = 0
Choose Digit in the Hundredth's place
-> choose_digit(num, 3) ->
Choose Digit in the Tenth's place
-> choose_digit(num, 2) ->
Choose Digit in the Unit's place
-> choose_digit(num, 1) ->
->->

=== choose_digit(ref num, digit)
VAR choice = 0
// + [1] etc etc 
// i.e. all the numeric options here
- >> Chose: {choice} <>
  ~ num += choice * POW(10, digit - 1)
  , Value: {num}
->->
```
In the above, if we chose `9`, `6` and `3`, we would compute `(9 * 100) + (6 * 10) + (3 * 1)` to make `963`. It could be simplified even further by skipping the Power operations and just using 100, 10 and 1 as the second function argument. Note that references are used to ensure that variables could be funneled into tunnels so they could be altered. Also a glue `<>` is used to calculate num before displaying it side-by-side with the choice value. 

But what if we couldn't control the digit count? This means we would need to support digit input much like a calculator where each digit gets shifted to the left when a new one is chosen. We could use string concatentation to accomplish this. The code below is similar but we have a new choice for concluding the input (RETURN). 

```ink
VAR num = 0

-> choose_digit_string
=== choose_digit_string
VAR choice = ""
+ [1] 
  ~ choice += "1"
+ [2] 
  ~ choice += "2"
+ [3] 
  ~ choice += "3"
+ [4] 
  ~ choice += "4"
+ [5] 
  ~ choice += "5" 
+ [6] 
  ~ choice += "6" 
+ [7] 
  ~ choice += "7" 
+ [8]
  ~ choice += "8" 
+ [9]
  ~ choice += "9"
+ [0]
  ~ choice += "0"
+ [RETURN]
  // ~ num = INT(choice) // Error: Cannot perform operation 'INT' on String
  Final number is {choice}
  -> DONE
- >> Value: {choice}
-> choose_digit_string

```

The problem with this method is that we're making a string which cannot be converted to an int for subsequent numeric calculations. Ink has only two operations for strings: comparison and substring. If you were testing for specific numbers stored as strings, the above would suffice but assuming we're not, we can do something else. 

Basically, we end up writing an Ink script that's similar to `choose_digit` and `choose_digit_string` - except to concatenate we multiply the current number by 10 and add the choice value to it. The result looks like this.

```ink
VAR just_a_number = 0

-> pick_number(just_a_number) ->
- picked number is {just_a_number}
~ just_a_number += 1
I added 1 to it to make {just_a_number}

////////////////////////////////////////////

=== pick_number(ref num)
~ num = 0
-> choose_digit(num) ->
->->

=== choose_digit(ref num)
VAR choice = 0
+ [1] 
  ~ choice = 1
+ [2] 
  ~ choice = 2
+ [3] 
  ~ choice = 3
+ [4] 
  ~ choice = 4 
+ [5] 
  ~ choice = 5 
+ [6] 
  ~ choice = 6 
+ [7] 
  ~ choice = 7 
+ [8]
  ~ choice = 8 
+ [9]
  ~ choice = 9
+ [0]
  ~ choice = 0
+ [RETURN]
  ->->
- >> Chose: {choice} <> 
  ~ num = num * 10 + choice
  Value: {num}
-> choose_digit(num)
```

Of course there's a problem in this script and it becomes apparent if you pick the number `2147483647` which is valid but that increment blows it past the maximum integer value and circles around to `-2147483648`. We could ignore this or we could early-out if the number gets bigger than a desired value. Something like this could do:

```ink
- >> Chose: {choice} <> 
  ~ num = num * 10 + choice
  Value: {num}
// early return
{ num > 999999 :
  ~num = 999999
  ->->
}
// etc.
-> choose_digit(num)
```
That random number `999999` could be stored as an INPUT_MAX global variable and customized per Ink file. 

## Extra (Complicated) Credit

Writing out 10 choices for each decimal digit is not so bad but let's assume we had more digits than that. In such a situation, we may want to use loops. Loops in Ink can be accomplished in a manner similar to loops in other programming languages that are made with JUMP or GOTO statements. This is a while loop in C unrolled into something kinda like x86 Assembly.

```
// C code
int i = 0;
while (i < 10) {
    i++;
}
// Pseudo Assembly
SET X to 0
- label while : 
 COMPARE X, 10
 if X is greater or equal to 10, JUMP to false
- label true :
  INCREMENT X
  JUMP to while
- label false :
;; rest of the code
```

It might looked complicated but if you squint, you can see how Ink diverts are similar to JUMPs. The above loop could look like this in Ink.

```ink
VAR X = 0
- (label_while)
  { X >= 10 : -> label_false }
- (label_true)
   X is {X}
   ~ X++
   -> label_while
- (label_false)
   Loop is finished
```

Here we're taking advantage of labelled [Gathers](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#1-gathers). The above Ink script prints the following.

> X is 0
>
> X is 1
>
> X is 2
>
> X is 3
>
> X is 4
>
> X is 5
>
> X is 6
>
> X is 7
>
> X is 8
>
> X is 9
>
> Loop is finished

So using a loop like this, we can then use [Threads](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#2-threads) to collect a bunch of choices. Basically `X is {X}` will be replaced by an Ink script that outputs choices. 

```ink
VAR X = 0
- (label_while)
  { X >= 10 : -> label_false }
- (label_true)
   <- generate_numeric_choice(X) // thread in action
   ~ X++
   -> label_while
- (label_false)
   >> Loop is finished


=== generate_numeric_choice(n)
  + [Input {n}]
    -> DONE // temporarily added
```

The above outputs `Input 0` to `Input 9` and can now be shoved into the Ink script we wrote in the previous section (with some modifications).

```ink
VAR choice = 0
VAR X = 0
=== choose_digit(ref num)
~ X = 0
- (label_while)
  { X >= 10 : -> label_false }
- (label_true)
   <- generate_numeric_choice(X)
   ~ X++
   -> label_while
- (label_false)
  // Skip text output and just add the next choice
+ [RETURN]
  ->->
- (chose) >> Chose: {choice} <> 
  ~ num = num * 10 + choice
  Value: {num}
-> choose_digit(num)

=== generate_numeric_choice(n)
  + [Input {n}]
    ~ choice = n
    -> choose_digit.chose
```

Because we need to run the while loop over and over again, we need to ensure X is reset to 0 each time so we use `~ X = 0` because `VAR X = 0` does not re-set the variable. Both `choice` and `X` are global so it might be better to move those out of the knot to avoid confusion. 


Also since the generated choices are stitched via threads, it's important to ensure that the generated choices divert to where they once used to gather. So we give the gather a label and can jump to it via knot-name.gather-name which in this case is `choose_digit.chose`.


As mentioned before this is extremely complicated for a few digits but it could be useful in other scenarios. 

## Conclusion
Using choices, it is possible to accept numeric input in Ink. By making a choice for each digit, we can conjure a number and use it for various mathematical calculations. The method of input _is_ definitely clunky in Inky. However, it should be possible to create virtual keyboards or similar user interfaces within the game engine to present a smoother user experience. 