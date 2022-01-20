# CPU_Scheduling

## CPU 스케줄링의 필요성

- 프로그램은 **CPU Burst**, **I/O Burst**의 연속이나 프로그램의 종류에 따라서는 빈도나 길이가 다름
- 여러 종류의 job(=process)이 섞여 있기 때문에 CPU 스케줄링이 필요함
- interactive job에게 적절한 response 제공 요망
- CPU와 I/O 장치 등 시스템 자원을 골고루 효율적으로 사용

## 프로세스 특성 분류

- I/O bound process
    - CPU를 잡고 계산하는 시간보다 I/O에 많은 시간이 필요한 job
    - CPU를 짧게 쓰고 중간에 I/O가 빈번히 끼어드는 작업 → interactive job
    - many / short CPU bursts
- CPU bound process
    - 계산 위주의 job
    - CPU만 오래 쓰는 작업
    - few / very long CPU bursts

## CPU Scheduler & Dispatcher → 물리적인 것이 아니라 OS에 코드로 존재 (추상화된 기능!)

- CPU Scheduler
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고름
- Dispatcher
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘김 (context switch 과정)

## CPU 스케줄링이 필요한 경우

1. Running -> Blocked (I/O 요청하는 시스템 콜)
2. Running -> Ready (할당 시간 만료로 timer interrupt)
3. Blocked -> Ready (I/O 완료 후 interrupt)
4. Terminate

> - 1,4 → **nonpreemptive** (자진 반납)
> - 나머지 → **preemptive** (강제로 빼앗김)
