---
name: ebd-abap-new-report
description: EBD 시스템 ALV Report 신규 개발 프롬프트 - Selection Screen 설계, INCLUDE 구조(TOP/SCR/F01/F02), 공통 유틸리티(check_auth/check_bukrs/f4_bukrs), ALV 이벤트 FORM 자동 생성 포함
---

# EBD 시스템 Report 신규 개발 프롬프트

## 목적
ALV Report 형태의 조회 프로그램을 신규 개발할 때 사용합니다.

---

## 프롬프트 본문

너는 SAP ABAP 시니어 개발자이며 EBD 시스템 표준을 준수한다.
연결 ID: EBD
핵심 표준: abap-core-standards 스킬 참조

[신규 Report 개발 요청]
- 프로그램명: {ZFIR/ZFIM/ZFII + 5자리 번호}
- 제목: {[모듈] 제목}
- T-Code: {프로그램명과 동일}
- 패키지: {ZFI01 등}
- Selection Screen 조건:
  * 회사코드(P_BUKRS, 필수): PARAMETERS p_bukrs TYPE bukrs
  * {추가 조건 1}: {타입 및 필수여부}
  * {추가 조건 2}: {타입 및 필수여부}
- 조회 테이블: {예: BKPF JOIN BSEG, 또는 KNA1}
- ALV 출력 필드: {필드명 목록}
- 특이사항: {있는 경우}

[AI 수행 지시]
1. 아래 Include를 순서대로 생성:
   - {PROGNAME}TOP: TYPE/DATA/CONSTANTS (gc_x, gc_e, gs_bukrs_info, gt_result 포함)
   - {PROGNAME}SCR: SELECTION-SCREEN (p_bukrs, p_vari1 포함)
   - {PROGNAME}F01: FORM check_input, select_data, display_data
   - {PROGNAME}O01: PBO (필요시)
   - {PROGNAME}I01: PAI (필요시)
   - {PROGNAME}F02: ALV 이벤트 FORM (user_command_alv)
2. 메인 프로그램: 표준 헤더 + INCLUDE 선언 + 표준 이벤트 블록
3. 필수 요소 반드시 포함:
   - check_auth IN PROGRAM zfir00010 IF FOUND (START-OF-SELECTION 첫줄)
   - check_bukrs IN PROGRAM zfir00010 (AT SELECTION-SCREEN ON p_bukrs)
   - zcl_fi_common=>f4_bukrs (F4 도움말)
   - MESSAGE s037/s019(zcam01) 표준 메시지
4. 코드 생성 후 abap-core-standards 스킬의 자체 검증 체크리스트 수행
5. 7단계 개발 프로세스 적용 (abap-workflow 스킬 참조)

---

## 메인 프로그램 표준 이벤트 블록 구조

```abap
INITIALIZATION.
*----------------------------------------------------------------------*
* 초기값 설정
*----------------------------------------------------------------------*
  PERFORM init_screen.

AT SELECTION-SCREEN OUTPUT.
*----------------------------------------------------------------------*
* 선택화면 출력
*----------------------------------------------------------------------*

AT SELECTION-SCREEN ON p_bukrs.
*----------------------------------------------------------------------*
* 회사코드 검증
*----------------------------------------------------------------------*
  PERFORM check_bukrs IN PROGRAM zfir00010 IF FOUND
                      USING    p_bukrs
                      CHANGING gs_bukrs_info.

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_bukrs.
  CALL METHOD zcl_fi_common=>f4_bukrs( ).

AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_vari1.
  PERFORM f4_alv_variant IN PROGRAM zfir00010 IF FOUND
                         USING '0001' sy-repid CHANGING p_vari1.

AT SELECTION-SCREEN.
  PERFORM check_input.

START-OF-SELECTION.
*----------------------------------------------------------------------*
* 권한 체크 (필수 첫번째)
*----------------------------------------------------------------------*
  PERFORM check_auth IN PROGRAM zfir00010 IF FOUND.
  PERFORM select_data.

END-OF-SELECTION.
  PERFORM display_data.
```