# 22. Generators
# 22. 제너레이터

The following GitHub repository contains the example code: generator-examples

다음 깃헙 저장소에 예제 코드가 있습니다. : 제너레이터 예제들

## 22.1 Overview
## 22.1 개요

Generators, a new feature of ES6, are functions that can be paused and resumed (think cooperative multitasking or coroutines). That helps with many applications. Two important ones are:

ES6의 새 기능 중 하나인 제너레이터 멈춤과 재실행을 할 수 있는 함수들(협력 멀티태스킹, 코루틴) 입니다. 이것은 많은 어플리케이션에 도움을 줍니다. 다음은 두 가지 중요한 것들 입니다. 

* Implementing iterables
* Blocking on asynchronous function calls
* 이터러블 구현
* 비동기 함수 호출에서 블로킹

The following subsections give brief overviews of these applications.

다음 소섹션에서 이 애플리케이션들을 간략하게 보여드리겠습니다.

### 22.1.1 Implementing iterables via generators
### 22.1.1 제너레이터를 통한 이터러블 구현
The following function returns an iterable over the properties of an object, one [key, value] pair per property:

다음 함수는 객체의 프로퍼티를 순환하고 반환값이 하나의 프로퍼티당 [key, value]인 이터러블을 반환합니다.
```javascript
// The asterisk after `function` means that
// `objectEntries` is a generator
function* objectEntries(obj) {
    const propKeys = Reflect.ownKeys(obj);

    for (const propKey of propKeys) {
        // `yield` returns a value and then pauses
        // the generator. Later, execution continues
        // where it was previously paused.
        yield [propKey, obj[propKey]];
    }
}
```
objectEntries() is used like this:

objectEntries()는 아래 처럼 사용됩니다. :

```
const jane = { first: 'Jane', last: 'Doe' };
for (const [key,value] of objectEntries(jane)) {
    console.log(`${key}: ${value}`);
}
// Output:
// first: Jane
// last: Doe
```
How exactly objectEntries() works is explained in a dedicated section. Implementing the same functionality without generators is much more work.

objectEntries()의 정확히 어떻게 동작하는지 22.3.4에서 설명합니다. 제너레이터 없이 같은 기능을 구현하려면 더 많은 작업을 해야 합니다. 

### 22.1.2 Blocking on asynchronous function calls
### 22.1.2 비동이 함수 호출에서 블록킹

In the following code, I use the control flow library co to asynchronously retrieve two JSON files. Note how, in line A, execution blocks (waits) via yield until the result of Promise.all() is ready. That means that the code looks synchronous while performing asynchronous operations.

다음 코드에서 비동기적으로 두개의 JSON 파일을 검색하기 위하여 흐름 제어 라이브러리인 co를 사용 하였습니다. 줄A에서 Promise.all()이 Promise.all()의 결과가 나올때 까지 yield를 통해 어떻게 실행 블록의 대기하는지를 주목하셔야 합니다. 이것은 비동기 연산 수행이 동기 코드 처럼 보일 수 있다는것을 의미 합니다. 

```javascript
co(function* () {
    try {
        const [croftStr, bondStr] = yield Promise.all([  // (A)
            getFile('http://localhost:8000/croft.json'),
            getFile('http://localhost:8000/bond.json'),
        ]);
        const croftJson = JSON.parse(croftStr);
        const bondJson = JSON.parse(bondStr);

        console.log(croftJson);
        console.log(bondJson);
    } catch (e) {
        console.log('Failure to read: ' + e);
    }
});
```

The Promise-based function getFile(url) retrieves the file pointed to by url. Its implementation is shown later. I’ll also explain how co works. For more info on Promises, consult Chap. “Promises for asynchronous programming”.

프로미스 기반의 함수 getFile(url)은 url을 통해 파일을 가져옵니다. 이 규현은 나중에 보여드리겠습니다. 또한 co가 어떻게 작동되는지 설명하겠습니다. 프로미스에 대한 더 많은 정보는 비동기 프로그래밍에 대한 프로미스를 참고 하시면 됩니다.

## 22.2 What are generators?
## 22.2 제너레이터는 무엇인가?

Generators are functions that can be paused and resumed (think cooperative multitasking or coroutines), which enables a variety of applications.

제너레이터는 멈추고 재실행 되는 함수이고, 애플리케이션의 변화를 가능하게 한다.

As a first example, consider the following generator function whose name is genFunc:

첫번째 예제를 통해 이름이 getFunc인 제너레이터 함수를 생각해 봅시다.

```javascript
function* genFunc() {
    // (A)
    console.log('First');
    yield; // (B)
    console.log('Second'); // (C)
}
```

Two things distinguish genFunc from a normal function declaration:

일반적인 함수에서 선언을 통해 getFunc를 두가지로 나누어 보면

* It starts with the “keyword” function*.
* It can pause itself, via yield (line B).
 
* function*인 키워드로 시작된다.
* yield를 통해 자신을 멈출 수 있다. (줄 B). 

Calling genFunc does not execute its body. Instead, you get a so-called generator object, with which you can control the execution of the body:

genFunc을 호출할 때 그 바디를 실행하지 않습니다. 그 대신에 제너레이터 객체라 불리는 것을 반환 받고 이것을 통해 바디의 실행을 제어 할 수 있습니다.
```javascript
> const genObj = genFunc();
```
genFunc() is initially suspended before the body (line A). The method call genObj.next() continues execution until the next yield:

getFunc()는 바디 전 (줄 A)에서 일시 정지 됩니다. genObj.next() 메소드 호출을 통해 다음 yield까지 계속적으로 실행 할 수 있습니다.

```javascript
> genObj.next()
First
{ value: undefined, done: false }
```
As you can see in the last line, genObj.next() also returns an object. Let’s ignore that for now. It will matter later.

마지막 라인에서 볼수 있듯이, genObj.next는 또한 객체로 반환됩니다. 이것은 나중 문제이니 지금은 무시하셔도 됩니다.

genFunc is now paused in line B. If we call next() again, execution resumes and line C is executed:

getFunc는 현재 B출에서 멈춰 있습니다. 만약 next()한번더 호출 한다면, 실행을 재개되고 줄 C는 실행 되어 집니다.:

```javascript
> genObj.next()
Second
{ value: undefined, done: true }
```
Afterwards, the function is finished, execution has left the body and further calls of genObj.next() have no effect.

이 후, 이 함수는 완료 되고 실행은 바디에 남아 있고 더 이상의 genObj.next()를 호출 해도 아무런 효과가 생기지 않습니다.

### 22.2.1 Kinds of generators
### 22.2.1 제너레이터의 종류

There are four kinds of generators:

네 가지의 제너레이터 선언 방식이 있습니다. :

1. Generator function declarations:
1. 제너레이서 함수 선언식
```javascript
 function* genFunc() { ··· }
 const genObj = genFunc();
```
2. Generator function expressions:
2. 제너레이터 함수 표현식
```javascript
 const genFunc = function* () { ··· };
 const genObj = genFunc();
```
3. Generator method definitions in object literals:
3. 객체 리터럴에서 제너레이터 메소드 선언식
```javascript
 const obj = {
     * generatorMethod() {
         ···
     }
 };
 const genObj = obj.generatorMethod();
```
4. Generator method definitions in class definitions (class declarations or class expressions):
4. 클래스 선언에서 제너레이터 메소드 선언식 (클래스 선언식 또는 클래스 표현식)
```javascript
 class MyClass {
     * generatorMethod() {
         ···
     }
 }
 const myInst = new MyClass();
 const genObj = myInst.generatorMethod();
```
### 22.2.2 Roles played by generators
### 22.2.2 제너레이터의 역할
Generators can play three roles:

제너레이터는 3가지 역할을 할 수 있습니다.

* Iterators (data producers): Each yield can return a value via next(), which means that generators can produce sequences of values via loops and recursion. Due to generator objects implementing the interface Iterable (which is explained in the chapter on iteration), these sequences can be processed by any ECMAScript 6 construct that supports iterables. Two examples are: for-of loops and the spread operator (...).
* Observers (data consumers): yield can also receive a value from next() (via a parameter). That means that generators become data consumers that pause until a new value is pushed into them via next().
* Coroutines (data producers and consumers): Given that generators are pausable and can be both data producers and data consumers, not much work is needed to turn them into coroutines (cooperatively multitasked tasks).
The next sections provide deeper explanations of these roles.

* 이터레이터 (데이터 생산자): 각 yield는 next()를 통해 값을 반환하고 이것은 루프문이나 재귀를 통해 값들의 순서열을 만들수 있다는 것을 의미 합니다. 제너레이터는 이터러블(이터러블장에서 설명한다.) 인터페이스를 구현했기 때문에, 이 순서열은 이터러블이 필요한 어떠한 ECMA6 생성자에서 사용될 수 있습니다. 두가지 예를 들면 for-of 루프와 펼침 연산자 (...)입니다.
* 관찰자 (데이터 소비자): yield는 또한 next()를 통해 값을 받을 수 있습니다. 이것은 제너레이터는 next()를 통해 새로운 값이 들어오기 전까지 멈춰있는 데이터 소비자가 될수 있다는것을 의미 합니다.
* 코루틴 (데이터 생상자와 소비자): 제너레이터 멈출수 있고, 데이터 생성자와 데이터 소비자가 될수 있음을 고려하여, 많은 작업을 하지 않고 코루틴으로 바꿀 수 있습니다. (협력형 멀티 태스크)

## 22.3 Generators as iterators (data production)
## 22.3 이터레이터로써의 제너레이터 (데이터 생성자)
For this section, you should be familiar with ES6 iteration. The previous chapter has more information.

이 섹션의 경우, ES 이터레이션에 대해 잘 알고 있어야 합니다. 이전 쳅터에 더 많은 정보를 확인할 수 있습니다. 

As explained before, generator objects can be data producers, data consumers or both. This section looks at them as data producers, where they implement both the interfaces Iterable and Iterator (shown below). That means that the result of a generator function is both an iterable and an iterator. The full interface of generator objects will be shown later.

