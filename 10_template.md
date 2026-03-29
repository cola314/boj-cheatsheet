# 10 내 C++ 템플릿 & 매크로

> 실제 코드 기반: 20920, 24479, 1300, 2098, 9663, 2042, 10999, 11402

---

## 최신 풀 템플릿 (20920, 24479, 1300 기준)

```cpp
#define _CRT_SECURE_NO_WARNINGS
#define _USE_MATH_DEFINES
#include <bits/stdc++.h>
#define fastio cin.tie(NULL); cin.sync_with_stdio(false);
#define endl "\n"
#define FOR(x, y) for(int x = 0; x < y; x++)
#define pb push_back
#define all(x) x.begin(),x.end()
#define sz(x) ((int)x.size())
#define ini(x, y) memset(x, y, sizeof(x));
#define INF 987654321
#define y1 omg
#define fi first
#define se second
#define mp make_pair
#define ceildv(x, y) ((x) / (y) + ((x) % (y) != 0))
#define sumn(x) ((x) * ((x) + 1) / 2)
#define square(x) ((x) * (x))
#define int ll
using namespace std;
using ll = long long;
using vi = vector<int>;
using ii = pair<int, int>;
using iii = pair<pair<int, int>, int>;
using vii = vector<ii>;
using vvi = vector<vector<int>>;

// int dx[] = { 1, -1, 0, 0, 1, 1, -1, -1 };
// int dy[] = { 0, 0, 1, -1, 1, -1, 1, -1 };

signed main() {
    fastio;
    // ...
}
```

---

## 구버전 템플릿 (2042, 10999, 11438 등 - bits 없음)

```cpp
#include <bits/stdc++.h>   // 또는 개별 include
#define fi first
#define se second
#define pb push_back
#define endl "\n"
#define fastio ios_base::sync_with_stdio(false); cin.tie(nullptr);
#define all(x) x.begin(),x.end()
#define y1 omg
#define INF 1000000010
#define FOR(x, n) for(int x=0; x<n; x++)
using namespace std;
typedef long long ll;
typedef pair<int, int> ii;
typedef pair<int, ii> iii;
typedef vector<int> vi;
typedef vector<ii> vii;
typedef vector<vi> vvi;
typedef vector<ll> vl;
```

---

## 매크로 전체 목록

### I/O 관련
| 매크로 | 정의 | 빈도 | 설명 |
|--------|------|------|------|
| `fastio` | `cin.tie(NULL); cin.sync_with_stdio(false);` | 208+ | cin/cout 가속 |
| `endl` | `"\n"` | 255+ | flush 방지 |

### 컨테이너 편의
| 매크로 | 정의 | 빈도 | 설명 |
|--------|------|------|------|
| `pb` | `push_back` | 255+ | vector 추가 |
| `all(x)` | `x.begin(), x.end()` | ~200 | sort 등에 사용 |
| `sz(x)` | `(int)x.size()` | ~100 | signed int 변환 |
| `fi` | `first` | 255+ | pair.first |
| `se` | `second` | 255+ | pair.second |
| `mp` | `make_pair` | ~100 | pair 생성 |

### 반복문
| 매크로 | 정의 | 빈도 | 설명 |
|--------|------|------|------|
| `FOR(x, n)` | `for(int x=0; x<n; x++)` | 206+ | 0-based n회 |
| `FOR(x, n)` (구버전) | `for(int x=1; x<=n; x++)` | 일부 | 1-based (1717 등) |

### 수학/배열
| 매크로 | 정의 | 빈도 | 설명 |
|--------|------|------|------|
| `ini(x, n)` | `memset(x, n, sizeof(x))` | ~50 | 배열 초기화 |
| `INF` | `987654321` 또는 `1000000010` | ~100 | 무한대 |
| `ceildv(x, y)` | `(x/y + (x%y != 0))` | 최신템플릿 | 올림 나눗셈 |
| `sumn(x)` | `x*(x+1)/2` | 최신템플릿 | 가우스 합 |
| `square(x)` | `(x)*(x)` | 최신템플릿 | 제곱 |
| `atoi(x)` | `(x)-'0'` | ~30 | 문자->숫자 (주의: 표준함수 덮어씀) |
| `MOD` | `1000000007` 또는 `(int)1e9+7` | ~20 | 모듈러 상수 |

