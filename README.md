# Agentic Workflow Guide

> **1인 창업자를 위한 AI 개발팀 구축 실전 가이드**
>
> "500만원 PC + 무제한 Claude + OpenHands" 조합은 망상이 아니라, 글로벌 Top-tier 솔로 파운더들이 실제로 구축하고 있는 아키텍처입니다.

## TL;DR

| 항목 | 내용 |
|------|------|
| **목표** | AI 에이전트를 "독립된 부서"처럼 세팅하여 1인이 10인 팀 역할 수행 |
| **월 비용** | ~37만원 (vs 주니어 개발자 1명 250-350만원) |
| **핵심 도구** | OpenHands ⭐69K, GitHub gh-aw ⭐4.1K, Dify ⭐133K |
| **핵심 패턴** | Issue 작성 → AI 자동 구현 → PR 생성 → 사람은 Merge만 |

## 시스템 블록 구조

```
┌─────────────────────────────────────────────────────────────┐
│                    Agentic Development Pipeline              │
│                                                              │
│  ┌──────────┐    ┌──────────────┐    ┌──────────┐           │
│  │ PM Block │───→│ Coder Block  │───→│ Review   │───→ Deploy│
│  │          │    │              │    │ Block    │           │
│  │ Notion   │    │ OpenHands    │    │ CodeRab- │    Vercel │
│  │ Linear   │    │ Claude Code  │    │ bit      │    GitHub │
│  │ GitHub   │    │ Aider        │    │ Claude   │    Actions│
│  │ Issues   │    │              │    │          │           │
│  └──────────┘    └──────┬───────┘    └──────────┘           │
│                         │                                    │
│                    Self-Healing                               │
│                    Loop (자동                                  │
│                    에러 수정)                                   │
└─────────────────────────────────────────────────────────────┘
```

## 문서 구조

| 문서 | 설명 |
|------|------|
| [docs/tools-catalog.md](docs/tools-catalog.md) | 검증된 도구 카탈로그 (Stars, 기능, 비용) |
| [docs/architecture-patterns.md](docs/architecture-patterns.md) | 4가지 아키텍처 패턴 상세 |
| [docs/cost-analysis.md](docs/cost-analysis.md) | 하드웨어 + 월 운영비 + 비교 분석 |
| [docs/setup-guide.md](docs/setup-guide.md) | Phase 1~3 단계별 셋업 가이드 |
| [docs/awesome-list.md](docs/awesome-list.md) | 관련 리포/도구/커뮤니티 큐레이션 |
| [tools/templates/](tools/templates/) | GitHub Actions 워크플로우 템플릿 |

## 검증된 핵심 도구 (Top 10)

| # | 도구 | Stars | 역할 | 비용 |
|---|------|-------|------|------|
| 1 | [OpenHands](https://github.com/OpenHands/OpenHands) | 69,000 | 자율 코딩 에이전트 | 무료 + LLM API |
| 2 | [Dify](https://github.com/langgenius/dify) | 133,000 | 비주얼 워크플로우 빌더 | 무료~$159/mo |
| 3 | [Daytona](https://github.com/daytonaio/daytona) | 64,500 | AI 코드 샌드박스 (90ms 기동) | 무료 |
| 4 | [GitHub gh-aw](https://github.com/github/gh-aw) | 4,100 | 마크다운→GitHub Actions 변환 | 무료 |
| 5 | [ruflo](https://github.com/ruvnet/ruflo) | 20,800 | Claude 멀티에이전트 스웜 | 무료 |
| 6 | [Aider](https://github.com/Aider-AI/aider) | - | 터미널 페어 프로그래밍 | 무료 + LLM API |
| 7 | [Claude Code](https://claude.ai) | - | 만능 AI 코더 (CLI) | $100-200/mo |
| 8 | [CodeRabbit](https://coderabbit.ai) | - | AI 코드 리뷰 | 무료~$15/mo |
| 9 | [GPT-Engineer](https://github.com/gpt-engineer-org/gpt-engineer) | - | 프로젝트 부트스트랩 | 무료 + API |
| 10 | [Sweep AI](https://github.com/sweepai/sweep) | - | Issue→PR 자동화 | 무료 |

## Quick Start (5분 안에 시작)

```bash
# 1. Claude Code 설치 (이미 있다면 스킵)
npm install -g @anthropic-ai/claude-code

# 2. OpenHands 설치 (Docker 필요)
docker pull ghcr.io/openhands/openhands:latest

# 3. Aider 설치
pip install aider-chat

# 4. GitHub Actions 워크플로우 복사
cp tools/templates/openhands-resolver.yml .github/workflows/
```

## 3단계 도입 로드맵

### Phase 1: 즉시 시작 (추가 비용 $0)
- Claude Code로 코딩 → GitHub에 push → Vercel 자동 배포

### Phase 2: 리눅스 PC 도입 (일회성 ~300만원)
- Ubuntu + Docker + OpenHands 설치
- GitHub Issue → 자동 PR 파이프라인 가동

### Phase 3: 풀 오토메이션 (월 ~37만원)
- PM(Notion) → Coder(OpenHands) → Review(CodeRabbit) → Deploy(Vercel)
- 대표는 [Merge] 버튼만 클릭

## 현실 체크

### AI가 잘하는 것
- CRUD / 보일러플레이트 코드
- 테스트 작성 및 버그 수정
- 랜딩 페이지 / API 연동
- 콘텐츠 자동화 파이프라인

### 아직 사람이 필요한 것
- 아키텍처 의사결정
- 비즈니스 로직 (도메인 판단)
- 보안 최종 리뷰
- 프로덕션 장애 대응

---

> **결론**: AI 개발팀 = 피곤 모르는 주니어 10명.
> 단, 시니어 아키텍트(대표)가 방향을 잡아줘야 합니다.

## License

MIT

## Contributing

이슈나 PR 환영합니다. 실제 사용 사례를 공유해 주세요.
