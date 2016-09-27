---
layout:            post
title:             "Swizzling with Intel AVX/AVX2"
date:              2016-09-27
tags:              AVX HPC Intrinsic
category:          High Performance Computing
author:            hongyuan

---

In this post we focus on `__m256`, which contains 8 single precision floats.

### 1.Blend
Blend two vectors to form a new one. `_mm256_blendv_ps()` has the same functionality but is  slower.
<a name="_mm256_blend_ps"></a>

```cpp
output = _mm256_blend_ps(a, b, 0b11100100);
```

![blend_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/blend_ps.png)

----

### 2.Broadcast
Broadcast either **128 bits** or **32 bits** from memory to the entire 256 bits container. `_mm256_broadcastss_ps()` is also used to broadcast **32 bits** but is slower.
<a name="_mm256_broadcast_ps"></a>

```cpp
output = _mm256_broadcast_ps((__m128*)&a);
```

![broadcast_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/broadcast_ps.png)

----
<a name="_mm256_broadcast_ss"></a>

```cpp
output = _mm256_broadcast_ss((float*)&a[1]);
```

![broadcast_ss.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/broadcast_ss.png)

To broadcast **64 bits**, use `_mm256_broadcast_sd()`.

----

### 3.Extract & Insert
Extract/insert **128 bits** from/into the vector.
<a name="_mm256_extractf128_ps"></a>

```cpp
output = _mm256_extractf128_ps(a, 1);
```

![extractf128_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/extractf128_ps.png)

----
<a name="_mm256_insertf128_ps"></a>

```cpp
output = _mm256_insertf128_ps(a, b, 1);
```

![insertf128_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/insertf128_ps.png)

----

### 4.Permute
Shuffle data inside the vector. `_mm256_permute_ps()` is faster but can only shuflle data inside each 128-bit lane while on the other hand, `_mm256_permutevar8x32_ps()` is slower but can shuffle data in a very flexible manner.
<a name="_mm256_permute_ps"></a>

```cpp
output = _mm256_permute_ps(a, 0b01110100);
```

![permute_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/permute_ps.png)

----
<a name="_mm256_permutevar8x32_ps"></a>

```cpp
output = _mm256_permutevar8x32_ps(a, idx);
```

![permutevar8x32_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/permutevar8x32_ps.png)

----

### 5.Permute2 & Shuffle
Shuffle data between two vectors. `_mm256_permute2f128_ps` can also be used to switch the high/low 128 bits if **a** and **b** are the same vector.
<a name="_mm256_permute2f128_ps"></a>

```cpp
output = _mm256_permute2f128_ps(a, b, 0b00100001);
```

![permute2f128_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/permute2f128_ps.png)

----
<a name="_mm256_shuffle_ps"></a>

```cpp
output = _mm256_shuffle_ps(a, b, 0b01110100);
```

![shuffle_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/shuffle_ps.png)

----

### 6.Unpack
Unpack and interleave elements from the **high/low half of each 128-bit lane** from two vectors.
<a name="_mm256_unpackhi_ps"></a>

```cpp
output = _mm256_unpackhi_ps(a, b);
```

![unpackhi_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/unpackhi_ps.png)

----
<a name="_mm256_unpacklo_ps"></a>

```cpp
output = _mm256_unpacklo_ps(a, b);
```

![unpacklo_ps.png]({{ site.github.url }}/res/2016-09-27-Swizzling_with_Intel_AVX_AVX2/unpacklo_ps.png)

----

### Appendix. Latency & Throughput on Haswell
See [this post]({{ site.github.url }}/blog/high%20performance%20computing/Intel_Intrinsic_Latency_vs_Throughput.html) to understand the terms **latency** & **throughput**. 

It should be pointed out that manipulating data across the high/low 128 bits causes higher latency and should be avoided if possible.

| Architecture | Latency | Throughput |
|--------------|---------|------------|
| [_mm256_blend_ps()](#_mm256_blend_ps)| 1 | 0.33 |
| [_mm256_broadcast_ps()](#_mm256_broadcast_ps) | 1 | - |
| [_mm256_broadcast_ss()](#_mm256_broadcast_ss) | - | - |
| [_mm256_extractf128_ps()](#_mm256_extractf128_ps) | 1 | 1 |
| [_mm256_insertf128_ps()](#_mm256_insertf128_ps) | <span style="color:red">3</span> | - |
| [_mm256_permute_ps()](#_mm256_permute_ps) | 1 | - |
| [_mm256_permutevar8x32_ps()](#_mm256_permutevar8x32_ps) | <span style="color:red">3</span> | 1 |
| [_mm256_permute2f128_ps()](#_mm256_permute2f128_ps) | <span style="color:red">3</span> | 1 |
| [_mm256_shuffle_ps()](#_mm256_shuffle_ps) | 1 | 1 |
| [_mm256_unpackhi_ps()](#_mm256_unpackhi_ps) | 1 | 1 |
| [_mm256_unpacklo_ps()](#_mm256_unpacklo_ps) | 1 | 1 |

### References
\[1] Intel Intrinsic Guide ([https://software.intel.com/sites/landingpage/IntrinsicsGuide/](https://software.intel.com/sites/landingpage/IntrinsicsGuide/))