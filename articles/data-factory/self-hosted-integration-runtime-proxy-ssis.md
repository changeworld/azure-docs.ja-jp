---
title: セルフホステッド統合ランタイムを SSIS のプロキシとして構成する
description: セルフホステッド統合ランタイムを Azure-SSIS Integration Runtime のプロキシとして構成する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/22/2021
ms.openlocfilehash: 776329b23ab4e0fdd11e48da6ec1a7cc3d21a7f7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131852315"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>セルフホステッド IR を Azure Data Factory で Azure-SSIS IR のプロキシとして構成する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、セルフホステッド統合ランタイム (セルフホステッド IR) をプロキシとして構成して、Azure Data Factory (ADF) の Azure-SSIS Integration Runtime (Azure-SSIS IR) で SQL Server Integration Services (SSIS) パッケージを実行する方法について説明します。 

この機能を使用すると、[Azure-SSIS IR を仮想ネットワークに参加](./join-azure-ssis-integration-runtime-virtual-network.md)させずに、オンプレミスのデータや実行タスクにアクセスすることができます。 この機能は、企業ネットワークの構成が複雑すぎるかポリシーの制限が厳しすぎて Azure-SSIS IR を導入できない場合に役立ちます。

<<<<<<< HEAD
この機能により、必要に応じて、SSIS データ フロー タスクが 2 つのステージング タスクに分割されるようになります。 
* **オンプレミスのステージング タスク**:このタスクでは、セルフホステッド IR 上のオンプレミスのデータ ストアに接続するデータ フロー コンポーネントが実行されます。 これは、データをオンプレミスのデータ ストアから Azure Blob Storage のステージング領域に、またはその逆に移動します。
* **クラウド ステージング タスク**:このタスクでは、Azure-SSIS IR 上のオンプレミスのデータ ストアに接続しないデータ フロー コンポーネントが実行されます。 これは、データを Azure Blob Storage のステージング領域からクラウド データ ストアに、またはその逆に移動します。
=======
この機能は、現時点では、SSIS データ フロー タスクと、SQL およびプロセス実行タスクでのみ有効にすることができます。 

データ フロー タスクでこの機能を有効にすると、該当する場合は、次の 2 つのステージング タスクに分割されます。 
* **オンプレミスのステージング タスク**:このタスクでは、セルフホステッド IR 上のオンプレミスのデータ ストアに接続するデータ フロー コンポーネントが実行されます。 これは、データをオンプレミスのデータ ストアから Azure BLOB ストレージのステージング領域に、またはその逆に移動します。
* **クラウド ステージング タスク**:このタスクでは、Azure-SSIS IR 上のオンプレミスのデータ ストアに接続しないデータ フロー コンポーネントが実行されます。 これは、データを Azure BLOB ストレージのステージング領域からクラウド データ ストアに、またはその逆に移動します。
>>>>>>> repo_sync_working_branch

データ フロー タスクを使用してオンプレミスからクラウドにデータを移動した場合、最初と 2 番目のステージング タスクは、それぞれオンプレミスとクラウドのステージング タスクになります。 データ フロー タスクを使用してクラウドからオンプレミスにデータを移動した場合、最初と 2 番目のステージング タスクは、それぞれクラウドとオンプレミスのステージング タスクになります。 データ フロー タスクを使用してオンプレミスからオンプレミスにデータを移動した場合、最初と 2 番目のステージング タスクは、両方ともオンプレミスのステージング タスクになります。 データ フロー タスクを使用してクラウドからクラウドにデータを移動した場合、この機能は適用されません。

この機能は、SQL およびプロセス実行タスクで有効になり、セルフホステッド IR で実行されます。 

この機能の他の利点や機能としては、まだ Azure-SSIS IR でサポートされていないリージョンでのセルフホステッド IR の設定が可能になったり、データ ソースのファイアウォールでのセルフホステッド IR のパブリック静的 IP アドレスが許可されたりすることなどがあります。

## <a name="prepare-the-self-hosted-ir"></a>セルフホステッド IR を準備する

この機能を使用するには、最初にデータ ファクトリを作成し、その中に Azure-SSIS IR を設定します。 まだ行っていない場合は、[Azure-SSIS IR の設定](./tutorial-deploy-ssis-packages-azure.md)に関する手順に従ってください。

次に、Azure-SSIS IR が設定されているのと同じデータ ファクトリで、セルフホステッド IR を設定します。 これを行うには、[セルフホステッド IR の作成](./create-self-hosted-integration-runtime.md)に関する記事を参照してください。

