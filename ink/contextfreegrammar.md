---
title: Ink Text Generation with Context-Free Grammar
parent: Ink
description: Documenting a primitive attempt at Procedural Narratives in Ink
---

{% include ink.html %}

# Ink Text Generation with Context-Free Grammar
Learn about Context-Free Grammar and how to implement it in Ink 

## Intro
Ink comes with all the functionality you need to generate stories using Context-Free Grammar. A grammar is a set of rules for writing strings by replacing variables with other strings and/or even other variables. Context-Free Grammar (CFG) means that variables can be replaced with any value without worrying about where the variable is (typically in a set of strings), i.e. its context. Since it's not reliant on context, it's context-free. If we have a sentence like "the animal is good" then the word "animal" can be replaced with cat, dog or any animal and the sentence would still be valid. It's a bit like Mad Libs and can be used for procedural narratives.

Detailed info can be found here: <https://en.wikipedia.org/wiki/Context-free_grammar>

## Implementation
Thanks to shuffles in Ink, implementing CFG variables is quite trivial.
```ink
VAR animal = ""
~ animal = "{~bat|cat|dog}"
The {animal} is good.

// or
~ animal = grammar_animal()
The {animal} is good.

=== function grammar_animal()
~ return "{~bat|cat|dog}"

```
Note the use of an inline shuffle within quotes to return a single string from a list of random strings. Now we can use the animal variable all over and each re-run of the Ink story would pick an animal at random.

Text is generated from a starting variable which we never see in its original form in the final result because all the necessary substitution has occurred. A parse tree or syntax tree can depict how CFG works. 

