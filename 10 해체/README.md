# `Destructuring` 해체 `Destructuring`

## 10.1 `Overview` 개요

`Destructuring is a convenient way of extracting values from data stored in (possibly nested) objects and Arrays. It can be used in locations that receive data (such as the left-hand side of an assignment). How to extract the values is specified via patterns (read on for examples).`

해체는 ( 대게 중첩된 )객체와 배열에 저장된 데이터의 값을 추출하는 편리한 방법이다. 데이터를 받는 위치( 예를 들어 할당문의 좌변 )에서 사용될 수 있다. 값을 추출하는 방법은 패턴으로 명시되어있다. ( 예제를 보자 )

## 10.1.1 `Object destructuring` 객체 해체

`Destructuring objects:` 객체 해체하기.

```javascript
const obj = { first: 'Jane', last: 'Doe' };
const {first: f, last: l} = obj;
// f = 'Jane'; l = 'Doe'

// `{prop} is short for {prop: prop}` {prop} 은 {prop: prop} 의 축약
const {first, last} = obj;
// first = 'Jane'; last = 'Doe'
```    

`Destructuring helps with processing return values:`

해체는 반환값 처리를 도와준다.

```javascript
const obj = { foo: 123 };

const {writable, configurable} = Object.getOwnPropertyDescriptor(obj, 'foo');

console.log(writable, configurable); // true true
```
## 10.1.2 `Array destructuring` 배열해체

`Array destructuring (works for all iterable values):`

배열 해체하기. ( 모든 이터러블 값에 작동한다. )

```javascript
const iterable = ['a', 'b'];
const [x, y] = iterable;
    // x = 'a'; y = 'b'
```

`Destructuring helps with processing return values:`

해체는 반환값 처리를 도와준다.

```javascript
const [all, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec('2999-12-31');
```

## 10.1.3 `Where can destructuring be used?` 해체는 어디서 사용될 수 있는가?

`Destructuring can be used in the following locations:`

해체는 아래 위치에서 사용될 수 있다.

```javascript
// `Variable declarations` 변수 선언:
const [x] = ['a'];
let [x] = ['a'];
var [x] = ['a'];

// `Assignments` 할당:
[x] = ['a'];

// `Parameter definitions:` 파라미터 선언:

function f([x]) { ··· }
f(['a']);
```

`You can also destructure in a for-of loop:`

`for-of` 루프 안에서도 해체할 수 있다.

```javascript
const arr1 = ['a', 'b'];
for (const [index, element] of arr1.entries()) {
    console.log(index, element);
}
// 결과:
// 0 a
// 1 b

const arr2 = [
    {name: 'Jane', age: 41},
    {name: 'John', age: 40},
];

for (const {name, age} of arr2) {
    console.log(name, age);
}
// 결과:
// Jane 41
// John 40
```

## 10.2 `Background: Constructing data versus extracting data` 배경: 데이터 구성 대 데이터 추출

`To fully understand what destructuring is, let’s first examine its broader context. JavaScript has operations for constructing data:`

해체가 무엇인지 완전히 이해하기 위해 먼저 넓은 맥락을 살펴보자. 자바스크립트에는 데이터를 구성하는 연산자가 있다.

```javascript
const obj = {};
obj.first = 'Jane';
obj.last = 'Doe';
```

`And it has operations for extracting data:`

그리고 데이터를 추출하는 연산자가 있다.

```javascript
const f = obj.first;
const l = obj.last;
```

`Note that we are using the same syntax that we have used for constructing.`

데이터 구성에 사용했던 것과 같은 문법을 사용한 것을 주목하라.

`There is nicer syntax for constructing – an object literal:`

객체 리터럴이라는 구성을 위한 더 나은 문법이 있다.

```javascript
const obj = { first: 'Jane', last: 'Doe' };
```

`Destructuring in ECMAScript 6 enables the same syntax for extracting data, where it is called an object pattern:`

`ECMAScript 6`의 해체는 객체 패턴이 호출된 곳에서 데이터를 추출하기 위해 같은 문법을 가능하게 한다.

```javascript
const { first: f, last: l } = obj;
```

`Just as the object literal lets us create multiple properties at the same time, the object pattern lets us extract multiple properties at the same time.`

객체 리터럴이 한번에 여러개의 프로퍼티를 생성하는 것처럼, 객체 패턴은 한번에 여러개의 프로퍼티를 추출해준다.

`You can also destructure Arrays via patterns:`

패턴을 통해 배열을 해체할 수도 있다.

```javascript
const [x, y] = ['a', 'b']; // x = 'a'; y = 'b'
```

##10.3 `Patterns` 패턴

`The following two parties are involved in destructuring:`

다음 두 파트는 해체와 관련이 있다.

- `Destructuring source: the data to be destructured. For example, the right-hand side of a destructuring assignment.`  
  헤체 소스: 재구성될 데이터이다. 예를 들어, 해체 할당의 우변이다.
- `Destructuring target: the pattern used for destructuring. For example, the left-hand side of a destructuring assignment.`  
  해체 타겟: 해체에 사용되는 패턴이다. 예를 들어 해체 할당의 좌변이다.

`The destructuring target is either one of three patterns:`

해체 타겟은 세가지 패턴 중 하나이다.

- `Assignment target. For example: x`  
  할당 대상. 예를 들어 `x`  
  - `In variable declarations and parameter definitions, only references to variables are allowed. In destructuring assignment, you have more options, as I’ll explain later.`  
    변수 선언과 파라미터 정의에서 변수에 대한 참조만 허용된다. 해체 할당에서 더 많은 옵션이 있는데 뒤에 설명한다.
- `Object pattern. For example: { first: «pattern», last: «pattern» }`  
  객체 패턴. 예를 들어 `{ first: <<pattern>>, last: <<pattern>> }`
  - `The parts of an object pattern are properties, the property values are again patterns (recursively).`  
    객체 패턴의 부품은 프로퍼티이고 프로퍼티의 값은 또 다시 패턴이다. ( 재귀 )

- `Array pattern. For example: [ «pattern», «pattern» ]`  
  배열 패턴. 예를 들어 `[ <<pattern>>>, <<pattern>> ]`
  - `The parts of an Array pattern are elements, the elements are again patterns (recursively).`
    배열 패턴의 부품은 원소이고 원소는 또 다시 패턴이다. ( 재귀 )

`That means that you can nest patterns, arbitrarily deeply:`

패턴을 엂마든지 깊게 포함할 수 있는 것이다.

```javascript
const obj = { a: [{ foo: 123, bar: 'abc' }, {}], b: true };
const { a: [{foo: f}] } = obj; // f = 123
```

