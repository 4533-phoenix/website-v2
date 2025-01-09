+++
title = 'Today in Code Team - January 8, 2025'
date = 2025-01-08T01:35:05Z
authors = ["lincoln", "drake", "sean", "eda", "chloe"]
tags = ['frc', 'code', 'vision', 'kotlin', 'router']
+++

# Today in Code Team

## Overview

Today was a mix of progress and challenges for the Code Team as we tackled key areas for the 2025 FRC robot. Here's the breakdown:

### **Robot Code Setup**

Drake led the effort to get **WPILib 2025** installed for the team. While the installation process was frustratingly slow due to the school’s throttled WiFi, it eventually worked, and we transitioned to setting up the project. Here's the workflow:

1. **Project Creation:**
   - Initialized a new robot project in **VS Code** using the WPILib tool.
   - Converted the project to Kotlin using the **FRC Kotlin extension**.

2. **Challenges Faced:**
   - The FRC Kotlin extension was outdated for 2024, requiring manual fixes:
     - Updated project configuration from `2024` to `2025`.
     - Upgraded Kotlin to `2.1` in `settings.gradle` to resolve dependency issues.
   - Vendor library APIs had changed since 2024, causing incompatibilities. Resolving these is on the to-do list.

3. **Dependencies Installed:**
   - YAGSL
   - REVLib
   - ReduxLib
   - Phoenix6
   - Phoenix5
   - Studica
   - ThriftyLib
   - MapleSim

4. **Progress:**
   - Subsystems folder and `SwerveDrive.kt` created.
   - `Constants.kt` initialized for global configuration.
   - A live server set up to allow real-time code viewing by teammates.

Where we left today off:
![Where we left off](/blog/today-in-code-team/2025/assets/jan8-left-off.png)

### **Vision System**

Chloe worked on vision-related tasks:

- Flashed a fresh image on a **Raspberry Pi 5** for vision processing.
- Experimented with **Chalkydri** vision software:
  - Successfully built on the Pi in just **2m 43s**.
  - Resolved most camera configuration issues but faced a limitation with a USB camera supporting only MJPEG instead of RGB8.
- Explored alternatives to school-mirrored Raspberry Pi OS repositories.

### **Router Issues**

Lincoln’s day revolved around fixing our router setup:

- The school’s network censorship and throttling of Linux repositories continues to be a bottleneck.
- Tried:
  - Tuning **Zapret** arguments.
  - Switching DNS from Google to Cloudflare (partially successful).
- The team’s new **2025 FRC router** provided local IPs effectively, but it’s clear that router stability needs further attention.

## Key Learnings & To-Do List

### **Takeaways:**
- WPILib 2025 and Kotlin integration is functional but requires adjustments for library API updates.
- Vision builds are efficient, but hardware constraints with cameras need addressing.
- Network bottlenecks demand better solutions—possibly alternative router setups.

### **To-Do:**
- Research updated APIs for 2025 vendor libraries.
- Complete the Swerve Drive subsystem.
- Upload code to GitHub.
- Fix camera issues for vision processing.

## Thing of the day

![Drake got lazy and reused thing of the day](/blog/today-in-code-team/2025/assets/jan8-drake-npm.png)
