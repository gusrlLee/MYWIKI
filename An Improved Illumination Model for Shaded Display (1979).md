# An Improved Illumination Model for Shaded Display
- Author : Turner Whitted
- Published in : Proceedings of the 6th annual conference on Computer graphics and interactive techniques. 1979
- Tags : Ray Tracing

## Summary
Ray Tracing의 시초 논문이다. 3113회의 인용수를 가지고 있으며, Ray Tracing의 가장 기본적인 내용을 담고 있는 논문이다.
image안의 각 pixel의 intensity에 영향을 주는 global illumination information는 intensity 가 계산이 되어질 때 알려져야 한다. 
간단하게 이 information은 viewer의 시점에서 어떤 표면과 첫번째로 마주친 지점과 그리고 다른 표면과 마주쳐 light source로 이어지는 “Rays”의 Tree에 복원되어진다. 
이 visible surface algorithm은 이 tree를 만들어 각 pixel에 대해 shading, reflection, shadow에 대한 연산을 진행한다.

## Detail
$$I = I_a + k_d \sum^{j=l_s}_{j=1} (\bar{N} \cdot \bar{L_j}) + k_sS + k_lT$$
where  
$S$ = the the intensity of light incident from the direction  
$k_l$ = the transmission coefficient.  
$T$ = the intensity of light from the $\bar{p}$ direction.  
