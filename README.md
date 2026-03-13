# Agentic Workflow Guide

> **1인 창업자를 위한 AI 개발팀 구축 실전 가이드**
>
> OpenClaw(비서) + OpenHands(자율코더) + Claude Code(정밀도구)
> = WhatsApp에서 지시 → AI가 코딩 → PR → 리뷰 → 자동 배포

## TL;DR

| 항목 | 내용 |
|------|------|
| **목표** | AI 에이전트를 "독립된 부서"처럼 세팅하여 1인이 10인 팀 역할 수행 |
| **월 비용** | ~38-56만원 (vs 주니어 개발자 1명 250-350만원) |
| **핵심 도구** | OpenClaw ⭐310K, OpenHands ⭐69K, Claude Code, CodeRabbit |
| **핵심 패턴** | WhatsApp 지시 → AI 자동 구현 → PR → 리뷰 → 사람은 Merge만 |

## 풀 아키텍처 (완성형)

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                  │
│   대표 (WhatsApp / Telegram)                                     │
│         │                                                        │
│         ▼                                                        │
│   ┌─────────────┐                                                │
│   │  OpenClaw    │ 24/7 AI 비서 (⭐310K)                         │
│   │  (접수/분류/  │ • 영구 메모리                                   │
│   │   위임/보고)  │ • 멀티에이전트 오케스트레이터                      │
│   └──────┬──────┘                                                │
│          │                                                        │
│     ┌────┴────┐                                                   │
│     ▼         ▼                                                   │
│  ┌────────┐ ┌────────────┐                                        │
│  │OpenHands│ │ Claude Code │                                       │
│  │(⭐69K)  │ │ (정밀 코딩)  │                                       │
│  │자율 코딩 │ │ 아키텍처/   │                                       │
│  │Self-Heal│ │ 리팩터링     │                                       │
│  └───┬────┘ └─────┬──────┘                                        │
│      └──────┬─────┘                                               │
│             ▼                                                     │
│   GitHub → CodeRabbit 리뷰 → [Merge] → Vercel 자동 배포           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

> 상세 아키텍처: [docs/full-architecture.md](docs/full-architecture.md)

## 검증된 수익 사례 (하이라이트)

| 창업자 | 제품 | 월 매출 | 방법 |
|--------|------|---------|------|
| Pieter Levels | Photo AI 외 | **$210K/월** | PHP+jQuery, 직원 0명 |
| Maor Shlomo | Base44 | $3.5M ARR → **$80M 엑싯** | Claude LLM 기반 바이브코딩 플랫폼 |
| Danny Postma | HeadshotPro | **$300K/월** | SEO + AI 래퍼, 솔로 |
| Nick Dobos | BoredHumans | **$733K/월** | 100+ AI 도구, 광고 수익 |
| Theanna (Anna) | Theanna | $203K ARR | **비개발자**, Claude Code + Lovable |

> 상세 사례: [docs/case-studies.md](docs/case-studies.md)

## 문서 구조

| 문서 | 설명 |
|------|------|
| **[docs/full-architecture.md](docs/full-architecture.md)** | **풀 아키텍처 기획서 (OpenClaw + OpenHands + Claude Code)** |
| [docs/openclaw-guide.md](docs/openclaw-guide.md) | OpenClaw 상세 가이드 (⭐310K, 설치, 연동) |
| [docs/tools-catalog.md](docs/tools-catalog.md) | 검증된 도구 카탈로그 (Stars, 기능, 비용) |
| [docs/architecture-patterns.md](docs/architecture-patterns.md) | 4가지 기본 아키텍처 패턴 |
| [docs/advanced-patterns.md](docs/advanced-patterns.md) | 고급 패턴 (Continuous Claude, 병렬 에이전트 등) |
| [docs/case-studies.md](docs/case-studies.md) | 실명+실매출 검증 사례 + 한국 커뮤니티 |
| [docs/cost-analysis.md](docs/cost-analysis.md) | 하드웨어 + 월 운영비 + 비교 분석 |
| [docs/setup-guide.md](docs/setup-guide.md) | Phase 1~3 단계별 셋업 가이드 |
| [docs/awesome-list.md](docs/awesome-list.md) | 관련 리포/도구/커뮤니티 큐레이션 |
| [tools/templates/](tools/templates/) | GitHub Actions 워크플로우 템플릿 |

## 핵심 도구 스택

### Tier 1: 완성형 3종 세트 (우리 아키텍처의 핵심)

| 도구 | Stars | 역할 | 비용 |
|------|-------|------|------|
| [**OpenClaw**](https://github.com/openclaw/openclaw) | **310,000** | 24/7 AI 비서 + 오케스트레이터 | 무료 + API |
| [**OpenHands**](https://github.com/OpenHands/OpenHands) | **69,000** | 자율 코딩 에이전트 (Self-Healing) | 무료 + API |
| [**Claude Code**](https://claude.ai) | - | 정밀 코딩 (아키텍처/리팩터링) | $100-200/mo |

### Tier 2: 보조 도구

| 도구 | Stars | 역할 | 비용 |
|------|-------|------|------|
| [CodeRabbit](https://coderabbit.ai) | - | AI 코드 리뷰 | 무료~$15/mo |
| [GitHub gh-aw](https://github.com/github/gh-aw) | 4,100 | 마크다운→GitHub Actions | 무료 |
| [Daytona](https://github.com/daytonaio/daytona) | 64,500 | AI 코드 샌드박스 | 무료 |
| [Aider](https://github.com/Aider-AI/aider) | - | 터미널 페어 프로그래밍 | 무료 + API |
| [Dify](https://github.com/langgenius/dify) | 133,000 | 비주얼 워크플로우 빌더 | 무료~ |

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

## 4단계 구축 로드맵

### Phase 0: 사전 준비 (1시간)
- GitHub 리포 + Anthropic API 키 + Vercel + Tailscale

### Phase 1: OpenClaw 설치 (Day 1 오전)
- 24/7 AI 비서 가동, WhatsApp/Telegram 연결

### Phase 2: OpenHands 설치 (Day 1 오후)
- Docker + GitHub Resolver, Issue→자동PR 파이프라인

### Phase 3: 브릿지 연결 (Day 2)
- OpenClaw → GitHub Issue → OpenHands 자동 위임

### Phase 4: Claude Code + 고급 설정 (Day 3)
- 정밀 코딩 연동, CodeRabbit, Self-Healing CI

> 상세 가이드: [docs/full-architecture.md](docs/full-architecture.md)

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
