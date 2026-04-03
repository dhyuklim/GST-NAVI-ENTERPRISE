# Phase 0 산출물 체크리스트

**기간**: 2026-04-03 ~ 2026-04-04  
**담당**: Frontend/UI Agent, Infrastructure Agent, PM Agent  
**목표**: 배포 + 보안 기본 조치 완료

---

## 📋 산출물 목록

| # | 산출물명 | 타입 | 저장위치 | 담당 | 예정일 | 상태 | 검증 |
|---|---------|------|---------|------|--------|------|------|
| 0-1-1 | Dashboard.tsx 수정 | 코드 | `P0/code/` | Frontend | 4/3 | ⏳ | - |
| 0-1-2 | ServiceDashboard.tsx 수정 | 코드 | `P0/code/` | Frontend | 4/3 | ⏳ | - |
| 0-1-3 | ProductionDashboard.tsx 수정 | 코드 | `P0/code/` | Frontend | 4/3 | ⏳ | - |
| 0-1-4 | Vercel 배포 완료 보고서 | 보고서 | `P0/deployment/` | Frontend | 4/3 | ⏳ | - |
| 0-2-1 | .env.example | 설정 | `P0/security/` | Infrastructure | 4/3 | ⏳ | - |
| 0-2-2 | JWT 환경변수 설정 가이드 | 문서 | `P0/security/` | Infrastructure | 4/3 | ⏳ | - |
| 0-2-3 | 보안 설정 체크리스트 | 문서 | `P0/security/` | Infrastructure | 4/3 | ⏳ | - |
| 0-3-1 | middleware/auth.ts | 코드 | `P0/code/middleware/` | Backend | 4/4 | ⏳ | - |
| 0-3-2 | API 인증 테스트 결과 | 테스트 | `P0/tests/` | QA | 4/4 | ⏳ | - |
| 0-4-1 | README.md 최신본 | 문서 | `P0/docs/` | PM | 4/4 | ⏳ | - |
| 0-4-2 | 개발 가이드 | 문서 | `P0/docs/` | PM | 4/4 | ⏳ | - |

---

## ✅ 완료 기준

### 각 산출물 검증 항목

**0-1: 로컬 변경 4건 푸시**
- [ ] 3개 컴포넌트 파일 수정 완료
- [ ] Vercel 배포 성공
- [ ] 배포 URL에서 모든 변경사항 확인됨
- [ ] 배포 스크린샷 저장됨

**0-2: JWT 환경변수화**
- [ ] .env.local 파일 생성됨
- [ ] .env.example에 변수명 명시됨
- [ ] Vercel 대시보드에 환경변수 설정됨
- [ ] 로컬/배포 환경 모두 정상 작동

**0-3: API 인증 미들웨어**
- [ ] middleware/auth.ts 파일 생성됨
- [ ] 모든 API 라우트에 적용됨
- [ ] 토큰 검증 로직 동작 확인됨
- [ ] 테스트 커버리지 80% 이상

**0-4: 문서 정리**
- [ ] README.md 현행화 (Phase 0~1 내용)
- [ ] 빠른 시작 가이드 작성됨
- [ ] 개발 환경 설정 방법 명시됨
- [ ] 팀 문서 링크 정리됨

---

## 📝 생성 가이드

### 각 산출물별 생성 방법

#### 0-1: 로컬 변경 4건 병합
```bash
# 1. 로컬 수정 파일들을 코드 폴더에 복사
cp src/components/dashboards/Dashboard.tsx OUTPUT/P0_즉시실행/code/Dashboard.tsx
cp src/components/dashboards/ServiceDashboard.tsx OUTPUT/P0_즉시실행/code/ServiceDashboard.tsx
cp src/components/dashboards/ProductionDashboard.tsx OUTPUT/P0_즉시실행/code/ProductionDashboard.tsx

# 2. 변경 이력 주석 추가 (각 파일 상단)
# - Dashboard.tsx: 판가 트렌드(ASP) 차트 + 기간 비교 모드
# - ServiceDashboard.tsx: 법인별 장비 + 워런티 + 커버리지
# - ProductionDashboard.tsx: 캐파 대비 가동률

# 3. Vercel 배포
git push origin master
# → Vercel 자동 배포

# 4. 배포 보고서 생성
# OUTPUT/P0_즉시실행/deployment/Deployment_Report_20260403.md
```

#### 0-2: JWT 환경변수화
```bash
# 1. .env.example 파일 생성
# OUTPUT/P0_즉시실행/security/.env.example
JWT_SECRET=your_jwt_secret_here
DATABASE_URL=postgres://...

# 2. 보안 설정 가이드 작성
# OUTPUT/P0_즉시실행/security/Security_Setup_Guide.md

# 3. 보안 체크리스트
# OUTPUT/P0_즉시실행/security/Security_Checklist.md
```

#### 0-3: API 인증 미들웨어
```bash
# 1. middleware/auth.ts 생성
# OUTPUT/P0_즉시실행/code/middleware/auth.ts

# 2. 테스트 작성
# OUTPUT/P0_즉시실행/tests/api_auth_test.ts

# 3. 테스트 실행 결과 저장
# OUTPUT/P0_즉시실행/tests/api_auth_test_results.json
```

#### 0-4: 문서 정리
```bash
# 1. README.md 업데이트
# OUTPUT/P0_즉시실행/docs/README_latest.md

# 2. 개발 가이드 작성
# OUTPUT/P0_즉시실행/docs/DEVELOPMENT_GUIDE.md

# 3. 문서 네비게이션 맵
# OUTPUT/P0_즉시실행/docs/DOCUMENTATION_MAP.md
```

---

## 🔍 검증 프로세스

### 매일 확인 (D-1, D-DAY)

```
4월 3일 (목)
├─ [ ] 0-1: 4개 파일 수정 완료
├─ [ ] 0-2: JWT 환경변수 설정 완료
├─ [ ] 0-3: API 인증 미들웨어 코드 작성 시작
└─ [ ] 0-4: 문서 정리 시작

4월 4일 (금) — 최종 마감
├─ [ ] 0-1: Vercel 배포 완료 + 스크린샷
├─ [ ] 0-2: Vercel 환경변수 설정 완료
├─ [ ] 0-3: API 테스트 완료 (모두 통과)
├─ [ ] 0-4: 문서 최종 검수 완료
└─ [ ] 모든 산출물 GitHub 커밋 완료
```

### PM 최종 검수

- [ ] 모든 산출물 생성됨
- [ ] 파일명 규칙 준수
- [ ] 품질 기준 충족
- [ ] 배포 성공 확인
- [ ] 문서화 완전

---

## 📊 진도 현황

### 현재 상태

```
████░░░░░░ 40% (04/03 기준)

생성됨:     4개 산출물
예정:       7개 산출물
완료 기한:  2026-04-04
```

---

**주간 마감 확인**: 2026-04-04 18:00 KST
