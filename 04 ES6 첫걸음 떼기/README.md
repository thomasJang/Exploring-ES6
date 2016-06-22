# 4. First steps with ECMAScript 6
# 4. ECMAScript 6의 첫 단계
This chapter helps you take your first steps with ECMAScript 6: It lists ES6 features that are easy to adopt and explains those features via ES5 code.

이 장은 ECMAScript6 첫 단계를 오르게 돕는다. 적용하기 쉽게 ES6기능을 나열하고, ES5를 통해 이 기능을 설명한다.

# 4.1 From var to let/const
# 4.1 var에서 let/const로
In ES5, you declare variables via var. Such variables are function-scoped, their scopes are the innermost enclosing functions. The behavior of var is occasionally confusing. This is an example:

ES5에서 변수를 var를 통해 선언했다. 이러한 변수는 함수 스코프이고, 이 스코프는 가장 안쪽 함수로 둘러싸여 있다. var의 행동은 때때로 혼란스럽다. 예제를 보면:

```javascript
var x = 3;
function func(randomize) {
    if (randomize) {
        var x = Math.random(); // (A) scope: 전체 함수
        return x;
    }
    return x; // A줄의 x에 접근
}
func(false); // undefined
```
That func() returns undefined may be surprising. You can see why if you rewrite the code so that it more closely reflects what is actually going on:

func()가 undefined 반환 하는 것에 놀랄지 모른다. 무슨일이 일어났는지 더 정확하게 반영하기 위해 이 코드를 재작성 한다면 무슨 일이 벌어졌는지 볼 수 있다.

```javascript
var x = 3;
function func(randomize) {
    var x;
    if (randomize) {
        x = Math.random();
        return x;
    }
    return x;
}
func(false); // undefined
```
In ES6, you can additionally declare variables via let and const. Such variables are block-scoped, their scopes are the innermost enclosing blocks. let is roughly a block-scoped version of var. const works like let, but creates variables whose values can’t be changed.

추가적으로 ES6에서 변수를 let이나 const를 통해서 선언할 수 있다. 이 같은 변수들은 블럭 스코프를 갖고, 이 스코프는 가장 안쪽에 블럭으로 둘러 싸여있다. let은 var의 대충 블럭스코프 버전이다. const는 마치 let처럼 동작하지만 값이 변할 수 없는 변수를 생성 한다.

let and const behave more strictly and throw more exceptions (e.g. when you access their variables inside their scope before they are declared). Block-scoping helps with keeping the effects of code fragments more local (see the next section for a demonstration). And it’s more mainstream than function-scoping, which eases moving between JavaScript and other programming languages.

let과 const는 더욱 엄격하게 행동하고, 더 많은 예외(예: 선언되기 전에 변수에 접근하면)를 던진다. 블럭 스코프는 코드 조각의 우효 범위가 더욱 지역적이게 유지 되도록 돕는다 (이 논증을 위한 다음 절을 보아라). 그리고 블럭 스코프는 함수 스코프보다 더 많이 사용되고, 이것은 자바스크립트와 다른 프로그래밍 언어 사이의 이동을 쉽게 한다.

If you replace var with let in the initial version, you get different behavior:

첫 버전에서 var를 let으로 바꾸면, 다른 동작을 볼 수 있다.:

```javascript
let x = 3;
function func(randomize) {
    if (randomize) {
        let x = Math.random();
        return x;
    }
    return x;
}
func(false); // 3
```
That means that you can’t blindly replace var with let or const in existing code; you have to be careful during refactoring.

이것은 작성된 코드에서 var를 let이나 const로 맹목적으로 변경 할 수 없다는 것을 의미한다. 리팩토리 하는 동안 주의를 기울여야 한다.

My advice is:

* Prefer const. You can use it for all variables whose values never change.
* Otherwise, use let – for variables whose values do change.
* Avoid var.

조언은:

* const를 선호해라. 변경되지 않는 모든 변수들은 const을 사용 수 있다.
* 반면에 let은 값이 변하는 변수를 위해 사용해라.
* var를 피해라.

More information: chapter “Variables and scoping”.

더 자세한 정보: "변수와 스코프".

## 4.2 From IIFEs to blocks
## 4.2 IIFEs에서 Block으로

In ES5, you had to use a pattern called IIFE (Immediately-Invoked Function Expression) if you wanted to restrict the scope of a variable tmp to a block:

ES5에서 변수 tmp를 블록으로 스코프를 제한 하길 원한다면, IIFE(즉시 호출된 함수 표현식)으로 불리는 패턴을 사용해야 한다.

