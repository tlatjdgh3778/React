React 웹앱을 만들던 중에 사소한 문제가 발생했는데, 페이지 이동 시 스크롤의 위치가 전 페이지의 위치 그대로 있는 문제였다.
구글에 검색을 해보니 여러 블로그에도 해결법이 나오고 react-router 문서에서도 해결책을 알려주고 있었다.

[Scroll Restoration](https://reactrouter.com/web/guides/scroll-restoration)

```js
import { useEffect } from "react";
import { useLocation } from "react-router-dom";

export default function ScrollToTop() {
  const { pathname } = useLocation();

  useEffect(() => {
    window.scrollTo(0, 0);
  }, [pathname]);

  return null;
}
```

위의 코드로 컴포넌트를 하나 만들고 이 컴포넌트를 `index.js` 에 선언해준다. `Router` 안에 선언해주어야 `pathname` 을 인식할 수 있기 때문에 꼭 `Router` 안에 선언해주도록 하자.

```js
ReactDOM.render(
  <BrowserRouter>
  <ScrollTop />
    <App />
  </BrowserRouter>,
  document.getElementById('root')
);
```

적용해주고나면 SPA이어도 페이지 이동 시 스크롤이 맨 위로 가게되어 불편함을 느끼지 않을 수 있다.
