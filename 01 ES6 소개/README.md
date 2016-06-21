# 1. About ECMAScript 6 (ES6)
# 1. ECMAScript 6에 대하여 (ES6)

It took a long time to finish it, but ECMAScript 6, the next version of JavaScript, is finally a reality:

끝내기 위해 긴 시간 걸렸지만, 자바스크립트 다음 버전인 ECMAScript 6은 마침내 현실이 되었다:
* It became a standard on 17 June 2015.
* Its features are slowly appearing in JavaScript engines (as documented in kangax’ ES6 compatibility table).
* Transpilers (such as Babel and Traceur) let you compile ES6 to ES5.
* 이것은 2015년 6월 17일 표준이 되었다.
* 그 기능은 천천히 자바스크립트 엔진에서 나타나고 있다(kangax의 ES6 호환 테이블 문서 에서). 
* 트렌스파일러(바벨, Traceur)은 ES5코드를 ES6으로 컴파일 해준다.

The next sections explain concepts that are important in the world of ES6.

다음 절은 ES6 세계에서 중요한 개념을 설명한다.

## 1.1 TC39 (Ecma Technical Committee 39)
## 1.1 TC39 (Ecma 기술 위원회 39)
TC39 (Ecma Technical Committee 39) is the committee that evolves JavaScript. Its members are companies (among others, all major browser vendors). TC39 meets regularly, its meetings are attended by delegates that members send and by invited experts. Minutes of the meetings are available online and give you a good idea of how TC39 works.

TC39 (Ecma Technical Committee 39)은 자바스크립트를 발전시키는 위원회이다. 주요 브라우져 벤더들이 위원회 회원이다. TC39는 정기적으로 모이고, 회원들이 보낸 대리인들과 초대된 전문가들이 참석한다. 회의록은 온라인에서 볼 수 있고, TC39 일하는 방식에 대하여 알려준다.

## 1.2 How ECMAScript 6 was designed
## 1.2 ECMAScript 6은 어떻게 설계 되었나
The ECMAScript 6 design process centers on proposals for features. Proposals are often triggered by suggestions from the developer community. To avoid design by committee, proposals are maintained by champions (1–2 committee delegates).

ECMAScript 6 설계의 과정은 기능대한 제안이 중심이다. 제안들은 대개 개발자 커뮤니티 추천으로터 부터 시작된다. 의원회에 의한 설계를 피하기 위해서 제안은 챔피언(1~2명 의원회 대표)들을 통해 관리 된다.

A proposal goes through the following steps before it becomes a standard:

제안은 표준이 되기 전에 다음 단계를 겪는다.
* Sketch (informally: “strawman proposal”): A first description of the proposed feature.
* Proposal: If TC39 agrees that a feature is important, it gets promoted to official proposal status. That does not guarantee it will  become a standard, but it considerably increases its chances. The deadline for ES6 proposals was May 2011, no major new proposals were considered after that.
* Implementations: Proposed features must be implemented, ideally in two JavaScript engines. Implementations and feedback from the community shape the proposal as it evolves.
* Standard: If the proposal continues to prove itself and is accepted by TC39, it will eventually be included in an edition of the ECMAScript standard. At this point, it is a standard feature.
* 초고 Sketch (비공식적: "스트로우맨strawman 제안"): 제안된 기능의 처음 기술
https://en.wikipedia.org/wiki/Straw_man
* 제안 Proposal: TC39가 기능이 중요하다고 동의하면, 공식 제안 상태가 된다. 공식 제안은 표준이 된다는것을 보장하지 않지만 이 제안은 큰 가능성이 있다. ES6 제안의 마지막 기한은 2011년 5월 이였다. 그 이후에 더 중요한 제안은 고려되지 않았다.
* 구현 Implementations: 제안된 기능은 이상적으로 두 자바스크립트 엔진에서 반드시 구현되어야 한다. 구현과 커뮤니티로부터의 피드백은 제안을 진화시키면서 다듬는다.
* 표준 Standard: 제안이 증명되고 TC39가 받아들이면, 마침내 ECMAScript 표준 판으로 추가된다. 이 시점에서 제안은 표준기능이다.

