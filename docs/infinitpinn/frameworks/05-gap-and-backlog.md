# 05. 갭 분석 · 3종 제작 백로그

원본 InfinitPINN Framework v0.2는 **공통 Backend Framework + 전체 Operation Arch**에 강점이 있고,  
**Edge·Web·SPC를 3개 제품으로 쪼갠 상세 스펙은 부족**하다. 본 문서는 그 갭과 제작 백로그다.

---

## 1. 원본 커버리지 (3종 관점)

| 주제 | Web | Edge | SPC | 근거 |
|------|-----|------|-----|------|
| UI/FE 스택 | ● | △ | △(Web 위) | React, 메타 UI |
| BE 패키지·Handler | ● | △(연동) | ● | p.10–12 |
| RBAC·Password | ● | ○ | ● | p.20–23 |
| Persistence | ● | △ | ● | p.13–17 |
| REST/gRPC/Kafka | ● | ● | ● | p.18–19 |
| Edge Role·프로토콜 | ○ | ●(개념) | ○ | p.7 |
| Edge 런타임·오프라인 | — | ✕ | — | 미기재 |
| SPC 엔진·관리도 | ○ | ○ | ✕(약어만) | p.7 SPC 언급 |
| AI Agent 권한 | TBD | TBD | TBD | p.4 |
| 암호화 구현 | TBD | TBD | TBD | p.5 |
| ELK To-Be | ● | ● | ● | p.27 |

● 상세 △ 일부 ○ 간접 ✕ 거의 없음 TBD 명시적 미정

---

## 2. Framework별 갭

### Web
| 갭 | 설명 | 우선 |
|----|------|------|
| 메타데이터 UI 스키마 | “동적 UI”만 있고 스키마 스펙 없음 | P0 |
| Design System | FE 시안과 연결 문서 없음 | P0 |
| Permission 카탈로그 | MENU/ACTION/API 목록 미정의 | P0 |
| BFF vs 직접 API | 경계 미정 | P1 |

### Edge
| 갭 | 설명 | 우선 |
|----|------|------|
| 런타임 언어/OS | Java 공유 여부 미정 | P0 |
| 어댑터 SPI | OPC-UA/SECS 등 플러그인 규약 | P0 |
| 오프라인 버퍼 | Store-and-forward 미정의 | P0 |
| 디바이스 Identity | 인증서/토큰·등록 절차 | P0 |
| 로컬 decision 스코프 | 어떤 판단을 Edge에서 할지 | P1 |
| ChaCha/TLS 프로파일 | 요구만 존재 | P1 |

### SPC
| 갭 | 설명 | 우선 |
|----|------|------|
| 도메인 모델 | Characteristic/Spec/Chart/Rule 미정의 | P0 |
| 관리도·룰 스펙 | 알고리즘·파라미터 | P0 |
| Ingest 스키마 | Edge/EDC 공통 포맷 | P0 |
| OOC 워크플로 | 홀드·조치·권한 | P1 |
| FDC/AI 상관 | trace 기반 조인 규칙 | P2 |

---

## 3. 공통(AMS Core) 갭 — 3종 모두 영향

| ID | 항목 | 상태 |
|----|------|------|
| TBD-01 | AI Agent 권한 | T.B.D |
| TBD-02 | 데이터 활용 권한 | T.B.D |
| TBD-03~04 | DB/전송 암호화 운영 | T.B.D |
| TBD-05 | Masking / De-ID 규칙표 | 방향만 |
| TBD-06 | 업무별 감사 필드 카탈로그 | 필요 명시 |
| TBD-10 | 메시지→TS/Java generate | 언급만 |

→ 상세는 [../08-analysis-summary.md](../08-analysis-summary.md)

---

## 4. 권장 제작 순서

```
Phase 0  AMS Core 동결 (메시지 헤더, Auth, Persistence, Handler, 표준 응답)
    │
Phase 1  Web Framework MVP (포털·로그인·기준정보·MICC 셸)
    │
Phase 2  Edge Framework MVP (1개 프로토콜 + MQTT + Ack + trace)
    │
Phase 3  SPC Framework MVP (ingest + 관리도 1종 + OOC 이벤트 + Web 차트)
    │
Phase 4  AI Sim-to-Real (승인 → Edge 실행 → Ack → 감사) E2E
```

Web을 먼저 두는 이유: 권한·관제·승인 UX 허브가 없으면 Edge/SPC를 검증할 창구가 없다.

---

## 5. 백로그 티켓 초안 (복사해서 이슈화)

### WEB
1. `WEB-001` React 앱 셸 + JWT 가드  
2. `WEB-002` Permission 가드 (MENU/ACTION)  
3. `WEB-003` 메타 폼/그리드 스키마 v0.1  
4. `WEB-004` MICC 셸 + Trace 조회 목업  
5. `WEB-005` 승인(Sim-to-Real) UI 와이어  

### EDGE
1. `EDGE-001` 런타임·배포 단위 ADR  
2. `EDGE-002` 어댑터 SPI + Mock PLC  
3. `EDGE-003` MQTT 토픽 규약 + Kafka 브리지  
4. `EDGE-004` Command/Ack 상태머신  
5. `EDGE-005` 오프라인 큐  

### SPC
1. `SPC-001` 도메인 모델 ADR  
2. `SPC-002` Ingest 스키마 (with Edge)  
3. `SPC-003` X̄-R 또는 I-MR 차트 1종  
4. `SPC-004` OOC 이벤트 → MICC  
5. `SPC-005` 조치 Hist + ACTION Permission  

### CORE
1. `CORE-001` Common Message Header 확정  
2. `CORE-002` OpenAPI/proto generate 파이프라인  
3. `CORE-003` user_behavior 필드 사전  
4. `CORE-004` ELK 파이프 PoC  

---

## 6. 문서 관리 규칙

| 규칙 | 내용 |
|------|------|
| 원본 Rev 상승 | `../` 장별 MD 갱신 후 본 폴더 매핑·갭 표 업데이트 |
| 3종 범위 공식화 | 아키텍트 확정 시 본 README 상단 “추론” 문구 제거·공식 명칭 반영 |
| 티켓 상태 | 본 백로그 ID를 이슈 트래커 ID에 매핑 테이블로 추가 |
