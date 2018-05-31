---
title: Azure Automation の Hybrid Runbook Worker
description: この記事では、ローカル データ センターまたはクラウド プロバイダー内のコンピューターで Runbook を実行できるようにする Azure Automation の機能である Hybrid Runbook Worker のインストールと使用について説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 270990d5dea53c2467bf2d7df4695a14b3dbec8c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34194623"
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する

Azure Automation の Runbook は Azure クラウドで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできないことがあります。 Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。

次の図にこの機能を示します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ

各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。 グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。 グループの各ワーカーは、実行可能なジョブがあるかどうかを確認するために Azure Automation をポーリングします。 ジョブが実行可能な場合、ジョブに最初に到達したワーカーがこれを実行します。 特定のワーカーを指定することはできません。

## <a name="installing-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker をインストールする

Hybrid Runbook worker をインストールするプロセスは OS によって異なります。 次の表には、Hybrid Runbook Worker のインストールに使用できるさまざまな方法へのリンクが含まれています。 Windows Hybrid Runbook Worker は、以下の 2 つの方法でインストール、構成できます。 Automation Runbook を使用して、Windows コンピューターの構成に必要なプロセスを完全に自動化する方法をお勧めします。 2 番目の方法は、ロールを手動でインストールして構成する手順を段階的に追うものです。 Linux マシンでは、コンピューターにエージェントをインストールする Python スクリプトを実行します。

