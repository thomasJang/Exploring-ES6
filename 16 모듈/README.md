## 16. 모듈
> 16. Modules

### 16.1 개요
> 16.1 Overview

자바스크립트는 오랫동안 모듈을 가지고 있었다. 그러나 모듈은 라이브러리를 통해서만 구현되었고 언어에 내장된 것은 아니었다. 자바스크립트가 내장된 모듈을 가지는 것은 ES6가 처음이다.

> JavaScript has had modules for a long time. However, they were implemented via libraries, not built into the language. ES6 is the first time that JavaScript has built-in modules.

ES6 모듈은 파일에 저장된다. 각 파일에 하나의 모듈, 각 모듈에 하나의 파일로 정확히 대응된다. 모듈에서 무언가를 익스포트 하는 방법은 두 가지가 있다. 이 두 가지 방법을 혼합해도 괜찮지만, 분리해서 따로 사용하는 것이 일반적으로 더 낫다.
> ES6 modules are stored in files. There is exactly one module per file and one file per module. You have two ways of exporting things from a module. These two ways can be mixed, but it is usually better to use them separately.

### 16.1.1 Multiple named exports

> 16.1.1 Multiple named exports

> There can be multiple named exports:

```js
//------ lib.js ------
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

//------ main.js ------
import { square, diag } from 'lib';
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

완전한 모듈을 임포트 할 수도 있다.
> You can also import the complete module:

```js
//------ main.js ------
import * as lib from 'lib';
console.log(lib.square(11)); // 121
console.log(lib.diag(4, 3)); // 5
```

### 16.1.2 Single default export
> 16.1.2 Single default export

There can be a single default export. For example, a function:

```js
//------ myFunc.js ------
export default function () { ··· } // no semicolon!

//------ main1.js ------
import myFunc from 'myFunc';
myFunc();
```

클래스도 가능하다.
> Or a class:

```js
//------ MyClass.js ------
export default class { ··· } // no semicolon!

//------ main2.js ------
import MyClass from 'MyClass';
const inst = new MyClass();
```

함수나 클래스를 기본 익스포트 할 경우 구문 끝에 세미콜론이 붙지 않는것에 주의하라(which are anonymous declarations)
Note that there is no semicolon at the end if you default-export a function or a class (which are anonymous declarations).

### 16.1.3 브라우저: 스크립트 vs 모듈
> 16.1.3 Browsers: scripts versus modules

### 16.2 자바스크립트의 모듈
> 16.2 Modules in JavaScript

자바스크립는 내장된 모듈을 지원한 적이 전혀 없음에도 불구하고, 커뮤니티는 ES5와 그 이전 버전의 라이브러리에 의해 지원되는 간단한 모듈 스타일을 수렴해왔다. 이런 스타일이 ES6에도 채택되었다.
> Even though JavaScript never had built-in modules, the community has converged on a simple style of modules, which is supported by libraries in ES5 and earlier. This style has also been adopted by ES6:

+ 각각의 모듈은 일단 한 번 로드 되면 실행되는 코드의 조각이다.
+ 그 코드에서 선언이 있을 수 있다(변수 선언, 함수 선언 등)
 - 자동적으로 이러한 선언은 모듈 내부에 유지된다.
 - 익스포트 할 모듈을 표시 할 수 있고, 다른 모듈이 이 모듈을 임포트 할 수 있다.
+ 하나의 모듈은 다른 모듈들로부터 여러가지를 임포트 할 수 있다. 모듈 명시자를 통해서 임포트된 모듈들을 참조하며, strings that are either :
 - 상대 경로('../model/user'): 이 경로는 임포팅하는 모듈의 위치를 상대적으로 해석한다. 파일 확장자인 .js 는 일반적으로 생략 될 수 있다.
 - 절대 경로('/lib/js/helpers'): 임포트되는 모듈의 파일을 직접적으로 가르킨다.
 - 이름('util'): 설정되어야 하는 참조 모듈의 이름
+ 모듈은 싱글톤이다. 모듈이 여러번 임포트된다고 할 지라도, 하나의 인스턴스만이 존재한다.

> + Each module is a piece of code that is executed once it is loaded.
+ In that code, there may be declarations (variable declarations, function declarations, etc.).
 - By default, these declarations stay local to the module.
 - You can mark some of them as exports, then other modules can import them.
+ A module can import things from other modules. It refers to those modules via module specifiers, strings that are either:
 - Relative paths ('../model/user'): these paths are interpreted relatively to the location of the importing module. The file extension .js can usually be omitted.
 - Absolute paths ('/lib/js/helpers'): point directly to the file of the module to be imported.
 - Names ('util'): What modules names refer to has to be configured.
+ Modules are singletons. Even if a module is imported multiple times, only a single “instance” of it exists.

이러한 모듈의 접근법은 전역 변수를 회피하고, 전역에는 모듈 명시자만이 있다.
> This approach to modules avoids global variables, the only things that are global are module specifiers.

### 16.2.1 ECMAScript 5 모듈 시스템
> 16.2.1 ECMAScript 5 module systems

ES5 모듈 시스템이 언어의 명확한 지원없이도 얼마나 잘 동작하는지 인상적이다. 가장 중요한 두 가지(불행히도 호환 불가한) 명세는 다음과 같다.
> It is impressive how well ES5 module systems work without explicit support from the language. The two most important (and unfortunately incompatible) standards are:

+ *커먼JS*: 이 명세의 주도적인 구현은 Node.js에서 볼 수 있다.(Node.js 모듈은 CommonJS를 능가하는 몇몇 기능을 가지고 있다.) 특징은 다음과 같다:
 - 간결한 문법
 - 동기 로딩과 서버를 위해 고안되었다
+ *비동기 모듈 정의*: 이 명세의 가장 유명한 구현은 RequireJS이다. 특징은 다음과 같다.
 - 약간 더 복잡한 문법, eval()을 안쓰고도 비동기적인 모듈 정의를 가능게 한다. (또는 컴파인 단계에서)
 - 비동기적인 로딩과 브라우저를 위해 고안되었다.

> + *CommonJS Modules*: The dominant implementation of this standard is in Node.js (Node.js modules have a few features that go beyond CommonJS). Characteristics:
- Compact syntax
- Designed for synchronous loading and servers
+ *Asynchronous Module Definition (AMD)*: The most popular implementation of this standard is RequireJS. Characteristics:
- Slightly more complicated syntax, enabling AMD to work without eval() (or a compilation step)
- Designed for asynchronous loading and browsers

위 내용은 ES5 모듈의 간략한 설명이다. 더 자세한 내용을 원한다면 "writing Modular JavaScript With AMD, CommonJS & Harmony" by Addy Osmani 를 살펴보길 권한다.
> The above is but a simplified explanation of ES5 modules. If you want more in-depth material, take a look at “Writing Modular JavaScript With AMD, CommonJS & ES Harmony” by Addy Osmani.

## 16.2.2 ECMAScript 6 모듈
> 16.2.2 ECMAScript 6 modules

ECMAScript 6 모듈의 목적은 CommonJS 와 AMD 유저 모두를 만족시키는 포맷을 만드는 것이었다.
> The goal for ECMAScript 6 modules was to create a format that both users of CommonJS and of AMD are happy with:

+ CommonJS와 유사하게, CommonJS는 간결한 문법, single exports의 선호, 상호?순환 의존성을 지원한다.
+ AMD와 유사하게, AMD는 비동기 로딩과 설정가능한 모듈로딩을 직접적으로 지원한다.
언어에 내장되는것은 CommonJS와 AMD를 넘어서는 ES6모듈을 허용한다. (자세한 내용은 나중에 설명한다.)

+ 문법은 CommonJS 보다 간결하다.
+ 구조는 전략적으로 분석될 수 있다.(정적 체킹, 최적화 등등)
+ 상호의존을 위한 지원은 CommonJS보다 낫다.

+ Similarly to CommonJS, they have a compact syntax, a preference for single exports and support for cyclic dependencies.
+ Similarly to AMD, they have direct support for asynchronous loading and configurable module loading.
Being built into the language allows ES6 modules to go beyond CommonJS and AMD (details are explained later):

+ Their syntax is even more compact than CommonJS’s.
+ Their structure can be statically analyzed (for static checking, optimization, etc.).
+ Their support for cyclic dependencies is better than CommonJS’s.
 
ES6 모듈 명세는 다음 두 가지를 갖는다

> The ES6 module standard has two parts:

+ 선언적 문법(임포트와 익스포트를 위한)
+ 프로그래밍적 로더 API(모듈이 로드 되는 방법 설정과 조건적으로 모듈을 로드하기 위해)

+ Declarative syntax (for importing and exporting)
+ Programmatic loader API: to configure how modules are loaded and to conditionally load modules

## 16.3 ES6 모듈의 기본
> 16.3 The basics of ES6 modules

익스포트의 두 가지 종류 : 명명된 익스포트(several per module)와 디폴트 익스포트이다. 한 번에 둘 다 사용하는 것도 가능하지만 분리하는게 일반적으로 좋다. 
> There are two kinds of exports: named exports (several per module) and default exports (one per module). As explained later, it is possible use both at the same time, but usually best to keep them separate.

## 명명 익스포트(several per module)
> 16.3.1 Named exports (several per module)

모듈은 export 키워드와 함께 쓰이는 선언문에 접두어를 붙임으로써 여러개를 익스포트 할 수 있다. 이러한 익스포트는 이름으로 구별되고 명명된 익스포트라고 불린다.
> A module can export multiple things by prefixing its declarations with the keyword export. These exports are distinguished by their names and are called named exports.

```js
//------ lib.js ------
export const sqrt = Math.sqrt;
export function square(x) {
    return x * x;
}
export function diag(x, y) {
    return sqrt(square(x) + square(y));
}

