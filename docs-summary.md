# 프로젝트 문서 요약

이 문서는 현재 프로젝트에 포함된 모든 마크다운(`.md`) 파일의 내용을 요약합니다.

## `README.md`

- **목적**: React, NestJS, FastAPI를 사용하는 보안 모노레포 템플릿의 가이드 역할
- **주요 내용**:
  - 저장소 구조, 4단계(로컬 방지, CI 가드레일, 아티팩트 보안, 실시간 알림) 보안 및 품질 자동화 프로세스
  - 각 단계별 자동화 도구 상세 설명
  - 시작 가이드(전제 조건, 로컬 보안 및 CI 알림 설정 포함)
- **특징**: DevSecOps 청사진 및 구현 가이드와 같은 상세 문서 링크 제공

## `architecture.md`

- **목적**: React 프론트엔드, NestJS 백엔드, FastAPI 파이썬 API를 사용하는 프로젝트의 모노레포 아키텍처 설명
- **주요 내용**:
  - 저장소 구조, 구성 요소(프론트엔드, 백엔드, 파이썬 API)
  - 공유 패키지(`shared`, `api-contracts`), 서비스 간 통신 방식
  - 모노레포의 장점 및 저장소 분리 시점에 대한 가이드라인

## `devsecops_blueprint_final.md`

- **목적**: 보편적인 언어 독립적 DevSecOps 청사진 제시
- **주요 내용**:
  - 4단계 보안 모델(로컬 방지, CI 가드레일, 아티팩트 보안, 실시간 알림)
  - "Shift-Left" 및 "심층 방어" 철학
  - 각 카테고리별 권장 도구 스택
  - `pre-commit`을 사용한 로컬 보안 및 GitHub Actions를 사용한 CI/CD 구현 가이드

## `devsecops_blueprint.md`

- **목적**: `devsecops_blueprint_final.md`와 유사한 이전 또는 대체 버전의 DevSecOps 청사진
- **주요 내용**:
  - 4단계 보안 모델, 권장 도구, 구현 지침
- **특징**: `final` 버전에 비해 구조와 강조점이 약간 다름

## `devsecops_implementation_guide.md`

- **목적**: DevSecOps 청사진 구현을 위한 상세한 코드 예제 및 구성 제공
- **주요 내용**:
  - 로컬 방지를 위한 `.pre-commit-config.yaml` 예시
  - CI 가드레일 및 아티팩트 보안을 위한 `.github/workflows/ci-security.yml`
  - Slack 및 Discord 실시간 알림을 위한 `.github/workflows/reusable-notify.yml`

## `GEMINI.md`

- **목적**: AI 모델(Gemini)을 위한 한국어 지침 목록
- **주요 내용**:
  - 정확한 정보 제공, 추측 지양
  - 코드 수정 처리 방식, 서버 관련 작업 미수행 등

## `new_arch.md`

- **목 '적**': `README.md` 및 `architecture.md`와 유사한 보안 모노레포 프레임워크의 또 다른 버전
- **주요 내용**:
  - 3단계 보안 및 품질 자동화 프로세스(로컬 방지, CI 가드레일, 실시간 알림)
  - 특정 도구 스택, 시작 가이드
  - 저장소 구조 및 통신 흐름

## `project_scaffolding_plan.md`

- **목적**: DevSecOps 청사진 기반의 프로젝트 구조 설정을 위한 남은 작업 요약
- **주요 내용**:
  - 완료된 작업(폴더 및 파일 생성)
  - 사용자가 수행해야 할 후속 단계 (Git 저장소 초기화, GitHub 푸시, GitHub Actions 시크릿 설정 등)
