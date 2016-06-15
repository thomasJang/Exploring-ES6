## 2. FAQ: ECMAScript 6

This chapter answers a few frequently asked questions about ECMAScript 6

> 2장은 ECMAScript 6에 관한 빈번한 질문에 대답한다. 

### 2.1 How can I use ES6 today?

> 2.1 현재 ES6는 어떻게 사용 할 수 있는가? 

Most of ES6 is already supported in current engines, consult Kangax’ ES6 compatibility table to find out what is supported where.

> ES6 스펙의 대부분은 현재의 엔진들에서 이미 지원되며, 어디서 무엇이 지원되는지 알아보기 위해서 Kangax 의 ES6 호환표 *Kangax’ ES6 compatibility table* 를 살펴보라.

For other options (e.g. interactive ES6 command lines and transpiling ES6 to ES5 via Babel), consult Chap. “Deploying ECMAScript 6” in “Setting up ES6”.

> 다른 선택지는, (예: 대화형 ES6 커맨드 라인과 바벨을 이용한 ES6에서 ES5로 변환) “Deploying ECMAScript 6” in “Setting up ES6”. 를 살펴보라.

### 2.2 Isn’t ECMAScript 6 now called ECMAScript 2015?

> 2.2 ECMAScript 6는 이제 ECMAScript 2015로 불리지 않는가?

Yes and no. The official name is ECMAScript 2015, but ES6 is the name that everyone knows and uses. That’s why I decided to use the latter for this book.

> 그렇기도 하고, 아니기도 하다. 공식적인 이름은 ECMAScript 2015 지만, ES6는 모두가 알고 있고 사용하는 이름이다. 그 것이 이 책에서 후자를 사용하기로 결정한 이유이다.

After ES6, ECMAScript editions are created via a new process and a yearly release cycle. That seems like a good opportunity to switch to the new naming scheme. Therefore, I’ll use the name “ECMAScript 2016” for the edition after ES6.

> ES6 이후, ECMAScript 판은 새로운 절차와 매년 출시 주기를 통해 만들어진다. 이 것은 새로운 명명 방식으로 전환하기 위한 좋은 기회로 보인다. 그러므로 이 책은 ES6 그 다음 판에 "ECMAScript 2016" 이라는 이름을 사용 할 것이다. 

### 2.3 How do I migrate my ECMAScript 5 code to ECMAScript 6?`
> 2.3 ECMAScript 5 코드를 ECMAScript 6으로 어떻게 통합하는가? 

There is nothing to do: ECMAScript 6 is a superset of ECMAScript 5. Therefore, all of your ES5 code is automatically ES6 code. That helps tremendously with incrementally adopting this new version. How exactly ES6 stays completely backwards compatible is explained in the chapter on “One JavaScript”.

> 해야 할 것은 없다 :  ECMAScript 6는 ECMAScript 5의 상위 집합이다. 그러므로, ES5로 작성된 모든 코드는 자동적으로 ES6 코드이다. 이것은  새로운 버전을 올려 적용하는 것에 굉장히 도움을 준다. 어떻게 ES6가 이전 버전과 완벽히 호환이 유지되는지는 “하나의 자바스크립트” 장에서 설명한다. 


### 2.4 Does it still make sense to learn ECMAScript 5?
> 2.4 ECMAScript 5를 배우는게 여전히 타당한가? 

ES6 is increasingly well supported everywhere. Does that mean that you shouldn’t learn ECMAScript 5, anymore? It doesn’t, for several reasons:

> ES6는 갈수록 더 모든 곳에서 지원이 잘 된다. 그 말이 ECMAScript 5를 더 이상 배우지 말아야 한다는 의미일까? 몇가지 이유에서 그렇지 않다. 

ECMAScript 6 is a superset of ECMAScript 5 – new JavaScript versions must never break existing code. Thus, nothing you learn about ECMAScript 5 is learned in vain.


> ECMAScript 6는 ECMAScript 5 의 상위 집합이다 - 새로운 자바스크립트 버전은 절대로 기존 코드를 망가뜨리지 않는다. 그러한 이유로, ECMAScript 5 관해 헛되이 학습되어지는 것은 없다.

There are several ECMAScript 6 features that kind of replace ECMAScript 5 features, but still use them as their foundations. It is important to understand those foundations. Two examples: classes are internally translated to constructors and methods are still functions (as they have always been).

