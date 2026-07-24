# 01. 요구사항 분석

원본: InfinitPINN Framework v0.2 p.4–6

---

## 1.1 Zero Trust 기반 권한 관리

### 원칙

| 원칙 | 의미 |
|------|------|
| Never Trust, Always Verify | 네트워크 위치와 무관하게 항상 검증 |
| Verify Explicitly | 명시적 검증 |
| Least Privilege Access | 최소 권한 |
| Assume Breach | 침해 가정 |

### 구현 방안 (문서 명시)

1. **User 등록**
2. **Role 등록** 후 User ↔ Role Mapping으로 권한 그룹 구성
3. **Role 계층**: Role이 다른 Role에 속할 수 있음 (Role → User 또는 Role → Role)
4. **Permission**으로 기능(Menu, Action, API 등) 정의 후 Role ↔ Permission Mapping
5. **실행 시 권한 Check** — Client 요청 기능에 대한 런타임 검증

### 기능 접근 권한 (Menu Access Role) — RBAC

직무(Role)에 따른 플랫폼 기능 접근 통제 대상 예시:

| 영역 | 예시 |
|------|------|
| 관제·모니터링 | MICC |
| 자산·기준정보 | DTAA |
| 시스템 설정·운영 | — |
| 권한 그룹 생성 | — |
| 제어 Action 단위 권한 | Action-level |

### AI Agent 사용 권한 — **T.B.D**

지능형 에이전트(PI-LAM, LLM 등) 호출·현장 반영 통제:

- 에이전트 호출 권한
- 제어 승인 권한 (**Sim-to-Real**)
- 프롬프트 및 레시피 수정

> 구현 방안 미정. FE/BE 시안·API 설계 시 별도 권한 타입으로 확장 필요.

### 데이터 활용 권한 — **T.B.D**

Data Governance & Access Role:

- 컨텍스트별 데이터 격리
- 데이터 다운로드·반출
- 지식자산(MIM) 기여 권한

---

## 1.2 데이터 보호 및 암호화

### DB 저장 구간 — **T.B.D**

목표: Oracle 등 상용 TDE 라이선스 없이 오픈소스 DB에서 보호

| 방안 | 내용 |
|------|------|
| OS 레벨 암호화 | LUKS / dm-crypt (무료) |
| 선택적 암호화 | 중요도 기반 Selective Encryption |
| 대상 DB | PostgreSQL / TimescaleDB |

### 전송 구간 — **T.B.D**

| 항목 | 내용 |
|------|------|
| 인증서 | Internal CA / Let's Encrypt |
| 프로토콜 | TLS 1.3 |
| Edge 최적화 | ChaCha20-Poly1305 |

### 마스킹·익명화 — **T.B.D** (설계 방향은 상세)

#### Dynamic Data Masking

- DB에는 원본 유지, 화면/API 응답 시 `*` 등 치환
- 장점: 암호 연산이 아닌 문자열 치환 → Latency ≈ 0
- 적용 위치: Spring Boot Interceptor 또는 API Gateway(Orchestrator)
- 효과: 내부자 Shoulder Surfing·화면 캡처 완화

#### De-identification (비식별화)

- AI 학습용: 고객사/제품명 등 식별자 제거, 물리 수치만 유지
- 예: `A고객사 배터리 레시피` → `Project_X_Recipe`
- 효과: AI 서버 유출 시에도 원천 식별 불가

---

## 1.3 가시성(Observability)과 감사추적(Audit Trail)

### 핵심 로깅 대상

- AI 에이전트 실행 및 제어 명령
- MSA 분산 추적 (Distributed Tracing)
- 고성능 로그 수집·저장
- MICC 연동 사후 분석 화면

### 구현 방향 (문서 명시)

| 항목 | 내용 |
|------|------|
| `trace_id` | 요청 cycle 단위 ID. Client→Server 헤더 전달 또는 Server 생성 |
| 로그 분리 | 일반 시스템 로그 ≠ 감사추적용 로그 |
| MSA | 서비스별 Process 로그 분리 |
| 1차 저장 | Logback → log file |
| 감사 저장 | `user_behavior_hist` — log file 또는 전용 Table |
| Table 방식 | Client 요청·수행결과, 업무 TX와 **비동기(Async)** 분리 저장 |
| Log 방식 | Kafka/Logstash → **ELK** |
| 저장 항목 | 업무별 상세 항목은 별도 정리 필요 |

### AI 에이전트 실행·제어 명령 (Compliance 수준)

| 로그 유형 | 기록 내용 |
|-----------|-----------|
| Explainable AI Log | 추론 시 참조 원천 데이터 스냅샷, PVSA 결과, 선택 근거(확률·기대효과) |
| Control Trail | Target 설비, Set-point 변경 명령, MIAM 승인/반려자·시점 |
| Acknowledge Log | PLC/Edge 전송·수신·상태 변경 Round-trip 결과 |

### MSA Distributed Tracing

1. 최초 수집/분석 시점에 **Trace ID** 발급
2. Kafka 헤더 / gRPC 메타데이터로 전 서비스 관통
3. Trace ID 조회 시 타임라인으로 지연·원인 서비스 즉시 파악 (MICC)

### 고성능 로그 아키텍처

```
App (Java/Python) --async--> Kafka --Logstash/Fluentd--> Elasticsearch/OpenSearch
                                      (Zero-latency 전략)
```

- 앱은 파일/DB에 직접 쓰지 않고 Kafka에만 비동기 적재
- ES 역인덱싱으로 대량 로그 고속 검색

---

## 요구사항 상태 요약

| ID | 영역 | 상태 |
|----|------|------|
| R-AUTH-01 | Zero Trust / RBAC User·Role·Permission | 방향 확정 |
| R-AUTH-02 | AI Agent 권한 | **T.B.D** |
| R-AUTH-03 | 데이터 활용 권한 | **T.B.D** |
| R-SEC-01 | DB 암호화 | **T.B.D** |
| R-SEC-02 | 전송 암호화 | **T.B.D** |
| R-SEC-03 | Masking / De-ID | 방향 상세, 구현 **T.B.D** |
| R-AUD-01 | trace_id / 로그 분리 | 방향 확정 |
| R-AUD-02 | AI Control Trail | 요구 상세 |
| R-AUD-03 | ELK 파이프라인 | To-Be 확정 |

→ 상세 오픈이슈는 [08-analysis-summary.md](./08-analysis-summary.md) 참고.
