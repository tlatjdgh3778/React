Netlify로 React 프로젝트를 배포할 때

`Failed to load resource: the server responded with a status of 401 ()`

이러한 오류가 발생했다.

API KEY를 받아오지 못해서 오류가 발생하는 것인데 Netlify 에서 배포할 때 설정하는 부분이 있다.

![](https://images.velog.io/images/tlatjdgh3778/post/fc4c4f4c-2035-4408-a0d9-bd992347a186/image.png)

![](https://images.velog.io/images/tlatjdgh3778/post/8f33bdff-f7c4-4bdb-aca8-a08a85ae197c/image.png)

Deploy Settings 에 들어가서 Environment 탭을 확인해보면 API KEY를 적을 수 있는 부분이 있다.

API KEY를 적어주고 저정 후 재배포를 하게되면 오류가 발생하지 않는다.

