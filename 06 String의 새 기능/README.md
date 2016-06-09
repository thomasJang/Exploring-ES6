#6. New string features
string의 새 기능

##6.1 Overview
개요

New string methods:
새로운 문자열 메소드들:

```js
'hello'.startsWith('hell');    // true
'hello'.endsWith('ello');      // true
'doo '.repeat(3);              // 'doo doo doo '
```

ES6 has a new kind of string literal, the template literal:
ES6에는 'template literal'이라고 하는 새로운 문자열 리터럴이 있습니다.

```js
// String interpolation via template literals (in backticks)
// 템플릿 리터럴을 통한 문자열 보간(string interpolation)
const first = 'Jane';
const last = 'Doe';
console.log(`Hello ${first} ${last}!`);    // Hello Jane Doe!

// Template literals also let you create strings with multiple lines
// 템플릿 리터럴로는 여러줄 문자열을 만들 수도 있습니다.
const multiLine = `
This is
a string
with multiple
lines`;
```

##6.2 Unicode code point escapes
유니코드 코드포인트 이스케이프

In ECMAScript 6, there is a new kind of Unicode escape that lets you specify any code point (even those beyond 16 bits):
ES6에는 16비트를 초과한 경우(non-BMP Unicode)를 포함한 모든 유니코드의 코드포인트를 특정할 수 있는, 새로운 유니코드 이스케이프가 있습니다.
_non-BMP Unicode : 둘 또는 그 이상의 unicode unit(각 16비트)로 이루어진 하나의 문자_

```js
console.log('\u{1F680}'); 
    // ES6: single code point (유니코드 유닛 하나로 이루어진 하나의 문자)
console.log('\uD83D\uDE80');
    // ES5: two code units (유니코드 유닛 두 개로 이루어진 하나의 문자)
```

More information on escapes is given in the chapter on Unicode.
이스케이프에 관한 더 자세한 내용은 유니코드 챕터에서 살펴보겠습니다.


##6.3 String interpolation, multi-line string literals and raw string literals
템플릿 리터럴의 제공 기능 - 문자열 보간, 줄바꿈, 이스케이프 미해석 기능


Template literals are described in depth in their own chapter. They provide three interesting features.
템플릿 리터럴은 세 가지 흥미로운 기능을 제공합니다. 여기서는 간략한 소개만 하고, 이어지는 챕터들에서 보다 자세히 살펴보겠습니다.

#####1) First, template literals support string interpolation:
템플릿 리터럴은 문자열 보간(interpolation)을 지원합니다:
_보간 : 사이에 무언가를 끼워넣는 행위._

```js
const first = 'Jane';
const last = 'Doe';
console.log(`Hello ${first} ${last}!`);    // Hello Jane Doe!
```

#####2) Second, template literals can contain multiple lines:
템플릿 리터럴에는 여러 줄을 담을 수 있습니다.

```js
const multiLine = `
This is
a string
with multiple
lines`;
```

#####3) Third, template literals are “raw” if you prefix them with the tag String.raw – the backslash is not a special character and escapes such as \n are not interpreted:
템플릿 리터럴은 앞에 `String.raw` 태그를 적용하면 "원시상태" - `\`이 특수문자가 아니며, `\n`과 같은 이스케이프 문자가 해석되지 않는 - 가 됩니다.

```js
const str = String.raw `Not a newline: \n`;
console.log(str === 'Not a newline: \\n');    // true
```

##6.4 Iterating over strings
문자열에 대한 이터레이션

Strings are iterable, which means that you can use for-of to iterate over their characters:
문자열은 이터러블합니다. 즉 `for-of` 구문을 통해 글자들을 이터레이트할 수 있습니다:

```js
for (const ch of 'abc') {
    console.log(ch);
}
// a
// b
// c
```

And you can use the spread operator (...) to turn strings into Arrays:
또한 펼침연산자(`...`)를 이용하여 문자열을 배열로 치환할 수도 있습니다:

```js
const chars = [...'abc'];    // ['a', 'b', 'c']
```


###6.4.1 Iteration honors Unicode code points
이터레이션은 유니코드 코드포인트를 존중합니다.

The string iterator splits strings along code point boundaries, which means that the strings it returns comprise one or two JavaScript characters:
문자열 이터레이터는 문자열 내의 코드포인트 경계들을 기준으로 분리합니다. 즉 반환된 각 문자열들은 하나 또는 두 개의 자바스크립트 글자들로 구성됩니다.

```js
for (const ch of 'x\uD83D\uDE80y') {
    console.log(ch.length);
}
// 1 (x)
// 2 (\uD83D\uDE80)
// 1 (y)
```

###6.4.2 코드포인트 카운트하기 `Counting code points`
Iteration gives you a quick way to count the Unicode code points in a string:
이터레이션을 이용하면 문자열 내에 존재하는 유니코드 코드포인트들을 빠르게 카운트할 수 있습니다.

```js
[...'x\uD83D\uDE80y'].length    // 3
```

###6.4.3  문자열 역순 처리시 non-BMP 코드포인트 보존 `Reversing strings with non-BMP code points`

Iteration also helps with reversing strings that contain non-BMP code points (which are larger than 16 bit and encoded as two JavaScript characters):
이터레이션은 non-BMP 코드포인트를 포함하는 문자열을 역순으로 뒤집는 경우에도 유용합니다.

```js
const str = 'x\uD83D\uDE80y';

