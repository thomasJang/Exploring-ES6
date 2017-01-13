# 13. Arrow functions
> # 13. 화살표 함수

## 13.1 Overview
> ## 13.1 개요

There are two benefits to arrow functions.
> 화살표 함수의 장점은 두 가지가 있다.

First, they are less verbose than traditional function expressions:
> 첫째, 기존의 함수 표현식보다 덜 장황하다.

```
const arr = [1, 2, 3];
const squares = arr.map(x => x * x);

// Traditional function expression:
const squares = arr.map(function (x) { return x * x });
```

Second, their `this` is picked up from surroundings (__lexical__). Therefore, you don’t need `bind()` or `that = this`, anymore.
> 둘째, 함수의 `this`를 주변(__lexical__)에서 가져온다. 그러므로 더이상 `bind()`나 `that = this` 같은 것들이 필요 없다.

```
function UiComponent() {
    const button = document.getElementById('myButton');
    button.addEventListener('click', () => {
        console.log('CLICK');
        this.handleClick(); // lexical `this`
    });
}
```

The following variables are all lexical inside arrow functions:
> 다음 변수들은 모두 다 화살표 함수 안에서 lexical 이다.

* arguments
* super
* this
* new.target


## 13.2 Traditional functions are bad non-method functions, due to `this`
## 13.2 `this` 때문에 메소드가 아닌 기존의 함수는 나쁘다.

In JavaScript, traditional functions can be used as:
> 자바스크립트에서 기존의 함수는 다음과 같이 쓰인다.

1. Non-method functions
    > 메소드가 아닌 함수
2. Methods
    > 메소드
3. Constructors
    > 생성자

These roles clash: Due to roles 2 and 3, functions always have their own `this`. But that prevents you from accessing the `this` of, e.g., a surrounding method from inside a callback (role 1).
> 역할 충돌 : 2번과 3번 역할 때문에 함수는 항상 자기만의 `this`를 갖는다. 그러나 그것은 (예를 들어) 콜백(역할1) 내부에서 감싸는 메소드의 `this`에 접근하는 것을 막는다.

You can see that in the following ES5 code:
> 이는 다음 ES5코드에서 확인 할 수 있다.

```
function Prefixer(prefix) {
    this.prefix = prefix;
}
Prefixer.prototype.prefixArray = function (arr) { // (A)
    'use strict';
    return arr.map(function (x) { // (B)
        // Doesn’t work:
        return this.prefix + x; // (C)
    });
};
```

In line C, we’d like to access `this.prefix`, but can’t do that because the `this` of the function from line B shadows the `this` of the method from line A. In strict mode, `this` is `undefined` in non-method functions, which is why we get an error if we use `Prefixer`:
> (C)에서 우리는 `this.prefix`에 접근하고 싶지만, (B) 에 있는 함수의 `this` 가 (A)에 있는 메소드의 `this`를 가려버리기 때문에 그럴 수 없다. strict 모드에서는 `this`가 메소드가 아닌 함수에서는 `undefined`인데, 이 때문에 `Prefixer` 를 사용할 때 에러가 발생한다.

```
> var pre = new Prefixer('Hi ');
> pre.prefixArray(['Joe', 'Alex'])
TypeError: Cannot read property 'prefix' of undefined
```

There are three ways to work around this problem in ECMAScript 5.
> ECMAScript 5에서 이 문제를 풀기 위한 방법으로 다음 3가지가 있다.

### 13.2.1 해결책1 : `that=this` (Solution 1: `that = this`)
You can assign `this` to a variable that isn’t shadowed. That’s what’s done in line A, below:
> `this`를 가려지지 않을 변수에 할당하는 방법이 있다. 그것이 아래 (A)에서 수행한 것이다.

```
function Prefixer(prefix) {
    this.prefix = prefix;
}
Prefixer.prototype.prefixArray = function (arr) {
    var that = this; // (A)
    return arr.map(function (x) {
        return that.prefix + x;
    });
};
```

