---
title: Azure Cosmos DB API for MongoDB の操作のレート制限エラーを回避する。
description: SSR (サーバー側の再試行) 機能を使用して、Azure Cosmos DB API for MongoDB の操作がレート制限エラーに到達しないようにする方法について説明します。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9067a87887c47229b8bba687535e2730c2ed1b
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123030905"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Azure Cosmos DB API for MongoDB の操作のレート制限エラーを回避する
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API for MongoDB の操作は、コレクションのスループット制限 (RU) を超えると、レート制限 (16500/429) エラーで失敗することがあります。 

サーバー側の再試行 (SSR) 機能を有効にし、サーバーでこれらの操作が自動的に再試行されるようにすることができます。 少し時間が経過した後に、アカウント内のすべてのコレクションに対して要求が再試行されます。 この機能は、クライアント アプリケーションでレート制限エラーを処理する方法に代わる便利な方法です。

## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Cosmos DB API for MongoDB アカウントに移動します。

1. **[設定]** セクションの下にある **[機能]** ペインにアクセスします。

1. **[Server Side Retry]\(サーバー側の再試行\)** を選択します。

1. **[有効化]** をクリックして、アカウント内のすべてのコレクションに対してこの機能を有効にします。

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Azure Cosmos DB API for MongoDB のサーバー側の再試行機能のスクリーンショット":::

## <a name="use-the-azure-cli"></a>Azure CLI の使用

1. ご利用のアカウントで SSR が既に有効になっているかどうかを確認します。

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

1. ご利用のデータベース アカウント内のすべてのコレクションに対して SSR を **有効にします**。 この変更が有効になるまで、最大 15 分かかる場合があります。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
   ```

1. 次のコマンドは、機能の一覧から `DisableRateLimitingResponses` を削除することによって、データベース アカウント内のすべてのコレクションに対してサーバー側の再試行を **無効** にします。 この変更が有効になるまで、最大 15 分かかる場合があります。

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
   ```

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-are-requests-retried"></a>要求はどのように再試行されますか。

60 秒のタイムアウトに達するまで、要求は継続的に (繰り返し) 再試行されます。 タイムアウトに達すると、クライアントに [ExceededTimeLimit 例外 (50)](error-codes-solutions.md) が返されます。

### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>サーバー側の再試行の影響はどのように監視するのですか。

[Cosmos DB メトリック] ウィンドウでは、サーバー側で再試行されたレート制限エラー (429) を確認できます。 SSR が有効になっている場合、これらのエラーはサーバー側で処理され再試行されるため、クライアントには返されないことにご注意ください。

[Cosmos DB リソース ログ](../cosmosdb-monitor-resource-logs.md)内で、*estimatedDelayFromRateLimitingInMilliseconds* を含むログ エントリを検索することができます。

### <a name="will-server-side-retry-affect-my-consistency-level"></a>サーバー側の再試行を適用すると、整合性レベルに影響がありますか。

サーバー側の再試行を適用しても、要求の整合性に影響はありません。 要求は、レート制限を受けた場合 (429 エラー)、サーバー側で再試行されます。

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>クライアントに返される可能性のある種類のエラーは、サーバー側の再試行の影響を受けますか。

いいえ。サーバー側の再試行の影響を受けるのは、レート制限エラー (429) のみであり、これらはサーバー側で再試行されます。 この機能を使用すると、クライアント アプリケーション内でレート制限エラーを処理しなくてもよくなります。 [他のエラー](error-codes-solutions.md)はすべて、クライアントに返されます。

## <a name="next-steps"></a>次の手順

一般的なエラーのトラブルシューティングの詳細については、次の記事を参照してください。

* [Azure Cosmos DB の MongoDB 用 API の一般的な問題のトラブルシューティング](error-codes-solutions.md)

Azure Cosmos DB に移行する容量計画を実行しようとしていますか? 容量計画のために、既存のデータベース クラスターに関する情報を使用できます。
* 既存のデータベース クラスター内の仮想コアとサーバー数のみがわかっている場合は、[仮想コア数または仮想 CPU 数を使用した要求ユニットの見積もり](../convert-vcore-to-request-unit.md)に関するページを参照してください 
* 現在のデータベース ワークロードに対する通常の要求レートがわかっている場合は、[Azure Cosmos DB Capacity Planner を使用した要求ユニットの見積もり](estimate-ru-capacity-planner.md)に関するページを参照してください