//------ main.js ------
import { square, diag } from 'lib';
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```


> There are other ways to specify named exports (which are explained later), but I find this one quite convenient: simply write your code as if there were no outside world, then label everything that you want to export with a keyword.

> If you want to, you can also import the whole module and refer to its named exports via property notation:

```js
//------ main.js ------
import * as lib from 'lib';
console.log(lib.square(11)); // 121
console.log(lib.diag(4, 3)); // 5
```

CommonJS 문법과 같은 코드 : 
> The same code in CommonJS syntax: For a while, I tried several clever strategies to be less redundant with my module exports in Node.js. Now I prefer the following simple but slightly verbose style that is reminiscent of the revealing module pattern:

```js
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

//------ main.js ------
var square = require('lib').square;
var diag = require('lib').diag;
console.log(square(11)); // 121
console.log(diag(4, 3)); // 5
```

## 16.3.2 Default exports (one per module)
> 16.3.2 Default exports (one per module)

> Modules that only export single values are very popular in the Node.js community. But they are also common in frontend development where you often have classes for models and components, with one class per module. An ES6 module can pick a default export, the main exported value. Default exports are especially easy to import.

아래 ECMAScript 6 모듈은 단일 함수이다.
> The following ECMAScript 6 module “is” a single function:

```js
//------ myFunc.js ------
export default function () {} // no semicolon!

//------ main1.js ------
import myFunc from 'myFunc';
myFunc();
```

> An ECMAScript 6 module whose default export is a class looks as follows:

```js
//------ MyClass.js ------
export default class {} // no semicolon!

