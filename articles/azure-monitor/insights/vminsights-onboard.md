---
title: Azure Monitor for VMs プレビューをデプロイする | Microsoft Docs
description: この記事では、分散アプリケーションがどのように実行されているか、および確認されている正常性の問題について理解できるように、Azure Monitor for VMs をデプロイして構成する方法について説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/07/2018
ms.author: magoedte
ms.openlocfilehash: 741288bd1a927b12705b3b31c5a1c60d6b94db5b
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53194251"
---
# <a name="deploy-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs プレビューをデプロイする
この記事では、Azure Monitor for VMs の設定方法について説明します。 お使いの Azure 仮想マシン (VM) と仮想マシン スケール セットおよび環境内の仮想マシンのオペレーティング システムの正常性を監視するサービスです。 この監視には、それらでホストされている可能性があるアプリケーションの依存関係の検出とマッピングが含まれます。 

Azure Monitor for VMs を有効にするには、次のいずれかの方法を使用します。  

* VM から直接 **Insights (プレビュー)** を選択することにより、単一の Azure 仮想マシンを有効にします。
* Azure Policy を使用して、複数の Azure VM を有効にします。 この方法を使用すると、既存 VM および新規 VM の必要な依存関係がインストールされて、適切に構成されます。 準拠していない VM が報告されるので、それらを有効にするかどうか、およびそれらを修復する方法を決めることができます。 
* PowerShell を使用して、指定したサブスクリプションまたはリソース グループ全体の複数の Azure VM または仮想マシン スケール セットを有効にします。

各方法についての他の情報は、記事の後半で提供します。

## <a name="prerequisites"></a>前提条件
始める前に、次のセクションの情報を理解しておいてださい。

### <a name="log-analytics"></a>Log Analytics 

Log Analytics ワークスペースが現在サポートされているのは、以下のリージョンです。

  - 米国中西部  
  - 米国東部  
  - 西ヨーロッパ  
  - 東南アジア<sup>1</sup>  

<sup>1</sup> このリージョンでは、Azure Monitor for VMs の正常性の機能はサポートされていません。   

>[!NOTE]
>Azure 仮想マシンは、任意のリージョンからデプロイできます。Log Analytics ワークスペースのサポートされるリージョンに制限されることはありません。
>

ワークスペースがない場合は、次のいずれかの方法で作成できます。
* [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)
* [PowerShell](../../azure-monitor/learn/quick-create-workspace-posh.md)
* [Azure ポータル](../../azure-monitor/learn/quick-create-workspace.md)
* [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md) 

Azure portal で単一の Azure VM に対する監視を有効にする場合は、このプロセスの間にワークスペースを作成できます。 

大規模シナリオに対するソリューションを有効にするには、最初に Log Analytics ワークスペースで以下を構成します。

* ServiceMap および InfrastructureInsights ソリューションをインストールします。 この記事で提供されている Azure Resource Manager テンプレートを使用することによってのみ、このインストールを完了できます。  
* パフォーマンス カウンターを収集するように Log Analytics ワークスペースを構成します。

