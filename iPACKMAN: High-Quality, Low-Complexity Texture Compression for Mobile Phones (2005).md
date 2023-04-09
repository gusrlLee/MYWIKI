# iPACKMAN: High-Quality, Low-Complexity Texture Compression for Mobile Phones
- Author : Ström, Jacob, and Tomas Akenine-Möller.
- Published in : ACM SIGGRAPH/EUROGRAPHICS conference on Graphics hardware. 2005.
- Tags : 

## Summary
본 논문은 PACKMAN 의 연구의 연장선이다. 
총 64개의 bits 를 이용하여 16 개의 pixel 들을 압축한다. 
24개의 bit는 base color 를 나타내고, 6 bits 들은 table codeword, 마지막 2 bit 는 flip, diff bit를 나타낸다.
그리고 마지막 32 bits 는 총 16 개의 pixel 을 2 bit 로 표현함으로써 압축한다. 
총 4 bpp 의 압축률을 보여주고 memory bandwidth 가 제한되어 있는 mobile platform을 위해 연구 되었다. 


## Detial 
### introduction
- First, the cost in gates should be low, especially for mobile phones.
- Second, avoiding look-up tables(LUTs) that depend on the current texture is preferred.
- Finally, the execution time for compressing a texture should be resonably short.
- 우리는 새로운 texture compression을 제안:
    - targetting for mobile phones.
    - 더 좋은 facto standard texture compression 보다 더 좋은 quality를 제공.
- basic idea of iPACKMAN:
    - 4x4 pixels를 이용. --> 기존 PACKMAN보다 더 큰 pixel block을 사용.
    - 단순히 PACKMAN의 block을 2개 합치는 방향으로 이용.

### Basic Design and Motivation
- A-PACKMAN-compressed image therefore has significantly less luminance banding than an image in which all pixels have been quantized to 12 bits.
- One way to combat these chrominance banding artifacts is to improve the color representation for slowly changing areas.
- 그리고 추가적으로 각 pixel의 차이를 측정해봤을 때, 우리는 각 pixel의 차이가 ==88%== 으로 총 8 bits 로 표현이 가능한 것을 실험으로 증명.
- table codeword 를 가지고 와서, 3 bits 로 표현하여 저장. --> table size는 8이다. 
    - 위 방식을 사용하면 0.2dB 의 PSNR 하락.
- 최종적으로 24 bits 의 Base color 를 나타내는 bit, 6 bit 의 table bits, 마지막으로 2 bits 는 flip, diff bit 이다.
- diff, flip 의 bit 들은 pixel 의 돌림 여부와 diff Base color 를 사용하여 RGB555 + RGB333 을 나타내는 여부이다. 
  
### Result 
- PSNR 측정 결과 PACKMAN 더 높은 quality 를 얻을 수 있다고 한다.
- Refer to Figure 4 graph
    
