# BetsyGPU: Texture compression Technique using GPU in Godot Engine
- Author : darksylinc
- GITHUB link : https://github.com/darksylinc/betsy
- Tags : Texture Compression, GPU, GLSL

## Summary
Betsy GPU 라는 것은 여러가지의 texture compression format을 위한 GPU encoder 이다. 
OpenGL API를 사용하여 한번에 많은 연산을 빠르게 처리할 수 있다. 
ETC2 format는 rg_etc1을 참고하고 k-means clustering iteration 에 대해서 모든 color값을 계산한다. 
그리고 P-mode 는 기존 P-mode와 다르게 inside pixel 까지 검사하여 더 높은 품질을 나타낼 수 있게 노력한다.
추가적으로 Simple Linear Regression 을 사용하여 더 fit한 color 를 만든다고 한다. 
그래서 최종적으로 가장 작은 error를 가지는 결과 값을 사용하여 compressed data를 저장한다고 한다. 

## Detail
### How does Betsy work?
- GPU encoding is all about exploting parallelism.
- Compute each compressed block in each GPU thread of OpenGL Compute shader.

### ETC1
- ETC1 Encoder of Betsy is based on was written by Rich Geldreich --> rg_etc1 : https://code.google.com/archive/p/rg-etc1/
    - He uses an algorithm for narrowing down the search within the colour space and then followes severl refinement steps to improve quality.
    - 이 C++ code를 기반으로 GLSL로 Convert 했다.
- Difference between CPU version and GPU version:
    - ETC1 divides the 4x4 block in two subblocks of either 4x2 or 2x4.
    - 그래서 총 아래와 같이 4 가지의 possible mode로 구성:
        - RGB444 + 4x2 scheme.
        - RGB444 + 2x4 scheme.
        - RGB565 + delta + 4x2 scheme
        - RGB565 + delta + 2x4 scheme
    - Compute Shader work local size를 (4, 4, 4) 로 두어서 GPU에서 한번에 수행.

### ETC2
- T/H-mode:
    - This code is based on was written by Jean-Philippe ANDRE --> link : https://github.com/titilambert/packaging-efl/blob/master/src/static_libs/rg_etc/etc2_encoder.c
    - K-means Clustering 의 모든 Candidates 들의 경우의 수 16 + 15 + ... 를 구하여 모든 경우의 수를 Compute Shader Thread 120개를 이용하여 한번에 수행.
    - 이 K-means Clustering 결과 120개를 가지고 모두 T/H mode를 수행. --> 가장 적은 error를 가지는 mode를 선택.

- P-mode:
    - There is an edge case though:
        - What if we have a smooth gradient, but the pixel at location O, H and/or V happens to be an outlier?
        - To account for this issue, rather than picking O at (0,0) we try other options.
    - 즉, 원래의 P-mode와 다르게 바깥 쪽 pixel을 선택하지 않고 inside 에 있는 pixel을 선택.
        - H at (3,N) --> original case.
        - H at (2,N) --> alternate case.
        - V at (N,3) --> original case.
        - V at (N,2) --> alternate case.
    - 그리고 16 개의 thread를 이용하여 가장 낮은 error를 Pick한다.
    - 추가적으로 Simple Linear Regression을 사용
        - to find new values of O,V and H that produce the best fitting gradient.
        - SLR algorithm:
            - Given a set of points.
            - find a line that tries to go through all of them
            - minimizing the distance.
            - SLR equation = $y = ax + b$
- Select lowest error mode.
    - 마지막 단계로 각 Mode에서 나온 Error값을 비교하여 가장 낮은 error를 가지는 mode를 pick하여 결과 값을 저장한다. 
      

