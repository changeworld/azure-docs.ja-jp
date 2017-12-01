---
title: "Azure Automation アカウントで監視タスクを作成する | Microsoft Docs"
description: "Azure Automation アカウントで監視タスクを作成してフォルダーに作成された新しいファイルを監視する方法を説明します。"
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Azure Automation の監視タスクでは、ローカル データセンターで発生しているイベントに対処できます

このチュートリアルでは、以下の目的で、新しい監視タスクを作成する方法を学習します。

> [!div class="checklist"]
> * ディレクトリ内の新しいファイルを検索する監視 Runbook を作成する。
> * Automation 変数を作成して、監視によってファイルが処理された最終時刻を保持する。
> * 監視 Runbook が新しいファイルを検出したときに呼び出される、アクション Runbook を作成する。
> * 監視 Runbook およびアクション Runbook を選択する、監視タスクを作成する。
> * ディレクトリに新しいファイルを追加することにより、監視をトリガーする。
> * 新しいファイルに関する情報を表示するアクション Runbook からの出力を検査する。  

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。
+ として機能します。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
+ 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](automation-offering-get-started.md)。
+ 監視タスクが実行される、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)。

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>新しいファイルを検索する監視 Runbook を作成する
1.  Automation アカウントを開き、[Runbook] ページをクリックします。
2.  [ギャラリーの参照] ボタンをクリックします。
![Runbook 一覧の UI](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  "Watch-NewFile" を検索し、その Runbook を Automation アカウントにインポートします。
![Runbook 発行の UI](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  [編集] をクリックして Runbook のソースを表示し、[発行] ボタンをクリックします。

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Automation 変数を作成して、監視によってファイルが処理された最終時刻を保持する
1.  共有リソースの下の [変数] ページを開き、[変数の追加] をクリックします。![変数の一覧の UI](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  名前に「Watch-NewFileTimestamp」を入力してください。
3.  DateTime 型の種類を選択し、[作成] ボタンをクリックします。
![透かし変数の作成の UI](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>監視 Runbook が新しいファイルを検出したときに呼び出されるアクション Runbook を作成する
1.  [プロセス オートメーション] カテゴリで [Runbook] ページをクリックします。
2.  [ギャラリーの参照] ボタンをクリックします。
3.  “Process-NewFile” を検索し、その Runbook を Automation アカウントにインポートします。
4.  [編集] をクリックして Runbook のソースを表示し、[発行] ボタンをクリックします。
![プロセス監視の UI](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>監視 Runbook およびアクション Runbook を選択する監視タスクを作成する
1.  [監視タスク] ページを開き、[監視タスクの追加] ボタンをクリックします。
![監視一覧の UI](media/automation-watchers-tutorial/WatchersList.png)
2.  名前として「WatchMyFolder」を入力します。
3.  [監視を構成] を選択し、“Watch-NewFile” Runbook を選択します。
![監視の構成の UI](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  パラメーターに次の値を入力します。
    *   FOLDERPATH。 新しいファイルが作成される場所である、ハイブリッド ワーカー上のフォルダー。
    *   EXTENSION。 すべてのファイル拡張子を処理するために、空白のままにします。
    *   RECURSE。 既定値のままにします。
    *   RUN SETTINGS。 ハイブリッド ワーカーを選択します。
5.  [OK] をクリックし、[監視] ページに戻るように選択します。
6.  [アクションを構成] を選択し、“Process-NewFile” Runbook を選択します。
![監視アクションの構成の UI](media/automation-watchers-tutorial/ConfigureAction.png)
7.  パラメーターに次の値を入力します。
    *   EVENTDATA。 空白のままにします。 監視 Runbook からデータが渡されます。
    *   Run Settings。 この Runbook は Automation サービスで実行するので、Azure のままにします。
8.  [OK] をクリックし、[監視] ページに戻るように選択します。
9.  [OK] をクリックして監視タスクを作成します。

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>ディレクトリに新しいファイルを追加することにより、監視をトリガーする
1.  ハイブリッド ワーカーにリモート接続します
2.  監視タスクによって監視されているフォルダーに、新しいテキスト ファイルを追加します。

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>新しいファイルに関する情報を表示するアクション Runbook からの出力を検査する
1.  "WatchMyFolder" の監視タスクをクリックします
2.  [監視ストリームを表示] をクリックして、監視が新しいファイルを検出しアクション Runbook を開始したかを確認します。
3.  [監視アクション ジョブを表示]をクリックして、アクション Runbook ジョブを表示します。
![監視アクション ジョブの UI](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>次のステップ:

詳細については、「[初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)」をご覧ください。