## 10.3.1 `Pick what you need` 필요한 것을 선택하라

`If you destructure an object, you mention only those properties that you are interested in:`

객체를 재구성하는 경우, 필요한 프로퍼티만 기술한다.

```javascript
const { x: x } = { x: 7, y: 3 }; // x = 7
```

`If you destructure an Array, you can choose to only extract a prefix:`

배열을 재구성하는 경우, 추출할 프리픽스만 선택할 수 있다.

```javascript
const [x,y] = ['a', 'b', 'c']; // x='a'; y='b';
```

## 10.4 `How do patterns access the innards of values?` 패턴은 어떻게 값의 내부에 접근하는가?

`In an assignment ( pattern = someValue ), how does the pattern access what’s inside someValue?`

할당( `패턴` = `어떤값` )에서 패턴은 어떻게 `어떤값`의 내부에 접근하는가?

## 10.4.1 `Object patterns coerce values to objects` 객체 패턴은 값을 객체로 강제한다

`The object pattern coerces destructuring sources to objects before accessing properties. That means that it works with primitive values:`

객체 패턴은 프로퍼티에 접근하기 전에 소스, 즉 원시값을 객체로 강제 변환한다.

```javascript
const {length : len} = 'abc'; // len = 3
const {toString: s} = 123; // s = Number.prototype.toString
```

## 10.4.1.1 `Failing to object-destructure a value` 값을 객체 해체의 실패

`The coercion to object is not performed via Object(), but via the internal operation ToObject(). Object() never fails:`

객체로 강제 변환은 `Object()`가 아닌 내장 연산자 `TOObject()`를 통해 수행된다. `Object()`는 절대 실패하지 않는다.

```javascript
> typeof Object('abc')
'object'
> var obj = {};
> Object(obj) === obj
true
> Object(undefined)
{}
> Object(null)
{}
```

`ToObject() throws a TypeError if it encounters undefined or null. Therefore, the following destructurings fail, even before destructuring accesses any properties:`

`ToObject()`는 `undefined`나 `null`을 만나면 `TypeError`를 발생시키므로, 해체는 임의의 프로퍼티에 접근하기 전에 실패한다.

```javascript
const { prop: x } = undefined; // TypeError
const { prop: y } = null; // TypeError
```

`As a consequence, you can use the empty object pattern {} to check whether a value is coercible to an object. As we have seen, only undefined and null aren’t:`

결과적으로, 값이 객체로 강제변환 될 수 있는지 빈 객체 패턴 `{}`을 사용해 체크할 수 있다. 보았듯이 `undefined`와 `null`만이 강제변환 되지 않는다.

```javascript
({} = [true, false]); // `OK, Arrays are coercible to objects` OK, 배열은 객체로 강제 변환 가능하다
({} = 'abc'); // `OK, strings are coercible to objects` OK, 문자열은 객체로 강제 변환 가능하다

({} = undefined); // TypeError
({} = null); // TypeError
```

`The parentheses around the expressions are necessary because statements must not begin with curly braces in JavaScript (details are explained later).`

표현식을 둘러싼 괄호는 자바스크립트에서 문이 중괄호로 시작하지 않아야 하기 때문에 필수이다 ( 자세한 내용은 뒤에 설명한다 ).

## 10.4.2 `Array patterns work with iterables` 배열 패턴은 이터러블과 작동한다.

`Array destructuring uses an iterator to get to the elements of a source. Therefore, you can Array-destructure any value that is iterable. Let’s look at examples of iterable values.`

배열 해체는 소스의 원소를 얻기위해 반복자를 사용한다. 따라서 이터러블인 임의의 값은 배열 해체할 수 있다. 이터러블 값의 예를 보자.

`Strings are iterable:`

문자열은 이터러블이다.

```javascript
const [x,...y] = 'abc'; // x='a'; y=['b', 'c']
```

`Don’t forget that the iterator over strings returns code points (“Unicode characters”, 21 bits), not code units (“JavaScript characters”, 16 bits). (For more information on Unicode, consult the chapter “Chapter 24. Unicode and JavaScript” in “Speaking JavaScript”.) For example:`

문자열의 반복자는 코드 단위( 16비트 자바스크립트 문자 )가 아닌 코드 포인트( 21비트 유니코드 문자 )를 반환하는 것을 잊지 말라. ( 유니코드에 대한 자세한 내용은 `자바스크립트를 말하다`의 24장 유니코드와 자바스크립트를 참조 ) 

예를 들어.

```javascript
const [x,y,z] = 'a\uD83D\uDCA9c'; // x='a'; y='\uD83D\uDCA9'; z='c'
```

`You can’t access the elements of a Set via indices, but you can do so via an iterator. Therefore, Array destructuring works for Sets:`

인덱스를 통해 `Set`의 요소에 접근할 수는 없지만 이터레이터를 통해서는 가능하다. 따라서 배열 해체는 `Set`에서도 동작한다.

```javascript
const [x,y] = new Set(['a', 'b']); // x='a'; y='b’;
```

`The Set iterator always returns elements in the order in which they were inserted, which is why the result of the previous destructuring is always the same.`

`Set` 이터레이터는 항상 이전 해체의 결과와 동일하게 삽입된 순서대로 요소를 반환한다.

`Infinite sequences. Destructuring also works for iterators over infinite sequences. The generator function allNaturalNumbers() returns an iterator that yields 0, 1, 2, etc.`

무한 시퀀스. 해체는 무한 시퀀스 상의 이터레이터에도 작동한다. 제너레이터 함수 `allNaturalNumbers`는 0, 1, 2 등의 `yield` 이터레이터를 반환한다.

```javascript
function* allNaturalNumbers() {
  for (let n = 0; ; n++) {
    yield n;
  }
}
```

`The following destructuring extracts the first three elements of that infinite sequence.`

다음 해체는 무한 시퀀스의 처음 세 요소 추출한다.

```javascript
const [x, y, z] = allNaturalNumbers(); // x=0; y=1; z=2
```

## 10.4.2.1 `Failing to Array-destructure a value` 값을 배열 해체의 실패

`A value is iterable if it has a method whose key is Symbol.iterator that returns an object. Array-destructuring throws a TypeError if the value to be destructured isn’t iterable:`

키가 `Symbol.iterator`이고 객체를 리턴하는 함수가 있는 값은 이터러블이다.
배열 해체는 해체할 값이 이터러블이 아닌 경우 `TypeError`를 발생시킨다.

