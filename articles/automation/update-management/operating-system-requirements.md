---
title: Azure Automation Update Management でサポートされているクライアント
description: この記事では、Azure Automation Update Management でサポートされている Windows オペレーティング システムと Linux オペレーティング システムについて説明します。
services: automation
ms.subservice: update-management
ms.date: 10/12/2021
ms.topic: conceptual
ms.openlocfilehash: 790544818a207d0a7bb0453f03b890b0dbf5510f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992807"
---
# <a name="operating-systems-supported-by-update-management"></a>Update Management でサポートされているオペレーティング システム

この記事では、サポートされている Windows オペレーティング システムおよび Linux オペレーティング システムと、Update Management によって管理されるマシンまたはサーバーのシステム要件について説明します。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

次の表は、更新プログラムの評価および修正プログラムの適用でサポートされているオペレーティング システムの一覧です。 ファイルの部分置換には、システム Hybrid Runbook Worker が必要です。これは、仮想マシンまたはサーバーで Update Management による管理を有効にしたときに自動的にインストールされます。 Hybrid Runbook Worker のシステム要件の詳細については、「[Windows Hybrid Runbook Worker をデプロイする](../automation-windows-hrw-install.md#prerequisites)」と「[Linux Hybrid Runbook Worker を展開する](../automation-linux-hrw-install.md#prerequisites)」を参照してください。

オペレーティング システムはすべて x64 と見なされます。 x86 はどのオペレーティング システムでもサポートされていません。

> [!NOTE]
> Linux マシンの更新プログラムの評価は、Automation アカウントと Log Analytics ワークスペースの[マッピング テーブル](../how-to/region-mappings.md#supported-mappings)に記載されている特定のリージョンでのみサポートされています。

|オペレーティング システム  |Notes  |
|---------|---------|
|Windows Server 2019 (Datacenter/Standard、Server Core を含む)<br><br>Windows Server 2016 (Datacenter/Standard、Server Core を除く)<br><br>Windows Server 2012 R2 (Datacenter、Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM および SP1 Standard)| Update Management では、このオペレーティング システムの評価および修正プログラムの適用がサポートされます。 Windows Server 2008 R2 では、[Hybrid Runbook Worker](../automation-windows-hrw-install.md) がサポートされています。 |
|CentOS 6、7、および 8       | Linux エージェントでは、更新リポジトリへのアクセス権が必要です。 分類に基づく修正プログラムでは、CentOS の RTM リリースには含まれていないセキュリティ データを返すための `yum` が必須です。 分類に基づく CentOS への修正プログラムの適用の詳細については、[Linux の更新プログラムの分類](view-update-assessments.md#linux)に関する記事を参照してください。          |
|Oracle Linux 6.x、7.x、8x | Linux エージェントでは、更新リポジトリへのアクセス権が必要です。        |
|Red Hat Enterprise 6、7、および 8      | Linux エージェントでは、更新リポジトリへのアクセス権が必要です。        |
|SUSE Linux Enterprise Server 12、15、および 15.1      | Linux エージェントでは、更新リポジトリへのアクセス権が必要です。     |
|Ubuntu 14.04 LTS、16.04 LTS、18.04 LTS、および 20.04 LTS       |Linux エージェントでは、更新リポジトリへのアクセス権が必要です。         |

> [!NOTE]
> Update Management では、Azure 仮想マシン スケール セット内のすべてのインスタンスで更新管理を安全に自動化することをサポートしていません。 [OS イメージの自動アップグレード](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)は、スケール セットで OS イメージのアップグレードを管理するために推奨される方法です。

## <a name="unsupported-operating-systems"></a>サポートされていないオペレーティング システム

次の表に、Update Management でサポートされていないオペレーティング システムを示します。

|オペレーティング システム  |Notes  |
|---------|---------|
|Windows クライアント     | クライアント オペレーティング システム (Windows 7 や Windows 10 など) はサポートされません。<br> Azure Windows Virtual Desktop (WVD) の場合、<br> 更新プログラムを管理するには、Windows 10 クライアント コンピューターの修正プログラム管理用の [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) をお勧めします。 |
|Windows Server 2016 Nano Server     | サポートされていません。       |
|Azure Kubernetes Service ノード | サポートされていません。 「[Azure Kubernetes Service (AKS) の Linux ノードにセキュリティとカーネルの更新を適用する](../../aks/node-updates-kured.md)」で説明されている修正プログラム適用プロセスを使用します。|

## <a name="system-requirements"></a>システム要件

オペレーティング システム固有の要件については、次の情報を参照してください。 追加のガイダンスについては、「[ネットワークの計画](plan-deployment.md#ports)」を参照してください。 TLS 1.2 の要件を理解するには、「[Azure Automation 用の TLS 1.2](../automation-managing-data.md#tls-12-for-azure-automation)」を参照してください。

### <a name="windows"></a>Windows

ソフトウェアの要件:

- .NET Framework 4.6 以降が必要です。 ([.NET Framework のダウンロード](/dotnet/framework/install/guide-for-developers)。)
- Windows PowerShell 5.1 が必要です ([Windows Management Framework 5.1 のダウンロード](https://www.microsoft.com/download/details.aspx?id=54616))。
- この Update Management 機能はシステムの Hybrid Runbook Worker ロールに依存し、その[システム要件](../automation-windows-hrw-install.md#prerequisites)を確認する必要があります。

Windows Update エージェントは、Windows Server Update Services (WSUS) サーバーと通信するか Microsoft Update にアクセスできるように構成する必要があります。 ハイブリッド マシンの場合は、最初にマシンを [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)に接続し、次に Azure Policy を使用して "[Windows Azure Arc マシンに Log Analytics エージェントをデプロイする](../../governance/policy/samples/built-in-policies.md#monitoring)" 組み込みポリシー定義を割り当てることにより、Windows 用の Log Analytics エージェントをインストールすることをお勧めします。 または、VM 分析情報を使用してマシンを監視する場合は、代わりに [[VM分析情報の有効化を有効にする]](../../governance/policy/samples/built-in-initiatives.md#monitoring) イニシアティブを使用します。

Microsoft Endpoint Configuration Manager は、Update Management と組み合わせて使用できます。 統合シナリオの詳細については、「[Update Management を使用して、Microsoft Endpoint Configuration Manager クライアントに更新プログラムを展開する](mecmintegration.md)」を参照してください。 Configuration Manager 環境のサイトによって管理されている Windows サーバーでは、[Windows 用の Log Analytics エージェント](../../azure-monitor/agents/agent-windows.md)が必要です。

既定では、Azure Marketplace からデプロイされた Windows VM は、Windows Update Service から自動更新を受信するように設定されています。 Windows VM をワークスペースに追加しても、この動作は変わりません。 Update Management を使用して更新プログラムを能動的に管理しない場合は、既定の動作 (更新プログラムが自動的に適用される) が適用されます。

> [!NOTE]
> グループ ポリシーを変更して、システムではなくユーザーだけがマシンの再起動を実行できるようにすることができます。 Update Management にユーザーによる手動操作なしでマシンを再起動する権限がない場合、管理対象のマシンが停止する可能性があります。 詳しくは、「[自動更新のグループ ポリシー設定を構成する](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)」をご覧ください。

### <a name="linux"></a>Linux

ソフトウェアの要件:

- マシンでは、プライベートまたはパブリックの更新リポジトリへのアクセス権が必要になります。
- Update Management と対話するには、TLS 1.1 または TLS 1.2 が必要です。
- この Update Management 機能はシステムの Hybrid Runbook Worker ロールに依存し、その[システム要件](../automation-linux-hrw-install.md#prerequisites)を確認する必要があります。 Update Management で Automation Runbook を使用してマシンの評価と更新を開始する場合は、サポートされている Linux ディストリビューションに必要な [Python のバージョン](../automation-linux-hrw-install.md#supported-runbook-types)を確認してください。

> [!NOTE]
> Linux マシンの更新プログラムの評価は、特定のリージョンでのみサポートされています。 Automation アカウントと Log Analytics ワークスペースの[マッピング テーブル](../how-to/region-mappings.md#supported-mappings)を参照してください。

ハイブリッド マシンの場合は、最初にマシンを [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)に接続し、次に Azure Policy を使用して "[Linux Azure Arc マシンに Log Analytics エージェントをデプロイする](../../governance/policy/samples/built-in-policies.md#monitoring)" 組み込みポリシー定義を割り当てることにより、Linux 用の Log Analytics エージェントをインストールすることをお勧めします。 また、Azure Monitor for VMs を使用してマシンの監視も行う場合は、代わりに [Azure Monitor for VMs を有効にする](../../governance/policy/samples/built-in-initiatives.md#monitoring)イニシアティブを使用します。

## <a name="next-steps"></a>次のステップ

Update Management を有効にして使用する前に、[Update Management デプロイの計画](plan-deployment.md)を確認してください。
