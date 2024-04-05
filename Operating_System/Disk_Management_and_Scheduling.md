### Disk Management and Scheduling

![Disk](./image/Disk.png)

- Disk Structure
  
  - logical block
    
    - 디스크의 외부에서 보는 디스크의 단위 정보 저장 공간들
    
    - 주소를 가진 1차원 배열처럼 취급
    
    - 정보를 전송하는 최소 단위
  
  - sector
    
    - logical block이 물리적인 디스크에 매핑된 위치
    
    - sector 0은 최외곽 실린더의 첫 트랙에 있는 첫 번째 섹터

- Disk Management
  
  - physical formatting (low-level formatting)
    
    - 디스크를 컨트롤러가 읽고 쓸 수 있도록 섹터들로 나누는 과정
    
    - 각 섹터는 header + 실제 data (보통 512 bytes) + trailer로 구성
    
    - header와 trailer는 sector number, ECC (Error-Correcting Code) 등의 정보가 저장되며 controller가 직접 접근 및 운영
  
  - partitioning
    
    - 디스크를 하나 이상의 실린더 그룹으로 나누는 과정
    
    - OS는 이것을 독립적 disk로 취급 (logical disk)
  
  - logical formatting
    
    - 파일 시스템을 만드는 것
    
    - FAT, inode, free space 등의 구조 포함
  
  - booting
    
    - ROM에 있는 small bootstrap loader의 실행
    
    - sector 0 (boot block)을 load하여 실행
    
    - sector 0은 full bootstrap loader program
    
    - OS를 디스크에서 load하여 실행

- Disk Scheduling
  
  - disk scheduling
    
    - access time의 구성
      
      - seek time
        
        - 헤드를 해당 실린더로 움직이는데 걸리는 시간
      
      - rotational latency
        
        - 헤드가 원하는 섹터에 도달하기까지 걸리는 회전지연시간
        
        - seek time의 1/10 정도
      
      - transfer time
        
        - 실제 데이터의 전송 시간
    
    - disk bandwidth
      
      - 단위 시간 당 전송된 바이트의 수
    
    - disk scheduling
      
      - seek time을 최소화하는 것이 목표
      
      - seek time ~= seek distance
  
  - disk scheduling algorithm
    
    - FCFS (First Come First Served)
      
      - 들어온 순서대로 처리
      
      - 비효율적
    
    - SSTF (Shortest Seek Time First)
      
      - 현재 헤드 위치에서 제일 가까운 요청을 가장 먼저 처리
      
      - starvation 문제
    
    - SCAN
      
      - disk arm이 디스크의 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청 처리
      
      - 다른 한쪽 끝에 도달하면 역방향으로 이동하며 오는 길목에 있는 모든 요청을 처리하며 다시 반대쪽 끝으로 이동
      
      - 실린더 위치에 따라 대기 시간이 다름
        
        - 가운데 부분이 양 끝부분보다 대기 시간이 짧음
    
    - C-SCAN (Circular SCAN)
      
      - 헤드가 한쪽 끝에서 다른쪽 끝으로 이동하며 가는 길목에 있는 모든 요청을 처리
      
      - 다른쪽 끝에 도달했으면 요청을 처리하지 않고 곧바로 출발점으로 다시 이동
      
      - SCAN과 비교했을 때 이동거리는 길어지지만 균일한 대기 시간 제공
    
    - N-SCAN
      
      - SCAN의 변형 알고리즘
      
      - 일단 arm이 한 방향으로 움직이기 시작하면 그 시점 이후에 도착한 job은 되돌아올 때 service
    
    - LOOK
      
      - SCAN과 진행방향은 같음
      
      - 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 이동
    
    - C-LOOK
      
      - C-SCAN과 진행방향은 같음
      
      - 헤드가 진행 중이다가 그 방향에 더 이상 기다리는 요청이 없으면 헤드의 이동방향을 즉시 반대로 이동