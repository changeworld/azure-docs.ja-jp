---
title: Azure-SSIS 統合ランタイムのセットアップのカスタマイズ
description: この記事では、Azure SSIS 統合ランタイムのカスタム セットアップ インターフェイスを使用して、追加のコンポーネントをインストールしたり、設定を変更したりする方法について説明します
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 02/14/2020
ms.openlocfilehash: 9c084564fec3faf59317fe9e05f3e850a38454d6
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251976"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムのセットアップのカスタマイズ

Azure-SQL Server Integration Services 統合ランタイム (Azure-SSIS IR) のカスタム セットアップには、使用する Azure-SSIS IR のセットアップまたは再構成中に独自の手順を追加するためのインターフェイスが用意されています。 

カスタム セットアップを使用すると、既定の動作の構成または環境を変更して、たとえば、追加の Windows サービスを開始したり、ファイル共有のアクセス資格情報を永続化したりすることができます。 また、ご利用の Azure-SSIS IR の各ノード上にアセンブリ、ドライバー、拡張機能などの追加のコンポーネントをインストールすることもできます。

ご利用の Azure-SSIS IR では、次の 2 つの方法のいずれかでカスタム セットアップを行うことができます。 
* **スクリプトを使用しない高速カスタム セットアップ**: いくつかの一般的なシステム構成および Windows コマンドを実行したり、スクリプトを使用せずにいくつかの一般的なまたは推奨される追加コンポーネントをインストールしたりします。
* **スクリプトを使用した標準カスタム セットアップ**: スクリプトとその関連ファイルを準備し、それらをすべてまとめてご利用の Azure ストレージ アカウントの BLOB コンテナーにアップロードします。 その後、Azure SSIS IR を設定または再構成するときに、ご利用のコンテナーに対して Shared Access Signature (SAS) の Uniform Resource Identifier (URI) を指定します。 ご利用の Azure SSIS IR の各ノードでは、その後、スクリプトとその関連ファイルがご利用のコンテナーからダウンロードされ、昇格されたアクセス許可を使用してご自分のカスタム セットアップが実行されます。 そのカスタム セットアップが完了すると、各ノードによって、実行の標準出力およびその他のログがご利用にコンテナーにアップロードされます。

高速および標準のカスタム セットアップでは、無料のライセンスなしのコンポーネントおよび有料のライセンスありコンポーネントの両方をインストールできます。 お客様が独立ソフトウェア ベンダー (ISV) である場合は、[Azure SSIS IR の有料またはライセンスありのコンポーネントの開発](how-to-develop-azure-ssis-ir-licensed-components.md)に関するドキュメントを参照してください。

> [!IMPORTANT]
> Azure-SSIS IR の v2 シリーズ ノードはカスタム セットアップには適していないため、代わりに v3 シリーズ ノードを使用してください。 既に v2 シリーズのノードを使用している場合は、できるだけ早く v3 シリーズのノードに切り替えてください。

## <a name="current-limitations"></a>現在の制限

次の制限事項は、標準カスタム セットアップにのみ適用されます。

- ご自分のスクリプトで *gacutil.exe* を使用してアセンブリをグローバル アセンブリ キャッシュ (GAC) にインストールする場合は、ご自分のカスタム セットアップの一部として *gacutil.exe* を指定する必要があります。 また、「手順」のセクションで後述するように、"*パブリック プレビュー*" コンテナーに用意されているコピーを使用することもできます。

- ご自分のスクリプト内でサブフォルダーを参照する場合、*msiexec.exe* ではルート フォルダーを参照する `.\` 表記はサポートされていません。 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` ではなく、`msiexec /i "MySubfolder\MyInstallerx64.msi" ...` などのコマンドを使用します。

- Windows によって自動的に作成される管理用共有、または非表示のネットワーク共有は、現在、Azure-SSIS IR ではサポートされていません。

- Azure-SSIS IR では、IBM iSeries Access ODBC ドライバーはサポートされません。 カスタム セットアップ中にインストール エラーが表示される場合があります。 その場合は、IBM のサポートにお問い合わせください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

