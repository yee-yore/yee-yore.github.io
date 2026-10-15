---
title: DorkAgent
date: 2026-10-15 22:22:00 +0900
categories: [Red Team, Reconnaissance]
---

DorkAgent는 Google Dorking의 전 과정을 LLM 에이전트로 자동화하는 도구다. 도메인만 넣으면 검색부터 분석, 리포트 작성까지 에이전트가 처리해주기 때문에 사용자는 리포트 검증만 하면 된다.

- GitHub: <https://github.com/yee-yore/DorkAgent>

## 개발 동기

버그바운티를 하면서 [Google Dorks for Bug Bounty](https://taksec.github.io/google-dorks-bug-bounty/)로 Google Dorking을 자주 했는데, 매번 반복되는 과정이 번거로웠다.

1. 도메인 입력
2. 검색
3. 검색 결과를 하나하나 클릭하며 attack surface인지, information disclosure인지, 쓸모없는 정보인지 판단

특히 3번이 문제였다. 버그바운티든 블루팀이든 넓은 범위의 도메인을 대상으로 하기 때문에 검색 결과가 수백, 수천 개씩 나온다.
[dorks-eye](https://github.com/BullsEye0/dorks-eye) 같은 기존 도구도 있었지만 검색까지만 자동화해줄 뿐, 결과 분석은 여전히 사람 몫이었다.

그러다 마침 배우고 있던 CrewAI가 떠올랐고, LLM으로 전 과정을 자동화할 수 있겠다는 생각이 들었다.

## 동작 방식

구조는 단순하다. 도메인은 변수로 넘겨주고, 역할별로 에이전트를 나눴다.

| 에이전트 | 역할 |
| --- | --- |
| 검색 | 검색 도구를 사용해 dork로 Google 검색 |
| 분석 | 검색 결과가 버그바운티에 유용한 정보인지 판단 |
| 리포트 | 분석 결과를 취합해 최종 리포트 작성 |

검색 에이전트는 Google Dorking을 수행하여 검색 결과(URL, Title, Snippet)를 분석 에이전트에게 전달한다.
분석 에이전트는 검색 결과를 보고 유용한 정보(정보노출, attack surface 등)인지 판단한다.
리포트 에이전트는 정리해서 사용자가 보기 쉽게 정리해준다.

dork는 위의 Google Dorks for Bug Bounty 외에도 버그바운티 팁을 공유하는 Telegram, X, LinkedIn, GitHub repo, GHDB를 참고했다. 쓰다 보니 결과가 대부분 단순 정보성이었던 dork는 빼고, juicy한 dork를 추가하는 식으로 계속 다듬었다.

## 공개 이후

그 당시 3사 가성비 모델(gpt mini, claude haiku, gemini flash)을 계속 굴리며 프롬프트도 개선하고 오탐도 검증하고 어느 모델이 좋은지 비교도 해보면서 dogfooding했다.

실제 찾은 정보노출 취약점이나 attack surface를 대상으로 검색하게해서 잘 찾아내는지도 실험해봤다.

1달 정도 만들어서 새벽에 GitHub에 올리고, [r/bugbounty에 post](https://www.reddit.com/r/bugbounty/comments/1jopmi8/created_a_tool_that_automates_google_dorking_with/)을 하나 썼다. 자고 일어나니 서브레딧에서 핫글에 올라 있었고 GitHub star도 30개가 달려 있었다.

이후 버그바운티를 하며 직접 dogfooding하고 조금씩 보완했다. 그러다 보니 X, LinkedIn, offensive tool 소개 플랫폼에서도 DorkAgent를 언급해주었다.

도구를 테스트하면서 information disclosure를 찾기도 했고, 이 도구로 사내외 교육도 진행하며 지인분들의 의견도 반영할 수 있었다.

## 마치며

지금은 Claude Desktop에 Google 검색 MCP만 연결해도 비슷한 작업을 할 수 있다. 그래도 도구를 계속 보완하며 GitHub에 친숙해졌고, GitHub 앱으로 모니터링하다보니 다른 bugbounty, 모의해킹 오픈소스 도구들을 많이 알게 된 것 같다.

무엇보다 해당 도구 개발 경험을 통해 이후 Code Assistant로 이런저런 자동화 개발 작업을 많이 하게 되었다.
