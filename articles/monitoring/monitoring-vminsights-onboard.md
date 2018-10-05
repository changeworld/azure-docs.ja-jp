---
title: Azure Monitor for VMs の配布準備 | Microsoft Docs
description: この記事では、分散アプリケーションがどのように実行されているか、および確認されている正常性の問題について理解できるように、Azure Monitor for VMs の配布準備方法および構成方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: 2f0568064eed556429675ffb34c84d588ac670d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47064358"
---
# <a name="how-to-onboard-the-azure-monitor-for-vms"></a>Azure Monitor for VMs を配布準備する方法 
この記事では、お使いの Azure 仮想マシンのオペレーティング システムの正常性を監視し、その仮想マシンでホストされている可能性があるアプリケーションの依存関係を検出してマップするために、Azure Monitor for VMs を設定する方法について説明します。  

Azure Monitor for VMs を有効にするには、次のいずれかの方法を使用して行います。各方法の詳細については、この記事で後述します。  

* VM から直接 **Insights (プレビュー)** を選択することによる単一の Azure 仮想マシン。
* 評価された既存の VM および新しい VM に必須の依存関係がインストールされ、正しく構成されるように、Azure Policy を使用する複数の Azure VM。  準拠していない VM は報告されるため、準拠していないことに基づいて修復方法を決めることができます。  
* PowerShell を使用して指定したサブスクリプションまたはリソース グループにまたがる複数の Azure VM または仮想マシン スケール セット。

## <a name="prerequisites"></a>前提条件
始める前に、次のサブセクションで説明されている次のものがあることを確認してください。

### <a name="log-analytics"></a>Log Analytics 

以下のリージョンでは、Log Analytics ワークスペースが現在サポートされています。

  - 米国中西部  
  - 米国東部  
  - 西ヨーロッパ  
  - 東南アジア<sup>1</sup>  

<sup>1</sup> このリージョンでは、Azure Monitor for VMs の正常性の機能はサポートされていません。   

ワークスペースをお持ちでない場合は、[Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)、[PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json)、[Azure portal](../log-analytics/log-analytics-quick-create-workspace.md) のいずれかを使用して作成できます。  

ソリューションを有効にするには、Log Analytics 共同作成者ロールのメンバーである必要があります。 Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../log-analytics/log-analytics-manage-access.md)」を参照してください。

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

次の表は、VM 用 Azure Monitor でサポートされている Windows および Linux オペレーティング システムの一覧を示しています。  サポートされているメジャーおよびマイナーの Linux OS リリースとカーネル バージョンを詳細に示す完全な一覧は、このセクションの後の方で示されます。

|OS バージョン |[パフォーマンス] |マップ |Health |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | ○ | ○ | ○ |
|Windows Server 2016 | ○ | ○ | ○ |  
|Windows Server 2012 R2 | ○ | ○ | |  
|Windows Server 2012 | ○ | ○ | |  
|Windows Server 2008 R2 | ○ | ○| |  
|RHEL 7、6| ○ | ○| ○ |  
|Ubuntu 18.04、16.04、14.04 | ○ | ○ | ○ |  
|Cent OS Linux 7、6 | ○ | ○ | ○ |  
|SLES 12 | ○ | ○ | ○ |  
|Oracle Linux 7 | X<sup>1</sup> | | ○ |  
|Oracle Linux 6 | ○ | ○ | ○ |  
|Debian 9.4、8 | X<sup>1</sup> | | ○ | 

<sup>1</sup> Azure Monitor for VMs のパフォーマンスの機能は、Azure Monitor からのみ使用できます。Azure VM の左側のウィンドウから直接アクセスした場合には、この機能は使用できません。  

>[!NOTE]
>次の情報は、Linux オペレーティング システムのサポートに適用されます。  
> - 既定と SMP Linux のカーネル リリースのみがサポートされています。  
> - PAE、Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が「2.6.16.21-0.8-xen」であるシステムはサポートされていません。  
> - カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。  
> - CentOS Plus カーネルはサポートされていません。  


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| OS バージョン | カーネル バージョン |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |
| 6.9 | 2.6.32-696 |

#### <a name="ubuntu-server"></a>Ubuntu Server

