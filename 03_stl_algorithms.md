# STL 알고리즘 & 유틸리티 치트시트

> 내 코드 343개 분석 기준 (2026-03)

---

## 내 코드에서 뽑은 패턴 (빈도수)

| 패턴 | 빈도 |
|------|------|
| `#define all(x) x.begin(),x.end()` | 274 / 343 |
| `#define pb push_back` | 255 / 343 |
| `sort(all(v))` | 다수 |
| `lower_bound` / `upper_bound` | 다수 |
| `unique` + `erase` | 다수 |
| `accumulate` | 소수 |
| `memset` (`ini` 매크로) | 다수 |
| `gcd` (직접 구현 또는 `__gcd`) | 다수 |
| `next_permutation` | 소수 |

---

## sort (정렬)

### 기본 sort

```cpp
sort(all(v));              // 오름차순 (all(x) = x.begin(),x.end())
sort(all(v), greater<int>()); // 내림차순

// 배열 정렬
sort(arr, arr + n);
sort(arr + 1, arr + n + 1);  // 1-indexed
```

### 커스텀 comparator — 함수 방식 (1181번)

```cpp
// 1181번: 길이 오름차순, 같으면 사전순
bool cmp(string s1, string s2) {
    if (s1.size() == s2.size())
        return s1 < s2;
    return s1.size() < s2.size();
}
sort(V.begin(), V.end(), cmp);
```

### 커스텀 comparator — 람다 방식 (7785번)

```cpp
// 7785번: 역순 정렬
sort(all(ans), greater<string>());

// 람다로 커스텀
sort(all(v), [](ii a, ii b) {
    if (a.fi != b.fi) return a.fi < b.fi;
    return a.se > b.se;  // fi 같으면 se 내림차순
});
```

### pair 정렬 (2108번)

```cpp
// {빈도, -값} 형태로 변환 후 정렬
vii vv;
for (auto x : M)
    vv.pb(mp(x.se, -x.fi));  // {빈도, -값}
sort(all(vv));   // 빈도 오름차순, 빈도 같으면 -값 오름차순 (= 값 내림차순)
```

---

## lower_bound / upper_bound

### 기본 사용

```cpp
// 정렬된 배열/벡터에서만 동작!
vector<int> v = {1, 2, 3, 3, 5};

// lower_bound: val 이상인 첫 번째 위치
auto it = lower_bound(all(v), 3);  // v[2] 를 가리킴
int idx = lower_bound(all(v), 3) - v.begin();  // = 2

// upper_bound: val 초과인 첫 번째 위치
auto it = upper_bound(all(v), 3);  // v[4] 를 가리킴 (값 5)
int idx = upper_bound(all(v), 3) - v.begin();  // = 4

// 값이 존재하는지 확인
if (lower_bound(all(v), x) != v.end() && *lower_bound(all(v), x) == x)
    // x 존재
```

### 좌표압축 (18870번) — 실제 내 코드

```cpp
vi idx, v;
cin >> n;
while (n--) { cin >> t; idx.pb(t); v.pb(t); }

sort(all(idx));
idx.erase(unique(all(idx)), idx.end());   // 중복 제거

for (int i = 0; i < v.size(); i++) {
    cout << lower_bound(all(idx), v[i]) - idx.begin() << " ";
}
// lower_bound 반환 - begin() = 0-indexed 위치 = 압축값
```

### LIS (12015번) — 실제 내 코드

```cpp
int arr[1000010];
for (int i = 0; i <= n; i++) arr[i] = INF;

for (int i = 0; i < n; i++) {
    cin >> num;
    *lower_bound(arr, arr + n, num) = num;  // 이분탐색으로 교체
}
// INF가 아닌 원소 수 = LIS 길이
while (arr[result++] != INF);
cout << result - 1;
```

---

## unique + erase (중복 제거)

### 패턴 (18870번, 1181번)

```cpp
// 반드시 sort 먼저!
sort(all(v));
v.erase(unique(all(v)), v.end());

// unique: 연속된 중복을 뒤로 밀고, 유효 범위의 끝 반환
// erase: unique가 반환한 위치부터 끝까지 삭제
```

