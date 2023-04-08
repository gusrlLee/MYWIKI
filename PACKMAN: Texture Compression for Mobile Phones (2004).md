# PACKMAN: Texture Compression for Mobile Phones
- Author : Ström, Jacob, and Tomas Akenine-Möller.
- Published in : ACM SIGGRAPH 2004 Sketches
- Tag : Texture Compression, Texture

## Summary
본 논문은 Mobile device를 위한 새로운 lossy texture compression 방식을 제안한다.
기존 방식들과 다르게 총 8개의 pixel을 이용하여 32bit로 compression을 진행한다. 
압축비는 4bpp를 나타내고 추후 ETC1, ETC2에 대한 선행 연구로 볼 수 있다. 

## Detail 
### Introduction.
- Mobile device를 위한 새로운 lossy Texture Compression (TC) scheme를 제시.
- Compresses $2 \times 4$ pixel blocks into 32bits. --> 4bpp.
- TC is therefore a fundamental technqiue for reducing bandwidth usage.
- Most TC schemes compress each block of individually.

### Algorithm
- The image is divided into $2 \times 4$ pixel blocks with each block represented by 32 bits.
- 12 bits = "base color bits", 20bits = "remaining bits"
- Required "pixel index" --> two bits per pixel 
- four bits --> "table index" : 전체의 block을 위해 사용되는 table index를 뜻함.
- Decompressing a single pixel works as follows:
    - The base color of the block is converted from 12 to 24 bits.
    - The 4-entry table is located using the table index.
    - The final color is computed by additively modifying the 24-bit base color with the value from the 4-entry table that corresponds to the pixel index.

