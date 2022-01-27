# Process Synchronization
<br>

## 컴퓨터 시스템 내에서의 데이터 접근

1. Storage-Box에 저장된 data
2. 연산할 data를 Execution-Box가 load
3. Execution-Box에서 연산
4. 연산 결과를 Storage-Box에 저장

| Execution Box | Storage Box |
| --- | --- |
| CPU | Memory |
| 컴퓨터내부 | 하드디스크 |
| 프로세스 | 그 프로세스의 주소 공간 |

<br>

## Race Condition (경쟁 상태)

- Storage Box를 공유하는 Execution Box가 여럿 있는 경우 race condition 발생 가능성이 있다.
- 동시에 같은 곳을 접근해서 생기는 문제
- 여러 프로세스들이 동시에 데이터에 접근하는 상황에서, 어떤 순서로 데이터에 접근하느냐에 따라 결과 값이 달라질 수 있는 상황
- 공유데이터(Shared data)의 동시 접근(Concurrent access)은 데이터의 불일치 문제(Inconsistency)를 발생시킴 → 일관성(Consistency) 유지를 위해서는 협력 프로세스(Cooperating Process) 간의 실행 순서(orderly execution)를 정해주는 메커니즘인 동기화 (Synchronization)이 필요
- 발생하는 3가지 경우
    1. kernel 모드로 수행 중 인터럽트 발생 시
    2. 프로세스가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
    3. Multiprocessor 에서 shared memory내의 kernel data에 접근하는 경우

<br>

## Critical Section

- 코드 상에서 공유 데이터에 접근하여 Race condition이 발생할 수 있는 부분
- Critical Section으로 인해 발생하는 문제들의 해결법의 충족 조건 3가지
    1. Mutual Exclusion (상호배제) : 이미 한 프로세스가 Critical Section에서 작업 중이면 다른 모든 프로세스는 Critical Section에 진입 불가
    2. Progress (진행) : Critical Section에서 작업 중인 프로세스가 없다면, Critical Section에 진입하고자 하는 프로세스가 존재하는 경우 진입 가능
    3. Bounding Waiting (한정 대기) : 프로세스가 Critical Section에 들어가기 위해 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 Critical Section에 들어가는 횟수에 한계가 있어야 함.
    
<br>

## Synchronization Algorithms

- Critical Section Problem 해결 방법 3가지

### Algorithm1

### Algorithm2

### Algorithm3 (Peterson’s Algorithm)

- turn 변수, flag 변수 모두 사용
- Mutual Exclusion, Progress, Bounded Waiting 모두 만족
- Critical Section 진입을 기다리면서 계속 CPU와 메모리를 사용하는 Busy Waiting 의 문제
<br>

## Synchronization Hardware

- Critical Section Problem을 해결하는 하드웨어적 방법
- 하드웨어적으로 현재 상태를 확인하고 변경하는 것을 **atomic** 하게 수행할 수 있도록 지원
- 현대 기계들은 특별한 atomic hardware instruction을 제공한다. (Test_and_Set)
- bounded waiting 조건을 만족하지 못하는 단점이 있음

<br>

## Mutex Locks

- Critical Section Problem을 해결하는 소프트웨어적 방법
- lock이 하나만 존재할 수 있는 locking 메커니즘 : 이미 어떤 프로세스가 Critical Section에서 작업 중이라면 다른 프로세스는 Critical Section에 진입 불가
- Busy Waiting의 단점이 있다.

**Spin Lock**

- lock이 반환될 때까지 계속 확인하면서 프로세스가 기다리는 것
- Context Switching 비용보다 Critical Section 진입 대기를 기다리는 시간이 짧은 경우를 위해 고안

<br>

## Semaphores

- 추상자료형

<br>

## Semaphore의 문제점

- DeadLock
- Starvation