```javascript
let x;
[x] = [true, false]; // `OK, Arrays are iterable` 정상, 배열은 이터러블이다
[x] = 'abc'; // `OK, strings are iterable` 정상, 문자열은 이터러블이다.
[x] = { * [Symbol.iterator]() { yield 1 } }; // `OK, iterable` 정상, 이터러블

[x] = {}; // `TypeError, empty objecdts are not iterable` TypeError, 빈 객체는 이터러블이 아니다
[x] = undefined; // `TypeError, not iterable` TypeError, 이터러블이 아니다
[x] = null; // `TypeError, not iterable` TypeError, 이터러블이 아니다
```

`The TypeError is thrown even before accessing elements of the iterable, which means that you can use the empty Array pattern [] to check whether a value is iterable:`

`TypeError`는 이터러블의 요소에 접근하기도 전에 발생하므로 빈 배열 패턴 `[]`을 사용하여 값이 이터러블인지 검사할 수 있다.

```javascript
[] = {}; // `TypeError, empty objects are not iterable` TypeError, 빈 객체는 이터러블이 아니다
[] = undefined; // `TypeError, not iterable` TypeError, 이터러블이 아니다
[] = null; // `TypeError, not iterable` TypeError, 이터러블이 아니다
```

## 10.5 `If a part has no match` 일치하는 부분이 없다면

`Similarly to how JavaScript handles non-existent properties and Array elements, destructuring fails silently if the target mentions a part that doesn’t exist in the source: the interior of the part is matched against undefined. If the interior is a variable that means that the variable is set to undefined:`

자바스크립트가 존재하지 않는 프로퍼티와 배열 요소를 처리하는 방법과 비슷하게, 해체는 소스에 없는 파트가 타겟에 기술되어 있으면 조용히 실패한다. 파트의 내부는 `undefined`와 맞춰보게 되었다. 내부가 변수라면 변수는 `undefined`로 설정된다.

```javascript
const [x] = []; // x = undefined
const {prop:y} = {}; // y = undefined
```

`Remember that object patterns and Array patterns throw a TypeError if they are matched against undefined.`

객체 패턴과 배열 패턴은 `undefined`와 맞춰보게되면 `TypeError`를 발생시키는 것을 기억하라.

## 10.5.1 `Default values` 기본값

`Default values are a feature of patterns: If a part (an object property or an Array element) has no match in the source, it is matched against:`

파트( 객체 프로퍼티 또는 배열 요소 )가 소스에서 일치하지 않으면 기본값에 일치하는 것이 패턴의 특징이다.

- `its default value (if specified)`  
  기본값 ( 지정된 경우 )
- `undefined (otherwise)`  
  undefined ( 그 외 )

`That is, providing a default value is optional.`

기본값을 제공하는 것은 선택적이다.

`Let’s look at an example. In the following destructuring, the element at index 0 has no match on the right-hand side. Therefore, destructuring continues by matching x against 3, which leads to x being set to 3.`

예제를 살펴 보자. 다음 해체에서 인덱스 0의 요소는 우변에 일치가 없다. 따라���, 해체는 x를 3에 일치시킴으로 계속된다.

```javascript
const [x=3, y] = []; // x = 3; y = undefined
```

`You can also use default values in object patterns:`

객체 패턴 또한 기본 값을 사용할 수 있다.

```javascript
const {foo: x=3, bar: y} = {}; // x = 3; y = undefined
```

## 10.5.1.1 `undefined triggers default values` `undefined`는 기본값을 트리거한다

`Default values are also used if a part does have a match and that match is undefined:`

기본값은 파트가 일치하는 항목이 `undefined`인 경우에도 사용된다.

```javascript
const [x=1] = [undefined]; // x = 1
const {prop: y=2} = {prop: undefined}; // y = 2
```

`The rationale for this behavior is explained in the next chapter, in the section on parameter default values.`

이 문제에 대한 이론적 근거는 파라미터 기본값을 다루는 다음 절에 설명되어있다.

## 10.5.1.2 `Default values are computed on demand` 기본값은 필요에 따라 연산된다

`The default values themselves are only computed when they are needed. In other words, this destructuring:`

기본값은 필요할 때만 연산된다. 다시 말해, 이 해체는.

```javascript
const {prop: y=someFunc()} = someValue;
```

`is equivalent to:`

이것과 동일하다.

```javascript
let y;
if (someValue.prop === undefined) {
    y = someFunc();
} else {
    y = someValue.prop;
}
```

`You can observe that if you use console.log():`

`console.log()`를 사용하여 확인할 수 있다.

```javascript
> function log(x) { console.log(x); return 'YES' }

> const [a=log('hello')] = [];
hello
> a
'YES'

> const [b=log('hello')] = [123];
> b
123
```

`In the second destructuring, the default value is not triggered and log() is not called.`

두번째 해체에서 기본값은 트리거되지 않았고 `log()`는 호출되지 않았다.

## 10.5.1.3 `Default values can refer to other variables in the pattern` 기본 값은 패턴의 다른 변수를 참조 할 수 있다

`A default value can refer to any variable, including another variable in the same pattern:`

기본값은 같은 패턴 내에 포함된 다른 어떤 변수든 참조할 수 있다.

```javascript
const [x=3, y=x] = [];     // x=3; y=3
const [x=3, y=x] = [7];    // x=7; y=7
const [x=3, y=x] = [7, 2]; // x=7; y=2
```

`However, order matters: the variables x and y are declared from left to right and produce a ReferenceError if they are accessed before their declaration:`

하지만, 변수 `x`와 `y`는 좌측에서 우측으로 선언되었고 변수의 선언전에 접근하면 `ReferenceError`를 생성하는 순서 문제가 있다.

```javascript
const [x=y, y=3] = []; // ReferenceError
```

## 10.5.1.4 `Default values for patterns` 패턴의 기본값

`So far we have only seen default values for variables, but you can also associate them with patterns:`

지금까지 변수에 대한 기본값만 살펴보았지만 패턴에도 연결지을 수 있다.

```javascript
const [{ prop: x } = {}] = [];
```

`What does this mean? Recall the rule for default values:`

이것은 무엇을 의미 하는가? 기본값에 대한 규칙을 떠올려보자.

> `If the part has no match in the source, destructuring continues with the default value […].`  
> 소스 내에 일치가없는 경우, 해체는 기본값으로 처리한다.

> ???

`The element at index 0 has no match, which is why destructuring continues with:`

인덱스 0번째의 요소는 해체가 계속될 일치가 없기 때문이다.

```javascript
const { prop: x } = {}; // x = undefined
```

`You can more easily see why things work this way if you replace the pattern { prop: x } with the variable pattern:`

패턴 `{ prop: x }`를 변수 패턴으로 교체하면 이런 식으로 동작하는 것을 더 쉽게 볼 수 있다.
 
