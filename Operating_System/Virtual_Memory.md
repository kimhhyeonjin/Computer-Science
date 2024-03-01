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