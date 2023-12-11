### Process Synchronization

- race condition (경쟁 상태)
  
  - 여러 프로세스가 공유 데이터를 동시에 접근할 때 실행 순서에 따라 값이 달라지는 상황
  
  - 데이터의 최종 연산 결과는 마지막에 그 데이터를 다룬 프로세스에 따라 달라짐
  
  - OS에서 race condition이 발생하는 경우
    
    - kernel 수행 중 인터럽트 발생 시
      
      - 커널모드 running 중 interrupt가 발생하여 인터럽트 처리루틴 수행
      
      - 양쪽 다 커널 코드이므로 kernel address space 공유
      
      - 해결책
        
        - 공유데이터에서 작업이 진행되는 경우 interrupt를 막음
        
        - 이후 interrupt가 진행될 수 있도록
    
    - 프로세스가 system call을 하여 kernal 모드로 수행중인데 context switch가 일어나는 경우
      
      - system call을 하는 동안 kernel address space의 data를 공유할 수 있음
      
      - 해결책
        
        - 커널 모드에서 수행 중일 때는 CPU는 preempt하지 않음
        
        - 커널 모드에서 사용자 모드로 돌아갈 때 preempt
    
    - multiprocessor에서 shared memory 내의 kernel data
      
      - interrupt enable/disable로 해결되지 않음
      
      - 해결책 두 가지
        
        - 한번에 하나의 CPU만 커널에 들어갈 수 있도록 함
        
        - 커널 내부에 있는 각 공유 데이터에 접근할 때마다 그 데이터에 대한 lock / unlock을 하는 방법

- Process Synchronization 문제
  
  - **공유 데이터의 동시 접근**은 데이터의 불일치 문제를 발생시킬 수 있음
  
  - 일관성 유지를 위해서는 협력 프로세스 (cooperating process) 간 실행 순서를 정해주는 메커니즘 필요
  
  - race condition을 막기 위해서는 concurrent process가 동기화되어야 함

- Critical Section (임계구역) Problem
  
  - n개의 프로세스가 공유 데이터를 동시에 사용하기를 원하는 경우 각 프로세스의 code segment에 존재하는 **공유 데이터를 접근하는 코드**
  
  - 하나의 프로세스가 critical section에 있을 때 다른 모든 프로세스는 critical section에 들어갈 수 없어야 함

- 프로그램적 해결법의 충족 조건
  
  - 가정
    
    - 모든 프로세스의 수행 속도는 0보다 빠름
    
    - 프로세스 간 상대적인 수행 속도는 가정하지 않음
  
  - Mutual Exclusion (상호 배제)
    
    - 프로세스가 critical section 부분을 수행 중이면 다른 모든 프로세스는 critical section에 들어가면 안됨
  
  - Progress (진행)
    
    - 아무도 critical section에 있지 않은 상태에서 critical section에 들어가고자 하는 프로세스가 있으면 critical section에 들어가게 해주어야 함
  
  - Bounded Waiting (유한 대기)
    
    - 프로세스가 critical section에 들어가려고 요청한 후부터 그 요청이 허용될 때까지 다른 프로세스가 critical section에 들어가는 횟수에 한계가 있어야 함

