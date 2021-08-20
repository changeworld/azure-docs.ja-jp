---
title: COVID-19 のオープン リサーチ データセット
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で COVID-19 のオープン リサーチ データセットを使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 1225cc9f88fb07766f8f6e89e5a2502bdb83653a
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2021
ms.locfileid: "112038954"
---
# <a name="covid-19-open-research-dataset"></a>COVID-19 のオープン リサーチ データセット

機械判読用に最適化され、グローバルな研究コミュニティで使用できる、COVID-19 およびコロナウイルス関連の学術論文の全文およびメタデータ データセット。

COVID-19 パンデミックに対する応答として、 [アレン人工知能研究所](https://allenai.org/)は、Covid 19 オープン リサーチ データセットを準備して配布する主要リサーチ グループと提携しています (CORD-19)。 このデータセットは47,000 本もの学術論文を無料公開しており、COVID-19 および世界中の研究コミュニティが使用するウィルスのコロナウィルス ファミリーについて 36,000 本以上は全文が公開されています。

このデータセットは、研究者を動員して自然言語処理における最新の進展状況を適用することで、この感染症との闘いをサポートするための新しい分析情報を生成する目的で作成されています。

全文献は、新しい研究は査読された出版物や [bioRxiv](https://www.biorxiv.org/)、[medRxiv](https://www.medrxiv.org/) などのアーカイブ サービスで公開されると、更新される可能性があります。

[!INCLUDE [Open Dataset usage notice](../../includes/open-datasets-usage-note.md)]

## <a name="license-terms"></a>ライセンス条項

このデータセットは、アレン人工知能研究所の学術文献検索サービス[Semantic Scholar](https://pages.semanticscholar.org/coronavirus-research)から利用できます。 CORD-19 データセットで提供されているコンテンツにアクセスしたりダウンロードしたりすることで、このデータセットの使用に関連する[データセット ライセンス](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/COVID.DATA.LIC.AGMT.pdf)に同意したものとみなされます。 データセット内の個別の記事に関する特定のライセンス情報は、メタデータ ファイルで提供されます。 ライセンス情報についての詳細は、[PMC Web サイト](https://www.ncbi.nlm.nih.gov/pmc/tools/openftlist/)、[medRxiv Web サイト](https://www.medrxiv.org/submit-a-manuscript)、および[bioRxiv Web サイト](https://www.biorxiv.org/about-biorxiv)で利用できます。

## <a name="volume-and-retention"></a>ボリュームとデータ保持期間

このデータセットは JSON 形式で保存されており、最新のリリースには 36,000 を超える全文記事が含まれています。 各記事は単一の JSON オブジェクトとして表されます。 [スキーマを表示します](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)。

## <a name="storage-location"></a>保存先
このデータセットは、米国東部 Azure リージョンに保存されています。 アフィニティのために、米国東部でコンピューティング リソースを割り当てることをお勧めします。

## <a name="citation"></a>引用

出版物や再版物に CORD-19 データを含める場合は、次のようにデータセットを引用してください。

参考文献内:

COVID-19 Open Research Dataset (CORD-19)。 2020 年。 バージョン YYYY-MM-DD。 [COVID-19 Open Research Dataset (CORD-19)](https://pages.semanticscholar.org/coronavirus-research) より取得。 YYYY-MM-DD にアクセス。 doi:10.5281/zenodo.3715505

テキスト内: (CORD-19, 2020)

## <a name="contact"></a>Contact

このデータセットに関するご質問がある場合は、partnerships@allenai.org にお問い合わせください。

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azure-storage"></a>[azure-storage](#tab/azure-storage)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=covid-19-open-research)** 。

## <a name="the-cord-19-dataset"></a>CORD-19 データセット

CORD-19 は、COVID-19、SARS-CoV-2、関連するコロナウイルスなどについて 5 万を超える学術記事を集めたものです。このうち 4 万本は全文記事です。 このデータセットは、COVID-19 パンデミックに挑む研究コミュニティを支援する目的で自由に利用できるようになりました。

このノートブックの目標は 2 つです。

1. Azure の CORD-19 データセットにアクセスする方法を示します。CORD-19 データセットを格納している Azure blob ストレージ アカウントに接続します。
2. データセットの構造を確認します。データセット内の記事は json ファイルとして格納されます。 次の例を示します。

  - 記事を検索する方法 (コンテナー内を移動する)
  - 記事を読む方法 (jacon スキーマ内を移動する)

依存関係: このノートブックには、次のライブラリが必要です。

- Azure ストレージ (例: `pip install azure-storage`)
- NLTK ([説明書](https://www.nltk.org/install.html))
- Pandas (例: `pip install pandas`)

### <a name="getting-the-cord-19-data-from-azure"></a>Azure からの CORD-19 データの取得

CORD-19 データが Azure オープン データセットとして[こちら](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)にアップロードされています。 この CORD-19 オープン データセットにリンクされている BLOB サービスを作成します。


```python
from azure.storage.blob import BlockBlobService

# storage account details
azure_storage_account_name = "azureopendatastorage"
azure_storage_sas_token = "sv=2019-02-02&ss=bfqt&srt=sco&sp=rlcup&se=2025-04-14T00:21:16Z&st=2020-04-13T16:21:16Z&spr=https&sig=JgwLYbdGruHxRYTpr5dxfJqobKbhGap8WUtKFadcivQ%3D"

# create a blob service
blob_service = BlockBlobService(
    account_name=azure_storage_account_name,
    sas_token=azure_storage_sas_token,
)
```

この BLOB サービスは、データのハンドルとして使用できます。 `BlockBlobService` API を活用するデータセットを移動できます。 詳細については次を参照してください。

* [Blob service の概念](/rest/api/storageservices/blob-service-concepts)
* [コンテナーに対する操作](/rest/api/storageservices/operations-on-containers)

CORD-19 データは `covid19temp` コンテナーに保存されます。 これは、コンテナー内のファイル構造とサンプルファイルです。

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

各 .json ファイルは、データセット内の個々のアーティクルに対応します。 ここで、タイトル、作成者、概要、および (使用可能な場合は) 全文データが格納されます。

### <a name="using-metadatacsv"></a>metadata.csv の使用
CORD-19のデータセットには、 CORD-19データセットで利用可能なすべての論文に関する基本情報を記録する 1 つのファイルである `metadata.csv` が付属しています。 これは、Web カメラを探すには便利なサイトです!


```python
# container housing CORD-19 data
container_name = "covid19temp"

# download metadata.csv
metadata_filename = 'metadata.csv'
blob_service.get_blob_to_path(
    container_name=container_name,
    blob_name=metadata_filename,
    file_path=metadata_filename
)
```

```python
import pandas as pd

# read metadata.csv into a dataframe
metadata_filename = 'metadata.csv'
metadata = pd.read_csv(metadata_filename)
```

```python
metadata.head(3)
```

一見して非常に便利なので、もう少し詳しく説明しましょう。

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```

```
There are 51078 many entries in this dataset:
-- 42511 have full text entries
-- 47741 have DOIs
-- 41082 have PubMed Central (PMC) ids
-- 964 have Microsoft Academic paper ids
```

## <a name="example-read-full-text"></a>例: 全文の読み取り

`metadata.csv` には、全文そのものは含まれません。 これを読む方法の例を見てみましょう。 全文の json を見つけて解凍し、文のリストに変換します。

```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pdf_json/{1}.json'.format(example_entry['full_text_file'], example_entry['sha'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)
```

これで、この BLOB に関連付けられている json コンテンツを次のように読み取ることができます。

```python
import json
blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

この例では、次のようにテキストデータを格納する `body_text` に注目します。

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

json の全スキーマは、[こちら](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)で入手できます。


```python
from nltk.tokenize import sent_tokenize

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```    

### <a name="pdf-vs-pmc-xml-parse"></a>PDF と PMC XML 解析

上記の例では、`has_pdf_parse == True` を使用する事例を見ました。 その場合、BLOB ファイルのパスの形式は次のようになります。

```
'<full_text_file>/pdf_json/<sha>.json'
```

代わりに、`has_pmc_xml_parse == True` を使用する事例の場合は、次の形式を使用します。

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

次に例を示します。


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
blob_name = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Full text blob for this entry:")
print(blob_name)

blob_as_json_string = blob_service.get_blob_to_text(container_name=container_name, blob_name=blob_name)
data = json.loads(blob_as_json_string.content)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
```
Full text blob for this entry:
custom_license/pmc_json/PMC546170.xml.json
An example sentence: Double-stranded small interfering RNA (siRNA) molecules have drawn much attention since it was unambiguously shown that they mediate potent gene knock-down in a variety of mammalian cells (1).
```    

## <a name="iterate-through-blobs-directly"></a>BLOB を直接反復処理する

上記の例では、`metadata.csv` ファイルを使用してデータを移動し、BLOB ファイルのパスを作成して、BLOB からデータを読み取ります。 もう 1 つの方法は、BLOB 自体を反復処理することです。


```python
# get and sort list of available blobs
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

これで、BLOB を直接反復処理できるようになりました。 たとえば、使用可能な json ファイルの数を数えてみましょう。


```python
# we can now iterate directly though the blobs
count = 0
for blob in sorted_blobs:
    if blob.name[-5:] == ".json":
        count += 1
print("There are {} many json files".format(count))
```

```
There are 59784 many json files
```  

## <a name="appendix"></a>付録

### <a name="data-quality-issues"></a>データ品質の問題

これは大きなデータセットであり、明確な理由から、急ぎではなくまとめられていました。 ここでは、確認されたいくつかのデータ品質の問題について説明します。

#### <a name="multiple-shas"></a>複数の SHA

特定のエントリに対して複数の SHA が存在する場合があることに注意してください。

```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```
```
There are 1999 many entries with multiple shas
```

## <a name="layout-of-the-container"></a>コンテナーのレイアウト

ここでは、将来更新される場合に備えて、単純な正規表現を使用してコンテナーのファイル構造を調べます。

```python
container_name = "covid19temp"
blobs = blob_service.list_blobs(container_name)
sorted_blobs = sorted(list(blobs), key=lambda e: e.name, reverse=True)
```

```python
import re
dirs = {}

pattern = '([\w]+)\/([\w]+)\/([\w.]+).json'
for blob in sorted_blobs:
    
    m = re.match(pattern, blob.name)
    
    if m:
        dir_ = m[1] + '/' + m[2]
        
        if dir_ in dirs:
            dirs[dir_] += 1
        else:
            dirs[dir_] = 1
        
dirs
```

<!-- nbend -->

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azureml-opendatasets&registryId=covid-19-open-research)** 。

## <a name="the-cord-19-dataset"></a>CORD-19 データセット

CORD-19 は、COVID-19、SARS-CoV-2、関連するコロナウイルスなどについて 5 万を超える学術記事を集めたものです。このうち 4 万本は全文記事です。 このデータセットは、COVID-19 パンデミックに挑む研究コミュニティを支援する目的で自由に利用できるようになりました。

このノートブックの目標は 2 つです。

1. Azure の CORD-19 データセットにアクセスする方法を示します。 AzureML データセットを使用して、CORD-19 データのコンテキストを提供します。
2. データセットの構造を確認します。データセット内の記事は json ファイルとして格納されます。 次の例を示します。

  - 記事を検索する方法 (ディレクトリ構造内の移動)
  - 記事を読む方法 (jacon スキーマ内を移動する)

依存関係: このノートブックには、次のライブラリが必要です。

- AzureML Python SDK (例: `pip install --upgrade azureml-sdk`)
- Pandas (例: `pip install pandas`)
- NLTK ([説明書](https://www.nltk.org/install.html)) (例: `pip install nltk`)

NLTK に `punkt` パッケージがない場合、次を実行する必要があります。

```
import nltk
nltk.download('punkt')
```

### <a name="getting-the-cord-19-data-from-azure"></a>Azure からの CORD-19 データの取得

CORD-19 データが Azure オープン データセットとして[こちら](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)にアップロードされています。 このノートブックでは、AzureML [データセット](/python/api/azureml-core/azureml.core.dataset.dataset)を使用して CORD-19 オープン データセットを参照します。

```python
import azureml.core
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

```python
from azureml.core import  Dataset
cord19_dataset = Dataset.File.from_files('https://azureopendatastorage.blob.core.windows.net/covid19temp')
mount = cord19_dataset.mount()
```

`mount()` メソッドは、データセットによって定義されたファイル システム ストリームをローカル ファイルとしてマウントするためのコンテキスト マネージャーを作成します。

`mount.start()` および `mount.stop()` を使用するか、代わりに `with mount():` を使用してコンテキストを管理します。

マウントは、Unix または Unix 系オペレーティング システムでのみサポートされており、libfuse が存在している必要があります。 Docker コンテナー内で実行している場合は、docker コンテナーを `--privileged` フラグで開始するか、`--cap-add SYS_ADMIN --device /dev/fuse` で開始する必要があります。 詳細については、[説明書](/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py#mount-mount-point-none----kwargs-&preserve-view=true)をご覧ください


```python
import os

COVID_DIR = '/covid19temp'
path = mount.mount_point + COVID_DIR

with mount:
    print(os.listdir(path))
```
```
['antiviral_with_properties_compressed.sdf', 'biorxiv_medrxiv', 'biorxiv_medrxiv_compressed.tar.gz', 'comm_use_subset', 'comm_use_subset_compressed.tar.gz', 'custom_license', 'custom_license_compressed.tar.gz', 'metadata.csv', 'noncomm_use_subset', 'noncomm_use_subset_compressed.tar.gz']
```

これは、CORD-19 データセット内のファイル構造とサンプル ファイルです。

```
metadata.csv
custom_license/
    pdf_json/
        0001418189999fea7f7cbe3e82703d71c85a6fe5.json        # filename is sha-hash
        ...
    pmc_json/
        PMC1065028.xml.json                                  # filename is the PMC ID
        ...
noncomm_use_subset/
    pdf_json/
        0036b28fddf7e93da0970303672934ea2f9944e7.json
        ...
    pmc_json/
        PMC1616946.xml.json
        ...
comm_use_subset/
    pdf_json/
        000b7d1517ceebb34e1e3e817695b6de03e2fa78.json
        ...
    pmc_json/
        PMC1054884.xml.json
        ...
biorxiv_medrxiv/                                             # note: there is no pmc_json subdir
    pdf_json/
        0015023cc06b5362d332b3baf348d11567ca2fbb.json
        ...
```

各 .json ファイルは、データセット内の個々のアーティクルに対応します。 ここで、タイトル、作成者、概要、および (使用可能な場合は) 全文データが格納されます。

## <a name="using-metadatacsv"></a>metadata.csv の使用

CORD-19のデータセットには、 CORD-19データセットで利用可能なすべての論文に関する基本情報を記録する 1 つのファイルである `metadata.csv` が付属しています。 これは、Web カメラを探すには便利なサイトです!


```python
import pandas as pd

# create mount context
mount.start()

# specify path to metadata.csv
COVID_DIR = 'covid19temp'
metadata_filename = '{}/{}/{}'.format(mount.mount_point, COVID_DIR, 'metadata.csv')

# read metadata
metadata = pd.read_csv(metadata_filename)
metadata.head(3)
```

```python
simple_schema = ['cord_uid', 'source_x', 'title', 'abstract', 'authors', 'full_text_file', 'url']

def make_clickable(address):
    '''Make the url clickable'''
    return '<a href="{0}">{0}</a>'.format(address)

def preview(text):
    '''Show only a preview of the text data.'''
    return text[:30] + '...'

format_ = {'title': preview, 'abstract': preview, 'authors': preview, 'url': make_clickable}

metadata[simple_schema].head().style.format(format_)
```

```python
# let's take a quick look around
num_entries = len(metadata)
print("There are {} many entries in this dataset:".format(num_entries))

metadata_with_text = metadata[metadata['full_text_file'].isna() == False]
with_full_text = len(metadata_with_text)
print("-- {} have full text entries".format(with_full_text))

with_doi = metadata['doi'].count()
print("-- {} have DOIs".format(with_doi))

with_pmcid = metadata['pmcid'].count()
print("-- {} have PubMed Central (PMC) ids".format(with_pmcid))

with_microsoft_id = metadata['Microsoft Academic Paper ID'].count()
print("-- {} have Microsoft Academic paper ids".format(with_microsoft_id))
```    

### <a name="example-read-full-text"></a>例: 全文の読み取り

`metadata.csv` には、全文そのものは含まれません。 これを読む方法の例を見てみましょう。 全文の json を見つけて解凍し、文のリストに変換します。


```python
# choose a random example with pdf parse available
metadata_with_pdf_parse = metadata[metadata['has_pdf_parse']]
example_entry = metadata_with_pdf_parse.iloc[42]

# construct path to blob containing full text
filepath = '{0}/{1}/pdf_json/{2}.json'.format(path, example_entry['full_text_file'], example_entry['sha'])
print("Full text filepath:")
print(filepath)
```    

これで、このファイルに関連付けられている json コンテンツを次のように読み取ることができます。

```python
import json

try:
    with open(filepath, 'r') as f:
        data = json.load(f)
except FileNotFoundError as e:
    # in case the mount context has been closed
    mount.start()
    with open(filepath, 'r') as f:
        data = json.load(f)
        
# in addition to the body text, the metadata is also stored within the individual json files
print("Keys within data:", ', '.join(data.keys()))
```

```
Keys within data: paper_id, metadata, abstract, body_text, bib_entries, ref_entries, back_matter
```

この例では、次のようにテキストデータを格納する `body_text` に注目します。

```json
"body_text": [                      # list of paragraphs in full body
    {
        "text": <str>,
        "cite_spans": [             # list of character indices of inline citations
                                    # e.g. citation "[7]" occurs at positions 151-154 in "text"
                                    #      linked to bibliography entry BIBREF3
            {
                "start": 151,
                "end": 154,
                "text": "[7]",
                "ref_id": "BIBREF3"
            },
            ...
        ],
        "ref_spans": <list of dicts similar to cite_spans>,     # e.g. inline reference to "Table 1"
        "section": "Abstract"
    },
    ...
]
```

[JSON フル スキーマ](https://ai2-semanticscholar-cord-19.s3-us-west-2.amazonaws.com/2020-03-13/json_schema.txt)を表示します。


```python
from nltk.tokenize import sent_tokenize
# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```
    

#### <a name="pdf-vs-pmc-xml-parse"></a>PDF と PMC XML 解析

上記の例では、`has_pdf_parse == True` を使用する事例を見ました。 その場合、ファイルのパスの形式は次のようになります。

```
'<full_text_file>/pdf_json/<sha>.json'
```

代わりに、`has_pmc_xml_parse == True` を使用する事例の場合は、次の形式を使用します。

```
'<full_text_file>/pmc_json/<pmcid>.xml.json'
```

次に例を示します。


```python
# choose a random example with pmc parse available
metadata_with_pmc_parse = metadata[metadata['has_pmc_xml_parse']]
example_entry = metadata_with_pmc_parse.iloc[42]

# construct path to blob containing full text
filename = '{0}/pmc_json/{1}.xml.json'.format(example_entry['full_text_file'], example_entry['pmcid'])  # note the repetition in the path
print("Path to file: {}\n".format(filename))

with open(mount.mount_point + '/' + COVID_DIR + '/' + filename, 'r') as f:
    data = json.load(f)

# the text itself lives under 'body_text'
text = data['body_text']

# many NLP tasks play nicely with a list of sentences
sentences = []
for paragraph in text:
    sentences.extend(sent_tokenize(paragraph['text']))

print("An example sentence:", sentences[0])
```

## <a name="appendix"></a>付録

### <a name="data-quality-issues"></a>データ品質の問題

これは大きなデータセットであり、明確な理由から、急ぎではなくまとめられていました。 ここでは、確認されたいくつかのデータ品質の問題について説明します。


```python
metadata_multiple_shas = metadata[metadata['sha'].str.len() > 40]

print("There are {} many entries with multiple shas".format(len(metadata_multiple_shas)))

metadata_multiple_shas.head(3)
```

<!-- nbend -->

---

## <a name="next-steps"></a>次のステップ

[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットを表示します。