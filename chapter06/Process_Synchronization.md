# Process Synchronization

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

## Race Condition (경쟁 상태)

- Storage Box를 공유하는 Execution Box가 여럿 있는 경우 race condition 발생 가능성이 있다.
- 동시에 같은 곳을 접근해서 생기는 문제
- 여러 프로세스들이 동시에 데이터에 접근하는 상황에서, 어떤 순서로 데이터에 접근하느냐에 따라 결과 값이 달라질 수 있는 상황
- 공유데이터(Shared data)의 동시 접근(Concurrent access)은 데이터의 불일치 문제(Inconsistency)를 발생시킴 → 일관성(Consistency) 유지를 위해서는 협력 프로세스(Cooperating Process) 간의 실행 순서(orderly execution)를 정해주는 메커니즘인 동기화 (Synchronization)이 필요
- 발생하는 3가지 경우
    1. kernel 모드로 수행 중 인터럽트 발생 시
    2. 프로세스가 system call을 하여 kernel mode로 수행 중인데 context switch가 일어나는 경우
    3. Multiprocessor 에서 shared memory내의 kernel data에 접근하는 경우

## Critical Section

- 코드 상에서 공유 데이터에 접근하여 Race condition이 발생할 수 있는 부분
- Critical Section으로 인해 발생하는 문제들의 해결법의 충족 조건 3가지
    1. Mutual Exclusion (상호배제) : 이미 한 프로세스가 Critical Section에서 작업 중이면 다른 모든 프로세스는 Critical Section에 진입 불가
    2. Progress (진행) : Critical Section에서 작업 중인 프로세스가 없다면, Critical Section에 진입하고자 하는 프로세스가 존재하는 경우 진입 가능
    3. Bounding Waiting (한정 대기) : 프로세스가 Critical Section에 들어가기 위해 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스들이 Critical Section에 들어가는 횟수에 한계가 있어야 함.
    

## Synchronization Algorithms

- Critical Section Problem 해결 방법 3가지

### Algorithm1

### Algorithm2

### Algorithm3 (Peterson’s Algorithm)

- turn 변수, flag 변수 모두 사용
- Mutual Exclusion, Progress, Bounded Waiting 모두 만족
- Critical Section 진입을 기다리면서 계속 CPU와 메모리를 사용하는 Busy Waiting 의 문제

## Synchronization Hardware

- Critical Section Problem을 해결하는 하드웨어적 방법
- 하드웨어적으로 현재 상태를 확인하고 변경하는 것을 **atomic** 하게 수행할 수 있도록 지원
- 현대 기계들은 특별한 atomic hardware instruction을 제공한다. (Test_and_Set)
- bounded waiting 조건을 만족하지 못하는 단점이 있음

## Mutex Locks

- Critical Section Problem을 해결하는 소프트웨어적 방법
- lock이 하나만 존재할 수 있는 locking 메커니즘 : 이미 어떤 프로세스가 Critical Section에서 작업 중이라면 다른 프로세스는 Critical Section에 진입 불가
- Busy Waiting의 단점이 있다.

**Spin Lock**

- lock이 반환될 때까지 계속 확인하면서 프로세스가 기다리는 것
- Context Switching 비용보다 Critical Section 진입 대기를 기다리는 시간이 짧은 경우를 위해 고안

## Semaphores

- 추상자료형 (기능의 구현 부분을 나타내지 않고 순수한 기능이 무엇인지 나열한 것)
- semaphore는 여러 프로세스가 하나의 공유자원에 접근하는 것을 제어하는 역
- semaphore는 정수값을 가지며 이 정수값에 P(S)와 V(S), 2가지 연산을 할 수 있다.
    
    **1. P(S) : semaphore를 획득(=공유자원에 대한 접근권한을 얻음)**
    
    **2. V(S) : semaphore를 반납(공유자원을 사용하고 접근권한을 반납)**
    
