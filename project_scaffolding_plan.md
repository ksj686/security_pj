# 🚧 Project Scaffolding Plan

이 문서는 DevSecOps 블루프린트에 기반한 프로젝트 구조 생성을 위해 남은 작업들을 정리한 계획서입니다. 세션 재시작 후 이어서 진행할 수 있습니다.

---

### ✅ 현재까지 완료된 작업

- `.github/workflows` 폴더 생성
- `.husky` 폴더 생성
- `apps` 폴더 생성
- `apps/web` 폴더 생성
- `apps/api` 폴더 생성
- `apps/ml-api` 폴더 생성
- `packages` 폴더 생성
- `packages/shared` 폴더 생성
- `packages/api-contracts` 폴더 생성
- `infra` 폴더 생성
- `infra/docker` 폴더 생성
- `infra/terraform` 폴더 생성
- `.pre-commit-config.yaml` 파일 생성
- `package.json` 파일 생성 (pnpm 워크스페이스용)
- `.gitignore` 파일 생성
- `.github/workflows/ci-security.yml` 파일 생성
- `.github/workflows/reusable-notify.yml` 파일 생성
- `apps/web/package.json` 파일 생성
- `apps/api/package.json` 파일 생성
- `apps/ml-api/requirements.txt` 파일 생성
- `packages/shared/index.js` 파일 생성
- `packages/api-contracts/schema.json` 파일 생성
- `infra/docker/Dockerfile` 파일 생성

---

### 📋 앞으로 진행할 작업 (사용자 직접 수행)

아래 작업들은 사용자가 직접 수행해야 하는 후속 단계입니다.

#### 1. Git 저장소 설정 및 GitHub에 Push

로컬 프로젝트를 Git으로 관리하고 원격 GitHub 저장소에 연결합니다.

```bash
# 1. Git 로컬 저장소 초기화
git init
git add .
git commit -m "feat: Initial commit with project scaffolding"

# 2. GitHub에서 새로운 저장소를 생성합니다. (예: my-devsecops-project)

# 3. 로컬 저장소와 원격 저장소를 연결합니다.
git remote add origin https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git

# 4. 코드를 원격 저장소로 Push합니다.
git branch -M main
git push -u origin main
```

#### 2. Pre-commit 훅(Hook) 활성화 (완료됨)

`pre-commit` 도구 설치 (`pip install pre-commit`) 및 Git 저장소에 훅 설치 (`python -m pre_commit install`)는 이미 성공적으로 완료되었습니다. 이제 다음 Git 명령어를 통해 `pre-commit` 훅이 정상적으로 작동하는지 확인할 수 있습니다.

```bash
git add .
git commit -m "feat: Check pre-commit hooks"
```

이제부터 `git commit`을 실행할 때마다 `.pre-commit-config.yaml`에 정의된 검사들이 자동으로 실행됩니다.

    #### 3. GitHub Actions 알림 시크릿 설정

    CI 파이프라인이 실패했을 때 Slack 또는 Discord로 알림을 받기 위해 GitHub 저장소에 웹훅(Webhook) URL을 시크릿으로 등록해야 합니다.

    1.  생성한 GitHub 저장소로 이동합니다.
    2.  `Settings` > `Secrets and variables` > `Actions` 메뉴로 이동합니다.
    3.  `New repository secret` 버튼을 클릭하여 아래 시크릿 중 사용할 시크릿을 추가합니다.
    -   `SLACK_WEBHOOK_URL`: Slack 알림을 사용하려면 여기에 웹훅 URL을 입력합니다.
    -   `DISCORD_WEBHOOK_URL`: Discord 알림을 사용하려면 여기에 웹훅 URL을 입력합니다.

이 설정이 완료되면 `main` 브랜치에 코드가 Push될 때마다 `.github/workflows/ci-security.yml` 워크플로우가 실행되며, 실패 시 등록된 채널로 알림이 전송됩니다.
