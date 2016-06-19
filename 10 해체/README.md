# `Destructuring` 해체 `Destructuring`

## 10.1 `Overview` 개요

`Destructuring is a convenient way of extracting values from data stored in (possibly nested) objects and Arrays. It can be used in locations that receive data (such as the left-hand side of an assignment). How to extract the values is specified via patterns (read on for examples).`

해체는 ( 아마도 포함된 )객체와 배열에 저장된 데이터에서 값을 추출하는 편리한 방법이다. 할당문의 좌변같은 데이터를 받는 위치에서 사용될 수 있다. 값을 추출하는 방법은 패턴에 의해 규정되었다. 예제를 계속 보자.

## 10.1.1 `Object destructuring` 객체 해체

`Destructuring objects:` 객체 해체하기.

```javascript
const obj = { first: 'Jane', last: 'Doe' };
const {first: f, last: l} = obj;
// f = 'Jane'; l = 'Doe'

// {prop} 은 {prop: prop} 의 축약형이다.
const {first, last} = obj;
// first = 'Jane'; last = 'Doe'
```    

`Destructuring helps with processing return values:`

해체는 반환값의 처리를 도와준다.

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

해체는 반환값의 처리를 도와준다.

```javascript
const [all, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec('2999-12-31');
```

## 10.1.3 `Where can destructuring be used?` 해체는 어디서 사용될 수 있는가?

`Destructuring can be used in the following locations:`

해체는 아래 위치에서 사용될 수 있다.

