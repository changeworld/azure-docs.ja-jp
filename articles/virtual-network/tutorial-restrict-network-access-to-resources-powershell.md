---
title: PaaS リソースへのネットワーク アクセスを制限する - Azure PowerShell | Microsoft Docs
description: この記事では、Azure PowerShell を使って仮想ネットワーク サービスのエンドポイントで Azure Storage、Azure SQL Database などの Azure リソースへのアクセスを制限する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: b3977e045751165947243c67291e81b998b5fcb5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38606116"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-powershell"></a>PowerShell を使用して仮想ネットワーク サービスのエンドポイントで PaaS リソースへのネットワーク アクセスを制限する

仮想ネットワーク サービスのエンドポイントを使用すると、一部の Azure サービス リソースへのネットワーク アクセスを、仮想ネットワーク サブネットに制限できます。 また、リソースに対するインターネット アクセスを排除することもできます。 サービス エンドポイントにより、使用している仮想ネットワークからサポートされている Azure サービスへの直接接続が提供されるため、ご自身の仮想ネットワークのプライベート アドレス スペースを使用して、Azure サービスにアクセスできるようになります。 サービス エンドポイントを介して Azure リソースに送信されるトラフィックは、常に Microsoft Azure のバックボーン ネットワーク上に留まります。 この記事では、次のことについて説明します:

* 1 つのサブネットを含む仮想ネットワークを作成する
* サブネットを追加し、サービス エンドポイントを有効にする
* Azure リソースを作成し、サブネットからのみネットワーク アクセスできるようにする
* 各サブネットに仮想マシン (VM) をデプロイする
* サブネットからリソースへのアクセスを確認する
* サブネットおよびインターネットからリソースへのアクセスが拒否されたことを確認する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

PowerShell をインストールしてローカルで使用する場合、この記事では Azure PowerShell モジュール バージョン 5.4.1 以降が必要になります。 インストールされているバージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Connect-AzureRmAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

仮想ネットワークを作成する前に、仮想ネットワークのリソース グループと、この記事で作成された他のすべてのリソースを作成する必要があります。 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを作成します。 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

[New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) を使用して仮想ネットワークを作成します。 次の例では、アドレス プレフィックスが *10.0.0.0/16* の、*myVirtualNetwork* という名前の仮想ネットワークを作成します。

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) を使用してサブネット構成を作成します。 次の例では、*Public* という名前のサブネットの構成を作成します。

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

[Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) を使用してサブネット構成を仮想ネットワークに書き込むことで、仮想ネットワークにサブネットを作成します。

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="enable-a-service-endpoint"></a>サービス エンドポイントを有効にする 

サービス エンドポイントをサポートするサービスについてのみ、サービス エンドポイントを有効にできます。 [Get-AzureRmVirtualNetworkAvailableEndpointService](/powershell/module/azurerm.network/get-azurermvirtualnetworkavailableendpointservice) を使用して、Azure の場所で使用できるサービス エンドポイント対応のサービスを表示します。 次の例では、*eastus* リージョンで使用できる、サービス エンドポイント対応サービスの一覧が返されます。 将来的にサービス エンドポイント対応の Azure サービスが増えるにつれて、返されるサービスの一覧も大きくなる予定です。

```azurepowershell-interactive
Get-AzureRmVirtualNetworkAvailableEndpointService -Location eastus | Select Name
``` 

追加のサブネットを仮想ネットワークに作成します。 この例では、*Microsoft.Storage* のサービス エンドポイントを持つ *Private* という名前のサブネットが作成されます。 

```azurepowershell-interactive
$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork `
  -ServiceEndpoint Microsoft.Storage

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-for-a-subnet"></a>サブネットのネットワーク アクセスを制限する

[New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig) を使用して、ネットワーク セキュリティ グループのセキュリティ規則を作成します。 次の規則では、Azure Storage サービスに割り当てられたパブリック IP アドレスへの発信アクセスを許可します。 

```azurepowershell-interactive
$rule1 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-Storage-All `
  -Access Allow `
  -DestinationAddressPrefix Storage `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 100 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

次の規則では、すべてのパブリック IP アドレスへのアクセスを拒否します。 この規則は、優先度が高い前の規則によってオーバーライドされます。これにより、Azure Storage のパブリック IP アドレスへのアクセスが許可されます。

```azurepowershell-interactive
$rule2 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Deny-Internet-All `
  -Access Deny `
  -DestinationAddressPrefix Internet `
  -DestinationPortRange * `
  -Direction Outbound `
  -Priority 110 `
  -Protocol * `
  -SourceAddressPrefix VirtualNetwork `
  -SourcePortRange *
```

次の規則では、任意の場所からサブネットへのリモート デスクトップ プロトコル (RDP) 受信トラフィックを許可します。 サブネットへのリモート デスクトップ接続が許可されるため、後の手順でリソースへのネットワーク アクセスを確認できます。

```azurepowershell-interactive
$rule3 = New-AzureRmNetworkSecurityRuleConfig `
  -Name Allow-RDP-All `
  -Access Allow `
  -DestinationAddressPrefix VirtualNetwork `
  -DestinationPortRange 3389 `
  -Direction Inbound `
  -Priority 120 `
  -Protocol * `
  -SourceAddressPrefix * `
  -SourcePortRange *
