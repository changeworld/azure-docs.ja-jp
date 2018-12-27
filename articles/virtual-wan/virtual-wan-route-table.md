---
title: NVA に誘導するための Azure Virtual WAN 仮想ハブ ルート テーブルを作成する | Microsoft Docs
description: トラフィックをネットワーク仮想アプライアンスに誘導するための Virtual WAN 仮想ハブ ルート テーブル。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: 821aecf5549548365d95ef83ea1fcdeb017a4a21
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321445"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>トラフィックをネットワーク仮想アプライアンスに誘導するための Virtual WAN ハブ ルート テーブルを作成する

この記事では、トラフィックを仮想ハブからネットワーク仮想アプライアンスに誘導するする方法を示します。 

![Virtual WAN のダイアグラム](./media/virtual-wan-route-table/vwanroute.png)

この記事では、以下を行う方法について説明します。

* WAN を作成する
* ハブを作成する
* ハブの仮想ネットワーク接続を作成する
* ハブ ルートを作成する
* ルート テーブルの作成
* ルート テーブルを適用する

## <a name="before-you-begin"></a>開始する前に

以下の条件を満たしていることを確認します。

1. 任意のサードパーティ製のソフトウェアであネットワーク仮想アプライアンス (NVA) があること。この NVA は、通常は Azure Marketplace (Link) から仮想ネットワークにプロビジョニングされます。
2. NVA ネットワーク インターフェイスに割り当てられたプライベート IP があること。 
3. NVA は仮想ハブにデプロイすることはできません。 独立した VNet 内にデプロイする必要があります。 この記事では、この VNet を "DMZ VNet" と呼びます。
4. "DMZ VNet" には、1 つまたは複数の仮想ネットワークを接続できます。 この記事では、それらの VNet を "間接スポーク VNet" と呼びます。 それらの VNet を、VNet ピアリングを使用して DMZ VNet に接続できます。
5. 2 つの VNet が既に作成されていることを確認します。 それらは、スポーク VNet として使用されます。 この記事では、VNet スポークのアドレス空間は 10.0.2.0/24 と 10.0.3.0/24 です。 VNet の作成方法に関する情報が必要な場合は、「[PowerShell を使用して仮想ネットワークを作成する](../virtual-network/quick-create-powershell.md)」を参照してください。
6. どの VNet 内にも仮想ネットワーク ゲートウェイがないことを確認します。

## <a name="signin"></a>1.[サインイン]

Resource Manager PowerShell コマンドレットの最新版がインストールされていることを確認します。 PowerShell コマンドレットのインストールの詳細については、[Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)に関するページを参照してください。 この点は重要です。以前のバージョンのコマンドレットには、この演習に必要な最新の値が含まれていません。

1. 昇格された特権で PowerShell コンソールを開き、Azure アカウントにサインインします。 このコマンドレットでは、サインイン資格情報の入力を求められます。 サインイン後にアカウント設定がダウンロードされ、Azure PowerShell で使用できるようになります。

  ```powershell
  Connect-AzureRmAccount
  ```
2. Azure サブスクリプションの一覧を取得します。

  ```powershell
  Get-AzureRmSubscription
  ```
3. 使用するサブスクリプションを指定します。

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Name of subscription"
  ```

## <a name="rg"></a>2.リソースを作成する

1. リソース グループを作成します。

  ```powershell
  New-AzureRmResourceGroup -Location "West US" -Name "testRG"
  ```
2. 仮想 WAN を作成します。

  ```powershell
  $virtualWan = New-AzureRmVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
  ```
3. 仮想ハブを作成します。

  ```powershell
  New-AzureRmVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
  ```

## <a name="connections"></a>3.接続を作成する

間接スポーク VNet と DMZ VNet から仮想ハブへのハブ仮想ネットワーク接続を作成します。

  ```powershell
  $remoteVirtualNetwork1= Get-AzureRmVirtualNetwork -Name “indirectspoke1” -ResourceGroupName “testRG”
  $remoteVirtualNetwork2= Get-AzureRmVirtualNetwork -Name “indirectspoke2” -ResourceGroupName “testRG”
  $remoteVirtualNetwork3= Get-AzureRmVirtualNetwork -Name “dmzvnet” -ResourceGroupName “testRG”

  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection1” -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection2” -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzureRmVirtualHubVnetConnection -ResourceGroupName “testRG” -VirtualHubName “westushub” -Name  “testvnetconnection3” -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4.仮想ハブ ルートを作成する

この記事では、間接スポーク VNet のアドレス空間は 10.0.2.0/24 と 10.0.3.0/24 であり、DMZ NVA ネットワーク インターフェイスのプライベート IP アドレスは 10.0.4.5 です。

```powershell
$route1 = New-AzureRmVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5.仮想ハブ ルート テーブルを作成する

仮想ハブ ルート テーブルを作成し、作成済みのルートに適用します。
 
```powershell
$routeTable = New-AzureRmVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6.変更をコミットする

仮想ハブに変更をコミットします。

```powershell
Update-AzureRmVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub” -RouteTable $routeTable
```

## <a name="cleanup"></a>リソースをクリーンアップする

これらのリソースが不要になったら、[Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
