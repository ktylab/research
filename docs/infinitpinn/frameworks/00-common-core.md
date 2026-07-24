# 00. 공통 코어 (AMS Core) — 3종 Framework 공유 기반

원본 매핑: Framework 아키텍처(p.8–9), Backend 패키지(p.10–12), Persistence(p.13–17), Interface(p.18–19), Auth(p.20–23), Audit(p.24–27)

---

## 1. 역할

Edge / Web / SPC가 **각자 다시 만들지 않는** 공통 기반.

| 영역 | 공통 제공 | 비고 |
|------|-----------|------|
| 런타임 | Spring Boot 4.x, Java 21 | Web·SPC BE 주력. Edge는 경량 런타임 별도 가능 |
| 보안 | JWT, Spring Security 6, RBAC | Edge 디바이스 인증은 확장 필요 |
| 컨텍스트 | `trace_id`, `factoryId`, `senderId`, `lang` | 전 채널 전파 |
| 서비스 레이어 | Worker → Rule → Operation | 제조 메시지 처리 표준 |
| Persistence | JPA / QueryDSL / JDBC + AmsRepository | Hist, Usable, Lock, Extension |
| 캐시 | Caffeine L1 + Redis | 마스터·NLS |
| 메시지 | REST / gRPC / Kafka / MQTT 규약 | 설계서→코드 generate |
| 로그·감사 | Logback, user_behavior_hist, ELK To-Be | AI Control Trail 포함 |
| NLS·예외 | MessageProvider, AmsException | 표준 JSON `Result/Message/Data` |

---

## 2. 기술 스택 (원본 확정)

| 구분 | 내용 | 3종 적용 |
|------|------|----------|
| Framework | Spring Boot 4.x | Web/SPC BE, Edge 연동 서비스 |
| Language | Java 21 LTS | 상동 |
| WAS | Embedded Tomcat 10.x | Web/SPC |
| WEB | Nginx Reverse Proxy | Web |
| DB | PostgreSQL + TimescaleDB | Web/SPC (시계열은 SPC·설비 공용) |
| FE | React + DevExtreme 등 | **Web** (SPC UI도 Web 위) |
| CI/CD | GitLab, Jenkins, Docker | 3종 모두 |
| Event Bus | Kafka / MQTT / RabbitMQ | Edge↔Core↔SPC |

---

## 3. Backend 패키지 (공통 카탈로그)

원본 14개 패키지는 **AMS Core**로 취급한다.

`aspect` · `cache` · `common.type` · `config` · `context` · `exception` · `handler` · `integration` · `log` · `nls` · `persistence` · `platform` · `support` · `util`

### handler — 3종 인입 통합점

| 클래스 | Web | Edge | SPC |
|--------|-----|------|-----|
| `AmsTidFilter` / ContextFilter | ● REST | ○ Gateway 경유 시 | ● |
| `AmsGrpcInterceptor` | ○ AI/서비스 | ● 고속 제어·추론 | ○ |
| `AmsKafkaInterceptor` | ● 이벤트 | ● Telemetry/Alarm | ● 품질 이벤트 |
| `WorkerRouter` / `MessageWorker` | ● | ● (커맨드) | ● |
| Global*ExceptionHandler | ● | ● | ● |

---

## 4. Layered Service Architecture (공통)

```
Worker Service   →  메시지 수신 (Client/Edge/Event)
Rule Service     →  Business Logic / 다수 Object
Operation Service→  Table(±Child) 단위 업무
```

단순화:
- 조회: Worker → Query Rule
- 기준정보: Client JSON dataset → Worker → 공통 Rule

---

## 5. Persistence 공통 규칙

| 규칙 | 내용 |
|------|------|
| 용도 분리 | MDM/단건=JPA, 화면조회=QueryDSL, 대량/IF=JDBC |
| Hist | `saveWithHist`, `prev_*` 자동 |
| Soft Delete | `makeUnUsableWithHist` |
| 확장 | Inheritance Extension Entity / UserProperty map |
| Lock | `*ForUpdate` 계열 |

SPC 대량 측정·Edge 수집 적재는 **JDBC/배치** 경로가 기본 후보.

---

## 6. 설계 철학 (원본 p.7)

> Smart Factory 한계 = 데이터 중심 IoT  
> 자율제조 핵심 = **Control 중심 Factory Intelligence**

공통 코어는 “저장·조회”만이 아니라 **제어 명령·승인·Ack·감사**를 1급으로 지원해야 3종 Framework가 같은 언어로 동작한다.

---

## 관련 상세 MD

- [../02-architecture.md](../02-architecture.md)
- [../03-backend-framework.md](../03-backend-framework.md)
- [../04-persistence.md](../04-persistence.md)
- [../05-interface.md](../05-interface.md)