console.log(str);   // x🚀y

console.log(str.split('').reverse().join(''));
  // y��x ('y\uDE80\uD83Dx')
  // ES5: \uD83D와 \uDE80를 별개의 문자로 인식하여 전혀 다른 문자를 출력합니다.

console.log([...str].reverse().join(''));
  // y🚀x ('y\uD83D\uDE80x')
  // ES6: \uD83D\uDE80를 하나의 단어로 인식하여 형태가 유지됩니다.
```

<img src="str.jpg" width="200" alt="The two reversed strings in the Firefox console."><br>: 파이어폭스 콘솔창에서 본 두가지 방식의 문자열 역순처리 결과
`The two reversed strings in the Firefox console.`

#### <img src="warning.png" width="25" alt=""남겨진 문제점: combining marks `Remaining problem: combining marks`

combining mark는 하나의 단어로 표시되는 두 개의 유니코드 코드포인트입니다. ES6는 non-BMP 코드포인트에 대한 문자열 역순처리에 대해서 앞서 설명한 바와 같이 하나의 단어로 인식하여 보존해주고 있지만, combining mark에 대해서는 아직 그러지 못하고 있습니다. 당장 combining mark에 대해서도 같은 방식으로 동작하게끔 하기 위해서는 Mathias Bynens의 [Esrever](https://github.com/mathiasbynens/esrever)와 같은 라이브러리를 활용하는 수밖에 없습니다.
A combining mark is a sequence of two Unicode code points that is displayed as single symbol. The ES6 approach to reversing a string that I have presented here works for non-BMP code points, but not for combining marks. For those, you need a library, e.g. Mathias Bynens’ Esrever.



##6.5 코드포인트의 수치화 `Numeric values of code points`
The new method codePointAt() returns the numeric value of a code point at a given index in a string:
새로 도입된 `codePointAt(index)` 메소드는 문자열 내 index 위치에 해당하는 문자의 코드포인트를 10진수의 수치로 변환하여 반환해줍니다.

```js
const str = 'x\uD83D\uDE80y';
console.log(str.codePointAt(0).toString(16)); // 78
console.log(str.codePointAt(1).toString(16)); // 1f680
console.log(str.codePointAt(3).toString(16)); // 79
```

This method works well when combined with iteration over strings:
이 메소드는 문자열 이터레이션 내에서도 잘 동작합니다.

```js
for (const ch of 'x\uD83D\uDE80y') {
    console.log(ch.codePointAt(0).toString(16));
}
// 78
// 1f680
// 79
```

The opposite of codePointAt() is String.fromCodePoint():
`String.fromCodePoint()`메소드는 `codePointAt()` 메소드의 반대되는 기능을 합니다.

```js
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'  // true
```


##6.6 포함 여부 확인 `Checking for inclusion`

Three new methods check whether a string exists within another string:
문자열 내에 어떤 문자열이 포함되어 있는지 여부를 확인할 수 있는 세 가지 메소드가 추가되었습니다.

```js
'hello'.startsWith('hell');    // true
'hello'.endsWith('ello');      // true
'hello'.includes('ell');       // true
```

Each of these methods has a position as an optional second parameter, which specifies where the string to be searched starts or ends:
이 메소드들에는 두 번째 파라미터에 인덱스값을 대입하여 검색을 시작할 위치 지정할 수도 있습니다.

```js
'hello'.startsWith('ello', 1)    // true
'hello'.endsWith('hell', 4)      // true

