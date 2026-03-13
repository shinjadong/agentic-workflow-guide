# 풀 아키텍처 기획서: OpenClaw + OpenHands + Claude Code

> "WhatsApp에서 지시 → AI가 자동 코딩 → PR → 리뷰 → 배포"
>
> 대표 1명이 AI 조직을 운영하는 완전체 시스템 설계

## 시스템 개요

```
┌─────────────────────────────────────────────────────────────────────┐
│                                                                      │
│   대표님 (어디서든)                                                    │
│   📱 WhatsApp / Telegram / Slack                                     │
│                                                                      │
│         │ "로그인 API 만들어줘"                                        │
│         │ "오늘 매출 리포트 보내줘"                                     │
│         │ "CCTV 랜딩페이지 수정해"                                     │
│         ▼                                                            │
│   ┌───────────────────────────────────────┐                          │
│   │         OpenClaw (전체 비서)            │                          │
│   │         ═══════════════════            │                          │
│   │  • 24/7 상시 가동                       │                          │
│   │  • 영구 메모리 (세션 간 유지)             │                          │
│   │  • 멀티에이전트 오케스트레이터            │                          │
│   │  • 21개 메시징 플랫폼 지원               │                          │
│   │  • MCP 네이티브 (5,400+ 스킬)          │                          │
│   │  • 스케줄링 (cron)                      │                          │
│   │                                        │                          │
│   │  역할: 접수 → 분류 → 위임 → 보고        │                          │
│   └────────────┬──────────┬────────────────┘                          │
│                │          │                                           │
│     ┌──────────┘          └──────────┐                                │
│     ▼                                ▼                                │
│   ┌──────────────────┐  ┌──────────────────┐                         │
│   │ OpenHands         │  │ Claude Code       │                         │
│   │ (자율 PM + 코더)   │  │ (정밀 코딩)        │                         │
│   │ ════════════════  │  │ ════════════════  │                         │
│   │                   │  │                   │                         │
│   │ • Docker 샌드박스  │  │ • 터미널 직접 제어  │                         │
│   │ • 자율 코딩 루프   │  │ • 코드베이스 깊은   │                         │
│   │   (Self-Healing)  │  │   컨텍스트 이해     │                         │
│   │ • GitHub Issue    │  │ • 리팩터링/아키텍처  │                         │
│   │   자동 해결       │  │ • claude -p 자동화  │                         │
│   │ • PR 자동 생성    │  │                   │                         │
│   │                   │  │ 용도:              │                         │
│   │ 용도:             │  │ • 복잡한 로직 구현  │                         │
│   │ • 기능 개발       │  │ • 아키텍처 변경     │                         │
│   │ • 버그 수정       │  │ • 코드 리뷰 보조    │                         │
│   │ • 테스트 작성     │  │ • 긴급 핫픽스       │                         │
│   └────────┬─────────┘  └────────┬──────────┘                         │
│            │                     │                                    │
│            └──────────┬──────────┘                                    │
│                       ▼                                               │
│   ┌──────────────────────────────────────┐                            │
│   │           GitHub Repository           │                            │
│   │           ═════════════════           │                            │
│   │                                       │                            │
│   │  PR 생성 ──→ CodeRabbit 자동 리뷰     │                            │
│   │          ──→ Self-Healing CI          │                            │
│   │          ──→ 대표님 최종 [Merge]       │                            │
│   │                                       │                            │
│   │  Merge ──→ GitHub Actions ──→ Vercel  │                            │
│   │                              자동 배포 │                            │
│   └──────────────────────────────────────┘                            │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 컴포넌트별 역할 정의

### Layer 1: OpenClaw (전체 비서 / 오케스트레이터)

| 항목 | 설명 |
|------|------|
| **역할** | 대표의 모든 지시를 **접수 → 분류 → 위임 → 보고** |
| **인터페이스** | WhatsApp, Telegram, Slack (대표가 편한 앱) |
| **가동** | 24/7 상시 (Mac Mini / 리눅스 PC) |
| **메모리** | 영구 — 프로젝트 상태, 의사결정 히스토리, 진행률 추적 |
| **모델** | Claude Sonnet (비용 효율) 또는 DeepSeek (초저가) |

#### OpenClaw이 처리하는 업무

```
지시 분류 매트릭스:

