```python
from colorama import Fore
```


```python
!pip install --user xlrd
```

    Requirement already satisfied: xlrd in /home/chiao/.local/lib/python3.6/site-packages (1.2.0)



```python
!tree -h --sort=size 開放權威檔/DDBC-Place

!tree -h --sort=size 開放權威檔/CBDB-Place
```

# Setup functions


```python
import pandas as pd
import os
import re

_filter_empty = lambda s: s != ''
filter_empty = lambda l: list(filter(_filter_empty , l))
filter_empty(re.split('[()]', '某氏(徐庶母)'))

def split_aliases(names):
    results = []
    for name in names:
        try:
            results.extend(filter_empty(re.split('[=()（）]', name)))
        except TypeError:
#             print('TypeError: value - ', name)
            pass
    return results

def get_entities(data_dirpath, col_name):
    all_entities = []

    for fname in os.listdir(data_dirpath):
        fpath = os.path.join(data_dirpath, fname)
        if os.path.isdir(fpath):
            print(fpath, 'is a directory. Go inside!')
            all_entities.extend(get_entities(fpath, col_name))
#             pass
        elif not os.path.isfile(fpath):
            print(fpath, 'is not a file. Skipped')
            continue
        else:
#             print('Processing', fpath, '...')
            df = pd.read_excel(fpath)
            entities = df[col_name].to_list()
            all_entities.extend(split_aliases(entities))
    return all_entities

def filter_symbols(es):
    special_symbols = '！？!?［］[]{}｛｝＠＃＄％︿＆＊＿＋｜＂：﹀＜～／－~@#$%^&*_-+=\\|"\';:></'

    f = lambda e: not (set(e) & set(special_symbols))
    return list(filter(f, es))

def main(data_dirpath, col_name, segmenter_output_fname, regexner_output_fname, tag, overridden_tag, min_len=2):
    all_entities = get_entities(data_dirpath, col_name)
    print('(info) total', len(all_entities))
    all_entities = filter_symbols(all_entities)
    print('(proc) after removing entites with symbols:', len(all_entities))
    all_entities = list(set(all_entities))
    print('(proc) after removing duplicates:', len(all_entities))
    from collections import Counter
    print('(info)', Counter(map(len, all_entities)))
    df = pd.DataFrame({"entity": all_entities, "tag": [tag] * len(all_entities), "override": [overridden_tag] * len(all_entities)})
    print('(proc) built data frame', df.head())
    print('(info) long entity', df.query('entity.str.len() > 5').head())
    
    if min_len:
        print(f'(proc) removing length < {min_len}:', df.query(f'entity.str.len() < {min_len}').head())
        df = df.query(f'entity.str.len() >= {min_len}')
        print('(info)', f'after removing length < {min_len}:', len(df))

    df.to_csv(regexner_output_fname, header=None, index=None, sep='\t')
    print(f'(proc) saving file to {regexner_output_fname}...')
    df.to_csv(segmenter_output_fname, header=None, index=None, columns=['entity'], sep='\t')
    print(f'(proc) saving file to {segmenter_output_fname}...')
    
    # copy to inside docker container
    print(f'(proc) copying {regexner_output_fname} to inside docker container ...')
    !docker cp {regexner_output_fname} corenlp_zh:/stanford-corenlp-full-2018-10-05


    print(f'(proc) copying {segmenter_output_fname} to inside docker container ...')
    !docker cp {segmenter_output_fname} corenlp_zh:/stanford-corenlp-full-2018-10-05
        
    # check
    print('(check) if existing in local ...')
    !head -n 2 {regexner_output_fname}
    !wc -l {regexner_output_fname}

    !head -n 2 {segmenter_output_fname}
    !wc -l {segmenter_output_fname}
    
    # check in docker
    print('(check) if existing in docker container ...')
    !docker exec corenlp_zh head -n 2 {regexner_output_fname}
    !docker exec corenlp_zh wc -l {regexner_output_fname}

    !docker exec corenlp_zh head -n 2 {segmenter_output_fname}
    !docker exec corenlp_zh wc -l {segmenter_output_fname}
```

# Main Process


```python
root_dir = '開放權威檔'

## CBDB-Place

# input
col_name = '行政區'
data_dir = 'CBDB-Place'
regexner_output_fname = 'cbdb-gpe-regexner.txt'
segmenter_output_fname = 'cbdb-gpe-segmenter.txt'
tag = 'GPE'
overridden_tag = 'O,PERSON'

data_dirpath = os.path.join(root_dir, data_dir)
main(data_dirpath, col_name, segmenter_output_fname, regexner_output_fname, tag, overridden_tag)

## DDBC-Place

col_name = '名'
data_dir = 'DDBC-Place'
regexner_output_fname = 'ddbc-place-regexner.txt'
segmenter_output_fname = 'ddbc-place-segmenter.txt'
tag = 'LOCATION'
overridden_tag = 'O,PERSON'
data_dirpath = os.path.join(root_dir, data_dir)
main(data_dirpath, col_name, segmenter_output_fname, regexner_output_fname, tag, overridden_tag)
```

## Generate properties file


```python
regexner_files = ['cbdb-person-regexner.txt', 'ddbc-person-regexner.txt', 'cbdb-gpe-regexner.txt', 'ddbc-place-regexner.txt']
segmenter_files = ['cbdb-person-segmenter.txt', 'ddbc-person-segmenter.txt', 'cbdb-gpe-segmenter.txt', 'ddbc-place-segmenter.txt']
```


```python
props_inpath = 'StanfordCoreNLP-chinese-fgc-template.properties'
props_outpath = 'StanfordCoreNLP-chinese-fgc.properties'

print('(proc) generating properties file ...')
!cp {props_inpath} {props_outpath}

old_str = '#ner.additional.regexner.mapping = ...'
new_str = 'ner.additional.regexner.mapping = ' + ','.join(regexner_files)
!sed -i 's/{old_str}/{new_str}/g' {props_outpath}

old_str = 'segment.serDictionary = edu\/stanford\/nlp\/models\/segmenter\/chinese\/dict-chris6.ser.gz'
new_str = old_str + ',' + ','.join(segmenter_files)
!sed -i 's/{old_str}/{new_str}/g' {props_outpath}

print('(modified part)')
!diff --color {props_inpath} {props_outpath}

print('(proc) moving to docker container')
!docker cp {props_outpath} corenlp_zh:/stanford-corenlp-full-2018-10-05/{props_outpath}
print('(check) if moved')
!docker exec corenlp_zh grep 'segment.serDictionary' {props_outpath}
!docker exec corenlp_zh grep 'ner.additional.regexner.mapping' {props_outpath}
```

