
--http://exploringjs.com/es6/ch_oop-besides-classes.html

# 14. New OOP features besides classes
# 14. Class 외의 새로운 OOP 특성

Classes (which are explained in the next chapter) are the major new OOP feature in ECMAScript 6. However, it also includes new features for object literals and new utility methods in Object. This chapter describes them.

ECMAScript 6에서 클래스는 새로운 OOP의 주요한 부분이다. 그 외에 객체 리터럴의 새로운 특성, 그리고 객체의 새로운 utility method 역시 포함하고 있다.

## 14.1 Overview
## 14.1 개요
### 14.1.1 New object literal features
### 14.1.1 새로운 객체 리터럴 특성

Method definitions:
메쏘드 정의 :
```js
const obj = {
    myMethod(x, y) {
        ···
    }
};
```
Property value shorthands:
프로퍼티값 속기:
```js
const first = 'Jane';
const last = 'Doe';

const obj = { first, last };
// Same as:
const obj = { first: first, last: last };
```
Computed property keys:
계산된 프로퍼티 키:
```js
const propKey = 'foo';
const obj = {
    [propKey]: true,
    ['b'+'ar']: 123
};
```
This new syntax can also be used for method definitions:
새로운 문법은 method의 정의도 가능하다.
```js
const obj = {
    ['h'+'ello']() {
        return 'hi';
    }
};
console.log(obj.hello()); // hi
```
The main use case for computed property keys is to make it easy to use symbols as property keys.
프로퍼티 키값 계산은 Symbol을 프로퍼티 키로 이용하기 쉽게 해준다.

### 14.1.2 New methods in Object
### 14.1.2 객체의 새로운 매서드

The most important new method of Object is assign(). Traditionally, this functionality was called extend() in the JavaScript world. In contrast to how this classic operation works, Object.assign() only considers own (non-inherited) properties.

assing()은 객체의 새로운 매서드 중 가장 중요하다. 기존의 자바스크립트 세계에서 이 함수는 extend() 로 불렸다. 기존에 어떻게 동작했었는지와는 별개로 Object.assign()은 고유의 속성(상속받지 않은)으로만 고려된다.

```js
const obj = { foo: 123 };
Object.assign(obj, { bar: true });
console.log(JSON.stringify(obj));
    // {"foo":123,"bar":true}
```
## 14.2 New features of object literals
## 14.2 객체 리터럴의 새로운 특징
### 14.2.1 Method definitions
### 14.2.1 메서드 정의

In ECMAScript 5, methods are properties whose values are functions:
ECMAScript 5에서 매서드는 그 값이 함수인 속성이다:
```js
var obj = {
    myMethod: function (x, y) {
        ···
    }
};
```
In ECMAScript 6, methods are still function-valued properties, but there is now a more compact way of defining them:
ECMAScript 6에서 매서드는 여전히 함수-값 속성이지만, 이를 정의하는 방법은 더 간단하다:
```js
const obj = {
    myMethod(x, y) {
        ···
    }
};
```
Getters and setters continue to work as they did in ECMAScript 5 (note how syntactically similar they are to method definitions):
게터와 세터는 ECMAScript 5와 마찬가지로 동작한다.(문법적으로 매서드 정의와 매우 유사함을 기억하라):
```js
const obj = {
    get foo() {
        console.log('GET foo');
        return 123;
    },
    set bar(value) {
        console.log('SET bar to '+value);
        // return value is ignored
    }
};
```
Let’s use obj:
obj를 이용해보자:
```js
> obj.foo
GET foo
123
> obj.bar = true
SET bar to true
true
```
There is also a way to concisely define properties whose values are generator functions:
그 값이 제너레이터 함수인 경우 보다 쉽게 속성 정의를 할 수 있다.
```js
const obj = {
    * myGeneratorMethod() {
        ···
    }
};
```
This code is equivalent to:
이 코드는 아래와 같다:
```js
const obj = {
    myGeneratorMethod: function* () {
        ···
    }
};
```
### 14.2.2 Property value shorthands
### 14.2.2 속성값 속기

Property value shorthands let you abbreviate the definition of a property in an object literal: If the name of the variable that specifies the property value is also the property key then you can omit the key. This looks as follows.

객체 리터럴에서 속성값의 속기로 간단하게 속성값을 정의할 수 있다.: 변수명으로 속성값을 지정한다면 속성키가 될 수 있으므로 키를 생략할 수 있다. 아래에서 볼 수 있다.

```js
const x = 4;
const y = 1;
const obj = { x, y };
```
The last line is equivalent to:
마지막 줄은 아래와 같다:
```js
const obj = { x: x, y: y };
```
Property value shorthands work well together with destructuring:
속성값의 속기는 해체에서도 잘 동작한다.
```js
const obj = { x: 4, y: 1 };
const {x,y} = obj;
console.log(x); // 4
console.log(y); // 1
```
One use case for property value shorthands are multiple return values (which are explained in the chapter on destructuring).
다중 리턴값에서도 속성값 속기는 이용된다.(해체 챕터에서 설명될 것이다.)
### 14.2.3 Computed property keys
### 14.2.3 속성 키값 계산

Remember that there are two ways of specifying a key when you set a property.
속성 키 값 지정에는 두 가지 방법이 있음을 기억하자.
```js
    Via a fixed name: obj.foo = true;
    속성명을 고정: obj.foo = true;
    Via an expression: obj['b'+'ar'] = 123;
    표현식을 이용 : obj['b'+'ar'] = 123;
```
In object literals, you only have option #1 in ECMAScript 5. ECMAScript 6 additionally provides option #2:
ECMAScript 5의 객체 리터럴에서는 #1의 방법밖에는 없었다. ECMAScript 6는 #2 방법도 추가적으로 제공된다:
```js
const propKey = 'foo';
const obj = {
    [propKey]: true,
    ['b'+'ar']: 123
};
```
This new syntax can also be used for method definitions:
새로운 문법은 매서드 정의에도 이용할 수 있다:
```js
const obj = {
    ['h'+'ello']() {
        return 'hi';
    }
};
console.log(obj.hello()); // hi
```

The main use case for computed property keys are symbols: you can define a public symbol and use it as a special property key that is always unique. One prominent example is the symbol stored in Symbol.iterator. If an object has a method with that key, it becomes iterable: The method must return an iterator, which is used by constructs such as the for-of loop to iterate over the object. The following code demonstrates how that works.

속성키값 계산에 Symbol이 가장 많이 이용된다 : 정의된 public 심볼을 특별한 속성키로 이용하면 언제나 유일하다. 특별한 예로 Symbol.iterator에 저장된 심볼을 들 수 있다.
객체가 이 키 값을 가진 메서드를 가지고 있다면 이터러블하다: 매서드는 이터레이터를 반환할 것이고, for-of 반복문과 같은 구조에서 객체의 반복처리에 이용될 수 있다.
아래의 코드는 어떻게 작동하는지를 보여준다.
```js
const obj = {
    * [Symbol.iterator]() { // (A)
        yield 'hello';
        yield 'world';
    }
};
for (const x of obj) {
    console.log(x);
}
// Output:
// hello
// world
```
Line A starts a generator method definition with a computed key (the symbol stored in Symbol.iterator).
A 라인은 계산된 키값으로 정의된 제너레이터 메서드로 시작한다.(Symbol.iterator에 저장된 symbol)

## 14.3 New methods of Object
## 14.3 객체의 새로운 매서드
### 14.3.1 Object.assign(target, source_1, source_2, ···)

This method merges the sources into the target: It modifies target, first copies all enumerable own (non-inherited) properties of source_1 into it, then all own properties of source_2, etc. At the end, it returns the target.
이 메서드는 타겟으로 소스를 병합한다: source_1의 모든 열거된 고유 속성(상속받지 않은), source_2의 모든 고유 속성, 등등등을 복사하여 타겟으로 넣고 타겟을 수정한다.
```js
const obj = { foo: 123 };
Object.assign(obj, { bar: true });
console.log(JSON.stringify(obj));
    // {"foo":123,"bar":true}
```
Let’s look more closely at how Object.assign() works:
Object.assign()이 어떻게 동작하는지 좀더 자세히 보자:

```js
    Both kinds of property keys: Object.assign() is aware of both strings and symbols as property keys.
    Only enumerable own properties: Object.assign() ignores inherited properties and properties that are not enumerable.
    Reading a value from a source: normal “get” operation (const value = source[propKey]). That means that if the source has a getter whose key is propKey then it will be invoked. All properties created by Object.assign() are data properties, it won’t transfer getters to the target.
    Writing a value to the target: normal “set” operation (target[propKey] = value). That means that if the target has a setter whose key is propKey then it will be invoked with value.

    두 종류의 객체 키 : Object.assign()은 string과 symbol 두 가지를 객체 키로 인식한다.
    열거가능한 고유 속성만 : Object.assign()은 상속받은 속성과 열거가 불가능한 속성은 무시한다.
    소스로부터 값을 읽기 : 일반적인 "get"(const value = source[propKey]) 연산. propKey인 키값을 가진 getter를 가진 소스라면 호출될 것이다.
    Object.asasgin()으로 생성된 모든 속성은 데이터 속성이고, 타겟으로 getter를 복사하지 않는다.
    타겟에 값 쓰기 : 일반적인 "set" 연산(target[propKey] = value). 이는 타겟이 propKey인 키값을 가진 setter를 가지고 있다면 값으로 호출될 것을 의미한다.
```

This is how you’d copy all properties (not just enumerable ones), while correctly transferring getters and setters, without invoking setters on the target:
아래는 올바르게 getter와 setter를 복사시 target의 setter를 호출하지 않고, 어떻게 모든 속성(열거 가능한 속성 뿐 아니라)을 복사할 수 있는지를 보여준다.
```js
function copyAllProperties(target, ...sources) {
    for (const source of sources) {
        for (const key of Reflect.ownKeys(source)) {
            const desc = Object.getOwnPropertyDescriptor(source, key);
            Object.defineProperty(target, key, desc);
        }
    }
    return target;
}
```
#### 14.3.1.1 Caveat: Object.assign() doesn’t work well for moving methods
#### 14.3.1.1 경고: Object.assign() 은 메서드 이동에는 제대로 동작하지 않는다.

On one hand, you can’t move a method that uses super: Such a method has an internal property [[HomeObject]] that ties it to the object it was created in. If you move it via Object.assign(), it will continue to refer to the super-properties of the original object. Details are explained in a section in the chapter on classes.

한편으로 super를 이용하여 메서드를 이동할 수 없다: 내부적인 속성을 가진 메서드[[HomeObject]]의 경우 그것을 생성시킨 객체에 묶여있다. 만약 Object.assign()으로 이동시키려 하면,
원래 지속적으로 객체의 상위-속성으로 참조할 것이다. 자세한 내용은 Class 챕터에 설명되어 있다.

On the other hand, enumerability is wrong if you move methods created by an object literal into the prototype of a class. The former methods are all enumerable (otherwise Object.assign() wouldn’t see them, anyway), but the prototype only has non-enumerable methods by default.
다른 한편으로, 객체 리터럴에 의해 생성된 매서드를 클래스의 프로토 타입으로 이동시키려고 하면 열거가능성이 잘못된다. 전자의 매서드는 모두 열거가능하지만, 프로토타입은 기본적으로 열거불가능한 메서드만 가질 수 있다.

#### 14.3.1.2 Use cases for Object.assign()
#### 14.3.1.2 Object.assign()의 쓰임새

Let’s look at a few use cases.
몇 가지 사용 예시를 보자.
##### 14.3.1.2.1 Adding properties to this
##### 14.3.1.2.1 this에 속성 추가

You can use Object.assign() to add properties to this in a constructor:
생성자의 this에 Object.assign()을 이용하여 속성을 추가할 수 있다.
```js
class Point {
    constructor(x, y) {
        Object.assign(this, {x, y});
    }
}
```

##### 14.3.1.2.2 Providing default values for object properties
##### 14.3.1.2.2 객체 속성에 기본값 제공

Object.assign() is also useful for filling in defaults for missing properties. In the following example, we have an object DEFAULTS with default values for properties and an object options with data.
Object.assign()은 누락된 속성의 기본값을 채우는데 유용하다. 아래의 예제에, 속성의 기본값을 가진 DEFAULTS라는 객체와 data를 가진 options 라는 객체가 있다.

```js
const DEFAULTS = {
    logLevel: 0,
    outputFormat: 'html'
};
function processContent(options) {
    options = Object.assign({}, DEFAULTS, options); // (A)
    ···
}
```
In line A, we created a fresh object, copied the defaults into it and then copied options into it, overriding the defaults. Object.assign() returns the result of these operations, which we assign to options.
라인 A에서 defaults 복사하여 넣은 후 options를 복사하여 넣은, 기본값을 재정의한 새로운 객체를 생성했다. Object.assign()은 이 options에 할당한 이런 작업들을 반환한다.

##### 14.3.1.2.3 Adding methods to objects
##### 14.3.1.2.3 객체에 매서드 추가하기

Another use case is adding methods to objects:
또다른 사용 예는 객체에 메서드를 추가하는 것이다 :
```js
Object.assign(SomeClass.prototype, {
    someMethod(arg1, arg2) {
        ···
    },
    anotherMethod() {
        ···
    }
});
```
You could also manually assign functions, but then you don’t have the nice method definition syntax and need to mention SomeClass.prototype each time:
수동으로 기능을 할당할 수는 있지만, 괜찮은 매서드 정의 구문도 없는데다 매번 SomeClass.prototype을 적어주어야 한다.
```js
SomeClass.prototype.someMethod = function (arg1, arg2) {
    ···
};
SomeClass.prototype.anotherMethod = function () {
    ···
};
```

##### 14.3.1.2.4 Cloning objects
##### 14.3.1.2.4 객체 복제

One last use case for Object.assign() is a quick way of cloning objects:
Object.assign()의 마지막 사용 예시로는 빠르게 객체복제 할 수 있다는 것이다:
```js
function clone(orig) {
    return Object.assign({}, orig);
}
```
This way of cloning is also somewhat dirty, because it doesn’t preserve the property attributes of orig. If that is what you need, you have to use property descriptors.
이 방식으로 복제하는 것은 오리지널의 속성 특성을 보존하지 않기때문에 좀 지저분하다. 이렇게 할 필요가 있다면 속성 디스크립터를 이용해야 한다.

If you want the clone to have the same prototype as the original, you can use Object.getPrototypeOf() and Object.create():
원래 것과 동일한 프로토타입을 가진 복제를 만들려면 Object.getPrototypeOf()과 Object.create()를 이용하면 된다:
```js
function clone(orig) {
    const origProto = Object.getPrototypeOf(orig);
    return Object.assign(Object.create(origProto), orig);
}
```

### 14.3.2 Object.getOwnPropertySymbols(obj)

Object.getOwnPropertySymbols(obj) retrieves all own (non-inherited) symbol-valued property keys of obj. It complements Object.getOwnPropertyNames(), which retrieves all string-valued own property keys. Consult a later section for more details on traversing properties.

Object.getOwnPropertySymbols(obj)는 객체의 모든 고유의 symbol값의 속성 키 값을 끌어낸다. 이는 객체의 모든 문자값의 속성키를 끌어내는 Object.getOwnPropertyNames()를 보완한다.
통과 속성에 대한 자세한 내용은 다음 섹션을 참고하면 된다.

### 14.3.3 Object.is(value1, value2)

