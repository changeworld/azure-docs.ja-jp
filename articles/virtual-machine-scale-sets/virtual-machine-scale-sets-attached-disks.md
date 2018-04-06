---
title: Azure 仮想マシン スケール セットの接続されたデータ ディスク | Microsoft Docs
description: 接続されたデータ ディスクを仮想マシン スケール セットで使用する方法について説明します。
services: virtual-machine-scale-sets
documentationcenter: ''
author: gatneil
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 4/25/2017
ms.author: negat
ms.openlocfilehash: ec11a2d66530129fb61d97681e6882b887c8654c
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Azure 仮想マシン スケール セットと接続されたデータ ディスク
[Virtual Machine Scale Sets](/azure/virtual-machine-scale-sets/) では、使用できるストレージを拡張するために、データ ディスクをアタッチした VM インスタンスがサポートされています。 新たに作成するスケール セットや既存のスケール セットにデータ ディスクをアタッチすることができます。

> [!NOTE]
>  データ ディスクがアタッチされたスケール セットを作成する場合、(スタンドアロン型の Azure VM と同様に) ディスクを使用する VM 内からディスクをマウントおよびフォーマットする必要があります。 このプロセスを完了するには、スクリプトを呼び出して VM 上のすべてのデータ ディスクをパーティション化およびフォーマットするカスタム スクリプト拡張機能を使用する方法が便利です。 詳細については、[Azure CLI 2.0](tutorial-use-disks-cli.md#prepare-the-data-disks) または [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks) の例を参照してください。


## <a name="create-and-manage-disks-in-a-scale-set"></a>スケール セットのディスクを作成、管理する
データ ディスクがアタッチされたスケール セットの作成、データ ディスクの準備とフォーマット、データ ディスクの追加と削除の方法について詳しくは、次のいずれかのチュートリアルを参照してください。

- [Azure CLI 2.0](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

以降この記事では、データ ディスクを必要とする Service Fabric クラスターや、既に内容を含んだデータ ディスクをスケール セットにアタッチする方法など、特定のユース ケースについて概説します。


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


## <a name="adding-pre-populated-data-disks-to-an-existent-scale-set"></a>データ投入済みディスクを現行のスケール セットに追加する 
> 現行のスケール セット モデルにディスクを追加すると、そのディスクは常時、意図的に空の状態で作成されます。 このシナリオには、スケール セットによって作成される新しいインスタンスも含まれます。 この振る舞いの理由は、スケールセット定義に空のデータ ディスクが存在するためです。 現行のスケール セット モデル用にデータ投入済みドライブを作成するには、次の 2 とおりの方法があります。

* カスタム スクリプトを実行してインスタンス 0 の VM から他の VM のデータ ディスクにデータをコピーする。
* OS ディスクと (必要なデータが格納された) データ ディスクとを含んだマネージ イメージを作成し、そのイメージを使って新しいスケールセットを作成する。 そうすることで、そのスケールセットの定義に指定されたデータ ディスクが、新たに作成されるすべての VM に割り当てられるようになります。 この定義によって参照されるイメージには、カスタマイズされたデータを格納したデータ ディスクが含まれるため、そのスケールセットの各仮想マシンには、それらの変更が反映されています。

> カスタム イメージを作成する方法については、「[Azure で一般化された VM の管理対象イメージを作成する](/azure/virtual-machines/windows/capture-image-resource/)」を参照してください。 

> 必要なデータを含んだインスタンス 0 の VM をユーザーがキャプチャしたうえで、その vhd をイメージの定義に使用する必要があります。


## <a name="additional-notes"></a>その他のメモ
Azure Managed Disks と、スケール セットに接続されたデータ ディスクのサポートは、Microsoft.Compute API のバージョン [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/arm-compute/2016-04-30-preview/swagger/compute.json) 以降で使用できます。

スケール セットに対する接続されたディスクのサポートの初期の実装では、スケール セット内の個々の VM に対し、データ の接続や接続解除を行うことはできません。

スケール セット内の接続されたデータ ディスクに対する Azure Portal でのサポートは、最初は制限されています。 要件に応じて、Azure テンプレート、CLI、PowerShell、SDK、および REST API を使用して、接続されたディスクを管理できます。