大規模なシナリオ向けにワークスペースを構成するには、[大規模なデプロイのための Log Analytics ワークスペースの設定](#setup-log-analytics-workspace)に関するセクションを参照してください。

### <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

次の表は、VM 用 Azure Monitor でサポートされている Windows および Linux オペレーティング システムの一覧を示しています。 メジャーおよびマイナーの Linux OS リリースとサポートされているカーネルのバージョンの詳細な一覧は、このセクションの後の方で示されます。

|OS バージョン |[パフォーマンス] |マップ |Health |  
|-----------|------------|-----|-------|  
|Windows Server 2016 1803 | X | X | X |
|Windows Server 2016 | X | X | X |  
|Windows Server 2012 R2 | X | X | |  
|Windows Server 2012 | X | X | |  
|Windows Server 2008 R2 | X | X| |  
|Red Hat Enterprise Linux (RHEL) 7、6| X | X| X |  
|Ubuntu 18.04、16.04、14.04 | X | X | X |  
|CentOS Linux 7、6 | X | X | X |  
|SUSE Linux Enterprise Server (SLES) 12 | X | X | X |  
|Oracle Linux 7 | X<sup>1</sup> | | X |  
|Oracle Linux 6 | X | X | X |  
|Debian 9.4、8 | X<sup>1</sup> | | X | 

<sup>1</sup> Azure Monitor for VMs のパフォーマンス機能は、Azure Monitor からのみ使用できます。 Azure VM の左側のウィンドウから直接アクセスしたときには使用できません。 

>[!NOTE]
>次の情報は、Linux オペレーティング システムのサポートに適用されます。  
> - 既定と SMP Linux のカーネル リリースのみがサポートされています。 
> - Physical Address Extension (PAE) や Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が *2.6.16.21-0.8-xen* であるシステムはサポートされていません。 
> - カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。 
> - CentOSPlus カーネルはサポートされていません。 


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

### <a name="the-microsoft-dependency-agent"></a>Microsoft Dependency Agent
Azure Monitor for VMs のマップ機能では、Microsoft Dependency Agent からデータが取得されます。 Dependency Agent は、Log Analytics への接続に関して Log Analytics エージェントに依存しています。 したがって、お使いのシステムに Log Analytics エージェントをインストールし、Dependency Agent を使用して構成する必要があります。 

単一の Azure VM に対して Azure Monitor for VMs を有効にするか、または大規模なデプロイ方法を使用するかにかかわらず、Azure VM Dependency Agent 拡張機能を使用し、エクスペリエンスの一部としてエージェントをインストールする必要があります。 

ハイブリッド環境では、次の 2 つの方法のいずれかで Dependency Agent をダウンロードしてインストールできます。手動で行うか、または Azure の外部でホストされている仮想マシンに対する自動デプロイ方法を使用します。 

次の表では、ハイブリッド環境でマップ機能がサポートしている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
|:--|:--|:--|
| Windows エージェント | [はい] | [Windows の Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)に加えて、Windows エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの完全な一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| Linux エージェント | [はい] | [Linux の Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)に加えて、Linux エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの完全な一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| System Center Operations Manager 管理グループ | いいえ  | |  

Dependency Agent は、次の場所からダウンロードできます。

| ファイル | OS | Version | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) |  Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) |  Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |

## <a name="role-based-access-control"></a>ロールベースのアクセス制御
Azure Monitor for VMs の機能を有効にしてアクセスするには、次のアクセス ロールが割り当てられている必要があります。 
  
- ソリューションを有効にするには、"*Log Analytics 共同作成者*" ロールが必要です。 

- パフォーマンス、正常性、マップのデータを表示するには、Azure VM に対する "*監視閲覧者*" ロールが必要です。 Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する必要があります。  

Log Analytics ワークスペースへのアクセスを制御する方法の詳細については、「[ワークスペースを管理する](../../azure-monitor/platform/manage-access.md)」を参照してください。

## <a name="enable-monitoring-in-the-azure-portal"></a>Azure Portal で監視を有効にする
Azure portal でお使いの Azure VM の監視を有効にするには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. **[仮想マシン]** を選択します。 
1. 一覧から VM を選択します。 
1. [VM] ページの **[監視]** セクションで、**[Insights (プレビュー)]** を選択します。
1. **[Insights (プレビュー)]** ページで、**[今すぐ試す]** を選択します。

    ![VM に対して Azure Monitor for VMs を有効にする](./media/vminsights-onboard/enable-vminsights-vm-portal-01.png)

1. **[Azure Monitor Insights Onboarding]\(Azure Monitor Insights の配布準備\)** ページで、同じサブスクリプションに既存の Log Analytics ワークスペースがある場合は、ドロップダウン リストでそれを選択します。  
    このリストでは、サブスクリプションで仮想マシンがデプロイされている既定のワークスペースと場所が事前に選択されています。 

    >[!NOTE]
    >VM からの監視データを格納するための新しい Log Analytics ワークスペースを作成する場合は、前に示されているサポートされるリージョンのいずれかにある「[Log Analytics ワークスペースの作成](../../azure-monitor/learn/quick-create-workspace.md)」の手順に従ってください。  

