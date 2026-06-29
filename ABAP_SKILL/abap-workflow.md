---
name: ebd-abap-workflow
description: EBD 시스템 ABAP 7단계 개발 워크플로우 - 요건분석부터 최종보고까지 단계별 상세 절차, PLAN.md 생성 위치(C:\ABAP_SKILL\{PROGNAME}\) 및 진행 상태 관리 규칙 포함
---

# EBD 시스템 ABAP 개발 워크플로우 가이드

> GitHub Copilot + VS Code + abapfs 환경에서의 표준 개발 프로세스

---

## 전체 흐름

```
개발 요청 접수
    
[STEP 1] 요건 분석
    
[STEP 2] PLAN.md 생성  C:\ABAP_SKILL\{PROGNAME}\PLAN.md
    
[STEP 3] 사용자 승인 요청  (거부 시 STEP 2로)
     (승인)
[STEP 4] 코드 개발 (INCLUDE 순서대로)
    
[STEP 5] ATC 검증  (에러 발견 시 STEP 4로)
     (에러 0건)
[STEP 6] 교차 검증 (PLAN vs 구현 100%)
    
[STEP 7] 최종 보고 + PLAN.md 완료 업데이트
    
개발 완료
```

---

## STEP 1: 요건 분석

**AI 수행 작업**:
1. 사용자 요구사항 파악 및 명확화 질문
2. `search_abap_objects`로 관련/유사 기존 프로그램 검색
3. `get_abap_object_lines`로 참조 프로그램 구조 분석
4. 사용할 BAPI/함수/클래스 실제 시그니처 확인 (추측 절대 금지)
5. 관련 DB 테이블 구조 파악

---

## STEP 2: PLAN.md 개발 계획서 생성

**AI 수행 작업**:
```
1. 폴더 생성: C:\ABAP_SKILL\{프로그램명}\
2. PLAN.md 파일 생성 (abap-dev-plan-template 스킬 양식 준수)
3. 아래 항목 채우기:
   - 기본 정보 (프로그램명, 타입, 패키지, T-Code)
   - 요건 분석 (개요, 참조 프로그램, 영향 범위)
   - INCLUDE 구조 설계
   - Phase 1~5 체크리스트
4. 사용자에게 계획 내용 보고
```

---

## STEP 3: 사용자 승인

**규칙**: 사용자 승인 전 코드 착수 절대 금지

**AI 수행 작업**:
1. PLAN.md 내용 사용자에게 요약 보고
2. 불명확한 부분 질문 (있는 경우)
3. **사용자 승인 대기**
4. 승인 후 PLAN.md Phase 1 완료 표시

---

## STEP 4: 코드 개발 (계획 기반)

**코딩 원칙**:
- PLAN.md Phase 2 체크리스트 순서대로 진행
- 각 Include 순서: TOP  SCR  CLS  F01  O01  I01  F02  메인
- 각 Include 완성 시 PLAN.md 체크박스 `[x]` 업데이트
- 공통 유틸리티 우선 활용 (ZFIR00010, ZCL_FI_COMMON)
- abap-core-standards 스킬 자체 검증 체크리스트 확인

---

## STEP 5: ATC 보안/문법 검증

**AI 수행 작업**:
```
1. run_atc_analysis 실행 (objectName = {프로그램명})
2. 결과 분류:
   - Priority 1 (Error)   즉시 수정  재분석
   - Priority 2 (Warning)  내용 검토  수정 또는 이슈 문서화
   - Priority 3 (Info)    검토 후 적용 여부 판단
3. 최종 Error 0건 확인 필수
```

**ATC 결과 기록** (PLAN.md Section 6):
```
| 분석 일시  | YYYY.MM.DD HH:MM |
| Error 건수 | 0건              |
| Warning 건수 | N건 처리 완료  |
| 최종 결과  |  통과          |
```

---

## STEP 6: 교차 검증

**비교 항목**:
```
[ ] PLAN.md Phase 2 모든 Include vs 실제 구현 (100% 일치)
[ ] 헤더 주석 완성도 (Program ID, Title, T-Code, 작성자, 변경이력)
[ ] 네이밍 컨벤션 (변수명 G/L 접두사, Include명, 클래스명)
[ ] 공통 유틸리티 활용 (check_auth, check_bukrs, f4_bukrs)
[ ] 권한 체크: START-OF-SELECTION 첫줄 check_auth 확인
[ ] 입력값 검증: check_input FORM 포함 확인
[ ] 에러 처리: gv_err = zfi01_error 패턴 확인
[ ] 표준 메시지: s037/s019(zcam01) 사용 확인
[ ] SELECT 최적화: 필드 지정, FOR ALL ENTRIES 전 IS NOT INITIAL
```

---

## STEP 7: 최종 결과 보고

**표준 보고 형식**:
```
========================================
[개발 완료 보고]
========================================
 프로그램명   : {프로그램명}
 계획서 위치  : C:\ABAP_SKILL\{프로그램명}\PLAN.md
 ATC 분석    : Error 0건, Warning N건 (처리 완료)
 교차 검증   : 계획 준수율 100%
 패키지      : {패키지명}
 T-Code      : {트랜잭션코드}
 특이사항    : {있는 경우 기재, 없으면 없음}
========================================
다음 단계: TR 할당 후 배포 준비
```

---

## PLAN.md 진행 상태 아이콘

| 아이콘 | 의미 |
|--------|------|
|  | 완료 |
|  | 진행 중 |
|  | 대기 |
|  | 실패/재작업 |

---

## 시나리오별 사용 예시

### A. 신규 Report 개발
```
요청  abap-new-report 스킬 사용
STEP 1~7 자동 진행  PLAN.md: C:\ABAP_SKILL\ZFIR{번호}\PLAN.md
```

### B. 기존 코드 리뷰/개선
```
요청  abap-code-review 스킬 사용
표준 대비 위반/개선 항목 목록 출력  수정 코드 제시
```

### C. 보안/성능 점검
```
요청  abap-security-check 스킬 사용
checklists/security-check 스킬 기반 자동 점검
```

### D. 리팩토링
```
요청  abap-refactoring 스킬 사용
변경 계획 수립  승인  수정 코드 생성  ATC 검증
```