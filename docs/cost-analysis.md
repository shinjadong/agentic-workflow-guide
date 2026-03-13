# 비용 분석 & 셋업 가이드

> 현실적인 숫자로 보는 AI 개발팀 구축 비용

## 하드웨어: 전용 리눅스 PC

### 추천 스펙 (500만원 예산 기준)

| 부품 | 추천 사양 | 비용 (KRW) | 왜 필요한가 |
|------|----------|-----------|------------|
| **CPU** | AMD Ryzen 9 7950X 또는 Intel i9-14900K | 60-80만원 | Docker 컨테이너 다중 실행 |
| **RAM** | **64GB DDR5** | 20-30만원 | Docker + IDE + 에이전트 세션 동시 실행 |
| **SSD** | 2TB NVMe | 15-20만원 | Docker 이미지, 코드, 로그 |
| **GPU** | RTX 4070 (선택) | 80-100만원 | 로컬 모델 실행 시에만 필요 |
| **보드+케이스+파워** | B650/Z790 + 850W | 40-60만원 | 안정적 운영 |
| **모니터+키보드** | 기본 세트 | 30-50만원 | 원격 접속 시 불필요 |

### 시나리오별 총 비용

| 시나리오 | 스펙 | 비용 |
|---------|------|------|
| **클라우드 LLM만 사용 (추천)** | GPU 없음 | **170-250만원** |
| **로컬 모델도 실행** | GPU 포함 | **300-400만원** |
| **풀스펙 (여유 예산)** | 최고 사양 | **400-500만원** |

> **팁**: Claude API/Max를 쓸 거면 GPU는 불필요합니다. 그 돈으로 RAM 128GB로 업그레이드하는 게 낫습니다.

---

## 월 운영비: 3단계 티어

### Tier 1: 미니멀 셋업 (~$120/mo, ~16만원)

| 서비스 | 비용 (USD) | 비용 (KRW) |
|--------|-----------|-----------|
| Claude Max ($100 플랜) | $100 | ~13.5만원 |
| GitHub Free | $0 | 0원 |
| Vercel Free | $0 | 0원 |
| CodeRabbit Free (오픈소스) | $0 | 0원 |
| **월 합계** | **$100** | **~13.5만원** |

**가능한 것**: Claude Code로 일상 코딩, GitHub push, 자동 배포

### Tier 2: 프로 셋업 (~$280/mo, ~37만원)

| 서비스 | 비용 (USD) | 비용 (KRW) |
|--------|-----------|-----------|
| Claude Max ($200 플랜) | $200 | ~27만원 |
| OpenHands (셀프호스팅) | $0 | 0원 |
| Claude API (OpenHands용) | $50-100 | ~7-13만원 |
| GitHub Pro | $4 | ~5,400원 |
| Vercel Pro | $20 | ~2.7만원 |
| CodeRabbit Pro | $15 | ~2만원 |
| 전기세 (PC 24시간) | ~$40 | ~5만원 |
| **월 합계** | **~$280-340** | **~37-46만원** |

**가능한 것**: 풀 자동화 파이프라인 (Issue → PR → 리뷰 → 배포)

### Tier 3: 파워 셋업 (~$500-800/mo, ~67-108만원)

| 서비스 | 비용 (USD) | 비용 (KRW) |
|--------|-----------|-----------|
| Claude Max ($200 플랜) | $200 | ~27만원 |
| Claude API (대량 사용) | $200-500 | ~27-67만원 |
| OpenHands Cloud (관리형) | $50-100 | ~7-13만원 |
| GitHub Team | $4/user | ~5,400원 |
| Vercel Pro | $20 | ~2.7만원 |
| Sentry (에러 모니터링) | $26 | ~3.5만원 |
| **월 합계** | **~$500-850** | **~67-115만원** |

**가능한 것**: 멀티 에이전트 오케스트레이션, 대규모 프로젝트 병렬 처리

---

## 비교: AI 셋업 vs 사람 채용

| 항목 | AI 개발팀 (Tier 2) | 주니어 개발자 1명 |
|------|-------------------|-----------------|
| **월 비용** | ~37만원 | 250-350만원 |
| **4대보험** | 없음 | +30-50만원 |
| **관리비용** | 없음 | 면접, 온보딩, 1:1 |
| **작업 시간** | 24시간/7일 | 8시간/5일 |
| **병가/휴가** | 없음 | 연 15-25일 |
| **이직 리스크** | 없음 | 있음 |
| **연 총비용** | **~450만원** | **~4,200-5,400만원** |
| **비용 차이** | | **9-12배 비쌈** |

