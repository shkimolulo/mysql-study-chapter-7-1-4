# 7.1.1 MySQL에서 지원하는 데이터 형식의 종류

## 숫자 데이터 형식

| 데이터 형식                          | 바이트 수 | 숫자 범위               | 설명 |
| --------------------------------- | ------- | --------------------- | --------------------------------------------------------------- |
| BIT(N)                            | N/8     |                       | 1~64bit를 표현, b'0000' 형식으로 표현 |
| TINYINT                           | 1       | -128~127              | 정수 |
| *SMALLINT                         | 2       | -32,768~32,767        | 정수 |
| MEDIUMINT                         | 3       | -8,388,608~8,388,607  | 정수 |
| *INT, INTEGER                     | 4       | 약 -21억~+21억          | 정수 |  
| *BIGINT                           | 8       | 약 -900경~+900경        | 정수 | 
| *FLOAT                            | 4       | -3.40E+38~-1.17E-38   | 소수점 아래 7자리까지 표현 |  
| *DOUBLE, REAL                     | 8       | -1.22E-308~1.79E+308  | 소수점 아래 15자리까지 표현 | 
| *DECIMAL(m, [d]), NUMBER(m, [d])  | 5~17    | -10^38+1~+10^38-1     | 전체자릿수(m)와 소수점 이하 자릿수(d)를 가진 숫자형  예) decimal(5,2)은 전체 자릿수를 5자리로 하되, 그 중 소수점 이하를 2자리로 하겠다는 의미 |
- DECIMAL: 정확한 수치 저장
- FLOAT, REAL: 근사치 숫자 저장
- 예) -999999.99 ~ +999999.99 -> DECIMAL(9,2)
- 부호없는 정수 UNSIGNED TINYINT(255), SMALLINT(65535), MEDIUMINT(16777215), INT(42억), BIGINT(약 1800경)
- FLOAT, DOUBLE, DECIMAL도 UNSIGNED 예약어 사용 가능하지만 자주 사용하지 않음 

## 문자 데이터 형식

| 데이터 형식  | 바이트 수  | 설명  |
|---|---|---|
| *CHAR(N)  | 1~255  | 고정길이 문자형, n을 1부터 255까지 지정, character 의 약자, 그냥 CHAR만 쓰면 CHAR(1)과 동일  |
| *VARCHAR(n)  | 1~65535  | 가변길이 문자형, n을 사용하면 1부터 65535까지 지정, Variable character 약자  |
| BINARY(n)  | 1~255  | 고정길이의 이진 데이터 값  |
| VARBINARY(n)  | 1~255  | 가변길이의 이진 데이터 값  |
| TINYTEXT  | 1~255  | 255 크기의 TEXT 데이터 값  |
| TEXT  | 1~65535  | N 크기의 TEXT 데이터 값  |
| MEDIUMTEXT  | 1~16777215  | 16777215 크기의 TEXT 데이터 값  |
| *LONGTEXT  | 1~4294967295  | 최대 4GB 크기의 TEXT 데이터 값  |
| TINYBLOB  | 1~255  | 255 크기의 BLOB 데이터 값  |
| BLOB  | 1~65535  | N 크기의 BLOB(Binary Large Object, 사진, 동영상, 문서 등) 데이터 값 |
| MEDIUMBLOB  | 1~16777215  | 16777215 크기의 BLOB 데이터 값  |
| *LONGBLOB  | 1~4294967295  | 최대 4GB 크기의 BLOB 데이터 값  |
| ENUM(값들)  | 1 또는 2  | 최대 65535개의 열거형 데이터 값(예) 요일) |
| SET(값들)  | 1,2,3,4,8  | 최대 64개의 서로 다른 데이터 값, 2개씩 세트로 저장  |

- CHAR
    - 고정 길이 문자형
    - INSERT/UPDATE 성능 좋음
    - CHAR(100) 으로 선언 후 3byte만 사용해도 100byte를 모두 확보 후 97byte 낭비
