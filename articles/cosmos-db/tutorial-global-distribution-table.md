---
title: "Table API の Azure Cosmos DB グローバル分散チュートリアル | Microsoft Docs"
description: "Table API を使用して Azure Cosmos DB グローバル分散をセットアップする方法について説明します。"
services: cosmos-db
keywords: "グローバル分散, Table"
documentationcenter: 
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: a1682ef88760da39d33fac2cc9f34e0e66ea19fb
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-table-api"></a>Table API を使用して Azure Cosmos DB グローバル分散をセットアップする方法

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * [Table API](table-introduction.md) を使用してグローバル分散を構成する

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Table API を使用して優先リージョンに接続する

[グローバル配布](distribute-data-globally.md)を活用するために、クライアント アプリケーションでは、ドキュメントの操作の実行に使用するリージョンの順序付き優先リストを指定できます。 これを行うには、Azure Cosmos DB Table API SDK の app.config で `TablePreferredLocations` 構成値を設定します。 Azure Cosmos DB Table API SDK はアカウント構成、現在のリージョンの利用可能性、および指定されている設定一覧に基づいて、通信に最適なエンドポイントを選定します。

`TablePreferredLocations` に読み取りの優先場所 (マルチホーム) をコンマで区切って指定してください。 各クライアント インスタンスは、低待機時間で読み取りを行えるように、優先順位を付けてこれらのリージョンのサブセットを指定できます。 リージョンは[表示名](https://msdn.microsoft.com/library/azure/gg441293.aspx) (`West US` など) を使用して指定する必要があります。

すべての読み取りは、`TablePreferredLocations` リストの最初の利用可能なリージョンに送信されます。 要求が失敗すると、クライアントはリストにある次のリージョンを試します。これが繰り返されます。

SDK は、`TablePreferredLocations` で指定されたリージョンからの読み取りを試みます。 このため、3 つのリージョンでデータベース アカウントが利用できるものの、クライアントが `TablePreferredLocations` の非書き込みリージョンを 2 つだけ指定している場合などには、書き込みリージョンの外で読み取りが処理されません。これはフェールオーバーの場合にもあてはまります。

SDK は、すべての書き込みを現在の書き込みリージョンに自動的に送信します。

`TablePreferredLocations` プロパティが設定されていない場合、すべての要求が現在の書き込みリージョンから処理されます。

```xml
    <appSettings>
      <add key="TablePreferredLocations" value="East US, West US, North Europe"/>           
    </appSettings>
```

このチュートリアルはこれで終わりです。 [Azure Cosmos DB の一貫性レベル](consistency-levels.md)に関する記事を読んで、グローバルにレプリケートされたアカウントの整合性を管理する方法について確認できます。 また、Azure Cosmos DB におけるグローバル データベース レプリケーションの動作の詳細については、[Azure Cosmos DB を使用したデータのグローバル分散](distribute-data-globally.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * Azure Cosmos DB Table API を使用してグローバル分散を構成する