"로그인 API 만들어줘"     → 코딩 → OpenHands (GitHub Issue 생성)
"결제 로직 리팩터링해"     → 정밀 코딩 → Claude Code (직접 실행)
"오늘 블로그 원고 써줘"    → 콘텐츠 → orchestrator.py (기존 시스템)
"내일 미팅 잡아줘"        → 일정 → Google Calendar 스킬
"매출 리포트 보내줘"       → 데이터 → Supabase 조회 → 정리 → 메시지
"PR 상태 어때?"          → 모니터링 → GitHub API 조회 → 보고
```

#### 자동 분류 로직 (OpenClaw 에이전트 설정)

```markdown
# .openclaw/agents/orchestrator/SKILL.md

## 역할
당신은 대표의 전체 비서입니다. 모든 지시를 분류하고 적절한 시스템에 위임합니다.

## 분류 규칙
1. **코딩 작업** (기능 개발, 버그 수정, 테스트)
   → GitHub Issue 생성 (fix-me 라벨) → OpenHands가 자동 처리
   → 복잡도 높으면 Claude Code 직접 실행 플래그

2. **콘텐츠 작업** (블로그, 마케팅)
   → orchestrator.py 호출 또는 직접 생성

3. **비즈니스 작업** (리포트, 분석, 일정)
   → 내장 스킬로 직접 처리

4. **모니터링** (진행 상황, PR 상태, 배포 상태)
   → GitHub API / Vercel API 조회 → 보고

## 보고 규칙
- 코딩 작업 위임 후: "OpenHands에 전달했습니다. Issue #XX 생성됨."
- PR 생성 시: "PR #XX가 생성되었습니다. CodeRabbit 리뷰 진행 중."
- 배포 완료 시: "v1.2.3 프로덕션 배포 완료."
```

---

### Layer 2-A: OpenHands (자율 PM + 코더)

| 항목 | 설명 |
|------|------|
| **역할** | GitHub Issue를 받아 **자율적으로** 코드 작성 → 테스트 → PR 생성 |
| **트리거** | GitHub Issue에 `fix-me` 라벨 또는 `@openhands-agent` 멘션 |
| **환경** | Docker 샌드박스 (격리됨, 안전) |
| **모델** | Claude Sonnet API (비용 효율) |
| **핵심 능력** | Self-Healing Loop — 에러를 스스로 읽고 수정 |

#### OpenHands가 받는 작업 흐름

```
OpenClaw이 GitHub Issue 생성
    │
    │  Issue 내용 예시:
    │  ────────────────
    │  제목: 할부 결제 API 구현
    │  본문:
    │  - 3, 6, 12개월 할부 옵션
    │  - PG사: 토스페이먼츠
    │  - 엔드포인트: POST /api/payments/installment
    │  - 테스트 포함
    │  라벨: fix-me
    │
    ▼
OpenHands Resolver 자동 트리거
    │
    ▼
Docker 샌드박스에서:
    1. 코드베이스 분석
    2. 코드 작성
    3. npm run dev → 에러? → 자동 수정 (Self-Healing)
    4. npm test → 에러? → 자동 수정
    5. 성공 → PR 생성
    │
    ▼