- VARCHAR
    - 가변 길이 문자형
    - 낭비하지 않고 저장할 길이만큼만 확보하여 공간 효율적 운영 
- 기본적으로 CHAR, VARCHAR UTF-8 형태로 영문, 한글 따라 내부적으로 크기 달라짐 
- 사용자 입장에서 영문, 한글 구분 없이 100글자 입력 가능 
- MySQL 5.7의 기본 문자 세트 my.ini 또는 my.cnf 파일에 설정
```
# 클라이언트 문자 세트 
[mysql]
default-character-set=utf8 
# 서버 문자 세트 
[mysqld] 
character-set-server=utf8
```

- ENUM
```
CREATE TABLE shirts (
    name VARCHAR(40),
    size ENUM('x-small', 'small', 'medium', 'large', 'x-large')
    -- 1, 2, 3, 4, 5 숫자값으로도 조회할 수 있음
    -- NULL은 NULL, 0은 ''의 값을 가짐
);
INSERT INTO shirts (name, size) VALUES ('dress shirt','large'), ('t-shirt','small'), ('polo shirt','small');
SELECT name, size FROM shirts WHERE size = 'medium';
SELECT * FROM shirts WHERE size=2;
SELECT size+1 FROM shirts;

```

### Quiz. (sun-1, mon-2, tues-3, wednes-4, thurs-5, fri-6, satur-7) 의 순서대로 요일을 갖는 test_enum 테이블 생성 후 인덱스를 이용해서 값을 삽입하기

- SET
```
CREATE TABLE myset (col SET('a', 'b', 'c', 'd'));
INSERT INTO myset (col) VALUES ('a,d'), ('d,a'), ('a,d,a'), ('a,d,d'), ('d,a,d');
SELECT col FROM myset;
INSERT INTO myset (col) VALUES ('a,d,d,s');
SHOW WARNINGS;
SELECT col FROM myset;
-- SET 타입에 없는 값을 삽입 시, error 발생 후 삽입되지 않음 
```

