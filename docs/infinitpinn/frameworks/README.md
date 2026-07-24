# InfinitPINN Frameworks — Edge · Web · SPC (3종)

플랫폼의 **근간(Foundation)** 은 단일 모놀리스가 아니라, 공통 AMS 코어 위에 올리는 **3종 Framework** 로 제작·관리한다.

| 항목 | 내용 |
|------|------|
| 근거 원본 | InfinitPINN 시스템아키텍처 및 Framework 기능 **v0.2** (2026-06-05, 김찬석) |
| 원본 PDF | [../InfinitPINN_Framework_Architecture_v0.2.pdf](../InfinitPINN_Framework_Architecture_v0.2.pdf) |
| 상세 장별 MD | [../README.md](../README.md) (`00`~`08`) |
| 본 폴더 목적 | 원본을 **Edge / Web / SPC 제품 관점**으로 재매핑·갭 분석 |
| MD 작성일 | 2026-07-24 |

> 원본 PPT는 “시스템 아키텍처 + Backend Framework 기능” 중심이다.  
> **Edge·Web·SPC를 3개 제품 라인으로 명시한 전용 목차는 없으므로**, 본 문서는 아키텍처·약어·인터페이스·감사 흐름에서 **추론·매핑한 Product Framework View** 이다. 확정 명칭·범위는 아키텍트 검수 필요.

---

## 3종 한눈에

| Framework | 한 줄 정의 | 주 런타임 | 주 프로토콜 | 주 사용자 |
|-----------|------------|-----------|-------------|-----------|
| **Web** | 브라우저 기반 Operation·관제·기준정보·권한 UI/API | React + Spring Boot | REST, JWT | 사무·감독·품질·관리자 |
| **Edge** | 현장 설비/센서 근접 수집·로컬판단·제어 반영 | Edge Runtime (+ Edge AI) | MQTT, OPC-UA/SECS, Industrial Ethernet, gRPC | 설비·라인·PLC/드라이버 |
| **SPC** | 통계적 공정관리·품질 데이터·관리도·이상 연계 | Web 공통 위 품질 도메인 (+ Timescale) | REST, Kafka event | 품질·공정엔지니어 |

```
                    ┌─────────────────────────────────────┐
                    │     InfinitPINN Platform (공통)      │
                    │  Auth · Persistence · Message · Audit │
                    │  Worker → Rule → Operation · Kafka    │
                    └───────────┬───────────┬───────────────┘
              ┌─────────────────┼─────────────────┐
              ▼                 ▼                 ▼
        ┌──────────┐     ┌──────────┐      ┌──────────┐
        │   Web    │     │   Edge   │      │   SPC    │
        │Framework │     │Framework │      │Framework │
        └──────────┘     └──────────┘      └──────────┘
         MICC/DTAA         ECS/Tag           FDC/EDC
         RBAC Portal       Local Decision    Control Chart
         Meta UI           PLC Ack           Quality Event
```

---

## 문서 맵

| 문서 | 내용 |
|------|------|
| [00-common-core.md](./00-common-core.md) | 3종이 공유하는 AMS 코어 (스택·레이어·패키지) |
| [01-web-framework.md](./01-web-framework.md) | Web Framework 범위·기능·화면 연계 |
| [02-edge-framework.md](./02-edge-framework.md) | Edge Framework 범위·인터페이스·보안·감사 |
| [03-spc-framework.md](./03-spc-framework.md) | SPC Framework 범위·품질 파이프·데이터 |
| [04-cross-cutting.md](./04-cross-cutting.md) | 3종 공통 메시지·권한·trace·배포 |
| [05-gap-and-backlog.md](./05-gap-and-backlog.md) | v0.2 대비 3종별 갭·제작 백로그 |

---

## 아키텍처 원본 스냅샷

![Operation & Control](./assets/page-07.png)

*원본 p.7 — Equipment / Operation / AI / Twin 레이어. Edge·SPC·Web이 각각 다른 레이어에 걸친다.*

---

## 제작 원칙 (권장)

1. **공통 코어 먼저** — Auth, Message Format, trace_id, Persistence, Exception/NLS는 3종이 동일 규약.
2. **Web이 UX 허브** — Edge·SPC 결과는 Web(MICC 등)에서 관제·승인·감사 조회.
3. **Edge는 Control 말단** — Sim-to-Real 최종 반영·Ack는 Edge 경로.
4. **SPC는 도메인 특화** — 공통 Web 스택을 쓰되, 시계열·관리도·룰/알람은 SPC 모듈로 분리.
5. **버전은 프레임워크별** — `web-fw-x.y`, `edge-fw-x.y`, `spc-fw-x.y` + 공통 `ams-core-x.y`.
