---
layout: post
title: 구글의 소프트웨어 엔지니어링
---

<div class="message">
Software Engineering at Google 논문을 읽고 내용 중 핵심적인 부분을 간추려 정리해본다.
</div>

*2017년 3월 10일 초안 작성*

## 내용

[Software Engineering at Google](https://arxiv.org/abs/1702.01715)이라는 논문을 읽었다. 재밌는 내용이 많다. 맨 먼저, 단 하나의 레파지토리. 크롬과 안드로이드는 규모가 크기 때문에 별도로 구성되어 있지만 이외에는 모두가 하나의 레파지토리로 되어 있다고. 9백만 소스 파일, 20억 라인, 3천5백만개의 커밋을 지닌 86TB 단일 레파지토리. 하루에도 4만 커밋이 올라온다고.

Blaze라는 분산 빌드 시스템도 인상적. 이건 Bazel이란 이름으로 오픈소스로 공개되어 있는데 내부에서는 Blaze라는 이름으로 사용 중인 재밌는 아나그램.[1] 당연히 코드 리뷰도 잘 갖춰져있다고 하며 아무나 리뷰할 수 있으나 리뷰 지연을 막기 위해 리뷰어를 지정할 수 있다고. 테스팅의 중요성은 말할 필요도 없고. 새로운 기능이 추가되면 반드시 커버하는 테스트를 작성해야 한다고.

[1]: https://www.facebook.com/likejazz/posts/10154870408045837?comment_id=10154876880415837&comment_tracking=%7B%22tn%22%3A%22R0%22%7D

### 언어
공식적으로 승인된 언어는 5개: **C++, Java, Python, Go, JavaScript**. 코드 재사용과 개발자간 협업을 위해 프로그래밍 언어는 위 5개로 제한할 것을 권장한다. 여기에는 스타일 가이드가 존재하고 코드 가독성을 높이기 위한 교육 과정도 있다고. 다른 언어간 통신은 당연히 Protocol Buffers 사용.

릴리즈는 가능한 자주. 릴리즈 작업은 대부분 자동화. 런치 전에는 개발팀 외부의 다수 승인이 필요함. 당연히 런치 프로세스를 위한 도구도 제공되고. 그리고 이게 중요한데 “Most software at Google gets rewritten every few years.” 끊임 없는 재작성. 재사용 보다 끊임없는 개선을 통한 재작성을 중요하게 여긴다.

### 잦은 재작성
“Most software at Google gets rewritten every few years.” 이 부분은 논문 2.11 챕터에 잘 나와 있는데 좀 더 살펴보면, **리팩토링이 아닌 완전한 재작성을 의미**한다. 당연히 엄청난 비용(incredibly costly)이 수반되는 점을 인정하고 시작한다. 그러나 구글 철학에 어울리는 결정적인 잇점들이 몇 가지 있는데,
1. 몇 년만 지나도 요구사항과 둘러싼 여러 기술들은 끊임없이 변한다.
1. 현재의 요구사항은 예전과 다르지만 시스템은 예전 요구사항에 적합한 방식으로 설계되어 있고, 그간 복잡성이 누적되어 있다. 재작성을 통해 누적된 복잡성을 제거한다. (사실 이 부분은 리팩토링도 동일하므로 재작성만의 장점으로는 볼 수 없음)
1. 이 부분이 재밌는데, 재작성은 새로운 팀 구성원에게 지식과 오너쉽(sense of ownership)을 부여하는 방법이다. 일반적으로 개발자는 자기가 직접 작성한 코드(“theirs"로 표현)에 애착을 갖기 때문에 이는 생산성에 매우 중요한 요인이다.  
아울러 빈번한 재작성을 통해 프로젝트간 엔지니어 이동을 장려한다. 트랜스퍼를 희망하는 엔지니어에게 리팩토링만 할 수 있는 팀, 원하는대로 새로 만들 수 있게 하는 팀. 이 중 어느 팀을 택할지는 뻔한 이야기. 특히 구글처럼 그런걸 장려하는 문화라면 더더욱. 결국 이 부분이 재작성의 가장 큰 장점이라는 생각.
1. 빈번한 재작성을 통해 최신 기술 및 방법론 적용. 이 부분은 1번과 비슷.

사실 나도 오래전부터 비슷한 생각을 해왔다. 항상 새로 만드는게 좋다고 생각해왔는데, 그 이유는 기술은 끊임없이 발전하므로 새로 만드는 것은 항상 가장 발전된 기술을 사용할 수 있기 때문이다. 물론 기술의 발전이 언제나 만능은 아니다. 일부는 실패할 수 있고 더 나빠질 수도 있다. 그러나 실패 또한 발판으로 삼아 기술을 발전시키면 그 다음 프로젝트는 더 나아질 수 있다. 물론 재작성에는 언제나 엄청난 비용이 수반되고 사람들은 실패에 대한 두려움이 있기 때문에 이 부분을 설득하는게 쉽지 않았는데, 구글이 같은 일들을 해오고 있다니 매우 큰 위안이 된다.

### 프로젝트 승인
또 한 가지 재밌는 점은 구글에는 런치 승인에 관해서는 잘 정리된 프로스세가 있지만 프로젝트 승인/취소에 관한 프로세스는 없다고. 심지어 논문 작성자는 구글에서 10년을 근무하고 매니저가 됐지만 아직도 의사 결정이 어떻게 이뤄지는지 완전히 이해하지 못한다고. 이 말은 조직별 프로젝트 진행을 각 조직의 재량에 맡긴다는 의미. 때로는 진행할 프로젝트를 팀 내에서 자유롭게 결정하는 bottom-up 방식으로 진행.

### 피플 매니지먼트
엔지니어링 업무 롤에는 technical leadership과 people management가 구분된다고. 커리어 패스도 엔지니어링과 매니지먼트로 구분. 아울러 조직간 트랜스퍼 장려. 조직간 지식과 기술을 전파하고 커뮤니케이션을 원할히 하는데 도움. 협업해야할 다른 팀이 우리 팀원 중 누군가 이전에 있었던 팀이라면 그 사람을 통해 아무래도 훨씬 더 부드러운 진행이 가능하다.

그렇다고 몇 달 안됐는데 마음대로 트랜스퍼 하는건 아니고 조직간 트랜스퍼는 해당 팀에서 12개월 후 좋은 평가를 받았을때 허용. 아울러 소프트웨어 엔지니어에게 다른 직군 임시 근무 장려. 예를 들어 사이트 구성 엔지니어(Site Reliability Engineering, SRE)로 6개월간 순환(rotation) 근무 등등.

## 정리

사실 어떻게 보면 반드시 구글이 아니더라도 상기 내용들은 이제는 대부분의 기업에서 잘 알고 있고 실천하고 있는 부분이다. 하지만 지금까지 저런 문화를 선도해왔고 또 이렇게 논문을 통해 공유하는 문화는 역시 구글이 가장 앞서 있다는 생각. 우리의 경우 별 것 아닌 문화나 사소한 코드 마저도 지나치게 보안을 강조하는 측면이 있다. 이제는 지나친 보안 강조는 자신이 없다는 얘기로 들린다. 결국은 가장 자신 있고 당당한 사람이 공개하게 되어 있으며, 공개 할수록 영향력은 강화된다.