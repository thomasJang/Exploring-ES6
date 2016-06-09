#  9. `Variables and scoping` 변수와 스코프

##  9.1 `Overview` 개요

`ES6 provides two new ways of declaring variables: let and const, which mostly replace the ES5 way of declaring variables, var.`

`ES6`에서는 변수를 선언하는 두가지 새로운 방법을 제공합니다. `ES5`에서 변수를 선언하는 방법인 `var`를 거의 완벽하게 대체할 수 있는 `let`과 `const`입니다.

###  9.1.1 `let`

`let works similarly to var, but the variable it declares is block-scoped, it only exists within the current block. var is function-scoped.`

`let`은 `var`와 유사하게 동작하지만, `let`으로 선언한 변수는 선언된 블록 내에서만 존재하는 블록 스코프입니다. `var`는 함수 스코프입니다.

`In the following code, you can see that the let-declared variable tmp only exists with the block that starts in line A:`

다음 코드에서 `let`으로 선언된 변수 `tmp`는 `A`라인으로 시작된 블록 내에서만 존재하는 것을 볼 수 있습니다.

```javascript
function order(x, y) {
    if (x > y) { // (A)
        let tmp = x;
        x = y;
        y = tmp;
    }
    console.log(tmp===x); // ReferenceError: tmp is not defined
    return [x, y];
}
```

### 9.1.2 const

`const works like let, but the variable you declare must be immediately initialized, with a value that can’t be changed afterwards.`

`const`는 `let`처럼 동작하지만, `const`로 선언한 변수는 즉시 초기화되어야 합니다. 초기화된 이후에 값을 변경할 수 없습니다.

```javascript
const foo; // SyntaxError: missing = in const declaration

const bar = 123;
bar = 456; // TypeError: `bar` is read-only
```

`Since for-of creates one binding (storage space for a variable) per loop iteration, it is OK to const-declare the loop variable:`

`for-of` 반복문은 루프 반복마다 하나의 바인딩 ( 변수의 저장 공간 )을 생성하기 때문에 `const`로 루프 변수를 선언하는 것도 가능합니다.

```javascript
for (const x of ['a', 'b']) {
    console.log(x);
}
// Output:
// a
// b
```

###  9.1.3 `Ways of declaring variables` 변수를 선언하는 방법들

`The following table gives an overview of six ways in which variables can be declared in ES6:`

아래 표는 `ES6`에서 변수가 선언되는 6가지 방법을 보여줍니다.

| | Hoisting | Scope | Creates global properties |
| -------- | ----- | ------- | ------ | ---------- |
| var | Declaration | Function | Yes |
| let | Temporal dead zone | Block | No |
| const | Temporal dead zone | Block | No |
| function | Complete | Block | Yes |
| class | No | Block | No |
| import | Complete | Module-global | No |

##  9.2 `Block scoping via let and const` `let`과 `const`를 통한 블록 스코핑

`Both let and const create variables that are block-scoped – they only exist within the innermost block that surrounds them. The following code demonstrates that the const-declared variable tmp only exists inside the then-block of the if statement:`

`let`과 `const`는 둘 다 변수를 둘러싼 가장 가까운 블록 내에서만 존재하는 블록 스코프 변수를 생성합니다. 아래 예제에서 `const`로 선언된 변수 `tmp`가 `if`문의 블록 안에서만 존재하는 것을 볼 수 있습니다.

```javascript
function func() {
    if (true) {
        const tmp = 123;
    }
    console.log(tmp); // ReferenceError: tmp는 정의되지 않았음
}
```

`In contrast, var-declared variables are function-scoped:`

반면, `var`로 선언된 변수는 함수 스코프를 가집니다.

```javascript
function func() {
    if (true) {
        var tmp = 123;
    }
    console.log(tmp); // 123
}
```
`Block scoping means that you can shadow variables within a function:`

블록 스코프는 함수 내에서 변수를 가릴 수 있습니다.

```javascript
function func() {
  const foo = 5;
  if (···) {
     const foo = 10; // 블록 밖의 변수 foo를 가림
     console.log(foo); // 10
  }
  console.log(foo); // 5
}
```

## 9.3 `const creates immutable variables` `const` 는 불변 (immutable) 변수를 생성한다

`Variables created by let are mutable:`

`let`으로 생성한 변수는 변경 가능합니다.

