---
name: ebd-abap-final-review-checklist
description: "EBD 시스템 ABAP 배포 전 최종 검토 체크리스트 - 섹션A 필수10항목(1개 Fail=배포차단)/섹션B 품질10항목/섹션C 배포준비6항목, 종합 판정 기준 및 최종 보고 형식 포함"
---

# EBD 시스템 최종 검토 체크리스트

> 배포 전 최종 확인용. 품질/보안/성능 핵심 항목 통합 + 배포 준비 확인

---

## 섹션 A: 필수 항목 (1개라도 Fail이면 배포 차단)

| # | 항목 | Pass 조건 | Pass/Fail |
|---|------|---------|---------|
| A1 | 헤더 주석 완성 | Program ID/Title/Type/T-Code/작성자/일자/변경이력 모두 있음 | |
| A2 | REPORT MESSAGE-ID | MESSAGE-ID zfim01 포함 | |
| A3 | START-OF-SELECTION check_auth | check_auth IN PROGRAM zfir00010 첫줄에 있음 | |
| A4 | 입력값 검증 | check_input FORM 또는 동등한 검증 로직 있음 | |
| A5 | Dynamic SQL 검증 | Dynamic SQL 없거나, CL_ABAP_DYN_PRG 검증 있음 | |
| A6 | Hard-coded 계정정보 없음 | 비밀번호/토큰/계정 하드코딩 없음 | |
| A7 | FOR ALL ENTRIES IS NOT INITIAL | FOR ALL ENTRIES 전 체크 있음 | |
| A8 | ATC 에러 0건 | run_atc_analysis Error 등급 0건 | |
| A9 | 활성화 성공 | SAP 시스템에서 Activate 성공 | |
| A10 | COMMIT WORK 직접 없음 | BAPI_TRANSACTION_COMMIT 사용 | |

---

## 섹션 B: 품질 핵심 항목 (3개 이상 Fail이면 재검토)

| # | 항목 | Pass 조건 | Pass/Fail |
|---|------|---------|---------|
| B1 | INCLUDE 구조 준수 | TOPSCRCLSO01I01F01F02 순서 | |
| B2 | 네이밍 컨벤션 | 변수 G/L 접두사 (GT_,GS_,GV_,GC_,GR_) 준수 | |
| B3 | 공통 유틸 활용 | check_bukrs, f4_bukrs, f4_alv_variant 표준 사용 | |
| B4 | SELECT 필드 지정 | SELECT * 없이 필드 명시 | |
| B5 | 에러 플래그 패턴 | gv_err + zfi01_error 패턴 사용 | |
| B6 | 표준 메시지 | s037(zcam01)/s019(zcam01) 표준 사용 | |
| B7 | sy-subrc 처리 | CALL/SELECT 후 sy-subrc 체크 있음 | |
| B8 | SORT+BINARY SEARCH | READ TABLE 시 BINARY SEARCH 사용 | |
| B9 | SELECT ENDSELECT 없음 | Loop DB 접근 없음 | |
| B10 | 섹션 주석 | 이벤트 블록마다 구분 주석 있음 | |

---

## 섹션 C: 배포 준비 확인

| # | 항목 | 확인 내용 | Pass/Fail |
|---|------|---------|---------|
| C1 | TR 할당 완료 | 적절한 TR에 오브젝트 할당됨 | |
| C2 | TR 설명문 | [모듈] 개발 내용 요약 형식으로 작성됨 | |
| C3 | 의존성 확인 | 사용하는 Include/함수/클래스 모두 TR에 포함 | |
| C4 | 패키지 확인 | $TMP가 아닌 적절한 패키지(ZFI01 등)에 할당됨 | |
| C5 | T-Code 확인 | 트랜잭션 코드 생성 및 프로그램 연결 완료 | |
| C6 | PLAN.md 완료 | C:\ABAP_SKILL\{프로그램명}\PLAN.md 모든 체크박스 완료 | |

---

## 종합 판정 기준

```
섹션 A: 10/10 Pass 필수 (1건이라도 Fail  배포 불가)
섹션 B: 7/10 Pass 이상 (3건 이상 Fail  재검토)
섹션 C: 6/6 Pass 필수 (배포 준비 완료)

종합 판정: A=100% AND B>=70% AND C=100%  배포 승인
           그 외  재작업 필요
```

---

## 최종 검증 보고 형식

```
========================================
[최종 검증 보고서]
프로그램명: {프로그램명}
검증일자  : {YYYY.MM.DD}
========================================
섹션 A (필수): {10}/10 Pass
섹션 B (품질): {N}/10 Pass
섹션 C (배포): {6}/6 Pass
----------------------------------------
ATC 분석: Error 0건, Warning {N}건 처리
교차 검증: 계획 준수율 100%
----------------------------------------
종합 판정:  배포 승인 /  재작업 필요
특이사항: {있는 경우}
========================================
```