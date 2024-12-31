---
title: Ink Text Logs
parent: Ink
description: How to display a text log (e.g. journal) in Ink
---

{% include ink.html %}

# Ink Text Logs
Learn one method of setting up a log of text entries

## Intro
I was playing the Atom Eve [visual novel RPG](https://store.steampowered.com/app/2060870/Invincible_Presents_Atom_Eve/) soon after its initial launch and it has a Journal feature which collects text entries in a scroll list. It both captures the main character's thoughts as well as summarizes major events that have happened so far. It can also serve as a refresher of the game's story for a returning player. Also, when it populates, it highlights the importance of the current moment or plot beat. 

![Screenshot of Atom Even game with Journal in view]({{ site.baseurl }}/assets/img/Ink_JournalLogAtomEve.jpg)

It got me thinking about how to reproduce something like the game's Journal in Ink. Why? Because the feature contains flavorful written-by-the-writer text and since an Ink story contains the main narrative, it should also contain other supporting narrative elements. But how could one go about doing this?

## Accessing the Log
Let's solve the simpler problem first: how to make an accessible yet separate list of text entries i.e. the log. We will assume we're **not** using a game engine and are just working in pure Ink. This means the opening of the log needs to be a choice (aka [options](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#2-choices)) offered to the player when choices are possible. Also, the log will need some way of returning to same set of choices. We will need to employ [Tunnels](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#1-tunnels) to "enter" and "exit" the log which will need its own Knot. Each choice set will need to be a stitch and will also contain an "extra" sticky choice that holds the tunnel to the log. The result would look like this.

```ink
The alarm clock rang.
->choice1
=choice1
+ Wake up -> wake_up
+ Go back to sleep -> sleep_in
+ [Player: Open Log] -> log -> choice1
-> DONE

=wake_up
-> DONE

=sleep_in
-> DONE

=== log
Opening Log
->->
```

The log knot can have its own options so if we didn't want it to automatically return, we could set it up so it needed user input to go back. 

## Collecting Text Entries
Now for the juicier part, collecting text entries for the log. It's juicy because Ink does not support traditional arrays so any solution involving a data collection of strings is not happening. Let's look at some other ways to tackle this. 

### Method 1: Simple List
If the log is static and linear, perhaps for something like a kinetic novel (no branching), making a list of text entries in Ink is actually a trivial affair. You would use a variable to keep track of how much of the log was unlocked and only display the necessary text entries.
```ink
VAR log_index = 0
=== log
Opening Log. Index is {log_index}
~ log_index ++ // This would be incremented during the story as needed
{ log_index > 0 : I tossed and turned. }
{ log_index > 1 : I woke up. }
{ log_index > 2 : I left the house feeling good. }
{ log_index > 3 : The train was crowded and the bus was late. }
->->
```

The above (and any similar variations) would only work if our journal was pretty much a simple list of text entries with no variance. Just increment the index with the story beats and you're set. This would definitely not work with branching narratives that could have different flows. 

### Method 2: String Concatenation
What is a string but an array of characters. You could just make the log a string and append strings to it.

```ink
VAR log_entries = ""

Groundhog Dazed 
-> day_start

=== day_start
Another day begins. What to do? -> choice1
=choice1
+ Go to work
    I took the bus to the office.
    ~ log_entries += "I went to work.<br>"
+ Stay at home
    I called in sick.
    ~ log_entries += "I stayed at home.<br>"
+ Go to the park
    Life is short; I walked to the park.
    ~ log_entries += "I visited the park. Felt good.<br>"
+ [Player: Open Log] -> log -> choice1
- The day has ended.
-> day_start // loop back to a new day

=== log
{log_entries} // Displays the string with all the log entries
->->
```

If you run the above and select different options, your entries get updated like so.  
> I went to work.
>
> I stayed at home.
>
> I visited the park. Felt good.
>
> I stayed at home.

This works but despite its flexibility, I won't say it's excellent. For one, you'll need to add line breaks to each entry so they get broken up when displayed. 

### Method 3: Using Ink Lists
Ink [Lists](https://github.com/inkle/ink/blob/master/Documentation/WritingWithInk.md#1-basic-lists) are their own special beast. If our log entries are ordered and do not repeat, using Ink Lists is a great way to go because it can lead to richer features. The code below makes a list and only shows specific text entries if the values are found in the list. The values are toggled on with the `+=` operator and we check if they're active using the `?` operator.
```ink
LIST logs = n1, n2, n3, n4, n5, n6

~ logs += n1    // flip n1 on
-> log ->       // test the log, return
~ logs += n3    // flip n3 on
-> log ->
~ logs += n6    // flip n6 on
-> log ->

=== log
Showing Log: 
{ logs ? n1 : I did the thing. }
{ logs ? n2 : I did another thing. }
{ logs ? n3 : I did yet another thing. }
{ logs ? n4 : I did X thing. }
{ logs ? n5 : I did Y thing. }
{ logs ? n6 : I did Z thing. }
..............
->->
```

The above output is

> Showing Log:
> 
> I did the thing.
> 
> ..............
> 
> Showing Log:
> 
> I did the thing.
> 
> I did yet another thing.
> 
> ..............
> 
> Showing Log:
> 
> I did the thing.
> 
> I did yet another thing.
> 
> I did Z thing.
> 
> ..............

Because lists are both variables and boolean sets,  we can do a lot with them. For example, we can make a new `log` list variable to keep track of new states on the text entries. Below, we create an unread status and add an option to "read" all available entries. 

```ink
LIST logs = n1, n2, n3, n4, n5, n6

// declare the list for unread logs
VAR logs_unread = ()
// mark all log entries as unread
~ logs_unread = LIST_ALL(logs)

~ logs += n1    // flip n1 on
-> log ->       // test the log, return
~ logs += n3    // flip n3 on
-> log ->
~ logs += n6    // flip n6 on
-> log ->

=== log
Showing Log: 
{ logs ? n1 : {logs_unread ? n1 : *} I did the thing. }
{ logs ? n2 : {logs_unread ? n2 : *} I did another thing. }
{ logs ? n3 : {logs_unread ? n3 : *} I did yet another thing. }
{ logs ? n4 : {logs_unread ? n4 : *} I did X thing. }
{ logs ? n5 : {logs_unread ? n5 : *} I did Y thing. }
{ logs ? n6 : {logs_unread ? n6 : *} I did Z thing. }
+ [Mark all as Read] 
    ~ logs_unread -= logs
    ->log
+ [Ignore/Go Back] ->->

```
Each text entry added an `*` symbol if it's found in `logs` and also found in `logs_unread`. When we perform "Mark All As Read," we remove all entries found in `logs` out of `logs_unread`.

![Inky snippet of logs_unread list in action]({{ site.baseurl }}/assets/img/Ink_JournalLogUnreadStatus.png)

There are some limitations to this method but its functionality is on par with Atom Eve's Journal feature. The thing to keep in mind is the log order is not determined by the list variable additions but rather by the order of knot's text.

## Conclusion
These methods are just scratching the surface, I think. Other features like functions, loops and switch statements open up more ways of making text logs. Robust solutions can be built using lists+loops and threads as well. 