ご利用の Azure SSIS IR をカスタマイズするには、次のアイテムが必要です。

- [Azure サブスクリプション](https://azure.microsoft.com/)

- [お使いの Azure SSIS IR のプロビジョニング](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure ストレージ アカウント](https://azure.microsoft.com/services/storage/)。 高速カスタム セットアップには必要ありません。 標準カスタム セットアップの場合は、カスタム セットアップ スクリプトとその関連ファイルをアップロードして BLOB コンテナーに格納します。 また、カスタム セットアップ処理では、同じ BLOB コンテナーに実行ログもアップロードします。

## <a name="instructions"></a>Instructions

1. PowerShell を使用して Azure-SSIS IR を設定または再構成する場合は、[Azure PowerShell](/powershell/azure/install-az-ps) をダウンロードしてインストールします。 高速カスタム セットアップの場合は、手順 4 に進みます。

1. カスタム セットアップ スクリプトとその関連ファイル (たとえば、.bat、.cmd、.exe、.dll、.msi、または .ps1 ファイル) を準備します。

   * カスタム セットアップのエントリ ポイントである *main.cmd* という名前のスクリプト ファイルを用意しておく必要があります。  
   * スクリプトを確実にサイレント実行できるようにするには、まずご利用のローカル コンピューターでテストすることをお勧めします。  
   * 他のツール (*msiexec* など) によって生成された追加のログが、ご利用のコンテナーにアップロードされるようにする場合は、定義済みの環境変数 `CUSTOM_SETUP_SCRIPT_LOG_DIR` をご自分のスクリプトのログ フォルダーとして指定します (たとえば、*msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*)。

1. [Azure Storage Explorer](https://storageexplorer.com/) をダウンロードし、インストールして、開きます。 そのためには次を行います。

   a. **[(Local and Attached)]\((ローカルで接続済み)\)** で、 **[ストレージ アカウント]** を右クリックして、 **[Azure Storage へ接続]** を選択します。

      ![Azure Storage への接続](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用する\)** を選択して、 **[次へ]** を選択します。

      ![ストレージ アカウントの名前とキーを使用する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   c. ご利用の Azure ストレージ アカウント名とキーを入力し、 **[次へ]** を選択してから **[接続]** を選択します。

      ![ストレージ アカウントの名前とキーを指定する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 接続された Azure ストレージ アカウントで、 **[BLOB コンテナー]** を右クリックし、 **[BLOB コンテナーの作成]** を選択して、新しいコンテナーに名前を付けます。

      ![BLOB コンテナーを作成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 新しいコンテナーを選択し、ご自分のカスタム セットアップ スクリプトとその関連ファイルをアップロードします。 任意のフォルダーではなく、必ずご利用のコンテナーの最上位に *main.cmd* をアップロードしてください。 また、ご利用のコンテナーには必要なカスタム セットアップ ファイルのみを確実に含めます。これにより、ご利用の Azure-SSIS IR に後でダウンロードする場合に時間が短縮されます。 カスタム セットアップの最長期間は現在 45 分に設定されていて、これを過ぎるとタイムアウトになります。これには、ご利用のコンテナーからすべてのファイルをダウンロードし、それらを Azure-SSIS IR にインストールする時間が含まれます。 セットアップにより長い時間が必要な場合は、サポート チケットを生成してください。

      ![BLOB コンテナーにファイルをアップロードする](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. コンテナーを右クリックしてから、 **[Shared Access Signature の取得]** を選択します。

      ![コンテナーの Shared Access Signature を取得する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 十分に長い有効期間と [読み取り]、[書き込み]、または [リスト] のアクセス許可を使用して、ご利用のコンテナー用に SAS URI を作成します。 ご利用の Azure SSIS IR の任意のノードが再イメージ化または再開されるときは常に、ご自分のカスタム セットアップ スクリプトとその関連ファイルをダウンロードして実行するために SAS URI が必要になります。 セットアップ実行ログをアップロードするには、書き込みアクセス許可が必要です。

      > [!IMPORTANT]
      > Azure-SSIS IR の作成から削除まで (この期間に Azure-SSIS IR を定期的に停止および開始する場合は特に) そのライフサイクル全体を通して、SAS URI の有効期限が切れないこと、カスタム セットアップ リソースが常に利用可能であることを保証します。

      ![コンテナーの Shared Access Signature を生成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. コンテナーの SAS URI をコピーして保存します。

      ![Shared Access Signature をコピーして保存する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. データ ファクトリ UI を使用して Azure-SSIS IR を設定または再構成する場合、 **[Integration runtime setup]\(統合ランタイムの設定)** ウィンドウの **[詳細設定]** セクションにある **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]\(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ランタイムをカスタマイズする\)** チェックボックスをオンにすれば、カスタム セットアップを追加または削除することができます。 

   標準カスタム セットアップを追加する場合は、 **[Custom setup container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** ボックスに、ご利用のコンテナーの SAS URI を入力します。 
   
   高速カスタム セットアップを追加する場合は、 **[新規]** を選択して **[Add express custom setup]\(高速カスタム セットアップの追加\)** ウィンドウを開いてから、 **[Express custom setup type]\(高速カスタム セットアップの種類\)** ドロップダウン リストで種類を選択します。

   * **[Run cmdkey command]\(cmdkey コマンドの実行\)** という種類を選択した場合は、 **[/Add]** 、 **[/User]** 、 **[/Pass]** の各ボックスに、ターゲットのコンピューター名またはドメイン名、アカウント名またはユーザー名、アカウント キーまたはパスワードを入力することにより、ご利用のファイル共有または Azure Files 共有のアクセス資格情報を Azure-SSIS IR で保持できます。 これは、ローカル コンピューターで Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) コマンドを実行する場合と似ています。
   
   * **[環境変数の追加]** という種類を選択した場合は、Azure-SSIS IR で実行されるパッケージで使用する Windows 環境変数を追加できます。そのためには、 **[変数名]** および **[変数値]** ボックスに環境変数の名前および値を入力します。 これは、ローカル コンピューターで Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) コマンドを実行する場合と似ています。

   * **[Install licensed component]\(ライセンス付与されたコンポーネントのインストール\)** という種類を選択した場合は、 **[コンポーネント名]** ドロップダウン リストで、Microsoft の ISV パートナーからの統合コンポーネントを選択できます。

     * **[SentryOne's Task Factory]\(SentryOne の Task Factory\)** コンポーネントを選択した場合は、Azure-SSIS IR に SentryOne の [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) という一連のコンポーネントをインストールできます。そのためには、 **[ライセンス キー]** ボックスで購入した製品ライセンス キーを入力します。 現在の統合バージョンは **2019.4.3** です。

     * **[oh22's HEDDA.IO]\(oh22 の HEDDA.IO\)** コンポーネントを選択した場合は、サービスを購入した後、Azure-SSIS IR に oh22 の [HEDDA.IO](https://hedda.io/ssis-component/) データ品質またはクレンジング コンポーネントをインストールできます。 現在の統合バージョンは **1.0.13** です。

     * **[oh22's SQLPhonetics.NET]\(oh22 の SQLPhonetics.NET\)** コンポーネントを選択した場合は、Azure-SSIS IR に oh22 の [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) データ品質または照合コンポーネントをインストールできます。そのためには、 **[ライセンス キー]** ボックスで購入した製品ライセンス キーを入力します。 現在の統合バージョンは **1.0.43** です。

     * **[KingswaySoft's SSIS Integration Toolkit]** \(KingswaySoft の SSIS Integration Toolkit\) コンポーネントを選択した場合は、お使いの Azure-SSIS IR 上の KingswaySoft から、購入した製品ライセンス キーを **[ライセンス キー]** ボックスに入力して、Microsoft Dynamics、SharePoint、Project Server、Oracle、Salesforce Marketing Cloud などの CRM、ERP、マーケティング、コラボレーション アプリ用の [SSIS Integration Toolkit](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) という一連のコンポーネントをインストールできます。 現在の統合バージョンは **2019.2** です。

     * **[KingswaySoft's SSIS Productivity Pack]** \(KingswaySoft の SSIS Productivity Pack\) コンポーネントを選択した場合は、お使いの Azure-SSIS IR 上の KingswaySoft から、購入した製品ライセンス キーを **[ライセンス キー]** ボックスに入力して [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) という一連のコンポーネントをインストールできます。 現在の統合バージョンは **10.0** です。

   追加した高速カスタム セットアップは、 **[詳細設定]** セクションに表示されます。 これらを削除する場合は、対応するチェック ボックスをオンにしてから **[削除]** を選択します。

   ![カスタム セットアップでの詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. PowerShell を使用して Azure-SSIS IR を設定または再構成する場合は、Azure-SSIS IR を開始する前に `Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行して、カスタム セットアップを追加または削除できます。
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   標準カスタム セットアップが完了し、Azure SSIS IR が開始した後、ご利用のストレージ コンテナーの *main.cmd.log* フォルダー内で *main.cmd* の標準出力とその他の実行ログを見つけることができます。

1. 標準カスタム セットアップのサンプルをいくつか確認するには、Azure Storage Explorer を使用して、パブリック プレビュー コンテナーに接続します。

   a. **[(Local and Attached)]\((ローカルおよび接続済み)\)** で、 **[ストレージ アカウント]** を右クリックし、 **[Connect to Azure storage]\(Azure Storage へ接続\)** を選択し、 **[Use a connection string or a shared access signature URI]\(接続文字列または Shared Access Signature URI を使用する\)** を選択して、 **[次へ]** を選択します。

      ![Shared Access Signature を使って Azure Storage に接続する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. **[SAS URI を使用する]** を選択してから、 **[URI]** ボックスに次の SAS URI を入力します。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![コンテナーに対して Shared Access Signature を提供する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   c. **[次へ]** 、 **[接続]** の順に選択します。

   d. 左側のウィンドウで、接続されている **publicpreview** コンテナーを選択して、 *[CustomSetupScript]* フォルダーをダブルクリックします。 このフォルダーには、次のアイテムが含まれています。

      * *Sample* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に基本タスクをインストールするためのカスタム セットアップが格納されています。 タスクは何も行いませんが、数秒間スリープ状態になります。 このフォルダーには、*gacutil* フォルダーも含まれていて、その内容全体 (*gacutil.exe*、*gacutil.exe.config*、および *1033\gacutlrc.dll*) を、そのままご利用のコンテナーにコピーできます。

      * *UserScenarios* フォルダー。これには、実際のユーザー シナリオからのいくつかのカスタム セットアップ サンプルが格納されています。

        ![パブリック プレビュー コンテナーの内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. *[UserScenarios]* フォルダーをダブルクリックして、次の項目を検索します。

      * *.NET FRAMEWORK 3.5* フォルダー。これには、ご利用の Azure-SSIS IR の各ノード上のカスタム コンポーネントに必要となる可能性がある、.NET Framework の以前のバージョンをインストールするためのカスタム セットアップが格納されています。

      * *BCP* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に、一括コピー プログラム (*bcp*) を含む SQL Server コマンドライン ユーティリティ (*MsSqlCmdLnUtils.msi*) をインストールするためのカスタム セットアップが格納されています。

      * *EXCEL* フォルダー。これには、ご利用の Azure-SSIS IR の各ノード上で Excel ファイルを動的に読み書きするためにスクリプト タスクで使用できる、C# アセンブリおよびライブラリをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 
      
        まず、[*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) と [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/) をダウンロードし、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。 または、標準の Excel 接続マネージャー、Excel ソース、および Excel 変換先のみを使用する場合、ご利用の Azure-SSIS IR には必要なアクセス再頒布可能パッケージが既にプレインストールされているため、カスタム セットアップは必要ありません。
      
      * *MYSQL ODBC* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に MySQL ODBC ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、ODBC 接続マネージャー、ソース、および変換先を使用して、MySQL サーバーに接続できます。 
     
        まず、[最新の 64 ビットおよび 32 ビット バージョンの MySQL ODBC ドライバー インストーラーをダウンロード](https://dev.mysql.com/downloads/connector/odbc/)し (たとえば、*mysql-connector-odbc-8.0.13-winx64.msi* や *mysql-connector-odbc-8.0.13-win32.msi*)、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。

      * *ORACLE ENTERPRISE* フォルダー。これには、ご利用の Azure SSIS IR Enterprise Edition の各ノードに Oracle コネクタおよび OCI ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) とサイレント インストール構成ファイル (*client.rsp*) が格納されています。 このセットアップでは、Oracle 接続マネージャー、ソース、変換先を使用して、Oracle サーバーに接続できます。 
      
        まず、Microsoft Connectors v5.0 for Oracle (*AttunitySSISOraAdaptersSetup.msi* および *AttunitySSISOraAdaptersSetup64.msi*) を [Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=55179)から、そして最新の Oracle クライアント (たとえば、*winx64_12102_client.zip*) を [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) からダウンロードし、次にそれらをすべて *main.cmd* および *client.rsp* と共にご利用のコンテナーにアップロードします。 TNS を使用して Oracle に接続する場合は、*tnsnames.ora* をダウンロードし、編集し、ご利用のコンテナーにアップロードすることも必要です。そのため、セットアップ時にこれを Oracle インストール フォルダーにコピーできます。

      * *ORACLE STANDARD ADO.NET* フォルダー。これには、ご利用の Azure-SSIS IR の各ノード上に Oracle ODP.NET ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、ADO.NET 接続マネージャー、ソース、および変換先を使用して、Oracle サーバーに接続できます。 
      
        まず、[最新の Oracle ODP.NET ドライバーをダウンロード](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)し (たとえば、*ODP.NET_Managed_ODAC122cR1.zip*)、次にそれを *main.cmd* と共にご利用のコンテナーにアップロードします。
       
      * *ORACLE STANDARD ODBC* フォルダー。これには、ご利用の Azure-SSIS IR の各ノード上に Oracle ODBC ドライバーをインストールし、データ ソース名 (DSN) を構成するためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、ODBC 接続マネージャー、ソース、および変換先を使用するか、または Power Query 接続マネージャーおよびソース (データ ソースの種類が ODBC である) を使用して Oracle サーバーに接続できます。 
      
        まず、最新の Oracle Instant Client (Basic Package または Basic Lite Package) と ODBC Package をダウンロードし、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。
        * [64 ビット パッケージをダウンロードします](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (Basic Package: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*。Basic Lite Package: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*。ODBC Package: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32 ビット パッケージをダウンロードします](https://www.oracle.com/technetwork/topics/winsoft-085727.html) (Basic Package: *instantclient-basic-nt-18.3.0.0.0dbru.zip*。Basic Lite Package: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*。ODBC Package: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * *ORACLE STANDARD OLEDB* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に Oracle OLEDB ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、OLEDB 接続マネージャー、ソース、および変換先を使用して、Oracle サーバーに接続できます。 
     
        まず、[最新の Oracle OLEDB ドライバーをダウンロード](https://www.oracle.com/partners/campaign/index-090165.html)し (たとえば、*ODAC122010Xcopy_x64.zip*)、次にそれを *main.cmd* と共にご利用のコンテナーにアップロードします。

      * *POSTGRESQL ODBC* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に PostgreSQL ODBC ドライバーをインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、ODBC 接続マネージャー、ソース、および変換先を使用して、PostgreSQL サーバーに接続できます。 
     
        まず、[最新の 64 ビットおよび 32 ビット バージョンの PostgreSQL ODBC ドライバー インストーラーをダウンロード](https://www.postgresql.org/ftp/odbc/versions/msi/)し (たとえば、*psqlodbc_x64.msi* や *psqlodbc_x86.msi*)、次にそれらをすべて *main.cmd* と共にご利用のコンテナーにアップロードします。

      * *SAP BW* フォルダー。これには、ご利用の Azure SSIS IR Enterprise Edition の各ノード上に SAP .NET コネクター アセンブリ (*librfc32.dll*) をインストールするためのカスタム セットアップ スクリプト (*main.cmd*) が格納されています。 このセットアップでは、SAP Business Warehouse (BW) 接続マネージャー、ソース、および変換先を使用して、SAP BW サーバーに接続できます。 
      
        まず、SAP インストール フォルダーからの 64 ビットまたは 32 ビット バージョンの *librfc32.dll* を、*main.cmd* と共にご利用のコンテナーにアップロードします。 次に、セットアップ中にスクリプトによって SAP アセンブリが *%windir%\SysWow64* または *%windir%\System32* フォルダーにコピーされます。

      * *STORAGE* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に Azure PowerShell をインストールするためのカスタム セットアップが格納されています。 このセットアップを使用すると、[ご利用の Azure ストレージ アカウントを操作するための PowerShell スクリプト](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)を実行する SSIS パッケージをデプロイして実行できるようになります。 
      
        *main.cmd*、サンプルの *AzurePowerShell.msi* (または最新バージョンを使用)、および *storage.ps1* をご利用のコンテナーにコピーします。 パッケージのテンプレートとして *PowerShell.dtsx* を使用します。 パッケージ テンプレートでは、*storage.ps1* を修正可能な PowerShell スクリプトとしてダウンロードする [Azure BLOB のダウンロード タスク](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)と、各ノード上でスクリプトを実行する[プロセス実行タスク](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)が組み合わされています。

      * *TERADATA* フォルダー。これには、カスタム セットアップ スクリプト (*main.cmd*)、その関連ファイル (*install.cmd*)、およびインストーラー パッケージ ( *.msi*) が格納されています。 これらのファイルによって、ご利用の Azure SSIS IR Enterprise Edition の各ノード上に Teradata コネクター、Teradata Parallel Transporter (TPT) API、および ODBC ドライバーがインストールされます。 このセットアップでは、Teradata 接続マネージャー、ソース、および変換先を使用して、Teradata サーバーに接続できます。 
      
        まず、[Teradata Tools and Utilities 15.x の zip ファイルをダウンロード](http://partnerintelligence.teradata.com)し (たとえば、*TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*)、次にそれを、前に説明した *.cmd* および *.msi* ファイルと共にご利用のコンテナーにアップロードします。

      * *ZULU OPENJDK* フォルダー。これには、ご利用の Azure SSIS IR の各ノード上に Zulu OpenJDK をインストールするためのカスタム セットアップ スクリプト (*main.cmd*) と PowerShell ファイル (*install_openjdk.ps1*) が格納されています。 このセットアップでは、Azure Data Lake Store または柔軟なファイル コネクタを使用して、ORC および Parquet ファイルを処理できます。 詳細については、[Integration Services 用の Azure Feature Pack](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java) に関するページを参照してください。 
      
        まず、[最新の Zulu OpenJDK をダウンロード](https://www.azul.com/downloads/zulu/zulu-windows/)し (たとえば、*zulu8.33.0.1-jdk8.0.192-win_x64.zip*)、次にそれを、*main.cmd* および *install_openjdk.ps1* と共にご利用のコンテナーにアップロードします。

        ![ユーザー シナリオ フォルダー内のフォルダー](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. これらのカスタム セットアップのサンプルを試行するには、選択したフォルダーからご利用のコンテナーへ内容をコピーします。
   
      データ ファクトリ UI を使用して Azure-SSIS IR を設定または再構成する場合は、 **[詳細設定]** セクションの **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]\(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ラインタイムをカスタマイズする\)** チェックボックスをオンにして、 **[Custom setup container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** ボックスにコンテナーの SAS URI を入力します。
   
      PowerShell を使用して Azure-SSIS IR を設定または再構成する場合は、ご利用のコンテナーの SAS URI を `SetupScriptContainerSasUri` パラメーターの値として設定して、`Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行します。

## <a name="next-steps"></a>次のステップ

- [Azure-SSIS Integration Runtime の Enterprise Edition を設定する](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Azure-SSIS Integration Runtime 用の有料またはライセンスありのカスタム コンポーネントを開発する](how-to-develop-azure-ssis-ir-licensed-components.md)
