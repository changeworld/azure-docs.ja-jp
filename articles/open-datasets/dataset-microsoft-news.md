---
title: Microsoft News Recommendation Dataset
titleSuffix: Azure Open Datasets
description: Azure Open Datasets で Microsoft News Recommendation Dataset を使用する方法について説明します。
ms.service: open-datasets
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 04/16/2021
ms.openlocfilehash: 29a7865a06fd96ce4d27892775fb5af728633a02
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982476"
---
# <a name="microsoft-news-recommendation"></a>Microsoft News Recommendation

**Mi** crosoft **N** ews **D** ataset (MIND) は、ニュース推奨研究のための大規模なデータセットです。 Microsoft News の Web サイトの匿名化された動作ログから収集されました。 MIND の使命は、ニュース推奨のためにベンチマーク データセットの役割を果たし、ニュース推奨と推奨システムの分野での研究を支援することです。

MIND には約 16 万件の英語ニュース記事と、100 万人のユーザーによって生成された 1,500 万件を超えるインプレッション ログが含まれています。 各ニュース記事には、タイトル、要約、本文、カテゴリ、エンティティを含む、情報に富んだテキスト コンテンツが含まれます。 各インプレッション ログには、クリック イベント、非クリック イベント、このインプレッションより前のこのユーザーによるニュース クリック動作の履歴が含まれています。 ユーザーのプライバシーを保護するため、ユーザーが匿名化された ID に安全にハッシュされた時点で、各ユーザーは運用システムから切り離されます。 MIND データセットに関する詳細情報については、「[MIND:A Large-scale Dataset for News Recommendation (MIND: ニュース推奨のための大規模なデータセット)](https://msnews.github.io/assets/doc/ACL2020_MIND.pdf)」という資料をご覧ください。
 
## <a name="volume"></a>ボリューム

トレーニングおよび検証データの両方は zip 圧縮されたフォルダーで、それぞれには次の 4 種類のファイルが含まれています。

| ファイル名 | Description |
|-|-|
| behaviors.tsv | ユーザーのクリック履歴とインプレッション ログ |
| news.tsv | ニュース記事の情報 |
| entity_embedding.vec | ナレッジ グラフから抽出したニュースに含まれるエンティティの埋め込み |
| relation_embedding.vec | ナレッジ グラフから抽出したエンティティ間の関係の埋め込み |

### <a name="behaviorstsv"></a>behaviors.tsv

behaviors.tsv ファイルには、インプレッション ログとユーザーのニュースのクリック履歴が含まれています。 タブ記号で区切った次の 5 つの列があります。

- インプレッション ID。 インプレッションの ID。
- ユーザー ID。 ユーザーの匿名 ID。
- 時間。 "MM/DD/YYYY HH:MM:SS AM/PM" という形式のインプレッション時刻。
- History。 このユーザーの、このインプレッションより前のニュース クリック履歴 (クリックしたニュースの ID リスト)。
- インプレッション。 このインプレッションに表示されたニュースのリストと、それらに対するユーザーのクリック動作 (クリックした場合は 1、クリックしなかった場合は 0)。

下のテーブルに例を示します。

| COLUMN | CONTENT |
|-|-|
| インプレッション ID | 123 |
| User ID | U131 |
| 時間 | 11/13/2019 8:36:57 AM |
| 履歴 | N11 N21 N103 |
| インプレッション数 | N4-1 N34-1 N156-0 N207-0 N198-0 |

### <a name="newstsv"></a>news.tsv

news.tsv ファイルには、behavior.tsv ファイルに関連するニュース記事の詳細情報が含まれています。 タブ記号で区切った次の 7 つの列があります。

- ニュース ID
- カテゴリ
- サブカテゴリ
- タイトル
- 要約
- URL
- タイトル エンティティ (このニュースのタイトルに含まれているエンティティ)
- 要約エンティティ (このニュースの要約に含まれているエンティティ)

ライセンスの構造のため、MSN ニュース記事コンテンツの完全な本文はダウンロードできません。 ただし、便宜を図るため、データセット内の MSN URL からニュースの Web ページを解析するために役立つ[ユーティリティ スクリプト](https://github.com/msnews/MIND/tree/master/crawler)をご用意しました。 時間制限のため、一部の URL は期限が切れて正常にアクセスできないことがあります。 現在、この問題の解決に向けて作業中です。

次のテーブルに例を示します。

| COLUMN | CONTENT |
|-|-|
| ニュース ID | N37378 |
| カテゴリ | スポーツ |
| サブカテゴリ | ゴルフ |
| タイトル | PGA ツアーの優勝者 |
| 要約 | PGA ツアーの最近の優勝者ギャラリー。 |
| URL | https://www.msn.com/en-us/sports/golf/pga-tour-winners/ss-AAjnQjj?ocid=chopendata |
| タイトル エンティティ | [{"Label": "PGA Tour", "Type": "O", "WikidataId": "Q910409", "Confidence": 1.0, "OccurrenceOffsets": [0], "SurfaceForms": ["PGA Tour"]}] |
| 要約エンティティ | [{"Label": "PGA Tour", "Type": "O", "WikidataId": "Q910409", "Confidence": 1.0, "OccurrenceOffsets": [35], "SurfaceForms": ["PGA Tour"]}] |

"エンティティ" 列のディクショナリ キーの説明は次のとおりです。

| キー | Description |
|-|-|
| Label | Wikidata のナレッジ グラフでのエンティティ名 |
| Type | Wikidata でのこのエンティティの種類 |
| WikidataId | Wikidata でのエンティティ ID |
| Confidence | エンティティのリンクの信頼度 |
| OccurrenceOffsets | タイトルまたは要約のテキストにおけるエンティティの文字レベルのオフセット |
| SurfaceForms | 元のテキストにおける未加工のエンティティ名 |

### <a name="entity_embeddingvec--relation_embeddingvec"></a>entity_embedding.vec と relation_embedding.vec

entity_embedding.vec と relation_embedding.vec の各ファイルには、TransE メソッドによりサブグラフ (WikiData ナレッジ グラフ) から学習されたエンティティと関係の 100 次元の埋め込みが含まれています。 どちらのファイルでも、最初の列はエンティティまたは関係の ID で、それ以外の列は埋め込みのベクトル値です。 このデータが、ナレッジ対応のニュース推奨の研究でお役に立てば幸いです。 例を次に示します。

| ID | 埋め込みの値 |
|-|-|
| Q42306013 | 0.014516 -0.106958 0.024590 … -0.080382 |

サブグラフからの埋め込みの学習に伴う何かの理由で、ごく一部のエンティティについて entity_embedding.vec ファイルに埋め込みが含まれていないことがあります。

## <a name="storage-location"></a>保存先

データは、米国西部または米国東部データ センターにある BLOB の、次の BLOB コンテナーに格納されます: "https://mind201910small.blob.core.windows.net/release/"。

コンテナー内には、トレーニング セットと検証セットがそれぞれ MINDlarge_train.zip と MINDlarge_dev.zip に圧縮されています。

## <a name="additional-information"></a>関連情報

MIND データセットは、[Microsoft Research ライセンス条項](https://github.com/msnews/MIND/blob/master/MSR%20License_Data.pdf)の下で、研究目的のために無料でダウンロードできます。 データセットについて不明な点がある場合は、mind@microsoft.com にお問い合わせください。

## <a name="data-access"></a>データ アクセス

### <a name="azure-notebooks"></a>Azure Notebooks

# <a name="azureml-opendatasets"></a>[azureml-opendatasets](#tab/azureml-opendatasets)

<!-- nbstart https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset -->

> [!TIP]
> **[代わりにノートブックをダウンロードします](https://opendatasets-api.azure.com/discoveryapi/OpenDataset/DownloadNotebook?serviceType=AzureNotebooks&package=azure-storage&registryId=microsoft-news-dataset)** 。

## <a name="demo-notebook-for-accessing-mind-data-on-azure"></a>Azure 上の MIND データにアクセスするためのデモ ノートブック

このノートブックは、Azure 上の BLOB ストレージから MIND データにアクセスする例を示すものです。

MIND データは米国西部または米国東部のデータ センターに格納されているため、このノートブックは米国西部または米国東部にある Azure コンピューティング上でより効率的に実行されます。

### <a name="imports-and-environment"></a>インポートと環境

```python
import os
import tempfile
import shutil
import urllib
import zipfile
import pandas as pd

# Temporary folder for data we need during execution of this notebook (we'll clean up
# at the end, we promise)
temp_dir = os.path.join(tempfile.gettempdir(), 'mind')
os.makedirs(temp_dir, exist_ok=True)

# The dataset is split into training and validation set, each with a large and small version.
# The format of the four files are the same.
# For demonstration purpose, we will use small version validation set only.
base_url = 'https://mind201910small.blob.core.windows.net/release'
training_small_url = f'{base_url}/MINDsmall_train.zip'
validation_small_url = f'{base_url}/MINDsmall_dev.zip'
training_large_url = f'{base_url}/MINDlarge_train.zip'
validation_large_url = f'{base_url}/MINDlarge_dev.zip'
```

### <a name="functions"></a>関数


```python
def download_url(url,
                 destination_filename=None,
                 progress_updater=None,
                 force_download=False,
                 verbose=True):
    """
    Download a URL to a temporary file
    """
    if not verbose:
        progress_updater = None
    # This is not intended to guarantee uniqueness, we just know it happens to guarantee
    # uniqueness for this application.
    if destination_filename is None:
        url_as_filename = url.replace('://', '_').replace('/', '_')
        destination_filename = \
            os.path.join(temp_dir,url_as_filename)
    if (not force_download) and (os.path.isfile(destination_filename)):
        if verbose:
            print('Bypassing download of already-downloaded file {}'.format(
                os.path.basename(url)))
        return destination_filename
    if verbose:
        print('Downloading file {} to {}'.format(os.path.basename(url),
                                                 destination_filename),
              end='')
    urllib.request.urlretrieve(url, destination_filename, progress_updater)
    assert (os.path.isfile(destination_filename))
    nBytes = os.path.getsize(destination_filename)
    if verbose:
        print('...done, {} bytes.'.format(nBytes))
    return destination_filename
```

### <a name="download-and-extract-the-files"></a>ファイルのダウンロードと抽出

```python
# For demonstration purpose, we will use small version validation set only.
# This file is about 30MB.
zip_path = download_url(validation_small_url, verbose=True)
with zipfile.ZipFile(zip_path, 'r') as zip_ref:
    zip_ref.extractall(temp_dir)

os.listdir(temp_dir)
```

### <a name="read-the-files-with-pandas"></a>pandas を使用してファイルを読み取る

```python
# The behaviors.tsv file contains the impression logs and users' news click histories. 
# It has 5 columns divided by the tab symbol:
# - Impression ID. The ID of an impression.
# - User ID. The anonymous ID of a user.
# - Time. The impression time with format "MM/DD/YYYY HH:MM:SS AM/PM".
# - History. The news click history (ID list of clicked news) of this user before this impression.
# - Impressions. List of news displayed in this impression and user's click behaviors on them (1 for click and 0 for non-click).
behaviors_path = os.path.join(temp_dir, 'behaviors.tsv')
pd.read_table(
    behaviors_path,
    header=None,
    names=['impression_id', 'user_id', 'time', 'history', 'impressions'])
```

```python
# The news.tsv file contains the detailed information of news articles involved in the behaviors.tsv file.
# It has 7 columns, which are divided by the tab symbol:
# - News ID
# - Category
# - Subcategory
# - Title
# - Abstract
# - URL
# - Title Entities (entities contained in the title of this news)
# - Abstract Entities (entities contained in the abstract of this news)
news_path = os.path.join(temp_dir, 'news.tsv')
pd.read_table(news_path,
              header=None,
              names=[
                  'id', 'category', 'subcategory', 'title', 'abstract', 'url',
                  'title_entities', 'abstract_entities'
              ])
```

```python
# The entity_embedding.vec file contains the 100-dimensional embeddings
# of the entities learned from the subgraph by TransE method.
# The first column is the ID of entity, and the other columns are the embedding vector values.
entity_embedding_path = os.path.join(temp_dir, 'entity_embedding.vec')
entity_embedding = pd.read_table(entity_embedding_path, header=None)
entity_embedding['vector'] = entity_embedding.iloc[:, 1:101].values.tolist()
entity_embedding = entity_embedding[[0,
                                     'vector']].rename(columns={0: "entity"})
entity_embedding
```

```python
# The relation_embedding.vec file contains the 100-dimensional embeddings
# of the relations learned from the subgraph by TransE method.
# The first column is the ID of relation, and the other columns are the embedding vector values.
relation_embedding_path = os.path.join(temp_dir, 'relation_embedding.vec')
relation_embedding = pd.read_table(relation_embedding_path, header=None)
relation_embedding['vector'] = relation_embedding.iloc[:,
                                                       1:101].values.tolist()
relation_embedding = relation_embedding[[0, 'vector'
                                         ]].rename(columns={0: "relation"})
relation_embedding
```

### <a name="clean-up-temporary-files"></a>一時ファイルをクリーンアップする

```python
shutil.rmtree(temp_dir)
```

<!-- nbend -->

---

## <a name="examples"></a>例

Microsoft News Recommender データセットの使用方法については、次の例を参照してください。

- [MIND のニュース推奨の課題](https://www.microsoft.com/research/?post_type=msr-academic-program&p=676692&secret=hCWU3V)
- [Microsoft Recommender のリポジトリ](https://github.com/microsoft/recommenders)
- [MIND: Microsoft News Dataset](https://msnews.github.io/index.html)


## <a name="next-steps"></a>次のステップ

[Microsoft Recommender のリポジトリ](https://github.com/microsoft/recommenders)から MIND に基づいて開発されたいくつかのベースライン ニュース推奨モデルを確認します


[Open Datasets カタログ](dataset-catalog.md)の残りのデータセットについて確認します。
