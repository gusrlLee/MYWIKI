# Ray Accelerator: Efficient and Flexible Ray Tracing a Heterogeneous Architecture (2017)
- Author : Barringer, Rasmus, Magnus Andersson, and Tomas Akenine‐Möller.
- Published in : Computer Graphics Forum. Vol. 36. No. 8. 2017.
- Tags : Ray Tracing, Hybrid System.

## Summary
본 논문은 Ray Tracing을 Hybrid system을 이용하여 Ray Tracing을 가속하는 기법을 발표했다.
기본적인 아이디어는 Ray stream이라는 기본 data를 정의해두고 그에 따른 state를 둔다. 
그래서 shared memory에 저장을 하여 scheduler에 의해 scheduling에 의해서 작업을 수행한다. 
이때 사용되는 방식은 Ray stream을 GPU로 넘기기 전에 GPU가 가장 빠르게 처리할 수 있는 양의 Ray를 정의하고,
다음 Loop shader를 사용하여 조금 더 적은 latency를 overhead를 이용했다고 한다. 
그래서 결과적으로 조금 더 좋은 성능을 이끌어 냈다고 밝히고 효과적인 CPU-GPU communication 을 만들었다고 contributions 으로 언급하고 있다. 

> Multi-BVH에 대한 Reference 존재.

## Detail
### Introduction.
- Our goal is to create a highly optimized rendering framework for such architecture.
- We implementation both path tracer and Whitted-style ray tracer with multiple recursive rays at each surface.
- 하지만 여러 문제점이 존재하지만 이런 문제점을 해결하는 방식에 대해 이야기한다.
- To summarize the contributions of this paper:
    - it presents a flexible system for ray tracing-based rendering that uses a novel scheduling algorithm based on large packets of rays.
    - Using this approach, shader coherency can be intersection tested on both the CPU and the integrated graphics processor using shared memory for fast communication.
    - We also introduce _loop shader_ as a method to achieve flexibility similar to single-ray ray tracing, while still batching up rays for performance.
    - We introduce the idea to use graphics processor texture unit after traversal and route the result back to the CPU.

### Heterogeneous Ray Tracing
- In our approach, the majority of ray queries are handled by the GPU, while the remaining parts of the renderer is implemented on the CPU.
- In our hybrid system, work is distributed using large buffers of _arbitrary_ rays, which we call _ray streams_.
- threads in our software architecture:
    - _ray dispatch threads_ : This thread feed the GPU with work in the form of ray streams to perform ray queries on.
    - _host threads_ : this thread run on the CPU cores that perform actual CPU work.
- 이 thread들은 scheduler에 의해 관리되어 진다.
- Ray Stream 에 4 가지의 status 를 부여:
    - Empty
    - NeedMore
    - Trace
    - Shade
- 위의 status에 따라 GPU, CPU에서 작업의 위치를 결정하고 빠른 모든 자원을 사용한다는 장점을 어필.

### Loop Shaders
- For the purpose of this section, we divide the shader program into two parts, namely the _hit shader_ and the _loop shader_:
    - _hit shader_ : this shader refer to the part that runs over all the rays of the intersection-tested ray stream.
    - 하지만 이런 hit shader만 사용하게 된다면 recursively 하게 연산이 계속 되어지므로 메모리 관련 문제가 발생할 수 있음.
    - 이런 문제를 해결하기 위해서 _loop shader_를 사용.
    - _Loop Shader_ : this shader is invoked time a ray terminates without spawning a secondary ray.
- Loop shader는 stack을 사용해서 light의 path에 state를 stack에 보관.
- 이로서 미리 allocated 함으로써 빠른 link를 통해 작업을 진행할 수 있다고 한다.
- 이런 접근은 CPU와 GPU의 shared memory에 할당한다.