The strict equals operator (===) treats two values differently than one might expect.
일치 연산자(===)는 기대와는 다르게 두 개의 값을 다룬다.

First, NaN is not equal to itself.
첫재로 Nan은 Nan과 동등하지 않다.
```js
> NaN === NaN
false
```
That is unfortunate, because it often prevents us from detecting NaN:
이는 안타깝게도 NaN 탐지하는 것을 종종 방해하기 때문이다.
```js
> [0,NaN,2].indexOf(NaN)
-1
```
Second, JavaScript has two zeros, but strict equals treats them as if they were the same value:
두번째로, JavaScript 는 두가지 제로가 있는데, 일치 연산자는 이 둘을 같은 값으로 다룬다:
```js
> -0 === +0
true
```
Doing this is normally a good thing.
이렇게 하는것이 일반적으로 좋은 것이다.

Object.is() provides a way of comparing values that is a bit more precise than ===. It works as follows:
Object.is() 는 === 보다 조금 더 정교한 값의 비교를 제공한다. 이는 아래와 같이 작동한다:

```js
> Object.is(NaN, NaN)
true
> Object.is(-0, +0)
false
```
Everything else is compared as with ===.
이 외의 대부분은 === 랑 똑같이 비교한다.

#### 14.3.3.1 Using Object.is() to find Array elements
#### 14.3.3.1 배열 요소 검색을 위한 Object.is()의 사용

If we combine Object.is() with the new ES6 Array method findIndex(), we can find NaN in Arrays:
Object.is()와 ES6의 새로운 배열 메소드인 findIndex()와 결합하여 이용하면, NaN을 배열에서 찾을 수 있다:

```js
function myIndexOf(arr, elem) {
    return arr.findIndex(x => Object.is(x, elem));
}

myIndexOf([0,NaN,2], NaN); // 1
```

In contrast, indexOf() does not handle NaN well:
이와는 대조적으로, indexOf()는 NaN을 제대로 처리하지 못한다:
```js
> [0,NaN,2].indexOf(NaN)
-1
```

### 14.3.4 Object.setPrototypeOf(obj, proto)

This method sets the prototype of obj to proto. The non-standard way of doing so in ECMAScript 5, that is supported by many engines, is via assigning to the special property __proto__. The recommended way of setting the prototype remains the same as in ECMAScript 5: during the creation of an object, via Object.create(). That will always be faster than first creating an object and then setting its prototype. Obviously, it doesn’t work if you want to change the prototype of an existing object.

이 메서드는 객체의 프로토타입을 proto로 지정한다. ECMAScript 5에서 이렇게 하기 위한 비표준적인 방법은, 많은 엔진에서 지원되는데, __proto__ 라는 특별한 속성에 할당함으로 써 가능하다.
프로토타입을 지정을 위한 가장 좋은 방법은 ECMAScript 5와 유사하게 남아있다 : 객체 생성시에 Object.create()를 이용하는 것이다.
이는 객체의 첫 생성이나 프로토 타입을 지정하는것 보다 언제나 더 빠를 것이다. 하지만 이미 존재하는 객체의 프로토 타입을 변경하길 원한다면, 그렇게 작동하지는  않을 것이다.

## 14.4 Traversing properties in ES6
## 14.4 ES6에서의 속성 고찰
### 14.4.1 Five operations that traverse properties
### 14.4.1 속성을 고찰시켜주는 5가지 연산

In ECMAScript 6, the key of a property can be either a string or a symbol. The following are five operations that traverse the property keys of an object obj:
ECMAScript 6에서, 속성의 키값은 문자열이나 혹은 심볼일 것이다. 아래의 다섯가지 연산은 객체 obj의 속성 키값을 이동시켜준다.
```js
    Object.keys(obj) : Array<string>
    retrieves all string keys of all enumerable own (non-inherited) properties.
    모든 열거 가능한(상속되지 않은) 속성의 문자열 키값을 반환.
    Object.getOwnPropertyNames(obj) : Array<string>
    retrieves all string keys of all own properties.
    모든 속성의 문자열 키값을 반환.
    Object.getOwnPropertySymbols(obj) : Array<symbol>
    retrieves all symbol keys of all own properties.
    모든 속성의 심볼 키 값을 반환.
    Reflect.ownKeys(obj) : Array<string|symbol>
    retrieves all keys of all own properties.
    모든 속성의 모든 키값을 반환.
    for (const key in obj)
    retrieves all string keys of all enumerable properties (inherited and own).
    열거 가능한 속성(상속 받은것 포함)의 모든 문자열 키값을 반환.
```
### 14.4.2 Traversal order of properties
### 14.4.2 속성의 traversal 순서

ES6 defines two traversal orders for properties.
ES6에는 두가지 속성값 탐색 순서가 있다.

Own Property Keys:
고유의 속성 키:

    Retrieves the keys of all own properties of an object, in the following order:
    객체의 모든 고유 속성의 키는 다음의 순서로 반환한다. :
        First, the string keys that are integer indices (what these are is explained in the next section), in ascending numeric order.
        Then all other string keys, in the order in which they were added to the object.
        Lastly, all symbol keys, in the order in which they were added to the object.
        먼저, 정수 인덱스(이에 대해서는 다음 섹션에서 설명한다.)의 문자열 키 - 숫자 오름차순으로 정렬.
        그리고 나서 그 외 모든 문자열 키 - 객체에 추가된 순서대로.
        마지막으로 모든 심볼 키 - 객체에 추가된 순서대로.

    Used by: Object.assign(), Object.defineProperties(), Object.getOwnPropertyNames(), Object.getOwnPropertySymbols(), Reflect.ownKeys()
    이용 :Object.assign(), Object.defineProperties(), Object.getOwnPropertyNames(), Object.getOwnPropertySymbols(), Reflect.ownKeys()

Enumerable Own Names:
열거 가능한 고유 명:

    Retrieves the string keys of all enumerable own properties of an object. The order is not defined by ES6, but it must be the same order in which for-in traverses properties.
    Used by: JSON.parse(), JSON.stringify(), Object.keys()
    객체의 모든 열거 가능한 고유 속성의 문자열 키를 반환. 이 순서는 ES6에서 정의된 순서는 아니지만 for-in에서 속성이 도는 동일한 순서일 것이다.
    이용: JSON.parse(), JSON.stringify(), Object.keys()

The order in which for-in traverses properties is not defined. Quoting Allen Wirfs-Brock:
for-in의 속성 통과하는 순서는 정의되지 않았다. Allen Wirfs-Brock를 인용하면 :

    Historically, the for-in order was not defined and there has been variation among browser implementations in the order they produce (and other specifics). ES5 added Object.keys and the requirement that it should order the keys identically to for-in. During development of both ES5 and ES6, the possibility of defining a specific for-in order was considered but not adopted because of web legacy compatibility concerns and uncertainty about the willingness of browsers to make changes in the ordering they currently produce.
    역사적으로, for-in 순서는 정의되지 않았고 구현된 브라우저 간에 편차가 있다. ES5에 Object.keys를 추가했고 키는 for-in에서 동일하게 정렬되어야 함을 요구한다.
    ES5와 ES6을 개발하는 동안, for-in의 명확한 정렬을 정의하기 위한 가능성이 고려되었으나 web 환경의 호환성 문제와 현재 생산된 브라우저가 정렬 방식을 변경하려는 의지에 대한 불확실성때문에 채택되지 않았다.


#### 14.4.2.1 Integer indices
#### 14.4.2.1 정수 색인

Many engines treat integer indices specially, even though they are still strings (at least as far as the ES6 spec is concerned). Therefore, it makes sense to treat them as a separate category of keys.
많은 엔진이 아직은 문자열이지만, 정수의 색인을 분명하게 다루고 있다.(최소한 ES6 스펙에서는 고려됨). 이는 이들을 키의 별도 항목으로 관리되어야 한다는 것이다.

Roughly, an integer index is a string that, if converted to a 53-bit non-negative integer and back is the same value. Therefore:
대체적으로, 정수 색인은 문자열이다, 53-bit의 음수가 아닌 정수로 전환했다가 다시 전환하면 이는 같은 값이다. 따라서 :

    '10' and '2' are integer indices.
    '02' is not an integer index. Converting it to an integer and back results in the different string '2'.
    '3.141' is not an integer index, because 3.141 is not an integer.
    '10'과 '2'는 정수 색인이다.
    '02' 는 정수 색인이 아니다. 이를 정수로 변환 했다가 돌아오면 그 결과는 '2'라는 다른 문자이다.
    '3.141'은 정수 색인이 아니다, 왜냐면 3.141이 정수가 아니기 때문이다.

In ES6, instances of String and Typed Arrays have integer indices. The indices of normal Arrays are a subset of integer indices: they have a smaller range of 32 bits. For more information on Array indices, consult “Array Indices in Detail” in “Speaking JavaScript”.
ES6에서 String과 Typed Arrays의 인스턴스는 정수 색인을 가지고 있다. 일반 배열의 색인은 정수 색인의 집합이다 : 32bit보다 작은 범위를 가진다. 배열 색인의 보다 많은 정보는 "자바스크립트를 말하다(Speaking JavaScript)"의
"Array Indices in Detail"에 설명되어 있다.

Integer indices have a 53-bit range, because thats the largest range of integers that JavaScript can handle. For details, see Sect. “Safe integers”.
정수 색인은 53 bit의 범위를 가진다, 때문에 JavaScript를 다루는 가장 큰 정수 범위가 된다. 자세한 내용은 "Safe integers" 섹션을 보면 된다.

#### 14.4.2.2 Example
#### 14.4.2.2 예

The following code demonstrates the traversal order “Own Property Keys”:
아래의 코드는 "고유 속성 키값"의 탐색 순서를 보여준다.:
```js
const obj = {
    [Symbol('first')]: true,
    '02': true,
    '10': true,
    '01': true,
    '2': true,
    [Symbol('second')]: true,
};
Reflect.ownKeys(obj);
    // [ '2', '10', '02', '01',
    //   Symbol('first'), Symbol('second') ]
```
Explanation:
설명 :

    '2' and '10' are integer indices, come first and are sorted numerically (not in the order in which they were added).
    '02' and '01' are normal string keys, come next and appear in the order in which they were added to obj.
    Symbol('first') and Symbol('second') are symbols and come last, in the order in which they were added to obj.
    '2'와 '10'은 정수 색인이고 제일 처음에 숫자로 정렬된다.(추가된 순서로 정렬되는게 아니라)
    '02'와 '01'은 일반 문자 키로, obj에 추가된 순서로 그 다음에 나타난다.
    Symbol('first')와 Symbol('second')는 심볼로서 가장 마지막에 그들이 추가된 순서에 맞춰 나타난다.

#### 14.4.2.3 Why does the spec standardize in which order property keys are returned?
#### 14.4.2.3 왜 속성 키값의 반환 스펙을 표준화하려고 하는가?

Answer by Tab Atkins Jr.:
Tab Atkins Jr.에 따르면:

    Because, for objects at least, all implementations used approximately the same order (matching the current spec), and lots of code was inadvertently written that depended on that ordering, and would break if you enumerated it in a different order. Since browsers have to implement this particular ordering to be web-compatible, it was specced as a requirement.
    적어도 객체에 대해서는, 모든 구현이 대략 비슷한 순서를 사용하였고, 이를 따라 많은 코드들이 잘못 짜여져 있어, 이를 다른 순서로 열거하게 되면 중단되어 버리기 때문이다.
    브라우저가 이 웹 호환되는 특정 순서를 구현해야 했으므로, 이 스펙이 필요한 것이다.
-------------------------
    There was some discussion about breaking from this in Maps/Sets, but doing so would require us to specify an order that is impossible for code to depend on; in other words, we’d have to mandate that the ordering be random, not just unspecified. This was deemed too much effort, and creation-order is reasonable valuable (see OrderedDict in Python, for example), so it was decided to have Maps and Sets match Objects.
    Map/Set에서의 이 중단에 대한 논의가 조금 있었으나, 특정 순서를 이를 임의의 순서로
-------------------------

#### 14.4.2.4 The order of properties in the spec
#### 14.4.2.4 스펙에서의 속성의 순서

The following parts of the spec are relevant for this section:
아래의 스펙 부분은 이 섹션과 관련이 있다:

    The section on Array exotic objects has a note on what Array indices are.
    The internal method [[OwnPropertyKeys]] is used by Reflect.ownKeys() and others.
    The operation EnumerableOwnNames is used by Object.keys() and others.
    The internal method [[Enumerate]] is used by for-in.
    배열 외부 객체의 섹션에서 배열 색인에 대한 노트가 있다.
    내부 매서드인 [[OwnPropertyKeys]]는 Reflect.ownKeys()와 다른데에서 이용된다.
    EnumerableOwnNames 연산은 Object.keys()와 다른 곳에서 이용된다.
    [[Enumerate]] 은 for-in에서 이용된다.

## 14.5 Assigning versus defining properties
## 14.5 속성의 할당과 정의

This section provides background knowledge that is needed in later sections.
이 섹션에서는 뒤 섹션에서 필요한 배경 지식을 제공한다.

There are two similar ways of adding a property prop to an object obj:
속성 prop을 객체 obj에 추가하는 두 가지 유사한 방법이 있다:
```js
    Assigning: obj.prop = 123
    할당: obj.prop = 123
    Defining: Object.defineProperty(obj, 'prop', 123)
    정의: Object.defineProperty(obj, 'prop', 123)
```
There are three cases in which assigning does not create an own property prop, even if it doesn’t exist, yet:
비록 존재하지 않더라도, 할당 시 고유 속성 prop을 생성하지 않는 세 가지 경우가 있다.

    A read-only property prop exists in the prototype chain. Then the assignment causes a TypeError in strict mode.
    A setter for prop exists in the prototype chain. Then that setter is called.
    A getter for prop without a setter exists in the prototype chain. Then a TypeError is thrown in strict mode. This case is similar to the first one.
    프로토타입 체인에 일기 전용 속성 prop이 존재한다. 그러면 strict 모드에서 할당은 TypeError가 발생시킨다.
    프로토 타입 체인에 prop에 setter가 존재한다. 그러면 setter가 호출된다.
    프로토 타입 체인에 setter를 제외한 getter가 존재한다. 그러면 strict 모드에서 TypeError를 던진다. 이는 첫번째 케이스와 유사하다.

None of these cases prevent Object.defineProperty() from creating an own property. The next section looks at case #3 in more detail.
이 케이스 중 그 어떤 것도 고유의 속성 생성에서 오는 Object.defineProperty()를 방지하지 못한다. 다음 섹션에서 #3 유형을 좀더 자세히 보겠다.

### 14.5.1 Overriding inherited read-only properties
### 14.5.1 상속된 일기 전용 속성 재정의

