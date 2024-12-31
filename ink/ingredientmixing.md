---
title: Mixing Ingredients in Ink
parent: Ink
description: How to define ingredients and combine them in Ink
---

{% include ink.html %}

# Mixing Ingredients in Ink
This article shows how to make a simple recipe system 

## Intro
The inspiration for this experiment is Coffee Talk, a visual novel with a coffee brewing component. You have a few key ingredients and you combine them to make different drinks.

![Screenshot of Coffee Talk in Endless mode]({{ site.baseurl }}/assets/img/Ink_RecipesCoffeeTalk.jpg)

There are two problems to address to make this work in Ink. One is to provide a way to mix the ingredients utilizing Ink Choices. The other is to determine if the selected ingredients create a valid combination. We'll address the latter first.

## Defining and Validating Ingredients
We'll define Ingredients using Lists. Lists in Ink are not arrays but sets of on/off variables that can each have a numeric value. When we assign each list entry to an ingredient, their combinations can be used to define recipes.

```ink
LIST ingredients = coffee, tea, milk, honey, cocoa

VAR dirty_chai = (coffee, tea, milk)
VAR sweet_coffee = (coffee, milk, honey)

VAR creation = ()
First add coffee
~ creation += coffee
Then add tea
~ creation += tea
Top off with milk
~ creation += milk

// Is creation a dirty chai?
{ creation == dirty_chai : You just made a dirty chai latte! }
```
In the Ink snippet above, list variables can be used for "adding" ingredients and can then be tested against predefined ones. We can add new variables for customer orders and compare against those to test if the player made the correct drink/dish/item/etc. 

Unfortunately, this method does not work if your recipes have duplicates or if the order matters. As it so happens, Coffee Talk recipes do allow duplicates and the order in which ingredients are added does change the outcome. Lists don't support duplicates; the entry is in the list variable or it's not. We also cannot check for sequences because lists sort based on the values set when the list is defined. The variable order in variables made from lists does not matter so we can't use this to enforce a specific sequence. The workaround to both these problems is to use strings instead. To pull this off, we would need a function that "converts" a list variable into a string.

```ink
LIST ingredients = coffee, tea, milk, honey, cocoa

CONST UNSET = ""
VAR dirty_chai = UNSET
VAR sweet_coffee = UNSET

~ dirty_chai = str(coffee) + str(tea) + str(milk)
~ sweet_coffee = str(coffee) + str(honey) + str(honey)

VAR creation = ""
First add coffee
~ creation += str(coffee)
Then add tea
~ creation += str(tea)
Top off with milk
~ creation += str(milk)

// Is creation a dirty chai?
{ creation == dirty_chai : You just made a dirty chai latte! | This is NOT a dirty chai latte... }

=== function str(ingredient)
{ ingredient :
- coffee : ~ return "cof"
- tea :  ~ return "tea"
- milk: ~ return "mlk"
- honey: ~ return "hon"
- cocoa: ~ return "cco"
}
```

Now our creations and the recipe outcomes are both concatenated strings. This way we can enforce order and allow duplicates in our comparisons. Note that we could also use numbers through list values but we would be limited to 10 ingredients or otherwise have to rely on more complicated math. 

## Mixing Choices
Instead of scripting the addition of ingredients, we will now offer them up as choices to the user. We'll first use a loop to provide all ingredients as choices. We'll need the pop function which is available in Inky's Ink menu under List handling. This function removes the smallest value from a list which in a loop enables list iteration. We'll loop with a labelled gather and output a choice for each list element using a thread and knot. Basically, the code below outputs a sticky choice for every ingredient.

```ink
~ temp all = LIST_ALL(ingredients) // Get a list with all ingredients
- (loop)
  ~ temp item = pop(all)
  { item :
    <- make_choice(item) // outputs the choice
    -> loop // jump to restart the loop
  } // if no items are left in the "all" list, we exit the loop

=== make_choice(ingredient)
+ [Add {ingredient}]
-> DONE

=== function pop(ref _list) 
    ~ temp el = LIST_MIN(_list) 
    ~ _list -= el
    ~ return el 
```

> Add coffee
> 
> Add tea
> 
> Add milk
> 
> Add honey
> 
> Add cocoa

