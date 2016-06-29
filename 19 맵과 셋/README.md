# 19. Maps 과 Sets

## 19.1 Overview 개요

Among others, the following four data structures are new in ECMAScript 6  
: Map, WeakMap, Set and WeakSet.  
ES6에는 다음의 네가지 데이터 구조가 새로 추가되었다.  
: 맵Map, 위크맵WeakMap, 셋Set, 위크셋WeakSet.  

### 19.1.1 Maps
### 19.1.1 맵

The keys of a Map can be arbitrary values:  
맵의 키는 임의의 값일 수 있다.

```js
const map = new Map(); // create an empty Map 비어있는 맵을 생성한다.
const KEY = {};

map.set(KEY, 123);
map.get(KEY);       // 123
map.has(KEY);       // true
map.delete(KEY);    // true
map.has(KEY);       // false
```

You can use an Array (or any iterable) with [key, value] pairs to set up the initial data in the Map:  
[키, 값]의 쌍들로 구성된 배열(또는 다른 아무 이터러블)을 맵의 초기 데이터를 셋팅하는 데에 이용할 수 있다.

```js
const map = new Map([
    [ 1, 'one' ],
    [ 2, 'two' ],
    [ 3, 'three' ], // trailing comma is ignored 끝의 컴마는 무시된다.
]);
```


### 19.1.2 Sets
### 19.1.2 셋

A Set is a collection of unique elements.  
셋은 유일무이한 요소들로 이루어진 집합이다.

```js
const arr = [5, 1, 5, 7, 7, 5];
const unique = [...new Set(arr)];   // [ 5, 1, 7 ]
```
As you can see, you can initialize a Set with elements if you hand the constructor an iterable (`arr` in the example) over those elements.  
위처럼 이터러블한 객체(위 예제의 `arr`)를 생성자에 넘겨주면 그 요소들로 구성된 셋을 생성할 수 있다.

### 19.1.3 WeakMaps
### 19.1.3 위크맵

A WeakMap is a Map that doesn’t prevent its keys from being garbage-collected. That means that you can associate data with objects without having to worry about memory leaks. For example:  
위크맵은 자신의 키가 가비지 컬렉션에 수집되는 것을 막지 않는 맵이다. 즉 메모리 누수에 대한 걱정 없이 객체에 프라이빗 데이터를 연결할 수 있다. 예를 들면 다음과 같다.

```js
//----- Manage listeners
//----- 리스너 관리
const _objToListeners = new WeakMap();

function addListener(obj, listener) {
    if (! _objToListeners.has(obj)) {
        _objToListeners.set(obj, new Set());
    }
    _objToListeners.get(obj).add(listener);
}
function triggerListeners(obj) {
    const listeners = _objToListeners.get(obj);
    if (listeners) {
        for (const listener of listeners) {
            listener();
        }
    }
}

//----- Example: attach listeners to an object
//----- 예: 객체에 리스너 붙이기
const obj = {};
addListener(obj, () => console.log('hello'));
addListener(obj, () => console.log('world'));

//----- Example: trigger listeners
//----- 예: 리스너 호출
triggerListeners(obj);

// Output:
// hello
// world
```


## 19.2 Map
## 19.2 맵

