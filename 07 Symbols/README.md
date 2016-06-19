# 7. Symbols

> 7. 심볼

## 7.1 Overview

> 개요

Symbols are a new primitive type in ECMAScript 6.

> 심볼은 ECMAScript 6의 새로운 원시 타입이다. 

### 7.1.1 Use case 1: unique property keys
> 7.1.1 이용 사례 1: 고유 프로퍼티 키

Symbols are mainly used as unique property keys – a symbol never clashes with any other property key (symbol or string). For example, you can make an object iterable (usable via the for-of loop and other language mechanisms), by using the symbol stored in Symbol.iterator as the key of a method (more information on iterables is given in the chapter on iteration):

> 심볼은 고유 프로퍼티 키로 주로 사용된다 - 심볼은 다른 어떤 프로퍼티키와 절대 충돌하지 않는다(심볼 또는 문자열). 객체 이터러블(for-of 루프와 다른 언어 메카니즘을 통해 이용 가능한)을 만드는 것을 예로 들 수 있는데, 객체 이터러블은 메소드의 키인 Symbol.iterator 안에 저장된 심볼을 사용해서 만들어진다. (이터러블에 관한 더 많은 정보는 이터레이션 장에 기재되어 있다) : 

```js
const iterableObject = {
    [Symbol.iterator]() { // (A)
        const data = ['hello', 'world'];
        let index = 0;
        return {
            next() {
                if (index < data.length) {
                    return { value: data[index++] };
                } else {
                    return { done: true };
                }
            }
        };
    }
}
for (const x of iterableObject) {
    console.log(x);
}
// Output:
// hello
// world
```

In line A, a symbol is used as the key of the method. This unique marker makes the object iterable and enables us to use the for-of loop.
> A 라인에서, 심볼은 메소드의 키로써 사용된다. 이 고유한 마커는 객체를 이터러블하게 만들고 for-of 루프를 사용 가능하게 만든다.

### 7.1.2 Use case 2: constants representing concepts

> 7.1.2 이용 사례 2: 컨셉을 나타내는 상수

In ECMAScript 5, you may have used strings to represent concepts such as colors. In ES6, you can use symbols and be sure that they are always unique:

> ECMAScript 5에서는 색깔같은 개념을 나타내기 위해서 문자열을 사용해왔을 것이다. ES6 에서는 심볼을 사용할 수있고 이 심볼은 항상 고유 하다는 것을 확신 할 수 있다.

```js
const COLOR_RED    = Symbol('Red');
const COLOR_ORANGE = Symbol('Orange');
const COLOR_YELLOW = Symbol('Yellow');
const COLOR_GREEN  = Symbol('Green');
const COLOR_BLUE   = Symbol('Blue');
const COLOR_VIOLET = Symbol('Violet');

function getComplement(color) {
    switch (color) {
        case COLOR_RED:
            return COLOR_GREEN;
        case COLOR_ORANGE:
            return COLOR_BLUE;
        case COLOR_YELLOW:
            return COLOR_VIOLET;
        case COLOR_GREEN:
            return COLOR_RED;
        case COLOR_BLUE:
            return COLOR_ORANGE;
        case COLOR_VIOLET:
            return COLOR_YELLOW;
        default:
            throw new Exception('Unknown color: '+color);
    }
}
```

### 7.1.3 Pitfall: you can’t coerce symbols to strings
> 7.1.3 함정: 심볼을 문자열로 강제 변환 할 수 없다.

Coercing (implicitly converting) symbols to strings throws exceptions:
> 심볼을 문자열로 강제 변환하는 것(암묵적 변환)은 예외를 발생시킨다.

```js
const sym = Symbol('desc');

const str1 = '' + sym; // TypeError
const str2 = `${sym}`; // TypeError
```

The only solution is to convert explicitly:
> 유일한 해결책은 명시적으로 변환하는 것이다.

```js
const str2 = String(sym); // 'Symbol(desc)'
const str3 = sym.toString(); // 'Symbol(desc)'
```

Forbidding coercion prevents some errors, but also makes working with symbols more complicated.
> 강제 변환을 방어하는 것은 몇몇 오류를 예방하지만, 심볼을 사용한 작업을 더욱 복잡하게 만들기도 한다.

### 7.1.4 Which operations related to property keys are aware of symbols?
> 7.1.4 프로퍼티키에 관련된 어떤 연산자가 심볼을 인지하는가?

The following operations are aware of symbols as property keys:
> 다음 연산자들은 프로퍼티 키로써의 심볼을 인지한다.

```js
Reflect.ownKeys()
Property access via []
Object.assign()
```

The following operations ignore symbols as property keys:
> 다음 연산자들은 프로퍼티키로써의 심볼을 무시한다.

