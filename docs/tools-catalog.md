# Agentic Workflow 도구 카탈로그

> GitHub에서 검증된 실제 도구들의 상세 분석

## 1. OpenHands (구 OpenDevin)

| 항목 | 내용 |
|------|------|
| **GitHub** | [OpenHands/OpenHands](https://github.com/OpenHands/OpenHands) |
| **Stars** | ⭐ 69,000+ |
| **라이선스** | MIT (코어) / Source-available (엔터프라이즈) |
| **언어** | Python 76%, TypeScript 22% |
| **기여자** | 482+ |
| **최신 버전** | v1.3.0 (2026.02) |

### 핵심 기능
- **자율 코딩**: 프롬프트만 주면 코드 작성 → 테스트 → 에러 수정 → PR 생성까지 자동
- **Self-Healing Loop**: 에러 로그를 스스로 읽고 코드를 수정. 사람이 에러를 복붙해줄 필요 없음
- **Docker 샌드박스**: 격리된 환경에서 안전하게 코드 실행
- **GitHub Resolver**: `@openhands-agent` 멘션 또는 `fix-me` 라벨로 자동 이슈 해결

### V1 아키텍처 (Event-Sourcing 기반)

```
┌─────────────────────────────────────────────────┐
│                 OpenHands V1                      │
│                                                   │
│  ┌──────────────┐                                 │
│  │ Software      │  Event Stream (불변 로그)        │
│  │ Agent SDK     │ ──────────────────────────→     │
│  │ (openhands-   │                                 │
│  │  sdk)         │ ←──────────────────────────     │
│  └──────────────┘   Observations                  │
│                                                   │
│  ┌──────────────┐  ┌──────────────┐               │
│  │ Agent Server  │  │ Docker       │               │
│  │ (원격 실행)    │  │ Sandbox      │               │
│  └──────────────┘  │              │               │
│                     │ Terminal     │               │
│  ┌──────────────┐  │ FileEditor   │               │
│  │ Workspace     │  │ TaskTracker  │               │
│  │ (로컬/원격)    │  │ Browser      │               │
│  └──────────────┘  └──────────────┘               │
│                                                   │
│  5가지 배포 모드:                                   │
│  CLI | Local GUI | Cloud | Enterprise | SDK        │
└─────────────────────────────────────────────────┘
```

### 배포 모드 5가지
| 모드 | 설명 | 대상 |
|------|------|------|
| **CLI** | 터미널에서 직접 사용 | 개발자 |
| **Local GUI** | React SPA 웹 UI | 팀 |
| **Cloud** | Slack/Jira/Linear 연동, RBAC | SaaS |
| **Enterprise** | K8s 자체 호스팅 | 대기업 |
| **SDK** | 임베드용 Python 라이브러리 | 커스텀 |

### OpenHands Resolver (CI/CD 핵심)

```yaml
# .github/workflows/openhands-resolver.yml
# 이슈에 fix-me 라벨 → 자동으로 코드 수정 PR 생성
on:
  issues:
    types: [labeled]
  issue_comment:
    types: [created]

# @openhands-agent 멘션으로도 트리거 가능
```

**지원 플랫폼**: GitHub, GitLab, Bitbucket, Azure DevOps

### 벤치마크
- **SWE-Bench**: 77.6점 (이슈 해결 능력)
- OpenHands Index: Claude Opus, GPT-5.2-Codex가 최고 성능

---

## 2. GitHub gh-aw (공식 Agentic Workflows)

| 항목 | 내용 |
|------|------|
| **GitHub** | [github/gh-aw](https://github.com/github/gh-aw) |
| **Stars** | ⭐ 4,100 |
| **상태** | Technical Preview |

### 핵심 기능
- **마크다운으로 워크플로우 작성** → 자동으로 GitHub Actions YAML 컴파일
- GitHub 내부에서 **100개+ 워크플로우** 운영 중
- Copilot, Claude, Codex를 AI 엔진으로 사용 가능

### 보안 모델
- 기본 Read-only, 쓰기는 sanitized safe outputs
- 샌드박스 실행, 도구 허용리스트, 네트워크 격리
- SHA-pinned 의존성

### 샘플 워크플로우 (50개+)
| 워크플로우 | 설명 |
|-----------|------|
| Issue Triage | 이슈 자동 분류 및 라벨링 |
| CI Failure Investigation | CI 실패 원인 자동 분석 |
| Code Review Automation | PR 자동 코드 리뷰 |
| Security Detection | 악성 코드 탐지 |
| Daily Summary | 일일 활동 요약 생성 |
| `/command` Trigger | 코멘트 명령으로 워크플로우 실행 |

**샘플 리포**: [githubnext/agentics](https://github.com/githubnext/agentics) (⭐453)

---

## 3. Dify

| 항목 | 내용 |
|------|------|
| **GitHub** | [langgenius/dify](https://github.com/langgenius/dify) |
| **Stars** | ⭐ 133,000 |
| **가격** | 무료 (셀프호스팅) ~ $159/mo (클라우드) |

### 핵심 기능
- **비주얼 워크플로우 빌더**: 드래그앤드롭으로 AI 파이프라인 구축
- 멀티 에이전트 오케스트레이션
- RAG (문서 기반 검색) 내장
- 200+ 모델 지원 (Claude, GPT, 로컬 모델)

### 용도
- 복잡한 멀티스텝 AI 워크플로우를 코드 없이 설계
- 챗봇, 에이전트, 워크플로우 올인원

---

## 4. Daytona

| 항목 | 내용 |
|------|------|
| **GitHub** | [daytonaio/daytona](https://github.com/daytonaio/daytona) |
| **Stars** | ⭐ 64,500 |
| **가격** | 무료 (오픈소스) |

### 핵심 기능
- AI 코드 실행용 **보안 샌드박스**
- **90ms** 만에 컨테이너 기동 (OCI 호환)
- OpenHands의 대체 런타임으로 채택됨
- 격리된 환경에서 AI가 자유롭게 코드 실행 가능

---

## 5. ruflo

| 항목 | 내용 |
|------|------|
| **GitHub** | [ruvnet/ruflo](https://github.com/ruvnet/ruflo) |
| **Stars** | ⭐ 20,800 |

### 핵심 기능
- **Claude 전용** 멀티에이전트 스웜 오케스트레이션
- 여러 Claude 에이전트가 협업하여 대규모 작업 처리
- 에이전트 간 역할 분담 및 통신

---

## 6. Aider

| 항목 | 내용 |
|------|------|
| **GitHub** | [Aider-AI/aider](https://github.com/Aider-AI/aider) |
| **가격** | 무료 + LLM API 비용 ($50-200/mo) |

### 핵심 기능
- **터미널 페어 프로그래밍**: `pip install aider-chat` 한 줄로 설치
- Git 직접 연동 — 파일 수정 후 자동 커밋
- Docker 불필요, 가벼움
- Claude, GPT 모델 지원
- 기존 프로젝트에 바로 적용 가능

### 사용법
```bash
# 설치
pip install aider-chat

# 프로젝트에서 실행
cd my-project
aider --model claude-3-opus

# 대화형으로 코딩
> 로그인 API에 rate limiting 추가해줘
```

---

## 7. Claude Code

| 항목 | 내용 |
|------|------|
| **제공** | Anthropic |
| **가격** | Claude Max $100-200/mo |

### 핵심 기능
- CLI 기반 AI 코딩 어시스턴트
- `claude -p` **프린트 모드**로 스크립트 자동화 가능 (핵심!)
- 파일 읽기/쓰기, 터미널 명령 실행, Git 조작
- `.claude/agents/` 디렉터리로 도메인별 에이전트 정의

### 자동화 패턴 (orchestrator.py 스타일)
```python
import subprocess
import os

env = os.environ.copy()
env.pop("CLAUDECODE", None)  # 중첩 세션 방지

result = subprocess.run(
    ["claude", "-p", "할부 결제 API 만들어줘"],
    capture_output=True, text=True, env=env
)
```

---

## 8. CodeRabbit

| 항목 | 내용 |
|------|------|
| **사이트** | [coderabbit.ai](https://coderabbit.ai) |
| **가격** | 무료 (오픈소스) ~ $15/mo (프라이빗) |

### 핵심 기능
- **AI 자동 코드 리뷰**: PR 올라오면 즉시 리뷰
- 보안 취약점, 코드 품질, 버그 탐지
- GitHub App으로 설치 (1분)
- 1인 개발자에게 "코드 리뷰어" 역할

---

## 9. GPT-Engineer

| 항목 | 내용 |
|------|------|
| **GitHub** | [gpt-engineer-org/gpt-engineer](https://github.com/gpt-engineer-org/gpt-engineer) |
| **가격** | 무료 + API 비용 |

### 핵심 기능
- 프롬프트 → **전체 프로젝트 구조** 자동 생성
- 명확화 질문 → 코드 생성 → 디렉터리 구조까지
- 새 프로젝트 부트스트랩에 최적

---

## 10. Agent OS 패턴

| 항목 | 내용 |
|------|------|
| **출처** | [buildermethods.com/agent-os](https://buildermethods.com/agent-os) |
| **대상** | Claude Code 사용자 |

### 핵심 패턴
```
.claude/
├── agents/
│   ├── engineering.md    # 개발 에이전트 정의
│   ├── growth.md         # 마케팅 에이전트 정의
│   └── operations.md     # 운영 에이전트 정의
├── CLAUDE.md             # 프로젝트 조율 파일
└── rules/                # 코딩 규칙
```

각 에이전트가 도메인 컨텍스트를 유지하면서 독립 작동.
공유 DB(Turso 등)로 에이전트 간 상태 동기화.

---

## 도구 비교 매트릭스

| 도구 | 자율성 | 설치 난이도 | Docker 필요 | 비용 | 최적 용도 |
|------|--------|-----------|------------|------|----------|
| OpenHands | ★★★★★ | 중 | O | API비용 | 자율 개발 |
| gh-aw | ★★★★☆ | 하 | X | 무료 | GitHub CI/CD |
| Dify | ★★★★☆ | 중 | O | 무료~ | 워크플로우 설계 |
| Claude Code | ★★★★☆ | 하 | X | $100-200 | 일상 코딩 |
| Aider | ★★★☆☆ | 하 | X | API비용 | 페어 프로그래밍 |
| CodeRabbit | ★★★☆☆ | 하 | X | 무료~$15 | 코드 리뷰 |
| GPT-Engineer | ★★★☆☆ | 하 | X | API비용 | 프로젝트 생성 |

---

## 추가 참고 리포지토리

| 리포 | Stars | 설명 |
|------|-------|------|
| [VoltAgent/awesome-claude-code-subagents](https://github.com/VoltAgent/awesome-claude-code-subagents) | - | Claude Code 전문 서브에이전트 100개+ |
| [bradAGI/awesome-cli-coding-agents](https://github.com/bradAGI/awesome-cli-coding-agents) | - | 터미널 AI 코딩 에이전트 디렉터리 |
| [e2b-dev/awesome-ai-agents](https://github.com/e2b-dev/awesome-ai-agents) | - | AI 자율 에이전트 목록 |
| [bytedance/deer-flow](https://github.com/bytedance/deer-flow) | 30,100 | ByteDance SuperAgent |
| [simstudioai/sim](https://github.com/simstudioai/sim) | 27,000 | AI 에이전트 오케스트레이션 |
| [eigent-ai/eigent](https://github.com/eigent-ai/eigent) | 13,000 | 데스크탑 멀티에이전트 |
