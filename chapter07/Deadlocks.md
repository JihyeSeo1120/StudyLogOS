# Deadlocks

## Deadlocks (교착상태)

- Deadlock은 일련의 프로세스가 자신의 자원을 점유하면서 동시에 다른 프로세스가 점유한 자원을 기다리면서 block된 상태를 의미
- 자원은 I/O device, CPU cycle, momory space, semaphore 등의 hw/sw 자원을 모두 포함.
- 프로세스가 자원을 사용하는 절차(4단계)
    - Request(자원 요청), Allocate(자원 획득), Use(사용), Release(반납)

---

## Deadlock의 발생조건

> 4가지 조건을 모두 만족해야 Deadlock 발생


1. Mutual Exclusion

상호배제 : 매 순간 하나의 프로세스만이 자원을 사용할 수 있음

2. No Preemption

비선점 : 프로세스는 자원을 스스로 내어놓을 뿐 강제로 빼앗기지 않음

3. Hold and wait

보유대기 : 자원을 가진 프로세스가 다른 자원을 기다릴 때 보유한 자원을 놓지 않고 계속 가지고 있음

4. Circular wait

순환대기 : 자원을 기다리는 프로세스 간에 사이클이 형성되어야 함

P1,P2,P3,P4프로세스가 있을 때 P1 → P2 → P3 → P4 → P1

---

## Resource - Allocation Graph

- 프로세스와 그 프로세스가 사용하는 자원간의 관계를 도식화한 것을 Resource - Allocation Graph
- 이 그래프의 사이클 유무에 따라 현재 상태에서 Deadlock의 유무를 확인할 수 있음.
- 원 : 프로세스
- 사각형 : 자원
    - 사각형 안에 있는 점: 자원의 instance
- 화살표
    - 자원 → 프로세스 : 프로세스가 이 자원을 가지고 있다
    - 프로세스 →자원 : 프로세스가 이 자원을 요청함, 요청을 했는데 아직 획득하지는 못한 상태

1. 그래프에 사이클이 없으면 Deadlock이 아님.

