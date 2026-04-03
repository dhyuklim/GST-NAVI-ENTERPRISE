# OUTPUT 폴더 가이드

**최종 수정**: 2026-04-03  
**목적**: 각 Phase별 산출물 저장소 구조 및 사용 가이드

---

## 📁 디렉토리 구조

```
OUTPUT/
├── P0_즉시실행/                    # Phase 0: 배포 + 보안 (4/3~4/4)
│   ├── code/                       # 구현 코드
│   ├── deployment/                 # 배포 관련 산출물
│   ├── security/                   # 보안 설정 파일
│   ├── tests/                      # 테스트 결과
│   └── docs/                       # 설계 문서
│
├── P1_대표지시반영/                # Phase 1: UI/대시보드 개선 (4/7~4/18)
│   ├── code/
│   │   ├── components/             # React 컴포넌트 (CommentPanel, Chart 등)
│   │   ├── utils/                  # 유틸리티 함수 (localStorage, growthRate 등)
│   │   └── services/               # 비즈니스 로직 (costAnalysis 등)
│   ├── data/                       # 샘플 데이터 (JSON)
│   ├── tests/                      # 테스트 케이스 + 결과
│   ├── demo/                       # 데모 영상
│   └── reports/                    # 완료 보고서
│
├── P2_생산TF반영/                  # Phase 2: 엑셀 업로드 + 다중 부문 (4/21~5/16)
│   ├── code/
│   │   ├── api/                    # API 엔드포인트 (엑셀 업로드 등)
│   │   ├── components/             # UI 컴포넌트 (ExcelUploadForm 등)
│   │   ├── utils/                  # 엑셀 파싱 등
│   │   ├── services/               # 생산성 계산 등
│   │   ├── dashboards/             # 대시보드 수정
│   │   └── contexts/               # React Context (UploadContext 등)
│   ├── templates/                  # 엑셀 양식 (생산/구매/SVC/품질)
│   ├── tf/                         # TF 산출물
│   ├── tests/                      # 테스트 결과
│   ├── reports/                    # 검증 리포트
│   └── docs/                       # 설계 문서
│
├── P3_시스템연동설계/              # Phase 3: DB + API 리팩토링 (5/19~7/11)
│   ├── code/
│   │   ├── api/                    # 리팩토링된 API
│   │   ├── schema/                 # Drizzle ORM 스키마
│   │   ├── queries/                # DB 쿼리 함수
│   │   ├── etl/                    # ETL 파이프라인
│   │   └── services/               # 비즈니스 로직
│   ├── database/                   # ERD, DDL 스크립트
│   ├── migrations/                 # DB 마이그레이션 파일
│   ├── specs/                      # 시스템 연동 명세서 (SAP/SVC/CRM)
│   ├── etl/                        # ETL 설계 문서
│   ├── docs/                       # BOM, 아키텍처 등
│   ├── tests/                      # DB, API 테스트
│   └── reports/                    # 마이그레이션 로그 등
│
└── P4_AI예측확산/                  # Phase 4: AI + 전사 확산 (7/14~10/3)
    ├── code/
    │   ├── ai/                     # AI 관련 API 핸들러
    │   ├── dashboards/             # 실 데이터 대시보드
    │   ├── reports/                # 보고서 생성 코드
    │   ├── pwa/                    # Service Worker 등
    │   └── utils/                  # 오프라인 지원 등
    ├── ai_models/                  # Python AI 모델 (Prophet, 이상탐지)
    ├── ai_notebooks/               # Jupyter 분석 노트북
    ├── ai_config/                  # KPI Alert 규칙, NLQ 프롬프트
    ├── integrations/               # SAP/MES/SVC 연동 코드
    ├── templates/                  # 보고서 템플릿
    ├── pwa/                        # manifest.json 등
    ├── tests/                      # E2E, AI 모델 테스트
    └── reports/                    # 최종 완료 보고서
```

---

## 📋 각 폴더별 저장 규칙

### 1. **code/** — 구현 코드

**저장 규칙**:
- TypeScript/JavaScript 파일만 저장
- 컴포넌트는 `.tsx`, 유틸은 `.ts`, API는 `routes.ts` 형식
- 주석 필수 (변경 이력 포함)

**파일명 규칙**:
```
[기능]_[설명]_v[버전].tsx

예:
- CommentPanel_v1.tsx
- CostChart_v1.tsx
- ExcelUploadForm_v1.tsx
- LineDetailModal_v1.tsx
```

### 2. **templates/** — 엑셀 양식

**저장 규칙**:
- 정확한 컬럼명 명시
- 검증 규칙 Sheet로 포함
- 샘플 데이터 최소 5줄

**파일명 규칙**:
```
[부문]_Template_v[버전]_[YYYYMMDD].xlsx

예:
- Production_Template_v1_20260421.xlsx
- Procurement_Template_v1_20260505.xlsx
```

### 3. **specs/** — 시스템 연동 명세서

**저장 규칙**:
- Markdown 형식 (.md)
- 필드 매핑 테이블 필수
- 에러 처리 방안 명시

**파일명 규칙**:
```
[시스템]_[모듈]_Spec_v[버전].md

예:
- SAP_FI_Interface_Spec_v1.md
- SVC_API_Spec_v1.md
- Salesforce_Integration_Spec_v1.md
```

