## 확장성과 유연성을 고려한 바벨 설정 방법

1. 패키지 설치
```bash
npm istall @babel/core @babel/cli @babel/plugin-transform-arrow-functions @babel/plugin-transform-template-litterals @babel/preset-react babel-preset-minify
```

common/.babelrc
```javascript
{
    "presets":["@babel/preset-react"],
    "plugins":[ //1
        [
            "@babel/plugin-transform-template-literals",
            {
                "loose":true
            }
        ]
    ]
}
```

1. 플러그인에 옵션을 설정할 때는 배열로 만들어서 두 번째 자리에 옵션을 넣는다.
템플릿 리터럴 플러그인에 loose 옵션을 주며 문자열을 연결할 때 concat 메서드 대신 + 연산자를 사용한다.


2. 프로젝트 루트에 src 폴더를 만들고 src 폴더 밑에 example-extends 폴더를 만들고 폴더 밑에 .babelrc 파일을 만들고 다음 내용을 입력한다.

```javascript
{
    "extends":"../../common/.babelrc", //1
    "plugins":[ //2
        "@babel/plugin-transform-template-literals", //3
        "@babel/plugin-transform-template-literals"
    ]
}
```

    1. extends 속성을 이용해서 다른 파일에 있는 설정을 가져온다.
    2. 가져온 설정에 플러그인을 추가한다. 
    3. 템플릿 리터럴 플러그인은 가져온 설정에 이미 존재 한다. 이때 플러그인 옵션은 현재 파일의 옵션으로 결정된다. 따라서 기존에 설정했던 loose 옵션 사라짐

3. example-extends 폴더 밑에 code.js 파일을 만들고 다음 코드를 입력함

```javascript
const element = <div>babel test</div>
const text= `element type is ${element.type}`;
const add = (a,b) => a + b;
```

4. 바벨 실행
```bash
npx babel src/example-extends/code.js
```

5. 실행 결과
```javascript
const element = /*#__PURE__*/React.createElement("div", null, "babel test"); //1
const text = "element type is ".concat(element.type); //2

const add = function (a, b) { /2
  return a + b;
};
```

    1.  리액트 프리셋 적용
    2.  템플릿 리터럴 플러그인이 적용  loose 옵션이 적용되지 않았기 때문에 concat 메서드가 보인다.
    3.  화살표 함수 플러그인이 적용 됐다.
    
## env 속성으로 환경별로 설정하기

1. src 밑에 example-env 폴더 생성 후 .babelrc 파일 만들고 아래 내용을 쓴다.
env 속성 사용 예
```javascript
{
    "presets":["@babel/preset-react"],/1
    "plugins":[ 
        "@babel/plugin-transform-arrow-functions", 
        "@babel/plugin-transform-template-literals"
    ],
    "env":{ //2
        "production":{
            "presets":["minify"] //3
        }
    }
}

```
    1. 프리셋 플러그인 설정
    2. env 속성을 이용하면 환경별로 다른 설정을 줄 수 있다.
    3. 프로덕션 환경에서는 압축 프리셋을 사용하도록 설정. 앞에서 설정한 리액트 프리셋은 유지되고 압축 프리셋이 추가되는 형태

2. example-env 폴더 밑에 code.js 파일을 만들고 example-extends/code.js 파일과 같은 코드를 입력한다.
바벨에서 현재 환경은 다음과 같이 결정된다.

process.env.BABEL_ENV || process.env.NODE_ENV || "development"

3. 프로덕션 환경으로 바벨 실행
```bash
MODE_ENV=production npx babel ./src/example-env
```

4. 결과
```javascript
const element=/*#__PURE__*/React.createElement("div",null,"babel test"),text="element type is ".concat(element.type),add=function(c,a){return c+a};
```

5. 압축 프리셋이 적용되어 코드를 읽기 힘들다. 이번에는 개발 환경에서 바벨을 실행한다.
```bash
npx babel ./src/example-env
```
NODE_ENV 환경 변수를 설정하지 않으면 기본값 development 가 사용된다.

```javascript
const element = /*#__PURE__*/React.createElement("div", null, "babel test");
const text = "element type is ".concat(element.type);

const add = function (a, b) {
  return a + b;
};
```
## overrides 속성으로 파일별로 설정하기
overrides 속성 사용 예
```javascript
{
    "presets":["babel/preset-react"], //1
    "plugins":["@babel/plugin-transform-template-literals"],
    "overrides":[//2
        {
            "include":"./service1",//3
            "exclude":"./service1/code2.js",//5
            "plugins":["@babel/plugin-transform-arrow-functions"]//4
        }
    ]
}

```
1. 리액트 프리셋과 템플릿 리터럴 플러그인을 설정한다.
2. overrides 속성을 이용하면 파일별로 다른 설정을 할 수 있다.
3. service1 폴더 밑에 있는 파일에는 4번 설정을 적용
5. service1/code2.js 파일에는 4번 설적을 적용하지 않는다. 
따라서 service1 폴더 하위에서 code2.js 파일을 제외한 모든 파일에 화살표 함수 플러그인이 적용된다.
