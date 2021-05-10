---
title: チュートリアル:MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、オンプレミスの MongoDB から Azure Cosmos DB の MongoDB 用 API にオフラインで移行します。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443869"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>チュートリアル: MongoDB を Azure Cosmos DB の MongoDB 用 API にオフラインで移行する

Azure Database Migration Service を使用して、MongoDB のオンプレミスまたはクラウドのインスタンスから Azure Cosmos DB の MongoDB 用 API に、データベースの 1 回限りの移行をオフラインで実行します。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
>
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

このチュートリアルでは、Azure 仮想マシンでホストされている MongoDB のデータセットを移行します。 Azure Database Migration Service を使用して、データセットを Azure Cosmos DB の MongoDB 用 API に移行します。 MongoDB ソースをまだ設定していない場合は、「[Azure の Windows VM に MongoDB をインストールして構成する](/previous-versions/azure/virtual-machines/windows/install-mongodb)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* スループットの見積もりやパーティション キーの選択など、[移行前の手順を完了](../cosmos-db/mongodb-pre-migration.md)します。
* [Azure Cosmos DB の MongoDB 用 API アカウントを作成します](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。
* Azure Resource Manager を使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。 このデプロイ モデルにより、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) のどちらかを使用して、オンプレミスのソース サーバーへのサイト間接続が提供されます。 仮想ネットワークの作成方法の詳細については、[Azure Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載された "クイックスタート" の記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイントや Azure Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* 仮想ネットワークのネットワーク セキュリティ グループ (NSG) の規則によって、次の各通信ポートがブロックされていないことを確認します。53、443、445、9354、および 10000 から 20000。 詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](../virtual-network/virtual-network-vnet-plan-design-arm.md)」を参照してください。
* Azure Database Migration Service がソース MongoDB サーバーにアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 27017 が使用されています。
* ソース データベースの前でファイアウォール アプライアンスを使用している場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスできるように、ファイアウォール規則の追加が必要になることがあります。

## <a name="configure-the-server-side-retry-feature"></a>サーバー側の再試行機能を構成する

MongoDB から Azure Cosmos DB に移行する場合は、リソース ガバナンス機能を活用できます。 これらの機能により、プロビジョニング済み要求ユニット (RU/秒) のスループットを最大限に利用できるようになります。 Azure Cosmos DB では、Database Migration Service の特定の要求がコンテナーにプロビジョニングされている RU/秒を超える場合に、移行の過程でその要求を調整することができます。 その後、その要求を再試行する必要があります。

Database Migration Service では再試行を実行できます。 Database Migration Service と Azure Cosmos DB の間のネットワーク ホップに関係するラウンドトリップ時間が、その要求の全体的な応答時間に影響することを理解しておくことが重要です。 調整された要求の応答時間を改善することにより、移行に必要な合計時間を短縮できます。

Azure Cosmos DB のサーバー側の再試行機能を使用すると、サービスで調整エラー コードをインターセプトし、はるかに短いラウンドトリップ時間で再試行することができるため、要求の応答時間が大幅に短縮されます。

サーバー側の再試行を使用するには、Azure Cosmos DB ポータルで **[機能]**  >  **[Server Side Retry]\(サーバー側の再試行\)** を選択します。