```javascript
const [pattern = {}] = [];
```

`More complex default values. Let’s further explore default values for patterns. In the following example, we assign a value to x via the default value { prop: 123 }:`

패턴의 더 복잡한 기본값을 알아보자. 다음 예에서 기본값 `{ prop: 123 }`을 통해 값을 x로 할당한다.

```javascript
const [{ prop: x } = { prop: 123 }] = [];
```

`Because the Array element at index 0 has no match on the right-hand side, destructuring continues as follows and x is set to 123.`

인덱스 0번째의 배열 요소가 우변에 일치가 없기 때문에 해체는 다음처럼 계속되고 `x`는 123으로 설정된다.

```javascript
const { prop: x } = { prop: 123 };  // x = 123
```

`However, x is not assigned a value in this manner if the right-hand side has an element at index 0, because then the default value isn’t triggered.`

하지만 우변의 인덱스 0번째에 요소가 있다면 기본값이 트리거되지 않기 때문에 `x`는 이런 방식으로 값이 할당되지 않는다.

```javascript
const [{ prop: x } = { prop: 123 }] = [{}];
```

`In this case, destructuring continues with:`

이 경우, 해체는 진행된다.

```javascript
const { prop: x } = {}; // x = undefined
```

`Thus, if you want x to be 123 if either the object or the property is missing, you need to specify a default value for x itself:`

따라서, 객체 또는 프로퍼티가 빠졌더라도 `x`가 123이 되길 원한다면 기본값을 지정할 필요가 있다.

```javascript
const [{ prop: x=123 } = {}] = [{}];
```

`Here, destructuring continues as follows, independently of whether the right-hand side is [{}] or [].`

이제 해체는 우변이 `[{}]` 또는 `[]`이더라도 다음과 같이 독립적으로 진행된다.

```javascript
const { prop: x=123 } = {}; // x = 123
```

> *:notebook: `Still confused?`  
  여전히 혼란스러운가?
> `A later section explains destructuring from a different angle, as an algorithm. That may give you additional insight.`  
  다음 절은 다른 관점인 알고리즘으로 해체를 설명한다. 추가적인 인사이트를 얻을 수 있을 것이다.

## 10.6 `More object destructuring features` 더 많은 객체 해체 기능

## 10.6.1 `Property value shorthands` 프로퍼티 값 축약

`Property value shorthands are a feature of object literals: If the value of a property is provided via a variable whose name is the same as the key, you can omit the key. This works for destructuring, too:`

프로퍼티의 값이 이름과 키가 같은 변수를 통해 제공되는 경우, 키를 생략할 수 있는 프로퍼티 값 축약은 객체 리터럴의 특징이다. 해체에서도 동작한다.

```javascript
const { x, y } = { x: 11, y: 8 }; // x = 11; y = 8
```

`This declaration is equivalent to:`

이 선언은 아래와 동일하다 :

```javascript
const { x: x, y: y } = { x: 11, y: 8 };
```

`You can also combine property value shorthands with default values:`

또한 프로퍼티 값 축약을 기본값과 결합할 수 있다.

```javascript
const { x, y = 1 } = {}; // x = undefined; y = 1
```

## 10.6.2 `Computed property keys` 계산된 프로퍼티 키

`Computed property keys are another object literal feature that also works for destructuring: You can specify the key of a property via an expression, if you put it in square brackets:`

계산된 프로퍼티 키는 표현식으로 프로퍼티의 키를 괄호안에 넣으면 지정할 수 있는 해체에서도 동작하는 다른 객체 리터럴 기능이다. 

```javascript
const FOO = 'foo';
const { [FOO]: f } = { foo: 123 }; // f = 123
```

`Computed property keys allow you to destructure properties whose keys are symbols:`

계산된 프로퍼티 키는 키가 심볼인 프로퍼티를 추출하게 해준다.

```javascript
// `Create and destructure a property whose key is a symbol` 키가 심볼인 프로퍼티를 생성하고 해체한다
const KEY = Symbol();
const obj = { [KEY]: 'abc' };
const { [KEY]: x } = obj; // x = 'abc'

// `Extract Array.prototype[Symbol.iterator]` Array.prototype[Symbol.iterator] 추출
const { [Symbol.iterator]: func } = [];
console.log(typeof func); // function
```

## 10.7 `More Array destructuring features` 더 많은 배열 해체 기능

## 10.7.1 `Elision` 생략

`Elision lets you use the syntax of Array “holes” to skip elements during destructuring:`

생략은 해체하는 동안에 요소를 스킵하기 위해 배열의 "holes" 문법을 사용할 수 있게 된다.

```javascript
const [,, x, y] = ['a', 'b', 'c', 'd']; // x = 'c'; y = 'd'
```

## 10.7.2 `Rest operator (...)` 나머지 연산자 (...)

`The rest operator lets you extract the remaining elements of an Array into an Array. You can only use the operator as the last part inside an Array pattern:`

나머지 연산자를 사용하면 배열의 남겨진 요소를 배열로 추출할 수 있다. 배열 패턴의 마지막 부분에서만 연산자를 사용할 수 있다.

```javascript
const [x, ...y] = ['a', 'b', 'c']; // x='a'; y=['b', 'c']
```

> *:notebook: `The rest operator operator extracts data. The same syntax (...) is used by the spread operator, which contributes data to Array literals and function calls and is explained in the next chapter.`  
  나머지 연산자는 펼치기 연산자와 같은 문법을 사용하여 데이터를 추출한다.
  ???
  
`If the operator can’t find any elements, it matches its operand against the empty Array. That is, it never produces undefined or null. For example:`

연산자가 아무 요소도 발견하지 못하면, 빈 배열의 피연산자와 일치한다. 즉, 절대 `undefined`나 `null`을 생성하지 않는다. 예를 들면.

```javascript
const [x, y, ...z] = ['a']; // x='a'; y=undefined; z=[]
```

`The operand of the rest operator doesn’t have to be a variable, you can use patterns, too:`

나머지 연산자의 피연산자는 패턴을 사용할 수 있는 변수가 될 필요가 없다.

```javascript
const [x, ...[y, z]] = ['a', 'b', 'c'];
    // x = 'a'; y = 'b'; z = 'c'
```

`The rest operator triggers the following destructuring:`

나머지 연산자는 다음 해체를 트리거한다.

```javascript
[y, z] = ['b', 'c']
```

> *:notebook: `The spread operator (...) looks exactly like the rest operator, but it is used inside function calls and Array literals (not inside destructuring patterns).`  
  펼치기 연산자 (...)는 정확히 나머지 연사자처럼 보이지만, 해체 패턴이 아닌 함수 호출과 배열 리터럴 안에서 사용된다.

