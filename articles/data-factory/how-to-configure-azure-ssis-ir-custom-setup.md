---
title: Azure-SSIS 統合ランタイムの設定のカスタマイズ
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
ms.date: 12/23/2019
ms.openlocfilehash: ccf7ba2fd27dabdb090be87c5438ad68471996da
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/26/2019
ms.locfileid: "75497062"
---
# <a name="customize-setup-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムの設定のカスタマイズ

Azure SSIS 統合ランタイムのカスタム セットアップ インターフェイスは、お使いの Azure SSIS IR のプロビジョニングや再構成の間に、独自のセットアップ手順を追加するためのインターフェイスを提供します。 

カスタム セットアップでは、既定の動作の構成または環境を変更したり (追加の Windows サービスを開始する、ファイル共有のアクセス資格情報を永続化するなど)、Azure SSIS IR の各ノードに追加のコンポーネントをインストールしたり (アセンブリ、ドライバー、または拡張機能など) することが可能です。

Azure-SSIS IR でカスタム セットアップを実行する場合は、スクリプトを使用しない高速カスタム セットアップと、スクリプトを使用する標準カスタム セットアップという 2 つの方法があります。

高速カスタム セットアップでは、いくつかの一般的なシステム構成または Windows コマンドを実行したり、スクリプトを使用せずにいくつかの一般的なまたは推奨される追加コンポーネントをインストールしたりすることができます。  

標準カスタム セットアップでは、スクリプトとその関連ファイルを準備し、それらをすべてまとめて Azure ストレージ アカウントの BLOB コンテナーにアップロードする必要があります。 その後、Azure SSIS IR をプロビジョニングまたは再構成するときに、コンテナーに対して Shared Access Signature (SAS) の Uniform Resource Identifier (URI) を指定します。 Azure SSIS IR の各ノードでは、その後、スクリプトとその関連ファイルをコンテナーからダウンロードし、昇格された特権を使用してカスタム セットアップを実行します。 カスタム セットアップが完了すると、各ノードでは、実行の標準出力およびその他のログをコンテナーにアップロードします。

高速または標準カスタム セットアップでは、無料またはライセンスなしおよび有料またはライセンスありのコンポーネントの両方をインストールできます。 ISV の場合は、[Azure SSIS IR の有料またはライセンスありのコンポーネントを開発する方法](how-to-develop-azure-ssis-ir-licensed-components.md)に関するドキュメントを参照してください。

> [!IMPORTANT]
> Azure-SSIS IR の v2 シリーズ ノードはカスタムセットアップには適していないため、代わりに v3 シリーズ ノードを使用してください。  既に v2 シリーズ ノードを使用している場合は、できるだけ早く v3 シリーズ ノードを使用するように切り替えてください。

## <a name="current-limitations"></a>現在の制限

標準カスタム セットアップにのみ適用される制限事項は次のとおりです。

- スクリプトで `gacutil.exe` を使用して、アセンブリをグローバル アセンブリ キャッシュ (GAC) にインストールする場合は、カスタム セットアップの一部として `gacutil.exe` を指定するか、以下のパブリック プレビュー コンテナー内に提供されているコピーを使用する必要があります。

- スクリプト内でサブフォルダーを参照する場合、`msiexec.exe` はルート フォルダーを参照するための `.\` 表記をサポートしていません。 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` の代わりに `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` のようなコマンドを使います。

- Windows によって自動的に作成された管理用共有、つまり、非表示のネットワーク共有は、現在、Azure-SSIS IR ではサポートされていません。

- Azure-SSIS IR では、IBM iSeries Access ODBC ドライバーはサポートされません。 カスタム セットアップ中にインストール エラーが表示される場合があります。 サポートについては、IBM のサポートにお問い合わせください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure SSIS IR をカスタマイズするには、以下のものが必要です。