'hello'.includes('ll', 1)        // true
'hello'.includes('ll', 3)        // false
```


##6.7 문자열 반복 `Repeating strings`

The `repeat()` method repeats strings.
`repeat()` 메소드는 문자열을 파라미터 값만큼 반복한 결과를 반환합니다.

```js
'doo '.repeat(3)    // 'doo doo doo '
```


##6.8 정규표현식을 위임하는 문자열 메소드들 `String methods that delegate regular expression work to their parameters`

In ES6, the four string methods that accept regular expression parameters do relatively little. They mainly call methods of their parameters:
ES6에는 정규표현식을 파라미터로 받는 네 가지 문자열 메소드가 추가되었습니다. 이들은 주로 파라미터에 지정된 메소드를 호출하는 정도의 간단한 역할만 수행합니다.

- `String.prototype.match(regexp)`은 `regexp[Symbol.match](this)`를 호출합니다.
- `String.prototype.replace(searchValue, replaceValue)`은 `searchValue[Symbol.replace](this, replaceValue)`를 호출합니다.
- `String.prototype.search(regexp)`은 `regexp[Symbol.search](this)`를 호출합니다.
- `String.prototype.split(separator, limit)`은 `separator[Symbol.split](this, limit)`를 호출합니다.
- `String.prototype.match(regexp)` calls `regexp[Symbol.match](this)`.

- `String.prototype.replace(searchValue, replaceValue)` calls `searchValue[Symbol.replace](this, replaceValue)`.
- `String.prototype.search(regexp)` calls `regexp[Symbol.search](this)`.
- `String.prototype.split(separator, limit)` calls `separator[Symbol.split](this, limit)`.

The parameters don’t have to be regular expressions, anymore. Any objects with appropriate methods will do.
위 메소드들의 파라미터에는 반드시 정규표현식을 대입해야 하는 것은 아니며, 해당 메소드의 기능에 적합한 어떤 객체라도 대입 가능합니다.

##6.9 Cheat sheet: the new string methods (요약)

#### 태그드 템플릿 `Tagged templates`
- `String.raw(callSite, ...substitutions)` : string

 Template tag for “raw” content (backslashes are not interpreted):
  '\'를 해석하지 않는 'raw' 탬플릿 태그함수

>
```js
String.raw `\` === '\\'
// true
```

 Consult the chapter on template literals for more information.
  자세한 정보는 '템플릿 리터럴' 장을 참고하세요.


#### 유니코드와 코드포인트 `Unicode and code points`
+ `String.fromCodePoint(...codePoints : number[])` : string

 Turns numbers denoting Unicode code points into a string.
  유니코드 코드포인트에 해당하는 숫자들을 변환하여 문자열을 반환합니다.

+ `String.prototype.codePointAt(pos)` : number

 Returns the number of the code point starting at position pos (comprising one or two JavaScript characters).
  `pos` 위치에서 시작하는 글자의 코드포인트에 해당하는 숫자(하나 또는 두 개의 유니코드 유닛)를 반환합니다.

+ `String.prototype.normalize(form? : string)` : string
  
 Different combinations of code points may look the same. Unicode normalization changes them all to the same value(s), their so-called canonical representation. That helps with comparing and searching for strings. The 'NFC' form is recommended for general text.
  가끔 서로 다른 코드포인트들을 조합하였음에도 똑같은 특수문자를 출력하는 경우가 있습니다. 유니코드 정규화(nomalization)는 이들을 모두 '규범적 표상(canonical representation'이라 불리는 특정 규칙(정규화 양식)에 따라 동일한 값으로 치환하므로, 문자열을 검색할 때 유용합니다. 일반적인 문서에 대해서는 'NFC' 정규화 양식에 따르는 것이 관례입니다.


#### 문자열 검색 `Finding strings`

+ `String.prototype.startsWith(searchString, position=0) : boolean`

Does the receiver start with searchString? position lets you specify where the string to be checked starts.
  메소드를 호출한 문자열이 `searchString`으로 시작하는지 여부를 판별합니다. `position`을 지정하여 어느 위치부터 검색을 시작할지를 특정할 수 있습니다.

+ `String.prototype.endsWith(searchString, endPosition=searchString.length) : boolean`

Does the receiver end with searchString? endPosition lets you specify where the string to be checked ends.
  메소드를 호출한 문자열이 `searchString`으로 끝나는지 여부를 판별합니다. `endPosition`를 통해, 지정한 문자열이 끝나는지 여부를 판별할 때의 '끝나는 위치'를 특정할 수 있습니다.

+ `String.prototype.includes(searchString, position=0) : boolean`
  
Does the receiver contain searchString? position lets you specify where the string to be searched starts.`
  메소드를 호출한 문자열이 `searchString`을 포함하고 있는지 여부를 판별합니다. `position`을 지정하면 어느 위치부터 검색을 시작할지를 특정할 수 있습니다.


#### 문자열 반복 `Repeating strings`
+ `String.prototype.repeat(count) : string`

Returns the receiver, concatenated count times.
  메소드를 호출한 문자열을 `count`에 지정한 횟수만큼 반복하여 나열한 긴 문자열을 반환합니다.
