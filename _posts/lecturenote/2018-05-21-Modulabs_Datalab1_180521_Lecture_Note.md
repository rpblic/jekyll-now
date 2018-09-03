---
layout: post
title: Modulabs Datalab1 180521
category: Lecture_note
tags: ['Modulabs', 'ComputerScience', 'Internet', 'InformationTheory']
---

# Modulabs Datalab1 180521

### How Computers Work?

* Input, Processing, Memory, Output
* Binary Data: 컴퓨터는 0과 1로 모든 데이터를 저장
* String >> ASCII Integer >> Binary(8bit)
IMG >> dots(pixels) with RGB Integer Data

* How to do Processing? >> Circuit(논리회로) and Logic
![Curcuit](http://bssbmi.com/wp-content/uploads/2016/10/NV_0807_Marston_Figure12.jpg)

* Processing(CPU) <> Information Storing(Memory)

----------------
### Internet 101

* 전기: 저렴하지만 정보 손실 가능성, 전달 거리 짧음
* 빛: 반사되는 각도에 따라 여러 비트를 보낼 수 있음, 빠르고 신호 손실이 없으나 비싸고 다루기 힘듬
* 라디오 전파: 전선이 필요없지만 정보 손실 가능성, 전달 거리 짧음

* IP(Internet Protocol): 기기의 주소
* DNS(Domain Name System): 인터넷 주소와 IP 주소를 연결해 주는 시스템
 * 분산 계층 구조를 통해 주소의 Searching을 가능하게 함
 * 사이버 공격 대상이 되기 쉬운 프로토콜(spoofing 등)
패킷, 라우터
* TCP(Transmission Control Protocol): 정보를 전송받는 일련의 과정
 * Scalable, 다른 사람의 이용을 방해하지 않음.
* HTML: 웹 문서 언어
 * 웹 브라우저>> POST 요청 >> 정보 확인 후 성공 페이지/ Cookie 발송
* 보안: SSL(Secure Sockets Layer), TLS(Transport Layer Security), SSH(공개 키 <> 개인 키 방식)

----------
### Information Theory- Ancient

상징하는 바를 닮은 그림 >> 추상적 개념으로써의 그림 >> 소리의 결합을 통한 새로운 의미 창출(레부스 체계) >> 그림의 의미가 사라지고 소리만 남음 >> 문자의 단순화, 필기에 간편하도록 변형(데모틱 문자)

1. Source Encoding: 신호를 전송할 때 발생하는 소음을 어떻게 제거할 수 있을지?
2. 얼마나 많은 정보를 전송할 수 있을지?
3. 정보를 전송하는 방법을 얼마나 빠르게 시행할 수 있을지?

- 정전기 전신: 전도체와 부도체의 존재를 발견, 방전 현상의 발견 >> 정전기를 이용한 전신의 제작, 축전기를 발명
- 전하의 발견과 이를 통한 배터리 개발
- 전자기의 개발, 이를 통한 검류계 발명
- 자기장을 증폭하는 방법을 발견, 긴 선과 배터리를 통한 정보 전달 가능성
- 모스 부호의 개발- 동일 시간에 전달할 수 있는 시간을 늘림, 스프링과 레버를 이용하는 UI
