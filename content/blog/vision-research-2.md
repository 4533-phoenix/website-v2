+++
title = 'More Coral'
description = 'FRC Vision Research Part 1b'
authors = ['lincoln']
tags = ['code', 'vision', 'tpu', 'edgetpu', 'frc']
date = '2024-09-04T16:40:00Z'
+++

It's been a while.
I know this isn't AprilTag stuff, but the next blog post (coming soon) will be.
This is kind of a personal project, but it's related to vision.

So I did more Coral stuff.

I tried to reverse engineer and rewrite libedgetpu in Rust and actually learned quite a bit of possibly useful information,
even if it feels like a waste of time in hindsight.

The README explains why I'm doing this (pardon the tone):
 > This is meant to be an alternative to `libedgetpu` for people that hate dealing with Google's bloated garbage software.
 > Edged has no dependency on TensorFlow, Flatbuffers, or Bazel whatsoever.
 >
 > Our codebase is also a fraction of the size and doesn't have so much useless C++ complexity it's unmaintainable and needs to be reverse engineered.

## Prelude

First, some acronyms and terms you may need:
 - **CSR**: command status register
 - **DMA**: direct memory access

## Why "reverse engineering"? Isn't it open source?

While libedgetpu *is* open source, there's no public documentation on how it really works under the covers.
I have some more thoughts on libedgetpu's openness, but I'll get to those in later sections.
For now, assume it's a open driver w/ closed blob type of deal.

## Interfacing with the TPU