//------ main2.js ------
import MyClass from 'MyClass';
const inst = new MyClass();
```

기본 익스포트는 두 가지 스타일이 있다.
> There are two styles of default exports:

+ Labeling declarations
+ Default-exporting values directly


## 16.3.2.1 기본 익스포트 스타일1 : labeling declarations
> 16.3.2.1 Default export style 1: labeling declarations

기본 익스포트를 위해 함수 선언(또는 제너레이터 함수)이나 클래스 선언에 export default 접두어를 붙일 수 있다. 
> You can prefix any function declaration (or generator function declaration) or class declaration with the keywords export default to make it the default export:

```js
export default function foo() {} // no semicolon!
export default class Bar {} // no semicolon!
```

위 경우에는 이름을 생략 할 수 있다. 이름을 생략하면 기본 익스포트는 익명 함수 선언과 익명 클래스 선언으로 위치한다.
> You can also omit the name in this case. That makes default exports the only place where JavaScript has anonymous function declarations and anonymous class declarations:

```js
export default function () {} // no semicolon!
export default class {} // no semicolon!
```

## 16.3.2.1.1 Why anonymous function declarations and not anonymous function expressions?
> 16.3.2.1.1 Why anonymous function declarations and not anonymous function expressions?

이 전의 코드 두 줄을 보면, 기본 익스포트의 피연산자부가 표현식이 되는 것도 예상 할 수 있다. 
> When you look at the previous two lines of code, you’d expect the operands of export default to be expressions. They are only declarations for reasons of consistency: operands can be named declarations, interpreting their anonymous versions as expressions would be confusing (even more so than introducing new kinds of declarations).

피 연산자부가 표현식으로 해석되길 원한다면 괄호를 써야한다.
> If you want the operands to be interpreted as expressions, you need to use parentheses:

```js
export default (function () {});
export default (class {});
```

### 16.3.2.2 기본 익스포트 스타일 2 : default-exporting values directly
> 16.3.2.2 Default export style 2: default-exporting values directly

값은 표현식을 통해 생산된다.

> The values are produced via expressions:

```js
export default 'abc';
export default foo();
export default /^xyz$/;
export default 5 * 7;
export default { no: false, yes: true };
```

위 각각의 기본 익스포트는 다음과 같은 구조를 갖는다.
> Each of these default exports has the following structure.

export default «표현식»;
> export default «expression»;

이는 아래와 동등하다:
> That is equivalent to:

const __default__ = «표현식»;
> const __default__ = «expression»;

```js
export { __default__ as default }; // (A)
```
라인 A구문은 익스포트 절이다.(이것은 뒤에 있을 섹션에서 설명된다.)
The statement in line A is an export clause (which is explained in a later section).

### 16.3.2.2.1 두 가지 기본 익스포트 스타일의 이유
> 16.3.2.2.1 Why two default export styles?

> The second default export style was introduced because variable declarations can’t be meaningfully turned into default exports if they declare multiple variables:

export default const foo = 1, bar = 2, baz = 3; // not legal JavaScript!
Which one of the three variables foo, bar and baz would be the default export?

## 16.3.3 임포트와 익스포트는 가장 최상단에 위치해야한다.
> 16.3.3 Imports and exports must be at the top level

> As explained in more detail later, the structure of ES6 modules is static, you can’t conditionally import or export things. That brings a variety of benefits.


> This restriction is enforced syntactically by only allowing imports and exports at the top level of a module:

```js
if (Math.random()) {
    import 'foo'; // SyntaxError
}

// You can’t even nest `import` and `export`
// inside a simple block:
{
    import 'foo'; // SyntaxError
}
```

> 16.3.4 Imports are hoisted
Module imports are hoisted (internally moved to the beginning of the current scope). Therefore, it doesn’t matter where you mention them in a module and the following code works without any problems:

```js
foo();

import { foo } from 'my_module';
```

## 16.3.5 임포트는 익스포트에서 읽기전용 뷰이다
> 16.3.5 Imports are read-only views on exports

ES6모듈의 임포트는 익스포트된 엔티티의 읽기전용 뷰이다. 
> The imports of an ES6 module are read-only views on the exported entities. That means that the connections to variables declared inside module bodies remain live, as demonstrated in the following code.

```js
//------ lib.js ------
export let counter = 3;
export function incCounter() {
    counter++;
}

//------ main.js ------
import { counter, incCounter } from './lib';

// The imported value `counter` is live
console.log(counter); // 3
incCounter();
console.log(counter); // 4
```


> How that works under the hood is explained in a later section.

> Imports as views have the following advantages:

> They enable cyclic dependencies, even for unqualified imports (as explained in the next section).

> Qualified and unqualified imports work the same way (they are both indirections).
You can split code into multiple modules and it will continue to work (as long as you don’t try to change the values of imports).

## 16.3.6 
> 16.3.6 Support for cyclic dependencies

> Two modules A and B are cyclically dependent on each other if both A (possibly indirectly/transitively) imports B and B imports A. If possible, cyclic dependencies should be avoided, they lead to A and B being tightly coupled – they can only be used and evolved together.

> Why support cyclic dependencies, then? Occasionally, you can’t get around them, which is why support for them is an important feature. A later section has more information.

> Let’s see how CommonJS and ECMAScript 6 handle cyclic dependencies.

## 16.3.6.1 
> 16.3.6.1 Cyclic dependencies in CommonJS

> The following CommonJS code correctly handles two modules a and b cyclically depending on each other.

```js
//------ a.js ------
var b = require('b');
function foo() {
    b.bar();
}
exports.foo = foo;

//------ b.js ------
var a = require('a'); // (i)
function bar() {
    if (Math.random()) {
        a.foo(); // (ii)
    }
}
exports.bar = bar;
```


> If module a is imported first then, in line i, module b gets a’s exports object before the exports are added to it. Therefore, b cannot access a.foo in its top level, but that property exists once the execution of a is finished. If bar() is called afterwards then the method call in line ii works.

> As a general rule, keep in mind that with cyclic dependencies, you can’t access imports in the body of the module. That is inherent to the phenomenon and doesn’t change with ECMAScript 6 modules.

> The limitations of the CommonJS approach are:

> Node.js-style single-value exports don’t work. There, you export single values instead of objects:
  module.exports = function () { ··· };
> If module a did that then module b’s variable a would not be updated once the assignment is made. It would continue to refer to the original exports object.

> You can’t use named exports directly. That is, module b can’t import foo like this:
  var foo = require('a').foo;
foo would simply be undefined. In other words, you have no choice but to refer to foo via a.foo.

> These limitations mean that both exporter and importers must be aware of cyclic dependencies and support them explicitly.

## 16.3.6.2  
> 16.3.6.2 Cyclic dependencies in ECMAScript 6

> ES6 modules support cyclic dependencies automatically. That is, they do not have the two limitations of CommonJS modules that were mentioned in the previous section: default exports work, as do unqualified named imports (lines i and iii in the following example). Therefore, you can implement modules that cyclically depend on each other as follows.

```js
//------ a.js ------
import {bar} from 'b'; // (i)
export function foo() {
    bar(); // (ii)
}