이전에 설명으로 제너레이터 객체는 데이터 생성자, 데이터 소비자 또는 둘다 될 수 있습니다. 이 섹션에서 데이터 소비자로써 볼 것이고, 이터러블이나 이터레이터 인터페이스(아래 보이는) 둘 다 구현을 볼 것입니다. 이것은 제너레이터 함수의 결과가 이터러블과 이터레이터라는것을 의미 합니다. 제너레이터의 전체 인터페이스는 아래를 보면 됩니다.

```
interface Iterable {
    [Symbol.iterator]() : Iterator;
}
interface Iterator {
    next() : IteratorResult;
}
interface IteratorResult {
    value : any;
    done : boolean;
}
```
I have omitted method return() of interface Iterable, because it is not relevant in this section.

이터러블의 인터페이스인 return()이 빠진 이유는 이것은 이번 센셕과 연관이 없기 때문입니다.

A generator function produces a sequence of values via yield, a data consumer consumes thoses values via the iterator method next(). For example, the following generator function produces the values 'a' and 'b':

이 제너레이터 함수는 yield를 통한 값의 순서열을 생산하고, 데이터 소비자는 이터러블 메소드인 next()를 통해서 이 값들을 소비 합니다. 예를 들면 아래 제너레이터 함수는 'a'와 'b'값을 생산합니다. :

```javascript
function* genFunc() {
    yield 'a';
    yield 'b';
}
```

This interaction shows how to retrieve the yielded values via the generator object genObj:

이 상호작용은 제너레이터 객체인 genObj를 통해 일드된 값을 어떻게 순환하는지 보여줍니다.:

```javascript
> const genObj = genFunc();
> genObj.next()
{ value: 'a', done: false }
> genObj.next()
{ value: 'b', done: false }
> genObj.next() // done: true => 순서열 종료
{ value: undefined, done: true }
```

### 22.3.1 Ways of iterating over a generator
### 22.3.1 제너레이터 전체 반복하는 방법

As generator objects are iterable, ES6 language constructs that support iterables can be applied to them. The following three ones are especially important.

제너레이터 객체는 이터러블로써, ES6언어에서이터러블을 지원하는 구조에서 적용할 수 있게 합니다. 아래 세개는 특히 중요 합니다.  

First, the for-of loop:

첫째, for-of 루프문:

```javascript
for (const x of genFunc()) {
    console.log(x);
}
// 출력:
// a
// b
```

Second, the spread operator (...), which turns iterated sequences into elements of an array (consult the chapter on parameter handling for more information on this operator):

둘째, 펼침 연산자 (...), 이것은 반복된 순서열을 배열의 원소들고 변환합니다. (파라미터를 다루는 이 연산자에 대한 더 많은 정보는 이 쳅터에서 확인 하세요.)

```javascript
const arr = [...genFunc()]; // ['a', 'b']
Third, destructuring:

> const [x, y] = genFunc();
> x
'a'
> y
'b'
```

### 22.3.2 Returning from a generator
### 22.3.2 제너레이터로부터 return

The previous generator function did not contain an explicit return. An implicit return is equivalent to returning undefined. Let’s examine a generator with an explicit return:

이전 제너레이터 함수는 명시적인 return을 포함하지 않습니다. 명시적인 return은 undefined를 반환하는것과 동일 합니다. 명시적인 return이 있을때 제너레이터를 살펴 봅시다.

```javascript
function* genFuncWithReturn() {
    yield 'a';
    yield 'b';
    return 'result';
}
```
The returned value shows up in the last object returned by next(), whose property done is true:

next()를 통한 마지막으로 반환된 객체에서 반환된 값은 나타나고, 이때 done 프로퍼티의 값은 true입니다.

```javascript
> const genObjWithReturn = genFuncWithReturn();
> genObjWithReturn.next()
{ value: 'a', done: false }
> genObjWithReturn.next()
{ value: 'b', done: false }
> genObjWithReturn.next()
{ value: 'result', done: true }
```
However, most constructs that work with iterables ignore the value inside the done object:

그러나 대부분의 이터러블이 작동하는 구조에서는 done객체의 value를 무시합니다.

```javascript
for (const x of genFuncWithReturn()) {
    console.log(x);
}
// Output:
// a
// b

const arr = [...genFuncWithReturn()]; // ['a', 'b']
```

yield*, an operator for making recursive generator calls, does consider values inside done objects. It is explained later.

재귀적인 제너레이터 호출을 위한 연산자 yield*는 done 객체의 value값이 고려 되어 집니다. 이것은 나중에 설명 드리겠습니다. 

### 22.3.3 Throwing an exception from a generator
### 22.3.3 제너레이터부터 예외 발생

If an exception leaves the body of a generator then next() throws it:

제너레이터 바디에서 예외가 남아 있다면, next() 시에 예외가 발생된다.:

```javascript
function* genFunc() {
    throw new Error('Problem!');
}
const genObj = genFunc();
genObj.next(); // Error: Problem!
```

That means that next() can produce three different “results”:

* For an item x in an iteration sequence, it returns { value: x, done: false }
* For the end of an iteration sequence with a return value z, it returns { value: z, done: true }
* For an exception that leaves the generator body, it throws that exception.
 
이것은 next()는 세가지 다른 "결과"를 만들 수 있다는 의미이다:
* 순환 순서열에서 원소 x를 대한 {value: x, done: false }를 반환한다.
* 순환 순서열의 종료와 반환값 z에 대한 {value: z, done:true}를 반환한다.
* 제너레이터 바디에 남겨진 예외에 대하여 예외를 발생 시킨다.

### 22.3.4 Example: iterating over properties
### 22.3.4 예제: 모든 프로퍼티 순환

Let’s look at an example that demonstrates how convenient generators are for implementing iterables. The following function, objectEntries(), returns an iterable over the properties of an object:

어떻게 간단한 제너레이터가 이터러블을 구현하기 위한 것인지 증명하는 예제를 보자. 다음 objectEntries 함수는 객체의 프로퍼티의 전체 순환을 반환한다:

```javascript
function* objectEntries(obj) {
    // In ES6, you can use strings or symbols as property keys,
    // Reflect.ownKeys() retrieves both
    const propKeys = Reflect.ownKeys(obj);

    for (const propKey of propKeys) {
        yield [propKey, obj[propKey]];
    }
}
```

This function enables you to iterate over the properties of an object jane via the for-of loop:
이 함수는 for-of루프를 통해 jane 객체의 프로퍼티 전체를 순환 가능하게 한다.

```javascript
const jane = { first: 'Jane', last: 'Doe' };
for (const [key,value] of objectEntries(jane)) {
    console.log(`${key}: ${value}`);
}
// 출력:
// first: Jane
// last: Doe
```
For comparison – an implementation of objectEntries() that doesn’t use generators is much more complicated:

비교 - 제너레이터를 사용하지 않은 objectEntries() 구현은 더욱더 복잡한다.

```javascript
function objectEntries(obj) {
    let index = 0;
    let propKeys = Reflect.ownKeys(obj);

    return {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            if (index < propKeys.length) {
                let key = propKeys[index];
                index++;
                return { value: [key, obj[key]] };
            } else {
                return { done: true };
            }
        }
    };
}
```

### 22.3.5 You can only yield in generators
### 22.3.5 제너레이터에서만 yield가 사용 가능하다

A significant limitation of generators is that you can only yield while you are (statically) inside a generator function. That is, yielding in callbacks doesn’t work:

제너레이터의 중요한 제한은 (적적으로) 제너레이터 함수 안에서만 일드할 수 있다는 것 이다. 즉, 콜백에서 일드는 작동하지 않는다: 

```javascript
function* genFunc() {
    ['a', 'b'].forEach(x => yield x); // SyntaxError
}
```
yield is not allowed inside non-generator functions, which is why the previous code causes a syntax error. In this case, it is easy to rewrite the code so that it doesn’t use callbacks (as shown below). But unfortunately that isn’t always possible.

yield는 비 제너레이터 함수에서 허용되지 않고, 이것이 이전 코드에서 문법 오류를 발생한 이유이다. 이 경우에, 코드를 재장하는것이 쉽기 때문에 (아래 보는것과 같이)콜백을 사용하지 않는다. 그러나 불행하게도 언제가 가능하지는 않다.

```javascript
function* genFunc() {
    for (const x of ['a', 'b']) {
        yield x; // OK
    }
}
```
The upside of this limitation is explained later: it makes generators easier to implement and compatible with event loops.
위의 제한에 대한 설명은 나중에 한다.: 이것은 제너레이터를 구현과 이벤트 루프와 호환하기 쉽게 만든다.

### 22.3.6 Recursion via yield*
### 22.3.6 yield*을 통한 재귀

You can only use yield within a generator function. Therefore, if you want to implement a recursive algorithm with generator, you need a way to call one generator from another one. This section shows that that is more complicated than it sounds, which is why ES6 has a special operator, yield\*, for this. For now, I only explain how yield\* works if both generators produce output, I’ll later explain how things work if input is involved.

제너레이터 함수안에서 yield를 사용 할 수 있다. 그러므로, 제너레이터에서 재귀 알고리즘을 구현하기 원한다면, 다른 곳에서 제너레이터를 호출하는 방법이 필요하다. 이 절은 그것은 들리는것보다 더 복잡하고 ES6에서 이를 위한 특별 연산자 yield\*를 가지는지는 이유에 대해서 볼 것이다. 지금은, 모든 제너레이터가 출력을 만든다면, yield\*가 어떻게 동작하는지 설명하고, 이후에 입력이 포함된 경우 어떻게 동작하는지 설명하겠다.

How can one generator recursively call another generator? Let’s assume you have written a generator function foo:

어떻게 하나의 제너레이터가 재귀적으로 다른 제너레이터를 호출할 수 있는가? 제너레이터 함수 foo를 가정해 보자:

```javascript
function* foo() {
    yield 'a';
    yield 'b';
}
```

How would you call foo from another generator function bar? The following approach does not work!

어떻게 다른 제너레이터 함수인 bar로부터 foo가 호출될까? 다음 접근은 동작하지 않는다!

```javascript
function* bar() {
    yield 'x';
    foo(); // does nothing!
    yield 'y';
}
```
Calling foo() returns an object, but does not actually execute foo(). That’s why ECMAScript 6 has the operator yield* for making recursive generator calls:

foo() 호출은 객체를 반환하지만, 실제로 foo()를 실행하지 않는다. 이것이 ECMAScript 6이 재귀 함수 호출을 위해 연산자 yield*를 가진 이유다.  

```javascript
function* bar() {
    yield 'x';
    yield* foo();
    yield 'y';
}


// Collect all values yielded by bar() in an array
// bar()를 통한 산출된 모든 값을 배열로 수집한다.
const arr = [...bar()];
    // ['x', 'a', 'b', 'y']
```

Internally, yield* works roughly as follows:

내부적으로 yield*는 대략 다음처럼 동작한다:

```javascript
function* bar() {
    yield 'x';
    for (const value of foo()) {
        yield value;
    }
    yield 'y';
}
```
The operand of yield* does not have to be a generator object, it can be any iterable:

yield*의 피연산자는 제너럴객체외에도 되고, 어떠한 이터러블도 될 수 있다:

```javascript
function* bla() {
    yield 'sequence';
    yield* ['of', 'yielded'];
    yield 'values';
}


const arr = [...bla()];
    // ['sequence', 'of', 'yielded', 'values']
```

### 22.3.6.1 yield* considers end-of-iteration values
### 22.3.6.1 yield*는 반복의 마지막 값이 고려된다

Most constructs that support iterables ignore the value included in the end-of-iteration object (whose property done is true). Generators provide that value via return. The result of yield* is the end-of-iteration value:

이터러블을 지원하는 대부분의 구조에서 순환 객체의 마지막에 포함된 (프로퍼티 done이 true인)값은 무시 된다. 제너레이터는 return을 통해 값을 제공한다. yield*의 결과 값은 마지막 순환값 이다.

```javascript
function* genFuncWithReturn() {
    yield 'a';
    yield 'b';
    return 'The result';
}
function* logReturned(genObj) {
    const result = yield* genObj;
    console.log(result); // (A)
}
```

If we want to get to line A, we first must iterate over all values yielded by logReturned():

만약 A줄을 얻고 싶다면, 우선 logReturned()를 통해 얻어진 뭐든 값을 순환 해야 한다. 

```javascript
> [...logReturned(genFuncWithReturn())]

The result
[ 'a', 'b' ]
```

#### 22.3.6.2 Iterating over trees
#### 22.3.6.2 트리 순환
Iterating over a tree with recursion is simple, writing an iterator for a tree with traditional means is complicated. That’s why generators shine here: they let you implement an iterator via recursion. As an example, consider the following data structure for binary trees. It is iterable, because it has a method whose key is Symbol.iterator. That method is a generator method and returns an iterator when called.

재귀적를 통한 트리 순환은 간단하고, 전통적인 의미에서 트리 순환을 작성하는것은 복잡하다. 이것이 제너레이터가 빛나는 이유다. 제너레이터는 재귀를 통해 이터레이터를 구현하게 한다. 예제 처럼, 다음 이진트리에 대한 다음 자료 구조 고려해 보자. 자료구조는 Symbol.iterator메소드를 가지고 있기 때문에 이터러블이다. 이 메소드는 제너레이터 메소드 이고, 호출 시에 이터레이터를 반환한다.

```javascript
class BinaryTree {
    constructor(value, left=null, right=null) {
        this.value = value;
        this.left = left;
        this.right = right;
    }

    /** 전위순회 */
    * [Symbol.iterator]() {
        yield this.value;
        if (this.left) {
            yield* this.left;
            // 축약하면: yield* this.left[Symbol.iterator]()
        }
        if (this.right) {
            yield* this.right;
        }
    }
}
```

The following code creates a binary tree and iterates over it via for-of:
다음 코드는 이진 트리를 생성하고, for-of를 통해 순환한다.

```javascript
const tree = new BinaryTree('a',
    new BinaryTree('b',
        new BinaryTree('c'),
        new BinaryTree('d')),
    new BinaryTree('e'));

for (const x of tree) {
    console.log(x);
}
// 출력:
// a
// b
// c
// d
// e
```

## 22.4 Generators as observers (data consumption)
## 22.4 관찰자로서 제너레이터 (데이터 소비자)
As consumers of data, generator objects conform to the second half of the generator interface, Observer:

데이터의 소비자로서 제너레이터 객체는 다음 반절의 제너레이터 인터페이스를 따른다, 관찰자:
```
interface Observer {
    next(value? : any) : void;
    return(value? : any) : void;
    throw(error) : void;
}
```
As an observer, a generator pauses until it receives input. There are three kinds of input, transmitted via the methods specified by the interface:

관찰자로서, 제너레이터는 입력을 받기 전까지 멈춰 있다. 세가지 종류의 입력이 있고, 이 인터페이스에 의해 정의된 메소드를 통해서 전송 된다.

* next() sends normal input.
* return() terminates the generator.
* throw() signals an error.

* next() 일반 입력 전달.
* reteurn() 제너레이터 종료.
* throw() 에러 신호를 보냄.

### 22.4.1 Sending values via next()
### 22.4.1 next()를 통한 값 보내기

If you use a generator as an observer, you send values to it via next() and it receives those values via yield:

제너레이터를 관찰자로서 사용한다면, next()를 통해 값을 제너레이터에 보내고고, 제너레이터는 yield를 통해 값을 받는다. 

```javascript
function* dataConsumer() {
    console.log('Started');
    console.log(`1. ${yield}`); // (A)
    console.log(`2. ${yield}`);
    return 'result';
}
```

Let’s use this generator interactively. First, we create a generator object:

대화형으로 제너레이터를 사용해 보자. 우선, 제너레이터 객체는 만든다:

```javascript
> const genObj = dataConsumer();
```

We now call genObj.next(), which starts the generator. Execution continues until the first yield, which is where the generator pauses. The result of next() is the value yielded in line A (undefined, because yield doesn’t have an operand). In this section, we are not interested in what next() returns, because we only use it to send values, not to retrieve values.

이제 제너레이터의 시작인 genObj.next()를 호출한다. 제너레이터가 멈추는 곳인 첫 yield 전 까지 계속 실행 된다. next()의 결과 줄 A에서 값이 산출(undefined, 왜냐하면 yield는 피 연산자가 없기 때문이다)된다. 이 절에서 오직 값을 받는게 아니라 보내기 위해 사용되기 때문에 next()가 무엇을 반환하는지에 대해 흥미가 없다. 

```javascript
> genObj.next()
Started
{ value: undefined, done: false }
```

We call next() two more times, in order to send the value 'a' to the first yield and the value 'b' to the second yield:

값 'a'를 첫 yield에 보내고 값 'b'를 두번째 yield에 보내기 위해 next()를 두번 더 호출 했다.

```javascript
> genObj.next('a')
1. a
{ value: undefined, done: false }

> genObj.next('b')
2. b
{ value: 'result', done: true }
```

The result of the last next() is the value returned from dataConsumer(). done being true indicates that the generator is finished.

마지막 next()결과는 dataConsumer()로 부터 전달 받은 값 이다. done이 true인 것은 제너레이터가 종료되었다는것을 나타낸다.

Unfortunately, next() is asymmetric, but that can’t be helped: It always sends a value to the currently suspended yield, but returns the operand of the following yield.

불행하게도, next()는 비대칭 이지만 도와 줄 수 없다: 항상 현재 멈춰 있는 yield에 값을 보내지만, 뒤따르는 yield의 피 연산자를 반환한다.

#### 22.4.1.1 The first next()
#### 22.4.1.1 첫번째 next()

When using a generator as an observer, it is important to note that the only purpose of the first invocation of next() is to start the observer. It is only ready for input afterwards, because this first invocation advances execution to the first yield. Therefore, any input you send via the first next() is ignored:

관찰자로써 제너레이터를 사용할때, 오직 첫번째 next의 호출 목적은 관찰자를 시작하는 것이라는 것을 주목하는게 중요하다. 첫 호출은 첫 yield로 전진하기 때문에, 관찰자는 나중에 입력만 준비한다.

```javascript
function* gen() {
    // (A)
    while (true) {
        const input = yield; // (B)
        console.log(input);
    }
}
const obj = gen();
obj.next('a');
obj.next('b');

// 출력:
// b
```

Initially, execution is paused in line A. The first invocation of next():

처음에는, 실행은 A줄에서 멈춰 있다. next() 첫 호출:

* Feeds the argument 'a' of next() to the generator, which has no way to receive it (as there is no yield). That’s why it is ignored.
* Advances to the yield in line B and pauses execution.
* Returns yield’s operand (undefined, because it doesn’t have an operand).

* next()의 'a' 인자값을 인자값을 받을수 있는 방법이 없는(yield가 없기 때문에) 제너레이터에게 공급한다. 이것이 무시된 이유다.
* B줄의 yield로 전진하고, 실행을 멈춘다.
* yield의 피 연산자를 반환(undefined, 왜냐하면 피연산자가 없기 때문에)한다.


The second invocation of next():

next() 두번째 호출:

* Feeds the argument 'b' of next() to the generator, which receives it via the yield in line B and assigns it to the variable input.
* Then execution continues until the next loop iteration, where it is paused again, in line B.
* Then next() returns with that yield’s operand (undefined).

* next()로 인자값 'b'를 B줄에서 yield를 통해 받고 변수 input값을 할당하는 제너레이터로 공급한다.
* 이후 다음 반복문 순환까지 계속해서 실행되고 B줄에서 다시 멈춘다.
* 이후 next()는 yield의 피연산자(undefined)로 반환한다.

