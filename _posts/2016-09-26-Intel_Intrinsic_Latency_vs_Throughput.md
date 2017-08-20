---
layout:            post
title:             "Intel Intrinsic Terminology: Latency vs. Throughput"
date:              2016-09-26
tags:              Performance SIMD Intrinsic
category:          Programming for Performance
author:            hongyuan

---

In [Intel Intrinsics Guide](https://software.intel.com/sites/landingpage/IntrinsicsGuide/#), `latency` and `throughput` are given as mesurements of performance of instructions. According to Intel's [Measuring Instruction Latency and Throughput](https://software.intel.com/en-us/articles/measuring-instruction-latency-and-throughput):

 - `Latency` is the number of processor clocks it takes for an instruction to have its data available for use by another instruction. Therefore, an instruction which has a latency of 6 clocks will have its data available for another instruction that many clocks after it starts its execution.

 - `Throughput` is the number of processor clocks it takes for an instruction to execute or perform its calculations. An instruction with a throughput of 2 clocks would tie up its execution unit for that many cycles which prevents an instruction needing that execution unit from being executed. Only after the instruction is done with the execution unit can the next instruction enter.

So both of them have the unit of cycles/instruction (some people use instructions/cycle when describing `throughput`, which is more similar to what `throughput` means in real life, i.e. some quantity per amount of time).

Usually an instruction's `latency` is larger than its `throughput` so you can use latency as the **wrost case measurement**. However, also according to Intel's [Measuring Instruction Latency and Throughput](https://software.intel.com/en-us/articles/measuring-instruction-latency-and-throughput):

 - If a processor has multiple execution units for a certain type of operation then the `throughput` of an instruction using that execution unit will effectively be divided by the number of those execution units because multiple instructions using those units can be executing at once.

That means, multiple instructions may be issued at the same time so if you write your program properly you can get the maximum throughput out of Intel's CPU. For instance, on Haswell, the `latency` of **_mm256_blend_ps()** is 1, so it takes 1 cycle for a sigle instruction to complete. However, the `throughput` of this instruction is only 0.33, which means there're 3 execution units that can perform this instruction in parallel. Therefore, if you can write you code in a way that every three blend instrutions are issued together it would effectively take only 1 cycle for these three instructions to complete.