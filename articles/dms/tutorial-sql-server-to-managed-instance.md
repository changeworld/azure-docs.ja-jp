---
title: DMS を使用して Azure SQL Database Managed Instance に移行する | Microsoft Docs
description: Azure Database Migration Service を使用して、オンプレミスの SQL Server から Azure SQL Database Managed Instance に移行する方法を学習します。
services: dms
author: edmacauley
ms.author: edmaca
manager: craigg
ms.reviewer: ''
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/12/2018
ms.openlocfilehash: c911b096af6662e11afb4c4262b92c239d252c36
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990229"
---
# <a name="migrate-sql-server-to-azure-sql-database-managed-instance-using-dms"></a>DMS を使用して SQL Server を Azure SQL Database Managed Instance に移行する
Azure Database Migration Service を使用して、オンプレミスの SQL Server インスタンスから [Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance.md) にデータベースを移行することができます。 一定の手作業が必要になる可能性のあるその他の方法については、記事「[Azure SQL Database Managed Instance への SQL Server インスタンスの移行](../sql-database/sql-database-managed-instance-migrate.md)」を参照してください。

> [!IMPORTANT]
> SQL Server から Azure SQL Database Managed Instance へのプロジェクトの移行はプレビュー段階にあり、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)の対象です。

このチュートリアルでは、Azure Database Migration Service を使用して、オンプレミスの SQL Server インスタンスから Azure SQL Database Managed Instance に **Adventureworks2012** データベースを移行します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。
> * 移行レポートをダウンロードする。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下を実行する必要があります。

- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の VNET を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 
  [Azure Database Migration Service を使用して Azure SQL DB Managed Instance を移行するためのネットワーク トポロジを学習します](https://aka.ms/dmsnetworkformi)。
- Azure Virtual Network (VNET) のネットワーク セキュリティ グループの規則によって、通信ポート 443、53、9354、445、12000 がブロックされていないことを確認します。 Azure VNET NSG トラフィックのフィルター処理の詳細については、「[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルタリング](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)」を参照してください。
- [ソース データベース エンジンへのアクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。既定では TCP ポート 1433 が使用されます。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスし、SMB ポート 445 経由でファイルにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- Azure SQL Database マネージド インスタンスを作成します。手順の詳細については、「[Create an Azure SQL Database Managed Instance in the Azure portal](https://aka.ms/sqldbmi)」 (Azure Portal で Azure SQL Database マネージド インスタンスを作成する) を参照してください。
- ソース SQL Server への接続と、ターゲットのマネージド インスタンスに使用するログインが sysadmin サーバー ロールのメンバーであることを確認してください。
- Azure Database Migration Service がソース データベースのバックアップに使用できるネットワーク共有を作成します。
- 作成したネットワーク共有に対して、ソース SQL Server インスタンスを実行しているサービス アカウントが書き込み特権を持っていること、およびソース サーバーのコンピューター アカウントが読み取り/書き込みアクセス権を持っていることを確認します。
- 作成したネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure ストレージ コンテナーにバックアップ ファイルをアップロードします。
- Blob コンテナーを作成して、[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)に関するページの手順に従って、SAS URI を取得します。SAS URI の作成時には、必ずポリシー ウィンドウに表示されるすべてのアクセス許可 (読み取り、書き込み、削除、一覧表示) を選択してください。 これにより、Azure Database Migration Service はストレージ アカウント コンテナーにアクセスし、Azure SQL Database Managed Instance にデータベースを移行するために使用されるバックアップ ファイルをアップロードできます

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する

1. Azure Portal にログインし、**[すべてのサービス]** を選択して **[サブスクリプション]** を選択します。

    ![ポータルのサブスクリプションの表示](media\tutorial-sql-server-to-managed-instance\portal-select-subscriptions.png)        

2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、**[リソース プロバイダー]** を選択します。

    ![リソース プロバイダーの表示](media\tutorial-sql-server-to-managed-instance\portal-select-resource-provider.png)

3. 移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。

    ![リソース プロバイダーの登録](media\tutorial-sql-server-to-managed-instance\portal-register-resource-provider.png)   

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

1. Azure portal で **[+ リソースの作成]** を選択し、**Azure Database Migration Service** を検索して、ドロップダウン リストから **[Azure Database Migration Service]** を選択します。

     ![Azure Marketplace](media\tutorial-sql-server-to-managed-instance\portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、**[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media\tutorial-sql-server-to-managed-instance\dms-create1.png)

3. **[移行サービスの作成]** 画面で、サービスの名前、サブスクリプション、新規または既存のリソース グループを指定します。

4. 既存の仮想ネットワーク (VNET) を選択するか、新たに VNET を作成します。
 
    この VNET が Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL Database Managed Instance へのアクセスを提供します。

    Azure portal で VNET を作成する方法の詳細については、[Azure portal を使用した仮想ネットワークの作成](https://aka.ms/DMSVnet)に関する記事を参照してください。

    その他の詳細については、記事「[Azure Database Migration Service を使用して Azure SQL DB Managed Instance を移行するためのネットワーク トポロジ](https://aka.ms/dmsnetworkformi)」を参照してください。

5. 価格レベルを選択します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。
   
    ![DMS サービスを作成する](media\tutorial-sql-server-to-managed-instance\dms-create-service1.png)

6.  **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure ポータルで、**[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media\tutorial-sql-server-to-azure-sql\dms-search.png)

2. **[Azure Database Migration Service]** 画面で、作成したインスタンスの名前を検索して選択します。
 
3. **[+ 新しい移行プロジェクト]** を選択します。

4. 
  **[New Migration Project]\(新しい移行プロジェクト\)** 画面でプロジェクトの名前を指定し、**[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、**[対象サーバーの種類]** テキスト ボックスで **[Azure SQL Database Managed Instance]** を選択します。

   ![DMS のプロジェクトを作成する](media\tutorial-sql-server-to-managed-instance\dms-create-project1.png)

5. **[作成]** を選択してプロジェクトを作成します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの詳細]** 画面で、ソース SQL Server の接続詳細を指定します。

2. 信頼できる証明書をサーバーにインストールしていない場合は、**[Trust server certificate]\(サーバー証明書を信頼する\)** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書を使用して暗号化されている SSL 接続のセキュリティは強力ではありません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 運用環境や、インターネットに接続されているサーバーでは、自己署名証明書を使用した SSL を信頼しないでください。

   ![ソースの詳細](media\tutorial-sql-server-to-managed-instance\dms-source-details1.png)

3. **[保存]** を選択します。

4. **[ソース データベースの選択]** 画面で、移行する **Adventureworks2012** データベースを選択します。

   ![ソース データベースを選択する](media\tutorial-sql-server-to-managed-instance\dms-source-database1.png)

5. **[保存]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1.  **[対象の詳細]** 画面で、ターゲットの接続情報を指定します。このターゲットは、**AdventureWorks2012** データベースの移行先である、事前プロビジョニング済みの Azure SQL Database Managed Instance です。

    Azure SQL Database Managed Instance のプロビジョニングがまだ済んでいない場合は、インスタンスのプロビジョニングに役立つリンクの **[いいえ]** を選択します。 そのままプロジェクトの作成を続行し、Azure SQL Database Managed Instance の準備ができたら、この特定のプロジェクトに戻って移行を実行できます。   
 
       ![ターゲットを選択する](media\tutorial-sql-server-to-managed-instance\dms-target-details1.png)

2.  **[保存]** を選択します。

3.  **[Project summary]\(プロジェクトの概要\)** 画面で、移行プロジェクトに関連付けられた詳細を確認します。
 
    ![移行プロジェクトの概要](media\tutorial-sql-server-to-managed-instance\dms-project-summary1.png)

4.  **[保存]** を選択します。   

## <a name="run-the-migration"></a>移行を実行する

1.  先ほど保存したプロジェクトを選択し、**[+ 新しいアクティビティ]**、**[移行の実行]** の順に選択します。

    ![新しいアクティビティを作成する](media\tutorial-sql-server-to-managed-instance\dms-create-new-activity1.png)

2.  メッセージが表示されたら、ソース サーバーと対象サーバーの資格情報を入力し、**[保存]** を選択します。

3.  **[ソース データベースの選択]** 画面で、移行するソース データベースを選択します。

    ![ソース データベースを選択する](media\tutorial-sql-server-to-managed-instance\dms-select-source-databases2.png)

4.  **[保存]** を選択し、**[ログインの選択]** 画面で、移行するログインを選択します。

    現在のリリースでは、SQL ログインの移行のみをサポートします。

    ![ログインを選択する](media\tutorial-sql-server-to-managed-instance\dms-select-logins.png)

5. **[保存]** を選択した後、**[移行の設定の構成]** 画面で、次の情報を入力します。

    | | |
    |--------|---------|
    |**ネットワーク共有の場所** | Azure Database Migration Service がソース データベース バックアップを移行できるローカル ネットワーク共有です。 ソースの SQL Server インスタンスを実行しているサービス アカウントには、このネットワーク共有に対する書き込み権限が必要です。 たとえば、ネットワーク共有のサーバーの FQDN または IP アドレスを "\\\servername.domainname.com\backupfolder" または "\\\IP address\backupfolder" と指定します。|
    |**ユーザー名** | Azure Database Migration Service が、復元操作の目的で Azure ストレージ コンテナーにバックアップ ファイルをアップロードするために借用できる、Windows ユーザー名です。 |
    |**パスワード** | ユーザーのパスワード。 |
    |**ストレージ アカウントの設定** | サービスがバックアップ ファイルをアップロードするストレージ アカウント コンテナーへのアクセスを、Azure Database Migration Service に提供する SAS URI。これが、データベースを Azure SQL Database Managed Instance に移行するために使用されます。 [Blob コンテナーの SAS URI を取得する方法について説明します](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container)。|
    
    ![移行設定の構成](media\tutorial-sql-server-to-managed-instance\dms-configure-migration-settings2.png)

5.  **[保存]** を選択した後、**[移行の概要]** 画面の **[アクティビティ名]** テキスト ボックスで、移行アクティビティの名前を指定します。

    ![移行の概要](media\tutorial-sql-server-to-managed-instance\dms-migration-summary2.png)

6. **[検証オプション]** セクションを展開して **[検証オプションの選択]** 画面を表示し、移行したデータベースでクエリの正確さを検証するかどうかを指定して、**[保存]** を選択します。  

7. **[移行の実行]** を選択します。

    移行アクティビティ ウィンドウが表示され、アクティビティの [状態] は **[Pending]\(保留\)** になっています。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、**[更新]** を選択して表示を更新します。
 
   ![進行中の移行アクティビティ](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-in-progress.png)

2. データベースとログインのカテゴリを展開して、該当するサーバー オブジェクトの移行状態を監視できます。

   ![進行中の移行アクティビティ](media\tutorial-sql-server-to-managed-instance\dms-migration-activity-monitor.png)

3. 移行が完了したら、**[レポートのダウンロード]** を選択して、移行プロセスに関連する詳細情報を一覧表示したレポートを取得します。
 
4. ターゲットの Azure SQL Database Managed Instance 環境にターゲット データベースがあることを確認します。

## <a name="next-steps"></a>次の手順

- T-SQL RESTORE コマンドを使用してマネージド インスタンスにデータベースを移行する方法を示したチュートリアルについては、[復元コマンドを使用したマネージド インスタンスへのバックアップの復元](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)に関するページを参照してください。
- マネージド インスタンスについては、「[What is a Managed Instance? (マネージド インスタンスとは)](../sql-database/sql-database-managed-instance.md)」をご覧ください。
- マネージド インスタンスへのアプリケーションの接続については、[アプリケーションの接続](../sql-database/sql-database-managed-instance-connect-app.md)に関するページを参照してください。