Now `Prefixer` works as expected:
> 이제 `Prefixer`가 예상대로 작동한다.

```
> var pre = new Prefixer('Hi ');
> pre.prefixArray(['Joe', 'Alex'])
[ 'Hi Joe', 'Hi Alex' ]
```

### 13.2.2 Solution 2: specifying a value for `this`
> ### 13.2.2 해결책2: `this` 값 지정
A few Array methods have an extra parameter for specifying the value that `this` should have when invoking the callback. That’s the last parameter in line A, below.
> 몇몇 array 메소드들은 콜백 호출시 `this` 값을 지정하기 위해 추가 매개변수를 갖고 있다. 아래 (A)의 마지막 매개변수가 그것이다.

```
function Prefixer(prefix) {
    this.prefix = prefix;
}
Prefixer.prototype.prefixArray = function (arr) {
    return arr.map(function (x) {
        return this.prefix + x;
    }, this); // (A)
};
```

### 13.2.3 Solution 3: `bind(this)`
> ### 13.2.3 해결책3:`bind(this)`
You can use the method `bind()` to convert a function whose `this` is determined by how it is called (via `call()`, a function call, a method call, etc.) to a function whose `this` is always the same fixed value. That’s what we are doing in line A, below.
> `bind()` 메소드를 사용하여 어떻게 호출(`call()`, 함수 호출, 메소드 호출 등)되는지에 따라 `this`가 결정되는 함수를 언제나 동일한 고정값을 가지고 있는 `this`를 가진 함수로 변환할 수 있다. 아래 (A)에서 하고 있는 것이 그것이다.

```
function Prefixer(prefix) {
    this.prefix = prefix;
}
Prefixer.prototype.prefixArray = function (arr) {
    return arr.map(function (x) {
        return this.prefix + x;
    }.bind(this)); // (A)
};
```

### 13.2.4 ECMAScript 6 solution: arrow functions
> ### 13.2.4 ECMAScript 6 해결책: 화살표 함수 
Arrow functions are basically solution 3, with a more convenient syntax. With an arrow function, the code looks as follows.
> 화살표 함수는 기본적으로 해결책3인데, 문법이 더 편리하다. 화살표 함수를 사용한 코드는 다음과 같다.

```
function Prefixer(prefix) {
    this.prefix = prefix;
}
Prefixer.prototype.prefixArray = function (arr) {
    return arr.map((x) => {
        return this.prefix + x;
    });
};
```

To fully ES6-ify the code, you’d use a class and a more compact variant of arrow functions:
> 코드를 완전하게 ES6스럽게 하려면, 클래스와 화살표 함수의 더 간결한 변형을 사용하면 된다.
```
class Prefixer {
    constructor(prefix) {
        this.prefix = prefix;
    }
    prefixArray(arr) {
        return arr.map(x => this.prefix + x); // (A)
    }
}
```

In line A we save a few characters by tweaking two parts of the arrow function:
> (A)에서 화살표 함수의 두 부분을 수정하여 몇 글자를 줄일 수 있었다.

* If there is only one parameter and that parameter is an identifier then the parentheses can be omitted.
    > 함수의 인자가 하나뿐이고, 그 매개변수가 식별자라면 괄호를 생략할 수 있다.
* An expression following the arrow leads to that expression being returned.
    > 화살표 다음에 오는 표현식 한 개는 반환된다.

In the code, you can also see that the methods `constructor` and `prefixArray` are defined using new, more compact ES6 syntax that also works in object literals.
> 코드에서 객체 리터럴에서도 작동하는 새롭고, 더 간결한 ES6 문법으로 `constructor`메소드와 `prefixArray` 메소드를 정의하는 것 또한 볼 수 있다.


## 13.3 Arrow function syntax
> ## 13.3 화살표 함수 문법
The “fat” arrow => (as opposed to the thin arrow ->) was chosen to be compatible with CoffeeScript, whose fat arrow functions are very similar.
> "뚱뚱한"화살표 => (얇은 화살표 -> 반대)는 굵은 화살표 기능이 매우 비슷한 CoffeeScript와 호환되도록 선택되었습니다.

