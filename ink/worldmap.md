---
title: Making a World Map in Ink
parent: Ink
description: How to build locations and routes in Ink
---

{% include ink.html %}

# Making a World Map in Ink
Learn how to setup locations and routes in Ink

## Intro
This document will demonstrate one way of making a navigable world map in Ink.

There are a few approaches one can take. For example, one can use variables for tracking locations and destinations. Choices can modify variables to change the current location and different content can be used per variable value. With the help of powerful Ink Lists, this approach can be taken further. 

```ink
LIST cities = SF, Oakland, Fremont, Sunnyvale

VAR current_city = SF

Where to next?
+ Go to Oakland
    ~ current_city = Oakland
+ Go to Fremont
    ~ current_city = Fremont
- 
I'm in { current_city }.
{ current_city :
- Oakland: Oh nice lake!
- Fremont: Huge city!
}
```
Following the above example, you could use another variable to keep track of which cities you’ve been to. 

This approach can work if the locations have minimal differences but once the locations get more distinct or complex, it becomes unwieldy to write with. There’s also route management to worry about. Fortunately, there is another method that can help mitigate such issues and that’s setting locations as knots.

## Locations as Knots
The "knots are locations" approach uses the content in a knot to describe the location and determine possible events at those locations. Diverts can be used to route to other location knots. Choices and conditions can be used to define which routes the player can take. 
```ink
-> SF
===SF
SF is nice but let me explore other cities! Where should I go?
+ Go to Oakland
   A short trip on the BART.
   -> Oakland
+ Go to Sunnyvale
   A long trip on the CALTRAIN.
   -> Sunnyvale

===Oakland
Oh boy! Check out that Lake!
-> DONE

===Sunnyvale
Yahoo! I'm in Sunnyvale!
-> DONE
```

The above method allows each location to have its own content, possible routes and content for the route itself. In the example, the content between the option and the divert describes the journey of the current location to the next one. 

In case routing gets more complex, it might be necessary to make route content their own stitches. Different methods of traveling are introduced below.
```ink
-> SF
===SF
+ [BART to Oakland]  -> Oakland_via_BART
+ [RideShare to Oakland] -> Oakland_via_RideShare

= Oakland_via_BART
I got a ticket at the counter.
The journey was oddly uneventful.
-> Oakland

= Oakland_via_RideShare
Traffic was awful but the car was comfy.
It took an hour but I made it.
-> Oakland

-> DONE

=== Oakland
Ah, Oakland. Time for some good BBQ!
-> DONE
```

Since the routes are now stitches, diversion is clearer and they can also be tracked with `knot_name.stitch_name`. Continuing from the above Ink snippet, the following can be used to test how the journey could have happened. 
```ink
=== Oakland
Ah, Oakland. Time for some good BBQ!
{ TURNS_SINCE(-> SF.Oakland_via_BART) == 0 : After riding the BART, I need some! }
-> DONE
```
As mentioned in the official Ink docs, `TURNS_SINCE(-> name) == 0` is a common check and is often made into a helper function `came_from`. It may not hurt to do the same here and call it something like `via(-> name)`. You could use just the stitch address i.e. just `{SF.Oakland_via_BART}` but that may not work as intended, especially if you can navigate back and forth between locations. It would test the number of uses as opposed to if it was just used prior. 

## Managing Routes
Route management can be driven by many rules. If these rules universally apply to all locations, we can use knot parameters and Ink [Threads](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#2-threads) to assemble our route choices dynamically.
```ink
-> SF
=== SF
I'm in SF now.
Time to drive.
<- make_route(-> SF, -> Oakland, "Go to Oakland")
-> DONE

=== Oakland
I'm in Oakland now.
Time to drive.
<- make_route(-> Oakland, -> SF, "Go to SF")
-> DONE

=== make_route(-> from, -> to, desc)
{ from != to :	// Ensure we cannot travel to the same location we are currently in
+ [ {desc} ]	// Output the desc string as choice text
    -> to	// Divert to destination
}
```
Invoking `make_route` is a way to define properties in each knot location. These can be used to generate a choice based on custom rules. For example, we can add fuel as a variable and specify fuel costs everytime we invoke the knot. This cost can then be used to modify how a route could take place.
```ink
VAR fuel = 10

-> SF
=== SF
I'm in SF now.
Time to drive.
<- make_route(-> SF, -> Oakland, "Go to Oakland", 4)
-> DONE

=== Oakland
I'm in Oakland now.
Time to drive.
<- make_route(-> Oakland, -> SF, "Go to SF", 4)
-> DONE

=== make_route(-> from, -> to, desc, cost)
{ from != to :
  + [ {desc} ]
    ~ fuel -= cost // Reduce fuel
    { fuel <= 0 :
        You ran out of fuel. You're stuck here forever.
        -> END
    - else :
        You have {fuel} units left. You made it!
          -> to // Divert to destination
    }
}
``` 

## Conclusion
It's possible to take a combination of the above techniques and make something more robust. 
* Lists can be used for tracking locations and location relationships
* Knot parameters can be used for defining location properties
* Threads can generate dynamic sets of choices based on custom rules.

To conclude, it's possible to build a usable map in Ink and have your player travel from point to point, giving the impression of a living world.


