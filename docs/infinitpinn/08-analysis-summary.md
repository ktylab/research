# 08. 분석 요약 · 오픈이슈 · FE 연계

원본 v0.2 전체 종합 분석 (2026-07-24)

---

## 8.1 플랫폼이 무엇인지 (해석)

InfinitPINN은 단순 MES/ERP 화면 모음이 아니라,

1. **설비·Edge·물류 제어 레이어**와  
2. **제조 Operation 서비스(POM/RMS/FDC 등)**와  
3. **AI Platform (학습·추론·MPC·RL·Edge AI)**를  

Kafka/gRPC/REST로 묶고, 그 위에 **Spring Boot 공통 Framework**(권한·영속성·메시지·감사)를 올리는 **Control-centric Factory Intelligence 플랫폼**이다.

**제품 제작 단위(근간 3종):** [Edge · Web · SPC Frameworks](./frameworks/README.md)

FE는 “데이터 대시보드”가 아니라 **관제·실행·승인·감사**가 한 줄로 이어지는 UX가 필요하다.

---

## 8.2 확정된 것 (v0.2)

| 영역 | 확정 내용 |
|------|-----------|
| 스택 | Java 21, Spring Boot 4, Nginx, PG+Timescale, React, JWT+Security 6 |
| 서비스 레이어 | Worker → Rule → Operation |
| 권한 | RBAC + Role 계층 + MENU/ACTION/API Permission |
| Persistence | JPA/QueryDSL/JDBC 용도 분리, AmsRepository(Usable/Lock/Hist) |
| Interface | REST/gRPC/Kafka/MQTT + 표준 메시지 + 코드 생성 |
| 감사 | trace_id, Logback, user_behavior_hist 비동기, ELK To-Be |
| Password | SHA-256, PWD_POLICY 상수 테이블 |

---

## 8.3 T.B.D / 오픈이슈 백로그

| ID | 이슈 | 영향 | 제안 Owner |
|----|------|------|------------|
| TBD-01 | AI Agent 사용 권한 (호출·Sim-to-Real·프롬프트/레시피) | FE 승인 UX, Permission 모델 | BE Auth + AI |
| TBD-02 | 데이터 활용 권한 (격리·반출·MIM) | 다운로드 버튼, 데이터 마스킹 | BE Auth + Data |
| TBD-03 | DB Selective Encryption / LUKS 운영안 | 인프라·배포 | Infra |
| TBD-04 | TLS 1.3 / Internal CA / Edge ChaCha 운영 | Edge·Gateway | Infra |
| TBD-05 | Dynamic Masking·De-ID 구현 위치·규칙표 | API 응답·AI 학습셋 | BE + AI |
| TBD-06 | 업무별 감사 저장 항목 카탈로그 | MICC·Compliance | Domain + BE |
| TBD-07 | ReBAC 도입 시점·범위 | 권한 모델 복잡도 | Architect |
| TBD-08 | 비밀번호 해시: SHA-256 vs salted/modern KDF | 보안 리뷰 | Security |
| TBD-09 | FE 메타데이터 UI 스키마 (동적 폼) | Design System | FE + BE |
| TBD-10 | 메시지 설계서 → TS/Java 동시 generate | API 정합 | Platform |

---

## 8.4 FE 시안(화면 유형) 매핑

선행 벤치마크 화면유형과 InfinitPINN 기능의 대응.

| 화면유형 | InfinitPINN 근거 | 우선 |
|----------|------------------|------|
| S01 역할 포털 | MENU Permission, 모듈(MICC/DTAA/설정) | 1차 |
| S02 Command Center | MICC, Observability, Trace 타임라인 | 1차 |
| S04 Shopfloor 실행 | Worker 메시지, ACTION 권한, Edge | 1차 |
| S05 그리드 | QueryDSL/화면 API, 기준정보 CRUD | 1차 |
| S07 APS/스케줄·제어 | Decision & Optimizer, Set-point, 승인 | 1차 |
| S09 품질 | FDC/EDC/SPC, AI Anomaly | 1차 |
| S06 Object 상세 | Hist, prev_*, Extension Entity | 2차 |
| S08 What-if | Digital Twin & Simulation | 2차 |
| S03 Andon | Telemetry/Alarm 현황 | 2차 |
| S10 Trace | Lot/Material, Acknowledge Log | 2차 |
| S11 설비·보전 | CMMS, Eqp DB, Alarm | 2차 |
| S12 리포트·감사조회 | user_behavior, ELK, MICC 사후분석 | 2차 |

---

## 8.5 모듈 약어 인덱스

| 약어 | 추정/문서 의미 | 등장 |
|------|----------------|------|
| POM | Production Operation Management | Arch |
| RMS | Recipe Management System | Arch |
| R2R | Run to Run Control | Arch |
| FDC | Fault Detection & Classification | Arch |
| EDC | Engineering Data Collection | Arch |
| SPC | Statistical Process Control | Arch |
| CMMS | Computerized Maintenance Mgmt | Arch |
| FEMS | Factory Energy Management | Arch |
| MICC | 관제·모니터링 센터 | Req |
| DTAA | 자산·기준정보 관리 | Req |
| MIAM | 권한 관리 | Audit/AI |
| MIM | 지식자산 | Req |
| PI-LAM | AI Agent (물리/공정 연계 LLM류) | Req |
| PVSA | 물리 분석 (문서 표기) | Audit |
| ECS/MCS | Equipment/Material Control System | Arch |

> MICC/DTAA/MIAM/MIM/PI-LAM/PVSA는 원본에서 약어 풀이가 제한적임. 용어 사전 확정 권장.

---

## 8.6 다음 문서화 권장

1. **메시지 스키마 카탈로그** (Common Header + 주요 Command)  
2. **Permission 카탈로그** (MENU/ACTION/API 목록)  
3. **감사 필드 사전** (업무별)  
4. **FE Design Token ↔ NLS/에러코드 매핑**  
5. **AI Sim-to-Real 승인 시퀀스 다이어그램**

---

## 8.7 원본·MD 관리 규칙

| 규칙 | 내용 |
|------|------|
| 원본 | `docs/infinitpinn/InfinitPINN_Framework_Architecture_v0.2.pdf` |
| 버전업 | PPT/PDF Rev 증가 시 `09-changelog.md`에 요약 후 해당 장 MD 갱신 |
| 다이어그램 | `assets/page-XX.png` 교체 또는 신규 추가 |
| TBD | `08-analysis-summary.md` 표에서 상태 갱신 (`T.B.D` → `Done` + 일자) |
