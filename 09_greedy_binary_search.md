# 09 그리디 & 이분탐색 (Greedy & Binary Search)

> 실제 코드 기반: 1931, 11047, 1541, 1654, 2805, 2110, 1300

---

## 그리디 기본 패턴

### 정렬 후 탐욕 - 회의실 배정
**출처: 1931 (회의실 배정)**

```cpp
bool cmp(pii a, pii b) {
    // 끝 시간 기준 정렬, 같으면 시작 시간 기준
    return a.first < b.first || (a.first == b.first && a.second < b.second);
}
// 실제로는 종료시간 기준 정렬이 핵심
// sort 후: 이전 선택 회의 종료 <= 현재 시작이면 선택

sort(V.begin(), V.end(), cmp);
RE.push_back(V[0]);
for (int i = 1; i < n; i++) {
    if (RE.back().second <= V[i].first) {
        RE.push_back(V[i]);
    } else if (RE.back().second > V[i].second) {
        // 더 일찍 끝나는 회의로 교체
        RE.pop_back();
        RE.push_back(V[i]);
    }
}
cout << RE.size();
```

- 핵심: **종료 시간** 오름차순 정렬
- 같은 종료시간이면 시작시간 오름차순 (1931 특이케이스)

### 큰 단위 동전부터 - 동전
**출처: 11047 (동전 0)**

```cpp
int ans = 0;
for (int i = n; i >= 1; i--) {
    ans += k / arr[i];
    k %= arr[i];
}
printf("%d", ans);
```

- 큰 단위부터 최대한 사용
- 단, 동전 배수 관계가 성립할 때만 그리디 최적

### 괄호/식 그리디
**출처: 1541 (잃어버린 괄호)**

```cpp
// '-' 이후 나오는 모든 항은 빼기
string line;
cin >> line;
int total = 0, cur = 0, flag = 0;
for (char c : line) {
    if (c == '+') { flag ? total -= cur : total += cur; cur = 0; }
    else if (c == '-') { flag ? total -= cur : total += cur; flag = 1; cur = 0; }
    else { cur = cur * 10 + (c - '0'); }
}
flag ? total -= cur : total += cur;
cout << total;
```

---

## 파라메트릭 서치 (Parametric Search)

### 틀 (최대화 문제)
**출처: 2805 나무자르기, 2110 공유기설치**

```cpp
ll lp = 0, rp = MAX_VAL, mp, result = 0;
while (lp <= rp) {
    mp = (lp + rp) / 2;
    if (possible(mp)) {
        lp = mp + 1;
        result = max(result, mp);  // 최대화
    } else {
        rp = mp - 1;
    }
}
cout << result;
```

### 틀 (최솟값 찾기)
**출처: 1300 (K번째 수), 1654 (랜선 자르기)**

```cpp
ll s = 1, e = MAX, m, ans = MAX;
while (s <= e) {
    m = (s + e) / 2;
    if (f(m) >= k) {  // m 이하인 수가 k개 이상
        e = m - 1;
        ans = min(ans, m);  // 최솟값
    } else {
        s = m + 1;
    }
}
cout << ans;
```

### 나무 자르기 (2805)
```cpp
bool possible(ll num) {
    ll cnt = 0;
    for (int i = 0; i < n; i++)
        if (arr[i] - num > 0) cnt += arr[i] - num;
    return cnt >= m;
}
// lp=0, rp=max(arr), 최대화 패턴 사용
```

### 랜선 자르기 (1654)
```cpp
bool check(int num) {
    int cnt = 0;
    for (int i = 0; i < n; i++) cnt += v[i] / num;
    return cnt >= k;
}
// a=1, b=max+1, a<b 조건, 결과는 b-1
ll a = 1, b = max_val + 1;
while (a < b) {
    ll c = (a + b) / 2;
    if (check(c)) a = c + 1;
    else b = c;
}
cout << b - 1;
```

### 공유기 설치 (2110) - 최소 거리 최대화
```cpp
bool possible(ll num) {
    ll cnt = 1;
    for (int i = 0, j = 1; j < n; j++) {
        if (V[j] - V[i] >= num) { cnt++; i = j; }
    }
    return cnt >= c;
}
sort(V.begin(), V.begin() + n);
// lp=1, rp=1e9, 최대화 패턴
```

---

## 이분탐색 직접 구현

### `lower_bound` / `upper_bound` 직접
```cpp
// lower_bound: v[mid] >= target 인 첫 위치
int lo = 0, hi = n;
while (lo < hi) {
    int mid = (lo + hi) / 2;
    if (v[mid] >= target) hi = mid;
    else lo = mid + 1;
}
// lo = lower_bound 위치

// upper_bound: v[mid] > target 인 첫 위치
lo = 0; hi = n;
while (lo < hi) {
    int mid = (lo + hi) / 2;
    if (v[mid] > target) hi = mid;
    else lo = mid + 1;
}
// lo = upper_bound 위치
```

### STL lower_bound / upper_bound
```cpp
// 반드시 정렬된 배열에서만 사용
auto it = lower_bound(v.begin(), v.end(), x);  // >= x 첫 위치
auto it = upper_bound(v.begin(), v.end(), x);  // > x 첫 위치
int idx = it - v.begin();  // 인덱스 변환
int cnt = upper_bound(...) - lower_bound(...);  // x의 개수
```

- 빈도: lower_bound 6개, upper_bound 5개 파일

---

## 이분탐색 패턴 비교

| 패턴 | 조건 | 초기값 | 결과 |
|------|------|--------|------|
| 최대화 | `possible(mid)` | `lp=0, rp=MAX` | `result = max(result, mp)` |
| 최솟값 | `f(mid) >= k` | `s=1, e=MAX` | `ans = min(ans, m)` |
| `a < b` 스타일 (1654) | `check(c)` | `a=1, b=MAX+1` | `b - 1` |

---

## 주의점 요약

| 항목 | 주의 |
|------|------|
| 그리디 정렬 기준 | 문제마다 다름, 반례 확인 필수 |
| 이분탐색 범위 | `rp = max_val + 1` (초과 경계), 오버플로우 주의 |
| `while (lp <= rp)` vs `while (lp < rp)` | 종료 조건 일관성 유지 |
| 파라메트릭 check | 단조성 확인 (가능/불가능이 명확히 구분) |
| `ll` 사용 | 이분탐색 경계 및 `possible` 내 계산에 `ll` 필요 |
| 1654 b-1 패턴 | `a < b` 루프 + 결과는 `b-1` (a==b 수렴 후) |