```

[New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup) を使用して、ネットワーク セキュリティ グループを作成します。 次の例では、*myNsgPrivate* という名前のネットワーク セキュリティ グループを作成します。

```azurepowershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myNsgPrivate `
  -SecurityRules $rule1,$rule2,$rule3
```

[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) を使用してこのネットワーク セキュリティ グループを *Private* サブネットに関連付けて、サブネット構成を仮想ネットワークに書き込みます。 次の例では、*myNsgPrivate* ネットワーク セキュリティ グループを *Private* サブネットに関連付けます。

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VirtualNetwork `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -ServiceEndpoint Microsoft.Storage `
  -NetworkSecurityGroup $nsg

$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="restrict-network-access-to-a-resource"></a>リソースへのネットワーク アクセスを制限する

サービス エンドポイントを有効にした Azure サービスを介して作成されたリソースへのネットワーク アクセスを制限するために必要な手順は、サービスによって異なります。 各サービスの具体的な手順については、それぞれのサービスのドキュメントをご覧ください。 この記事の残りの部分では、例として、Azure ストレージ アカウントのネットワーク アクセスを制限する手順を示します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

[New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) を使用して Azure ストレージ アカウントを作成します。 `<replace-with-your-unique-storage-account-name>` を Azure 全体における一意の名前 (3 から 24 文字で、数字と小文字のみを使用) に置き換えます。

```azurepowershell-interactive
$storageAcctName = '<replace-with-your-unique-storage-account-name>'

New-AzureRmStorageAccount `
  -Location EastUS `
  -Name $storageAcctName `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

ストレージ アカウントが作成されたら、[Get AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) を使用して、変数に読み込むストレージ アカウントのキーを取得します。

```azurepowershell-interactive
$storageAcctKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName myResourceGroup `
  -AccountName $storageAcctName).Value[0]
```

キーは、後の手順でファイル共有を作成するときに使用します。 `$storageAcctKey` を入力し、値をメモします。後の手順でファイル共有を VM のドライブにマップするときは、この値も手動で入力する必要があります。

### <a name="create-a-file-share-in-the-storage-account"></a>ストレージ アカウントにファイル共有を作成する

[New-AzureStorageContext](/powershell/module/azure.storage/new-azurestoragecontext) を使用して、ご自身のストレージ アカウントとキーのコンテキストを作成します。 コンテキストによってストレージ アカウント名とアカウント キーがカプセル化されます。

```azurepowershell-interactive
$storageContext = New-AzureStorageContext $storageAcctName $storageAcctKey
```

[New-AzureStorageShare](/powershell/module/azure.storage/new-azurestorageshare) を使用してファイル共有を作成します。

$share = New-AzureStorageShare my-file-share -Context $storageContext

### <a name="deny-all-network-access-to-a-storage-account"></a>ストレージ アカウントへのすべてのネットワーク アクセスを拒否する

既定では、ストレージ アカウントは、任意のネットワーク上のクライアントからのネットワーク接続を受け入れます。 選択したネットワークのみにアクセスを制限するには、[Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset) を使用して、既定のアクションを *Deny* に変更します。 ネットワーク アクセスが拒否されると、ストレージ アカウントには、どのネットワークからもアクセスできなくなります。

```azurepowershell-interactive
Update-AzureRmStorageAccountNetworkRuleSet  `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -DefaultAction Deny
```

### <a name="enable-network-access-from-a-subnet"></a>サブネットからのネットワーク アクセスを有効にする

[Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) を使用して作成済み仮想ネットワークを取得し、[Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig) を使用して、変数に読み込むプライベート サブネット オブジェクトを取得します。

```azurepowershell-interactive
$privateSubnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Name "myVirtualNetwork" `
  | Get-AzureRmVirtualNetworkSubnetConfig `
  -Name "Private"
```

[Add-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig) を使用して、*Private* サブネットからストレージ アカウントへのネットワーク アクセスを許可します。

```azurepowershell-interactive
Add-AzureRmStorageAccountNetworkRule `
  -ResourceGroupName "myresourcegroup" `
  -Name $storageAcctName `
  -VirtualNetworkResourceId $privateSubnet.Id
```

## <a name="create-virtual-machines"></a>仮想マシンを作成する

ストレージ アカウントへのネットワーク アクセスをテストするには、各サブネットに VM をデプロイします。

### <a name="create-the-first-virtual-machine"></a>最初の仮想マシンを作成する

[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) を使用して、"*パブリック*" サブネット内に仮想マシンを作成します。 次のコマンドを実行するとき、資格情報の入力を求められます。 入力した値は、VM のユーザー名とパスワードとして構成されます。 `-AsJob` オプションを使用すると、VM はバックグラウンドで作成されるため、次の手順に進むことができます。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Public" `
    -Name "myVmPublic" `
    -AsJob
```

