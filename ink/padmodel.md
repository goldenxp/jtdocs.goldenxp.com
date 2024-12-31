---
title: Mapping a PAD Model in Ink
parent: Ink
description: How to setup a Pleasure-Arousal-Dominance model in Ink
---

{% include ink.html %}

# Mapping a PAD Model in Ink
Learn two methods of setting up character emotional states in Ink

## Intro
This document will briefly talk about the PAD emotional state model and demonstrate two ways of setting it up in Ink.

Definitely read the Wikipedia article before proceeding: <https://en.wikipedia.org/wiki/PAD_emotional_state_model>

The TL;DR is that we can map a character's emotional state along three emotional axes:
1. Pleasure to Displeasure (enjoyable or not)
2. Arousal to Non-Arousal (stimulating or not)
3. Dominance to Submissive (in control or not) 

This system affords an interesting way of writing character dispositions in Ink. We can supplement existing systems with simple variables to have characters dynamically react to their environments (or changes made to the environment by the player). 

## Simple Mapping
A simple method of using PAD in Ink would be to map the min and max of PAD's 3 dimensions in a single integer variable per character. So, each character would have their own number that would represent pleasure or displeasure, arousal or non-arousal and dominance or submissive.

Unfortunately, at the time of writing this article, Ink does not support bitwise operations so we can't express these sub-states as each digit of a number. If we could, numbers 0 to 7 would suffice and map out all emotional states from Boredom to Exuberant.

<svg xmlns="http://www.w3.org/2000/svg" width="512" height="512" viewBox="0 0 135 135"><path fill="#806600" fill-opacity=".59" fill-rule="evenodd" d="m20.863 98.216 39.075 18.253V19.616L20.863 32.584Z"/><path fill="#fd5" fill-opacity=".59" fill-rule="evenodd" d="M59.938 116.47V19.615l57.509 5.673v75.117z"/><path fill="#d4aa00" fill-opacity=".59" fill-rule="evenodd" d="m20.863 98.216 39.075 18.253 57.509-16.063-48.169-10.932Z"/><path fill="#fea" fill-opacity=".59" fill-rule="evenodd" d="m69.278 89.474 48.169 10.932V25.289l-48.169 9.313z"/><path fill="#a80" fill-opacity=".59" fill-rule="evenodd" d="M20.863 98.216V32.584l48.415 2.018v54.872z"/><path fill="#d4aa00" fill-opacity=".59" fill-rule="evenodd" d="m20.863 32.584 39.075-12.968 57.509 5.673-48.169 9.313Z"/><text xml:space="preserve" x="66.533" y="77.395" stroke-width=".265" font-family="sans-serif" font-size="10.583" font-weight="400" style="line-height:1.25"><tspan x="66.533" y="77.395">P</tspan></text><text xml:space="preserve" x="48.402" y="96.962" stroke-width=".265" font-family="sans-serif" font-size="10.583" font-weight="400" style="line-height:1.25"><tspan x="48.402" y="96.962">A</tspan></text><text xml:space="preserve" x="79.468" y="97.565" stroke-width=".265" font-family="sans-serif" font-size="10.583" font-weight="400" style="line-height:1.25"><tspan x="79.468" y="97.565">D</tspan></text><text xml:space="preserve" x="69.393" y="87.257" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25"><tspan x="69.393" y="87.257" style="text-align:center" text-anchor="middle">bored </tspan><tspan x="69.393" y="93.43" style="text-align:center" text-anchor="middle">0-0-0</tspan><tspan x="69.393" y="99.604" style="text-align:center" text-anchor="middle">0</tspan></text><text xml:space="preserve" x="59.41" y="16.154" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25"><tspan x="59.41" y="16.154" style="text-align:center" text-anchor="middle">exuberant</tspan><tspan x="59.41" y="22.327" style="text-align:center" text-anchor="middle">1-1-1</tspan><tspan x="59.41" y="28.501" style="text-align:center" text-anchor="middle">7</tspan></text><text xml:space="preserve" x="69.305" y="32.985" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25;text-align:center" text-anchor="middle"><tspan x="69.305" y="32.985" style="text-align:center">docile</tspan><tspan x="69.305" y="39.159" style="text-align:center">1-0-0</tspan><tspan x="69.305" y="45.332" style="text-align:center">4</tspan></text><text xml:space="preserve" x="60.222" y="114.342" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25"><tspan x="60.222" y="114.342" style="text-align:center" text-anchor="middle">hostile</tspan><tspan x="60.222" y="120.515" style="text-align:center" text-anchor="middle">0-1-1</tspan><tspan x="60.222" y="126.689" style="text-align:center" text-anchor="middle">3</tspan></text><text xml:space="preserve" x="20.179" y="96.405" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25;text-align:center" text-anchor="middle"><tspan x="20.179" y="96.405" style="text-align:center">anxious</tspan><tspan x="20.179" y="102.579" style="text-align:center">0-1-0</tspan><tspan x="20.179" y="108.752" style="text-align:center">2</tspan></text><text xml:space="preserve" x="21.56" y="31.056" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25"><tspan x="21.56" y="31.056" style="text-align:center" text-anchor="middle">dependent </tspan><tspan x="21.56" y="37.229" style="text-align:center" text-anchor="middle">1-1-0</tspan><tspan x="21.56" y="43.403" style="text-align:center" text-anchor="middle">6</tspan></text><text xml:space="preserve" x="118.08" y="97.982" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25"><tspan x="118.08" y="97.982" style="text-align:center" text-anchor="middle">disdainful</tspan><tspan x="118.08" y="104.156" style="text-align:center" text-anchor="middle">0-0-1</tspan><tspan x="118.08" y="110.329" style="text-align:center" text-anchor="middle">1</tspan></text><text xml:space="preserve" x="116.712" y="23.873" stroke-width=".265" font-family="sans-serif" font-size="4.939" font-weight="400" style="line-height:1.25"><tspan x="116.712" y="23.873" style="text-align:center" text-anchor="middle">relaxed</tspan><tspan x="116.712" y="30.047" style="text-align:center" text-anchor="middle">1-0-1</tspan><tspan x="116.712" y="36.221" style="text-align:center" text-anchor="middle">5</tspan></text></svg>

