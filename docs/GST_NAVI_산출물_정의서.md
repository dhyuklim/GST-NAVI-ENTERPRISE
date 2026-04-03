# GST NAVI — 산출물 정의서 및 체크리스트

**작성일**: 2026-04-03  
**목표**: 각 Phase별 산출물 명확히 정의 및 생성 자동화  
**산출물 저장소**: `/OUTPUT` 디렉토리

---

## I. 산출물 분류 체계

### 산출물 타입별 분류

| 타입 | 설명 | 예시 |
|------|------|------|
| **설계 문서** | 아키텍처, 스키마, 인터페이스 설계서 | ERD, API 명세, DDL |
| **기능 산출물** | 구현 코드, 컴포넌트 | React 컴포넌트, API 엔드포인트 |
| **데이터 산출물** | 엑셀, CSV, JSON 양식 | 엑셀 템플릿, 샘플 데이터 |
| **테스트 산출물** | 테스트 케이스, 검증 리포트 | Unit Test, E2E Test |
| **배포 산출물** | 배포 스크립트, 문서 | Docker, CI/CD 설정 |
| **회의 산출물** | 회의록, 요구사항 정의서 | 회의록, BRD, SRS |

---

## II. Phase별 산출물 목록

### Phase 0: 즉시 실행 (W0: 4/3~4/4)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 0-1 | 로컬 변경 4건 병합 코드 | Frontend/UI | 기능 | `OUTPUT/P0/code/` | Dashboard/ServiceDashboard/ProductionDashboard 수정 완료 |
| 0-1 | Vercel 배포 완료 보고서 | Frontend/UI | 배포 | `OUTPUT/P0/deployment/` | 배포 URL + 스크린샷 |
| 0-2 | JWT 환경변수 설정 문서 | Infrastructure | 설계 | `OUTPUT/P0/security/` | `.env.local` + `.env.example` |
| 0-2 | 보안 설정 체크리스트 | Infrastructure | 배포 | `OUTPUT/P0/security/` | 모든 항목 체크 완료 |
| 0-3 | API 인증 미들웨어 코드 | Backend/API | 기능 | `OUTPUT/P0/code/middleware/` | `auth.ts` 파일 |
| 0-3 | API 인증 테스트 결과 | QA | 테스트 | `OUTPUT/P0/tests/` | 테스트 케이스 + 결과 |
| 0-4 | 통합 개발 계획 최종본 | PM | 설계 | `OUTPUT/P0/docs/` | README.md + 개발 가이드 |
| 0-4 | 문서 구조도 | PM | 설계 | `OUTPUT/P0/docs/` | 문서 링크 맵 |

**총 산출물**: 8개  
**예상 크기**: ~50MB  
**완료 기한**: 2026-04-04

---

### Phase 1: 대표이사 지시 반영 (W1~W2: 4/7~4/18)

#### Week 1 (4/7~4/11)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 1-1 | CommentPanel 컴포넌트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | React 컴포넌트 + Storybook |
| 1-1 | localStorage 관리 유틸 | Frontend/UI | 기능 | `OUTPUT/P1/code/utils/` | comment.ts CRUD 함수 |
| 1-1 | 멘트 기능 테스트 결과 | QA | 테스트 | `OUTPUT/P1/tests/` | Unit + E2E 테스트 |
| 1-2 | 원가 구성비 계산 로직 | Backend/API | 기능 | `OUTPUT/P1/code/services/` | `costAnalysis.ts` |
| 1-2 | 누적 바 차트 컴포넌트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | CostChart.tsx |
| 1-2 | 원가 데이터 예시 | Data/ETL | 데이터 | `OUTPUT/P1/data/` | sample_cost_data.json |
| 1-3 | 라인별 드릴다운 모달 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | LineDetailModal.tsx |
| 1-3 | 6개월 추이 차트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | CapacityTrendChart.tsx |
| 1-3 | 비가동 사유 분석 차트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | DowntimeAnalysisChart.tsx |
| 1-4 | 생산성 추이 차트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | ProductivityTrendChart.tsx |
| 1-4 | HR 데이터 매핑 | Data/ETL | 데이터 | `OUTPUT/P1/data/` | hr_productivity_mapping.json |

