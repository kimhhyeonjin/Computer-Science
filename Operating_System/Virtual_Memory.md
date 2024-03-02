### Virtual Memory

- demand paging
  
  - 실제로 필요할 때 page를 메모리에 올리는 것
    
    - I/O 양의 감소
    
    - memory 사용량 감소
    
    - 빠른 응답 시간
    
    - 더 많은 사용자 수용
  
  - valid / invalid bit 사용
    
    - invalid
      
      - 사용되지 않는 주소 영역인 경우
      
      - 페이지가 물리적 메모리에 없는 경우
    
    - 처음에는 모든 page entry가 invalid로 초기화
    
    - address translation 시에 invalid bit로 set되어 있으면 **page fault**
      
      - 요청한 페이지가 메모리에 없는 경우
      
      - page fault
        
        - invalid page를 접근하면 MMU가 trap을 발생시킴 (page fault trap)
          
          - CPU가 운영체제한테 자동으로 넘어감
        
        - kernel mode로 들어가서 page fault handler가 invoke 됨
        
        - 아래의 순서로 page fault 처리
          
          - 잘못된 요청인지 확인 (bad address, protection violation 등)
            
            - 프로세스 중단
          
          - 빈 페이지 프레임을 가져옴
            
            - 페이지를 디스크에서 메모리로 올려줘야 함
            
            - 메모리가 가득 찬 경우 기존의 페이지에서 뺏어옴
          
          - 해당 페이지를 disk에서 memory로 읽어옴
            
            - disk I/O가 끝나기까지 이 프로세스는 CPU를 preempt 당함 (block)
            
            - disk read가 끝나면 page tables entry 기록, valid/invalid bit을 valid로
            
            - ready queue에 process를 insert -> dispatch later
          
          - 이 프로세스가 CPU를 잡고 다시 running
          
          - 아까 중단되었던 instruction을 재개
  
  - performance of demand paging
    
    - page fault rate에 따라 크게 좌우됨
    
    - page fault rate p, 0 <= p <= 1
      
      - p = 0이면 page fault 없음
      
      - p = 1이면, 메모리 참조할 때마다 page fault 발생
      
      - 실제로는 거의 0에 가까운 값으로 나옴
    
    - effective access time
      
      - (1 - p) * memory access + p * (OS & HW page fault overhead + [swap page out if needed] + swap page in + OS & HW restart overhead)
        
        - page fault가 발생하면 엄청난 시간이 소요됨

- Replacement Algorithm
  
  - page replacement
    
    - free frame이 없는 경우 어떤 프레임을 빼앗아올지 정해야 함
      
      - OS의 역할
    
    - 곧바로 사용되지 않을 page를 쫓아내는 것이 좋음
    
    - 동일한 페이지가 여러 번 메모리에서 쫓겨났다가 다시 들어올 수 있음
  
  - replacement algorithm
    
    - page-fault rate을 최소화하는 것이 목표
    
    - 알고리즘의 평가
      
      - 주어진 page reference string에 대해 page fault를 얼마나 내는지 조사
  
  - optimal algorithm
    
    - 가장 먼 미래에 참조되는 page를 replace
    
    - 미래의 참조를 현실적으로 알 수는 없기 때문에 offline algorithm이라고 불림
      
      - 다른 알고리즘의 성능에 대한 upper bound 제공
        
        - Belady's optimal algorithm, MIN, OPT 등으로 불림
  
  - FIFO (First In First Out) algorithm
    
    - 먼저 들어온 페이지를 먼저 내쫓음
    
    - FIFO Anomaly (Belady's Anomaly)
      
      - 메모리 크기를 늘리는데 page fault 수가 더 늘어나 성능이 더 나빠지는 경우가 발생하기도 함
  
  - LRU (Least Recently Used) algorithm
    
    - 가장 오래 전에 참조된 것을 지움
    
    - linked-list 형태
      
      - O(1) complexity
  
  - LFU (Least Frequently Used) algorithm
    
    - 참조 횟수(reference count)가 가장 적은 페이지를 지움
      
      - 참조 횟수가 가장 적은 페이지가 여러 개인 경우
        
        - LFU 알고리즘 자체에서는 여러 page 중 임의로 선정
        
        - 성능 향상을 위해 가장 오래 전에 참조된 page를 지우게 구현할 수도 있음
      
      - 장단점
        
        - LRU처럼 직전 참조 시점만 보는 것이 아니라 장기적인 시간 규모를 보기 때문에 page의 인기도를 좀 더 정확히 반영할 수 있음
        
        - 참조 시점의 최근성을 반영하지 못함
        
        - LRU보다 구현이 복잡함
    
    - heap 형태
      
      - O(log n) complexity