監視を有効にした後、仮想マシンの正常性メトリックが表示されるまで、約 10 分かかる場合があります。 

![Azure Monitor for VMs の監視デプロイ プロセスを有効にする](./media/vminsights-onboard/onboard-vminsights-vm-portal-status.png)


## <a name="deploy-at-scale"></a>大規模にデプロイする
このセクションでは、Azure Policy または Azure PowerShell を使用して大規模に Azure Monitor for VMs をデプロイします。 

VM をデプロイする前に、以下のようにして Log Analytics ワークスペースを事前構成します。

1. ワークスペースがまだない場合は、Azure Monitor for VMs をサポートできるワークスペースを作成します。  
    先に進む前に「[ワークスペースを管理する](../../log-analytics/log-analytics-manage-access.md?toc=/azure/azure-monitor/toc.json)」を参照して、コスト、管理、およびコンプライアンスに関する考慮事項を理解してください。      

1. Azure Monitor for VMs をサポートするために使用できる新しいワークスペースがまだ存在しない場合は作成します。 新しいワークスペースを作成する前に「[ワークスペースを管理する](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json)」を読み、コスト、管理、およびコンプライアンスの考慮事項を理解してから次に進んでください。       

1. Linux および Windows VM 上のコレクションのために、ワークスペースのパフォーマンス カウンターを有効にします。

1. ワークスペースに ServiceMap および InfrastructureInsights ソリューションをインストールして有効にします。 

### <a name="set-up-a-log-analytics-workspace"></a>Log Analytics ワークスペースを設定する
Log Analytics ワークスペースがない場合は、「[前提条件](#log-analytics)」セクションで推奨されている方法を確認してワークスペースを作成します。 

#### <a name="enable-performance-counters"></a>パフォーマンス カウンターを有効にする
ソリューションによって参照されている Log Analytics ワークスペースが、ソリューションで必要なパフォーマンス カウンターを収集するようにまだ構成されていない場合は、カウンターを有効にする必要があります。 次の 2 つの方法のいずれかで行うことができます。
* 「[Log Analytics での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/platform/data-sources-performance-counters.md)」の説明に従って、手動で行います
* [Azure PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)で入手できる PowerShell スクリプトをダウンロードして実行します
 
#### <a name="install-the-servicemap-and-infrastructureinsights-solutions"></a>ServiceMap および InfrastructureInsights ソリューションをインストールする
この方法には、Log Analytics ワークスペースでソリューション コンポーネントを有効にするための構成を指定する JSON テンプレートが含まれています。 

テンプレートを使用してリソースをデプロイすることに慣れていない場合は、以下をご覧ください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

Azure CLI を使用する場合は、まず、ローカルに CLI をインストールして使用する必要があります。 Azure CLI バージョン 2.0.27 以降を実行する必要があります。 ご利用のバージョンを識別するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)に関するページを参照してください。 

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