- semaphore는 2가지 타입이 있다.
    
    1. counting semaphore
    
    도메인이 0 이상인 임의의 정수값으로 주로 자원에 접근할 수 있는 프로세스의 수를 제어(resource counting)하는데 사용
    
    2. binary semaphore(= mutex)
    
    0또는 1 값만 가질 수 있는 semaphore로 주로 mutual exclusion(lock / unlcok)에 사용
    

## semaphore를 Block / WakeUp으로 구현

- **busy waiting(spin lock)** : semaphore의 P(S)연산의 경우, 만약 공유자원에 대한 접근을 얻지 못하면 해당 프로세스는 while()에서 semaphore를 얻을 때까지 계속 유한대기를 하기 때문에 cpu를 낭비. 해당 프로세스가 cpu를 받아도 그냥 while()에서 대기할 뿐...
    
    → 해당 프로세스를 blocked시키고 다른 프로세스가 semaphore를 내놓을 때 그때다시 깨워주는 방법(cpu ready queue에 삽입)이 더 효율적 (block/wakeup)
    

![https://blog.kakaocdn.net/dn/dO5sSS/btqDF09MMka/rMWi1kbc1WYn2j5NBOWGKk/img.png](https://blog.kakaocdn.net/dn/dO5sSS/btqDF09MMka/rMWi1kbc1WYn2j5NBOWGKk/img.png)

- block/wakeup방식으로 semaphore를 구현할 때 P연산과 V연산
    
    1. P(S)
    
    ![https://blog.kakaocdn.net/dn/NeL5v/btqDGgkcq9P/aj3HSQbOEpRJvdTn9zeUP1/img.png](https://blog.kakaocdn.net/dn/NeL5v/btqDGgkcq9P/aj3HSQbOEpRJvdTn9zeUP1/img.png)
    
    P(S)연산의 경우 semaphore를 하나 가져가기 때문에 S.value를 하나 빼줌. 만약 음수인 경우 해당 프로세스를 semaphore를 기다리는 wait queue에 넣고 block()시킴. 이렇게 되면 해당 프로세스가 cpu를 받을 일이 없게 되어 busy waiting이 해결!
    
    2. V(S)
    
    ![https://blog.kakaocdn.net/dn/bGQYTd/btqDEGxlPjp/cypJA5PYtS3EwEuR4LCeR0/img.png](https://blog.kakaocdn.net/dn/bGQYTd/btqDEGxlPjp/cypJA5PYtS3EwEuR4LCeR0/img.png)
    
    자원을 반납할 때는 S.value를 하나 증가. 만약 하나 증가시켰는데도 그 값이 0 이하라는 것은 내 semaphore를 내놓기를 기다리고 있는 프로세스가 있다는 의미(block되어 있음). 그렇기 때문에 wait queue에 잠들어 있는 프로세스를 깨워줌.
    

## **동기화와 관련된 고전적인 문제**

1. **Producer - Consumer Problem(Bounded - Buffer Problem)**

![https://blog.kakaocdn.net/dn/bibdoj/btqDF0IAqgU/wJi7apPY379MBth2Fnizkk/img.png](https://blog.kakaocdn.net/dn/bibdoj/btqDF0IAqgU/wJi7apPY379MBth2Fnizkk/img.png)

- buffer의 hole에 데이터를 채우는 것은 생산자(producer),  데이터를 가져가는 것은 소비자(consumer). 생산자와 소비자는 여러명이 있으며 이들 사이에는 동기화가 필요.
- 둘 이상이 소비자/생산자가 데이터에 동시에 접근하는 것을 방지하기 위해 데이터 접근에 대한 lock/unlcok이 필요함
- 버퍼가 가득 차거나/비었을 때 소비자/생산자가 확인할 수 있도록 가용 자원을 확인할 수 있도록 counting semaphore가 필요함

![https://blog.kakaocdn.net/dn/Su3Dg/btqDGgYSTic/tCrNfMpgaFdU56AIAicqT1/img.png](https://blog.kakaocdn.net/dn/Su3Dg/btqDGgYSTic/tCrNfMpgaFdU56AIAicqT1/img.png)

1. full : 버퍼에 내용이 들어있는 공간의 개수
2. empty : 버퍼에 비어있는 공간의 개수
3. mutex : lock을 걸기 위한 변수
- Producer : buffer에 데이터를 넣음
    
    먼저 P(empty)로 버퍼가 비어있는지 검사. 
    
    만약 비어있지 않다면 Consumer가 소비할 때까지 기다림. 비어있다면 빈 버퍼를 획득하고 버퍼에 lock을 걸고(P(mutex)) 데이터를 넣음. 그리고 unlcok(V(mutext))을 한 후 full의 개수를 증가(V(full)).
    
- Consumer : buffer의 데이터를 가져감
    
    먼저 내용이 들어있는 버퍼가 있다면(P(full)) 버퍼에 lock을 검(P(mutex)). 이후 버퍼의 데이터를 하나 가져간 후 unlcok(V(mutex)). 그리고 비어있는 버퍼의 개수를 하나 증가시킴(V(empty))
    

2. **Readers and Writers Problem**

readr/write하는 작업의 대상(공유 데이터)을 db라 가정.

(해결해야 할 부분) write는 DB를 read하는 프로세스가 하나도 없을 때 할 수 있으며, read는 여러 프로세스가 할 수 있으며 write할 때는 read할 수 없음.

![https://blog.kakaocdn.net/dn/GEqQA/btqDGgLuaoO/UcT3jjKi3qI8PAaFiQgxKK/img.png](https://blog.kakaocdn.net/dn/GEqQA/btqDGgLuaoO/UcT3jjKi3qI8PAaFiQgxKK/img.png)

1. DB : 접근하려는 공유 데이터
2. db : DB에 대한 semaphore 변수
3. readcount : DB의 데이터를 read하는 프로세스의 수
4. mutex : readcount에 대해 lock을 걸기위한 변수
5. P(mutext), V(mutex) : reactcount 접근에 대한 lock/unlock 연산
6. P(db), V(db) : DB 접근에 대한 lock/unlcok 연산
- Writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 reader들을 다 DB에 접근가능. Writer는 대기 중인 Reader가 하나도 없을 때 DB 접근이 허용. 반면 Writer가 DB에 접근 중이면 Reader들은 접근이 금지. Writer가 DB에서 빠져나가야지만 Reader의 접근이 허용.
- Starvation 문제 발생 → reader가 lock을 걸어논 상태에서 reader가 계속 db에 접근한다면 writer가 지나치게 오래 기다리는 현상 발생
    
    해결 방법: 큐에다가 우선수위를 부여하여 writer가 일정 수준 기다리지 않게 함
    

3. **Dining - Philosophers Problem**

![https://blog.kakaocdn.net/dn/Ryb4C/btqDGgLwLop/Fil4VRWQaZYW1itABDIlB0/img.png](https://blog.kakaocdn.net/dn/Ryb4C/btqDGgLwLop/Fil4VRWQaZYW1itABDIlB0/img.png)

철학자들의 만찬 문제 : 각 자리가 있고 자리왼쪽/오른쪽에 포크가 하나씩 있음. 식사를 하기 위해서는 왼쪽/오른쪽 포크를 모두 사용하여 식사를 해야 하며 철학자들이 할 수 있는 일은 식사를 하거나 생각을 하거나 둘 중 하나. 여기서 3번째 철학자가 식사를 하면 2,3번째 철학자는 식사를 하지 못하게 됨. 어떻게 하면 철학자들이 공평하게 식사를 할 수 있을지에 대한 문제.

![https://blog.kakaocdn.net/dn/cdvDRa/btqDIRX67W8/yhj8O81j9sKujkmgUUQja1/img.png](https://blog.kakaocdn.net/dn/cdvDRa/btqDIRX67W8/yhj8O81j9sKujkmgUUQja1/img.png)

- 만약 5명의 철학자가 동시에 배가 고파져서 왼쪽 젓가락을 집으면 오른쪽 젓가락은 영원히 잡을 수 없는 상황이 된다. 그 누구도 식사 못함 → Deadlock
- 해결 방법 3가지
1. 4명의 철학자만이 이 테이블에 동시에 앉을 수 있도록 한다.
2. 젓가락을 두 개 모두 잡을 수 있을 때에만 젓가락을 잡을 수 있게 한다
3. 짝수(홀수) 철학자는 왼쪽(오른쪽) 젓가락 부터 집도록 한다.

## **Monitor**

- why Monitor? semaphore를 정확하게 사용하면 문제가 되지 않지만 프로그래머의 실수로 시스템에 치명적인 영향을 끼칠 수 있음. 예를 들어 P(S), V(S)를 잘못 사용하는 경우 상호배제가 깨지거나 Deadlock문제가 발생할 수 있음. 정확성 입증이 어려움..그래서 나온게 Monitor
- monitor는 프로세스가 공유데이터에 접근하기 위해서는 monitor 내부의 어떠한 절차를 통해서만 공유 데이터에 접근할 수 있게 함.
- 모니터의 경우 기본적으로 여러 프로세스가 모니터에 대한 동시접근을 제한. 그렇기 때문에 한 순간에 하나의 프로세스만 모니터에 접근하여 공유데이터를 사용할 수 있음. 다른 프로세스가 접근하면 queue에서 기다리게 됨. 따라서 semaphore처럼 lock/unlock을 해 줄 필요 nooooo. 모니터가 알아서 제어하기 때문.
    
    ex) 만약 A프로세스가 모니터에 있는 도중에 cpu시간이 끝나게 되어  B프로세스가 모니터에 접근하려는 경우 : A프로세스가 이미 monitor에 활성화된 상태로 존재하기 때문에 B프로세스는 monitor에 들어가지 못하고 queue에서 대기.
    
- monitor에서는 condition variable를 사용. 이것의 역할을 만약 프로세스가 monitor에서 어떤 일을 하는데 조건이 충족되지 않아서 잠시 잠들게 해야하는데 어떤 조건이 충족되지 않았느냐에 따라 그 조건에 해당하는 것을 변수로 설정할 수 있습니다.
- condition variable의 2 연산
    
    1. wait()
    
    2. signal() 
    

**Producer - Consumer Problem(Bounded - Buffer Problem)문제를 monitor로 해결하는 경우**

![https://blog.kakaocdn.net/dn/dopRts/btqDGH3oOdv/IyKJZluToqcQxvEnoSGCbK/img.png](https://blog.kakaocdn.net/dn/dopRts/btqDGH3oOdv/IyKJZluToqcQxvEnoSGCbK/img.png)

- full : 내용이 들어있는 버퍼를 기다리는 프로세스를 줄세움
- empty : 빈 버퍼를 기다리는 프로세스를 줄세움
- produce
    
    만약 버퍼가 모두 차서 데이터를 넣을 빈 버퍼가 없다면 빈 버퍼를 기다리는 줄에가서 잠들게 함.(empty.wait())
    
    그렇지 않고 빈버퍼가 있다면 버퍼에 데이터를 넣고 내용이 들어있는 버퍼를 기다리면서 잠들어 있는 프로세스를 깨움(full.signal())
    
- consume
    
    만약 내용이 들어있는 버퍼가 없다면 내용이 들어 있는 버퍼를 기다리는 줄에 가서 잠들게 함.(full.wait())
    
    그렇지 않고 내용이 들어있는 버퍼가 있다면 버퍼로부터 아이템을 가져오고 빈 버퍼를 기다리는 프로세스를 깨움(empty.signal())
    

**Dining Philosophers Problem을 monitor로 해결하는 경우**

![https://media.vlpt.us/images/ryujm/post/63763531-9233-46cd-a658-e052fa64b228/6-14_5.png](https://media.vlpt.us/images/ryujm/post/63763531-9233-46cd-a658-e052fa64b228/6-14_5.png)

### Semaphore와 Monitor의 차이

- semaphore : 자원을 획득하기 위해서 프로그래머가 알아서 P연산, V연산을 해줘야 함.
- monitor : 동시 접근을 막는 것을 모니터 차원에서 지원해줌