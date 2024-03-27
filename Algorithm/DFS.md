### DFS (Depth-First Search, 깊이우선탐색)

- DFS
  
  - 그래프에서 깊은 부분을 우선적으로 탐색하는 알고리즘
  
  - 스택 자료구조 이용
    
    - 동작 과정
      
      - 시작 정점 v를 결정하여 방문
      
      - 정점 v에 인접한 정점 중에서 방문하지 않은 정점 w가 있으면 정점 v를 스택에 push하고 정점 w를 방문
        
        - 방문하지 않은 정점이 없으면 탐색의 방향을 바꾸기 위해서 스택을 pop하여 받은 가장 마지막 방문 정점을 v로 하여 다시 해당 과정을 반복한다
      
      - 위의 과정을 스택이 공백이 될 때까지 반복
    
    <details close>
    <summary>dfs 예시</summary>
    <div markdown="1">
    
    ```python
    def dfs(v):                       # v: 시작점
        top = -1
        visited[v] = 1                # 시작점 방문 표시
        print(v)
        while True:
            for w in adjList[v]:
            # adjList는 리스트의 각 인덱스에 선이 연결되어있는 숫자가 입력되어 있음
                if visited[w] == 0:
                    top += 1          # push(v)
                    stack[top] = v
                    v = w             # w에 방문
                    visited[w] = 1
                    print(v)
                    break
            else:
                if top != -1:         # 스택이 비어있지 않은 경우
                    v = stack[top]    # pop()
                    top -= 1
                else:                 # 스택이 비어있으면
                    break             # while문 빠져나옴
    
    # visited와 stack 리스트 생성
    N = int(input())              # 정점의 개수 받기
    visited = [0] * (N + 1)       # visited 생성
    stack = [0] * (N + 1)         # stack 생성
    # adjList 입력받기
    L = list(map(int, input().split()))   # 모든 경로를 받기
    E = len(L) // 2                       # 간선의 개수
    adjList = [[] for _ in range(N + 1)]  # N은 정점의 개수
    for i in range(E):
        a, b = list(map(int, input().split()))
        adjList[a].append(b)
        adjList[b].append(a)
    dfs(v)
    ```
    
    </div>
    </details>
  
  - 재귀 함수(Recursive Function)를 이용할 때 간결하게 구현할 수 있음
    
    - 재귀 함수 
      
      - 자기 자신을 다시 호출하는 함수
      
      - 내부적으로 스택의 자료구조와 동일
      
      - 호출 회수가 크면 실행속도가 느려짐
      
      <details close>
      <summary>재귀함수 예시</summary>
      <div markdown="1">
      
      ```python
      # 피보나치
      def fibo(n):
          if n < 2:
              return n
          else:
              return fibo(n-1) + fibo(n-2)
      
      # 중복호출이 존재하기 때문에 숫자가 커지면 엄청 느려짐
      # for i in range(101):
      #     print(i, fibo(i))
      for i in range(21):
          print(i, fibo(i))
      ```
      
      </div>
      </details>
      
      <details close>
      <summary>재귀함수를 이용한 dfs 예시</summary>
      <div markdown="1">
      
      ```python
      def dfs(v):
      print(v)  # v 방문
      visited[v] = 1
      for w in adjList[v]:
          if visited[w] == 0:  # 방문하지 않은 w
              dfs(w)
      ```
      
      </div>
      </details>