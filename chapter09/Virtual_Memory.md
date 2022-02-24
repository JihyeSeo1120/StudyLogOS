# Virtual Memory

주소변환은 운영체제가 관여하지 않았지만 
virtual memory 기법은 전적으로 운영체제가 관여한다. 
또한 메모리 관리 기법 중에서 Paging 기법을 사용한다고 가정한다.

## Demand paging

- **실제로 필요할 때 page를 메모리에 올린다.**
- 효과
    - I/O 양의 감소
    - memory 사용량 감소
    - 더 빠른 응답 시간
    - 더 많은 사용자(프로그램) 수용

## Vaild / Invalid bit의 사용

![https://user-images.githubusercontent.com/76726411/148774295-72f6bc16-d037-42c8-a85c-378fd9a4db42.png](https://user-images.githubusercontent.com/76726411/148774295-72f6bc16-d037-42c8-a85c-378fd9a4db42.png)

당장 필요한 A,C,F는 physical memory에 올라가 있고(valid), 그렇지 않은 부분은 backing store에 내려가 있다(invalid). 사용되지 않는 주소 영역 또한 invalid

### invalid의 의미

- **사용되지 않는 주소 영역**인 경우
- **페이지가 물리적 메모리에 없는 경우** : 디스크의 swap area 영역에 있다.
1. 처음에는 모든 page entry가 invalid로 초기화
2. **address translation(주소변환)시에 invalid bit이 set 되어**있다면 **page fault**가 일어난다.
3. page fault가 일어나면 사용자 프로세스가 해결하지 못하므로 운영체제에게 CPU 제어권이 넘어감(page fault trap이 걸림 - 일종의 software interrupt)

## Page Fault

![https://user-images.githubusercontent.com/76726411/148774327-86d2ab41-cbbf-40ed-96e4-1aa583ed1fc1.png](https://user-images.githubusercontent.com/76726411/148774327-86d2ab41-cbbf-40ed-96e4-1aa583ed1fc1.png)

- **invalid page를 접근하면 MMU(주소변환해주는 hardware)가 trap을 발생**시킨다. (page fault trap) CPU가 자동적으로 운영체제에게 넘어온다.
- kernel mode로 들어가서 page fault handler가 invoke 된다.

<page fault 처리 순서>

1. invaild reference | 잘못된 요청이 아닌지 확인한다. (ex: bad address, protection violation) 
잘못된 요청이면 abort process로 종료, 정상적인 메모리 요청이면 페이지를 디스크에서 메모리로 올려줘야한다.
2. Get an empty page frame(빈 page frame을 하나 획득하고 없으면 뺏어온다 - replace).
3. 해당 page를 disk에서 memory로 읽어온다.
    1. disk I/O가 끝나기까지 이 프로세스는 CPU를 선점(preempt)당하고(blocked state) ready state의 가장 우선순위 높은 프로세스가 CPU를 가져감
    2. disk read가 끝나면 page tables entry 기록, vaild/invalid bit = “valid”
    3. ready queue에 process를 insert → dispatch later
4. 이 프로세스가 CPU를 잡고 다시 running
5. 중단되었던 instruction 재개

## Performance of Demand Paging

page fault가 났을 때 disk로의 접근은 대단히 오래걸리는 작업이다. page fault가 얼마나 나느냐에 따라 메모리 접근시간이 크게 차이난다.

대부분의 경우 page fault가 나지 않는다(p = 0.0x) 
그러나 page fault가 한번 나면 overhead가 매우매우 크다.

## Free frame이 없는 경우

### **page replacement**

- **어떤 frame을 빼앗아올지 결정**해야 한다.
- 동일한 page가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있다.
- 바로 사용되지 않을 page를 쫓아내는 것이 좋다.

![https://user-images.githubusercontent.com/76726411/148774472-f41925cc-40bb-4254-a099-49fc1322887a.png](https://user-images.githubusercontent.com/76726411/148774472-f41925cc-40bb-4254-a099-49fc1322887a.png)

### **replacement algorithm**

- **page-fault rate을 최소화**하는 것이 목표
- 알고리즘의 평가
    - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사.
    - refrence string 예시 : 1,2,3,4,1,2,5,1,2,3,4,5 (page)

## Optimal Algorithm

가장 좋은 Replacement Algorithm이다.

MIN(OPT) : 미래에 무엇이 들어올지 다 알고있다고 가정한다.

가장 먼 미래에 참조되는 page를 replace한다.

이건 실제로는 쓰일 수 없고(미래를 예측할 수 없다) 다른 알고리즘의 성능에 대한 upper bound를 제공한다. (아무리 좋은 알고리즘을 만들어도 이것보다 좋은 성능을 낼 수 없다.)

## FIFO(First In First Out) Algorithm

**FIFO : 먼저 들어온 것을 먼저 내쫓는다.**

![https://user-images.githubusercontent.com/76726411/148774510-fd9738a5-daa6-4bbf-990f-a052a3a7237e.png](https://user-images.githubusercontent.com/76726411/148774510-fd9738a5-daa6-4bbf-990f-a052a3a7237e.png)

FIFO Anomaly(특이한 성질) : 메모리 frame을 늘려주면 성능이 더 좋아질 것으로 예측하나 오히려 성능이 더 나빠지는 경우가 발생할 수 있다.

## LRU(Least Recently Used) Algorithm

**LRU: 가장 오래 전에 참조된 것을 지운다.**

![https://user-images.githubusercontent.com/76726411/148774565-61a25300-dd8d-40f1-b67e-75ae98653327.png](https://user-images.githubusercontent.com/76726411/148774565-61a25300-dd8d-40f1-b67e-75ae98653327.png)

## LFU(Least Frequently Used) Algorithm

**LFU: 참조 횟수(reference count)가 가장 적은 page를 지운다.**

- 최저 참조 횟수인 page가 여러개 있는 경우
    - LFU 알고리즘 자체에서는 여러 page중 임의의 page를 지운다.
    - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우도록 할 수 있다.

**장점**

- LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 더 정확히 반영가능하다.

**단점**

- 참조 시점의 최근성을 반영하지 못한다.
- LRU보다 구현이 복잡하다.

## LRU와 LFU 알고리즘 예제

![https://user-images.githubusercontent.com/76726411/148774628-d60e1c94-7763-435d-aad4-c098f711f036.png](https://user-images.githubusercontent.com/76726411/148774628-d60e1c94-7763-435d-aad4-c098f711f036.png)

## LRU와 LFU 알고리즘의 구현

![https://user-images.githubusercontent.com/76726411/148774665-3eb880f8-0460-4b5c-b51a-b1658d28732b.png](https://user-images.githubusercontent.com/76726411/148774665-3eb880f8-0460-4b5c-b51a-b1658d28732b.png)

- LRU는 한 줄로 줄세워서 linked list 형태로 구현 → O(1)
- LFU는 heap 구조를 사용해서 tree 형태로 구현 → O(log n)

## 다양한 캐슁 환경

### 캐슁(caching) 기법

- 한정된 빠른 공간(=캐쉬)에 요청된 데이터를 저장해 두었다가 후속 요청시 캐쉬로부터 직접 서비스하는 방식
- paging system 뿐만 아니라 cache memory, buffer caching, web caching등 다양한 분야에서 사용한다.

### **캐쉬 운영의 시간 제약**

- **교체 알고리즘에서 삭제할 항목을 결정하는 일에 지나치게 많은 시간이 걸리는 경우 실제 시스템에서 사용할 수 없다.**
- buffer caching이나 web caching의 경우
    - O(1) ~ O(log n) 정도까지 허용
- paging system인 경우
    - page fault인 경우에만 OS가 관여한다.
    - O(1)인 LRU의 list 조작조차 불가능하다.
    - 페이지가 이미 메모리에 존재하는 경우 참조시각 등의 정보를 OS가 알 수 없다.

### Paging System에서 LRU, LFU가 가능한가?

운영체제가 참조 횟수가 가장 적은 page를 알 수 있는가?

**알 수 없다.**

why?) 메모리에 page 정보가 이미 있을 경우(page table에서 valid인 page를 사용할 때)는 운영체제에게 CPU가 넘어가지 않는다. 하드웨어적으로 주소변환을 해서 CPU가 읽어들인다. 따라서 운영체제는 누가 얼만큼 참조되었는지 알 수 없다.

page fault가 발생해야 CPU의 제어권이 운영체제로 넘어오고, 운영체제는 disk에 있던 page가 메모리로 올라온 시간을 알 수 있다.

그래서 paging system에서는 운영체제에게 주어지는 정보가 반쪽만 주어지게 된다. LRU의 linked list, LFU의 tree 자료구조의 조작을 운영체제가 하는건데 실제로는 정보가 반쪽만 존재하므로 정확한 조작을 할 수 없다.

→ **paging system에서는 LRU, LFU 알고리즘을 사용할 수 없다.**

## Clock Algorithm

paging system에서는 LRU, LFU 알고리즘을 사용할 수 없고, **일반적으로 clock algorithm을 쓴다.**

![https://user-images.githubusercontent.com/76726411/148779846-10223ef0-2915-4ec1-a620-84da89f85b7c.png](https://user-images.githubusercontent.com/76726411/148779846-10223ef0-2915-4ec1-a620-84da89f85b7c.png)

**Clock algorithm**

- LRU의 근사(approximation) 알고리즘
- second chance algorithm
- NUR(Not Used Recently) 또는 NRU(Not Recently Used)

### 알고리즘 진행과정

1. reference bit을 사용해서 교체 대상 page 선정(circular list)
2. hardware가 해당 page 참조시에 reference bit을 1로 변경
3. reference bit이 0인 것을 찾을 때까지 포인터를 하나씩 앞으로 이동
4. 포인터 이동 중에 포인터로 가리킨 reference bit 1은 모두 0으로 바꿈
5. Reference bit 0인 것을 찾으면 그때 페이지 교체
6. 한 바퀴 되돌아와서도(=second chance) 0이면 그 때 replace 당함
    - 시계바늘이 한 바퀴 돌 때 참조가 한 번도 되지 않은 놈은 replace

→ 자주 사용되는 page라면 second chance가 올 때 reference bit이 1(참조당함)이 될 것이다.

### **Clock algorithm의 개선**

- reference bit과 modified bit(dirty bit)을 함께 사용한다.
    - reference bit = 1: 최근에 참조된 페이지
    - modified bit = 1 : 최근에 변경된 페이지(I/O를 동반하는 page)
- memory에서 올라온 이후로 page에 CPU가 write를 해서 내용이 바뀌었다는 의미이다.
- 따라서 replace될때 바로 지우면 안되고 backing store에도 그 내용을 수정해줘야 한다.
- modified bit이 1인 page는 replace 할 때 해야 할 일이 많으니까 되도록 replace 하지 않는 식으로 동작시키면 속도가 개선..

## Page Frame의 Allocation

현재는 어떤 프로세스에 속한 page인지 무관하게, clock algorithm을 이용해서 쫓아내고 있다. 실제로 프로그램이 page fault를 잘 안내면서 원활하게 실행되기 위해서는 한 프로그램의 page들이 메모리에 같이 올라와 있어야 더 효율적이다.

### Allocation problem

각 프로세스에 얼마만큼의 page frame을 할당할 것인가?

### Allocation의 필요성

메모리 참조 명령어 수행시 명령어, 데이터 등 여러 page 동시 참조한다.

- 명령어 수행을 위해 최소한 할당되어야 하는 frame의 수가 있다.

Loop을 구성하는 page들은 한꺼번에 allocate 되는 것이 유리하다.

- 최소한의 allocation이 없으면 매 loop마다 page fault가 발생기 때문이다.

### Allocation Scheme

어떻게 프로그램별로 page 개수를 할당해줄까?

- equal allocation: 모든 프로세스에 똑같은 개수 할당
- proportional allocation: 프로세스 크기에 비례하여 할당
- priority allocation: 프로세스의 priority에 따라 다르게 할당

## Global vs Local Replacement

할당을 해주지 않더라도 replacement 알고리즘을 사용하다보면 어느정도씩 할당하게 되는 효과가 있다.

### Global replacement

- replace시 다른 프로세스에 할당된 frame을 빼앗아 올 수 있다.
- Process별 할당량을 조절하는 또 다른 방법이다.
- FIFO, LRU, LFU 등의 알고리즘을 global replacement로 사용시에 해당
- working set, PFF 알고리즘 사용

### Local replacement

- 자신에게 할당된 frame 내에서만 replacement
- FIFO, LRU, LFU등의 알고리즘을 process 별로 운영할 때

## Thrashing

![https://user-images.githubusercontent.com/76726411/148779925-e3a66118-dde8-4c1b-ba9e-19a7770288c5.png](https://user-images.githubusercontent.com/76726411/148779925-e3a66118-dde8-4c1b-ba9e-19a7770288c5.png)

### Thrashing

- 프로세스의 원활한 수행에 필요한 최소한의 page frame 수를 할당 받지 못한 경우 발생
- page fault rate이 매우 높아짐
- CPU utilization이 낮아짐
- OS는 MPD(Multiprogramming degree)를 높여야 한다고 판단
- 또 다른 프로세스가 시스템에 추가됨(higher MPD)
- 프로세스 당 할당된 frame의 수가 더욱 감소
- 프로세스는 page의 swap in / swap out으로 매우 바쁨
- 대부분의 시간에 CPU는 한가함
- low throughput

이 문제를 해결하기 위해서 degree of multiprogramming을 조절해 줘야 한다. → 동시에 올라가 있는 프로세스의 갯수 조절

- working-set
- PFF(page-fault frequency)

## Working-Set Model

### Locality of reference

- 프로세스는 특정 시간 동안 일정 장소만을 집중적으로 참조한다. (ex. for 문을 도는 경우, 어떤 함수를 실행하는 경우 등)
- **집중적으로 참조되는 해당 page들의 집합을 locality set**이라고 한다.

### Working-Set model

- Locality에 기반하여 프로세스가 **일정 시간동안 원활하게 수행되기 위해 한꺼번에 메모리에 올라와 있어야 하는 page들의 집합**을 **working set**이라 정의한다.
- working set 모델에서는 프로세스의 working set 전체가 메모리에 올라와 있어야 수행되고 그렇지 않을 경우 모든 frame을 반납한 후 disk로 swap out(suspend 상태)
- Thrashing 방지
- Multiprogramming degree 조절

### Working-Set Algorithm

과거를 통해 working set을 추정하는 방식

![https://user-images.githubusercontent.com/76726411/148779948-1a6a3793-c213-466f-a8cb-391dcea0af62.png](https://user-images.githubusercontent.com/76726411/148779948-1a6a3793-c213-466f-a8cb-391dcea0af62.png)

### Working-Set의 결정 방법

- 현재시점부터 과거의 어떤 시점까지의 시간 크기만큼을 window라 하고 그 메모리는 working set으로 간주
- working set의 크기는 그때끄때 바뀐다.
- 참조된 후 delta 시간동안 해당 page를 메모리에 유지한 후 버린다.

## PFF(Page-Fault Frequency) Scheme

![https://user-images.githubusercontent.com/76726411/148779992-563fe8a9-ca74-472e-99ed-670af425a739.png](https://user-images.githubusercontent.com/76726411/148779992-563fe8a9-ca74-472e-99ed-670af425a739.png)

page-fault rate의 상한값과 하한값을 둔다.

- page fault rate이 상한값을 넘으면 frame을 더 할당함
- page fault rate이 하한값 이하이면 할당 frame 수를 줄임

빈 frame이 없으면 일부 프로세스를 swap out한다.

## Page Size의 결정

### page size를 감소시키면?

- page 수 증가
- page table 크기 증가
- internal fragmentation 감소
- disk transfer의 효율성 감소
    - disk가 찾는 시간(seek)이 늘어난다.
    - • Seek 시간이 오래걸리는 작업이므로 한번에 많은 내용을 가져오는 것이 좋다.
- 필요한 정보만 메모리에 올라와 메모리 이용이 효율적
    - locality의 활용 측면에서는 좋지 않다.

### Trend

- larger page size