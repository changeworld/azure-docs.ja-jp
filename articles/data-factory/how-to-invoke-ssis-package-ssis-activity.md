---
title: ポータルで SSIS パッケージの実行アクティビティを使用する
description: この記事では、SSIS パッケージの実行アクティビティと Azure Data Factory Studio ポータルを使用することで、Azure Data Factory パイプラインで SQL Server Integration Services (SSIS) パッケージを実行する方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/22/2021
ms.openlocfilehash: ef334cf16502e6c2bd39193dbd377758a90ddb24
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131842437"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-the-azure-data-factory-studio-portal"></a>Azure Data Factory Studio ポータルで SSIS パッケージの実行アクティビティを使用し、SSIS パッケージを実行する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、SSIS パッケージの実行アクティビティと Azure Data Factory Studio ポータル UI を使用することで、Azure Data Factory パイプラインで SQL Server Integration Services (SSIS) パッケージを実行する方法について説明します。 

## <a name="prerequisites"></a>前提条件

Azure-SSIS 統合ランタイム (IR) がない場合は、[「チュートリアル: Azure-SSIS 統合ランタイムのプロビジョニング](./tutorial-deploy-ssis-packages-azure.md)」の手順に従って作成します。

## <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS パッケージの実行アクティビティでパイプラインを作成する
この手順では、Data Factory の UI またはアプリを使用してパイプラインを作成します。 パイプラインに SSIS パッケージの実行アクティビティを追加し、SSIS パッケージを実行するように構成します。 

1. Azure portal の Data Factory の概要ページまたはホームページで、 **[作成と監視]** タイルを選択して Data Factory の UI またはアプリを別のタブで開始します。 

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png" alt-text="Data Factory ホーム ページ":::

   ホーム ページで **[調整]** を選択します。 

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF のホーム ページを示すスクリーンショット。":::

1. **[アクティビティ]** ツールボックスで **[全般]** を展開します。 次に、**SSIS パッケージの実行** アクティビティをパイプライン デザイナー画面にドラッグします。 

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png" alt-text="SSIS パッケージの実行アクティビティをデザイナー画面にドラッグする"::: 

   [SSIS パッケージの実行] アクティビティ オブジェクトを選択して、 **[全般]** 、 **[設定]** 、 **[SSIS パラメーター]** 、 **[接続マネージャー]** 、および **[プロパティのオーバーライド]** タブを構成します。

### <a name="general-tab"></a>[全般] タブ

[SSIS パッケージの実行] アクティビティの **[全般]** タブで、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png" alt-text="[一般] タブでプロパティを設定する":::

   1. **[名前]** には、[SSIS パッケージの実行] アクティビティの名前を入力します。

   1. **[説明]** には、[SSIS パッケージの実行] アクティビティの説明を入力します。

   1. **[タイムアウト]** には、[SSIS パッケージの実行] アクティビティを実行できる最大時間を入力します。 既定値は 7 日で、形式は D.HH:MM:SS です。

   1. **[再試行]** には、[SSIS パッケージ実行] アクティビティの最大再試行回数を入力します。

   1. **[再試行間隔]** には、[SSIS パッケージ実行] アクティビティの各再試行の間隔を秒数で入力します。 既定値は 30 秒です。

   1. **[Secure output]\(出力の保護\)** チェック ボックスをオンにして、[SSIS パッケージの実行] アクティビティの出力をログから除外するかどうかを選択します。

   1. **[Secure input]\(入力の保護\)** チェック ボックスをオンにして、[SSIS パッケージの実行] アクティビティの入力をログから除外するかどうかを選択します。

### <a name="settings-tab"></a>Settings tab

