#6. New string features 
#6. string의 새 기능

##6.1 Overview
##6.1 개요

New string methods:  
새로운 문자열 메소드들

```js
'hello'.startsWith('hell');    // true
'hello'.endsWith('ello');      // true
'doo '.repeat(3);              // 'doo doo doo '
```

ES6 has a new kind of string literal, the template literal:  
ES6에는 템플릿 리터럴template literal이라는 새로운 문자열 리터럴이 있다.

```js
// String interpolation via template literals (in backticks)
// 템플릿 리터럴을 통한 문자열 보간string interpolation (` ` 안)
const first = 'Jane';
const last = 'Doe';
console.log(`Hello ${first} ${last}!`);    // Hello Jane Doe!

// Template literals also let you create strings with multiple lines
// 템플릿 리터럴로는 여러줄 문자열을 만들 수도 있다.
const multiLine = `
This is
a string
with multiple
lines`;
```

##6.2 Unicode code point escapes
##6.2 유니코드 코드포인트 이스케이프

In ECMAScript 6, there is a new kind of Unicode escape that lets you specify any code point (even those beyond 16 bits):  
ECMAScript 6에는 어떤 유니코드의 코드포인트라도 특정할 수 있는 새로운 타입의 유니코드 이스케이프가 있다(16비트를 초과한 경우조차).

```js
console.log('\u{1F680}'); 
    // 🚀 ES6: single code point (코드포인트 하나)
console.log('\uD83D\uDE80');
    // 🚀 ES5: two code units (코드 유닛 두 개)
```

More information on escapes is given in the chapter on Unicode.  
이스케이프에 관해서는 26장에서 더 자세히 다룰 것이다.


##6.3 String interpolation, multi-line string literals and raw string literals
##6.3 문자열 보간, 여러줄 문자열 리터럴, 원시 문자열 리터럴

Template literals are described in depth in their own chapter. They provide three interesting features.  
템플릿 리터럴은 세 가지 흥미로운 기능을 제공한다. 이에 대해서는 8장에서 자세히 살펴보겠다.

#####First, template literals support string interpolation:
#####첫째, 템플릿 리터럴은 문자열 보간(interpolation)을 지원한다.

```js
const first = 'Jane';
const last = 'Doe';
console.log(`Hello ${first} ${last}!`);    // Hello Jane Doe!
```


#####Second, template literals can contain multiple lines:
#####둘째, 템플릿 리터럴에는 여러 줄을 담을 수 있다.

```js
const multiLine = `
This is
a string
with multiple
lines`;
```


#####Third, template literals are “raw” if you prefix them with the tag String.raw – the backslash is not a special character and escapes such as \n are not interpreted:
#####셋째, 템플릿 리터럴은 앞에 `String.raw` 태그를 적용하면 "원시상태" - `\`이 특수문자가 아니고 `\n`과 같은 이스케이프가 해석되지 않는 상태 - 가 된다.

```js
const str = String.raw `Not a newline: \n`;
console.log(str === 'Not a newline: \\n');    // true
```

##6.4 Iterating over strings
##6.4 문자열에 대한 이터레이션

Strings are iterable, which means that you can use for-of to iterate over their characters:  
문자열은 이터러블하다. 즉 `for-of` 구문을 통해 글자들을 이터레이트할 수 있다.

```js
for (const ch of 'abc') {
    console.log(ch);
}
// a
// b
// c
```

And you can use the spread operator (...) to turn strings into Arrays:  
또한 펼침연산자(`...`)를 이용하여 문자열을 배열로 치환할 수도 있다.

```js
const chars = [...'abc'];    // ['a', 'b', 'c']
```


###6.4.1 Iteration honors Unicode code points
###6.4.1 이터레이션은 유니코드 코드포인트를 존중한다.

The string iterator splits strings along code point boundaries, which means that the strings it returns comprise one or two JavaScript characters:  
문자열 이터레이터는 코드포인트 경계들을 기준으로 문자열을 분리한다. 이는 반환된 각 문자열들은 자바스크립트 글자 하나 또는 두 개로 구성됨을 의미한다.

```js
for (const ch of 'x\uD83D\uDE80y') {
    console.log(ch.length);
}
// 1 (x)
// 2 (\uD83D\uDE80)
// 1 (y)
```

###6.4.2 Counting code points
###6.4.2 코드포인트 카운트하기