```javascript
// 변수 선언:
const [x] = ['a'];
let [x] = ['a'];
var [x] = ['a'];

// 할당:
[x] = ['a'];

// 매개변수 선언:

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

## 10.2 `Background: Constructing data versus extracting data` 배경: 데이터 구축 대 데이터 추출

`To fully understand what destructuring is, let’s first examine its broader context. JavaScript has operations for constructing data:`

해체가 무엇인지 완전히 이해하기 위해 먼저 큰 맥락을 살펴보자. 자바스크립트는 데이터를 구축하는 행동을 가지고 있다.

```javascript
const obj = {};
obj.first = 'Jane';
obj.last = 'Doe';
```

`And it has operations for extracting data:`

그리고 값을 추출하기 위한 행동을 가지고 있다.

```javascript
const f = obj.first;
const l = obj.last;
```

`Note that we are using the same syntax that we have used for constructing.`

구축을 위해 사용했던 것과 같은 문법을 사용한 것을 기억하라.

`There is nicer syntax for constructing – an object literal:`

객체 리터럴이라는 구축을 위한 더 나은 문법이 있다.

```javascript
const obj = { first: 'Jane', last: 'Doe' };
```

`Destructuring in ECMAScript 6 enables the same syntax for extracting data, where it is called an object pattern:`

ECMA의 해체는 객체 패턴을 사용한 곳에서 데이터를 추출하기 위해 같은 문법을 가능하게 한다. 

```javascript
const { first: f, last: l } = obj;
```

`Just as the object literal lets us create multiple properties at the same time, the object pattern lets us extract multiple properties at the same time.`

객체 리터럴이 한번에 여러개의 프로퍼티를 생성해 주는 것처럼, 객체 패턴은 한번에 여러 개의 프로퍼티를 추출해준다.

`You can also destructure Arrays via patterns:`

패턴을 통해 배열을 해체할 수도 있다.

```javascript
const [x, y] = ['a', 'b']; // x = 'a'; y = 'b'
```

##10.3 `Patterns` 패턴

`The following two parties are involved in destructuring:`

다음 두 부분은 해체에 관련이 있다.

- `Destructuring source: the data to be destructured. For example, the right-hand side of a destructuring assignment.`  
  헤체 소스: 데이터는 파괴될 것이다. 예를 들어, 해체 할당의 우변이다.
- `Destructuring target: the pattern used for destructuring. For example, the left-hand side of a destructuring assignment.`  
  해체 타겟: 파괴를 위해 패턴이 사용된다. 예를 들어, 해체 할당의 좌변이다.

`The destructuring target is either one of three patterns:`

해체 타겟은 세가지 패턴 중 하나이다.
- `Assignment target. For example: x`  
  할당 대상. 예를 들어 `x`  
  - `In variable declarations and parameter definitions, only references to variables are allowed. In destructuring assignment, you have more options, as I’ll explain later.`  
    변수 선언과 파라미터 정의에서 변수의 참조만 허용된다. 해체 할당에서는 옵션이 더 있다. 뒤에 설명한다.
- `Object pattern. For example: { first: «pattern», last: «pattern» }`  
  객체 패턴. 예를 들어 `{ first: <<pattern>>, last: <<pattern>> }`
  - `The parts of an object pattern are properties, the property values are again patterns (recursively).`  
    객체 패턴의 부분은 프로퍼티이다. 프로퍼티 값은 다시 패턴이다. ( 재귀 )
- `Array pattern. For example: [ «pattern», «pattern» ]`  
  배열 패턴. 예를 들어 `[ <<pattern>>>, <<pattern>> ]`
  - `The parts of an Array pattern are elements, the elements are again patterns (recursively).`
    배열 패턴의 부분은 원소이다.  원소는 다시 패턴이다. ( 재귀 )

`That means that you can nest patterns, arbitrarily deeply:`

패턴을 얼마든지 깊게 포함할 수 있다.

```javascript
const obj = { a: [{ foo: 123, bar: 'abc' }, {}], b: true };
const { a: [{foo: f}] } = obj; // f = 123
```

## 10.3.1 `Pick what you need` 필요한 것을 가려내기

`If you destructure an object, you mention only those properties that you are interested in:`

객체를 해체한다면 관심있는 프로퍼티만 기술한다.

```javascript
const { x: x } = { x: 7, y: 3 }; // x = 7
```

`If you destructure an Array, you can choose to only extract a prefix:`

배열을 해체한다면 추출할 프리픽스만 고를 수 있다.

```javascript
const [x,y] = ['a', 'b', 'c']; // x='a'; y='b';
```

## 10.4 `How do patterns access the innards of values?` 패턴은 어떻게 값의 내부에 접근하는가?

`In an assignment ( pattern = someValue ), how does the pattern access what’s inside someValue?`

할당패턴 ( `pattern = someValue` ) 에서 패턴은 어떻게 `someValue` 내부에 접근하는가?

## 10.4.1 `Object patterns coerce values to objects` 객체 패턴은 값을 객체로 강제한다

`The object pattern coerces destructuring sources to objects before accessing properties. That means that it works with primitive values:`

객체 패턴은 프로퍼티에 접근하기 전에 소스를 객체로 해체하는 것을 강제한다. 원시값과 작동한다.

```javascript
const {length : len} = 'abc'; // len = 3
const {toString: s} = 123; // s = Number.prototype.toString
```

## 10.4.1.1 `Failing to object-destructure a value` 값을 객체 해체하는 것의 실패

`The coercion to object is not performed via Object(), but via the internal operation ToObject(). Object() never fails:`

객체의 강제는 `Object()`가 아닌 내부 동작인 `ToObject()`를 통해 수행된다. `Object()`는 절대 실패하지 않는다.

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

이 정의는 null가 발생하면 ToObject ()는 형식 오류가 발생합니다. 따라서, 다음과 같은 구조 조정은 destructuring 어떤 속성에 접근하기도 전에 실패 :

```javascript
const { prop: x } = undefined; // TypeError
const { prop: y } = null; // TypeError
```

`As a consequence, you can use the empty object pattern {} to check whether a value is coercible to an object. As we have seen, only undefined and null aren’t:`

결과적으로, 당신은 값이 객체에 강제 할 수 있는지 여부를 점검하려면 빈 객체 패턴을 {} 사용할 수 있습니다. 우리가 보았 듯이, 오직 정의와 null가되지 않습니다 :

```javascript
({} = [true, false]); // `OK, Arrays are coercible to objects` OK, 배열은 객체에 강제 할 수 있습니다
({} = 'abc'); // `OK, strings are coercible to objects` OK, 문자열은 객체에 강제 할 수 있습니다

