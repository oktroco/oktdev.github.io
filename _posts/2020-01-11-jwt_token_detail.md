---
layout: post
title: "JWT의 단점과 보완방법(Refresh 토큰)"
date: 2020-01-11 21:00:00 +0900
categories: [development, etc]
show_sidebar: false
menubar: main_menu
published: true
comment: true
---

JWT토큰은 치명적인 단점이 있다. 세션과 다르게 만료가 되지 않기 때문에 한번 털리면 토큰이 만료되기 전까지 계속 털려야한다.
이러한 단점을 보완하기위해 보편적으로 jwt토큰의 만료기간을 5분정도로 짧은 access토큰과 refresh 토큰을 같이 쓴다.
access 토큰은 일반적인 통신을 할 때 항상 헤더에 포함되어야하는 토큰이다.
access 토큰의 만료기간을 짧게 잡으면, 토큰이 털리더라도 짧은 시간의 로그인만 허용하므로 단점이 어느 정도 보완된다.
그러면 5분마다 사용자가 로그인을 해야하는가? 당연히 그럴 수는 없기에 이때 등장하는 게 refresh 토큰이다.
통상적인 통신을 위해 쓰이는 access 토큰이 만료가 되면 이 때 상대적으로 만료제한기간이 긴 refresh토큰을 이용하여 새로운 access 토큰을 발행한다.
이러면 유저입장에서는 refresh 토큰의 유효기간 동안은 아무 문제 없이 편리하게 서비스를 이용하면서, JWT토큰의 단점을 어느정도 보완할 수 있다.

(작성 중)