## 10.8 `You can assign to more than just variables` 변수보다 더 많이 할당할 수 있다.

`If you assign via destructuring, each assignment target can be everything that is allowed on the left-hand side of a normal assignment, including a reference to a property (obj.prop) and a reference to an Array element (arr[0]).`

해체를 통해 할당하는 경우, 각각의 할당 대상은 프로퍼티의 참조( `obj.prop` )와 배열 요소의 참조( `arr[0]` )를 포함한 일반적인 할당의 좌변에 올 수 있는 모든 것이 될 수 있다.

```javascript
const obj = {};
const arr = [];

({ foo: obj.prop, bar: arr[0] }) = { foo: 123, bar: true };

console.log(obj); // {prop:123}
console.log(arr); // [true]
```

`You can also assign to object properties and Array elements via the rest operator (...):`

또한 나머지 연산자( `...` )를 통해 객체 프로퍼티와 배열 요소에 할당할 수도 있다.

```javascript
const obj = {};
[first, ...obj.prop] = ['a', 'b', 'c'];
    // first = 'a'; obj.prop = ['b', 'c']
```

`If you declare variables or define parameters via destructuring then you must use simple identifiers, you can’t refer to object properties and Array elements.`

해체를 통해 변수를 선언하거나 파라미터를 정의할 때 객체 프로퍼티와 배열 요소를 참조할 수 없으므로 간단한 식별자를 사용해야 한다.

##10.9 `Pitfalls of destructuring` 해체의 함정

`There are two things to be mindful of when using destructuring:`

해체를 사용할 때 염두할 것 두 가지가 있다.

- `You can’t start a statement with a curly brace.`  
  중괄호로 문을 시작할 수 없다.
- `During destructuring, you can either declare variables or assign to them, but not both.`  
  해체하는 동안, 변수를 선언, 할당할 수 있지만 둘을 다 할 수는 없다.

`The next two sections have the details.`

다음 두 절에서 자세히 다룬다.

## 10.9.1 `Don’t start a statement with a curly brace` 중괄호로 문을 시작하지 말라

`Because code blocks begin with a curly brace, statements must not begin with one. This is unfortunate when using object destructuring in an assignment:`

불행히도 할당에서 객체 해체를 사용하는 경우, 코드 블록이 중괄호로 시작하기 때문에 문은 중괄호로 시작하지 않아야 한다.

```javascript
{ a, b } = someObject; // SyntaxError
```

`The work-around is to put the complete expression in parentheses:`

해결책은 괄호 안에 완전한 표현식을 넣는 것이다.

```javascript
({ a, b } = someObject); // ok
```

`The following syntax does not work:`

다음 구문은 작동하지 않는다.

```javascript
({ a, b }) = someObject; // SyntaxError
```

`With let, var and const, curly braces never cause problems:`

`let`, `var`와 `const`를 사용할 때, 중괄호는 문제가 되지 않는다.

```javascript
const { a, b } = someObject; // OK
```

## 10.9.2 `You can’t mix declaring and assigning to existing variables` 기존 변수에 선언과 할당을 섞어 사용할 수 없다.

`Within a destructuring variable declaration, every variable in the source is declared. In the following example, we are trying to declare the variable b and refer to the existing variable f, which doesn’t work.`

변수 선언을 해체할 때, 소스의 모든 변수는 선언되었다. 다음 예제는 변수 `b`를 선언하고 동작하지 않는 기존 변수 `f`를 참조하려고 하고있다.

```javascript
let f;
···
let { foo: f, bar: b } = someObject;
    // `During parsing (before running the code):` 코드를 실행하기 전 파싱하는 동안에
    // `SyntaxError: Duplicate declaration, f` SyntaxError: 중복된 선언
```

`The fix is to use a destructuring assignment and to declare b beforehand:`

해결책은 해체 할당을 사용하고 `b`를 이전에 선언하는 것이다.

```javascript
let f;
···
let b;
({ foo: f, bar: b }) = someObject;
```

## 10.10 `Examples of destructuring` 해체의 예

`Let’s start with a few smaller examples.`

몇 가지 작은 예제를 살펴보자.

`The for-of loop supports destructuring:`

`for-of` 루프는 해체를 지원한다.

```javascript
const map = new Map().set(false, 'no').set(true, 'yes');
for (const [key, value] of map) {
  console.log(key + ' is ' + value);
}
```

`You can use destructuring to swap values. That is something that engines could optimize, so that no Array would be created.`

값을 교환하기 위해 해체를 사용할 수 있다. 엔진이 최적화 하기 때문에 배열이 생성되지 않는다.

```javascript
[a, b] = [b, a];
```

`You can use destructuring to split an Array:`

배열을 분할하기 위해 해체를 사용할 수 있다.

```javascript
const [first, ...rest] = ['a', 'b', 'c'];
    // first = 'a'; rest = ['b', 'c']
```

## 10.10.1 `Destructuring returned Arrays` 반환된 배열을 해체

`Some built-in JavaScript operations return Arrays. Destructuring helps with processing them:`

일부 내장 자바스크립트 연산자는 배열을 반환합니다. 해체는 반환값을 처리하는데 도움이 됩니다.

```javascript
const [all, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec('2999-12-31');
```

`If you are only interested in the groups (and not in the complete match, all), you can use elision to skip the array element at index 0:`

전체 `match`가 아닌 매칭된 그룹만 얻고 싶다면 배열의 인덱스 0에 있는 요소를 스킵하기 위해 `elision`을 사용할 수 있다.

```javascript
const [, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec('2999-12-31');
```

`exec() returns null if the regular expression doesn’t match. Unfortunately, you can’t handle null via default values, which is why you must use the Or operator (||) in this case:`

`exec()`는 정규 표현식에 일치하는 부분이 없으면 `null`을 반환한다. 안타깝게도 `null`을 기본 값으로 처리할 수는 없다. 이런 경우에는 `or` 연산자( `||` )를 사용해야만 한다.

```javascript
const [, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec(someStr) || [];
```

`Array.prototype.split() returns an Array. Therefore, destructuring is useful if you are interested in the elements, not the Array:`

`Array.prototype.split()`는 배열을 반환한다. 따라서 배열이 아닌 요소만을 원할때 해체는 유용하다.

```javascript
const cells = 'Jane\tDoe\tCTO'
const [firstName, lastName, title] = cells.split('\t');
console.log(firstName, lastName, title);
```

## 10.10.2 `Destructuring returned objects` 반환된 객체를 해체

