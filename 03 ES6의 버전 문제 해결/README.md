## 3. 단일 자바스크립트 : ECMAScript 6에서 버저닝을 피하기(One JavaScript: avoiding versioning in ECMAScript 6)

What is the best way to add new features to a language? This chapter describes the approach taken by ECMAScript 6. It is called _One JavaScript_, because it avoids versioning.

언어에 새로운 기능을 추가하는 가장 좋은 방법은 무엇일까? 3장에서는 ECMAScript6에 다가가는 법을 설명한다. 버저닝(versioning)을 피해야 하기 때문에 이를 _단일 자바스크립트(One JavaScript)_라고 한다. 

### 3.1 버저닝(Versioning)
In principle, a new version of a language is a chance to clean it up, by removing outdated features or by changing how features work. That means that new code doesn’t work in older implementations of the language and that old code doesn’t work in a new implementation. Each piece of code is linked to a specific version of the language. Two approaches are common for dealing with versions being different.

원칙적으로 한 언어의 새로운 버전은 오래된 기능을 제거하거나, 동작 방식을 변경함으로써 언어를 정비할 수 있는 기회가 된다. 이는 곧 새로운 코드가 해당 언어의 옛 구현체에서는 동작하지 않거나, 오래된 코드가 새로운 구현체에서 동작하지 않게 됨을 의미한다. 각각의 코드 조각들은 언어의 특정 버전과 연결되어 있다. 서로 다른 버전을 다루는 데는 흔히 두 가지 접근법이 사용된다.

First, you can take an “all or nothing” approach and demand that, if a code base wants to use the new version, it must be upgraded completely. Python took that approach when upgrading from Python 2 to Python 3. A problem with it is that it may not be feasible to migrate all of an existing code base at once, especially if it is large. Furthermore, the approach is not an option for the web, where you’ll always have old code and where JavaScript engines are updated automatically.

첫째, "전부 아니면 아무것도(all or nothing)" 전략을 선택할 수 있다. 만약 코드 베이스(code base)를 새로운 버전으로 사용하기 원한다면 완벽한 업그레이드가 필요하다. 파이썬은 2에서 3으로 버전을 올릴 때 이 방법을 사용했다. 이 방법이 가진 문제점 중 하나는 현실적으로 코드 베이스의 모든 것을 한번에 통합하는 것이 불가능 하다는 것이다. 특히 규모가 크다면 더더욱 어렵다. 더군다나 언제나 오래된 코드가 존재하고, 자바스크립트 엔진이 자동으로 업데이트되는 웹에서는 선택할 수 있는 방법이 아니다.

Second, you can permit a code base to contain code in multiple versions, by tagging code with versions. On the web, you could tag ECMAScript 6 code via a dedicated Internet media type. Such a media type can be associated with a file via an HTTP header:

