---
layout: post
title:  WIBTW - Tilt Maze Game
categories: [agentic AI, LLM, game]
excerpt: I wanted to get my son exposed to how to use LLMs more elegantly, so we made a game.
---

For "What I built this week" (WIBTW), I created a Tilt Maze game—really just a demo to show what's possible. My main goal was to expose my son to a more elegant way of using LLMs, so we decided to build this together. 

The biggest lesson I wanted him to learn is that you don't just "one-shot" a project. Instead, you start by planning. You break that plan down into manageable sessions, creating a master plan and handoff documents so you can start fresh each time. 

This approach makes the entire process much more efficient. It keeps you within scope, limits "context rot" in the AI's memory, and honestly, just makes development a lot easier. 

If you want to check out the final product, here is the [app](https://apps.benneely.com/tilt-maze) and the [source code](https://github.com/neely/apps/blob/main/tilt-maze.html).

---

### The Initial Prompt

We started with this prompt:

```text
We are going to make a game to play on my phone, but we are going to serve it as a static html file. We think the game would be a maze and you have to tilt the phone to make a ball roll around in the maze to get from one end to another. There will be levels, make them fun with some silliness or obstacles, and the goal is to see your highest level. I want the progress to be saved, either on local memory or as a json or something I can download.

First, help me plan the steps to do this, which we will save as a handoff.
```

### The Response & Master Plan
  
Response: Here's a full plan for the tilt-maze game, structured as a handoff document we can refer back to as we build each piece.

![Claude's Master Plan](images/maze_game_handoff_plan.svg)

Here's the full breakdown of what we'll build and in what order:


* **Phase 1 — Core engine:** We'll build the HTML canvas game loop: reading DeviceMotionEvent (the phone's accelerometer) to get tilt angles, translating that into a velocity + gravity physics model, and detecting collisions between the ball and maze walls. On iOS, tilt access requires a user gesture to trigger a permission request — we'll handle that with a "tap to start" button so it works from a saved HTML file.
* **Phase 2 — Level design:** Mazes will be defined as simple JSON grids (walls as a 2D array of booleans + cell type flags). Each level adds a fun twist: slippery ice floors, sticky mud that slows you down, teleporters that zap the ball across the maze, bouncy rubber walls, and eventually moving walls. Higher levels mix these together.
* **Phase 3 — Persistence:** We'll use localStorage to auto-save the highest level cleared every time you beat one. We'll also add a "Download Save" button that exports a tiny JSON file (so you can back it up or transfer it), plus an "Import Save" button to restore it — this is important since saved HTML files sometimes clear localStorage.
* **Phase  4 — Game feel:** Screen flow (menu → game → win screen → back), a high score display, level name titles, confetti or silly animations on win, and some basic sound effects using the Web Audio API (no external files needed).
* **Phase 5 — Single file packaging:** Everything — JS, CSS, level data, all assets — gets inlined into one .html file you can drop onto your phone and open in Safari or Chrome with no server needed.


> **LLM:** *"Ready to start building? We can go phase by phase. I'd suggest beginning with Phase 1 so you can immediately try tilting your phone and see the ball roll, then layer in levels and persistence. Want me to kick off Phase 1 now?"*

---

## Running Notes

For each subsequent step, I would paste in these directions along with the HTML completed in the last session and for the most part it you would just build it out. If it had questions or there were issues we would discuss. 

For instance, Phase 3 started like this (apparently I was asking it to talk like a caveman to save on tokens):

```text
Phase 3: Persistence. We'll use localStorage to auto-save the highest level cleared every time you beat one. We'll also add a "Download Save" button that exports a tiny JSON file (so you can back it up or transfer it), plus an "Import Save" button to restore it — this is important since saved HTML files sometimes clear localStorage. 

Read the html, check for errors, and understand where we are. Respond like caveman with plan before execute.
```

Now the hardest part, and this is true in most larger projects, is that you need to not stray from the current step, skip forward or go backwards (as much as possible). In otherwords, in session 1 we spent a ton of time getting the physics right before moving on. At some point we did come back and remove the walls, and that caused some issues. Point it, if you can have some way to navigate sessions, asking for handoffs between steps and bring only files you need over (like the last version and maybe the master plan) you will have the greatest success, especially with an LLM like Claude that isn't going to do persistent memory by default.

&nbsp;  
&nbsp;  
&nbsp;  