//------ b.js ------
import {foo} from 'a'; // (iii)
export function bar() {
    if (Math.random()) {
        foo(); // (iv)
    }
}
```

This code works, because, as explained in the previous section, imports are views on exports. That means that even unqualified imports (such as bar in line ii and foo in line iv) are indirections that refer to the original data. Thus, in the face of cyclic dependencies, it doesn’t matter whether you access a named export via an unqualified import or via its module: There is an indirection involved in either case and it always works.

## 16.4 
> 16.4 Importing and exporting in detail

## 16.4.1
> 16.4.1 Importing styles

> ECMAScript 6 provides several styles of importing1:

Default import:
  import localName from 'src/my_lib';
Namespace import: imports the module as an object (with one property per named export).
  import * as my_lib from 'src/my_lib';
Named imports:
  import { name1, name2 } from 'src/my_lib';
You can rename named imports:

  // Renaming: import `name1` as `localName1`
  import { name1 as localName1, name2 } from 'src/my_lib';
    
  // Renaming: import the default export as `foo`
  import { default as foo } from 'src/my_lib';
Empty import: only loads the module, doesn’t import anything. The first such import in a program executes the body of the module.
  import 'src/my_lib';
There are only two ways to combine these styles and the order in which they appear is fixed; the default export always comes first.

Combining a default import with a namespace import:
  import theDefault, * as my_lib from 'src/my_lib';
Combining a default import with named imports
  import theDefault, { name1, name2 } from 'src/my_lib';
  
## 16.4.2

> 16.4.2 Named exporting styles: inline versus clause

> There are two ways in which you can export named things inside modules.

> On one hand, you can mark declarations with the keyword export.

```js
export var myVar1 = ···;
export let myVar2 = ···;
export const MY_CONST = ···;

export function myFunc() {
    ···
}
export function* myGeneratorFunc() {
    ···
}
export class MyClass {
    ···
}
```

> On the other hand, you can list everything you want to export at the end of the module (which is similar in style to the revealing module pattern).

```js
const MY_CONST = ···;
function myFunc() {
    ···
}

export { MY_CONST, myFunc };
You can also export things under different names:

export { MY_CONST as FOO, myFunc };
```

## 16.4.3

> 16.4.3 Re-exporting

> Re-exporting means adding another module’s exports to those of the current module. You can either add all of the other module’s exports:

```js
export * from 'src/other_module';
Default exports are ignored2 by export *.
```

> Or you can be more selective (optionally while renaming):

```js
export { foo, bar } from 'src/other_module';

// Renaming: export other_module’s foo as myFoo
export { foo as myFoo, bar } from 'src/other_module';
```

## 16.4.3.1 

> 16.4.3.1 Making a re-export the default export

> The following statement makes the default export of another module foo the default export of the current module:

```js
export { default } from 'foo';
```

> The following statement makes the named export myFunc of module foo the default export of the current module:

```js
export { myFunc as default } from 'foo';
```

## 16.4.4
> 16.4.4 All exporting styles

> ECMAScript 6 provides several styles of exporting3:

Re-exporting:
Re-export everything (except for the default export):
  export * from 'src/other_module';
Re-export via a clause:
  export { foo as myFoo, bar } from 'src/other_module';

  export { default } from 'src/other_module';
  export { default as foo } from 'src/other_module';
  export { foo as default } from 'src/other_module';
Named exporting via a clause:
  export { MY_CONST as FOO, myFunc };
  export { foo as default };
Inline named exports:
Variable declarations:
  export var foo;
  export let foo;
  export const foo;
Function declarations:
  export function myFunc() {}
  export function* myGenFunc() {}
Class declarations:
  export class MyClass() {}
Default export:
Function declarations (can be anonymous here):
  export default function myFunc() {}
  export default function () {}

  export default function* myGenFunc() {}
  export default function* () {}
Class declarations (can be anonymous here):
  export default class MyClass() {}
  export default class () {}
Expressions: export values. Note the semicolons at the end.
  export default foo;
  export default 'Hello world!';
  export default 3 * 7;
  export default (function () {});
  
## 16.4.5

> 16.4.5 Having both named exports and a default export in a module

> The following pattern is surprisingly common in JavaScript: A library is a single function, but additional services are provided via properties of that function. Examples include jQuery and Underscore.js. The following is a sketch of Underscore as a CommonJS module:

```js
//------ underscore.js ------
var _ = function (obj) {
    ···
};
var each = _.each = _.forEach =
    function (obj, iterator, context) {
        ···
    };
module.exports = _;

//------ main.js ------
var _ = require('underscore');
var each = _.each;
···
```

> With ES6 glasses, the function _ is the default export, while each and forEach are named exports. As it turns out, you can actually have named exports and a default export at the same time. As an example, the previous CommonJS module, rewritten as an ES6 module, looks like this:

```js
//------ underscore.js ------
export default function (obj) {
    ···
}
export function each(obj, iterator, context) {
    ···
}
export { each as forEach };

//------ main.js ------
import _, { each } from 'underscore';
···
```

> Note that the CommonJS version and the ECMAScript 6 version are only roughly similar. The latter has a flat structure, whereas the former is nested.

## 16.4.5.1
> 16.4.5.1 Recommendation: avoid mixing default exports and named exports

> I generally recommend to keep the two kinds of exporting separate: per module, either only have a default export or only have named exports.

> However, that is not a very strong recommendation; it occasionally may make sense to mix the two kinds. One example is a module that default-exports an entity. For unit tests, one could additionally make some of the internals available via named exports.

## 16.4.5.2

> 16.4.5.2 The default export is just another named export
> The default export is actually just a named export with the special name default. That is, the following two statements are equivalent:

```js
import { default as foo } from 'lib';
import foo from 'lib';
```

Similarly, the following two modules have the same default export:
```js
//------ module1.js ------
export default function foo() {} // function declaration!