[SSIS パッケージの実行] アクティビティの **[設定]** タブで、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png" alt-text="[設定] タブでプロパティを設定する - 自動":::

   1. **[Azure-SSIS IR]** では、SSIS パッケージの実行アクティビティを実行する指定された Azure-SSIS IR を選択します。

   1. **[説明]** には、[SSIS パッケージの実行] アクティビティの説明を入力します。

   1. **[Windows 認証]** チェック ボックスをオンにして、Windows 認証を使用してデータ ストア (オンプレミスの SQL サーバー/ファイル共有、Azure Files など) にアクセスするかどうかを選択します。
   
      このチェック ボックスを選択した場合、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、パッケージ実行資格情報の値を入力します。 たとえば、Azure Files にアクセスする場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。

      または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます。 これを行うには、その横にある **[AZURE KEY VAULT]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、値に対するシークレットの名前またはバージョンを選択します。 キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。
      
   1. **[32 ビット ランタイム]** チェック ボックスをオンにして、パッケージの実行に 32 ビット ランタイムが必要かどうかを選択します。

   1. **[パッケージの場所]** では、 **[SSISDB]** 、 **[ファイル システム (パッケージ)]** 、 **[ファイル システム (プロジェクト)]** 、 **[Embedded package]\(埋め込みパッケージ\)** 、または **[パッケージ ストア]** を選択します。 

#### <a name="package-location-ssisdb"></a>パッケージの場所:SSISDB

