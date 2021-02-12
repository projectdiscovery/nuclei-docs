Github project for Nuclei documentation website https://nuclei.projectdiscovery.io

<details>
<summary>Local version of nuclei-docs</summary>


###### Installing mkdocs

```
python3 -m pip install mkdocs
python3 -m pip install pymdown-extensions>=7.0 --user
python3 -m pip install mkdocs-material-extensions>=1.0 --user
```

#### Running self-hosted version of nuclei-docs

```
mkdocs serve
```

self-hosted application will be served at http://127.0.0.1:8000

#### Publish changes on github pages

```
mkdocs gh-deploy
```

*Note:- mkdocs uses python3.x*

</details>
