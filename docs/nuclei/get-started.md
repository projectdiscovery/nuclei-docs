## **Nuclei** Features


!!! example "Features."

    * HTTP | DNS | TCP | FILE support
    * Fully configurable templates.
    * Large scale scanning.
    * Out of band based detection.
    * Easily write your own templates.


## **Nuclei** Installation

=== "Go"


    ```
    go install -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei@latest
    ```

    !!! info
        Nuclei require latest **GO** version to install successfully.

=== "Brew"

    ```
    brew install nuclei
    ```

    !!! info
        Supported in **macOS** (or Linux)

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
        Nuclei require the latest **GO** version to install successfully.
=== "Binary"

    ```
    https://github.com/projectdiscovery/nuclei/releases
    ```

    !!! tip
        - Download the latest binary for your OS.
        - Unzip the ready to run binary.

=== "Helm"

    ```
    git clone https://github.com/projectdiscovery/nuclei.git
    cd nuclei/helm
    helm upgrade --install nuclei . -f values.yaml 
    ```

    !!! tip
        This Helm chart creates two primary resources (intended to be configured via `values.yaml`):

	    - A Kubernetes CronJob to run Nuclei on a defined schedule

	    - An [Interactsh](https://github.com/projectdiscovery/interactsh) service for Nuclei to use



## Nuclei **Templates**

Nuclei has built-in support for automatic update/download templates since version [v2.4.0](https://github.com/projectdiscovery/nuclei/releases/tag/v2.4.0). [**Nuclei-Templates**](https://github.com/projectdiscovery/nuclei-templates) project provides a community-contributed list of ready-to-use templates that is constantly updated.

Nuclei also support for update/download custom template repositories. You can pass the file/list of github repositories by using `-gtr`/`-github-template-repo` flag. This will download the repositories under `nuclei-templates/github` directory. To update the repo you can pass the `-update-templates` with `-gtr` flag.

Nuclei checks for new community template releases upon each execution and automatically downloads the latest version when available. This feature can be disabled using the `-duc`, `-disable-update-check` flags via the CLI or the configuration file.


The nuclei engine can also be updated to latest version by using the `-update` flag.

!!! tip
    Writing your own unique templates will always keep you one step ahead of others.

## **Nuclei** Usage

```
nuclei -h
```

This will display help for the tool. Here are all the switches it supports.

```
Nuclei is a fast, template based vulnerability scanner focusing
on extensive configurability, massive extensibility and ease of use.

Usage:
  nuclei [flags]

Flags:
TARGET:
   -u, -target string[]       target URLs/hosts to scan
   -l, -list string           path to file containing a list of target URLs/hosts to scan (one per line)
   -resume string             resume scan using resume.cfg (clustering will be disabled)
   -sa, -scan-all-ips         scan all the IP's associated with dns record
   -iv, -ip-version string[]  IP version to scan of hostname (4,6) - (default 4)

TEMPLATES:
   -nt, -new-templates                    run only new templates added in latest nuclei-templates release
   -ntv, -new-templates-version string[]  run new templates added in specific version
   -as, -automatic-scan                   automatic web scan using wappalyzer technology detection to tags mapping
   -t, -templates string[]                list of template or template directory to run (comma-separated, file)
   -tu, -template-url string[]            list of template urls to run (comma-separated, file)
   -w, -workflows string[]                list of workflow or workflow directory to run (comma-separated, file)
   -wu, -workflow-url string[]            list of workflow urls to run (comma-separated, file)
   -validate                              validate the passed templates to nuclei
   -nss, -no-strict-syntax                disable strict syntax check on templates
   -td, -template-display                 displays the templates content
   -tl                                    list all available templates

FILTERING:
   -a, -author string[]               templates to run based on authors (comma-separated, file)
   -tags string[]                     templates to run based on tags (comma-separated, file)
   -etags, -exclude-tags string[]     templates to exclude based on tags (comma-separated, file)
   -itags, -include-tags string[]     tags to be executed even if they are excluded either by default or configuration
   -id, -template-id string[]         templates to run based on template ids (comma-separated, file)
   -eid, -exclude-id string[]         templates to exclude based on template ids (comma-separated, file)
   -it, -include-templates string[]   templates to be executed even if they are excluded either by default or configuration
   -et, -exclude-templates string[]   template or template directory to exclude (comma-separated, file)
   -em, -exclude-matchers string[]    template matchers to exclude in result
   -s, -severity value[]              templates to run based on severity. Possible values: info, low, medium, high, critical, unknown
   -es, -exclude-severity value[]     templates to exclude based on severity. Possible values: info, low, medium, high, critical, unknown
   -pt, -type value[]                 templates to run based on protocol type. Possible values: dns, file, http, headless, network, workflow, ssl, websocket, whois
   -ept, -exclude-type value[]        templates to exclude based on protocol type. Possible values: dns, file, http, headless, network, workflow, ssl, websocket, whois
   -tc, -template-condition string[]  templates to run based on expression condition

OUTPUT:
   -o, -output string            output file to write found issues/vulnerabilities
   -sresp, -store-resp           store all request/response passed through nuclei to output directory
   -srd, -store-resp-dir string  store all request/response passed through nuclei to custom directory (default "output")
   -silent                       display findings only
   -nc, -no-color                disable output content coloring (ANSI escape codes)
   -json                         write output in JSONL(ines) format
   -irr, -include-rr             include request/response pairs in the JSONL output (for findings only)
   -nm, -no-meta                 disable printing result metadata in cli output
   -ts, -timestamp               enables printing timestamp in cli output
   -rdb, -report-db string       nuclei reporting database (always use this to persist report data)
   -ms, -matcher-status          display match failure status
   -me, -markdown-export string  directory to export results in markdown format
   -se, -sarif-export string     file to export results in SARIF format

CONFIGURATIONS:
   -config string                 path to the nuclei configuration file
   -fr, -follow-redirects         enable following redirects for http templates
   -fhr, -follow-host-redirects   follow redirects on the same host
   -mr, -max-redirects int        max number of redirects to follow for http templates (default 10)
   -dr, -disable-redirects        disable redirects for http templates
   -rc, -report-config string     nuclei reporting module configuration file
   -H, -header string[]           custom header/cookie to include in all http request in header:value format (cli, file)
   -V, -var value                 custom vars in key=value format
   -r, -resolvers string          file containing resolver list for nuclei
   -sr, -system-resolvers         use system DNS resolving as error fallback
   -dc, -disable-clustering       disable clustering of requests
   -passive                       enable passive HTTP response processing mode
   -fh2, -force-http2             force http2 connection on requests
   -ev, -env-vars                 enable environment variables to be used in template
   -cc, -client-cert string       client certificate file (PEM-encoded) used for authenticating against scanned hosts
   -ck, -client-key string        client key file (PEM-encoded) used for authenticating against scanned hosts
   -ca, -client-ca string         client certificate authority file (PEM-encoded) used for authenticating against scanned hosts
   -sml, -show-match-line         show match lines for file templates, works with extractors only
   -ztls                          use ztls library with autofallback to standard one for tls13
   -sni string                    tls sni hostname to use (default: input domain name)
   -sandbox                       sandbox nuclei for safe templates execution
   -i, -interface string          network interface to use for network scan
   -at, -attack-type string       type of payload combinations to perform (batteringram,pitchfork,clusterbomb)
   -sip, -source-ip string        source ip address to use for network scan
   -config-directory string       override the default config path ($home/.config)
   -rsr, -response-size-read int  max response size to read in bytes (default 10485760)
   -rss, -response-size-save int  max response size to read in bytes (default 1048576)

INTERACTSH:
   -iserver, -interactsh-server string  interactsh server url for self-hosted instance (default: oast.pro,oast.live,oast.site,oast.online,oast.fun,oast.me)
   -itoken, -interactsh-token string    authentication token for self-hosted interactsh server
   -interactions-cache-size int         number of requests to keep in the interactions cache (default 5000)
   -interactions-eviction int           number of seconds to wait before evicting requests from cache (default 60)
   -interactions-poll-duration int      number of seconds to wait before each interaction poll request (default 5)
   -interactions-cooldown-period int    extra time for interaction polling before exiting (default 5)
   -ni, -no-interactsh                  disable interactsh server for OAST testing, exclude OAST based templates

UNCOVER:
   -uc, -uncover                  enable uncover engine
   -uq, -uncover-query string[]   uncover search query
   -ue, -uncover-engine string[]  uncover search engine (shodan,shodan-idb,fofa,censys,quake,hunter,zoomeye,netlas) (default shodan)
   -uf, -uncover-field string     uncover fields to return (ip,port,host) (default "ip:port")
   -ul, -uncover-limit int        uncover results to return (default 100)
   -ucd, -uncover-delay int       delay between uncover query requests in seconds (0 to disable) (default 1)

RATE-LIMIT:
   -rl, -rate-limit int               maximum number of requests to send per second (default 150)
   -rlm, -rate-limit-minute int       maximum number of requests to send per minute
   -bs, -bulk-size int                maximum number of hosts to be analyzed in parallel per template (default 25)
   -c, -concurrency int               maximum number of templates to be executed in parallel (default 25)
   -hbs, -headless-bulk-size int      maximum number of headless hosts to be analyzed in parallel per template (default 10)
   -headc, -headless-concurrency int  maximum number of headless templates to be executed in parallel (default 10)

OPTIMIZATIONS:
   -timeout int                        time to wait in seconds before timeout (default 10)
   -retries int                        number of times to retry a failed request (default 1)
   -ldp, -leave-default-ports          leave default HTTP/HTTPS ports (eg. host:80,host:443)
   -mhe, -max-host-error int           max errors for a host before skipping from scan (default 30)
   -project                            use a project folder to avoid sending same request multiple times
   -project-path string                set a specific project path 
   -spm, -stop-at-first-match          stop processing HTTP requests after the first match (may break template/workflow logic)
   -stream                             stream mode - start elaborating without sorting the input
   -ss, -scan-strategy value           strategy to use while scanning(auto/host-spray/template-spray) (default 0)
   -irt, -input-read-timeout duration  timeout on input read (default 3m0s)
   -nh, -no-httpx                      disable httpx probing for non-url input
   -no-stdin                           disable stdin processing

HEADLESS:
   -headless                    enable templates that require headless browser support (root user on Linux will disable sandbox)
   -page-timeout int            seconds to wait for each page in headless mode (default 20)
   -sb, -show-browser           show the browser on the screen when running templates with headless mode
   -sc, -system-chrome          use local installed Chrome browser instead of nuclei installed
   -lha, -list-headless-action  list available headless actions

DEBUG:
   -debug                    show all requests and responses
   -dreq, -debug-req         show all sent requests
   -dresp, -debug-resp       show all received responses
   -p, -proxy string[]       list of http/socks5 proxy to use (comma separated or file input)
   -pi, -proxy-internal      proxy all internal requests
   -ldf, -list-dsl-function  list all supported DSL function signatures
   -tlog, -trace-log string  file to write sent requests trace log
   -elog, -error-log string  file to write sent requests error log
   -version                  show nuclei version
   -hm, -hang-monitor        enable nuclei hang monitoring
   -v, -verbose              show verbose output
   -profile-mem string       optional nuclei memory profile dump file
   -vv                       display templates loaded for scan
   -svd, -show-var-dump      show variables dump for debugging
   -ep, -enable-pprof        enable pprof debugging server
   -tv, -templates-version   shows the version of the installed nuclei-templates
   -hc, -health-check        run diagnostic check up

UPDATE:
   -un, -update                      update nuclei engine to the latest released version
   -ut, -update-templates            update nuclei-templates to latest released version
   -ud, -update-template-dir string  custom directory to install / update nuclei-templates
   -duc, -disable-update-check       disable automatic nuclei/templates update check

STATISTICS:
   -stats                    display statistics about the running scan
   -sj, -stats-json          write statistics data to an output file in JSONL(ines) format
   -si, -stats-interval int  number of seconds to wait between showing a statistics update (default 5)
   -m, -metrics              expose nuclei metrics on a port
   -mp, -metrics-port int    port to expose nuclei metrics on (default 9092)
```

## Running **Nuclei**

Nuclei templates can be primarily executed in two ways,

1) **Templates** (`-t/templates`)

As default, all the templates (except nuclei-ignore list) gets executed from default template installation path.

```sh
nuclei -u https://example.com
```

Custom template directory or multiple template directory can be executed as follows,

```sh
nuclei -u https://example.com -t cves/ -t exposures/
```

Custom template github repos are downloaded under `github` directory. Custom repo templates can be passed as follows
```sh
nuclei -u https://example.com -t github/private-repo
```

Similarly, Templates can be executed against list of URLs.

```sh
nuclei -list http_urls.txt
```

2) **Workflows** (`-w/workflows`)

