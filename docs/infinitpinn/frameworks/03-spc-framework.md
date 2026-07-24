# 03. SPC Framework

InfinitPINN **SPC Framework** — Statistical Process Control(통계적 공정관리) 및 품질 데이터 운영의 근간.

원본 매핑: Operation Layer 약어 SPC/FDC/EDC(p.7), Timescale·계측 데이터, AI Anomaly/검사, 품질 권한·감사

---

## 1. 제품 정의

| 항목 | 내용 |
|------|------|
| 약어 | **SPC** = Statistical Process Control (원본 p.7) |
| 목적 | 공정·계측 데이터의 통계적 감시, 관리도·룰, 이상/부적합 이벤트 연계 |
| 포지션 | 3종 중 **품질·공정 안정성 도메인 FW**. UI는 Web에 얹고, 데이터는 Edge/EDC에서 유입 |
| 인접 모듈 | EDC(계측 수집), FDC(고장·이상 분류), Edge AI(검사/Anomaly) |

> 원본에 “SPC Framework”라는 제품 박스 문구는 없다.  
> 플랫폼 Operation Layer에 SPC가 명시되고, 품질·시계열·이상탐지가 핵심이므로 **3종 제작 대상의 품질 FW**로 문서화한다.

---

## 2. 범위 (In / Out)

### In Scope
- 계측/검사 데이터 모델 (Lot·설비·특성치·스펙)
- 관리도(Control Chart) 유형·관리한계 계산 엔진
- Western Electric / Nelson 등 룰 엔진 (스펙은 후속)
- 스펙(USL/LSL)·타겟·샘플링 계획 마스터
- OOC(Out of Control)·OOS 이벤트 발행 (Kafka)
- Web용 SPC API·차트 데이터 API
- Timescale 기반 시계열 저장·집계
- FDC/이상 이벤트와 상관 조회 훅
- 품질 ACTION 권한 (판정, 홀드, 릴리즈 — Permission 확장)

### Out of Scope
- 범용 포털·사용자 관리 → **Web / Core**
- PLC 직결 수집 → **Edge** (SPC는 소비)
- 전체 MES 실행(POM) → Operation 타 모듈

---

## 3. 아키텍처 슬롯

```
Edge / EDC ──측정·검사 데이터──► Kafka / REST
                                      │
                              ┌───────▼────────┐
                              │  SPC Framework │
                              │  ingest · chart│
                              │  rules · alert │
                              │  Timescale DB  │
                              └───────┬────────┘
                                      │ OOC event / API
                         ┌────────────┼────────────┐
                         ▼            ▼            ▼
                       Web UI      FDC/MICC     AI Anomaly
                     (관리도)      (상관)      (보조신호)
```

원본 AI Platform의 “검사자동화 / Anomaly detection”과 SPC 이벤트는 **상호 보완** (통계 룰 vs 학습 기반).

---

## 4. 데이터·Persistence

| 데이터 | 저장 후보 | 접근 |
|--------|-----------|------|
| 원시 계측 시계열 | TimescaleDB | JDBC/배치 ingest |
| 스펙·차트 설정 | PostgreSQL MDM | JPA |
| OOC 이벤트·조치 Hist | PG + Hist | `saveWithHist` |
| 집계 롤업 | Timescale continuous aggregate | Query |

대량 적재는 원본 Persistence 가이드상 **JDBC batch** 경로.

---

## 5. Web Framework와의 UI 계약

| Web 화면 | SPC 제공 |
|----------|----------|
| S09 품질 | 관리도, 판정, NCR 연계 진입 |
| S02 MICC | OOC 알람 타일·드릴다운 |
| S05/S06 | 스펙·샘플링 마스터, 특성치 Object |
| S12 | 품질 감사·조치 이력 |

Permission: `MENU`=SPC 모듈, `ACTION`=판정/홀드/스펙수정, `API`=차트·ingest.

---

## 6. Edge / AI와의 계약

| 상대 | 계약 |
|------|------|
| Edge | 특성치·검사 결과 토픽 (공장/설비/Lot/특성/값/단위/시각) |
| FDC | 설비 이상과 OOC 동시 발생 시 상관 ID(`trace_id`) |
| AI | Anomaly score를 SPC 보조 신호로 오버레이 (마스킹·비식별 요구 적용) |

---

## 7. 보안·거버넌스

- 고객사·제품명 등: AI 학습 전 **De-identification** (원본 요구)
- 화면/API: Dynamic Masking 후보
- 다운로드·반출: 데이터 활용 권한 **TBD**

---

## 8. 제작 산출물 체크리스트

- [ ] SPC 도메인 모델 (Characteristic, Spec, Sample, Chart, Rule, Event)
- [ ] Ingest API + Kafka consumer
- [ ] Control chart 계산 라이브러리
- [ ] Rule engine + OOC 이벤트 스키마
- [ ] Timescale 스키마·보존 정책
- [ ] Web 차트 API (페이징·줌·드릴)
- [ ] MICC 알람 연동
- [ ] 조치(Hist) 워크플로 + ACTION Permission
- [ ] FDC/AI 상관 뷰 (2차)

관련: [../02-architecture.md](../02-architecture.md), [../04-persistence.md](../04-persistence.md)
