# ISA-95 Layer 3~4 웹 업무시스템 UI/UX 벤치마크 조사

- **작성일**: 2026-07-24
- **대상**: MES / ERP / APS 등 Layer 3~4 Web 기반 업무시스템
- **목적**: infinitPINN·소르테크 UI/UX 시안 설계를 위한 한국·글로벌 벤치마킹
- **산출물**: 본 보고서 + 화면 유형별 PPT (`deliverables/SorTech_Layer34_UI_Benchmark.pptx`)

---

## 1. 조사 범위 (ISA-95)

| Layer | 영역 | 대표 시스템 | UI 핵심 사용자 |
|-------|------|-------------|----------------|
| L4 | 경영·자원계획 | ERP, SCM, 재무/구매/영업 | 경영진, 구매, 영업, 생산계획 |
| L3 | 제조운영 | MES/MOM, APS, WMS, QMS | 현장감독, 오퍼레이터, 품질, 스케줄러 |
| L2 이하 | 제어·설비 | SCADA/HMI, PLC | (참고만, 본 조사 핵심 아님) |

본 조사는 **브라우저 기반 웹 UI**를 중심으로 하며, 설치형 클라이언트라도 웹으로 전환 중인 제품은 포함한다.

---

## 2. 한국 시장 제품 맵

### 2.1 MES / MOM (L3)

| 제품 | 벤더 | 포지셔닝 | UI/UX 특징 |
|------|------|----------|------------|
| Nexplant MESplus | 미라콤아이앤씨 | 국내 MES 대표, 21업종·451+ 코어화면 | 플랫폼 공통 UX, 모듈 선택 탑재, 클라우드/온프렘 |
| Nexplant MES | 삼성SDS | 반도체·디스플레이·2차전지 | 스케줄링/디스패칭, 실시간 제조운영, Tool Control 연동 |
| Factova MES | LG CNS | 대기업 스마트팩토리 → 중견 확산 | AI/빅데이터 품질·이상감지, 설비 데이터 통합(Control) |
| K-System MES | 영림원소프트랩 | ERP+MES 통합 (중견) | ERP 작업지시→MES 배분→POP 실적, 웹·현장 연계 |
| MES 10 | 더존비즈온 | ERP10 확장 MES | ERP 통합, 현장 전자결재, MESA 가이드 준수 |
| Frame7 MES/POP | 이맥스솔루션 | 중견·중소 통합 | Kiosk 현장화면, ERP+MES+APS+FA 단일 DB |

### 2.2 ERP (L4)

| 제품 | 벤더 | 포지셔닝 | UI/UX 특징 |
|------|------|----------|------------|
| Amaranth 10 | 더존비즈온 | ERP+그룹웨어+문서 올인원 | TANGO 디자인 원칙, Portal/Tango View, 포틀릿 개인화, 통합검색 |
| ERP 10 / DEWS | 더존비즈온 | 구축형 확장 ERP | 포털·모바일·API·웹 개발도구 |
| K-System Ace / Ace I&I | 영림원소프트랩 | 한국형 ERP, AI 통합 | 프로세스 시각화 메뉴, 경영자정보(EIS), SaaS(시스템에버) |
| 비즈메카 등 SaaS ERP | 더존 외 | 중소 클라우드 | 단순 메뉴·폼 중심, 모바일 우선 |

### 2.3 APS (L3~L4 경계)

| 제품 | 벤더 | 포지셔닝 | UI/UX 특징 |
|------|------|----------|------------|
| MOZART | 브이엠에스솔루션스 | AI·디지털트윈 APS (반도체/디스플레이 강세) | 시뮬레이션, What-if, Management Console, 계획 시각화 |
| Asprova (국내 도입 다수) | Asprova | 고속 다공정 스케줄링 | Gantt 중심, 부하/재고 연동 뷰 |
| 영림원/이맥스 내장 APS | 영림원, 이맥스 등 | ERP 패키지형 | ERP 화면 스타일 + 단순 일정표 |

### 2.4 한국 시장 UI 공통 관찰