次のサンプル出力のような出力が返されます。

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command                  
--     ----            -------------   -----         -----------     --------             -------                  
1      Long Running... AzureLongRun... Running       True            localhost            New-AzureRmVM     
```

### <a name="create-the-second-virtual-machine"></a>2 番目の仮想マシンを作成する

*Private* サブネットに仮想マシンを作成します。

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Location "East US" `
    -VirtualNetworkName "myVirtualNetwork" `
    -SubnetName "Private" `
    -Name "myVmPrivate"
```

Azure では VM の作成に数分かかります。 Azure が VM の作成を完了し、出力を PowerShell に返すまでは、次の手順に進まないでください。 

## <a name="confirm-access-to-storage-account"></a>ストレージ アカウントへのアクセスを確認する

[Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) を使用して、VM のパブリック IP アドレスを返します。 次の例では、*myVmPrivate* VM のパブリック IP アドレスを返しています。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドの `<publicIpAddress>` を、前のコマンドで返されたパブリック IPアドレスで置換して、次のコマンドを入力します。 

```powershell
mstsc /v:<publicIpAddress>
```

リモート デスクトップ プロトコル (.rdp) ファイルが作成され、お使いのコンピューターにダウンロードされます。 ダウンロードされた rdp ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、**[その他]**、**[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。 **[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、**[はい]** または **[続行]** を選択して接続処理を続行します。

*myVmPrivate* VM で、PowerShell を使用して、Azure ファイル共有を Z ドライブにマップします。 次のコマンドを実行する前に、`<storage-account-key>` と `<storage-account-name>` を、ご自身で指定した値、または「[ストレージ アカウントの作成](#create-a-storage-account)」で取得した値で置き換えます。

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```
PowerShell によって、次のサンプル出力のような出力が返されます。

```powershell
Name           Used (GB)     Free (GB) Provider      Root
----           ---------     --------- --------      ----
Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
```

Azure ファイル共有は Z ドライブに正常にマップされました。

VM から他のパブリック IP アドレスへの送信接続がないことを確認します。

```powershell
ping bing.com
```

応答はありません。これは、*Private* サブネットに関連付けられているネットワーク セキュリティ グループでは、Azure Storage サービスに割り当てられているアドレス以外のパブリック IP アドレスへの発信アクセスが許可されていないためです。

*myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="confirm-access-is-denied-to-storage-account"></a>ストレージ アカウントへのアクセスが拒否されたことを確認する

*myVmPublic* VM のパブリック IP アドレスを取得します。

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmPublic `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

次のコマンドの `<publicIpAddress>` を、前のコマンドで返されたパブリック IPアドレスで置換して、次のコマンドを入力します。 

```powershell
mstsc /v:<publicIpAddress>
```

*myVmPublic* VM で、Z ドライブへの Azure ファイル共有のマッピングを試みます。次のコマンドを実行する前に、`<storage-account-key>` と `<storage-account-name>` を、ご自身で指定した値、または「[ストレージ アカウントの作成](#create-a-storage-account)」で取得した値で置き換えます。

```powershell
$acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
```

共有へのアクセスが拒否され、`New-PSDrive : Access is denied` エラーが発生します。 *myVmPublic* VM が *Public* サブネットにデプロイされているため、アクセスが拒否されました。 *Public* サブネットでは、Azure Storage に対してサービス エンドポイントが有効になっていません。ストレージ アカウントが許可しているのは *Public* サブネットからではなく、*Private* サブネットからのネットワーク アクセスです。

*myVmPublic* VM へのリモート デスクトップ セッションを閉じます。

自分のコンピューターから、次のコマンドを使用してストレージ アカウントのファイル共有の表示を試みます。

```powershell-interactive
Get-AzureStorageFile `
  -ShareName my-file-share `
  -Context $storageContext
```

アクセスが拒否され、"*Get-AzureStorageFile : The remote server returned an error: (403) Forbidden.\(Get-AzureStorageFile: リモート サーバーがエラー「(403) 許可されていません」を返しました。\)HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation\(HTTP 状態コード: 403 - HTTP エラー メッセージ: この要求には、この操作を実行する権限がありません\)*" というエラーが発生します。使用しているコンピューターが *MyVirtualNetwork* 仮想ネットワークの *Private* サブネットにないためです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要なくなったら、[Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

この記事では、仮想ネットワーク サブネットのサービス エンドポイントを有効にしました。 複数の Azure サービスでデプロイされているリソースに対して、サービス エンドポイントを有効にできることを学習しました。 Azure ストレージ アカウントを作成し、そのストレージ アカウントへのネットワーク アクセスを、仮想ネットワーク サブネット内のリソースだけに制限しました。 サービス エンドポイントの詳細については、[サービス エンドポイントの概要](virtual-network-service-endpoints-overview.md)と[サブネットの管理](virtual-network-manage-subnet.md)に関するページをご覧ください。

アカウントに複数の仮想ネットワークがある場合は、各仮想ネットワーク内のリソースが相互に通信できるように、2 つの仮想ネットワークを接続することもできます。 方法については、[仮想ネットワークの接続](tutorial-connect-virtual-networks-powershell.md)に関するページをご覧ください。
