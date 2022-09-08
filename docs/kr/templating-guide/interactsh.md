[Nuclei v2.3.6](https://github.com/projectdiscovery/nuclei/releases/tag/v2.3.6) 공개 이후 Nuclei는 자동 Request 상관관계가 내장된 OOB 기반 취약성 검사를 위한 [interact.sh](https://github.com/projectdiscovery/interactsh) API의 사용을 지원합니다. request의 아무 곳에 나 `{{interactsh-url}}`을 작성하고 `interact_protocol`에 대한 matcher를 추가하는 것처럼 쉽습니다. Nuclei는 템플릿에 대한 상호 작용의 상관관계와 간편한 OOB 스캔을 허용하여 생성된 request를 처리합니다.



### Interactsh Placeholder
`{{interactsh-url}}` placeholder는 **http** 및 **network** requests에서 지원됩니다.

`{{interactsh-url}}` 자리 표시자가 있는 nuclei request의 예는 아래에 나와 있습니다. 런타임 시 고유한 interact.sh URLs로 대체됩니다.


```yaml
  - raw:
      - |
        GET /plugins/servlet/oauth/users/icon-uri?consumerUri=https://{{interactsh-url}} HTTP/1.1
        Host: {{Hostname}}
```

### Interactsh Matchers
Interactsh 상호 작용은 다음 부분을 사용하여 `word`, `regex` 또는 `dsl` matcher/extractor와 함께 사용할 수 있습니다.

| part                |
|---------------------|
| interactsh_protocol |
| interactsh_request  |
| interactsh_response |


!!! abstract "interactsh_protocol"
    값은 dns, http 또는 smtp 일 수 있습니다. 이것은 본질적으로 심하게 방해가 되지 않기 때문에 dns를 공통 값으로 사용하는 모든 상호 작용 기반 템플릿의 표준 matcher 입니다.

!!! abstract "interactsh_request"
    interact.sh 서버가 수신한 request

!!! abstract "interactsh_response"
    interact.sh 서버가 클라이언트에 보낸 response

Example of Interactsh DNS Interaction matcher:

```yaml
    matchers:
      - type: word
        part: interactsh_protocol # Confirms the DNS Interaction
        words:
          - "dns"
```

Interaction 콘텐츠의 HTTP Interaction matcher + word matcher의 예시

```yaml
matchers-condition: and
matchers:
    - type: word
      part: interactsh_protocol # Confirms the HTTP Interaction
      words:
        - "http"

    - type: regex
      part: interactsh_request # Confirms the retrieval of etc/passwd file
      regex:
        - "root:[x*]:0:0:"
```
