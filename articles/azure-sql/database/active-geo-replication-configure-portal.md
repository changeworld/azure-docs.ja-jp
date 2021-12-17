---
title: チュートリアル:ポータルの geo レプリケーションとフェールオーバー
description: Azure portal または Azure CLI を使用して SQL データベースの geo レプリケーションを構成し、フェールオーバーを開始する方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: 7a0e1e84972b109f6f8e0d567224f5d08b7d18cf
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161731"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-azure-sql-database"></a>チュートリアル: アクティブ geo レプリケーションとフェールオーバーを構成する (Azure SQL Database)

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、[Azure portal](https://portal.azure.com) または Azure CLI を使用して [Azure SQL Database のアクティブ geo レプリケーション](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)を構成し、フェールオーバーを開始する方法について説明します。

自動フェールオーバー グループを利用したベスト プラクティスについては、[Azure SQL Database のベスト プラクティス](auto-failover-group-overview.md#best-practices-for-sql-database)と[Azure SQL Managed Instance のベスト プラクティス](auto-failover-group-overview.md#best-practices-for-sql-managed-instance)に関するセクションを参照してください。 



## <a name="prerequisites"></a>前提条件

# <a name="portal"></a>[ポータル](#tab/portal)

Azure Portal を使ってアクティブ geo レプリケーションを構成するには、次のリソースが必要です。

* Azure SQL Database 内のデータベース:別の地理的リージョンにレプリケートするプライマリ データベースです。

> [!Note]
> Azure portal を使用する場合は、プライマリと同じサブスクリプション内にのみセカンダリ データベースを作成できます。 別のサブスクリプション内にセカンダリ データベースが必要な場合、[データベースの作成の REST API](/rest/api/sql/databases/createorupdate) または [ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql) を使用します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

アクティブ geo レプリケーションを構成するには、Azure SQL Database のデータベースが必要です。 これは、別の地理的リージョンにレプリケートするプライマリ データベースです。

Azure CLI の環境を準備します。

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="add-a-secondary-database"></a>セカンダリ データベースの追加

次の手順では、geo レプリケーション パートナーシップに新しいセカンダリ データベースを作成します。  

セカンダリ データベースを追加するには、サブスクリプションの所有者または共同所有者でなければなりません。

セカンダリ データベースは、プライマリ データベースと同じ名前となります。また、既定でのサービス レベルおよびコンピューティング サイズはプライマリ データベースと同じになります。 セカンダリ データベースは、単一データベースまたはプールされたデータベースとすることができます。 詳細については、[DTU ベースの購入モデル](service-tiers-dtu.md)と[仮想コアベースの購入モデル](service-tiers-vcore.md)に関する記事を参照してください。
セカンダリ データベースを作成しシード処理を行うと、プライマリ データベースから新しいセカンダリ データベースへのデータのレプリケートが開始されます。

> [!NOTE]
> パートナー データベースが既に存在する場合 (たとえば、前の geo レプリケーションのリレーションシップを終了した結果として)、コマンドは失敗します。

# <a name="portal"></a>[ポータル](#tab/portal)

1. [Azure Portal](https://portal.azure.com) で、geo レプリケーションについてセットアップするデータベースを参照します。
2. [SQL Database] ページで、データベースを選択し、 **[データ管理]** までスクロールします。次に、 **[レプリカ]** を選択し、 **[レプリカの作成]** を選択します。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-cli-create-geo-replica.png" alt-text="geo レプリケーションの構成":::

3. セカンダリ データベースのサーバーを選択または作成し、必要に応じて **[コンピューティングとストレージ]** オプションを構成します。 セカンダリ サーバーでは任意のリージョンを選択できますが、[ペアのリージョン](../../best-practices-availability-paired-regions.md)をお勧めします。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-create-and-configure-replica.png" alt-text="{alt-text}":::

    必要に応じて、以下のようにセカンダリ データベースをエラスティック プールに追加できます。 プールにセカンダリ データベースを作成するには、 **[SQL エラスティック プールを使用しますか?]** の横で **[はい]** を選択し、ターゲット サーバーのプールを選択します。 プールは、ターゲット サーバーに既に存在する必要があります。 このワークフローでは、プールを作成しません。

4. **[確認と作成]** をクリックし、情報を確認してから、 **[作成]** をクリックします。
5. セカンダリ データベースが作成され、デプロイ プロセスが始まります。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-geo-replica-deployment.png" alt-text="セカンダリ データベースのデプロイ状態を示すスクリーンショット。":::

6. デプロイが完了すると、セカンダリ データベースにその状態が表示されます。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-sql-database-secondary-status.png" alt-text="セカンダリ データベースのデプロイ後の状態を示すスクリーンショット。":::

7. プライマリ データベース ページに戻り、 **[レプリカ]** を選択します。 セカンダリ データベースが **[Geo replicas]\(Geo レプリカ\)** の下に一覧表示されます。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-sql-db-geo-replica-list.png" alt-text="SQL データベースのプライマリと geo レプリカを示すスクリーンショット。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

geo レプリケーションについて設定するデータベースを選択します。 次の情報が必要になります。
- 元の Azure SQL データベース名。
- Azure SQL サーバー名。
- リソース グループ名。
- 新しいレプリカを作成するサーバーの名前。

> [!NOTE]
> セカンダリ データベースには、プライマリと同じサービス レベルがなければなりません。

セカンダリ サーバーでは任意のリージョンを選択できますが、[ペアのリージョン](../../best-practices-availability-paired-regions.md)をお勧めします。

[az sql db replica create](/cli/azure/sql/db/replica#az_sql_db_replica_create) コマンドを実行します。

```azurecli
az sql db replica create --resource-group ContosoHotel --server contosoeast --name guestlist --partner-server contosowest --family Gen5 --capacity 2 --secondary-type Geo
```

必要に応じて、以下のようにセカンダリ データベースをエラスティック プールに追加できます。 プール内にセカンダリ データベースを作成するには、`--elastic-pool` パラメーターを使用します。 プールは、ターゲット サーバーに既に存在する必要があります。 このワークフローでは、プールを作成しません。

セカンダリ データベースが作成され、デプロイ プロセスが始まります。

デプロイが完了したら、[az sql db replica list-links](/cli/azure/sql/db/replica#az_sql_db_replica_list-links) コマンドを実行して、セカンダリ データベースの状態を確認できます。
    
```azurecli
az sql db replica list-links --name guestlist --resource-group ContosoHotel --server contosowest
```

---

## <a name="initiate-a-failover"></a>フェールオーバーの開始

セカンダリ データベースは、プライマリ データベースとして使用するように切り替えることができます。

# <a name="portal"></a>[ポータル](#tab/portal)  

1. [Azure Portal](https://portal.azure.com) で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. **[データ管理]** までスクロールし、 **[レプリカ]** を選択します。
3. **[Geo replicas]\(Geo レプリカ\)** の一覧で、新しいプライマリとして使用するデータベースを選択し、省略記号を選択して、 **[強制フェールオーバー]** を選択します。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-forced-failover.png" alt-text="ドロップダウンからの強制フェールオーバーの選択を示すスクリーンショット。":::
4. **[はい]** を選択して、フェールオーバーを開始します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az sql db replica set-primary](/cli/azure/sql/db/replica#az_sql_db_replica_set-primary) コマンドを実行します。

```azurecli
az sql db replica set-primary --name guestlist --resource-group ContosoHotel --server contosowest
```

---

このコマンドは、セカンダリ データベースをプライマリ ロールに即座に切り替えます。 このプロセスは、通常 30 秒以内に完了します。

ロールの切り替え中に、わずかですが両方のデータベースが使用できなくなる期間 (0 ～ 25 秒程度) が生じます。 プライマリ データベースに複数のセカンダリ データベースがある場合は、コマンドによって、新しいプライマリに接続するように他のセカンダリが自動的に再構成されます。 通常の状況では、操作全体が完了するのに 1 分かかりません。

> [!NOTE]
> このコマンドは、障害発生時にデータベースを迅速に復旧するために設計されています。 データを同期せずに、フェールオーバーがトリガーされます (強制フェールオーバー)。  コマンドが発行されたときに、プライマリがオンラインでトランザクションをコミット中の場合、一部のデータが失われる可能性があります。

## <a name="remove-secondary-database"></a>セカンダリ データベースを削除する

この操作では、セカンダリ データベースへのレプリケーションを完全に停止し、セカンダリのロールを通常の読み取り/書き込みデータベースに変更します。 セカンダリ データベースへの接続が切断された場合、コマンドは成功しますが、接続が復元するまでセカンダリは読み取り/書き込み状態になりません。

# <a name="portal"></a>[ポータル](#tab/portal)  

1. [Azure Portal](https://portal.azure.com) で、geo レプリケーション パートナーシップのプライマリ データベースを参照します。
2. **[レプリカ]** を選択します。
3. **[Geo replicas]\(Geo レプリカ\)** の一覧で、geo レプリケーション パートナーシップから削除するデータベースを選択し、省略記号を選択して、 **[レプリケーションの停止]** を選択します。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-stop-replication.png" alt-text="ドロップダウンから [レプリケーションの停止] を選択していることを示すスクリーンショット。":::
5. 確認ウィンドウが開きます。 **[はい]** をクリックして geo レプリケーション パートナーシップからデータベースを削除します。 (データベースを読み取り/書き込みデータベースに設定することは、レプリケーションの一部ではありません)。
 
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete-link) コマンドを実行します。

```azurecli
az sql db replica delete-link --name guestlist --resource-group ContosoHotel --server contosoeast --partner-server contosowest
```

操作を実行することを確認します。

---

## <a name="next-steps"></a>次のステップ

* アクティブ geo レプリケーションについて詳しくは、[アクティブ geo レプリケーション](active-geo-replication-overview.md)に関するページを参照してください。
* 自動フェールオーバー グループについては、[自動フェールオーバー グループ](auto-failover-group-overview.md)に関するページを参照してください
* ビジネス継続性の概要およびシナリオについては、 [ビジネス継続性の概要](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関する記事を参照してください。