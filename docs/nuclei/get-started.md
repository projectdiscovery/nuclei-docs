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
    GO111MODULE=on go get -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei
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
        - Unzip the binary with `tar -xzvf nuclei-*.tar.gz`

## Nuclei **Templates**

Nuclei has had built-in support for automatic update/download templates since version [v2.4.0](https://github.com/projectdiscovery/nuclei/releases/tag/v2.4.0). [**Nuclei-Templates**](https://github.com/projectdiscovery/nuclei-templates) project provides a community-contributed list of ready-to-use templates that is constantly updated.

You may still use the `update-templates` flag to update the nuclei templates at any time; automatic updates happen every 24 hours.

!!! tip
    Writing your own unique templates will always keep you one step ahead of others.

## **Nuclei** Usage

```
nuclei -h
```

This will display help for the tool. Here are all the switches it supports.

??? info "nuclei -h"
    ```
    Flag                                   Description                                                 
    -------------------------------------  ------------------------------------------------------------
    -H, -header value                  Custom Header.
    -author value                      Templates to run based on author
    -bs, -bulk-size int                Maximum Number of hosts analyzed in parallel per template (default 25)
    -c, -concurrency int               Maximum Number of templates executed in parallel (default 10)
    -config string                     Nuclei configuration file
    -debug                             Debugging request and responses
    -debug-req                         Debugging request
    -debug-resp                        Debugging response
    -et, -exclude value                Templates to exclude, supports single and multiple templates using directory.
    -etags, -exclude-tags value        Exclude templates with the provided tags
    -headless                          Enable headless browser based templates support
    -impact, -severity value           Templates to run based on severity
    -irr, -include-rr                  Write requests/responses for matches in JSON output
    -include-tags value                Tags to force run even if they are in denylist
    -include-templates value           Templates to force run even if they are in denylist
    -interactions-cache-size int       Number of requests to keep in interactions cache (default 5000)
    -interactions-cooldown-period int  Extra time for interaction polling before exiting (default 5)
    -interactions-eviction int         Number of seconds to wait before evicting requests from cache (default 60)
    -interactions-poll-duration int    Number of seconds before each interaction poll request (default 5)
    -interactsh-url string             Self Hosted Interactsh Server URL (default https://interact.sh)
    -json                              Write json output to files
    -l, -list string                   List of URLs to run templates on
    -me, -markdown-export string       Directory to export results in markdown format
    -metrics                           Expose nuclei metrics on a port
    -metrics-port int                  Port to expose nuclei metrics on (default 9092)
    -nc, -no-color                     Disable colors in output
    -nt, -new-templates                Only run newly added templates
    -nm, -no-meta                      Don't display metadata for the matches
    -no-interactsh                     Do not use interactsh server for blind interaction polling
    -o, -output string                 File to write output to (optional)
    -page-timeout int                  Seconds to wait for each page in headless (default 20)
    -passive                           Enable Passive HTTP response processing mode
    -project                           Use a project folder to avoid sending same request multiple times
    -project-path string               Use a user defined project folder, temporary folder is used if not specified but enabled
    -proxy-socks-url string            URL of the proxy socks server
    -proxy-url string                  URL of the proxy server
    -r, -resolvers string              File containing resolver list for nuclei
    -rl, -rate-limit int               Maximum requests to send per second (default 150)
    -rc, -report-config string         Nuclei Reporting Module configuration file
    -rdb, -report-db string            Local Nuclei Reporting Database (Always use this to persistent report data)
    -retries int                       Number of times to retry a failed request (default 1)
    -se, -sarif-export string          File to export results in sarif format
    -show-browser                      Show the browser on the screen
    -si, -stats-interval int           Number of seconds between each stats line (default 5)
    -silent                            Show only results in output
    -spm, -stop-at-first-path          Stop processing http requests at first match (this may break template/workflow logic)
    -stats                             Display stats of the running scan
    -stats-json                        Write stats output in JSON format
    -system-resolvers                  Use system dns resolving as error fallback
    -t, -templates value               Templates to run, supports single and multiple templates using directory.
    -tags value                        Tags to execute templates for
    -u, -target string                 URL to scan with nuclei
    -tv, -templates-version            Shows the installed nuclei-templates version
    -timeout int                       Time to wait in seconds before timeout (default 5)
    -tl                                List available templates
    -trace-log string                  File to write sent requests trace log
    -ud, -update-directory string      Directory storing nuclei-templates (default /Users/geekboy/nuclei-templates)
    -ut, -update-templates             Download / updates nuclei community templates
    -v, -verbose                       Show verbose output
    -validate                          Validate the passed templates to nuclei
    -version                           Show version of nuclei
    -vv                                Display Extra Verbose Information
    -w, -workflows value               Workflows to run for nuclei
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

Similarly, all filters are supported in workflows as well.


```sh
nuclei -w workflows/wordpress-workflow.yaml -severity critical,high -list http_urls.txt
```

!!! info "Workflows"

    In Workflows, Nuclei filters are applied on templates or sub-templates running via workflows, not on the workflows itself.

### Rate **Limits**

Nuclei have multiple rate limit controls for multiple factors, including a number of templates to execute in parallel, a number of hosts to be scanned in parallel for each template, and the global number of request / per second you wanted to make/limit using nuclei, here is an example of each flag with description.

| Flag                   | Description                                                          |
| ---------------------- | ---------------------------------------------------------------------|
| rate-limit             | Control the total number of request to send per seconds              |
| bulk-size              | Control the number of hosts to process in parallel for each template |
| c                      | Control the number of templates to process in parallel               |

    

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

We can update the nuclei configuration file to include these tags for all scans.

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

# Templates Filters
tags: exposures,cve
author: geeknik,pikpikcu,dhiyaneshdk
severity: critical,high,medium

# Template Allowlist
include-tags: dos,fuzz # Tag based inclusion (allows to overwrite nuclei-ignore list)
include-templates: # Template based inclusion (allows to overwrite nuclei-ignore list)
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
  </tr>
</table>


`-rc, -report-config` flag can be used to provide a config file to read configuration details of the platform to integrate. Here is an [example config file](https://github.com/projectdiscovery/nuclei/blob/master/v2/cmd/nuclei/issue-tracker-config.yaml) for all supported platforms.



For example, to create tickets on GitHub, create a config file with the following content and replace the appropriate values:-

```yaml
# Github contains configuration options for GitHub issue tracker

github: 
  username: "$user"
  owner: "$user"
  token: "$token"
  project-name: "testing-project"
  issue-label: "Nuclei"
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

## Scan **Metrics**

Nuclei expose running scan metrics on a local port `9092` when `-metrics` flag is used and can be accessed at **localhost:9092/metrics**, default port to exposes scan information is configurable using `-metrics-port` flag.

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
