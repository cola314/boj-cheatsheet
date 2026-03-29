# 그래프 알고리즘 치트시트

> 실제 코드 기반 | 343개 파일 분석 | adj_list: 263회, visited: 28회, dijkstra: 3회, topological: 4회, scc: 4회

---

## 헤더 & 공통 매크로

```cpp
#include <bits/stdc++.h>
#define INF 987654321
#define fastio ios_base::sync_with_stdio(false); cin.tie(nullptr);
#define pb push_back
#define sz(x) ((int)(x).size())
using namespace std;
typedef pair<int,int> ii;
typedef vector<int> vi;
typedef vector<ii> vii;
typedef vector<vi> vvi;
```

---

## 1. 인접 리스트 구성 (263회 — 압도적 1위)

```cpp
// 가중치 없는 그래프
vvi graph;
graph.resize(n + 1);
graph[a].pb(b);
graph[b].pb(a);   // 양방향이면 양쪽 추가

// 가중치 있는 그래프 (다익스트라 등)
vector<vector<ii>> G;   // G[u] = {(v, cost), ...}
G.resize(v + 1);
G[a].pb(ii(b, c));      // 단방향
```

---

## 2. DFS (재귀) (1260 DFS+BFS)

```cpp
vi dfs_num;   // 방문 배열

void dfs(vvi& g, int v) {
    if (dfs_num[v] == 1) return;   // 방문 체크
    dfs_num[v] = 1;
    cout << v << " ";
    for (int i = 0; i < g[v].size(); i++) {
        dfs(g, g[v][i]);
    }
}

// 초기화 & 호출
dfs_num.resize(n + 1, 0);
for (int i = 0; i <= n; i++) sort(all(graph[i]));  // 번호 오름차순 방문
dfs(graph, v);
```

---

## 3. BFS 기본 (1260 DFS+BFS, 2178 미로)

```cpp
// 기본 BFS — 최단 거리
int bfs_num[110][110];   // 방문 & 거리 겸용
queue<iii> Q;            // {거리, {x, y}}

int dx[] = { 1, -1, 0, 0 };
int dy[] = { 0, 0, 1, -1 };

Q.push(make_pair(1, make_pair(1, 1)));
bfs_num[1][1] = 1;

while (!Q.empty()) {
    iii t = Q.front(); Q.pop();
    if (t.se.fi == n && t.se.se == m) {
        cout << t.fi;   // 목적지 도달 거리 출력
    }
    for (int i = 0; i < 4; i++) {
        int nx = dx[i] + t.se.fi;
        int ny = dy[i] + t.se.se;
        if (nx < 1 || ny < 1 || nx > n || ny > m) continue;
        if (bfs_num[nx][ny] == 0 && arr[nx][ny] == '1') {
            bfs_num[nx][ny] = 1;
            Q.push(make_pair(t.fi + 1, make_pair(nx, ny)));
        }
    }
}
```

### BFS 핵심 원칙
- 방문 표시는 **큐에 넣을 때** — pop 할 때 하면 중복 삽입 발생
- 4방향: `dx[] = {1,-1,0,0}`, `dy[] = {0,0,1,-1}`
- 8방향: `dx[] = {1,-1,0,0,1,1,-1,-1}`, `dy[] = {0,0,1,-1,1,-1,1,-1}`

---

## 4. 다중 소스 BFS (7576 토마토)

```cpp
// 초기화: 모든 시작점을 큐에 미리 삽입
for (int i = 1; i <= m; i++)
    for (int j = 1; j <= n; j++) {
        target += (arr[i][j] == 0);
        if (arr[i][j] == 1)
            Q.push(make_pair(0, make_pair(i, j)));  // 거리=0으로 시작
    }

int day = 0, cur = 0;
while (!Q.empty()) {
    iii t = Q.front(); Q.pop();
    day = max(day, t.fi);
    for (int i = 0; i < 4; i++) {
        int nx = dx[i] + t.se.fi;
        int ny = dy[i] + t.se.se;
        if (범위 초과) continue;
        if (arr[nx][ny] == 0) {
            arr[nx][ny] = 1;   // 방문 표시로 arr 자체 수정
            cur++;
            Q.push(make_pair(t.fi + 1, make_pair(nx, ny)));
        }
    }
}
if (cur == target) cout << day;
else cout << -1;
```

