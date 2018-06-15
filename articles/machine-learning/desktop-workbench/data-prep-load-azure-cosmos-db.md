---
title: Azure Machine Learning Workbench で Azure Cosmos DB をデータ ソースとして接続する | Microsoft Docs
description: このドキュメントでは、Azure Machine Learning Workbench 経由で Azure Cosmos DB に接続する方法の例について説明します。
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 20e23f41310b90c62eacb7279ea3da0eec376683
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/07/2018
ms.locfileid: "34830692"
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Azure Cosmos DB をデータ ソースとして接続する
この記事には、Azure Machine Learning Workbench で Cosmos DB に接続できる Python サンプルが含まれています。

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>データ準備に Azure Cosmos DB データを読み込む

新しいスクリプト ベースのデータ フローを作成し、次のスクリプトを使用して、Azure Cosmos DB からデータを読み込みます  

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

## <a name="other-data-source-connections"></a>他のデータ ソース接続
その他のサンプルについては、[追加のソース データ接続のサンプル](data-prep-appendix8-sample-source-connections-python.md)に関するページを参照してください。
