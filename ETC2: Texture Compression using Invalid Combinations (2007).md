# ETC2: Texture Compression using Invalid Combinations
- Author : Jacob Ström and Martin Pettersson
- Published in : Graphics Hardware 2007
- Tags : ETC, Texture Compression

## Summary 
본 논문은 기존 ETC1에서 3개의 mode를 추가하여 개선 시킨 연구이다. 
기존 bit의 구조에서 T/H-mode 그리고 P-mode를 추가하여 image quality를 개선시키는 방향으로 진행된다. 
기존에 읽었던 THUMB의 연구에서 P-mode 가 추가된 것을 알 수 있다. 
추가적으로 diff bit의 Overflow를 이용하여 pattern을 찾고 Overflow의 결과에 따른 pattern을 이용했다.
그래서 그 각 RGB에서 발생하는 Overflow를 이용하여 T, H, P mode 를 정의하고 그에 따른 encoding 방식을 사용했다.
결과적으로 더 기존 보다 더 좋은 quality를 획득할 수 있었고 기존 연구보다 더 다양한 Base color를 나타낸다.

## Detail
### Introduction
- Texture Compression can be used to reduce bandwidth usage.
- Our major contribution is to show how three extra modes can be added by the use of invalid bit combinations.

### Invalid Bit Sequences.
- One major contribution of this paper is to show how invalid bit combinations can be used to improve ETC.
- This idea is related to the ordering trick that has been used in DXT1 to select the one-bit alpha mode for RGBA textures.
- If the diffbit is not set, the 63 remaining bits are decoded using the regular individual mode from ETC.
- If the diffbit is set, we first see whether the addition $R1 = R0 + dR$ underflows or overflows.
    - 다른 G, B 에 대해서도 동일하게 적용되는 idea.
- If we are not overflowing, we are decoding the 63 bits the usualway, using the differential mode from ETC
    - Refer to Figure 3.
- 만약 여기에서 overflow 가 발생한다면 64 - 8 - 1 = 55 bits 의 payload 를 얻을 수 있다.
- 본 논문에서 표시된 Table 1 을 확인한다면 overflow 가 발생할 때 pattern 을 보이는 것을 확인.
- 이런 방식으로 4bits 를 이용하여 T, H, 추가적인 P mode 까지 emit 을 할 수 있음.

### New Modes in ETC2
- T-mode :
    - Note how most of the colors are in the top left group, which would suggest that regular ETC compression would work quite well with intensity variation of a yellow base color.
    - However, some of the pixels are of a rather different color, which breaks the ETC model.
    - 이러한 이유 때문에 T-mode를 소개. --> T-mode motivation.
    - Each pixel can here choose from four paint colors.
    - Three of the paint colors are obtained by modifying the first base color along the intensity direction by adding the vectors --> vector : (d, d, d)
    - The distance $d$ is obtained from a small look-up table.
- H-mode :
    - T-mode 와 비슷하게 distance 를 이용하여 base color를 표현.
    - H-mode 도 동일하게 T-mode 처럼 small look-up table을 가지고 있음.
    - since the H-pattern is completely symmetrical, we can swap the base colors and obtain exactly the same result.
- p-mode :
    - it is desirable to find a representation that can cope well with smoothly varying chrominances.
    - Therefore we propose the use of a planar approximation of the color components in the block.
    - To specify a plane, it suffices to specify the colors in three locations in the block.
    - We have positioned three red components $R_0, R_H, R_V$ in certain positions in the block.
    - So, The red component can now be calculated using interpolation.
