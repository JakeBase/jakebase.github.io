---
layout: post
title: "안드로이드 스튜디오(IntelliJ) 유용한 단축키 및 설정"
tags: [Android, Android Studio, IntelliJ]
comments: true
---

최근 사내 초빙 강사로 Hadi Hariri 라는 분이왔었다. 알고보니 JetBrain의 Developer Advocacy 팀의 총괄 리더라고 한다. 또한 [Google I/O 17 에서 코틀린을 소개](https://www.youtube.com/watch?v=X1RVYt2QKQE)하기도 했다. (~~히익~~) 이런 분을 몰라뵙고 굉장히 편한 마음으로 강의를 들었는데 생각보다 너무 많은 꿀팁을 방출해주셔서 너무 감사했다. 정식 강의 명칭은 "IntelliJ IDEA를 활용한 개발 노하우" 였다. 이 글에서는 해당 강의에서 소개한 팁중에서도 유용해 보였던 기능들을 정리해보았다. 

## 1. [Double Shift + \#]
![double_shift](/images/intellij/intellij-sharp.png){: width="500px"}
IntelliJ를 사용하면서 Double Shift를 사용하지 않는 사람은 거의 없을 것이다. 여기에 \#을 입력하면 안드로이드 스튜디오 설정에 관련된 것들만 모아서 볼 수 있다.
![double_shift2](/images/intellij/intellij-sharp2.png){: width="500px"}
그리고 그중 하나를 선택하면 바로 해당 설정을 On/Off 할 수 있는 화면이 된다.

## 2. [Shift + Command + A]
![action](/images/intellij/action.png){: width="500px"}
Action이나 Option을 찾아준다. menu bar에 등록할만큼은 아니지만 어느 정도 사용 빈도가 있는 기능들은 이 것을 이용하면 편할 듯 하다.

## 3. Keymap Abbreviation
![abbreviation](/images/intellij/abbreviation.png){: width="400px"}
![abbreviation2](/images/intellij/abbreviation2.png){: width="250px"}
IntelliJ에는 수 이미 많은 단축키(Keymap)가 있기 때문에 단축키 대신 축약어(Abbreviation)으로 등록돼있는 것들이 있다. 이 축약어들을 수정할 수 있고 직접 추가할 수도 있다. spvt로 축약어를 등록했다면 Search EveryWhere 에서 spvt를 입력해 해당 action이 나타나는 걸 확인 할 수 있다.

## 4. Completion
##### Smart Completion (^⇧Space)
![smartcompletion](/images/intellij/smartcompletion.png){: width="600px"}
IntelliJ의 가장 대표적인 단축키로 Type을 기반으로 상황에 맞는 자동 Completion을 제공해준다. 변수 할당, 초기화, 함수의 리턴 문, 메소드 arguments, chained expresion 등 대부분의 상황에서 사용 가능하다.
##### Hippie Completion 
![hippiecompletion](/images/intellij/hippiecompletion.png){: width="600px"}
현재 파일에 있는 symbol들을 차례대로 순회하며 추천해준다. Smart Completion에 비해 생소한 단축키지만 자동 완성 제안 범위를 현재 파일로 좁히고 싶을땐 더 유용할 수 있다.