| OS バージョン | カーネル バージョン |
|:--|:--|
| Ubuntu 18.04 | カーネル 4.15.* |
| Ubuntu 16.04.3 | カーネル 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

#### <a name="oracle-enterprise-linux-6-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel を搭載した Oracle Enterprise Linux 6
| OS バージョン | カーネル バージョン
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-enterprise-linux-5-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel を搭載した Oracle Enterprise Linux 5

| OS バージョン | カーネル バージョン
|:--|:--|
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| OS バージョン | カーネル バージョン
|:--|:--|
|12 SP2 | 4.4.* |
|12 SP3 | 4.4.* |

### <a name="hybrid-environment-connected-sources"></a>ハイブリッド環境に接続されたソース
Azure Monitor for VMs マップは、Microsoft Dependency Agent からデータを取得します。 Dependency Agent は、Log Analytics への接続に関して Log Analytics エージェントに依存しています。 つまり、システムには Log Analytics エージェントをインストールしておき、Dependency Agent を使用して構成する必要があります。  次の表では、ハイブリッド環境でマップ機能がサポートしている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
|:--|:--|:--|
| Windows エージェント | [はい] | [Windows の Log Analytics エージェント](../log-analytics/log-analytics-concept-hybrid.md)に加えて、Windows エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」を参照してください。 |
| Linux エージェント | [はい] | [Linux の Log Analytics エージェント](../log-analytics/log-analytics-concept-hybrid.md)に加えて、Linux エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」を参照してください。 |
| System Center Operations Manager 管理グループ | いいえ  | |  

Windows では、監視データの収集と送信のために System Center Operations Manager と Log Analytics の両方で Microsoft Monitoring Agent (MMA) が使用されます。 System Center Operations Manager と Log Analytics では、エージェントのすぐに使用できるバージョンが異なります。 これらのバージョンはそれぞれ、System Center Operations Manager、Log Analytics、またはその両方にレポートできます。  

Linux では、Linux の Log Analytics エージェントが監視データを収集して Log Analytics に送信します。   

Windows または Linux コンピューターがサービスに直接接続できない場合は、OMS ゲートウェイを使用して Log Analytics に接続するように Log Analytics エージェントを構成する必要があります。 OMS ゲートウェイをデプロイおよび構成する方法の詳細については、「[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って接続する](../log-analytics/log-analytics-oms-gateway.md)」を参照してください。  

Dependency Agent は、次の場所からダウンロードできます。

| ファイル | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.1 | 55030ABF553693D8B5112569FB2F97D7C54B66E9990014FC8CC43EFB70DE56C6 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.1 | 43C75EF0D34471A0CBCE5E396FFEEF4329C9B5517266108FA5D6131A353D29FE |

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供するサービスの品質、セキュリティ、整合性の向上に努めています。 正確で効果的なトラブルシューティング機能を提供するために、マップ機能からのデータにはソフトウェアの構成に関する情報が含まれています。たとえば、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などです。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="performance-counters-enabled"></a>パフォーマンス カウンターの有効化
Azure Monitor for VMs では、ソリューションによって使用されるパフォーマンス カウンターを収集するため、Log Analytics ワークスペースを構成します。  次の表に、60 秒ごとに収集される、ソリューションによって構成されるオブジェクトとカウンターを示します。

### <a name="windows-performance-counters"></a>Windows パフォーマンス カウンター

|オブジェクト名 |カウンター名 |  
|------------|-------------|  
|LogicalDisk |% Free Space |  
|LogicalDisk |Avg.Disk sec/Read |  
|LogicalDisk |Avg.Disk sec/Transfer |  
|LogicalDisk |Avg.Disk sec/Write |  
|LogicalDisk |Disk Bytes/sec |  
|LogicalDisk |Disk Read Bytes/sec  |  
|LogicalDisk |Disk Reads/sec  |  
|LogicalDisk |Disk Transfers/sec |  
|LogicalDisk |Disk Write Bytes/sec |  
|LogicalDisk |Disk Writes/sec |  
|LogicalDisk |Free Megabytes |  
|メモリ |Available MBytes |  
|ネットワーク アダプター |Bytes Received/sec |  
|ネットワーク アダプター |Bytes Sent/sec |  
|プロセッサ |% Processor Time |  

