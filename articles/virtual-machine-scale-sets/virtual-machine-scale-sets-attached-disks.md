---
title: "Azure 仮想マシン スケール セットの接続されたデータ ディスク | Microsoft Docs"
description: "接続されたデータ ディスクを仮想マシン スケール セットで使用する方法について説明します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: 52ea7e35b941d5b1e45f39203757e4a3644cc9a5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 仮想マシン スケール セットと接続されたデータ ディスク
Azure [仮想マシン スケール セット](/azure/virtual-machine-scale-sets/)で、接続されたデータ ディスクを備えた仮想マシンがサポートされるようになりました。 データ ディスクは、Azure Managed Disks で作成したスケール セットのストレージ プロファイルに定義できます。 以前は、スケール セット内の VM で使用できる直接接続されたストレージのオプションは、OS ドライブと一時ドライブだけでした。

> [!NOTE]
>  接続されたデータ ディスクが定義されたスケール セットを作成する場合、(スタンドアロン型の Azure VM と同様に) ディスクを使用する VM 内からディスクをマウントおよびフォーマットする必要があります。 このプロセスを完了するには、標準スクリプトを呼び出して VM 上のすべてのデータ ディスクをパーティション化およびフォーマットするカスタム スクリプト拡張機能を使用する方法が便利です。

