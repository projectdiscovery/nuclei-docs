## Template **Preprocessors** (템플릿 전처리기)

각 템플릿 실행에 대해 생성된 임의의 ID와 같은 것을 달성하기 위해 템플릿이 로드되자마자 실행되는 템플릿의 어느 곳에서나 특정 전처리기를 전역적으로 지정할 수 있습니다.


### randstr

!!! abstract "Info"

  각 nuclei 실행에서 템플릿에 대한 [random ID](https://github.com/rs/xid)를 생성합니다. 이것은 템플릿의 어디에서나 사용할 수 있으며 항상 동일한 값을 포함합니다. `randstr`은 숫자로 접미사로 할 수 있으며, 해당 이름에 대해서도 새로운 임의의 ID가 생성됩니다. 예시. 템플릿 전체에서 동일하게 유지되는 `{{randstr_1}}`

	`randstr`은 matcher 내에서도 지원되며, 입력을 매칭하는데 사용할 수 있습니다.

예시:-

```yaml
requests:
  - method: POST
    path:
      - "{{BaseURL}}/level1/application/"
    headers:
      cmd: echo '{{randstr}}'

    matchers:
      - type: word
        words:
          - '{{randstr}}'
```