//------ module2.js ------
function foo() {}
export { foo as default };
```

## 16.4.5.3

> 16.4.5.3 default: OK as export name, but not as variable name

> You can’t use reserved words (such as default and new) as variable names, but you can use them as names for exports (you can also use them as property names in ECMAScript 5). If you want to directly import such named exports, you have to rename them to proper variables names.

> That means that default can only appear on the left-hand side of a renaming import:

```js
import { default as foo } from 'some_module';
```

And it can only appear on the right-hand side of a renaming export:

```js
export { foo as default };
```

In re-exporting, both sides of the as are export names:

```js
export { myFunc as default } from 'foo';
export { default as otherFunc } from 'foo';

// The following two statements are equivalent:
export { default } from 'foo';
export { default as default } from 'foo';
```

## 16.5 ECMAScript 6 모듈 로더 API

> 16.5 The ECMAScript 6 module loader API

> In addition to the declarative syntax for working with modules, there is also a programmatic API. It allows you to:

Programmatically work with modules
Configure module loading
The module loader API is not part of the ES6 standard
It will be specified in a separate document, the “JavaScript Loader Standard”, that will be evolved more dynamically than the language specification. The repository for that document states:

[The JavaScript Loader Standard] consolidates work on the ECMAScript module loading semantics with the integration points of Web browsers, as well as Node.js.

The module loader API is work in progress
As you can see in the repository of the JavaScript Loader Standard, the module loader API is still work in progress. Everything you read about it in this book is tentative. To get an impression of what the API may look like, you can take a look at the ES6 Module Loader Polyfill on GitHub.

## 16.5.1 로더
> 16.5.1 Loaders

> Loaders handle resolving module specifiers (the string IDs at the end of import-from), loading modules, etc. Their constructor is Reflect.Loader. Each platform keeps a default instance in the global variable System (the system loader), which implements its specific style of module loading.

## 16.5.2
> 16.5.2 Loader method: importing modules

프로미스를 기반으로 한 API를 이용해서 모듈을 임포트 할 수 있다.
> You can programmatically import a module, via an API based on Promises:

```js
System.import('some_module')
.then(some_module => {
    // Use some_module
})
.catch(error => {
    ···
});
```

> System.import() enables you to:

스크립트 포함 구문~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

```js
Promise.all(
    ['module1', 'module2', 'module3']
    .map(x => System.import(x)))
