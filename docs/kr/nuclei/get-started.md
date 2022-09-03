## **Nuclei** 기능


!!! example "Features."

    * HTTP | DNS | TCP | FILE 지원
    * 수정 가능한 템플릿
    * 대규모 스캐닝
    * 대역 외 기반 탐지
    * 본인만의 템플릿을 쉽게 작성가능


## **Nuclei** 설치

=== "Go"


    ```
    go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest
    ```

    !!! info
        Nuclei를 성공적으로 설치하기 위해서는 최신 버전의 **Go** 가 필요합니다.

=== "Brew"

    ```
    brew install nuclei
    ```

    !!! info
        **macOS** (or Linux) 를 지원합니다.

=== "Docker"

    ```
    docker pull projectdiscovery/nuclei:latest
    ```

=== "Github"

    ```
    git clone https://github.com/projectdiscovery/nuclei.git; \
    cd nuclei/v2/cmd/nuclei; \
    go build; \
    mv nuclei /usr/local/bin/; \
    nuclei -version;
    ```

    !!! info
        Nuclei를 성공적으로 설치하기 위해서는 최신 버전의 **Go** 가 필요합니다.
=== "Binary"

    ```
    https://github.com/projectdiscovery/nuclei/releases
    ```

    !!! tip
        - OS에 따라 최신 버전의 실행파일을 다운로드 합니다.
        - 실행하기 위해서 압축을 해제합니다.

## Nuclei **템플릿**

