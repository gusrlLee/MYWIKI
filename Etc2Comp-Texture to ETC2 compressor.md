# Etc2Comp-Texture to ETC2 compressor
- Author : Google Inc and Blue Shift 2017
- github : https://github.com/google/etc2comp
- Blog : https://medium.com/@duhroach/building-a-blazing-fast-etc2-compressor-307f3e9aad99#.acqks0pzc
- Tags : Texture Compression, ETC2, Texture.

## Summary
본 Open source는 ETC2 format의 본질적인 artifact를 개선시키기 위해 개발된 코드이다. 
결국 image block에 pixel들을 다시 다른 Mode들로 개선을 시켜나가며 quality를 올리는 것을 목표로 한다.
하지만 이런 RGB search space를 넓게 사용을 한다면 Iteration의 수는 매우 클 것이며 연산 성능에 직결된다. 
그래서 Effort라는 paramter를 두어서 image block 의 문제있는 Block들의 처리할 %를 선택하여 성능을 개선시키는 방법을 사용했다. 

## Detail
### Why is Etc2Comp so much faster?
- A directed block search : Etc2Comp uses a much more limited, targeted search that is based on block type.
- A full-effort setting : While Etc2Comp contains a scalar value which lets you adjust how much effort you want in search-space exploration.
- Highly multi-threading code.

### Understanding the problem.
- 고품질의 ETC2로 encoding 된 texture를 만드는 것은 image의 각 block에 대해 어떤 block format이 가장 좋은 시각적 결과를 나타내는 것을 알아야 한다.
- 간단히 말해서, search space은 test하기에 너무 많은 양을 수행해야 하고, 이것은 encoder 성능과 직접적으로 관련된 큰 문제.
- texture compression를 압축하는데 걸리는 시간을 개선하고 싶다면 search space을 최소화해야 한다. 

### Generating the proto-block-chains, offline
- 각 block이 전체 space를 탐색하는 대신 potential space를 통해 direct graph set 를 정의한다.
- 각 graph mode가 특정 블록 원형에 맞을 가능성에 따라 모드의 순서를 정의한다.
- 그래서 모든 모드에 대해서 미리 block-chain을 정의한다.

### Block scoring, sorting, and assigning
- encoder가 image를 받으면, 가장 먼저 MSE를 사용하여 perceptual score를 계산한다.
- 그래서 score에 따라서 image pixel들을 sorting 한다.
- score 를 기반으로 한 이 sorting 과정은 encoding 과정에서 image를 통과할 때마다 여러 번 발생한다.
- 그럼 다음 block에 block-archetypes 에 대해 test 되고, 가장 잘 맞는 format-chain을 할당한다.
- 블록은 시간이 지남에 따라 sort process에서 끝날 수 있는 위치에 관계없이 전체 encoding process를 통해 이 체인을 유지할 것이다.
 
### Block testing 
- 우리 목록의 각 block에 대해, 우리의 목표는 proto-chain 을 통해 적절하게 반복하고, 어떤 모드가 이 블록에 가장 적합한지 결정 하는 것이다.
- encoding이 시작되면, 각 블록은 proto-chain의 첫 번째 형식으로 기본 setting 되어진다.
- 반복이 발생함에 따라, source block은 chain의 다음 mode를 사용하여 encoding되며, 시각적인 결과는 가장 좋은 결과와 비교한다.

### Iterating 
- 블록의 encoding은 pass에서 일어난다.
- encoder 는 각 pass 동안 블록에 대해 하나의 mode만 test한다는 점에 유의하는 것이 중요하다.
- image의 모든 block이 test되면, 모든 block이 다시 sorting 되고 update된 quality 값을 할당하는 2단계로 돌아간다. 

### The effort parameter
- etc2comp는 effort 라는 변수를 할당해서 사용한다.
- 기존 effort로 기준을 두어서 Image blocks에 대한 얼만큼의 block을 다시 test를 할 것인지 선택적으로 고르는 것이다.
- effort paramter는 0 ~ 100% 비율을 두어서 test를 setting 한다.