CodeRabbit 자동 리뷰 → 대표님 [Merge]
```

---

### Layer 2-B: Claude Code (정밀 코딩)

| 항목 | 설명 |
|------|------|
| **역할** | 복잡한 아키텍처 변경, 리팩터링, 긴급 핫픽스 |
| **트리거** | OpenClaw이 "복잡도 높음"으로 판단 시, 또는 대표가 직접 지시 |
| **환경** | 터미널 (Termux 또는 리눅스 PC SSH) |
| **모델** | Claude Opus (Max 구독) |
| **핵심 능력** | 코드베이스 깊은 이해, 인라인 수정, 대화형 |

#### Claude Code 사용 시나리오

```
# 시나리오 1: OpenClaw 경유 (자동)
대표: "결제 시스템 전체 리팩터링해줘"
OpenClaw: (복잡도 높음 판단) → Claude Code -p 실행

# 시나리오 2: 대표 직접 (수동)
대표: SSH로 PC 접속 → claude 실행 → 직접 대화

# 시나리오 3: 하이브리드
OpenHands가 기본 구현 → PR 생성
대표가 Claude Code로 PR 코드 검토/수정 → Merge
```

---

### Layer 3: GitHub (조율 허브)

모든 코드가 모이는 **단일 진실의 원천(Single Source of Truth)**:

```
GitHub Repository
    │
    ├── Issues: 작업 요청 (OpenClaw이 생성, OpenHands가 해결)
    ├── PRs: 코드 변경 (OpenHands/Claude Code가 생성)
    ├── Actions: CI/CD + Self-Healing
    ├── CodeRabbit: 자동 코드 리뷰
    └── Vercel: 자동 배포
```

---

## 연동 프로토콜 상세

### 1. OpenClaw → OpenHands (GitHub Issue 경유)

가장 안정적이고 추적 가능한 연동 방식:

```
OpenClaw                          GitHub                      OpenHands
   │                                │                            │
   │  gh issue create               │                            │
   │  --title "..."                 │                            │
   │  --body "..."                  │                            │
   │  --label "fix-me"             │                            │
   │ ──────────────────────────→    │                            │
   │                                │  issue.labeled event       │
   │                                │ ──────────────────────→    │
   │                                │                            │
   │                                │    (자율 코딩...)            │
   │                                │                            │
   │                                │  ←── PR 생성               │
   │                                │                            │
   │  ←── Webhook: PR 생성 알림     │                            │
   │                                │                            │
   │  대표에게 보고:                  │                            │
   │  "PR #42 생성됨,               │                            │
   │   CodeRabbit 리뷰 중"          │                            │
```

#### OpenClaw 스킬 구현

```yaml
# .openclaw/skills/create-dev-task/SKILL.md
name: create-dev-task
description: 코딩 작업을 GitHub Issue로 변환하여 OpenHands에 위임

tools:
  - shell

instructions: |
  사용자의 코딩 요청을 분석하여:
  1. 적절한 GitHub Issue 제목과 본문 생성
  2. 기술 요구사항, 엔드포인트, 테스트 조건 명시
  3. gh CLI로 Issue 생성 (fix-me 라벨 필수)
  4. 사용자에게 Issue 번호와 링크 보고
```

### 2. OpenClaw → Claude Code (Shell 경유)

복잡한 작업을 Claude Code의 headless 모드로 실행:

```
OpenClaw                          Linux PC                    Claude Code
   │                                │                            │
   │  ssh user@pc "claude -p        │                            │
   │  --allowedTools Edit,Write     │                            │
   │  '결제 로직 리팩터링해줘'"       │                            │
   │ ──────────────────────────→    │ ──────────────────────→    │
   │                                │                            │
   │                                │    (코딩...)                │
   │                                │                            │
   │                                │  ←── 결과 출력              │
   │  ←── 실행 결과 수신             │                            │
   │                                │                            │
   │  대표에게 보고:                  │                            │
   │  "리팩터링 완료.                 │                            │
   │   변경 파일 5개, 테스트 통과"    │                            │
```

### 3. 모니터링 루프 (OpenClaw cron)

```yaml
# 매 30분마다 실행
schedule: "*/30 * * * *"

