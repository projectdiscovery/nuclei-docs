### Headless Requests

Nuclei는 간단한 DSL로 브라우저 자동화를 지원합니다. headless 브라우저 엔진은 완전히 사용자 정의할 수 있으며 사용자 작업을 스크립팅하여 브라우저를 완벽하게 제어할 수 있습니다. 이를 통해 다양한 고유한 사용자 지정 워크플로를 사용할 수 있습니다.

```yaml
# 템플릿 요청을 바로 여기에서 시작하세요.
headless:
```

#### Actions

Action은 Nuclei Headless Engine을 위한 단일 작업입니다. 각 작업은 어떤 방식으로든 브라우저 상태를 조작하고 마침내 캡처하려는 상태로 이어집니다.

Nuclei는 다양한 작업을 지원합니다. 이러한 작업 목록과 해당 인수는 다음과 같습니다.

#### navigate

주어진 URL을 탐색합니다. url 필드는 `{{BaseURL}}`, `{{Hostname}}`과 같은 변수를 지원하여 요청을 완전히 사용자 정의합니다.

```yaml
action: navigate
args: 
  url: "{{BaseURL}}
```

##### script

스크립트는 현재 브라우저 페이지에서 JS 코드를 실행합니다. 가장 간단한 수준에서 실행하려는 JS snippet과 함께 `code` 인수를 제공하면 페이지에서 실행됩니다.

```yaml
action: script
args:
  code: alert(document.domain)
```

값을 검사하기 위해 JS 객체에서 매처를 실행하고 싶다고 가정합니다. 이러한 유형의 데이터 추출 사용 사례는 nuclei headless에서도 지원됩니다. 예를 들어 애플리케이션이 `window.random-object`라는 개체를 값으로 설정하고 해당 값과 일치시키려고 한다고 가정해 보겠습니다.

```yaml
- action: script
  args:
    code: window.random-object
  name: script-name
...
matchers:
  - type: word
    part: script-name
    words:
      - "some-value"
```

Nuclei는 `hook` 인수로 페이지를 로드하기 전에 일부 사용자 정의 자바스크립트 실행을 지원합니다. 이것은 페이지가 로드되기 전에 항상 제공된 자바스크립트를 실행합니다.

예제는 애플리케이션에서 생성된 경고가 크롤러를 중지하지 않도록 `window.alert`를 후크합니다.

```yaml
- action: script
  args:
    code: (function() { window.alert=function(){} })()
    hook: true
```

이것은 하나의 사용 사례이며 DOM XSS 감지 및 Javascript-Injection 기반 테스트 기술과 같은 기능 후킹의 사용 사례가 더 많습니다. 추가 예제는 예제 페이지에 제공됩니다.

##### click

클릭은 선택기로 지정된 요소를 마우스 왼쪽 버튼으로 클릭하는 것을 시뮬레이션합니다.

```yaml
action: click
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

Nuclei는 XPath, Regex, CSS 등을 포함하되 이에 국한되지 않는 다양한 선택기 유형을 지원합니다. 선택기에 대한 자세한 내용은 [here](#selectors).

##### rightclick

RightClick은 선택기로 지정된 요소를 마우스 오른쪽 버튼으로 클릭하는 것을 시뮬레이션합니다.

```yaml
action: rightclick
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

##### text

텍스트는 키보드로 입력에 무언가를 입력하는 것을 시뮬레이션합니다. 선택기를 사용하여 입력할 요소를 지정할 수 있습니다.

```yaml
action: text
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  value: username
```

##### screenshot

스크린샷은 페이지의 스크린샷을 찍어 디스크에 씁니다. 전체 페이지와 일반 스크린샷을 모두 지원합니다.

```yaml
action: screenshot
args: 
  to: /root/test/screenshot-web
```

전체 페이지 스크린샷이 필요한 경우 인수에 `fullpage: true` 옵션을 사용하면 됩니다.

```yaml
action: screenshot
args: 
  to: /root/test/screenshot-web
  fullpage: true
```

##### time

시간은 RFC3339 형식의 페이지에서 시간 입력에 값을 입력합니다.

```yaml
action: time
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  value: 2006-01-02T15:04:05Z07:00
```

##### select

select은 selection에 의해 HTML 입력에 대한 selector을 수행합니다.

```yaml
action: select
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  selected: true
  value: option[value=two]
  selector: regex
```

##### files

Files는 웹페이지에서 파일 업로드 입력을 처리합니다.

```yaml
action: files
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  value: /root/test/payload.txt
```

##### waitload

WaitLoads는 페이지가 로드를 완료하고 유휴 상태가 될 때까지 기다립니다.

```yaml
action: waitload
```

Nuclei의 `waitload` 작업은 DOM이 로드되기를 기다리고 window.onload 이벤트가 수신되기를 기다린 후 페이지가 1초 동안 유휴 상태가 될 때까지 기다립니다.

##### getresource

GetResource는 요소에 대한 src 속성을 반환합니다.