We need these choices to do something, specifically adding the selected ingredient to our "container" variable; we can route this through using references. We also need the choices to divert to a knot or stitch that will symbolize the next step in our combination process. We will also pass this through using parameters. 

```ink
~ temp creation = "" // where we add our ingredients
~ temp all = LIST_ALL(ingredients) // Get a list with all ingredients
- (loop)
  ~ temp item = pop(all)
  { item :
    // output a choice that will modify the creation variable and divert to finish
    <- make_choice(creation, item, -> finish)
    -> loop // jump to restart the loop
  } // if no items are left in the "all" list, we exit the loop

= finish
We're done!
-> DONE

=== make_choice(ref cup, ingredient, -> next)
+ [Add {ingredient}] // Display choice using ingredient
    Adding {ingredient}! // Display feedback
    ~ cup += str(ingredient) // Append the ingredient string
    -> next
```

Next we need to present the choices multiple times. Some games allow only two-item combinations but here we need to support three. Giving the ingredient loop its own knot let's us re-use it easily to present the ingredient choices as well as set the next step. Since we combine three items to create our stuff, we'll just make stitches for each combination step. Dynamic recipes may need to rely on loops but that's out of scope for this document.

```ink
~ temp creation = "" // where we add our ingredients
-> first
= first
-> make_choice_set(creation, -> second)
= second
-> make_choice_set(creation, -> third)
= third
-> make_choice_set(creation, -> finish)
= finish
We're done!
// Check what we made
{ creation:
  - dirty_chai : Tasty chai latte! 
  - sweet_coffee : Sweet but good coffee.
  - else: I have no idea what this is...
}
-> DONE

=== make_choice_set(ref cup, -> next_step)
~ temp all = LIST_ALL(ingredients) // Get a list with all ingredients
- (loop)
  ~ temp item = pop(all)
  { item :
    // output a choice that will modify the creation variable and divert to finish
    <- make_choice(cup, item, next_step)
    -> loop // jump to restart the loop
  } // if no items are left in the "all" list, we exit the loop
-> DONE

=== make_choice(ref cup, ingredient, -> next)
+ [Add {ingredient}] // Display choice using ingredient
    Adding {ingredient}! // Display feedback
    ~ cup += str(ingredient) // Append the ingredient string
    -> next
```

## Extra Credit
This is a solid start but there's more that can be done.

For one, list variables are bound by variable naming rules so we can't use spaces. We may want to make a new function to output a user-facing name as well as a combination key.
```ink
=== make_choice(ref cup, ingredient, -> next)
+ [Add {prettyname(ingredient)}] // Display choice using ingredient
// other stuff
=== function prettyname(ingredient)
{ ingredient :
- coffee : ~ return "Coffee"
- tea :  ~ return "Tea"
- milk: ~ return "Milk®"
- honey: ~ return "Bee Honey"
- cocoa: ~ return "Cocoa Powder"
}
```

Putting the step stitches into a knot would allow it to be re-used as well. When generating choices, we may want to add the ability to restart, so having that knot would help us reset the `creation` variable and start at the first step again. 

```ink
=== brew_process
creation = UNSET
-> first
= first
-> make_choice_set(creation, -> second)
// other stuff
=== make_choice_set(ref cup, -> next_step)
~ temp all = LIST_ALL(ingredients) // Get a list with all ingredients
- (loop)
  ~ temp item = pop(all)
  { item :
    // output a choice that will modify the creation variable and divert to finish
    <- make_choice(cup, item, next_step)
    -> loop // jump to restart the loop
  } // if no items are left in the "all" list, we exit the loop
  + [Start again] -> brew_process.first // adds an option about the looped choices
-> DONE
```

The recipe testing and feedback is also very rudimentary and can be improved upon. It could be its own knot/function with more internal rules. If we're making drinks for a customer like in Coffee Talk, we may want to add logic that tests whether we made the drink they wanted. This can be done with knot parameters and list variables too.

## Conclusion
Using lists, variables and threads, it's possible to offer choices to combine items and validate the combinations against pre-defined recipes. The concepts demonstrated in this document can be used as the basis for crafting or inventory puzzle systems. 