**소계 W1**: 11개 산출물

#### Week 2 (4/14~4/18)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 1-5 | 법인별 카드 UI 컴포넌트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | CorporationCard.tsx |
| 1-5 | 장비 목록 테이블 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | EquipmentTable.tsx |
| 1-5 | 워런티 타임라인 차트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | WarrantyTimeline.tsx |
| 1-5 | 인력 배치 표시 컴포넌트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | TechnicianCoverage.tsx |
| 1-6 | 기간 비교 모드 토글 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | ComparisonModeToggle.tsx |
| 1-6 | 나란히 바 차트 컴포넌트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | SideBySideBarChart.tsx |
| 1-6 | 성장률 계산 유틸 | Frontend/UI | 기능 | `OUTPUT/P1/code/utils/` | growthRate.ts |
| 1-7 | DataSourceBadge 컴포넌트 | Frontend/UI | 기능 | `OUTPUT/P1/code/components/` | DataSourceBadge.tsx |
| 1-7 | 메타데이터 구조 정의 | Backend/API | 설계 | `OUTPUT/P1/docs/` | metadata_schema.json |
| 1-x | Phase 1 데모 영상 | Frontend/UI | 배포 | `OUTPUT/P1/demo/` | Demo_P1_2026-04-18.mp4 |
| 1-x | Phase 1 완료 보고서 | PM | 회의 | `OUTPUT/P1/reports/` | Completion_Report_P1.md |

**소계 W2**: 11개 산출물

**총 Phase 1**: 22개 산출물  
**예상 크기**: ~200MB  
**완료 기한**: 2026-04-18

---

### Phase 2: 생산 TF 반영 + 데이터 확장 (W3~W6: 4/21~5/16)

#### Week 3~4 (4/21~5/2)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 2-1 | TF 캐파 정의서 | PM | 회의 | `OUTPUT/P2/tf/` | TF_Capacity_Definition_v1.xlsx |
| 2-1 | 캐파 정의 API 스펙 | Backend/API | 설계 | `OUTPUT/P2/api/` | capacity_api_spec.md |
| 2-1 | 캐파 정의 API 코드 | Backend/API | 기능 | `OUTPUT/P2/code/api/` | capacityRoutes.ts |
| 2-2 | TF 생산성 지표 정의서 | PM | 회의 | `OUTPUT/P2/tf/` | TF_Productivity_Definition_v1.xlsx |
| 2-2 | 생산성 계산 로직 | Backend/API | 기능 | `OUTPUT/P2/code/services/` | productivityCalculation.ts |
| 2-2 | 생산성 테스트 결과 | QA | 테스트 | `OUTPUT/P2/tests/` | productivity_test_results.json |
| 2-3 | 생산 엑셀 양식 | Data/ETL | 데이터 | `OUTPUT/P2/templates/` | Production_Template.xlsx |
| 2-3 | 엑셀 업로드 UI 코드 | Frontend/UI | 기능 | `OUTPUT/P2/code/components/` | ExcelUploadForm.tsx |
| 2-3 | 엑셀 파싱 유틸 | Data/ETL | 기능 | `OUTPUT/P2/code/utils/` | excelParser.ts |
| 2-4 | 생산 대시보드 수정 코드 | Frontend/UI | 기능 | `OUTPUT/P2/code/dashboards/` | ProductionDashboard_updated.tsx |

**소계 W3~4**: 10개 산출물

