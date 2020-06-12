---
title: Azure-SSIS Integration Runtime パッケージ ストアを使用したパッケージの管理
description: Azure-SSIS Integration Runtime パッケージ ストアを使用してパッケージを管理する方法について説明します。
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
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84198869"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Azure-SSIS Integration Runtime パッケージ ストアを使用したパッケージの管理

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

オンプレミスの SQL Server Integration Services (SSIS) ワークロードをクラウドにリフト アンド シフトするには、Azure Data Factory (ADF) で Azure-SSIS Integration Runtime (IR) をプロビジョニングします。 詳細については、[Azure-SSIS IR のプロビジョニング](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)に関するページを参照してください。 Azure-SSIS IR では、次のことがサポートされます。

- Azure SQL Database サーバーまたは Managed Instance をホストとする SSIS カタログ (SSISDB) にデプロイされたパッケージを実行する (プロジェクト デプロイ モデル)
- Azure SQL Managed Instance をホストとするファイル システム、Azure Files、SQL Server データベース (MSDB) のいずれかにデプロイされたパッケージを実行する (パッケージ デプロイ モデル)

パッケージ デプロイ モデルを使用する場合は、Azure SQL Managed Instance によってホストされたファイル システム、Azure Files、または MSDB 上にパッケージ管理レイヤーを提供するパッケージ ストアとともに、Azure-SSIS IR をプロビジョニングするかどうかを選択できます。 Azure-SSIS IR パッケージ ストアを使用すると、パッケージのインポート、エクスポート、削除、実行のほか、実行中のパッケージの監視と停止を、[従来の SSIS パッケージ ストア](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)と同様に SQL Server Management Studio (SSMS) を介して行うことができます。 

## <a name="connect-to-azure-ssis-ir"></a>Azure-SSIS IR への接続

Azure-SSIS IR がプロビジョニングされたら、それに接続して SSMS でパッケージ ストアを参照することができます。

