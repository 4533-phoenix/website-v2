+++
title = 'Coral and libedgetpu'
description = 'FRC Vision Research Part 1'
authors = ['lincoln']
tags = ['code', 'vision', 'tpu', 'edgetpu', 'frc']
date = '2024-04-22T21:40:15Z'
series = ['Vision']
series_order = 1
draft = false
+++

So I've been doing a lot of research and work on vision stuff.
This is going to be a multi-part series, because there's a LOT to cover.

## Edge TPU

So what is an "edge TPU"?

A made-up thing called a "tensor processing unit".
It's really just an ASIC[^1] designed by Google for fast, efficient machine learning in embedded environments.
It's great for FRC, because it's a relatively low-cost, easy way to do tons of cool ML stuff off of a Pi.

The trouble is... Google has a [history](https://en.wikipedia.org/wiki/Google%2B#Shutdown_of_consumer_version) [with](https://github.com/tensorflow/swift) [side](https://www.theverge.com/2021/1/21/22243484/alphabet-google-shutting-down-loon-internet-balloon-company-x) [projects](https://web.archive.org/web/20201202153708/https://support.google.com/poly/answer/10192635).
Nearly 300 of them have been ["killed by Google"](https://killedbygoogle.com/).

Official edge TPU libraries have seen fewer and fewer activity from developers at Google.
[Their latest blog post](https://developers.googleblog.com/2022/05/coral-googles-platform-for-edge-ai.html) is nearly 2 years old.

Some PyCoral users have expressed disappointment and speculated in [this Github issue](https://github.com/google-coral/pycoral/issues/137):

 > *Has this project been officially abandoned?*
 >
 > -- tranzmatt @ Github

 > *There hasn't been any comment, action, or even statement in a very long time. The repos are frozen in time. Such a pity when every chip manufacturer is presenting NPUs as the next big thing, and Google (who pioneered it with the edgeTPU so long ago) decided to drop the ball. To make it worse, no official statement either, and a community that relies/invested on it is left it to its own devices (pun very much intended). And to make it worse, coral.ai still sells these products as fully supported when they are nothing but.*
 >
 > *Sad, and infuriating.*
 >
 > -- feranick @ Github

And so many more.

But there's some hope!
Cocoa, an independent developer writing *Elixir* bindings to TFLite, made a fork of libedgetpu.
Their fork is well-maintained and offers shared libraries via Github releases, making my life much easier.

## TFLite

Coral devices only work with TensorFlow Lite, Google's machine learning library for embedded devices.
It's supposed to be minimal and stuff, but it's still written in C++.
Maybe they meant minimal sanity? Anyway...

They support Bazel and CMake as build systems.
Bazel seems to be the preferred method, but I think CMake is used more by TFLite users.
I'm more familiar with CMake and Bazel's kinda weird about outputting usable shared libraries, so I went with CMake.

After admittedly way too much time, I figured out how to make a functional build of TFLite's C API:

```shell
git clone https://github.com/tensorflow/tensorflow
cd tensorflow/
cmake ../tensorflow/lite/c/
make
```

I then linked it into my custom code and it worked!

## Putting it together

I like Rust.
It's a joy to write. It's a joy to run.
It has a very helpful community, and most popular libraries have excellent documentation.

So I tried it for my vision experiments.
It performed great.

I wrote thin bindings around TFLite and libedgetpu, wrapped up into one.
It needs a little work before I'd really trust it, but it outperforms all our previous tests.

Drake found [this model](https://drive.google.com/file/d/1SPsWfmfVcdSvBOJgvrviB1rTjDHqKx_o/view?usp=sharing), which I plugged into `edgetpu_compiler` to confirm it can run all operations on the TPU.
Yep.

I did a few not-so-scientific benchmarks:
 - With [my custom Python code](https://github.com/4533-phoenix/crescendo-vision/blob/07f16aaed6947af4b7decbf7587e32f45bf62145/crescendo_vision/notedetector.py), it crashed.
 - With Limelight, it pulled 30 fps.
 - With [my Rust code](https://github.com/frc4533-lincoln/chalkydri/blob/main/chalkydri-tfledge/src/main.rs), it pulled just over 60 fps.

I don't know why my Python code segfaulted, but I don't really care enough to look into it.
It's not going to be super performant anyway: multithreading/multiprocessing in Python is a pain.
I'll leave Python wizardry to Drake.

Anyway this Rust thing might be worth it.

I tested with one camera.
Assuming it scales linearly, two edge TPUs could handle four cameras at 30 fps, with a few extra frames of tolerance.
With a camera on each side of the bot, running at 30 fps, we can do a lot of cool stuff.

## Going forward

In the next part, I'll be discussing AprilTags: how others have detected them, how I've tried to detect them, and what I've found to work well and not-so-well.

There's still a lot to cover.



[^1]: Application-specific integrated circuit
