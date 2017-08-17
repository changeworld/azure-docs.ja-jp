---
title: "Azure Automation の Hybrid Runbook Worker | Microsoft Docs"
description: "この記事では、ローカル データ センターまたはクラウド プロバイダー内のコンピューターで Runbook を実行できるようにする Azure Automation の機能である Hybrid Runbook Worker のインストールと使用について説明します。"
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/05/2017
ms.author: magoedte;bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 783fb22b0154915f2e3d8574ab95538dbd646705
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Hybrid Runbook Worker を使用してデータ センターまたはクラウドのリソースを自動化する
Azure Automation の Runbook は Azure クラウドで実行されるため、他のクラウドやオンプレミス環境のリソースにはアクセスできません。  Azure Automation の Hybrid Runbook Worker 機能を使用すると、ロールをホスティングしているコンピューター上で環境内のリソースに対して Runbook を直接実行して、これらのローカル リソースを管理できます。 Runbook は Azure Automation で格納および管理された後、1 つ以上の指定されたコンピューターに配信されます。  

次の図にこの機能を示します。<br>  

![Hybrid Runbook Worker の概要](media/automation-offering-get-started/automation-infradiagram-networkcomms.png)

Hybrid Runbook Worker ロールの技術的な概要とデプロイの考慮事項については、「[Automation アーキテクチャの概要](automation-offering-get-started.md#automation-architecture-overview)」を参照してください。    

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker のグループ
各 Hybrid Runbook Worker は、エージェントのインストール時に指定する Hybrid Runbook Worker グループのメンバーです。  グループには単一のエージェントを含めることができますが、高可用性グループに複数のエージェントをインストールすることができます。

Hybrid Runbook Worker で Runbook を開始する場合は、実行されるグループを指定します。  要求を処理するワーカーは、グループのメンバーが決定します。  特定のワーカーを指定することはできません。

## <a name="relationship-to-service-management-automation"></a>Service Management Automation との関係
[Service Management Automation (SMA)](https://technet.microsoft.com/library/dn469260.aspx) では、ローカル データ センターの Azure Automation でサポートされているものと同じ Runbook を実行することができます。 Windows Azure Pack には SMA 管理用のグラフィカル インターフェイスが含まれているため、通常 SMA は、Windows Azure パックと一緒にデプロイされます。 Azure Automation とは異なり、SMA には API をホストする Web サーバー、Runbook と SMA の構成を保持するためのデータベース、およびRunbook ジョブを実行する Runbook Worker を含むローカル インストールが必要です。 Azure Automation ではクラウドでこれらのサービスが提供されるため、必要なのはローカル環境での Hybrid Runbook Worker の維持のみです。

既存の SMA ユーザーの場合は、何も変更しなくても Hybrid Runbook Worker で使用する Azure Automation に Runbook を移動できます。ただし、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」の説明に従って、リソースを独自に認証することが前提となります。  SMA の Runbook は、Runbook の認証を提供できるワーカー サーバーのサービス アカウントのコンテキストで実行されます。

Hybrid Runbook Worker 機能を持つ Azure Automation と Service Management Automation のどちらがより要件に合っているかを判断するために、次の条件を使用できます。

* グラフィカル管理インターフェイスが必要な場合、SMA には Windows Azure パックに接続されている、基になるコンポーネントのローカル インストールが必要です。 ローカル Runbook ワーカーにインストールされているエージェントのみを必要とする Azure Automation よりも、ローカル リソースとメンテナンス コストがかかります。 エージェントは Operations Management Suite によって管理され、メンテナンス コストがより削減されます。
* Azure Automation はその Runbook をクラウド内に格納し、オンプレミスの Hybrid Runbook Worker に配信します。 セキュリティ ポリシーでこの動作が許可されていない場合は、SMA を使用する必要があります。
* SMA は System Center に付属するため、System Center 2012 R2 のライセンスが必要です。 Azure Automation は、階層化されたサブスクリプション モデルに基づきます。
* Azure Automation には、SMA で使用できないグラフィカル Runbook などの拡張機能があります。

## <a name="installing-the-windows-hybrid-runbook-worker"></a>Windows Hybrid Runbook Worker のインストール 

Windows Hybrid Runbook Worker は、以下の 2 つの方法でインストール、構成できます。  Automation Runbook を使用して、Windows コンピューターの構成に必要なプロセスを完全に自動化する方法をお勧めします。  2 番目の方法は、ロールを手動でインストールして構成する手順を段階的に追うものです。  

> [!NOTE]
> Hybrid Runbook Worker ロールをサポートするサーバーの構成を Desired State Configuration (DSC) を使用して管理するには、サーバーを DSC ノードとして追加する必要があります。  DSC による管理のためのサーバーのオンボードの詳細については、「[Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」を参照してください。           
><br>
>[更新管理ソリューション](../operations-management-suite/oms-solution-update-management.md)を有効にすると、このソリューションに含まれる Runbook をサポートするために、OMS ワークスペースに接続された Windows コンピューターは Hybrid Runbook Worker として自動的に構成されます。  ただし、このコンピューターは、Automation アカウントで既に定義した可能性のあるハイブリッド worker グループには登録されません。  このソリューションと Hybrid Runbook Worker グループ メンバーシップの両方に同じアカウントを使用していれば、このコンピューターを Automation アカウントの Hybrid Runbook Worker に追加して Automation Runbook をサポートできます。  この機能は、Hybrid Runbook Worker のバージョン 7.2.12024.0 に追加されました。  

Hybrid Runbook Worker のデプロイを開始する前に、[ハードウェアとソフトウェアの要件](automation-offering-get-started.md#hybrid-runbook-worker)および[ネットワークを準備するための情報](automation-offering-get-started.md#network-planning)に関する情報を見直します。  Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。  
 
### <a name="automated-deployment"></a>自動化されたデプロイ

Windows Hybrid Worker ロールのインストールと構成を自動化するには、次の手順を実行します。  

1. [PowerShell ギャラリー](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.0/DisplayScript) の *New-OnPremiseHybridWorker.ps1* スクリプトを Hybrid Runbook Worker ロールを実行しているコンピューターから、または環境内の別のコンピューターから直接ダウンロードし、worker にコピーします。  

    *New-OnPremiseHybridWorker.ps1* スクリプトは、実行中に次のパラメーターを必要とします。

  * *AutomationAccountName* (必須) - Automation アカウントの名前。  
  * *ResourceGroupName* (必須) - Automation アカウントと関連付けられたリソース グループの名前。  
  * *HybridGroupName* (必須) - このシナリオをサポートしている Runbook のターゲットとして指定する Hybrid Runbook Worker グループの名前。 
  *  *SubscriptionID* (必須) - Automation アカウントがある Azure サブスクリプション ID。
  *  *WorkspaceName* (省略可能) - OMS ワークスペースの名前。  OMS ワークスペースがない場合は、スクリプトがこれを作成して構成します。  

     > [!NOTE]
     > OMS と Automation の統合は、現時点では**オーストラリア南東部**、**米国東部 2**、**東南アジア**、および**西ヨーロッパ**の地域でサポートされています。  Automation アカウントがそれらの地域のいずれかにない場合、スクリプトによって OMS ワークスペースが作成されますが、Automation アカウントと OMS ワークスペースのリンクができないと警告されます。
     > 
2. コンピューターの**スタート**画面から、管理者モードで **Windows PowerShell** を起動します。  
3. PowerShell コマンド ライン シェルから、ダウンロードしたスクリプトが含まれているフォルダーに移動し、スクリプトを実行します。その際、*-AutomationAccountName*、*-ResourceGroupName*、*-HybridGroupName*、*-SubscriptionId*、*-WorkspaceName* の各パラメーターの値を変更します。

     > [!NOTE] 
     > スクリプトの実行後、Azure での認証が求められます。  サブスクリプション管理ロールのメンバーかつサブスクリプションの共同管理者であるアカウントを使用してサインインする**必要があります**。  
     >  
    
        .\New-OnPremiseHybridWorker.ps1 -AutomationAccountName <NameofAutomationAccount> `
        -ResourceGroupName <NameofOResourceGroup> -HybridGroupName <NameofHRWGroup> `
        -SubscriptionId <AzureSubscriptionId> -WorkspaceName <NameOfOMSWorkspace>

4. **NuGet** のインストールに同意するように求められ、Azure 資格情報で認証するように求められます。<br><br> ![New-OnPremiseHybridWorker スクリプトの実行](media/automation-hybrid-runbook-worker/new-onpremisehybridworker-scriptoutput.png)

5. スクリプトが完了したら、[ハイブリッド worker グループ] ブレードには新しいグループとメンバーの数が表示されますが、既存のグループの場合はメンバーの数がインクリメントされます。  **[ハイブリッド worker グループ]** ブレード上にあるリストからグループを選択し、**[ハイブリッド worker]** タイルを選択できます。  **[ハイブリッド worker]** ブレードで、グループの各メンバーが一覧表示されます。  

### <a name="manual-deployment"></a>手動デプロイ 
最初の 2 つのステップは Automation 環境に対して 1 回だけ実行し、残りのステップは worker コンピューターごとに繰り返します。

#### <a name="1-create-operations-management-suite-workspace"></a>1.Operations Management Suite のワークスペースを作成する
Operations Management Suite のワークスペースがまだない場合は、[ワークスペースの管理](../log-analytics/log-analytics-manage-access.md)に関するページの手順に従って作成します。 既存のワークスペースがある場合は、それを使用できます。

#### <a name="2-add-automation-solution-to-operations-management-suite-workspace"></a>手順 2.Operations Management Suite ワークスペースに Automation ソリューションを追加します。
ソリューションにより、Operations Management Suite に機能が追加されます。  Automation ソリューションは、Hybrid Runbook Worker のサポートなど、Azure Automation 用の機能を追加します。  ソリューションをワークスペースに追加すると、次の手順でインストールする worker コンポーネントがエージェント コンピューターに自動的にプッシュダウンされます。

「 [To add a solution using the Solutions Gallery (ソリューション ギャラリーを使用してソリューションを追加するには)](../log-analytics/log-analytics-add-solutions.md) 」の説明に従って、Operations Management Suite のワークスペースに **Automation** ソリューションを追加します。

#### <a name="3-install-the-microsoft-monitoring-agent"></a>3.Microsoft Monitoring Agent をインストールする
Microsoft Monitoring Agent は Operations Management Suite にコンピューターを接続します。  エージェントをオンプレミスのコンピューターにインストールし、ワークスペースに接続すると、Hybrid Runbook Worker に必要なコンポーネントが自動的にダウンロードされます。

「[Windows コンピューターを Log Analytics に接続する](../log-analytics/log-analytics-windows-agents.md)」の手順に従って、オンプレミスのコンピューターにエージェントをインストールします。  コンピューターごとにこのプロセスを繰り返して、複数の worker を環境に追加できます。

エージェントが Operations Management Suite に正常に接続すると、Operations Management Suite の **[設定]** ウィンドウの **[接続されているソース]** タブにエージェントが表示されます。  C:\Program Files\Microsoft Monitoring Agent\Agent に **AzureAutomationFiles** という名前のフォルダーが作成されていることを調べて、エージェントが Automation ソリューションを正常にダウンロードしたことを確認できます。  Hybrid Runbook Worker のバージョンを確認するには、C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\ に移動し、\\*バージョン* サブフォルダーをメモします。   

#### <a name="4-install-the-runbook-environment-and-connect-to-azure-automation"></a>4.Runbook 環境をインストールして、Azure Automation に接続する
エージェントを Operations Management Suite に追加すると、Automation ソリューションは、**Add-HybridRunbookWorker** コマンドレットを含む **HybridRegistration** PowerShell モジュールをプッシュダウンします。  コンピューターに Runbook 環境をインストールして、Azure Automation に登録する場合は、このコマンドレットを使用します。

管理者モードで PowerShell セッションを開き、次のコマンドを実行してモジュールをインポートします。

    cd "C:\Program Files\Microsoft Monitoring Agent\Agent\AzureAutomation\<version>\HybridRegistration"
    Import-Module HybridRegistration.psd1

次に、以下の構文を使用して、 **Add-HybridRunbookWorker** コマンドレットを実行します。

    Add-HybridRunbookWorker –GroupName <String> -EndPoint <Url> -Token <String>

このコマンドレットに必要な情報は、Azure ポータルの **[キーの管理]** ブレードから取得できます。  このブレードを開くには、Automation アカウントの **[設定]** ブレードで **[キー]** オプションを選択します。

![Hybrid Runbook Worker の概要](media/automation-hybrid-runbook-worker/elements-panel-keys.png)

* **GroupName** は、Hybrid Runbook Worker グループの名前です。 Automation アカウントにこのグループが既に存在する場合は、現在のコンピューターがそれに追加されます。  まだ存在しない場合は、追加されます。
* **EndPoint** は、**[キーの管理]** ブレードの **[URL]** フィールドです。
* **Token** は、**[キーの管理]** ブレードの **[プライマリ アクセス キー]** です。  

インストールに関する詳細な情報を受け取るには、**Add-HybridRunbookWorker** で **-Verbose** スイッチを使用します。

#### <a name="5-install-powershell-modules"></a>5.PowerShell モジュールをインストールする
Runbook は、Azure Automation 環境にインストールされているモジュールで定義されているアクティビティとコマンドレットをすべて使用できます。  これらのモジュールはオンプレミス コンピューターに自動的に配置されないため、手動でインストールする必要があります。  ただし、Azure モジュールは既定でインストールされ、すべての Azure サービスのコマンドレットと Azure Automation のアクティビティにアクセスできます。

Hybrid Runbook Worker 機能の主な目的はローカル リソースを管理することであるため、ほとんどの場合、これらのリソースをサポートするモジュールをインストールする必要があります。  Windows PowerShell モジュールのインストールについては、「 [モジュールのインストール](http://msdn.microsoft.com/library/dd878350.aspx) 」を参照してください。  インストールされるモジュールは PSModulePath 環境変数によって参照されている場所に置き、ハイブリッド worker によって自動的にインポートされるようにする必要があります。  詳細については、「[Modifying the PSModulePath Installation Path (PSModulePath インストール パスの変更)](https://msdn.microsoft.com/library/dd878326%28v=vs.85%29.aspx)」を参照してください。 

## <a name="installing-linux-hybrid-runbook-worker"></a>Linux Hybrid Runbook Worker のインストール
Linux の Hybrid Runbook Worker は、非常に簡単に手動でインストールし、ロールを構成できます。  **Automation Hybrid Worker** ソリューションを OMS ワークスペースで有効にする必要があります。その後、一連のコマンドを実行してコンピューターを worker として登録し、新規または既存のグループに追加します。 

1.  OMS で "Automation Hybrid Worker" ソリューションを有効にします。 これは以下のいずれかの方法で実行できます。

   1. [OMS ポータル](https://mms.microsoft.com)のソリューション ギャラリーから、**Automation Hybrid Worker** ソリューションを有効にします。
   2. 次のコマンドレットを実行します。

        ```$null = Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName  <ResourceGroupName> -WorkspaceName <WorkspaceName> -IntelligencePackName  "AzureAutomation" -Enabled $true
        ```
2.  Run the following command with the proper parameters (endpoint and key can be taken from the portal from the automation account linked to the workspace used in the steps above):
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/onboarding.py --register -w <OMSworkspaceId> -k <automationsharedkey> --groupname <hybridgroupname> -e <automationendpoint>


## Removing Hybrid Runbook Worker 
You can remove one or more Hybrid Runbook Workers from a group or you can remove the group, depending on your requirements.  To remove a Hybrid Runbook Worker from an on-premises computer, perform the following steps.

1. In the Azure portal, navigate to your Automation account.  
2. From the **Settings** blade, select **Keys** and note the values for field **URL** and **Primary Access Key**.  You need this information for the next step.
3. Open a PowerShell session in Administrator mode and run the following command - `Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>`.  Use the **-Verbose** switch for a detailed log of the removal process.

> [!NOTE]
> This does not remove the Microsoft Monitoring Agent from the computer, only the functionality and configuration of the Hybrid Runbook Worker role.  

## Remove Hybrid Worker groups
To remove a group, you first need to remove the Hybrid Runbook Worker from every computer that is a member of the group using the procedure shown earlier, and then you perform the following steps to remove the group.  

1. Open the Automation account in the Azure portal.
2. Select the **Hybrid Worker Groups** tile and in the **Hybrid Worker Groups** blade, select the group you wish to delete.  After selecting the specific group, the **Hybrid worker group** properties blade is displayed.<br> ![Hybrid Runbook Worker Group Blade](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)   
3. On the properties blade for the selected group, click **Delete**.  A message appears asking you to confirm this action, select **Yes** if you are sure you want to proceed.<br> ![Delete Group Confirmation Dialog](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)<br> This process can take several seconds to complete and you can track its progress under **Notifications** from the menu.  

## Troubleshooting 
The Hybrid Runbook Worker depends on the Microsoft Monitoring Agent to communicate with your Automation account to register the worker, receive runbook jobs, and report status. If  registration of the worker fails, here are some possible causes for the error:  

1. The hybrid worker is behind a proxy or firewall.  
    Verify the computer has outbound access to *.azure-automation.net on port 443.  

2. The computer the hybrid worker is running on has less than the minimum hardware [requirements](automation-offering-get-started.md#hybrid-runbook-worker).  
    Computers running the Hybrid Runbook Worker should meet the minimum hardware requirements before designating it to host this feature. Otherwise, depending on the resource utilization of other background processes and contention caused by runbooks during execution, the computer will become over utilized and cause runbook job delays or timeouts.
    Confirm the computer designated to run the Hybrid Runbook Worker feature meets the minimum hardware requirements.  If it does, monitor CPU and memory utilization to determine any correlation between the performance of Hybrid Runbook Worker processes and Windows.  If there is memory or CPU pressure, this may indicate the need to upgrade or add additional processors, or increase memory to address the resource bottleneck and resolve the error. Alternatively, select a different compute resource that can support the minimum requirements and scale when workload demands indicate an increase is necessary.
    
3. The Microsoft Monitoring Agent service is not running.  
    If the Microsoft Monitoring Agent Windows service is not running, this prevents the Hybrid Runbook Worker from communicating with Azure Automation.  Verify the agent is running by entering the following command in PowerShell: `get-service healthservice`.  If the service is stopped, enter the following command in PowerShell to start the service: `start-service healthservice`.  

4. In the **Application and Services Logs\Operations Manager** event log, you see event 4502  and EventMessage containing **Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent** with the following description:  *The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Please contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication. The article KB3126513 has additional troubleshooting information for connectivity issues.*
    This can be caused by your proxy or network firewall blockking communication to Microsoft Azure.  Verify the computer has outbound access to *.azure-automation.net on ports 443.

Logs are stored locally on each hybrid worker at C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  You can check if there are any warning or error events written to the **Application and Services Logs\Microsoft-SMA\Operations** and **Application and Services Logs\Operations Manager** event log that would indicate a connectivity or other issue affecting onboarding of the role to Azure Automation or issue while performing normal operations.  

## Next steps
Review [run runbooks on a Hybrid Runbook Worker](automation-hrw-run-runbooks.md) to learn how to configure your runbooks to automate processes in your on-premises datacenter or other cloud environment.