({} = undefined); // TypeError
({} = null); // TypeError
```

`The parentheses around the expressions are necessary because statements must not begin with curly braces in JavaScript (details are explained later).`

문은 자바 스크립트에서 중괄호 (자세한 내용은 나중에 설명)로 시작하지해야하기 때문에 표현의 주위에 괄호가 필요합니다.

## 10.4.2 `Array patterns work with iterables` 배열 패턴을 반복 가능 객체 작동

`Array destructuring uses an iterator to get to the elements of a source. Therefore, you can Array-destructure any value that is iterable. Let’s look at examples of iterable values.`

배열 destructuring는 소스의 요소에 도착하는 반복자를 사용합니다. 따라서 반복 가능한 임의의 값을 배열-destructure 수 있습니다. 의는 반복 가능한 값의 예를 살펴 보자.

`Strings are iterable:`

문자열은 이터러블이다.

```javascript
const [x,...y] = 'abc'; // x='a'; y=['b', 'c']
```

`Don’t forget that the iterator over strings returns code points (“Unicode characters”, 21 bits), not code units (“JavaScript characters”, 16 bits). (For more information on Unicode, consult the chapter “Chapter 24. Unicode and JavaScript” in “Speaking JavaScript”.) For example:`

문자열을 통해 반복자가 코드 포인트 ( "유니 코드 문자", 21 비트)가 아닌 코드 단위 ( "자바 스크립트 문자", 16 비트)를 반환하는 것을 잊지 마십시오. 예를 들어 (. 유니 코드에 대한 자세한 내용은 "자바 스크립트를 말하기"의 장 "제 24 장 유니 코드와 자바 스크립트"를 참조)

```javascript
const [x,y,z] = 'a\uD83D\uDCA9c'; // x='a'; y='\uD83D\uDCA9'; z='c'
```

`You can’t access the elements of a Set via indices, but you can do so via an iterator. Therefore, Array destructuring works for Sets:`

당신은 인덱스를 통해 설정의 요소에 액세스 할 수 있지만 반복자를 통해 수행 할 수 있습니다. 따라서, 배열 destructuring는 설정 작동 :

```javascript
const [x,y] = new Set(['a', 'b']); // x='a'; y='b’;
```

`The Set iterator always returns elements in the order in which they were inserted, which is why the result of the previous destructuring is always the same.`

설정 반복자는 항상 이전 destructuring의 결과가 항상 동일 왜 그들이 삽입 된 순서에 요소를 반환합니다.

`Infinite sequences. Destructuring also works for iterators over infinite sequences. The generator function allNaturalNumbers() returns an iterator that yields 0, 1, 2, etc.`

무한 시퀀스. Destructuring는 무한 시퀀스를 통해 반복자 작동합니다. 발전기 기능 allNaturalNumbers ()는 0, 1, 2 등을 산출 반복자를 반환

```javascript
function* allNaturalNumbers() {
  for (let n = 0; ; n++) {
    yield n;
  }
}
```

`The following destructuring extracts the first three elements of that infinite sequence.`

다음 destructuring는 무한 시퀀스의 처음 세 요소를 추출한다.

```javascript
const [x, y, z] = allNaturalNumbers(); // x=0; y=1; z=2
```

## 10.4.2.1 `Failing to Array-destructure a value` 배열-destructure 값으로 실패

`A value is iterable if it has a method whose key is Symbol.iterator that returns an object. Array-destructuring throws a TypeError if the value to be destructured isn’t iterable:`

그것은 누구의 키 객체를 반환 Symbol.iterator 수있는 방법이있는 경우 값은 반복 가능한 것입니다. 탈구되는 값이 반복 가능한 아닌 경우 배열 destructuring는 형식 오류가 발생합니다 :

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

형식 오류 심지어 당신이 값이 반복 가능 여부를 확인하기 위해 빈 배열 패턴 []을 사용할 수 있다는 것을 의미 반복자의 요소에 액세스하기 전에 발생합니다 :

```javascript
[] = {}; // `TypeError, empty objects are not iterable` TypeError, 빈 객체는 이터러블이 아니다
[] = undefined; // `TypeError, not iterable` TypeError, 이터러블이 아니다
[] = null; // `TypeError, not iterable` TypeError, 이터러블이 아니다
```

## 10.5 `If a part has no match` 일부 일치가없는 경우

`Similarly to how JavaScript handles non-existent properties and Array elements, destructuring fails silently if the target mentions a part that doesn’t exist in the source: the interior of the part is matched against undefined. If the interior is a variable that means that the variable is set to undefined:`

대상이 소스에 존재하지 않는 부분을 언급하는 경우 마찬가지로 자바 스크립트가 존재하지 않는 특성 및 배열 요소를 처리하는 방법에, destructuring는 자동으로 실패 : 부품의 내부는 정의와 일치합니다. 내부 변수가 정의로 설정되는 것을 의미 가변 인 경우 :

```javascript
const [x] = []; // x = undefined
const {prop:y} = {}; // y = undefined
```

`Remember that object patterns and Array patterns throw a TypeError if they are matched against undefined.`

객체 패턴을 기억하고 그들이 정의에 대해 일치하는 경우 배열 패턴은 형식 오류를 throw합니다.

## 10.5.1 `Default values` 기본값

`Default values are a feature of patterns: If a part (an object property or an Array element) has no match in the source, it is matched against:`

기본값은 패턴의 특징이다 : 부분 (객체 속성 또는 배열 요소)은 소스에서 일치가 없다면, 그것은 대해 일치한다 :

- `its default value (if specified)`  
  기본값 ( 지정된 경우 )
- `undefined (otherwise)`  
  undefined ( 그 외 )

`That is, providing a default value is optional.`

즉 디폴트 값은 선택 제공한다.

`Let’s look at an example. In the following destructuring, the element at index 0 has no match on the right-hand side. Therefore, destructuring continues by matching x against 3, which leads to x being set to 3.`

예제를 살펴 보자. 다음 destructuring에서 인덱스 0의 요소는 오른쪽에 일치가 없습니다. 따라서, destructuring는 3으로 설정되고 x를 리드 3에 대한 X를 일치시켜 계속됩니다.

```javascript
const [x=3, y] = []; // x = 3; y = undefined
```

`You can also use default values in object patterns:`

또한 객체 패턴에 기본 값을 사용할 수 있습니다 :

```javascript
const {foo: x=3, bar: y} = {}; // x = 3; y = undefined
```

## 10.5.1.1 `undefined triggers default values` 정의되지 않은 트리거 기본값

`Default values are also used if a part does have a match and that match is undefined:`

일부가 일치하는 항목을 가지고 그 경기가 정의되지 않은 경우 기본 값도 사용된다 :

```javascript
const [x=1] = [undefined]; // x = 1
const {prop: y=2} = {prop: undefined}; // y = 2
```

`The rationale for this behavior is explained in the next chapter, in the section on parameter default values.`

이 문제에 대한 이론적 근거는 매개 변수의 기본값 섹션에서 다음 장에 설명되어 있습니다.

## 10.5.1.2 `Default values are computed on demand` 기본값은 필요에 따라 계산된다

`The default values themselves are only computed when they are needed. In other words, this destructuring:`

기본값은 필요할 때 자신 만 계산 값. 즉,이 destructuring :

```javascript
const {prop: y=someFunc()} = someValue;
```

`is equivalent to:`

동일합니다 :

```javascript
let y;
if (someValue.prop === undefined) {
    y = someFunc();
} else {
    y = someValue.prop;
}
```

`You can observe that if you use console.log():`

`console.log()`를 사용하여 관찰할 수 있다.

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

두 번째 destructuring에서 기본 값이 트리거되지 않고 로그는 ()가 호출되지 않습니다.

## 10.5.1.3 `Default values can refer to other variables in the pattern` 기본 값은 패턴에서 다른 변수를 참조 할 수 있습니다

`A default value can refer to any variable, including another variable in the same pattern:`

디폴트 값은 동일한 패턴의 다른 변수를 포함하여 모든 변수를 참조 할 수 있습니다

```javascript
const [x=3, y=x] = [];     // x=3; y=3
const [x=3, y=x] = [7];    // x=7; y=7
const [x=3, y=x] = [7, 2]; // x=7; y=2
```

`However, order matters: the variables x and y are declared from left to right and produce a ReferenceError if they are accessed before their declaration:`

변수 X와 Y가 왼쪽에서 오른쪽으로 선언되고 그들의 선언하기 전에 액세스되는 경우 ReferenceError가 생산 : 그러나, 물질을 순서 :

```javascript
const [x=y, y=3] = []; // ReferenceError
```

## 10.5.1.4 `Default values for patterns` 패턴의 기본값

`So far we have only seen default values for variables, but you can also associate them with patterns:`

지금까지 우리는 변수에 대한 기본 값을 볼 수있다, 그러나 당신은 또한 패턴을 연결할 수 있습니다 :

```javascript
const [{ prop: x } = {}] = [];
```

`What does this mean? Recall the rule for default values:`

이것은 무엇을 의미 하는가? 디폴트 값에 대한 규칙을 불러 :

> `If the part has no match in the source, destructuring continues with the default value […].`  
> 일부 소스의 일치가없는 경우, destructuring 기본값 [...] 계속합니다.

`The element at index 0 has no match, which is why destructuring continues with:`

인덱스 0 요소는 destructuring이 계속 왜 일치를,이 없습니다 :

```javascript
const { prop: x } = {}; // x = undefined
```

`You can more easily see why things work this way if you replace the pattern { prop: x } with the variable pattern:`

당신이 패턴을 교체하면 일이 이런 식으로 일을 왜 더 쉽게 볼 수 있습니다 {프로 : X} 변수 패턴 :
 
```javascript
const [pattern = {}] = [];
```

`More complex default values. Let’s further explore default values for patterns. In the following example, we assign a value to x via the default value { prop: 123 }:`

더 복잡한 기본값. 의 추가 패턴에 대한 기본 값을 알아 보자. 다음 예에서, 우리는 기본값을 통해 X에 {: (123) 소품} 값을 할당 :

```javascript
const [{ prop: x } = { prop: 123 }] = [];
```

`Because the Array element at index 0 has no match on the right-hand side, destructuring continues as follows and x is set to 123.`

인덱스 0의 배열 요소는 오른쪽에 일치하는이 없기 때문에 다음이고, x는 123로 설정되어, destructuring가 계속됩니다.

```javascript
const { prop: x } = { prop: 123 };  // x = 123
```

`However, x is not assigned a value in this manner if the right-hand side has an element at index 0, because then the default value isn’t triggered.`

다음 기본값이 발생되지 않기 때문에 오른편은 인덱스 0의 요소를 갖는 경우, X는 이와 같이 값이 할당되지 않는다.

```javascript
const [{ prop: x } = { prop: 123 }] = [{}];
```

`In this case, destructuring continues with:`

이 경우, destructuring은 계속 :

```javascript
const { prop: x } = {}; // x = undefined
```

`Thus, if you want x to be 123 if either the object or the property is missing, you need to specify a default value for x itself:`

당신이 원하는 경우 개체 또는 속성이없는 하나가, 당신은 그 자체 x의 디폴트 값을 지정해야하는 경우 따라서 x는 123이어야합니다 :

```javascript
const [{ prop: x=123 } = {}] = [{}];
```
여기 객체는 다음처럼 우변이 [{}] 나 []에 상관없이 독립적으로 지속된다.

```javascript
const { prop: x=123 } = {}; // x = 123
```

아직 혼란스러운가?
나중에 알고리즘으로써의 관점에서 해체를 살펴보겠다. 이것은 또 다른 인사이트를 줄 것이다.

##10.6 더 많은 객체 해체 약칭
##10.6.1 속성값 바로 가기

속성값의 약칭은 객체 리터럴의 특징이다.: 만약 속성값이 이름이 키와 같은 변수를 통해 제공된다면 키를 제외해도 해체는 동작한다.:

```javascript
const { x, y } = { x: 11, y: 8 }; // x = 11; y = 8
```

이 선언은 다음과 비교할만하다.
```javascript

