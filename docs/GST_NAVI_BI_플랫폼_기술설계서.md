# GST NAVI BI 플랫폼 — 기술 설계서

**문서 버전**: v1.0  
**작성일**: 2026-04-01  
**작성 주체**: IT기획 / 아키텍처 / 데이터 / AI / 프론트엔드 / 백엔드 (6인 전문가 협업)  
**분류**: 내부 기밀 — 개발팀 한정 배포  

---

## 목차

- [I. 프로젝트 개요](#i-프로젝트-개요)
- [II. 시스템 아키텍처](#ii-시스템-아키텍처)
- [III. 데이터베이스 설계](#iii-데이터베이스-설계)
- [IV. 데이터 인터페이스](#iv-데이터-인터페이스)
- [V. 노코드 기능 상세](#v-노코드-기능-상세)
- [VI. AI/ML 파이프라인](#vi-aiml-파이프라인)
- [VII. 인프라 및 배포](#vii-인프라-및-배포)
- [VIII. 기술 스택 종합](#viii-기술-스택-종합)
- [IX. 개발 일정 (WBS)](#ix-개발-일정-wbs)
- [X. 투자 계획](#x-투자-계획)
- [XI. 기대 효과 및 ROI](#xi-기대-효과-및-roi)

---

## I. 프로젝트 개요

### 1. 비전

> **"코딩 없이 운영 가능한 제조업 특화 BI 플랫폼"**

GST NAVI BI 플랫폼은 기존 경영진 전용 고정 대시보드(GST NAVI v2)를 전사적 셀프서비스 BI 플랫폼으로 확장하는 프로젝트다. 코드 한 줄 작성 없이 데이터소스 연결부터 차트 설계, KPI 계산, 보고서 생성, AI 인사이트까지 전 과정을 운영할 수 있는 제조업 특화 BI 플랫폼을 목표로 한다.

### 2. AS-IS / TO-BE

```
AS-IS (GST NAVI v2 현재)                     TO-BE (GST NAVI BI 플랫폼)
┌──────────────────────────┐                 ┌─────────────────────────────────────┐
│  고정 대시보드              │                 │  GST NAVI BI Platform                │
│  • 9개 탭                 │                 │                                     │
│  • 31개 고정 차트           │    ────────▶    │  ✦ Executive Dashboard (기존 고도화)  │
│  • 1개 데이터소스 (엑셀)    │                 │  ✦ Self-Service BI Studio (신규)     │
│  • 경영진 5~10명           │                 │  ✦ Report Builder & Scheduler (신규) │
│  • 읽기 전용               │                 │  ✦ 다중 데이터소스: ERP+MES+IoT      │
│  • 개발자 코딩 필요          │                 │  ✦ AI 자연어 분석 / 예측 모델        │
│                           │                 │  ✦ 전사 50~200명 사용               │
└──────────────────────────┘                 └─────────────────────────────────────┘
```

### 3. 목표 기능 요약

| 영역 | 기능 | 노코드 여부 |
|------|------|:---------:|
| 데이터소스 | SAP/DB/API/Excel UI 연결 설정 | ✅ |
| 시맨틱 레이어 | 비즈니스 용어 ↔ SQL 매핑 편집기 | ✅ |
| 차트 빌더 | 드래그&드롭 15종 차트 설계 | ✅ |
| KPI 계산 | Excel-like 수식 편집기 | ✅ |
| 대시보드 | 위젯 배치 + 필터 연동 디자이너 | ✅ |
| 보고서 | WYSIWYG 섹션 편집 + 스케줄링 | ✅ |
| Alert | 조건 + 액션 규칙 GUI | ✅ |
| AI 질의 | 한국어 자연어 → SQL → 차트 | ✅ |
| 예측 모델 | 매출/장비고장/이탈 예측 | ✅ |
| 권한 관리 | 역할/사용자/리소스 매트릭스 GUI | ✅ |

### 4. 대상 사용자

| 역할 | 인원 | 주요 사용 기능 |
|------|:----:|--------------|
| 경영진 (CEO/CFO/COO) | 5명 | Executive Dashboard, 슬라이드쇼, AI 인사이트 |
| 부서장 | 15명 | 부서별 대시보드, KPI Alert, 보고서 수신 |
| 분석가 | 20명 | BI Studio, 차트 빌더, 자연어 질의 |
| 현업 담당자 | 60~160명 | 부서 대시보드 열람, 보고서 열람, 데이터 업로드 |
| IT 관리자 | 3명 | 커넥터 관리, 사용자 권한, 시스템 모니터링 |

### 5. 핵심 성과 지표 (KPI)

- 대시보드 생성 시간: 개발자 2주 → 사용자 30분
- 보고서 생성 시간: 수동 4시간 → 자동 5분
- 데이터 접근 가능 사용자: 10명 → 200명
- 데이터소스 수: 1개(엑셀) → 10개 이상
- 쿼리 응답 시간: 5초 이내 (95th percentile)

---

## II. 시스템 아키텍처

### 1. 전체 아키텍처 (5-Layer 상세 다이어그램)

```
┌──────────────────────────────────────────────────────────────────────────────────────┐
│                              LAYER 1: Presentation Layer                              │
│                                                                                      │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐ │
│  │  Executive      │  │  BI Studio      │  │  Report         │  │  Mobile PWA     │ │
│  │  Dashboard      │  │  (Self-Service) │  │  Builder        │  │  App            │ │
│  │                 │  │                 │  │                 │  │                 │ │
│  │ • 9탭 대시보드   │  │ • 차트 빌더      │  │ • WYSIWYG 편집  │  │ • 반응형 뷰     │ │
│  │ • 슬라이드쇼     │  │ • 데이터 탐색    │  │ • 스케줄링       │  │ • KPI 카드      │ │
│  │ • 드릴다운       │  │ • 자연어 질의    │  │ • PDF/Excel 출력 │  │ • Alert 수신    │ │
│  │ • AI 인사이트    │  │ • 대시보드 디자인 │  │ • 공유/구독      │  │                 │ │
│  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘  └────────┬────────┘ │
│           └───────────────────┬┴───────────────────┬┘                    │           │
│                               │                    │                     │           │
│  React 19 + TypeScript + Vite │ ECharts 6          │ Tailwind CSS        │ PWA SW    │
└───────────────────────────────┼────────────────────┼─────────────────────┼───────────┘
                                │                    │                     │
┌───────────────────────────────▼────────────────────▼─────────────────────▼───────────┐
│                              LAYER 2: API Gateway Layer                               │
│                                                                                      │
│  ┌─────────────────────────────────────────────────────────────────────────────────┐ │
│  │                     API Gateway (Node.js / Express + nginx)                     │ │
│  │                                                                                 │ │
│  │   ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────────┐   │ │
│  │   │  JWT     │  │  Rate    │  │  Query   │  │  Request │  │  CORS /      │   │ │
│  │   │  Auth    │  │  Limiter │  │  Cache   │  │  Logger  │  │  Security    │   │ │
│  │   │  + RBAC  │  │  (Redis) │  │  (Redis) │  │  (ELK)   │  │  Headers     │   │ │
│  │   └──────────┘  └──────────┘  └──────────┘  └──────────┘  └──────────────┘   │ │
│  │                                                                                 │ │
│  │   REST: /api/v1/**          GraphQL: /graphql          WebSocket: /ws/**        │ │
│  └─────────────────────────────────────────────────────────────────────────────────┘ │
└───────────────────────────────┬─────────────────────────────────────────────────────┘
                                │
┌───────────────────────────────▼─────────────────────────────────────────────────────┐
│                           LAYER 3: Business Logic Layer (Microservices)               │
│                                                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │
│  │ Auth         │  │ Dashboard    │  │ Analytics    │  │ Query        │            │
│  │ Service      │  │ Service      │  │ Engine       │  │ Engine       │            │
│  │ :3001        │  │ :3002        │  │ :3003        │  │ :3004        │            │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘            │
│                                                                                      │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐            │
│  │ Report       │  │ Alert        │  │ AI           │  │ Connector    │            │
│  │ Service      │  │ Service      │  │ Service      │  │ Service      │            │
│  │ :3005        │  │ :3006        │  │ :3007        │  │ :3008        │            │
│  └──────────────┘  └──────────────┘  └──────────────┘  └──────────────┘            │
│                                                                                      │
│  ┌──────────────┐                                                                    │
│  │ Admin        │  ← 메시지 버스: Redis Pub/Sub + Bull Queue                         │
│  │ Service      │                                                                    │
│  │ :3009        │                                                                    │
│  └──────────────┘                                                                    │
└───────────────────────────────┬─────────────────────────────────────────────────────┘
                                │
┌───────────────────────────────▼─────────────────────────────────────────────────────┐
│                           LAYER 4: Data Platform Layer                                │
│                                                                                      │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐                   │
│  │  PostgreSQL 16   │  │   ClickHouse /   │  │   Redis 7        │                   │
│  │  (OLTP)          │  │   TimescaleDB    │  │   (Cache/Queue)  │                   │
│  │                  │  │   (OLAP)         │  │                  │                   │
│  │  • 메타데이터     │  │  • Fact Tables   │  │  • 세션           │                   │
│  │  • 사용자/권한    │  │  • 집계 쿼리     │  │  • 쿼리 캐시      │                   │
│  │  • 보고서 템플릿  │  │  • 시계열 분석   │  │  • Bull Queue    │                   │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘                   │
│                                                                                      │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────┐                   │
│  │  Elasticsearch   │  │  MinIO / S3      │  │  ETL Pipeline    │                   │
│  │  (Search/Log)    │  │  (Object Storage)│  │  (Scheduler)     │                   │
│  │                  │  │                  │  │                  │                   │
│  │  • 감사 로그      │  │  • 보고서 PDF/XLS│  │  • cron 스케줄   │                   │
│  │  • 전문 검색      │  │  • 업로드 파일    │  │  • 변환 규칙     │                   │
│  │  • 로그 분석      │  │  • 차트 스냅샷   │  │  • 에러 모니터링  │                   │
│  └──────────────────┘  └──────────────────┘  └──────────────────┘                   │
└───────────────────────────────┬─────────────────────────────────────────────────────┘
                                │
┌───────────────────────────────▼─────────────────────────────────────────────────────┐
│                           LAYER 5: Source System Layer                                │
│                                                                                      │
│  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌────────┐  ┌──────┐  │
│  │  SAP   │  │  MES   │  │  SVC   │  │  CRM   │  │  IoT   │  │  PLM   │  │Excel │  │
│  │  ERP   │  │        │  │ System │  │        │  │ MQTT   │  │        │  │Upload│  │
│  │ S/4HANA│  │        │  │        │  │        │  │ OPC-UA │  │        │  │      │  │
│  └────────┘  └────────┘  └────────┘  └────────┘  └────────┘  └────────┘  └──────┘  │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

### 2. 마이크로서비스 구성 상세

#### 2.1 Auth Service (포트: 3001)

**역할**: JWT 기반 인증, RBAC 인가, LDAP/SSO 연동, 세션 관리

**기술 스택**: Node.js + Express + Passport.js + jsonwebtoken + bcrypt

**API 엔드포인트**:

| Method | Path | 설명 | Auth |
|--------|------|------|:----:|
| POST | /api/v1/auth/login | 이메일+패스워드 로그인 | ❌ |
| POST | /api/v1/auth/refresh | Access Token 재발급 | Refresh Token |
| POST | /api/v1/auth/logout | 로그아웃 (토큰 무효화) | ✅ |
| GET | /api/v1/auth/me | 현재 사용자 정보 | ✅ |
| POST | /api/v1/auth/sso/saml | SAML SSO 응답 처리 | ❌ |
| POST | /api/v1/auth/mfa/verify | OTP 2차 인증 | ✅ |
| PUT | /api/v1/auth/password | 패스워드 변경 | ✅ |
| POST | /api/v1/auth/password/reset | 패스워드 초기화 요청 | ❌ |

**의존성**: PostgreSQL (사용자 DB), Redis (세션/블랙리스트), SMTP (이메일)

**환경 변수**:
```env
JWT_ACCESS_SECRET=...
JWT_REFRESH_SECRET=...
JWT_ACCESS_EXPIRE=15m
JWT_REFRESH_EXPIRE=7d
LDAP_URL=ldap://corp.gst.co.kr
SMTP_HOST=smtp.gst.co.kr
MFA_ISSUER=GST_NAVI
```

---

#### 2.2 Dashboard Service (포트: 3002)

**역할**: 대시보드 CRUD, 위젯 관리, 레이아웃 저장, 공유 링크 생성

**기술 스택**: Node.js + Express + Sequelize (PostgreSQL ORM)

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| GET | /api/v1/dashboards | 대시보드 목록 (권한 필터) |
| POST | /api/v1/dashboards | 대시보드 생성 |
| GET | /api/v1/dashboards/:id | 대시보드 상세 (위젯 포함) |
| PUT | /api/v1/dashboards/:id | 대시보드 수정 |
| DELETE | /api/v1/dashboards/:id | 대시보드 삭제 |
| PUT | /api/v1/dashboards/:id/layout | 레이아웃 저장 |
| POST | /api/v1/dashboards/:id/share | 공유 링크 생성 |
| GET | /api/v1/dashboards/:id/widgets | 위젯 목록 |
| POST | /api/v1/dashboards/:id/widgets | 위젯 추가 |
| PUT | /api/v1/dashboards/:id/widgets/:wid | 위젯 수정 |
| DELETE | /api/v1/dashboards/:id/widgets/:wid | 위젯 삭제 |
| POST | /api/v1/dashboards/:id/duplicate | 대시보드 복제 |
| PUT | /api/v1/dashboards/:id/publish | 게시 상태 변경 |

**의존성**: PostgreSQL (meta_dashboards), Redis (뷰 캐시), Analytics Engine (데이터 조회)

---

#### 2.3 Analytics Engine (포트: 3003)

**역할**: 집계 쿼리 실행, KPI 계산, 차트 데이터 생성, 드릴다운 처리

**기술 스택**: Node.js + Express + pg (PostgreSQL driver) + ClickHouse driver

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| POST | /api/v1/analytics/kpi | KPI 값 계산 |
| POST | /api/v1/analytics/chart | 차트 데이터 생성 |
| POST | /api/v1/analytics/drilldown | 드릴다운 데이터 |
| POST | /api/v1/analytics/compare | 기간 비교 분석 |
| GET | /api/v1/analytics/trending | 트렌드 데이터 |
| POST | /api/v1/analytics/pivot | 피벗 테이블 데이터 |
| GET | /api/v1/analytics/cache/:key | 캐시 조회 |
| DELETE | /api/v1/analytics/cache/:key | 캐시 무효화 |

**의존성**: ClickHouse (OLAP), Redis (결과 캐시 TTL 5분), Query Engine

---

#### 2.4 Query Engine (포트: 3004)

**역할**: 시맨틱 레이어 기반 SQL 빌더, 쿼리 최적화, 실행 계획 분석

**기술 스택**: Node.js + Express + knex.js (쿼리 빌더) + pg + ClickHouse

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| POST | /api/v1/query/execute | SQL 쿼리 실행 (샌드박스) |
| POST | /api/v1/query/validate | SQL 문법/권한 검증 |
| POST | /api/v1/query/preview | 쿼리 미리보기 (100행) |
| GET | /api/v1/query/history | 쿼리 실행 이력 |
| GET | /api/v1/semantic/datasets | 데이터셋 목록 |
| GET | /api/v1/semantic/datasets/:id/fields | 필드 목록 |
| GET | /api/v1/semantic/measures | 측정값 목록 |
| POST | /api/v1/semantic/measures | 측정값 생성 |
| GET | /api/v1/semantic/dimensions | 차원 목록 |
| POST | /api/v1/semantic/nl-to-sql | 자연어 → SQL 변환 |

**의존성**: PostgreSQL (meta_datasets, meta_fields), ClickHouse (쿼리 실행)

---

#### 2.5 Report Service (포트: 3005)

**역할**: 보고서 템플릿 관리, PDF/Excel/PPT 생성, 이메일/슬랙 배포, 스케줄링

**기술 스택**: Node.js + Express + Puppeteer (PDF) + ExcelJS + Bull (Queue)

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| GET | /api/v1/reports | 보고서 목록 |
| POST | /api/v1/reports | 보고서 템플릿 생성 |
| GET | /api/v1/reports/:id | 보고서 상세 |
| PUT | /api/v1/reports/:id | 보고서 수정 |
| DELETE | /api/v1/reports/:id | 보고서 삭제 |
| POST | /api/v1/reports/:id/generate | 보고서 즉시 생성 |
| GET | /api/v1/reports/:id/history | 생성 이력 |
| GET | /api/v1/reports/:id/download | 파일 다운로드 |
| POST | /api/v1/reports/:id/schedule | 스케줄 설정 |
| PUT | /api/v1/reports/:id/schedule | 스케줄 수정 |
| DELETE | /api/v1/reports/:id/schedule | 스케줄 삭제 |
| POST | /api/v1/reports/:id/send | 즉시 발송 |

**의존성**: PostgreSQL (meta_reports), MinIO (파일 저장), Bull+Redis (스케줄), SMTP/Slack

---

#### 2.6 Alert Service (포트: 3006)

**역할**: KPI 임계값 모니터링, 알림 규칙 관리, 이메일/브리티웍스 발송

**기술 스택**: Node.js + Express + Bull + node-schedule

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| GET | /api/v1/alerts | 알림 규칙 목록 |
| POST | /api/v1/alerts | 알림 규칙 생성 |
| GET | /api/v1/alerts/:id | 알림 규칙 상세 |
| PUT | /api/v1/alerts/:id | 알림 규칙 수정 |
| DELETE | /api/v1/alerts/:id | 알림 규칙 삭제 |
| POST | /api/v1/alerts/:id/test | 테스트 발송 |
| GET | /api/v1/alerts/history | 발송 이력 |
| GET | /api/v1/alerts/active | 현재 활성 Alert |
| POST | /api/v1/alerts/:id/acknowledge | Alert 확인 처리 |

**Alert 규칙 예시**:
```json
{
  "name": "매출 목표 달성률 경보",
  "kpi_id": "sales_achievement_rate",
  "condition": {
    "operator": "less_than",
    "threshold": 85,
    "consecutive_periods": 2
  },
  "severity": "warning",
  "channels": ["email", "slack"],
  "recipients": ["ceo@gst.co.kr", "#slack-channel-sales"],
  "schedule": "0 9 * * 1-5",
  "cooldown_minutes": 240
}
```

---

#### 2.7 AI Service (포트: 3007)

**역할**: 자연어 쿼리(NLQ), 예측 모델 서빙, 자동 인사이트 생성

**기술 스택**: Python + FastAPI + Claude API + scikit-learn + Prophet

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| POST | /api/v1/ai/nlq | 자연어 → SQL → 결과 |
| GET | /api/v1/ai/nlq/history | NLQ 이력 |
| POST | /api/v1/ai/predict/sales | 매출 예측 |
| POST | /api/v1/ai/predict/equipment | 장비 고장 예측 |
| POST | /api/v1/ai/predict/hr | 인력 이탈 예측 |
| POST | /api/v1/ai/insight/generate | 자동 인사이트 생성 |
| GET | /api/v1/ai/insight/latest | 최신 인사이트 목록 |
| POST | /api/v1/ai/anomaly/detect | 이상 감지 |
| GET | /api/v1/ai/models | 모델 목록 및 상태 |
| POST | /api/v1/ai/models/:id/train | 모델 재학습 트리거 |

**의존성**: Claude API (Anthropic), PostgreSQL (이력), ClickHouse (학습 데이터), Redis (결과 캐시)

---

#### 2.8 Connector Service (포트: 3008)

**역할**: 외부 데이터소스 연결 설정, 테스트, ETL 파이프라인 실행

**기술 스택**: Node.js + Express + node-sap-rfc + knex (DB) + axios (API) + xlsx (Excel)

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| GET | /api/v1/connectors | 커넥터 목록 |
| POST | /api/v1/connectors | 커넥터 등록 |
| GET | /api/v1/connectors/:id | 커넥터 상세 |
| PUT | /api/v1/connectors/:id | 커넥터 수정 |
| DELETE | /api/v1/connectors/:id | 커넥터 삭제 |
| POST | /api/v1/connectors/:id/test | 연결 테스트 |
| GET | /api/v1/connectors/:id/schema | 스키마 조회 |
| POST | /api/v1/connectors/:id/preview | 데이터 미리보기 |
| POST | /api/v1/connectors/:id/sync | 즉시 동기화 |
| GET | /api/v1/connectors/:id/sync-history | 동기화 이력 |
| POST | /api/v1/pipelines | ETL 파이프라인 생성 |
| GET | /api/v1/pipelines | 파이프라인 목록 |
| PUT | /api/v1/pipelines/:id/schedule | 스케줄 설정 |

---

#### 2.9 Admin Service (포트: 3009)

**역할**: 사용자 관리, 역할/권한 관리, 시스템 설정, 감사 로그

**기술 스택**: Node.js + Express + Sequelize

**API 엔드포인트**:

| Method | Path | 설명 |
|--------|------|------|
| GET | /api/v1/admin/users | 사용자 목록 |
| POST | /api/v1/admin/users | 사용자 생성 |
| PUT | /api/v1/admin/users/:id | 사용자 수정 |
| DELETE | /api/v1/admin/users/:id | 사용자 삭제 |
| PUT | /api/v1/admin/users/:id/roles | 역할 할당 |
| GET | /api/v1/admin/roles | 역할 목록 |
| POST | /api/v1/admin/roles | 역할 생성 |
| PUT | /api/v1/admin/roles/:id/permissions | 권한 설정 |
| GET | /api/v1/admin/audit-logs | 감사 로그 (페이징) |
| GET | /api/v1/admin/system/health | 시스템 헬스체크 |
| GET | /api/v1/admin/system/metrics | 시스템 메트릭 |
| POST | /api/v1/admin/system/cache/clear | 캐시 초기화 |

---

### 3. 노코드 엔진 아키텍처

```
┌──────────────────────────────────────────────────────────────────────────────┐
│                          NOCODE ENGINE ARCHITECTURE                          │
│                                                                              │
│  ┌────────────────────────────────────────────────────────────────────────┐  │
│  │                      Nocode Studio (프론트엔드 UI)                      │  │
│  │                                                                        │  │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐  │  │
│  │  │데이터소스  │  │시맨틱    │  │차트      │  │대시보드  │  │보고서  │  │  │
│  │  │커넥터 관리│  │레이어    │  │빌더      │  │디자이너  │  │디자이너│  │  │
│  │  │마법사     │  │편집기    │  │(드래그)   │  │(그리드)  │  │(WYSIWYG│  │  │
│  │  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘  └───┬────┘  │  │
│  └───────┼──────────────┼──────────────┼──────────────┼────────────┼──────┘  │
│          │              │              │              │            │          │
│  ┌───────▼──────────────▼──────────────▼──────────────▼────────────▼──────┐  │
│  │                    Nocode Runtime Engine (백엔드)                       │  │
│  │                                                                        │  │
│  │  ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐   │  │
│  │  │ Connector Manager│   │ Semantic Resolver │   │ Chart Renderer   │   │  │
│  │  │ • 연결 풀 관리    │   │ • 용어→SQL 변환   │   │ • ECharts 옵션   │   │  │
│  │  │ • 인증 정보 암호화│   │ • 계산 필드 파싱  │   │ • 데이터 매핑    │   │  │
│  │  │ • 스키마 탐색     │   │ • 필터 조건 병합  │   │ • 드릴다운 설정  │   │  │
│  │  └──────────────────┘   └──────────────────┘   └──────────────────┘   │  │
│  │                                                                        │  │
│  │  ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐   │  │
│  │  │ KPI Formula      │   │ Dashboard Layout │   │ Report Generator │   │  │
│  │  │ Engine           │   │ Engine           │   │                  │   │  │
│  │  │ • 수식 파싱       │   │ • 그리드 레이아웃  │   │ • 섹션 렌더링    │   │  │
│  │  │ • 집계 함수 실행  │   │ • 위젯 바인딩     │   │ • Puppeteer PDF  │   │  │
│  │  │ • 달성률 계산     │   │ • 필터 전파       │   │ • 스케줄 큐 등록 │   │  │
│  │  └──────────────────┘   └──────────────────┘   └──────────────────┘   │  │
│  └────────────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────────┘
```

**노코드 설정 메타데이터 흐름**:
```
사용자 UI 조작
    │
    ▼
JSON 설정 생성 (meta_charts, meta_dashboards 등 PostgreSQL 저장)
    │
    ▼
API 요청 시 메타데이터 로드 → Semantic Resolver → SQL 생성 → 실행
    │
    ▼
결과 → Chart Renderer → ECharts 옵션 JSON → 프론트엔드 렌더링
```

---

## III. 데이터베이스 설계

### 1. 데이터베이스 전략

| DB 시스템 | 용도 | 버전 | 선택 이유 |
|-----------|------|------|---------|
| PostgreSQL | OLTP, 메타데이터, 사용자 관리 | 16 | ACID, JSON 지원, 안정성 |
| ClickHouse | OLAP, Fact 테이블, 집계 쿼리 | 24.x | 컬럼형 스토리지, 1억행+ 쿼리 성능 |
| TimescaleDB | IoT 시계열, 실시간 모니터링 | 2.x | PostgreSQL 확장, 시계열 최적화 |
| Redis | 세션, 쿼리 캐시, 메시지 큐 | 7.x | 인메모리 고속 캐시 |
| Elasticsearch | 로그 검색, 감사 로그 분석 | 8.x | 전문 검색, 로그 집계 |
| MinIO | 파일 스토리지 (PDF, Excel, 업로드) | RELEASE.2024 | S3 호환, 온프레미스 |

**데이터 분리 원칙**:
- 트랜잭션 데이터 → PostgreSQL (meta*, user*, audit*)
- 분석 데이터 → ClickHouse (fact*, dim*)
- 세션/캐시 → Redis (TTL 설정)
- 비정형 로그 → Elasticsearch
- 바이너리 파일 → MinIO

---

### 2. 스키마 설계 — Fact Tables (7종) DDL

#### fact_sales — 매출 Fact 테이블

```sql
-- ClickHouse DDL
CREATE TABLE fact_sales (
    sale_id           UUID           DEFAULT generateUUIDv4(),
    date_key          UInt32         NOT NULL COMMENT '날짜 키 (YYYYMMDD)',
    customer_key      UInt32         NOT NULL COMMENT '고객 차원 키',
    product_key       UInt32         NOT NULL COMMENT '제품 차원 키',
    channel_key       UInt32         NOT NULL COMMENT '채널 차원 키',
    sales_team_key    UInt32         NOT NULL COMMENT '영업팀 차원 키',
    location_key      UInt32         NOT NULL COMMENT '법인/지역 차원 키',
    
    -- 수량/금액 측정값
    order_qty         Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '수주 수량',
    ship_qty          Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '출하 수량',
    invoice_qty       Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '청구 수량',
    
    revenue_krw       Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '매출액 (원)',
    revenue_usd       Decimal(18,2)  NOT NULL DEFAULT 0 COMMENT '매출액 (USD)',
    cogs_krw          Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '매출원가 (원)',
    gross_profit_krw  Decimal(18,0)  GENERATED ALWAYS AS (revenue_krw - cogs_krw) VIRTUAL,
    sga_krw           Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '판관비 배부액 (원)',
    op_profit_krw     Decimal(18,0)  GENERATED ALWAYS AS (revenue_krw - cogs_krw - sga_krw) VIRTUAL,
    
    -- 목표 대비
    target_revenue    Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '매출 목표',
    target_qty        Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '수량 목표',
    
    -- 메타
    source_system     LowCardinality(String) DEFAULT 'SAP' COMMENT '소스 시스템',
    document_no       String         COMMENT 'SAP 전표번호',
    etl_batch_id      UUID           COMMENT 'ETL 배치 ID',
    loaded_at         DateTime       DEFAULT now() COMMENT '적재 일시',
    
    INDEX idx_date_key    (date_key)    TYPE minmax GRANULARITY 4,
    INDEX idx_customer    (customer_key) TYPE set(100) GRANULARITY 4,
    INDEX idx_product     (product_key)  TYPE set(100) GRANULARITY 4
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, customer_key, product_key, channel_key)
TTL toDate(toString(date_key)) + INTERVAL 5 YEAR
SETTINGS index_granularity = 8192;

-- PostgreSQL 파티션 테이블 (메타/트랜잭션용 보조)
CREATE TABLE fact_sales_meta (
    sale_id           UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    date_key          INTEGER        NOT NULL,
    customer_key      INTEGER        NOT NULL,
    product_key       INTEGER        NOT NULL,
    revenue_krw       NUMERIC(18,0)  NOT NULL DEFAULT 0,
    cogs_krw          NUMERIC(18,0)  NOT NULL DEFAULT 0,
    sga_krw           NUMERIC(18,0)  NOT NULL DEFAULT 0,
    target_revenue    NUMERIC(18,0)  NOT NULL DEFAULT 0,
    loaded_at         TIMESTAMPTZ    NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (date_key);

CREATE TABLE fact_sales_2026 PARTITION OF fact_sales_meta
    FOR VALUES FROM (20260101) TO (20270101);
CREATE TABLE fact_sales_2025 PARTITION OF fact_sales_meta
    FOR VALUES FROM (20250101) TO (20260101);

CREATE INDEX idx_fs_date ON fact_sales_meta (date_key);
CREATE INDEX idx_fs_customer ON fact_sales_meta (customer_key);
CREATE INDEX idx_fs_product ON fact_sales_meta (product_key);
```

#### fact_production — 생산 Fact 테이블

```sql
CREATE TABLE fact_production (
    production_id     UUID           DEFAULT generateUUIDv4(),
    date_key          UInt32         NOT NULL COMMENT '날짜 키',
    product_key       UInt32         NOT NULL COMMENT '제품 차원 키',
    process_key       UInt32         NOT NULL COMMENT '공정 차원 키',
    equipment_key     UInt32         NOT NULL COMMENT '장비 차원 키',
    location_key      UInt32         NOT NULL COMMENT '공장/라인 키',
    department_key    UInt32         NOT NULL COMMENT '부서 차원 키',
    
    -- 생산 측정값
    plan_qty          Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '계획 수량',
    actual_qty        Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '실적 수량',
    good_qty          Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '양품 수량',
    defect_qty        Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '불량 수량',
    
    -- 시간 측정값 (분 단위)
    plan_hours        Decimal(10,2)  NOT NULL DEFAULT 0 COMMENT '계획 가동시간',
    actual_hours      Decimal(10,2)  NOT NULL DEFAULT 0 COMMENT '실적 가동시간',
    downtime_hours    Decimal(10,2)  NOT NULL DEFAULT 0 COMMENT '비가동시간',
    
    -- OEE 구성요소
    availability      Decimal(5,2)   COMMENT '가동률 (%)',
    performance       Decimal(5,2)   COMMENT '성능률 (%)',
    quality_rate      Decimal(5,2)   COMMENT '품질률 (%)',
    oee               Decimal(5,2)   COMMENT 'OEE (%)',
    
    -- 원가
    labor_cost        Decimal(15,0)  NOT NULL DEFAULT 0 COMMENT '노무비',
    material_cost     Decimal(15,0)  NOT NULL DEFAULT 0 COMMENT '재료비',
    overhead_cost     Decimal(15,0)  NOT NULL DEFAULT 0 COMMENT '제조간접비',
    
    source_system     LowCardinality(String) DEFAULT 'MES',
    work_order_no     String         COMMENT '작업지시 번호',
    lot_no            String         COMMENT '로트 번호',
    shift             LowCardinality(String) COMMENT '근무조 (주/야/철)',
    loaded_at         DateTime       DEFAULT now()
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, product_key, process_key, equipment_key)
SETTINGS index_granularity = 8192;
```

#### fact_quality — 품질 Fact 테이블

```sql
CREATE TABLE fact_quality (
    quality_id        UUID           DEFAULT generateUUIDv4(),
    date_key          UInt32         NOT NULL,
    product_key       UInt32         NOT NULL,
    process_key       UInt32         NOT NULL,
    customer_key      UInt32         COMMENT '현장 불량 시 고객 키',
    location_key      UInt32         NOT NULL,
    
    -- 품질 측정값
    inspection_qty    Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '검사 수량',
    pass_qty          Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '합격 수량',
    reject_qty        Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '불합격 수량',
    rework_qty        Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '재작업 수량',
    scrap_qty         Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '폐기 수량',
    
    -- 현장/필드 불량
    field_fail_count  UInt32         NOT NULL DEFAULT 0 COMMENT '현장고장 건수',
    warranty_cost     Decimal(15,0)  NOT NULL DEFAULT 0 COMMENT '보증 비용',
    
    -- 개선 활동
    improvement_count UInt32         NOT NULL DEFAULT 0 COMMENT '개선 건수',
    improvement_done  UInt32         NOT NULL DEFAULT 0 COMMENT '개선 완료 건수',
    
    defect_type       LowCardinality(String) COMMENT '불량 유형',
    defect_cause      String         COMMENT '불량 원인',
    source_system     LowCardinality(String) DEFAULT 'QMS',
    loaded_at         DateTime       DEFAULT now()
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, product_key, process_key);
```

#### fact_service — 서비스 Fact 테이블

```sql
CREATE TABLE fact_service (
    service_id        UUID           DEFAULT generateUUIDv4(),
    date_key          UInt32         NOT NULL,
    customer_key      UInt32         NOT NULL,
    equipment_key     UInt32         NOT NULL,
    department_key    UInt32         NOT NULL,
    location_key      UInt32         NOT NULL,
    
    -- 서비스 건수
    pm_count          UInt32         NOT NULL DEFAULT 0 COMMENT '예방보전 건수',
    cm_count          UInt32         NOT NULL DEFAULT 0 COMMENT '사후보전 건수',
    em_count          UInt32         NOT NULL DEFAULT 0 COMMENT '긴급보전 건수',
    predictive_count  UInt32         NOT NULL DEFAULT 0 COMMENT '예지보전 건수',
    
    -- 시간 측정값 (시간 단위)
    mttr_hours        Decimal(8,2)   NOT NULL DEFAULT 0 COMMENT '평균수리시간',
    mtbf_hours        Decimal(10,2)  NOT NULL DEFAULT 0 COMMENT '평균고장간격',
    response_hours    Decimal(8,2)   NOT NULL DEFAULT 0 COMMENT '응답시간',
    
    -- 가동률
    available_hours   Decimal(10,2)  NOT NULL DEFAULT 0 COMMENT '가능시간',
    operating_hours   Decimal(10,2)  NOT NULL DEFAULT 0 COMMENT '가동시간',
    uptime_rate       Decimal(5,2)   COMMENT '장비가동률 (%)',
    
    -- 인원/비용
    service_engineer_cnt UInt32      NOT NULL DEFAULT 0 COMMENT '서비스 인원',
    service_cost      Decimal(15,0)  NOT NULL DEFAULT 0 COMMENT '서비스 비용',
    parts_cost        Decimal(15,0)  NOT NULL DEFAULT 0 COMMENT '부품 비용',
    
    total_equipment_cnt UInt32       NOT NULL DEFAULT 0 COMMENT '총 관리 장비수',
    source_system     LowCardinality(String) DEFAULT 'SVC',
    work_order_no     String,
    loaded_at         DateTime       DEFAULT now()
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, customer_key, equipment_key);
```

#### fact_procurement — 구매 Fact 테이블

```sql
CREATE TABLE fact_procurement (
    procurement_id    UUID           DEFAULT generateUUIDv4(),
    date_key          UInt32         NOT NULL,
    vendor_key        UInt32         NOT NULL COMMENT '협력사 차원 키',
    product_key       UInt32         NOT NULL,
    location_key      UInt32         NOT NULL,
    department_key    UInt32         NOT NULL,
    
    -- 구매 측정값
    po_count          UInt32         NOT NULL DEFAULT 0 COMMENT '발주 건수',
    po_qty            Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '발주 수량',
    receipt_qty       Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '입고 수량',
    po_amount_krw     Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '발주 금액',
    receipt_amount    Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '입고 금액',
    
    -- 납기/품질
    otd_count         UInt32         NOT NULL DEFAULT 0 COMMENT '납기준수 건수',
    total_po_count    UInt32         NOT NULL DEFAULT 0 COMMENT '전체 발주 건수',
    incoming_defect   Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '수입검사 불량 수량',
    
    -- 재고
    inventory_qty     Decimal(15,3)  NOT NULL DEFAULT 0 COMMENT '재고 수량',
    inventory_value   Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '재고 금액',
    
    lead_time_days    Decimal(8,2)   NOT NULL DEFAULT 0 COMMENT '리드타임 (일)',
    source_system     LowCardinality(String) DEFAULT 'SAP',
    po_no             String         COMMENT '구매주문 번호',
    loaded_at         DateTime       DEFAULT now()
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, vendor_key, product_key);
```

#### fact_hr — 인사 Fact 테이블

```sql
CREATE TABLE fact_hr (
    hr_id             UUID           DEFAULT generateUUIDv4(),
    date_key          UInt32         NOT NULL COMMENT '기준 월 (YYYYMM01)',
    department_key    UInt32         NOT NULL,
    location_key      UInt32         NOT NULL,
    
    -- 인원 현황
    headcount_start   UInt32         NOT NULL DEFAULT 0 COMMENT '기초 인원',
    headcount_end     UInt32         NOT NULL DEFAULT 0 COMMENT '기말 인원',
    hire_count        UInt32         NOT NULL DEFAULT 0 COMMENT '입사자',
    resign_count      UInt32         NOT NULL DEFAULT 0 COMMENT '퇴사자',
    
    -- 인건비
    salary_total      Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '급여 총액',
    bonus_total       Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '상여 총액',
    benefit_total     Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '복리후생비',
    labor_cost_total  Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '총 인건비',
    
    -- 생산성
    value_added       Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '부가가치',
    overtime_hours    Decimal(10,2)  NOT NULL DEFAULT 0 COMMENT '초과근무 시간',
    
    -- 근태
    attendance_days   Decimal(8,2)   NOT NULL DEFAULT 0 COMMENT '출근 일수',
    absence_days      Decimal(8,2)   NOT NULL DEFAULT 0 COMMENT '결근 일수',
    
    job_grade         LowCardinality(String) COMMENT '직급',
    employment_type   LowCardinality(String) COMMENT '고용형태 (정규/계약/파견)',
    source_system     LowCardinality(String) DEFAULT 'HR',
    loaded_at         DateTime       DEFAULT now()
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, department_key, location_key);
```

#### fact_finance — 재무 집계 Fact 테이블

```sql
CREATE TABLE fact_finance (
    finance_id        UUID           DEFAULT generateUUIDv4(),
    date_key          UInt32         NOT NULL COMMENT '기준 월 (YYYYMM01)',
    location_key      UInt32         NOT NULL COMMENT '법인 키',
    department_key    UInt32         NOT NULL COMMENT '부서 키',
    account_code      String         NOT NULL COMMENT '계정과목 코드',
    account_name      String         COMMENT '계정과목명',
    account_category  LowCardinality(String) COMMENT '계정 구분 (P&L/BS/CF)',
    
    -- 금액 측정값
    plan_amount       Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '예산',
    actual_amount     Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '실적',
    last_year_amount  Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '전년 동기',
    
    -- 파생 측정값 (집계 시 계산)
    plan_ytd          Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '예산 누계',
    actual_ytd        Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT '실적 누계',
    
    currency_code     LowCardinality(String) DEFAULT 'KRW' COMMENT '통화',
    fx_rate           Decimal(12,6)  NOT NULL DEFAULT 1 COMMENT '환율',
    amount_krw        Decimal(18,0)  NOT NULL DEFAULT 0 COMMENT 'KRW 환산액',
    
    profit_center     String         COMMENT '손익센터',
    cost_center       String         COMMENT '코스트센터',
    source_system     LowCardinality(String) DEFAULT 'SAP',
    document_no       String,
    posting_date      Date,
    loaded_at         DateTime       DEFAULT now()
)
ENGINE = MergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, location_key, account_code, department_key);

-- 집계 테이블 (월별 요약)
CREATE MATERIALIZED VIEW mv_finance_monthly
ENGINE = SummingMergeTree()
PARTITION BY toYYYYMM(toDate(toString(date_key)))
ORDER BY (date_key, location_key, account_category)
AS SELECT
    date_key,
    location_key,
    account_category,
    sum(plan_amount)    AS plan_amount,
    sum(actual_amount)  AS actual_amount,
    sum(last_year_amount) AS last_year_amount
FROM fact_finance
GROUP BY date_key, location_key, account_category;
```

---

### 2. 스키마 설계 — Dimension Tables (10종) DDL

#### dim_date — 날짜 차원

```sql
-- PostgreSQL (ClickHouse에 복제)
CREATE TABLE dim_date (
    date_key          INTEGER        PRIMARY KEY,            -- YYYYMMDD
    full_date         DATE           NOT NULL,
    year              SMALLINT       NOT NULL,
    quarter           SMALLINT       NOT NULL CHECK (quarter BETWEEN 1 AND 4),
    month             SMALLINT       NOT NULL CHECK (month BETWEEN 1 AND 12),
    month_name        VARCHAR(10)    NOT NULL,               -- '1월', '2월'...
    week_of_year      SMALLINT       NOT NULL,
    day_of_month      SMALLINT       NOT NULL,
    day_of_week       SMALLINT       NOT NULL CHECK (day_of_week BETWEEN 1 AND 7),
    day_name          VARCHAR(10)    NOT NULL,               -- '월', '화'...
    is_weekend        BOOLEAN        NOT NULL DEFAULT FALSE,
    is_holiday        BOOLEAN        NOT NULL DEFAULT FALSE,
    holiday_name      VARCHAR(50),
    is_business_day   BOOLEAN        NOT NULL DEFAULT TRUE,
    fiscal_year       SMALLINT       NOT NULL,               -- 회계연도
    fiscal_quarter    SMALLINT       NOT NULL,
    fiscal_month      SMALLINT       NOT NULL,
    ytd_flag          BOOLEAN        GENERATED ALWAYS AS (
                          month <= EXTRACT(MONTH FROM CURRENT_DATE)
                          AND year = EXTRACT(YEAR FROM CURRENT_DATE)
                      ) STORED,
    quarter_label     VARCHAR(10)    GENERATED ALWAYS AS (
                          year::TEXT || 'Q' || quarter::TEXT
                      ) STORED,
    month_label       VARCHAR(10)    GENERATED ALWAYS AS (
                          year::TEXT || '-' || LPAD(month::TEXT, 2, '0')
                      ) STORED
);

COMMENT ON TABLE dim_date IS '날짜 차원 테이블 (2020-01-01 ~ 2030-12-31 사전 생성)';

-- 날짜 차원 자동 생성 프로시저
CREATE OR REPLACE FUNCTION populate_dim_date(
    start_date DATE DEFAULT '2020-01-01',
    end_date   DATE DEFAULT '2030-12-31'
) RETURNS VOID AS $$
DECLARE
    d DATE := start_date;
BEGIN
    WHILE d <= end_date LOOP
        INSERT INTO dim_date (date_key, full_date, year, quarter, month, month_name,
            week_of_year, day_of_month, day_of_week, day_name, is_weekend,
            fiscal_year, fiscal_quarter, fiscal_month)
        VALUES (
            TO_CHAR(d, 'YYYYMMDD')::INTEGER,
            d,
            EXTRACT(YEAR FROM d)::SMALLINT,
            EXTRACT(QUARTER FROM d)::SMALLINT,
            EXTRACT(MONTH FROM d)::SMALLINT,
            TO_CHAR(d, 'MM') || '월',
            EXTRACT(WEEK FROM d)::SMALLINT,
            EXTRACT(DAY FROM d)::SMALLINT,
            EXTRACT(DOW FROM d)::SMALLINT + 1,
            CASE EXTRACT(DOW FROM d)
                WHEN 0 THEN '일' WHEN 1 THEN '월' WHEN 2 THEN '화'
                WHEN 3 THEN '수' WHEN 4 THEN '목' WHEN 5 THEN '금'
                ELSE '토' END,
            EXTRACT(DOW FROM d) IN (0, 6),
            CASE WHEN EXTRACT(MONTH FROM d) >= 1 THEN EXTRACT(YEAR FROM d)::SMALLINT
                 ELSE (EXTRACT(YEAR FROM d) - 1)::SMALLINT END,
            CASE WHEN EXTRACT(MONTH FROM d) BETWEEN 1 AND 3 THEN 1
                 WHEN EXTRACT(MONTH FROM d) BETWEEN 4 AND 6 THEN 2
                 WHEN EXTRACT(MONTH FROM d) BETWEEN 7 AND 9 THEN 3
                 ELSE 4 END,
            EXTRACT(MONTH FROM d)::SMALLINT
        )
        ON CONFLICT (date_key) DO NOTHING;
        d := d + INTERVAL '1 day';
    END LOOP;
END;
$$ LANGUAGE plpgsql;
```

#### dim_customer — 고객 차원

```sql
CREATE TABLE dim_customer (
    customer_key      SERIAL         PRIMARY KEY,
    customer_id       VARCHAR(20)    NOT NULL UNIQUE,        -- SAP 고객코드
    customer_name     VARCHAR(200)   NOT NULL,
    customer_name_en  VARCHAR(200),
    customer_type     VARCHAR(50)    NOT NULL DEFAULT 'B2B', -- B2B/B2C/내부
    industry          VARCHAR(100),                          -- 산업군
    country_code      CHAR(2)        NOT NULL DEFAULT 'KR',
    country_name      VARCHAR(100),
    region            VARCHAR(100),                          -- 지역/권역
    city              VARCHAR(100),
    address           TEXT,
    
    -- 세분화
    customer_grade    VARCHAR(20),                           -- VIP/Gold/Silver/Bronze
    customer_segment  VARCHAR(50),                           -- 세그먼트
    sales_rep_id      VARCHAR(20),                           -- 담당 영업사원 ID
    sales_team_key    INTEGER        REFERENCES dim_sales_team(sales_team_key),
    
    -- 재무
    credit_limit      NUMERIC(18,0)  DEFAULT 0,
    payment_terms     VARCHAR(50),                           -- 결제조건 (NET30 등)
    currency_code     CHAR(3)        DEFAULT 'KRW',
    
    -- 메타
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    sap_debitor_no    VARCHAR(20)    UNIQUE,                 -- SAP 채무자 번호
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    valid_from        DATE           NOT NULL DEFAULT '2000-01-01',
    valid_to          DATE           NOT NULL DEFAULT '9999-12-31',
    is_current        BOOLEAN        NOT NULL DEFAULT TRUE    -- SCD Type 2
);

CREATE INDEX idx_dim_cust_name ON dim_customer (customer_name);
CREATE INDEX idx_dim_cust_type ON dim_customer (customer_type);
CREATE INDEX idx_dim_cust_grade ON dim_customer (customer_grade);
CREATE INDEX idx_dim_cust_country ON dim_customer (country_code);
```

#### dim_product — 제품 차원

```sql
CREATE TABLE dim_product (
    product_key       SERIAL         PRIMARY KEY,
    product_id        VARCHAR(50)    NOT NULL UNIQUE,        -- SAP 자재번호
    product_name      VARCHAR(300)   NOT NULL,
    product_name_en   VARCHAR(300),
    product_code      VARCHAR(50),                           -- 내부 제품 코드
    
    -- 분류 계층
    category_l1       VARCHAR(100)   NOT NULL,               -- 대분류 (제품군)
    category_l2       VARCHAR(100),                          -- 중분류
    category_l3       VARCHAR(100),                          -- 소분류
    product_type      VARCHAR(50),                           -- 완제품/반제품/원재료
    
    -- 기술 사양
    unit_of_measure   VARCHAR(10)    NOT NULL DEFAULT 'EA',  -- 단위
    weight_kg         NUMERIC(10,3),
    standard_price    NUMERIC(15,2),                         -- 표준 단가
    
    -- 연결
    brand             VARCHAR(100),
    model_no          VARCHAR(100),
    series            VARCHAR(100),
    
    -- 메타
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    launch_date       DATE,
    eol_date          DATE,                                  -- End of Life 날짜
    sap_material_no   VARCHAR(20)    UNIQUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    valid_from        DATE           NOT NULL DEFAULT '2000-01-01',
    valid_to          DATE           NOT NULL DEFAULT '9999-12-31',
    is_current        BOOLEAN        NOT NULL DEFAULT TRUE
);

CREATE INDEX idx_dim_prod_cat1 ON dim_product (category_l1);
CREATE INDEX idx_dim_prod_cat2 ON dim_product (category_l2);
CREATE INDEX idx_dim_prod_type ON dim_product (product_type);
CREATE INDEX idx_dim_prod_name ON dim_product USING gin (to_tsvector('simple', product_name));
```

#### dim_channel — 판매채널 차원

```sql
CREATE TABLE dim_channel (
    channel_key       SERIAL         PRIMARY KEY,
    channel_id        VARCHAR(20)    NOT NULL UNIQUE,
    channel_name      VARCHAR(100)   NOT NULL,
    channel_type      VARCHAR(50)    NOT NULL,               -- 직판/대리점/온라인/해외
    channel_group     VARCHAR(50),                           -- 채널 그룹
    region            VARCHAR(100),
    country_code      CHAR(2)        NOT NULL DEFAULT 'KR',
    is_domestic       BOOLEAN        NOT NULL DEFAULT TRUE,
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### dim_sales_team — 영업팀 차원

```sql
CREATE TABLE dim_sales_team (
    sales_team_key    SERIAL         PRIMARY KEY,
    team_id           VARCHAR(20)    NOT NULL UNIQUE,
    team_name         VARCHAR(100)   NOT NULL,
    team_type         VARCHAR(50),                           -- 국내/해외/채널
    division          VARCHAR(100),                          -- 사업부
    department        VARCHAR(100),                          -- 부서
    manager_emp_id    VARCHAR(20),                           -- 팀장 사원번호
    manager_name      VARCHAR(100),
    headcount         INTEGER        NOT NULL DEFAULT 0,
    target_revenue    NUMERIC(18,0)  DEFAULT 0,              -- 연간 목표
    region            VARCHAR(100),
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### dim_department — 부서 차원

```sql
CREATE TABLE dim_department (
    dept_key          SERIAL         PRIMARY KEY,
    dept_id           VARCHAR(20)    NOT NULL UNIQUE,        -- 부서 코드
    dept_name         VARCHAR(100)   NOT NULL,
    dept_type         VARCHAR(50),                           -- 영업/생산/관리/연구
    parent_dept_id    VARCHAR(20)    REFERENCES dim_department(dept_id),
    dept_level        SMALLINT       NOT NULL DEFAULT 1,     -- 조직 계층 레벨
    dept_path         TEXT,                                  -- /법인/사업부/부서
    
    cost_center       VARCHAR(20),                           -- SAP 코스트센터
    profit_center     VARCHAR(20),                           -- SAP 손익센터
    location_key      INTEGER,
    manager_emp_id    VARCHAR(20),
    manager_name      VARCHAR(100),
    headcount         INTEGER        NOT NULL DEFAULT 0,
    
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_dim_dept_parent ON dim_department (parent_dept_id);
CREATE INDEX idx_dim_dept_type ON dim_department (dept_type);
```

#### dim_vendor — 협력사 차원

```sql
CREATE TABLE dim_vendor (
    vendor_key        SERIAL         PRIMARY KEY,
    vendor_id         VARCHAR(20)    NOT NULL UNIQUE,
    vendor_name       VARCHAR(200)   NOT NULL,
    vendor_name_en    VARCHAR(200),
    vendor_type       VARCHAR(50),                           -- 원자재/부품/서비스
    business_reg_no   VARCHAR(20),                           -- 사업자등록번호
    country_code      CHAR(2)        NOT NULL DEFAULT 'KR',
    region            VARCHAR(100),
    
    -- 협력사 등급
    vendor_grade      VARCHAR(20),                           -- A/B/C
    is_certified      BOOLEAN        NOT NULL DEFAULT FALSE, -- 인증 협력사 여부
    certification_date DATE,
    
    -- 거래 조건
    payment_terms     VARCHAR(50),
    currency_code     CHAR(3)        DEFAULT 'KRW',
    lead_time_days    INTEGER,
    
    sap_vendor_no     VARCHAR(20)    UNIQUE,
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### dim_equipment — 장비 차원

```sql
CREATE TABLE dim_equipment (
    equipment_key     SERIAL         PRIMARY KEY,
    equipment_id      VARCHAR(50)    NOT NULL UNIQUE,        -- 장비 ID
    equipment_name    VARCHAR(200)   NOT NULL,
    equipment_type    VARCHAR(100),                          -- 설비 유형
    equipment_class   VARCHAR(50),                           -- 생산설비/검사장비/유틸리티
    
    -- 위치
    location_key      INTEGER        NOT NULL,
    department_key    INTEGER,
    line_id           VARCHAR(50),                           -- 라인 ID
    line_name         VARCHAR(100),
    
    -- 사양
    manufacturer      VARCHAR(200),
    model_no          VARCHAR(100),
    serial_no         VARCHAR(100)   UNIQUE,
    install_date      DATE,
    purchase_date     DATE,
    purchase_cost     NUMERIC(15,0),
    useful_life_years SMALLINT,
    
    -- 보전
    pm_cycle_days     INTEGER,                               -- PM 주기 (일)
    last_pm_date      DATE,
    next_pm_date      DATE,
    
    -- IoT
    has_iot_sensor    BOOLEAN        NOT NULL DEFAULT FALSE,
    iot_device_id     VARCHAR(100),
    iot_mqtt_topic    VARCHAR(200),
    
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    sap_equipment_no  VARCHAR(20)    UNIQUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_dim_equip_type ON dim_equipment (equipment_type);
CREATE INDEX idx_dim_equip_loc ON dim_equipment (location_key);
CREATE INDEX idx_dim_equip_dept ON dim_equipment (department_key);
```

#### dim_process — 공정 차원

```sql
CREATE TABLE dim_process (
    process_key       SERIAL         PRIMARY KEY,
    process_id        VARCHAR(50)    NOT NULL UNIQUE,
    process_name      VARCHAR(200)   NOT NULL,
    process_type      VARCHAR(100),                          -- 가공/조립/검사/포장
    
    parent_process_id VARCHAR(50)    REFERENCES dim_process(process_id),
    process_level     SMALLINT       NOT NULL DEFAULT 1,
    process_order     INTEGER,                               -- 공정 순서
    
    location_key      INTEGER        NOT NULL,
    department_key    INTEGER,
    standard_time     NUMERIC(10,4),                        -- 표준 공수 (시간/단위)
    
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### dim_location — 법인/지역 차원

```sql
CREATE TABLE dim_location (
    location_key      SERIAL         PRIMARY KEY,
    location_id       VARCHAR(20)    NOT NULL UNIQUE,
    location_name     VARCHAR(200)   NOT NULL,
    location_type     VARCHAR(50)    NOT NULL,               -- 본사/공장/지사/해외법인
    
    country_code      CHAR(2)        NOT NULL DEFAULT 'KR',
    country_name      VARCHAR(100),
    region            VARCHAR(100),
    city              VARCHAR(100),
    address           TEXT,
    timezone          VARCHAR(50)    NOT NULL DEFAULT 'Asia/Seoul',
    
    parent_location_id VARCHAR(20)   REFERENCES dim_location(location_id),
    
    is_domestic       BOOLEAN        NOT NULL DEFAULT TRUE,
    sap_company_code  VARCHAR(4),                            -- SAP 회사코드
    sap_plant         VARCHAR(4),                            -- SAP 플랜트
    currency_code     CHAR(3)        DEFAULT 'KRW',
    
    latitude          NUMERIC(9,6),
    longitude         NUMERIC(9,6),
    
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

---

### 2. 스키마 설계 — 메타데이터 Tables (16종) DDL

#### meta_datasources — 데이터소스 연결 정보

```sql
CREATE TABLE meta_datasources (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    name              VARCHAR(200)   NOT NULL,
    description       TEXT,
    connector_type    VARCHAR(50)    NOT NULL,   -- SAP_RFC/SAP_ODATA/POSTGRESQL/MYSQL/REST_API/EXCEL/KAFKA/MQTT
    
    -- 연결 설정 (AES-256 암호화된 JSON)
    connection_config JSONB          NOT NULL DEFAULT '{}',
    -- 예시:
    -- { "host": "192.168.1.10", "port": 5432, "database": "erp", 
    --   "username": "reader", "password": "enc:AES256:...", "ssl": true }
    
    -- 인증
    auth_type         VARCHAR(50)    DEFAULT 'basic',        -- basic/oauth2/api_key/saml
    auth_config       JSONB          DEFAULT '{}',
    
    -- 동기화 설정
    sync_mode         VARCHAR(20)    DEFAULT 'scheduled',    -- realtime/scheduled/manual
    sync_schedule     VARCHAR(100),                          -- cron 표현식
    sync_timezone     VARCHAR(50)    DEFAULT 'Asia/Seoul',
    
    -- 상태
    status            VARCHAR(20)    NOT NULL DEFAULT 'inactive', -- active/inactive/error
    last_sync_at      TIMESTAMPTZ,
    last_sync_status  VARCHAR(20),
    last_error        TEXT,
    
    -- 메타
    owner_user_id     UUID           NOT NULL,
    is_shared         BOOLEAN        NOT NULL DEFAULT FALSE,
    tags              TEXT[]         DEFAULT '{}',
    
    created_by        UUID           NOT NULL,
    updated_by        UUID,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    
    CONSTRAINT chk_connector_type CHECK (connector_type IN (
        'SAP_RFC','SAP_ODATA','SAP_BW','POSTGRESQL','MYSQL','ORACLE','MSSQL',
        'MONGODB','REST_API','GRAPHQL','SOAP','EXCEL','CSV','JSON',
        'SALESFORCE','GOOGLE_SHEETS','KAFKA','RABBITMQ','MQTT','OPC_UA','WEBSOCKET'
    ))
);

CREATE INDEX idx_mds_type ON meta_datasources (connector_type);
CREATE INDEX idx_mds_status ON meta_datasources (status);
CREATE INDEX idx_mds_owner ON meta_datasources (owner_user_id);
```

#### meta_datasets — 데이터셋 정의

```sql
CREATE TABLE meta_datasets (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    datasource_id     UUID           NOT NULL REFERENCES meta_datasources(id),
    name              VARCHAR(200)   NOT NULL,
    description       TEXT,
    
    -- 쿼리 정의
    query_type        VARCHAR(20)    NOT NULL DEFAULT 'table', -- table/sql/api
    source_table      VARCHAR(500),                           -- 테이블명 또는 API 경로
    custom_sql        TEXT,                                   -- 커스텀 SQL (query_type='sql'인 경우)
    
    -- 캐시 설정
    cache_enabled     BOOLEAN        NOT NULL DEFAULT TRUE,
    cache_ttl_seconds INTEGER        NOT NULL DEFAULT 300,
    
    -- 필터 기본값
    default_filters   JSONB          DEFAULT '[]',
    max_rows          INTEGER        NOT NULL DEFAULT 10000,
    
    -- 접근 제어
    visibility        VARCHAR(20)    NOT NULL DEFAULT 'private', -- private/team/public
    allowed_roles     TEXT[]         DEFAULT '{}',
    
    -- 메타
    record_count      BIGINT,
    last_refreshed_at TIMESTAMPTZ,
    refresh_schedule  VARCHAR(100),
    
    created_by        UUID           NOT NULL,
    updated_by        UUID,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_mdsets_ds ON meta_datasets (datasource_id);
CREATE INDEX idx_mdsets_creator ON meta_datasets (created_by);
```

#### meta_fields — 필드 매핑

```sql
CREATE TABLE meta_fields (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    dataset_id        UUID           NOT NULL REFERENCES meta_datasets(id) ON DELETE CASCADE,
    
    -- 원본 필드
    source_field      VARCHAR(200)   NOT NULL,               -- DB 컬럼명
    source_type       VARCHAR(50),                           -- DB 원본 타입
    
    -- 비즈니스 레이어
    display_name      VARCHAR(200)   NOT NULL,               -- UI 표시명 (예: '매출액')
    description       TEXT,
    field_type        VARCHAR(20)    NOT NULL,               -- dimension/measure/date/filter
    data_type         VARCHAR(20)    NOT NULL,               -- string/number/date/boolean
    
    -- 포맷
    format_type       VARCHAR(50),                           -- currency/percent/number/date
    format_pattern    VARCHAR(100),                          -- '#,##0' / 'YYYY-MM-DD'
    unit              VARCHAR(20),                           -- '원', '%', 'EA', 'kg'
    
    -- 집계
    default_aggregation VARCHAR(20) DEFAULT 'sum',           -- sum/avg/count/min/max
    
    -- 계산 필드
    is_calculated     BOOLEAN        NOT NULL DEFAULT FALSE,
    formula           TEXT,                                  -- 계산 수식 (Excel-like)
    
    -- 정렬/필터
    sort_order        INTEGER        NOT NULL DEFAULT 0,
    is_visible        BOOLEAN        NOT NULL DEFAULT TRUE,
    is_filterable     BOOLEAN        NOT NULL DEFAULT TRUE,
    is_sortable       BOOLEAN        NOT NULL DEFAULT TRUE,
    
    -- 드릴다운
    drill_target_dataset_id UUID    REFERENCES meta_datasets(id),
    drill_filter_field VARCHAR(200),
    
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    
    UNIQUE (dataset_id, source_field)
);

CREATE INDEX idx_mfields_dataset ON meta_fields (dataset_id);
CREATE INDEX idx_mfields_type ON meta_fields (field_type);
```

#### meta_measures — 측정값 정의 + 계산식

```sql
CREATE TABLE meta_measures (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    dataset_id        UUID           REFERENCES meta_datasets(id),
    
    name              VARCHAR(200)   NOT NULL,
    display_name      VARCHAR(200)   NOT NULL,
    description       TEXT,
    
    -- 계산식 (시맨틱 레이어)
    formula           TEXT           NOT NULL,
    -- 예: "SUM(revenue_krw) / SUM(invoice_qty)"
    -- 예: "CASE WHEN SUM(target_revenue) = 0 THEN 0 ELSE SUM(revenue_krw) / SUM(target_revenue) * 100 END"
    
    formula_type      VARCHAR(20)    NOT NULL DEFAULT 'sql', -- sql/excel/simple
    
    -- 포맷
    format_type       VARCHAR(50)    NOT NULL DEFAULT 'number',
    format_pattern    VARCHAR(100)   DEFAULT '#,##0',
    unit              VARCHAR(20),
    decimal_places    SMALLINT       NOT NULL DEFAULT 0,
    
    -- 목표/임계값
    target_value      NUMERIC,
    warning_threshold NUMERIC,
    critical_threshold NUMERIC,
    threshold_direction VARCHAR(10)  DEFAULT 'lower',       -- lower/higher (낮을수록 경보)
    
    -- KPI 템플릿 연결
    kpi_template_id   UUID           REFERENCES meta_kpi_templates(id),
    
    -- 메타
    category          VARCHAR(50),                           -- 재무/영업/생산/서비스/품질/HR
    is_system         BOOLEAN        NOT NULL DEFAULT FALSE, -- 시스템 기본 KPI
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    
    created_by        UUID           NOT NULL,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_mmeasures_dataset ON meta_measures (dataset_id);
CREATE INDEX idx_mmeasures_category ON meta_measures (category);
```

#### meta_charts — 차트 설정 JSON

```sql
CREATE TABLE meta_charts (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    name              VARCHAR(200)   NOT NULL,
    description       TEXT,
    chart_type        VARCHAR(50)    NOT NULL,
    -- bar/line/area/pie/donut/scatter/bubble/heatmap/treemap/funnel/
    -- gauge/radar/sankey/waterfall/combo
    
    -- 데이터 바인딩
    dataset_id        UUID           REFERENCES meta_datasets(id),
    dimensions        JSONB          NOT NULL DEFAULT '[]',
    -- [{"field_id": "uuid", "label": "월", "sort": "asc"}]
    measures          JSONB          NOT NULL DEFAULT '[]',
    -- [{"measure_id": "uuid", "label": "매출액", "axis": "left", "color": "#5470c6"}]
    filters           JSONB          DEFAULT '[]',
    
    -- 차트 옵션 (ECharts 설정 오버라이드)
    options           JSONB          DEFAULT '{}',
    -- { "title": {"text": "월별 매출 추이"}, "legend": {"show": true}, "tooltip": {...} }
    
    -- 인터랙션
    drill_enabled     BOOLEAN        NOT NULL DEFAULT FALSE,
    drill_target_id   UUID           REFERENCES meta_charts(id),
    cross_filter      BOOLEAN        NOT NULL DEFAULT FALSE,
    
    -- 표시 설정
    color_palette     VARCHAR(50)    DEFAULT 'default',
    show_label        BOOLEAN        NOT NULL DEFAULT FALSE,
    show_legend       BOOLEAN        NOT NULL DEFAULT TRUE,
    enable_zoom       BOOLEAN        NOT NULL DEFAULT FALSE,
    
    -- 정렬/제한
    sort_by           VARCHAR(200),
    sort_direction    VARCHAR(4)     DEFAULT 'desc',
    top_n             INTEGER,
    
    -- 메타
    visibility        VARCHAR(20)    NOT NULL DEFAULT 'private',
    tags              TEXT[]         DEFAULT '{}',
    
    created_by        UUID           NOT NULL,
    updated_by        UUID,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_mcharts_type ON meta_charts (chart_type);
CREATE INDEX idx_mcharts_dataset ON meta_charts (dataset_id);
CREATE INDEX idx_mcharts_creator ON meta_charts (created_by);
```

#### meta_dashboards — 대시보드 레이아웃

```sql
CREATE TABLE meta_dashboards (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    name              VARCHAR(200)   NOT NULL,
    description       TEXT,
    
    -- 레이아웃
    layout_type       VARCHAR(20)    NOT NULL DEFAULT 'grid', -- grid/freeform
    grid_cols         INTEGER        NOT NULL DEFAULT 24,
    theme             VARCHAR(20)    NOT NULL DEFAULT 'light', -- light/dark/custom
    background_color  VARCHAR(20),
    
    -- 글로벌 필터
    global_filters    JSONB          DEFAULT '[]',
    -- [{"field": "date_key", "type": "daterange", "default": "this_month"}]
    global_variables  JSONB          DEFAULT '{}',
    -- {"location_id": "KR01"}
    
    -- 자동 새로고침
    auto_refresh      BOOLEAN        NOT NULL DEFAULT FALSE,
    refresh_interval  INTEGER        DEFAULT 300,             -- 초
    
    -- 공유/접근
    visibility        VARCHAR(20)    NOT NULL DEFAULT 'private',
    allowed_roles     TEXT[]         DEFAULT '{}',
    share_token       VARCHAR(100)   UNIQUE,                  -- 공유 링크 토큰
    
    -- 게시
    is_published      BOOLEAN        NOT NULL DEFAULT FALSE,
    published_at      TIMESTAMPTZ,
    
    -- 분류
    category          VARCHAR(100),
    tags              TEXT[]         DEFAULT '{}',
    is_template       BOOLEAN        NOT NULL DEFAULT FALSE,  -- 템플릿 여부
    
    -- 메타
    view_count        BIGINT         NOT NULL DEFAULT 0,
    favorite_count    INTEGER        NOT NULL DEFAULT 0,
    
    created_by        UUID           NOT NULL,
    updated_by        UUID,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_mdash_creator ON meta_dashboards (created_by);
CREATE INDEX idx_mdash_category ON meta_dashboards (category);
CREATE INDEX idx_mdash_published ON meta_dashboards (is_published);
```

#### meta_dashboard_widgets — 위젯 배치

```sql
CREATE TABLE meta_dashboard_widgets (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    dashboard_id      UUID           NOT NULL REFERENCES meta_dashboards(id) ON DELETE CASCADE,
    chart_id          UUID           REFERENCES meta_charts(id),
    
    -- 위젯 유형
    widget_type       VARCHAR(50)    NOT NULL,
    -- chart/kpi_card/text/image/filter/table/iframe
    
    -- 그리드 레이아웃 (react-grid-layout 호환)
    grid_x            INTEGER        NOT NULL DEFAULT 0,
    grid_y            INTEGER        NOT NULL DEFAULT 0,
    grid_w            INTEGER        NOT NULL DEFAULT 6,
    grid_h            INTEGER        NOT NULL DEFAULT 4,
    min_w             INTEGER        DEFAULT 2,
    min_h             INTEGER        DEFAULT 2,
    
    -- 위젯 설정
    title             VARCHAR(200),
    show_title        BOOLEAN        NOT NULL DEFAULT TRUE,
    config            JSONB          DEFAULT '{}',
    -- kpi_card: { "measure_id": "uuid", "comparison": "mom/yoy", "icon": "trending-up" }
    -- text: { "content": "markdown text", "font_size": 14 }
    -- filter: { "field_id": "uuid", "type": "select/daterange/slider" }
    
    -- 필터 연동
    filter_scope      TEXT[]         DEFAULT '{}',           -- 연동할 위젯 ID 목록
    
    -- 데이터 오버라이드 (차트 설정 재정의)
    data_overrides    JSONB          DEFAULT '{}',
    
    sort_order        INTEGER        NOT NULL DEFAULT 0,
    is_visible        BOOLEAN        NOT NULL DEFAULT TRUE,
    
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_mdw_dashboard ON meta_dashboard_widgets (dashboard_id);
CREATE INDEX idx_mdw_chart ON meta_dashboard_widgets (chart_id);
```

#### meta_reports — 보고서 템플릿

```sql
CREATE TABLE meta_reports (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    name              VARCHAR(200)   NOT NULL,
    description       TEXT,
    report_type       VARCHAR(30)    NOT NULL DEFAULT 'periodic',
    -- periodic/ad_hoc/event_driven
    
    -- 출력 형식
    output_formats    TEXT[]         NOT NULL DEFAULT '{pdf}',
    -- {pdf, excel, pptx, html}
    paper_size        VARCHAR(10)    DEFAULT 'A4',
    orientation       VARCHAR(10)    DEFAULT 'portrait',
    
    -- 변수
    variables         JSONB          DEFAULT '[]',
    -- [{"name": "year_month", "type": "date", "default": "current_month"}]
    
    -- 배포
    distribution      JSONB          DEFAULT '{}',
    -- {"channels": ["email","slack"], "recipients": [...], "subject": "월간 보고서 {{year_month}}"}
    
    -- 스케줄
    schedule_enabled  BOOLEAN        NOT NULL DEFAULT FALSE,
    schedule_cron     VARCHAR(100),
    schedule_timezone VARCHAR(50)    DEFAULT 'Asia/Seoul',
    next_run_at       TIMESTAMPTZ,
    last_run_at       TIMESTAMPTZ,
    last_run_status   VARCHAR(20),
    
    -- 메타
    visibility        VARCHAR(20)    NOT NULL DEFAULT 'private',
    allowed_roles     TEXT[]         DEFAULT '{}',
    category          VARCHAR(100),
    tags              TEXT[]         DEFAULT '{}',
    
    created_by        UUID           NOT NULL,
    updated_by        UUID,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### meta_report_sections — 보고서 섹션

```sql
CREATE TABLE meta_report_sections (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    report_id         UUID           NOT NULL REFERENCES meta_reports(id) ON DELETE CASCADE,
    
    section_type      VARCHAR(30)    NOT NULL,
    -- header/footer/text/chart/table/kpi_card/page_break/image
    
    title             VARCHAR(200),
    content           TEXT,          -- 텍스트/HTML (Markdown 지원)
    chart_id          UUID           REFERENCES meta_charts(id),
    dataset_id        UUID           REFERENCES meta_datasets(id),
    
    -- 레이아웃
    sort_order        INTEGER        NOT NULL DEFAULT 0,
    width_percent     INTEGER        NOT NULL DEFAULT 100,    -- 열 너비 (%)
    column_group      INTEGER        DEFAULT 1,              -- 같은 행의 컬럼 그룹
    
    -- 스타일
    style_config      JSONB          DEFAULT '{}',
    -- {"fontSize": 12, "fontWeight": "bold", "textAlign": "center", "color": "#333"}
    
    -- 조건부 표시
    show_condition    JSONB,                                  -- 조건 충족 시만 표시
    
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_mrs_report ON meta_report_sections (report_id, sort_order);
```

#### meta_alerts — 알림 규칙

```sql
CREATE TABLE meta_alerts (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    name              VARCHAR(200)   NOT NULL,
    description       TEXT,
    
    -- 모니터링 대상
    measure_id        UUID           REFERENCES meta_measures(id),
    dataset_id        UUID           REFERENCES meta_datasets(id),
    custom_query      TEXT,          -- 직접 쿼리
    
    -- 조건
    condition_operator VARCHAR(20)   NOT NULL,
    -- gt/gte/lt/lte/eq/neq/between/outside_range/percent_change_gt
    condition_value   NUMERIC        NOT NULL,
    condition_value2  NUMERIC,       -- between 연산자의 상한값
    consecutive_periods INTEGER      NOT NULL DEFAULT 1,
    
    -- 심각도
    severity          VARCHAR(10)    NOT NULL DEFAULT 'warning',
    -- info/warning/critical
    
    -- 알림 채널
    channels          TEXT[]         NOT NULL DEFAULT '{email}',
    recipients        TEXT[]         NOT NULL DEFAULT '{}',
    
    -- 메시지 템플릿
    message_template  TEXT,
    -- "{{measure_name}}이 {{value}}로 임계값 {{threshold}}를 {{direction}} 이탈했습니다."
    
    -- 스케줄
    check_schedule    VARCHAR(100)   NOT NULL DEFAULT '0 * * * *',
    check_timezone    VARCHAR(50)    DEFAULT 'Asia/Seoul',
    
    -- 억제
    cooldown_minutes  INTEGER        NOT NULL DEFAULT 60,
    suppress_until    TIMESTAMPTZ,
    
    -- 상태
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    last_check_at     TIMESTAMPTZ,
    last_triggered_at TIMESTAMPTZ,
    last_status       VARCHAR(20),   -- ok/warning/critical/error
    
    created_by        UUID           NOT NULL,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### meta_kpi_templates — KPI 계산 템플릿

```sql
CREATE TABLE meta_kpi_templates (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    name              VARCHAR(200)   NOT NULL,
    display_name      VARCHAR(200)   NOT NULL,
    description       TEXT,
    
    -- 분류
    category          VARCHAR(50)    NOT NULL,
    -- finance/sales/production/service/quality/hr
    subcategory       VARCHAR(100),
    
    -- 수식
    formula_template  TEXT           NOT NULL,
    -- "SUM({revenue}) / SUM({invoice_qty})"
    -- 플레이스홀더: {field_name} 형식
    
    -- 파라미터 (사용자가 필드를 매핑)
    parameters        JSONB          NOT NULL DEFAULT '[]',
    -- [{"name": "revenue", "label": "매출액 필드", "type": "measure", "required": true}]
    
    -- 포맷
    format_type       VARCHAR(30)    NOT NULL DEFAULT 'number',
    unit              VARCHAR(20),
    decimal_places    SMALLINT       NOT NULL DEFAULT 1,
    
    -- 목표/임계값 가이드
    target_direction  VARCHAR(10)    DEFAULT 'higher',
    typical_range_low NUMERIC,
    typical_range_high NUMERIC,
    
    -- 메타
    is_system         BOOLEAN        NOT NULL DEFAULT TRUE,
    is_active         BOOLEAN        NOT NULL DEFAULT TRUE,
    usage_count       INTEGER        NOT NULL DEFAULT 0,
    
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### meta_filters — 필터 설정

```sql
CREATE TABLE meta_filters (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    name              VARCHAR(200)   NOT NULL,
    filter_type       VARCHAR(30)    NOT NULL,
    -- select/multiselect/daterange/numberrange/search/radio/checkbox
    
    -- 데이터 소스
    dataset_id        UUID           REFERENCES meta_datasets(id),
    field_id          UUID           REFERENCES meta_fields(id),
    
    -- 값 목록 (정적)
    static_options    JSONB          DEFAULT '[]',
    -- [{"label": "전체", "value": ""}, {"label": "국내", "value": "KR"}]
    
    -- 값 목록 (동적)
    dynamic_sql       TEXT,          -- SELECT DISTINCT value, label FROM ...
    
    -- 기본값
    default_value     JSONB,
    
    -- UI 설정
    placeholder       VARCHAR(200),
    allow_all         BOOLEAN        NOT NULL DEFAULT TRUE,
    cascade_parent_id UUID           REFERENCES meta_filters(id),
    
    -- 전파 범위
    scope             VARCHAR(20)    DEFAULT 'dashboard',
    -- dashboard/report/widget
    
    created_by        UUID           NOT NULL,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);
```

#### user_analyses — 사용자 저장 분석

```sql
CREATE TABLE user_analyses (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id           UUID           NOT NULL,
    name              VARCHAR(200)   NOT NULL,
    description       TEXT,
    
    analysis_type     VARCHAR(30)    NOT NULL DEFAULT 'chart',
    -- chart/nlq/pivot/custom
    
    -- 분석 상태 저장 (차트 설정 스냅샷)
    state             JSONB          NOT NULL DEFAULT '{}',
    
    -- NLQ인 경우
    nlq_question      TEXT,
    nlq_generated_sql TEXT,
    
    -- 공유
    is_shared         BOOLEAN        NOT NULL DEFAULT FALSE,
    share_token       VARCHAR(100)   UNIQUE,
    
    tags              TEXT[]         DEFAULT '{}',
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    updated_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
);

CREATE INDEX idx_ua_user ON user_analyses (user_id);
```

#### user_favorites — 즐겨찾기

```sql
CREATE TABLE user_favorites (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id           UUID           NOT NULL,
    resource_type     VARCHAR(30)    NOT NULL,
    -- dashboard/chart/report/analysis
    resource_id       UUID           NOT NULL,
    sort_order        INTEGER        NOT NULL DEFAULT 0,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW(),
    UNIQUE (user_id, resource_type, resource_id)
);

CREATE INDEX idx_uf_user ON user_favorites (user_id, resource_type);
```

#### audit_logs — 감사 로그

```sql
CREATE TABLE audit_logs (
    id                UUID           PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id           UUID,
    username          VARCHAR(100),
    
    -- 액션
    action            VARCHAR(50)    NOT NULL,
    -- LOGIN/LOGOUT/CREATE/READ/UPDATE/DELETE/EXPORT/SHARE/ADMIN
    resource_type     VARCHAR(50)    NOT NULL,
    resource_id       VARCHAR(100),
    resource_name     VARCHAR(200),
    
    -- 요청 정보
    ip_address        INET,
    user_agent        TEXT,
    request_path      VARCHAR(500),
    request_method    VARCHAR(10),
    
    -- 변경 내용 (개인정보 마스킹 처리)
    before_value      JSONB,
    after_value       JSONB,
    
    -- 결과
    status            VARCHAR(10)    NOT NULL DEFAULT 'success', -- success/failure
    error_message     TEXT,
    duration_ms       INTEGER,
    
    -- 파티셔닝용
    log_date          DATE           NOT NULL DEFAULT CURRENT_DATE,
    created_at        TIMESTAMPTZ    NOT NULL DEFAULT NOW()
) PARTITION BY RANGE (log_date);

CREATE TABLE audit_logs_2026_q1 PARTITION OF audit_logs
    FOR VALUES FROM ('2026-01-01') TO ('2026-04-01');
CREATE TABLE audit_logs_2026_q2 PARTITION OF audit_logs
    FOR VALUES FROM ('2026-04-01') TO ('2026-07-01');

CREATE INDEX idx_al_user ON audit_logs (user_id, log_date);
CREATE INDEX idx_al_action ON audit_logs (action, log_date);
CREATE INDEX idx_al_resource ON audit_logs (resource_type, resource_id);
```

---

### 3. ERD (ASCII)

#### Fact-Dimension Star Schema

```
                    ┌───────────────┐
                    │   dim_date    │
                    │───────────────│
                    │ date_key (PK) │
                    │ full_date     │
                    │ year          │
                    │ month         │
                    │ quarter       │
                    └───────┬───────┘
                            │ date_key
         ┌──────────────────┼──────────────────┐
         │                  │                  │
┌────────┴──────┐  ┌────────┴──────┐  ┌────────┴──────┐
│  dim_customer │  │  dim_product  │  │  dim_location  │
│───────────────│  │───────────────│  │────────────────│
│customer_key(PK│  │product_key(PK)│  │location_key(PK)│
│customer_id    │  │product_id     │  │location_id     │
│customer_name  │  │product_name   │  │location_name   │
│customer_type  │  │category_l1    │  │location_type   │
│customer_grade │  │category_l2    │  │country_code    │
└───────┬───────┘  └───────┬───────┘  └───────┬────────┘
        │ customer_key      │ product_key       │ location_key
        └──────────┬────────┘         ┌─────────┘
                   │                  │
          ┌────────▼──────────────────▼──────┐
          │           fact_sales             │
          │──────────────────────────────────│
          │ sale_id (PK)                     │
          │ date_key ──────────────────────▶ dim_date
          │ customer_key ──────────────────▶ dim_customer
          │ product_key ───────────────────▶ dim_product
          │ channel_key ───────────────────▶ dim_channel
          │ sales_team_key ────────────────▶ dim_sales_team
          │ location_key ──────────────────▶ dim_location
          │ revenue_krw                      │
          │ cogs_krw                         │
          │ target_revenue                   │
          └──────────────────────────────────┘

          ┌──────────────────────────────────┐
          │        fact_production           │
          │──────────────────────────────────│
          │ date_key ──────────────────────▶ dim_date
          │ product_key ───────────────────▶ dim_product
          │ process_key ───────────────────▶ dim_process
          │ equipment_key ─────────────────▶ dim_equipment
          │ location_key ──────────────────▶ dim_location
          │ department_key ────────────────▶ dim_department
          │ plan_qty / actual_qty / oee      │
          └──────────────────────────────────┘
```

#### 메타데이터 ERD

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ meta_datasources│─1:N─│  meta_datasets  │─1:N─│   meta_fields   │
│─────────────────│     │─────────────────│     │─────────────────│
│ id (PK)         │     │ id (PK)         │     │ id (PK)         │
│ connector_type  │     │ datasource_id(FK│     │ dataset_id (FK) │
│ connection_cfg  │     │ query_type      │     │ source_field    │
│ auth_config     │     │ custom_sql      │     │ display_name    │
│ sync_schedule   │     │ cache_ttl       │     │ field_type      │
└─────────────────┘     └───────┬─────────┘     │ formula         │
                                │               └─────────────────┘
                       ┌────────┴────────┐
                       │                 │
              ┌────────▼───┐    ┌────────▼───┐
              │meta_measures│    │meta_charts │
              │─────────────│    │────────────│
              │ dataset_id  │    │ dataset_id │
              │ formula     │    │ chart_type │
              │ format_type │    │ dimensions │
              │ kpi_tmpl_id │    │ measures   │
              └─────────────┘    └─────┬──────┘
                                       │ chart_id
                         ┌─────────────▼────────────┐
                         │  meta_dashboard_widgets   │
                         │──────────────────────────│
                         │ dashboard_id (FK)         │
                         │ chart_id (FK)             │
                         │ grid_x, grid_y, grid_w,h  │
                         └──────────────┬────────────┘
                                        │ dashboard_id
                         ┌──────────────▼────────────┐
                         │     meta_dashboards        │
                         │────────────────────────────│
                         │ id (PK)                    │
                         │ name                       │
                         │ layout_type                │
                         │ global_filters             │
                         │ is_published               │
                         └────────────────────────────┘
```

---

### 4. 파티셔닝 전략

| 테이블 | 파티션 기준 | 파티션 단위 | 보존 정책 |
|--------|-----------|-----------|---------|
| fact_sales | date_key (YYYYMM) | 월별 | 3년 온라인 / 5년 아카이브 |
| fact_production | date_key (YYYYMM) | 월별 | 3년 온라인 / 5년 아카이브 |
| fact_quality | date_key (YYYYMM) | 월별 | 3년 온라인 / 5년 아카이브 |
| fact_service | date_key (YYYYMM) | 월별 | 3년 온라인 / 5년 아카이브 |
| fact_procurement | date_key (YYYYMM) | 월별 | 3년 온라인 / 5년 아카이브 |
| fact_hr | date_key (YYYYMM) | 월별 | 5년 온라인 |
| fact_finance | date_key (YYYYMM) | 월별 | 7년 온라인 (법적 의무) |
| audit_logs | log_date | 분기별 | 2년 온라인 / 5년 아카이브 |

**ClickHouse TTL 설정 예시**:
```sql
ALTER TABLE fact_sales MODIFY TTL
    toDate(toString(date_key)) + INTERVAL 3 YEAR,
    toDate(toString(date_key)) + INTERVAL 3 YEAR TO VOLUME 'archive';
```

---

### 5. 인덱스 전략

```sql
-- fact_sales 핵심 인덱스 (ClickHouse)
-- Primary key (ORDER BY) 가 인덱스 역할: (date_key, customer_key, product_key, channel_key)
-- Skipping index:
ALTER TABLE fact_sales ADD INDEX idx_revenue (revenue_krw) TYPE minmax GRANULARITY 4;
ALTER TABLE fact_sales ADD INDEX idx_source (source_system) TYPE set(5) GRANULARITY 4;

-- dim_customer 인덱스 (PostgreSQL)
CREATE INDEX CONCURRENTLY idx_cust_name_trgm ON dim_customer
    USING gin (customer_name gin_trgm_ops);     -- 부분 문자열 검색
CREATE INDEX CONCURRENTLY idx_cust_grade ON dim_customer (customer_grade)
    WHERE is_active = TRUE;                     -- 부분 인덱스

-- meta_dashboards 복합 인덱스
CREATE INDEX CONCURRENTLY idx_dash_vis_pub ON meta_dashboards (visibility, is_published)
    WHERE is_published = TRUE;                  -- 게시된 대시보드만

-- audit_logs 복합 인덱스
CREATE INDEX CONCURRENTLY idx_al_user_date ON audit_logs (user_id, log_date DESC);
CREATE INDEX CONCURRENTLY idx_al_action_date ON audit_logs (action, log_date DESC);
```

---

## IV. 데이터 인터페이스

### 1. 커넥터 프레임워크

```
Connector Framework
├── ERP
│   ├── SAP ECC (RFC/BAPI)
│   │   ├── 설정: AS Host, System Number, Client, Language, RFC User/Password
│   │   ├── 인증: SAP Logon (Basic Auth over RFC)
│   │   ├── BAPI 예시: BAPI_SALESORDER_GETLIST, BAPI_MATERIAL_GET_ALL
│   │   └── 추출: RFC_READ_TABLE, Z_CUSTOM_FUNCTION
│   ├── SAP S/4HANA (OData v4)
│   │   ├── 설정: Base URL, Client ID, Client Secret
│   │   ├── 인증: OAuth2 Client Credentials
│   │   └── 서비스: API_SALES_ORDER_SRV, API_PRODUCT_SRV
│   └── SAP BW (MDX)
│       ├── 설정: BW Server, InfoProvider, Query Name
│       └── 추출: MDX SELECT ... FROM [INFOPROVIDER]
│
├── Database
│   ├── PostgreSQL / MySQL / Oracle / MSSQL
│   │   ├── 설정: Host, Port, Database, Schema, Username, Password, SSL
│   │   ├── 인증: Basic (Username/Password)
│   │   ├── 연결 풀: Min 2, Max 10, Idle Timeout 10분
│   │   └── 추출: SELECT 쿼리 또는 테이블 직접 선택
│   └── MongoDB
│       ├── 설정: Connection String, Database, Collection
│       └── 추출: Aggregation Pipeline → JSON → 컬럼 매핑
│
├── API
│   ├── REST API (JSON/XML)
│   │   ├── 설정: Base URL, Method, Headers, Auth Type
│   │   ├── 인증: None/Basic/Bearer Token/OAuth2/API Key
│   │   ├── 페이지네이션: offset/cursor/page 지원
│   │   └── 응답 매핑: JSON Path → 필드 매핑 UI
│   ├── GraphQL
│   │   ├── 설정: Endpoint URL, Query, Variables
│   │   └── 인증: Bearer Token
│   └── SOAP/WSDL
│       ├── 설정: WSDL URL, Operation, Namespace
│       └── 인증: WS-Security
│
├── File
│   ├── Excel (.xlsx)
│   │   ├── 설정: 시트명, 헤더 행, 데이터 시작 행
│   │   └── 타입 감지: 자동 감지 + 사용자 수정
│   └── CSV / JSON
│       ├── 설정: 구분자, 인코딩, 날짜 형식
│       └── 스트리밍 파싱 (대용량 지원)
│
├── IoT / Streaming
│   ├── MQTT
│   │   ├── 설정: Broker URL, Port, Topic, QoS, ClientID
│   │   ├── 인증: Username/Password 또는 Client Certificate
│   │   └── 메시지 파싱: JSON/CSV/Binary → 필드 매핑
│   ├── OPC-UA
│   │   ├── 설정: Endpoint URL, Security Policy, NodeId
│   │   └── 수집 주기: 최소 1초
│   └── Kafka
│       ├── 설정: Bootstrap Servers, Topic, Consumer Group, Offset
│       ├── 인증: SASL/PLAIN, SASL/SSL
│       └── 역직렬화: JSON/Avro/Protobuf
```

#### 커넥터 설정 파라미터 상세 (SAP S/4HANA 예시)

```json
{
  "connector_type": "SAP_ODATA",
  "config": {
    "base_url": "https://s4hana.gst.co.kr:8080",
    "service_path": "/sap/opu/odata/sap/API_SALES_ORDER_SRV",
    "auth": {
      "type": "oauth2_client_credentials",
      "token_url": "https://s4hana.gst.co.kr/oauth/token",
      "client_id": "GST_NAVI_BI",
      "client_secret": "enc:AES256:BASE64_ENCRYPTED_SECRET",
      "scope": "API_SALES_ORDER_SRV"
    },
    "options": {
      "timeout_ms": 30000,
      "max_retries": 3,
      "retry_delay_ms": 5000,
      "page_size": 1000,
      "ssl_verify": true,
      "proxy": null
    }
  },
  "field_mapping": [
    {"source": "SalesOrder", "target": "document_no", "type": "string"},
    {"source": "CreationDate", "target": "date_key", "type": "date", "format": "YYYYMMDD"},
    {"source": "NetAmount", "target": "revenue_krw", "type": "number", "multiplier": 1000}
  ]
}
```

---

### 2. ETL 파이프라인

#### 파이프라인 구성 요소

```
ETL Pipeline Architecture
┌─────────────────────────────────────────────────────────────────┐
│                         Scheduler (Bull Queue + Redis)          │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │  Job: { type: "etl", pipeline_id: "uuid", triggered_by: "cron" }  │
│  └──────────────────────────────────────────────────────────┘   │
└────────────────────────┬────────────────────────────────────────┘
                         │
          ┌──────────────▼──────────────┐
          │         EXTRACT              │
          │  Connector → Raw Data       │
          │  • 증분 추출 (last_sync_at   │
          │    이후 변경 데이터만)        │
          │  • 전체 추출 (월별 초기화)    │
          │  • 스트리밍 (실시간)          │
          └──────────────┬──────────────┘
                         │ Raw JSON/CSV/DB Rows
          ┌──────────────▼──────────────┐
          │         TRANSFORM            │
          │  UI 설정 기반 변환 규칙       │
          │  • 타입 변환 (string→date)   │
          │  • 값 매핑 (코드→레이블)      │
          │  • 계산 필드 추가            │
          │  • 필터링 (조건 제외)        │
          │  • 중복 제거 (dedup)         │
          │  • 데이터 검증              │
          └──────────────┬──────────────┘
                         │ Validated & Transformed Rows
          ┌──────────────▼──────────────┐
          │          LOAD                │
          │  • Upsert to fact/dim tables│
          │  • Conflict: ON CONFLICT UPDATE
          │  • 배치 크기: 5,000행       │
          │  • 오류 행: error_log 적재  │
          │  • 완료 후 캐시 무효화       │
          └──────────────┬──────────────┘
                         │
          ┌──────────────▼──────────────┐
          │         MONITOR              │
          │  • 실행 이력 저장 (PostgreSQL│
          │  • 처리 건수 / 오류 건수     │
          │  • 소요 시간                 │
          │  • 오류 시 Alert 발송        │
          └─────────────────────────────┘
```

#### ETL 파이프라인 설정 예시 (UI 저장 JSON)

```json
{
  "pipeline_id": "550e8400-e29b-41d4-a716-446655440000",
  "name": "SAP 월별 매출 데이터 ETL",
  "datasource_id": "uuid-sap-connector",
  "schedule": "0 2 * * *",
  "timezone": "Asia/Seoul",
  "extract": {
    "type": "incremental",
    "watermark_field": "PostingDate",
    "watermark_format": "YYYYMMDD",
    "query": "SELECT * FROM V_SALES_DAILY WHERE PostingDate >= '{last_sync}'"
  },
  "transform": {
    "rules": [
      {"type": "rename", "from": "PostingDate", "to": "date_key"},
      {"type": "convert_type", "field": "date_key", "to": "integer", "format": "YYYYMMDD"},
      {"type": "lookup", "field": "CustomerCode", "target_field": "customer_key",
       "lookup_table": "dim_customer", "lookup_key": "customer_id"},
      {"type": "calculate", "field": "gross_profit",
       "formula": "NetAmount - COGS"},
      {"type": "filter", "condition": "NetAmount > 0"},
      {"type": "validate", "field": "date_key",
       "rule": "not_null", "on_error": "skip"}
    ]
  },
  "load": {
    "target_table": "fact_sales",
    "mode": "upsert",
    "conflict_key": ["date_key", "document_no"],
    "batch_size": 5000
  }
}
```

---

### 3. 실시간 데이터 스트리밍

#### WebSocket 아키텍처

```
클라이언트 (대시보드)
  │
  │ WebSocket connect: ws://api.gst-navi.co.kr/ws
  │ 인증: JWT in query param (?token=...)
  │
  ▼
WebSocket Server (Node.js + ws + Socket.IO)
  │
  ├── 채널 구독: { "subscribe": "dashboard:uuid:live" }
  ├── 대시보드 실시간 갱신: 5~60초 주기 (설정에 따라)
  ├── Alert 푸시: { "type": "alert", "severity": "critical", "message": "..." }
  └── IoT 라이브 데이터: 5초 간격
```

#### 실시간 데이터 업데이트 흐름

```
IoT Sensor → MQTT Broker → MQTT Subscriber (Node.js)
                                    │
                           TimescaleDB 저장 (5초 집계)
                                    │
                           Redis Pub/Sub: channel "iot:equipment:{id}"
                                    │
                      WebSocket Server (subscribe 중인 클라이언트에 push)
                                    │
                      클라이언트 ECharts 차트 실시간 업데이트
```

---

### 4. API 설계 (REST + GraphQL)

#### REST API 완전 명세

##### Auth API

```
POST /api/v1/auth/login
Request:
  Content-Type: application/json
  {
    "email": "user@gst.co.kr",
    "password": "P@ssw0rd!",
    "mfa_code": "123456"    // optional
  }
Response 200:
  {
    "access_token": "eyJhbGciOiJSUzI1NiIs...",
    "refresh_token": "550e8400-e29b-41d4-...",
    "expires_in": 900,
    "user": {
      "id": "uuid",
      "email": "user@gst.co.kr",
      "name": "홍길동",
      "roles": ["analyst", "dashboard_viewer"],
      "permissions": ["dashboard:read", "chart:write", ...]
    }
  }
Response 401: { "error": "invalid_credentials" }
Response 423: { "error": "account_locked", "locked_until": "2026-04-01T10:00:00Z" }

---

POST /api/v1/auth/refresh
Request:
  Authorization: Bearer {refresh_token}
Response 200:
  { "access_token": "eyJ...", "expires_in": 900 }

---

GET /api/v1/auth/me
Request:
  Authorization: Bearer {access_token}
Response 200:
  {
    "id": "uuid",
    "email": "user@gst.co.kr",
    "name": "홍길동",
    "avatar_url": "https://...",
    "department": "전략기획팀",
    "roles": [...],
    "permissions": [...],
    "preferences": { "theme": "light", "language": "ko", "timezone": "Asia/Seoul" }
  }
```

##### Dashboard API

```
GET /api/v1/dashboards
Query Parameters:
  ?page=1&limit=20&category=sales&visibility=shared&search=매출
Headers:
  Authorization: Bearer {token}
Response 200:
  {
    "data": [
      {
        "id": "uuid",
        "name": "월별 매출 현황",
        "description": "...",
        "category": "sales",
        "thumbnail_url": "...",
        "is_published": true,
        "view_count": 142,
        "created_by": {"id": "uuid", "name": "홍길동"},
        "created_at": "2026-03-01T00:00:00Z",
        "updated_at": "2026-03-25T14:30:00Z"
      }
    ],
    "total": 48,
    "page": 1,
    "limit": 20
  }

---

POST /api/v1/dashboards
Request Body:
  {
    "name": "신규 대시보드",
    "description": "...",
    "layout_type": "grid",
    "grid_cols": 24,
    "theme": "light",
    "category": "sales",
    "global_filters": [
      {
        "field": "date_key",
        "type": "daterange",
        "default": {"start": "2026-01-01", "end": "2026-12-31"}
      }
    ]
  }
Response 201: { "id": "uuid", ...dashboard_object }

---

POST /api/v1/dashboards/:id/widgets
Request Body:
  {
    "widget_type": "chart",
    "chart_id": "uuid",
    "title": "월별 매출액",
    "grid_x": 0, "grid_y": 0, "grid_w": 12, "grid_h": 6,
    "config": {}
  }
Response 201: { "id": "uuid", ...widget_object }
```

##### Query/Analytics API

```
POST /api/v1/query/execute
Request Body:
  {
    "sql": "SELECT date_key, SUM(revenue_krw) AS revenue FROM fact_sales WHERE date_key >= 20260101 GROUP BY date_key ORDER BY date_key",
    "datasource_id": "uuid",
    "timeout_ms": 30000,
    "max_rows": 10000
  }
Response 200:
  {
    "columns": [
      {"name": "date_key", "type": "integer"},
      {"name": "revenue", "type": "number"}
    ],
    "rows": [
      [20260101, 1250000000],
      [20260102, 980000000]
    ],
    "total_rows": 92,
    "execution_ms": 145,
    "from_cache": false,
    "query_id": "uuid"
  }

---

POST /api/v1/analytics/kpi
Request Body:
  {
    "measure_id": "uuid-sales-achievement",
    "filters": {
      "date_key": {"gte": 20260101, "lte": 20261231},
      "location_key": [1, 2, 3]
    },
    "compare_period": "yoy"
  }
Response 200:
  {
    "measure_name": "매출 달성률",
    "current_value": 94.7,
    "previous_value": 91.2,
    "change_rate": 3.84,
    "change_direction": "up",
    "unit": "%",
    "format": "#,##0.0",
    "status": "warning",
    "target": 100.0,
    "as_of": "2026-03-31"
  }
```

##### AI / NLQ API

```
POST /api/v1/ai/nlq
Request Body:
  {
    "question": "올해 1분기 매출이 가장 높은 상위 5개 고객을 보여줘",
    "context": {
      "dataset_id": "uuid-sales-dataset",
      "current_filters": {"location_key": 1}
    }
  }
Response 200:
  {
    "question": "올해 1분기 매출이 가장 높은 상위 5개 고객을 보여줘",
    "interpreted_as": "2026년 1~3월 기준 고객별 매출액 상위 5개",
    "generated_sql": "SELECT c.customer_name, SUM(s.revenue_krw) AS revenue FROM fact_sales s JOIN dim_customer c ON s.customer_key = c.customer_key WHERE s.date_key BETWEEN 20260101 AND 20260331 GROUP BY c.customer_name ORDER BY revenue DESC LIMIT 5",
    "results": {
      "columns": ["customer_name", "revenue"],
      "rows": [["삼성전자", 8500000000], ...]
    },
    "suggested_chart": {
      "type": "bar",
      "x_field": "customer_name",
      "y_field": "revenue"
    },
    "insight": "삼성전자가 1분기 매출의 34.2%를 차지하며 1위를 유지했습니다. 전년 동기 대비 12.3% 성장했습니다.",
    "confidence": 0.94,
    "execution_ms": 850
  }
```

---

## V. 노코드 기능 상세

### 1. 데이터소스 커넥터 관리 UI

#### 커넥터 등록 워크플로우 (5단계 마법사)

```
Step 1: 커넥터 유형 선택
  ┌─────────────────────────────────────────────────────┐
  │  어떤 데이터소스를 연결하시겠습니까?                    │
  │                                                     │
  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────┐ │
  │  │  SAP ERP  │ │ Database │ │ REST API │ │  File  │ │
  │  │  ⊕       │ │  ⊕       │ │  ⊕       │ │  ⊕     │ │
  │  └──────────┘ └──────────┘ └──────────┘ └────────┘ │
  └─────────────────────────────────────────────────────┘

Step 2: 연결 정보 입력 (커넥터 유형별 동적 폼)
  SAP S/4HANA OData 선택 시:
  ┌─────────────────────────────────────────────────────┐
  │  SAP S/4HANA 연결 설정                              │
  │  ──────────────────────────                         │
  │  서버 URL: [https://s4hana.gst.co.kr:8080        ]  │
  │  인증 방식: [OAuth2 Client Credentials ▼]           │
  │  Client ID: [GST_NAVI_BI                         ]  │
  │  Client Secret: [••••••••••••••••••              ]  │
  │  Token URL: [https://.../oauth/token             ]  │
  └─────────────────────────────────────────────────────┘

Step 3: 연결 테스트
  ┌─────────────────────────────────────────────────────┐
  │  ✓ 서버 연결 성공 (125ms)                            │
  │  ✓ 인증 성공                                        │
  │  ✓ API 서비스 접근 가능 (3개 서비스 발견)             │
  └─────────────────────────────────────────────────────┘

Step 4: 데이터 선택 및 미리보기
  ┌─────────────────────────────────────────────────────┐
  │  사용할 데이터 선택                                   │
  │  ○ API_SALES_ORDER_SRV / A_SalesOrder              │
  │  ○ API_PRODUCT_SRV / A_Product                     │
  │  [미리보기 100행] → 테이블 형태로 표시               │
  └─────────────────────────────────────────────────────┘

Step 5: 동기화 설정
  ┌─────────────────────────────────────────────────────┐
  │  동기화 방식: [스케줄 자동 동기화 ▼]                  │
  │  주기: [매일 새벽 2시 ▼]                             │
  │  증분/전체: [증분 (마지막 동기화 이후만) ▼]           │
  │  실패 시: [이메일 알림 발송 ☑]                       │
  └─────────────────────────────────────────────────────┘
```

---

### 2. 시맨틱 레이어 편집기

#### 비즈니스 용어 ↔ SQL 매핑 UI

```
시맨틱 레이어 편집기
┌─────────────────────────────────────────────────────────────────┐
│  데이터셋: 매출 데이터 (fact_sales + dim*)                        │
│  ──────────────────────────────────────────────────────────     │
│  필드 목록                         │  필드 편집                   │
│  ─────────────────────────         │  ─────────────────────────  │
│  ▼ 차원 (Dimensions)              │  표시명: [매출액 (원)       ] │
│    📅 날짜 (date_key)             │  원본필드: revenue_krw       │
│    👤 고객명 (customer_name)      │  타입: [측정값 ▼]            │
│    📦 제품명 (product_name)       │  데이터타입: [숫자 ▼]        │
│    🏭 공장 (location_name)       │  집계방식: [합계(SUM) ▼]     │
│  ▼ 측정값 (Measures)             │  포맷: [통화 (#,##0원) ▼]    │
│    💰 매출액 (revenue_krw)        │  단위: [원               ]   │
│    📊 매출총이익 (gross_profit)   │                              │
│    🎯 달성률 (achievement_rate)  │  계산 수식:                  │
│    [+ 계산 필드 추가]             │  ┌─────────────────────┐    │
│                                  │  │ SUM(revenue_krw) /  │    │
│                                  │  │ SUM(target_revenue) │    │
│                                  │  │ * 100               │    │
│                                  │  └─────────────────────┘    │
│                                  │  [수식 검증] [미리보기]       │
└─────────────────────────────────────────────────────────────────┘
```

#### Excel-like 수식 편집기 지원 함수

| 분류 | 함수 | 설명 | 예시 |
|------|------|------|------|
| 집계 | SUM | 합계 | `SUM(revenue_krw)` |
| 집계 | AVG | 평균 | `AVG(unit_price)` |
| 집계 | COUNT | 건수 | `COUNT(order_id)` |
| 집계 | COUNTD | 고유 건수 | `COUNTD(customer_id)` |
| 집계 | MAX/MIN | 최대/최소 | `MAX(revenue_krw)` |
| 수학 | ROUND | 반올림 | `ROUND(value, 2)` |
| 수학 | ABS | 절대값 | `ABS(variance)` |
| 수학 | DIVIDE | 안전 나누기 | `DIVIDE(profit, revenue, 0)` |
| 조건 | IF | 조건 | `IF(revenue > 0, revenue/qty, 0)` |
| 조건 | CASE | 다중 조건 | `CASE WHEN grade='A' THEN 3 ELSE 1 END` |
| 조건 | COALESCE | NULL 대체 | `COALESCE(value, 0)` |
| 날짜 | DATE_DIFF | 날짜 차이 | `DATE_DIFF(end_date, start_date, 'day')` |
| 날짜 | DATE_TRUNC | 날짜 절삭 | `DATE_TRUNC('month', order_date)` |
| 문자 | CONCAT | 연결 | `CONCAT(year, '년 ', month, '월')` |
| 창함수 | RANK | 순위 | `RANK() OVER (ORDER BY revenue DESC)` |
| 창함수 | LAG | 이전 값 | `LAG(revenue, 1) OVER (ORDER BY date_key)` |
| 비율 | PERCENT_OF | 비율 | `PERCENT_OF(SUM(revenue_krw))` |

---

### 3. KPI 계산 템플릿 엔진

#### 내장 KPI 템플릿 전체 목록

```
KPI 템플릿 라이브러리 (31종)
│
├── 재무 (6종)
│   ├── EBITDA = SUM(op_profit) + SUM(depreciation) + SUM(amortization)
│   ├── EBITDA Margin = DIVIDE(EBITDA, SUM(revenue_krw), 0) * 100
│   ├── 매출총이익율 = DIVIDE(SUM(revenue_krw) - SUM(cogs_krw), SUM(revenue_krw), 0) * 100
│   ├── 영업이익율 = DIVIDE(SUM(op_profit_krw), SUM(revenue_krw), 0) * 100
│   ├── 재고회전율 = DIVIDE(SUM(cogs_krw), AVG(inventory_value), 0)
│   └── ROE = DIVIDE(SUM(net_income), AVG(equity), 0) * 100
│
├── 영업 (5종)
│   ├── 수주달성률 = DIVIDE(SUM(actual_order), SUM(target_order), 0) * 100
│   ├── 매출달성률 = DIVIDE(SUM(revenue_krw), SUM(target_revenue), 0) * 100
│   ├── 고객유지율(CRR) = DIVIDE(SUM(retained_customers), SUM(prev_customers), 0) * 100
│   ├── ASP = DIVIDE(SUM(revenue_krw), SUM(invoice_qty), 0)
│   └── 수주잔고 = SUM(backlog_amount)
│
├── 생산 (6종)
│   ├── OTD = DIVIDE(SUM(otd_count), SUM(total_delivery_count), 0) * 100
│   ├── 가동률 = DIVIDE(SUM(actual_qty), SUM(plan_qty), 0) * 100
│   ├── 생산성 = DIVIDE(SUM(actual_qty), SUM(headcount), 0)
│   ├── OEE = AVG(availability) * AVG(performance) * AVG(quality_rate) / 10000
│   ├── 불량률 = DIVIDE(SUM(defect_qty), SUM(actual_qty), 0) * 100
│   └── 직행률 = DIVIDE(SUM(good_qty), SUM(actual_qty), 0) * 100
│
├── 서비스 (6종)
│   ├── 예지보전율 = DIVIDE(SUM(predictive_count), SUM(pm_count) + SUM(cm_count), 0) * 100
│   ├── 장비가동율 = DIVIDE(SUM(operating_hours), SUM(available_hours), 0) * 100
│   ├── MTTR = DIVIDE(SUM(repair_hours), SUM(cm_count), 0)
│   ├── MTBF = DIVIDE(SUM(operating_hours), SUM(breakdown_count), 0)
│   ├── 인당커버리지 = DIVIDE(SUM(total_equipment_cnt), SUM(service_engineer_cnt), 0)
│   └── 긴급수리율 = DIVIDE(SUM(em_count), SUM(cm_count), 0) * 100
│
├── 품질 (4종)
│   ├── 불량률 = DIVIDE(SUM(reject_qty), SUM(inspection_qty), 0) * 100
│   ├── Field Fail Rate = DIVIDE(SUM(field_fail_count), SUM(total_equipment_cnt), 0) * 100
│   ├── 개선완료율 = DIVIDE(SUM(improvement_done), SUM(improvement_count), 0) * 100
│   └── 재작업율 = DIVIDE(SUM(rework_qty), SUM(inspection_qty), 0) * 100
│
└── HR (4종)
    ├── 인력생산성 = DIVIDE(SUM(value_added), SUM(labor_cost_total), 0)
    ├── 이직률 = DIVIDE(SUM(resign_count), AVG(headcount_start + headcount_end) / 2, 0) * 100
    ├── 초과근무율 = DIVIDE(SUM(overtime_hours), SUM(attendance_days) * 8, 0) * 100
    └── 인건비율 = DIVIDE(SUM(labor_cost_total), SUM(revenue_krw), 0) * 100
```

#### 커스텀 KPI 수식 편집기 UI

```
커스텀 KPI 생성
┌──────────────────────────────────────────────────────────────────┐
│  KPI명: [월간 통합 효율 지수                                     ]│
│  카테고리: [생산 ▼]    단위: [점   ]    포맷: [#,##0.0 ▼]        │
│                                                                  │
│  수식 편집기                                                      │
│  ┌────────────────────────────────────────────────────────────┐  │
│  │ (OEE * 0.4) + (가동률 * 0.3) + ((100 - 불량률) * 0.3)    │  │
│  └────────────────────────────────────────────────────────────┘  │
│                                                                  │
│  함수 도움말 [?]     │  필드 선택 [+]     │  [수식 검증]          │
│                                                                  │
│  검증 결과: ✓ 문법 정상   │  미리보기 값: 87.3                    │
│                                                                  │
│  임계값 설정:                                                     │
│  목표: [90      ]   경고: [80      ]   위험: [70      ]          │
│  방향: [높을수록 좋음 ▼]                                          │
└──────────────────────────────────────────────────────────────────┘
```

---

### 4. 차트 빌더 (노코드)

#### 지원 차트 15종 및 설정 항목

| 차트 유형 | 설명 | 주요 설정 항목 |
|----------|------|--------------|
| 세로 막대 (Bar) | 범주별 비교 | X축(차원), Y축(측정값 1~3개), 스택/그룹, 색상, TopN, 정렬, 레이블 |
| 가로 막대 (HBar) | 순위 시각화 | 동일 + 순위 표시, 기준선 |
| 꺾은선 (Line) | 시계열 추이 | X축(날짜), Y축(측정값), 추세선, 스무딩, 마커, 영역 채우기 |
| 영역 (Area) | 누적 추이 | 꺾은선 + 영역 채우기, 투명도 |
| 원형 (Pie) | 구성 비율 | 차원(레이블), 측정값, 최소 표시 비율, 기타 그룹화, 레이블 위치 |
| 도넛 (Donut) | 구성 비율 + 중앙 KPI | 원형 + 중앙 텍스트 설정 |
| 산점도 (Scatter) | 상관 분석 | X축, Y축, 크기(버블), 색상 차원, 추세선, 사분면 기준선 |
| 버블 (Bubble) | 3차원 비교 | 산점도 + Z축(버블 크기), 레이블 |
| 히트맵 (Heatmap) | 2차원 분포 | X축(차원), Y축(차원), 값(측정값), 색상 범위, 셀 크기 |
| 트리맵 (Treemap) | 계층 구성비 | 계층 차원(2~3레벨), 크기(측정값), 색상(측정값 2) |
| 깔때기 (Funnel) | 프로세스 전환율 | 단계(차원), 값(측정값), 전환율 표시 |
| 게이지 (Gauge) | 목표 달성도 | 측정값, 목표값, 임계값(경고/위험), 색상 범위 |
| 레이더 (Radar) | 다차원 비교 | 축(차원 최대 8개), 값(측정값), 비교 계열 |
| 폭포수 (Waterfall) | 증감 분석 | 시작값, 증감 항목(차원+측정값), 합계 표시 |
| 복합 (Combo) | 막대+꺾은선 | Bar 설정 + Line 설정, 이중 Y축 |

#### 차트 빌더 UI 레이아웃

```
차트 빌더 화면 구성
┌────────────────────────────────────────────────────────────────────┐
│  ← 뒤로   차트 빌더: 월별 매출 분석                [저장] [미리보기]│
├──────────┬─────────────────────────────────┬──────────────────────┤
│ 데이터    │        차트 캔버스                │  속성 패널           │
│ 패널      │                                 │                      │
│ ──────── │  ┌───────────────────────────┐  │  차트 유형:          │
│ 데이터셋: │  │                           │  │  [막대 차트 ▼]      │
│ [매출▼]  │  │      월별 매출 추이         │  │                      │
│          │  │  (차트 미리보기)            │  │  X축:               │
│ 차원:     │  │                           │  │  [월(month) ▼]      │
│ [월  ]   │  └───────────────────────────┘  │                      │
│ [채널]   │                                 │  Y축 (왼쪽):         │
│ [+추가]  │  드래그하여 필드를 여기에 놓으세요 │  [매출액(revenue)▼] │
│          │                                 │                      │
│ 측정값:   │                                 │  Y축 (오른쪽):      │
│ [매출액] │                                 │  [달성률(%) ▼]      │
│ [이익률] │                                 │                      │
│ [+추가]  │                                 │  정렬: [날짜 오름차▼]│
│          │                                 │  TopN: [전체    ▼]  │
│ 필터:     │                                 │                      │
│ [+추가]  │                                 │  색상 팔레트:        │
│          │                                 │  [기본 ▼]           │
│          │                                 │                      │
│          │                                 │  레이블 표시: [✓]   │
│          │                                 │  범례 표시:  [✓]    │
│          │                                 │  드릴다운:   [✓]    │
└──────────┴─────────────────────────────────┴──────────────────────┘
```

---

### 5. 대시보드 디자이너

#### 기능 상세

```
대시보드 디자이너 핵심 기능

1. 드래그&드롭 위젯 배치 (react-grid-layout 기반)
   • 24 컬럼 그리드
   • 위젯 크기: 최소 2×2, 최대 24×12
   • 자동 충돌 방지
   • 모바일 반응형 레이아웃 자동 생성

2. 위젯 유형
   • 차트 위젯: meta_charts에서 선택 또는 인라인 차트 생성
   • KPI 카드: 단일 측정값 + 비교 화살표 + 스파크라인
   • 텍스트/HTML: Markdown 편집기, 변수 삽입 {{variable_name}}
   • 이미지: URL 또는 파일 업로드
   • 필터 위젯: 선택형/날짜/슬라이더
   • 테이블 위젯: 페이지네이션, 정렬, 컬럼 숨기기
   • iframe: 외부 URL 임베드

3. 글로벌 필터 연동
   • 대시보드 상단 필터바
   • 필터 변경 시 연동 위젯 전체 일괄 갱신
   • 특정 위젯 필터 무시 설정 가능

4. 변수 바인딩
   • 대시보드 변수: {{year}}, {{location}}, {{department}}
   • URL 파라미터로 초기값 설정: /dashboard/uuid?year=2026&location=KR01
   • 사용자별 기본값 (preferences)

5. 테마 설정
   • Light / Dark / 사용자 정의
   • 컬러 팔레트 선택
   • 폰트/크기 설정
   • 차트 기본 색상 오버라이드
```

---

### 6. 보고서 디자이너

#### WYSIWYG 보고서 편집기 기능

```
보고서 디자이너 화면
┌──────────────────────────────────────────────────────────────────┐
│  도구모음: [텍스트][차트][표][KPI카드][이미지][페이지구분][변수삽입] │
├──────────────────────────────────────────────────────────────────┤
│ ┌──────────────────────────────────────────────────────────────┐ │
│ │                    2026년 3월 경영 보고서                     │ │
│ │                     GST Corporation                          │ │
│ └──────────────────────────────────────────────────────────────┘ │
│ ┌───────────────────────────┐ ┌──────────────────────────────┐   │
│ │  매출 현황                 │ │  주요 KPI                    │   │
│ │  [월별 매출 Bar Chart]     │ │  매출달성률: 94.7%           │   │
│ │                           │ │  영업이익율: 12.3%           │   │
│ └───────────────────────────┘ └──────────────────────────────┘   │
│ ┌──────────────────────────────────────────────────────────────┐ │
│ │  전략적 시사점 및 액션 플랜                                    │ │
│ │  이번 달 매출은 목표 대비 94.7% 달성하였으며, {{insight_text}}  │ │
│ └──────────────────────────────────────────────────────────────┘ │
└──────────────────────────────────────────────────────────────────┘

스케줄 설정:
┌──────────────────────────────────────────────────────────────────┐
│  자동 발송 스케줄: [매월 5일 오전 9시 ▼]                          │
│  출력 형식: [PDF ✓]  [Excel ✓]  [PPT  ]                          │
│  수신자: ceo@gst.co.kr, cfo@gst.co.kr, +슬랙 #executive          │
│  제목 템플릿: {{year}}년 {{month}}월 경영 보고서                   │
└──────────────────────────────────────────────────────────────────┘
```

---

## VI. AI/ML 파이프라인

### 1. NLQ (자연어 질의) — 상세 아키텍처

#### 처리 흐름

```
사용자 입력: "이번 달 매출이 지난달보다 왜 떨어졌나요?"
      │
      ▼
┌─────────────────────────────────────────────────────────────────┐
│                    NLQ Preprocessing                             │
│  • 언어 감지 (한국어)                                            │
│  • 의도 분류: 비교/트렌드/랭킹/Why분석/예측                      │
│  • 개체명 인식 (NER): 날짜="이번 달", 지표="매출", 비교="지난달" │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Context Assembly                              │
│  시맨틱 레이어 메타데이터 주입 (현재 데이터셋의 스키마 정보)         │
│                                                                 │
│  System Prompt에 포함되는 정보:                                  │
│  • 테이블 목록 및 컬럼 설명                                      │
│  • 비즈니스 용어 매핑 (매출 = SUM(revenue_krw))                  │
│  • KPI 공식 정의                                                │
│  • 현재 활성 필터 (location_key=1, year=2026)                   │
│  • 현재 날짜 컨텍스트                                            │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Claude API 호출                               │
│                                                                 │
│  Model: claude-3-5-sonnet-20241022                              │
│                                                                 │
│  System: "당신은 제조업 BI 플랫폼의 SQL 전문가입니다.            │
│  다음 스키마를 참조하여 사용자의 질문을 분석하고 SQL을 생성하세요.  │
│  가능한 경우 Why 분석도 함께 제공하세요.                          │
│  [스키마 메타데이터]                                             │
│  현재 날짜: 2026-04-01, 활성 필터: ..."                          │
│                                                                 │
│  User: "이번 달 매출이 지난달보다 왜 떨어졌나요?"                 │
│                                                                 │
│  Response Format: JSON                                          │
│  {                                                              │
│    "interpretation": "...",                                     │
│    "sql": "SELECT ...",                                         │
│    "followup_sqls": ["SELECT ...", "SELECT ..."],               │
│    "insight_template": "..."                                    │
│  }                                                              │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    SQL Validation & Execution                    │
│  • SQL 문법 검증                                                 │
│  • 접근 권한 검증 (행 레벨 보안)                                  │
│  • 실행 계획 분석 (위험 쿼리 차단)                                │
│  • ClickHouse 실행 (최대 30초 타임아웃)                          │
└─────────────────────────────┬───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Result Processing                             │
│  • 데이터 + 인사이트 텍스트 생성 (Claude API 2차 호출)             │
│  • 최적 차트 유형 추천                                            │
│  • 후속 질문 제안 3개 생성                                        │
│  • 이력 저장 (user_analyses 테이블)                              │
└─────────────────────────────────────────────────────────────────┘
```

#### Claude API System Prompt 템플릿

```
당신은 GST Corporation의 제조업 BI 플랫폼 AI 어시스턴트입니다.
사용자의 한국어 질문을 분석하여 정확한 SQL을 생성하고 인사이트를 제공합니다.

## 데이터베이스 스키마
{semantic_layer_schema}

## 현재 컨텍스트
- 현재 날짜: {current_date}
- 활성 필터: {active_filters}
- 사용자 권한: {user_permissions}
- 기본 데이터셋: {default_dataset}

## SQL 생성 규칙
1. ClickHouse SQL 문법을 사용하세요
2. 테이블 조인 시 차원 테이블을 항상 LEFT JOIN으로 연결하세요
3. 날짜 필터: date_key 컬럼은 YYYYMMDD 형식의 Integer입니다
4. NULL 값은 COALESCE(value, 0)으로 처리하세요
5. 결과는 최대 {max_rows}행으로 제한하세요
6. Why 분석 시 기여 요인 분해(Factor Decomposition) 쿼리를 추가로 생성하세요

## 응답 형식 (반드시 JSON으로)
{
  "interpretation": "질문 해석 (한국어)",
  "sql": "메인 SQL",
  "followup_sqls": ["분석 1 SQL", "분석 2 SQL"],
  "suggested_chart": {"type": "bar/line/...", "x_field": "...", "y_field": "..."},
  "insight_template": "결과 데이터를 받으면 생성할 인사이트 템플릿",
  "followup_questions": ["후속 질문 1", "후속 질문 2", "후속 질문 3"]
}
```

---

### 2. 예측 모델

#### 2.1 매출 예측 (Prophet + ARIMA 앙상블)

```python
# 매출 예측 모델 파이프라인
class SalesForecastPipeline:
    """
    Facebook Prophet + ARIMA 앙상블 매출 예측
    - 입력: 과거 24개월 월별 매출 데이터
    - 출력: 향후 12개월 예측값 + 신뢰구간
    """
    
    def __init__(self, config: dict):
        self.prophet_model = Prophet(
            seasonality_mode='multiplicative',
            yearly_seasonality=True,
            weekly_seasonality=False,
            daily_seasonality=False,
            changepoint_prior_scale=0.05,     # 트렌드 변화 민감도
            seasonality_prior_scale=10,
            holidays=KoreanHolidays()          # 한국 공휴일 반영
        )
        self.arima_order = (2, 1, 2)           # ARIMA(p,d,q)
        self.ensemble_weights = [0.6, 0.4]    # Prophet:ARIMA 가중치
    
    def add_regressors(self):
        """외부 변수 추가"""
        self.prophet_model.add_regressor('marketing_spend')
        self.prophet_model.add_regressor('num_new_products')
        self.prophet_model.add_regressor('economic_index')
    
    def train(self, df: pd.DataFrame) -> None:
        """학습 (df: ds(날짜), y(매출), 외부변수 컬럼 포함)"""
        ...
    
    def predict(self, periods: int = 12) -> pd.DataFrame:
        """
        Returns: DataFrame with columns:
          ds, yhat, yhat_lower, yhat_upper, 
          prophet_pred, arima_pred, ensemble_pred
        """
        ...
    
    def evaluate(self, test_df: pd.DataFrame) -> dict:
        """
        Returns: {
          "mape": 5.2,      # 평균절대백분율오차 (%)
          "rmse": 123456789,
          "mae": 98765432,
          "r2": 0.94
        }
        """
        ...
```

#### 2.2 장비 고장 예측 (RandomForest + LSTM)

```python
class EquipmentFailurePrediction:
    """
    장비 고장 예측 모델
    - 알고리즘: RandomForest (표 형식 특징) + LSTM (시계열 패턴)
    - 입력: IoT 센서 데이터 (온도, 진동, 전류, 소음) + 보전 이력
    - 출력: 향후 30일 고장 확률 (0~1)
    """
    
    FEATURES = [
        'temperature_avg', 'temperature_max',
        'vibration_rms', 'vibration_peak',
        'current_avg', 'current_peak',
        'noise_db',
        'days_since_last_pm',
        'total_operating_hours',
        'equipment_age_days',
        'error_count_7d',
        'error_count_30d'
    ]
    
    def predict_failure_probability(
        self,
        equipment_id: str,
        prediction_horizon_days: int = 30
    ) -> dict:
        """
        Returns:
          {
            "equipment_id": "EQ-001",
            "equipment_name": "CNC 가공기 #3",
            "failure_probability": 0.73,
            "risk_level": "high",
            "predicted_failure_date": "2026-04-15",
            "confidence": 0.85,
            "contributing_factors": [
              {"factor": "진동 증가 추세", "importance": 0.42},
              {"factor": "온도 이상", "importance": 0.28},
              {"factor": "마지막 PM 90일 경과", "importance": 0.19}
            ],
            "recommended_action": "즉시 점검 및 베어링 교체 권장"
          }
        """
        ...
```

#### 2.3 인력 이탈 예측 (Gradient Boosting Classification)

```python
class HRAttritionPrediction:
    """
    직원 이탈 예측 모델
    - 알고리즘: XGBoost / LightGBM
    - 입력: 인사 데이터 (재직 기간, 급여, 평가, 초과근무, 부서 등)
    - 출력: 이탈 위험도 (High/Medium/Low)
    """
    
    FEATURES = [
        'tenure_months',        # 재직기간
        'age',
        'salary_ratio',         # 동일직급 평균 대비 급여 비율
        'last_promotion_months',# 마지막 승진 후 기간
        'performance_score',    # 최근 평가 점수
        'overtime_hours_3m',    # 최근 3개월 초과근무
        'absence_days_3m',      # 최근 3개월 결근일수
        'dept_turnover_rate',   # 부서 이직률
        'manager_change_count', # 최근 1년 매니저 변경 횟수
        'training_hours',       # 연간 교육 시간
    ]
    
    def predict_attrition_risk(self, employee_data: dict) -> dict:
        """
        Returns:
          {
            "employee_id": "EMP-001",
            "attrition_probability": 0.68,
            "risk_level": "high",
            "top_risk_factors": [
              "초과근무 시간 과다 (월 평균 45시간)",
              "24개월간 승진 없음",
              "동료 2명 최근 퇴직"
            ],
            "recommended_interventions": [
              "즉시 1:1 면담 실시",
              "업무 부하 재조정",
              "승진/보상 검토"
            ]
          }
        """
        ...
```

---

### 3. Auto Insight 엔진

#### 자동 변동 분석 (Variance Analysis)

```
Auto Insight 처리 흐름:

1. 이상 감지 (Anomaly Detection)
   ├── Z-score 방법: |z| > 2.5 이면 이상값
   ├── IQR 방법: Q1 - 1.5*IQR < x < Q3 + 1.5*IQR 벗어나면 이상
   └── LSTM Autoencoder: 재구성 오류가 임계값 초과 시 이상

2. 트렌드 변화점 감지 (Change Point Detection)
   └── PELT 알고리즘 (Pruned Exact Linear Time)
       → 트렌드 방향 변화 시점 자동 탐지

3. 기여 요인 분해 (Factor Decomposition)
   └── Shapley Value 기반 기여도 계산
       예) 매출 전월比 -8% = 고객 감소(-5%) + ASP 하락(-4%) + 채널 믹스(+1%)

4. 인사이트 텍스트 생성
   └── Claude API: 분석 결과 → 한국어 경영 인사이트
       "3월 매출이 전월 대비 8% 감소한 것은 주로 A고객 주문 중단(-5%)과
        수출 단가 하락(-4%)에 기인합니다. 특히 A고객의 재고 조정으로 인한
        일시적 수요 감소로 4월에는 회복이 예상됩니다."

5. 추천 액션 생성
   └── 인사이트 기반 → 우선순위 액션 3개 자동 제안
```

---

### 4. MLOps 파이프라인

```
MLOps Architecture
┌─────────────────────────────────────────────────────────────────┐
│  데이터 파이프라인                                               │
│  ClickHouse 학습 데이터 → Feature Engineering → Feature Store   │
└─────────────────────────────┬───────────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────────┐
│  모델 학습 파이프라인                                             │
│  • 주기: 매주 일요일 새벽 2시 자동 재학습                         │
│  • 학습 데이터: 최근 24개월                                      │
│  • 검증: 마지막 3개월 Hold-out 검증                              │
│  • 기준: MAPE < 8% (매출예측) / AUC > 0.80 (고장예측)           │
└─────────────────────────────┬───────────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────────┐
│  모델 레지스트리 (MLflow)                                         │
│  • 모델 버전 관리 (v1, v2, v3...)                               │
│  • 메트릭 추적 (MAPE, RMSE, AUC)                               │
│  • 아티팩트 저장 (MinIO)                                         │
│  • A/B 테스트: 트래픽 20% → 신모델, 80% → 현행모델              │
└─────────────────────────────┬───────────────────────────────────┘
                              │
┌─────────────────────────────▼───────────────────────────────────┐
│  모델 서빙 (FastAPI)                                              │
│  • 추론 API 서버 (AI Service :3007)                             │
│  • 모델 로드: 시작 시 메모리 적재                                 │
│  • 추론 캐시: Redis (TTL 1시간)                                  │
│  • SLA: P95 < 2초                                               │
└─────────────────────────────────────────────────────────────────┘

모델 성능 모니터링:
• Data Drift 감지: PSI (Population Stability Index) > 0.2 시 알림
• 모델 성능 저하: 실제값 vs 예측값 MAPE 상승 시 자동 재학습 트리거
• Grafana 모델 모니터링 대시보드
```

---

## VII. 인프라 및 배포

### 1. 컨테이너 아키텍처 (Docker + Kubernetes)

#### docker-compose.yml (개발 환경)

```yaml
version: '3.9'

services:
  # API Gateway / Nginx
  nginx:
    image: nginx:1.25-alpine
    ports: ["80:80", "443:443"]
    volumes:
      - ./nginx/conf.d:/etc/nginx/conf.d:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on: [auth-service, dashboard-service, ai-service]

  # Auth Service
  auth-service:
    build: ./services/auth
    environment:
      - NODE_ENV=production
      - PORT=3001
      - DATABASE_URL=postgresql://bi_user:${DB_PASS}@postgres:5432/gst_navi
      - REDIS_URL=redis://redis:6379/0
      - JWT_ACCESS_SECRET=${JWT_ACCESS_SECRET}
      - JWT_REFRESH_SECRET=${JWT_REFRESH_SECRET}
    depends_on: [postgres, redis]
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:3001/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  # Dashboard Service
  dashboard-service:
    build: ./services/dashboard
    environment:
      - NODE_ENV=production
      - PORT=3002
      - DATABASE_URL=postgresql://bi_user:${DB_PASS}@postgres:5432/gst_navi
      - REDIS_URL=redis://redis:6379/1
    depends_on: [postgres, redis]

  # Analytics Engine
  analytics-engine:
    build: ./services/analytics
    environment:
      - PORT=3003
      - CLICKHOUSE_URL=http://clickhouse:8123
      - CLICKHOUSE_USER=bi_reader
      - CLICKHOUSE_PASS=${CH_PASS}
      - REDIS_URL=redis://redis:6379/2

  # Query Engine
  query-engine:
    build: ./services/query
    environment:
      - PORT=3004
      - CLICKHOUSE_URL=http://clickhouse:8123
      - POSTGRES_URL=postgresql://bi_user:${DB_PASS}@postgres:5432/gst_navi

  # Report Service
  report-service:
    build: ./services/report
    environment:
      - PORT=3005
      - DATABASE_URL=postgresql://bi_user:${DB_PASS}@postgres:5432/gst_navi
      - REDIS_URL=redis://redis:6379/3
      - MINIO_ENDPOINT=minio:9000
      - MINIO_ACCESS_KEY=${MINIO_KEY}
      - MINIO_SECRET_KEY=${MINIO_SECRET}
      - SMTP_HOST=${SMTP_HOST}
    volumes:
      - /tmp/reports:/tmp/reports   # Puppeteer 임시 파일

  # Alert Service
  alert-service:
    build: ./services/alert
    environment:
      - PORT=3006
      - DATABASE_URL=postgresql://bi_user:${DB_PASS}@postgres:5432/gst_navi
      - REDIS_URL=redis://redis:6379/4
      - SLACK_WEBHOOK=${SLACK_WEBHOOK}

  # AI Service (Python FastAPI)
  ai-service:
    build: ./services/ai
    environment:
      - PORT=3007
      - ANTHROPIC_API_KEY=${ANTHROPIC_API_KEY}
      - CLICKHOUSE_URL=http://clickhouse:8123
      - REDIS_URL=redis://redis:6379/5
      - MLFLOW_TRACKING_URI=http://mlflow:5000
    volumes:
      - ./models:/app/models   # 학습된 모델 파일

  # Connector Service
  connector-service:
    build: ./services/connector
    environment:
      - PORT=3008
      - DATABASE_URL=postgresql://bi_user:${DB_PASS}@postgres:5432/gst_navi
      - REDIS_URL=redis://redis:6379/6
      - ENCRYPTION_KEY=${ENCRYPTION_KEY}   # AES-256 키 (커넥터 인증정보 암호화)

  # Admin Service
  admin-service:
    build: ./services/admin
    environment:
      - PORT=3009
      - DATABASE_URL=postgresql://bi_user:${DB_PASS}@postgres:5432/gst_navi
      - ELASTICSEARCH_URL=http://elasticsearch:9200

  # Frontend (React SPA)
  frontend:
    build: ./frontend
    environment:
      - VITE_API_BASE_URL=/api/v1
      - VITE_WS_URL=wss://gst-navi.co.kr/ws

  # PostgreSQL 16
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: gst_navi
      POSTGRES_USER: bi_user
      POSTGRES_PASSWORD: ${DB_PASS}
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./db/init:/docker-entrypoint-initdb.d:ro
    command: >
      postgres
        -c max_connections=200
        -c shared_buffers=2GB
        -c effective_cache_size=6GB
        -c maintenance_work_mem=512MB
        -c checkpoint_completion_target=0.9
        -c wal_buffers=64MB
        -c default_statistics_target=100

  # ClickHouse
  clickhouse:
    image: clickhouse/clickhouse-server:24.3
    environment:
      CLICKHOUSE_USER: bi_reader
      CLICKHOUSE_PASSWORD: ${CH_PASS}
      CLICKHOUSE_DB: gst_analytics
    volumes:
      - clickhouse_data:/var/lib/clickhouse
      - ./clickhouse/config.xml:/etc/clickhouse-server/config.d/custom.xml:ro
    ulimits:
      nofile:
        soft: 262144
        hard: 262144

  # Redis 7
  redis:
    image: redis:7-alpine
    command: redis-server --maxmemory 2gb --maxmemory-policy allkeys-lru
    volumes:
      - redis_data:/data

  # MinIO
  minio:
    image: minio/minio:RELEASE.2024-01-01
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: ${MINIO_KEY}
      MINIO_ROOT_PASSWORD: ${MINIO_SECRET}
    volumes:
      - minio_data:/data

  # Elasticsearch
  elasticsearch:
    image: elasticsearch:8.12.0
    environment:
      - discovery.type=single-node
      - ES_JAVA_OPTS=-Xms2g -Xmx2g
      - xpack.security.enabled=false
    volumes:
      - es_data:/usr/share/elasticsearch/data

  # MLflow
  mlflow:
    image: ghcr.io/mlflow/mlflow:2.10.0
    command: mlflow server --host 0.0.0.0 --backend-store-uri postgresql://bi_user:${DB_PASS}@postgres:5432/mlflow --default-artifact-root s3://mlflow-artifacts/
    depends_on: [postgres, minio]

volumes:
  postgres_data:
  clickhouse_data:
  redis_data:
  minio_data:
  es_data:
```

#### Kubernetes Deployment (운영 환경 핵심)

```yaml
# k8s/deployments/auth-service.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: auth-service
  namespace: gst-navi
  labels:
    app: auth-service
    version: v1.0.0
spec:
  replicas: 2
  selector:
    matchLabels:
      app: auth-service
  template:
    metadata:
      labels:
        app: auth-service
    spec:
      containers:
        - name: auth-service
          image: registry.gst.co.kr/gst-navi/auth-service:1.0.0
          ports:
            - containerPort: 3001
          resources:
            requests:
              memory: "256Mi"
              cpu: "100m"
            limits:
              memory: "512Mi"
              cpu: "500m"
          readinessProbe:
            httpGet:
              path: /health
              port: 3001
            initialDelaySeconds: 10
            periodSeconds: 5
          livenessProbe:
            httpGet:
              path: /health
              port: 3001
            initialDelaySeconds: 30
            periodSeconds: 10
          env:
            - name: DATABASE_URL
              valueFrom:
                secretKeyRef:
                  name: gst-navi-secrets
                  key: database-url
            - name: JWT_ACCESS_SECRET
              valueFrom:
                secretKeyRef:
                  name: gst-navi-secrets
                  key: jwt-access-secret
      topologySpreadConstraints:
        - maxSkew: 1
          topologyKey: kubernetes.io/hostname
          whenUnsatisfiable: DoNotSchedule
          labelSelector:
            matchLabels:
              app: auth-service

---
apiVersion: v1
kind: Service
metadata:
  name: auth-service
  namespace: gst-navi
spec:
  selector:
    app: auth-service
  ports:
    - port: 3001
      targetPort: 3001
  type: ClusterIP

---
# HPA (Auto Scaling)
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: auth-service-hpa
  namespace: gst-navi
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: auth-service
  minReplicas: 2
  maxReplicas: 8
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 80
```

---

### 2. CI/CD 파이프라인

```yaml
# .github/workflows/deploy.yml
name: GST NAVI BI - Deploy Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with: { node-version: '20' }
      - name: Install & Test
        run: |
          npm ci
          npm run test:unit
          npm run test:integration
      - name: Lint & Type Check
        run: |
          npm run lint
          npm run type-check

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build Docker Images
        run: |
          docker build -t auth-service:${{ github.sha }} ./services/auth
          docker build -t dashboard-service:${{ github.sha }} ./services/dashboard
          # ... 각 서비스 빌드
      - name: Push to Registry
        run: |
          docker push registry.gst.co.kr/gst-navi/auth-service:${{ github.sha }}
          # ...

  deploy-staging:
    needs: build
    runs-on: ubuntu-latest
    environment: staging
    steps:
      - name: Deploy to Staging (K8s)
        run: |
          kubectl set image deployment/auth-service \
            auth-service=registry.gst.co.kr/gst-navi/auth-service:${{ github.sha }} \
            -n gst-navi-staging
      - name: Run E2E Tests
        run: npm run test:e2e -- --env staging

  deploy-production:
    needs: deploy-staging
    runs-on: ubuntu-latest
    environment: production
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Blue/Green Deploy
        run: |
          # 신규 버전을 Green 환경에 배포
          kubectl apply -f k8s/production/green-deployment.yaml
          # 헬스체크 통과 후 트래픽 전환
          kubectl patch service auth-service -p '{"spec":{"selector":{"version":"green"}}}'
          # 구 버전(Blue) 종료
          kubectl delete -f k8s/production/blue-deployment.yaml
```

---

### 3. 모니터링 스택 (Prometheus + Grafana + ELK)

#### Prometheus 메트릭 설정

```yaml
# prometheus.yml
global:
  scrape_interval: 15s
  evaluation_interval: 15s

rule_files:
  - "alert_rules.yml"

scrape_configs:
  - job_name: 'gst-navi-services'
    static_configs:
      - targets:
          - 'auth-service:3001'
          - 'dashboard-service:3002'
          - 'analytics-engine:3003'
          - 'query-engine:3004'
    metrics_path: '/metrics'

  - job_name: 'postgresql'
    static_configs:
      - targets: ['postgres-exporter:9187']

  - job_name: 'clickhouse'
    static_configs:
      - targets: ['clickhouse-exporter:9116']

  - job_name: 'redis'
    static_configs:
      - targets: ['redis-exporter:9121']
```

#### 핵심 알림 규칙

```yaml
# alert_rules.yml
groups:
  - name: gst-navi-sla
    rules:
      - alert: HighQueryResponseTime
        expr: histogram_quantile(0.95, rate(query_duration_seconds_bucket[5m])) > 5
        for: 2m
        labels: { severity: warning }
        annotations:
          summary: "쿼리 응답시간 SLA 위반 (P95 > 5초)"

      - alert: ServiceDown
        expr: up{job="gst-navi-services"} == 0
        for: 1m
        labels: { severity: critical }
        annotations:
          summary: "서비스 다운: {{ $labels.instance }}"

      - alert: HighErrorRate
        expr: rate(http_requests_total{status=~"5.."}[5m]) / rate(http_requests_total[5m]) > 0.05
        for: 5m
        labels: { severity: warning }
        annotations:
          summary: "오류율 5% 초과: {{ $labels.job }}"

      - alert: DatabaseConnectionHigh
        expr: pg_stat_activity_count > 150
        for: 3m
        labels: { severity: warning }
        annotations:
          summary: "PostgreSQL 연결 수 경고 (> 150)"
```

#### Grafana 대시보드 목록

| 대시보드 | 주요 패널 |
|---------|---------|
| 시스템 개요 | 서비스별 응답시간, 오류율, TPS, 가용성 |
| 데이터베이스 | PostgreSQL 쿼리 성능, ClickHouse 집계 속도, Redis HIT률 |
| AI/ML 모니터링 | NLQ 응답시간, 예측 정확도 추이, Claude API 사용량 |
| ETL 파이프라인 | 파이프라인별 실행 상태, 처리 건수, 오류 이력 |
| 비즈니스 메트릭 | 일간 활성 사용자(DAU), 대시보드 생성 수, 보고서 발송 수 |

---

### 4. 보안 아키텍처

#### 인증/인가 흐름

```
사용자 로그인 흐름:
1. POST /api/v1/auth/login (이메일+패스워드)
2. Auth Service: bcrypt.compare(password, hash)
3. MFA 활성화 시: TOTP OTP 추가 검증
4. JWT Access Token (15분) + Refresh Token (7일) 발급
5. Access Token: Authorization: Bearer 헤더로 모든 API 요청에 포함
6. API Gateway: JWT 서명 검증 + 만료 검증 + 블랙리스트 확인
7. RBAC 미들웨어: 사용자 역할 → 리소스 권한 확인

LDAP/SSO 연동:
1. 사용자가 "SSO 로그인" 버튼 클릭
2. SAML 2.0 요청 → IdP (회사 AD/LDAP)
3. IdP 인증 완료 → SAML Response → Auth Service
4. SAML Assertion 검증 → 내부 사용자 생성/업데이트
5. JWT 발급 → 이후 동일

행 레벨 보안 (Row-Level Security):
• 데이터셋에 RLS 정책 설정 가능
• 예: 영업팀원은 자신의 담당 고객 데이터만 조회
• SQL 자동 주입: WHERE sales_rep_id = '{current_user_emp_id}'
```

#### 보안 설정 체크리스트

```
✅ HTTPS 강제 (HSTS: max-age=31536000)
✅ CORS: 허용된 오리진만 (화이트리스트)
✅ SQL Injection 방지: Prepared Statements, ORM 사용
✅ XSS 방지: Content-Security-Policy 헤더
✅ Rate Limiting: IP당 100req/min (로그인: 5회/min)
✅ 커넥터 인증정보 AES-256 암호화 저장
✅ 감사 로그: 모든 데이터 접근/변경 기록
✅ 개인정보 마스킹: 감사 로그 내 민감 정보
✅ 네트워크 분리: DMZ/Internal/Data 영역 분리
✅ mTLS: 서비스 간 통신 상호 인증 (사내 K8s)
✅ Secrets 관리: Kubernetes Secrets + HashiCorp Vault
✅ 취약점 스캔: Trivy (Docker 이미지), OWASP ZAP (API)
```

---

## VIII. 기술 스택 종합

### 레이어별 기술 선정표

| 레이어 | 기술 | 버전 | 선정 이유 |
|--------|------|------|---------|
| **프론트엔드** | React | 19 | 최신 Concurrent 기능, 팀 보유 기술 |
| | TypeScript | 5.x | 타입 안전성, 대규모 코드베이스 유지보수 |
| | Vite | 5.x | 개발 서버 속도, HMR 성능 |
| | ECharts | 6.x | 이미 사용 중, 15종 차트, 한국어 지원 |
| | react-grid-layout | 1.4 | 드래그&드롭 대시보드 레이아웃 |
| | Tailwind CSS | 3.x | 빠른 UI 개발, 커스텀 테마 |
| | Zustand | 4.x | 경량 상태 관리 (Redux 대비 보일러플레이트 최소) |
| | TanStack Query | 5.x | 서버 상태 캐싱/동기화 |
| | Monaco Editor | 0.45 | SQL/수식 코드 편집기 (VS Code 기반) |
| **API Gateway** | nginx | 1.25 | 리버스 프록시, 로드 밸런싱, SSL 종단 |
| | Express | 4.x | 미들웨어 생태계, 팀 친숙도 |
| **마이크로서비스** | Node.js | 20 LTS | 비동기 I/O, npm 생태계 |
| | Python | 3.11 | AI/ML 라이브러리 (AI Service 전용) |
| | FastAPI | 0.109 | Python 비동기 API, 자동 문서화 |
| **메시지 큐** | Bull | 4.x | Redis 기반 Job Queue, 재시도/스케줄 |
| | Redis Pub/Sub | 7.x | 실시간 이벤트, WebSocket 브로드캐스트 |
| **OLTP DB** | PostgreSQL | 16 | ACID, JSON, 파티셔닝, 성숙도 |
| **OLAP DB** | ClickHouse | 24.x | 컬럼형 저장, 집계 쿼리 100배 성능 |
| **캐시** | Redis | 7 | 인메모리 고속, 데이터 구조 다양 |
| **검색** | Elasticsearch | 8.x | 감사 로그 분석, 전문 검색 |
| **파일 저장** | MinIO | 2024 | S3 호환, 온프레미스, 보안 |
| **AI** | Claude API | 3.5 Sonnet | 한국어 NLQ 성능 최고, Function Calling |
| | Prophet | 1.1 | 시계열 예측, 계절성 자동 처리 |
| | XGBoost | 2.x | 표 형식 분류/회귀, 높은 정확도 |
| | scikit-learn | 1.4 | ML 파이프라인 표준 |
| **MLOps** | MLflow | 2.x | 모델 레지스트리, 실험 추적 |
| **컨테이너** | Docker | 25.x | 표준 컨테이너 |
| | Kubernetes | 1.29 | 오케스트레이션, HPA, 롤링 업데이트 |
| **CI/CD** | GitHub Actions | - | 코드 연동, 무료 러너 |
| **모니터링** | Prometheus | 2.49 | 메트릭 수집 표준 |
| | Grafana | 10.x | 시각화, 알림 |
| | ELK Stack | 8.x | 로그 수집/분석 |
| **보안** | HashiCorp Vault | 1.15 | 시크릿 관리 |
| | Keycloak | 23 | SSO/SAML IdP (선택) |
| **PDF 생성** | Puppeteer | 21.x | Chrome headless, 고품질 PDF |
| | ExcelJS | 4.x | Excel 파일 생성 |
| **보고서 발송** | Nodemailer | 6.x | 이메일 |
| | Slack Bolt | 3.x | 슬랙 통합 |

---

## IX. 개발 일정 (WBS)

### Phase 1: 기반 구축 (1~3개월)

| 주차 | 작업 | 담당 | 공수(MD) | 산출물 |
|------|------|------|:-------:|--------|
| 1-2 | 인프라 셋업 (K8s 클러스터, DB, Redis) | 백엔드/DevOps | 10 | 인프라 구성서 |
| 1-2 | DB 스키마 설계 및 DDL 실행 | 데이터/백엔드 | 15 | ERD, DDL |
| 2-3 | Auth Service 개발 | 백엔드 | 10 | 로그인/RBAC API |
| 2-4 | 커넥터 프레임워크 기반 개발 | 백엔드 | 20 | DB/REST/Excel 커넥터 |
| 3-4 | ETL 파이프라인 엔진 개발 | 데이터 | 20 | Extract/Transform/Load |
| 3-5 | Query Engine (시맨틱 레이어) | 백엔드/데이터 | 25 | SQL 빌더, 메타데이터 |
| 4-6 | 프론트엔드 기반 (라우터, 상태관리, 공통 컴포넌트) | 프론트엔드 | 20 | 프레임워크 |
| **Phase 1 합계** | | | **120 MD** | |

### Phase 2: 노코드 코어 (4~7개월)

| 주차 | 작업 | 담당 | 공수(MD) | 산출물 |
|------|------|------|:-------:|--------|
| 7-9 | 데이터소스 커넥터 관리 UI | 프론트엔드 | 20 | 커넥터 마법사 |
| 7-9 | SAP 커넥터 (RFC/OData) 개발 | 백엔드 | 25 | SAP 연동 |
| 9-11 | 시맨틱 레이어 편집기 UI | 프론트엔드 | 20 | 필드/측정값 편집기 |
| 9-11 | KPI 계산 템플릿 엔진 | 백엔드 | 20 | 31종 KPI 수식 |
| 11-13 | 차트 빌더 UI (15종 차트) | 프론트엔드 | 30 | 드래그&드롭 차트 빌더 |
| 11-14 | Analytics Engine 개발 | 백엔드 | 25 | KPI/차트 데이터 API |
| 13-15 | 대시보드 디자이너 | 프론트엔드 | 25 | 위젯 배치/필터 연동 |
| 15-17 | Dashboard Service 개발 | 백엔드 | 20 | 대시보드 CRUD API |
| **Phase 2 합계** | | | **185 MD** | |

### Phase 3: 보고서/Alert/AI (8~11개월)

| 주차 | 작업 | 담당 | 공수(MD) | 산출물 |
|------|------|------|:-------:|--------|
| 17-19 | 보고서 디자이너 UI | 프론트엔드 | 25 | WYSIWYG 보고서 편집기 |
| 17-20 | Report Service (PDF/Excel/스케줄) | 백엔드 | 30 | 보고서 자동 생성/발송 |
| 20-21 | Alert 규칙 편집기 UI | 프론트엔드 | 15 | Alert GUI |
| 20-22 | Alert Service 개발 | 백엔드 | 20 | KPI 모니터링/발송 |
| 22-24 | AI NLQ 개발 (Claude API 연동) | AI/백엔드 | 30 | 자연어 질의 |
| 24-26 | 예측 모델 개발 (Prophet/XGBoost) | AI | 35 | 매출/고장/이탈 예측 |
| 26-27 | Auto Insight 엔진 | AI | 20 | 자동 인사이트 |
| 27-28 | MLflow 모델 레지스트리 연동 | AI/DevOps | 15 | MLOps 파이프라인 |
| **Phase 3 합계** | | | **190 MD** | |

### Phase 4: 관리/보안/최적화 (12~14개월)

| 주차 | 작업 | 담당 | 공수(MD) | 산출물 |
|------|------|------|:-------:|--------|
| 28-30 | Admin Service (사용자/권한 관리) | 백엔드 | 25 | 관리자 콘솔 |
| 30-31 | 권한 관리 GUI (RBAC 매트릭스) | 프론트엔드 | 15 | 권한 관리 UI |
| 31-32 | LDAP/SSO 연동 | 백엔드 | 15 | SSO 인증 |
| 32-33 | 보안 감사 (취약점 점검) | 보안 | 10 | 보안 점검 보고서 |
| 33-35 | 성능 최적화 (쿼리, 캐시, 인덱스) | 데이터/백엔드 | 20 | 성능 최적화 보고서 |
| 35-36 | 모니터링 대시보드 (Grafana) | DevOps | 10 | 운영 모니터링 |
| **Phase 4 합계** | | | **95 MD** | |

### Phase 5: UAT 및 오픈 (15~16개월)

| 주차 | 작업 | 담당 | 공수(MD) | 산출물 |
|------|------|------|:-------:|--------|
| 36-38 | 통합 테스트 (E2E) | 전체 | 20 | 테스트 결과 보고서 |
| 38-40 | 사용자 수용 테스트 (UAT) | IT기획/현업 | 15 | UAT 결과 |
| 40-41 | 파일럿 운영 (50명) | 전체 | 10 | 파일럿 피드백 |
| 41-42 | 결함 수정 및 안정화 | 전체 | 20 | 수정 완료 |
| 42-44 | 전사 오픈 (200명) + 사용자 교육 | IT기획 | 15 | 운영 매뉴얼, 교육자료 |
| **Phase 5 합계** | | | **80 MD** | |

**총 개발 공수: 670 MD (실 작업) + 버퍼 20% = 약 24개월**

---

## IX-2. 마일스톤 (2026-04 시작 기준)

### 전체 타임라인

```
2026                                                                      2028
 4월   5월   6월   7월   8월   9월  10월  11월  12월   1월   2월   3월   4월
  │     │     │     │     │     │     │     │     │     │     │     │     │
  ▼     ▼           ▼                 ▼                 ▼           ▼     ▼
 M0    M1          M2                M3                M4          M5    M6
                                                                          
 2028
  5월   6월   7월   8월   9월  10월  11월  12월   1월   2월   3월
  │     │     │     │     │     │     │     │     │     │     │
  ▼           ▼                 ▼                       ▼     ▼
 M7          M8                M9                      M10   M11

```

### 마일스톤 상세

| ID | 마일스톤 | 일자 | Phase | 완료 기준 | 승인자 |
|:--:|---------|------|:-----:|----------|:------:|
| **M0** | 프로젝트 킥오프 | 2026-04-07 | 0 | PJT 헌장 승인, 팀 구성, 환경 구축 | 대표이사 |
| **M1** | 인프라 + DB 완료 | 2026-05-30 | 1 | 서버 셋업, PostgreSQL DDL, Docker 배포, CI/CD | IT기획 |
| **M2** | 기반 서비스 완료 | 2026-07-25 | 1 | Auth/Query Engine/커넥터 프레임워크/프론트 기반 | PM |
| **M3** | 노코드 코어 v1 | 2026-10-31 | 2 | 커넥터 UI, 시맨틱레이어, KPI 엔진, 차트빌더 15종 | 대표이사 |
| **M4** | 대시보드 + 리포트 | 2027-02-28 | 2 | 대시보드 디자이너, 보고서 빌더, Alert 시스템 | PM |
| **M5** | AI 통합 | 2027-03-28 | 3 | NLQ, 예측 모델, Auto Insight, MLOps | IT기획 |
| **M6** | SAP/SVC 연동 v1 | 2027-04-25 | 3 | SAP FI/CO/SD 커넥터, SVC API 연동 | 대표이사 |
| **M7** | 보안 + 관리 | 2027-05-30 | 4 | RBAC GUI, LDAP/SSO, 보안 감사, 성능 최적화 | IT기획 |
| **M8** | 통합 테스트 완료 | 2027-07-25 | 4 | E2E 테스트, 보안 점검, 성능 테스트 합격 | QA |
| **M9** | UAT 완료 | 2027-10-17 | 5 | 사용자 수용 테스트 통과, 결함 수정 | 현업 |
| **M10** | 파일럿 오픈 | 2028-02-27 | 5 | 20명 파일럿 운영, 피드백 수집 | PM |
| **M11** | Grand Open | 2028-03-31 | 5 | 전사 오픈, 교육 완료, 운영 이관 | **대표이사** |

### 마일스톤별 산출물

#### M0: 프로젝트 킥오프 (2026-04-07)
| 산출물 | 담당 |
|--------|:----:|
| 프로젝트 헌장 (PJT Charter) | PM |
| WBS + 일정표 (MS Project / Jira) | PM |
| 팀 구성 + R&R 확정 | PM |
| 개발 환경 구축 (서버 셋업, Git, Jira) | DevOps |
| 킥오프 미팅 회의록 | PM |

#### M1: 인프라 + DB 완료 (2026-05-30)
| 산출물 | 담당 |
|--------|:----:|
| 서버 환경 구축 완료 (Rocky Linux + Docker) | DevOps |
| PostgreSQL 16 설치 + Star Schema DDL 실행 | 데이터 |
| Redis 7 캐시 설정 | DevOps |
| Nginx 리버스 프록시 + SSL | DevOps |
| CI/CD 파이프라인 (GitHub Actions or Gitea CI) | DevOps |
| Grafana + Prometheus 모니터링 | DevOps |
| DB 스키마 문서 (ERD + 딕셔너리) | 데이터 |

#### M2: 기반 서비스 완료 (2026-07-25)
| 산출물 | 담당 |
|--------|:----:|
| Auth Service (JWT + RBAC + 토큰 갱신) | 백엔드 |
| Query Engine (SQL 빌더 + 시맨틱 레이어 API) | 백엔드+데이터 |
| 커넥터 프레임워크 (DB/REST/Excel 기본 3종) | 백엔드 |
| ETL 파이프라인 엔진 (스케줄러 포함) | 데이터 |
| 프론트엔드 프레임워크 (라우터, 상태관리, 공통 UI) | 프론트 |
| API 문서 (Swagger/OpenAPI) | 백엔드 |
| 디자인 시스템 가이드 (Figma) | UX |

#### M3: 노코드 코어 v1 (2026-10-31)
| 산출물 | 담당 | 비고 |
|--------|:----:|------|
| 데이터소스 커넥터 관리 UI | 프론트 | 마법사 형태 |
| SAP 커넥터 (RFC/OData) | 백엔드 | SAP 연동 테스트 |
| 시맨틱 레이어 편집기 UI | 프론트 | 필드/측정값 매핑 |
| KPI 계산 템플릿 엔진 | 백엔드 | 25종 기본 제공 |
| KPI 수식 편집기 UI | 프론트 | Excel-like |
| 차트 빌더 UI (15종 차트) | 프론트 | 드래그&드롭 |
| Analytics Engine API | 백엔드 | 집계/필터/정렬 |
| **★ 1차 데모** | PM | **대표이사 보고** |

#### M4: 대시보드 + 리포트 (2027-02-28)
| 산출물 | 담당 |
|--------|:----:|
| 대시보드 디자이너 (위젯 배치 + 필터 연동) | 프론트 |
| Dashboard Service (CRUD + 공유 + 권한) | 백엔드 |
| 보고서 디자이너 (WYSIWYG) | 프론트 |
| Report Service (PDF/Excel 생성 + 스케줄 발송) | 백엔드 |
| Alert 규칙 편집기 UI | 프론트 |
| Alert Service (모니터링 + 이메일/슬랙 발송) | 백엔드 |
| **★ 2차 데모** | PM | **현업 시연** |

#### M5: AI 통합 (2027-03-28)
| 산출물 | 담당 |
|--------|:----:|
| NLQ 엔진 (한국어 파싱 → SQL → 차트+답변) | AI |
| 예측 모델 3종 (매출/고장/이탈) | AI |
| Auto Insight (자동 변동 분석) | AI |
| AI 서비스 API (FastAPI) | AI+백엔드 |
| 모델 레지스트리 (MLflow or 자체) | AI+DevOps |

#### M6: SAP/SVC 연동 v1 (2027-04-25)
| 산출물 | 담당 |
|--------|:----:|
| SAP FI/CO 커넥터 (재무 데이터) | 백엔드 |
| SAP SD 커넥터 (영업 데이터) | 백엔드 |
| SAP MM/PP 커넥터 (구매/생산) | 백엔드 |
| SVC 시스템 API 연동 | 백엔드 |
| ETL 스케줄 (SAP → DB 주기적 동기화) | 데이터 |
| 데이터 정합성 검증 보고서 | QA |
| **★ 3차 데모** | PM | **대표이사 보고 (실 데이터)** |

#### M7: 보안 + 관리 (2027-05-30)
| 산출물 | 담당 |
|--------|:----:|
| Admin Service (사용자/역할 CRUD) | 백엔드 |
| RBAC 매트릭스 GUI | 프론트 |
| LDAP/SSO 연동 | 백엔드 |
| 감사 로그 (접속/변경 이력) | 백엔드 |
| 보안 감사 보고서 (OWASP Top 10) | 보안 |
| 성능 최적화 (쿼리/인덱스/캐시 튜닝) | 데이터+백엔드 |

#### M8: 통합 테스트 완료 (2027-07-25)
| 산출물 | 담당 |
|--------|:----:|
| E2E 테스트 수트 (Playwright) | QA |
| API 통합 테스트 | QA |
| 부하 테스트 (20명 동시 접속) | QA |
| 보안 침투 테스트 | 보안 |
| 테스트 결과 보고서 (합격 기준 충족) | QA |

#### M9: UAT 완료 (2027-10-17)
| 산출물 | 담당 |
|--------|:----:|
| UAT 시나리오 (부서별 핵심 업무 흐름) | PM+현업 |
| UAT 실행 결과 (합격/보류/불합격) | 현업 |
| 결함 수정 완료 (우선순위별) | 개발팀 |
| 사용자 매뉴얼 (온라인 도움말) | PM |

#### M10: 파일럿 오픈 (2028-02-27)
| 산출물 | 담당 |
|--------|:----:|
| 파일럿 대상자 20명 선정 + 교육 | PM |
| 파일럿 운영 (4주) | 전체 |
| 피드백 수집 + 개선 반영 | PM+개발 |
| 안정화 기간 (2주) | 전체 |

#### M11: Grand Open (2028-03-31)
| 산출물 | 담당 |
|--------|:----:|
| 전사 사용자 교육 (부서별 2시간) | PM |
| 운영 매뉴얼 | PM |
| 운영팀 이관 문서 (SLA, 에스컬레이션) | PM+DevOps |
| 최종 경영진 보고서 | PM |
| **★ Grand Open 행사** | **전체** |

---

### 의사결정 게이트 (Decision Gate)

| 게이트 | 시점 | 결정 사항 | 승인자 |
|:------:|------|----------|:------:|
| **G0** | 2026-04-07 | 프로젝트 착수 승인 | 대표이사 |
| **G1** | 2026-07-25 | 기반 서비스 검토 → Phase 2 진행 승인 | IT기획 |
| **G2** | 2026-10-31 | 노코드 코어 데모 → Phase 3 진행 + SAP 연동 범위 확정 | 대표이사 |
| **G3** | 2027-04-25 | 실 데이터 데모 → Phase 4 진행 + 전사 오픈 범위 결정 | 대표이사 |
| **G4** | 2027-07-25 | 테스트 합격 → UAT 진행 승인 | IT기획 |
| **G5** | 2027-10-17 | UAT 통과 → 파일럿 진행 승인 | 대표이사 |
| **G6** | 2028-03-31 | Grand Open 승인 + 운영 이관 | **대표이사** |

---

### 주요 데모/보고 일정

| 회차 | 시점 | 대상 | 내용 |
|:----:|------|------|------|
| 1차 데모 | 2026-10 (M3) | 대표이사 + IT기획 | 노코드 차트빌더 + KPI 편집기 시연 |
| 2차 데모 | 2027-02 (M4) | 대표이사 + 현업 팀장 | 대시보드 디자이너 + 보고서 빌더 시연 |
| 3차 데모 | 2027-04 (M6) | 대표이사 + 전 임원 | SAP 실 데이터 연동 + AI 인사이트 시연 |
| UAT 리뷰 | 2027-10 (M9) | 현업 부서장 | 업무 시나리오별 검증 결과 |
| Grand Open | 2028-03 (M11) | 전사 | 시스템 오픈 + 사용자 교육 |

---

### 리스크 대응 체크포인트

| 시점 | 리스크 체크 | 대응 |
|------|-----------|------|
| M1 (05월) | 서버 사양 충분한가? | 부하 테스트 → 스케일업 또는 서버 추가 |
| M3 (10월) | SAP 연동 가능한가? (기술 검증) | RFC/OData 테스트 → 불가 시 엑셀 병행 |
| M5 (03월) | AI 정확도 달성? | 정확도 70% 미달 시 규칙 기반으로 대체 |
| M6 (04월) | 데이터 정합성 OK? | 검증 실패 시 오픈 연기 + 데이터 클렌징 |
| M8 (07월) | 성능 합격? | 불합격 시 쿼리 최적화 + 캐싱 강화 |
| M10 (02월) | 사용자 반응? | 부정적 피드백 시 UI 재설계 (1개월 추가) |

---

## X. 투자 계획

### 1. 인력 투자

> **단가 기준**: 2026년 한국 SW 개발 외주 시장 기준 (중급~특급 기술사)  
> **일정 기준**: 버퍼 20% 포함, 실 작업 + 커뮤니케이션 + 이슈 대응 포함  
> **프로젝트 기간**: 24개월 (2년)

| 역할 | 등급 | 인원 | 기간(월) | 월 단가(만원) | 총 비용(만원) | 비고 |
|------|:----:|:----:|:-------:|:-----------:|:-----------:|------|
| IT기획자/PM | 특급 | 1 | 24 | 1,100 | 26,400 | 전체 기간 상주 |
| 아키텍처 전문가 | 특급 | 1 | 18 | 1,200 | 21,600 | Phase 1~3 |
| 백엔드 개발자 (시니어) | 고급 | 2 | 22 | 1,000 | 44,000 | 핵심 개발 |
| 백엔드 개발자 (중급) | 중급 | 1 | 20 | 750 | 15,000 | Phase 2~4 보조 |
| 프론트엔드 개발자 (시니어) | 고급 | 2 | 22 | 1,000 | 44,000 | 노코드 UI/차트빌더 |
| 프론트엔드 개발자 (중급) | 중급 | 1 | 18 | 750 | 13,500 | Phase 2~4 보조 |
| 데이터 엔지니어 | 고급 | 1 | 20 | 1,000 | 20,000 | DB/ETL/파이프라인 |
| AI/ML 엔지니어 | 고급 | 1 | 14 | 1,100 | 15,400 | Phase 3~4 (NLQ/예측) |
| DevOps 엔지니어 | 고급 | 1 | 12 | 1,000 | 12,000 | 인프라/CI/CD/모니터링 |
| QA 엔지니어 | 중급 | 1 | 16 | 750 | 12,000 | Phase 2~4 |
| UI/UX 디자이너 | 고급 | 1 | 8 | 900 | 7,200 | Phase 1~2 |
| **인력 합계** | | **13** | | | **231,100** | **약 23.1억** |

#### 단가 산정 근거 (2026년 기준)

| 등급 | 경력 | 월 단가 범위 | 적용 단가 |
|------|:----:|:----------:|:--------:|
| 특급 기술사 | 12년+ | 1,000~1,300만 | 1,100~1,200만 |
| 고급 기술사 | 8~12년 | 850~1,100만 | 1,000만 |
| 중급 기술사 | 4~8년 | 650~850만 | 750만 |

> ※ 한국소프트웨어산업협회(KOSA) SW 기술자 노임 단가 + 시장 할증 반영
> ※ 프리랜서/SI 외주 기준. 정규직 채용 시 복리후생 포함 130% 적용

#### 일정 산정 근거

| 항목 | 산정 |
|------|------|
| 실 개발 공수 | 16개월 |
| 커뮤니케이션/회의 | +20% (3.2개월) |
| 이슈 대응/리팩토링 | +15% (2.4개월) |
| 안정화/인수인계 | +10% (1.6개월) |
| **총 프로젝트 기간** | **24개월 (버퍼 포함)** |

### 2. 인프라 — 자체 서버 (온프레미스)

> **실 사용자**: 대시보드 20명 미만, 동시 접속 5~10명  
> **서버**: 기존 보유 서버 활용 (신규 구매 불필요)

#### 서버 사양

| 항목 | 사양 |
|------|------|
| CPU | Intel Xeon Silver (16 Core) |
| 메모리 | 64GB DDR4 |
| OS 디스크 | 480GB SSD × 2 (RAID 1, 미러링) |
| 데이터 디스크 | 960GB SSD × 3 (RAID 5, 가용 1.92TB) |
| OS | Rocky Linux 9.x |
| 비고 | **기존 보유 서버 — 추가 구매 비용 없음** |

#### 서버 내 서비스 배치

```
┌─ 서버 (Xeon Silver 16Core / 64GB / Rocky Linux 9) ──────────────┐
│                                                                   │
│  ┌─ Docker Compose ───────────────────────────────────────────┐   │
│  │                                                             │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐  │   │
│  │  │  Nginx       │  │  Node.js     │  │  Node.js         │  │   │
│  │  │  (리버스     │  │  (프론트엔드) │  │  (API 서버)      │  │   │
│  │  │   프록시)    │  │  port 3000   │  │  port 3001       │  │   │
│  │  │  port 80/443│  │  Vite SSR    │  │  Express         │  │   │
│  │  └──────────────┘  └──────────────┘  └──────────────────┘  │   │
│  │                                                             │   │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐  │   │
│  │  │  PostgreSQL  │  │  Redis       │  │  Python          │  │   │
│  │  │  16         │  │  7.x         │  │  (AI Service)    │  │   │
│  │  │  port 5432  │  │  port 6379   │  │  FastAPI         │  │   │
│  │  │  Data: RAID5│  │  캐시 2GB    │  │  port 8000       │  │   │
│  │  └──────────────┘  └──────────────┘  └──────────────────┘  │   │
│  │                                                             │   │
│  │  ┌──────────────┐  ┌──────────────┐                        │   │
│  │  │  Grafana     │  │  Prometheus  │                        │   │
│  │  │  (모니터링)  │  │  (메트릭)    │                        │   │
│  │  │  port 3100  │  │  port 9090   │                        │   │
│  │  └──────────────┘  └──────────────┘                        │   │
│  └─────────────────────────────────────────────────────────────┘   │
│                                                                   │
│  디스크 배치:                                                      │
│  /dev/sda (RAID1, 480G×2) → / (OS + Docker + 앱)                 │
│  /dev/sdb (RAID5, 960G×3) → /data (PostgreSQL + Redis + 백업)    │
└───────────────────────────────────────────────────────────────────┘
```

#### 리소스 할당 (64GB 메모리 기준)

| 서비스 | CPU | 메모리 | 디스크 |
|--------|:---:|:------:|:------:|
| PostgreSQL | 4코어 | 16GB | RAID5 (1.5TB) |
| Node.js (프론트+API) | 4코어 | 8GB | OS 디스크 |
| Redis | 1코어 | 2GB | OS 디스크 |
| Python AI Service | 2코어 | 8GB | OS 디스크 |
| Nginx | 1코어 | 1GB | OS 디스크 |
| Grafana + Prometheus | 1코어 | 2GB | OS 디스크 |
| OS + 여유 | 3코어 | 27GB | - |
| **합계** | **16코어** | **64GB** | **~2TB** |

#### 인프라 비용

| 항목 | 비용 | 비고 |
|------|:----:|------|
| 서버 하드웨어 | **₩0** | 기존 보유 장비 활용 |
| Rocky Linux | **₩0** | 오픈소스 (무료) |
| Docker / Docker Compose | **₩0** | 오픈소스 (무료) |
| 전력/네트워크 | 월 5만원 | 사내 IDC/서버실 |
| **인프라 합계 (연간)** | **₩60만** | |

### 3. 라이선스 비용 — 전액 무료 (100% 오픈소스)

> **원칙**: 상용 라이선스 없이 전체 시스템 구성

| 항목 | 용도 | 라이선스 | 비용 |
|------|------|---------|:----:|
| **Rocky Linux 9** | 서버 OS | BSD | ₩0 |
| **PostgreSQL 16** | RDBMS | PostgreSQL License | ₩0 |
| **Redis 7** | 캐시/세션 | BSD | ₩0 |
| **Node.js 22** | 런타임 | MIT | ₩0 |
| **React 19** | UI 프레임워크 | MIT | ₩0 |
| **Vite 6** | 빌드 도구 | MIT | ₩0 |
| **TypeScript 5** | 언어 | Apache 2.0 | ₩0 |
| **ECharts 6** | 차트 라이브러리 | Apache 2.0 | ₩0 |
| **Tailwind CSS 4** | 스타일 | MIT | ₩0 |
| **Express 4** | API 프레임워크 | MIT | ₩0 |
| **Drizzle ORM** | DB ORM | Apache 2.0 | ₩0 |
| **Nginx** | 웹서버/프록시 | BSD | ₩0 |
| **Docker** | 컨테이너 | Apache 2.0 | ₩0 |
| **Grafana OSS** | 모니터링 | AGPL 3.0 | ₩0 |
| **Prometheus** | 메트릭 수집 | Apache 2.0 | ₩0 |
| **Python 3.12** | AI 서비스 | PSF License | ₩0 |
| **FastAPI** | AI API | MIT | ₩0 |
| **Prophet** | 시계열 예측 | MIT | ₩0 |
| **scikit-learn** | ML | BSD | ₩0 |
| **jsPDF** | PDF 생성 | MIT | ₩0 |
| **SheetJS (xlsx)** | 엑셀 처리 | Apache 2.0 | ₩0 |
| **react-grid-layout** | 드래그&드롭 | MIT | ₩0 |
| **Lucide React** | 아이콘 | ISC | ₩0 |
| **Let's Encrypt** | SSL 인증서 | 무료 | ₩0 |
| **Git (자체 Gitea)** | 소스 관리 | MIT | ₩0 |
| **───────────** | | | |
| **라이선스 합계** | | | **₩0** |

#### AI 서비스 비용 (선택사항)

| 항목 | 옵션 | 비용 | 비고 |
|------|------|:----:|------|
| Claude API (NLQ) | **옵션A: 자체 NLQ 엔진** | ₩0 | 현재 규칙 기반 파서 (구현 완료) |
| | **옵션B: Claude API** | 월 5~10만원 | 20명 × 월 20회 = 400건/월 |
| | **옵션C: 로컬 LLM (Ollama)** | ₩0 | 서버에 7B 모델 배포 (16코어면 가능) |

> **권장**: 옵션A(자체 규칙 기반)로 시작 → 고도화 시 옵션C(로컬 LLM) 검토

### 4. 투자 총합 — 실 환경 기준

| 구분 | 금액 | 비고 |
|------|:----:|------|
| 개발 인력 (1회성, 24개월) | **23.1억** | 13명, 버퍼 20% 포함 |
| 서버 하드웨어 | **₩0** | 기존 보유 |
| 인프라 운영 (연간) | **₩60만** | 전력/네트워크만 |
| 라이선스 (연간) | **₩0** | 100% 오픈소스 |
| AI API (선택, 연간) | **₩0~120만** | 자체 or Claude |
| **초기 투자** | **23.1억** | **인력 비용만** |
| **연간 운영 비용** | **₩60~180만** | **사실상 무료** |

> 참고: 외주 대신 **정규직 3~4명 + Claude Code 활용** 시 인건비 대폭 절감 가능
> 현재 POC는 개발자 1명 + Claude Code로 2일 만에 구축한 사례

#### 비용 비교 (vs 상용 BI 도구)

| 비교 항목 | Power BI (20명) | Tableau (20명) | **GST NAVI BI** |
|----------|:--------------:|:-------------:|:---------------:|
| 라이선스 (연) | 3,600만 | 5,400만 | **₩0** |
| 인프라 (연) | 클라우드 포함 | 클라우드 포함 | **₩60만** |
| 커스터마이징 | 제한적 | 제한적 | **무제한** |
| 제조업 특화 | ❌ | ❌ | **✅** |
| 한국어 AI | △ | ❌ | **✅** |
| 판관비 자동배부 | ❌ | ❌ | **✅** |
| 3년 TCO | **1.08억+** | **1.62억+** | **₩180만** |

---

## XI. 기대 효과 및 ROI

### 1. 정량적 기대 효과

| 효과 항목 | 현재 | 목표 | 연간 절감 효과 | 산출 근거 |
|----------|:----:|:----:|:------------:|----------|
| 보고서 수작업 제거 | 관리직 300명 × 주 2회 × 2시간 | 자동화 90% | **5.6억원/년** | 300명 × 4H/주 × 48주 × 5만원 × 90% |
| 데이터 분석 리드타임 | 요청→분석→보고 2주 | 30분 (셀프서비스) | **1.2억원/년** | 분석 인력 3명 × 연 40건 × 100만원 절감 |
| 의사결정 속도 향상 | 월 1회 정기 보고 | 실시간 대시보드 | 기회비용 **2.0억원** | 납기 1일 단축 → 매출 조기인식 |
| ERP 리포트 개발 비용 | SAP 커스텀 보고서 | BI 자체 제작 | **0.8억원/년** | 외주 개발 10건 × 800만원 |
| 외부 BI 도구 불필요 | Power BI 등 검토 중 | 자체 BI | **0.3억원/년** | Pro 라이선스 × 50명 불필요 |
| 예지보전 비용 절감 | 장애콜 58건/월 | -30% 감소 | **4.5억원/년** | 출장비+부품비+다운타임 절감 |
| **합계** | | | **14.4억원/년** | |

> **투자 회수 기간**: 초기 투자 23.1억 ÷ 연간 효과 14.4억 = **약 19개월 (1년 7개월)**
> 
> ※ 정규직 + Claude Code 방식 시: 투자 약 8~10억 → 회수 약 8개월

### 2. 정성적 기대 효과

```
1. 데이터 민주화
   • 전 임직원이 데이터에 접근하고 스스로 분석 가능
   • IT 부서 의존도 해소: 차트 추가 요청 → 직접 생성

2. 신속한 의사결정
   • 실시간 KPI 모니터링으로 문제 조기 감지
   • AI 자연어 질의: "매출이 왜 떨어졌나?" → 30초 내 원인 파악

3. 운영 효율화
   • 월간 보고서 자동 생성 및 배포 (주 1회 → 완전 자동)
   • KPI 임계값 알림으로 선제적 대응

4. 제조업 최적화
   • OEE, 예지보전, 불량률 실시간 모니터링
   • 장비 고장 예측으로 예방보전 전환 → 긴급수리 비용 절감

5. 경쟁력 확보
   • 데이터 기반 경영 문화 정착
   • AI 인사이트로 전략 수립 고도화
```

### 3. ROI 계산

```
투자 대비 수익률 (3년 기준)

초기 투자: 118,700만원

연간 절감 효과:
  보고서 자동화:          900,000만원/년
  ERP 커스터마이징:       10,000만원/년
  외부 BI 도구:           3,000만원/년
  ────────────────────
  총 절감:               913,000만원/년 (91.3억원/년)

연간 운영 비용: 10,650만원/년

3년 순 절감 효과:
  = (91.3억 - 1.065억) × 3 - 118,700만원
  = 90.235억 × 3 - 11.87억
  = 270.705억 - 11.87억
  = 258.835억원 (3년 누적)

ROI (3년) = 258.835억 / 11.87억 × 100 = 2,180%
투자 회수 기간 = 118,700만원 / (91.3억 - 1.065억) = 1.3개월
```

> **주**: 보고서 자동화 절감 효과는 데이터 분석 인력 인건비 절감 및 의사결정 속도 향상에 따른 기회 비용 절감을 포함한 보수적 추정치입니다.

---

## 부록 A. 환경변수 전체 목록

```env
# === 공통 ===
NODE_ENV=production
TZ=Asia/Seoul

# === 데이터베이스 ===
DATABASE_URL=postgresql://bi_user:PASSWORD@postgres:5432/gst_navi
CLICKHOUSE_URL=http://clickhouse:8123
CLICKHOUSE_USER=bi_reader
CLICKHOUSE_PASS=PASSWORD
CLICKHOUSE_DB=gst_analytics

# === 캐시 ===
REDIS_URL=redis://redis:6379

# === 파일 저장 ===
MINIO_ENDPOINT=minio:9000
MINIO_ACCESS_KEY=ACCESS_KEY
MINIO_SECRET_KEY=SECRET_KEY
MINIO_BUCKET_REPORTS=reports
MINIO_BUCKET_UPLOADS=uploads

# === 인증 ===
JWT_ACCESS_SECRET=64자이상_랜덤_시크릿
JWT_REFRESH_SECRET=64자이상_랜덤_시크릿
JWT_ACCESS_EXPIRE=15m
JWT_REFRESH_EXPIRE=7d
ENCRYPTION_KEY=32바이트_AES_키_BASE64

# === LDAP/SSO ===
LDAP_URL=ldap://corp.gst.co.kr
LDAP_BASE_DN=DC=gst,DC=co,DC=kr
LDAP_BIND_DN=CN=bi_service,OU=Service,DC=gst,DC=co,DC=kr
LDAP_BIND_PASS=PASSWORD
SAML_SP_ENTITY_ID=https://gst-navi.co.kr
SAML_IDP_METADATA_URL=https://sso.gst.co.kr/metadata

# === AI ===
ANTHROPIC_API_KEY=sk-ant-...
ANTHROPIC_MODEL=claude-3-5-sonnet-20241022
ANTHROPIC_MAX_TOKENS=4096
MLFLOW_TRACKING_URI=http://mlflow:5000

# === 이메일 ===
SMTP_HOST=smtp.gst.co.kr
SMTP_PORT=587
SMTP_USER=noreply@gst.co.kr
SMTP_PASS=PASSWORD
SMTP_FROM=GST NAVI <noreply@gst.co.kr>

# === 슬랙 ===
SLACK_WEBHOOK_URL=https://hooks.slack.com/services/...
SLACK_BOT_TOKEN=xoxb-...

# === 모니터링 ===
PROMETHEUS_PORT=9090
SENTRY_DSN=https://...@sentry.io/...

# === 보안 ===
ALLOWED_ORIGINS=https://gst-navi.co.kr,https://bi.gst.co.kr
RATE_LIMIT_WINDOW_MS=60000
RATE_LIMIT_MAX=100
```

---

## 부록 B. 디렉토리 구조

```
gst-navi-bi/
├── frontend/                    # React 19 SPA
│   ├── src/
│   │   ├── components/          # 공통 컴포넌트
│   │   │   ├── charts/          # ECharts 래퍼 (15종)
│   │   │   ├── dashboard/       # 대시보드 디자이너
│   │   │   ├── report/          # 보고서 디자이너
│   │   │   ├── chart-builder/   # 차트 빌더
│   │   │   ├── semantic/        # 시맨틱 레이어 편집기
│   │   │   ├── connector/       # 커넥터 마법사
│   │   │   └── nlq/             # 자연어 질의 UI
│   │   ├── pages/               # 라우트 페이지
│   │   ├── stores/              # Zustand 스토어
│   │   ├── hooks/               # 커스텀 훅
│   │   ├── api/                 # API 클라이언트
│   │   └── types/               # TypeScript 타입
│   └── public/
│
├── services/                    # 마이크로서비스
│   ├── auth/                    # Auth Service (Node.js)
│   ├── dashboard/               # Dashboard Service
│   ├── analytics/               # Analytics Engine
│   ├── query/                   # Query Engine
│   ├── report/                  # Report Service
│   ├── alert/                   # Alert Service
│   ├── ai/                      # AI Service (Python)
│   │   ├── app/
│   │   │   ├── nlq/             # NLQ 처리
│   │   │   ├── forecast/        # 예측 모델
│   │   │   ├── insight/         # 인사이트 생성
│   │   │   └── mlops/           # MLflow 연동
│   │   └── models/              # 학습된 모델 파일
│   ├── connector/               # Connector Service
│   └── admin/                   # Admin Service
│
├── db/
│   ├── migrations/              # DB 마이그레이션 (Flyway)
│   ├── seeds/                   # 초기 데이터
│   └── init/                    # Docker 초기화 스크립트
│
├── k8s/                         # Kubernetes 매니페스트
│   ├── base/
│   ├── staging/
│   └── production/
│
├── nginx/                       # nginx 설정
├── monitoring/                  # Prometheus/Grafana 설정
├── .github/workflows/           # CI/CD
└── docs/                        # 개발 문서
    ├── api/                     # OpenAPI 3.0 스펙
    └── architecture/            # 아키텍처 다이어그램
```

---

## 부록 C. 주요 리스크 및 대응 방안

| 리스크 | 발생 가능성 | 영향도 | 대응 방안 |
|--------|:---------:|:-----:|---------|
| SAP 연동 복잡도 초과 | 높음 | 높음 | RFC 방식 fallback, SAP BASIS 팀 협력, PoC 선행 |
| ClickHouse 운영 인력 부족 | 중간 | 중간 | PostgreSQL TimescaleDB로 대체 가능 |
| Claude API 비용 초과 | 중간 | 중간 | 캐싱(Redis TTL), 요약 컨텍스트 최적화, 사용량 모니터링 |
| 사용자 변화 저항 | 높음 | 중간 | 단계적 오픈, 현업 챔피언 육성, UI/UX 집중 |
| 데이터 품질 이슈 | 높음 | 높음 | ETL 검증 규칙 강화, 데이터 프로파일링, 현업 확인 |
| 보안 취약점 | 낮음 | 높음 | 정기 침투 테스트, OWASP Top 10 점검, 코드 리뷰 |
| 일정 지연 | 중간 | 중간 | MoSCoW 우선순위화, Phase 분리, 스프린트 리뷰 |

---

*문서 끝 — GST NAVI BI 플랫폼 기술 설계서 v1.0*  
*작성: IT기획 / 아키텍처 / 데이터 / AI / 프론트엔드 / 백엔드 전문가 6인 협업*  
*2026-04-01*
