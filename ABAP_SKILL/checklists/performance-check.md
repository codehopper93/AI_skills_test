---
name: ebd-abap-performance-checklist
description: EBD 시스템 ABAP 성능 체크리스트 20항목 - DB접근(SELECT필드/WHERE조건/FOR-ALL-ENTRIES/SELECT-ENDSELECT/JOIN)/인터널테이블(SORT+BINARY-SEARCH/HASHED-TABLE/이중LOOP)/LOOP최적화/메모리 카테고리별 Pass/Fail 판정
---

# EBD 시스템 성능 체크리스트

> Critical 항목 Fail: 즉시 수정 필수
> High 항목 3개 이상 Fail: 성능 튜닝 후 재검증

---

## 성능 체크리스트 20항목

| # | 카테고리 | 검증 항목 | Pass 조건 | 영향도 |
|---|---------|---------|---------|--------|
| 1 | DB 접근 | SELECT 필드 명시 | SELECT matnr maktx ... (SELECT * 없음) | High |
| 2 | DB 접근 | WHERE 조건 필수 | 대형 테이블(MARA,BKPF) 조회 시 WHERE 있으면 Pass | High |
| 3 | DB 접근 | FOR ALL ENTRIES IS NOT INITIAL | FOR ALL ENTRIES 전 IS NOT INITIAL 체크 있으면 Pass | Critical |
| 4 | DB 접근 | SELECT ENDSELECT 없음 | Loop DB 접근 없으면 Pass | High |
| 5 | DB 접근 | OPEN CURSOR 대용량 처리 | 100만건 이상 조회 시 PACKAGE SIZE 사용하면 Pass | Medium |
| 6 | DB 접근 | JOIN 활용 | 연관 테이블을 개별 SELECT 루프 대신 JOIN 사용하면 Pass | Medium |
| 7 | DB 접근 | 인덱스 활용 | WHERE 조건이 Primary Key 또는 인덱스 필드 활용하면 Pass | Medium |
| 8 | DB 접근 | 버퍼 테이블 활용 | T001/T001W 등 버퍼 테이블 캐시 활용하면 Pass | Low |
| 9 | 인터널 테이블 | SORT+BINARY SEARCH | READ TABLE 시 BINARY SEARCH 사용하면 Pass | High |
| 10 | 인터널 테이블 | HASHED TABLE 단건조회 | 반복 단건 조회 시 HASHED TABLE 사용하면 Pass | Medium |
| 11 | 인터널 테이블 | 이중 LOOP 없음 | LOOP 안 LOOP (O(n)) 없으면 Pass | High |
| 12 | 인터널 테이블 | DELETE ADJACENT DUPLICATES | 중복 제거 후 처리하면 Pass | Low |
| 13 | LOOP 최적화 | WHERE 절 LOOP | LOOP AT lt WHERE (조건) 사용하면 Pass | Medium |
| 14 | LOOP 최적화 | FIELD-SYMBOLS 활용 | 대용량 LOOP 시 FIELD-SYMBOLS 사용하면 Pass | Medium |
| 15 | LOOP 최적화 | LOOP AT ASSIGNING | 수정 시 ASSIGNING 방식 사용하면 Pass | Medium |
| 16 | 메모리 | FREE 후 미사용 테이블 | 대용량 처리 완료 후 FREE 사용하면 Pass | Medium |
| 17 | 메모리 | OCCURS 0 지양 | 오래된 OCCURS 0 구문 없으면 Pass | Low |
| 18 | 문자열 | CONCATENATE 대신 Template String | 리터럴 조합에 VALUE 또는 Template String 사용하면 Pass | Low |
| 19 | 병렬처리 | BACKGROUND 처리 | 대용량 배치는 백그라운드 실행 가능하면 Pass | Medium |
| 20 | 전반 | ATC Performance Check | ATC 성능 관련 Warning 0건이면 Pass | High |

---

## 성능 점수 기준

```
Critical 항목 Fail  즉시 수정 필수
High 항목 3개 이상 Fail  성능 튜닝 후 재검증
전체 Pass  성능 검증 완료 
```

---

## 주요 성능 개선 패턴

### FOR ALL ENTRIES 안전 패턴
```abap
" 필수: IS NOT INITIAL 체크 먼저
IF lt_key[] IS NOT INITIAL.
  SELECT bukrs belnr gjahr waers
    FROM bkpf
    INTO TABLE @lt_bkpf
    FOR ALL ENTRIES IN @lt_key
    WHERE bukrs = @lt_key-bukrs
      AND belnr = @lt_key-belnr
      AND gjahr = @lt_key-gjahr.
ENDIF.
```

### BINARY SEARCH 패턴
```abap
" SORT 후 BINARY SEARCH
SORT lt_mara BY matnr.
READ TABLE lt_mara INTO ls_mara
  WITH KEY matnr = lv_matnr BINARY SEARCH.
IF sy-subrc = 0.
  " 처리
ENDIF.
```

### FIELD-SYMBOLS 패턴
```abap
FIELD-SYMBOLS: <ls_data> TYPE ty_s_data.
LOOP AT lt_data ASSIGNING <ls_data>.
  <ls_data>-processed = abap_true.
ENDLOOP.
```