Nuclei는 2.4.0버전부터 템플릿의 자동 업데이트/다운로드를 지원합니다. [**Nuclei-Templates**](https://github.com/projectdiscovery/nuclei-templates) 프로젝트는 지속적으로 업데이트 되는 템플릿 리스트를 제공합니다. 해당 리스트는 커뮤니티에서 기여해주고 있습니다.

Nuclei has built-in support for automatic update/download templates since version [v2.4.0](https://github.com/projectdiscovery/nuclei/releases/tag/v2.4.0). [**Nuclei-Templates**](https://github.com/projectdiscovery/nuclei-templates) project provides a community-contributed list of ready-to-use templates that is constantly updated.

Nuclei는 실행마다 생성되는 새로운 템플릿을 체크하고 사용가능할 때 자동으로 최신 버전을 다운로드 받습니다. 이 기능은 터미널 혹은 설정 파일에서 `-duc`, `-disable-update-check` 플래그를 사용하여 적용시키지 않을 수 있습니다.

Nuclei checks for new template releases upon each execution and automatically downloads the latest version when available. This feature can be disabled using the `-duc`, `-disable-update-check` flags via the CLI or the configuration file.

Nuclei 엔진은 `-update` 플래그를 사용하여 최신 버전으로 업데이트 할 수 있습니다.

The nuclei engine can also be updated to latest version by using the `-update` flag.

!!! tip
    자신만의 독특한 템플릿을 작성하는 것은 항상 다른 이들보다 자신을 앞서게 해줄 것입니다.
    Writing your own unique templates will always keep you one step ahead of others.

## **Nuclei** 사용법

```
nuclei -h
```

이 명령어는 nuclei의 사용법을 보여줍니다. 아래는 지원하고 있는 기능들을 보여줍니다.

This will display help for the tool. Here are all the switches it supports.

```
Nuclei is a fast, template based vulnerability scanner focusing
on extensive configurability, massive extensibility and ease of use.

Usage:
  nuclei [flags]

Flags:
TARGET:
   -u, -target string[]  스캔할 URLs/hosts 대상
   -l, -list string      스캔할 URLs/hosts 대상 목록이 포함된 파일 경로(줄당 하나씩)
   -resume string        resume.cfg를 사용한 스캔 재개(클러스터링이 비활성화됨)

TEMPLATES:
   -nt, -new-templates          nuclei-templates에 가장 최근에 추가된 새 템플릿만 실행
   -as, -automatic-scan         태그 매핑에 대한 wappalyzer 기술 탐지를 사용한 자동 웹 스캔
   -t, -templates string[]      실행할 템플릿 또는 템플릿 디렉터리 목록(쉼표로 구분된 파일)
   -tu, -template-url string[]  실행할 템플릿 URL 목록(쉼표로 구분된 파일)
   -w, -workflows string[]      실행할 워크플로 또는 워크플로 디렉터리 목록(쉼표로 구분된 파일)
   -wu, -workflow-url string[]  실행할 워크플로 URL 목록(쉼표로 구분된 파일)
   -validate                    nuclei로 전달된 템플릿 검증
   -tl                          사용 가능한 모든 템플릿 목록

FILTERING:
   -a, -author string[]              작성자를 기준으로 실행할 템플릿(쉼표로 구분된 파일)
   -tags string[]                    태그를 기준으로 실행할 템플릿(쉼표로 구분된 파일)
   -etags, -exclude-tags string[]    태그를 기준으로 제외할 템플릿(쉼표로 구분된 파일)
   -itags, -include-tags string[]    태그가 기본 또는 구성에 의해 제외된 경우에도 실행됨
   -id, -template-id string[]        템플릿 ID들을 기준으로 실행할 템플릿(쉼표로 구분된 파일)
   -eid, -exclude-id string[]        템플릿 ID들을 기준으로 제외할 템플릿(쉼표로 구분된 파일)
   -it, -include-templates string[]  템플릿이 기본 또는 구성에 의해 제외된 경우에도 실행됨
   -et, -exclude-templates string[]  제외할 템플릿 또는 템플릿 디렉터리(파일로 구분됨, 파일)
   -s, -severity value[]             심각도를 기준으로 실행할 템플릿. 가능한 값: info, low, medium, high, critical, unknown
   -es, -exclude-severity value[]    심각도를 기준으로 제외할 템플릿. 가능한 값: info, low, medium, high, critical, unknown
   -pt, -type value[]                프로토콜 유형을 기준으로 실행할 템플릿. 가능한 값: dns, file, http, headless, network, workflow, ssl, websocket, whois
   -ept, -exclude-type value[]       프로토콜 유형에 따라 제외할 템플릿. 가능한 값: dns, file, http, headless, network, workflow, ssl, websocket, whois

OUTPUT:
   -o, -output string            발견된 문제/취약점를 쓰기 위한 출력 파일
   -sresp, -store-resp           nuclei을 통해 전달된 모든 요청/응답을 출력 디렉터리에 저장
   -srd, -store-resp-dir string  nuclei을 통해 전달된 모든 요청/응답을 사용자 지정 디렉터리에 저장(기본 "output")
   -silent                       결과만 표시
   -nc, -no-color                출력 내용 색상 비활성화 (ANSI escape codes)
   -json                         JSONL(ines) 형식으로 출력
   -irr, -include-rr             JSONL 출력에 요청/응답 쌍 포함(결과만)
   -nm, -no-meta                 cli 출력에서 결과 메타데이터 출력 비활성화
   -nts, -no-timestamp           cli 출력에서 결과 타임스탬프 출력 비활성화
   -rdb, -report-db string       nuclei 보고 데이터베이스(보고서 데이터를 유지하려면 항상 이것을 사용)
   -ms, -matcher-status          매치 실패 상태 표시
   -me, -markdown-export string  마크다운 형식으로 결과를 내보낼 디렉터리
   -se, -sarif-export string     결과를 SARIF 형식으로 내보내는 파일

CONFIGURATIONS:
   -config string              nuclei 환경 설정 파일 경로
   -fr, -follow-redirects      http 템플릿에 following redirects 활성화
   -mr, -max-redirects int     http 템플릿에 따라야 할 최대 리디렉션 수(기본값 10)
   -dr, -disable-redirects     http 템플릿에 대한 리디렉션 비활성화
   -rc, -report-config string  nuclei 보고 모듈 환경 설정 파일
   -H, -header string[]        헤더:값 형식의 모든 http 요청에 포함할 사용자 정의 헤더/쿠키(cli, file)
   -V, -var value              키=값 형식의 사용자 정의 변수
   -r, -resolvers string       nuclei에 대한 리졸버 목록이 포함된 파일
   -sr, -system-resolvers      에러 fallback으로 시스템 DNS 리졸빙 사용
   -passive                    수동 HTTP 응답 처리 모드 활성화
   -ev, -env-vars              템플릿에서 환경 변수를 사용할 수 있도록 설정
   -cc, -client-cert string    스캔된 호스트에 인증하는 데 사용되는 클라이언트 인증서 파일(PEM-encoded)
   -ck, -client-key string     스캔된 호스트에 인증하는 데 사용되는 클라이언트 key 파일(PEM-encoded)
   -ca, -client-ca string      스캔된 호스트에 인증하는 데 사용되는 클라이언트 인증 기관 파일(PEM-encoded)
   -sml, -show-match-line      파일 템플릿에 대해 일치하는 줄 표시, extractors에서만 작동
   -ztls                       tls13의 표준으로 autofallback과 함께 ztls 라이브러리 사용
   -sni string                 사용할 tls sni 호스트 이름(기본: 입력한 도메인 이름)

INTERACTSH:
   -iserver, -interactsh-server string  자체 호스팅된 인스턴스에 대한 interactsh 서버 URL (기본: oast.pro,oast.live,oast.site,oast.online,oast.fun,oast.me)
   -itoken, -interactsh-token string    자체 호스팅된 interactsh 서버에 대한 인증 토큰
   -interactions-cache-size int         상호 작용 캐시에 유지할 요청 수 (기본 5000)
   -interactions-eviction int           캐시에서 요청을 제거하기 전에 대기할 시간(초) (기본 60)
   -interactions-poll-duration int      각 상호 작용 폴링 요청 전에 대기할 시간(초) (기본 5)
   -interactions-cooldown-period int    종료 전 상호작용 폴링을 위한 추가 시간 (기본 5)
   -ni, -no-interactsh                  OAST 테스트를 위해 interactsh 서버 비활성화, OAST 기반 템플릿 제외

RATE-LIMIT:
   -rl, -rate-limit int            초당 보낼 최대 요청 수 (기본 150)
   -rlm, -rate-limit-minute int    분당 보낼 최대 요청 수
   -bs, -bulk-size int             템플릿당 병렬로 분석할 최대 호스트 수 (기본 25)
   -c, -concurrency int            병렬로 실행할 최대 템플릿 수 (기본 25)
   -hbs, -headless-bulk-size int   템플릿당 병렬로 분석할 최대 headless 호스트 수 (기본 10)
   -hc, -headless-concurrency int  병렬로 실행할 최대 headless 템플릿 수 (기본 10)

OPTIMIZATIONS:
   -timeout int                타임아웃 전 대기 시간(초) (기본 5)
   -retries int                실패한 요청을 재시도하는 횟수 (기본 1)
   -ldp, -leave-default-ports  leave default HTTP/HTTPS ports (eg. host:80,host:443
   -mhe, -max-host-error int   스캔을 건너뛰기 전에 호스트에 대한 최대 오류 수 (기본 30)
   -project                    프로젝트 폴더를 사용하여 동일한 요청을 여러 번 보내지 않음
   -project-path string        특정 프로젝트 경로 설정
   -spm, -stop-at-first-path   첫 번째 일치 후 HTTP 요청 처리 중지 (template/workflow 로직이 중단될 수 있음)
   -stream                     stream 모드 - 입력을 정렬하지 않고 elaborating 시작

HEADLESS:
   -headless            헤드리스 브라우저 지원이 필요한 템플릿 활성화(root user on linux will disable sandbox)
   -page-timeout int    헤드리스 모드에서 각 페이지를 기다리는 시간(초)(기본 20)
   -sb, -show-browser   헤드리스 모드로 템플릿을 실행할 때 화면에 브라우저 표시
   -sc, -system-chrome  설치된 nuclei 대신 로컬에 설치된 chrome 브라우저 사용

DEBUG:
   -debug                    모든 요청 및 응답 표시
   -dreq, -debug-req         보낸 모든 요청 표시
   -dresp, -debug-resp       받은 모든 응답 표시
   -p, -proxy string[]       사용할 http/socks5 프록시 목록(쉼표로 구분하거나 파일 입력)
   -pi, -proxy-internal      모든 내부 요청을 프록시
   -tlog, -trace-log string  보낸 요청 추적 로그를 기록할 파일
   -elog, -error-log string  보낸 요청 오류 로그를 기록할 파일
   -version                  nuclei 버전 출력
   -hm, -hang-monitor        nuclei hang monitoring 활성화
   -v, -verbose              상세 출력 표시
   -vv                       스캔을 위해 로드된 디스플레이 템플릿 표시
   -ep, -enable-pprof        pprof debugging server 활성화
   -tv, -templates-version   설치된 nuclei-templates 버전 출력

UPDATE:
   -update                        최신 릴리스 버전으로 nuclei 엔진 업데이트
   -ut, -update-templates         최신 릴리스 버전으로 nuclei-templates 엔진 업데이트
   -ud, -update-directory string  nuclei-templates를 설치할 기본 디렉터리를 덮어씀
   -duc, -disable-update-check    자동 nuclei/templates 업데이트 확인 비활성화

STATISTICS:
   -stats                    실행 중인 스캔에 대한 통계 표시
   -sj, -stats-json          JSONL(ines) 형식으로 출력 파일에 통계 데이터 쓰기
   -si, -stats-interval int  통계 업데이트를 표시할 때까지 대기하는 시간(초) (기본 5)
   -m, -metrics              expose nuclei metrics on a port
   -mp, -metrics-port int    port to expose nuclei metrics on (기본 9092)
```

## **Nuclei** 실행

Nuclei 템플릿은 크게 두 가지 방법으로 실행 될 수 있습니다.

1) **Templates** (`-t/templates`)

기본적으로, 모든 템플릿 (nuclei-ignore 리스트를 제외한) 들은 기본 템플릿 설치 경로에서 실행됩니다.

```sh
nuclei -u https://example.com
```

별도의 템플릿 폴더 혹은 다수의 템플릿 폴더는 다음과 같이 실행될 수 있습니다.

```sh
nuclei -u https://example.com -t cves/ -t exposures/
```

URL 리스트에 대해서도 템플릿들은 실행될 수 있습니다.

```sh
nuclei -list http_urls.txt
```

2) **Workflows** (`-w/workflows`)

