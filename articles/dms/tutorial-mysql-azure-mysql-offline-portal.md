---
title: 'チュートリアル: DMS を使用して MySQL を Azure Database for MySQL にオフラインで移行する'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、オンプレミスの MySQL から Azure Database for MySQL へのオフライン移行を実行する方法を説明します。
services: dms
author: sumitgaurin
ms.author: sgaur
manager: arthiaga
ms.reviewer: arthiaga
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 04/11/2021
ms.openlocfilehash: cafe928ad8baed2a597bfef9bbedca8ca72e1d76
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201999"
---
# <a name="tutorial-migrate-mysql-to-azure-database-for-mysql-offline-using-dms"></a>チュートリアル: DMS を使用して MySQL を Azure Database for MySQL にオフラインで移行する

Azure Database Migration Service を使用すると、高速データ移行機能によって、オンプレミスの MySQL インスタンスから [Azure Database for MySQL](../mysql/index.yml) に対し、1 回限りの完全データベース移行を実行できます。 このチュートリアルでは、Azure Database Migration Service のオフライン移行アクティビティを使用して、サンプル データベースを MySQL 5.7 のオンプレミス インスタンスから Azure Database for MySQL (v5.7) に移行します。 この記事は、ソースとして MySQL データベース インスタンスを、ターゲットとして Azure Database for MySQL を想定していますが、ソース サーバーの名前と資格情報さえ変更すれば Azure Database for MySQL 間の移行にも使用できます。 また、MySQL サーバーの下位バージョン (v5.6 以上) から上位バージョンへの移行にも対応します。