If an object obj inherits a property prop that is read-only then you can’t assign to that property:
객체 obj가 속성 prop을 상속받으면 이는 읽기 전용이고 다른 속성을 할당할 수 없다:
```js
const proto = Object.defineProperty({}, 'prop', {
    writable: false,
    configurable: true,
    value: 123,
});
const obj = Object.create(proto);
obj.prop = 456;
    // TypeError: Cannot assign to read-only property //TypeError 일기전용 속성에 할당할 수 없습니다.
```
This is similar to how an inherited property works that has a getter, but no setter. It is in line with viewing assignment as changing the value of an inherited property. It does so non-destructively: the original is not modified, but overridden by a newly created own property. Therefore, an inherited read-only property and an inherited setter-less property both prevent changes via assignment. You can, however, force the creation of an own property by defining a property:
상속받은 속성이 getter는 있지만 setter는 없이 동작하는 것과 유사하다. 할당으로 상속받은 속성 값을 변경하려는 점에서 일치한다. 이는 비파괴적이다 : 원본은 수정되지 않지만 새로 생성된 고유 속성은 재정의 된다는 것. 그러므로 상속받은 읽기전용 속성과 상속받은 setter가 없는 속성은 할당을 통해 변경되지 않는다. 하지만 속성 정의를 통해 생성된 고유 속성에는 이를 강제할 수 있다.

```js
const proto = Object.defineProperty({}, 'prop', {
    writable: false,
    configurable: true,
    value: 123,
});
const obj = Object.create(proto);
Object.defineProperty(obj, 'prop', {value: 456});
console.log(obj.prop); // 456
```

## 14.6 __proto__ in ECMAScript 6
## 14.6 ECMAScript 6의 __proto__

The property __proto__ (pronounced “dunder proto”) has existed for a while in most JavaScript engines. This section explains how it worked prior to ECMAScript 6 and what changes with ECMAScript 6.
__proto__ 속성(던더 프로토라고 발음) 은 대부분의 JavaScript 엔진에 존재해왔다. 이 섹션에서는 __proto__ 가 ECMAScript 6 이전에 어떻게 동작했었는지, 그리고 ECMAScript 6에서는 무엇이 변경되었는지를 설명할 것이다.

For this section, it helps if you know what prototype chains are. Consult Sect. “Layer 2: The Prototype Relationship Between Objects” in “Speaking JavaScript”, if necessary.
이 섹션을 위해 프로토 타입 체인에 대해 아는것이 도움이 된다. "자바스크립트를 말하다(Speaking JavaScript)"의 "Layer 2: The Prototype Relationship Between Objects(객체 프로토타입 관계)"를 참고하라.

### 14.6.1 __proto__ prior to ECMAScript 6
### 14.6.1 ECMAScript 6 이전의 __proto__
#### 14.6.1.1 Prototypes
#### 14.6.1.1 프로토타입

Each object in JavaScript starts a chain of one or more objects, a so-called prototype chain. Each object points to its successor, its prototype via the internal property [[Prototype]] (which is null if there is no successor). That property is called internal, because it only exists in the language specification and cannot be directly accessed from JavaScript. In ECMAScript 5, the standard way of getting the prototype p of an object obj is:
JavaScript 의 각 객체는 프로토타입 체인이라는 하나 이상의 객체 체인으로부터 시작한다. 각 객체는 내부 속성인 [[Prototype]]로 상속받은 객체를 가리킨다(만약 상속객체가 없다면 null이다). 이 속성은 언어 스펙에만 존재하며, JavaScript에서 직접적으로 접근할 수 없기 때문에 내부적으로만 호출된다.

```js
var p = Object.getPrototypeOf(obj);
```
There is no standard way to change the prototype of an existing object, but you can create a new object obj that has the given prototype p:
존재하는 객체의 프로토타입을 변경할 수 있는 표준적인 방법은 없지만, 새로운 객체 obj를 생성하여 프로토타입 p를 부여할 수 있다.
```js
var obj = Object.create(p);
```

#### 14.6.1.2 __proto__

A long time ago, Firefox got the non-standard property __proto__. Other browsers eventually copied that feature, due to its popularity.
오래전 파이어폭스는 비표준 __proto__ 를 도입했다. 다른 브라우저도 그 인기 때문에 결국 이 기능을 따라했다.

Prior to ECMAScript 6, __proto__ worked in obscure ways:
ECMAScript 6이전에, __proto__ 는 애매하게 동작했다.

    You could use it to get or set the prototype of any object:
    이것을 아무 객체의 프로토타입에 get 또는 set해줄 수 있었다.
```js
      var obj = {};
      var p = {};

      console.log(obj.__proto__ === p); // false
      obj.__proto__ = p;
      console.log(obj.__proto__ === p); // true
```
    However, it was never an actual property:
    하지만 실제 속성은 아니었다
```js
      > var obj = {};
      > '__proto__' in obj
      false
```

#### 14.6.1.3 Subclassing Array via __proto__
#### 14.6.1.3 __proto__ 로 배열 상속한 클래스 만들기

The main reason why __proto__ became popular was because it enabled the only way to create a subclass MyArray of Array in ES5: Array instances were exotic objects that couldn’t be created by ordinary constructors. Therefore, the following trick was used:
__proto__ 의 인기는 ES5에서 배열을 상속받은 MyArray를 생성할 유일한 방법이었기 때문이다. 배열의 인스턴스는 일반적인 생성자로는 생성할 수 없는 이례적인 객체이다. 따라서 아래의 트릭을 이용했다:
```js
function MyArray() {
    var instance = new Array(); // exotic object //이례적인 객체
    instance.__proto__ = MyArray.prototype;
    return instance;
}
MyArray.prototype = Object.create(Array.prototype);
MyArray.prototype.customMethod = function (···) { ··· };
```
Subclassing in ES6 works differently than in ES5 and supports subclassing builtins out of the box.
ES6에서의 하위클래스 생성은 ES5와는 다르게 동작하고 외부로부터의 하위클래스 생성이 내장되어 지원한다.

#### 14.6.1.4 Why __proto__ is problematic in ES5
#### 14.6.1.4 왜 ES5에서 __proto__ 가 문제가 되었나

The main problem is that __proto__ mixes two levels: the object level (normal properties, holding data) and the meta level.
__proto__ 의 주된 문제점은 두 레벨을 섞어버린다는 것이다:  객체 레벨(데이터를 가진 일반 속성)과, 메타 레벨

If you accidentally use __proto__ as a normal property (object level!), to store data, you get into trouble, because the two levels clash. The situation is compounded by the fact that you have to abuse objects as maps in ES5, because it has no built-in data structure for that purpose. Maps should be able to hold arbitrary keys, but you can’t use the key '__proto__' with objects-as-maps.
데이터를 저장을 위한 일반 속성으로써 __proto__ 를 이용하게 되면 두 레벨의 충돌 떄문에 문제가 생길것이다. ES5에서는 이를 위한 데이터 구조가 내장되지 않아 객체를 맵으로 속여야 하고, 이 때문에 문제가 발생한다. 맵은 임의의 키를 가지고 있겠지만 맵으로 속인 객체와 키' __proto__ '를 함께 사용할 수 없다.

In theory, one could fix the problem by using a symbol instead of the special name __proto__, but keeping meta-operations completely separate (as done via Object.getPrototypeOf()) is the best approach.
이론적으로 __proto__ 대신 심볼을 이용하면 문제를 해결할 수 있지만, 메타 연산을 완벽하게 분리하는 것이 가장 좋은 방법이다(Object.getPrototypeOf()를 이용해서 하듯).

### 14.6.2 The two kinds of __proto__ in ECMAScript 6
### 14.6.2 ECMAScript 6에서 __proto__ 두 종류

Because __proto__ was so widely supported, it was decided that its behavior should be standardized for ECMAScript 6. However, due to its problematic nature, it was added as a deprecated feature. These features reside in Annex B in the ECMAScript specification, which is described as follows:
__proto__ 가 폭넓게 지원되기 때문에, ECMAScript 6에서 표준화를 하기로 결정되었다. 하지만 태생적인 문제때문에 디프리케이트(사용되지 않음) 속성으로 추가되었다.
이 특성은 ECMAScript 스펙 부속서 B에 존재하며 아래와 같이 설명되어 있다.

    The ECMAScript language syntax and semantics defined in this annex are required when the ECMAScript host is a web browser. The content of this annex is normative but optional if the ECMAScript host is not a web browser.
    이 부속서에서 ECMAScript 언어 구문과 의미는 ECMAScript 호스트가 웹 브라우저일 때 요구된다. 이 부속서의 내용은 표준화 되어있지만, ECMAScript 호스트가 웹 브라우저가 아닌 경우에는 선택사항이다.

JavaScript has several undesirable features that are required by a significant amount of code on the web. Therefore, web browsers must implement them, but other JavaScript engines don’t have to.
JavaScript는 몇 가지 바람직하지 않은 특성은 웹상에서 상당량의 코드를 필요로 하게 한다. 이 때문에 웹 브라우저는 반드시 이를 구현해야 하고, 다른 자바스크립트 엔진은 그럴 필요 가 없다.

In order to explain the magic behind __proto__, two mechanisms were introduced in ES6:
__proto__ 의 마법같은 면을 소개하기 위해 ES6에서 두 종류의 매커니즘이 제안 되었다.:

    A getter and a setter implemented via Object.prototype.__proto__.
    In an object literal, you can consider the property key '__proto__' a special operator for specifying the prototype of the created objects.
    getter와 setter는 Object.prototype.__proto__ 를 구현한다.
    객체 리터럴에서 생성된 객체의 프로토 타입을 구분짓기 위해 속성키 '__proto__'를 특별 연산자로 이용해도 된다.

#### 14.6.2.1 Object.prototype.__proto__

ECMAScript 6 enables getting and setting the property __proto__ via a getter and a setter stored in Object.prototype. If you were to implement them manually, this is roughly what it would look like:
ECMAScript 6 는 Object.prototype에 내장된 getter와 setter를 이용해 __proto__ 를 지정하거나 이용할 수 있다. 만약 정석대로 구현했다면 대략 아래와 같을 것이다 :
```js
Object.defineProperty(Object.prototype, '__proto__', {
    get() {
        const _thisObj = Object(this);
        return Object.getPrototypeOf(_thisObj);
    },
    set(proto) {
        if (this === undefined || this === null) {
            throw new TypeError();
        }
        if (!isObject(this)) {
            return undefined;
        }
        if (!isObject(proto)) {
            return undefined;
        }
        const status = Reflect.setPrototypeOf(this, proto);
        if (! status) {
            throw new TypeError();
        }
    },
});
function isObject(value) {
    return Object(value) === value;
}
```
The getter and the setter for __proto__ in the ES6 spec:
ES6 스펙의 __proto__ 의 getter와 setter:
```js
    get Object.prototype.__proto__
    set Object.prototype.__proto__
```

#### 14.6.2.2 The property key __proto__ as an operator in an object literal
#### 14.6.2.2 객체 리터럴에서 연산자로서의 속성 키 __proto__

If __proto__ appears as an unquoted or quoted property key in an object literal, the prototype of the object created by that literal is set to the property value:
객체 리터럴에서 __proto__ 에 따옴표를 주거나 혹은 빼도, 객체 리터럴로 생성된 이 프로토 타입에 속성값을 줄 수 있다:
```js
> Object.getPrototypeOf({ __proto__: null })
null
> Object.getPrototypeOf({ '__proto__': null })
null
```
Using the string value '__proto__' as a computed property key does not change the prototype, it creates an own property:
문자열 '__proto__' 를 속성 키로 이용하는 것이 프로토타입을 변경하는 것이 아니고, 새로운 고유 속성으로 생성된다.
```js
> const obj = { ['__proto__']: null };
> Object.getPrototypeOf(obj) === Object.prototype
true
> Object.keys(obj)
[ '__proto__' ]
```
The special property key '__proto__' in the ES6 spec:
ES6 스펙에서 특별한 속성 키'__proto__'는:

    __proto__ Property Names in Object Initializers
    __proto__ 객체 이니셜라이저의 속성명이다

### 14.6.3 Avoiding the magic of __proto__
### 14.6.3 __proto__ 의 마법을 회피하기
#### 14.6.3.1 Defining (not assigning) __proto__
#### 14.6.3.1 __proto__ 정의(할당 아님)

In ECMAScript 6, if you define the own property __proto__, no special functionality is triggered and the getter/setter Object.prototype.__proto__ is overridden:
ECMAScript 6에서 고유 속성 __proto__ 을 정의 하면, 아무런 특별한 기능을 발생시키지 않고 Object.prototype.__proto__ 의 getter/setter가 재정의 된다.
```js
const obj = {};
Object.defineProperty(obj, '__proto__', { value: 123 })

Object.keys(obj); // [ '__proto__' ]
console.log(obj.__proto__); // 123
```

#### 14.6.3.2 Objects that don’t have Object.prototype as a prototype
#### 14.6.3.2 Object.prototype를 프로토타입으로 가지고 있지 않은 객체

The __proto__ getter/setter is provided via Object.prototype. Therefore, an object without Object.prototype in its prototype chain doesn’t have the getter/setter, either. In the following code, dict is an example of such an object – it does not have a prototype. As a result, __proto__ now works like any other property:
__proto__ 의 getter/setter는 Object.prototype에서 나온 것이다. 따라서, 프로토 타입 체인에 Object.prototype가 없는 객체는 getter/setter가 없다. 아래의 코드에서 dict는 그런 객체이다 - 프로토타입이 없는. 결과로 __proto__ 는 다른 속성과 같이 동작한다.
```js
> const dict = Object.create(null);
> '__proto__' in dict
false
> dict.__proto__ = 'abc';
> dict.__proto__
'abc'
```

#### 14.6.3.3 __proto__ and dict objects
#### 14.6.3.3 __proto__ 와 사전 객체

If you want to use an object as a dictionary then it is best if it doesn’t have a prototype. That’s why prototype-less objects are also called dict objects. In ES6, you don’t even have to escape the property key '__proto__' for dict objects, because it doesn’t trigger any special functionality.
객체를 사전으로 이용하려면 객체에 프로토타입이 없어야 한다. 이 때문에 프로토타입이 없는 객체를 사전객체(dict object)로 부르기도 한다. ES6의 사전객체 객체 키 '__proto__'가 아무 기능도 하지 않기 때문에, 이를 회피할 필요도 없다.

__proto__ as an operator in an object literal lets you create dict objects more concisely:
연산자로서의 __proto__ 는 객체 리터럴에서 사전 객체를 더 간결하게 생성할 수 있게 해준다.
```js
const dictObj = {
    __proto__: null,
    yes: true,
    no: false,
};
```
Note that in ES6, you should normally prefer the built-in data structure Map to dict objects, especially if keys are not fixed.
ES6에서, 일반적으로 키값이 고정되지 않은 경우, 내장 자료 구조인 맵을 사전객체로 이용하기도 함을 기억하자.

#### 14.6.3.4 __proto__ and JSON
#### 14.6.3.4 __proto__ 와 JSON

Prior to ES6, the following could happen in a JavaScript engine:
ES6 이전에는 자바스크립트 엔진에서 아래와 같은 일이 발생할 수 있었다:
```js
> JSON.parse('{"__proto__": []}') instanceof Array
true
```
With __proto__ being a getter/setter in ES6, JSON.parse() works fine, because it defines properties, it doesn’t assign them (if implemented properly, an older version of V8 did assign).
ES6에서는 getter/setter인 __proto__ 와 JSON.parse()는 잘 동작하는데 이는 속성을 할당하는게 아니라 정의하기 때문이다.(제대로 구현되었다면 그렇고, 예전 버전의 v8엔진에서는 할당했었다.)

JSON.stringify() isn’t affected by __proto__, either, because it only considers own properties. Objects that have an own property whose name is __proto__ work fine:
JSON.stringify() 도 __proto__ 에 영향을 받지 않는데, 이는 고유의 속성만을 고려하기 때문이다. __proto__ 라는 고유 속성명을 가진 객체는 제대로 동작한다 :
```js
> JSON.stringify({['__proto__']: true})
'{"__proto__":true}'
```

