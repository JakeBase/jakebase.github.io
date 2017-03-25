---
layout: post
title: "드로이드 나이츠"
slug: "droid-knights"
date: 2017-03-25 01:00:00 +0900
categories: blog
---
# Droid Knights

## Compose everything with rx & kotlin , 허재위

- Rx자바의 함정
  - 메모리 릭이 있다. Stream을 취소해주지 않으면 예전에 돌던 작업이 계속 됨.
  - CompositeDisposable이라는 어레이에 스트림들을 넣고 나중에 한번에 취소해준다?
- 화면 회전이 일어났을 때 문제를 Rx로 해결
  - Cache / Subject로 이런 상황을 해결
  - proxy stream을 만들어 UI와 관련된 스트림을 모두 넘겨줌
  - Cache로 에전에 진행중이던 stream을 다시 붙임?
  - 지금까지 개발해오던 방식(상태 변수등을 이용해 화면 회전같을 때? UI유지하는거)를 탈피하기 위해..
- kotlin-maze
  - main function
- 장점은?
  - test하기 편하다
- 결론
  - 어떤 화면에 대한 logic을 간단하게 처리할 수 있다.
  - 함수의 재사용성이 높아진다
  - 학습 비용이 크다
  - 팀 사이즈가 커지면 서로간의 합의가 필요함
  - 옳은 방향인가? 그런듯하다

## Clean Architecture in Android, 황성현

- 개선되고 변화하는 제품, 코드가 계속 변화한다.
- 변화하는 코드에 대응할 수 있는 아키텍쳐는 무엇인가?
  - 변화에 따른 코드의 변경이 적다.
  - 코드가 잘 분리되어있다.
  - 코드가 본질에 맞게 설계되어있다.
- The Clean Architecture
  - Framework
  - UI
  - Database
  - 등에서 independant할 것
- 4 Layers
  - Presentation Layer
  - Data
  - Domain
  - Entity
  - 아래로 의존적인 단방향 모델(Data는 presentation에 대해 모른다)
- Entity
  - 순수한 Java(Kotlin) 모듈
  - Android와의 의존성이 없음
  - Foreign key를 이용한 join등은 Entity level에서 표현하지 않는다.
- Domain
  - 순수한 Java(Kotlin) 모듈
  - Use Case
- Data
  - Repsitory의 실제 구현
  - Data Source 의존성
  - Android 의존성(SharedPreference, Context등이 필요한 경우가 있다)
- Presentation
  - UI 레벨의 처리
  - Android 의존성
  - View가 Presentation의 어떤 부분을 호출할지 결정하는건 좋지 않은 구조라고 생각
  - View의 onViewCreated() -> Presenter의 onViewCreate()호출
- 좋은 코드 구조가 좋은 제품으로 이어진다고 믿음

## 안드로이드 스튜디오 플러그인 만들기 - 차영호

- 안드로이드를 차량에 올리는 작업을 하고있음
- intellij에서 안드로이드 빌드가 가능하게 하는 시도를 하면서 얻은 경험을 공유
- Android Studio와 IntelliJ IDEA소스를 직접 참고를 해야한다..API문서가 없어서..
  - github에 공개가 돼있는데 다 못봄 자바 파일만 5만개
  - openapi 폴더만 보면 봐야할 파일이 3000개로 줄어든다
  - 끝에 api라고 돼있는 폴더들 보면 3000-> 1200개만 보면 된다
  - API문서가 예전에는 있었는데 오픈소스로 오픈하면서 없어짐
- 문서 중간쯤 보면 gradle 어떻게 설정하는지 나옴, 근데 이것도 옛날 내용

## 파이어베이스를 실제 모바일 백엔드로 사용하면 일어날 수 있는 일들

- 클라이언트 개발자가 서버 관련 작업을 할 수 있다.
- 보안이 좋다
- Firebase 데이터베이스
  - 데이터베이스를 설계하는 과정에서 한계가 있다.
- Firebase 사용
  - 검색은 안된다고 보는게 맞다
  - 이미지 로딩하는데 시간이 오래걸렸음
  - 이미지 불러오는데 3단계가 되는데 서버가 미국에 있음..
  - 리스트에서 여러 이미지를 불러오는 경우 딜레이가 너무 크다.
  - 토큰 값이 계속 바뀌기 때문에 picasso, glide등을 수정하지 않고 그대로 쓰기 힘들다.
  - image preview는 meta 값을 이용하는데 firebase는 이것을 지원하지 않는다.
- share를 위해(Preview)외부 서버 추가함
  - node.js 이용
  - thumbnail client가 원하는 사이즈로 땡겨올 수 있게 만들고
  - dynamic end 페이지 만들고..
  - preview 가능하게 만들었음.
- 그럼 firebase를 왜씀?
  - node.js를 사용하면 firebase의 cloud function 사용가능. beta인데 이게 좋다.
  - realtime database 지원
  - analytics
  - HTTP Request 처리 가능
  - cloud pub/sub 가능
  - firebase 는 nosql이라 첨 db설정은 어려움이 있을수 있지만 동시 접속자 수에 제한이 없다.
- Firebase가 망하면 어떡하나?
  - Paas.com 서비스 접었던 사례가 있음

## Firebase Remote Config로 배포없이 앱 운영하기

- 실시간에 가깝게 사용자에게 피드백하는 것이 중요하다
  - 불만이 늘어나는 시간을 줄이는 것
  - 앱은 실시간으로 대응하기에는 배포가 무겁다
    - 개발 - 리뷰 -  배포 ...
- Firebase Remote Config
  - 사용자가 앱 업데이트를 받지 않아도 앱의 동작을 바꿀 수 있는 것
    1. Firebase에 앱의 동작을 제어하는 값을 정의한다
    1. 앱에서 Firebase에 정의한 값을 가져와 적용
    1. 배포 없이 업데이트 된 새로운 동작을 확인한다.
  - Condition : 이 값을 어떤 대상에게 배포하는지 설정, 특정OS버전, 특정 사용자들을 구분 가능.
  - RemoteConfig 개체에서 사용가능한 API
    ```java
    getBoolean()
    getByteArray()
    getDouble()
    getLong()
    getString()
    ```
- 장점
  - 쉽게 운영 페이지까지 개발이 된다. 게임쪽에선 이런 니즈가 크다.
  - 가장 좋은 점은 target이 쉽게 변경 가능하다는 점
  - 실제 장점은 서버 개발자가 아니라 운영팀에서 config설정을 바꿀 수 있다는 점 정도인듯
- 단점
  - Test가 불가능함~

## 빠르고 지속적으로 전달하기: Continuous Delivery for Android

- 캐시 슬라이드 , 2주에 한번씩 업데이트 진행, 반복되는 작업이 많다
- 소프트웨어 개발 파이프라인
  - Commit - Build - Test - Deploy
- Continuous Integration
  - 새로운 빌드와 단위 테스트
- Continuous Delivery
  - 지속적인 통합, 자동화 테스트, 배포
  - 최소한의 수동 오버헤드로 제품을 배포 가능하게 하는 것
- Continuous Deployment
  - 패키징
- 자동화
  - 자동화가 목적은 아니다. 자동화는 목적을 해결하는 도구
  - CI != CI 시스템
  - Jenkins