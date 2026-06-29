---
name: ebd-abap-development
description: EBD 시스템 SAP ABAP 개발 종합 스킬 - 7단계 표준 개발 프로세스, 코딩 표준, 공통 유틸리티, ATC 검증을 포함한 완전한 ABAP 개발 가이드. 연결 ID: EBD
---

# EBD 시스템 ABAP 개발 종합 스킬

> 이 스킬은 EBD 시스템 전용 SAP ABAP 개발 통합 가이드입니다.
> 모든 개발 작업은 아래 7단계 프로세스와 코딩 표준을 반드시 준수합니다.

---

## 시스템 기본 정보

| 항목 | 값 |
|------|-----|
| 연결 ID | EBD |
| FI 공통 프로그램 | ZFIR00010 |
| FI 공통 클래스 | ZCL_FI_COMMON |
| FI 메시지 클래스 | ZFIM01 |
| 공통 메시지 클래스 | ZCAM01 |
| 에러 상수 | ZFI01_ERROR, ZFI01_E |
| 참조 프로그램 | ZFIR40090, ZFIR22030, ZFIR24000, ZFIMLS1010, ZFIR40160 |
| BW 패키지 | ZBW01 |

---

## AI 서브에이전트 설정

| 티어 | 에이전트 ID | 모델 |
|------|------------|------|
| Tier 1 | abap-discoverer, abap-reader, abap-creator, abap-visualizer, abap-documenter | Claude Haiku 4.5 |
| Tier 2 | abap-usage-analyzer, abap-quality-checker, abap-historian, abap-debugger, abap-troubleshooter, abap-data-analyst | GPT-4o |
| Tier 3 | abap-orchestrator, abap-code-reviewer | Claude Sonnet 4 |

> Opus / Max 계열 모델은 절대 사용 금지 (비용 과다)

---

## 개발 프로세스 7단계 요약

```
[1] 요건분석    search_abap_objects, get_abap_object_lines (시그니처 확인 필수)
[2] PLAN.md    C:\ABAP_SKILL\{PROGNAME}\PLAN.md 생성 (abap-dev-plan-template 스킬 참조)
[3] 사용자승인  PLAN.md 내용 보고 후 승인 요청 (승인 전 코드 착수 금지)
[4] 코드개발   PLAN.md 체크리스트 순서대로, 완료 시 [x] 업데이트
[5] ATC검증    run_atc_analysis 실행, Error 0건 확인 필수
[6] 교차검증   계획 vs 구현 100% 비교 (네이밍, 공통유틸, 권한, 에러처리)
[7] 최종보고   "[개발 완료] 프로그램:{명} ATC:에러0건 교차검증:100%" 형식
```

---

## 스킬 파일 목록

| 스킬 파일 | 용도 |
|----------|------|
| `abap-core-standards.md` | 핵심 코딩 표준, 네이밍 규칙, 금지 패턴 |
| `abap-workflow.md` | 7단계 개발 워크플로우 상세 |
| `abap-full-development.md` | 통합 개발 프롬프트 (7단계 전체) |
| `abap-new-report.md` | Report 신규 개발 프롬프트 |
| `abap-new-class.md` | Class 신규 개발 프롬프트 |
| `abap-code-review.md` | 코드 리뷰/개선 프롬프트 |
| `abap-refactoring.md` | 리팩토링 프롬프트 |
| `abap-security-check.md` | 보안 점검 프롬프트 |
| `abap-dev-plan-template.md` | 개발계획서(PLAN.md) 템플릿 |
| `checklists/final-review.md` | 배포 전 최종 검토 체크리스트 |
| `checklists/quality-check.md` | 품질 체크리스트 (30항목) |
| `checklists/security-check.md` | 보안 체크리스트 (15항목) |
| `checklists/performance-check.md` | 성능 체크리스트 (20항목) |

---

## 필수 공통 유틸리티 (직접 구현 절대 금지)

```abap
" 권한 체크 (START-OF-SELECTION 첫째 - 모든 프로그램 필수)
PERFORM check_auth IN PROGRAM zfir00010 IF FOUND.

" 회사코드 검증 (AT SELECTION-SCREEN ON p_bukrs)
PERFORM check_bukrs IN PROGRAM zfir00010 IF FOUND
                    USING p_bukrs CHANGING gs_bukrs_info.

" 회사코드 F4 / GSBER 검증 / WERKS 검증
CALL METHOD zcl_fi_common=>f4_bukrs( ).
CALL METHOD zcl_fi_common=>check_gsber EXPORTING it_gsber = s_gsber[].
CALL METHOD zcl_fi_common=>check_werks EXPORTING it_werks = s_werks[].

" 조회 결과 없음 / 건수 메시지 (표준)
MESSAGE s037(zcam01) DISPLAY LIKE gc_e.
MESSAGE s019(zcam01) WITH CONV i( CONV string( lines( gt_result ) ) ).
```

---

## 개발 금지 사항

- PLAN.md 없이 코드 개발 착수 금지
- 사용자 승인 없이 Phase 4 (코드 개발) 착수 금지
- ATC 검증 없이 개발 완료 선언 금지
- 공통 유틸리티 직접 재구현 금지 (ZFIR00010, ZCL_FI_COMMON 활용)
- 함수/메서드 시그니처 추측 사용 금지 (반드시 실제 시그니처 확인)
- `adt://` URI 방식으로 파일 검색 금지 (ADT 검색 기능 활용)

---

## 최종 보고 형식

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