### 14.6.4 Detecting support for ES6-style __proto__
### 14.6.4 ES6 스타일로 __proto__ 동작하는지 알아보기

Support for ES6-style __proto__ varies from engine to engine. Consult kangax’ ECMAScript 6 compatibility table for information on the status quo:
지원하는 ES6 스타일의 __proto__ 는 엔진별로 다양하다. kangax의 ECMAScript 6 호환성 테이블에 현상태의 정보가 설명되어 있다:

```js
    Object.prototype.__proto__
    __proto__ in object literals
    객체 리터럴의 __proto__
```
The following two sections describe how you can programmatically detect whether an engine supports either of the two kinds of __proto__.
뒤에 오는 두 섹션에서 프로그램적으로 엔진이 두 종류의 __proto__ 를 지원하는지 여부를 어떻게 알 수 있는지 설명되어 있다.

#### 14.6.4.1 Feature: __proto__ as getter/setter
#### 14.6.4.1 특징 : getter/setter로써의 __proto__

A simple check for the getter/setter:
간단한 getter/setter 체크 방법
```js
var supported = {}.hasOwnProperty.call(Object.prototype, '__proto__');
```
A more sophisticated check:
좀더 복잡한 체크 방법:
```js
var desc = Object.getOwnPropertyDescriptor(Object.prototype, '__proto__');
var supported = (
    typeof desc.get === 'function' && typeof desc.set === 'function'
);
```

#### 14.6.4.2 Feature: __proto__ as an operator in an object literal
#### 14.6.4.2 특징 : 객체 리터럴에서 연산자로서의 __proto__

You can use the following check:
아래의 방법으로 체크할 수 있다 :
```js
var supported = Object.getPrototypeOf({__proto__: null}) === null;
```

### 14.6.5 __proto__ is pronounced “dunder proto”
### 14.6.5 __proto__ 는 "던더 프로토(dunder proto)"라고 발음한다

Bracketing names with double underscores is a common practice in Python to avoid name clashes between meta-data (such as __proto__) and data (user-defined properties). That practice will never become common in JavaScript, because it now has symbols for this purpose. However, we can look to the Python community for ideas on how to pronounce double underscores.
두 개의 밑줄로 감싸진 이름은 파이썬에서 메타 데이타(__proto__ 와 같은)나 데이터(사용자 지정 속성)와 변수명의 충돌을 피하기 위해 일반적으로 이용하는 방법이다. 이 방법은 자바스크립트에서는 일반적으로 쓰일 일이 절대 없을텐데, 이는 이제 심볼을 사용하기 때문이다. 하지만 두번 밑줄의 발음에 대한 아이디러를 파이선 커뮤니티에서 찾을 수 있다.

The following pronunciation has been suggested by Ned Batchelder:
네드 배챌러가 아래의 발음을 제안했다:

    An awkward thing about programming in Python: there are lots of double underscores. For example, the standard method names beneath the syntactic sugar have names like __getattr__, constructors are __init__, built-in operators can be overloaded with __add__, and so on. […]
    파이썬으로 개발할 때 거북한 것은 : 두 개의 밑줄이 너무 많다는 것이다. 예를 들어 표준 메서드 명은 구문 아래 __getattr__, 생성자는 __init__, __add__ 로 내장 연산자가 추가 될 것이고, 또..

    My problem with the double underscore is that it’s hard to say. How do you pronounce __init__? “underscore underscore init underscore underscore”? “under under init under under”? Just plain “init” seems to leave out something important.
    이중 밑줄에 대한 나의 문제는 부르기 너무 어렵다는 것이다. __init__ 을 어떻게 발음할 것인가? 밑줄밑줄 이닛 민툴밑줄 ? 밑밑 이닛 밑밑 ? 그냥 이닛이라고 발음하기에는 뭔가 중요한 것을 빼먹은 느낌이 난다.

    I have a solution: double underscore should be pronounced “dunder”. So __init__ is “dunder init dunder”, or just “dunder init”.
    나의 해답은 : 이중 밑줄을 "던더"라고 발음하는 것이다. 그러면 __init__ 은 던더 이닛 던더 혹은 던더 이닛이 된다.

Thus, __proto__ is pronounced “dunder proto”. The chances for this pronunciation catching on are good, JavaScript creator Brendan Eich uses it.
고로, __proto__ 는 던더 프로토 로 발음한다. 이 발음이 괜찮았던지 자바스크립트 창시자인 브랜든 아이크도 그렇게 발음하고 있다.

### 14.6.6 Recommendations for __proto__
### 14.6.6 __proto__ 의 권장사항

It is nice how well ES6 turns __proto__ from something obscure into something that is easy to understand.
ES6이 __proto__ 를 애매한 무언가에서 이해하기 쉬운 무언가로 바꾼것은 아주 잘된일이다.

However, I still recommend not to use it. It is effectively a deprecated feature and not part of the core standard. You can’t rely on it being there for code that must run on all engines.
하지만 이것을 사용하지 않는 것을 권한다. 이는 실제로 디프리케이트된 특징이고 표준의 주요한 부분도 아니다. 모든 엔진에서 반드시 돌아가야 하는 코드로 존재한다고 기대할 수 없다.

More recommendations:
다른 권장사항:

    Use Object.getPrototypeOf() to get the prototype of an object.
    Prefer Object.create() to create a new object with a given prototype. Avoid Object.setPrototypeOf(), which hampers performance on many engines.
    I actually like __proto__ as an operator in an object literal. It is useful for demonstrating prototypal inheritance and for creating dict objects. However, the previously mentioned caveats do apply.
    객체의 프로토타입을 얻기 위해 Object.getPrototypeOf()를 이용한다.
    주어진 프로토타입으로 새로운 객체를 생성하기 위해 Object.create()를 이용한다. 엔진에 부하가 걸리는 Object.setPrototypeOf()의 이용을 자제한다.
    저자는 __proto__ 를 객체 리터럴의 연산자로 사용하는 것을 선호한다. 프로토 타입 상속을 설명하는데 유용하고 사전 객체를 생성할때도 유용하다. 하지만, 하지만 앞서 언급한 주의 사항을 적용한다.

## 14.7 Enumerability in ECMAScript 6
## 14.7 ECMAScript 6에서 열거가능성

Enumerability is an attribute of object properties. This section explains how it works in ECMAScript 6. Let’s first explore what attributes are.
열거가능성은 객체 프로퍼티의 한 속성이다. ECMAScript 6에서 그것이 어떻게 동작하는지 설명할 것이다. 그 첫번째 속성이 무엇인지 알아보자.

### 14.7.1 Property attributes
### 14.7.1 프로퍼티 속성

Each object has zero or more properties. Each property has a key and three or more attributes, named slots that store the data of the property (in other words, a property is itself much like a JavaScript object or like a record with fields in a database).
각 객체는 0개 이상의 속성을 가지고 있다. 각 프로퍼티는 키를 가지고 있고 3개 이상의 속성, 그리고 프로퍼티 데이터를 저장하는 네임드 슬롯을 가진다(다시 말해, 속성은 자바스크립트 객체 혹은 디비의 데이터 필드 그 자체이다.).

ECMAScript 6 supports the following attributes (as does ES5):
ECMAScript 6은 아래의 속성을 지원한다(ES5처럼):

    All properties have the attributes:
        enumerable: Setting this attribute to false hides the property from some operations.
        configurable: Setting this attribute to false prevents several changes to a property (attributes except value can’t be change, property can’t be deleted, etc.).
    Normal properties (data properties, methods) have the attributes:
        value: holds the value of the property.
        writable: controls whether the property’s value can be changed.
    Accessors (getters/setters) have the attributes:
        get: holds the getter (a function).
        set: holds the setter (a function).
    모든 프로퍼티는 속성을 가진다:
        열거가능 : 이 속성을 false로 두면 일부 작업의 프로퍼티를 숨길 수 있다.
        구성 : 이 속성을 false로 두면 몇가지 프로퍼티 변경을 방지할 수 있다.(값을 제외한 속성은 바꿀 수 없고, 프로퍼티는 지워지지 않고 등등.)
    일반적 프로퍼티는 (데이터 속성, 메서드) 는 속성을 가진다:
        값 : 프로퍼티의 값을 가진다.
        쓸 수 있음 : 프로퍼티의 값을 조절하여 바꿀 수 있다.
    접근자(getter/setter) 도 속성을 가진다:
        get : getter를 가진다(함수).
        set : setter를 가진다(함수).

You can retrieve the attributes of a property via Object.getOwnPropertyDescriptor(), which returns the attributes as a JavaScript object:
속성의 속성을 Object.getOwnPropertyDescriptor()로 알 수 있는데, 이는 자바스크립트 객체의 속성을 반환한다.
```js
> const obj = { foo: 123 };
> Object.getOwnPropertyDescriptor(obj, 'foo')
{ value: 123,
  writable: true,
  enumerable: true,
  configurable: true }
```
This section explains how the attribute enumerable works in ES6. All other attributes and how to change attributes is explained in Sect. “Property Attributes and Property Descriptors” in “Speaking JavaScript”.
이 섹션에서는 ES6에서 속성이 어떤식으로 열거가능한 작업을 하는지를 설명한다. 모든 다른 속성과 속성을 어떻게 변경하는지는 "자바스크립트를 말하다"의 "프로퍼티 속성과 프로퍼티 디스크립터" 장에 설명되어 있다.

### 14.7.2 Constructs affected by enumerability
### 14.7.2 생성자는 열거가능성에 영향받는다.

ECMAScript 5:

    for-in loop: traverses the string keys of own and inherited enumerable properties.
    Object.keys(): returns the string keys of enumerable own properties.
    JSON.stringify(): only stringifies enumerable own properties with string keys.
    for-in 루프 : 고유한 프로퍼티와 상속받은 프로퍼티의 문자 키값을 나열한다.
    Object.keys(): 고유 프로퍼티의 문자열 키값을 반환한다.
    JSON.stringify(): 문자 키를 가진 열거 가능한 고유 프로퍼티를 문자화 한다.

ECMAScript 6:

    Object.assign(): only copies enumerable own properties (both string keys and symbol keys are considered).
    Object.assign(): 열거 가능한 고유 프로퍼티만 복사한다.(문자열, 심볼 키 값 모두)

for-in is the only built-in operations where enumerability matters for inherited properties. All other operations only work with own properties.
상속받은 프로퍼티의 열거가능한 인자에서도 작동하는 for-in 은 유일한 내장 연산자이다. 다른 연산자는 고유 프로퍼티에서만 동작한다.

### 14.7.3 Use cases for enumerability
### 14.7.3 열거가능성의 사용 예

Unfortunately, enumerability is quite an idiosyncratic feature. This section presents several use cases for it and argues that, apart from protecting legacy code from breaking, its usefulness is limited.
안타깝게도 enumerability는 아주 독특한 특징이다. 이 섹션에서는 몇 가지 사용 예와 레거시 코드 파괴로를 막기 위해 제한된 사용 예쩨를 보여준다.

#### 14.7.3.1 Use case: Hiding properties from the for-in loop
#### 14.7.3.1 사용 예 : for-in 루프에서 프로퍼티 숨기기

The for-in loop traverses all enumerable properties of an object, own and inherited ones. Therefore, the attribute enumerable is used to hide properties that should not be traversed. That was the reason for introducing enumerability in ECMAScript 1.
for-in 루프는 객체의 모든 열거가능한 고유한 프로퍼티와 상속받은 프로퍼티를 보여준다. 속성 이너머러블이 프로퍼티를 숨기는데 이용되고 이는 결국 보여지지 않는다. ECMAScript 1에서 이너머러빌리티가 소개된 이유이다.

##### 14.7.3.1.1 Non-enumerability in the language
##### 14.7.3.1.1 언어의 Non-enumerability

Non-enumerable properties occur in the following locations in the language:
언어에서 아래의 경우에 non-enumerable 프로퍼티가 나타난다.

    All prototype properties of built-in classes are non-enumerable:
    클래스에 내장된 모든 프로토타입 프로퍼티는 non-enumerable:
```js
      > const desc = Object.getOwnPropertyDescriptor.bind(Object);
      > desc(Object.prototype, 'toString').enumerable
      false
```
    All prototype properties of classes are non-enumerable:
    객체의 모든 프로퍼티도 열거 불가능:
```js
      > desc(class {foo() {}}.prototype, 'foo').enumerable
      false
```
    In Arrays, length is not enumerable, which means that for-in only traverses indices. (However, that can easily change if you add a property via assignment, which is makes it enumerable.)
    배열에서 length는 이너머러블이 아닌데 이는, for-in에서 색인으로 이용됨을 의미한다. (하지만, 할당으로 프로퍼티를 추가하면 쉽게 바뀐다).
```js
      > desc([], 'length').enumerable
      false
      > desc(['a'], '0').enumerable
      true
```
The main reason for making all of these properties non-enumerable is to hide them (especially the inherited ones) from legacy code that uses the for-in loop or $.extend() (and similar operations that copy both inherited and own properties; see next section). Both operations should be avoided in ES6. Hiding them ensures that the legacy code doesn’t break.
모든 프로퍼티를 열거불가능하게 만드는 가장 큰 이유는 $.extend()를 이용하는 레거시 코드로 부터 숨기기 위해서이다(특히 상속받은것).(상속받거나 고유의 프로퍼티를 복자하는 동작과 유사).
이 동작 모두 ES6에서는 지양한다. 숨기는 것이 코드가 파괴되지 않음을 보장한다.

#### 14.7.3.2 Use case: Marking properties as not to be copied
#### 14.7.2.2 사용 예 : 복사하지 않고 프로퍼티 등록
##### 14.7.3.2.1 Historical precedents
##### 14.7.3.2.1 과거의 예시

When it comes to copying properties, there are two important historical precedents that take enumerability into consideration:
프로퍼티를 복사할 때, 열거가능성을 구성에 넣기 위해 두 가지 주요한 선례가 있다.
```js
    Prototype’s Object.extend(destination, source)

      const obj1 = Object.create({ foo: 123 });
      Object.extend({}, obj1); // { foo: 123 }

      const obj2 = Object.defineProperty({}, 'foo', {
          value: 123,
          enumerable: false
      });
      Object.extend({}, obj2) // {}

    jQuery’s $.extend(target, source1, source2, ···) copies all enumerable own and inherited properties of source1 etc. into own properties of target.

      const obj1 = Object.create({ foo: 123 });
      $.extend({}, obj1); // { foo: 123 }

      const obj2 = Object.defineProperty({}, 'foo', {
          value: 123,
          enumerable: false
      });
      $.extend({}, obj2) // {}
```
Problems with this way of copying properties:
이 방식으로 프로퍼티 복사하게 되면 문제점 :

    Turning inherited source properties into own target properties is rarely what you want. That’s why enumerability is used to hide inherited properties.
    Which properties to copy and which not often depends on the task at hand, it rarely makes sense to have a single flag for everything. A better choice is to provide the copying operation with a predicate (a callback that returns a boolean) that tells it when to consider a property.
    상속받은 소스 프로퍼티를 타겟 프로퍼티로 전환하는 것을 거의 드물다. 열거가능성이 상속받은 프로퍼티를 숨기는데 이용되는 이유이다.
    어떤 프로퍼티가 복사되고 어떤것은 아닌것은 작업에 따라 다른데, 전체의 한가지 지표로 드물게 .........?
    서술부(boolean을 리턴하는 콜백)를 가진 복사 작업을 제공하는 것이 가장 좋은 선택지이다.