<svg xmlns="http://www.w3.org/2000/svg" id="svg99" style="width:600px;height:450px" version="1.1" viewBox="0 0 600 450"><style id="style2">text{text-anchor:middle;font-size:small;font-weight:700;fill:#000}line,rect{stroke:#000}rect{stroke-width:1;fill:#241f1c}line{stroke-width:.5;opacity:.5;stroke-dasharray:3,2}</style><defs id="defs9"><marker id="arrowHead" markerHeight="15" markerUnits="strokeWidth" markerWidth="15" orient="auto" preserveAspectRatio="none" viewBox="-10 -5 10 10"><path id="path6" d="M-10-5 0 0l-10 5z"/></marker></defs><g id="g97"><rect width="119.899" height="54.416" x="239.089" y="17.443" rx="7.932" ry="7.932" style="fill:#fc0"/><text x="299.961" y="17.443"><tspan id="tspan13" x="299.961" dy="32.378">Sentence</tspan></text><rect width="119.899" height="54.416" x="95.21" y="137.343" rx="7.932" ry="7.932" style="fill:#fc0"/><text x="155.16" y="137.343"><tspan id="tspan19" x="155.16" dy="32.378">NounPhrase</tspan></text><line x1="279.671" x2="155.16" y1="71.859" y2="137.343" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="383.89" y="137.343" rx="7.932" ry="7.932" style="fill:#fc0"/><text x="443.84" y="137.343"><tspan id="tspan27" x="443.84" dy="32.378">VerbPhrase</tspan></text><line x1="319.329" x2="443.84" y1="71.859" y2="137.343" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="23.271" y="257.242" rx="7.932" ry="7.932" style="fill:#fc0"/><text x="83.22" y="257.242"><tspan id="tspan35" x="83.22" dy="32.378">Determiner</tspan></text><line x1="134.869" x2="83.22" y1="191.758" y2="257.242" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="167.15" y="257.242" rx="7.932" ry="7.932" style="fill:#fc0"/><text x="227.099" y="257.242"><tspan id="tspan43" x="227.099" dy="32.378">Noun</tspan></text><line x1="175.451" x2="227.099" y1="191.758" y2="257.242" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="311.951" y="257.242" rx="7.932" ry="7.932" style="fill:#fc0"/><text x="371.901" y="257.242"><tspan id="tspan51" x="371.901" dy="32.378">Verb</tspan></text><line x1="423.549" x2="371.901" y1="191.758" y2="257.242" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="455.83" y="257.242" rx="7.932" ry="7.932" style="fill:#fc0"/><text x="515.779" y="257.242"><tspan id="tspan59" x="515.779" dy="32.378">Adjective</tspan></text><line x1="464.131" x2="515.779" y1="191.758" y2="257.242" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="23.271" y="377.141" rx="7.932" ry="7.932" style="fill:#dde9af"/><text x="83.22" y="377.141" style="fill:green"><tspan id="tspan67" x="83.22" dy="32.378" style="fill:green">The</tspan></text><line x1="83.22" x2="83.22" y1="311.657" y2="377.141" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="167.15" y="377.141" rx="7.932" ry="7.932" style="fill:#dde9af"/><text x="227.099" y="377.141" style="fill:green"><tspan id="tspan75" x="227.099" dy="32.378" style="fill:green">dog</tspan></text><line x1="227.099" x2="227.099" y1="311.657" y2="377.141" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="311.951" y="377.141" rx="7.932" ry="7.932" style="fill:#dde9af"/><text x="371.901" y="377.141" style="fill:green"><tspan id="tspan83" x="371.901" dy="32.378" style="fill:green">is</tspan></text><line x1="371.901" x2="371.901" y1="311.657" y2="377.141" marker-end="url(#arrowHead)"/><rect width="119.899" height="54.416" x="455.83" y="377.141" rx="7.932" ry="7.932" style="fill:#dde9af"/><text x="515.779" y="377.141" style="fill:green"><tspan id="tspan91" x="515.779" dy="32.378" style="fill:green">good</tspan></text><line x1="515.779" x2="515.779" y1="311.657" y2="377.141" marker-end="url(#arrowHead)"/></g></svg>

Below is the above implemented in Ink
```ink
VAR Sentence    = ""
  VAR NounPhrase  = ""
    VAR Determiner  = ""
    VAR Noun        = ""
  VAR VerbPhrase  = ""
    VAR Verb        = ""
    VAR Adjective   = ""

~ Determiner = "{~ the|my|your|each|which|this|that}"
~ Noun = "{~ dog|cat|mouse|hamster|chicken}"
~ Verb = "{~ is|was}"
~ Adjective = "{~ good|hungry|dead|happy|sad}"

~ NounPhrase = "{Determiner} {Noun}"
~ VerbPhrase = "{Verb} {Adjective}"
~ Sentence = "{NounPhrase} {VerbPhrase}"
{Sentence} // prints all types of sentences each restart
```
Notice how the parse/syntax tree is flipped in Ink. When using expressions like this, we need to fetch the children first and work up to the root parent. We can wrap these expressions in functions to easily invoke them as many times as needed. Each function can fetch the necessary values and we won't have to worry about the correct order of substitution.

```ink
{g_sentence()}
{g_sentence()}
{g_sentence()}

// Root function
=== function g_sentence()
~ NounPhrase = g_noun_phrase()
~ VerbPhrase = g_verb_phrase()
~ Sentence = "{NounPhrase} {VerbPhrase}"
~ return Sentence

// Branch functions
=== function g_noun_phrase()
~ Determiner = g_determiner()
~ Noun = g_noun()
~ NounPhrase = "{Determiner} {Noun}"
~ return NounPhrase

=== function g_verb_phrase()
~ Verb = g_verb()
~ Adjective = g_adjective()
~ VerbPhrase = "{Verb} {Adjective}"
~ return VerbPhrase

// Leaf functions
=== function g_determiner()
~ return "{~the|my|your|each|which|this|that}"

=== function g_noun()
~ return "{~dog|cat|mouse|hamster|chicken}"

=== function g_verb()
~ return "{~ is|was}"

=== function g_adjective()
~ return "{~ good|hungry|dead|happy|sad}"
```

Of course, with functions, we can also forego Ink variables!

```
// Look ma no variables!
=== function g_sentence()
~ return g_noun_phrase() + " " + g_verb_phrase()

=== function g_noun_phrase()
~ return g_determiner() + " " + g_noun()

=== function g_verb_phrase()
~ return g_verb() + " " + g_adjective()

// Leaf functions etc...
```

Here's a sample output
> that chicken is hungry
>
> each cat was good
>
> this dog is sad

It's not exactly a compelling story but here's the thing. Our roots don't have to be basic sentences; they can be pieces of an actual plot.

## Application
As stated in the intro, grammars are rule sets and we can set the rules as we please in order to produce the results we want. Here's an example of writing a narrative that is perhaps a bit more compelling.
```ink
-> story
=== story
~ temp Job = "{~librarian|soldier|blacksmith}"
~ temp DogName = "{~Fido|Aki|Dee}"
~ temp DogBreed = "{~poodle|corgi|boxer}"
~ temp Home = "{~city|village|settlement}"
~ temp HomeAdj = "{~a remote|a faraway|an isolated}"
~ temp Suspicion = "{~ got lost|ran away|fell in a hole}"
~ temp TimeDesc = "{~ slipping|running out|of the essence}"
~ temp FootDesc = "{~ huge|muddy|strange}"
~ temp VillainName = "{~Kai|Lee|Avery}"

I was just a {Job} who lived in {HomeAdj} {Home}. My best friend was a dog named {DogName}, the cutest, friendliest {DogBreed}. Everything was great but trouble was brewing near the {Home}.
One fateful day, {DogName} disappeared. I looked high and low but couldn't find my faithful pet. Everyone in the {Home} suspected that {DogName} {Suspicion} but I had my doubts.
Time was {TimeDesc}. I just couldn't sit there and wait. I grabbed my backpack and set out. 
I found {DogName}'s pawprints near the edge of the forest. There was a set of {FootDesc} human footprints beside them. I followed it into the forest. 
I found an encampment and a figure seated by its bonfire. They had {FootDesc} feet and a sleeping {DogName} lay on their lap.
"Who are you?" I demanded.
"Me?" they responded. "I'm {VillainName}, a humble traveler."
I exclaimed "That's my dog you got there!"
"Is it now? I once had a dog too, y'know. Come sit and let me tell you the tale..."
+ [Listen to the story] -> story
```
Basically, a knot is set up with temporary variables and these are used to tell the story. While it is possible to swap some variables with inline shuffles, the use of variables makes the text somewhat easier to read. It's also handy if values needs to be repeated. The end of the knot loops back to the beginning to generate a new story told within the one that was just displayed - a bit of recursive storytelling, if you will. Here's a sample output.

> I was just a soldier who lived in an isolated city. My best friend was a dog named Fido, the cutest, friendliest corgi. Everything was great but trouble was brewing near the city.
> 
> One fateful day, Fido disappeared. I looked high and low but couldn't find my faithful pet. Everyone in the city suspected that Fido ran away but I had my doubts.
> 
> Time was running out. I just couldn't sit there and wait. I grabbed my backpack and set out.
> 
> I found Fido's pawprints near the edge of the forest. There was a set of strange human footprints beside them. I followed it into the forest.
> 
> I found an encampment and a figure seated by its bonfire. They had strange feet and a sleeping Fido lay on their lap.
> 
> "Who are you?" I demanded.
> 
> "Me?" they responded. "I'm Lee, a humble traveler."
> 
> I exclaimed "That's my dog you got there!"
> 
> "Is it now? I once had a dog too, y'know. Come sit and let me tell you the tale..."
> 
> Listen to the story
> 
> I was just a librarian who lived in a remote village. My best friend was a dog named Aki, the cutest, friendliest poodle. Everything was great but trouble was brewing near the village.
> 
> One fateful day, Aki disappeared. I looked high and low but couldn't find my faithful pet. Everyone in the village suspected that Aki got lost but I had my doubts.
> 
> (etc)

What's interesting about this example is it's been greatly simplified to tell a specific story. At the same time, each section of the text represents a core element of a typical story like conflict and climax. These story elements could themselves be variables expressed by yet another layer of rules. Denser trees can be harder to work with but can lead to interesting results! 

## Conclusion
Simpler rules that generate basic sentences are easier to write but will lack prose and can produce chaotic results. Complex rules are harder to write but can produce coherent stories at the cost of plot variance. There's a sweet spot somewhere in there but undoubtedly, better rules will generate better text. At the very least, we now have a basic understanding and a framework for writing with Context-Free Grammars.