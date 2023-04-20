### Network Requests

Nuclei는 자동화 가능한 **Netcat** 역할을 하여 사용자가 바이트를 유선으로 보내고 받을 수 있으며 응답에 대한 일치 및 추출 기능을 제공합니다.

네트워크 요청은 템플릿에 대한 요청의 시작을 지정하는 **network** 블록으로 시작합니다.

```yaml
# 템플릿 요청을 바로 여기에서 시작하세요.
tcp:
```

#### Inputs

요청의 첫 번째 항목은 **inputs**입니다. 입력은 서버로 보낼 데이터이며 선택적으로 서버에서 읽을 데이터입니다.

가장 간단합니다. 문자열을 지정하면 네트워크 소켓을 통해 전송됩니다.

```yaml
# inputs은 서버에 보낼 입력 목록입니다.
inputs: 
  - data: "TEST\r\n"
```

또한 먼저 디코딩되고 Raw 바이트가 서버로 전송될 16진수로 인코딩된 텍스트를 보낼 수 있습니다.

```yaml
inputs:
  - data: "50494e47"
    type: hex
  - data: "\r\n"
```

도우미 함수 표현식은 입력에서도 정의할 수 있으며 먼저 평가된 다음 서버로 전송됩니다. 마지막 Hex Encoded 예제는 이런 식으로 도우미 함수와 함께 보낼 수 있습니다.

```yaml
inputs:
  - data: 'hex_decode("50494e47")\r\n'
```

입력으로 수행할 수 있는 마지막 작업은 소켓에서 데이터를 읽는 것입니다. 0이 아닌 값으로 `read-size`를 지정하면 트릭이 수행됩니다. 읽은 데이터에 이름을 지정하여 해당 부분에서 일치를 수행할 수도 있습니다.

```yaml
inputs:
  - read-size: 8
```

여러 바이트를 읽고 일치하는 경우의 예입니다.

```yaml
inputs:
  - read-size: 8
    name: prefix
...
matchers:
  - type: word
    part: prefix
    words: 
      - "CAFEBABE"
```

여러 단계를 순서대로 연결하여 네트워크 읽기/쓰기를 수행할 수 있습니다.

#### Host

요청의 다음 부분은 연결할 **host**입니다. 동적 변수를 경로에 배치하여 런타임에 값을 수정할 수 있습니다. 변수는 `{{`로 시작하고 `}}`로 끝나며 대소문자를 구분합니다.

1. **Hostname** - 변수는 명령줄에 제공된 호스트 이름으로 대체됩니다.

예제 이름 값:

```yaml
host: 
  - "{{Hostname}}"
```

Nuclei는 또한 대상 서버에 TLS 연결을 수행할 수 있습니다. **Hostname** 앞에 접두사로 `tls://`를 추가하기만 하면 됩니다.

```yaml
host:
  - "tls://{{Hostname}}"
```

호스트에 포트가 지정되면 사용자가 제공한 포트는 무시되고 템플릿 포트가 우선합니다.

#### Matchers / Extractor Parts

Matchers/Extractor용 **Network** 프로토콜에서 지원하는 유효한 `part` 값은 다음과 같습니다. 
    
| Value            | Description                         |
|------------------|-------------------------------------|
| request          | Network Request                     |
| data             | Final Data Read From Network Socket |
| raw / body / all | All Data received from Socket       |


#### **Example Network Template**

작동하는 matchers가 있는 서버에서 실행되는 MongoDB를 감지하기 위한 '16진수'로 인코딩된 입력에 대한 최종 예제 템플릿 파일은 아래에 제공됩니다.

```yaml
id: input-expressions-mongodb-detect

info:
  name: Input Expression MongoDB Detection
  author: pd-team
  severity: info
  reference: https://github.com/orleven/Tentacle

tcp:
  - inputs:
      - data: "{{hex_decode('3a000000a741000000000000d40700000000000061646d696e2e24636d640000000000ffffffff130000001069736d6173746572000100000000')}}"
    host:
      - "{{Hostname}}"
    read-size: 2048
    matchers:
      - type: word
        words:
          - "logicalSessionTimeout"
          - "localTime"
```

더 완전한 예가 제공됩니다. [here](../../template-examples/network.md)
