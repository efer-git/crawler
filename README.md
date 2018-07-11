
[숙제] Crawler 짜는 문서
=================================
서초동 숙제용 문서이다
****
# Crawler Design

* 하려고 하는 것
  1. 네이버(지도)에서 식당리스트를 받음
  2. 해당 식당과 연관된 인스타그램 태그 사진을 받음
  
## 1. 네이버(지도)에서 식당 리스트 받기
숙제용이라면 1.1도 괜찮아보일...수도 있으나, 후술할 문제로 1.2.로 가는걸로 함
### 1.1. 네이버 '지도'에서 식당 리스트 받기
모티브) 우리가 네이버 맵을 눌렀을 때의 상황을 구현함  
``` rr = requests.get("http://map.naver.com/search2/local.nhn?page=3?searchCoord=127.0190942;37.4887635&query=%EB%A7%88%EC%B9%B4%EB%A1%B1&menu=location",headers=header) ```
header에 너의 쿠키정보가 있어야함.. 토큰같은걸 발급해서 리턴값을 주는 듯..  
문제는, 일정시간이 지나면 토큰이 만료되서 토큰을 따로 받아야함  
bioi처럼 토큰 갱신하는 코드가 명시적으로 있지는 않아보임...  
(장점)   
1. 식당이름 - 식당사진 - 부가정보가 세트로 나옴
2. 직관적임
3. 9 개씩 끊어서 리턴을 줌(이거 장점 아닌것 같음)  
-> 그렇게 자주 크롤링할게 아니라면 괜찮은 방법인듯  

(단점)
1. 세션정보가 필요함
2. 9 개씩 끊어서 리턴을 줌(이거 장점 아닌것 같음)  
3. 네이버 지도에서 제공하는 사진을 쓰므로, 네이버 지도랑 차별성이 없음

### 1.2. 네이버에서 식당리스트 받기
모티브) 네이버 검색창에서 쿼리를 날렸을 때의 상황을 구현함  
사실 뭔가 많이 나옴. 블로그/카페/지식인 같은거 들어가서 파싱하고 싶지는 않음.  
이게 정형화된 데이터가 아니라면 상관이 없는데, 숙제 자체는 꽤나 정형화된 상황임  
메뉴판이나, 블로거 추천 메뉴같은걸 가져오려면 쓸만은 해보임...  
~~~근데 어짜피 내가 안갈꺼라 굳이 짤필요는 없어보인다~~~   
겉보기에는 네이버 지도를 가져온듯한 뭔가가 있지만, 개발자모드를 키면 뭔가 다른 api도 돌아가는걸 볼 수 있음   
``` rr = requests.get("https://store.naver.com/sogum/api/businesses?query=%EC%84%9C%EC%B4%88%EB%8F%99%20%EB%A7%9B%EC%A7%91&pageIndex=1&start=13&display=100")```  

뭔가 위에 API처럼 쿠키정보를 요구하지는 않아보임.굳굳함  
근데 브라우저상에서 해당 정보를 출력하는 부분은 어디일까?  
뭐 일단 잘 줌...굳이 개발자 API를 쓰지 않아도 되어보임...~~~의문의 꿀~~~

### 1.3. 네이버 API를 이용해 식당리스트 받기
사실 이게 정공법이긴 함  
근데 개발자 등록하고 서비스생성 눌러서 별도의 ID-password 받아야함  
얼마 안걸리는 작업이기는 한데, ~~~귀찮기도 하고...크롤러 짜는주제에 거창하기도 하고....~~~   
```curl "%EC%B4%88%EB%8F%99%20%EB%A7%88%EC%B9%B4%EB%A1%B1&display=10&start=1&sort=random" -H "X-Naver-Client-Id:DlHWM1uoDznSIgv66VWC" -H "X-Naver-Client-Secret:mAvKi6UsVZ" -v```  
*--> 뭐 아무튼 결과는 받음...SUCCESS!*

----

## 2. Instagram에 query 날리기 
얘도 로그인같은거 해서 코드를 받아야함..귀찮음...  
~~~컴퓨터에서 인스타 로그인하는거 좀 적응안댐ㅋㅋ~~  
Client_ID Client secret CLIENT_TOKEN등이 혼재한다.  
id / secret / response_url --> token 
scope도 적어서 토큰 받아야함. 신기한건, scope를 바꿔도 token값은 안바뀜...  
SANDBOX MODE에서는 또 뭐가 제대로 안댐  
```https://api.instagram.com/v1/tags/jeju/media/recent?access_token=TOKEN```  
하면 뭔가 나와야하는데 sandbox mode에서는 deprecation warning만 띄움  
live mode로 해야 된다고 함...

