---
title: Visual Studio Code で Azure Stream Analytics ジョブを調べる
description: この記事では、Azure Stream Analytics ジョブをローカル プロジェクトにエクスポートする方法、ジョブを一覧表示する方法、ジョブ エンティティを表示する方法について説明します。
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 07/21/2021
ms.topic: how-to
ms.openlocfilehash: 85b6dece608f47818f2115d0462dd4a56752581f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750601"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Visual Studio Code で使用する Azure Stream Analytics の詳細 (プレビュー)

Visual Studio Code 用 Azure Stream Analytics 拡張機能は、Stream Analytics ジョブを管理するための軽量エクスペリエンスを開発者に提供します。 Windows、Mac、Linux 上で使用できます。 Azure Stream Analytics 拡張機能を使用すると、次のことができます。

- ジョブの[作成](quick-create-visual-studio-code.md)、開始、停止
- 既存のジョブのローカル プロジェクトへのエクスポート
- ジョブを一覧表示してジョブ エンティティを表示する
- ジョブ モニターでのジョブ ダイアグラムの表示とデバッグ

## <a name="export-a-job-to-a-local-project"></a>ジョブのローカル プロジェクトへのエクスポート

ジョブをローカル プロジェクトにエクスポートするには、Visual Studio Code の **Stream Analytics Explorer** で、エクスポートするジョブを特定します。 次に、プロジェクトのフォルダーを選択します。 プロジェクトが選択したフォルダーにエクスポートされ、Visual Studio Code からジョブの管理を続行できます。 Visual Studio Code を使用した Stream Analytics ジョブの管理の詳細については、Visual Studio Code の[クイック スタート](quick-create-visual-studio-code.md)を参照してください。

![Visual Studio Code での ASA ジョブのエクスポート](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>ジョブを一覧表示してジョブ エンティティを表示する

ジョブ ビューを使用して、Visual Studio から Azure Stream Analytics ジョブとやりとりすることができます。


1. Visual Studio Code のアクティビティ バーの **[Azure]** アイコンをクリックし、**Stream Analytics ノード** を展開します。 ジョブは、サブスクリプションの下に表示されます。

   ![Stream Analytics Explorer を開く](./media/vscode-explore-jobs/open-explorer.png)

2. ジョブ ノードを展開し、ジョブのクエリ、構成、入力、出力、および関数を開いて表示できます。 

3. ジョブ ノードを右クリックし、 **[Open Job View in Portal]\(ポータルでジョブ ビューを開く\)** ノードを選択して、Azure portal でジョブ ビューを開きます。

   ![[Open Job View in Portal]\(ポータルでジョブ ビューを開く\)](./media/vscode-explore-jobs/open-job-view.png)

## <a name="view-job-diagram-and-debug-in-job-monitor"></a>ジョブ モニターでのジョブ ダイアグラムの表示とデバッグ

Visual Studio Code のジョブ モニターを使用して、Azure Stream Analytics ジョブの表示とトラブルシューティングを行うことができます。

### <a name="view-job-diagram-and-job-summary"></a>ジョブ ダイアグラムとジョブの概要の表示
1. **[Job Monitor]\(ジョブ モニター\)** を選択します。 ジョブ モニターが表示され、ジョブ ダイアグラムが自動的に読み込まれます。
   
   ![ジョブ モニターを開く](./media/vscode-explore-jobs/open-job-monitor.png)

2.  ジョブ ダイアグラムを表示し、 **[ジョブの概要]** をクリックすると、対象のジョブのプロパティと情報が表示されます。 

      ![ジョブの概要を表示する](./media/vscode-explore-jobs/view-jobs-summary.png)

3.  **[接続テスト]** ボタンをクリックすると、対象の入力と出力への接続をテストできます。

      ![接続をテスト](./media/vscode-explore-jobs/test-connection.png)

4.  また、 **[Locate Script]\(スクリプトの検索\)** ボタンをクリックすると、対象のクエリを表示できます。
   
      ![クエリを表示する](./media/vscode-explore-jobs/view-query.png)

### <a name="monitor-and-debug-with-metrics"></a>メトリックを使用した監視とデバッグ

1.  矢印ボタンをクリックすると、[メトリック] パネルが開きます。

      ![[メトリック] パネルを開く](./media/vscode-explore-jobs/open-metrics-panel.png)

2.  これを操作し、グラフに示されている主要なメトリックを使用して、対象のジョブを分析できます。 ジョブレベルのメトリックまたはノード レベルのメトリックを表示できます。 また、グラフに表示するメトリックを選択することもできます。

      ![メトリックを表示する](./media/vscode-explore-jobs/view-metrics.png)

### <a name="debug-with-diagnostic-logs-and-activity-logs"></a>診断ログとアクティビティ ログを使用したデバッグ

トラブルシューティングのために、ジョブの診断ログとアクティビティ ログを表示できます。

1. **[診断ログ]** タブを選択します。

   ![診断ログを表示する](./media/vscode-explore-jobs/view-diagnostic-log.png)

2. **[アクティビティ ログ]** タブを選択します 

   ![アクティビティ ログを表示する](./media/vscode-explore-jobs/view-activity-logs.png)

## <a name="next-steps"></a>次のステップ

* [Visual Studio Code で Azure Stream Analytics クラウド ジョブを作成する (プレビュー)](quick-create-visual-studio-code.md)
