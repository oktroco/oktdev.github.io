---
layout: post
title: "Django 잊고 있을 수 있는 팁"
date: 2019-01-16 20:00:07 +0900
categories: [development, etc]
show_sidebar: true
menubar: main_menu
published: false
comment: true
---

# related_name

FOO_set
역참조 기능

# Foreign Key의 _id 옵션

속성에 "_id"를 붙이면 해당 모델의 primary_key 값을 그대로 가져온다.

ex) 속성이름이 party라면 party_id를 탐색할 경우 pk를 바로 가져옴. None이라도 오류 안뜸.

ForeignKey인 속성이 null=False 라면 해당 키를 참조할 때 오류를 띄운다.

null=True 를 하면 오류를 띄우지 않고 None을 반환한다.

윈인이 __str__ 같은 건 아니고, Django에서 ForeignKey를 참조하려면 그 안의 속성들을 탐색하는데, 이때 오류가 난다.
하지만 null=True라면 오류에 대해 핸들링을 하준다.

# pip 설치목록 백업

pip freeze > 이름



# 생각해야 하는 것

다음 결제일까지 allow되어있지 않은 멤버의 결제. 처리.

본인인증 계약 관련,
본인인증 DB 저장 방식


# git

모듈 설치 필요 엑셀
user db에 계좌정보 account_name, account_bank, account_num 추가
