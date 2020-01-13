---
title: DB 문제풀이 1일차
date: 2020-01-06 10:12:50
categories: 
    - Algorithm
    - DB
---

[문제출처](https://drive.google.com/drive/folders/1U8Lz4ogouytU6Mwoozi_11PdbCDEP8Ri)

#### 1. `country` 테이블에서 중복을 제거한 `Continent`를 조회하세요.

~~~mysql
USE world;
SELECT DISTINCT Continent
FROM country
~~~

`DISTINCT 키워드`: 지정된 컬럼명이 중복되지 않고 고유한 자료만으로 반환

#### 2. Sakila 데이터 베이스에서 인도 고객의 수를 출력하시오.

~~~mysql
USE world;
SELECT Name, Population
FROM country
WHERE Name="India"
~~~

#### 3. 한국 도시중에 인구가 100만이 넘는 도시를 조회하여 인구순으로 내림차순하시오.

~~~mysql
USE world;
SELECT *
FROM city
WHERE CountryCode="KOR" AND Population >= 1000000
ORDER BY population DESC
~~~

#### 4. `city` 테이블에서 `population`이 800만 ~ 1000만 사이인 도시 데이터를 인구수순으로 내림차순하시오.

~~~mysql
USE world;
SELECT *
FROM city
WHERE Population BETWEEN 8000000 AND 10000000
ORDER BY Population DESC
~~~

#### 5. `country` 테이블에서 1940 ~ 1950년도 사이에 독립한 국가들을 조회하고 독립한 년도 순으로 오름차순하시오.

~~~mysql
USE world;
SELECT Code, Name, IndepYear
FROM country
WHERE IndepYear BETWEEN 1940 AND 1950
ORDER BY IndepYear ASC
~~~

#### 6. `countrylanguage` 테이블에서 스페인어, 한국어, 영어를 95% 이상 사용하는 국가코드를 `Percentage`로 내림차순하여 조회하시오.

~~~mysql
USE world;
SELECT CountryCode, Language, Percentage
FROM countrylanguage
WHERE Language IN ("Spanish", "Korean", "English") AND Percentage >= 95
ORDER BY Percentage DESC
~~~

#### 7. `country` 테이블에서 `Code`가 `A`로 시작하고 `GovernmentForm`에 `Republic`이 포함되는 데이터를 조회하시오.

~~~mysql
USE world;
SELECT Code, Name, Continent, GovernmentForm, Population
FROM country
WHERE Code LIKE "A%" AND GovernmentForm LIKE "%Republic%"
~~~

s