# Restart CoreNLPServer


```python
# kill existent server
ps_str = !docker exec corenlp_zh ps | grep StanfordCoreNLPServer
if ps_str:
    print('(proc) killing server ps:', ps_str[0])
    pid = int(ps_str[0].strip().split(' ')[0])
    p = !docker exec corenlp_zh kill {pid} && ps | grep StanfordCoreNLPServer
    assert not p, p

# start a new server
print('(proc) starting a new server')
!docker exec -d corenlp_zh ash -c "java -mx8g -cp '*' edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -serverProperties StanfordCoreNLP-chinese-fgc.properties &> run.log" && docker exec corenlp_zh ps -a | grep StanfordCoreNLPServer
```

    (proc) starting a new server
       11 root      0:00 java -mx8g -cp * edu.stanford.nlp.pipeline.StanfordCoreNLPServer -port 9000 -timeout 15000 -serverProperties StanfordCoreNLP-chinese-fgc.properties


# Test Results


```python
!pip install stanfordnlp
```

    Collecting stanfordnlp
    [?25l  Downloading https://files.pythonhosted.org/packages/41/bf/5d2898febb6e993fcccd90484cba3c46353658511a41430012e901824e94/stanfordnlp-0.2.0-py3-none-any.whl (158kB)
    [K     |████████████████████████████████| 163kB 189kB/s eta 0:00:01
    [?25hCollecting protobuf (from stanfordnlp)
    [?25l  Downloading https://files.pythonhosted.org/packages/ff/f1/8dcd4219bbae8aa44fe8871a89f05eca2dca9c04f8dbfed8a82b7be97a88/protobuf-3.11.3-cp37-cp37m-manylinux1_x86_64.whl (1.3MB)
    [K     |████████████████████████████████| 1.3MB 71.6MB/s eta 0:00:01
    [?25hRequirement already satisfied: tqdm in /home/chiao/anaconda3/lib/python3.7/site-packages (from stanfordnlp) (4.36.1)
    Requirement already satisfied: numpy in /home/chiao/anaconda3/lib/python3.7/site-packages (from stanfordnlp) (1.17.2)
    Requirement already satisfied: requests in /home/chiao/anaconda3/lib/python3.7/site-packages (from stanfordnlp) (2.22.0)
    Collecting torch>=1.0.0 (from stanfordnlp)
    [?25l  Downloading https://files.pythonhosted.org/packages/1a/3b/fa92ece1e58a6a48ec598bab327f39d69808133e5b2fb33002ca754e381e/torch-1.4.0-cp37-cp37m-manylinux1_x86_64.whl (753.4MB)
    [K     |████████████████████████████████| 753.4MB 6.7kB/s eta 0:00:010   |▏                               | 4.2MB 48.6MB/s eta 0:00:16
    [?25hRequirement already satisfied: six>=1.9 in /home/chiao/anaconda3/lib/python3.7/site-packages (from protobuf->stanfordnlp) (1.12.0)
    Requirement already satisfied: setuptools in /home/chiao/anaconda3/lib/python3.7/site-packages (from protobuf->stanfordnlp) (41.4.0)
    Requirement already satisfied: urllib3!=1.25.0,!=1.25.1,<1.26,>=1.21.1 in /home/chiao/anaconda3/lib/python3.7/site-packages (from requests->stanfordnlp) (1.24.2)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /home/chiao/anaconda3/lib/python3.7/site-packages (from requests->stanfordnlp) (3.0.4)
    Requirement already satisfied: certifi>=2017.4.17 in /home/chiao/anaconda3/lib/python3.7/site-packages (from requests->stanfordnlp) (2019.9.11)
    Requirement already satisfied: idna<2.9,>=2.5 in /home/chiao/anaconda3/lib/python3.7/site-packages (from requests->stanfordnlp) (2.8)
    Installing collected packages: protobuf, torch, stanfordnlp
    Successfully installed protobuf-3.11.3 stanfordnlp-0.2.0 torch-1.4.0



```python
import sys
sys.path.append('../wiki_kb_inference')
from stanfordnlp_utils import *
from fgc_utils import *
from utils import load_json
import ipywidgets as widgets
from ipywidgets import interact, interact_manual

from stanfordnlp.server import CoreNLPClient
```


```python
docs = load_json('../wiki_kb_inference/FGC_release_all(cn).json')
```


```python
dids = [doc['DID'] for doc in docs]
get_doc(dids[0], docs)
# dids
```




    {'DID': 'D001',
     'DTEXT': '蘇軾（1037年1月8日－1101年8月24日），眉州眉山（今四川省眉山市）人，北宋時著名的文學家、政治家、藝術家、醫學家。字子瞻，一字和仲，號東坡居士、鐵冠道人。嘉佑二年進士，累官至端明殿學士兼翰林學士，禮部尚書。南宋理學方熾時，加賜諡號文忠，複追贈太師。有《東坡先生大全集》及《東坡樂府》詞集傳世，宋人王宗稷收其作品，編有《蘇文忠公全集》。\n其散文、詩、詞、賦均有成就，且善書法和繪畫，是文學藝術史上的通才，也是公認韻文散文造詣皆比較傑出的大家。蘇軾的散文為唐宋四家（韓愈、柳宗元、歐蘇）之末，與唐代的古文運動發起者韓愈並稱為「韓潮蘇海」，也與歐陽修並稱「歐蘇」；更與父親蘇洵、弟蘇轍合稱「三蘇」，父子三人，同列唐宋八大家。蘇軾之詩與黃庭堅並稱「蘇黃」，又與陸游並稱「蘇陸」；其詞「以詩入詞」，首開詞壇「豪放」一派，振作了晚唐、五代以來綺靡的西崑體餘風。後世與南宋辛棄疾並稱「蘇辛」，惟蘇軾故作豪放，其實清朗；其賦亦頗有名氣，最知名者為貶謫期間借題發揮寫的前後《赤壁賦》。宋代每逢科考常出現其文命題之考試，故當時學者曰：「蘇文熟，喫羊肉、蘇文生，嚼菜羹」。藝術方面，書法名列「蘇、黃、米、蔡」北宋四大書法家（宋四家）之首；其畫則開創了湖州畫派；並在題畫文學史上佔有舉足輕重的地位。',
     'QUESTIONS': [{'QID': 'D001Q01',
       'QTYPE': '基础题',
       'QTEXT': '蘇東坡在中國歷史上，是哪一個朝代的人？',
       'SENTS': [{'text': '苏东坡在中国历史上，', 'start': 0, 'end': 10},
        {'text': '是哪一个朝代的人？', 'start': 10, 'end': 19}],
       'ANSWER': [{'ATEXT': '北宋',
         'ATOKEN': [{'text': '北宋', 'start': 40}],
         'ATEXT_CN': '北宋'}],
       'ATYPE': 'Date-Duration',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [{'text': '苏轼', 'start': 0, 'end': 2},
        {'text': '东坡居士', 'start': 72, 'end': 76},
        {'text': '北宋', 'start': 40, 'end': 42}],
       'SHINT': [0, 2, 4],
       'QTEXT_CN': '苏东坡在中国历史上，是哪一个朝代的人？'},
      {'QID': 'D001Q02',
       'QTYPE': '基础题',
       'QTEXT': '蘇東坡是中國哪個省份的人？',
       'SENTS': [{'text': '苏东坡是中国哪个省份的人？', 'start': 0, 'end': 13}],
       'ANSWER': [{'ATEXT': '四川省',
         'ATOKEN': [{'text': '四川省', 'start': 31}],
         'ATEXT_CN': '四川省'}],
       'ATYPE': 'Location',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [{'text': '苏轼', 'start': 0, 'end': 2},
        {'text': '四川省', 'start': 31, 'end': 34},
        {'text': '东坡居士', 'start': 72, 'end': 76}],
       'SHINT': [0, 1, 4],
       'QTEXT_CN': '苏东坡是中国哪个省份的人？'},
      {'QID': 'D001Q03',
       'QTYPE': '基础题',
       'QTEXT': '蘇東坡的爸爸叫什麼名字?',
       'SENTS': [{'text': '苏东坡的爸爸叫什么名字?', 'start': 0, 'end': 12}],
       'ANSWER': [{'ATEXT': '蘇洵',
         'ATOKEN': [{'text': '苏洵', 'start': 288}],
         'ATEXT_CN': '苏洵'}],
       'ATYPE': 'Person',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [{'text': '苏轼', 'start': 0, 'end': 2},
        {'text': '东坡居士', 'start': 72, 'end': 76},
        {'text': '苏轼', 'start': 225, 'end': 227},
        {'text': '苏洵', 'start': 288, 'end': 290}],
       'SHINT': [0, 4, 17, 20],
       'QTEXT_CN': '苏东坡的爸爸叫什么名字?'},
      {'QID': 'D001Q04',
       'QTYPE': '进阶题',
       'QTEXT': '蘇文忠公指的是誰?',
       'SENTS': [{'text': '苏文忠公指的是谁?', 'start': 0, 'end': 9}],
       'ANSWER': [{'ATEXT': '蘇軾',
         'ATOKEN': [{'text': '苏轼', 'start': 0}],
         'ATEXT_CN': '苏轼'}],
       'ATYPE': 'Person',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [{'text': '苏轼', 'start': 0, 'end': 2},
        {'text': '文忠', 'start': 120, 'end': 122}],
       'SHINT': [0, 8],
       'QTEXT_CN': '苏文忠公指的是谁?'},
      {'QID': 'D001Q05',
       'QTYPE': '基础题',
       'QTEXT': '《蘇文忠公全集》是由何人編纂？',
       'SENTS': [{'text': '《苏文忠公全集》是由何人编纂？', 'start': 0, 'end': 15}],
       'ANSWER': [{'ATEXT': '王宗稷',
         'ATOKEN': [{'text': '王宗稷', 'start': 153}],
         'ATEXT_CN': '王宗稷'}],
       'ATYPE': 'Person',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [{'text': '苏文忠公全集', 'start': 164, 'end': 170},
        {'text': '王宗稷', 'start': 153, 'end': 156}],
       'SHINT': [11, 12],
       'QTEXT_CN': '《苏文忠公全集》是由何人编纂？'},
      {'QID': 'D001Q06',
       'QTYPE': '进阶题',
       'QTEXT': '韓愈在中國歷史上，是哪一個朝代的人？',
       'SENTS': [{'text': '韩愈在中国历史上，', 'start': 0, 'end': 9},
        {'text': '是哪一个朝代的人？', 'start': 9, 'end': 18}],
       'ANSWER': [{'ATEXT': '唐代',
         'ATOKEN': [{'text': '唐代', 'start': 250}],
         'ATEXT_CN': '唐代'}],
       'ATYPE': 'Date-Duration',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [{'text': '唐代', 'start': 250, 'end': 252},
        {'text': '韩愈', 'start': 260, 'end': 262}],
       'SHINT': [18],
       'QTEXT_CN': '韩愈在中国历史上，是哪一个朝代的人？'},
      {'QID': 'D001Q07',
       'QTYPE': '进阶题',
       'QTEXT': '蘇東坡與韓愈是否為好朋友?',
       'SENTS': [{'text': '苏东坡与韩愈是否为好朋友?', 'start': 0, 'end': 13}],
       'ANSWER': [{'ATEXT': '否',
         'ATOKEN': [{'text': '否', 'start': -1}],
         'ATEXT_CN': '否'}],
       'ATYPE': 'YesNo',
       'AMODE': 'YesNo',
       'ASPAN': [{'text': '唐代', 'start': 250, 'end': 252},
        {'text': '韩愈', 'start': 260, 'end': 262},
        {'text': '苏轼', 'start': 0, 'end': 2},
        {'text': '北宋', 'start': 40, 'end': 42},
        {'text': '东坡居士', 'start': 72, 'end': 76}],
       'SHINT': [0, 2, 4, 18],
       'QTEXT_CN': '苏东坡与韩愈是否为好朋友?'},
      {'QID': 'D001Q08',
       'QTYPE': '进阶题',
       'QTEXT': '蘇東坡曾擔任過哪些職位?',
       'SENTS': [{'text': '苏东坡曾担任过哪些职位?', 'start': 0, 'end': 12}],
       'ANSWER': [{'ATEXT': '端明殿學士、翰林學士及禮部尚書',
         'ATOKEN': [{'text': '端明殿学士', 'start': 92},
          {'text': '翰林学士', 'start': 98},
          {'text': '礼部尚书', 'start': 103}],
         'ATEXT_CN': '端明殿学士、翰林学士及礼部尚书'}],
       'ATYPE': 'Organization',
       'AMODE': 'Multi-Spans-Extraction',
       'ASPAN': [{'text': '苏轼', 'start': 0, 'end': 2},
        {'text': '东坡居士', 'start': 72, 'end': 76},
        {'text': '端明殿学士', 'start': 92, 'end': 97},
        {'text': '翰林学士', 'start': 98, 'end': 102},
        {'text': '礼部尚书', 'start': 103, 'end': 107}],
       'SHINT': [0, 4, 6, 7],
       'QTEXT_CN': '苏东坡曾担任过哪些职位?'},
      {'QID': 'D001Q09',
       'QTYPE': '基础题',
       'QTEXT': '辛棄疾是哪一個朝代的人？',
       'SENTS': [{'text': '辛弃疾是哪一个朝代的人？', 'start': 0, 'end': 12}],
       'ANSWER': [{'ATEXT': '南宋',
         'ATOKEN': [{'text': '南宋', 'start': 108}],
         'ATEXT_CN': '南宋'}],
       'ATYPE': 'Date-Duration',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [{'text': '辛弃疾', 'start': 384, 'end': 387},
        {'text': '南宋', 'start': 382, 'end': 384}],
       'SHINT': [27],
       'QTEXT_CN': '辛弃疾是哪一个朝代的人？'},
      {'QID': 'D001Q11',
       'QTYPE': '申论',
       'QTEXT': '蘇東坡為何被後人認為是文學藝術史上的通才?',
       'SENTS': [{'text': '苏东坡为何被后人认为是文学艺术史上的通才?', 'start': 0, 'end': 21}],
       'ANSWER': [{'ATEXT': '',
         'ATOKEN': [{'text': '', 'start': 0}],
         'ATEXT_CN': ''}],
       'ATYPE': 'Event',
       'AMODE': 'Single-Span-Extraction',
       'ASPAN': [],
       'SHINT': [],
       'QTEXT_CN': '苏东坡为何被后人认为是文学艺术史上的通才?'}],
     'SENTS': [{'text': '苏轼（1037年1月8日－1101年8月24日），', 'start': 0, 'end': 25},
      {'text': '眉州眉山（今四川省眉山市）人，', 'start': 25, 'end': 40},
      {'text': '北宋时著名的文学家、政治家、艺术家、医学家。', 'start': 40, 'end': 62},
      {'text': '字子瞻，一字和仲，', 'start': 62, 'end': 71},
      {'text': '号东坡居士、铁冠道人。', 'start': 71, 'end': 82},
      {'text': '嘉佑二年进士，', 'start': 82, 'end': 89},
      {'text': '累官至端明殿学士兼翰林学士，', 'start': 89, 'end': 103},
      {'text': '礼部尚书。南宋理学方炽时，', 'start': 103, 'end': 116},
      {'text': '加赐谥号文忠，', 'start': 116, 'end': 123},
      {'text': '复追赠太师。', 'start': 123, 'end': 129},
      {'text': '有《东坡先生大全集》及《东坡乐府》词集传世，', 'start': 129, 'end': 151},
      {'text': '宋人王宗稷收其作品，', 'start': 151, 'end': 161},
      {'text': '编有《苏文忠公全集》。', 'start': 161, 'end': 172},
      {'text': '\n其散文、诗、词、赋均有成就，', 'start': 172, 'end': 187},
      {'text': '且善书法和绘画，', 'start': 187, 'end': 195},
      {'text': '是文学艺术史上的通才，', 'start': 195, 'end': 206},
      {'text': '也是公认韵文散文造诣皆比较杰出的大家。', 'start': 206, 'end': 225},
      {'text': '苏轼的散文为唐宋四家（韩愈、柳宗元、欧苏）之末，', 'start': 225, 'end': 249},
      {'text': '与唐代的古文运动发起者韩愈并称为「韩潮苏海」，', 'start': 249, 'end': 272},
      {'text': '也与欧阳修并称「欧苏」；', 'start': 272, 'end': 284},
      {'text': '更与父亲苏洵、弟苏辙合称「三苏」，', 'start': 284, 'end': 301},
      {'text': '父子三人，同列唐宋八大家。', 'start': 301, 'end': 314},
      {'text': '苏轼之诗与黄庭坚并称「苏黄」，', 'start': 314, 'end': 329},
      {'text': '又与陆游并称「苏陆」；', 'start': 329, 'end': 340},
      {'text': '其词「以诗入词」，', 'start': 340, 'end': 349},
      {'text': '首开词坛「豪放」一派，', 'start': 349, 'end': 360},
      {'text': '振作了晚唐、五代以来绮靡的西昆体余风。', 'start': 360, 'end': 379},
      {'text': '后世与南宋辛弃疾并称「苏辛」，', 'start': 379, 'end': 394},
      {'text': '惟苏轼故作豪放，', 'start': 394, 'end': 402},
      {'text': '其实清朗；其赋亦颇有名气，', 'start': 402, 'end': 415},
      {'text': '最知名者为贬谪期间借题发挥写的前后《赤壁赋》。', 'start': 415, 'end': 438},
      {'text': '宋代每逢科考常出现其文命题之考试，', 'start': 438, 'end': 455},
      {'text': '故当时学者曰：「苏文熟，吃羊肉、苏文生，嚼菜羹」。', 'start': 455, 'end': 480},
      {'text': '艺术方面，书法名列「苏、黄、米、蔡」北宋四大书法家（宋四家）之首；', 'start': 480, 'end': 513},
      {'text': '其画则开创了湖州画派；', 'start': 513, 'end': 524},
      {'text': '并在题画文学史上占有举足轻重的地位。', 'start': 524, 'end': 542}],
     'DTEXT_CN': '苏轼（1037年1月8日－1101年8月24日），眉州眉山（今四川省眉山市）人，北宋时著名的文学家、政治家、艺术家、医学家。字子瞻，一字和仲，号东坡居士、铁冠道人。嘉佑二年进士，累官至端明殿学士兼翰林学士，礼部尚书。南宋理学方炽时，加赐谥号文忠，复追赠太师。有《东坡先生大全集》及《东坡乐府》词集传世，宋人王宗稷收其作品，编有《苏文忠公全集》。\n其散文、诗、词、赋均有成就，且善书法和绘画，是文学艺术史上的通才，也是公认韵文散文造诣皆比较杰出的大家。苏轼的散文为唐宋四家（韩愈、柳宗元、欧苏）之末，与唐代的古文运动发起者韩愈并称为「韩潮苏海」，也与欧阳修并称「欧苏」；更与父亲苏洵、弟苏辙合称「三苏」，父子三人，同列唐宋八大家。苏轼之诗与黄庭坚并称「苏黄」，又与陆游并称「苏陆」；其词「以诗入词」，首开词坛「豪放」一派，振作了晚唐、五代以来绮靡的西昆体余风。后世与南宋辛弃疾并称「苏辛」，惟苏轼故作豪放，其实清朗；其赋亦颇有名气，最知名者为贬谪期间借题发挥写的前后《赤壁赋》。宋代每逢科考常出现其文命题之考试，故当时学者曰：「苏文熟，吃羊肉、苏文生，嚼菜羹」。艺术方面，书法名列「苏、黄、米、蔡」北宋四大书法家（宋四家）之首；其画则开创了湖州画派；并在题画文学史上占有举足轻重的地位。'}




```python

@interact_manual
def pretty_corenlp(did=dids):
    doc_dic = get_doc(did, docs)
    print('[New]')
    with CoreNLPClient(endpoint='http://localhost:9000', start_server=False) as nlp:
        doc = nlp.annotate(doc_dic['DTEXT_CN'], properties={'ssplit.boundaryTokenRegex': '[。]|[!?！？]+'})

        for sent in doc.sentence:
            print(f'(s{sent.sentenceIndex})', end=' ')
            snp_pprint(sent, mode='custom', classes_w_color=['PERSON', 'GPE', 'LOCATION', 'MISC', 'TITLE'])
    print('-----------------------------------------------------')
    print('[Old]')
    with CoreNLPClient(endpoint='http://140.109.19.191:9000', start_server=False) as nlp:
        doc = nlp.annotate(doc_dic['DTEXT_CN'], properties={'ssplit.boundaryTokenRegex': '[。]|[!?！？]+',
                                                            'pipelineLanguage': 'zh'})

        for sent in doc.sentence:
            print(f'(s{sent.sentenceIndex})', end=' ')
            snp_pprint(sent, mode='custom', classes_w_color=['PERSON', 'GPE', 'LOCATION', 'MISC', 'TITLE'])
        
```


    interactive(children=(Dropdown(description='did', options=('D001', 'D002', 'D003', 'D004', 'D006', 'D007', 'D0…



```python
nlp = CoreNLPClient(endpoint='http://140.109.19.191:9000', start_server=False)

doc = nlp.annotate(docs[0]['DTEXT_CN'],properties={'ssplit.boundaryTokenRegex': '[。]|[!?！？]+',
                                                    'pipelineLanguage': 'zh'},
                  annotators='tokenize,ssplit,pos,lemma,ner,depparse,parse,coref,entitylink')

for sent in doc.sentence:
    print(f'(s{sent.sentenceIndex})', end=' ')
    snp_pprint(sent)
```

    (s0) 


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-3-a9770860473c> in <module>
          7 for sent in doc.sentence:
          8     print(f'(s{sent.sentenceIndex})', end=' ')
    ----> 9     snp_pprint(sent)
    

    /d/wiki_kb_inference/stanfordnlp_utils.py in snp_pprint(snp_sent, mode, **kwargs)
        134     }
        135     if mode == 'color':
    --> 136         print_multicolor_strs(token_texts, token_ners, color_table, **kwargs)
        137     elif mode == 'custom':
        138         print_multicolor_strs(token_texts, token_ners, all_classes=color_table.keys(), **kwargs)


    /d/wiki_kb_inference/utils.py in print_multicolor_strs(strs, strs_class, color_table, all_classes, classes_w_color, classes_wo_color, colors, **kwargs)
         81         classes_w_color, classes_wo_color = all_classes, {}
         82 
    ---> 83     num_classes_w_color = len(classes_w_color)
         84     num_classes_wo_color = len(classes_wo_color)
         85     assert num_classes_w_color + num_classes_wo_color == len(all_classes)


    TypeError: object of type 'NoneType' has no len()



```python
nlp = CoreNLPClient(endpoint='http://localhost:9000', start_server=False)

doc = nlp.annotate(docs[3]['DTEXT_CN'], properties={'ssplit.boundaryTokenRegex': '[。]|[!?！？]+'})

for sent in doc.sentence:
    print(f'(s{sent.sentenceIndex})', end=' ')
    snp_pprint(sent)
```

    (s0) 


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    <ipython-input-4-953e3248eb41> in <module>
          5 for sent in doc.sentence:
          6     print(f'(s{sent.sentenceIndex})', end=' ')
    ----> 7     snp_pprint(sent)
    

    /d/wiki_kb_inference/stanfordnlp_utils.py in snp_pprint(snp_sent, mode, **kwargs)
        134     }
        135     if mode == 'color':
    --> 136         print_multicolor_strs(token_texts, token_ners, color_table, **kwargs)
        137     elif mode == 'custom':
        138         print_multicolor_strs(token_texts, token_ners, all_classes=color_table.keys(), **kwargs)


    /d/wiki_kb_inference/utils.py in print_multicolor_strs(strs, strs_class, color_table, all_classes, classes_w_color, classes_wo_color, colors, **kwargs)
         81         classes_w_color, classes_wo_color = all_classes, {}
         82 
    ---> 83     num_classes_w_color = len(classes_w_color)
         84     num_classes_wo_color = len(classes_wo_color)
         85     assert num_classes_w_color + num_classes_wo_color == len(all_classes)


    TypeError: object of type 'NoneType' has no len()



```python
nlp = CoreNLPClient(endpoint='http://140.109.19.191:9000', start_server=False)

doc = nlp.annotate(docs[3]['DTEXT_CN'], properties={'ssplit.boundaryTokenRegex': '[。]|[!?！？]+',
                                                    'pipelineLanguage': 'zh'
#                                                  'segment.serDictionary': 'edu/stanford/nlp/models/segmenter/chinese/dict-chris6.ser.gz,ddbc-person-segmenter.txt,cbdb-person-segmenter.txt',
#                                                     'ner.additional.regexner.mapping': 'ddbc-person-regexner.txt,cbdb-person-regexner.txt'
                                                   })

for sent in doc.sentence:
    print(f'(s{sent.sentenceIndex})', end=' ')
    snp_pprint(sent)
```

    (s0) [35m元[0m [35m祐[0m [44m元年[0m [39m（[0m [44m1086年[0m [39m）[0m [39m，[0m [41m宋哲宗[0m [39m即位[0m [39m，[0m [39m高[0m [39m太[0m [39m皇太[0m [39m后[0m [39m垂帘听政[0m [39m，[0m [39m回朝[0m [39m任[0m [45m礼部[0m [45m郎中[0m [39m、[0m [1;40m中书舍人[0m [39m、[0m [39m翰林[0m [39m学士[0m [39m，[0m [35m元祐[0m [33m四[0m [35m年[0m [39m（[0m [44m1089年[0m [39m）[0m [39m拜[0m [39m龙图阁[0m [39m学士[0m [39m，[0m [39m曾[0m [39m出任[0m [42m杭州[0m [39m、[0m [32m颍州[0m [39m等[0m [39m知州[0m [39m职务[0m [39m，[0m [39m官[0m [39m至[0m [39m礼部[0m [39m尚书[0m [39m。[0m
    (s1) [35m绍圣[0m [44m元年[0m [39m（[0m [44m1094年[0m [39m）[0m [39m被[0m [39m哲宗[0m [39m贬谪[0m [39m至[0m [42m惠州[0m [39m、[0m [42m儋州[0m [39m（[0m [1;32m[46m海南岛[0m[0m [39m）[0m [39m。[0m
    (s2) [35m元符[0m [33m三[0m [35m年[0m [39m（[0m [44m1100年[0m [39m）[0m [39m，[0m [39m宋徽宗[0m [39m即位[0m [39m，[0m [39m向[0m [39m太后[0m [39m垂帘听政[0m [39m，[0m [39m下诏[0m [39m让[0m [41m苏轼北[0m [39m还[0m [39m。[0m
    (s3) [39m建中[0m [39m靖国[0m [44m元年[0m [39m（[0m [44m1101年[0m [39m）[0m [39m，[0m [34m夏天[0m [39m因[0m [39m冷饮[0m [39m过度[0m [39m，[0m [39m下痢[0m [39m不止[0m [39m，[0m [39m又[0m [39m误[0m [39m服[0m [39m黄芪[0m [39m，[0m [39m结果[0m [39m病情[0m [39m恶化[0m [39m，[0m [39m「[0m [39m齿间[0m [39m出血[0m [39m如[0m [39m蚯蚓[0m [39m者[0m [39m无数[0m [39m」[0m [39m，[0m [44m七月[0m [44m二十八日[0m [39m于[0m [34m[45m常州[0m[0m [34m[45m孙氏馆[0m[0m [39m病[0m [39m卒[0m [39m，[0m [35m享年[0m [33m六十四[0m [35m岁[0m [39m。[0m
    (s4) [39m由[0m [39m弟[0m [41m苏辙[0m [39m归葬[0m [39m于[0m [39m郏县[0m [39m小[0m [1;32m[46m峨眉山[0m[0m [39m。[0m
    (s5) [39m南宋[0m [39m宋孝宗[0m [39m追赠[0m [39m谥号[0m [39m「[0m [39m文忠[0m [39m」[0m [39m。[0m
    (s6) [39m苏轼[0m [39m疲于[0m [39m应付[0m [39m新旧[0m [39m党争[0m [39m，[0m [39m遇事[0m [39m「[0m [39m如[0m [39m食[0m [39m内有蝇[0m [39m，[0m [39m吐[0m [39m之[0m [39m乃[0m [39m已[0m [39m」[0m [39m，[0m [41m苏轼[0m [39m既[0m [39m反对[0m [41m王安石[0m [39m比较[0m [39m急进[0m [39m的[0m [39m改革[0m [39m措施[0m [39m，[0m [39m也[0m [39m不[0m [39m同意[0m [39m旧[0m [39m党[0m [39m司马光[0m [39m尽[0m [39m废[0m [39m新法[0m [39m，[0m [39m所以[0m [39m虽然[0m [45m新党[0m [39m一直[0m [39m称[0m [41m苏轼为[0m [39m旧[0m [39m党[0m [39m，[0m [39m但[0m [39m其实[0m [39m他[0m [39m在[0m [39m新旧[0m [33m两[0m [39m党[0m [39m之间[0m [39m均受[0m [39m排斥[0m [39m，[0m [39m仕途[0m [39m坎坷[0m [39m，[0m [39m时常[0m [39m远[0m [39m贬[0m [39m外方[0m [39m，[0m [39m不过[0m [39m他[0m [39m在[0m [39m各地[0m [39m居官[0m [39m清正[0m [39m，[0m [39m为民[0m [39m兴利[0m [39m除弊[0m [39m，[0m [39m政绩[0m [39m颇[0m [39m善[0m [39m，[0m [39m口碑[0m [39m甚佳[0m [39m，[0m [42m杭州[0m [1;32m[46m西湖[0m[0m [39m的[0m [32m苏堤[0m [39m就[0m [39m是[0m [39m实证[0m [39m。[0m



```python
nlp = CoreNLPClient(endpoint='http://localhost:9000', start_server=False)

doc = nlp.annotate(docs[27]['DTEXT_CN'], properties={'ssplit.boundaryTokenRegex': '[。]|[!?！？]+',
#                                                  'segment.serDictionary': 'edu/stanford/nlp/models/segmenter/chinese/dict-chris6.ser.gz,ddbc-person-segmenter.txt,cbdb-person-segmenter.txt',
#                                                     'ner.additional.regexner.mapping': 'ddbc-person-regexner.txt,cbdb-person-regexner.txt'
                                                   })

for sent in doc.sentence:
    print(f'(s{sent.sentenceIndex})', end=' ')
    snp_pprint(sent)
```

    (s0) [39m「[0m [39mPTT[0m [39m创世神[0m [39m」[0m [41m杜奕瑾[0m [39m创办[0m [39m的[0m [45m台湾[0m [45m人工[0m [45m智慧[0m [45m实验室[0m [39m推出[0m [39m「[0m [39m雅婷[0m [39m逐字稿[0m [39m」[0m [39mApp[0m [39m，[0m [39m已[0m [39m在[0m [39miOS[0m [39m与[0m [39mAndroid[0m [39m平台[0m [39m上[0m [39m线[0m [39m，[0m [39m官方[0m [39m表示[0m [39m能[0m [39m节省[0m [39m至少[0m [34m[43m60%[0m[0m [39m的[0m [39m听打[0m [39m时间[0m [39m，[0m [39m还[0m [39m听[0m [39m得[0m [39m懂[0m [1;40m台湾[0m [1;40m国语[0m [39m和[0m [39m中英[0m [39m夹杂[0m [39m。[0m
    (s1) [39m根据[0m [39mApp[0m [39m官方[0m [39m介绍[0m [39m，[0m [39m由[0m [45m台湾[0m [45m人工[0m [45m智慧[0m [45m实验室[0m [39m（[0m [39mAILabs[0m [39m）[0m [39m推出[0m [39m的[0m [39m「[0m [45m雅婷[0m [45m逐字稿[0m [39m」[0m [39mApp[0m [39m除了[0m [39m能[0m [39m即时[0m [39m做[0m [39m语音[0m [39m转[0m [39m文字[0m [39m，[0m [39m也可以[0m [39m用来[0m [39m提升[0m [39m听障人[0m [39m沟通[0m [39m效率[0m [39m。[0m
    (s2) [39m无论是[0m [39m记录[0m [39m生活[0m [39m大小事[0m [39m，[0m [39m或是[0m [39m记录[0m [39m访谈[0m [39m、[0m [39m课堂[0m [39m、[0m [39m会议[0m [39m内容[0m [39m，[0m [39m都[0m [39m可以[0m [39m精准[0m [39m且[0m [39m快速[0m [39m地[0m [39m达成[0m [39m原本[0m [39m需要[0m [39m花费[0m [33m大量[0m [39m时间[0m [39m才[0m [39m有的[0m [39m逐字稿[0m [39m。[0m
    (s3) [39m脸[0m [39m书[0m [39m帐号[0m [39mShar[0m [39mYuan[0m [39m的[0m [34m[45m台湾[0m[0m [34m[45m人工[0m[0m [34m[45m智慧[0m[0m [34m[45m实验室[0m[0m [39m员工[0m [44m日前[0m [39m发文[0m [39m说明[0m [39m，[0m [39m透过[0m [39m「[0m [39m雅婷[0m [39m逐字稿[0m [39m」[0m [39m，[0m [39m听障[0m [39m人士[0m [39m可[0m [39m透过[0m [39m文字[0m [39m的[0m [39m方式[0m [39m理解[0m [35m[1;40m其他[0m[0m [39m人[0m [39m在说[0m [39m什么[0m [39m，[0m [39m媒体[0m [39m工作者[0m [39m或[0m [31m记者[0m [39m能[0m [39m即时[0m [39m生成[0m [39m访谈[0m [39m或[0m [39m会议[0m [39m逐字稿[0m [39m，[0m [31m老师[0m [39m也[0m [39m能[0m [39m快速[0m [39m生成[0m [39m课程[0m [39m文字档[0m [39m。[0m
    (s4) [41m杜奕瑾[0m [44m今年[0m [39m初[0m [39m接受[0m [39m商业[0m [39m周刊[0m [39m采访[0m [39m时[0m [39m说[0m [39m，[0m [45m行政院[0m [39m政务[0m [31m委员[0m [41m唐[0m [39m凤[0m [39m为[0m [39m提倡[0m [39m政府[0m [39m资讯[0m [39m公开[0m [39m，[0m [39m常[0m [39m带[0m [39m著[0m [39m每[0m [39m分钟[0m [39m打字[0m [39m高达[0m [33m350[0m [39m字[0m [39m的[0m [39m速录师[0m [41m薛雅婷[0m [39m为[0m [39m会议[0m [39m制作[0m [39m逐[0m [39m字稿[0m [39m，[0m [39m因此[0m [45m台湾[0m [45m人工[0m [45m智慧[0m [45m实验室[0m [39m把[0m [39m内部[0m [39m开发[0m [39m的[0m [39m语音[0m [39m辨识[0m [39m系统[0m [39m取名[0m [39m为[0m [39m「[0m [39m雅婷[0m [33m一[0m [35m号[0m [39m」[0m [39m。[0m
    (s5) [41m杜奕瑾[0m [39m在[0m [39m访谈[0m [39m中[0m [39m指出[0m [39m，[0m [39m国际[0m [39m大[0m [39m厂[0m [39m已[0m [39m把[0m [1;40m英文[0m [39m、[0m [1;40m中文[0m [39m语音[0m [39m辨识[0m [39m的[0m [39m应用[0m [39m做得[0m [39m很[0m [39m好[0m [39m，[0m [39m台商[0m [39m机会[0m [39m在于[0m [39m把[0m [39m本[0m [39m土腔[0m [39m调和[0m [39m特殊[0m [39m用语[0m [39m做[0m [39m得[0m [39m更[0m [39m精进[0m [39m。[0m



```python
nlp = CoreNLPClient(endpoint='http://140.109.19.191:9000', start_server=False)

doc = nlp.annotate(docs[25]['DTEXT_CN'], properties={'ssplit.boundaryTokenRegex': '[。]|[!?！？]+',
                                                    'pipelineLanguage': 'zh'
#                                                  'segment.serDictionary': 'edu/stanford/nlp/models/segmenter/chinese/dict-chris6.ser.gz,ddbc-person-segmenter.txt,cbdb-person-segmenter.txt',
#                                                     'ner.additional.regexner.mapping': 'ddbc-person-regexner.txt,cbdb-person-regexner.txt'
                                                   })

for sent in doc.sentence:
    print(f'(s{sent.sentenceIndex})', end=' ')
    snp_pprint(sent)
```

    (s0) [39m根据[0m [39m「[0m [39m悬浮[0m [39m微粒[0m [39m特征[0m [39m对[0m [39m民众[0m [39m健康[0m [39m影响[0m [39m之[0m [39m研究[0m [39m」[0m [39m，[0m [39m捷运[0m [39m竟是[0m [39mPM2.5[0m [39m浓度[0m [39m暴露[0m [39m最[0m [39m高[0m [39m的[0m [39m交通[0m [39m工具[0m [39m。[0m
    (s1) [45m行政院[0m [39m环境[0m [39m保护[0m [39m署[0m [44m今天[0m [39m表示[0m [39m，[0m [39m这[0m [39m项[0m [39m结果[0m [39m只是[0m [39m瞬间[0m [39m数值[0m [39m，[0m [39m平均[0m [39m浓度[0m [39m最[0m [39m高[0m [39m的[0m [39m仍然[0m [39m是[0m [39m机车[0m [39m。[0m
    (s2) [45m环保署[0m [39m与[0m [45m国卫院[0m [45m国家[0m [45m环境[0m [45m医学[0m [45m研究所[0m [44m106年[0m [39m度[0m [39m针对[0m [39m针对[0m [39m捷运[0m [39m、[0m [39m公车[0m [39m、[0m [39m汽车[0m [39m、[0m [39m机车[0m [39m、[0m [39m步行[0m [39m、[0m [39m脚踏车[0m [39m等[0m [33m6[0m [39m大[0m [39m交通[0m [39m方式[0m [39m，[0m [39m进行[0m [39m「[0m [39m悬浮[0m [39m微粒[0m [39m特征[0m [39m对[0m [39m民众[0m [39m健康[0m [39m影响[0m [39m之[0m [39m研究[0m [39m」[0m [39m，[0m [39m结果[0m [39m报告[0m [39m指出[0m [39m，[0m [39m细[0m [39m悬浮[0m [39m微粒[0m [39m（[0m [39mPM2.5[0m [39m）[0m [39m浓度[0m [39m暴露[0m [39m排名[0m [39m最高[0m [39m的[0m [39m竟是[0m [39m捷运[0m [39m。[0m
    (s3) [45m环保署[0m [39m空气[0m [39m品质[0m [39m保护[0m [39m及[0m [39m噪音[0m [39m管制[0m [39m处[0m [39m专门[0m [31m委员[0m [41m徐淑芷[0m [39m接受[0m [45m中央社[0m [31m记者[0m [39m访问[0m [39m时[0m [39m表示[0m [39m，[0m [41m国卫[0m [39m院[0m [39m使用[0m [39m的[0m [39m方式[0m [39m是[0m [39m以[0m [39m空气[0m [39m盒子[0m [39m进行[0m [39m测量[0m [39m，[0m [39m在[0m [39m定点[0m [39m容易[0m [39m受到[0m [39m周遭[0m [39m环境[0m [39m或[0m [39m瞬间[0m [39m污染[0m [39m，[0m [39m而[0m [39m呈现[0m [39m高值[0m [39m。[0m
    (s4) [45m捷运[0m [45m测量[0m [45m处[0m [39m是[0m [39m在[0m [39m月台[0m [39m，[0m [39m列车[0m [39m进站[0m [39m时[0m [39m的[0m [39m状态[0m [39m，[0m [39m因为[0m [39m车刚[0m [39m进站[0m [39m会[0m [39m有[0m [33m一[0m [39m阵[0m [39m风[0m [39m，[0m [39m在[0m [39m地下[0m [39m化且[0m [39m没有[0m [39m闸门[0m [39m完全[0m [39m阻隔[0m [39m的[0m [39m站台[0m [39m，[0m [39m粉尘[0m [39m或[0m [39m微粒[0m [39m很[0m [39m容易[0m [39m因[0m [39m风[0m [39m而起[0m [39m，[0m [39m导致[0m [39m瞬间[0m [39m数值[0m [39m高升[0m [39m，[0m [39m与[0m [39m一般[0m [39m理解[0m [39m搭[0m [39m捷运[0m [39m的[0m [39m观念[0m [39m不[0m [39m一样[0m [39m，[0m [39m并不[0m [39m是[0m [39m一直[0m [39m维持[0m [39m高[0m [39m浓度[0m [39m的[0m [39mPM2.5[0m [39m。[0m
    (s5) [41m徐淑芷[0m [39m指出[0m [39m，[0m [39m虽然[0m [39m测量[0m [39m捷运[0m [39mPM2.5[0m [39m浓度[0m [39m瞬间[0m [39m值[0m [39m容易[0m [39m升高[0m [39m，[0m [39m但[0m [39m因为[0m [39m民众[0m [39m不是[0m [39m长时间[0m [39m曝露[0m [39m，[0m [39m整体[0m [39m来[0m [39m看相[0m [39m较于[0m [35m[1;40m其他[0m[0m [39m交通[0m [39m工具[0m [39m而言[0m [39m还是[0m [39m低[0m [39m的[0m [39m；[0m [45m捷运[0m [45m公司[0m [39m也[0m [39m有[0m [39m定期[0m [39m清理[0m [39m轨道[0m [39m、[0m [39m集尘[0m [39m等[0m [39m，[0m [39m降低[0m [39m因[0m [39m风[0m [39m扬起[0m [39m粉尘[0m [39m的[0m [39m情况[0m [39m，[0m [39m在[0m [39m地面[0m [39m的[0m [39m捷运[0m [39m车站[0m [39m虽然[0m [39m容易[0m [39m受到[0m [39m外界[0m [39m空气[0m [39m污染源[0m [39m影响[0m [39m，[0m [39m但[0m [39m不[0m [39m会[0m [39m有[0m [39m列车[0m [39m进站[0m [39m时[0m [39m因[0m [39m风[0m [39m瞬间[0m [39m扬起[0m [39m的[0m [39m粉尘[0m [39m。[0m
    (s6) [39m监测[0m [39m结果[0m [39m，[0m [39m以[0m [39m骑乘[0m [39m机车[0m [39m通勤[0m [39mPM2.5[0m [39m暴露[0m [39m量[0m [39m平均[0m [39m浓度[0m [39m最高[0m [39m，[0m [39m因[0m [39m道路[0m [39m行驶[0m [39m时[0m [39m难以[0m [39m避免[0m [39m接近[0m [39m高[0m [39m污染[0m [39m车辆[0m [39m，[0m [39m致使[0m [39mPM2.5[0m [39m暴露[0m [39m量[0m [39m有[0m [39m瞬间[0m [39m攀升[0m [39m严重[0m [39m污染[0m [39m的[0m [39m情形[0m [39m；[0m [39m其次[0m [39m为[0m [39m公车[0m [39m，[0m [39m因为[0m [39m常[0m [39m处于[0m [39m主要[0m [39m交通[0m [39m要道[0m [39m，[0m [39m车门[0m [39m开启[0m [39m时[0m [39m车门[0m [39m外[0m [39m常[0m [39m受到[0m [39m摩托车[0m [39m围绕[0m [39m，[0m [39m使[0m [39m移动[0m [39m源[0m [39m污染物[0m [39m进入[0m [39m车厢[0m [39m；[0m [43m第三[0m [39m才[0m [39m是[0m [39m捷运[0m [39m，[0m [39m会[0m [39m受[0m [39m站区[0m [39m或[0m [39m车厢[0m [39m的[0m [39m换气[0m [39m设备[0m [39m效能[0m [39m主导[0m [39m整体[0m [39m空气[0m [39m品质[0m [39m。[0m
    (s7) [41m徐淑芷[0m [39m强调[0m [39m，[0m [44m现在[0m [39m捷运[0m [39m车厢[0m [39m内[0m [39m的[0m [39m空气[0m [39m滤网[0m [39m、[0m [39m通风[0m [39m都[0m [39m做[0m [39m得[0m [39m很[0m [39m好[0m [39m，[0m [39m基本上[0m [39m车厢[0m [39m内[0m [39m的[0m [39m值[0m [39m都[0m [39m非常[0m [39m低[0m [39m；[0m [39m还是[0m [39m鼓励[0m [39m民众[0m [39m多[0m [39m使用[0m [39m大众[0m [39m运输[0m [39m工具[0m [39m，[0m [39m相较于[0m [39m骑[0m [39m机车[0m [39m、[0m [39m步行[0m [39m或[0m [39m脚踏车[0m [39m等[0m [39m，[0m [39m比较[0m [39m不[0m [39m会[0m [39m直接[0m [39m暴露[0m [39m在[0m [39m空气[0m [39m污染源[0m [39m中[0m



```python

```
