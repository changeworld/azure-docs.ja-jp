---
title: SSDT から SSIS パッケージを実行する
description: SSDT から Azure 内で SSIS パッケージを実行する方法について説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81399441"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT から Azure 内で SSIS パッケージを実行する

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事で説明する SQL Server Data Tools (SSDT) での Azure 対応の SQL Server Integration Services (SSIS) プロジェクトの機能を使用すると、Azure Data Factory (ADF) 内の Azure-SSIS Integration Runtime (IR) でパッケージを実行できます。  この機能を使用して、既存の SSIS パッケージを Azure にリフト アンド シフト/以降する前にテストしたり、Azure 内で実行する新しい SSIS パッケージを開発したりすることができます。

この機能では、新しい Azure-SSIS IR を作成したり、既存のものを SSIS プロジェクトにアタッチしてから、その上でパッケージを実行することができます。  実行するパッケージを、プロジェクト デプロイ モデルで SSIS カタログ (SSISDB) にデプロイすること、およびパッケージ デプロイ モデルでファイル システム/ファイル共有/Azure Files にデプロイすることがサポートされています。 

## <a name="prerequisites"></a>前提条件
この機能を使うには、Visual Studio 用の SSIS プロジェクト拡張機能を含む最新の SSDT を[こちら](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)からダウンロードしてインストールするか、または[こちら](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)からスタンドアロン インストーラーとしてダウンロードしてインストールしてください。

## <a name="azure-enable-ssis-projects"></a>Azure 対応の SSIS プロジェクト
SSDT では、**Integration Services プロジェクト (Azure 対応)** テンプレートを使用して、Azure 対応の新しい SSIS プロジェクトを作成できます。