Specifying parameters:
> 매개 변수 지정 :
```
    () => { ... } // no parameter
     x => { ... } // one parameter, an identifier
(x, y) => { ... } // several parameters
```

Specifying a body:
> 본문 지정 :
```
x => { return x * x }  // block
x => x * x  // expression, equivalent to previous line
```

The statement block behaves like a normal function body. For example, you need return to give back a value. 
With an expression body, the expression is always implicitly returned.
> 명령문 블록은 일반 함수 본문처럼 작동합니다. 예를 들어, 값을 반환하려면 리턴이 필요합니다. 표현식 본문에서는 표현식이 항상 리턴됩니다.

Note how much an arrow function with an expression body can reduce verbosity. Compare:
> 표현식 본문이있는 화살표 함수가 얼마나 자세한 정보를 줄일 수 있는지 확인하십시오. 비교:

```
const squares = [1, 2, 3].map(function (x) { return x * x });
const squares = [1, 2, 3].map(x => x * x);
```

#### 13.3.1 Omitting parentheses around single parameters
> #### 13.3.1 단일 매개 변수를 괄호로 묶지 않음

Omitting the parentheses around the parameters is only possible if they consist of a single identifier:
> 매개 변수 주위에 괄호를 생략하는 것은 단일 식별자로 구성되는 경우에만 가능합니다.

```
> [1,2,3].map(x => 2 * x)
[ 2, 4, 6 ]
```

As soon as there is anything else, you have to type the parentheses, even if there is only a single parameter. For example, you need parens if you destructure a single parameter:
> 다른 것이 있으면 바로 괄호를 입력해야합니다. 단 하나의 매개 변수 만있는 경우에도 마찬가지입니다. 예를 들어, 단일 매개 변수를 제거하는 경우 괄호가 필요합니다.

```
> [[1,2], [3,4]].map(([a,b]) => a + b)
[ 3, 7 ]
```

And you need parens if a single parameter has a default value (undefined triggers the default value!):
> 그리고 하나의 매개 변수의 부모가 필요합니다 기본값(미정의 기본값을 트리거!) :

```
> [1, undefined, 3].map((x='yes') => x)
[ 1, 'yes', 3 ]
```

### 13.4 Lexical variables
> ### 13.4 어휘 변수들
#### 13.4.1 Sources of variable values: static versus dynamic
> #### 13.4.1 변수 값의 출처 : 정적 대 동적
The following are two ways in which a variable can receive its value.
> 다음은 변수가 값을 수신 할 수있는 두 가지 방법입니다.

First, statically (lexically): Its value is determined by the structure of the program; it receives its value from a surrounding scope. For example:
> 첫째, 정적(어휘적) : 그 값은 프로그램의 구조에 의해 결정됩니다. 주변의 영역에서 값을 받습니다. 예 :

```
const x = 123;

function foo(y) {
    return x; // value received statically
}
```

Second, dynamically: It receives its value via a function call. For example:
> 둘째, 동적으로 : 함수 호출을 통해 값을 받습니다. 예 :

```
function bar(arg) {
    return arg; // value received dynamically
}
```

#### 13.4.2 Variables that are lexical in arrow functions
> #### 13.4.2 화살표 함수에서 어휘가있는 변수
The source of this is an important distinguishing aspect of arrow functions:
> this의 출처는 화살표 함수를 구별하는 중요한 점 입니다. :
* Traditional functions have a dynamic this; its value is determined by how they are called.
    > 전통적인 함수에는 동적 인 'this'가 있습니다. 그 값은 호출 방법에 따라 결정됩니다.
* Arrow functions have a lexical this; its value is determined by the surrounding scope.
The complete list of variables whose values are determined lexically is:
    > 화살표 함수에는 어휘 'this'가 있습니다. 그 값은 주변 범위에 의해 결정됩니다.
      값이 어휘 적으로 결정되는 변수의 전체 목록은 다음과 같습니다.