- [Azure サブスクリプション](https://azure.microsoft.com/)

- [お使いの Azure SSIS IR のプロビジョニング](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure Storage アカウント](https://azure.microsoft.com/services/storage/)。 高速カスタム セットアップには必要ありません。 標準カスタム セットアップの場合は、カスタム セットアップ スクリプトとその関連ファイルをアップロードして BLOB コンテナーに格納します。 また、カスタム セットアップ処理では、同じ BLOB コンテナーに実行ログもアップロードします。

## <a name="instructions"></a>Instructions

1. PowerShell を使用して Azure-SSIS IR をプロビジョニングまたは再構成する場合は、[Azure PowerShell](/powershell/azure/install-az-ps) をダウンロードしてインストールします。 高速カスタム セットアップの場合は、手順 4 に進みます。

1. カスタム セットアップ スクリプトとその関連ファイル (たとえば、.bat、.cmd、.exe、.dll、.msi、または .ps1 ファイル) を準備します。

   1. カスタム セットアップのエントリ ポイントである `main.cmd` という名前のスクリプト ファイルを保持している必要があります。

   1. スクリプトを自動的に実行できることを確認する必要があります。まずは、ローカル コンピューター上でスクリプトをテストすることをお勧めします。

   1. 他のツール (`msiexec.exe` など) で生成された追加ログをコンテナーにアップロードする場合は、事前定義された環境変数 `CUSTOM_SETUP_SCRIPT_LOG_DIR` をお使いのスクリプトのログ フォルダー として指定します (たとえば、`msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log`)。

1. [Azure Storage Explorer](https://storageexplorer.com/)をダウンロードし、インストールして、起動します。

   1. **[(Local and Attached)]\((ローカルおよび接続済み)\)** で、 **[ストレージ アカウント]** を右クリックし、 **[Connect to Azure storage]\(Azure Storage へ接続\)** を選択します。

      ![Azure Storage へ接続](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   1. **[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択して、 **[次へ]** を選択します。

      ![ストレージ アカウントの名前とキーを使用する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   1. お使いの Azure Storage アカウント名とキーを入力し、 **[次へ]** を選択してから **[接続]** を選択します。

      ![ストレージ アカウントの名前とキーを提供する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   1. 接続された Azure Storage アカウントで、 **[BLOB コンテナー]** を右クリックし、 **[BLOB コンテナーの作成]** を選択して、新しいコンテナーに名前を付けます。

      ![BLOB コンテナーを作成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   1. 新しいコンテナーを選択し、カスタム セットアップ スクリプトとその関連ファイルをアップロードします。 任意のフォルダーではなく、必ずコンテナーの最上位に `main.cmd` をアップロードしてください。 後で Azure-SSIS IR にダウンロードする時間を短縮できるように、コンテナーには必要なカスタム セットアップ ファイルのみが含まれていることを確認してください。 カスタム セットアップの最大期間は現在 45 分でタイムアウトとなります。これには、コンテナーからすべてのファイルをダウンロードし、Azure SSIS IR のインストールにかかる時間が含まれます。 長い期間が必要な場合は、サポート チケットを発行してください。

      ![BLOB コンテナーにファイルをアップロードする](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   1. コンテナーを右クリックして、 **[Get Shared Access Signature]\(Shared Access Signature の取得\)** を選択します。

      ![コンテナーの Shared Access Signature を取得する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   1. 十分に長い有効期間と [読み取り]、[書き込み]、および [リスト] のアクセス許可を備えたコンテナーの SAS URI を作成します。 お使いの Azure SSIS IR の任意のノードが再イメージ化/再開されるときは常に、カスタム セットアップ スクリプトとその関連ファイルをダウンロードして実行するために SAS URI が必要になります。 セットアップ実行ログをアップロードするには、書き込みアクセス許可が必要です。

      > [!IMPORTANT]
      > Azure-SSIS IR の作成から削除まで (この期間に Azure-SSIS IR を定期的に停止および開始する場合は時に) そのライフサイクル全体を通して、SAS URI の有効期限が切れないようにし、カスタム セットアップ リソースが常に利用可能な状態になるようにしてください。

      ![コンテナーの Shared Access Signature を生成する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   1. コンテナーの SAS URI をコピーして保存します。

      ![Shared Access Signature をコピーして保存する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. データ ファクトリ UI を使用して Azure-SSIS IR をプロビジョニングまたは再構成する場合は、統合ランタイム セットアップ パネルの **[詳細設定]** セクションにある **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations\]\(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ランタイムをカスタマイズする\)** チェックボックスをオンにして、カスタム セットアップを追加または削除できます。 

   標準カスタム セットアップを追加する場合は、 **[Custom setup container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** フィールドにコンテナーの SAS URI を入力します。 
   
   高速カスタム セットアップを追加する場合は、 **[新規]** を選択して **[Add express custom setup]\(高速カスタム セットアップの追加\)** パネルを開いてから、 **[Express custom setup type]\(高速カスタム セットアップの種類\)** ドロップダウン メニューでいずれかの種類を選びます。

   1. **[Run cmdkey command]\(cmdkey コマンドの実行\)** という種類を選択した場合は、 **[/Add]** 、 **[/User]** 、および **[/Pass]** の各フィールドに、ターゲットのコンピューターまたはドメイン名、アカウント名またはユーザー名、およびアカウント キーまたはパスワードを入力することにより、ファイル共有または Azure Files のアクセス資格情報を Azure-SSIS IR で保持できます。 これは、ローカル コンピューターで Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) コマンドを実行する場合と似ています。
   
   1. **[環境変数の追加]** という種類を選択した場合は、Azure-SSIS IR で実行されるパッケージで使用する Windows 環境変数を追加できます。そのためには、 **[変数名]** および **[変数値]** フィールドにそれぞれ環境変数の名前および値を入力します。 これは、ローカル コンピューターで Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) コマンドを実行する場合と似ています。

   1. **[Install licensed component]\(ライセンス付与されたコンポーネントのインストール\)** という種類を選択した場合は、 **[コンポーネント名]** ドロップダウン メニューで、ISV パートナーの任意の統合コンポーネントを選択できます。

      1. **[SentryOne's Task Factory]\(SentryOne の Task Factory\)** コンポーネントを選択した場合は、Azure-SSIS IR に SentryOne の [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) という一連のコンポーネントをインストールできます。そのためには、 **[ライセンス キー]** フィールドで購入した製品ライセンス キーを入力します。 現在の統合バージョンは **2019.4.3** です。

      1. **[oh22's HEDDA.IO]\(oh22 の HEDDA.IO\)** コンポーネントを選択した場合は、サービスを購入した後、Azure-SSIS IR に oh22 の [HEDDA.IO](https://hedda.io/ssis-component/) データ品質またはクレンジング コンポーネントをインストールできます。 現在の統合バージョンは **1.0.13** です。

      1. **[oh22's SQLPhonetics.NET]\(oh22 の SQLPhonetics.NET\)** コンポーネントを選択した場合は、Azure-SSIS IR に oh22 の [SQLPhonetics.NET](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) データ品質または照合コンポーネントをインストールできます。そのためには、 **[ライセンス キー]** フィールドで購入した製品ライセンス キーを入力します。 現在の統合バージョンは **1.0.43** です。
   
   追加された高速カスタム セットアップは、 **[詳細設定]** セクションに表示されます。 これらを削除する場合は、対応するチェック ボックスをオンにしてから **[削除]** を選択できます。

   ![カスタム セットアップでの詳細設定](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

   PowerShell を使用して Azure-SSIS IR をプロビジョニングまたは再構成する場合は、Azure-SSIS IR を開始する前に `Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行して、カスタム セットアップを追加または削除できます。
   
   標準カスタム セットアップでは、コンテナーの SAS URI を `SetupScriptContainerSasUri` パラメーターの値として指定できます。 次に例を示します。

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                         -Name $MyAzureSsisIrName `
                                         -ResourceGroupName $MyResourceGroupName `
                                         -SetupScriptContainerSasUri $MySetupScriptContainerSasUri

   Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $MyDataFactoryName `
                                           -Name $MyAzureSsisIrName `
                                           -ResourceGroupName $MyResourceGroupName
   ```
   
   標準カスタム セットアップが完了し、Azure SSIS IR が開始した後、ストレージ コンテナーの `main.cmd.log` フォルダー内で `main.cmd` の標準出力とその他の実行ログを見つけることができます。

1. 標準カスタム セットアップのサンプルをいくつか確認するには、Azure Storage Explorer を使用してパブリック プレビュー コンテナーに接続します。

   1. **[(Local and Attached)]\((ローカルおよび接続済み)\)** で、 **[ストレージ アカウント]** を右クリックし、 **[Connect to Azure storage]\(Azure Storage へ接続\)** を選択し、 **[Use a connection string or a shared access signature URI]\(接続文字列または Shared Access Signature URI を使用する\)** を選択して、 **[次へ]** を選択します。

      ![Shared Access Signature を使って Azure Storage に接続する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   1. **[Use a SAS URI]\(SAS URI を使用する\)** を選択し、パブリック プレビュー コンテナーに対して以下の SAS URI を入力します。 **[次へ]** 、 **[接続]** の順に選択します。

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2018-04-08T14%3A10%3A00Z&se=2020-04-10T14%3A10%3A00Z&sv=2017-04-17&sig=mFxBSnaYoIlMmWfxu9iMlgKIvydn85moOnOch6%2F%2BheE%3D&sr=c`

      ![コンテナーに対して Shared Access Signature を提供する](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   1. 接続されたパブリック プレビュー コンテナーを選択して、`CustomSetupScript` フォルダーをダブルクリックします。 このフォルダーには、次のアイテムが含まれています。

      1. `Sample` フォルダー。Azure SSIS IR の各ノード上に基本タスクをインストールするための 1 個のカスタム セットアップが格納されています。 タスクは何も行いませんが、数秒間スリープ状態になります。 フォルダーには、`gacutil` フォルダーも含まれています。このフォルダーのコンテンツ全体 (`gacutil.exe`、`gacutil.exe.config`、`1033\gacutlrc.dll`) をそのままご利用のコンテナーにコピーすることができます。

      1. `UserScenarios` フォルダー。実際のユーザー シナリオからの複数のカスタム セットアップ サンプルが含まれています。

      ![パブリック プレビュー コンテナーの内容](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   1. `UserScenarios` フォルダーをダブルクリックして、次の項目を見つけます。

      1. `.NET FRAMEWORK 3.5` フォルダー。Azure-SSIS IR の各ノードのカスタム コンポーネントに必要となる可能性がある、.NET Framework の以前のバージョンをインストールするためのカスタム設定が格納されています。

      1. `BCP` フォルダー。Azure SSIS IR の各ノード上に、一括コピー プログラム (`bcp`) を含む SQL Server コマンドライン ユーティリティ (`MsSqlCmdLnUtils.msi`) をインストールするための 1 個のカスタム セットアップが格納されています。

      1. `EXCEL` フォルダー。Azure-SSIS IR の各ノードで Excel ファイルを動的に読み書きするためにスクリプト タスクで使用できる、C# アセンブリまたはライブラリをインストールするためのカスタム セットアップ スクリプト (`main.cmd`) が含まれています。 まず、`ExcelDataReader.dll` を[こちら](https://www.nuget.org/packages/ExcelDataReader/)から、`DocumentFormat.OpenXml.dll` を[こちら](https://www.nuget.org/packages/DocumentFormat.OpenXml/)からダウンロードし、`main.cmd` と共にそれらをすべてまとめてコンテナーにアップロードします。 または、Excel Connection Manager/Source/Destination のみを使用する場合は、必要な Access の再頒布可能パッケージが既に Azure-SSIS IR にプレインストールされているため、カスタム セットアップは必要ありません。
      
      1. `MYSQL ODBC` フォルダー。Azure SSIS IR の各ノード上に MySQL ODBC ドライバーをインストールするためのカスタム セットアップ スクリプト (`main.cmd`) が含まれています。 このセットアップでは、ODBC Connection Manager/Source/Destination を使用して、MySQL サーバーに接続できます。 まず、[MySQL](https://dev.mysql.com/downloads/connector/odbc/) から最新の 64 ビットおよび 32 ビット バージョンの MySQL ODBC ドライバー インストーラー (`mysql-connector-odbc-8.0.13-winx64.msi` および `mysql-connector-odbc-8.0.13-win32.msi` など) をダウンロードし、`main.cmd` と共にそれらをすべてまとめてコンテナーにアップロードします。

      1. `ORACLE ENTERPRISE` フォルダー。Azure SSIS IR Enterprise Edition の各ノードに Oracle コネクタおよび OCI ドライバーをインストールするための 1 個のカスタム セットアップ スクリプト (`main.cmd`) とサイレント インストール構成ファイル (`client.rsp`) が格納されています。 このセットアップでは、Oracle Connection Manager/Source/Destination を使用して、Oracle サーバーに接続できます。 まず、Oracle 用 Microsoft Connectors v5.0 (`AttunitySSISOraAdaptersSetup.msi` と `AttunitySSISOraAdaptersSetup64.msi`) を[Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=55179)から、および最新の Oracle クライアント (`winx64_12102_client.zip` など) を [Oracle](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html) からダウンロードし、次に `main.cmd` と `client.rsp` と共にそれらをすべてまとめてコンテナーにアップロードします。 TNS を使用して Oracle に接続する場合、`tnsnames.ora` をダウンロードし、編集し、コンテナーにアップロードする必要があります。そのため、セットアップ時にこのファイルを Oracle インストール フォルダーにコピーできます。

      1. `ORACLE STANDARD ADO.NET` フォルダー。Azure SSIS IR の各ノード上に Oracle ODP.NET ドライバーをインストールするための 1 個のカスタム セットアップ スクリプト (`main.cmd`) が格納されています。 このセットアップでは、ADO.NET Connection Manager/Source/Destination を使用して、Oracle サーバーに接続できます。 まず、[Oracle](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) から最新の Oracle ODP.NET ドライバー (`ODP.NET_Managed_ODAC122cR1.zip` など) をダウンロードし、`main.cmd` と共にそれをコンテナーにアップロードします。
       
      1. `ORACLE STANDARD ODBC` フォルダー。Azure-SSIS IR の各ノード上に Oracle ODBC ドライバーをインストールし、DSN を構成するためのカスタム セットアップ スクリプト (`main.cmd`) が格納されています。 このセットアップでは、ODBC Connection Manager/Source/Destination または Power Query Connection Manager/Source と ODBC データ ソースの種類を使用して Oracle サーバーに接続できます。 まず、最新の Oracle Instant Client (Basic Package または Basic Lite Package) と ODBC Package をダウンロードします。たとえば、64 ビット パッケージを[こちら](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)から (Basic Package: `instantclient-basic-windows.x64-18.3.0.0.0dbru.zip`、Basic Lite Package: `instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip`、ODBC Package: `instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip`)、または 32 ビット パッケージを[こちら](https://www.oracle.com/technetwork/topics/winsoft-085727.html)から (Basic Package: `instantclient-basic-nt-18.3.0.0.0dbru.zip`、Basic Lite Package: `instantclient-basiclite-nt-18.3.0.0.0dbru.zip`、ODBC Package: `instantclient-odbc-nt-18.3.0.0.0dbru.zip`) ダウンロードした後、`main.cmd` と共にそれらをすべてまとめてコンテナーにアップロードします。

      1. `ORACLE STANDARD OLEDB` フォルダー。Azure SSIS IR の各ノード上に Oracle OLEDB ドライバーをインストールするためのカスタム セットアップ スクリプト (`main.cmd`) が含まれています。 このセットアップでは、OLEDB Connection Manager/Source/Destination を使用して、Oracle サーバーに接続できます。 まず、[Oracle](https://www.oracle.com/partners/campaign/index-090165.html) から最新の Oracle OLEDB ドライバー (`ODAC122010Xcopy_x64.zip` など) をダウンロードし、`main.cmd` と共にそれをコンテナーにアップロードします。

      1. `POSTGRESQL ODBC` フォルダー。Azure SSIS IR の各ノード上に PostgreSQL ODBC ドライバーをインストールするためのカスタム セットアップ スクリプト (`main.cmd`) が含まれています。 このセットアップでは、ODBC Connection Manager/Source/Destination を使用して、PostgreSQL サーバーに接続できます。 まず、[PostgreSQL](https://www.postgresql.org/ftp/odbc/versions/msi/) から最新の 64 ビットおよび 32 ビット バージョンの PostgreSQL ODBC ドライバー インストーラー (`psqlodbc_x64.msi` および `psqlodbc_x86.msi` など) をダウンロードし、`main.cmd` と共にそれらをすべてまとめてコンテナーにアップロードします。

      1. `SAP BW` フォルダー。Azure SSIS IR Enterprise Edition の各ノードに SAP .NET コネクター アセンブリ (`librfc32.dll`) をインストールするためのカスタム セットアップ スクリプト (`main.cmd`) が含まれています。 このセットアップでは、SAP BW Connection Manager/Source/Destination を使用して、SAP BW サーバーに接続できます。 まず、SAP インストール フォルダーから 64 ビットまたは 32 ビット バージョンの `librfc32.dll` を、`main.cmd` と共にコンテナーにアップロードします。 その後、セットアップ中に、スクリプトによって SAP アセンブリが `%windir%\SysWow64` または `%windir%\System32` フォルダーにコピーされます。

      1. `STORAGE` フォルダー。Azure SSIS IR の各ノード上に Azure PowerShell をインストールするための 1 個のカスタム セットアップが格納されています。 このセットアップによって、[Azure Storage アカウントを操作するための PowerShell スクリプト](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)を実行する SSIS パッケージをデプロイして実行できるようになります。 `main.cmd`、サンプルの `AzurePowerShell.msi` (または最新バージョンを使用)、および `storage.ps1` をコンテナーにコピーします。 パッケージのテンプレートとして PowerShell.dtsx を使用します。 パッケージ テンプレートでは、`storage.ps1` を修正可能な PowerShell スクリプトとしてダウンロードする [Azure BLOB のダウンロード タスク](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)と、各ノード上でスクリプトを実行する[プロセス実行タスク](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)を結合しています。

      1. `TERADATA` フォルダー。カスタム セットアップ スクリプト (`main.cmd`)、その関連ファイル (`install.cmd`)、およびインストーラー パッケージ (`.msi`) が格納されています。 これらのファイルでは、Azure SSIS IR Enterprise Edition の各ノード上に Teradata コネクター、TPT API、ODBC ドライバーをインストールします。 このセットアップでは、Teradata Connection Manager/Source/Destination を使用して、Teradata サーバーに接続できます。 まず、Teradata Tools and Utilities 15.x の zip ファイル (`TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip` など) を [Teradata](http://partnerintelligence.teradata.com) からダウンロードし、それを上述の `.cmd` および `.msi` ファイルと共にコンテナーにアップロードします。

      1. `ZULU OPENJDK` フォルダー。Azure SSIS IR の各ノード上に Zulu OpenJDK をインストールするためのカスタム セットアップ スクリプト (`main.cmd`) と PowerShell ファイル (`install_openjdk.ps1`) が含まれています。 このセットアップでは、Azure Data Lake Store または柔軟なファイル コネクタを使用して、ORC または Parquet ファイルを処理できます。詳細については、[こちら](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)を参照してください。 まず、[こちら](https://www.azul.com/downloads/zulu/zulu-windows/)から最新の Zulu OpenJDK (`zulu8.33.0.1-jdk8.0.192-win_x64.zip` など) をダウンロードし、`main.cmd` と `install_openjdk.ps1` と共にそれをコンテナーにアップロードします。

      ![ユーザー シナリオ フォルダー内のフォルダー](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   1. これらのカスタム セットアップのサンプルを試すには、選択されたフォルダーからコンテナーにコンテンツをコピーして貼り付けます。
   
      データ ファクトリ UI を使用して Azure-SSIS IR をプロビジョニングまたは再構成する場合は、 **[詳細設定]** セクションの **[Customize your Azure-SSIS Integration Runtime with additional system configurations/component installations]\(追加のシステム構成およびコンポーネントのインストールで Azure-SSIS 統合ラインタイムをカスタマイズする\)** チェックボックスをオンにして、 **[Custom setup container SAS URI]\(カスタム セットアップ コンテナー SAS URI\)** フィールドにコンテナーの SAS URI を入力します。
   
      PowerShell を使用して Azure-SSIS IR をプロビジョニングまたは再構成する場合は、コンテナーの SAS URI を `SetupScriptContainerSasUri` パラメーターの値として設定して、`Set-AzDataFactoryV2IntegrationRuntime` コマンドレットを実行します。

## <a name="next-steps"></a>次のステップ

-   [Azure SSIS 統合ランタイムの Enterprise Edition](how-to-configure-azure-ssis-ir-enterprise-edition.md)

-   [Azure SSIS 統合ランタイムの有料 (ライセンスあり) カスタム コンポーネントを開発する方法](how-to-develop-azure-ssis-ir-licensed-components.md)