[Source of this section: “The Harmony Process” by David Herman.]
[이 절의 원문: "The Harmony Process" 다비드허만]

### 1.2.1 The design process after ES6
### 1.2.1 ES6이후 설계 절차
Starting with ECMAScript 7 (whose official name is ECMAScript 2016), TC39 will time-box releases. The plan is to release a new version of ECMAScript every year, with whatever features are ready at that time. That means that from now on, ECMAScript versions will be relatively small upgrades.

ESMAScript 7(공식명칭은 ECMAScript 2016) 부터 TC39는 일정 주기로 공개할 것이다. 계획은 새로운 ECMAScript 버전을 매년 그때 준비된 기능과 함께 공개하는 것이다. 이 말은 이제부터는 ECMAScript 버전들은 비교적 작은 개선될 것을 의미 한다.

Work on ECMAScript 2016 (and later) has already begun, current proposals are listed on GitHub. The process has changed, too, and is described in a TC39 process document.

ECMAScript 2016(및 그 이후 버전)에 대한 작업은 이미 시작되었고, 현재 제안은 깃헙에 정리 되어 있다. 그 절차는 변경 되고 있으며, TC39 절차 문서에 기술되고 있다.

## 1.3 JavaScript versus ECMAScript
## 1.3 자바스크립트 대 ECMAScript
JavaScript is what everyone calls the language, but that name is trademarked (by Oracle, which inherited the trademark from Sun). Therefore, the official name of JavaScript is ECMAScript. That name comes from the standards organization Ecma, which manages the language standard. Since ECMAScript’s inception, the name of the organization has changed from the acronym “ECMA” to the proper name “Ecma”.

자바스크립트는 누구나 프로그램 언어로 생각하지만, 그 이름은 상표(오라클에 의해, sun으로 부터 받은 상표)다. 따라서, 공식적인 자바스크립트 이름은 ECMAscript이다. 이 이름은 언어의 표준을 관리하는 조직인 Ecma로 부터 가져 왔다. ECMAscript 시작 이후로 조직의 이름은 약어 "ECMA"로 부터 고유명사인 "Ecma"로 변경되었다.

Versions of JavaScript are defined by specifications that carry the official name of the language. Hence, the first standard version of JavaScript is ECMAScript 1 which is short for “ECMAScript Language Specification, Edition 1”. ECMAScript x is often abbreviated ESx.

자바스크립트 버전은 언어의 공식적 이름을 지닌 사양에 의해서 정의 된다. 따라서 초기 자바스크립트 표준 버전은 "ECMAScript Language Specification, Edition 1"를 줄여서 ECMAScript 1이다. ECMAScript X는 때때로 ESX로 줄여 부른다.

## 1.4 Upgrading to ES6
## 1.4 ES6로 업그레이드
The stake holders on the web are:

* Implementors of JavaScript engines
* Developers of web applications
* Users

웹 이해 관계자들:
* 자바스크립트 엔진 개발자
* 웹 애플리케이션 개발자
* 사용자

These groups have remarkably little control over each other. That’s why upgrading a web language is so challenging.

이 그룹들은 서로 매우 적게 제어를 갖는다. 이것이 바로 왜 웹 언어 업그레이드가 매우 도전적인 이유이다.

On one hand, upgrading engines is challenging, because they are confronted with all kinds of code on the web, some of which is very old. You also want engine upgrades to be automatic and unnoticeable for users. Therefore, ES6 is a superset of ES5, nothing is removed1. ES6 upgrades the language without introducing versions or modes. It even manages to make strict mode the de-facto default (via modules), without increasing the rift between it and sloppy mode. The approach that was taken is called “One JavaScript” and explained in a separate chapter.