```sh
nuclei -u https://example.com -w workflows/
```

Similarly, Workflows can be executed against list of URLs.

```sh
nuclei -list http_urls.txt -w workflows/wordpress-workflow.yaml
```

### Nuclei **Filters**

Nuclei engine supports three basic filters to customize template execution.


1. Tags (`-tags`)

    Filter based on tags field available in the template.

2. Severity (`-severity`)

    Filter based on severity field available in the template.

3. Author (`-author`)

    Filter based on author field available in the template.

As default, Filters are applied on installed path of templates and can be customized with manual template path input.

For example, below command will run all the templates installed at `~/nuclei-templates/` directory and has `cve` tags in it.

```sh
nuclei -u https://example.com -tags cve
```

And this example will run all the templates available under `~/nuclei-templates/exposures/` directory and has `config` tag in it.

```sh
nuclei -u https://example.com -tags config -t exposures/
```

Multiple filters works together with AND condition, below example runs all template with `cve` tags AND has `critical` OR `high` severity AND `geeknik` as author of template.

```sh
nuclei -u https://example.com -tags cve -severity critical,high -author geeknik
```

Multiple filters can also be combined using the template condition flag (`-tc`) that allows complex expressions like the following ones:
```sh
nuclei -tc "contains(id,'xss') || contains(tags,'xss')"
nuclei -tc "contains(tags,'cve') && contains(tags,'ssrf')"
nuclei -tc "contains(name, 'Local File Inclusion')"
```

