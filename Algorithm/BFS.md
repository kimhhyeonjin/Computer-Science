### BFS (Breadth-First Search, 너비우선탐색)

- BFS
  
  - 그래프에서 가까운 노드부터 탐색하는 알고리즘
  
  - 큐 자료구조 이용
    
    - 동작 과정
      
      - 탐색 시작 노드를 큐에 삽입하고 방문 처리
      
      - 큐에서 노드를 꺼내 해당 노드의 인접 노드 중에서 방문하지 않은 노드를 모두 큐에 삽입하고 방문 처리
      
      - 위의 과정을 큐가 공백이 될 때까지 반복
      
      <details close>
      <summary>bfs 예시1</summary>
      <div markdown="1">
      
      ```python
      def bfs(v, N):  # v: 시작정점, N: 마지막정점
          visited = [0] * (N+1)  # visited 생성
          q = []  # 큐 생성
          q.append(v)  # 시작점 인큐
          visited[v] = 1  # 시작점 처리 표시
      
          while q:  # 큐가 비어있지 않으면
              v = q.pop(0)  # 디큐
              print(v)  # visit(v)
              for w in adjList[v]:  # 인접하고 미방문(인큐되지 않은) 정점 w가 있으면
                  if visited[w] == 0:
                      q.append(w)
                      visited[w] = visited[v] + 1
      
      V, E = map(int, input().split())
      N = V + 1
      adjList = [[] for _ in range(N)]
      for _ in range(E):
          a, b = map(int, input().split())
          adjList[a].append(b)
          adjList[b].append(a)
      
      bfs(0, V)
      ```
      
      </div>
      </details>
    
    - deque 자료구조
      
      ```python
      from collections import deque
      ```
      
      - 스택과 큐의 장점을 모두 채택
      
      - 데이터를 넣고 빼는 속도가 리스트 자료형에 비해 효율적
      
      <details close>
      <summary>deque 예시</summary>
      <div markdown="1">
      
      ```python
      from collections import deque
      
      queue = deque()
      queue.append(5)
      queue.append(2)
      queue.append(3)
      queue.append(7)
      queue.popleft()
      queue.append(1)
      queue.append(4)
      queue.popleft()
      
      print(queue)        # deque([3, 7, 1, 4])
      queue.reverse()
      print(queue)        # deque([4, 1, 7, 3])
      print(list(queue))  # [4, 1, 7, 3]
      ```
      
      </div>
      </details>
      
      <details close>
      <summary>bfs 예시2</summary>
      <div markdown="1">
      
      ```python
      from collections import deque
      
      N, M = map(int, input().split())
      adjList = [[] for _ in range(N+1)]
      visited = [0] * (N+1)
      for _ in range(M):
          u, v = map(int, sys.stdin.readline().strip().split())
          adjList[u].append(v)
          adjList[v].append(u)
      
      def bfs(i):
          q = deque([i])
          while q:
              x = q.popleft()
              for nxt in adjList[x]:
                  if not visited[nxt]:
                      visited[nxt] = 1
                      q.append(nxt)
      
      cnt = 0
      for i in range(1, N+1):
          if not visited[i]:
              cnt += 1
              visited[i] = 1
              bfs(i)
      print(cnt)
      ```
      
      </div>
      </details>