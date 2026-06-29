---
name: ebd-abap-dev-plan-template
description: "EBD 시스템 ABAP 개발계획서(PLAN.md) 템플릿 - 기본정보/요건분석/INCLUDE구조/Phase1~5 체크리스트/ATC결과/교차검증표/최종확인 포함. 저장위치: C:\ABAP_SKILL\{PROGNAME}\PLAN.md"
---

# EBD 시스템 ABAP 개발계획서 템플릿

> 저장 위치: `C:\ABAP_SKILL\{프로그램명}\PLAN.md`
> PLAN.md 생성 후 사용자에게 요약 보고 및 승인 요청 필수

---

## PLAN.md 생성 템플릿

아래 내용에서 `{변수}` 부분을 실제 내용으로 채워 파일로 저장하라.

---

# [{PROG_NAME}] 개발 계획서

생성일: {YYYY.MM.DD}
생성자: GitHub Copilot (AI 자동 생성)
표준 참조: C:\ABAP_SKILL\SKILL.md

---

## 1. 기본 정보

| 항목 | 내용 |
|------|------|
| 프로그램명 | {프로그램명} |
| 프로그램 제목 | {프로그램 제목} |
| 타입 | Report / Online / Interface / BDC |
| 패키지 | {패키지명 예: ZFI01} |
| T-Code | {트랜잭션코드} |
| 메시지 클래스 | ZFIM01 |
| 작성자 | {작성자 ID} |
| 요청자 | {요청자 이름/직위} |

---

## 2. 요건 분석

### 2.1 요건 개요
{요건 상세 설명}

### 2.2 참조 프로그램

| 프로그램명 | 참조 이유 |
|-----------|----------|
| {참조 프로그램} | {참조 이유} |
| ZFIR40090 | 표준 구조 참조 |

### 2.3 영향 범위

| 구분 | 오브젝트명 | 용도 |
|------|-----------|------|
| DB 테이블 | {테이블명} | {용도} |
| BAPI/함수 | {함수명} | {용도} |
| 클래스 | ZCL_FI_COMMON | 공통 유틸리티 |
| 공통 프로그램 | ZFIR00010 | check_auth, check_bukrs |

---

## 3. INCLUDE 구조 설계

| Include명 | 용도 | 주요 내용 |
|-----------|------|----------|
| {PROGNAME}TOP | 전역 데이터 | TYPE 정의, DATA 선언, CONSTANTS |
| {PROGNAME}SCR | Selection Screen | 조회 조건 화면 |
| {PROGNAME}CLS | Local Class | (필요시) |
| {PROGNAME}O01 | PBO Module | 화면 출력 처리 |
| {PROGNAME}I01 | PAI Module | 사용자 입력 처리 |
| {PROGNAME}F01 | FORM 루틴 | 핵심 업무 로직 |
| {PROGNAME}F02 | FORM 루틴 | ALV 이벤트, 보조 로직 |

---

## 4. 개발 계획 체크리스트

### Phase 1: 분석 및 설계
- [ ] 요건 분석 완료
- [ ] 기존 관련 프로그램 검색 및 분석
- [ ] 관련 테이블/함수/BAPI 목록 확인
- [ ] 실제 함수/메서드 시그니처 검증 (get_abap_object_lines 활용)
- [ ] INCLUDE 구조 설계 완료
- [ ] 사용자 계획 승인 완료

### Phase 2: INCLUDE 개발
- [ ] {PROGNAME}TOP 작성 (TYPE, DATA, CONSTANTS)
- [ ] {PROGNAME}SCR 작성 (Selection Screen)
- [ ] {PROGNAME}CLS 작성 (필요시)
- [ ] {PROGNAME}F01 작성 (핵심 업무 로직)
- [ ] {PROGNAME}O01 작성 (PBO Module)
- [ ] {PROGNAME}I01 작성 (PAI Module)
- [ ] {PROGNAME}F02 작성 (ALV/보조 - 필요시)

### Phase 3: 메인 프로그램
- [ ] 헤더 주석 작성 (표준 박스형)
- [ ] REPORT 문 + MESSAGE-ID
- [ ] INCLUDE 선언 (표준 순서)
- [ ] INITIALIZATION 이벤트
- [ ] AT SELECTION-SCREEN ON p_bukrs (check_bukrs)
- [ ] AT SELECTION-SCREEN ON VALUE-REQUEST FOR p_bukrs
- [ ] AT SELECTION-SCREEN (check_input)
- [ ] START-OF-SELECTION (check_auth 첫째 포함)
- [ ] END-OF-SELECTION
- [ ] 활성화(Activate) 성공

### Phase 4: 보안/문법 검증 (ATC)
- [ ] run_atc_analysis 실행
- [ ] Error 등급 발견 건 수정
- [ ] Warning 등급 검토 및 처리
- [ ] ATC 재분석 최종 에러 0건 확인
- [ ] 권한 체크 로직 확인
- [ ] 입력값 검증 확인
- [ ] 공통 유틸리티 활용 여부 확인

### Phase 5: 교차 검증
- [ ] PLAN.md Phase 목록 vs 실제 구현 100% 비교
- [ ] 네이밍 컨벤션 준수 확인
- [ ] 헤더 주석 완성도 확인
- [ ] 변경이력 기재 확인
- [ ] 성능 영향 검토
- [ ] 최종 사용자 확인 요청

---

## 5. 진행 현황

| 단계 | 상태 | 완료일 | 비고 |
|------|------|--------|------|
| Phase 1: 분석/설계 |  진행 중 | | |
| Phase 2: INCLUDE 개발 |  대기 | | |
| Phase 3: 메인 프로그램 |  대기 | | |
| Phase 4: ATC 검증 |  대기 | | |
| Phase 5: 교차 검증 |  대기 | | |

상태:  완료 /  진행 중 /  대기 /  실패

---

## 6. ATC 검증 결과

| 항목 | 내용 |
|------|------|
| 분석 일시 | {YYYY.MM.DD HH:MM} |
| Error 건수 | {N}건 |
| Warning 건수 | {N}건 |
| 최종 결과 |  통과 /  미통과 |

---

## 7. 교차 검증 결과

| 검증 항목 | 계획 | 구현 | 일치 |
|----------|------|------|------|
| INCLUDE 구조 | {계획} | {실제} | / |
| 헤더 주석 | 표준 박스형 | | / |
| 변수 네이밍 | G/L 접두사 | | / |
| 권한 체크 | check_auth | | / |
| 공통 유틸 | check_bukrs, f4_bukrs | | / |
| 에러 처리 | gv_err 패턴 | | / |

계획 준수율: {N}%

---

## 8. 최종 확인

- [ ] 개발 완료
- [ ] ATC 검증 완료 (에러 0건)
- [ ] 교차 검증 완료
- [ ] 사용자 최종 확인 완료
- [ ] 운영 이관 준비 완료