한편 엔진 업그래이드는 모든 종류의 웹상에서의 코드와 매우 오래된 코드와 맞닥뜨리기 때문에 엔진을 업그레이드하는 것은 도전적이다. 또한 자연스럽고 사용자 모르게 엔진 업그레이드 되길 원한다. 따라서 ES6은 ES5의 상위 집합으로 아무것도 제거 된 것이 없다. ES6는 언어를 버전이나 모드를 도입하지 않고 업그레이드 한다. 이것은 심지어 슬리피 모드와의 틈을 벌리지 않고, 스트릭트 모드를 사실상 기본(모듈을 통한)으로 관리 한다. 이 "하나의 자바스크립트"로 불리는 접근는 별도에 3장에서 설명하겠다.

On the other hand, upgrading code is challenging, because your code must run on all JavaScript engines that are used by your target audience. Therefore, if you want to use ES6 in your code, you only have two choices: You can either wait until no one in your target audience uses a non-ES6 engine, anymore. That will take years; mainstream audiences were at that point w.r.t. ES5 when ES6 became a standard in June 2015. And ES5 was standardized in December 2009! Or you can compile ES6 to ES5 and use it now. More information on how to do that is given in the book “Setting up ES6”, which is free to read online.

다른 한편 코드는 반드시 목표 사용자가 사용하는 모든 자바스크립트 엔진에서 돌아야 하기 때문에 코드를 업그레이드 하는 것은 도전적이다. 따라서 ES6을 코드에서 사용하길 원한다면 오직 두 가지 선택 밖에 없다. 목표 사용자가 ES6를 지원하지 않는 엔진을 더이상 사용하지 않을 때까지 기다릴 수 있다. 이것은 수년 걸릴 것이다. ES6이 2015년 6월에 표준이 되었을 때 주요 사용자는 ES5를 사용하였다. 그리고 ES5는 2009년 11월 표준화 되었고, ES6을 ES5로 컴파일하고 사용 할 수 있다. 이 작업에 대한 자세한 방법은 "Setting up ES6"에 나와 있고 이것은 온라인에서 공짜로 볼 수 있다.

Goals and requirements clash in the design of ES6:
* Goals are fixing JavaScript’s pitfalls and adding new features.
* Requirements are that both need to be done without breaking existing code and without changing the lightweight nature of the language.

ES6 설계에서 목표와 요구사항의 충돌
* 목표는 자바스크립트의 함정을 수정하고 새로운 기능을 추가한다.
* 요구 사항은 존재하는 코드의 깨짐이나 언어의 경량특성을 변경 없이 되는 것 둘 다 이다. 

## 1.5 Goals for ES6
## 1.5 ES6의 목표
The original project page for Harmony/ES6 includes several goals. In the following subsections, I’m taking a look at some of them.

하모니/ES6의 원래 프로젝트 페이지는 몇개 목표를 가진다. 다음 소단원에서 목표를 설명하겠다.

### 1.5.1 Goal: Be a better language
### 1.5.1 목표: 더 나은 언어가 되기

The goal is: Be a better language for writing:

1. complex applications;
2. libraries (possibly including the DOM) shared by those applications;
3. code generators targeting the new edition.

목표는 사용하기 더 나은 언어가 되는 것 이다:

1. 복잡한 어플리케이션;
2. 애플리케이션에 의해 공유된 라이브러리(아마도 DOM을 포함);
3. 새 판을 겨냥한 코드 생성기.

Sub-goal (i) acknowledges that applications written in JavaScript have grown huge. A key ES6 feature fulfilling this goal is built-in modules.

소 목적 (1) 자바스크립트를 사용한 애플리케이션 사례는 크게 증가되었다. 목표를 실현하는 ES6 기능 중 열쇠는 내장 모듈이다.

Modules are also an answer to goal (ii). As an aside, the DOM is notoriously difficult to implement in JavaScript. ES6 Proxies should help here (as described in a separate chapter).

