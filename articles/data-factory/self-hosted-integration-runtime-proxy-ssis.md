---
title: セルフホステッド統合ランタイムを SSIS のプロキシとして構成する
description: セルフホステッド統合ランタイムを Azure-SSIS Integration Runtime のプロキシとして構成する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 09/09/2020
ms.openlocfilehash: d135320d8dd9f86fbc313b17b8b55ed3c609e9dc
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89595022"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>セルフホステッド IR を Azure Data Factory で Azure-SSIS IR のプロキシとして構成する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、セルフホステッド統合ランタイム (セルフホステッド IR) をプロキシとして構成して、Azure Data Factory の Azure-SSIS Integration Runtime (Azure-SSIS IR) で SQL Server Integration Services (SSIS) パッケージを実行する方法について説明します。 

この機能を使用すると、[Azure-SSIS IR を仮想ネットワークに参加](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)させずに、オンプレミスのデータにアクセスすることができます。 この機能は、企業ネットワークの構成が複雑すぎるかポリシーの制限が厳しすぎて Azure-SSIS IR を導入できない場合に役立ちます。

この機能により、必要に応じて、SSIS データ フロー タスクが 2 つのステージング タスクに分割されるようになります。 
* **オンプレミスのステージング タスク**:このタスクでは、セルフホステッド IR 上のオンプレミスのデータ ストアに接続するデータ フロー コンポーネントが実行されます。 これは、データをオンプレミスのデータ ストアから Azure BLOB ストレージのステージング領域に、またはその逆に移動します。
* **クラウド ステージング タスク**:このタスクでは、Azure-SSIS IR 上のオンプレミスのデータ ストアに接続しないデータ フロー コンポーネントが実行されます。 これは、データを Azure BLOB ストレージのステージング領域からクラウド データ ストアに、またはその逆に移動します。

データ フロー タスクを使用してオンプレミスからクラウドにデータを移動した場合、最初と 2 番目のステージング タスクは、それぞれオンプレミスとクラウドのステージング タスクになります。 データ フロー タスクを使用してクラウドからオンプレミスにデータを移動した場合、最初と 2 番目のステージング タスクは、それぞれクラウドとオンプレミスのステージング タスクになります。 データ フロー タスクを使用してオンプレミスからオンプレミスにデータを移動した場合、最初と 2 番目のステージング タスクは、両方ともオンプレミスのステージング タスクになります。 データ フロー タスクを使用してクラウドからクラウドにデータを移動した場合、この機能は適用されません。

この機能の他の利点や機能としては、まだ Azure-SSIS IR でサポートされていないリージョンでのセルフホステッド IR の設定が可能になったり、データ ソースのファイアウォールでのセルフホステッド IR のパブリック静的 IP アドレスが許可されたりすることなどがあります。

## <a name="prepare-the-self-hosted-ir"></a>セルフホステッド IR を準備する

この機能を使用するには、最初にデータ ファクトリを作成し、その中に Azure-SSIS IR を設定します。 まだ行っていない場合は、[Azure-SSIS IR の設定](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)に関する手順に従ってください。

次に、Azure-SSIS IR が設定されているのと同じデータ ファクトリで、セルフホステッド IR を設定します。 これを行うには、[セルフホステッド IR の作成](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)に関する記事を参照してください。

