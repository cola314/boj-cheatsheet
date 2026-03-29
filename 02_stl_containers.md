# STL 컨테이너 총정리 치트시트

> 내 코드 343개 분석 기준 (2026-03)

---

## 내 코드에서 뽑은 패턴 (빈도수)

| 컨테이너 | 빈도 |
|----------|------|
| `vector` | 269 / 343 |
| `pair` | 268 / 343 |
| `queue` | 25 / 343 |
| `priority_queue` | 20 / 343 |
| `stack` | 18 / 343 |
| `map` | 11 / 343 |
| `set` | 2 / 343 |
| `deque` | 소수 |
| `list` | 소수 |

---

## vector (빈도: 269)

### 내 코드 패턴 (18870번, 1181번)

```cpp
// typedef / using 선언 (표준 템플릿에 포함)
using vi = vector<int>;
using vii = vector<ii>;   // ii = pair<int,int>

vi v;
v.push_back(3);          // pb 매크로로 단축: v.pb(3)
v.pb(3);                 // #define pb push_back

// 좌표압축 (18870번) — 실제 코드
vi idx, v;
sort(all(idx));                           // all(x) = x.begin(),x.end()
idx.erase(unique(all(idx)), idx.end());   // 중복 제거
lower_bound(all(idx), v[i]) - idx.begin(); // 압축값
```

### 메서드 정리

| 메서드 | 설명 | 반환 |
|--------|------|------|
| `push_back(val)` | 끝에 추가 | void |
| `pop_back()` | 끝 제거 | void |
| `back()` | 마지막 원소 참조 | T& |
| `front()` | 첫 원소 참조 | T& |
| `size()` | 크기 | size_t |
| `empty()` | 비어있으면 true | bool |
| `clear()` | 전체 삭제 | void |
| `resize(n)` | 크기 변경 | void |
| `resize(n, val)` | 크기 변경 + 초기값 | void |
| `assign(n, val)` | n개로 초기화 | void |
| `erase(it)` | 이터레이터 위치 삭제 | iterator |
| `erase(it1, it2)` | 범위 삭제 | iterator |
| `begin()` / `end()` | 이터레이터 | iterator |
| `[i]` | 인덱스 접근 | T& |

---

## pair (빈도: 268)

### 내 코드 패턴 (2108번, 11650번)

```cpp
// typedef / using 선언 (표준 템플릿에 포함)
using ii = pair<int, int>;
using iii = pair<pair<int, int>, int>;
using vii = vector<ii>;

// 생성
ii p = {1, 2};
ii p = make_pair(1, 2);   // mp 매크로: mp(1, 2)
ii p = mp(1, 2);

// 접근
p.first;    // fi 매크로: p.fi
p.second;   // se 매크로: p.se

// 2108번: map 결과를 pair로 변환 후 정렬
vii vv;
for (auto x : M)
    vv.pb(mp(x.se, -x.fi));   // {빈도, -값} 으로 변환
sort(all(vv));                 // 빈도 오름차순, 같으면 -값 오름차순 (=값 내림차순)
```

### pair 정렬 규칙

```cpp
// pair는 first 기준 정렬, 같으면 second 기준
vector<ii> v = {{1,3},{1,2},{2,1}};
sort(all(v));  // {1,2}, {1,3}, {2,1}

// 내림차순
sort(all(v), greater<ii>());

// 커스텀: first 오름차순, second 내림차순
sort(all(v), [](ii a, ii b){
    if (a.fi != b.fi) return a.fi < b.fi;
    return a.se > b.se;
});
```

---

## stack (빈도: 18)

### 내 코드 패턴 (28278번)

```cpp
stack<int> st;

// 28278번: 실제 사용 패턴
cin >> a;
if (a == 1) { cin >> b; st.push(b); }
if (a == 2) {
    if (!st.empty()) { cout << st.top() << endl; st.pop(); }
    else cout << -1 << endl;
}
if (a == 3) cout << sz(st) << endl;    // sz(x) = x.size()
if (a == 4) cout << (sz(st) == 0) << endl;
if (a == 5) {
    if (!st.empty()) cout << st.top() << endl;
    else cout << -1 << endl;
}
```

### 메서드 정리

| 메서드 | 설명 | 반환 |
|--------|------|------|
| `push(val)` | 맨 위에 추가 | void |
| `pop()` | 맨 위 제거 | **void** |
| `top()` | 맨 위 원소 | T& |
| `size()` | 크기 | size_t |
| `empty()` | 비어있으면 true | bool |

