---
title: Table API による Azure Cosmos DB グローバル分散のチュートリアル
description: Azure Cosmos DB Table API アカウントにおけるグローバル分散の動作とリージョンの優先リストを構成する方法について説明します
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: 627086bdb13acdd29821af399f90fee8deaae432
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900189"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Table API を使用して Azure Cosmos DB グローバル分散を設定する

この記事に含まれるタスクは次のとおりです。 

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * [Table API](table-introduction.md) を使用してグローバル分散を構成する

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Table API を使用して優先リージョンに接続する

[グローバル分散](distribute-data-globally.md)を活用するためには、クライアント アプリケーションがその現在の実行場所を指定する必要があります。 これは `CosmosExecutorConfiguration.CurrentRegion` プロパティを設定することによって行います。 `CurrentRegion` プロパティには、場所が 1 つだけ格納されている必要があります。 クライアントのインスタンスごとに異なるリージョンを指定して、読み取りの遅延を抑えることができます。 リージョンの指定には、その[表示名](https://msdn.microsoft.com/library/azure/gg441293.aspx) ("米国西部" など) を使用する必要があります。 

Azure Cosmos DB Table API SDK は、アカウント構成と現在のリージョンの可用性に基づいて、通信に最適なエンドポイントを自動的に選定します。 クライアントの待ち時間を短くするために、最も近いリージョンが優先されます。 現在の `CurrentRegion` プロパティを設定すると、読み取り要求と書き込み要求が次のように誘導されます。

* **読み取り要求:** 読み取り要求はすべて、構成済みの `CurrentRegion` に送信されます。 高可用性を目的に geo レプリケーションされるフォールバック リージョンは、SDK が近接関係に基づいて自動的に選択します。

* **書き込み要求:** SDK は自動的に、すべての書き込み要求を現在の書き込みリージョンに送信します。 マルチ マスター アカウントの場合も、現在のリージョンが書き込み要求を処理します。 高可用性を目的に geo レプリケーションされるフォールバック リージョンは、SDK が近接関係に基づいて自動的に選択します。

`CurrentRegion` プロパティを指定しなかった場合、現在の書き込みリージョンがすべての操作に使用されます。

たとえば、Azure Cosmos アカウントが "米国西部" リージョンと "米国東部" リージョンにあるとします。 "米国西部" が書き込みリージョンで、アプリケーションは "米国東部" に存在するとします。 CurrentRegion プロパティが構成されていない場合、すべての読み取り要求と書き込み要求は常に "米国西部" リージョンに誘導されます。 CurrentRegion プロパティが構成されている場合は、読み取り要求はすべて "米国東部" リージョンで処理されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、次の手順を行いました。

> [!div class="checklist"]
> * Azure Portal を使用してグローバル分散を構成する
> * Azure Cosmos DB Table API を使用してグローバル分散を構成する