```js
Object.keys()
Object.getOwnPropertyNames()
for-in loop
```

## 7.2 A new primitive type
> 7.2 새로운 원시 타입

ECMAScript 6 introduces a new primitive type: symbols. They are tokens that serve as unique IDs. You create symbols via the factory function Symbol() (which is loosely similar to String returning strings if called as a function):
> ECMAScript 6는 새로운 원시 타입을 소개한다 : 심볼. 심볼은 고유한 아이디로써 존재하는 토큰이다. 팩토리 함수인 Symbol()로 심볼을 생성한다(함수로 호출되면 문자열을 반환하는 String과 느슨하게 유사하다)

```js
const symbol1 = Symbol();
```

Symbol() has an optional string-valued parameter that lets you give the newly created Symbol a description. That description is used when the symbol is converted to a string (via toString() or String()):
> Symbol()은 문자열로 평가되는 파라미터를 갖는다. 이 파라미터는 선택적이며 새롭게 생성된 심볼에게 설명을 부여한다. 이 설명은 심볼이 문자열로 변환 될 때 사용된다(toString() 또는 String()을 통해)

```js
const symbol2 = Symbol('symbol2');
String(symbol2)
'Symbol(symbol2)'
```

Every symbol returned by Symbol() is unique, every symbol has its own identity:
> Symbol()로 반환된 모든 심볼은 고유하며, 모든 심볼은 각자의 정체성을 갖는다.

```js
Symbol() === Symbol()
false
```

You can see that symbols are primitive if you apply the typeof operator to one of them – it will return a new symbol-specific result:
> 심볼에 typeof 연산자를 사용해보면 심볼은 원시타입인것을 알 수 있다. - 심볼임을 특정하는 새로운 결과를 반환 할 것이다.

```js
typeof Symbol()
'symbol'
```

### 7.2.1 Symbols as property keys
> 7.2.1 프로퍼티키로써의 심볼

Symbols can be used as property keys:
> 심볼은 프로퍼티키로 사용될 수 있다.

```js
const MY_KEY = Symbol();
const obj = {};

obj[MY_KEY] = 123;
console.log(obj[MY_KEY]); // 123
```

Classes and object literals have a feature called computed property keys: You can specify the key of a property via an expression, by putting it in square brackets. In the following object literal, we use a computed property key to make the value of MY_KEY the key of a property.
> 클래스와 객체 리터럴은 계산된 프로퍼티 키 *computed property keys*로 불리는 기능을 갖는다 : 표현식을 통해 프로퍼티의 키를 대괄호 안에 넣음으로써 이를 특정할 수 있다. 다음 객체리터럴에서, MY_KEY 의 값을 프로퍼티의 키로 만들기 위해 계산된 프로퍼티 키를 사용한다. 

```js
const MY_KEY = Symbol();
const obj = {
    [MY_KEY]: 123
};
```

A method definition can also have a computed key:
> 메서드 선언도 계산된 키를 가질 수 있다.

```js
const FOO = Symbol();
const obj = {
    [FOO]() {
        return 'bar';
    }
};

console.log(obj[FOO]()); // bar
```

### 7.2.2 Enumerating own property keys
> 7.2.2 프로퍼티키 열거하기 

Given that there is now a new kind of value that can become the key of a property, the following terminology is used for ECMAScript 6:

+ Property keys are either strings or symbols.
+ String-valued property keys are called property names.
+ Symbol-valued property keys are called property symbols.

> 프로퍼티키는 문자열 또는 심볼이다.
> 문자열로 평가되는 프로퍼티키는 프로퍼티 이름이라 칭한다.
> 심볼로 평가되는 프로퍼티키는 프로퍼티 심볼이라 칭한다.

Let’s examine the API for enumerating own property keys by first creating an object.
> 프로퍼티키를 나열하기 위해 객체를 먼저 만들어야하는 API를 알아보자

```js
const obj = {
    [Symbol('my_key')]: 1,
    enum: 2,
    nonEnum: 3
};
Object.defineProperty(obj, 'nonEnum', { enumerable: false });
```
   
Object.getOwnPropertyNames() ignores symbol-valued property keys:
> Object.getOwnPropertyNames()는 심볼로 평가되는 프로퍼티 키를 무시한다.

```js
Object.getOwnPropertyNames(obj)
['enum', 'nonEnum']
```

Object.getOwnPropertySymbols() ignores string-valued property keys:
> Object.getOwnPropertySymbols()는 문자열로 평가되는 프로퍼티 키를 무시한다.

```js
Object.getOwnPropertySymbols(obj)
[Symbol(my_key)]
```

Reflect.ownKeys() considers all kinds of keys:
> Reflect.ownKeys() 는 모든 종류의 키를 고려한다.

```js
Reflect.ownKeys(obj)
[Symbol(my_key), 'enum', 'nonEnum']
```

