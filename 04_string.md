# 문자열 처리 치트시트

> 실제 코드 기반 | 343개 파일 분석 | string 사용: 93회

---

## 헤더 & 기본 설정

```cpp
#include <bits/stdc++.h>
#define fastio ios_base::sync_with_stdio(false); cin.tie(nullptr);
#define sz(x) ((int)(x).size())
using namespace std;
```

---

## 1. char 처리 — ASCII & 대소문자 변환

### 패턴: 대문자 → 소문자 (1157 단어공부)
```cpp
// 직접 변환
if ('A' <= str[i] && str[i] <= 'Z')
    str[i] = str[i] - 'A' + 'a';

// 빈도 배열 인덱싱
arr[str[i] - 'a']++;

// 역변환: 인덱스 → 문자
printf("%c", i + 'A');
```

### ASCII 빠른 계산 (2908 문자열뒤집기)
```cpp
#define atoi(c) (c - '0')   // char → int (매크로)

// 뒤집기: 자릿수별 수동 파싱
x = atoi(a[0]) + 10*atoi(a[1]) + 100*atoi(a[2]);
```

| 표현 | 의미 |
|------|------|
| `c - '0'` | char → digit int |
| `c - 'a'` | 소문자 → 0~25 인덱스 |
| `c - 'A'` | 대문자 → 0~25 인덱스 |
| `tolower(c)` / `toupper(c)` | STL 변환 |

---

## 2. string 기본 연산

### substr & find (11478 부분문자열)
```cpp
string s;
set<string> st;

// 모든 부분문자열 열거 → set으로 중복 제거
for (int i = 0; i < sz(s); i++)
    for (int j = 1; i + j <= sz(s); j++) {
        auto t = s.substr(i, j);   // substr(시작, 길이)
        st.insert(t);
    }
cout << sz(st);   // 서로 다른 부분문자열 수
```

### 주요 string 메서드

| 메서드 | 용도 | 예시 |
|--------|------|------|
| `s.substr(pos, len)` | 부분문자열 | `s.substr(2, 3)` |
| `s.find(t)` | 위치 탐색 | `s.find("ab")` → `string::npos` |
| `s.size()` / `s.length()` | 길이 | `sz(s)` 매크로 권장 |
| `s.c_str()` | `const char*` 변환 | Trie insert 시 사용 |
| `getline(cin, s)` | 공백 포함 입력 | KMP 문제에서 필수 |

---

## 3. 문자열 파싱 — 포인터 슬라이딩 (2941 크로아티아알파벳)

```cpp
// 현재 위치 포인터 전달, i를 참조로 증가
int f(char *str, char *s, int *i) {
    if (strlen(s) == 2) {
        if (str[0] == s[0] && str[1] == s[1]) {
            (*i)++;      // 2글자 패턴: 인덱스 1 추가 전진
            return 1;
        }
    } else if (strlen(s) == 3) {
        if (str[0]==s[0] && str[1]==s[1] && str[2]==s[2]) {
            (*i) += 2;   // 3글자 패턴: 인덱스 2 추가 전진
            return 1;
        }
    }
    return 0;
}

// 호출: &str[i] 로 현재 위치 슬라이스 전달
for (int i = 0; str[i]; i++) {
    ans++;
    if (f(&str[i], "c=", &i)) continue;
    if (f(&str[i], "dz=", &i)) continue;
    // ...
}
```

> 주의: `for` 루프 `i++`와 `(*i)++`가 합산됨. 오프바이원 주의.

---

## 4. 스택 기반 문자열 폭발 (9935 문자열폭발)

```cpp
string s, bomb;
stack<char> st, temp;

FOR(i, sz(s)) {
    st.push(s[i]);
    char hasBomb = 1;
    if (sz(st) >= sz(bomb)) {
        // 스택 뒤에서 bomb 역방향 비교
        FOR(i, sz(bomb)) {
            if (bomb[sz(bomb) - i - 1] == st.top()) {
                temp.push(st.top()); st.pop();
            } else {
                hasBomb = 0;
            }
        }
        if (!hasBomb)
            while (!temp.empty()) st.push(temp.top()), temp.pop();
        else
            while (!temp.empty()) temp.pop();  // 폭탄 제거
    }
}
// 결과 출력: 역순 복원
while (!st.empty()) temp.push(st.top()), st.pop();
while (!temp.empty()) { cout << temp.top(); temp.pop(); }
```

