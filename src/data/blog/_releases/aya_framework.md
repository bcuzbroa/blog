---
title: Example Draft Post
author: bcuzbroa
pubDatetime: 2026-12-31
slug: example-draft-post
featured: false
draft: true
tags:
  - TypeScript
  - Astro
description:
  Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor
  incididunt ut labore et dolore magna aliqua. Praesent elementum facilisis leo vel
  fringilla est
---

Users cannot see this post because it is in draft.

## Notes


To do list:

Aya book chapter
1 : DONE
2 : 2.2 DONE
    2.3 TODO
      Discovering task_struct
    2.4 TODO
3. TODO 

ust eBPF program to be "CO-RE" (Compile Once – Run Everywhere)

Phase 1: The Mental Model (The "Why")

Before writing code, you must understand the constraints of the eBPF Virtual Machine. It is not a general-purpose CPU.

The Constraint: No heap, no standard library, and a very small stack (512 bytes).
The Verifier: The kernel checks your code before it runs. If it can't prove your code won't crash the kernel, it will reject it.
The Resource: Read the first three chapters of the Aya Book. It explains the "Userspace vs. Kernel-space" split perfectly.

2.2
[2026-02-17T13:44:09Z INFO  xdp_log] SRC IP: 76.223.92.165, SRC PORT: 443
[2026-02-17T13:44:09Z INFO  xdp_log] SRC IP: 18.239.208.128, SRC PORT: 443
[2026-02-17T13:44:10Z INFO  xdp_log] SRC IP: 18.239.208.128, SRC PORT: 443
[2026-02-17T13:44:11Z INFO  xdp_log] SRC IP: 18.239.208.128, SRC PORT: 443
[2026-02-17T13:44:11Z INFO  xdp_log] SRC IP: 35.186.224.24, SRC PORT: 443
[2026-02-17T13:44:11Z INFO  xdp_log] SRC IP: 35.186.224.24, SRC PORT: 443
[2026-02-17T13:44:11Z INFO  xdp_log] SRC IP: 35.186.224.24, SRC PORT: 443
[2026-02-17T13:44:11Z INFO  xdp_log] SRC IP: 35.186.224.24, SRC PORT: 443
[2026-02-17T13:44:11Z INFO  xdp_log] SRC IP: 35.186.224.24, SRC PORT: 443
^X[2026-02-17T13:44:12Z INFO  xdp_log] SRC IP: 35.186.224.33, SRC PORT: 443
[2026-02-17T13:44:12Z INFO  xdp_log] SRC IP: 18.239.208.128, SRC PORT: 443
[2026-02-17T13:44:12Z INFO  xdp_log] SRC IP: 35.186.224.33, SRC PORT: 443
[2026-02-17T13:44:12Z INFO  xdp_log] SRC IP: 35.186.224.33, SRC PORT: 443
[2026-02-17T13:44:13Z INFO  xdp_log] SRC IP: 18.239.208.128, SRC PORT: 443
[2026-02-17T13:44:14Z INFO  xdp_log] SRC IP: 18.239.208.128, SRC PORT: 443


Discovering 