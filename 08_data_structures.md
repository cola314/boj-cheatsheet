# 08 고급 자료구조 (Advanced Data Structures)

> 실제 코드 기반: 1717, 2042, 10999, 11438, 13510, 14725, 13547

---

## Union-Find (경로압축 + 랭크/크기)
**출처: 1717 (집합의 표현)**

```cpp
const int MAX_SIZE = 1000010;
int uni[MAX_SIZE];  // 음수 = 루트 (절댓값이 집합 크기)

int root(int x) {
    if (uni[x] < 0) return x;
    return uni[x] = root(uni[x]);  // 경로 압축
}

bool isConnected(int a, int b) { return root(a) == root(b); }

int size(int a) { return -uni[root(a)]; }

void connect(int a, int b) {
    a = root(a); b = root(b);
    if (a == b) return;
    // 크기 기반 union (작은 쪽을 큰 쪽에 붙이기)
    if (size(a) < size(b)) { a ^= b; b ^= a; a ^= b; }  // swap
    uni[a] += uni[b];  // 크기 합산
    uni[b] = a;        // b의 루트를 a로
}

// 초기화: for (int i = 0; i <= n; i++) uni[i] = -1;
```

- `uni[i] = -1` 초기화 (각자 크기 1인 독립 집합)
- 빈도: 8개 파일

---

## 세그먼트 트리 (기본, 반복문)
**출처: 2042 (구간 합 구하기)**

```cpp
// base = 2의 거듭제곱 (배열 크기 이상)
int base;
vi seg;  // 크기 base * 2

void update(int x, int value) {
    x = x + base;
    seg[x] = value;
    for (x >>= 1; x >= 1; x >>= 1)
        seg[x] = seg[x*2] + seg[x*2+1];
}

int get_sum(int x, int y) {  // [x, y] 구간 합
    x += base; y += base;
    int ret = 0;
    while (x < y) {
        if (x % 2 == 1) ret += seg[x++];
        if (y % 2 == 0) ret += seg[y--];
        x >>= 1; y >>= 1;
    }
    if (x == y) ret += seg[x];
    return ret;
}

// 초기화
for (base = 1; base < MAX; base <<= 1);
seg.resize(base * 2);
```

- 인덱스 0-based (x + base로 리프 접근)
- 빈도: 11개 파일

---

## 세그먼트 트리 with Lazy Propagation
**출처: 10999 (구간 합 구하기 2)**

```cpp
vector<ll> tree, lazy, arr;

ll init(int node, int s, int e) {
    if (s == e) return tree[node] = arr[s];
    int mid = (s + e) / 2;
    return tree[node] = init(node*2, s, mid) + init(node*2+1, mid+1, e);
}

void push_down(int node, int s, int e) {  // update_lazy
    if (lazy[node] != 0) {
        tree[node] += (ll)(e - s + 1) * lazy[node];
        if (s != e) {
            lazy[node*2] += lazy[node];
            lazy[node*2+1] += lazy[node];
        }
        lazy[node] = 0;
    }
}

void update_range(int node, int s, int e, int l, int r, ll diff) {
    push_down(node, s, e);
    if (r < s || e < l) return;
    if (l <= s && e <= r) {
        tree[node] += (ll)(e - s + 1) * diff;
        if (s != e) { lazy[node*2] += diff; lazy[node*2+1] += diff; }
        return;
    }
    int mid = (s + e) / 2;
    update_range(node*2, s, mid, l, r, diff);
    update_range(node*2+1, mid+1, e, l, r, diff);
    tree[node] = tree[node*2] + tree[node*2+1];
}

ll sum(int node, int s, int e, int l, int r) {
    push_down(node, s, e);
    if (r < s || e < l) return 0;
    if (l <= s && e <= r) return tree[node];
    int mid = (s + e) / 2;
    return sum(node*2, s, mid, l, r) + sum(node*2+1, mid+1, e, l, r);
}

// tree.resize(4*n); lazy.resize(4*n);
// init(1, 0, n-1);
// update_range(1, 0, n-1, l, r, diff);
// sum(1, 0, n-1, l, r);
```

- lazy 배열 초기값 0 (tree는 `resize`로 0 초기화)
- 빈도: 8개 파일

---

## LCA (이진 리프팅, Sparse Table)
**출처: 11438 (LCA 2)**

```cpp
const int MAXD = 18;  // log2(100000) < 17
int depth[MAX], parent[MAX][MAXD];
vvi graph(MAX);

void dfs(int x, int d) {
    depth[x] = d;
    for (int y : graph[x]) {
        if (depth[y]) continue;
        dfs(y, d + 1);
        parent[y][0] = x;
    }
}

// sparse table 구성
for (int i = 1; i < MAXD; i++)
    for (int j = 0; j < n; j++)
        parent[j][i] = parent[parent[j][i-1]][i-1];

int lca(int x, int y) {
    if (depth[x] < depth[y]) swap(x, y);
    // 깊이 맞추기
    for (int i = MAXD-1; i >= 0; i--)
        if (depth[x] - depth[y] >= (1 << i))
            x = parent[x][i];
    if (x == y) return x;
    // 같이 올라가기
    for (int i = MAXD-1; i >= 0; i--)
        if (parent[x][i] != parent[y][i]) {
            x = parent[x][i];
            y = parent[y][i];
        }
    return parent[x][0];
}

// dfs(0, 1);  // root=0, depth 1부터 시작 (0이면 미방문과 구분 불가)
```

