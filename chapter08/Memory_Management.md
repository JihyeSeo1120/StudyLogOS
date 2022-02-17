# Memory Management

## 메모리 주소

### 1. Logical address(= Virtual address)

각 프로세스마다 독립적으로 가지는 주소공간으로 주소가 0부터 시작하여 CPU가 보는 주소는 logical address

### 2. Physical adddress

물리 메모리에 실제 올라가는 위치

✳︎ symbolic address : 코드 단에서 설정하는 address

---

## 주소 바인딩

- virtual address에서 physical address로 변환하는 과정을 주소 바인딩이라고 한다.
- Memory Management Unit (MMU)에서 두 개의 register를 사용해 주소 바인딩을 수행한다. MMU는 실질적으로 주소를 변환하는 relocation register와 요청된 주소와 program의 최대 주소를 비교해 다른 프로세스의 memory로 접근하는지 확인하는 limit register 로 구성되어 있다.

![https://blog.kakaocdn.net/dn/bedbYM/btqDJgknIRS/ESumX4qhUatVxidsRdZYH0/img.png](https://blog.kakaocdn.net/dn/bedbYM/btqDJgknIRS/ESumX4qhUatVxidsRdZYH0/img.png)

- 주소 바인딩은 프로그램이 컴파일될 때 수행되며, 특성에 따라 3가지로 분류된다.
    
    ### 1. Compile time binding
    
    physical address가 컴파일 시 결정되며 시작 위치 변경 시 컴파일 과정을 다시 거침. 과거 시스템내에 하나의 프로그램을 실행할 때 사용되었으며 최근의 시스템에서는 사용하지 않음
    
    ### 2. Load time binding
    
    소스코드가 컴파일되어 logical address만 결정되고 메모리에 올라갈 때 physical address가 결정되며, loader의 책임하에 physical address를 부여하여 컴파일러가 재배치가능코드를 생성한 경우 가능
    
    ### 3. Execution time binding( =Run time binding)
    
    Load time binding 처럼 physical address가 프로그램 실행시에 결정되며, 차이점은 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음, cpu가 주소를 참조할 때마다 binding을 점검(address mapping table)하며 하드웨어적인 지원이 필요(base and limit register, MMU)
    
    !!! Compile time binding, Load time binding은 physical address가 고정되어 있지만 execution time binding은 실행시에 address가 계속 바뀔 수 있음
    

---

일반적으로 중기 스케줄러에 의해 swap out 시킬 프로세스를 선정하며 우선순위가 낮은 프로세스를 swapped out시키고 우선순위가 높은 프로세스를 메모리에 올려 놓음. compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in해야 하며 Execution time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음

---

## Allocation of Physical Memory

- OS 상주영역 : interrupt vector와 함께 낮은 주소 영역 사용
- 사용자 프로세스 영역 : 높은 주소 영역 사용

---

## 사용자 프로세스 영역의 할당 방법

### 1. contiguous allocation (연속 할당)

각각의 프로세스가 메모리의 연속적인 공간에 적재되도록 하는 것

### *고정 분할 방식*

![https://blog.kakaocdn.net/dn/bEz80e/btqDH2gdZYv/YAZIAQ6uuQGhl30Jdz8co1/img.png](https://blog.kakaocdn.net/dn/bEz80e/btqDH2gdZYv/YAZIAQ6uuQGhl30Jdz8co1/img.png)

- 물리적 메모리를 몇개의 영구적 분할(partition)으로 나눔
- 분할의 크기가 모두 동일한 방식과 서로 다른 방식이 존재
- 분할당 하나의 프로그램 적재
- 융통성이 없음 : 동시에 메모리에 load되는 프로그램 수가 고정, 최대 수행 가능 프로그램 수 또한 제한
- internal fragmentation 발생(external fragmentation도 발생)

### *가변 분할 방식*

![https://blog.kakaocdn.net/dn/vXmHM/btqDKT3byMT/UeT90CkXGGjC2kMrqqM5cK/img.png](https://blog.kakaocdn.net/dn/vXmHM/btqDKT3byMT/UeT90CkXGGjC2kMrqqM5cK/img.png)

- 메모리가 할당되는 순서대로 물리적 메모리에 차곡차곡 할당된다.
- 운영체제는 물리적 메모리 중간중간 비어있는 hole에 대한 정보를 가지고 있고, 다음 프로세스를 할당할 때 참고해야 한다.
- External fragmentation 발생

**Hole**

![https://blog.kakaocdn.net/dn/boh7qp/btqDLDZ95EK/vy5iu3Bkeq7DiU7rO1Hynk/img.png](https://blog.kakaocdn.net/dn/boh7qp/btqDLDZ95EK/vy5iu3Bkeq7DiU7rO1Hynk/img.png)

- 다양한 크기의 hole들이 메모리 여러 곳에 흩어져 있다.
- 프로세스가 도착하면 수용가능한 hole을 할당한다.
- 운영체제는 할당 공간, 가용 공간의 정보를 유지해야한다.

**Compaction**

- external fragmentation 문제를 해결하는 한가지 방법
- 사용 중인 메모리 영역을 한군데로 몰고 hole들을 다른 한 곳으로 몰아 큰 가용공간을 확보한다.
- 매우 비용이 많이 드는 방법으로 최소한의 메모리 이동으로 compaction 하는 방법(매우 복잡)
- compaction은 프로세스의 주소가 실행 시간에 동적으로 재배치 가능한 경우에만 수행 가능하다.

**Dynamic storage allocation problem**

- size n인 요청을 만족하는 가장 적절한 hole을 찾는 문제로, 해결 방안은 first-fit, best-fit, worst-fit 등이 존재한다.
- First - fit : size가 n 이상인 것 중 최초로 찾아지는 hole에 할당
- Best - fit : size가 n 이상인 가장 작은 hole에 할당, hole들의 리스트 크기가 정렬되지 않은 경우 리스트를 탐색
- Worst - fit : 가장 큰 hole에 할당, 모든 리스트를 탐색

→ First - fit과 Best - fit이 Worst - fit보다 속도와 공간 이용률 측면에서 효과적임

### 2. noncontiguous allocation (불연속 할당)

하나의 프로세스가 메모리의 여러 영역에 분산되어 올라갈 수 있음

**paging** : process의 virtual memory를 동일한 사이즈의 page로 나누어 물리 메모리에 할당된다. page table 연산 + 실제 메모리 접근 으로 약 2배의 시간이 소요된다.

- segmentation : process의 virtual memory를 segment단위(code, data...)로 물리 메모리에 올림
- paged segmentation : page와 segmentation을 혼용한 방법

---

## **Paging 기법**

![https://blog.kakaocdn.net/dn/3jpL7/btqDIm6Gm8M/FMX6v5RktKWto3Y8kDam7K/img.png](https://blog.kakaocdn.net/dn/3jpL7/btqDIm6Gm8M/FMX6v5RktKWto3Y8kDam7K/img.png)

![Untitled](https://oowgnoj.dev/static/Paging-465c8d259f2f66eb130d2bf640ccd6f5.png)

process의 virtual memory를 동일한 사이즈(보통 4KB)의 page로 나누어 page 단위로 물리 메모리에 불연속적으로 저장된다. 일부는 backing store, 일부는 physical memory에 저장된다.

physical memory와 virtual memory를 동일한 크기로 나누고 각각 frame, page로 부른다. frame과 page간의 관계는 page table을 사용하여 logical address를 physical address로 변환한다. external fragmentation은 발생하지 않지만 internal fragmentation을 발생할 수 있다.

cpu가보는 logical address는 physical address상의 주소로 변환되어야 하는데 이 변환과정에서 page table을 사용한다. logical address에서 앞부분 p는 페이지 번호, 뒷부분d는 페이지 내의 offset이 된다. page table에서는 페이지 번호를 사용하여 프레임 번호(f)를 찾아서 physical address를 구한다.

---

## Page Table

### page-table

논리적 주소를 물리적 메모리 주소로 변환하는 과정에 사용된다. 모든 프로그램마다 page-table을 가지고 있다. 캐싱 하드웨어인 TLB를 사용한다. page table에서 자주 사용되는 데이터의 일부분만 가지고 있고, parallel search가 가능하다.TLB는 각 페이지의 physical memory address, Valid(v) / Invalid(i) Bit, protection bit를 가지고 있다.

**Valid / Invalid Bit in a Page Table**

![https://blog.kakaocdn.net/dn/beHYz4/btqDLFju9Ji/hmJ8tpnZe7S4MXbVymniqK/img.png](https://blog.kakaocdn.net/dn/beHYz4/btqDLFju9Ji/hmJ8tpnZe7S4MXbVymniqK/img.png)

page table내는 해당 페이지가 물리 메모리(프레임)에 올라가 있는지를 확인할 수 있는 valid/invalid bit가 존재합니다.

- protection bit

page table내는 해당 페이지가 물리 메모리(프레임)에 올라가 있는지를 확인할 수 있는 valid/invalid bit가 존재

- protection bit
    - page의 연산에 대한 접근 권한(read/write/read-only)
    - code의 경우 read-only이지만 data,stack은 연산이 가능하므로 read/write권한을 부여
- valid
    - 해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함
- invalid
    - 해당 주소의 frame에 유효한 내용이 없음을 뜻함, 2가지 경우로 나누어 생각할 수 있음
    
    1) 프로세스가 그 주소 부분을 사용하지 않는 경우
    
    2) 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우
    

### Two(multi)-level page table

page table 자체를 page로 구성해 hard-disk에 저장하고 있다가 필요시 메모리(on demand)로 로드하는 방식을 사용한다. 프로그램이 실행되면 반복적으로 자주 쓰이는 부분이 존재하기에 에러처리 등 가끔 쓰이는 부분등 모든 부분을 물리적 메모리에 올리지 않아 저장공간을 확보할 수 있다.

### Inverted page table

기존 page table의 단점을 보완하는 방식이다. 기존 테이블은 모든 프로세스마다 logical address에 대응하는 모든 page에 대해 page table entry가 존재하는 반면, inverted page table은 system-wide 하게 한 개의 page table이 존재한다. page table entry에는 물리적 메모리의 page frame이 담고 있는 내용을 표시한다. (pid, process의 logical address) 프로세스가 수행될 때, 테이블 전체를 탐색해야 하는 단점이 있지만 associative register를 사용해 병렬 탐색이 가능하다.

### shared page

read-only로 하여 프로세스 간에 하나의 code 만 메모리에 옮긴다. shared code는 모든 프로세스의 logical address space에서 동일한 위치에 있어야 한다

---

## Segmentation 기법

프로그램을 의미 단위인 여러개의 segment로 나누는 방법으로 작게는 함수 하나하나를, 크게는 프로그램 전체를 하나의 세그먼트로 정의하며 일반적으로는 code, data, stack부분이 하나씩의 세그먼트로 정의된다. 의미 단위로 쪼개어 segment table이 가볍고, 공유(sharing)와 보안(protection)에 있어 paging 보다 효과적이다. 하지만 메모리 공간 사용의 측면에서 segment의 크기가 동일하지 않으므로 가변 분할 방식과 같은 문제점이 발생한다.(first fit / best fit, external fragment 발생)

- segment가 되는 logical unit
    - main()
    - function
    - global varialbes
    - symbal table, arrays
- logical address는 <segment-number, offset>으로 구성,변경
- segment table의 구성
    - base : 세그먼트의 시작 물리주소
    - limit : 세그먼트의 길이(paging기법의 경우 크기가 모두 같지만 segment기법의 경우 segment의 길이가 모두 다르기 때문에)
- 주소변환을 위해 제공되던 Cpu 레지스터
    - Segment Table Base Register(STBR) : 물리적 메모리에서의 segment table의 위치
    - Segment Table Length Register(STLR) : 프로그램이 사용하는 segment의 수

code의 경우 read-only이지만 data,stack은 연산이 가능하므로 read/write권한을 부여

- valid

해당 주소의 frame에 그 프로세스를 구성하는 유효한 내용이 있음을 뜻함

- invalid

해당 주소의 frame에 유효한 내용이 없음을 뜻함, 2가지 경우로 나누어 생각할 수 있음

1) 프로세스가 그 주소 부분을 사용하지 않는 경우

2) 해당 페이지가 메모리에 올라와 있지 않고 swap area에 있는 경우

---

## Paged Segmentation

의미 분할, 공유, 보안 등은 segment 단위로, 물리적 메모리에 올라갈 때는 page 단위로 올라간다.

---

## 주소 변환에서 운영체제의 역할

운영체제가 하는 역할은 하나도 없다. 프로세스가 CPU를 가지고 있었을 때 메모리에서 instruction을 실행할 때 주소변환을 사용하는데, 이때마다 OS 로 제어권이 넘어가진 않는다.