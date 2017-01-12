# 8 템플릿 리터럴

## 8.1 개요
ES6은 두가지 종류의 템플릿을 가진다.: 템플릿 리터럴과 태그드템플릿 리터럴. 이 두 리터럴의 이름은 모양은 유사하나 아주 다르다. 이것을 구분하는것은 중요하다.:

* 웹 템플릿 (data) : 빈칸이 있는 HTML을 채움
* 템플릿 리터럴 (code) : 다중 라인과 보간
* 태그드 템플릿 리터럴 (code) : 함수 호출

템플릿  리터럴은 여러 줄을 걸칠수 있고 보간 표현법을 갖는 문자 리터럴이다.

```javascript
const firstName = 'Jane';
console.log(`Hello ${firstName}!
How are you
today?`);

// Output:
// Hello Jane!
// How are you
// today?
```

태그드 템플릿 리터럴 (짤게 말하면 태그드 템플릿)은 이전에 템플릿 리터럴 이전의 함수언급에 의해 생성되어진다.
```javascript
> String.raw`A \tagged\ template`
'A \\tagged\\ template'
```

태그드 템플릿은 함수 호출이라는 것을 아는것이 중요하다. 이전 예제에서 String.raw 메소드는 태그드 템플릿 결과를 생성하기 위해 불려진다.

## 8.2 소개
리터럴들은 값을 생성하는 구문 생성자다. 문자열 리터럴(문자열 생성)과 정규표현식 리터럴(정규표현식 생성) 포함된 예제가 있다. ECMAScript 6 은 두개의 리터럴들을 가진다.
* 템플릿 리터럴은 보간과 멀티라인을 지원하는 문자리터럴이다.
* 태그드 템플릿 리터럴 (짧게: 태그드 템플릿)은 함수 호출이고, 그 호출의 파라미터들은 템플릿 리터럴을 통해 제공되어 진다.

템플릿 리터럴과 태그드 템플릿의 이름들이 약간의 오해가 소지가 있을을 꼭 명심하기 바란다. 이것들은 우리가 종종 사용하는 웹 개발 템플릿(빈 텍스트 파일을 예를 들면 JSON으로 채우는 것)과 아무 상관없다.

### 8.2.1 템플릿 리터럴
템플릿 리터럴은 멀티라인과 보간 표현법을 할 수 있는 새로운 종류의 문자열 리터럴이다.
예를 들면:

```javascript
const firstName = 'Jane';
console.log(`Hello ${firstName}!
How are you
today?`);

// Output:
// Hello Jane!
// How are you
// today?
```

