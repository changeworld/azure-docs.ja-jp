---
title: MongoDB API の Azure Cosmos DB グローバル分散チュートリアル | Microsoft Docs
description: MongoDB API を使用して Azure Cosmos DB グローバル分散を設定する方法について説明します。
services: cosmos-db
keywords: グローバル分散, MongoDB
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: 1885c979fe2532d26b2e7b59111675bebee8ec05
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38668090"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-mongodb-api"></a>MongoDB API を使用して Azure Cosmos DB グローバル分散を設定する

この記事では、Azure Portal を使用して Azure Cosmos DB グローバル分散をセットアップし、MongoDB API を使用して接続する方法を説明します。

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * [MongoDB API](mongodb-introduction.md) を使用してグローバル分散を構成する

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup-using-the-mongodb-api"></a>MongoDB API を使用してリージョン設定を検証する
MongoDB 用 API 内のグローバル構成を再確認する最も簡単な方法は、Mongo シェルから *isMaster()* コマンドを実行することです。

Mongo シェルから次のコマンドを実行します。

   ```
      db.isMaster()
   ```
   
結果の例:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region-using-the-mongodb-api"></a>MongoDB API を使用して優先リージョンに接続する

MongoDB API では、グローバル分散データベースのコレクションの読み取り設定を指定することができます。 待機時間の短い読み取りとグローバルな高可用性の両方を得るために、コレクションの読み取り設定には *Nearest* を設定することをお勧めします。 *Nearest* 読み取り設定は、最寄りのリージョンから読み取るように構成します。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

プライマリ読み取り/書き込みリージョンとディザスター リカバリー (DR) 用のセカンダリ リージョンを使用するアプリケーションでは、コレクションの読み取り設定を *SecondaryPreferred* に設定することをお勧めします。 *SecondaryPreferred* 読み取り設定は、プライマリ リージョンが利用できない場合、セカンダリ リージョンから読み取るように構成します。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

最後に、読み取りリージョンを手動で指定する場合について説明します。 リージョン タグは、読み取り設定で設定できます。

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

このチュートリアルはこれで終わりです。 [Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事を読んで、グローバルにレプリケートされたアカウントの整合性を管理する方法について確認できます。 また、Azure Cosmos DB におけるグローバル データベース レプリケーションの動作の詳細については、[Azure Cosmos DB を使用したデータのグローバル分散](distribute-data-globally.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * SQL API を使用してグローバル分散を構成する

これで次のチュートリアルに進むことができます。Azure Cosmos DB ローカル エミュレーターを使用してローカルで開発する方法について学びます。

> [!div class="nextstepaction"]
> [エミュレーターを使用したローカル開発](local-emulator.md)