There's two different ways of interacting with the chips:
 - [gasket](https://github.com/google/gasket-driver), the kernel driver (for PCIe and M.2 devices)
 - The libusb-based userspace driver (for the USB Accelerator)

I think their official stance on the standalone chip is something like this: “Here's a schematic. Have fun!”

In both cases, we have to use some sort of DMA abstraction.

There's some sort of instruction set.

The memory layout is done by the absolutely proprietary `edgetpu_compiler`, so I have to reverse engineer it.

## Reverse engineering & rewriting `libedgetpu`

This was by far the most time consuming and dull part.
I don't really have a background in C++, which surely would've helped, but the code seemed very convoluted.

I found that the code seemed to all be structured around DMA.
That makes sense for the kernel driver (it uses memory-mapped I/O), but not for the libusb-based driver.
I'm guessing probably just for a simpler implementation on the USB Accelerator.
I'm not sure exactly how it's hooked up, but the USB firmware is pretty small.

The kernel driver is much easier to support, since it doesn't do anything weird with DMA over USB.
I just had to write a little code to interact with the ioctls.

Either way it's just reading and writing to addresses.

I haven't finished this, but got a good amount done.
It's kind of hard to continue without understanding more about how the chip itself works.

## Reverse engineering `edgetpu_compiler`

`edgetpu_compiler` is actually where most of the interesting stuff happens and it's proprietary.

TensorFlow's tests include a set of Python scripts for generating valid models for each builtin operation.
This happens to be just what I needed.

According to Google, the following operations are (at least somewhat) supported[^1]:
 - Add
 - AveragePool2d
 - Concatenation
 - Conv2d
 - DepthwiseConv2d
 - ExpandDims
 - FullyConnected
 - L2Normalization
 - Logistic
 - LSTM
 - Maximum
 - MaxPool2d
 - Mean
 - Minimum
 - Mul
 - Pack
 - Pad
 - PReLU
 - Quantize
 - ReduceMax
 - ReduceMin
 - ReLU
 - ReLU6
 - ReLUN1To1
 - Reshape
 - ResizeBilinear
 - ResizeNearestNeighbor
 - Rsqrt
 - Slice
 - Softmax
 - SpaceToDepth
 - Split
 - Squeeze
 - StridedSlice
 - Sub
 - Sum
 - Squared-difference
 - Tanh
 - Transpose
 - TransposeConv

(Putting the entire list here seemed like a better idea before I typed it.)

So, I set up Bazel in a Github Codespace and let it run roughly five hours.
At which point it was unhappy with Ubuntu 22.04's clang and I decided to try again, twice.

After wasting a day and my little remaining sanity, I gave up and wrote some tooling myself in Python.
I did try Rust, but the maintainer of the broken Rust implementation of FlatBuffers isn't supporting it.

### First observations of the edgetpu

The instruction bitstreams are very interesting to me, so I took a look at them first.
These are just some random models I either had or downloaded from somewhere.

 > A generic MobileNet v2 model (min runtime 10):
 >
 > ```text
 > 00000000: 800f 0014 8400 0000 0000 0000 0000 0000  ................
 > 00000010: 0009 0000 9f01 0000 0000 0000 0000 0000  ................
 > 00000020: 0000 0000 0000 0000 0000 0000 0000 4003  ..............@.
 > 00000030: 0009 0000 0802 0000 0000 0000 0000 0000  ................
 > 00000040: 0000 0000 0000 0000 0000 0000 0000 4003  ..............@.
 > 00000050: 80f6 ff0f 00f0 ff1f 0080 ff01 0000 0000  ................
 > 00000060: 80f6 ff1f 0020 0080 0080 ff01 0000 0000  ..... ..........
 > 00000070: 0008 0000 0000 c01b 0000 0000 0000 0000  ................
 > 00000080: 0008 0000 0000 c00b 0000 0000 0000 0000  ................
 > 00000090: 0008 0000 0000 c03b 0000 0000 0000 0000  .......;........
 > 000000a0: 0008 0000 0000 c02b 0000 0000 0000 0000  .......+........
 > 000000b0: 80f6 ff2f 0000 0000 0080 ff01 0008 0000  .../............
 > 000000c0: 00f9 ff0f 0000 0002 00fe ff01 0000 0000  ................
 > 000000d0: 0000 0000 0000 0000 0000 0000 0000 0003  ................
 > 000000e0: c0f5 ff0f 00c0 0000 0000 0000 0000 0000  ................
 > 000000f0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
 > ```

 > A specialized MobileNet v2 model (min runtime 14):
 >
 > ```text
 > 00000000: 800f 00cc f100 0000 0000 0000 0000 0000  ................
 > 00000010: 80f6 ff0f 00f0 ff7f 0080 ff01 0008 0000  ................
 > 00000020: 00f9 ff0f ff01 0002 00fe ff01 0000 0000  ................
 > 00000030: 0000 0000 0000 0000 0000 0000 0000 c007  ................
 > 00000040: 0008 0000 0000 c01b 0000 0000 0000 0000  ................
 > 00000050: 0008 0000 0000 c00b 0000 0000 0000 0000  ................
 > 00000060: 0008 0000 0000 c03b 0000 0000 0000 0000  .......;........
 > 00000070: 0008 0000 0000 c02b 0000 0000 0000 0000  .......+........
 > 00000080: 0009 0000 0500 0000 0000 0000 0000 0000  ................
 > 00000090: 0000 0000 0000 0000 0000 0000 0000 0003  ................
 > 000000a0: 80f6 ff1f 0000 0000 0080 ff01 0008 0000  ................
 > 000000b0: 00f9 ff0f 0000 0002 00fe ff01 0000 0000  ................
 > 000000c0: 0000 0000 0000 0000 0000 0000 0000 0003  ................
 > 000000d0: c0f5 ff0f 0080 0000 0000 0000 0000 0000  ................
 > 000000e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
 > 000000f0: 0010 0e08 0000 0000 0000 0000 0000 0000  ................
 > ```

 > A generic Yamnet model (min runtime 12):
 >
 > ```text
 > 00000000: 800f 0050 3200 0000 0000 0000 0000 0000  ...P2...........
 > 00000010: 80f6 ff0f 00f0 ff7f 0080 ff01 0008 0000  ................
 > 00000020: 00f9 ff0f ff01 0002 00fe ff01 0000 0000  ................
 > 00000030: 0000 0000 0000 0000 0000 0000 0000 c007  ................
 > 00000040: 0008 0000 0000 c01b 0000 0000 0000 0000  ................
 > 00000050: 0008 0000 0000 c00b 0000 0000 0000 0000  ................
 > 00000060: 0008 0000 0000 c03b 0000 0000 0000 0000  .......;........
 > 00000070: 0008 0000 0000 c02b 0000 0000 0000 0000  .......+........
 > 00000080: 0009 0000 0500 0000 0000 0000 0000 0000  ................
 > 00000090: 0000 0000 0000 0000 0000 0000 0000 0003  ................
 > 000000a0: 80f6 ff1f 0000 0000 0080 ff01 0008 0000  ................
 > 000000b0: 00f9 ff0f 0000 0002 00fe ff01 0000 0000  ................
 > 000000c0: 0000 0000 0000 0000 0000 0000 0000 0003  ................
 > 000000d0: c0f5 ff0f 0080 0000 0000 0000 0000 0000  ................
 > 000000e0: 0000 0000 0000 0000 0000 0000 0000 0000  ................
 > 000000f0: 0000 0408 0000 0000 0000 0000 0000 0000  ................
 > ```

You may have noticed some patterns here.
All of them start with `0x800F`, followed by 4 bytes of data (or maybe 2 bytes of data w/ padding), followed by 10 null bytes.
`0x06` - `0xEF` are identical for the Yamnet model and specialized MobileNet v2 model, so I think `0x00` - `0xEF` are probably initialization stuff for those two.

Next, I spent a week trying to generate TFLite "models" to trick `edgetpu_compiler` into translating extremely simple single operations for me to compare and try to figure out.
It seems `edgetpu_compiler` didn't really like that.

```
Edge TPU Compiler version 16.0.384591198
Started a compilation timeout timer of 5 seconds.
Compilation child process completed within timeout period.
Compilation failed!
```

## Other miscellaneous findings that may be useful for continuing this project

 - 3+ different code names: darwinn, beagle, apex
 - `driver::Registers`:
   - provides an interface for polling a given register by its offset
   - spins until getting expected value
 - `driver::SocketRegisters`:
   - provides an interface for sending requests and receiving results over OS sockets
   - IP and port number, so meant to be used over a network?
 - `driver::Bitfield`:
   - "Helper class to get/set command status register (CSR) fields"
   - does the stupid C++ operator definition thing
 - `driver::DeviceBufferMapper`:
   - "Maps request-specific Buffers to DeviceBuffers, and keeps track of DeviceBuffers."
 - `driver::DeviceBuffer`:
   - "Abstracts a device addressable buffer."
 - PCIe requires a kernel driver.
 - The kernel driver doesn't have USB support.
 - Opcodes are 16-bit iirc (maybe 32?)

## Wrapping up

You can find some more details and source code [here](https://github.com/dragynfruit/edged).

This used up roughly a month of my already short summer break.
I'm pretty burnt out on this.
I won't be continuing this project unless there's enough interest in helping out, particularly from someone with reverse engineering expertise.
If that sounds like you, please get in touch!

[^1]: <https://coral.ai/docs/edgetpu/models-intro/#supported-operations>