이 리터럴 스스로를 역 따옴표(`)로 구분하고, 그 리터럴 안에 보간 표현법은 ${와}로 구분된다. 템플릿 리터럴은 항상 문자열을 생성한다.

### 8.2.2 템플릿 리터럴에서 라인 종결자는 항상 LF(\n)

라인 종료의 일반적인 방법은:
* 라인 피드(LF, \n, U+000A): Unix에서 사용(OS X 포함)
* 캐리지 리턴(CR, \r, U+000D): 오래된 Mac OS에서 사용.
* CRLF (\r\n): 윈도우에서 사용.

템플릿 리터럴에서 모든 라인 종결자는 LF로 표준화 되어 있다. 그래서 모든 플랫폼에서 아래의 코드의 로그는 true이다.

```javascript
const str = `BEFORE
AFTER`;
console.log(str === 'BEFORE\nAFTER'); // true
```
스팩: 템플릿 리터럴에서 라인 종결자
ESMAScript 명세에서 "Static Semantics: TV and TRV"은 템플릿 리터럴에서 라인 종결자가 해석 되는 방법을 정의 한다.
* The TRV of LineTerminatorSequence :: <LF> is the code unit value 0x000A.
* The TRV of LineTerminatorSequence :: <CR> is the code unit value 0x000A.
* The TRV of LineTerminatorSequence :: <CR><LF> is the sequence consisting of the code unit value 0x000A.

### 8.2.3 태그드 템플릿 리터럴

아래는 태그드 템플릿 리터럴 이다. (줄여서 태그드 템플릿):

```javascript
tagFunction`Hello ${firstName} ${lastName}!`
```

식 뒤에 템플릿 리터럴을 놓는 것은 함수호출을 발생 시키고, 이것은 변수 리스트(괄호 안에 컴마로 구분된 값)가 함수 호출을 발생시키는것과 유사하다. 이전의 코드는 아래의 함수 호출과 동등합니다. (실제로 이 함수는 더 많은 정보를 얻지만 이건 이후에 설명하겠다.)

```javascript
tagFunction(['Hello ', ' ', '!'], firstName, lastName)
```

따라서, 역 따옴표로 둘러쌓인 컨텐트 이전에 나오는 이름은 호출 될 함수(태그 함수)의 이름이다. 이 태그 함수는 두 종류의 데이터를 받는다.
* 'Hello' 와 같은 템플릿 문자열.
* firstName (${}로 구분된) 치환자. 치환자는 어떤 표현도 될 수 있다.

템플릿 문자열 정적(컴플릿 시간)으로 알려져 있고, 치환자는 오직 런타입때 알려진다. 태그 함수는 그 변수로 원하는 바를 할 수 있다.: 이것은 템플릿 문자열을 완전히 무시 할 수 있고, 다른 어떤 타입의 값으로 반환할 수 있다.

추가적으로 태그 함수는 각 템플릿 문자열의 두가지 버전을 얻는다.:
* 역슬래시 안의 "raw" 버전은 해석되지 않는다. (`\n`는 `\\n`이 된다. 문자열의 길이는 2)
* 역슬래시 안의 "cooked" 버전은 특별하다. (`\n`는 줄바꿈이 있는 문자열이 된다.)

String.raw는 아래처럼 수행된다.:
```javascript
> String.raw`\n` === '\\n'
true
```

## 8.3 태그드 템플릿 리터럴 사용예

 자바스크립트는 당신을 위한 파싱을 수행하기 때문에 태그드 템플릿 리터럴은 작은 노력으로 커스톰 임베디드 서브 랭귀지(때때로 이것은 도메인 특화 언어라고 불린다.)를 구현하는 것을 가능하게 한다. 단지 결과를 받는 함수를 구현 해야 한다.

예제를 봐라. 어떤건 템플릿 리터럴의 "the original proposal"에서 영감을 받았다. 오래된 이름인 quasi-literals를 통해 그것들을 참고 할 수 있다.

### 8.3.1 Raw 문자열

ES6은 역슬래시가 아무 의미가 없는 String.raw 태그 함수를 포함하고 있다.
```javascript
const str = String.raw`This is a text
with multiple lines.
Escapes are not interpreted,
\n is not a newline.`;
```

역슬래시를 가진 문자열 생성이 필요할때 유용하다.
예를 들면:

```javascript
function createNumberRegExp(english) {
    const PERIOD = english ? String.raw`\.`; // (A)
    return new RegExp(`[0-9]+(${PERIOD}[0-9]+)?`);
}
```
줄 A에서 String.raw는 정규표현식에서 역슬래시를 쓸 수 있게 한다. 일반 문자열에서 두번 이스케이프 해야 한다. 첫번째 정규표현식을 위한 점 이스케이프가 필요하다. 두번째 문자 리터럴을 위한 역슬래시 이스케이프가 필요하다.

### 8.3.2 쉘 커맨드
```javascript
const proc = sh`ps ax | grep ${pid}`;
```

### 8.3.3 바이트 문자열
```javascript
const buffer = bytes`455336465457210a`;
```

### 8.3.4 HTTP 요청
```javascript
POST`http://foo.org/bar?a=${a}&b=${b}
     Content-Type: application/json
     X-Credentials: ${credentials}

     { "foo": ${foo},
       "bar": ${bar}}
     `
     (myOnReadyStateChangeHandler);
```

### 8.3.5 더 강력해진 정규 표현식

Steven Levithan는 태그드 템플릿 리터럴이 그의 정규표현식 라이브러리 XRegExp에서 어떻게 사용되어지는 것에 대한 예제를 주고 있다.

만약 정규 표현식을 가지고 일한다면, XRegExp를 강력히 추천한다. 당신은 많은 발전된 기능을 얻을 수 있다. 그러나 그것들은 생성 시에 한번 약간의 성능상 불이익이 있다. 왜냐하면 XRegExp은 그것의 입력을 기존의 정규표현식으로 컴파일 하기 때문이다.

태그드 템플릿을 사용하지 않는다면, 너는 코드는 아래와 같을 것이다.
```javascript
var parts = '/2015/10/Page.html'.match(XRegExp(
  '^ # match at start of string only \n' +
  '/ (?<year> [^/]+ ) # capture top dir name as year \n' +
  '/ (?<month> [^/]+ ) # capture subdir name as month \n' +
  '/ (?<title> [^/]+ ) # capture base name as title \n' +
  '\\.html? $ # .htm or .html file ext at end of path ', 'x'
));

console.log(parts.year); // 2015
```

XRegExp가 이름 있는 그룹(year, month, title)과x 플래그를 제공하는것을 볼 수 있다. 그 플래그가 있으면 대부분의 공백을 무시하고 주석을 삽입 할 수 있다.
문자열 리터럴이 여기서 잘 동작하지 않는 두가지 이유가 있다. 첫째는 우리는 모든 정규표현식은 문자열 리터럴의 백슬래시 처리를 위한 정규표현식에 백슬래쉬가 두번 쳐야 한다. 둘째로 멀티 라인처리가 성가시다. 추가적인문자들 대신에 아마도 라인 마지막에 백슬래쉬를 쓸 수 있지만 그것들은 다루기 힘들고 여전히 \n을 통해 명시적으로 줄바꿈을 추가해야 한다. 이 두가지 문제는 태그드 템플릿통해 떨쳐버릴 수 있다.

```javascript
var parts = '/2015/10/Page.html'.match(XRegExp.rx`
    ^ # match at start of string only
    / (?<year> [^/]+ ) # capture top dir name as year
    / (?<month> [^/]+ ) # capture subdir name as month
    / (?<title> [^/]+ ) # capture base name as title
    \.html? $ # .htm or .html file ext at end of path
`);
```

태그드 템플릿은 또한 ${v}을 통해 v값을 삽입할수 있게 한다. 나는 정규표현식 라이브러리에 문자열을 이스케이프 하고 정규표현식 축약어를 삽입하는 것을 기대하고 있다.

```javascript
var str   = 'really?';
var regex = XRegExp.rx`(${str})*`;
```

이것은 아래와 같다.
```javascript
var regex = XRegExp.rx`(really\?)*`;
```

### 8.3.6 질의어

예를 들면:
```javascript
$`a.${className}[href=~'//${domain}/']`
```
이 돔 쿼리는 css class 가 className이고, 그 타겟이 주어진 domain이 url인 모든 a 태그를 찾는다. 이 태그 함수 $는 인자들은 정확하게 이스케이프 하고, 문자열 연결 보다 더 안전한 접근을 보장한다.

### 8.3.7 React JSX를 통한 태그드 템플릿
페이스북 리엑트는 "사용자 인터페이스를 구축하는 자바스크립트 라이브러리" 이다. 이것은 추가적인 언어 확장인 JSX를 가진다. JSX는 당신이 사용자 인터페이스를 위한 가상 돔 트리를 만들 수 있게 한다. 이 확장은 당신의의 코드를 더 간결하게 하지만, 이것은 비표준이고 다른 자바스크립트 생태계를 호환성을 파괴한다.

t7.js 라이브러리는 JSX와 다른 대안을 제공한다. t7 사용을 보자:
```javascript
t7.module(function(t7) {
  function MyWidget(props) {
    return t7`
      <div>
        <span>I'm a widget ${ props.welcome }</span>
      </div>
    `;
  }

  t7.assign('Widget', MyWidget);

  t7`
    <div>
      <header>
        <Widget welcome="Hello world" />
      </header>
    </div>
  `;
});
```

"Why not Template Literals?"를 보면, 리엑트 팀은 왜 템플릿 리터럴을 사용하지 않은지에 대해 설명한다. 하나의 문제는 태그 템플릿안의 컨포넌트 접근이다. 예를 들면 위의 예제에서 MyWidget은 두번째 태그드 템플릿에 의해 접근 되어 진다. 하나의 자세한 방법은 이렇게 될 것 이다.:

```
<${MyWidget} welcome="Hello world" />
```

Instead, t7.js uses a registry which is filled via t7.assign(). That requires extra configuration, but the template literals look nicer, especially if there is both an opening and a closing tag.

> 대신, t7.js는 t7.assign()을 통해 등록된 레지스트리를 사용한다. 이것은 여분의 설정을 요구하지만, 템플릿 리터럴은 보기 좋고, 특히 시작과 닫는 태그가 그렇다.

### 8.3.8 Facebook GraphQL
> ### 8.3.8 페이스북 GraphQL
Facebook Relay is a “JavaScript framework for building data-driven React applications”. One of its parts is the query language GraphQL whose queries can be created via templates tagged with Relay.QL. For example (borrowed from the Relay homepage):
> 페이스북 Relay는 "데이터 주도 React 어플리케이션 구축을 위한 자바스크립트 프레임웍" 이다. 그 부분 중 하나는 템플릿 태그(Relay.QL)를 통해 생성될 수 있는 GraphQL 질의어 이다. 예를 들면:

```javascript
 class TeaStore extends React.Component {
  render() {
    return <ul>
      {this.props.store.teas.map(
        tea => <Tea tea={tea} />
      )}
    </ul>;
  }
}

TeaStore = Relay.createContainer(TeaStore, {
  fragments: { // (A)
    store: () => Relay.QL`
      fragment on Store {
        teas { ${Tea.getFragment('tea')} },
      }
    `,
  },
});

```

The query is created in method fragments (line A) and attached to the React component TeaStore. Its results of the query are put into this.props.store.
> 이 쿼리는 fragments 메소드를 통해 생성(A줄) 되고 리액트 컴포넌트인 TeaStore에 붙착된다. 그 쿼리의 결과는 this.props.store에 넣는다.

This is the data that the query operates on:
> 여기 쿼리 수행 결과 데이터가 있다:
```javascript
const STORE = {
  teas: [
    {name: 'Earl Grey Blue Star', steepingTime: 5},
    ···
  ],
};
```

### 8.3.9 Text localization (L10N)
> ### 8.3.9 텍스트 지역화 (L10N)

This section describes a simple approach to text localization that supports different languages and different locales (how to format numbers, time, etc.). Given the following message.
> 이 색션은 다른 언어와 지역을 지원하는 문자열 지역화에 대한 간단한 접근법을 설명한다. (숫자, 시간등 형식을 지정하는 방법) 주어진 메세지를 보면
```javascript
alert(msg`Welcome to ${siteName}, you are visitor
          number ${visitorNumber}:d!`);
```
The tag function msg would work as follows.
> 태그 함수 msg는 아래와 같이 동작할 것이다.

First, The literal parts are concatenated to form a string that can be used to look up a translation in a table. An example for a lookup string is:
> 첫째 리터럴 부분은 표에서 번역을 찾기 위해 사용될 수 있는 문자열을 형성하기 위에 연결된다. 문자열을 찾는 예를 보자

```
'Welcome to {0}, you are visitor number {1}!'
```

An example for a translation to German is:
> 독일어로 번역을 예를 들면:

```
'Besucher Nr. {1}, willkommen bei {0}!'
```
The English “translation” would be the same as the lookup string.
> 영어 "번역" 검색 문자열과 동일 하다.

Second, the result from the lookup is used to display the substitutions. Because a lookup result includes indices, it can rearrange the order of the substitutions. That has been done in German, where the visitor number comes before the site name. How the substitutions are formatted can be influenced via annotations such as :d. This annotation means that a locale-specific decimal separator should be used for visitorNumber. Thus, a possible English result is:
> 둘째, 검색 결과는 치환을 표현하는데 사용된다. 왜냐하면 검색 결과는 치환의 순서를 재배열 할 수 있는 색인을 포함하기 때문이다. 독일어로 하면 방문자 수가 싸이트 이름 보다 앞에 온다. 그 치환물들은 형성하는 방법은 :d와 같은 주석을 통해 영향을 미칠 수 있다. 그 주석은 visitorNumber를 위해 사용 되어진 지역 특화 수 구분자를 의미 하는 것 이다. 따라서 가능한 영어 결과는

```
Welcome to ACME Corp., you are visitor number 1,300!
```
In German, we have results such as:
> 독일어 결과는:
```
Besucher Nr. 1.300, willkommen bei ACME Corp.!
```

### 8.3.10 Text templating via untagged template literals
> ### 8.3.10 비태그드 템플릿 리터럴을 통한 문자열 템플링

Let’s say we want to create HTML that display the following data in a table:
> 이제 우리는 테이블에서 다음 데이터를 표시하는 HTML 만들려고 가정해 보자.

```javascript
const data = [
    { first: '<Jane>', last: 'Bond' },
    { first: 'Lars', last: '<Croft>' },
];
const data = [
    { first: '<Jane>', last: 'Bond' },
    { first: 'Lars', last: '<Croft>' },
];
```

As explained previously, template literals are not templates. They are code that is executed immediately, not text with holes that you can apply to data. The latter description gives us a hint how we can turn a template literal into an actual template – it sounds like the description of a function (data in, text out). Let’s implement a template tmpl as a function from data to strings:
> 이전 설명에 따르면 템플릿 리터럴은 템플릿이 아니다. 이것은 즉시 실행 코드이고,  당신이 데이터를 넣을 수 있는 빈칸이 있는 문자열는 아니다. 이 뒷부분의 설명은 우리에서 어떻게 우리가 템플릿 리터럴을 실제 템플릿으로 변할 수 있는 방법에 대한 힌트를 준다. 이것은 마치 함수의 모양처럼 보인다(data in text out). 자 함수 처럼 데이터를 문자열로 바꾸는 템플릿 tmpl을 구현해 보자.

```javascript
const tmpl = addrs => `
    <table>
    ${addrs.map(addr => `
        <tr><td>${addr.first}</td></tr>
        <tr><td>${addr.last}</td></tr>
    `).join('')}
    </table>
`;
console.log(tmpl(data));
// Output:
// <table>
//
//     <tr><td><Jane></td></tr>
//     <tr><td>Bond</td></tr>
//
//     <tr><td>Lars</td></tr>
//     <tr><td><Croft></td></tr>
//
// </table>
```
The outer template literal provides the bracketing <table> and </table>. Inside, we are embedding JavaScript code that produces a string by joining an Array of strings. The Array is created by mapping each address to two table rows. Note that the plain text pieces <Jane> and <Croft are not properly escaped. How to do that via a tagged template is explained in the next section.
> 바깥쪽 템플릿 리터럴은 <table> </table> 묶음을 제공한다. 내부, 우리는 문자열 배열의 결합을 통해 문자열을 생성하는 자바스크립트 코드를 포함하고 있다. 이 배열은 두 개의 테이블 행을 각 주소를 맴핑을 통해 생성하게 된다. 일반 문자열 조각들 <Jane>과 <Croft>는 적절히 익스케이프 되지 않음을 주의하라. 다음 섹션에서 태그드 템플릿을 통한 방법을 설명한다.

#### 8.3.10.1 Should I use this technique in production code?
> #### 8.3.10.1 내가 회사 코드에서 이 기술을 사용해야 합니까?

This is a useful quick solution for smaller templating tasks. For larger task, you may want more powerful solutions such as the templating engine Handlebars.js or the JSX syntax used in React.
> 이것은 작은 템플릿 작업을 하기 유용하고 빠른 해답 이다. 큰 작업을 위해서는 아마도 당신은 더 강력한 해답인 Handlbars.js나 리액트에서 사용된 JSX 문법를 원할지 모른다.

Acknowledgement: This approach to text templating is based on an idea by Claus Reinke.
> 감사: 문자열 템플릿에 대한 접근은 Claus Reinke의 아이디어에 기반한다.

### 8.3.11 A tag function for HTML templating
> ### 8.3.11 HTML 템플링을 위한 태그 함수

Compared to using untagged templates for HTML templating, like we did in the previous section, tagged templates bring two advantages:
> 우리가 이전 섹션에 있는 HTML 템플릿에서 비 태그 템플릿을 사용한 것과 비교해 본다면, 태그 템플릿은 두 가지 이점이 있다.:
* They can escape characters for us if we prefix ${} with a dollar sign. That is needed for the names, which contain characters that need to be escaped (<Jane>).
> 만약 우리가 ${} 앞에 달러문자를 붙인다면, 우리는 문자열을 이스케이프 할 수 있게 할 수 있다. 이것은 이스케이프 처리가 필요한 문자를 포함된 (<Jane>) 이름에 필요 하다.
* They can automatically join() Arrays for us, so that we don’t have to call that method ourselves.
> 그것은 우리를 위해 자동으로 배열을 join() 할 수 있게 할 수 있고, 따라서 우리는 우리 스스로 그 메소드를 호출할 필요가 없다.

Then the code for the template looks as follows. The name of the tag function is html:
> 다음과 같은 템플릿 코드가 보인다. 태그 함수의 이름은 html이다.:

```javascript
const tmpl = addrs => html`
    <table>
    ${addrs.map(addr => html`
        <tr><td>$${addr.first}</td></tr>
        <tr><td>$${addr.last}</td></tr>
    `)}
    </table>
`;
const data = [
    { first: '<Jane>', last: 'Bond' },
    { first: 'Lars', last: '<Croft>' },
];
console.log(tmpl(data));
// Output:
// <table>
//
//     <tr><td>&lt;Jane&gt;</td></tr>
//     <tr><td>Bond</td></tr>
//
//     <tr><td>Lars</td></tr>
//     <tr><td>&lt;Croft&gt;</td></tr>
//
// </table>
```

Note that the angle brackets around Jane and Croft are escaped, whereas those around tr and td aren’t.
> 꺽쇠안에 있는 Jane과 Croft는 익스케이프 되었고, 반면에 그것들을 둘러싸고 있는 tr과 td는 이스케이프 되지 않았음을 주목하라.

The syntax $${} is used for text that should be HTML-escaped. It is not in any way special; it’s just the normal text $ followed by the substitution ${}. Therefore, the tag function has to check the text preceding a substitution in order to determine whether to escape or not.
> $${} 문법은 반드시 이스케이스가 필요한 HTML 문자열을 위해 사용된다. 이것은 특별한 방법이 아니다. 이것은 단지 평범한 문자열 $뒤에 치환자${}가 따라오는 것 이다. 따라서, 태그 함수는 이스케이브 여부를 결정하기 위해 치환 이전에 문자열을 확인한다.

An implementation of html is shown later.
> html의 구현은 뒤에 보여진다.

## 8.4 Implementing tag functions
> ## 8.4 태그 함수 구현

The following is a tagged template literal:
> 아래 태그드 함수 리터럴:

```javascript
tagFunction`lit1\n${subst1} lit2 ${subst2}`
```

This is a simplified version of the function call triggered by this literal:
> 이것은 이 문자열을 통해 실행되는 함수 호출의 간략화 된 버전이다.:

```javascript
tagFunction(['lit1\n',  ' lit2 ', ''], subst1, subst2)
```
The exact function call looks more like this:
> 정확한 함수 호출은 아래와 비슷하다.

```javascript
// Globally: add template object to per-realm template map
{
    // “Cooked” template strings: backslash is interpreted
    const templateObject = ['lit1\n',  ' lit2 ', ''];
    // “Raw” template strings: backslash is verbatim
    templateObject.raw   = ['lit1\\n', ' lit2 ', ''];

    // The Arrays with template strings are frozen
    Object.freeze(templateObject.raw);
    Object.freeze(templateObject);

    __templateMap__[716] = templateObject;
}

// In-place: invocation of tag function
tagFunction(__templateMap__[716], subst1, subst2)
```

There are two kinds of input that the tag function receives:
> 태그 함수가 받는 입력은 두가지다.

* Template strings: are delivered via the template object in the first parameter. They are the static parts that don’t change (e.g. ' lit2 '). You get both “cooked” template strings (with escapes such as \n interpreted) and “raw” template strings (with uninterpreted escapes).
    > 템플릿 문자열: 템플릿 객체를 통해 전달 되는 첫번째 인자 이다. 이 객체는 변경불가한 스태틱 부분이다(예 'lit2'). "cooked" 템플릿 문자열(\n 해석을 통한 이스케이프)과 "raw" 템플릿 문자열(해석 하지 않은 이스케이프)을 얻는다.

* Substitutions: are delivered via trailing parameters. They are embedded inside template literals via ${} (e.g. subst1). Substitutions are dynamic, they can change with each invocation.
    > 치환물: 다음 인자값으로 전달된다. 치환물들은 ${}(예 subst1)을 통해 템플릿 리터럴에 끼워진다. 치환물은 동적이고 그것들은 호출 시 변경 될 수 있다.

The number of template strings is always one plus the number of substitutions. If a substitution is first in a literal, it is prefixed by an empty template string. If a substitution is last, it is suffixed by an empty template string (as in the previous example).
> 템플릿 문자열의 수는 항상 치환물 더하기 일 이다. 만약 치환물이 리터럴의 제일 처음에 있으면, 빈 템플릿 문자열을 앞에 추가 한다. 만약 치환물이 마지막에 있다면 빈 템플릿 문자열을 뒤에 추가 한다.(위의 예제 처럼)

The idea behind the template object is that the same tagged template might be executed multiple times (e.g. in a loop or a function). The template object enables the tag function to cache data from previous invocations: It can put data it derived from input source #1 into the object, to avoid recomputing it needlessly. Caching happens per realm (think frame in a browser). That is, there is one template object per call site and realm.
> 템플릿 객체의 기본 개념은 동일한 태그드 템플릿은 어려번 수행될 수 있다(예를 들면 루프나 함수안에서). 그 템플릿 객체는 태그 함수가 이전 호출으로 부터 데이터를 캐쉬하는것을 가능하게 한다. 이것은 불필요한 재계산을 피하기 위해서 인풋소스 #1로 전달 받은 데이터를 객체로 변환한다. 캐싱은 영역(내 생각에는 브라우저 프래임) 별로 발생한다. 즉, 사이트와 영역 호출 당 하나의 템플릿 객체가 있다.

The following is a first example of a tag function, a reimplementation of String.raw:
> 다음은 String.raw를 재구현한 첫번째 예이다.:

```javascript
function raw(strs, ...substs) {
    let result = strs.raw[0];
    for (let [i,subst] of substs.entries()) {
        result += subst;
        result += strs.raw[i+1];
    }
    return result;
}
```

Tagged template literals in the spec
A section on tagged template literals explains how they are interpreted as function calls. A separate section explains how a template literal is turned into a list of arguments: the template object and the substitutions.
> 스펙에서의 태그드 템플릿 리터럴
태그드 템플릿 리터럴 섹션은 어떻게 그들이 함수 호출로 해석되는 지를 설명한다. 별도 섹션은 어떻게 템플릿 리터럴이  템플릿 객체와 치환물 인 인자들의 리스트로 변환되는지 설명한다.

### 8.4.1 Escaping in tagged template literals: cooked versus raw
> ### 8.4.1 태그트 템플릿 리터럴 안의 이스케이핑: cooked 대 raw

In tagged template literals, there are more rules for escaping, because template strings (the text fragments inside the backticks, excluding substitutions) are available in two interpretations: cooked and raw. The rules are:
> 태그드 템플릿 리터럴 안에서 이스케이핑에 대한 더 많은 규칙이 있다. 왜냐하면 템플릿 문자열(역따옴표 안의 치환물을 제외한 문서 조각)은 두개의 cooked와 raw해석이 가능하기 때문이다.:

* In both cooked and raw interpretation, a backslash (\) in front of a dollar sign ($) prevents ${ from being interpreted as starting a substitution.
    > cooked와 raw 해석 시 둘 다 달럴 기호($) 앞의 백 슬래시는 ${로 부터 시작되는 치환물로 해석 되지 않게 한다. 
* However, every single backslash is mentioned in the raw interpretation, even the ones that escape substitutions.
    > 하지만 raw 해석에는 하나의 백슬래시는 심지어 이스케이프 치환물 일때도 나타난다.

The tag function describe allows us to explore what that means.
> 태그 함수인 describe는 우리에게 이게 무슨 의미인지 알려준다.

```javascript
function describe(tmplObj, ...substs) {
    console.log('Cooked:', intersperse(tmplObj, substs));
    console.log('Raw:   ', intersperse(tmplObj.raw, substs));
}
function intersperse(tmplStrs, substs) {
    // There is always at least one element in tmplStrs
    let result = tmplStrs[0];
    substs.forEach((subst, i) => {
        result += String(subst);
        result += tmplStrs[i+1];
    });
    return result;
}
```

Let’s use this tag function (I am not showing the result undefined of these function calls):
> 이제 이 태크 함수를 사용해보자(나는 이 함수 호출의 결과 중 undefined는 보여주지 않았다.)

```
 > describe`${3+3}`
Cooked: 6
Raw:    6

> describe`\${3+3}`
Cooked: ${3+3}
Raw:    \${3+3}

> describe`\\${3+3}`
Cooked: \6
Raw:    \\6

> describe`\\\${3+3}`
Cooked: \${3+3}
Raw:    \\\${3+3}
```

As you can see, whenever the cooked interpretation has a substitution then so does the raw interpretation. However, all backslashes from the literal appear in the raw interpretation; if a backslash precedes the characters ${ then it prevented a substitution.
> 눈으로 본 것 같이 cooked 해석이 치환물을 가지면 raw 해석 또한 치환물을 갖지만, raw 해석에서는 리터럴에 있는 모든 백슬래쉬가 나타난다. 만약 백슬래쉬가 ${에 앞선다면 그것은 치환을 막는다.

Other occurrences of the backslash are interpreted similarly:
> 백슬래시의 다른 것들은 아래처럼 해석된다.:

* In cooked mode, the backslash is handled like in string literals.
    > cooked 모드일때 백슬래시는 문자열 리터럴 처럼 다뤄진다.
* In raw mode, the backslash is used verbatim. 
    > raw 모드일때 백슬래시는 그대로 사용된다.

For example:
> 예를 들면
```
> `\n`
'\n'
> String.raw`\n`
'\\n'
```
The only time the backslash ever has an effect in raw mode is when it appears in front of a substitution (which it escapes).
> raw모드에서 백슬래시가 효과가 있을 때 는 이것이 치환물 앞에 있을 때 이다.

Escaping in tagged template literals in the spec
> 스펙에서 태그드 릴터럴 이스케이핑

In the grammar for template literals, you can see that, within a template literal, there must be no open curly brace ({) after a dollar sign ($). However, an escaped dollar sign (\$) can be followed by an open curly brace. The rules for interpreting the characters of a template literal are explained in a separate section.
> 템플릿 문법에 안에서, 템플릿 리터럴 안에서 달러 문자($) 뒤에 반드시 열린 중괄호({)가 없어야한다는 것을 볼 수 있다. 그러나 이스케이프된 $문자(\$)에 열린 괄호가 따라올 수 있다. 템플릿 리터럴의 문자들 해석 규칙은 분리된 센셕에서 설명하고 있다.

### 8.4.2 Example: implementing a tag function for HTML templating
> ### 8.4.2 예제: HTML 템플릿 태그 함수 구현

I previously demonstrated the tag function html for HTML templating:
> 나는 이전에 HTML템플링을 위한 태그 함수 html을 보였다.

```javascript
const tmpl = addrs => html`
    <table>
    ${addrs.map(addr => html`
        <tr><td>$${addr.first}</td></tr>
        <tr><td>$${addr.last}</td></tr>
    `)}
    </table>
`;
const data = [
    { first: '<Jane>', last: 'Bond' },
    { first: 'Lars', last: '<Croft>' },
];
console.log(tmpl(data));
// Output:
// <table>
//
//     <tr><td>&lt;Jane&gt;</td></tr>
//     <tr><td>Bond</td></tr>
//
//     <tr><td>Lars</td></tr>
//     <tr><td>&lt;Croft&gt;</td></tr>
//
// </table>
```
The syntax $${} is used for text that should be HTML-escaped. It is not in any way special; it’s just the normal text $ followed by the substitution ${}. Therefore, the tag function has to check the text preceding a substitution in order to determine whether to escape or not.
> 이 $${} 문법은 HTML 이스케이프가 필요한 텍스트를 위해 사용된다. 이것은 특별한 방법이 아니다. 이것은 단지 치환물 ${} 앞에 있는 평범한 $를 문자 이다. 따라서, 이 태그 함수는 이스케이프 되는지 안되는지를 결정하기 위해서 치환물 앞에 텍스트 확인한다.

This is an implementation of html:
> html 구현은 이것이다.

```javascript
function html(templateObject, ...substs) {
    // Use raw template strings: we don’t want
    // backslashes (\n etc.) to be interpreted
    const raw = templateObject.raw;

    let result = '';

    substs.forEach((subst, i) => {
        // Retrieve the template string preceding
        // the current substitution
        const lit = raw[i];

        // In the example, map() returns an Array:
        // If substitution is an Array (and not a string),
        // we turn it into a string
        if (Array.isArray(subst)) {
            subst = subst.join('');
        }

        // If the substitution is preceded by a dollar sign,
        // we escape special characters in it
        if (lit.endsWith('$')) {
            subst = htmlEscape(subst);
            lit = lit.slice(0, -1);
        }
        result += lit;
        result += subst;
    });
    // Take care of last template string
    // (Never fails, because an empty tagged template
    // produces one template string, an empty string)
    result += raw[raw.length-1]; // (A)

    return result;
}
```

Each substitution is always surrounded by template strings. If the tagged template literal ends with a substitution, the last template string is an empty string. Accordingly, the following expression is always true:
> 각 치환물은 항상 템플릿 문자열들에 의해 감싸여 있다. 만약 태그드 템플릿 리터럴에서 치환물로 끝나면 마지막에 템플릿 문자열은 빈 문자열을 추가 한다. 따라서 아래의 표현은 항상 참이다:
```javascript
templateObject.length === substs.length + 1
```

That’s why we need to append the last template string in line A.
> 이것이 바로 우리는 A줄에 마지막 템플릿 문자열을 붙이는 이유이다.

The following is a simple implementation of htmlEscape().
> 아래는 간단한 htmlEscape() 구현이다.

```javascript
function htmlEscape(str) {
    return str.replace(/&/g, '&amp;') // first!
              .replace(/>/g, '&gt;')
              .replace(/</g, '&lt;')
              .replace(/"/g, '&quot;')
              .replace(/'/g, '&#39;')
              .replace(/`/g, '&#96;');
}
```
#### 8.4.2.1 More ideas
> #### 8.4.2.1 더 많은 아이디어

There are more things you can do with this approach to templating:
> 이 템플링 방법을 통해서 할 수 있는게 더 있다.

* This approach isn’t limited to HTML, it would work just as well for other kinds of text. Obviously, escaping would have to be adapted.
    > 이 방법은 HTML에 제한된 것이 아니고, 이것은 다른 종류의 텍스트에도 잘 동작한다. 물론 이스케이프가 적용되여야 할 것이다.
* if-then-else inside the template can be done via the ternary operator (cond?then:else) or via the logical Or operator (||):
    > 템플릿 안의  if-then-else(cond? then:else)는 삼항연산자 또는 로직적으로 Or0 연산자를(||) 통해 할 수 있다.

```javascript
$${addr.first ? addr.first : '(No first name)'}
$${addr.first || '(No first name)'}
```

* Some of the leading whitespace in each line can be trimmed if the first non-whitespace character in the first line defines where the first column is.
    > 만약 첫 줄 처음에 공백이 아닌 문자열로 정의하는 경우 각 행의 맨 앞의 공백을 제거 할 수 있다. 
Destructuring(풀어쓰기는) 사용될 수 있다.

```javascript
${addrs.map(({first,last}) => html`
      <tr><td>$${first}</td></tr>
      <tr><td>$${last}</td></tr>
  `)}
```

### 8.4.3 Example: assembling regular expressions
> ### 8.4.3 예제: 정규표현식과의 결합

There are two ways of creating regular expression instances.
> 정규표현식 객체를 만드는 두가지 방법이 있다.

* Statically (at compile time), via a regular expression literal: /^abc$/i
    > 정적으로(컴파일 시에), 정규표현식 리터럴을 통해: /^abc$/i
* Dynamically (at runtime), via the RegExp constructor: new RegExp('^abc$', 'i')
    > 동적으로(런타입 시에), RegExp 생성자를 통해: new RegExp(‘^abc$’,i)

If you use the latter, it is because you have to wait until runtime so that all necessary ingredients are available. You are creating the regular expression by concatenating three kinds of pieces:
> 만약 동적으로 사용한다면 필요한 모든 재료가 사용 가능 하도록 런타임 때 까지 기다릴 필요가 있기 때문이다. 당신은 세가지 종류의 조각을 연결하여 정규표현식을 생성한다.:

* Static text
    > 스태틱 문자열
* Dynamic regular expressions
    > 동적 정규 표현식 
* Dynamic text
    > 동적 문자열

#3 특수 문자들 (점, 대괄호 등) 반드시 이스케이프 해야 한다. 반면에 #1과 #2 는 원래 그대로 사용되어야 한다. 정규표현식 태그 함수 regex는 이런 작업을 도울 수 있다.
```javascript
const INTEGER = /\d+/;
const decimalPoint = '.'; // locale-specific! E.g. ',' in Germany
const NUMBER = regex`${INTEGER}(${decimalPoint}${INTEGER})?`;
```
regex looks like this:
> regex 아래 처럼 작성된다.:
```javascript
function regex(tmplObj, ...substs) {
    // Static text: verbatim
    let regexText = tmplObj.raw[0];
    for ([i, subst] of substs.entries()) {
        if (subst instanceof RegExp) {
            // Dynamic regular expressions: verbatim
            regexText += String(subst);
        } else {
            // Other dynamic data: escaped
            regexText += quoteText(String(subst));
        }
        // Static text: verbatim
        regexText += tmplObj.raw[i+1];
    }
    return new RegExp(regexText);
}
function quoteText(text) {
    return text.replace(/[\\^$.*+?()[\]{}|=!<>:-]/g, '\\$&');
}
```
## 8.5 FAQ: template literals and tagged template literals
> ## 8.5 FAQ: 템플릿 리터럴과 태그드 템플릿 리터럴
### 8.5.1 Where do template literals and tagged template literals come from?
> ### 8.5.1 어디서 템플릿 리터럴과 태그드 리터럴은 유래되었는가?

Template literals and tagged template literals were borrowed from the language E, which calls this feature quasi literals.
> 템플릿 리터럴과 태그드 템플릿 리터럴은 E언어로 부터 차용되었다. 이 기능은 quasi literals이라 부른다.

### 8.5.2 What is the difference between macros and tagged template literals?
> ### 8.5.2 매크로와 태그드 템플릿 리터럴은 무엇이 다른가?

Macros allow you to implement constructs that have custom syntax. Providing macros for a language whose syntax is as complex as JavaScript’s is difficult and ongoing research (see Mozilla’s sweet.js).
> 매크로는 사용자 정의 구문의 구조를 구현할수 있게 한다. 자바스크립트 같은 복잡한 언어에서 매크로를 제공하는 것은 어렵고 지속적으로 연구 중이다. (모질라의 sweet.js)

While macros are much more powerful for implementing sub-languages than tagged templates, they depend on the tokenization of the language. Therefore, tagged templates are complementary, because they specialize on text content.
> 매크로는 태그드 템플릿 보다 하위 언어 구현할 때 더욱 강력하지만 그것은 언어 토크 의존 한다. 따라서 태그드 템플릿은 문자열 콘텐츠 전문이기 때문에 보완적이다. 

### 8.5.3 Can I load a template literal from an external source?
> ### 8.5.3 외부소스로 부터 템플릿 리터럴을 가져오는게 가능한가?

What if I want to load a template literal such as \`Hello ${name}!\` from an external source (e.g., a file)?
> 만일 \`Hello ${name}\`과 같은 템플릿 리터럴을 외부 소스로 부터 로드하는것을 원한다면?

Note that you are abusing this mechanism if you do so. Given that a template literal can contain arbitrary expressions and is a literal, loading it from somewhere else is similar to loading an expression or a string literal – you have to use eval() or something similar.
> 그렇게 하는 것은 이 메카니즘을 악용 하고 있는 것 이라고 명심해라. 템플릿 리터럴이 임의의 표현식을 포함 하고 리터럴 일 수도 있다는 것을 고려하면 다른 곳에서 로딩하는 것과 표현식과 문자열 리터럴을 로딩하는 것과 유사하다.- eval()이나 다른 유사한것을 사용해야 한다.

Coming back to the example, this is how you’d do it:
> 예제로 돌아가면 다음은 할 수 있는 방법이다.

```javascript
const str = '`Hello ${name}!`'; // external source

const func = new Function('name', str);

const name = 'Jane';
const result = func(name);
```

Every variable that isn’t declared inside the template literal has to become a parameter of the function func that we are creating. Alternatively, you could load a whole function and eval it1:
> 생성 시에 템플릿 리터럴 안에 선언되지 않은 모든 변수는 함수func의 인자가 되어야 한다. 대안적으로 함수 전체를 로드하고 그것을 eval 할 수 있다.
```javascript
const str = '(name) => `Hello ${name}!`'; // external source

const func = eval.call(null, str); // indirect eval

const name = 'Jane';
const result = func(name);
```

### 8.5.4 Why are backticks the delimiters for template literals?
> ### 8.5.4 왜 템플릿 리터럴의 구분자가 역따옴표인가?

The backtick was one of the few ASCII characters that were still unused. The syntax ${} for interpolation is the de-facto standard (Unix shells, etc.).
> 역따옴표는 아스키 문자들 중에 여전히 적게 사용하는 문자이다. 이 보간을 위한 문법 ${}은 사실상의 표준이다. (Unix shell, etc.)

### 8.5.5 Weren’t template literals once called template strings?
### 8.5.5 한때 템플릿 리터럴은 템플릿 문자열로 불렸나요?
The template literal terminology changed relatively late during the creation of the ES6 spec. The following are the old terms:
> 이 템플릿 리터럴 용어는 ES6스펙의 생성 동안에 비교적 늦게 변경 되고 있다. 다음은 오래된 용어들이다.

* Template string (literal): the old name for template literal.
    > 템플릿 문자열: 템플릿 리터럴의 오래된 이름
* Tagged template string (literal): the old name for tagged template literal.
    > 태그 템플릿 스트링: 태그드 템플릿 리터럴의 오래된 이름.
* Template handler: the old name for tag function.
    > 템플릿 핸들러: 태그 함수의 오래된 이름
* Literal section: the old name for template string (the term substitution remains the same).
    > 리터럴 섹션: 템플릿 스트링의 오래된 이름(이 용어는 치환물과 동일하게 유지)