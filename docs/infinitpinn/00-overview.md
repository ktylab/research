# 00. InfinitPINN 문서 개요

## 문서 식별

| 항목 | 값 |
|------|-----|
| 문서 제목 | InfinitPINN Platform Development — 시스템 아키텍처 및 Framework 기능 |
| 문서번호 | InfinitPINN_시스템아키텍쳐_Framework_기능_v0.2.pptx |
| 버전 | Rev **0.2** |
| 작성일 | 2026-06-05 |
| 작성자 | 김찬석 (SorTech) |
| 분류 | Confidential |

## Document History

| 버전 | 일자 | 구분 | 내역 | 담당 |
|------|------|------|------|------|
| (생성) | 2026-05-17 | Framework | Initial | 김찬석 |
| v0.1 | 2026-05-28 | Framework | Framework 아키텍처 및 설명 | 김찬석 |
| v0.2 | 2026-06-05 | Framework | Persistence, Interface, Log 추가 | 김찬석 |

## 원본 목차 (p.3)

1. 요구사항 분석  
2. 프레임워크 아키텍처  
3. Backend Framework 기능  
4. Persistence  
5. Interface  
6. 사용자 및 권한 관리  
7. 감사 추적  

## 문서가 다루는 것 / 다루지 않는 것

| 포함 | 미포함·별도 문서 예상 |
|------|------------------------|
| Zero Trust·RBAC·암호화·감사 요구 | 상세 화면 시안(FE Design) |
| Operation & Control 전체 구조도 | 설비 드라이버 상세 스펙 |
| BE Framework 패키지·핸들러·Persistence | AI 모델 학습 파이프라인 상세 |
| 메시지 Interface 개념 | 업종별 POM/RMS 업무 프로세스 상세 |
| Password Policy·User Behavior Log | 운영 Runbook / SLA |

## 핵심 키워드

`Zero Trust` · `RBAC` · `PI-LAM` · `MICC` · `DTAA` · `MIAM` · `trace_id` ·  
`Worker > Rule > Operation` · `AmsRepository` · `Kafka` · `gRPC` · `ELK` ·  
`PostgreSQL/TimescaleDB` · `Spring Boot 4` · `Java 21` · `React`
