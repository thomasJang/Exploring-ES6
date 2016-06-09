# 19. Maps 과 Sets

## 19.1 개요 `Overview`

ES6에는 다음의 네가지 데이터 구조가 새로 추가되었습니다.
: Map, WeakMap, Set, WeakSet
> Among others, the following four data structures are new in ECMAScript 6: Map, WeakMap, Set and WeakSet.

### 19.1.1 Maps

맵의 키값은 임의의 값일 수 있습니다.
> The keys of a Map can be arbitrary values:

```js
const map = new Map(); // 비어있는 맵을 생성합니다. `create an empty Map`
const KEY = {};

map.set(KEY, 123);
map.get(KEY);       // 123
map.has(KEY);       // true
map.delete(KEY);    // true
map.has(KEY);       // false
```

배열(또는 이터러블한 객체이면 어떤 것이든 무관)에 대해 [key, value]로 짝지어진 데이터 묶음으로 맵의 초기 데이터를 셋팅할 수 있습니다.
> You can us an Array (or any iterable) with [key, value] pairs to set up the initial data in the Map:

```js
const map = new Map([
    [ 1, 'one' ],
    [ 2, 'two' ],
    [ 3, 'three' ], // 끝의 컴마는 무시됩니다. `trailing comma is ignored`
]);
```


### 19.1.2 Sets

셋은 각 요소들이 유일무이한 값들로 이루어진 컬렉션입니다.
> A Set is a collection of unique elements

```js
const arr = [5, 1, 5, 7, 7, 5];
const unique = [...new Set(arr)];   // [ 5, 1, 7 ]
```
위와 같이 이터러블한 객체(예를 들면 배열)를 생성자에 넘겨줌으로써, 이 객체 내의 요소들을 원소로 하는 Set을 생성할 수 있습니다.
> As you can see, you can initialize a Set with elements if you hand the constructor an iterable (`arr` in the example) over those elements.

### 19.1.3 WeakMaps

위크맵은 자신의 키가 가비지 컬렉션에 수집되는 것을 막지 못하는 맵입니다. 이는 메모리 누수에 대한 걱정 없이 프라이빗 데이터를 객체에 연결할 수 있음을 의미합니다.
> A WeakMap is a Map that doesn’t prevent its keys from being garbage-collected. That means that you can associate data with objects without having to worry about memory leaks. For example:

```js
//----- 리스너 관리 `Manage listeners`
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

//----- 예: 객체에 리스너 붙이기 `Example: attach listeners to an object`
const obj = {};
addListener(obj, () => console.log('hello'));
addListener(obj, () => console.log('world'));

//----- 예: 리스너 호출 `Example: trigger listeners`
triggerListeners(obj);

// Output:
// hello
// world
```


## 19.2 Map

