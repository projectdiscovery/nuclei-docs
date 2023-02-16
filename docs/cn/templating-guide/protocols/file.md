### 文件请求

Nuclei 也支持对文件系统中的文件做匹配 / 提取。

```yaml
# 文件模板块的开始
file:
```

#### 扩展名

要匹配的所有的扩展名，请用 -

```yaml
extensions:
  - all
```

也可以提供一个需要匹配的自定义扩展名列表。

```yaml
extensions:
  - py
  - go
```

还可提供排除的扩展名列表，以这些扩展名结尾的文件不会被处理。

```yaml
extensions:
  - all

denylist:
  - go
  - py
  - txt
```

默认情况，下面这些扩展名将会被 Nuclei 排除掉 -

```
3g2,3gp,7z,apk,arj,avi,axd,bmp,css,csv,deb,dll,doc,drv,eot,exe,flv,gif,gifv,gz,h264,ico,iso,jar,jpeg,jpg,lock,m4a,m4v,map,mkv,mov,mp3,mp4,mpeg,mpg,msi,ogg,ogm,ogv,otf,pdf,pkg,png,ppt,psd,rar,rm,rpm,svg,swf,sys,tar,tar.gz,tif,tiff,ttf,txt,vob,wav,webm,wmv,woff,woff2,xcf,xls,xlsx,zip
```

#### 更多选项

**max-size** 参数限制 Nuclei 读取文件的最大大小（单位：字节）。

`max-size` 默认值为 5 MB（5242880），超过 `max-size` 的内容将不会被处理。

-----

**no-recursive** 选项禁止 Nuclei 递归处理文件。

#### 匹配 / 提取

**File** 协议支持两种匹配类型 -

| 匹配类型 | 匹配位置 |
|----------|----------|
| word     | all      |
| regex    | all      |


| 提取类型 | 匹配位置 |
|----------|----------|
| word     | all      |
| regex    | all      |


#### **实例模板**

最后提供一个检测私钥泄漏的模板作为演示：

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
# 检测 http-response/ 目录
nuclei -t file.yaml -target http-response/

# 检测 output.txt 文件
nuclei -t file.yaml -target output.txt
```

[这里](../../template-examples/file.md)提供了更多的实例。
