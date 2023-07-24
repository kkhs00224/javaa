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
외래키의 제약 조건의 구현이 표준 SQL에서 다음과 같은 차이가 있다.   

- InnoDB 엔진에서 동일한 외래키 값을 가진 상위 테이블에 여러 행이 있는 경우, 동일한 키값을 가진 다른 상위 행이 존재하지 않는 것처럼 참조키 검사를 수행한다. 예를 들면 외래키의 제약 조건을 ```RESTRICT``` 유형으로 정의하고 부모 행이 여러 개인 자식 행이 있는 경우, 부모 행 삭제를 허용하지 않는다.
  ```mysql
  CREATE TABLE product(
     category INT NOT NULL,
     id INT NOT NULL,
     price DECIMAL,
  
     PRIMARY KEY(category, id)
  ) ENGINE=INNODB;
  CREATE TABLE product_order (
    no INT NOT NULL AUTO_INCREMENT,
    product_category INT NOT NULL,
    product_id INT NOT NULL,
    customer_id INT NOT NULL,

    PRIMARY KEY(no),
    FOREIGN KEY (product_category, product_id)
      REFERENCES product(category, id)
      ON UPDATE CASCADE ON DELETE RESTRICT
   )   ENGINE=INNODB;
  INSERT INTO product (category, id, price) VALUES(001, 110011, 3000);
  INSERT INTO product_order (product_category, product_id,customer_id) VALUES(001, 110011, 1);
  DELETE FROM product WHERE category=001;
  ```
  위의 예시를 보면 알 수 있듯이 외래키를 ```DELETE RESTRICT```로 설정해두면 부모행은 제약 조건에 의해 삭제가 되지 않는다.
  
- ```ON UPDATE CASCADE```나 ```ON UPDATE SET NULL``이 동일한 캐스케이드에서 이전에 업데이트한 테이블을 업데이트하면 ```RESTRICT```처럼 작동한다. 이는 자가 참조가 불가능하다는 것으로, 무한 루프를 방지하기 위한 것이다. ```ON DELETE SET NULL```과 ```ON DELETE CASCADE```는 자가참조가 가능하지만, 캐스케이드 작업은 15번 이상 중첩될 수 없다.
- SQL 표준에 따르면 기본 동작은 지연 확인(deferred checking)이어야 한다. 이는 제약 조건은 전체 SQL문이 처리된 후에만 확인된다는 것으로, 외래키를 사용하여 자신을 참조하는 행을 삭제할 수 것을 의미한다. 하지만 MySQL는 외래키 제약 조건을 즉시 확인한다. 여러 행을 삽입, 삭제, 업데이트하는 경우 고유 제약 조건과 외래키 제약 조건을 행별로 확인한다. InnoDB는 외래키 확인을 수행할 때 검사해야 하는 자식이나 부모 레코드에 공유된 행 수준의 잠금을 설정한다.
- InnoDB를 비롯한 모든 스토리지 엔진은 참조 무결성 제역 조건의 정의에 사용되는 ```MATCH```절을 인식하거나 강제하지 않는다. ```MATCH```절 사용은 지정된 효과를 가지지 않으며, ```ON DELETE```나 ```ON UPDATE```절이 무시되도록 한다. MATCH 옵션은 MySQL에서 다른 효과를 가지지 않으며, 사실상 ```MATCH SIMPLE```의 시멘틱을 항상 적용하여 외래키가 모두 또는 일부가 NULL일 수 있다. 이 경우 자식 테이블의 외래키를 포함하는 행은 참조된 부모 테이블의 어떤 행과도 일치하지 않더라도 삽입할 수 있다.   
  표준 SQL의 경우엔 ```MATCH```절은 참조된 테이블의 기본키와 비교할 때 복합 외래키의 NULL값을 처리하는 방법을 제어한다. ```MATCH FULL```은 모든 외래키 열이 NULL이면 참조된 테이블에서 일치하는 행이 필요하지 않고, ```MATCH SIMPLE```은 외래키 중 하나가 NULL이면 참조된 테이블에서 일치하는 행이 필요하지 않으며, ```MATCH PARTIAL```은 아직 구현되지 않았다고 한다.
- MySQL은 성능상의 이유로 참조된 열이 인덱싱되어 있어야 하지만, 참조된 열이 ```UNIQUE``` 또는 ```NOT NULL```로 강제되지 않고, NULL이고 인덱스가 없는 경우에도 참조할 수 있다.
  외래키가 NULL값을 포함하는 비고유 키, 또는 NULL값을 포함하는 키에 대한 처리는 UPDATE와 DELETE CASCADE 같은 작업에 명확하게 정의되어 있지 않다. UNIQUE(기본키도 포함)와 NOT NULL키만 참조하는 외래키를 사용하는 것이 좋다.
- MyISAM 스토리지 엔진일 경우, MySQL 서버는 외래키 사양을 구문 분석(parses)하고 무시한다.
- MySQL은 표준 SQL에 정의된 "inline REFERENCES specifications"를 구문 분석(parses)하지만 무시한다. FOREIGN KEY로 지정된 경우에만 ```REFERENCES```절을 허용한다.
- FOREIGN KEY가 아닌 경우에 ```REFERENCES tbl_name(col_name)절을 사용하여 열을 정의하면 실제로 효과가 없으며, 현재 정의하고 있는 열의 참조 의도에 대한 메모 또는 주석 정도로만 사용된다. 그렇기에 이 구문을 사용할 때 다음 사항을 인지하는 것이 중요하다.
  - MySQL은 col_name이 tbl_name에 실제로 존재하는지 또는 tbl_name 자체가 존재하는지 확인하지 않는다.
  - tbl_name에 대해 ```ON DELETE```나 ```ON UPDATE``` 동작과 같은 수행된 작업에 대한 응답으로 행을 변경, 삭제하는 등의 작업을 수행하지 않는다.(작성할 수는 있지만 무시된다.)
  - 이 구문은 열을 생성하지만, 인덱스나 키를 생성하지는 않는다.
      ```mysql
      CREATE TABLE person (
          id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
          name CHAR(60) NOT NULL,
          PRIMARY KEY (id)
      );
      
      CREATE TABLE shirt (
          id SMALLINT UNSIGNED NOT NULL AUTO_INCREMENT,
          style ENUM('t-shirt', 'polo', 'dress') NOT NULL,
          color ENUM('red', 'blue', 'orange', 'white', 'black') NOT NULL,
          owner SMALLINT UNSIGNED NOT NULL REFERENCES person(id),
          PRIMARY KEY (id)
      );
      SHOW CREATE TABLE shirt;
      +-------+-----------------------------------------------------------------------------+
      | Table | Create Table                                                                |
      +-------+-----------------------------------------------------------------------------+
      | shirt | CREATE TABLE `shirt` (                                                      |
      | `id` smallint unsigned NOT NULL AUTO_INCREMENT,                                     |
      | `style` enum('t-shirt','polo','dress') NOT NULL,                                    |
      | `color` enum('red','blue','orange','white','black') NOT NULL,                       |  
      | `owner` smallint unsigned NOT NULL,                                                 |
      | PRIMARY KEY (`id`)                                                                  |
      |   ) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci                |
      +-------+-----------------------------------------------------------------------------+

      DESCRIBE shirt;
      +-------+---------------------------------------------+------+-----+---------+----------------+
      | Field | Type                                        | Null | Key | Default | Extra          |
      +-------+---------------------------------------------+------+-----+---------+----------------+
      | id    | smallint unsigned                           | NO   | PRI | NULL    | auto_increment |
      | style | enum('t-shirt','polo','dress')              | NO   |     | NULL    |                |
      | color | enum('red','blue','orange','white','black') | NO   |     | NULL    |                |
      | owner | smallint unsigned                           | NO   |     | NULL    |                |
      +-------+---------------------------------------------+------+-----+---------+----------------+
      ```
    위 예시의 shirt 테이블에 ```REFEREENCES person(id)```를 작성했지만, 이를 확인하기 위한 ```SHOW CREATE TABLE```과 ```DESCRIBE```에서 모두 REFERENCES의 정보를 찾아볼 수 없다.

