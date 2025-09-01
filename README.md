
# GRI 공시 항목 관리 서비스

GRI(Global Reporting Initiative) 기준에 따라 기업의 지속가능 경영 데이터를 항목별로 등록, 수정, 분석할 수 있는 서비스입니다.  
ESG 공시 대응을 위한 핵심 기능 중 하나로, 사용자가 입력한 데이터를 바탕으로 진행률을 자동 계산하여 관리 효율성을 높입니다.

---

## 주요 기능

| 기능 | 설명 |
|------|------|
| GRI 전체 조회 | 전체 GRI 항목을 목록 형태로 조회 |
| GRI 단건 조회 | 코드 기반 특정 항목 상세 조회 |
| GRI 등록 | 새로운 GRI 항목 입력 |
| GRI 수정 | 기존 항목 수정 (소유자 확인 포함) |
| GRI 삭제 | 항목 삭제 (소유자 확인 포함) |
| GRI 진행률 조회 | 전체 항목 중 입력 완료 항목의 비율 계산 |

---

## 인증 흐름

- 모든 요청은 공통적으로 `X-MEMBER-ID` 헤더 인증을 요구합니다.
- 인증 실패 시 `401 Unauthorized` 응답을 반환하고 요청 처리를 중단합니다.

---

## 서비스 흐름도

```mermaid
flowchart TD

%% 공통 인증 처리
    Start((Start))
    Start --> Auth["X-MEMBER-ID 인증"]
    Auth --> AuthCheck{"인증 성공 여부"}
    AuthCheck -- "아니오" --> Error401["401 Unauthorized"] --> EndErr((End))
    AuthCheck -- "예" --> Route["요청 분기"]

%% GRI 전체 조회
    Route --> GriAll["GRI 전체 조회 요청"]
    GriAll --> GriAllSvc["griDisclosureService.getAll()"]
    GriAllSvc --> GriAllResp["전체 목록 반환"]
    GriAllResp --> End1((End))

%% GRI 단건 조회
    Route --> GriOne["GRI 단건 조회 요청"]
    GriOne --> GriOneSvc["griDisclosureService.getByCode()"]
    GriOneSvc --> GriOneResp["단건 항목 반환"]
    GriOneResp --> End2((End))

%% GRI 항목 등록
    Route --> GriCreate["GRI 항목 등록 요청"]
    GriCreate --> GriCreateSvc["griDisclosureService.create()"]
    GriCreateSvc --> GriCreateResp["등록 완료"]
    GriCreateResp --> End3((End))

%% GRI 항목 수정
    Route --> GriUpdate["GRI 항목 수정 요청"]
    GriUpdate --> UpdateSvc["소유자 확인 후 수정"]
    UpdateSvc --> GriUpdateResp["수정 완료"]
    GriUpdateResp --> End4((End))

%% GRI 항목 삭제
    Route --> GriDelete["GRI 항목 삭제 요청"]
    GriDelete --> DeleteSvc["소유자 확인 후 삭제"]
    DeleteSvc --> GriDeleteResp["삭제 완료"]
    GriDeleteResp --> End5((End))

%% GRI 진행률 조회
    Route --> GriProgress["GRI 진행률 조회 요청"]
    GriProgress --> ProgressSvc["진행률 계산 로직"]
    ProgressSvc --> GriProgressResp["진행률 반환"]
    GriProgressResp --> End6((End))

%% 색상 스타일 정의
    classDef forest fill:#e6f4ea,stroke:#2e7d32,stroke-width:1.5px,color:#2e7d32;
    classDef terminal fill:#d0f0c0,stroke:#1b5e20,color:#1b5e20;
    classDef error fill:#fdecea,stroke:#c62828,color:#c62828;

%% 클래스 적용
    class Start,End1,End2,End3,End4,End5,End6,EndErr terminal;
    class Auth,AuthCheck,Route,GriAll,GriAllSvc,GriAllResp,GriOne,GriOneSvc,GriOneResp,GriCreate,GriCreateSvc,GriCreateResp,GriUpdate,UpdateSvc,GriUpdateResp,GriDelete,DeleteSvc,GriDeleteResp,GriProgress,ProgressSvc,GriProgressResp forest;
    class Error401 error;
````

---

## 기술 스택

* **Spring Boot + REST API**
* 인증 기반 요청 처리 (`X-MEMBER-ID` 헤더)
* GRI 항목 코드 기반 CRUD
* GRI 진행률 계산 로직 내장
* 추후 확장: 공시 템플릿 기반 자동 입력 추천

---

## 개발 기여

* ESG 항목 중 `GRI 시리즈(2~400)` 전체 입력 구조 설계
* 프론트엔드 상태관리 구조(Zustand)와 연동된 비동기 API 설계
* 입력/수정/진행률 계산까지 단일 서비스 흐름으로 통합 구현

---