Object.keys() only considers enumerable property keys that are strings:
> Object.keys()는 오로지 문자열로 열거 가능한 프로퍼티키만 고려한다 :

```js
Object.keys(obj)
['enum']
```

The name Object.keys clashes with the new terminology (only string keys are listed). Object.names or Object.getEnumerableOwnPropertyNames would be a better choice now.
> Object.keys는 새로운 용어(문자열 키만 나열된다)와 충돌한다. Object.names 또는 Object.getEnumerableOwnPropertyNames 가 이제 더 나은 선택이 될 것이다.

## 7.3 Using symbols to represent concepts
> 7.3 개념을 나타내기 위해 심볼을 사용하기

In ECMAScript 5, one often represents concepts (think enum constants) via strings. For example:
> ECMAScript 5 에서는, 문자열로 컨셉(열거 상수를 생각해보라)을 종종 표현한다. 예를 들면:

```js
var COLOR_RED    = 'Red';
var COLOR_ORANGE = 'Orange';
var COLOR_YELLOW = 'Yellow';
var COLOR_GREEN  = 'Green';
var COLOR_BLUE   = 'Blue';
var COLOR_VIOLET = 'Violet';
```

However, strings are not as unique as we’d like them to be. To see why, let’s look at the following function.
> 하지만 문자열은 우리가 원하는 것만큼 고유하지 않다. 왜 그런지 알아보기 위해, 다음 함수를 보자.  

```js
function getComplement(color) {
    switch (color) {
        case COLOR_RED:
            return COLOR_GREEN;
        case COLOR_ORANGE:
            return COLOR_BLUE;
        case COLOR_YELLOW:
            return COLOR_VIOLET;
        case COLOR_GREEN:
            return COLOR_RED;
        case COLOR_BLUE:
            return COLOR_ORANGE;
        case COLOR_VIOLET:
            return COLOR_YELLOW;
        default:
            throw new Exception('Unknown color: '+color);
    }
}
```
It is noteworthy that you can use arbitrary expressions as switch cases, you are not limited in any way. For example:
스위치문으로 임의의 표현식을 사용할 수 있고 아무런 제한을 받지 않는다는것은 주목할만하다. 예를 들어:

```js
function isThree(x) {
    switch (x) {
        case 1 + 1 + 1:
            return true;
        default:
            return false;
    }
}
```

We use the flexibility that switch offers us and refer to the colors via our constants (COLOR_RED etc.) instead of hard-coding them ('RED' etc.).
> 우리은 스위치가 제공한다 유연성 (COLOR_RED 등) 하드코딩 대신에 ('RED' 등) 상수를 통해 색깔을 참조하거 뭐래~~~

Interestingly, even though we do so, there can still be mix-ups. For example, someone may define a constant for a mood:
>  흥미롭게도 

```js
var MOOD_BLUE = 'BLUE';
```

Now the value of BLUE is not unique anymore and MOOD_BLUE can be mistaken for it. If you use it as a parameter for getComplement(), it returns 'ORANGE' where it should throw an exception.
> 이제 BLUE의 값은 더 이상 고유하지 않고 MOOD_BLUE는 실수가 될 수 있다. getComplement()를 위한 파라미터로 이를 사용한다면 예외를 던지는 곳에서 'ORANGE'를 반환한다. 

Let’s use symbols to fix this example. Now we can also use the ES6 feature const, which lets us declare actual constants (you can’t change what value is bound to a constant, but the value itself may be mutable).
> 이 예제를 고치기 위해 심볼을 사용하자. 이제 실제 상수를 선언할 수 있게 하는 ES6 기능인 const를 사용할 수 있다. (상수에 할당된 값은 변경할 수 없지만, 이 값은 스스로 변할 수도 있다) 

```js
const COLOR_RED    = Symbol('Red');
const COLOR_ORANGE = Symbol('Orange');
const COLOR_YELLOW = Symbol('Yellow');
const COLOR_GREEN  = Symbol('Green');
const COLOR_BLUE   = Symbol('Blue');
const COLOR_VIOLET = Symbol('Violet');
```

Each value returned by Symbol is unique, which is why no other value can be mistaken for BLUE now. Intriguingly, the code of getComplement() doesn’t change at all if we use symbols instead of strings, which shows how similar they are.
> 심볼에 의해 반환된 각각의 값은 고유하고 ~~~~~~~. 아주 흥미롭게도 getComplement()의 코드는 전혀 바꾸지 않는다. 문자열 대신에 심볼을 사용한다면 ~~~~~

## 7.4 Symbols as keys of properties
> 7.4 프로퍼티의 키인 심볼

Being able to create properties whose keys never clash with other keys is useful in two situations:
> 