### 하지만 AI가 못하는 것

| 항목 | 설명 |
|------|------|
| 아키텍처 결정 | "어떤 구조로 만들지"는 대표가 판단 |
| 비즈니스 감각 | 고객이 뭘 원하는지는 AI가 모름 |
| 창의적 문제해결 | 전례 없는 문제는 사람이 풀어야 함 |
| 책임 | 장애 발생 시 대응은 사람 몫 |

> **결론**: AI는 "손"이고, 대표가 "머리"입니다.
> 최적 조합 = **AI(실행) + 대표(판단) + 필요시 시니어 프리랜서(감수)**

---

## 투자 대비 회수 (ROI) 시뮬레이션

### 가정
- 초기 투자: PC 300만원 + 첫 달 운영비 37만원 = **337만원**
- 월 운영비: 37만원
- 주니어 1명 대체 기준: 월 300만원 절감

### 손익분기점

| 월 | 누적 투자 | 누적 절감 | 순이익 |
|----|----------|----------|--------|
| 1 | 337만원 | 300만원 | -37만원 |
| **2** | 374만원 | 600만원 | **+226만원** ← 흑자 전환 |
| 6 | 522만원 | 1,800만원 | +1,278만원 |
| 12 | 744만원 | 3,600만원 | +2,856만원 |

> **2개월 만에 투자 회수.** 1년이면 약 2,800만원 순이익.

---

## 단계별 셋업 가이드

### Phase 1: 즉시 시작 (비용 $0, 시간 30분)

현재 환경(Termux + Claude Code)에서 바로 시작:

```bash
# 1. GitHub 리포 생성
gh repo create directmarket-v2 --public

# 2. Vercel 연결 (자동 배포)
# vercel.com에서 GitHub 리포 연결

# 3. Claude Code로 개발 시작
claude "Next.js 프로젝트 초기화하고 랜딩 페이지 만들어줘"

# 4. 배포
git add -A && git commit -m "init" && git push
# → Vercel이 자동으로 배포
```

### Phase 2: 리눅스 PC 도입 (비용 ~300만원, 시간 1일)

```bash
# 1. Ubuntu 24.04 LTS 설치
# 2. 기본 환경 설정
sudo apt update && sudo apt install -y docker.io git nodejs npm python3-pip

# 3. Docker 설정
sudo usermod -aG docker $USER
newgrp docker

# 4. OpenHands 설치
docker pull ghcr.io/openhands/openhands:latest

# 5. Aider 설치
pip install aider-chat

# 6. Claude Code 설치
npm install -g @anthropic-ai/claude-code

# 7. API 키 설정
echo 'export ANTHROPIC_API_KEY="sk-ant-..."' >> ~/.bashrc
source ~/.bashrc

# 8. OpenHands 실행
docker run -d \
  -p 3000:3000 \
  -e LLM_API_KEY=$ANTHROPIC_API_KEY \
  -e LLM_MODEL=claude-3-opus \
  -v /var/run/docker.sock:/var/run/docker.sock \
  ghcr.io/openhands/openhands:latest

# 9. 브라우저에서 localhost:3000 접속 → 웹 UI로 사용
```

### Phase 3: 풀 오토메이션 (시간 반나절)

```bash
# 1. OpenHands Resolver 워크플로우 추가
mkdir -p .github/workflows
# → openhands-resolver.yml 복사 (tools/templates/ 참조)

# 2. CodeRabbit 설치
# → GitHub Marketplace에서 "CodeRabbit" 검색 → Install

# 3. GitHub Secrets 설정
gh secret set ANTHROPIC_API_KEY --body "sk-ant-..."

# 4. 테스트: 이슈 작성
gh issue create --title "로그인 API에 rate limiting 추가" \
  --body "초당 10회 제한" --label "fix-me"

# 5. 확인: 자동으로 PR이 생성되는지 확인
gh pr list
```

---

## 원격 접속 설정 (태블릿에서 PC 제어)

```bash
# 리눅스 PC에서
sudo apt install openssh-server
sudo systemctl enable ssh

# Tailscale VPN 설치 (외부에서도 접속 가능)
curl -fsSL https://tailscale.com/install.sh | sh
tailscale up

# 태블릿 Termux에서
pkg install openssh
ssh user@[tailscale-ip]

# 또는 Claude Code로 직접 원격 작업
# PC의 Claude Code를 SSH 터널로 사용
```