### 4. **ai_models/** — AI 모델 (Python)

**저장 규칙**:
- `.py` 파일 또는 `.joblib` (저장된 모델)
- requirements.txt 포함
- 학습 데이터셋 명시

**파일명 규칙**:
```
[모델명]_v[버전]_[YYYYMMDD].py

예:
- sales_forecast_prophet_v1_20260811.py
- anomaly_detection_v1_20260812.py
```

### 5. **tests/** — 테스트 결과

**저장 규칙**:
- 테스트 코드 + 실행 결과
- JSON 또는 HTML 리포트 형식
- 통과율 100% 확인

**파일명 규칙**:
```
[기능]_test_v[버전]_[YYYYMMDD].json

예:
- comment_feature_test_v1_20260408.json
- validation_test_v1_20260516.json
```

### 6. **reports/** — 보고서 및 로그

**저장 규칙**:
- Markdown (.md) 또는 HTML (.html)
- 변경 이력 포함
- 결론과 제안 명시

**파일명 규칙**:
```
[리포트타입]_[내용]_v[버전]_[YYYYMMDD].[md|html]

예:
- Completion_Report_P1_v1_20260418.md
- Validation_Report_P2_v1_20260516.html
- Migration_Log_v1_20260613.md
```

### 7. **database/** — DB 설계

**저장 규칙**:
- ERD: `.svg` 또는 `.png` (PlantUML 추천)
- DDL: `.sql` 파일
- 마이그레이션 계획: `.md`

**파일명 규칙**:
```
[이름]_[설명]_v[버전].[확장자]

예:
- ERD_Star_Schema_v1.svg
- schema_001_initial_v1.sql
- Migration_Plan_v1.md
```

---

## 🔄 산출물 생성 프로세스

### Step 1: 폴더 확인
```bash
# 해당 Phase 폴더 확인
ls OUTPUT/P1_대표지시반영/code/components/
```

### Step 2: 파일 생성
```bash
# 파일 생성 후 폴더에 저장
# 예: CommentPanel.tsx → OUTPUT/P1_대표지시반영/code/components/
```

### Step 3: README 작성
```markdown
# 산출물명

**작성일**: 2026-04-08  
**담당**: Frontend/UI Agent  
**버전**: v1.0

## 개요
[기능 설명]

## 파일 구조
[폴더 구조]

## 사용 방법
[사용 가이드]

## 변경 이력
| 버전 | 날짜 | 내용 |
|------|------|------|
| v1.0 | 2026-04-08 | 초안 작성 |
```

### Step 4: 커밋 및 푸시
```bash
cd OUTPUT/P1_대표지시반영/code/components/
git add CommentPanel.tsx
git commit -m "P1-1-1: CommentPanel 컴포넌트 추가"
git push origin master
```

---

## ✅ 산출물 체크리스트

### 매주 확인 사항 (담당자 필수)

- [ ] 산출물이 올바른 폴더에 저장됨
- [ ] 파일명이 네이밍 규칙 준수
- [ ] README 또는 변경 이력 주석 포함
- [ ] 코드는 린트 통과 (0 에러)
- [ ] 테스트 커버리지 80% 이상
- [ ] 문서화 완전 (주석, 파라미터 설명)
- [ ] 민감한 정보 (키, 암호) 제거됨
- [ ] GitHub에 커밋됨

---

## 📊 산출물 현황 대시보드

### 주간 산출물 생성 현황

**Template: 주간 산출물 집계**

```markdown
# P[X] 주간 산출물 보고

**기간**: YYYY-MM-DD ~ YYYY-MM-DD  
**담당**: [에이전트명]

| 과제 | 산출물 | 생성 여부 | 검증 상태 | 비고 |
|------|--------|:-------:|:-------:|------|
| 1-1 | CommentPanel.tsx | ✅ | ✅ | 완료 |
| 1-1 | comment.ts | ✅ | ✅ | 완료 |
| 1-2 | costAnalysis.ts | ✅ | ⏳ | 검토 중 |
| 1-2 | CostChart.tsx | ❌ | ⏳ | 예정 4/9 |

**총 현황**: 3/4 완료 (75%)
```

---

## 🚀 자동화 커맨드

### 산출물 폴더 생성 (자동)

```bash
# 새로운 Phase 폴더 생성
npm run output:init -- --phase P5

# 산출물 수집
npm run output:collect -- --phase P1

# 산출물 검증
npm run output:validate -- --phase P2

# 산출물 패킹 (ZIP)
npm run output:pack -- --phase P3 --format zip
```

### Git 커밋 메시지 형식

```
[Phase][과제]_[산출물타입]: [설명]

예:
[P1-1-1]_code: CommentPanel 컴포넌트 추가
[P2-5-3]_template: 구매 엑셀 양식 최종본
[P3-1-1]_database: PostgreSQL 스키마 정의 완료
```

---

## 📞 문의 및 지원

| 담당자 | 역할 | 연락처 |
|--------|------|--------|
| PM Agent | 산출물 검수 | [PM] |
| Infrastructure | 저장소 관리 | [DevOps] |
| QA | 품질 검증 | [QA] |

---

**마지막 수정**: 2026-04-03  
**버전**: v1.0