#### Week 5~6 (5/5~5/16)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 2-5 | 구매 엑셀 양식 | Data/ETL | 데이터 | `OUTPUT/P2/templates/` | Procurement_Template.xlsx |
| 2-6 | SVC 엑셀 양식 | Data/ETL | 데이터 | `OUTPUT/P2/templates/` | Service_Template.xlsx |
| 2-7 | 품질 엑셀 양식 | Data/ETL | 데이터 | `OUTPUT/P2/templates/` | Quality_Template.xlsx |
| 2-8 | 다중 엑셀 업로드 API | Backend/API | 기능 | `OUTPUT/P2/code/api/` | multiExcelUploadRoutes.ts |
| 2-8 | 다중 업로드 UI 컴포넌트 | Frontend/UI | 기능 | `OUTPUT/P2/code/components/` | MultiExcelUploadPanel.tsx |
| 2-8 | 업로드 상태 관리 Context | Frontend/UI | 기능 | `OUTPUT/P2/code/contexts/` | UploadContext.tsx |
| 2-9 | 검증 규칙 정의서 | QA | 설계 | `OUTPUT/P2/docs/` | Validation_Rules.md |
| 2-9 | 검증 로직 코드 | Backend/API | 기능 | `OUTPUT/P2/code/services/` | dataValidation.ts |
| 2-9 | 검증 리포트 생성기 | QA | 기능 | `OUTPUT/P2/code/services/` | validationReportGenerator.ts |
| 2-9 | 샘플 검증 리포트 | QA | 테스트 | `OUTPUT/P2/reports/` | Sample_Validation_Report.html |
| 2-x | Phase 2 완료 보고서 | PM | 회의 | `OUTPUT/P2/reports/` | Completion_Report_P2.md |

**소계 W5~6**: 11개 산출물

**총 Phase 2**: 21개 산출물  
**예상 크기**: ~300MB  
**완료 기한**: 2026-05-16

---

### Phase 3: 시스템 연동 설계 (W7~W14: 5/19~7/11)

#### Week 7~10 (5/19~6/13)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 3-1 | PostgreSQL ERD | Backend/API | 설계 | `OUTPUT/P3/database/` | erd.svg + erd.png |
| 3-1 | 스키마 DDL 스크립트 | Backend/API | 설계 | `OUTPUT/P3/database/` | schema_001_initial.sql |
| 3-1 | 마이그레이션 계획서 | Backend/API | 설계 | `OUTPUT/P3/database/` | Migration_Plan.md |
| 3-2 | Drizzle 스키마 정의 | Backend/API | 기능 | `OUTPUT/P3/code/schema/` | schema.ts |
| 3-2 | 마이그레이션 파일 | Backend/API | 배포 | `OUTPUT/P3/migrations/` | 001_initial_schema.sql |
| 3-2 | 마이그레이션 실행 로그 | Backend/API | 테스트 | `OUTPUT/P3/reports/` | Migration_Execution_Log.md |
| 3-3 | API 리팩토링 코드 | Backend/API | 기능 | `OUTPUT/P3/code/api/` | refactored_routes_*.ts |
| 3-3 | DB 쿼리 함수 라이브러리 | Backend/API | 기능 | `OUTPUT/P3/code/queries/` | queries.ts |
| 3-3 | API 성능 테스트 결과 | QA | 테스트 | `OUTPUT/P3/reports/` | API_Performance_Test.json |
| 3-4 | ETL 파이프라인 코드 | Backend/API | 기능 | `OUTPUT/P3/code/etl/` | excelToPipeline.ts |

**소계 W7~10**: 10개 산출물

#### Week 11~14 (6/16~7/11)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 3-5 | SAP FI 인터페이스 명세 | Backend/API | 설계 | `OUTPUT/P3/specs/` | SAP_FI_Interface_Spec.md |
| 3-5 | SAP CO 인터페이스 명세 | Backend/API | 설계 | `OUTPUT/P3/specs/` | SAP_CO_Interface_Spec.md |
| 3-5 | SAP SD 인터페이스 명세 | Backend/API | 설계 | `OUTPUT/P3/specs/` | SAP_SD_Interface_Spec.md |
| 3-5 | SAP MM 인터페이스 명세 | Backend/API | 설계 | `OUTPUT/P3/specs/` | SAP_MM_Interface_Spec.md |
| 3-5 | SAP PP 인터페이스 명세 | Backend/API | 설계 | `OUTPUT/P3/specs/` | SAP_PP_Interface_Spec.md |
| 3-6 | SVC API 명세서 | Backend/API | 설계 | `OUTPUT/P3/specs/` | SVC_API_Spec.md |
| 3-7 | Salesforce 연동 명세 | Backend/API | 설계 | `OUTPUT/P3/specs/` | Salesforce_Integration_Spec.md |
| 3-8 | ETL 아키텍처 설계서 | Backend/API | 설계 | `OUTPUT/P3/etl/` | ETL_Architecture_Design.md |
| 3-8 | ETL DAG 정의 | Backend/API | 설계 | `OUTPUT/P3/etl/` | etl_dags.yaml |
| 3-9 | BOM 기반 원가 설계서 | Backend/API | 설계 | `OUTPUT/P3/docs/` | BOM_Cost_Design.md |
| 3-9 | BOM 테이블 스키마 | Backend/API | 설계 | `OUTPUT/P3/database/` | bom_tables.sql |
| 3-x | Phase 3 설계 완료 보고서 | PM | 회의 | `OUTPUT/P3/reports/` | Completion_Report_P3.md |

