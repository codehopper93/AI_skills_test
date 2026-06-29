---
name: ebd-abap-security-checklist
description: "EBD 시스템 ABAP 보안 체크리스트 15항목 - 권한검사/입력검증/SQL보안/데이터보호/로깅 카테고리별 Pass/Fail 판정, Critical Fail=즉시 배포차단/High Fail 2건이상=재검토 기준"
---

# EBD 시스템 보안 체크리스트

> 코드 완성 후 보안 점검 (ATC 이전 선점검)
> Critical Fail 항목 발견 시 즉시 수정 후 배포

---

## 보안 체크리스트 15항목

| # | 카테고리 | 검증 항목 | Pass 조건 | 영향도 |
|---|---------|---------|---------|--------|
| 1 | 권한 검사 | START-OF-SELECTION check_auth | check_auth IN PROGRAM zfir00010 첫줄에 있으면 Pass | Critical |
| 2 | 권한 검사 | AUTHORITY-CHECK 사용 시 결과 처리 | sy-subrc 체크 후 거부 처리 있으면 Pass | Critical |
| 3 | 권한 검사 | 민감 데이터 별도 권한 체크 | 급여/개인정보 조회 시 추가 AUTHORITY-CHECK 있으면 Pass | High |
| 4 | 입력 검증 | check_input FORM 존재 | 입력값 검증 FORM 있으면 Pass | High |
| 5 | 입력 검증 | 필수 입력값 IS INITIAL 체크 | 핵심 파라미터 빈값 체크 있으면 Pass | High |
| 6 | 입력 검증 | 날짜/범위 논리 검증 | 시작일 <= 종료일 체크 있으면 Pass | Medium |
| 7 | SQL 보안 | Dynamic SQL 입력값 검증 | Dynamic WHERE 사용 시 CL_ABAP_DYN_PRG 검증 있으면 Pass | Critical |
| 8 | SQL 보안 | 정적 SQL 우선 사용 | Dynamic SQL 비율이 20% 미만이면 Pass | High |
| 9 | SQL 보안 | SELECT * 대형 테이블 없음 | BKPF/BSEG/MARA 등에 SELECT * 없으면 Pass | High |
| 10 | 데이터 보호 | Hard-coded 계정정보 없음 | 비밀번호/계정정보 상수값 없으면 Pass | Critical |
| 11 | 데이터 보호 | COMMIT WORK 직접 호출 없음 | BAPI_TRANSACTION_COMMIT 사용하면 Pass | High |
| 12 | 데이터 보호 | 개인정보 로깅 없음 | 주민번호/전화번호를 로그에 직접 기록하지 않으면 Pass | High |
| 13 | 에러 처리 | 예외 발생 시 사용자 정보 미노출 | 기술적 에러 메시지를 사용자에게 직접 노출하지 않으면 Pass | Medium |
| 14 | 로깅 | 중요 트랜잭션 로그 | 데이터 변경 시 변경 로그 작성 있으면 Pass | Medium |
| 15 | 검증 완료 | ATC Security Check 실행 | run_atc_analysis 실행 후 Security 에러 0건이면 Pass | Critical |

---

## 보안 점수 기준

```
Critical 항목 Fail 시   배포 즉시 차단, 즉시 수정 필수
High 항목 2개 이상 Fail  검토 후 배포 결정
전체 Pass               보안 검증 완료 
```

---

## 주요 보안 수정 패턴

### Dynamic SQL 보안 처리
```abap
" Before (취약)
SELECT * FROM (lv_table) WHERE (lv_where).

" After (안전)
DATA(lv_safe_table) = cl_abap_dyn_prg=>check_table_name_str(
  val      = lv_table
  packages = VALUE #( ( 'Z*' ) ) ).
SELECT * FROM (lv_safe_table) WHERE (lv_where).
```

### Hard-coded 금지
```abap
" 절대 금지
CONSTANTS: gc_password TYPE string VALUE 'MySecret'.

" 권장 (설정 테이블 활용)
SELECT SINGLE value FROM ztfi_config
  INTO @DATA(lv_value) WHERE param = 'API_KEY'.
```