The only instance property that is non-enumerable in the standard library is property length of Arrays. However, that property only needs to be hidden due to it magically updating itself via other properties. You can’t create that kind of magic property for your own objects (short of using a Proxy).
표준 라이브러리의 non-enumerable한 유일한 인스턴스 프로퍼티는 배열의 length 프로퍼티이다. 하지만 이 프로퍼티는 다른 프로퍼티를 통해 마법적으로 업데이트되기 때문에 숨겨져야만 한다.

##### 14.7.3.2.2 ES6: Object.assign()

In ES6, Object.assign(target, source_1, source_2, ···) can be used to merge the sources into the target. All own enumerable properties of the sources are considered (that is, keys can be either strings or symbols). Object.assign() uses a “get” operation to read a value from a source and a “set” operation to write a value to the target.
ES6에서 Object.assign(target, source_1, source_2, ···)은 소스를 타겟에 병합하는데 이용된다. 소스의 모든 고유 열거가능 프로퍼티가 가능하다.(이는 키는 문자열, 심볼 모두 가능하다는 것)
Object.assign() "get"연산을 소스의 값을 읽는데 이용하고 "set"연산을 타겟에 그 값을 쓰는데 이용한다.

With regard to enumerability, Object.assign() continues the tradition of Object.extend() and $.extend(). Quoting Yehuda Katz:
열거가능성을 염두해두고, Object.assign()은 Object.extend()와 $.extend()의 전동을 이어간다. Yehuda Katz를 인용하면 :

    Object.assign would pave the cowpath of all of the extend() APIs already in circulation. We thought the precedent of not copying enumerable methods in those cases was enough reason for Object.assign to have this behavior.
    Object.assign는 이미 환경에 존재하는 모든 extend() API의 발자취를 따라 길을 닦는다. 그런 경우에서 열거가능 매서드를 복사하지 않는 선례에서 Object.assign 이런 동작을 하는 많은 이유를 생각해 보았다.

In other words: Object.assign() was created with an upgrade path from $.extend() (and similar) in mind. Its approach is cleaner than $.extend’s, because it ignores inherited properties.
다시 말해 : Object.assign()은 $.extend() 의 업그레이드 된 길을 염두해 생성되었다. 이런 접근은 $.extend 보다 명확한데 이는 상속받은 프로퍼티를 무시하기 때문이다.

### 14.7.3.3 Marking properties as private
### 14.7.3.3 프로퍼티를 비공개로 두기

If you make a property non-enumerable, it can’t by seen by Object.keys() and the for-in loop, anymore. With regard to those mechanisms, the property is private.
열거가능하지 않은 프로퍼티를 만든다면, Object.keys()나 for-in 루프에서 더이상 보여서는 안된다. 이런 메카니즘으로 프로퍼티는 비공개이다.

However, there are several problems with this approach:
이러한 접근에는 몇가지 문제가 있다:

    When copying an object, you normally want to copy private properties. That clashes making properties non-enumerable that shouldn’t be copied (see previous section).
    The property isn’t really private. Getting, setting and several other mechanisms make no distinction between enumerable and non-enumerable properties.
    When working with code either as source or interactively, you can’t immediately see whether a property is enumerable or not. A naming convention (such as prefixing property names with an underscore) is easier to discover.
    You can’t use enumerability to distinguish between public and private methods, because methods in prototypes are non-enumerable by default.
    보통 객체를 복사할 때 비공개 프로퍼티도 함께 복사하길 원한다. 이 충돌은 열거가능하지 않은 속성을 만들어내, 복사할 수 없게 만든다(앞의 섹션을 봐라).
    그 프로퍼티는 정말 비공개는 아니다. getting, setting 그리고 몇가지 다른 메커니즘은 열거, 비열거 프로퍼티 사이에 구분을 두지 않는다.
    코드가 소스 혹은 상호작용으로 동작할 때, 프로퍼티가 열거가능한지 여부를 바로 알 수 없다. ??

### 14.7.3.4 Hiding own properties from JSON.stringify()
### 14.7.3.4 고유의 프로퍼티를 JSON.stringify()로부터 숨기기

JSON.stringify() does not include properties in its output that are non-enumerable. You can therefore use enumerability to determine which own properties should be exported to JSON. This use case is similar to marking properties as private, the previous use case. But it is also different, because this is more about exporting and slightly different considerations apply. For example: Can an object be completely reconstructed from JSON?
JSON.stringify() 는 출력시에 프로퍼티를 포함하지 않고 열거불가능하다. 이는 또 다른데, 내보내기에 좀더 관련이 있고 적용하는 것과는 조금 거리가 있기 때문이다. ??
예를 들어: 어떤 객체가 JSON에서 완벽히 재건될 수 있을까?

An alternative for specifying how an object should be converted to JSON is to use toJSON():
```js
const obj = {
    foo: 123,
    toJSON() {
        return { bar: 456 };
    },
};
JSON.stringify(obj); // '{"bar":456}'
```
I find toJSON() cleaner than enumerability for the current use case. It also gives you more control, because you can export properties that don’t exist on the object.
현재의 사용 예에는 열거가능성보다 toJSON()이 좀더 확실함을 발견했다. toJSON()은 쉽게 컨트롤 할 수 있는데, 객체에 존재하지 않는 프로퍼티를 내보낼 수 있기 때문이다.

### 14.7.4 Naming inconsistencies
### 14.7.4 불일치 네이밍

In general, a shorter name means that only enumerable properties are considered:
일반적으로 짧은 네임은 열거가능 프로퍼티만 고려한다.

    Object.keys() ignores non-enumerable properties
    Object.getOwnPropertyNames() lists all property names
    Object.keys() 는 열거불가능한 프로퍼티는 무시한다.
    Object.getOwnPropertyNames()는 모든 프로퍼티명을 나열한다.

However, Reflect.ownKeys() deviates from that rule, it ignores enumerability and returns the keys of all properties. Additionally, starting with ES6, the following distinction is made:
하지만 Reflect.ownKeys()는 규칙으로부터 벗어나있다. Reflect.ownKeys()는 열거가능성을 무시하고 모든 프로퍼티 키를 반환한다. 게다가 ES6가 시작하면서 아래의 구분이 생겼다:

    Property keys are either strings or symbols.
    Property names are only strings.
    프로퍼티 키는 문자 혹은 심볼이다.
    프로퍼티 네임은 오로지 문자열이다.

Therefore, a better name for Object.keys() would now be Object.names().
따라서, Object.keys()의 더 좋은 명칭은 Object.names() 이다.

### 14.7.5 Looking ahead
### 14.7.5 돌아보다

It seems to me that enumerability is only suited for hiding properties from the for-in loop and $.extend() (and similar operations). Both are legacy features, you should avoid them in new code. As for the other use cases:
열거가능성은 for-in 루프와 $.extend()(혹은 비슷한 연산)의 숨김 프로퍼티에 적합하다고 생각한다. 둘다 레거시 특징이지만, 새로운 코드에서는 사용하지 않는것이 좋다. 다른 사용 예에서 보듯:

    I don’t think there is a need for a general flag specifying whether or not to copy a property.
    Non-enumerability does not work well as a way to keep properties private.
    The toJSON() method is more powerful and explicit than enumerability when it comes to controlling how to convert an object to JSON.
    프로퍼티를 복사할지를 지정하는 일반적인 플래그기 팔요하다고 생각하지 않는다.
    열거불가능성은 프로퍼티를 비공개로 두기 위한 방법으로 그렇게 잘 동작하는건 아니다.
    객체를 제이슨으로 변환할 때에는 열거가능성에 비해 toJSON()이 보다 강력하고 명확하다.

I’m not sure what the best strategy is for enumerability going forward. If, with ES6, we had started to pretend that it didn’t exist (except for making prototype properties non-enumerable so that old code doesn’t break), we might eventually have been able to deprecate enumerability. However, Object.assign() considering enumerability runs counter that strategy (but it does so for a valid reason, backward compatibility).
열거가능성이 어떤 방향으로 나아가는 것이 좋을지는 모르겠다. 만약 ES6에서 존재하지 않는다고 생각하고 시작해왔다면, 최종적으로 열거가능성을 사용하지 않을 수 있을 것이다. 하지만 Object.assign()가 열거가능성을 이 생각과 반대되게 사용하는 것을 고려하고 있다.

In my own ES6 code, I’m not using enumerability, except (implicitly) for classes whose prototype methods are non-enumerable.
저자의 ES6 코드에서, 프로토타입 메서드가 열거가능하지 않는 클래스를 제외하고는 열거가능성을 사용하지 않는다.

Lastly, when using an interactive command line, I occasionally miss an operation that returns all property keys of an object, not just the own ones (Reflect.ownKeys). Such an operation would provide a nice overview of the contents of an object.
마지막으로 상호작용하는 커맨드 라인을 이용하는 경우, 때로 객체의 고유키 뿐 아니라, 모든 키를 반환하는 연산을 깜빡한다.

## 14.8 Customizing basic language operations via well-known symbols

This section explains how you can customize basic language operations by using the following well-known symbols as property keys:
```js
    Symbol.hasInstance (method)
    Lets an object C customize the behavior of x instanceof C.
    Symbol.toPrimitive (method)
    Lets an object customize how it is converted to a primitive value. This is the first step whenever something is coerced to a primitive type (via operators etc.).
    Symbol.toStringTag (string)
    Called by Object.prototype.toString() to compute the default string description of an object obj: ‘[object ‘+obj[Symbol.toStringTag]+’]’.
    Symbol.unscopables (Object)
    Lets an object hide some properties from the with statement.
```
### 14.8.1 Property key Symbol.hasInstance (method)

An object C can customize the behavior of the instanceof operator via a method with the key Symbol.hasInstance that has the following signature:
```js
[Symbol.hasInstance](potentialInstance : any)
```
x instanceof C works as follows in ES6:

    If C is not an object, throw a TypeError.
    If the method exists, call C[Symbol.hasInstance](x), coerce the result to boolean and return it.
    Otherwise, compute and return the result according to the traditional algorithm (C must be callable, C.prototype in the prototype chain of x, etc.).

#### 14.8.1.1 Uses in the standard library

The only method in the standard library that has this key is:

    Function.prototype[Symbol.hasInstance]()

This is the implementation of instanceof that all functions (including classes) use by default. Quoting the spec:

    This property is non-writable and non-configurable to prevent tampering that could be used to globally expose the target function of a bound function.

The tampering is possible because the traditional instanceof algorithm, OrdinaryHasInstance(), applies instanceof to the target function if it encounters a bound function.

Given that this property is read-only, you can’t use assignment to override it, as mentioned earlier.
#### 14.8.1.2 Example: checking whether a value is an object

As an example, let’s implement an object ReferenceType whose “instances” are all objects, not just objects that are instances of Object (and therefore have Object.prototype in their prototype chains).
```js
const ReferenceType = {
    [Symbol.hasInstance](value) {
        return (value !== null
            && (typeof value === 'object'
                || typeof value === 'function'));
    }
};
const obj1 = {};
console.log(obj1 instanceof Object); // true
console.log(obj1 instanceof ReferenceType); // true

const obj2 = Object.create(null);
console.log(obj2 instanceof Object); // false
console.log(obj2 instanceof ReferenceType); // true
```
### 14.8.2 Property key Symbol.toPrimitive (method)

Symbol.toPrimitive lets an object customize how it is coerced (converted automatically) to a primitive value.

Many JavaScript operations coerce values to the types that they need.

    The multiplication operator (\*) coerces its operands to numbers.
    new Date(year, month, date) coerces its parameters to numbers.
    parseInt(string , radix) coerces its first parameter to a string.

The following are the most common types that values are coerced to:

    Boolean: Coercion returns true for truthy values, false for falsy values. Objects are always truthy (even new Boolean(false)).
    Number: Coercion converts objects to primitives first. Primitives are then converted to numbers (null → 0, true → 1, '123' → 123, etc.).
    String: Coercion converts objects to primitives first. Primitives are then converted to strings (null → 'null', true → 'true', 123 → '123', etc.).
    Object: The coercion wraps primitive values (booleans b via new Boolean(b), numbers n via new Number(n), etc.).

Thus, for numbers and strings, the first step is to ensure that a value is any kind of primitive. That is handled by the spec-internal operation ToPrimitive(), which has three modes:

    Number: the caller needs a number.
    String: the caller needs a string.
    Default: the caller needs either a number or a string.

The default mode is only used by:

    Equality operator (==)
    Addition operator (+)
    new Date(value) (exactly one parameter!)

If the value is a primitive then ToPrimitive() is already done. Otherwise, the value is an object obj, which is converted to a primitive as follows:

    Number mode: Return the result of obj.valueOf() if it is primitive. Otherwise, return the result of obj.toString() if it is primitive. Otherwise, throw a TypeError.
    String mode: works like Number mode, but toString() is called first, valueOf() second.
    Default mode: works exactly like Number mode.

This normal algorithm can be overridden by giving an object a method with the following signature:
```js
[Symbol.toPrimitive](hint : 'default' | 'string' | 'number')
```
In the standard library, there are two such methods:

    Symbol.prototype[Symbol.toPrimitive](hint) prevents toString() from being called (which throws an exception).
    Date.prototype[Symbol.toPrimitive](hint) This method implements behavior that deviates from the default algorithm. Quoting the specification: “Date objects are unique among built-in ECMAScript object in that they treat 'default' as being equivalent to 'string'. All other built-in ECMAScript objects treat 'default' as being equivalent to 'number'.”

#### 14.8.2.1 Example

The following code demonstrates how coercion affects the object obj.
```js
const obj = {
    [Symbol.toPrimitive](hint) {
        switch (hint) {
            case 'number':
                return 123;
            case 'string':
                return 'str';
            case 'default':
                return 'default';
            default:
                throw new Error();
        }
    }
};
console.log(2 * obj); // 246
console.log(3 + obj); // '3default'
console.log(obj == 'default'); // true
console.log(String(obj)); // 'str'
```
### 14.8.3 Property key Symbol.toStringTag (string)

In ES5 and earlier, each object had the internal own property [[Class]] whose value hinted at its type. You could not access it directly, but its value was part of the string returned by Object.prototype.toString(), which is why that method was used for type checks, as an alternative to typeof.

In ES6, there is no internal property [[Class]], anymore, and using Object.prototype.toString() for type checks is discouraged. In order to ensure the backwards-compatibility of that method, the public property with the key Symbol.toStringTag was introduced. You could say that it replaces [[Class]].

Object.prototype.toString() now works as follows:

    Convert this to an object obj.
    Determine the toString tag tst of obj.
    Return '[object ' + tst + ']'.

#### 14.8.3.1 Default toString tags

The default values for various kinds of objects are shown in the following table.
Value 	toString tag
undefined 	'Undefined'
null 	'Null'
An Array object 	'Array'
A string object 	'String'
arguments 	'Arguments'
Something callable 	'Function'
An error object 	'Error'
A boolean object 	'Boolean'
A number object 	'Number'
A date object 	'Date'
A regular expression object 	'RegExp'
(Otherwise) 	'Object'

Most of the checks in the left column are performed by looking at internal properties. For example, if an object has the internal property [[Call]], it is callable.

