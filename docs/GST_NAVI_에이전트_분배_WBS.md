# GST NAVI — 에이전트 분배 및 상세 WBS

**작성일**: 2026-04-03  
**기준**: 통합 개발계획 v2  
**목표**: 역할 분담을 통한 병렬 개발 + 명확한 과제 추적

---

## I. 에이전트 구성 및 역할

### 1. 🟦 Backend/API Agent (DB, API, 시스템 연동)
**역할**: 서버 아키텍처, 데이터베이스, API 개발

#### 담당 영역
- PostgreSQL 스키마 설계 및 구현
- ORM (Drizzle) 기반 API 개발
- 엑셀 업로드 파이프라인
- SAP/MES/SVC API 연동 설계
- ETL 파이프라인 개발
- 데이터 검증 및 마이그레이션

#### 주요 기술
- Node.js/Express
- PostgreSQL + Drizzle ORM
- Kafka/Bull Queue (데이터 처리)

#### Phase별 투입 시점
- **Phase 0~2**: 부분 투입 (엑셀 API)
- **Phase 3**: 주요 투입 (DB/API 리팩토링)
- **Phase 4**: 시스템 연동 (SAP/MES/SVC)

---

### 2. 🟪 Frontend/UI Agent (UI, 대시보드, 시각화)
**역할**: 사용자 인터페이스, 대시보드 개발, 실시간 데이터 표시

#### 담당 영역
- 대시보드 컴포넌트 개발 및 고도화
- 차트/시각화 (ECharts, Recharts)
- 드릴다운 상호작용
- 멘트/코멘트 입력 기능
- 데이터 라벨 (LIVE/MOCK 표시)
- 기간 비교 UI
- 모바일 PWA 개발

#### 주요 기술
- React + Vite + TypeScript
- ECharts / Recharts
- Tailwind CSS / shadcn/ui

#### Phase별 투입 시점
- **Phase 0~4**: 지속 투입 (UI 주도)

---

### 3. 🟨 Data/ETL Agent (데이터 처리, 엑셀 연동)
**역할**: 데이터 수집, 정제, 변환 및 엑셀 파이프라인

#### 담당 영역
- 엑셀 데이터 수집 양식 설계
- 데이터 검증 로직 구현
- 엑셀 ↔ DB 변환 파이프라인
- 데이터 품질 모니터링
- 임시 저장소 관리 (JSON/CSV)
- 데이터 정규화 및 매핑

#### 주요 기술
- Excel.js / XLSX 라이브러리
- Pandas (Python)
- Node.js 스트림 처리

#### Phase별 투입 시점
- **Phase 1**: 부분 투입 (엑셀 양식 설계)
- **Phase 2**: 주요 투입 (다중 엑셀 업로드)
- **Phase 3**: 지속 (ETL 구축)

---

### 4. 🟩 AI/Analytics Agent (AI, 예측, 분석)
**역할**: AI 기반 분석, 예측 모델, 자동 생성

#### 담당 영역
- 매출 예측 모델 (시계열 분석)
- 이상 탐지 (KPI Alert)
- 자연어 질의 (NLQ) LLM 연동
- 자동 월간 보고서 생성
- 통계 분석 및 인사이트
- A/B 테스트 설계

#### 주요 기술
- Python + scikit-learn + TensorFlow
- Claude API / LangChain
- Time Series (Prophet, ARIMA)
- Statistical Analysis

#### Phase별 투입 시점
- **Phase 3**: 파일럿 (AI 설계)
- **Phase 4**: 주요 투입 (AI 모델 개발)

---

### 5. 🟫 Infrastructure/DevOps Agent (배포, 보안, 운영)
**역할**: 배포 자동화, 보안, 성능 모니터링

#### 담당 영역
- 환경 설정 및 보안 강화 (JWT, 환경변수)
- Vercel 배포 자동화
- Docker 컨테이너화
- 성능 모니터링
- 백업/복구 전략
- 보안 감시 (OWASP)

#### 주요 기술
- Docker / Docker Compose
- GitHub Actions / CI-CD
- Vercel / AWS
- Sentry (모니터링)

#### Phase별 투입 시점
- **Phase 0**: 즉시 투입 (보안)
- **Phase 3**: 확대 투입 (DB/API 배포)
- **Phase 4**: 지속 (모니터링)

---

### 6. 🟧 QA/Testing Agent (테스트, 검증)
**역할**: 자동화 테스트, 버그 추적, 성능 검증

#### 담당 영역
- 단위 테스트 (Unit Test)
- E2E 테스트 (Playwright)
- 성능 테스트 (Load Test)
- 데이터 검증 테스트
- 버그 추적 및 보고
- 회귀 테스트 자동화

#### 주요 기술
- Vitest / Jest
- Playwright / Cypress
- LoadRunner / k6
- GitHub Issues / Jira

#### Phase별 투입 시점
- **Phase 2**: 부분 투입 (테스트 구축)
- **Phase 3**: 주요 투입 (자동화)
- **Phase 4**: 지속 (품질 관리)

---

### 7. 🟧 PM/Requirements Agent (기획, 요구사항)
**역할**: 비즈니스 요구사항, 진도 관리, 커뮤니케이션

#### 담당 영역
- 사용자 요구사항 수집 및 정의
- 부문별 담당자 협업 (제조/SVC/재무)
- TF 진행 (캐파/생산성 정의)
- 주간 진도 관리
- 리스크 모니터링
- 문서 관리 (WBS/마일스톤)

#### 주요 기술
- 요구사항 분석 (BRD/SRS)
- 프로젝트 계획 (Gantt/WBS)
- 커뮤니케이션 (Slack/Email)

#### Phase별 투입 시점
- **Phase 0~4**: 지속 투입 (Project Lead)

---

## II. Phase별 에이전트 배치 계획

### Phase 0: 즉시 실행 (W0: 4/3~4/4)

| 에이전트 | 투입 | 담당 과제 | 비고 |
|---------|:----:|----------|------|
| **Frontend/UI** | 🔴 | 0-1: 로컬 변경 4건 푸시 | Priority |
| **Infrastructure** | 🔴 | 0-2, 0-3: 보안 조치 | 병렬 |
| **PM** | 🟡 | 0-4: 문서 정리 + 주간 회의 | 동시 |

**산출물**: 배포 + 보안 기본 조치

---

### Phase 1: 대표이사 지시 반영 (W1~W2: 4/7~4/18)

#### Week 1 (4/7~4/11)

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **Frontend/UI** | 🔴 | 1-1: 멘트/코멘트 기능 (Dashboard) |
| **Frontend/UI** | 🔴 | 1-2: 원가 구성비 시각화 개선 |
| **Frontend/UI** | 🔴 | 1-3: 생산 캐파/가동률 드릴다운 |
| **Frontend/UI** | 🔴 | 1-4: 1인당 생산성 월별 추이 |
| **PM** | 🟡 | 캐파 정의 TF 킥오프 |
| **QA** | 🟡 | 1-1~1-4 기본 테스트 케이스 작성 |