```javascript
(function () {  // 열기 IIFE
    var tmp = ···;
    ···
}());  // 닫기 IIFE

console.log(tmp); // ReferenceError
```
In ECMAScript 6, you can simply use a block and a let declaration (or a const declaration):

ECMAScript 6에서, 블럭과 let선언(또는 const 선언)을 간단하게 사용할 수 있다.

```javascript
{  // 블럭 열기
    let tmp = ···;
    ···
}  // 블럭 닫기

console.log(tmp); // ReferenceError
```
More information: section “Avoid IIFEs in ES6”.

더 자세한 정보: "ES6에서 IIFEs를 피해라"

## 4.3 From concatenating strings to template literals
## 4.3 문자열 결합에서 템플릿 리터럴로
With ES6, JavaScript finally gets literals for string interpolation and multi-line strings.

ES6에서, 자바스크립트는 문자열 보간과 멀티라인 문자열을 위한 리터럴을 마침내 얻었다.

### 4.3.1 String interpolation
### 4.3.1 문자열 보간
In ES5, you put values into strings by concatenating those values and string fragments:

ES5에서 값과 문자열 조각을 결합하여 값을 문자열에 넣었다.

```javascript
function printCoord(x, y) {
    console.log('('+x+', '+y+')');
}
```
In ES6 you can use string interpolation via template literals:

ES6에서 템플릿 리터럴을 통해 문자 보간을 사용할 수 있다:

```javascript
function printCoord(x, y) {
    console.log(`(${x}, ${y})`);
}
```

### 4.3.2 Multi-line strings
### 4.3.2 멀티라인 문자열
Template literals also help with representing multi-line strings.

템플릿 리터럴은 멀티라인 문자열을 표현하는데 도움을 준다.

For example, this is what you have to do to represent one in ES5:

예를 들면, 이것은 ES5에서 멀티라인을 표현하기 위해 해야 하는 것 이다:

```javascript
var HTML5_SKELETON =
    '<!doctype html>\n' +
    '<html>\n' +
    '<head>\n' +
    '    <meta charset="UTF-8">\n' +
    '    <title></title>\n' +
    '</head>\n' +
    '<body>\n' +
    '</body>\n' +
    '</html>\n';
```

If you escape the newlines via backslashes, things look a bit nicer (but you still have to explicitly add newlines):

백슬래쉬를 통해 뉴라인을 이스케이프 한다면, 좀 더 좋게 보여진다(그러나 당신은 여전히 명시적으로 뉴라인을 추가해야 한다.):

```javascript
var HTML5_SKELETON = '\
    <!doctype html>\n\
    <html>\n\
    <head>\n\
        <meta charset="UTF-8">\n\
        <title></title>\n\
    </head>\n\
    <body>\n\
    </body>\n\
    </html>';
```
ES6 template literals can span multiple lines:

ES6 템플릿 리터럴은 여러줄에 걸쳐 있을 수 있다.:

```javascript
const HTML5_SKELETON = `
    <!doctype html>
    <html>
    <head>
        <meta charset="UTF-8">
        <title></title>
    </head>
    <body>
    </body>
    </html>`;
```
(The examples differ in how much whitespace is included, but that doesn’t matter in this case.)
(이 예제는 얼마나 많은 공백이 포함하고 있는지에 대해 차이가 나지만 이 경우에는 별 문제가 없다.)

More information: chapter “Template literals and tagged templates”.

더 자세한 정보: "템플릿 리터럴과 태그드 템플릿".

## 4.4 From function expressions to arrow functions
## 4.4 함수표현법에서 애로우 함수로

In current ES5 code, you have to be careful with this whenever you are using function expressions. In the following example, I create the helper variable _this (line A) so that the this of UiComponent can be accessed in line B.

현재 ES5 코드에서, 함수 표현식을 사용할 때 this를 주의 해야 한다. 다음 예제에서 헬퍼 변수 _this(A줄)를 생성하였기 때문에 UiComponent의 this를 B줄에서 접근 할수 있다.
```javascript
function UiComponent() {
    var _this = this; // (A)
    var button = document.getElementById('myButton');
    button.addEventListener('click', function () {
        console.log('CLICK');
        _this.handleClick(); // (B)
    });
}
UiComponent.prototype.handleClick = function () {
    ···
};
```
In ES6, you can use arrow functions, which don’t shadow this (line A):