1. このファイルを *installsolutionsforvminsights.json* としてローカル フォルダーに保存します。
1. *WorkspaceName*、*ResourceGroupName*、および *WorkspaceLocation* の値を編集します。 *WorkspaceName* の値は、ワークスペース名を含む Log Analytics ワークスペースの完全なリソース ID です。 *WorkspaceLocation* の値は、ワークスペースが定義されているリージョンです。
1. これで、次の PowerShell コマンドを使用して、このテンプレートをデプロイする準備ができました。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    provisioningState       : Succeeded
    ```

### <a name="enable-by-using-azure-policy"></a>Azure Policy を使用して有効にする
新しくプロビジョニングされる VM の一貫したコンプライアンスと自動的な有効化が保証される方法で大規模に Azure Monitor for VMs を有効にするには、[Azure Policy](../../azure-policy/azure-policy-introduction.md) をお勧めします。 該当するポリシーを以下に示します。

* Log Analytics エージェントと Dependency Agent をデプロイします。 
* コンプライアンスの結果を報告します。 
* 準拠していない VM を修復します。

テナントで Azure Policy を使用して Azure Monitor for VMs を有効にするには: 

- スコープにイニシアティブを割り当てる: 管理グループ、サブスクリプション、またはリソース グループ 
- コンプライアンスの結果をレビューおよび修復する  

続ける前に、Azure Policy の割り当ての詳細について、[Azure Policy の概要](../../governance/policy/overview.md#policy-assignment)および[管理グループの概要](../../governance/management-groups/index.md)に関する記事をご覧ください。 

次の表では、ポリシーの定義の一覧を示します。 

|Name |説明 |type |  
|-----|------------|-----|  
|[プレビュー]:Azure Monitor for VMs の有効化 |指定されたスコープ (管理グループ、サブスクリプション、またはリソース グループ) で、Azure Monitor for VMs を有効にします。 Log Analytics ワークスペースをパラメーターとして受け取ります。 |イニシアティブ |  
|[プレビュー]:Audit Dependency Agent のデプロイ - 一覧にない VM イメージ (OS) |一覧で VM イメージ (OS) が定義されておらず、エージェントがインストールされていない場合は、VM を非準拠として報告します。 |ポリシー |  
|[プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS) |一覧で VM イメージ (OS) が定義されておらず、エージェントがインストールされていない場合は、VM を非準拠として報告します。 |ポリシー |  
|[プレビュー]:Linux VM への Dependency Agent のデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Linux VM に Dependency Agent をデプロイします。 |ポリシー |  
|[プレビュー]:Windows VM への Dependency Agent のデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Windows VM に Dependency Agent をデプロイします。 |ポリシー |  
|[プレビュー]:Linux VM への Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Linux VM に Log Analytics エージェントをデプロイします。 |ポリシー |  
|[プレビュー]:Windows VM への Log Analytics エージェントのデプロイ |VM イメージ (OS) が一覧で定義されており、エージェントがインストールされていない場合は、Windows VM に Log Analytics エージェントをデプロイします。 |ポリシー |  

ここではスタンドアロン ポリシー (イニシアティブには含まれません) について説明します。 

|Name |説明 |type |  
|-----|------------|-----|  
|[プレビュー]:VM 用 Audit Log Analytics ワークスペース - 不一致の報告 |ポリシー/イニシアティブの割り当てで指定された Log Analytics ワークスペースにログインしていない場合、VM を非準拠として報告します。 |ポリシー |

#### <a name="assign-the-azure-monitor-initiative"></a>Azure Monitor のイニシアティブを割り当てる
この初期リリースでは、Azure portal においてのみポリシーの割り当てを作成できます。 これらの手順を完了する方法については、 [Azure portal でのポリシー割り当ての作成](../../governance/policy/assign-policy-portal.md)に関する記事を参照してください。 

1. Azure portal で Azure Policy サービスを起動するには、**[すべてのサービス]** を選択し、**[ポリシー]** を探して選択します。 
1. Azure Policy ページの左側のウィンドウで、**[割り当て]** を選択します。  
    割り当ては、特定のスコープ内で実行するように割り当てられたポリシーです。
1. **[ポリシー - 割り当て]** ページの上部で、**[イニシアティブの割り当て]** を選択します。
1. **[イニシアティブの割り当て]** ページで、省略記号 [...] をクリックして **[スコープ]** を選択し、管理グループまたはサブスクリプションを選択します。  
    この例では、スコープにより、ポリシーの割り当てが仮想マシンのグループに制限されて実施されます。
1. **[スコープ]** ページの下部にある **[選択]** を選択して、変更を保存します。
1. (省略可能) スコープから 1 つまたは複数のリソースを削除するには、**[除外]** を選択します。 
1. **[イニシアティブ定義]** の省略記号 [...] を選択して、使用可能な定義の一覧を開きます。次に、**[[プレビュー]: VM 用 Azure Monitor を有効にする]** を選択して、**[選択]** を選択します。  
    **[割り当て名]** ボックスには選択したイニシアティブ名が自動的に入力されますが、この名前は変更できます。 必要に応じて、説明を追加することもできます。 **[割り当て担当者]** ボックスは、ログインしたユーザーに基づいて自動的に入力されます。この値は省略可能です。
1. サポートされているリージョンの **[Log Analytics ワークスペース]** ドロップダウン リストで、ワークスペースを選択します。

    >[!NOTE]
    >ワークスペースが割り当てのスコープの範囲外にある場合は、ポリシー割り当てのプリンシパル ID に "*Log Analytics 共同作成者*" アクセス許可を付与します。 そうしないと、次のようなデプロイ エラーが表示される可能性があります。`The client '343de0fe-e724-46b8-b1fb-97090f7054ed' with object id '343de0fe-e724-46b8-b1fb-97090f7054ed' does not have authorization to perform action 'microsoft.operationalinsights/workspaces/read' over scope ... ` アクセス権の付与については、[マネージド ID を手動で構成する方法](../../governance/policy/how-to/remediate-resources.md#manually-configure-the-managed-identity)に関する記事をご覧ください。
    >  
    割り当てられるイニシアティブには *deployIfNotExists* 効果のあるポリシーが含まれているため、**[マネージド ID]** チェック ボックスがオンになります。 
1. **[マネージド ID の場所]** ドロップダウン リストで、適切なリージョンを選択します。 
1. **[割り当て]** を選択します。

#### <a name="review-and-remediate-the-compliance-results"></a>コンプライアンスの結果をレビューおよび修復する 

コンプライアンスの結果をレビューする方法については、[非準拠の結果の確認](../../governance/policy/assign-policy-portal.md#identify-non-compliant-resources)に関する記事を参照してください。 左側のウィンドウで、**[コンプライアンス]** を選択します。次に、作成した割り当てに従って準拠していない VM の **[[プレビュー]: VM 用 Azure Monitor を有効にする]** イニシアティブを探します。

![Azure VM のポリシーへの準拠](./media/vminsights-onboard/policy-view-compliance-01.png)

以下のシナリオでは、イニシアティブに含まれるポリシーの結果に基づいて、VM が非準拠として報告されます。  
  
* Log Analytics も Dependency Agent もデプロイされていない。 
   このシナリオは、既存の VM を含むスコープでよくみられます。 これを軽減するには、非準拠ポリシーで[修復タスクを作成する](../../governance/policy/how-to/remediate-resources.md)ことにより、必要なエージェントをデプロイします。   
 
    - [プレビュー]: Deploy Dependency Agent for Linux VMs   
    - [プレビュー]: Deploy Dependency Agent for Windows VMs  
    - [プレビュー]: Deploy Log Analytics Agent for Linux VMs  
    - [プレビュー]: Deploy Log Analytics Agent for Windows VMs  

* VM イメージ (OS) が、ポリシー定義で示されていない。 
   デプロイ ポリシーの基準には、よく知られている Azure VM イメージからデプロイされた VM のみが含まれます。 VM の OS がサポートされているかどうか、ドキュメントで確認します。 サポートされていない場合は、デプロイ ポリシーを複製し、イメージが準拠するようにそれを更新または変更します。 
  
    - [プレビュー]:Audit Dependency Agent のデプロイ - 一覧にない VM イメージ (OS)  
    - [プレビュー]:Audit Log Analytics エージェントのデプロイ - 一覧にない VM イメージ (OS)

* VM が、指定された Log Analytics ワークスペースにログインしていない。  
    イニシアティブ スコープ内の一部の VM が、ポリシー割り当てで指定されているもの以外の Log Analytics ワークスペースにログインしている可能性があります。 このポリシーは、非準拠のワークスペースに報告を行っている VM を識別するための手段となります。 
 
    - [プレビュー]: Audit Log Analytics Workspace for VM - Report Mismatch  

### <a name="enable-with-powershell"></a>PowerShell を使用した有効化
複数の VM または仮想マシン スケール セットに対して Azure Monitor for VMs を有効にするには、Azure PowerShell ギャラリーから入手できる PowerShell スクリプトの [Install-VMInsights.ps1](https://www.powershellgallery.com/packages/Install-VMInsights/1.0) を使用できます。 このスクリプトでは、サブスクリプション内、または *ResourceGroup* で指定されたスコープ内のリソース グループ内のすべての仮想マシンと仮想マシン スケール セット、または *Name* で指定された単一の VM または仮想マシン スケール セットに対して反復処理が実行されます。 このスクリプトでは、各 VM または仮想マシン スケール セットに対して、VM 拡張機能が既にインストールされているかどうかが確認されます。 VM 拡張機能がインストールされていない場合、スクリプトで再インストールが試みられます。 VM 拡張機能がインストールされている場合、スクリプトでは Log Analytics と Dependency Agent の VM 拡張機能がインストールされます。  

このスクリプトには、Azure PowerShell モジュール バージョン 5.7.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

スクリプトの引数の詳細と使用例の一覧を取得するには、`Get-Help` を実行します。  

```powershell
Get-Help .\Install-VMInsights.ps1 -Detailed

