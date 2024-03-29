{"description":"A look into my preference to good UI design, placing things according to reach and visibility."}

> This is a subject of interest because I'm currently writing an Android app for myself.

## Intro

I don't enjoy the design of most mobile apps.
UIs seems to be designed based on aesthetics and not usability.
Reaching around the screen is often very uncomfortable and cumbersome.

I'm looking at usability from two aspects: affordance and comfort.

This article are entirely from my point of view;
it is what might work for people similar to me, 
however this does not consider any wider research.

## Design Considerations

### Reach

The most annoying feature I encounter everyday are interactive elements that I can't physically reach.
I normally operate my phone with one hand and my thumb cannot comfortably reach the whole screen.

Assuming one hand usage with thumb interacting with the screen:

```
 Screen reach comfort:                                                              
 Right hand grip               Both hands overlaid                                 
 +-------------------------+   +-------------------------+                          
 |                         |   |                         |                          
 |                         |   |                         |                          
 |                         |   |        TERRIBLE         |                          
 |      TERRIBLE        ---|   |                         |                          
 |                -----/   |   |                         |                          
 |          -----/         |   |          -- --          |                          
 |       --/               |   |       --/     \--       |                          
 |     -/                  |   |     -/           \-     |                          
 |  --/                    |   |  --/               \--  |                          
 |-/                       |   |-/                     \-|                          
 |                         |   |                         |                          
 |         GOOD            |   |          GOOD           |                          
 |                         |   |                         |                          
 |                         |   |                         |                          
 |                      ---|   |---                   ---|                          
 |                  ---/   |   |   \---           ---/   |                          
 |                  |      |   |      |           |      |                          
 |                 /  BAD  |   | BAD   \         /  BAD  |                          
 |                 |       |   |       |         |       |                          
 +-------------------------+   +-------------------------+ 
```

From the diagram the good comfort reach zone is rather small.
Anything that is interacted with very often must be in the good comfort area.

The bottom corners of the screen are difficult but not impossible to reach.
The top is and absolute pain to reach, requires shuffling the entire phone across the hand,
a manoeuvre that sometimes results in dropping the phone on my face. Terrible.

My rules:

* Most interactive content should be in the good zone.
* Only the most rarely interacted-with items can be allowed in the terrible zone.
* Only occasionally interacted items should be in the bad zone.


### Visibility

My thumb tends to hover over the bottom half of the screen, making is slightly less visible.
Therefore the best zone for viewing is the top half, especially content that is quickly scanned and dismissed.

My thumb is most likely to be blocking any area with buttons.
This makes the rest of the screen better for information that changes often due to interactions.
Also as a result it is better to have buttons at the bottom so less of the screen is blocked by reaching.

The bottom edge of the screen is the most likely to be obscured so using buttons in that area may incur a tiny delay.


## Relevance in Menu Design

### Hamburger Menu / Top buttons

The most common menu design on android is the hamburger menu.
The hamburger menu positioned at the top of the screen in the terrible comfort zone.
Same issue with other buttons placed at the top in the title bar space including the 3 dot drop down menu.
Might be ok for a very rarely used menu, however should be avoided if there are other options.


### Edge Swiped Menu

These days it is common to summon the hamburger and other menus via an edge swiping gesture.
Although this is an improvement over just the hamburger, I do not find this satisfactory.

Swiping actions rarely have any affordance, there is nothing on the screen to indicate it as a possibility.
A tiny handle on the edge of the screen linked to the menu button might work, but tends interfere with content.

Swiping also has a tendency to fail when on the other side of the screen;
the phone gets squashed into the hand and the touch gets confused.
The bigger the screen the worse it gets, to the point where you can no longer reach the edge to swipe.

Another problem is that the gesture often gets confused with swiping the screen content, the Google Maps app is a 
prime example.

All these problems lead to edge swiping being unreliable, it does not work as a standard interaction for me.


### Tab Bar

Apple's bottom of the screen [tab bar](https://developer.apple.com/ios/human-interface-guidelines/ui-bars/tab-bars/)
appears to be the most comfortable menu for me.

* It is rather obvious, covering the whole bottom edge of the screen.
* It is easy enough to interact occasionally.
* It provides immediate access to the menu, it is not a hidden menu.

The obvious big difference is that it is a small direct menu, not a large hidden menu as discussed before.
However this is probably a good thing, forces the design to stay simple and high level.
An app that cannot be simplified to a few options may need to think differently.

It turns out that Google realised that this is a good approach and updated 
[their guidelines](https://material.google.com/components/bottom-navigation.html#bottom-navigation-style) earlier this year.


## Outro

So far I've only considered menus, eventually I'll write about other parts of the UI and how to get the happiest 
interactions with them.