const { x: x, y: y } = { x: 11, y: 8 };
```
또한 기본값과 함께 속성값 약칭을 섞을 수 있다.:
```javascript
const { x, y = 1 } = {}; // x = undefined; y = 1
```

##10.6.2 계산된 속성키
계산된 속성키는 다른 해체를 위해 동작하는 객체 리터럴 특징이다.: 대괄호를 이용한다면 표현식을 통해 속성키를 명시할 수 있다. :
```javascript
const FOO = 'foo';
const { [FOO]: f } = { foo: 123 }; // f = 123
```
계산된 속성키는 심볼키인 프로퍼티의 해체를 허용한다.:
```javascript
// 심볼키를 가지는 속성의 생성과 해체
const KEY = Symbol();
const obj = { [KEY]: 'abc' };
const { [KEY]: x } = obj; // x = 'abc'

// Array.prototype[Symbol.iterator] 추출
const { [Symbol.iterator]: func } = [];
console.log(typeof func); // function
```

##10.7 더 많은 배열 해체의 특징
##10.7.1 생략
해체 구문 사이의 배열의 빈 구멍은 생략할 수 있다.:
```javascript
const [,, x, y] = ['a', 'b', 'c', 'd']; // x = 'c'; y = 'd'
```

##10.7.2 나머지 연산자(rest operator) (...)
나머지 연산자는 배열의 각 요소의 추출한 배열가능케 한다. 배열 패턴의 마지막 파라미터로 나머지 연산자를 사용할 수 있다.:
```javascript
const [x, ...y] = ['a', 'b', 'c']; // x='a'; y=['b', 'c']
```
나머지 연산자는 데이터를 추출한다. 펼침 연산자(...) 의 문법도 똑같다. 펼침 연산자는 데이터를 배열 리터럴과 함수 호출에 공헌한다. 이건 다음 챕터에서 설명한다.

만약 연산자가 요소를 찾지 못한다면, 빈배열로 매칭된다. 때문에 undefined나 null은 절대 할당되지 않는다.

```javascript
const [x, y, ...z] = ['a']; // x='a'; y=undefined; z=[]
```
나머지 연산자의 피연산자가 변수일 필요는 없다. 이 역시 패턴을 사용하면 된다. :
```javascript
const [x, ...[y, z]] = ['a', 'b', 'c'];
    // x = 'a'; y = 'b'; z = 'c'
