#  9. 변수와 스코프 `Variables and scoping`

##  9.1 개요

ES6에서는 변수를 선언하는 두가지 새로운 방법을 제공합니다. ES5에서 변수를 선언하는 방법인 `var`를 대체할 수 있는 `let`과 `const`입니다.

`ES6 provides two new ways of declaring variables: let and const, which mostly replace the ES5 way of declaring variables, var.`

###  9.1.1 let

`let`은 `var`와 비슷하게 동작하지만, `let`으로 선언한 변수는 선언된 블럭 내에서만 존재하는 블럭 스코프입니다. `var`는 함수 스코프입니다.

`let works similarly to var, but the variable it declares is block-scoped, it only exists within the current block. var is function-scoped.`

다음 코드에서 `let`으로 선언된 변수 `tmp`는 `A`라인으로 시작된 블록 내에서만 존재하는 것을 볼 수 있습니다.

`In the following code, you can see that the let-declared variable tmp only exists with the block that starts in line A:`

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

`const`는 `let`처럼 동작하지만, `const`로 선언한 변수는 즉시 초기화되어야 합니다. 초기화된 이후에 값을 변경할 수 없습니다.

`const works like let, but the variable you declare must be immediately initialized, with a value that can’t be changed afterwards.`

```javascript
const foo; // SyntaxError: missing = in const declaration

const bar = 123;
bar = 456; // TypeError: `bar` is read-only
```

`for-of` 반복문이 루프 반복마다 하나의 바인딩 ( 변수의 저장 공간 )을 생성하기 때문에 루프 변수를 const로 선언하는 것도 가능합니다.

`Since for-of creates one binding (storage space for a variable) per loop iteration, it is OK to const-declare the loop variable:`

```javascript
for (const x of ['a', 'b']) {
    console.log(x);
}
// Output:
// a
// b
```

###  9.1.3 변수 선언 방법들 `Ways of declaring variables`

아래 표는 ES6에서 변수가 선언되는 6가지 방법을 보여줍니다.

`The following table gives an overview of six ways in which variables can be declared in ES6:`

| | Hoisting | Scope | Creates global properties |
| -------- | ----- | ------- | ------ | ---------- |
| var | Declaration | Function | Yes |
| let | Temporal dead zone | Block | No |
| const | Temporal dead zone | Block | No |
| function | Complete | Block | Yes |
| class | No | Block | No |
| import | Complete | Module-global | No |

##  9.2 let 과 const 를 통한 블럭 스코핑 `Block scoping via let and const`

`let`과 `const`는 둘 다 둘러싼 가장 가까운 블록 내에서만 존재하는 블럭 스코프 변수를 생성합니다. 아래 예제에서 `const`로 선언된 변수 `tmp`가 `if`문의 블럭 안에서만 존재하는 것을 볼 수 있습니다.

`Both let and const create variables that are block-scoped – they only exist within the innermost block that surrounds them. The following code demonstrates that the const-declared variable tmp only exists inside the then-block of the if statement:`

```javascript
function func() {
    if (true) {
        const tmp = 123;
    }
    console.log(tmp); // ReferenceError: tmp is not defined
}
```

반면, `var`로 선언된 변수는 함수 스코프를 가집니다.

`In contrast, var-declared variables are function-scoped:`

```javascript
function func() {
    if (true) {
        var tmp = 123;
    }
    console.log(tmp); // 123
}
```

블럭 스코프는 함수 내에서 변수를 가릴 수 있습니다.

`Block scoping means that you can shadow variables within a function:`

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

## 9.3 `const` 는 불변 (immutable) 변수를 생성한다 `const creates immutable variables`

`let`으로 생성한 변수는 변경 가능합니다.

`Variables created by let are mutable:`

```javascript
let foo = 'abc';
foo = 'def';
console.log(foo); // def
```

`const`로 생성된 변수는 상수로서 다른 값을 할당할 수 없으며 불변입니다.

`Constants, variables created by const, are immutable – you can’t assign them a different value:`

```javascript
const foo = 'abc';
foo = 'def'; // TypeError
```