> **주의**: `pop()`은 void! 값을 꺼내려면 `top()` 후 `pop()` 순서로.

---

## queue (빈도: 25)

### 내 코드 패턴 (18258번)

```cpp
queue<int> Q;

// 18258번: 실제 사용 패턴
if (line[1] == 'o') {       // "pop"
    if (Q.empty()) cout << -1 << "\n";
    else { cout << Q.front() << "\n"; Q.pop(); }
}
else if (line[0] == 'p') {  // "push"
    cin >> num; Q.push(num);
}
else if (line[0] == 'f') cout << (Q.empty() ? -1 : Q.front()) << "\n";
else if (line[0] == 'b') cout << (Q.empty() ? -1 : Q.back()) << "\n";
```

### 메서드 정리

| 메서드 | 설명 | 반환 |
|--------|------|------|
| `push(val)` | 뒤에 추가 | void |
| `pop()` | 앞 제거 | **void** |
| `front()` | 앞 원소 | T& |
| `back()` | 뒤 원소 | T& |
| `size()` | 크기 | size_t |
| `empty()` | 비어있으면 true | bool |

> **주의**: `pop()`은 void! stack과 동일.

---

## deque (빈도: 소수)

### 내 코드 패턴 (28279번)

```cpp
deque<int> dq;

// 28279번: 앞/뒤 모두 push/pop
dq.push_front(b);   // 앞에 추가
dq.pb(b);           // 뒤에 추가 (push_back)
dq.pop_front();     // 앞 제거
dq.pop_back();      // 뒤 제거
dq.front();         // 앞 원소
dq.back();          // 뒤 원소
```

### 메서드 정리

| 메서드 | 설명 |
|--------|------|
| `push_front(val)` | 앞에 추가 |
| `push_back(val)` | 뒤에 추가 |
| `pop_front()` | 앞 제거 (void) |
| `pop_back()` | 뒤 제거 (void) |
| `front()` | 앞 원소 |
| `back()` | 뒤 원소 |
| `[i]` | 인덱스 접근 가능 (vector처럼) |

---

## priority_queue (빈도: 20)

### 내 코드 패턴 (2696번)

```cpp
// 최대 힙 (기본)
priority_queue<int> maxq;
maxq.push(t);
maxq.top();    // 최댓값
maxq.pop();    // 최댓값 제거

// 최소 힙
priority_queue<int, vector<int>, greater<int>> minq;
minq.push(t);
minq.top();    // 최솟값

// 2696번: 중앙값 유지 (최대힙 + 최소힙)
if (maxq.size() <= minq.size()) maxq.push(t);
else minq.push(t);
// 균형 맞추기
if (!minq.empty() && maxq.top() > minq.top()) {
    int a = maxq.top(); maxq.pop();
    int b = minq.top(); minq.pop();
    maxq.push(b); minq.push(a);
}
```

### pair priority_queue (우선순위 큐 + pair)

```cpp
// {거리, 노드} — 거리 최소 힙 (다익스트라)
priority_queue<ii, vector<ii>, greater<ii>> pq;
pq.push({dist, node});
auto [d, v] = pq.top(); pq.pop();

// {-거리, 노드} — 최대힙으로 최소 시뮬레이션
priority_queue<ii> pq;
pq.push({-dist, node});
```

### 메서드 정리

| 메서드 | 설명 | 반환 |
|--------|------|------|
| `push(val)` | 삽입 | void |
| `pop()` | 최댓값(최솟값) 제거 | **void** |
| `top()` | 최댓값(최솟값) 조회 | const T& |
| `size()` | 크기 | size_t |
| `empty()` | 비어있으면 true | bool |

---

## map (빈도: 11)

### 내 코드 패턴 (1620번, 7785번, 2108번)

```cpp
// 1620번: string ↔ int 양방향 매핑
map<string, string> M;
M[s] = to_string(i);
M[to_string(i)] = s;
cout << M[s];

// 7785번: 빈도 카운팅
map<string, int> M;
if (b == "enter") M[a]++;
else M[a]--;
// 순회 (map은 key 기준 정렬됨)
for (auto [a, b] : M)
    if (b > 0) ans.pb(a);

// 2108번: 최빈값 구하기
map<int, int> M;
for (auto x : v) M[x]++;
```

