### CPU Scheduling

- CPU 스케줄링의 필요성
  
  - 프로그램은 CPU burst와 I/O burst의 연속
    
    - CPU burst
      
      - CPU를 사용하는 과정
        
        - load store, add store, read from file 등의 작업
    
    - I/O burst
      
      - I/O를 사용하는 과정
        
        - wait for I/O
  
  - 프로세스의 특성 분류
    
    - I/O bound process
      
      - I/O busrt가 중간중간 있어 CPU burst 지속시간이 짧은 프로세스
      
      - CPU burst가 짧은 잦음
    
    - CPU bound process
      
      - CPU burst 지속시간이 긴 프로세스
      
      - 계산 위주의 job
  
  - 컴퓨터 안에는 CPU bound job과 I/O bound job이 섞여있기 때문에 CPU 스케줄링이 필요함
    
    - I/O bound job은 상호작용이 필요한 job인데 CPU bound job이 CPU를 사용하고 있으면 I/O bound job이 너무 오래 기다려야 함
    
    - 효율성이 중요하기 때문에 I/O bound job에 우선권을 주어 빠르게 상호작용할 수 있도록 하기 위함

- CPU Scheduler & Dispatcher
  
  - 운영체제의 특정 기능
  
  - CPU Scheduler
    
    - Ready 상태의 프로세스 중에서 이번에 CPU를 줄 프로세스를 고름
  
  - Dispatcher
    
    - CPU의 제어권을 CPU scheduler에 의해 선택된 프로세스에게 넘김
      
      - context switch

- CPU 스케줄링이 필요한 경우
  
  - 프로세스가 running에서 blocked된 경우
    
    - I/O를 요청하는 시스템 콜 등
    
    - **nonpreemptive** (비선점형)
      
      - 강제로 빼앗지 않고 자진으로 반납
  
  - 프로세스가 running에서 ready된 경우
    
    - 할당 시간 만료로 인해 timer interrupt가 발생한 경우
    
    - **preemptive** (선점형)
      
      - 강제로 빼앗음
  
  - 프로세스가 blocked에서 ready된 경우
    
    - I/O 완료 후 interrupt
    
    - preemptive (선점형)
  
  - 프로세스가 terminate된 경우
    
    - nonpreemptive (비선점형)

- Scheduling Criteria
  
  - Performance Index (=Performance Measure, 성능 척도)
    
    - 시스템 입장에서의 성능 척도
      
      - CPU utilization (이용률)
        
        - keep the CPU as busy as possible
      
      - Throughput (처리량)
        
        - number of processes that complete their execution per time unit
    
    - 프로그램 입장에서의 성능 척도
      
      - Turnaround time (소요시간, 반환시간)
        
        - amount of time to execute a particular process
        
        - 프로세스가 시작돼서 종료될 때까지의 시간이 아니고 해당 프로세스가 CPU를 사용하러 들어와서 CPU를 쓰고 I/O를 하러 나갈 때까지 걸린 시간을 의미
      
      - Waiting time (대기 시간)
        
        - amount of time a process has been waiting in the ready queue
      
      - Response time (응답 시간)
        
        - amount of time it takes from when a request was submitted until **the first response is produced**, not output
        
        - time-sharing 환경에서 중요한 척도

### Scheduling Algorithms

- FCFS (First-Come First-Service)

- SJF (Shortest-Job-First)

- SRTF (Shortest-Remaining-Time-First)

- Priority Scheduling

- RR (Round Robin)

- Multilevel Queue

- Multilevel Feedback Queue

---

#### FCFS (First-Come First Service)

- 비선점형 스케줄링 (nonpreemptive)

- CPU를 오래 사용하는 프로그램이 CPU를 사용하고 있으면 프로세스가 다 끝날 때까지 기다려야 하기 때문에 비효율적임

- convoy effect (호위효과)
  
  - short process behind long process

### SJF (Shortest-Job-First)

- 각 프로세스의 다음번 CPU burst time을 가지고 스케줄링에 활용

- CPU burst time이 가장 짧은 프로세스를 제일 먼저 스케줄

- 최적의 방법임 (preemptive인 경우)
  
  - 주어진 프로세스에 대해 minimum average waiting time 보장

- 비선점형 스케줄링 (nonpreemptive)
  
  - 일단 CPU를 잡으면 CPU burst가 완료될 때까지 CPU 선점

- 선점형 스케줄링 (preemptive)
  
  - 현재 수행중인 프로세스의 남은 burst time보다 더 짧은 CPU burst time을 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김 **->** SRTF (Shortest-Remaining-Time-First)

- 문제점
  
  - starvation
    
    - CPU 사용시간이 긴 프로세스는 영원히 CPU를 사용하지 못할 수도 있음
  
  - CPU burst time을 미리 알 수 없음
    
    - 추정 (estimate)만 가능
      
      - 과거의 CPU burst time이용해서 추정
        
        - exponential averaging

#### Priority Scheduling (우선순위 스케줄링)

- 우선순위가 가장 높은 프로세스에게 CPU를 할당
  
  - smallest integer == highest priority
  
  - 우선순위를 정의하는 방법은 다양함

- SJF는 priority scheduling에 포함됨

