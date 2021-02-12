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

## Running **Nuclei**

Templates can executed in multiple ways in nuclei.

!!! info ""
	Running **exposures** template directory on `target_urls.txt`

```bash
nuclei -t exposures -l target_urls.txt
```


!!! info ""
	Running **cves**, **vulnerabilities**, and **misconfigurations** template directory on `target_urls.txt`

```
nuclei -t cves/ -t vulnerabilities -t misconfigurations -l target_urls.txt
```

!!! info ""
	Running **exposures/configs/git-config.yaml** template on `target_urls.txt`

```
nuclei -t exposures/configs/git-config.yaml -l target_urls.txt
```


!!! info ""
	Running **cves** template directory on `target.tld`

```
nuclei -t cves/ -target https://target.tld
```

!!! info ""
	Running **nuclei-templates** directory on `urls.txt` using docker container.

```bash
cat urls.txt | docker run -v $HOME/nuclei-templates:/app/nuclei-templates -i projectdiscovery/nuclei -t /app/nuclei-templates/cves/ > nuclei_cve_scan.txt
```

!!! warning
	Nuclei accepts **URLs** as input format in order to execute HTTP templates.

!!! tip
	[httpx](https://github.com/projectdiscovery/httpx) can be used to generate URLs from subdomains as a input for nuclei.
## Rate **Limits**

Nuclei have multiple rate limit controls for multiple factors including a number of templates to execute in parallel, a number of hosts to be scanned in parallel for each template, and the global number of request / per second you wanted to make/limit using nuclei, here is an example of each flags with description.

- **rate-limit** => controls the total number of request to send per second.
- **bulk-size** => controls the number of hosts to process in parallel for each template.
- **c** => Controls the number of templates to process in parallel.
	

Feel free to play with these flags to tune your nuclei scan speed and accuracy.

!!! tip
	`rate-limit` flag takes precedence over other two flag, number of request/seconds can't go beyond the value defined for `rate-limit` flag regardless the value of `c` and `bulk-size` flag.

## Template **Exclusion**

Since release of nuclei `v2.1.1`, Nuclei got support of `.nuclei-ignore` file that works along with `update-templates` flag of nuclei, in `.nuclei-ignore` file, you can define all the template directory or template path that you wanted to exclude from all nuclei scans.

Here is [default list](https://github.com/projectdiscovery/nuclei-templates/blob/master/.nuclei-ignore) of `nuclei-ignore` file that gets downloaded along with template download/update using `update-templates` flag and exclude the listed templates and directory from execution.


!!! warning
	`.nuclei-ignore` works only when templates are downloaded using `update-templates` flag.

You can also add, update and remove entires from nuclei-ignore file at following path

```
$HOME/nuclei-templates/.nuclei-ignore
```

## **Code** Contribution

[Nuclei templates](https://github.com/projectdiscovery/nuclei-templates) are base of nuclei project, we appreciate if you can write and submit new templates to keep this project alive, and one of the reason to keep us motivated to keep working on this project. 

##License

Nuclei is distributed under MIT [License](https://github.com/projectdiscovery/nuclei/blob/master/LICENSE.md).
