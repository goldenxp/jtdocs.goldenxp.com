---
title: Tabular Data in Ink
parent: Ink
description: Set up and use comma-separated values in Ink
---

{% include ink.html %}

# Tabular Data in Ink
Or how to write and use CSV in Ink  

## Intro
With a little imagination, it's possible to maintain tabular data within Ink itself. The impetus for the following setup is having the Ink script be the source of truth. This means that the game's data is primarily stored in Ink as opposed to an external source. 

## Writing the Data
We'll be using comma-separated values for the data. I wrote some RPG stats directly in Ink.

```ink
/*
name,age,power,hp,mp
Nimbus,35,Sword,120,30
Eris,26,Magic,80,80
Bullet,45,Gun,200,10
Chifa,32,Boxer,90,60
*/

Once upon a time . . . 
```

In a real world scenario, these may get written in an external spreadsheet software and copy/pasted into the Ink file. The key thing here is to surround the CSV data with box comments so that the Ink compiler does not try to use it. 

But if the CSV data is commented out, how does it get used?

## Using the Data
In order to use this data, we'll convert them into Ink functions using Regular Expressions. Each row of the data table will make a new function and each column will be a parameter value. The use of capture groups will be crucial in this endeavor. Unfortunately, Inky uses Ace which has some limitations with its Search and Replace functionality. So the following step is best done in a more robust text editor. 

![Screenshot of CSV in Notepad++ with Replace window]({{ site.baseurl }}/assets/img/Ink_CSV.png)

Basically our data is duplicated and a Find/Replace in Selection operation with RegEx is done on it. Our Find field will capture each value and our Replace field will generate the necessary function. Here's the breakdown.

```
// Find
(.+),(.+),(.+),(.+),(.+) // captured as $1 for name through to $5 for mp

// Replace
=== function GetData$1 // starts with function declaration composed of "GetData" and the name
\( // Escaped bracket to start parameter setup
ref v1, ref v2, ref v3, ref v4, ref v5 // 5 params, 1 for each row value, all by reference
\)\n // Close parenthesis and insert newline
~v1="$1"\n // set v1 reference to the name string captured in $1 then new line
~v2=$2\n // set v2 to value in $2, then new line
~v3=$3\n~v4=$4\n~v5=$5\n // repeat for the rest
// Since it's a single-line field in my Notepad++ editor, the above is input as one long entry
=== function GetData$1\(ref v1, ref v2, ref v3, ref v4, ref v5\)\n~v1="$1"\n~v2=$2\n~v3=$3\n~v4=$4\n~v5=$5\n
```

The result looks like this.

```ink
/*
name,age,power,hp,mp
Nimbus,35,Sword,120,30
Eris,26,Magic,80,80
Bullet,45,Gun,200,10
Chifa,32,Boxer,90,60
*/

=== function GetDataNimbus(ref v1, ref v2, ref v3, ref v4, ref v5)
~v1="Nimbus"
~v2=35
~v3=Sword
~v4=120
~v5=30

=== function GetDataEris(ref v1, ref v2, ref v3, ref v4, ref v5)
~v1="Eris"
~v2=26
~v3=Magic
~v4=80
~v5=80

=== function GetDataBullet(ref v1, ref v2, ref v3, ref v4, ref v5)
~v1="Bullet"
~v2=45
~v3=Gun
~v4=200
~v5=10

=== function GetDataChifa(ref v1, ref v2, ref v3, ref v4, ref v5)
~v1="Chifa"
~v2=32
~v3=Boxer
~v4=90
~v5=60
```

We can jump back to Inky and we'll notice some errors because some data values were used as variables (and not string values). I intended to use a list declaration for RPG classes. Just add the following to resolve the errors.

```ink
LIST classes = Sword,Magic,Gun,Boxer
```

Now, we can use these functions to store values as usable variables. We may not need all data values so we can use throwaway variables with the function.

```ink
Once upon a time . . . 
~ temp bHP = 0
~ temp dummy = 0
~ GetDataBullet(dummy, dummy, dummy, bHP, dummy)
{ bHP > 100 : Bullet was super strong. }

~ temp nHP = 0
~ GetDataNimbus(dummy, dummy, dummy, nHP, dummy)
{ nHP > bHP : Nimbus was stronger. | Nimbus was not as strong. }
```

The performance impact of functions as data providers remains to be seen. There might be ways to mitigate this by outputting switch-case blocks instead of functions. Regardless, the above approach illustrates an acceptable way of defining data in Ink and converting the data into a form that can be used in Ink. 

## Conclusion
Consider this: the CSV data can also be initially defined in an external application and brought into Ink to rest as both data and functions. This data could be further edited in Ink and exported back out to spreadsheet software for further manipulation or value testing. This way the Ink file is still considered the primary holder of the data and a source of truth for the project. 

Also, the steps in the process can be consolidated or automated with other scripting languages. 

> Batch processing data to output Ink files opens up many new possibilities. 

In the case of tabular data definitions, their output could reside in a separate Ink file (that gets included by other Ink files) allowing for easier back and forth conversions. Once again, the main advantage here is that the Ink script will be playable with minimal reliance on the game engine. 