> 핵심: 별도 visited 배열 없이 `arr` 값을 1로 바꿔 방문 표시.

---

## 5. 3D BFS — 상태 추가 (2206 벽부수기)

```cpp
// 상태: {거리, x, y, 벽파괴여부}
struct MY { int d, x, y, b; };
int bfs_num[1010][1010][2];   // [x][y][벽파괴사용여부]

Q.push({ 1, 1, 1, 0 });
bfs_num[1][1][0] = 1;

while (!Q.empty()) {
    MY t = Q.front(); Q.pop();
    for (int i = 0; i < 4; i++) {
        int nx = t.x + dx[i], ny = t.y + dy[i];
        if (범위초과) continue;

        // 일반 이동 (빈 칸)
        if (arr[nx][ny] == 0 && bfs_num[nx][ny][t.b] == 0) {
            bfs_num[nx][ny][t.b] = t.d + 1;
            Q.push({ t.d + 1, nx, ny, t.b });
        }
        // 벽 부수기 (아직 안 부쉈을 때만)
        if (arr[nx][ny] == 1 && t.b == 0 && bfs_num[nx][ny][1] == 0) {
            bfs_num[nx][ny][1] = t.d + 1;
            Q.push({ t.d + 1, nx, ny, 1 });
        }
    }
}
// 두 상태 중 최솟값
int ans = min(bfs_num[n][m][0], bfs_num[n][m][1]);
cout << (ans == INF ? -1 : ans);
```

> 3D BFS 패턴: 기존 2D에 **"상태 차원"** 추가. visited도 상태에 따라 분리.

---

## 6. 다익스트라 (1753)

```cpp
vi Dijkstra(vector<vector<ii>>& graph, int s) {
    vi dis(graph.size() + 1, INF);
    priority_queue<ii, vii, greater<ii>> Q;   // 최소힙: {거리, 노드}
    dis[s] = 0;
    Q.push(ii(0, s));

    while (!Q.empty()) {
        ii t = Q.top(); Q.pop();
        int d = t.first, u = t.second;
        if (d > dis[u]) continue;   // 이미 더 짧은 경로 처리됨 — 스킵

        for (auto& e : graph[u]) {
            int tu = e.first, td = e.second;   // 목적지, 가중치
            if (d + td < dis[tu]) {
                dis[tu] = d + td;
                Q.push(ii(dis[tu], tu));
            }
        }
    }
    return dis;
}

// 사용
G.resize(v + 1);
G[a].pb(ii(b, c));   // 단방향 유향 그래프
vi dis = Dijkstra(G, s);
```

### 다익스트라 핵심
- `priority_queue<ii, vii, greater<ii>>` — 최소힙 필수
- `if (d > dis[u]) continue` — 낡은 거리값 처리 스킵 (핵심 최적화)
- 음수 간선 불가 → 음수 있으면 벨만포드

---

## 7. 플로이드-워셜 (11404)

```cpp
// 초기화
vvi dis(n + 1, vi(n + 1, INF));
for (int i = 1; i <= n; i++) dis[i][i] = 0;   // 자기 자신 = 0

// 간선 입력
cin >> a >> b >> c;
dis[a][b] = min(dis[a][b], c);   // 중복 간선: 최솟값 유지

// 3중 for (k=경유 노드)
for (int k = 1; k <= n; k++)
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            if (dis[i][k] + dis[k][j] < dis[i][j])
                dis[i][j] = dis[i][k] + dis[k][j];

// 출력: INF는 0으로
cout << (dis[i][j] == INF ? 0 : dis[i][j]);
```

