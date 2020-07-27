##Get Started

### Installing using Source

Nuclei requires latest go version to install successfully, run the following command to install it using source -

```bash
GO111MODULE=on go get -u -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei
```
### Installing using Binary

Download latest binary based on your operating system from the [release](https://github.com/projectdiscovery/nuclei/releases) page.

```bash
tar -xzvf nuclei-linux-amd64.tar.gz
mv nuclei /usr/bin/
nuclei -h
```
### Installing using Github

You can also install by cloning the Github project and build it manually. 

```bash
git clone https://github.com/projectdiscovery/nuclei.git
cd nuclei/v2/cmd/nuclei/
go build .
mv nuclei /usr/local/bin/
nuclei -h
```

### Installing using Docker

We update nuclei dockerhub image on every release, you can simply pull the [dockerhub](https://hub.docker.com/r/projectdiscovery/nuclei) image to run.

```bash
docker pull projectdiscovery/nuclei
```


## Features

#### False positive free results
> This is test
#### Fully configurable templates
> This is test
#### Large scale scanning
> This is test
#### Create own templates
> This is test

## Using nuclei 

### Running single template

```bash
nuclei -l urls.txt -t files/git-core.yaml -o git-core.txt
```

### Running multiple templates

```bash
nuclei -l urls.txt -t files/ -t tokens/ -t cves/ -o output.txt
```
### Running with docker

```bash
cat urls.txt | docker run -v /path-to-nuclei-templates:/go/src/app/ -i projectdiscovery/nuclei -t ./files/git-config.yaml > results.txt
```
### Automation integration

```bash
subfinder -dL domains.txt -silent | httpx -silent | nulcei -t cves/ -o cves.txt
```

## Code contribution

##License

MIT License

Copyright (c) 2020 Exposed Atoms

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.