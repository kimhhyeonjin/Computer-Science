### Process Management

- 프로세스 생성 (process creation)
  
  - 부모 프로세스 (parent process)가 자식 프로세스 (children process) 생성
  
  - 프로세스의 트리 (계층 구조) 형성
  
  - 프로세스는 자원을 필요로 함
    
    - 운영체제로부터 받음
  
  - 자원의 공유
    
    - 부모와 자식이 모든 자원을 공유하는 모델
    
    - 일부를 공유하는 모델
    
    - 전혀 공유하지 않는 모델
      
      - 일반적인 형태
  
  - 수행 (execution)
    
    - 부모와 자식은 공존하며 수행되는 모델
    
    - 자식이 종료 (terminate) 될 때까지 부모가 기다리는 (blocked) 모델
  
  - 주소 공간 (address space)
    
    - 자식이 부모의 공간을 복사 (binary and OS data)
    
    - 그 공간에 새로운 프로그램 올림
  
  - 유닉스의 경우
    
    - **fork()** 시스템 콜이 `새로운 프로세스를 생성`
      
      - 부모를 그대로 복사 (OS data except PID + binary)
      
      - 주소 공간 할당
    
    - fork 다음에 이어지는 **exec()** 시스템 콜을 통해 `새로운 프로그램을 메모리에 올림`

- 프로세스 종료 (process termination)
  
  - 프로세스가 마지막 명령을 수행한 후 운영체제에게 이를 알려줌 (**exit**)
    
    - 자식이 부모에게 output data를 보냄
    
    - 프로세스의 각종 자원들이 운영체제에게 반납됨
  
  - 부모 프로세스가 자식의 수행을 종료시킴 (**abort**)
    
    - 자식이 할당 자원의 한계치를 넘어섬
    
    - 자식에게 할당된 태스크가 더 이상 필요하지 않음
    
    - 부모가 종료 (exit)하는 경우
      
      - 운영체제는 부모 프로세스가 종료하는 경우 자식이 더 이상 수행되도록 두지 않음
      
      - 단계적 종료

- 프로세스와 관련한 시스템 콜
  
  - fork()
    
    - create a child (copy)
    
    - creates a new address space that is a duplicate of the caller
      
      ```c
      int main()
      {
          int pid;
          pid = fork();
          // child
          if (pid == 0)
              printf("\n Hello, I am a child!\n");
          // parent
          else if (pid > 0)
              printf("\n Hello, I am a parent!\n")
      }
      ```
      
      - 부모의 fork의 반환값 (return value)은 양수이고, 자식은  fork의 반환값이 0
      
      - 자식이 복제되었을 때 `int main()`부터 실행되는 것이 아니고 `if (pid == 0)` 줄부터 실행됨
        
        - 부모와 진행과정도 동일한 상태에서 시작
  
  - exec()
    
    - overlay new image
    
    - replaces the memory image of the caller `with a new program`
      
      ```c
      int main()
      {
          int pid;
          pid = fork();
          if (pid == 0)
          {
              printf("\n Hello, I am a child! Now I'll run date \n");
              execlp("/bin/date", "/bin/date", (char *) 0);
          }
          else if (pid > 0)
              printf("\n Hello, I am a parent!\n")
      }
      ```
      
      - execlp
        
        - exec 시스템 콜을 하는 함수
        
        - 이전의 기억은 잊고 새로운 프로그램으로 덮어 씌우는 것
      
      ```c
      int main()
      {
          printf("\n Hello, I am a child! Now I'll run date \n");
          execlp("/bin/date", "/bin/date", (char *) 0);
          printf("\n Hello, I am a parent!\n")
      }
      ```
      
      - exce 이후의 코드는 실행 불가능
        
        - `printf("\n Hello, I am a parent!\n")`는 실행되지 않음

  - wait()
    
    - sleep until child is done
    
    - 프로세스 A가 wait() 시스템 콜을 호출하면 커널은 자식이 종료될 때까지 프로세스 A를 block 상태로 재우고 자식 프로세스가 종료되면 ready 상태로 깨움
    
    - 프로세스 수행 시 자식이 종료될 때까지 부모가 기다리는 모델에 해당
  
  - exit()
    
    - frees all the resources, notify parent
    
    - 프로세스의 종료
      
      - 자발적 종료
        
        - exit() 시스템 콜을 통한 방법
        
        - 프로그램에 명시적으로 적어주지 않아도 main 함수가 리턴되는 위치에 컴파일러가 넣어줌
      
      - 비자발적 종료
        
        - 부모 프로세스가 자식 프로세스를 강제 종료시킴
          
          - 자식 프로세스가 한계치를 넘어서는 자원 요청
          
          - 자식에게 할당된 태스크가 더 이상 필요하지 않음
        
        - 키보드로 kill, break 등을 친 경우
        
        - 부모가 종료하는 경우
          
          - 부모 프로세스가 종료하기 전 자식이 먼저 종료됨

- 프로세스 간 협력
  
  - 독립적 프로세스 (Independent process)
    
    - 프로세스는 각자의 주소 공간을 가지고 수행되므로 원칙적으로 하나의 프로세스는 다른 프로세스의 수행에 영향을 미치지 못함
  
  - 협력 프로세스 (Cooperating process)
    
    - 프로세스 협력 메커니즘을 통해 하나의 프로세스가 다른 프로세스의 수행에 영향을 미칠 수 있음
  
  - 프로세스 간 협력 메커니즘 (IPC: Interprocess Communication)
    
    - message passing
      
      - **커널**을 통해 메시지 전달
      
      - 프로세스 사이에 공유 변수 (shared variable)를 일체 사용하지 않고 통신하는 시스템
        
        - direct communication
          
          - 통신하려는 프로세스의 이름을 명시적으로 표시
        
        - indirect communication
          
          - 커널에 존재하는 mailbox 또는 port를 통해 메시지를 간접적으로 전달
    
    - shared memory
      
      - 서로 다른 프로세스 간에도 **일부 주소 공간을 공유**하게 하는 메커니즘
      
      - 커널에 shared memory를 한다는 시스템 콜을 보내어 매핑을 한 후 공유
    
    - <<참고>> thread
      
      - thread는 사실상 하나의 프로세스이므로 프로세스 간 협력으로 보기는 어렵지만 동일한 프로세스를 구성하는 thread 간에는 주소 공간을 공유하므로 협력 가능