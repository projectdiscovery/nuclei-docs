!!! info "Requests"

    Nuclei는 HTTP 프로토콜과 관련된 다양한 기능을 광범위하게 지원합니다. None-RFC 클라이언트 요청도 지원하는 옵션과 함께 raw 및 모델 기반 HTTP 요청이 지원됩니다. 페이로드를 지정할 수도 있고 페이로드 값을 기반으로 raw 요청을 변환할 수 있으며 이 페이지의 뒷부분에 표시되는 더 많은 기능을 사용할 수 있습니다.

    HTTP 요청은 템플릿에 대한 요청의 시작을 지정하는 'request' 블록으로 시작합니다.

```yaml
# 템플릿 요청을 바로 여기에서 시작하세요.
http:
```

!!! info "Method"
    요청 방법은 필요에 따라 **GET**, **POST**, **PUT**, **DELETE** 등이 될 수 있습니다.

```yaml
# Method는 요청에 대한 메소드입니다.
method: GET
```

!!! info "Redirects"

    각 템플릿별로 리디렉션 조건을 지정할 수 있습니다. 기본적으로 리디렉션은 따르지 않습니다. 그러나 원하는 경우 요청 세부정보에서 'redirects: true'로 활성화할 수 있습니다. 기본적으로 최대 10개의 리디렉션이 따르므로 대부분의 사용 사례에 충분합니다. 'max-redirects' 필드를 사용하여 리디렉션 수에 대해 더 세분화된 제어를 실행할 수 있습니다.

사용법의 예:

```yaml
http:
  - method: GET
    path:
      - "{{BaseURL}}/login.php"
    redirects: true
    max-redirects: 3
```

!!! warning
    현재 리디렉션은 요청별로가 아니라 템플릿별로 정의됩니다.

!!! info "Path"

    요청의 다음 부분은 요청 경로의 **path**입니다. 동적 변수를 경로에 배치하여 런타임 시 동작을 수정할 수 있습니다.

    변수는 `{{`로 시작하고 `}}`로 끝나며 대소문자를 구분합니다.

    **{{BaseURL}}** - 이것은 요청의 런타임 시 대상 파일에 지정된 Base URL로 대체됩니다.

    **{{RootURL}}** - 이것은 요청의 런타임 시 대상 파일에 지정된 Root URL로 대체됩니다.

    **{{Hostname}}** - 호스트 이름 변수는 런타임에 대상의 포트를 포함하는 Hostname으로 대체됩니다.

    **{{Host}}** - 이것은 대상 파일에 지정된 대로 입력 Host에 의한 요청의 런타임 시 대체됩니다.

    **{{Port}}** - 이것은 요청의 런타임 시 대상 파일에 지정된 입력 Port로 대체됩니다.

    **{{Path}}** - 이것은 요청의 런타임 시 대상 파일에 지정된 입력 Path로 대체됩니다.

    **{{File}}** - 이것은 요청의 런타임 시 대상 파일에 지정된 입력 파일 이름으로 대체합니다.

    **{{Scheme}}** - 이것은 대상 파일에 지정된 프로토콜 체계에 의한 요청의 런타임 시 대체됩니다.

아래에 예가 제공됩니다. - https://example.com:443/foo/bar.php

| Variable     | Value                               |
|--------------|-------------------------------------|
| {{BaseURL}}  | https://example.com:443/foo/bar.php |
| {{RootURL}}  | https://example.com:443             |
| {{Hostname}} | example.com:443                     |
| {{Host}}     | example.com                         |
| {{Port}}     | 443                                 |
| {{Path}}     | /foo                                |
| {{File}}     | bar.php                             |
| {{Scheme}}   | https                               |


몇 가지 샘플 동적 변수 대체 예:

```yaml
path: "{{BaseURL}}/.git/config"
# 이 경로는 실행 시 BaseURL로 대체됩니다.
# BaseURL이 https://abc.com 으로 설정된 경우
# 경로는 https://abc.com/.git/config 로 대체됩니다.
```