The following utility function fixes this issue:

다음 유틸리티 함수는 이 이슈를 해결한다:

```javascript
/**
 * Returns a function that, when called,
 * returns a generator object that is immediately
 * ready for input via `next()`
 * 호출시, next()를 통해 입력이 즉시 준비된 제너레이터 객체 반환하는 함수를 반환한다.
 */
function coroutine(generatorFunction) {
    return function (...args) {
        const generatorObject = generatorFunction(...args);
        generatorObject.next();
        return generatorObject;
    };
}
```
To see how coroutine() works, let’s compare a wrapped generator with a normal one:

coroutine()의 동작 방식을 보려면 랩핑된 제너레이터와 평범한 것을 비교해 보자:

```javascript
const wrapped = coroutine(function* () {
    console.log(`First input: ${yield}`);
    return 'DONE';
});
const normal = function* () {
    console.log(`First input: ${yield}`);
    return 'DONE';
};
```
The wrapped generator is immediately ready for input:

랩핑된 제너레이터는 입력을 위해 즉시 준비 되어 있다:
```javascript
> wrapped().next('hello!')
First input: hello!
```

The normal generator needs an extra next() until it is ready for input:

일반 제너레이터는 입력을 위한 준비가 필요할 때까지 별도의 next()가 필요하다.
```javascript
> const genObj = normal();
> genObj.next()
{ value: undefined, done: false }
> genObj.next('hello!')
First input: hello!
{ value: 'DONE', done: true }
```

### 22.4.2 yield binds loosely
### 22.4.2 느슨한 yield 바인딩

yield binds very loosely, so that we don’t have to put its operand in parentheses:

yield는 매우 느슨하게 바인딩 되기 때문에 괄호 안에 피 연산자를 넣지 않아도 된다.

```javascript
yield a + b + c;
```
This is treated as:

이것은 다음처럼 다뤄진다:

```javascript
yield (a + b + c);
```
Not as:

다음 처럼이 아니라:
```javascript
(yield a) + b + c;
```

As a consequence, many operators bind more tightly than yield and you have to put yield in parentheses if you want to use it as an operand. For example, you get a SyntaxError if you make an unparenthesized yield an operand of plus:

이 결과 때문에, 많은 연산자는 yield보다 더 단단하게 바인딩 되고, 이 연산자에서 yield를 피연산자로 사용하려면 yield를 괄호 안에 넣어야 한다. 예를 들면 괄호 없이 yield를 더하기의 피연산자로 만들면 SyntexError가 발생된다.

```javascript
console.log('Hello' + yield); // SyntaxError
console.log('Hello' + yield 123); // SyntaxError

console.log('Hello' + (yield)); // OK
console.log('Hello' + (yield 123)); // OK
```
You do not need parens if yield is a direct argument in a function or method call:

yield가 함수나 메소드 호출에서의 직접적인 인자값이라면 괄호가 필요하지 않다:

```javascript
foo(yield 'a', yield 'b');
```

You also don’t need parens if you use yield on the right-hand side of an assignment:

yield를 할당의 오른쪽편에서 사용한다면 또한 괄호가 필요하지 않다:

```javascript
const input = yield;
```

#### 22.4.2.1 yield in the ES6 grammar
#### 22.4.2.1 ES6문법에서 yield

The need for parens around yield can be seen in the following grammar rules in the ECMAScript 6 specification. These rules describe how expressions are parsed. I list them here from general (loose binding, lower precedence) to specific (tight binding, higher precedence). Wherever a certain kind of expression is demanded, you can also use more specific ones. The opposite is not true. The hierarchy ends with ParenthesizedExpression, which means that you can mention any expression anywhere, if you put it in parentheses.

yield 주변에 괄호의 필요는 ECMAScript6 사양에서 다음 문법 규칙을 볼 수 있다. 이 규칙은 어떻게 표현식이 파씽되는지 설명한다. 여기에 일반적인 것 부터(느슨한 바인딩, 낮은 우선 순위) 특수한 것(강한 바인딩, 높은 우선순위)까지 나열한다. 표현식의 특수한 종류가 요구되는 곳이면, 더 특수한 것을 사용할 수 있다. 반대는 참이 아니다. 이 계층의 끝은 괄호이고 괄호 안에서 넣고 싶은 곳 어디던지 어떤 표현식도 사용할 수 있다. 

```
Expression : 표현식: 
    AssignmentExpression 할당 표현식
    Expression , AssignmentExpression 표현식, 할당 표현식
AssignmentExpression : 할당 표현식: 
    ConditionalExpression 조건 표현식
    YieldExpression 일드 표현식
    ArrowFunction 화살표함수
    LeftHandSideExpression = AssignmentExpression 왼손 표현식 = 할당 표현식
    LeftHandSideExpression AssignmentOperator AssignmentExpression 왼손 표현식 할당 연산자 할당 표현식

···

AdditiveExpression : 덧셈 표현식
    MultiplicativeExpression 곱셈 표현식
    AdditiveExpression + MultiplicativeExpression 덧셈표현식 + 곱셈 표현식
    AdditiveExpression - MultiplicativeExpression 덧셈표현식 - 곱셈 표현식
MultiplicativeExpression : 곱셈 표현식
    UnaryExpression 단항 표현식
    MultiplicativeExpression MultiplicativeOperator UnaryExpression 곱셈표현식 곱셈 연산자 단항 연산자

···

PrimaryExpression : 기본 표현식 :
    this
    IdentifierReference 식별자
    Literal 리터럴
    ArrayLiteral 배열리터럴
    ObjectLiteral 객체리터럴
    FunctionExpression 함수표현식
    ClassExpression 클래스표현식
    GeneratorExpression 제너레이터 표현식
    RegularExpressionLiteral 정규 표현식
    TemplateLiteral 템플릿리터럴
    ParenthesizedExpression 괄호 표현식
ParenthesizedExpression : 괄호표현식 :
    ( Expression ) (표현식)
```    
The operands of an AdditiveExpression are an AdditiveExpression and a MultiplicativeExpression. Therefore, using a (more specific) ParenthesizedExpression as an operand is OK, but using a (more general) YieldExpression isn’t.

덧셈 표현식의 피연산자는 덧셈표현식과 곱셈 표현식이다. 따라서 피 연산자로 (더 특수한)괄호 표현식을 사용하는것은 OK이지만, (더 일반적인)일드 표현식은 그렇지 않다.

### 22.4.3 return() and throw()
### 22.4.3 return() 과 throw()

Generator objects have two additional methods, return() and throw(), that are similar to next().

제너레이터 객체는 두개의 추가적인 메소드 return()과 throw()를 갖고, 이들은 next()와 유사하다.

Let’s recap how next(x) works (after the first invocation):

next(x) (첫 호출 이후)동작을 다시한번 보자:

1. The generator is currently suspended at a yield operator.
2. Send the value x to that yield, which means that it evaluates to x.
3. Proceed to the next yield, return or throw:
  * yield x leads to next() returning with { value: x, done: false }
  * return x leads to next() returning with { value: x, done: true }
  * throw err (not caught inside the generator) leads to next() throwing err.

1. 제너레이터는 현재 yield연산자에서 멈춰 있다.
2. 값 x를 yield에게 보낸다는것은 이것을 x로 평가한다는것을 의미한다.
3. return 또는 throw로 다음 yield를 진행한다.
  * yield x는 next()를 통해 { value: x, done: false }로 반환에 이르게 한다.
  * return x는 next()를 통해 { value: x, done: true }로 반환에 이르게 한다.
  * throw err (제너레이터 안에서 잡지 않음)는 next()를 통해 err를 던지게 한다.


return() and throw() work similarly to next(), but they do something different in step 2:

return() 와 throw() 동작 방식은 next()와 유사하나, 이것들은 2 단계에서 약간 다르다:

* return(x) executes return x at the location of yield.
* throw(x) executes throw x at the location of yield.

* return(x)는 yield 위치에서 return x를 실행한다.
* throw(x)는 yield 위치에서 throw x를 실행한다.

### 22.4.4 return() terminates the generator
### 22.4.4 return() 제너레이터 종료

return() performs a return at the location of the yield that led to the last suspension of the generator. Let’s use the following generator function to see how that works.

return()는 제너레이터의 마지막 멈춤으로 이끄는 yield의 지역에서 반환한다. 어떻게 동작하는지 보기 위해 다음 제너레이터 함수를 사용해 보자.

```javascript
function* genFunc1() {
    try {
        console.log('Started');
        yield; // (A)
    } finally {
        console.log('Exiting');
    }
}
```
In the following interaction, we first use next() to start the generator and to proceed until the yield in line A. Then we return from that location via return().

다음 상호작용에서, 제너레이션 시작과  A줄 yield까지 실행을 위해 처음에 next()를 사용한다. 이후 return()을 통해 해당 위치에서 반환 된다.

```javascript
> const genObj1 = genFunc1();
> genObj1.next()
Started
{ value: undefined, done: false }
> genObj1.return('Result')
Exiting
{ value: 'Result', done: true }
```


#### 22.4.4.1 Preventing termination
#### 22.4.4.1 종료 막기
You can prevent return() from terminating the generator if you yield inside the finally clause (using a return statement in that clause is also possible):

yield가 finally절 안에 있으면(finally 절에서 return문을 사용한은것은 또한 가능하다), 제너레이터의 종료에서 return()을 방지 할 수 있다.

```javascript
function* genFunc2() {
    try {
        console.log('Started');
        yield;
    } finally {
        yield 'Not done, yet!';
    }
}
```

This time, return() does not exit the generator function. Accordingly, the property done of the object it returns is false.

이때, return()는 제너레이터 함수를 빠져 나오지 않는다. 따라서 반환될 때 객체의 done 프로퍼티는 false이다.

```javascript
> const genObj2 = genFunc2();

> genObj2.next()
Started
{ value: undefined, done: false }

> genObj2.return('Result')
{ value: 'Not done, yet!', done: false }
```

