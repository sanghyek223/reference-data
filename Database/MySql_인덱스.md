# MySql INDEX
#
#
### 1. INDEX
- **지정한 컬럼들을 기준으로 메모리 영역에 일종의 목차를 생성**
- **INDEX 는 MYI (MySQL Index) 파일에 저장 된다**
- **INDEX 는 테이블의 동작속도(조회)를 높여주는 자료구조**
- **INDEX 는 하나 혹은 여러 개의 컬럼에 대해 설정할 수 있다. (단일 또는 여러컬럼을 묶어 복합 INDEX)**
- **INDEX 가 없다면 Table Full Scan 이 일어나 성능 저하되거나 치명적인 장애가 발생 할 수 있다**
- **SELECT 속도는 빨라지지만 UPDATE, INSERT, DELETE 의 속도는 저하된다는 단점이 있다**
#
#
### 2. ORDER BY 와 GROUP BY에 대한 INDEX
###### INDEX는 ORDER BY 와 GROUP BY 에도 영향을 끼치는데 다음과 같은 경우에는 INDEX를 타지 않는다
- **ORDER BY INDEX 컬럼1, 컬럼2 : 복수의 키에 대해서 ORDER BY를 사용한 경우**
- **WHERE 컬럼1 = '값' ORDER BY INDEX 컬럼 : 연속하지 않은 컬럼에 대해 ORDER BY를 실행한 경우**
- **ORDER BY INDEX 컬럼1 DESC, INDEX 컬럼2 ASC : DESC와 ASC를 혼합해서 사용한 경우**.
- **GROUP BY 컬럼1 ORDER BY 컬럼2 : GROUP BY와 ORDER BY 의 컬럼이 다른 경우**
- **ORDER BY ABS(컬럼) : ORDER BY 절에 다른 표현을 사용한 경우**
#
#
## 3. 다중 컬럼 인덱스
###### 다중 컬럼 인덱스는 두개 이상의 필드를 조합해서 생성한 INDEX이다. 
###### 1번째 조건과 이를 만족하는 2번째 조건을 함께 INDEX해서 사용한다. (MySQL은 INDEX에 최대 15개 컬럼으로 구성 가능)
#
#### 단일인덱스와 다중 컬럼 인덱스의 차이점
#
- Table1(단일 인덱스)
~~~sql
CREATE TABLE table1(
    uid INT(11) NOT NULL auto_increment,
    id VARCHAR(20) NOT NULL,
    name VARCHAR(50) NOT NULL,
    address VARCHAR(100) NOT NULL,
    PRIMARY KEY('uid'),
    key idx_name(name),
    key idx_address(address)
)
~~~
#
- Table2(다중 컬럼 인덱스)
~~~sql
CREATE TABLE table2(
    uid INT(11) NOT NULL auto_increment,
    id VARCHAR(20) NOT NULL,
    name VARCHAR(50) NOT NULL,
    address VARCHAR(100) NOT NULL,
    PRIMARY KEY('uid'),
    key idx_name(name, address)    
)
~~~
#
- Query
~~~sql
SELECT * FROM table1 WHERE name='홍길동' AND address='서울';
~~~
#
###### table1 의 경우에 name 과 address 에 INDEX 가 걸려있기 때문에 
###### MySQL 은  둘 중에 더 빠르게 검색되는 컬럼을 판단 후 빠른쪽을 먼저 검색하고 그 다음 다른 컬럼을 검색하게 된다
#
###### table2 의 경우 바로 원하는 값을 찾는데 그 이유는 INDEX를 저장할 때 name 과 address를 같이 저장하기 때문이다
###### 즉 name 과 address 의 값을 같이 색인하고 검색 에서도 '홍길동서울'로 검색을 시도하게 된다
###### 이렇게 사용할 경우 table1 보다 table2 의 경우가 더 빠른 검색을 할 수 있다
#
###### 그렇지만 다중 컬럼 인덱스를 아래와 같이 사용하면 INDEX를 타지 않는다
~~~sql
SELECT * FROM table2 WHERE address='서울';
~~~
#
###### 이 경우에는 다중 컬럼 인덱스로 설정되어 있던 name 이 함께 검색이 되지 않으므로 INDEX 의 효과를 볼 수가 없다
###### 다중 컬럼 인덱스를 사용할 때는 INDEX로 설정해준 제일 왼쪽 컬럼이 WHERE 절에 사용 되어야 한다
#
#
## 4. 설계방법
###### - 고유한 값 위주로 설계
###### - 조회시 자주 사용하는 컬럼
###### - 한 컬럼이 갖고 있는 중복의 정도가 낮을 수록 좋다
###### - INDEX 키의 크기는 되도록 작게 설계
###### - UPDATE 가 빈번하지 않은 컬럼
###### - JOIN 시 자주 사용하는 컬럼
###### - PK, JOIN 의 연결고리가 되는 컬럼
###### - 단일 INDEX 여러 개 보다 다중 컬럼 INDEX 생성 고려
###### - 무조건 많이 설정하지 않는다. (한 테이블당 3~5개가 적당 목적에 따라 상이)
###### - INDEX 를 생성할 때 가장 효율적인 자료형은 정수형 자료(가변적 데이터는 비효율적)
#
#
## 5. 문법
- 인덱스 생성
~~~sql
-- 단일 인덱스
CREATE INDEX 인덱스이름 ON 테이블이름(필드이름1)

-- 다중 컬럼 인덱스
CREATE INDEX 인덱스이름 ON 테이블이름(필드이름1, 필드이름2, ...)
~~~
#
- 인덱스 조회
~~~sql
SHOW INDEX FROM 테이블이름
~~~
#
- UNUQUE 인덱스 생성(중복 값을 허용하지 않는 인덱스)
~~~sql
-- 단일 인덱스
CREATE UNIQUE INDEX 인덱스 이름 ON 테이블이름(필드이름1)
-- 다중 컬럼 인덱스
CREATE UNIQUE INDEX 인덱스 이름 ON 테이블이름(필드이름1, 필드이름2, ...)
~~~
#
- 인덱스 정렬(인덱스 생성 시점에 필드의 정렬방식 설정)
~~~sql
CREATE INDEX 인덱스이름 ON 테이블이름 (필드이름 DESC)
CREATE INDEX 인덱스이름 ON 테이블이름 (필드이름 ASC)
~~~
#
- 인덱스 삭제
~~~sql
ALTER TABLE 테이블이름 DROP INDEX 인덱스이름;
~~~
#
- 인덱스 추가
~~~sql
ALTER TABLE 테이블이름 ADD (UNIQUE)INDEX 인덱스이름(컬럼명1, 컬럼명2...);
~~~
#
#
## 참고 블로그
[블로그 링크](https://spiderwebcoding.tistory.com/6)
