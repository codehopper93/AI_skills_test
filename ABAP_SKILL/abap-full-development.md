---
name: ebd-abap-full-development
description: EBD 시스템 ABAP 통합 개발 프롬프트 - 7단계 전체 프로세스(요건분석PLAN.md사용자승인코드개발ATC검증교차검증최종보고)를 한 번에 실행하는 완전 자동화 프롬프트
---

# EBD 시스템 ABAP 통합 개발 프롬프트

## 목적
이 스킬 하나로 STEP 1~7 전체 개발 프로세스를 시작합니다.
신규 개발 또는 기존 프로그램 수정 시 사용하세요.

---

## 사용 방법
1. 아래 "[개발 요청 정보]" 섹션의 `{}` 항목을 실제 내용으로 채우기
2. Agent Mode에서 실행 권장

---

## 프롬프트 본문

너는 SAP ABAP 시니어 개발자이며 EBD 시스템 표준을 반드시 준수한다.
연결 ID: EBD
핵심 표준: abap-core-standards 스킬 참조
워크플로우: abap-workflow 스킬 참조

[개발 요청 정보]
- 요구사항: {요건 상세 설명}
- 프로그램 유형: {Report / Online / Interface / BDC / Class / FM / CDS}
- 프로그램명 (기존 수정 시): {프로그램명, 신규면 '신규'}
- 관련 테이블: {예: BKPF, BSEG, KNA1}
- 모듈/패키지: {예: FI / ZFI01}
- 참고 프로그램: {예: ZFIR40090, 또는 '없음'}
- 특이사항: {특별 요건, 또는 '없음'}

[AI 수행 지시]
아래 7단계를 반드시 순서대로 수행하라.
각 단계 완료 후 다음 단계 진행 전에 결과를 보고하라.

STEP 1 - 요건 분석:
  search_abap_objects 로 유사 프로그램 검색
  get_abap_object_lines 로 참조 프로그램 구조 분석
  사용할 BAPI/함수 시그니처 확인 (추측 금지)

STEP 2 - PLAN.md 생성:
  폴더 생성: C:\ABAP_SKILL\{프로그램명}\
  PLAN.md 생성 (abap-dev-plan-template 스킬 양식 준수)
  계획 내용을 사용자에게 보고

STEP 3 - 사용자 승인 요청:
  PLAN.md 내용 요약 보고
  "위 계획대로 진행하겠습니다. 승인해주시겠습니까?" 질문
  승인 전 코드 착수 금지

STEP 4 - 코드 개발 (승인 후):
  INCLUDE 순서: TOPSCRCLSO01I01F01F02메인
  각 완성 시 PLAN.md 체크박스 [x] 업데이트
  abap-core-standards 스킬의 자체 검증 체크리스트 확인 후 코드 제공

STEP 5 - ATC 검증:
  run_atc_analysis 실행
  Error 등급 즉시 수정 후 재분석
  Warning 검토 후 처리

STEP 6 - 교차 검증:
  PLAN.md 계획 vs 구현 100% 비교
  abap-core-standards 스킬 자체 검증 체크리스트 전항목 확인

STEP 7 - 최종 보고:
  표준 보고 형식으로 결과 보고
  PLAN.md 최종 업데이트

---

## 예상 결과물

| 단계 | 결과물 |
|------|--------|
| STEP 1 | 요건 이해 확인 + 유사 프로그램 목록 |
| STEP 2 | PLAN.md 파일 (C:\ABAP_SKILL\{프로그램명}\) |
| STEP 3 | 계획 승인 요청 |
| STEP 4 | 완성된 ABAP 코드 (Include별) |
| STEP 5 | ATC 분석 결과 요약 |
| STEP 6 | 교차 검증 결과표 |
| STEP 7 | 최종 완료 보고 |