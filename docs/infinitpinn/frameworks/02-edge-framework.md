# 02. Edge Framework

InfinitPINN **Edge Framework** — 현장 근접 수집·로컬 판단·설비 제어 반영의 근간.

원본 매핑: Operation & Control Arch(p.7), 전송암호화 Edge(p.5), Ack Log(p.6), Client/Edge 감사 흐름(p.25–27)

---

## 1. 제품 정의

| 항목 | 내용 |
|------|------|
| 목적 | Factory 내 Edge에서 **데이터 수집 / Local decision / 시스템 Control** 수행 |
| 위치 | Equipment · Machine Control Layer와 Operation Layer 사이 |
| 포지션 | 3종 중 **Control 말단**. Web의 승인·AI 명령을 현장(PLC 등)에 반영하고 Ack를 되돌림 |

### 원본 Edge Role (p.7)

1. 데이터 수집  
2. Local decision  
3. 시스템 Control  

---

## 2. 범위 (In / Out)

### In Scope
- Tag Collector / ECS 연동 포인트
- 산업 프로토콜 어댑터: OPC-UA, SECS, EtherNet/IP, PROFINET, Serial 등
- Telemetry Pipeline (Sensor, Process Value, Event, Alarm, 검사/계측)
- Operation 연계: Material In/Out, Recipe/Set Value, Remote Control
- MQTT(및 Kafka bridge) 발행·구독
- Edge AI (검사 자동화 / Anomaly detection) 추론 런타임 슬롯
- 제어 명령 수신 → PLC/설비 반영 → **Acknowledge (Round-trip)**
- Edge 구간 보안: TLS 1.3, ChaCha20-Poly1305 (요구, 구현 TBD)
- `trace_id`를 헤더에 실어 Core로 전파

### Out of Scope
- 브라우저 포털·RBAC 관리 UI → **Web**
- 관리도·SPC 통계 엔진 → **SPC** (Edge는 측정값 공급)
- 중앙 Model Registry / 장기 학습 → AI Platform (Edge는 inference·일부 로컬)

---

## 3. 아키텍처 슬롯

```
[Equipment] PLC / Tool / OHTC·AMR·AGV / Sensors
        │  OPC-UA / SECS / Industrial Ethernet / Serial
        ▼
┌───────────────────────── Edge Framework ─────────────────────────┐
│  Drivers / Tag Collector / In-line Controller                      │
│  Local decision · Edge AI inference                                │
│  MQTT publish · gRPC/REST to Core · Kafka (via gateway)            │
│  Command consumer → Set-point write → Ack                          │
└───────────────────────────────┬───────────────────────────────────┘
                                │ Telemetry / Alarm / Ack / Event
                                ▼
                    Streaming Platform (Kafka/MQTT/…)
                                │
                    Manufacturing Operation + AI Platform
                                │
                              Web (MICC)
```

관련 원본 구성: ECS, MCS, Tool Controller, Drivers, Edge AI, Telemetry/Operation Pipeline.

---

## 4. 메시지·감사

| 흐름 | Edge 역할 |
|------|-----------|
| 상향 (현장→Core) | Sensor/PV/Event/Alarm/계측 메시지 + `trace_id` |
| 하향 (Core→현장) | Recipe / Set Value / Remote Control 커맨드 |
| Ack | PLC/Edge 수신·상태 변경 Round-trip → Acknowledge Log |
| 감사 | Client/**Edge** → REST 또는 Kafka Listener → Behavior/ELK |

원본 감사 다이어그램은 요청 주체를 **Client/Edge**로 표기 — Edge는 1급 클라이언트.

---

## 5. 보안 요구 (원본, 구현 TBD)

| 항목 | 내용 |
|------|------|
| 전송 | TLS 1.3, Internal CA / Let's Encrypt |
| Edge 최적화 | ChaCha20-Poly1305 |
| 제어 승인 | Web/MIAM 승인 없는 고위험 Set-point는 정책으로 차단 (Agent 권한 TBD) |

---

## 6. Persistence 관점

- 대량 Tag/시계열: **Timescale** 또는 로컬 버퍼 후 적재 → JDBC/배치 적합
- 로컬 단기 버퍼·오프라인 큐: Edge FW 자체 설계 필요 (**원본 미상세 → 갭**)
- 이력성 insert: `insertBatch` 패턴 참고

---

## 7. Web / SPC와의 계약

| 상대 | 계약 |
|------|------|
| → Web | 알람·상태·Ack 이벤트를 MICC에 표시; 승인 결과 수신 |
| → SPC | 측정·공정 수치 스트림 공급 (EDC/SPC 입력) |
| ← AI | 추론 Set-point 제안; 승인 후 Edge 실행 |

---

## 8. 제작 산출물 체크리스트

- [ ] Edge 런타임 선정 (Java 경량 / 별도 언어) — **미정**
- [ ] 프로토콜 어댑터 SPI (OPC-UA, SECS, …)
- [ ] MQTT 토픽·Kafka 매핑 규약
- [ ] Command 실행기 + Ack 상태머신
- [ ] 로컬 버퍼·재시도·스토어앤포워드
- [ ] Edge AI inference 훅
- [ ] 인증서·ChaCha 프로파일
- [ ] trace_id 전파 테스트 스위트
- [ ] MICC 연동 샘플 (알람 1종 + Ack 1종)

상세: [../02-architecture.md](../02-architecture.md), [../07-audit-trail.md](../07-audit-trail.md), [../01-requirements.md](../01-requirements.md)
