# UnicodeWorldCharacters

Unicode character table for different languages of the world. Obtained using the [Unicode Common Locale Data Repository](http://cldr.unicode.org/index/downloads).

## How is this done?

I used the files at this link: https://github.com/unicode-org/cldr/tree/release-37/common/main

Processing was carried out by the following python script:

```python
from os import listdir
from os.path import isfile, join
import xml.etree.ElementTree as ET
import csv

mypath = r"path\cldr-common-37.0\common\main"
onlyfiles = [f for f in listdir(mypath) if isfile(join(mypath, f))]
types = ['basic', 'index', 'auxiliary', 'numbers', 'punctuation']
out = open("characters.csv", "w", buffering=1, encoding="utf-8")
writer = csv.writer(out)
writer.writerow(["lang"] + types)
for file in onlyfiles:
    root = ET.parse(join(mypath, file)).getroot()
    for type_tag in root.findall('characters'):
        d = {t : "[]" for t in types}
        for characters in type_tag.findall('exemplarCharacters'):
            t = characters.get('type')
            if t is None:
                t = 'basic'
            d[t] = characters.text
        line = [file.split(".")[0]]
        for t in types:
            line.append(d[t])
        writer.writerow(line)
out.close()
```
