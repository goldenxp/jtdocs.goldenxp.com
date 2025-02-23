---
title: Verse Quickstart in UEFN
parent: Verse
description: Jump right into using Verse with UEFN
---

# Verse Quickstart in UEFN

Let's get started with Verse coding in UEFN.

Use the Epic Games Launcher to set up the Unreal Editor for FortNite (UEFN).  

Start UEFN and make a new Blank project. 

Go to the Verse menu in the top menu and open the Verse Explorer. It should open up next to the Outliner tab. 

![Create New Verse File via right-click context menu]({{ site.baseurl }}/assets/img/Verse_QS01.png)

Right-click the Content folder and click `Create New Verse File`. A wizard should pop up.

For this exercise, we will create an empty Verse device.

> Devices are part of the Creative toolkit and can be used to add various functionality to your Fortnite Islands. Verse devices let you run code in .verse files and declare editable properties that can be set in the editor.

Double-click the newly added Verse file to open it in VS Code. 

We will now make a simple device that hides the player HUD. Clear any contents in the Verse file and write the following code:

```
using { /Fortnite.com/Devices }
hud_hider := class(creative_device):
    OnBegin<override>():void =
        Print("Hiding HUD")
```

With the above, we're making a new class called `hud_hider` that extends `creative_device`; this makes our own custom Verse device that we can use in UEFN. To use the `creative_device` code, we need to import the module it lives in which is `/Fortnite.com/Devices`. In order to test our device, we will override `OnBegin` which is a function that runs when the Island starts. We will print a phrase that will show up in a message log.

Save the Verse file and jump back to UEFN. In order to use our `hud_hider` device, we will need to update the project with what we just wrote. Jump to the Verse menu and click `Build Verse Code` or use the shortcut Ctrl + Shift + B.

![Verse class is visible in Content Browser]({{ site.baseurl }}/assets/img/Verse_QS02.png)

A `hud_hider` Verse Class will show up in the Content Browser.

Drag and drop this in the level and we will run our game to preview it.

Click "Launch Session" and save any files if needed. Fortnite will now start up and load up the island based on your UEFN project. It will start in Game Mode (as opposed to Edit Mode). You should see the phrase print just once (up in the top-left) confirming that the message only shows up when the game starts.

Flip back to VS Code. You'll be juggling UEFN, Fortnite and VS Code quite often so it's best to get used to this. What we'll do now is hide all the UI with Verse only. It's possible to hide all UI using just UEFN's Island settings but we will endeavor to use Verse as much as possible in these Verse docs.

We'll import the `/Fortnite.com/UI` module and get the Playspace's hud controller. This gets us the HUD Controller Device that controls the visibility of various UI elements in Fortnite using its ShowElements, HideElements and ResetElementVisibility functions. We'll be using HideElements which takes in an array of type hud_element_identifier.

There are many Subclasses of this type, with each one representing a specific part of the UI. 

We'll make an array called Identifiers that has one element for `creative_hud_identifier_all`.

```
using { /Fortnite.com/Devices }
using { /Fortnite.com/UI }

hud_hider := class(creative_device):
    OnBegin<override>():void =
        Print("Hiding HUD")
        HUDController := GetPlayspace().GetHUDController()
        Identifiers := array:
            creative_hud_identifier_all{}
        HUDController.HideElements(Identifiers)
```

Jump back to UEFN and click Push Changes to update the Verse code. This will terminate the running game and start it back up. If you were disconnected for some reason, click Launch Session again.

Now when you switch back to Fortnite, you will notice the game's HUD is gone. 

![Fortnite game with (near) empty HUD]({{ site.baseurl }}/assets/img/Verse_QS03.png)

Well almost! There are three resource indicators on the bottom. We need to modify our array to include those as well. Modify your Verse code to match the following.
```
Identifiers := array:
    creative_hud_identifier_all{}
    hud_identifier_world_resource_wood{}
    hud_identifier_world_resource_stone{}
    hud_identifier_world_resource_metal{}
```

Notice that the Verse device object is actually visible in the game. We can hide that too. Just call the device's Hide function and it'll make the computer console prop invisible.

![Fortnite game with (near) empty HUD]({{ site.baseurl }}/assets/img/Verse_QS04.png)

The final code looks like this.

```
using { /Fortnite.com/Devices }
using { /Fortnite.com/UI }

hud_hider := class(creative_device):
    OnBegin<override>():void =
        Print("Hiding HUD")
        HUDController := GetPlayspace().GetHUDController()
        Identifiers := array:
            creative_hud_identifier_all{}
            hud_identifier_world_resource_wood{}
            hud_identifier_world_resource_stone{}
            hud_identifier_world_resource_metal{}
        HUDController.HideElements(Identifiers)
        Hide()
```

In this short guide, we've seen how we can compile and run Verse code using UEFN. Verse devices are very powerful and can enable complex relationships between other creative devices.
