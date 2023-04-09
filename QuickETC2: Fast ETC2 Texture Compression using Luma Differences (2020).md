# QuickETC2: Fast ETC2 Texture Compression using Luma Differences
- Author : Nah, Jae-Ho. 
- Published in : ACM Transactions on Graphics (TOG) 2020
- Tags : Optimization, Texture Compression, ETC2

## Summary

## Detail
### Introduction.
- To accelerate ETC2 compression, we introduce two new compression Techniques, named QuickETC2.
- The first technique is an early compression-mode decision scheme.
    - Instead of testing all ETC1/2 modes to compress a texel block, we select proper modes for each block by exploting the luma difference of the block to reduce unnecessary comperssion overhead.
- The second technique is a fase luma-based T-mode and H-mode compression method.
    - When clustering each texel into two groups, we replace the 3D RGB space with the 1D luma space.
    - And quickly find the two groups that have the minimum luma difference.
    - We also selectively perform the T- or H-mode and reduce its distance candidates, according to the lum a differences of each group.
- We use SIMD parallelism such as AVX2.

### EARLY COMPRESSION-MODE DECISION.
- 


### OUR APPROACH
- 

