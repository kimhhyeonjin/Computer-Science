### Memory Management

- 메모리 주소
  
  - 메모리는 주소를 통해 접근
  
  - Logical Address vs Physical Address
    
    - Logical Address (논리 주소 = Virtual Address, 가상 주소)
      
      - 프로세스마다 독립적으로 가지는 주소 공간
      
      - 각 프로세스마다 0번부터 시작
      
      - **CPU가 보는 주소는 logical address**
    
    - Physical Address (물리 주소)
      
      - 메모리에 실제 올라가는 위치
  
  - 주소 바인딩 (Address Binding)
    
    - 주소를 결정하는 것
      
      - Symbolic Address -> Logical Address ---(이 시점)--->  Physical Address
    
    - Compile time binding
      
      - 물리적 메모리 주소 (physical address)가 컴파일 시 알려짐
        
        - 논리 주소가 물리 주소
        
        - 그렇기 때문에 다른 주소가 비어있어도 항상 0번부터 시작
          
          - 비효율적
      
      - 시작 위치 변경 시 재컴파일해야 함
      
      - 컴파일러는 절대 코드 (absolute code) 생성
    
    - Load time binding
      
      - 실행이 시작될 때 주소 변환이 이루어짐
        
        - = 메모리에 올라갈 때
      
      - Loader의 책임 하에 물리적 메모리 주소 부여
      
      - 컴파일러가 재배치가능코드 (relocatable code)를 생성한 경우 가능
    
    - Execution time binding (=Run time binding)
      
      - 수행이 시작된 이후에도 프로세스의 메모리 상 위치를 옮길 수 있음
      
      - CPU가 주소를 참조할 때마다 binding을 점검 (address mapping table)
      
      - 하드웨어적인 지원 필요 (base and limit registers, MMU)
        
        - MMU (Memory-Management Unit)
          
          - logical address를 physical address로 매핑해주는 **하드웨어** device
          
          - scheme
            
            ![MMU](./image/MMU.png)
            
            - 사용자 프로세스가 CPU에서 수행되며 생성해내는 모든 주소 값에 대해
            
            - limit register를 확인해 범위 내에 있으면 base register (relocation register)의 값을 logical address 값에 더함
              
              - limit register
                
                - 논리적 주소의 범위
                
                - 범위 내에 없으면 addressing error
              
              - relocation register
                
                - 접근할 수 있는 물리적 메모리 주소의 최소값

- 용어
  
  - dynamic loading
    
    - 프로세스 전체를 메모리에 미리 다 올리는 것이 아니라 해당 루틴이 불려질 때 메모리에 load하는 것
      
      - loading
        
        - 메모리로 올리는 것
    
    - memory utilization의 향상
    
    - 가끔씩 사용되는 많은 양의 코드의 경우 유용
      
      - 오류 처리 루틴 등
    
    - 운영체제의 특별한 지원 없이 프로그램 자체에서 구현 가능 (OS는 라이브러리를 통해 지원 가능)
  
  - dynamic linking
    
    - linking을 실행 시간 (execution time)까지 미루는 기법
    
    - static linking과 dynamic linking
      
      - static linking
        
        - 라이브러리가 프로그램의 실행 파일 코드에 포함
        
        - 실행 파일의 크기가 커짐
        
        - 동일한 라이브러리를 각각의 프로세스가 메모리에 올리므로 메모리 낭비 (prinf 함수의 라이브러리 코드와 같이 여러 번 사용되는 경우)
      
      - dynamic linking
        
        - 라이브러리가 실행 시 연결됨
        
        - 라이브러리 호출 부분에 라이브러리 루틴의 위치를 찾기 위한 stub이라는 작은 코드를 둠
        
        - 라이브러리가 이미 메모리에 있으면 그 루틴의 주소로 가고 없으면 디스크에서 읽어옴
        
        - 운영체제의 도움이 필요
  
  - overlays
    
    - 메모리에 프로세스의 부분 중 실제 필요한 정보만을 올림
    
    - 프로세스의 크기가 메모리보다 클 때 유용
    
    - 운영체제의 지원없이 사용자에 의해 구현
    
    - 작은 공간의 메모리를 사용하던 초창기 시스템에서 **수작업으로 프로그래머가 구현**
      
      - Manual Overlay
      
      - 프로그래밍이 매우 복잡
  
  - swapping
    
    - 프로세스를 일시적으로 메모리에서 backing store로 쫓아내는 것
      
      - backing store (swap area)
        
        - 디스크
          
          - 많은 사용자의 프로세스 이미지를 담을만큼 충분히 빠르고 큰 저장 공간
    
    - swap in / swap out
      
      - 일반적으로 중기 스케줄러 (swapper)에 의해 swap out 시킬 프로세스 선정
      
      - priority-based CPU scheduling algorithm
        
        - priority가 낮은 프로세스를 swapped out 시킴
        
        - priority가 높은 프로세스를 메모리에 올려놓음
      
      - compile time 혹은 load time binding에서는 원래 메모리 위치로 swap in 해야하기 때문에 큰 효과 못느낄 수도 있음
      
      - execution time binding에서는 추후 빈 메모리 영역 아무 곳에나 올릴 수 있음
      
      - swap time은 대부분 transfer time (swap되는 양에 비례하는 시간)임