You can invoke next() one more time. Similarly to non-generator functions, the return value of the generator function is the value that was queued prior to entering the finally clause.

next()를 한번 더 호출 할 수 있다. 비제너레이터 함수와 유사하고, 제너레이터 함수의 반환값은 finally 절에 들어가기 전에 대기된 값 이다.

```javascript
> genObj2.next()
{ value: 'Result', done: true }
```

### 22.4.4.2 Returning from a newborn generator
### 22.4.4.2 새로 만들어진 제너레이터에서 반환

Returning a value from a newborn generator (that hasn’t started yet) is allowed:

새로 만들어진 제너레이터(즉, 아직 시작되지 않은)에서 값을 반환하는 것은 허용된다:

```javascript
> function* genFunc() {}
> genFunc().return('yes')
{ value: 'yes', done: true }
```

Further reading

더 읽어보기

return() is also used to close iterators. The chapter on iteration has a detailed section on that.

return()는 또한 이터레이터를 종료하는데 사용된다. 이터레이션 장은 자세한 절을 가지고 있다.

### 22.4.5 throw() signals an error
### 22.4.5 throw() 에러 신호를 보낸다

throw() throws an exception at the location of the yield that led to the last suspension of the generator. Let’s examine how that works via the following generator function.

throw()는 제너레이터의 마지막 멈춤을 이끄는 yield에서 익셉션을 던진다. 다음 제너레이터 함수를 통해 어떻게 동작하는지 살펴보자.

```javascript
function* genFunc1() {
    try {
        console.log('Started');
        yield; // (A)
    } catch (error) {
        console.log('Caught: ' + error);
    }
}
```

In the following interaction, we first use next() to start the generator and proceed until the yield in line A. Then we throw an exception from that location.

다음 상호작용에서, 제너레이터를 시작하고 A줄 yield까지 실행하기 위해 처음에 next()를 사용한다. 이후 해당 장소로 부터 익셉션을 던진다.

```javascript
> const genObj1 = genFunc1();

> genObj1.next()
Started
{ value: undefined, done: false }

> genObj1.throw(new Error('Problem!'))
Caught: Error: Problem!
{ value: undefined, done: true }
```

The result of throw() (shown in the last line) stems from us leaving the function with an implicit return.

 함축적인 return를 이용하여 함수를 나가서 throw()의 결과(마지막 줄에서 보이는)가 발생한다.

### 22.4.5.1 Throwing from a newborn generator
### 22.4.5.1 새로 만들어진 제너레이터로 부터 던지기

Throwing an exception in a newborn generator (that hasn’t started yet) is allowed:

세로 만들어진 제너레이터(아직 시작전)에서 익셉션을 발생시키는것은 허용된다:

```javascript
> function* genFunc() {}
> genFunc().throw(new Error('Problem!'))
Error: Problem!
```

### 22.4.6 Example: processing asynchronously pushed data
### 22.4.6 예제: 비동기적인 푸시 데이터 처리

The fact that generators-as-observers pause while they wait for input makes them perfect for on-demand processing of data that is received asynchronously. The pattern for setting up a chain of generators for processing is as follows:

관찰자로써 제너레이터는 입력을 기다리는 동안 멈추있는 사실은  

Each member of the chain of generators (except the last one) has a parameter target. It receives data via yield and sends data via target.next().
The last member of the chain of generators has no parameter target and only receives data.
The whole chain is prefixed by a non-generator function that makes an asynchronous request and pushes the results into the chain of generators via next().

As an example, let’s chain generators to process a file that is read asynchronously.

The code of this example is in the file generator-examples/node/readlines.js. It must be executed via babel-node.

The following code sets up the chain: it contains the generators splitLines, numberLines and printLines. Data is pushed into the chain via the non-generator function readFile.

readFile(fileName, splitLines(numberLines(printLines())));
I’ll explain what these functions do when I show their code.

As previously explained, if generators receive input via yield, the first invocation of next() on the generator object doesn’t do anything. That’s why I use the previously shown helper function coroutine() to create coroutines here. It executes the first next() for us.

readFile() is the non-generator function that starts everything:

import {createReadStream} from 'fs';

/**
 * Creates an asynchronous ReadStream for the file whose name
 * is `fileName` and feed it to the generator object `target`.
 *
 * @see ReadStream https://nodejs.org/api/fs.html#fs_class_fs_readstream
 */
function readFile(fileName, target) {
    const readStream = createReadStream(fileName,
        { encoding: 'utf8', bufferSize: 1024 });
    readStream.on('data', buffer => {
        const str = buffer.toString('utf8');
        target.next(str);
    });
    readStream.on('end', () => {
        // Signal end of output sequence
        target.return();
    });
}
The chain of generators starts with splitLines:

/**
 * Turns a sequence of text chunks into a sequence of lines
 * (where lines are separated by newlines)
 */
const splitLines = coroutine(function* (target) {
    let previous = '';
    try {
        while (true) {
            previous += yield;
            let eolIndex;
            while ((eolIndex = previous.indexOf('\n')) >= 0) {
                const line = previous.slice(0, eolIndex);
                target.next(line);
                previous = previous.slice(eolIndex+1);
            }
        }
    } finally {
        // Handle the end of the input sequence
        // (signaled via `return()`)
        if (previous.length > 0) {
            target.next(previous);
        }
        // Signal end of output sequence
        target.return();
    }
});
Note an important pattern:

readFile uses the generator object method return() to signal the end of the sequence of chunks that it sends.
readFile sends that signal while splitLines is waiting for input via yield, inside an infinite loop. return() breaks from that loop.
splitLines uses a finally clause to handle the end-of-sequence.
The next generator is numberLines:

//**
 * Prefixes numbers to a sequence of lines
 */
const numberLines = coroutine(function* (target) {
    try {
        for (const lineNo = 0; ; lineNo++) {
            const line = yield;
            target.next(`${lineNo}: ${line}`);
        }
    } finally {
        // Signal end of output sequence
        target.return();
    }
});
The last generator is printLines:

/**
 * Receives a sequence of lines (without newlines)
 * and logs them (adding newlines).
 */
const printLines = coroutine(function* () {
    while (true) {
        const line = yield;
        console.log(line);
    }
});
The neat thing about this code is that everything happens lazily (on demand): lines are split, numbered and printed as they arrive; we don’t have to wait for all of the text before we can start printing.

22.4.7 yield*: the full story
As a rough rule of thumb, yield* performs (the equivalent of) a function call from one generator (the caller) to another generator (the callee).

So far, we have only seen one aspect of yield: it propagates yielded values from the callee to the caller. Now that we are interested in generators receiving input, another aspect becomes relevant: yield* also forwards input received by the caller to the callee. In a way, the callee becomes the active generator and can be controlled via the caller’s generator object.

22.4.7.1 Example: yield* forwards next()
The following generator function caller() invokes the generator function callee() via yield*.

function* callee() {
    console.log('callee: ' + (yield));
}
function* caller() {
    while (true) {
        yield* callee();
    }
}
callee logs values received via next(), which allows us to check whether it receives the value 'a' and 'b' that we send to caller.

> const callerObj = caller();

> callerObj.next() // start
{ value: undefined, done: false }

> callerObj.next('a')
callee: a
{ value: undefined, done: false }

> callerObj.next('b')
callee: b
{ value: undefined, done: false }
throw() and return() are forwarded in a similar manner.

22.4.7.2 The semantics of yield* expressed in JavaScript
I’ll explain the complete semantics of yield* by showing how you’d implemented it in JavaScript.

The following statement:

let yieldStarResult = yield* calleeFunc();
is roughly equivalent to:

let yieldStarResult;

const calleeObj = calleeFunc();
let prevReceived = undefined;
while (true) {
    try {
        // Forward input previously received
        const {value,done} = calleeObj.next(prevReceived);
        if (done) {
            yieldStarResult = value;
            break;
        }
        prevReceived = yield value;
    } catch (e) {
        // Pretend `return` can be caught like an exception
        if (e instanceof Return) {
            // Forward input received via return()
            calleeObj.return(e.returnedValue);
            return e.returnedValue; // “re-throw”
        } else {
            // Forward input received via throw()
            calleeObj.throw(e); // may throw
        }
    }
}
To keep things simple, several things are missing in this code:

The operand of yield* can be any iterable value.
return() and throw() are optional iterator methods. We should only call them if they exist.
If an exception is received and throw() does not exist, but return() does then return() is called (before throwing an exception) to give calleeObject the opportunity to clean up.
calleeObj can refuse to close, by returning an object whose property done is false. Then the caller also has to refuse to close and yield* must continue to iterate.
22.5 Generators as coroutines (cooperative multitasking)
We have seen generators being used as either sources or sinks of data. For many applications, it’s good practice to strictly separate these two roles, because it keeps things simpler. This section describes the full generator interface (which combines both roles) and one use case where both roles are needed: cooperative multitasking, where tasks must be able to both send and receive information.

22.5.1 The full generator interface
The full interface of generator objects, Generator, handles both output and input:

interface Generator {
    next(value? : any) : IteratorResult;
    throw(value? : any) : IteratorResult;
    return(value? : any) : IteratorResult;
}
interface IteratorResult {
    value : any;
    done : boolean;
}
This interface is described in the spec in the section “Properties of Generator Prototype”.

The interface Generator combines two interfaces that we have seen previously: Iterator for output and Observer for input.

interface Iterator { // data producer
    next() : IteratorResult;
    return?(value? : any) : IteratorResult;
}

interface Observer { // data consumer
    next(value? : any) : void;
    return(value? : any) : void;
    throw(error) : void;
}
22.5.2 Cooperative multitasking
Cooperative multitasking is an application of generators where we need them to handle both output and input. Before we get into how that works, let’s first review the current state of parallelism in JavaScript.

JavaScript runs in a single process. There are two ways in which this limitation is being abolished:

