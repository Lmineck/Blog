# DECODE

오라클의 `TRUNC` 함수는 숫자나 날짜 데이터를 잘라내는(버림) 기능을 제공합니다.  
- **숫자**: 지정한 소수점 자리까지 값을 남기고 그 아래를 버림합니다.
- **날짜**: 지정한 날짜 단위를 기준으로 시간을 잘라냅니다.

---

## TRUNC 함수의 기본 문법

### 숫자용 TRUNC
```sql
TRUNC(number, [decimal_places])
```
- **number**: 잘라낼 숫자.
- **decimal_places**: (선택 사항) 소수점 이하 몇 번째 자리까지 남길지 지정. 기본값은 0으로 정수부만 남김.

### 날짜용 TRUNC
```sql
TRUNC(date, [date_format])
```
- **date**: 잘라낼 날짜.
- **date_format**: (선택 사항) 날짜를 자를 기준. 기본값은 'DD'(일 단위)로 날짜만 남김.

---

## TRUNC 함수의 작동 방식

### 1. 숫자에서의 TRUNC
- 지정한 소수점 이하를 버림.
- 지정하지 않으면 기본적으로 정수 부분만 남음.

### 2. 날짜에서의 TRUNC
- 날짜를 지정한 단위까지 남기고, 그 하위 단위를 잘라냄.
- `date_format`에 따라 연도, 월, 일 등의 단위로 자를 수 있음.

---

## TRUNC 함수의 예시

### 숫자 데이터에서 TRUNC

1. 기본 사용: 소수점 자르기
```sql
SELECT TRUNC(123.456) AS TRUNCATED_NUMBER FROM DUAL;
```
**결과**:  
```
TRUNCATED_NUMBER
----------------
123
```

2. 소수점 두 번째 자리까지 남기기
```sql
SELECT TRUNC(123.456, 2) AS TRUNCATED_NUMBER FROM DUAL;
```
**결과**:  
```
TRUNCATED_NUMBER
----------------
123.45
```

3. 음수를 사용하여 정수부 자르기
```sql
SELECT TRUNC(1234.56, -2) AS TRUNCATED_NUMBER FROM DUAL;
```
**결과**:  
- `-2`는 정수부에서 두 자리 잘라냄.
```
TRUNCATED_NUMBER
----------------
1200
```

---

### 날짜 데이터에서 TRUNC
1. 기본 사용: 날짜의 시간 제거
```sql
SELECT TRUNC(SYSDATE) AS TRUNCATED_DATE FROM DUAL;
```
**결과**:  
- 현재 날짜의 시간 부분이 제거되고 날짜만 남음.
```
TRUNCATED_DATE
---------------
2024-11-25 00:00:00
```

2. 날짜를 월 기준으로 자르기
```sql
SELECT TRUNC(SYSDATE, 'MM') AS TRUNCATED_DATE FROM DUAL;
```
**결과**:  
- 해당 월의 1일로 잘림.
```
TRUNCATED_DATE
---------------
2024-11-01 00:00:00
```

3. 날짜를 연도 기준으로 자르기
```sql
SELECT TRUNC(SYSDATE, 'YYYY') AS TRUNCATED_DATE FROM DUAL;
```
**결과**:  
- 해당 연도의 1월 1일로 잘림.
```
TRUNCATED_DATE
---------------
2024-01-01 00:00:00
```

4. 특정 단위로 잘라내기
```sql
SELECT TRUNC(SYSDATE, 'DAY') AS START_OF_WEEK FROM DUAL;
```
**결과**:  
- 주의 시작일(기본적으로 일요일)로 잘림.
```
START_OF_WEEK
--------------
2024-11-24 00:00:00
```

---

## TRUNC 함수 요약
- **숫자**: 소수점 이하 또는 정수부를 잘라내어 간단한 값을 생성.
- **날짜**: 날짜 데이터를 특정 단위로 잘라내어 원하는 수준의 정보를 표현.

`TRUNC` 함수는 데이터의 간결화, 그룹화, 날짜 연산 등 다양한 작업에서 유용하게 활용됩니다.