Github project for Nuclei documentation website https://nuclei.projectdiscovery.io


### Running self-hosted version

```bash
git clone https://github.com/projectdiscovery/nuclei-docs; cd nuclei-docs; \
docker run --rm -it -p 8000:8000 -v ${PWD}:/docs titom73/mkdocs
```