**산출물**: 재무 + 생산 보강 완료

#### Week 2 (4/14~4/18)

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **Frontend/UI** | 🔴 | 1-5: SVC 법인별 드릴다운 |
| **Frontend/UI** | 🔴 | 1-6: 기간 비교 시각화 (나란히 바 차트) |
| **Frontend/UI** | 🔴 | 1-7: 데이터 완성도 라벨 (LIVE/MOCK) |
| **Data/ETL** | 🟡 | SVC 법인별 목업 데이터 정리 |
| **PM** | 🟡 | 데모 준비 + 요구사항 확정 |
| **QA** | 🟡 | E2E 테스트 케이스 추가 |

**산출물**: SVC + 기간비교 고도화 + 데이터 라벨

---

### Phase 2: 생산 TF 반영 + 데이터 확장 (W3~W6: 4/21~5/16)

#### Week 3~4 (4/21~5/2): 생산 TF 결과 반영

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **PM** | 🔴 | 2-1/2-2: TF 킥오프 + 캐파/생산성 정의 협의 |
| **Frontend/UI** | 🟡 | 2-3: 엑셀 양식 UI 설계 |
| **Data/ETL** | 🔴 | 2-3/2-4: 생산 엑셀 양식 개발 + 업로드 기능 |
| **Backend/API** | 🟡 | 2-4: 엑셀 데이터 임시 저장 API |
| **QA** | 🟡 | 데이터 검증 테스트 설계 |

**산출물**: 생산 엑셀 양식 + 업로드 기능

#### Week 5~6 (5/5~5/16): 기타 부문 확장

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **Data/ETL** | 🔴 | 2-5~2-7: 구매/SVC/품질 엑셀 양식 설계 |
| **Backend/API** | 🔴 | 2-8: 다중 엑셀 업로드 API 개발 |
| **Frontend/UI** | 🔴 | 2-8: 다중 엑셀 업로드 UI |
| **QA** | 🔴 | 2-9: 데이터 검증 리포트 테스트 |
| **PM** | 🟡 | 주간 회의 + 진도 모니터링 |

**산출물**: 엑셀 양식 4종 + 다중 업로드 기능 + 검증 리포트

---

### Phase 3: 시스템 연동 설계 + DB 전환 (W7~W14: 5/19~7/11)

#### Week 7~10 (5/19~6/13): DB + API 기반 구축

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **Backend/API** | 🔴 | 3-1: PostgreSQL 스키마 설계 (Star Schema) |
| **Backend/API** | 🔴 | 3-2: Drizzle ORM 도입 + 마이그레이션 |
| **Backend/API** | 🔴 | 3-3: API 리팩토링 (DB 연동) |
| **Backend/API** | 🔴 | 3-4: 엑셀 → DB 파이프라인 |
| **Frontend/UI** | 🟡 | 데이터 바인딩 변경 대응 |
| **QA** | 🔴 | DB 마이그레이션 검증 테스트 |
| **Infrastructure** | 🔴 | 개발/스테이징 DB 환경 구축 |

**산출물**: PostgreSQL 운영 DB + API 리팩토링 완료

#### Week 11~14 (6/16~7/11): 시스템 연동 설계

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **Backend/API** | 🔴 | 3-5: SAP 인터페이스 명세 (FI/CO/SD/MM/PP) |
| **Backend/API** | 🔴 | 3-6: SVC API 명세 |
| **Backend/API** | 🔴 | 3-7: CRM 연동 명세 |
| **Backend/API** | 🔴 | 3-8: ETL 파이프라인 설계 |
| **Backend/API** | 🔴 | 3-9: BOM 기반 원가 설계 |
| **Data/ETL** | 🟡 | ETL 프로토타입 개발 |
| **PM** | 🟡 | SAP/SVC 담당자 협의 |

**산출물**: 인터페이스 명세서 5종 + ETL 설계서

---

### Phase 4: AI/예측 + 전사 확산 (W15~W26: 7/14~10/3)

#### Week 15~18 (7/14~8/8): 시스템 연동 개발

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **Backend/API** | 🔴 | 4-1: SAP FI/SD 연동 개발 |
| **Backend/API** | 🔴 | 4-2: MES 연동 개발 |
| **Backend/API** | 🔴 | 4-3: SVC API 연동 개발 |
| **Frontend/UI** | 🟡 | 실 데이터 대시보드 업데이트 |
| **QA** | 🔴 | 시스템 연동 E2E 테스트 |
| **Infrastructure** | 🔴 | 운영 환경 배포 |

**산출물**: SAP/MES/SVC 실 데이터 연동

#### Week 19~22 (8/11~9/5): AI/예측 개발

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **AI/Analytics** | 🔴 | 4-4: 매출 예측 모델 (시계열) |
| **AI/Analytics** | 🔴 | 4-5: 이상 탐지 (KPI Alert) |
| **AI/Analytics** | 🔴 | 4-6: 자연어 질의 LLM 연동 |
| **Frontend/UI** | 🟡 | AI 결과 시각화 UI |
| **Backend/API** | 🟡 | AI 모델 API 서빙 |
| **QA** | 🟡 | AI 모델 성능 테스트 |

**산출물**: AI 매출 예측 + 이상탐지 + NLQ

#### Week 23~26 (9/8~10/3): 자동 보고서 + PWA + 안정화

| 에이전트 | 투입 | 담당 과제 |
|---------|:----:|----------|
| **AI/Analytics** | 🔴 | 4-7: 자동 월간 보고서 생성 |
| **Frontend/UI** | 🔴 | 4-8: 모바일 PWA 개발 |
| **QA** | 🔴 | 전체 회귀 테스트 |
| **Infrastructure** | 🔴 | 성능 최적화 + 모니터링 |
| **PM** | 🟡 | 최종 검수 + 배포 |

**산출물**: 자동 보고서 + PWA + 안정화 완료

---

## III. 상세 WBS 및 과제 정의

### 📋 Phase 0: 즉시 실행 (W0: 4/3~4/4)

#### 과제 0-1: 로컬 변경 4건 푸시
**담당**: Frontend/UI Agent  
**공수**: 즉시 (2시간)  
**완료조건**:
- [ ] Dashboard.tsx: 판가 트렌드(ASP) 차트 + 기간 비교 모드 병합
- [ ] ServiceDashboard.tsx: 법인별 장비 + 워런티 + 커버리지 추가
- [ ] ProductionDashboard.tsx: 캐파 대비 가동률 추가
- [ ] 모든 변경사항 Vercel 배포 완료