So what we'll do is use an Ink list to define each state; each state will have a specific decimal value that's the binary equivalent of 0 to 7. This sentence might make more sense if you look at the following list declaration.

```ink
LIST State = Bored=000, Disdainful=001, Anxious=010,  Hostile=011, Docile=100, Relaxed=101, Dependent=110, Exuberant=111
```

Essentially, 0/1 values are negative/positive while the Hundredths, Tenths and Units place are Pleasure, Arousal and Dominance axes. Now, with simple arithmetic, we can toggle sub-states with addition or subtraction of 100, 10 or 1 for Pleasure, Arousal or Dominance, respectively. We can wrap these in functions for convenient usage. A potential setup can look like this.

```ink
LIST State = Bored=000, Disdainful=001, Anxious=010,  Hostile=011, Docile=100, Relaxed=101, Dependent=110, Exuberant=111

// CONSTs for consistency
CONST P = "pleasure"
CONST A = "arousal"
CONST D = "dominance"

=== function toggle_pleasure(someState)
~ return toggle_state(someState, P)

=== function toggle_arousal(someState)
~ return toggle_state(someState, A)

=== function toggle_dominance(someState)
~ return toggle_state(someState, D)

=== function toggle_state(someState, axis)
// Convert state to integer
~ temp val = LIST_VALUE(someState)
// Determine digit's place to change against
~ temp place = 0
{ axis:
- P : 
    ~ place = 100
- A : 
    ~ place = 10
- D : 
    ~ place = 1
}
// If modulo is less than placement, we need to add to toggle
{ val % (place * 10) < place : 
    ~ val = val + place 
  - else:  
    ~ val = val - place 
}
~return State(val)
```

If you change the CONSTs to the placement numeric values determined in the switch case in `toggle_state`, the script can be simplified further.

```ink
// CONSTs for consistency
CONST P = 100
CONST A = 10
CONST D = 1

// .. same helper functions as above

=== function toggle_state(someState, place)
// Convert state to integer
~ temp val = LIST_VALUE(someState)
// If modulo is less than placement, we need to add to toggle
{ val % (place * 10) < place : 
    ~ val = val + place 
  - else:  
    ~ val = val - place 
}
~return State(val)
```