모듈들은 또한 목표(2)의 답이 된다. 여담으로 DOM은 자바스크립트에서 구현 어렵기로 악명이 높다. ES6 프록시는 여기서 도움을 준다(28장에서 설명).

Several features were specifically added not to improve JavaScript, but to make it easier to compile to JavaScript. Two examples are:
* Math.fround() – rounding Numbers to 32 bit floats
* Math.imul() – multiplying two 32 bit ints

몇개의 기능은 자바스크립트 개선이 아닌 특별하게 추가 되었으나, 이 기능은 자바스크립트로 컴파일을 쉽게 해준다. 두 예제 보면:
* Math.fround() – 32비트 부동소수점으로 반올림한다.
* Math.imul() – 32비트 정수을 곱한다.

They are both useful for, e.g., compiling C/C++ to JavaScript via Emscripten.

이 기능은 예를 들면 C/C++을 Emscripten을 통해 자바스크립트로 컴파일 할 때 둘 다 유용하다. 

### 1.5.2 Goal: Improve interoperation
### 1.5.2 목표: 상호 운영 개선 
The goal is: Improve interoperation, adopting de facto standards where possible.

이 목표는 상호운용 개선, 가능한 사실상의 표준을 채택이다.

Examples are:

* Classes: are based on how constructor functions are currently used.
* Modules: picked up design ideas from the CommonJS module format.
* Arrow functions: have syntax that is borrowed from CoffeeScript.
* Named function parameters: There is no built-in support for named parameters. Instead, the existing practice of naming parameters via object literals is supported via destructuring in parameter definitions.

예를 들면:

* 클래스: 는 생성자 함수들이 현재 사용되는 방법을 기반으로 한다.
* 모듈: 은 CommonJS 모델 포멧으로 부터 설계 아이디어를 가져왔다.
* 애로우 함수: 은 CoffeeScript로 부터 빌려온 문법을 가진다.
* 기명 함수 파라미터: 기명 파라미터에 대한 지원이 내장되어 있지 않다. 대신, 객체 리터럴를 통한 기명 파라미터로 사용하는 기존 방법은 파라미터 정의 시 해체를 통해 제공 할 수 있다.

### 1.5.3 Goal: Versioning
### 1.5.3 목표: 버져닝
The goal is: Keep versioning as simple and linear as possible.

목표는: 가능한 간단하고 선형적으로 버저닝을 유지 하는 것 이다.

As mentioned previously, ES6 avoids versioning via “One JavaScript”: In an ES6 code base, everything is ES6, there are no parts that are ES5-specific.

이전에 말했듯이, ES6은 "하나의 자바스크립트"를 통해 버저닝을 피한다: ES6코드 기반에서, 모든것은 ES6이고, ES5 특화된 부분은 아무것도 없다.

## 1.6 Categories of ES6 features
## 1.6 ES6기능의 종류
The introduction of the ES6 specification lists all new features:

ECMAScript 6의 새로운 기능 사양 목록 소개:

Some of [ECMAScript 6’s] major enhancements include modules, class declarations, lexical block scoping, iterators and generators, promises for asynchronous programming, destructuring patterns, and proper tail calls. The ECMAScript library of built-ins has been expanded to support additional data abstractions including maps, sets, and arrays of binary numeric values as well as additional support for Unicode supplemental characters in strings and regular expressions. The built-ins are now extensible via subclassing.

모듈, 클래식 정의, 어휘적 블록 스코프, 이터레이터와 제너레이터, 비동기 프로그래밍을 위한 프로미스, 해체 패턴와 꼬리 재귀는 ECMAScript 6의 주요 개선의 일부로 포함된다. 내장된 ECMAScript 라이브러리는 맵, 셋, 이진 숫자 값의 배열뿐만 아니라 문자열과 정규표현식에서 유니코드 보조문자 대한 추가 지원을 포함한 추가 데이터 추상화를 지원하도록 확장되었다. 현재 내장 모듈은 서브클래스를 통해 확장 가능하다.