SYNOPSIS
    This script installs VM extensions for Log Analytics and the Dependency agent as needed for VM Insights.


SYNTAX
    .\Install-VMInsights.ps1 [-WorkspaceId] <String> [-WorkspaceKey] <String> [-SubscriptionId] <String> [[-ResourceGroup]
    <String>] [[-Name] <String>] [[-PolicyAssignmentName] <String>] [-ReInstall] [-TriggerVmssManualVMUpdate] [-Approve] [-WorkspaceRegion] <String>
    [-WhatIf] [-Confirm] [<CommonParameters>]


DESCRIPTION
    This script installs or re-configures following on VMs and VM Scale Sets:
    - Log Analytics VM Extension configured to supplied Log Analytics Workspace
    - Dependency Agent VM Extension

    Can be applied to:
    - Subscription
    - Resource Group in a Subscription
    - Specific VM/VM Scale Set
    - Compliance results of a policy for a VM or VM Extension

    Script will show you list of VMs/VM Scale Sets that will apply to and let you confirm to continue.
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
        If using PolicyAssignmentName parameter, subscription that VMs are in

    -ResourceGroup <String>
        <Optional> Resource Group to which the VMs or VM Scale Sets belong

    -Name <String>
        <Optional> To install to a single VM/VM Scale Set

    -PolicyAssignmentName <String>
        <Optional> Take the input VMs to operate on as the Compliance results from this Assignment
        If specified will only take from this source.

    -ReInstall [<SwitchParameter>]
        <Optional> If VM/VM Scale Set is already configured for a different workspace, set this to change to the new workspace

    -TriggerVmssManualVMUpdate [<SwitchParameter>]
        <Optional> Set this flag to trigger update of VM instances in a scale set whose upgrade policy is set to Manual

    -Approve [<SwitchParameter>]
        <Optional> Gives the approval for the installation to start with no confirmation prompt for the listed VMs/VM Scale Sets

    -WorkspaceRegion <String>
        Region the Log Analytics Workspace is in
        Supported values: "East US","eastus","Southeast Asia","southeastasia","West Central US","westcentralus","West Europe","westeurope"
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

    Install for all VMs in a Resource Group in a subscription

    -------------------------- EXAMPLE 2 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -ResourceGroup <ResourceGroup> -ReInstall

    Specify to reinstall extensions even if already installed, for example to update to a different workspace

    -------------------------- EXAMPLE 3 --------------------------
    .\Install-VMInsights.ps1 -WorkspaceRegion eastus -WorkspaceId <WorkspaceId>-WorkspaceKey <WorkspaceKey> -SubscriptionId <SubscriptionId>
    -PolicyAssignmentName a4f79f8ce891455198c08736 -ReInstall

    Specify to use a PolicyAssignmentName for source, and to reinstall (move to a new workspace)
