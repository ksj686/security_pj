# 프로젝트 진행 상황 요약 (2026.02.04)

## 완료된 작업

오늘(2026년 2월 4일)은 GitHub Actions 워크플로우의 안정성과 기능 개선을 위해 다음과 같은 작업을 진행했습니다.

1.  **알림 시크릿 처리 개선**: `reusable-notify.yml`에서 `secrets` 참조 시 발생하던 파싱 오류를 해결하기 위해 `check-notification-status` 잡을 도입하고, 시크릿 존재 여부를 `output`으로 전달하는 방식으로 변경했습니다.
2.  **Semgrep 액션 마이그레이션**: 사용 중단된 `semgrep/semgrep-action@v2` 액션을 제거하고, `docker run` 명령을 통해 Semgrep을 직접 실행하고 SARIF 결과를 GitHub Security Dashboard에 업로드하는 방식으로 변경했습니다.
3.  **SARIF 경로 문제 해결**: `semgrep-results.sarif` 파일이 다음 스텝에서 인식되지 않던 문제를 `docker run` 명령의 볼륨 마운트를 통해 해결했습니다.
4.  **CodeQL 액션 버전 업데이트**: 모든 `github/codeql-action` 액션을 `v3`에서 `v4`로 업데이트하여 향후 발생할 수 있는 사용 중단 문제를 방지했습니다.
5.  **알림 조건 확장**: `notify-on-failure` 잡의 실행 조건을 `push` 이벤트 실패뿐만 아니라 `pull_request` 이벤트 실패 시에도 알림이 가도록 수정했습니다.
6.  **pip-audit 실패 처리 복원**: `pip-audit`가 취약점 발견 시 워크플로우를 실패시키도록 `|| true` 옵션을 제거했습니다. 이는 사용자 요청에 따른 변경으로, 취약점 발견 시 알림을 받기 위함입니다.
7.  **YAML 문법 오류 수정**: Pre-commit 훅에서 보고되던 `check-yaml` 및 `prettier` 오류를 해결하기 위해 `ci-security.yml` 파일 내 Semgrep 관련 스텝의 YAML 들여쓰기 오류를 수정했습니다.

## 현재까지 해결되지 않은 문제 및 다음 작업

현재 `pip-audit`에서 `Error: Process completed with exit code 1.`이 발생하며, 워크플로우가 실패했음에도 불구하고 알림(`notify`)이 전송되지 않는 문제가 남아있습니다.

### 문제 원인 추정

`notify-on-failure` 잡은 `security-analysis` 잡이 실패하고 (`failure()`) 워크플로우가 `push` 또는 `pull_request` 이벤트로 트리거되었을 때 실행됩니다. 현재 알림이 전송되지 않는다는 것은 이 조건 중 하나 이상이 충족되지 않았음을 의미합니다.

### 다음 작업 계획

1.  **`security-analysis` 잡의 최종 상태 확인**:
    - GitHub Actions 실행 로그에서 `pip-audit` 스텝이 실패한 후 `security-analysis` 잡의 **최종 상태**가 실제로 '실패(Failure)'로 표시되는지 확인해야 합니다.
    - 만약 `pip-audit` 실패에도 불구하고 `security-analysis` 잡이 '성공(Success)'으로 끝난다면, 알림 잡은 트리거되지 않습니다. 이 경우 `security-analysis` 잡의 다른 스텝들이 실패를 덮어쓰는지 확인해야 합니다.
2.  **워크플로우 트리거 이벤트 확인**:
    - `notify-on-failure` 잡의 `if` 조건에 따라, 워크플로우가 `push` 또는 `pull_request` 이벤트로 트리거되었는지 확인해야 합니다. 다른 이벤트(예: `workflow_dispatch`를 수동으로 실행한 경우)에서는 알림이 전송되지 않을 수 있습니다.

이 두 가지 사항을 확인하여 알림이 전송되지 않는 정확한 원인을 파악하고, 그에 맞는 추가 조치를 진행할 예정입니다.
