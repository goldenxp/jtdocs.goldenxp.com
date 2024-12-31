---
title: Prototyping a Card Swiping Game in Ink
parent: Ink
description: Possible approaches to making a strategy game like Reigns in Ink
---

{% include ink.html %}

# Prototyping a Card Swiping Game in Ink
This document will go over two methods of building a card strategy game similar to Reigns. 

## Intro
Reigns is a game of carefully balancing 4 values - Military, People, Religion and Money - while being presented with a variety of colorful decisions in the form of Cards. You decide by swiping left or right and the outcome changes the aforementioned values in different ways. Often a boost in one value will lead to a reduction of another and that's where the decision making comes into play. 

Because Ink is a robust language, it is possible to build a similar game (sans swiping) in pure Ink. A straightforward approach would be to present the decisions as [knots](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#3-knots). This offers a lot of flexibility and can be fairly organized.

## Cards as Knots
The heart of this method is to capture the narrative and outcome of each card in a single knot. Then, a primary knot "deals" the cards by diverting to the card knots. In the skeletal example below, the `DRAW_CARD` knot picks cards at random and the `CARD_1` knot has two options (aka [choices](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#2-choices) which are proxies of swiping left or right. When an option is selected, we [gather](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#1-gathers) and return to `DRAW_CARD` and this captures our game loop. 

```ink
-> DRAW_CARD
=== DRAW_CARD
~ temp card = RANDOM(1,2)
{ card :
- 1 : -> CARD_1
- else : -> CARD_ELSE
}

=== CARD_1
Flavor text
+ Choice1
   // Change stats
+ Choice2
  // Change stats
- -> DRAW_CARD
```

Here's the fleshed out version of the above example.
```ink
VAR MIL = 50
VAR PPL = 50
VAR REL = 50
VAR MON = 50

-> DRAW_CARD
=== DRAW_CARD
MIL:{MIL} PPL:{PPL} REL:{REL} MON:{MON} // Show Stats
// Check for lose condition here
// Now, pick the next card to show
~ temp card = RANDOM(1,3)
{ card :
- 1 : -> CARD_PPL_MIL
- 2 : -> CARD_REL_MON
- else : -> CARD_GAMBLE
}

=== CARD_PPL_MIL
The farmers could use military assistance!
+ No, soldiers are for war
  ~ MIL += 10
  ~ PPL -= 20
+ Yes, let's all work together
  ~ MIL -= 20
  ~ PPL += 10
- -> DRAW_CARD

=== CARD_REL_MON
To appease the gods, we need a new altar...made of gold
+ Sure, anything for the almighties!
  ~ REL += 30
  ~ MON -= 30
+ Ha ha nope, we don't have that kind of money!
  ~ REL -= 10
- -> DRAW_CARD


=== CARD_GAMBLE
Do you want to try your luck?
+ Yes, Double or Half my money!
    { RANDOM(0,2) == 0 :
    ~ MON = MON * 2
    You win. Way to go!
    - else: 
    ~ MON = INT( MON / 2 )
    You lose. Too bad..
    }
+ No, Gambling is for losers...
- -> DRAW_CARD
```

As more cards (knots) are added, the switch case can be modified to add more diverts. Basically, each integer maps to a divert. Sophisticated selection techniques can ensure we don't see the same cards too often. 

## Cards as Threaded List Items
Another approach involves using lists where each item in the list can output a unique [thread](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#2-threads). Unlike the prior approach, this one works better if the cards function similarly. Unique behaviors per card are difficult to wrangle because this technique depends on knot parameter definitions for each card. 


There are two parts to this. The first is the use of lists which affords the creation of sets of cards. One big list declaration can be used for all cards and list variables can be used to make smaller sets of cards. To draw a card, the `LIST_RANDOM` function can be used and the function result can be removed from the set variable to ensure we don't pick it again. This way we can actually deplete a card set which feels more organic. 

The second part involves the use of threads to output a knot based on parameter values. Each list item maps to a knot invocation which displays the text/options and handles choice outcomes. This is what it could look like. 

```ink
LIST all_cards = donate, marry, taxes, hard1, hard2, hard3
VAR set1 = (donate, marry, taxes)

-> DRAW_CARD
=== DRAW_CARD
~ temp card = LIST_RANDOM(set1)
~ set1 -= card
-> GET(card)

=== GET(card)
{ card : 
- donate : <- CARD("Donate alms?", "Yes, help the poor", "No we need the money") 
- marry  : <- CARD("Marry the princess?", "Yep, peace between kingdoms", "Nope I like being single")
- taxes  : <- CARD("Increase taxes?", "We have to", "Nah it's theft")
}
-> DONE

=== CARD(text, opt1, opt2)
{ text }
+ [{ opt1 }]
+ [{ opt2 }]
-
-> DRAW_CARD
```

And below is a version of the above that's a bit more functional.

```ink
LIST all_cards = donate, marry, taxes, hard1, hard2, hard3
VAR set1 = (donate, marry, taxes)

VAR ARM = 50
VAR PPL = 50
VAR REL = 50
VAR MON = 50

-> DRAW_CARD
=== DRAW_CARD
ARM:{ARM} PPL:{PPL} REL:{REL} MON:{MON} // Show Stats
~ temp card = LIST_RANDOM(set1)
~ set1 -= card
-> GET(card)

=== GET(card)
{ card : 
- donate : <- CARD("Donate alms?", "Yes, help the poor", 0, 10, 10, 0, "No we need the money", 0, -10, -10, 0) 
- marry  : <- CARD("Marry the princess?", "Yep, peace between kingdoms", 0, 0, 0, -10, "Nope I like being single", 10, 0, 0, 0)
- taxes  : <- CARD("Increase taxes?", "We have to", 0, -20, 0, 10, "Nah it's theft", 0, 10, 0, 0)
}
-> DONE

=== CARD(text, opt1, a1, p1, r1, m1, opt2, a2, p2, r2, m2)
{ text }
+ [{ opt1 }]
    ~ ARM += a1
    ~ PPL += p1
    ~ REL += r1
    ~ MON += m1
+ [{ opt2 }]
    ~ ARM += a2
    ~ PPL += p2
    ~ REL += r2
    ~ MON += m2
-
-> DRAW_CARD

```

## Conclusion
The two methods above can be blended. We can skip integers in the first and use lists for easier card management. And in the second, we can skip threads and knot invocations and just use unique knots per list item. The project's scope or game depth can define which method is more suitable. 