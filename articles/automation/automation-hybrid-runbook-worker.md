---
title: Azure Automation Hybrid Runbook Worker の概要
description: この記事では、ローカル データ センターまたはクラウド プロバイダー内のコンピューターで Runbook を実行できるようにする Hybrid Runbook Worker の概要について説明します。
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 9305d0d6443c923c680af0d5fafc58887dadb902
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835293"
---
# <a name="hybrid-runbook-worker-overview"></a>Hybrid Runbook Worker の概要

Azure Automation の Runbook は Azure クラウド プラットフォームで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできないことがあります。 Azure Automation の Hybrid Runbook Worker 機能を使う、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の割り当て済みコンピューターに配信されます。

次の図にこの機能を示します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/automation.png)

Hybrid Runbook Worker は、Windows または Linux オペレーティング システムのいずれかを実行できます。 監視を行うには、サポートされているオペレーティング システムの、Azure Monitor と Log Analytics エージェントを使用する必要があります。 詳細については、[Azure Monitor](automation-runbook-execution.md#azure-monitor) に関するページをご覧ください。

各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。 グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。 それぞれのマシンでは、1 つのハイブリッド worker レポートを 1 つの Automation アカウントにホストできます。 

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。 グループの各ワーカーは、実行可能なジョブがあるかどうかを確認するために Azure Automation をポーリングします。 ジョブが実行可能な場合、ジョブに最初に到達した worker がこれを実行します。 ジョブ キューの処理時間は、ハイブリッド worker のハードウェア プロファイルと負荷によって異なります。 特定の worker を指定することはできません。 

[Azure サンドボックス](automation-runbook-execution.md#runbook-execution-environment)の代わりに Hybrid Runbook Worker を 使用します。これには、ディスク領域、メモリ、またはネットワーク ソケットに関するサンドボックスの多くの[制限](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)がないためです。 ハイブリッド worker の制限は、ワーカーそのもののリソースにのみ関連します。 

> [!NOTE]
> Hybrid Runbook Worker には、Azure サンドボックスに存在するような[フェア シェア](automation-runbook-execution.md#fair-share)による時間制限はありません。 

## <a name="hybrid-runbook-worker-installation"></a>Hybrid Runbook Worker のインストール

Hybrid Runbook Worker をインストールするプロセスは、オペレーティング システムによって異なります。 次の表に、デプロイのタイプを定義します。

|オペレーティング システム  |デプロイのタイプ  |
|---------|---------|
|Windows     | [自動](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Azure Automation Runbook を使用して、Windows コンピューターを構成するプロセスを完全に自動化するインストール方法をお勧めします。 2 番目の方法は、ロールを手動でインストールして構成するステップごとの手順に従うものです。 Linux マシンでは、コンピューターにエージェントをインストールする Python スクリプトを実行します。

## <a name="network-planning"></a><a name="network-planning"></a>ネットワークの計画

Hybrid Runbook Worker が Azure Automation に接続して登録するには、このセクションで説明されているポート番号と URL へのアクセスが必要です。 worker は、Azure Monitor Log Analytics ワークスペースに接続するために [Log Analytics エージェントに必要なポートと URL](../azure-monitor/platform/agent-windows.md) へのアクセスも必要です。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Hybrid Runbook Worker には、次のポートと URL が必要です。

* ポート: 送信インターネット アクセスには TCP 443 のみが必要です
* グローバル URL: *.azure-automation.net
* 米国バージニア州 (政府機関向け) のグローバル URL: *.azure automation.us
* エージェント サービス: https://\<workspaceId\>.agentsvc.azure-automation.net

[例外](automation-runbook-execution.md#exceptions)を定義するときは、一覧に示されているアドレスを使用することをお勧めします。 IP アドレスについては、[Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=56519)をダウンロードできます。 このファイルは毎週更新され、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。

### <a name="dns-records-per-region"></a>リージョンあたりの DNS レコード数

特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの Hybrid Runbook Worker の通信を制限できます。 次の表は、リージョンごとの DNS レコードを示しています。

| **リージョン** | **DNS レコード** |
| --- | --- |
| オーストラリア中部 |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| オーストラリア東部 |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov バージニア州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| 米国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 米国西部 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

リージョン名の代わりにリージョン IP アドレスの一覧を入手するには、Microsoft ダウンロード センターから [Azure データセンター IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653) XML ファイルをダウンロードします。 更新された IP アドレス ファイルが毎週発表されます。 

IP アドレス ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 計算、SQL、およびストレージの範囲が含まれ、現在デプロイされている範囲と今後変更される IP 範囲が反映されます。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。

新しい IP アドレス ファイルを毎週ダウンロードすることをお勧めします。 その後、Azure で実行されているサービスを正しく識別するようにサイトを更新します。 

> [!NOTE]
> Azure ExpressRoute を使用している場合は、毎月第 1 週に Azure 領域の Border Gateway Protocol (BGP) アドバタイズを更新するために、IP アドレス ファイルが使用されることを覚えておきます。

### <a name="proxy-server-use"></a>プロキシ サーバーの使用

Azure Automation と Log Analytics エージェントの間の通信にプロキシ サーバーを使用する場合は、適切なリソースにアクセスできることを確認してください。 Hybrid Runbook Worker および Automation サービスからの要求のタイムアウトは 30 秒です。 3 回試行した後で、要求は失敗します。 

### <a name="firewall-use"></a>ファイアウォールの使用

ファイアウォールを使用してインターネットへのアクセスを制限する場合は、アクセスを許可するようにファイアウォールを構成する必要があります。 Log Analytics ゲートウェイをプロキシとして使用した場合、Hybrid Runbook Worker 用に構成されていることを確認してください。 [Automation Hybrid Worker に向けた Log Analytics ゲートウェイの構成](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway)に関するセクションを参照してください。

## <a name="update-management-on-hybrid-runbook-worker"></a>Hybrid Runbook Worker の Update Management

Azure Automation [Update Management](automation-update-management.md) を有効にすると、Log Analytics ワークスペースに接続されたコンピューターはすべて Hybrid Runbook Worker として自動的に構成されます。 各 worker は、対象の Runbook を更新の管理でサポートできます。 

このように構成されたコンピューターは、Automation アカウントで既に定義した可能性のある Hybrid Runbook Worker グループには登録されません。 このコンピューターを Hybrid Runbook Worker グループに追加できますが、Update Management と Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用する必要があります。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Hybrid Runbook Worker の Update Management アドレス

Update Management には、Hybrid Runbook Worker に必要となる標準アドレスとポートに加えて、次の表のアドレスが必要です。 これらのアドレスへの通信には、ポート 443 が使用されます。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     | *.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net | *.blob.core.usgovcloudapi.net|

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Azure Automation State Configuration

Hybrid Runbook Worker で [Azure Automation State Configuration](automation-dsc-overview.md) を実行できます。 Hybrid Runbook Worker をサポートするサーバーの構成を管理するには、サーバーを DSC ノードとして追加する必要があります。 [Azure Automation State Configuration による管理のためのマシンの有効化](automation-dsc-onboarding.md)に関するページを参照してください。

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker での Runbook

ローカル コンピューター上のリソースを管理したり、Hybrid Runbook Worker が展開されているローカル環境のリソースに対して実行したりする Runbook があるとします。 この場合は、Automation アカウントではなく、ハイブリッド worker で Runbook を実行できます。 Hybrid Runbook Worker で実行される Runbook は、Automation アカウントで実行する Runbook と同じ構造になります。 詳細については、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照してください。

### <a name="hybrid-runbook-worker-jobs"></a>Hybrid Runbook Worker ジョブ

Hybrid Runbook Worker ジョブは、Windows ではローカルの**システム** アカウントで実行され、Linux では [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) アカウントで実行されます。 Azure Automation による Hybrid Runbook Worker でのジョブの処理は、Azure サンドボックスで実行されるジョブとは若干異なります。 「[Runbook の実行環境](automation-runbook-execution.md#runbook-execution-environment)」を参照してください。

Hybrid Runbook Worker のホスト コンピューターが再起動された場合、実行中の Runbook ジョブは最初から再起動されるか、PowerShell ワークフロー Runbook の最後のチェックポイントから再起動されます。 Runbook ジョブの再起動が 3 回を超えると、そのジョブは中断されます。

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker に対する Runbook のアクセス許可

Hybrid Runbook Worker で実行される Runbook は Azure 以外のリソースにアクセスするため、Azure リソースへの認証に Runbook で通常使用される認証メカニズムを使用できません。 Runbook では、ローカル リソースに対して独自の認証を提供するか、または [Azure リソース用のマネージド ID](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) を使用して認証を構成します。 また、すべての Runbook にユーザー コンテキストを提供する実行アカウントを指定することもできます。

## <a name="next-steps"></a>次のステップ

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。
* Hybrid Runbook Worker をトラブルシューティングする方法については、「[Hybrid Runbook Worker のトラブルシューティング](troubleshoot/hybrid-runbook-worker.md#general)」を参照してください。