# CPU_Scheduling

<br>

## CPU 스케줄링의 필요성

- 프로그램은 **CPU Burst**, **I/O Burst**의 연속이나 프로그램의 종류에 따라서는 빈도나 길이가 다름
- 여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요함
- interactive job에게 적절한 response 제공 요망
- CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

<br>

## 프로세스 특성 분류

- I/O bound process
    - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
    - CPU를 짧게 쓰고 중간에 I/O가 빈번히 끼어드는 작업 → interactive job
    - many / short CPU bursts
- CPU bound process
    - 계산 위주의 job
    - CPU만 오래 쓰는 작업
    - few / very long CPU bursts

<br>

## CPU Scheduler & Dispatcher → 물리적인 것이 아니라 OS에 코드로 존재 (추상화된 기능!)

- CPU Scheduler
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고름
- Dispatcher
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘김 (context switch 과정)

<br>

## CPU 스케줄링이 필요한 경우

1. Running -> Blocked (I/O 요청하는 시스템 콜)
2. Running -> Ready (할당 시간 만료로 timer interrupt)
3. Blocked -> Ready (I/O 완료 후 interrupt)
4. Terminate

> - 1,4 → **nonpreemptive** (자진 반납) : 비선점
> - 나머지 → **preemptive** (강제로 빼앗김) : 선점

<br>

## Scheduling Criteria
- **Cpu 스케줄링 알고리즘의 성능 척도 (Performance Index, Performance Measure)**
- 시스템 입장에서 성능척도
    - Cpu utilization(이용률) : 전체시간 당 cpu가 일한 시간의 비율을 높게
    - Throughput(처리량) : 단위 시간당 처리된 프로세스의 비율을 높게
- 프로그램(고객,프로세스)입장에서 성능척도
    - Turnaround Time (소요시간, 반환시간) : cpu 점유 후 다 끝나고 나갈 때까지 걸리는 시간(cpu burst 걸리는 시간의 합)
    - Waiting Time (대기시간) : ready Queue에서 기다린 시간 (모두 합친 시간)
    - Responsive Time (응답시간) : ready Queue에 들어와서 처음으로 cpu를 점유하는데 걸리는 시간, time sharing environment 에서는 빠른 응답이 중요
    
<br>

## Cpu Scheduling Algorithms

### FCFS (First-Come First-Served)

- 먼저 온 프로세스부터 처리한다.
- 비선점형

<br>

### SJF (Shortest Job First)

- cpu burst(cpu 사용시간)가 가장 적게 드는 프로세스 먼저 스케쥴링
- average waiting time이 최소가 되도록 보장하는 알고리즘
- 2가지 관점
    - nonpreemptive : 일단 프로세스가 cpu를 선점하면 이번 cpu burst가 완료될 때까지 cpu를 뺏기지 않는다. 즉, 뒤에 cpu burst가 더 짧은 프로세스가 와도 상관 Nooo
    - preemptive : 현재 수행 중인 프로세스의 남은 cpu burst time보다 더 짧은 cpu burst time을 갖는 새로운 프로세스가 도착하면 cpu를 뺏긴다. (**SRTF** : shortest remaining time first)
- SJF의 2가지 문제
    - Starvation : 극단적으로 cpu 사용시간이 짧은 프로세스가 계속 들어온다면 cpu 사용시간이 긴 프로세스는 반영구적으로 cpu 점유 불가
    - 실제 cpu burst time을 미리 알 수 없음 : 과거의 cpu burst time으로 예측,추정 (exponential averaging)

<br>

### Priority Scheduling

- 우선순위가 제일 높은 프로세스에게 cpu를 줌 (smallest integer-hightest priority)
    - preemptive
    - nonpreemptive
- SJF는 일종의 priority scheduling
    - 즉, priority 가 cpu burst time
- 발생 문제 : starvation → 해결 : Aging (시간이 지남에 따라 우선순위를 높여준다)

<br>

### Round Robin (RR)

- 각 프로세스는 동일한 크기의 할당 시간 (**Time Quantum**) 을 가짐 → 응답시간이 빨라지는 장점
- 할당 시간이 지나면 프로세스는 preempted 당하고 ready queue의 제일 뒤에 가서 줄을 선다.
    - n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit일 때, 각 프로세스는 최대 q time unit 단위로 cpu 시간의 1/n 을 얻는다.
    - 즉, 각 프로세스는 최대 q(n-1) time unit 이상 기다리지 않는다.