The supported fields are:

- `id` string
- `name` string
- `description` string
- `tags` slice of strings
- `authors` slice of strings
- `severity` string
- `protocol` string
- `http_methods` slice of strings
- `bodies` string (containing all request bodies if any)
- `matcher_types` slice of string
- `extractor_types` slice of string
- `description` string

Also, every key-value pair from the template metadata section is accessible. All fields can be combined with logical operators (`||` and `&&`) and used with DSL helper functions.

Similarly, all filters are supported in workflows as well.


```sh
nuclei -w workflows/wordpress-workflow.yaml -severity critical,high -list http_urls.txt
```

!!! info "Workflows"

    In Workflows, Nuclei filters are applied on templates or sub-templates running via workflows, not on the workflows itself.

### Rate **Limits**

Nuclei have multiple rate limit controls for multiple factors, including a number of templates to execute in parallel, a number of hosts to be scanned in parallel for each template, and the global number of request / per second you wanted to make/limit using nuclei, here is an example of each flag with description.

| Flag       | Description                                                          |
|------------|----------------------------------------------------------------------|
| rate-limit | Control the total number of request to send per seconds              |
| bulk-size  | Control the number of hosts to process in parallel for each template |
| c          | Control the number of templates to process in parallel               |

    

Feel free to play with these flags to tune your nuclei scan speed and accuracy.