actions:
  - check: gh pr list --state open --json number,title,reviewDecision
  - check: gh run list --limit 5 --json status,conclusion
  - if: new_pr_merged → notify user "PR #{n} 머지 → 배포 시작"
  - if: ci_failed → notify user "CI 실패: {error_summary}"
  - if: review_approved → notify user "CodeRabbit 승인됨, Merge 대기"
```

---

## 하드웨어 & 인프라 설계

### 옵션 A: 단일 리눅스 PC (추천)

```
┌──────────────────────────────────┐
│  리눅스 PC (Ubuntu 24.04)         │
│                                   │
│  ┌─────────┐  ┌────────────────┐ │
│  │ OpenClaw │  │ Docker          │ │
│  │ Gateway  │  │  ┌───────────┐ │ │
│  │ (Node)   │  │  │ OpenHands │ │ │
│  │          │  │  │ Agent     │ │ │
│  │ port:    │  │  └───────────┘ │ │
│  │ 18789    │  │  port: 3000    │ │
│  └─────────┘  └────────────────┘ │
│                                   │
│  ┌─────────┐  ┌────────────────┐ │
│  │ Claude   │  │ SSH Server     │ │
│  │ Code CLI │  │ (Termux 접속)   │ │
│  └─────────┘  └────────────────┘ │
│                                   │
│  스펙: 64GB RAM, 2TB SSD, GPU 불필요│
│  비용: ~200-300만원                │
└──────────────────────────────────┘
         │
         │ Tailscale VPN
         │
┌────────────────┐
│ 태블릿 (Termux)  │
│ • SSH 접속       │
│ • Claude Code   │
│ • WhatsApp 지시  │
└────────────────┘
```

### 옵션 B: Mac Mini (초저전력)

```
Mac Mini M4 (16GB+)
• 6W 유휴 전력 = 연 $5 전기세
• OpenClaw 24/7 Gateway
• Docker Desktop → OpenHands
• Claude Code CLI
• 비용: ~100-200만원
```

### 추천 스펙

| 부품 | 최소 | 추천 | 왜? |
|------|------|------|-----|
| RAM | 16GB | **64GB** | Docker + OpenHands + OpenClaw 동시 |
| SSD | 512GB | **2TB** | Docker 이미지, 코드, 로그 |
| CPU | 8코어 | 16코어 | 병렬 에이전트 실행 |
| GPU | 불필요 | 불필요 | 전부 API 방식 |
| 네트워크 | 유선 | 유선 | 24/7 안정성 |

---

## 구축 순서 (Phase별)

### Phase 0: 사전 준비 (1시간)

```
✅ GitHub 리포 생성 (directmarket-v2 등)
✅ Anthropic API 키 발급
✅ Vercel 연결
✅ Tailscale 계정 생성
```

### Phase 1: OpenClaw 설치 + 메시징 연결 (Day 1 오전)

```bash
# 1. OpenClaw 설치
curl -fsSL https://openclaw.ai/install.sh | bash

# 2. 온보딩 (10분 가이드)
openclaw onboard --install-daemon

# 3. 메시징 연결 (WhatsApp 또는 Telegram)
# → 온보딩 위자드에서 선택

# 4. AI 모델 설정
# → Claude Sonnet API 연결 (비용 효율)

# 5. 테스트
# → WhatsApp에서 "안녕" 보내기 → 응답 확인
```

### Phase 2: OpenHands 설치 + GitHub 연동 (Day 1 오후)

```bash
# 1. Docker 설치 (아직 없다면)
sudo apt install -y docker.io
sudo usermod -aG docker $USER

# 2. OpenHands 실행
docker run -d \
  --name openhands \
  -p 3000:3000 \
  -e LLM_API_KEY=$ANTHROPIC_API_KEY \
  -e LLM_MODEL=anthropic/claude-sonnet-4-20250514 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $HOME/workspace:/opt/workspace_base \
  --restart unless-stopped \
  ghcr.io/openhands/openhands:latest