- 비선점형 스케줄링 (nonpreemptive)
  
  - 일단 CPU를 잡으면 CPU burst가 완료될 때까지 CPU 선점

- 선점형 스케줄링 (preemptive)
  
  - 현재 수행중인 프로세스의 우선순위보다 더 높은 우선순위를 가지는 새로운 프로세스가 도착하면 CPU를 빼앗김

- 문제점
  
  - starvation
    
    - CPU 사용시간이 긴 프로세스는 영원히 CPU를 사용하지 못할 수도 있음
    
    - 이를 해결하고자 aging 기법 도입
      
      - 기다리는 시간이 길수록 우선순위를 높여줌

### RR (Round Robin)

- 현대적인 컴퓨터 시스템에서 사용하는 CPU 스케줄링은 RR에 기반하고 있음

- 각 프로세스는 동일한 크기의 할당 시간 (time quantum)을 가짐
  
  - 일반적으로 10-100 milliseconds

- 할당 시간이 지나면 프로세스는 선점 (preempted) 당하고 ready queue의 제일 뒤에 가서 줄 섬

- n개의 프로세스가 ready queue에 있고 할당 시간이 q time unit인 경우 각 프로세스는 최대 q time unit 단위로 CPU 시간의 1/n을 얻음
  
  - 어떤 프로세스도 (n-1)q time unit 이상 기다리지 않음

- 대기 시간이 각 프로세스의 CPU 사용시간에 비례
  
  - CPU를 길게 쓰는 프로세스는 기다리는 시간도 길어지고 CPU를 짧게 쓰는 프로세스는 기다리는 시간이 짧아짐

- 응답 시간이 빠르다는 장점

- q값에 따른 수행
  
  - q가 큰 경우
    
    - FCFS와 같음
  
  - q가 작은 경우
    
    - context switch가 빈번하게 발생하여 오버헤드가 커짐

- 일반적으로 SJF보다 average turnaround time이 길지만 response time은 더 짧음

### Multilevel Queue

- Ready queue를 여러 개로 분할
  
  - foreground (interactive)
  
  - background (batch - no human interaction)

- 각 큐는 독립적인 스케줄링 알고리즘을 가짐
  
  - foreground - RR
    
    - interactive 하기 때문
  
  - background - FCFS
    
    - CPU를 계속 사용하는 작업이 많기 때문

- 큐에 대한 스케줄링 필요
  
  - Fixed priority scheduling
    
    - foreground를 우선으로 처리하고 background 수행
    
    - starvation 있을 수도 있음
  
  - Time slice
    
    - 각 큐에 CPU time을 적절한 비율로 할당

### Multilevel Feedback Queue

- 프로세스가 다른 큐로 이동 가능

- aging을 이와 같은 방식으로 구현할 수 있음

- 파라미터
  
  - Queue의 수
  
  - 각 큐의 scheduling 알고리즘
  
  - 프로세스를 상위 큐로 보내는 기준
  
  - 프로세스를 하위 큐로 내쫓는 기준
  
  - 프로세스가 CPU 서비스를 받으려 할 때 들어갈 큐를 결정하는 기준

### 특이한 케이스에서의 CPU 스케줄링

- Multiple-Processor Scheduling
  
  - CPU가 여러 개인 경우 스케줄링은 더욱 복잡해짐
  
  - Homogeneous processor인 경우
    
    - 큐에 한 줄로 세워서 각 프로세서가 알아서 꺼내갈 수 있음
    
    - 반드시 특정 프로세서에서 수행되어야 하는 프로세스가 있는 경우 문제가 더 복잡해짐
  
  - Load sharing
    
    - 일부 프로세서에 일이 몰리지 않도록 부하를 적절히 공유하는 메커니즘 필요
    
    - 별개의 큐를 두는 방법 vs 공동 큐를 사용하는 방법
  
  - Symmetric Multiprocessing (SMP)
    
    - 각 프로세서가 각자 알아서 스케줄링 결정
  
  - Asymmetric Multiprocessing
    
    - 하나의 프로세서가 시스템 데이터의 접근과 공유를 책임지고 나머지 프로세서는 거기에 따름

- Real-Time Scheduling
  
  - Hard real-time systems
    
    - 정해진 시간 안에 반드시 끝내도록 스케줄링 해야 함
  
  - Soft real-time computing
    
    - 일반 프로세스에 비해 높은 우선순위를 갖도록 해야 함

- Thread Scheduling
  
  - Local Scheduling
    
    - User level thread의 경우 사용자 수준의 thread library에 의해 어떤 thread를 스케줄할지 결정
  
  - Global Scheduling
    
    - Kernel level thread의 경우 일반 프로세스와 마찬가지로 커널의 단기 스케줄러가 어떤 thread를 스케줄할지 결정

### Algorithm Evaluation

- Queueing models
  
  - 확률 분포로 주어지는 arrival rate와 service rate 등을 통해 각종 performance index 값을 계산

- Implementation & Measurement
  
  - 실제 시스템에 알고리즘을 구현하여 실제 작업에 대한 성능 측정 비교

- Simulation
  
  - 알고리즘을 모의 프로그램으로 작성 후 trace를 입력으로 하여 결과 비교