ES6에서 애로우 함수를 사용하면, 이 때 this(A줄)를 덮지 않을 수 있다.:

```javascript
function UiComponent() {
    var button = document.getElementById('myButton');
    button.addEventListener('click', () => {
        console.log('CLICK');
        this.handleClick(); // (A)
    });
}
```
(In ES6, you also have the option of using a class instead of a constructor function. That is explored later.)

(ES6에서는 또한 생성자 함수 대신에 클래스를 사용하는 선택이 있다. 이것은 나중에 살펴보자)

Arrow functions are especially handy for short callbacks that only return results of expressions.

애로우 함수는 단지 표현식의 값을 반환하는 짧은 콜백에 특히 편리하다.

In ES5, such callbacks are relatively verbose:

ES5에서, 이런 콜백은 비교적 장황하다:

```javascript
var arr = [1, 2, 3];
var squares = arr.map(function (x) { return x * x });
```
In ES6, arrow functions are much more concise:

ES6에서, 애로우 함수는 더욱더 간결하다.:

```javascript
const arr = [1, 2, 3];
const squares = arr.map(x => x * x);
```
When defining parameters, you can even omit parentheses if the parameters are just a single identifier. Thus: (x) => x * x and x => x * x are both allowed.

파라미터를 정의시, 파라미터가 단 하나의 식별자 라면, 당신은 심지어 괄호를 뺄 수 있다. 따라서: (x) => x * x 와 x => x * x 는  둘다 허용된다.

More information: chapter “Arrow functions”.

더 자세한 내용은: "애로우 함수" 장.

## 4.5 Handling multiple return values
## 4.5 다중 리턴 값 다루기
Some functions or methods return multiple values via arrays or objects. In ES5, you always need to create intermediate variables if you want to access those values. In ES6, you can avoid intermediate variables via destructuring.

특정 함수나 메소드는 다중 값을 배열이나 객체를 통해 반환한다. ES5에서 다중 값 접근을 원하면, 항상 중간 변수 생성이 필요하다. ES6에서는 해체(destructuring)를 통해 중간 변수를 피할 수 있다.

### 4.5.1 Multiple return values via arrays
### 4.5.1 배열을 통한 다중 리턴 값
exec() returns captured groups via an Array-like object. In ES5, you need an intermediate variable (matchObj in the example below), even if you are only interested in the groups:

exec()는 유사배열을 통해 캡쳐된 그룹을 반환한다. ES5에서 비록 그룹 중에서 하나만 원하는 값이 있더라도, 중간 변수 (아래 예제의 matchObj)가 필요하다.:

```javascript
var matchObj =
    /^(\d\d\d\d)-(\d\d)-(\d\d)$/
    .exec('2999-12-31');
var year = matchObj[1];
var month = matchObj[2];
var day = matchObj[3];
```
In ES6, destructuring makes this code simpler:

ES6에서는 해체는 이 코드를 단순하게 해준다.:
```javascript
const [, year, month, day] =
    /^(\d\d\d\d)-(\d\d)-(\d\d)$/
    .exec('2999-12-31');
```
The empty slot at the beginning of the Array pattern skips the Array element at index zero.

배열 패턴의 시작부분인 빈 슬롯은 인덱스가 0인 배열 요소를 건너뛴다.

### 4.5.2 Multiple return values via objects
### 4.5.2 객체를 통한 다중 값 반환
The method Object.getOwnPropertyDescriptor() returns a property descriptor, an object that holds multiple values in its properties.
메소드인 Object.getOwnPropertyDescriptor()는 프로퍼티 디스크립터를 반환하고, 디스크립터 객체는 그 프로퍼티의 다중 값을 갖는다.

In ES5, even if you are only interested in the properties of an object, you still need an intermediate variable (propDesc in the example below):

ES5에서 비록 이 객체 중 하나의 프로퍼티만 원한다 하더라도, 여전히 중간 변수(아래의 예제 propDesc)가 필요하다.:

```javascript
var obj = { foo: 123 };

var propDesc = Object.getOwnPropertyDescriptor(obj, 'foo');
var writable = propDesc.writable;
var configurable = propDesc.configurable;

console.log(writable, configurable); // true true
```
In ES6, you can use destructuring:
ES6에서 해체를 사용할 수 있다:

```javascript
const obj = { foo: 123 };

const {writable, configurable} =
    Object.getOwnPropertyDescriptor(obj, 'foo');

console.log(writable, configurable); // true true
```
{writable, configurable} is an abbreviation for:

{writable, configurable} 는 다음의 약어 이다:
```javascript
{ writable: writable, configurable: configurable }
```
More information: chapter “Destructuring”.
더 자세한 정보: "해체" 장.

## 4.6 From for to forEach() to for-of
## 4.6 for에서 forEach()로 for-of로
Prior to ES5, you iterated over Arrays as follows:

ES5 이전에는, 배열을 다음 처럼 순환했다:

```javascript
var arr = ['a', 'b', 'c'];
for (var i=0; i<arr.length; i++) {
    var elem = arr[i];
    console.log(elem);
}
```
In ES5, you have the option of using the Array method forEach():

ES5에서는, 당신은 배열 메소드 forEach 사용에 대한 선택권이 있다.:

```javascript
arr.forEach(function (elem) {
    console.log(elem);
});
```
A for loop has the advantage that you can break from it, forEach() has the advantage of conciseness.

for 루프는 당신이 정지 할 수 있는 장점이 있지만, forEach는 간결하다는 장점이 있다.

In ES6, the for-of loop combines both advantages:
ES6에서는 for-fo 루프는 두 장점을 갖는다:

```javascript
const arr = ['a', 'b', 'c'];
for (const elem of arr) {
    console.log(elem);
}
```
If you want both index and value of each array element, for-of has got you covered, too, via the new Array method entries() and destructuring:

각 배열의 원소의 인덱스나 값을 원한다면 새로운 배열 메소드 entires()와 해체, for-of를 사용하여 할 수 있다:

```javascript
for (const [index, elem] of arr.entries()) {
    console.log(index+'. '+elem);
}
```
More information: Chap. “The for-of loop”.

더 자세한 내용: "for-of 루프" 장

## 4.7 Handling parameter default values
## 4.7 파라미터 기본값 다루기
In ES5, you specify default values for parameters like this:

ES5에서 파라미터의 기본값을 이 같이 지정한다.:

```javascript
function foo(x, y) {
    x = x || 0;
    y = y || 0;
    ···
}
```
ES6 has nicer syntax:

ES6는 더 좋은 문법을 갖는다:

```javascript
function foo(x=0, y=0) {
    ···
}
```
An added benefit is that in ES6, a parameter default value is only triggered by undefined, while it is triggered by any falsy value in the previous ES5 code.

ES6에서 추가적인 이득은 파라미터 기본값은 단지 undefined에 의해 유발되며 반면에 ES5이전에서는 거짓인 값(falsy)에 의해 유발된다.

더 자세한 내용: "파라미터 기본값" 절.

## 4.8 Handling named parameters
## 4.8 기명 파라미터 다루기
A common way of naming parameters in JavaScript is via object literals (the so-called options object pattern):

자바스크립트에서 파라미터에 이름을 붙이는 일반적인 방법은 객체리터럴을 통한 것 이다(이는 옵션 객체 패턴으로 불려진다):

```javascript
selectEntries({ start: 0, end: -1 });
```
Two advantages of this approach are: Code becomes more self-descriptive and it is easier to omit arbitrary parameters.

이 접근의 두가지 이점은: 코드가 스스로를 잘 설명할 수 있고, 임의로 파라미터를 빼기 더 쉽다.

In ES5, you can implement selectEntries() as follows:

ES5에서 selectEntries()를 다음 같이 구현할 수 있다:

```javascript
function selectEntries(options) {
    var start = options.start || 0;
    var end = options.end || -1;
    var step = options.step || 1;
    ···
}
```
In ES6, you can use destructuring in parameter definitions and the code becomes simpler:

ES6에서 파라미터 정의 시 해체 사용할 수 있고 코드는 더욱 간단해 진다:

```javascript
function selectEntries({ start=0, end=-1, step=1 }) {
    ···
}
```

### 4.8.1 Making the parameter optional
### 4.8.1 선택적 파라미터 만들기
To make the parameter options optional in ES5, you’d add line A to the code:

ES5에서 파라미터 options을 선택적으로 만들기 위해서 코드의 A줄을 추가 해야 한다:

```javascript
function selectEntries(options) {
    options = options || {}; // (A)
    var start = options.start || 0;
    var end = options.end || -1;
    var step = options.step || 1;
    ···
}
```

In ES6 you can specify {} as a parameter default value:

ES에서 파라미터 기본값으로 {}을 지정 할 수 있다:

```javascript
function selectEntries({ start=0, end=-1, step=1 } = {}) {
    ···
}
```

더 자세한 내용은: "시뮬레이션 명명 파라미터"절

