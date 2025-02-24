---
title: AnalyserNode.getFloatFrequencyData()
slug: Web/API/AnalyserNode/getFloatFrequencyData
tags:
  - API
  - AnalyserNode
  - Method
  - Reference
  - Web Audio API
browser-compat: api.AnalyserNode.getFloatFrequencyData
---
{{ APIRef("Web Audio API") }}

{{domxref("AnalyserNode")}} 인터페이스의 **`getFloatFrequencyData()`** 메서드는 전달된 {{domxref("Float32Array")}} 배열 내로 현재 주파수 데이터를 복사합니다.

배열 내의 각 원소는 특정한 주파수에 대한 데시벨 값을 나타냅니다. 주파수들은 0에서 샘플 레이트의 1/2까지 선형적으로 퍼져 있습니다. 예를 들자면, `48000` Hz 샘플 레이트에 대해서, 배열의 마지막 원소는 `24000` Hz에 대한 데시벨 값을 나타냅니다.

만약 여러분이 더 높은 성능을 원하고 정밀성에 대해서는 상관하지 않는다면, {{domxref("AnalyserNode.getByteFrequencyData()")}}을 대신 사용할 수 있는데, 이는 {{domxref("Uint8Array")}}에서 동작합니다.

## 구문

```js
var audioCtx = new AudioContext();
var analyser = audioCtx.createAnalyser();
var dataArray = new Float32Array(analyser.frequencyBinCount); // Float32Array는 frequencyBinCount와 같은 길이여야만 합니다

void analyser.getFloatFrequencyData(dataArray); // getFloatFrequencyData()로부터 반환된 데이터로 Float32Array를 채웁니다
```

### 매개변수

- `array`
  - : 주파수 영역 데이터가 복사될 {{domxref("Float32Array")}}. 소리가 없는 모든 샘플에 대해서, 값은 `-Infinity`입니다.
    만약 배열이 {{domxref("AnalyserNode.frequencyBinCount")}}보다 더 적은 요소를 가지고 있다면, 초과한 요소는 탈락됩니다. 만약 이것이 필요한 것보다 더 많은 요소를 가지고 있다면, 초과한 요소는 무시됩니다.

### 반환 값

없음.

## 예제

```js
const audioCtx = new AudioContext();
const analyser = audioCtx.createAnalyser();
// Float32Array는 frequencyBinCount와 같은 길이여야만 합니다
const myDataArray = new Float32Array(analyser.frequencyBinCount);
// getFloatFrequencyData()로부터 반환된 데이터로 Float32Array를 채웁니다
analyser.getFloatFrequencyData(myDataArray);
```

### 스펙트럼 그리기

다음의 예제는 {{domxref("MediaElementAudioSourceNode")}}를 `AnalyserNode`에 연결하기 위한 {{domxref("AudioContext")}}의 기본 사용을 보여줍니다. 오디오가 재생되는 동안, 우리는 {{domxref("window.requestAnimationFrame()","requestAnimationFrame()")}}로 주파수 데이터를 반복적으로 수집하고 "winamp 막대그래프 스타일"을 {{htmlelement("canvas")}} 요소에 그립니다.

더 완벽한 응용 예제/정보를 보려면 [Voice-change-O-matic-float-data](https://mdn.github.io/voice-change-o-matic-float-data/) 데모를 확인하세요 ([소스 코드](https://github.com/mdn/voice-change-o-matic-float-data)도 보세요).

```html
<!doctype html>
<body>
<script>
const audioCtx = new AudioContext();

//오디오 소스를 생성합니다
//여기서, 우리는 오디오 파일을 사용하나, 이것은 또한 예를 들자면 마이크 입력도 될 수 있습니다
const audioEle = new Audio();
audioEle.src = 'my-audio.mp3';//파일명을 여기 삽입하세요
audioEle.autoplay = true;
audioEle.preload = 'auto';
const audioSourceNode = audioCtx.createMediaElementSource(audioEle);

//analyser 노드를 생성합니다
const analyserNode = audioCtx.createAnalyser();
analyserNode.fftSize = 256;
const bufferLength = analyserNode.frequencyBinCount;
const dataArray = new Float32Array(bufferLength);

//오디오 노드 네트워크를 설정합니다
audioSourceNode.connect(analyserNode);
analyserNode.connect(audioCtx.destination);

//2D canvas를 생성합니다
const canvas = document.createElement('canvas');
canvas.style.position = 'absolute';
canvas.style.top = 0;
canvas.style.left = 0;
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;
document.body.appendChild(canvas);
const canvasCtx = canvas.getContext('2d');
canvasCtx.clearRect(0, 0, canvas.width, canvas.height);

function draw() {
  //다음 draw를 예정시킵니다
  requestAnimationFrame(draw);

  //스펙트럼 데이터를 얻습니다
  analyserNode.getFloatFrequencyData(dataArray);

  //검은색 배경을 그립니다
  canvasCtx.fillStyle = 'rgb(0, 0, 0)';
  canvasCtx.fillRect(0, 0, canvas.width, canvas.height);

  //스펙트럼을 그립니다
  const barWidth = (canvas.width / bufferLength) * 2.5;
  let posX = 0;
  for (let i = 0; i &#x3C; bufferLength; i++) {
    const barHeight = (dataArray[i] + 140) * 2;
    canvasCtx.fillStyle = 'rgb(' + Math.floor(barHeight + 100) + ', 50, 50)';
    canvasCtx.fillRect(posX, canvas.height - barHeight / 2, barWidth, barHeight / 2);
    posX += barWidth + 1;
  }
};

draw();
</script>
</body>
```

## 명세

{{Specifications}}

## 브라우저 호환성

{{Compat}}

## 같이 보기

- [Web Audio API 사용하기](/ko/docs/Web/API/Web_Audio_API/Using_Web_Audio_API)
