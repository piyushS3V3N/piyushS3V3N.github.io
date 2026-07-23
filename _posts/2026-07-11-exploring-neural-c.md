---
layout: post
title: "Building a Neural Net from Scratch in C (and why it's painful but fun)"
date: 2026-07-11 14:00:00 +0530
categories: [Machine Learning, C Programming, Metal API]
tags: [neural-networks, c, metal, macos, raylib]
---

I've been hacking on a side project recently called **Neural-C**. My goal was to see if I could build a deep neural network entirely from scratch in C, bypassing massive libraries like TensorFlow or PyTorch. It's an excellent way to force yourself to understand the math behind matrix multiplication and backpropagation instead of just calling `model.fit()`.

To make it performant enough to be usable, I integrated Apple's Metal API for GPU acceleration and built a real-time visualizer using Raylib to monitor the training loop.

Here's a look at the live visualizer in action:

![Neural-C Live Visualizer](/assets/neural-c-ss1.png)

### The Upsides

The most rewarding part of this project is the speed. By writing compute shaders in Metal and dispatching them from Objective-C, the heavy matrix math is offloaded directly to the Mac's GPU. 

The Raylib visualizer is also a great addition. Watching the weights update and the loss graph drop in real-time provides immediate feedback. I also added a feature where you can press '1' or '0' while it's running to correct predictions on the fly and force the model to re-evaluate. 

### Bridging C and Metal

The biggest hurdle wasn't the neural network math itself—it was getting the CPU and GPU to communicate efficiently without stalling the pipeline. Writing the matrix multiplication kernel in `.metal` was straightforward:

```metal
kernel void matrix_mul(
    device const float* A [[buffer(0)]],
    device const float* B [[buffer(1)]],
    device float* C [[buffer(2)]],
    uint2 gid [[thread_position_in_grid]]
) {
    // Standard dot product implementation...
}
```

The real challenge was the Objective-C++ bridging layer. Because my core engine is pure C, I had to write a wrapper that took raw `float*` arrays, allocated `MTLBuffer` objects (using `MTLResourceStorageModeShared`), copied the memory over, and then calculated the optimal `MTLSize` threadgroups based on the layer dimensions. Early on, miscalculating the grid size meant the GPU would either throw a silent fault or hang the system temporarily.

Once I got the synchronization right—using dispatch semaphores to ensure the CPU didn't read the loss gradient before the GPU finished computing it—the performance jump was massive. A forward pass that took 12ms on the CPU dropped to around 0.4ms.

### Current Limitations and Next Steps

Building a custom engine from scratch means dealing with a lot of rough edges. Here are the main areas I'm looking to improve:

**1. Platform Lock-in**
Because I relied heavily on Apple's Metal API to achieve high performance, the project is completely locked to macOS. Eventually, I'd like to abstract the hardware acceleration layer to support Vulkan or OpenCL for cross-platform compatibility.

**2. Memory Management Constraints**
Writing everything in raw C requires dealing with `malloc` and `free` everywhere. While manageable for the current setup, scaling this up to handle larger datasets or deeper networks will require a more robust approach, such as implementing a custom memory arena allocator to prevent leaks and fragmentation.

**3. Architectural Rigidity**
Right now, the network architecture is fairly hardcoded. Adding new layer types (like CNNs or LSTMs) requires writing a lot of boilerplate C code. Designing a more modular, composable API for stacking different layers is high on the priority list.

### Wrapping Up

Despite the challenges, Neural-C has been a fantastic exercise in pushing C and macOS graphics APIs. It even includes persistent memory (saving weights to a simple `brain.bin` file), so the model retains what it learned between runs.

There's still plenty of work left to do, but it's a solid start for a custom inference engine.
