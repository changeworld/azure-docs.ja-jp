---
title: Azure SQL Managed Instance エージェントによって SSIS パッケージを実行する
description: Azure SQL Managed Instance エージェントによって SSIS パッケージを実行する方法を説明します。
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394061"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Azure SQL Managed Instance エージェントによって SSIS パッケージを実行する
この記事では、Azure SQL Managed Instance エージェントを使用して SQL Server Integration Services (SSIS) パッケージを実行する方法について説明します。 この機能は、オンプレミス環境で SQL Server エージェントによって SSIS パッケージをスケジュールする場合と同様に動作します。

この機能を使用すると、Azure SQL Managed Instance の SSISDB に格納されている SSIS パッケージや、Azure Files などのファイル システムを実行できます。

## <a name="prerequisites"></a>前提条件
この機能を使用するには、最新バージョンの SSMS (バージョン 18.5 以降) をダウンロードしてインストールします。 これは、[こちらの Web サイト](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)からダウンロードできます。

また、Azure SQL Managed Instance をエンドポイント サーバーとして使用する Azure-SSIS Integration Runtime を Azure Data Factory 内でプロビジョニングする必要があります。 これをまだプロビジョニングしていない場合は、[チュートリアル](tutorial-create-azure-ssis-runtime-portal.md)の手順に従ってプロビジョニングします。 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Azure SQL Managed Instance エージェントを使用して SSISDB 内の SSIS パッケージを実行する
このステップでは、Azure SQL Managed Instance エージェントを使用して、Azure SQL Managed Instance 内の SSISDB に格納されている SSIS パッケージを起動します。
1. 最新バージョンの SSMS で、Azure SQL Managed Instance に接続します。
2. 新しいエージェント ジョブと新しいジョブ ステップを作成します。

![新しいエージェント ジョブ](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. **[新しいジョブ ステップ]** ページで、 **[SQL Server Integration Services パッケージ]** の種類を選択します。

![新しい SSIS ジョブ ステップ](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. **[パッケージ]** タブで、パッケージ ソースの種類として **[SSIS カタログ]** を選択します。
5. SSISDB は同じ Azure SQL Managed Instance 内にあるため、認証を指定する必要はありません。
6. SSISDB から SSIS パッケージを指定します。

![パッケージ ソースの種類 - SSIS カタログ](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. **[構成]** タブでは、 **[パラメーター]** 値の指定、 **[接続マネージャー]** 値のオーバーライド、 **[プロパティ]** のオーバーライド、 **[ログ レベル]** の選択を行うことができます。

![パッケージ ソースの種類 - SSIS カタログ構成](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. 上記のすべての構成を完了したら、 **[OK]** をクリックして、エージェント ジョブの構成を保存します。
9. SSIS パッケージを実行するエージェント ジョブを開始します。


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Azure SQL Managed Instance エージェントによってファイル システム内の SSIS パッケージを実行する
このステップでは、Azure SQL Managed Instance エージェントを使用して、ファイル システムに格納されている SSIS パッケージを起動して実行します。
1. 最新バージョンの SSMS で、Azure SQL Managed Instance に接続します。
2. 新しいエージェント ジョブと新しいジョブ ステップを作成します。

   ![新しいエージェント ジョブ](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. **[新しいジョブ ステップ]** ページで、 **[SQL Server Integration Services パッケージ]** の種類を選択します。

   ![新しい SSIS ジョブ ステップ](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. **[パッケージ]** タブで、パッケージ ソースの種類として **[ファイル システム]** を選択します。

   ![パッケージ ソースの種類 - ファイル システム](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. パッケージが Azure ファイルにアップロードされている場合は、ファイル ソースの種類として **[Azure ファイル共有]** を選択します。
      - パッケージ パスは **\\<storage account name>.file.core.windows.net\<ファイル共有名>\<パッケージ名>.dtsx** です
      - Azure ファイルにアクセスするには、 **[Package file access credential]\(パッケージ ファイル アクセス資格情報\)** に Azure ファイル アカウント名とアカウント キーを入力します。 ドメインは **Azure** として設定されます。
   2. パッケージがネットワーク共有にアップロードされている場合は、 **[ネットワーク共有]** をファイル ソースの種類として選択します。
      - パッケージ パスは、パッケージ ファイルの **UNC パス**であり、dtsx 拡張子が付いています。
      - ネットワーク共有パッケージ ファイルにアクセスするには、対応する**ドメイン**、**ユーザー名**、および**パスワード**を入力します。
   3. パッケージ ファイルがパスワードで暗号化されている場合は、 **[暗号化用パスワード]** を選択し、パスワードを入力します。

 5. SSIS パッケージを実行するための構成ファイルが必要な場合は、 **[構成]** タブで、**構成ファイルのパス**を入力します。
 6. **[実行オプション]** タブで、SSIS パッケージを実行するために **Windows 認証**と **32 ビット ランタイム**のどちらを使用するかを選択できます。
 7. **[ログ]** タブでは、ログ ファイルを格納するための**ログ パス**および対応するログ アクセス資格情報を選択できます。 既定では、ログ パスはパッケージ フォルダー パスと同じであり、ログ アクセス資格情報はパッケージ アクセス資格情報と同じになります。
 8. **[Set values]\(値の設定\)** タブで、 **[プロパティのパス]** および **[値]** に入力してパッケージのプロパティをオーバーライドできます。
 たとえば、ユーザー変数の値をオーバーライドするには、 **\Package.Variables[User::<variable name>].Value** の形式でパスを入力します。
 9. 上記のすべての構成を完了したら、 **[OK]** をクリックして、エージェント ジョブの構成を保存します。
 10. SSIS パッケージを実行するエージェント ジョブを開始します。


 ## <a name="cancel-ssis-package-execution"></a>SSIS パッケージ実行の取り消し
 Azure SQL マネージド エージェント ジョブからパッケージの実行を取り消すには、エージェント ジョブを直接停止するのではなく、以下の手順に従う必要があります。
 1. **msdb.dbo.sysjobs** から SQL エージェント **jobId** を探します。
 2. 次のクエリによって対応する SSIS **executionId** をジョブ ID に基づいて探します。
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. SSIS カタログの下の **[アクティブな操作]** を選択します。

    ![パッケージ ソースの種類 - ファイル システム](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. **executionId** に基づいて、対応する操作を停止します。

## <a name="next-steps"></a>次のステップ
 Azure Data Factory を使用して、SSIS パッケージのスケジュールを設定することもできます。 詳細な手順については、[Azure Data Factory イベント トリガー](how-to-create-event-trigger.md)に関する記事を参照してください。 