.then(([module1, module2, module3]) => {
    // Use module1, module2, module3
});
```

## 16.5.3 더 많은 로더 메서드

> 16.5.3 More loader methods

> Loaders have more methods. Three important ones are:

System.module(source, options?)
evaluates the JavaScript code in source to a module (which is delivered asynchronously via a Promise).
System.set(name, module)
is for registering a module (e.g. one you have created via System.module()).
System.define(name, source, options?)
both evaluates the module code in source and registers the result.

## 16.5.4 설정 모듈 로딩

> 16.5.4 Configuring module loading

> The module loader API will have various hooks for configuring the loading process. Use cases include:

Lint modules on import (e.g. via JSLint or JSHint).
Automatically translate modules on import (they could contain CoffeeScript or TypeScript code).
Use legacy modules (AMD, Node.js).
Configurable module loading is an area where Node.js and CommonJS are limited.

## 16.6 브라우저에서 ES6 모듈 사용하기

> 16.6 Using ES6 modules in browsers

브라우저에서 ES6 모듈이 어떻게 지원되는지 알아보자
> Let’s look at how ES6 modules are supported in browsers.

> Support for ES6 modules in browsers is work in progress
Similarly to module loading, other aspects of support for modules in browsers are still being worked on. Everything you read here may change.

## 16.6.1 브라우저: 비동기 모듈 vs 동기 스크립트

> 16.6.1 Browsers: asynchronous modules versus synchronous scripts

> In browsers, there are two different kinds of entities: scripts and modules. They have slightly different syntax and work differently.

> This is an overview of the differences, details are explained later:

~~~~~~~~~~~~~~~~~~~가져와야한다
 	
## 16.6.1.1 스크립트
> 16.6.1.1 Scripts

> Scripts are the traditional browser way to embed JavaScript and to refer to external JavaScript files. Scripts have an internet media type that is used as:

> The content type of JavaScript files delivered via a web server.

> The value of the attribute type of <script> elements. Note that for HTML5, the recommendation is to omit the type attribute in <script> elements if they contain or refer to JavaScript.
The following are the most important values:

text/javascript: is a legacy value and used as the default if you omit the type attribute in a script tag. It is the safest choice for Internet Explorer 8 and earlier.
application/javascript: is recommended for current browsers.
Scripts are normally loaded or executed synchronously. The JavaScript thread stops until the code has been loaded or executed.

## 16.6.1.2 모듈
> 16.6.1.2 Modules


## 16.6.1.4 모듈 또는 스크립트
> 16.6.1.3 Module or script – a matter of context
Whether a file is a module or a script is only determined by how it is imported or loaded. Most modules have either imports or exports and can thus be detected. But if a module has neither then it is indistinguishable from a script. For example:

var x = 123;
The semantics of this piece of code differs depending on whether it is interpreted as a module or as a script:

As a module, the variable x is created in module scope.
As a script, the variable x becomes a global variable and a property of the global object (window in browsers).
More realistic example is a module that installs something, e.g. a polyfill in global variables or a global event listener. Such a module neither imports nor exports anything and is activated via an empty import:

import './my_module';
Sources of this section
“Modules: Status Update”, slides by David Herman.
“Modules vs Scripts”, an email by David Herman.
16.7 Details: imports as views on exports
The code in this section is available on GitHub.

Imports work differently in CommonJS and ES6:

In CommonJS, imports are copies of exported values.
In ES6, imports are live read-only views on exported values.
The following sections explain what that means.

16.7.1 In CommonJS, imports are copies of exported values
With CommonJS (Node.js) modules, things work in relatively familiar ways.

If you import a value into a variable, the value is copied twice: once when it is exported (line A) and once it is imported (line B).

//------ lib.js ------
var counter = 3;
function incCounter() {
    counter++;
}
module.exports = {
    counter: counter, // (A)
    incCounter: incCounter,
};

//------ main1.js ------
var counter = require('./lib').counter; // (B)
var incCounter = require('./lib').incCounter;

// The imported value is a (disconnected) copy of a copy
console.log(counter); // 3
incCounter();
console.log(counter); // 3

// The imported value can be changed
counter++;
console.log(counter); // 4
If you access the value via the exports object, it is still copied once, on export:

//------ main2.js ------
var lib = require('./lib');

// The imported value is a (disconnected) copy
console.log(lib.counter); // 3
lib.incCounter();
console.log(lib.counter); // 3

// The imported value can be changed
lib.counter++;
console.log(lib.counter); // 4
16.7.2 In ES6, imports are live read-only views on exported values
In contrast to CommonJS, imports are views on exported values. In other words, every import is a live connection to the exported data. Imports are read-only:

Unqualified imports (import x from 'foo') are like const-declared variables.
The properties of a module object foo (import * as foo from 'foo') are like the properties of a frozen object.
The following code demonstrates how imports are like views:

//------ lib.js ------
export let counter = 3;
export function incCounter() {
    counter++;
}

//------ main1.js ------
import { counter, incCounter } from './lib';

// The imported value `counter` is live
console.log(counter); // 3
incCounter();
console.log(counter); // 4

// The imported value can’t be changed
counter++; // TypeError
If you import the module object via the asterisk (*), you get the same results:

//------ main2.js ------
import * as lib from './lib';

// The imported value `counter` is live
console.log(lib.counter); // 3
lib.incCounter();
console.log(lib.counter); // 4

// The imported value can’t be changed
lib.counter++; // TypeError
Note that while you can’t change the values of imports, you can change the objects that they are referring to. For example:

//------ lib.js ------
export let obj = {};

//------ main.js ------
import { obj } from './lib';

obj.prop = 123; // OK
obj = {}; // TypeError
16.7.2.1 Why a new approach to importing?
Why introduce such a relatively complicated mechanism for importing that deviates from established practices?

Cyclic dependencies: The main advantage is that it supports cyclic dependencies even for unqualified imports.
Qualified and unqualified imports work the same. In CommonJS, they don’t: a qualified import provides direct access to a property of a module’s export object, an unqualified import is a copy of it.
You can split code into multiple modules and it will continue to work (as long as you don’t try to change the values of imports).
On the flip side, module folding, combining multiple modules into a single module becomes simpler, too.
In my experience, ES6 imports just work, you rarely have to think about what’s going on under the hood.

16.7.3 Implementing views
How do imports work as views of exports under the hood? Exports are managed via the data structure export entry. All export entries (except those for re-exports) have the following two names:

Local name: is the name under which the export is stored inside the module.
Export name: is the name that importing modules need to use to access the export.
After you have imported an entity, that entity is always accessed via a pointer that has the two components module and local name. In other words, that pointer refers to a binding (the storage space of a variable) inside a module.

Let’s examine the export names and local names created by various kinds of exporting. The following table (adapted from the ES6 spec) gives an overview, subsequent sections have more details.

Statement	Local name	Export name
export {v};	'v'	'v'
export {v as x};	'v'	'x'
export const v = 123;	'v'	'v'
export function f() {}	'f'	'f'
export default function f() {}	'f'	'default'
export default function () {}	'*default*'	'default'
export default 123;	'*default*'	'default'

### 16.7.3.1 익스포트 절
> 16.7.3.1 Export clause

```js
function foo() {}
export { foo };
Local name: foo
Export name: foo
function foo() {}
export { foo as bar };
Local name: foo
Export name: bar
```

### 16.7.3.2 인라인 익스포트
> 16.7.3.2 Inline exports

아래는 인라인 익스포트이다: 
> This is an inline export:

```js
export function foo() {}
```

이는 다음 코드와 동등하다
> It is equivalent to the following code:

```js
function foo() {}
export { foo };
```

Therefore, we have the following names:

Local name: foo
Export name: foo

### 16.7.3.3 기본 익스포트

> 16.7.3.3 Default exports

There are two kinds of default exports:

Default exports of hoistable declarations (function declarations, generator function declarations) and class declarations are similar to normal inline exports in that named local entities are created and tagged.
All other default exports are about exporting the results of expressions.

### 16.7.3.3.1 기본 익스포트 표현식
> 16.7.3.3.1 Default-exporting expressions

> The following code default-exports the result of the expression 123:

> export default 123;

> It is equivalent to:

```js
const *default* = 123; // *not* legal JavaScript
export { *default* as default };
```

> If you default-export an expression, you get:

Local name: *default*
Export name: default

> The local name was chosen so that it wouldn’t clash with any other local name.

Note that a default export still leads to a binding being created. But, due to *default* not being a legal identifier, you can’t access that binding from inside the module.

### 16.7.3.3.2 

> 16.7.3.3.2 Default-exporting hoistable declarations and class declarations

> The following code default-exports a function declaration:

```js
export default function foo() {}
```

> It is equivalent to:

```js
function foo() {}
export { foo as default };
```

The names are:

Local name: foo
Export name: default

> That means that you can change the value of the default export from within the module, by assigning a different value to foo.

> (Only) for default exports, you can also omit the name of a function declaration:

```js
export default function () {}
```

That is equivalent to:

```js
function *default*() {} // *not* legal JavaScript
export { *default* as default };
```

The names are:

Local name: *default*
Export name: default

> Default-exporting generator declarations and class declarations works similarly to default-exporting function declarations.

### 16.7.4 
> 16.7.4 Imports as views in the spec

> This section gives pointers into the ECMAScript 2015 (ES6) language specification.

> Managing imports:

CreateImportBinding() creates local bindings for imports.
GetBindingValue() is used to access them.
ModuleDeclarationInstantiation() sets up the environment of a module (compare: FunctionDeclarationInstantiation(), BlockDeclarationInstantiation()).
The export names and local names created by the various kinds of exports are shown in table 42 in the section “Source Text Module Records”. The section “Static Semantics: ExportEntries” has more details. You can see that export entries are set up statically (before evaluating the module), evaluating export statements is described in the section “Runtime Semantics: Evaluation”.

### 16.8 ES6 모듈을 위한 설계 목표 
> 16.8 Design goals for ES6 modules
If you want to make sense of ECMAScript 6 modules, it helps to understand what goals influenced their design. The major ones are:

Default exports are favored
Static module structure
Support for both synchronous and asynchronous loading
Support for cyclic dependencies between modules
The following subsections explain these goals.

### 16.8.1 기본 익스포트가 선호된다.
> 16.8.1 Default exports are favored

> The module syntax suggesting that the default export “is” the module may seem a bit strange, but it makes sense if you consider that one major design goal was to make default exports as convenient as possible. Quoting David Herman:

> ECMAScript 6 favors the single/default export style, and gives the sweetest syntax to importing the default. Importing named exports can and even should be slightly less concise.

### 16.8.2 정적 모듈 구조
> 16.8.2 Static module structure

현재 자바스크립트 모듈 포맷은 동적 구조를 가진다. : 임포트되거나 익스포트된 것은 런타임에 변경될 수 있다. 
> Current JavaScript module formats have a dynamic structure: What is imported and exported can change at runtime. One reason why ES6 introduced its own module format is to enable a static structure, which has several benefits. But before we go into those, let’s examine what the structure being static means.

> It means that you can determine imports and exports at compile time (statically) – you only need to look at the source code, you don’t have to execute it. ES6 enforces this syntactically: You can only import and export at the top level (never nested inside a conditional statement). And import and export statements have no dynamic parts (no variables etc. are allowed).

> The following are two examples of CommonJS modules that don’t have a static structure. In the first example, you have to run the code to find out what it imports:

```js
var my_lib;
if (Math.random()) {
    my_lib = require('foo');
} else {
    my_lib = require('bar');
}
```

> In the second example, you have to run the code to find out what it exports:

```js
if (Math.random()) {
    exports.baz = ···;
}
```

> ECMAScript 6 modules are less flexible and force you to be static. As a result, you get several benefits, which are described next.

### 16.8.2.1
> 16.8.2.1 Benefit: dead code elimination during bundling

프론트엔드 개발에서 모듈은 다음과 같이 다뤄진다.
> In frontend development, modules are usually handled as follows:


> During development, code exists as many, often small, modules.
For deployment, these modules are bundled into a few, relatively large, files.
The reasons for bundling are:

> Fewer files need to be retrieved in order to load all modules.
Compressing the bundled file is slightly more efficient than compressing separate files.
During bundling, unused exports can be removed, potentially resulting in significant space savings.
Reason #1 is important for HTTP/1, where the cost for requesting a file is relatively high. That will change with HTTP/2, which is why this reason doesn’t matter there.

> Reason #3 will remain compelling. It can only be achieved with a module format that has a static structure.

### 16.8.2.2
> 16.8.2.2 Benefit: compact bundling, no custom bundle format

> The module bundler Rollup proved that ES6 modules can be combined efficiently, because they all fit into a single scope (after renaming variables to eliminate name clashes). This is possible due to two characteristics of ES6 modules:

> Their static structure means that the bundle format does not have to account for conditionally loaded modules (a common technique for doing so is putting module code in functions).

> Imports being read-only views on exports means that you don’t have to copy exports, you can refer to them directly.
As an example, consider the following two ES6 modules.

```js
// lib.js
export function foo() {}
export function bar() {}