![サーバー側の再試行機能を見つける場所を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

機能が無効になっている場合は、 **[有効]** を選択します。

![サーバー側の再試行を有効にする方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>リソース プロバイダーの登録

1. Azure portal にサインインし、 **[すべてのサービス]** を選択し、 **[サブスクリプション]** を選択します。

   ![ポータルのサブスクリプションを示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択してから、 **[リソース プロバイダー]** を選びます。

    ![リソース プロバイダーを示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service のインスタンスを作成する方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. **[移行サービスの作成]** で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. Azure Database Migration Service のインスタンスを作成する場所を選択します。 

5. 既存の仮想ネットワークを選択するか、新しく作成します。

    この仮想ネットワークによって、Azure Database Migration Service に、ソース MongoDB インスタンスとターゲット Azure Cosmos DB アカウントへのアクセスが提供されます。

    Azure portal で仮想ネットワークを作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)に関するページを参照してください。

6. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service のインスタンスの構成設定を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスを作成したら、Azure portal 内でそのサービスを見つけて開きます。 次に、新しい移行プロジェクトを作成します。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

      ![Azure Database Migration Service のすべてのインスタンスを検索する方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. **[Azure Database Migration Services]** 画面で、作成した Azure Database Migration Service インスタンスの名前を検索して選択します。

3. **[+ New Migration Project]\(+ 新しい移行プロジェクト\)** を選択します。

4. **[新しい移行プロジェクト]** 画面で、プロジェクトの名前を指定し、 **[ソース サーバーの種類]** テキスト ボックスで **[MongoDB]** を選択します。 **[ターゲット サーバーの種類]** テキスト ボックスで **[CosmosDB (MongoDB API)]** を選択し、 **[アクティビティの種類を選択します]** で **[オフライン データの移行]** を選択します。 

    ![プロジェクトのオプションを示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの詳細]** 画面で、ソース MongoDB サーバーの接続の詳細を指定します。

   > [!IMPORTANT]
   > Azure Database Migration Service では、Azure Cosmos DB はソースとしてサポートしていません。

    ソースへの接続には、3 つのモードがあります。
   * **標準モード**: 完全修飾ドメイン名または IP アドレス、ポート番号、および接続の資格情報を受け取ります。
   * **接続文字列のモード**: MongoDB 接続文字列を受け取ります。詳細については、「[接続文字列の URI 形式](https://docs.mongodb.com/manual/reference/connection-string/)」を参照してください。
   * **Azure Storage からのデータ**: BLOB コンテナーの SAS URL を受け取ります。 MongoDB [bsondump ツール](https://docs.mongodb.com/manual/reference/program/bsondump/)によって生成された BSON ダンプが BLOB コンテナーに含まれている場合は、 **[Blob には BSON ダンプが含まれています]** を選択します。 コンテナーに JSON ファイルが含まれている場合は、このオプションを選択しないでください。

     このオプションを選択する場合は、ストレージ アカウントの接続文字列が次の形式で表示されていることを確認してください。

     ```
     https://blobnameurl/container?SASKEY
     ```

     この BLOB コンテナーの SAS 接続文字列は Azure Storage Explorer 内で見つけることができます。 該当するコンテナーの SAS を作成すると、要求された形式で URL が提供されます。
     
     また、Azure Storage のダンプの種類情報に基づいて、次の点に注意してください。

     * BSON ダンプの場合、BLOB コンテナー内のデータは bsondump 形式にする必要があります。 データ ファイルは、包含データベースに基づいた名前が付けられたフォルダーに、*collection.bson* の形式で配置します。 メタデータ ファイルには *collection.metadata.json* の形式を使用して名前を付けます。

     * JSON ダンプの場合、BLOB コンテナー内のファイルは、包含データベースの名前が付けられたフォルダーに配置する必要があります。 データ ファイルは、各データベース フォルダー内の *data* というサブフォルダーに配置し、*collection.json* の形式で名前を付ける必要があります。 メタデータ ファイルは、*metadata* というサブフォルダーに配置し、同じ形式の *collection.json* を使用して名前を付ける必要があります。 メタデータ ファイルは、MongoDB bsondump ツールによって生成されたものと同じ形式にする必要があります。

    > [!IMPORTANT]
    > MongoDB サーバーでは、自己署名証明書の使用はお勧めしません。 使用する必要がある場合は、接続文字列のモードを使用してサーバーに接続し、接続文字列に必ず引用符 ("") を付けるようにしてください。
    >
    >```
    >&sslVerifyCertificate=false
    >```

   DNS 名を解決できない場合に備えて、IP アドレスを使用することもできます。

   ![ソースの詳細を指定する方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[移行のターゲットの詳細]** 画面で、ターゲットの Azure Cosmos DB アカウントに対する接続の詳細を指定します。 このアカウントは、事前プロビジョニングされた Azure Cosmos DB の MongoDB 用 API アカウントで、MongoDB データの移行先となります。

    ![ターゲットの詳細を指定する方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. **[保存]** を選択します。

## <a name="map-to-target-databases"></a>ターゲット データベースにマップする

1. **[Map to target databases]\(ターゲット データベースにマップ\)** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。

    データベース名の横に **[作成]** と表示される場合は、Azure Database Migration Service でターゲット データベースが見つからず、サービスによってデータベースが自動的に作成されることを示します。

    以降のこの時点で、[スループットをプロビジョニングする](../cosmos-db/set-throughput.md)ことができます。 Azure Cosmos DB では、データベース レベルで、またはコレクションごとに個別に、スループットをプロビジョニングできます。 スループットは、[要求ユニット](../cosmos-db/request-units.md)で測定されます。 [Azure Cosmos DB の価格](https://azure.microsoft.com/pricing/details/cosmos-db/)の詳細を確認してください。

    ![ターゲット データベースにマッピングする方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. **[保存]** を選択します。
3. **[コレクション設定]** 画面で、コレクションの一覧表示を展開し、移行されるコレクションの一覧を確認します。

    Azure Database Migration Service では、ソースの MongoDB インスタンスに存在しているが、ターゲットの Azure Cosmos DB アカウントには存在していない、すべてのコレクションが自動的に選択されます。 データが既に含まれているコレクションを再度移行する場合は、このペインでコレクションを明示的に選択する必要があります。

    コレクションで使用する RU の数を指定することができます。 Azure Database Migration Service では、コレクションのサイズに基づいて適切な既定値が提案されます。

    > [!NOTE]
    > データベースの移行とコレクションを並列で実行します。 必要に応じて、Azure Database Migration Service の複数のインスタンスを使用して、実行速度を上げることができます。

    また、最適なスケーラビリティのために、シャード キーを指定して [Azure Cosmos DB のパーティション分割](../cosmos-db/partitioning-overview.md)を利用することもできます。 [シャードまたはパーティション キーの選択に関するベスト プラクティス](../cosmos-db/partitioning-overview.md#choose-partitionkey)を確認してください。

    ![コレクション テーブルの選択方法を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. **[保存]** を選択します。

5. **[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** ボックスに移行アクティビティの名前を指定します。

    ![移行の概要を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>移行を実行する

**[移行の実行]** を選択します。 移行アクティビティ ウィンドウが表示され、アクティビティの状態は **[未開始]** になっています。

![アクティビティの状態を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>移行を監視する

移行アクティビティ画面で、移行の状態が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

> [!NOTE]
> アクティビティを選択して、データベースレベルおよびコレクションレベルの移行メトリックの詳細を取得できます。

![アクティビティが完了した状態を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Azure Cosmos DB 内のデータを検証する

移行が完了した後、Azure Cosmos DB アカウントを調べて、すべてのコレクションが正常に移行されたことを確認できます。

![すべてのコレクションが正常に移行されたことを確認するために、Azure Cosmos DB アカウントを確認する場所を示すスクリーンショット。](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>移行後の最適化

MongoDB データベースの格納データを Azure Cosmos DB の MongoDB 用 API に移行したら、Azure Cosmos DB に接続してデータを管理できます。 また、移行後のその他の最適化手順を実行することもできます。 これには、インデックス作成ポリシーの最適化、既定の整合性レベルの更新、Azure Cosmos DB アカウントのグローバル分散の構成などが含まれます。 詳細については、[移行後の最適化](../cosmos-db/mongodb-post-migration.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ

「[Azure データベースの移行ガイド](https://datamigration.microsoft.com/)」で、他のシナリオの移行ガイダンスを確認する。



