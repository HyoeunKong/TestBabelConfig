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
```bash
const element = /*#__PURE__*/React.createElement("div", null, "babel test"); //1
const text = "element type is ".concat(element.type); //2

const add = function (a, b) { /2
  return a + b;
};
```

    1.  리액트 프리셋 적용
    2.  템플릿 리터럴 플러그인이 적용  loose 옵션이 적용되지 않았기 때문에 concat 메서드가 보인다.
    3.  화살표 함수 플러그인이 적용 됐다.
    