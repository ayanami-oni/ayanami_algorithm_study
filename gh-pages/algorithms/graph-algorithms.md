# 图算法

图算法是计算机科学中用于处理图结构数据的核心算法，图由顶点（Vertex）和边（Edge）组成。

## 图的基础

### 图的分类

| 类型 | 特点 | 典型应用 |
|------|------|----------|
| 无向图 | 边没有方向 | 社交网络好友关系 |
| 有向图 | 边有方向 | 网页链接、任务依赖 |
| 加权图 | 边有权重 | 路线规划、网络路由 |
| 无权图 | 边无权重 | 二进制关系 |

### 图的表示方法

**邻接矩阵**

```python
# 邻接矩阵表示
graph = [
    [0, 1, 0, 1],
    [1, 0, 1, 1],
    [0, 1, 0, 0],
    [1, 1, 0, 0]
]
```

**邻接表**

```python
# 邻接表表示
graph = {
    0: [1, 3],
    1: [0, 2, 3],
    2: [1],
    3: [0, 1]
}
```

---

## 图的遍历

### BFS（广度优先搜索）

```python
from collections import deque

def bfs(graph, start):
    visited = set()
    queue = deque([start])
    visited.add(start)
    
    while queue:
        vertex = queue.popleft()
        print(vertex, end=' ')
        
        for neighbor in graph[vertex]:
            if neighbor not in visited:
                visited.add(neighbor)
                queue.append(neighbor)
```

### DFS（深度优先搜索）

```python
def dfs(graph, start, visited=None):
    if visited is None:
        visited = set()
    
    visited.add(start)
    print(start, end=' ')
    
    for neighbor in graph[start]:
        if neighbor not in visited:
            dfs(graph, neighbor, visited)
```

### BFS vs DFS

| 特性 | BFS | DFS |
|------|-----|-----|
| 数据结构 | 队列 | 栈 |
| 空间复杂度 | O(V) | O(V) |
| 最短路径 | ✅ 支持 | ❌ 不支持 |
| 深度探索 | ❌ 不支持 | ✅ 支持 |

---

## 最短路径算法

### Dijkstra 算法

适用于非负权重的单源最短路径。

```python
import heapq
import math

def dijkstra(graph, start):
    dist = {v: math.inf for v in graph}
    dist[start] = 0
    pq = [(0, start)]
    
    while pq:
        d, u = heapq.heappop(pq)
        
        if d > dist[u]:
            continue
        
        for v, weight in graph[u]:
            if dist[u] + weight < dist[v]:
                dist[v] = dist[u] + weight
                heapq.heappush(pq, (dist[v], v))
    
    return dist
```

### Bellman-Ford 算法

可以处理负权重边，检测负环。

```python
def bellman_ford(graph, start):
    dist = {v: math.inf for v in graph}
    dist[start] = 0
    
    # 松弛所有边 V-1 次
    for _ in range(len(graph) - 1):
        for u in graph:
            for v, weight in graph[u]:
                if dist[u] + weight < dist[v]:
                    dist[v] = dist[u] + weight
    
    # 检测负环
    for u in graph:
        for v, weight in graph[u]:
            if dist[u] + weight < dist[v]:
                return "负环存在"
    
    return dist
```

### Floyd-Warshall 算法

多源最短路径算法。

```python
def floyd_warshall(graph, vertices):
    dist = [[math.inf] * vertices for _ in range(vertices)]
    
    for i in range(vertices):
        dist[i][i] = 0
    
    for u in graph:
        for v, w in graph[u]:
            dist[u][v] = w
    
    for k in range(vertices):
        for i in range(vertices):
            for j in range(vertices):
                if dist[i][k] + dist[k][j] < dist[i][j]:
                    dist[i][j] = dist[i][k] + dist[k][j]
    
    return dist
```

---

## 最小生成树

### Prim 算法

贪心算法，从一个顶点开始，逐步扩展。

```python
def prim(graph, start):
    mst = []
    visited = {start}
    edges = [(w, start, v) for v, w in graph[start]]
    heapq.heapify(edges)
    
    while edges:
        w, u, v = heapq.heappop(edges)
        if v not in visited:
            visited.add(v)
            mst.append((u, v, w))
            for nxt, w2 in graph[v]:
                if nxt not in visited:
                    heapq.heappush(edges, (w2, v, nxt))
    
    return mst
```

### Kruskal 算法

按边权重排序，逐条加入不形成环的边。

```python
def kruskal(graph):
    edges = []
    for u in graph:
        for v, w in graph[u]:
            edges.append((w, u, v))
    
    edges.sort()
    parent = {v: v for v in graph}
    
    def find(x):
        if parent[x] != x:
            parent[x] = find(parent[x])
        return parent[x]
    
    mst = []
    for w, u, v in edges:
        if find(u) != find(v):
            mst.append((u, v, w))
            parent[find(u)] = find(v)
    
    return mst
```

---

## 拓扑排序

有向无环图（DAG）的顶点线性排序。

```python
def topological_sort(graph):
    in_degree = {v: 0 for v in graph}
    
    for u in graph:
        for v, _ in graph[u]:
            in_degree[v] += 1
    
    queue = deque([v for v in graph if in_degree[v] == 0])
    result = []
    
    while queue:
        vertex = queue.popleft()
        result.append(vertex)
        
        for neighbor, _ in graph[vertex]:
            in_degree[neighbor] -= 1
            if in_degree[neighbor] == 0:
                queue.append(neighbor)
    
    if len(result) == len(graph):
        return result
    return "图中存在环"
```

---

## 强连通分量

使用 Kosaraju 算法。

```python
def kosaraju(graph, rev_graph):
    visited = set()
    finish_order = []
    
    def dfs1(v):
        visited.add(v)
        for neighbor in graph[v]:
            if neighbor not in visited:
                dfs1(neighbor)
        finish_order.append(v)
    
    for v in graph:
        if v not in visited:
            dfs1(v)
    
    visited.clear()
    sccs = []
    
    def dfs2(v, component):
        visited.add(v)
        component.append(v)
        for neighbor in rev_graph[v]:
            if neighbor not in visited:
                dfs2(neighbor, component)
    
    for v in reversed(finish_order):
        if v not in visited:
            component = []
            dfs2(v, component)
            sccs.append(component)
    
    return sccs
```

---

## 算法复杂度总结

| 算法 | 时间复杂度 | 空间复杂度 | 应用场景 |
|------|-----------|-----------|----------|
| BFS/DFS | O(V + E) | O(V) | 图遍历 |
| Dijkstra | O((V+E)logV) | O(V) | 单源最短路 |
| Bellman-Ford | O(VE) | O(V) | 含负权最短路 |
| Floyd-Warshall | O(V³) | O(V²) | 多源最短路 |
| Prim | O((V+E)logV) | O(V) | 最小生成树 |
| Kruskal | O(ElogE) | O(V) | 最小生成树 |
| Kosaraju | O(V + E) | O(V) | 强连通分量 |