```sh
nuclei -u https://example.com -w workflows/
```

URL 리스트에 대해서도 Workflows들은 실행될 수 있습니다.

```sh
nuclei -list http_urls.txt -w workflows/wordpress-workflow.yaml
```

### Nuclei **필터**

Nuclei 엔진은 템플릿 실행을 커스텀하기 위해서 3개의 기본 필터를 제공합니다.
Nuclei engine supports three basic filters to customize template execution.


1. Tags (`-tags`)

    템플렛에서 사용가능한 태그 속성에 기반한 필터
    Filter based on tags field available in the template.

2. Severity (`-severity`)

    템플릿에서 사용가능한 심각도 속성에 기반한 필터
    Filter based on severity field available in the template.

3. Author (`-author`)

    템플릿에서 사용가능한 작성자 속성에 기반한 필터
    Filter based on author field available in the template.

기본적으로, 필터들은 템플릿 설치 경로에 적용되고, 수동 템플릿 경로 입력을 사용하여 커스텀할 수 있습니다.
As default, Filters are applied on installed path of templates and can be customized with manual template path input.

예를 들어, 아래의 명령은 `~/nuclei-templates/` 폴더에 설치된 템플릿들 중에서 `cve` 태그를 가진 템플릿들을 실행시킵니다.
For example, below command will run all the templates installed at `~/nuclei-templates/` directory and has `cve` tags in it.

