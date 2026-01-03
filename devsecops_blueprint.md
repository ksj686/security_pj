# 🚀 Universal DevSecOps Blueprint: A Language-Agnostic Guide

이 문서는 특정 프로그래밍 언어나 프레임워크에 구애받지 않고, 모든 종류의 저장소에 적용할 수 있는 **표준 DevSecOps(개발-보안-운영) 자동화 청사진**을 제시합니다. 코드 작성부터 배포까지 전 과정에 보안을 내재화하는 것을 목표로 합니다.

---

## 🌟 Core Security Workflow: A Quick Start

이 청사진의 핵심 워크플로우를 3단계로 요약하면 다음과 같습니다.

- **Step 1: Local Prevention (Pre-commit)**

  - **Gitleaks:** API Key, DB 패스워드 등 민감 정보가 Git 히스토리에 기록되는 것을 원천 차단합니다.
  - **Security Linting:** `eslint-plugin-security` (JavaScript), `Bandit` (Python) 등을 통해 개발 즉시 기본적인 취약 문법을 교정합니다.

- **Step 2: CI Guardrail (Code Analysis)**

  - **Dependency Audit (SCA):** `pnpm audit`, `pip-audit` 등을 통해 알려진 취약점을 가진 오픈소스 라이브러리의 유입을 차단합니다.
  - **Static Analysis (SAST):** `Semgrep`(고속)로 CI 단계에서 빠른 피드백을 제공하고, `CodeQL`(심층)으로 데이터 흐름을 정밀 분석하여 복합적인 취약점을 탐지합니다.

- **Step 3: Artifact Security (Final Scan)**
  - **Container Scanning:** `Trivy`와 같은 도구로 Docker 이미지 빌드 후, 내부의 OS 패키지 및 시스템 라이브러리에 존재하는 잠재적 취약점(CVE)을 최종 스캔합니다.
  - **Deployment Policy:** 이 단계에서 **`Critical` 또는 `High` 레벨의 취약점이 발견되면 배포를 즉시 중단**하고, 개발팀에 알림을 보내 수정을 강제합니다.

---

## filozofija: Shift-Left & Defense in Depth

- **Shift-Left (좌측 이동):** 개발 수명 주기(SDLC)의 초반 단계(좌측)에서 보안 문제를 조기에 발견하고 해결하여, 후반 단계의 비용과 위험을 최소화합니다.
- **Defense in Depth (심층 방어):** 단일 보안 조치에 의존하는 대신, 여러 단계에 걸쳐 계층적인 보안 장벽을 구축하여 전체 시스템의 안정성을 높입니다.

---

## 🔒 4-Phase Security Model

이 청사진은 4단계의 계층적 보안 모델을 기반으로 합니다.

### **Phase 1: Local Prevention (개발자 환경에서의 사전 방어)**

코드가 원격 저장소에 커밋되기 전, 개발자의 로컬 머신에서 1차적으로 문제를 차단합니다. 즉각적인 피드백을 통해 개발 생산성을 유지하면서 기본적인 실수를 방지합니다.

- **범주 1: 민감 정보 스캐닝 (Secret Scanning):** 소스 코드에 하드코딩된 API 키, 비밀번호, 인증서 등의 유출을 원천 차단합니다.
- **범주 2: 코드 품질 및 스타일 일관성 (Linting & Formatting):** 정해진 규칙에 따라 코드를 분석하고 자동으로 스타일을 교정하여 가독성과 잠재적 오류 발생 가능성을 줄입니다.
- **범주 3: 기초 보안 스캐닝 (Basic SAST):** 언어별로 알려진 위험한 코드 패턴이나 문법을 탐지합니다.

### **Phase 2: CI Guardrail (코드 레벨 자동 분석)**

`Push` 또는 `Pull Request` 시 CI(Continuous Integration) 서버에서 코드와 의존성을 심층 분석합니다.

- **범주 4: 의존성 분석 (SCA - Software Composition Analysis):** 프로젝트가 사용하는 오픈소스 라이브러리나 패키지의 알려진 보안 취약점을 탐지하고 차단합니다.
- **범주 5: 심층 정적 분석 (Deep SAST - Static Application Security Testing):** 코드의 실행 없이 데이터 흐름, 로직 등을 심층 분석하여 복잡한 보안 취약점을 찾아냅니다.

### **Phase 3: Real-time Alerting (실시간 알림)**

CI/CD 파이프라인에서 보안 문제가 발견되거나 빌드가 실패했을 때, 그 즉시 개발팀의 핵심 커뮤니케이션 채널(Slack, 이메일, Discord 등)로 상황을 전파하여 신속한 대응을 유도합니다.

---

## 🛠️ Recommended Tool Stack (by Category)