// main.js
import {foo} from './lib.js';
console.log(foo());
```

> Rollup can bundle these two ES6 modules into the following single ES6 module (note the eliminated unused export bar):

```js
function foo() {}

console.log(foo());
```

> Another benefit of Rollup’s approach is that the bundle does not have a custom format, it is just an ES6 module.

### 16.8.2.3
> 16.8.2.3 Benefit: faster lookup of imports

> If you require a library in CommonJS, you get back an object:

```js
var lib = require('lib');
lib.someFunc(); // property lookup
```

> Thus, accessing a named export via lib.someFunc means you have to do a property lookup, which is slow, because it is dynamic.

> In contrast, if you import a library in ES6, you statically know its contents and can optimize accesses:

```js
import * as lib from 'lib';
lib.someFunc(); // statically resolved
```
### 16.8.2.4
> 16.8.2.4 Benefit: variable checking

> With a static module structure, you always statically know which variables are visible at any location inside the module:

> Global variables: increasingly, the only completely global variables will come from the language proper. Everything else will come from modules (including functionality from the standard library and the browser). That is, you statically know all global variables.
Module imports: You statically know those, too.
Module-local variables: can be determined by statically examining the module.
This helps tremendously with checking whether a given identifier has been spelled properly. This kind of check is a popular feature of linters such as JSLint and JSHint; in ECMAScript 6, most of it can be performed by JavaScript engines.

> Additionally, any access of named imports (such as lib.foo) can also be checked statically.

## 16.8.2.5
> 16.8.2.5 Benefit: ready for macros

> Macros are still on the roadmap for JavaScript’s future. If a JavaScript engine supports macros, you can add new syntax to it via a library. Sweet.js is an experimental macro system for JavaScript. The following is an example from the Sweet.js website: a macro for classes.

```js
// Define the macro
macro class {
    rule {
        $className {
                constructor $cparams $cbody
                $($mname $mparams $mbody) ...
        }
    } => {
        function $className $cparams $cbody
        $($className.prototype.$mname
            = function $mname $mparams $mbody; ) ...
    }
}

