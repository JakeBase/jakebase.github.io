---
layout: post
title: "드로이드 나이츠"
slug: "droid-knights"
date: 2017-03-25 10:00:00 +0900
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
  - 설정해줘야 하는 부분이 너무 많다
- fastlane
  - ios빌드에 많이 쓰인다
  - 안드로이드는 supply, screengrab 두가지 정도 사용 가능
- circleci
  - 처음엔 travis를 고려했으니 너무 비싸고 안드로이드에선 별로임 그래서 circleci, 상용으로 많이 쓰고있어서 reference할만한게 많음
  - 장점
    - 각 빌드가 독립적으로 동작
    - 모던한 UI
    - 가격 적절 1달에 50달러
    - circle.yml 설정 파일을 수정하는 것만으로 빌드 설정 적용 가능
    - github과 유기적인 연동 가능
  - github 루트 디렉토리에 놓으면 빌드가 된다
    - 편하긴 한데 빌드 설정 바꿀때마다 git에 push해야함..
  - keystore 저장을 위한 추가적인 저장소가 필요 -> 보통 dropbox사용
- bitrise
  - 기본은 무료(빌드 시간,횟수 제한), 1달 50달러
  - 모바일 환경에 적합한 CI시스템
  - Android, iOS CI preset이 잘 만들어져있다.
  - Workflow 라는 과정이 명시적으로 있다
  - secret environment 설정 가능. 필요하지만 공개되지 않았으면 하는 환경변수 설정 가능.
  - 장점
    - circle ci의 장점은 대부분 가지고있음
    - 워크플로우를 UI로 조정가능
    - fastlane을 자동화 툴로 지원
    - 가장 많은 써드파티 연동 지원
    - preset덕분에 극단적으로 빠르게 적용 가능
    - docker 기반으로 circleci보다 빠른 느낌
  - CI 서버에 테스트 : Roboletric 적용중
  - AAR 같은 라이브러리는 어떻게?
    - Bitray? 사용

## UiAutomator와 AWS Device Farm 을 활용한 UI 테스팅 자동화

- 수동테스트는 많은 인력과 시간이 필요함
- 안드로이드는 각 기기별로 어떤 문제가 생길지 파악하기 힘듦
- 자동화 테스트를 통해 이런 문제를 해결 가능하다
- UiAutomator는 multiple vs Espresso는 single App에서
  - Espresso는 화이트박스 테스트이고 해당 앱에서만 테스트 가능함
  - UiAutomator는 blackbox 테스트, 사용자가 직접 테스트 하는것처럼 테스트 가능
- 실제 사용자가 앱을 쓰는 시나리오처럼 테스트 하는게 목적이었다.
- UiAutomator2
  - By : BySelector 객체를 생성하는 유틸리티 클래스
  - BySelector : 화면상에서 Ui요소를 찾기 위한 선택지
  - UiDevice : 기기의 상태에 접근하고 기기를 제어하는 클래스
  - UiObject2 : ?
  - 이 네가지를 알면 웬만한건 모두 가능하다
- 패키지
  - 특정 패키지에 속하는 요소를 찾을 수 있다
- 클래스
  - 특정 클래스에 해당하는 요소를 찾을 수 있다
- 컨텐트 설명
  - 컨텐트 설명으로 요소를 찾을 수 있다
- BySelector를 조합하면 모든 요소를 찾을 수 있다
  - 이렇게 찾은 요소를 가지고 테스트를 하면된다
- UI 동기화를 시켜줘야 fail이 잘 안난다
- 애니메이션은 꼭 비활성화를 시키고 하는게 좋다
  - 기기는 직접 끄면 됨
  - 서버에서 테스트 할때는 코드로 처리해야함
- 클라우드 테스팅
  ```text
  - AWS : 기기도 많고 버전도 다양함, 거의 모든 폰 테스트 가능
  - firebase testlab : 기기가 적음
  - xamarin test cloud : CI 서버 사용이 제한적임
  ```
  - AWS로 선정!
  - UiAutomator2부터는 APK파일을 업로드 해주면 된다
    - assemble android test -> enable
  - device 선택가능

## Anatomy of Realm (Realm 심층 분석)

- Realm Mobile Platform
  - 전통적인 REST 서버를 사용할때 생기는 불편한 점들을 없애줌
  - 로컬, 서버사이드 서버가 같음
- Realm Mobile Database는 어떤식으로 구성되어있나?
- Realm 모바일 객체의 특성
- 왜 그렇게 하는건가요?
1. 무복제 매커니즘 : 자바로 데이터를 복사하지 않는 것
- Facebook 사례
  - json 인코딩 디코딩에 많은 시간이 들었음
  - google의 flatbuffer를 이용하기로 결정
- Hydrate
  - 일반적인 데이터베이스 사용시 쿼리된 결과를 문자열이나 인코딩된 형태로 가져옴
  - 결과를 파싱, 디코딩해서 객체에 할당해줌
  - 이런 수화과정이 CPU와 메모리를 낭비함, 쓰지도 않을 데이터를 전부 변환하고 필드에 담았음.
- Realm은 무 복제 메커니즘을 사용한다
  - 로우와 컬럼을 이용해서 데이터를 가져옴
  - 사용자의 RealmObject를 가지고 RealmProxyObject를 만든다
  - APT를 이용함
  - Realm은 어떤 어노테이션 프로세스를 실행할지 결정하는 메타데이터가 있다
    > 앱에서 반복적으로 하는 작업이 있다면 APT를 만들어보세요
- APT만으론 부족하다
  - APT는 객체를 생성할 수만있다, 기존 객체를 상속받고 변경하는게 불가능
    - 그래서 예전 Realm은 getter,setter를 정해진 이름만 사용하도록 요구했음
- Byte code를 변경하는 것이 해결책
  - Lazy하게 custom getter,setter를 호출하는 것이 가능
- 트랜스포머 API
  - 간단하게 바이트코드를 변환할 수 있다
2. 크로스 플랫폼
3. 자동 갱신
- 베리 빅, 빅+ 트리
- 최근 데이터베이스에선 한column씩 저장하는 추세, padding을 따로 넣을 필요가 없고
  - 멀티 스레딩을 고려하면 같은 필드끼리 묶어서 저장하는게 나을 수 있음
- MVCC
  - 새 쓰기는 새 스냅샷을 만든다
  - 쓰기중에도 읽기가 가능하다.
  - 읽기는 배타적이지 않음
- Gradle Plugin
  - 환경별로 어노테이션 설치는 사용자에게 어렵다.
  - Plugin 만들기
    - Plugin 객체 상속
    - Apply 메소드 구현
- Realm Core와 Realm Java는 오픈소스입니다

## KeyFrame과 Lottie로 인터렉티브한 애니메이션을 구현해보기

- KeyFrame과 Lottie
  - AfterEffect기준으로 기능들이 만들어짐
  - Lottie에선 keyframe에서 안되는게 다 가능함
  - 그래서 Lottie로 발표함ㅎ
- Lottie 라이브러리 사용하기
  - AfterEffect, Body-Movin Plugin, ? 가 필요함
- 끊기지 않게 하려면 24프레임 이상만 설정하면 된다
- AfterEffect에서 json파일을 생성 -> 안드로이드 Asset에 넣으면 사용 가능
- 어디에 사용할 수 있을까?
  - Android Material Stepper, 간단하게 stepper형태의 애니메이션을 구현할 수 있는 라이브러리
  - 내가 원하는 frame만큼 재생을 하고 중지 시킬 수 있다.
- http://www.lottiefiles.com