> [!IMPORTANT]
> オンライン移行では、[データイン レプリケーション](../mysql/concepts-data-in-replication.md)と共に、[MyDumper/MyLoader](https://centminmod.com/mydumper.html) などのオープンソース ツールを使用できます。 

> [!NOTE]
> この移行エクスペリエンスを PowerShell スクリプトで実現したバージョンについては、[Azure Database for MySQL へのオフライン移行のスクリプト制御](./migrate-mysql-to-azure-mysql-powershell.md)に関するページを参照してください。

> [!NOTE]
> Amazon Relational Database Service (RDS) for MySQL および Amazon Relational Database Service (RDS) for Amazon Aurora (MySQL ベース) も移行元としてサポートされます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
>
> * mysqldump ユーティリティを使用して、データベース スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

* アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free)。
* バージョン 5.7 の MySQL データベースをオンプレミスに用意します。 ない場合は、[MySQL Community Edition](https://dev.mysql.com/downloads/mysql/) 5.7 をダウンロードしてインストールしてください。
* MySQL オフライン移行は、Premium DMS SKU でのみサポートされています。
* [Azure Database for MySQL でインスタンスを作成します](../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)。 Workbench アプリケーションを使用してデータベースを接続、作成する方法の詳細については、[MySQL Workbench を使用した接続とデータの照会](../mysql/connect-workbench.md)に関するページを参照してください。 Azure Database for MySQL のバージョンは、オンプレミスの MySQL のバージョンと同じかそれ以上である必要があります。 たとえば、MySQL 5.7 は Azure Database for MySQL 5.7 に移行したり、8 にアップグレードしたりすることができます。 
* Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続が確立されます。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    >
    > * ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > * ストレージ エンドポイント
    > * サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

* 仮想ネットワークのネットワーク セキュリティ グループの規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
* Azure Database Migration Service がソース MySQL Server (既定では TCP ポート 3306) にアクセスできるよう、Windows ファイアウォールを開放して仮想ネットワークからの接続を許可します。
* ソース データベースの手前でファイアウォール アプライアンスを使用する場合は、移行対象のソース データベースに Azure Database Migration Service がアクセスできるよう、その仮想ネットワークからの接続を許可するファイアウォール規則を追加することが必要となる場合があります。
* Azure Database Migration Service の仮想ネットワークからターゲット データベースにアクセスできるよう、サーバーレベルの[ファイアウォール規則](../azure-sql/database/firewall-configure.md)を作成するか、ターゲット Azure Database for MySQL の [VNET サービス エンドポイントを構成](../mysql/howto-manage-vnet-using-portal.md)します。
* ソース MySQL は、サポートされている MySQL Community Edition で実行されている必要があります。 MySQL インスタンスのバージョンを確認するには、MySQL ユーティリティまたは MySQL Workbench で、次のコマンドを実行します。

    ```
    SELECT @@version;
    ```

* Azure Database for MySQL は、InnoDB テーブルのみをサポートします。 MyISAM テーブルを InnoDB に変換するには、[MyISAM から InnoDB へのテーブルの変換](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html)に関する記事を参照してください。
* ユーザーには、ソース データベースにあるデータの読み取り権限が必要です。

## <a name="sizing-the-target-azure-database-for-mysql-instance"></a>ターゲットの Azure Database for MySQL インスタンスをサイズ設定する

Azure Database Migration Service を使用してデータ読み込みを高速化するようにターゲットの Azure Database for MySQL サーバーを準備するには、次のサーバー パラメーターと構成の変更をお勧めします。 

* max_allowed_packet – 1073741824 (1GB) に設定して、行が大きいために発生する接続の問題を防ぎます。 
* slow_query_log – OFF に設定して、低速のクエリ ログを無効にします。 これにより、データの読み込み中の低速クエリ ログによって発生するオーバーヘッドがなくなります。
* query_store_capture_mode – NONE に設定し、クエリ ストアを無効にします。 これにより、クエリ ストアによるサンプリング アクティビティで発生するオーバーヘッドがなくなります。
* innodb_buffer_pool_size – Innodb_buffer_pool_size は、Azure Database for MySQL サーバーのコンピューティングをスケールアップしないと増やすことができません。 移行中にサーバーをポータルの [価格レベル] から 64 仮想コア General Purpose SKU にスケールアップし、innodb_buffer_pool_size を増やします。 
* innodb_io_capacity & innodb_io_capacity_max - IO 使用率を向上させて移行速度を最適化するために、Azure portal のサーバー パラメーターから 9000 に変更します。
* innodb_write_io_threads および innodb_write_io_threads - Azure portal のサーバー パラメーターで 4 に変更し、移行の速度を向上させます。
* ストレージ層のスケールアップ – Azure Database for MySQL サーバーの IOP は、ストレージ層の増加に合せて徐々に増加します。 
    * 単一サーバーのデプロイ オプションでは、読み込みを高速化するために、ストレージ層を増やしてプロビジョニングされる IOP を増やすことをお勧めします。 
    * フレキシブル サーバーのデプロイ オプションでは、ストレージ サイズに関係なく IOPS をスケーリング (増減) することができます。 
    * ストレージ サイズはスケールアップのみ可能で、スケールダウンはできません。

移行が完了したら、サーバー パラメーターと構成を、ワークロードで必要な値に戻すことができます。 


## <a name="migrate-database-schema"></a>データベース スキーマを移行する

テーブル スキーマ、インデックス、ストアド プロシージャなどのすべてのデータベース オブジェクトを転送するには、ソース データベースからスキーマを抽出し、ターゲット データベースに適用する必要があります。 スキーマを抽出するには、mysqldump と `--no-data` パラメーターを使用できます。 そのためには、ソースの MySQL データベースとターゲットの Azure Database for MySQL の両方に接続できるマシンが必要です。

mysqldump を使用してスキーマをエクスポートするには、次のコマンドを実行します。

```
mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
```

次に例を示します。

```
mysqldump -h 10.10.123.123 -u root -p --databases migtestdb --no-data > d:\migtestdb.sql
```

ターゲットの Azure Database for MySQL にスキーマをインポートするには、次のコマンドを実行します。

```
mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
 ```

次に例を示します。

```
mysql.exe -h mysqlsstrgt.mysql.database.azure.com -u docadmin@mysqlsstrgt -p migtestdb < d:\migtestdb.sql
 ```

スキーマに外部キーまたはトリガーが含まれている場合、移行中の並列データ読み込みが移行タスクによって処理されます。 スキーマの移行中に外部キーまたはトリガーをドロップする必要はありません。

[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-a-database-migration-service-instance"></a>Database Migration Service インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

    ![Azure Marketplace](media/tutorial-mysql-to-azure-mysql-offline-portal/03-dms-portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-mysql-to-azure-mysql-offline-portal/04-dms-portal-marketplace-create.png)
  
3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. 価格レベルを選択し、ネットワーク画面に移動します。 オフライン移行機能は、Premium の価格レベルでのみ使用できます。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service の基本設定を構成する](media/tutorial-mysql-to-azure-mysql-offline-portal/05-dms-portal-create-basic.png)

5. 既存の仮想ネットワークを一覧から選択するか、新しく作成する仮想ネットワークの名前を入力します。 [確認と作成] 画面に移動します。 必要に応じて、[タグ] 画面を使用してサービスにタグを追加できます。

    この仮想ネットワークが Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL Database インスタンスへのアクセスを提供します。

    ![Azure Database Migration Service のネットワーク設定を構成する](media/tutorial-mysql-to-azure-mysql-offline-portal/06-dms-portal-create-networking.png)

    Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。

6. 構成を確認し、 **[作成]** を選択してサービスを作成します。
    
    ![Azure Database Migration Service の作成](media/tutorial-mysql-to-azure-mysql-offline-portal/07-dms-portal-create-submit.png)

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。  

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. 検索結果から移行サービスのインスタンスを選択し、 **[+ 新しい移行プロジェクト]** を選択します。
    
    ![新しい移行プロジェクトを作成する](media/tutorial-mysql-to-azure-mysql-offline-portal/08-02-dms-portal-new-project.png)

3. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、 **[ソース サーバーの種類]** 選択ボックスで **[MySQL]** を、 **[ターゲット サーバーの種類]** 選択ボックスで **[Azure Database for MySQL]** を選択して、 **[Migration activity type]\(移行アクティビティの種類\)** 選択ボックスで **[Data migration]\(データの移行\)** を選択します。 **[アクティビティの作成と実行]** を選択します。

    ![Database Migration Service プロジェクトを作成する](media/tutorial-mysql-to-azure-mysql-offline-portal/09-dms-portal-project-mysql-create.png)

    > [!NOTE]
    > または、 **[プロジェクトのみを作成します]** を選択して移行プロジェクトを作成しておき、移行は後で実行することもできます。

## <a name="configure-migration-project"></a>移行プロジェクトを構成する

1. **[ソースの選択]** 画面で、ソース MySQL インスタンスの接続情報を指定し、 **[次へ: ターゲットの選択>>]** を選択します。

    ![[Add source details]\(ソースの詳細の追加\) 画面](media/tutorial-mysql-to-azure-mysql-offline-portal/10-dms-portal-project-mysql-source.png)

2. **[ターゲットの選択]** 画面で、ターゲット Azure Database for MySQL インスタンスの接続情報を指定し、 **[次へ: データベースの選択>>]** を選択します。

    ![[ターゲットの追加に関する詳細] 画面](media/tutorial-mysql-to-azure-mysql-offline-portal/11-dms-portal-project-mysql-target.png)

3. **[データベースの選択]** 画面で、移行のソース データベースとターゲット データベースをマッピングし、 **[次へ: 移行の設定の構成>>]** を選択します。 **[Make Source Server Read Only]\(ソース サーバーを読み取り専用にする\)** オプションを選択することでソースを読み取り専用にできますが、これはサーバー レベルの設定であることに注意してください。 選択した場合、選択したデータベースだけでなく、サーバー全体が読み取り専用に設定されます。
    
    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure Database Migration Service では、既定でターゲット データベースが選択されます。
    ![データベースの選択の詳細画面](media/tutorial-mysql-to-azure-mysql-offline-portal/12-dms-portal-project-mysql-select-db.png)
    
    > [!NOTE] 
    > この手順で複数のデータベースを選択することもできますが、コンピューティングは各データベースで共有されるため、この方法で移行できる DB の数や移行の速度には制限があります。 Premium SKU の既定の構成では、各移行タスクによって、2 つのテーブルを並列に移行することが試みられます。 それが、選択されたデータベースのうちのどのテーブルになるかは不定です。 これで十分な速度が得られない場合は、データベースの移行アクティビティを別々の移行タスクに分割し、複数のサービスにまたがるようにスケーリングしてください。 また、リージョンごとのサブスクリプションあたりの Azure Database Migration Service のインスタンス数は、10 に制限されています。
    > 移行のスループットと並列化をより細かく制御するには、記事「[PowerShell: DMS を使用して MySQL データベースから Azure Database for MySQL へのオフライン移行を実行する](./migrate-mysql-to-azure-mysql-powershell.md)」を参照してください。

4. **[移行の設定の構成]** 画面で、移行の対象となるテーブルを選択し、 **[Next : Summary>>]\(次へ: サマリー>>\)** を選択します。 ターゲット テーブルにデータが含まれている場合、それらは既定では選択されませんが、明示的に選択することもできます。その場合、テーブルに格納されているデータは、移行の開始前にすべて削除されます。

    ![テーブルの選択画面](media/tutorial-mysql-to-azure-mysql-offline-portal/13-dms-portal-project-mysql-select-tbl.png)

5. **[Summary]\(サマリー\)** 画面の **[アクティビティ名]** ボックスで、移行アクティビティの名前を指定します。サマリーを見直して、ソースとターゲットの詳細が先ほど指定した内容と一致していることを確認します。

    ![移行プロジェクトの概要](media/tutorial-mysql-to-azure-mysql-offline-portal/14-dms-portal-project-mysql-activity-summary.png)

6. **[移行の開始]** を選択します。 移行アクティビティ ウィンドウが表示されます。アクティビティの **[状態]** は **[初期化中]** になります。 テーブルの移行が開始されると、 **[状態]** が **[実行中]** に変わります。
 
     ![実行中の移行](media/tutorial-mysql-to-azure-mysql-offline-portal/15-dms-portal-project-mysql-running.png)

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で **[最新の情報に更新]** を選択して表示を更新し、完了したテーブル数についての進行状況を確認します。 

2. アクティビティ画面でデータベース名をクリックして、移行中の各テーブルの状態を確認します。 **[最新の情報に更新]** を選択して、表示を更新してください。 

     ![移行の監視](media/tutorial-mysql-to-azure-mysql-offline-portal/16-dms-portal-project-mysql-monitor.png)

## <a name="complete-the-migration"></a>移行を完了する

1. 移行アクティビティ画面で、移行の **[状態]** が **[完了]** になるまで **[最新の情報に更新]** を選択して表示を更新します。

    ![移行の完了](media/tutorial-mysql-to-azure-mysql-offline-portal/17-dms-portal-project-mysql-complete.png)

## <a name="post-migration-activities"></a>移行後のアクティビティ

オフライン移行の本番環境への導入はアプリケーションに依存するプロセスであり、このドキュメントで取り扱う範囲を超えていますが、移行後のアクティビティとしては次のような指針があります。

1. アプリケーションの要件に従って、ログイン、ロール、アクセス許可を作成します。
2. 移行前の手順で抽出したすべてのトリガーをターゲット データベースに再作成します。
3. ターゲット データベースに対してアプリケーションの健全性テストを実施して移行を認定します。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

今後 Database Migration Service を使用しない場合は、次の手順に従ってサービスを削除してください。

1. Azure ポータルで、 **[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![DMS のすべてのインスタンスを検索する](media/tutorial-mysql-to-azure-mysql-offline-portal/08-01-dms-portal-search-service.png)

2. 検索結果から移行サービスのインスタンスを選択し、 **[サービスの削除]** を選択します。
    
    ![移行サービスを削除する](media/tutorial-mysql-to-azure-mysql-offline-portal/18-dms-portal-delete.png)

3. 確認ダイアログで、 **[TYPE THE DATABASE MIGRATION SERVICE NAME]\(DATABASE MIGRATION SERVICE の名前を入力してください\)** ボックスにサービスの名前を入力し、 **[削除]** を選択します。

    ![移行サービスの削除を確定する](media/tutorial-mysql-to-azure-mysql-offline-portal/19-dms-portal-deleteconfirm.png)

## <a name="next-steps"></a>次の手順

* DMS を使用した移行の実行時の既知の問題と制限事項については、[Azure Database Migration Service の一般的な問題](./known-issues-troubleshooting-dms.md)に関する記事を参照してください。
* DMS の使用中に発生するソース データベースの接続の問題のトラブルシューティングについては、[ソース データベースへの接続時の問題](./known-issues-troubleshooting-dms-source-connectivity.md)に関する記事を参照してください。
* Azure Database Migration Service の詳細については、「[Azure Database Migration Service とは](./dms-overview.md)」を参照してください。
* Azure Database for MySQL の詳細については、「[Azure Database for MySQL とは](../mysql/overview.md)」の記事を参照してください。
* PowerShell から DMS を使用するうえでのガイダンスについては、記事「[PowerShell: DMS を使用して MySQL データベースから Azure Database for MySQL へのオフライン移行を実行する](./migrate-mysql-to-azure-mysql-powershell.md)」を参照してください。
