---
title: "URL Enumeration"
date: 2026-10-10 23:09:00 +0900
categories: [Red Team, Reconnaissance]
---

URL enumeration은 Wayback Machine, Common Crawl, AlienVault OTX 같은 크롤링 데이터 소스를 활용해서, 특정 도메인의 과거·현재 URL을 수집하는 과정이다. 정찰에서 juicy한 데이터를 많이 얻을 수 있는 과정으로, 다양한 공격 표면을 식별할 수 있다. 버그바운티에서는 XSS oneliner나 JS recon oneliner에도 사용된다.

대표적인 URL enumeration 도구는 아래와 같으며, 파이프라이닝이 편한 Project Discovery의 urlfinder나 여러 소스로부터 많은 URL을 수집해주는 waymore를 주로 사용한다. 도구별 옵션을 이용해 수집 기간, 확장자, 출력 형태 등을 설정할 수 있으며 용도에 맞게 설정하면 된다.

| tool | github link |
|---|---|
| urx | [github.com/hahwul/urx](https://github.com/hahwul/urx) |
| urlfinder | [github.com/projectdiscovery/urlfinder](https://github.com/projectdiscovery/urlfinder) |
| waybackurls | [github.com/tomnomnom/waybackurls](https://github.com/tomnomnom/waybackurls) |
| waymore | [github.com/xnl-h4ck3r/waymore](https://github.com/xnl-h4ck3r/waymore) |
| gau | [github.com/lc/gau](https://github.com/lc/gau) |

수집된 수십, 수백만 개의 URL에서 아래와 같은 공격 표면들을 식별할 수 있다.

**공격 표면 유형**

#### 1. 민감한 엔드포인트
- `/graphql`, `/swagger-ui` → introspection, 스키마 분석 대상
- `/admin`, `/dashboard` → 인증 우회 타깃
- `/upload`, `/file` → 파일 업로드/다운로드 취약점

#### 2. 민감한 파일
- `.xls`, `.csv` → 개인정보나 민감 정보 포함 가능성
- `.pdf` → "confidential", "internal use only" 같은 키워드가 담긴 문서
- `.js` → API 키, 하드코딩된 엔드포인트, 비공개 URL
- `.txt` → robots.txt, security.txt

#### 3. 개발 스타일
- 파라미터 네이밍(예: `redirect_to=`, `next=`) → 다른 서브도메인에도 같은 패턴이 있을 가능성
- 디렉터리 구조(예: `/admin`, `/dev`, `/api/v1`) → 다른 엔드포인트를 유추

> 예시: `a.redacted.com/link?redirect_to=...` → `site:*.redacted.com inurl:redirect_to` 같은 google dorking으로 확장 가능

#### 4. 취약점 테스트용 파라미터
- `?id=`, `?user=`, `?redirect=` → IDOR, SQLi, open redirect 테스트를 위한 공격 표면

**LLM 활용을 위한 팁**

가공하지 않은 raw 데이터를 그대로 LLM에 넣는 건 토큰 낭비다. 보통 큰 도메인의 경우 수백만 개까지 수집되기 때문에 아래와 같은 전처리를 통해 토큰(비용)을 절약할 수 있다.

- 중복 제거
- 정적 파일(.png, .jpg, .css, .ttf, .otf 등) 제거
- HTTP 스키마(`https://`, `http://`) 제거
- 타임스탬프 파라미터(`ts=`) 제거 후 중복 제거

모의해킹 시 외부 서비스일 경우 테스트 포인트(공격 표면)를 빠르게 식별하기 위해 좋은 기법으로, 진입점 이외에도 파급력을 높이기에 좋다. 예를 들어

1. a 엔드포인트의 `search=` 파라미터에서 XSS가 발생했을 경우 → URL enumeration → `search=` grep → XSS 도구로 전체 테스트
2. a 엔드포인트의 GraphQL 엔드포인트(`/graphql`)에서 Introspection이 활성화되어 있을 경우 → URL enumeration → `/graphql` grep으로 다른 서브도메인까지 확인

방어적인 관점에서는 robots.txt를 체계적으로 설정해 크롤러가 못 들어오게 함으로써 아카이브가 쌓이지 않게 하거나, 주기적으로 삭제 요청을 보내는 방법이 있다. 실제로 실서비스에서는 이미 죽은 URL이었지만, Wayback에 남아있던 `.xls`에 개인정보가 담겨 있어 정보노출 취약점으로 제보하고 Wayback에 삭제 요청하라고 권고했던 적이 있다.
