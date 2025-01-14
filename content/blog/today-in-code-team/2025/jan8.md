+++
title = 'Today in Code Team - January 8, 2025'
description = 'Your semi-regularly updated blog from the code team.'
date = 2025-01-08T01:35:05Z
authors = ["drake"]
tags = ['frc', 'code', 'drive', 'vision', 'router']
+++

## Overview

Drake and the robot team in set up the 2025 robot project in Kotlin FRC extension after using WPILib to create the project. The FRC Kotlin extension caused some headaches since it was designed for 2024, but we fixed it by updating configurations and Gradle settings. Vendor dependencies like YAGSL and REVLib were set up, and we began structuring the robot code. Vendor Dependencie's API changes remain an issue for next time.

The router is still a problem. Lincoln tried tweaking zapret args and switched DNS to Cloudflare, but the school network remains a major bottleneck. He continued on to compile vision code on the Pi 5 and succesfully got it opening a usb cam but nothing else. This also came with the usage with the new radio for this FRC season.

Here's where we left off:

![Where we left off](/blog/today-in-code-team/2025/assets/jan8-left-off.png)

## Thing of the day

![Drake got lazy and reused thing of the day](/blog/today-in-code-team/2025/assets/jan8-drake-npm.png)