```

次の例では、フォルダー内で PowerShell コマンドを使用して Azure Monitor for VMs を有効化して、想定される出力を解釈する方法を示しています。

```powershell
$WorkspaceId = "<GUID>"
$WorkspaceKey = "<Key>"
$SubscriptionId = "<GUID>"
.\Install-VMInsights.ps1 -WorkspaceId $WorkspaceId -WorkspaceKey $WorkspaceKey -SubscriptionId $SubscriptionId -WorkspaceRegion eastus

Getting list of VMs or VM ScaleSets matching criteria specified

VMs or VM ScaleSets matching criteria:

db-ws-1 VM running
db-ws2012 VM running

This operation will install the Log Analytics and Dependency agent extensions on above 2 VMs or VM Scale Sets.
VMs in a non-running state will be skipped.
Extension will not be reinstalled if already installed. Use -ReInstall if desired, for example to update workspace

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

## <a name="enable-for-a-hybrid-environment"></a>ハイブリッド環境対応にする
このセクションでは、Azure Monitor for VMs による監視のために、データ センターやその他のクラウド環境でホストされている仮想マシンまたは物理コンピューターをデプロイする方法について説明します。 

Azure Monitor for VMs マップの Dependency Agent でデータ自体が送信されることはないため、ファイアウォールやポートを変更する必要はありません。 マップ データは、Log Analytics エージェントによって常に Azure Monitor サービスに直接送信されます。または、ご利用の IT セキュリティ ポリシーでネットワーク上のコンピューターがインターネットに接続することが許可されていない場合には、[OMS ゲートウェイ](../../azure-monitor/platform/gateway.md)を経由して送信されます。

