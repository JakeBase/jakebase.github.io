---
layout: post
title: "안드로이드 Systrace"
slug: "android-systrace"
date: 2017-04-19 21:00:00 +0900
categories: blog
---
# Android Systrace

다양한 안드로이드 Profiling Tool중에 오늘은 Systrace에 대해서 알아보려고 합니다.

이미 잘 사용하고 계시겠지만 최근 Systrace를 이용해 Jank가 발생하는 부분을 발견하고 수정한(다행히도 배포 전에..) 경험이 있어 함께 공유하고자 합니다.

구글I/O 2015에서 구글은 Systrace의 업데이트를 발표했는데요, 이 업데이트부터 Systrace는 화면에 그려지는 Frame에 대한 지연 정보를 포함하여 더 쉽게 UI문제를 찾아낼 수 있게 됐습니다.

그 외에도 CPU usage, threads, ui renderer 등에 다양한 분석 정보를 보여주고 있습니다.

## 시작하기

1. Android Device Monitor 실행<br>
<img src="https://developer.android.com/images/tools/performance/systrace/gettingstarted_image001.png">

2. DDMS - 디버깅할 앱 선택 - Systrace 버튼 클릭<br>
<img src="https://developer.android.com/images/tools/performance/systrace/gettingstarted_image003.png">

3. trace setting 설정
  - 분석할 항목, 시간, 버퍼 사이즈 파일 명들을 입력하고 ok를 누르면 trace가 시작됩니다.<br>
<img src="https://developer.android.com/images/tools/performance/systrace/gettingstarted_image004.png">


## 분석하기

trace가 끝나면 setting에서 지정한 경로에 html파일이 저장되는데 이파일을 열면 trace정보가 나옵니다.

대략 아래 그림과 같은 식으로 trace정보가 나타납니다.

<img src="https://developer.android.com/images/systrace/overview.png">

중요한 부분만 몇가지 살펴 보면,

#### Alerts

분석결과를 토대로 Performance에 문제가 생길만한 부분을 체크해서 알려줍니다. Alerts에 표시된 아이콘을 클릭하면 어느 부분에서 문제가 생겼는지, 어떤 부분이 문제인지, 그리고 그와 관련된 Android Performance Pattern 영상을 추천해줍니다.
![alerts]({{ site.url }}/images/systrace/alerts.png)

#### Frames

Frame drop이 발생하는 부분에 대해서 색상으로 알려줍니다. Frame drop에 영향을 주준 부분은 아래 항목들을 통해 알 수 있습니다.
![frames]({{ site.url }}/images/systrace/alerts.png)

#### 단축키

보기는 좋지만 생각보다 조작이 불편합니다;; 그래서 단축키가 매우 중요합니다.

아래 그림의 키는 마우스 드래그에 동작을 정하는 키입니다. 순서대로 선택, 이동, 확대, 구간선택을 나타내고 키보드 1,2,3,4로 선택할 수 있습니다.

<img src="{{ site.url }}/images/systrace/shortcut.png" width="200">

| 키          | 기능                                                      |
|-------------|-----------------------------------------------------------|
| w           | trace timeline 확대                                       |
| s           | trace timeline 축소                                       |
| a           | 왼쪽으로 이동                                             |
| d           | 오른쪽으로 이동                                           |
| e           | timeline을 마우스 위치로 이동(그런데 d키와 동일하게 작동) |
| g           | 선택한 task 시작 부분에 그리드 표시                       |
| Shift+g     | 선택한 task 끝 부분에 그리드 표시                         |
| Right Arrow | 선택한 타임라인의 다음 이벤트 선택                        |
| Left Arrow  | 선택한 타임라인의 이전 이벤트 선택                        |


## 성능 개선 사례

최근 뮤직앱 개편 작업을 하면서 잠금화면에서 곡 전환시 있던 delay time을 없앴는데요, 기존에는 다음곡 버튼을 누르면 500ms 후에 화면 전환이 이루어졌는데 이 delay를 없애면서 화면 곡 변경시 일어나는 이벤트(재생 곡 변경, 배경 색 변경 등) 가 연속으로 발생할 수 있게 되면서 기존에는 느낄 수 없던 성능상 문제가 눈에 띄게 되었습니다.

