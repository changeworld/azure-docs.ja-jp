---
title: "Azure PowerShell を使用した Azure DDoS Protection Standard の管理 | Microsoft Docs"
description: "Azure PowerShell を使用して Azure DDoS Protection Standard を管理する方法について説明します。"
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 33ff6cfcacd1632dc49b448e70361e1cb2ce1176
ms.sourcegitcommit: 09a2485ce249c3ec8204615ab759e3b58c81d8cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Azure PowerShell を使用した Azure DDoS Protection Standard の管理

分散型サービス拒否 (DDoS) Protection の有効と無効を切り替える方法と、テレメトリを使用して Azure DDoS Protection Standard で DDoS 攻撃を軽減する方法について説明します。 DDoS Protection Standard は、Azure [パブリック IP アドレス](virtual-network-public-ip-address.md)が割り当てられた Azure リソース (仮想マシン、ロード バランサー、アプリケーション ゲートウェイなど) を保護します。 DDoS Protection Standard とその機能の詳細については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。 

>[!IMPORTANT]
>Azure DDoS Protection Standard (DDoS Protection) は、現在プレビュー段階にあります。 DDoS Protection がサポートされる Azure リソースの数と使用可能なリージョンは限られています。 使用可能なリージョンの一覧については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。 サブスクリプション用に DDoS Protection を有効にするには、制限されたプレビュー中に[このサービスに登録する](http://aka.ms/ddosprotection)必要があります。 登録すると、Azure DDoS チームから連絡があり、有効化プロセスが案内されます。


## <a name="log-in-to-azure"></a>Azure にログインする

`Login-AzureRmAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 Azure PowerShell をインストールまたはアップグレードする必要がある場合は、「[Install Azure PowerShell module (Azure PowerShell モジュールのインストール)](/powershell/azure/install-azurerm-ps)」を参照してください。

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>DDoS Protection Standard を有効にする - 新しい仮想ネットワーク

DDoS Protection が有効になった仮想ネットワークを作成するには、次の例を実行します。

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

この例では、2 つのサブネットと 2 つの DNS サーバーを含む仮想ネットワークを作成します。 仮想ネットワーク上の DNS サーバーを指定する効果は、この仮想ネットワークにデプロイされる NIC/VM が既定値としてこれらの DNS サーバーを継承する点にあります。 仮想ネットワーク内のすべての保護されたリソースに対して DDoS 保護が有効になります。

> [!WARNING]
> リージョンを選択する場合は、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」に記載された、サポートされているリージョンの一覧から選択してください。

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>既存の仮想ネットワーク上で DDoS Protection を有効にする

既存の仮想ネットワーク上で DDoS Protection を有効にするには、次の例を実行します。

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> 仮想ネットワークは、サポートされているリージョン内に存在している必要があります。 サポートされているリージョンの一覧については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。

## <a name="disable-ddos-protection-on-a-virtual-network"></a>仮想ネットワーク上の DDoS Protection を無効にする

仮想ネットワーク上の DDoS Protection を無効にするには、次の例を実行します。

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>仮想ネットワークの DDoS Protection の状態を確認する 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>DDoS Protection テレメトリを使用する

攻撃のテレメトリは、Azure Monitor 経由でリアルタイムに提供されます。 このテレメトリは、パブリック IP アドレスが軽減策のもとにある期間中のみ使用できます。 攻撃が軽減される前または軽減された後、テレメトリは表示されません。

### <a name="configure-alerts-on-ddos-protection-metrics"></a>DDoS Protection メトリックにアラートを構成する

Azure Monitor のアラート構成を利用すると、攻撃中にアクティブな軽減策が存在する場合に警告するいずれかの使用可能な DDoS Protection メトリックを選択できます。

#### <a name="configure-email-alert-rules-via-azure"></a>Azure を使用してメール アラート ルールを構成する

1. 使用できるサブスクリプションの一覧を取得します。 適切なサブスクリプションが動作していることを確認します。 そうでない場合は、それを Get-AzureRmSubscription からの出力を使用して適切なものに設定します。 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. リソース グループの既存のルールを一覧表示するには、次のコマンドを使用します。 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. ルールを作成するには、まず次の情報が必要です。 

    - アラートを設定するリソースのリソース ID。
    - そのリソースに使用できるメトリック定義。 リソース ID は、Azure ポータルを使用して取得できます。 そのリソースが既に作成されていると仮定して、Azure Portal でそれを選択します。 その後、次のページで、*[設定]* セクションの下にある *[プロパティ]* を選択します。 **[RESOURCE ID] \(リソース ID)** は、次のページにあるフィールドです。 また、[Azure リソース エクスプローラー](https://resources.azure.com/)を使用することもできます。 パブリック IP のリソース ID の例として `/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip` があります。

    次の例では、仮想ネットワーク内のリソースに関連付けられているパブリック IP アドレスに対するアラートを作成します。 アラートを作成するメトリックは、**[Under DDoS attack or not] \(DDoS 攻撃を受けているかどうか)** です。 これはブール値 1 または 0 です。 **1** は、攻撃を受けていることを示します。 **0** は、攻撃を受けていないことを示します。 過去 5 分以内に攻撃が開始された場合、アラートが作成されます。

    アラートが作成されたときに webhook を作成するか、またはメールを送信するには、まずメールまたは webhook、あるいはその両方を作成します。 メールまたは webhook を作成したら、次の例に示すように、`-Actions` タグを使用してすぐにルールを作成します。 PowerShell を使用して、既存のルールに webhook またはメールを関連付けることはできません。

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. ルールを確認して、アラートが正しく作成されていることを確かめます。

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

また、[webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json)や、アラートを作成するための[ロジック アプリ](../logic-apps/logic-apps-overview.md)の詳細についても学習できます。

## <a name="configure-logging-on-ddos-protection-metrics"></a>DDoS Protection メトリックへのログ記録を構成する

PowerShell 経由で Azure の診断ログにアクセスしたり、この診断ログを構成したりするには、[PowerShell クイック スタート サンプル](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json)を参照してください。

## <a name="next-steps"></a>次の手順

- [Azure 診断ログの詳細を確認する](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Log Analytics を使用した、Azure ストレージからのログの分析](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Event Hubs の使用](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)