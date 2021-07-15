# Custom Hook

> 공식 문서의 custom hook 설명 
> 자신만의 Hook을 만들면 컴포넌트 로직을 함수로 뽑아내어 재사용할 수 있습니다.

이전 게시글 [(useEffect)](https://github.com/tlatjdgh3778/React/blob/89216d8bc27a207060510df9ab397600414aac23/React%20Hooks,%20useEffect()%EC%97%90%20%EB%8C%80%ED%95%9C%20%EC%A0%95%EB%A6%AC/README.md) 에서 작성한 코드를 보자.

```js
import React, { useEffect, useState } from "react";

const Hello2 = () => {
  const [name, setName] = useState("Mary");
  const [surname, setSurname] = useState("Poppins");
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    document.title = name + " " + surname;
  });

  useEffect(() => {
    const handleResize = () => {
      console.log("Resize Event");
      setWidth(window.innerWidth);
  	};
    window.addEventListener("resize", handleResize);
 
    // clean-up
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  });

  const handleNameChange = (e) => {
    setName(e.target.value);
  };

  const handleSurnameChange = (e) => {
    setSurname(e.target.value);
  };

  return (
    <section>
      <div>
        <label>Name : </label>
        <input value={name} onChange={handleNameChange} />
      </div>
      <div>
        <label>Surname : </label>
        <input value={surname} onChange={handleSurnameChange} />
      </div>
      <div>
        <label>Width : </label>
        {width}
      </div>
    </section>
  );
};

export default Hello2;
```

여기서 *resize하는 로직*, *document.title*, 그리고 *이름을 입력하는 부분* 을 custom hook으로 만들 수 있다. 

## Custom Hook으로 만들기

해당 로직을 분리하여 Custom Hook으로 만들어서 관리해보자.

```js
// Hello2.js
import React, { useEffect, useState } from "react";

const Hello2 = () => {
  const name = useInputForm("Mary");
  const surname = useInputForm("Poppins");
  const width = useWindowWidth();
  useDocumentTitle(name.value + " " + surname.value);

  return (
    <section>
      <div>
        <label>Name : </label>
        <input {...name} />
      </div>
      <div>
        <label>Surname : </label>
        <input {...surname} />
      </div>
      <div>
        <label>Width : </label>
        {width}
      </div>
    </section>
  );
};

// 로직 분리
const useInputForm = (initialValue) => {
  const [value, setValue] = useState(initialValue);

  const handleChange = (e) => {
    setValue(e.target.value);
  };

  return {
    value,
    onChange: handleChange
  };
};

// 로직 분리
const useDocumentTitle = (title) => {
  useEffect(() => {
    document.title = title;
  });
};

// 로직 분리
const useWindowWidth = () => {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => {
      console.log("Resize Event");
      setWidth(window.innerWidth);
    };
    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  });
  return width;
};

export default Hello2;
```

하나씩 hooks 폴더에 넣어서 관리할 수 있다.

### **resize 하는 로직을 hooks 폴더로 분리해서 관리**

```js
// Hello2.js
useEffect(() => {
    const handleResize = () => {
      console.log("Resize Event");
      setWidth(window.innerWidth);
  	};
    window.addEventListener("resize", handleResize);
 
    // clean-up
    return () => {
      window.removeEventListener("resize", handleResize);
    };
  });
```

```js
// hooks/useWindowWidth.js
import { useState, useEffect } from "react";

const useWindowWidth = () => {
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => {
      console.log("Resize Event");
      setWidth(window.innerWidth);
    };
    window.addEventListener("resize", handleResize);

    return () => {
      window.removeEventListener("resize", handleResize);
    };
  });
  return width;
};

export default useWindowWidth;
```

#### **`useWindowWidth` custom hook 사용**
 
```js
// Hello2.js 
const width = useWindowWidth(); 
```

### **이름 입력하는 부분 hooks 폴더로 분리해서 관리**

```js
// Hello2.js
const [name, setName] = useState('Mary');
const [surname, setSurname] = useState('Poppins');

  const handleNameChange = (e) => {
    setName(e.target.value);
  };

  const handleSurnameChange = (e) => {
    setSurname(e.target.value);
  };
```

```js
// hooks/useInputForm.js
import { useState } from "react";

const useInputForm = (initialValue) => {
  const [value, setValue] = useState(initialValue);

  const handleChange = (e) => {
    setValue(e.target.value);
  };

  return {
    value,
    onChange: handleChange
  };
};

export default useInputForm;
```

#### **`useInputForm` custom hook 사용**
```js
// Hello2.js
const name = useInputForm("Mary"); // value, onChange
const surname = useInputForm("Poppins"); // value, onChange

...
return (
    <section>
      <div>
        <label>Name : </label>
        <input {...name} />
      </div>
      <div>
        <label>Surname : </label>
        <input {...surname} />
      </div>
      <div>
        <label>Width : </label>
        {width}
      </div>
    </section>
  );
```

### **Document.title 부분 hooks 폴더로 분리해서 관리**
```js
// Hello2.js
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    document.title = name + " " + surname;
  });
```

```js
// hooks/useDocumentTitle.js
import { useEffect } from 'react';

const useDocumentTitle = (title) => {
  useEffect(() => {
    document.title = title;
  });
};

export default useDocumentTitle;
```

#### **`useDocumentTitle` custom hook 사용**
```js
// Hello2.js
useDocumentTitle(name.value + " " + surname.value);
```

## 참고
[YOUTUBE - React Today and Tomorrow and 90% Cleaner React With Hooks](https://www.youtube.com/watch?v=dpw9EHDh2bM)
[React 공식문서 - 자신만의 Hook 만들기](https://ko.reactjs.org/docs/hooks-custom.html)
