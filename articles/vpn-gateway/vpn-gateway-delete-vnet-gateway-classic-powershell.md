---
title: "仮想ネットワーク ゲートウェイの削除: PowerShell: Azure クラシック | Microsoft Docs"
description: "クラシック デプロイ モデルで、PowerShell を使用して仮想ネットワーク ゲートウェイを削除します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: ac797f879ef306a7d423969ecfadca3a423b4cd5
ms.contentlocale: ja-jp
ms.lasthandoff: 05/11/2017


---
# PowerShell を使用して仮想ネットワーク ゲートウェイを削除する (クラシック)
<a id="delete-a-virtual-network-gateway-using-powershell-classic" class="xliff"></a>
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [クラシック - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

この記事は、クラシック デプロイ モデルで、PowerShell を使用して VPN ゲートウェイを削除するのに役立ちます。 仮想ネットワーク ゲートウェイを削除したら、ネットワーク構成ファイルを変更して、使用しなくなった要素を削除します。

##ステップ 1: Azure への接続
<a id="step-1-connect-to-azure" class="xliff"></a>

### 1.最新の PowerShell コマンドレットをインストールします。
<a id="1-install-the-latest-powershell-cmdlets" class="xliff"></a>

Azure サービス管理 (SM) PowerShell コマンドレットの最新バージョンをダウンロードしてインストールします。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。

### 2.Azure アカウントに接続します。
<a id="2-connect-to-your-azure-account" class="xliff"></a> 

管理者特権で PowerShell コンソールを開き、アカウントに接続します。 接続については、次の例を参考にしてください。

```powershell
Add-AzureAccount
```

## ステップ 2: ネットワーク構成ファイルのエクスポートと表示
<a id="step-2-export-and-view-the-network-configuration-file" class="xliff"></a>

コンピューターにディレクトリを作成し、ネットワーク構成ファイルをそのディレクトリにエクスポートします。 このファイルを使用して、現在の構成情報を表示するだけでなく、ネットワーク構成を変更することもできます。

次の例では、ネットワーク構成ファイルは C:\AzureNet にエクスポートされます。

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

テキスト エディターでファイルを開き、クラシック VNet の名前を確認します。 Azure Portal で VNet を作成するときには、Azure で使用される完全名はポータルには表示されません。 たとえば、Azure Portal に "ClassicVNet1" という名前で表示されている VNet は、ネットワーク構成ファイルではかなり長い名前である可能性があります。 "Group ClassicRG1 ClassicVNet1" のような名前の場合があります。 仮想ネットワーク名は、**'VirtualNetworkSite name ='** と示されています。 PowerShell コマンドレットを実行するときは、ネットワーク構成ファイルの名前を使用します。

## ステップ 3: 仮想ネットワーク ゲートウェイの削除
<a id="step-3-delete-the-virtual-network-gateway" class="xliff"></a>

仮想ネットワーク ゲートウェイを削除すると、そのゲートウェイ経由での VNet への接続がすべて切断されます。 VNet に接続されている P2S クライアントがある場合、警告なしに切断されます。

次の例では、仮想ネットワーク ゲートウェイを削除します。 必ずネットワーク構成ファイルの仮想ネットワークの完全名を使用します。

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

削除が正常に完了すると、次のように表示されます。

```
Status : Successful
```

## ステップ 4: ネットワーク構成ファイルの変更
<a id="step-4-modify-the-network-configuration-file" class="xliff"></a>

仮想ネットワーク ゲートウェイを削除したときに、コマンドレットではネットワーク構成ファイルは変更されません。 構成ファイルを変更して、使用されなくなった要素を削除する必要があります。 以下のセクションは、ダウンロードしたネットワーク構成ファイルを変更する際に役立ちます。

### ローカル ネットワーク サイトの参照
<a id="local-network-site-references" class="xliff"></a>

サイトの参照情報を削除するには、**ConnectionsToLocalNetwork/LocalNetworkSiteRef** の構成を変更します。 ローカル サイトの参照を削除すると、Azure によってトンネルが削除されます。 作成済みの構成によっては、**LocalNetworkSiteRef** が表示されない場合があります。

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

例:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

###ローカル ネットワーク サイト
<a id="local-network-sites" class="xliff"></a>

使用しなくなったすべてのローカル サイトを削除します。 作成済みの構成により、**LocalNetworkSite** が表示されない可能性があります。

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

次の例では、Site3 だけを削除しています。

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### クライアントの AddressPool
<a id="client-addresspool" class="xliff"></a>

VNet に P2S 接続していた場合、**VPNClientAddressPool** があります。 削除した仮想ネットワーク ゲートウェイに対応するクライアント アドレス プールを削除します。

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

例:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### GatewaySubnet
<a id="gatewaysubnet" class="xliff"></a>

VNet に対応する **GatewaySubnet** を削除します。

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

例:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## ステップ 5: ネットワーク構成ファイルのアップロード
<a id="step-5-upload-the-network-configuration-file" class="xliff"></a>

変更を保存し、ネットワーク構成ファイルを Azure にアップロードします。 必要に応じて、ファイル パスを環境に合わせて変更してください。

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

アップロードが正常に完了すると、次の例のように表示されます。

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
