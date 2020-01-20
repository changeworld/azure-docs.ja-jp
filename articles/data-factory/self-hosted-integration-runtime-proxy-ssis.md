---
title: セルフホステッド統合ランタイムを SSIS のプロキシとして構成する
description: セルフホステッド統合ランタイムを Azure-SSIS IR のプロキシとして構成する方法について説明します。
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
ms.date: 12/23/2019
ms.openlocfilehash: 8754b2ef9727b5526cc9bcf756085f2f05f6a398
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552102"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>セルフホステッド IR を ADF で Azure-SSIS IR のプロキシとして構成する

この記事では、セルフホステッド統合ランタイム (IR) をプロキシとして構成して、Azure Data Factory (ADF) の Azure SSIS 統合ランタイム (IR) で SQL Server Integration Services (SSIS) パッケージを実行する方法について説明します。  この機能を使用すると、[Azure-SSIS IR を仮想ネットワークに参加](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)させずに、オンプレミスのデータにアクセスすることができます。  これは、企業ネットワークが、Azure SSIS IR を挿入するには複雑すぎる構成または制限の厳しいポリシーを持っている場合に役立ちます。

この機能は、オンプレミスのデータ ソースを使用するデータ フロー タスクを含むパッケージを 2 つのステージング タスクに分割します。セルフホステッド IR で実行されている 1 番目のタスクが、まずオンプレミスのデータ ソースから Azure Blog Storage のステージング領域にデータを移動し、Azure-SSIS IR で実行されている 2 番目のタスクが、ステージング領域から目的のデータ宛先にデータを移動します。

また、この機能には、まだ Azure SSIS IR でサポートされていないリージョンでのセルフホステッド IR のプロビジョニングが可能になったり、データ ソースのファイアウォールでのセルフホステッド IR のパブリック静的 IP アドレスが許可されたりするなど、他の利点や機能もあります。

## <a name="prepare-self-hosted-ir"></a>セルフホステッド IR を準備する

この機能を使用するには、[Azure-SSIS IR をプロビジョニングする方法](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)に関する記事に従って、まず ADF を作成し、そこで Azure SSIS IR をプロビジョニングする必要があります (まだ行っていない場合)。

次に、[セルフホステッド IR を作成する方法](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)に関する記事に従って、Azure-SSIS IR がプロビジョニングされている同じ ADF にセルフホステッド IR をプロビジョニングする必要があります。