```sh
nuclei -u https://example.com -tags cve
```

그리고 다음은 `~/nuclei-templates/exposures/` 폴더의 `config` 태그를 가진 모든 사용가능한 템플릿들을 실행하는 예입니다.
And this example will run all the templates available under `~/nuclei-templates/exposures/` directory and has `config` tag in it.

```sh
nuclei -u https://example.com -tags config -t exposures/
```

다수의 필터를 AND 조건으로 함께 사용이 가능합니다. 다음은 `cve` 태그를 가졌고, `critical` 또는 `high` 의 심각도를 가졌고, 작성자가 `geeknik`인 모든 템플릿을 실행하는 예입니다.
Multiple filters works together with AND condition, below example runs all template with `cve` tags AND has `critical` OR `high` severity AND `geeknik` as author of template.

```sh
nuclei -u https://example.com -tags cve -severity critical,high -author geeknik
```

다수의 필터는 템플릿 조건 플래그 (`tc`)를 이용해 조합하여 사용할 수 있습니다. 템플릿 조건 플래그으로 다음과 같은 복잡한 표현식을 사용할 수 있습니다. 
Multiple filters can also be combined using the template condition flag (`-tc`) that allows complex expressions like the following ones:
```sh
nuclei -tc "contains(id,'xss') || contains(tags,'xss')"
nuclei -tc "contains(tags,'cve') && contains(tags,'ssrf')"
nuclei -tc "contains(name, 'Local File Inclusion')"
```

필터링을 지원하는 속성 목록입니다.
The supported fields are:

- `id` string
- `name` string
- `description` string
- `tags` slice of strings
- `authors` slice of strings
- `severity` string


또한, 템플릿의 메타데이터 영역의 키-값으로 구성된 모든 쌍이 가능합니다. 모든 속성들은 논리 연산자(`||` 와 `&&`) 를 통해 조합될 수 있고, DSL helper 함수들과 함께 사용될 수 있습니다.
Also, every key-value pair from the template metadata section is accessible. All fields can be combined with logical operators (`||` and `&&`) and used with DSL helper functions.

모든 필터들은 workflows 에서도 마찬가지로 지원됩니다.
Similarly, all filters are supported in workflows as well.


