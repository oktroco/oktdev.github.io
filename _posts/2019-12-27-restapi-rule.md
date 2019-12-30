---
layout: post
title: "REST API"
date: 2019-12-27 21:00:00 +0900
categories: [development, etc]
show_sidebar: false
published: true
comment: true
---


# REST API 설계 기본 규칙

## Collection/Document/Store/Controller

### Collection
Collection은 단일 리소스(Document)들의 묶음이다. DB에 비유하여 말하자면 테이블 정도가 되겠다. 일반적으로 Collection은 복수로 이름을 짓는다.
예를 들어 가입된 파티를 관리하는 프로그램일 경우, 파티가 Collection이 되겠다.

```yml
http://<host>/parties #복수로 적는다.
```

### Documnet
Document는 컬랙션 내의 단일 리소스를 말한다. DB에 비유하면 한 테이블의 레코드 정도가 되겠다. 일반적으로 단수로 이름을 짓는다. 만약 내가 관리하는 파티의 아이디가 3인 경우, 3이 Document가 되겠다.

```yml
http://<host>/parties/3 #파티의 아이디가 3인 경우
http://<host>/parties/myparty #파티의 아이디아 myparty인 경우
```

### Store
클라이언트 입장에서 저장하는 리소스 저장소를 이야기한다. DB개념에 굳이 비교하자면 테이블인데, 클라이언트에서 좀더 자유롭게 다루는 리소스이다. 일반적으로 복수로 이름을 짓는다. 쇼핑몰 같은 경우, 장바구니나 위시리스트 정도 되겠다.

```yml
http://<host>/users/3/carts #3번 유저의 장바구니
http://<host>/users/10/wishes #10번 유저의 위시리스트
```

### Controller
(작성 중)

그래서 일반적으로 URL을 구성할 때는 Collection/Document/Store 순서로 짓게 되는데, Collection과 Document가 상위, 하위 개념으로 순서가 정해진다면, Store는 예외로 마지막에 온다고 생각하면 될 것 같다.
각각 다른 Collection과 Document의 관계는 한 url에 모두 표현 될 수 있다.
예를 들어서 3번 파티의 4번 멤버의 경우 아래와 같이 표현될 수 있겠다.

```yml
http://<host>/parties/3/members/4 #Collection/Document/Collection/Document
```

3번 파티의 4번 멤버가 자신의 클라이언트에서 자유롭게 조정할 수 있는 위시리스트를 요청할 때는 아래와 같이 쓸 수 있겠다.

```yml
http://<host>/parties/3/members/4/wish #Collection/Document/Collection/Document/Store
```

## GET/POST/PATCH/DELETE CRUD는 URL에 넣지 않는다

```yml
GET http://<host>/parties/3/delete # X
DELETE http://<host>/parties/3 # O
```

## 언더바 대신 슬래시를 넣는다.

```yml
http://<host>/parties/allow_member # X
http://<host>/parties/allow-member # O
```

(작성 중)