## 4.9 From arguments to rest parameters
## 4.9 arguments에서 나머지 파라미터로
In ES5, if you want a function (or method) to accept an arbitrary number of arguments, you must use the special variable arguments:

ES5에서 함수(또는 메소드) 인수를 임의의 수를 받기를 원한다면 반드시 특별 변수인 arguments를 사용해야 한다:

```javascript
function logAllArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```
In ES6, you can declare a rest parameter (args in the example below) via the ... operator:

ES6에서 나머지 파라미터(아래 예제의 args)는 ...연산자를 통해 선언 할 수 있다:

```javascript
function logAllArguments(...args) {
    for (const arg of args) {
        console.log(arg);
    }
}
```
Rest parameters are even nicer if you are only interested in trailing parameters:

뒷 부분에만 흥미가 있다면 나머지 파라미터가 더 좋다:

```javascript
function format(pattern, ...args) {
    ···
}
```
Handling this case in ES5 is clumsy:

ES5에서 이 상황을 다루면 꼴사납다:

```javascript
function format() {
    var pattern = arguments[0];
    var args = [].slice.call(arguments, 1);
    ···
}
```
Rest parameters make code easier to read: You can tell that a function has a variable number of parameters just by looking at its parameter definitions.
나머지 파라미터는 코드를 읽기 쉽게 만들어 준다: 파라미터 정의를 보는 것을 통해 함수가 가지는 파라미터 가변 수를 말할 수 있다.

더 자세한 정보: "나머지 파라미터"절.

## 4.10 From apply() to the spread operator (...)
## 4.10 apply()에서 펼침 연산자 (...)
In ES5, you turn arrays into parameters via apply(). ES6 has the spread operator for this purpose.

ES5에서 배열을 apply()을 통해 파라미터로 변환한다. ES6은 이 목적을 위해 펼침 연산자를 갖는다.

### 4.10.1 Math.max();
ES5 – apply():
```javascript
> Math.max.apply(null, [-1, 5, 11, 3])
11
``
ES6 – spread operator:

ES6 – 펼침 연산자:

```javascript
> Math.max(...[-1, 5, 11, 3])
11
```

### 4.10.2 Array.prototype.push()
ES5 – apply():

```javascript
var arr1 = ['a', 'b'];
var arr2 = ['c', 'd'];

arr1.push.apply(arr1, arr2);
    // arr1는 ['a', 'b', 'c', 'd']
```

ES6 – spread operator:
ES6 – 펼침 연산자:

```javascript
const arr1 = ['a', 'b'];
const arr2 = ['c', 'd'];

arr1.push(...arr2);
    // arr1는 ['a', 'b', 'c', 'd']
```

더 자세한 내용: "펼침 연산자 (...)"절.

## 4.11 From concat() to the spread operator (...)
## 4.11 concat()에서 펼침 연산자 (...)로
The spread operator can also turn the contents of its operand into Array elements. That means that it becomes an alternative to the Array method concat().

펼침 연산자는 또한 피연산자의 내용을 배열 원소로 바꿀 수 있다. 이것은 배열 메소드 concat()의 대안이 되는 것을 의미한다.

ES5 – concat():

```javascript
var arr1 = ['a', 'b'];
var arr2 = ['c'];
var arr3 = ['d', 'e'];

console.log(arr1.concat(arr2, arr3));
    // [ 'a', 'b', 'c', 'd', 'e' ]
```

ES6 – spread operator:

ES6 – 펼침 연산자:

```javascript
const arr1 = ['a', 'b'];
const arr2 = ['c'];
const arr3 = ['d', 'e'];

console.log([...arr1, ...arr2, ...arr3]);
    // [ 'a', 'b', 'c', 'd', 'e' ]