Azure-SSIS IR が Azure SQL Database サーバー/Managed Instance でホストされている SSIS カタログ (SSISDB) を使用してプロビジョニングされている場合、パッケージの場所として **SSISDB** が自動的に選択されます。または、自分で選択することもできます。 選択されている場合は、次の手順を実行します。

   1. Azure-SSIS IR の実行中に **[Manual entries]\(手動入力\)** チェック ボックスがオフの場合は、既存のフォルダー、プロジェクト、/パッケージ、および環境を SSISDB から参照します。 **[最新の情報に更新]** を選択して、SSISDB から新しく追加したフォルダー、プロジェクト、パッケージ、または環境を取り込み、それらを参照して選択できるようにします。 パッケージを実行するための環境を参照して選択するには、プロジェクトを事前に構成して、SSISDB の同じフォルダーからの参照としてこれらの環境を追加する必要があります。 詳細については、[SSIS 環境の作成とマップ](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages)に関するページを参照してください。

   1. **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、 **[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 

   1. Azure-SSIS IR が実行されていない場合、または **[Manual entries]\(手動入力\)** チェック ボックスがオンになっている場合は、`<folder name>/<project name>/<package name>.dtsx` および `<folder name>/<environment name>` の形式で SSISDB からパッケージと環境のパスを直接入力します。

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png" alt-text="[設定] タブでプロパティを設定する - 手動":::

#### <a name="package-location-file-system-package"></a>パッケージの場所:ファイル システム (パッケージ)

Azure-SSIS IR が SSISDB なしでプロビジョニングされた場合、パッケージの場所として **[ファイル システム (パッケージ)]** が自動的に選択されます。または、自分で選択することもできます。 選択されている場合は、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png" alt-text="[設定] タブでプロパティを設定する - ファイル システム (パッケージ)":::
   
   1. **[パッケージ パス]** でパッケージ ファイル (`.dtsx` を含む) への汎用名前付け規則 (UNC) パスを指定して、実行するパッケージを指定します。 パッケージを参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、Azure Files にパッケージを保存する場合、そのパスは `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx` です。 
   
   1. 別のファイルでパッケージを構成する場合は、 **[構成パス]** ボックスに構成ファイル (`.dtsConfig` を含む) への UNC パスも指定する必要があります。 構成を参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、構成を Azure Files に保存する場合、そのパスは `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` です。

   1. パッケージと構成ファイルにアクセスするための資格情報を指定します。 以前にパッケージ実行の資格情報値を入力した場合は (**Windows 認証** 用)、 **[Same as package execution credentials]\(パッケージ実行の資格情報と同じ\)** チェック ボックスをオンにして再利用できます。 それ以外の場合は、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、パッケージ アクセス資格情報の値を入力します。 たとえば、パッケージおよび構成を Azure Files に保存する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 

      または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます。 これを行うには、その横にある **[AZURE KEY VAULT]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、値に対するシークレットの名前またはバージョンを選択します。 キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。 

      これらの資格情報は、パッケージで指定されている独自のパスや他の構成によって参照される、パッケージ実行タスクの子パッケージにアクセスするためにも使用されます。 

   1. SQL Server Data Tools (SSDT) を使用してパッケージを作成するときに **EncryptAllWithPassword** または **EncryptSensitiveWithPassword** 保護レベルを使用した場合は、 **[暗号化用パスワード]** ボックスにパスワードの値を入力します。 または、Azure Key Vault に格納されているシークレットを値として使用することもできます (上述を参照)。
      
      **EncryptSensitiveWithUserKey** 保護レベルを使用した場合は、構成ファイルか、 **[SSIS パラメータ]** 、 **[接続マネージャー]** 、または **[プロパティのオーバーライド]** タブに、機密性の高い値を再入力します (下記を参照)。
      
      **EncryptAllWithUserKey** 保護レベルを使用した場合は、サポートされません。 SSDT または `dtutil` コマンドライン ユーティリティを使用して、別の保護レベルを使用するようにパッケージを再構成する必要があります。 

   1. **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、 **[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 
   
   1. パッケージに指定できる標準のログ プロバイダーを使用する以外に、パッケージの実行をログに記録する場合は、 **[ログ パス]** ボックスで UNC パスを指定してログ フォルダーを指定します。 ログ フォルダを参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、ログを Azure Files に保存する場合、ログ記録のパスは `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` です。 サブフォルダーは、個々のパッケージ実行ごとにこのパス内に作成され、SSIS パッケージの実行アクティビティの実行 ID に基づいて名前が付けられます。このログ ファイルは 5 分ごとに生成されます。 
   
   1. ログ フォルダーにアクセスするための資格情報を指定します。 以前にパッケージ アクセスの資格情報値を入力した場合は (上記を参照)、 **[Same as package access credentials]\(パッケージ アクセスの資格情報と同じ\)** チェック ボックスをオンにして再利用できます。 それ以外の場合は、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、ログイン アクセス資格情報の値を入力します。 たとえば、Azure Files にログを格納する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます (上記を参照)。
   
前述のすべての UNC パスでは、完全修飾ファイル名を 260 文字未満にする必要があります。 ディレクトリ名は 248 文字未満でなければなりません。

#### <a name="package-location-file-system-project"></a>パッケージの場所:ファイル システム (プロジェクト)

パッケージの場所として **[ファイル システム (プロジェクト)]** を選択した場合は、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png" alt-text="[設定] タブでプロパティを設定する - ファイル システム (プロジェクト)":::

   1. 実行するパッケージを指定するには、 **[プロジェクト パス]** ボックスにプロジェクト ファイル (`.ispac` を含む) への UNC パス、 **[パッケージ名]** ボックスにプロジェクトのパッケージ ファイル (`.dtsx` を含む) を指定します。 プロジェクトを参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、Azure Files にプロジェクトを保存する場合、そのパスは `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac` です。

   1. プロジェクトおよびパッケージ ファイルにアクセスするための資格情報を指定します。 以前にパッケージ実行の資格情報値を入力した場合は (**Windows 認証** 用)、 **[Same as package execution credentials]\(パッケージ実行の資格情報と同じ\)** チェック ボックスをオンにして再利用できます。 それ以外の場合は、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、パッケージ アクセス資格情報の値を入力します。 たとえば、プロジェクトを Azure Files に保存する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 

      または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます。 これを行うには、その横にある **[AZURE KEY VAULT]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、値に対するシークレットの名前またはバージョンを選択します。 キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。 

      これらの資格情報は、同じプロジェクトから参照されるパッケージ実行タスクの子パッケージにアクセスするためにも使用されます。 

   1. SSDT を使用してパッケージを作成するときに **EncryptAllWithPassword** または **EncryptSensitiveWithPassword** 保護レベルを使用した場合は、 **[暗号化用パスワード]** ボックスにパスワードの値を入力します。 または、Azure Key Vault に格納されているシークレットを値として使用することもできます (上述を参照)。
      
      **EncryptSensitiveWithUserKey** 保護レベルを使用した場合は、 **[SSIS パラメータ]** 、 **[接続マネージャー]** 、または **[プロパティのオーバーライド]** タブに、機密性の高い値を再入力します (後述を参照)。
      
      **EncryptAllWithUserKey** 保護レベルを使用した場合は、サポートされません。 SSDT または `dtutil` コマンドライン ユーティリティを使用して、別の保護レベルを使用するようにパッケージを再構成する必要があります。 

   1. **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、 **[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 
   
   1. パッケージに指定できる標準のログ プロバイダーを使用する以外に、パッケージの実行をログに記録する場合は、 **[ログ パス]** ボックスで UNC パスを指定してログ フォルダーを指定します。 ログ フォルダを参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、ログを Azure Files に保存する場合、ログ記録のパスは `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` です。 サブフォルダーは、個々のパッケージ実行ごとにこのパス内に作成され、SSIS パッケージの実行アクティビティの実行 ID に基づいて名前が付けられます。このログ ファイルは 5 分ごとに生成されます。 
   
   1. ログ フォルダーにアクセスするための資格情報を指定します。 以前にパッケージ アクセスの資格情報値を入力した場合は (上記を参照)、 **[Same as package access credentials]\(パッケージ アクセスの資格情報と同じ\)** チェック ボックスをオンにして再利用できます。 それ以外の場合は、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、ログイン アクセス資格情報の値を入力します。 たとえば、Azure Files にログを格納する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます (上記を参照)。
   
前述のすべての UNC パスでは、完全修飾ファイル名を 260 文字未満にする必要があります。 ディレクトリ名は 248 文字未満でなければなりません。

#### <a name="package-location-embedded-package"></a>パッケージの場所:埋め込みパッケージ

パッケージの場所として **[埋め込みパッケージ]** を選択した場合は、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png" alt-text="[設定] タブでプロパティを設定する - 埋め込みパッケージ":::

   1. パッケージ ファイル (`.dtsx`) をドラッグ アンド ドロップするか、ファイル フォルダーから指定されたボックスに **アップロード** します。 パッケージは自動的に圧縮され、アクティビティ ペイロードに埋め込まれます。 埋め込みが完了すると、パッケージは後で編集用に **ダウンロード** できます。 また、埋め込みパッケージを複数のアクティビティで使用できるパイプライン パラメーターに割り当てることによって **パラメーター化する** ことで、パイプライン ペイロードのサイズを最適化することもできます。 現在、プロジェクト ファイル (`.ispac`) の埋め込みはサポートされていないため、埋め込みパッケージのプロジェクトレベルのスコープで SSIS パラメーター/接続マネージャーを使用することはできません。
   
   1. 埋め込みパッケージがすべて暗号化されておらず、その内部でパッケージ実行タスク (EPT) の使用が検出された場合は、 **[パッケージ実行タスク]** チェック ボックスが自動的に選択され、ファイル システムのパスで参照されている子パッケージも、埋め込むことができるように自動的に追加されます。
   
      EPT の使用を検出できない場合は、 **[パッケージ実行タスク]** チェック ボックスを手動で選択し、ファイル システムのパスで参照されている子パッケージを埋め込むことができるように 1 つずつ追加する必要があります。 子パッケージが SQL Server database (MSDB) に格納されている場合は、それらを埋め込むことができないため、Azure-SSIS IR が SQL Server 参照を使用してフェッチする MSDB にアクセスできることを確認する必要があります。 現在、プロジェクト ファイル (`.ispac`) の埋め込みはサポートされていないため、子パッケージのプロジェクトベースの参照を使用することはできません。
   
   1. SSDT を使用してパッケージを作成するときに **EncryptAllWithPassword** または **EncryptSensitiveWithPassword** 保護レベルを使用した場合は、 **[暗号化用パスワード]** ボックスにパスワードの値を入力します。 
   
      または、Azure Key Vault に格納されているシークレットを値として使用することもできます。 これを行うには、その横にある **[AZURE KEY VAULT]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、値に対するシークレットの名前またはバージョンを選択します。 キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。
      
      **EncryptSensitiveWithUserKey** 保護レベルを使用した場合は、構成ファイルか、 **[SSIS パラメータ]** 、 **[接続マネージャー]** 、または **[プロパティのオーバーライド]** タブに、機密性の高い値を再入力します (下記を参照)。
      
      **EncryptAllWithUserKey** 保護レベルを使用した場合は、サポートされません。 SSDT または `dtutil` コマンドライン ユーティリティを使用して、別の保護レベルを使用するようにパッケージを再構成する必要があります。

   1. **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、 **[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 
   
   1. パッケージに指定できる標準のログ プロバイダーを使用する以外に、パッケージの実行をログに記録する場合は、 **[ログ パス]** ボックスで UNC パスを指定してログ フォルダーを指定します。 ログ フォルダを参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、ログを Azure Files に保存する場合、ログ記録のパスは `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` です。 サブフォルダーは、個々のパッケージ実行ごとにこのパス内に作成され、SSIS パッケージの実行アクティビティの実行 ID に基づいて名前が付けられます。このログ ファイルは 5 分ごとに生成されます。 
   
   1. ログ フォルダーにアクセスするための資格情報を指定するには、 **[ドメイン]** 、 **[ユーザー名]** 、および **[パスワード]** ボックスに値を入力します。 たとえば、Azure Files にログを格納する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます (上記を参照)。
   
前述のすべての UNC パスでは、完全修飾ファイル名を 260 文字未満にする必要があります。 ディレクトリ名は 248 文字未満でなければなりません。

#### <a name="package-location-package-store"></a>パッケージの場所:パッケージ ストア

パッケージの場所として **[パッケージ ストア]** を選択した場合は、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings6.png" alt-text="[設定] タブでプロパティを設定する - パッケージ ストア":::
   
   1. **[Package store name]\(パッケージ ストア名\)** には、Azure-SSIS IR に関連付けられている既存のパッケージ ストアを選択します。

   1. **[パッケージ パス]** ボックスで、選択したパッケージ ストアからのパス (`.dtsx` なし) を指定して、実行するパッケージを指定します。 選択したパッケージ ストアがファイル システム/Azure Files の上にある場合、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択してパッケージを参照して選択できます。それ以外の場合は、`<folder name>\<package name>` の形式でパスを入力できます。 [レガシ SSIS パッケージ ストア](/sql/integration-services/service/package-management-ssis-service)と同様に SQL Server Management Studio (SSMS) を使用して、選択したパッケージ ストアに新しいパッケージをインポートすることもできます。 詳細については、[Azure-SSIS IR パッケージ ストアを使用した SSIS パッケージの管理](./azure-ssis-integration-runtime-package-store.md)に関するページを参照してください。

   1. 別のファイルでパッケージを構成する場合は、 **[構成パス]** ボックスに構成ファイルへの UNC パス (`.dtsConfig`) を指定する必要があります。 構成を参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、構成を Azure Files に保存する場合、そのパスは `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig` です。

   1. 構成ファイルにアクセスするための資格情報を個別で指定するかどうかを選択するには、 **[Configuration access credentials]\(構成アクセスの資格情報\)** チェック ボックスをオンにします。 これは、選択したパッケージ ストアが Azure SQL Managed Instance でホストされている SQL Server データベース (MSDB) 上にある場合、または構成ファイルを格納していない場合に必要です。
   
      以前にパッケージ実行の資格情報値を入力した場合は (**Windows 認証** 用)、 **[Same as package execution credentials]\(パッケージ実行の資格情報と同じ\)** チェック ボックスをオンにして再利用できます。 それ以外の場合は、 **[ドメイン]** 、 **[ユーザー名]** 、 **[パスワード]** ボックスに、構成アクセス資格情報の値を入力します。 たとえば、構成を Azure Files に保存する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 

      または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます。 これを行うには、その横にある **[AZURE KEY VAULT]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、値に対するシークレットの名前またはバージョンを選択します。 キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。

   1. SSDT を使用してパッケージを作成するときに **EncryptAllWithPassword** または **EncryptSensitiveWithPassword** 保護レベルを使用した場合は、 **[暗号化用パスワード]** ボックスにパスワードの値を入力します。 または、Azure Key Vault に格納されているシークレットを値として使用することもできます (上述を参照)。
      
      **EncryptSensitiveWithUserKey** 保護レベルを使用した場合は、構成ファイルか、 **[SSIS パラメータ]** 、 **[接続マネージャー]** 、または **[プロパティのオーバーライド]** タブに、機密性の高い値を再入力します (下記を参照)。
      
      **EncryptAllWithUserKey** 保護レベルを使用した場合は、サポートされません。 SSDT または `dtutil` コマンドライン ユーティリティを使用して、別の保護レベルを使用するようにパッケージを再構成する必要があります。 

   1. **[ログ レベル]** で、パッケージ実行用のログの定義済みのスコープを選択します。 カスタマイズしたログ名を代わりに入力する場合は、 **[Customized]\(カスタマイズ\)** チェック ボックスをオンにします。 
   
   1. パッケージに指定できる標準のログ プロバイダーを使用する以外に、パッケージの実行をログに記録する場合は、 **[ログ パス]** ボックスで UNC パスを指定してログ フォルダーを指定します。 ログ フォルダを参照して選択するには、 **[Browse file storage]\(ファイル ストレージを参照\)** を選択するか、パスを手動で入力します。 たとえば、ログを Azure Files に保存する場合、ログ記録のパスは `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>` です。 サブフォルダーは、個々のパッケージ実行ごとにこのパス内に作成され、SSIS パッケージの実行アクティビティの実行 ID に基づいて名前が付けられます。このログ ファイルは 5 分ごとに生成されます。 
   
   1. ログ フォルダーにアクセスするための資格情報を指定するには、 **[ドメイン]** 、 **[ユーザー名]** 、および **[パスワード]** ボックスに値を入力します。 たとえば、Azure Files にログを格納する場合、ドメインは `Azure`、ユーザー名は `<storage account name>`、パスワードは `<storage account key>` です。 または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます (上記を参照)。
   
前述のすべての UNC パスでは、完全修飾ファイル名を 260 文字未満にする必要があります。 ディレクトリ名は 248 文字未満でなければなりません。

### <a name="ssis-parameters-tab"></a>[SSIS パラメーター] タブ

[SSIS パッケージの実行] アクティビティの **[SSIS パラメーター]** タブで、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png" alt-text="[SSIS パラメーター] タブでプロパティを設定する":::

   1. Azure-SSIS IR の実行中にパッケージの場所として **[SSISDB]** が選択され、 **[設定]** タブの **[Manual entries]\(手動入力\)** チェック ボックスがオフの場合は、値を割り当てるパラメーターとして、SSISDB から選択したプロジェクトおよびパッケージ内の既存の SSIS パラメーターが表示されます。 それ以外の場合は、値を 1 つずつ入力して手動で値を割り当てることができます。 パッケージの実行が成功するためには、それらが存在し、正しく入力されていることを確認します。 
   
   1. SSDT でパッケージを作成するときに **EncryptSensitiveWithUserKey** 保護レベルを使用し、パッケージの場所として **[ファイル システム (パッケージ)]** 、 **[ファイル システム (プロジェクト)]** 、 **[埋め込みパッケージ]** 、または **[パッケージ ストア]** を選択する場合は、構成ファイルまたはこのタブで機密性の高いパラメーターを再入力して値を割り当てる必要もあります。 
   
パラメーターに値を割り当てるときに、式、関数、Data Factory システム変数、および Data Factory パイプライン パラメーターまたは変数を使用して動的コンテンツを追加できます。

または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます。 これを行うには、その横にある **[AZURE KEY VAULT]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、値に対するシークレットの名前またはバージョンを選択します。 キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。 

### <a name="connection-managers-tab"></a>[接続マネージャー] タブ

[SSIS パッケージの実行] アクティビティの **[接続マネージャー]** タブで、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png" alt-text="[接続マネージャー] タブでプロパティを設定する":::

   1. Azure-SSIS IR の実行中にパッケージの場所として **[SSISDB]** が選択され、 **[設定]** タブの **[Manual entries]\(手動入力\)** チェック ボックスがオフの場合は、そのプロパティに値を割り当てるパラメーターとして、SSISDB から選択したプロジェクトおよびパッケージ内の既存の接続マネージャーが表示されます。 それ以外の場合は、値を 1 つずつ入力して手動でプロパティに値を割り当てることができます。 パッケージの実行が成功するためには、それらが存在し、正しく入力されていることを確認します。 
   
      接続マネージャーの正しい **SCOPE**、**NAME**、**PROPERTY** 名を取得するには、SSDT でその接続マネージャーが含まれているパッケージを開きます。 パッケージが開いたら、該当する接続マネージャーを選択して、SSDT の **[プロパティ]** ウィンドウで、そのすべてのプロパティの名前と値を表示します。 この情報を使用して、実行時に接続マネージャーのプロパティの値をオーバーライドできます。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-connection-manager-properties.png" alt-text="SSDT から接続マネージャーのプロパティを取得する":::

      たとえば、SSDT で元のパッケージを変更せずに、SQL Server で実行されているオンプレミスからオンプレミスへのデータ フローを、ADF の SSIS IR 上で実行されているオンプレミスからクラウドへのデータ フローに変換できます。これを行うには、実行時に既存の接続マネージャーの **ConnectByProxy**、**ConnectionString**、および **ConnectUsingManagedIdentity** プロパティの値をオーバーライドします。
      
      このような実行時のオーバーライドにより、オンプレミスのデータにアクセスするときに SSIS IR のプロキシとしてセルフホステッド IR (SHIR) を有効にすることができます ([SHIR を SSIS IR のプロキシとして構成する](./self-hosted-integration-runtime-proxy-ssis.md)方法に関する記事を参照)。また、最新の MSOLEDBSQL ドライバーを使用して Azure SQL Database/マネージド インスタンスに接続することで、ADF マネージド ID による Azure Active Directory (AAD) 認証が有効になります ([OLEDB 接続のために ADF マネージド ID による AAD 認証を構成する](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)方法に関する記事を参照)。

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers2.png" alt-text="SSDT の [接続マネージャー] タブでプロパティを設定する":::
   
   1. SSDT でパッケージを作成するときに **EncryptSensitiveWithUserKey** 保護レベルを使用し、パッケージの場所として **[ファイル システム (パッケージ)]** 、 **[ファイル システム (プロジェクト)]** 、 **[埋め込みパッケージ]** 、または **[パッケージ ストア]** を選択する場合は、構成ファイルまたはこのタブで機密性の高い接続マネージャー プロパティを再入力して値を割り当てる必要もあります。 

接続マネージャーのプロパティに値を割り当てるときに、式、関数、Data Factory システム変数、および Data Factory パイプライン パラメーターまたは変数を使用して動的コンテンツを追加できます。 

または、お使いの Azure Key Vault に格納されているシークレットを値として使用することもできます。 これを行うには、その横にある **[AZURE KEY VAULT]** チェック ボックスをオンにします。 既存のキー コンテナーのリンクされたサービスを選択または編集するか、新しいサービスを作成します。 次に、値に対するシークレットの名前またはバージョンを選択します。 キー コンテナーのリンクされたサービスを作成または編集するときに、既存のキー コンテナーを選択または編集したり、新しいキー コンテナーを作成したりできます。 まだ行っていない場合は、キー コンテナーへのアクセスを、Data Factory のマネージド ID に許可します。 `<key vault linked service name>/<secret name>/<secret version>` の形式でシークレットを直接入力することもできます。 

### <a name="property-overrides-tab"></a>[プロパティのオーバーライド] タブ

[SSIS パッケージの実行] アクティビティの **[プロパティのオーバーライド]** タブで、次の手順を実行します。

:::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png" alt-text="[プロパティのオーバーライド] タブでプロパティを設定する":::

   1. 選択したパッケージの既存のプロパティのパスを 1 つずつ入力して、手動でそれらに値を割り当てます。 パッケージの実行が成功するためには、それらが存在し、正しく入力されていることを確認します。 たとえば、ユーザー変数の値をオーバーライドするには、`\Package.Variables[User::<variable name>].Value` の形式でパスを入力します。 

      パッケージ プロパティの正しい **PROPERTY PATH** を取得するには、SSDT でそのプロパティが含まれているパッケージを開きます。 パッケージが開いたら、その制御フローを選択し、SSDT の **[プロパティ]** ウィンドウで **[構成]** プロパティを選択します。 次に、 **[構成]** プロパティの横にある省略記号 ( **...** ) ボタンを選択して、**パッケージ構成オーガナイザー** を開きます。これは、通常、[パッケージ配置モデルのパッケージ構成を作成する](/sql/integration-services/packages/legacy-package-deployment-ssis#create-package-configurations)ために使用されます。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties.png" alt-text="SSDT からパッケージ プロパティを取得する - [構成] プロパティ":::

      **[パッケージ構成オーガナイザー]** ダイアログ ボックスで、 **[パッケージの構成を有効にする]** チェック ボックスをオンにし、 **[追加...]** ボタンをクリックして **パッケージ構成ウィザード** を開きます。 
      
      **パッケージ構成ウィザード** で、 **[構成の種類]** ドロップダウン メニューの **[XML 構成ファイル]** 項目を選択し、 **[構成設定を直接指定する]** ボタンをクリックして構成ファイル名を入力し、 **[次へ >]** ボタンをクリックします。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties2.png" alt-text="SSDT からパッケージ プロパティを取得する - 構成オーガナイザー":::

      最後に、必要なパスを含むパッケージ プロパティを選択して **[次へ >]** をクリックします。  これで、必要なパッケージ プロパティのパスを表示し、コピーして構成ファイルに貼り付け、保存することができます。 この情報を使用して、実行時にパッケージ プロパティの値をオーバーライドできます。 

      :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/ssdt-package-properties3.png" alt-text="SSDT からパッケージ プロパティを取得する - 構成ウィザード":::
   
   1. SSDT でパッケージを作成するときに **EncryptSensitiveWithUserKey** 保護レベルを使用し、パッケージの場所として **[ファイル システム (パッケージ)]** 、 **[ファイル システム (プロジェクト)]** 、 **[埋め込みパッケージ]** 、または **[パッケージ ストア]** を選択する場合は、構成ファイルまたはこのタブで機密性の高いパッケージ プロパティを再入力して値を割り当てる必要もあります。 
   
パッケージ プロパティに値を割り当てるときに、式、関数、Data Factory システム変数、および Data Factory パイプライン パラメーターまたは変数を使用して動的コンテンツを追加できます。

構成ファイルおよび **[SSIS パラメーター]** タブで割り当てられた値は、 **[接続マネージャー]** タブまたは **[プロパティ オーバーライド]** タブを使用してオーバーライドできます。 **[接続マネージャー]** タブで割り当てられた値は、 **[プロパティ オーバーライド]** タブでオーバーライドすることもできます。

パイプラインの構成を検証するために、ツール バーの **[検証]** を選択します。 **[Pipeline Validation Report]\(パイプライン検証レポート\)** を閉じるには、 **[>>]** を選択します。

Data Factory にパイプラインを発行するには、 **[Publish All]\(すべて発行\)** を選択します。 

## <a name="run-the-pipeline"></a>パイプラインを実行する
この手順では、パイプラインの実行をトリガーします。 

1. パイプラインの実行をトリガーするには、ツール バーの **[トリガー]** を選択し、 **[Trigger Now]\(今すぐトリガー\)** を選択します。 

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png" alt-text="[Trigger Now]\(今すぐトリガー\)":::

2. **[Pipeline Run]\(パイプラインの実行\)** ウィンドウで **[完了]** を選択します。 

## <a name="monitor-the-pipeline"></a>パイプラインの監視

1. 左側で **[監視]** タブに切り替えます。 パイプラインの実行とその状態が、その他の情報 (**実行開始** 時刻など) と共に表示されます。 表示を更新するには、 **[最新の情報に更新]** を選択します。

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png" alt-text="パイプライン実行":::

2. **[アクション]** 列の **[View Activity Runs]\(アクティビティの実行の表示\)** リンクを選択します。 パイプラインに 1 つしかアクティビティがないので、アクティビティの実行が 1 つだけ表示されます。 SSIS パッケージの実行アクティビティです。

   :::image type="content" source="./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png" alt-text="アクティビティの実行":::

3. パッケージが実行されたことを確認するには、次のクエリを SQL サーバーの SSISDB データベースに対して実行します。 

   ```sql
   select * from catalog.executions
   ```

   :::image type="content" source="./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png" alt-text="パッケージの実行を確認する":::

4. パイプラインのアクティビティ実行の出力から SSISDB 実行 ID を取得し、その ID を使用して SQL Server Management Studio 内でより包括的な実行ログとエラー メッセージを確認することもできます。

   :::image type="content" source="media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png" alt-text="実行 ID を取得します。":::

## <a name="schedule-the-pipeline-with-a-trigger"></a>トリガーを使用してパイプラインのスケジュールを設定する

パイプラインがスケジュールに従って実行されるように (時間単位、日単位など)、パイプラインに対してスケジュールされたトリガーを作成することもできます。 例については、「[Create a data factory - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)」 (データ ファクトリの作成 - データ ファクトリ UI ) リンクをご確認ください。

## <a name="next-steps"></a>次のステップ
- [PowerShell を使用し、Azure Data Factory の SSIS パッケージの実行アクティビティで SSIS パッケージを実行する](how-to-invoke-ssis-package-ssis-activity-powershell.md)
- [Azure Data Factory パイプラインでの SSIS アクティビティを含む ETL/ELT ワークフローの最新化と拡張](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)