> 핵심: 스택에 문자를 쌓으면서, 끝에서 bomb 길이만큼 역방향으로 매칭 확인.

---

## 5. KMP 패턴 매칭 (1786)

```cpp
// 실패 함수(failure function) 생성
vector<int> makeTable(string pattern) {
    vector<int> table(pattern.size(), 0);
    int j = 0;
    for (int i = 1; i < pattern.size(); i++) {
        while (j > 0 && pattern[i] != pattern[j])
            j = table[j - 1];           // 실패 시 이전 일치 위치로
        if (pattern[i] == pattern[j])
            table[i] = ++j;
    }
    return table;
}

// KMP 탐색
vector<int> KMP(string parent, string pattern) {
    vector<int> table = makeTable(pattern);
    vector<int> ret;
    int j = 0;
    for (int i = 0; i < sz(parent); i++) {
        while (j > 0 && parent[i] != pattern[j])
            j = table[j - 1];
        if (parent[i] == pattern[j]) {
            if (j == sz(pattern) - 1) {
                ret.push_back(i - sz(pattern) + 2);  // 1-indexed 시작위치
                j = table[j];                         // 겹침 허용
            } else j++;
        }
    }
    return ret;
}

// 사용
getline(cin, s1);   // 공백 포함 입력은 getline 필수
getline(cin, s2);
vi v = KMP(s1, s2);
```

> 시간복잡도: O(N+M) | 브루트포스 O(NM) 대비 핵심

---

## 6. 트라이 (Trie) (5670 휴대폰 자판)

```cpp
struct Trie {
    Trie* next[26];
    int isend = 0, cnt = 0;   // cnt: 자식 수
    Trie() { FOR(i, 26) next[i] = nullptr; }
    ~Trie() { FOR(i, 26) if (next[i]) delete next[i]; }

    void insert(const char* str) {
        if (*str == 0) { isend = 1; return; }
        int code = *str - 'a';
        if (!next[code]) { next[code] = new Trie(); cnt++; }
        next[code]->insert(str + 1);
    }

    // 자동완성 필요 버튼 수 계산
    int getVal(const char* str) {
        if (*str == 0) return 1;
        int code = *str - 'a';
        if (cnt > 1 || isend)   // 분기점 or 단어끝 → 버튼 필요
            return 1 + next[code]->getVal(str + 1);
        return next[code]->getVal(str + 1);
    }
};

// 사용
Trie* T = new Trie(INF);   // 루트는 항상 분기로 처리
T->insert(s.c_str());
double ans = T->getVal(word.c_str()) - 1;  // 루트 분기 보정
delete T;
```

### 트라이 복잡도

| 연산 | 복잡도 |
|------|--------|
| insert | O(L) |
| search | O(L) |
| 공간 | O(26 * 총 노드 수) |

---

## 패턴 빈도 요약

| 패턴 | 빈도 | 대표 문제 |
|------|------|-----------|
| `string` 타입 사용 | 93회 | 11478 |
| `set<string>` 중복 제거 | 높음 | 11478 |
| char 배열 포인터 슬라이딩 | 중간 | 2941 |
| KMP | 1회 | 1786 |
| Trie | 3회 | 5670 |
| stack 기반 폭발 처리 | 중간 | 9935 |

---

## 주의점

- `getline` 사용 전 `cin >>` 후 개행 제거: `cin.ignore()` 필요
- `string::npos`는 `(size_t)-1` — int 비교 시 버그 주의
- `substr(pos, len)`: len 초과해도 에러 없이 끝까지 반환
- Trie `delete` 시 소멸자 재귀 호출 → 깊은 트리에서 스택 오버플로우 가능
- KMP 1-indexed 시작위치: `i - sz(pattern) + 2`