The following interaction demonstrates the default toString tags.
```js
> Object.prototype.toString.call(null)
'[object Null]'
> Object.prototype.toString.call([])
'[object Array]'
> Object.prototype.toString.call({})
'[object Object]'
> Object.prototype.toString.call(Object.create(null))
'[object Object]'
```
#### 14.8.3.2 Overriding the default toString tag

If an object has an (own or inherited) property whose key is Symbol.toStringTag then its value overrides the default toString tag. For example:
```js
> ({}.toString())
'[object Object]'
> ({[Symbol.toStringTag]: 'Foo'}.toString())
'[object Foo]'
```
Instances of user-defined classes get the default toString tag (of objects):
```js
class Foo { }
console.log(new Foo().toString()); // [object Object]
```
One option for overriding the default is via a getter:
```js
class Bar {
    get [Symbol.toStringTag]() {
      return 'Bar';
    }
}
console.log(new Bar().toString()); // [object Bar]
```
In the JavaScript standard library, there are the following custom toString tags. Objects that have no global names are quoted with percent symbols (for example: %TypedArray%).

    Module-like objects:
        JSON[Symbol.toStringTag] → 'JSON'
        Math[Symbol.toStringTag] → 'Math'
    Actual module objects M: M[Symbol.toStringTag] → 'Module'
    Built-in classes
        ArrayBuffer.prototype[Symbol.toStringTag] → 'ArrayBuffer'
        DataView.prototype[Symbol.toStringTag] → 'DataView'
        Map.prototype[Symbol.toStringTag] → 'Map'
        Promise.prototype[Symbol.toStringTag] → 'Promise'
        Set.prototype[Symbol.toStringTag] → 'Set'
        get %TypedArray%.prototype[Symbol.toStringTag] → 'Uint8Array' etc.
        WeakMap.prototype[Symbol.toStringTag] → 'WeakMap'
        WeakSet.prototype[Symbol.toStringTag] → 'WeakSet'
    Iterators
        %MapIteratorPrototype%[Symbol.toStringTag] → 'Map Iterator'
        %SetIteratorPrototype%[Symbol.toStringTag] → 'Set Iterator'
        %StringIteratorPrototype%[Symbol.toStringTag] → 'String Iterator'
    Miscellaneous
        Symbol.prototype[Symbol.toStringTag] → 'Symbol'
        Generator.prototype[Symbol.toStringTag] → 'Generator'
        GeneratorFunction.prototype[Symbol.toStringTag] → 'GeneratorFunction'

All of the built-in properties whose keys are Symbol.toStringTag have the following property descriptor:
```js
{
    writable: false,
    enumerable: false,
    configurable: true,
}
```
As mentioned earlier, you can’t use assignment to override those properties, because they are read-only.
### 14.8.4 Property key Symbol.unscopables (Object)

Symbol.unscopables lets an object hide some properties from the with statement.

The reason for doing so is that it allows TC39 to add new methods to Array.prototype without breaking old code. Note that current code rarely uses with, which is forbidden in strict mode and therefore ES6 modules (which are implicitly in strict mode).

Why would adding methods to Array.prototype break code that uses with (such as the widely deployed Ext JS 4.2.1)? Take a look at the following code. The existence of a property Array.prototype.values breaks foo(), if you call it with an Array:
```js
function foo(values) {
    with (values) {
        console.log(values.length); // abc (\*)
    }
}
Array.prototype.values = { length: 'abc' };
foo([]);
```
Inside the with statement, all properties of values become local variables, shadowing even values itself. Therefore, if values has a property values then the statement in line * logs values.values.length and not values.length.

Symbol.unscopables is used only once in the standard library:

    Array.prototype[Symbol.unscopables]
        Holds an object with the following properties (which are therefore hidden from the with statement): copyWithin, entries, fill, find, findIndex, keys, values

## 14.9 FAQ: object literals
### 14.9.1 Can I use super in object literals?

Yes you can! Details are explained in the chapter on classes.







14. 클래스를 포함한 새로운 OOP기능

클래스는 es6에 도입된 주요한 새 OOP특징이다. 하지만 오브젝트리터럴과 Objectㅇ 메소드에도 새로운 기능이 포함되어있다.
이 쳅터에서는 이들을 다룬다.

14.1 개요

14.1.1 새 오브젝트리터럴

메소드선언:
```javascript
const obj = {
    myMethod(x, y) {
        ···
    }
};
```
속성값의 간단한 표현:

```javascript
const first = 'Jane';
const last = 'Doe';

const obj = { first, last };
// Same as:
const obj = { first: first, last: last };
```
계산된 속성키:
```javascript
const propKey = 'foo';
const obj = {
    [propKey]: true,
    ['b'+'ar']: 123
};
```
계산된 속성키는 메소드선언에도 사용됨:
```javascript
const obj = {
    ['h'+'ello']() {
        return 'hi';
    }
};
console.log(obj.hello()); // hi
```
계산된 속성키의 주된 활용처는 속성키로 심볼사용을 쉽게 만들어주는 것이다.

14.1.2 Object의 새로운 메소드
Object의 새 메소드중 가장 중요한 것ㅇㄴ assign()이다. 전통적으로 이 기능은 자바스크립트세계에서 extend()라 불렸다.
고전적인 동작과는 대조적으로 Object.assign()은 오직 자신의(상속되지않은)속성만 고려한다.
```javascript
const obj = { foo: 123 };
Object.assign(obj, { bar: true });
console.log(JSON.stringify(obj));
    // {"foo":123,"bar":true}
```
14.2 오브젝트리터럴의 새로운 기능
14.2.1 메소드 정의
es5에서 메소드는 함수값을 갖는 속성이다:
```javascript
var obj = {
    myMethod: function (x, y) {
        ···
    }
};
```
es6에서는 메소드가 여전히 함수를 값으로 갖고 있는 속성이긴 하지만 훨씬 간단한 방법으로 정의할 수 있다:

```javascript
const obj = {
    myMethod(x, y) {
        ···
    }
};
```
es5에서 사용되던 Getter와 Setter도 잘 작동한다.
```javascript
const obj = {
    get foo() {
        console.log('GET foo');
        return 123;
    },
    set bar(value) {
        console.log('SET bar to '+value);
        // return value is ignored
    }
};
```
obj를 써보자:
```javascript
> obj.foo
GET foo
123
> obj.bar = true
SET bar to true
true
```
제네레이터 함수를 값으로 하는 속성을 간단하게 정의하는 방법 또한 제공된다:
```javascript
const obj = {
    * myGeneratorMethod() {
        ···
    }
};
```
이 코드의 의미는 아래와 같다.
```javascript
const obj = {
    myGeneratorMethod: function* () {
        ···
    }
};
```
14.2.2 Property value shorthands
Property value shorthands let you abbreviate the definition of a property in an object literal: If the name of the variable that specifies the property value is also the property key then you can omit the key. This looks as follows.

const x = 4;
const y = 1;
const obj = { x, y };
The last line is equivalent to:

const obj = { x: x, y: y };
Property value shorthands work well together with destructuring:

const obj = { x: 4, y: 1 };
const {x,y} = obj;
console.log(x); // 4
console.log(y); // 1
One use case for property value shorthands are multiple return values (which are explained in the chapter on destructuring).

14.2.3 Computed property keys
Remember that there are two ways of specifying a key when you set a property.

Via a fixed name: obj.foo = true;
Via an expression: obj['b'+'ar'] = 123;
In object literals, you only have option #1 in ECMAScript 5. ECMAScript 6 additionally provides option #2:

const propKey = 'foo';
const obj = {
    [propKey]: true,
    ['b'+'ar']: 123
};
This new syntax can also be used for method definitions:

const obj = {
    ['h'+'ello']() {
        return 'hi';
    }
};
console.log(obj.hello()); // hi
The main use case for computed property keys are symbols: you can define a public symbol and use it as a special property key that is always unique. One prominent example is the symbol stored in Symbol.iterator. If an object has a method with that key, it becomes iterable: The method must return an iterator, which is used by constructs such as the for-of loop to iterate over the object. The following code demonstrates how that works.

const obj = {
    * [Symbol.iterator]() { // (A)
        yield 'hello';
        yield 'world';
    }
};
for (const x of obj) {
    console.log(x);
}
// Output:
// hello
// world
Line A starts a generator method definition with a computed key (the symbol stored in Symbol.iterator).

14.3 New methods of Object
14.3.1 Object.assign(target, source_1, source_2, ···)
This method merges the sources into the target: It modifies target, first copies all enumerable own (non-inherited) properties of source_1 into it, then all own properties of source_2, etc. At the end, it returns the target.

const obj = { foo: 123 };
Object.assign(obj, { bar: true });
console.log(JSON.stringify(obj));
    // {"foo":123,"bar":true}
Let’s look more closely at how Object.assign() works:

Both kinds of property keys: Object.assign() is aware of both strings and symbols as property keys.
Only enumerable own properties: Object.assign() ignores inherited properties and properties that are not enumerable.
Reading a value from a source: normal “get” operation (const value = source[propKey]). That means that if the source has a getter whose key is propKey then it will be invoked. All properties created by Object.assign() are data properties, it won’t transfer getters to the target.
Writing a value to the target: normal “set” operation (target[propKey] = value). That means that if the target has a setter whose key is propKey then it will be invoked with value.
This is how you’d copy all properties (not just enumerable ones), while correctly transferring getters and setters, without invoking setters on the target:

function copyAllProperties(target, ...sources) {
    for (const source of sources) {
        for (const key of Reflect.ownKeys(source)) {
            const desc = Object.getOwnPropertyDescriptor(source, key);
            Object.defineProperty(target, key, desc);
        }
    }
    return target;
}
14.3.1.1 Caveat: Object.assign() doesn’t work well for moving methods
On one hand, you can’t move a method that uses super: Such a method has an internal property [[HomeObject]] that ties it to the object it was created in. If you move it via Object.assign(), it will continue to refer to the super-properties of the original object. Details are explained in a section in the chapter on classes.

On the other hand, enumerability is wrong if you move methods created by an object literal into the prototype of a class. The former methods are all enumerable (otherwise Object.assign() wouldn’t see them, anyway), but the prototype only has non-enumerable methods by default.

14.3.1.2 Use cases for Object.assign()
Let’s look at a few use cases.

14.3.1.2.1 Adding properties to this
You can use Object.assign() to add properties to this in a constructor:

class Point {
    constructor(x, y) {
        Object.assign(this, {x, y});
    }
}
14.3.1.2.2 Providing default values for object properties
Object.assign() is also useful for filling in defaults for missing properties. In the following example, we have an object DEFAULTS with default values for properties and an object options with data.

const DEFAULTS = {
    logLevel: 0,
    outputFormat: 'html'
};
function processContent(options) {
    options = Object.assign({}, DEFAULTS, options); // (A)
    ···
}
In line A, we created a fresh object, copied the defaults into it and then copied options into it, overriding the defaults. Object.assign() returns the result of these operations, which we assign to options.

14.3.1.2.3 Adding methods to objects
Another use case is adding methods to objects:

Object.assign(SomeClass.prototype, {
    someMethod(arg1, arg2) {
        ···
    },
    anotherMethod() {
        ···
    }
});
You could also manually assign functions, but then you don’t have the nice method definition syntax and need to mention SomeClass.prototype each time:

SomeClass.prototype.someMethod = function (arg1, arg2) {
    ···
};
SomeClass.prototype.anotherMethod = function () {
    ···
};
14.3.1.2.4 Cloning objects
One last use case for Object.assign() is a quick way of cloning objects:

function clone(orig) {
    return Object.assign({}, orig);
}
This way of cloning is also somewhat dirty, because it doesn’t preserve the property attributes of orig. If that is what you need, you have to use property descriptors.

If you want the clone to have the same prototype as the original, you can use Object.getPrototypeOf() and Object.create():

function clone(orig) {
    const origProto = Object.getPrototypeOf(orig);
    return Object.assign(Object.create(origProto), orig);
}
14.3.2 Object.getOwnPropertySymbols(obj)
Object.getOwnPropertySymbols(obj) retrieves all own (non-inherited) symbol-valued property keys of obj. It complements Object.getOwnPropertyNames(), which retrieves all string-valued own property keys. Consult a later section for more details on iterating over property keys.

14.3.3 Object.is(value1, value2)
The strict equals operator (===) treats two values differently than one might expect.

First, NaN is not equal to itself.

> NaN === NaN
false
That is unfortunate, because it often prevents us from detecting NaN:

> [0,NaN,2].indexOf(NaN)
-1
Second, JavaScript has two zeros, but strict equals treats them as if they were the same value:

> -0 === +0
true
Doing this is normally a good thing.

Object.is() provides a way of comparing values that is a bit more precise than ===. It works as follows:

> Object.is(NaN, NaN)
true
> Object.is(-0, +0)
false
Everything else is compared as with ===.

14.3.3.1 Using Object.is() to find Array elements
If we combine Object.is() with the new ES6 Array method findIndex(), we can find NaN in Arrays:

function myIndexOf(arr, elem) {
    return arr.findIndex(x => Object.is(x, elem));
}

myIndexOf([0,NaN,2], NaN); // 1
In contrast, indexOf() does not handle NaN well:

> [0,NaN,2].indexOf(NaN)
-1
14.3.4 Object.setPrototypeOf(obj, proto)
This method sets the prototype of obj to proto. The non-standard way of doing so in ECMAScript 5, that is supported by many engines, is via assigning to the special property __proto__. The recommended way of setting the prototype remains the same as in ECMAScript 5: during the creation of an object, via Object.create(). That will always be faster than first creating an object and then setting its prototype. Obviously, it doesn’t work if you want to change the prototype of an existing object.

14.4 Iterating over property keys in ES6
In ECMAScript 6, the key of a property can be either a string or a symbol. There are now five tool methods that retrieve the property keys of an object obj:

Object.keys(obj) : Array<string>
retrieves all string keys of all enumerable own (non-inherited) properties.
Object.getOwnPropertyNames(obj) : Array<string>
retrieves all string keys of all own properties.
Object.getOwnPropertySymbols(obj) : Array<symbol>
retrieves all symbol keys of all own properties.
Reflect.ownKeys(obj) : Array<string|symbol>
retrieves all keys of all own properties.
Reflect.enumerate(obj) : Iterator
retrieves all string keys of all enumerable properties.
14.4.1 Iteration order of property keys
The following operations in ECMAScript 6 traverse the own keys of properties:

Object.keys()
Object.getOwnPropertyNames()
Reflect.ownKeys()
Own property keys are traversed in the following order:

First, the string keys that are integer indices (what these are is explained in the next section), in ascending numeric order.
Then, all other string keys, in the order in which they were added to the object.
Lastly, all symbol keys, in the order in which they were added to the object.
Many engines treat integer indices specially, even though they are still strings (at least as far as the ES6 spec is concerned). Therefore, it makes sense to treat them as a separate category of keys.

14.4.1.1 Integer indices
Roughly, an integer index is a string that, if converted to a 53-bit non-negative integer and back is the same value. Therefore:

'10' and '2' are integer indices.
'02' is not an integer index. Coverting it to an integer and back results in the different string '2'.
'3.141' is not an integer index, because 3.141 is not an integer.
In ES6, instances of String and Typed Arrays have integer indices. The indices of normal Arrays are a subset of integer indices: they have a smaller range of 32 bits. For more information on Array indices, consult “Array Indices in Detail” in “Speaking JavaScript”.

