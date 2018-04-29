---
title: DMS を使用して Azure SQL Database マネージド インスタンスに移行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの SQL Server から Azure SQL Database マネージ インスタンスに移行する方法を学習します。
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 04/10/2018
ms.openlocfilehash: 6628ea218c4c7a9aacc0c2899c1ea4e5b6169b51
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2018
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>DMS を使用して SQL Server を Azure SQL Database マネージド インスタンスに移行する
Azure Database Migration Service を使用して、オンプレミスの SQL Server インスタンスから [Azure SQL Database マネージド インスタンス](../sql-database/sql-database-managed-instance.md)に、ほぼダウンタイムなしでデータベースを移行することができます。 一定のダウンタイムを必要とするその他の方法については、「[Azure SQL Database マネージド インスタンスへの SQL Server インスタンスの移行](../sql-database/sql-database-managed-instance-migrate.md)」を参照してください。

このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server のオンプレミス インスタンスからの **Adventureworks2012** データベースを、Azure SQL Database に移行します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure Database Migration Service インスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下を実行する必要があります。

- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の VNET を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 [Azure Database Migration Service を使用して Azure SQL DB マネージ インスタンスを移行するためのネットワーク トポロジについて説明します](https://aka.ms/dmsnetworkformi)。
- Azure Virtual Network (VNET) のネットワーク セキュリティ グループの規則によって、通信ポート 443、53、9354、445、12000 がブロックされていないことを確認します。 Azure VNET NSG トラフィックのフィルター処理の詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-nsg)」を参照してください。
- [ソース データベース エンジンへのアクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスし、SMB ポート 445 経由でファイルにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- Azure SQL Database マネージド インスタンスを作成します。手順の詳細については、「[Create an Azure SQL Database Managed Instance in the Azure portal](https://aka.ms/sqldbmi)」 (Azure Portal で Azure SQL Database マネージド インスタンスを作成する) を参照してください。
- ソース SQL Server への接続と、ターゲットのマネージ インスタンスに使用するログインが sysadmin サーバー ロールのメンバーであることを確認してください。
- Azure Database Migration Service がソース データベースのバックアップに使用できるネットワーク共有を作成します。
- ソース SQL Server インスタンスを実行しているサービス アカウントに、作成したネットワーク共有に対する書き込み権限があることを確認します。
- 上記で作成したネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。
- Blob コンテナーを作成して、[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)に関するページの手順に従って、SAS URI を取得します。SAS URI の作成時には、必ずポリシー ウィンドウに表示されるすべてのアクセス許可 (読み取り、書き込み、削除、一覧表示) を選択してください。 これにより、Azure Database Migration Service はストレージ アカウント コンテナーにアクセスし、Azure SQL Database マネージ インスタンスにデータベースを移行するために使用されるバックアップ ファイルをアップロードできます

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1.  Azure Portal にログインし、**[すべてのサービス]** を選択して **[サブスクリプション]** を選択します。
![ポータルのサブスクリプションの表示](media\tutorial-sql-server-to-managed-instance\portal-select-subscription.png)

1.  Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、**[リソース プロバイダー]** を選択します。
![リソース プロバイダーの表示](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

1.  移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。
![リソース プロバイダーの登録](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)    

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

1.  Azure Portal で **[+ リソースの作成]** を選択し、**Azure Database Migration Service** を検索して、ドロップダウン リストから **Azure Database Migration Service** を選択します。

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

1.  **Azure Database Migration Service (preview)\(Azure Database Migration Service (プレビュー)\)** 画面で、**[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media\tutorial-sql-server-to-managed-instance\dms-create.png)

1.  **[Database Migration Service]** 画面で、サービスの名前、サブスクリプション、リソース グループ、仮想ネットワーク、および価格レベルを指定します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。 *Azure Database Migration Service は現在、プレビュー段階で、課金されません。*

    **ネットワーク:** 既存の VNET を選択するか、新しい VNET を作成して、Azure Database Migration Service に、ソース SQL Server とターゲットの Azure SQL Database マネージ インスタンスへのアクセスを提供します。 [Azure Database Migration Service を使用して Azure SQL DB マネージ インスタンスを移行するためのネットワーク トポロジについて説明します](https://aka.ms/dmsnetworkformi)。

    Azure ポータルで VNET を作成する方法の詳細については、「[Azure ポータルを使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)」を参照してください。

    ![DMS サービスを作成する](media\tutorial-sql-server-to-managed-instance\dms-create-service.png)

1.  **[作成]** を選択して、サービスを作成します。


## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure ポータル内でそのサービスを特定して開きます。

1.  **[+ New Migration Project]\(+ 新しい移行プロジェクト\)** を選択します。

1.  **[New Migration Project]\(新しい移行プロジェクト\)** 画面でプロジェクトの名前を指定し、**[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、**[対象サーバーの種類]** テキスト ボックスで **[Azure SQL Database マネージ インスタンス]** を選択します。

    ![DMS のプロジェクトを作成する](media\tutorial-sql-server-to-managed-instance\dms-create-project.png)

1.  **[作成]** を選択してプロジェクトを作成します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1.  **[ソースの詳細]** 画面で、ソース SQL Server の接続詳細を指定します。

    ![ソースの詳細](media\tutorial-sql-server-to-managed-instance\dms-source-details.png)

1.  **[保存]** を選択し、移行する **AdventureWorks2012** データベースを選択します。

    ![ソース データベースを選択する](media\tutorial-sql-server-to-managed-instance\dms-source-database.png)

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1.  **[保存]** を選択し、**[対象の詳細]** 画面で、ターゲットの接続情報を指定します。このターゲットは、**AdventureWorks2012** データベースが移行される、事前プロビジョニング済みの Azure SQL Database マネージ インスタンスです。

    ![ターゲットを選択する](media\tutorial-sql-server-to-managed-instance\dms-target-details.png)

1.  **[保存]** を選択します。

1.  **[Project summary]\(プロジェクトの概要\)** 画面で、移行プロジェクトに関連付けられた詳細を確認します。

## <a name="run-the-migration"></a>移行を実行する

1.  先ほど保存したプロジェクトを選択し、**[+ 新しいアクティビティ]** を選択して、**[移行の実行]** を選択します。

    ![新しいアクティビティを作成する](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity.png)

1.  メッセージが表示されたら、ソース サーバーと対象サーバーの資格情報を入力し、**[保存]** を選択します。

1.  **[Map to target databases]\(ターゲット データベースへのマッピング\)** 画面で、移行するソース データベースを選択します。

    ![ソース データベースを選択する](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases.png)

1.  **[Configure migration settings]\(移行設定の構成\)** 画面で、**[保存]** を選択し、次の情報を入力します。

    | | |
    |--------|---------|
    |**サーバー バックアップの場所** | Azure Database Migration Service がソース データベース バックアップを移行できるローカル ネットワーク共有です。 ソースの SQL Server インスタンスを実行しているサービス アカウントには、このネットワーク共有に対する書き込み権限が必要です。 |
    |**ユーザー名** | Azure Database Migration Service が、復元操作の目的で Azure ストレージ コンテナーにバックアップ ファイルをアップロードするために借用できる、Windows ユーザー名です。 |
    |**パスワード** | 上記ユーザーのパスワード。 |
    |**ストレージの SAS URI** | サービスがバックアップ ファイルをアップロードするストレージ アカウント コンテナーへのアクセスを、Azure Database Migration Service に提供する SAS URI。これが、データベースを Azure SQL Database マネージ インスタンスに移行するために使用されます。 [Blob コンテナーの SAS URI を取得する方法について説明します](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。|
    
    ![移行設定の構成](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings.png)

1.  [Migration summary]\(移行の概要\) 画面で **[保存]** を選択し、**[アクティビティ名]** テキスト ボックスに移行アクティビティの名前を指定します。

    ![移行の概要](media\tutorial-sql-server-to-managed-instance\dms-migration-summary.png)


## <a name="monitor-the-migration"></a>移行を監視する

1.  移行アクティビティを選択して、アクティビティの状態を確認します。

1.  移行が完了したら、ターゲットの Azure SQL Database マネージ インスタンス上にターゲット データベースがあることを確認します。

    ![移行を監視する](media\tutorial-sql-server-to-managed-instance\dms-monitor-migration.png)

## <a name="next-steps"></a>次の手順

- T-SQL RESTORE コマンドを使用してマネージド インスタンスにデータベースを移行する方法を示したチュートリアルについては、[復元コマンドを使用したマネージド インスタンスへのバックアップの復元](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)に関するページを参照してください。
- BACPAC ファイルからのデータベースのインポートに関する詳細については、「[BACPAC ファイルを新しい Azure SQL Database にインポートする](../sql-database/sql-database-import.md)」を参照してください。
- マネージ インスタンスについては、「[What is a Managed Instance? (マネージ インスタンスとは)](../sql-database/sql-database-managed-instance.md)」をご覧ください。
- マネージド インスタンスへのアプリケーションの接続については、[アプリケーションの接続](../sql-database/sql-database-managed-instance-connect-app.md)に関するページを参照してください。
