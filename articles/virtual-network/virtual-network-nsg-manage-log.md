---
title: ネットワーク セキュリティ グループ イベントとルール カウンター Azure 診断ログ | Microsoft Docs
description: Azure ネットワーク セキュリティ グループのイベントとルール カウンター診断ログを有効にする方法を説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: jdial
ms.openlocfilehash: 81809660bdda957eb4502e02799b9f7f5538ae51
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114025"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>ネットワーク セキュリティ グループの診断ログ

ネットワーク セキュリティ グループ (NSG) には、仮想ネットワーク サブネットとネットワーク インターフェイスのどちらか一方または両方へのトラフィックを許可または拒否するルールが含まれています。 NSG の診断ログを有効にすると、次のカテゴリの情報をログに記録できます。

* **イベント:** MAC アドレスに基づいて、VM に適用される NSG ルールに関するエントリがログに記録されます。 これらのルールの状態は 60 秒ごとに収集されます。
* **ルール カウンター:** トラフィックを拒否または許可するために各 NSG ルールが適用された回数に関するエントリが含まれます。

診断ログは、Azure Resource Manager デプロイ モデルでデプロイされた NSG についてのみ使用できます。 クラシック デプロイ モデルでデプロイされた NSG については診断ログを有効にできません。 2 つのモデルについて理解を深めるには、[Azure デプロイ モデルの理解](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。

診断ログは、診断データを収集する "*個々の*" NSG に対して個別に有効にします。 操作ログまたはアクティビティ ログに関心がある場合は、[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。

## <a name="enable-logging"></a>ログの有効化

[Azure portal](#azure-portal)、[PowerShell](#powershell)、または [Azure CLI](#azure-cli) を使って、診断ログを有効にすることができます。

### <a name="azure-portal"></a>Azure Portal

1. [ポータル](https://portal.azure.com)にサインインします。
2. **[すべてのサービス]** を選び、「*ネットワーク セキュリティ グループ*」と入力します。 検索結果に **[ネットワーク セキュリティ グループ]** が表示されたら、それを選びます。
3. ログを有効にする NSG を選択します。
4. 次の図に示すように、**[監視]** で **[診断ログ]** を選び、**[診断をオンにする]** を選びます。
 
    ![診断の有効化](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. **[診断設定]** で、次の情報を入力するか選んだ後、**[保存]** を選びます。

    | Setting                                                                                     | 値                                                          |
    | ---------                                                                                   |---------                                                       |
    | Name                                                                                        | 任意の名前です。  例: *myNsgDiagnostics*      |
    | **ストレージ アカウントへのアーカイブ**、**イベント ハブへのストリーム**、**Log Analytics への送信** | 必要なだけいくつでも保存先を選択できます。 それぞれについて詳しくは、「[ログの保存先](#log-destinations)」をご覧ください。                                                                                                                                           |
    | ログ                                                                                         | いずれか一方または両方のログ カテゴリを選びます。 各カテゴリでログに記録されるデータについて詳しくは、「[ログのカテゴリ](#log-categories)」をご覧ください。                                                                                                                                             |
6. ログを表示して分析します。 詳しくは、「[ログの表示と分析](#view-and-analyze-logs)」をご覧ください。

### <a name="powershell"></a>PowerShell

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/powershell) で、またはコンピューターから PowerShell を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 お使いのコンピューターから PowerShell を実行する場合は、*AzureRM* PowerShell モジュール、バージョン 6.1.1 以降が必要です。 コンピューターで `Get-Module -ListAvailable AzureRM` を実行して、インストールされているバージョンを確認してください。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `Login-AzureRmAccount` を実行して Azure にサインインする必要もあります。

診断ログを有効にするには、既存の NSG の ID が必要です。 既存の NSG がない場合は、[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) を実行して作成できます。

[Get-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/get-azurermnetworksecuritygroup) を使用して、診断ログを有効にするネットワーク セキュリティ グループを取得します。 たとえば、*myResourceGroup* という名前のリソース グループに存在する *myNsg* という名前の NSG を取得するには、次のコマンドを入力します。

```azurepowershell-interactive
$Nsg=Get-AzureRmNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

3 種類の保存先に診断ログを書き込むことができます。 詳しくは、「[ログの保存先](#log-destinations)」をご覧ください。 この記事では、例として *Log Analytics* の保存先にログを送信します。 [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) を使用して、既存の Log Analytics ワークスペースを取得します。 たとえば、*myWorkspaces* という名前のリソース グループに存在する *myWorkspace* という名前の既存のワークスペースを取得するには、次のコマンドを入力します。

```azurepowershell-interactive
$Oms=Get-AzureRmOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

既存のワークスペースがない場合は、[New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) を使用して作成できます。

2 つのカテゴリのログを有効にすることができます。 詳しくは、「[ログのカテゴリ](#log-categories)」をご覧ください。 [Set-AzureRmDiagnosticSetting](/powershell/module/azurerm.insights/set-azurermdiagnosticsetting) を使用して、NSG に対する診断ログを有効にします。 次の例では、NSG の ID と前に取得したワークスペース を使って、NSG のイベントとカウンター カテゴリ データの両方をワークスペースに記録します。

```azurepowershell-interactive
Set-AzureRmDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

両方ではなくどちらか一方のカテゴリのデータだけを記録したい場合は、前のコマンドに `-Categories` オプションを追加し、続けて *NetworkSecurityGroupEvent* または  *NetworkSecurityGroupRuleCounter* を指定します。 Log Analytics ワークスペース以外の[保存先](#log-destinations)にログを記録したい場合は、Azure [ストレージ アカウント](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)または [Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の適切なパラメーターを使います。

ログを表示して分析します。 詳しくは、「[ログの表示と分析](#view-and-analyze-logs)」をご覧ください。

### <a name="azure-cli"></a>Azure CLI

以下のコマンドは、[Azure Cloud Shell](https://shell.azure.com/bash) で、またはコンピューターから Azure CLI を実行することで実行できます。 Azure Cloud Shell は無料の対話型シェルです。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 コンピューターから CLI を実行する場合は、バージョン 2.0.38 以降が必要です。 コンピューターで `az --version` を実行して、インストールされているバージョンを確認してください。 アップグレードする必要がある場合は、「[Azure CLI のインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)」を参照してください。 CLI をローカルで実行している場合、[必要なアクセス許可](virtual-network-network-interface.md#permissions)を持つアカウントで `az login` を実行して Azure にサインインする必要もあります。

診断ログを有効にするには、既存の NSG の ID が必要です。 既存の NSG がない場合は、[az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) を使用して作成できます。

[az network nsg show](/cli/azure/network/nsg#az-network-nsg-show) を使用して、診断ログを有効にするネットワーク セキュリティ グループを取得します。 たとえば、*myResourceGroup* という名前のリソース グループに存在する *myNsg* という名前の NSG を取得するには、次のコマンドを入力します。

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

3 種類の保存先に診断ログを書き込むことができます。 詳しくは、「[ログの保存先](#log-destinations)」をご覧ください。 この記事では、例として *Log Analytics* の保存先にログを送信します。 詳しくは、「[ログのカテゴリ](#log-categories)」をご覧ください。 

[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) を使用して、NSG に対する診断ログを有効にします。 次の例では、*myWorkspaces* というリソース グループ内に存在する *myWorkspace* という既存のワークスペースに対するイベントとカウンター カテゴリ データの両方と、以前に取得した NSG の ID をログに記録します。

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

既存のワークスペースがない場合は、[Azure portal](../log-analytics/log-analytics-quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [PowerShell](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) を使って作成できます。 2 つのカテゴリのログを有効にすることができます。 

あるカテゴリまたは他のカテゴリのデータのみをログに記録する場合は、前のコマンドでデータをログに記録しないカテゴリを削除します。 Log Analytics ワークスペース以外の[保存先](#log-destinations)にログを記録したい場合は、Azure [ストレージ アカウント](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)または [Event Hub](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) の適切なパラメーターを使います。

ログを表示して分析します。 詳しくは、「[ログの表示と分析](#view-and-analyze-logs)」をご覧ください。

## <a name="log-destinations"></a>ログの保存先

診断データは次のようにすることができます。
- 監査や手動での検査に使用するために、[Azure ストレージ アカウントに書き込みます](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。 リソース診断設定を使用して、リテンション期間 (日数) を指定できます。
- サード パーティーのサービスや PowerBI などのカスタム分析ソリューションで取り込むために、[Event ハブにストリーミングします](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)。
- [Azure Log Analytics に書き込みます](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-diagnostics-direct-to-log-analytics)。

## <a name="log-categories"></a>ログのカテゴリ

次のログ カテゴリの JSON 形式データが書き込まれます。

### <a name="event"></a>Event

イベント ログには、MAC アドレスに基づいて、VM に適用される NSG ルールに関する情報が含まれます。 各イベントについて次の例のようなデータがログ記録されます。

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"[PORT RANGE]",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter"></a>ルール カウンター

ルール カウンター ログには、リソースに適用される各ルールに関する情報が含まれます。 ルールが適用されるたびに、次の例のようなデータがログ記録されます。

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY RULE NAME]",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> 通信の送信元 IP アドレスは記録されません。 ただし、NSG の [NSG フロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md)を有効にすることができます。このログでは、すべてのルール カウンター情報だけでなく、通信を開始した送信元 IP アドレスも記録されます。 NSG フロー ログ データは Azure Storage アカウントに書き込まれます。 Azure Network Watcher の [トラフィック分析](../network-watcher/traffic-analytics.md)機能を使って、データを分析することができます。

## <a name="view-and-analyze-logs"></a>ログの表示と分析

診断ログ データの表示方法については、「[Azure 診断ログの概要](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」をご覧ください。 診断データの送信先に応じて、次のようになります。
- **Log Analytics**: [ネットワーク セキュリティ グループ分析](../log-analytics/log-analytics-azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-log-analytics
)ソリューションを使って、詳細な分析情報を取得できます。 このソリューションは、仮想マシン内のネットワーク インターフェイスのトラフィックを MAC アドレスに従って許可または拒否する NSG ルールの視覚化を提供します。
- **Azure ストレージ アカウント**: データは PT1H.json ファイルに書き込まれます。 各ログは次のパスで見つかります。
    - イベント ログ: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
    - ルール カウンター ログ: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>次の手順

- 以前は監査ログまたは操作ログと呼ばれていた[アクティビティ ログ](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)について詳しく学習します。 どちらの Azure デプロイ モデルで作成された NSG に対しても、アクティビティ ログが既定で有効になります。 NSG で完了した操作を確認するには、アクティビティ ログで次のリソース タイプを含むエントリを探します。
    - Microsoft.ClassicNetwork/networkSecurityGroups
    - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
    - Microsoft.Network/networkSecurityGroups
    - Microsoft.Network/networkSecurityGroups/securityRules
- 診断情報に各フローの送信元 IP アドレスを含める方法については、[NSG フロー ログ](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。
