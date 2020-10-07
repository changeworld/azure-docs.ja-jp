---
title: Azure Cosmos DB の MongoDB 用 API アカウントの Mongo バージョンをアップグレードする
description: 既存の Azure Cosmos DB の MongoDB 用 API アカウントに使用する MongoDB ワイヤ プロトコル バージョンをシームレスにアップグレードする方法
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409665"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>Azure Cosmos DB の MongoDB 用 API アカウントの MongoDB ワイヤ プロトコル バージョンをアップグレードする

この記事では、Azure Cosmos DB の MongoDB 用 API アカウントの、ワイヤ プロトコル バージョンをアップグレードする方法について説明します。 ワイヤ プロトコル バージョンをアップグレードした後、Azure Cosmos DB の MongoDB 用 API に含まれる最新機能を使用できます。 アップグレード プロセスによって、アカウントの可用性が中断されることはなく、どの時点においても、RU/秒が消費されたりデータベースの容量が減少したりすることはありません。 既存のデータまたはインデックスには、このプロセスの影響はありません。

>[!Note]
> 現時点では、サーバー バージョン 3.2 を使用している条件を満たすアカウントのみをバージョン 3.6 にアップグレードできます。 アカウントにアップグレード オプションが表示されない場合は、[サポート チケットをご提出ください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="upgrading-from-version-32-to-36"></a>バージョンを 3.2 から 3.6 にアップグレードする

### <a name="benefits-of-upgrading-to-version-36"></a>バージョン 3.6 にアップグレードする利点

バージョン 3.6 には、次の新機能があります。
- 強化されたパフォーマンスと安定性
- 新しいデータベース コマンドのサポート
- 既定の集計ステージと新しい集計ステージによる集計パイプラインのサポート
- Change Stream のサポート
- 複合インデックスのサポート
- 次の操作のためのクロスパーティションのサポート: 更新、削除、カウント、並べ替え
- 次の集計操作のパフォーマンスの向上: $count、$skip、$limit、$group
- ワイルドカードのインデックス作成がサポートされるようになりました

### <a name="changes-from-version-32"></a>バージョン 3.2 からの変更点

- **RequestRateIsLarge エラーは削除されました**。 クライアント アプリケーションからの要求で、16500 エラーはもう返されません。 代わりに、要求は完了するかタイムアウトになるまで再開されます。
- 要求ごとのタイムアウトは 60 秒に設定されています。
- 新しいワイヤ プロトコル バージョンで作成された MongoDB コレクションによって、既定でインデックスが作成されるのは `_id` プロパティのみです。

### <a name="action-required"></a>必要な操作

バージョン 3.6 へのアップグレードの場合、データベース アカウントのエンドポイント サフィックスは次の形式に更新されます。

```
<your_database_account_name>.mongo.cosmos.azure.com
```

このデータベース アカウントで接続するアプリケーションとドライバーの既存のエンドポイントを置き換える必要があります。 **新しいエンドポイントを使用している接続のみが、MongoDB バージョン 3.6 の機能にアクセスできます**。 以前のエンドポイントには `.documents.azure.com` というサフィックスが付いているはずです。

>[!Note]
> このエンドポイントは、お使いのアカウントがソブリン、政府、または制限された Azure クラウドに作成されたときは少し異なる場合があります。

### <a name="how-to-upgrade"></a>アップグレードする方法

1. まず Azure portal に移動し、Azure Cosmos DB の MongoDB 用 API アカウントに移動します。 サーバーのバージョンが `3.2` であることを確認します。 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

2. 左側のオプションで、[`Features`] ブレードを選択します。 これにより、データベース アカウントで使用できるアカウント レベルの機能が表示されます。

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

3. `Upgrade to Mongo server version 3.6` 行をクリックします。 このオプションが表示されない場合は、アカウントがこのアップグレードに適合していない可能性があります。 これに該当する場合は、[サポート チケット](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)をご提出ください。

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

4. この特定のアップグレードについて表示されている情報を確認します。 このセクションで強調表示されているように、更新されたエンドポイントがアプリケーションで使用されるまで、アップグレードは完了しないことにご注意ください。 プロセスを開始する準備ができ次第、[`Enable`] をクリックします。

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

5. プロセスを開始すると、[`Features`] メニューにアップグレードの状態が表示されます。 状態は `Pending` から `In Progress` になり、`Upgraded` になります。 このプロセスは、データベース アカウントの既存の機能や操作には影響しません。

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

6. アップグレードが完了すると、状態が `Upgraded` と表示されます。 それをクリックすると、プロセスを終了するために必要な次の手順とアクションの詳細が表示されます。 要求の処理中に問題が発生した場合は、[サポートにお問い合わせください](https://azure.microsoft.com/en-us/support/create-ticket/)。

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

7. **アップグレードされたバージョンのデータベース アカウントの使用を開始するには**、[`Overview`] ブレードに戻り、アプリケーションで使用する新しい接続文字列をコピーします。 アプリケーションで新しいエンドポイントに接続するとすぐに、アップグレードされたバージョンの使用が開始されます。 既存の接続は中断されず、都合のよいときに更新できます。 一貫性のあるエクスペリエンスを確保するには、すべてのアプリケーションで新しいエンドポイントを使用する必要があります。

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

## <a name="next-steps"></a>次のステップ

- サポート対象およびサポート対象外の [MongoDB バージョン 3.6 の機能](mongodb-feature-support-36.md)について確認します。
- 詳細については、[Mongo 3.6 バージョンの機能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)を参照してください。
