+++
title = 'Today in Code Team - January 13, 2025'
description = 'Your semi-regularly updated blog from the code team.'
date = 2025-01-14T00:00:00Z
authors = ["lincoln"]
tags = ['frc', 'code', 'drive', 'vision']
+++

## Overview

We're starting to make some major progress.

![](/blog/today-in-code-team/2025/assets/jan13-drake-crap-1.png)
![](/blog/today-in-code-team/2025/assets/jan13-drake-crap-2.png)
![](/blog/today-in-code-team/2025/assets/jan13-drake-crap-3.jpg)

Drive code is way ahead of schedule, with the test chassis nearly operational.
Drake switched to 2025 YAGSL and started testing the code on the test chassis (image above) after gathering necessary data required for it to work closer to correctly.
Sean read through most of the YAGSL documents (to be completed) and learned about the chassis and assisted in data gathering.

Vision is now detecting AprilTags, though actually finding and using positions is a WIP.
Chloe got YUV420 to RGB8 conversion working.
Lincoln made some rough changes to the async task handling so the capriltags subsystem would work properly.
We'll need to rewrite some of the framebuffer and subsystem message passing code.
We also need to implement pose estimation and start publishing to NetworkTables by the end of this week.

## Thing of the day

![WHYYYYYYYYYYYYYYYYYYY](/blog/today-in-code-team/2025/assets/jan13-bazel-rust-analyzer.png)