> :notebook: 스펙에 따르면 `const` 변수의 값을 변경하는 것은 항상 `TypeError`가 발생합니다.  
> `Spec detail: changing a const variable always throws a TypeError`  
> 일반적으로, 불변 바인딩을 변경하는 것은 `strict mode`에서만 `SetMutableBinding()`[^1]에서 예외가 발생합니다. 하지만 `const`로 변수를 선언하면 언제나 엄격한( strict )바인딩을 생성합니다. - 35.b.i.1 장의 FunctionDeclarationInstantiation(func, argumentsList)[^2]를 확인하세요.  
> `Normally, changing an immutable binding only causes an exception in strict mode, as per SetMutableBinding(). But const-declared variables always produce strict bindings – see FunctionDeclarationInstantiation(func, argumentsList), step 35.b.i.1.`

[^1]: http://www.ecma-international.org/ecma-262/6.0/#sec-declarative-environment-records-setmutablebinding-n-v-s
[^2]: http://www.ecma-international.org/ecma-262/6.0/#sec-functiondeclarationinstantiation

### 9.3.1 함정 : `const`는 값을 불변으로 만들지 않는다. `Pitfall: const does not make the value immutable`

`const`는 변수가 항상 같은 값을 가지고 있다는 의미일 뿐, 값 자체가 불변이 되는 것은 아닙니다. 예제에서 `obj` 변수는 상수이지만 가리키는 값 객체는 불변이 아니며 속성을 추가할 수 있습니다.

`const only means that a variable always has the same value, but it does not mean that the value itself is or becomes immutable. For example, obj is a constant, but the value it points to is mutable – we can add a property to it:`

```javascript
const obj = {};
obj.prop = 123;
console.log(obj.prop); // 123
```

하지만 `obj`변수에 다른 값을 할당할 수는 없습니다.

`We cannot, however assign a different value to obj:`

```javascript
obj = {}; // TypeError
```

`obj`의 값이 불변하게 하려면, 동결이라던지 필요한 처리를 해주어야 합니다.

`If you want the value of obj to be immutable, you have to take care of it, yourself, e.g. by freezing it:`

```javascript
const obj = Object.freeze({});
obj.prop = 123; // TypeError
```

#### 9.3.1.1 함정 : `Object.freeze()` 는 얕다. `Pitfall: Object.freeze() is shallow`

`Object.freeze()`는 얕다는 것을 기억해야 합니다. `Object.freeze()`는 속성만을 동결하고, 속성에 할당된 객체는 동결하지 않습니다. 예제에서 `obj` 객체는 동결됩니다.

`Keep in mind that Object.freeze() is shallow, it only freezes the properties of its argument, not the objects stored in its properties. For example, the object obj is frozen:`

```javascript
const obj = Object.freeze({ foo: {} });
obj.bar = 123
// TypeError: Can't add property bar, object is not extensible
obj.foo = {}
// TypeError: Cannot assign to read only property 'foo' of #<Object>
```

하지만, `obj.foo` 객체는 동결되지 않습니다.

`But the object obj.foo is not.`

```javascript
obj.foo.qux = 'abc';
obj.foo.qux
// 'abc'
```

### 9.3.2 반복문 안에서의 `const` `const in loop bodies`

한번 `const` 변수가 생성되면 값이 변경될 수 없습니다. 하지만 반복문의 스코프에 재진입 했을때 새로운 값으로 갱신되지 않는 것은 아닙니다. 예제의 반복문을 보겠습니다.

`Once a const variable has been created, it can’t be changed. But that doesn’t mean that you can’t re-enter its scope and start fresh, with a new value. For example, via a loop:`

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

## 9.4 `TDZ` (The temporal dead zone) `The temporal dead zone`

`let` 또는 `const`로 선언된 변수는 `TDZ` (temporal dead zone)로 불리는 것을 갖게 되며, 스코프에 진입하면 선언부에 도달하기 전까지 할당( `got` or `set` )을 할 수 없게 됩니다. `TDZ`를 갖지않는 `var` 선언과 `TDZ`를 갖는 `let`선언의 라이프사이클을 비교해보겠습니다.

`A variable declared by let or const has a so-called temporal dead zone (TDZ): When entering its scope, it can’t be accessed (got or set) until execution reaches the declaration. Let’s compare the life cycles of var-declared variables (which don’t have TDZs) and let-declared variables (which have TDZs).`

### 9.4.1 `var`로 선언된 변수의 라이프사이클 `The life cycle of var-declared variables`

`var`로 선언된 변수는 `TDZ`를 갖지 않습니다. 라이프 사이클은 다음과 같은 단계로 이루어집니다.

