---
title: Azure Cosmos DB 統合キャッシュを構成する方法
description: Azure Cosmos DB 統合キャッシュを構成する方法について説明します
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/28/2021
ms.author: tisande
ms.openlocfilehash: 251f534669333c17a4bb408a23b33f73c79c9e9f
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129229709"
---
# <a name="how-to-configure-the-azure-cosmos-db-integrated-cache-preview"></a>Azure Cosmos DB 統合キャッシュを構成する方法 (プレビュー)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、専用ゲートウェイをプロビジョニングし、統合キャッシュを構成し、アプリケーションを接続する方法について説明します。 

## <a name="prerequisites"></a>前提条件

- [Azure サブスクリプション](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)をお持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- Azure Cosmos DB を使用する既存のアプリケーション。 これがない場合、[こちらのいくつかサンプルがあります](https://github.com/AzureCosmosDB/labs)。
- 既存の [Azure Cosmos DB SQL (コア) API アカウント](create-cosmosdb-resources-portal.md)。

## <a name="provision-a-dedicated-gateway-cluster"></a>専用ゲートウェイ クラスターをプロビジョニングする

1. Azure portal で Azure Cosmos DB アカウントに移動し、 **[専用ゲートウェイ]** タブを選択します。

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" alt-text="[専用ゲートウェイ] タブに移動する方法を示す画像" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" border="false":::

2. **[専用ゲートウェイ]** フォームに、次の詳細を入力します。

   * **[専用ゲートウェイ]** - トグルを **[プロビジョニング済み]** に切り替えます。 
   * **[SKU]** - 必要なコンピューティングおよびメモリ サイズを持つ SKU を選択します。 
   *  **[インスタンスの数]** - ノードの数。 開発目的の場合、D4 サイズの 1 つのノードから開始することをお勧めします。 最初のテスト後、キャッシュする必要があるデータの量に基づいて、ノード サイズを大きくすることができます。

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" alt-text="専用ゲートウェイ クラスターを作成するためのサンプル入力設定を示す画像" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" border="false":::

3. **[保存]** を選択し、専用ゲートウェイのプロビジョニングが完了するまで約 5 から 10 分待機します。 プロビジョニングが完了すると、次の通知が表示されます。

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" alt-text="専用ゲートウェイのプロビジョニングが完了したかどうか確認する方法を示す画像" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" border="false":::

## <a name="configuring-the-integrated-cache"></a>統合キャッシュを構成する

1. 専用ゲートウェイを作成すると、統合キャッシュが自動的にプロビジョニングされます。 統合キャッシュにより、専用ゲートウェイのメモリの約 70% が使用されます。 専用ゲートウェイのメモリの残りの 30% は、要求をバックエンド パーティションにルーティングするために使用されます。

2.  新しい専用ゲートウェイ エンドポイントを使用するようにアプリケーションの接続文字列を変更します。

      更新された専用ゲートウェイ接続文字列は、 **[キー]** ブレードにあります。
   
      :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" alt-text="専用ゲートウェイ接続文字列を示す画像" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" border="false":::

      すべての専用ゲートウェイ接続文字列は、同じパターンに従います。 元の接続文字列から `documents.azure.com` を削除し、`sqlx.cosmos.azure.com` に置き換えます。 専用ゲートウェイは、削除して再プロビジョニングした場合であっても、常に同じ接続文字列を使用します。

      同じ Azure Cosmos DB アカウントを使用するどのアプリケーションでも、接続文字列の変更は必要ありません。 たとえば、一方の `CosmosClient` 接続で、ゲートウェイ モードと専用ゲートウェイ エンドポイントを使用し、他方の `CosmosClient` でダイレクト モードを使用できます。 つまり、専用ゲートウェイを追加しても、Azure Cosmos DB に接続するための既存の方法には影響しません。

3. .NET または Java SDK を使用する場合は、接続モードを[ゲートウェイ モード](sql-sdk-connection-modes.md#available-connectivity-modes)に設定します。 この手順は、Python と Node.js の各 SDK では不要です。これらには、ゲートウェイ モード以外で接続するための追加オプションがないためです。

> [!NOTE]
> 最新バージョンの .NET または Java SDK を使用している場合、既定の接続モードはダイレクト モードです。 統合キャッシュを使用するには、この既定値をオーバーライドする必要があります。

Java SDK を使用している場合は、`CosmosClientBuilder` 内で [contentResponseOnWriteEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.contentresponseonwriteenabled?view=azure-java-stable&preserve-view=true) を `true` に手動で設定する必要もあります。 他の SDK を使用している場合、この値は既に既定で `true` に設定されているので、変更を加える必要はありません。

## <a name="adjust-request-consistency"></a>要求の整合性を調整する

要求の整合性を [セッション] または [eventual]\(最終的\) に調整する必要があります。 これを行わないと、要求は常に統合キャッシュをバイパスします。 すべての読み取り操作の固有の整合性を構成する最も簡単な方法は、[アカウント レベルでこれを設定する方法](consistency-levels.md#configure-the-default-consistency-level)です。 また、[要求レベル](how-to-manage-consistency.md#override-the-default-consistency-level)で整合性を構成することもできます。これは、読み取りのサブセットでのみ統合キャッシュを使用する場合に推奨されます。

> [!NOTE]
> Python SDK を使用する場合、各要求の整合性レベルを明示的に設定する **必要があります**。 既定のアカウント レベルの設定は、自動的には適用されません。

## <a name="adjust-maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness の調整

`MaxIntegratedCacheStaleness` を構成します。これは、古いキャッシュされたデータを許容する最大時間です。 `MaxIntegratedCacheStaleness` を可能な限り高く設定することをお勧めします。これは、ポイントの読み取りとクエリが繰り返しキャッシュ ヒットする可能性が高いためです。 `MaxIntegratedCacheStaleness` を 0 に設定した場合、整合性レベルに関係なく、読み取り要求で統合キャッシュは **使用されません**。 構成されていない場合、既定値は `MaxIntegratedCacheStaleness` 5 分です。

**.NET**

```csharp
FeedIterator<Food> myQuery = container.GetItemQueryIterator<Food>(new QueryDefinition("SELECT * FROM c"), requestOptions: new QueryRequestOptions
        {
            ConsistencyLevel = ConsistencyLevel.Eventual,
            DedicatedGatewayRequestOptions = new DedicatedGatewayRequestOptions 
            { 
                MaxIntegratedCacheStaleness = TimeSpan.FromMinutes(30) 
            }
        }
);
```

> [!NOTE]
> 現時点では、最新の [.NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) と [Java](https://mvnrepository.com/artifact/com.azure/azure-cosmos/4.16.0-beta.1) のプレビュー SDK を使用して MaxIntegratedCacheStaleness のみを調整できます。

## <a name="verify-cache-hits"></a>キャッシュ ヒット数を確認する

最後に、アプリケーションを再起動して、繰り返されるポイント読み取りまたはクエリに対して、統合キャッシュのヒット数を確認できます。 専用ゲートウェイ エンドポイントを使用するように `CosmosClient` を変更すると、すべての要求が専用ゲートウェイ経由でルーティングされます。

読み取り要求 (ポイント読み取りまたはクエリ) で統合キャッシュを利用するには、次の **すべて** の条件が満たされている必要があります。

-   クライアントが専用ゲートウェイ エンドポイントに接続している
-  クライアントがゲートウェイ モードを使用している (Python と Node.js の SDK では、常にゲートウェイ モードが使用されています)
-   要求の整合性を、session または eventual に設定する必要があります。

> [!NOTE]
> 統合キャッシュについてフィードバックはありますか? ご意見をお待ちしています。 フィードバックは、Azure Cosmos DB エンジニアリング チーム (cosmoscachefeedback@microsoft.com) と直接共有できます


## <a name="next-steps"></a>次の手順

- [統合キャッシュのよくあるご質問](integrated-cache-faq.md)
- [統合キャッシュの概要](integrated-cache.md)
- [専用ゲートウェイ](dedicated-gateway.md)