Iteration gives you a quick way to count the Unicode code points in a string:  
이터레이션을 이용하면 문자열 내의 유니코드 코드포인트들을 빠르게 카운트할 수 있다.

```js
[...'x\uD83D\uDE80y'].length    // 3
```

###6.4.3 Reversing strings with non-BMP code points
###6.4.3 non-BMP 코드포인트를 포함한 문자열 뒤집기

Iteration also helps with reversing strings that contain non-BMP code points (which are larger than 16 bit and encoded as two JavaScript characters):  
이터레이션은 non-BMP 코드포인트(16비트를 초과하여 두 개의 자바스크립트 글자로 인코딩되는)를 포함하는 문자열을 역순으로 뒤집는 경우에도 유용하다.

```js
const str = 'x\uD83D\uDE80y';

console.log(str);   // x🚀y

console.log(str.split('').reverse().join(''));
  // y��x ('y\uDE80\uD83Dx')

console.log([...str].reverse().join(''));
  // y🚀x ('y\uD83D\uDE80x')
```

<img src="str.jpg" width="200" alt="The two reversed strings in the Firefox console."><br>
: The two reversed strings in the Firefox console.  
: 파이어폭스 콘솔창에서 본 두가지 방식의 문자열 역순처리 결과

#### <img src="warning.png" width="25" alt=""남겨진 문제점: combining marks `Remaining problem: combining marks`

