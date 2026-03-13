# Agentic Workflow 아키텍처 패턴

> GitHub에서 확인된 4가지 실전 패턴

## 패턴 1: Issue-Driven 자율 개발 (가장 완성형)

**개요**: GitHub Issue만 작성하면 AI가 알아서 코드를 짜고 PR을 올리는 패턴

**사용 도구**: OpenHands Resolver + CodeRabbit + GitHub Actions

```
┌─────────────────────────────────────────────────────────────────┐
│                  Issue-Driven Autonomous Pipeline                 │
│                                                                   │
│  1. 대표님이 GitHub Issue 작성                                      │
│     "S1 할부 결제 API 만들어줘"                                      │
│     → fix-me 라벨 또는 @openhands-agent 멘션                       │
│                          │                                        │
│                          ▼                                        │
│  2. GitHub Actions 자동 트리거                                      │
│     → OpenHands 에이전트 기동                                       │
│                          │                                        │
│                          ▼                                        │
│  3. Docker 샌드박스에서 작업                                         │
│     ┌─────────────────────────────────┐                           │
│     │  에이전트 루프:                     │                           │
│     │  코드 작성 → npm run dev          │                           │
│     │  → 에러? → 로그 읽기 → 수정        │ ← Self-Healing Loop       │
│     │  → 다시 실행 → 성공!               │                           │
│     └─────────────────────────────────┘                           │
│                          │                                        │
│                          ▼                                        │
│  4. Pull Request 자동 생성                                          │
│     → CodeRabbit 자동 코드 리뷰                                     │
│                          │                                        │
│                          ▼                                        │
│  5. 대표님: 금요일 밤 맥주 마시며 코드 확인                              │
│     → [Merge] 클릭                                                 │
│                          │                                        │
│                          ▼                                        │
│  6. GitHub Actions → Vercel 자동 배포                               │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

### 구현 방법

1. **OpenHands Resolver 워크플로우 추가**:
```yaml
# .github/workflows/openhands-resolver.yml
name: OpenHands Issue Resolver
on:
  issues:
    types: [labeled]
  issue_comment:
    types: [created]

jobs:
  resolve:
    if: |
      (github.event_name == 'issues' &&
       contains(github.event.issue.labels.*.name, 'fix-me')) ||
      (github.event_name == 'issue_comment' &&
       contains(github.event.comment.body, '@openhands-agent'))
    uses: OpenHands/OpenHands/.github/workflows/openhands-resolver.yml@main
    secrets:
      LLM_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

2. **CodeRabbit 설치**: GitHub Marketplace에서 앱 설치 (1분)

3. **Vercel 연동**: GitHub 리포 연결 → main 브랜치 머지 시 자동 배포

### 장점
- 가장 높은 자동화 수준
- 사람은 이슈 작성 + 머지만 하면 됨
- 표준 GitHub 워크플로우와 100% 호환

### 단점
- Docker 환경 필요 (리눅스 PC 또는 클라우드)
- 복잡한 비즈니스 로직은 이슈 설명을 상세히 써야 함
- LLM API 비용 발생

---

## 패턴 2: 대화형 개발 파이프라인

**개요**: 터미널에서 AI와 대화하며 코딩. 가장 진입장벽이 낮음.

**사용 도구**: Claude Code / Aider + Git + CI/CD

```
┌─────────────────────────────────────────────────────────────┐
│              Conversational Development Pipeline              │
│                                                               │
│  1. 터미널에서 AI에게 지시                                       │
│     $ claude "로그인 API에 rate limiting 추가해줘"               │
│     또는                                                       │
│     $ aider --model claude-3-opus                              │
│     > rate limiting 추가해줘                                    │
│                          │                                     │
│                          ▼                                     │
│  2. AI가 코드 수정 + 자동 Git 커밋                                │
│     [main abc1234] feat: add rate limiting to login API        │
│                          │                                     │
│                          ▼                                     │
│  3. git push                                                   │
│                          │                                     │
│                          ▼                                     │
│  4. CI 실행                                                    │
│     ├── 성공 → 자동 배포                                        │
│     └── 실패 → AI에게 에러 로그 전달 → 수정 → 재push              │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### 구현 방법

```bash
# Claude Code 방식 (이미 사용 중)
claude "payment API에 할부 옵션 추가해줘. 3, 6, 12개월 지원"