> **주의**: `unique`는 **정렬된** 컨테이너에서만 의미 있음. 정렬 없이 쓰면 연속 중복만 제거.

---

## accumulate (합산)

### 내 코드 패턴 (10773번, 2108번)

```cpp
// 10773번: 스택 처럼 쌓고 누적합
#include <numeric>  // bits/stdc++.h 사용 시 불필요
vector<int> V;
printf("%d", accumulate(V.begin(), V.end(), 0));

// 2108번: 평균 계산
cout << int(round(double(accumulate(all(v), 0)) / sz(v)));
```

### 주의사항

```cpp
// int 오버플로우 주의
accumulate(all(v), 0);     // int로 계산 → 오버플로우 가능
accumulate(all(v), 0LL);   // long long으로 계산 → 안전
accumulate(all(v), (ll)0); // 동일
```

---

## memset / fill (초기화)

### 내 코드 패턴

```cpp
// ini 매크로 = memset(x, y, sizeof(x))
#define ini(x, y) memset(x, y, sizeof(x));

ini(arr, 0);    // 0으로 초기화
ini(arr, -1);   // -1로 초기화 (0xFF로 채움 → -1)
ini(visited, 0);

// 직접 memset
memset(dp, 0, sizeof(dp));
memset(dp, 0x3f, sizeof(dp));  // 큰 값으로 초기화 (약 1e9)
```

### fill (안전한 초기화)

```cpp
fill(arr, arr + n, 0);
fill(all(v), -1);

// 2차원
fill(&dp[0][0], &dp[0][0] + 1001 * 1001, 0);
```

| 방법 | 속도 | 안전성 | 주의 |
|------|------|--------|------|
| `memset(arr, 0, sizeof(arr))` | 빠름 | O | 0, -1, 0x3f만 안전 |
| `memset(arr, -1, sizeof(arr))` | 빠름 | O | -1 = 0xFF×4 |
| `fill(arr, arr+n, val)` | 보통 | O | 임의 값 가능 |
| `for` 루프 | 보통 | O | 임의 값 가능 |

---

## gcd / lcm

### 내 코드 패턴 (1934번)

```cpp
// 직접 구현 (내 코드)
int gcd(int a, int b) { return b == 0 ? a : gcd(b, a % b); }

// LCM
cout << a * b / gcd(a, b) << endl;

// C++17 표준 함수
#include <numeric>  // bits/stdc++.h 포함
__gcd(a, b);        // 구버전
gcd(a, b);          // C++17 std::gcd
lcm(a, b);          // C++17 std::lcm
```

> **주의**: `a * b / gcd(a, b)` 에서 `a * b`가 오버플로우 날 수 있음 → `(ll)a * b / gcd(a, b)`

---

## max / min / swap / reverse

```cpp
max(a, b);
min(a, b);
max({a, b, c});         // initializer_list
min({a, b, c, d});

swap(a, b);             // 두 값 교환
swap(v[i], v[j]);

reverse(all(v));        // 뒤집기
reverse(arr, arr + n);

// max_element / min_element (2108번에서 사용)
*max_element(all(v));   // 최댓값
*min_element(all(v));   // 최솟값
int idx = max_element(all(v)) - v.begin();  // 최댓값 인덱스
```

---

## next_permutation / prev_permutation

```cpp
// 다음 순열 (오름차순 정렬 상태에서 시작)
sort(all(v));
do {
    // v 처리
} while (next_permutation(all(v)));

// 이전 순열
sort(all(v), greater<int>());
do {
    // v 처리
} while (prev_permutation(all(v)));

// bool 반환: 다음 순열이 없으면 false (= 내림차순 정렬 상태)
```

> **참고**: 내 코드(15649번)는 `next_permutation` 대신 백트래킹으로 직접 구현

```cpp
// 15649번: 백트래킹 방식 (내 코드)
int arr[10], visited[10];
void select(int cnt) {
    if (cnt == m) {
        for (int i = 0; i < m; i++) printf("%d ", arr[i]);
        puts(""); return;
    }
    for (int i = 1; i <= n; i++) {
        if (!visited[i]) {
            visited[i] = 1; arr[cnt] = i;
            select(cnt + 1);
            visited[i] = 0;
        }
    }
}
```