[Log Analytics Linux および Windows エージェント](../../log-analytics/log-analytics-agent-overview.md)の要件とデプロイ方法を確認します。 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

手順は次のとおりです。

1. Windows または Linux 用の Log Analytics エージェントをインストールします。
1. [Windows](https://aka.ms/dependencyagentwindows) または [Linux](https://aka.ms/dependencyagentlinux) 用の Azure Monitor for VMs Map Dependency Agent をダウンロードしてインストールします。
1. パフォーマンス カウンターの収集を有効にします。
1. Azure Monitor for VMs をデプロイします。

### <a name="install-the-dependency-agent-on-windows"></a>Windows に Dependency Agent をインストールする 
`InstallDependencyAgent-Windows.exe` を実行することで、Dependency Agent を Windows コンピューターに手動でインストールできます。 オプションを指定せずにこの実行可能ファイルを実行すると、セットアップ ウィザードが起動します。ここで、指示に従って対話形式でエージェントをインストールできます。 

>[!NOTE]
>エージェントをインストールまたはアンインストールするには、"*管理者*" 特権が必要です。

次の表に、コマンド ラインからのエージェントのセットアップでサポートされているパラメーターを示します。 

| パラメーター | 説明 |
|:--|:--|
| /? | コマンド ライン オプションの一覧を返します。 |
| /S | ユーザーの操作を必要とせずに、サイレント インストールを実行します。 |

たとえば、`/?` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Windows.exe /?**」と入力します。

Windows Dependency Agent のファイルは、既定で *C:\Program Files\Microsoft Dependency Agent* にインストールされます。 セットアップの完了後に Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 ログ ディレクトリは、*%Programfiles%\Microsoft Dependency Agent\logs* にあります。 

### <a name="install-the-dependency-agent-on-linux"></a>Linux に Dependency Agent をインストールする
Dependency Agent は、*InstallDependencyAgent-Linux64.bin* (自己解凍バイナリ ファイルを含むシェル スクリプト) から Linux サーバーにインストールされます。 `sh` を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。

>[!NOTE]
> エージェントをインストールまたは構成するには、ルート アクセスが必要です。
> 

| パラメーター | 説明 |
|:--|:--|
| -help | コマンド ライン オプションの一覧を取得します。 |
| -s | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールしません。 |

たとえば、`-help` パラメーターを使用してインストール プログラムを実行するには、「**InstallDependencyAgent-Linux64.bin -help**」と入力します。

コマンド `sh InstallDependencyAgent-Linux64.bin` を実行し、Linux Dependency Agent を root としてインストールします。
    
Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、*/var/opt/microsoft/dependency-agent/log* にあります。

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | 場所 |
|:--|:--|
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |

### <a name="enable-performance-counters"></a>パフォーマンス カウンターを有効にする
ソリューションによって参照されている Log Analytics ワークスペースが、ソリューションで必要なパフォーマンス カウンターを収集するようにまだ構成されていない場合は、カウンターを有効にする必要があります。 次の 2 つの方法のいずれかで行うことができます。 
* 「[Log Analytics での Windows および Linux のパフォーマンス データ ソース](../../azure-monitor/platform/data-sources-performance-counters.md)」の説明に従って、手動で行います
* [Azure PowerShell ギャラリー](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)で入手できる PowerShell スクリプトをダウンロードして実行します
 
### <a name="deploy-azure-monitor-for-vms"></a>Azure Monitor for VMs をデプロイする
この方法には、Log Analytics ワークスペースでソリューション コンポーネントを有効にするための構成を指定する JSON テンプレートが含まれています。 

テンプレートを使用してリソースをデプロイすることに慣れていない場合は、以下をご覧ください。
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy.md)
* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../../azure-resource-manager/resource-group-template-deploy-cli.md) 

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

