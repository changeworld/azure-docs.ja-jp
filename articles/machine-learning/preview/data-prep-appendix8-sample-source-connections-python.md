---
title: "Azure Machine Learning データ準備で可能な追加ソース データベース接続の例 | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning データ準備ツールで可能なソース データの接続の例を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 3ffe0e385f9dd71d8341305f42ceb10e0ea49af4
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/18/2017
---
# <a name="sample-of-custom-source-connections-python"></a>カスタム ソース接続のサンプル (Python) 
この付録を読む前に、[Python 機能拡張の概要](data-prep-python-extensibility-overview.md)に関する記事をご覧ください。

## <a name="load-data-from-dataworld"></a>data.world からデータを読み込む

### <a name="prerequisites"></a>前提条件

#### <a name="register-yourself-at-dataworld"></a>data.world に登録します
data.world Web サイトからの API トークンが必要です。

#### <a name="install-dataworld-library"></a>data.world ライブラリをインストールする

**[ファイル]** > **[Open command-line interface]\(コマンド ライン インターフェイスを開く\)** を選択して、Azure Machine Learning ワークベンチのコマンド ライン インターフェイスを開きます。

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

次に、コマンド ラインで `dw configure` を実行します。トークンが求められます。 トークンを入力すると、ホーム ディレクトリに .dw/ ディレクトリが作成され、トークンがそこに格納されます。

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
これで data.world ライブラリをインポートすることができます。

#### <a name="load-data-into-data-preparation"></a>データ準備にデータを読み込む

新しいスクリプト ベースのデータ フローを作成します。 その後、次のスクリプトを使用して data.world からデータを読み込みます。

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>データ準備に Azure Cosmos DB データを読み込む

新しいスクリプト ベースのデータ フローを作成し、次のスクリプトを使用して、Azure Cosmos DB からデータを読み込みます  (先にライブラリをインストールする必要があります。 詳しくは、前述のリンク先の参照ドキュメントをご覧ください)。

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```
