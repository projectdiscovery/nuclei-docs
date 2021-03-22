## **Nuclei** Features


!!! example "Features."

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

## Template **Download**

Nuclei templates can be downloaded and update using the `update-templates` flag of nuclei, which downloads the latest release from [Nuclei templates](https://github.com/projectdiscovery/nuclei-templates/releases) GitHub project, a community-curated list of templates that are ready to use.

```
nuclei -update-templates
```

!!! abstract "Info"
    **update-templates** flag download/pulls the latest release from https://github.com/projectdiscovery/nuclei-templates/releases, that does not include recently added templates in the master branch.

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
    -H, -header value                      Custom Header.
    -biid, -burp-collaborator-biid string  Burp Collaborator BIID
    -bs, -bulk-size int                    Maximum Number of hosts analyzed in parallel per template (default 25)
    -c, -concurrency int                   Maximum Number of templates executed in parallel (default 10)
    -config string                         Nuclei configuration file
    -debug                                 Debugging request and responses
    -debug-req                             Debugging request
    -debug-resp                            Debugging response
    -et, -exclude value                    Templates to exclude, supports single and multiple templates using directory.
    -etags, -exclude-tags value            Exclude templates with the provided tags
    -headless                              Enable headless browser based templates support
    -impact, -severity value               Templates to run based on severity, supports single and multiple severity.
    -irr, -include-rr                      Write requests/responses for matches in JSON output
    -json                                  Write json output to files
    -l, -list string                       List of URLs to run templates on
    -metrics                               Expose nuclei metrics on a port
    -metrics-port int                      Port to expose nuclei metrics on (default 9092)
    -nc, -no-color                         Disable colors in output
    -nt, -new-templates                    Only run newly added templates
    -nm, -no-meta                          Don't display metadata for the matches
    -o, -output string                     File to write output to (optional)
    -page-timeout int                      Seconds to wait for each page in headless (default 20)
    -passive                               Enable Passive HTTP response processing mode
    -project                               Use a project folder to avoid sending same request multiple times
    -project-path string                   Use a user defined project folder, temporary folder is used if not specified but enabled
    -proxy-socks-url string                URL of the proxy socks server
    -proxy-url string                      URL of the proxy server
    -r, -resolvers string                  File containing resolver list for nuclei
    -ra, -random-agent                     Use randomly selected HTTP User-Agent header value
    -rl, -rate-limit int                   Maximum requests to send per second (default 150)
    -rc, -report-config string             Nuclei Reporting Module configuration file
    -rdb, -report-db string                Local Nuclei Reporting Database
    -retries int                           Number of times to retry a failed request (default 1)
    -show-browser                          Show the browser on the screen
    -si, -stats-interval int               Number of seconds between each stats line (default 5)
    -silent                                Show only results in output
    -spm, -stop-at-first-path              Stop processing http requests at first match (this may break template/workflow logic)
    -stats                                 Display stats of the running scan
    -system-resolvers                      Use system dns resolving as error fallback
    -t, -templates value                   Templates to run, supports single and multiple templates using directory.
    -tags value                            Tags to execute templates for
    -u, -target string                     URL to scan with nuclei
    -tv, -templates-version                Shows the installed nuclei-templates version
    -timeout int                           Time to wait in seconds before timeout (default 5)
    -tl                                    List available templates
    -trace-log string                      File to write sent requests trace log
    -ud, -update-directory string          Directory storing nuclei-templates (default /root/nuclei-templates)
    -ut, -update-templates                 Download / updates nuclei community templates
    -v, -verbose                           Show verbose output
    -version                               Show version of nuclei
    -w, -workflows value                   Workflows to run for nuclei
    ```

## Running **Nuclei**

Nuclei templates can be executed in multiple ways, currently using **tags, templates, severity** and **workflows**.


??? info "Running nuclei with templates"

    **Templates** flag is used to provide template file or/and directory, single or multiple templates or directory can be used using multiple **t** flag inputs.

    !!! check "Running nuclei with single template"
    
    ```
    nuclei -t exposures/configs/git-config.yaml -l urls.txt
    ```
    
    !!! check "Running nuclei with template directory"
    
    ```
    nuclei -t cves/2021/ -l urls.txt
    ```
    
    !!! check "Running nuclei with multiple template directory"
    
    ```
    nuclei -t cves/2020/ -t exposed-tokens -t misconfiguration -l urls.txt
    ```

??? info "Running nuclei using tags"
    
    **Tags** can be used for template execution with or without the need of template directory/flag, if **templates/t** flag is used with `tags`, the tags will be applied on the particular template directory, otherwise, it will run all the templates with matched tag from default template download location (`$HOME/nuclei-templates/`).

    !!! check "Running nuclei with single tags"

    ```
    nuclei -tags cve -u urls.txt
    ```

    ```
    nuclei -tags network -u urls.txt
    ```

    ```
    nuclei -tags logs -u urls.txt
    ```

    !!! check "Running nuclei with tags along with directory"

    ```
    nuclei -tags config -t exposures/ -u urls.txt
    ```

    !!! check "Running nuclei with multiple tags"

    ```
    nuclei -tags lfi,ssrf,rce -t cves/ -l urls.txt
    ````

    ```
    nuclei -tags xss -t vulnerabilities/ -l urls.txt
    ````

??? info "Running nuclei with workflows"

    **Workflows** are the best possible way to manage and run multiple templates using a single workflow file for custom and dedicated workflow depending on the project and test case.

    !!! check "Running nuclei with workflow"

    ```
    nuclei -w workflows/wordpress-workflow.yaml -l wordpress_urls.txt
    ```

    !!! check "Running nuclei with multiple workflow"

    ```
    nuclei -w workflows/wordpress-workflow.yaml -w workflows/jira-workflow.yaml -l urls.txt
    ```

??? info "Running nuclei with severity"

    **Severity** flag is used to run templates with specific or multiple severities altogether. 

    !!! check "Running nuclei with single severity"

    ```
    nuclei -t cves/ -severity critical -l urls.txt
    ```

    !!! check "Running nuclei with multiple severity"

    ```
    nuclei -t cves/ -t vulnerabilities -severity critical,high -l urls.txt
    ```

??? info "Running nuclei with docker"

    !!! check "Running nuclei in docker container"
    
    ```
    echo hackerone.com | docker run -v $HOME/nuclei-templates:/root/nuclei-templates -i projectdiscovery/nuclei:v2.3.0 -t dns > output.txt
    ``` 

!!! warning
    Nuclei accept **URLs** as input format in order to execute HTTP templates.

!!! tip
    [httpx](https://github.com/projectdiscovery/httpx) can be used to generate URLs from subdomains as a input for nuclei.

## Nuclei **Reporting**

Nuclei comes with reporting module support with the release of [v2.3.0](https://nuclei.projectdiscovery.io/releases/nuclei-changelog/#nuclei-v230-10-march-2021) supporting GitHub, GitLab, and Jira integration, this allows nuclei engine to create automatic tickets on the supported platform based on found results.

<table>
  <tr>
    <th>Platform</th>
    <td>GitHub</td>
    <td>GitLab</td>
    <td>Jira</td>

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

## Rate **Limits**

Nuclei have multiple rate limit controls for multiple factors, including a number of templates to execute in parallel, a number of hosts to be scanned in parallel for each template, and the global number of request / per second you wanted to make/limit using nuclei, here is an example of each flag with description.

| Flag                   | Description                                                          |
| ---------------------- | ---------------------------------------------------------------------|
| rate-limit             | Control the total number of request to send per seconds              |
| bulk-size              | Control the number of hosts to process in parallel for each template |
| c                      | Control the number of templates to process in parallel               |

    

Feel free to play with these flags to tune your nuclei scan speed and accuracy.

!!! tip
    `rate-limit` flag takes precedence over the other two flags, the number of requests/seconds can't go beyond the value defined for `rate-limit` flag regardless the value of `c` and `bulk-size` flag.

## Template **Exclusion**

Since the release of nuclei `v2.1.1`, Nuclei got the support of `.nuclei-ignore` file that works along with `update-templates` flag of nuclei, in `.nuclei-ignore` file, you can define all the template directory or template path that you wanted to exclude from all nuclei scans.

Here is the ==[default list](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore)== of `nuclei-ignore` file that gets downloaded along with template download/update using `update-templates` flag and exclude the listed templates and directory from execution.


!!! warning
    `.nuclei-ignore` works only when templates are downloaded using `update-templates` flag.

You can always add, update and remove entries from `.nuclei-ignore` file using any preferred text editor from the following location,

```
$HOME/nuclei-templates/.nuclei-ignore
```
!!! abstract "Why nuclei-ignore?"
    
    To ensure nuclei is not getting used to hammer the web servers with templates that are meant to be used for specific use cases, including workflows, templates, and more, templates that have **severe** impact, e.g., DOS.

Nuclei also support template exclusion at run time using `exclude` flag, `exclude` flag works in a similar manner as `t` flag, single or multiple template files or directory can be provided using `exclude` flag multiple times.

!!! info "Running nuclei with single template exclusion"

    ```
    nuclei -l target_urls.txt -t cves/ -exclude cves/2020/CVE-2020-XXXX.yaml
    ```

!!! info "Running nuclei with multiple template exclusion"

    ```
    nuclei -l target_urls.txt -t nuclei-templates -exclude exposed-panels/ -exclude technologies
    ```



## **Code** Contribution

[Nuclei templates](https://github.com/projectdiscovery/nuclei-templates) are the base of the nuclei project. We appreciate it if you can write and submit new templates to keep this project alive, and one of the reasons to keep us motivated to keep working on this project. 

##License

Nuclei is distributed under MIT [License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).