```sh
nuclei -w workflows/wordpress-workflow.yaml -severity critical,high -list http_urls.txt
```

!!! info "Workflows"
    Workflows에서는 Workflows를 통해 실행되는 템플릿, 서브템플릿에 Nuclei 필터가 적용됩니다.
    In Workflows, Nuclei filters are applied on templates or sub-templates running via workflows, not on the workflows itself.

### 속도 **제한**

Nuclei는 다양한 요인으로 다중 속도 제한 제어를 합니다. 요인으로는 병렬로 실행할 템플릿 수, 각 템플릿에 대해 병렬로 검색할 호스트 수, Nuclei를 이용하여 생성하거나 제한하고자 한 초당 총 요청 수 등이 있습니다.아래는 각 플래그에 대한 설명입니다.

Nuclei have multiple rate limit controls for multiple factors, including a number of templates to execute in parallel, a number of hosts to be scanned in parallel for each template, and the global number of request / per second you wanted to make/limit using nuclei, here is an example of each flag with description.

| Flag       | Description                                                          |
|------------|----------------------------------------------------------------------|
| rate-limit | Control the total number of request to send per seconds 초당 총 요청의 수를 제어합니다.             |
| bulk-size  | Control the number of hosts to process in parallel for each template 각 템플릿마다 동시에 진행할 호스트의 수를 제어합니다. |
| c          | Control the number of templates to process in parallel 동시에 진행할 템플릿의 수를 제어합니다.              |

    
Nuclei의 속도 제한과 정확도를 이 플래그를 이용하여 자유롭게 조정하세요!
Feel free to play with these flags to tune your nuclei scan speed and accuracy.

!!! tip
    `rate-limit` 플래그는 다른 두 플래그보다 우선합니다. 초당 요청의 수는 `c` 와 `bulk-size` 플래그 값에 상관없이 `rate-limit` 값을 초과할 수 없습니다.
    `rate-limit` flag takes precedence over the other two flags, the number of requests/seconds can't go beyond the value defined for `rate-limit` flag regardless the value of `c` and `bulk-size` flag.

### Traffic **Tagging**

많은 버그 바운티 플랫폼과 프로그램들은 식별을 위해 여러분들이 생성한 HTTP 트래픽을 요구합니다. 이는 `$HOME/.config/nuclei/config.yaml` 의 설정 파일을 이용하거나 `-H / header` CLI 플래그를 이용하면 됩니다. 

Many BugBounty platform/programs requires you to identify the HTTP traffic you make, this can be achieved by setting custom header using config file at `$HOME/.config/nuclei/config.yaml` or CLI flag `-H / header`


!!! info "설정 파일을 이용한 사용자 정의 헤더 설정"

    ```yaml
    # Headers to include with each request.
    header:
      - 'X-BugBounty-Hacker: h1/geekboy'
      - 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) / nuclei'
    ```

!!! info "CLI 플래그를 이용한 사용자 정의 헤더 설정"


    ```yaml
    nuclei -header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) / nuclei' -list urls.txt -tags cves
    ```


### 템플릿 **제외**

Nuclei는 실행에 포함할 템플릿을 제외/방지 하는 다양한 방법을 제공합니다. 스캔으로 인한 예측되지 않은 퍼즈를 피하고, 대량 스캔에서 일부 제외를 위해 실행되지 않아야 하는 태그들과 템플릿들을 Nuclei에서는 기본적으로 제외합니다. 기본 제외 목록은 아래의 링크를 통해 확인 가능합니다. 이 파일은 설정 파일 혹은 플래그들을 통해 쉽게 고칠 수 있습니다.

Nuclei supports a variety of methods for excluding / blocking templates from execution. By default, **nuclei** excludes the tags/templates listed below from execution to avoid unexpected fuzz based scans and some that are not supposed to run for mass scan, and these can be easily overwritten with nuclei configuration file / flags.

- Default [Template ignore](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) list.