1. **ERP↔MES 통합 패키지** 강조 (영림원, 더존, 이맥스) — 화면 톤이 ERP 그리드형으로 수렴하는 경향.
2. **포털·결재·검색**이 L4 UX의 차별점 (Amaranth 10이 대표).
3. **대기업 MES**는 기능·신뢰성 중심, 공개 UI 시안이 적고 SI 커스터마이징 비율 높음.
4. **현장(POP/Kiosk)** 화면은 대형 버튼·터치·실적 입력 중심 — 사무 UI와 분리.
5. **APS**는 MOZART·Asprova 등 전문 솔루션이 Gantt/시뮬레이션 UI를 주도.

---

## 3. 글로벌 시장 제품 맵

### 3.1 MES / MOM

| 제품 | 벤더 | UI/UX 특징 |
|------|------|------------|
| Opcenter Execution / Opcenter X | Siemens | Mendix low-code 개인화, Role-based Shopfloor, Quality 도면 오버레이 |
| FactoryTalk ProductionCentre | Rockwell | Plant model 기반 OEE 대시보드, 라인→설비 드릴다운 |
| AVEVA MES + OMI | AVEVA | Web Portal, 위젯형 대시보드, CONNECT 시각화 |
| Plex / Infor MES | Rockwell/Infor | SaaS, 모바일·오케스트레이션 UI |
| Critical Manufacturing MES | Critical | 반도체/하이테크, 구성 가능 화면 |

### 3.2 ERP

| 제품 | 벤더 | UI/UX 특징 |
|------|------|------------|
| SAP S/4HANA + Fiori | SAP | Design System(Horizon), Floorplan, 태스크 앱, 임베디드 애널리틱스 |
| Oracle Cloud Manufacturing | Oracle | Redwood UX, 역할 대시보드 |
| Infor CloudSuite | Infor | 산업 템플릿, Coleman AI 어시스턴트 |
| Microsoft Dynamics 365 | Microsoft | Fluent UI, Power Platform 확장 |
| NetSuite Manufacturing | Oracle | 그리드 스케줄러, 색상 상태바 |

### 3.3 APS

| 제품 | 벤더 | UI/UX 특징 |
|------|------|------------|
| Opcenter APS | Siemens | Planning Board, COM/API, 제약 스케줄 |
| SAP PP/DS Scheduling Board | SAP | Fiori Gantt2.0, Side panel, DnD, Heuristics |
| DELMIA Ortems | Dassault | VIC 설정, What-if, ERP/MES/PLM 연동 |
| PlanetTogether | AVEVA | DnD Gantt, 관련 작업 자동 이동, What-if 슬라이더 |
| Aspen Schedule Explorer | AspenTech | 웹 협업, 스케줄 코멘트, 플랜트↔스케줄러 정렬 |
| Asprova / CyberPlan | 각사 | 고속 Gantt, Visual Analytics |

### 3.4 글로벌 UI 공통 DNA

- **Design System** (Fiori, Redwood, Fluent, Mendix 템플릿)
- **Role / Task based** 앱 분해
- **Exception-first** + ISA-101 색상 철학 (관제·현황판)
- **Composable / Low-code** 현장 맞춤
- **Digital Thread** (ERP↔MES↔APS↔PLM deep link)
- **AI 추천은 기존 워크플로에 임베드**

---

## 4. 화면 유형 분류 (Screen Taxonomy)

infinitPINN 시안 설계용으로 **12개 화면 유형**으로 정규화한다.

| ID | 화면 유형 | 주 레이어 | 주 사용자 | 대표 벤치마크 |
|----|-----------|-----------|-----------|---------------|
| S01 | 역할 포털 / 홈 | L4/L3 | 전 역할 | Amaranth Portal, Fiori Launchpad, Opcenter Portal |
| S02 | 경영·운영 Command Center | L4/L3 | 경영진·공장장 | SAP Analytics, Rockwell OEE Dashboard |
| S03 | 라인/공정 현황판 (Andon) | L3 | 감독·현장 | MES Andon, High-Performance HMI |
| S04 | 작업지시 실행 (Shopfloor) | L3 | 오퍼레이터 | Opcenter Shopfloor, POP Kiosk |
| S05 | 마스터/트랜잭션 그리드 | L4 | 사무·관리 | ERP 그리드, Fiori List Report |
| S06 | 상세 Object 페이지 | L4/L3 | 사무·품질 | Fiori Object Page, MES Lot 상세 |
| S07 | APS 스케줄 Gantt | L3 | 스케줄러 | SAP PSB, PlanetTogether, MOZART |
| S08 | What-if / 시나리오 비교 | L3 | 플래너 | Ortems, PlanetTogether, MOZART |
| S09 | 품질검사·NCR | L3 | 품질 | Opcenter Quality, eDHR |
| S10 | 자재·재고·Trace | L3/L4 | 물류·품질 | WMS/MES Lot genealogy |
| S11 | 설비·보전 | L3 | 설비 | EAM/MES Equipment |
| S12 | 리포트·분석·내보내기 | L4/L3 | 분석가 | Embedded analytics, BI |

