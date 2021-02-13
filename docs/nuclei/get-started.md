## **Nuclei** Features


!!! example "Features"

	* HTTP | DNS | TCP  | FILE support
	* Fully configurable templates.
	* Large scale scanning.
	* Easily write your own templates.


## **Nuclei** Installation

=== "Go"


    ```
    GO111MODULE=on go get -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei
    ```

    !!! info
		Nuclei requires latest **GO** version to install successfully.

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
		Nuclei requires latest **GO** version to install successfully.
=== "Binary"

    ```
    https://github.com/projectdiscovery/nuclei/releases
    ```

    !!! tip
		- Download the latest binary for your OS.
		- Unzip the binary with `tar -xzvf nuclei-*.tar.gz`

## Template **Download**

Nuclei templates can be downloaded and update using `update-templates` flag of nuclei which downloads latest release from [Nuclei templates](https://github.com/projectdiscovery/nuclei-templates/releases) GitHub project, a community curated list of templates that are ready to use.

```
nuclei -update-templates
```

!!! info
	`update-templates` flag does not include/download templates from master, i.e templates added after last release.

!!! tip
	Writing your own unique templates will always keep you ahead of others.

## **Nuclei** Usage

```
nuclei -h
```

This will display help for the tool. Here are all the switches it supports.

??? info "nuclei -h"

    | Flag                   | Description                                       | Example                                         |
    | ---------------------- | ------------------------------------------------- | ----------------------------------------------- |
    | bulk-size              | Max hosts analyzed in parallel per template       | nuclei -bulk-size 25                            |
    | burp-collaborator-biid | Burp Collaborator BIID                            | nuclei -burp-collaborator-biid XXXX             |
    | c                      | Number of concurrent requests (default 10)        | nuclei -c 100                                   |
    | l                      | List of urls to run templates                     | nuclei -l urls.txt                              |
    | target                 | Target to scan using Templates                    | nuclei -target hxxps://example.com              |
    | t                      | Templates input file/files to check across hosts  | nuclei -t git-core.yaml                         |
    | t                      | Templates input file/files to check across hosts  | nuclei -t cves/                                 |
    | no-color               | Don't Use colors in output                        | nuclei -no-color                                |
    | no-meta                | Don't display metadata for the matches            | nuclei -no-meta                                 |
    | json                   | Prints and write output in json format            | nuclei -json                                    |
    | include-rr             | Inlcude req/resp of matched output in JSON output | nuclei -json -include-rr                        |
    | o                      | File to save output result (optional)             | nuclei -o output.txt                            |
    | project                | Project flag to avoid sending same requests       | nuclei -project                                 |
    | project-path           | Use a user defined project folder                 | nuclei -project -project-path test              |
    | stats                  | Enable the progress bar (optional)                | nuclei -stats                                   |
    | silent                 | Show only found results in output                 | nuclei -silent                                  |
    | retries                | Number of times to retry a failed request         | nuclei -retries 1                               |
    | timeout                | Seconds to wait before timeout (default 5)        | nuclei -timeout 5                               |
    | trace-log              | File to write sent requests trace log             | nuclei -trace-log logs                          |
    | rate-limit             | Maximum requests/second (default 150)             | nuclei -rate-limit 100                          |
    | severity               | Run templates based on severity                   | nuclei -severity critical,high                  |
    | stop-at-first-match    | Stop processing http requests at first match      | nuclei -stop-at-first-match                     |
    | exclude                | Template input dir/file/files to exclude          | nuclei -exclude panels -exclude tokens          |
    | debug                  | Allow debugging of request/responses.             | nuclei -debug                                   |
    | update-templates       | Download and updates nuclei templates             | nuclei -update-templates                        |
    | update-directory       | Directory for storing nuclei-templates(optional)  | nuclei -update-directory templates              |
    | tl                     | List available templates                          | nuclei -tl                                      |
    | templates-version      | Shows the installed nuclei-templates version      | nuclei -templates-version                       |
    | v                      | Shows verbose output of all sent requests         | nuclei -v                                       |
    | version                | Show version of nuclei                            | nuclei -version                                 |
    | proxy-url              | Proxy URL                                         | nuclei -proxy-url hxxp://127.0.0.1:8080         |
    | proxy-socks-url        | Socks proxyURL                                    | nuclei -proxy-socks-url socks5://127.0.0.1:8080 |
    | random-agent           | Use random User-Agents                            | nuclei -random-agent                            |
    | H                      | Custom Header                                     | nuclei -H "x-bug-bounty: hacker"                |

## Running **Nuclei**

Nuclei templates can executed in multiple ways, `t` flag is used to provide template file or/and directory, single or multiple templates or directory can be used using multiple `t` input, here are few examples,

!!! info ""
    Running **exposures/configs/git-config.yaml** template on `target_urls.txt`

```
nuclei -t exposures/configs/git-config.yaml -l target_urls.txt
```

!!! info ""
	Running **cves/2020/** template directory on `target_urls.txt`

```
nuclei -t cves/2020/ -l target_urls.txt
```

!!! info "Running nuclei with severity filter"

```
nuclei -t cves/ -severity critical,high -l target_urls.txt
```

!!! info "Running nuclei in docker container"

```
cat urls.txt | docker run -v $HOME/nuclei-templates:/app/nuclei-templates -i projectdiscovery/nuclei -t /app/nuclei-templates/cves/ > nuclei_cve_scan.txt
```

??? info "More examples of running nuclei"
	Running **cves** template directory on `target.tld`

    ```
    nuclei -t cves/ -target https://target.tld
    ```

    !!! info "Running multiple templates directory"
    Running **cves**, **vulnerabilities**, and **misconfigurations** template directory on `target_urls.txt`

    ```
    nuclei -t cves/ -t vulnerabilities -t misconfigurations -l target_urls.txt
    ```

!!! warning
	Nuclei accepts **URLs** as input format in order to execute HTTP templates.

!!! tip
	[httpx](https://github.com/projectdiscovery/httpx) can be used to generate URLs from subdomains as a input for nuclei.
## Rate **Limits**

Nuclei have multiple rate limit controls for multiple factors including a number of templates to execute in parallel, a number of hosts to be scanned in parallel for each template, and the global number of request / per second you wanted to make/limit using nuclei, here is an example of each flags with description.

| Flag                   | Description                                                          |
| ---------------------- | ---------------------------------------------------------------------|
| rate-limit             | Control the total number of request to send per seconds              |
| bulk-size              | Control the number of hosts to process in parallel for each template |
| c                      | Control the number of templates to process in parallel               |

	

Feel free to play with these flags to tune your nuclei scan speed and accuracy.

!!! tip
	`rate-limit` flag takes precedence over other two flag, number of request/seconds can't go beyond the value defined for `rate-limit` flag regardless the value of `c` and `bulk-size` flag.

## Template **Exclusion**

Since release of nuclei `v2.1.1`, Nuclei got support of `.nuclei-ignore` file that works along with `update-templates` flag of nuclei, in `.nuclei-ignore` file, you can define all the template directory or template path that you wanted to exclude from all nuclei scans.

Here is the [default list](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) of `nuclei-ignore` file that gets downloaded along with template download/update using `update-templates` flag and exclude the listed templates and directory from execution.


!!! warning
	`.nuclei-ignore` works only when templates are downloaded using `update-templates` flag.

You can always add, update and remove entires from `.nuclei-ignore` file using any preferred text editor from following location,

```
$HOME/nuclei-templates/.nuclei-ignore
```

Nuclei also supports template exclusion at run time using `exclude` flag, `exclude` flag works in similar manner as `t` flag, single or multiple template files or directory can be provided using `exclude` flag multiple times.

!!! info "Running nuclei with single template exclusion"

    ```
    nuclei -l target_urls.txt -t cves/ -exclude cves/2020/CVE-2020-XXXX.yaml
    ```

!!! info "Running nuclei with multiple template exclusion"

    ```
    nuclei -l target_urls.txt -t nuclei-templates -exclude exposed-panels/ -exclude technologies
    ```

## **Code** Contribution

[Nuclei templates](https://github.com/projectdiscovery/nuclei-templates) are base of nuclei project, we appreciate if you can write and submit new templates to keep this project alive, and one of the reason to keep us motivated to keep working on this project. 

##License

Nuclei is distributed under MIT [License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).
