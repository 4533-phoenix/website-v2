+++
title = 'Today in Code Team - January 15, 2025'
description = 'Your semi-regularly updated blog from the code team.'
date = 2025-01-15T00:00:00Z
authors = ["drake"]
tags = ['frc', 'code', 'drive', 'vision', 'pid']
+++

## Overview

Today focused on two main areas: PID tuning for the test chassis and vision system improvements.

Drake, Sean, and Ethan worked on debugging the swerve drive PID loops. We're seeing issues where the wheels know their target direction but aren't reaching it correctly - they either undershoot/overshoot or exhibit erratic behavior. While the code correctly calculates desired wheel orientations, there appears to be a disconnect between the commanded positions and actual wheel movement. We suspect this could be related to how the PID controllers are interpreting feedback from the turn encoders.

![PID Tuning HowTo](/blog/today-in-code-team/2025/assets/jan15-pid-tuning.png)

On the vision front, Lincoln made significant progress with our NetworkTables implementation. The `minint` client now supports array data types, which will be crucial for sending pose data. Additionally, we've integrated the AprilTag C library's built-in pose estimation code, which should give us more reliable positioning data compared to our previous approach.

![Previous AprilTag Detection](/blog/today-in-code-team/2025/assets/jan15-previous-apriltag-detection.gif)

## Thing of the day

![Rust vs Python typecast](/blog/today-in-code-team/2025/assets/jan15-rust-typecast.png)