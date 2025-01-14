+++
title = 'Today in Code Team - January 11, 2025'
description = 'Your semi-regularly updated blog from the code team.'
date = 2025-01-11T00:00:00Z
authors = ["lincoln", "drake", "sean", "chloe", "ethan"]
tags = ['frc', 'code', 'drive', 'vision', 'router']
+++

## Overview

After ongoing issues with the Kotlin language server (which appears to be unmaintained), Drake made the decision to switch the robot code back to Java. The team spent time converting the existing Kotlin code and adapting to Java. While waiting for the chassis, Drake implemented button mapping and control code for driving the robot. A subsystem manager was briefly explored but ultimately deemed too complex for our needs.

When the test chassis was ready, we deployed the code and attempted to drive it. The drive motors exhibited erratic behavior (spasming) while the turn motors remained unresponsive. We suspect issues with the feedback sensors for the turn motors, likely due to significant API changes in the 2025 update.

On the vision front, Lincoln got Neovim configured on the Raspberry Pi. We discovered the camera outputs SRGGB10_CSI2P raw format, but we're currently using YUV420 as a workaround. Chloe set up a DevContainer development environment and committed code that handles Y, U, and V planes and strides from camera buffers, created an RGB value buffer, and implemented a YUV to RGB converter.

Sean's java code:

![Seans's java code](/blog/today-in-code-team/2025/assets/jan11-sean-code.png)


## Thing of the day

![Water game](/blog/today-in-code-team/2025/assets/jan11-water-game.png)