There are three major categories of features:

* Better syntax for features that already exist (e.g. via libraries). For example:
   * Classes
   * Modules
* New functionality in the standard library. For example:
   * New methods for strings and Arrays
   * Promises
   * Maps, Sets
* Completely new features. For example:
   * Generators
   * Proxies
   * WeakMaps

세가지 주요 기능의 기능 그룹:

* 이미 존재하는 기능을 위한 더 나은 문법 (예: 라이브러리를 통해). 예를 들면:
   * Classes
   * Modules
* 표준 라이브러리의 새로운 기능. 예를 들면:
   * 문자열, 배열을 위한 새로운 메소드
   * 프로미스
   * 맵, 셋
* 완전한 새로운 기능. 예를 들면:
   * 제너레이터
   * 프록시
   * 위크맵
  
## 1.7 A brief history of ECMAScript
## 1.7 ECMAScript의 역사 요약
This section describes what happened on the road to ECMAScript 6.

이 절은 ECMAScript 6으로 가는길에서 무슨일이 있었는지 설명한다.

### 1.7.1 The early years: ECMAScript 1–3
### 1.7.1 초기: ECMAScript 1-3
* ECMAScript 1 (June 1997) was the first version of the JavaScript language standard.
* ECMAScript 2 (June 1998) contained minor changes, to keep the spec in sync with a separate ISO standard for JavaScript.
* ECMAScript 3 (December 1999) introduced many features that have become popular parts of the language2:
* ECMAScript 1 (1997.06) 자바스크립트 언어 표준의 첫번째 버전이다.
* ECMAScript 2 (1998.06) 자바스크립트에 대한 별도의 ISO 표준과 동기화 사양을 유지하기 위해, 작은 변화를 포함하였다.
* ECMAScript 3 (1999.12) 은 언어의 인기있는 부분이 될 많은 기능을 도입 하였다.

### 1.7.2 ECMAScript 4 (abandoned in July 2008) 
### 1.7.2 ECMAScript 4 (2008.07 폐기됨)
Work on ES4 started after the release of ES3 in 1999. In 2003, an interim report was released after which work on ES4 paused. Subsets of the language described in the interim report were implemented by Adobe (in ActionScript) and by Microsoft (in JScript.NET).

ES4의 작업은 ES3이 배포된 1999 이후에 시작되었다. 2003년 중간보고서가 배포된 후에 ES4 작업은 중단되었다. 중간 보고서에 기술된 언어의 일부분은 어도비(액션스크립트)와 마이크로소프트(JScript.NET)에 의해 구현되었다.

In February 2005, Jesse James Garrett observed that new techniques had become popular for implementing dynamic frontend apps in JavaScript. He called those techniques Ajax. Ajax enabled a completely new class of web apps and led to a surge of interest in JavaScript.

2005년 2월에 제시 제임스 개럿은 자바스크립트에서 동적 프론트 앱 구현에 대한 인기를 얻게 될 새로운 기술을 목격하게 된다. 그는 이 기술을 Ajax로 불렀다. Ajax는 완전하게 새로운 차원의 웹 앱 가능하게 하였고 자바스크립트에서 흥미의 파도를 이끌었다.

That may have contributed to TC39 resuming work on ES4 in fall 2005. They based ES4 on ES3, the interim ES4 report and experiences with ActionScript and JScript.NET.

아마 그것은 TC39가 2005년 가을에 ES4를 다시 시작하게 만들었다. 그들은 ES4를 ES3, ES4 중간 보고서, 액션 스크립트와 JScript.NET을 통한 경험을 기반으로 했다.

There were now two groups working on future ECMAScript versions:
미래 ECMAScript 버전 만드는 두 그룹이 있다.:

* ECMAScript 4 was designed by Adobe, Mozilla, Opera, and Google and was a massive upgrade. Its planned feature sets included:
   * Programming in the large (classes, interfaces, namespaces, packages, program units, optional type annotations, and optional static type checking and verification)
   * Evolutionary programming and scripting (structural types, duck typing, type definitions, and multimethods)
   * Data structure construction (parameterized types, getters and setters, and meta-level methods)
   * Control abstractions (proper tail calls, iterators, and generators)
   * Introspection (type meta-objects and stack marks)
* ECMAScript 3.1 was designed by Microsoft and Yahoo. It was planned as a subset of ES4 and an incremental upgrade of ECMAScript 3, with bug fixes and minor new features. ECMAScript 3.1 eventually became ECMAScript 5.

* ECMAScript 4는 어도비, 모질라, 오페라, 구글에 의해서 설계 되었고, 대규모로 업그레이드 되었다. 이 계획의 기능들은 다음을 포함한다:
   * 큰 (클래스, 인터페이스, 네임스페이스, 패키지, 프로그램 유닛, 옵션형 어노테이션, 옵션형 스태틱 타입, 체크, 확인) 프로그래밍
   * 진화적 프로그래밍과 스크립팅 (구조상의 타입, 덕 타입, 다입 정의, 멀티메소드)
   * 자료구조 생성자 (매개변수 타입, 겟터와 셋터, 메타레벨 메소드)
   * 제어 추상 (꼬리 재귀 선호, 반복자, 제너레이터)
   * Introspection(리플랙션)(메타 객체 타입, 스택 마크)
* ECMAScript 3.1은 마이크로소프트와 야후에 의해 설계되었다. 그것은 ES4의 부분집합으로, ECMAScript 3 점진적인 업그레이드, 버그 수정과 약간의 새 기능을 계획 하였다. ECMAScript3.1은 마침내 ECMAScript 5가 되었다.

The two groups disagreed on the future of JavaScript and tensions between them continued to increase.

두 그룹은 자바스크립트의 미래에 동의 하지 않았으며, 계속 둘 사이의 긴장은 증가되고 있다.

Sources of this section:

이 절의 원문:
* “Proposed ECMAScript 4th Edition – Language Overview”. 2007-10-23
* “ECMAScript Harmony” by John Resig. 2008-08-13

### 1.7.3 ECMAScript Harmony
### 1.7.3 ECMAScript Harmony
At the end of July 2008, there was a TC39 meeting in Oslo, whose outcome was described as follows by Brendan Eich:

2008년 7월말에 오슬로에서 TC39 모임이 있었고, 그 결론은 브랜트 아이크는 다음 같이 서술 했다:

It’s no secret that the JavaScript standards body, Ecma’s Technical Committee 39, has been split for over a year, with some members favoring ES4 […] and others advocating ES3.1 […]. Now, I’m happy to report, the split is over.

자바스크립트 표준의 핵심인 에크마 기술 의원회 39는 분할된 채로 수년간 지낸건 비밀이 아니다. 어떤 사람은 ES4를 좋아하고 다른 사람은 ES3.1을 옹호한다. 나는 즐겁게 말한다. 대립은 끝났다고.

The agreement that was worked out at the meeting consisted of four points

회의에서 합의된 4가지 동의

1. Develop an incremental update of ECMAScript (which became ECMAScript 5).
2. Develop a major new release, which was to be more modest than ECMAScript 4, but much larger in scope than the version after ECMAScript 3. This version was code-named Harmony, due to the nature of the meeting in which it was conceived.
3. Features from ECMAScript 4 that would be dropped: packages, namespaces, early binding.
4. Other ideas were to be developed in consensus with all of TC39.

1. ECMAScript의 점차적 업데이트로 개발(이것은 ECMAScript 5가 된다.)
2. ECMAScript4 보다 적당하지만 ECMAScript 3 이후 버전보다 더 큰 범위를 가지는 주요 배포판 개발. 태어날때의 회의의 성격에 때문에 버전은 코드 네임 하모니이다.
3. 제거될 ECMAScript4 기능: packages, namespace, early binding.
4. 다른 아이디어는 TC39의 모두의 동의 속에서 개발되어진다. 