+ For non-public properties in inheritance hierarchies.
+ To keep meta-level properties from clashing with base-level properties.

> 상속 계층에서 공개되지 않은 프로퍼티를 위해
> meta-level 프로퍼티가 base-level 프로퍼티와 충돌하는 것을 막기 위해

### 7.4.1 Symbols as keys of non-public properties
> 7.4.1 공개되지 않은 프로퍼티의 키인 심볼

Whenever there are inheritance hierarchies in JavaScript (e.g. created via classes, mixins or a purely prototypal approach), you have two kinds of properties:
자바스크립트에서 상속 계층(클래스, 믹스인, 순수한 프로토타입 접근법)은 언제든지 두 종류의 프로퍼티를 갖는다.

+ Public properties are seen by clients of the code.
+ Private properties are used internally within the pieces (e.g. classes, mixins or objects) that make up the inheritance hierarchy. (Protected properties are shared between several pieces and face the same issues as private properties.)

> 공개 프로퍼티는 코드의 클라이언트에 의해 보인다.
> 비공개 프로퍼티는 내부적으로 상속 계층을 구성하는 조각(클래스, 믹스인 또는 객체) 내에서 사용된다. 

For usability’s sake, public properties usually have string keys. But for private properties with string keys, accidental name clashes can become a problem. Therefore, symbols are a good choice. For example, in the following code, symbols are used for the private properties _counter and _action.

> 사용 적합성을 위해 공개 프로퍼티는 일반적으로 문자열 키를 갖는다. 그러나 문자열 키인 비공개 프로퍼티를 위해 우연한 이름 충돌이 문제가 될 수 있다. 그래서 심볼이 좋은 선택이다. 예를 들어, 다음 코드에서 심볼은 _counter 와 _action 인 비공개 프로퍼티로 사용된다.

```js
const _counter = Symbol('counter');
const _action = Symbol('action');
class Countdown {
    constructor(counter, action) {
        this[_counter] = counter;
        this[_action] = action;
    }
    dec() {
        let counter = this[_counter];
        if (counter < 1) return;
        counter--;
        this[_counter] = counter;
        if (counter === 0) {
            this[_action]();
        }
    }
}
```

Note that symbols only protect you from name clashes, not from unauthorized access, because you can find out all own property keys – including symbols – of an object via Reflect.ownKeys(). If you want protection there, as well, you can use one of the approaches listed in Sect. “Private data for classes”.

심볼은 비허가된 접근이 아니라 오로지 이름 충돌만을 방어한다는 점을 알아두어야한다. Reflect.ownKeys()를 이용하면 심볼을 포함한 모든 프로퍼티 키를 찾을 수 있기 때문이다. 비허가된 접근의 방어 또한 원한다면, 5장 3절 "클래스의 비공개 데이터"에 나열된 접근법중에 하나를 사용 할 수 있다.

### 7.4.2 Symbols as keys of meta-level properties
> 7.4.2 메타레벨 프로퍼티의 키로써 심볼

Symbols having unique identities makes them ideal as keys of public properties that exist on a different level than “normal” property keys, because meta-level keys and normal keys must not clash. One example of meta-level properties are methods that objects can implement to customize how they are treated by a library. Using symbol keys protects the library from mistaking normal methods as customization methods.
> 고유한 정체성을 갖는 심볼은 "보통" 프로퍼티 키보다 다른 레벨의 공개 프로퍼티의 키로써 적절하게 만든다. 메타레벨 키와 보통 키는 충돌하면 안되기 때문이다. 메타레벨 프로퍼티의 한가지 예는 객체가 라이브러리로 취급되는 메서드이다. 후.....~~~~~~
심볼키를 사용하는 것은 라이브러리를 일반적인 메서드를 실수 보호한다 ~~~

Iterability in ECMAScript 6 is one such customization. An object is iterable if it has a method whose key is the symbol (stored in) Symbol.iterator. In the following code, obj is iterable.
> ECMAScript 6에서 이터러빌리티는 하나의 그런 사용자 정의이다. 객체는 키가 심볼(Symbol.iterator에 저장된)인 메서드를 갖는다면 이터러블하다. 다음 코드에서, obj는 이터러블하다.

```js
const obj = {
    data: [ 'hello', 'world' ],
    [Symbol.iterator]() {
        const self = this;
        let index = 0;
        return {
            next() {
                if (index < self.data.length) {
                    return {
                        value: self.data[index++]
                    };
                } else {
                    return { done: true };
                }
            }
        };
    }
};
```

The iterability of obj enables you to use the for-of loop and similar JavaScript features:
> obj의 이터러빌리티는 for-of 루프의 사용 그리고 유사한 자바스크립트의 기능 사용을 가능케한다.

```js
for (const x of obj) {
    console.log(x);
}

// 출력:
// hello
// world
```

