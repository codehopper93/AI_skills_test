---
name: ebd-abap-core-standards
description: "EBD 시스템 ABAP 핵심 코딩 표준 - 헤더 주석, INCLUDE 구조, 네이밍 규칙, 공통 유틸리티, 에러 처리, 성능/보안 규칙 및 금지 패턴 전체 포함"
---

# EBD 시스템 ABAP 핵심 코딩 표준

> 모든 코드 생성 전 이 표준을 확인하고, 생성 후에는 자체 검증 체크리스트를 수행한다.

---

## 1. 시스템 기본 정보

| 항목 | 값 |
|------|-----|
| 연결 ID | EBD |
| FI 공통 프로그램 | ZFIR00010 |
| FI 공통 클래스 | ZCL_FI_COMMON |
| FI 메시지 클래스 | ZFIM01 |
| 공통 메시지 클래스 | ZCAM01 |
| 에러 상수 | ZFI01_ERROR, ZFI01_E |
| 참조 프로그램 | ZFIR40090, ZFIR22030, ZFIR24000, ZFIMLS1010, ZFIR40160 |

---

## 2. 필수 규칙 (위반 시 즉시 수정)

### 2.1 헤더 주석 형식

```abap
************************************************************************
* Program ID    : ZFIR40090
* 모듈/서브모듈 : FI
* Title         : [FI] 프로그램 제목
* Type          : Report | Online | Interface | BDC
* T-Code        : ZFIR40090
* 작성자        : ABAP16
* 작성일자      : 2026.06.15
* Description   : 프로그램 상세 설명
************************************************************************
*                               변경이력                               *
* -------- ---------- -------------- --------------------------------- *
* 변경번호  변경일자  변경자(요청자)             변경 내용             *
* -------- ---------- -------------- --------------------------------- *
*    N     2026.06.15 ABAP16         최초 작성                         *
************************************************************************
```

### 2.2 REPORT 문

```abap
" [DO] MESSAGE-ID 반드시 포함
REPORT zfir40090 MESSAGE-ID zfim01.
```

### 2.3 권한 체크 (START-OF-SELECTION 첫째)

```abap
START-OF-SELECTION.
  PERFORM check_auth IN PROGRAM zfir00010 IF FOUND.
  PERFORM check_input.
```

### 2.4 회사코드 검증

```abap
AT SELECTION-SCREEN ON p_bukrs.
  PERFORM check_bukrs IN PROGRAM zfir00010 IF FOUND
                      USING    p_bukrs
                      CHANGING gs_bukrs_info.
```

### 2.5 INCLUDE 선언 순서 (표준 고정)

```abap
INCLUDE {prog}top.   " 전역 데이터 선언
INCLUDE {prog}scr.   " Selection Screen
INCLUDE {prog}cls.   " 클래스 정의 (있는 경우)
INCLUDE {prog}o01.   " PBO Modules
INCLUDE {prog}i01.   " PAI Modules
INCLUDE {prog}f01.   " FORM 루틴 - 핵심 로직
INCLUDE {prog}f02.   " FORM 루틴 - ALV/보조 (필요시)
```

---

## 3. 네이밍 규칙

### 3.1 프로그램명: Z{모듈}{R/M/I}{5숫자}

| 타입 | 설명 | 예시 |
|------|------|------|
| R | Report (조회/출력) | ZFIR40090 |
| M | Online (C/R/U/D) | ZFIM10010 |
| I | Interface | ZFII10010 |

### 3.2 변수 접두사 (G=전역, L=로컬)

```abap
DATA: gt_mara  TYPE TABLE OF mara,         " GT_/LT_ : 인터널 테이블
      gs_mara  LIKE LINE OF gt_mara,        " GS_/LS_ : 구조체
      gv_cnt   TYPE i,                      " GV_/LV_ : 단순변수
      gc_x     TYPE c LENGTH 1 VALUE 'X',   " GC_/LC_ : 상수
      gr_bukrs TYPE RANGE OF bukrs,         " GR_/LR_ : Range
      gf_done  TYPE c LENGTH 1,             " GF_/LF_ : 플래그
      go_alv   TYPE REF TO cl_gui_alv_grid. " GO_/LO_ : 객체참조

PARAMETERS:     p_bukrs TYPE bukrs.         " P_ : 파라미터
SELECT-OPTIONS: s_matnr FOR mara-matnr.     " S_ : SELECT-OPTIONS
```

### 3.3 기타 오브젝트명

- 클래스: ZCL_{모듈}_{기능명} (예: ZCL_FI_COMMON)
- 함수그룹: Z{모듈}{3숫자} (예: ZFI001)
- 함수모듈: Z{모듈}_{서술명} (예: ZFI_FIDOC_DATA_FOR_PRINT)
- 테이블: Z{모듈}T{5숫자} (예: ZFIT10010)
- 구조체: Z{모듈}S{5숫자} (예: ZFIS0010)
- 메시지클래스: Z{모듈}M{2숫자} (예: ZFIM01)

