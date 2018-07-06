---
title: Azure 診断ログの Log Analytics へのストリーミング
description: Azure 診断ログを Log Analytics ワークスペースにストリーミングする方法について説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: d8966edb6061ed07f5aecb9682fca081ed589040
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083976"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Azure 診断ログの Log Analytics へのストリーミング

Azure Log Analytics には、ポータル、PowerShell コマンドレット、Azure CLI 2.0 のいずれかの方法を使い、**[Azure 診断ログ](monitoring-overview-of-diagnostic-logs.md)** をほぼリアルタイムでストリーミングすることができます。

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Log Analytics から診断ログを使ってできること

Azure Log Analytics は、ログの検索と分析に対応したフレキシブルなツールです。Azure リソースから生成された生のログ データから洞察を得ることができます。 いくつかの機能の例を次に示します。

* **ログ検索** - ログ データに対する高度なクエリを記述して、各種ソースからのログを相互に関連付けます。また、Azure ダッシュボードにピン留め可能なグラフを生成することもできます。
* **アラート** - 特定のクエリにイベントが一致したことを検出し、メールまたは webhook 呼び出しで通知します。
* **ソリューション** - 既製のビューとダッシュボードを使用して、すぐにログ データから洞察を得ることができます。
* **高度な分析** - 機械学習とパターン マッチング アルゴリズムを適用して、ログから潜在的な問題を特定します。

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Log Analytics への診断ログのストリーミングを有効にする

診断ログのストリーミングは、プログラム、ポータル、または [Azure Monitor REST API](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings) を使用して有効にすることができます。 どの方法でも、Log Analytics ワークスペースと、そこに送信するログのカテゴリおよびメトリックを指定する診断設定を作成します。 診断**ログ カテゴリ**とは、リソースから得られるログの種類です。

設定を構成するユーザーが両方のサブスクリプションに対して適切な RBAC アクセスを持っている限り、Log Analytics ワークスペースはログを出力するリソースと同じサブスクリプションに属している必要はありません。

> [!NOTE]
> 診断設定を使用した多ディメンション メトリックの送信は現在サポートされていません。 ディメンションを含むメトリックは、ディメンション値間で集計され、フラット化された単一ディメンションのメトリックとしてエクスポートされます。
>
> *例*: イベント ハブの "受信メッセージ" メトリックは、キュー単位のレベルで調査およびグラフ化できます。 ただし、診断設定を使用してエクスポートすると、メトリックは、イベント ハブ内のすべてのキューのすべての受信メッセージとして表されます。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>ポータルを使用して診断ログをストリーミングする
1. ポータルで、Azure Monitor に移動し、**[診断設定]** をクリックします。

    ![Azure Monitor の [監視] セクション](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. 必要に応じて、リソース グループまたはリソースの種類で一覧をフィルタリングして、診断設定を行うリソースをクリックします。

3. 選択したリソースの設定が存在しない場合は、設定を作成するように求められます。 [診断を有効にする] をクリックします。

   ![診断設定の追加 - 既存の設定が存在しない](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   リソースに既存の設定が存在する場合は、このリソースで構成済みの設定の一覧が表示されます。 [診断設定の追加] をクリックします。

   ![診断設定の追加 - 既存の設定が存在する](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. 設定に名前を付け、**[Log Analytics への送信]** チェック ボックスをオンにして、Log Analytics ワークスペースを選択します。

   ![診断設定の追加 - 既存の設定が存在する](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. **[Save]** をクリックします。

しばらくすると、このリソースの設定一覧に新しい設定が表示され、新しいイベント データが生成されるとすぐに、診断ログがそのワークスペースにストリーミングされます。 イベントが生成されてから、それが Log Analytics に表示されるまでに 15 分ほどかかる場合があるので注意してください。

### <a name="via-powershell-cmdlets"></a>PowerShell コマンドレットの使用
[Azure PowerShell コマンドレット](insights-powershell-samples.md)を使用してストリーミングを有効にするには、次のパラメーターを指定して `Set-AzureRmDiagnosticSetting` コマンドレットを使用します。

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

workspaceID プロパティに指定するのは、ワークスペースの完全な Azure リソース ID です。Log Analytics ポータルに表示されるワークスペース ID/キーではないので注意してください。

### <a name="via-azure-cli-20"></a>Azure CLI 2.0 の使用

[Azure CLI 2.0](insights-cli-samples.md) を使ってストリーミングを有効にするには、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) コマンドを使います。

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--logs` パラメーターとして渡された JSON 配列にディクショナリを追加することによって、診断ログに他のカテゴリを追加できます。

`--resource-group` 引数は、`--workspace` がオブジェクト ID でない場合にのみ必要です。

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Log Analytics 内のデータを照会する方法

ポータルの [ログ検索] ブレードまたは Log Analytics の機能である [高度な分析] から、Log Management ソリューションの範囲内の診断ログを AzureDiagnostics テーブルで照会することができます。 他にも、[Azure リソース向けのソリューションがいくつか](../log-analytics/log-analytics-add-solutions.md)存在します。こうしたソリューションをインストールすることで、Log Analytics に送信中のログ データからすぐに洞察を得ることができます。

## <a name="next-steps"></a>次の手順

* [Azure 診断ログの詳細を確認する](monitoring-overview-of-diagnostic-logs.md)
