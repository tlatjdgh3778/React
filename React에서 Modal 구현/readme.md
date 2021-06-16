이번에 개인 프로젝트를 하면서 모달창을 구현하는 일이 생겼다.

`react-modal` 과 같은 라이브러리도 있지만 이번에는 라이브러리를 사용하지 않고 모달창을 구현하고 싶었다.

**1. 모달창 디자인**
```js
// SideBar.js
const [showModal, setShowModal] = useState(false);

const openModal = () => {
  setShowModal(true);
}
//...
<MenuIcon onClick={openModal}></MenuIcon>
<SideBarModal showModal={showModal} closeModal={closeModal}></SideBarModal>
```

```js
// SideBarModal.js
const Background = styled.div`
    position: fixed;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    background-color: rgba(0,0,0,0.50);
    z-index: 0;
`;

const ModalContainer = styled.div`
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    max-height: 80%;
    width: 20rem;
    height: 80%;
    padding: 16px;
    background: rgb(25, 31, 44);
    border-radius: 10px;
    text-align: center;

    @media ${(props) => props.theme.device.MobileLandscape}{
        width: 90%;
    }
`;

//...
  {showModal ? 
    <Background>
    <ModalContainer>
    <div>메뉴</div>
   <CloseIcon></CloseIcon>
   </ModalContainer>
   </Background> : null}
```

기본 상태(``false``)에서 메뉴 아이콘을 누르면 `SideBar.js` 의 ``<MenuIcon onClick={openModal}></MenuIcon>`` 코드로 인해 상태가 ``true`` 로 바뀌고 그 상태가 ``SideBarModal.js`` 로 전달이 되면서 모달창이 화면에 나타나게 된다. (마지막 코드 참고)


전체적인 코드는 아니지만.. 여기까지의 결과물은 아래와 같을 것이다.


![Modal.Image](https://images.velog.io/images/tlatjdgh3778/post/54c8a763-074b-40b6-a2a9-8dc86441555b/image.png)

**2. 이벤트 등록**

다음은 x를 눌렀을 때 모달창이 종료되는 이벤트가 필요하다.
```js
// SideBar.js
const closeModal = () => {
  setShowModal(false);
}
```
함수를 만들어주고,
```js
<SideBarModal showModal={showModal} closeModal={closeModal}></SideBarModal>
```
`SideBarModal.js` 에 전달해준다.

전달 받은 `props`를 x를 눌렀을 때 실행되도록 적용시켜주면 끝이다.
```js
<CloseIcon onClick={closeModal}></CloseIcon>
```


**2-1. 모달창 바깥을 눌렀을 때 모달창 종료**

이번에 모달창을 구현하면서 이 부분이 문제였다. 처음에는 `Background` 부분에 그냥 `onClick` 을 주어서 클릭하면 모달창이 종료되게 하면 되는줄 알았다. 
`<Background onClick={closeModal}>` 
이런식으로 코드를 구현했었다.

하지만 이렇게 구현하게되면 어디를 클릭해도 모달창이 종료가 되는 현상이 나타난다.
그 이유는 이벤트 버블링 때문이다. [이벤트 버블링](https://ko.javascript.info/bubbling-and-capturing)

간단히 설명하자면 한 요소에 이벤트가 발생하면 그 부모의 이벤트도 발생한다는 것이다.
```js
<>
  {showModal ? 
   <Background onClick={()=>console.log('background')}>
     <ModalContainer onClick={()=>console.log('modalcontainer')}>
       <div>메뉴</div>
	<CloseIcon onClick={()=>console.log('close')}></CloseIcon>
</ModalContainer>
</Background> : null}
</>
// close
// modalcontainer
// background
```
이 코드에서..
`CloseIcon` 만 눌러도 `close`, `modalcontainer`, `background` 가 전부 출력이 된다는 뜻이다.

이 현상을 해결하기 위해서는 이벤트 전파를 막고 싶은 부분에 `e.stopPropagation()` 을 작성해주면 이벤트 버블링 현상을 막아줄 수 있다.

```js
<>
  {showModal ? 
   <Background onClick={()=>console.log('background')}>
     <ModalContainer onClick={e => e.stopPropagation()}>
       <div>메뉴</div>
	<CloseIcon onClick={()=>console.log('close')}></CloseIcon>
</ModalContainer>
</Background> : null}
</>
// close
```

최종 코드
```js
<>
  {showModal ? 
   <Background onClick={closeModal}>
  <ModalContainer onClick={e => e.stopPropagation()}>
    <div>메뉴</div>
<CloseIcon onClick={closeModal}></CloseIcon>
</ModalContainer>
</Background> : null}
</>
```

전체 코드를 보고 싶으면 [여기로](https://github.com/tlatjdgh3778/COVID19/tree/main/src/components/SideBar) .

끝.