둘째, 코드에 버전을 명시함으로써 여러가지 버전의 코드를 포함하는 코드베이스를 허용할 수도 있다. 웹에서는 전용 (인터넷 미디어 타입)[http://en.wikipedia.org/wiki/Internet_media_type] 을 통한 ECMAScript 6 코드를 태그할 수도 있다. 어떤 미디어 타입은 HTTP 해더를 통해 파일과 연결시킬 수 있다.

```
Content-Type: application/ecmascript;version=6
```

It can also be associated via the type attribute of the `<script>` element (whose default value is text/javascript):

또한 `<script>` 요소( (기본 값)[http://www.w3.org/TR/html5/scripting-1.html#attr-script-type] 은 text/javascript 이다)에 연결 시킬 수도 있다.

```
<script type="application/ecmascript;version=6">
    ···
</script>
```

This specifies the version out of band, externally to the actual content. Another option is to specify the version inside the content (in-band). For example, by starting a file with the following line:

이것은 실제 컨텐츠의 외부에(out of band) 버전을 따로 명시하는 것이다. 또 다른 옵션은 컨텐츠 내부에 버전을 명시하는 것이다.(in-band). 예를 들어 다음과 같이 파일을 시작하면 된다.

```
use version 6;
```

Both ways of tagging are problematic: out-of-band versions are brittle and can get lost, in-band versions add clutter to code.

두 방법 모두 문제가 있는데, 외부에 버전을 표시하는 방법은 다루기 힘들고 유실될 염려가 있다. 내부에 표시하는 방법은 코드를 어지럽힌다.

A more fundamental issue is that allowing multiple versions per code base effectively forks a language into sub-languages that have to be maintained in parallel. This causes problems:

또한 더욱 근본적인 문제는 코드 베이스마다 여러 버전을 허용하는 것은 한 언어를 동시에 유지 보수해야 하는 다른 하위-언어(sub-languages)로 효과적으로 포크할 수 있게 한다. 이는 다음과 같은 문제를 일으킨다.

* Engines become bloated, because they need to implement the semantics of all versions. The same applies to tools analyzing the language (e.g. style checkers such as JSLint).
* Programmers need to remember how the versions differ.
* Code becomes harder to refactor, because you need to take versions into consideration when you move pieces of code.

Therefore, versioning is something to avoid, especially for JavaScript and the web.

* 모든 버전의 구문을 해석할 수 있어야 하기 때문에 엔진이 비대해진다. 이는 언어 분석 도구도 마찬가지다. (예. JSLint 같은 스타일 점검 도구)
* 프로그래머는 버전별 차이점을 기억해야 한다.
* 코드 조각을 움직이는 순간 버전을 고려해야하기 때문에 코드 리팩토링이 어려워진다.

그러므로, 버저닝은 피해야 한다. 특히나 자바스크립트와 웹에서라면 더더욱 그러하다.

### 3.1.1 버저닝 없는 진화(Evolution without versioning)
But how can we get rid of versioning? By always being backwards-compatible. That means we must give up some of our ambitions w.r.t. cleaning up JavaScript: We can’t introduce breaking changes. Being backwards-compatible means not removing features and not changing features. The slogan for this principle is: “don’t break the web”.

그렇다면 어떻게 버저닝을 제거 할 수 있을까? 항상 이전 버전과 호환이 되야 하기 때문에, 우리는 자바스크립트를 정화하겠다는 야망을 포기해야만 한다. 즉, 주요 변경 사항을 도입할 수 없다는 것이다. 이전 버전과 호환이 된다는 것은 이전 버전의 기능을 변경하거나 없애지 않는 것을 의미한다. 이 원칙에 의거한 슬로건이 바로 "웹을 망가뜨리지 말라(don't break the web)"이다.

We can, however, add new features and make existing features more powerful.
하지만 우리는 새로운 기능을 추가하고, 기존의 기능을 더 강력하게 만들 수 있다.

As a consequence, no versions are needed for new engines, because they can still run all old code. David Herman calls this approach to avoiding versioning One JavaScript (1JS) [1], because it avoids splitting up JavaScript into different versions or modes. As we shall see later, 1JS even undoes some of a split that already exists, due to strict mode.

결론적으로, 엔진이 여전히 이전 버전의 모든 코드를 실행할 수 있기 때문에 새로운 엔진을 위한 버전은 필요가 없다. 데이비드 허만(David Herman) 은 버저닝을 피하기 위한 이러한 접근법을 '단일 자바스크립트((One JavaScript, 1JS)[http://exploringjs.com/es6/ch_one-javascript.html#one-js_1])[1]'라고 한다. 이는 자바스크립트가 다른 버전이나 다른 모드로 분열되는 것을 피할 수 있기 때문이다. 이후 다시 살펴보겠지만, 1JS는 strict 모드로 인해 이미 분리된 것을 다시 원상태로 복구하기도 한다.

One JavaScript does not mean that you have to completely give up on cleaning up the language. Instead of cleaning up existing features, you introduce new, clean, features. One example for that is let, which declares block-scoped variables and is an improved version of var. It does not, however, replace var, it exists alongside it, as the superior option.

단일 자바스크립트는 우리가 언어를 정돈하는 것을 완전히 포기해야 한다는 것을 뜻하지 않는다. 이미 존재하는 기능들을 없애는 대신에 새롭고, 말끔한 기능을 도입할 수 있다. 예를 들어 let은 블록 스코프 변수를 선언하는 기능을 하고 var의 향상된 버전이다. 그러나 let은 var를 대체하는 것이 아니라 더 상위 선택지로서 var와 나란히 존재한다.

One day, it may even be possible to eliminate features that nobody uses, anymore. Some of the ES6 features were designed by surveying JavaScript code on the web. Two examples are:

언젠가 더 이상 아무도 사용하지 않는 기능들은 제거 될 수도 있다. ES6의 기능 중 몇 가지는 웹에서 자바스크립트 코드를 조사하면서 고안되었다. 다음 두 예제를 보자.

    * let-declarations are difficult to add to non-strict mode, because let is not a reserved word in that mode. The only variant of let that looks like valid ES5 code is:
        ```javascript
          let[x] = arr;
        ```
    Research yielded that no code on the web uses a variable let in non-strict mode in this manner. That enabled TC39 to add let to non-strict mode. Details of how this was done are described later in this chapter.

    * `let` 선언은 non-strict 모드에 추가하기는 어렵다. 왜냐하면 non-strict 모드에서는 `let`이 에약어가 아니기 때문이다. `let`을 유효한 ES5 코드로 바꿔 쓰면 다음과 같다.
    
     ```javascript
          let[x] = arr;
    ```

    웹에서 조사해보니 non-strict 모드에서 이 방법으로 `let`을 사용한 코드는 찾아 볼 수 없었다. 그래서 TC39는 non-stric 모드에 `let`을 추가할 수 있었다. 자세한 방법은 이 장의 뒷부분에서 설명하겠다.

    * Function declarations do occasionally appear in non-strict blocks, which is why the ES6 specification describes measures that web browsers can take to ensure that such code doesn’t break. Details are explained later.

    함수 선언은 때때로 non-strict 블록에서 나타나는데, 이 때문에 ES6 스펙에서 웹브라우저 지원율 현황을 보여주고 있다. 자세한 내용은 뒤에 설명하겠다. 

###3.2 strict 모드와 ECMAScript6(Strict mode and ECMAScript 6)
Strict mode was introduced in ECMAScript 5 to clean up the language. It is switched on by putting the following line first in a file or in a function:

strict 모드는 언어를 정돈하기 위해 ECMAScript 5부터 도입되었다. 파일이나 함수의 첫줄에 아래와 같이 추가하면 strict 모드가 된다.

```javascript
'use strict';
```

Strict mode introduces three kinds of breaking changes:
strict 모드에는 3가지 주요 변경 사항이 있다.

    * Syntactic changes: some previously legal syntax is forbidden in strict mode. For example:
        - The with statement is forbidden. It lets users add arbitrary objects to the chain of variable scopes, which slows down execution and makes it tricky to figure out what a variable refers to.
        - Deleting an unqualified identifier (a variable, not a property) is forbidden.
        - Functions can only be declared at the top level of a scope.
        - More identifiers are reserved: implements interface let package private protected public static yield

    * 문법적 변화 : 이전의 몇가지 유효한 문법은 strict 모드에서는 금지된다.
        - with문이  금지된다. with문은 사용자가 임의의 객체를 변수 스코프 체인에 추가하게 한다. 이것은 실행을 느리게 하고 변수 참조를 알아보기 어렵게 만든다. 
        - 자격이 없는 식별자(객체의 속성이 아닌 변수)를 지우는 것이 금지된다.
        - 함수 선언은 오직 스코프의 최상단에서만 가능하다.
        - 몇몇 식별자가 추가로 예약어가 되었다. : implements interface let package private protected public static yield
   
    * More errors. For example:
        + Assigning to an undeclared variable causes a `ReferenceError`. In non-strict mode, a global variable is created in this case.
        + Changing read-only properties (such as the length of a string) causes a `TypeError`. In non-strict mode, it simply has no effect.
    
    * 에러의 증가.
        + 선언되지 않은 변수에 할당하는 것은 `ReferenceError` 를 발생시킨다. non-strict 모드 에서는 이와 같은 경우 전역 변수가 생성된다.
        + (string의 length 같이) 읽기 전용 속성을 변경하는 것은 `typeError`를 일으킨다. non-strict 모드에서는 아무런 영향이 없다.

    * Different semantics: Some constructs behave differently in strict mode. For example:
        + arguments doesn’t track the current values of parameters, anymore.
        + `this` is `undefined` in non-method functions. In non-strict mode, it refers to the global object (`window`), which meant that global variables were created if you called a constructor without `new`.

   
    * 의미의 변화 : 어떤 것들은 strict 모드에서 다르게 동작한다.
        + `arguments`는 더이상 인자의 최신값을 추적하지 않는다.
        + 메소드가 아닌 함수에서 `this`는 `undefined`이다. non-strict 모드에서 `this`는 전역 객체(`window`)를 참조하는데, `new` 연산자 없이 생성자를 호출했을 때 전역 변수가 만들어진다는 것을 의미한다. 
    
Strict mode is a good example of why versioning is tricky: Even though it enables a cleaner version of JavaScript, its adoption is still relatively low. The main reasons are that it breaks some existing code, can slow down execution and is a hassle to add to files (let alone interactive command lines). I love the idea of strict mode and don’t nearly use it often enough.

strict 모드는 버저닝이 왜 까다로운 일인지 잘 보여주고 있다. strict 모드는 더 정갈한 버전의 자바스크립트를 사용할 수 있게 해주지만, 상대적으로 많이 사용되지 않고 있다. 가장 중요한 이유는 현존하는 코드가 돌아가지 않게 되거나, 실행 속도를 늦추고, 파일에 추가하는 것이 번거롭기 때문이다(커맨드 라인과 홀로 상호작용할 수 있게 해주는). 나는 strict 모드에 대한 발상은 사랑하지만, 충분히 사용해보지는 못했다.

### 3.2.1 sloppy(non-strict) 모드 지원
One JavaScript means that we can’t give up on sloppy mode: it will continue to be around (e.g. in HTML attributes). Therefore, we can’t build ECMAScript 6 on top of strict mode, we must add its features to both strict mode and non-strict mode (a.k.a. sloppy mode). Otherwise, strict mode would be a different version of the language and we’d be back to versioning. Unfortunately, two ECMAScript 6 features are difficult to add to sloppy mode: let declarations and block-level function declarations. Let’s examine why that is and how to add them, anyway.

단일 자바스크립트는 sloppy 모드를 포기 할 수 없다는 것을 의미한다. 아마도 계속 사용될 것이다(예: HTML속성에서). 그러므로 strict 모드 위에서는 ECMAScript 6를 빌드할 수 없고, 그 기능들을 strict 모드와 (sloppy 모드로 알려진) non-strict 모드 양쪽에 추가해야만한다. 그렇지 않으면 strict 모드는 언어의 다른 버전이 되어 버려 우리는 다시 버전닝의 어려움을 겪게 될 것이다. 불행하게도, ECMAScript6 기능 중 2가지는 sloppy 모드에 추가하기 어렵다. 이는 let 선언과 블록 수준에서의 함수 선언이다. 자, 이제 왜 이것이 어렵고, 어떻게 추가 할 수 있는 알아보자.

### 3.2.2 sloppy 모드에서의 let 선언
`let` enables you to declare block-scoped variables. It is difficult to add to sloppy mode, because let is only a reserved word in strict mode. That is, the following two statements are legal ES5 sloppy code:

`let`은 블록 스코프 변수 선언 가능하게 해준다. 하지만 sloppy 모드에 적용하기는 어려운데, `let`이 strict 모드에서만 예약어이기 때문이다. 다음 2개의 문은 ES5 sloppy 모드에서 유효하다. 

```javascript
var let = [];
let[x] = 'abc';
```

In strict ECMAScript 6, you get an exception in line 1, because you are using the reserved word `let` as a variable name. And the statement in line 2 is interpreted as a `let` variable declaration (that uses destructuring).

ECMAScript 6 strict 모드에서는 첫 번째 줄에서 예외가 발생한다. 왜냐하면 예약어인 `let`을 변수 이름으로 사용하고 있기 때문이다. 그리고 두 번째 줄은 (해체를 사용한) `let` 변수 선언으로 해석된다.

In sloppy ECMAScript 6, the first line does not cause an exception, but the second line is still interpreted as a `let` declaration. This way of using the identifier `let` is so rare on the web that ES6 can afford to make this interpretation. Other ways of writing `let` declarations can’t be mistaken for sloppy ES5 syntax:

ECMAScript 6 sloppy 모드에서는 첫 번째 줄에서 예외가 발생하지 않는다. 그러나 두 번째 줄은 여전히 `let` 선언으로 해석된다. 이러한 방식으로 `let` 식별자를 사용하는 것은 웹에서는 굉장히 보기 드물었기 때문에 ES6에서는 `let`으로 해석할 수 있었다. 다른 방식으로 서술된 `let` 선언은 ES5 sloppy 모드 문법에서 잘못 해석되지 않는다.

```javascript
let foo = 123;
let {x,y} = computeCoordinates();
```

### 3.2.3 sloppy 모드에서 블록 수준 함수 선언(Block-level function declarations in sloppy mode)

ECMAScript 5 strict mode forbids function declarations in blocks. The specification allowed them in sloppy mode, but didn’t specify how they should behave. Hence, various implementations of JavaScript support them, but handle them differently.

ECMAScript 5 strict 모드에서는 블록 내에서의 함수 선언을 금지한다. sloppy 모드에서는 허용되지만, 그 함수들이 어떻게 동작해야 하는지는 명시하지 않고있다. 그런 연유로 다양한 자바스크립트 구현체들이 블록 수준 함수 선언을 지원하지만, 구현 방법은 각기 다르다.

ECMAScript 6 wants a function declaration in a block to be local to that block. That is OK as an extension of ES5 strict mode, but breaks some sloppy code. Therefore, ES6 provides “web legacy compatibility semantics” for browsers that lets function declarations in blocks exist at function scope.

ECMAScript 6에서는 블록 내 함수 선언을 해당 블록의 로컬 스코프로 만들고자 했다. 그것은 ES5의 strict 모드 확장으로서는 괜찮았지만, 몇몇 sloppy 모드의 코드들은 제대로 동작하지 않았다. 그래서 ES6는 "웹 레거시 호환성 의미체계(web legacy compatibility semantics)"을 브라우저에 제공하여 블록 내 함수 선언을 함수 스코프 상에 존재하도록 해주었다.

### 3.2.4 다른 키워드들
The identifiers yield and static are only reserved in ES5 strict mode. ECMAScript 6 uses context-specific syntax rules to make them work in sloppy mode:
    * In sloppy mode, `yield` is only a reserved word inside a generator function.
    * `static` is currently only used inside class literals, which are implicitly strict (see below).

`yield`와 `static` 식별자는 ES5 strict 모드에서만 예약어이다. ECMAScript 6에서는 sloppy 모드에서 이들을 동작하게 하기 위해 컨텍스트 한정 문법을 사용한다.
    * sloppy 모드에서 `yield`는 제네레이터 함수 내부에서만 예약어이다.
    * `static`은 현재 클래스의 리터럴 내부에서만 예약어인데, 이는 암시적 strict 모드 이다.(다음 절을 보라.)
    
### 3.2.5 암시적 strict 모드(Implicit strict mode)
The bodies of modules and classes are implicitly in strict mode in ECMAScript 6 – there is no need for the '`use strict`' marker. Given that virtually all of our code will live in modules in the future, ECMAScript 6 effectively upgrades the whole language to strict mode.

ECMAScript 6에서는 모듈과 클래스의 바디에서 암시적 strict 모드이므로 '`use stict`'를 기술 할 필요가 없다. 사실상 앞으로 모든 코드는 모듈 안에서 움직일 것이고, 이에 따라 ECMAScript 6는 효과적으로 언어 전반을 strict 모드로 업그레이드 할 수 있게 될 것이다.

The bodies of other constructs (such as arrow functions and generator functions) could have been made implicitly strict, too. But given how small these constructs usually are, using them in sloppy mode would have resulted in code that is fragmented between the two modes. Classes and especially modules are large enough to make fragmentation less of an issue.

다른 생성자(화살표 함수나, 제네레이터 함수 등)의 바디에서도 역시 암시적 strict 모드이다. 그러나 보통 이런 생성자들은 얼마나 작은지에 따라 sloppy 모드에서 이들을 사용하는 것은 두 모드 사이에서 파편화된 코드가 될 수도 있다. 클래스와 모듈은 충분히 크기 때문에 파편화를 줄일 수 있다.

### 3.2.6 수정할 수 없는 것들(Things that can’t be fixed)
The downside of One JavaScript is that you can’t fix existing quirks, especially the following two.

단일 자바스크립트의 약점은 기존의 문제점을 수정할 수 없다는 것인데, 특히 다음 두 가지이다.

First, `typeof null` should return the string '`null`' and not '`object`'. TC39 tried fixing it, but it broke existing code. On the other hand, adding new results for new kinds of operands is OK, because current JavaScript engines already occasionally return custom values for host objects. One example are ECMAScript 6’s symbols:

먼저, `typeof null` 은 문자열 '`object`'가 아니라 '`null`'을 리턴해야 한다. TC39는 이것을 고치려고 했지만, 그렇게 되면 기존 코드가 망가지게 된다. 반면에 새로운 종류의 연산자의 결과를 추가하는 괜찮았다. 현재도 이미 자바스크립트 엔진이 때때로 커스텀 값을 호스트 객체에 전달해주고 있기 때문이다. 그 한 예가 ECMAScript 6의 심볼이다.


```
> typeof Symbol.iterator
'symbol'
```

Second, the global object (window in browsers) shouldn’t be in the scope chain of variables. But it is also much too late to change that now. At least, one won’t be in global scope in modules and let never creates properties of the global object, not even when used in global scope.

다음으로 전역 객체(브라우저에서는 `window`)는 변수의 스코프 체인상에 있어서는 안 된다. 하지만 이것도 역시 이제와서 고치기에는 너무 늦었다. 적어도 모듈의 전역 스코프 상에는 하나도 없거나, 전역 스코프에서 사용하는 경우를 제외하고는 전역 객체의 프로퍼티를 만들어서는 안된다. 

### 3.3 ES6 주요 변경 사항(Breaking changes in ES6)
ECMAScript 6 does introduce a few minor breaking changes (nothing you’re likely to encounter). They are listed in two annexes:

    * Annex D: Corrections and Clarifications in ECMAScript 2015 with Possible Compatibility Impact
    * Annex E: Additions and Changes That Introduce Incompatibilities with Prior Editions

ECMAScript 6에는 (좀처럼 만나기 힘든) 몇 가지 마이너 변경 사항이 있다. 다음 두 부록의 목록에서 확인할 수 있다.

    * 부록 D: ECMAScript 2015에서 호환성에 영향을 줄 수 있는 수정 사항과 설명.
    * 부록 E: 이전 버전과 호환되지 않은 추가/변경 사항.

### 3.4 결론(Conclusion)
One JavaScript means making ECMAScript 6 completely backwards compatible. It is great that that succeeded. Especially appreciated is that modules (and thus most of our code) are implicitly in strict mode.

단일 자바스크립트는 ECMAScript 6를 완전히 하위 호환되도록 만드는 것을 뜻한다. 이것이 성공했다는 것은 굉장한 것이다. 특히나 모듈(뿐만 아니라 대부분의 코드)이 암시적 strict 모드인 것은 높이 평가한다.

In the short term, adding ES6 constructs to both strict mode and sloppy mode is more work when it comes to writing the language specification and to implementing it in engines. In the long term, both the spec and engines profit from the language not being forked (less bloat etc.). Programmers profit immediately from One JavaScript, because it makes it easier to get started with ECMAScript 6.

단기적으로 언어 사양을 작성하거고 그것을 엔진에 도입하기에 앞서, ES6 생성자를 strict 모드와 sloppy 모드에 양쪽 모두에 추가하는 것이 우선이다. 장기적으로는 언어의 사양과 엔진의 장점(적은 확장 등)이 확실할 것이다. 프로그래머들은 단일 자바스크립트가 ECMAScript 6로 시작하기 훨씬 수월하게 해주기 때문에 즉시 이득을 볼 수 있다.

### 3.5 더 읽을거리(Further reading)
[1] The original 1JS proposal (warning: out of date): “ES6 doesn’t need opt-in” by David Herman.

[1] 데이비드 허먼(David Herman)의 1JS에 대한 제안(warning: out of date): “ES6 doesn’t need opt-in”, 