## <a name="create-a-scale-set-with-attached-data-disks"></a>接続されたデータ ディスクを備えたスケール セットの作成
接続されたデータ ディスクを備えたスケール セットを作成するには、[az vmss create](/cli/azure/vmss#az_vmss_create) コマンドを使う方法が簡単です。 次の例では、Azure リソース グループと、それぞれ 50 GB と 100 GB の 2 つの接続されたデータ ディスクを備えた 10 台の Ubuntu VM の仮想マシン スケール セットを作成します。

```bash
az group create -l southcentralus -n dsktest
az vmss create -g dsktest -n dskvmss --image ubuntults --instance-count 10 --data-disk-sizes-gb 50 100
```

[az vmss create](/cli/azure/vmss#az_vmss_create) コマンドでは、構成値を指定しない場合に既定で特定の構成値が使用されます。 上書きできる使用可能なオプションを表示するには、次のように入力します。

```bash
az vmss create --help
```

接続されたデータ ディスクを備えたスケール セットを作成するもう 1 つの方法として、Azure Resource Manager テンプレートにスケール セットを定義し、_storageProfile_ に _dataDisks_ セクションを含め、テンプレートをデプロイする方法があります。 前述の例の 50 GB と 100 GB のディスクは、次のテンプレート例のように定義されます。

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    }
]
```

[https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data](https://github.com/chagarw/MDPP/tree/master/101-vmss-os-data) で、接続されたディスクが定義された、すぐにデプロイできる完全版のスケール セット テンプレートの例を確認できます。

## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>データ ディスクをアタッチして Service Fabric クラスターを作成する
Azure で実行されている [Service Fabric](/azure/service-fabric) クラスター内の各[ノード タイプ](../service-fabric/service-fabric-cluster-nodetypes.md)は、仮想マシン スケール セットによって提供されます。  Azure Resource Manager テンプレートを使用して、Service Fabric クラスターを構成するスケール セットにデータ ディスクをアタッチできます。 [既存のテンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)を出発点として使用できます。 テンプレートで _Microsoft.Compute/virtualMachineScaleSets_ リソースの _storageProfile_ 内に _dataDisks_ セクションを追加して、そのテンプレートをデプロイします。 次の例では、128 GB のデータ ディスクがアタッチされます。

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

クラスターがデプロイされるときに、データ ディスクのパーティション、フォーマット、マウントを自動的に行えます。  カスタム スクリプト拡張機能をスケール セットの _virtualMachineProfile_ の _extensionProfile_ に追加します。

Windows クラスターでデータ ディスクを自動的に準備するには、以下を追加します。

```json
{
    "name": "customScript",    
    "properties": {    
        "publisher": "Microsoft.Compute",    
        "type": "CustomScriptExtension",    
        "typeHandlerVersion": "1.8",    
        "autoUpgradeMinorVersion": true,    
        "settings": {    
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Linux クラスターでデータ ディスクを自動的に準備するには、以下を追加します。
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```

## <a name="adding-a-data-disk-to-an-existing-scale-set"></a>既存のスケール セットへのデータ ディスクの追加
> [!NOTE]
>  データ ディスクは、[Azure Managed Disks](./virtual-machine-scale-sets-managed-disks.md) で作成されたスケール セットにのみ接続できます。

Azure CLI の _az vmss disk attach_ コマンドを使うと、データ ディスクを仮想マシン スケール セットに追加することができます。 まだ使用されていない LUN を指定していることを確認します。 次の CLI の例では、50 GB のドライブを LUN 3 に追加しています。

```bash
az vmss disk attach -g dsktest -n dskvmss --size-gb 50 --lun 3
```

次の PowerShell の例では、50 GB のドライブを LUN 3 に追加しています。

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myvmssrg -VMScaleSetName myvmss
$vmss = Add-AzureRmVmssDataDisk -VirtualMachineScaleSet $vmss -Lun 3 -Caching 'ReadWrite' -CreateOption Empty -DiskSizeGB 50 -StorageAccountType StandardLRS
Update-AzureRmVmss -ResourceGroupName myvmssrg -Name myvmss -VirtualMachineScaleSet $vmss
```

> [!NOTE]
> VM サイズが異なると、サポートされる接続されたドライブの数に関する制限も異なります。 新しいディスクを追加する前に、[仮想マシンのサイズ特性](../virtual-machines/windows/sizes.md)を確認してください。

また、スケール セット定義の _storageProfile_ の _dataDisks_ プロパティに新しいエントリを追加し、その変更を適用してディスクを追加する方法もあります。 これをテストするには、[Azure リソース エクスプローラー](https://resources.azure.com/)で既存のスケール セット定義を見つけます。 次の例のように、_[編集]_ を選択し、データ ディスクの一覧に新しいディスクを追加します。

```json
"dataDisks": [
    {
    "lun": 1,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 50
    },
    {
    "lun": 2,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 100
    },
    {
    "lun": 3,
    "createOption": "Empty",
    "caching": "ReadOnly",
    "diskSizeGB": 20
    }          
]
```

次に、_[PUT]_ を選択して、スケール セットに変更を適用します。 この例は、3 台以上の接続されたデータ ディスクがサポートされる VM サイズを使用している場合に有効です。

> [!NOTE]
> データ ディスクを追加または削除するなど、スケール セット定義に変更を加えた場合、変更された定義は新しく作成されるすべての VM に適用されます。既存の VM に対しては、_upgradePolicy_ プロパティが "Automatic" に設定されている場合にのみ、変更された定義が適用されます。 このプロパティが "Manual" に設定されている場合は、新しいモデルを既存の VM に手動で適用する必要があります。 これをポータルで実行するには、_Update-AzureRmVmssInstance_ PowerShell コマンドを使用するか、_az vmss update-instances_ CLI コマンドを使用します。

## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>データ投入済みディスクを現行のスケール セットに追加する 
> 現行のスケール セット モデルにディスクを追加すると、そのディスクは常時、意図的に空の状態で作成されます。 このシナリオには、スケール セットによって作成される新しいインスタンスも含まれます。 この振る舞いの理由は、スケールセット定義に空のデータ ディスクが存在するためです。 現行のスケール セット モデル用にデータ投入済みドライブを作成するには、次の 2 とおりの方法があります。

* カスタム スクリプトを実行してインスタンス 0 の VM から他の VM のデータ ディスクにデータをコピーする。
* OS ディスクと (必要なデータが格納された) データ ディスクとを含んだマネージ イメージを作成し、そのイメージを使って新しいスケールセットを作成する。 そうすることで、そのスケールセットの定義に指定されたデータ ディスクが、新たに作成されるすべての VM に割り当てられるようになります。 この定義によって参照されるイメージには、カスタマイズされたデータを格納したデータ ディスクが含まれるため、そのスケールセットの各仮想マシンには、それらの変更が反映されています。

> カスタム イメージを作成する方法については、「[Azure で一般化された VM の管理対象イメージを作成する](/azure/virtual-machines/windows/capture-image-resource/)」を参照してください。 

> 必要なデータを含んだインスタンス 0 の VM をユーザーがキャプチャしたうえで、その vhd をイメージの定義に使用する必要があります。

## <a name="removing-a-data-disk-from-a-scale-set"></a>スケール セットからのデータ ディスクの削除
Azure CLI の _az vmss disk detach_ コマンドを使用して、データ ディスクを仮想マシン スケール セットから削除することができます。 たとえば、次のコマンドを実行すると、LUN 2 で定義されているディスクが削除されます。
```bash
az vmss disk detach -g dsktest -n dskvmss --lun 2
```  
同様に、_storageProfile_ 内の _dataDisks_ プロパティからエントリを削除し、その変更を適用することによって、スケール セットからディスクを削除することもできます。 

## <a name="additional-notes"></a>その他のメモ
Azure Managed Disks と、スケール セットに接続されたデータ ディスクのサポートは、Microsoft.Compute API のバージョン [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 以降で使用できます。

スケール セットに対する接続されたディスクのサポートの初期の実装では、スケール セット内の個々の VM に対し、データ の接続や接続解除を行うことはできません。

スケール セット内の接続されたデータ ディスクに対する Azure Portal でのサポートは、最初は制限されています。 要件に応じて、Azure テンプレート、CLI、PowerShell、SDK、および REST API を使用して、接続されたディスクを管理できます。


