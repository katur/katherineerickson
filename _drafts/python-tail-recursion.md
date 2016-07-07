---
layout: post
title:  "Python Tail Recursion bug"
tags: python
---

Have you ever had a moment where you wondered how you could program in a
language for years, and not understand some of its most basic features?

For my [Recurse Center](http://recurse.com) pairing interview, I made
Connect Four in python. I separated my model (holding the game state
and game logic) from my views (I had a separate GUI view and command line
view for playing the game, plus a logging view that simply logs events).

For the two to communicate without the model being aware of which (if any)
views are listening, I used a publish-subscribe (pubsub) pattern.
Views "subscribe" to the model, which means that they assign particular
functions to be called when a particular action occurs.
The model "publishes" that an action occurred, which results in any
subscribed callbacks being called.

In essence, what happens throughout the game is:
- something happens in the model (e.g. a disc being placed at a particular spot
  in the board)
- the model informs the views (indirectly, via pubsub)
- the view process this information (e.g., by coloring a square)
- some user action (e.g. the next player playing a disc in some column)
  results in the view calling the model directly.

So the game loop is: model - pubsub - view - model - pubsub - view
(essentially, model - view - model - view).

So what's the problem with this?

**Hint:** While I didn't have issues with the GUI view, after playing about
12 games with the command line view, I got a stack overflow!

**Answer:** Python doesn't implement tail recursion.
