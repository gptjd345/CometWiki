
#### Step 1. 브라우저는 HTTP 메시지를 만든다. 
브라우저 검색 창에서 URL(www.naver.com)을 입력한다. 
브라우저는 www.naver.com을 해독한다. 
url 이 보통 `http://www.naver.com/` 이렇게 `/` 로 끝나는 경우 요청하는 html  파일이 생략되어있음을 뜻하며 보통 서버에서  해당 요청에서 대해 디폴트로 보여줄 페이지를 설정하는데 그게 index.html 이다. 

![[Pasted image 20240713232343.png]]
[https://hanseul-lee.github.io/2020/12/24/20-12-24-URL/](https://hanseul-lee.github.io/2020/12/24/20-12-24-URL/)


#### Step 2. 로컬 DNS 캐시를 확인
`www.naver.com` 라는 도메인 네임에 대한 IP 주소를 획득하기 위해 먼저 로컬 DNS 캐시를 확인해서 `www.naver.com`에 대한 IP 주소 정보가 있는지 확인한다. 로컬 DNS 캐시에 해당 IP 정보가 있다면 
해당 IP 로 http 요청을 보낸다. 

#### Step 3. HTTP 요청 전송
http 요청을 네트워크 전송에 적합한 형태로 인코딩한다. 그 후 사용자와 www.naver.com 서버 간의
논리적인 연결이 세션을 만들고 물리적인 데이터 송수신을 위한 포트 번호와 순서정보등에 대한 정보를 받은 뒤 IP 주소를 이용하여 결정된 경로로 물리적인 네트워크 장비 간 데이터를 전송한다.  




