---
title: Change Tracking と Inventory に関する問題のトラブルシューティング
description: Azure Automation Change Tracking および Inventory ソリューションに関する問題をトラブルシューティングして解決する方法について説明します。
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582154"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Change Tracking と Inventory に関する問題のトラブルシューティング

この記事では、Azure Automation の [変更履歴とインベントリ] の問題をトラブルシューティングする方法について説明します。

>[!NOTE]
>この記事は、新しい Azure PowerShell Az モジュールを使用するために更新されました。 AzureRM モジュールはまだ使用でき、少なくとも 2020 年 12 月までは引き続きバグ修正が行われます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)」を参照してください。 Hybrid Runbook Worker での Az モジュールのインストール手順については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)」を参照してください。 Automation アカウントについては、「[Azure Automation の Azure PowerShell モジュールを更新する](../automation-update-azure-modules.md)」に従って、モジュールを最新バージョンに更新できます。

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>シナリオ: Windows マシンに Change Tracking と Inventory のレコードが表示されない

#### <a name="issue"></a>問題

オンボードされている Windows マシンに Change Tracking および Inventory の結果が表示されません。

#### <a name="cause"></a>原因

このエラーには次の原因が考えられます。

* Windows 用 Azure Log Analytics エージェントが実行されていないません。
* Automation アカウントに戻る通信がブロックされています。
* Change Tracking と Inventory 用の管理パックがダウンロードされていません。
* オンボードされている VM の複製元が、Windows 用 Log Analytics エージェントがインストールされた状態で sysprep されなかった複製マシンであった可能性があります。

#### <a name="resolution"></a>解像度

Log Analytics エージェント マシン上で **C:\Program Files\Microsoft Monitoring Agent\Agent\Tools** に移動し、次のコマンドを実行します。

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

それでもサポートが必要な場合は、診断情報を収集して、サポートにお問い合わせください。

> [!NOTE]
> 既定では、Log Analytics エージェントによってエラー トレースが有効になります。 前の例のように詳細なエラー メッセージを有効にするには、`VER` パラメーターを使用します。 情報トレースの場合は、`StartTracing.cmd` を呼び出すときに `INF` を使用します。

##### <a name="log-analytics-agent-for-windows-not-running"></a>Windows 用 Log Analytics エージェントが実行されていない

Windows 用 Log Analytics エージェント (**HealthService.exe**) がマシン上で実行されていることを確認します。

##### <a name="communication-to-automation-account-blocked"></a>Automation アカウントへの通信がブロックされる

マシン上でイベント ビューアーをチェックして、`changetracking` という単語が含まれているイベントを探します。

[変更履歴とインベントリ] が動作するために必要なアドレスとポートについては、「[Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する](../automation-hybrid-runbook-worker.md#network-planning)」を参照してください。

##### <a name="management-packs-not-downloaded"></a>管理パックがダウンロードされていない

Change Tracking とインベントリの次の管理パックがローカルにインストールされていることを確認します。

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>sysprep されていない複製マシンの VM

複製されたイメージを使用する場合は、まずイメージを sysprep し、次に Windows 用 Log Analytics エージェントをインストールします。

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>シナリオ: Linux マシン上に Change Tracking と Inventory の結果がない

#### <a name="issue"></a>問題

ソリューションのためにオンボードされている Linux マシンに対して [変更履歴とインベントリ] の結果が表示されません。 

#### <a name="cause"></a>原因
この問題には次のような原因が考えられます。
* Linux 用 Log Analytics エージェントが実行されていません。
* Linux 用 Log Analytics エージェントが正しく構成されていません。
* ファイルの整合性の監視 (FIM) の競合があります。

#### <a name="resolution"></a>解像度 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Linux 用 Log Analytics エージェントが実行されていない

Linux 用 Log Analytics エージェント (**omsagent**) のデーモンがマシン上で実行されていることを確認します。 自分の Automation アカウントにリンクされた Log Analytics ワークスペースで、次のクエリを実行します。

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

クエリ結果にマシンが表示されない場合は、最近チェックインされていません。 ローカルの構成に問題がある可能性があるため、エージェントを再インストールする必要があります。 インストールと構成の詳細については、「[Log Analytics エージェントを使用してログ データを収集する](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent)」を参照してください。

マシンがクエリ結果に表示される場合は、スコープの構成を確認します。 「[Azure Monitor での監視ソリューションのターゲット設定](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)」を参照してください。

この問題のトラブルシューティングの詳細については、「[問題点: Linux データが表示されない](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data)」を参照してください。

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Linux 用 Log Analytics エージェントが正しく構成されていない

Linux 用 Log Analytics エージェントは、OMS Log Collector ツールを使用したログおよびコマンド ライン出力収集用に正しく構成されていない可能性があります。 [Change Tracking および Inventory ソリューションを使用した環境内の変更の追跡](../change-tracking.md)に関するページを参照してください。

##### <a name="fim-conflicts"></a>FIM の競合

Azure Security Center の FIM 機能で、Linux ファイルの整合性が正しく検証されていない可能性があります。 FIM が動作し、Linux ファイル監視用に正しく構成されていることを確認します。 [Change Tracking および Inventory ソリューションを使用した環境内の変更の追跡](../change-tracking.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

該当する問題がここにない場合、または問題を解決できない場合は、追加のサポートを受けるために、次のいずれかのチャネルをお試しください。

* [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
* [@AzureSupport](https://twitter.com/azuresupport) (カスタマー エクスペリエンスを向上させるための Microsoft Azure の公式アカウント) に連絡する。 Azure サポートにより、Azure コミュニティの回答、サポート、エキスパートと結び付けられます。
* Azure サポート インシデントを送信する。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、 **[サポートの要求]** をクリックします。
