# 표준 SQL(Standard SQL)과 MySQL의 차이
- - - 
###### ❗ 해당 글은 제가 공부했던 기억(도서 및 포스트 등)과 개인적 의견이 섞여 작성되었기 때문에 틀리거나 제대로 설명하지 못한 부분이 있을 수 있습니다. 의견 남겨주시면 감사히 여기며 수정하도록 하겠습니다.❗

◀️ [이전글. 인덱스가 무엇일까?](https://github.com/kkhs00224/mystudy/blob/main/mysql/1_index.md)   
▶️

## 1. SQL표준과 MySQL
MySQL도 SQL이라는 이름을 쓰는 것처럼 ANSI/ISO SQL 표준에 따라 기능을 확장하여 만들어졌다. 이중 누락된 기능, 일부 차이에 대해 알아보며 MySQL의 특징을 간단히 알아보자.

### 1.1 표준 SQL
SQL은 Structured Query Language의 약자이며 풀이하면 구조화된 질의 언어라는 의미이다. 단순히 질의만 수행하는 것이 아니라 데이터베이스의 모든 작업을 통제하는 비절차적 언어로, 사용자가 원하는 작업의 결과만 얻고 그 작업이 어떻게 수행될 것인지 고려하지 않아도 된다.   

   
SQL은 데이터베이스 작업을 기술하기 위한 SQL문을 정의하고 있는데, 우리가 해당 구문을 통해 기술할 수 있는 작업에는 다음과 같은 것이 있다.

- 스키마 객체(Schema Object)의 생성, 변경, 제거
- 데이터베이스 질의
- 데이터의 삽입, 갱신, 삭제
- 트랜잭션 관리 및 세션 관리 등을 포함하는 데이터베이스 관리

#### 1.1.1 SQL 문장의 종류
- DDL(Data Definotion Language, 데이터 정의어)
- DML(Data Manipulation Language, 데이터 조작어)
- DCL(Data Control Language, 데이터 제어어)

### 1.2 MySQL에서 확장된 기능
MySQL에는 다른 SQL 데이터베이스에서는 볼 수 없는 확장 기능이 지원되고 있다.   

먼저 MySQL의 확장 기능들에 대해 간단히 알아보자.

- 디스크의 데이터 구성(Organization of data on disk)   
  각 데이터베이스를 MySQL 데이터 디렉터리 하위의 디렉터리로 매핑하고, 데이터베이스 내의 테이블을 데이터베이스 디렉터리의 파일 이름으로 매핑한다. 그렇기에 데이터베이스와 테이블의 이름은 대소문자를 구분한다.

- 일반 언어 구문(General language syntax)
  - 문자열을 ```"```와 ```'``` 모두를 사용해 묶을 수 있다. 만약 ```ANSL_QUOTES SQL```모드가 활성화되면 ```'```로만 묶을 수 있다.
  - 문자열에서 ```\```은 이스케이프 문자이다.
  - db_name.tbl_name 구문을 사용해 다른 데이터베이스의 테이블에 액세스할 수 있다. 일부 SQL 서버(예. 오라클)에서 이런 기능을 제공하며 User space라고 하는데, MySQL 서버에서는 ```CREATE TABLE ralph.my_table ... IN my_tablespace```와 같은 테이블 스페이스를 지원하지 않는다.

- SQL문 구문(SQL statement syntax)
  - ```ANALYZE TABLE```, ```CHECK TABLE```, ```OPTIMIZE TABLE```, ```REPAIR TABLE```
  - ```CREATE DATABASE```, ```DROP DATABASE```, ```ALTER DATABASE```
  - ```DO``` do 구문을 ```SELECT``` 대신 사용하면 결과 집합을 반환하지 않고 구문을 수행하고, 이벤트를 생성할 때 수행할 행동을 지정하기도 합니다.
  - ```EXPLAIN SELECT``` 쿼리 옵티마이저가 테이블을 처리하는 방법에 대한 설명을 얻을 수 있다.
    ```mysql
    mysql> EXPLAIN SELECT * FROM user;
    +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
    | id | select_type | table | partitions | type | possible_keys | key  | key_len | ref  | rows | filtered | Extra |
    +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
    |  1 | SIMPLE      | user  | NULL       | ALL  | NULL          | NULL | NULL    | NULL |    1 |   100.00 | NULL  |
    +----+-------------+-------+------------+------+---------------+------+---------+------+------+----------+-------+
    ```
  - ```FLUSH```와 ```RESET```
  - ```SET```
  - ```SHOW```
  - ```LOAD DATA```. 오라클의 ```LOAD DATA```와 호환되는 경우가 많다.
  - ```RENAME TABLE```
  - ```REPLACE```. 두 개의 구문, ```DELETE```와 ```INSERT```를 대신해 데이터를 치환하기 위해 쓰인다.
  - ```ALTER TABLE```에서 ```CHANGE col_name```, ```DROP col_name``` 또는 ```DROP INDEX```, ```IGNORE```, ```RENAME``` 등 ADD, ALTER, DROP, CHANGE절의 사용
  - 인덱스 이름, 컬럼 접두사(prefix)에 대한 인덱스, CREATE TABLE문에서 ```INDEX``` 사용
  - CREATE TABLE과 함께 ```TEMPORARY```, ```IF NOT EXISTS``` 사용
  - DROP TABLE, DROP DATABASE와 함께 ```IF EXISTS``` 사용
  - 하나의 DROP TABLE 구문으로 여러 테이블 삭제 가능
  - UPDATE와 DELETE 구문의 ```ORDER BY```와 ```LIMIT```절
  - ```INSERT INTO tbl_name SET col_name = ...``` 구문
  - INSERT와 REPLACE 문에서의 ```DELAYED```절
  - INSERT, REPLACE, DELETE, UPDATE 구문의 ```LOW_PRIORITY```
  - SELECT 구문에서의 ```INTO OUTFILE```, ```INTO DUMPFILE``` 사용
  - SELECT 구문의 ```STRAIGHT_JOIN```이나 ```SQL_SMALL_RESULT```와 같은 옵션
  - ```GROUP BY```절에서 모든 열의 이름을 지정할 필요가 없다.
  - ```GROUP BY```와 함께 ```ASC```로 오름차순, ```DESC```로 내림차순을 지정할 수 있다.
  - ```:=``` 할당 연산자를 사용해 구문에서 변수를 설정할 수 있다.

- 데이터 타입(Data types)
  - ```MEDIUMINT```, ```SET```, ```ENUM```, ```BLOB```, ```TEXT``` 등 다양한 데이터 유형
  - ```AUTO_INCREMENT```, ```BINARY```, ```NULL```, ```UNSIGNED```, ```ZEROFILL``` 데이터 유형 속성

- 기능과 연산자(Functions and operators)
  - 다른 SQL 환경에서의 마이그레이션을 위한 함수의 별칭을 지원한다. 예를 들어, 모든 문자열 함수는 표준 SQL 구문과 ODBC 구문을 지원한다.
  - 논리연산자 ```OR```, ```||``` 그리고 ```AND```, ```&&```를 모두 지원한다. 이 이유로 표준 SQL의 ```||``` 연산자는 지원하지 않고, ```CONCAT()```으로 대신 사용해 인수의 수를 자유롭게 넣을 수 있다.
  - ```COUNT(DISTINCT value_list)```에서 value_list가 두 개 이상의 요소가 있을 때 DISTINCT를 사용할 수 있다.
  - 문자열 비교는 대소문자 구분을 하지 않는다. 대소문자를 구분하여 비교하려면 열을 BINARY 속성으로 선언하거나 캐스팅을 해야 한다.
  - ```%``` dustkswksms ```MOD()```와 같다. ```%```은 C언어와 PostgreSQL과 호환성을 위해 지원된다.
  - =, <>, <=, <, >=, >, <<, >>, <=>, AND, OR 또는 LIKE 연산자는 SELECT 문에서 FROM 왼쪽의 출력 열 목록에서 사용할 수 있다.
  - ```LAST_INSERT_ID()``` 함수는 가장 최근의 ```AUTO_INCREMENT```값을 반환한다.
  - 숫자값에 ```LIKE```를 사용할 수 있다.
  - 정규식dmf 확장한 ```REGEXP```, ```NOT REGEXP``` 연산자
  - ```CONCAT()```, ```CHAR()```는 하나 이상, 두 개 이상의 인수를 사용할 수 있다.
  - ```BIT_COUNT()```, ```CASE```, ```ELT()```, ```FROM_DAYS()```, ```FORMAT()```, ```IF()```, ```MD5()```, ```PERIOD_ADD()```, ```PERIOD_DIFF()```, ```TO_DAYS()``` 및 ```WEEKDAY()``` 함수.
  - ```TRIM()```을 사용해 하위 문자열을 잘라낼 수 있다. 표준 SQL운 단일 문자만 제거할 수 있다.
  - ```STD()```, ```BIT_OR()```, ```BIT_AND()```, ```BIT_XOR()``` 및 ```GROUP_CONCAT()``` 함수를 사용하는 GROUP BY 함수
 
## 2. 표준 SQL과 MySQL의 차이점
   
### 2.1 UPDATE 차이점
표현식을 통해 테이블의 열에 접근(access)할 경우에 UPDATE는 열의 현재 값을 사용한다. 
```mysql
UPDATE t1 SET col1 = col1 + 1, col2 = col1;
```
위의 구문에서 col2를 col1값이 아닌, col1+1로 업데이트된 col1값으로 설정한다. 그렇기에 col1과 col2는 동일한 값을 가지게 되는데, 이런 동작은 표준 SQL과 다른다.

### 2.2 FOREIGN KEY 제약의 차이점 (FOREIGN KEY Constraint Differences)


- - -
#### 📖 참고문서
- [MySQL 8.0 참조 설명서](https://dev.mysql.com/doc/refman/8.0/en/)
- [SQL 소개](https://technet.tmaxsoft.com/upload/download/online/tibero/pver-20160406-000002/sql-reference/ch_SQL_concept.html)