Multiprocessing: Web Workers let you run JavaScript in multiple processes. Shared access to data is one of the biggest pitfalls of multiprocessing. Web Workers avoid it by not sharing any data. That is, if you want a Web Worker to have a piece of data, you must send it a copy or transfer your data to it (after which you can’t access it anymore).
Cooperative multitasking: There are various patterns and libraries that experiment with cooperative multitasking. Multiple tasks are run, but only one at a time. Each task must explicitly suspend itself, giving it full control over when a task switch happens. In these experiments, data is often shared between tasks. But due to explicit suspension, there are few risks.
Two use cases benefit from cooperative multitasking, because they involve control flows that are mostly sequential, anyway, with occasional pauses:

Streams: A task sequentially processes a stream of data and pauses if there is no data available.
For binary streams, WHATWG is currently working on a standard proposal that is based on callbacks and Promises.
For streams of data, Communicating Sequential Processes (CSP) are an interesting solution. A generator-based CSP library is covered later in this chapter.
Asynchronous computations: A task blocks (pauses) until it receives the result of a long- running computation.
In JavaScript, Promises have become a popular way of handling asynchronous computations. Support for them is included in ES6. The next section explains how generators can make using Promises simpler.
22.5.2.1 Simplifying asynchronous computations via generators
Several Promise-based libraries simplify asynchronous code via generators. Generators are ideal as clients of Promises, because they can be suspended until a result arrives.

The following example demonstrates what that looks like if one uses the library co by T.J. Holowaychuk. We need two libraries (if we run Node.js code via babel-node):

import fetch from 'isomorphic-fetch';
const co = require('co');
co is the actual library for cooperative multitasking, isomorphic-fetch is a polyfill for the new Promise-based fetch API (a replacement of XMLHttpRequest; read “That’s so fetch!” by Jake Archibald for more information). fetch makes it easy to write a function getFile that returns the text of a file at a url via a Promise:

function getFile(url) {
    return fetch(url)
        .then(request => request.text());
}
We now have all the ingredients to use co. The following task reads the texts of two files, parses the JSON inside them and logs the result.

co(function* () {
    try {
        const [croftStr, bondStr] = yield Promise.all([  // (A)
            getFile('http://localhost:8000/croft.json'),
            getFile('http://localhost:8000/bond.json'),
        ]);
        const croftJson = JSON.parse(croftStr);
        const bondJson = JSON.parse(bondStr);

        console.log(croftJson);
        console.log(bondJson);
    } catch (e) {
        console.log('Failure to read: ' + e);
    }
});
Note how nicely synchronous this code looks, even though it makes an asynchronous call in line A. A generator-as-task makes an async call by yielding a Promise to the scheduler function co. The yielding pauses the generator. Once the Promise returns a result, the scheduler resumes the generator by passing it the result via next(). A simple version of co looks as follows.

function co(genFunc) {
    const genObj = genFunc();
    step(genObj.next());

    function step({value,done}) {
        if (!done) {
            // A Promise was yielded
            value
            .then(result => {
                step(genObj.next(result)); // (A)
            })
            .catch(error => {
                step(genObj.throw(error)); // (B)
            });
        }
    }
}
I have ignored that next() (line A) and throw() (line B) may throw exceptions (whenever an exception escapes the body of the generator function).

22.5.3 The limitations of cooperative multitasking via generators
Coroutines are cooperatively multitasked tasks that have no limitations: Inside a coroutine, any function can suspend the whole coroutine (the function activation itself, the activation of the function’s caller, the caller’s caller, etc.).

In contrast, you can only suspend a generator from directly within a generator and only the current function activation is suspended. Due to these limitations, generators are occasionally called shallow coroutines [3].

22.5.3.1 The benefits of the limitations of generators
The limitations of generators have two main benefits:

Generators are compatible with event loops, which provide simple cooperative multitasking in browsers. I’ll explain the details momentarily.
Generators are relatively easy to implement, because only a single function activation needs to be suspended and because browsers can continue to use event loops.
JavaScript already has a very simple style of cooperative multitasking: the event loop, which schedules the execution of tasks in a queue. Each task is started by calling a function and finished once that function is finished. Events, setTimeout() and other mechanisms add tasks to the queue.

This explanation of the event loop is a simplification that is good enough for now. If you are interested in details, consult the chapter on asynchronous programming.

This style of multitasking makes one important guarantee: run to completion; every function can rely on not being interrupted by another task until it is finished. Functions become transactions and can perform complete algorithms without anyone seeing the data they operate on in an itermediate state. Concurrent access to shared data makes multitasking complicated and is not allowed by JavaScript’s concurrency model. That’s why run to completion is a good thing.

Alas, coroutines prevent run to completion, because any function could suspend its caller. For example, the following algorithm consists of multiple steps:

step1(sharedData);
step2(sharedData);
lastStep(sharedData);
If step2 was to suspend the algorithm, other tasks could run before the last step of the algorithm is performed. Those tasks could contain other parts of the application which would see sharedData in an unfinished state. Generators preserve run to completion, they only suspend themselves and return to their caller.

co and similar libraries give you most of the power of coroutines, without their disadvantages:

They provide schedulers for tasks defined via generators.
Tasks “are” generators and can thus be fully suspended.
A recursive (generator) function call is only suspendable if it is done via yield*. That gives callers control over suspension.
22.6 Examples of generators
This section gives several examples of what generators can be used for.

The following GitHub repository contains the example code: generator-examples

22.6.1 Implementing iterables via generators
In the chapter on iteration, I implemented several iterables “by hand”. In this section, I use generators, instead.

22.6.1.1 The iterable combinator take()
take() converts a (potentially infinite) sequence of iterated values into a sequence of length n:

function* take(n, iterable) {
    for (const x of iterable) {
        if (n <= 0) return;
        n--;
        yield x;
    }
}
The following is an example of using it:

const arr = ['a', 'b', 'c', 'd'];
for (const x of take(2, arr)) {
    console.log(x);
}
// Output:
// a
// b
An implementation of take() without generators is more complicated:

function take(n, iterable) {
    const iter = iterable[Symbol.iterator]();
    return {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            if (n > 0) {
                n--;
                return iter.next();
            } else {
                maybeCloseIterator(iter);
                return { done: true };
            }
        },
        return() {
            n = 0;
            maybeCloseIterator(iter);
        }
    };
}
function maybeCloseIterator(iterator) {
    if (typeof iterator.return === 'function') {
        iterator.return();
    }
}
Note that the iterable combinator zip() does not profit much from being implemented via a generator, because multiple iterables are involved and for-of can’t be used.

22.6.1.2 Infinite iterables
naturalNumbers() returns an iterable over all natural numbers:

function* naturalNumbers() {
    for (const n=0;; n++) {
        yield n;
    }
}
This function is often used in conjunction with a combinator:

for (const x of take(3, naturalNumbers())) {
    console.log(x);
}
// Output
// 0
// 1
// 2
Here is the non-generator implementation, so you can compare:

function naturalNumbers() {
    let n = 0;
    return {
        [Symbol.iterator]() {
            return this;
        },
        next() {
            return { value: n++ };
        }
    }
}
22.6.1.3 Array-inspired iterable combinators: map, filter
Arrays can be transformed via the methods map and filter. Those methods can be generalized to have iterables as input and iterables as output.

22.6.1.3.1 A generalized map()
This is the generalized version of map:

function* map(iterable, mapFunc) {
    for (const x of iterable) {
        yield mapFunc(x);
    }
}
map() works with infinite iterables:

> [...take(4, map(naturalNumbers(), x => x * x))]
[ 0, 1, 4, 9 ]
22.6.1.3.2 A generalized filter()
This is the generalized version of filter:

function* filter(iterable, filterFunc) {
    for (const x of iterable) {
        if (filterFunc(x)) {
            yield x;
        }
    }
}
filter() works with infinite iterables:

> [...take(4, filter(naturalNumbers(), x => (x % 2) === 0))]
[ 0, 2, 4, 6 ]
22.6.2 Generators for lazy evaluation
The next two examples show how generators can be used to process a stream of characters.

The input is a stream of characters.
Step 1 – tokenizing (characters → words): The characters are grouped into words, strings that match the regular expression /^[A-Za-z0-9]$/. Non-word characters are ignored, but they separate words. The input of this step is a stream of characters, the output a stream of words.
Step 2 – extracting numbers (words → numbers): only keep words that match the regular expression /^[0-9]+$/ and convert them to numbers.
Step 3 – adding numbers (numbers → numbers): for every number received, return the total received so far.
The neat thing is that everything is computed lazily (incrementally and on demand): computation starts as soon as the first character arrives. For example, we don’t have to wait until we have all characters to get the first word.

22.6.2.1 Lazy pull (generators as iterators)
Lazy pull with generators works as follows. The three generators implementing steps 1–3 are chained as follows:

addNumbers(extractNumbers(tokenize(CHARS)))
Each of the chain members pulls data from a source and yields a sequence of items. Processing starts with tokenize whose source is the string CHARS.

22.6.2.1.1 Step 1 – tokenizing
The following trick makes the code a bit simpler: the end-of-sequence iterator result (whose property done is false) is converted into the sentinel value END_OF_SEQUENCE.

/**
 * Returns an iterable that transforms the input sequence
 * of characters into an output sequence of words.
 */
function* tokenize(chars) {
    const iterator = chars[Symbol.iterator]();
    let ch;
    do {
        ch = getNextItem(iterator); // (A)
        if (isWordChar(ch)) {
            let word = '';
            do {
                word += ch;
                ch = getNextItem(iterator); // (B)
            } while (isWordChar(ch));
            yield word; // (C)
        }
        // Ignore all other characters
    } while (ch !== END_OF_SEQUENCE);
}
const END_OF_SEQUENCE = Symbol();
function getNextItem(iterator) {
    const {value,done} = iterator.next();
    return done ? END_OF_SEQUENCE : value;
}
function isWordChar(ch) {
    return typeof ch === 'string' && /^[A-Za-z0-9]$/.test(ch);
}
How is this generator lazy? When you ask it for a token via next(), it pulls its iterator (lines A and B) as often as needed to produce as token and then yields that token (line C). Then it pauses until it is again asked for a token. That means that tokenization starts as soon as the first characters are available, which is convenient for streams.