# Aider 방식
cd my-project
aider --model claude-3-opus
> payment API에 할부 옵션 추가해줘
```

### 장점
- **즉시 시작 가능** (설치만 하면 됨)
- Docker 불필요 (Aider)
- 직관적인 대화형 인터페이스
- 기존 워크플로우에 자연스럽게 통합

### 단점
- 사람이 계속 터미널 앞에 있어야 함
- 완전 자율은 아님 (반자동)

### 팁: claude -p로 자동화
```bash
# 스크립트로 자동화 가능 (orchestrator.py 패턴)
claude -p "articles.json의 article-001 원고를 생성해줘" > output.md
```

---

## 패턴 3: 멀티 에이전트 오케스트레이션 (최종 목표)

**개요**: 여러 AI 에이전트가 각자 역할(PM, 개발자, 리뷰어)을 수행

**사용 도구**: ruflo / Dify / 커스텀 오케스트레이터

```
┌─────────────────────────────────────────────────────────────┐
│              Multi-Agent Orchestration                        │
│                                                               │
│  ┌──────────┐     ┌──────────┐     ┌──────────┐              │
│  │ PM Agent │────→│ Coder    │────→│ Review   │              │
│  │          │     │ Agent    │     │ Agent    │              │
│  │ Claude   │     │ OpenHands│     │ CodeRab- │              │
│  │ Opus     │     │ / Claude │     │ bit /    │              │
│  │          │     │ Code     │     │ Claude   │              │
│  │ 역할:     │     │          │     │          │              │
│  │ - 요구사항│     │ 역할:     │     │ 역할:     │              │
│  │   분석    │     │ - 구현    │     │ - 코드   │              │
│  │ - 태스크  │     │ - 테스트  │     │   품질    │              │
│  │   분해    │     │ - 디버깅  │     │ - 보안   │              │
│  │ - 스펙   │     │ - PR     │     │ - 피드백  │              │
│  │   작성    │     │   생성    │     │          │              │
│  └──────────┘     └──────────┘     └──────────┘              │
│        │                                  │                   │
│        │         ┌──────────┐             │                   │
│        └────────→│ Deploy   │←────────────┘                   │
│                  │ Agent    │                                  │
│                  │ GitHub   │                                  │
│                  │ Actions  │                                  │
│                  └──────────┘                                  │
│                                                               │
│  공유 상태: Git 리포 + Turso DB + Event Stream                  │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### Agent OS 구현

```
.claude/
├── agents/
│   ├── pm.md           # PM 에이전트 프롬프트
│   ├── developer.md    # 개발자 에이전트 프롬프트
│   ├── reviewer.md     # 리뷰어 에이전트 프롬프트
│   └── deployer.md     # 배포 에이전트 프롬프트
├── CLAUDE.md           # 전체 조율
└── rules/
    ├── coding-style.md
    └── security.md
```

### ruflo로 Claude 스웜 구축

```bash
# ruflo 설치 후
ruflo init my-project
ruflo add-agent pm --model claude-opus
ruflo add-agent coder --model claude-sonnet
ruflo add-agent reviewer --model claude-haiku
ruflo run "결제 시스템 리팩터링"
```

### 장점
- 가장 높은 자동화 + 품질
- 각 에이전트가 전문 역할 수행
- 대규모 작업도 분할 처리 가능

### 단점
- 셋업 복잡도 높음
- LLM 비용 높음 (여러 에이전트 동시 실행)
- 에이전트 간 조율 로직 필요

---

## 패턴 4: Self-Healing CI/CD

**개요**: CI가 실패하면 AI가 자동으로 에러를 수정하는 패턴

**사용 도구**: GitHub Actions + OpenHands / Claude Code

```
┌─────────────────────────────────────────────────────────────┐
│                  Self-Healing CI/CD                           │
│                                                               │
│  코드 Push                                                    │
│      │                                                        │
│      ▼                                                        │
│  GitHub Actions CI 실행                                       │
│      │                                                        │
│      ▼                                                        │
│  테스트 실행                                                   │
│      │                                                        │
│      ├── 성공 ──→ 배포                                         │
│      │                                                        │
│      └── 실패 ──→ AI 에이전트 트리거                             │
│                      │                                        │
│                      ▼                                        │
│                  에러 로그 분석                                  │
│                      │                                        │
│                      ▼                                        │
│                  코드 자동 수정                                  │
│                      │                                        │
│                      ▼                                        │
│                  새 커밋 Push                                   │
│                      │                                        │
│                      ▼                                        │
│                  CI 재실행 ──→ 성공 ──→ 배포                     │
│                                                               │
│  * 최대 3회 재시도 후 실패 시 사람에게 알림                         │
│                                                               │
└─────────────────────────────────────────────────────────────┘
```

### 구현 워크플로우

```yaml
# .github/workflows/self-healing-ci.yml
name: Self-Healing CI
on: [push]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm test

  auto-fix:
    needs: test
    if: failure()
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: AI Fix
        uses: OpenHands/OpenHands/.github/workflows/openhands-resolver.yml@main
        with:
          prompt: "CI 테스트가 실패했습니다. 로그를 분석하고 수정해주세요."
```

### 장점
- CI 실패에 대한 자동 대응
- 개발자 개입 최소화
- 빠른 피드백 루프

### 단점
- 무한 루프 방지 로직 필요 (최대 재시도 횟수)
- 근본 원인이 복잡한 경우 한계
- API 비용 증가 가능

---

## 패턴 선택 가이드

| 상황 | 추천 패턴 |
|------|----------|
| **지금 당장 시작** | 패턴 2 (대화형) |
| **리눅스 PC 도입 후** | 패턴 1 (Issue-Driven) |
| **CI 안정성 강화** | 패턴 4 (Self-Healing) |
| **대규모 프로젝트** | 패턴 3 (멀티 에이전트) |
| **1인 창업자 최적** | 패턴 2 → 1 → 4 순차 도입 |

---

## 모든 패턴의 공통점

1. **Git이 조율 레이어** — 모든 도구가 Git으로 통합됨
2. **Docker가 안전 장치** — AI가 격리 환경에서 코드 실행
3. **Self-Healing이 핵심** — 에러를 스스로 수정하는 능력
4. **Human-in-the-Loop** — 최종 리뷰는 반드시 사람이
5. **점진적 도입** — 하나씩 추가하며 확장
