---
title: チュートリアル:SQL Server から SQL Managed Instance に移行する
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service を使用して、SQL Server から Azure SQL Managed Instance に移行する方法について説明します。
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 08/16/2021
ms.openlocfilehash: 0ff725703d96b524b9c6a45c84e95ae45af33cf8
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214649"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>チュートリアル:DMS を使用してオフラインで SQL Server を Azure SQL Managed Instance に移行する

Azure Database Migration Service を使用して、SQL Server インスタンスから [Azure SQL Managed Instance ](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) にデータベースを移行することができます。 一定の手作業が必要になる可能性のあるその他の方法については、記事「[Azure SQL Managed Instance に対する SQL Server ](../azure-sql/migration-guides/managed-instance/sql-server-to-managed-instance-guide.md)」を参照してください。

このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server のオンプレミス インスタンスから SQL Managed Instance に [AdventureWorks2016](/sql/samples/adventureworks-install-configure#download-backup-files) データベースを移行します。

学習内容:
> [!div class="checklist"]
>
> - Azure DataMigration リソース プロバイダーを登録する。
> - Azure Database Migration Service のインスタンスを作成する。
> - Azure Database Migration Service を使用して移行プロジェクトを作成する。
> - 移行を実行する。
> - 移行を監視する。

> [!IMPORTANT]
> SQL Server から SQL Managed Instance へのオフライン移行では、Azure Database Migration Service によって自動でバックアップ ファイルを作成できます。 または、サービスがデータベースの移行に使用する SMB ネットワーク共有に、データベースの最新の完全バックアップを提供することもできます。 各バックアップは、独立したバックアップ ファイルまたは複数のバックアップ ファイルに書き込まれます。 ただし、1 つのバックアップ メディアに複数のバックアップを追加することはサポートされていません。 なお、圧縮されたバックアップを使用して、大規模なバックアップの移行に関する潜在的な問題が発生する可能性を低減させることもできます。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

この記事では、SQL Server から SQL Managed Instance へのオフライン移行について説明します。 オンライン移行については、[DMS を使用してオンラインで SQL Server を Azure SQL Managed Instance に移行する方法](tutorial-sql-server-managed-instance-online.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下を実行する必要があります。

- [SQL Server 2016 以降](https://www.microsoft.com/sql-server/sql-server-downloads)をダウンロードしてインストールします。
- SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。
- [AdventureWorks2016 データベースを SQL Server インスタンスに復元します。](/sql/samples/adventureworks-install-configure#restore-to-sql-server)
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の Microsoft Azure 仮想ネットワークを作成します。これで、[ExpressRoute](../expressroute/expressroute-introduction.md) または [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。 [Azure Database Migration Service を使用した SQL Managed Instance の移行のネットワーク トポロジについて学習してください](./resource-network-topologies.md)。 仮想ネットワークの作成方法の詳細については、[Virtual Network のドキュメント](../virtual-network/index.yml)を参照してください。特に、詳細な手順が記載されたクイックスタートの記事を参照してください。

    > [!NOTE]
    > 仮想ネットワークのセットアップ中、Microsoft へのネットワーク ピアリングに ExpressRoute を使用する場合は、サービスのプロビジョニング先となるサブネットに、次のサービス [エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を追加してください。
    > - ターゲット データベース エンドポイント (SQL エンドポイント、Cosmos DB エンドポイントなど)
    > - ストレージ エンドポイント
    > - サービス バス エンドポイント
    >
    > Azure Database Migration Service にはインターネット接続がないため、この構成が必要となります。

- 仮想ネットワークのネットワーク セキュリティ グループの規則によって、ServiceBus、Storage、AzureMonitor の ServiceTag の送信ポート 443 がブロックされていないことを確認します。 仮想ネットワークの NSG トラフィックのフィルター処理の詳細については、[ネットワーク セキュリティ グループによるネットワーク トラフィックのフィルター処理](../virtual-network/virtual-network-vnet-plan-design-arm.md)に関する記事を参照してください。
- [ソース データベース エンジンへのアクセスのために Windows ファイアウォール](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure Database Migration Service でソース SQL Server にアクセスできるように Windows ファイアウォールを開放します。既定では TCP ポート 1433 が使用されます。 使用している既定のインスタンスが他のポートでリッスンしている場合は、それをファイアウォールに追加してください。
- 動的ポートを使用して複数の名前付き SQL Server インスタンスを実行している場合は、SQL Browser サービスを有効にし、ファイアウォール経由の UDP ポート 1434 へのアクセスを許可することをお勧めします。これにより、Azure Database Migration Service はソース サーバー上の名前付きインスタンスに接続できるようになります。
- ソース データベースの前でファイアウォール アプライアンスを使用する場合は、Azure Database Migration Service が移行のためにソース データベースにアクセスし、SMB ポート 445 経由でファイルにアクセスできるように、ファイアウォール規則を追加することが必要な場合があります。
- SQL マネージド インスタンスを作成します。手順の詳細については、[Azure portal で SQL マネージド インスタンスを作成する方法](../azure-sql/managed-instance/instance-create-quickstart.md)に関する記事を参照してください。
- ソースの SQL Server とターゲットの SQL Managed Instance の接続に使用するログインが、sysadmin サーバー ロールのメンバーであることを確認します。

    >[!NOTE]
    >既定では Azure Database Migration Service でサポートされているのは、SQL ログインの移行のみです。 ただし、次の方法により Windows ログインを移行する機能を有効にすることができます。
    >
    >- ターゲット SQL Managed Instance に AAD 読み取りアクセス権が付与されていることを確認します。これは、**グローバル管理者** ロールを持つユーザーが Azure portal を介して構成できます。
    >- Azure portal ([構成] ページ) を介して設定された Windows ユーザーまたはグループのログイン移行を有効にするように、ご利用の Azure Database Migration Service インスタンスを構成します。 この設定を有効にしたら、サービスを再起動して変更を有効にします。
    >
    > サービスを再起動すると、Windows ユーザーまたはグループのログインが、移行可能なログインの一覧に表示されます。 移行する Windows ユーザーまたはグループのログインについては、関連付けられているドメイン名を指定するように求められます。 サービス ユーザー アカウント (ドメイン名 NT AUTHORITY を含むアカウント) と仮想ユーザー アカウント (ドメイン名 NT SERVICE を含むアカウント名) はサポートされていません。

- Azure Database Migration Service がソース データベースのバックアップに使用できるネットワーク共有を作成します。
- 作成したネットワーク共有に対して、ソース SQL Server インスタンスを実行しているサービス アカウントが書き込み特権を持っていること、およびソース サーバーのコンピューター アカウントが読み取り/書き込みアクセス権を持っていることを確認します。
- 作成したネットワーク共有に対するフル コントロール権限を持つ Windows ユーザー (とパスワード) をメモしておきます。 Azure Database Migration Service は、ユーザーの資格情報を借用して、復元操作のために、Azure Storage コンテナーにバックアップ ファイルをアップロードします。
- Blob コンテナーを作成して、[ストレージ エクスプローラーを使用した Azure Blob Storage リソースの管理](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)に関するページの手順に従って、SAS URI を取得します。SAS URI の作成時には、必ずポリシー ウィンドウに表示されるすべてのアクセス許可 (読み取り、書き込み、削除、一覧表示) を選択してください。 この詳細情報により、Azure Database Migration Service は、対象のストレージ アカウント コンテナーにアクセスし、SQL Managed Instance にデータベースを移行するために使用されるバックアップ ファイルをアップロードできます。

    > [!NOTE]
    > Azure Database Migration Service では、[移行の設定の構成](#configure-migration-settings)ステップでストレージ アカウント設定を構成するときに、アカウント レベルの SAS トークンの使用がサポートされていません。
    
[!INCLUDE [resource-provider-register](../../includes/database-migration-service-resource-provider-register.md)]

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service インスタンスを作成する

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。 **[Azure Database Migration Service]** を検索して選択します。

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. **[Azure Database Migration Service]** 画面で、 **[作成]** を選択します。

    ![Azure Database Migration Service インスタンスを作成する](media/tutorial-sql-server-to-managed-instance/dms-create-service-1.png)

3. **[移行サービスの作成]** の [基本] 画面で、次の手順を実行します。

     - サブスクリプションを選択します。
     - 新しいリソース グループを作成するか、既存のリソース グループを選択します。
     - Azure Database Migration Service のインスタンスの名前を指定します。
     - Azure Database Migration Service のインスタンスを作成する場所を選択します。
     - サービス モードとして **[Azure]** を選択します。
     - 価格レベルを選択します。 コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

    ![Azure Database Migration Service インスタンスの基本設定を構成する](media/tutorial-sql-server-to-managed-instance/dms-create-service-2.png)

     - **[次へ: ネットワーク]** を選択します。

4. **[移行サービスの作成]** の [ネットワーク] 画面で、次の手順を実行します。

    - 既存の仮想ネットワークを選択するか、新しく作成します。 この仮想ネットワークによって、Azure Database Migration Service に、ソース SQL Server とターゲット Azure SQL Managed Instance へのアクセスが提供されます。
     
    - Azure portal で仮想ネットワークを作成する方法の詳細については、「[Azure portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)」を参照してください。
    
    - その他の詳細については、記事「[Azure Database Migration Service を使用して Azure SQL Managed Instance を移行するためのネットワーク トポロジ](./resource-network-topologies.md)」を参照してください。

    ![Azure Database Migration Service インスタンスのネットワーク設定を構成する](media/tutorial-sql-server-to-managed-instance/dms-create-service-3.png)

    - **[確認と作成]** を選択して詳細を確認し、 **[作成]** を選択してサービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する

サービスのインスタンスが作成されたら、Azure portal 内でそのサービスを探して開き、新しい移行プロジェクトを作成します。

1. Azure portal のメニューで、 **[すべてのサービス]** を選択します。 **[Azure Database Migration Service]** を検索して選択します。

    ![Azure Database Migration Service のすべてのインスタンスを検索する](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. **[Azure Database Migration Service]** 画面で、作成した Azure Database Migration Service インスタンスを選択します。

3. **[新しい移行プロジェクト]** を選択します。

     ![Azure Database Migration Service のインスタンスを検索する](media/tutorial-sql-server-to-managed-instance/dms-create-project-1.png)

4. **[新しい移行プロジェクト]** 画面でプロジェクトの名前を指定し、**[Source server type]\(ソース サーバーの種類\)** ボックスで **[SQL Server]** を選択した後、**[ターゲット サーバーの種類]** ボックスで **[Azure SQL Database Managed Instance]** を選択し、**[アクティビティの種類を選択します]** で **[オフライン データの移行]** を選択します。

   ![Database Migration Service プロジェクトを作成する](media/tutorial-sql-server-to-managed-instance/dms-create-project-2.png)

5. **[アクティビティの作成と実行]** を選択してプロジェクトを作成し、移行アクティビティを実行します。

## <a name="specify-source-details"></a>ソース詳細を指定する

1. **[ソースの選択]** 画面で、ソース SQL Server インスタンスの接続の詳細を指定します。

    ソース SQL Server インスタンス名には、必ず完全修飾ドメイン名 (FQDN) を使用してください。 DNS の名前解決ができない場合は、IP アドレスを使用することもできます。

2. 信頼できる証明書をサーバーにインストールしていない場合は、 **[サーバー証明書を信頼する]** チェック ボックスをオンにします。

    信頼できる証明書がインストールされていない場合、SQL Server はインスタンスの開始時に自己署名証明書を生成します。 この証明書は、クライアント接続の資格情報の暗号化に使用されます。

    > [!CAUTION]
    > 自己署名証明書で暗号化されている TLS 接続のセキュリティは強力ではありません。 man-in-the-middle (中間者) 攻撃を受ける可能性が高くなります。 実稼働環境やインターネットに接続しているサーバーでは、自己署名証明書を使用した TLS 接続は使用しないでください。

   ![ソースの詳細](media/tutorial-sql-server-to-managed-instance/dms-source-details.png)

3. **[次へ: ターゲットの選択]** を選択します。

## <a name="specify-target-details"></a>ターゲット詳細を指定する

1. **[ターゲットの選択]** 画面で、ターゲット (**AdventureWorks2016** データベースの移行先である事前プロビジョニング済み SQL Managed Instance) の接続詳細を指定します。

    SQL Managed Instance のプロビジョニングがまだ済んでいない場合は、インスタンスのプロビジョニングに役立つ[リンク](../azure-sql/managed-instance/instance-create-quickstart.md)を選択します。 そのままプロジェクトの作成を続行し、SQL Managed Instance の準備ができたら、この特定のプロジェクトに戻って移行を実行できます。

    ![ターゲットを選択する](media/tutorial-sql-server-to-managed-instance/dms-target-details.png)

2. **[次へ: データベースの選択]** を選択します。 **[データベースの選択]** 画面で、移行する **AdventureWorks2016** データベースを選択します。

   ![ソース データベースを選択する](media/tutorial-sql-server-to-managed-instance/dms-source-database.png)

    > [!IMPORTANT]
    > SQL Server Integration Services (SSIS) を使用する場合、DMS では現在、SQL Server から SQL Managed Instance への SSIS プロジェクトまたはパッケージ (SSISDB) 用のカタログ データベースの移行がサポートされていません。 ただし、Azure Data Factory (ADF) に SSIS をプロビジョニングした後、SQL Managed Instance によってホストされている移行先 SSISDB に SSIS プロジェクトまたはパッケージを再デプロイすることはできます。 SSIS パッケージの移行の詳細については、記事「[SQL Server Integration Services パッケージを Azure に移行する](./how-to-migrate-ssis-packages.md)」を参照してください。

3. **[次へ: ログインの選択]** を選択します。

## <a name="select-logins"></a>ログインを選択する

1. **[ログインの選択]** 画面で、移行するログインを選択します。

    >[!NOTE]
    >既定では Azure Database Migration Service でサポートされているのは、SQL ログインの移行のみです。 Windows ログインの移行に対するサポートを有効にするには、このチュートリアルの「**前提条件**」セクションを参照してください。

    ![ログインを選択する](media/tutorial-sql-server-to-managed-instance/dms-select-logins.png)

2. **[次へ: 移行の設定の構成]** を選択します。

## <a name="configure-migration-settings"></a>移行の設定の構成

1. **[移行の設定の構成]** 画面で、次の詳細情報を入力します。

    | パラメーター | 説明 |
    |--------|---------|
    |**ソースのバックアップ オプションを選択します** | DMS がデータベースの移行に使用できる完全バックアップ ファイルが既に存在する場合は、 **[最新のバックアップ ファイルを自分で用意する]** オプションを選択します。 ソース データベースの完全バックアップを最初に DMS で作成し、それを移行に使用する場合は、 **[I will let Azure Database Migration Service create backup files]\(バックアップ ファイルを Azure Database Migration Service で作成する\)** を選択します。 |
    |**ネットワーク共有の場所** | Azure Database Migration Service がソース データベース バックアップを移行できるローカル SMB ネットワーク共有です。 ソースの SQL Server インスタンスを実行しているサービス アカウントには、このネットワーク共有に対する書き込み権限が必要です。 たとえば、ネットワーク共有のサーバーの FQDN または IP アドレスを "\\\servername.domainname.com\backupfolder" または "\\\IP address\backupfolder" と指定します。|
    |**ユーザー名** | 上で指定したネットワーク共有に対するフル コントロール権限が Windows ユーザーにあることを確認してください。 Azure Database Migration Service により、ユーザーの資格情報を借用することで、復元操作のために、Azure Storage コンテナーにバックアップ ファイルがアップロードされます。 TDE 対応データベースが選択されている場合、Azure Database Migration Service で証明書ファイルをアップロードしたり削除したりするために、上記の Windows ユーザーはビルトイン Administrator アカウントでなければならず、[ユーザー アカウント制御](/windows/security/identity-protection/user-account-control/user-account-control-overview)は無効にする必要があります。 |
    |**パスワード** | ユーザーのパスワード。 |
    |**ストレージ アカウントの設定** | サービスがバックアップ ファイルをアップロードするストレージ アカウント コンテナーへのアクセスを Azure Database Migration Service に提供する SAS URI。これが、データベースを SQL Managed Instance に移行するために使用されます。 [Blob コンテナーの SAS URI を取得する方法について説明します](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。 この SAS URI は、ストレージ アカウントではなく、BLOB コンテナー用である必要があります。|
    |**TDE の設定** | Transparent Data Encryption (TDE) が有効になっているソース データベースを移行する場合は、ターゲット SQL マネージド インスタンスに対する書き込み特権が必要です。  SQL Managed Instance がプロビジョニングされているサブスクリプションをドロップダウン メニューから選択します。  ドロップダウン メニューでターゲットの **[Azure SQL Database Managed Instance]** を選択します。 |

    ![移行設定の構成](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings.png)

2. **概要** を選択します。

## <a name="review-the-migration-summary"></a>移行の概要を確認する

1. **[概要]** 画面の **[アクティビティ名]** テキスト ボックスに、移行アクティビティの名前を指定します。

2. 移行プロジェクトに関連付けられた詳細を確認します。

    ![移行プロジェクトの概要](media/tutorial-sql-server-to-managed-instance/dms-project-summary.png)

## <a name="run-the-migration"></a>移行を実行する

- **[移行の開始]** を選択します。

  移行アクティビティ ウィンドウが開き、データベースとログインの現在の移行状態が表示されます。

## <a name="monitor-the-migration"></a>移行を監視する

1. 移行アクティビティ画面で、 **[更新]** を選択して表示を更新します。

   ![移行アクティビティ画面と [更新] ボタンを示すスクリーンショット。](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration.png)

2. データベースとログインのカテゴリを展開して、該当するサーバー オブジェクトの移行状態を監視できます。

   ![進行中の移行アクティビティ](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

3. 移行が完了したら、SQL Managed Instance 環境のターゲット データベースを確認します。

## <a name="additional-resources"></a>その他のリソース

- T-SQL RESTORE コマンドを使用して SQL Managed Instance にデータベースを移行する方法を示したチュートリアルについては、[復元コマンドを使用した SQL Managed Instance へのバックアップの復元](../azure-sql/managed-instance/restore-sample-database-quickstart.md)に関するページを参照してください。
- SQL Managed Instance については、「[Azure SQL Managed Instance とは](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)」を参照してください。
- SQL Managed Instance へのアプリの接続については、[アプリケーションの接続](../azure-sql/managed-instance/connect-application-instance.md)に関するページを参照してください。
