---
title: "Windows Server 向け Azure Hybrid Benefit | Microsoft Docs"
description: "Windows ソフトウェア アシュアランスの特典を最大限利用してオンプレミスのライセンスを Azure で使用する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: kmouss
manager: timlt
editor: 
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: c2b406530aec60299ea2db38ad9e34895fe36dcd
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/23/2017
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure Hybrid Benefit
ソフトウェア アシュアランスを取得したお客様は、Windows Server 向け Azure Hybrid Benefit により、オンプレミスの Windows Server ライセンスを使用し、Azure で Windows 仮想マシンを低コストで実行することができます。 Windows Server 向け Azure Hybrid Benefit を使用して、Azure でサポートされるプラットフォームの Windows Server イメージまたはカスタムの Windows イメージから新しい仮想マシンをデプロイできます。 この記事では、Windows Server 向け Azure ハイブリッド特典での新しい VM のデプロイ方法と、既存の稼働中 VM を更新する方法について説明します。 Windows Server 向け Azure Hybrid Benefit のライセンスとコスト削減について詳しくは、[Windows Server 向け Azure Hybrid Benefit のライセンス ページ](https://azure.microsoft.com/pricing/hybrid-use-benefit/)をご覧ください。

> [!IMPORTANT]
> Azure Marketplace での Enterprise Agreement のお客様向けに発行された従来の '[HUB]' Windows Server イメージは 2017 年 9 月 11 日にインベントリから削除されました。ポータルで Windows Server 向け Azure Hybrid Benefit の "コスト削減" オプションを使用して標準の Windows Server をご利用ください。 詳しくは、こちらの[記事](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)を参照してください。
>

> [!NOTE]
> SQL Server やサードパーティー製マーケットプレース イメージなどの追加ソフトウェアに料金が発生する VM では、Windows Server 向け Azure ハイブリッド特典は現在使用できません。「プロパティ 'LicenseType' の変更は許可されていません」のような 409 エラーが発生した場合は、追加のソフトウェア料金がかかる新しい Windows Server VM に変換しようとしているか、それをデプロイしようとしています。これはそのリージョンではサポートされていない可能性があります。 変換を行うポータル構成オプションを検索しようとして、その VM で見つからない場合も同じです。
>


> [!NOTE]
> クラシック VM では、オンプレミスのカスタム イメージからの新規 VM のデプロイのみがサポートされています。 この記事でサポートされる機能を利用するには、最初にクラシック VM を Resource Manager モデルに移行する必要があります。
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure Hybrid Benefit の使用方法
Azure Hybrid Benefit で Windows 仮想マシンを使用する方法はいくつかあります。

1. [Azure Marketplace で提供されている Windows Server イメージ](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview)の 1 つから VM をデプロイできます
2. [カスタム VM をアップロード](#upload-a-windows-vhd)し、[Resource Manager テンプレート](#deploy-a-vm-via-resource-manager)または [Azure PowerShell](#detailed-powershell-deployment-walkthrough) を使ってデプロイできます
3. 既存の VM については、Azure ハイブリッド特典で実行するか、Windows Server のオンデマンド料金を支払うかの間で切り替えおよび変換することができます
4. Windows Server 向け Azure Hybrid Benefit で新しい仮想マシン スケール セットをデプロイすることもできます

> [!NOTE]
> Windows Server 向け Azure ハイブリッド特典を使用するように既存の仮想マシン スケール セットを変換することはサポートされていません
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Windows Server Marketplace イメージからの VM のデプロイ
Azure Marketplace から利用できるすべての Windows Server イメージは、Windows Server 向け Azure Hybrid Benefit で有効になっています。 たとえば、Windows Server 2016、Windows Server 2012R2、Windows Server 2012、Windows Server 2008SP1 などです。 これらのイメージを使用して、Azure Portal、Resource Manager テンプレート、Azure PowerShell、その他の SDK から VM を直接デプロイできます。

これらのイメージは、Azure Portal から直接デプロイできます。 Resource Manager テンプレートと Azure PowerShell で使う場合は、次のようにイメージの一覧を表示します。

### <a name="powershell"></a>Powershell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
手順に従って [PowerShell で Windows 仮想マシンを作成](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)し、LicenseType = "Windows_Server" を渡すことができます。 このオプションを使用すると、Azure で既存の Windows Server ライセンスを使用することができます。

### <a name="portal"></a>ポータル
手順に従って [Azure Portal で Windows 仮想マシンを作成](#https://docs.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)し、既存の Windows Server ライセンスを使用するオプションを選択できます。

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>既存の VM を Windows Server 向け Azure ハイブリッド特典を使用するように変換する
Windows Server 向け Azure ハイブリッド特典を利用するように変換したい既存の VM がある場合は、VM のライセンスの種類を以下のように更新できます。

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure ハイブリッド特典の使用に変換する
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>従量課金制に戻す
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>ポータル
ポータル VM ブレードから、"構成" オプションを選択して Azure ハイブリッド特典を使用するように VM を更新し、"Azure ハイブリッド特典" オプションに切り替えることができます。

> [!NOTE]
> [構成] に [Azure ハイブリッド特典] を切り替えるオプションが表示されない場合、それは、選択した VM の種類 (たとえば、カスタム イメージから構築された VM や、SQL Sever、Azure Marketplace サード パーティ ソフトウェアなど、追加有料ソフトウェアを含むイメージから構築された VM) では変換がまだサポートされていないためです。
>

## <a name="upload-a-windows-server-vhd"></a>Windows Server VHD をアップロードする
Windows Server VM を Azure にデプロイするには、先に Windows の基本ビルドを含む VHD を作成する必要があります。 この VHD は、Sysprep を使用して適切に準備した後、Azure にアップロードする必要があります。 [VHD 要件と Sysprep プロセスの詳細](upload-generalized-managed.md)や「[Sysprep Support for Server Role (サーバー ロールに対する Sysprep サポート)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)」を参照してください。 Sysprep を実行する前に、VM をバックアップします。 

VHD が準備できたら、次のように、その VHD を Azure Storage アカウントにアップロードします。

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server、SharePoint Server、Dynamics も、ソフトウェア アシュアランス ライセンスを利用できます。 ただし、Windows Server イメージはあくまでご自身で用意する必要があります。アプリケーション コンポーネントをインストールし、適宜ライセンス キーを指定したうえで、ディスク イメージを Azure にアップロードします。 「[SysPrep を使用した SQL Server のインストールに関する注意点](https://msdn.microsoft.com/library/ee210754.aspx)」や「[Build a SharePoint Server 2016 Reference Image (Sysprep) (SharePoint Server 2016 リファレンス イメージを構築する (Sysprep))](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)」など、アプリケーションを含めて Sysprep を実行するための適切なドキュメントを参照してください。
>
>

[VHD を Azure にアップロードするプロセス](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)の詳細を確認できます。

## <a name="deploy-a-vm-via-resource-manager-template"></a>Resource Manager テンプレートを使用して VM をデプロイする
Resource Manager テンプレート内に、追加パラメーター `licenseType` を指定する必要があります。 [Azure Resource Manager テンプレートの作成](../../resource-group-authoring-templates.md)で詳細を確認できます。 VHD を Azure にアップロードした後、コンピューティング プロバイダーの一部としてライセンスの種類を含めるように Resource Manager テンプレートを編集し、テンプレートを通常どおりデプロイします。

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>PowerShell クイックスタートを使用して VM をデプロイする
PowerShell を使用して Windows Server VM をデプロイするときに、追加パラメーター `-LicenseType` を指定できます。 VHD を Azure にアップロードした後、`New-AzureRmVM` を使って VM を作成し、ライセンスの種類を次のように指定します。

Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

異なる手順に関する詳しいガイドを読んで、[Resource Manager と PowerShell を使用して Windows VM を作成](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)することができます。

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM がライセンスの特典を利用していることを確認する
VM を PowerShell、Resource Manager テンプレート、ポータルのいずれかの方法でデプロイした後、次のように `Get-AzureRmVM` を使用してライセンスの種類を確認できます。

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

出力は次の Windows Server の例のようになります。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

この出力を、Windows Server 向け Azure Hybrid Benefit のライセンスなしでデプロイされた次の VM と比べてください。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>サブスクリプションのすべての Windows Server VM 向け Azure Hybrid Benefit の一覧表示

Windows Server 向け Azure Hybrid Benefit でデプロイされたすべての仮想マシンを参照し、カウントするには、自分のサブスクリプションから次のコマンドを実行します。

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server 向け Azure Hybrid Benefit で仮想マシン スケール セットをデプロイする
仮想マシン スケール セットの Resource Manager テンプレート内に、追加パラメーター `licenseType` を指定する必要があります。 [Azure Resource Manager テンプレートの作成](../../resource-group-authoring-templates.md)で詳細を確認できます。 Resource Manager テンプレートを編集して、licenseType プロパティをスケール セットの virtualMachineProfile の一部として含め、テンプレートを通常どおりデプロイします。2016 Windows Server イメージを使用した次の例をご覧ください。


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
[仮想マシン スケール セットを作成して展開](#https://docs.microsoft.com/en-us/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create)し、LicenseType プロパティを設定することもできます

## <a name="next-steps"></a>次のステップ
[Azure ハイブリッド特典でコストを削減する方法](https://azure.microsoft.com/pricing/hybrid-use-benefit/)について詳しく読みます

[Windows Server 向け Azure ハイブリッド特典のライセンスの詳しいガイダンス](https://docs.microsoft.com/en-us/windows-server/get-started/azure-hybrid-benefit)を確認します。

[Resource Manager テンプレートの使用方法](../../azure-resource-manager/resource-group-overview.md)の詳細を確認します

[Windows Server 向け Azure ハイブリッド特典と Azure Site Recovery によって、Azure へのアプリケーションの移行のコスト効率を高める方法](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)の詳細を確認します

[マルチテナント ホスティング権限を使用した Azure 上の Windows 10](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment) について確認します

[よく寄せられる質問](#https://azure.microsoft.com/en-us/pricing/hybrid-use-benefit/faq/)を参照します