```
더 자세한 내용: "펼침 연산자 (...)" 절.

## 4.12 From function expressions in object literals to method definitions
## 4.12 객체 리터럴에서의 함수표현식에서 메소드 정의로
In JavaScript, methods are properties whose values are functions.

자바스크립트에서 메소드는 값이 함수인 프로퍼티 이다.

In ES5 object literals, methods are created like other properties. The property values are provided via function expressions.
ES5 객체 리터럴에서 메소드는 다른 프로퍼티 처럼 생성된다. 이 프로퍼티 값은 함수 표현식을 통해 제공된다.

```javascript
var obj = {
    foo: function () {
        ···
    },
    bar: function () {
        this.foo();
    }, // trailing comma is legal in ES5
}
```
ES6 has method definitions, special syntax for creating methods:

ES6은 메소드 정의를 갖고, 메소드 생성에 대한 특별 문법을 갖는다:

```javascript
const obj = {
    foo() {
        ···
    },
    bar() {
        this.foo();
    },
}
```
더 자세한 내용: "메소드 정의" 절.

## 4.13 From constructors to classes
## 4.13 생성자로부터 클래스로
ES6 classes are mostly just more convenient syntax for constructor functions.

ES6 클래스는 대개 함수 생성자에 대한 문법보다 더 편리 하다.

### 4.13.1 Base classes
### 4.13.1 기본 클래스
In ES5, you implement constructor functions directly:

ES5에서 생성자 함수를 직접 구현한다:

```javascript
function Person(name) {
    this.name = name;
}
Person.prototype.describe = function () {
    return 'Person called '+this.name;
};
```
In ES6, classes provide slightly more convenient syntax for constructor functions (note especially the compact syntax for method definitions – no keyword function needed):

ES6에서 클래스는 생성자 함수에 대한 약간 더 편리한 문법을 제공한다(특히 메소드 정의에 대한 간결한 문법을 주목하라 - function 키워드는 필요치 않다):

```javascript
class Person {
    constructor(name) {
        this.name = name;
    }
    describe() {
        return 'Person called '+this.name;
    }
}
```

### 4.13.2 Derived classes
### 4.13.2 파생 클래스
Subclassing is complicated in ES5, especially referring to super-constructors and super-properties. This is the canonical way of creating a sub-constructor of Person, Employee:

ES5에서 하위 클래스를 만드는 것은 복잡하고 특히 부모 생성자와 부모 프로퍼티를 참조 하는 것이 복잡하다. 여기 Person에 하위 생성자 Employee를 생성하는 규범적인 방법이 있다:

```javascript
function Employee(name, title) {
    Person.call(this, name); // super(name)
    this.title = title;
}
Employee.prototype = Object.create(Person.prototype);
Employee.prototype.constructor = Employee;
Employee.prototype.describe = function () {
    return Person.prototype.describe.call(this) // super.describe()
           + ' (' + this.title + ')';
};
```
ES6 has built-in support for subclassing, via the extends clause:

ES6는 extends 절을 통한 서브클래스 생성을 내장으로 지원한다:

```javascript
class Employee extends Person {
    constructor(name, title) {
        super(name);
        this.title = title;
    }
    describe() {
        return super.describe() + ' (' + this.title + ')';
    }
}
```
더 자세한 내용: "클래스"장.

## 4.14 From custom error constructors to subclasses of Error
## 4.14 커스톰 에러 생성자로 부터 에러의 하위 클래스로
In ES5, it is impossible to subclass the built-in constructor for exceptions, Error (the chapter “Subclassing Built-ins” in “Speaking JavaScript” explains why). The following code shows a work-around that gives the constructor MyError important features such as a stack trace:

ES5에서 익셉션, 에러에 대한 내장 생성자를 하위 클래스를 만드는것은 불가능 했다(말하는 자바스크립트의 내장객체의 하위 클래스 장에서 이유를 설명한다). 아래 코드는 생성자 MyError에게 스택 트래이스처럼 중요한 기능을 전달하는 차선책을 보여준다:

```javascript
function MyError() {
    // Use Error as a function
    var superInstance = Error.apply(null, arguments);
    copyOwnPropertiesFrom(this, superInstance);
}
MyError.prototype = Object.create(Error.prototype);
MyError.prototype.constructor = MyError;
```
In ES6, all built-in constructors can be subclassed, which is why the following code achieves what the ES5 code can only simulate:

ES6에서 모든 내장 생성자는 서브클래스를 만들수 있고, 이것은 아래의 코드가 ES5코드를 흉내 내는 것을 할 수 있는 이유이다:

```javascript
class MyError extends Error {
}
```
More information: section “Subclassing built-in constructors”.

더 자세한 정보: "내장 생성자의 서브클래스 만들기"절.

## 4.15 From objects to Maps
## 4.15 객체에서 맵으로
Using the language construct object as a map from strings to arbitrary values (a data structure) has always been a makeshift solution in JavaScript. The safest way to do so is by creating an object whose prototype is null. Then you still have to ensure that no key is ever the string '__proto__', because that property key triggers special functionality in many JavaScript engines.

문자열을 통해 아무 값을 시상하는 것(자료구조)처럼 언어 구조 객체는 자바스크립트에서 항상 임시 방법이 였다. 더 안전한 방법은 프로퍼티가 null인 객체 생성을 통한 것 이다. 이 때도 문자열 '__proto__' 가 없는지 확인해야 한다. 왜냐하면 그 프로퍼티 키는 많은 자바스크립트 엔진에서 특별한 기능을 유발하기 때문이다.

The following ES5 code contains the function countWords that uses the object dict as a map:

아래 ES5 코드는 맵으로써 객체 dict을 사용한 함수 countWords를 포함한다:

```javascript
var dict = Object.create(null);
function countWords(word) {
    var escapedWord = escapeKey(word);
    if (escapedWord in dict) {
        dict[escapedWord]++;
    } else {
        dict[escapedWord] = 1;
    }
}
function escapeKey(key) {
    if (key.indexOf('__proto__') === 0) {
        return key+'%';
    } else {
        return key;
    }
}
```
In ES6, you can use the built-in data structure Map and don’t have to escape keys. As a downside, incrementing values inside Maps is less convenient.

ES6에서 내장 자료 구조 Map을 사용할 수 있고 이스케이프 키는 없어도 된다. 단점은 맵안에서 값을 증가시키는 것은 덜 편리하다.

```javascript
const map = new Map();
function countWords(word) {
    const count = map.get(word) || 0;
    map.set(word, count + 1);
}
```
Another benefit of Maps is that you can use arbitrary values as keys, not just strings.

맵에 대한 다른 장점은 키로 문자열 뿐 아니라 아무값을 키로 사용할 수 있다.

더 자세한 내용: :
자바스크립트 말하기 "사전 패턴: 프로토타입 없는 객체는 더 나은 맵 이다."절
"맵과 셋" 장

## 4.16 New string methods
## 4.16 새로운 문자열 메소드
The ECMAScript 6 standard library provides several new methods for string
ECMAScript 6 표준 라이브러리는 문자열에 관한 여러개의 새로운 메소드를 제공한다.

From indexOf to startsWith:

indexOf로 부터 startsWidth:

```javascript
if (str.indexOf('x') === 0) {} // ES5
if (str.startsWith('x')) {} // ES6
```

From indexOf to endsWith:

indexOf로 부터 endsWith:

```javascript
function endsWith(str, suffix) { // ES5
  var index = str.indexOf(suffix);
  return index >= 0
    && index === str.length-suffix.length;
}
str.endsWith(suffix); // ES6
```
From indexOf to includes:

indexOf로 부터 includes:

```javascript
if (str.indexOf('x') >= 0) {} // ES5
if (str.includes('x')) {} // ES6
```
From join to repeat (the former way of repeating a string is more of a hack):

join으로 부터 repeat (문자열 반복 이전의 방법은 꼼수(hack)이다.):

```javascript
new Array(3+1).join('#') // ES5
'#'.repeat(3) // ES6
```

더 자세한 정보: "새로운 문자열 기능"장

## 4.17 New Array methods
## 4.17 새로운 배열 메소드
There are also several new Array methods in ES6.

ES6에서 또한 여러개의 새로운 배열 메소드가 있다.

###4.17.1 From Array.prototype.indexOf to Array.prototype.findIndex
### 4.17.1 Array.prototype.indexOf에서 Array.prototype.findIndex
The latter can be used to find NaN, which the former can’t detect:
후자는 NaN을 발견하는데 사용 할 수 있지만 전자는 발견하지 못한다:

```javascript
const arr = ['a', NaN];