* arguments
* super
* this
* new.target

## 13.5 Syntax pitfalls
> 구문 함정

There are a few syntax-related details that can sometimes trip you up.
> 여기에 때때로 당신이 실수할 수 있는 문법과 관련된 것 들이 있다.

### 13.5.1 Arrow functions bind very loosely
> ### 13.5.1 화살표 함수는 매우 느슨하게 묶입니다.

Syntactically, arrow functions bind very loosely. The reason is that you want every expression that can appear in an expression body to “stick together”; it should bind more tightly than the arrow function:
> 구문 적으로 화살표 함수는 매우 느슨하게 바인딩됩니다. 그 이유는 표현식 본문에 나타날 수있는 모든 표현식을 "결합"하고 싶기 때문입니다. 화살표 함수보다 더 단단히 바인딩 하는 것이 좋다.
```
const f = x => (x % 2) === 0 ? x : 0;
```

As a consequence, you often have to wrap arrow functions in parentheses if they appear somewhere else. For example:
> 따라서 화살표 함수가 다른 곳에 표시되면 괄호 안에 화살표 함수를 넣어야하는 경우가 있습니다. 예 :
```
console.log(typeof () => {}); // SyntaxError
console.log(typeof (() => {})); // OK
```

On the flip side, you can use typeof as an expression body without putting it in parens:
> 반대로, typeof는 표현식 본문으로 괄호 안에 넣지 않고 사용할 수 있습니다.
```
const f = x => typeof x;
```

### 13.5.2 No line break after arrow function parameters
> ### 13.5.2 화살표 함수 매개 변수 다음에 줄 바꿈 없음
ES6 forbids a line break between the parameter definition and the arrow of an arrow function:
> ES6는 매개 변수 정의와 화살표 함수의 화살표 사이에서 줄 바꿈을 금지합니다.

```
const func1 = (x, y) // SyntaxError
=> {
    return x + y;
};
const func2 = (x, y) => // OK
{
    return x + y;
};
const func3 = (x, y) => { // OK
    return x + y;
};

const func4 = (x, y) // SyntaxError
=> x + y;
const func5 = (x, y) => // OK
x + y;
```

Line breaks inside parameter definitions are OK:
> 매개 변수 정의 내의 줄 바꿈은 정상입니다.
```
const func6 = ( // OK
    x,
    y
) => {
    return x + y;
};
```

The rationale for this restriction is that it keeps the options open w.r.t. to “headless” arrow functions in the future: if there are zero parameters, you’d be able to omit the parentheses.
> 이 제한받는 이유에 대해서는 추후 살펴볼 "헤드리스(headless)" 화살표 함수에서 설명하겠다.: 만약 매개 변수가 없으면 괄호를 생략 할 수 있습니다.

### 13.5.3 You can’t use statements as expression bodies
> ### 13.5.3 표현을 표현체로 사용할 수 없습니다.
#### 13.5.3.1 Expressions versus statements
> #### 13.5.3.1 표현식 대 명령문
Quick review (consult “Speaking JavaScript” for more information):
> 빠른 검토 (자세한 내용은 "JavaScript를 말하다" 참조) :

Expressions produce (are evaluated to) values. Examples:
> 표현식은 값을 산출합니다 (평가됩니다). 예 :

```
3 + 4
foo(7)
'abc'.length
```

Statements do things. Examples:

```
while (true) { ··· }
return 123;
```

Most expressions[1] can be used as statements, simply by mentioning them in statement positions:
> 대부분의 표현식[1]은 명령문 위치에서 언급하는 것만으로 명령문으로 사용할 수 있습니다.
```
function bar() {
    3 + 4;
    foo(7);
    'abc'.length;
}
```

####13.5.3.2 The bodies of arrow functions
> #### 13.5.3.2 화살표 함수의 본문
If an expression is the body of an arrow function, you don’t need braces:
> 표현식이 화살표 함수의 본문이라면 중괄호가 필요하지 않습니다.
```
asyncFunc.then(x => console.log(x));
```