![Azure 対応の新しい SSIS プロジェクト](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

または、SSDT のソリューション エクスプローラー パネルでプロジェクト ノードを右クリックしてメニューをポップアップ表示し、 **[Azure-Enabled]\(Azure 対応\)** メニュー項目を選択することで、既存の SSIS プロジェクトを Azure 対応にすることもできます。

![既存の SSIS プロジェクトを Azure 対応にする](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

既存の SSIS プロジェクトを Azure 対応にするには、ターゲット サーバーのバージョンを Azure-SSIS IR でサポートされている最新のバージョン (現在は **SQL Server 2017**) に設定する必要があるため、まだ行っていない場合は、これを行うためのダイアログ ウィンドウが表示されます。

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Azure 対応プロジェクトを Azure Data Factory の SSIS に接続する
Azure 対応プロジェクトを ADF の SSIS に接続することにより、パッケージを Azure Files にアップロードして、Azure-SSIS IR 上で実行できます。  そのためには、以下の手順のようにします。

1. SSDT のソリューション エクスプローラー パネルでプロジェクトまたは **[Linked Azure Resources]\(リンクされた Azure リソース\)** ノードを右クリックしてメニュー を表示し、 **[Connect To SSIS in Azure Data Factory]\(Azure Data Factoryで SSIS に接続する\)** メニュー項目を選択して、 **[SSIS in ADF Connection Wizard]\(ADF での SSIS 接続ウィザード\)** を起動します。

   ![ADF で SSIS に接続する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. **[SSIS in ADF Introduction]\(ADF での SSIS の概要\)** ページで、概要を確認し、 **[次へ]** ボタンをクリックして続行します。

   ![ADF での SSIS の概要](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. **[Select SSIS IR in ADF]\(ADF で SSIS IR を選択\)** ページで、パッケージを実行する既存の ADF と Azure-SSIS IR を選択するか、まだない場合は新しく作成します。
   - 既存の Azure-SSIS IR を選択するには、まず、関連する Azure サブスクリプションと ADF を選択します。
   - Azure-SSIS IR がない既存の ADF を選択した場合は、 **[Create SSIS IR]\(SSIS IR の作成\)** ボタンをクリックして、ADF ポータル/アプリに新しく作成します。
   - ADF がない既存の Azure サブスクリプションを選択した場合は、 **[Create SSIS IR]\(SSIS IR の作成\)** ボタンをクリックして **Integration Runtime 作成ウィザード**を起動します。そこでは、新しい Azure リソース グループ、Data Factory、SSIS IR が自動的に作成されるように、場所とプレフィックスを入力できます。次のパターンで名前が設定されます。**YourPrefix-RG/DF/IR-YourCreationTime**.
   
   ![ADF で SSIS IR を選択する](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. **[Select Azure Storage]\(Azure Storage の選択\)** ページで、既存の Azure ストレージ アカウントを選択してパッケージを Azure Files にアップロードするか、ない場合は新しく作成します。
   - 既存の Azure ストレージ アカウントを選択するには、まず、関連する Azure サブスクリプションを選択します。
   - Azure ストレージ アカウントがない Azure-SSIS IR と同じ Azure サブスクリプションを選択した場合は、 **[Create Azure Storage]\(Azure Storage の作成\)** ボタンをクリックして、Azure-SSIS IR と同じ場所に自動的に新しいものを作成します。名前は、Azure-SSIS IR の名前のプレフィックスと作成日を組み合わせて付けられます。
   - Azure ストレージ アカウントがない別の Azure サブスクリプションを選択した場合は、 **[Create Azure Storage]\(Azure Storage の作成\)** ボタンをクリックして、Azure portal で新しく作成します。
   
   ![Azure Storage を選択する](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. **[接続]** ボタンをクリックして、接続を完了します。  SSDT のソリューション エクスプローラー パネルの **[Linked Azure Resources]\(リンクされた Azure リソース\)** ノードに、選択した Azure-SSIS IR と Azure ストレージ アカウントが表示されます。  また、Azure-SSIS IR の状態も更新されます。ノードを右クリックしてメニューをポップアップ表示し、 **[Start\Stop\Manage]\(開始\停止\管理\)** メニュー項目を選択して ADF ポータル/アプリに移動して、それを管理できます。

## <a name="execute-ssis-packages-in-azure"></a>Azure で SSIS パッケージを実行する
### <a name="starting-package-executions"></a>パッケージの実行を開始する
ADF の SSIS にプロジェクトを接続した後は、Azure-SSIS IR でパッケージを実行できます。  パッケージの実行を開始するには、次の 2 つのオプションがあります。
-  SSDT ツールバーの **[Start]\(開始\)** ボタンをクリックしてメニューをドロップダウンし、 **[Execute in Azure]\(Azure で実行\)** メニュー項目を選択します。 

   ![Azure で実行する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  SSDT のソリューション エクスプローラー パネルでパッケージ ノードを右クリックしてメニューをポップアップ表示し、 **[Execute Package in Azure]\(Azure でパッケージを実行\)** メニュー項目を選択します。

   ![Azure でパッケージを実行する](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Azure でパッケージを実行するには、Azure-SSIS IR が実行されている必要があるため、Azure-SSIS IR が停止している場合は、ダイアログ ウィンドウが表示されて、開始することができます。  カスタム セットアップ時間を除けば、このプロセスは 5 分以内に完了しますが、Azure SSIS IR が仮想ネットワークに参加するのに約 20 から 30 分かかる場合があります。  Azure でパッケージを実行した後は、SSDT のソリューション エクスプローラー パネルでノードを右クリックしてメニューをポップアップ表示し、 **[Start\Stop\Manage]\(開始\停止\管理\)** メニュー項目を選択して ADF ポータル/アプリに移動し、Azure-SSIS IR を停止して実行コストを管理できます。

### <a name="checking-package-execution-logs"></a>パッケージの実行ログをチェックする
パッケージの実行を開始すると、SSDT の進行状況ウィンドウにログが書式設定されて表示されます。  長時間実行されるパッケージの場合、ログは分単位で定期的に更新されます。  SSDT ツールバーの **[Stop]\(停止\)** ボタンをクリックすることで、パッケージの実行をすぐに停止できます。  汎用名前付け規則 (UNC) パス `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` でログの生データを一時的に検索できますが、1 日後にクリーンアップされます。

### <a name="switching-package-protection-level"></a>パッケージの保護レベルを切り替える
Azure での SSIS パッケージの実行では、**EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 保護レベルはサポートされません。  そのため、パッケージがそれらで構成されている場合は、Azure-SSIS IR で実行するためにパッケージを Azure Files にアップロードするときにランダムに生成されるパスワードで、それぞれ一時的に **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** に切り替えられます。

> [!NOTE]
> **EncryptSensitiveWithUserKey**/**EncryptAllWithUserKey** 保護レベルで構成された他のパッケージを参照するパッケージ実行タスクがパッケージに含まれている場合は、パッケージを実行する前に、それぞれ **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** を使用するようにこれらの他のパッケージを手動で再構成する必要があります。

パッケージが **EncryptSensitiveWithPassword**/**EncryptAllWithPassword** 保護レベルで既に構成されている場合は、変更されずに維持されますが、それでも Azure-SSIS IR で実行するためにパッケージを Azure Files にアップロードするときにランダムに生成されるパスワードが使用されます。

### <a name="using-package-configuration-file"></a>パッケージ構成ファイルを使用する
パッケージ デプロイ モデルのパッケージ構成ファイルを使用して実行時に変数の値を変更する場合は、パッケージを含むそれらのファイルが、Azure-SSIS IR で実行するために Azure Files に自動的にアップロードされます。

### <a name="using-execute-package-task"></a>パッケージ実行タスクを使用する
パッケージに、ローカル ファイル システムに格納されている他のパッケージを参照するパッケージ実行タスクが含まれている場合は、次の追加セットアップを実行する必要があります。

1. プロジェクトに接続されているのと同じ Azure ストレージ アカウントで他のパッケージを Azure Files にアップロードし、新しい UNC パス (例: `\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`) を取得します

2. パッケージ実行タスクのファイル接続マネージャーで、それらの他のパッケージのファイル パスを新しい UNC パスに置き換えます。
   - SSDT が実行されているコンピューターが新しい UNC パスにアクセスできない場合は、ファイル接続マネージャーの [プロパティ] パネルでファイル パスを変更できます。
   - または、ファイル パスに変数を使用して、実行時に適切な値を割り当てることもできます。

パッケージに、同じプロジェクト内の他のパッケージを参照するパッケージ実行タスクが含まれている場合は、追加のセットアップは必要ありません。

## <a name="next-steps"></a>次のステップ
SSDT から Azure でのパッケージの実行に問題がなければ、ADF パイプラインに SSIS パッケージ実行アクティビティとしてそれらをデプロイし、実行できます。[ADF パイプラインの SSIS パッケージ実行アクティビティとしての SSIS パッケージの実行](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に関する記事を参照してください。