| 단계                   | 범주                     | 추천 도구 (예시)                                                         | 주요 역할                                                       |
| :--------------------- | :----------------------- | :----------------------------------------------------------------------- | :-------------------------------------------------------------- |
| **Phase 1<br>(Local)** | **Secret Scanning**      | `Gitleaks`, `TruffleHog`                                                 | Git 히스토리를 포함한 코드 내 민감 정보 탐지 (언어 무관)        |
|                        | **Linting & Formatting** | `Prettier`, `ESLint` (JS), `Black`, `Ruff` (Python), `Checkstyle` (Java) | 코드 스타일 통일 및 잠재적 오류 사전 탐지                       |
|                        | **Basic SAST**           | `Bandit` (Python), `eslint-plugin-security` (JS)                         | 언어별 기본적인 보안 취약 패턴 스캔                             |
| **Phase 2<br>(CI)**    | **SCA**                  | `npm/pnpm audit` (JS), `pip-audit` (Python), `OWASP Dependency-Check`    | 오픈소스 라이브러리 보안 취약점 분석                            |
|                        | **Deep SAST**            | `CodeQL`, `Semgrep`                                                      | 데이터 흐름 및 코드 구조 기반의 심층 보안 분석 (다중 언어 지원) |
|                        | **Container Scanning**   | `Trivy`, `Grype`                                                         | Docker 컨테이너 이미지 취약점 스캔                              |
|                        | **DAST**                 | `OWASP ZAP`                                                              | 실행 중인 애플리케이션의 런타임 취약점 분석                     |
| **Phase 3<br>(Alert)** | **Alerting**             | `GitHub Actions` + `Slack`, `Discord Webhook`, `SMTP`                    | CI/CD 실패 및 보안 이벤트 발생 시 다중 채널로 실시간 알림       |

---

## 🚀 Generic Implementation Guide

> 💡 **For detailed code examples and complete workflow files, please refer to the [Detailed Implementation Guide](./devsecops_implementation_guide.md).**

### 1. 로컬 보안 설정 (`pre-commit` 활용)

`pre-commit`은 특정 Git 이벤트(주로 `pre-commit`) 발생 시 자동으로 스크립트를 실행해주는 프레임워크입니다.

1.  **`pre-commit` 설치:**

    ```bash
    # Python 환경 필요
    pip install pre-commit
    ```

2.  **`.pre-commit-config.yaml` 파일 생성:** 프로젝트 루트에 아래와 같이 설정 파일을 추가합니다. 사용할 도구들을 이곳에 정의합니다.

    ```yaml
    # .pre-commit-config.yaml 예시
    repos:
      - repo: https://github.com/gitleaks/gitleaks
        rev: v8.18.2
        hooks:
          - id: gitleaks
      - repo: https://github.com/pre-commit/pre-commit-hooks
        rev: v4.6.0
        hooks:
          - id: check-yaml
          - id: end-of-file-fixer
          - id: trailing-whitespace
      - repo: https://github.com/psf/black
        rev: 24.4.2
        hooks:
          - id: black
            language_version: python3
    ```

3.  **Hook 활성화:**
    ```bash
    pre-commit install
    ```
    이제부터 `git commit`을 실행할 때마다 위에서 정의한 훅들이 자동으로 실행됩니다.

### 2. CI/CD 파이프라인 설정 (GitHub Actions 예시)

`.github/workflows` 디렉터리 아래에 워크플로우 파일을 생성하여 `Phase 2` 검사를 자동화합니다.

- **`ci-main.yml`:** 빌드, 테스트, SCA, SAST(Semgrep 등)를 포함하는 메인 파이프라인을 정의합니다.
- **`codeql.yml`:** CodeQL 분석을 위한 워크플로우를 별도로 정의합니다. (Public Repo 무료)
- **`reusable-notify.yml`:** 알림 전용 재사용 워크플로우를 만들어, 다른 파이프라인들이 실패 시 이를 호출하도록 구성합니다.

### 3. 알림 설정

GitHub 저장소의 `Settings > Secrets and variables > Actions`에 아래의 Secret 값들을 등록하여 알림을 활성화합니다.

- `SLACK_WEBHOOK_URL`: Slack 알림을 받을 채널의 Incoming Webhook URL.
- `DISCORD_WEBHOOK_URL`: Discord 알림을 받을 채널의 Webhook URL.
- `NAVER_MAIL_USER`: 발신자로 사용할 네이버 메일 주소.
- `NAVER_MAIL_PASSWORD`: 해당 네이버 계정의 앱 비밀번호 또는 2단계 인증 앱 암호.

---

이 청사진을 바탕으로 각 프로젝트의 특성에 맞게 도구를 선택하고 적용함으로써, 일관성 있고 강력한 보안 자동화 체계를 구축할 수 있습니다.