However, statements have to be put in braces:
> 그러나 명령문은 중괄호로 묶어야합니다.
```
asyncFunc.catch(x => { throw x });
```

#### 13.5.4 Returning object literals
> #### 13.5.4 객체 리터럴 반환
Having a block body in addition to an expression body means that if you want the expression body to be an object literal, you have to put it in parentheses.
The body of this arrow function is a block with the label bar and the expression statement 123.

> 표현 본문 외에 블록 본문을 갖는 것은 표현 본문을 객체 리터럴로 만들려면 괄호 안에 넣어야 함을 의미합니다.
이 화살표 함수의 본문은 레이블 bar와 표현식 문 123이있는 블록입니다.

```
const f = x => { bar: 123 }
```

The body of this arrow function is an expression, an object literal:
> 이 화살표 함수의 본문은 표현식 인 객체 리터럴입니다.
```
const f = x => ({ bar: 123 })
```

## 13.6 Immediately-invoked arrow functions
> 13.6 즉시 호출되는 화살표 함수

Remember Immediately Invoked Function Expressions (IIFEs)? They look as follows and are used to simulate block-scoping and value-returning blocks in ECMAScript 5:
> 즉시 호출 된 함수 식 (IIFE)을 기억합니까? 이들은 다음과 같이 보이며 ECMAScript 5에서 블록 범위 지정 및 값 반환 블록을 시뮬레이션하는 데 사용됩니다.
```
(function () { // open IIFE
    // inside IIFE
}()); // close IIFE
```

You can save a few characters if you use an Immediately Invoked Arrow Function (IIAF):
> Immediately Invoked Arrow Function (IIAF)을 사용하면 몇 개의 문자를 절약할 수 있습니다.
```
(() => {
    return 123
})();
```

Similarly to IIFEs, you should terminate IIAFs with semicolons (or use an equivalent measure), to avoid two consecutive IIAFs being interpreted as a function call (the first one as the function, the second one as the parameter).
> IIFE와 마찬가지로 IIAF를 세미콜론으로 끝내야합니다(또는 동등한 방법을 사용하십시오). 두 개의 연속적인 IIAF가 함수 호출로 해석되는 것을 피하기 위해(첫번째는 함수, 두번째는 매개 변수).

Even if the IIAF has a block body, you must wrap it in parentheses, because it can’t be (directly) function-called, due to how loosely it binds. Note that the parentheses must be around the arrow function. With IIFEs you have a choice: you can either put the parentheses around the whole statement or just around the function expression.
> IIAF에 블록 바디가 있어도, 느슨하게 바인드(bind)하기 때문에 (직접) 함수 호출 할 수 없으므로 괄호로 묶어야합니다. 괄호는 화살표 함수 주위에 있어야합니다. IIFE를 사용하면 문장 전체에 괄호를 붙이거나 함수 표현식 주위에 괄호를 넣을 수 있습니다.

As mentioned in the previous section, arrow functions binding loosely is useful for expression bodies, where you want this expression:
> 이전 섹션에서 언급 한 것처럼, 화살표 함수는 느슨하게 바인딩하여 표현식 본문에 유용합니다.

```
const value = () => foo()
```
to be interpreted as:
> 다음과 같이 해석된다 :
```
const value = () => (foo())
```
and not as:
> 다음처럼 되지 않는다.
```
const value = (() => foo)()
```
A section in the chapter on callable entities has more information on using IIFEs and IIAFs in ES6.
> '호출 가능 엔터티'에 대한 장의 섹션에는 'ES6'에서 IIFE 및 IIAF 사용에 대한 추가 정보가 있습니다.

## 13.7 Arrow functions vs. bind()
> 13.7 화살표 함수와 bind()

ES6 arrow functions are often a compelling alternative to Function.prototype.bind().
> ES6 화살표 함수는 종종 Function.prototype.bind()의 강력한 대안입니다.

### 13.7.1 Extracting methods
> 메소드 추출