```javascript
let foo = 'abc';
foo = 'def';
console.log(foo); // def
```

`Constants, variables created by const, are immutable – you can’t assign them a different value:`

`const`로 생성된 변수는 상수로서 다른 값을 할당할 수 없으며 불변입니다.

```javascript
const foo = 'abc';
foo = 'def'; // TypeError
```

> :notebook: `Spec detail: changing a const variable always throws a TypeError`  
> 스펙에 따르면 `const` 변수의 값을 변경하는 것은 항상 `TypeError`가 발생합니다.  
> `Normally, changing an immutable binding only causes an exception in strict mode, as per SetMutableBinding(). But const-declared variables always produce strict bindings – see FunctionDeclarationInstantiation(func, argumentsList), step 35.b.i.1.`  
> 일반적으로, 불변 바인딩을 변경하는 것은 `strict mode`에서만 [`SetMutableBinding()`](http://www.ecma-international.org/ecma-262/6.0/#sec-declarative-environment-records-setmutablebinding-n-v-s)에서 예외가 발생합니다. 하지만 `const`로 변수를 선언하면 언제나 엄격한( strict )바인딩을 생성합니다. - 35.b.i.1 장의 [FunctionDeclarationInstantiation(func, argumentsList)](http://www.ecma-international.org/ecma-262/6.0/#sec-functiondeclarationinstantiation)를 확인하세요.

### 9.3.1 `Pitfall: const does not make the value immutable` 함정 : `const`는 값을 불변으로 만들지 않는다. 

`const only means that a variable always has the same value, but it does not mean that the value itself is or becomes immutable. For example, obj is a constant, but the value it points to is mutable – we can add a property to it:`

`const`는 변수가 항상 같은 값을 가지고 있다는 의미일 뿐, 값 자체가 불변이 되는 것은 아닙니다. 예제에서 `obj` 변수는 상수이지만 가리키는 값 객체는 불변이 아니며 속성을 추가할 수 있습니다.

```javascript
const obj = {};
obj.prop = 123;
console.log(obj.prop); // 123
```

`We cannot, however assign a different value to obj:`

하지만 `obj`변수에 다른 값을 할당할 수는 없습니다.

```javascript
obj = {}; // TypeError
```

`If you want the value of obj to be immutable, you have to take care of it, yourself, e.g. by freezing it:`

`obj`의 값이 불변하게 하려면, 동결이라던지 필요한 처리를 해주어야 합니다.

```javascript
const obj = Object.freeze({});
obj.prop = 123; // TypeError
```

#### 9.3.1.1 `Pitfall: Object.freeze() is shallow` 함정 : `Object.freeze()` 는 얕다.

`Keep in mind that Object.freeze() is shallow, it only freezes the properties of its argument, not the objects stored in its properties. For example, the object obj is frozen:`

`Object.freeze()`는 얕다는 것을 기억해야 합니다. 즉 `Object.freeze()`는 대상의 속성만을 동결할 뿐, 속성에 할당된 객체는 동결하지 않습니다. 아래 예제에서 `obj` 객체는 동결됩니다.

```javascript
const obj = Object.freeze({ foo: {} });
obj.bar = 123
// TypeError: 객체는 확장될 수 없으므로 bar 프로퍼티를 추가할 수 없다.
obj.foo = {}
// TypeError: 객체의 읽기 전용 프로퍼티 'foo'에 할당할 수 없다.
```

`But the object obj.foo is not.`

하지만, `obj.foo` 객체는 동결되지 않습니다.

```javascript
obj.foo.qux = 'abc';
obj.foo.qux
// 'abc'
```

### 9.3.2 `const in loop bodies` 루프 바디 안에서의 `const`

`Once a const variable has been created, it can’t be changed. But that doesn’t mean that you can’t re-enter its scope and start fresh, with a new value. For example, via a loop:`

한번 `const` 변수가 생성되고나면 값이 변경될 수 없습니다. 하지만 루프의 스코프에 재진입 했을때 새로운 값으로 갱신되지 않는 것은 아닙니다. 예제의 루프를 보겠습니다.

```javascript
function logArgs(...args) {
    for (const [index, elem] of args.entries()) {
        const message = index + '. ' + elem;
        console.log(message);
    }
}
logArgs('Hello', 'everyone');

// Output:
// 0. Hello
// 1. everyone
```

## 9.4 `The temporal dead zone` `TDZ` (The temporal dead zone)

`A variable declared by let or const has a so-called temporal dead zone (TDZ): When entering its scope, it can’t be accessed (got or set) until execution reaches the declaration. Let’s compare the life cycles of var-declared variables (which don’t have TDZs) and let-declared variables (which have TDZs).`

`let` 또는 `const`로 선언된 변수는 `TDZ` (temporal dead zone)로 불리는 것을 갖게 되며, 스코프에 진입하면 선언부에 도달하기 전까지 할당( `got` or `set` )을 할 수 없게 됩니다. `TDZ`를 갖지않는 `var` 선언과 `TDZ`를 갖는 `let`선언의 라이프 사이클을 비교해보겠습니다.

### 9.4.1 `The life cycle of var-declared variables` `var`로 선언된 변수의 라이프사이클

`var variables don’t have temporal dead zones. Their life cycle comprises the following steps:`

`var`로 선언된 변수는 `TDZ`를 갖지 않습니다. 라이프 사이클은 다음과 같은 단계로 이루어집니다.

1. `When the scope (its surrounding function) of a var variable is entered, storage space (a binding) is created for it. The variable is immediately initialized, by setting it to undefined.`  
  `var` 변수의 ( `function`으로 감싸진 ) 스코프 영역에 진입하면 변수를 위한 저장 공간( 바인딩 )이 생성되고 변수는 즉시 `undefined`로 초기화됩니다.
2. `When the execution within the scope reaches the declaration, the variable is set to the value specified by the initializer (an assignment) – if there is one. If there isn’t, the value of the variable remains undefined.`  
  변수는 선언부에 도달하면 지정( 할당 )된 값으로 설정됩니다. 또는 지정된 값이 없는 경우에 값은 여전히 `undefined`입니다.

### 9.4.2 `The life cycle of let-declared variables` `let`으로 선언된 변수의 라이프사이클

`Variables declared via let have temporal dead zones and their life cycles look like this:`

`let`으로 선언된 변수는 `TDZ`를 가지며 라이프 사이클은 다음과 같습니다.

1. `When the scope (its surrounding block) of a let variable is entered, storage space (a binding) is created for it. The variable remains uninitialized.`  
  `let` 변수의 블록으로 감싸진 스코프 영역에 진입하면 변수를 위한 저장 공간( 바인딩 )이 생성되고 값은 초기화되지 않습니다.
2. `Getting or setting an uninitialized variable causes a ReferenceError.`  
  초기화되지 않은 변수에 접근하면 `ReferenceError`가 발생합니다.
3. `When the execution within the scope reaches the declaration, the variable is set to the value specified by the initializer (an assignment) – if there is one. If there isn’t then the value of the variable is set to undefined.`  
  변수의 선언부에 도달하면 지정( 할당 )된 값으로 설정됩니다. 지정된 값이 없으면 값은 `undefined`로 설정됩니다.

`const variables work similarly to let variables, but they must have an initializer (i.e., be set to a value immediately) and can’t be changed.`

`const`변수도 `let`과 유사하게 동작하지만, 반드시 즉시 초기화( 값을 할당 )가 되어야 하고 할당된 값을 변경할 수 없습니다.

### 9.4.3 `Examples` 예제 

`Within a TDZ, an exception is thrown if a variable is got or set:`

`TDZ` 내에서 초기화 되지 않은 변수에 접근하면 예외가 발생합니다.

```javascript
let tmp = true;
if (true) { // 스코프 진입. TDZ 시작
    // 초가화되지 않은 tmp 변수의 바인딩은 생성되지 않았음.
    console.log(tmp); // ReferenceError

    let tmp; // TDZ 종료. tmp 변수는 undefined로 초기화 됨.
    console.log(tmp); // undefined

    tmp = 123;
    console.log(tmp); // 123
}
console.log(tmp); // true
```

`If there is an initializer then the TDZ ends after the assignment was made:`

초기화가 된 경우 변수에 값이 할당되고나서 `TDZ`는 종료됩니다.

```javascript
let foo = console.log(foo); // ReferenceError
```

`The following code demonstrates that the dead zone is really temporal (based on time) and not spatial (based on location):`

다음 예제는 `TDZ`가 실제로 일시적( 시간 기준 )이며, 공간( 위치 ) 기준이 아님을 보여줍니다.

```javascript
if (true) { // 새로운 스코프 진입. TDZ 시작
    const func = function () {
        console.log(myVar); // OK!
    };

    // TDZ 공간이며 myVar에 접근은 ReferenceError

    let myVar = 3; // TDZ 종료
    func(); // TDZ 밖에서 호출
}
```

### 9.4.4 `typeof throws a ReferenceError for a variable in the TDZ` `TDZ` 안에서 `typeof`를 사용시 `ReferenceError`가 발생하는 경우

`If you access a variable in the temporal dead zone via typeof, you get an exception:`

`TDZ` 안에서 `typeof`를 사용하여 변수에 접근하는 경우, 예외가 발생합니다.

```javascript
if (true) {
    console.log(typeof foo); // ReferenceError (TDZ)
    console.log(typeof aVariableThatDoesntExist); // 'undefined'
    let foo;
}
```

`Why? The rationale is as follows: foo is not undeclared, it is uninitialized. You should be aware of its existence, but aren’t. Therefore, being warned seems desirable.`

왜냐하면, 개발자가 `foo` 변수의 존재를 인지할 수는 있겠지만 이론적으로 `foo` 변수는 선언되지 않았고 초기화되지 않았기 때문입니다. 따라서 경고가 나타나는 것은 바람직합니다.

`Furthermore, this kind of check is only useful for conditionally creating global variables. That’s something that only advanced JavaScript programmers should do and it can only be achieved via var.`

또한 이런 류의 검사는 전역 변수의 생성 여부를 체크하는 경우에만 유용합니다. 주로 자바스크립트 숙련자들이 사용하는데 `var`를 사용할 때에만 유효합니다.

`There is a way to check whether a global variable exists that does not involve typeof:`

전역 변수의 존재 여부는 `typeof`를 사용하지 않고 검사할 수도 있습니다.

```javascript
// `typeof`로 검사
if (typeof someGlobal === 'undefined') {
    var someGlobal = { ··· };
}

// `typeof` 없이 검사
if (!('someGlobal' in window)) {
    window.someGlobal = { ··· };
}
```

`The former way of creating a global variable only works in global scope (and therefore not inside ES6 modules).`

전역 변수를 생성하는 전자의 방법은 글로벌 스코프에서만 작동합니다. `ES6`모듈 안에서는 가능하지 않습니다.

### 9.4.5 `Why is there a temporal dead zone?` `TDZ`가 필요한 이유

1. `To catch programming errors: Being able to access a variable before its declaration is strange. If you do so, it is normally by accident and you should be warned about it.`  
  프로그래밍 에러를 잡기위해 : 선언 전에 변수에 접근이 가능하다는 것은 이상합니다. 문제를 일으키기 쉬운 부분이기 때문에 경고를 해주는 것이 좋습니다.
2. `For const: Making const work properly is difficult. Quoting Allen Wirfs-Brock: “TDZs … provide a rational semantics for const. There was significant technical discussion of that topic and TDZs emerged as the best solution.” let also has a temporal dead zone so that switching between let and const doesn’t change behavior in unexpected ways.`  
  `const`를 위해 : `const`를 제대로 사용하는 것은 어렵습니다. Allen Wirfs-Brock의 말에 따르자면, "`TDZ`는 `const`를 위해 합리적인 의미론을 제공한다. 이 주제에 대한 중요한 기술적인 토론이 있었고 `TDZ`가 최고의 솔루션으로 채택되었다." `let` 또한 `TDZ`를 가지고 있기 때문에 `let`과 `const`를 서로 바꾸어 써도 예상치 못하게 동작하는 일은 없습니다.
3. `Future-proofing for guards: JavaScript may eventually have guards, a mechanism for enforcing at runtime that a variable has the correct value (think runtime type check). If the value of a variable is undefined before its declaration then that value may be in conflict with the guarantee given by its guard.`  
  추후 `guards`를 지원하기 위해 : 자바스크립트는 언젠가 런타임에 변수가 올바른 값을 가지고 있는지 검사( 타입 체크처럼 )하는 매커니즘인 `guards`를 가질 수도 있습니다. 변수가 선언되기 전에 이미 `undefined`값을 가지고 있다면 `guards`가 보장해 주는 결과와 충돌이 일어날 수 있습니다.

### 9.4.6 `Further reading` 더 읽을거리 

이 섹션에서 참고한 자료 `Sources of this section:`

- [“Performance concern with let/const”](https://esdiscuss.org/topic/performance-concern-with-let-const)
- [“Bug 3009 – typeof on TDZ variable”](https://bugs.ecmascript.org/show_bug.cgi?id=3009)

## 9.5 `let and const in loop heads` 루프 헤드에서의 `let` 과 `const`

`The following loops allow you to declare variables in their heads:`

다음 루프들에서는 변수를 루프의 헤드에 선언할 수 있습니다.

+ for
+ for-in
+ for-of

`To make a declaration, you can use either var, let or const. Each of them has a different effect, as I’ll explain next.`

변수를 선언할 때, `var`, `let` 또는 `const`를 사용할 수 있습니다. 각각의 다른 점을 설명하겠습니다.

### 9.5.1 for 루프 `for loop`

`var-declaring a variable in the head of a for loop creates a single binding (storage space) for that variable:`

`for` 루프의 헤드에서 선언한 `var`변수는 변수를 위한 단일 바인딩( 저장 공간 )을 생성합니다.

```javascript
const arr = [];
for (var i = 0; i < 3; i++) {
    arr.push(() => i);
}
arr.map(x => x()); // [3,3,3]
```

`Every i in the bodies of the three arrow functions refers to the same binding, which is why they all return the same value.`

바디안에서 세번 실행되는 화살표 함수의 모든 `i` 변수는 같은 바인딩을 가리키고 있습니다. 따라서 모든 반환값이 같게 됩니다.

`If you let-declare a variable, a new binding is created for each loop iteration:`

`let`으로 변수를 선언하면 `for` 루프의 반복마다 새로운 바인딩이 생성됩니다.

```javascript
const arr = [];
for (let i = 0; i < 3; i++) {
    arr.push(() => i);
}
arr.map(x => x()); // [0,1,2]
```

`This time, each i refers to the binding of one specific iteration and preserves the value that was current at that time. Therefore, each arrow function returns a different value.`

이번에는 `i`가 매 반복마다 바인딩되고, 바인딩 된 시점을 기준으로 값을 가집니다. 따라서 각 화살표 함수는 다른 값을 반환합니다.

`const works like var, but you can’t change the initial value of a const-declared variable:`

`const`는 `var`처럼 동작하지만, 선언 시점의 초기값을 변경할 수는 없습니다.

`Getting a fresh binding for each iteration may seem strange at first, but it is very useful whenever you use loops to create functions that refer to loop variables, as explained in a later section.`

반복마다 새로운 바인딩을 얻는 것이 처음엔 이상해 보일 수 있지만, 다음 예제처럼 루프 내에서 루프 변수를 참조하는 함수를 생성하는 경우에 매우 유용합니다.

> *:notebook: `for loop: per-iteration bindings in the spec` `for` 루프 반복마다 바인딩에 관한 스펙  
> `The evaluation of the for loop handles var as the second case and let/const as the third case. Only let-declared variables are added to the list perIterationLets (step 9), which is passed to ForBodyEvaluation() as the second-to-last parameter, perIterationBindings.`  
> `for` 루프의 평가는 `var`를 두번째로 처리하고 `let`, `const`를 세번째로 처리합니다. `let`으로 선언된 변수만이 `ForBodyEvaluation()`에 두번째 이후의 파라미터 `perIterationBindings`로 전달되는 `perIterationLets`( 9장 참고 ) 리스트에 추가됩니다.

### 9.5.2 `for-of loop and for-in loop` `for-of` 루프와 `for-in` 루프

`In a for-of loop, var creates a single binding:`

`for-of` 루프에서 `var`는 단일 바인딩을 생성합니다.

```javascript
const arr = [];
for (var i of [0, 1, 2]) {
    arr.push(() => i);
}
arr.map(x => x()); // [2,2,2]
```

`let creates one binding per iteration:`

`let`은 매 반복마다 바인딩을 생성합니다.

```javascript
const arr = [];
for (let i of [0, 1, 2]) {
    arr.push(() => i);
}
arr.map(x => x()); // [0,1,2]
```

`const also creates one binding per iteration, but the bindings it creates are immutable.`

`const` 또한 반복마다 하나의 바인딩을 생성하지만, 생성된 바인딩은 불변입니다.

`The for-in loop works similarly to the for-of loop.`

`for-in` 루프는 `for-of` 루프와 비슷하게 작동합니다.

> :notebook: `for-of loop: per-iteration bindings in the spec` `for-of` 루프 반복마다 바인딩되는 스펙  
> `Per-iteration bindings in for-of are handled by ForIn/OfBodyEvaluation. In step 5.b, a new environment is created and bindings are added to it via BindingInstantiation (mutable for let, immutable for const). The current iteration value is stored in the variable nextValue and used to initialize the bindings in either one of two ways:`  
> `for-of`에서 반복마다 바인딩은 `ForIn/OfBodyEvaluation`에서 제어합니다. 5.b장을 보면, `BindingInstantiation`( 가변 `let`, 불변 `const` )을 통해서 새로운 환경이 생성되고 바인딩이 추가됩니다. 현재 반복 변수가 `nextValue` 변수에 저장되고 둘 중 하나의 방법으로 바인딩 초기화에 사용됩니다.  
> + `Declaration of single variable (step 5.h.i): is handled via InitializeReferencedBinding`  
> + 하나의 변수 선언(5.h.i장)은 `InitializeReferencedBinding`을 통해 제어됩니다.  
> + `Destructuring (step 5.i.iii): is handled via one case of BindingInitialization (ForDeclaration), which invokes another case of BindingInitialization (BindingPattern).`  
> + 해체(5.i.iii장)는 `BindingInitialization` (`BindingPattern`)라는 다른 케이스를 초기화하는 `BindingInitialization`(`ForDeclaration`)에서 하나의 케이스로 처리됩니다.

### 9.5.3 `Why are per-iteration bindings useful?` 왜 반복 마다 바인딩은 유용할까?

`The following is an HTML page that displays three links:`

다음은 세 개의 링크를 보여주는 `HTML`페이지입니다.

1. `If you click on “yes”, it is translated to “ja”.`  
  yes를 클릭하면 일본어(ja)로 번역됩니다.  
2. `If you click on “no”, it is translated to “nein”.`  
  no를 클릭하면 러시아어(nein)로 번역됩니다.  
3. `If you click on “perhaps”, it is translated to “vielleicht”.`  
  perhaps를 클릭하면 독일어(vielleicht)로 번역됩니다.  

```html
<!doctype html>
<html>
<head>
    <meta charset="UTF-8">
</head>
<body>
    <div id="content"></div>
    <script>
        const entries = [
            ['yes', 'ja'],
            ['no', 'nein'],
            ['perhaps', 'vielleicht'],
        ];
        const content = document.getElementById('content');
        for (let [source, target] of entries) { // (A)
            content.insertAdjacentHTML('beforeend',
                `<div><a id="${source}" href="">${source}</a></div>`);
            document.getElementById(source).addEventListener(
                'click', (event) => {
                    event.preventDefault();
                    alert(target); // (B)
                });
        }
    </script>
</body>
</html>
```

`What is displayed depends on the variable target (line B). If we were to use var instead of let in line (A), there would be a single binding for the whole loop and target would have the value 'vielleicht', afterwards. Therefore, no matter what link you click on, you would always get the translation 'vielleicht'.`

무엇이 보여질지는 B라인에 있는 `target`변수에 따라 다릅니다. 만약 A라인에서 `let` 대신 `var`를 사용했다면, 모든 루프 반복에서 단일 바인딩을 사용했을 것이고 `target`의 값은 결국 `vielleicht`가 되었을 것입니다. 따라서 어떤 링크를 클릭하더라도 항상 독일어로 번역이 되었을 것입니다.

`Thankfully, with let, we get one binding per loop iteration and the translations are displayed correctly.`

고맙게도 `let`은 루프 반복마다 하나의 바인딩을 가지기 때문에 코드는 제대로 동작합니다.

## 9.6 `Parameters` 파라미터

### 9.6.1 `Parameters versus local variables` 파라미터 와 로컬 변수

`If you let-declare a variable that has the same name as a parameter, you get a static (load-time) error:`

만약 `let`으로 선언한 변수가 파라미터와 같은 이름으로 선언된다면 정적 에러(load-time)가 발생합니다.

```javascript
function func(arg) {
    let arg; // static error: duplicate declaration of `arg`
}
```

`Doing the same inside a block shadows the parameter:`

블록 안에서 같은 이름으로 선언하면 파라미터는 가려집니다.

```javascript
function func(arg) {
    {
        let arg; // arg 파라미터는 가려진다.
    }
}
```

`In contrast, var-declaring a variable that has the same name as a parameter does nothing, just like re-declaring a var variable within the same scope does nothing.`

반면, `var`로 선언한 변수가 파라미터와 같은 이름으로 선언되면 아무 일도 일어나지 않습니다. 함수 스코프 내에서 `var`변수를 재선언한 것과 같습니다.

```javascript
function func(arg) {
    var arg; // 아무일도 일어나지 않는다.
}

function func(arg) {
    {
        // arg는 여전히 같은 var 스코프에 존재.
        var arg; // 아무일도 일어나지 않는다.
    }
}
```

### 9.6.2 `Parameter default values and the temporal dead zone` 파라미터 기본값과 TDZ

`If parameters have default values, they are treated like a sequence of let statements and are subject to temporal dead zones:`

파라미터가 기본값을 가지는 경우, `let` 선언의 연속처럼 처리되고 `TDZ`를 가지게 됩니다.

```javascript
// 정상: y는 x가 선언된 이후에 x에 접근하고 있다.
function foo(x=1, y=x) {
    return [x, y];
}
foo(); // [1,1]

// 에러: x가 TDZ안에서 y에 접근하고 있다.
function bar(x=y, y=2) {
    return [x, y];
}
bar(); // ReferenceError
```

### 9.6.3 `Parameter default values don’t see the scope of the body` 파라미터의 기본값은 바디의 스코프를 알지 못한다.

`The scope of parameter default values is separate from the scope of the body (the former surrounds the latter). That means that methods or functions defined “inside” parameter default values don’t see the local variables of the body:`

파라미터의 기본값 스코프는 바디의 스코프와는 다릅니다. ( 전자가 후자를 감쌉니다. ) 파라미터의 기본값으로 정의된 메소드나 함수는 바디 내의 지역 변수를 참조할 수 없습니다.

```javascript
const foo = 'outer';
function bar(func = x => foo) {
    const foo = 'inner';
    console.log(func()); // outer
}
bar();
```

## 9.7 `The global object` 전역 객체

`JavaScript’s global object (window in web browsers, global in Node.js) is more a bug than a feature, especially with regard to performance. That’s why it makes sense that ES6 introduces a distinction:`

자바스크립트에서의 전역 객체( 웹 브라우저의 `window`, Node.js의 `global` )는 특히 성능적인 면에서는 기능이라기 보다 버그에 가깝습니다. 따라서 `ES6`에서는 다음과 같은 구분을 도입하였습니다.

+ 모든 전역 객체의 속성은 전역 변수입니다. 전역 스코프에서 아래 선언들은 전역 객체의 속성을 생성합니다.  
  `All properties of the global object are global variables. In global scope, the following declarations create such properties:`
 + `var` 선언  
   `var declarations`
 + `Function` 선언  
   `Function declarations`
+ 하지만 이제 전역 객체의 속성이 아닌 전역 변수도 있습니다. 전역 스코프에서 아래 선언들이 그렇습니다.  
  `But there are now also global variables that are not properties of the global object. In global scope, the following declarations create such variables:`
 + `let` 선언  
   `let declarations`
 + `const` 선언  
   `const declarations`
 + `Class` 선언  
   `Class declarations`

## 9.8 함수 선언과 클래스 선언 `Function declarations and class declarations`

함수 선언은...

`Function declarations…`

+ `let`처럼 블록 스코프를 가집니다.  
  `are block-scoped, like let.`
+ `var`처럼 ( 전역 스코프에서 )전역 객체에 속성을 생성합니다.  
  `create properties on the global object (while in global scope), like var.`
+ 호이스팅 됩니다. 함수 스코프 내의 어느 위치에서 선언이 되었던 스코프의 시작점에서 생성됩니다.    
  `are hoisted: independently of where a function declaration is mentioned in its scope, it is always created at the beginning of the scope.`

함수 선언의 호이스팅 예제입니다.

`The following code demonstrates the hoisting of function declarations:`

```javascript
{ // 새로운 스코프 진입

    console.log(foo()); // 호이스팅이 되기 때문에 정상.
    function foo() {
        return 'hello';
    }
}
```
클래스 선언은...

`Class declarations…`

+ 블록 스코프를 가집니다.  
  `are block-scoped.`
+ 전역 객체의 속성을 생성하지 않습니다.  
  `don’t create properties on the global object.`
+ 호이스팅되지 않습니다.  
  `are not hoisted.`

클래스가 호이스팅되지 않는다는 것이 의아해 보일 수도 있습니다. 클래스가 생성하는 것이 함수이기 때문입니다. 호이스팅되지 않는 이유는 `extends` 절의 값들이 이미 선언되고 실행되어 있어야 하기 때문입니다.

`Classes not being hoisted may be surprising, because, under the hood, they create functions. The rationale for this behavior is that the values of their extends clauses are defined via expressions and those expressions have to be executed at the appropriate times.`

```javascript
{ // 새로운 스코프 진입

    const identity = x => x;

    // MyClass는 TDZ에 있다.
    const inst = new MyClass(); // ReferenceError

    // extend 절이 있다.
    class MyClass extends identity(Object) {
    }
}
```

## 9.9 코딩 스타일 : `const` 대 `let` 대 `var` `Coding style: const versus let versus var`

항상 `let`과 `const`중 하나를 사용할 것을 추천합니다.

`I recommend to always use either let or const:`

1. 먼저 `const`가 우선입니다. 값이 변경되지 않는 모든 곳에 사용할 수 있습니다. 다시 말해 변수가 절대 할당문의 좌변이 되지 않거나 `++` 또는 `--` 의 피연산자가 아닐 경우입니다. `const` 변수가 가리키는 객체는 변경될 수 있습니다.  
  `Prefer const. You can use it whenever a variable never changes its value. In other words: the variable should never be the left-hand side of an assignment or the operand of ++ or --. Changing an object that a const variable refers to is allowed:` 

  ```javascript
  const foo = {};
  ```

  루프 반복마다 하나의 ( 불변 )바인딩이 생성되기 때문에 `for-of` 루프에서도 사용할 수 있습니다.

  `You can even use const in a for-of loop, because one (immutable) binding is created per loop iteration:`

  ```javascript
  for (const x of ['a', 'b']) {
      console.log(x);
  }
  // Output:
  // a
  // b
  ```

  `for-of` 루프의 바디 안에서는 변수 `x`를 변경할 수 없습니다.

  `Inside the body of the for-of loop, x can’t changed.`

2. 그 외에, 변수의 초기값이 나중에 변경된다면 `let`을 사용합니다.  
  `Otherwise, use let – when the initial value of a variable changes later on.`

  ```javascript
  let counter = 0; // 초기값
  counter++; // 값을 변경
  
  let obj = {}; // 초기값
  obj = { foo: 123 }; // 값을 변경
  ```

3. `var`는 사용하지 않습니다.  
  `Avoid var.`

이 규칙을 따르면 `var`는 리팩토링이 필요한 래거시 코드에서만 볼 수 있게됩니다.

`If you follow these rules, var will only appear in legacy code, as a signal that careful refactoring is required.`

`var`는 `let`과 `const`가 하지 않는 전역 객체의 속성이 되는 일을 해주지만 이것은 일반적으로 좋지 않습니다. 필요하다면 `window` 또는 `global`에 속성을 할당을 해도 효과는 같습니다.

`var does one thing that let and const don’t: variables declared via it become properties of the global object. However, that’s generally not a good thing. You can achieve the same effect by assigning to window (in browsers) or global (in Node.js).`

### 9.9.1 또 다른 스타일. `An alternative approach`

앞에 언급한 코딩 스타일 규칙 뿐 아니라 완전히 불변인 값들( 원시값과 동결된 객체들 )에만 `const`를 사용하는 방법도 있습니다. 두 가지 방법은.

`An alternative to the just mentioned style rules is to use const only for things that are completely immutable (primitive values and frozen objects). Then we have two approaches:`

1. `const` 우선 (최선) : `const`를 불변 바인딩에 사용합니다.  
  `Prefer const (recommended): const marks immutable bindings`
2. `let` 우선 (차선) : `const`을 불변값에 사용합니다.  
  `Prefer let(alternative): const marks immutable values`

차선으로도 충분하지만 최선을 사용하는 것을 추천합니다.

`#2 is perfectly acceptable; I only lean slightly in favor of #1.`