![https://blog.kakaocdn.net/dn/emf5RP/btqDHr0nQEu/QHJuhWGKuKKsNp9uaeDJHk/img.png](https://blog.kakaocdn.net/dn/emf5RP/btqDHr0nQEu/QHJuhWGKuKKsNp9uaeDJHk/img.png)

2. 그래프에 cycle이 있으면.....경우 따지기

![https://blog.kakaocdn.net/dn/pps93/btqDImxt61b/jDI3KwhgYrBKV3UrFwwFR0/img.png](https://blog.kakaocdn.net/dn/pps93/btqDImxt61b/jDI3KwhgYrBKV3UrFwwFR0/img.png)

- 만약 각 자원에 instance(사진에서 네모 박스의 검은 점)가 하나만 있다면 무조건 deadlock.
- 그렇지 않고 각 자원에 여러개의 instance가 있다면 deadlock 가능성이 있음.
- 오른쪽의 경우 사이클이 만들어졌지만 R2자원을 가진 P4가 작업을 마무리 하면 R2의 자원을 내놓으므로 사이클이 만들어 졌지만 Deadlock nooo0
- 왼쪽의 경우 Deadlock인 상황

---

## Deadlock의 처리 방법

### 1. Deadlock Prevention

자원 할당 시 Deadlock의 4가지 필요조건 중 하나를 차단하여 Dealock에 들어가지 못하도록 하는 방법 (미연에 방지)

- Mutual Exclusion

자원을 배타적으로 사용하는 것은 막을 수 있는 조건이 아님

- Hold and Wait

프로세스가 자원을 요청할 때 다른 어떤 자원도 가지고 있지 않아야함

방법 1 : 프로세스 시작 시 모든 필요한 자원을 할당 받게 하는 방법

방법 2 : 자원이 필요한 경우 보유 자원을 모두 놓고 다시 요청

- No Preemption

process가 어떤 자원을 기다려야 하는 경우 이미 보유한 자원이 선점됨

모든 필요한 자원을 얻을 수 있을 때 그 프로세스는 다시 시작됨

state를 쉽게 save하고 restore할 수 있는 자원에서 주로 사용(CPU, memory)

- Circular wait

모든 자원 유헝에 할당 순서를 정하여 정해진 순서대로만 자원 할당

예를 들어 순서가 3인 자원 R1을 보유 중인 프로세스가 순서가 1인 자원 R1을 할당받기 위해서는 우선 R1를 반납해야 함

→ Deadlock을 원천적으로 막을 순 있지만 utilization 저하, throughput 감소, starvation 문제...매우 비효율

### 2. Deadlock Avoidance

자원 요청에 대한 부가적인 정보를 이용해서 Deadlock의 가능성이 없는 경우에만 자원을 할당 하거나 시스템 state가 원래 state로 돌아올 수 있는 경우에만 할당

- 자원 요청에 대한 부가정보를 이용해서 자원 할당이 Deadlock으로 부터 안전(safe)한지를 동적으로 조사해서 안전한 경우에만 할당
- 가장 단순하고 일반적인 모델은 프로세스들이 필요로 하는 각 자원별 최대 사용량을 미리 선언하도록 하는 방법
- 각 자원에 하나의 instance만 있는 경우 : Resource Allocation Graph algorithm을 사용
- 각 자원에 두개 이상의 instance가 있는 경우 : Banker's algorithm을 사용

### 3. Deadlock Detection and Recover

Deadlock 발생은 허용하되 그에 대한 detection 루틴을 두어 Deadlock 발견시 recover, 여기서 Deadlock의 존재 여부는 Resource - Allocation Graph를 통해 알 수 있음.

Deadlock 발생시 recovery 2가지 방법

- process termination : 연루된 모든 process를 죽이거나, Deadlock이 없어질 때 까지 하니씩 죽임
- resource preemption : 비용이 가장 적은 process를 선정해 자원을 회수

### 4. Deadlock ignorance

Deadlock이 일어나지 않는다고 생각하고 아무런 조치를 하지 않으며 Deadlock이 매우 드물게 발생하므로 Deadlock의 조치에 자체가 더 큰 overhead일 수 있음. UNIX를 포함한 대부분의 OS가 사용하며 사람이 프로세스가 문제 생겼을 때 프로세스를 죽이든 알아서 처리

---

## **Resource Allocation Graph algorithm | Banker's algorithm**

- Deadlock 회피(avoidance)는 프로세스가 시작할 때 본인이 평생 사용할 자원들을 미리 declare하고 그걸 이용해서 Deadlock이 발생할 수 있는 최악의 상황, 즉 현재 상황에서 만약에 어떤 프로세스가 어떤 자원에 대한 요청을 하였을 때 생길 수 있는 1%의 경우를 피해가는 방법.
- 각 자원에 instance가 하나만 있는 경우 Resource Allocation Graph algorithm,
- 각 자원에 instance가 두개 이상있는 경우 Banker's algorithm을 사용합니다.

### 1. Resource Allocation Graph algorithm

![https://blog.kakaocdn.net/dn/dauzJS/btqDISjxnQa/t9DDeTH0HFhuoYBOsmKZ41/img.png](https://blog.kakaocdn.net/dn/dauzJS/btqDISjxnQa/t9DDeTH0HFhuoYBOsmKZ41/img.png)

- 자원 → 프로세스(실선) : 해당 자원이 프로세스에게 할당됨
- 프로세스 → 자원(실선) : 프로세스가 해당 자원을 요청했지만 아직 할당받지 못함
- 프로세스 → 자원(점선) : 프로세스가 평생에 적어도 한번은(언젠간) 해당 자원을 사용할 일이 있음

### 2. Banker's algorithm

Banker's algorithm은 항상 최악의 경우를 생각하는 굉장히 보수적인 방법으로 Deadlock을 회피. 프로세스의 할당된 자원과 한번의 요청에 최대로 필요한 자원을 계산하고 프로세스가 자원을 요청했을 때 최대로 필요한 자원을 고려(최악의 상황)하여 만약 남은 가용자원으로 그 조건을 만족할 수 있으면 자원을 할당하고 그렇지 않으면 자원을 할당하지 않음.

![https://blog.kakaocdn.net/dn/bzxmCX/btqDHsMDQvs/Y7t4t6CSxCQFgLvOAFehNk/img.png](https://blog.kakaocdn.net/dn/bzxmCX/btqDHsMDQvs/Y7t4t6CSxCQFgLvOAFehNk/img.png)

P0, P1, P2, P3, P4프로세스와 A,B,C자원이 있으며 각각의 instance 수는 (10,5,7).

- Allocation : 프로세스에게 할당된 자원
- Max : 프로세스가 한번에 필요로하는 최대 자원
- Available : 각 프로세스에게 할당되고 남은 자원, 시스템 내의 가용자원
- Need : 현재 할당된 자원에서 Max를 만족하는데 추가적으로 필요한 자원