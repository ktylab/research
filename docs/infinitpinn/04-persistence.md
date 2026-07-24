# 04. Persistence

원본: InfinitPINN Framework v0.2 p.13–17

---

## 4.1 JPA vs QueryDSL vs JDBC

| 비교 | 순수 JPA ORM | QueryDSL (JPA) | JDBC / JdbcTemplate |
|------|--------------|----------------|---------------------|
| 핵심 가치 | 객체-DB 일치, CRUD 생산성, 단건 표준화 | 동적 쿼리, Type-Safe, 복잡 조회 유지보수 | 최고 성능, SQL 중심 제어 |
| 성능 오버헤드 | 영속성 컨텍스트·생명주기 | JPA + Q-Class (미미) | 거의 없음 |
| 대량 CUD | 비추천 (건별·메모리) | 비추천 (벌크 시 컨텍스트 미반영 리스크) | **강력 추천** (`batchUpdate`) |
| 이력 연동 | **자동** (`AmsBaseEntity` + 리스너) | **자동** (JPA 기반) | **수동** SQL |
| 사용처 | MDM CRUD, 단건 비즈, DDD 핵심 | Service/Repository 동적 검색·화면 API | 설비 IF 수집, 대용량 로그, 통계 배치, 마이그레이션 |

### 선택 가이드 (요약)

```
마스터/단건 업무     → JPA
화면 동적 조회       → QueryDSL
대량·설비·배치·로그  → JDBC
```

---

## 4.2 Entity · Repository 원칙

- ORM 사용 시 **Table마다 Entity, Repository**가 기본.
- **Hist Table**: Entity 필수, Repository는 Option.
- **업무 로직 없는 CRUD**: Entity 필수, Repository Option.
- Custom Entity 대비 Annotation:
  - `@Inheritance(strategy = SINGLE_TABLE)`
  - `@DiscriminatorColumn(name = "dtype", …)`
  - `@DiscriminatorValue("STD")`
- Entity는 가능하면 **generator**로 생성.
- 복합 Key면 ID(PK) 반드시 설정.
- Hist 저장 시: `isSnapshotEnabled = true`

---

## 4.3 표준 JPA — SimpleJpaRepository (요약)

### 저장·수정
`save` · `saveAll` · `saveAndFlush`

### 단건 조회
`findById` · `getReferenceById` · `existsById`

### 목록
`findAll` · `findAllById` · `findAll(Sort)` · `findAll(Pageable)`  
⚠ `findAll()` 전체 조회는 메모리 부하 주의

### Delete
`deleteById` · `delete` · `deleteAllById` · `deleteAll` · `deleteAll()`  
⚠ `deleteAll()`은 전체 조회 후 건건 DELETE → **대형 장애 유발 가능**

### Delete Batch (추천)
`deleteAllInBatch` · `deleteAllByIdInBatch` · `deleteAllInBatch()`  
→ 선조회 없이 벌크 DELETE

### Etc.
`count` · `flush`

---

## 4.4 공통 Repository — AmsRepository

프레임워크 확장 Repository. **Lock**과 **Usable** 조건이 핵심 차별점.

### 단건 조회

| 메서드 | Lock | Usable |
|--------|------|--------|
| `findById` | No | — |
| `findByIdForUpdate` | **Yes** (수정용) | — |
| `getUsableById` | No | **Usable** |
| `getUsableByIdForUpdate` | **Yes** | **Usable** |

### 목록 조회

| 메서드 | Lock | Usable |
|--------|------|--------|
| `findList` | No | — |
| `findListForUpdate` | Yes | — |
| `getList` | No | Usable |
| `getListForUpdate` | Yes | Usable |
| `getAllUsable` | — | Usable 전체 |

### Insert / 저장·이력 / Soft Delete

| 메서드 | 설명 |
|--------|------|
| `insertBatch` | 일괄 삽입 (이력성 insert 최적화) |
| `saveWithHist` | 저장 + History (기본 true) |
| `saveAllWithHist` | 다건 + History |
| `makeUnUsableWithHist` | Soft Delete + 이력 |

---

## 4.5 Hist · Extended Column

### Hist Table 저장

`saveWithHist` 사용 시:

1. 현재 데이터를 이력에 저장
2. `prev_xxxx` 컬럼이 있으면 **직전 값 자동 세팅**

### Extended Column (커스텀 컬럼)

표준 테이블 외 컬럼 추가 시 두 가지 방식.

#### 1) Extended Entity

```java
// 표준 Logic은 extension entity를 미리 감안
Class<? extends Lot> lotClass = entityFactory.getExtensionClass(Lot.class);

@DiscriminatorValue("EXT")
public class LotExt extends Lot {
    @Column(name = "areaid", length = 40)
    private String areaid;
}
```

#### 2) UserProperty Map

- 각 Entity의 `UserProperty` map 활용
- Client 메시지에 `columnName` + `value`를 넣으면 해당 컬럼 업데이트

---

## Persistence 시사점 (분석)

1. **Usable + Soft Delete**가 표준 → FE는 “삭제”보다 **사용불가/활성화** UX가 맞을 수 있음.
2. **ForUpdate** 계열 → 동시 편집·낙관/비관 잠금 UI(잠금 중 메시지) 필요.
3. **saveWithHist / prev_*** → 이력·변경 비교 화면(S06 Object 상세)의 BE 근거.
4. **Extension Entity / UserProperty** → 메타데이터 UI·동적 폼과 직결.
5. 대량 설비 데이터는 JDBC 경로 → FE 그리드는 페이징·스트리밍 전제.
