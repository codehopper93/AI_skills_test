---
name: ebd-abap-quality-checklist
description: "EBD 시스템 ABAP 품질 체크리스트 30항목 - 헤더/문서화/구조/권한보안/공통유틸/에러처리/네이밍/성능/가독성/재사용성/모듈화 카테고리별 Pass/Fail 판정 기준, 품질 점수 산출식 포함"
---

# EBD 시스템 품질 체크리스트

> 코드 개발 완료 후 AI 자동 점검 또는 개발자 수동 점검
> Pass 조건: 해당 항목이 코드에 존재하거나 기준 충족
> High 영향도 항목 1개라도 Fail  배포 불가

---

## 체크리스트 30항목

| # | 카테고리 | 검증 항목 | Pass 조건 | 영향도 |
|---|---------|---------|---------|--------|
| 1 | 헤더/문서화 | 박스형 헤더 주석 존재 | Program ID, Title, Type, T-Code, 작성자, 작성일자 모두 있으면 Pass | High |
| 2 | 헤더/문서화 | 변경이력 테이블 존재 | 변경번호, 변경일자, 변경자, 변경내용 항목 있으면 Pass | High |
| 3 | 헤더/문서화 | 최초 작성 이력 N 항목 | 변경번호 N 행이 있으면 Pass | Medium |
| 4 | 구조 | MESSAGE-ID 선언 | REPORT {prog} MESSAGE-ID {class}. 형태면 Pass | High |
| 5 | 구조 | INCLUDE 선언 존재 | TOP, O01, I01, F01 중 최소 2개 이상 있으면 Pass | High |
| 6 | 구조 | INCLUDE 순서 준수 | TOPSCRCLSO01I01F01F02 순서면 Pass | Medium |
| 7 | 구조 | 이벤트 블록 순서 | INITIALIZATIONAT SEL-SCRSTART-OF-SELEND-OF-SEL 순이면 Pass | Medium |
| 8 | 권한/보안 | START-OF-SELECTION 첫줄 check_auth | PERFORM check_auth IN PROGRAM zfir00010 IF FOUND. 가 첫번째면 Pass | High |
| 9 | 권한/보안 | 입력값 검증 FORM | check_input FORM 존재하면 Pass | High |
| 10 | 공통 유틸 | 회사코드 검증 공통 루틴 | check_bukrs IN PROGRAM zfir00010 사용하면 Pass | High |
| 11 | 공통 유틸 | F4 도움말 공통 클래스 | zcl_fi_common=>f4_bukrs 사용하면 Pass | Medium |
| 12 | 에러 처리 | 에러 플래그 패턴 | gv_err + zfi01_error 비교 있으면 Pass | High |
| 13 | 에러 처리 | 표준 메시지 사용 | s037(zcam01) 또는 s019(zcam01) 있으면 Pass | Medium |
| 14 | 에러 처리 | sy-subrc 처리 | CALL/SELECT 후 sy-subrc 체크 있으면 Pass | Medium |
| 15 | 네이밍 | 인터널 테이블 GT_/LT_ | 전역 테이블명이 GT_로 시작하면 Pass | Medium |
| 16 | 네이밍 | 구조체 GS_/LS_ | 전역 구조체명이 GS_로 시작하면 Pass | Medium |
| 17 | 네이밍 | 상수 GC_/LC_ | CONSTANTS 변수명이 GC_ 또는 LC_로 시작하면 Pass | Medium |
| 18 | 네이밍 | PARAMETERS P_ | PARAMETERS 변수명이 P_로 시작하면 Pass | Medium |
| 19 | 네이밍 | SELECT-OPTIONS S_ | SELECT-OPTIONS 변수명이 S_로 시작하면 Pass | Medium |
| 20 | 네이밍 | 프로그램명 형식 | Z{2자}[R/M/I]{5숫자} 형식이면 Pass | High |
| 21 | 성능 | SELECT 필드 지정 | SELECT * 없이 필드 명시하면 Pass | High |
| 22 | 성능 | FOR ALL ENTRIES IS NOT INITIAL | FOR ALL ENTRIES 앞에 IS NOT INITIAL 체크 있으면 Pass | High |
| 23 | 성능 | SORT+READ BINARY SEARCH | READ TABLE 시 BINARY SEARCH 사용하면 Pass | Medium |
| 24 | 성능 | SELECT ENDSELECT 없음 | SELECT~ENDSELECT 구문이 없으면 Pass | High |
| 25 | 가독성 | FORM 의미있는 이름 | FORM 이름이 동사+목적어 형태면 Pass | Low |
| 26 | 가독성 | 섹션 구분 주석 | 구분 주석이 이벤트 블록마다 있으면 Pass | Low |
| 27 | 가독성 | 소문자 키워드 | ABAP 키워드 소문자 사용하면 Pass (권장) | Low |
| 28 | 재사용성 | 비즈니스 로직 FORM 분리 | 로직이 FORM/METHOD로 분리되어 있으면 Pass | Medium |
| 29 | 재사용성 | 공통 유틸 직접 구현 없음 | ZCL_FI_COMMON 기능 재구현 없으면 Pass | High |
| 30 | 모듈화 | TOP Include 분리 | 전역 DATA/TYPE 선언이 TOP Include에 있으면 Pass | Medium |

---

## 품질 점수 산출

```
Pass 항목 수 / 30 x 100 = 품질 점수

90점 이상    : 우수
70~89점     : 양호
70점 미만   : 재검토 필요

High 영향도 항목 1개라도 Fail  배포 불가
```