자바스크립트는 늘 아주 엄격한 표준 라이브러리를 정의해왔기 때문에, 늘 값과 값들을 매핑하는 데이터구조에 대한 강한 아쉬움이 따를 수밖에 없었습니다. ES5 이하에서 이러한 아쉬움을 해결하는 최선의 방법은 객체의 허점을 이용해 문자열과 임의의 값을 매핑하는 것이었습니다. 실수를 유발하는 [몇가지 함정들](http://speakingjs.com/es5/ch17.html#_pitfalls_using_an_object_as_a_map)에도 불구하고 말이지요. ES6에서는 맵에 문자열만이 아닌 다른 어떤 임의의 값이든 키로 쓸 수 있게 되었으니, 이는 매우 환영할 일입니다.
> JavaScript has always had a very spartan standard library. Sorely missing was a data structure for mapping values to values. The best you can get in ECMAScript 5 is a Map from strings to arbitrary values, by abusing objects. Even then there are [several pitfalls](http://speakingjs.com/es5/ch17.html#_pitfalls_using_an_object_as_a_map) that can trip you up.
The `Map` data structure in ECMAScript 6 lets you use arbitrary values as keys and is highly welcome.


### 19.2.1 기본 동작들 `Basic operations`

#####단일 엔트리에 대한 제어
> Working with single entries:

```js
const map = new Map();
map.set('foo', 123);
map.get('foo');         // 123
map.has('foo');         // true
map.delete('foo');      // true
map.has('foo');         // false
```

#####맵의 사이즈 결정 및 맵의 내용 제거
> Determining the size of a Map and clearing it:

```js
const map = new Map();
map.set('foo', true);
map.set('bar', false);
map.size                // 2
map.clear();
map.size                // 0
```


### 19.2.2 맵 세팅하기 `Setting up a Map`

`[키, 값]`의 쌍(2개의 인자를 가진 배열)들로 이루어진 이터러블을 통해 맵을 세팅할 수 있습니다. 예를 들어 배열은 이터러블하므로, [키, 값]의 쌍을 각 요소로 하는 배열을 파라미터로 넘김으로써 맵을 세팅할 수 있을 것입니다.
> You can set up a Map via an iterable over key-value “pairs” (Arrays with 2 elements). One possibility is to use an Array (which is iterable):

```js
const map = new Map([
    [ 1, 'one' ],
    [ 2, 'two' ],
    [ 3, 'three' ], // 끝의 컴마는 무시됩니다. `trailing comma is ignored`
]);
```

또한 `set()` 메서드 체인으로 지정하는 방법도 있습니다.
> Alternatively, the set() method is chainable:

```js
const map = new Map()
.set(1, 'one')
.set(2, 'two')
.set(3, 'three');
```


### 19.2.3 Keys
어떤 값이든 키로 할당할 수 있습니다. 심지어 객체도 키가 될 수 있습니다.
>Any value can be a key, even an object:

```js
const map = new Map();

const KEY1 = {};
map.set(KEY1, 'hello');
console.log(map.get(KEY1));     // hello

const KEY2 = {};
map.set(KEY2, 'world');
console.log(map.get(KEY2));     // world
```


#### 19.2.3.1 어떤 키들을 서로 동등하다고 여길까요? `What keys are considered equal?`

대부분의 맵 명령어들은 자신의 키 중 하나와 명령어의 파라미터 값이 동등한지 여부를 검토하는 과정을 거쳐야 합니다. 이 과정은 [SameValueZero](http://www.ecma-international.org/ecma-262/6.0/#sec-samevaluezero)라는 내부조작을 통해 이루어집니다. 대체로 `===`의 판단기준과 동일하지만, NaN과 NaN에 대해서는 언제나 서로 동등하다고 여긴다는 점은 `===`과 다릅니다.
>Most Map operations need to check whether a value is equal to one of the keys. They do so via the internal operation [SameValueZero](http://www.ecma-international.org/ecma-262/6.0/#sec-samevaluezero), which works like ===, but considers NaN to be equal to itself.

우선 `===` 가 NaN을 어떻게 여기는지를 살펴봅시다.
> Let’s first see how === handles NaN:

```js
NaN === NaN     //false
```

맵에서는 위와 달리 NaN을 다른 값들과 마찬가지로 키로 할당할 수 있습니다.
> Conversely, you can use NaN as a key in Maps, just like any other value:

```js
const map = new Map();
map.set(NaN, 123);
map.get(NaN);           // 123
```

한편 `-0`과 `+0`에 대해서는 `===`의 판단과 마찬가지로 동등한 값으로 여깁니다. 이는 일반적으로 오직 하나의 `0`만 존재하는 것으로 간주하는 편이 가장 효율적이기 때문입니다(자세한 설명은 [자바스크립트를 말하다](http://speakingjs.com/es5/ch11.html#two_zeros)를 참고하세요).
> Like ===, -0 and +0 are considered the same value. That is normally the best way to handle the two zeros (details are explained in “Speaking JavaScript”).

```js
map.set(-0, 123);
map.get(+0);            // 123
```

서로 다른 객체는 언제나 다르다고 봅니다. 이에 대해서는 아래의 [FAQ](#user-content-1962-어째서-맵이나-셋에서-키나-값을-비교하는-방법을-임의로-설정하지-못하나요)절에서 설명하겠습니다.
> Different objects are always considered different. That is something that can’t be configured (yet), as explained later, in the [FAQ](#user-content-1962-어째서-맵이나-셋에서-키나-값을-비교하는-방법을-임의로-설정하지-못하나요).

```js
new Map().set({}, 1).set({}, 2).size  // 2
```

`get()` 메서드로 존재하지 않는 키에 접근하면 undefined를 반환합니다.
> Getting an unknown key produces undefined:

```js
> new Map().get('asfddfsasadf')
undefined
```


### 19.2.4 맵에 대한 이터레이팅 `Iterating over Maps`

맵이 어떻게 이터레이트 되는지를 예제를 통해 살펴봅시다.
> Let’s set up a Map to demonstrate how one can iterate over it.

```js
const map = new Map([
    [false, 'no'],
    [true,  'yes'],
]);
```

맵은 각 요소가 삽입된 순서대로 요소들을 저장(기록)하며, 키나 값, 항목을 이터레이트할 때에도 역시 이 순서를 유지합니다.
> Maps record the order in which elements are inserted and honor that order when iterating over keys, values or entries.

#### 19.2.4.1 키와 값을 위한 이터러블 `Iterables for keys and values`

keys()는 맵의 키에 대한 이터러블을 반환합니다.
> keys() returns an iterable over the keys in the Map:

```js
for (const key of map.keys()) {
    console.log(key);
}
// Output:
// false
// true
```

values()는 맵의 값에 대한 이터러블을 반환합니다.
> values() returns an iterable over the values in the Map:

```js
for (const value of map.values()) {
    console.log(value);
}
// Output:
// no
// yes
```

#### 19.2.4.2 엔트리에 대한 이터러블 `Iterables for entries`

entries()는 맵의 엔트리들([키, 값]의 쌍으로 이루어진 배열)에 대한 이터러블을 반환합니다.

> entries() returns the entries of the Map as an iterable over [key,value] pairs (Arrays).

```js
for (const entry of map.entries()) {
    console.log(entry[0], entry[1]);
}
// Output:
// false no
// true yes
```

해체를 통해 키와 값에 바로 접근할 수 있습니다.
> Destructuring enables you to access the keys and values directly:

```js
for (const [key, value] of map.entries()) {
    console.log(key, value);
}
```

맵에 기본적으로 할당된 이터레이터는 entries()의 결과와 같습니다.
> The default way of iterating over a Map is entries():

```js
> map[Symbol.iterator] === map.entries
true
```

즉 entries() 대신 맵 자신에 대해서도 이터레이팅이 가능하므로, 코드를 더욱 짧게 만들 수 있습니다.
> Thus, you can make the previous code snippet even shorter:

```js
for (const [key, value] of map) {
    console.log(key, value);
}
```


#### 19.2.4.3 이터러블(맵 포함)을 배열로 변환하기
`Converting iterables (incl. Maps) to Arrays`

펼침연산자(...)를 이용하면 이터러블을 배열로 변환할 수 있습니다. `Map.prototype.keys()`은 이터러블이므로, 펼침연산자를 적용하면 그 결과물을 배열로 변환할 수 있습니다.
> The spread operator (...) can turn an iterable into an Array. That lets us convert the result of Map.prototype.keys() (an iterable) into an Array:

```js
> const map = new Map().set(false, 'no').set(true, 'yes');
> [...map.keys()]
[ false, true ]
```

맵은 그 자체가 이터러블하므로, 마찬가지로 펼침연산자를 통해 배열로 변환할 수 있습니다.
> Maps are also iterable, which means that the spread operator can turn Maps into Arrays:

```js
> const map = new Map().set(false, 'no').set(true, 'yes');
> [...map]
[ [ false, 'no' ],
  [ true, 'yes' ] ]
```


### 19.2.5 맵의 엔트리들에 대한 루프 `Looping over Map entries`

맵의 `forEach` 메서드는 다음과 같은 특징을 지닙니다.
> The Map method forEach has the following signature:

```js
Map.prototype.forEach((value, key, map) => void, thisArg?) : void
```
첫번째 파라미터는 `Array.prototype.forEach`의 콜백과 같습니다. 첫번째 파라미터의 첫번째 요소로 '값'이 오는 것은 이 때문입니다.
>The signature of the first parameter mirrors the signature of the callback of Array.prototype.forEach, which is why the value comes first.

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

### 19.2.6 매핑 및 필터링 `Mapping and filtering Maps`
맵에는 배열의 map()이나 filter() 역할을 수행하는 메서드가 없지만, 다음과 같은 방식으로 해결할 수 있습니다.
> You can map() and filter() Arrays, but there are no such operations for Maps. The solution is:

- 맵을 [키,밸류] 쌍의 배열로 전환합니다.
- 배열의 맵 / 필터 메서드를 적용합니다.
- 그 결과를 다시 맵으로 변환합니다.

> - Convert the Map into an Array of [key,value] pairs.
- Map or filter the Array.
- Convert the result back to a Map.

다음 예를 통해 동작을 시연해보겠습니다.
> I’ll use the following Map to demonstrate how that works.

```js
const originalMap = new Map()
.set(1, 'a')
.set(2, 'b')
.set(3, 'c');
```

원본맵을 매핑:
> Mapping originalMap:

```js
const mappedMap = new Map( // step 3
    [...originalMap] // step 1
    .map(([k, v]) => [k * 2, '_' + v]) // step 2
);
// Resulting Map: {2 => '_a', 4 => '_b', 6 => '_c'}
```

원본맵을 필터링:
> Filtering originalMap:

```js
const filteredMap = new Map( // step 3
    [...originalMap] // step 1
    .filter(([k, v]) => k < 3) // step 2
);
// Resulting Map: {1 => 'a', 2 => 'b'}
```

1단계는 펼침 연산자(`...`)에 의해 수행됩니다.
> Step 1 is performed by the spread operator (...) which I have explained previously.


### 19.2.7 맵의 결합 `Combining Maps`

여러 맵을 하나로 결합하는 메서드는 없습니다. 따라서 앞의 섹션과 비슷한 접근이 필요할 것입니다.
> There are no methods for combining Maps, which is why the approach from the previous section must be used to do so.

아래의 두 맵을 결합해봅시다:
> Let’s combine the following two Maps:

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

두 맵을 각각 펼침 연산자를 이용해 배열로 전환하고, 이 배열을 병합합니다. 그 다음 이 결과를 다시 맵으로 전환합니다. 이상의 동작이 첫번째 줄에서 이뤄집니다.
> To combine map1 and map2, I turn them into Arrays via the spread operator (...) and concatenate those Arrays. Afterwards, I convert the result back to a Map. All of that is done in the first line.

```js
const combinedMap = new Map([...map1, ...map2])
[...combinedMap] // 출력을 위한 배열 변환 `convert to Array to display`
// [[1,"a1"],[2,"b2"],[3,"c2"],[4, "d2"]]
```

### 19.2.8 [키, 값] 쌍으로 구성된 배열로부터 임의의 맵을 생성하여 JSON화 하기
`Arbitrary Maps as JSON via Arrays of pairs`

맵이 임의의 JSON 호환 데이터로 구성되어 있는 경우, 이를 [키, 값] 쌍으로 구성된 배열로 인코딩하여 JSON 형식으로 전환할 수 있습니다.
> If a Map contains arbitrary (JSON-compatible) data, we can convert it to JSON by encoding it as an Array of key-value pairs (2-element Arrays). Let’s examine first how to achieve that encoding.

#### 19.2.8.1 [키, 값] 쌍으로 구성된 배열과 맵 간의 상호 전환 `Converting Maps to and from Arrays of pairs`

펼침 연산자는 맵을 [키,값] 쌍으로 구성된 배열로 전환해줍니다:
> The spread operator lets you convert a Map to an Array of pairs:

```js
const myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);
[...myMap]
// [ [ true, 7 ], [ { foo: 3 }, [ 'abc' ] ] ]
```

맵 생성자는 [키,값] 쌍으로 구성된 배열을 맵으로 전환해줍니다:
> The Map constructor lets you convert an Array of pairs to a Map:

```js
new Map([[true, 7], [{foo: 3}, ['abc']]])
// Map {true => 7, Object {foo: 3} => ['abc']}
```


#### 19.2.8.2 맵과 JSON 간의 상호 전환 `The conversion to and from JSON`

JSON과 호환되는 데이터를 가진 임의의 맵을 JSON으로, 혹은 JSON을 맵으로 전환하기 위해 앞서 살펴본 내용을 활용해봅시다:
> Let’s use this knowledge to convert any Map with JSON-compatible data to JSON and back:

```js
function mapToJson(map) {
    return JSON.stringify([...map]);
}
function jsonToMap(jsonStr) {
    return new Map(JSON.parse(jsonStr));
}
```

다음은 위 함수들을 어떻게 적용하는지를 보여줍니다:
> The following interaction demonstrates how these functions are used:

```js
const myMap = new Map().set(true, 7).set({foo: 3}, ['abc']);

mapToJson(myMap)
// '[[true,7],[{"foo":3},["abc"]]]'

jsonToMap('[[true,7],[{"foo":3},["abc"]]]')
// Map {true => 7, Object {foo: 3} => ['abc']}
```


### 19.2.9 객체를 이용한 문자열 키로 구성된 맵의 JSON화 `String Maps as JSON via objects`

맵에 오직 문자열 키만 존재할 경우에는 이를 객체로 인코딩하여 JSON으로 전환할 수 있습니다. 어떻게 인코딩이 이루어지는지 살펴봅시다.
> Whenever a Map only has strings as keys, you can convert it to JSON by encoding it as an object. Let’s examine first how to achieve that encoding.

#### 19.2.9.1 문자열 키로 구성된 맵과 객체 간의 상호 전환 `Converting a string Map to and from an object`

아래의 두 함수는 문자열 키로 구성된 맵을 객체로, 또는 객체를 문자열 키로 구성된 맵으로 전환합니다:
> The following two function convert string Maps to and from objects:

```js
function strMapToObj(strMap) {
    const obj = Object.create(null);
    for (const [k,v] of strMap) {
        // 구 엔진에서 문제가 생길 수 있으므로 '__proto__' 키를 회피하지 않았습니다.
        // `We don’t escape the key '__proto__' which can cause problems on older engines`
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

위의 두 함수를 사용해봅시다:
> Let’s use these two functions:

```js
const myMap = new Map().set('yes', true).set('no', false);

strMapToObj(myMap)
// { yes: true, no: false }

objToStrMap({yes: true, no: false})
// [['yes', true], ['no', false]]
```


#### 19.2.9.2 맵과 JSON 간의 상호 전환 `The conversion to and from JSON`

제이슨과의 형변환은 위의 두 헬퍼 함수를 활용하면 됩니다.
> With these helper functions, the conversion to JSON works as follows:

```js
function strMapToJson(strMap) {
    return JSON.stringify(strMapToObj(strMap));
}
function jsonToStrMap(jsonStr) {
    return objToStrMap(JSON.parse(jsonStr));
}
```

다음은 위 함수를 적용한 예시입니다:
> This is an example of using these functions:

```js
const myMap = new Map().set('yes', true).set('no', false);

strMapToJson(myMap)
// '{"yes":true,"no":false}'

jsonToStrMap('{"yes":true,"no":false}');
// Map {'yes' => true, 'no' => false}
```


### 19.2.10 맵 API `Map API`

#### 생성자 `Constructor` :
- `new Map(entries? : Iterable<[any,any]>)`

  이터러블한 파라미터를 제공하지 않은 경우 빈 맵이 생성됩니다. [키, 값] 쌍으로 구성된 이터러블을 제공할 경우에는 이 쌍들이 맵의 엔트리로 추가되는데, 예를 들면 다음과 같습니다.
  > If you don’t provide the parameter iterable then an empty Map is created. If you do provide an iterable over [key, value] pairs then those pairs are used to add entries to the Map. For example:

  ```js
  const map = new Map([
    [ 1, 'one' ],
    [ 2, 'two' ],
    [ 3, 'three' ], // 끝의 컴마는 무시됩니다. `trailing comma is ignored`
  ]);
  ```

#### 개별 엔트리에 대한 제어 `Handling single entries` :
- `Map.prototype.get(key)` : any

  키에 매핑된 값을 리턴합니다. 맵에 해당 키가 없으면 undefined를 반환합니다.
  > Returns the value that key is mapped to in this Map. If there is no key key in this Map, undefined is returned.

- `Map.prototype.set(key, value)` : this

  이미 키가 존재한다면 해당 키에 값을 매핑하고, 존재하지 않는다면 새로운 [키, 값] 엔트리를 추가합니다. 이 메서드는 this를 리턴하므로, 메서드 체이닝이 가능합니다.
  > Maps the given key to the given value. If there is already an entry whose key is key, it is updated. Otherwise, a new entry is created. This method returns this, which means that you can chain it.
  
- `Map.prototype.has(key)` : boolean
  
  맵에 해당 키가 존재하는지 여부를 반환합니다.
  > Returns whether the given key exists in this Map.
  
- `Map.prototype.delete(key)` : boolean

  맵에 해당 키가 존재하면 이를 삭제하면서 true를 반환하고, 존재하지 않는다면 false를 반환합니다.
  > If there is an entry whose key is key, it is removed and true is returned. Otherwise, nothing happens and false is returned.

#### Handling all entries `엔트리들에 대한 일괄 제어` :
- `get Map.prototype.size` : number

  맵에 몇개의 엔트리가 들어있는지를 반환합니다.
  > Returns how many entries there are in this Map.

- `Map.prototype.clear()` : void
  
  맵의 모든 엔트리를 제거합니다.
  > Removes all entries from this Map.

#### 이터레이팅과 루프 동작 : 맵에 엔트리가 추가된 순서대로 발생합니다.
`Iterating and looping: happens in the order in which entries were added to a Map.`

- `Map.prototype.entries()` : Iterable<[any,any]>

  맵의 [키,밸류] 쌍의 각 엔트리를 반환합니다. 쌍의 배열은 언제나 길이가 2입니다.
  > Returns an iterable with one [key,value] pair for each entry in this Map. The pairs are Arrays of length 2.

- `Map.prototype.forEach((value, key, collection) => void, thisArg?)` : void

  첫번째 파라미터는 맵 내의 각 엔트리당 한 번씩 호출되는 콜백함수입니다. `thisArg`가 존재할 경우 각 콜백시마다 `this`에는 `thisArg`이 바인딩되고, 그렇지 않은 경우에는 undefined가 바인딩됩니다.
  > The first parameter is a callback that is invoked once for each entry in this Map. If thisArg is provided, this is set to it for each invocation. Otherwise, this is set to undefined.

- `Map.prototype.keys()` : Iterable<any>

  맵 내의 모든 키들에 대한 이터러블을 반환합니다.
  > Returns an iterable over all keys in this Map.

- `Map.prototype.values()` : Iterable<any>

  맵 내의 모든 값에 대한 이터러블을 반환합니다.
  > Returns an iterable over all values in this Map.

- `Map.prototype[Symbol.iterator]()` : Iterable<[any,any]>

  맵을 이터레이트하는 기본 방식(`Map.prototype.entries()` 호출)을 제공합니다. 
  > The default way of iterating over Maps. Refers to Map.prototype.entries.


## 19.3 WeakMap

위크맵은 다음 몇가지 차이를 제외하고는 대체로 맵과 동일하게 동작합니다.
> WeakMaps work mostly like Maps, with the following differences:

- 위크맵의 키는 객체이다 (값은 어떤것이든 무관하다).
  > WeakMap keys are objects (values can be arbitrary values)

- 위크맵의 키들은 위크맵에 약하게 연결된다. 
  > WeakMap keys are weakly held

- 위크맵 내의 전체 내용을 한 눈에 살펴볼 수 있는 메서드는 없다.
  > You can’t get an overview of the contents of a WeakMap

- 위크맵은 내용은 일괄 삭제(clear)할 수 없다.
  > You can’t clear a WeakMap

다음 절에서 위의 각 차이들에 대해 설명하겠다.
> The following sections explain each of these differences.


### 19.3.1 위크맵의 키는 객체입니다. `WeakMap keys are objects`

위크맵에 엔트리를 추가할 때, 그 엔트리의 키는 반드시 객체여야만 합니다.
> If you add an entry to a WeakMap then the key must be an object:

```js
const wm = new WeakMap()

wm.set('abc', 123); // TypeError
wm.set({}, 123); // ok
```
### 19.3.2 WeakMap keys are weakly held

The keys in a WeakMap are weakly held: Normally, an object that isn’t referred to by any storage location (variable, property, etc.) can be garbage-collected. WeakMap keys do not count as storage locations in that sense. In other words: an object being a key in a WeakMap does not prevent the object being garbage-collected.

Additionally, once a key is gone, its entry will also disappear (eventually, but there is no way to detect when, anyway).
### 19.3.3 You can’t get an overview of a WeakMap or clear it

It is impossible to inspect the innards of a WeakMap, to get an overview of them. That includes not being able to iterate over keys, values or entries. Put differently: to get content out of a WeakMap, you need a key. There is no way to clear a WeakMap, either (as a work-around, you can create a completely new instance).

These restrictions enable a security property. Quoting Mark Miller: “The mapping from weakmap/key pair value can only be observed or affected by someone who has both the weakmap and the key. With clear(), someone with only the WeakMap would’ve been able to affect the WeakMap-and-key-to-value mapping.”

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

### 19.6.2 어째서 맵이나 셋에서 키나 값을 비교하는 방법을 임의로 설정하지 못하나요?
> Why can’t I configure how Maps and Sets compare keys and values?

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