> JavaScript has always had a very spartan standard library. Sorely missing was a data structure for mapping values to values. The best you can get in ECMAScript 5 is a Map from strings to arbitrary values, by abusing objects. Even then there are [several pitfalls](http://speakingjs.com/es5/ch17.html#_pitfalls_using_an_object_as_a_map) that can trip you up.
The `Map` data structure in ECMAScript 6 lets you use arbitrary values as keys and is highly welcome.  
자바스크립트는 늘 아주 엄격한 표준 라이브러리 규격을 갖고 있었다. 몹시 아쉬운 점은 값과 값들을 매핑하는 데이터구조가 없다는 것이었다. ECMAScript5에서 이러한 아쉬움을 해결하는 최선의 방법은 객체의 허점을 이용해 문자열에 임의의 값을 매핑하는 것이었다. 여기엔 실수를 유발하는 [몇가지 함정들](http://speakingjs.com/es5/ch17.html#_pitfalls_using_an_object_as_a_map)이 있음에도 말이다. ECMAScript 6의 `맵` 데이터구조는 임의의 값을 키로 쓸 수 있으며 이는 매우 환영할 일이다.


### 19.2.1 Basic operations
### 19.2.1 기본 동작들

##### Working with single entries:
##### 단일 엔트리에 대한 제어

```js
const map = new Map();
map.set('foo', 123);
map.get('foo');         // 123
map.has('foo');         // true
map.delete('foo');      // true
map.has('foo');         // false
```

##### Determining the size of a Map and clearing it:
##### 맵의 사이즈 결정 및 맵의 내용 제거

```js
const map = new Map();
map.set('foo', true);
map.set('bar', false);
map.size                // 2
map.clear();
map.size                // 0
```

### 19.2.2 Setting up a Map
### 19.2.2 맵 세팅하기

You can set up a Map via an iterable over key-value “pairs” (Arrays with 2 elements). One possibility is to use an Array (which is iterable):  
맵은 `[키, 값]`의 쌍(2개의 요소를 가진 배열)들로 이루어진 이터러블을 통해 구성할 수 있다. 한가지 가능성은 배열을 이용하는 것이다(배열은 이터러블하다).

```js
const map = new Map([
    [ 1, 'one' ],
    [ 2, 'two' ],
    [ 3, 'three' ], // trailing comma is ignored 끝의 컴마는 무시된다.
]);
```

Alternatively, the set() method is chainable:  
한편, `set()` 메소드는 메소드 체인으로 연결할 수 있다.

```js
const map = new Map()
.set(1, 'one')
.set(2, 'two')
.set(3, 'three');
```


### 19.2.3 Keys
### 19.2.3 키

Any value can be a key, even an object:  
어떤 값이든 키가 될 수 있다. 심지어 객체도.

```js
const map = new Map();

const KEY1 = {};
map.set(KEY1, 'hello');
console.log(map.get(KEY1));     // hello

const KEY2 = {};
map.set(KEY2, 'world');
console.log(map.get(KEY2));     // world
```


#### 19.2.3.1 What keys are considered equal?
#### 19.2.3.1 어떤 키들을 서로 동등하다고 여길까? 

Most Map operations need to check whether a value is equal to one of the keys. They do so via the internal operation [SameValueZero](http://www.ecma-international.org/ecma-262/6.0/#sec-samevaluezero), which works like ===, but considers NaN to be equal to itself.  
대부분의 맵 명령은 어떤 값이 자신의 키 중 하나와 동등한지 여부를 검토하는 과정을 거쳐야 한다. 이 과정은 [SameValueZero](http://www.ecma-international.org/ecma-262/6.0/#sec-samevaluezero)라는 내부조작을 통해 이루어지는데, 이는 대체로 `===`의 판단기준과 동일하지만, NaN과 NaN을 동등하다고 판단하는 점은 주의하자.

Let’s first see how === handles NaN:  
우선 `===` 가 NaN을 어떻게 여기는지를 살펴보자.

```js
NaN === NaN     //false
```

Conversely, you can use NaN as a key in Maps, just like any other value:  
위와 달리, 맵에는 NaN을 다른 값들과 마찬가지로 키로 할당할 수 있다.

```js
const map = new Map();
map.set(NaN, 123);
map.get(NaN);           // 123
```

Like ===, -0 and +0 are considered the same value. That is normally the best way to handle the two zeros (details are explained in “Speaking JavaScript”).  
`===`처럼, `-0`과 `+0`에 대해서도 동등한 값으로 여긴다. 이는 보통 두 `0`을 취급하는 최고의 방법이기 때문이다(자세한 설명은 [자바스크립트를 말하다](http://speakingjs.com/es5/ch11.html#two_zeros)를 참고할 것).

```js
map.set(-0, 123);
map.get(+0);            // 123
```

Different objects are always considered different. That is something that can’t be configured (yet), as explained later, in the FAQ.  
서로 다른 객체는 언제나 다른 값으로 여긴다. (아직은) 조건을 설정할 수 없는 내용이 있어서 그러한데, 이에 대해서는 아래의 [FAQ](#user-content-1962-why-cant-i-configure-how-maps-and-sets-compare-keys-and-values)절에서 설명하겠다.

```js
new Map().set({}, 1).set({}, 2).size  // 2
```

Getting an unknown key produces undefined:  
존재하지 않는 키의 값을 얻고자 하면 undefined가 반환된다.

```js
> new Map().get('asfddfsasadf')
undefined
```


### 19.2.4 Iterating over Maps
### 19.2.4 맵에 대한 이터레이팅

Let’s set up a Map to demonstrate how one can iterate over it.  
맵이 어떻게 이터레이트 되는지를 살펴보기 위해 맵을 세팅해보자.

```js
const map = new Map([
    [false, 'no'],
    [true,  'yes'],
]);
```

Maps record the order in which elements are inserted and honor that order when iterating over keys, values or entries.  
맵은 각 요소가 삽입된 순서대로 요소들을 저장(기록)하며, 키나 값 또는 엔트리를 이터레이트할 때에도 이 순서에 따른다.

#### 19.2.4.1 Iterables for keys and values
#### 19.2.4.1 키와 값에 대한 이터러블

keys() returns an iterable over the keys in the Map:  
`keys()`는 맵의 키에 대한 이터러블을 반환한다.

```js
for (const key of map.keys()) {
    console.log(key);
}
// Output:
// false
// true
```

values() returns an iterable over the values in the Map:  
`values()`는 맵의 값에 대한 이터러블을 반환한다.

```js
for (const value of map.values()) {
    console.log(value);
}
// Output:
// no
// yes
```

#### 19.2.4.2 Iterable for entries
#### 19.2.4.2 엔트리에 대한 이터러블

entries() returns the entries of the Map as an iterable over [key,value] pairs (Arrays).  
`entries()`는 맵의 [키, 값] 쌍(배열)에 대한 이터러블로 맵의 엔트리들을 반환한다.

```js
for (const entry of map.entries()) {
    console.log(entry[0], entry[1]);
}
// Output:
// false no
// true yes
```

Destructuring enables you to access the keys and values directly:  
해체를 이용하면 키와 값들에 직접 접근할 수 있다.

```js
for (const [key, value] of map.entries()) {
    console.log(key, value);
}
```

The default way of iterating over a Map is entries():  
맵의 기본 이터레이팅 방법은 `entries()`와 같다.

```js
> map[Symbol.iterator] === map.entries
true
```

Thus, you can make the previous code snippet even shorter:
따라서, 위의 코드조각을 더 짧게 만들 수 있다.

```js
for (const [key, value] of map) {
    console.log(key, value);
}
```


#### 19.2.4.3 Converting iterables (incl. Maps) to Arrays
#### 19.2.4.3 이터러블(맵 포함)을 배열로 변환하기

The spread operator (...) can turn an iterable into an Array. That lets us convert the result of Map.prototype.keys() (an iterable) into an Array:  
펼침연산자(...)를 이용하면 이터러블을 배열로 변환할 수 있다. 이에 따르면 `Map.prototype.keys()`(이터러블)결과로부터 배열을 얻을 수 있다.

```js
> const map = new Map().set(false, 'no').set(true, 'yes');
> [...map.keys()]
[ false, true ]
```

Maps are also iterable, which means that the spread operator can turn Maps into Arrays:  
맵 역시 이터러블하므로, 펼침연산자를 통해 배열로 변환할 수 있다.

```js
> const map = new Map().set(false, 'no').set(true, 'yes');
> [...map]
[ [ false, 'no' ],
  [ true, 'yes' ] ]
```


### 19.2.5 Looping over Map entries
### 19.2.5 맵의 엔트리들에 대한 루프

The Map method forEach has the following signature:  
맵의 `forEach` 메소드는 다음과 같은 특징을 지닌다.

```js
Map.prototype.forEach((value, key, map) => void, thisArg?) : void
```

The signature of the first parameter mirrors the signature of the callback of Array.prototype.forEach, which is why the value comes first.  
첫번째 파라미터의 특징은 `Array.prototype.forEach`의 콜백의 특징과 같은데, 첫번째 요소로 값이 오는 것은 이 때문이다.

```js
const map = new Map([
    [false, 'no'],
    [true,  'yes'],
]);
map.forEach((value, key) => {
    console.log(key, value);
});
// Output:
// false no
// true yes
```


### 19.2.6 Mapping and filtering Maps  
### 19.2.6 매핑 및 필터링

You can map() and filter() Arrays, but there are no such operations for Maps. The solution is:  
배열에서는 `map()`이나 `filter()`을 할 수 있지만, 맵에는 이러한 명령어가 없다. 해결법은 다음과 같다.

- Convert the Map into an Array of [key,value] pairs.
- Map or filter the Array.
- Convert the result back to a Map.

- 맵을 [키, 값] 쌍으로 구성된 배열로 전환한다.
- 배열에 맵 또는 필터 메소드를 적용한다.
- 결과를 다시 맵으로 변환한다.


I’ll use the following Map to demonstrate how that works.  
다음의 맵으로 해결과정을 시연해보겠다.

```js
const originalMap = new Map()
.set(1, 'a')
.set(2, 'b')
.set(3, 'c');
```

Mapping originalMap:  
원본맵을 매핑하기

```js
const mappedMap = new Map( // step 3
    [...originalMap] // step 1
    .map(([k, v]) => [k * 2, '_' + v]) // step 2
);
// Resulting Map: {2 => '_a', 4 => '_b', 6 => '_c'}
```

Filtering originalMap:  
원본맵을 필터링하기

```js
const filteredMap = new Map( // step 3
    [...originalMap] // step 1
    .filter(([k, v]) => k < 3) // step 2
);
// Resulting Map: {1 => 'a', 2 => 'b'}
```

Step 1 is performed by the spread operator (...) which I have explained previously.  
1단계는 앞서 설명한대로 펼침 연산자(`...`)에 의해 수행된다.


### 19.2.7 Combining Maps
### 19.2.7 맵의 결합

There are no methods for combining Maps, which is why the approach from the previous section must be used to do so.  
여러 맵을 하나로 결합해주는 메소드는 없으므로, 앞 절의 접근법을 따라야 할 것이다.

Let’s combine the following two Maps:  
다음 두 맵을 결합해보자.

```js
const map1 = new Map()
.set(1, 'a1')
.set(2, 'b1')
.set(3, 'c1');

const map2 = new Map()
.set(2, 'b2')
.set(3, 'c2')
.set(4, 'd2');
```

To combine map1 and map2, I turn them into Arrays via the spread operator (...) and concatenate those Arrays. Afterwards, I convert the result back to a Map. All of that is done in the first line.  
맵1과 맵2를 결합하기 위해, 두 맵을 각각 펼침 연산자를 이용해 배열로 전환하고, 이 배열을 병합한다. 그 다음, 이 결과를 다시 맵으로 전환한다. 이상의 동작이 첫번째 줄에서 모두 이루어진다.

```js
const combinedMap = new Map([...map1, ...map2])
[...combinedMap] // convert to Array to display 출력을 위한 배열 변환
// [[1,"a1"],[2,"b2"],[3,"c2"],[4, "d2"]]
```



### 19.2.8 Arbitrary Maps as JSON via Arrays of pairs
### 19.2.8 [키, 값] 쌍으로 구성된 배열로부터 임의의 맵을 생성하여 JSON화 하기

If a Map contains arbitrary (JSON-compatible) data, we can convert it to JSON by encoding it as an Array of key-value pairs (2-element Arrays). Let’s examine first how to achieve that encoding.  
맵이 임의의 JSON 호환 데이터로 구성되어 있다면, 이를 [키, 값] 쌍으로 구성된 배열로 인코딩하여 JSON 형식으로 전환할 수 있다.


#### 19.2.8.1 Converting Maps to and from Arrays of pairs  
#### 19.2.8.1 [키, 값] 쌍으로 구성된 배열과 맵 간의 상호 전환

The spread operator lets you convert a Map to an Array of pairs:  
펼침 연산자는 맵을 [키,값] 쌍으로 구성된 배열로 전환해준다.

```js
const myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
[...myMap]
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```

The Map constructor lets you convert an Array of pairs to a Map:  
맵 생성자는 [키,값] 쌍으로 구성된 배열을 맵으로 전환해준다.

```js
new Map([[true, 7], [{foo: 3}, ['abc']]])
// Map {true => 7, Object {foo: 3} => ['abc']}
```


#### 19.2.8.2 The conversion to and from JSON
#### 19.2.8.2 맵과 JSON 간의 상호 전환

Let’s use this knowledge to convert any Map with JSON-compatible data to JSON and back:  
위 지식을 활용하여 JSON 호환 데이터를 가진 임의의 맵을 JSON으로, 혹은 JSON을 맵으로 전환해보자.

```js
function mapToJson(map) {
    return JSON.stringify([...map]);
}
function jsonToMap(jsonStr) {
    return new Map(JSON.parse(jsonStr));
}
```

The following interaction demonstrates how these functions are used:  
다음 상호작용은 위 함수들을 어떻게 활용하지를 보여준다.

```js
const myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);

mapToJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'

jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']}
```


### 19.2.9 String Maps as JSON via objects
### 19.2.9 객체를 이용한 문자열 키로 구성된 맵의 JSON화

Whenever a Map only has strings as keys, you can convert it to JSON by encoding it as an object. Let’s examine first how to achieve that encoding.  
맵에 오직 문자열 키만 존재할 경우에는 언제나 이를 객체로 인코딩하여 JSON으로 전환할 수 있다. 우선 어떻게 인코딩이 이루어지는지 살펴보자.

#### 19.2.9.1 Converting a string Map to and from an object
#### 19.2.9.1 문자열 키로 구성된 맵과 객체 간의 상호 전환

The following two function convert string Maps to and from objects:  
다음 두 함수는 문자열 맵을 객체로, 또는 객체를 문자열 맵으로 전환한다.

```js
function strMapToObj(strMap) {
    const obj = Object.create(null);
    for (const [k,v] of strMap) {
        // `We don’t escape the key '__proto__' which can cause problems on older engines`
        // 구 엔진에서 문제가 될 수 있으므로 '__proto__'키를 건너뛰지 않았다.
        obj[k] = v;
    }
    return obj;
}
function objToStrMap(obj) {
    const strMap = new Map();
    for (const k of Object.keys(obj)) {
        strMap.set(k, obj[k]);
    }
    return strMap;
}
```

Let’s use these two functions:  
위의 두 함수를 사용해보자:

```js
const myMap = new Map().set('yes', true).set('no', false);

strMapToObj(myMap)
// { yes: true, no: false }

objToStrMap({yes: true, no: false})
// [['yes', true], ['no', false]]
```


#### 19.2.9.2 The conversion to and from JSON
#### 19.2.9.2 맵과 JSON 간의 상호 전환

With these helper functions, the conversion to JSON works as follows:  
위의 두 헬퍼 함수를 활용하면 JSON과의 형변환은 다음과 같이 동작한다.

```js
function strMapToJson(strMap) {
    return JSON.stringify(strMapToObj(strMap));
}
function jsonToStrMap(jsonStr) {
    return objToStrMap(JSON.parse(jsonStr));
}
```

This is an example of using these functions:  
다음은 위 함수들을 적용한 예시이다.

```js
const myMap = new Map().set('yes', true).set('no', false);

strMapToJson(myMap)
// '{"yes":true,"no":false}'

jsonToStrMap('{"yes":true,"no":false}');
// Map {'yes' => true, 'no' => false}
```


### 19.2.10 Map API
### 19.2.10 맵 API

#### Constructor
#### 생성자
- `new Map(entries? : Iterable<[any,any]>)`  
  If you don’t provide the parameter iterable then an empty Map is created. If you do provide an iterable over [key, value] pairs then those pairs are used to add entries to the Map. For example:  
  이터러블한 파라미터를 제공하지 않으면 빈 맵이 생성된다. [키, 값] 쌍으로 구성된 이터러블을 제공할 경우 이 쌍들이 맵에 엔트리들을 추가하는데, 예를 들면 다음과 같다.

  ```js
  const map = new Map([
    [ 1, 'one' ],
    [ 2, 'two' ],
    [ 3, 'three' ], // trailing comma is ignored 끝의 컴마는 무시된다.
  ]);
  ```

#### Handling single entries :
#### 개별 엔트리에 대한 제어
- `Map.prototype.get(key)` : any  
  Returns the value that key is mapped to in this Map. If there is no key key in this Map, undefined is returned.
  키에 매핑된 값을 리턴한다. 맵에 해당 키가 없으면 undefined를 반환한다.

- `Map.prototype.set(key, value)` : this  
  Maps the given key to the given value. If there is already an entry whose key is key, it is updated. Otherwise, a new entry is created. This method returns this, which means that you can chain it.  
  지정한 키에 지정한 값을 매핑한다. 이미 키가 존재한다면 해당 키에 값을 업데이트하고, 존재하지 않는다면 새로운 엔트리를 생성한다. 이 메소드는 this를 리턴하므로, 메소드 체이닝이 가능하다.
  
- `Map.prototype.has(key)` : boolean  
  Returns whether the given key exists in this Map.  
  맵에 해당 키가 존재하는지 여부를 반환한다.
  
- `Map.prototype.delete(key)` : boolean
  If there is an entry whose key is key, it is removed and true is returned. Otherwise, nothing happens and false is returned.
  맵에 해당 키가 존재하면 이를 삭제하면서 true를 반환하고, 존재하지 않는다면 false를 반환한다.


#### Handling all entries :
#### 엔트리들에 대한 일괄 제어

- `get Map.prototype.size` : number  
  Returns how many entries there are in this Map.  
  맵에 몇개의 엔트리가 들어있는지를 반환한다.

- `Map.prototype.clear()` : void  
  Removes all entries from this Map.  
  맵의 모든 엔트리를 제거한다.


#### Iterating and looping: happens in the order in which entries were added to a Map.
#### 이터레이팅과 루프 동작 : 맵에 엔트리가 추가된 순서대로 발생한다.

- `Map.prototype.entries()` : Iterable<[any,any]>  
  Returns an iterable with one [key,value] pair for each entry in this Map. The pairs are Arrays of length 2.  
  맵의 [키,밸류] 쌍의 각 엔트리를 반환한다. 쌍은 길이가 2인 배열이다.

- `Map.prototype.forEach((value, key, collection) => void, thisArg?)` : void  
  The first parameter is a callback that is invoked once for each entry in this Map. If thisArg is provided, this is set to it for each invocation. Otherwise, this is set to undefined.  
  첫번째 파라미터는 맵 내의 각 엔트리당 한 번씩 호출되는 콜백함수이다. `thisArg`를 지정한 경우, 콜백 호출시마다 `this`에 `thisArg`이 대입되고, 그렇지 않은 경우에는 undefined가 대입된다.

- `Map.prototype.keys()` : Iterable<any>  
  Returns an iterable over all keys in this Map.  
  맵 내의 모든 키에 대한 이터러블을 반환한다.

- `Map.prototype.values()` : Iterable<any>  
  Returns an iterable over all values in this Map.  
  맵 내의 모든 값에 대한 이터러블을 반환합니다.

- `Map.prototype[Symbol.iterator]()` : Iterable<[any,any]>  
  The default way of iterating over Maps. Refers to Map.prototype.entries.
  맵을 이터레이트하는 기본 방식(`Map.prototype.entries()` 호출)을 제공한다. 


## 19.3 WeakMap
## 19.3 위크맵

WeakMaps work mostly like Maps, with the following differences:  
위크맵은 다음 몇가지 차이를 제외하고는 대체로 맵과 동일하게 동작한다.

- WeakMap keys are objects (values can be arbitrary values)  
  위크맵의 키는 객체이다(값은 어떤것이든 무관).

- WeakMap keys are weakly held  
  위크맵의 키들은 위크맵에 약하게 연결된다. 

- You can’t get an overview of the contents of a WeakMap  
  위크맵 내의 전체 내용을 살펴볼 수 있는 메소드는 없다.

- You can’t clear a WeakMap  
  위크맵은 내용은 일괄 삭제(clear)할 수 없다.

The following sections explain each of these differences.  
다음 절부터는 이러한 차이들을 설명하겠다.


### 19.3.1 WeakMap keys are objects
### 19.3.1 위크맵의 키는 객체입니다.

If you add an entry to a WeakMap then the key must be an object:  
위크맵에 엔트리를 추가할 때, 그 엔트리의 키는 반드시 객체여야 한다.

```js
const wm = new WeakMap()

wm.set('abc', 123); // TypeError
wm.set({}, 123); // ok
```


### 19.3.2 WeakMap keys are weakly held
### 19.3.2 위크맵의 키는 약하게 연결되어 있다.

The keys in a WeakMap are weakly held: Normally, an object that isn’t referred to by any storage location (variable, property, etc.) can be garbage-collected. WeakMap keys do not count as storage locations in that sense. In other words: an object being a key in a WeakMap does not prevent the object being garbage-collected.  
위크맵의 키는 약하게 연결되어 있다. 보통 어떠한 저장장소(변수, 프로퍼티 등)에서도 언급되지 않는 객체는 가비지 컬렉션의 대상이 될 수 있다. 이런 맥락에서 위크맵의 키는 저장장소로 간주되지 않는다. 다시 말해 위크맵의 키로 할당된 객체는 가비지에 수집되는 것을 막지 않는다.

Additionally, once a key is gone, its entry will also disappear (eventually, but there is no way to detect when, anyway).  
또한, 일단 키가 사라지면, 해당 엔트리 역시 사라질 것이다(결국에는. 다만 어차피 언제 사라지는지를 알아낼 방법은 없다).


### 19.3.3 You can’t get an overview of a WeakMap or clear it
### 19.3.3 위크맵의 내용은 전체를 살펴보거나 일괄 삭제할 수 없다.

It is impossible to inspect the innards of a WeakMap, to get an overview of them. That includes not being able to iterate over keys, values or entries. Put differently: to get content out of a WeakMap, you need a key. There is no way to clear a WeakMap, either (as a work-around, you can create a completely new instance).  
위크맵의 내용 전체를 살펴보기 위해 점검하는 것은 불가능하다. 키에 대해 이터레이트하는 것이 불가능할 뿐 아니라, 값이나 엔트리에 대해서도 마찬가지이다. 바꿔말해, 위크맵의 내용을 얻기 위해서는 키가 필요하다. 또한 위크맵의 내용을 일괄 삭제하는 것도 불가능하다(제2의 해결책으로, 완전히 새로운 인스턴스를 생성할 수는 있다).

These restrictions enable a security property. Quoting Mark Miller: “The mapping from weakmap/key pair value can only be observed or affected by someone who has both the weakmap and the key. With clear(), someone with only the WeakMap would’ve been able to affect the WeakMap-and-key-to-value mapping.”  
이러한 제한은 프로퍼티 보안을 가능케 한다. 마크 밀러Mark Miller의 말을 인용해보면, "위크맵/키 쌍에 대한 값의 할당은 오직 위크맵과 키 모두를 가진 자에 의해서만 관찰되거나 영향을 받을 수 있다."

Additionally, iteration would be difficult to implement, because you’d have to guarantee that keys remain weakly held.


### 19.3.4 Use cases for WeakMaps

WeakMaps are useful for associating data with objects whose life cycle you can’t (or don’t want to) control. In this section, we look at two examples:

    Caching computed results
    Managing listeners
    Keeping private data


#### 19.3.4.1 Caching computed results via WeakMaps

With WeakMaps, you can associate previously computed results with objects, without having to worry about memory management. The following function countOwnKeys is an example: it caches previous results in the WeakMap cache.
```js
const cache = new WeakMap();
function countOwnKeys(obj) {
    if (cache.has(obj)) {
        console.log('Cached');
        return cache.get(obj);
    } else {
        console.log('Computed');
        const count = Object.keys(obj).length;
        cache.set(obj, count);
        return count;
    }
}
```
If we use this function with an object obj, you can see that the result is only computed for the first invocation, while a cached value is used for the second invocation:
```js
> const obj = { foo: 1, bar: 2};
> countOwnKeys(obj)
Computed
2
> countOwnKeys(obj)
Cached
2
```
#### 19.3.4.2 Managing listeners

Let’s say we want to register listeners for objects without changing the objects. That way, we can even register listeners for immutable objects.

This is how to do that:
```js
const _objToListeners = new WeakMap();

function addListener(obj, listener) {
    if (! _objToListeners.has(obj)) {
        _objToListeners.set(obj, new Set());
    }
    _objToListeners.get(obj).add(listener);
}

function triggerListeners(obj) {
    const listeners = _objToListeners.get(obj);
    if (listeners) {
        for (const listener of listeners) {
            listener();
        }
    }
}
```
This is how you use these functions:
```js
const obj = {};
addListener(obj, () => console.log('hello'));
addListener(obj, () => console.log('world'));
triggerListeners(obj);

// Output:
// hello
// world
```
The advantage of using a WeakMap here is that, once an object is garbage-collected, its listeners will be garbage-collected, too. In other words: there won’t be any memory leaks.
#### 19.3.4.3 Keeping private data via WeakMaps

In the following code, the WeakMaps _counter and _action are used to store the data of virtual properties of instances of Countdown:
```js
const _counter = new WeakMap();
const _action = new WeakMap();
class Countdown {
    constructor(counter, action) {
        _counter.set(this, counter);
        _action.set(this, action);
    }
    dec() {
        let counter = _counter.get(this);
        if (counter < 1) return;
        counter--;
        _counter.set(this, counter);
        if (counter === 0) {
            _action.get(this)();
        }
    }
}
```
More information on this technique is given in the chapter on classes.
### 19.3.5 WeakMap API

The constructor and the four methods of WeakMap work the same as their Map equivalents:
```js
new WeakMap(entries? : Iterable<[any,any]>)

WeakMap.prototype.get(key) : any
WeakMap.prototype.set(key, value) : this
WeakMap.prototype.has(key) : boolean
WeakMap.prototype.delete(key) : boolean
```
## 19.4 Set

ECMAScript 5 doesn’t have a Set data structure, either. There are two possible work-arounds:

    Use the keys of an object to store the elements of a set of strings.
    Store (arbitrary) set elements in an Array: Check whether it contains an element via indexOf(), remove elements via filter(), etc. This is not a very fast solution, but it’s easy to implement. One issue to be aware of is that indexOf() can’t find the value NaN.

ECMAScript 6 has the data structure Set which works for arbitrary values, is fast and handles NaN correctly.
### 19.4.1 Basic operations

Managing single elements:
```js
> const set = new Set();
> set.add('red')

> set.has('red')
true
> set.delete('red')
true
> set.has('red')
false
```
Determining the size of a Set and clearing it:
```js
> const set = new Set();
> set.add('red')
> set.add('green')

> set.size
2
> set.clear();
> set.size
0
```
### 19.4.2 Setting up a Set

You can set up a Set via an iterable over the elements that make up the Set. For example, via an Array:
```js
const set = new Set(['red', 'green', 'blue']);
```
Alternatively, the add method is chainable:
```js
const set = new Set().add('red').add('green').add('blue');
```
#### 19.4.2.1 Pitfall: new Set() has at most one argument

The Set constructor has zero or one arguments:

    Zero arguments: an empty Set is created.
    One argument: the argument needs to be iterable; the iterated items define the elements of the Set.

Further arguments are ignored, which may lead to unexpected results:
```js
> Array.from(new Set(['foo', 'bar']))
[ 'foo', 'bar' ]
> Array.from(new Set('foo', 'bar'))
[ 'f', 'o' ]
```
For the second Set, only 'foo' is used (which is iterable) to define the Set.
### 19.4.3 Comparing Set elements

As with Maps, elements are compared similarly to ===, with the exception of NaN being like any other value.
```js
> const set = new Set([NaN]);
> set.size
1
> set.has(NaN)
true
```
Adding an element a second time has no effect:
```js
> const set = new Set();

> set.add('foo');
> set.size
1

> set.add('foo');
> set.size
1
```
Similarly to ===, two different objects are never considered equal (which can’t currently be customized, as explained later, in the FAQ, later):
```js
> const set = new Set();

> set.add({});
> set.size
1

> set.add({});
> set.size
2
```
### 19.4.4 Iterating

Sets are iterable and the for-of loop works as you’d expect:
```js
const set = new Set(['red', 'green', 'blue']);
for (const x of set) {
    console.log(x);
}
// Output:
// red
// green
// blue
```
As you can see, Sets preserve iteration order. That is, elements are always iterated over in the order in which they were inserted.

The previously explained spread operator (...) works with iterables and thus lets you convert a Set to an Array:
```js
const set = new Set(['red', 'green', 'blue']);
const arr = [...set]; // ['red', 'green', 'blue']
```
We now have a concise way to convert an Array to a Set and back, which has the effect of eliminating duplicates from the Array:
```js
const arr = [3, 5, 2, 2, 5, 5];
const unique = [...new Set(arr)]; // [3, 5, 2]
```
### 19.4.5 Mapping and filtering

In contrast to Arrays, Sets don’t have the methods map() and filter(). A work-around is to convert them to Arrays and back.

Mapping:
```js
const set = new Set([1, 2, 3]);
set = new Set([...set].map(x => x * 2));
// Resulting Set: {2, 4, 6}
```
Filtering:
```js
const set = new Set([1, 2, 3, 4, 5]);
set = new Set([...set].filter(x => (x % 2) == 0));
// Resulting Set: {2, 4}
```
### 19.4.6 Union, intersection, difference

ECMAScript 6 Sets have no methods for computing the union (e.g. addAll), intersection (e.g. retainAll) or difference (e.g. removeAll). This section explains how to work around that limitation.
#### 19.4.6.1 Union

Union (a ∪ b): create a Set that contains the elements of both Set a and Set b.
```js
const a = new Set([1,2,3]);
const b = new Set([4,3,2]);
const union = new Set([...a, ...b]);
    // {1,2,3,4}
```
The pattern is always the same:

    Convert one or both Sets to Arrays.
    Perform the operation.
    Convert the result back to a Set.

The spread operator (...) inserts the elements of something iterable (such as a Set) into an Array. Therefore, [...a, ...b] means that a and b are converted to Arrays and concatenated. It is equivalent to [...a].concat([...b]).
#### 19.4.6.2 Intersection

Intersection (a ∩ b): create a Set that contains those elements of Set a that are also in Set b.
```js
const a = new Set([1,2,3]);
const b = new Set([4,3,2]);
const intersection = new Set(
    [...a].filter(x => b.has(x)));
    // {2,3}
```
Steps: Convert a to an Array, filter the elements, convert the result to a Set.
#### 19.4.6.3 Difference

Difference (a \ b): create a Set that contains those elements of Set a that are not in Set b. This operation is also sometimes called minus (-).
```js
const a = new Set([1,2,3]);
const b = new Set([4,3,2]);
const difference = new Set(
    [...a].filter(x => !b.has(x)));
    // {1}
```
### 19.4.7 Set API

Constructor:

    new Set(elements? : Iterable<any>)
    If you don’t provide the parameter iterable then an empty Set is created. If you do then the iterated values are added as elements to the Set. For example:
```js
      const set = new Set(['red', 'green', 'blue']);
```
Single Set elements:

    Set.prototype.add(value) : this
    Adds value to this Set. This method returns this, which means that it can be chained.
    Set.prototype.has(value) : boolean
    Checks whether value is in this Set.
    Set.prototype.delete(value) : boolean
    Removes value from this Set.

All Set elements:

    get Set.prototype.size : number
    Returns how many elements there are in this Set.
    Set.prototype.clear() : void
    Removes all elements from this Set.

Iterating and looping:

    Set.prototype.values() : Iterable<any>
    Returns an iterable over all elements of this Set.
    Set.prototype[Symbol.iterator]() : Iterable<any>
    The default way of iterating over Sets. Points to Set.prototype.values.
    Set.prototype.forEach((value, key, collection) => void, thisArg?)
    Loops over the elements of this Set and invokes the callback (first parameter) for each one. value and key are both set to the element, so that this method works similarly to Map.prototype.forEach. If thisArg is provided, this is set to it for each call. Otherwise, this is set to undefined.

Symmetry with Map: The following two methods only exist so that the interface of Sets is similar to the interface of Maps. Each Set element is handled as if it were a Map entry whose key and value are the element.
```js
    Set.prototype.entries() : Iterable<[any,any]>
    Set.prototype.keys() : Iterable<any>
```
entries() allows you to convert a Set to a Map:
```js
const set = new Set(['a', 'b', 'c']);
const map = new Map(set.entries());
    // Map { 'a' => 'a', 'b' => 'b', 'c' => 'c' }
```
## 19.5 WeakSet

A WeakSet is a Set that doesn’t prevent its elements from being garbage-collected. Consult the section on WeakMap for an explanation of why WeakSets don’t allow iteration, looping and clearing.
### 19.5.1 Use cases for WeakSets

Given that you can’t iterate over their elements, there are not that many use cases for WeakSets. They do enable you to mark objects.

For example, if you have a factory function for proxies, you can use a WeakSet to record which objects were created by that factory:
```js
const proxies = new WeakSet();

function createProxy(obj) {
    const proxy = ···;
    proxies.add(proxy);
    return proxy;
}

function isProxy(obj) {
    return proxies.has(obj);
}
```
The complete example is shown in the chapter on proxies.
### 19.5.2 WeakSet API

The constructor and the three methods of WeakSet work the same as their Set equivalents:
```js
new WeakSet(elements? : Iterable<any>)

WeakSet.prototype.add(value)
WeakSet.prototype.has(value)
WeakSet.prototype.delete(value)
```
## 19.6 FAQ: Maps and Sets
### 19.6.1 Why do Maps and Sets have the property size and not length?

Arrays have the property length to count the number of entries. Maps and Sets have a different property, size.

The reason for this difference is that length is for sequences, data structures that are indexable – like Arrays. size is for collections that are primarily unordered – like Maps and Sets.

### 19.6.2 Why can’t I configure how Maps and Sets compare keys and values?
### 19.6.2 어째서 맵이나 셋에서 키나 값을 비교하는 방법을 임의로 설정하지 못하나?

It would be nice if there were a way to configure what Map keys and what Set elements are considered equal. But that feature has been postponed, as it is difficult to implement properly and efficiently.
### 19.6.3 Is there a way to specify a default value when getting something out of a Map?

If you use a key to get something out of a Map, you’d occasionally like to specify a default value that is returned if the key is not in the Map. ES6 Maps don’t let you do this directly. But you can use the Or operator (||), as demonstrated in the following code. countChars returns a Map that maps characters to numbers of occurrences.
```js
function countChars(chars) {
    const charCounts = new Map();
    for (const ch of chars) {
        ch = ch.toLowerCase();
        const prevCount = charCounts.get(ch) || 0; // (A)
        charCounts.set(ch, prevCount+1);
    }
    return charCounts;
}
```
In line A, the default 0 is used if ch is not in the charCounts and get() returns undefined.
### 19.6.4 When should I use a Map, when an object?

If you map anything other than strings to any kind of data, you have no choice: you must use a Map.

If, however, you are mapping strings to arbitrary data, you must decide whether or not to use an object. A rough general guideline is:

    Is there a fixed set of keys (known at development time)?
    Then use an object and access the values via fixed keys: obj.key
    Can the set of keys change at runtime?
    Then use a Map and access the values via keys stored in variables: map.get(theKey)

### 19.6.5 When would I use an object as a key in a Map?

Map keys mainly make sense if they are compared by value (the same “content” means that two values are considered equal, not the same identity). That excludes objects. There is one use case – externally attaching data to objects, but that use case is better served by WeakMaps where an entry goes away when the key disappears.