### 7.4.3 Examples of name clashes in JavaScript’s standard library
> 7.4.3 자바스크립트 표준 라이브러리에서 이름 충돌의 예

In case you think that name clashes don’t matter, here are three examples of where name clashes caused problems in the evolution of the JavaScript standard library:
> 이름 충돌이 문제되지 않을 때를 대비하여, 

+ When the new method Array.prototype.values() was created, it broke existing code where with was used with an Array and shadowed a variable values in an outer scope (bug report 1, bug report 2). Therefore, a mechanism was introduced to hide properties from with (Symbol.unscopables).
+ String.prototype.contains clashed with a method added by MooTools and had to be renamed to String.prototype.includes (bug report).
+ The ES2016 method Array.prototype.contains also clashed with a method added by MooTools and had to be renamed to Array.prototype.includes (bug report).

In contrast, adding iterability to an object via the property key Symbol.iterator can’t cause problems, because that key doesn’t clash with anything.
> 대조적으로, 프로퍼티키인 Symbol.iterator로 객체에 이터러빌리티를 추가하는 것으로는 문제를 발생시킬 수 없다. 키가 다른 것과 충돌하지 않기 때문이다.

These examples demonstrate what it means to be a web language: backward compatibility is crucial, which is why compromises are occasionally necessary when evolving the language. As a side benefit, evolving old JavaScript code bases is simpler, too, because new ECMAScript versions never (well, hardly ever) break them.
> 이러한 예제들은 웹 언어라는게 어떤 의미인지 증명한다 : 하위 호환성은 중대하며 언어가 서서히 진화할 때 타협이 가끔 필요한 이유이다. 부수적인 이점으로, 오래된 자바스크립트 코드 기반을 발전시키는 것은 더욱 간단하다. 새로운 ECMAScript 버전은 절대(거의) 기존 코드를 망가뜨리지 않기 때문이다.

## 7.5 Converting symbols to other primitive types
> 7.5 심볼을 다른 원시 타입으로 변환하기

The following table shows what happens if you explicitly or implicitly convert symbols to other primitive types:
다음 테이블은 심볼이 다른 원시 타입으로 명시적 또는 암묵적 변환될 때 어떤 일이 발생하는지 보여준다.

| Conversion to | Explicit conversion |	Coercion (implicit conversion) |
|---------------|---------------------|--------------------------------|
| boolean | Boolean(sym) → OK | !sym → OK |

boolean	Boolean(sym) → OK	!sym → OK
number	Number(sym) → TypeError	sym*2 → TypeError
string	String(sym) → OK	''+sym → TypeError
 	sym.toString() → OK	`${sym}` → TypeError

### 7.5.1 Pitfall: coercion to string
Coercion to string being forbidden can easily trip you up:

```js
const sym = Symbol();

console.log('A symbol: '+sym); // TypeError
console.log(`A symbol: ${sym}`); // TypeError
```

To fix these problems, you need an explicit conversion to string:

```js
console.log('A symbol: '+String(sym)); // OK
console.log(`A symbol: ${String(sym)}`); // OK
```

### 7.5.2 Making sense of the coercion rules
Coercion (implicit conversion) is often forbidden for symbols. This section explains why.

#### 7.5.2.1 Truthiness checks are allowed
Coercion to boolean is always allowed, mainly to enable truthiness checks in if statements and other locations:

```js
if (value) { ··· }

param = param || 0;
```

#### 7.5.2.2 Accidentally turning symbols into property keys
Symbols are special property keys, which is why you want to avoid accidentally converting them to strings, which are a different kind of property keys. This could happen if you use the addition operator to compute the name of a property:

myObject['__' + value]
That’s why a TypeError is thrown if value is a symbol.

#### 7.5.2.3 Accidentally turning symbols into Array indices
You also don’t want to accidentally turn symbols into Array indices. The following is code where that could happen if value is a symbol:

myArray[1 + value]
That’s why the addition operator throws an error in this case.

### 7.5.3 Explicit and implicit conversion in the spec
#### 7.5.3.1 Converting to boolean
To explicitly convert a symbol to boolean, you call Boolean(), which returns true for symbols:

```js
const sym = Symbol('hello');
Boolean(sym)
true
```

Boolean() computes its result via the internal operation ToBoolean(), which returns true for symbols and other truthy values.

Coercion also uses ToBoolean():

```js
!sym
false
```

#### 7.5.3.2 Converting to number
To explicitly convert a symbol to number, you call Number():

```js
const sym = Symbol('hello');
Number(sym)
TypeError: can't convert symbol to number
```

Number() computes its result via the internal operation ToNumber(), which throws a TypeError for symbols.

Coercion also uses ToNumber():

```js
+sym
TypeError: can't convert symbol to number
```