### 트릭
| 매크로 | 정의 | 이유 |
|--------|------|------|
| `y1 omg` | `#define y1 omg` | `<cmath>`의 전역변수 `y1`과 충돌 방지 |
| `#define int ll` | `long long`을 int처럼 사용 | 오버플로우 방지 |

---

## typedef / using 목록

```cpp
// 구버전 (typedef)
typedef long long ll;
typedef pair<int, int> ii;
typedef pair<int, ii> iii;
typedef vector<int> vi;
typedef vector<ii> vii;
typedef vector<vi> vvi;
typedef vector<ll> vl;

// 신버전 (using)
using ll = long long;
using ii = pair<int, int>;
using iii = pair<pair<int, int>, int>;  // 신버전에서 iii 구조 변경
using vi = vector<int>;
using vii = vector<ii>;
using vvi = vector<vector<int>>;
```

---

## `#define int ll` + `signed main()` 패턴

```cpp
#define int ll        // int를 long long으로 치환
using namespace std;
using ll = long long; // ll은 long long 유지 (실제 ll 필요시)

signed main() {       // main의 반환형은 int (= ll) 이므로 signed 명시
    // int x = ...; 가 실제론 long long x = ...;
}
```

- 빈도: 최신 파일 대부분 사용 (20920, 24479, 1300, 11402 등)
- 주의: `int arr[N]` 도 ll 배열이 됨 → 메모리 2배
- `printf("%d", x)` 와 혼용 불가 → `cout` 전용

---

## dx/dy 배열

```cpp
// 4방향 (상하좌우)
int dx[] = { 1, -1, 0, 0 };
int dy[] = { 0, 0, 1, -1 };

// 8방향 (대각선 포함) - 내 코드 표준
int dx[] = { 1, -1, 0, 0, 1, 1, -1, -1 };
int dy[] = { 0, 0, 1, -1, 1, -1, 1, -1 };
```

---

## bits/stdc++.h vs 개별 include

| 스타일 | 파일 | 비고 |
|--------|------|------|
| `#include <bits/stdc++.h>` | 최신 (20920, 24479, 1929 등) | 빈도 208/343 |
| 개별 include | 구버전 (1629, 10830, 2098, 9663 등) | 초기 스타일 |

구버전 개별 include 목록:
```cpp
#include <iostream>
#include <algorithm>
#include <string>
#include <cstring>
#include <cmath>
#include <vector>
#include <stack>
#include <queue>
#include <set>
#include <bitset>
#include <map>
```

---

## fastio 두 가지 형태

```cpp
// 형태 1 (구버전, ios_base)
#define fastio ios_base::sync_with_stdio(false); cin.tie(nullptr);

// 형태 2 (최신버전, cin)
#define fastio cin.tie(NULL); cin.sync_with_stdio(false);
```

두 형태 모두 동일한 효과. `cin.tie(nullptr)` == `cin.tie(NULL)`.

---

## 사용 빈도 통계 (343개 파일 기준)

| 패턴 | 빈도 | 비율 |
|------|------|------|
| `#define` 매크로 전체 | 274 | 80% |
| `#define pb` | 255 | 74% |
| `#define fi/se` | 255 | 74% |
| `#define endl "\n"` | 255 | 74% |
| `bits/stdc++.h` | 208 | 61% |
| `fastio` | 208 | 61% |
| `#define y1 omg` | 207 | 60% |
| `#define FOR` | 206 | 60% |
| 비트시프트 (`>>`, `<<`) | 278 | 81% |
| `bitmask &` | 7 | 2% |
| `lower_bound` | 6 | 2% |
| `upper_bound` | 5 | 1% |
| `segment_tree` | 11 | 3% |
| `union_find` | 8 | 2% |
| `gcd` | 8 | 2% |

---

## 주의점 요약

| 항목 | 주의 |
|------|------|
| `#define int ll` | `signed main()` 필수, printf("%d") 사용 불가 |
| `#define atoi(x)` | 표준 `atoi()` 덮어씀, 문자열->int 변환 사용 불가 |
| `#define endl "\n"` | `std::endl` 덮어씀, flush 필요시 `"\n"` + `cout.flush()` |
| `y1 omg` | `<cmath>` 포함 시 `y1` 전역변수 충돌 방지 |
| `FOR` 1-based vs 0-based | 파일마다 다름, 복붙 시 확인 |
| `sz(x)` | `(int)` 캐스팅으로 signed/unsigned 비교 경고 방지 |