그래서 Systrace를 사용해봤습니다. trace중 동작은 잠금화면의 다음곡 버튼을 빠르게 연속해서 누르는 것이었습니다.

### 개선 전 trace log

결과는 제가 느끼는 것보다 심각했습니다. frame drop을 나타내는 빨간 F 아이콘이 한눈에 봐도 너무 많았습니다.

![frames]({{ site.url }}/images/systrace/trace1.png)

### 뭐가 문제일까?

trace log를 살펴보고 두가지 문제점을 발견했고 이를 수정했습니다.

 - 어디선가 거대한? blocking이 걸리고 있었음

![blocking]({{ site.url }}/images/systrace/blocking.png)

playlist.PlayListItem 클래스에서 blocking이 발생하고 있었습니다. 위 blocking 정보에서 힌트를 얻어 다음곡 버튼을 눌렀을 때 호출되는 코드중 lock을 사용하는 코드가 있는지 살펴봤습니다.

```java
public List<PlayListItem> getItems(boolean shuffled, boolean ordered) {
    ...
    List<PlayListItem> items = PlayListManager.getInstance().getFilteredPlayListItems(shuffled);
    ...
    synchronized (mLock) {
        int size = items.size();
        for (int i = 0; i < size; i++) {
            PlayListItem track = items.get(i);
            trackList.add(new PlayListItem(track));
        }
        return trackList;
    }
    ...
}
```

다음 곡 버튼에 다음 곡을 미리 load하는 logic이 포함되어 있어 최종적으로 으로 위의 lock을 거는 method를 다음곡을 누를때마다 호출하고 있었습니다. trackList가 길어질 수록 blocking 시간도 길어지는데 이를 연속적으로 호출하면 당연히 느려질 수밖에 없습니다. 다음곡 버튼의 delay를 없앨 때 당연히 고려했어야 하는 부분인데 이부분을 신경쓰지 못했습니다.

- Animation이 중첩되고 있었음

아래 이미지는 위의 blocking 문제를 수정하고 난 뒤의 trace log입니다. 위처럼 blocking 관련된 지표는 나타나지 않았지만 frame에서는 여전히 노란색 frame들이 많이 나타나고 있었습니다(빨간색은 많이 없어졌네요!).

![anim]({{ site.url }}/images/systrace/anim.png)

문제는 Animation의 중첩이었습니다. 곡 전환시 배경색이 변하는 Animation이 있는데 300ms 짜리 Animation이라 곡전환 delay가 있을 때는 문제가 없었지만 delay가 없는 상황에서는 Animation이 중첩 실행될 경우를 고려했어야 했습니다.

```java
if(mObjectAnimator != null && mObjectAnimator.isRunning()){
        mObjectAnimator.cancel();
}
mObjectAnimator = ObjectAnimator.ofObject(mAlbumBGView, "backgroundColor", new ArgbEvaluator(), lastColor, newColor);
mObjectAnimator.setDuration(300);
mObjectAnimator.start();
```

Animation이 이미 실행중이면 취소하는 위 코드를 추가했고 아래와 같은 trace log 를 얻을 수 있었습니다. 노란색 Frame 아이콘도 많이 사라진 것을 확인 할 수 있습니다.

![anim_improved]({{ site.url }}/images/systrace/anim_improved.png)

최종적으로 Alert, Frame에서 다음과 같은 결과를 얻었습니다. 1초 단위로 확대한 화면입니다. 처음과 비교해보면 크게 개선이 됐습니다.

![result1]({{ site.url }}/images/systrace/result_alert.png)

![result2]({{ site.url }}/images/systrace/result_frame.png)


## 마치며

systrace를 이용해 놓쳤던 부분들을 찾고 수정하면서 프로파일링의 중요성을 다시 깨달았습니다 ㅎㅎ. Colt McAnlis 아저씨가 systrace를 계속 강조한 이유를 알것같습니다. 

## Reference
- https://developer.android.com/studio/profile/systrace-commandline.html
- https://developer.android.com/studio/profile/systrace.html
- http://lunchtimecoding.tistory.com/4