```
나머지 연산자는 다음의 해체를 발생시킨다:
```javascript
[y, z] = ['b', 'c']
```
펼침 연산자는  (...) 나머지 연산자와 완전히 똑같이 생겼다. 그러나 나머지 연산자는 함수 호출과 배열 리터럴 안에서만 사용된다.(해체 패턴 안이 아니라)

10.8 변수가 아닌 곳에도 할당가능하다.
해체를 통한 할당이라면, 각 할당 타겟은 객체의 프로퍼티(obj.prop) 참조나 배열의 요소(arr[0]) 참조를 포함하여 일반적인 할당문의 좌변에서 허용되는 모든 것이 될 수 있다. 
```javascript
const obj = {};
const arr = [];

({ foo: obj.prop, bar: arr[0] }) = { foo: 123, bar: true };

console.log(obj); // {prop:123}
console.log(arr); // [true]
```

또한 나머지 연산자(...)를 이용해서 객체 프로퍼티와 배열 요소를 할당 할 수도 있다.

```javascript
const obj = {};
[first, ...obj.prop] = ['a', 'b', 'c'];
    // first = 'a'; obj.prop = ['b', 'c']
```
만약 해체를 이용해서 변수를 선언하거나 파라미터를 정의 한다면, simple identifiers를 사용해야한다. 객체 프로퍼티나 배열의 요소를 참조하면 안된다.


##10.9 해체의 Pitfalls
해체를 사용할 때 2가지 유념해야할 사항이 있다.

중괄호로 선언문을 시작하면 안된다.
You can’t start a statement with a curly brace.

해체문에는 변수 선언이나 변수 할당이 가능하다. 둘 다 동시에 하는 것은 불가능하다.
다음 두 섹션에 자세한 내용이 있다.

##10.9.1 중괄호로 선언문을 시작하지 말아라
코드 블럭이 중괄호로 시작하기 때문에, 선언문은 그렇게 하면 안된다. 이것은 할당문에서 객체 해체를 사용할 때 불편하다 :
```javascript
{ a, b } = someObject; // SyntaxError
```
감싸는 괄호안에 완전한 표현식을 써넣어야 한다:

({ a, b } = someObject); // ok

##10.9.2 이미 존재하는 변수에 선언과 할당을 조합하면 안된다.
변수선언을 해체하는 곳에서, 소스의 모든 변수는 선언된다. 다음 예제를 보자. 변수 b를 선언하고 존재하는 변수 f를 참조하지만 동작하지 않는다.
```javascript
let f;
···
let { foo: f, bar: b } = someObject;
    // During parsing (before running the code):
    // SyntaxError: Duplicate declaration, f