---

## 5. 화면 유형별 UI/UX 특징 상세

### S01 역할 포털 / 홈
- **레이아웃**: 타일/포틀릿/카드 그리드, 상단 통합검색·알림
- **특징**: 개인화 배치, 미결 업무·승인·알람 우선, 앱 런치패드
- **한국**: Amaranth 포털·통합검색이 강세
- **글로벌**: Fiori Launchpad, Opcenter Mendix Portal
- **소르테크 시사점**: “메뉴 트리”가 아닌 **역할×오늘 할 일** 진입점

### S02 Command Center
- **레이아웃**: KPI 카드 + 트렌드 + 예외 리스트 + 공장/라인 맵
- **특징**: Overview→Drill-down, 멀티사이트, 실시간 갱신
- **주의**: 장식용 3D/무지개색 지양, 이상만 강조
- **소르테크 시사점**: 생산·품질·스케줄·에너지 KPI 통합 “한 장 관제”

### S03 라인/공정 현황판
- **레이아웃**: 대형 디스플레이, 상태 블록, Andon 색상
- **특징**: 원거리 가독성, 최소 인터랙션, 교대/라인 단위
- **소르테크 시사점**: 사무 UI와 **시각 언어를 분리** (터치·관제 모드)

### S04 작업지시 실행
- **레이아웃**: 현재 작업 1개 중심, 대형 CTA, 단계 위저드
- **특징**: 바코드/NFC, 전자서명, 문서/도면 첨부, 오프라인 내성
- **소르테크 시사점**: 글러브·조명 환경을 가정한 **물리 UX**

### S05 마스터/트랜잭션 그리드
- **레이아웃**: 필터바 + 테이블 + 툴바 액션 + 엑셀형 편집
- **특징**: 대량 데이터, 컬럼 개인화, 일괄처리, 유효성 검증
- **소르테크 시사점**: ERP 밀도를 유지하되 **빈 상태·오류 메시지 품질**로 차별

### S06 Object 상세 페이지
- **레이아웃**: 헤더 KPI + 탭(일반/이력/문서/관계) + 액션바
- **특징**: 컨텍스트 유지 deep link (오더↔Lot↔설비)
- **소르테크 시사점**: Digital Thread의 **허브 화면**

### S07 APS Gantt
- **레이아웃**: 좌 리소스 테이블 + 우 타임라인, 글로벌 툴바, Side panel
- **특징**: DnD, 제약 위반 하이라이트, pegging 관계선, 부하 히트맵
- **학습**: SAP Fiori — 과도한 팝업보다 **Side panel + flat hierarchy**가 유효
- **소르테크 시사점**: 스케줄 “예쁨”보다 **의사결정 속도**(색·패턴·범례)

### S08 What-if / 시나리오
- **레이아웃**: A/B 병치 Gantt 또는 KPI 델타 테이블
- **특징**: 시나리오 저장/비교, 최적화 슬라이더, 적용/롤백
- **소르테크 시사점**: AI 추천 시 **왜 이 계획인가** 설명 패널 필수

### S09 품질·NCR
- **레이아웃**: 검사 체크리스트 + 이미지/도면 + Pass/Fail CTA
- **특징**: 규격 대비 측정값, 부적합 워크플로, 감사추적
- **소르테크 시사점**: 규제 산업 대비 **전자기록 UX**

