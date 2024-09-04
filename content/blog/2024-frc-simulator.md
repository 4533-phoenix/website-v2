+++
title = 'Simulating a FRC robot'
description = 'FRC simulation in godot to train drivers virtually.'
authors = ['drake']
tags = ['frc', 'simulation', 'godot', 'code']
date = '2024-09-04T02:51:30+0000'
+++

This year, before we were able to finish designing and building our [2024 robot Bothoven](../../about/seasons/2024), I was busy at homee reconstructing the field in godot from the cad models provided by [First](https://www.firstinspires.org/) to make a semi-realistic simulation.

## Building the field
To make a good simulation you first have to start with the environment. In this case it would be the frc field. I first imported the cad model from the onshape page into godot, however, all the textures and colors were gone due to the file format/onshape. To fix this I made scripts in godot to help apply textures that I sourced from online. I grabbed the carpet texture from the manufactures website, images from first, and textures from various online sources. When I was done it looked something like this:

![Field](../assets/2024-frc-simulator/field.png "Field")

After it was looking good, I had to add collision to the walls and floor. Again, I made another script to help with this. After the painful process of texturing and colliders, the environment was finally done.

## Building the first robot
After creating the field it was time to make the first robot. The initial robot was simple, it was the default godot car, which was good at the time but this simplicity would come at a heavy cost down the road. For now, though, everything worked great so I quickly moved on to making swerve drive. Swerve drive was a lot more complicated. My initial attempt at swerve drive was terrible as it did not follow any of the mathematics behind swerve. It barley worked so I moved on to more fun tasks.

## Early vision tests
When I first made this project I had intended it to be used for vision testing, however, this never got utilised and the code eventually got deleted. While it never got used, I implemented a first person camera on the robot that would create a webserver using raw sockets and share a mjpeg stream to any viewer. Here is it working:

![Vision](../assets/2024-frc-simulator/vision.png "Vision")

## Confusion
I was pretty burnt out over the project by now so I handed over the project to [Lincoln](../../authors/lincoln). He made some great improvements like adding a UI, but when implementing better swerve drive code, the whole codebase finally gave out and was imposible to debug. We left the project in this state for a few weeks while we worked on the actual code.

## Back with vengence
### Correct code
After I was refreshed I picked back up the project and reverted the code to where I left off before handing the project over. (Yes I know this seems mean but I was planning on re-adding the features once it got stable). I then looked up actual code for swerve drive and I found [this blog](https://dominik.win/blog/programming-swerve-drive/) by [Dominik Winecki](https://dominik.win/). This blog was very helpful and it included [the code used to generate the models](https://dominik.win/blog/res/swerve/SwerveDrive.html). I implemented all the code correctly and it was driving correctly, in certain conditions. 

### Quick Hacks
This is when I found out the first of many consequences of using the godot vehicle body. It turns out that there is no way to set the velocity of the wheel, only set the engine power. To counterract this, I implemented a PID controller in the game and tuned it to set the velocity of each wheel. 

```python
extends Node
class_name PIDController

var prev_error: float = 0.0
var prev_integral: float = 0.0
var prev_time: float = 0.0
@export var k_p: float = 0.01
@export var k_i: float = 0
@export var k_d: float = 0

func _init(pidValues: Vector3 = Vector3(0.01, 0, 0)):
	prev_time = Time.get_ticks_usec()
	setPID(pidValues)

func calculate(setpoint, pv):
	var time = Time.get_ticks_usec()
	
	var dt = time - prev_time
	var error = setpoint - pv
	var integral = prev_integral + (error * dt)
	var derivative = (error - prev_error) / dt
	
	var value = (k_p * error) + (k_i * integral) + (k_d * derivative)
	
	prev_error = error
	prev_integral = integral
	prev_time = time
	
	return value
	
func setPID(pidValues: Vector3):
	k_p = pidValues.x
	k_i = pidValues.y
	k_d = pidValues.z
```

This hack was working fine until you started rotating the wheels. This is when I figures out that the godot wheels have a bug where they calculate RPM around the starting axis, not the current one. This means that when you turn the wheel 90 degrees at 50 rpm it would say that it was spinning at 0 rpm. Obviously this was bad. I contacted the discord and one guy suggested to use another hack that uses trig to calculate the wheel speed. This works but it degrades in accuracy the close you get to 90 degrees.

```python
corrected_rpm = wheel.get_rpm() / cos(wheel.steering)
```

### Done
After fixing the buggy wheels, I could test the correctly implemented swerve code and it worked great. I added other modes like field relative and field relative rotation. This completed the basic, correct, swerve chasis.

![Swerve](../assets/2024-frc-simulator/swerve.png "Swerve")

## Busy season
After fixing everything I left it like this while I worked on robot code, occasionally bringing it out for drivers or kids to play on it and it worked fine for what it was. However, I was not done...

## Improvements
During competitions, programmers can get bored if there is nothing to code. Luckily I brought my gaming laptop with the source code of the simulator. During the competitions I would slowly improve the simulation like porting a low poly replica of our competition robot into the game.

![Robot](../assets/2024-frc-simulator/robot.png "Robot")

### More issues
I would of imported the full quality cad model but the origins were super messed up and cleaning them would of taken hours. (Trust me I tried). After importing the actual robot I realised that the wheels were not holding up the robot, instead they were touching the ground and the suspension built into the godot wheels were inversed while the chasis of the robot was sliding on the ground. This was not ideal so I set the stiffness and travel to max and 0 (respectivly). This just made the robot jump around to floating point imprecission when godot was calculating suspension. I asked the godot discord again, but this time no one had and hacks. I tried to make my own wheel physics but deemed it to hard and not a good use of my time so I reverted back to the original setup.

### Fun stuff
After having a decent enough robot I coded controls to pick up and shoot notes out of our launcher. I used a raycast to detect notes and if the intake was pressed it would reparent the note to the robot and freeze it in the chamber. When the shoot button was pressed it would move the note and add velocity to shoot it out of the cannon. I spread notes across the field and a button to spawn new ones. This is where I stopped and where it is today. The team drivers said I was 90% realistic if it counts for anything.

## Future
In the future I wish to rewrite the wheel physics and get rid of the ugly hacks. Maybe add back the ui and introduce multiplayer. If anyone wants to help here is the [github](https://github.com/4533-phoenix/Simulator). Make sure you are on branch ```drake```.