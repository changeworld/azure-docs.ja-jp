---
title: 強制トンネリングで KMS ライセンス認証を有効にするために Azure カスタム ルートを使用する | Microsoft Docs
description: Azure で強制トンネリングによって KMS ライセンス認証を有効にするために Azure カスタム ルートを使用する方法を示します。
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: f1e2ab6a954361a7807d78dc2baf5d24af52a679
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53797587"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>強制トンネリングを使用したライセンス認証の問題

この記事では、サイト間 VPN 接続または ExpressRoute シナリオで強制トンネリングを有効にしたときに発生する可能性がある KMS ライセンス認証の問題を解決する方法について説明します。

## <a name="symptom"></a>症状

Azure 仮想ネットワーク サブネットで[強制トンネリング](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md)を有効にし、インターネットへのすべてのトラフィックをオンプレミス ネットワークに戻すようにします。 このシナリオでは、Windows Server 2012 R2 以降のバージョンを実行する Azure の仮想マシン (VM) で Windows を正常にライセンス認証することができます。 ただし、それ以前のバージョンの Windows を実行する VM では、Windows のライセンス認証に失敗します。 

## <a name="cause"></a>原因

Azure Windows VM は、Windows のライセンス認証のために Azure KMS サーバーに接続する必要があります。 このライセンス認証では、ライセンス認証要求が Azure のパブリック IP アドレスから送られる必要があります。 強制トンネリング シナリオでは、ライセンス認証要求が Azure のパブリック IP ではなく、オンプレミス ネットワークから行われるため、ライセンス認証は失敗します。 

## <a name="solution"></a>解決策

この問題を解決するには、Azure カスタム ルートを使用して、Azure KMS サーバー (23.102.135.246) に ライセンス認証のトラフィックをルーティングします。 

IP アドレス 23.102.135.246 は、Azure Global クラウドの KMS サーバーの IP アドレスです。 その DNS 名は、kms.core.windows.net です。 Azure Germany などの他の Azure プラットフォームを使用する場合は、対応する KMS サーバーの IP アドレスを使用する必要があります。 詳細については、後の表を参照してください。

|プラットフォーム| KMS DNS|KMS IP|
|------|-------|-------|
|Azure Global|kms.core.windows.net|23.102.135.246|
|Azure Germany|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure China 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


カスタム ルートを追加するには、次の手順を実行します。

### <a name="for-resource-manager-vms"></a>Resource Manager VM の場合

1. Azure PowerShell を開き、[Azure サブスクリプションにサインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。
2. 次のコマンドを実行します。

    ```powershell
    # First, we will get the virtual network hosts the VMs that has activation problems. In this case, I get virtual network ArmVNet-DM in Resource Group ArmVNet-DM

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, we create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out

    $RouteTable = New-AzureRmRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzureRmRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzureRmRouteTable -RouteTable $RouteTable
    ```
3. ライセンス認証の問題がある VM に移動し、[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) を使用して KMS サーバーにアクセスできるかどうかをテストします。

        psping kms.core.windows.net:1688

4. Windows のライセンス認証を試してみて、問題が解決されるかどうかを確認します。

### <a name="for-classic-vms"></a>クラシック VM の場合

1. Azure PowerShell を開き、[Azure サブスクリプションにサインイン](https://docs.microsoft.com/powershell/azure/authenticate-azureps)します。
2. 次のコマンドを実行します。

    ```powershell
    # First, we will create a new route table
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the routetable that was created
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply KMS route table to the subnet that host the problem VMs (in this case, I will apply it to the subnet named Subnet-1)
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. ライセンス認証の問題がある VM に移動し、[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) を使用して KMS サーバーにアクセスできるかどうかをテストします。

        psping kms.core.windows.net:1688

4. Windows のライセンス認証を試してみて、問題が解決されるかどうかを確認します。

## <a name="next-steps"></a>次の手順

- [KMS クライアントのセットアップ キー](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [ライセンス認証の方法の確認と選択](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)