# DECODE

오라클의 `DECODE` 함수는 조건에 따라 서로 다른 값을 반환하는 Oracle SQL 함수입니다. `CASE` 구문의 간단한 형태로 생각할 수 있습니다. 특정 입력값을 기준으로 원하는 출력값을 반환할 때 유용합니다.

## DECODE 함수의 기본 문법
```sql
DECODE(expression, search1, result1, [search2, result2, ..., default_result])
```

- **expression**: 비교하고자 하는 값 또는 열.
- **search1, search2, ...**: 비교 대상 값들.
- **result1, result2, ...**: 각 비교 대상과 일치할 경우 반환할 값.
- **default_result**: (선택 사항) 비교 대상과 일치하는 값이 없을 경우 반환할 기본 값.

## 작동 방식
1. `expression`이 `search1`과 같으면 `result1` 반환.
2. `expression`이 `search2`와 같으면 `result2` 반환.
3. 일치하는 값이 없으면 `default_result` 반환. `default_result`를 생략하면 `NULL` 반환.

---

## 예시

### 1. 기본 사용
직원의 `DEPTNO`(부서번호)에 따라 부서 이름을 반환하는 쿼리:
```sql
SELECT 
    EMPNO,
    ENAME,
    DEPTNO,
    DECODE(DEPTNO, 
           10, 'ACCOUNTING', 
           20, 'RESEARCH', 
           30, 'SALES', 
           'OTHER') AS DEPT_NAME
FROM EMP;
```
**설명**:  
- `DEPTNO`가 10이면 `'ACCOUNTING'` 반환.
- `DEPTNO`가 20이면 `'RESEARCH'` 반환.
- `DEPTNO`가 30이면 `'SALES'` 반환.
- 그 외에는 `'OTHER'` 반환.

---

### 2. NULL 값 처리
`DECODE`는 `NULL` 값을 특별히 처리합니다. 예를 들어, 특정 값이 `NULL`인 경우 별도의 처리를 추가할 수 있습니다.
```sql
SELECT 
    EMPNO,
    ENAME,
    SAL,
    DECODE(COMM, 
           NULL, 'NO COMMISSION', 
           0, 'NO COMMISSION',
           'HAS COMMISSION') AS COMM_STATUS
FROM EMP;
```
**설명**:
- `COMM`(커미션)이 `NULL`이면 `'NO COMMISSION'` 반환.
- `COMM`이 0이어도 `'NO COMMISSION'` 반환.
- 나머지 경우 `'HAS COMMISSION'` 반환.

---

### 3. 숫자 연산과 조합
판매 수량(`QUANTITY`)에 따라 할인율을 반환:
```sql
SELECT 
    PRODUCT_ID,
    QUANTITY,
    DECODE(
        SIGN(QUANTITY - 100), 
        1, '20%',    -- QUANTITY > 100
        0, '10%',    -- QUANTITY = 100
        -1, '5%'     -- QUANTITY < 100
    ) AS DISCOUNT
FROM ORDERS;
```
**설명**:
- `SIGN` 함수는 `QUANTITY - 100`의 부호를 반환.  
  - `1`이면 20% 할인.
  - `0`이면 10% 할인.
  - `-1`이면 5% 할인.

---

### 4. 조건으로 사용
`DECODE`를 조건문 안에서 사용하여 필터링:
```sql
SELECT *
FROM EMP
WHERE DECODE(DEPTNO, 10, 'ACCOUNTING', 20, 'RESEARCH', 'OTHER') = 'ACCOUNTING';
```
**설명**:
- 부서가 10(`ACCOUNTING`)인 행만 반환.

---

`DECODE` 함수는 간단한 조건 논리를 처리할 때 강력하지만, 복잡한 조건이 필요한 경우에는 더 유연한 `CASE` 문을 사용하는 것이 일반적입니다.