### 참고
- [The Enum Type](https://dev.mysql.com/doc/refman/5.7/en/enum.html)
- [The Set Type](https://dev.mysql.com/doc/refman/5.7/en/set.html) 

## 날짜와 시간 데이터 형식

| 데이터 형식  | 바이트 수  | 설명  |
|---|---|---|
| *DATE  | 3  | 1001-01-01~9999-12-31까지 저장, 날짜 형식만 사용, 'YYYY-MM-DD'  |
| TIME  | 3  | -838:59:59.000000~838.59:59.000000까지 저장, 'HH:MM:SS'  |
| *DATETIME  | 8  | 1001-01-01 00:00:00~9999-12-31 23:59:59까지 저장, 'YYYY-MM-DD HH:MM:SS'  |
| TIMESTAMP  | 4  | 1001-01-01 00:00:00~9999-12-31 23:59:59, 'YYYY-MM-DD HH:MM:SS', time_zone 시스템 변수와 관련, UTC 시간대로 변환하여 저장  |
| YEAR  | 1  | 1901~2155까지 저장, 'YYYY'  |

```
SELECT CAST('2020-10-19 12:35:29.123' AS DATE) AS 'DATE';   --'2020-10-19'
SELECT CAST('2020-10-19 12:35:29.123' AS TIME) AS 'TIME';   --'12:35:29'
SELECT CAST('2020-10-19 12:35:29.123' AS DATETIME) AS 'DATETIME';   --'2020-10-19 12:35:29' 
```

## 기타 데이터 형식

| 데이터 형식  | 바이트 수  | 설명  |
|:-:|---|:-:|
| GEOMETRY  | N/A  | 공간 데이터 형식으로 선, 점 및 다각형 같은 공간 데이터 개체를 저장하고 조작  |
| JSON  | 8  | JSON(JavaScript Object Notation) 문서 저장, MySQL 5.7.8 이후부터 지원  |

- GEOMETRY 
    - 타입, 각각 인스터스화 가능한 클래스의 하나로 속한다.
    - SRID(spatial reference identifer), 공간 구분하는 식별자 
    - 좌표, 최소 한 쌍의 (X, Y) 좌표를 포함 

- JSON
    - JSON 타입의 값 삽입
    ```
    CREATE TABLE test_json (jdoc JSON);
    INSERT INTO test_json VALUES('{"key1": "value1", "key2": "value2"}');
    INSERT INTO test_json VALUES('{"key3": "value1", "key4": "value2"}');
    INSERT INTO test_json VALUES('[1, 2,');
    INSERT INTO test_json VALUES ('[1,2,3,1,2,3,1,2,3]');
    -- JSON 형식에 맞지 않아도 입력 가능
    ```
    
    - JSON_ARRAY(), JSON_OBJECT(), JSON_MERGE()
    ```
    SELECT JSON_ARRAY('a', 1, NOW());   -- 원소의 값들을 포함하는 배열의 JSON 반환
    SELECT JSON_OBJECT('key1', 1, 'key2', 'abc');   -- key, value 값을 갖는 객체 JSON 반환
    SELECT JSON_MERGE('["a", 1]', '{"key": "value"}');  -- 두 JSON 데이터 연결
    ```

    - JSON_SEARCH()
    ```
    -- JSON 타입의 컬럼에서 one(한 개만), all(모두) 지정하고 찾을 값을 검색
    SELECT JSON_SEARCH(jdoc, 'one', 'key1') FROM test_json;
    SELECT JSON_SEARCH(jdoc, 'one', 'value1') FROM test_json;
    SELECT JSON_SEARCH(jdoc, 'all', '1') FROM test_json;
    ```
### 참고
- [Geometry Class](https://dev.mysql.com/doc/refman/5.7/en/gis-class-geometry.html)
- [The JSON Data Type](https://dev.mysql.com/doc/refman/5.7/en/json.html)
- [JSON Search](https://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html)

## LONGTEXT, LONGBLOB
- LOB(Large Object, 대량의 데이터) 저장하기 위해 지원 
- 약 4GB 파일 하나의 데이터로 저장 가능 -> LONGTEXT(장편 소설), LONGBLOB(동영상) 

# 7.1.2 변수의 사용 
- 스토어드 프로시저나 함수 안에서의 변수는 DECLARE문으로 선언 후 사용 
- @변수명 '전역 변수'처럼, DECLARE 변수명 '지역 변수' 처럼 사용 
```
SET @변수이름 = 변수의 값;  -- 변수의 선언 및 값 대입 
SELECT @변수이름;   -- 변수의 값 출력 
```

- 일반적인 SELECT ... FROM 문과도 같이 사용 
```
SET @myVar1 = 5;
SET @myVar2 = 3;
SET @myVar3 = 4.25;
SET @myVar4 = '가수 이름==> ';

SELECT @myVar1; -- 5
SELECT @myVar2 + @myVar3;   -- 7.25000000000000000 
SELECT @myVar4, Name From userTbl WHERE height > 180;
/*
@myVar4         name
가수 이름==>        임재범
가수 이름==>        이승기
가수 이름==>        성시경
*/
```

- LIMIT에 원칙적으로 변수 사용할 수 없으나 PREPARE, EXECUTE문 활용해서 변수 활용 
```
SET @myVar1 = 3;
PREPARE myQuery
    FROM 'SELECT Name, height FROM userTbl ORDER BY height LIMIT ?';    -- myQuery에 쿼리문을 준비만 해놓고 실행하지 않음
EXECUTE myQuery USING @myVar1;  -- USING @myVar1 으로 ?에 3이 대입되고 실행, LIMIT @변수 형식과 동일한 효과 
```

# 7.1.3 데이터 형식과 형 변환 

## 데이터 형식 변환 함수
- CAST(), CONVERT() 
```
CAST (expression AS 데이터형식[ (길이) ])
CONVERT (expression, 데이터형식[ (길이) ])
-- BINARY, CHAR, DATE, DATETIME, DECIMAL, JSON, SIGNED INTEGER, TIME, UNSIGNED INTEGER 
```

- sqlDB의 구매 테이블(buyTbl)에서 평균 구매 개수 구하기 
```
SELECT AVG(amount) AS '평균 구매 개수' FROM buyTbl; -- 2.9167

SELECT CAST(AVG(amount) AS SIGNED INTEGER) AS '평균 구매 개수' FROM buyTbl; -- 3
SELECT CONVERT(AVG(amount), SIGNED INTEGER) AS '평균 구매 개수' FROM buyTbl;    -- 3
```

- 다양한 구분자 날짜 형식으로도 변경 
```
SELECT CAST('2020$12$12' AS DATE);
SELECT CAST('2020/12/12' AS DATE);
SELECT CAST('2020%12%12' AS DATE);
SELECT CAST('2020@12@12' AS DATE);
-- 2020-12-12
```

- 쿼리의 결과를 보기 좋게 할 때 사용
```
SELECT num, 
    CONCAT(CAST(price AS CHAR(10)), 'X', 
    CAST(amount AS CHAR(4)), '=') AS '단가X수량', 
    price*amount AS '구매액' 
FROM buyTbl;

/*
num     단가x수량   구매액 
1       30X2=       60
2       1000X1=     1000
3       200X1=      200
*/
```

## 암시적인 형변환
- 명시적인 변환(Explicit Conversion): CAST(), CONVERT() 이용하여 형변환 
- 암시적인 변환(Implicit Conversion): CAST(), CONVERT() 이용하지 않고 형변환
```
SELECT '100' + '200';   -- 문자와 문자를 더함 (정수로 변환되서 연산됨), 다른 DBMS에서는 문자열로 처리하기도 함, 300
SELECT CONCAT('100','200'); -- 문자와 문자를 연결 (문자로 처리) // 100200
SELECT CONCAT(100, '200');  -- 정수와 문자를 연결 (정수가 문자로 변환되서 처리) // 100200
SELECT 1 > '2mega'; -- 정수인 2로 변환되어서 비교, 0
SELECT 3 > '2MEGA'; -- 정수인 2로 변환되어서 비교, 1
SELECT 0 = 'mega2'; -- 문자는 0으로 변환됨, 1
```

# 7.1.4 MySQL 내장함수

## 제어 흐름 함수
### IF(수식, 참, 거짓)
- 수식이 참 또는 거짓인지 결과에 따라서 2중분기
```
SELECT IF(100>200, '참이다', '거짓이다');
-- 거짓이다
```

### IFNULL(수식1, 수식2)
- 수식1이 NULL이 아니면 수식1 반환, 수식1이 NULL이면 수식2 반환 
```
SELECT IFNULL(NULL, '널이군요'), IFNULL(100, '널이군요');
-- 널이군요, 100
```

### NULLIF(수식1, 수식2)
- 수식1과 수식2가 같으면 NULL 반환, 다르면 수식1 반환 
```
SELECT NULLIF(100,100), NULLIF(200,100);
-- NULL, 200
```

### CASE ~ WHEN ~ ELSE ~ END
- CASE 내장 함수는 아니며 연산자로 분류, 다중 분기에 사용될 수 있음
```
SELECT CASE 10
    WHEN 1 THEN '일'
    WHEN 5 THEN '오'
    WHEN 10 THEN '십'
    ELSE '모름' 
END AS 'result';
-- 십
```

## 문자열 함수
### ASCII(아스키 코드), CHAR(숫자)
- 문자의 아스키 코드값이나 숫자의 아스키 코드값에 해당하는 문자 돌려줌
```
SELECT ASCII('A'), CHAR(65);
-- 65, 'A'
```

### BIT_LENGTH(문자열), CHAR_LENGTH(문자열), LENGTH(문자열)
- BIT_LENGTH(문자열): 할당된 Bit 크기 또는 문자 크기 반환
- CHAR_LENGTH(문자열): 문자의 개수 반환
- LENGTH(문자열): 할당된 Byte 수 
- MySQL 5.7은 기본적으로 UTF-8 코드를 사용하기 때문에 영문은 3Byte, 한글은 3 * 3 = 9Byte 할당
```
SELECT BIT_LENGTH('abc'), CHAR_LENGTH('abc'), LENGTH('abc');
SELECT BIT_LENGTH('가나다'), CHAR_LENGTH('가나다'), LENGTH('가나다');
```

### CONCAT(문자열1, 문자열2, ...), CONCAT_WS(문자열1, 문자열2)
- CONCAT(문자열1, 문자열2, ...): 문자열 이어줌
- CONCAT_WS(문자열1, 문자열2): 구분자와 함께 문자열 이어줌
```
SELECT CONCAT_WS('/', '2020', '01', '01');
-- 2020/01/01
```

### ELT(위치, 문자열1, 문자열2, ...), FIELD(찾을 문자열, 문자열1, 문자열2, ...), FIND_IN_SET(찾을 문자열, 문자열 리스트), INSTR(기준 문자열, 부분 문자열), LOCATE(부분 문자열, 기준 문자열)
- ELT(위치, 문자열1, 문자열2, ...): 위치 번째 해당 문자열 반환
- FIELD(찾을 문자열, 문자열1, 문자열2, ...): 찾을 문자열의 위치를 찾아서 반환
- FIND_IN_SET(찾을 문자열, 문자열 리스트): 찾을 문자열을 문자열 리스트에서 찾아서 위치 반환, 문자열 리스트는 콤마(,)로 구분하고 공백이 없어야 함
- INSTR(기준 문자열, 부분 문자열): 기준 문자열에서 부분 문자열 찾아서 그 시작 위치 반환
- LOCATE(부분 문자열, 기준 문자열): INSTR과 동일하지만 파라미터 순서가 반대로 되어 있음, POSITION 과 같음
```
SELECT 
    ELT(2, '하나', '둘', '셋') AS 'ELT', 
    FIELD('둘', '하나', '둘', '셋') AS 'FIELD', 
    FIND_IN_SET('둘', '하나,둘,셋') AS 'FIND_IN_SET',
    INSTR('하나둘셋', '둘') AS 'INSTR', 
    LOCATE('둘', '하나둘셋') AS 'LOCATE'
-- '둘', 2, 2, 3, 3
```

### FORMAT(숫자, 소수점 자릿수)
- 숫자를 소수점 아래 자릿수까지 표현
- 또한 1000 단위마다 콤마(,) 표시
```
SELECT FORMAT(123456.123456, 4);
-- 123,456.1235
```

### BIN(숫자), HEX(숫자), OCT(숫자)
- 2진수, 16진수, 8진수의 값 반환
```
SELECT BIN(31), HEX(31), OCT(31);
-- 11111, 1F, 37
```

### INSERT(기준 문자열, 위치, 길이, 삽입할 문자열)
- 기준 문자열의 위치부터 길이만큼을 지우고 삽입할 문자열을 끼워 넣는다.
```
SELECT INSERT('abcdefghi', 3, 4, '@@@@'), INSERT('abcdefghi', 3, 2, '@@@@');
-- 'ab@@@@ghi', 'ab@@@@efghi'
```

### LEFT(문자열, 길이), RIGHT(문자열, 길이)
- 왼쪽 또는 오른쪽에서 문자열의 길이만큼 반환
```
SELECT LEFT('abcdefghi', 3), RIGHT('abcdefghi', 3);
-- 'abc', 'ghi'
```

### UCASE(문자열), LCASE(문자열)
- 소문자를 대문자로, 소문자를 대문자로 변경
```
SELECT LCASE('abcdEFGH'), UCASE('abcdEFGH');
-- abcdefgh, ABCDEFGH
```

### UPPER(문자열), LOWER(문자열)
- 소문자를 대문자로, 소문자를 대문자로 변경
```
SELECT UPPER('abcdEFGH'), LOWER('abcdEFGH');
-- abcdefgh, ABCDEFGH
```

### LPAD(문자열, 길이, 채울 문자열), RPAD(문자열, 길이, 채울 문자열)
- 문자열을 길이만큼 늘린 후에 빈 곳을 채울 문자열로 채움
```
SELECT LPAD('이것이', 5, '##'), RPAD('이것이', 5, '##');
-- '##이것이', '이것이##'
```

### LTRIM(문자열), RTRIM(문자열)
- 문자열의 왼쪽/오른쪽 공백 제거, 중간의 공백은 제거되지 않음
```
SELECT LTRIM('   이것이'), RTRIM('이것이   ');
-- 공백 제거된 '이것이' 반환
```

### TRIM(문자열), TRIM(방향 자를_문자열 FROM 문자열)
- 문자열의 앞뒤 공백을 모두 없앰
- 방향 LEADING(앞), BOTH(양쪽), TRAILING(뒤)
```
SELECT TRIM('   이것이   '), TRIM(BOTH 'ㅋ' FROM 'ㅋㅋㅋ재밌어요.ㅋㅋㅋ');
-- '이것이', '재밌어요.'
```

### REPEAT(문자열, 횟수)
- 문자열을 횟수만큼 반복
```
SELECT REPEAT('이것이', 3);
-- '이것이이것이이것이'
```

### REPLACE(문자열, 원래 문자열, 바꿀 문자열)
- 문자열을 원래 문자열을 찾아서 바꿀 문자열로 바꿈
```
SELECT REPLACE ('이것이 MySQL이다', '이것이' , 'This is');
-- 'This is MySQL이다'
```

### REVERSE(문자열)
- 문자열의 순서를 거꾸로
```
SELECT REVERSE ('MySQL');
-- 'LQSym'
```

### SPACE(길이)
- 길이만큼의 공백 반환
```
SELECT CONCAT('이것이', SPACE(10), 'MySQL이다');
-- '이것이          MySQL이다'
```

### SUBSTRING(문자열, 시작위치, 길이) 또는 SUBSTRING(문자열 FROM 시작위치 FOR 길이)
- 시작위치부터 길이만큼 문자 반환
- 길이가 생략되면 문자열의 끝까지 반환
```
SELECT SUBSTRING('대한민국만세', 3, 2);
-- '민국'
```

### SUBSTRING_INDEX(문자열, 구분자, 횟수)
- 문자열에서 구분자가 왼쪽부터 횟수 번째(음수면 반대 방향)까지 나오면 그 이후의 오른쪽은 버림
```
SELECT SUBSTRING_INDEX('cafe.naver.com', '.', 2),  SUBSTRING_INDEX('cafe.naver.com', '.', -2);
-- 'cafe.naver', 'naver.com'
```

## 수학 함수
### ABS(숫자)
- 숫자의 절댓값 반환
```
SELECT ABS(-100);
-- 100
```

### ACOS(숫자), ASIN(숫자), ATAN(숫자), ATAN2(숫자1, 숫자2), SIN(숫자), COS(숫자), TAN(숫자)
- 삼각함수와 관련된 함수

### CEILING(숫자), FLOOR(숫자), ROUND(숫자)
- 올림, 내림, 반올림
```
SELECT CEILING(4.7), FLOOR(4.7), ROUND(4.7);
-- 5, 4, 5
```

### CONV(숫자, 원래 진수, 변환할 진수)
- 숫자를 원래 진수에서 변환할 진수로 계산
```
SELECT CONV('AA', 16, 2), CONV(100, 10, 8);
-- 10101010, 144
```

### DEGREES(숫자), RADIANS(숫자), PI()
- 라디안 값을 각도값으로, 각도값을 라디안 값으로 반환, PI()는 파이값인 3.141592
- [라디안](https://ko.wikipedia.org/wiki/%EB%9D%BC%EB%94%94%EC%95%88#/media/File:Circle_radians.gif)
```
SELECT DEGREES(PI()), RADIANS(180);
-- 파이의 각도값 180, 180의 라디안 값
```

### EXP(X), LN(숫자), LOG(숫자), LOG(밑수, 숫자), LOG2(숫자), LOG10(숫자)
- 지수, 로그와 관련된 함수 제공

### MOD(숫자1, 숫자2) 또는 숫자1 % 숫자2 또는 숫자1 MOD 숫자2
- 숫자1을 숫자2로 나눈 나머지 
```
SELECT MOD(157, 10), 157 % 10, 157 MOD 10;
-- 157/10 = 15 ... 7
```

### POW(숫자1, 숫자2), SQRT(숫자)
- POW(), POWER() 동일한 함수
```
SELECT POW(2,3), SQRT(9);
-- 2의 3제곱, 루트 9
```

### RAND()
- 0 이상 1 미만의 실수
- m <= 임의의 정수 < n 구하고 싶다면, FLOOR(m + RAND() * (n-m))
```
SELECT RAND(), FLOOR(1 + (RAND() * (6-1)) );
-- 0~1 미만의 실수, 1~6 숫자
```

### SIGN(숫자)
- 숫자가 양수, 0, 음수인지 구함
- 1, 0, -1
```
SELECT SIGN(100), SIGN(0), SIGN(-100.123);
-- 1, 0, -1
```

### TRUNCATE(숫자, 정수)
- 숫자를 소수점 기준으로 정수 위치까지 구하고 나머지 버림
```
SELECT TRUNCATE(12345.12345, 2), TRUNCATE(12345.12345, -2);
-- 12345.12, 12300
```

## AS
### JSON_SEARCH()
- JSON_SEARCH(json_doc, one_or_all, search_str[, escape_char[, path] ...])
- json_doc 안에서 주어진 문자열의 위치를 반환
- NULL 반환
    1) json_doc, search_str, path 중 NULL이 있을 경우
    2) json_doc에 path 가 없을 경우
    3) search_str을 찾을 수 없는 경우
- ERROR 반환
    1) 어떤 path 도 유효하지 않은 경로 표현식인 경우
    2) one_or_all 위치에 'one', 'all' 이외의 것이 올 경우
    3) escape_char 변하지 않는 표현식인 경우
- one_or_all
    - one: 첫번째 매칭 이후의 검색을 실행하고 첫번째 경로 문자열 반환, 첫번째로 찾는 기준은 정해져 있지 않음
    - all: 모두 매칭되는 경로를 반환, 복수의 문자열이 있다면 배열로 덮어씌워짐. 배열의 순서는 정해져 있지 않음
- 참고
    - [MySQL 5.7 reference manual - JSON_SEARCH](https://dev.mysql.com/doc/refman/5.7/en/json-search-functions.html#function_json-search)
    - [JSON_SEARCH 예제](https://database.guide/json_search-find-the-path-to-a-string-in-a-json-document-in-mysql/)

### JSON path expression
- $: root 객체 또는 요소로 모든 경로 표현은 이것으로 시작함
- @: 필터링이 실행된 현재 위치
- *: 이름이나 숫자가 필요한 어디든지 사용할 수 있는 와일드카드
- .<name>: 점으로 자식에 접근
- ['<name>' (, '<name>')]: 대괄호로 자식에 접근
- [<number> (, <number>)]: 배열의 인덱스
  