Thus: The ES4 group agreed to make Harmony less radical than ES4, the rest of TC39 agreed to keep moving things forward.

따라서: ES4 그룹은 ES4보다 덜 급진적으로 만들길 동의하였고, EC39의 나머진 앞으로 나아가길 동의하였다.

The next versions of ECMAScript are:
* ECMAScript 5 (December 2009). This is the version of ECMAScript that most browsers support today. It brings several enhancements to the standard library and updated language semantics via a strict mode.
* ECMAScript 5.1 (June 2011). ES5 was submitted as an ISO standard. In the process, minor corrections were made. ES5.1 contains those corrections, it is the same text as ISO/IEC 16262:2011.
* ECMAScript 6 (June 2015). This version went through several name changes:
   * ECMAScript Harmony: was the initial code name for JavaScript improvements after ECMAScript 5.
   * ECMAScript.next: It became apparent that the plans for Harmony were too ambitious for a single version, so its features were split into two groups: The first group of features had highest priority and was to become the next version after ES5. The code name of that version was ECMAScript.next, to avoid prematurely comitting to a version number, which proved problematic with ES4. The second group of features had time until after ECMAScript.next.
    * ECMAScript 6: As ECMAScript.next matured, its code name was dropped and everybody started to call it ECMAScript 6.
    * ECMAScript 2015: In late 2014, TC39 decided to change the official name of ECMAScript 6 to ECMAScript 2015, in light of upcoming yearly spec releases. However, given how established the name “ECMAScript 6” already is and how late TC39 changed their minds, I expect that that’s how everybody will continue to refer to that version.
* ECMAScript 2016 was previously called ECMAScript 7. Starting with ES2016, the language standard will see smaller yearly releases.

ECMAScript의 다음버전은:
* ECMAScript 5 (2009.11). ECMAScript의 버전은 요즘 대부분의 브라우져가 지원하고 있다. 이것은 표준 라이브러리의 개선과 스트릭모드를 통한 언어 문법의 업데이트를 제공한다.
* ECMAScript 5.1 (2011.06). ES5는 IOS 표준으로 제출되었다. 그 과정에서 약간의 수정이 이루어졌다. ES5.1은 그 수정을 포함하고, 그것은 ISO/IEC 16262:2011과 같은 글이다.
* ECMAScript 6 (2015.06). 이 버전은 여러 이름 변화를 겪었다.:
   * ECMAScript Harmony: 는 ECMAScript 5 이후에 자바스크립트 개선의 첫 코드 이름이었다.
   * ECMAScript.next: 하모니를 위한 계획은 단일 버전으로 너무 거창한게 드러났고, 그래서 그 기능은 두 그룹으로 분리 되었다. 기능의 첫번째 그룹은 높은 우선순위를 갖으며, ES5다음 버전이 될 것이다. 이 버전의 코드 이름은 ECMAScript.next이다. ES4의 문제를 시험하기 위해서 먼저 버전 넘버를 기록하는것을 피하기 위해서다. 기능의 두번째 그룹은 ECMAScript.next이후부터의 시간을 가진다.
   * ECMAScript 6: ECMAScript.next가 성숙함으로써, 코드 이름은 버려지고 모두 ECMAScript 6으로 불르기 시작했다.
   * ECMAScript 2015: 2014 말에 TC39는 ECMAScript 6를 매년 작은 스펙의 배포를 위해 ECMAScript2015로 공식이름을 변경하는 것을 결정하였다. 그러나 어떻게 "ECMAScript 6" 이름이 정착된는지, 어떻게 TC39가 그들의 마음을 바꾼것을 고려한다면, 나는 어떻게 지속적으로 모든 사람이 버전을 참조하기를 예측한다.
* ECMAScript 2016은 이전에 ECMAScript7로 불렸다. ES2016 시작 이후로 이 언어표준은 소규모 연간 배포를 볼 수 있다.