`Destructuring is also useful for extracting data from objects that are returned by functions or methods. For example, the iterator method next() returns an object with two properties, done and value. The following code logs all elements of Array arr via the iterator iter. Destructuring is used in line A.`

해체는 또한 함수 또는 메소드로부터 반환된 객체의 데이터를 추출하는데 유용하다. 예를 들어, 이터레이터 메소드 `next()`는 `done`과 값, 두개의 프로퍼티를 가진 객체를 반환한다. 다음 코드는 이터레이터 `iter`를 통해 배열 `arr`의 모든 요소를 출력한다. 해체는 `(A)`라인에서 사용되었다.

```javascript
const arr = ['a', 'b'];
const iter = arr[Symbol.iterator]();
while (true) {
    const {done,value} = iter.next(); // (A)
    if (done) break;
    console.log(value);
}
```

## 10.10.3 `Array-destructuring iterable values` 이터러블 값을 배열 해체

`Array-destructuring works with any iterable value. That is occasionally useful:`

배열 해체는 모든 이터러블 값에 작동하며 종종 유용하다.

```javascript
const [x,y] = new Set().add('a').add('b');
    // x = 'a'; y = 'b'

const [a,b] = 'foo';
    // a = 'f'; b = 'o'
```

## 10.10.4 `Multiple return values` 여러 반환값

`To see the usefulness of multiple return values, let’s implement a function findElement(a, p) that searches for the first element in the Array a for which the function p returns true. The question is: what should that function return? Sometimes one is interested in the element itself, sometimes in its index, sometimes in both. The following implementation returns both.`

여러 반환값의 유용함 확인하기 위해, 배열의 첫번째 요소를 찾는 `findElement(a, p)` 함수를 구현해보자. 문제는 이 함수는 무엇을 반환할 것인가? 때로는 요소 자체가 필요할 때도 있고, 요소의 인덱스나 또는 둘 다 필요할 것이다. 아래 구현은 둘 다 반환한다.

```javascript
function findElement(array, predicate) {
    for (const [index, element] of array.entries()) { // (A)
        if (predicate(element)) {
            return { element, index }; // (B)
        }
    }
    return { element: undefined, index: -1 };
}
```

`In line A, the Array method entries() returns an iterable over [index,element] pairs. We destructure one pair per iteration. In line B, we use property value shorthands to return the object { element: element, index: index }.`

`(A)`라인에서, 배열 메소드 `entries`는 인덱스, 요소 쌍의 이터러블을 반환하고 반복마다 하나의 쌍을 해체한다. `(B)` 라인에서는 `{ 요소: 요소, 인덱스: 인덱스 }` 형태의 객체를 리턴하기 위해 프로퍼티 값 축약을 사용한다.

`Let’s use findElement(). In the following example, several ECMAScript 6 features allow us to write more concise code: The callback is an arrow function, the return value is destructured via an object pattern with property value shorthands.`

`findElement()`를 사용해 보자. 다음 예에서 여러가지 `ECMAScript 6` 기능은 더 간결한 코드를 작성하게 한다. 콜백은 프로퍼티 값 축약과 객체 패턴을 통해 해체된 반환값을 가진 화살표 함수이다.

```javascript
const arr = [7, 8, 6];
const {element, index} = findElement(arr, x => x % 2 === 0);
    // element = 8, index = 1
```

`Due to index and element also referring to property keys, the order in which we mention them doesn’t matter:`

또한 인덱스와 요소 또한 프로퍼티 키를 참조할 때, 기술한 순서는 중요하지 않습니다.

```javascript
const {index, element} = findElement(···);
```

`We have successfully handled the case of needing both index and element. What if we are only interested in one of them? It turns out that, thanks to ECMAScript 6, our implementation can take care of that, too. And the syntactic overhead compared to functions with single return values is minimal.`

성공적으로 인덱스와 요소 모두를 필요로하는 경우를 처리했다. `ECMAScript 6` 덕분에 둘 중 하나만 필요한 경우에 위의 구현으로 처리가 가능하고 단일 리턴값을 가진 함수와 비교하여 구문 오버헤드가 최소화된다. 

```javascript
const a = [7, 8, 6];

const {element} = findElement(a, x => x % 2 === 0);
    // element = 8

const {index} = findElement(a, x => x % 2 === 0);
    // index = 1
```

`Each time, we only extract the value of the one property that we need.`

매번, 필요한 하나의 프로퍼티 값을 추출한다.

## 10.11 `The destructuring algorithm` 해체 알고리즘

`This section looks at destructuring from a different angle: as a recursive pattern matching algorithm.`

이 절에서는 해체를 재귀 패턴 매칭 알고리즘이라는 다른 각도로 살펴본다.

> *:notebook: `This different angle should especially help with understanding default values. If you feel you don’t fully understand them yet, read on.`  
  다른 각도로 살펴보는 것은 결국 기본값을 이해하는 것에 도움이 될 것이다. 아직 완전히 이해가 되지 않았다면, 읽어보기 바란다.

`At the end, I’ll use the algorithm to explain the difference between the following two function declarations.`

마지막에 다음 두 함수 선언의 차이를 설명하기위한 알고리즘을 사용할 것이다.

```javascript
function move({x=0, y=0} = {})         { ··· }
function move({x, y} = { x: 0, y: 0 }) { ··· }
```

## 10.11.1 `The algorithm` 알고리즘

`A destructuring assignment looks like this:`

해체 할당은 다음과 같다.

`«pattern» = «value»`

`We want to use pattern to extract data from value. I’ll now describe an algorithm for doing so, which is known in functional programming as pattern matching (short: matching). The algorithm specifies the operator ← (“match against”) for destructuring assignment that matches a pattern against a value and assigns to variables while doing so:`

