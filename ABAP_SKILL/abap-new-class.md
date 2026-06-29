---
name: ebd-abap-new-class
description: "EBD 시스템 ABAP Class/Interface 신규 개발 프롬프트 - CLASS DEFINITION/IMPLEMENTATION, ABAP Doc 주석, TRY-CATCH 예외처리, 단위 테스트 stub 자동 생성 포함"
---

# EBD 시스템 Class 신규 개발 프롬프트

## 목적
ZCL_ 클래스 또는 ZIF_ 인터페이스를 신규 개발할 때 사용합니다.

---

## 프롬프트 본문

너는 SAP ABAP 시니어 개발자이며 EBD 시스템 표준을 준수한다.
연결 ID: EBD
핵심 표준: abap-core-standards 스킬 참조

[신규 Class 개발 요청]
- 클래스명: {ZCL_{모듈}_{기능명}}
- 패키지: {ZFI01 등}
- 역할/목적: {클래스가 담당하는 기능}
- 인터페이스 구현: {ZIF_xxx 또는 없음}
- 주요 메서드:
  * {메서드명1}: {IN/OUT 파라미터 및 역할}
  * {메서드명2}: {IN/OUT 파라미터 및 역할}
- 예외 클래스: {ZCX_xxx 또는 없음}
- 특이사항: {있는 경우}

[AI 수행 지시]
1. CLASS DEFINITION/IMPLEMENTATION 작성
2. 클래스 헤더 주석 포함 (역할/메서드 목록)
3. CONSTRUCTOR 메서드 포함 (필요시)
4. 각 메서드에 ABAP Doc 주석 ("! @parameter) 포함
5. TRY-CATCH 예외 처리 포함
6. 단위 테스트 stub (FOR TESTING) 포함
7. 네이밍: 메서드명은 {동사}_{목적어} 형태 (get_data, check_auth 등)
8. 7단계 개발 프로세스 적용 (abap-workflow 스킬 참조)

---

## 클래스 표준 구조

```abap
CLASS zcl_{module}_{name} DEFINITION
  PUBLIC FINAL CREATE PUBLIC.

  PUBLIC SECTION.
    "! 메서드 설명
    "! @parameter iv_param | 입력 파라미터 설명
    "! @parameter rv_result | 반환값 설명
    METHODS get_data
      IMPORTING iv_bukrs TYPE bukrs
      RETURNING VALUE(rv_result) TYPE string
      RAISING   zcx_fi_error.

    CLASS-METHODS:
      "! Factory 메서드
      create
        RETURNING VALUE(ro_instance) TYPE REF TO zcl_{module}_{name}.

  PRIVATE SECTION.
    DATA: mv_bukrs TYPE bukrs.
ENDCLASS.

CLASS zcl_{module}_{name} IMPLEMENTATION.
  METHOD create.
    ro_instance = NEW #( ).
  ENDMETHOD.

  METHOD get_data.
    TRY.
      " 비즈니스 로직
    CATCH cx_sy_no_handler INTO DATA(lx_error).
      RAISE EXCEPTION TYPE zcx_fi_error
        MESSAGE ID 'ZFIM01' TYPE 'E' NUMBER '001'
        WITH lx_error->get_text( ).
    ENDTRY.
  ENDMETHOD.
ENDCLASS.
```

---

## 단위 테스트 stub

```abap
CLASS ltc_{class_name} DEFINITION FINAL FOR TESTING
  DURATION SHORT RISK LEVEL HARMLESS.

  PRIVATE SECTION.
    DATA: mo_cut TYPE REF TO zcl_{module}_{name}.

    METHODS: setup,
             test_{method_name} FOR TESTING.
ENDCLASS.

CLASS ltc_{class_name} IMPLEMENTATION.
  METHOD setup.
    mo_cut = zcl_{module}_{name}=>create( ).
  ENDMETHOD.

  METHOD test_{method_name}.
    " Given
    " When
    " Then
    cl_abap_unit_assert=>assert_equals(
      act = 'actual'
      exp = 'expected'
      msg = '테스트 실패 메시지' ).
  ENDMETHOD.
ENDCLASS.
```