```yaml
action: getresource
name: extracted-value-src
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

##### extract

Extract는 HTML 노드의 텍스트 또는 사용자가 지정한 속성을 추출합니다.

아래 코드는 지정된 XPath 선택기 요소에 대한 텍스트를 추출합니다. 그러면 매처 및 추출기와 함께 `extracted-value`이라는 이름으로 일치될 수도 있습니다.

```yaml
action: extract
name: extracted-value
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
```

요소에 대한 속성도 추출할 수 있습니다. 예를 들어 -

```yaml
action: extract
name: extracted-value-href
args: 
  by: xpath
  xpath: /html/body/div[1]/div[3]/form/div[2]/div[1]/div[1]/div/div[2]/input
  target: attribute
  attribute: href
```

##### setmethod

SetMethod는 요청에 대한 메서드를 재정의합니다.

```yaml
action: setmethod
args: 
  part: request
  method: DELETE
```

##### addheader

AddHeader는 요청/응답에 헤더를 추가합니다. 이것은 기존 헤더를 덮어쓰지 않습니다.

```yaml
action: addheader
args: 
  part: response # can be request too
  key: Content-Security-Policy
  value: "default-src * 'unsafe-inline' 'unsafe-eval' data: blob:;"
```

##### setheader

SetHeader는 요청/응답에 헤더를 설정합니다.

```yaml
action: setheader
args: 
  part: response # can be request too
  key: Content-Security-Policy
  value: "default-src * 'unsafe-inline' 'unsafe-eval' data: blob:;"
```

##### deleteheader

헤더 삭제는 요청/응답에서 헤더를 삭제합니다.

```yaml
action: deleteheader
args: 
  part: response # can be request too
  key: Content-Security-Policy
```

##### setbody

SetBody는 요청/응답의 본문을 설정합니다.

```yaml
action: setbody
args: 
  part: response # can be request too
  body: '{"success":"ok"}'
```

##### waitevent

WaitEvent는 페이지에서 이벤트가 트리거될 때까지 기다립니다.

```yaml
action: waitevent
args: 
  event: 'Page.loadEventFired'
```

지원되는 이벤트 목록은 [here](https://github.com/go-rod/rod/blob/master/lib/proto/definitions.go)에 나와 있습니다.

##### keyboard

키보드는 키보드의 단일 키 누르기를 시뮬레이션합니다.

```yaml
action: keyboard
args: 
  keys: '\r' # 이것은 키보드에서 Enter 키를 누르는 것을 시뮬레이션합니다.
```

'keys' 인수는 키 코드를 허용합니다.

##### debug

디버그는 각 헤드리스 작업 사이에 5초의 지연을 추가하고 브라우저에서 발생하는 모든 헤드리스 이벤트의 추적도 표시합니다.

> 참고: 디버깅 목적으로만 사용하고 프로덕션 템플릿에서는 사용하지 마십시오.

```yaml
action: debug
```

##### sleep

절전 모드는 브라우저가 지정된 시간(초) 동안 기다리게 합니다. 이것은 디버깅 목적으로도 유용합니다.

```yaml
action: sleep
args:
  duration: 5
```

#### Selectors

선택기는 nuclei 헤드리스 엔진이 작업을 실행할 요소를 식별하는 방법입니다. Nuclei는 다양한 옵션을 포함하여 선택기 가져오기를 지원합니다.

| Selector             | Description                                         |
|----------------------|-----------------------------------------------------|
| `r` / `regex`        | Element matches CSS Selector and Text Matches Regex |
| `x` / `xpath`        | Element matches XPath selector                      |
| `js`                 | Return elements from a JS function                  |
| `search`             | Search for a query (can be text, XPATH, CSS)        |
| `selector` (default) | Element matches CSS Selector                        |


#### Matchers / Extractor Parts

Matchers/Extractor용 **Headless** 프로토콜이 지원하는 유효한 `part` 값은 다음과 같습니다.
    
| Value             | Description                     |
|-------------------|---------------------------------|
| request           | Headless Request                |
| `<out_names>`     | Action names with stored values |
| raw / body / data | Final DOM response from browser |


#### **Example Headless Template**

DVWA에 자동으로 로그인하는 헤드리스 템플릿의 예는 다음과 같습니다.


```yaml
id: dvwa-headless-automatic-login
info:
  name: DVWA Headless Automatic Login
  author: pdteam
  severity: high
headless:
  - steps:
      - args:
          url: "{{BaseURL}}/login.php"
        action: navigate
      - action: waitload
      - args:
          by: xpath
          xpath: /html/body/div/div[2]/form/fieldset/input
        action: click
      - action: waitload
      - args:
          by: xpath
          value: admin
          xpath: /html/body/div/div[2]/form/fieldset/input
        action: text
      - args:
          by: xpath
          xpath: /html/body/div/div[2]/form/fieldset/input[2]
        action: click
      - action: waitload
      - args:
          by: xpath
          value: password
          xpath: /html/body/div/div[2]/form/fieldset/input[2]
        action: text
      - args:
          by: xpath
          xpath: /html/body/div/div[2]/form/fieldset/p/input
        action: click
      - action: waitload
    matchers:
      - part: resp
        type: word
        words:
          - "You have logged in as"
```

더 완전한 예가 제공됩니다. [here](../../template-examples/headless.md)
