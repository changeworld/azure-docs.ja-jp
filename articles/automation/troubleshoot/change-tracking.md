---
title: Azure Change Tracking の問題を解決する
description: Azure Automation Change Tracking とインベントリ機能に関する問題をトラブルシューティングして解決する方法について説明します。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198532"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Change Tracking とインベントリのトラブルシューティング

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>シナリオ:Windows マシンの Change Tracking レコードが表示されない

#### <a name="issue"></a>問題

Change Tracking 用にオンボーディングされている Windows マシンの Change Tracking またはインベントリの結果が表示されません。

#### <a name="cause"></a>原因

このエラーには次の原因が考えられます。

* Microsoft Monitoring Agent が動作していません。
* Automation アカウントに戻る通信がブロックされています。
* Change Tracking 用の管理パックがダウンロードされていません。
* オンボードしている VM の複製元が、Microsoft Monitoring Agent がインストールされた状態で sysprep されなかった複製マシンであった可能性があります。

#### <a name="resolution"></a>解決策

ここで説明する解決策で問題を解決できる場合があります。 それでもサポートが必要な場合は、診断情報を収集して、サポートにお問い合わせください。 エージェント マシン上で C:\Program Files\Microsoft Monitoring Agent\Agent\Tools に移動し、次のコマンドを実行します。

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> 既定では、エラー トレースは有効です。 前の例のように詳細なエラー メッセージを有効にするには、*VER* パラメーターを使用します。 情報トレースの場合は、**StartTracing.cmd** を呼び出すときに *INF* を使用します。

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent が動作していない

Microsoft Monitoring Agent (HealthService.exe) がマシン上で実行されていることを確認します。

##### <a name="communication-to-automation-account-blocked"></a>Automation アカウントへの通信がブロックされる

マシン上でイベント ビューアーをチェックして、"changetracking" という単語が含まれているイベントを検索します。

Change Tracking が動作するために必要なアドレスとポートについては、「[Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する](../automation-hybrid-runbook-worker.md#network-planning)」を参照してください。

##### <a name="management-packs-not-downloaded"></a>管理パックがダウンロードされていない

Change Tracking とインベントリの次の管理パックがローカルにインストールされていることを確認します。

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>sysprep されていない複製マシンの VM

複製されたイメージを使用する場合は、まずイメージを sysprep してから Microsoft Monitoring Agent をインストールします。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>シナリオ:Linux マシン上に Change Tracking またはインベントリの結果がない

#### <a name="issue"></a>問題

Change Tracking のためにオンボーディングされている Linux マシンに対してインベントリまたは Change Tracking が表示されません。 

#### <a name="cause"></a>原因
この問題には次のような原因が考えられます。
* Linux 用 Log Analytics エージェントが実行されていません。
* Linux 用 Log Analytics エージェントが正しく構成されていません。
* ファイルの整合性の監視 (FIM) の競合があります。

#### <a name="resolution"></a>解決策 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 用 Log Analytics エージェントが実行されていない

Linux 用 Log Analytics エージェント (omsagent) のデーモンがマシン上で実行されていることを確認します。 自分の Automation アカウントにリンクされた Log Analytics ワークスペースで、次のクエリを実行します。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

クエリ結果にマシンが表示されない場合は、最近チェックインされていません。 ローカルの構成に問題がある可能性があるため、エージェントを再インストールする必要があります。 インストールと構成の詳細については、「[Log Analytics エージェントを使用してログ データを収集する](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)」を参照してください。 

マシンがクエリ結果に表示される場合は、スコープの構成を確認します。 「[Azure Monitor での監視ソリューションのターゲット設定](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)」を参照してください。

この問題のトラブルシューティングの詳細については、「[問題点: Linux データが表示されない](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)」を参照してください。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 用 Log Analytics エージェントが正しく構成されていない

Linux 用 Log Analytics エージェントは、OMS Log Collector ツールを使用したログおよびコマンド ライン出力収集用に正しく構成されていない可能性があります。 「[Change Tracking ソリューションを使用してユーザーの環境内の変更を追跡する](../change-tracking.md)」を参照してください。

##### <a name="fim-conflicts"></a>FIM の競合

Azure Security Center の FIM 機能で、Linux ファイルの整合性が正しく検証されていない可能性があります。 FIM が動作し、Linux ファイル監視用に正しく構成されていることを確認します。 「[Change Tracking ソリューションを使用してユーザーの環境内の変更を追跡する](../change-tracking.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルを使用してサポートを受けてください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
* さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
