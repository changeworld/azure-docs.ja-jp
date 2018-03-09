---
title: "Azure Machine Learning データ準備で可能な追加ソース データベース接続の例 | Microsoft Docs"
description: "このドキュメントでは、Azure Machine Learning データ準備ツールで可能なソース データの接続の例を示します"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 27d72f1b49a89929098c0cc35f92d3915fc8b0b9
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2018
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

「データフローの変換 (スクリプト)」変換を作成します。 その後、次のスクリプトを使用して data.world からデータを読み込みます。

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>データ ソース接続としての Azure Cosmos DB
データ接続としての Azure Cosmos DB の例については、[ソース データ接続としての Azure Cosmos DB の読み込み](data-prep-load-azure-cosmos-db.md)に関するページを参照してください。
