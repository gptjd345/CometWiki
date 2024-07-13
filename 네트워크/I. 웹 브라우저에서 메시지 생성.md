
#### Step 1. 브라우저는 HTTP 메시지를 만든다. 
브라우저 검색 창에서 URL(www.naver.com)을 입력한다. 
브라우저는 www.naver.com을 해독한다. 
url 이 보통 `http://www.naver.com/` 이렇게 `/` 로 끝나는 경우 요청하는 html  파일이 생략되어있음을 뜻하며 보통 서버에서  해당 요청에서 대해 디폴트로 보여줄 페이지를 설정하는데 그게 index.html 이다. 

![[Pasted image 20240713232343.png]]
[https://hanseul-lee.github.io/2020/12/24/20-12-24-URL/](https://hanseul-lee.github.io/2020/12/24/20-12-24-URL/)


#### Step 2. DNS 리졸버를 통해 DNS 서버를 조회한다.  
HTTP 메시지를 만들면 다음에 이것을 OS에 의뢰하여 엑세스 대상의 웹서버에게 송신한다. 
브라우저는 URL을 해독하거나 HTTP 메시지를 만들지만 메시지를 네트워크에 송출하는 기능은 없으므로 OS에 의뢰하여 송신한다. 이때 URL 안에 쓰여있는 서버의 도메인명에서 IP 주소를 조사해야한다. 
OS에 송신을 의뢰할때는 도메인 명이 아니라 IP 주소로 메시지 수신 상대를 지정해야하기 때문이다. 

