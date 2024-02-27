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