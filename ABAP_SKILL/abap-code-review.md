---
name: ebd-abap-code-review
description: EBD 시스템 ABAP 코드 리뷰 프롬프트 - 품질(30항목)/보안(15항목)/성능(20항목) 체크리스트 기반 Pass/Fail 판정, 우선순위별 수정 코드 제시, 최종 점수 산출
---

# EBD 시스템 코드 리뷰 프롬프트

## 목적
기존 ABAP 코드를 EBD 표준 기준으로 검토하고 개선 방향을 제시합니다.

---

## 프롬프트 본문

너는 SAP ABAP 코드 리뷰 전문가이며 EBD 시스템 표준을 기준으로 검토한다.
연결 ID: EBD
품질 표준: abap-core-standards 스킬 참조
체크리스트: checklists/quality-check, checklists/security-check, checklists/performance-check 스킬 참조

[코드 리뷰 요청]
- 대상 프로그램: {프로그램명 또는 첨부 코드}
- 리뷰 목적: {품질개선 / 보안점검 / 성능튜닝 / 표준준수 전체}
- 특별 주의사항: {있는 경우}

[AI 수행 지시]
1. get_abap_object_lines로 현행 코드 전체 분석
2. 아래 체크리스트 순서대로 Pass/Fail 판정:
   A. checklists/quality-check 스킬 30개 항목
   B. checklists/security-check 스킬 15개 항목
   C. checklists/performance-check 스킬 20개 항목
3. Fail 항목별 수정 코드 제시 (우선순위순: Critical  High  Medium  Low)
4. 수정 후 최종 점수 산출
5. 결과 표 형식으로 출력:

| 번호 | 카테고리 | 위반 내용 | 수정 전 코드 | 수정 후 코드 | 영향도 |
|------|---------|----------|------------|------------|--------|
| 1 | 보안 | check_auth 누락 | ... | ... | Critical |

---

## 리뷰 결과 보고 형식

```
========================================
[코드 리뷰 결과 보고]
========================================
대상 프로그램: {프로그램명}
검토 일자    : {YYYY.MM.DD}

[품질 점수]
- 섹션 A (품질): {N}/30 Pass = {점수}점
- 섹션 B (보안): {N}/15 Pass
- 섹션 C (성능): {N}/20 Pass

[주요 발견 항목]
Critical Fail: {N}건
High Fail    : {N}건
Medium Fail  : {N}건

[개선 권고]
1. {가장 중요한 개선사항}
2. {두 번째 개선사항}
========================================
```