### <a name="linux-performance-counters"></a>Linux パフォーマンス カウンター

|オブジェクト名 |カウンター名 |  
|------------|-------------|  
|論理ディスク |% Used Space |  
|論理ディスク |Disk Read Bytes/sec  |  
|論理ディスク |Disk Reads/sec  |  
|論理ディスク |Disk Transfers/sec |  
|論理ディスク |Disk Write Bytes/sec |  
|論理ディスク |Disk Writes/sec |  
|論理ディスク |Free Megabytes |  
|論理ディスク |Logical Disk Bytes/sec |  
|メモリ |Available MBytes Memory |  
|ネットワーク |Total Bytes Received |  
|ネットワーク |Total Bytes Transmitted |  
|プロセッサ |% Processor Time |  

## <a name="enable-from-the-azure-portal"></a>Azure portal から有効にする
Azure portal でお使いの Azure VM の監視を有効にするには、次の操作を行います。

1. Azure Portal で、**[仮想マシン]** を選択します。 
2. 一覧から VM を選択します。 
3. [VM] ページの **[監視]** セクションで、**[Insights (プレビュー)]** を選択します。
4. **[Insights (プレビュー)]** ページで、**[今すぐ試す]** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](./media/monitoring-vminsights-onboard/enable-vminsights-vm-portal-01.png)

5. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  このリストでは、サブスクリプションで仮想マシンがデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成する場合は、前に示されているサポートされるリージョンのいずれかにある「[Log Analytics ワークスペースの作成](../log-analytics/log-analytics-quick-create-workspace.md)」の手順に従ってください。   

監視を有効にした後、仮想マシンの正常性メトリックが表示されるまで、約 10 分かかる場合があります。 

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](./media/monitoring-vminsights-onboard/onboard-vminsights-vm-portal-status.png)

## <a name="enable-using-azure-policy"></a>Azure Policy を使用して有効にする
複数の Azure VM に対してソリューションを有効にして、一貫性のあるコンプライアンスを保証し、プロビジョニングされた新しい VM に対して自動有効化を確実に実施するには、[Azure Policy](../azure-policy/azure-policy-introduction.md) をお勧めします。  指定されたポリシーを持つ Azure Policy を使用することで、新しい VM に次の利点がもたらされます。

* 定義されたスコープ内で各 VM に対して Azure Monitor for VMs を有効にする
* Log Analytics エージェントをデプロイする 
* Dependency Agent をデプロイして、アプリケーションの依存関係を検出し、マップに表示する
* お使いの Azure VM の OS イメージがポリシー定義の定義済み一覧にある場合に監査する  
* お使いの Azure VM が指定されたワークスペース以外のワークスペースにログ記録している場合に監査する
* コンプライアンス結果を報告する 
* 非準拠の VM の修復をサポートする

お使いのテナントに対して Azure Policy をアクティブ化するには、このプロセスで次のことを行う必要があります。

- ここに記載されている手順を使用して、Log Analytics ワークスペースを構成する
- イニシアティブ定義をご自分のテナントに (管理グループまたはサブスクリプション レベルで) インポートする
- ポリシーを目的のスコープに割り当てる
- コンプライアンス結果を確認する