// Use the macro
class Person {
    constructor(name) {
        this.name = name;
    }
    say(msg) {
        console.log(this.name + " says: " + msg);
    }
}
var bob = new Person("Bob");
bob.say("Macros are sweet!");
```

> For macros, a JavaScript engine performs a preprocessing step before compilation: If a sequence of tokens in the token stream produced by the parser matches the pattern part of the macro, it is replaced by tokens generated via the body of macro. The preprocessing step only works if you are able to statically find macro definitions. Therefore, if you want to import macros via modules then they must have a static structure.


##16.8.2.6 Benefit: ready for types

> 16.8.2.6 Benefit: ready for types

> Static type checking imposes constraints similar to macros: it can only be done if type definitions can be found statically. Again, types can only be imported from modules if they have a static structure.

> Types are appealing because they enable statically typed fast dialects of JavaScript in which performance-critical code can be written. One such dialect is Low-Level JavaScript (LLJS).

## 16.8.2.7
> 16.8.2.7 Benefit: supporting other languages
> If you want to support compiling languages with macros and static types to JavaScript then JavaScript’s modules should have a static structure, for the reasons mentioned in the previous two sections.

##16.8.2.8
> 16.8.2.8 Source of this section

“Static module resolution” by David Herman

## 16.8.3
> 16.8.3 Support for both synchronous and asynchronous loading

> ECMAScript 6 modules must work independently of whether the engine loads modules synchronously (e.g. on servers) or asynchronously (e.g. in browsers). Its syntax is well suited for synchronous loading, asynchronous loading is enabled by its static structure: Because you can statically determine all imports, you can load them before evaluating the body of the module (in a manner reminiscent of AMD modules).

## 16.8.4

> 16.8.4 Support for cyclic dependencies between modules

> Support for cyclic dependencies was a key goal for ES6 modules. Here is why:

> Cyclic dependencies are not inherently evil. Especially for objects, you sometimes even want this kind of dependency. For example, in some trees (such as DOM documents), parents refer to children and children refer back to parents. In libraries, you can usually avoid cyclic dependencies via careful design. In a large system, though, they can happen, especially during refactoring. Then it is very useful if a module system supports them, because the system doesn’t break while you are refactoring.

> The Node.js documentation acknowledges the importance of cyclic dependencies and Rob Sayre provides additional evidence:

> Data point: I once implemented a system like [ECMAScript 6 modules] for Firefox. I got asked for cyclic dependency support 3 weeks after shipping.

> That system that Alex Fritze invented and I worked on is not perfect, and the syntax isn’t very pretty. But it’s still getting used 7 years later, so it must have gotten something right.

## 16.9 FAQ: 모듈
> 16.9 FAQ: modules

## 16.9.1
> 16.9.1 Can I use a variable to specify from which module I want to import?
> The import statement is completely static: its module specifier is always fixed. If you want to dynamically determine what module to load, you need to use the programmatic loader API:

```js
const moduleSpecifier = 'module_' + Math.random();
System.import(moduleSpecifier)
.then(the_module => {
    // Use the_module
})
```

## 16.9.2
> 16.9.2 Can I import a module conditionally or on demand?

> Import statements must always be at the top level of modules. That means that you can’t nest them inside if statements, functions, etc. Therefore, you have to use the programmatic loader API if you want to load a module conditionally or on demand:

```js
if (Math.random()) {
    System.import('some_module')
    .then(some_module => {
        // Use some_module
    })
}
```

## 16.9.3 

> 16.9.3 Can I use variables in an import statement?

> No, you can’t. Remember – what is imported must not depend on anything that is computed at runtime. Therefore:

```js
// Illegal syntax:
import foo from 'some_module'+SUFFIX;
```

## 16.9.4
> 16.9.4 Can I use destructuring in an import statement?
No you can’t. The import statement only looks like destructuring, but is completely different (static, imports are views, etc.).

> Therefore, you can’t do something like this in ES6:

```js
// Illegal syntax:
import { foo: { bar } } from 'some_module';
```

## 16.9.5

> 16.9.5 Are named exports necessary? Why not default-export objects?

> You may be wondering – why do we need named exports if we could simply default-export objects (like in CommonJS)? The answer is that you can’t enforce a static structure via objects and lose all of the associated advantages (which are explained in this chapter).

## 16.9.6

> 16.9.6 Can I eval() the code of module?

> No, you can’t. Modules are too high-level a construct for eval(). The module loader API provides the means for creating modules from strings. Syntactically, eval() accepts scripts (which don’t allow import and export), not modules.

## 16.10
ECMAScript 6 모듈의 이점
> 16.10 Advantages of ECMAScript 6 modules

> At first glance, having modules built into ECMAScript 6 may seem like a boring feature – after all, we already have several good module systems. But ECMAScript 6 modules have several new features:

> More compact syntax
Static module structure (helping with dead code elimination, optimizations, static checking and more)
Automatic support for cyclic dependencies
ES6 modules will also – hopefully – end the fragmentation between the currently dominant standards CommonJS and AMD. Having a single, native standard for modules means:

> No more UMD (Universal Module Definition): UMD is a name for patterns that enable the same file to be used by several module systems (e.g. both CommonJS and AMD). Once ES6 is the only module standard, UMD becomes obsolete.
New browser APIs become modules instead of global variables or properties of navigator.
No more objects-as-namespaces: Objects such as Math and JSON serve as namespaces for functions in ECMAScript 5. In the future, such functionality can be provided via modules.

### 16.11 더 읽을거리
> 16.11 Further reading

커먼JS vs ES6: “JavaScript Modules” (by Yehuda Katz)는 
> CommonJS versus ES6: “JavaScript Modules” (by Yehuda Katz) is a quick intro to ECMAScript 6 modules. Especially interesting is a second page where CommonJS modules are shown side by side with their ECMAScript 6 versions.

+ [Spec] Sect. “Imports” starts with grammar rules and continues with semantics.↩
+ [Spec] The specification method GetExportedNames() collects the exports of a module. In step (7.d.i), a check prevents other modules’ default exports from being re-exported.↩
+ [Spec] Sect. “Exports” starts with grammar rules and continues with semantics.↩