### S10 자재·Trace
- **레이아웃**: 재고 그리드 + Genealogy 트리/그래프
- **특징**: Lot/Serial 추적, 홀드/릴리즈, FIFO 시각화
- **소르테크 시사점**: 리콜 시나리오용 **역추적 원클릭**

### S11 설비·보전
- **레이아웃**: 설비 상태 + 알람 + 작업지시 연계
- **특징**: 가동/정지 사유, PM 일정, 예지보전 점수
- **소르테크 시사점**: MES 실적과 보전 티켓의 **양방향 링크**

### S12 리포트·분석
- **레이아웃**: 위젯 캔버스 또는 고정 리포트 + 내보내기
- **특징**: 임베디드 차트, 스케줄 리포트, Excel/PDF
- **소르테크 시사점**: 트랜잭션 화면에 **인라인 인사이트** 삽입

---

## 6. 벤치마크 매트릭스 (요약)

| 화면유형 | 한국 강점 레퍼런스 | 글로벌 강점 레퍼런스 | 피해야 할 패턴 |
|----------|--------------------|----------------------|----------------|
| S01 포털 | Amaranth 개인화·검색 | Fiori Launchpad | 깊은 메뉴 트리만 제공 |
| S02 관제 | 대기업 MES 현황판 | Rockwell/AVEVA 위젯 | 장식용 다크네온 대시보드 |
| S03 Andon | POP/Kiosk | ISA-101 HMI | 작은 폰트·과도한 차트 |
| S04 실행 | 현장 Kiosk | Opcenter Shopfloor | 사무 폼을 그대로 터치 |
| S05 그리드 | K-System/더존 ERP | Fiori List Report | 검증 없는 엑셀 복제 |
| S07 Gantt | MOZART, Asprova | SAP PSB, PlanetTogether | 팝업 과다, 범례 부재 |
| S08 What-if | MOZART 시뮬 | Ortems, PlanetTogether | 결과만 보여주고 근거 없음 |

---

## 7. 소르테크·infinitPINN UI 차별화 방향 (초안)

1. **Layer 3~4를 하나의 Design System으로** — 사무(ERP)와 현장(MES)이 톤은 공유하되 **밀도·터치·정보계층은 분리**.
2. **Persona × Task × Device** 매트릭스로 화면 카탈로그 작성 (모듈명 중심 지양).
3. **예외·조치 중심** — “데이터 전시”가 아닌 “지금 해야 할 일”.
4. **스케줄·실행·품질을 Digital Thread로 연결** — Object Page가 허브.
5. **AI/PINN 결과는 Side panel 설명형 UX** — 블랙박스 추천 지양.
6. **한국형 업무습관 반영** — 결재·엑셀친화·통합검색 + 글로벌 Design System 품질.

---

## 8. 참고 링크 (선별)

### 한국
- https://miracom-inc.com/smartfactory/mes/
- https://www.samsungsds.com/kr/mes/nexplant-mes.html
- https://www.ksystem.co.kr/k-system-mes/
- https://www.douzone.com/product/mes10.jsp
- https://mv.amaranth10.co.kr/scene_5_en.html
- https://vms-solutions.com/kr/product/mozart.php
- https://www.newswire.co.kr/newsRead.php?no=866946 (LG CNS Factova)

### 글로벌
- https://www.siemens.com/en-us/products/opcenter
- https://www.sap.com/design-system/fiori-design-web
- https://www.rockwellautomation.com/en-us/products/software/factorytalk/operationsuite/mes/productioncentre.html
- https://www.aveva.com/en/products/system-platform/
- https://www.3ds.com/products/delmia/ortems
- https://www.planettogether.com/aps/gantt-charts-as-a-tool-for-production-planning-and-control
- https://www.aspentech.com/en/products/msc/aspen-schedule-explorer
- https://www.isa.org/standards-and-publications/isa-standards/isa-101-standards

### 기존 수집본
- `research/manufacturing-business-system-ui-report.md` (글로벌 링크·트렌드 상세)

---

*공개 웹 자료(벤더 사이트, 뉴스, 디자인 가이드, UX 논문/블로그) 기반. 상용 제품의 전체 화면 갤러리는 비공개인 경우가 많아, UI 패턴은 공식 데모·릴리즈·가이드에서 추론함.*
