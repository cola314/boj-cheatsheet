# 입출력 & 자료형 치트시트

> 내 코드 343개 분석 기준 (2026-03)

---

## 내 코드에서 뽑은 패턴 (빈도수)

| 패턴 | 빈도 |
|------|------|
| `cin` / `cout` 사용 | 278 / 343 |
| fastio 매크로 | 274 / 343 |
| `#define endl "\n"` | 245 / 343 |
| `scanf` / `printf` 사용 | 67 / 343 |
| `long long` 사용 | 254 / 343 |
| `typedef long long ll` | 184 / 343 |
| `#define int ll` | 104 / 343 |
| `signed main()` | 94 / 343 |
| `double` 사용 | 24 / 343 |
| `bits/stdc++.h` | 208 / 343 |

---

## 템플릿 헤더 (실제 내 코드)

### 표준 템플릿 (최근 코드 기준 — 2108, 25206, 24416, 28278 등)

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
#define int ll          // long long을 int처럼 쓸 때
using namespace std;
using ll = long long;
using vi = vector<int>;
using ii = pair<int, int>;
using vii = vector<ii>;
```

### 구버전 템플릿 (15552, 11382 등)

```cpp
#include <bits/stdc++.h>
#define fastio ios_base::sync_with_stdio(false); cin.tie(nullptr);
#define endl "\n"
// ...
typedef long long ll;
typedef pair<int, int> ii;
typedef vector<int> vi;
```

---

## 입출력

### cin / cout (빈도: 278)

```cpp
// 기본 사용 (1000번)
int a, b;
cin >> a >> b;
cout << a + b;

// 여러 줄 출력 (endl 매크로 = "\n" 이므로 flush 없음)
cout << a + b << endl;   // "\n" 출력 (빠름)
```

### fastio 설정 (빈도: 274)

```cpp
// 방법 1: 매크로로 (최근 코드 표준)
#define fastio cin.tie(NULL); cin.sync_with_stdio(false);
// main() 첫 줄에서 호출
fastio;

// 방법 2: 풀어쓰기 (15552번)
cin.sync_with_stdio(0);
cout.sync_with_stdio(0);
cin.tie(0);
cout.tie(0);

// 방법 3: 다른 스타일
ios_base::sync_with_stdio(false);
cin.tie(nullptr);
```

> **주의**: fastio 사용 시 `scanf`/`printf`와 혼용 금지!

### scanf / printf (빈도: 67)

```cpp
// 기본 사용 (1000번 초기 풀이)
int a, b;
scanf("%d %d", &a, &b);
printf("%d", a + b);

// 문자열
char s[100];
scanf("%s", s);
printf("%s\n", s);
```

### EOF 처리 (10951번)

```cpp
// scanf로 EOF 처리
while (scanf("%d %d", &a, &b) != EOF)
    printf("%d\n", a + b);

// cin으로 EOF 처리
while (cin >> a >> b)
    cout << a + b << "\n";
```

### 반복 입력 (15552번)

```cpp
int n, a, b;
cin >> n;
FOR(i, n) {          // FOR(x, y) = for(int x = 0; x < y; x++)
    cin >> a >> b;
    cout << a + b << endl;
}
```

### getline

```cpp
string line;
getline(cin, line);

// cin 이후 getline 쓸 때 (개행 문자 제거)
cin >> n;
cin.ignore();
getline(cin, line);
```

---

## 자료형

### int vs long long (11382번)

```cpp
// long long 선언 (11382: a+b+c 최대 3×10^18)
ll a, b, c;
cin >> a >> b >> c;
cout << a + b + c;

// typedef 방식 (구버전)
typedef long long ll;
ll x = 1e18;

// using 방식 (최신 코드)
using ll = long long;
```

### #define int ll + signed main (빈도: 104/94)

```cpp
#define int ll          // int를 모두 long long으로 대체
using namespace std;
using ll = long long;   // ll은 별도로 정의

int a, b;               // 실제로는 long long

signed main() {         // main의 반환형 int → signed (= long long이 됐으므로)
    // ...
    return 0;           // 생략해도 됨
}
```

> **주의**: `#define int ll` 사용 시 반드시 `signed main()` 써야 함. `int main()`으로 쓰면 `long long main()`이 돼서 컴파일 에러 또는 경고.

### double / 실수 출력 (25206번)

```cpp
double sum = 0;
double b;          // 학점 (실수)
// ...
cout << fixed << setprecision(5) << sum / cnt;
// fixed: 고정소수점 표기
// setprecision(5): 소수점 이하 5자리
```

| 포맷 | 설명 | 예시 |
|------|------|------|
| `setprecision(n)` | 유효숫자 n개 | `3.14159` |
| `fixed << setprecision(n)` | 소수점 이하 n자리 고정 | `3.14159` |
| `scientific` | 지수 표기 | `3.14e+00` |

### 자료형 크기 & 범위

| 자료형 | 크기 | 범위 |
|--------|------|------|
| `int` | 4 bytes | ±2.1×10^9 |
| `long long` | 8 bytes | ±9.2×10^18 |
| `double` | 8 bytes | 유효숫자 15~16자리 |
| `long double` | 16 bytes | 유효숫자 18~19자리 |
| `unsigned int` | 4 bytes | 0 ~ 4.3×10^9 |

---

## 자주 쓰는 상수 정의 (내 코드 기준)

```cpp
#define INF 987654321        // 10^9 미만 (구버전)
#define INF 1000000010       // 10^9 + 10 (신버전)
#define MOD ((int)1e9 + 7)   // 10^9 + 7

// long long INF가 필요할 때
const ll INF = 1e18;
const ll INF = 4e18;
```

---

## 주의할 점 & 실수하기 쉬운 것

| 상황 | 실수 | 올바른 방법 |
|------|------|------------|
| fastio 쓰면서 scanf 혼용 | 출력 순서 꼬임 | 둘 중 하나만 사용 |
| `endl` 자주 쓰기 | `flush` 호출로 느려짐 | `#define endl "\n"` |
| `#define int ll` 후 `int main()` | 컴파일 오류 | `signed main()` 사용 |
| `int` 범위 초과 | 오버플로우 | `long long` 또는 `#define int ll` |
| `double` 비교 | `a == b` 오류 가능 | `abs(a-b) < 1e-9` |
| `accumulate` 초기값 타입 | `int` 오버플로우 | `accumulate(all(v), 0LL)` |
| `round()` 반환형 | `double` 반환 | `(int)round(x)` 또는 `llround(x)` |

---

## 빠른 참조

```cpp
// 정수 읽기
int n; cin >> n;
ll x; cin >> x;

// 실수 읽기 + 출력
double d; cin >> d;
cout << fixed << setprecision(6) << d << "\n";

// 문자열 읽기
string s; cin >> s;         // 공백 전까지
string line; getline(cin, line);  // 한 줄 전체

// 문자 읽기
char c; cin >> c;           // 공백 무시
char c = getchar();         // 공백 포함

// 숫자 → 문자열
string s = to_string(42);

// 문자열 → 숫자
int n = stoi("42");
ll n = stoll("42");
double d = stod("3.14");
```
