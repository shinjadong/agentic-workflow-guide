# OpenClaw 상세 가이드

> 24/7 AI 비서 — 310,000+ Stars, MIT 라이선스

## OpenClaw이란?

| 항목 | 내용 |
|------|------|
| **GitHub** | [openclaw/openclaw](https://github.com/openclaw/openclaw) ⭐310,000+ |
| **라이선스** | MIT (완전 오픈소스) |
| **창시자** | Peter Steinberger (PSPDFKit 창업자, $100M 회사) |
| **현 상태** | 창시자 OpenAI 합류 (2026.02), 커뮤니티가 계속 개발 |
| **커밋** | 18,305+ |
| **설치** | Node >= 22, 한 줄 설치 |

### 히스토리
1. **2025.11**: "Clawdbot"으로 출시
2. **2025.12**: Anthropic 법적 경고 → "Moltbot"으로 리브랜딩
3. **2026.01**: "OpenClaw"으로 최종 리브랜딩
4. **2026.01.09**: Anthropic OAuth 차단 사건
5. **2026.02**: 창시자 Peter Steinberger, OpenAI 합류

---

## 핵심 기능

### 1. 21개 메시징 플랫폼 지원
WhatsApp, Telegram, Slack, Discord, Signal, iMessage (BlueBubbles), Google Chat, Microsoft Teams, Matrix, LINE, IRC, Feishu 등

### 2. 멀티 모델 지원
| 모델 | 비용 | 용도 |
|------|------|------|
| Claude Sonnet API | $3/M 입력 | 일반 비서 (추천) |
| Claude Opus API | $15/M 입력 | 복잡한 추론 |
| DeepSeek | ~$1/M | 초저가 백업 |
| GPT-4o | $5/M | 범용 |
| Ollama (로컬) | 무료 | GPU 있을 때 |

### 3. 영구 메모리
- 세션 간 컨텍스트 유지 (Claude Code와의 핵심 차이)
- 프로젝트 상태, 의사결정 히스토리, 사용자 선호도 저장
- 주 단위 이상 장기 기억

### 4. MCP 네이티브
- 5,400+ 스킬 (ClawHub 마켓플레이스)
- 모든 MCP 서버와 호환
- 커스텀 스킬 작성 가능

### 5. 멀티에이전트 오케스트레이션
- Gateway당 여러 에이전트 운영
- 에이전트 간 통신 (`sessions_send`)
- 오케스트레이터 패턴: 메인 에이전트 → 서브에이전트 스폰
- Mission Control 대시보드

### 6. 자동화
- Cron 작업 (스케줄링)
- 웹훅 수신
- 브라우저 자동화 (Chrome DevTools Protocol)
- 파일 시스템 접근
- 셸 명령 실행

---

## 설치 가이드

### 요구 사항
- Node.js >= 22
- macOS / Linux / WSL2
- 메시징 앱 계정 (WhatsApp, Telegram 등)
- AI 모델 API 키

### 설치 (1분)

```bash
# 방법 1: 원라이너
curl -fsSL https://openclaw.ai/install.sh | bash

# 방법 2: npm
npm i -g openclaw@latest

# 온보딩 (10분 가이드)
openclaw onboard --install-daemon
```

온보딩 위자드가 안내하는 것:
1. 메시징 플랫폼 선택 + 연결
2. AI 모델 프로바이더 설정
3. Gateway 데몬 설치 (systemd/launchd)
4. 테스트 메시지 발송

### Mac Mini 24/7 셋업 (인기 구성)

```
필요한 것:
• Mac Mini M4 ($599~) 또는 M2 ($499~)
• HDMI 더미 플러그 ($8-10) — macOS가 디스플레이 연결됨으로 인식
• 유선 인터넷

설정:
• 시스템 설정 > 에너지 > "자동 잠자기 방지" 활성화
• OpenClaw 데몬 → launchd 자동 시작
• 전기세: 6W 유휴 = 연 ~$5

장소:
• TV 뒤, 서랍장 안, 어디든 — 소음 없음
```

### 리눅스 PC 셋업

```bash
# Ubuntu 24.04
sudo apt update && sudo apt install -y nodejs npm

# Node 22+ 확인
node -v  # v22.x 이상

# OpenClaw 설치
npm i -g openclaw@latest
openclaw onboard --install-daemon

# 데몬 확인
systemctl status openclaw-gateway
```

---

## Anthropic OAuth 차단 사건 (2026.01.09)

### 무슨 일?
많은 사용자가 "Claude Max API Proxy"를 사용하여 Claude Max 구독($200/월 플랫)을 OpenAI 호환 API로 노출 → OpenClaw에서 무제한 사용. Anthropic이 서버측에서 차단.

### 영향
- ❌ Claude Free/Pro/Max OAuth 토큰 → 제3자 도구에서 사용 불가
- ❌ OpenClaw에서 Claude Max 플랫 요금 사용 불가
- ✅ Anthropic API 키 (종량제) → 정상 사용 가능

### 대응 옵션
| 옵션 | 비용 | 설명 |
|------|------|------|
| Claude Sonnet API | ~$50-100/월 | 가장 무난 |
| DeepSeek | ~$15/월 | 초저가, 품질 양호 |
| Kimi K2.5 | ~$15/월 | 초저가 대안 |
| Claude Opus API | $200+/월 | 최고 품질, 비쌈 |
| 로컬 모델 (Ollama) | 무료 | GPU 필요 |

---

## OpenClaw vs Claude Code vs OpenHands

| 기능 | OpenClaw | Claude Code | OpenHands |
|------|----------|-------------|-----------|
| **핵심 역할** | 범용 AI 비서 | 정밀 코딩 | 자율 코딩 |
| **인터페이스** | 메시징 앱 21개 | 터미널/CLI | 웹 UI + CLI |
| **코딩 능력** | 약 (IDE 연동 없음) | **최강** (인라인, 컨텍스트) | **강** (자율, Self-Healing) |
| **비서 능력** | **최강** (일정, 메일, 메모) | 없음 | 없음 |
| **메모리** | **영구** (주 단위+) | 세션 내 | 세션 내 |
| **가동** | **24/7 상시** | 수동 실행 | 트리거 기반 |
| **모델** | 다중 (Claude, GPT, 로컬) | Claude 전용 | 다중 |
| **자동화** | cron, 웹훅, 멀티에이전트 | claude -p (headless) | GitHub Resolver |
| **비용** | 무료 + API | $200/월 (Max) | 무료 + API |

### 결론: 3개 다 필요하다
```
OpenClaw  = 접수창구 + 비서 + 오케스트레이터 (두뇌)
OpenHands = 자율 개발자 (손)
Claude Code = 정밀 도구 (메스)
```

---

## 멀티에이전트 설정

### 기본 구조

```yaml
# ~/.openclaw/config.yaml
agents:
  - name: orchestrator
    model: claude-sonnet
    role: "전체 비서, 작업 분류 및 위임"
    skills:
      - create-dev-task
      - dev-monitor
      - precision-coding

  - name: content-writer
    model: deepseek
    role: "블로그/마케팅 콘텐츠 생성"
    skills:
      - blog-writer
      - seo-optimizer

  - name: data-analyst
    model: claude-sonnet
    role: "매출 데이터 분석, 리포트 생성"
    skills:
      - supabase-query
      - report-generator
```

### 에이전트 간 통신

```
대표 → orchestrator: "이번 주 블로그 3편 써줘"
                          │
orchestrator → content-writer: "블로그 3편 생성 요청"
                          │
content-writer → orchestrator: "완료. 3편 생성됨."
                          │
orchestrator → 대표: "블로그 3편 완료:
  1. 아기홈캠 추천 TOP 5
  2. CCTV 설치비용 비교
  3. 매장 보안 솔루션 가이드"
```

---

## 보안 주의사항

| 리스크 | 대응 |
|--------|------|
| API 키 노출 | .env 파일에 저장, git-ignore |
| 메시징 플랫폼 접근 | 개인 계정 대신 비즈니스 계정 사용 |
| 셸 명령 남용 | allowedTools 제한 설정 |
| 스킬 공급망 공격 | ClawHub 검증된 스킬만 사용 |
| 프롬프트 인젝션 | 외부 입력 최소화, 핵심 작업은 확인 요청 |

### 최소 권한 원칙
```yaml
# 에이전트별 도구 제한
agents:
  - name: orchestrator
    allowedTools:
      - shell  # gh CLI만 사용
      - web    # GitHub API 조회만
    blockedCommands:
      - rm -rf
      - sudo
      - docker rm
```

---

## 참고 리소스

| 리소스 | URL |
|--------|-----|
| GitHub 리포 | [openclaw/openclaw](https://github.com/openclaw/openclaw) |
| 공식 사이트 | [openclaw.ai](https://openclaw.ai/) |
| 공식 문서 | [docs.openclaw.ai](https://docs.openclaw.ai/) |
| 멀티에이전트 가이드 | [docs.openclaw.ai/concepts/multi-agent](https://docs.openclaw.ai/concepts/multi-agent) |
| ClawHub 스킬 | [github.com/openclaw/skills](https://github.com/openclaw/skills) |
| Mission Control | [github.com/abhi1693/openclaw-mission-control](https://github.com/abhi1693/openclaw-mission-control) |
| Mac Mini 셋업 | [Medium 가이드](https://medium.com/@jh.baek.sd/how-i-set-up-openclaw-on-my-mac-mini-m4-my-24-7-ai-assistant-that-costs-less-than-a-coffee-89c4b470b064) |
| 보안 가이드 | [ComposioHQ/secure-openclaw](https://github.com/ComposioHQ/secure-openclaw) |
