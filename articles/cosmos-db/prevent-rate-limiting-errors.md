---
title: Azure Cosmos DB API for MongoDB の操作のレート制限エラーを回避する。
description: SSR (サーバー側の再試行) 機能を使用して、Azure Cosmos DB API for MongoDB の操作がレート制限エラーに到達しないようにする方法について説明します。
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540374"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Azure Cosmos DB API for MongoDB の操作のレート制限エラーを回避する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB API for MongoDB の操作は、コレクションのスループット制限 (RU) を超えると、レート制限 (16500/429) エラーで失敗することがあります。 

サーバー側の再試行 (SSR) 機能を有効にし、サーバーでこれらの操作が自動的に再試行されるようにすることができます。 少し時間が経過した後に、アカウント内のすべてのコレクションに対して要求が再試行されます。 この機能は、クライアント アプリケーションでレート制限エラーを処理する方法に代わる便利な方法です。


## <a name="use-the-azure-portal"></a>Azure ポータルの使用

1. [Azure portal](https://portal.azure.com/) にサインインします。

1. Azure Cosmos DB API for MongoDB アカウントに移動します。

1. **[設定]** セクションの下にある **[機能]** ペインにアクセスします。

1. **[Server Side Retry]\(サーバー側の再試行\)** を選択します。

1. **[有効化]** をクリックして、アカウント内のすべてのコレクションに対してこの機能を有効にします。

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Azure Cosmos DB API for MongoDB のサーバー側の再試行機能のスクリーンショット":::


## <a name="next-steps"></a>次の手順

一般的なエラーのトラブルシューティングの詳細については、次の記事を参照してください。

* [Azure Cosmos DB の MongoDB 用 API の一般的な問題のトラブルシューティング](mongodb-troubleshoot.md)
