# Get Started

## Installing using Source

```bash
GO111MODULE=on go get -u -v github.com/projectdiscovery/nuclei/v2/cmd/nuclei
```
## Installing using Binary

```bash
tar -xzvf nuclei-linux-amd64.tar.gz
mv nuclei /usr/bin/
nuclei -h
```
## Installing using Github

```bash
git clone https://github.com/projectdiscovery/nuclei.git
cd nuclei/v2/cmd/nuclei/
go build .
mv nuclei /usr/local/bin/
nuclei -h
```

## Installing using Docker

```bash
docker pull projectdiscovery/nuclei
```


# Features

- False positive free results
- Fully configurable templates
- Large scale scanning
- Define and create own templates

# Uses

## Running with single templates

```bash
nuclei -l urls.txt -t files/git-core.yaml -o results.txt
```

## Running with multiple templates

```bash
cat urls.txt | nuclei -t files/git-core.yaml -o results.txt
```
## Running with docker

```bash
cat urls.txt | docker run -v /path-to-nuclei-templates:/go/src/app/ -i projectdiscovery/nuclei -t ./files/git-config.yaml > results.txt
```
## Integrating with automation pipeline

```bash
subfinder -dL domains.txt -silent | httpx -silent | nulcei -t cves/ -o cves.txt
```
# Contribution

## Code contribution

# License

## License