A combining mark is a sequence of two Unicode code points that is displayed as single symbol. The ES6 approach to reversing a string that I have presented here works for non-BMP code points, but not for combining marks. For those, you need a library, e.g. Mathias Bynens’ Esrever.  
결합기호combining mark는 하나의 단어로 표시되는 두 개의 유니코드 코드포인트이다. ES6는 non-BMP 코드포인트에 대한 문자열 역순처리에 대해서 앞서 설명한 바와 같이 접근하고 있지만, 결합기호에 대해서는 그렇지 않다. 이를 위해서는 Mathias Bynens의 [Esrever](https://github.com/mathiasbynens/esrever)와 같은 라이브러리가 필요할 것이다.


##6.5 Numeric values of code points
##6.5 코드포인트의 수치화

The new method codePointAt() returns the numeric value of a code point at a given index in a string:  
새로운 메소드인 `codePointAt(index)`는 문자열 내의 index 위치에 대한 코드포인트의 수치값을 반환한다.

```js
const str = 'x\uD83D\uDE80y';
console.log(str.codePointAt(0).toString(16)); // 78
console.log(str.codePointAt(1).toString(16)); // 1f680
console.log(str.codePointAt(3).toString(16)); // 79
```

This method works well when combined with iteration over strings:  
이 메소드는 문자열에 대한 이터레이션과 결합된 경우에도 잘 동작한다.

```js
for (const ch of 'x\uD83D\uDE80y') {
    console.log(ch.codePointAt(0).toString(16));
}
// 78
// 1f680
// 79
```

The opposite of codePointAt() is String.fromCodePoint():  
`codePointAt()`의 반대는 `String.fromCodePoint()`이다.

```js
String.fromCodePoint(0x78, 0x1f680, 0x79) === 'x\uD83D\uDE80y'  // true
```


##6.6 Checking for inclusion
##6.6 포함 여부 확인

Three new methods check whether a string exists within another string:  
어떤 문자열 내에 또다른 문자열이 포함되어 있는지 여부를 확인할 수 있는 새로운 세 개의 메소드가 있다.

```js
'hello'.startsWith('hell');    // true
'hello'.endsWith('ello');      // true
'hello'.includes('ell');       // true
```

Each of these methods has a position as an optional second parameter, which specifies where the string to be searched starts or ends:  
이들 각 메소드는 선택적인 두 번째 파라미터를 가지는데, 여기에 인덱스값을 대입하면 검색을 시작할(혹은 끝날) 위치를 특정할 수 있다.

```js
'hello'.startsWith('ello', 1)    // true
'hello'.endsWith('hell', 4)      // true

'hello'.includes('ll', 1)        // true
'hello'.includes('ll', 3)        // false
```


##6.7 Repeating strings
##6.7 문자열 반복 

The `repeat()` method repeats strings.  
`repeat()` 메소드는 문자열을 반복한다.

```js
'doo '.repeat(3)    // 'doo doo doo '
```


##6.8 String methods that delegate regular expression work to their parameters
##6.8 파라미터에 적용될 정규표현식을 위임하는 문자열 메소드들

In ES6, the four string methods that accept regular expression parameters do relatively little. They mainly call methods of their parameters:  
ES6에는 정규표현식을 파라미터로 받는 네 가지 문자열 메소드가 추가되었다. 이들은 주로 파라미터에 대한 메소드를 호출하는 간단한 역할만을 수행한다.

- `String.prototype.match(regexp)` calls `regexp[Symbol.match](this)`.
- `String.prototype.replace(searchValue, replaceValue)` calls `searchValue[Symbol.replace](this, replaceValue)`.
- `String.prototype.search(regexp)` calls `regexp[Symbol.search](this)`.
- `String.prototype.split(separator, limit)` calls `separator[Symbol.split](this, limit)`.

- `String.prototype.match(regexp)`은 `regexp[Symbol.match](this)`를 호출한다.
- `String.prototype.replace(searchValue, replaceValue)`은 `searchValue[Symbol.replace](this, replaceValue)`를 호출한다.
- `String.prototype.search(regexp)`은 `regexp[Symbol.search](this)`를 호출한다.
- `String.prototype.split(separator, limit)`은 `separator[Symbol.split](this, limit)`를 호출한다.

The parameters don’t have to be regular expressions, anymore. Any objects with appropriate methods will do.  
더이상 위 파라미터에 정규표현식만을 대입하지 않아도 된다. 해당 메소드에 적합한 어떤 객체라도 대입할 수 있다.


##6.9 Cheat sheet: the new string methods (요약)

#### Tagged templates 태그드 템플릿

- `String.raw(callSite, ...substitutions)` : string  
  Template tag for “raw” content (backslashes are not interpreted):  
  '원시' 탬플릿 태그 ('\'를 해석하지 않음)

```js
String.raw `\` === `\\`  // true`
```

  Consult the chapter on template literals for more information.  
  자세한 정보는 8장을 참고할 것.


#### Unicode and code points 유니코드와 코드포인트
+ `String.fromCodePoint(...codePoints : number[])` : string

  Turns numbers denoting Unicode code points into a string.  
  유니코드 코드포인트를 지칭하는 숫자들을 문자열로 변환한다.

+ `String.prototype.codePointAt(pos)` : number

  Returns the number of the code point starting at position pos (comprising one or two JavaScript characters).  
  `pos` 위치부터 시작하는 글자(하나 또는 두 개의 자바스크립트 글자)의 코드포인트를 지칭하는 숫자를 반환한다.

+ `String.prototype.normalize(form? : string)` : string
  
  Different combinations of code points may look the same. Unicode normalization changes them all to the same value(s), their so-called canonical representation. That helps with comparing and searching for strings. The 'NFC' form is recommended for general text.  
  서로 다른 코드포인트의 조합이 다른 조합의 결과와 똑같이 보이는 경우가 있다. 유니코드 정규화nomalization는 이들을 모두 규범적 표상canonical representation이라 불리는 특정 규칙(정규화 양식)에 따라 동일한 값으로 치환한다. 정규화는 문자열을 검색할 때 유용하다. 일반적인 문서에 대해서는 'NFC' 정규화 양식이 추천된다.


#### Finding strings 문자열 검색

+ `String.prototype.startsWith(searchString, position=0) : boolean`

  Does the receiver start with searchString? position lets you specify where the string to be checked starts.  
  수신자가 `searchString`으로 시작하는가? `position`을 지정하면 확인하고자 하는 문자열이 시작할 지점을 특정할 수 있다.

+ `String.prototype.endsWith(searchString, endPosition=searchString.length) : boolean`

  Does the receiver end with searchString? endPosition lets you specify where the string to be checked ends.  
  수신자가 `searchString`으로 끝나는가? `endPosition`를 지정하면 확인하고자 하는 문자열이 끝나는 지점을 특정할 수 있다.

+ `String.prototype.includes(searchString, position=0) : boolean`
  
  Does the receiver contain searchString? position lets you specify where the string to be searched starts.  
  수신자가 `searchString`을 포함하고 있는가? `position`을 지정하면 확인하고자 하는 문자열을 검색할 시작점을 특정할 수 있다.


#### Repeating strings 문자열 반복

+ `String.prototype.repeat(count) : string`

  Returns the receiver, concatenated count times.  
  수신자를 `count`에 지정한 횟수만큼 반복 나열한 긴 문자열을 반환한다.