`var variables don’t have temporal dead zones. Their life cycle comprises the following steps:`

1. `var` 변수의 `function`으로 감싸진 스코프 영역에 진입하면 저장 공간( 스코프 )이 생성되고 변수는 즉시 `undefined`로 초기화됩니다.
  `When the scope (its surrounding function) of a var variable is entered, storage space (a binding) is created for it. The variable is immediately initialized, by setting it to undefined.`
2. 스코프 내에서 변수의 선언부에 도달하면 지정( 할당 )된 값으로 설정됩니다. 또는 지정된 값이 없는 경우에는 값은 여전히 `undefined`입니다.
   `When the execution within the scope reaches the declaration, the variable is set to the value specified by the initializer (an assignment) – if there is one. If there isn’t, the value of the variable remains undefined.`

### 9.4.2 `let`으로 선언된 변수의 라이프사이클 `The life cycle of let-declared variables`

`let`으로 선언된 변수는 `TDZ`를 가지며 라이프 사이클은 다음과 같습니다.

`Variables declared via let have temporal dead zones and their life cycles look like this:`

1. `let` 변수의 블록으로 감싸진 스코프 영역에 진입하면 저장 공간( 스코프 )이 생성되고 값은 초기화되지 않습니다.
   `When the scope (its surrounding block) of a let variable is entered, storage space (a binding) is created for it. The variable remains uninitialized.`
2.  초기화되지 않은 변수에 접근하면 `ReferenceError`가 발생합니다.
   `Getting or setting an uninitialized variable causes a ReferenceError.`
3. 스코프 내에서 변수의 선언부에 도달하면 지정( 할당 )된 값으로 설정됩니다. 지정된 값이 없으면 값은 `undefined`로 설정됩니다.
   `When the execution within the scope reaches the declaration, the variable is set to the value specified by the initializer (an assignment) – if there is one. If there isn’t then the value of the variable is set to undefined.`

const 변수도 let 변수와 비슷한 동작을 한다. 그러나 반드시 이니셜라이저를 가져야 하고 (예를 들면 즉시 값 설정이 되어야 한다는 뜻이다) 변경할 수 없다.

`const`변수도 `let`과 유사하게 동작하지만, 반드시 즉시 초기화가 되어야 하고 값을 변경할 수 없습니다.

`const variables work similarly to let variables, but they must have an initializer (i.e., be set to a value immediately) and can’t be changed.`

### 9.4.3 예제 `Examples`

`TDZ` 내에서 초기화 되지 않은 변수에 접근하면 예외가 발생합니다.

`Within a TDZ, an exception is thrown if a variable is got or set:`

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

초기화가 있는 경우 변수에 값이 할당되고나서 TDZ는 종료됩니다.

`If there is an initializer then the TDZ ends after the assignment was made:`

```javascript
let foo = console.log(foo); // ReferenceError
```

다음 코드는 `TMZ`가 일시적( 시간 기준 )이며, 공간( 위치 ) 기준이 아님을 보여줍니다.

`The following code demonstrates that the dead zone is really temporal (based on time) and not spatial (based on location):`

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

### 9.4.4 `TDZ` 안에서 typeof를 사용시 ReferenceError가 발생하는 경우

`typeof throws a ReferenceError for a variable in the TDZ`

`TDZ` 안에서 `typeof`를 사용하여 변수에 접근하는 경우, 예외가 발생합니다.

`If you access a variable in the temporal dead zone via typeof, you get an exception:`

```javascript
if (true) {
    console.log(typeof foo); // ReferenceError (TDZ)
    console.log(typeof aVariableThatDoesntExist); // 'undefined'
    let foo;
}
```

왜냐하면, 개발자가 `foo`의 존재를 인지할 수는 있지만 이론적으로 `foo` 변수는 선언되지 않았고 초기화되지 않았기 때문입니다. 따라서 경고가 나타나는 것이 맞습니다.

`Why? The rationale is as follows: foo is not undeclared, it is uninitialized. You should be aware of its existence, but aren’t. Therefore, being warned seems desirable.`

또한 이런 류의 체크는 전역 변수의 생성 여부를 체크하는 경우에만 유용합니다. 자바스크립트 숙련자만들은 이렇게 사용하고 있고 `var`를 사용할 때만 사용할 수 있습니다.

