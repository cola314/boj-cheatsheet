# 07 수학 패턴 (Math)

> 실제 코드 기반: 1929, 1934, 11401, 1629, 10830, 11402, 11653

---

## GCD / LCM
**출처: 1934 (최소공배수)**

```cpp
int gcd(int a, int b) { return b == 0 ? a : gcd(b, a % b); }
// LCM = a * b / gcd(a, b)
cout << a * b / gcd(a, b) << endl;
```

- 유클리드 호제법, 재귀 1줄
- `a * b`가 오버플로우날 수 있음 → `(ll)a * b / gcd(a, b)` 사용
- 빈도: 8개 파일에서 사용

---

## 소수 판별 (단일)
**출처: 1929 (소수 구하기)**

```cpp
// O(sqrt(n)) 단일 판별
int f(int n) {
    if (n == 1) return 0;
    for (int i = 2; i <= sqrt(n); i++) {
        if (n % i == 0) return 0;
    }
    return 1;
}
```

- `sqrt(n)`까지만 확인
- 1929 코드는 `<math.h>` + `sqrt()` 사용 (부동소수점 주의 → `i * i <= n` 권장)

---

## 에라토스테네스의 체
**출처: 1929 패턴 (범위 소수 열거)**

```cpp
// O(N log log N)
vector<bool> sieve(int n) {
    vector<bool> is_prime(n + 1, true);
    is_prime[0] = is_prime[1] = false;
    for (int i = 2; i * i <= n; i++) {
        if (is_prime[i]) {
            for (int j = i * i; j <= n; j += i)
                is_prime[j] = false;
        }
    }
    return is_prime;
}
```

- 빈도: 3개 파일
- `j = i*i`부터 시작 (최적화)

---

## 소인수분해
**출처: 11653 (소인수분해)**

```cpp
int n;
cin >> n;
for (int i = 2; i > 1 && i <= n; i++) {
    if (n % i == 0) {
        cout << i << endl;
        n /= i;
        i--;  // 같은 소인수 반복 처리
    }
}
```

- `i--` 트릭으로 같은 인수 반복 출력
- 시간복잡도 O(sqrt(N)) 수준

---

## 거듭제곱 (분할정복)
**출처: 1629 (곱셈)**

```cpp
ll f(ll a, ll b) {  // a^b % c
    if (b == 1) return a % c;
    if (b % 2 == 0) {
        return (f(a, b / 2) * f(a, b / 2)) % c;
    } else {
        return (f(a, b / 2 + 1) * f(a, b / 2)) % c;
    }
}
```

- O(log b)
- 주의: `f(a, b/2)`를 두 번 호출 → 메모이제이션 없이 O(log^2 b). 개선판:

```cpp
ll pw(ll a, ll b, ll mod) {
    ll res = 1; a %= mod;
    for (; b > 0; b >>= 1) {
        if (b & 1) res = res * a % mod;
        a = a * a % mod;
    }
    return res;
}
```

---

## 이항계수 + 모듈러 역원
**출처: 11401 (이항계수 3, MOD=1e9+7)**

```cpp
#define MOD 1000000007

ll customPow(ll a, ll b) {
    if (b == 0) return 1;
    if (b == 1) return a % MOD;
    if (b % 2 == 0) { a = customPow(a, b / 2); return a * a % MOD; }
    else return (customPow(a, b / 2 + 1) * customPow(a, b / 2)) % MOD;
}

ll fac(ll a) {
    ll re = 1;
    for (ll i = 1; i <= a; i++) re = re * i % MOD;
    return re;
}

// C(n, k) mod p = n! * (k! * (n-k)!)^{-1} mod p
// 페르마 소정리: a^{-1} ≡ a^{p-2} (mod p)
ll C(ll n, ll k) {
    ll a = fac(n);
    ll b = fac(k) * fac(n - k) % MOD;
    return a * customPow(b, MOD - 2) % MOD;
}
```

- MOD가 소수일 때만 페르마 소정리 적용 가능
- 빈도: 4개 파일에서 combination 패턴

---

## 뤼카 정리 (Lucas' Theorem)
**출처: 11402 (이항계수 4, 임의의 소수 p)**

```cpp
// C(n, r) mod p  (p가 소수, n이 매우 클 때)
int dp[MAX][MAX];  // dp[n][r] = C(n,r) mod p (0 <= n,r < p)

int f(int n, int r) {
    if (r > n) return 0;
    if (n == r || r == 0) return 1;
    int& ans = dp[n][r];
    if (ans != -1) return ans;
    return ans = (f(n - 1, r - 1) + f(n - 1, r)) % p;
}

int ncr(int n, int r) {
    if (n == 0 && r == 0) return 1;
    return f(n % p, r % p) * ncr(n / p, r / p) % p;
}
// ini(dp, -1) 로 초기화 필요
```

- Lucas: C(n,r) ≡ C(n mod p, r mod p) * C(n/p, r/p) (mod p)
- p가 작은 소수일 때 사용

---

## 행렬 거듭제곱
**출처: 10830 (행렬 제곱)**

```cpp
vector<vi> matrixMultiply(vector<vi> a, vector<vi> b) {
    vector<vi> c(n+1, vi(n+1, 0));
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            for (int k = 1; k <= n; k++)
                c[i][j] = (c[i][j] + a[i][k] * b[k][j]) % 1000;
    return c;
}

vector<vi> matrixPow(vector<vi> v, ll b) {
    if (b == 1) return v;
    if (b % 2 == 0) {
        auto re = matrixPow(v, b / 2);
        return matrixMultiply(re, re);
    } else {
        auto t = v;
        auto re = matrixPow(v, b / 2);
        re = matrixMultiply(re, re);
        return matrixMultiply(re, t);
    }
}
```

- O(n^3 log b)
- 항등행렬(identity matrix)이 필요한 경우 b==0 처리 추가

---

## 주의점 요약

| 항목 | 주의 |
|------|------|
| GCD overflow | `a*b` → `(ll)a * b` |
| 소수 판별 | `sqrt()` 부동소수점 → `i*i <= n` 권장 |
| 모듈러 역원 | MOD가 소수일 때만 페르마 소정리 사용 |
| 행렬곱 | 중간 결과도 모듈러 취하기 |
| Lucas | p가 소수이고 n이 매우 클 때만 사용 |
| 거듭제곱 | 반복문 방식이 재귀보다 호출 수 적음 |