最後に、次のように、オンプレミスのマシンまたは Azure 仮想マシン (VM) に、セルフホステッド IR の最新バージョン、および追加のドライバーとランタイムをダウンロードしてインストールします。
- [セルフホステッド IR](https://www.microsoft.com/download/details.aspx?id=39717) の最新バージョンをダウンロードしてインストールします。
- パッケージでオブジェクトのリンクと埋め込みデータベース (OLE DB)、Open Database Connectivity (ODBC)、または ADO.NET コネクタを使用する場合は、セルフホステッド IR がインストールされている同じコンピューターに、関連するドライバーをダウンロードしてインストールしてください (まだ行っていない場合)。  

  SQL Server 用の OLEDB ドライバーの以前のバージョン (SQL Server Native Client (SQLNCLI)) を使用する場合は、[64 ビット版をダウンロードします](https://www.microsoft.com/download/details.aspx?id=50402)。  

  SQL Server 用の OLEDB ドライバーの最新バージョン (MSOLEDBSQL) を使用する場合は、[64 ビット版をダウンロードします](https://www.microsoft.com/download/details.aspx?id=56730)。  
  
  PostgreSQL、MySQL、Oracle などの他のデータベース システム用の OLEDB/ODBC/ADO.NET ドライバーを使用する場合は、それらの Web サイトから 64 ビット版をダウンロードできます。
- パッケージで Azure Feature Pack のデータ フロー コンポーネントを使用する場合は、セルフホステッド IR がインストールされているのと同じマシンに [SQL Server 2017 用の Azure Feature Pack をダウンロードしてインストールします](https://www.microsoft.com/download/details.aspx?id=54798) (まだインストールしていない場合)。
- セルフホステッド IR がインストールされているのと同じコンピューターに、[64 ビット版の Visual C++ (VC) ランタイムをダウンロードしてインストールします](https://www.microsoft.com/en-us/download/details.aspx?id=40784) (まだ行っていない場合)。

### <a name="enable-windows-authentication-for-on-premises-tasks"></a>オンプレミスのタスクで Windows 認証を有効にする

セルフホステッド IR 上のオンプレミスのステージング タスクと、SQL およびプロセス実行タスクが Windows 認証を必要とする場合は、[Azure-SSIS IR で windows 認証機能も構成](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth)する必要があります。 

オンプレミスのステージング タスクと、SQL およびプロセス実行タスクは、セルフホステッド IR サービス アカウント (既定では *NT SERVICE\DIAHostService*) を使用して起動し、データ ストアには Windows 認証アカウントを使用してアクセスします。 どちらのアカウントにも、特定のセキュリティ ポリシーが割り当てられている必要があります。 セルフホステッド IR コンピューターで、 **[ローカル セキュリティ ポリシー]**  >  **[ローカル ポリシー]**  >  **[ユーザー権利の割り当て]** を選択し、次の操作を行います。

1. *[プロセスのメモリ クォータの増加]* ポリシーと *[プロセス レベル トークンの置き換え]* ポリシーをセルフホステッド IR サービス アカウントに割り当てます。 これは、既定のサービス アカウントを使用してセルフホステッド IR をインストールするときに自動的に実行されます。 そうでない場合は、手動でこれらのポリシーを割り当てます。 別のサービス アカウントを使用する場合は、同じポリシーを割り当てます。

1. *[サービスとしてログオン]* ポリシーを Windows 認証アカウントに割り当てます。

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Azure Blob Storage のリンクされたサービスをステージング用に準備する

Azure Blob Storage のリンクされたサービスを、Azure-SSIS IR が設定されているのと同じデータ ファクトリに作成します (まだそうしていない場合)。 これを行うには、[Azure Data Factory のリンクされたサービスの作成 ](./quickstart-create-data-factory-portal.md#create-a-linked-service)に関する記事を参照してください。 次のことを行ってください。
- **[データ ソース]** で **[Azure Blob Storage]** を選択します。  
- **統合ランタイム経由で接続** する場合は、Azure Blob Storage のアクセス資格情報を取り込むために Azure-SSIS IR を使うのでなく、(Azure-SSIS IR でもセルフホステッド IR でもなく) **AutoResolveIntegrationRuntime** を選択します。
- **[認証方法]** で、 **[アカウント キー]** 、 **[SAS URI]** 、 **[サービス プリンシパル]** 、 **[マネージド ID]** 、または **[ユーザー割り当てマネージド ID]** を選択します。  

>[!TIP]
>**サービス プリンシパル** 方法を選択した場合は、サービス プリンシパルに少なくとも *ストレージ BLOB データ共同作成者* のロールを付与します。 詳細については、「[Azure Blob Storage コネクタ](connector-azure-blob-storage.md#linked-service-properties)」をご覧ください。 **[マネージド ID]** / **[ユーザー割り当てマネージド ID]** の方式を選択した場合は、ADF の指定のシステムまたはユーザー割り当てマネージド ID に、Azure Blob Storage にアクセスするための適切なロールを付与します。 詳細については、[ADF の指定のシステムまたはユーザー割り当てマネージド ID で、Azure Active Directory (Azure AD) 認証を使用して Azure Blob Storage にアクセスする](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)方法に関するページをご覧ください。

:::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png" alt-text="Azure Blob Storage のリンクされたサービスをステージング用に準備する":::

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>セルフホステッド IR をプロキシとして使用して Azure-SSIS IR を構成する

セルフホステッド IR と Azure Blob Storage のリンク サービスをステージング用に準備したので、次にデータ ファクトリ ポータルまたはアプリでセルフホステッド IR をプロキシとして使用して、新規または既存の Azure-SSIS IR を構成することができます。 ただし、上記を実行する前に、既存の Azure-SSIS IR が実行されている場合は、これを停止し、後で再起動してください。

1. **[統合ランタイムのセットアップ]** ペインで、 **[続行]** ボタンを選択して、 **[全般設定]** と **[デプロイ設定]** ページをスキップします。 

1. **[詳細設定]** ページで、次の手順を実行します。

   1. **[Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime]\(セルフホステッド IR を Azure-SSIS 統合ランタイムのプロキシとして設定する\)** チェックボックスをオンにします。 

   1. **[Self-Hosted Integration Runtime]\(セルフホステッド統合ランタイム\)** ドロップダウン リストで、既存のセルフホステッド IR を Azure-SSIS IR のプロキシとして選択します。

<<<<<<< HEAD
   1. **[Staging storage linked service]\(ステージング ストレージのリンクされたサービス\)** ドロップダウン リストで、既存の Azure Blob Storage のリンクされたサービスを選択するか、またはステージング用の新しいリンクされたサービスを作成します。

   1. **[ステージング パス]** ボックスで、選択した Azure Blob Storage アカウント内の BLOB コンテナーを指定するか、またはこれを空のままにしてステージング用の既定のパスを使用します。
=======
   1. **[ステージング ストレージのリンク サービス]** ドロップダウン リストで、既存の Azure BLOB ストレージのリンク サービスを選択するか、またはステージング用の新しいリンク サービスを作成します。

   1. **[ステージング パス]** ボックスで、選択した Azure ストレージ アカウント内の BLOB コンテナーを指定するか、またはこれを空のままにしてステージング用の既定のパスを使用します。
>>>>>>> repo_sync_working_branch

   1. **[Continue]\(続行\)** を選択します。

   :::image type="content" source="./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png" alt-text="セルフホステッド IR の詳細設定":::

また、PowerShell を使用し、セルフホステッド IR をプロキシとして使用して新規または既存の Azure-SSIS IR を構成することもできます。

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data access
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-use-a-proxy"></a>SSIS パッケージでプロキシを使用できるようにする

最新の SSDT を Visual Studio の SSIS Projects 拡張機能またはスタンドアロンのインストーラのいずれかとして使用すると、サポートされているデータ フロー コンポーネントの接続マネージャーでは新しい `ConnectByProxy` プロパティを、SQL およびプロセス実行タスクでは `ExecuteOnProxy` プロパティを見つけることができます。
* [Visual Studio 用の SSIS プロジェクト拡張機能をダウンロードする](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [スタンドアロン インストーラーをダウンロードする](/sql/ssdt/download-sql-server-data-tools-ssdt#ssdt-for-vs-2017-standalone-installer)   

オンプレミスでデータにアクセスするコンポーネントを持つデータ フロー タスクを含む新しいパッケージを設計する場合、`ConnectByProxy` プロパティを有効にするには、関連する接続マネージャーの **[プロパティ]** ウィンドウでこれを "*True*" に設定します。

オンプレミスで実行する SQL およびプロセス実行タスクを含む新しいパッケージを設計する場合、`ExecuteOnProxy` プロパティを有効にするには、関連するタスク自身の **[プロパティ]** ペインでこれを *True* に設定します。

:::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-proxy-properties.png" alt-text="ConnectByProxy および ExecuteOnProxy プロパティを有効にする":::

また、既存のパッケージを実行する場合も、それらを 1 つずつ手動で変更することなく、`ConnectByProxy`/`ExecuteOnProxy` プロパティを有効にできます。 2 つのオプションがあります。
- **オプション A**: Azure-SSIS IR で実行する、最新の SSDT を備えたこれらのパッケージを含むプロジェクトを開いて、再構築し、再デプロイする。 SSMS からパッケージを実行するときに **[パッケージの実行**] ポップアップ ウィンドウの **[接続マネージャー]** タブに表示される、関連する接続マネージャーに対して *True* に設定することで `ConnectByProxy` プロパティを有効にできます。

  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png" alt-text="ConnectByProxy および ExecuteOnProxy プロパティを有効にする 2":::

  また、Data Factory パイプラインでパッケージを実行するときに [SSIS パッケージの実行アクティビティ](./how-to-invoke-ssis-package-ssis-activity.md)の **[接続マネージャー]** タブに表示される、関連する接続マネージャーに対して *True* に設定することで `ConnectByProxy` プロパティを有効にできます。
  
  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png" alt-text="ConnectByProxy および ExecuteOnProxy プロパティを有効にする 3":::

- **オプション B:** SSIS IR で実行するこれらのパッケージを含むプロジェクトを再デプロイする。 次に、プロパティ パス `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`/`\Package\YourExecuteSQLTaskName.Properties[ExecuteOnProxy]`/`\Package\YourExecuteProcessTaskName.Properties[ExecuteOnProxy]` を指定し、SSMS からパッケージを実行するときに **[パッケージの実行]** ポップアップ ウィンドウの **[詳細]** タブでプロパティ オーバーライドとして *True* に設定することで `ConnectByProxy`/`ExecuteOnProxy` プロパティを有効にできます。

  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png" alt-text="ConnectByProxy および ExecuteOnProxy プロパティを有効にする 4":::

  また、プロパティ パス `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`/`\Package\YourExecuteSQLTaskName.Properties[ExecuteOnProxy]`/`\Package\YourExecuteProcessTaskName.Properties[ExecuteOnProxy]` を指定し、Data Factory パイプラインでパッケージを実行するときに [SSIS パッケージ実行アクティビティ](./how-to-invoke-ssis-package-ssis-activity.md)の **[プロパティのオーバーライド]** タブでプロパティ オーバーライドとして *True* に設定することで `ConnectByProxy`/`ExecuteOnProxy` プロパティを有効にできます。
  
  :::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png" alt-text="ConnectByProxy および ExecuteOnProxy プロパティを有効にする 5":::

## <a name="debug-the-on-premises-tasks-and-cloud-staging-tasks"></a>オンプレミス タスクとクラウドのステージング タスクをデバッグする

セルフホステッド IR では、ランタイム ログは *C:\ProgramData\SSISTelemetry* フォルダー内にあり、オンプレミスのステージング タスクと、SQL およびプロセス実行タスクの実行ログは *C:\ProgramData\SSISTelemetry\ExecutionLog* フォルダー内にあります。 クラウドにあるステージング タスクの実行ログは、パッケージを格納した場所が SSISDB であるかどうかや、[Azure Monitor の統合](./monitor-ssis.md)などを有効にするかどうかに応じて、SSISDB 内、指定されたロギング ファイル パス、または Azure Monitor にあります。オンプレミスのステージング タスクの一意の ID も、クラウドにあるステージング タスクの実行ログ内にあります。 

:::image type="content" source="media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png" alt-text="1 番目のステージング タスクの一意の ID":::

カスタマー サポート チケットを発行した場合は、セルフホステッド IR にインストールされている **Microsoft Integration Runtime Configuration Manager** の **[診断]** タブ で **[ログの送信]** ボタンを選択して、最近の操作/実行ログを調査対象として送信できます。

## <a name="billing-for-the-on-premises-tasks-and-cloud-staging-tasks"></a>オンプレミス タスクとクラウドのステージング タスクの請求

セルフホステッド IR で実行されるオンプレミスのステージング タスクと SQL およびプロセス実行タスクは、セルフホステッド IR で実行されるあらゆるデータ移動アクティビティが課金されるのと同じように、個別に課金されます。 これは、[Azure Data Factory データ パイプラインの価格](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)に関するページに記載されています。

Azure-SSIS IR で実行されるクラウドにあるステージング タスクは個別に課金されませんが、実行中の Azure-SSIS IR は、[Azure-SSIS IR の価格](https://azure.microsoft.com/pricing/details/data-factory/ssis/)に関する記事の指定どおりに課金されます。

## <a name="enable-custom3rd-party-data-flow-components"></a>カスタム/サードパーティのデータフロー コンポーネントを有効にする 

Azure-SSIS IR のプロキシとしてセルフホステッド IR を使用してオンプレミスのデータにアクセスするために、カスタム/サードパーティ データ フロー コンポーネントを有効にするには、次の手順に従います。

1. [標準/簡易カスタム セットアップ](./how-to-configure-azure-ssis-ir-custom-setup.md)を使用して、Azure-SSIS IR に SQL Server 2017 をターゲットとするカスタム/サードパーティ データ フロー コンポーネントをインストールします。

1. セルフホステッド IR に次の DTSPath レジストリ キーを作成します (まだ存在しない場合)。
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` を `C:\Program Files\Microsoft SQL Server\140\DTS\` に設定
   1. `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\Microsoft SQL Server\140\SSIS\Setup\DTSPath` を `C:\Program Files (x86)\Microsoft SQL Server\140\DTS\` に設定
   
1. 上記の DTSPath でセルフホステッド IR に SQL Server 2017 をターゲットとするカスタム/サードパーティ データ フロー コンポーネントをインストールし、次のインストール プロセスを確認します。

   1. `<DTSPath>`、`<DTSPath>/Connections`、`<DTSPath>/PipelineComponents`、`<DTSPath>/UpgradeMappings` フォルダーがまだ存在しない場合は作成します。
   
   1. `<DTSPath>/UpgradeMappings` フォルダーに拡張機能マッピング用に独自の XML ファイルを作成します。
   
   1. カスタム/サードパーティ データ フロー コンポーネント アセンブリで参照されるすべてのアセンブリをグローバル アセンブリ キャッシュ (GAC) にインストールします。

ここでは、パートナーである [Theobald Software](https://kb.theobald-software.com/xtract-is/XIS-for-Azure-SHIR) と [Aecorsoft](https://www.aecorsoft.com/blog/2020/11/8/using-azure-data-factory-to-bring-sap-data-to-azure-via-self-hosted-ir-and-ssis-ir)の例を示します。こちらのデータ フロー コンポーネントは、高速カスタム セットアップと、セルフホステッド IR を Azure-SSIS IR のプロキシとしてを使用するように設定されています。

## <a name="enforce-tls-12"></a>TLS 1.2 を適用する

最も強力な暗号化や、最も安全なネットワーク プロトコル (TLS 1.2) のみを使用するように構成されている、ステージング用の Azure Blob Storage などのデータ ストアにアクセスする必要がある場合は、セルフホステッド IR で TLS 1.2 のみを有効にし、古い SSL/TLS バージョンは同時に無効にする必要があります。 パブリック プレビューの BLOB コンテナーの *CustomSetupScript/UserScenarios/TLS 1.2* フォルダーに用意されている *main.cmd* スクリプトをダウンロードして実行してください。 [Azure Storage Explorer](https://storageexplorer.com/) を使用して、次の SAS URI を入力することで、パブリック プレビュー BLOB コンテナーに接続できます。

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>現在の制限

- 現在、Hadoop/HDFS/DQS コンポーネントを除き、Azure-SSIS IR Standard Edition に組み込まれているか、事前にインストールされているデータ フロー コンポーネントのみがサポートされています。[Azure-SSIS IR に組み込まれているか、事前にインストールされているすべてのコンポーネント](./built-in-preinstalled-components-ssis-integration-runtime.md)に関するページをご覧ください。
- 現在、マネージド コード (.NET Framework) で記述されたカスタム/サードパーティのデータ フロー コンポーネントのみがサポートされています。ネイティブ コード (C++) で記述されたコンポーネントは、現在サポートされていません。
- オンプレミスとクラウドの両方のステージング タスクで変数値を変更することは、現在サポートされていません。
- オンプレミスのステージング タスクでオブジェクト型の変数値を変更しても、他のタスクには反映されません。
- OLEDB ソースの *ParameterMapping* は現在サポートされていません。 回避策として、*AccessMode* として *変数からの SQL コマンド* を使用し、*式* を使用して SQL コマンドに変数やパラメーターを挿入してください。 図のように、パブリック プレビュー BLOB コンテナーの *SelfHostedIRProxy/Limitations* フォルダーに置かれた *ParameterMappingSample.dtsx* パッケージを参照してください。 Azure Storage Explorer を使用して、上の SAS URI を入力することで、パブリック プレビュー BLOB コンテナーに接続できます。

## <a name="next-steps"></a>次のステップ

セルフホステッド IR を Azure-SSIS IR のプロキシとして構成したら、Data Factory パイプラインの SSIS パッケージ実行アクティビティとしてパッケージをデプロイおよび実行してオンプレミスのデータにアクセスできます。 方法については、[Data Factory パイプラインの SSIS パッケージ実行アクティビティとしての SSIS パッケージの実行](./how-to-invoke-ssis-package-ssis-activity.md)に関する記事を参照してください。