> ECMAScript 5의 기능을 대체하는 ECMAScript 6의 기능이 몇가지 있지만, ECMAScript 6의 가반으로써 ECMAScript 5를 여전히 사용한다. 이러한 기반을 이해하는 것은 중요하다. 두 가지 예를 들자면 : class는 내부적으로 생성자로 변환되고 메서드는 여전히 함수이다. (언제나 그래왔듯이).

As long as ECMAScript 6 is compiled to ECMAScript 5, it is useful to understand the output of the compilation process. And you’ll have to compile to ES5 for a while (probably years), until you can rely on ES6 being available in all relevant browsers. It’s important to be able to understand legacy code.

> ECMAScript 6가 ECMAScript 5로 컴파일 될 뿐만 아니라 컴파일 절차의 산출물을 이해하는 것도 유용하다. 그리고 한동안은 ES5로 컴파일을 해야만 할 것이다.(아마도 수년간) 모든 적절한 브라우저에서 이용 가능한 ES6에 기댈 수 있을 때까지 말이다. 레거시 코드를 이해할수 있게 되는 것은 중요하다. 



### 2.5 Is ES6 bloated?
> 2.5 ES6는 비대한가? 

One occasionally comes across accusations of ES6 being bloated and introducing too much useless syntactic sugar (more convenient syntax for something that already exists).

> 간혹 ES6가 비대해진다는 비난과 너무나도 쓸모 없는 편의 문법(이미 존재하는 무언가를 위한 더욱 편리한 문법)을 소개한다는 인상을 남긴다.


However, in many ways, JavaScript is just now catching up with languages such as Python and Ruby. Both still have more features and come with a much richer standard library.

> 그러나 여러모로, 자바스크립트는 이제 막 파이썬이나 루비 같은 언어에 발 맞추어 가고 있을 뿐이다. 그 두 언어는 더 많은 기능을 가지며  더욱 풍부한 표준 라이브러리가 딸려있다.

If someone complains about ES6 being too big, I suggest that they try it out for a while. Nobody forces you to use any of the new features. You can start small (consult Chap. “First steps with ECMAScript 6” for suggestions) and then use more new features, as you grow more comfortable with ES6. So far, the feedback I get from people who have actually programmed with ES6 (as opposed to read about it) is overwhelmingly positive.

> 너무 거대해지고 있는 ES6에 관해 불만을 제기한다면, 한동안 ES6를 직접 써보는 걸 권한다. 그 누구도 새로운 기능을 사용해야한다고 강제 하지는 않는다. 작은 부분에서부터 시작한 다음에(“First steps with ECMAScript 6”를 추천한다) 더욱 새로운 기능을 써보면, 당신이 성장하는 만큼 ES6가 더욱 마음에 들 것이다. 지금까지 ES6로 실제 프로그래밍을 하는 사람들(이에 관해 읽기만 하는 사람과는 대조적으로)에게 받은 피드백은 너무나도 긍정적이다. 

Furthermore, things that superficially look like syntactic sugar (such as classes and modules) bring much-needed standardization to the language and serve as foundations for future features.

> 더욱이, 겉보기에 편의 문법으로 보이는 것들은(클래스와 모듈 같은 것) 언어에 매우 필요한 표준화를 불러왔고 향후 기능들을 위한 기반의 역할을 한다.


Lastly, several features were not created for normal programmers, but for library authors (e.g. generators, iterators, proxies). “Normal programmers” only need to know them superficially if at all.
> 마지막으로, 몇몇 기능들은 일반적인 프로그래머를 위한 것이 아니라 라이브러리 작성자들을 위해 만들어졌다. (예: 제너레이터, 이터레이터, 프록시) "일반적인 프로그래머"들은 항상 표면적인 부분만 알아도 된다.


### 2.6 Isn’t the ES6 specification very big?
> 2.6 ES6의 스펙은 매우 거대하지 않은가? 

The ECMAScript specification has indeed grown tremendously: The ECMAScript 5.1 PDF had 245 pages, the ES6 PDF has 593 pages. But, for comparison, the Java 8 language specification has 724 pages (excluding an index). Furthermore, the ES6 specification contains details that many other language specifications omit as implementation-defined. It also specifies how its standard library works1.
> ECMAScript 스펙은 정말 엄청나게 성장해왔다. ECMAScript 5.1 PDF 문서는 245페이지이고, ES6 PDF문서는 593페이지를 갖는다. 그러나 비교 기준으로, 자바 8 버전의 스팩은 724페이지(목차 제외)이다. 더욱이 ES6 스펙은 많은 다른 언어 스펙에서 누락시킨 스펙들을 정의된 구현체로써 상세 내용을 포함한다. 또한 이는 표준라이브러리가 어떻게 동작하는지 명시한다.