|OS  |デプロイのタイプ  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[手動](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするサーバーの構成を Desired State Configuration (DSC) を使用して管理するには、サーバーを DSC ノードとして追加する必要があります。 DSC による管理のためのサーバーのオンボードの詳細については、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。
>
>[[更新管理ソリューション]](automation-update-management.md) を有効にすると、このソリューションに含まれている Runbook をサポートするために、Log Analytics ワークスペースに接続されたコンピューターはすべて Hybrid Runbook Worker として自動的に構成されます。 ただし、このコンピューターは、Automation アカウントで既に定義した可能性のあるハイブリッド worker グループには登録されません。 このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用していれば、このコンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートできます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。

Hybrid Runbook Worker のデプロイを開始する前に、[ネットワークの計画に関する情報](#network-planning)を確認してください。 Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

## <a name="removing-hybrid-runbook-worker"></a>Hybrid Runbook Worker の削除

要件に応じて、グループから 1 つ以上の Hybrid Runbook Worker を削除したり、グループを削除することができます。 オンプレミス コンピューターから Hybrid Runbook Worker を削除するには、次の手順を実行します。

1. Azure Portal で、Automation アカウントに移動します。
2. **[設定]** ブレードから、**[キー]** を選択し、フィールドの **[URL]** と **[プライマリ アクセス キー]** の値をメモします。 この情報は、次の手順に必要です。

### <a name="windows"></a>Windows

管理者モードで PowerShell セッションを開き、次のコマンドを実行します。 削除処理の詳細なログを取得するには、 **-Verbose** スイッチを使用します。

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

ハイブリッド Worker グループから古いコンピューターを削除するには、省略可能な `machineName` パラメーターを使用します。

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> これによってコンピューターから Microsoft Monitoring Agent が削除されることはありません。Hybrid Runbook Worker ロールの機能と構成のみが削除されます。

## <a name="remove-hybrid-worker-groups"></a>Hybrid Worker グループを削除する

グループを削除するには、まず、先ほどの手順を使用して、グループのメンバーであるすべてのコンピューターから Hybrid Runbook Worker を削除する必要があります。その後、次の手順を実行してグループを削除します。

1. Azure ポータルで Automation アカウントを開きます。
1. **[プロセス オートメーション]** で **[ハイブリッド Worker グループ]** を選択します。 削除するグループを選択します。 特定のグループを選択すると、**[ハイブリッド worker グループ]** プロパティ ページが表示されます。

   ![Hybrid Runbook Worker グループ ページ](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. 選択したグループのプロパティ ページで、**[削除]** をクリックします。 このアクションの確認を求めるメッセージが表示されるので、続行する場合は **[はい]** をクリックします。

   ![グループの削除の確認ダイアログ](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   このプロセスは、完了までに数秒かかる場合があります。進行状況は、メニューの **[通知]** で確認してください。

## <a name="network-planning"></a>ネットワークを構成する

### <a name="hybrid-worker-role"></a>Hybrid Worker ロール

Hybrid Runbook Worker が Log Analytics に接続して登録するには、このセクションで説明されているポート番号と URL へのアクセスが必要です。 これは、Log Analytics に接続するために [Microsoft Monitoring Agent に必要なポートと URL](../log-analytics/log-analytics-agent-windows.md) に加えて必要です。

エージェントと Log Analytics サービスの間の通信にプロキシ サーバーを使用する場合は、適切なリソースにアクセスできることを確認してください。 ファイアウォールを使用してインターネットへのアクセスを制限する場合は、アクセスを許可するようにファイアウォールを構成する必要があります。

Hybrid Runbook Worker ロールが Automation と通信するには、次のポートと URL が必要です。

* ポート: 送信インターネット アクセスには TCP 443 のみが必要です。
* グローバル URL: *.azure-automation.net
* 米国バージニア州 (政府機関向け) のグローバル URL: *.azure automation.us
* エージェント サービス: https://\<workspaceId\>.agentsvc.azure-automation.net

特定のリージョンに対して定義された Automation アカウントがある場合は、そのリージョン データセンターへの通信を制限できます。 次の表は、リージョンごとの DNS レコードを示しています。

| **[リージョン]** | **DNS レコード** |
| --- | --- |
| 米国中西部 | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| 米国中南部 |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| 米国東部 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| カナダ中部 |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| 西ヨーロッパ |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| 北ヨーロッパ |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| 東南アジア |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| インド中部 |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| 東日本 |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| オーストラリア東南部 |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| 英国南部 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| 米国政府バージニア州 | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

リージョン名の代わりにリージョン IP アドレスの一覧を入手するには、Microsoft ダウンロード センターから [Azure データセンター IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653) XML ファイルをダウンロードします。

> [!NOTE]
> Azure データセンター IP アドレス XML ファイルには、Microsoft Azure データセンターで使用されている IP アドレス範囲が一覧表示されています。 このファイルには、計算、SQL、およびストレージの範囲が含まれています。
>
>更新されたファイルが毎週投稿されます。 このファイルには、現在デプロイされている範囲と、次に予定されている IP 範囲の変更が反映されています。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。
>
> 新しい XML ファイルを毎週ダウンロードすることをお勧めします。 その後、Azure で実行されているサービスを正しく識別するようにサイトを更新します。 Azure ExpressRoute ユーザーは、このファイルが、毎月第 1 週に Azure 領域のボーダー ゲートウェイ プロトコル (BGP) アドバタイズを更新するために使用されることに注意してください。

### <a name="update-management"></a>更新管理

更新管理には、Hybrid Runbook Worker に必要となる標準アドレスとポートに加えて、次のアドレスが特に必要となります。 このアドレスへの通信は、ポート 443 を使用して行われます。

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>トラブルシューティング

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うためにエージェントに依存しています。 Windows では、このエージェントは Microsoft Monitoring Agent です。 Linux では OMS Agent for Linux です。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>Hybrid Worker がプロキシまたはファイアウォールの内側にある

コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>Hybrid Worker を実行しているコンピューターが、ハードウェアの最小要件を満たしていない

Hybrid Runbook Worker を実行するコンピューターは、この機能をホストするよう指定する前に、ハードウェアの最小要件を満たしている必要があります。 満たしていない場合、他のバックグラウンド プロセスのリソース使用状況および実行中の Runbook による競合によっては、コンピューターが過負荷になり、Runbook ジョブが遅延またはタイムアウトします。

Hybrid Runbook Worker の機能を実行するように指定されているコンピューターがハードウェアの最小要件を満たしていることを確認します。 満たしている場合は、CPU とメモリの使用状況を監視して、Hybrid Runbook Worker プロセスのパフォーマンスと Windows の間の相関関係を調べます。 メモリまたは CPU に負荷がかかる場合は、リソースのボトルネックを解消してエラーを解決するには、アップグレード、プロセッサの追加、またはメモリの増設が必要である可能性があります。 または、最小要件を満たす異なるコンピューティング リソースを選択し、ワークロードがさらに多くのリソースを必要とすることを示したときは拡張します。

特定の OS でのトラブルシューティングの詳細については、[Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#troubleshooting) または [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#troubleshooting) を参照してください。

## <a name="next-steps"></a>次の手順

「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