**소계 W11~14**: 12개 산출물

**총 Phase 3**: 22개 산출물  
**예상 크기**: ~500MB  
**완료 기한**: 2026-07-11

---

### Phase 4: AI/예측 + 전사 확산 (W15~W26: 7/14~10/3)

#### Week 15~18 (7/14~8/8)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 4-1 | SAP FI/SD 연동 코드 | Backend/API | 기능 | `OUTPUT/P4/integrations/` | sapFinancialIntegration.ts |
| 4-1 | SAP 데이터 매핑 변환기 | Backend/API | 기능 | `OUTPUT/P4/integrations/` | sapDataTransformer.ts |
| 4-2 | MES 연동 코드 | Backend/API | 기능 | `OUTPUT/P4/integrations/` | mesIntegration.ts |
| 4-2 | MES 실시간 수신 핸들러 | Backend/API | 기능 | `OUTPUT/P4/integrations/` | mesRealtimeHandler.ts |
| 4-3 | SVC API 연동 코드 | Backend/API | 기능 | `OUTPUT/P4/integrations/` | svcIntegration.ts |
| 4-3 | 실 데이터 대시보드 업데이트 | Frontend/UI | 기능 | `OUTPUT/P4/code/dashboards/` | LiveDataDashboards_updated.tsx |
| 4-x | 시스템 연동 E2E 테스트 | QA | 테스트 | `OUTPUT/P4/tests/` | integration_e2e_tests.ts |

**소계 W15~18**: 7개 산출물

#### Week 19~22 (8/11~9/5)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 4-4 | 매출 예측 모델 (Prophet) | AI/Analytics | 기능 | `OUTPUT/P4/ai_models/` | sales_forecast_prophet.py |
| 4-4 | 시계열 분석 노트북 | AI/Analytics | 기능 | `OUTPUT/P4/ai_notebooks/` | TimeSeries_Analysis.ipynb |
| 4-5 | 이상 탐지 모델 | AI/Analytics | 기능 | `OUTPUT/P4/ai_models/` | anomaly_detection.py |
| 4-5 | KPI Alert 규칙 정의 | AI/Analytics | 설계 | `OUTPUT/P4/ai_config/` | kpi_alert_rules.json |
| 4-6 | Claude API 연동 코드 | AI/Analytics | 기능 | `OUTPUT/P4/code/ai/` | claudeNLQHandler.ts |
| 4-6 | NLQ 프롬프트 템플릿 | AI/Analytics | 설계 | `OUTPUT/P4/ai_config/` | nlq_prompts.md |
| 4-6 | NLQ 테스트 결과 | QA | 테스트 | `OUTPUT/P4/tests/` | nlq_test_results.md |

**소계 W19~22**: 7개 산출물

#### Week 23~26 (9/8~10/3)

