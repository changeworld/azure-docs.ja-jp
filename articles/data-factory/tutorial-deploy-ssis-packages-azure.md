---
title: SSIS パッケージを Azure にデプロイする | Microsoft Docs
description: この記事では、Azure Data Factory を使用して SSIS パッケージを Azure にデプロイし、Azure-SSIS 統合ランタイムを作成する方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: hero-article
ms.date: 01/29/2018
ms.author: douglasl
ms.openlocfilehash: aca9f822bf3fd3b26e554240a4fee2474b89143d
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure"></a>SQL Server Integration Services パッケージを Azure にデプロイする
このチュートリアルでは、Azure Portal を使用して Azure-SSIS 統合ランタイム (IR) を Azure Data Factory にプロビジョニングする手順について説明します。 その後、SQL Server Data Tools または SQL Server Management Studio を使用して、Azure 上のこのランタイムに SQL Server Integration Services (SSIS) パッケージをデプロイできます。 Azure-SSIS IR の概念については、[Azure-SSIS 統合ランタイムの概要](concepts-integration-runtime.md#azure-ssis-integration-runtime)に関する記事を参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムをプロビジョニングします。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[Data Factory バージョン 1 のドキュメント](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)を参照してください。


## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。 
- **Azure SQL Database サーバー**。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 Azure Data Factory は、このデータベース サーバーに SSIS カタログ (SSISDB データベース) を作成します。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB データベースに実行ログを書き込むことができます。 
- データベース サーバーで **[Azure サービスへのアクセスを許可]** の設定が有効になっていることを確認します。 詳細については、「[Azure SQL データベースのセキュリティ保護](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)」を参照してください。 PowerShell を使用してこの設定を有効にするには、「[New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)」を参照してください。
- クライアント マシンの IP アドレス、またはクライアント マシンの IP アドレスを含む IP アドレスの範囲を、データベース サーバーのファイアウォール設定にあるクライアント IP アドレスの一覧に追加します。 詳細については、「[Azure SQL Database のサーバーレベルとデータベースレベルのファイアウォール規則](../sql-database/sql-database-firewall-configure.md)」を参照してください。
- Azure SQL Database サーバーに SSIS カタログ (SSISDB データベース) がないことを確認します。 Azure SSIS IR のプロビジョニングでは、既存の SSIS カタログの使用がサポートされていません。

> [!NOTE]
> - バージョン 2 のデータ ファクトリは、米国東部、米国東部 2、東南アジア、西ヨーロッパの各リージョンで作成できます。 
> - Azure-SSIS IR は、米国東部、米国東部 2、米国中部、北ヨーロッパ、西ヨーロッパ、オーストラリア東部の各リージョンで作成できます。 

## <a name="create-a-data-factory"></a>Data Factory を作成する。

1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。
2. [Azure Portal](https://portal.azure.com/) にサインインします。    
3. 左側のメニューの **[新規]** を選択し、**[データ + 分析]** を選択して、**[Data Factory]** を選択します。 
   
   ![[新規] ウィンドウでの [Data Factory] の選択](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)
3. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**MyAzureSsisDataFactory**」と入力します。 
      
   ![[新しいデータ ファクトリ] ページ](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)
 
   Azure データ ファクトリの名前は *グローバルに一意*にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (**&lt;yourname&gt;MyAzureSsisDataFactory** など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。
  
   `Data factory name “MyAzureSsisDataFactory” is not available`
4. **[サブスクリプション]** で、データ ファクトリを作成する Azure サブスクリプションを選択します。 
5. **リソース グループ**について、次の手順のいずれかを行います。
     
   - **[既存のものを使用]** を選択し、一覧から既存のリソース グループを選択します。 
   - **[新規作成]**を選択し、リソース グループの名前を入力します。   
         
   リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。  
6. **[バージョン]** では、**[V2 (プレビュー)]** を選択します。
7. **[場所]** で、データ ファクトリの場所を選択します。 一覧には、データ ファクトリの作成がサポートされている場所のみが表示されます。
8. **[ダッシュボードにピン留めする]** をオンにします。     
9. **[作成]**を選択します。
10. ダッシュボードに、**[Deploying Data Factory]\(データ ファクトリをデプロイしています\)** というステータスを示した次のタイルが表示されます。 

   ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)
11. 作成が完了すると、**[データ ファクトリ]** ページが表示されます。
   
   ![データ ファクトリのホーム ページ](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)
12. **[作成と監視]** を選択して、別のタブで Data Factory ユーザー インターフェイス (UI) を開きます。 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムのプロビジョニング

1. **[Let's get started]\(始めましょう\)** ページで、**[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\)** タイルをクリックします。 

   ![[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\) タイル](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)
2. **[Integration Runtime Setup]\(統合ランタイムの設定\)** の **[全般設定]** ページで、次の手順を実行します。 

   ![全般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[名前]** で、統合ランタイムの名前を指定します。

   b. **[場所]** で、統合ランタイムの場所を選択します。 サポートされている場所のみが表示されます。

   c. **[ノード サイズ]** で、SSIS ランタイムで構成するノードのサイズを選択します。

   d. **[Node Number]\(ノード数\)** で、クラスターのノード数を指定します。
   
   e. **[次へ]**を選択します。 
3. **[SQL の設定]** ページで、次の手順を完了します。 

   ![SQL の設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[サブスクリプション]** で、Azure データベース サーバーを保有する Azure サブスクリプションを指定します。

   b. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で、Azure データベース サーバーを選択します。

   c. **[管理ユーザー名]** で、管理者ユーザー名を入力します。

   d. **[管理パスワード]** で、管理者のパスワードを入力します。

   e. **[Catalog Database Server Tier]\(カタログ データベース サーバー層\)** で、SSISDB データベースのサービス レベルを選択します。 既定値は **Basic** です。

   f. **[次へ]**を選択します。 
4. **[詳細設定]** ページで、**[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** の値を選択します。   

   ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)    
5. この手順は*省略可能*です。 (クラシック デプロイメント モデルまたは Azure Resource Manager を使用して作成した) 仮想ネットワークがあり、これに統合ランタイムを参加させる場合は、**[Select a VNet for your Azure-SSIS integration runtime to join and allow Azure services to configure VNet permissions/settings]\(参加させる Azure-SSIS 統合ランタイム用の VNet を選択し、Azure サービスが VNet の権限/設定を構成することを許可する\)** チェック ボックスをオンにします。 その後、次の手順を完了します。 

   ![仮想ネットワークの詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)    

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 **[サブスクリプション]** で、仮想ネットワークを保有するサブスクリプションを指定します。

   b. **[VNet 名]** で、仮想ネットワークの名前を選択します。

   c. **[サブネット名]** で、仮想ネットワークのサブネットの名前を選択します。 
6. **[完了]** を選択して、Azure-SSIS 統合ランタイムの作成を開始します。 

   > [!IMPORTANT]
   > このプロセスは、完了するまで約 20 分かかります。
   >
   > Data Factory サービスは、Azure SQL Database に接続して SSIS カタログ (SSISDB データベース) を準備します。 このスクリプトは、仮想ネットワークのアクセス許可と設定も構成します (指定されている場合)。 さらに、Azure-SSIS 統合ランタイムの新しいインスタンスを仮想ネットワークに結合します。
   > 
   > Azure-SSIS IR のインスタンスをプロビジョニングすると、ほかにも Azure Feature Pack for SSIS と Access の再頒布可能パッケージがインストールされます。 これらのコンポーネントは、組み込みのコンポーネントでサポートされるデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 現時点では、SSIS 用のサードパーティ コンポーネントをインストールすることはできません  (この制限には、Attunity による Oracle コンポーネントおよび Teradata コンポーネント、SAP BI コンポーネントなど、Microsoft が提供するサードパーティ コンポーネントが含まれます)。

7. **[接続]** タブで、必要に応じて **[Integration Runtimes]\(統合ランタイム\)** に切り替えます。 **[最新の情報に更新]** を選択して、状態を更新します。 

   ![作成状態、[最新の情報に更新] ボタン](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)
8. **[アクション]** 列のリンクを使用して、統合ランタイムを停止/起動、編集、または削除します。 最後のリンクを使用すると、統合ランタイムの JSON コードを表示できます。 編集ボタンと削除ボタンは、IR が停止している場合にのみ有効になります。 

   ![[アクション] 列のリンク](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)        

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムを作成します

1. Azure Data Factory UI で、**[編集]** タブに切り替えます。**[接続]** を選択し、**[Integration Runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。 
   ![既存の IR を表示するための選択](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)
2. **[新規]** を選択して Azure-SSIS IR を作成します。 

   ![メニューによる統合ランタイム](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)
3. **[Integration Runtime Setup]\(統合ランタイムの設定\)** ウィンドウで、**[Lift-and-shift existing SSIS packages to execute in Azure]\(Azure で実行する既存の SSIS パッケージをリフトアンドシフトする\)** を選択し、**[次へ]** を選択します。

   ![統合ランタイムの種類を指定する](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)
4. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。 

 
## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ
次に、SQL Server Data Tools または SQL Server Management Studio を使って SSIS パッケージを Azure にデプロイします。 SSIS カタログ (SSISDB データベース) をホストする Azure データベース サーバーに接続します。 Azure データベース サーバーの名前は、`<servername>.database.windows.net` の形式になります (Azure SQL Database の場合)。 

SSIS ドキュメントの次の記事をご覧ください。 

- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSIS カタログへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure でのパッケージ実行のスケジュール設定](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [Windows 認証を使用したオンプレミス データ ソースへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>次の手順
このチュートリアルで学習した内容は次のとおりです。 

> [!div class="checklist"]
> * データ ファクトリを作成します。
> * Azure-SSIS 統合ランタイムをプロビジョニングします。

オンプレミスからクラウドにデータをコピーする方法について学習するには、次のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [データをクラウドにコピーする](tutorial-copy-data-portal.md)