> 시간복잡도: O(V³) | 모든 쌍 최단거리 | N≤500 수준에서 사용

---

## 8. 위상정렬 (2252)

```cpp
vvi Adj;
vi indegree;
queue<int> Q;

Adj.resize(n + 1);
indegree.resize(n + 1, 0);
cin >> a >> b;
Adj[a].pb(b);
indegree[b]++;

// 진입차수 0인 노드 모두 큐에 삽입
for (int i = 1; i <= n; i++)
    if (indegree[i] == 0) Q.push(i);

while (!Q.empty()) {
    int t = Q.front(); Q.pop();
    cout << t << " ";
    for (int next : Adj[t]) {
        indegree[next]--;
        if (indegree[next] == 0) Q.push(next);
    }
}
```

> 사이클 존재 시 큐가 비어도 모든 노드 처리 안 됨 — 사이클 감지에 응용 가능.

---

## 9. SCC — 타잔 알고리즘 (2150)

```cpp
int n, m, id;
vi d(MAX), finished(MAX);
vvi graph(MAX);
vector<pair<int, vi>> SCC;
stack<int> St;

int dfs(int x) {
    d[x] = ++id;      // 방문 순서 부여
    St.push(x);
    int parent = d[x];

    FOR(i, sz(graph[x])) {
        int y = graph[x][i];
        if (!d[y])           parent = min(parent, dfs(y));         // 미방문
        else if (!finished[y]) parent = min(parent, d[y]);          // 방문했지만 미완료
    }

    // 루트 노드: parent == d[x] → SCC 확정
    if (parent == d[x]) {
        vi scc;
        while (1) {
            int t = St.top(); St.pop();
            scc.pb(t);
            finished[t] = 1;
            if (d[t] == parent) break;
        }
        sort(all(scc));
        SCC.pb(mp(scc[0], scc));   // 정렬 후 저장
    }
    return parent;
}

// 모든 노드에서 미방문이면 dfs 시작
for (int i = 1; i <= n; i++)
    if (d[i] == 0) dfs(i);

sort(all(SCC));   // SCC를 사전순 정렬
cout << sz(SCC) << endl;
```

### 타잔 vs 코사라주

| 알고리즘 | 구현 | DFS 횟수 |
|----------|------|----------|
| 타잔 | 재귀 DFS 1번 | 1회 |
| 코사라주 | 역방향 그래프 + DFS | 2회 |

---

## 알고리즘 선택 가이드

| 상황 | 알고리즘 | 복잡도 |
|------|----------|--------|
| 단일 출발 최단거리, 양수 간선 | 다익스트라 | O((V+E) log V) |
| 단일 출발, 음수 간선 | 벨만포드 | O(VE) |
| 모든 쌍 최단거리 | 플로이드-워셜 | O(V³) |
| 순서 결정 / DAG | 위상정렬 | O(V+E) |
| 강연결 요소 분리 | 타잔/코사라주 | O(V+E) |
| 미로 최단거리 | BFS | O(V+E) |
| 연결 요소 탐색 | DFS | O(V+E) |

---

## 주의점 & 자주 하는 실수

- **방문 표시 시점**: 큐에 넣을 때 (`push` 직후) — pop 후 하면 중복 삽입
- **다익스트라 스킵 조건**: `if (d > dis[u]) continue` 빠뜨리면 TLE
- **플로이드 k 루프 가장 바깥**: `k → i → j` 순서 — 다른 순서면 틀림
- **위상정렬 사이클**: 결과 노드 수 < n이면 사이클 존재
- **타잔 finished 배열**: 스택에 있는데 방문된 노드 구분용 — 없으면 오답
- **3D BFS 배열 크기**: `bfs_num[1010][1010][2]` — 상태 차원 확인
- **다중 소스 BFS**: 모든 소스를 거리=0으로 초기에 큐 삽입
- **INF + INF 오버플로우**: 플로이드에서 `dis[i][k] != INF` 조건 체크 권장