Let’s try out tokenization. Note that the spaces and the dot are non-words. They are ignored, but they separate words. We use the fact that strings are iterables over characters (Unicode code points). The result of tokenize() is an iterable over words, which we turn into an array via the spread operator (...).

> [...tokenize('2 apples and 5 oranges.')]
[ '2', 'apples', 'and', '5', 'oranges' ]
22.6.2.1.2 Step 2 – extracting numbers
This step is relatively simple, we only yield words that contain nothing but digits, after converting them to numbers via Number().

/**
 * Returns an iterable that filters the input sequence
 * of words and only yields those that are numbers.
 */
function* extractNumbers(words) {
    for (const word of words) {
        if (/^[0-9]+$/.test(word)) {
            yield Number(word);
        }
    }
}
You can again see the laziness: If you ask for a number via next(), you get one (via yield) as soon as one is encountered in words.

Let’s extract the numbers from an Array of words:

> [...extractNumbers(['hello', '123', 'world', '45'])]
[ 123, 45 ]
Note that strings are converted to numbers.

22.6.2.1.3 Step 3 – adding numbers
/**
 * Returns an iterable that contains, for each number in
 * `numbers`, the total sum of numbers encountered so far.
 * For example: 7, 4, -1 --> 7, 11, 10
 */
function* addNumbers(numbers) {
    let result = 0;
    for (const n of numbers) {
        result += n;
        yield result;
    }
}
Let’s try a simple example:

> [...addNumbers([5, -2, 12])]
[ 5, 3, 15 ]
22.6.2.1.4 Pulling the output
On its own, the chain of generator doesn’t produce output. We need to actively pull the output via the spread operator:

const CHARS = '2 apples and 5 oranges.';
const CHAIN = addNumbers(extractNumbers(tokenize(CHARS)));
console.log([...CHAIN]);
    // [ 2, 7 ]
The helper function logAndYield allows us to examine whether things are indeed computed lazily:

function* logAndYield(iterable, prefix='') {
    for (const item of iterable) {
        console.log(prefix + item);
        yield item;
    }
}

const CHAIN2 = logAndYield(addNumbers(extractNumbers(tokenize(logAndYield(CHA\
RS)))), '-> ');
[...CHAIN2];

// Output:
// 2
//  
// -> 2
// a
// p
// p
// l
// e
// s
//  
// a
// n
// d
//  
// 5
//  
// -> 7
// o
// r
// a
// n
// g
// e
// s
// .
The output shows that addNumbers produces a result as soon as the characters '2' and ' ' are received.

22.6.2.2 Lazy push (generators as observables)
Not much work is needed to convert the previous pull-based algorithm into a push-based one. The steps are the same. But instead of finishing via pulling, we start via pushing.

As previously explained, if generators receive input via yield, the first invocation of next() on the generator object doesn’t do anything. That’s why I use the previously shown helper function coroutine() to create coroutines here. It executes the first next() for us.

The following function send() does the pushing.

/**
 * Pushes the items of `iterable` into `sink`, a generator.
 * It uses the generator method `next()` to do so.
 */
function send(iterable, sink) {
    for (const x of iterable) {
        sink.next(x);
    }
    sink.return(); // signal end of stream
}
When a generator processes a stream, it needs to be aware of the end of the stream, so that it can clean up properly. For pull, we did this via a special end-of-stream sentinel. For push, the end-of-stream is signaled via return().

Let’s test send() via a generator that simply outputs everything it receives:

/**
 * This generator logs everything that it receives via `next()`.
 */
const logItems = coroutine(function* () {
    try {
        while (true) {
            const item = yield; // receive item via `next()`
            console.log(item);
        }
    } finally {
        console.log('DONE');
    }
});
Let’s send logItems() three characters via a string (which is an iterable over Unicode code points).

> send('abc', logItems());
a
b
c
DONE
22.6.2.2.1 Step 1 – tokenize
Note how this generator reacts to the end of the stream (as signaled via return()) in two finally clauses. We depend on return() being sent to either one of the two yields. Otherwise, the generator would never terminate, because the infinite loop starting in line A would never terminate.

/**
 * Receives a sequence of characters (via the generator object
 * method `next()`), groups them into words and pushes them
 * into the generator `sink`.
 */
const tokenize = coroutine(function* (sink) {
    try {
        while (true) { // (A)
            let ch = yield; // (B)
            if (isWordChar(ch)) {
                // A word has started
                let word = '';
                try {
                    do {
                        word += ch;
                        ch = yield; // (C)
                    } while (isWordChar(ch));
                } finally {
                    // The word is finished.
                    // We get here if
                    // - the loop terminates normally
                    // - the loop is terminated via `return()` in line C
                    sink.next(word); // (D)
                }
            }
            // Ignore all other characters
        }
    } finally {
        // We only get here if the infinite loop is terminated
        // via `return()` (in line B or C).
        // Forward `return()` to `sink` so that it is also
        // aware of the end of stream.
        sink.return();
    }
});

function isWordChar(ch) {
    return /^[A-Za-z0-9]$/.test(ch);
}
This time, the laziness is driven by push: as soon as the generator has received enough characters for a word (in line C), it pushes the word into sink (line D). That is, the generator does not wait until it has received all characters.

tokenize() demonstrates that generators work well as implementations of linear state machines. In this case, the machine has two states: “inside a word” and “not inside a word”.

Let’s tokenize a string:

> send('2 apples and 5 oranges.', tokenize(logItems()));
2
apples
and
5
oranges
22.6.2.2.2 Step 2 – extract numbers
This step is straightforward.

/**
 * Receives a sequence of strings (via the generator object
 * method `next()`) and pushes only those strings to the generator
 * `sink` that are “numbers” (consist only of decimal digits).
 */
const extractNumbers = coroutine(function* (sink) {
    try {
        while (true) {
            const word = yield;
            if (/^[0-9]+$/.test(word)) {
                sink.next(Number(word));
            }
        }
    } finally {
        // Only reached via `return()`, forward.
        sink.return();
    }
});
Things are again lazy: as soon as a number is encountered, it is pushed to sink.

Let’s extract the numbers from an Array of words:

> send(['hello', '123', 'world', '45'], extractNumbers(logItems()));
123
45
DONE
Note that the input is a sequence of strings, while the output is a sequence of numbers.

22.6.2.2.3 Step 3 – add numbers
This time, we react to the end of the stream by pushing a single value and then closing the sink.

/**
 * Receives a sequence of numbers (via the generator object
 * method `next()`). For each number, it pushes the total sum
 * so far to the generator `sink`.
 */
const addNumbers = coroutine(function* (sink) {
    let sum = 0;
    try {
        while (true) {
            sum += yield;
            sink.next(sum);
        }
    } finally {
        // We received an end-of-stream
        sink.return(); // signal end of stream
    }
});
Let’s try out this generator:

> send([5, -2, 12], addNumbers(logItems()));
5
3
15
DONE
22.6.2.2.4 Pushing the input
The chain of generators starts with tokenize and ends with logItems, which logs everything it receives. We push a sequence of characters into the chain via send:

const INPUT = '2 apples and 5 oranges.';
const CHAIN = tokenize(extractNumbers(addNumbers(logItems())));
send(INPUT, CHAIN);

// Output
// 2
// 7
// DONE
The following code proves that processing really happens lazily:

const CHAIN2 = tokenize(extractNumbers(addNumbers(logItems({ prefix: '-> ' })\
)));
send(INPUT, CHAIN2, { log: true });

// Output
// 2
//  
// -> 2
// a
// p
// p
// l
// e
// s
//  
// a
// n
// d
//  
// 5
//  
// -> 7
// o
// r
// a
// n
// g
// e
// s
// .
// DONE
The output shows that addNumbers produces a result as soon as the characters '2' and ' ' are pushed.

22.6.3 Cooperative multi-tasking via generators
22.6.3.1 Pausing long-running tasks
In this example, we create a counter that is displayed on a web page. We improve an initial version until we have a cooperatively multitasked version that doesn’t block the main thread and the user interface.

This is the part of the web page in which the counter should be displayed:

<body>
    Counter: <span id="counter"></span>
</body>
This function displays a counter that counts up forever1:

function countUp(start = 0) {
    const counterSpan = document.querySelector('#counter');
    while (true) {
        counterSpan.textContent = String(start);
        start++;
    }
}
If you ran this function, it would completely block the user interface thread in which it runs and its tab would become unresponsive.

Let’s implement the same functionality via a generator that periodically pauses via yield (a scheduling function for running this generator is shown later):

function* countUp(start = 0) {
    const counterSpan = document.querySelector('#counter');
    while (true) {
        counterSpan.textContent = String(start);
        start++;
        yield; // pause
    }
}
Let’s add one small improvement. We move the update of the user interface to another generator, displayCounter, which we call via yield*. As it is a generator, it can also take care of pausing.

function* countUp(start = 0) {
    while (true) {
        start++;
        yield* displayCounter(start);
    }
}
function* displayCounter(counter) {
    const counterSpan = document.querySelector('#counter');
    counterSpan.textContent = String(counter);
    yield; // pause
}
Lastly, this is a scheduling function that we can use to run countUp(). Each execution step of the generator is handled by a separate task, which is created via setTimeout(). That means that the user interface can schedule other tasks in between and will remain responsive.

function run(generatorObject) {
    if (!generatorObject.next().done) {
        // Add a new task to the event queue
        setTimeout(function () {
            run(generatorObject);
        }, 1000);
    }
}
With the help of run, we get a (nearly) infinite count-up that doesn’t block the user interface:

run(countUp());
You can run this example online.

22.6.3.2 Cooperative multitasking with generators and Node.js-style callbacks
If you call a generator function (or method), it does not have access to its generator object; its this is the this it would have if it were a non-generator function. A work-around is to pass the generator object into the generator function via yield.

