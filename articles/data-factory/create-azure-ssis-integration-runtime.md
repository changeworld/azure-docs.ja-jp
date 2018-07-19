---
title: Azure Data Factory で Azure-SSIS 統合ランタイムを作成する | Microsoft Docs
description: Azure 上で SSIS パッケージをデプロイして実行できるように、Azure-SSIS 統合ランタイムを Azure Data Factory に作成する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 1b9b1fa5b67e37181ff4c76773c6666ccbbcf275
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082867"
---
# <a name="create-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Azure Data Factory で Azure-SSIS 統合ランタイムを作成する
この記事では、Azure Data Factory で Azure-SSIS 統合ランタイムをプロビジョニングする手順について説明します。 その後、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使用して、Azure 上のこのランタイムに SQL Server Integration Services (SSIS) パッケージをデプロイして実行できます。 

チュートリアル「[SQL Server Integration Services パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md)」では、SSIS カタログをホストするために Azure SQL Database を使用して Azure SSIS 統合ランタイム (IR) を作成する方法を説明しています。 この記事では、チュートリアルをさらに掘り下げ、次の操作を行う方法を示します。 

- 必要に応じて、Azure SQL Database と仮想ネットワーク サービスのエンドポイント/Managed Instance (プレビュー) をデータベース サーバーとして使用して、SSIS カタログ (SSISDB データベース) をホストします。 SSISDB をホストするためにデータベース サーバーの種類を選択する際のガイダンスについては、「[Compare SQL Database and Managed Instance (Preview)](create-azure-ssis-integration-runtime.md#compare-sql-database-and-managed-instance-preview)」(SQL Database およびマネージド インスタンスを比較する (プレビュー)) を参照してください。 前提条件として、Azure-SSIS IR を仮想ネットワークに参加させ、仮想ネットワークのアクセス許可を設定し、必要に応じて構成する必要があります。 「[仮想ネットワークへの Azure-SSIS IR の参加](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)」を参照してください。 

- 必要に応じて、Azure-SSIS IR に対する Azure Data Factory マネージド サービス ID (MSI) で Azure Active Directory (AAD) 認証を使用して、データベース サーバーに接続します。 前提条件として、データベース サーバーへのアクセス許可が割り当てられている AAD グループに Data Factory MSI を追加する必要があります。「[Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir)」をご覧ください。 

## <a name="overview"></a>概要
この記事では、Azure SSIS IR のさまざまなプロビジョニング方法を示します。 

- [Azure Portal](#azure-portal) 
- [Azure PowerShell](#azure-powershell) 
- [Azure Resource Manager テンプレート](#azure-resource-manager-template) 

Azure-SSIS IR 作成すると、Data Factory は、Azure SQL Database に接続して SSIS カタログ データベース (SSISDB) を準備します。 また、仮想ネットワーク (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS 統合ランタイムの新しいインスタンスを仮想ネットワークに参加させます。 

Azure-SSIS IR のインスタンスをプロビジョニングすると、Azure Feature Pack for SSIS と Access の再頒布可能パッケージもインストールされます。 これらのコンポーネントは、組み込みのコンポーネントでサポートされるデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 追加のコンポーネントをインストールすることもできます。 詳細については、「[Azure SSIS 統合ランタイムのカスタム セットアップ](how-to-configure-azure-ssis-ir-custom-setup.md)」をご覧ください。 

## <a name="prerequisites"></a>前提条件 
- **Azure サブスクリプション**。 サブスクリプションがない場合は、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)アカウントを作成できます。 

- **Azure SQL Database サーバー/Managed Instance (プレビュー)**。 まだデータベース サーバーをお持ちでない場合は、あらかじめ Azure Portal でデータベース サーバーを作成しておいてください。 このサーバーは、SSIS カタログ データベース (SSISDB) をホストします。 このデータベース サーバーは、統合ランタイムと同じ Azure リージョンに作成することをお勧めします。 この構成により、統合ランタイムは、Azure リージョンにまたがることなく SSISDB に実行ログを書き込むことができます。 SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、または Managed Instance (プレビュー) 上に自動的に作成できます。SSISDB には、パブリック ネットワークで、または仮想ネットワークに参加することでアクセスできます。 Azure SQL Database でサポートされている価格レベルの一覧については、[SQL Database のリソース制限](../sql-database/sql-database-resource-limits.md)に関するページを参照してください。 

    Azure SQL Database サーバーまたは Managed Instance (プレビュー) に、SSIS カタログ (SSIDB データベース) が既に存在しないことを確認します。 Azure SSIS IR のプロビジョニングでは、既存の SSIS カタログの使用がサポートされていません。 

- **クラシック または Azure Resource Manager 仮想ネットワーク (オプション)**。 次の条件に 1 つでも当てはまる場合は、Azure 仮想ネットワークが必要です。 
    - 仮想ネットワーク内部の Azure SQL Database と仮想ネットワーク サービス エンドポイント/Managed Instance (プレビュー) で SSIS カタログ データベースをホストしている。 
    - Azure-SSIS 統合ランタイム上で実行される SSIS パッケージからオンプレミス データ ストアに接続する必要がある。 

- **Azure PowerShell**。 クラウド内で SSIS パッケージを実行する Azure SSIS 統合ランタイムをプロビジョニングするために PowerShell を使用してスクリプトを実行する場合は、「[Azure PowerShell のインストールおよび構成方法](/powershell/azure/install-azurerm-ps)」の指示に従います。 

### <a name="region-support"></a>リージョンのサポート
現在 Data Factory が利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[Data Factory]** を探してください。

現在 Azure-SSIS 統合ランタイムが利用できる Azure リージョンの一覧については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/)」ページで目的のリージョンを選択し、**[分析]** を展開して **[SSIS Integration Runtime]\(SSIS 統合ランタイム\)** を探してください。### SQL Database と Managed Instance の比較 (プレビュー)

### <a name="compare-sql-database-and-managed-instance-preview"></a>SQL Database と Managed Instance (プレビュー) を比較する

次の表では、Azure-SSIR IR に関連する SQL Database と Managed Instance (プレビュー) の一部の機能を比較しています。

| Feature | SQL Database | Managed Instance |
|---------|--------------|------------------|
| **スケジュール設定** | SQL Server エージェントは使用できません。<br/><br/>「[Schedule a package as part of an Azure Data Factory pipeline](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages.md#activity)」(Azure Data Factory パイプラインの一部としてパッケージをスケジュールする) を参照してください。| SQL Server エージェントは使用できます。 |
| **認証** | **dbmanager** ロール内の任意の Azure Active Directory ユーザーを表す包含データベース ユーザー アカウントを使用してデータベースを作成できます。<br/><br/>「[Azure SQL Database で Azure AD を有効にする](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database)」を参照してください。 | Azure AD 管理者以外の任意の Azure Active Directory ユーザーを表す包含データベース ユーザー アカウントを使用してデータベースを作成することはできません。 <br/><br/>「[Azure SQL Database Managed Instance で Azure AD を有効にする](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance)」を参照してください。 |
| **サービス レベル** | SQL Database 上に Azure-SSIS IR を作成するときに、SSISDB のサービス レベルを選択できます。 複数のサービス レベルがあります。 | Managed Instance 上に Azure-SSIS IR を作成するときに、SSISDB のサービス レベルを選択することはできません。 同じ Managed Instance 上のすべてのデータベースは、そのインスタンスに割り当てられた同じリソースを共有します。 |
| **Virtual Network** | Azure Resource Manager とクラシック仮想ネットワークの両方をサポートします。 | Azure Resource Manager 仮想ネットワークのみをサポートします。 仮想ネットワークは必須です。<br/><br/>Managed Instance と同じ仮想ネットワークに Azure-SSIS IR を参加させる場合、必ず Azure-SSIS IR を Managed Instance とは異なるサブネットに配置します。 Managed Instance とは異なる仮想ネットワークに Azure-SSIS IR を参加させる場合、仮想ネットワーク ピアリング (同じリージョンに限定される) または仮想ネットワーク接続への 1 つの仮想ネットワークのどちらかをお勧めします。 「[Azure SQL Database Managed Instance にアプリケーションを接続する](../sql-database/sql-database-managed-instance-connect-app.md)」を参照してください。 |
| **分散トランザクション** | エラスティック トランザクションを介してサポートされます。 Microsoft 分散トランザクション コーディネーター (MSDTC) トランザクションはサポートされていません。 パッケージが MSDTC を使用して分散トランザクションを調整している場合は、SQL Database 用のエラスティック トランザクションに移行することを検討してください。 詳細については、「[クラウド データベースにまたがる分散トランザクション](../sql-database/sql-database-elastic-transactions-overview.md)」を参照してください。 | サポートされていません。 |
| | | |

## <a name="azure-portal"></a>Azure ポータル
このセクションでは、Azure ポータル、具体的にはデータ ファクトリ UI を使用して、Azure-SSIS IR を作成します。 

### <a name="create-a-data-factory"></a>Data Factory を作成する。 
1. Web ブラウザー (**Microsoft Edge** または **Google Chrome**) を起動します。 現在、Data Factory の UI がサポートされる Web ブラウザーは Microsoft Edge と Google Chrome だけです。 
2. [Azure Portal](https://portal.azure.com/) にログインします。 
3. 左側のメニューで **[新規]** をクリックし、**[データ + 分析]**、**[Data Factory]** の順にクリックします。 

   ![New->DataFactory](./media/tutorial-create-azure-ssis-runtime-portal/new-data-factory-menu.png)

4. **[新しいデータ ファクトリ]** ページで、**[名前]** に「**MyAzureSsisDataFactory**」と入力します。 

   ![[新しいデータ ファクトリ] ページ](./media/tutorial-create-azure-ssis-runtime-portal/new-azure-data-factory.png)

   Azure データ ファクトリの名前は **グローバルに一意**にする必要があります。 次のエラーが発生した場合は、データ ファクトリの名前を変更して (yournameMyAzureSsisDataFactory など) 作成し直してください。 Data Factory アーティファクトの名前付け規則については、[Data Factory の名前付け規則](naming-rules.md)に関する記事を参照してください。 

   `Data factory name “MyAzureSsisDataFactory” is not available`

5. データ ファクトリを作成する Azure **サブスクリプション**を選択します。 
6. **[リソース グループ]** について、次の手順のいずれかを行います。 

   - **[Use existing (既存のものを使用)]** を選択し、ドロップダウン リストから既存のリソース グループを選択します。 
   - **[新規作成]** を選択し、リソース グループの名前を入力します。 

   リソース グループの詳細については、 [リソース グループを使用した Azure のリソースの管理](../azure-resource-manager/resource-group-overview.md)に関するページを参照してください。 

7. **バージョン**として **[V2]** を選択します。 
8. データ ファクトリの **場所** を選択します。 データ ファクトリの作成がサポートされている場所のみが一覧に表示されます。 
9. **[ダッシュボードにピン留めする]** をオンにします。 
10. **Create** をクリックしてください。 
11. ダッシュボードに、**[Deploying data factory]\(データ ファクトリをデプロイしています\)** というステータスを示したタイルが表示されます。 

    ![[Deploying data factory]\(データ ファクトリをデプロイしています\) タイル](media/tutorial-create-azure-ssis-runtime-portal/deploying-data-factory.png)

12. 作成が完了すると、図に示されているような **[Data Factory]** ページが表示されます。 

    ![データ ファクトリのホーム ページ](./media/tutorial-create-azure-ssis-runtime-portal/data-factory-home-page.png)

13. **[Author & Monitor]\(作成と監視\)** をクリックして、別のタブで Data Factory ユーザー インターフェイス (UI) を起動します。 

### <a name="provision-an-azure-ssis-integration-runtime"></a>Azure SSIS 統合ランタイムをプロビジョニングする 
1. 開始ページで、**[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\)** タイルをクリックします。 

   ![[Configure SSIS Integration Runtime]\(SSIS 統合ランタイムの構成\) タイル](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. **[Integration Runtime Setup]\(統合ランタイムの設定\)** の **[全般設定]** ページで、次の手順を実行します。 

   ![全般設定](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. **[名前]** に統合ランタイムの名前を入力します。 

    b. **[説明]** に統合ランタイムの説明を入力します。 

    c. **[場所]** で、統合ランタイムの場所を選択します。 サポートされている場所のみが表示されます。 SSISDB をホストするデータベース サーバーと同じ場所を選択することをお勧めします。 

    d. **[ノード サイズ]** で、統合ランタイム クラスター内のノードのサイズを選択します。 サポートされているノード サイズのみが表示されます。 コンピューティングやメモリを大量に使用するパッケージを多数実行する場合は、大きなノード サイズを選択します (スケールアップ)。 

    e. **[Node Number]\(ノード数\)** で、統合ランタイム クラスター内のノードの数を選択します。 サポートされているノード数のみが表示されます。 多くのパッケージを並列で実行する場合は、ノード数が多い大規模なクラスターを選択します (スケールアウト)。 

    f. **[Edition/License]\(エディション/ライセンス\)** で、統合ランタイムの SQL Server エディション/ライセンス ([Standard] または [Enterprise]) を選択します。 統合ランタイムで高度な機能やプレミアム機能を使用する場合は、[Enterprise] を選択します。 

    g. **[お金を節約]** で、統合ランタイムの Azure ハイブリッド特典 (AHB) オプション ([はい] または [いいえ]) を選択します。 ソフトウェア アシュアランス付き SQL Server ライセンスを移行して、ハイブリッド使用によるコスト節約の恩恵を受ける場合は、[はい] を選択します。 

    h. **[次へ]** をクリックします。 

3. **[SQL の設定]** ページで、次の手順を完了します。 

   ![SQL の設定](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. **[サブスクリプション]** で、SSISDB をホストするデータベース サーバーを保有する Azure サブスクリプションを選択します。 

    b. **[場所]** で、SSISDB をホストするデータベース サーバーの場所を選択します。 統合ランタイムと同じ場所を選択することをお勧めします。 

    c. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で、SSISDB をホストするデータベース サーバーのエンドポイントを選択します。 SSISDB は、選択したデータベース サーバーに基づいて、1 つのデータベースやエラスティック プールの一部として、または Managed Instance (プレビュー) 上に自動的に作成できます。SSISDB には、パブリック ネットワークで、または仮想ネットワークに参加することでアクセスできます。 

    d. **[Use AAD authentication with your ADF MSI]\(ADF MSI で AAD 認証を使用する\)** チェック ボックスで、SSISDB をホストするデータベース サーバーの認証方法 (SQL または Azure Active Directory (AAD) と Azure Data Factory マネージド サービス ID (MSI)) を選択します。 オンにする場合は、データベース サーバーへのアクセス許可が割り当てられている AAD グループに Data Factory MSI を追加する必要があります。「[Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir)」をご覧ください。 

    e. **[管理者ユーザー名]** に、SSISDB をホストするデータベース サーバーの SQL 認証ユーザー名を入力します。 

    f. **[管理者パスワード]** に、SSISDB をホストするデータベース サーバーの SQL 認証パスワードを入力します。 

    g. **[Catalog Database Service Tier]\(カタログ データベース サービス階層\)** で、SSISDB をホストするデータベース サーバーのサービス階層 (Basic/Standard/Premium 階層またはエラスティック プール名) を選択します。 

    h. **[接続テスト]** をクリックし、成功した場合は **[次へ]** をクリックします。 

4.  **[詳細設定]** ページで、次の手順を実行します。 

    ![詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. **[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** で、統合ランタイム クラスター内のノードあたりの同時に実行するパッケージの最大数を選択します。 サポートされているパッケージ数のみが表示されます。 コンピューティングやメモリを大量に使用する 1 つの大規模/重量なパッケージを実行するために複数のコアを使用する場合は、低い数値を選択します。 1 つのコアで 1 つ以上の小規模/軽量なパッケージを実行する場合は、高い数値を選択します。 

    b. **[Custom Setup Container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** に、必要に応じて、セットアップ スクリプトとその関連ファイルが格納されている Azure Storage Blob コンテナーの Shared Access Signature (SAS) Uniform Resource Identifier (URI) を入力します。「[Azure SSIS 統合ランタイムのカスタム セットアップ](https://docs.microsoft.com/en-us/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)」をご覧ください。 

5. **[Select a virtual network...]\(参加させる...\)** チェック ボックスで、統合ランタイムを仮想ネットワークに参加させるかどうかを選択します。 Azure SQL Database と仮想ネットワーク サービス エンドポイント/Managed Instance (プレビュー) を使用して SSISDB をホストする場合、またはオンプレミスのデータにアクセスする必要がある場合、つまり SSIS パッケージ内にオンプレミスのデータ ソース/ターゲットがある場合は、オンにします。「[仮想ネットワークへの Azure-SSIS IR の参加](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)」をご覧ください。 オンにした場合は、次の手順のようにします。 

   ![仮想ネットワークの詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. **[サブスクリプション]** で、仮想ネットワークを含む Azure サブスクリプションを選択します。 

    b. **[場所]** では、統合ランタイムと同じ場所が選択されています。 

    c. **[種類]** で、仮想ネットワークの種類の (クラシックまたは Azure Resource Manager) を選択します。 クラシック仮想ネットワークは間もなく非推奨になるので、Azure Resource Manager 仮想ネットワークを選択することをお勧めします。 

    d. **[VNet 名]** で、仮想ネットワークの名前を選択します。 この仮想ネットワークは、SSISDB をホストするために Azure SQL Database と仮想ネットワーク サービス エンドポイント/Managed Instance (プレビュー) で使用されているものと同じ仮想ネットワーク、またはオンプレミスのネットワークに接続されている仮想ネットワークである必要があります。 

    e. **[サブネット名]** で、お使いの仮想ネットワークのサブネットの名前を選択します。 これは、SSISDB をホストするために Managed Instance (プレビュー) で使われているものとは異なるサブネットにする必要があります。 

6. **[VNet Validation]\(VNet の検証\)** をクリックし、成功した場合は、**[完了]** をクリックして Azure-SSIS 統合ランタイムの作成を始めます。 

    > [!IMPORTANT]
    > - このプロセスは、完了するまで約 20 から 30 分かかります
    > - Data Factory サービスは、Azure SQL Database に接続して SSIS カタログ データベース (SSISDB) を準備します。 また、仮想ネットワーク (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS 統合ランタイムの新しいインスタンスを仮想ネットワークに参加させます。 

7. **[接続]** ウィンドウで、必要に応じて **[Integration Runtimes]\(統合ランタイム\)** に切り替えます。 **[最新の情報に更新]** をクリックして、状態を更新します。 

   ![作成の状態](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. **[アクション]** 列のリンクを使用して、統合ランタイムを停止/起動、編集、または削除します。 最後のリンクを使用すると、統合ランタイムの JSON コードを表示できます。 編集ボタンと削除ボタンは、IR が停止している場合にのみ有効になります。 

   ![Azure SSIS IR - アクション](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>ポータルでの Azure SSIS 統合ランタイム
1. Azure Data Factory UI で、**[編集]** タブに切り替えます。**[接続]** をクリックし、**[Integration Runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。 

   ![既存の IR を表示する](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. **[新規]** をクリックして新しい Azure-SSIS IR を作成します。 

   ![メニューによる統合ランタイム](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Azure-SSIS 統合ランタイムを作成するには、図に示すように **[新規]** をクリックします。 
4. [Integration Runtime Setup]\(統合ランタイムの設定\) ウィンドウで、**[Lift-and-shift existing SSIS packages to execute in Azure]\(Azure で実行する既存の SSIS パッケージをリフトアンドシフトする\)** を選択し、**[次へ]** をクリックします。 

   ![統合ランタイムの種類を指定する](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Azure-SSIS IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。 

## <a name="azure-powershell"></a>Azure PowerShell
このセクションでは、Azure PowerShell を使用して、Azure-SSIS IR を作成します。

### <a name="create-variables"></a>変数の作成
このチュートリアルのスクリプトで使用する変数を定義します。

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"
```

### <a name="log-in-and-select-subscription"></a>ログインとサブスクリプションの選択
ログインして Azure サブスクリプションを選択するには、スクリプトに次のコードを追加します。 

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database"></a>データベースへの接続の検証
Azure SQL Database サーバー エンドポイントを検証するには、次のスクリプトを追加します。 

```powershell
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}
```

### <a name="configure-virtual-network"></a>仮想ネットワークを構成する
Azure-SSIS 統合ランタイムが参加するための仮想ネットワークのアクセス許可/設定を自動的に構成するには、次のスクリプトを追加します。

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>リソース グループの作成
[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) コマンドで [Azure リソース グループ](../azure-resource-manager/resource-group-overview.md)を作成します。 リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Data Factory を作成する。
次のコマンドを実行して、データ ファクトリを作成します。

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>統合ランタイムの作成
Azure 内で SSIS パッケージを実行する Azure-SSIS 統合ランタイムを作成するには、次のコマンドを実行します。 

Azure SQL Database と仮想ネットワーク サービス エンドポイント/Managed Instance (プレビュー) を使用して SSISDB をホストしない場合、またはオンプレミスのデータにアクセスする必要がない場合は、VNetId パラメーターと Subnet パラメーターを省略するか、またはこれらに空の値を渡してかまいません。 そうではない場合は、これらのパラメーターを省略することはできず、お使いの仮想ネットワーク構成の有効な値を渡す必要があります。「[仮想ネットワークへの Azure-SSIS IR の参加](https://docs.microsoft.com/en-us/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)」をご覧ください。 

Managed Instance (プレビュー) を使って SSISDB をホストする場合は、CatalogPricingTier パラメーターを省略するか、空の値を渡すことができます。 それ以外の場合は、このパラメーターを省略することはできず、Azure SQL Database でサポートされる価格レベルの一覧から有効な値を渡す必要があります。[SQL Database リソースの制限](../sql-database/sql-database-resource-limits.md)に関するページをご覧ください。 

Azure Data Factory マネージド サービス ID (MSI) で Azure Active Directory (AAD) 認証を使ってデータベース サーバーに接続する場合は、CatalogAdminCredential パラメーターを省略できますが、データベース サーバーへのアクセス許可を持つ AAD グループに Data Factory MSI を追加する必要があります。「[Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする](https://docs.microsoft.com/en-us/azure/data-factory/enable-aad-authentication-azure-ssis-ir)」をご覧ください。 それ以外の場合は、省略することができません。SQL 認証のためのサーバー管理者のユーザー名とパスワードから構成された有効なオブジェクトを渡す必要があります。

```powershell               
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}
```

### <a name="start-integration-runtime"></a>統合ランタイムの起動
Azure-SSIS 統合ランタイムを起動するには、次のコマンドを実行します。 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```

このコマンドは、完了までに **20 ～ 30 分**かかります。 

### <a name="full-script"></a>完全なスクリプト
Azure-SSIS 統合ランタイムを作成する完全なスクリプトを次に示します。 

```powershell
### Azure Data Factory information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$".
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" 

### Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeSize = "Standard_D4_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# Azure-SSIS IR edition/license info: Standard or Enterprise 
$AzureSSISEdition = "" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 8 
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance (Preview)/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon    
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use a different subnet than the one used for your Managed Instance (Preview)

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance (Preview) name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance (Preview)]"

### Log in and select subscription
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database
# Validate only when you do not use VNet nor AAD authentication
if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
{
    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
    {
        $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword    
        $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
        Try
        {
            $sqlConnection.Open();
        }
        Catch [System.Data.SqlClient.SqlException]
        {
            Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
            Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
            $yn = Read-Host
            if(!($yn -ieq "Y"))
            {
                Return;
            } 
        }
    }
}

### Configure virtual network
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName

### Create an integration runtime
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Type Managed `
                                           -Name $AzureSSISName `
                                           -Description $AzureSSISDescription `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -SetupScriptContainerSasUri $SetupScriptContainerSasUri `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier

if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
{
    $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
    $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogAdminCredential $serverCreds
}

### Start integration runtime   
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート
このセクションでは、Azure Resource Manager テンプレートを使用して、Azure-SSIS 統合ランタイムを作成します。 サンプル チュートリアルを次に示します。 

1. 次の Azure Resource Manager テンプレートを使用して、JSON ファイルを作成します。 山かっこ内の値 (プレース ホルダー) を独自の値に置き換えます。 

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2017-09-01-preview",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D4_v2",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```
    
2. Azure Resource Manager テンプレートをデプロイするには、次の例で示すように New-AzureRmResourceGroupDeployment コマンドを実行します。ADFTutorialResourceGroup はリソース グループの名前、ADFTutorialARM.json はデータ ファクトリと Azure-SSIS IR の JSON 定義を含むファイルです。 

    ```powershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    このコマンドは、データ ファクトリを作成し、その中に Azure-SSIS IR を作成しますが、IR の起動は行いません。 

3. Azure-SSIS IR を起動するには、Start-AzureRmDataFactoryV2IntegrationRuntime コマンドを実行します。 

    ```powershell
    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ``` 

## <a name="deploy-ssis-packages"></a>SSIS パッケージのデプロイ
次に、SQL Server Data Tools (SSDT) または SQL Server Management Studio (SSMS) を使って SSIS パッケージを Azure にデプロイします。 SSIS カタログ (SSISDB) をホストするデータベース サーバーに接続します。 データベース サーバーの名前の形式は、&lt;Azure SQL Database サーバー名&gt;.database.windows.net または &lt;Managed Instance (Preview) 名.DNS プレフィックス&gt;.database.windows.net です。 手順については、[パッケージのデプロイ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)に関する記事を参照してください。 

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure SSIS IR に関する、次のような他のトピックも参照してください。 

- [Azure-SSIS 統合ランタイム](concepts-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR など、統合ランタイムの一般的な概念について説明されています。 
- [チュートリアル: SSIS パッケージを Azure にデプロイする](tutorial-create-azure-ssis-runtime-portal.md):  この記事では、Azure-SSIS IR を作成し、Azure SQL Database を使って SSIS カタログをホストする手順が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime):  この記事では、Azure-SSIS IR に関する情報を取得する方法と、返された情報での状態が説明されています。 
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md):  この記事では、Azure-SSIS IR を停止、開始、削除する方法が説明されています。 また、IR にノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。 
- [仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md): この記事では、Azure 仮想ネットワークへの Azure-SSIS IR の参加に関する概念情報が説明されています。 Azure-SSIS IR が仮想ネットワークに参加できるように Azure Portal を使用して仮想ネットワークを構成する手順も説明されています。 
