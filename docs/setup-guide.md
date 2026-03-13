# 단계별 셋업 가이드

> Phase 1(지금 당장) → Phase 2(PC 도입) → Phase 3(풀 자동화)

## Phase 1: 즉시 시작 (비용 $0, 시간 30분)

> 지금 Termux + Claude Code 환경에서 바로 시작

### 1-1. GitHub 리포 생성

```bash
# 프로젝트 생성
mkdir ~/directmarket-v2
cd ~/directmarket-v2
git init

# GitHub 리포 생성
gh repo create directmarket-v2 --public \
  --description "다이렉트마켓 V2 — AI가 만드는 이커머스"
git remote add origin https://github.com/[username]/directmarket-v2.git
```

### 1-2. 프로젝트 초기화 (Claude Code로)

```bash
# Claude Code에게 프로젝트 초기화 맡기기
claude "Next.js 14 + TypeScript + Tailwind CSS로 프로젝트를 초기화해줘.
폴더 구조: src/app, src/components, src/lib, src/api
package.json에 필요한 의존성 추가하고 tsconfig 설정해줘."
```

### 1-3. Vercel 자동 배포 연결

1. [vercel.com](https://vercel.com) 접속 → GitHub 로그인
2. "Import Project" → directmarket-v2 리포 선택
3. 설정은 기본값 → "Deploy" 클릭
4. 이후 `main` 브랜치에 push하면 **자동 배포**

### 1-4. 기본 CI 워크플로우

```bash
mkdir -p .github/workflows
```

```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run build
      - run: npm test
```

### Phase 1 결과
- ✅ GitHub에 코드 관리
- ✅ push하면 자동 빌드/테스트
- ✅ main 머지하면 자동 배포
- ✅ Claude Code로 일상 코딩

---

## Phase 2: 리눅스 PC 도입 (비용 ~300만원, 시간 1일)

> 전용 PC에서 OpenHands 자율 에이전트 실행

### 2-1. Ubuntu 설치 및 기본 설정

```bash
# Ubuntu 24.04 LTS 설치 후

# 기본 패키지
sudo apt update && sudo apt install -y \
  docker.io docker-compose git curl \
  nodejs npm python3-pip python3-venv \
  openssh-server htop tmux

# Docker 권한
sudo usermod -aG docker $USER
newgrp docker

# Node.js 최신 버전 (nvm 사용)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install 20
```

### 2-2. AI 도구 설치

```bash
# Claude Code
npm install -g @anthropic-ai/claude-code

# Aider
pip install aider-chat

# API 키 설정
echo 'export ANTHROPIC_API_KEY="sk-ant-api03-..."' >> ~/.bashrc
source ~/.bashrc
```

### 2-3. OpenHands 설치 및 실행

```bash
# OpenHands Docker 이미지
docker pull ghcr.io/openhands/openhands:latest

# 실행 (웹 UI)
docker run -d \
  --name openhands \
  -p 3000:3000 \
  -e LLM_API_KEY=$ANTHROPIC_API_KEY \
  -e LLM_MODEL=anthropic/claude-sonnet-4-20250514 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $HOME/workspace:/opt/workspace_base \
  --restart unless-stopped \
  ghcr.io/openhands/openhands:latest

# 확인
echo "OpenHands UI: http://localhost:3000"
```

### 2-4. 원격 접속 설정 (태블릿에서 PC 제어)

```bash
# PC: SSH 서버 활성화
sudo systemctl enable ssh
sudo systemctl start ssh

# PC: Tailscale VPN (외부 접속용)
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up

# 태블릿 (Termux):
pkg install openssh
ssh user@[PC의-tailscale-IP]

# 또는 OpenHands 웹 UI에 접속
# http://[PC의-tailscale-IP]:3000
```

### Phase 2 결과
- ✅ Phase 1의 모든 것 +
- ✅ OpenHands 웹 UI로 자율 코딩
- ✅ 태블릿에서 PC를 원격 제어
- ✅ Docker 샌드박스에서 안전한 코드 실행

---

## Phase 3: 풀 오토메이션 (시간 반나절)

> Issue 쓰면 AI가 자동으로 PR 생성 → 코드 리뷰 → 배포

### 3-1. OpenHands Resolver 설정

```bash
cd ~/directmarket-v2

# 워크플로우 복사
cp ~/agentic-workflow-guide/tools/templates/openhands-resolver.yml \
   .github/workflows/openhands-resolver.yml

# GitHub Secrets 설정
gh secret set ANTHROPIC_API_KEY --body "$ANTHROPIC_API_KEY"

# 커밋 및 푸시
git add .github/workflows/openhands-resolver.yml
git commit -m "feat: add OpenHands auto-resolver workflow"
git push
```

### 3-2. CodeRabbit 설치

1. [github.com/marketplace/coderabbit](https://github.com/marketplace/coderabbit) 방문
2. "Install" 클릭 → directmarket-v2 리포 선택
3. 설정 완료 — 이후 모든 PR에 자동 코드 리뷰

### 3-3. Self-Healing CI 추가

```bash
# Self-Healing CI 워크플로우 복사
cp ~/agentic-workflow-guide/tools/templates/self-healing-ci.yml \
   .github/workflows/self-healing-ci.yml

git add .github/workflows/self-healing-ci.yml
git commit -m "feat: add self-healing CI workflow"
git push
```

### 3-4. 테스트 런

```bash
# 이슈 작성 → 자동 PR 생성 테스트
gh issue create \
  --title "로그인 페이지 구현" \
  --body "이메일/비밀번호 로그인 폼을 만들어줘.
  - Next.js App Router 사용
  - 폼 유효성 검사 포함
  - 에러 메시지 표시" \
  --label "fix-me"

# 잠시 후 PR 확인
gh pr list

# PR이 생성되면 → CodeRabbit이 자동 리뷰
# → 대표님은 확인 후 Merge만
```

### 3-5. Agent OS 패턴 적용 (선택)

```bash
# 도메인별 에이전트 정의
mkdir -p .claude/agents

cat > .claude/agents/developer.md << 'EOF'
# Developer Agent
You are the development agent for 다이렉트마켓 V2.
- Stack: Next.js 14, TypeScript, Tailwind CSS
- DB: Supabase
- Deploy: Vercel
- Always write tests for new features
- Follow the coding style in .claude/rules/
EOF

cat > .claude/agents/reviewer.md << 'EOF'
# Reviewer Agent
You review code for 다이렉트마켓 V2.
- Check for security vulnerabilities
- Verify TypeScript types are correct
- Ensure error handling is present
- Check for performance issues
EOF
```

### Phase 3 결과
- ✅ Phase 1 + 2의 모든 것 +
- ✅ 이슈 작성 → AI 자동 PR 생성
- ✅ AI 자동 코드 리뷰 (CodeRabbit)
- ✅ CI 실패 → AI 자동 수정
- ✅ 테스트 통과 → 자동 배포

---

## 일일 워크플로우 (Phase 3 완성 후)

```
아침 (9:00)
├── Linear/Notion에서 오늘 할 일 확인
├── GitHub Issues에 작업 등록 + fix-me 라벨
└── AI가 백그라운드에서 작업 시작

낮 (12:00-18:00)
├── AI가 PR 올린 것들 확인
├── CodeRabbit 리뷰 결과 확인
├── 괜찮으면 [Merge] 클릭
└── 복잡한 건 Claude Code로 직접 코딩

저녁 (20:00)
├── 오늘 머지된 것들 프로덕션 확인
├── 내일 이슈 미리 등록
└── AI가 밤새 작업 → 아침에 결과 확인
```

---

## 트러블슈팅

### OpenHands가 Docker에서 안 뜰 때
```bash
# Docker 소켓 권한 확인
ls -la /var/run/docker.sock
sudo chmod 666 /var/run/docker.sock
```

### Claude API 키 인식 안 될 때
```bash
# 환경 변수 확인
echo $ANTHROPIC_API_KEY
# GitHub Secret 확인
gh secret list
```

### OpenHands Resolver가 트리거 안 될 때
```bash
# 워크플로우 실행 내역 확인
gh run list --workflow=openhands-resolver.yml
# 라벨 확인
gh issue view [issue-number] --json labels
```
