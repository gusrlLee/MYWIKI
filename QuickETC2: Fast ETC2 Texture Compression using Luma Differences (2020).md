# QuickETC2: Fast ETC2 Texture Compression using Luma Differences
- Author : Nah, Jae-Ho. 
- Published in : ACM Transactions on Graphics (TOG) 2020
- Tags : Optimization, Texture Compression, ETC2

## Summary
극한의 최적화와 매우 빠른 성능을 목표로 하는 Encoder 이다.
접근 방식은 기존 ETC2 의 T,H,P-mode를 전부 수행 하지 않고 기존과 다르게 먼저 Luma 값을 기반으로 pixel의 RGB space 를 1차원으로 나타낸다. 
그리고 Luma 값의 Min/Max 값을 이용하여 각 차이 값을 구한다. 
다음 Min/Max 값의 차이 값에 대한 threshold를 두어 constrast 를 4 step으로 나누어 수행되는 ETC2 mode를 선택하게 만들어준다. 
결과적으로 기존 etcpak와 품질은 유지하며, 속도는 약 1ms 대로 현존하는 CPU encoder 중 가장 빠른 compressor 이다.

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
- If we can determine proper compression mode(s) in advance according to the luma difference of a block, we can avoid duplicated compression.
- We first calculate the luma values of each pixel in a block.
- And find min/max luma values in the block
- Using the value, We classify the block into one of four types:
    - very-low constrast --> planar mode
    - low-constrast --> planar mode.
    - mid-constrast --> ETC1 mode.
    - high-constrast --> T/H-mode.
- Contrast 의 기준은 threshold 를 두어서 결정.
- SIMD 연산을 이용하여 encoding 작업을 가속.

### OUR APPROACH
- First, we replace the 3D RGB space on pixel clustering with the 1D luma space.
- Second, we select a proper mode in advance
- Third, we reduce the number of distance candidates of a block
- Fourth, we implement the error calculation part using SSE/AVX2 intrinsics to exploit SIMD parallelism.
- Finally, we only use one base-color pair and do not allow additional iterations with multiple base-color pairs.
- detailed procedure of our approach:
    - First, we sort the pairs of a luma value and a pixel index in an input block, in ascending order of luma values.
    - Second, to find two proper clusters, we calculate the minimum value of the 15 summed luma differences in the line.
    - Third, based on the two clusters generated from the above luma-based clustering, we set a proper compression mode of the block before error calculation.
    - Forth, we calculate two base color from the two clusters.
    - Fifth, to reduce the number of error-calculation iterations using the distance table in the ETC2 format.
        - We set the start distance index in advance, according to the average RGB distances of the two clusters.
    - Sixth, we find the best candidate with the minimum error by changing the distance value.
    - This end-distance-index optimization is based on that pattern of error values is usually V-curves.