最後に、次のように、オンプレミスのマシンまたは Azure 仮想マシン (VM) に、セルフホステッド IR の最新バージョン、および追加のドライバーとランタイムをダウンロードしてインストールします。
- [セルフホステッド IR](https://www.microsoft.com/download/details.aspx?id=39717) の最新バージョンをダウンロードしてインストールします。
- パッケージでオブジェクトのリンクと埋め込みデータベース (OLEBC) または Open Database Connectivity (ODBC) コネクタを使用する場合は、セルフホステッド IR がインストールされている同じコンピューターに、関連するドライバーをダウンロードしてインストールしてください (まだ行っていない場合)。  

  SQL Server 用の OLEDB ドライバーの以前のバージョン (SQL Server Native Client (SQLNCLI)) を使用する場合は、[64 ビット版をダウンロードします](https://www.microsoft.com/download/details.aspx?id=50402)。  

  SQL Server 用の OLEDB ドライバーの最新バージョン (MSOLEDBSQL) を使用する場合は、[64 ビット版をダウンロードします](https://www.microsoft.com/download/details.aspx?id=56730)。  
  
  PostgreSQL、MySQL、Oracle などの他のデータベース システム用の OLEDB/ODBC ドライバーを使用する場合は、それらの Web サイトから 64 ビット版をダウンロードできます。
- セルフホステッド IR がインストールされているのと同じコンピューターに、[64 ビット版の Visual C++ (VC) ランタイムをダウンロードしてインストールします](https://www.microsoft.com/download/details.aspx?id=40784) (まだ行っていない場合)。

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>Azure Blob Storage のリンクされたサービスをステージング用に準備する

Azure Blob Storage のリンクされたサービスを、Azure-SSIS IR が設定されているのと同じデータ ファクトリに作成します (まだそうしていない場合)。 これを行うには、[Azure データ ファクトリのリンクされたサービスの作成 ](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)に関する記事を参照してください。 次のことを行ってください。
- **[データ ソース]** で **[Azure Blob Storage]** を選択します。  
- **統合ランタイム経由で接続**する場合は、既定の Azure IR を使用して Azure Blob Storage のアクセス資格情報を取り込むため、(Azure-SSIS IR でもセルフホステッド IR でもなく) **AutoResolveIntegrationRuntime** を選択します。
- **[認証方法]** で、 **[アカウント キー]** 、 **[SAS URI]** 、または **[サービス プリンシパル]** を選択します。  

    >[!TIP]
    >**サービス プリンシパル** メソッドを選択した場合は、サービス プリンシパルに少なくとも *ストレージ BLOB データ共同作成者* のロールを付与します。 詳細については、 [Azure Blob Storage コネクタ](connector-azure-blob-storage.md#linked-service-properties)に関するページを参照してください。

![Azure Blob Storage のリンクされたサービスをステージング用に準備する](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>セルフホステッド IR をプロキシとして使用して Azure-SSIS IR を構成する

セルフホステッド IR と Azure Blob Storage のリンクされたサービスをステージング用に準備したので、次にデータ ファクトリ ポータルまたはアプリでセルフホステッド IR をプロキシとして使用して、新規または既存の Azure-SSIS IR を構成することができます。 ただし、上記を実行する前に、既存の Azure-SSIS IR が実行されている場合は、これを停止し、後で再起動してください。

1. **[Integration Runtime Setup]\(統合ランタイムの設定\)** ペインで、 **[次へ]** を選択して、 **[全般設定]** と **[SQL 設定]** セクションをスキップします。 

1. **[詳細設定]** セクションで、次の手順を実行します。

   1. **[Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime]\(セルフホステッド IR を Azure-SSIS 統合ランタイムのプロキシとして設定する\)** チェックボックスをオンにします。 

   1. **[Self-Hosted Integration Runtime]\(セルフホステッド統合ランタイム\)** ドロップダウン リストで、既存のセルフホステッド IR を Azure-SSIS IR のプロキシとして選択します。

   1. **[Staging storage linked service]\(ステージング ストレージのリンクされたサービス\)** ドロップダウン リストで、既存の Azure BLOB ストレージのリンクされたサービスを選択するか、またはステージング用の新しいリンクされたサービスを作成します。

   1. **[ステージング パス]** ボックスで、選択した Azure BLOB ストレージ アカウント内の BLOB コンテナーを指定するか、またはこれを空のままにしてステージング用の既定のパスを使用します。

   1. **[続行]** をクリックします。

   ![セルフホステッド IR の詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

また、PowerShell を使用し、セルフホステッド IR をプロキシとして使用して新規または既存の Azure-SSIS IR を構成することもできます。

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
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

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS パッケージのプロキシでの接続を有効にする

最新の SSDT を Visual Studio の SSIS Projects 拡張機能またはスタンドアロン インストーラのいずれかとして使用すると、サポートされているデータ フロー コンポーネントの接続マネージャーに追加された新しい `ConnectByProxy` プロパティを見つけることができます。
* [Visual Studio 用の SSIS プロジェクト拡張機能をダウンロードする](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [スタンドアロン インストーラーをダウンロードする](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

オンプレミスでデータにアクセスするコンポーネントを持つデータ フロー タスクを含む新しいパッケージを設計する場合、このプロパティを有効にするには、関連する接続マネージャーの **[プロパティ]** ウィンドウでこれを "*True*" に設定します。

![ConnectByProxy プロパティを有効にする](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

また、既存のパッケージを実行する場合も、それらを 1 つずつ手動で変更することなく、このプロパティを有効にできます。  2 つのオプションがあります。
- **オプション A**: Azure-SSIS IR で実行する、最新の SSDT を備えたこれらのパッケージを含むプロジェクトを開いて、再構築し、再デプロイする。 その後、関連する接続マネージャーに対して *True* に設定して、このプロパティを有効にできます。 SSMS からパッケージを実行している場合、これらの接続マネージャーは、 **[パッケージの実行]** ポップアップ ウィンドウの **[接続マネージャー]** タブに表示されます。

  ![ConnectByProxy プロパティを有効にする 2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  また、Data Factory パイプラインでパッケージを実行するときに [SSIS パッケージの実行アクティビティ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)の **[接続マネージャー]** タブに表示される、関連する接続マネージャーに対して *True* に設定することでプロパティを有効にできます。
  
  ![ConnectByProxy プロパティを有効にする 3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **オプション B:** SSIS IR で実行するこれらのパッケージを含むプロジェクトを再デプロイする。 プロパティ パス `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` を指定し、SSMS からパッケージを実行するときに **[パッケージの実行]** ポップアップ ウィンドウの **[詳細]** タブでプロパティ オーバーライドとして *True* に設定することでプロパティを有効にできます。

  ![ConnectByProxy プロパティを有効にする 4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  プロパティ パス `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` を指定し、Data Factory パイプラインでパッケージを実行するときに [SSIS パッケージ実行アクティビティ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)の **[プロパティのオーバーライド]** タブでプロパティ オーバーライドとして *True* に設定することでプロパティを有効にできます。
  
  ![ConnectByProxy プロパティを有効にする 5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-on-premises-and-cloud-staging-tasks"></a>オンプレミスとクラウドのステージング タスクをデバッグする

セルフホステッド IR では、ランタイム ログは *C:\ProgramData\SSISTelemetry* フォルダー内にあり、オンプレミスのステージング タスクの実行ログは *C:\ProgramData\SSISTelemetry\ExecutionLog* フォルダー内にあります。  クラウドにあるステージング タスクの実行ログは、パッケージを格納した場所が SSISDB であるか否かに応じて、SSISDB 内、または指定されたロギング パスにあります。 オンプレミスのステージング タスクの一意の ID も、クラウドにあるステージング タスクの実行ログ内にあります。 

![1 番目のステージング タスクの一意の ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-on-premises-staging-tasks"></a>オンプレミスのステージング タスクで Windows 認証を使用する

セルフホステッド IR にあるオンプレミスのステージング タスクで Windows 認証が必要な場合は、[同じ Windows 認証を使用するように SSIS パッケージを構成します](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)。 

オンプレミスのステージング タスクは、セルフホステッド IR サービス アカウント (既定では *NT SERVICE\DIAHostService*) を使用して起動され、データ ストアには Windows 認証アカウントを使用してアクセスされます。 どちらのアカウントにも、特定のセキュリティ ポリシーが割り当てられている必要があります。 セルフホステッド IR コンピューターで、 **[ローカル セキュリティ ポリシー]**  >  **[ローカル ポリシー]**  >  **[ユーザー権利の割り当て]** を選択し、次の操作を行います。

1. *[プロセスのメモリ クォータの増加]* ポリシーと *[プロセス レベル トークンの置き換え]* ポリシーをセルフホステッド IR サービス アカウントに割り当てます。 これは、既定のサービス アカウントを使用してセルフホステッド IR をインストールするときに自動的に実行されます。 そうでない場合は、手動でこれらのポリシーを割り当てます。 別のサービス アカウントを使用する場合は、同じポリシーを割り当てます。

1. *[サービスとしてログオン]* ポリシーを Windows 認証アカウントに割り当てます。

## <a name="billing-for-the-on-premises-and-cloud-staging-tasks"></a>オンプレミスとクラウドのステージング タスクの請求

セルフホステッド IR で実行されるオンプレミスのステージング タスクは、セルフホステッド IR で実行されるあらゆるデータ移動アクティビティが課金されるのと同じように、個別に課金されます。 これは、[Azure Data Factory データ パイプラインの価格](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)に関するページに記載されています。

Azure-SSIS IR で実行されるクラウドにあるステージング タスクは個別に課金されませんが、実行中の Azure-SSIS IR は、[Azure-SSIS IR の価格](https://azure.microsoft.com/pricing/details/data-factory/ssis/)に関する記事の指定どおりに課金されます。

## <a name="enabling-tls-12"></a>TLS 1.2 の有効化

強力な暗号化やより安全なネットワークプロトコル (TLS 1.2) を使用し、セルフホステッド IR で以前の SSL/TLS バージョンを無効にする必要がある場合は、パブリック プレビュー コンテナーの *CustomSetupScript/UserScenarios/TLS 1.2* フォルダーに置かれた、*main.cmd* スクリプトをダウンロードして実行できます。  [Azure Storage Explorer](https://storageexplorer.com/) を使用して、次の SAS URI を入力することで、パブリック プレビュー コンテナーに接続できます。

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>現在の制限

- OLEDB、ODBC、フラット ファイルのソースまたは OLEDB 出力先を使用するデータ フロー タスクのみが、現在サポートされています。
- *アカウント キー*、*Shared Access Signature (SAS) URI*、または*サービス プリンシパル*の認証を使って構成された Azure Blob Storage のリンクされたサービスのみが現在サポートされています。
- OLEDB ソースの *ParameterMapping* は現在サポートされていません。 回避策として、*AccessMode* として*変数からの SQL コマンド*を使用し、*式*を使用して SQL コマンドに変数やパラメーターを挿入してください。 図のように、パブリック プレビュー コンテナーの *SelfHostedIRProxy/Limitations* フォルダーに置かれた *ParameterMappingSample.dtsx* パッケージを参照してください。 Azure Storage Explorer を使用して、上の SAS URI を入力することで、パブリック プレビュー コンテナーに接続できます。

## <a name="next-steps"></a>次のステップ

セルフホステッド IR を Azure-SSIS IR のプロキシとして構成したら、Data Factory パイプラインの SSIS パッケージ実行アクティビティとしてパッケージをデプロイおよび実行してオンプレミスのデータにアクセスできます。 方法については、[Data Factory パイプラインの SSIS パッケージ実行アクティビティとしての SSIS パッケージの実行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。
