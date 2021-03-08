---
title: Azure Cosmos DB の MongoDB 用 API アカウントの Mongo バージョンをアップグレードする
description: 既存の Azure Cosmos DB の MongoDB 用 API アカウントに使用する MongoDB ワイヤ プロトコル バージョンをシームレスにアップグレードする方法
author: christopheranderson
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/02/2021
ms.author: chrande
ms.openlocfilehash: 1818838a68c2712336a3515b2a82b5fdd518d237
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661173"
---
# <a name="upgrade-the-api-version-of-your-azure-cosmos-db-api-for-mongodb-account"></a>MongoDB 用 Azure Cosmos DB API アカウントの API バージョンをアップグレードする
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

この記事では、Azure Cosmos DB の MongoDB 用 API アカウントの API バージョンをアップグレードする方法について説明します。 アップグレード後、Azure Cosmos DB の MongoDB 用 API に含まれる最新機能を使用できます。 アップグレード プロセスによって、アカウントの可用性が中断されることはなく、どの時点においても、RU/秒が消費されたりデータベースの容量が減少したりすることはありません。 既存のデータまたはインデックスには、このプロセスの影響はありません。 

新しい API バージョンにアップグレードする場合は、運用環境のワークロードをアップグレードする前に、開発 (またはテスト) ワークロードから始めます。 MongoDB 用 Azure Cosmos DB API アカウントをアップグレードする前に、アップグレード後の API バージョンと互換性のあるバージョンにクライアントをアップグレードしておくことが大切です。

>[!Note]
> 現時点では、サーバー バージョン 3.2 を使用している条件を満たすアカウントのみをバージョン 3.6 または 4.0 にアップグレードできます。 アカウントにアップグレード オプションが表示されない場合は、[サポート チケットをご提出ください](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="upgrading-to-40-or-36"></a>4\.0 または 3.6 にアップグレードする

### <a name="benefits-of-upgrading-to-version-40"></a>バージョン 4.0 にアップグレードする利点

バージョン 4.0 には、次の新機能があります。
- シャード化されていないコレクション内の複数ドキュメント トランザクションのサポート。
- 新しい集計演算子
- スキャン パフォーマンスの向上
- ストレージの高速化と効率化

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

- [SSR (Server Side Retry)](prevent-rate-limiting-errors.md) 機能が既定で有効になっているため、クライアント アプリケーションからの要求で 16500 エラーが返されることはありません。 要求が完了するか 60 秒のタイムアウトに達するまで、要求が再開されます。
- 要求ごとのタイムアウトは 60 秒に設定されています。
- 新しいワイヤ プロトコル バージョンで作成された MongoDB コレクションによって、既定でインデックスが作成されるのは `_id` プロパティのみです。

### <a name="action-required-when-upgrading-from-32"></a>3\.2 からアップグレードする際に必要な対応

3\.2 からアップグレードすると、データベース アカウントのエンドポイントのサフィックスが次の形式に更新されます。

```
<your_database_account_name>.mongo.cosmos.azure.com
```

バージョン 3.2 からアップグレードする場合は、このデータベース アカウントで接続するアプリケーションとドライバーの既存のエンドポイントを置き換える必要があります。 **新しいエンドポイントを使用している接続のみが、新しい API バージョンの機能にアクセスできます**。 以前の 3.2 エンドポイントには `.documents.azure.com` というサフィックスが付いているはずです。

>[!Note]
> このエンドポイントは、お使いのアカウントがソブリン、政府、または制限された Azure クラウドに作成されたときは少し異なる場合があります。

## <a name="how-to-upgrade"></a>アップグレードする方法

1. Azure portal に移動し、MongoDB 用 Azure Cosmos DB API アカウントの概要ブレードに移動します。 現在のサーバーが、想定したバージョンであることを確認します。

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="MongoDB アカウントを使用した Azure portal の概要" border="false":::

2. 左側のオプションで、[`Features`] ブレードを選択します。 これにより、データベース アカウントで使用できるアカウント レベルの機能が表示されます。

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure portal で機能ブレードが強調表示された MongoDB アカウントの概要" border="false":::

3. `Upgrade Mongo server version` 行をクリックします。 このオプションが表示されない場合は、アカウントがこのアップグレードに適合していない可能性があります。 これに該当する場合は、[サポート チケット](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)をご提出ください。

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="オプションを含む機能ブレード。" border="false":::

4. アップグレードについて表示されている情報を確認します。 プロセスを開始する準備ができ次第、[`Enable`] をクリックします。

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="展開されたアップグレード ガイダンス。" border="false":::

5. プロセスを開始すると、[`Features`] メニューにアップグレードの状態が表示されます。 状態は `Pending` から `In Progress` になり、`Upgraded` になります。 このプロセスは、データベース アカウントの既存の機能や操作には影響しません。

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="アップグレードの開始後の状態。" border="false":::

6. アップグレードが完了すると、状態が `Upgraded` と表示されます。 それをクリックすると、プロセスを終了するために必要な次の手順とアクションの詳細が表示されます。 要求の処理中に問題が発生した場合は、[サポートにお問い合わせください](https://azure.microsoft.com/en-us/support/create-ticket/)。

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="アップグレードされたアカウントの状態。" border="false":::

7. 
    1. 3\.2 からアップグレードする場合は、`Overview` ブレードに戻り、アプリケーションで使用する新しい接続文字列をコピーします。 3\.2 を実行している古い接続文字列は中断されません。 一貫性のあるエクスペリエンスを確保するには、すべてのアプリケーションで新しいエンドポイントを使用する必要があります。
    2. 3\.6 からアップグレードした場合、既存の接続文字列は、指定されたバージョンにアップグレードされて引き続き使用されます。

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="新しい概要ブレード。" border="false":::


## <a name="how-to-downgrade"></a>ダウングレードする方法
「アップグレードする方法」セクションにある同じ手順に従って、アカウントを 4.0 から 3.6 にダウングレードすることもできます。 

3\.2 から (4.0 または 3.6 に) アップグレードした後で 3.2 にダウングレードしたい場合は、アップグレード後もバージョン 3.2. を実行しているアクティブなホスト `accountname.documents.azure.com` で、以前 (3.2) の接続文字列に戻すだけでかまいません。


## <a name="next-steps"></a>次のステップ

- サポート対象およびサポート対象外の [MongoDB バージョン 4.0 の機能](mongodb-feature-support-40.md)について確認します。
- サポート対象およびサポート対象外の [MongoDB バージョン 3.6 の機能](mongodb-feature-support-36.md)について確認します。
- 詳細については、[Mongo 3.6 バージョンの機能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)を参照してください。