### 2.3 '--'로 시작하는 주석
표준 SQL은 주석을 쓸 때 /\*comment\*/ 구문을 사용하고 있으며, MySQL도 이 구문을 지원하고 있다.   
또한, 표준 SQL은 시작 주석 시퀀스로(start-comment sequence)로 "--"를 사용하고, MySQL 서버에선 "#"를 시작 주석 문자로 사용한다.   

MySQL에서의 "--" 주석의 경우에는 한 칸 띄운 후에 사용하는 것이 필요하다. 아래의 예시를 보자.
```mysql
SET @count = 0;
SELECT @count;
SELECT @count--1;
SELECT @count --1;
```

```@count```를 0으로 할당한 뒤, 아래의 SELECT 문에 표준 SQL의 주석 표현식인 "--"을 사용했다. 과연 결과는 어떻게 나왔을까? 각각,

```mysql
+--------+
| @count |
+--------+
|      0 |
+--------+
+-----------+
| @count--1 |
+-----------+
|         1 |
+-----------+
+------------+
| @count --1 |
+------------+
|          1 |
+------------+
```
다음과 같이 주석으로 처리되지 않고 1이 더해져서 출력되는 것을 볼 수 있다. 그리고 이번에는 "--" 뒤에 한 칸 띄운 뒤에 출력을 해보자.
```mysql
SELECT @count-- 1;
+--------+
| @count |
+--------+
|      0 |
+--------+

SELECT @count -- 1;
+--------+
| @count |
+--------+
|      0 |
+--------+
```
나만 그런 것인지 엔터 후 세미콜론을 한 번 더 붙여준 뒤에야 결과가 출력되었는데, 아무튼 1이 모두 주석으로 처리되고 ```@count```가 주석처리되어 원래 가지고 있던 값인 0으로 출력되는 것을 볼 수 있다!

- - -
#### 📖 참고문서
- [MySQL 8.0 참조 설명서](https://dev.mysql.com/doc/refman/8.0/en/)
- [SQL 소개](https://technet.tmaxsoft.com/upload/download/online/tibero/pver-20160406-000002/sql-reference/ch_SQL_concept.html)
