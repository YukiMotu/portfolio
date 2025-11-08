---
title: "How to Optimize CUDA Matrix Multiplication"
description: ""
pubDate: "OCt 14 2025"
heroImage: "/cuda1.png"
tags: ["CUDA", "GPU"]
---


**Source**: [How to Optimize a CUDA Matmul Kernel for cuBLAS-like Performance: a Worklog](https://siboehm.com/articles/22/CUDA-MMM)

## Overview

This article walks through optimizing matrix multiplication (matmul) in CUDA from scratch, starting with a basic implementation and progressively improving it until reaching 95% of cuBLAS performance.

## Key Concepts

**What is SGEMM?** Single-precision (32-bit float) General Matrix Multiply - performs C = αAB + βC

**Why it matters:** Matrix multiplication is the most important algorithm in modern deep learning, making up almost all computations during training and inference.

**Test setup:** Multiplying 4092×4092 matrices on an A6000 GPU (30 TFLOPs theoretical peak)

## The Optimization Journey

### Kernel 1: Naive Implementation
- **Performance:** 309 GFLOPs (1.3% of cuBLAS)
- **Approach:** Each thread calculates one output element
- **Problem:** Terrible memory access patterns - threads load data inefficiently

### Kernel 2: Global Memory Coalescing  
- **Performance:** 1,987 GFLOPs (8.5% of cuBLAS)
- **Improvement:** Reorganized how threads access memory so consecutive threads read consecutive memory locations
- **Key insight:** GPUs can combine multiple small memory reads into one large read when addresses are consecutive

### Kernel 3: Shared Memory Caching
- **Performance:** 2,980 GFLOPs (12.8% of cuBLAS)
- **Approach:** Load chunks of data into fast on-chip "shared memory" that all threads can access
- **Key insight:** Shared memory is 16x faster than global memory

### Kernel 4: 1D Block Tiling
- **Performance:** 8,475 GFLOPs (36.5% of cuBLAS)
- **Approach:** Each thread now calculates 8 output elements instead of 1
- **Key insight:** Reduces memory accesses per result because data can be reused

### Kernel 5: 2D Block Tiling
- **Performance:** 15,972 GFLOPs (68.7% of cuBLAS)
- **Approach:** Each thread calculates an 8×8 grid of outputs (64 elements)
- **Key insight:** Computing a square of results allows even more data reuse than computing a column

### Kernel 6: Vectorized Memory Access
- **Performance:** 18,237 GFLOPs (78.4% of cuBLAS)
- **Approach:** Load 4 floats at once instead of 1 at a time
- **Key insight:** Memory systems work most efficiently when loading larger chunks

### Kernel 9: Autotuning
- **Performance:** 19,721 GFLOPs (84.8% of cuBLAS)
- **Approach:** Automatically test different block sizes to find the best configuration

### Kernel 10: Warp Tiling
- **Performance:** 21,779 GFLOPs (93.7% of cuBLAS)
- **Final optimization:** Organize work at the "warp" level (groups of 32 threads)

## Main Lessons

1. **Memory is the bottleneck**: Early kernels were limited by how fast they could fetch data, not by computation speed

2. **Data reuse is critical**: Computing more results per thread means you can reuse loaded data more times

3. **Hardware details matter**: Understanding warps (groups of 32 threads), shared memory, and memory coalescing is essential

4. **Progressive optimization works**: Each optimization built on the previous one, with clear performance improvements at each step

## The Final Result

Starting from 309 GFLOPs with a naive implementation, the optimized kernel reached 21,779 GFLOPs - a **70x speedup** and 93.7% of professional library performance!

---

*Article by Simon Boehm, December 2022*  
*Full code available on [GitHub](https://github.com/siboehm/SGEMM_CUDA)*