#### 7.5.3.3 Converting to string
To explicitly convert a symbol to string, you call String():

```js
const sym = Symbol('hello');
String(sym)
'Symbol(hello)'
```

If the parameter of String() is a symbol then it handles the conversion to string itself and returns the string Symbol() wrapped around the description that was provided when creating the symbol. If no description was given, the empty string is used:

```js
String(Symbol())
'Symbol()'
```

The toString() method returns the same string as String(), but neither of these two operations calls the other one, they both call the same internal operation SymbolDescriptiveString().

```js
Symbol('hello').toString()
'Symbol(hello)'
```

Coercion is handled via the internal operation ToString(), which throws a TypeError for symbols. One method that coerces its parameter to string is Number.parseInt():

```js
Number.parseInt(Symbol())
TypeError: can't convert symbol to string
```

#### 7.5.3.4 Not allowed: converting via the binary addition operator (+)
The addition operator works as follows:

Convert both operands to primitives.
If one of the operands is a string, coerce both operands to strings (via ToString()), concatenate them and return the result.
Otherwise, coerce both operands to numbers, add them and return the result.
Coercion to either string or number throws an exception, which means that you can’t (directly) use the addition operator for symbols:

```js
'' + Symbol()
TypeError: can't convert symbol to string
1 + Symbol()
TypeError: can't convert symbol to number
```

## 7.6 JSON and symbols
### 7.6.1 Generating JSON via JSON.stringify()
JSON.stringify() converts JavaScript data to JSON strings. A preprocessing step lets you customize that conversion: a callback, a so-called replacer, can replace any value inside the JavaScript data with another one. That means that it can encode JSON-incompatible values (such as symbols and dates) as JSON-compatible values (such as strings). JSON.parse() lets you reverse this process via a similar mechanism1.

However, stringify ignores non-string property keys, so this approach works only if symbols are property values. For example, like this:

```js
function symbolReplacer(key, value) {
    if (typeof value === 'symbol') {
        return '@@' + Symbol.keyFor(value) + '@@';
    }
    return value;
}
const MY_SYMBOL = Symbol.for('http://example.com/my_symbol');
const obj = { myKey: MY_SYMBOL };

const str = JSON.stringify(obj, symbolReplacer);
console.log(str);
    // {"myKey":"@@http://example.com/my_symbol@@"}
```

A symbol is encoded as a string by putting '@@' before and after the symbol’s key. Note that only symbols that were created via Symbol.for() have such a key.

### 7.6.2 Parsing JSON via JSON.parse()

JSON.parse() converts JSON strings to JavaScript data. A postprocessing step lets you customize that conversion: a callback, a so-called reviver, can replace any value inside the initial output with another one. That means that it can decode non-JSON data (such as symbols and dates) stored in JSON data (such as strings)2. This looks as follows.

```js
const REGEX_SYMBOL_STRING = /^@@(.*)@@$/;
function symbolReviver(key, value) {
    if (typeof value === 'string') {
        const match = REGEX_SYMBOL_STRING.exec(value);
        if (match) {
            const symbolKey = match[1];
            return Symbol.for(symbolKey);
        }
    }
    return value;
}

const parsed = JSON.parse(str, symbolReviver);
console.log(parse);
```

Strings that start and end with '@@' are converted to symbols by extracting the symbol key in the middle.

## 7.7 Wrapper objects for symbols

While all other primitive values have literals, you need to create symbols by function-calling Symbol. Thus, it is relatively easy to accidentally invoke Symbol as a constructor. That produces instances of Symbol and is not very useful. Therefore, an exception is thrown when you try to do that:

```js
> new Symbol()
TypeError: Symbol is not a constructor
There is still a way to create wrapper objects, instances of Symbol: Object, called as a function, converts all values to objects, including symbols.

> const sym = Symbol();
> typeof sym
'symbol'

> const wrapper = Object(sym);
> typeof wrapper
'object'
> wrapper instanceof Symbol
true
```

### 7.7.1 Accessing properties via [ ] and wrapped keys
The square bracket operator [ ] for accessing properties unwraps string wrapper objects and symbol wrapper objects. Let’s use the following object to examine this phenomenon.

```js
const sym = Symbol('yes');
const obj = {
    [sym]: 'a',
    str: 'b',
};
```

Interaction:

```js
> const wrappedSymbol = Object(sym);
> typeof wrappedSymbol
'object'
> obj[wrappedSymbol]
'a'

> const wrappedString = new String('str');
> typeof wrappedString
'object'
> obj[wrappedString]
'b'
```

#### 7.7.1.1 Property access in the spec

The operator for getting and setting properties uses the internal operation ToPropertyKey(), which works as follows:

