# 자바스크립트 비동기 처리와 콜백 함수

### 비동기 처리? 그게 뭔가요?
자바스크립트의 비동기 처리란 특정 코드의 연산이 끝날 때까지 코드의 실행을 멈추지 않고 다음 코드를 먼저 실행하는 자바스크립트의 특성을 의미합니다.

### 비동기 처리의 첫 번째 사례
비동기 처리의 가장 흔한 사례는 제이쿼리의 ajax입니다.
제이쿼리로 실제 웹 서비스를 개발할 때 ajax 통신을 빼놓을 수가 없습니다.
보통 화면에 표시할 이미지나 데이터를 서버에서 불러와 표시해야 하는데 이때 ajax 통신으로 해당 데이터를 서버로부터 가져올 수 있기 때문입니다.(ex. 공공 API)

그럼 ajax 코드를 잠깐 살펴보겠습니다.

```javascript
function getData() {
	var tableData;
	$.get('https://domain.com/products/1', function(response) {
		tableData = response;
	});
	return tableData;
}

console.log(getData()); // undefined
```

여기서 $.get()이 ajax 통신을 하는 부분입니다. https://domain.com 에다가 HTTP GET 요청을 날려 1번 상품(product) 정보를 요청하는 코드죠.
좀 더 쉽게 말하면 지정된 URL에 저장된 ‘데이터를 하나 보내주세요’ 라는 요청을 날리는 것과 같습니다.

그렇게 서버에서 받아온 데이터는 response 인자에 담깁니다. 그리고 tableData = response; 코드로 받아온 데이터를 tableData라는 변수에 저장합니다. 
그럼 이제 이 getData()를 호출하면 어떻게 될까요? 받아온 데이터가 뭐든 일단 뭔가 찍혀야겠죠. 
근데 결과는 맨 아래에서 보시는 것처럼 undefined입니다. 왜 그럴까요? 

그 이유는 $.get()로 데이터를 요청하고 받아올 때까지 기다려주지 않고 다음 코드인 return tableData;를 실행했기 때문입니다.
외부와 데이터 통신을 하면 받아오는데 까지 시간이 0초 이상은 걸리게 됩니다.
따라서, getData()의 결과 값은 초기 값을 설정하지 않은 tableData의 값(var 사용) undefined를 출력합니다.

`이렇게 특정 로직의 실행이 끝날 때까지 기다려주지 않고 나머지 코드를 먼저 실행하는 것이 비동기 처리입니다.` 정보가 올 때까지 기다리지 않음(비동기)
자바스크립트에서 비동기 처리가 필요한 이유를 생각해보면,
화면에서 서버로 데이터를 요청했을 때 서버가 언제 그 요청에 대한 응답을 줄지도 모르는데
마냥 다른 코드를 실행 안 하고 기다릴 순 없기 때문입니다. 
위에선 간단한 요청 1개만 보냈는데 만약 100개 보낸다고 생각해보세요.
100개를 기다릴 순 없습니다.
비동기 처리가 아니고 동기 처리라면 코드 실행하고 기다리고, 실행하고 기다리고..
아마 웹 애플리케이션을 실행하는데 수십 분은 걸릴 겁니다.

### 콜백 함수로 비동기 처리 방식의 문제점 해결하기

```javascript
function getData(callbackFunc) {
	$.get('https://domain.com/products/1', function(response) {
		callbackFunc(response); // 서버에서 받은 데이터 response를 callbackFunc() 함수에 넘겨줌
	});
}

getData(function(tableData) {
	console.log(tableData); // $.get()의 response 값이 tableData에 전달됨
});
```

이렇게 콜백 함수를 사용하면 특정 로직이 끝났을 때 원하는 동작을 실행시킬 수 있습니다. 
데이터를 불러오면 비로소 익명 함수가 실행되서 console.log(tableData) 를 실행하게 됩니다.

### 비유로 이해하는 콜백 함수 동작 방식
콜백 함수의 동작 방식은 일종의 식당 자리 예약과 같습니다. 
일반적으로 맛집을 가면 사람이 많아 자리가 없습니다.
그래서 대기자 명단에 이름을 쓴 다음에 자리가 날 때까지 주변 식당을 돌아다니죠. 
만약 식당에서 자리가 생기면 전화로 자리가 났다고 연락이 옵니다.
자리가 나면 비로소 손님에게 전화를 합니다. (데이터가 오면 비로소 콜백함수를 호출합니다)
그 전화를 받는 시점이 여기서의 콜백 함수가 호출되는 시점과 같습니다. 
손님 입장에서는 자리가 날 때까지 식당에서 기다리지 않고 근처 가게에서 잠깐 쇼핑을 할 수도 있고 아니면 다른 식당 자리를 알아볼 수도 있습니다.

자리가 났을 때만 연락이 오기 때문에 미리 가서 기다릴 필요도 없고,
직접 식당 안에 들어가서 자리가 비어 있는지 확인할 필요도 없습니다. 자리가 준비된 시점,
즉 데이터가 준비된 시점에서만 저희가 원하는 동작(자리에 앉는다, 특정 값을 출력한다 등)을 수행할 수 있습니다.

---

### 참고 자료

- [캡틴 판교님](https://joshua1988.github.io/web-development/javascript/javascript-asynchronous-operation/)