!!! danger ""

    **nuclei-ignore** 파일은 사용자가 업데이트/편집/제거할 수 없습니다. 이를 덮어쓰기 위해서는 [nuclei configuration](https://nuclei.projectdiscovery.io/nuclei/get-started/#nuclei-config) 파일을 이용합니다.
    **nuclei-ignore** file is not supposed to be updated / edited / removed by user, to overwrite default ignore list, utilize [nuclei configuration](https://nuclei.projectdiscovery.io/nuclei/get-started/#nuclei-config) file. 

Nuclei는 두 가지 방법을 통해 스캔에서 제외할 템플릿을 조정할 수 있습니다.
Nuclei engine supports two ways to manually exclude templates from scan,

1. Exclude Templates (`-exclude-templates/exclude`) 템플릿 제외하기 (`-exclude-templates/exclude`)

    **exclude-templates** 플래그는 하나 혹은 다수의 템플릿 그리고 디렉토리를 제외할 수 있습니다. 복수의 `-exclude-templates` 플래그를 통해 여러 값을 제공합니다.
    **exclude-templates** flag is used to exclude single or multiple templates and directory, multiple `-exclude-templates` flag can be used to provide multiple values.


2. Exclude Tags (`-exclude-tags/etags`) 태그 제외하기 (`-exclude-tags/etags`)

    **exclude-tags** 플래그는 정의된 태그들을 기반으로 템플릿을 제외합니다. 하나 혹은 다수의 태그를 사용해 제외할 수 있습니다.
    **exclude-tags** flag is used to exclude templates based in defined tags, single or multiple can be used to exclude templates.


!!! info "하나의 템플릿을 제외하는 예"

    ```
    nuclei -list urls.txt -t cves/ -exclude-templates cves/2020/CVE-2020-XXXX.yaml
    ```

!!! info "다수의 템플릿을 제외하는 예"

    ```
    nuclei -list urls.txt -exclude-templates exposed-panels/ -exclude-templates technologies/
    ```

!!! info "하나의 태그로 템플릿을 제외하는 예"

    ```
    nuclei -l urls.txt -t cves/ -etags xss
    ```

!!! info "다수의 태그를 이용해 템플릿을 제외하는 예"

    ```
    nuclei -l urls.txt -t cves/ -etags sqli,rce
    ```

[nuclei-ignore](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore)를 쉽게 설정하기 위해 Nuclei는 **include-tags** / **include-templates** 플래그를 지원합니다.
To easily overwrite [nuclei-ignore](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore), Nuclei engine supports **include-tags** / **include-templates** flag.

!!! info "제외된 태그들을 실행하는 예"

    ```
    nuclei -l urls.txt -include-tags iot,misc,fuzz
    ```

모든 검색에 대해 이러한 태그를 포함하도록 nuclei 설정 파일을 업데이트할 수 있습니다.
We can update the nuclei configuration file to include these tags for all scans.

## Nuclei **설정**

!!! abstract ""

    [v.2.3.2](https://blog.projectdiscovery.io/nuclei-v2-3-0-release/) 버전의 nuclei부터 깔끔한 CLI 환경과 포맷팅된 길거나 짧은 형식의 플래그들을 사용하기 위해서 [goflags](https://github.com/projectdiscovery/goflags)를 사용합니다.
    Since release of [v.2.3.2](https://blog.projectdiscovery.io/nuclei-v2-3-0-release/) nuclei uses [goflags](https://github.com/projectdiscovery/goflags) for clean CLI experience and long/short formatted flags.

    자동 생성 설정 파일을 생성하는 [goflags](https://github.com/projectdiscovery/goflags)는 사용 가능한 모든 CLI플래그를 설정 파일로 커버합니다. 기본적으로 로드되는 반복적인 CLI 플래그를 피하기 위해서 모든 CLI 플래그들을 설정 파일에 정의할 수 있습니다.
    [goflags](https://github.com/projectdiscovery/goflags) comes with auto-generated config file support that coverts all available CLI flags into config file, basically you can define all CLI flags into config file to avoid repetitive CLI flags that loads as default for every scan of nuclei.

    Nuclei 설정 파일의 기본 경로는 `$HOME/.config/nuclei/config.yaml` 입니다. 기본적으로 실행할 플래그를 설정하거나 주석 해제합니다.
    Default path of nuclei config file is `$HOME/.config/nuclei/config.yaml`, uncomment and configure the flags you wish to run as default.

아래는 설정 파일의 예시입니다.
Here is an example config file:-

```yaml
# Headers to include with all HTTP request
header:
  - 'X-BugBounty-Hacker: h1/geekboy'

# Directory based template execution
templates:
  - cves/
  - vulnerabilities/
  - misconfiguration/

# Tags based template execution
tags: exposures,cve

# Template Filters
tags: exposures,cve
author: geeknik,pikpikcu,dhiyaneshdk
severity: critical,high,medium

# Template Allowlist
include-tags: dos,fuzz # Tag based inclusion (allows overwriting nuclei-ignore list)
include-templates: # Template based inclusion (allows overwriting nuclei-ignore list)
  - vulnerabilities/xxx
  - misconfiguration/xxxx

# Template Denylist
exclude-tags: info # Tag based exclusion
exclude-templates: # Template based exclusion
  - vulnerabilities/xxx
  - misconfiguration/xxxx

# Rate Limit configuration
rate-limit: 500
bulk-size: 50
concurrency: 50
```

일단 설정이 되면, 설정 파일을 기본적으로 사용합니다. `-config` 플래그를 통해 사용자 정의 설정 파일을 추가적으로 제공할 수 있습니다.
Once configured, **config file be used as default**, additionally custom config file can be also provided using `-config` flag.

!!! info "사용자 정의 파일로 nuclei 실행하기"

    ```
    nuclei -config project.yaml -list urls.txt
    ```

## Nuclei **신고**

Nuclei는 GitHub, GitLab, Jira integration 을 지원하는 [v2.3.0](https://nuclei.projectdiscovery.io/releases/nuclei-changelog/#nuclei-v230-10-march-2021) 버전과 함께 신고 모듈을 제공합니다. 이는 발견된 결과에 따라 지원되는 플랫폼에서 자동으로 신고 내역을 생성할 수 있게 해줍니다.
Nuclei comes with reporting module support with the release of [v2.3.0](https://nuclei.projectdiscovery.io/releases/nuclei-changelog/#nuclei-v230-10-march-2021) supporting GitHub, GitLab, and Jira integration, this allows nuclei engine to create automatic tickets on the supported platform based on found results.

<table>
  <tr>
    <th>Platform</th>
    <td>GitHub</td>
    <td>GitLab</td>
    <td>Jira</td>
    <td>Markdown</td>
    <td>SARIF</td>
    <td>Elasticsearch</td>

  </tr>
  <tr>
    <th>Support</th>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
  </tr>
</table>


`-rc, -report-config` 플래그는 통합을 위해 플랫폼의 세부 정보를 읽기 위해 설정 파일을 제공할 수 있습니다. 지원되는 모든 플랫폼에 대한 [예시 파일](https://github.com/projectdiscovery/nuclei/blob/master/v2/cmd/nuclei/issue-tracker-config.yaml) 입니다.
`-rc, -report-config` flag can be used to provide a config file to read configuration details of the platform to integrate. Here is an [example config file](https://github.com/projectdiscovery/nuclei/blob/master/v2/cmd/nuclei/issue-tracker-config.yaml) for all supported platforms.


예를 들어, Github 에서 신고 내역을 생성하기 위해 다음의 내용을 담은 설정 파일을 생성하고 적절한 값들을 바꾸어 봅니다.
For example, to create tickets on GitHub, create a config file with the following content and replace the appropriate values:-

```yaml
# GitHub contains configuration options for GitHub issue tracker

github: 
  username: "$user"
  owner: "$user"
  token: "$token"
  project-name: "testing-project"
  issue-label: "Nuclei"
```

Elasticsearch의 결과들을 저장하기 위해 다음의 내용을 담은 설정 파일을 생성하고 적절한 값들을 바꾸어 봅니다.
To store results in Elasticsearch, create a config file with the following content and replace the appropriate values:-

```yaml
# elasticsearch contains configuration options for elasticsearch exporter
elasticsearch:
  # IP for elasticsearch instance
  ip: 127.0.0.1
  # Port is the port of elasticsearch instance
  port: 9200
  # IndexName is the name of the elasticsearch index
  index-name: nuclei
```

**신고 모듈과 함께 Nuclei 실행하기:-**

```bash
nuclei -l urls.txt -t cves/ -rc issue-tracker.yaml
```

비슷하게 다른 플랫폼들도 설정할 수 있습니다. 신고 모듈은 중복되는 신고 내역을 생성하지 않기 위해서 기본적인 필터링과 중복 체크를 지원합니다.
Similarly, other platforms can be configured. Reporting module also supports basic filtering and duplicate checks to avoid duplicate ticket creation.

```yaml
allow-list:
  severity: high,critical
```

위는 심각도가 **high** 이고 **critical**로 식별되는 이슈들에 대해서만 신고 내역을 생성합니다. 비슷하게도 `deny-list` 설정은 특정 심각도의 이슈들을 제외할 수 있습니다.
This will ensure to only creating tickets for issues identified with **high** and **critical** severity; similarly, `deny-list` can be used to exclude issues with a specific severity.

같은 에셋에 대해 주기적인 스캔을 실행한다면, 신고 모듈이 비교하고 **유니크한 이슈들에 대한 신고 내역을 생성**하는 주어진 디렉토리에서의 유효한 발견에 대해서 로컬 복사본을 생성하는 `-rdb, -report-db` 플래그를 고려해볼 수 있습니다.
If you are running periodic scans on the same assets, you might want to consider `-rdb, -report-db` flag that creates a local copy of the valid findings in the given directory utilized by reporting module to compare and **create tickets for unique issues only**.

```bash
nuclei -l urls.txt -t cves/ -rc issue-tracker.yaml -rdb prod
```

**<ins>Markdown 추출</ins>**

Nuclei는 `-me, -markdown-export` 플래그를 통해 유효한 발견들을 마크다운 형식으로 추출하는 것을 지원합니다. 이 플래그는 마크다운 형식의 결과를 저장하기 위해 입력으로 디렉토리를 사용합니다.
Nuclei supports markdown export of valid findings with `-me, -markdown-export` flag, this flag takes directory as input to store markdown formatted reports.

마크다운 형식의 결과에서 요청/응답을 포함하는 것은 선택 사항입니다. 혹은 `-irr, -include-rr` 플래그가 `-me`와 함께 사용될 때 포함됩니다.
Including request/response in the markdown report is optional, and included when `-irr, -include-rr` flag is used along with `-me`.

```bash
nuclei -l urls.txt -t cves/ -irr -markdown-export reports
```

**<ins>SARIF 추출</ins>**

Nuclei는 `-se, -sarif-export` 플래그를 통해 유효한 발견들을 SARIF 형식으로 추출하는 것을 지원합니다. 이 플래그는 SARIF 형식의 결과를 저장하기 위해 입력으로 파일을 사용합니다.
Nuclei supports SARIF export of valid findings with `-se, -sarif-export` flag. This flag takes a file as input to store SARIF formatted report.

요청-응답 정보는 마크다운 문법 형식이고, SARIF의 응답 영역에 저장됩니다.
The request-response pairs are formatted using markdown syntax and are stored in the SARIF response section.

```bash
nuclei -l urls.txt -t cves/ -sarif-export report.sarif
```

## Scan **Metrics**

Nuclei는 **localhost:9092/metrics** 에 접속 가능하며 `-metrics` 플래그를 사용하면 로컬 포트 `9092`에서 실행 중인 scan metrics를 볼 수 있습니다. 기본 포트는 `-metrics-port` 플래그를 사용하여 설정 할 수 있습니다.
Nuclei expose running scan metrics on a local port `9092` when `-metrics` flag is used and can be accessed at **localhost:9092/metrics** , default port to expose scan information is configurable using `-metrics-port` flag.


`nuclei -t cves/ -l urls.txt -metrics` 로 nuclei가 실행된 동안 `metrics`로 응답을 요청한 예시입니다.
Here is an example to query `metrics` while running nuclei as following `nuclei -t cves/ -l urls.txt -metrics`

```bash

curl -s localhost:9092/metrics | jq .
```

```json
{
  "duration": "0:00:03",
  "errors": "2",
  "hosts": "1",
  "matched": "0",
  "percent": "99",
  "requests": "350",
  "rps": "132",
  "startedAt": "2021-03-27T18:02:18.886745+05:30",
  "templates": "256",
  "total": "352"
}
```

## Passive Scan

Nuclei는 파일 지원을 통해 HTTP 기반 템플릿에 대한 수동 모드 스캔을 지원합니다. 이 기능을 통해 다른 도구에서 수집된 로컬에 저장된 HTTP 응답 데이터에 대해 HTTP 기반 템플릿을 실행할 수 있습니다.
Nuclei engine supports passive mode scanning for HTTP based template utilizing file support, with this support we can run HTTP based templates against locally stored HTTP response data collected from any other tool.

```sh
nuclei -passive -target http_data
```

!!! info ""

    수동모드는 `{{BasedURL}}` 혹은 `{{BasedURL/}}`을 기본 경로로 가지는 템플릿들에 대해 제한됩니다.
    Passive mode support is limited for templates having `{{BasedURL}}` or `{{BasedURL/}}` as base path.
    
## **기여**

[Nuclei templates](https://github.com/projectdiscovery/nuclei-templates) 는 nuclei 프로젝트의 기반입니다. 이 프로젝트를 위해 새로운 탬플릿을 작성하거나 제출해주시는 분께 감사드립니다. 그리고 이러한 기여는 저희가 프로젝트를 계속 지속할 큰 동기가 됩니다. 

## License

Nuclei is distributed under MIT [License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).