`Furthermore, this kind of check is only useful for conditionally creating global variables. That’s something that only advanced JavaScript programmers should do and it can only be achieved via var.`

전역 변수의 존재 여부를 `typeof`를 사용하지 않고 체크하는 방법도 있습니다.

`There is a way to check whether a global variable exists that does not involve typeof:`

```javascript
// With `typeof`
if (typeof someGlobal === 'undefined') {
    var someGlobal = { ··· };
}

// Without `typeof`
if (!('someGlobal' in window)) {
    window.someGlobal = { ··· };
}
```

전역 변수를 생성하는 전자의 방법은 글로벌 스코프에서만 작동합니다. 따라서, `ES6`에서는 가능하지 않습니다.

`The former way of creating a global variable only works in global scope (and therefore not inside ES6 modules).`

### 9.4.5 `TDZ`가 필요한 이유 `Why is there a temporal dead zone?`

1. 프로그래밍 오류를 잡기위해 : 선언 전에 변수에 접근이 가능하다는 것은 이상합니다. 문제를 일으킬기 쉬운 부분이기 때문에 경고를 해주어야 합니다.
  `To catch programming errors: Being able to access a variable before its declaration is strange. If you do so, it is normally by accident and you should be warned about it.`
2. `const` : `const`를 제대로 사용하는 것은 어렵습니다. Allen Wirfs-Brock의 말에 따르자면, "TDZ는 `const`를 위해 합리적인 의미를 제공한다. 중요한 기술적인 토론이 있었고 TDZ가 최고의 솔루션으로 채택되었다." `let` 또한 `TDZ`를 가지고 있기 때문에 `let`과 `const`를 서로 교체하는 것은 예상치 못한 문제가 발생하는 것을 예방한다.
  `For const: Making const work properly is difficult. Quoting Allen Wirfs-Brock: “TDZs … provide a rational semantics for const. There was significant technical discussion of that topic and TDZs emerged as the best solution.” let also has a temporal dead zone so that switching between let and const doesn’t change behavior in unexpected ways.`
3. 방어를 위한 교정 : 자바스크립트는 결국 런타임에 변수가 올바른 값( 타입 체크를 통해서 )을 가지고있는지 방어할 수도 있습니다. 변수의 값이 선언되기 전에 `undefined`라면 값은 방어를 통해 얻은 인증과 충돌할 수 있습니다.
  `Future-proofing for guards: JavaScript may eventually have guards, a mechanism for enforcing at runtime that a variable has the correct value (think runtime type check). If the value of a variable is undefined before its declaration then that value may be in conflict with the guarantee given by its guard.`

### 9.4.6 더 읽을거리 `Further reading`

이 섹션에서 참고한 자료 `Sources of this section:`