---

## counting sort (카운팅 정렬)

### 내 코드 패턴 (10989번)

```cpp
// 10989번: 1~10000 범위 정수 정렬
int arr[10010] = {};  // 카운팅 배열

scanf("%d", &n);
for (int i = 1; i <= n; i++) {
    scanf("%d", &t);
    arr[t]++;
}
for (int i = 1; i <= 10000; i++)
    for (int j = 1; j <= arr[i]; j++)
        printf("%d\n", i);

// 시간: O(n + k), 공간: O(k) — k는 값 범위
// 메모리 초과 주의: 값 범위가 클 때는 일반 sort 사용
```

---

## 알고리즘 함수 정리표

| 함수 | 헤더 | 시간복잡도 | 설명 |
|------|------|-----------|------|
| `sort(b, e)` | algorithm | O(n log n) | 기본 정렬 |
| `sort(b, e, cmp)` | algorithm | O(n log n) | 커스텀 정렬 |
| `stable_sort(b, e)` | algorithm | O(n log n) | 안정 정렬 |
| `lower_bound(b, e, v)` | algorithm | O(log n) | v 이상 첫 위치 |
| `upper_bound(b, e, v)` | algorithm | O(log n) | v 초과 첫 위치 |
| `binary_search(b, e, v)` | algorithm | O(log n) | 존재 여부 bool |
| `unique(b, e)` | algorithm | O(n) | 연속 중복 제거 |
| `reverse(b, e)` | algorithm | O(n) | 뒤집기 |
| `next_permutation(b, e)` | algorithm | O(n) | 다음 순열 |
| `max_element(b, e)` | algorithm | O(n) | 최댓값 이터레이터 |
| `min_element(b, e)` | algorithm | O(n) | 최솟값 이터레이터 |
| `count(b, e, v)` | algorithm | O(n) | 값 개수 세기 |
| `find(b, e, v)` | algorithm | O(n) | 값 위치 찾기 |
| `fill(b, e, v)` | algorithm | O(n) | 값으로 채우기 |
| `swap(a, b)` | utility | O(1) | 두 값 교환 |
| `accumulate(b, e, init)` | numeric | O(n) | 합산 |
| `gcd(a, b)` | numeric (C++17) | O(log n) | 최대공약수 |
| `lcm(a, b)` | numeric (C++17) | O(log n) | 최소공배수 |
| `memset(arr, v, size)` | cstring | O(n) | 바이트 단위 초기화 |

---

## 주의할 점 & 실수하기 쉬운 것

| 상황 | 실수 | 올바른 방법 |
|------|------|------------|
| `unique` 단독 사용 | 중복 제거 완료 착각 | 반드시 `erase`와 함께 사용 |
| `unique` 전 `sort` 생략 | 비연속 중복 유지됨 | `sort` → `unique` → `erase` 순서 |
| `lower_bound` 정렬 미확인 | 잘못된 결과 | 정렬된 범위에서만 사용 |
| `lower_bound` 반환값 역참조 | end()일 때 UB | `!= end()` 확인 후 역참조 |
| `accumulate` 초기값 타입 | int 오버플로우 | `0LL` 사용 |
| `a * b / gcd(a,b)` LCM | 중간 오버플로우 | `(ll)a * b / gcd(a,b)` |
| `memset(arr, -1, ...)` | -1이 바이트 단위 | `int` 배열에서 -1은 0xFFFFFFFF → OK |
| `memset(arr, 1, ...)` | 1은 0x01010101 = 약 1700만 | INF 초기화는 `0x3f3f3f3f` 사용 |
| `sort` comparator | strict weak ordering 위반 | `<` 사용, `<=` 쓰면 UB |
| `next_permutation` 시작 상태 | 정렬 안 된 채로 시작 | `sort(all(v))` 후 사용 |
| `max(a, b)` 타입 불일치 | 컴파일 에러 | `max((ll)a, b)` 또는 형변환 |