# 3. GitHub Resolver 워크플로우 추가
# → .github/workflows/openhands-resolver.yml 복사

# 4. GitHub Secrets 설정
gh secret set ANTHROPIC_API_KEY --body "$ANTHROPIC_API_KEY"

# 5. 테스트: Issue 생성 → 자동 PR 확인
gh issue create --title "테스트: Hello World API" \
  --body "GET /api/hello → {message: 'Hello!'} 반환" \
  --label "fix-me"
```

### Phase 3: OpenClaw ↔ OpenHands 브릿지 (Day 2)

```bash
# 1. OpenClaw에 GitHub 스킬 추가
# → gh CLI를 OpenClaw이 사용할 수 있도록 설정

# 2. create-dev-task 스킬 작성
mkdir -p ~/.openclaw/skills/create-dev-task
cat > ~/.openclaw/skills/create-dev-task/SKILL.md << 'SKILL'
name: create-dev-task
description: 코딩 작업을 GitHub Issue로 변환

tools:
  - shell

instructions: |
  사용자의 코딩 요청을 받으면:
  1. 기술 요구사항을 구체적인 Issue로 변환
  2. gh issue create --repo [repo] --title "..." --body "..." --label "fix-me"
  3. Issue 번호와 링크를 사용자에게 보고
  4. "OpenHands가 자동으로 작업을 시작합니다" 안내
SKILL

# 3. 모니터링 스킬 작성
cat > ~/.openclaw/skills/dev-monitor/SKILL.md << 'SKILL'
name: dev-monitor
description: GitHub PR/CI 상태 모니터링

tools:
  - shell

instructions: |
  주기적으로 또는 요청 시:
  1. gh pr list --state open → 열린 PR 목록
  2. gh run list --limit 5 → 최근 CI 실행 결과
  3. 변경사항이 있으면 사용자에게 보고
SKILL

# 4. 테스트: WhatsApp에서 코딩 지시
# → "로그인 API 만들어줘" 보내기
# → OpenClaw이 Issue 생성 → OpenHands가 코딩 시작
```

### Phase 4: Claude Code 연동 + 고급 설정 (Day 3)

```bash
# 1. Claude Code 설치 (리눅스 PC)
npm install -g @anthropic-ai/claude-code

# 2. OpenClaw에서 Claude Code 호출 스킬
cat > ~/.openclaw/skills/precision-coding/SKILL.md << 'SKILL'
name: precision-coding
description: 복잡한 코딩 작업을 Claude Code로 직접 실행

tools:
  - shell

instructions: |
  사용자가 복잡한 아키텍처 변경이나 리팩터링을 요청하면:
  1. 작업 내용을 Claude Code 프롬프트로 변환
  2. claude -p "작업 내용" 실행
  3. 결과를 사용자에게 요약 보고
  4. 필요시 git commit + push
SKILL

# 3. CodeRabbit 설치
# → GitHub Marketplace에서 Install

# 4. Self-Healing CI 워크플로우 추가
cp tools/templates/self-healing-ci.yml .github/workflows/

# 5. 풀 파이프라인 테스트
# WhatsApp: "상품 목록 API 만들어줘. GET /api/products, 페이지네이션 지원"
# → OpenClaw → GitHub Issue → OpenHands → PR → CodeRabbit → [Merge] → Vercel
```

---

## 일일 운영 시나리오

### 시나리오 1: 일반 기능 개발

```
09:00  대표 (WhatsApp): "상품 검색 API 만들어줘. 카테고리 필터, 가격 정렬 지원"
       ↓
09:01  OpenClaw: "Issue #15 생성했습니다. OpenHands가 작업 시작합니다."
       ↓
09:30  OpenClaw: "PR #16 생성됨. CodeRabbit 리뷰 중."
       ↓