- [“Performance concern with let/const”](https://esdiscuss.org/topic/performance-concern-with-let-const)
- [“Bug 3009 – typeof on TDZ variable”](https://bugs.ecmascript.org/show_bug.cgi?id=3009)

## 9.5 루프 헤드 안에서의 `let` 과 `const` `let and const in loop heads`

다음 루프들에서는 변수를 루프의 헤드에 선언할 수 있습니다.

`The following loops allow you to declare variables in their heads:`

+ for
+ for-in
+ for-of

변수를 선언할 때, `var`, `let` 또는 `const`를 사용할 수 있습니다. 각각은 다른 효과를 가지는데 뒷 부분에서 다루겠습니다.

`To make a declaration, you can use either var, let or const. Each of them has a different effect, as I’ll explain next.`

### 9.5.1 for loop `for loop`

`for` 루프의 헤드에서 선언한 변수는 변수를 위한 하나의 바인딩( 저장 공간 )을 생성합니다.

`var-declaring a variable in the head of a for loop creates a single binding (storage space) for that variable:`

```javascript
const arr = [];
for (var i = 0; i < 3; i++) {
    arr.push(() => i);
}
arr.map(x => x()); // [3,3,3]
```

바디안에서 세개의 화살표 함수가 참조하고있는 모든 `i` 변수는 하나의 바인딩을 참조하고 있습니다. 그렇기 때문에 모든 반환값이 같게 됩니다.

`Every i in the bodies of the three arrow functions refers to the same binding, which is why they all return the same value.`

만일 let으로 변수를 선언하면, 새로운 바인딩이 루프 이터레이션마다 생성된다.

`let`으로 변수를 선언하면 `for` 루프의 반목마다 새로운 바인딩이 생성됩니다.

`If you let-declare a variable, a new binding is created for each loop iteration:`

```javascript
const arr = [];
for (let i = 0; i < 3; i++) {
    arr.push(() => i);
}
arr.map(x => x()); // [0,1,2]
```

이제, `i`는 반복마다 한번씩 바인딩되고, 바인딩 된 시점을 기준으로 값을 가집니다. 따라서 각 화살표 함수는 다른 값을 반환합니다.

`This time, each i refers to the binding of one specific iteration and preserves the value that was current at that time. Therefore, each arrow function returns a different value.`

`const`는 `var`처럼 동작하지만, 선언 시점에 초기화된 값을 변경할 수 없습니다.

`const works like var, but you can’t change the initial value of a const-declared variable:`

반복마다 새로운 바인딩을 얻는 것이 처음엔 이상해 보일 수 있지만, 다음 예처럼 루프 내에서 루프 변수를 참조하는 함수를 생성하는 경우에 매우 유용합니다.

`Getting a fresh binding for each iteration may seem strange at first, but it is very useful whenever you use loops to create functions that refer to loop variables, as explained in a later section.`

> *:notebook: for loop: 반복마다 바인딩에 관한 스펙 `for loop: per-iteration bindings in the spec`  
> `for` 루프의 평가는 `var`를 두번째로 처리하고 `let`, `const`를 세번째로 처리합니다. `let`으로 선언된 변수만이 `ForBodyEvaluation()`에 두번째 이후의 파라미터 `perIterationBindings`로 전달되는 `perIterationLets`( 9장 참고 ) 리스트에 추가됩니다.  
> `The evaluation of the for loop handles var as the second case and let/const as the third case. Only let-declared variables are added to the list perIterationLets (step 9), which is passed to ForBodyEvaluation() as the second-to-last parameter, perIterationBindings.`

### 9.5.2 for-of loop 와 for-in loop `for-of loop and for-in loop`

`for-of` 루프에서 `var`는 하나의 바인딩을 생성합니다.

`In a for-of loop, var creates a single binding:`

```javascript
const arr = [];
for (var i of [0, 1, 2]) {
    arr.push(() => i);
}
arr.map(x => x()); // [2,2,2]
```

`let`은 반복마다 바인딩을 하나씩 생성합니다.

`let creates one binding per iteration:`

```javascript
const arr = [];
for (let i of [0, 1, 2]) {
    arr.push(() => i);
}
arr.map(x => x()); // [0,1,2]
```

`const` 또한 반복마다 하나의 바인딩을 생성하지만, 생성된 바인딩은 불변입니다.

`const also creates one binding per iteration, but the bindings it creates are immutable.`

`for-in` 루프는 `for-of` 루프와 비슷하게 작동합니다.

`The for-in loop works similarly to the for-of loop.`

> :notebook: for-of loop: 반복마다 바인딩되는 스펙 `for-of loop: per-iteration bindings in the spec`  
> `for-of`에서 반복마다 바인딩은 `ForIn/OfBodyEvaluation`에서 제어합니다. 5.b장을 보면, `BindingInstantiation`( 가변 `let`, 불변 `const` )을 통해서 새로운 환경이 생성되고 바인딩이 추가됩니다. 현재 반복 변수가 `nextValue` 변수에 저장되고 둘 중 하나의 방법으로 바인딩 초기화에 사용됩니다.  
> `Per-iteration bindings in for-of are handled by ForIn/OfBodyEvaluation. In step 5.b, a new environment is created and bindings are added to it via BindingInstantiation (mutable for let, immutable for const). The current iteration value is stored in the variable nextValue and used to initialize the bindings in either one of two ways:`
> + 하나의 변수 선언(5.h.i장)은 `InitializeReferencedBinding`을 통해 제어됩니다.  
> + `Declaration of single variable (step 5.h.i): is handled via InitializeReferencedBinding`  
> + 해체(5.i.iii장)는 `BindingInitialization` (`BindingPattern`)라는 다른 케이스를 초기화하는 `BindingInitialization`(`ForDeclaration`)에서 하나의 케이스로 처리됩니다.  
> + `Destructuring (step 5.i.iii): is handled via one case of BindingInitialization (ForDeclaration), which invokes another case of BindingInitialization (BindingPattern).`

### 9.5.3 왜 반복 마다 바인딩은 유용할까? `Why are per-iteration bindings useful?`

다음은 세 개의 링크를 보여주는 `HTML`페이지입니다.

`The following is an HTML page that displays three links:`

1. yes를 클릭하면 일본어(ja)로 번역됩니다.  
  `If you click on “yes”, it is translated to “ja”.`
2. no를 클릭하면 러시아어(nein)로 번역됩니다.  
  `If you click on “no”, it is translated to “nein”.`
3. perhaps를 클릭하면 독일어(vielleicht)로 번역됩니다.  
  `If you click on “perhaps”, it is translated to “vielleicht”.`

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

무엇이 보여질지는 B라인에 있는 `target`변수에 따라 다릅니다. A라인에서 `let` 대신 `var`를 사용했다면, 모든 루프 반복에서 하나의 바인딩을 사용했을 것이고 `target`의 값은 `vielleicht`가 되었을 것입니다. 그래서 어떤 링크를 클릭하더라도 항상 독일어로 번역이 되었을 것입니다.

`What is displayed depends on the variable target (line B). If we were to use var instead of let in line (A), there would be a single binding for the whole loop and target would have the value 'vielleicht', afterwards. Therefore, no matter what link you click on, you would always get the translation 'vielleicht'.`

고맙게도 `let`은 루프 반복마다 하나의 바인딩을 가지기 때문에 코드는 제대로 동작합니다.

`Thankfully, with let, we get one binding per loop iteration and the translations are displayed correctly.`

## 9.6 파라미터 `Parameters`

### 9.6.1 파라미터 와 로컬 변수 `Parameters versus local variables`

만약 `let`으로 선언한 변수가 파라미터와 같은 이름으로 선언되면 정적 오류(load-time)가 발생합니다.

`If you let-declare a variable that has the same name as a parameter, you get a static (load-time) error:`

```javascript
function func(arg) {
    let arg; // static error: duplicate declaration of `arg`
}
```

블록 안에서 같은 이름으로 선언하면 파라미터는 가려집니다.

`Doing the same inside a block shadows the parameter:`

```javascript
function func(arg) {
    {
        let arg; // shadows parameter `arg`
    }
}
```

반면, `var`로 선언한 변수가 파라미터와 같은 이름으로 선언되면 아무 일도 일어나지 않습니다. 함수 스코프 내에서 `var`변수를 재선언한 것과 같습니다.

`In contrast, var-declaring a variable that has the same name as a parameter does nothing, just like re-declaring a var variable within the same scope does nothing.`

```javascript
function func(arg) {
    var arg; // does nothing
}

function func(arg) {
    {
        // We are still in same `var` scope as `arg`
        var arg; // does nothing
    }
}
```

### 9.6.2 파라미터 기본값과 TMZ `Parameter default values and the temporal dead zone`

파라미터가 기본값을 가지는 경우, `let` 선언문처럼 취급되고 TMZ를 갖게 됩니다.

`If parameters have default values, they are treated like a sequence of let statements and are subject to temporal dead zones:`

```javascript
// OK: `y` accesses `x` after it has been declared
function foo(x=1, y=x) {
    return [x, y];
}
foo(); // [1,1]

// Exception: `x` tries to access `y` within TDZ
function bar(x=y, y=2) {
    return [x, y];
}
bar(); // ReferenceError
```

### 9.6.3 파라미터의 기본값은 바디의 스코프를 알지 못한다.  `Parameter default values don’t see the scope of the body`

파라미터의 기본값 스코프는 바디의 스코프와 는 다릅니다. 파라미터 기본값으로 선언된 메소드나 함수는 바디 내의 지역 변수를 참조할 수 없습니다.

`The scope of parameter default values is separate from the scope of the body (the former surrounds the latter). That means that methods or functions defined “inside” parameter default values don’t see the local variables of the body:`

```javascript
const foo = 'outer';
function bar(func = x => foo) {
    const foo = 'inner';
    console.log(func()); // outer
}
bar();
```

## 9.7 전역 객체 `The global object`

자바스크립트의 전역 객체( 웹 브라우저의 `window`, Node.js의 `global` )는 특히 성능적인 면에서 기능이라기 보다 버그로 볼 수 있습니다.

`JavaScript’s global object (window in web browsers, global in Node.js) is more a bug than a feature, especially with regard to performance. That’s why it makes sense that ES6 introduces a distinction:`

+ 모든 전역 객체의 속성은 전역 변수입니다. 전역 스코프에서 아래 선언들이 그렇습니다.  
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
{ // Enter a new scope

    console.log(foo()); // OK, due to hoisting
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

클래스가 호이스팅되지 않는다는 것이 의아해 보일 수도 있습니다. 클래스가 생성하는 것이 함수이기 때문입니다. 호이스팅되지 않는 이유는 `extends` 절의 값들이 이미 선언되어 실행되어 있어야 하기 때문입니다.

`Classes not being hoisted may be surprising, because, under the hood, they create functions. The rationale for this behavior is that the values of their extends clauses are defined via expressions and those expressions have to be executed at the appropriate times.`

```javascript
{ // Enter a new scope

    const identity = x => x;

    // Here we are in the temporal dead zone of `MyClass`
    const inst = new MyClass(); // ReferenceError

    // Note the expression in the `extends` clause
    class MyClass extends identity(Object) {
    }
}
```

## 9.9 코딩 스타일 : `const` 대 `let` 대 `var` `Coding style: const versus let versus var`

항상 `let` 또는 `const`를 사용할 것을 추천합니다.

`I recommend to always use either let or const:`

1. `const`가 우선입니다. 값이 변경되지 않는 모든 곳에 사용할 수 있습니다. 다시 말해 변수가 절대 할당문의 좌변이 되지 않거나 `++` 또는 `--` 의 피연산자가 아닐 경우입니다. `const`의 값인 객체는 변경될 수 있습니다.  
  `Prefer const. You can use it whenever a variable never changes its value. In other words: the variable should never be the left-hand side of an assignment or the operand of ++ or --. Changing an object that a const variable refers to is allowed:` 

  ```javascript
  const foo = {};
  ```

  루프 반복마다 하나의 바인딩이 생성되기 때문에 `for-of` 반복문에서도 사용할 수 있습니다.

  `You can even use const in a for-of loop, because one (immutable) binding is created per loop iteration:`

  ```javascript
  for (const x of ['a', 'b']) {
      console.log(x);
  }
  // Output:
  // a
  // b
  ```

  `for-of` 반복문의 몸체 안에서는 변수 `x`를 변경할 수 없습니다.

  `Inside the body of the for-of loop, x can’t changed.`

2. 그 외에 초기화된 변수의 값이 나중에 변경된다면 `let`을 사용합니다.  
  `Otherwise, use let – when the initial value of a variable changes later on.`

  ```javascript
  let counter = 0; // initial value
  counter++; // change
  
  let obj = {}; // initial value
  obj = { foo: 123 }; // change
  ```

3. `var`는 사용하지 않습니다.  
  `Avoid var.`

이 규칙을 따르면 `var`는 리팩토링이 필요한 래거시 코드에서만 볼 수 있게됩니다.

`If you follow these rules, var will only appear in legacy code, as a signal that careful refactoring is required.`

`var`는 `let`과 `const`가 하지 않는 전역 객체의 속성이 되는 일을 합니다만 이것은 좋은 것이 아닙니다. `window` 또는 `global`을 사용하는 것이 낫습니다.

`var does one thing that let and const don’t: variables declared via it become properties of the global object. However, that’s generally not a good thing. You can achieve the same effect by assigning to window (in browsers) or global (in Node.js).`

### 9.9.1 대안. `An alternative approach`

앞에 언급한 코딩 스타일 규칙 외에 대안은 완전히 불변인 것들( 원시값과 동결된 객체들 )에는 `const`를 사용하는 것입니다. 그렇게 되면 두가지 방법이 있습니다.

`An alternative to the just mentioned style rules is to use const only for things that are completely immutable (primitive values and frozen objects). Then we have two approaches:`

1. `const` 우선 (최선) : `const`를 불변 바인딩에 사용합니다.  
  `Prefer const (recommended): const marks immutable bindings`
2. `let` 우선 (차선) : `const`을 불변값에 사용합니다.  
  `Prefer let(alternative): const marks immutable values`

차선으로도 충분하지만 최선을 사용하는 것을 추천합니다.

`#2 is perfectly acceptable; I only lean slightly in favor of #1.`