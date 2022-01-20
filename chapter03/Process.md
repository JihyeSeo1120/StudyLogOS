# **Process**

## Process 란

- 실행 중인 프로그램

## Process의 문맥 (context)

- 프로세스의 현재 상태 (ex. 어디까지 진행했는가..) 를 나타내기 위한 모든 요소
- 왜 중요할까?
    - CPU는 하나의 프로세스가 완료될 때까지 실행하는 것이 아니라 아주 짧고 시간동안 하나의 프로세스를 실행하므로 (Time Sharing..) CPU를 다시 점유한 프로세스가 처음부터 다시 실행되는 것이 아니라 완료된 부분 이후부터 실행이 되려면 프로세스의 문맥을 알고 있어야 한다.

### Process의 문맥 구성

1. CPU 수행 상태를 나타내는 하드웨어적 문맥
    - Program Counter
    - 각종 register
    - 어떤 프로세스가 현 시점에 instruction을 어디까지 수행했는가를 알기 위해선 register에 현재 저장된 값, program counter가 어디를 가르키고 있는지 알아야한다.

2. 프로세스의 주소 공간
    - code, data, stack
    - 현시점에 process의 code, data, stack에 무엇이 들어있는가

3. 프로세스 관련 커널 자료구조
    - PCB(Process Control Block)
    - Kernel Stack
        - 어떤 프로세스가 System cal 했는지 알기 위해 커널 stack은 프로세스마다 별도로 둠
    - OS는 프로세스를 관리한다. 프로세스 관리를 위해 OS는 PCB라는 자료구조를 프로세스마다 하나씩 두고있다.

## 프로세스의 상태 (Process State)

> 프로세스는 상태(state)가 변경되며 수행된다.
> 

*가정 : 컴퓨터 안에 CPU는 단일이라 CPU를 점유하는 프로세스는 CPU당 하나이다.*

1. Running : CPU를 잡고 instruction이 수행 중인 상태
2. Ready : CPU를 기다리는 상태 (메모리 등 다른 조건을 만족하는 경우)
3. Blocked(wait/sleep) : CPU를 주어도 당장 instruction 수행 불가 상태
    1. Process 자신이 요청한 event(ex. I/O) 가 즉시 만족되지 않아 이를 기다리는 상태
    2. 디스크에서 file를 읽어와야 하는 경우
4. [Suspended](#Suspended-상태)
5. New : 프로세스가 생성 중인 상태
6. Terminated: 수행이 끝난 상태 (정리 상태)

## Process Control Block (PCB)

- OS가 각 프로세스를 관리하기 위해 프로세스당 유지하는 정보
- 1 Process 당 1PCB in Kernel data block

### PCB 구성요소 4가지 (구조체 형태)

1. OS가 관리상 사용하는 정보 : Process State, ProcessID, Scheduling Information, Priority
2. CPU 수행 관련 하드웨어 값 : Program Counter, Registers
3. 메모리 관련 : Code, Data, Stack의 메모르 위치 정보

## 문맥 교환 (Context Switch)

- cpu를 한 프로세스에서 다른 프로세스로 넘겨주는 과정
- cpu가 다른 프로세스로 넘아갈 때 (Process A → Process B)
    - A 의 상태를 OS Kerneldml data block에 있는 A의 PCB에 저장
    - CPU B의 상태를 B의 PCB로부터 읽어옴
- 문맥 교환은 프로세스 간의 교체이기 때문에 System Call이나 Interrupt 발생시 context switch 가 무조건 일어나는 것은 아니다.

## 프로세스 스케줄링하기 위한 큐

- Job Queue : 현재 시스템 내에 있는 모든 프로세스의 집합
- Ready Queue
- Device Queue

## 스케쥴러 (Schedular)

1. Short-Term Schedular
    - 어떤 프로세스를 다음에 running 시킬지 결정 → 프로세스에 cpu를 주는 문제
    - 충분히 짧아야 (millisecond 단위) → short-term
2. Long-Term Schedular
    - new 프로세스 중 어떤 것을 Ready 큐로 보낼지 결정(admitted) → 프로세스에 메모리를 주는 문제 (degree og Multiprogramming by 메모리에 몇개를 올려놓을지..)
    - 현재 (time sharing)에서는 장기 스케줄러 없음 (무조건 ready 상태)
3. **Medium-Term Schedular (Swapper)**
    - 처음에 무조건 메모리를 줌 → 필요없는 프로세스는 통째로 메모리에서 디스크로 out
    - 프로세스에게서 메모리를 뺏는 문제 → degree of Multiprogramming 제어
    

## Suspended 상태 
- (Stopped)
- 외부적인 이유로 프로세스의 수행이 정지된 상태
- Medium-Term Schedular에 의해 프로세스가 메모리를 모두 빼았기고 disk로 swap out
- ex) 사용자가 프로그램을 일시 정지시킨 경우 / 시스템이 여러 이유로 프로세스를 잠시 중단

| Blocked | Suspended |
| --- | --- |
| process가 요청한 event가 만족되면 ready | 외부에서 resume해줘야 Active |

---

# Thread

## Thread 란

- lightweight process
- Process 하나에 cpu 수행 단위를 여러개 둔 것

## Thread의 구성

- program counter, register set, stack space
    - 쓰레드마다 별도로 갖는 부분
    - cpu 수행과 관련된 부분만 thread 마다 별도로 갖는다
- code, data, OS resources
    - 동료 쓰레드와 공유하는 부분
    - task
    

## Thread의 장점

1. responsiveness
2. resource sharing : 프로세스의 code, data , resources 공유
3. economy
4. Multiprocessor 아키택쳐에서 병럴처리가 가능

## Thread의 구현

1. Kernel Threads
    - kernel의 지원
    - 스레드가 여러 개 있다는 사실을 운영체제 커널이 알고 있음
    - 하나의 스레드에서 다른 스레드로 넘어가는 것도 커널이 CPU 스케줄링 하듯 동작
2. User Threads
    - library를 통해서 지원
    - 프로세스 안에 스레드가 여러 개 있다는 사실을 운영체제가 알지 못함
    - 사용자 프로그램이 여러 개의 스레드를 관리, 구현