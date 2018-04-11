---
title: Azure Automation の Linux Hybrid Runbook Worker
description: この記事では、Azure Automation Hybrid Runbook Worker をインストールして、ローカル データ センターやクラウド環境にある Linux ベースのコンピューターで Runbook を実行できるようにする方法について説明します。
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: b4559afa9294111eaa1f20fdf295d1fb26dcc994
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="how-to-deploy-a-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のデプロイ方法

Azure Automation の Runbook は Azure クラウドで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできません。 Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。

次の図にこの機能を示します。<br>  

![Hybrid Runbook Worker の概要](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Hybrid Runbook Worker ロールの技術的な概要については、「[Automation アーキテクチャの概要](automation-offering-get-started.md#automation-architecture-overview)」を参照してください。 Hybrid Runbook Worker のデプロイを開始する前に、[ハードウェアとソフトウェアの要件](automation-offering-get-started.md#hybrid-runbook-worker)および[ネットワークを準備するための情報](automation-offering-get-started.md#network-planning)に関する情報を見直します。 Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。     

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ
各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。 グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。 要求を処理するワーカーは、グループのメンバーが決定します。 特定のワーカーを指定することはできません。

## <a name="installing-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のインストール
Linux コンピューターに Hybrid Runbook Worker をインストールして構成するには、ロールを手動でインストールおよび構成するための非常に簡単なプロセスに従います。 **Automation Hybrid Worker** ソリューションを Log Analytics ワークスペースで有効にする必要があります。その後、一連のコマンドを実行してコンピューターを worker として登録し、新規または既存のグループに追加します。 

続行する前に、Automation アカウントがリンクされている Log Analytics ワークスペースだけでなく、Automation アカウントの主キーにも注意する必要があります。 ポータルから、Automation アカウント、ワークスペース ID の**ワークスペース**、および主キーの**キー**を選択することで両方を検索できます。  

1.  Azure で "Automation Hybrid Worker" ソリューションを有効にします。 これは以下のいずれかの方法で実行できます。

   1. 「[Azure Log Analytics 管理ソリューションをワークスペースに追加する](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-add-solutions)」の手順を使用して、**Automation Hybrid Worker** ソリューションをサブスクリプションに追加します。
   2. 次のコマンドレットを実行します。

        ```powershell
         $null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```

2.  次のコマンドを、*-w*、*-k*、*-g*、および *-e* パラメーターの値を変更して実行します。 *-g* パラメーターを、新しい Linux Hybrid Runbook Worker を参加させる Hybrid Runbook Worker グループの名前の値に置き換えます。 この名前がまだ Automation アカウントに存在しない場合は、その名前を持つ新しい Hybrid Runbook Worker グループが作成されます。
    
    ```python
    sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <LogAnalyticsworkspaceId> -k <AutomationSharedKey> -g <hybridgroupname> -e <automationendpoint>
    ```
3. コマンドが完了すると、Azure Portal の [ハイブリッド worker グループ] ブレードに新しいグループとメンバー数が表示されますが、既存のグループの場合はメンバー数が追加されます。 **[ハイブリッド worker グループ]** ブレード上にあるリストからグループを選択し、**[ハイブリッド worker]** タイルを選択できます。 **[ハイブリッド worker]** ブレードで、グループの各メンバーが一覧表示されます。  


## <a name="turning-off-signature-validation"></a>署名の検証をオフにする 
既定では、Linux Hybrid Runbook Worker は、署名の検証を必要とします。 ワーカーに対して未署名の Runbook を実行した場合は、"署名の検証が失敗しました" を含むエラーが表示されます。 署名の検証をオフにするには、2 番目のパラメーターを Log Analytics ワークスペース ID に置き換えて、次のコマンドを実行します。

 ```python
 sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --false <LogAnalyticsworkspaceId>
 ```

## <a name="supported-runbook-types"></a>サポートされている Runbook の種類

Linux Hybrid Runbook Worker は、Azure オートメーション内にある一連の完全な Runbook の種類をサポートしていません。

Linux Hybrid Worker では、次の Runbook の種類が機能します。

* Python 2
* PowerShell
 
Linux Hybrid Worker では、次の Runbook の種類は機能しません。

* PowerShell ワークフロー
* グラフィカル
* グラフィカル PowerShell ワークフロー

## <a name="next-steps"></a>次の手順

* 「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。
* Hybrid Runbook Worker を削除する方法については、「[Remove Azure Automation Hybrid Runbook Workers (Azure Automation の Hybrid Runbook Worker の削除)](automation-remove-hrw.md)」をご覧ください。
