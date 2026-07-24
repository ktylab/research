# 03. Backend Framework 기능

원본: InfinitPINN Framework v0.2 p.10–12

---

## 3.1 패키지 전체 맵

| # | 패키지 | 핵심 기능 | 설명 |
|---|--------|-----------|------|
| 1 | `aspect` | Log, Exception | 서비스 레이어 로깅(`AmsLogAspect`), 성능측정, 공통 예외 가로채기 |
| 2 | `cache` | 캐싱 엔진 | Caffeine L1 + Redis. 마스터·NLS 초고속 조회 |
| 3 | `common.type` | 상수·타입 | Enum, Constants, 공통 인터페이스 |
| 4 | `config` | 환경설정 | Bean, DB Pool, Cache Manager, AOP |
| 5 | `context` | 실행 컨텍스트 | 스레드별 사용자, TID 등 상태 보관·전파 |
| 6 | `exception` | 표준 예외 | `AmsException` — 에러코드 기반 |
| 7 | `handler` | 공통 핸들러 | HTTP/gRPC/Kafka 인입부터 예외·라우팅까지 **실행 제어 사령탑** |
| 8 | `integration` | 외부 연동 | Kafka/MQTT, 타시스템 송수신 |
| 9 | `log` | 표준 로깅 | `AmsLog` — TID, User, Caller 포함 |
| 10 | `nls` | 다국어 | `MessageProvider`, `{0}` 동적 치환 |
| 11 | `persistence` | 영속성 | 공통 Repository/Entity, CRUD JPA Wrapper, 이력 자동화 |
| 12 | `platform` | query·기준정보 | 공통 기능(기준정보 등) |
| 13 | `support` | 보조도구 | EntityHelper, Reflection 등 |
| 14 | `util` | 유틸 | 날짜, 문자열, 파일 I/O |

---

## 3.2 handler 패키지 상세

Handler는 외부 인입(HTTP, gRPC, Kafka)의 **컨텍스트 주입 → 예외 수습 → 메시지 라우팅**을 중앙 제어한다.

| # | 클래스 | 핵심 기능 |
|---|--------|-----------|
| 1 | `AmsTidFilter` | HTTP 최전방 `traceId` 발급 및 MDC 바인딩 |
| 2 | `AmsContextFilter` | 세션 정보(`senderId`, `factoryId`, `lang`) → `AmsUserContext` |
| 3 | `AmsGrpcInterceptor` | gRPC 메타데이터에서 traceId·세션 동기화 |
| 4 | `AmsKafkaInterceptor` | Kafka 헤더 `traceId` → 비동기 스레드 컨텍스트 |
| 5 | `AmsErrorProcessor` | 예외 → 표준 에러코드 + NLS 메시지 |
| 6 | `GlobalExceptionHandler` | Spring MVC 전역 예외 → 표준 JSON |
| 7 | `GrpcGlobalExceptionHandler` | gRPC 표준 상태·메타데이터 래핑 |
| 8 | `KafkaGlobalExceptionHandler` | 리스너 예외 로깅, DLQ 인계 |
| 9 | `MessageResultHandler` | 응답을 `Result / Message / Data` 공통 포맷으로 패킹 |
| 10 | `WorkerRouter` | 커맨드/타입 분석 → `MessageWorker` 동적 라우팅 |
| 11 | `MessageWorker` | 실제 비즈니스 핸들링 실행체 (추상 레이어) |
| 12 | `EntityTypeManager` | 가변 메시지 직렬화·엔티티 메타타입 런타임 식별 |

### 요청 처리 개념 흐름

```
Client/Edge
   │  message(+trace_id)
   ▼
AmsTidFilter / AmsContextFilter  (또는 gRPC/Kafka Interceptor)
   │  MDC + AmsUserContext
   ▼
WorkerRouter → MessageWorker → Rule → Operation
   │
   ├─ MessageResultHandler → 표준 JSON
   └─ Exception → AmsErrorProcessor → Global*ExceptionHandler
```

---

## 3.3 platform 패키지 — 공통 기능

### Auth

| 기능 | 설명 |
|------|------|
| Login/Out | 로그인·로그아웃 |
| Password | password 관리 rule |
| Login Hist | 로그인 이력 |

### Query / Update 실행

| 기능 | 설명 |
|------|------|
| 조회용 query 실행 | Query를 Table에 저장 후 읽어 실행. Result = DataTable(컬럼+데이터) |
| Update query (JPA) | 단건 CUD |
| Batch Update (JPA) | 복수 row CUD |
| Batch Update (JDBC) | 복수 row CUD — 성능 |
| 조회 Procedure | 조회 컬럼 명시 필요 |
| Update Procedure | Update용 Procedure |

### 사용자·권한 공통

| 기능 | 설명 |
|------|------|
| 사용자 | 계정 생성, 상태 제어 등 User 마스터 CRUD |
| 사용자 그룹(Role) | Role 생성·변경, 그룹별 권한 매핑 |
| Privilege | 메뉴·기능별 접근 권한 동적 할당 |
| User–Role 연계 | User↔Role, **Role↔Role** (1인 다그룹 가능) |
| Role–Privilege 연계 | 그룹–권한 매핑 |

---

## BE Framework 시사점 (분석)

1. **traceId가 1급 시민** — FE도 요청 헤더·에러 화면·감사 조회에 동일 ID 노출 검토.
2. **표준 응답 `Result/Message/Data`** — FE API 클라이언트 규약으로 고정.
3. **WorkerRouter 패턴** — 제조 메시지(커맨드) 중심; REST CRUD만이 아님.
4. **NLS** — UI 카피·에러 메시지를 코드 하드코딩하지 말고 MessageProvider 키 사용.
5. **factoryId / lang / senderId** — 멀티팩토리·다국어 컨텍스트가 세션 기본값.
