---
published: true   
title:  "캐시(Cache)와 Redis"
excerpt: "캐시(Cache)의 개념과 Redis"

categories:
- Cache

tags:
- [Cache, Redis]

toc: true
toc_sticky: true

date: 2022-08-18
last_modified_at: 2022-08-18
---

### 캐시(Cache)의 개념
캐시란 나중에 요청할 결과를 미리 저장해둔 후 빠르게 서비스해 주는 것을 의미한다.    
한 번 읽어온 데이터를 임의의 공간에 저장하여 다음에 읽을 때는 빠르게 결과값을 받을 수 있도록 도와주는 공간이다. 그래서, 같은 요청이 들어오는 경우에는 캐시서버에서 바로 결과값을 반환해주기 때문에 DB부하를 줄일 수 있음과 동시에 서비스의 개선도 이룰 수 있다.

### 캐시 서버 도입
서비스를 처음 운영할 때는 WEB-WAS-DB의 구조로 구축하지만, 사용자가 늘어나면 DB만으로는 부하를 견딜 수 없게된다. 매 트랜잭션마다 디스크에 접근해야하므로 부하가 많아지면서 성능이 떨어지게 되어 캐시 서버 도입을 검토하게 된다.  

#### 캐시는 아래와 같은 경우에 사용을 고려하면 좋다.
- 접근 시간에 비히 원래 데이터를 접근하는 시간이 오래 걸리는 경우(서버의 균일한 API 데이터)
- 반복적으로 동일한 결과를 돌려주는 경우(이미지나 썸네일 등)

### 캐시 사용 구조
![cache](https://user-images.githubusercontent.com/62706198/185438590-ddf6b70c-2883-4d87-beca-50385317c02f.JPG)
1. Client로 부터 요청을 받는다.
2. Cache와 작업을 한다.
3. 실제 DB와 작업한다
4. 다시 Cache와 작업한다.

클라이언트가 웹 서버에 요청을 보내면, 웹 서버는 데이터를 DB에서 가져오기 전에 캐시에 데이터가 있는지 확인하고, 있다면 바로 클라이언트에게 저장된 데이터를 반환한다. 이를 Cache Hit일라고 한다.  
반대로 캐시 서버에 데이터가 없으면 DB에 데이터를 요청하여 원하는 데이터를 조회한 후 그 데이터를 클라이언트에게 제공하는데, 이를 Cache Miss라고 한다.   
위 flow에서 캐시를 어떻게 사용하느냐에 따라서 look aside cache와 write back으로 나뉜다.

#### look aside cache (Lazy Loading)
1. Cache에 Data 존재 유무 확인
2. Data가 있다면 cache의 Data 사용
3. Data가 없다면 cache의 실제 DB Data 사용
4. DB에서 가져온 Data를 cache에 저장
- look aside cache는 캐시를 한번 접근하여 데이터가 있는지 판단한 후, 있다면 cache의 데이터를 사용하며 없다면 실제 DB 또는 API를 호출하는 로직으로 구현된다. 대부분의 cache를 사용한 개발이 해당 프로세스를 따르고 있다.

#### wirte back
1. Data를 Cache에 저장
2. Cache에 있는 Data를 일정 기간동안 Check
3. 모여있는 Data를 DB에 저장
4. Cache에 있는 Data 삭제
- write back은 데이터를 cache에 모으고 일정한 주기 또는 일정한 크기가 되면 한번에 처리하는 방식이다. DB에서 디스크를 접근하는 횟수가 줄어들기 때문에 성능 향상을 기대할 수 있지만, DB에 데이터를 저장하기 전에 캐시 서버가 죽으면 데이터가 유실된다는 문제점이 있다.


### Redis 
Redis는 Remot Dictionary Server의 약자로 외부에서 사용 가능한 Key-Value 쌍의 해시 맵 형태의 서버라고 생각할 수 있다. 그래서 별도의 쿼리 없이 Key를 통해 빠르게 결과를 가져올 수 있다.
![redis](https://user-images.githubusercontent.com/62706198/185442009-fce80d43-b791-4dad-8540-804013cb5968.JPG)


### Redis의 특징
- 영속성을 지원하는 인-메모리 데이터 저장소
- 다양한 자료구조를 지원한다. 
    1. Strings: 단순한 키-값 매핑 구조
    2. Lists: Array 형식의 데이터 구조. List를 사용하면 처음과 끝에 데이터를 넣고 빼는 것은 빠르지만 중간에 데이터를 삽입할 때에 어려움이 있다.
    3. Sets: 순서가 없는 데이터의 집합. 중복된 데이터는 하나로 처리한다.
    4. Sorted Sets: Sets과 같은 구조이지만, Score를 통해서 순서를 정할 수 있다.
    5. Hashes: 키-값의 구조를 여러개 가진 object 타입을 저장하기 좋은 구조이다.
- 싱글 스레드 방식으로 인해 연산을 원자적으로 수행이 가능하다.

"레디스는 고성능 키-값 저장소로서 문자열, 리스트, 해시, 셋, 정렬된 셋 형식의 데이터를 지원하는 NoSQL이다."

### Redis 영속성
Redis는 지속성을 보장하기 위해 데이터를 DISK에 저장할 수 있다. 서버가 내려가더라도 디스크에 저장된 데이터를 읽어서 메모리에 로딩한다.   
데이터를 디스크에 저장하는 방식은 크게 두 가지 방식이 있다.
- RDB(Snapshotting) 방식: 순간적으로 메모리에 있는 내용 전체를 디스크에 옮겨 담는 방식
- AOF(Append On File) 방식: Redis의 모든 write/update 연산 자체를 모두 log 파일에 기록하는 형태

### 정리
Redis는 인-메모리 방식의 데이터 저장소로 캐시서버를 구성하는데 사용되는 오픈소스이다. 캐시서버를 구성하기 위한 논의를 시작하게 되면 보통 제일 먼저 생각할만큼 널리 쓰이고 있다. 캐시서버를 구성하는 순간 비용도 생각보다 많이 들고, 관리도 어렵다. 하지만 서비스 개선을 위해서 반드시 필요하다.

#### 참고
https://www.youtube.com/watch?v=mPB2CZiAkKM   
https://brunch.co.kr/@jehovah/20