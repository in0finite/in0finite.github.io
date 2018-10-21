---
layout: post
title: Collection of Unity stupidities
---

When nesting ui elements, put image component on nesting game object – otherwise child elements will become frozen (unable to change anchors)

When scroll view is disabled, it does not work properly => sets width and height of viewport to 0 - as a consequence, all child objects which work with UI elements will... well, not work

Scroll view's content position is sometimes, when the game starts, changed without any obvious reason - you need to add a script to it, which will correct the position

Script execution order is sometimes random, and sometimes not

Unity randomly logs error, on app start, about missing references on null game objects - it doesn't happen in editor