> The algorithm specifies the operator ← (“match against”) 알고리즘은 연산자 ← ( ~에 일치 )를 사용한다.
for destructuring assignment 해체할당을 위해
that matches a pattern against a value and assigns to variables 값과 변수에 할당에 대한 패턴과 일치 하는 // 패턴이 값과 변수 할당에 일치하는 해체 할당을 위해
while doing so:` 그렇게 하는 동안 // 설명에서는 

값에서 데이터를 추출하기 위해 패턴을 사용하려한다. 지금은 그러기위한 함수형 프로그래밍에서 패턴 매칭( 매칭 )으로 알려진 알고리즘을 설명할 것이다. 설명중에 알고리즘은 패턴이 값과 변수 할당에 일치하는 해체 할당을 연산자 ← ( ~에 일치 )로 명시한다.

`«pattern» ← «value»`

`The algorithm is specified via recursive rules that take apart both operands of the ← operator. The declarative notation may take some getting used to, but it makes the specification of the algorithm more concise. Each rule has two parts:`

알고리즘은 ← 의 연산자와 피 연산자 부분 둘다 적용되는 재귀 규칙을 통해 명시된다. 선언적 표기가 익숙할 수도 있지만, 더 간결한 알고리즘의 명세를 만든다. 각 규칙은 두 부분으로 구성되어 있다.

- `The head specifies which operands are handled by the rule.`  
  헤드는 규칙에 의해 처리되는 피연산자를 지정한다.
- `The body specifies what to do next.`  
  바디는 다음 할 것을 지정한다.

`I only show the algorithm for destructuring assignment. Destructuring variable declarations and destructuring parameter definitions work similarly.`

해체 할당의 알고리즘만 본다. 변수 선언 해체와 파라미터 정의 해체도 비슷하게 작동한다.

`I don’t cover advanced features (computed property keys; property value shorthands; object properties and array elements as assignment targets), either. Only the basics.`

기본을 벗어나는 고급 기능들( 계산된 프로퍼티 키, 프로퍼티 값 축약, 할당 타겟으로서의 객체 프로퍼티와 배열 요소 )을 포함하지 않는다.

``

## 10.11.1.1 `Patterns` 패턴

`A pattern is either:`

패턴은 다음 중 하나이다.

- `A variable: x`  
  변수 `x`
- `An object pattern: {«properties»}`  
  객체 패턴 `{«properties»}`
- `An Array pattern: [«elements»]`  
  배열 패턴 : `[«elements»]`

`Each of the following sections describes one of these three cases.`

다음 각 절은 이 세 가지를 하나씩 살펴본다.

## 10.11.1.2 `Variable` 변수
- `(1) x ← value (including undefined and null)`  
  (1) `x` ← 값 ( `undefined`와 `null`을 포함한 )  
  `x = value`
  x = value

## 10.11.1.3 `Object pattern` 객체패턴

- `(2a) {«properties»} ← undefined`  
  (2a) {«properties»} ← undefined  
  `throw new TypeError();`  
  throw new TypeError();
- `(2b) {«properties»} ← null`  
  (2b) {«properties»} ← null  
  `throw new TypeError();`
  throw new TypeError();
- `(2c) {key: «pattern», «properties»} ← obj`  
  (2c) {key: «pattern», «properties»} ← obj
  `«pattern» ← obj.key`  
  «pattern» ← obj.key
  `{«properties»} ← obj`
  {«properties»} ← obj
- `(2d) {key: «pattern» = default_value, «properties»} ← obj`  
  (2d) {key: «pattern» = default_value, «properties»} ← obj
  ```javascript
  const tmp = obj.key;
  if (tmp !== undefined) {
      «pattern» ← tmp
  } else {
      «pattern» ← default_value
  }
  ```
  `{«properties»} ← obj`  
  {«properties»} ← obj
- `(2e) {} ← obj`
  (2e) {} ← obj
  `// No properties left, nothing to do` 
  // 아무 프로퍼티도 남지 않으므로 아무것도 안함

## 10.11.1.4 `Array pattern` 배열 패턴

`Array pattern and iterable. The algorithm for Array destructuring starts with an Array pattern and an iterable:`

배열 패턴과 이터러블. 배열 해체의 알고리즘은 배열 패턴과 이터러블으로 시작한다.

- `(3a) [«elements»] ← non_iterable`  
  (3a) [«elements»] ← non_iterable
  `assert(!isIterable(non_iterable))`  
  assert(!isIterable(non_iterable))
  `throw new TypeError();`
  throw new TypeError();
- `(3b) [«elements»] ← iterable`  
  (3b) [«elements»] ← iterable
  `assert(isIterable(iterable))`  
  assert(isIterable(iterable))
  `const iterator = iterable[Symbol.iterator]();`  
  const iterator = iterable[Symbol.iterator]();
  `«elements» ← iterator`
  «elements» ← iterator

`Helper function:`

헬퍼 함수:  

```javascript
function isIterable(value) {
    return (value !== null
        && typeof value === 'object'
        && typeof value[Symbol.iterator] === 'function');
}
```

`Array elements and iterator. The algorithm continues with the elements of the pattern (left-hand side of the arrow) and the iterator that was obtained from the iterable (right-hand side of the arrow).`

배열 요소와 이터레이터. 알고리즘은 패턴( 화살표의 좌변 )의 요소와 이터러블에서 얻어온 이터레이터( 화살표의 우변 )와 계속된다.

- `(3c) «pattern», «elements» ← iterator`  
  (3c) «pattern», «elements» ← iterator
  `«pattern» ← getNext(iterator) // undefined after last item`  
  «pattern» ← getNext(iterator) // undefined after last item
  `«elements» ← iterator`  
  «elements» ← iterator
- `(3d) «pattern» = default_value, «elements» ← iterator`  
  (3d) «pattern» = default_value, «elements» ← iterator
  ```javascript
  const tmp = getNext(iterator);  // undefined after last item
  if (tmp !== undefined) {
      «pattern» ← tmp
  } else {
      «pattern» ← default_value
  }
  ```
  `«elements» ← iterator`
  «elements» ← iterator
- `(3e) , «elements» ← iterator (hole, elision)`  
  (3e) , «elements» ← iterator (hole, elision)
  `getNext(iterator); // skip`  
  getNext(iterator); // skip
  `«elements» ← iterator`
  «elements» ← iterator
- `(3f) ...«pattern» ← iterator (always last part!)`  
  (3f) ...«pattern» ← iterator (always last part!)
  ```javascript
  const tmp = [];
  for (const elem of iterator) {
      tmp.push(elem);
  }
  ```
  `«pattern» ← tmp`
  «pattern» ← tmp
- `(3g) ← iterator`  
  (3g) ← iterator
  `// No elements left, nothing to do`  
  // 아무 요소도 남지 않으므로 아무일도 하지 않는다.

`Helper function:`

```javascript
function getNext(iterator) {
    const {done,value} = iterator.next();
    return (done ? undefined : value);
}
```

## 10.11.2 `Applying the algorithm` 알고리즘 적용

`The following function definition has named parameters, a technique that is sometimes called options object and explained in the chapter on parameter handling. The parameters use destructuring and default values in such a way that x and y can be omitted. But the object with the parameter can be omitted, too, as you can see in the last line of the code below. This feature is enabled via the = {} in the head of the function definition.`

