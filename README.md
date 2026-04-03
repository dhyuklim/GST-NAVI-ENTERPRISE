# GST NAVI Enterprise

> 코딩 없이 운영 가능한 제조업 특화 BI 플랫폼

## 프로젝트 개요

| 항목 | 내용 |
|------|------|
| 프로젝트명 | GST NAVI Enterprise BI Platform |
| 비전 | 노코드 BI — 시스템 연동/차트 설계/지표 계산을 코딩 없이 운영 |
| 기간 | 24개월 (2026-04 ~ 2028-03) |
| 기술 스택 | React 19 + TypeScript + PostgreSQL + Redis + ECharts |
| 인프라 | Xeon Silver 16Core/64GB/Rocky Linux 9 (온프레미스) |
| 라이선스 | **100% 오픈소스 (비용 ₩0)** |

## 기술 스택

| 레이어 | 기술 | 라이선스 |
|--------|------|---------|
| Frontend | React 19 + Vite 6 + Tailwind CSS 4 | MIT |
| Charts | Apache ECharts 6 | Apache 2.0 |
| State | Zustand 5 | MIT |
| Backend | Express 4 + TypeScript | MIT |
| ORM | Drizzle ORM | Apache 2.0 |
| Database | PostgreSQL 16 | PostgreSQL |
| Cache | Redis 7 | BSD |
| AI | Python + FastAPI + scikit-learn | MIT/BSD |
| Container | Docker + Docker Compose | Apache 2.0 |
| Monitoring | Grafana + Prometheus | AGPL/Apache |
| OS | Rocky Linux 9 | BSD |

## 디렉토리 구조

```
GST-NAVI-ENTERPRISE/
├── docs/                    # 기획/설계 문서
├── src/                     # 프론트엔드 소스
│   ├── components/
│   │   ├── admin/           # 관리 기능
│   │   ├── common/          # 공통 컴포넌트
│   │   ├── dashboard/       # 대시보드 뷰어
│   │   ├── designer/        # 노코드 디자이너
│   │   └── report/          # 보고서
│   ├── hooks/               # React 훅
│   ├── services/            # API 클라이언트
│   ├── stores/              # Zustand 스토어
│   ├── types/               # TypeScript 타입
│   ├── utils/               # 유틸리티
│   ├── config/              # 설정
│   └── lib/                 # 라이브러리
├── server/                  # 백엔드 소스
│   ├── services/            # 비즈니스 로직
│   ├── routes/              # API 라우트
│   ├── middleware/           # 미들웨어
│   ├── connectors/          # 데이터소스 커넥터
│   ├── etl/                 # ETL 파이프라인
│   ├── ai/                  # AI/ML 서비스
│   └── models/              # DB 모델 (Drizzle)
├── docker/                  # Docker 설정
├── scripts/                 # DB 마이그레이션/시드
├── tests/                   # 테스트
│   ├── unit/
│   ├── e2e/
│   └── api/
├── public/                  # 정적 파일
└── data/                    # 런타임 데이터 (gitignore)
```

## 시작하기

```bash
# 의존성 설치
npm install

# 환경 변수 설정
cp .env.example .env

# DB 마이그레이션
npm run db:migrate

# 개발 서버 실행
npm run dev:all
```

## 문서

- [기술 설계서](docs/GST_NAVI_BI_플랫폼_기술설계서.md)
- [통합 개발 계획](docs/GST_NAVI_통합_개발계획_v2.md)
- [아키텍처 메뉴 구성](docs/GST_NAVI_아키텍처_메뉴구성.md)
- [회의록 분석](docs/GST_NAVI_회의록_분석_20260403.md)

## POC 참조

기존 POC 프로젝트: `../gst-navi (v2)/`
- 22,447 LOC / 32 컴포넌트 / 실 운영 중
- https://gst-navi-v2.vercel.app