### <a name="add-the-policies-and-initiative-to-your-subscription"></a>イニシアティブとポリシーをサブスクリプションに追加する
ポリシーを使用するには、提供されている PowerShell スクリプト ([Add-VMInsightsPolicy.ps1](https://www.powershellgallery.com/packages/Add-VMInsightsPolicy/1.2)) を使用することで、このタスクを完了できます。このスクリプトは、Azure PowerShell ギャラリーから入手できます。 このスクリプトにより、ポリシーとイニシアティブがご自分のサブスクリプションに追加されます。  サブスクリプションで Azure Policy を構成するには、次の手順を実行します。 

1. PowerShell スクリプトをローカル ファイル システムにダウンロードします。

2. フォルダー内で次の PowerShell コマンドを使用して、ポリシーを追加します。 このスクリプトは、次の省略可能なパラメーターをサポートしています。 

    ```powershell
    -UseLocalPolicies [<SwitchParameter>]
      <Optional> Load the policies from a local folder instead of https://raw.githubusercontent.com/dougbrad/OnBoardVMInsights/Policy/Policy/

    -SubscriptionId <String>
      <Optional> SubscriptionId to add the Policies/Initiatives to
    -ManagementGroupId <String>
      <Optional> Management Group Id to add the Policies/Initiatives to

    -Approve [<SwitchParameter>]
      <Optional> Gives the approval to add the Policies/Initiatives without any prompt
    ```  

    >[!NOTE]
    >注: イニシアティブ/ポリシーを複数のサブスクリプションに割り当てる場合、この定義を、ポリシーを割り当てるサブスクリプションを含む管理グループに格納する必要があります。 そのためには、-ManagementGroupID パラメーターを使用する必要があります。
    >
   
    パラメーターなしの例: `.\Add-VMInsightsPolicy.ps1`

### <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する
`Add-VMInsightsPolicy.ps1` PowerShell スクリプトを実行すると、次のイニシアティブとポリシーが追加されます。

* **Windows VM への Log Analytics エージェントのデプロイ - プレビュー**
* **Linux VM への Log Analytics エージェントのデプロイ - プレビュー**
* **Windows VM への Dependency Agent のデプロイ - プレビュー**
* **Linux VM への Dependency Agent のデプロイ - プレビュー**
* **Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS) - プレビュー**
* **Audit Dependency Agent のデプロイ - 一覧にない VM イメージ (OS) - プレビュー**

次のイニシアティブ パラメーターが追加されます。

- **Log Analytice ワークスペース** (PowerShell または CLI を使用して割り当てを適用する場合は、ワークスペースの ResourceID を指定する必要があります)

    監査ポリシー **OS スコープ内にない VM** に準拠していないとして検出された VM の場合、デプロイ ポリシーの条件には、よく知られている Azure VM イメージからデプロイされた VM のみが含まれます。 VM の OS がサポートされているかどうか、ドキュメントで確認します。  サポートされていない場合は、デプロイ ポリシーを複製し、イメージがスコープ内になるようにそれを更新または変更する必要があります。

次の省略可能なスタンドアロン ポリシーが追加されます。

- **一致しない Log Analytics ワークスペースのために構成される VM - プレビュー**

    これは、[Log Analytics VM 拡張機能](../virtual-machines/extensions/oms-windows.md)を使用して既に構成されているが、意図していた (ポリシーの割り当てによって示されている) ものとは異なるワークスペースを使用して構成されている VM を識別するために使用できます。 これは WorkspaceID のパラメーターを受け取ります。

この初期リリースでは、Azure portal からポリシーの割り当てのみを作成できます。 これらの手順を完了する方法については、[Azure portal からポリシー割り当ての作成](../azure-policy/assign-policy-definition.md)に関するページを参照してください。

## <a name="enable-with-powershell"></a>PowerShell を使用した有効化
複数の VM または VM スケール セットに対して Azure Monitor for VMs を有効にするには、指定された PowerShell スクリプト ([Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0)) を使用することで、このタスクを完了できます。このスクリプトは、Azure PowerShell ギャラリーから入手できます。  このスクリプトは、サブスクリプション内、または *ResourceGroup* で指定されたスコープ内のリソース グループ内のすべての仮想マシンと VM スケール セット、または *Name* で指定された単一の VM またはスケール セットに対して反復処理を行います。  このスクリプトは、各 VM または VM スケール セットに対して、VM 拡張機能が既にインストールされているかどうか、それを再インストールしようとしていないかどうかを確認します。  それ以外の場合は、Log Analytics と Dependency Agent の VM 拡張機能のインストールに進みます。   

このスクリプトには、Azure PowerShell モジュール バージョン 5.7.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関するページをご覧ください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

スクリプトに関するヘルプを取得するには、`Get-Help` を実行して、引数の詳細と使用例の一覧を取得します。   

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and Dependency Agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VM's and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VM's/VM Scale Sets that will apply to and let you confirm to continue.
    Use -Approve switch to run without prompting, if all required parameters are provided.

    If the extensions are already installed will not install again.
    Use -ReInstall switch if you need to for example update the workspace.

    Use -WhatIf if you would like to see what would happen in terms of installs, what workspace configured to, and status of the extension.


PARAMETERS
    -WorkspaceId <String>
        Log Analytics WorkspaceID (GUID) for the data to be sent to

    -WorkspaceKey <String>
        Log Analytics Workspace primary or secondary key

    -SubscriptionId <String>
        SubscriptionId for the VMs/VM Scale Sets
        If using PolicyAssignmentName parameter, subscription that VM's are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong to

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VM's to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VM's/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Suported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
        For Health supported is: "East US","eastus","West Central US","westcentralus"

    -WhatIf [<SwitchParameter>]
        <Optional> See what would happen in terms of installs.
        If extension is already installed will show what workspace is currently configured, and status of the VM extension

    -Confirm [<SwitchParameter>]
        <Optional> Confirm every action

    <CommonParameters>
        This cmdlet supports the common parameters: Verbose, Debug,
        ErrorAction, ErrorVariable, WarningAction, WarningVariable,
        OutBuffer, PipelineVariable, and OutVariable. For more information, see
        about_CommonParameters (https:/go.microsoft.com/fwlink/?LinkID=113216).

    -------------------------- EXAMPLE 1 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup>

    Install for all VM's in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to ReInstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to ReInstall (move to a new workspace)
```

次の例では、フォルダー内で PowerShell コマンドを使用して Azure Monitor for VMs を有効化して、想定される出力を解釈する方法を示しています。

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VM's or VM ScaleSets matching criteria specified

VM's or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency Agent extensions on above 2 VM's or VM Scale Sets.
VM's in a non-running state will be skipped.
Extension will not be re-installed if already installed. Use -ReInstall if desired, for example to update workspace

Confirm
Continue?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y

db-ws-1 : Deploying DependencyAgentWindows with name DAExtension
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Deploying DependencyAgentWindows with name DAExtension
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Deploying MicrosoftMonitoringAgent with name MMAExtension
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Summary:

Already Onboarded: (0)

Succeeded: (4)
db-ws-1 : Successfully deployed DependencyAgentWindows
db-ws-1 : Successfully deployed MicrosoftMonitoringAgent
db-ws2012 : Successfully deployed DependencyAgentWindows
db-ws2012 : Successfully deployed MicrosoftMonitoringAgent

Connected to different workspace: (0)

Not running - start VM to configure: (0)

Failed: (0)
```

## <a name="enable-for-hybrid-environment"></a>ハイブリッド環境対応にする
このセクションでは、Azure Monitor for VMs による監視のために、データ センターやその他のクラウド環境でホストされている仮想マシンまたは物理コンピューターを配布準備する方法について説明します。  

Azure Monitor for VMs マップの Dependency Agent がデータ自体を送信することはないため、ファイアウォールやポートを変更する必要はありません。 マップ内のデータは、Log Analytics エージェントによって常に Azure Monitor サービスに直接送信されます。または、ご利用の IT セキュリティ ポリシーでネットワーク上のコンピューターがインターネットに接続することが許可されていない場合には、[OMS ゲートウェイ](../log-analytics/log-analytics-oms-gateway.md) を通じて送信されます。

[Log Analytics Linux および Windows エージェント](../log-analytics/log-analytics-concept-hybrid.md)の要件とデプロイ方法を確認します。

手順の要約:

1. Windows または Linux 用の Log Analytics エージェントをインストールする
2. Azure Monitor for VMs マップの Dependency Agent をインストールする
3. パフォーマンス カウンターの収集を有効にする
4. Azure Monitor for VMs の配布準備をする

### <a name="install-the-dependency-agent-on-windows"></a>Windows に Dependency Agent をインストールする 
`InstallDependencyAgent-Windows.exe` を実行することで、Dependency Agent を Windows コンピューターに手動でインストールできます。 オプションを指定せずにこの実行可能ファイルを実行すると、セットアップ ウィザードが起動します。ここで、指示に従って対話形式でインストールを実行できます。  

>[!NOTE]
>エージェントをインストールまたはアンインストールするには、管理者特権が必要です。

次の表に、コマンド ラインからのエージェントのセットアップでサポートされている特定のパラメーターを示します。  

| パラメーター | 説明 |
|:--|:--|
| /? | コマンド ライン オプションの一覧を返します。 |
| /S | ユーザーの操作を必要とせずに、サイレント インストールを実行します。 |

たとえば、`/?` パラメーターを使用してインストール プログラムを実行するには、`InstallDependencyAgent-Windows.exe /?` を入力します。

Windows Dependency Agent のファイルは、`C:\Program Files\Microsoft Dependency Agent` に既定でインストールされます。  セットアップの完了後に Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは `%Programfiles%\Microsoft Dependency Agent\logs` です。 

### <a name="install-the-dependency-agent-on-linux"></a>Linux に Dependency Agent をインストールする
Dependency Agent は、`InstallDependencyAgent-Linux64.bin` (自己解凍バイナリ ファイルを含むシェル スクリプト) によって Linux サーバーにインストールされます。 `sh` を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。

>[!NOTE]
> エージェントをインストールまたは構成するには、ルート アクセスが必要です。
> 

| パラメーター | 説明 |
|:--|:--|
| -help | コマンド ライン オプションの一覧を取得します。 |
| -s | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールしません。 |

たとえば、`-help` パラメーターを使用してインストール プログラムを実行するには、`InstallDependencyAgent-Linux64.bin -help` を入力します。

次のコマンドを実行して、Linux Dependency Agent を root としてインストールします。`sh InstallDependencyAgent-Linux64.bin`
    
Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントでは、ログ ディレクトリは `/var/opt/microsoft/dependency-agent/log` です。

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | Location |
|:--|:--|
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>パフォーマンス カウンターを有効にする
ソリューションによって参照されている Log Analytics ワークスペースが、ソリューションで必要なパフォーマンス カウンターを収集するようにまだ構成されていない場合は、カウンターを有効にする必要があります。 これは、[ここ](../log-analytics/log-analytics-data-sources-performance-counters.md)で説明されているように手動で実行するか、[Azure Powershell ギャラリー](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)から入手可能な PowerShell スクリプトをダウンロードして実行することで行えます。
 
### <a name="onboard-azure-monitor-for-vms"></a>Azure Monitor for VMs の配布準備をする
この方法には、Log Analytics ワークスペースに対してソリューション コンポーネントを有効にする構成を指定する JSON テンプレートが含まれています。  

テンプレートを使用するリソースのデプロイの概念について馴染みがない場合は、以下を参照してください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/resource-group-template-deploy-cli.md) 

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