console.log(arr.indexOf(NaN)); // -1
console.log(arr.findIndex(x => Number.isNaN(x))); // 1
```
As an aside, Number.isNaN() provides a safe way to detect NaN (because it doesn’t coerce non-numbers to numbers):

여담으로 Number.isNaN()은 NaN을 확인하는 안전한 방법을 제공한다. (왜냐하면 이것은 비 숫자를 숫자로 강제로 변환하지 않기 때문이다.):

```javascript
> isNaN('abc')
true
> Number.isNaN('abc')
false
```

### 4.17.2 From Array.prototype.slice() to Array.from()
### 4.17.2 Array.prototype.slice()에서 Array.from()으로
In ES5, the former method was used to convert Array-like objects to Arrays. In ES6, you have Array.from():

ES5에서 후자 메소드는 유사 배열을 배열로 변경하는데 사용하였다. ES6에서는 Array.from()을 사용한다:

```javascript
var arr1 = Array.prototype.slice.call(arguments); // ES5
const arr2 = Array.from(arguments); // ES6
```
If a value is iterable, you can also use the spread operator (...) to convert it to an Array:

값이 이터러블이면 펼침 연산자를 또 사용하여 배열로 만들 수 있다:

```javascript
const arr1 = [...'abc'];
    // ['a', 'b', 'c']
