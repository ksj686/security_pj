# Monorepo 구조 (React + NestJS + FastAPI)

이 저장소는 **프론트엔드(React)**, **백엔드(NestJS)**, **Python API(FastAPI)** 를 하나의 repository에서 관리하는 **Monorepo** 구조를 따른다.

---

## Repository Structure

```txt
repo-root/
├─ apps/
│  ├─ web/            # Frontend (React / Next.js / Vite)
│  ├─ api/            # Backend API (NestJS)
│  └─ ml-api/         # Python API (FastAPI, ML/Async tasks)
│
├─ packages/
│  ├─ shared/         # Shared utilities, constants
│  └─ api-contracts/  # API contracts (OpenAPI, DTO, schema)
│
├─ infra/
│  ├─ docker/         # Dockerfiles
│  └─ terraform/      # Infrastructure as Code (optional)
│
├─ docker-compose.yml
├─ package.json       # Workspace configuration
└─ README.md
```

---

## Components

### Frontend (`apps/web`)

- React 기반 애플리케이션
- 사용자 인터페이스 담당
- API 통신은 NestJS 또는 FastAPI와 HTTP 기반으로 수행

---

### Backend API (`apps/api`)

- NestJS 기반 메인 백엔드
- 인증, 비즈니스 로직, 데이터베이스 접근 담당
- FastAPI 서비스와 내부 API 통신 가능

---

### Python API (`apps/ml-api`)

- FastAPI 기반 서비스
- 머신러닝, 고부하 연산, 비동기 처리 담당
- OpenAPI 스펙을 통해 다른 서비스와 연동

---

## Shared Packages (`packages`)

### `shared`

- 공통 유틸리티
- 상수, 헬퍼 함수
- (선택) 공통 타입 정의

### `api-contracts`

- OpenAPI(Swagger) 정의
- API 요청/응답 스키마
- 서비스 간 계약(Contract) 관리

---

## Communication

- Frontend → Backend: HTTP (REST)
- Backend → FastAPI:

  - HTTP (REST)
  - gRPC (선택)
  - Message Queue (Kafka, RabbitMQ 등, 선택)

---

## Advantages

- 단일 저장소에서 전체 시스템 관리
- API 변경 시 프론트/백엔드 동기화 용이
- 공통 코드 및 스펙 공유
- CI/CD 및 버전 관리 단순화

---

## When to Split Repositories

다음과 같은 경우 개별 repository 분리를 고려한다.

- 팀 규모 확장
- 서비스별 독립적인 배포 주기 필요
- 보안 또는 접근 권한 분리 요구
- 외부 공개 또는 오픈소스화

---

## Notes

- Node.js 기반 워크스페이스 도구(pnpm, Yarn, Nx, Turborepo) 사용 권장
- FastAPI 서비스는 Docker 기반 독립 배포를 권장

---

원하면

- **실제 README에 바로 쓸 수 있는 더 간결한 버전**
- **다이어그램용 구조 설명**
- **CI/CD 섹션 추가**

중에서 하나 더 만들어줄게.
