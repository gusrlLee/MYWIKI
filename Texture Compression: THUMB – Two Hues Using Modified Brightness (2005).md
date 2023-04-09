# Texture Compression: THUMB – Two Hues Using Modified Brightness
- Author : PETTERSSON M., STRÖM J.
- Published in : Sigrad, Lund 2005 
- Tags : Texture Compression, Texture, ETC2

## Summary
본 논문은 ETC1의 고질적인 box artifact를 개선시키기 위해 RGB space의 패턴에 따라 2가지의 mode를 추가한다.
이런 mode를 추가한 동기와 Encoding/Decoding 방식을 제안한다. 
Selective Comrpession 방식을 사용하여 0.05 dB의 quality 하락은 존재했다. 
하지만 Time performance 를 개선 시킨 방안을 채택 했다. 
ETC1의 box artifact 를 개선시킬 수 있는 conclusion 을 가질 수 있었고,
추후 ETC2 의 format 의 발판이 되었다. 

## Detail
### introduction.
- A Texture compression system differs from a normal image compression system in a number of ways:
    - First, it needs to allow random access to the texels.
    - Second, the decompression of a block should ideally be of low complexity.
    - Third, it is advantagous to avoid texture dependent look-up tables (LUTs)
- So In this paper, They propose new texture compression advanced iPACMAN

### THUMB Texture Compression
- Basic Design and Motivation:
    - new scheme is called _Two Hues Using Modified Brightness_ of THUMB.
    - RGB space 상의 pixel 들의 pattern이 T, H 와 비슷하게 생겨 각각의 mode 를 생성.
    - 그래서 각각의 T, H mode 의 base color에 distance를 두어 더 다양한 색상을 표현할 수 있도록 만듬.
- Compression:
    - LBG Compression.
    - Radius Compression.
    - Selective Compression --> Solution!
- Combining THUMB and iPACKMAN:
    - 각각의 pixel block은 2가지의 mode로 encoding:
        - ETC1 mode
        - THUMB mode
    - 2 개의 encoding 결과에서 가장 낮은 MSE를 선택하여 저장하는 방식.
### Result 
- 결과 사진을 보면 대각선 부분에서 더 iPACKMAN 보다 더 부드러운 결과를 얻는 것을 확인.
- box artifact 가 사라지는 것을 확인할 수 있었음.