```
b를 선언하기 위해 해체 할당을 사용하고 있다.:

```javascript
let f;
···
let b;
({ foo: f, bar: b }) = someObject;
```

##10.10 해체의 예제
몇가지 간단한 예제로 시작해보자.

for-of 반복문은 해체를 지원한다:
```javascript
const map = new Map().set(false, 'no').set(true, 'yes');
for (const [key, value] of map) {
  console.log(key + ' is ' + value);
}
```
값을 바꾸기 위해 해체를 사용 할 수 있다. 엔진이 최적화를 해줄수 있으므로 배열이 생성되지 않는다.

```javascript
[a, b] = [b, a];
```
배열을 스플릿 하기 위해 해체를 사용 할 수 있다
:
```javascript
const [first, ...rest] = ['a', 'b', 'c'];
    // first = 'a'; rest = ['b', 'c']
```
##10.10.1 반환된 배열 해체하기

자바스크립트에 내장된 몇가지 기능은 배열을 반환한다. 해체는 이러한 처리를 돕는다:
```javascript
const [all, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec('2999-12-31');
```
그룹(완벽히 일치하지 않는)을 찾고 싶다면, 0번 인덱스에서 배열요소를 건너뛰는것으로 생략 가능하다:
```javascript
const [, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec('2999-12-31');
```
exec() 는 표현식이 매칭되지 않으면 null을 반환한다. 불행히도 null을 기본값으로 다루지는 못한다. 이것은 ||연산자를 사용해야만 하는 이유가 되는 예제이다:

```javascript
const [, year, month, day] = /^(\d\d\d\d)-(\d\d)-(\d\d)$/.exec(someStr) || [];
```

Array.prototype.split()은 배열을 반환한다. 그러므로 해체는 배열이 아닌 각 요소 안에서 유용하다:
```javascript
const cells = 'Jane\tDoe\tCTO'
const [firstName, lastName, title] = cells.split('\t');
console.log(firstName, lastName, title);
```

##10.10.2 반환된 객체 해체하기
해체는 함수나 메소드에 의해 반환된 객체의 데이터를 추출하는데도 유용하다. 예를 들면 이터레이터 메소드인 next()는 done과 value 두가지 속성을 갖는 객체를 반환한다. 다음 코드는 이터레이터를 이용해 배열의 모든 요소 로그를 찍는다. 해체는 line A에서 사용된다.

```javascript
const arr = ['a', 'b'];
const iter = arr[Symbol.iterator]();
while (true) {
    const {done,value} = iter.next(); // (A)
    if (done) break;
    console.log(value);
}
```

##10.10.3 이터러블 값의 배열해체
이터러블 값의 배열해체도 동작 한다. 이것은 때때로 유용하다:

```javascript
const [x,y] = new Set().add('a').add('b');
    // x = 'a'; y = 'b'

const [a,b] = 'foo';
    // a = 'f'; b = 'o'
```

##10.10.4 값을 여러번 반환하기

값을 여러번 반환하는 유용함을 알아보기 위해, findElement(a, p)를 구현해보자. 이 함수는 true를 리턴하는 함수 p의 배열에서 첫 번째 요소를 찾는다. 문제는 그 함수가 무엇을 반환해야 하는가 이다.
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

line A에서, 배열 메소드 entries() 는 [index, element] 쌍으로 가진 이터러블을 반환한다. 한번의 이터레이션 마다 한 쌍을 해체한다. line B에서 객체  { element: element, index: index } 를 반환하기 위해 속성값 약칭을 사용한다.

findElement()를 사용해봅시다. 다음 예제에서, 몇몇 ECMAScript 6 특징들은 더 간략한 코딩을 가능하게 해준다.: 콜백은 arrow function 으로, 반환값은 속성값의 약칭인 객체 패턴으로 해체된다
```javascript
const arr = [7, 8, 6];
const {element, index} = findElement(arr, x => x % 2 === 0);
    // element = 8, index = 1
```
속성 키를 참조하는 인덱스와 요소 때문에 우리가 언급한 그 순서는 상관이 없다. 
```javascript
const {index, element} = findElement(···);
```
인덱스와 요소 모두 요구되는 케이스를 성공적으로 다뤘다. 그 것들중에 하나에만 관심이 있었다면 어떨까? 이것은 ECMAScript6에 이르러 해결되었다. 우리의 구현은 이제 그게 가능하다. 그리고 문법 과부하는 하나의 반환값 으로 비교된다.

```javascript
const a = [7, 8, 6];

const {element} = findElement(a, x => x % 2 === 0);
    // element = 8

const {index} = findElement(a, x => x % 2 === 0);
    // index = 1
```

매번 우리가 필요한 속성값을 추출한다 

##10.11 해체 알고리즘
이번 섹션은 다른관점에서 해체를 바라볼 것이다.: 재귀적 패턴 매칭 알고리즘

이 다른 관점은 특히 기본값을 이해하는데 도움을 준다. 만약 완벽히 이해되지 않는다면 계속 읽어봐라

마지막으로 다음 2개의 함수선언문의 차이점을 설명하기 위한 알고리즘을 사용 할 것이다.
```javascript
function move({x=0, y=0} = {})         { ··· }
function move({x, y} = { x: 0, y: 0 }) { ··· }
```
##10.11.1 알고리즘
해체 할당은 다음과 같이 생겼다.

«pattern» = «value»
값으로 부터 데이터 추출을 위한 패턴을 사용을 원한다. 이제부터 그 방법에 대한 알고리즘을 설명 할 것이다. 이것은 패턴 매칭에 의한 함수형 프로그래밍으로 알려져 있다.(이하 매칭). 이 알고리즘은 값의 패턴매칭이 되는 해체 할당을 위해 ← 를 연산자로 규정하고 이러한 과정이 진행되며 변수에 할당한다.

«pattern» ← «value»

이 알고리즘은 좌측을 가르키는 연산자를 받는 재귀적 법칙에 의해 명시된다. 이 선언적인 표기법은 종종 사용되지만 알고리즘의 스펙을 더 간결하게 만든다. 각각의 법칙은 두 부분으로 나뉜다.

head는 룰에 의해 어떤 피연산자부가 다뤄질지 명시한다.
body는 다음에 무엇을 할지 명시한다.

오직 해체 할당을 위한 알고리즘만 보여준다. 변수 선언을 해체하는 것과와 파라미터선언을 해체하는 것은 비슷하게 작동한다.

고급 특징들을 다루지는 않는다. (계산된 속성 키, 속성값, 할당자로써의 객체 속성 또는 배열요소). 기본만 다룬다.


##10.11.1.1 패턴
하나의 패턴은 다음과 같다:

변수: x
객체 패턴: {«properties»}
배열 패턴: [«elements»]
각각의 섹션은 다음중 하나의 상황을 설명한다.

##10.11.1.2 변수
(1) x ← value (including undefined and null)
  x = value
  
##10.11.1.3 객체패턴
(2a) {«properties»} ← undefined  
  throw new TypeError();  
(2b) {«properties»} ← null  
  throw new TypeError();  
(2c) {key: «pattern», «properties»} ← obj  
  «pattern» ← obj.key  
  {«properties»} ← obj  
(2d) {key: «pattern» = default_value, «properties»} ← obj  
  const tmp = obj.key;  
    ```javascript
  if (tmp !== undefined) {
      «pattern» ← tmp
  } else {
      «pattern» ← default_value
  }
  ```
  
  {«properties»} ← obj  
(2e) {} ← obj
  // 좌변의 빈 프로퍼티, 아무것도 안함
  
##10.11.1.4 배열 패턴
배열 패턴과 이터러블. 배열해체를 위한 알고리즘은 배열 패턴과 이터러블로 시작한다.

(3a) [«elements»] ← non_iterable  
assert(!isIterable(non_iterable))  
  throw new TypeError();  
(3b) [«elements»] ← iterable  
assert(isIterable(iterable))  
  const iterator = iterable[Symbol.iterator]();  
  «elements» ← iterator  
헬퍼 함수:  

```javascript
function isIterable(value) {
    return (value !== null
        && typeof value === 'object'
        && typeof value[Symbol.iterator] === 'function');
}
```
배열 요소와 이터레이터. 이 알고리즘은 패턴(화살표의 좌변부)의 요소와 이터러블에서 얻어진 이터레이터(화살표의 우변부)로 함께 지속된다.

(3c) «pattern», «elements» ← iterator  
  «pattern» ← getNext(iterator) // undefined after last item  
  «elements» ← iterator  
(3d) «pattern» = default_value, «elements» ← iterator  
```javascript
  const tmp = getNext(iterator);  // undefined after last item  
  if (tmp !== undefined) {
      «pattern» ← tmp
  } else {
      «pattern» ← default_value
  }
  ```
  «elements» ← iterator  
  
(3e) , «elements» ← iterator (hole, elision)  
  getNext(iterator); // skip  
  «elements» ← iterator  
(3f) ...«pattern» ← iterator (always last part!)  
```javascript
  const tmp = [];
  for (const elem of iterator) {
      tmp.push(elem);
  }
  ```
  «pattern» ← tmp  
  
(3g) ← iterator  
  // No elements left, nothing to do  
헬퍼 함수:  

```javascript
function getNext(iterator) {
    const {done,value} = iterator.next();
    return (done ? undefined : value);
}
```

##10.11.2 알고리즘 적용
다음의 함수선언문은 *이름있는 파라미터(named parameters)*를 가지고 있다. 이 기술은 파라미터 핸들링 챕터에서 때때로 options object 라고 불리거나 설명된다. 이 파라미터는 해체를 사용하고 x와 y가 생략될 수 있는 방법의 기본값을 사용한다. 그러나 아래 코드의 마지막 라인에서 보듯이 객체와 함께있는 파라미터도 생략 가능하다. 이 특징은 함수 선언문의 머리에서  {} 를 통해 사용된다.

```javascript
function move1({x=0, y=0} = {}) {
    return [x, y];
}
move1({x: 3, y: 8}); // [3, 8]  
move1({x: 3}); // [3, 0]  
move1({}); // [0, 0]  
move1(); // [0, 0]  
```
그러나 이전에 왜 코드 스니핏에서 파라미터를 선언해야 하는가? 다음은 완벽히 적합한 ES6 code코드 인가?

```javascript
function move2({x, y} = { x: 0, y: 0 }) {
    return [x, y];
}
```
move1()이 왜 올바른지 알기위해 두 가지 예제를 위한 함수를 사용해보자. 그 전에 우선, 어떻게 파라미터 전달이 매칭에 의해 설명 될수 있는지를 먼저 보자. 

##10.11.2.1 배경: 매칭을 이용한 파라미터 전달
함수 호출을 위해, 일반적인 파라미터(함수 선언 내)들은 실제 파라미터로 매칭된다.(함수 호출 내) 예제와 같이 다음 함수선언과 호출을 하라.
```javascript
function func(a=0, b=0) { ··· }
func(1, 2);
```
파라미터 a와 b는 다음의 해체와 유사한 과정으로 세팅된다.  

[a=0, b=0] ← [1, 2]  

##10.11.2.2 사용하기  move2()  
 move2() 해체가 어떻게 이뤄지는지 보자.  


예제 1. move2()의 해체 과정:  

[{x, y} = { x: 0, y: 0 }] ← []  
오직 좌변부의 배열 요소는 우변과 매치되지 않는다 이것은 {x,y} 가 기본값에 매칭되는 이유이고 우변부의 데이터에 반대된다. (rules 3b, 3d):

{x, y} ← { x: 0, y: 0 }  
좌변은 속성값 약칭을 소유한다 이것은 다음과 같다.

{x: x, y: y} ← { x: 0, y: 0 }  
이 해체는 아래 내용처럼 두 개의 할당과 같은 결과로 이끈다.(rule 2c, 1):  

x = 0;  
y = 0;  

##10.11.3 결론 
예제에서는 기본값이 패턴부(객체의 프로퍼티 또는 배열 요소)의 특징임을 증명합니다. 일치하는 부분이 없거나 undefined 라면 기본값이 사용 됩니다. 말인즉슨, 패턴은 기본값과 일치됩니다.