#### <a name="create-and-execute-a-template"></a>テンプレートを作成して実行する

1. 以下の JSON 構文をコピーして、ファイルに貼り付けます。

    ```json
    {

    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "WorkspaceName": {
            "type": "string"
        },
        "WorkspaceLocation": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-03-15-preview",
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('WorkspaceName')]",
            "location": "[parameters('WorkspaceLocation')]",
            "resources": [
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },

                    "plan": {
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                        "promotionCode": ""
                    }
                },
                {
                    "apiVersion": "2015-11-01-preview",
                    "location": "[parameters('WorkspaceLocation')]",
                    "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                    "type": "Microsoft.OperationsManagement/solutions",
                    "dependsOn": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    ],
                    "properties": {
                        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                    },
                    "plan": {
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "publisher": "Microsoft",
                        "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                        "promotionCode": ""
                    }
                }
            ]
        }
    ]
    ```

2. このファイルを **installsolutionsforvminsights.json** としてローカル フォルダーに保存します。
3. **WorkspaceName**、**ResourceGroupName**、および **WorkspaceLocation** の値を編集します。  **WorkspaceName** の値は、ワークスペース名を含む Log Analytics ワークスペースの完全なリソース ID で、**WorkspaceLocation** の値は、ワークスペースが定義されているリージョンです。
4. これで、次の PowerShell コマンドを使用して、このテンプレートをデプロイする準備ができました。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    provisioningState       : Succeeded
    ```
監視を有効にしてから、ハイブリッド コンピューターの正常性の状態とメトリックが表示されるまで、約 10 分かかる場合があります。 

## <a name="next-steps"></a>次の手順

お使いの仮想マシンに対して監視を有効にすると、Azure Monitor for VMs での分析にこの情報を使用できます。  正常性機能を使用する方法については、[Azure Monitor for VMs の正常性の表示](monitoring-vminsights-health.md)に関するページを参照してください。検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs マップの表示](monitoring-vminsights-maps.md)に関するページを参照してください。  