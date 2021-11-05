---
title: Azure portal を使用した Azure-SSIS 統合ランタイムの作成
description: Azure portal を使用して Azure Data Factory で Azure-SSIS 統合ランタイムを作成し、Azure で SSIS パッケージをデプロイして実行できるようにする方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bcbb939f35e7bb4fa1a3508f7403f990edd6e282
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131038759"
---
# <a name="create-an-azure-ssis-integration-runtime-via-azure-portal"></a>Azure portal を使用した Azure-SSIS 統合ランタイムの作成 

この記事では、Azure portal/ADF UI を使用して Azure Data Factory (ADF) で Azure-SQL Server Integration Services (SSIS) 統合ランタイム (IR) を作成する方法について説明します。

## <a name="create-a-data-factory"></a>Data Factory の作成

Azure portal でデータ ファクトリを作成するには、[UI を使用してデータ ファクトリを作成する](./quickstart-create-data-factory-portal.md#create-a-data-factory)手順に従ってください。 その時に、作成後にすぐアクセスできるよう、 **[ダッシュボードにピン留めする]** を選択します。 

データ ファクトリが作成されたら、その概要ページが Azure portal で開きます。 **[作成と監視]** タイルを選択して、別のタブで **[Let's get started]\(はじめに\)** ページを開きます。そこから Azure-SSIS IR の作成に進むことができます。   

## <a name="provision-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムのプロビジョニング

ホーム ページで、 **[SSIS 構成]** タイルをクリックして、 **[統合ランタイムのセットアップ]** ペインを開きます。

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF のホーム ページを示すスクリーンショット。":::

   **[統合ランタイムのセットアップ]** ペインには、全般設定、デプロイ設定、詳細設定という一連の構成を行うための 3 つのページがあります。

### <a name="general-settings-page"></a>[全般設定] ページ

**[統合ランタイムのセットアップ]** ペインの **[全般設定]** ページで、次の手順を行います。

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png" alt-text="全般設定":::

1. **[名前]** に統合ランタイムの名前を入力します。

2. **[説明]** に統合ランタイムの説明を入力します。

3. **[場所]** で、統合ランタイムの場所を選択します。 サポートされている場所のみが表示されます。 SSISDB をホストするデータベース サーバーと同じ場所を選択することをお勧めします。

4. **[ノード サイズ]** で、統合ランタイム クラスター内のノードのサイズを選択します。 サポートされているノード サイズのみが表示されます。 コンピューティングやメモリを大量に使用するパッケージを多数実行する場合は、大きなノード サイズを選択します (スケールアップ)。

   > [!NOTE]
   > [コンピューティングの分離](../azure-government/azure-secure-isolation-guidance.md#compute-isolation)を必要とする場合は、**Standard_E64i_v3** ノード サイズを選択してください。 このノード サイズは、物理ホスト全体を消費し、特定のワークロード (米国国防総省の影響レベル 5 (IL5) のワークロードなど) で必要とされる分離レベルを提供する分離された仮想マシンを表します。
   
5. **[Node Number]\(ノード数\)** で、統合ランタイム クラスター内のノードの数を選択します。 サポートされているノード数のみが表示されます。 多くのパッケージを並列で実行する場合は、ノード数が多い大規模なクラスターを選択します (スケールアウト)。

6. **[Edition/License]\(エディション/ライセンス\)** で、統合ランタイムの SQL Server エディション ([Standard] または [Enterprise]) を選択します。 統合ランタイムで高度な機能を使用する場合は、[Enterprise] を選択します。

7. **[お金を節約]** で、統合ランタイムの Azure ハイブリッド特典オプション ( **[はい]** または **[いいえ]** ) を選択します。 ソフトウェア アシュアランス付き SQL Server ライセンスを移行して、ハイブリッド使用によるコスト節約の利点を得るには、 **[はい]** を選択します。

8. **[続行]** をクリックします。

### <a name="deployment-settings-page"></a>[デプロイ設定] ページ

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインの **[デプロイ設定]** ページには、SSISDB や Azure-SSIS IR パッケージ ストアを作成するオプションがあります。

#### <a name="creating-ssisdb"></a>SSISDB を作成する

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインの **[デプロイ設定]** ページで、パッケージを SSISDB (プロジェクト デプロイ モデル) にデプロイしたい場合は、 **[Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs]\(Azure SQL Database サーバーおよびマネージド インスタンスによってホストされた SSIS カタログ (SSISDB) を作成してプロジェクト、パッケージ、環境、実行ログを格納する\)** チェック ボックスをオンにします。 Azure SQL Managed Instance にホストされた SQL Server データベース (MSDB)、Azure Files、ファイル システムのいずれかにパッケージをデプロイする場合 (パッケージ デプロイ モデル) は、SSISDB を作成する必要はなく、チェック ボックスをオンにする必要もありません。

デプロイ モデルに関係なく、Azure SQL Managed Instance にホストされた SQL Server エージェントを使用してパッケージの実行を調整 (スケジュール) したい場合は、SSISDB によって有効になっているため、このチェック ボックスをオンにしてください。 詳細については、[Azure SQL Managed Instance エージェントを使用して SSIS パッケージの実行をスケジュールする](./how-to-invoke-ssis-package-managed-instance-agent.md)方法に関するページを参照してください。
   
このチェック ボックスをオンにした場合は、自動的に作成および管理される SSISDB のホストとなる独自のデータベース サーバーを次の手順に従って用意してください。

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png" alt-text="SSISDB のデプロイ設定":::
   
1. **[サブスクリプション]** で、SSISDB をホストするデータベース サーバーを保有する Azure サブスクリプションを選択します。 

1. **[場所]** で、SSISDB をホストするデータベース サーバーの場所を選択します。 統合ランタイムと同じ場所を選択することをお勧めします。

1. **[Catalog Database Server Endpoint]\(カタログ データベース サーバー エンドポイント\)** で、SSISDB をホストするデータベース サーバーのエンドポイントを選択します。 
   
   SSISDB インスタンスは、選択したデータベース サーバーに基づいて、単一データベースやエラスティック プールの一部として、またはマネージド インスタンス上に自動的に作成できます。 パブリック ネットワークからアクセスできるほか、仮想ネットワークに参加させてアクセスすることもできます。 SSISDB をホストするデータベース サーバーの種類を選択する際のガイダンスについては、[SQL Database と SQL Managed Instance の比較](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)に関するセクションを参照してください。   

   IP ファイアウォール規則/仮想ネットワーク サービス エンドポイントを備えた Azure SQL Database サーバーまたはプライベート エンドポイントを備えたマネージド インスタンスを選択して SSISDB をホストする場合、またはセルフホステッド IR を構成せずにオンプレミスのデータへのアクセスを必要としている場合は、Azure-SSIS IR を仮想ネットワークに参加させる必要があります。 詳細については、「[Azure-SSIS 統合ランタイムを仮想ネットワークに参加させる](./join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。
      
1. **[Use AAD authentication with the system managed identity for Data Factory]\(AAD 認証をデータ ファクトリのシステム マネージド ID と共に使用する\)** または **[Use AAD authentication with a user-assigned managed identity for Data Factory]\(AAD 認証をデータ ファクトリのユーザー割り当てマネージド ID と共に使用する\)** のチェック ボックスをオンにして、SSISDB をホストするデータベース サーバーにアクセスするための Azure-SSIS IR の Azure AD 認証方法を選択します。 SQL 認証方法を代わりに選択する場合、これらのチェック ボックスをオンにしないでください。

   このいずれかのチェック ボックスをオンにする場合は、データベース サーバーへのアクセス許可を持つ Azure AD グループに、データ ファクトリの指定のシステム/ユーザー割り当てマネージド ID を追加する必要があります。 **[Use AAD authentication with a user-assigned managed identity for Data Factory]\(AAD 認証をデータ ファクトリのユーザー割り当てマネージド ID と共に使用する\)** チェック ボックスをオンにした場合は、指定のユーザー割り当てマネージド ID を使用して作成された既存の資格情報を選択するか、新たに作成できます。 詳細については、「[Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする](./enable-aad-authentication-azure-ssis-ir.md)」を参照してください。

1. **[管理者ユーザー名]** に、SSISDB をホストするデータベース サーバーの SQL 認証ユーザー名を入力します。 

1. **[管理者パスワード]** に、SSISDB をホストするデータベース サーバーの SQL 認証パスワードを入力します。 

1. 事業継続とディザスター リカバリー (BCDR) のために Azure SQL Database または Managed Instance フェールオーバー グループと同期して機能するデュアル スタンバイ Azure SSIS IR ペアを構成するには、 **[Use dual standby Azure-SSIS Integration Runtime pair with SSISDB failover]\(SSISDB フェールオーバーと共にデュアル スタンバイ Azure-SSIS Integration Runtime ペアを使用する\)** チェック ボックスをオンにします。
   
   このチェック ボックスをオンにした場合は、 **[Dual standby pair name]\(デュアル スタンバイ ペア名\)** テキスト ボックスに、プライマリとセカンダリの Azure-SSIS IR ペアを識別する名前を入力します。 プライマリとセカンダリの Azure SSIS IR を作成するときは、同じペア名を入力する必要があります。

   詳細については、[BCDR のための Azure-SSIS IR の構成](./configure-bcdr-azure-ssis-integration-runtime.md)に関する記事をご覧ください。

1. **[Catalog Database Service Tier]\(カタログ データベースのサービス レベル)** で、SSISDB をホストするデータベース サーバーのサービス レベルを選択します。 Basic、Standard、Premium のいずれかのレベルを選択するか、エラスティック プール名を選択してください。

**[テスト接続]** を選択し (該当する場合)、成功したら **[続行]** を選択します。

> [!NOTE]
> Azure SQL Database サーバーを使用して SSISDB をホストする場合、データは既定でバックアップ用に geo 冗長ストレージに格納されます。 他のリージョンでデータをレプリケートしたくない場合は、「[PowerShell を使用してバックアップ ストレージの冗長性を構成する](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell)」の手順に従ってください。
   
#### <a name="creating-azure-ssis-ir-package-stores"></a>Azure-SSIS IR パッケージ ストアを作成する

**[Integration runtime setup]\(統合ランタイムのセットアップ\)** ペインの **[デプロイ設定]** ページで、MSDB、ファイル システム、または Azure Files (パッケージ デプロイ モデル) にデプロイされたパッケージを Azure-SSIS IR パッケージ ストアで管理したい場合は、 **[Create package stores to manage your packages that are deployed into file system/Azure Files/SQL Server database (MSDB) hosted by Azure SQL Managed Instance]\(パッケージ ストアを作成して、Azure SQL Managed Instance をホストとする SQL Server データベース (MSDB)、Azure Files、ファイル システムにデプロイされたパッケージを管理する\)** チェック ボックスをオンにします。
   
Azure-SSIS IR パッケージ ストアを使用すると、パッケージのインポート、エクスポート、削除、実行のほか、実行中のパッケージの監視と停止を、[従来の SSIS パッケージ ストア](/sql/integration-services/service/package-management-ssis-service)と同様、SSMS を介して行うことができます。 詳細については、[Azure-SSIS IR パッケージ ストアを使用した SSIS パッケージの管理](./azure-ssis-integration-runtime-package-store.md)に関するページを参照してください。
   
このチェック ボックスをオンにした場合、 **[新規]** を選択することで Azure-SSIS IR に複数のパッケージ ストアを追加できます。 逆に、1 つのパッケージ ストアを複数の Azure-SSIS IR で共有することもできます。

:::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png" alt-text="MSDB、ファイル システム、Azure Files のデプロイ設定":::

**[Add package store]\(パッケージ ストアの追加\)** ペインで、次の手順を実行します。
   
   1. **[Package store name]\(パッケージ ストア名\)** に、パッケージ ストアの名前を入力します。 

   1. **[Package store linked service]\(パッケージ ストアのリンクされたサービス\)** で、パッケージのデプロイ先のファイル システム、Azure Files、Azure SQL Managed Instance へのアクセス情報を格納する既存のリンクされたサービスを選択するか、 **[New]\(新規\)** を選択して新たに作成します。 **[New Linked Service]\(新しいリンクされたサービス\)** ペインで、次の手順を実行します。
   
      > [!NOTE]
      > **Azure File Storage** か **File System** にリンクされているサービスを利用し、Azure Files にアクセスできます。 **Azure File Storage** にリンクされているサービスを使用する場合、Azure-SSIS IR パッケージ ストアでは現在のところ、(**アカウント キー** でも **SAS URI** でもなく) **基本** 認証方法のみがサポートされています。  

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png" alt-text="リンクされたサービスのデプロイ設定":::

      1. **[名前]** に、リンクされたサービスの名前を入力します。 
         
      1. **[説明]** に、リンクされたサービスの説明を入力します。 
         
      1. **[Type]\(種類\)** で、 **[Azure File Storage]** 、 **[Azure SQL Managed Instance]** 、 **[ファイル システム]** のいずれかを選択します。

      1. **[統合ランタイム経由で接続]** は無視してかまいません。パッケージ ストアのアクセス情報は、常に Microsoft がお客様の Azure-SSIS IR を使用してフェッチするからです。

      1. **[Azure File Storage]** を選択した場合は、 **[認証方法]** に **[基本]** を選択して次の手順を実行します。 

         1. **[Account selection method]\(アカウントの選択方法\)** で、 **[From Azure subscription]\(Azure サブスクリプションから\)** または **[Enter manually]\(手動で入力\)** を選択します。
         
         1. **[From Azure subscription]\(Azure サブスクリプションから\)** を選択した場合は、適切な **Azure サブスクリプション**、**ストレージ アカウント名**、**ファイル共有** を選択します。
            
         1. **[Enter manually]\(手動で入力\)** を選択した場合は、 **[ホスト]** に「`\\<storage account name>.file.core.windows.net\<file share name>`」を、 **[ユーザー名]** に「`Azure\<storage account name>`」を、 **[パスワード]** に「`<storage account key>`」を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

      1. **[Azure SQL Managed Instance]** を選択した場合は、次の手順を実行します。 

         1. **[接続文字列]** を選択するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

         1. **[接続文字列]** を選択した場合は、次の手順を実行します。 
             1. **[アカウントの選択方法]** で **[Azure サブスクリプションから]** を選択した場合は、該当する **Azure サブスクリプション**、**サーバー名**、**エンドポイントの種類**、**データベース名** を選択します。 **[手動で入力]** を選択した場合は、次の手順を実行します。 
                1.  Azure SQL Managed Instance のプライベート エンドポイントまたはパブリック エンドポイントとして、それぞれ「`<server name>.<dns prefix>.database.windows.net`」または「`<server name>.public.<dns prefix>.database.windows.net,3342`」を **[完全修飾ドメイン名]** に入力します。 プライベート エンドポイントを入力した場合、ADF UI からは到達できないため、 **[テスト接続]** は利用できません。

                1. **[データベース名]** に「`msdb`」と入力します。

            1. **[認証の種類]** で、 **[SQL 認証]** 、 **[マネージド ID]** 、 **[サービス プリンシパル]** 、 **[ユーザー割り当てマネージド ID]** のいずれかを選択します。

                - **[SQL 認証]** を選択した場合は、適切な **ユーザー名** と **パスワード** を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

                -  **[マネージド ID]** を選択した場合は、ADF のシステム マネージド ID に Azure SQL Managed Instance へのアクセス権を付与します。

                - **[サービス プリンシパル]** を選択した場合は、適切な **サービス プリンシパル ID** と **サービス プリンシパル キー** を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

                -  **[ユーザー割り当てマネージド ID]** を選択した場合は、ADF の指定のユーザー割り当てマネージド ID に Azure SQL Managed Instance へのアクセス権を付与します。 これで、指定のユーザー割り当てマネージド ID を使用して作成された既存の資格情報を選択するか、新たに作成できます。

      1. **[ファイル システム]** を選択した場合は、パッケージのデプロイ先フォルダーの UNC パスを **[ホスト]** に入力すると共に、適切な **ユーザー名** と **パスワード** を入力するか、それがシークレットとして保存されている **Azure Key Vault** を選択します。

      1. **[テスト接続]** を選択し (該当する場合)、成功したら **[作成]** を選択します。

   1. 追加したパッケージ ストアが **[Deployment settings]\(デプロイ設定\)** ページに表示されます。 これらを削除する場合は、対応するチェック ボックスをオンにしてから **[削除]** を選択します。

**[テスト接続]** を選択し (該当する場合)、成功したら **[Continue]\(続行\)** を選択します。

### <a name="advanced-settings-page"></a>[詳細設定] ページ

**[統合ランタイムのセットアップ]** ペインの **[詳細設定]** ページで、次の手順を行います。

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png" alt-text="詳細設定":::

   1. **[Maximum Parallel Executions Per Node]\(ノードあたりの最大並列実行数\)** で、統合ランタイム クラスター内のノードあたりの同時に実行するパッケージの最大数を選択します。 サポートされているパッケージ数のみが表示されます。 コンピューティングやメモリを大量に使用する 1 つの大規模なパッケージを実行するために複数のコアを使用する場合は、低い数値を選択します。 1 つのコアで 1 つ以上の小規模なパッケージを実行する場合は、高い数値を選択します。

   1. Azure-SSIS IR 上で標準および高速カスタム セットアップを追加するかどうかを選択するには、 **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]\(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ランタイムをカスタマイズする\)** チェック ボックスをオンにします。 詳細については、「[Azure-SSIS 統合ランタイムの設定のカスタマイズ](./how-to-configure-azure-ssis-ir-custom-setup.md)」を参照してください。

      このチェック ボックスを選択した場合、次の手順を行います。

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png" alt-text="カスタム セットアップでの詳細設定":::
   
      1. **[Custom setup container SAS URI] (カスタム セットアップ コンテナー SAS URI)** では、標準カスタム セットアップのためのスクリプトや関連付けられたファイルを格納するコンテナーの SAS URI を入力します。

      1. **[Express custom setup] (高速カスタム セットアップ)** では、 **[新規]** を選択して **[Add express custom setup] (高速カスタム セットアップの追加)** パネルを開き、 **[Express custom setup type] (高速カスタム セットアップの種類)** ドロップダウン メニューの下にあるいずれかの種類 ( **[Run cmdkey command] (cmdkey コマンドの実行)** 、 **[Add environment variable] (環境変数の追加)** 、 **[Install licensed component] (ライセンス付与されたコンポーネントのインストール)** など) を選択します。

         **[Install licensed component]\(ライセンス付与されたコンポーネントのインストール\)** の種類を選択した場合は、その後に **[コンポーネント名]** ドロップダウン メニューで ISV パートナーの任意の統合コンポーネントを選択し、必要に応じて **[ライセンス キー]** / **[ライセンス ファイル]** ボックスにパートナーから購入した製品ライセンス キーを入力するか、製品ライセンス ファイルをアップロードします。
  
         追加された高速カスタム セットアップは、 **[詳細設定]** ページに表示されます。 これらを削除する場合は、対応するチェック ボックスをオンにしてから **[削除]** を選択できます。

   1. Azure-SSIS IR を仮想ネットワークに参加させるかどうかを選択するには、 **[Select a VNet for your Azure-SSIS Integration Runtime to join, allow ADF to create certain network resources, and optionally bring your own static public IP addresses]\(参加させる Azure-SSIS 統合ランタイムの VNet を選択し、ADF で特定のネットワーク リソースを作成できるようにし、必要に応じて独自の静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。 

      これをオンにするのは、仮想ネットワーク サービス エンドポイント/IP ファイアウォール規則/プライベート エンドポイントを備えた Azure SQL Database サーバーまたは SSISDB をホストする仮想ネットワークに参加する Azure SQL Managed Instance を使用する場合、またはセルフホステッド IR を構成せずにオンプレミスのデータへのアクセスを必要としている (つまり、SSIS パッケージ内にオンプレミスのデータ ソースまたは宛先がある) 場合です。 詳細については、「[仮想ネットワークへの Azure-SSIS IR の参加](join-azure-ssis-integration-runtime-virtual-network.md)」を参照してください。 

      このチェック ボックスを選択した場合、次の手順を行います。

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png" alt-text="仮想ネットワークの詳細設定":::

      1. **[サブスクリプション]** で、仮想ネットワークを含む Azure サブスクリプションを選択します。

      1. **[場所]** では、統合ランタイムと同じ場所が選択されています。

      1. **[タイプ]** で、仮想ネットワークの種類を **Azure Resource Manager Virtual Network** または従来の仮想ネットワークから選択します。 従来の仮想ネットワークは間もなく非推奨になるので、**Azure Resource Manager Virtual Network** を選択することをお勧めします。

      1. **[VNet 名]** で、仮想ネットワークの名前を選択します。 これは、SSISDB をホストする Azure SQL Database サーバーの仮想ネットワーク サービス エンドポイントまたはプライベート エンドポイントの構成に使用されるものと同じものである必要があります。 または、SSISDB をホストする Azure SQL Managed Instance が参加するものと同じものである必要があります。 または、オンプレミス ネットワークに接続されているものと同じである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意の仮想ネットワークを指定できます。

      1. **[サブネット名]** で、お使いの仮想ネットワークのサブネットの名前を選択します。 これは、SSISDB をホストする Azure SQL Database サーバーの仮想ネットワーク サービス エンドポイントの構成に使用されるものと同じものである必要があります。 または、SSISDB をホストする Azure SQL Managed Instance が参加するものとは異なるサブネットである必要があります。 それ以外の場合は、Azure-SSIS IR に独自の静的パブリック IP アドレスを使用するための任意のサブネットを指定できます。

      1. **VNet インジェクション方法** の場合は、仮想ネットワーク インジェクションの方法として **[Express]\(高速\)** / **[Standard]\(標準\)** を選択します。 
   
         これらの方法を比較するには、「[標準と高速の仮想ネットワークの挿入方法の比較](azure-ssis-integration-runtime-virtual-network-configuration.md#compare)」に関する記事を参照してください。 
   
         **[Express]\(高速\)** を選択した場合は、「[高速仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-express-virtual-network-injection.md)」の記事を参照してください。 
      
         **[Standard]\(標準\)** を選択した場合は、「[標準仮想ネットワーク インジェクション方法](azure-ssis-integration-runtime-standard-virtual-network-injection.md)」の記事を参照してください。  この方法を使用すると、次の処理を実行できます。

         1. Azure-SSIS IR に独自の静的パブリック IP アドレスを使用して、それらをデータ ストアのファイアウォールで許可するかどうかを選択するには、 **[Bring static public IP addresses for your Azure-SSIS Integration Runtime]\(Azure-SSIS 統合ランタイムに静的パブリック IP アドレスを使用する\)** チェック ボックスをオンにします。

            このチェック ボックスを選択した場合、次の手順を行います。

            1. **[First static public IP address]\(最初の静的パブリック IP アドレス\)** では、Azure-SSIS IR の [要件を満たす](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip)最初の静的パブリック IP アドレスを選択します。 まだお持ちでない場合は、 **[新規作成]** リンクをクリックして Azure portal で静的パブリック IP アドレスを作成してから、ここにある更新ボタンをクリックして、それを選択できるようにします。
      
            1. **[Second static public IP address]\(2 番目の静的パブリック IP アドレス\)** では、Azure-SSIS IR の [要件を満たす](azure-ssis-integration-runtime-standard-virtual-network-injection.md#ip) 2 番目の静的パブリック IP アドレスを選択します。 まだお持ちでない場合は、 **[新規作成]** リンクをクリックして Azure portal で静的パブリック IP アドレスを作成してから、ここにある更新ボタンをクリックして、それを選択できるようにします。

   1. セルフホステッド IR を Azure-SSIS IR のプロキシとして構成するかどうかを選択するには、 **[Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime] (セルフホステッド統合ランタイムを Azure-SSIS 統合ランタイムのプロキシとして設定する)** チェック ボックスをオンにします。 詳細については、[セルフホステッド IR のプロキシとしての設定](./self-hosted-integration-runtime-proxy-ssis.md)に関するページを参照してください。 

      このチェック ボックスを選択した場合、次の手順を行います。

      :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png" alt-text="セルフホステッド IR の詳細設定":::

      1. **[Self-Hosted Integration Runtime] (セルフホステッド統合ランタイム)** では、既存のセルフホステッド IR を Azure-SSIS IR のプロキシとして選択します。

      1. **[Staging Storage Linked Service]\(ステージング ストレージのリンクされたサービス\)** では、既存の Azure BLOB ストレージのリンクされたサービスを選択するか、またはステージング用の新しいリンクされたサービスを作成します。

      1. **[Staging Path] (ステージング パス)** では、選択した Azure BLOB ストレージ アカウント内の BLOB コンテナーを指定するか、またはこれを空のままにしてステージング用の既定のパスを使用します。

   1. **[VNet Validation]\(VNet の検証\)** を選択します。 検証が成功した場合は、 **[続行]** を選択します。 

**[概要]** ページで、Azure-SSIS IR のすべての設定を確認し、推奨されるドキュメント リンクをブックマークし、 **[完了]** を選択して統合ランタイムの作成を開始します。

> [!NOTE]
> この処理は、カスタムのセットアップ時間があればそれを除くと、5 分以内で完了するはずです。 ただし、標準の挿入方法を使用して Azure-SSIS IR を仮想ネットワークに参加させるには、20 から 30 分かかる場合があります。
>
> SSISDB を使用する場合、Data Factory サービスは、SSISDB を準備するためにデータベース サーバーに接続します。 また、仮想ネットワーク (指定されている場合) に必要なアクセス許可と設定を構成し、Azure-SSIS IR を仮想ネットワークに参加させます。
>
> Azure-SSIS IR をプロビジョニングすると、Access の再頒布可能パッケージと Azure Feature Pack for SSIS もインストールされます。 これらのコンポーネントは、組み込みのコンポーネントで既にサポートされているデータ ソースに加えて、Excel ファイルと Access ファイルのほか、さまざまな Azure データ ソースに対する接続を実現するものです。 組み込みコンポーネントとプレインストール コンポーネントの詳細については、[Azure-SSIS IR の組み込みコンポーネントとプレインストール コンポーネント](./built-in-preinstalled-components-ssis-integration-runtime.md)に関するページを参照してください。 その他インストールできるコンポーネントの詳細については、[Azure-SSIS IR のカスタム設定](./how-to-configure-azure-ssis-ir-custom-setup.md)に関するページを参照してください。

### <a name="connections-pane"></a>[接続] ペイン

**[管理]** ハブの **[接続]** ペインで、 **[Integration runtimes]\(統合ランタイム\)** ページに切り替え、 **[Refresh]\(更新\)** を選択します。 

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png" alt-text="[接続] ペイン":::

   Azure-SSIS IR は、その名前を選択することで編集したり再構成したりすることができます。 該当するボタンを選択することで、Azure-SSIS IR を監視、開始、停止、削除したり、Azure-SSIS IR 上で動作する SSIS パッケージの実行アクティビティを含む ADF パイプラインを自動生成したりできます。また、Azure-SSIS IR の JSON コード (またはペイロード) を表示することもできます。  Azure-SSIS IR の編集と削除は、それが停止しているときにしか実行できません。

## <a name="azure-ssis-integration-runtimes-in-the-portal"></a>ポータルでの Azure SSIS 統合ランタイム

1. Azure Data Factory の UI で、 **[管理]** タブに切り替え、 **[接続]** ペインの **[Integration runtimes]\(統合ランタイム\)** タブに切り替えて、データ ファクトリ内の既存の統合ランタイムを表示します。

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png" alt-text="既存の IR を表示する":::

1. **[新規]** を選択して Azure-SSIS IR を作成し、 **[統合ランタイムのセットアップ]** ペインを開きます。

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png" alt-text="メニューによる統合ランタイム":::

1. **[統合ランタイムのセットアップ]** ペインで、 **[Azure で実行するには、既存の SSIS パッケージをリフトアンドシフトします]** タイルを選択してから、 **[Continue]\(続行\)** を選択します。

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png" alt-text="統合ランタイムの種類を指定する":::

1. Azure-SSIS-IR を設定する残りの手順については、「[Azure SSIS 統合ランタイムをプロビジョニングする](#provision-an-azure-ssis-integration-runtime)」セクションを参照してください。
 
## <a name="next-steps"></a>次のステップ

- [Azure PowerShell を使用してAzure-SSIS IR を作成する](create-azure-ssis-integration-runtime-powershell.md)。
- [Azure Resource Manager テンプレートを使用して Azure-SSIS IR 作成する](create-azure-ssis-integration-runtime-resource-manager-template.md)。
- [Azure-SSIS IR で SSIS パッケージをデプロイして実行する](create-azure-ssis-integration-runtime-deploy-packages.md)。

Azure-SSIS IR の詳細については、次の記事を参照してください。 

- 「[Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime)」。 この記事では、Azure-SSIS IR を含め、IR に関する全般的な概念情報が説明されています。 
- [Azure-SSIS IR を監視する](monitor-integration-runtime.md#azure-ssis-integration-runtime): この記事では、Azure-SSIS IR についての情報を取得して理解するための方法を示します。
- [Azure-SSIS IR を管理する](manage-azure-ssis-integration-runtime.md): この記事では、Azure-SSIS IR を停止、開始、または削除する方法を示しています。 また、ノードを追加することで Azure-SSIS IR をスケールアウトする方法も説明されています。
- [Azure での SSIS パッケージのデプロイ、実行、監視](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Azure での SSISDB への接続](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Windows 認証を使用したオンプレミス データ ストアへの接続](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Azure で SSIS パッケージの実行スケジュールを設定する](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