| # | 산출물명 | 담당 | 타입 | 저장위치 | 완료조건 |
|---|---------|------|------|---------|---------|
| 4-7 | 월간 보고서 생성 코드 | AI/Analytics | 기능 | `OUTPUT/P4/code/reports/` | monthlyReportGenerator.ts |
| 4-7 | 보고서 템플릿 | AI/Analytics | 설계 | `OUTPUT/P4/templates/` | monthly_report_template.html |
| 4-8 | PWA 매니페스트 | Frontend/UI | 배포 | `OUTPUT/P4/pwa/` | manifest.json |
| 4-8 | Service Worker 코드 | Frontend/UI | 기능 | `OUTPUT/P4/code/pwa/` | serviceWorker.ts |
| 4-8 | 오프라인 지원 로직 | Frontend/UI | 기능 | `OUTPUT/P4/code/utils/` | offlineSync.ts |
| 4-x | 전체 회귀 테스트 결과 | QA | 테스트 | `OUTPUT/P4/tests/` | Full_Regression_Test.md |
| 4-x | 성능 최적화 보고서 | Infrastructure | 배포 | `OUTPUT/P4/reports/` | Performance_Optimization_Report.md |
| 4-x | Phase 4 최종 완료 보고서 | PM | 회의 | `OUTPUT/P4/reports/` | Final_Completion_Report_P4.md |

**소계 W23~26**: 8개 산출물

**총 Phase 4**: 22개 산출물  
**예상 크기**: ~700MB  
**완료 기한**: 2026-10-03

---

## III. 산출물 저장소 구조

```
OUTPUT/
├── P0_즉시실행/
│   ├── code/
│   ├── deployment/
│   ├── security/
│   ├── tests/
│   └── docs/
│
├── P1_대표지시반영/
│   ├── code/
│   │   ├── components/
│   │   ├── utils/
│   │   └── services/
│   ├── data/
│   ├── tests/
│   ├── demo/
│   └── reports/
│
├── P2_생산TF반영/
│   ├── code/
│   │   ├── api/
│   │   ├── components/
│   │   ├── utils/
│   │   ├── services/
│   │   ├── dashboards/
│   │   └── contexts/
│   ├── templates/
│   ├── tf/
│   ├── tests/
│   ├── reports/
│   └── docs/
│
├── P3_시스템연동설계/
│   ├── code/
│   │   ├── api/
│   │   ├── schema/
│   │   ├── queries/
│   │   ├── etl/
│   │   └── services/
│   ├── database/
│   ├── migrations/
│   ├── specs/
│   ├── etl/
│   ├── docs/
│   ├── tests/
│   └── reports/
│
├── P4_AI예측확산/
│   ├── code/
│   │   ├── ai/
│   │   ├── dashboards/
│   │   ├── reports/
│   │   ├── pwa/
│   │   └── utils/
│   ├── ai_models/
│   ├── ai_notebooks/
│   ├── ai_config/
│   ├── integrations/
│   ├── templates/
│   ├── pwa/
│   ├── tests/
│   └── reports/
│
└── README.md (산출물 가이드)
```

---

## IV. 산출물 생성 에이전트 명령어 (자동화)

### 산출물 에이전트 역할

**🤖 Output Management Agent**
- 매주 Phase 진행 상황 모니터링
- 완료된 산출물 자동 수집 및 정렬
- 미완료 산출물 알림
- 산출물 품질 검증

### 생성 명령어 템플릿

```bash
# Phase별 산출물 폴더 생성
npm run output:init -- --phase P0

# 산출물 수집 및 정렬
npm run output:collect -- --phase P1 --week W1

# 산출물 검증 리포트 생성
npm run output:validate -- --phase P2

# 산출물 패킹 (ZIP 생성)
npm run output:pack -- --phase P3 --format zip
```

---

## V. 산출물 체크리스트 (매주 확인)

### Template: Phase X, Week Y 산출물 체크리스트

**기간**: YYYY-MM-DD ~ YYYY-MM-DD  
**담당**: [에이전트명]

| # | 산출물명 | 예상 생성 날짜 | 실제 생성 날짜 | 검증 상태 | 비고 |
|---|---------|--------------|--------------|----------|------|
| 1-1 | 멘트 UI 컴포넌트 | 2026-04-08 | 2026-04-08 | ✅ 완료 | CommentPanel.tsx |
| 1-2 | localStorage 유틸 | 2026-04-09 | 2026-04-09 | ✅ 완료 | comment.ts |
| ... | | | | | |

---

## VI. 산출물 버전 관리

### 버전 네이밍 규칙