**검증**:
- Vercel 배포 성공 (https://gst-navi-v2.vercel.app)
- 로컬 변경 4건 모두 반영 확인

---

#### 과제 0-2: JWT 시크릿 환경변수화
**담당**: Infrastructure Agent  
**공수**: 1시간  
**상세 작업**:
1. `.env.local` 추가 (`.gitignore` 등록)
2. `JWT_SECRET` 환경변수 설정
3. `process.env.JWT_SECRET` 사용으로 코드 변경
4. Vercel 환경변수 설정 (대시보드)
5. 기존 하드코딩된 시크릿 제거

**완료조건**:
- [ ] JWT 시크릿이 환경변수로 분리됨
- [ ] 로컬/배포 환경 모두 정상 작동
- [ ] 시크릿이 GitHub에 노출되지 않음

---

#### 과제 0-3: API 인증 미들웨어 적용
**담당**: Infrastructure Agent  
**공수**: 2시간  
**상세 작업**:
1. `middleware/auth.ts` 생성 (JWT 검증)
2. 모든 API 라우트에 인증 미들웨어 적용
3. 토큰 만료 처리 (refreshToken)
4. 권한별 접근 제어 (Role-based)

**완료조건**:
- [ ] 인증 없이 API 접근 불가
- [ ] 토큰 검증 로직 동작 확인
- [ ] 기간만료 토큰 자동 갱신

---

#### 과제 0-4: 문서 정리
**담당**: PM Agent  
**공수**: 2시간  
**상세 작업**:
1. 중복 MD 문서 통합 (README 마스터 정리)
2. 개발 가이드 (Quick Start) 업데이트
3. 버전 관리 방침 명시
4. 팀 문서 링크 정리

**완료조건**:
- [ ] README.md 현행화 (Phase 0~1 기준)
- [ ] 개발 환경 설정 가이드 명시
- [ ] 비필수 문서 정리 완료

---

### 📋 Phase 1: 대표이사 지시 반영 (W1~W2: 4/7~4/18)

#### 과제 1-1: 멘트/코멘트 입력 기능
**담당**: Frontend/UI Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**1.1.1 멘트 UI 컴포넌트 개발**
- CommentPanel 컴포넌트 생성 (우측 사이드바)
- 입력/편집/삭제 UI 구현
- 타임스탬프 + 작성자 표시

**1.1.2 localStorage 연동**
- 멘트 저장 구조: `comments_[dashboardId]_[chartId]`
- 로컬 CRUD 함수 작성
- 새로고침 후 멘트 복원 확인

**1.1.3 차트별 멘트 연동**
- 모든 차트에 CommentPanel 추가
- 차트 클릭 시 해당 멘트 로드
- 멘트 변경 시 실시간 반영

**1.1.4 테스트**
- 멘트 입력/저장/삭제 테스트
- localStorage 용량 관리
- 다중 탭 멘트 동기화

**완료조건**:
- [ ] 모든 대시보드 차트에 멘트 입력 기능 추가
- [ ] localStorage에 멘트 저장됨
- [ ] 새로고침 후 멘트 복원됨
- [ ] 멘트 30개 이상 저장 가능

---

#### 과제 1-2: 원가 구성비 시각화 개선
**담당**: Frontend/UI Agent  
**공수**: 1일 (8시간)  
**상세 작업**:

**1.2.1 원가 데이터 구조 분석**
- 현재 데이터: 매출원가 / R&D / 유지보수 / 판관비
- 각 항목별 비율 계산
- 실데이터 기반 계산식 정의

**1.2.2 차트 개선**
- 파이 차트 → 누적 바 차트 전환 (월별 추이)
- 색상 일관성 강화
- 범례/레이블 명확화

**1.2.3 인터랙션**
- 항목별 백분율 표시
- 호버 시 상세 금액 표시
- 드릴다운으로 상세 구성비 조회

**완료조건**:
- [ ] 누적 바 차트 (월별 원가 구성비) 구현
- [ ] 실 데이터 기반 계산 확인
- [ ] 호버 시 금액/비율 상세 표시

---

#### 과제 1-3: 생산 캐파/가동률 드릴다운
**담당**: Frontend/UI Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**1.3.1 라인별 드릴다운 UI**
- ProductionDashboard에서 라인 클릭 시 모달 열기
- 선택된 라인 상세 정보 표시

**1.3.2 월별 가동률 추이 차트**
- 라인별 6개월 가동률 추이 (선 그래프)
- 목표 캐파 선 (reference line) 표시
- 편차 강조 (빨강/녹색)

**1.3.3 비가동 사유 분석**
- 계획 정지 / 고장 / 기타 카테고리화
- 누적 바 차트 (시간 기준)
- 상위 5개 사유 표시

**1.3.4 상세 정보**
- 라인명 / 캐파 / 현재 가동률 / 평균 가동률
- 월별 상세 테이블 (일자/가동시간/비가동 사유)

**완료조건**:
- [ ] 라인 클릭 시 드릴다운 모달 열림
- [ ] 6개월 추이 차트 표시됨
- [ ] 비가동 사유 분석 표시됨
- [ ] 상세 테이블 필터/정렬 가능

---

#### 과제 1-4: 1인당 생산성 월별 추이
**담당**: Frontend/UI Agent  
**공수**: 1일 (8시간)  
**상세 작업**:

**1.4.1 생산성 데이터 수집**
- HR 데이터: 부서별 인원 수
- 생산 데이터: 부서별 월별 생산량
- 계산식: 생산성 = 생산량 / 인원 수

**1.4.2 차트 구현**
- 부서별 선 그래프 (6개월 추이)
- 평균선 (벤치마크) 표시
- 목표선 (설정값) 표시

**1.4.3 인터랙션**
- 부서 선택/제외 토글
- 월별 상세 수치 표시
- 비교 기능 (이전년 동월)

**완료조건**:
- [ ] HR Dashboard에 생산성 추이 차트 추가
- [ ] 6개월 데이터 표시됨
- [ ] 부서별 비교 가능

---

#### 과제 1-5: SVC 법인별 드릴다운
**담당**: Frontend/UI Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**1.5.1 법인별 카드 UI**
- ServiceDashboard에서 법인 카드 클릭 시 모달 열기
- 법인명/설치 장비 수/워런티 현황 표시

**1.5.2 장비 목록 드릴다운**
- 테이블: 장비번호/모델/설치일자/상태
- 상태별 색상 표시 (정상/보증기간/만료)
- 정렬/필터 기능

**1.5.3 워런티 상세**
- 워런티 IN/OUT 현황 타임라인
- 월별 워런티 통계
- 갱신 예정 장비 알림

**1.5.4 인력 배치**
- 담당자 / 할당 장비 수 / 커버리지 표시
- 인당 커버리지 평균값 기준 대비 강조

**완료조건**:
- [ ] 법인 클릭 시 드릴다운 모달 열림
- [ ] 장비 목록/워런티/인력 정보 모두 표시됨
- [ ] 테이블 정렬/필터 동작

---

#### 과제 1-6: 기간 비교 시각화
**담당**: Frontend/UI Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**1.6.1 비교 모드 UI**
- 대시보드 상단에 비교 모드 토글
- "이번 월" vs "비교 월" 선택 드롭다운
- 비교 월은 이전년 동월/이전월 등 선택 가능

**1.6.2 차트 변경**
- 기본: 단일 월 데이터
- 비교 모드: 나란히 바 차트 (2개 시리즈)
- 색상 구분 (이번월/비교월)

**1.6.3 성장률 표시**
- 차트 상단에 성장률 표시 (+5.2% / -3.1%)
- 화살표 + 색상으로 추이 표시

**1.6.4 드릴다운**
- 비교 모드 유지한 채 드릴다운 가능
- 상세 테이블도 비교 모드 반영

**완료조건**:
- [ ] 비교 모드 토글 추가됨
- [ ] 나란히 바 차트 구현됨
- [ ] 성장률 계산/표시됨
- [ ] 모든 차트에서 비교 모드 동작

---

#### 과제 1-7: 데이터 완성도 라벨
**담당**: Frontend/UI Agent  
**공수**: 1일 (8시간)  
**상세 작업**:

**1.7.1 데이터 소스 정의**
- LIVE: 실시간 ERP/MES 연동 데이터
- MOCK: 목업 데이터
- 준비중: 아직 미구현

**1.7.2 라벨 위치 결정**
- 각 대시보드 탭 상단에 배지 추가
- 개별 차트 우상단 작은 라벨

**1.7.3 라벨 구현**
- 메타데이터 구조: `{ chartId, dataSource, lastUpdate }`
- 라벨 컴포넌트: `<DataSourceBadge source="LIVE" lastUpdate="2026-04-03 14:30" />`

**1.7.4 스타일**
- LIVE: 녹색 / MOCK: 회색 / 준비중: 주황색

**완료조건**:
- [ ] 모든 탭에 데이터 소스 라벨 추가됨
- [ ] 각 차트에 소스 정보 표시됨
- [ ] 색상/스타일 일관성 유지

---

### 📋 Phase 2: 생산 TF 반영 + 데이터 확장 (W3~W6: 4/21~5/16)

#### 과제 2-1: 캐파 정의 반영
**담당**: PM Agent (협의) + Backend/API Agent (구현)  
**공수**: 3일 (24시간)  
**상세 작업**:

**2-1.1 TF 킥오프 (PM)**
- 제조 부문 회의 (라인별 캐파 정의)
- 현황: 라인별 명목 캐파 / 실제 가능 캐파
- 산출물: TF_캐파_정의_v1.xlsx

**2-1.2 데이터 구조 설계 (Backend)**
```
CapacityDefinition {
  lineId: string
  lineName: string
  nominalCapacity: number  // 명목 캐파
  effectiveCapacity: number  // 실제 가능 캐파
  unit: string  // 시간/개
  definedDate: date
  status: "draft" | "approved"
}
```

**2-1.3 API 개발**
- `/api/capacity/definitions` (GET/POST/PUT)
- TF 산출물 엑셀 업로드 기능

**2-1.4 대시보드 연동**
- ProductionDashboard에서 캐파 정의 적용
- 차트 계산 로직 업데이트

**완료조건**:
- [ ] TF 캐파 정의서 작성 완료
- [ ] 캐파 정의 API 구현됨
- [ ] 생산 대시보드에 새 캐파 정의 반영됨

---

#### 과제 2-2: 생산성 지표 정의 반영
**담당**: PM Agent (협의) + Frontend/UI Agent (구현)  
**공수**: 3일 (24시간)  
**상세 작업**:

**2-2.1 TF 정의 (PM)**
- Activity 기반 생산성 정의
- 산출물: TF_생산성지표_정의_v1.xlsx
- 예: 생산성 = (정상작업시간 / 전체작업시간) × 산출량

**2-2.2 계산 로직 구현**
```typescript
calculateProductivity = (activity: Activity[], output: number) => {
  const normalWorkTime = activity
    .filter(a => a.type === 'normal')
    .reduce((sum, a) => sum + a.duration, 0);
  const totalTime = activity.reduce((sum, a) => sum + a.duration, 0);
  return (normalWorkTime / totalTime) * output;
};
```

**2-2.3 HR 대시보드 업데이트**
- 1인당 생산성 재계산
- 월별 추이 차트 업데이트

**2-2.4 검증**
- TF 산출값과 대시보드 값 비교
- 편차 분석

**완료조건**:
- [ ] 생산성 지표 정의 완료
- [ ] 계산 로직 구현 및 테스트됨
- [ ] HR/생산 대시보드 연동됨

---

#### 과제 2-3: 생산 엑셀 양식 설계 및 개발
**담당**: Data/ETL Agent + Frontend/UI Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**2-3.1 엑셀 양식 설계 (Data/ETL)**
- Sheet 1: 라인별 월별 가동률
  - 컬럼: 라인명, 1월, 2월, ..., 12월, 평균
  - 주의: 0~100% 범위, 실수 소수점 2자리
- Sheet 2: 라인별 비가동 사유
  - 컬럼: 라인명, 계획정지(시간), 고장(시간), 기타(시간)
- Sheet 3: 부서별 1인당 생산성
  - 컬럼: 부서명, 1월, 2월, ..., 12월, 평균

**2-3.2 UI 개발 (Frontend)**
- 업로드 폼: 파일 선택 + 미리보기
- 검증 표시 (성공/오류)
- 저장 버튼

**2-3.3 파이프라인 (Data/ETL)**
- 엑셀 파싱 (xlsx 라이브러리)
- 데이터 검증 (타입/범위/필수값)
- JSON 변환

**2-3.4 임시 저장 (Backend)**
- `/api/production/upload` (POST)
- 임시 저장소: `data/production_temp/[timestamp].json`

**완료조건**:
- [ ] 생산 엑셀 양식 완성됨
- [ ] 업로드 UI 구현됨
- [ ] 파일 검증 로직 동작

---

#### 과제 2-4: 생산 대시보드 실데이터 전환
**담당**: Frontend/UI Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**2-4.1 데이터 바인딩**
- 목업 데이터 제거
- 업로드된 엑셀 데이터 연동
- 상태 관리: 목업 ↔ 실데이터 전환 로직

**2-4.2 라벨 업데이트**
- ProductionDashboard 데이터 소스 라벨: "MOCK" → "LIVE"
- lastUpdate 타임스탐프 추가

**2-4.3 기본값 설정**
- 엑셀 업로드 전: 목업 데이터 표시
- 엑셀 업로드 후: 실 데이터 표시
- 새로고침 시 엑셀 데이터 로드

**완료조건**:
- [ ] 생산 대시보드에서 엑셀 데이터 표시됨
- [ ] 라벨 "LIVE"로 표시됨
- [ ] 업로드 후 대시보드 자동 갱신

---

#### 과제 2-5: 구매 엑셀 양식 설계
**담당**: Data/ETL Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**2-5.1 요구사항 수집 (PM)**
- 구매 부문 담당자 인터뷰
- 현재 집계 방식 파악
- 필요 지표 정의

**2-5.2 엑셀 양식 설계**
- Sheet 1: 월별 외주 조달
  - 협력사별 발주액 / 납기율 / 품질
- Sheet 2: 자재 부족 현황
  - 자재명 / 부족수량 / 예정 입고일
- Sheet 3: 협력사 평가
  - 협력사명 / 납기율 / 품질점수 / 단가경쟁력

**2-5.3 검증 규칙**
- 발주액: 양수, 천단위
- 납기율: 0~100%
- 필수 컬럼 체크

**완료조건**:
- [ ] 구매 엑셀 양식 완성됨
- [ ] 샘플 데이터 작성 완료

---

#### 과제 2-6: SVC 엑셀 양식 설계
**담당**: Data/ETL Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**2-6.1 요구사항 수집 (PM)**
- SVC 부문 담당자 인터뷰
- 장비 관리 / 워런티 / 인력 현황

**2-6.2 엑셀 양식 설계**
- Sheet 1: 법인별 장비 현황
  - 법인명 / 장비명 / 모델 / 설치일 / 상태
- Sheet 2: 워런티 현황
  - 장비명 / 워런티 시작일 / 종료일 / 갱신 여부
- Sheet 3: 인력 배치
  - 담당자명 / 할당 장비 수 / 지역

**2-6.3 검증 규칙**
- 날짜 형식: YYYY-MM-DD
- 장비 수: 양의 정수
- 필수 컬럼 체크

**완료조건**:
- [ ] SVC 엑셀 양식 완성됨
- [ ] 샘플 데이터 작성 완료

---

#### 과제 2-7: 품질 엑셀 양식 설계
**담당**: Data/ETL Agent  
**공수**: 2일 (16시간)  
**상세 작업**:

**2-7.1 요구사항 수집 (PM)**
- 품질 부문 담당자 인터뷰
- 불량 / 검사 / CAPA 현황

**2-7.2 엑셀 양식 설계**
- Sheet 1: 월별 불량률
  - 제품명 / 검사건수 / 불량건수 / 불량률
- Sheet 2: Pareto 데이터
  - 불량원인별 발생 빈도
- Sheet 3: CAPA 현황
  - CAPA ID / 내용 / 상태 / 완료예정일

**2-7.3 검증 규칙**
- 불량률: 0~100%
- 빈도: 양의 정수
- 상태: enum (진행중/완료/미진행)

**완료조건**:
- [ ] 품질 엑셀 양식 완성됨
- [ ] 샘플 데이터 작성 완료

---

#### 과제 2-8: 다중 엑셀 업로드 기능 개발
**담당**: Backend/API Agent + Frontend/UI Agent  
**공수**: 5일 (40시간)  
**상세 작업**:

**2-8.1 API 개발 (Backend)**
```typescript
// POST /api/upload/multi
{
  files: [
    { section: "production", file: File },
    { section: "procurement", file: File },
    { section: "service", file: File },
    { section: "quality", file: File }
  ]
}
// Response:
{
  uploadId: "uuid",
  results: [
    { section: "production", status: "success", rows: 50 },
    { section: "procurement", status: "success", rows: 30 },
    ...
  ]
}
```

**2-8.2 처리 파이프라인**
- 다중 파일 수신
- 각 파일별 검증
- 병렬 처리 (부문별 독립적)
- 결과 통합 응답

**2-8.3 UI 개발 (Frontend)**
- 4개 섹션 별도 영역 (생산/구매/SVC/품질)
- 각 섹션: 파일 선택 + 드래그 드롭
- 업로드 진행률 표시 (부문별)
- 결과 요약 (성공/오류 건수)

**2-8.4 상태 관리**
- Context API: UploadContext
- 상태: idle → uploading → success/error
- 결과 임시 저장 (sessionStorage)

**완료조건**:
- [ ] 다중 파일 동시 업로드 가능
- [ ] 각 부문별 검증 독립적 진행
- [ ] 결과 요약 UI 표시됨

---

#### 과제 2-9: 데이터 검증 리포트
**담당**: QA Agent + Backend/API Agent  
**공수**: 3일 (24시간)  
**상세 작업**:

**2-9.1 검증 규칙 정의**
- 필수값 누락
- 범위 초과 (예: 불량률 > 100%)
- 중복 검사 (예: 같은 라인 중복 입력)
- 데이터 타입 불일치
- 외래키 참조 오류

**2-9.2 검증 로직 구현 (Backend)**
```typescript
interface ValidationResult {
  section: string
  totalRows: number
  validRows: number
  errors: {
    rowNum: number
    column: string
    error: string
    value: any
  }[]
  warnings: string[]
}
```

**2-9.3 리포트 생성**
- HTML 리포트: `ValidationReport_[timestamp].html`
- 포함 내용:
  - 부문별 검증 결과
  - 오류 목록 (행/컬럼 명시)
  - 경고 메시지
  - 다운로드 링크

**2-9.4 UI 표시**
- 업로드 후 자동 검증 리포트 표시
- 오류 행 강조 표시
- 재업로드 권유

**완료조건**:
- [ ] 검증 규칙 모두 정의됨
- [ ] 검증 로직 구현되고 테스트됨
- [ ] 리포트 생성/다운로드 가능

---

### 📋 Phase 3: 시스템 연동 설계 (W7~W14: 5/19~7/11)

#### 과제 3-1: PostgreSQL 스키마 설계 (Star Schema)
**담당**: Backend/API Agent  
**공수**: 5일 (40시간)  
**상세 작업**:

**3-1.1 요구사항 분석**
- 현재 데이터 구조 (각 대시보드별)
- 미래 시스템 연동 (SAP/MES/SVC/CRM)
- 성능 요구사항 (조회 응답시간 < 2초)

**3-1.2 Star Schema 설계**
```
Fact Tables:
- FactFinance (재무: 매출, 원가, 이익)
- FactProduction (생산: 생산량, 가동률, 비가동시간)
- FactSales (영업: 수주, 파이프라인, 고객)
- FactService (서비스: 장비, 워런티, 장애콜)
- FactQuality (품질: 불량, 검사)
- FactHR (인사: 인력, 생산성)

Dimension Tables:
- DimDate (날짜 차원)
- DimProduct (제품 차원)
- DimCustomer (고객 차원)
- DimDepartment (부서 차원)
- DimEmployee (직원 차원)
- DimLocation (위치 차원)
```

**3-1.3 스키마 정의 (DDL)**
- 각 테이블: PK, FK, Index
- 데이터 타입 결정
- Constraint 정의

**3-1.4 마이그레이션 전략**
- Phase 1~2 엑셀 데이터 → DB 마이그레이션
- 기존 API 응답 호환성 유지

**완료조건**:
- [ ] Star Schema ERD 완성됨
- [ ] DDL 스크립트 작성 완료
- [ ] 마이그레이션 계획 수립 완료

---

#### 과제 3-2: Drizzle ORM 도입 + 마이그레이션
**담당**: Backend/API Agent  
**공수**: 5일 (40시간)  
**상세 작업**:

**3-2.1 Drizzle 설정**
- `drizzle.config.ts` 생성
- PostgreSQL 드라이버 설정
- 마이그레이션 폴더 구조

**3-2.2 스키마 정의 (ORM)**
```typescript
// schema.ts
import { pgTable, serial, varchar, numeric, timestamp, foreignKey } from 'drizzle-orm/pg-core';

export const dimDate = pgTable('dim_date', {
  dateKey: serial('date_key').primaryKey(),
  date: timestamp('date').notNull(),
  year: numeric('year'),
  month: numeric('month'),
  ...
});

export const factFinance = pgTable('fact_finance', {
  financeKey: serial('finance_key').primaryKey(),
  dateKey: serial('date_key').references(() => dimDate.dateKey),
  ...
});
```

**3-2.3 마이그레이션 실행**
- 마이그레이션 파일 생성: `migrations/001_initial_schema.sql`
- 드라이 런(dry-run) 테스트
- 실행

**3-2.4 데이터 이관**
- 기존 엑셀 데이터 → DB 로드
- 검증: 행 수, 합계 값 확인
- 롤백 계획 수립

**완료조건**:
- [ ] Drizzle 설정 완료
- [ ] 스키마 정의됨
- [ ] 마이그레이션 성공적으로 실행됨
- [ ] 기존 데이터 이관 완료

---

#### 과제 3-3: API 엔드포인트 DB 연동 리팩토링
**담당**: Backend/API Agent  
**공수**: 5일 (40시간)  
**상세 작업**:

**3-3.1 API 엔드포인트 재설계**
```typescript
// 기존: /api/dashboard/finance → JSON 하드코딩
// 신규: /api/dashboard/finance → DB 쿼리

GET /api/financial-dashboard
  → SELECT * FROM fact_finance JOIN dim_date WHERE date >= ?
  
GET /api/financial-dashboard/drill/:productId
  → SELECT * FROM fact_finance WHERE productId = ? AND dateKey IN (...)
```

**3-3.2 쿼리 구현 (Drizzle)**
```typescript
export async function getFinancialData(startDate: Date, endDate: Date) {
  return db
    .select()
    .from(factFinance)
    .leftJoin(dimDate, eq(factFinance.dateKey, dimDate.dateKey))
    .where(and(
      gte(dimDate.date, startDate),
      lte(dimDate.date, endDate)
    ))
    .orderBy(desc(dimDate.date));
}
```

**3-3.3 응답 형식 호환성**
- 기존 프론트엔드 코드 변경 최소화
- 응답 구조 동일 유지
- gradual migration (동시 지원)

**3-3.4 성능 최적화**
- Index 생성 (자주 쿼리되는 컬럼)
- N+1 쿼리 문제 해결
- 응답시간 < 2초 검증

**완료조건**:
- [ ] 모든 API 엔드포인트 DB 연동됨
- [ ] 기존 응답 형식 유지됨
- [ ] 성능 요구사항 만족

---

#### 과제 3-4: 엑셀 업로드 → DB 저장 파이프라인
**담당**: Backend/API Agent  
**공수**: 3일 (24시간)  
**상세 작업**:

**3-4.1 파이프라인 설계**
```
엑셀 파일 수신
  ↓
검증 (데이터 품질 체크)
  ↓
정규화 (DB 스키마에 매핑)
  ↓
중복 제거 (기존 데이터와 비교)
  ↓
트랜잭션 내 INSERT/UPDATE
  ↓
로그 기록
  ↓
응답 (결과 요약)
```

**3-4.2 구현**
```typescript
async function uploadAndProcessExcel(file: Buffer, section: string) {
  const transaction = await db.transaction(async (trx) => {
    // 1. 파싱
    const data = parseExcel(file);
    
    // 2. 검증
    const validationResult = validateData(data, section);
    if (!validationResult.isValid) throw new Error(...);
    
    // 3. 기존 데이터 삭제 (또는 업데이트)
    await trx.delete(getFactTable(section))
      .where(eq(periodColumn, getCurrentPeriod()));
    
    // 4. 삽입
    await trx.insert(getFactTable(section)).values(data.rows);
    
    return { uploadId, section, rowsInserted: data.rows.length };
  });
  
  return transaction;
}
```

**3-4.3 에러 처리**
- 트랜잭션 롤백
- 상세 오류 로깅
- 부분 성공 여부 판단

**완료조건**:
- [ ] 파이프라인 구현됨
- [ ] 트랜잭션 안전성 확보됨
- [ ] 에러 처리 완벽

---

#### 과제 3-5: SAP 인터페이스 명세서 (FI/CO/SD/MM/PP)
**담당**: Backend/API Agent + PM (협의)  
**공수**: 5일 (40시간)  
**상세 작업**:

**3-5.1 SAP 모듈별 요구사항**

| 모듈 | 필요 데이터 | 추출 방식 |
|------|-----------|---------|
| **FI (재무)** | GL 계정, 매출, 원가, 경비 | BDC / RFC 호출 |
| **CO (원가)** | 수익성 분석, 예산, 원가센터 | Report ALV / RFC |
| **SD (영업)** | 수주, 청구, 고객 | Table VBAK/VBAP / RFC |
| **MM (자재)** | 구매발주, 입고, 재고 | Table EKKO/EKPO/MARD |
| **PP (생산)** | 생산계획, 생산지시, BOM | Table PLPO/AFPO / RFC |

**3-5.2 각 인터페이스 명세**

**FI 인터페이스**
```
Data Source: /SAPDMC/C_REPOST (GL Report)
Extraction: Monthly Schedule (매월 1일)
Fields:
  - ChartOfAccount
  - GLAccount
  - MonthlyAmount
  - CostCenter

Mapping:
  SAP.Amount → FactFinance.RevenueAmount
```

**CO 인터페이스**
```
Data Source: COPA (CO-PA)
Fields:
  - CostCenter
  - Product
  - SalesVolume
  - Cost
  - Profit

Mapping:
  SAP.CostCenter → DimDepartment.CostCenterCode
```

**SD 인터페이스**
```
Data Source: VBAK/VBAP (Sales Orders)
Fields:
  - Order Number
  - Customer
  - Amount
  - DeliveryDate

Mapping:
  SAP.VBAK-VBELN → FactSales.SalesOrderNumber
```

**MM 인터페이스**
```
Data Source: EKKO/EKPO (Purchase Orders)
Fields:
  - PO Number
  - Vendor
  - Amount
  - GoodsReceiptDate

Mapping:
  SAP.EKPO-NETWR → FactProcurement.POAmount
```

**PP 인터페이스**
```
Data Source: AFPO (Production Orders)
Fields:
  - Order Number
  - Product
  - TargetQuantity
  - Order Status

Mapping:
  SAP.AFPO-GMENGE → FactProduction.ProductionQuantity
```

**3-5.3 추출 방식 결정**
- Option A: RFC 호출 (실시간, 고비용)
- Option B: 테이블 직접 조회 (배치, 저비용)
- Option C: SAP BI System 활용 (표준)

**3-5.4 명세서 작성**
- 각 인터페이스별 상세 문서
- 필드 매핑 테이블
- 에러 처리 방안
- 테스트 계획

**완료조건**:
- [ ] SAP 5개 모듈 인터페이스 명세 완성됨
- [ ] 필드 매핑 검증됨
- [ ] 추출 방식 결정 완료

---

#### 과제 3-6: SVC 시스템 API 명세서
**담당**: Backend/API Agent + PM (SVC 담당자 협의)  
**공수**: 3일 (24시간)  
**상세 작업**:

**3-6.1 SVC 시스템 데이터 요구사항**
- 설치 장비 현황 (Equipment Inventory)
- 워런티 관리 (Warranty Management)
- 장애콜/유지보수 (Service Calls)
- 인력 배치 (Technician Assignment)

**3-6.2 API 명세**

```
GET /svc/api/equipment
Response: {
  equipmentId: string
  model: string
  serialNumber: string
  installationDate: date
  location: string
  status: "active" | "warranty_expired" | "inactive"
}

GET /svc/api/warranty/:equipmentId
Response: {
  warrantyStartDate: date
  warrantyEndDate: date
  coverage: string
  renewalStatus: "active" | "expired"
}

GET /svc/api/service-calls?period=YYYY-MM
Response: {
  callId: string
  equipmentId: string
  callDate: date
  issue: string
  resolution: string
  technicianId: string
}

GET /svc/api/technician-coverage
Response: {
  technicianId: string
  name: string
  assignedEquipmentCount: number
  coveredRegions: string[]
}
```

**3-6.3 통합 계획**
- SVC API 인증 방식 (API Key / OAuth)
- 호출 빈도 (일 1회, 월 1회 등)
- 에러 처리 방안

**완료조건**:
- [ ] SVC API 명세 완성됨
- [ ] 필드 매핑 정의됨
- [ ] 통합 일정 수립 완료

---

#### 과제 3-7: CRM(Salesforce) 연동 명세서
**담당**: Backend/API Agent + PM  
**공수**: 3일 (24시간)  
**상세 작업**:

**3-7.1 Salesforce 데이터 요구사항**
- 고객 정보 (Account)
- 기회/파이프라인 (Opportunity)
- 거래/수주 (Deal)
- 영업 활동 (Activity)

**3-7.2 API 명세**
```
GET /salesforce/api/accounts?modifiedAfter=YYYY-MM-DD
GET /salesforce/api/opportunities?status=Open&stage=Proposal
GET /salesforce/api/deals?closedDate_after=YYYY-MM-DD
```

**3-7.3 Salesforce REST API 활용**
- 인증: OAuth 2.0 (Connected App)
- 추출: SOQL (Salesforce Object Query Language)

**완료조건**:
- [ ] Salesforce API 명세 완성됨
- [ ] 인증 방식 결정 완료
- [ ] SOQL 쿼리 설계 완료

---

#### 과제 3-8: ETL 파이프라인 설계
**담당**: Backend/API Agent  
**공수**: 5일 (40시간)  
**상세 작업**:

**3-8.1 ETL 아키텍처 설계**
```
Source Systems:
├─ SAP (FI/CO/SD/MM/PP)
├─ MES (Production Data)
├─ SVC (Service Data)
├─ Salesforce (CRM)
└─ Excel (Temporary)

ETL Layer:
├─ Extract (SAP RFC / Table / API)
├─ Transform (Mapping / Aggregation)
├─ Load (PostgreSQL)

Orchestration:
├─ Scheduler (Cron / Airflow)
├─ Error Handling
├─ Logging & Monitoring
└─ Data Quality Checks
```

**3-8.2 데이터 흐름 정의**

| 소스 | 대상 | 빈도 | 지연 |
|------|------|------|------|
| SAP FI | FactFinance | 일 1회 (야간) | 24시간 |
| SAP CO | FactProduction | 일 1회 | 24시간 |
| MES | FactProduction | 실시간 | < 5분 |
| SVC API | FactService | 일 1회 | 24시간 |
| Salesforce | FactSales | 시간 1회 | 1시간 |

**3-8.3 기술 선택**
- **Option A**: Node.js + Bull Queue (경량)
- **Option B**: Apache Airflow (표준)
- **Option C**: Kafka (고성능, 스트리밍)

**추천**: Option B (Airflow) Phase 3 후반, Option A (Bull) Phase 3 초반

**3-8.4 설계 문서**
- ETL DAG 구조
- 각 Task 정의
- 에러 처리 및 재시도 전략
- SLA 정의

**완료조건**:
- [ ] ETL 아키텍처 정의됨
- [ ] 데이터 흐름 매핑 완료
- [ ] 기술 스택 결정 완료
- [ ] 설계서 작성 완료

---

#### 과제 3-9: BOM 기반 원가 구성비 설계
**담당**: Backend/API Agent + PM (협의)  
**공수**: 5일 (40시간)  
**상세 작업**:

**3-9.1 BOM 구조 이해**
```
SAP: STPO (Bill of Material)
├─ Material (제품)
├─ Component (부품)
├─ Quantity
├─ UOM
└─ ValuationClass
```

**3-9.2 원가 계산 로직**
```
제품 원가 = 
  재료비 (Material Cost × Quantity) +
  노동비 (Labor Hours × Rate) +
  가공비 (Process Cost) +
  간접비 (Overhead)

예시:
  제품 A 원가 = (재료: 5,000) + (노동: 2,000) + (가공: 1,000) + (간접: 1,000) = 9,000
  
  원가 구성비:
  - 재료: 55.6%
  - 노동: 22.2%
  - 가공: 11.1%
  - 간접: 11.1%
```

**3-9.3 데이터 구조 설계**
```sql
CREATE TABLE dim_bom (
  bomKey SERIAL PRIMARY KEY,
  productId VARCHAR,
  componentId VARCHAR,
  quantity NUMERIC,
  costElement VARCHAR,
  costAmount NUMERIC,
  validFrom DATE,
  validTo DATE
);

CREATE TABLE fact_cost (
  costKey SERIAL PRIMARY KEY,
  productId VARCHAR,
  costType VARCHAR, -- 'Material', 'Labor', 'Process', 'Overhead'
  costAmount NUMERIC,
  dateKey BIGINT
);
```

**3-9.4 대시보드 연동**
- 재무 대시보드: 원가 구성비 시각화
- 상세 드릴다운: 부품별 원가 분석
- 추이 차트: 월별 원가 변화

**3-9.5 설계서**
- BOM 데이터 수집 방식
- 원가 계산 공식
- 검증 기준
- 성능 고려사항

**완료조건**:
- [ ] BOM 기반 원가 계산 로직 설계됨
- [ ] 데이터 구조 정의됨
- [ ] 대시보드 연동 계획 수립 완료

---

### 📋 Phase 4: AI/예측 + 전사 확산 (W15~W26)

#### 과제 4-1: SAP FI/SD 연동 개발
**담당**: Backend/API Agent  
**공수**: 10일  
**상세**: 3-5 명세 기반 SAP 실시간/배치 연동 구현

---

#### 과제 4-2: MES 연동 개발
**담당**: Backend/API Agent  
**공수**: 10일  
**상세**: 생산 실시간 데이터 (가동률/생산량) MES 연동

---

#### 과제 4-3: SVC API 연동 개발
**담당**: Backend/API Agent  
**공수**: 7일  
**상세**: 3-6 명세 기반 SVC 데이터 동기화

---

#### 과제 4-4: AI 매출 예측 (시계열 분석)
**담당**: AI/Analytics Agent  
**공수**: 10일  
**상세**: Prophet/ARIMA 기반 월별/분기별 매출 예측

---

#### 과제 4-5: AI 이상 탐지 (KPI Alert)
**담당**: AI/Analytics Agent  
**공수**: 7일  
**상세**: KPI 임계값 자동 설정 + Alert 조건 정의

---

#### 과제 4-6: 자연어 질의 LLM 연동
**담당**: AI/Analytics Agent  
**공수**: 10일  
**상세**: Claude API 활용한 NLQ 기능 (예: "지난달 매출은?" → SQL 자동 생성)

---

#### 과제 4-7: 자동 월간 보고서 생성
**담당**: AI/Analytics Agent  
**공수**: 5일  
**상세**: 월말 자동 PDF 리포트 생성 + 이메일 발송

---

#### 과제 4-8: 모바일 PWA
**담당**: Frontend/UI Agent  
**공수**: 5일  
**상세**: 오프라인 지원 + 앱 설치 가능한 PWA 구현

---

## IV. 에이전트별 주간 투입 계획

### Phase 1 (W1~W2)

```
Frontend/UI:    ████████████████ (주요 투입)
Data/ETL:       ██ (보조)
Backend/API:    ██ (보조)
QA:             ██ (보조)
Infrastructure: ░░ (대기)
AI/Analytics:   ░░ (대기)
PM:             ██ (진행중)
```

### Phase 2 (W3~W6)

```
Frontend/UI:    ████░░░░ (감소)
Data/ETL:       ████████ (주요 투입)
Backend/API:    ████████ (주요 투입)
QA:             ████░░░░ (증가)
Infrastructure: ██░░░░░░ (부분)
AI/Analytics:   ░░░░░░░░ (대기)
PM:             ████░░░░ (주간 미팅)
```

### Phase 3 (W7~W14)

```
Frontend/UI:    ░░░░░░░░ (유지보수만)
Data/ETL:       ████░░░░ (감소)
Backend/API:    ████████ (주요 투입)
QA:             ████████ (주요 투입)
Infrastructure: ████░░░░ (증가)
AI/Analytics:   ░░░░░░░░ (설계단계)
PM:             ██░░░░░░ (미팅만)
```

### Phase 4 (W15~W26)

```
Frontend/UI:    ████░░░░ (AI/보고서)
Data/ETL:       ██░░░░░░ (유지보수)
Backend/API:    ████░░░░ (SAP/MES)
QA:             ████████ (지속)
Infrastructure: ██░░░░░░ (모니터링)
AI/Analytics:   ████████ (주요 투입)
PM:             ██░░░░░░ (미팅)
```

---

## V. 성공 기준 및 KPI

### Phase별 성공 기준

| Phase | 기준 | 목표 |
|-------|------|------|
| **0** | 배포 + 보안 | ✅ 배포 성공, 보안 기본 조치 완료 |
| **1** | 대표 요구사항 충족 | ✅ 7개 과제 모두 구현 완료 |
| **2** | 엑셀 데이터 대량 처리 | ✅ 4개 부문 업로드 기능 + 검증 리포트 |
| **3** | DB/API 리팩토링 완료 | ✅ PostgreSQL 운영, 5개 시스템 명세 |
| **4** | 실 데이터 전사 확산 | ✅ 모든 부문 실데이터 + AI 기능 |

### 월별 진도율

```
2026년
├─ 4월 (Phase 0~1): 30% → 60%
├─ 5월 (Phase 1~2): 60% → 100%
├─ 6월 (Phase 3): 100% → 40% (새 기능)
├─ 7월 (Phase 3~4): 40% → 70%
├─ 8월 (Phase 4): 70% → 90%
└─ 9월 (Phase 4): 90% → 100%
```

---

## VI. 리스크 및 완화 전략

| 리스크 | 확률 | 영향 | 완화 전략 |
|--------|:----:|:----:|---------|
| 캐파/생산성 정의 지연 | 높음 | 높음 | 목업 선개발 + TF 병렬 진행 |
| SAP 연동 접근권 제한 | 중간 | 높음 | 엑셀 업로드 병행 유지 |
| DB 마이그레이션 오류 | 중간 | 높음 | 롤백 계획 + 테스트 환경 사전 구축 |
| 요구사항 변경 | 높음 | 중간 | 주간 회의 + 우선순위 재정렬 |
| 데이터 품질 문제 | 중간 | 중간 | 검증 리포트 + 자동 체크 |

---

**문서 이력:**

| 버전 | 일자 | 내용 |
|:----:|------|------|
| 1.0 | 2026-04-03 | 에이전트 분배 + 상세 WBS 작성 |