- cpu burst가 긴 프로세스는 대기시간이 길어지고 cpu burst가 짧은 프로세스는 대기시간이 짧아진다.
- 성능 : q time unit이 중요
    - q가 크다면 FCFS와 다를바 없다.
    - q가 작다면 context switch로 인한 오버헤드가 커진다.
- 현대의 cpu scheduling은 대부분 RR기반이다.

<br>

### MultiLevel Queue

- Ready Queue를 여러개로 분할
    - Foreground : 사람과의 interaction이 많은 interaction한 프로세스
    - Background : batch process → no human interaction
- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
    - Foreground : RR
    - Background : FCFS
- 큐에 대한 (큐 사이의) 스케쥴링이 필요
    - Fixed Priority Scheduling
    - Time Slice

<br>

### MultiLevel Feedback Queue

- 생각해봐야 할 관점
    - 큐의 수
    - 각 큐의 스케쥴링 알고리즘
    - 프로세스를 하위 우선순위의 큐에서 상위 우선순위의 큐로 보내는 기준
    - 프로세스를 상위 우선순위의 큐에서 하위 우선순위의 큐로 내쫓는 기준
    - 프로세스가 cpu 서비스를 받으려할 때 들어갈 큐를 결정하는 기준
- Q의 우선 순위 : Q0 (RR : time quantum=8) > Q1 (RR : time quantum=16) > Q2 (FCFS)
    1. new job이 Q0로 들어감
    2. cpu를 잡아서 할당 시간 8ms동안 수행됨
    3. if (할당 시간내에 다 끝내면) out / else Q1으로 내려감
        1. Q1에서 줄서서 기다렸다가 cpu를 잡아서 16ms동안 수행됨
        2. if (할당 시간내에 다 끝내면) out / else Q2로 내려감
            1. Q2는 FCFS 방식으로 작동하는 큐이므로 순서를 기다림

**⬆ 1 cpu scheduling**

---

**⬇ multi cpu scheduling**

## Multiple Processor Scheduling

- cpu가 여러개인 경우 스케줄링은 더욱 복잡해짐
- Homogeneous Processor
    - 큐에 한 줄로 세워서 각 프로세서가 알아서 꺼내가게 할 수 있다.
    - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우시 문제가 복잡해짐
- Load Sharing
    - 일부 프로세서에 job이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
    - 별개의 큐를 두는 방법 vs 공동 큐를 사용 방법
- Symmetric Multiprocessing
    - 각 cpu가 대등하게, 각자 알아서 스케쥴링 결정
- Asymmetric Multiprocessing
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임, 나머지 프로세서는 거기에 따름
    
<br>


## Real-Time Scheduling

- Real-Time Job : Deadline이 있다. 정해진 시간안에 반드시 실해되야 하는 job. Deadline을 보장하는 job. 미리 스케쥴링하여 job을 배치
- Hard real-time system : 반드시 데드라인을 보장한다.
- Easy real-time computing : 일반 프로세스에 비해 높은 우선순위를 갖도록 한다. 데드라인을 반드시 보장하지 않는다.

<br>

## Thread Scheduling

- Thread : 1개의 프로세스 안에 여러개의 cpu 수행 단위
- Local Scheduling
    - user level thread의 경우
    - 사용자 수준의 thread library에 의해 어떤 thread를 스케쥴할지 결정
    - 사용자 프로세스가 직접 어떤 thread에게 cpu를 줄지 결정
- Global Scheduling
    - Kernel level thread의 경우
    - 일반 프로세스와 마찬가지로 커널의 단기 스케쥴러가 어떤 쓰레드를 스케쥴할지 결정
    
<br>

## Algorithm Evaluation (cpu algorithm 평가 모델)

- Queueing Models : 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index값을 구현
- Implementation(구현) & Measurement(성능 측정) : 실제 시스템에 알고리즘을 구현하여 실제 작업(workload)에 대해서 성능 측정 비교
- Simulation(모의실험) : 알고리즘을 모의 프로그램으로 작성 후 trace(input)을 입력으로 하여 결과 비교