```
[산출물타입]_[기능]_v[버전]_[YYYYMMDD].파일확장자

예시:
- Component_CommentPanel_v1_20260408.tsx
- Spec_SAP_FI_v2_20260701.md
- Report_Validation_v1_20260516.html
- Template_Production_v3_20260505.xlsx
```

### 변경 이력 추적

각 산출물 상단에 변경 이력 주석 추가:

```markdown
# 산출물명

**최종 수정**: 2026-04-08  
**버전**: v1.2  
**담당**: Frontend/UI Agent

## 변경 이력
| 버전 | 날짜 | 내용 |
|------|------|------|
| v1.2 | 2026-04-08 | 오류 수정 + 성능 개선 |
| v1.1 | 2026-04-07 | 초안 검토 의견 반영 |
| v1.0 | 2026-04-06 | 초안 작성 |
```

---

## VII. 산출물 품질 기준

### 각 산출물 타입별 검증 기준

| 타입 | 검증 항목 | 통과 기준 |
|------|----------|----------|
| **코드** | 린트 | 0 에러, 0 경고 |
| **코드** | 테스트 커버리지 | 80% 이상 |
| **설계 문서** | 완성도 | 100% (모든 섹션 작성) |
| **설계 문서** | 검토 | PM + 담당자 서명 |
| **엑셀 양식** | 샘플 데이터 | 최소 5줄 이상 |
| **엑셀 양식** | 검증 규칙 | 모든 컬럼 정의됨 |
| **테스트 결과** | 커버리지 | 80% 이상 |
| **테스트 결과** | 통과율 | 100% |
| **배포 산출물** | 실행 가능 여부 | 실행 성공 |
| **배포 산출물** | 문서화 | README 포함 |

---

## VIII. 산출물 제출 및 검수 프로세스

### 제출 플로우

```
1. 산출물 생성 (담당 에이전트)
   ↓
2. 저장소 폴더 정리 (자동화)
   ↓
3. 품질 검증 (QA Agent)
   ↓
4. PM 최종 검수 (PM Agent)
   ↓
5. 승인 및 GitHub Push (Infrastructure Agent)
   ↓
6. 산출물 목록 업데이트 (PM Agent)
```

### 검수 항목 (PM 체크리스트)

- [ ] 산출물이 정의서에 명시된 형식과 일치
- [ ] 파일명이 네이밍 규칙 준수
- [ ] 필수 문서 (README, 변경이력) 포함
- [ ] 기술적 품질 기준 충족
- [ ] 완성도 100% (미완료 항목 없음)
- [ ] 주석/문서화 완전
- [ ] 팀 전체 공유 가능 상태

---

## IX. 월별 산출물 현황

### 2026년 산출물 통계

| 월 | Phase | 예상 산출물 수 | 누적 | 전체 진도율 |
|---|-------|:------:|:-----:|:----------:|
| 4월 | P0~P1 | 30 | 30 | 15% |
| 5월 | P1~P2 | 21 | 51 | 26% |
| 6월 | P3 | 22 | 73 | 37% |
| 7월 | P3~P4 | 29 | 102 | 52% |
| 8월 | P4 | 15 | 117 | 60% |
| 9월~10월 | P4 | 80+ | 197+ | 100% |

---

## X. 산출물 활용 가이드

### 산출물 제공 대상

| 대상 | 제공 산출물 | 방식 | 주기 |
|------|----------|------|------|
| **경영진** | 주간 진도 보고서 + 데모 영상 | 온라인 | 주간 |
| **개발팀** | 전체 코드 + 설계 문서 | GitHub | 실시간 |
| **부문 담당자** | 엑셀 양식 + 부문별 요구사항 정의서 | 이메일 | 필요시 |
| **사용자** | 기능 데모 + 사용 가이드 | 온라인 | 월간 |
| **시스템 담당자** | 배포 가이드 + API 문서 | GitHub Wiki | 필요시 |

---

**문서 이력:**

| 버전 | 일자 | 내용 |
|:----:|------|------|
| 1.0 | 2026-04-03 | 산출물 정의서 + 저장소 구조 작성 |

