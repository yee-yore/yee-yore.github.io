---
title: "Google Dorking"
date: 2026-10-05 22:27:00 +0900
categories: [Red Team, Reconnaissance]
tags: [osint, google-dorking, recon, bugbounty]
---

Google Dorking(=Google Hacking)은 Google 검색엔진에서 필터와 연산자를 이용해 원하는 정보를 체계적으로 검색하는 OSINT 기법이다.

개인적으로 느끼기에 Google Dorking은 알아두면 업무 외에도 요긴하게 써먹을 때가 많다. (e.g. 정확한 키워드가 포함된 검색, 게재된 논문과 같은 이력 검색)

아래와 같은 필터와 연산자를 주로 사용하며, 이를 조합하면 내가 원하는 정보를 구체적으로 탐색할 수 있다.

| 연산자 | 설명 | 예시 |
|---|---|---|
| `site:` | 특정 도메인 내에서만 검색 | `site:example.com` |
| `inurl:` | URL에 특정 문자열이 포함된 페이지 | `inurl:admin` |
| `intitle:` | 페이지 제목에 특정 문자열이 포함된 페이지 | `intitle:"index of"` |
| `intext:` | 본문에 특정 문자열이 포함된 페이지 | `intext:"password"` |
| `filetype:` / `ext:` | 특정 확장자의 파일 | `ext:pdf`, `filetype:xlsx` |
| `"..."` | 정확히 일치하는 문구 | `"internal use only"` |
| `-` | 특정 단어나 조건 제외 | `site:example.com -www` |
| `\|` / `OR` | 둘 중 하나라도 포함 | `ext:pdf \| ext:docx` |
| `*` | 임의의 단어(와일드카드) | `"admin * panel"` |
| `( )` | 조건 묶기 | `(inurl:v1 \| inurl:v2)` |
| `before:` / `after:` | 특정 날짜 기준 이전/이후 | `after:2026-01-01` |

AND는 따로 연산자를 쓰지 않고 공백으로 이어 붙이면 모든 조건을 만족하는 결과만 나온다.

Google Dorking은 주로 아래와 같은 업무에서 사용된다.

**1. 레드티밍, 모의해킹**

- 침투를 위한 공격 표면 식별
  - 피싱을 위한 이메일 수집
    ```
    (site:linkedin.com | site:github.com) "@[domain].[tld]"
    ```
  - API 엔드포인트 수집
    ```
    site:target.com inurl:graphql
    site:target.com inurl:api (inurl:v1 | inurl:v2 | inurl:v3)
    ```

**2. 버그바운티 헌팅**

- Information Disclosure 취약점
  ```
  site:target.com ext:pdf ("confidential" | "internal use only" | "do not distribute")
  ```
- Directory Listing
  ```
  site:target.com intitle:"index of /"
  ```

**3. 공격자 C2 탐색**

- 간혹 C2에 감염 신호를 전송할 때 URL이 패턴화(e.g. `blahblah/set_agent?id=blahblah&type=chrome`)된 경우가 있으며, 아래와 같이 탐색할 수 있다.
  ```
  inurl:set_agent inurl:id inurl:type
  ```

이러한 검색식을 Google Dork이라고 하며, Linkedin, Github, Twitter에 많이 공유되고 ExploitDB의 [GHDB(Google Hacking Database)](https://www.exploit-db.com/google-hacking-database)에서도 참고할 수 있다.

외부 공개 웹서비스 모의해킹이나 버그바운티 시 주로 사용하는 사이트는 [TakSec/google-dorks-bug-bounty](https://github.com/TakSec/google-dorks-bug-bounty)이고, 자주 사용되는 Google Dork을 이용해 Google Dorking을 자동화하는 도구로는 대표적으로 [BullsEye0/dorks-eye](https://github.com/BullsEye0/dorks-eye)가 있다.

## Reference

- [레드팀.com - Google Dorking](https://www.xn--hy1b43d247a.com/initial-recon/osint/google-dorking)
