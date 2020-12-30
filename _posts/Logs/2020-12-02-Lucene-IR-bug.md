---
title: Lucene IR bug
---

- datetime: 12/01 09:55 PM (UTF+8)

## Description

- This bug happened in the stage of IR_test during executing the dvc pipeline, using `dvc repro`. This command runs several predefined commands in `dvc.yaml` by user and takes the parameter values from `params.yaml`. 

- The exeception shows up in the `main.py` of `pylucene` directory in the server side (which is hosted by docker container `pylucene`).

  ```
  /workspace/main.py in get_docs(self, query_str, topN, no_duplicate, escaped)
  81         query = self.query_parser.parse(query_str)
  JavaError: <super: <class 'JavaError'>, <JavaError object>>
      Java stacktrace:
  org.apache.lucene.queryparser.classic.ParseException: Cannot parse 'wind 0 , 0 km \/ h e . barometer 1000 , 2 hpa , falling slowly . temperature 12 , 4 ° c . rain today 0 , 0 mm . humidity "71 %"^': Encountered "<EOF>" at line 1, column 128.
  
  at org.apache.lucene.queryparser.classic.QueryParserBase.parse(QueryParserBase.java:114)
  ...
  ```

  You can also find in the `logs/server_debug.log` the logging information about this exception.

  ```
  2020/12/01 01:55:01 PM root:DEBUG:serve:55: data['query_str']: wind 0 , 0 km \/ h e . barometer 1000 , 2 hpa , falling slowly . temperature 12 , 4 ° c . rain today 0 , 0 mm . humidity "71 %"^2
  ```

  This tells us that the query string received by server does not seem to be causing any problem, while during parsing, the last `2` character was eaten when you compared 
  `wind 0 , 0 km \/ h e . barometer 1000 , 2 hpa , falling slowly . temperature 12 , 4 ° c . rain today 0 , 0 mm . humidity "71 %"^` 
  to the original query string
  `wind 0 , 0 km \/ h e . barometer 1000 , 2 hpa , falling slowly . temperature 12 , 4 ° c . rain today 0 , 0 mm . humidity "71 %"^2`

## Troubleshooting

```
import lucene
vm = lucene.initVM()
from org.apache.lucene.queryparser.classic import QueryParser
from org.apache.lucene.analysis.standard import StandardAnalyzer

analyzer = StandardAnalyzer()
query_parser = QueryParser("text", analyzer)
query_str = '° "trump"^2'
query_parser.parse(query_str)
```


character|string type|query string|equivalently used for parsing|status code
---|---|---|---|---
`°`|unicode string|`° "trump"^2`|`... "trump"^`|Error (last char was eaten)
`°`|utf8 encoded byte string|`\xc2\xb0 "trump"^2`|`... "trump"^2`|Success
`😂`|unicode string|`😂 "trump"^2`|`😂 "trump"^2`|Success
`😂`|utf8 encodedbyte string|`\xf0\x9f\x98\x82 "trump"^2`|`... "trump`|Error (last 3 chars were eaten)

### Observation

Unicode characters with UTF8 encoding starting with `C2` byte, (ranging from `C2 80` to `C2 BF`) (of `Latin-1 Supplement (Unicode block)`) from `U+0080` to `U+00BF` will raise exeception.



### All unicodes

```
import lucene
vm = lucene.initVM()
from org.apache.lucene.queryparser.classic import QueryParser
from org.apache.lucene.analysis.standard import StandardAnalyzer
analyzer = StandardAnalyzer()
query_parser = QueryParser("text", analyzer)

import sys
results = []
for i in range(sys.maxunicode):
    result = []
    results.append(result)
    result.append(str(i))
    u = chr(i)
    result.append(u)
    try:
        result.append(repr(u.encode('utf-8')))
    except Exception as e:
        result.append(repr(e))

    query_str = u + ' "trumpster"'
    try:
        result.append(repr(query_parser.parse(query_str)))
    except Exception as e:
        result.append(repr(e))
    try:
        result.append(repr(query_parser.parse(query_str.encode('utf-8'))))
    except Exception as e:
        result.append(repr(e))
    
with open('/tmp/lucene_unicode', 'w') as f:
    f.write('\t|\t'.join(['code #', 'char', 'utf8', 'query parsed', 'query parsed (utf8)']) + '\n')
    for res in results:
        _ = '\t|\t'.join(res)
        try:
            f.write(_ + '\n')
        except Exception as e:
            f.write(repr(e) + '\n')
```

