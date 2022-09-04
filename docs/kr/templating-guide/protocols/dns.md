### DNS Requests

DNS protocol은 nuclei에서 쉽게 모델링할 수 있습니다. 완전히 사용자 정의 가능한 DNS 요청은 nuclei에 의해 네임서버로 전송될 수 있으며 응답에 대해 일치/추출이 수행될 수 있습니다.

DNS 요청은 템플릿에 대한 요청의 시작을 지정하는 **dns** 블록으로 시작합니다.

```yaml
# 템플릿 요청을 바로 여기에서 시작하세요.
dns:
```

#### Type

요청사항의 첫 번째는 **type**입니다. 요청 유형은 **A**, **NS**, **CNAME**, **SOA**, **PTR**, **MX**, **TXT**, **AAAA**입니다.

```yaml
# type은 DNS 요청에 대한 유형입니다.
type: A
```

#### Name

요청의 다음 부분은 해결할 DNS **name**입니다. 동적 변수를 경로에 배치하여 런타임에 값을 수정할 수 있습니다. 변수는 '{{'로 시작하고 '}}'로 끝나며 대소문자를 구분합니다.

1. **FQDN** - 변수는 런타임 시 대상의 호스트 이름/FQDN으로 대체됩니다.

예제 이름 값:

```yaml
name: {{FQDN}}.com
# 이 값은 실행 시 FQDN으로 대체됩니다.
# FQDN이 https://this.is.an.example이면
# name은 다음으로 대체됩니다: this.is.an.example.com
```

현재 이 도구는 요청당 하나의 이름만 지원합니다.

#### Class

클래스 유형은 **INET**, **CSNET**, **CHAOS**, **HESIOD**, **NONE** 및 **ANY**일 수 있습니다. 일반적으로 **INET**으로 두는 것으로 충분합니다.

```yaml
# 메소드는 dns 요청에 대한 클래스입니다.
class: inet
```

#### Recursion

재귀는 부울 값이며 해석기가 캐시된 결과만 반환해야 하는지 아니면 전체 DNS 루트 트리를 탐색하여 새로운 결과를 검색해야 하는지를 결정합니다. 일반적으로 **true**로 두는 것이 좋습니다.

```yaml
# 재귀는 요청이 재귀인지 여부를 결정하는 부울입니다.
recursion: true
```

#### Retries

Retries는 다른 확인자 간에 포기하기 전에 DNS 쿼리를 다시 시도한 횟수입니다. **3**과 같은 합리적인 값을 권장합니다.

```yaml
# Retries는 dns 해결을 포기하기 전의 재시도 횟수입니다.
retries: 3
```

#### Matchers / Extractor Parts

Matchers/Extractor용 **DNS** 프로토콜에서 지원하는 유효한 `part` 값은 다음과 같습니다.

| Value            | Description                 |
|------------------|-----------------------------|
| request          | DNS Request                 |
| rcode            | DNS Rcode                   |
| question         | DNS Question Message        |
| extra            | DNS Message Extra Field     |
| answer           | DNS Message Answer Field    |
| ns               | DNS Message Authority Field |
| raw / all / body | Raw DNS Message             |


#### **Example DNS Template**

`A` 쿼리를 수행하고 응답에 CNAME 및 A 레코드가 있는지 확인하기 위한 최종 예제 템플릿 파일은 다음과 같습니다.

```yaml
id: dummy-cname-a

info:
  name: Dummy A dns request
  author: mzack9999
  severity: none
  description: Checks if CNAME and A record is returned.

dns:
  - name: "{{FQDN}}"
    type: A
    class: inet
    recursion: true
    retries: 3
    matchers:
      - type: word
        words:
          # The response must contain a CNAME record
          - "IN\tCNAME"
          # and also at least 1 A record
          - "IN\tA"
        condition: and
```

더 완전한 예제는 [here](../../template-examples/dns.md)에서 제공됩니다.
