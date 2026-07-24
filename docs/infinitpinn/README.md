# InfinitPINN Platform — Framework & Architecture Docs

SorTech **InfinitPINN** 플랫폼의 시스템 아키텍처 및 Backend Framework 기능 문서를 Markdown으로 관리한다.

| 항목 | 내용 |
|------|------|
| 원본 문서 | `InfinitPINN_시스템아키텍쳐_Framework_기능_v0.2.pptx` |
| 문서 버전 | **Rev 0.2** (2026-06-05) |
| 작성자 | 김찬석 |
| 원본 PDF | [InfinitPINN_Framework_Architecture_v0.2.pdf](./InfinitPINN_Framework_Architecture_v0.2.pdf) |
| MD 변환일 | 2026-07-24 |

---

## 3종 Framework (제품 관점) ★

플랫폼 근간을 **Edge · Web · SPC** 로 나눠 제작할 때의 문서:

→ **[frameworks/README.md](./frameworks/README.md)**  
공통 코어 · Web · Edge · SPC · Cross-cutting · 갭/백로그

## 문서 맵 (원본 장 순서)

| # | 문서 | 원본 장 | 요약 |
|---|------|---------|------|
| 00 | [개요·히스토리](./00-overview.md) | p.1–3 | 문서 메타, 변경이력, 목차 |
| 01 | [요구사항 분석](./01-requirements.md) | p.4–6 | Zero Trust, 암호화, 감사/가시성 |
| 02 | [시스템·Framework 아키텍처](./02-architecture.md) | p.7–9 | Operation & Control, FE/BE 스택 |
| 03 | [Backend Framework](./03-backend-framework.md) | p.10–12 | 패키지, handler, platform 공통 |
| 04 | [Persistence](./04-persistence.md) | p.13–17 | JPA/QueryDSL/JDBC, AmsRepository |
| 05 | [Interface](./05-interface.md) | p.18–19 | REST/gRPC/Kafka 메시지 규격 |
| 06 | [사용자·권한](./06-user-auth.md) | p.20–23 | RBAC, 암호, Password Policy |
| 07 | [감사추적](./07-audit-trail.md) | p.24–27 | Logback, user_behavior, ELK To-Be |
| 08 | [분석 요약·오픈이슈](./08-analysis-summary.md) | — | TBD, FE 시안 연계, 우선순위 |

---

## 플랫폼 한 줄 정의

> InfinitPINN은 **데이터 중심 IoT를 넘어 Control 중심 Factory Intelligence**를 지향하는 제조 Operation & Control 플랫폼이다.  
> Spring Boot 기반 Framework 위에 RBAC·감사추적·Persistence·멀티 프로토콜 Interface를 공통화하고, AI Agent(PI-LAM 등)의 추론·제어·승인을 안전하게 연결한다.

---

## 기술 스택 (v0.2 확정)

| 영역 | 선택 |
|------|------|
| Language | Java 21 LTS |
| Framework | Spring Boot 4.x |
| WAS | Embedded Tomcat 10.x |
| WEB | Nginx Reverse Proxy |
| DB | PostgreSQL + TimescaleDB |
| FE | React (반응형) + DevExtreme 등 |
| 인증 | JWT + Spring Security 6.x |
| Cache | Redis, Caffeine |
| SCM/CI | GitLab Self-hosted, Jenkins/GitLab CI, Docker |
| Messaging | Kafka / MQTT / gRPC / REST |

---

## 다이어그램 원본 스냅샷

| 페이지 | 파일 | 내용 |
|--------|------|------|
| 7 | [page-07.png](./assets/page-07.png) | Manufacturing Operation & Control Architecture |
| 8 | [page-08.png](./assets/page-08.png) | Framework 아키텍처 (FE/BE) |
| 9 | [page-09.png](./assets/page-09.png) | 스택·Layered Service Architecture |
| 18–19 | [page-18.png](./assets/page-18.png) | Interface Message |
| 21 | [page-21.png](./assets/page-21.png) | User–Role–Permission 모델 |
| 25–27 | [page-25.png](./assets/page-25.png) 등 | 감사추적 As-Is / Behavior / ELK To-Be |

---

## 변경 이력 (원본)

| 버전 | 일자 | 내역 | 담당 |
|------|------|------|------|
| — | 2026-05-17 | Framework Initial | 김찬석 |
| v0.1 | 2026-05-28 | Framework 아키텍처 및 설명 | 김찬석 |
| v0.2 | 2026-06-05 | Persistence, Interface, Log 추가 | 김찬석 |
| MD | 2026-07-24 | PDF→Markdown 구조화·분석 정리 | Cursor Agent |

---

*Confidential — Copyright © SorTech. All rights reserved.*
