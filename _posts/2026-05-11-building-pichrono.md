---
layout: post
title: "I accidentally deleted my own commits, so I wrote a doomsday protocol"
date: 2026-05-11 14:30:00 +0530
categories: [C Programming, Version Control, Systems]
tags: [c, git, pichrono, systems-programming, vcs]
---

### Building the Core Engine

Writing a version control system from scratch in C forces you to confront the absolute lowest levels of data management. The initial goal for Pichrono was to replicate Git's core triad: blobs (file contents), trees (directory structures), and commits (snapshots in time). 

To do this, I had to write a custom SHA-1 hashing module and integrate `zlib` for compression. The pain points emerged almost immediately. C does not have built-in associative arrays or hash maps, meaning I had to build a custom Directed Acyclic Graph (DAG) parser just to traverse the Merkle tree of commits. Every time you run `pichrono commit`, the C engine has to hash the payload, deflate it into binary, write it to `.pichrono/objects`, and then update a pointer in `.pichrono/refs`. Managing memory allocations (`malloc` / `free`) for these nested tree structures was a brutal exercise in avoiding segmentation faults.

### The Incident

There is a distinct kind of panic that sets in when you type `git reset --hard` and immediately realize you just nuked two days of unpushed work. 

Now, imagine experiencing that same panic, but you can't blame Git because *you wrote the version control system yourself*. 

For the past few weeks, I've been deep in the trenches building [Pichrono](https://github.com/piyushS3V3N/pichrono)—a VCS written purely in C. I wanted to understand Merkle trees and content-addressable storage from first principles. Everything went great until I introduced branching. Due to a botched pointer arithmetic operation in my branch-switching logic, I effectively orphaned my entire commit history. 

My files were still technically on disk, compressed via ZLIB and hashed with SHA-1, but the Directed Acyclic Graph (DAG) had completely forgotten how to find them.

### The Doomsday Protocol

So, I stopped working on standard VCS features and spent three days writing a "Doomsday Recovery" module. 

```c
// Actual excerpt from my descent into madness
void pichrono_doomsday_scan(const char *objects_dir) {
    printf("[!] INITIATING DOOMSDAY PROTOCOL\n");
    // Brute force read every single zlib-compressed object 
    // on disk to reconstruct the shattered DAG...
}
```

It acts as an aggressive object scanner. When triggered, it ignores the broken refs, dynamically allocates a buffer, and uses `inflateInit` from `zlib.h` to decompress every single object file in the `.pichrono/objects` directory. Since Git objects (and Pichrono objects) are formatted as `<type> <size>\0<content>`, I had to write a custom C parser that hunts for that null byte. It reconstructs the parent-child commit hashes in memory and builds a reverse-lookup table to figure out which nodes are completely disconnected from any known branch.

### Visualizing the Rescue

To make sure it was actually working and I wasn't just hallucinating data, I hooked up an embedded Mongoose web server to serve the commit graph as an SVG to `localhost:8080`. 

Here is what the recovery actually looked like in the visualizer once it successfully re-attached the lost commits:

![Pichrono Commit DAG Visualizer](/assets/pichrono-dag.svg)

Notice the dashed red line and the `doomsday-recovery` branch? That is Pichrono automatically rescuing `m3n4` from the abyss.

### The Takeaway

Writing your own tools from scratch is an incredible way to learn. It is also an incredible way to shoot yourself in the foot. But hey, at least I know exactly how the bandage works now.
