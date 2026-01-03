보안 관리 자동화 프로세스가 포함된 Monorepo 가이드 문서를 작성해 드립니다. 이 내용은 `README.md` 또는 `CONTRIBUTING.md`에 바로 활용하실 수 있도록 정리했습니다.

---

# 🛡️ Secure Monorepo Template (React + NestJS + FastAPI)

이 저장소는 **보안 자동화(Security Automation)**가 통합된 Full-stack Monorepo 샘플입니다. 코드 작성 단계부터 배포 전까지 단계별 보안 검사 및 **다중 채널(Slack, Naver Mail, Discord) 알림 시스템**을 포함합니다.

---

## 🏗️ Repository Structure

```txt
repo-root/
├─ .github/
│  └─ workflows/       # CI/CD (GitHub Actions) & 다중 채널 알림
├─ .husky/             # Git Hooks (pre-commit)
├─ apps/
│  ├─ web/             # React (Frontend)
│  ├─ api/             # NestJS (Backend)
│  └─ ml-api/          # FastAPI (Python API)
├─ packages/
│  ├─ shared/          # 공통 유틸리티
│  └─ api-contracts/   # OpenAPI / DTO 스키마
├─ .pre-commit-config.yaml # pre-commit 설정 파일
└─ package.json        # Turborepo / PNPM Workspace

```

---

## 🔒 Security & Quality Automation

이 프로젝트는 `devsecops_blueprint_final.md`에 정의된 4단계 보안 및 품질 관리 프로세스를 따릅니다.

### **Phase 1: Local Prevention (Pre-commit)**

커밋 전, 개발자의 로컬 환경에서 `pre-commit`을 통해 민감 정보 유출, 코드 스타일, 기초 보안 취약점을 자동으로 검사하고 차단합니다.

### **Phase 2: CI Guardrail (Code Analysis)**

`Push` 또는 `Pull Request` 시, GitHub Actions가 코드와 외부 라이브러리의 보안 취약점을 `CodeQL`, `Semgrep`, `npm/pnpm audit` 등으로 심층 분석합니다.

### **Phase 3: Artifact Security (Final Scan)**

코드 분석을 통과하면 Docker 이미지를 빌드하고, `Trivy`를 사용해 OS 및 시스템 레벨의 최종 취약점을 스캔하여 배포될 산출물의 안정성을 검증합니다.

### **Phase 4: Real-time Alerting**

모든 과정에서 보안 문제나 빌드 실패가 발생하면, 즉시 **Slack, Naver Mail, Discord** 등 핵심 채널로 알림을 전파하여 신속한 대응을 유도합니다.

---

## 🛠️ Automated Tools Detail

| 단계 (Phase)          | 도구 (Tool)                | 목적                                            |
| :-------------------- | :------------------------- | :---------------------------------------------- |
| **Phase 1: Local**    | `pre-commit`, `Husky`      | 커밋 전, 로컬 환경에서 자동 검사 실행           |
|                       | `Gitleaks`                 | 코드 내 민감 정보(API 키 등) 유출 차단          |
|                       | `ESLint`, `Bandit`, etc.   | 언어별 코드 품질 및 기초 보안 분석              |
| **Phase 2: CI**       | `CodeQL`, `Semgrep`        | SAST - 소스 코드의 보안 취약점 심층 분석        |
|                       | `npm audit`, `pip-audit`   | SCA - 오픈소스 라이브러리 취약점 검사           |
| **Phase 3: Artifact** | `Trivy`                    | 배포될 Docker 이미지의 OS 및 패키지 취약점 스캔 |
| **Phase 4: Alert**    | `Slack`, `Discord`, `SMTP` | CI/CD 실패 및 보안 이벤트 발생 시 실시간 알림   |

---

## 🚀 Getting Started

### 1. 전제 조건 (Prerequisites)

- **Node.js:** v18 (LTS) 또는 v20 (LTS)
- **Python:** 3.9 이상
- **Pre-commit:** `pipx install pre-commit` (권장) 또는 `pip install pre-commit`

### 2. 로컬 보안 설정

```bash
# 1. 프로젝트 의존성 설치
pnpm install

# 2. Git Hook 활성화
pre-commit install

# 3. (선택 사항) 전체 파일 수동 검사
pre-commit run --all-files

```

### 3. 알림 설정 (CI)

GitHub Repository Secrets에 아래 항목을 등록해야 합니다.

- **Slack:**
  - `SLACK_WEBHOOK_URL`: 알림을 받을 슬랙 채널의 웹훅 주소.
- **Naver Mail:**
  - `NAVER_MAIL_USER`: 네이버 메일 계정 (e.g., `username@naver.com`)
  - `NAVER_MAIL_PASSWORD`: 네이버 메일 앱 비밀번호
- **Discord:**
  - `DISCORD_WEBHOOK_URL`: 알림을 받을 디스코드 채널의 웹훅 주소.

---

## 🔗 Communication Flow

- **Frontend → Backend:** REST API 통신.
- **Backend → FastAPI:** 고부하 연산 및 ML 태스크 위임 (HTTP/gRPC).
- **Shared Contract:** `api-contracts`를 통해 모든 서비스 간 스키마 동기화.

---

## 📚 Learn More

이 프로젝트에 적용된 보안 자동화 방법론에 대한 더 자세한 내용은 아래 문서들을 참고하세요.

- **[Universal DevSecOps Blueprint](./devsecops_blueprint_final.md):** 모든 프로젝트에 적용할 수 있도록 일반화된 DevSecOps 청사진과 4단계 보안 모델을 설명합니다.
- **[Detailed Implementation Guide](./devsecops_implementation_guide.md):** 청사진의 각 단계를 실제로 구현하기 위한 상세한 코드 예제와 가이드를 제공합니다.

---