---

## 4. 공통 유틸리티 (직접 구현 절대 금지)

```abap
" [1] 회사코드 F4 도움말
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_bukrs.
  CALL METHOD zcl_fi_common=>f4_bukrs( ).

" [2] GSBER 권한 검증
CALL METHOD zcl_fi_common=>check_gsber
  EXPORTING it_gsber = s_gsber[]
  EXCEPTIONS no_authorization = 1 invalid_business_area = 2 OTHERS = 3.

" [3] WERKS 권한 검증
CALL METHOD zcl_fi_common=>check_werks
  EXPORTING it_werks = s_werks[]
  EXCEPTIONS no_authorization = 1 OTHERS = 3.

" [4] ALV Variant F4
AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_vari1.
  PERFORM f4_alv_variant IN PROGRAM zfir00010 IF FOUND
                         USING '0001' sy-repid CHANGING p_vari1.

" [5] 조회 결과 없음
MESSAGE s037(zcam01) DISPLAY LIKE gc_e.

" [6] 조회 건수 표시
MESSAGE s019(zcam01) WITH CONV i( CONV string( lines( gt_result ) ) ).
```

---

## 5. 에러 처리 표준

```abap
IF gt_result[] IS INITIAL.
  IF gv_err = zfi01_error.
    EXIT.
  ELSE.
    MESSAGE s037(zcam01) DISPLAY LIKE gc_e.
    EXIT.
  ENDIF.
ELSE.
  MESSAGE s019(zcam01) WITH CONV i( CONV string( lines( gt_result ) ) ).
ENDIF.
```

---

## 6. 성능 규칙

```abap
" [DO] 필요 필드만 SELECT
SELECT matnr maktx INTO TABLE @lt_mara FROM mara WHERE mtart = @lv_type.

" [DO] FOR ALL ENTRIES 전 IS NOT INITIAL 체크 필수
IF lt_key[] IS NOT INITIAL.
  SELECT * INTO TABLE @lt_data FROM ztable
  FOR ALL ENTRIES IN @lt_key WHERE key = @lt_key-key.
ENDIF.

" [DO] SORT + READ TABLE BINARY SEARCH
SORT lt_data BY key1 key2.
READ TABLE lt_data INTO ls_data WITH KEY key1 = lv_val BINARY SEARCH.
```

---

## 7. 보안 규칙

```abap
" [DO] 권한 체크
AUTHORITY-CHECK OBJECT 'F_BKPF_BUK'
  ID 'ACTVT' FIELD '03' ID 'BUKRS' FIELD p_bukrs.
IF sy-subrc <> 0.
  MESSAGE s001(zfim01) DISPLAY LIKE gc_e. EXIT.
ENDIF.

" [DO] 입력값 검증 FORM 분리
FORM check_input.
  IF p_bukrs IS INITIAL. MESSAGE e001(zfim01). EXIT. ENDIF.
  IF p_from > p_to. MESSAGE e002(zfim01) DISPLAY LIKE gc_e. EXIT. ENDIF.
ENDFORM.
```

---

## 8. 금지 패턴 (절대 금지)

- `SELECT *` 대형 테이블 (BKPF, BSEG, MARA 등) WHERE 조건 없음
- `COMMIT WORK` 직접 호출  `CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'` 사용
- Hard-coded 계정정보/비밀번호
- Dynamic SQL에 미검증 입력값 사용
- ZFIR00010, ZCL_FI_COMMON 기능 직접 재구현
- 함수/메서드 시그니처 추측 사용 (반드시 get_abap_object_lines 확인)
- INCLUDE 선언 없이 메인 프로그램에 모든 코드 작성
- `SELECT ENDSELECT` Loop DB 호출
- 메시지 클래스 없이 MESSAGE 사용
- `adt://` URI 방식으로 파일 검색

---

## 9. 코드 생성 후 자체 검증 체크리스트

```
[ ] 헤더 주석 완성 (Program ID, Title, Type, T-Code, 작성자, 변경이력)
[ ] REPORT {prog} MESSAGE-ID zfim01. 포함
[ ] INCLUDE 순서: TOPSCRCLSO01I01F01F02
[ ] START-OF-SELECTION 첫줄: check_auth IN PROGRAM zfir00010 IF FOUND.
[ ] AT SELECTION-SCREEN ON p_bukrs: check_bukrs IN PROGRAM zfir00010
[ ] AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_bukrs: zcl_fi_common=>f4_bukrs
[ ] 변수명 G/L 접두사 준수 (GT_,GS_,GV_,GC_,GR_,GF_,GO_)
[ ] 에러처리: gv_err = zfi01_error 패턴 포함
[ ] 표준 메시지: s037/s019(zcam01) 사용
[ ] SELECT: 필요 필드만 조회, FOR ALL ENTRIES 전 IS NOT INITIAL 체크
[ ] 보안: 권한 체크, 입력값 검증 FORM 포함
[ ] ATC 분석 예정 (run_atc_analysis 실행 계획)
```