### 2.7 Does ES6 have array comprehensions?
> 2.7 ES6는 배열내포(array comprehensions)를 지원하는가? 

Originally, ES6 was to have Array and generator comprehensions (similarly to Haskell and Python). But they were not added, because TC39 wanted to explore two avenues:

> 원래 ES6는 배열과 제너레이터 내포를 갖는 것이었다(하스켈과 파이썬과 유사하게) 그러나 TC39 위원회가 두 가지 길을 탐험하길 원해서 추가되지는 않았다. 

+ `It may be possible to create comprehensions that work for arbitrary datatypes (think Microsoft’s LINQ).`
+ `It may also be possible that methods for iterators are a better way to achieve what comprehensions do.`

+ 임의의 데이터 타입을 위해 작동하는 내포를 생성하게 될 수 있다.(마이크로소프트의 LINQ를 떠올려보라)
+ 또한 이터레이터 메소드는 내포가 하는 일을 수행하기 위한 더 나은 방법일 수 있다


### 2.8 Is ES6 statically typed?
> 2.8 ES6는 정적 타입인가? 


> Static typing is not part of ES6. However, the following two technologies add static typing to JavaScript. Similar features may eventually be standardized.

정적 타입은 ES6의 일부가 아니다. 그러나 다음의 두 가지 기술은 자바스크립트에 정적인 타입을 추가한다. 유사한 기능들이 종내에는 표준화 될 수도 있다.


+ Microsoft TypeScript: is basically ES6 plus optional type annotations. At the moment, it is compiled to ES5 and throws away the type information while doing so. Optionally, it can also make that information available at runtime, for type introspection and for runtime type checks.

> 마이크로소프트 타입스크립트: 타입스크립트는 ES6에 선택적인 타입 어노테이션이 추가된 것이다. ES5 로 컴파일되고 그 동안에 타입 정보는 버려진다. 부가적으로 타입 정보는 런타임에 이용가능하게 만든다. 

+ Facebook Flow: is a type checker for ECMAScript 6 that is based on flow analysis. As such, it only adds optional type annotations to the language and infers and checks types. It does not help with compiling ES6 to ES5.

> 페이스북 플로우: 플로우는 플로우 분석에 기반을 둔 ECMAScript 6를 위한 타입 체커이다. 예를 들어 선택적인 타입 어노테이션만을 언어에 추가하고 타입을 추론하고 확인한다. 플로우는 ES6를 ES5로 변환하는데 도움을 주지는 않는다.

Two benefits of static typing are:
> 정적 타입의 두 가지 장점 : 

+ It allows you to detect a certain category of errors earlier, because the code is analyzed statically (during development, without running code). As such, static typing is complementary to testing and catches different errors.

> 정적 타입은 코드가 정적으로 분석되기 때문에, 에러의 확실한 카테고리를 더욱 빨리 찾을 수 있게한다. (개발중에, 코드의 실행이 없이도) 그래서 정적 타입은 테스팅과 다른 오류를 잡는것에 있어서 상호 보완적이다. 

+ It helps IDEs with auto-completion.

> 정적 타입은 IDE의 자동 완성을 돕는다. 

Both TypeScript and Flow are using the same notation. Type annotations are optional, which makes this approach relatively lightweight. Even without annotations, types can often be inferred. Therefore, this kind of type checking is even useful for completely unannotated code, as a consistency check.

> 타입스크립트와 플로우는 똑같은 표기법을 이용한다. 타입 어노테이션은 필수가 아닌 선택적이며 이 접근법을 상대적으로 가볍게 만든다. 어노테이션이 없을지라도, 타입은 종종 추론 될 수 있다. 그러므로 타입을 확인하는 이런 방법은 어노테이션이 전혀 붙지 않은 코드조차 일관성 검사 측면에서 유용하다.


### 2.9 Where can I find more ES6 resources?
> 2.9 더 많은 ES6 자료는 어디에서 찾을수 있나? 

These are two lists with ES6 resources:
> 아래는 ES6 학습에 도움되는 두 가지 자료이다. 

+ “ECMAScript 6 Tools” by Addy Osmani.
+ “ECMAScript 6 Learning!” by Eric Douglas.
