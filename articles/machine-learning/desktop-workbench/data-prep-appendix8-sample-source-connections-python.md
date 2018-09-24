---
title: Azure Machine Learning データ準備で可能な追加ソース データベース接続の例 | Microsoft Docs
description: このドキュメントでは、Azure Machine Learning データ準備ツールで可能なソース データの接続の例を示します
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ROBOTS: NOINDEX
ms.openlocfilehash: ed0e6bc4c506535fcb679fc2b4015a61274a77f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967695"
---
# <a name="sample-of-custom-source-connections-python"></a>カスタム ソース接続のサンプル (Python) 

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


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
