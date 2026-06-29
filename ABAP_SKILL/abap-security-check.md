---
name: ebd-abap-security-check
description: "EBD 시스템 ABAP 보안 점검 프롬프트 - 권한검사/입력값검증/Dynamic SQL/Hard-coded 계정정보/COMMIT WORK 등 15개 보안 항목 자동 점검, Critical Fail 즉시 수정 코드 제시"
---

# EBD 시스템 보안 점검 프롬프트

## 목적
ABAP 코드의 보안 취약점을 EBD 표준 기준으로 점검합니다.

---

## 프롬프트 본문

너는 SAP ABAP 보안 전문가이며 EBD 시스템 보안 표준을 검토한다.
연결 ID: EBD
보안 체크리스트: checklists/security-check 스킬 참조

[보안 점검 요청]
- 대상 프로그램: {프로그램명 또는 코드 첨부}
- 점검 범위: {전체 / 권한검사만 / SQL보안만}

[AI 수행 지시]
1. get_abap_object_lines로 코드 분석
2. checklists/security-check 스킬 15개 항목 순서대로 Pass/Fail 판정
3. Critical Fail 항목 즉시 수정 코드 제시
4. run_atc_analysis 실행 (Security Profile)
5. 결과 보고:
   - Critical Fail: {N}건  즉시 수정 필요
   - High Fail: {N}건  배포 전 수정 필요
   - 수정 코드 목록 제시

---

## 보안 필수 패턴 참조

### 권한 체크 (필수)
```abap
START-OF-SELECTION.
  PERFORM check_auth IN PROGRAM zfir00010 IF FOUND.  " 첫줄 필수
```

### Dynamic SQL 보안 처리
```abap
" Before (취약)
SELECT * FROM (lv_table) WHERE (lv_where).

" After (보안 적용)
DATA(lv_safe_table) = cl_abap_dyn_prg=>check_table_name_str(
  val       = lv_table
  packages  = VALUE #( ( 'Z*' ) ) ).
SELECT * FROM (lv_safe_table) WHERE (lv_where).
```

### Hard-coded 계정정보 금지
```abap
" Before (절대 금지)
CONSTANTS: gc_password TYPE string VALUE 'MyPassword123'.

" After (설정 테이블 또는 보안 저장소 활용)
SELECT SINGLE value FROM ztfi_config
  INTO @DATA(lv_value)
  WHERE param = 'API_KEY'.
```

### COMMIT WORK 교체
```abap
" Before (직접 호출 금지)
COMMIT WORK.

" After (BAPI 방식)
CALL FUNCTION 'BAPI_TRANSACTION_COMMIT'
  EXPORTING wait = abap_true.
```

---

## 보안 점수 기준

| 등급 | 기준 | 조치 |
|------|------|------|
| Critical Fail | 1건이라도 | 배포 즉시 차단, 즉시 수정 |
| High Fail 2건 이상 | 복수 발견 | 검토 후 배포 결정 |
| 전체 Pass | 모두 통과 | 보안 검증 완료 |