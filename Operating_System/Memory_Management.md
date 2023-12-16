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