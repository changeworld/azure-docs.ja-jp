---
title: Azure Monitor エージェントのインストール
description: Azure 仮想マシンと Azure Arc 対応サーバーに Azure Monitor エージェント (AMA) をインストールするためのオプション。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 3c1b3ac13f79037b3357f3ecba0d3d668f88d0b5
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850890"
---
# <a name="install-the-azure-monitor-agent"></a>Azure Monitor エージェントのインストール
この記事では、Azure 仮想マシンと Azure Arc 対応サーバーの両方に [Azure Monitor エージェント](azure-monitor-agent-overview.md) をインストールするために現在使用できるさまざまなオプションについて説明し、エージェントが収集するデータを定義する[データ収集ルールとの関連付け](data-collection-rule-azure-monitor-agent.md)を作成するオプションについても説明します。

## <a name="prerequisites"></a>前提条件
Azure Monitor エージェントをインストールする前に、次の前提条件を満たす必要があります。

- [マネージド システム ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) が Azure 仮想マシンで有効になっている必要があります。 これは、Azure Arc 対応サーバーでは必要ありません。 [Azure portal でデータ収集ルールを作成して割り当てる](#install-with-azure-portal)プロセスの一部としてエージェントがインストールされている場合は、システム ID が自動的に有効になります。
- 仮想マシンの仮想ネットワークで [AzureResourceManager サービス タグ](../../virtual-network/service-tags-overview.md)を有効にする必要があります。
- 仮想マシンは、次の HTTPS エンドポイントにアクセスできる必要があります。
  - *.ods.opinsights.azure.com
  - *.ingest.monitor.azure.com
  - *.control.monitor.azure.com

> [!IMPORTANT]
> Azure Monitor エージェントでは、プライベート リンクは現在サポートされていません。

## <a name="virtual-machine-extension-details"></a>仮想マシン拡張機能の詳細
Azure Monitor エージェントは、次の表に示す詳細で [Azure VM 拡張機能](../../virtual-machines/extensions/overview.md)として実装されます。 この記事で説明されているものを含め、仮想マシン拡張機能をインストールする、どの方法でもインストールできます。

| プロパティ | Windows | Linux |
|:---|:---|:---|
| Publisher | Microsoft.Azure.Monitor  | Microsoft.Azure.Monitor |
| 型      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

## <a name="extension-versions"></a>拡張機能のバージョン
プレビューまたは中間バージョンを使用する代わりに、以下に示す GA+ バージョンに更新することを強くお勧めします。

| リリース日 | リリース ノート | Windows | Linux |
|:---|:---|:---|:---|:---|
| 2021 年 6 月 | 一般提供が告知されました。 <ul><li>メトリックの宛先を除くすべての機能が一般提供になりました</li><li>実稼働の品質、セキュリティとコンプライアンス</li><li>すべてのパブリック リージョンで利用可能</li><li>EPS を上げるためのパフォーマンスとスケーリングの改善</li></ul> [詳細情報](https://azure.microsoft.com/updates/azure-monitor-agent-and-data-collection-rules-now-generally-available/) | 1.0.12.0 | 1.9.1.0 |
| 2021 年 7 月 | <ul><li>直接プロキシのサポート</li><li>Log Analytics ゲートウェイのサポート</li></ul> [詳細情報](https://azure.microsoft.com/updates/general-availability-azure-monitor-agent-and-data-collection-rules-now-support-direct-proxies-and-log-analytics-gateway/) | 1.1.1.0 | 1.10.5.0 |
| 2021 年 8 月 | Azure Monitor のメトリックを唯一の宛先として許可する問題を解決 | 1.1.2.0 | 1.10.9.0<sup>1</sup> |
| 2021 年 9 月 | <ul><li>エージェントの再起動時にデータが失われる問題を解決</li><li>Arc Windows サーバー用の 1.1.3.1<sup>2</sup> で新しく発生したバグに対処しました</li></ul> | 1.1.3.2 | 1.12.2.0 <sup>2</sup> |  

<sup>1</sup> AMA Linux バージョン 1.10.7.0 は使用しないでください <sup>2</sup> Arc 対応サーバーで動作しない既知のバグ


## <a name="install-with-azure-portal"></a>Azure portal を使用してインストールする
Azure portal を使用して Azure Monitor エージェントをインストールするには、Azure portal で[データ収集ルールを作成する](data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal)プロセスに従います。 これにより、1 つ以上の Azure 仮想マシンまたは Azure Arc 対応サーバーにデータ収集ルールを関連付けることができます。 エージェントが、まだインストールされていないあらゆる仮想マシンにインストールされます。


## <a name="install-with-resource-manager-template"></a>Resource Manager テンプレートを使用してインストールする
Resource Manager テンプレートを使用して、Azure 仮想マシンおよび Azure Arc 対応サーバーに Azure Monitor エージェントをインストールし、データ収集ルールとの関連付けを作成することができます。 関連付けを作成する前に、データ収集ルールを作成する必要があります。

エージェントをインストールし、関連付けを作成するためのサンプル テンプレートを、以下から取得します。 

- [Azure Monitor エージェント (Azure および Azure Arc) をインストールするためのテンプレート](../agents/resource-manager-agent.md#azure-monitor-agent-preview) 
- [データ収集ルールとの関連付けを作成するためのテンプレート](./resource-manager-data-collection-rules.md)

次のコマンドのような、[Resource Manager テンプレートのデプロイ方法のいずれか](../../azure-resource-manager/templates/deploy-powershell.md)を使用して、テンプレートをインストールします。

# <a name="powershell"></a>[PowerShell](#tab/ARMAgentPowerShell)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
# <a name="cli"></a>[CLI](#tab/ARMAgentCLI)
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resource-group-name>" -TemplateFile "<template-filename.json>" -TemplateParameterFile "<parameter-filename.json>"
```
---

## <a name="install-with-powershell"></a>PowerShell を使用したインストール
仮想マシン拡張機能を追加するための PowerShell コマンドを使用して、Azure 仮想マシンと Azure Arc 対応サーバーに Azure Monitor エージェントをインストールすることができます。 

### <a name="azure-virtual-machines"></a>Azure の仮想マシン
次の PowerShell コマンドを使用して、Azure の仮想マシンに Azure Monitor エージェントをインストールします。
# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5
```
---

### <a name="azure-arc-enabled-servers"></a>Azure Arc 対応サーバー
次の PowerShell コマンドを使用して Azure Monitor エージェントを Azure Arc 対応サーバーにインストールします。
# <a name="windows"></a>[Windows](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location>
```
---
## <a name="install-with-azure-cli"></a>Azure CLI を使用してインストールする
仮想マシン拡張機能を追加するための Azure CLI コマンドを使用して、Azure 仮想マシンと Azure Arc 対応サーバーに Azure Monitor エージェントをインストールすることができます。 

### <a name="azure-virtual-machines"></a>Azure の仮想マシン
次の CLI コマンドを使用して、Azure 仮想マシンに Azure Monitor エージェントをインストールします。
# <a name="windows"></a>[Windows](#tab/CLIWindows)
```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
# <a name="linux"></a>[Linux](#tab/CLILinux)
```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids <vm-resource-id>
```
---
### <a name="azure-arc-enabled-servers"></a>Azure Arc 対応サーバー
次の CLI コマンドを使用して Azure Monitor エージェントを Azure Arc 対応サーバーにインストールします。

# <a name="windows"></a>[Windows](#tab/CLIWindowsArc)
```azurecli
az connectedmachine extension create --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorWindowsAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
# <a name="linux"></a>[Linux](#tab/CLILinuxArc)
```azurecli
az connectedmachine extension create --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --type AzureMonitorLinuxAgent --machine-name <arc-server-name> --resource-group <resource-group-name> --location <arc-server-location>
```
---


## <a name="install-with-azure-policy"></a>Azure Policy を使用してインストールする
次のポリシーとポリシー イニシアティブにより、仮想マシンを作成するたびに自動的にエージェントをインストールし、それをデータ収集ルールに関連付けることができます。

### <a name="built-in-policy-initiatives"></a>組み込みのポリシー イニシアチブ
[エージェントのインストールに必要なものを確認します](azure-monitor-agent-install.md#prerequisites)。 

Windows および Linux 仮想マシン用のポリシー イニシアティブは、次のことを実行する個別のポリシーによって構成されます。

- Azure Monitor エージェント拡張機能を仮想マシンにインストールする。
- 仮想マシンとデータ収集ルールの関連付けを作成し、デプロイする。

![[Azure ポリシー定義] ページの一部のスクリーンショット。Azure Monitor エージェントを構成するための 2 つの組み込みポリシー イニシアティブが表示されています。](media/azure-monitor-agent-install/built-in-ama-dcr-initiatives.png)  

### <a name="built-in-policies"></a>組み込みのポリシー 
ニーズに応じて、それぞれのポリシー イニシアティブから個別のポリシーを選択して使用することができます。 たとえば、エージェントを自動でインストールするだけであれば、次の例に示すように、イニシアティブの最初のポリシーだけを使用します。  

![[Azure ポリシー定義] ページの一部のスクリーンショット。Azure Monitor エージェントを構成するためのイニシアティブに含まれるポリシーが表示されています。](media/azure-monitor-agent-install/built-in-ama-dcr-policy.png)  

### <a name="remediation"></a>Remediation
イニシアチブとポリシーは、作成時に、各仮想マシンに適用されます。 [修復タスク](../../governance/policy/how-to/remediate-resources.md)を実行すると、イニシアティブのポリシー定義が "*既存のリソースに*" デプロイされるので、作成済みのすべてのリソースに対して Azure Monitor エージェントを構成できます。 

Azure portal を使用して割り当てを作成するときに、修復タスクを同時に作成することができます。 修復の詳細については、「[Azure Policy を使って準拠していないリソースを修復する](../../governance/policy/how-to/remediate-resources.md)」を参照してください。

![Azure Monitor エージェントのイニシアティブの修復を示すスクリーンショット。](media/azure-monitor-agent-install/built-in-ama-dcr-remediation.png)

## <a name="diagnostic-settings"></a>診断設定
[[診断設定]](../essentials/diagnostic-settings.md) を使用すると、Azure リソースからリソース ログとメトリックが収集され、複数の場所にルーティングされます。 一般的な場所は Log Analytics ワークスペースです。そこで[ログ クエリ](../logs/log-query-overview.md)や[ログ アラート](../alerts/alerts-log.md)を使用してデータを分析することができます。 リソースを作成するたびに診断設定を自動的に作成するには、Azure Policy を使用します。


## <a name="next-steps"></a>次のステップ

- [データ収集ルールを作成し](data-collection-rule-azure-monitor-agent.md)、エージェントからデータを収集して Azure Monitor に送信します。