대상에 대해 요청될 하나의 요청에 여러 경로를 지정할 수도 있습니다.

#### Headers

Headers는 요청과 함께 보내도록 지정할 수도 있습니다. 헤더는 키/값 쌍의 형태로 배치됩니다. 헤더 구성의 예는 다음과 같습니다:

```yaml
# headers contain the headers for the request
headers:
  # Custom user-agent header
  User-Agent: Some-Random-User-Agent
  # Custom request origin
  Origin: https://google.com
```

#### Body

Body는 요청과 함께 보낼 본문을 지정합니다. 예를 들어:

```yaml
# Body는 요청과 함께 전송되는 문자열입니다.
body: "{\"some random JSON\"}"

# Body는 요청과 함께 전송되는 문자열입니다.
body: "admin=test"
```

#### Session

여러 요청 간에 세션과 같은 쿠키 기반 브라우저를 유지하려면 템플릿에서 `cookie-reuse: true`를 사용하면 됩니다. 익스플로잇 체인을 완료하고 인증된 스캔을 수행하기 위해 일련의 요청 간에 세션을 유지하려는 경우에 유용합니다.

```yaml
# cookie-reuse는 기본적으로 부울 입력 및 false를 허용합니다.
cookie-reuse: true
```

#### Request Condition

요청 조건을 사용하면 복잡한 검사를 작성하기 위한 여러 요청과 익스플로잇 체인을 완료하기 위한 여러 HTTP 요청을 포함하는 익스플로잇 간의 조건을 확인할 수 있습니다.

DSL 매처와 함께 `req-condition: true`와 각 속성의 접미사로 숫자를 추가하여 활용할 수 있습니다(예: `status_code_1`, `status_code_3`, `body_2`).

```yaml
    req-condition: true
    matchers:
      - type: dsl
        dsl:
          - "status_code_1 == 404 && status_code_2 == 200 && contains((body_2), 'secret_string')"
```

#### **Example HTTP Template**

위에서 언급한 `.git/config` 파일의 최종 템플릿 파일은 다음과 같습니다.

```yaml
id: git-config

info:
  name: Git Config File
  author: Ice3man
  severity: medium
  description: Searches for the pattern /.git/config on passed URLs.

http:
  - method: GET
    path:
      - "{{BaseURL}}/.git/config"
    matchers:
      - type: word
        words:
          - "[core]"
```

### RAW HTTP requests

요청을 생성하는 또 다른 방법은 다음과 같은 DSL 도우미 기능을 더 유연하게 지원하고 지원하는 Raw 요청을 사용하는 것입니다(현재로서는 `{{Hostname}}`), 모든 Matcher, Extractor 기능은 위에서 설명한 것과 동일한 방식으로 RAW 요청과 함께 사용할 수 있습니다.

```yaml
http:
  - raw:
    - |
        POST /path2/ HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/x-www-form-urlencoded

        a=test&b=pd
```

원하는 대로 정확한 작업을 수행하도록 요청을 미세 조정할 수 있습니다. Nuclei 요청은 완전히 구성할 수 있음을 의미합니다. 즉, 대상 서버로 보낼 요청에 대한 모든 것을 구성하고 정의할 수 있습니다.

