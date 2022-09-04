### File Requests

Nuclei는 파일 시스템에서도 일치/추출할 수 있는 모델링 템플릿을 허용합니다.

```yaml
# Start of file template block
file:
```

#### Extensions

모든 확장자(기본 거부 목록에 있는 확장자 제외)에서 일치시키려면 다음을 사용하십시오.

```yaml
extensions:
  - all
```

일치해야 하는 사용자 지정 확장 목록을 제공할 수도 있습니다.

```yaml
extensions:
  - py
  - go
```

확장 프로그램의 거부 목록도 제공할 수 있습니다. 이러한 확장자를 가진 파일은 nuclei에서 처리되지 않습니다.

```yaml
extensions:
  - all

denylist:
  - go
  - py
  - txt
```

기본적으로 특정 확장자는 nuclei 파일 모듈에서 제외됩니다. 이들의 목록은 아래에 제공됩니다-

```
3g2,3gp,7z,apk,arj,avi,axd,bmp,css,csv,deb,dll,doc,drv,eot,exe,flv,gif,gifv,gz,h264,ico,iso,jar,jpeg,jpg,lock,m4a,m4v,map,mkv,mov,mp3,mp4,mpeg,mpg,msi,ogg,ogm,ogv,otf,pdf,pkg,png,ppt,psd,rar,rm,rpm,svg,swf,sys,tar,tar.gz,tif,tiff,ttf,txt,vob,wav,webm,wmv,woff,woff2,xcf,xls,xlsx,zip
```

#### More Options

**max-size** 매개변수는 nuclei 엔진이 읽는 파일의 최대 크기(바이트)를 제한하는 매개변수를 제공할 수 있습니다.

기본적으로 `max-size` 값은 5MB(5242880)이며 `max-size`보다 큰 파일은 처리되지 않습니다.

-----

**no-recursive** 옵션은 입력이 nuclei의 파일 모듈에 대해 처리되는 동안 디렉토리/글로브의 재귀적 워킹을 비활성화합니다.

#### Matchers / Extractor

**File** 프로토콜은 2가지 유형의 Matchers를 지원합니다. -

| Matcher Type | Part Matched |
|--------------|--------------|
| word         | all          |
| regex        | all          |


| Extractors Type | Part Matched |
|-----------------|--------------|
| word            | all          |
| regex           | all          |


#### **Example File Template**

개인 키 감지를 위한 최종 예제 템플릿 파일은 아래에 나와 있습니다.

```yaml
id: google-api-key

info:
  name: Google API Key
  author: pdteam
  severity: info

file:
  - extensions:
      - all
      - txt

    extractors:
      - type: regex
        name: google-api-key
        regex:
          - "AIza[0-9A-Za-z\\-_]{35}"
```

```bash
# http-response/ 디렉토리에서 파일 템플릿 실행
nuclei -t file.yaml -target http-response/

# output.txt에서 파일 템플릿 실행
nuclei -t file.yaml -target output.txt
```

더 완전한 예가 제공됩니다. [here](../../template-examples/file.md)
