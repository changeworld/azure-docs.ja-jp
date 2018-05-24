---
title: Azure Automation の Windows Hybrid Runbook Worker
description: この記事では、お使いのローカル データ センターまたはクラウド 環境内の Windows ベースのコンピューターで Runbook を実行できるようにする Azure Automation Hybrid Runbook Worker のインストールについて説明します。
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 30eda7683a1e8c27eb117b92744bf90eae3fd5d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34193834"
---
# <a name="how-to-deploy-a-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker のデプロイ方法

Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。 この記事では、Windows コンピューターに Hybrid Runbook Worker をインストールする方法について説明します。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker のインストール

Windows Hybrid Runbook Worker は、以下の 2 つの方法でインストール、構成できます。 Automation Runbook を使用して、Windows コンピューターの構成に必要なプロセスを完全に自動化する方法をお勧めします。 2 番目の方法は、ロールを手動でインストールして構成する手順を段階的に追うものです。

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするサーバーの構成を Desired State Configuration (DSC) を使用して管理するには、サーバーを DSC ノードとして追加する必要があります。

Windows Hybrid Runbook Worker の最小要件は次のようになります。

* Windows Server 2012 以降。
* Windows PowerShell 4.0 以降が必要です ([WMF 4.0 をダウンロード](https://www.microsoft.com/download/details.aspx?id=40855))。 確実な Windows PowerShell 5.1 ([WMF 5.1 をダウンロード](https://www.microsoft.com/download/details.aspx?id=54616)) をお勧めします。
* .NET Framework 4.6.2 以降。
* 少なくとも 2 つのコア。
* 少なくとも 4 GB の RAM。
* ポート 443 (送信)

Hybrid Runbook Worker のその他のネットワーク要件については、[ネットワークの構成](automation-hybrid-runbook-worker.md#network-planning)に関するページを参照してください。

DSC による管理のためのサーバーのオンボードの詳細については、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。
[更新管理ソリューション](../operations-management-suite/oms-solution-update-management.md)を有効にすると、このソリューションに含まれている Runbook をサポートするために、Log Analytics ワークスペースに接続された Windows コンピューターはすべて Hybrid Runbook Worker として自動的に構成されます。 ただし、このコンピューターは、Automation アカウントで既に定義した可能性のあるハイブリッド worker グループには登録されません。 このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用していれば、このコンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートできます。 この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。

Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

### <a name="automated-deployment"></a>自動化されたデプロイ

Windows Hybrid Worker ロールのインストールと構成を自動化するには、次の手順を実行します。

1. [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/DisplayScript) の *New-OnPremiseHybridWorker.ps1* スクリプトを Hybrid Runbook Worker ロールを実行しているコンピューターから、または環境内の別のコンピューターから直接ダウンロードし、worker にコピーします。

   *New-OnPremiseHybridWorker.ps1* スクリプトは、実行中に次のパラメーターを必要とします。

   * *AutomationAccountName* (必須) - Automation アカウントの名前。
   * *AAResourceGroupName* (必須) - Automation アカウントに関連付けられているリソース グループの名前。
   * *OMSResourceGroupName* (省略可能) - OMS ワークスペース用のリソース グループの名前。 指定されていない場合は、AAResourceGroupName が使用されます。
   * *HybridGroupName* (必須) - このシナリオをサポートしている Runbook のターゲットとして指定する Hybrid Runbook Worker グループの名前。
   * *SubscriptionID* (必須) - Automation アカウントが存在する Azure サブスクリプション ID。
   * *WorkspaceName* (省略可能) - Log Analytics ワークスペースの名前。 Log Analytics ワークスペースがない場合は、スクリプトがこれを作成して構成します。

     > [!NOTE]
     > 現在、Log Analytics との統合がサポートされている Automation リージョンは、**オーストラリア南東部**、**米国東部 2**、**東南アジア**、および**西ヨーロッパ**だけです。 Automation アカウントがこれらのいずれかのリージョンに存在しない場合、スクリプトは Log Analytics ワークスペースを作成しますが、それらをリンクできないことをユーザーに警告します。

1. コンピューターの**スタート**画面から、管理者モードで **Windows PowerShell** を起動します。
1. PowerShell コマンド ライン シェルから、ダウンロードしたスクリプトを含むフォルダーに移動し、そのスクリプトを実行して *-AutomationAccountName*、*-AAResourceGroupName*、*-OMSResourceGroupName*、*-HybridGroupName*、*-SubscriptionId*、および *-WorkspaceName* パラメーターの値を変更します。

     > [!NOTE]
     > スクリプトの実行後、Azure での認証が求められます。 サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインする**必要があります**。

   ```powershell-interactive
   .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> -AAResourceGroupName <NameofResourceGroup>`
   -OMSResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
   -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfLogAnalyticsWorkspace>
   ```

1. **NuGet** のインストールに同意するように求められ、Azure 資格情報で認証するように求められます。

1. スクリプトが完了したら、[ハイブリッド worker グループ] ページには新しいグループとメンバーの数が表示されますが、既存のグループの場合はメンバーの数がインクリメントされます。 **[ハイブリッド worker グループ]** ページ上にあるリストからグループを選択し、**[ハイブリッド worker]** タイルを選択できます。 **[ハイブリッド worker]** ページで、グループの各メンバーが一覧表示されます。

### <a name="manual-deployment"></a>手動デプロイ

最初の 2 つのステップは Automation 環境に対して 1 回だけ実行し、残りのステップは worker コンピューターごとに繰り返します。

#### <a name="1-create-log-analytics-workspace"></a>1.Log Analytics ワークスペースの作成

Log Analytics ワークスペースがまだない場合は、[ワークスペースの管理](../log-analytics/log-analytics-manage-access.md)に関するページの手順に従って作成します。 既存のワークスペースがある場合は、それを使用できます。

#### <a name="2-add-automation-solution-to-log-analytics-workspace"></a>2.Log Analytics ワークスペースに Automation ソリューションを追加する

ソリューションにより、Log Analytics に機能が追加されます。 Automation ソリューションは、Hybrid Runbook Worker のサポートなど、Azure Automation 用の機能を追加します。 ソリューションをワークスペースに追加すると、次の手順でインストールする worker コンポーネントがエージェント コンピューターに自動的にプッシュダウンされます。

[ソリューション ギャラリーを使用したソリューションの追加](../log-analytics/log-analytics-add-solutions.md)に関するページの手順に従って、Log Analytics ワークスペースに **Automation** ソリューションを追加します。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>手順 3.Microsoft Monitoring Agent をインストールする

Microsoft Monitoring Agent は Log Analytics にコンピューターを接続します。 エージェントをオンプレミスのコンピューターにインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

「[Windows コンピューターを Log Analytics に接続する](../log-analytics/log-analytics-windows-agent.md)」の手順に従って、オンプレミスのコンピューターにエージェントをインストールします。 コンピューターごとにこのプロセスを繰り返して、複数の worker を環境に追加できます。

エージェントが Log Analytics に正常に接続すると、Log Analytics の **[設定]** ページの **[接続されているソース]** タブに表示されます。 C:\Program Files\Microsoft Monitoring Agent\Agent に **AzureAutomationFiles** という名前のフォルダーが作成されていることを調べて、エージェントが Automation ソリューションを正常にダウンロードしたことを確認できます。 Hybrid Runbook Worker のバージョンを確認するには、C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ に移動し、\\*バージョン* サブフォルダーをメモします。

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.Runbook 環境をインストールして、Azure Automation に接続する

エージェントを Log Analytics に追加すると、Automation ソリューションは、**Add-HybridRunbookWorker** コマンドレットを含む **HybridRegistration** PowerShell モジュールをプッシュダウンします。 コンピューターに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

管理者モードで PowerShell セッションを開き、次のコマンドを実行してモジュールをインポートします。

```powershell-interactive
cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
Import-Module HybridRegistration.psd1
```

次に、以下の構文を使用して、 **Add-HybridRunbookWorker** コマンドレットを実行します。

```powershell-interactive
Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>
```

このコマンドレットに必要な情報は、Azure Portal の **[キーの管理]** ページから取得できます。 このページを開くには、Automation アカウントの **[設定]** ページで **[キー]** オプションを選択します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** は、Hybrid Runbook Worker グループの名前です。 Automation アカウントにこのグループが既に存在する場合は、現在のコンピューターがそれに追加されます。 まだ存在しない場合は、追加されます。
* **EndPoint** は、**[キーの管理]** ページの **[URL]** フィールドです。
* **Token** は、**[キーの管理]** ページの **[プライマリ アクセス キー]** です。

インストールに関する詳細な情報を受け取るには、**Add-HybridRunbookWorker** で **-Verbose** スイッチを使用します。

#### <a name="5-install-powershell-modules"></a>5.PowerShell モジュールをインストールする

Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。 これらのモジュールはオンプレミス コンピューターに自動的に配置されないため、手動でインストールする必要があります。 ただし、Azure モジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker 機能の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュールをインストールする必要があります。 Windows PowerShell モジュールのインストールについては、「 [モジュールのインストール](http://msdn.microsoft.com/library/dd878350.aspx) 」を参照してください。 インストールされるモジュールは PSModulePath 環境変数によって参照されている場所に置き、ハイブリッド worker によって自動的にインポートされるようにする必要があります。 詳細については、「[Modifying the PSModulePath Installation Path](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)」(PSModulePath インストール パスの変更) を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

Hybrid Runbook Worker は、Automation アカウントと通信してワーカーの登録、Runbook ジョブの受信、および状態の報告を行うために Microsoft Monitoring Agent に依存しています。 ワーカーの登録に失敗した場合に考えられるエラーの原因を次に示します。

### <a name="the-microsoft-monitoring-agent-is-not-running"></a>Microsoft Monitoring Agent が動作していない

Microsoft Monitoring Agent の Windows サービスが実行されていない場合、Hybrid Runbook Worker は Azure Automation と通信できません。 PowerShell で次のコマンドを入力して、このエージェントが実行されていることを確認します: `Get-Service healthservice`。 サービスが停止している場合は、PowerShell で次のコマンドを入力してサービスを開始します: `Start-Service healthservice`。

### <a name="event-4502-in-operations-manager-log"></a>Operations Manager ログのイベント 4502

**アプリケーションとサービス ログ\Operations Manager** イベント ログに、イベント 4502 と **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** および次の説明を含む EventMessage が表示されます: *サービス \<wsid\>.oms.opinsights.azure.com によって提供された証明書は、Microsoft サービスに使用される証明機関によって発行されていません。TLS/SSL 通信を遮断するプロキシが実行されているかどうかをネットワーク管理者に問い合わせてください。記事 KB3126513 に、接続の問題に関するトラブルシューティング情報が記載されています。*"

これは、プロキシまたはネットワークのファイアウォールが Microsoft Azure への通信をブロックしていることが原因で発生する可能性があります。 コンピューターがポート 443 で *.azure-automation.net に発信アクセスできることを確認します。

ログは、各ハイブリッド worker の C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes にローカルに格納されます。 Azure Automation へのロールのオンボードに影響する接続などの問題や、通常の動作の実行中に発生した問題を示唆する警告やエラー イベントが、**アプリケーションとサービス ログ\Microsoft-SMA\Operations** と**アプリケーションとサービス ログ\Operations Manager** イベント ログに書き込まれているかどうかをチェックできます。

Update Management の問題を解決する方法の追加手順については、[Update Management のトラブルシューティング](automation-update-management.md#troubleshooting) ページを参照してください。

## <a name="next-steps"></a>次の手順

* 「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
* Hybrid Runbook Worker を削除する方法については、「[Remove Azure Automation Hybrid Runbook Workers (Azure Automation の Hybrid Runbook Worker の削除)](automation-hybrid-runbook-worker.md#removing-hybrid-runbook-worker)」をご覧ください。