### 메서드 정리

| 메서드 | 설명 |
|--------|------|
| `m[key]` | 접근 (없으면 0/default 생성) |
| `m.find(key)` | 이터레이터 반환 (없으면 end()) |
| `m.count(key)` | 존재하면 1, 없으면 0 |
| `m.insert({key, val})` | 삽입 |
| `m.erase(key)` | 삭제 |
| `m.size()` | 크기 |
| `m.empty()` | 비어있으면 true |

> **주의**: `m[key]` 접근 시 key가 없으면 **자동 생성**됨 (0 또는 default)

---

## set (빈도: 2)

### 기본 사용

```cpp
set<int> s;
s.insert(3);
s.erase(3);
s.count(3);    // 있으면 1, 없으면 0
s.find(3);     // iterator, 없으면 s.end()
// 자동 정렬, 중복 제거

// 내림차순 set
set<int, greater<int>> s;
```

---

## list (빈도: 소수)

### 내 코드 패턴 (2346번 — 풍선 게임)

```cpp
list<ii> L;   // ii = pair<int,int>
FOR(i, n) L.push_back(mp(i+1, t));

auto it = L.begin();
// 삭제 후 다음 이터레이터 받기
it = L.erase(it);
if (it == L.end()) it = L.begin();

// 앞으로 이동
while (--t) {
    if (++it == L.end()) it = L.begin();
}
// 뒤로 이동
while (t--) {
    if (it == L.begin()) it = L.end();
    --it;
}
```

### 메서드 정리

| 메서드 | 설명 |
|--------|------|
| `push_back(val)` | 뒤에 추가 |
| `push_front(val)` | 앞에 추가 |
| `pop_back()` | 뒤 제거 (void) |
| `pop_front()` | 앞 제거 (void) |
| `erase(it)` | 이터레이터 위치 삭제, 다음 iterator 반환 |
| `insert(it, val)` | it 앞에 삽입 |
| `begin()` / `end()` | 이터레이터 |

> **주의**: list는 `[i]` 인덱스 접근 불가! 이터레이터로만 접근.

---

## unordered_map (참고)

```cpp
// map보다 빠름 (O(1) 평균), 정렬 안 됨
unordered_map<string, int> um;
um["hello"]++;
// map과 동일한 메서드 사용
```

---

## 컨테이너 비교표

| 컨테이너 | 정렬 | 중복 | 접근 | 삽입/삭제 | 특징 |
|----------|------|------|------|-----------|------|
| `vector` | X | O | O(1) 인덱스 | O(1) 끝, O(n) 중간 | 가장 많이 사용 |
| `deque` | X | O | O(1) 인덱스 | O(1) 양 끝 | 앞뒤 모두 빠름 |
| `list` | X | O | O(n) 순회만 | O(1) 어디서든 | 이터레이터 이동 필요 |
| `stack` | X | O | top만 | O(1) | LIFO |
| `queue` | X | O | front/back | O(1) | FIFO |
| `priority_queue` | 자동 | O | top만 | O(log n) | 최대/최소 힙 |
| `set` | O | X | O(log n) | O(log n) | 정렬+중복제거 |
| `map` | O | X (key) | O(log n) | O(log n) | key-value |
| `unordered_map` | X | X (key) | O(1) 평균 | O(1) 평균 | 해시맵 |

---

## 주의할 점 & 실수하기 쉬운 것

| 상황 | 실수 | 올바른 방법 |
|------|------|------------|
| `stack::pop()` | 반환값 있다고 착각 | `top()` 먼저, 그 다음 `pop()` |
| `queue::pop()` | 반환값 있다고 착각 | `front()` 먼저, 그 다음 `pop()` |
| `map[key]` | 존재 확인 전 접근 | `count(key)` 또는 `find(key)` 먼저 |
| empty() 미확인 | 빈 컨테이너 접근 → UB | 반드시 `!empty()` 확인 |
| `list` 인덱스 접근 | `l[i]` → 컴파일 에러 | 이터레이터 사용 |
| `priority_queue` 최솟값 | 기본이 최대힙 | `greater<T>` 명시 |
| `set` 중복 삽입 | 무시됨 (오류 아님) | `count()` 로 확인 가능 |
| `size()` 반환형 | `size_t` (unsigned) | `sz(x)` 매크로로 `int` 변환 |