最後に、次のように、オンプレミスのマシンまたは Azure 仮想マシン (VM) に、セルフホステッド IR の最新バージョン、および追加のドライバーとランタイムをダウンロードしてインストールする必要があります。
- セルフホステッド IR の最新バージョンは、[こちら](https://www.microsoft.com/download/details.aspx?id=39717)からダウンロードしてインストールしてください。
- パッケージで OLEDB コネクタを使用する場合は、セルフホステッド IR がインストールされている同じコンピューターに、関連する OLEDB ドライバーをダウンロードしてインストールしてください (まだインストールしていない場合)。  SQL Server 用の OLEDB ドライバーの以前のバージョン (SQLNCLI) を使用する場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=50402)から 64 ビット版をダウンロードできます。  SQL Server 用の OLEDB ドライバーの最新バージョン (MSOLEDBSQL) を使用する場合は、[こちら](https://www.microsoft.com/download/details.aspx?id=56730)から 64 ビット版をダウンロードできます。  PostgreSQL、MySQL、Oracle などの他のデータベースシステム用の OLEDB ドライバーを使用する場合は、それぞれの Web サイトから 64 ビット版をダウンロードできます。
- セルフホステッド IR がインストールされている同じコンピューターに、Visual C++ (VC) ランタイムをダウンロードしてインストールしてください (まだインストールしていない場合)。  64 ビット版は、[こちら](https://www.microsoft.com/download/details.aspx?id=40784)からダウンロードできます。

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Azure Blob Storage のリンクされたサービスをステージング用に準備する

[ADF のリンクされたサービスを作成する方法](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)に関する記事に従って、Azure-SSIS IR がプロビジョニングされている同じ ADF に、Azure Blob Storage のリンクされたサービスを作成してください (またインストールしていない場合)。  以下のことを確認してください。
- **データ ストア**に **Azure Blob Storage** が選択されている
- **[Connect via integration runtime]\(統合ランタイム経由で接続\)** に **AutoResolveIntegrationRuntime** が選択されている
- **[認証方法]** に **[アカウント キー]** / **[SAS URI]** / **[サービス プリンシパル]** のいずれかが選択されている

>[!TIP]
> **[サービス プリンシパル]**   が選択されている場合は、 **ストレージ BLOB データ共同作成者ロール**以上を付与します。 詳細については、 [Azure Blob Storage コネクタ](connector-azure-blob-storage.md#linked-service-properties)に関するページを参照してください。

![Azure Blob Storage のリンクされたサービスをステージング用に準備する](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>セルフホステッド IR をプロキシとして使用して Azure-SSIS IR を構成する

セルフホステッド IR と Azure Blob Storage のリンクされたサービスをステージング用に準備したので、次に ADF ポータル/アプリでセルフホステッド IR をプロキシとして使用して、新規/既存の Azure SSIS IR を構成することができます。  既存の Azure SSIS IR が実行されている場合は、上記を実行する前に停止して、後で再起動してください。

1. 統合ランタイムの設定パネルで、 **[次へ]** ボタンを選択して **[全般設定]** および **[SQL 設定]** セクションに進みます。 

1. **[詳細設定]** セクションで次のようにします。

   1. **[Set up Self-Hosted Integration Runtime as a proxy for your Azure-SSIS Integration Runtime]\(セルフホステッド IR を Azure-SSIS 統合ランタイムのプロキシとして設定する\)** チェックボックスをオンにします。 

   1. **[Self-Hosted Integration Runtime]\(セルフホステッド統合ランタイム\)** については、既存のセルフホステッド IR を Azure-SSIS IR のプロキシとして選択します。

   1. **[Staging Storage Linked Service]\(ステージング ストレージのリンクされたサービス\)** では、既存の Azure BLOB ストレージのリンクされたサービスを選択するか、またはステージング用の新しいリンクされたサービスを作成します。

   1. **[ステージング パス]** では、選択した Azure BLOB ストレージ アカウント内の BLOB コンテナーを指定するか、またはこれを空のままにしてステージング用の既定のパスを使用します。

   1. **[続行]** をクリックします。

   ![セルフホステッド IR の詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS パッケージのプロキシでの接続を有効にする

Visual Studio 用の SSIS プロジェクト拡張機能 ([こちら](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)からダウンロード可能) と共に、またはスタンドアロン インストーラ ([こちら](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)からダウンロード可能) として最新の SSDT を使用することで、OLEDB/フラット ファイルの接続マネージャーに追加された新しい **ConnectByProxy** プロパティを検出できます。  

オンプレミスのデータベース/ファイルにアクセスするために OLEDB/フラット ファイルのソースを使用するデータ フロー タスクを含む新しいパッケージを設計する場合、関連する接続マネージャーの [プロパティ] パネルで **True** に設定することでこのプロパティを有効にできます。

![ConnectByProxy プロパティを有効にする](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

また、既存のパッケージの実行する場合も、それらを 1 つずつ手動で変更することなく、このプロパティを有効にできます。  2 つのオプションがあります。
- Azure SSIS-IR で実行する、最新の SSDT を備えたこれらのパッケージを含むプロジェクトを開いて、再構築し、再デプロイする:SSMS からパッケージを実行するときに [Execute Package]\(パッケージの実行\) ポップアップ ウィンドウの **[接続マネージャー]** タブに表示される、関連する接続マネージャーに対して **True** に設定することでプロパティを有効にできます。

  ![ConnectByProxy プロパティを有効にする 2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  ADF パイプラインでパッケージを実行するときに [SSIS パッケージの実行アクティビティ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)の **[接続マネージャー]** タブに表示される、関連する接続マネージャーに対して **True** に設定することでプロパティを有効にできます。
  
  ![ConnectByProxy プロパティを有効にする 3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- SSIS IR で実行するこれらのパッケージを含むプロジェクトを再デプロイする:プロパティ パス `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` を指定し、SSMS からパッケージを実行するときに [Execute Packages]\(パッケージの実行\) ポップアップ ウィンドウの **[詳細]** タブでプロパティ オーバーライドとして **True** に設定することでプロパティを有効にできます。

  ![ConnectByProxy プロパティを有効にする 4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  プロパティ パス `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]` を指定し、ADF パイプラインでパッケージを実行するときに [SSIS パッケージ実行アクティビティ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)の **[Property Overrides]\(プロパティ オーバーライド\)** タブでプロパティ オーバーライドとして **True** に設定することでプロパティを有効にできます。
  
  ![ConnectByProxy プロパティを有効にする 5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>1 番目と 2 番目のステージング タスクをデバッグする

セルフホステッド IR では、`C:\ProgramData\SSISTelemetry` フォルダーにランタイム ログ、`C:\ProgramData\SSISTelemetry\ExecutionLog` フォルダーに 1 番目のステージング タスクの実行ログがあります。  2 番目のステージング タスクの実行ログは、パッケージを格納した場所が SSISDB か、ファイル システム/ファイル共有/Azure Files かによって、それぞれ SSISDB 内、または指定されたロギング パスにあります。  1 番目のステージング タスクの一意の ID も、2 番目のステージング タスクの実行ログ内にあります。たとえば、次のようになります。 

![1 番目のステージング タスクの一意の ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>ステージング タスクでの Windows 認証の使用

セルフホステッド IR のステージング タスクで Windows 認証が必要な場合は、[同じ Windows 認証を使用するように SSIS パッケージを構成する](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15)必要があります。 ステージング タスクは、セルフホステッド IR サービス アカウント (既定では `NT SERVICE\DIAHostService`) を使用して起動され、データ ストアには Windows 認証アカウントを使用してアクセスされます。 どちらのアカウントにも、特定のセキュリティ ポリシーが割り当てられている必要があります。 そのため、セルフホステッド IR マシンで `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` を開き、次の手順を実行します。
- **[プロセスのメモリ クォータの増加]** ポリシーと **[プロセス レベル トークンの置き換え]** ポリシーをセルフホステッド IR サービス アカウントに割り当てます。 これは、既定のサービス アカウントを使用してセルフホステッド IR をインストールするときに自動的に実行されます。 別のサービス アカウントを使用する場合は、同じポリシーを割り当てます。
- **[サービスとしてログオン]** ポリシーを Windows 認証アカウントに割り当てます。

## <a name="billing-for-the-first-and-second-staging-tasks"></a>1 番目と 2 番目のステージング タスクの課金

セルフホステッド IR で実行されている 1 番目のステージング タスクは、[ADF データ パイプラインの価格](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)に関する記事に指定されているように、セルフホステッド IR で実行されるすべてのデータ移動アクティビティの課金と同様に、別個に課金されます。

Azure-SSIS IR で実行されている 2 番目のステージング タスクは別個に課金されませんが、実行中の Azure-SSIS IR は、[Azure-SSIS IR の価格](https://azure.microsoft.com/pricing/details/data-factory/ssis/)に関する記事の指定どおりに課金されます。

## <a name="current-limitations"></a>現在の制限

- ODBC、OLEDB、フラット ファイルの接続マネージャーと ODBC、OLEDB、フラット ファイルのソースを使用するデータ フロー タスクのみが現在サポートされています。 
- **アカウント キー**/**SAS URI**/**サービス プリンシパル**の認証を使って構成された Azure Blob Storage のリンクされたサービスのみが現在サポートされています。

## <a name="next-steps"></a>次のステップ

セルフホステッド IR を Azure-SSIS IR のプロキシとして構成したら、ADF パイプラインの SSIS パッケージ実行アクティビティとしてパッケージをデプロイおよび実行してオンプレミスのデータにアクセスできます。[ADF パイプラインの SSIS パッケージ実行アクティビティとしての SSIS パッケージの実行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。