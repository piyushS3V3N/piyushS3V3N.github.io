---
layout: post
title: "I accidentally deleted my own commits, so I wrote a doomsday protocol"
date: 2026-05-11 14:30:00 +0530
categories: [C Programming, Version Control, Systems]
tags: [c, git, pichrono, systems-programming, vcs]
---

### Building the Core Engine

Writing a version control system from scratch in C is a great way to confront the reality of low-level data management. The initial goal for Pichrono was to replicate Git's core triad: blobs (file contents), trees (directory structures), and commits (snapshots in time). 

To do this, I implemented a custom SHA-1 hashing module and integrated `zlib` for compression. The challenges emerged almost immediately. Since C does not have built-in associative arrays or hash maps, I had to build a custom Directed Acyclic Graph (DAG) parser to traverse the Merkle tree of commits. Every time `pichrono commit` is executed, the engine hashes the payload, deflates it into binary, writes it to `.pichrono/objects`, and updates a pointer in `.pichrono/refs`. Managing memory allocations (`malloc` / `free`) for these nested tree structures required careful boundary checks to avoid segmentation faults.

### The Incident

There is a distinct kind of panic that sets in when you type `git reset --hard` and realize you just lost two days of unpushed work. It's worse when you can't blame Git because *you wrote the version control system yourself*. 

For the past few weeks, I've been building [Pichrono](https://github.com/piyushS3V3N/pichrono)—a VCS written purely in C—to understand content-addressable storage from first principles. Everything went smoothly until I introduced branching. Due to a pointer arithmetic bug in my branch-switching logic, I effectively orphaned my entire commit history. 

My files were still technically on disk—compressed via ZLIB and hashed with SHA-1—but the DAG had completely lost the references needed to find them.

### Building a Recovery Tool

Instead of continuing with standard VCS features, I spent a few days writing a recovery module to salvage the orphaned objects.

```c
void pichrono_doomsday_scan(const char *objects_dir) {
    printf("[!] INITIATING RECOVERY PROTOCOL\n");
    // Sequentially read and decompress every zlib object 
    // on disk to reconstruct the orphaned DAG...
}
```

The recovery tool acts as a block-level object scanner. When triggered, it ignores the broken references in `.pichrono/refs`, dynamically allocates a buffer, and uses `inflateInit` from `zlib.h` to decompress every object file in the `.pichrono/objects` directory. Since the objects are formatted as `<type> <size>\0<content>`, I wrote a simple parser that scans for the null byte. It extracts the parent-child commit hashes and builds a reverse-lookup table in memory to figure out which nodes are disconnected from the active branch.

### Visualizing the Rescue

To verify that the recovery was working correctly, I hooked up an embedded Mongoose web server to serve the commit graph as an SVG to `localhost:8080`. 

Here is what the recovery looked like in the visualizer once it successfully re-attached the lost commits:

![Pichrono Commit DAG Visualizer](/assets/pichrono-dag.svg)

The dashed red line and the `doomsday-recovery` branch show Pichrono automatically rescuing commit `m3n4` and linking it back into the graph.

### The Takeaway

Writing your own tools from scratch is an incredible way to learn how systems work under the hood, even if it occasionally results in shooting yourself in the foot. Building this recovery protocol was a great exercise in understanding how cryptographic verification decouples data durability from graph corruption.