Integer indices have a 53-bit range, because thats the largest range of integers that JavaScript can handle. For details, see Sect. “Safe integers”.

14.4.1.2 Example
The following code demonstrates the order in which the own keys of an object are iterated over:

const obj = {
    [Symbol('first')]: true,
    '02': true,
    '10': true,
    '01': true,
    '2': true,
    [Symbol('second')]: true,
};
Reflect.ownKeys(obj);
    // [ '2', '10', '02', '01',
    //   Symbol('first'), Symbol('second') ]
Explanation:

'2' and '10' are integer indices, come first and are sorted numerically (not in the order in which they were added).
'02' and '01' are normal string keys, come next and appear in the order in which they were added to obj.
Symbol('first') and Symbol('second') are symbols and come last, in the order in which they were added to obj.
14.4.1.3 Why does the spec standardize in which order property keys are returned?
Answer by Tab Atkins Jr.:

Because, for objects at least, all implementations used approximately the same order (matching the current spec), and lots of code was inadvertently written that depended on that ordering, and would break if you enumerated it in a different order. Since browsers have to implement this particular ordering to be web-compatible, it was specced as a requirement.

There was some discussion about breaking from this in Maps/Sets, but doing so would require us to specify an order that is impossible for code to depend on; in other words, we’d have to mandate that the ordering be random, not just unspecified. This was deemed too much effort, and creation-order is reasonable valuable (see OrderedDict in Python, for example), so it was decided to have Maps and Sets match Objects.

14.4.1.4 The order of properties in the spec
Two parts of the spec are relevant:

The section on Array exotic objects has a note on what Array indices are.
The section on the internal method [[OwnPropertyKeys]] defines the order in which properties are returned.
14.5 Assigning versus defining properties
This section provides background knowledge that is needed in later sections.

There are two similar ways of adding a property prop to an object obj:

Assigning: obj.prop = 123
Defining: Object.defineProperty(obj, 'prop', 123)
There are three cases in which assigning does not create an own property prop, even if it doesn’t exist, yet:

A read-only property prop exists in the prototype chain. Then the assignment causes a TypeError in strict mode.
A setter for prop exists in the prototype chain. Then that setter is called.
A getter for prop without a setter exists in the prototype chain. Then a TypeError is thrown in strict mode. This case is similar to the first one.
None of these cases prevent Object.defineProperty() from creating an own property. The next section looks at case #3 in more detail.

14.5.1 Overriding inherited read-only properties
If an object obj inherits a property prop that is read-only then you can’t assign to that property:

const proto = Object.defineProperty({}, 'prop', {
    writable: false,
    configurable: true,
    value: 123,
});
const obj = Object.create(proto);
obj.prop = 456;
    // TypeError: Cannot assign to read-only property
This is similar to how an inherited property works that has a getter, but no setter. It is in line with viewing assignment as changing the value of an inherited property. It does so non-destructively: the original is not modified, but overridden by a newly created own property. Therefore, an inherited read-only property and an inherited setter-less property both prevent changes via assignment. You can, however, force the creation of an own property by defining a property:

const proto = Object.defineProperty({}, 'prop', {
    writable: false,
    configurable: true,
    value: 123,
});
const obj = Object.create(proto);
Object.defineProperty(obj, 'prop', {value: 456});
console.log(obj.prop); // 456
14.6 __proto__ in ECMAScript 6
The property __proto__ (pronounced “dunder proto”) has existed for a while in most JavaScript engines. This section explains how it worked prior to ECMAScript 6 and what changes with ECMAScript 6.

For this section, it helps if you know what prototype chains are. Consult Sect. “Layer 2: The Prototype Relationship Between Objects” in “Speaking JavaScript”, if necessary.

14.6.1 __proto__ prior to ECMAScript 6
14.6.1.1 Prototypes
Each object in JavaScript starts a chain of one or more objects, a so-called prototype chain. Each object points to its successor, its prototype via the internal property [[Prototype]] (which is null if there is no successor). That property is called internal, because it only exists in the language specification and cannot be directly accessed from JavaScript. In ECMAScript 5, the standard way of getting the prototype p of an object obj is:

var p = Object.getPrototypeOf(obj);
There is no standard way to change the prototype of an existing object, but you can create a new object obj that has the given prototype p:

var obj = Object.create(p);
14.6.1.2 __proto__
A long time ago, Firefox got the non-standard property __proto__. Other browsers eventually copied that feature, due to its popularity.

Prior to ECMAScript 6, __proto__ worked in obscure ways:

You could use it to get or set the prototype of any object:
  var obj = {};
  var p = {};

  console.log(obj.__proto__ === p); // false
  obj.__proto__ = p;
  console.log(obj.__proto__ === p); // true
However, it was never an actual property:
  > var obj = {};
  > '__proto__' in obj
  false
14.6.1.3 Subclassing Array via __proto__
The main reason why __proto__ became popular was because it enabled the only way to create a subclass MyArray of Array in ES5: Array instances were exotic objects that couldn’t be created by ordinary constructors. Therefore, the following trick was used:

function MyArray() {
    var instance = new Array(); // exotic object
    instance.__proto__ = MyArray.prototype;
    return instance;
}
MyArray.prototype = Object.create(Array.prototype);
MyArray.prototype.customMethod = function (···) { ··· };
Subclassing in ES6 works differently than in ES5 and supports subclassing builtins out of the box.

14.6.1.4 Why __proto__ is problematic in ES5
The main problem is that __proto__ mixes two levels: the object level (normal properties, holding data) and the meta level.

If you accidentally use __proto__ as a normal property (object level!), to store data, you get into trouble, because the two levels clash. The situation is compounded by the fact that you have to abuse objects as maps in ES5, because it has no built-in data structure for that purpose. Maps should be able to hold arbitrary keys, but you can’t use the key '__proto__' with objects-as-maps.

In theory, one could fix the problem by using a symbol instead of the special name __proto__, but keeping meta-operations completely separate (as done via Object.getPrototypeOf()) is the best approach.

14.6.2 The two kinds of __proto__ in ECMAScript 6
Because __proto__ was so widely supported, it was decided that its behavior should be standardized for ECMAScript 6. However, due to its problematic nature, it was added as a deprecated feature. These features reside in Annex B in the ECMAScript specification, which is described as follows:

The ECMAScript language syntax and semantics defined in this annex are required when the ECMAScript host is a web browser. The content of this annex is normative but optional if the ECMAScript host is not a web browser.

JavaScript has several undesirable features that are required by a significant amount of code on the web. Therefore, web browsers must implement them, but other JavaScript engines don’t have to.

In order to explain the magic behind __proto__, two mechanisms were introduced in ES6:

A getter and a setter implemented via Object.prototype.__proto__.
In an object literal, you can consider the property key '__proto__' a special operator for specifying the prototype of the created objects.
14.6.2.1 Object.prototype.__proto__
ECMAScript 6 enables getting and setting the property __proto__ via a getter and a setter stored in Object.prototype. If you were to implement them manually, this is roughly what it would look like:

Object.defineProperty(Object.prototype, '__proto__', {
    get() {
        const _thisObj = Object(this);
        return Object.getPrototypeOf(_thisObj);
    },
    set(proto) {
        if (this === undefined || this === null) {
            throw new TypeError();
        }
        if (!isObject(this)) {
            return undefined;
        }
        if (!isObject(proto)) {
            return undefined;
        }
        const status = Reflect.setPrototypeOf(this, proto);
        if (! status) {
            throw new TypeError();
        }
    },
});
function isObject(value) {
    return Object(value) === value;
}
The getter and the setter for __proto__ in the ES6 spec:

get Object.prototype.__proto__
set Object.prototype.__proto__
14.6.2.2 The property key __proto__ as an operator in an object literal
If __proto__ appears as an unquoted or quoted property key in an object literal, the prototype of the object created by that literal is set to the property value:

> Object.getPrototypeOf({ __proto__: null })
null
> Object.getPrototypeOf({ '__proto__': null })
null
Using the string value '__proto__' as a computed property key does not change the prototype, it creates an own property:

> const obj = { ['__proto__']: null };
> Object.getPrototypeOf(obj) === Object.prototype
true
> Object.keys(obj)
[ '__proto__' ]
The special property key '__proto__' in the ES6 spec:

__proto__ Property Names in Object Initializers
14.6.3 Avoiding the magic of __proto__
14.6.3.1 Defining (not assigning) __proto__
In ECMAScript 6, if you define the own property __proto__, no special functionality is triggered and the getter/setter Object.prototype.__proto__ is overridden:

const obj = {};
Object.defineProperty(obj, '__proto__', { value: 123 })

Object.keys(obj); // [ '__proto__' ]
console.log(obj.__proto__); // 123
14.6.3.2 Objects that don’t have Object.prototype as a prototype
The __proto__ getter/setter is provided via Object.prototype. Therefore, an object without Object.prototype in its prototype chain doesn’t have the getter/setter, either. In the following code, dict is an example of such an object – it does not have a prototype. As a result, __proto__ now works like any other property:

> const dict = Object.create(null);
> '__proto__' in dict
false
> dict.__proto__ = 'abc';
> dict.__proto__
'abc'
14.6.3.3 __proto__ and dict objects
If you want to use an object as a dictionary then it is best if it doesn’t have a prototype. That’s why prototype-less objects are also called dict objects. In ES6, you don’t even have to escape the property key '__proto__' for dict objects, because it doesn’t trigger any special functionality.

__proto__ as an operator in an object literal lets you create dict objects more concisely:

const dictObj = {
    __proto__: null,
    yes: true,
    no: false,
};
Note that in ES6, you should normally prefer the built-in data structure Map to dict objects, especially if keys are not fixed.

14.6.3.4 __proto__ and JSON
Prior to ES6, the following could happen in a JavaScript engine:

> JSON.parse('{"__proto__": []}') instanceof Array
true
With __proto__ being a getter/setter in ES6, JSON.parse() works fine, because it defines properties, it doesn’t assign them (if implemented properly, an older version of V8 did assign).

JSON.stringify() isn’t affected by __proto__, either, because it only considers own properties. Objects that have an own property whose name is __proto__ work fine:

> JSON.stringify({['__proto__']: true})
'{"__proto__":true}'
14.6.4 Detecting support for ES6-style __proto__
Support for ES6-style __proto__ varies from engine to engine. Consult kangax’ ECMAScript 6 compatibility table for information on the status quo:

Object.prototype.__proto__
__proto__ in object literals
The following two sections describe how you can programmatically detect whether an engine supports either of the two kinds of __proto__.

14.6.4.1 Feature: __proto__ as getter/setter
A simple check for the getter/setter:

var supported = {}.hasOwnProperty.call(Object.prototype, '__proto__');
A more sophisticated check:

var desc = Object.getOwnPropertyDescriptor(Object.prototype, '__proto__');
var supported = (
    typeof desc.get === 'function' && typeof desc.set === 'function'
);
14.6.4.2 Feature: __proto__ as an operator in an object literal
You can use the following check:

var supported = Object.getPrototypeOf({__proto__: null}) === null;
14.6.5 __proto__ is pronounced “dunder proto”
Bracketing names with double underscores is a common practice in Python to avoid name clashes between meta-data (such as __proto__) and data (user-defined properties). That practice will never become common in JavaScript, because it now has symbols for this purpose. However, we can look to the Python community for ideas on how to pronounce double underscores.

The following pronunciation has been suggested by Ned Batchelder:

An awkward thing about programming in Python: there are lots of double underscores. For example, the standard method names beneath the syntactic sugar have names like __getattr__, constructors are __init__, built-in operators can be overloaded with __add__, and so on. […]

My problem with the double underscore is that it’s hard to say. How do you pronounce __init__? “underscore underscore init underscore underscore”? “under under init under under”? Just plain “init” seems to leave out something important.

I have a solution: double underscore should be pronounced “dunder”. So __init__ is “dunder init dunder”, or just “dunder init”.

Thus, __proto__ is pronounced “dunder proto”. The chances for this pronunciation catching on are good, JavaScript creator Brendan Eich uses it.

14.6.6 Recommendations for __proto__
It is nice how well ES6 turns __proto__ from something obscure into something that is easy to understand.

However, I still recommend not to use it. It is effectively a deprecated feature and not part of the core standard. You can’t rely on it being there for code that must run on all engines.

More recommendations:

Use Object.getPrototypeOf() to get the prototype of an object.
Prefer Object.create() to create a new object with a given prototype. Avoid Object.setPrototypeOf(), which hampers performance on many engines.
I actually like __proto__ as an operator in an object literal. It is useful for demonstrating prototypal inheritance and for creating dict objects. However, the previously mentioned caveats do apply.
14.7 Enumerability in ECMAScript 6
Enumerability is an attribute of object properties. This section explains how it works in ECMAScript 6. Let’s first explore what attributes are.

14.7.1 Property attributes
Each object has zero or more properties. Each property has a key and three or more attributes, named slots that store the data of the property (in other words, a property is itself much like a JavaScript object or like a record with fields in a database).

ECMAScript 6 supports the following attributes (as does ES5):

All properties have the attributes:
enumerable: Setting this attribute to false hides the property from some operations.
configurable: Setting this attribute to false prevents several changes to a property (attributes except value can’t be change, property can’t be deleted, etc.).
Normal properties (data properties, methods) have the attributes:
value: holds the value of the property.
writable: controls whether the property’s value can be changed.
Accessors (getters/setters) have the attributes:
get: holds the getter (a function).
set: holds the setter (a function).
You can retrieve the attributes of a property via Object.getOwnPropertyDescriptor(), which returns the attributes as a JavaScript object:

> const obj = { foo: 123 };
> Object.getOwnPropertyDescriptor(obj, 'foo')
{ value: 123,
  writable: true,
  enumerable: true,
  configurable: true }
This section explains how the attribute enumerable works in ES6. All other attributes and how to change attributes is explained in Sect. “Property Attributes and Property Descriptors” in “Speaking JavaScript”.

14.7.2 Constructs affected by enumerability
ECMAScript 5:

for-in loop: iterates over the string keys of own and inherited enumerable properties.
Object.keys(): returns the string keys of enumerable own properties.
JSON.stringify(): only stringifies enumerable own properties with string keys.
ECMAScript 6:

Object.assign(): only copies enumerable own properties (both string keys and symbol keys are considered).
Reflect.enumerate(): returns all property names that for-in iterates over.
for-in and Reflect.enumerate() are the only built-in operations where enumerability matters for inherited properties. All other operations only work with own properties.

14.7.3 Use cases for enumerability
Unfortunately, enumerability is quite an idiosyncratic feature. This section presents several use cases for it and argues that, apart from protecting legacy code from breaking, its usefulness is limited.

14.7.3.1 Use case: Hiding properties from the for-in loop
The for-in loop iterates over all enumerable properties of an object, own and inherited ones. Therefore, the attribute enumerable is used to hide properties that should not be iterated over. That was the reason for introducing enumerability in ECMAScript 1.

14.7.3.1.1 Non-enumerability in the language
Non-enumerable properties occur in the following locations in the language:

All prototype properties of built-in classes are non-enumerable:
  > const desc = Object.getOwnPropertyDescriptor.bind(Object);
  > desc(Object.prototype, 'toString').enumerable
  false
All prototype properties of classes are non-enumerable:
  > desc(class {foo() {}}.prototype, 'foo').enumerable
  false
