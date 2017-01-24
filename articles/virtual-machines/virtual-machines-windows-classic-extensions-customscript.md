---
title: "Windows VM のカスタム スクリプト拡張機能 | Microsoft Docs"
description: "カスタム スクリプト拡張機能を使って Azure VM 構成タスクを自動化し、リモート Windows VM でPowerShell スクリプトを実行する"
services: virtual-machines-windows
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2016
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 862b19b334b0a4da6e61983f428e3e6732189870
ms.openlocfilehash: a5e3fc8a1160fc3eef3c98320840992f86b67d14


---
# <a name="custom-script-extension-for-windows-virtual-machines"></a>Windows 仮想マシンでのカスタムのスクリプト拡張機能

この記事では、Azure Service Management API を使用した Azure PowerShell コマンドレットを使って Windows VM でカスタムのスクリプト拡張機能を使用する概要について説明します。

Microsoft や信頼された第三者の発行元によってビルドされた仮想マシン (VM) の拡張機能を使って、VM の機能を拡張します。 VM 拡張機能の概要については、「[Azure VM 拡張機能と機能](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 [Resource Manager モデルを使用してこれらの手順を実行する](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)方法を確認してください。

## <a name="custom-script-extension-overview"></a>カスタム スクリプト拡張機能の概要

Windows のカスタム スクリプト拡張機能を使うと、リモートの VM で PowerShell スクリプトを実行できます。サインインする必要はありません。 このスクリプトは VM をプロビジョニングした後か、VM の稼働中であればいつでも実行できます。VM で新たにポートを開く必要はありません。 カスタム スクリプト拡張機能は、VM のプロビジョニング後、追加のソフトウェアを実行する際、インストールする際、構成する際に最もよく使用されます。

### <a name="prerequisites-for-running-the-custom-script-extension"></a>カスタム スクリプト拡張機能を実行する前提条件

1. <a href="http://azure.microsoft.com/downloads" target="_blank">Azure PowerShell コマンドレット</a> バージョン 0.8.0 以降をインストールします。
2. スクリプトを既存の VM で実行する場合、VM エージェントがその VM 上で有効であることを確認してください。 インストールされていない場合は、こちらの[手順](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)に従ってください。 VM が Azure ポータルから作成されている場合、VM エージェントは既定でインストールされています。
3. VM で実行するスクリプトを Azure Storage にアップロードします。 スクリプトは、1 つのコンテナーまたは複数のストレージ コンテナーから取得できます。
4. スクリプトは、拡張機能によって起動されるエントリ スクリプトが、他のスクリプトを順に起動するように記述されている必要があります。

## <a name="custom-script-extension-scenarios"></a>カスタム スクリプト拡張機能のシナリオ

### <a name="upload-files-to-the-default-container"></a>既定コンテナーへのファイルのアップロード

下記の例は、サブスクリプションの既定アカウントのストレージ コンテナーにスクリプトがある場合に、VM でスクリプトを実行する方法について示しています。 ContainerName は、スクリプトをアップロードする場所を示します。 既定のストレージ アカウントは、 **Get-AzureSubscription –Default** コマンドを使って検証できます。

次の例では新しい VM が作成されますが、同じシナリオを既存の VM でも実行できます。

```powershell
# Create a new VM in Azure.
$vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
$vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password

# Add Custom Script extension to the VM. 
# The container name refers to the storage container that contains the file.
$vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
New-AzureVM -ServiceName $servicename -Location $location -VMs $vm

# Viewing the  script execution output.
$vm = Get-AzureVM -ServiceName $servicename -Name $name
# Use the position of the extension in the output as index.
$vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList
```

### <a name="upload-files-to-a-non-default-storage-container"></a>既定以外のストレージ コンテナーへのファイルのアップロード

このシナリオでは同一サブスクリプションで、または異なるサブスクリプションで、既定以外のストレージ コンテナーを使用してスクリプトやファイルをアップロードする方法について説明します。 この例では既存の VM を使用していますが、VM を作成して同様の操作を行うこともできます。

```powershell
Get-AzureVM -Name $name -ServiceName $servicename | ` 
Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey ` 
-ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | ` 
Update-AzureVM
```

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>異なるストレージ アカウントにわたる複数のコンテナーへのスクリプトのアップロード

スクリプト ファイルが複数のコンテナーに保存されている場合、スクリプトを実行するには、それらのファイルの完全な SAS (Shared Access Signature) URL を指定する必要があります。

```powershell
Get-AzureVM -Name $name -ServiceName $servicename | ` 
Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey ` 
-ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | ` 
Update-AzureVM
```

### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Azure ポータルからのカスタム スクリプト拡張機能の追加

<a href="https://portal.azure.com/ " target="_blank">Azure ポータル</a>の VM を表示し、実行するスクリプト ファイルを指定して拡張機能を追加します。

![スクリプト ファイルを指定][5]

### <a name="uninstall-the-custom-script-extension"></a>カスタム スクリプト拡張機能のアンインストール

次のコマンドを使って、VM からカスタム スクリプト拡張機能をアンインストールできます。

```powershell
get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | ` 
Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM
```

### <a name="use-the-custom-script-extension-with-templates"></a>テンプレートとカスタム スクリプト拡張機能の併用

Azure Resource Manager テンプレートとカスタム スクリプト拡張機能を併用する方法の詳細については、「[Azure Resource Manager テンプレートでの Windows VM のカスタム スクリプト拡張機能の使用](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。



<!--HONumber=Dec16_HO3-->