const arr2 = [...new Set(['b', 'b', 'a', 'b'])];
    // ['b', 'a']
```

### 4.17.3 From apply() to Array.prototype.fill()
### 4.17.3 apply()에서 Array.ptototype.fill()로
The former enables a hack for creating an Array of arbitrary length that is filled with values. The latter provides a cleaner way for doing so (it overwrites all existing elements and treats each hole as if it were the element undefined).

전자는 값으로 채워진 임의 수의 배열 크기를 갖는 배열 생성에 대한 꼼수(hack)를 할 수 있게 하였다. 후자는 이것에 대한 명백한 방법(모든 존재하는 원소를 덥어쓰고 원소가 undefinded 같은 구멍을 다룬다)을 제공한다.

```javascript
// ES5: same as Array(undefined, undefined)
var arr1 = Array.apply(null, new Array(2));
    // [undefined, undefined]
const arr2 = new Array(2).fill(undefined);
    // [undefined, undefined]

var arr3 = Array.apply(null, new Array(2))
    .map(function (x) { return 'x' });
    // ['x', 'x']
const arr3 = new Array(2).fill('x');
    // ['x', 'x']
```
More information: Sect. “Creating Arrays filled with values”
더 자세한 정보: "새로운 배열 기능"장

## 4.18 From CommonJS modules to ES6 modules
## 4.18 CommonJS 모듈에서 ES6모듈로
심지어 ES5에서 모듈 시스템은 AMD 문법 또는 CommonJS문법 기반으로 대부분 "the revealing module pattern"같은 수기로 변경하는 방법을 가졌다.

ES6는 모듈을 내장해서 지원한다. 유감스럽게도 네이티브로 제공하는 자바스크립트 엔진은 없었다. 그러나 브라우져파이, 웹팩 또는 jspm 같은 툴은 당신이 모듈 생성에 대한 ES6 문법을 사용하게 해 주고 당신의 쓴 코드를 미래지향적으로 만들어 준다.

### Multiple export

CommonJS에서 당신은 어려 개체를 익스포트는 다음과 같다.:

```javascript
//------ lib.js ------
var sqrt = Math.sqrt;
function square(x) {
    return x * x;
}
function diag(x, y) {
    return sqrt(square(x) + square(y));
}
module.exports = {
    sqrt: sqrt,
    square: square,
    diag: diag,
};

//------ main1.js ------
var square = require('lib').square;
var diag = require('lib').diag;

console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

그 대신에 당신은 객체로 전체 모듈을 임포트할 수 있고 그것을 통해 square나 diag에 접근할 수 있다.

```javascript
//------ main2.js ------
var lib = require('lib');
console.log(lib.square(11)); // 121
console.log(lib.diag(4, 3)); // 5
```

ES6에서 다중 익스포트는 기명된 익스포트로 불리고 이것 처럼 다룬다.:

```javascript
//------ lib.js ------
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

//------ main1.js ------
import { square, diag } from 'lib';
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

객체 처럼 모듈 임포트하는것에 대한 문법은 아래 (줄 A) 처럼 보인다.:

```javascript
//------ main2.js ------
import * as lib from 'lib'; // (A)
console.log(lib.square(11)); // 121
console.log(lib.diag(4, 3)); // 5
```

### 4.18.2 단일 익스포트
Node.js는 CommonJS를 확장하였고 module.exports를 통해 당신을 모듈로 부터 단일 값으로 익스포트한다.:

```javascript
//------ myFunc.js ------
module.exports = function () { ··· };

//------ main1.js ------
var myFunc = require('myFunc');
myFunc();
```
ES6에서 exprot default를 통해 같은 것을 한다.:

```javascript
//------ myFunc.js ------
export default function () { ··· } // no semicolon!

//------ main1.js ------
import myFunc from 'myFunc';
myFunc();
```
더 자세한 정보: 챕터 "모듈".

## 4.19 다음에는 무엇을 할까
이제 당신은 ES6에 대해 처음으로 맛을 보았고, 당신은 챕터를 검색하여 탐험을 계속 할 수 있다. 각 챕터는 기능이나 관련된 기능의 집합을 다루고 개요와 함께 시작된다.