Convert the operand to a primitive via ToPrimitive() with the preferred type string:
Primitive values are returned as is.
Most objects are converted via the method toString() – if it returns a primitive value. Otherwise, valueOf() is used – if it returns a primitive value. Otherwise, a TypeError is thrown.
Symbol objects are one exception: they are converted to the symbols that they wrap.
If the result is a symbol, return it.
Otherwise, coerce the result to string via ToString().
7.8 Crossing realms with symbols
This is an advanced topic.

A code realm (short: realm) is a context in which pieces of code exist. It includes global variables, loaded modules and more. Even though code exists “inside” exactly one realm, it may have access to code in other realms. For example, each frame in a browser has its own realm. And execution can jump from one frame to another, as the following HTML demonstrates.

```html
<head>
    <script>
        function test(arr) {
            var iframe = frames[0];
            // This code and the iframe’s code exist in
            // different realms. Therefore, global variables
            // such as Array are different:
            console.log(Array === iframe.Array); // false
            console.log(arr instanceof Array); // false
            console.log(arr instanceof iframe.Array); // true

            // But: symbols are the same
            console.log(Symbol.iterator ===
                        iframe.Symbol.iterator); // true
        }
    </script>
</head>
<body>
    <iframe srcdoc="<script>window.parent.test([])</script>">
</iframe>
</body>
```

The problem is that each realm has its own local copy of Array and, because objects have individual identities, those local copies are considered different, even though they are essentially the same object. Similarly, libraries and user code are loaded once per realm and each realm has a different version of the same object.

In contrast, members of the primitive types boolean, number and string don’t have individual identities and multiple copies of the same value are not a problem: The copies are compared “by value” (by looking at the content, not at the identity) and are considered equal.

Symbols have individual identities and thus don’t travel across realms as smoothly as other primitive values. That is a problem for symbols such as Symbol.iterator that should work across realms: If an object is iterable in one realm, it should be iterable in others, too. If a cross-realm symbol is managed by the JavaScript engine, the engine can make sure that the same value is used in each realm. For libraries, however, we need extra support, which comes in the form of the global symbol registry: This registry is global to all realms and maps strings to symbols. For each symbol, libraries need to come up with a string that is as unique as possible. To create the symbol, they don’t use Symbol(), they ask the registry for the symbol that the string is mapped to. If the registry already has an entry for the string, the associated symbol is returned. Otherwise, entry and symbol are created first.

You ask the registry for a symbol via Symbol.for() and retrieve the string associated with a symbol (its key) via Symbol.keyFor():

```js
> const sym = Symbol.for('Hello everybody!');
> Symbol.keyFor(sym)
'Hello everybody!'
As expected, cross-realm symbols, such as Symbol.iterator, that are provided by the JavaScript engine are not in the registry:

> Symbol.keyFor(Symbol.iterator)
undefined
```

## 7.9 FAQ: symbols
### 7.9.1 Can I use symbols to define private properties?
The original plan was for symbols to support private properties (there would have been public and private symbols). But that feature was dropped, because using “get” and “set” (two meta-object protocol operations) for managing private data does not interact well with proxies:

On one hand, you want a proxy to be able to completely isolate its target (for membranes) and to intercept all MOP operations applied to its target.
On the other hand, proxies should not be able to extract private data from an object; private data should remain private.
These two goals are at odds.

The chapter on classes explains your options for managing their private data.

### 7.9.2 Are symbols primitives or objects?
In some ways, symbols are like primitive values, in other ways, they are like objects:

Symbols are like strings (primitive values) w.r.t. what they are used for: as representations of concepts and as property keys.
Symbols are like objects in that each symbol has its own identity.
What are symbols then – primitive values or objects? In the end, they were turned into primitives, for two reasons.

First, symbols are more like strings than like objects: They are a fundamental value of the language, they are immutable and they can be used as property keys. Symbols having unique identities doesn’t necessarily contradict them being like strings: UUID algorithms produce strings that are quasi-unique.

Second, symbols are most often used as property keys, so it makes sense to optimize the JavaScript specification and the implementations for that use case. Then many abilities of objects are unnecessary:

Objects can become prototypes of other objects.
Wrapping an object with a proxy doesn’t change what it can be used for.
Objects can be introspected: via instanceof, Object.keys(), etc.
Them not having these abilities makes life easier for the specification and the implementations. There are also reports from the V8 team that when handling property keys, it is simpler to treat primitives differently than objects.

### 7.9.3 Do we really need symbols? Aren’t strings enough?
In contrast to strings, symbols are unique and prevent name clashes. That is nice to have for tokens such as colors, but it is essential for supporting meta-level methods such as the one whose key is Symbol.iterator. Python uses the special name __iter__ to avoid clashes. You can reserve double underscore names for programming language mechanisms, but what is a library to do? With symbols, we have an extensibility mechanism that works for everyone. As you can see later, in the section on public symbols, JavaScript itself already makes ample use of this mechanism.

