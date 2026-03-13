# 고급 아키텍처 패턴 (심화)

> GitHub에서 발견된 최신 고급 패턴들

## 패턴 5: Continuous Claude (무한 루프 에이전트)

**GitHub**: [AnandChowdhary/continuous-claude](https://github.com/AnandChowdhary/continuous-claude)

```
while true:
  1. 새 브랜치 생성
  2. Claude Code 실행 (프롬프트 + SHARED_TASK_NOTES.md 컨텍스트)
  3. Claude가 코드 생성, 커밋
  4. Push → PR 생성
  5. CI 체크 대기
  6. PASS → PR 머지 → SHARED_TASK_NOTES.md 업데이트
  7. FAIL → 브랜치 폐기
  8. 다음 이터레이션으로 루프
```

### 핵심: SHARED_TASK_NOTES.md
- 이터레이션 간 **영구 메모리** 역할
- 이전 작업 결과, 남은 할 일, 컨텍스트 저장
- 용도: 밤새 테스트 커버리지 0% → 80% 달성 등 대규모 작업

### 장점
- 사람이 자는 동안 AI가 일함
- 각 PR이 독립적이라 실패해도 안전
- CI 통과한 것만 머지되므로 품질 보장

---

## 패턴 6: Composio Agent Orchestrator (병렬 멀티에이전트)

**GitHub**: [ComposioHQ/agent-orchestrator](https://github.com/ComposioHQ/agent-orchestrator)

```
상위 목표 (High-level objective)
    ↓
Planner가 서브태스크로 분해
    ↓
N개의 에이전트 병렬 생성
    ↓
각 에이전트:
  ├── 독립 git worktree 할당
  ├── 독립 브랜치 + PR
  ├── 언어/패키지 매니저 자동 감지
  ├── CI 실패 → 에러 주입 → 자동 수정
  └── 리뷰어 코멘트 → 자동 대응
```

### 검증 규모
- **40,000줄** TypeScript 프로젝트
- **3,288개** 테스트 케이스
- 에이전트가 전부 처리

---

## 패턴 7: Claude Code GitHub Actions (공식)

**GitHub**: [anthropics/claude-code-action](https://github.com/anthropics/claude-code-action)

```yaml
# .github/workflows/claude.yml
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

jobs:
  claude:
    if: contains(github.event.comment.body, '@claude')
    runs-on: ubuntu-latest
    permissions:
      contents: write
      pull-requests: write
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
```

### 작동 방식
1. 이슈나 PR에 `@claude` 멘션
2. Claude가 전체 컨텍스트 읽음
3. 코드 구현 또는 리뷰/수정
4. 브랜치 생성 + 커밋 + PR

### 통계
- 50K+ 라인 프로젝트에서 코드 리뷰 시간 **45% 단축**
- 채택 팀의 60%+가 `-p` (headless) 모드를 자동화에 활용

---

## 패턴 8: GitHub Copilot Coding Agent

```
GitHub Issue (또는 Jira 티켓 + "copilot" 라벨)
    ↓
Copilot이 GitHub Actions 러너 기동 (샌드박스)
    ↓
copilot/issue-{N} 브랜치 생성
    ↓
코드베이스 + 이슈 컨텍스트 + 리포 인스트럭션 읽음
    ↓
코드 작성, 린터/테스트 반복 실행
    ↓
Draft PR 생성
    ↓
사람 리뷰 → 승인 → 머지
```

**제약**: Copilot은 `copilot/*` 브랜치만 push 가능 (main/master 직접 접근 불가)

---

## 패턴 9: Self-Healing CI 실전 사례 (Elastic)

**출처**: [Elastic Engineering Blog](https://www.elastic.co/search-labs/blog/ci-pipelines-claude-ai-agent)

```
Renovate 봇이 의존성 업데이트 PR 생성
    ↓
CI 빌드 실패 (Gradle 에러)
    ↓
Claude 에이전트 트리거 (--allowedTools 제약)
    ↓
에이전트가 Gradle 실패 로그 읽음
    ↓
코드 수정 → 커밋 → 파이프라인 재실행
    ↓
성공
```

### 실적
- 파일럿 1개월: **24개 깨진 PR 자율 수정**
- 절감: **~20 개발자-일**

---

## 추가 발견된 핵심 도구

| 도구 | Stars | 역할 | 비용 |
|------|-------|------|------|
| [Continuous Claude](https://github.com/AnandChowdhary/continuous-claude) | - | 무한 루프 자율 에이전트 | API 비용만 |
| [Composio Agent Orchestrator](https://github.com/ComposioHQ/agent-orchestrator) | - | 병렬 멀티에이전트 | 무료 |
| [anthropics/claude-code-action](https://github.com/anthropics/claude-code-action) | - | Claude Code GitHub Actions | API 비용만 |
| [Devika](https://github.com/stitionai/devika) | - | 오픈소스 Devin 대안 | 무료 |
| [smol-developer](https://github.com/smol-ai/developer) | 11,800 | 200줄 미니 에이전트 | 무료 |
| [MetaGPT](https://github.com/geekan/MetaGPT) | - | PM/아키텍트/개발자 역할 멀티에이전트 | 무료 |
| [Devin](https://devin.ai) | 비공개 | 풀 자율 AI 개발자 | $20/mo + ACU |

### GitHub 공식 "Awesome Continuous AI"

**리포**: [githubnext/awesome-continuous-ai](https://github.com/githubnext/awesome-continuous-ai)

| 카테고리 | 도구들 |
|----------|--------|
| Continuous Triage | Ultralytics Actions, Dosu, GenAI Issue Labeller |
| Continuous Documentation | Penify.dev, DeepWiki, cADR |
| Continuous Code Review | CodeRabbit, Gemini Code Assist, Shippie |
| Continuous Test | SoftwareTesting AI, DiffBlue |
| Continuous AI Engineering | Continuous Claude, alignment testing |
| Agentic Frameworks | Copilot Agent, Claude Code Actions, Amazon Q |

---

## 비용 비교: Claude Max vs API (정밀 분석)

### 실제 비교 데이터

| 시나리오 | API 비용 | Max 구독 | 절감률 |
|---------|---------|---------|--------|
| 8개월 ~100억 토큰 | $15,000 | $1,600 ($200×8) | **89%** |
| 1개월 헤비 코딩 | $5,623 | $200 | **96%** |
| 일반 개발자 (일 $12 미만) | ~$360/월 | $200/월 | **44%** |

### 핵심 제한사항
- **Claude Max는 프로그래밍 방식 호출 불가** (Agent SDK 연동 안 됨)
- OpenHands 같은 외부 에이전트 = **API Key 별도 필요**
- 현실적 비용 = **Max $200 + API $50-100 = $250-300/월**

### 최적 조합 (솔로 파운더)
```
일상 코딩:  Claude Max $200/월 (Claude Code 사실상 무제한)
자율 에이전트: Claude API ~$50-100/월 (OpenHands/Continuous Claude)
코드 리뷰:   CodeRabbit 무료
CI/CD:      GitHub Actions 무료
────────────────────────────────
총 월 비용:  ~$250-300 (약 34-40만원)
```

---

## 하드웨어 현실 체크

### API 방식이면 500만원 PC 불필요

| 시나리오 | 하드웨어 | 비용 |
|---------|---------|------|
| **API만 사용 (추천)** | Mac Mini M4 또는 저가 리눅스 박스 | **60-200만원** |
| **로컬 모델도 구동** | RTX 4090 + 고사양 | 350-500만원 |

- OpenHands는 API 호출 방식 → GPU 불필요
- Docker만 돌리면 됨 → **RAM이 핵심** (32-64GB)
- Mac Mini M4 Pro 64GB: ~200만원, 24/7 서버로 운영 가능
- 클라우드 VM ($40-80/월) 대비 8-12개월 만에 손익분기

### AMD + OpenHands 공식 협업
- AMD Ryzen AI Max+ 395: 126 AI TOPS
- 내장 GPU로 Qwen3-Coder-30B 구동 가능
- OpenHands가 AMD와 공식 협업 발표

**출처**: [AMD - OpenHands Collaboration](https://www.amd.com/en/developer/resources/technical-articles/2025/OpenHands.html)