!!! tip
    `rate-limit` flag takes precedence over the other two flags, the number of requests/seconds can't go beyond the value defined for `rate-limit` flag regardless the value of `c` and `bulk-size` flag.

### Traffic **Tagging**

Many BugBounty platform/programs requires you to identify the HTTP traffic you make, this can be achieved by setting custom header using config file at `$HOME/.config/nuclei/config.yaml` or CLI flag `-H / header`


!!! info "Setting custom header using config file"

    ```yaml
    # Headers to include with each request.
    header:
      - 'X-BugBounty-Hacker: h1/geekboy'
      - 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) / nuclei'
    ```

!!! info "Setting custom header using CLI flag"


    ```yaml
    nuclei -header 'User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64) / nuclei' -list urls.txt -tags cves
    ```


### Template **Exclusion**

Nuclei supports a variety of methods for excluding / blocking templates from execution. By default, **nuclei** excludes the tags/templates listed below from execution to avoid unexpected fuzz based scans and some that are not supposed to run for mass scan, and these can be easily overwritten  with nuclei configuration file / flags.

- Default [Template ignore](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) list.


!!! danger ""

    **nuclei-ignore** file is not supposed to be updated / edited / removed by user, to overwrite default ignore list, utilize [nuclei configuration](https://nuclei.projectdiscovery.io/nuclei/get-started/#nuclei-config) file. 

Nuclei engine supports two ways to manually exclude templates from scan,

1. Exclude Templates (`-exclude-templates/exclude`)

    **exclude-templates** flag is used to exclude single or multiple templates and directory, multiple `-exclude-templates` flag can be used to provide multiple values.


2. Exclude Tags (`-exclude-tags/etags`)

    **exclude-tags** flag is used to exclude templates based in defined tags, single or multiple can be used to exclude templates.


!!! info "Example of excluding single template"

    ```
    nuclei -list urls.txt -t cves/ -exclude-templates cves/2020/CVE-2020-XXXX.yaml
    ```

!!! info "Example of multiple template exclusion"

    ```
    nuclei -list urls.txt -exclude-templates exposed-panels/ -exclude-templates technologies/
    ```

!!! info "Example of excluding templates with single tag"

    ```
    nuclei -l urls.txt -t cves/ -etags xss
    ```

!!! info "Example of excluding templates with multiple tags"

    ```
    nuclei -l urls.txt -t cves/ -etags sqli,rce
    ```

To easily overwrite [nuclei-ignore](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore), Nuclei engine supports **include-tags** / **include-templates** flag.

!!! info "Example of running blocked templates"

    ```
    nuclei -l urls.txt -include-tags iot,misc,fuzz
    ```
### Uncover **Integration**

Nuclei supports integration with [uncover](https://github.com/projectdiscovery/uncover) to executes template against hosts returned by uncover for the given query.

Here are uncover options to use -
```console
nuclei -h uncover

UNCOVER:
   -uc, -uncover                  enable uncover engine
   -uq, -uncover-query string[]   uncover search query
   -ue, -uncover-engine string[]  uncover search engine (shodan,shodan-idb,fofa,censys,quake,hunter,zoomeye) (default shodan)
   -uf, -uncover-field string     uncover fields to return (ip,port,host) (default "ip:port")
   -ul, -uncover-limit int        uncover results to return (default 100)
   -ucd, -uncover-delay int       delay between uncover query requests in seconds (0 to disable) (default 1)
```
You have set the API key of the engine you are using as an environment variable in your shell.
```
export SHODAN_API_KEY=xxx
export CENSYS_API_ID=xxx
export CENSYS_API_SECRET=xxx
export FOFA_EMAIL=xxx
export FOFA_KEY=xxx
export QUAKE_TOKEN=xxx
export HUNTER_API_KEY=xxx
export ZOOMEYE_API_KEY=xxx
```
Required API keys can be obtained by signing up on following platform [Shodan](https://account.shodan.io/register), [Censys](https://censys.io/register), [Fofa](https://fofa.info/toLogin), [Quake](https://quake.360.net/quake/#/index), [Hunter](https://user.skyeye.qianxin.com/user/register?next=https%3A//hunter.qianxin.com/api/uLogin&fromLogin=1) and [ZoomEye](https://www.zoomeye.org/login) .

Example of template execution using a search query.
```
export SHODAN_API_KEY=xxx
nuclei -id 'CVE-2021-26855' -uq 'vuln:CVE-2021-26855' -ue shodan
```
It can also read queries from templates metadata and execute template against hosts returned by uncover for that query.

Example of template execution using template-defined search queries.

Template snippet of [CVE-2021-26855](https://github.com/projectdiscovery/nuclei-templates/blob/master/cves/2021/CVE-2021-26855.yaml)

```yaml
  metadata:
    shodan-query: 'vuln:CVE-2021-26855'
```
```console
nuclei -t cves/2021/CVE-2021-26855.yaml -uncover
nuclei -tags cve -uncover
```

We can update the nuclei configuration file to include these tags for all scans.

## **Mass Scanning** using Nuclei

Nuclei fully utilises resources to optimise scanning speed. However, when scanning **thousands**, if not **millions, of targets**, scanning using default parameter values is bound to cause some performance issues ex: low RPS, Slow Scans, Process Killed, High RAM consumption, etc. this is due to limited resources and network I/O. Hence following parameters need to be tuned based on system configuration and targets. 

| Flag          | Short |  Description                                                         |
|---------------|-------|----------------------------------------------------------------------|
| scan-strategy |  -ss  | Scan Strategy to Use (auto/host-spray/template-spray)                |
| bulk-size     |  -bs  | Max Number of targets to scan in parallel                            |
| concurrency   |  -c   | Max Number of templates to use in parallel while scanning            |
| stream        |   -   | stream mode - start elaborating without sorting the input            |

!!! info "Note"

These are common parameters that need to be tuned . apart from these `-rate-limit`,`-retries`,`-timeout`,`-max-host-error` also need to be tuned based on targets that are being scanned

### Which Scan Strategy to Use?

**scan-strategy** option can have three possible values

- `host-spray`     : All templates are iterated over each target.
- `template-spray` : Each template is iterated over all targets.
- `auto`(Default)  : Placeholder of `template-spray` for now.

User should select **Scan Strategy** based on number of targets and Each strategy has its own pros & cons.

 - When targets < 1000 . `template-spray` should be used . this strategy is slightly faster than `host-spray` but uses more RAM and doesnot optimally reuse connections.
 - When targets > 1000 . `host-spray` should be used . this strategy uses less RAM than `template-spray` and reuses HTTP connections along with some minor improvements and these are crucial when mass scanning.

### Concurrency & Bulk-Size

Whatever the `scan-strategy` is `-concurrency` and `-bulk-size` are crucial for tuning any type of scan. While tuning these parameters following points should be noted.

**If `scan-strategy` is template-spray**

  - `-concurrency` < `bulk-size` (Ex: `-concurrency 10 -bulk-size 200`)

**If `scan-strategy` is host-spray**

  - `-concurrency` > `bulk-size` (Ex: `-concurrency 200 -bulk-size 10`) 


!!! Tip

`-concurrency` x `-bulk-size` <= 2500 (depending on system config)

### Stream

This option should only be enabled if targets > 10k . This skips any type of sorting or preprocessing on target list.



## Nuclei **Config**

!!! abstract ""

    Since release of [v.2.3.2](https://blog.projectdiscovery.io/nuclei-v2-3-0-release/) nuclei uses [goflags](https://github.com/projectdiscovery/goflags) for clean CLI experience and long/short formatted flags.

    [goflags](https://github.com/projectdiscovery/goflags) comes with auto-generated config file support that coverts all available CLI flags into config file, basically you can define all CLI flags into config file to avoid repetitive CLI flags that loads as default for every scan of nuclei.

    Default path of nuclei config file is `$HOME/.config/nuclei/config.yaml`, uncomment and configure the flags you wish to run as default.

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

Once configured, **config file be used as default**, additionally custom config file can be also provided using `-config` flag.

!!! info "Running nuclei with custom config file"

    ```
    nuclei -config project.yaml -list urls.txt
    ```

## Nuclei **Reporting**

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
    <td>Splunk HEC</td>
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
    <td><svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" width="24" height="24"><path fill-rule="evenodd" d="M1 12C1 5.925 5.925 1 12 1s11 4.925 11 11-4.925 11-11 11S1 18.075 1 12zm16.28-2.72a.75.75 0 00-1.06-1.06l-5.97 5.97-2.47-2.47a.75.75 0 00-1.06 1.06l3 3a.75.75 0 001.06 0l6.5-6.5z"></path></svg>
    </td>
  </tr>
</table>


`-rc, -report-config` flag can be used to provide a config file to read configuration details of the platform to integrate. Here is an [example config file](https://github.com/projectdiscovery/nuclei/blob/master/v2/cmd/nuclei/issue-tracker-config.yaml) for all supported platforms.



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

To forward results to Splunk HEC, create a config file with the following content and replace the appropriate values:-

```yaml
# splunkhec contains configuration options for splunkhec exporter
splunkhec:
  # Hostname for splunkhec instance
  host: "$hec_host"
  # Port is the port of splunkhec instance
  port: 8088
  # IndexName is the name of the splunkhec index
  index-name: nuclei
  # SSL enables ssl for splunkhec connection
  ssl: true
  # SSLVerification disables SSL verification for splunkhec
  ssl-verification: true
  # HEC Token for the splunkhec instance
  token: "$hec_token"  
```

**Running nuclei with reporting module:-**

```bash
nuclei -l urls.txt -t cves/ -rc issue-tracker.yaml
```

Similarly, other platforms can be configured. Reporting module also supports basic filtering and duplicate checks to avoid duplicate ticket creation.

```yaml
allow-list:
  severity: high,critical
```

This will ensure to only creating tickets for issues identified with **high** and **critical** severity; similarly, `deny-list` can be used to exclude issues with a specific severity.

If you are running periodic scans on the same assets, you might want to consider `-rdb, -report-db` flag that creates a local copy of the valid findings in the given directory utilized by reporting module to compare and **create tickets for unique issues only**.

```bash
nuclei -l urls.txt -t cves/ -rc issue-tracker.yaml -rdb prod
```

**<ins>Markdown Export</ins>**

Nuclei supports markdown export of valid findings with `-me, -markdown-export` flag, this flag takes directory as input to store markdown formatted reports.

Including request/response in the markdown report is optional, and included when `-irr, -include-rr` flag is used along with `-me`.

```bash
nuclei -l urls.txt -t cves/ -irr -markdown-export reports
```

**<ins>SARIF Export</ins>**

Nuclei supports SARIF export of valid findings with `-se, -sarif-export` flag. This flag takes a file as input to store SARIF formatted report.

```bash
nuclei -l urls.txt -t cves/ -sarif-export report.sarif
```

It is also possible to visualize Nuclei results using **sarif** file.

1. By Uploading SARIF File to [SARIF Viewer](https://microsoft.github.io/sarif-web-component/)

2. By Uploading SARIF File to Github Actions

more info [here](https://github.com/projectdiscovery/nuclei/pull/2925).

!!! info "Note"

These are **not official** viewers of Nuclei and `Nuclei` has no liability towards any of these options to visualize **Nuclei** results. These are just some publicly available options to visualize SARIF File.


## Scan **Metrics**

Nuclei expose running scan metrics on a local port `9092` when `-metrics` flag is used and can be accessed at **localhost:9092/metrics**, default port to expose scan information is configurable using `-metrics-port` flag.

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

Nuclei engine supports passive mode scanning for HTTP based template utilizing file support, with this support we can run HTTP based templates against locally stored HTTP response data collected from any other tool.

```sh
nuclei -passive -target http_data
```

!!! info ""

    Passive mode support is limited for templates having `{{BasedURL}}` or `{{BasedURL/}}` as base path.
    
## **Code** Contribution

[Nuclei templates](https://github.com/projectdiscovery/nuclei-templates) are the base of the nuclei project. We appreciate it if you can write and submit new templates to keep this project alive, and one of the reasons to keep us motivated to keep working on this project. 

## License

Nuclei is distributed under MIT [License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).