1. このファイルを *installsolutionsforvminsights.json* としてローカル フォルダーに保存します。
1. *WorkspaceName*、*ResourceGroupName*、および *WorkspaceLocation* の値を編集します。 *WorkspaceName* の値は、ワークスペース名を含む Log Analytics ワークスペースの完全なリソース ID です。 *WorkspaceLocation* の値は、ワークスペースが定義されているリージョンです。
1. これで、次の PowerShell コマンドを使用して、このテンプレートをデプロイする準備ができました。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    設定の変更が完了するまで数分かかります。 完了すると、次のような結果を含むメッセージが表示されます。

    ```powershell
    provisioningState       : Succeeded
    ```
監視を有効にしてから、ハイブリッド コンピューターの正常性の状態とメトリックが表示されるまで、約 10 分かかる場合があります。 

## <a name="performance-counters-enabled"></a>パフォーマンス カウンターの有効化
Azure Monitor for VMs では、ソリューションによって使用されるパフォーマンス カウンターを収集するため、Log Analytics ワークスペースを構成します。 次の表に、60 秒ごとに収集される、ソリューションによって構成されるオブジェクトとカウンターを示します。

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
|LogicalDisk | Disk Write Bytes/sec |  
|LogicalDisk | Disk Writes/sec |  
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
|論理ディスク | Disk Write Bytes/sec |  
|論理ディスク | Disk Writes/sec |  
|論理ディスク |Free Megabytes |  
|論理ディスク |Logical Disk Bytes/sec |  
|メモリ |Available MBytes Memory |  
|ネットワーク |Total Bytes Received |  
|ネットワーク |Total Bytes Transmitted |  
|プロセッサ |% Processor Time |  

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
Microsoft は、お客様による Azure Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供するサービスの品質、セキュリティ、整合性の向上に努めています。 正確で効果的なトラブルシューティング機能を提供するために、マップ機能からのデータにはソフトウェアの構成に関する情報が含まれています。たとえば、オペレーティング システムとバージョン、IP アドレス、DNS 名、ワークステーション名などです。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]
## <a name="next-steps"></a>次の手順

仮想マシンの監視が有効になったので、Azure Monitor for VMs での分析にこの情報を使用できます。 正常性機能の使用方法については、[Azure Monitor for VMs の正常性の表示](vminsights-health.md)に関する記事をご覧ください。 検出されたアプリケーションの依存関係を表示するには、[Azure Monitor for VMs のマップの表示](vminsights-maps.md)に関する記事をご覧くださいい。 