In Arrays, length is not enumerable, which means that for-in only iterates over indices. (However, that can easily change if you add a property via assignment, which is makes it enumerable.)
  > desc([], 'length').enumerable
  false
  > desc(['a'], '0').enumerable
  true
The main reason for making all of these properties non-enumerable is to hide them (especially the inherited ones) from legacy code that uses the for-in loop or $.extend() (and similar operations that copy both inherited and own properties; see next section). Both operations should be avoided in ES6. Hiding them ensures that the legacy code doesn’t break.

14.7.3.2 Use case: Marking properties as not to be copied
14.7.3.2.1 Historical precedents
When it comes to copying properties, there are two important historical precedents that take enumerability into consideration:

Prototype’s Object.extend(destination, source)
  const obj1 = Object.create({ foo: 123 });
  Object.extend({}, obj1); // { foo: 123 }

  const obj2 = Object.defineProperty({}, 'foo', {
      value: 123,
      enumerable: false
  });
  Object.extend({}, obj2) // {}
jQuery’s $.extend(target, source1, source2, ···) copies all enumerable own and inherited properties of source1 etc. into own properties of target.
  const obj1 = Object.create({ foo: 123 });
  $.extend({}, obj1); // { foo: 123 }

  const obj2 = Object.defineProperty({}, 'foo', {
      value: 123,
      enumerable: false
  });
  $.extend({}, obj2) // {}
Problems with this way of copying properties:

Turning inherited source properties into own target properties is rarely what you want. That’s why enumerability is used to hide inherited properties.
Which properties to copy and which not often depends on the task at hand, it rarely makes sense to have a single flag for everything. A better choice is to provide the copying operation with a predicate (a callback that returns a boolean) that tells it when to consider a property.
The only instance property that is non-enumerable in the standard library is property length of Arrays. However, that property only needs to be hidden due to it magically updating itself via other properties. You can’t create that kind of magic property for your own objects (short of using a Proxy).

14.7.3.2.2 ES6: Object.assign()
In ES6, Object.assign(target, source_1, source_2, ···) can be used to merge the sources into the target. All own enumerable properties of the sources are considered (that is, keys can be either strings or symbols). Object.assign() uses a “get” operation to read a value from a source and a “set” operation to write a value to the target.

With regard to enumerability, Object.assign() continues the tradition of Object.extend() and $.extend(). Quoting Yehuda Katz:

Object.assign would pave the cowpath of all of the extend() APIs already in circulation. We thought the precedent of not copying enumerable methods in those cases was enough reason for Object.assign to have this behavior.

In other words: Object.assign() was created with an upgrade path from $.extend() (and similar) in mind. Its approach is cleaner than $.extend’s, because it ignores inherited properties.

14.7.3.3 Marking properties as private
If you make a property non-enumerable, it can’t by seen by Object.keys() and the for-in loop, anymore. With regard to those mechanisms, the property is private.

However, there are several problems with this approach:

When copying an object, you normally want to copy private properties. That clashes making properties non-enumerable that shouldn’t be copied (see previous section).
The property isn’t really private. Getting, setting and several other mechanisms make no distinction between enumerable and non-enumerable properties.
When working with code either as source or interactively, you can’t immediately see whether a property is enumerable or not. A naming convention (such as prefixing property names with an underscore) is easier to discover.
You can’t use enumerability to distinguish between public and private methods, because methods in prototypes are non-enumerable by default.
14.7.3.4 Hiding own properties from JSON.stringify()
JSON.stringify() does not include properties in its output that are non-enumerable. You can therefore use enumerability to determine which own properties should be exported to JSON. This use case is similar to marking properties as private, the previous use case. But it is also different, because this is more about exporting and slightly different considerations apply. For example: Can an object be completely reconstructed from JSON?

An alternative for specifying how an object should be converted to JSON is to use toJSON():

const obj = {
    foo: 123,
    toJSON() {
        return { bar: 456 };
    },
};
JSON.stringify(obj); // '{"bar":456}'
I find toJSON() cleaner than enumerability for the current use case. It also gives you more control, because you can export properties that don’t exist on the object.

14.7.4 Naming inconsistencies
In general, a shorter name means that only enumerable properties are considered:

Object.keys() ignores non-enumerable properties
Object.getOwnPropertyNames() lists all property names
However, Reflect.ownKeys() deviates from that rule, it ignores enumerability and returns the keys of all properties. Additionally, starting with ES6, the following distinction is made:

Property keys are either strings or symbols.
Property names are only strings.
Therefore, a better name for Object.keys() would now be Object.names().

14.7.5 Looking ahead
It seems to me that enumerability is only suited for hiding properties from the for-in loop and $.extend() (and similar operations). Both are legacy features, you should avoid them in new code. As for the other use cases:

I don’t think there is a need for a general flag specifying whether or not to copy a property.
Non-enumerability does not work well as a way to keep properties private.
The toJSON() method is more powerful and explicit than enumerability when it comes to controlling how to convert an object to JSON.
I’m not sure what the best strategy is for enumerability going forward. If, with ES6, we had started to pretend that it didn’t exist (except for making prototype properties non-enumerable so that old code doesn’t break), we might eventually have been able to deprecate enumerability. However, Object.assign() considering enumerability runs counter that strategy (but it does so for a valid reason, backward compatibility).

In my own ES6 code, I’m not using enumerability, except (implicitly) for classes whose prototype methods are non-enumerable.

Lastly, when using an interactive command line, I occasionally miss an operation that returns all property keys of an object, not just the own ones (Reflect.ownKeys) or not just string-valued enumerable ones (Reflect.enumerate). Such an operation would provide a nice overview of the contents of an object.

14.8 Customizing basic language operations via well-known symbols
This section explains how you can customize basic language operations by using the following well-known symbols as property keys:

Symbol.hasInstance (method)
Lets an object C customize the behavior of x instanceof C.
Symbol.toPrimitive (method)
Lets an object customize how it is converted to a primitive value. This is the first step whenever something is coerced to a primitive type (via operators etc.).
Symbol.toStringTag (string)
Called by Object.prototype.toString() to compute the default string description of an object obj: ‘[object ‘+obj[Symbol.toStringTag]+’]’.
Symbol.unscopables (Object)
Lets an object hide some properties from the with statement.
14.8.1 Property key Symbol.hasInstance (method)
An object C can customize the behavior of the instanceof operator via a method with the key Symbol.hasInstance that has the following signature:

[Symbol.hasInstance](potentialInstance : any)
x instanceof C works as follows in ES6:

If C is not an object, throw a TypeError.
If the method exists, call C[Symbol.hasInstance](x), coerce the result to boolean and return it.
Otherwise, compute and return the result according to the traditional algorithm (C must be callable, C.prototype in the prototype chain of x, etc.).
14.8.1.1 Uses in the standard library
The only method in the standard library that has this key is:

Function.prototype[Symbol.hasInstance]()
This is the implementation of instanceof that all functions (including classes) use by default. Quoting the spec:

This property is non-writable and non-configurable to prevent tampering that could be used to globally expose the target function of a bound function.

The tampering is possible because the traditional instanceof algorithm, OrdinaryHasInstance(), applies instanceof to the target function if it encounters a bound function.

Given that this property is read-only, you can’t use assignment to override it, as mentioned earlier.

14.8.1.2 Example: checking whether a value is an object
As an example, let’s implement an object ReferenceType whose “instances” are all objects, not just objects that are instances of Object (and therefore have Object.prototype in their prototype chains).

const ReferenceType = {
    [Symbol.hasInstance](value) {
        return (value !== null
            && (typeof value === 'object'
                || typeof value === 'function'));
    }
};
const obj1 = {};
console.log(obj1 instanceof Object); // true
console.log(obj1 instanceof ReferenceType); // true

const obj2 = Object.create(null);
console.log(obj2 instanceof Object); // false
console.log(obj2 instanceof ReferenceType); // true
14.8.2 Property key Symbol.toPrimitive (method)
Symbol.toPrimitive lets an object customize how it is coerced (converted automatically) to a primitive value.

Many JavaScript operations coerce values to the types that they need.

The multiplication operator (*) coerces its operands to numbers.
new Date(year, month, date) coerces its parameters to numbers.
parseInt(string , radix) coerces its first parameter to a string.
The following are the most common types that values are coerced to:

Boolean: Coercion returns true for truthy values, false for falsy values. Objects are always truthy (even new Boolean(false)).
Number: Coercion converts objects to primitives first. Primitives are then converted to numbers (null → 0, true → 1, '123' → 123, etc.).
String: Coercion converts objects to primitives first. Primitives are then converted to strings (null → 'null', true → 'true', 123 → '123', etc.).
Object: The coercion wraps primitive values (booleans b via new Boolean(b), numbers n via new Number(n), etc.).
Thus, for numbers and strings, the first step is to ensure that a value is any kind of primitive. That is handled by the spec-internal operation ToPrimitive(), which has three modes:

Number: the caller needs a number.
String: the caller needs a string.
Default: the caller needs either a number or a string.
The default mode is only used by:

Equality operator (==)
Addition operator (+)
new Date(value) (exactly one parameter!)
If the value is a primitive then ToPrimitive() is already done. Otherwise, the value is an object obj, which is converted to a primitive as follows:

Number mode: Return the result of obj.valueOf() if it is primitive. Otherwise, return the result of obj.toString() if it is primitive. Otherwise, throw a TypeError.
String mode: works like Number mode, but toString() is called first, valueOf() second.
Default mode: works exactly like Number mode.
This normal algorithm can be overridden by giving an object a method with the following signature:

[Symbol.toPrimitive](hint : 'default' | 'string' | 'number')
In the standard library, there are two such methods:

Symbol.prototype[Symbol.toPrimitive](hint) prevents toString() from being called (which throws an exception).
Date.prototype[Symbol.toPrimitive](hint) This method implements behavior that deviates from the default algorithm. Quoting the specification: “Date objects are unique among built-in ECMAScript object in that they treat 'default' as being equivalent to 'string'. All other built-in ECMAScript objects treat 'default' as being equivalent to 'number'.”
14.8.2.1 Example
The following code demonstrates how coercion affects the object obj.

const obj = {
    [Symbol.toPrimitive](hint) {
        switch (hint) {
            case 'number':
                return 123;
            case 'string':
                return 'str';
            case 'default':
                return 'default';
            default:
                throw new Error();
        }
    }
};
console.log(2 * obj); // 246
console.log(3 + obj); // '3default'
console.log(obj == 'default'); // true
console.log(String(obj)); // 'str'
14.8.3 Property key Symbol.toStringTag (string)
In ES5 and earlier, each object had the internal own property [[Class]] whose value hinted at its type. You could not access it directly, but its value was part of the string returned by Object.prototype.toString(), which is why that method was used for type checks, as an alternative to typeof.

In ES6, there is no internal property [[Class]], anymore, and using Object.prototype.toString() for type checks is discouraged. In order to ensure the backwards-compatibility of that method, the public property with the key Symbol.toStringTag was introduced. You could say that it replaces [[Class]].

Object.prototype.toString() now works as follows:

Convert this to an object obj.
Determine the toString tag tst of obj.
Return '[object ' + tst + ']'.
14.8.3.1 Default toString tags
The default values for various kinds of objects are shown in the following table.

Value	toString tag
undefined	'Undefined'
null	'Null'
An Array object	'Array'
A string object	'String'
arguments	'Arguments'
Something callable	'Function'
An error object	'Error'
A boolean object	'Boolean'
A number object	'Number'
A date object	'Date'
A regular expression object	'RegExp'
(Otherwise)	'Object'
Most of the checks in the left column are performed by looking at internal properties. For example, if an object has the internal property [[Call]], it is callable.

The following interaction demonstrates the default toString tags.

> Object.prototype.toString.call(null)
'[object Null]'
> Object.prototype.toString.call([])
'[object Array]'
> Object.prototype.toString.call({})
'[object Object]'
> Object.prototype.toString.call(Object.create(null))
'[object Object]'
14.8.3.2 Overriding the default toString tag
If an object has an (own or inherited) property whose key is Symbol.toStringTag then its value overrides the default toString tag. For example:

> ({}.toString())
'[object Object]'
> ({[Symbol.toStringTag]: 'Foo'}.toString())
'[object Foo]'
Instances of user-defined classes get the default toString tag (of objects):

class Foo { }
console.log(new Foo().toString()); // [object Object]
One option for overriding the default is via a getter:

class Bar {
    get [Symbol.toStringTag]() {
      return 'Bar';
    }
}
console.log(new Bar().toString()); // [object Bar]
In the JavaScript standard library, there are the following custom toString tags. Objects that have no global names are quoted with percent symbols (for example: %TypedArray%).

Module-like objects:
JSON[Symbol.toStringTag] → 'JSON'
Math[Symbol.toStringTag] → 'Math'
Actual module objects M: M[Symbol.toStringTag] → 'Module'
Built-in classes
ArrayBuffer.prototype[Symbol.toStringTag] → 'ArrayBuffer'
DataView.prototype[Symbol.toStringTag] → 'DataView'
Map.prototype[Symbol.toStringTag] → 'Map'
Promise.prototype[Symbol.toStringTag] → 'Promise'
Set.prototype[Symbol.toStringTag] → 'Set'
get %TypedArray%.prototype[Symbol.toStringTag] → 'Uint8Array' etc.
WeakMap.prototype[Symbol.toStringTag] → 'WeakMap'
WeakSet.prototype[Symbol.toStringTag] → 'WeakSet'
Iterators
%MapIteratorPrototype%[Symbol.toStringTag] → 'Map Iterator'
%SetIteratorPrototype%[Symbol.toStringTag] → 'Set Iterator'
%StringIteratorPrototype%[Symbol.toStringTag] → 'String Iterator'
Miscellaneous
Symbol.prototype[Symbol.toStringTag] → 'Symbol'
Generator.prototype[Symbol.toStringTag] → 'Generator'
GeneratorFunction.prototype[Symbol.toStringTag] → 'GeneratorFunction'
All of the built-in properties whose keys are Symbol.toStringTag have the following property descriptor:

{
    writable: false,
    enumerable: false,
    configurable: true,
}
As mentioned earlier, you can’t use assignment to override those properties, because they are read-only.

14.8.4 Property key Symbol.unscopables (Object)
Symbol.unscopables lets an object hide some properties from the with statement.

The reason for doing so is that it allows TC39 to add new methods to Array.prototype without breaking old code. Note that current code rarely uses with, which is forbidden in strict mode and therefore ES6 modules (which are implicitly in strict mode).

Why would adding methods to Array.prototype break code that uses with (such as the widely deployed Ext JS 4.2.1)? Take a look at the following code. The existence of a property Array.prototype.values breaks foo(), if you call it with an Array:

function foo(values) {
    with (values) {
        console.log(values.length); // abc (*)
    }
}
Array.prototype.values = { length: 'abc' };
foo([]);
Inside the with statement, all properties of values become local variables, shadowing even values itself. Therefore, if values has a property values then the statement in line * logs values.values.length and not values.length.

Symbol.unscopables is used only once in the standard library:

Array.prototype[Symbol.unscopables]
Holds an object with the following properties (which are therefore hidden from the with statement): copyWithin, entries, fill, find, findIndex, keys, values
14.9 FAQ: object literals
14.9.1 Can I use super in object literals?
Yes you can! Details are explained in the chapter on classes.
