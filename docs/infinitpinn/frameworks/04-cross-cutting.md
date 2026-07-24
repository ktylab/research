# 04. 3종 Cross-Cutting — 메시지·권한·추적·배포

Edge · Web · SPC가 **어긋나면 안 되는** 횡단 관심사.

---

## 1. 표준 메시지

| 채널 | Web | Edge | SPC |
|------|-----|------|-----|
| REST JSON | ● 주력 | ○ 관리/커맨드 | ● API |
| gRPC | ○ AI | ● 제어·추론 | ○ |
| Kafka | ● 구독 | ● Telemetry/Ack | ● Ingest/OOC |
| MQTT | — | ● 현장 버스 | ○ (브리지) |

### Common Header (권장 최소)

| 필드 | 설명 |
|------|------|
| `trace_id` | 요청/이벤트 cycle ID |
| `factory_id` | 공장 컨텍스트 |
| `sender_id` | User 또는 Edge 디바이스 ID |
| `sender_type` | `USER` \| `EDGE` \| `SERVICE` |
| `command` / `type` | WorkerRouter 라우팅 키 |
| `timestamp` | 발생 시각 (UTC) |
| `schema_ver` | 메시지 버전 |

설계서 → Java/TS **동시 generate** (원본: Python generate 언급).

---

## 2. 권한

| 구분 | Web | Edge | SPC |
|------|-----|------|-----|
| 사람 RBAC | ● | 승인 UI는 Web | ● 품질 역할 |
| 디바이스 인증 | — | ● (인증서/토큰) **갭** | — |
| MENU/ACTION/API | ● | 커맨드=API/ACTION | ● |
| AI Sim-to-Real | Web 승인 | 실행만 | 이벤트 참조 |
| 데이터 반출 | Web | — | ● 리포트 다운로드 |

Agent 권한·Data Governance는 원본 **T.B.D** → 3종 공통 정책으로 선정의 필요.

---

## 3. trace_id · 감사

```
Edge 수집 시작 ─┐
Web 사용자 액션 ─┼─► trace_id 발급/전파 ─► Worker/AI/SPC ─► Ack
Kafka 이벤트   ─┘         │
                          ▼
              Logback + user_behavior_hist (+ ELK To-Be)
                          │
                          ▼
                     MICC 타임라인 (Web)
```

| 감사 유형 | 주 발생 | 조회 UI |
|-----------|---------|---------|
| Explainable AI Log | AI Platform | Web |
| Control Trail | Web 승인 + Edge 실행 | Web |
| Acknowledge Log | Edge | Web |
| SPC OOC 조치 | SPC/Web | Web |
| user_behavior | Web/Edge 요청 | Web |

---

## 4. 데이터 저장소 분담

| 저장소 | Web | Edge | SPC |
|--------|-----|------|-----|
| PostgreSQL | MDM, Auth, Hist | 설정 복제(옵션) | 스펙·이벤트·Hist |
| TimescaleDB | 관제 조회 | 버퍼 적재 대상 | **주력 시계열** |
| Redis | 세션·캐시 | 로컬 캐시(옵션) | 집계 캐시 |
| ES/OpenSearch | 감사 검색 | 로그 선적 | OOC 검색(옵션) |

---

## 5. 배포·버전

| 아티팩트 | 내용 |
|----------|------|
| `ams-core` | 공통 라이브러리/베이스 이미지 |
| `web-fw` | React + Web BE 서비스 |
| `edge-fw` | Edge 에이전트/어댑터 패키지 |
| `spc-fw` | SPC 서비스 + (옵션) Web 모듈 |

원본 CI/CD: GitLab Self-hosted, Jenkins/GitLab CI, Docker.  
K8S/Docker 시 Pod·Container 수만큼 로그 파일 분산 → **ELK 전제** 권장.

---

## 6. 오케스트레이션

원본: **Apache Camel + Karavan** (EIP + Workflow)

3종 이벤트 라우팅·변환·재시도의 중앙 허브 후보.  
Edge MQTT ↔ Kafka, SPC OOC → MICC 알림 등을 Camel 라우트로 표준화.

---

## 7. 상호 의존 매트릭스

| From \\ To | Web | Edge | SPC |
|------------|-----|------|-----|
| **Web** | — | 제어 커맨드·설정 배포 | 차트 API·조치 |
| **Edge** | 알람·Ack·상태 | — | 측정 스트림 |
| **SPC** | OOC 알람·리포트 | 샘플링 지시(옵션) | — |
