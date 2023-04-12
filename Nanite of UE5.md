# Nanite of UE5

## Motivation
- 아티스트에 대한 걱정을 줄임으로써 더 편한 작업 환경을 제시해줌.
    - Polycount, Draw calls, memory cost에 대한 부담을 줄여주는 것이 목표.
    - 여기서 그치지 않고 Rendering engine에 넣기만 하면 Auto-Processing, Auto-LOD building 작업이 진행하는 것을 목표.
    - 추가적으로 아티스트가 만든 Quality를 유지하면서 processing을 진행하도록 만듬.
     
- 하지만 이런 방식은 단순한 것이 아님.
    - Not just memory management.
    - Geometry detail은 rendering cost에 대해 직접적인 영향이 존재.
    - Geometry 는 filterable 하지 않기 때문.

- Option:
    - Voxels
    - Subdivision surfaces
    - Displacement maps
    - Points
    - Triangles --> Solution!

## GPU Driven Pipeline
- 우리는 Triangle rendering pipeline 을 구축하기 위해 필요한 요소:
    - GPU instance cull
    - Triangle Rasterization.

- 우리는 UE4의 Renderer를 Refactoring 해나감.
    - GPU scene 을 memory에 저장. --> frame 간에 유지가 됨.
    - 장면의 사이에서 change 되는 부분만 update.
    - vertex/index들은 a single large resource 로 저장. --> 데이터에 대한 추가적인 bind instruction이 필요 없음.
    
## Triangle cluster culling 
- GPU Driven Pipeline 을 사용한다는 것은 많은 연산을 진행할 수 있다는 장점이 존재.
- 하지만 아직도 상당한 양의 triangle을 처리하지 못한다는 문제가 발생.
- 이런 문제를 해결하기 위해 Triangle Cluster culling 을 구축.
- 불필요한 작업을 Culling 함으로써 속도를 개선.

## Two pass occlusion culling
- HZB를 구축하여 Occlusion을 사용.
- 하지만 bulit 된 HZB를 다시 reprojection하는 것은 framerate의 유지가 어려움.
- 그래서 Two pass occlusion culling 방식을 사용.
    - Draw what was visible in the previous frame.
    - Build HZB from that
    - Test the HZB to determine what is visible now but wasn't in the last frame and draw anything that's new.

## Decople visibility from material 
- UE5에서는 Visibility와 Material을 분리하는 것을 선호.
- material evalution 작업과 depth buffer를 disconnection 하는 것을 의미 --> Rasterization동안 Shader code switching 이 발생하지 않게 만듬.
- 이런 작업을 위해서 여러가지 옵션들이 존재:
    - REYES
    - Texture spacing shading
    - Deferred materials
- 하지만 이런 방식들은 overshade에 대해서 cost가 존재하고 View dependent와 animating에서는 안좋은 결과를 나타냄.

## Visibility buffer
- 그래서 가장 좋은 방식은 visibility buffer를 이용하는 방식. --> 가장 좋은 선택!
- Basic Idea:
    - To write the smallest amount of geometry data to the screen in the form of depth, instaneID, and TriangleID.
- 따라서 Visibility buffer는 많은 데이터들을 가지고 있음.
- 하지만 성능은 빠름!
    - lots of cache hits.
    - No overdraw or pixel quad inefficiencies

## Sub-linear scaling
- 앞의 visibility buffer를 사용하여 속도는 개선되어 짐. --> 하지만, scaling 작업은 아직 linearly 한다는 문제.
- Instance --> scaling 작업이 편함.
- Triangle --> scaling 작업이 어려움.
- 가정 --> Why should we draw more triangles that pixels?
- cluster의 측면에서 우리는 every frame에서 object의 Dense나 개수를 무시하고 같은 수의 cluster를 draw하는 것을 원함. --> 하지만 불가능.
- 또한 screen resolution이 rendering cost에 직접적인 영향이 있음. --> Screen complexity가 아님!
- "That means constant time in terms of scene complexity and constant time means level of detail"
    - 위 문장을 내가 이해한 바로는, scene complexity는 그려야 할 땐 dynamic 하지 않고 constant함.
    - 그래서 rendering cost에서는 일정한 값을 가지게 됨.
    - 즉, resolution이 Rendering cost에 영향이 있고 geometry data는 결국 static한 Data이기 때문에 Constant time이라고 의미할 수 있다고 생각.
- scene complexity --> constant time --> Level of Detail

## Level of Detial
- 우리는 scene 의 complexity를 Hierachy한 tree 형태로 나타낼 수 있음.
- parent node = simplification child node
- space projected error 에 기반한 view dependent way 방식으로 rendering --> virtual texturing technique 과 비슷.
- 하지만 Crack이라는 문제점 발생 --> 주위의 geometry data와 level 이 달라 생기는 artifact
- 이런 문제를 해결하기 위한 방식:
    - naive approach --> Lock Shared boundary edges during simplification.
    - UE5 approach --> Group Clustering !! 
      
## Build Operation.
- Group:
    - Clusters to clean their shared boundary
    - Group partitioning problem --> METIS library를 사용.

- Merge:
    - triangles from group into shared list

- Simplify:
    - to 50% the # of triangles
    - QEM (Quadric Error Metric) 를 사용하여 error 값을 기반으로 계산되어 짐.

- Split:
    - simplified triangle list into clusters
    - Group step과 동일하게 수행 됨.

## Hierarchical culling - naive approach
- 기본적으로 Hierachy tree를 어떻게 parallel 한 방식으로 rendering을 할 수 있는 지에 대한 설명이 진행됨.
- GPU 연산에 Sync를 맞추어 계산을 진행.
- 하지만 이 방식은 비효율적이다라고 말함.

## Persistent threads
- UE5 에서 제시한 방안.
- 기존에 만든 thread들을 재사용하면서 job queue를 이용하여 재사용하는 Thread들에게 작업을 분산시키는 방식.
- mini job system에서 사용.
- naive한 approach 보다 25% 빨라짐.

## Rasterization.
- Tiny Triangle --> Software Rasterization이 3x faster!
- Larger Triangle --> Hardware Rasterization으로 처리.
- hierarhical instancing 의 memory 문제를 해결하기 위한 Solution --> Visibility Imposter.

## Material Evaluation.
- 앞서 설명한 것처럼 우리는 Decopling visibility buffer from material evaluation 했다.
- 그러면 우리는 visibility buffer를 그리고 그 다음 그 pixel의 material을 어떻게 결정을 하나. --> 이것 또한 visibility buffer를 이용.
- 삼각형을 통한 attribute의 Analytic derivatives를 계산한다.
- 모든 sample call은 gradient를 사용하여 sampleGrad를 사용.
- 이러한 노드는 artist 가 생성한 Node graph를 통해 chain rule을 사용하여 자동으로 propagation 이 된다.

## Shadow
- geometry를 그리는 것만 중요한 것이 아닌 shadow를 그리는 것 또한 중요하다.
- 감사하게도 대부분의 light와 geogmetry는 casting shadow 할 때 움지기이지 않는 물체이다.
- 그래서 virtual shadow maps를 지원함.
- 16K 의 해상도의 map을 모든 곳을 위해 사용함.
- Multi-view rendering을 사용하여 여러 multiview support를 추가. --> 성능향상.

