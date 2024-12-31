---
title: Character Pronouns in Ink
parent: Ink
description: Approaches on dealing with pronouns in Ink
---

{% include ink.html %}

# Character Pronouns in Ink
Learn a few ways of handling pronouns while writing in Ink

## Intro
Because Ink narratives can be dynamic, there may be situations where the pronouns in a sentence need to change dynamically too. For example, if a game lets you customize your main character or pick from a stock of diverse character presets, you may want your text to change to reflect that. Of course, it is still grammatically correct to use gender-neutral pronouns all the way but suppose you didn't want to pursue that route, the following document goes over some potential solutions.

## Pronouns as Variables
A common solution is to store the necessary pronouns as variables, populate the values as needed and unroll them into the text with braces.

```ink
VAR pronoun = "He"
VAR pronoun_possessive = "his"

{pronoun} ate {pronoun_possessive} meal.
// output: He ate his meal.
```
Ink is supposed to be writer-friendly so it's better to use variable names that are easier to read in the text as opposed to ones that accurately describe what they are. A better look would be…

```ink
VAR He = "He"
VAR his = "his"

{He} ate {his} meal.
// output: He ate his meal.

~ He = "She"
~ his = "her"
{He} ate {his} meal.
// output: She ate her meal.
```
This can work but there are already problems with it. Capitalization is not handled for one so that will need multiple variables or string logic via internal (Ink) or external (game engine) functions. Also the verb `ate` is in the simple past tense; if we were to use other tenses, this method would need more variables. 

```ink
// direct assignment is a shortcut for demonstration purposes
VAR He = "They"
VAR his = "their"
VAR has = "have"
VAR s = ""

{He} {has} eaten {his} meal.
// output: They have eaten their meal.
{He} often eat{s} early.
// output: They often eat early.
``` 
And it does not stop there. Just like our oddly named `s` variable we'd need 3 more helper variables for verbs like "does","watches" and "tries." As more gets written, more cases are bound to pop up but this method does take care of the basics and can work for very specific conditions. One of these is . . . English only. 

These rules are for English and don't work in other languages that have different grammatical rules. Basically, for each language your game might ship in, you would need to conjure up a set of variables for pronouns, verbs, etc. and ensure these variables are properly used throughout the Ink story. Because it requires the Ink writer to be a grammar polyglot and/or the translators to know (and maybe even use) Ink, this method may not always suffice.

## Write Every Case
A safer approach would be to actually write out all possible entries (using no variables) and have Ink pick the necessary strings based on the character or context. Below is an example of this method in action. 

```ink
LIST pronouns = he, she, they
VAR MC = she
{ MC:
- he : He has eaten his meal.
- she : She has eaten her meal.
- they : They have eaten their meal. 
}
```

We'll have more words to localize which might be an added expense; but at the same time, issues like capitalization or special conjugations will not pose any problem. The downside is we need to use switch blocks and we'll have to write them over and over again. A better approach would be to use a function that accepts the strings as parameters and returns the one needed for that context.

```ink
LIST pronouns = they, she, he, it

VAR MC = it
{p(MC, "They have eaten their meal", "She has eaten her meal", "He has eaten his meal", "It has eaten its meal")}.
    
// short function name
=== function p(char, c, f, m, i)
{ char:
- they :
    ~ return c
- she :
    ~ return f
- he :
    ~ return m
- it :
    ~ return i
- else : 
    ~ return c
}
```
The nice thing about having this function is it can also be used to make tags. It might be necessary to have unique tags for voice over lines, for example.

```ink
#AUD_{p(MC,"S1C1","S1C2","S1C3","S1C4")}
// outputs tag # AUD_S1C4
```

Using multiple parameters might warrant the need for validation but beyond that, we have another problem: what if the subject AND object in a sentence both have dynamic pronouns. We could expand the function to take in a subject and object parameters but with 4 possible pronoun choices, we're looking at 16 possible strings. Even if we remove "it" and stick with 3 choices, it's still 9 strings which is quite a bit. In this situation, we can still use the original function but we'll have to break the sentences down.

## Text Fragments
This method is almost like a blend of the first two methods. We'll use a mixture of case functions and temporary variables. With just the original function, it can look like this. 

```ink
VAR MC = he
VAR Friend = she

{p(MC,"They were","She was","He was","It was")} angry and <>
shoved the money in {p(Friend,"their","her","his","its")} <>
hand. #line_10_{MC}_{Friend}
// glues are used for easier reading, totally optional
```

But if we had to repeat the pronouns, the `p` function calls would crowd up our text, making it harder to read. We can cache values into temporary variables and use these as needed. The idea is to avoid global variables; trying to fit global variables all situations would be prone to issues caused by edge cases. We can also add helper functions to consolidate frequently used strings. 

```ink
~ temp PMC = Pronoun(MC)
~ temp pMC = pronoun(MC)
~ temp oMC = pronoun_object(MC)
~ temp was_angry = p(MC, "were angry", "was angry", "was angry", "was angry")
~ temp PFriend = Pronoun(Friend)
~ temp pFriend = pronoun(Friend)
~ temp pFriends = pronoun_possessive(Friend)

{PMC} {was_angry} and shoved the money in {pFriends} hand. #line_10_{MC}_{Friend}
{PFriend} asked {oMC} why {pMC} did that and {pMC} responded rudely. #line_11_{MC}_{Friend}
// ... more story stuff can go here

=== function Pronoun(char)
~ return p(char, "They","She","He","It")

=== function pronoun(char)
~ return p(char, "they","she","he","it")

=== function pronoun_possessive(char)
~ return p(char, "their","her","his","its")

=== function pronoun_object(char)
~ return p(char, "them","her","him","it")
``` 

Of course, just like the first method, this one will work well in English but depending on the language structure, it might not be possible to use the temporary variables in the same way in the translation. What I consider to be the safer approach is writing every case for the subject pronoun but use temporary variables to populate any objects in those cases.

```ink
LIST pronouns = they, she, he, it

VAR MC = it
VAR Friend = they
VAR AnotherFriend = she

~ temp object1 = p(Friend, "their dog", "her dog", "his dog", "its dog")
~ temp object2 = p(AnotherFriend, "their cat", "her cat", "his cat", "its cat")
~ temp c = "They have played with {object1} just like they often do with {object2}"
~ temp f = "She had played with {object1} just like she often does with {object2}"
~ temp m = "He had played with {object1} just like he often does with {object2}"
~ temp i = "It had played with {object1} just like it often does with {object2}"
{ p(MC, c, f, m, i) }. #line_12_{MC}_{Friend}_{AnotherFriend}

```

Ultimately, the languages you need to support (and your localization strategy) might determine how you want to break up your text. 

## Conclusion
We've seen a few ways of setting up custom pronouns in pure Ink. Using functions and variables are valid approaches but depending on complexities of the scripts and other needs, the simplest solution might be the most effective - having different Ink files for each pronoun selection.
