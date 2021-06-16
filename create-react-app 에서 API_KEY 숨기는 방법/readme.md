# create-react-app 에서 API_KEY 숨기는 방법

리액트 프로젝트를 하다 보면 아래처럼 API_KEY 를 발급받아 사용하는 일이 종종 있다.
```js
const API_KEY = "123456";
```
하지만 이 상태로 `git repository` 에 바로 올리거나 배포를 할 경우에는 악용될 우려가 있기 때문에 숨길 필요가 있는데 주로 환경 변수를 사용한다.


1. 먼저 create-react-app 으로 생성된 폴더 (src외부) 에 `.env` 파일을 생성한다.

![.env](https://images.velog.io/images/tlatjdgh3778/post/a3af2e4e-cdf5-4209-9aa2-3a13d5ebc0d0/env.png)

2. `.env` 파일 안에 다음과 같이 작성해 준다. (무조건 `REACT_APP_` 로 시작) 
```js
REACT_APP_API_KEY = 123456
// ex
REACT_APP_GOOGLE_KEY = 12345678
```

3. `.gitignore` 파일 하단에 `.env` 를 추가해서 github에 올라가지 않도록 해준다.

![.gitignore](https://images.velog.io/images/tlatjdgh3778/post/3eff63db-5d4f-4e57-9251-a8f271f08f73/gitignore.png)

 
4. API_KEY 사용 시에는 다음과 같이 작성해 주면 된다.
```js
const API_KEY = process.env.REACT_APP_API_KEY;
```

env 설정 후에는 Ctrl+c로 서버를 한 번 끄고 다시 시작시켜주면 적용이 완료된다.
