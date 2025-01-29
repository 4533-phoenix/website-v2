+++
title = 'Today in Code Team - January 27, 2025'
date = '2025-01-27T00:00:00Z'
authors = ['lincoln']
tags = ['frc', 'code', 'vision', 'drive']
+++

## Overview

Drake, Sean and Mr. Cutter set up and cleaned the drive carpet and set up information for path-planned and then tested and debugged/fixed the path-planning code.

Chloe redid the ML subsystem to work with our new subsystem structure.

![](/blog/today-in-code-team/2025/assets/jan27-ml.png)

## Major vision improvements

We were out of our workshop for a week due to snow.
But with e-learning, I was able to use that time and made a lot of improvements to vision:

### No more libcamera!! (mostly)
We're now using [nokhwa](https://github.com/l1npengtul/nokhwa), a Rust camera library, instead of [libcamera bindings](https://github.com/chalkydri/libcamera-rs).
nokhwa exposes a much nicer API and should allow for development on Windows and Mac (with some exceptions).
I'm currently working to upstream some fixes and a libcamera backend for nokhwa, which is unfortunately required to support newer Pi camera modules.
The upcoming 0.11 release introduces multi-camera and async support, which will be very skibidi.

 > **AD:**
 > Pasted is a free, privacy respecting frontend for Pastebin.
 > It uses no JavaScript and is as simple as possible while supporting as many features as possible.
 > [Try it now!](https://pasted.drakeerv.com)

### No more Actix!
I got rid of the Actix dependency.
It seems like it's making async more of a pain, rather than easier to understand.
The `Subsystem` trait is now much simpler, using pure [Tokio](https://tokio.rs).

### Rerun!!!
I finally got [Rerun](https://rerun.io) set up.
Rerun is a very fancy visualizer/logging system for computer vision, which should be useful for debugging; but it's mostly just for looks right now.
We'll need somebody to learn how to use it well.

## Thing of the day

[![](/blog/today-in-code-team/2025/assets/jan27-imagine.png)](https://cxx.rs/context.html?highlight=Imagine#geometric-intuition-for-why-there-is-so-much-opportunity-for-improvement)
