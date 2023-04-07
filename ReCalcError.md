## Review Code of ReCalcError  
- 기존 RGB Data를 받아와서 진행하는 것을 확인할 수 있음
- iteration을 통해서 각 원하는 데이터를 뽑아옴
- idx[2][16] 이 부분은 데이터의 대한 data block에 대한 회전을 고려하여 배열 설정.
- First, we can get BGR data from comrpessed data.
- Second, delta BGR data is calculated using index table 
- 데이터를 가지고 와서 idx table을 이용해서 데이터를 뽑음.
- tab = ETC1 table index 
- sel = tsel의 index를 이용해서 데이터를 가지고 오는 느낌.
- j라는 table인덱스를 만들기 위해 bit operation을 통해서 가지고 옴.
- 가지고 온 table index와 distance를 이용해서 data에 대한 error를 게산.

## New Idea
- 교수님이 아이디어를 제시함.
    - 해당하는 방식은 아래와 동일.
    - 만약 encoding 작업을 진행하면서 codeword index가 커지게 된다면 betsy GPU로 encoding을 진행하는 방식.
    - 만약 index table codeword만으로도 distance를 유추해낸다면 원래 이미지와의 차이가 커질 것이 분명해지므로 이런 index를 가지고 처리하는 것이 더 좋은 방식이 되어질 수 있음.
- 구현은 기존 보다 단순해질 수 있음.
