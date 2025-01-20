+++
title = 'Today in Code Team - January 17, 2025'
description = 'Your semi-regularly updated blog from the code team.'
date = 2025-01-17T00:00:00Z
authors = ["sean"]
tags = ['frc', 'code', 'drive', 'vision', 'pid']
+++

## Overview

Today focused on two main areas: PID tuning for the test chassis and vision system improvements.

Drake, Sean, and Ethan started tuning the PID loops for the velocity motors on the SWERVE drives. Drake fixed the bugs from last meeting at home and found the issue in having values in the optional section of the YAGSL code which was breaking the roational motors ability to rotate the way they should. We finished tuning the PID loops for rotation and will begin on tuning velocity motors next meeting.

The image shows a line chart we used to help tune the PID loops for the rotation motors on the test chassis.

![PID rotation tuning line chart](/blog/today-in-code-team/2025/assets/jan17-rotation-tuning.jpg)

Drake input an incorrect value and it caused the wheels to have a robot equivalent of a seizure.

![Rotation motor heading](/blog/today-in-code-team/2025/assets/jan17-rotation-seizure.jpg)

On the vision front, Lincoln and Chloe worked on debugging the Vision code.

## Thing of the day

![sunbathing rock-tupus](/blog/today-in-code-team/2025/assets/jan20-sunbathing-rock-tupus.jpg)