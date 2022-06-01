# onResult 관리법
각 모델에 따른 결과값을 onResult(콜백함수) 와 같은 형식으로 처리한다. 
```javascript
function resultHandler(results) {
    console.log(results)
}

pose.onResult(resultHandler);
```
<code>위 코드는 카메라로부터 인식된 Pose의 결과값을 반환한다.</code>

<br>먼저 다음과 같은 요구조건을 충족해야 한다.
- <code>result</code>를 받아오면 받아오는 즉시 해당 데이터를 이용하여 Canvas에 그래픽을 그려야 한다.
- 사용자가 촬영 버튼을 누를 시에 Canvas에 그려진 그래픽과 같은 데이터를 가지는 <code>result</code> 객체를 어딘가에 보관하고 관리해야 한다.

나는 가장 먼저 <code>state</code>를 통해 관리하려고 했다.

```javascript
const [result,setResult] = useState(null);

function resultHandler(results) {
    setResult(results);
}

useEffect(()=> {
    drawing(result);
},[result])

pose.onResult(resultHandler)
```

pose.onResult()는 굉장히 짧은 간격(실시간)으로 값을 비동기로 넘겨주기 때문에, state가 짧은 간격으로 바뀌고, 그에 비례해 불필요한 렌더링이 계속 되는 Side Effect가 발생하였다.

```javascript
const [result,setResult] = useState(null);

function resultHandler(results) {
    result = results
}

useEffect(()=> {
    drawing(result);
},[result])

pose.onResult(resultHandler)
```

리액트 관련 많은 게시글에서 setResult()를 사용해야 한다는 이야기들이 있었다. 하지만 그 내용은 대체로 렌더링을 위하여 써야한다는 배경을 전제하고 있었다. 
그렇다면 굳이 state를 통해 관리할 필요가 있을까? 우리는 해당 데이터를 다른 컴포넌트에서 사용하지도 않고, 관여하지도 않는다. 
그리고 렌더링을 위해서도 쓰지 않는다. (오히려 렌더링을 되지 말아야 한다) 

```javascript
const result = null;

function resultHandler(results) {
    result = results
}

useEffect(()=> {
    drawing(result);
},[result])

pose.onResult(resultHandler)
```
<code>문제없이 잘 작동된다.</code>