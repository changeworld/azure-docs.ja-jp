---
title: Azure Automation 監視タスクを使用して更新されたファイルを追跡する
description: この記事では、Azure Automation アカウントで監視タスクを作成してフォルダーに作成された新しいファイルを監視する方法を説明します。
services: automation
ms.subservice: process-automation
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 0a4b62d60c255d79eaf0042aa075b216d362dbce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758505"
---
# <a name="track-updated-files-with-a-watcher-task"></a>監視タスクによる更新されたファイルの追跡

Azure Automation では、PowerShell Runbook で監視タスクを使用して、イベントを検索し、アクションをトリガーします。 監視タスクには、監視とアクションという 2 つの部分が含まれています。 監視 Runbook は、監視タスクで定義された間隔で実行され、データをアクション Runbook に出力します。

> [!NOTE]
> Azure China Vianet 21 では、監視タスクはサポートされていません。

> [!IMPORTANT]
> 2020 年 5 月から、イベントの監視、定期的なタスクのスケジュール設定、およびアクションのトリガーを行う方法として、Azure Logic Apps を使用することが推奨され、サポートされるようになりました。 「[Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)」を参照してください。

この記事では、ディレクトリへの新しいファイルの追加を監視する監視タスクの作成手順について説明します。 学習内容は次のとおりです。

* 監視 Runbook をインポートする
* Automation 変数を作成する
* アクション Runbook を作成する
* 監視タスクを作成する
* 監視をトリガーする
* 出力を確認する

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](./index.yml)。
* 監視タスクが実行される、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)。
* PowerShell Runbook。 PowerShell ワークフロー Runbook は、監視タスクではサポートされていません。

## <a name="import-a-watcher-runbook"></a>監視 Runbook をインポートする

この記事では、**ディレクトリ内の新規ファイルを検索する監視 Runbook** と呼ばれる監視 Runbook を使用して、ディレクトリ内の新規ファイルを検索します。 この監視 Runbook は、フォルダー内のファイルに対する既知の最終書き込み時刻を取得し、この基準時刻より新しいファイルを監視します。

次の手順を使用して、この Runbook をポータルから Automation アカウントにインポートできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Automation アカウント** を検索して選択します。 
1. **[Automation アカウント]** ページで、リストから自分の Automation アカウントの名前を選択します。
1. 左ウィンドウで、 **[プロセス オートメーション]** の下の **[Runbook ギャラリー]** を選択します。
1. **[ソース]** ドロップダウン リスト内で **GitHub** が選択されていることを確認します。
1. **監視 Runbook** を検索します。
1. **[Watcher runbook that looks for new files in a directory]\(ディレクトリ内の新規ファイルを検索する監視 Runbook\)** を選択し、詳細ページ上で **[インポート]** を選択します。
1. Runbook の名前と説明 (省略可能) を入力して **[OK]** をクリックし、Automation アカウントにこの Runbook をインポートします。 ウィンドウ右上のペイン内に、**インポート成功** というメッセージが表示されます。
1. インポートされた Runbook は、左側のウィンドウから [Runbook] を選択したときに指定した名前の下のリストに表示されます。
1. Runbook をクリックし、Runbook の詳細ページで **[編集]** を選択して、 **[公開]** をクリックします。 プロンプトが表示されたら、 **[はい]** をクリックして Runbook を公開します。