RAW 요청 형식은 [various helper functions](https://nuclei.projectdiscovery.io/templating-guide/helper-functions/)도 지원하므로 입력으로 런타임 조작이 가능합니다. 헤더에 도우미 함수를 사용하는 예입니다. 

```yaml
    raw:
      - |
        GET /manager/html HTTP/1.1
        Host: {{Hostname}}
        Authorization: Basic {{base64('username:password')}} # Helper function to encode input at run time.
```

추가 변조 없이 입력으로 지정된 URL에 대한 요청을 수행하려면 아래 지정된 대로 빈 요청 URI를 사용하여 사용자 지정 입력에 대한 요청을 수행할 수 있습니다.

```yaml
    raw:
      - |
        GET HTTP/1.1
        Host: {{Hostname}}
```


### HTTP Fuzzing

!!! info
    Nuclei engine supports fuzzing module that allow to run various type of payloads in multiple format, It's possible to define placeholders with simple keywords (or using brackets `{{helper_function(variable)}}` in case mutator functions are needed), and perform **batteringram**, **pitchfork** and **clusterbomb** attacks. The wordlist for these attacks needs to be defined during the request definition under the Payload field, with a name matching the keyword, Nuclei supports both file based and in template wordlist support and Finally all DSL functionalities are fully available and supported, and can be used to manipulate the final values.

    페이로드는 변수 이름을 사용하여 정의되며 `§ §` 또는 `{{ }}` 마커 사이에서 요청에서 참조할 수 있습니다.

로컬 단어 목록과 함께 페이로드를 사용하는 예:

```yaml
    # HTTP Intruder fuzzing using local wordlist.

    payloads:
      paths: params.txt
      header: local.txt
```

템플릿 단어 목록 지원과 함께 페이로드를 사용하는 예:

```yaml
    # HTTP Intruder fuzzing using in template wordlist.

    payloads:
      password:
        - admin
        - guest
        - password
```

**Note:** 예기치 않은 입력이 템플릿을 손상시키므로 공격 유형을 선택할 때 주의하십시오. 

예를 들어 `clusterbomb` 또는 `pitchfork`를 공격 유형으로 사용하고 페이로드 섹션에 하나의 변수만 정의한 경우 `clusterbomb` 또는 `pitchfork`가 템플릿에서 둘 이상의 변수를 사용할 것으로 예상하므로 템플릿은 컴파일되지 않습니다.

#### Attack mode

Nuclei 엔진은 일반적으로 단일 매개변수를 퍼징하는 데 사용되는 기본 유형인 `batteringram`, 클래식 burp 침입자와 동일하게 작동하는 여러 매개변수를 퍼징하는 `clusterbomb` 및 `pitchfork`를 포함한 여러 공격 유형을 지원합니다.

<table>
  <tr>
    <th>Type</th>
    <td>batteringram</td>
    <td>pitchfork</td>
    <td>clusterbomb</td>

  </tr>
  <tr>
    <th>Support</th>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
  </tr>
</table>

!!! abstract "batteringram"
    batteringram 공격 유형은 모든 위치에 동일한 페이로드 값을 배치합니다. 하나의 페이로드 세트만 사용합니다. 페이로드 세트를 반복하고 모든 위치를 페이로드 값으로 바꿉니다.


!!! abstract "pitchfork"
    pitchfork 공격 유형은 각 위치에 대해 하나의 페이로드 세트를 사용합니다. 첫 번째 페이로드를 첫 번째 위치에 배치하고 두 번째 페이로드를 두 번째 위치에 배치하는 식입니다.

    그런 다음 동시에 모든 페이로드 세트를 반복합니다. 첫 번째 요청은 각 페이로드 세트의 첫 번째 페이로드를 사용하고, 두 번째 요청은 각 페이로드 세트의 두 번째 페이로드를 사용하는 식입니다.

!!! abstract "clusterbomb"
    클러스터 폭탄 공격은 다양한 페이로드 조합을 시도합니다. 여전히 첫 번째 페이로드를 첫 번째 위치에 배치하고 두 번째 페이로드를 두 번째 위치에 배치합니다. 그러나 페이로드 세트를 반복할 때 모든 조합을 시도합니다.

    그런 다음 동시에 모든 페이로드 세트를 반복합니다. 첫 번째 요청은 각 페이로드 세트의 첫 번째 페이로드를 사용하고, 두 번째 요청은 각 페이로드 세트의 두 번째 페이로드를 사용하는 식입니다.

    이 공격 유형은 무차별 대입 공격에 유용합니다. 첫 번째 페이로드 세트에서 일반적으로 사용되는 사용자 이름 목록을 로드하고 두 번째 페이로드 세트에서 일반적으로 사용되는 비밀번호 목록을 로드합니다. 클러스터 폭탄 공격은 모든 조합을 시도합니다.

    자세한 내용은 [here](https://www.sjoerdlangkemper.nl/2017/08/02.burp-intruder-attack-types/). 

fuzz에 `clusterbomb` 공격을 사용한 예.

```yaml
http:
  - raw:
      - |
        POST /?file={{path}} HTTP/1.1
        User-Agent: {{header}}
        Host: {{Hostname}}

    payloads:
      path: helpers/wordlists/prams.txt
      header: helpers/wordlists/header.txt
    attack: clusterbomb # Defining HTTP fuzz attack type
```

#### Unsafe HTTP Requests

Nuclei는 HTTP reqeust smuggling, Host header injection, 잘못된 문자가 포함된 CRLF 등과 같은 문제에 대해 **any kind of malformed requests**을 허용하는 완전한 요청 제어 및 지정을 위한 [rawhttp](https://github.com/projectdiscovery/rawhttp)를 지원합니다.

**rawhttp** 라이브러리는 기본적으로 비활성화되어 있으며 요청 블록에 `unsafe: true`를 포함하여 활성화할 수 있습니다.

다음은 `rawhttp`를 사용한 HTTP 요청 밀수 탐지 템플릿의 예입니다.

```yaml
http:
  - raw:
    - |+
        POST / HTTP/1.1
        Host: {{Hostname}}
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 150
        Transfer-Encoding: chunked

        0

        GET /post?postId=5 HTTP/1.1
        User-Agent: a"/><script>alert(1)</script>
        Content-Type: application/x-www-form-urlencoded
        Content-Length: 5

        x=1
    - |+
        GET /post?postId=5 HTTP/1.1
        Host: {{Hostname}}

    unsafe: true # Enables rawhttp client
    matchers:
      - type: dsl
        dsl:
          - 'contains(body, "<script>alert(1)</script>")'
```


### Advance Fuzzing

우리는 웹 서버의 고급 퍼징을 허용하기 위해 nuclei을 강화했습니다. 이제 사용자는 여러 옵션을 사용하여 HTTP 퍼징 워크플로를 조정할 수 있습니다.
#### Pipelining

HTTP 파이프라이닝 지원이 추가되어 다음에서 영감을 받은 동일한 연결에서 여러 HTTP 요청을 보낼 수 있습니다. [http-desync-attacks-request-smuggling-reborn](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn).

HTTP 파이프라인 기반 템플릿을 실행하기 전에 실행 중인 대상이 HTTP 파이프라인 연결을 지원하는지 확인하십시오. 그렇지 않으면 핵 엔진이 표준 HTTP 요청 엔진으로 폴백합니다.

주어진 도메인 또는 하위 도메인 목록이 HTTP 파이프라이닝을 지원하는지 확인하려면 [httpx](https://github.com/projectdiscovery/)에 `-pipeline` 플래그가 있습니다.

nuclei의 파이프라이닝 속성을 표시하는 구성의 예입니다.

```yaml
    unsafe: true
    pipeline: true
    pipeline-concurrent-connections: 40
    pipeline-requests-per-connection: 25000
```


nuclei의 파이프라이닝 기능을 보여주는 예제 템플릿이 아래에 제공되었습니다.

```yaml
id: pipeline-testing
info:
  name: pipeline testing
  author: pdteam
  severity: info

http:
  - raw:
      - |+
        GET /{{path}} HTTP/1.1
        Host: {{Hostname}}
        Referer: {{BaseURL}}

    attack: batteringram
    payloads:
      path: path_wordlist.txt

    unsafe: true
    pipeline: true
    pipeline-concurrent-connections: 40
    pipeline-requests-per-connection: 25000

    matchers:
      - type: status
        part: header
        status:
          - 200
```

#### Connection pooling

이전 버전의 nuclei는 연결 풀링을 수행하지 않았지만 이제 사용자는 HTTP 연결 풀링을 사용하거나 사용하지 않도록 템플릿을 구성할 수 있습니다. 이를 통해 요구 사항에 따라 더 빠른 스캔이 가능합니다.

템플릿에서 연결 풀링을 활성화하려면 페이로드 섹션에서 사용하려는 각 스레드 수로 `threads` 속성을 정의할 수 있습니다.

`Connection: Close` 헤더는 HTTP 연결 풀링 템플릿에서 사용할 수 없습니다. 그렇지 않으면 엔진이 실패하고 풀링이 있는 표준 HTTP 요청으로 대체됩니다.

HTTP 연결 풀링을 사용하는 예제 템플릿-

```yaml
id: fuzzing-example
info:
  name: Connection pooling example
  author: pdteam
  severity: info

http:

  - raw:
      - |
        GET /protected HTTP/1.1
        Host: {{Hostname}}
        Authorization: Basic {{base64('admin:§password§')}}

    attack: batteringram
    payloads:
      password: password.txt
    threads: 40

    matchers-condition: and
    matchers:
      - type: status
        status:
          - 200

      - type: word
        words:
          - "Unique string"
        part: body
```

#### Smuggling

HTTP Smuggling은 최근 [Portswigger's Research](https://portswigger.net/research/http-desync-attacks-request-smuggling-reborn)에서 화제가 된 웹 공격 클래스입니다. 자세한 개요는 위에 링크된 기사를 참조하십시오.

오픈 소스 공간에서 http 밀수 탐지는 특히 탐지 요청의 특성상 기형적이기 때문에 탐지가 어렵습니다. Nuclei는 [rawhttp](https://github.com/projectdiscovery/rawhttp) 엔진을 활용하여 HTTP Smuggling 취약점을 안정적으로 탐지할 수 있습니다.

HTTP Smuggling 취약점의 가장 기본적인 예는 CL.TE Smuggling입니다. rawhttp 기반 요청에 대해 `unsafe: true` 속성을 사용하여 CE.TL HTTP Smuggling 취약점을 감지하는 예제 템플릿이 아래에 제공됩니다.

```yaml
id: CL-TE-http-smuggling

info:
  name: HTTP request smuggling, basic CL.TE vulnerability
  author: pdteam
  severity: info
  reference: https://portswigger.net/web-security/request-smuggling/lab-basic-cl-te

http:
  - raw:
    - |+
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked
      
      0
      
      G      
    - |+
      POST / HTTP/1.1
      Host: {{Hostname}}
      Connection: keep-alive
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6
      Transfer-Encoding: chunked
      
      0
      
      G
            
    unsafe: true
    matchers:
      - type: word
        words:
          - 'Unrecognized method GPOST'
```

더 많은 예제는 다음에서 사용할 수 있습니다. [template-examples](https://nuclei.projectdiscovery.io/template-examples/http-smuggling/) section for smuggling templates.


#### Race conditions

경쟁 조건은 기존 도구를 통해 쉽게 자동화되지 않는 또 다른 유형의 버그입니다. Burp Suite는 모든 요청에 대한 모든 바이트가 전송 이벤트를 동기화하는 모든 요청에 대해 함께 전송되는 한 번에 마지막 바이트를 예상하는 Turbo Intruder에 게이트 메커니즘을 도입했습니다.

우리는 Nuclei 엔진에 **Gate** 메커니즘을 구현했으며 템플릿을 통해 실행할 수 있도록 하여 이 특정 버그 클래스에 대한 테스트를 간단하고 이식 가능하게 만듭니다.

템플릿 내에서 경쟁 조건 확인을 활성화하려면 `race` 속성을 `true`로 설정하고 `race_count`는 시작하려는 동시 요청 수를 정의합니다.

다음은 게이트 로직을 사용하여 동일한 요청이 10번 반복되는 예제 템플릿입니다.

```yaml
id: race-condition-testing

info:
  name: Race condition testing
  author: pdteam
  severity: info

http:
  - raw:
      - |
        POST /coupons HTTP/1.1
        Host: {{Hostname}}

        promo_code=20OFF        

    race: true
    race_count: 10

    matchers:
      - type: status
        part: header
        status:
          - 200
```

`POST` 요청을 취약한 것으로 의심되는 요청으로 교체하고 필요에 따라 `race_count`를 변경하면 실행할 준비가 됩니다.

```bash
nuclei -t race.yaml -target https://api.target.com
```

**Multi request race condition testing**

경쟁 조건을 이용하기 위해 여러 요청을 보내야 하는 시나리오의 경우 스레드를 사용할 수 있습니다.

```yaml
    threads: 5
    race: true
```

`threads`는 경쟁 조건 테스트를 수행하기 위해 템플릿으로 만들고자 하는 총 요청 수입니다.


다음은 게이트 로직을 사용하여 동시에 여러(5) 개의 고유 요청이 전송되는 예제 템플릿입니다.

```yaml
id: multi-request-race

info:
  name: Race condition testing with multiple requests
  author: pd-team
  severity: info

http:
  - raw:  
      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=1
        
      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=2

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=3

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=4

      - |
        POST / HTTP/1.1
        Pragma: no-cache
        Host: {{Hostname}}
        Cache-Control: no-cache, no-transform
        User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0

        id=5

    threads: 5
    race: true
```

**Requests Annotation**

요청 인라인 주석은 요청 속성/동작 재정의에 따라 수행할 수 있습니다. python/java 클래스 주석과 매우 유사하며 RFC 라인 바로 앞에 요청에 넣어야 합니다. 현재 다음 재정의만 지원됩니다.:

- `@Host:` 요청의 실제 대상을 재정의합니다(일반적으로 입력으로 제공되는 host/ip). ip/domain, port 및 schema가 있는 구문을 지원합니다(예: `domain.tld`, `domain.tld:port`, `http://domain.tld:port`).
- `@tls-sni:` 이는 TLS 요청의 SNI 이름(일반적으로 입력으로 제공된 호스트 이름)을 재정의합니다. 모든 리터럴을 지원하며 특수 값 `request.host`는 `Host` 헤더 값을 사용합니다.
- `@timeout:` 사용자 지정 기간에 대한 요청의 시간 초과를 재정의합니다. 문자열 형식의 기간을 지원합니다. 기간을 지정하지 않으면 기본 시간 초과 플래그 값이 사용됩니다.

다음 예는 요청 내의 주석을 보여줍니다.

```yaml
- |
  @Host: https://projectdiscovery.io:443
  POST / HTTP/1.1
  Pragma: no-cache
  Host: {{Hostname}}
  Cache-Control: no-cache, no-transform
  User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:47.0) Gecko/20100101 Firefox/47.0
```
이것은 예를 들어 여러 요청이 있는 템플릿의 경우에 특히 유용합니다. 초기 요청 이후에 하나의 요청을 특정 호스트에 대해 수행해야 하는 경우(예: API 유효성 검사):

```yaml
http:
  - raw:
      # this request will be sent to {{Hostname}} to get the token
      - |
        GET /getkey HTTP/1.1
        Host: {{Hostname}}
        
      # This request will be sent instead to https://api.target.com:443 to verify the token validity
      - |
        @Host: https://api.target.com:443
        GET /api/key={{token}} HTTP/1.1
        Host: api.target.com:443

    extractors:
      - type: regex
        name: token
        part: body
        regex:
          # random extractor of strings between prefix and suffix
          - 'prefix(.*)suffix'

    matchers:
      - type: word
        part: body
        words:
          - valid token
```

사용자 지정 `timeout` 주석의 예 -

```yaml
- |
  @timeout: 25s
  POST /conf_mail.php HTTP/1.1
  Host: {{Hostname}}
  Content-Type: application/x-www-form-urlencoded
  
  mail_address=%3B{{cmd}}%3B&button=%83%81%81%5B%83%8B%91%97%90M
```
