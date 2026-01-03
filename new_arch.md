# 🛡️ Secure Monorepo Framework (React + NestJS + FastAPI)

이 저장소는 **보안 관리 자동화(Security Automation)**가 내재화된 Full-stack Monorepo 샘플입니다. 코드 작성부터 병합(Merge)까지 각 단계에서 보안 취약점을 자동으로 필터링하며, 실패 시 **다중 채널(Slack, Naver Mail, Discord)로 실시간 알림**을 제공합니다.

---

## 🏗️ 1. Repository Structure

> **💡 CodeQL 워크플로우는 왜 별도로 관리하나요?**
> CodeQL은 GitHub에 내장된 기능이지만, `codeql-scan.yml`과 같은 별도 워크플로우 파일을 사용하면 **분석할 언어, 스캔 주기, 사용자 정의 쿼리 추가 등** GitHub의 기본 설정을 넘어 훨씬 세밀한 제어가 가능합니다. 따라서 명시적인 파일로 관리하는 것이 표준적인 방식입니다.

```txt
repo-root/
├─ .github/
│  └─ workflows/
│     ├─ ci-pipeline.yml        # 빌드 + 의존성 체크 + SAST (Semgrep)
│     ├─ codeql-scan.yml        # 정밀 보안 분석 (CodeQL)
│     └─ reusable-notify.yml    # 실패 시 다중 채널 알림 (Reusable)
│
├─ .husky/
│  └─ pre-commit           # 커밋 전 Secret & Lint 체크
│
├─ apps/
│  ├─ web/                 # Frontend (React / Next.js / Vite)
│  ├─ api/                 # Backend API (NestJS)
│  └─ ml-api/              # Python API (FastAPI, ML/Async tasks)
│
├─ packages/
│  ├─ shared/              # Shared utilities, constants
│  └─ api-contracts/       # API contracts (OpenAPI, DTO, schema)
│
├─ infra/
│  ├─ docker/          # Dockerfiles
│  └─ terraform/       # Infrastructure as Code (optional)
│
├─ .pre-commit-config.yaml # Gitleaks, Bandit, ESLint 설정
├─ package.json            # pnpm workspace & Turborepo 설정
└─ README.md

```

---

## 🔒 2. Security & Quality Automation

이 프로젝트는 개발 생산성을 저해하지 않으면서도 보안 구멍을 최소화하기 위해 **3단계 계층 구조**를 따릅니다.

### **Phase 1: Local Prevention (Pre-commit)**

개발자의 로컬 환경에서 가장 빠르게 피드백을 주어 오염된 코드가 저장소에 올라오는 것을 원천 차단합니다.

- **Secret Scanning:** `Gitleaks`를 통해 AWS 키, API 토큰 등 민감 정보 유출 차단.
- **Static Analysis:** \* **JS/TS:** `ESLint` (`eslint-plugin-security` 포함)로 보안 취약 문법 탐지.
- **Python:** `Flake8`, `Bandit`을 사용한 취약점 스캐닝.

- **Formatting:** `Prettier`, `Black`을 강제하여 코드 가독성 및 리뷰 효율 증대.

### **Phase 2: CI Guardrail (GitHub Actions)**

`Push` 또는 `Pull Request`가 발생할 때마다 실행되며, **매일 정해진 시간(예: 자정)에 스케줄링**되어 주기적인 보안 감사를 수행합니다. 모든 검사를 통과해야만 머지가 가능합니다.

- **Dependency Audit:** \* `pnpm audit --audit-level=high`: 취약한 npm 패키지 발견 시 즉시 중단.
- `pip-audit`: FastAPI 의존성 라이브러리의 보안 결함 탐지.

- **SAST (Static Application Security Testing):**
- **Semgrep:** 고속 스캐닝을 통해 SQL Injection, XSS 패턴 탐지 (`ci-pipeline.yml`).
- **CodeQL:** GitHub 전용 엔진으로 심층적인 데이터 흐름(Taint Analysis) 분석 (`codeql-scan.yml`).

- **Build Check:** 각 서비스(Web, API, ML)가 독립적으로 정상 빌드되는지 확인.

### **Phase 3: Real-time Alert (Multi-Channel Integration)**

CI 파이프라인에서 문제가 발견되면 즉시 개발팀의 주요 소통 채널로 상황을 전파합니다.

- **Slack:** 팀 채널에 실시간으로 핵심 오류와 담당자 정보를 요약하여 보냅니다.
- **Naver Mail:** 상세한 분석 리포트와 로그가 포함된 메일을 담당자 및 팀 리더에게 발송합니다.
- **Discord:** 개발자 커뮤니티나 관련 채널에 빌드/보안 상태에 대한 간략한 알림을 보냅니다.

- **Trigger:** 빌드 실패, 테스트 실패, 보안 취약점 발견 시 작동.
- **Contents:** 오류가 발생한 워크플로우 링크, 커밋 메시지, 담당자(Author) 정보를 포함하여 각 채널 특성에 맞게 발송.

---

## 🛠️ 3. 핵심 도구 상세 (Tool Stack)

| 단계          | 도구 (Tool)                              | 대상 서비스  | 주요 역할                          |
| ------------- | ---------------------------------------- | ------------ | ---------------------------------- |
| **Hook**      | `Husky` & `pre-commit`                   | 전체         | Git Hook 관리 및 자동 검사 실행    |
| **Secrets**   | `Gitleaks`                               | 전체         | 하드코딩된 자격 증명(Key) 탐지     |
| **JS/TS**     | `ESLint Security`                        | web, api     | 취약한 함수 사용 금지 및 보안 린팅 |
| **Python**    | `Bandit`, `Safety`                       | ml-api       | FastAPI 코드 보안 및 의존성 스캔   |
| **SCA**       | `pnpm audit`                             | Node.js 기반 | 취약한 라이브러리 버전 머지 차단   |
| **Deep SAST** | `CodeQL`                                 | 전체         | 심층 보안 결함 분석 (GitHub 통합)  |
| **Alert**     | `Slack/Discord Webhook`, `E-mail (SMTP)` | 전체         | 실패 시 다중 채널 실시간 알림      |

---

## 🔗 4. Communication & Flow

- **Frontend → Backend:** REST API 기반 통신.
- **Backend → FastAPI:** 고부하 연산(ML, 데이터 처리) 위임. (HTTP/REST 또는 gRPC)
- **Shared Contract:** `api-contracts`를 통해 모든 서비스 간 DTO 및 API 스키마 공유.

---

## 🚀 5. Getting Started

### **1. 전제 조건 (Prerequisites)**

- Node.js 18+ 및 `pnpm`
- Python 3.9+
- `pre-commit` 설치: `pip install pre-commit`

### **2. 로컬 보안 설정**

```bash
# 의존성 설치
pnpm install

# pre-commit hook 활성화
pre-commit install

# 전체 파일 수동 보안 검증 (커밋 전 테스트)
pre-commit run --all-files

```

### **3. 알림 설정**

GitHub 저장소의 `Settings > Secrets > Actions`에 아래 변수들을 추가해야 합니다.

- **Slack:**
  - `SLACK_WEBHOOK_URL`: 알림을 받을 채널의 Incoming Webhook URL.
- **Discord:**
  - `DISCORD_WEBHOOK_URL`: 알림을 받을 Discord 채널의 Webhook URL.
- **Naver Mail (SMTP):**
  - `NAVER_MAIL_USER`: 발신자로 사용할 네이버 메일 주소.
  - `NAVER_MAIL_PASSWORD`: 해당 네이버 계정의 앱 비밀번호 또는 2단계 인증 앱 암호.