There is one hypothetical alternative to symbols when it comes to clash-free property keys: use a naming convention. For example, strings with URLs (e.g. 'http://example.com/iterator'). But that would introduce a second category of property keys (versus “normal” property names that are usually valid identifiers and don’t contain colons, slashes, dots, etc.), which is basically what symbols are, anyway. Thus it is more elegant to explicitly turn those keys into a different kind of value.

### 7.9.4 Are JavaScript’s symbols like Ruby’s symbols?
No, they are not.

Ruby’s symbols are basically literals for creating values. Mentioning the same symbol twice produces the same value twice:

:foo == :foo
The JavaScript function Symbol() is a factory for symbols – each value it returns is unique:

Symbol('foo') !== Symbol('foo')

## 7.10 The spelling of well-known symbols: why Symbol.iterator and not Symbol.ITERATOR (etc.)?
Well-known symbols are stored in properties whose names start with lowercase characters and are camel-cased. In a way, these properties are constants and it is customary for constants to have all-caps names (Math.PI etc.). But the reasoning for their spelling is different: Well-known symbols are used instead of normal property keys, which is why their “names” follow the rules for property keys, not the rules for constants.

## 7.11 The symbol API
This section gives an overview of the ECMAScript 6 API for symbols.

### 7.11.1 The function Symbol
Symbol(description?) : symbol
Creates a new symbol. The optional parameter description allows you to give the symbol a description. The only way to access the description is to convert the symbol to a string (via toString() or String()). The result of such a conversion is 'Symbol('+description+')'.
Symbol is can’t be used as a constructor – an exception is thrown if you invoke it via new.

### 7.11.2 Methods of symbols
The only useful method that symbols have is toString() (as provided via Symbol.prototype.toString()).

### 7.11.3 Converting symbols to other values
Conversion to	Explicit conversion	Coercion (implicit conversion)
boolean	Boolean(sym) → OK	!sym → OK
number	Number(sym) → TypeError	sym*2 → TypeError
string	String(sym) → OK	''+sym → TypeError
 	sym.toString() → OK	`${sym}` → TypeError
object	Object(sym) → OK	Object.keys(sym) → OK

### 7.11.4 Well-known symbols
The global object Symbol has several properties that serve as constants for so-called well-known symbols. These symbols let you configure how ES6 treats an object, by using them as property keys. This is a list of all well-known symbols:

Customizing basic language operations (explained in Chap. “New OOP features besides classes”):
Symbol.hasInstance (method)
Lets an object C customize the behavior of x instanceof C.
Symbol.toPrimitive (method)
Lets an object customize how it is converted to a primitive value. This is the first step whenever something is coerced to a primitive type (via operators etc.).
Symbol.toStringTag (string)
Called by Object.prototype.toString() to compute the default string description of an object obj: ‘[object ‘+obj[Symbol.toStringTag]+’]’.
Symbol.unscopables (Object)
Lets an object hide some properties from the with statement.
Iteration (explained in the chapter on iteration):
Symbol.iterator (method)
A method with this key makes an object iterable (its elements can be iterated over by language constructs such as the for-of loop and the spread operator (...)); it returns an iterator. Details: chapter “Iterables and iterators”.
Forwarding calls from string methods: The following string methods are forwarded to methods of their parameters (usually regular expressions).
Symbol.match is used by String.prototype.match.
Symbol.replace is used by String.prototype.replace.
Symbol.search is used by String.prototype.search.
Symbol.split is used by String.prototype.split.
The details are explained in Sect. “String methods that delegate regular expression work to their parameters” in the chapter on strings.

Miscellaneous:
Symbol.species (method)
Configures how built-in methods (such as Array.prototype.map()) create objects that are similar to this. The details are explained in the chapter on classes.
Symbol.isConcatSpreadable (boolean)
Configures whether Array.prototype.concat() adds the indexed elements of an object to its result (“spreading”) or the object as a single element (details are explained in the chapter on Arrays).

### 7.11.5 Global symbol registry
If you want a symbol to be the same in all realms, you need to create it via the global symbol registry. The following method lets you do that:

Symbol.for(str) : symbol
Returns the symbol whose key is the string str in the registry. If str isn’t in the registry yet, a new symbol is created and filed in the registry under the key str.
Another method lets you make the reverse look up and found out under which key a string is stored in the registry. This is may be useful for serializing symbols.

Symbol.keyFor(sym) : string
returns the string that is associated with the symbol sym in the registry. If sym isn’t in the registry, this method returns undefined.

[Speaking JS] Details are explained in the chapter on JSON.↩
[Speaking JS] The details are explained in the chapter on JSON.↩