![Azure-SSIS IR への接続](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

SSMS の **[オブジェクト エクスプローラー]** ウィンドウで、 **[接続]** ドロップダウン メニューの **[Azure-SSIS Integration Runtime]** を選択します。 次に Azure にサインインし、パッケージ ストアがアタッチされている、関連するサブスクリプション、ADF、および Azure-SSIS IR を選択します。 Azure-SSIS IR が **[実行中のパッケージ]** および **[格納済みパッケージ]** ノードの下に表示されます。 **[格納済みパッケージ]** ノードを展開して、パッケージ ストアを下に表示します。 パッケージ ストアを展開して、配下にあるフォルダーとパッケージを表示します。 SSMS により自動的に接続できない場合は、パッケージ ストアのアクセス資格情報を入力するように求められる場合があります。 たとえば、MSDB の上部でパッケージ ストアを展開したとき、最初に Azure SQL Managed Instance に接続するように求められる場合があります。

![Azure SQL Managed Instance に接続する](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>フォルダーとパッケージの管理

SSMS で Azure-SSIS IR を参照しているときに、任意のパッケージ ストア、フォルダー、パッケージを右クリックしてメニューをポップアップ表示し、 **[新しいフォルダー]** 、 **[パッケージのインポート]** 、 **[パッケージのエクスポート]** 、 **[削除]** 、または **[更新]** を選択できます。

   ![フォルダーとパッケージの管理](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  インポートしたパッケージ用の新しいフォルダーを作成するには **[新しいフォルダー]** を選択します。

   *  **ファイル システム**、**SQL Server** (MSDB)、または従来の **SSIS パッケージ ストア** からパッケージ ストアにパッケージをインポートするには、 **[パッケージのインポート]** を選択します。

      ![パッケージのインポート](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      インポート元の **[パッケージの場所]** に応じて、該当する **[サーバー]** / **[認証の種類]** を選択し、必要に応じてアクセス資格情報を入力し、 **[パッケージのパス]** を選択して、新しい **[パッケージ名]** を入力します。 パッケージをインポートするときに、保護レベルを変更することはできません。 これを変更するには、SQL Server Data Tools (SSDT) または `dtutil` コマンド ライン ユーティリティを使用します。

   *  パッケージ ストアから **ファイル システム**、**SQL Server** (MSDB)、または従来の **SSIS パッケージ ストア**にパッケージをエクスポートするには、 **[パッケージのエクスポート]** を選択します。

      ![パッケージのエクスポート](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      エクスポート先の **[パッケージの場所]** に応じて、該当する **[サーバー]** / **[認証の種類]** を選択し、必要に応じてアクセス資格情報を入力して、 **[パッケージのパス]** を選択します。 パッケージをエクスポートするときに、それらが暗号化されている場合は、最初にパスワードを入力して暗号化を解除します。その後、機密データを保存しない、または、ユーザー キーやパスワードを使用してすべてのデータ/機密データを暗号化する、などに保護レベルを変更できます。

   *  パッケージ ストアから既存のフォルダーまたはパッケージを削除するには、 **[削除]** を選択します。

   *  パッケージ ストアに新しく追加されたフォルダーまたはパッケージを表示するには、 **[更新]** を選択します。

## <a name="execute-packages"></a>パッケージの実行

SSMS で Azure-SSIS IR を参照しているときに、格納されている任意のパッケージを右クリックしてメニューをポップアップ表示し、 **[パッケージの実行]** を選択できます。  これにより、 **[パッケージ実行ユーティリティ]** ダイアログが開き、ADF パイプラインでの SSIS パッケージ アクティビティとして、Azure-SSIS IR でパッケージの実行を構成できます。

![パッケージ実行ユーティリティ ページ 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![パッケージ実行ユーティリティ ページ 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**[パッケージ実行ユーティリティ]** ダイアログの **[全般]** 、 **[構成]** 、 **[実行オプション]** 、および **[ログ記録]** のページは、[SSIS パッケージの実行] アクティビティの **[設定]** タブに対応しています。ここでは、パッケージの暗号化パスワードの入力、パッケージの構成ファイルに関するアクセス情報、パッケージ実行の資格情報とプロパティ、そしてログ フォルダーのアクセス情報を入力できます。  **[パッケージ実行ユーティリティ]** ダイアログの **[値の設定]** ページは、[SSIS パッケージの実行] アクティビティの **[プロパティのオーバーライド]** タブに対応しています。ここでは、上書きする既存のパッケージのプロパティを入力できます。 詳細については、[ADF パイプラインで SSIS パッケージ実行アクティビティとして SSIS パッケージを実行する](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に方法に関するページを参照してください。

**[パッケージ実行ユーティリティ]** ダイアログの **[実行]** ボタンを選択すると、SSIS パッケージの実行アクティビティを含む、新しい ADF パイプラインが自動的に生成され、トリガーされます。 同じパッケージ実行設定をもつ ADF パイプラインが既に存在する場合は、それが再実行され、新しいパイプラインは生成されません。 ADF パイプラインと SSIS パッケージ実行アクティビティには、それぞれ `Pipeline_SSMS_YourPackageName_HashString` および `Activity_SSMS_YourPackageName` という名前が付けられます。

![パッケージ実行ユーティリティ ボタン](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS パッケージ アクティビティの実行](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>実行中のパッケージの監視および停止

SSMS で Azure-SSIS IR を参照しているときに、 **[実行中のパッケージ]** ノードを展開して、現在実行中のパッケージを下に表示できます。  そのいずれかを右クリックしてメニューをポップアップ表示し、 **[停止]** または **[更新]** を選択します。

   ![実行中のパッケージの監視および停止](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  [SSIS パッケージの実行] アクティビティとしてパッケージを実行する、現在実行中の ADF パイプラインをキャンセルするには、 **[停止]** を選択します。

   *  パッケージ ストアから新しく実行されたパッケージを表示するには、 **[更新]** を選択します。

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR の監視とパッケージ ストアの編集

SSMS で Azure-SSIS IR を参照しているときに、右クリックしてメニューをポップアップ表示し、 **[Go to Azure Data Factory portal]\(Azure Data Factory ポータルに移動\)** または **[更新]** を選択することができます。

   ![ADF ポータルに移動](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  **[Go to Azure Data Factory portal]\(Azure Data Factory ポータルに移動\)** を選択して、ADF 監視ハブの **[統合ランタイム]** ページを開きます。ここでは、お使いの Azure-SSIS IR を監視できます。 **[パッケージ ストア]** タイルで、Azure-SSIS IR にアタッチされているパッケージ ストアの数を確認できます。  この数値を選択すると、パッケージ ストアのアクセス情報を格納する、ADF のリンクされたサービスを編集できるウィンドウがポップアップ表示されます。

      ![パッケージ ストアの編集](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  パッケージ ストアに新しく追加されたフォルダーまたはパッケージ、およびパッケージ ストアから実行されたパッケージを表示するには、 **[更新]** を選択します。

## <a name="next-steps"></a>次のステップ

自動生成された ADF パイプラインを SSIS パッケージの実行アクティビティを使用して再実行または編集することや、ADF ポータルで新たに作成することができます。 詳細については、[ADF パイプラインで SSIS パッケージ実行アクティビティとして SSIS パッケージを実行する](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)に方法に関するページを参照してください。