09:35  OpenClaw: "CodeRabbit 승인. Merge하시겠습니까?"
       ↓
09:36  대표: "ㅇㅇ 머지해"
       ↓
09:37  OpenClaw: "Merge 완료. Vercel 배포 시작. 2분 후 프로덕션 반영."
```

### 시나리오 2: 복잡한 리팩터링

```
14:00  대표 (WhatsApp): "결제 시스템 전체를 Strategy 패턴으로 리팩터링해줘"
       ↓
14:01  OpenClaw: "복잡한 아키텍처 변경이네요. Claude Code로 직접 처리합니다."
       ↓
14:15  OpenClaw: "리팩터링 완료. 변경 파일 12개, 테스트 전부 통과.
                  주요 변경사항:
                  - PaymentStrategy 인터페이스 추가
                  - TossStrategy, KakaoStrategy 구현
                  - 기존 if-else → Strategy 패턴 교체
                  PR #17 생성됨."
       ↓
14:20  대표: Claude Code로 SSH 접속하여 코드 직접 확인 → Merge
```

### 시나리오 3: 자동 모니터링

```
03:00  (새벽, 대표 수면 중)
       OpenHands가 예약된 Issue 작업 완료 → PR #18 생성
       ↓
03:05  CI 실행 → 테스트 1개 실패
       ↓
03:06  Self-Healing CI → AI가 에러 분석 → 자동 수정 → CI 재실행
       ↓
03:10  CI 통과. PR 대기.
       ↓
08:00  대표 기상
       OpenClaw (WhatsApp): "좋은 아침입니다.
         - PR #18: 회원가입 API (CI 통과, Merge 대기)
         - 어제 배포된 v1.3.2 안정적 (에러 0건)
         - 오늘 예정: Issue #19, #20"
```

---

## 비용 요약

| 항목 | 월 비용 |
|------|---------|
| OpenClaw (소프트웨어) | 무료 |
| OpenHands (소프트웨어) | 무료 |
| Claude Code (소프트웨어) | 무료 |
| **Claude Max** (직접 코딩) | $200 (~27만원) |
| **Claude API** (OpenHands + OpenClaw) | $50-150 (~7-20만원) |
| GitHub Pro | $4 (~5,400원) |
| Vercel Pro | $20 (~2.7만원) |
| 전기세 | ~$5-40 (~0.7-5만원) |
| **월 합계** | **$280-415 (~38-56만원)** |
| **하드웨어** (일회성) | **200-300만원** |

> vs 주니어 개발자 1명 = 300만원+/월 (4대보험 별도)
> **2개월 만에 투자 회수**

---

## 확장 가능성

### 단기 (1-3개월)
- 다이렉트마켓 V2 개발 파이프라인 가동
- 블로그 자동화 (기존 orchestrator.py 연동)
- 고객 문의 자동 응답 (OpenClaw 메시징)

### 중기 (3-6개월)
- 멀티 프로젝트 지원 (smartstore, cctv, care_ontology)
- A/B 테스트 자동화
- 매출 리포트 자동 생성 + 알림

### 장기 (6-12개월)
- 에이전트 간 자율 조율 (사람 개입 최소화)
- 새 프로젝트 부트스트랩 자동화
- AI 직원 "온보딩" 패턴 표준화

---

## 리스크 & 대응

| 리스크 | 확률 | 대응 |
|--------|------|------|
| Anthropic API 가격 인상 | 중 | DeepSeek/Kimi 백업 모델 준비 |
| OpenClaw 프로젝트 중단 | 낮 | MIT 라이선스, 포크 가능 |
| OpenHands 품질 불안정 | 중 | Claude Code fallback, 사람 리뷰 필수 |
| 보안 취약점 | 중 | API 키 최소 권한, Docker 격리, VPN |
| 토큰 비용 폭주 | 중 | 일일 한도 설정, 저가 모델 라우팅 |
