---
title: "Azure Database Migration Service を使用して SQL Server を Azure SQL Database に移行する | Microsoft Docs"
description: "Azure Database Migration Service を使用して、オンプレミスの SQL Server から Azure SQL に移行する方法を学習します。"
services: dms
author: HJToland3
ms.author: jtoland
manager: jhubbard
ms.reviewer: 
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 11/17/2017
ms.openlocfilehash: 3e7e80d58a3eb27920736a1594633021b90014e9
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2017
---
# <a name="migrate-sql-server-to-azure-sql-database"></a>SQL Server を Azure SQL Database に移行する
Azure Database Migration Service を使用して、オンプレミスの SQL Server インスタンスから Azure SQL Database にデータベースを移行することができます。 このチュートリアルでは、Azure Database Migration Service を使用して、SQL Server 2016 (以上) のオンプレミス インスタンスに復元された**Adventureworks2012** データベースを、Azure SQL Database に移行します。

このチュートリアルで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Data Migration Assistant を使用して、オンプレミス データベースを評価する。
> * Data Migration Assistant を使用して、サンプル スキーマを移行する。
> * Azure Database Migration Service のインスタンスを作成する。
> * Azure Database Migration Service を使用して移行プロジェクトを作成する。
> * 移行を実行する。
> * 移行を監視する。

## <a name="prerequisites"></a>前提条件
このチュートリアルを完了するには、以下を実行する必要があります。