- 프로세스의 일반적인 구조
  
  ```c
  do {
      entry section
      critical section        # 공유하는 부분
      exit section
      remainder section
  } while (1);
  ```
  
  - 알고리즘1
    
    - 프로세스 i는 turn == i인 경우 critical section에 들어갈 수 있음
      
      - 프로세스 0의 경우
        
        ```c
        do {
            while (turn != 0);      # turn 값이 0이 아닌 경우 계속 while문 반복
                                    # turn 값이 0이 되면 아래의 critical section 실행
            critical section        # 공유하는 부분
            turn = 1;               # 프로세스 1이 동작할 수 있도록 turn 값 변경
            remainder section
        } while (1);
        ```
      
      - 프로세스 1의 경우
        
        ```c
        do {
            while (turn != 1);      # turn 값이 1이 아닌 경우 계속 while문 반복
                                    # turn 값이 1이 되면 아래의 critical section 실행
            critical section        # 공유하는 부분
            turn = 0;               # 프로세스 0이 동작할 수 있도록 turn 값 변경
            remainder section
        } while (1);
        ```
    
    - mutual exclusion은 만족하지만 progress는 만족하지 않음
      
      - 과잉양보
        
        - 반드시 한번씩 교대로 들어가야만 함
        
        - 특정 프로세스가 더 빈번히 critical section에 들어가야 하는 경우 문제 발생
        
        - 프로세스 0은 여러번 들어가야 하고 프로세스 1은 한번만 들어가는 경우 프로세스 0은 영원히 들어갈 수 없음
  
  - 알고리즘2
    
    - flag 변수 사용
      
      boolean flag[n];
      
      - 프로세스 i의 경우
        
        ```c
        do {
            flag[i] = true;         # 나 들어갈거야
            while (flag[j]);        # j번째 flag 확인해서 critical section 사용하는지 확인하고 true이면 끝날 때까지 기다림
            critical section
            flag[i] = false;        # 나 나왔어
            remainder section
        } while (1);
        ```
    
    - mutual exclusion은 만족하지만 progress는 만족하지 않음
      
      - 1행 수행 후 CPU를 넘겨준 경우 두 프로세스가 critical section에 들어가지 못하고 끊임없이 양보만 할 수 있음
  
  - 알고리즘3 (Peterson's Algorithm)
    
    - 알고리즘1과 알고리즘2 결합
      
      ```c
      do {
          flag[i] = true;         # 나 들어갈거야
          turn = j
          while (flag[j] && turn == j);
          critical section
          flag[i] = false;        # 나 나왔어
          remainder section
      } while (1);
      ```
      
      - 세 가지 충족 조건을 모두 만족
      
      - busy waiting (=spin lock) 문제 존재
        
        - 계속 CPU와 메모리를 쓰면서 기다리기 때문에 비효율적임
  
  - 하드웨어적으로 문제 해결하기
    
    - Test_and_set(a)
      
      - a를 읽고 a값을 1로 지정하는 명령을 한번에 처리
      
      ```c
      # Synchronization variable:
      boolean lock = false;
      
      # Process Pi
      do {
          while (Test_and_set(lock));
          # lock이 false인 경우 lock을 true(=1)로 바꾼 후 critical section 처리
          critical section
          lock = false;
          remainder section
      }
      ```

- Semaphores
  
  - 일종의 추상화
  
  - 정수값을 가질 수 있음
    
    - 자원의 갯수 의미
  
  - P 연산과 V 연산 존재
    
    - P 연산
      
      - Semaphore 값 (공유 데이터)을 획득하는 과정
      
      - lock을 거는 과정
        
        ```c
        # P(S):
        
        while (S <= 0) do no-op;    # wait
        S--;
        ```
    
    - V 연산
      
      - Semaphore 값 (공유 데이터)을 반납하는 과정
      
      - lock을 푸는 과정
        
        ```c
        # V(S):
        
        S++;
        ```
  
  - critical section of n processes
    
    ```c
    # Synchronization variable
    semaphore mutex;        # 처음은 1 : 1개가 CS에 들어갈 수 있음
    
    # Process Pi
    do {
        P(mutex);
        critical section
        V(mutex);
        remainder section
    } while (1);
    ```
    
    - busy wait는 효율적이지 못함 (=spin lock)
  
  - block / wakeup implementation
    
    - semaphore 정의
      
      ```c
      typedef struct
      {
          int value;            # semaphore
          struct process *L;    # process wait queue
      } semaphore;
      ```
      
      - P 연산
        
        ```c
        S.value--;
        if (S.value < 0) {    # 자원의 여분이 없음
            add this process to S.L;
            block();
        }
        ```
      
      - V 연산
        
        ```c
        S.value++;
        if (S.value <= 0) {
            remove a process P from S.L;
            wakeup(P);
        }
        ```
    
    - block과 wakeup 가정
      
      - block
        
        - 커널은 block을 호출한 프로세스를 suspend 시킴
        
        - 이 프로세스의 PCB를 semaphore에 대한 wait queue에 넣음
      
      - wakeup(P)
        
        - block된 프로세스 P를 wakeup 시킴
        
        - 이 프로세스의 PCB를 ready queue로 옮김
  
  - busy-wait vs block/wakeup
    
    - critical section의 길이가 긴 경우 block/wakeup이 적당
    
    - critical section의 길이가 매우 짧은 경우 block/wakeup 오버헤드가 busy-wait보다 더 커질 수 있음
    
    - 일반적으로는 block/wakeup 방식이 더 좋음
  
  - Two types of semaphores
    
    - counting semaphores
      
      - 도메인이 0 이상인 임의의 정수값
      
      - 주로 resource counting에 사용
    
    - binary semaphore (mutex)
      
      - 0 또는 1 값만 가질 수 있는 semaphore
      
      - 주로 mutual exclusion (lock/unlock)에 사용

- classical problems of synchronization
  
  - bounded-buffer problem (producer-consumer problem)
  
  - readers and writers problem
  
  - dining-philosophers problem

- bounded-buffer problem (producer-consumer problem)
  
  - buffer
    
    - 임시로 데이터를 저장하는 공간
  
  - producer은 shared memory에 empty buffer가 있으면 데이터 추가
    
    - empty buffer가 있는지 확인 (없으면 기다림)
    
    - 공유데이터에 lock 걺
    
    - empty buffer에 데이터 입력 및 buffer 조작
    
    - lock을 풂
    
    - full buffer 하나 증가
  
  - consumer는 shared memory에 데이터가 있는 buffer가 있으면 사용
    
    - full buffer가 있는지 확인 (없으면 기다림)
    
    - 공유데이터에 lock 걺
    
    - full buffer에서 데이터 꺼내고 buffer 조작
    
    - lock 풂
    
    - empty buffer 하나 증가
  
  - shared data
    
    - buffer 자체 및 buffer 조작 변수 (empty/full buffer의 시작 위치)
  
  - synchronization variables
    
    - mutual exclusion
      
      - need binary semaphore
      
      - shared data의 mutual exclusion
    
    - resource count
      
      - need integer semaphore
      
      - 남은 empty/full buffer의 수 표시
  
  - 수도코드
    
    - synchronization variables
      
      ```c
      semaphore full = 0, empty = n, mutex = 1;
      ```
    
    - producer
      
      ```c
      do {
          ...
          produce an item in x
          ...
          P(empty);
          P(mutex);
          ...
          add x to buffer
          ...
          V(mutex);
          V(full);
      } while (1);
      ```
    
    - consumer
      
      ```c
      do {
          P(full);
          P(mutex);
          ...
          remove an item from buffer to y
          ...
          V(mutex);
          V(empty);
          ...
          consume the item in y
          ...
      } while (1);
      ```

- readers and writers problem
  
  - 한 프로세스가 공유데이터 (여기서는 DB)에 write 중일 때 다른 프로세스가 접근하면 안됨
  
  - read는 동시에 여럿이 가능
  
  - 해결 방법
    
    - writer가 DB에 접근 허가를 아직 얻지 못한 상태에서는 모든 대기중인 reader를 다 DB에 접근 가능하도록
    
    - writer는 대기 중인 reader가 하나도 없을 때 DB 접근 허용
    
    - writer가 DB 접근 중이면 reader 접근은 금지
    
    - writer가 DB를 빠져나가야만 reader 접근 허용
  
  - shared data
    
    - DB 자체
    
    - readcount
      
      - 현재 DB에 접근 중인 reader의 수
  
  - synchronization variables
    
    - mutex
      
      - critical section의 mutual exclusion 보장을 위해 사용
    
    - db
  
  - 수도코드
    
    ```c
    # Shared data
    int readcount = 0;
    DB 자체;
    
    # Synchronization variables
    semaphore mutex = 1, db = 1;
    
    # Writer
    P(db);
    ...
    writing DB performed
    ...
    V(db);
    # starvation 가능
    
    # Reader
    P(mutex);                     # readcount lock 설정
    readcount++;
    if (readcount == 1) P(db);    # block writer
    V(mutex);                     # readcount lock 해제
    ...
    reading DB performed
    ...
    P(mutex);                     # readcount lock 설정
    readcount--;
    if (readcount == 0) V(db);    # 마지막 reader인 경우 enable writer
    V(mutex);                     # readcount lock 해제
    ```

- dining-philosophers problem
  
  - 수도코드
    
    ```c
    # Synchronization variables
    semaphore chopstick[5];        # initially all values are 1
                                   # [1, 1, 1, 1, 1]
    
    # Philosopher i
    do {
        P(chopstick[i]);
        P(chopstick[(i+1) % 5]);
        ...
        eat();
        ...
        V(chopstick[i]);
        V(chopstick[(i+1) % 5]);
        ...
        think();
        ...
    } while(1);
    ```
    
    - deadlock 가능성 있음
      
      - deadlock
        
        - 둘 이상의 프로세스가 서로 상대방에 의해 충족될 수 있는 이벤트를 무한히 기다리는 현상
      
      - 모든 철학자가 동시에 왼쪽 젓가락을 집은 경우
    
    - 해결 방안
      
      - 4명의 철학자만 동시에 앉을 수 있도록
      
      - 젓가락을 두 개 모두 집을 수 있을 때에만 젓가락을 집을 수 있도록
        
        ```c
        enum {thinking, hungry, eating} state[5];
        # 젓가락 권한 (1이면 권한 있음, 0이면 없음)
        semaphore self[5] = 0;
        semaphore mutex = 1;
        
        # test
        void test(int i) {
            if (state[(i+4) % 5] != eating && state[i] == hungry && state[(i+1) % 5] != eating) {
                state[i] = eating;
                V(self[i]);
            }
        }
        
        # pickup
        void pickup(int i) {
            P(mutex);
            state[i] = hungry;
            test(i);
            V(mutex);
            P(self[i]);
        }
        
        # putdown
        void putdown(int i) {
            P(mutex);
            state[i] = thinking;
            test((i+4) % 5);
            test((i+1) % 5);
            V(mutex);
        }
        
        # Philosopher i
        do {
            pickup(i);
            eat();
            putdown(i);
            think();
        } while(1);
        ```
      
      - 짝수 철학자는 왼쪽 젓가락부터 집도록하고 홀수 철학자는 오른쪽 젓가락부터 집도록

- Monitor
  
  - semaphore의 문제점
    
    - 코딩 어려움
    
    - 정확성 입증 어려움
    
    - 자발적 협력 필요
    
    - 한 번의 실수가 모든 시스템에 치명적 영향
  
  - monitor
    
    - 동시 수행중인 프로세스 사이에서 abstract data type의 안전한 공유를 보장하기 위한 high-level synchronization construct
      
      - semaphore에서의 lock이 필요하지 않음
        
        ```c
        monitor monitor-name
        {
            shared variable declarations
            procedure body P1(...) {
                ...
            }
            procedure body P2(...) {
                ...
            }
            ...
            procedure body Pn(...) {
                ...
            }
            {
                initialization code
            }
        }
        ```
    
    - 모니터 내에서는 한 번에 하나의 프로세스만 활동 가능
    
    - 프로그래머가 동기화 제약 조건을 명시적으로 코딩할 필요 없음
    
    - 프로세스가 monitor 안에서 기다릴 수 있도록 하기 위해 condition variable 사용
      
      - condition variable은 **wait**와 **signal** 연산에 의해서만 접근 가능
      
      - x.wait();
        
        - x.wait()을 invoke한 프로세스는 다른 프로세스가 x.signal()을 invoke하기 전까지 suspend됨
      
      - x.signal();
        
        - 정확하게 하나의 suspend된 프로세스를 resume
        
        - suspend된 프로세스가 없으면 아무 일도 일어나지 않음
  
  - bounded-buffer problem
    
    ```c
    monitor bounded_buffer
    {
        int buffer[N];
        # condition variable은 값을 가지지 않고 자신의 큐에 프로세스를 매달아서 sleep시키거나 큐에서 프로세스를 깨우는 역할만 함
        condition full, empty;
    
        void produce(int x) {
            if there is no empty buffer
                empty.wait();
            add x to an empty buffer
            full.signal();
        }
    
        void consume(int *x) {
            if there is no full buffer
                full.wait();
            remove an item from buffer and store it to *x
            empty.signal();
        }
    }
    ```
  
  - dining philosophers example
    
    ```c
    monitor dining_philosopher
    {
        enum {thinking, hungry, eating} state[5];
        condition self[5];
    
        void pickup(int i) {
            state[i] = hungry;
            test(i);
            if (state[i] != eating) {
                self[i].wait();
            }
        }
    
        void putdown(int i) {
            state[i] = thinking;
            test((i+4) % 5);
            test((i+1) % 5);
        }
    
        void test(int i) {
            if ((state[(i+4) % 5] != eating) && (state[i] == hungry) && (state[(i+1) % 5] != eating)) {
                state[i] = eating;
                self[i].signal();
            }
        }
    
        void init() {
            for (int i = 0; i < 5, i++) {
                state[i] = thinking;
            }
        }
    }
    
    Philosopher(i):
    {
        pickup(i);
        eat();
        putdown(i);
        think();
    } while(1)
    ```