다음 함수 정의에는 종종 옵션 객체라고 불리는 테크닉인 파라미터라는 것이 있으며, `파라미터 다루기` 장에서 다룬다. 파라미터가 해체와 기본값을 사용할 경우 `x`와 `y`는 생략될 수 있다. 하지만 코드의 마지막 줄에서 볼 수 있듯이 파라미터의 객체 역시 생략될 수 있다. 이 기능은 함수 정의의 헤드에 `= {}`를 통해 사용할 수 있다.

```javascript
function move1({x=0, y=0} = {}) {
    return [x, y];
}
move1({x: 3, y: 8}); // [3, 8]  
move1({x: 3}); // [3, 0]  
move1({}); // [0, 0]  
move1(); // [0, 0]  
```

`But why would you define the parameters as in the previous code snippet? Why not as follows – which is also completely legal ES6 code?`

하지만 왜 이전 코드에서와 같이 매개변수를 정의할 것인가? 왜 다음과 같은 온전한 `ES6` 코드로 정의하지 않는가?

```javascript
function move2({x, y} = { x: 0, y: 0 }) {
    return [x, y];
}
```

`move1()`가 적절한 이유를 확인하기 위해 두 예제에서 함수를 둘 다 사용해보자. 그 전에, 파라미터 전달이 매칭을 통해 어떻게 설명되는지를 먼저 보자.

`To see why move1() is correct, let’s use both functions for two examples. Before we do that, let’s see how the passing of parameters can be explained via matching.`

## 10.11.2.1 `Background: passing parameters via matching` 배경 : 매칭을 통한 파라미터 전달

`For function calls, formal parameters (inside function definitions) are matched against actual parameters (inside function calls). As an example, take the following function definition and the following function call.`

함수 호출의 경우, 정식 파라미터( 함수 정의 안에 정의된 )는 실제 파라미터( 함수 호출 내부의 )와 일치된다. 예를 들어, 다음 함수 선언과 함수 호출을 보자.

```javascript
function func(a=0, b=0) { ··· }
func(1, 2);
```

`The parameters a and b are set up similarly to the following destructuring.`

파라미터 `a`와 `b`는 해체와 유사하게 설정된다.

`[a=0, b=0] ← [1, 2]`

## 10.11.2.2 `Using move2()` move2() 사용하기

`Let’s examine how destructuring works for move2().`

`move2`의 해체 작동 방법을 살펴보자.

`Example 1. move2() leads to this destructuring:`

예제 1. `move2()`가 해체로 연결.

`[{x, y} = { x: 0, y: 0 }] ← []`

[{x, y} = { x: 0, y: 0 }] ← []

`The only Array element on the left-hand side does not have a match on the right-hand side, which is why {x,y} is matched against the default value and not against data from the right-hand side (rules 3b, 3d):`

좌변의 배열 요소는 우변과 일치하지 않기 때문에 `{x, y}`는 기본값과 일치하게 되고 우변의 데이터와 일치되지 않는다. ( 3b, 3d )

`{x, y} ← { x: 0, y: 0 }`

{x, y} ← { x: 0, y: 0 }

`The left-hand side contains property value shorthands, it is an abbreviation for:`

왼쪽은의 축약이며, 속성 값 shorthands이 포함되어 있습니다

`{x: x, y: y} ← { x: 0, y: 0 }`

`This destructuring leads to the following two assignments (rule 2c, 1):`

이 destructuring가 (규칙 (2C), 1) 다음의 두 가지 과제로 이어진다

```javascript
x = 0;
y = 0;
```

`However, this is the only case in which the default value is used.`

그러나, 이러한 디폴트 값이 사용되는 유일한 경우이다.

`Example 2. Let’s examine the function call move2({z:3}) which leads to the following destructuring:`

예 2.의가 살펴 보자 함수 호출 영화 2 ({Z : 3}) 다음 destructuring에 이르게 :

`[{x, y} = { x: 0, y: 0 }] ← [{z:3}]`

`There is an Array element at index 0 on the right-hand side. Therefore, the default value is ignored and the next step is (rule 3d):`

오른쪽에 인덱스 0에서 배열의 요소가있다. 따라서, 디폴트 값은 무시하고, 다음 단계 (룰 3D)이다 :

`{x, y} ← { z: 3 }`

`That leads to both x and y being set to undefined, which is not what we want.`

즉, x와 y 모두가 우리가 원하는 것을하지 않은, undefined로 설정되는 리드.

## `10.11.2.3 Using move1()` move1() 사용하기

`Let’s try move1().`

`move1`을 시도해보자.

`Example 1: move1()`

예제1: move1()

`[{x=0, y=0} = {}] ← []`

`We don’t have an Array element at index 0 on the right-hand side and use the default value (rule 3d):`

우리는 오른쪽에 인덱스 0 배열 요소를 가지고 기본값 (룰 3D)을 사용하지 않는다 :

`{x=0, y=0} ← {}`

`The left-hand side contains property value shorthands, which means that this destructuring is equivalent to:`

이 destructuring가 동등하다는 것을 의미 측이 속성 값 shorthands이 포함 된 좌측 :

`{x: x=0, y: y=0} ← {}`

`Neither property x nor property y have a match on the right-hand side. Therefore, the default values are used and the following destructurings are performed next (rule 2d):`

어느 속성 X 나 속성 y는 오른쪽에 일치있다. 따라서, 디폴트 값을 사용하여 다음 destructurings 다음 (룰 2D)에 수행된다 :

```javascript
x ← 0
y ← 0
```

`That leads to the following assignments (rule 1):`

즉, 다음과 같은 과제 (규칙 1)로 연결 :

```javascript
x = 0
y = 0
```

Example 2: move1({z:3})

`[{x=0, y=0} = {}] ← [{z:3}]`

`The first element of the Array pattern has a match on the right-hand side and that match is used to continue destructuring (rule 3d):`

어레이 패턴의 첫 번째 요소는 우측의 매치를 가지며, 그 일치 destructuring (룰 3D)을 계속하기 위해 사용된다 :

`{x=0, y=0} ← {z:3}`

`Like in example 1, there are no properties x and y on the right-hand side and the default values are used:`

실시 예 1과 마찬가지로,이 우측에는 특성 X와 Y는없고 디폴트 값이 사용된다 :

```javascript
x = 0
y = 0
```

## 10.11.3 `Conclusion` 결론

`The examples demonstrate that default values are a feature of pattern parts (object properties or Array elements). If a part has no match or is matched against undefined then the default value is used. That is, the pattern is matched against the default value, instead.`

예는 기본 값이 패턴 부품 (개체 속성 또는 배열 요소)의 기능이 있음을 보여줍니다. 일부 일치가 없습니다 또는 정의에 일치하는 경우 기본값이 사용됩니다. 즉, 패턴 대신에, 디폴트 값에 대해 일치한다.