The following Node.js script uses this technique, but wraps the generator object in a callback (next, line A). It must be run via babel-node.

import {readFile} from 'fs';

const fileNames = process.argv.slice(2);

run(function* () {
    const next = yield;
    for (const f of fileNames) {
        const contents = yield readFile(f, { encoding: 'utf8' }, next);
        console.log('##### ' + f);
        console.log(contents);
    }
});
In line A, we get a callback that we can use with functions that follow Node.js callback conventions. The callback uses the generator object to wake up the generator, as you can see in the implementation of run():

function run(generatorFunction) {
    const generatorObject = generatorFunction();

    // Step 1: Proceed to first `yield`
    generatorObject.next();

    // Step 2: Pass in a function that the generator can use as a callback
    function nextFunction(error, result) {
        if (error) {
            generatorObject.throw(error);
        } else {
            generatorObject.next(result);
        }
    }
    generatorObject.next(nextFunction);

    // Subsequent invocations of `next()` are triggered by `nextFunction`
}
22.6.3.3 Communicating Sequential Processes (CSP)
The library js-csp brings Communicating Sequential Processes (CSP) to JavaScript, a style of cooperative multitasking that is similar to ClojureScript’s core.async and Go’s goroutines. js-csp has two abstractions:

Processes: are cooperatively multitasked tasks and implemented by handing a generator function to the scheduling function go().
Channels: are queues for communication between processes. Channels are created by calling chan().
As an example, let’s use CSP to handle DOM events, in a manner reminiscent of Functional Reactive Programming. The following code uses the function listen() (which is shown later) to create a channel that outputs mousemove events. It then continuously retrieves the output via take, inside an infinite loop. Thanks to yield, the process blocks until the channel has output.

import csp from 'js-csp';

csp.go(function* () {
    const element = document.querySelector('#uiElement1');
    const channel = listen(element, 'mousemove');
    while (true) {
        const event = yield csp.take(channel);
        const x = event.layerX || event.clientX;
        const y = event.layerY || event.clientY;
        element.textContent = `${x}, ${y}`;
    }
});
listen() is implemented as follows.

function listen(element, type) {
    const channel = csp.chan();
    element.addEventListener(type,
        event => {
            csp.putAsync(channel, event);
        });
    return channel;
}
This example is taken from the blog post “Taming the Asynchronous Beast with CSP Channels in JavaScript” by James Long. Consult this blog post for more information on CSP.

22.7 Inheritance within the iteration API (including generators)
This is a diagram of how various objects are connected in ECMAScript 6 (it is based on Allen Wirf-Brock’s diagram in the ECMAScript specification):


Legend:

The white (hollow) arrows express the has-prototype relationship (inheritance) between objects. In other words: a white arrow from x to y means that Object.getPrototypeOf(x) === y.
Parentheses indicate that an object exists, but is not accessible via a global variable.
An instanceof arrow from x to y means that x instanceof y.
Remember that o instanceof C is equivalent to C.prototype.isPrototypeOf(o).
A prototype arrow from x to y means that x.prototype === y.
The right column shows an instance with its prototypes, the middle column shows a function and its prototypes, the left column shows classes for functions (metafunctions, if you will), connected via a subclass-of relationship.
The diagram reveals two interesting facts:

First, a generator function g works very much like a constructor (however, you can’t invoke it via new; that causes a TypeError): The generator objects it creates are instances of it, methods added to g.prototype become prototype methods, etc.:

> function* g() {}
> g.prototype.hello = function () { return 'hi!'};
> const obj = g();
> obj instanceof g
true
> obj.hello()
'hi!'
Second, if you want to make methods available for all generator objects, it’s best to add them to (Generator).prototype. One way of accessing that object is as follows:

const Generator = Object.getPrototypeOf(function* () {});
Generator.prototype.hello = function () { return 'hi!'};
const generatorObject = (function* () {})();
generatorObject.hello(); // 'hi!'
22.7.1 IteratorPrototype
There is no (Iterator) in the diagram, because no such object exists. But, given how instanceof works and because (IteratorPrototype) is a prototype of g1(), you could still say that g1() is an instance of Iterator.

All iterators in ES6 have (IteratorPrototype) in their prototype chain. That object is iterable, because it has the following method. Therefore, all ES6 iterators are iterable (as a consequence, you can apply for-of etc. to them).

[Symbol.iterator]() {
    return this;
}
The specification recommends to use the following code to access (IteratorPrototype):

const proto = Object.getPrototypeOf.bind(Object);
const IteratorPrototype = proto(proto([][Symbol.iterator]()));
You could also use:

const IteratorPrototype = proto(proto(function* () {}.prototype));
Quoting the ECMAScript 6 specification:

ECMAScript code may also define objects that inherit from IteratorPrototype. The IteratorPrototype object provides a place where additional methods that are applicable to all iterator objects may be added.

IteratorPrototype will probably become directly accessible in an upcoming version of ECMAScript and contain tool methods such as map() and filter() (source).

22.7.2 The value of this in generators
A generator function combines two concerns:

It is a function that sets up and returns a generator object.
It contains the code that the generator object steps through.
That’s why it’s not immediately obvious what the value of this should be inside a generator.

In function calls and method calls, this is what it would be if gen() wasn’t a generator function, but a normal function:

function* gen() {
    'use strict'; // just in case
    yield this;
}

// Retrieve the yielded value via destructuring
const [functionThis] = gen();
console.log(functionThis); // undefined

const obj = { method: gen };
const [methodThis] = obj.method();
console.log(methodThis === obj); // true
If you access this in a generator that was invoked via new, you get a ReferenceError (source: ES6 spec):

function* gen() {
    console.log(this); // ReferenceError
}
new gen();
A work-around is to wrap the generator in a normal function that hands the generator its generator object via next(). That means that the generator must use its first yield to retrieve its generator object:

const generatorObject = yield;
22.8 Style consideration: whitespace before and after the asterisk
Reasonable – and legal – variations of formatting the asterisk are:

A space before and after it:
function * foo(x, y) { ··· }
A space before it:
function *foo(x, y) { ··· }
A space after it:
function* foo(x, y) { ··· }
No whitespace before and after it:
function*foo(x, y) { ··· }
Let’s figure out which of these variations make sense for which constructs and why.

22.8.1 Generator function declarations and expressions
Here, the star is only used because generator (or something similar) isn’t available as a keyword. If it were, then a generator function declaration would look like this:

generator foo(x, y) {
    ···
}
Instead of generator, ECMAScript 6 marks the function keyword with an asterisk. Thus, function* can be seen as a synonym for generator, which suggests writing generator function declarations as follows.

function* foo(x, y) {
    ···
}
Anonymous generator function expressions would be formatted like this:

const foo = function* (x, y) {
    ···
}
22.8.2 Generator method definitions
When writing generator method definitions, I recommend to format the asterisk as follows.

const obj = {
    * generatorMethod(x, y) {
        ···
    }
};
There are three arguments in favor of writing a space after the asterisk.

First, the asterisk shouldn’t be part of the method name. On one hand, it isn’t part of the name of a generator function. On the other hand, the asterisk is only mentioned when defining a generator, not when using it.

Second, a generator method definition is an abbreviation for the following syntax. (To make my point, I’m redundantly giving the function expression a name, too.)

const obj = {
    generatorMethod: function* generatorMethod(x, y) {
        ···
    }
};
If method definitions are about omitting the function keyword then the asterisk should be followed by a space.

Third, generator method definitions are syntactically similar to getters and setters (which are already available in ECMAScript 5):

const obj = {
    get foo() {
        ···
    }
    set foo(value) {
        ···
    }
};
The keywords get and set can be seen as modifiers of a normal method definition. Arguably, an asterisk is also such a modifier.

22.8.3 Formatting recursive yield
The following is an example of a generator function yielding its own yielded values recursively:

function* foo(x) {
    ···
    yield* foo(x - 1);
    ···
}
The asterisk marks a different kind of yield operator, which is why the above way of writing it makes sense.

22.8.4 Documenting generator functions and methods
Kyle Simpson (@getify) proposed something interesting: Given that we often append parentheses when we write about functions and methods such as Math.max(), wouldn’t it make sense to prepend an asterisk when writing about generator functions and methods? For example: should we write *foo() to refer to the generator function in the previous subsection? Let me argue against that.

When it comes to writing a function that returns an iterable, a generator is only one of the several options. I think it is better to not give away this implementation detail via marked function names.

Furthermore, you don’t use the asterisk when calling a generator function, but you do use parentheses.

Lastly, the asterisk doesn’t provide useful information – yield* can also be used with functions that return an iterable. But it may make sense to mark the names of functions and methods (including generators) that return iterables. For example, via the suffix Iter.

22.9 FAQ: generators
22.9.1 Why use the keyword function* for generators and not generator?
Due to backward compatibility, using the keyword generator wasn’t an option. For example, the following code (a hypothetical ES6 anonymous generator expression) could be an ES5 function call followed by a code block.

generator (a, b, c) {
    ···
}
I find that the asterisk naming scheme extends nicely to yield*.

22.9.2 Is yield a keyword?
yield is only a reserved word in strict mode. A trick is used to bring it to ES6 sloppy mode: it becomes a contextual keyword, one that is only available inside generators.

22.10 Conclusion
I hope that this chapter convinced you that generators are a useful and versatile tool.

I like that generators let you implement cooperatively multitasked tasks that block while making asynchronous function calls. In my opinion that’s the right mental model for async calls. Hopefully, JavaScript goes further in this direction in the future.

22.11 Further reading
Sources of this chapter:

[1] “Async Generator Proposal” by Jafar Husain

[2] “A Curious Course on Coroutines and Concurrency” by David Beazley

[3] “Why coroutines won’t work on the web” by David Herman

Or rather, the function counts up until the number start overflows and becomes Infinity, at which point it doesn’t change, anymore.↩
