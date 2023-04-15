https://inpa.tistory.com/entry/MYSQL-%F0%9F%93%9A-%ED%92%80%ED%85%8D%EC%8A%A4%ED%8A%B8-%EC%9D%B8%EB%8D%B1%EC%8A%A4Full-Text-Index-%EC%82%AC%EC%9A%A9%EB%B2%95#%ED%92%80%ED%85%8D%EC%8A%A4%ED%8A%B8_%EC%9D%B8%EB%8D%B1%EC%8A%A4_%EC%A0%84%EC%B2%B4%ED%85%8D%EC%8A%A4%ED%8A%B8_%EC%9D%B8%EB%8D%B1%EC%8A%A4


# 검색어 자동완성
- 검색어 자동완성에는 특정 컬럼에 대해 `Full Text Search`와 `n-gram`을 이용한다.
- DB마다 다른 이름이지만 기능은 유사하다. mysql은 `n-gram`, postgresql은 `tringram` 등을 이용한다.

<br />

## 1. 검색어 자동완성 구현 방법
1. DB의 n-gram 기능 이용 (Mysql의 `n-gram`, Postgresql의 `tringram` 등등)
    - 이용자가 적은 경우 (백오피스)
    - 검색엔진 DB 구축 시간대비 효율이 적은 경우
2. 검색엔진 DB 구축 (`Elastic Search`, `Solr`)
    - 데이터의 양이 엄청나게 많은 경우
    - 검색 대상 텍스트의 길이가 꽤 긴 경우 
    - 유사 매칭도 결과에 포함시키고 싶은 경우

## 2. Mysql Full Text Search
- `Full Text Search`는 문자열을 토큰화하여 토큰들의 빈도수를 계산하여 검색하는 방법이다.

### 2-1. 사용법 
1. Full Text Index 생성 

```sql
-- 생성
create fulltext index 인덱스명 on 테이블명(컬럼명);
alter table 테이블명 add fulltext index 인덱스명(컬럼명);

-- 변경
alter table  

show index from 테이블명;

-- 기존 테이블에 추가했다면 인덱스 재 생성
REPAIR TABLE 테이블명 QUICK;
```

<br />

2. Full Text 최소 글자수 설정 (`my.cnf`)
```sql
show variables like '%ft_min%';
```
![image](https://user-images.githubusercontent.com/70880695/229294417-aeaec770-cfca-4987-b64b-1c9d77c6d8af.png)
- `ft_min_word_len`: 최소 토큰 길이 (무조건 4글자 이상 검색어로 들어와야 함)
- `my.cnf` 변경 이후 `OPTIMIZE TABLE (TABLE_NAME)`
- 검색의 편의를 위해 2로 설정

<br />

3. Full Text 검색 진행
```sql
-- 검색어1 또는 검색어2 (or 검색)
SELECT * FROM Table_Name
WHERE MATCH(Col_Name) AGAINST('검색어1 검색어2');
 
-- 검색어 중간에 공백이 들어가는 경우
SELECT * FROM Table_Name
WHERE MATCH(Col_Name) AGAINST('"검색어 검색어"');
 
-- 검색어1을 검색한 결과에서 검색어2가 들어간것을 제외 할 때
SELECT * FROM Table_Name
WHERE MATCH(Col_Name) AGAINST('"검색어1" -검색어2' in boolean mode);
 
-- 검색어1, 검색어2 함께 검색이 되는 경우 (and 검색) 
SELECT * FROM Table_Name
WHERE MATCH(Col_Name) AGAINST('+"검색어1" +"검색어2"' in boolean mode);
 
 -- 검색어1과 검색어2 And 검색과 함께 검색어2의 결과도 함께 출력
SELECT * FROM Table_Name
WHERE MATCH(Col_Name) AGAINST('"검색어1" +"검색어2"' in boolean mode);
```

<br />

## 3. n-gram (Maria 기준)
1. `n-gram`은 문자열을 n개의 문자로 분리하여 토큰화하는 방법이다.
2. `hjun` 문자가 있다면 
   - `n=2` : `hj` `un` 인덱스
   - `n=3` : `hju` `jun` 인덱스

### 3-1. 사용법

#### 1. TEXTFULL INDEX + ngram PARSER 
```sql
CREATE FULLTEXT INDEX 인덱스명 ON 테이블명(컬럼명) WITH PARSER ngram;
```


<br />

<br />

### 참고
1. [sTricky - MySql Mariadb full-text](https://stricky.tistory.com/435)