- `depth[0] = 1` (루트)로 시작해야 미방문(0) 체크 가능
- MAXD = 18이면 N <= 200000 커버
- 빈도: 4개 파일

---

## HLD (Heavy-Light Decomposition)
**출처: 13510 (트리와 쿼리 1)**

```cpp
int parent[MAX], depth[MAX], heavy[MAX], head[MAX], id[MAX], ID;
int values[MAX];  // 정점/간선 값
vector<vii> graph(MAX);  // graph[x] = {(y, weight), ...}

int dfs(int x) {  // subtree size 반환, heavy child 결정
    int size = 1, msize = 0;
    for (auto [y, d] : graph[x]) {
        if (y == parent[x]) continue;
        values[y] = d; parent[y] = x; depth[y] = depth[x] + 1;
        int ysize = dfs(y);
        size += ysize;
        if (ysize > msize) { msize = ysize; heavy[x] = y; }
    }
    return size;
}

void decompose(int x, int h) {
    head[x] = h; id[x] = ++ID;
    if (heavy[x]) decompose(heavy[x], h);  // heavy child 먼저
    for (auto [y, w] : graph[x])
        if (y != parent[x] && y != heavy[x])
            decompose(y, y);  // light child: 새 체인 시작
}

int query(int a, int b) {  // 경로 최댓값
    int res = 0;
    for (; head[a] != head[b]; b = parent[head[b]]) {
        if (depth[head[a]] > depth[head[b]]) swap(a, b);
        res = max(res, getMax(id[head[b]], id[b]));  // 세그트리 쿼리
    }
    if (depth[a] > depth[b]) swap(a, b);
    if (id[a] != id[b]) res = max(res, getMax(id[a]+1, id[b]));
    return res;
}
```

- 간선 값은 자식 정점에 저장 (루트 제외)
- `id[a]+1`: 간선 쿼리 시 LCA 자체 제외

---

## 트라이 (Trie)
**출처: 14725 (개미굴)**

```cpp
struct Trie {
    string s;
    map<string, Trie*> child;  // 문자열 키 (알파벳 키면 array<Trie*, 26> 권장)
    Trie() : Trie("") {}
    Trie(string s) : s(s) {}

    void insert(int idx, vector<string>& v) {
        if (idx == sz(v)) return;
        if (child.find(v[idx]) == child.end())
            child[v[idx]] = new Trie(v[idx]);
        child[v[idx]]->insert(idx + 1, v);
    }

    void tour(int d) {  // DFS 출력
        for (auto it : child) {
            for (int i = 0; i < d; i++) cout << "--";
            cout << it.first << endl;
            it.second->tour(d + 1);
        }
    }
};
// Trie* T = new Trie(); T->insert(0, v); T->tour(0);
```

- `map` 사용 시 자동 정렬 (사전순 출력)
- 일반 문자 트라이는 `array<Trie*,26> child = {}` 더 빠름
- 빈도: 3개 파일

---

## Mo's Algorithm
**출처: 13547 (수열과 쿼리 5)**

```cpp
int sq, cur;
int val[MAX], cnt[MAX_N], result[MAX];
vector<iii> v;  // {쿼리idx, {l, r}}

// 정렬: 블록 기준 l, 같은 블록이면 r 오름차순
sq = sqrt(n);
sort(all(v), [](auto& a, auto& b) {
    return (a.se.fi / sq < b.se.fi / sq) ||
           (a.se.fi / sq == b.se.fi / sq && a.se.se < b.se.se);
});

// 첫 쿼리 초기화
a = v[0].se.fi; b = v[0].se.se;
for (int i = a; i <= b; i++) if (cnt[val[i]]++ == 0) cur++;
result[v[0].fi] = cur;

// 이후 쿼리 처리
for (int i = 1; i < m; i++) {
    while (v[i].se.fi < a) if (cnt[val[--a]]++ == 0) cur++;
    while (v[i].se.fi > a) if (--cnt[val[a++]] == 0) cur--;
    while (v[i].se.se < b) if (--cnt[val[b--]] == 0) cur--;
    while (v[i].se.se > b) if (cnt[val[++b]]++ == 0) cur++;
    result[v[i].fi] = cur;
}
```

- 시간복잡도: O((N + Q) * sqrt(N))
- 쿼리 순서 저장 필수 (`result[원래인덱스]`)
- 홀짝 최적화: 홀수 블록은 r 내림차순 정렬

---

## 주의점 요약

| 자료구조 | 주의 |
|----------|------|
| Union-Find | `uni[i] = -1` 초기화, swap 시 XOR 트릭 |
| 세그트리(반복) | `base = 2^k >= N`, `seg.resize(base*2)` |
| Lazy | `push_down` 항상 먼저, tree 크기 4*N |
| LCA | depth 루트=1 (0이면 미방문 구분 불가) |
| HLD | 간선값은 자식 정점에, query시 LCA 제외 |
| Trie | new로 동적 할당, map은 정렬되지만 느림 |
| Mo's | 쿼리 원래 인덱스 보존, result 배열 별도 |
