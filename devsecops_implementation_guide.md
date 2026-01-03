# 🛠️ DevSecOps Blueprint: Detailed Implementation Guide

이 문서는 `devsecops_blueprint_final.md`에서 설명하는 각 보안 단계를 실제로 어떻게 구현하는지에 대한 구체적인 코드 예제와 설정을 제공합니다.

---

## Phase 1: Local Prevention (Pre-commit)

개발자가 코드를 커밋하기 전에 로컬 환경에서 자동으로 보안 검사를 실행하여, 문제가 있는 코드가 저장소에 유입되는 것을 원천 차단합니다.

### 설정 파일: `.pre-commit-config.yaml`

프로젝트 루트 디렉터리에 아래 내용으로 `.pre-commit-config.yaml` 파일을 생성합니다.

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace # 끝에 있는 공백 제거
      - id: end-of-file-fixer # 파일 끝에 한 줄 추가
      - id: check-yaml # YAML 파일 문법 검사
      - id: check-added-large-files # 대용량 파일 추가 방지

  - repo: https://github.com/gitleaks/gitleaks
    rev: v8.18.2
    hooks:
      - id: gitleaks # 민감 정보(API 키, 비밀번호 등) 유출 방지

  - repo: https://github.com/psf/black
    rev: 24.4.2
    hooks:
      - id: black
        language_version: python3 # Python 코드 포맷팅

  - repo: https://github.com/prettier/pre-commit
    rev: 3.1.0
    hooks:
      - id: prettier
        types: [javascript, typescript, css, html, json, markdown, yaml] # 다양한 파일 타입 포맷팅
```

### 설치 및 활성화

1.  **`pre-commit` 도구 설치:**
    ```bash
    pip install pre-commit
    ```
2.  **Git Hook 활성화:**
    `bash
    # 프로젝트 루트에서 한 번만 실행
    pre-commit install
    `이제부터`git commit`을 실행할 때마다 위 `hooks`에 정의된 검사들이 자동으로 실행됩니다.

---

## Phase 2 & 3: CI Guardrail & Artifact Security

GitHub Actions를 사용하여 `Push` 또는 `Pull Request` 시점에 코드 분석(SAST, SCA) 및 아티팩트(Docker 이미지) 스캔을 수행합니다.

### 설정 파일: `.github/workflows/ci-security.yml`

```yaml
# .github/workflows/ci-security.yml
name: CI Security Pipeline

on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read # for actions/checkout
  security-events: write # for github/codeql-action/upload-sarif
  actions: read # for slackapi/slack-github-action

jobs:
  security-analysis:
    name: Security Analysis
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      # --- Phase 2: 코드 레벨 분석 ---

      # 1. SCA: Python 의존성 분석 (pip-audit)
      - name: Install and run pip-audit
        run: |
          pip install pip-audit
          pip-audit

      # 2. SAST: Semgrep 스캔 (고속)
      - name: Run Semgrep SAST Scan
        uses: semgrep/semgrep-action@v2
        with:
          # 기본 커뮤니티 정책(p/default)으로 스캔
          config: >-
            p/default

      # 3. SAST: CodeQL 분석 (심층) - 초기화
      - name: Initialize CodeQL
        uses: github/codeql-action/init@v3
        with:
          languages: "go, javascript, python" # 분석할 언어 지정

      # (여기에 빌드 및 테스트 스텝이 위치합니다)
      # - name: Build project...
      # - name: Run tests...

      - name: Perform CodeQL Analysis
        uses: github/codeql-action/analyze@v3

      # --- Phase 3: 아티팩트 보안 검증 ---

      # 4. Docker 이미지 빌드 (예시)
      - name: Build Docker image
        run: |
          docker build -t my-app:${{ github.sha }} .

      # 5. Container Scanning: Trivy 스캔
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: "my-app:${{ github.sha }}"
          format: "table"
          # Critical, High 등급의 취약점이 발견되면 워크플로우 실패 처리
          exit-code: "1"
          ignore-unfixed: true
          vuln-type: "os,library"
          severity: "CRITICAL,HIGH"
```

---

## Phase 4: Real-time Alerting

워크플로우 실패 시 Slack, Discord, 이메일 등으로 실시간 알림을 보냅니다.

### 설정 파일: `.github/workflows/reusable-notify.yml`

호출 전용(reusable) 워크플로우를 만들어 알림 로직을 중앙화합니다.

```yaml
# .github/workflows/reusable-notify.yml
name: Reusable Notification

on:
  workflow_call:
    inputs:
      status:
        required: true
        type: string
    secrets:
      SLACK_WEBHOOK_URL:
        required: false
      DISCORD_WEBHOOK_URL:
        required: false

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - name: Send Slack Notification on Failure
        if: ${{ inputs.status == 'failure' && secrets.SLACK_WEBHOOK_URL != '' }}
        uses: slackapi/slack-github-action@v1.26.0
        with:
          payload: |
            {
              "text": "CI/CD Pipeline Failed! 🚨",
              "blocks": [
                {
                  "type": "section",
                  "text": {
                    "type": "mrkdwn",
                    "text": "*Repository:* ${{ github.repository }}\n*Event:* ${{ github.event_name }}\n*Author:* ${{ github.actor }}\n<${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}|View Workflow Run>"
                  }
                }
              ]
            }
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}

      - name: Send Discord Notification on Failure
        if: ${{ inputs.status == 'failure' && secrets.DISCORD_WEBHOOK_URL != '' }}
        uses: tsg-oss/discord-webhook-action@v1
        with:
          webhook_url: ${{ secrets.DISCORD_WEBHOOK_URL }}
          title: "CI/CD Pipeline Failed! 🚨"
          description: |
            Repository: ${{ github.repository }}
            Author: ${{ github.actor }}
            Link: <${{ github.server_url }}/${{ github.repository }}/actions/runs/${{ github.run_id }}>
```

### `ci-security.yml`에서 알림 워크플로우 호출

메인 워크플로우의 마지막에 아래 `job`을 추가하여, 실패 시 알림을 보내도록 합니다.

```yaml
# ci-security.yml의 마지막에 추가
notify-on-failure:
  # security-analysis job이 실패했을 때만 실행
  if: ${{ failure() && github.event_name == 'push' }}
  needs: [security-analysis]
  uses: ./.github/workflows/reusable-notify.yml
  with:
    status: "failure"
  secrets: inherit
```