この Runbook は [Azure Automation GitHub 組織](https://github.com/azureautomation)からダウンロードすることもできます。

1. Azure Automation GitHub 組織ページの [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-runbook-that-looks-for-new-files-in-a-directory#watcher-runbook-that-looks-for-new-files-in-a-directory) に移動します。
1. GitHub から Runbook をダウンロードするには、ページの右側にある **[Code]\(コード\)** を選択し、 **[Download ZIP]\(ZIP のダウンロード\)** を選択して、zip ファイル内のコード全体をダウンロードします。
1. コンテンツを抽出し、[Runbook をインポート](manage-runbooks.md#import-a-runbook-from-the-azure-portal)します。

## <a name="create-an-automation-variable"></a>Automation 変数を作成する

[Automation 変数](./shared-resources/variables.md)を使用して、上述の Runbook が各ファイルから読み取って保存するタイムスタンプを格納します。

1. **[共有リソース]** の **[変数]** を選択し、 **[+ 変数の追加]** をクリックします。
1. 名前に「**Watch-NewFileTimestamp**」と入力します。
1. 型には **DateTime** を選択します。 既定では、現在の日付と時刻が指定されます。

   :::image type="content" source="./media/automation-watchers-tutorial/create-new-variable.png" alt-text="変数の新規作成ブレードのスクリーンショット。":::

1. **[作成]** をクリックして Automation 変数を作成します。

## <a name="create-an-action-runbook"></a>アクション Runbook を作成する

監視タスクで、アクション Runbook を使用して、監視 Runbook から渡されたデータを操作します。 事前定義済みアクション Runbook を、Azure portal または [Azure Automation GitHub 組織](https://github.com/azureautomation)からインポートする必要があります。

この Runbook を Azure portal から Automation アカウントにインポートできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Automation アカウント** を検索して選択します。 
1. **[Automation アカウント]** ページで、リストから自分の Automation アカウントの名前を選択します。
1. 左ウィンドウで、 **[プロセス オートメーション]** の下の **[Runbook ギャラリー]** を選択します。
1. **[ソース]** ドロップダウン リスト内で **GitHub** が選択されていることを確認します。
1. **監視アクション** を検索して **[Watcher action that processes events triggered by a watcher runbook]\(監視 Runbook でトリガーされたイベントを処理する監視アクション\)** を選択し、 **[インポート]** をクリックします。
1. 必要に応じて、インポート ページで Runbook の名前を変更し、 **[OK]** をクリックして Runbook をインポートします。 ブラウザー右上の通知ウィンドウ内に、**インポート成功** というメッセージが表示されます。
1. Automation アカウント ページに移動し、左側の **[Runbook]** をクリックします。 新しい Runbook は、前の手順で指定した名前の下に表示されます。 Runbook をクリックし、Runbook の詳細ページで **[編集]** を選択して、 **[公開]** をクリックします。 プロンプトが表示されたら、 **[はい]** をクリックして Runbook を公開します。

[Azure Automation GitHub 組織](https://github.com/azureautomation)からダウンロードすることによってアクション Runbook を作成するには:

1. Azure Automation GitHub 組織ページの [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook) に移動します。
1. GitHub から Runbook をダウンロードするには、ページの右側にある **[Code]\(コード\)** を選択し、 **[Download ZIP]\(ZIP のダウンロード\)** を選択して、zip ファイル内のコード全体をダウンロードします。
1. コンテンツを抽出し、[Runbook をインポート](manage-runbooks.md#import-a-runbook-from-the-azure-portal)します。

## <a name="create-a-watcher-task"></a>監視タスクを作成する

この手順では、前述のセクションで定義した監視 Runbook とアクション Runbook を参照する監視タスクを構成します。

1. Automation アカウントに移動して、 **[プロセスの自動化]** で **[監視タスク]** を選択します。
1. [監視タスク] ページを選択して、 **[+ 監視タスクの追加]** をクリックします。
1. 名前として「**WatchMyFolder**」を入力します。

1. **[監視を構成]** を選択し、**Watch-NewFile** Runbook を選びます。

1. 各パラメーターの値を次のように入力します。

   * **FOLDERPATH** - 新しいファイルが作成される、ハイブリッド Runbook ワーカー上のフォルダー (たとえば、**d:\examplefiles**)。
   * **EXTENSION** - 構成の拡張機能。 すべてのファイル拡張子を処理するために、空白のままにします。
   * **RECURSE** - 再帰的操作。 この値は既定値のままにします。
   * **RUN SETTINGS** - Runbook を実行するための設定。 ハイブリッド ワーカーを選択します。

1. **[OK]** をクリックし、[監視] ページに戻るように **選択** します。
1. **[アクションを構成]** を選択し、**Process-NewFile** Runbook を選びます。
1. 各パラメーターの値を次のように入力します。

   * **EVENTDATA** - イベント データ。 空白のままにします。 監視 Runbook からデータが渡されます。
   * **Run Settings** - Runbook を実行するための設定。 この Runbook は Azure Automation で実行するので、Azure のままにします。

1. **[OK]** をクリックし、[監視] ページに戻るように **選択** します。
1. **[OK]** をクリックして監視タスクを作成します。

   :::image type="content" source="./media/automation-watchers-tutorial/watchertaskcreation.png" alt-text="Azure portal 内での監視アクションの構成のスクリーンショット。":::


## <a name="trigger-a-watcher"></a>監視をトリガーする

監視タスクが期待どおりに動作することを確実にするために、次の説明に従ってテストを実行する必要があります。 

1. ハイブリッド Runbook ワーカーにリモート接続します。
1. **PowerShell** を開き、テスト対象のフォルダーにテスト ファイルを作成します。

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

次の例で、想定される出力を示します。

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>出力を確認する

1. Automation アカウントに移動して、 **[プロセスの自動化]** で **[監視タスク]** を選択します。
1. 監視タスク **WatchMyFolder** を選択します。
1. **[ストリーム]** の **[監視ストリームを表示]** をクリックして、監視によって新しいファイルが検出され、アクション Runbook が開始されたことを確認します。
1. アクション Runbook ジョブを表示するために、 **[監視アクション ジョブを表示]** をクリックします。 各ジョブを選択すると、ジョブの詳細を確認できます。

   :::image type="content" source="./media/automation-watchers-tutorial/WatcherActionJobs.png" alt-text="Azure portal からの監視アクション ジョブのスクリーンショット。":::


新しいファイルが検出された場合に想定される出力は、次の例のようになります。

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>次のステップ

独自の Runbook の作成の詳細については、[PowerShell Runbook の作成](./learn/powershell-runbook-managed-identity.md)に関する記事を参照してください。