- [SQL Server 2016 以降](https://www.microsoft.com/sql-server/sql-server-downloads) (任意のエディション) をダウンロードしてインストールします。
- SQL Server Express のインストール時に既定では無効になっている TCP/IP プロトコルを有効にします。有効にする手順については、[サーバー ネットワーク プロトコルの有効化または無効化](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)に関する記事を参照してください。
- [データベース エンジン アクセスのために Windows ファイアウォール](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)を構成します。
- Azure SQL Database インスタンスを作成します。作成する場合は「[Azure Portal で Azure SQL データベースを作成する](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)」の手順に従います。
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 以降をダウンロードしてインストールします。
- Azure Resource Manager デプロイ モデルを使用して、Azure Database Migration Service 用の VNET を作成します。これで、[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) または [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) を使用したオンプレミスのソース サーバーとのサイト間接続を確立します。
- ソースの SQL Server インスタンスへの接続に使用される資格情報に、[CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) アクセス許可を含めます。
- ターゲットの Azure SQL Database インスタンスへの接続に使用される資格情報に、ターゲットの Azure SQL データベースに対する CONTROL DATABASE アクセス許可を含めます。
- Azure Database Migration Service がソースの SQL Server にアクセスできるように Windows ファイアウォールを開きます。

## <a name="assess-your-on-premises-database"></a>オンプレミス データベースを評価する
オンプレミスの SQL Server インスタンスから Azure SQL Database にデータを移行する前に、SQL Server データベースを評価して、移行を妨げる問題がないかどうか確認する必要があります。 Data Migration Assistant v3.3 以降で、[SQL Server の移行評価の実行](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)に関するページの手順を実行して、オンプレミス データベースの評価を完了してください。 必要な手順の概要は次のとおりです。
1.  Data Migration Assistant で、新規 (+) アイコンを選択し、**[評価]** プロジェクト タイプを選択します。
2.  プロジェクト名を指定し、**[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、**[対象サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。
3.  **[作成]** を選択してプロジェクトを作成します。

    Azure SQL Database に移行するソース SQL Server データベースを評価する際には、次の両方の評価レポート タイプを選択できます。
    - データベース互換性をチェックする
    - 機能の類似性をチェックする

    どちらのレポート タイプも、既定で選択されています。
4.  Data Migration Assistant の **[オプション]** 画面で、**[次へ]** を選択します。
5.  **[ソースの選択]** 画面の **[サーバーへの接続]** ダイアログ ボックスで、SQL Server への接続詳細を入力し、**[接続]** を選択します。
6.  **[Add sources]\(ソースの追加\)** ダイアログ ボックスで、**AdventureWorks2012**、**[追加]**、**[Start Assessment]\(評価の開始\)** の順に選択します。

    評価が完了すると、次のグラフィックに結果が表示されます。

    ![データ移行の評価](media\tutorial-sql-server-to-azure-sql\dma-assessments.png)

    評価では、Azure SQL Database について、移行を妨げる問題と機能の類似性の問題が特定されます。

7.  特定のオプションを選択して、移行を妨げる問題と機能の類似性の問題についての評価結果をレビューします。
    - **SQL Server の機能類似性**カテゴリでは、幅広い推奨事項や、Azure で利用できる代替アプローチ、および移行プロジェクトの計画に役立つ移行手順を確認できます。
    - **互換性問題**カテゴリでは、部分的にサポートされている機能やサポートされていない機能を確認できます。この情報は、オンプレミスの SQL Server データベースから Azure SQL Database への移行を妨げる可能性がある互換性の問題を反映しています。 また、それらの問題への対処に役立つ推奨事項も確認できます。


## <a name="migrate-the-sample-schema"></a>サンプル スキーマを移行する
評価結果をレビューし、選択したデータベースが Azure SQL Database に対して適格であることを確認したら、Data Migration Assistant を使用してスキーマを Azure SQL Database に移行します。

> [!NOTE]
> Data Migration Assistant で移行プロジェクトを作成する前に、Azure SQL データベースが前提要件での説明に従って既にプロビジョニングされていることを確認してください。 このチュートリアルでは、Azure SQL Database の名前は **AdventureWorksAzure** としますが、必要に応じて別の名前を使用することもできます。

**AdventureWorks2012** スキーマを Azure SQL Database に移行するには、次の手順を実行します。

1.  Data Migration Assistant で、新規 (+) アイコンを選択し、**[プロジェクト タイプ]** で **[移行]** を選択します。
3.  プロジェクト名を指定し、**[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、**[対象サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。
4.  **[移行スコープ]** で、**[Schema only]\(スキーマのみ\)** を選択します。

    上記の手順を実行すると、Data Migration Assistant のインターフェイスの表示が次の図のようになります。
    
    ![Data Migration Assistant プロジェクトを作成する](media\tutorial-sql-server-to-azure-sql\dma-create-project.png)

5.  **[作成]** を選択してプロジェクトを作成します。
6.  Data Migration Assistant で、SQL Server のソース接続詳細を指定し、**[接続]** を選択した後、**AdventureWorks2012** データベースを選択します。

    ![Data Migration Assistant のソース接続詳細](media\tutorial-sql-server-to-azure-sql\dma-source-connect.png)
7.  **[次へ]** を選択し、**[Connect to target server]\(対象サーバーへの接続\)** で Azure SQL データベースのターゲット接続の詳細を指定し、**[接続]** を選択し、Azure SQL データベースで事前プロビジョニングした **AdventureWorksAzure** データベースを選択します。

    ![Data Migration Assistant のターゲット接続詳細](media\tutorial-sql-server-to-azure-sql\dma-target-connect.png)
8.  **[次へ]** を選択して **[オブジェクトの選択]** 画面に進みます。この画面では、Azure SQL Database にデプロイする必要がある **AdventureWorks2012** データベース内のスキーマ オブジェクトを指定できます。

    既定では、すべてのオブジェクトが選択されています。

    ![SQL スクリプトを生成する](media\tutorial-sql-server-to-azure-sql\dma-assessment-source.png)
9.  **[SQL スクリプトの生成]** を選択して SQL スクリプトを作成し、スクリプトにエラーがないかを確認します。

    ![スキーマ スクリプト](media\tutorial-sql-server-to-azure-sql\dma-schema-script.png)
10. **[Deploy schema]\(スキーマのデプロイ\)** を選択して Azure SQL データベースにスキーマをデプロイし、スキーマがデプロイされたら、対象サーバーに異常がないかをチェックします。

    ![スキーマをデプロイする](media\tutorial-sql-server-to-azure-sql\dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration リソース プロバイダーを登録する
1. Azure Portal にログインし、**[すべてのサービス]** を選択して **[サブスクリプション]** を選択します。
 
   ![ポータルのサブスクリプションの表示](media\tutorial-sql-server-to-azure-sql\portal-select-subscription.png)
       
2. Azure Database Migration Service のインスタンスを作成するサブスクリプションを選択して、**[リソース プロバイダー]** を選択します。
 
    ![リソース プロバイダーの表示](media\tutorial-sql-server-to-azure-sql\portal-select-resource-provider.png)    
3.  移行を検索し、**Microsoft.DataMigration** の右側にある **[登録]** を選択します。
 
    ![リソース プロバイダーの登録](media\tutorial-sql-server-to-azure-sql\portal-register-resource-provider.png)    


## <a name="create-an-instance"></a>インスタンスを作成する
1.  Azure Portal で **[+ リソースの作成]** を選択し、Azure Database Migration Service を検索して、ドロップダウン リストから **Azure Database Migration Service** を選択します。

    ![Azure Marketplace](media\tutorial-sql-server-to-azure-sql\portal-marketplace.png)
2.  **Azure Database Migration Service (preview)\(Azure Database Migration Service (プレビュー)\)** 画面で、**[作成]** を選択します。
 
    ![Azure Database Migration Service インスタンスを作成する](media\tutorial-sql-server-to-azure-sql\dms-create.png)
  
3.  **[Database Migration Service]** 画面で、サービスの名前、サブスクリプション、仮想ネットワーク、および価格レベルを指定します。

    コストと価格レベルの詳細については、[価格に関するページ](https://aka.ms/dms-pricing)を参照してください。

     ![Azure Database Migration Service インスタンス設定を構成する](media\tutorial-sql-server-to-azure-sql\dms-settings.png)

4.  **[作成]** を選択して、サービスを作成します。

## <a name="create-a-migration-project"></a>移行プロジェクトを作成する
サービスが作成されたら、Azure ポータル内でそのサービスを特定し、移行プロジェクトを作成します。
1. Azure ポータルで、**[All services]\(すべてのサービス\)** を選択し、Azure Database Migration Service を検索して、**Azure Database Migration Service** を選択します。
 
      ![Azure Database Migration Service のすべてのインスタンスを検索する](media\tutorial-sql-server-to-azure-sql\dms-search.png)
2. **[Azure Database Migration Service]** 画面で、作成した Azure DMS インスタンスの名前を検索し、インスタンスを選択します。
 
     ![Azure Database Migration Service のインスタンスを検索する](media\tutorial-sql-server-to-azure-sql\dms-instance-search.png)
 
3. **[+ New Migration Project]\(+ 新しい移行プロジェクト\)** を選択します。
4. **[New Migration Project]\(新しい移行プロジェクト\)** 画面で、プロジェクトの名前を指定し、**[Source server type]\(ソース サーバーの種類\)** テキスト ボックスで **SQL Server** を選択した後、**[対象サーバーの種類]** テキスト ボックスで **Azure SQL Database** を選択します。

    ![Database Migration Service プロジェクトを作成する](media\tutorial-sql-server-to-azure-sql\dms-create-project.png)

5.  **[作成]** を選択してプロジェクトを作成します。


## <a name="specify-source-details"></a>ソース詳細を指定する
1. **[ソースの詳細]** 画面で、ソース SQL Server の接続詳細を指定します。

    ![ソースを選択する](media\tutorial-sql-server-to-azure-sql\dms-select-source.png)

2. **[保存]** を選択し、移行用 **AdventureWorks2012** データベースを選択します。

    ![ソース DB を選択する](media\tutorial-sql-server-to-azure-sql\dms-select-source-db.png)

## <a name="specify-target-details"></a>ターゲット詳細を指定する
1. **[保存]** を選択し、**[対象の詳細]** 画面で、ターゲットの接続詳細を指定します。このターゲットは、Data Migration Assistant を使用して **AdventureWorks2012** スキーマをデプロイした、事前プロビジョニング済みの Azure SQL Database です。

    ![ターゲットを選択する](media\tutorial-sql-server-to-azure-sql\dms-select-target.png)

2. **[保存]** を選択してプロジェクトを保存します。
3. **[Migration summary]\(移行の概要\)** 画面で、移行プロジェクトに関連付けられた詳細を確認します。

    ![DMS の概要](media\tutorial-sql-server-to-azure-sql\dms-summary.png)

4. **[保存]** を選択します。

## <a name="run-the-migration"></a>移行を実行する
1.  先ほど保存したプロジェクトを選択し、**[+ 新しいアクティビティ]** を選択して、**[Run Data Migration]\(データ移行の実行\)** を選択します。

    ![新しいアクティビティ](media\tutorial-sql-server-to-azure-sql\dms-new-activity.png)

2.  メッセージが表示されたら、ソース サーバーと対象サーバーの資格情報を入力し、**[保存]** を選択します。
3.  **[Map to target databases]\(ターゲット データベースにマップ\)** 画面で、移行用のソース データベースとターゲット データベースをマップします。

    ターゲット データベースにソース データベースと同じデータベース名が含まれている場合、Azure DMS は既定でターゲット データベースを選択します。

    ![ターゲット データベースにマップする](media\tutorial-sql-server-to-azure-sql\dms-map-targets-activity.png)

4. **[保存]** を選択し、**[テーブルの選択]** 画面でテーブルの一覧を展開し、影響を受けるフィールドの一覧を確認します。

    ![テーブルを選択する](media\tutorial-sql-server-to-azure-sql\dms-configure-setting-activity.png)

5.  **[保存]** を選択し、**[Migration summary]\(移行の概要\)** 画面の **[アクティビティ名]** テキスト ボックスで、移行アクティビティの名前を指定します。

    この画面では、**[Choose validation option]\(検証オプションの選択\)** 画面を展開し、移行したデータベースの検証対象として、次のオプションを指定することもできます。
    - スキーマ
    - データ整合性
    - クエリの正確性とパフォーマンス

    ![検証オプションを選択する](media\tutorial-sql-server-to-azure-sql\dms-configuration.png)

6.  **[保存]** を選択し、概要をレビューして、ソースとターゲットの詳細が先ほど指定した内容に一致していることを確認します。

    ![移行の概要](media\tutorial-sql-server-to-azure-sql\dms-run-migration.png)

7.  **[移行の実行]** を選択して移行アクティビティを開始し、**[最新の情報に更新]** 選択して現在の状態を確認します。

    ![アクティビティの状態](media\tutorial-sql-server-to-azure-sql\dms-activity-status.png)

## <a name="monitor-the-migration"></a>移行を監視する
1. 移行アクティビティを選択して、アクティビティの状態を確認します。
2. 移行が完了したら、ターゲットの Azure SQL データベースを確認します。

    ![完了](media\tutorial-sql-server-to-azure-sql\dms-completed-activity.png)