If an extracted method is to work as a callback, you must specify a fixed this, otherwise it will be invoked as a function (and this will be undefined or the global object). For example:
> 추출 된 메소드가 콜백으로 작동하려면 고정 된 'this'를 지정해야합니다. 그렇지 않으면 함수로 호출됩니다 (정의되지 않았거나 전역 객체가됩니다). 예 :

```
obj.on('anEvent', console.log.bind(console))
```
  
An alternative is to use an arrow function:
> 또 다른 방법은 화살표 기능을 사용하는 것입니다.
```
obj.on('anEvent', x => console.log(x))
```

### 13.7.2 this via parameters
> 13.7.2 매개 변수를 통한 'this'

The following code demonstrates a neat trick: For some methods, you don’t need bind() for a callback, because they let you specify the value of this, via an additional parameter. filter() is one such method:
> 다음 코드는 깔끔한 트릭을 보여줍니다. 일부 메소드의 경우 콜백에 대해 bind()가 필요하지 않습니다. 추가 매개 변수를 통해 이 값을 지정할 수 있기 때문입니다. filter()는 그러한 메서드 중 하나입니다.

```
const as = new Set([1, 2, 3]);
const bs = new Set([3, 2, 4]);
const intersection = [...as].filter(bs.has, bs);
    // [2, 3]
```
However, this code is easier to understand if you use an arrow function:
> 그러나 화살표 함수를 사용하면 이 코드를 더 쉽게 이해할 수 있습니다.
```
const as = new Set([1, 2, 3]);
const bs = new Set([3, 2, 4]);
const intersection = [...as].filter(a => bs.has(a));
    // [2, 3]
```

### 13.7.3 Partial evaluation
> 13.7.3 부분 평가

bind() enables you to do partial evaluation, you can create new functions by filling in parameters of an existing function:
> bind()를 사용하면 부분 평가가 가능하며 기존 함수의 매개 변수를 채워서 새 함수를 만들 수 있습니다.

```
function add(x, y) {
    return x + y;
}
const plus1 = add.bind(undefined, 1);
```

Again, I find an arrow function easier to understand:
> 마찬가지로, 화살표 함수로도 쉽게 이해할수 있다.
```
const plus1 = y => add(1, y);
```

## 13.8 Arrow functions versus normal functions
> ## 13.8 화살표 함수 대 일반 함수

An arrow function is different from a normal function in only two ways:
> 화살표 함수는 두 가지만 일반 함수와 다릅니다.

The following constructs are lexical: arguments, super, this, new.target
It can’t be used as a constructor: Normal functions support new via the internal method [[Construct]] and the property prototype. Arrow functions have neither, which is why new (() => {}) throws an error.
Apart from that, there are no observable differences between an arrow function and a normal function. For example, typeof and instanceof produce the same results:
> 다음 구문은 어휘입니다. arguments, super, this, new.target
생성자로 사용할 수 없습니다 : 일반 함수는 [[Construct]] 내부 메소드와 속성 프로토 타입을 통해 새 함수를 지원합니다. 화살표 함수에는 아무 것도 없기 때문에 new (() => {})가 오류를 throw합니다.
그것만 제외하면, 화살표 함수와 일반 함수 간에는 관찰 가능한 차이점이 없습니다. 예를 들어, typeof와 instanceof는 동일한 결과를 생성합니다.

```
> typeof (() => {})
'function'
> () => {} instanceof Function
true

> typeof function () {}
'function'
> function () {} instanceof Function
true
```

Consult the chapter on callable entities for more information on when to use arrow functions and when to use traditional functions.
> 화살표 함수를 사용하는시기와 전통적인 기능을 사용하는시기에 대한 자세한 내용은 호출 대상에 대한 장을 참조하십시오.

Foot Note
> 각주
-- 
[1]The exceptions are function expressions and object literals, which you have to put in parentheses, because they look like function declarations and code blocks.↩
> [1] 예외는 함수 표현과 객체 리터럴입니다. 함수 선언과 코드 블록처럼 보이므로 괄호 안에 넣어야합니다.↩