Now, if a character was in a Bored state and was stimulated, they can now become Anxious.
```ink
VAR player_mood = Bored
~ player_mood = toggle_arousal( player_mood )
The player is now { player_mood } // outputs: Anxious 
```
Of course, there's nothing stopping us from doing a direct assigment but the ability to toggle the different axes can enable interesting forms of dynamic storytelling. Here's a trivial example of visiting an angry shopkeeper. The intro and outro change based on Pleasure.  

```ink
VAR seller_mood = Hostile
-> shop
=== shop
Seller: { seller_mood >= Docile : Welcome back to my store! |  Oh, it's you again. }
+ [Strike a hard bargain]
+ [Be fair and tip]
    { is_pleased(seller_mood) == false : 
        ~ seller_mood = toggle_pleasure(seller_mood)
    }
-
Thank you for visiting our store{ seller_mood != Hostile:. Please come again. |, jerk. Don't come back! }
-> DONE

=== function is_pleased(someState)
~ return LIST_VALUE(someState) % (P * 10) >= P
``` 

## Advanced Mapping
The simple implementation should suffice for most scenarios but the PAD model actually features more levels of granularity. Essentially, each dimension has more than two values as assumed in the simple mapping example. Two values assume only negative and positive states and does not take neutrality into account. This can capture more emotional states such as sadness and surprise. 

Basically, we need to express each axis as if it were a slider as opposed to a checkbox. If this slider can hold 4 possible values per dimensions, we have 64 possible emotional states to deal with (instead of just 8). Expressing this in a single Ink list is difficult and does not scale. We're dealing with cubed numbers here! 

While it might be better to delegate this to the external game engine, it is possible to achieve this in Ink using multiple lists. This is what it could look like.

```ink
LIST Pleasure = Unpleasant, PartUnpleasant, PartPleasant, Pleasant

LIST Arousal = Unaroused, PartUnaroused, PartAroused, Aroused

LIST Dominance = Submissive, PartSubmissive, PartDominant, Dominant
```

It is vital that each dimensional list have the same number of items. The above declaration allows for 64 possible emotions but we could dial it down to 3 per dimension and have 27 instead. We can define some emotional states as variables; each variable would be a combination of a single value from each dimensional list. These can be treated like presets and used for comparisons later on. 

```ink
VAR Boredom = (PartUnpleasant, PartUnaroused, PartSubmissive)
VAR Depression = (Unpleasant, Unaroused, Submissive)
VAR Anger = (Unpleasant, Aroused, Dominant)
VAR Fear = (Unpleasant, Aroused, Submissive)
// ... etc
```

Now, each character can have a single list variable to express their emotional state. We can set these values using the predefined list variables but we likely won't have all 64 emotions defined as unique variables. As mentioned, this doesn't scale; instead we should rely on the three values stored within. We can do this with contains/intersection checks.

```ink
VAR player_state = ()
~ player_state = Boredom
{ player_state has PartUnpleasant : You don't feel so good. }
{ player_state has Unpleasant : You feel downright awful. } // Does not print
{ player_state ^ (PartUnaroused, PartSubmissive) : Life feels unexciting and you don't feel like you're in control. }
```

You can modify emotional states in many ways but care must be taken to not allow multiple values from the same list. It is better to use functions that can sanitize the list variables. Here's an example.

```ink
You drink some coffee.
~ IncreasePleasure(player_state)
You go out for a walk.
~ IncreasePleasure(player_state)
{ player_state has Pleasant : You feel good! }

=== function IncreasePleasure(ref state)
// Get all possible pleasure values
~ temp allPleasure = LIST_ALL(Pleasure)
// Find all pleasure values in this state
~ temp found = allPleasure ^ state
// Remove old values from the state
~ state -= found
// Just in case multiples exist, consolidate to the smallest one
~ temp oldValue = LIST_MIN(found)
// Only increase within the bounds of pleasure (errr wording?)
~ temp maxPleasure = LIST_MAX(allPleasure)
~ temp newValue = oldValue + 1*(oldValue != maxPleasure)
// Add the new value to the emotional state
~ state += newValue

```

## Conclusion
It could be possible to use a single integer with values from 0 to 63 to express the advanced mapping. But checking and manipulating the values could get complicated. With the help of three Ink lists and list variables, it's easier to maintain and utilize complex emotional configurations. Of course, if such complexity is not needed, the simple mapping will suffice. Either method can produce sophisticated results especially when designing Ink stories that have dynamic choices and systemic events. 


